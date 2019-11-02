---
layout: post
title:      "Craniometric Analysis of Skulls from Caesarea Maritima"
date:       2019-11-02 20:32:45 +0000
permalink:  craniometric_analysis_of_skulls_from_caesarea_maritima
---

## Introduction

Craniometry is the analysis and classification of skulls using anatomical measurements. Forensic anthropologists have tried to use statistical analysis to classify skulls of unknown origin, primarily in contexts where remains may belong to one of several ethnocultural groups. Classification of this sort requires a vast amount of data that can be used to train a model, and while there are datasets with many samples from various ethnogroups, certain groups are underrepresented, either do to religious or cultural taboos or simply a lack of viable specimens. 

![](https://imgur.com/Fd46kyi)

One of the most notable datasets is that W. W. Howells, which is maintained by Dr. Benjammin Auerbach of the University of Tennessee-Knoxville. This dataset contains over two thousand individual skulls from thirty ethnogroups around the world. While this may sound like a large sample size, keep in mind that there are currently over seven and a half billion people living on Earth and how different individuals living in the same city block can look compared to each other.

The skulls attempted to be classified are from the city of Caesarea Maritima, and according to archaeological reports were casualties of the Second Crusades. The burials from which the individuals were excavated were classified as either Muslim or Christian. This was primarily due to the position in which the remains were found.

The application of craniometry to these skulls is two-fold: to further bolster the evidence for identification of individuals in archaeological sites and to demonstrate the benefits and limitations of applying machine learning techniques for this purpose.

## Process
First, the dataset that will be used as our training set must be cleaned (null values either dropped or filled with the mean average, categorical columns dropped or given dummy values, and float values converted to integers). Then the value of K must be found for use in our KNN analysis. This will be compared to both decision tree learning and random forest learning to find the most accurate machine learning model. 

The decision tree's accuracy was only 41%, while KNN's F1-score was 68% and the accuracy for the Random Forest model was 61%. While none of these are excellent, one must keep in mind how little variation there is amongst different ethnogroups and how much variation there is within a single group. 

The next step is to add the individuals from the Caesarea Martima dataset. Unfortunately, these reconstructed skulls were not measured as thoroughly so we have to drop some of the variables from our original dataset, including some of the most important features. This ends up leaving our random forest model with an accuracy of a dismal 2%.

After adjusting, the depth of the decision trees and random forest models from 5 to 30, all models classified with an accuracy of 100%, with the exception of the combined randome forest model, which only increased from 2% to 3%., which speaks to likely overfitting.

In an attempt to improve accuracy, the next step was to reduce the ethnogroups to European and Northern African populations, the likely victims of the Crusades.

## Results
![](https://imgur.com/TJEibrd)
As expected, the reduction of variables left us with extremely poor accuracy when run through both our decision tree and our random forest models. This is in part due to the overlap in similarity between groups of peoples. With the exception of highly isolated groups, there is far too much heterogeneity due to human migration in the individual groups for the computer to accurately classify new samples. While this is a nuisance for our intended purpose, it shows two beneficial points: first, that there is very little difference between human beings around the world, and second, our ability to extrapolate that there is still enough biological variation within our species to prevent extreme bottle-necking (when the gene pool grows too similar to produce a healthy genetic line).

## Further Research
* Comparison against known Frankish and Abbuyyid skulls
* Addition of larger samples of each of the known groups to fine-tune training data
* Further measures taken (where possible) on test data
* Combine craniometric data with genomic data to reinforce phenotypic statistics

These suggestions and more will not only serve to help classify archaeological remains, but also help to identify victims of mass tragedies (natural disasters, terrorist attacks, etc.). ForDisc, created by the University of Tennesee--Knoxville, was considered inaccurate due to the limited number of ethnic groups represented. However, with the advancements of machine learning and programming capabilities as well as the ubiquity of DNA testing, another more refined program could be created.
## Sources
### Howells Dataset
* Howells Database Website (Dr. Auerbach at UT-Knoxville): https://web.utk.edu/~auerbach/HOWL.htm
* Howells WW. 1973. Cranial Variation in Man. A Study by Multivariate Analysis of Patterns of Differences Among Recent Human Populations. Papers of the Peabody Museum of Archeology and Ethnology, vol. 67, pp. 259. Cambridge, Mass.: Peabody Museum.
* Howells WW. 1989. Skull Shapes and the Map. Craniometric Analyses in the Dispersion of Modern Homo. Papers of the Peabody Museum of Archaeology and Ethnology, vol. 79, pp. 189. Cambridge, Mass.: Peabody Museum.
* Howells WW. 1995. Who’s Who in Skulls. Ethnic Identification of Crania from Measurements. Papers of the Peabody Museum of Archaeology and Ethnology, vol. 82, pp. 108. Cambridge, Mass.: Peabody Museum.

### Caesarea Maritima Craniometric Dataset
* McChesney S. 2012. Undergraduate Senior Research Project. Unpublished.
