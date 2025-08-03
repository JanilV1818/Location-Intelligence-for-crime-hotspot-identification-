# Location-Intelligence-for-crime-hotspot-identification-
Pandas, FastAPI, Flask, Scikit-learn, Google Maps API, PostgreSQL, etc.  ArcGIS  Pro, ArcGIS Online, ArcGIS Dashboards, ArcGIS Experience Builder, ArcGIS Story Maps, etc.

Pandas (Data Analysis & Preprocessing)
import pandas as pd

# Load dataset
df = pd.read_csv("crime_data.csv")

# Basic cleaning
df.dropna(subset=['latitude', 'longitude', 'crime_type'], inplace=True)

# Filter specific crime types
violent_crimes = df[df['crime_type'].isin(['Assault', 'Robbery', 'Homicide'])]

# Group by location
crime_counts = violent_crimes.groupby(['latitude', 'longitude']).size().reset_index(name='count')

from fastapi import FastAPI
import pandas as pd

app = FastAPI()

@app.get("/hotspots/")
def get_hotspots():
    df = pd.read_csv("hotspot_data.csv")
    return df.to_dict(orient="records")

from flask import Flask, jsonify
import pandas as pd

app = Flask(__name__)

@app.route('/hotspots', methods=['GET'])
def hotspots():
    df = pd.read_csv("hotspot_data.csv")
    return jsonify(df.to_dict(orient="records"))

if __name__ == '__main__':
    app.run(debug=True)
from sklearn.cluster import KMeans
import numpy as np

# Coordinates of crimes
coords = crime_counts[['latitude', 'longitude']].values

# Apply KMeans clustering
kmeans = KMeans(n_clusters=5, random_state=0).fit(coords)

crime_counts['cluster'] = kmeans.labels_

<script src="https://maps.googleapis.com/maps/api/js?key=YOUR_KEY&libraries=visualization"></script>
<script>
  var heatmapData = [
    new google.maps.LatLng(37.782, -122.447),
    new google.maps.LatLng(37.782, -122.445),
    // Add your clustered hotspot data here
  ];

  var heatmap = new google.maps.visualization.HeatmapLayer({
    data: heatmapData
  });
  heatmap.setMap(map);
</script>

CREATE EXTENSION postgis;

-- Create crime table
CREATE TABLE crime_data (
    id SERIAL PRIMARY KEY,
    crime_type TEXT,
    occurred_at TIMESTAMP,
    location GEOGRAPHY(Point, 4326)
);

-- Sample insert
INSERT INTO crime_data (crime_type, occurred_at, location)
VALUES ('Theft', NOW(), ST_GeogFromText('SRID=4326;POINT(72.8567 19.0760)'));

Project Workflow Summary

1. Collect Data (CSV/GeoJSON/API)
2. Clean with Pandas
3. Cluster/Model with Scikit-learn
4. Store in PostgreSQL/PostGIS
5. Build API (FastAPI/Flask)
6. Visualize: Google Maps / ArcGIS Online
7. Dashboards & Storytelling via ArcGIS Apps
