# EMODnet Biology Phytoplankton of the greater Baltic Sea

## Introduction
The large databases of EMODNET Biology only store confirmed presences of species. However, when mapping species distribution, it is also important where the species did not occur: there is at least as much information in absences as in presences. Inferring absences from presence-only databases is difficult and always involves some guesswork. In this product we have used as much meta-information as possible to guide us in inferring absences. There is important meta-information at two different levels: the level of the data set, and the level of the species. Datasets can contain implicit information on absences when they have uniformly searched for the same species over a number of sample locations. Normally, if the species would have been present there, it would have been recorded. Other datasets, however, are not informative at all about absences. Typical examples are museum collections. The fact that a specimen is found at a particular place confirms that it lived there, but does not give information on any other species being present or absent in the same spot. It is also helpful to plot the total number of species versus the total number of samples. Incomplete datasets have far less species than expected for their size, compared to 'complete' datasets. At the species level, taxonomic databases such as WoRMS give information on the functional group the species belongs to. This information is present for many species, but it is incomplete. However, we can use it in a step to select the most useful datasets. We could use it to restrict the species to be used in mapping, but since the data are incomplete that would cause some loss of interesting information.

## General procedure in preparing the data product
The retrieval of data goes in three steps. 

### Step 1. Use functional group information to harvest potentially interesting datasets. 
A query is performed for data on species known to be phytoplankton (in WoRMS) and to occur in a number of different sea regions. This yields a large dataset with phytoplankton data, but many of these data come from datasets that are not useful for our purpose.

From the datasets resulting from this action we harvest all potentially interesting datasets, that contain at least one phytoplankton species in the region of interest. We subsequently use the imis database with meta-information on the datasets, to list the meta-data of all these datasets. This results in the file ./data/derived_data/allDatasets.csv.

In this file we perform the (manual) selection of datasets to be used. We also qualify the datasets as either 'complete' or 'incomplete'. The result of this manual procedure is the file ./data/derived_data/allDatasets_selection.csv.

### Step 2. Download by dataset.
In this step, we download the part of all these useful datasets that occur in the region of interest. For practical reasons this region is subdivided in many subregions - in that way the downloaded files are not too big and there is less risk of interruptions of the process. After download, all these files will be recombined into one big datafile. 

