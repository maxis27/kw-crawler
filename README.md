# KW Crawler

## Building

To build the project, run the following command:

```bash
mvn clean package
```

This will create a JAR file in the `target` directory.

## Downloading

```bash
java -jar target/kw-crawler-1.0-SNAPSHOT.jar download --max 160000 WL1A/00XXXXXX/X
```

## Searching

First, index the data for a given district:

```bash
java -jar target/kw-crawler-1.0-SNAPSHOT.jar index WL1A
```

Then you can search the index:

```bash
java -jar target/kw-crawler-1.0-SNAPSHOT.jar search 'text'
```

or

```bash
java -jar target/kw-crawler-1.0-SNAPSHOT.jar search '+lokalizacja:Miedźno +właściciel:Jan'
```

## Mapping

1. Start Docker Compose with PostGIS and GeoServer:
    ```bash
    docker-compose up -d
    ```
2. Create a table in PostGIS:
    ```bash
    pgcli -h localhost -p 15432 -U postgres -d postgres
    ```

    ```sql
    CREATE TABLE my_geometries (id text, description text, owner_type text, geom GEOMETRY(Geometry, 2180));
    ```
3. Map the data:
    ```bash
    java -jar target/kw-crawler-1.0-SNAPSHOT.jar map WL1A
    ```
4. Add a layer into GeoServer:
    - Go to http://localhost:18080/geoserver/
    - Log in with `admin:geoserver`
    - Add a new workspace: `local`
    - Add a new store: `PostGIS`
        - Workspace: `local`
        - Data Source Name: `kw`
        - Host: `postgis`
        - User: `postgres`
        - Password: `postgres`
        - Schema: `public`
        - Table: `my_geometries`
    - Add a new layer: `my_geometries`
5. Define the following style from the file `geoserver-style.xml` and make it the default for the layer.

6. Open `index.html` in your browser to see the map.
