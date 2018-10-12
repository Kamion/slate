---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://maps.pathsquad.com'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the PathSquad API! Here you will find all the endpoints with their requests and responses, which will allow you to harness the power of PathSquad routing in your own apps.

You can view code examples in the dark area to the right.

To register for PathSquad services, please visit [https://maps.pathsquad.com](https://maps.pathsquad.com).

# Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "https://api.pathsquad.com"
  -H "Authorization: Bearer [developer_token]"
```

> Make sure to replace `[developer_token]` with your API key.

PathSquad uses an authorization header with a developer key to allow access to the API. You can find your API key in your [profile page](https://maps.pathsquad.com/#/profile).

PathSquad expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer [developer_token]`

<aside class="notice">
You must replace <code>[developer_token]</code> with your personal API key.
</aside>

# Routes

## Get Route

```shell
curl "https://api.pathsquad.com/route"
  -X POST
  -H "Authorization: Bearer [developer_token]"
```

> Request Body

```json
{
  "stops": [
    {
      "lat": 42.03498259999999,
      "lng": -87.67957690000003
    },
    {
      "lat": 41.1281491,
      "lng": -74.1628983
    }
  ],
  "depart_at": "2011-08-30T09:30:16.768-04:00",
  "hazmat": false
}
```

> The above command returns JSON structured like this:

```json
{
  "route_id": "62353d46-e28c-437d-b5b7-15265f97abf0",
  "route": {
    "id": "",
    "trip": {
      "language": "en-US",
      "status": 0,
      "units": "miles",
      "status_message": "Found route between points",
      "legs": [
        {
          "shape": "gqrdoAxdpffD~l@qGxQyB`C]....",
          "summary": {
            "max_lon": -74.161285,
            "max_lat": 42.034981,
            "time": 43873,
            "length": 802.582,
            "min_lat": 40.862888,
            "min_lon": -87.679581
          },
          "maneuvers": [
            {
              "travel_mode": "drive",
              "begin_shape_index": 0,
              "length": 1.09,
              "time": 139,
              "type": 1,
              "end_shape_index": 27,
              "instruction": "Drive south on Chicago Avenue.",
              "verbal_pre_transition_instruction": "Drive south on Chicago Avenue for 1.1 miles.",
              "travel_type": "tractor_trailer",
              "street_names": [
                "Chicago Avenue"
              ],
              "sign": {
                "exit_branch_elements": null
              }
            }
          ]
        }
      ],
      "summary": {
        "max_lon": -74.161285,
        "max_lat": 42.034981,
        "time": 91044,
        "length": 802.582,
        "min_lat": 40.862888,
        "min_lon": -87.679581
      },
      "locations": [
        {
          "original_index": 0,
          "type": "break",
          "lon": -87.679581,
          "lat": 42.034981,
          "side_of_street": ""
        },
        {
          "original_index": 0,
          "type": "break",
          "lon": -74.162895,
          "lat": 41.128151,
          "side_of_street": ""
        }
      ]
    }
  },
  "eta_summary": {
    "driving_eta": 53244,
    "sleeper_eta": 36000,
    "off_duty_eta": 1800,
    "arrive_at": {
      "timestamp": "2018-10-12T20:48:15Z",
      "timezone": "America/New_York"
    }
  },
  "predicted_stops": {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "geometry": {
          "type": "Point",
          "coordinates": [
            -80.353928,
            41.184982
          ]
        },
        "properties": {
          "activity": "Break",
          "duration": 1800,
          "timestamp": "2018-10-12T03:30:47Z",
          "timezone": "America/New_York"
        }
      },
      {
        "type": "Feature",
        "geometry": {
          "type": "Point",
          "coordinates": [
            -77.498612,
            41.037002
          ]
        },
        "properties": {
          "activity": "Sleeper Berth",
          "duration": 36000,
          "timestamp": "2018-10-12T07:00:32Z",
          "timezone": "America/New_York"
        }
      }
    ]
  }
}
```

The route response can be used as is in the [MapBox mobile SDK](https://www.mapbox.com/mobile/). To decode the the polyline, please keep in mind that you have to use six digits as opposed to five stated in the Google documentation. You can find more info and examples [here](https://github.com/valhalla/valhalla-docs/blob/master/decoding.md). The predicted stops are suggestions based on our propietary algorithms which aim to guide the drivers to the most efficient stops for their particular route.

### HTTP Request

`POST https://api.pathsquad.com/route`

