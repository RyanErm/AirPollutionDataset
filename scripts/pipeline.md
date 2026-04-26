# Pipeline

Set up logging information


```python
# set up logging
import logging

logging.basicConfig(
    level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s',
    filename='pipeline.log'
)
logger = logging.getLogger(__name__)
```

If you are running the following, please save your password to MongoDB as an environment variable in powershell under the name "MONGO_PASSWORD". Do NOT commit your password to anywhere public. Alternatively, skip this first step and just set the password variable to your password


```python
#obtain the password from your environment
try:
    import os
    password = os.environ.get("MONGO_PASSWORD")
    logger.info("Got Password!")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```

The following code makes a custom function that will query MONGODB through MONGOSH


```python
try:
    #import packages
    import subprocess
    import os
    import json
    import pandas as pd
    #create  function that will query through MONGOSH
    def mongosh_fetch(query):
        #connect
        uri = f"mongodb+srv://rveermo_db_user:{password}@pollution.q8lipjf.mongodb.net/airquality"
        command = f"mongosh '{uri}' --eval '{query}' --json=relaxed --quiet"
        #run the query 
        result = subprocess.run(
            ['wsl', 'bash', '-c', command],
            capture_output=True,
            text=True
        )
        #obtain result
        return result.stdout
    logger.info("Made the query function")
except:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}") 
```

Now we need to gather the data back from MongoDB


```python
try:
    #fetch the data
    raw = mongosh_fetch("JSON.stringify(db.pollution.find({}, {_id: 0}).toArray())")
    # strip the outer quotes and unescape
    cleaned = raw.strip().strip('"').replace('\\"', '"')
    #turn into df
    data = json.loads(cleaned)
    df = pd.DataFrame(data)
    logger.info(f"Retrieved {len(df)} documents from MongoDB")
    print(df.head())
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```

       Temperature  Humidity  PM2.5  PM10   NO2   SO2    CO  \
    0         28.3      75.6    2.3  12.2  30.8   9.7  1.64   
    1         23.1      74.7   26.7  33.8  24.4  12.6  1.63   
    2         27.1      39.1    6.1   6.3  13.5   5.3  1.15   
    3         20.0      53.3    3.7  12.9  26.1   6.6  1.09   
    4         22.3      80.5    4.5  12.0  17.2   6.3  1.18   
    
       Proximity_to_Industrial_Areas  Population_Density Air Quality  
    0                            6.0                 611    Moderate  
    1                            5.2                 619    Moderate  
    2                           11.1                 551        Good  
    3                           10.2                 538        Good  
    4                           10.4                 232        Good  
    

# Model Work
Random Forest Classification

For this project, a Random Forest Classifier will be used to predict the air quality of a given record/location. A random forest model was chosen because decision trees are ideal at predicting the category of a record. Using many decision trees together, in the context of a random forest, helps to create more robust predictions and cut back on uncertainty. Additionally, these types of models are very easy to comprehend by many people as they are comprehendable and explainable. This was key in the choice of choosing this model as the results will be used by both a technical and non-technical audience. 

Random Forest models also have many hyper-parameters, such as the max depth of individual trees or how many trees are in the model. With many hyper-parameter possibilities, we are able to find the best set for our model using GridSearchCV (which optimizes the hyper-parameters). Finally, a classifier was chosen because the target variable is a categorical classification of air quality (e.g. Good, hazardous)


```python
# Importing packages
try:
    import pandas as pd
    from sklearn.ensemble import RandomForestClassifier
    from sklearn.pipeline import Pipeline
    from sklearn.model_selection import GridSearchCV
    from sklearn.model_selection import train_test_split
    from sklearn.metrics import r2_score, mean_squared_error
    import matplotlib.pyplot as plt
    from sklearn.tree import plot_tree
    logger.info("Importing Packages")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```

Next, the data is split (Train & Test)


```python
try:
    #taking out the target column
    y = df["Air Quality"]
    #dropping it now
    X = df.drop("Air Quality", axis = 1)
    logger.info("Taking out the target variable")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```

Next, the data is split (Train & Test)


```python
try:
    #further split the data to obtain a new training and testing set
    X_train_rf, X_test_rf, y_train_rf, y_test_rf = train_test_split(X, y, test_size=0.2, random_state=67)

    #create a pipeline
    pipeline_classifier = Pipeline([
        ('classifier', RandomForestClassifier(random_state=670))
    ])
    logger.info("Building the random forest model")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```

Then the optimal hyperparameters are found for this model using GridSearchCV


