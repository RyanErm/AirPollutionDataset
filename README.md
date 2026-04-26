# DS 4320 Project 2 - AirPollutionDataset
Ryan Ermovick - jph4dg

**DOI:** NEED TO ADD IN

NEED TO CHANGE THE LINK
DOI Linked [here](https://doi.org/10.5281/zenodo.19362900)

**Executive Summary** 


This repository contains the detailed creation of the AirPollutionDataset, including the origin of the data, how it was transformed, and how it can be used. The README contains information on the problem being solved, domain exposition, data creation, and metadata. The repository also contains a RandomForestClassifier that is used to predict the air quality ranking of new locations. The press release details how this model can be used and why it is important. When utilizing the AirPollutionDataset, please refer to the License in this repository. 

#### Important Links
| File/Resource | Link |
| :--- | :--- |
| Original Data File | [Data Set](https://myuva-my.sharepoint.com/:x:/g/personal/jph4dg_virginia_edu/IQD5Sikj0_GYSqFhC_V6NKiIAY4uVWGm6lxdAOjQbBX53RE?e=z9Vw2g) |
| Press Release | [Press_release](documents/press_release.md) |
| Data Creation File | [Data_preparation](scripts/data_preparation.ipynb) |
| Data Creation Markdown | [Data_preparation.md](scripts/data_preparation.md) |
| Pipeline & Model File | [Pipeline](scripts/pipeline.ipynb) |
| Pipeline & Model Markdown | [Pipeline.md](scripts/pipeline.md) |
| Ryan Ermovick MIT License | [License](LICENSE) | 

#### Quick Folder Guide
| Folder | Description |
| :--- | :--- |
| documents | Contains important documents, such as the Press Release |
| images | Contains images created in this project that can be used elsewhere |
| scripts | Contains the scripts used in this project |


## Problem Definition
**Initial Problem:** Predicting Air Quality

**Refined Specific Problem:** How can current environmental data be used to predict the air quality of a certain location (such as city) so that work can be done to fix this issue?

### Reasoning

**Rationale for Refinement:** I refined the original problem to be centered around predicting the air quality of a specific location given the environmental data from that area. I choose this refinement because it is extremely important to be able to predict the air quality of a certain location so that citizens can be advised on any risks/health hazards for that day (and how to combat them, such as with masks). Also, predicting the air quality has to be done in a small enough location (such as a city) because too large of an area would have too much variation in air quality (e.g. rural vs. urban). Finally, specifying current environmental data is important because then we can aim to predict air quality in later dates, and possibly start to prevent worsening conditions.

**Motivation for Project:** My motivation for this project comes from my other degree, Chemistry! I am curious how the two fields can intersect in unique ways and what insights I can bring. Air quality is a great pick to combine both DS and Chem as I will be analyzing data on chemicals in the environment and how they effect/predict the air quality. Additionally, I am interested in this project because I have seen many videos and reports online of bad air quality throughout the world (such as heavy smog), so I am curious to learn more about the subject.

**How Bad is the Air You're Breathing?** Read the full Press Release [Here](documents/press_release.md)!

## Domain Exposition
#### Terminology Table
| Term/KPI | Definition | Term or KPI |
| :--- | :--- | :--- |  
| Air Quality | How clean the air is of pollutants/safe for humans | Term/KPI |
| Pollutant | A chemical or substance that was not originally present and is not meant to be there | Term |
| Environmental Data | Information on the natural/earthen world of a certain area | Term |
| Health Hazard | A danger to the wellbeing of a human | Term |
| Protection | Means to guard against breathing in pollution, such as a mask | Term |
| Sustainability | Attempting to minimize use of resources and impact on environment, both for respect of environment and use for future generations | Term |
#### Domain Explanation
This project lives in multiple domains. It exists in the domain of sustainability and environmental care, since many air pollutants are the result of human action. There is a need for us (humans) to be more cognizant and careful with how our actions affect the world that we live in and how we can keep it livable for future generations. This project also lives in the health domain. Worsening air quality poses a great risk for all people, but especially those in urban areas, younger children, and the elderly. Learning what causes bad air quality and predicting when it will happen can help mitigate these health hazards and effects.
#### Check out some Background Reading on this field below!
| Title | Description | Link |
| :--- | :--- | :--- |  
| Air Pollution: Current and Future Challenges | This article describes the current state of air pollution with details on specific domains (such as toxic air pollutants and climate change) | https://myuva-my.sharepoint.com/:b:/g/personal/jph4dg_virginia_edu/IQBPej3EaLq3RLRISL19WS05AQIVSid_AO3tfO21fvuCksQ?e=pt7KtF |
| Chemical Releases | This article describes how chemicals/pollutants can be released into the air. | https://myuva-my.sharepoint.com/:b:/g/personal/jph4dg_virginia_edu/IQDXs3hJ-P4yT5ZCd7Rw_Bm0AeGXtN4mTM1k-Bruyr_ZvX0?e=TzSl8I |
| Air Pollution and Your Health | This article describes some of the common pollutants, what health concerns they might cause, and who is at most risk. | https://myuva-my.sharepoint.com/:b:/g/personal/jph4dg_virginia_edu/IQAE9a2bgV-VR70sAlcrherMAUuSCp9AEBR6CiAW_OT2F-k?e=quZVAi |
| Air Quality Index | This article describes what an Air Quality Index is, a common metric for air pollution.| https://myuva-my.sharepoint.com/:b:/g/personal/jph4dg_virginia_edu/IQAEqt972dA5T4yCk-C1TbpOASBrAahKibIiApQ1FmJvtSg?e=PizUKT |
| 5 ways Cites are Cleaning the Air we Breathe | This article describes the tactics and increased monitoring that cities are implementing to combat air pollution. This is useful as air quality prediction models can point towards the cities that need new, greener infrastructure. | https://myuva-my.sharepoint.com/:b:/g/personal/jph4dg_virginia_edu/IQBGaMJVswUiS7Ka2GuHSI38AeM7VxosM0a-8KZ-0Rttsc8?e=9dyyTZ |

Click [here](https://myuva-my.sharepoint.com/:f:/g/personal/jph4dg_virginia_edu/IgDT33upNmyYRq8Z0HyWDj90AYe2nlcObaC5WditA5sZM7o?e=N4wwye) to access a folder of all the readings!

## Data Creation
#### Provenance
The data used in this project is about air pollution and air quality ratings. The main dataset was found from Kaggle, and upon further inspection, this dataset originates from multiple sources, including the World Health Organization and the World Bank. This data was web-scraped to form the Kaggle dataset. For this project, the Kaggle dataset was converted to the document model using MongoDB.

#### Bias and Mitigation
In this case, bias was not introduced in my creation of the dataset as I am just pulling dataset from online. However, there are multiple spots where bias could have been introduced in its earlier stages. This dataset is a combination of data from the World Health Organization and the World Bank. Bias could have come from this data being repurposed for a use different than its original intent. Bias could have also been introduced when this data was collected in the first place. For example, perhaps there was a political or external pressure that influenced the data collector to misrepresent data or only collect atmospheric data at certain times (so as to downplay or exaggerate the spread of air pollution). Another source of bias could come from the fact that this dataset is not very large, so any predictions made off of it could be skewed. Additionally, recording instrument inaccuracies could lead to biased data. These are all possible sources of bias in the data's provenance that could influence its current state. 

First, some of the possible biases listed above likely did not happen. The WHO has strict data principles that ensure that their data is accurate and accesible to all. As for other possible sources of bias, such as a small dataset and machine inaccuracy, a solution to this could be bootstrapping the current dataset so as to simulate multiple/larger datasets. This could help users make more robust predictions. Also to address the possible bias of misused data, this data is all about air pollution and air quality. This is a theme through the dataset's lifetime and its different stages, so the risk of the data being misused is relatively low as it is being used in this project for Air quality prediction.

#### Key Decisions
In this project there were not many massive judgement calls. Perhaps the biggest judgement call is determining how this dataset should be used. A common metric for air quality is the air quality index. This is a calculated value, and hence is not useful for machine learning predictions. While this metric is not already included in the dataset, it could have been calculated from the current data. 

Instead, a value that could be useful in machine learning prediction is what class of air quality each region is. Since this is a classification column based on the data, it can be predicted accurately. This was an important decision because I had to choose how I wanted to make my dataset so that it can make accurate predictions and not be suseptible to data leakage. I decided that this classification model was sufficient and would be especially good for user interpretation. 

#### Data Files
| Code File | Description | Link |
| :--- | :--- | :--- |
| Data Creation | File that shows the data creation | [Data Preparation](scripts/data_preparation.ipynb) |

## Metadata
#### Implicit Schema
Below is the implicit schema for what a document in the Population collection *could* look like, however, if any of the keys are missing, then the document is still valid


{
  
  _id: STRING,

  Temperature: FLOAT,

  Humidity: FLOAT,

  PM2.5: FLOAT,

  PM10: FLOAT,

  NO2: FLOAT,

  SO2: FLOAT,

  CO: FLOAT,

  Proximity_to_Industrial_Areas: FLOAT,

  Population_Density: INT,

  Air Quality: STRING

}

#### Summary of Data Tables
| Data Table | Description | Location |
| :--- | :--- | :--- |  
| Pollution | Data on pollutants in the air and the air quality rating | In the MongoDB project "airquality" in the collection "pollution". |

#### Data Dictionary for the Pollution Data table

| Name | Datatype | Description | Example | Uncertainty |
| :--- | :--- | :--- | :--- | :--- |  
| _id | STRING | The unique Id for each document | 69e4e1322c1f48546c50b31f | N/A |
| Temperature | FLOAT | Average temperature of the region in Celsius | 29.8 | 0.1 |
| Humidity | FLOAT | Relative Humidity (%) | 59.1 | 0.1 |
| PM2.5 | FLOAT | Fine particulate matter levels | 5.2 | 0.1 |
| PM10 | FLOAT | Coarse particulate matter levels | 17.9 | 0.1 |
| NO2 | FLOAT | Nitrogen dioxide level | 18.9 | 0.1 |
| SO2 | FLOAT | Sulfur Dioxide level | 9.2 | 0.1 |
| CO | FLOAT | Carbon Monoxide level | 1.72 | 0.1 |
| Proximity_to_Industrial_Areas | FLOAT | Distance to nearest industrial zone | 6.3 | 0.1 |
| Population_Density | INT | Number of people per square kilometer | 311 | 1 |
| Air Quality | STRING | Air Quality Rating | Hazardous | N/A |

#### Quantification of Uncertainty
There are many measured numerical values in this dataset. These columns include: Temperature, Humidity, PM2.5, PM10, NO2, SO2, CO, Proximity_to_Industrial_Areas, and Population_Density. Each of these values contain some uncertainty as they are measured values. 

The numerical uncertainty for all of the float columns is 0.01 due to recording instrument error or human error in reading the values. 

Population_density is a calculated value and has an uncertainty of 1 due to possible mis-measurements in human counting or square kilometer range. 

