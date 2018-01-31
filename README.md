# Bear data repository of Provincia di Trento
### Documentation of the (shared) database
Last update: 10 Dec 2017
 
## Content  

1. [Introduction](#introduction)
2. [Connection parameters](#connectionparameters)
3. [Data content](#datacontent) 
4. [Data structure](#datastructure)
5. [Client tools](#clienttools)
6. [Other database in the FEM DB cluster](#femcluster) 

## <a name="introduction"></a> Introduction  
The bear data collected by  *Provincia di Trento* are stored in a spatial relational database based on the software platform PostgreSQL/PostGIS. The database is hosted at Mach Foundation (Italy). All the information in the database is checked, structured, standardized and coded (whenever possible) to facilitate data use and sharing.   
The database is designed to (potentially) store also data from different groups, **if** this will be a requirement in the future. In this case some additional work will be needed to harmonize datasets form different sources (and potentially adapt/extend the database data model), particularly for non-tracking data.   
Users can connect to the database to visualize/analyze/manage/modify the data using remote client applications (if they have an account with the permission to do so) with no data replication (i.e. no potential inconsistencies generated by multiple versions of the same data set). Accesses to the database by several users at the same time are managed by the server. Appropriate mechanisms and rules are set up inside the database to grant data consistency. 

In this document, users can find the basic information to understand the data content and to connect to and use the database.   
If you want to know more about the advantages of database to store and manage wildlife biologging (but not only) data, give a read to the paper [Wildlife tracking data management: a new vision](http://rstb.royalsocietypublishing.org/content/365/1550/2177).

This document is not a guide to SQL, which is needed if you want to do more than just download or visualize tables. If you want to learn more about SQL, (the standard language for storing, manipulating and retrieving data in relational databases), you can look on the web for one of the many free tutorial/courses.   
If you are specifically interested to database for biologging data, the project can provide you a copy of the book [Spatial Database for GPS Wildlife Tracking Data](http://www.springer.com/us/book/9783319037424).  

If project partners need to format/combine data in a specific way and do not have the SQL knowledge to do it, we can support, just send an email to one of the project coordinators with the details of what you want.

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

The two data sets also include additional information about the animals (for more details see [Schema Main](#schemamain)). Data are organized in a set of related tables. The core table is the list of monitored animals with their key attributes (table main.animals).

*To be extended [...]*

## <a name="datastructure"></a> Data structure  
A database contains one or more named [schemas](https://www.postgresql.org/docs/current/static/ddl-schemas.html), which in turn contain tables. Schemas also contain other kinds of named objects, including data types, functions, and operators.  
Schema are used to organize database objects into logical groups, to make them more manageable, and to allow many users to use one database without interfering with each other. Schemas are similar to folders in a file system.

The schemas in the bear_db at the moment are:

* **main**: This schema is the place where all the core information related to the main objects involved in bear monitoring are stored: animals, genetics, data from sensors, sensors, deployments, research groups.	
* **env_data**: This schema hosts all the environmental used to enhance location data (DEM, slope, aspect, Corine land cover [not yet available], NDVI Modis [not yet available], snow Modis [not yet available].	
* **lu_tables**: This schema is where the look up tables are stored. These tables are the list and the description of codes referenced by other tables in the database and are a kind of valid domains for specific fields.	
* **tools**: This schema hosts all the functions and tools that are used throughout the database to manage, massage, analyse and query data.
* **data_trentino**: This schema is used to store the original data provided by Trentino (Italy) and is accessible by users from this research group only.
* **public**: Standard public schema used by the system. No information on bears is stored here.

Here below, the figure represents the complete structure of the database objects (tables). All tables with suffix *lu_* are use to store the set of valid values for specific fields. In the picture, the tables are reported with the list of their columns, the primary and external keys and the links between tables (click on it to enlarge the picture).
<p align="center">
  <img src="https://github.com/feurbano/bear_data/blob/master/beardb_complete.png" Height="350"/>	
</p>
In the images below, two simplified version of the tables structure are illustrated. The first one with all the tables.
<p align="center">
  <img src="https://github.com/feurbano/bear_data/blob/master/beardb_schema.png" Height="350"/>	
</p>
The second one with just core tables (no look up tables).
<p align="center">
  <img src="https://github.com/feurbano/bear_data/blob/master/beardb_core.png" Height="350"/>	
</p>

In the next section, a detailed description of all the tables and views in *main* schema and a general review of tables in the other schemas are reported.  

### <a name="schemamain"></a> SCHEMA MAIN

#### Table animals
Table with the information on the animals that are part of the project. Information included in the table are sex, year of birth (exact or estimated), name and id in the original data set. 

Fields:

* *animals_code*:  Name (code) of the animal that is used as identifier of the animal.	Primary key.
* *animals_id*: Code used to identify the animal (when an integer instead of a string must be used).	
* *research\_groups_id*:  Research group that monitors the animal.	
* *sex_code*:  Code for sex. It can be either "f" (female) or "m" (male). When the sex is not known, the field can be empty.	
* *notes_general*:  Open field where general notes on the animal can be added.	
* *year\_birth*:  Year of birth (when known). In the year\_birth_exact field it is described if this is the exact year of birth of just an estimation (minimum year of birth).
* *year_birth\_exact*:  Flag (yes/no) that specifies if the year of birth is exact (yes) or just an estimation (no) (e.g. when I know that the animal is at least 4 years old but I do not know the exact age.	
* *fate_code*:  Fate of the animal, value connected to the lookup table lu_fate.	
* *problematic_code*: Code of the problematic behaviour, value connected to the lookup table lu_problematic.	
* *problematic_date*: Date when the animale started to be problematic.
* *origin_code*:  Code of the origin of the animal, value connected to the lookup table lu_origin.
* *notes_area*:  Open field where general notes on the area of the animal are reported.
* *notes_fate*:  Open field where general notes on the fate of the animal are reported.
* *insert_timestamp*:  Date and time when the record was uploaded into the database.	
* *update_timestamp*:  Date and time when the record was updated (last time).

#### Table: animals_genotypes
Table with info on genotypes. Relation 1 to 1 with table main.animals. (description of the table to be extended)

Fields:

* *animals_code*: identification code for each bear	
* *cxx20_a*: Allele size of specified str lucus	
* *cxx20_b*: Allele size of specified str lucus	
* *g10m_a*: Allele size of specified str lucus	
* *g10m_b*: Allele size of specified str lucus	
* *g10p_a*: Allele size of specified str lucus	
* *g10p_b*: Allele size of specified str lucus	
* *g10x_a*: Allele size of specified str lucus	
* *g10x_b*: Allele size of specified str lucus	
* *g1d_a*: Allele size of specified str lucus	
* *g1d_b*: Allele size of specified str lucus	
* *mu11_a*: Allele size of specified str lucus	
* *mu11_b*: Allele size of specified str lucus	
* *mu15_a*: Allele size of specified str lucus	
* *mu15_b*: Allele size of specified str lucus	
* *mu23_a*: Allele size of specified str lucus	
* *mu23_b*: Allele size of specified str lucus	
* *mu50_a*: Allele size of specified str lucus	
* *mu50_b*: Allele size of specified str lucus	
* *mu59_a*: Allele size of specified str lucus	
* *mu59_b*: Allele size of specified str lucus	
* *g10c_a*: Allele size of specified str lucus	
* *g10c_b*: Allele size of specified str lucus	
* *g10h_a*: Allele size of specified str lucus	
* *g10h_b*: Allele size of specified str lucus	
* *g10l_a*: Allele size of specified str lucus	
* *g10l_b*: Allele size of specified str lucus	
* *mu09_a*: Allele size of specified str lucus	
* *mu09_b*: Allele size of specified str lucus	
* *mu10_a*: Allele size of specified str lucus	
* *mu10_b*: Allele size of specified str lucus	
* *sry_a*: Allele size of specified str lucus	
* *sry_b*: Allele size of specified str lucus	
* *notes_loci*: notes about str loci

#### Table: animals_parents
Table with info on parents. Relation 1 to 1 with table main.animals. (description of the table to be extended)

Fields:

* *animals_code*: identification code for each bear
* *paternity_reliability_code*: reliability for paternity assignment
* *pedigree*: parents assignment
* *pedigree_note*: notes on pedigree assignment
* *software_used*: software used for pedigree analysis
* *alternative_mother*: Alternative hypothesis of mother assignment
* *alternative_father*: Alternative hypothesis of father assignment
* *alternative_pedigree*: Alternative hypothesis of parents assignment
* *alternative_pedrigree_note*: notes on alternative pedigree
* *mismatch_number*: number of mismatch in analysed str loci
* *allelic_dropout_number*: number of allelic dropout in analysed str loci
* *mother_animals_code*: mother assignment
* *father_animals_code*: father assignment
* *notes_parents*: notes on parents

#### Table: gps\_data_animals
Table with GPS locations associated to animals and with a list of derived ancillary information calculated using the coordinates and the acquisition time, and intersecting with environmental layers.	

Fields:

* *gps_data_animals_id*: DB identifier for the location	
* *animals_id*: DB identifier for the animal	
* *animals\_code*:  Name (code) of the animal in the original data set.	
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
* *animals\_code*:  Name (code) of the animal in the original data set.	
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
Research groups that joined the project.

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

#### Table main.identifications
Tables that stores all the identifications of bears particularly from genetics, but also derived from damages, radio tracking, sighting, tracks. 

Fields:

* *identifications_id*: DB identifier of the identification record.
* *date_identification*: Date of sample collection or of the observation.
* *year_identification*: Year of sample collection or of the observation.
* *identification_type_code*: Refer to the kind of sample used for bear identifications.
* *sample_impact_code*: Refer to the nature of genetic sample.
* *sample_method_code*: Refer to the sampling strategy.
* *sample_type_code*: Refer to the type of organic sample used for genetic analysis.
* *hair_number_code*: Number of collected hairs (< or > 5).
* *sex_code*: Sex of the identified bear.
* *genotype_code*: If the sample is determined as bear.
* *genotype_identification_code*: Refer to the kind of identification (individual, mixed, undefined).
* *genotype_animal_code*: The code of the identified bear.
* *genotype_animal_confirmed*: If the identification of the individual is confirmed or just supposed.
* *scalp_code*: The code of reliability of sample, according to SCALP methodology.
* *geom*: Spatial object (point) where the sampling/observation occurred.
* *municipality*: Territorial entity in which the sample has been collected (derived from coordinates reported).
* *istat_cod_com*: ISTAT code for italian municipality.
* *note_municipality*: Name of the municipality as reported in the original data source.
* *place*: Place name where the sample has been collected
* *province*: Name of the province as reported in the original data source.
* *region*: Name of the region as reported in the original data source.
* *state*: Name of the country as reported in the original data source.
* *notes_field*: Notes concerning the collection of samples
* *data_origin*: The public administration / agency that stored the sample into its own database (not coded).
* *collector*: People who collected the sample.
* *authority*: The public administration / agency people who own the collected sample.
* *ispra_id*: ID of sample genetically analysed by Italian Institute for Conservation and Research on Nature (ISPRA).
* *old_field_id*: Sample ID used during field collection.
* *field_id*: Sample ID used during field collection.

#### Table main.cubs_unrecognized
Table with the information on the cubs that have been seen with their mother but that were never recognized with genetics (only year and mother are known).

Fields:

* *cubs_unrecognized_id*: Database id of the unrecognized cub.
* *animals_code_mother*: Animal code of the mother of the cub.
* *year*: Year when the cub was seen with her mother.
* *cub_id*: Id of the cub in case more than one cub is seen with a mother in a year.

#### VIEWS
A view is the result set of a stored query on the data, which the database users can query just as they would in a persistent database collection object. This pre-established query command is kept in the database dictionary. Unlike ordinary base tables in a relational database, a view does not form part of the physical schema: as a result set, it is a virtual table computed or collated dynamically from data in the database when access to that view is requested. Changes applied to the data in a relevant underlying table are reflected in the data shown in subsequent invocations of the view.

*view\_animals\_decoded*: In formation from the table animals where all the coded values are reported along with their description.
*view\_animals\_genetics*: View that joins all the inforation from the tables animals, animals_parents, animals_genotypes and where all the coded values are reported with their description.
*view\_bear\_gps\_positions*: View that joins the locations of the animals with the information on individuals.
*view_convexhull*: View with the convex hull of all valid locations per all the animals
*view_cubs*: View with the list of cubs and father for each female/year (it joins known and unknown cubs). All cubs are in a single fields.
*view\_cubs\_split*: View with the list of cubs and father for each female/year (it joins known and unknown cubs).Every cub in a different column (maximum 4 cubs per year/mother).
*view_trajectories*: Complete trajectories as linear spatial features per each of the animals


### SCHEMA ENV_DATA

* Table **dem_copernicus**: Altitude. Raster layer. Source: copernicus project v1.0, resolution 25 meters.	
* Table **aspect_copernicus**: Aspect. Raster layer. Source: copernicus project v1.0, resolution 25 meters, generated with gdaldem from DEM.
* Table **slope_copernicus**: Slope. Raster layer. Source: copernicus project v1.0, resolution 25 meters, generated with gdaldem from DEM.

### SCHEMA LU_TABLES

* Table **lu_end_deployment**: Look up table for end_deployment_code field: it specifies the meaning of the code used to identify the reasons of the end of deployment.
* Table **lu_fate**: Look up table for fate_code field (table main.animals_contacts): it specifies the meaning of the code used to identify the fate of the animal.
* Table **lu_genotype**: Look up table for the genotype (if it is confirmed that the animal is a bear).
* Table **lu_genotype_identification**: Look up table for the genotype identification of the animal (mixed, individual, undefined).
* Table **lu_gps_validity**: Look up table for GPS locations validity.
* Table **lu_hair_number**: Look up table for (the class of) number of hairs taken in a sample (used in the table identifications).
* Table **lu_identification_type**: Look up table for type of identification (used in the table identifications): it specifies the meaning of the code used to identify the animal (e.g. genetic, sighting, etc).
* Table **lu_origin**: Look up table for origin_code field: it specifies the meaning of the code used to identify the origin of the animal (immigrated, reintroduced, wild born).
* Table **lu_problematic**: Look up table for problematic_code field: it specifies the meaning of the code used to identify the problematic behaviour of the animal. It can change over time.
* Table **lu_sample_impact**: Look up table for the impact of the sample on the animal (invasive or non invasive).
* Table **lu_sample_method**: Look up table for the method of the sample (e.g. Opportunistic, Systematic, Tree).
* Table **lu_sample_type**: Look up table for type of sample taken (used in the table identifications).
* Table **lu_scalp**: Look up table for type of sample taken (used in the table identifications).
* Table **lu_sex**: Look up table for the sex of the animal.

## <a name="clienttools"></a> Client tools
While data are (safely and consistently) stored in the database, users can use client applications to access the data. Data are not duplicated but a single, controlled version is available to all users. According to the task that must be performed (including a plain export to .csv for they who are allergic to databases), users can use one of the many clients available. For tabual data visualization, query, download, the recommended tools are PgAdmin (3 or 4) or PhpPgAdmin. For visualization and processing of spatial data, QGIS is the best client. For statistical analysis, R is the most used tool. LibreOffice offers a complete suite of products to interact with the database.  
Data can also be imported in the specific software, and processed independently from the version in the database, although this means that a new "version" of the data is created with consequent problem or synchronization (if the dataset is updated).

If you need technical support, please contact the project or the database manager.

All these tools are open source, standard compliant, efective and well documented. It is also possible to access the data using proprietary software like ArcGIS, Office, SAS. In this case, interested users can look for tutorial on how connect these tools to the database.

### phppgAdmin
The data in the database can be accessed, explored, queried and downloaded (as csv) using a web based tool available at [http://eurodeer2.fmach.it/phppgadmin/](http://eurodeer2.fmach.it/phppgadmin/). There is no need to install any software on the local computer. The interface is very intuitive and similar to PgAdmin, with a structure pretty similar to a file system exporer. This is the recommended interface for basic users. It is not possible to visualize spatial data. For an example, see the figure below.

![](https://github.com/feurbano/bear_data/blob/master/phppgadmin.png)

### PgAdmin
[PgAdmin](https://www.pgadmin.org/) is the most popular and feature rich Open Source administration and development platform for PostgreSQL. There are two versions: 3 and 4 (the former is more intuitive, the latter more modern and powerful). This is the best tool to manage the data and explore both structure and content, including the formulation of advanced queries and the creation of new database objects.
Here below and example of the window with the connection parameters.

![](https://github.com/feurbano/bear_data/blob/master/pgadmin.png)

### QGIS
[QGIS](www.qgis.org) is a desktop GIS that is very well integrated with PostgreSQL and PostGIS and offers a large set of tools to deal with spatial data in the database. Connecting to the database is pretty simple and weel documented, for example [here](http://docs.qgis.org/2.18/en/docs/training_manual/databases/index.html). Data can be accessed in three steps: create a connection to the db. Open the connection. Get the data. Here below is reported as an example a screenshot of the connection window in QGIS.

![](https://github.com/feurbano/bear_data/blob/master/qgis1.png)

![](https://github.com/feurbano/bear_data/blob/master/qgis2.png)

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