```python
try:
    #create a parameter table
    parameter_grid_classifier = {
    "classifier__n_estimators": [20, 100],        
    "classifier__max_depth": [5, 10, 15],              
    "classifier__min_samples_leaf": [1, 2, 4],         
    "classifier__max_features": ["sqrt", "log2"]          
    }
    #find best parameter
    grid_search_class = GridSearchCV(pipeline_classifier, parameter_grid_classifier, cv=3)
    #fit
    grid_search_class.fit(X_train_rf, y_train_rf.values.ravel())
    print(grid_search_class.best_params_)
    logger.info("Finding the optimal hyperparameters")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")

```

    {'classifier__max_depth': 15, 'classifier__max_features': 'sqrt', 'classifier__min_samples_leaf': 1, 'classifier__n_estimators': 100}
    

The optimal parameters are then used to train the model. 


```python
try:
    #grab all the optimal parameters
    best_estimators = grid_search_class.best_params_["classifier__n_estimators"]
    best_depth = grid_search_class.best_params_["classifier__max_depth"]
    best_leaf = grid_search_class.best_params_["classifier__min_samples_leaf"]
    best_features = grid_search_class.best_params_["classifier__max_features"]

    #create a new pipeline that includes the optimized parameters
    pipeline_classifier_improved = Pipeline([
        ('regressor', RandomForestClassifier(n_estimators=best_estimators, max_depth=best_depth, max_features=best_features, min_samples_leaf=best_leaf, random_state=67))
    ])

    #fit the pipeline again with the optimized parameters
    logger.info("Refitting the model with the ideal hyperparameters")
    pip_improved = pipeline_classifier_improved.fit(X_train_rf, y_train_rf.values.ravel())

    #make predictions
    y_pred = pipeline_classifier_improved.predict(X_test_rf)
    logger.info("Predicting using the ideal hyperparameters")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")

```

The optimized model was then evaluated


```python
try:
    from sklearn.metrics import classification_report, f1_score
    # in grid search
    scoring = "f1_weighted"

    # after fitting, full breakdown per class
    print(classification_report(y_test_rf, y_pred, target_names=["Good", "Moderate", "Poor", "Hazardous"]))

    # single weighted f1 score
    f1_weighted = f1_score(y_test_rf, y_pred, average="weighted")
    print(f"Weighted F1: {f1_weighted:.4f}")
    logger.info("Computing performance metrics")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```

                  precision    recall  f1-score   support
    
            Good       1.00      1.00      1.00       412
        Moderate       0.88      0.88      0.88        92
            Poor       0.95      0.97      0.96       282
       Hazardous       0.91      0.88      0.90       214
    
        accuracy                           0.95      1000
       macro avg       0.93      0.93      0.93      1000
    weighted avg       0.95      0.95      0.95      1000
    
    Weighted F1: 0.9547
    

For this random forest model, F1 was used to determine performance. F1 is a balance of the models precision and recall. A perfect F1 score is 1. The F1 score for each class of air quality is relatively high, indicated a high performing model. 

# Visualization
Below is the code to create the following visualization for this ML model. 


```python
try:
    import matplotlib.pyplot as plt
    import matplotlib.ticker as mticker
    import seaborn as sns
    import numpy as np
    from sklearn.metrics import confusion_matrix

    labels = ["Good", "Moderate", "Poor", "Hazardous"]
    cm = confusion_matrix(y_test_rf, y_pred, labels=labels)
    cm_pct = cm.astype(float) / cm.sum(axis=1)[:, np.newaxis]
    #colors
    fig, ax = plt.subplots(figsize=(8, 7))
    fig.patch.set_facecolor("white")
    ax.set_facecolor("white")

    colors = ["#E1F5EE", "#9FE1CB", "#1D9E75", "#085041"]
    cmap = sns.color_palette(colors, as_cmap=True)
    #creating the actual heatmap
    sns.heatmap(
        cm_pct,
        annot=False,
        cmap=cmap,
        xticklabels=labels,
        yticklabels=labels,
        ax=ax,
        linewidths=1.5,
        linecolor="white",
        cbar=True,
        vmin=0,
        vmax=1
    )

    for i in range(len(labels)):
        for j in range(len(labels)):
            pct = cm_pct[i, j]
            count = cm[i, j]
            text_color = "white" if pct > 0.5 else "#085041"
            ax.text(j + 0.5, i + 0.42,
                    f"{pct:.0%}",
                    ha="center", va="center",
                    fontsize=15, fontweight="500",
                    color=text_color)
            ax.text(j + 0.5, i + 0.62,
                    f"n={count}",
                    ha="center", va="center",
                    fontsize=10,
                    color=text_color if pct > 0.5 else "#0F6E56")

    ax.set_title("Air quality classification — confusion matrix",
                fontsize=14, fontweight="500", color="#1a1a1a", pad=18, loc="left")
    ax.set_xlabel("Predicted label", fontsize=11, color="#555555", labelpad=12)
    ax.set_ylabel("True label", fontsize=11, color="#555555", labelpad=12)
    #spacing and formatting
    ax.tick_params(axis="both", which="both", length=0, labelsize=11, colors="#333333")
    ax.xaxis.tick_top()
    ax.xaxis.set_label_position("top")

    cbar = ax.collections[0].colorbar
    cbar.ax.tick_params(labelsize=9, colors="#555555")
    cbar.ax.yaxis.set_major_formatter(mticker.PercentFormatter(xmax=1, decimals=0))
    cbar.outline.set_visible(False)
    #add text in
    fig.text(0.01, 0.01,
            f"Random Forest classifier   |   n={cm.sum()} test observations   |   4-class prediction",
            fontsize=9, color="#888888")

    for spine in ax.spines.values():
        spine.set_visible(False)
    #save image
    plt.tight_layout()
    plt.savefig("../images/confusion_matrix.png", dpi=300, bbox_inches="tight", facecolor="white")
    plt.show()
    logger.info("Image has been successfully created and saved!")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```


    