The regions of interest were selected from the intersect of the Exclusive Economic Zones and IHO sea areas (Flanders Marine Institute, 2020) created by [Marine Regions](https://www.marineregions.org). The numbers specified in this map are the [Marine Regiones MRGIDs](https://marineregions.org/mrgid.php) of each gazetteer feature.

![regions of interest](https://github.com/EMODnet/EMODnet-Biology-Phytoplankton-Greater-BalticSea/blob/master/regionsOfInterest.png)

### Step 3. Combine all downloaded datasets into one big dataset
In this step, we read in all the files written during the previous step, and combine the data into one big dataset to be used for further analysis and production of the maps. It is kept separate from the previous step because the downloading is very time-consuming and it can better be completed before this step takes place, including checks on errors, timeouts etc.

### Analysis of the species represented in the dataset
The selection of data in the first step makes use of the traits stored in WoRMS, the World Register of Marine Species. For many species in this database, the functional group to which the species belongs is recorded. However, this is not yet complete. We can help the development of these traits databases from the compilation of data performed here. Since we selected phytoplankton data sets, we can assume that most species in our database will be phytoplankton, although it appears this is not absolutely the case everywhere. Here we try to use as much information as possible, either from the traits database or from the taxonomic position of the taxa, to derive what functional group they belong to. That is used to narrow down the list of taxa to the phytoplankton species we are targeting, but also to report back to WoRMS with suggestions to improve the traits database.
 
Results have been written to a file after performing this step once.

### Production of rasters with presence/absence information
For the production of maps, we define 'sampling events' as the ensembles of records that share time and place. We consider such events as one sample. For the incomplete datasets, we inventory what species they have targeted. Finally, for every species we determine whether or not it was present in all sampling events of all relevant datasets. This presence/absence information is written to the output file, together with the spatial location and the sampling date. The intention is to use this information to produce interpolated maps covering also the non-sampled space. As a first step in visualisation, we rasterize this information and show it in a map per species. In the following code block, the rasters are calculated and stored in one file per species (for all species that were found more than 200 times - this could easily be extended to all species).

### Production of maps of the rasters
We use the EMODnet mapping package [EMODnetBiologyMaps](https://github.com/EMODnet/EMODnetBiologyMaps) to produce maps of the rasters.

### Notes
The phytoplankton data have different time series from different data sources/regions and hence the output data may differ in the total number of analyzed  samples for each dataset. A statistically weighted method of the analysis, taking into account the variation in time series would likely yield a different result. 

Variation in the phytoplankton communities are strongly connected with season, with e.g. diatoms and dinoflagellates found in higher abundances in spring compared to summer and cyanobacteria found in higher abundances in summer compared to spring. A statistically weighted method of the analysis, taking into account seasonality would likely yield a different result. Some datasets are also limited to certain seasons such as spring or summer but lack data from autumn and winter which could further lead to biases in the analyses of whole time series including whole seasons in the data.

Phytoplankton species often change names when taxonomist gain a better understanding of the placement in a taxonomic backbone. Therefore, some species such as Skeletonema marinoi may have different names in differnt datasets. The connection with a single aphaid but different names (synonyms) may help to clear the bias in name changes but as in the example of Skeletonema marinoi, this species have had a old name linked to it at least in data derived from the Kattegat and Skagerrak seas whereas in the Baltic Sea this species have always had the same name.  har haft sitt gamla namn under perioder  i vårt dataset.

## Directory structure
```
Phytoplankton_greater_Baltic_Sea/
├── analysis
├── data/
│   ├── derived_data/
│   └── raw_data/
│         ├── byDataset/
│         └── byTrait/
├── docs/
└── product/
    ├── maps/ 
    ├── species_plots/ 
    └── species_rasters/

```
* **analysis** - Markdown notebook with the general workflow used in this product, All R code used during the analysis is incorporated into the Markdown notebook. For consistency reasons, the code is not replicated as independent scripts. If needed, code chunks can easily be extracted from the notebook.
* **data** - Raw and derived data. Note that due to the data size, these directories do not contain the actual mass of data of the project. The directories are made to store data when the project is running. There are a few ancillary files in derived_data, needed for running the download and producing maps
* **docs** - Rendered report of the Markdown document
* **product** - Output product files. The subdirectory maps contains general files with the results of the analysis for all species found 200 times or more (note that for the rarer species no output is produced, although this can easily be done using the provided code). There are three files. spe.csv is a very large csv file recording for each species and each sampling event whether the species was present (1), absent (0) or not looked for (NA). This information is also stored in the binary file spe.Rdata that can only be read from R. The names and some attributes of the species in spe.csv are stored in specieslist.csv. For each species, a raster is made with fraction presences, and stored as a geotiff file in the subdirectory species_rasters. The rasters are plotted and stored per species in the subdirectory species_plots.

## Data series
This product is based on the compilation of a large number of data sets. Details of candidate datasets and datasets actually used are in the code and in the ancillary .csv files. The best summary is given in the file ./data/derived_data/allDatasets_selection.csv. It lists dataset ids, titles, abstracts, as well as fields describing whether the data set has been included and whether it is 'complete' in the sense of having sampled the entire phytoplankton community.
The wfs calls can also be found in the code.

## Data product
![example product Dinophysis acuminata](https://github.com/EMODnet/EMODnet-Biology-Phytoplankton-Greater-BalticSea/blob/master/0007_109603_Dinophysis-acuminata.png)

Per species the presence or absence in each of the sampling events is recorded as a Boolean variable. Output is restricted to species that have been found more than 200 times in the entire dataset, but this can be changed in the code. This file is to be used as a basis for the production of interpolation maps, but can also be used as a basis for clustering and descriptive analyses. The file is saved as an R binary file and as a .csv file.

Per species, the presence/absence data are also rasterized in a relatively fine raster. For each raster cell, the proportion of observations with presence of the species is calculated. The map shows these proportions (between 0 and 1). 
Currently, there are maps available for a total of 1003 taxa. These encompass all taxa that have been observed more than 200 times in the total dataset of over 34968 samples. 

Distinction between ‘complete’ and ‘incomplete’ datasets was made based on the description of the datasets in the meta-information, and checked using the relation between sampling effort and number of species found. The latter showed a good overall correspondence for the ‘complete’ datasets, although some datasets focusing on estuarine areas had a relatively modest number of taxa found for a relatively large sampling effort.

## More information:
### References

Flanders Marine Institute (2020). The intersect of the Exclusive Economic Zones and IHO sea areas, version 4. Available online at https://www.marineregions.org/.https://doi.org/10.14284/402

Salvador Fernández-Bejarano, Lennert Schepers (2020). EMODnetBiologyMaps: Creates ggplot maps with the style of EMODnet. R package version 0.0.1.0. Integrated data products created under the European Marine Observation Data Network (EMODnet) Biology project (EASME/EMFF/2017/1.3.1.2/02/SI2.789013), funded by the by the European Union under Regulation (EU) No 508/2014 of the European Parliament and of the Council of 15 May 2014 on the European Maritime and Fisheries Fund, https://github.com/EMODnet/EMODnetBiologyMaps

WoRMS Editorial Board (2021). World Register of Marine Species. Available from https://www.marinespecies.org at VLIZ. Accessed 2021-03-01. doi:10.14284/170 

### Citation and download link
This product should be cited as:

Daniela Figueroa, Markus Lindh, Luuk van der Heijden, Willem Stolte & Lisa Sundqvist (2020). Presence/Absence maps of phytoplankton in the Greater Baltic Sea. Integrated data products created under the European Marine Observation Data Network (EMODnet) Biology project (EASME/EMFF/2017/1.3.1.2/02/SI2.789013), funded by the by the European Union under Regulation (EU) No 508/2014 of the European Parliament and of the Council of 15 May 2014 on the European Maritime and Fisheries Fund

Available to download in:

https://www.emodnet-biology.eu/data-catalog?module=dataset&dasid=6618

### Authors
Daniela Figueroa, Markus Lindh, Luuk van der Heijden, Willem Stolte, Lisa Sundqvist
