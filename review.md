## Package Review

*Please check off boxes as applicable, and elaborate in comments below.  Your review is not limited to these topics, as described in the reviewer guide*

- **Briefly describe any working relationship you have (had) with the package authors.**
- [X] As the reviewer I confirm that there are no [conflicts of interest](https://devguide.ropensci.org/policies.html#coi) for me to review this work (if you are unsure whether you are in conflict, please speak to your editor _before_ starting your review).

#### Documentation

The package includes all the following forms of documentation:

- [X] **A statement of need:** clearly stating problems the software is designed to solve and its target audience in README
- [X] **Installation instructions:** for the development version of package and any non-standard dependencies in README
- [X] **Vignette(s):** demonstrating major functionality that runs successfully locally
- [X] **Function Documentation:** for all exported functions
- [X] **Examples:** (that run successfully locally) for all exported functions
- [X] **Community guidelines:** including contribution guidelines in the README or CONTRIBUTING, and DESCRIPTION with `URL`, `BugReports` and `Maintainer` (which may be autogenerated via `Authors@R`).

#### Functionality

- [X] **Installation:** Installation succeeds as documented.
- [X] **Functionality:** Any functional claims of the software have been confirmed.
- [X] **Performance:** Any performance claims of the software have been confirmed.
- [X] **Automated tests:** Unit tests cover essential functions of the package and a reasonable range of inputs and conditions. All tests pass on the local machine.
- [X] **Packaging guidelines**: The package conforms to the rOpenSci packaging guidelines.

Estimated hours spent reviewing: 4.5

- [X] Should the author(s) deem it appropriate, I agree to be acknowledged as a package reviewer ("rev" role) in the package DESCRIPTION file.

---

### Review Comments

Thanks to the authors of emodnet.wfs for submitting this well presented package. It was a pleasure to explore the available data from this source that I was not previously familiar with. 

emodnet.wfs checked lots of boxes in terms of packaging guide and what I would expect as a new user including:

- a good overview of functions in the README and introductory vignette
- sf package used instead of older spatial packages
- good test coverage

On my local machine, R CMD check returned 0 errors/warnings/notes and goodpractice checks looked good. 

My four main comments are related to: 1) function naming, 2) function documentation, 3) introductory vignette, and 4) returned objects. 


#### Function naming