![png](pipeline_files/pipeline_24_0.png)
    


For this model, a confusion matrix was chosen to visualize results. A confusion matrix is a very understandable format to see where the model predicted correctly or not. Since this information will be delivered to both technical and non-technical audiences, the visualization should be high-level and explainable to all. A confusion matrix achieves this goal by presenting the matrix in easily-readable numbers and colors. 

 Specifically, each square of the matrix represents a predicted vs. truth scenario for every possible combination (4x4=16). The color Green was used to signify performance because of its connotation with environmental protection and cleanliness. A scale showing the gradient of green represents how each shade relates to performance. In each square, both the percentage and the actual count of that circumstance is presented. This helps users to see the relative prevalence along with the true amount of that circumstance happening. 

# Further Usage

This dataset can also be used to find trends and correlation between air quality and other factors. Below is the code to create a scatter plot of air quality based on population density. 


```python
try:
    #importing packages
    import pandas as pd
    import matplotlib.pyplot as plt
    import numpy as np
    #setting up colors
    color_map = {
        'Good':      '#1D9E75',
        'Moderate':  '#378ADD',
        'Poor':      '#BA7517',
        'Hazardous': '#A32D2D'
    }

    fig, ax = plt.subplots(figsize=(12, 7))
    fig.patch.set_facecolor('white')
    ax.set_facecolor('white')
    #actual plot creation
    for label, group in df.groupby('Air Quality'):
        x = np.random.uniform(0, 1, size=len(group))
        ax.scatter(x, group['Population_Density'],
                color=color_map[label],
                label=label,
                alpha=0.6,
                s=15,
                edgecolors='none')
    #text
    ax.set_title("Population Density by Air Quality Label",
                fontsize=15, fontweight='bold', color='black', pad=15)
    ax.set_ylabel("Population Density (people/km²)", fontsize=12, color='#333333', labelpad=10)
    ax.set_xticks([])
    ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f'{int(x):,}'))
    ax.tick_params(colors='#333333')
    for spine in ax.spines.values():
        spine.set_edgecolor('#cccccc')
    #legend
    legend = ax.legend(title='Air Quality', fontsize=11,
                    facecolor='white', edgecolor='#cccccc',
                    labelcolor='black', title_fontsize=11)
    legend.get_title().set_color('black')

    fig.text(0.5, 0.01,
            f"n={len(df):,} observations   |   "
            f"Good: {(df['Air Quality']=='Good').sum()}   "
            f"Moderate: {(df['Air Quality']=='Moderate').sum()}   "
            f"Poor: {(df['Air Quality']=='Poor').sum()}   "
            f"Hazardous: {(df['Air Quality']=='Hazardous').sum()}",
            ha='center', fontsize=10, color='#555555')
    #saving image
    plt.tight_layout(rect=[0, 0.03, 1, 1])
    plt.savefig('../images/population_density_by_air_quality.png', dpi=150, facecolor=fig.get_facecolor())
    plt.show()
    logger.info("Image has been successfully created and saved!")
except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
    
```


    
![png](pipeline_files/pipeline_27_0.png)
    