### Request Body

Provide the stops in the order you want them to appear in the route. The `depart_at` property is taken into account when calculating the ETA for the route. You can leave it empty and the service will take the current local time based on departure location as a starting time. The string format is RFC3339.

## Get mileage breakdown per state

```shell
curl "https://api.pathsquad.com/ifta/<ID>"
  -H "Authorization: Bearer [developer_token]"
```

> The above command returns JSON structured like this:

```json
{
  "IL": 42357.37650526342,
  "IN": 252314.33842087357,
  "NJ": 111204.47424270134,
  "NY": 4184.132985528076,
  "OH": 381309.4708496073,
  "PA": 500267.60900097404
}
```

This endpoint returns the mileage breakdown per state. The <code><ID></code> URL parameter is the property <code>route_id</code> from the [route request](#get-route). The route ID is available for 10 minutes after the [route request](#get-route) is executed. The distances in the response are in meters.

### HTTP Request

`GET https://api.pathsquad.com/ifta/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The route ID, generated by [route request](#get-route)

## Get weather along route

```shell
curl "https://api.pathsquad.com/weather/<ID>"
  -H "Authorization: Bearer [developer_token]"
```

> The above command returns JSON structured like this:

```json
{
    "points": {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Point",
                    "coordinates": [
                        -82.116234,
                        41.38932
                    ]
                },
                "properties": {
                    "timestamp": "2018-10-12T20:48:35Z",
                    "weather": {
                        "time": 1539377315,
                        "summary": "Partly Cloudy",
                        "icon": "partly-cloudy-day",
                        "precipIntensity": 0.0014,
                        "precipProbability": 0.03,
                        "precipType": "rain",
                        "temperature": 54.81,
                        "apparentTemperature": 54.81,
                        "dewPoint": 38.46,
                        "windSpeed": 10.73,
                        "windBearing": 270,
                        "cloudCover": 0.56,
                        "humidity": 0.54,
                        "pressure": 1011.69,
                        "visibility": 10,
                        "ozone": 291.28
                    }
                }
            },
            {
                "type": "Feature",
                "geometry": {
                    "type": "Point",
                    "coordinates": [
                        -77.829293,
                        40.990772
                    ]
                },
                "properties": {
                    "timestamp": "2018-10-13T00:26:58Z",
                    "weather": {
                        "time": 1539390418,
                        "summary": "Partly Cloudy",
                        "icon": "partly-cloudy-night",
                        "precipIntensity": 0.0009,
                        "precipProbability": 0.02,
                        "precipType": "rain",
                        "temperature": 47.49,
                        "apparentTemperature": 45.79,
                        "dewPoint": 36.89,
                        "windSpeed": 4.24,
                        "windBearing": 274,
                        "cloudCover": 0.54,
                        "humidity": 0.66,
                        "pressure": 1009.81,
                        "visibility": 10,
                        "ozone": 276.05
                    }
                }
            }
        ]
    }
}
```

This endpoint returns the weather forecast along the route. The <code><ID></code> URL parameter is the property <code>route_id</code> from the [route request](#get-route). The route ID is available for 10 minutes after the [route request](#get-route) is executed. Weather forecast is retrieved for the relative time and location the truck is estimated to be at.


### HTTP Request

`GET https://api.pathsquad.com/weather/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The route ID, generated by [route request](#get-route)