Given the purpose of the emodenet.wfs package is to provide access to emodnet data through R, it is understandable and expected that the names are inherited from the broader EMODnet documentation. Looking at the [NAMESPACE](https://github.com/EMODnet/emodnet.wfs/blob/d98df3354e7abb1528c3eb6cc5bdd64e6bc8b90b/NAMESPACE#L4-L14), I see:

- emodnet_*
- wfs_*
- layer_*
- attribute(s)_*

and a variety of operators/modifiers including “get_”, “get_all”, “_info”, “client”, “descriptions”, “get_names”, “summarise”, “tbl”. 

My suggestion is to consider renaming functions to provide a more intuitive scheme for users. I understand this requires deprecating old functions, future breaking changes, etc - generally a fair amount of work. But I think it's important that function names are intuitive for users and follow a coherent scheme. My suggestions are to first remove all instances of “emodnet” since that is implied by the package name. Next, focuse on maybe three families of functions: “wfs_”, “layer_”, “attribute_”. Then reduce the length of function names and clarify the operation or expected output by including one or two specific operators. For instance, just by reading the function names, what might a user expect the differences are between “emodnet_wfs” and “emodnet_init_wfs_client”? Or for example, is there a risk that layer_attribute_* and layer_attributes_* provides a frustrating user experience if they miss this difference in pluralization? 

My related suggestion is to consider including a dictionary of this scheme somewhere in the README or introductory vignette. I am not sure exactly where/if this already exists in the broader EMODnet documentation but I think a description of each family of functions wfs/layer/attributes would be useful for new users getting started with the package. 

#### Function documentation

The documentation for this package is not extensively detailed. For example, the description sections for R functions are missing except for 1 function (emodnet_get_layers). I would suggest including a paragraph to describe each exported function so the user sees more than the title and list of parameters when they first open the help pages. I would also suggest including the name of the function in the parameters when an argument for a function is the output from another function, eg. in [R/info.R](https://github.com/EMODnet/emodnet.wfs/blob/d98df3354e7abb1528c3eb6cc5bdd64e6bc8b90b/R/info.R#L81-L82): @param wfs …, from emodnet_init_wfs_client. 

#### Introductory vignette

I appreciated the suggestion from @MargaretSiple-NOAA but I think one step further for renaming the introductory vignette is to highlight that it is the introductory vignette with something like “getting started” or “introduction to” etc. At the moment, the introductory vignette on the pkgdown site is nestled between the other two vignettes making it unclear which vignette a user should start with. 

The word “lot” is used a couple of times but it is unclear what this word refers to with respect to emodnet.wfs functions (wfs/layers/attributes). 

Consider using {knitr} tables or similar functions to tidy up the printing of outputs. 

[This chunk](https://github.com/EMODnet/emodnet.wfs/blob/d98df3354e7abb1528c3eb6cc5bdd64e6bc8b90b/vignettes/emodnet.wfs.Rmd#L173-L186) feels like a pretty non-introductory chunk of code. Not sure if it's required or could be simplified. 

There is an error related to mapview/webshot in the pkgdown/rendered vignette:

```r
# Transform to Polygon geometry type from Multisurface
if (unique(st_geometry_type(habitats_directive_layers)) == "MULTISURFACE") {
  habitats_directive_layers <- habitats_directive_layers %>%
    st_cast(to = "GEOMETRYCOLLECTION") %>%
    st_collection_extract(type = "POLYGON")
}

# Visualize
map <- mapview(habitats_directive_layers, zcol = "habitat_description", burst = TRUE)

map
#> Error in loadNamespace(name): there is no package called 'webshot'
```

#### Returned objects

The argument "reduce_layers" could use more explanation. The documentation suggests reducing layers will be attempted, but what is an example of reasons which reducing might fail? 

Why does a single layer return a list? I think it's intuitive to match the names of multiple layers requested to a named list but wouldn't a user specifying a single layer expect an sf object to be directly returned?

```r
> result <- emodnet_get_layers(
+     service = "biology",
+     layers = "mediseh_zostera_m_pnt"
+ )
✔ WFS client created successfully
ℹ Service: "https://geo.vliz.be/geoserver/Emodnetbio/wfs"
ℹ Version: "2.0.0"
> class(result)
[1] "list"
```


#### Minor comments

Given the overlap in authorship, if the authors are aware of how EMODnetWCS differs from emodnet.wfs or what the roadmap looks like for EMODnetWCS, it might be useful to include it in the README under other web services. Are [these](https://github.com/EMODnet/EMODnet-Biology-Guidance?tab=readme-ov-file#r-client-for-emodnet-biology-webservices) also additional tools to reference there? Could you add more precision to how "these three ways to access EMODnet complement each other"?

Codecov badge present in README twice, pointing to the root and tree/main, giving different code coverage percentages. 


The authors could use `cffr::cff_write()` to generate a citation file. 

Are there any rate limits/rules for the API? If so, could these be mentioned in the README and vignettes?

The skimr package is listed in the Suggests only to be used once in the ECQL filtering vignette. Is it a necessary dependency? I find reading through the vignette that the single skimr line returns an unexpected/different/confusing output to the previous outputs. 


Some broken links detected with {urlchecker}.

```r
> urlchecker::url_check()
! Warning: vignettes/emodnet.wfs.Rmd:199:100 Moved
Blondel, Emmanuel. (2020, May 27). ows4R: R Interface to OGC Web-Services (Version 0.1-5). Zenodo. http://doi.org/10.5281/zenodo.3860330
                                                                                                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
                                                                                                   https://doi.org/10.5281/zenodo.3860330
✖ Error: README.md:67:70 400: Bad Request
| geology_sea_floor_bedrock                                       | <https://drive.emodnet-geology.eu/geoserver/bgr/wfs>                          |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:69:70 400: Bad Request
| geology_submerged_landscapes                                    | <https://drive.emodnet-geology.eu/geoserver/bgs/wfs>                          |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:66:70 400: Bad Request
| geology_marine_minerals                                         | <https://drive.emodnet-geology.eu/geoserver/gsi/wfs>                          |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:68:70 400: Bad Request
| geology_seabed_substrate_maps                                   | <https://drive.emodnet-geology.eu/geoserver/gtk/wfs>                          |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:65:70 400: Bad Request
| geology_events_and_probabilities                                | <https://drive.emodnet-geology.eu/geoserver/ispra/wfs>                        |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:64:70 400: Bad Request
| geology_coastal_behavior                                        | <https://drive.emodnet-geology.eu/geoserver/tno/wfs>                          |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
! Warning: README.md:26:11 Moved
Services](https://emodnet.ec.europa.eu/en/data). [Web Feature services
          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
          https://emodnet.ec.europa.eu/en/data-0
✖ Error: README.md:59:70 400: Bad Request
| biology_occurrence_data                                         | <https://geo.vliz.be/geoserver/Dataportal/wfs>                                |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:58:70 400: Bad Request
| biology                                                         | <https://geo.vliz.be/geoserver/Emodnetbio/wfs>                                |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:90:16 400: Bad Request
#> ℹ Service: "https://geo.vliz.be/geoserver/Emodnetbio/wfs"
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:95:17 400: Bad Request
#> ....|-- url: https://geo.vliz.be/geoserver/Emodnetbio/wfs
                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:107:16 400: Bad Request
#> ℹ Service: "https://geo.vliz.be/geoserver/Emodnetbio/wfs"
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:271:16 400: Bad Request
#> ℹ Service: "https://geo.vliz.be/geoserver/Emodnetbio/wfs"
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:339:16 400: Bad Request
#> ℹ Service: "https://geo.vliz.be/geoserver/Emodnetbio/wfs"
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:62:70 400: Bad Request
| chemistry_contaminants                                          | <https://geoserver.hcmr.gr/geoserver/EMODNET_SHARED/wfs>                      |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:57:70 400: Bad Request
| bathymetry                                                      | <https://ows.emodnet-bathymetry.eu/wfs>                                       |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:70:70 400: Bad Request
| human_activities                                                | <https://ows.emodnet-humanactivities.eu/wfs>                                  |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:73:70 400: Bad Request
| seabed_habitats_individual_habitat_map_and_model_datasets       | <https://ows.emodnet-seabedhabitats.eu/geoserver/emodnet_open_maplibrary/wfs> |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: README.md:72:70 400: Bad Request
| seabed_habitats_general_datasets_and_products                   | <https://ows.emodnet-seabedhabitats.eu/geoserver/emodnet_open/wfs>            |
                                                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
✖ Error: vignettes/emodnet.wfs.Rmd:29:338 404: Not Found
For this tutorial we will make use of the `sf`,  `dplyr`  and `mapview` packages. The simple features `sf` package is a well known standard for dealing with geospatial vector data. The package `dplyr` is a strong library for data manipulation. This package also loads `magrittr`'s pipe operator `%>%` (you could also use the [base pipe](https://r4ds.hadley.nz/workflow-pipes.html)), which allows to write pipelines in R. To visualize geometries,  `mapview` will create quick interactive maps.
                                                                                                                                                                                                                                                                                                                                                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
! Warning: vignettes/emodnet.wfs.Rmd:40:97 Moved
With the emodnet.wfs package, we can explore and combine the data served by the [EMODnet lots ](https://www.emodnet.eu/en/portals) through [OGC Web Feature Services](https://en.wikipedia.org/wiki/Web_Feature_Service) or WFS.
                                                                                                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
                                                                                                https://emodnet.ec.europa.eu/en/emodnet-themes
```
