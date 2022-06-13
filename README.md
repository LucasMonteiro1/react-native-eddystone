<p align="center">
  <img src="https://cdn.arstechnica.net/wp-content/uploads/2015/07/2015-07-13_16-46-26-640x201.jpg" width="500" alt="eddystone">
</p>

# react-native-eddystone

<img src="https://img.shields.io/npm/v/@ms-v3/react-native-eddystone.svg" /> <img src="https://img.shields.io/github/repo-size/ms-v3/react-native-eddystone.svg" /> <img src="https://img.shields.io/github/issues/ms-v3/react-native-eddystone.svg" /> <img src="https://img.shields.io/github/license/ms-v3/react-native-eddystone.svg" />

A simple Eddystone™ implementation in React Native for both iOS and Android. The library also include an opinionated beacon manager class that enables simple beacon telemetry linking, caching and expiration.

## Disclaimer
This package is being published out of necessity until the changes contained in it are published in the original library.

## Installation

`$ yarn add @ms-v3/react-native-eddystone`

## Usage

This is a very simple example of how to listen to UID broadcastz from Eddystone beacons. For more examples, refer to the `examples` folder.

```javascript
import Eddystone from "@ms-v3/react-native-eddystone";

// bind a callback when detecting a uid frame
Eddystone.addListener("onUIDFrame", function(beacon) {
  console.log(beacon);
});

// start listening for beacons
Eddystone.startScanning();

// stop listening for beacons
Eddystone.stopScanning();
```

### API

| Method         | Arguments                                 | Description                                                  |
| -------------- | ----------------------------------------- | ------------------------------------------------------------ |
| startScanning  | none                                      | Starts the device's bluetooth manager and looks for Eddystone beacons. |
| stopScanning   | none                                      | Stop the device's bluetooth manager from listening for Eddystone beacons. |
| addListener    | `event: string`<br />`callback: Function` | Registers a callback function to an event.                   |
| removeListener | `event: string`<br />`callback: Function` | Unregisters a callback function to an event.                 |
| Manager        | class                                     | A simple beacon telemetry linking, caching and expiration class built on top of the current API. See below. |

### Events

There are many events that can be subscribed to using the library's `addListener` method.

| Name             | Parameters                 | Description                                                  |
| ---------------- | -------------------------- | ------------------------------------------------------------ |
| onUIDFrame       | `beacon: BeaconData`       | The device received information from a beacon broadcasting UID data. |
| onEIDFrame       | `beacon: BeaconData`       | The device received information from a beacon broadcasting EID data. |
| onURLFrame       | `url: URLData`             | The device received a Url broadcasted by a beacon.           |
| onTelemetryFrame | `telemetry: TelemetryData` | The device received telemetry information from a beacon.     |
| onStateChanged   | `state: string`            | The device's bluetooth manager state has changed. (iOS only) |

### Data Structures

#### BeaconData

```js
{
  id: string,
  uid: string,
  rssi: number,
  txPower: number
}
```

#### TelemetryData

```js
{
  uid: string,
  voltage: number,
  temp: number
}
```

#### URLData

```js
{
  uid: string,
  url: string
}
```



## Manager

### API

| Method         | Arguments                                    | Description                                                  |
| -------------- | -------------------------------------------- | ------------------------------------------------------------ |
| constructor    | `expiration: number`                         | Creates a instance of the manager with a specific `expiration` time for beacons. |
| start          | none                                         | Starts the device's bluetooth manager and looks for Eddystone beacons. |
| stop           | none                                         | Stop the device's bluetooth manager from listening for Eddystone beacons. |
| has            | `uid: string`                                | Determines whether or not the beacon exists within the manager or not. |
| addBeacon      | `data: BeaconData`                           | Adds a beacon to the manager. This is done automatically when the start method is called but you're allowed to do it manually at any point. |
| addUrl         | `beacon: Beacon` <br />`data: URLData`       | Updates a beacon to set its URL. This is done automatically when the start method is called but you're allowed to do it manually at any point. |
| addTelemetry   | `beacon: Beacon`<br /> `data: TelemetryData` | Updates a beacon to set its telemetry data. This is done automatically when the start method is called but you're allowed to do it manually at any point. |
| addListener    | `event: string`<br />`callback: Function`    | Registers a callback function to an event.                   |
| removeListener | `event: string`<br />`callback: Function`    | Unregisters a callback function to an event.                 |

### Events

Events that can be subscribed using the manager's `addListener` method.

| Name            | Parameters       | Description                                                  |
| --------------- | ---------------- | ------------------------------------------------------------ |
| onBeaconAdded   | `beacon: Beacon` | The manager received information from a new beacon broadcasting UID or EID data. |
| onBeaconUpdated | `beacon: Beacon` | The manager received information from a beacon broadcasting a URL or Telemetry data. |
| onBeaconExpired | `beacon: Beacon` | The manager has not received information from a beacon within the ammount of millisecond set by the `expiration` value. |



## Beacon

### API

| Method        | Arguments                                  | Description                                                  |
| ------------- | ------------------------------------------ | ------------------------------------------------------------ |
| constructor   | `data: BeaconData`<br />`manager: Manager` | Creates a instance of a beacon from data and manager         |
| setExpiration | `time: number`                             | Sets the beacon expiration. This is done automatically by the manager but you're allowed to do it manually at any point. |
| getDistance   | none                                       | Returns the approximative distance in meters from the device. |
