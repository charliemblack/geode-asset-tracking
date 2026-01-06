# Apache Geode Asset Tracking

This project demonstrates Apache Geode's Lucene indexing capabilities for geospatial data, using spatial queries over indexed locations.

The project consists of three parts:
1. **Base Library**: Contains all common classes.
2. **Simulator**: Uses a KML file of active truck lanes in California to randomly move vehicles around the state.
3. **Web Server**: Hosts a mapping application.

The focus of this project is on showcasing Apache Geode application development rather than creating a complex application. However, it can easily be extended to correlate tracking information with "beacon" information, providing a 360-degree view of the beacon.

This 360-degree profile enrichment is accomplished using "cache listeners," "async event listeners," or "cache writer." The key is to stage the data with the identifier for the beacon, allowing for high throughput with in-memory performance. Geode enhances this by co-locating regions, ensuring beacons operate on the servers holding the data.

The choice of technique depends on the use case:

* **CacheListener** - Synchronous, triggered after the data has been persisted in the context of a "put".
* **CacheWriter** - Synchronous, triggered before the data has been persisted in the context of a "put".  A CacheWriter can also prohibit a data item from being actually stored.  This could be treated as a data validation.
* **AsyncCacheListener** - Asynchronous, triggered after the data has been stored and operates outside the context of a "put".

## How to Use

### Install Apache Geode

Follow these steps to install Apache Geode:

1. **Install Geode**: Download and unpack the Apache Geode distribution.

### Build the Projects

Before starting Geode, build the libraries for our domain objects in the [asset tracker library](tracker-lib) by running the `gradlew bootJar` command, then copy runtime dependencies:

```shell
gradlew bootJar
```

```shell
gradlew tracker-lib:copyLuceneLibs
```
This copies Geode’s Lucene/Spatial runtime jars into `tracker-lib/build/lucene-libs` so the server classpath can pick them up when starting Geode.

### Run Geode

To simplify running Geode, batch scripts are provided for local setup and configuration. For Windows, use the following command:

```shell
cd <project>\scripts
startGeode.bat
```

This command starts Geode with one locator and two servers, then deploys the necessary classes for Geode Lucene indexing and searching. The deployed project is located in the [library](tracker-lib/src/main/java/demo/geode/asset/tracker/lib).

The script also creates the index and a region for the demo.

### Start the Simulator

The simulator injects the current location of assets we are tracking. It simulates movement around California using trucking lanes data. The [`development` setting](tracker-simulator/src/main/resources/config/application.yml) configures 100k assets moving around.

```shell
cd <project>
gradlew tracker-simulator:bootRun
```

### Start the Web Application

The web application uses OpenLayers for mapping capabilities and provides a REST interface to query beacons in a given area. The code for querying beacons can be found [here](tracker-web-app/src/main/java/demo/geode/asset/tracker/web/GeospatialWebServer.java).

```shell
cd <project>
gradlew tracker-web-app:bootRun
```

Open your browser and navigate to [http://localhost:8080](http://localhost:8080). Use **Ctrl+Drag** (or **Command+Drag** on Mac) to draw boxes over California to query the beacons. Start with smaller boxes to gauge your browser's performance, as no result limiting was implemented to keep the code simple.

![Example Query](/images/sample_query.png)

## Debugging Tips for Windows

To kill all Java processes, use the following command:

```shell
taskkill /f /im java.exe
```

>curl "http://localhost:8080/query?minLng=-117.1208548486265&minLat=32.83976867526056&maxLng=-117.11506127715433&maxLat=32.843590641384694" 
[{"lat":32.84179,"lng":-117.11903,"uid":"95299"},{"lat":32.841824,"lng":-117.11555,"uid":"60688"},{"lat":32.84179,"lng":-117.11868,"uid":"83857"},{"lat":32.841812,"lng":-117.11744,"uid":"74060"},{"lat":32.84179,"lng":-117.11903,"uid":"98491"},{"lat":32.841812,"lng":-117.11744,"uid":"73182"},{"lat":32.841824,"lng":-117.11555,"uid":"58739"},{"lat":32.84182,"lng":-117.11689,"uid":"66694"},{"lat":32.84179,"lng":-117.11903,"uid":"92442"},{"lat":32.84179,"lng":-117.11903,"uid":"92325"},{"lat":32.841866,"lng":-117.11413,"uid":"52801"},{"lat":32.84182,"lng":-117.11689,"uid":"69312"},{"lat":32.841866,"lng":-117.11413,"uid":"53019"},{"lat":32.841812,"lng":-117.11744,"uid":"81841"},{"lat":32.841824,"lng":-117.11555,"uid":"57492"},{"lat":32.84179,"lng":-117.11903,"uid":"96609"},{"lat":32.841824,"lng":-117.11555,"uid":"56277"},{"lat":32.84179,"lng":-117.11903,"uid":"95898"},{"lat":32.841812,"lng":-117.11744,"uid":"75009"}]

