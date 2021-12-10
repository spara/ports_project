# Traffic Analytics and NavStreets

This project is a set of utility scripts for:

- requesting data from the HERE Traffic Analytics API: [get traffic analytics data.ipynb](./src/get%20traffic%20analytics%20data.ipynb)
- calculating the congestion and weighted congestion score for roads: [calculate congestion score.ipynb](./src/calculate%20congestion%20score.ipynb)
- add fields to NavStreets to join the congestion score: [HERE_Streets_to_TA.ipynb](./src/HERE_Streets_to_TA.ipynb)
- join the congestion to NavStreets and save as geojson [join and export geojson files.ipynb](./src/join%20and%20export%20geojson%20files.ipynb)

## To run the notebooks

1. Create a Python3 virtual environment 

```sh
$ python3 -m venv venv
$ pip install -r requirements.txt
$ source venv/bin/activate
```

2. Start Jupyter

```sh
$ jupyter notebook
```

## Step 1: Requesting data from the Traffic Analytics API

The `get traffic analytics data` notebook uses a geojson file with one feature per line:

```json
{ "type": "Feature", "properties": { "OBJECTID": 1, "COUNT": 1, "SHAPE_Length": 0.92073356600019451, "SHAPE_Area": 0.050308487050890478, "NAME": "box" }, "geometry": { "type": "Polygon", "coordinates": [ [ [ 0, 0 ], [ 0, 1 ], [ 1, 1 ], [ 1, 0 ] ] ] } }
{ "type": "Feature", "properties": { "OBJECTID": 1, "COUNT": 1, "SHAPE_Length": 0.92073356600019451, "SHAPE_Area": 0.050308487050890478, "NAME": "another_box" }, "geometry": { "type": "Polygon", "coordinates": [ [ [ 1, 1 ], [ 1, 2 ], [ 2, 2 ], [ 2, 1 ] ] ] } }
```

The  script extracts the coordinates of the polygon to create the body of the Traffic Analytics API request using `template.json` as a starting point. It also sets the start and end date for the query. The script can be modified to set other parameters of the query.

Note that the notebook currently only prints out the requests. The Traffic API does not handle multiple requests and the section for sending requests has been commented out. The accompanying `get traffic analytics` script should not be used until the the API can handle multiple requests.

## Step 2: Calculating congestion scores

The congestion score is calculated from the Traffic Analytics data stored in this [directory](./traffic_analytics).

> congestion metric is: 1 −  𝐶𝑢𝑟𝑟𝑒𝑛𝑡𝑆𝑝𝑒𝑒𝑑/𝐹𝑟𝑒𝑒𝐹𝑙𝑜𝑤𝑆𝑝𝑒𝑒𝑑  (floor at 0)

> weighted congestion by length of road segment: ∑𝑆𝑒𝑔𝑚𝑒𝑛𝑡𝐿𝑒𝑛𝑔𝑡ℎ ∗(1 −𝐶𝑢𝑟𝑟𝑒𝑛𝑡𝑆𝑝𝑒𝑒𝑑𝐹𝑟𝑒𝑒𝐹𝑙𝑜𝑤𝑆𝑝𝑒𝑒𝑑)〗

These scores are added to the traffic analytics data and saved to the same directory. 

## Step 3: Preparing NavStreets for joining congestion scores

The traffic data have to and from road `LINK-IDs`. These fields must be added to the NavStreets data to join the congestion data to display the data on a map. The `HERE_Streets_to_TA` notebook adds and populates these field. The NavStreets data should be clipped to the area of interest in the reference file to reduce the amount of processing.

## Step 4: Joining congestion scores to NavStreets

The `join and export geojson` notebook joins the updated StreetsNav file to the congestion data and exports the dataframe as a geojson file. The output file has only the minimum needed to display the data in a web mapping application.