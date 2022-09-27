# flightplan2litchimission
`fp2lm` is a command-line tool for converting the output generated by the [Flight Planner](https://github.com/JMG30/flight_planner) plugin for [QGIS](https://www.qgis.org/en/site/) to a [Litchi](https://flylitchi.com) mission.

Usage: `cat [FlightplannerMission].csv | fp2lm > [LitchiMission].csv`

## Description

`fp2lm` reads a stream of waypoints generated by Flight Planner for QGIS and prints a stream of properly-structured Litchi Mission waypoints, line-by-line, to standard output (this is to allow subsequent stream editing using another tool, if desired).  I/O redirection may be used to capture the output in a new file for use by Litchi. 

## Building

`go build fp2lm.go`

## Steps to produce a Litchi Mission using QGIS

1) Install the flight_planner plugin from QGIS → Plugins → Manage and Install Plugins... and search for 'Flight Planner'.
2) Load the map layer of your choice.  To use Google Earth or OpenStreetMap, select 'XYZ Tiles' in your project's browser and add it as a layer to your project by double-clicking or right-clicking and selecting 'Add Layer to Project'.
3) Scribe your Area of Interest (AoI) by creating a new shapefile layer from Layer → Create Layer → New Shapefile Layer.  Select 'Polygon' as the Geometry type.  Select the desired points on the map. ℹ️ Depending on the CRS you are using, you may need to change the CRS of the AoI to work with Flight Planner — which requires measurements in meters.
4) Follow the [instructions](https://github.com/JMG30/flight_planner/wiki/Guide) for Flight Planner to plan your flight.  ℹ️ If you are using a DJI drone, you will probably need to add your own camera lens.  Consult the manufacture's specifications.
5) You will need to create latitude and longitude coordinates for use by Litchi.  Fortunately, QGIS makes this easy. With the flight plan generated, select the `waypoints` layer in the newly-created `flight_design` layer group.  Select Vector → Geometry Tools → Add Geometry Attributes.  Select your AoI layer, and calculate the latitude and longitude coordinates using an appropriate CRS (for example, EPSG:4326).  Add the new layer to your project.  ℹ️ The newly-created layer will have two new fields for latitude and longitude called `xcoord` and `ycoord`.  You may verify the new values by right-clicking on the layer and selecting Open Attribute Table.
6) Export the new layer with latitude and longitude points add to a CSV file. ℹ️ The exported file should have the following header: `️Waypoint Number,X [m],Y [m],Alt. ASL [m],Alt. AGL [m],xcoord,ycoord`
7) Run `fp2lm` against the CSV file as described above.

**_NOTE:_** `fp2lm` expects a CSV file of waypoints.  At the time of this writing, Litchi missions are limited to 100 waypoints, thus if waypoints are used to trigger photographs, the allowed waypoints will be quickly consumed; therefore, for this workflow, waypoints are only used for course changes.  Photograph intervals may be set expediently by measuring the distance between projection centres in QGIS and configuring Litchi to photograph at equal distance intervals.  This is a work-around until Litchi adds support for more waypoints.