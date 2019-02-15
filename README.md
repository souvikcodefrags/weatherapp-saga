# weatherapp-saga
WeatherApp with redux-saga

##### [Demo](https://v6qw8k9l.codesandbox.io/)

### <a name="folders-list"></a>Folders List
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
  * [reducer_weather.js](#reducers-reducer-weather-js)
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
##### [Back to Folders List](#folders-list)

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
##### [Back to Folders List](#folders-list)

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
##### [Back to Folders List](#folders-list)

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
##### [Back to Folders List](#folders-list)

### <a name="components-app-js"></a>/components/app.js

```sh
import React, { Component } from "react";
import WeatherList from "../containers/weather_list";
import SearchBar from "../containers/search_bar";
import Notify from "./notify";

export default class App extends Component {
  render() {
    return (
      <div>
        <SearchBar />
        <div className="alert-container">
          <Notify />
        </div>
        <WeatherList />
      </div>
    );
  }
}
```
##### [Back to Folders List](#folders-list)

### <a name="components-notify-js"></a>/components/notify.js

```sh
import React, { Component } from "react";
import { connect } from "react-redux";

class Notify extends Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }

  closeAlert(e) {
    const node = this.myRef.current;
    node.className += " hide";
  }

  componentDidUpdate() {
    console.log("componentDidUpdate-", this.props.fetchStatus);
    if (!this.props.fetchStatus) {
      setTimeout(() => {
        this.closeAlert();
      }, 5000);
    }
  }

  render() {
    return (
      !this.props.fetchStatus && (
        <div className="alert-div" ref={this.myRef}>
          {this.props.alertMessage}
          <span className="float-right" onClick={e => this.closeAlert(e)}>
            X
          </span>
        </div>
      )
    );
  }
}

const mapStateToProps = ({ weather }) => ({
  fetchStatus: weather.fetchStatus,
  alertMessage: weather.alertMessage
});

export default connect(mapStateToProps)(Notify);
```
##### [Back to Folders List](#folders-list)

### <a name="containers-search-bar-js"></a>/containers/search_bar.js

```sh
import React, { Component } from "react";
import { connect } from "react-redux";
import { fetchWeatherAction } from "../actions/index";

class SearchBar extends Component {
  constructor(props) {
    super(props);
    this.state = { term: "" };
  }

  handleInputChange = event => {
    this.setState({ term: event.target.value });
  };

  handleSubmit = e => {
    e.preventDefault();
    this.props.fetchWeatherAct(this.state.term);
    this.setState({ term: "" });
  };

  render() {
    return (
      <div>
        <form action="" className="input-group" onSubmit={this.handleSubmit}>
          <input
            type="text"
            placeholder="Type US City Name"
            className="form-control"
            value={this.state.term}
            onChange={this.handleInputChange}
          />
          <span className="input-group-btn">
            <button type="submit" className="btn btn-secondary">
              Submit
            </button>
          </span>
        </form>
      </div>
    );
  }
}

const mapDispatchToProps = dispatch => ({
  fetchWeatherAct: data => {
    dispatch(fetchWeatherAction(data));
  }
});

export default connect(
  null,
  mapDispatchToProps
)(SearchBar);

```
##### [Back to Folders List](#folders-list)

### <a name="containers-weather-list-js"></a>/containers/weather_list.js

```sh
import React, { Component } from "react";
import { connect } from "react-redux";

class WeatherList extends Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  renderWeather(cityData) {
    return cityData ? (
      <tr key={cityData.city.name}>
        <td>{cityData.city.name}</td>
        <td>{cityData.list[0].main.temp}</td>
        <td>{cityData.list[0].main.pressure}</td>
        <td>{cityData.list[0].main.humidity}</td>
      </tr>
    ) : null;
  } 

  render() {
    return (
      <>        
        <table className="table-group">
          <thead>
            <tr>
              <th>City</th>
              <th>Temparature</th>
              <th>Pressure</th>
              <th>Humidity</th>
            </tr>
          </thead>
          <tbody>{this.props.weather.map(this.renderWeather)}</tbody>
        </table>
      </>
    );
  }
}

const mapStateToProps = ({ weather }) => {  
  return {
    weather: weather.weatherData,
    fetchStatus: weather.fetchStatus
  };
};
export default connect(mapStateToProps)(WeatherList);

```
##### [Back to Folders List](#folders-list)

### <a name="reducers-index-js"></a>/reducers/index.js

```sh
import { combineReducers } from "redux";
import WeatherReducer from "./reducer_weather";

const rootReducer = combineReducers({
  weather: WeatherReducer
});

export default rootReducer;
```
### <a name="reducers-reducer-weather-js"></a>/reducers/reducer_weather-js

```sh
import {
  FETCH_WEATHER,
  FETCH_WEATHER_SUCCESS,
  FETCH_WEATHER_FAIL
} from "../actions/types";

export default function(
  state = { weatherData: [], fetchStatus: true },
  action
) {
  switch (action.type) {
    case FETCH_WEATHER:
      return { ...state, fetchStatus: true };
    case FETCH_WEATHER_SUCCESS:
      // var obj = { ...state, weatherData: [...action.data] };
      //console.log("REDUCER--", state.weatherData);
      return { ...state, weatherData: [...state.weatherData, action.data] };
    case FETCH_WEATHER_FAIL:
      return { ...state, fetchStatus: false, alertMessage: "City not found" };
  }
  return state;
}
```
##### [Back to Folders List](#folders-list)

### <a name="sagas-index-js"></a>/sagas/index.js

```sh
import { put, call, takeEvery, takeLatest } from "redux-saga/effects";
import { fetchWeather } from "../api";
import {
  FETCH_WEATHER,
  FETCH_WEATHER_SUCCESS,
  FETCH_WEATHER_FAIL
} from "../actions/types";

export function* featchWeatherFromAxios(action) {
  try {
    const response = yield call(fetchWeather, action.data);
    yield put({ type: FETCH_WEATHER_SUCCESS, data: response.data });
  } catch (error) {
    yield put({ type: FETCH_WEATHER_FAIL, error });
  }
}

export default function* rootSaga() {
  yield takeLatest(FETCH_WEATHER, featchWeatherFromAxios);
}
```
