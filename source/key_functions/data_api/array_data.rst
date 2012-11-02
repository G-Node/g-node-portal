=====================
Partial Data Requests
=====================

You may want to request object data or relationaships only (see list of objects and their attributes, data fields and relationaships in the Tables 2.1 - 2.4), or even just get the overall information about an object (like object size) without getting any data or attributes. We support the following parameters for all GET requests for a single object 

 * [q] - can be one of "full", "info", "data", "beard", "link". The response will contain content varying from just generic object information ("link", "info") to the full response ("full").
 * [cascade] - when set to "True" will recursively retrieve all the children objects (not only their permalinks). Please be careful with such requests, requesting a big block in a "cascade" mode may result in several Gigobytes of data to download, and significant delays.

Specifically for signal-based objects (Analog Signal, Irregularly Sampled Signal) the following GET request parameters are supported:

 * [start_time] - start time of the required range (calculated using the same time unit as the t_start of the signal)
 * [end_time] - end time of the required range (calculated using the same time unit as the t_start of the signal)
 * [duration] - duration of the required range (calculated using the same time unit as the t_start of the signal)
 * [start_index] - start index of the required datarange (an index of the starting datapoint)
 * [end_index] - end index of the required range (an index of the end datapoint)
 * [samples_count] - number of points of the required range (an index of the end datapoint)
 * [downsample] - number of datapoints. This parameter is used to indicate whether downsampling is needed. The downsampling is applied on top of the selected data range using other parameters (if specified).

*Note. Some reasonable combinations of these parameters (like 'start_time' and 'duration' or 'start_index' and 'end_time' will return a correct response. Using redundant number of parameters will lead to their disregard, useless combinations may throw a 400 bad request.*

For example, send the following GET request

 ::
    
    Request: GET /electrophysiology/analogsignal/11/?start_index=30&end_index=100&downsample=10&q=data


to get the Analog Signal range with datapoints as of 30 to 100, downsampled from 71 points to 10:

 ::

    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [{
        "fields": {
            "sampling_rate": {
                "units": "hz",
                "data": 10000
            },
            "signal": {
                "units": "mv",
                "data": [
                    -109.15087908065715,
                    -143.56599349341815,
                    -123.28235629296393,
                    -116.57424590237954,
                    -85.95255136469267,
                    -86.48956135366747,
                    -45.100152757898336,
                    -29.496947080422245,
                    -52.86667933701428,
                    -30.721555308717267
                ]},
            "t_start": {
                "units": "ms",
                "data": 3
            }
        },
        "model": "neo_api.analogsignal",
        "permalink": "/electrophysiology/analogsignal/11"
        }
    }],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }

Notice that the "t_start" data field in the response has a data value of 3, indicating the start of the requested part of the array, but not the full original array.
