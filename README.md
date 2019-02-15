# weatherapp-saga
WeatherApp with redux-saga

* actions   
  * [index.js](#actions-index-js)
  * [types.js ](#actions-types-js)
* api
  * [index.js](#api-index-js)
* components
  * [app.js](#components-app-js)
  * [notify.js](#components-notify-js)
* containers
  * [search_bar.js](#containers-search-bar-js)
  * [weather_list.js](#containers-weather-list-js)
* reducers
  * [index.js](#reducers-index-js)
  * [reducer_weather.js](#reducers-weather-js)
* sagas
  * [index.js](#sagas-index-js)
* [index.js](#root-index-js)
* [styles.css](#root-styles-css)


### <a name="root-index-js"></a>/index.js

```sh
import React from "react";
import ReactDOM from "react-dom";
import { Provider } from "react-redux";
import { createStore, applyMiddleware } from "redux";
import ReduxPromise from "redux-promise";
import createSagaMiddleware from "redux-saga";
import rootSaga from "./sagas/index";

import App from "./components/app";
import rootReducer from "./reducers";

import "./styles.css";

const sagaMiddleware = createSagaMiddleware();
const store = createStore(
  rootReducer,
  applyMiddleware(sagaMiddleware, ReduxPromise)
);
sagaMiddleware.run(rootSaga);

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.querySelector(".container")
);
```

### <a name="root-styles-css"></a>/styles.css

```sh
body {
  font-family: Verdana;
}
table.table-group {
  margin-top: 10px;
  border-spacing: 0px;
}

table.table-group th,
tr,
td {
  border-collapse: separate;
  border: 1px solid #999;
  padding: 5px;
  font: bold 14px verdana;
}
table.table-group td {
  padding: 2px;
  font: normal 12px verdana;
}

.alert-container {
  border: 0px solid blue;
  height: 40px;
  padding-top: 20px;
}

.alert-div {
  border: 1px solid rgb(247, 5, 5);
  padding: 5px 0px 5px 5px;
  height: auto;
  width: 100%;
  font: bold 12px verdana;
  background-color: lightgoldenrodyellow;
  letter-spacing: 1px;
  text-align: center;
}
.float-right {
  float: right;
  cursor: pointer;
  color: yellow;
  background-color: red;
  padding: 5px;
  position: relative;
  top: -5px;
}
.hide {
  opacity: 0;
  transition: opacity 2s linear;
}

```

### <a name="actions-index-js"></a>/actions/index.js

```sh
import { FETCH_WEATHER } from "./types";

export const fetchWeatherAction = data => ({ type: FETCH_WEATHER, data });
```

### <a name="actions-types-js"></a>/actions/types.js

```sh
export const FETCH_WEATHER = "FETCH_WEATHER";
export const FETCH_WEATHER_SUCCESS = "FETCH_WEATHER_SUCCESS";
export const FETCH_WEATHER_FAIL = "FETCH_WEATHER_FAIL";
```

### <a name="api-index-js"></a>/api/index.js

```sh
import axios from "axios";

const API_KEY = "710486ede3c1b3a27559af4c487bb598";
const ROOT_URL = `https://api.openweathermap.org/data/2.5/forecast?appid=${API_KEY}`;

export function fetchWeather(city) {
  // console.log(city);
  const url = `${ROOT_URL}&q=${city},us`;
  const req = axios.get(url);
  // console.log(JSON.stringify(req));
  return req;
}
```
