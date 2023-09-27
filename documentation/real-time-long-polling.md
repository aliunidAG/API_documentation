
# Accessing business real-time data in aliunid OPERATOR via HTTP long polling

This documentation provides code snippets in JavaScript to demonstrate how to use API keys for HTTP long polling authorization for accessing business real-time data in [aliunid OPERATOR](https://operator.aliunid.com/).

## Prerequisites

Before proceeding, ensure you have the following:

1. An API key to authenticate your WebSocket connection. (Settings -> API keys -> Add API key)
2. The business ID. (Navigate to the Dashboard and copy the ID from the address bar. For example in the url `https://operator.aliunid.com/businesses/9bf32959-1fd4-4fa4-83e0-9e9cd80093af` business ID is `9bf32959-1fd4-4fa4-83e0-9e9cd80093af`)
3. OPTIONAL: Measuring point IDs. (Navigate to the desired Measuring point and copy the ID from the address bar. For example in the url `https://operator.aliunid.com/businesses/9bf32959-1fd4-4fa4-83e0-9e9cd80093af/self/measuring-points/e113fba4-9a5d-4ae9-aba9-b92b5b7ddf34` measuring point ID is `e113fba4-9a5d-4ae9-aba9-b92b5b7ddf34`)
4. A WebSocket client library. We'll use the `ws` library for this example.

## Installation

Install the `ws` library using npm:

```bash
npm install ws
```

## WebSocket Connection

Establish a WebSocket connection to the server using your API key:

```javascript
const WebSocket = require('ws')

const API_KEY = 'YOUR_API_KEY'
const BUSINESS_ID = 'YOUR_BUSINESS_ID'
const MEASURING_POINT_ID_1 = 'YOUR_MEASURING_POINT_1'
const MEASURING_POINT_ID_2 = 'YOUR_MEASURING_POINT_2'
const OPTIONAL_MEASURING_POINT_IDS = `&id=${MEASURING_POINT_ID_1}&id=${MEASURING_POINT_ID_2}`
const WEBSOCKET_ENDPOINT = `wss://operator.aliunid.com/web/api/data-export/v1/businesses/${BUSINESS_ID}/measuring-points/live?apiKey=${YOUR_API_KEY}${OPTIONAL_MEASURING_POINT_IDS}`

const ws = new WebSocket(WEBSOCKET_ENDPOINT)
```

This WebSocket connection will return all real-time data from the moment of the connection. To listen to specific measuring point IDs only add them as url query params.

## Listening real-time data 

Once the WebSocket connection is established, handle the incoming data from the server:

```javascript
ws.on('open', () => {
  console.log('WebSocket connection opened.');

  ws.on('message', (data) => {
    const jsonData = JSON.parse(data)

    if (jsonData.type === 'data') {
      // Handle data for a specific measuring point
      const measuringPointId = jsonData.id
      const measuringPointData = jsonData.data
      console.log(`Data for Measuring Point ${measuringPointId}:`, measuringPointData)
    } else {
      console.log('Unknown data format, are you connected to the appropriate endpoint version?:', jsonData)
    }
  });

  ws.on('close', () => {
    console.log('WebSocket connection closed.')
  });
});
```

## Publishing

The service is not supporting message handling. If the WebSocket client sends a message it will be disconnected.


## Closing the WebSocket Connection

To close the WebSocket connection:

```javascript
// Close the WebSocket connection when no longer needed
ws.close();
```

## Data Format


```json
{
  "type": "data",
  "id": "957f7b09-af00-4b59-b708-e06ae28f7bbb", // measuring point id
  "data": { "sys": { "kW": 0, "kWh+": 123 } } // measuring point data
}
```

Please note that the specific implementation details may vary depending on your WebSocket server. Adapt the code accordingly to fit your specific use case.
