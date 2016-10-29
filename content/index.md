---
title: EEme API v1
---

# EEme Disaggrgation API

{:toc}

## Current Version

Currently we only provide **v1**.

## Authentication

### API Key (sent in a header)

``` command-line
$ curl -H "Authorization: Bearer {your api key}" {{ site.data.variables.product.api_url_pre }}
```

## Error Handling

If an error occurred, in addition to the status code provided in the header portion of the HTTP response, the HTTP response body contains an error object which provides more details on the specific cause of the error and how to resolve it.

    {
        "error": {
            "code": ERROR_CODE,
            "message": "Something bad happened",
            "description": "Here are more details about what happened"
        }
    }

## Create a disaggregation task

    POST /home

### Input

Name | Type | Description
-----|------|--------------
`usage`|`file` | **Required**. A csv file that contains usage data and time/location information.
`config_id`|`int` | The configuration of disaggregation See [configuration](#analytics-configuration)

### Curl Example
``` command-line
$ curl -0 -X POST \
        -H "Authorization: Bearer {api key}" \
        -F "usage=@usage.csv" \
        -F "config_id=2" \
        {{ site.data.variables.product.api_url_pre }}/v1/home
```

### Response
``` json
{
    "home_id": "f9be1fd7-e288-4b4d-8570-1497d8fce2e3", 
    "success": "yes"
}
```


## List disaggregation tasks

    GET /home

### Curl Example
``` command-line
$ curl -0 -X GET \
        -H "Authorization: Bearer {api key}" \
        {{ site.data.variables.product.api_url_pre }}/v1/home
```

### Response
``` json
{
    "data": [
        {
            "created_at": "2016-10-22 01:37:35", 
            "filename": "samle.csv", 
            "id": "b098acb2-c119-45ae-b237-6de5a6a28f9a", 
            "process_time": "2.89min", 
            "status": "completed"
        }, 
        {
            "created_at": "2016-10-22 01:37:33", 
            "filename": "samle.csv", 
            "id": "cc44a8a8-7a5b-4722-bf0b-193daa777740", 
            "process_time": "2.62min", 
            "status": "completed"
        }, 
        {
            "created_at": "2016-10-22 01:37:32", 
            "filename": "samle.csv", 
            "id": "d13c4b4f-7c69-4e70-b80c-2584ab8e4aa7", 
            "process_time": "2.27min", 
            "status": "completed"
        }
    ], 
    "paging": {
        "page": 1, 
        "page_size": 3, 
        "total_size": 3
    }
}
```

## Get the disaggregation result

    GET /home

### Curl Example
``` command-line
$ curl -0 -X GET \
        -H "Authorization: Bearer {api key}" \
        {{ site.data.variables.product.api_url_pre }}/v1/home/:id
```

### Response
``` json
{
    "data": [
        {
            "always_on": 1.138, 
            "electric_vehicle": 0.0, 
            "hvac": 3.343, 
            "lighting": 0.961, 
            "pool_pump": 0.0, 
            "refrigerator": 1.135, 
            "timestamp": "2015-01-01 00:00:00", 
            "total_load(kwh)": 11.942, 
            "water_heater": 1.157
        }, 
        {
            "always_on": 1.091, 
            "electric_vehicle": 0.0, 
            "hvac": 4.671, 
            "lighting": 0.768, 
            "pool_pump": 0.0, 
            "refrigerator": 1.201, 
            "timestamp": "2015-01-02 00:00:00", 
            "total_load(kwh)": 10.807, 
            "water_heater": 0.06
        },
        ...,
        {
            "always_on": 4.735, 
            "electric_vehicle": 0.0, 
            "hvac": 2.344, 
            "lighting": 1.768, 
            "pool_pump": 0.0, 
            "refrigerator": 0.378, 
            "timestamp": "2015-12-31 00:00:00", 
            "total_load(kwh)": 11.118, 
            "water_heater": 1.191
        }
    ], 
    "home_id": "053492d3-75d9-4010-af17-866a742b488a"
}

```


## Usage File Format

Usage data is in CSV format. It includes the location, timestamp, and meter reading. The first line is used to indicate the location, followed by the meter readings. The minimun length of data is 6 months.

### CSV format
location, Zipcode/City/Address Country  
timstamp, meter reading (the unit depends on your analytics configuration)

### Example 
location,15232 USA  
2016-01-01 00:00:00,0.3523  
2016-01-01 01:00:00,0.38  
2016-01-01 02:00:00,0.4314  
2016-01-01 03:00:00,0.4532  
2016-01-01 04:00:00,0.5686  
2016-01-01 05:00:00,0.5058  



## Analytics Configuration
ID | Interval | Module | Region | Space | Units
---|----------|--------|--------|-------|------
`2`|`15-min` | load-disaggregation | US | residential | kwh
`6`|`1-hr` | load-disaggregation | US | residential | kwh
`11`|`15-min` | load-disaggregation | China | residential | kwh
