# fodr

`fodr` is an R package to access various French Open Data portals.

Many of those portals use the OpenDataSoft platform to make their data available and this platform can be accessed with the [OpenDataSoft APIs](https://docs.opendatasoft.com/en/api/catalog_api.html). 

`fodr` wraps this API to make it easier to retrieve data directly in R.

## Installation

The `devtools` package is needed to install `fodr`:

```r
devtools::install_github("tutuchan/fodr")
```

## Portals

#### Available portals

The following portals are currently available with `fodr`:

| Portal                                                                             | URL                                           |  fodr slug  |
|------------------------------------------------------------------------------------|-----------------------------------------------|:-----------:|
| Infogreffe                                                                         | http://datainfogreffe.fr                      |  infogreffe |
| Région Ile de France                                                               | http://data.iledefrance.fr                    | iledefrance |
| Toulouse Métropole                                                                 | https://data.toulouse-metropole.fr            |   toulouse  |
| Paris                                                                              | http://opendata.paris.fr                      |    paris    |
| Issy-les-Moulineaux                                                                | http://data.issy.com                          |     issy    |
| RATP                                                                               | http://data.ratp.fr                           |     ratp    |
| STIF                                                                               | http://opendata.stif.info                     |     stif    |
| STAR                                                                               | https://data.explore.star.fr                  |     star    |
| Ministère de l'Education nationale, de l'enseignement supérieur et de la Recherche | http://data.enseignementsup-recherche.gouv.fr |    enesr    |
| Tourisme Alpes de Haute-Provence                                                   | http://tourisme04.opendatasoft.com            |      04     |
| Tourisme Pas-de-Calais                                                             | http://tourisme62.opendatasoft.com            |      62     |
| Département des Hauts-de-Seine                                                     | https://opendata.hauts-de-seine.fr            |      92     |
| ERDF                                                                               | https://data.erdf.fr                          |     erdf    |
| OpenDataSoft Public                                                                | https://public.opendatasoft.com               |     ods     |
| RTE                                                                                | https://opendata.rte-france.com               |     rte     |

The portals have been identified from the [Open Data Inception](http://opendatainception.io) website. Many of these portals do not actually contain data and a large number of them are available *via* the ArcGIS Open platform. This API will be supported in a future release.

You can find this list in the package with the `list_portals` function.

#### Retrieve datasets on a portal

Use the `fodr_portal` function with the corresponding **fodr slug** to create a `FODRPortal` object:

```r
library(fodr)
portal <- fodr_portal("paris")
portal
```

```
FODRPortal object
--------------------------------------------------------------------
Portal: paris 
Number of datasets: 175 
Themes:
  - Administration
  - Citoyens
  - Commerces
  - Culture
  - Déplacements
  - Environnement
  - Finances
  - Services
  - Urbanisme 
--------------------------------------------------------------------
```

The `search` method allows you to find datasets on this portal (see the function documentation for more information). By default, and contrary to the Open Data Soft API, all elements satisfying the search are returned. 

Let's look at the datasets that contain the word *vote*:

```r
list_datasets <-  portal$search(q = "vote")
list_datasets[[1]]
```

```
FODRDataset object
--------------------------------------------------------------------
Dataset id: budgets-votes-annexes 
Theme: Finances 
Keywords: budget 
Publisher: Mairie de Paris  
--------------------------------------------------------------------
Number of records: 4868 
Number of files: 0 
Modified: 2016-02-18 
Facets: exercice_comptable, budget, section_budgetaire_i_f, sens_depense_recette, type_d_operation_r_o_i_m, type_du_vote, chapitre_budgetaire_cle, chapitre_budgetaire_texte 
Sortables: exercice_comptable 
--------------------------------------------------------------------
```

#### Retrieve datasets by theme

```r
list_culture_datasets <-  portal$search(theme = "Culture")
lapply(list_culture_datasets, function(dataset) dataset$info$metas$theme) %>% 
  unlist() %>% 
  unique()%>% 
  sort()
```

## Datasets

#### Retrieve records on a dataset

```r
dts <- list_datasets[[1]]
dts$get_records()
```

```
Source: local data frame [4,868 x 11]

   chapitre_budgetaire_cle nature_budgetaire_cle sens_depense_recette budget credits_votes_pmt                   chapitre_budgetaire_texte
                     <chr>                 <chr>                <chr>  <chr>             <dbl>                                       <chr>
1                      022                   022             Dépenses M4 TAM             40000                     DEPENSES IMPREVUES (SE)
2                      042                  6811             Dépenses M4 TAM           7490000 DOTATIONS AUX AMORTISSEMENTS ET PROVISIONS.
3                      011                  6026             Dépenses M4 TAM             10000                 CHARGES À CARACTERE GENERAL
4                      011                 61521             Dépenses M4 TAM            120000                 CHARGES À CARACTERE GENERAL
5                      011                   618             Dépenses M4 TAM             70000                 CHARGES À CARACTERE GENERAL
6                      011                  6288             Dépenses M4 TAM             25000                 CHARGES À CARACTERE GENERAL
7                      012                  6414             Dépenses M4 TAM           4790000     CHARGES DE PERSONNEL ET FRAIS ASSIMILES
8                      012                  6478             Dépenses M4 TAM             90000     CHARGES DE PERSONNEL ET FRAIS ASSIMILES
9                       65                   658             Dépenses M4 TAM               500         AUTRES CHARGES DE GESTION COURANTE.
10                      67                  6712             Dépenses M4 TAM             60000                    CHARGES EXCEPTIONNELLES.
..                     ...                   ...                  ...    ...               ...                                         ...
Variables not shown: nature_budgetaire_texte <chr>, type_du_vote <chr>, type_d_operation_r_o_i_m <chr>, section_budgetaire_i_f <chr>, exercice_comptable
```

#### Filter records

```r
dts <- list_datasets[[1]]
records <- dts$get_records(nrows = dts$info$metas$records_count, refine = list(type_du_vote = "Décision modif. 2"))
records$type_du_vote
```

```
 [1] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
 [8] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[15] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[22] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[29] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[36] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[43] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[50] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[57] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[64] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[71] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[78] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[85] "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2" "Décision modif. 2"
[92] "Décision modif. 2"
```

#### Download attachments

Some datasets have attached files in a pdf, docx, xlsx, ... format. These can be retrieved using the `get_attachments` method:

```r
dts <- fodr_dataset("erdf", "coefficients-des-profils")
dts$get_attachments("DictionnaireProfils.xlsx")
```

## GIS data

Some datasets have geographical information on each data point.

For these datasets, two additional columns will be present when fetching records: `lng` and `lat` that correspond to the longitude and latitude of the coordinates of the data point. Additionally, if there are shapes associated to data points (polygons or linestrings for example), they will be stored in the `geo_shape` column as a list of `data.frame`s with the same two columns `lng` and `lat`.

See for example the following dataset:

```r
dts <- fodr_dataset("stif", "gares-routieres-idf")
dfRecords <- dts$get_records(nrows = 10)
dfRecords
```
```
Source: local data frame [10 x 6]

                           gro_nom type_gr gro_ide      lng      lat       geo_shape
                             <chr>   <chr>   <int>    <dbl>    <dbl>          <list>
1             Gare SNCF Argenteuil      GR      80 2.257681 48.94618  <tbl_df [9,2]>
2  Gare SNCF la Ferté sous Jouarre      GR      28 3.124543 48.95055 <tbl_df [12,2]>
3      Gare SNCF Enghien les Bains      GR      47 2.305979 48.97374  <tbl_df [8,2]>
4      Gare RER Survilliers Fosses      GR      57 2.525156 49.09942  <tbl_df [7,2]>
5     Gare RER Neuville Université      GR      61 2.079814 49.01484 <tbl_df [17,2]>
6            Gare RER Sartrouville      GR      98 2.158423 48.93758  <tbl_df [6,2]>
7                  Gare RER Yerres      GR     174 2.483177 48.70694  <tbl_df [9,2]>
8                  Gare RER Brunoy      GR     175 2.505788 48.69948  <tbl_df [5,2]>
9        Gare RER Nogent sur Marne      GR     202 2.472381 48.83457 <tbl_df [18,2]>
10     Gare RER Boissy Saint Léger      GR     210 2.505877 48.75369 <tbl_df [12,2]>
```

You can then use [leaflet](http://rstudio.github.io/leaflet/) to easily plot this data on a map:
```r
library(leaflet)
leaflet(dfRecords) %>% 
  addProviderTiles("CartoDB.Positron") %>% 
  addMarkers(popup = ~gro_nom)
```


## License of the data 

Most of the data is available under the [Open Licence](https://www.etalab.gouv.fr/licence-ouverte-open-licence) ([english PDF version](https://www.etalab.gouv.fr/wp-content/uploads/2014/05/Open_Licence.pdf)) but double check if you are unsure.

## TODO

+ handle portals that require authentification,
+ handle ArcGIS-powered portals,
+ possibly handle navitia.io portals,
+ ?
