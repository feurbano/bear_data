# Bear data repository of Provincia di Trento
### Documentation of the shared database
Last update: 17 Dec 2017
 
## Content  

1. [Introduction](#introduction)
2. [Connection parameters](#connectionparameters)
3. [Data content](#datacontent) 
4. [Data structure](#datastructure)
5. [Client tools](#clienttools)
6. [Other database in the FEM DB cluster](#femcluster) 

## <a name="introduction"></a> Introduction  
The bear data collected by  *Provincia di Trento* are stored in a spatial relational database based on the software platform PostgreSQL/PostGIS. The database is hosted at Mach Foundation (Italy). All the information in the database was checked, structured, standardized and coded (whenever possible) to facilitate data use and sharing.   
The database is designed to (potentially) store data from different groups, if this will be an option in the future. In this case some additional work might be needed to harmonize datasets form different sources, particularly for non-tracking data.   
Users can connect with the database to visualize/analyze/manage the data using remote client applications (if they have an account with the permission to do so) with no need for data replication. Multiple accesses to the database are managed by the server. Appropriate mechanisms and rules are set up inside the database to grant data consistency.   
In this document, users can find the basic information needed to understand the data content and use the database.   
If you want to know more about the advantages of database to store and manage wildlife biologging data, give a look to the paper [Wildlife tracking data management: a new vision](http://rstb.royalsocietypublishing.org/content/365/1550/2177).

If you want to learn more about SQL, standard language for storing, manipulating and retrieving data in databases, you can look on the web one of the many free tutorial/courses.   
If you are specifically interested to database for biologging data, the project can provide you a copy of the book [Spatial Database for GPS Wildlife Tracking Data](http://www.springer.com/us/book/9783319037424).  

## <a name="connectionparameters"></a> Connection parameters  
The database can be accessed using the following parameters:

	  IP or Domain name of the server: eurodeer2.fmach.it
	  Server Port: 5432
	  Name of the Database: bear_db
	  User Name: ...
	  Password: ...


If you don't have a user account (and you are entitle to have you) please contact Francesca Cagnacci or Luca Pedrotti or Ferdinando Urbano.

Each client software has a different interface to PostgreSQL/PostGIS. For some examples see section [Client tools](#clienttools).

## <a name="datacontent"></a> Data content  

The database cointains two main data sets:

1. Tracking data (GPS sensors)
2. Genetic data

The two data sets also include additional information about the animals (for more details see [Schema Main](#schemamain))

*To be extended [...]*

**At the moment only tracking (and related) data have been uploaded. Genetic data and information about cubs are being processed and are expected to be available by November 30th.**

## <a name="datastructure"></a> Data structure  
A database contains one or more named [schemas](https://www.postgresql.org/docs/current/static/ddl-schemas.html), which in turn contain tables. Schemas also contain other kinds of named objects, including data types, functions, and operators.  
Schema are used to organize database objects into logical groups, to make them more manageable, and to allow many users to use one database without interfering with each other. Schema are similar to folders in a file system.

The schema in the bear_db at the moment are:

* **main**: This schema is the place where all the core information related to the main objects involved in bear monitoring are stored: data from sensors, sensors, animals, research groups, deployments, genetics.	
* **env_data**: This schema hosts all the environmental used to enhance location data (DEM, slope, aspect, Corine land cover [not yet available], NDVI Modis [not yet available], snow Modis [not yet available].	
* **lu_tables**: This schema is where the look up tables are stored. These tables are the list and the description of codes referenced by other tables in the database and are a kind of valid domains for specific fields.	
* **tools**: This schema hosts all the functions and tools that are used throughout the database to manage, massage, analyse and query data.
* **data_trentino**: This schema is used to store the original data provided by Trentino (Italy)	and is accessible by users from this research group only.
* **public**: Standard public schema used by the system. No information on bears is stored here.

In the next section, a detailed description of all the tables and views in *main* schema and a general review of tables in the other schemas are reported.  

### <a name="schemamain"></a> SCHEMA MAIN

#### Table animals
Table with the information on the animals that are part of the project. Information included in the table are sex, year of birth (exact or estimated), name and id in the original data set. 

Fields:

* *animals_id*:  Database id of the animal.	
* *research\_groups_id*:  Research group that monitors the animal.	
* *animals\_original_id*:  Identifier of the animal in the original data set.	
* *animals\_original_name*:  Nome of the animal in the original data set.	
* *sex*:  Code for sex. It can be either "f" (female) or "m" (male). When the sex is not known, the field can be empty.	
* *notes*:  Open field where general notes on the animal can be added.	
* *year\_birth*:  Year of birth (when known). In the year\_birth_exact field it is described if this is the exact year of birth of just an estimation (minimum year of birth).
* *year_birth_exact*:  Flag (yes/no) that specifies if the year of birth is exact (yes) or just an estimation (no) (e.g. when I know that the animal is at least 4 years old but I do not know the exact age.	
* *animals_original\_name_father*:  Nome of the father of the animal in the original data set.	
* *animals_original\_name_mother*:  Nome of the mother of the animal in the original data set.	
* *animal_id_father*:  Database id of the father of the animal.	
* *animal_id_mother*:  Database id of the mother of the animal.	
* *fate\_code*:  Fate of the animal, value connected to the lookup table lu_fate.	
* *problematic_code*:  Code of the problematic behaviour.	
* *problematic_date*:  Date when the animale started to be problematic.
* *insert_timestamp*:  Date and time when the record was uploaded into the database.	
* *update_timestamp*:  Date and time when the record was updated (last time).

#### Table animals_captures 
Table with the information on captures. This table might have to be modified when data on genetics will be included.

Fields:

* *animals_captures_id*: Database ID of the capture of an animal. Each animal can have multiple captures.	
* *animals_id*: Database ID of the animal (external key to the table main.animals).	
* *capture_timestamp*: Time and date (with time zone) when the animal was captured (animal fall in the trap, box, net, etc.).	
* *release_timestamp*: Time and date (with time zone) when the animal was released (animal taken out from the box, trap, net etc. or put back into a transportation box).	
* *handling_start*: Time and date (with time zone) when the animal handling started (taken out from the box, trap, net etc. and is in direct contact with people).	
* *handling_end*: Time and date (with time zone) when the animal handling ended (animals is no more in direct contact with people for marking and measurements or it is released or put back in to a transportation box).	
* *longitude_captures*: Coordinate of the capture (can be an approximation).	
* *latitude_captures*: Coordinate of the capture (can be an approximation).	
* *geom_capture*: Location (point) of the capture (can be an approximation).	
* *gps_sensors_animals_id*: In case the animal has been collared with GPS, this is the id of the related deployment.	
* *notes*: General notes on the capture.

#### Table: gps\_data_animals
Table with GPS locations associated to animals and with a list of derived ancillary information calculated using the coordinates and the acquisition time, and intersecting with environmental layers.	

Fields:

* *gps_data_animals_id*: DB identifier for the location	
* *animals_id*: DB identifier for the animal	
* *gps_sensors_id*: DB identifier for the GPS sensor	
* *acquisition_time*: Date and time of acquisition of the GPS coordinates (with time zone)	
* *geom*: Geometry of the location (point, 4326)	
* *latitude*: Latitude recorded by the GPS sensor	
* *longitude*: Longitude recorded by the GPS sensor	
* *altitude_gps*: Altitude recorded by the GPS sensor	
* *dop*: Dilution Of Precision	
* *temperature_sensor*: Temperature as measured by the sensor associated to the GPS	
* *fixtype*: Generic field with information from gps sensors on the quality of the fix. Not coded as it depends on data providers
* *x_laea*: X coordinate projected in the european reference system Lambert Azimuthal Equal Area (SRID 3035)	
* *y_laea*: Y coordinate projected in the european reference system Lambert Azimuthal Equal Area (SRID 3035)	
* *gps\_validity\_code*: This field tags the record according to its "validity" or degree of reliability (explanation of codes in lu\_tables.lu\_gps\_validity)	
* *sun_angle*: Sun angle above (or below) the horizon (in degrees) as computed by the function tools.sun_elevation_angle_function	
* *corine\_land\_cover\_1990\_code*: Not yet available	
* *corine\_land\_cover\_2000\_code*: Not yet available	
* *corine\_land\_cover\_2006\_code*: Not yet available	
* *corine_land\_cover\_2012\_code*: Not yet available	
* *ndvi\_modis\_smoothed*: Not yet available	
* *snow_modis*: Not yet available	
* *altitude_copernicus*: Altitude in meters	(source: copernicus project, v1.0)
* *slope_copernicus*: Degrees, -9999 (NULL), (source: copernicus project, v1.0), generated with gdaldem	
* *aspect_copernicus*: Degrees calculated clockwise from north, -9999 (NULL) means no aspect (flat) (source: copernicus project, v1.0), generated with gdaldem	
* *forest_density*: Not yet available	
* *insert_timestamp*: Date and time when the record was uploaded into the database.	
* *update_timestamp*: Date and time when the record was updated (last time).


#### Table gps_sensors
Catalogue of GPS sensors. Each sensor belongs to a research group. The attributes include the brand and the model. The id used in the original data set is also included.	

Fields:

* *gps\_sensors_id*: DB identifier for GPS sensors.
* *research\_groups_id*: Id of the research group that owns the GPS sensor.
* *gps\_sensors\_original_id*: Identifier of the GPS sensor in the original data set.
* *vendor*: Company that produced the sensor.
* *model*: Model of the GPS sensor.
* *insert_timestamp*: Date and time when the record was uploaded into the database.

#### Table gps\_sensors_animals
Table with the information on the deployments of GPS sensors on animals (starting and ending date and time of the deployment), reason of the end of deployment. 

Fields:

* *gps\_sensors\_animals_id*: DB identifier of the deployment.
* *gps\_sensors_id*: DB identifier of the GPS sensor.
* *animals_id*: DB identifier of the animal.
* *start_time*: Time and date of the start of the deployment.
* *end_time*: Time and date of the end of the deployment.
* *end\_deployment_code*: Code for the reason of the end of deployment (reference to the look up table lu\_tables.lu\_end\_deployment).
* *mortality_code*: If the reason of the end of deployment is dead, this field specifies the reason of the death (code described in the look up table lu_tables.lu_mortality
* *animals\_captures_id*: Reference to the capture when the sensor was deployed on the animal.
* *notes_original*: Notes as reported in the original data set.
* *update_timestamp*: Date and time when the record was updated (last time).
* *insert_timestamp*: Date and time when the record was uploaded into the database.
* *end\_gps\_monitoring_code*: Code for the reason of the end of monitoring (reference to the look up table lu\_tables.lu\_end\_gps\_monitoring).
* *notes*: Open field where general notes on the deployment can be added.

#### Table research_groups
Research groups that joined the project. At the moment, only Provincia di Trento is in.

Fields:

* *research\_groups_id*: DB identifier for research groups.
* *research\_group_name*: Name of the research group.
* *contact*: Contact person of the research group for the project.
* *institution*: Institute of the research group.
* *short_name*: Short name of the research group.
* *country*: Country of the research group.
* *geom*: Approximate location of the research group.
* *year_joined*: Year when the group joined the project.
* *insert_timestamp*: Date and time when the record was uploaded into the database.

#### VIEWS
A view is the result set of a stored query on the data, which the database users can query just as they would in a persistent database collection object. This pre-established query command is kept in the database dictionary. Unlike ordinary base tables in a relational database, a view does not form part of the physical schema: as a result set, it is a virtual table computed or collated dynamically from data in the database when access to that view is requested. Changes applied to the data in a relevant underlying table are reflected in the data shown in subsequent invocations of the view.

* *view\_bear\_gps\_positions*: View with the valid locations with name of the animal and reference research group.
* *view_convexhull*: View with the convex hull of all valid locations for each animal.	
* *view_trajectories*: View with the trajectory as linear spatial feature for each animal.

### SCHEMA ENV_DATA

* Table **dem_copernicus**: Altitude. Raster layer. Source: copernicus project v1.0, resolution 25 meters.	
* Table **aspect_copernicus**: Aspect. Raster layer. Source: copernicus project v1.0, resolution 25 meters, generated with gdaldem from DEM.
* Table **slope_copernicus**: Slope. Raster layer. Source: copernicus project v1.0, resolution 25 meters, generated with gdaldem from DEM.

### SCHEMA LU_TABLES

* Table **lu\_end_deployment**: Look up table for end\_deployment_code field: it specifies the meaning of the code used to identify the reasons of the end of deployment.	
* Table **lu_fate**: Look up table for fate_code field (table main.animals): it specifies the meaning of the code used to identify the fate of the animal.	
* Table **lu\_gps_validity**: Look up table for GPS locations validity.	
* Table **lu_problematic**: Look up table for problematic_code field: it specifies the meaning of the code used to identify the problematic behaviuor of the animal.

## <a name="clienttools"></a> Client tools
While data are (safely and consistently) stored in the database, users can use client applications to access the data. Data are not duplicated but a single, controlled version is available to all users. According to the task that must be performed (including a plain export to .csv for they who are allergic to databases), users can use one of the many clients available. For tabual data visualization, query, download, the recommended tools are PgAdmin (3 or 4) or PhpPgAdmin. For visualization and processing of spatial data, QGIS is the best client. For statistical analysis, R is the most used tool. LibreOffice offers a complete suite of products to interact with the database.  
Data can also be imported in the specific software, and processed independently from the version in the database, although this means that a new "version" of the data is created with consequent problem or synchronization (if the dataset is updated).

If you need technical support, please contact the project or the database manager.

All these tools are open source, standard compliant, efective and well documented. It is also possible to access the data using proprietary software like ArcGIS, Office, SAS. In this case, interested users can look for tutorial on how connect these tools to the database.

### phppgAdmin
The data in the database can be accessed, explored, queried and downloaded (as csv) using a web based tool available at [http://eurodeer2.fmach.it/phppgadmin/](http://eurodeer2.fmach.it/phppgadmin/). There is no need to install any software on the local computer. The interface is very intuitive and similar to PgAdmin, with a structure pretty similar to a file system exporer. This is the recommended interface for basic users. It is not possible to visualize spatial data. For an example, see the figure below.

FIGURE

### PgAdmin
[PgAdmin](https://www.pgadmin.org/) is the most popular and feature rich Open Source administration and development platform for PostgreSQL. There are two versions: 3 and 4 (the former is more intuitive, the latter more modern and powerful). This is the best tool to manage the data and explore both structure and content, including the formulation of advanced queries and the creation of new database objects.
Here below and example of the window with the connection parameters.

FIGURE

### QGIS
[QGIS](www.qgis.org) is a desktop GIS that is very well integrated with PostgreSQL and PostGIS and offers a large set of tools to deal with spatial data in the database. Connecting to the database is pretty simple and weel documented, for example [here](http://docs.qgis.org/2.18/en/docs/training_manual/databases/index.html). Data can be accessed in three steps: create a connection to the db. Open the connection. Get the data. Here below is reported as an example a screenshot of the connection window in QGIS.

figure
figure

![](https://i.imgur.com/u8ld0ky.png)

### R
You can easily import data from the database into [R](https://www.r-project.org/) using a code like the one reported below:

	library(RPostgreSQL)
	drv <- dbDriver("PostgreSQL")
	con <- dbConnect(drv, dbname="bear_db", host="eurodeer2.fmach.it", port="5434", user="YOURUSER", password="YOURPASSWORD")
	rs <- dbSendQuery(con, "select * from main.animals")
	df <- fetch(rs,-1)
	df[1:4,]
	str(df)
	dbClearResult(rs)
  
In the *dbSendQuery* command, you can include any SQL code.

Note that in the database is activated [Pl/R](http://www.joeconway.com/plr.html), a tool that permits to embed R code into SQL. In addition, there are other R packages that facilitate the acquiition of spatial data.

### LibreOffice
Base and Calc are two tools of the suite [LibreOffice](https://www.libreoffice.org/) that allow the connection with the database and offer the possibility to create queries with graphical tools, build masks, edit the data in a user-friendly environment and visualize the information as spreadsheet.
More information on how to connect with PostrgeSQL can be found in the LibreOffice documentation.

## <a name="femcluster"></a> Other database in the FEM DB cluster
A PostgreSQL database cluster contains one or more named databases. In this case, **bear_db** is hosed in the same server used by the euroungulates project (eurodeer, eureddeer and euroboar database). Users and groups of users are shared across the entire cluster, but no other data is shared across databases. Any given client connection to the server can access only the data in a single database, the one specified in the connection request.

Users of a cluster do not necessarily have the privilege to access every database in the cluster. This means that user of euroungulates do not have access to bear_db and bear_db users cannot access euroungulates databases.
