# Massachusetts Coastal Resilience

## FinalProject
Repo for my database management class project
## Introduction
Globally, coastal areas including Massachusetts coastal areas are facing the looming threat of sea level rise because of climate change. With Massachusetts having extensive coastline and diverse ecosystems, understanding the vulnerability of these coastal regions to sea level rise is essential. This project aims to analyze and map areas along the coast of Massachusetts that are susceptible to inundation from rising sea levels. By incorporating projections of future sea level rise scenarios, critical infrastructure, residential areas, and ecological habitats at risk can be identified, aiding in informed decision-making and proactive planning efforts.

## Purpose
The primary purpose of this project is to assess the vulnerability of coastal areas in Massachusetts to sea level rise specifically 1 meter sea level rise and its associated impacts. By creating detailed maps that visualize areas at risk of inundation under various sea level rise scenarios, stakeholders can better understand the magnitude of the threat and prioritize adaptation and mitigation measures. Additionally, identifying critical infrastructures, residential areas, and ecological habitats that may be impacted allows for targeted interventions to protect assets, safeguard communities, and preserve important ecosystems in the face of climate change.

## Importance
- Helps to quantify vulnerability of coastal areas as well as identifies specific areas of concern facilitating the development of adaptation strategies that minimize risks and maximize societal and ecological benefits.
- It promotes collaboration in implementing solutions that ensure the long-term sustainability and vitality of coastal communities and ecosystems by raising awareness about the impacts of sea level rise and the need for concerted action among diverse stakeholders.
- The project contributes to building a more resilient and climate-resilient future for the state by tackling the challenges posed by sea level rise head-on.

## Data
The data included in this repository are:

1 Digitized Elevation Model(DEM) (TIF) [(https://coast.noaa.gov/slrdata/)]

2 Aquatic Core (SHP) [(https://www.mass.gov/info-details/massgis-data-biomap-the-future-of-conservation?)]

3 Rare Species Core (SHP)  [(https://www.mass.gov/info-details/massgis-data-biomap-the-future-of-conservation?)]

4 Forest  Core (SHP)  [(https://www.mass.gov/info-details/massgis-data-biomap-the-future-of-conservation?)]

5 Building (SHP)  [(https://www.mass.gov/info-details/massgis-data-building-structures-2-d?)]

6 Cropland (SHP) [(https://www.mass.gov/info-details/massgis-data-2016-land-coverland-use?)]

7 Community Health Centers (SHP) [(https://www.mass.gov/info-details/massgis-data-community-health-centers)]

8 Roads (SHP) [(https://www.mass.gov/info-details/massgis-data-massgis-massdot-roads)]

9 Major Roads (SHP) [(https://www.mass.gov/info-details/massgis-data-massgis-massdot-roads)]

#### Preprocessing in QGIS

I had to preform some basic preprocessing of my data before using the them. I clipped all of my vecotor and raster data to the Massachusetts Coastal zone boundary  to optimize computational resources during our analysis. I also projected all my data to EPSG:26986 - NAD83 / Massachusetts Mainland to avoid no errors as a result of projection from occurring.

![MA Coastal Map](https://github.com/Gracey0201/FinalProject/blob/main/Maps/MA%20Coasatal%20Map.PNG)

![MA Coastal Map2](https://github.com/Gracey0201/FinalProject/blob/main/Maps/MA%20Coastal%20Map2.PNG)


## Methodology

- Create Sealevelrise database and enable  postgis extenstion
- Generate tables both raster and vector needed for datanormalization in the analysis
    - I had many vector tables which can be found below and one raster table (DEM)
      
- I converted all the shapefiles to sql files by using the shp2pgsql function. An example of this code is:

`"C:\Users\default.DESKTOP-GCP9U73\OneDrive - Clark University\Documents\DATABASE MANAGEMENT\FinalProject">"C:\Program Files\PostgreSQL\16\bin\shp2pgsql" -s 4326 -I Data\Buildings.shp building_vector > building.sql`

- Next, I also converted my raster into .sql files using the raster2pgsql function. An example of this code is:

  `"C:\Users\default.DESKTOP-GCP9U73\OneDrive - Clark University\Documents\DATABASE MANAGEMENT\FinalProject">"C:\Program Files\PostgreSQL\16\bin\raster2pgsql" -s 4326 -t 1000x1000 -I -C -M  Data\Clipped_DEM.tif elevation > DEM.sql`

- The newly created vector and raster sql files were added to PgAdmin database by reading the file into the already created database "sealevelrise" An example of this code is:
  
`pgsql -U postgres -d Flooding -f "C:\Users\rutha\OneDrive - Clark University\Documents\SpatialDatabase\FloodingProject\LocalVersion\boreholes.sql"`

- I created empty tables for some of my vector layers, like the Aquatic core table, populating them with columns from the original data relevent to my analysis. Using the code below.

  `CREATE TABLE aquaticcore_clean_vector(
gid int PRIMARY KEY,
shape_area numeric,
shape_len numeric,
geom GEOMETRY
);`

-- populate the new table with columns

`INSERT INTO floodwalls_clean_vector(gid, shape_area, shape_len,  geom)
SELECT gid, shape_area, shape_len,  geom
FROM aquaticcore_vector;`

## Normalization of Tables
Database normalization involves a systematic approach to organizing data within a database to reduce redundancy and improve data integrity.

### Reasons for Normalization

- To prevent redundancy in data.
- To simplify database structure.
- To maintain consistent relationships between tables.
- To facilitate easier database maintenance and updates.

### Checking for Normalization

_All the tables created in this analysis are normalized, that is, they are all in 1NF, 2NF, 3NF and 4NF because of the reasons stated below_

#### First Normal Form (1NF)

- There are no multiple values stored in a single cell of these tables, thereby reducing complexity.

#### Second Normal Form (2NF)

- Since the tables are already in 1NF, and there are no partial dependencies (that is, all non-prime attributes are depending on the entire primary key), therefore, they satisfies Second Normal Form (2NF).

#### Third Normal Form (3NF)

- They also meet the requirements of 3NF as they are already in 1NF and 2NF, and there are no transitive dependencies among non-prime attributes, each non-key attribute in thes tables are  directly dependent on the primary key (gid). Therefore, they meet the requirements of the 3NF.

 #### Fouth Normal Form (4NF)

There seems not to be any multi-valued dependencies present in all the tables. Each attribute seems to depend only on the primary key (gid) and not on combinations of other non-key attributes.
They thsrefore satisfy the requirements for Fourth Normal Form (4NF), given that they are already in 3NF, and there are no multi-valued dependencies present as each attribute seems to depend only on the primary key (gid) and not on combinations of other non-key attributes.


### Tables
### Aquatic Core Table
![Aquatic core table](https://github.com/Gracey0201/FinalProject/blob/main/Tables/Aquatic_core.PNG)

### Coastal Zone Table
![Coaastal Zone Table](https://github.com/Gracey0201/FinalProject/blob/main/Tables/Coastalzone.PNG)

### Coastline Table
![Coastline Table](https://github.com/Gracey0201/FinalProject/blob/main/Tables/Coastline.PNG)

### Cropland Table
![Cropland Table](https://github.com/Gracey0201/FinalProject/blob/main/Tables/Cropland.PNG)

### Forest Core Table
![Forest Core Table](https://github.com/Gracey0201/FinalProject/blob/main/Tables/Forest_core.PNG)

### Major Roads Table
![Major Roads Table](https://github.com/Gracey0201/FinalProject/blob/main/Tables/major_roads.PNG)



