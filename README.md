# ECG Triage Research

Contributors: Toby Klauder, Jonah Gent, Harry Zhu, Olivia Barner
Faculty Advisors: Jillian Darrow and Dr. Brian R. Snider, PhD

## About

Over the course of this project, we attempted to make classifications on the different arrhythmias represented in the dataset using 3 different types of classifiers: Random Forest, XGBoost, and (minimally) CNN.  
 
Random Forest has the pros of being relatively robust, handles non-linear data without the need for transformation, and provides good indicators of the importance of different features. These classifiers are less likely to overfit because these types of classifiers leverage multiple different decision trees each trained on different pieces of the dataset. That being said, depending on how many decision trees the model makes use of for a particular dataset - Random Forest models consume a lot of memory and processing power, and can take additional time to make predictions.  

XGBoost was trimmed relatively early on in our machine learning experiments. Although on average it is more performant than other models, it requires more time and effort tuning the model to ensure optimal performance (tweaking params like the depth of the trees, the learning rate, and the number of trees). It also is less transparent, making it more difficult for the programmer to understand why it makes certain classification decisions.  

CNN was experimented with minimally, given our relatively limited academic exposure to them. That being said, they’re generally relatively performant with time-series data as they process data from multiple layers of filters.  
 
Across all of our runs, we found that on average, Random Forest classifiers performed by far the best (and hence, all numbers reported in our final presentation(s) were output by our Random Forest classifier). In the codebase, we have XGBoost and CNN code alongside the Random Forest code. 

## Stats Tool

Inside of `/stats-cli`, there is a `README.md` explaining the final deliverable.

## Making New Experiments

### In `load_model.py `:

Loads an existing model from disk (in the /prebuilt directory). 

Example Usage: `python3 load_model.py -m prebuilt/random_forest_model.pkl -d data/data_json -s results`

Parameters: 
-m, --model a prebuilt model, built and stored using the pickle python library 
-d, --data folder containing the JSON files in TZ Medical format - will ingest all .json files in the specified directory 
-s, --store directory to store the resulting JSON files (actual and probabilities)

model.py 

Builds new models according to programmer's specifications. 

Example Usage: `python3 model.py -p data/data_json -a -s prebuilt`

Parameters: 
-p, --path path to the folder containing json data files 
-a, -all build all models (random forest and xgb)
-s, --store path to store the models to 

## Features Engineered

- R-R Intervals
- QRS Duration
- Absolute Means
- Kurtosis
- First Derivative
- Second Derivative
