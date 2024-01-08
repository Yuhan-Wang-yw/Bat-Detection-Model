# Bat-Detection-Model
Biointerphase Team A - Fall 2023 AI Studio Project from Break Through Tech AI program at MIT.

Team members: 
- Anders Freeman[^1] 
- Anusha Bandaru[^2]
- Iris Yang[^3]
- Linda Dominguez[^1]
- Yuhan Wang[^4]

[^1]: Wellesley College
[^2]: Northeastern University
[^3]: Tufts University
[^4]: Smith College

Our AI Studio TA is Leandra Marie Tejedor, and our challenge advisor is Noah Snyder from Biointerphase.

This project aimed to understand the outlook for bat population decline in North America due to WNS, use machine Learning models to predict what features signal population decline, and so to provide insight to guide efficient bioengineered solutions to combat WNS for Biointerphase. 

## Table of Contents
- [Introduction](#introduction)
- [Data](#data)
- [Model & Results](#mr)
- [Conclusion](#conclusion)
- [Potential Next Step](#potential-next-step)
- [Team Documents](#team-documents)

## Introduction
White Nose Syndrome is a fungal disease that infects skin of the muzzle, ears, wings of hibernating bats and has ravaged North American bat populations. Over 3 months, we developed 2 machine learning models, a NLP (natural language processing model) and a random forest, that predict bat population decline and the presence of White Nose Syndrome(WNS) by analyzing time-stamped bat population data in North America.
<br/>Here is an overview of our approach:<br/>
<img width="750" alt="Approach Overview" src="https://github.com/Yuhan-Wang-yw/Bat-Detection-Model/assets/102437257/0c494173-b117-4e6f-97a0-bfed7e183867">


## Data
Our project combines 3 datasets: Skin mycobiomes of Easter North American bats, west_master_table, and white_nose_county_status. The first 2 datasets contain information about bat population, fungi detected[^5], collection date, collection location, host group, CFU, etc. The 3rd dataset contains information about time, location, and WNS presence. We merged 3 datasets by time, location, and species overlap, and then one-hot-encoded the data for model training. Our label is WNS present or not, and we aimed to explore what freatures best predict the presence of WNS.

Exploritory Data Analysis was performed in EDA_west_master_table.ipynb, and further data exploration and data cleaning of the merged dataset is in CURRENT_DATA_CLEANING.ipynb. Unfortunately, we realized that location and time are highly related to our label,so different location and time can be used to directly tell if WNS is present or not despite other features, which allows the model to cheat. Therefore, location and time are eliminated in the final model training.

Final dataset has 800 entries and includes columns as follow:
* Location
* Complete biological classification
* Concentration of fungi
* Time of sample collection
* If WNS had been present in the population at that time
For the final model training, Modeling(1).csv is used for the NLP model, and one_hot_model_data_without_county_and_state.csv is used for the Random Forest model.

[^5]: Specifically, information about fungal classification on differnet levels: Phylum, Class,	Order, Family, Genus, and Species

## Model and Results <a name="mr"></a>
We built 2 models in the project: a NLP(natural language processing) and a Random Forest. We started with the NLP because as the fungi classification on differnet levels are essentially arbitrary words, we wanted the model to find its own pattern. Then we chose to build a seconde model -- a random forest for a more explananble model.

### NLP Model 
- Took all features and put them as a natural text “sentence” and fed that data into a natural
language processing pipeline.
- Created the actual neural network with the keras library, while training it on the features in
the set
- Finally, we plotted the training and validation loss and accuracy.

The feature for the NLP model is a giant string with all features combined as the text input, for example, “2015-01-15 unassigned Hamilton New York 12 Ascomycota Leotiomycetes Thelebolales Pseudeurotiaceae Pseudogymnoascus destructans”.

Here is a visualization of the training of the NLP Model. The model training is good and steady, as the loss for both training dataset and validation dataset decreases after every epoch, and the accuracy increases. <br/>
<img width='550' alt="NLP model training overview" src='https://github.com/Yuhan-Wang-yw/Bat-Detection-Model/assets/102437257/dba77d49-db1c-4cba-8ebb-9289f1aa9255'>

Then here is a table that concludes the accuracy results of different combinations of features:<br/>
|                  Data   Included                             |  Accuracy  |
|:------------------------------------------------------------:| :---------:|
| State, County, Date, Host Group, CFU, Fungal Classification  |    98.26%  |
|                    Date                                      |    95.95%  |
|                    State, County                             |    100.00% |
|            Fungus Classifications                            |    71.01%  |
|            CFU                                               |    83.24%  |
|           Host Group, CFU, Fungus Classification             |    86.13%  |

The last column is our final choice.

We conclude our model's predictions in the following 2 confusion matrix that visualizes the prediction result. We can see that the model is pretty good at predicting the presence of WNS, and tended to predict a not WNS present case to be WNS present. The second wordcloud shows in the TP, TN, FP, FN squares from the confusion matrix, what words the model rely on the most to make its prediction.<br/>
<img width='470' height='230' alt="NLP model training overview" src='https://github.com/Yuhan-Wang-yw/Bat-Detection-Model/assets/102437257/92dcb711-12ab-4d5e-b84e-e93bdd4a20ba'>
<img width='470' height='230' alt="NLP model training overview" src='https://github.com/Yuhan-Wang-yw/Bat-Detection-Model/assets/102437257/d9a98bea-028c-465a-b16d-1fb903f70b9f'>

The main insights of the NLP model is that:
- The presence of WNS fungus in the population is not directly linked with WNS itself
- Species of fungus are associated with populations that have had WNS in the past

### Random Forest Model
- Is more interpretable than a NLP model
- Splits the data into training and testing sets, as well as a random state to ensure
reproducibility
- Has a max_depth of 32 and a maximum number of estimators at 300.
- After the model is fitted and predictions are made, we use the root mean squared error
(RMSE) and the R2 score to get our results. The RMSE measures the difference between a model's predicted values and the actual values, while the R2 score is the accuracy.

Here is a visualization of the Random Forest Model. It shows the importance of features that the random forest model considers when making its prediction.<br/>
<img width="900" alt="Accuracy & Loss of Model 2 training" src="https://github.com/Yuhan-Wang-yw/Bat-Detection-Model/assets/102437257/49f4ddc4-eb9c-4e32-abd2-6824f1b427e8">

Here is a table showing the overall results.<br />
|                  Data   Included                             |  Accuracy  |
|:------------------------------------------------------------:| :---------:|
| Base accuracy                                                |    92.1%  |
| Removing Sample Collection Date                              |    86.7%  |
|                    Removing Resistant                        |    97.3%  |
|            Removing Unnamed: 0                               |    90.4%  |
| Removing both Sample Collection Date and Resistant           |    94.9%  |
| Removing both Sample Collection Date and Unnamed: 0          |    33.6%  |

Some key findings from the Random Forest Model:
- After performing a grid search with cross-validation to tune the hyperparameters, we found that the best-performing combination was a max depth of 30 and n-estimators of 100.
- We also found that the features that were most correlated with the data were the date and how resistant the population was.
- Sample Collection Date, and to a lesser extent, Unnamed: 0, is crucial to the accuracy of the Random Forest Model. Without it, the accuracy drops by a lot.

### 2 Model Comparison
<img width='750' alt="NLP model training overview" src='https://github.com/Yuhan-Wang-yw/Bat-Detection-Model/assets/102437257/cfb46ee3-b7eb-4725-96e2-dd1438e1f676'>

## Conclusion
Combined with visualizations of the prediction results, our project indicates new directions in the research of WNS and also foundational models for future application of machine learning in the study of similar diseases. We helped inform Biointerphase of the most efficient action to combat WNS, and provided a basis for future work on combating WNS, including the availability of public datasets on WNS, 2 preliminary ML models on WNS-related data, and possible research directions.

On top of the technical aspect, we also realized the importance of having a clear outline of our research question. As dataset is really the foundation of a successful machine learning model, we hope to prioritize data exploration, analysis, and experiment in the future. Last but not least, communication, even over communication, is very essetial for a team project that scoped 3 months.

## Potential Next Step
#### NLP model
- Improvements to data cleaning pipeline and time series labeling
- - create time-specific WNS presence label
- - implement different vectorizers
- Analyze the NLP Model to find correlations between Fungus species and WNS presence
- - evaluating if fungus species is correlated with bat species, and if bat species is correlated with WNS
#### Random Forest model
- Feature engineering
- Looking into stacking ensemble models
- Expand our dataset for a more comprehensive model
#### Compare 2 models’ results for more insights

## Team Documents
Some of our team documents are uploaded here for your reference, including:
- Project Scope and Deliverables
- Monthly Progress Summary (September & October)
- Final Presentation Slides
They recorded how our project has been moving on and pivoted at some key points. I personally recommend the final presentation slide as the best high level overview and conclusion of our project, while the other files recorded the challenges and progress of a given moment.
