# Accessing business real-time data in aliunid OPERATOR via HTTP Long Polling

This documentation provides code snippets in JavaScript to demonstrate how to use API keys for HTTP Long Polling authorization to access business real-time data in [aliunid OPERATOR](https://operator.aliunid.com/).

## Prerequisites

Before proceeding, ensure you have the following:

1. An API key for authorization. (Settings -> API keys -> Add API key)
2. The business ID. (Navigate to the Dashboard and copy the ID from the address bar. For example, in the url `https://operator.aliunid.com/businesses/9bf32959-1fd4-4fa4-83e0-9e9cd80093af`, the business ID is `9bf32959-1fd4-4fa4-83e0-9e9cd80093af`).
3. OPTIONAL: Measuring point IDs. (Navigate to the desired Measuring point and copy the ID from the address bar. For example, in the url `https://operator.aliunid.com/businesses/9bf32959-1fd4-4fa4-83e0-9e9cd80093af/self/measuring-points/e113fba4-9a5d-4ae9-aba9-b92b5b7ddf34`, the measuring point ID is `e113fba4-9a5d-4ae9-aba9-b92b5b7ddf34`).
4. An HTTP client library. We'll use the `axios` library for this example.

## Installation

Install the `node-fetch` library using npm:

```bash
npm install node-fetch
```

## Making a Long Polling Request

Establish a connection to the server using your API key:

```javascript
const fetch = require('node-fetch')

const API_KEY = 'YOUR_API_KEY'
const BUSINESS_ID = 'YOUR_BUSINESS_ID'
const MEASURING_POINT_ID_1 = 'YOUR_MEASURING_POINT_1'
const MEASURING_POINT_ID_2 = 'YOUR_MEASURING_POINT_2'
const OPTIONAL_MEASURING_POINT_IDS = `id=${MEASURING_POINT_ID_1}&id=${MEASURING_POINT_ID_2}`
const POLLING_ENDPOINT = `https://operator.aliunid.com/web/api/data-export/v1/businesses/${BUSINESS_ID}/measuring-points/live?${OPTIONAL_MEASURING_POINT_IDS}`

fetch(POLLING_ENDPOINT, {
  method: 'GET',
  headers: {
    'x-api-key': API_KEY
  }
})
.then(response => response.json())
.then(data => {
  console.log('Received data:', data)
})
.catch(error => {
  console.error('Error:', error)
})
```

## Listening to real-time data

With HTTP Long Polling, you would typically set up a loop where, once the server responds to a request, a new request is immediately made. This loop emulates real-time behavior:

```javascript
function pollData() {
    fetch(POLLING_ENDPOINT, {
      'x-api-key': API_KEY
    })
    .then(response => {
      if (!response.ok) {
        throw new Error(`HTTP error! Status: ${response.status}`)
      }
      return response.json()
    })
    .then(data => {
      // Handle the data here...
      console.log(data)

      // Poll again
      pollData()
    })
    .catch(error => {
      console.error('Error occurred:', error)
      setTimeout(pollData, 5000)  // Retry after 5 seconds if there's an error
    })
}

pollData()
```

## Data Format

```json
{
  "type": "data",
  "id": "957f7b09-af00-4b59-b708-e06ae28f7bbb", // measuring point id
  "data": { "sys": { "kW": 0, "kWh+": 123 } } // measuring point data
}
```

Please note that the specific implementation details may vary. Adapt the code accordingly to fit your specific use case.

---

> :warning: **Warning:** Remember, long polling is essentially a hack to simulate real-time data on HTTP. The valid use-case for its usage is only when WebSockets are not available or live data for a short period of time is required. In any other case the [WebSockets](./real-time-websocket.md) should be used.
