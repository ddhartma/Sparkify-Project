[image1]: assets/Distribution_of_churned_and_non_churned_users.png "image1"
[image2]: assets/feature_importance_rf.png "image2"


# Sparkify Project Workspace
An Apache Spark Big Data Project - predict users who are going to churn from a web service

This project is part of the Udacity Nanodegree program 'Data Science'. Check this [link](https://www.udacity.com) for more information.

Furthermore: Please take look at my medium Blog post [How to predict users churning from a web service](https://hartmann-david.medium.com/how-to-predict-users-churning-from-a-web-service-4287e134903a)

## Content
- [Spark - a Big Data Framework](#spark_big_data)
- [Business Understanding - The Goal of the Project](#project_goal)
- [DataFrame Understanding](#DataFrame_Understanding)
- [DataFrame preparation](#DataFrame_preparation)
- [Modeling](#modeling)
- [Evaluation](#evaluation)
- [Further optimizations](#further_optimizations)
- [Files in the repo](#files_in_repo)
- [Setup Instructions](#Setup_Instructions)
- [Acknowledgments](#Acknowledgments)
- [Further Links](#Further_Links)


## Spark - a Big Data Framework <a name="spark_big_data"></a>

Why is Spark a powerful tool for **Big Data** Analytics? 

Besides Hadoop, Spark is currently one of the most popular tools for big data analytics. Hadoop is a slightly older technology although still in use by some companies. Spark is generally faster than Hadoop, which is why Spark has become more popular over the last few years.

## Business Understanding - The Goal of the Project <a name="project_goal"></a>

Suppose, you are a Data Scientist at popular digital music service similar to Spotify or Pandora. Let's call it Sparkify. Users of Sparkify can stream their favourite songs either by using a free tier that places advertisements between the songs or by using the premium subscription model. The latter one is a flat rate account, where they stream music as free but pay a fixed price on a monthly base. Users can upgrade, downgrade or cancel their service at any time. So it's crucial to make sure our users love the service.

A user can interact with the Sparkify service in multiple ways, e.g. playing songs, liking a song with a thumbs up, listen to an advertisement, downgrade the service etc. Every time a user interacts with the service data is generated. All this data contains information about keeping your users happy. Now your Data Team receive the task to predict which users are at risk to churn either downgrading from premium to free tier or cancelling their services altogether. If you could accurately **predict these users before they leave your service** you could offer them for example discounts. In the end this could increase your business revenue significantly.

A user can interact with the following web pages:
|    |     |    |
| :------------- | :------------- | :------------- |
| About     | Add Friend       | Add to Playlist
| Cancel     | Cancellation Confirmation       | Downgrade 
| Error     | Help       | Home
| Login     | Logout       | NextSong
| Register  | Roll Advert       | Save Settings
| Settings     | Submit Downgrade      | Submit Registration
| Submit Upgrade | Thumbs Down | Thumbs Up
| Upgrade|||

A dataset of 12GB (provided by Udacity) is the base for this project. To tackle such a Big Data task a data deployment on a distributed computing cluster, preferably by using a service like AWS or IBM Cloud, would be beneficial. In the following, for prototyping purposes, a small 128MB subset (‘mini_sparkify_event_data.json’) was analysed. However, in order to apply the model development to distributed cloud cluster environments in the future, Apache Spark’s DataFrame and Machine Learning techniques were implemented (instead of scikit-learn frameworks for example).

A user who churned submitted a **Submit Downgrade** or **Cancellation Confirmation** event.

In order to predict **churn** the following steps need to be addressed:
1. Load Data as an Apache Spark DataFrame
2. Explore the data
3. Clean the Data 
4. Define the 'churn' label and create features useful for 'churn' prediction
5. Create Spark Vectors as input variables for Sparks Machine Learning Tools 
6. Scale/Normalize features
7. Split Data into Training and testing sets 
8. Train the model
9. Evaluate the model using F1 score.

## DataFrame Understanding <a name="DataFrame_Understanding"></a>
The columns of the dataframe are:

| Column Name    | Type  | Description
| :------------- | :------------- | :------------- 
| artist    | string     | user identifier
| auth    | string     | authentication level, 4 categories (Logged In, Logged Out, Cancelled, Guest) 
| firstName    | string     | user's first name
| gender | string | user's gender, 2 categories (M, F)
| itemInSession    | long     | item count during a session
| lastName    | string     | user's last name
| length    | double     | length of a song in seconds
| level    | string     | user's account type, 2 categories (free or paid)
| location    | string     | user's location
| method    | string     |type of http request method; 2 categories (GET and PUT)
| page    | string     | type of interaction, 22 categories (see above)
| registration    | long     | timestamp of user registration
| sessionId    | long     | actual user's session number 
| song    | string     | name of the actual song  
| status    | long     | http status code, 3 categories (200, 307 and 404)
| ts    | long     | timestamp of log
| userAgent    | string     | agent used by the user to access the streaming service, 57 different categories
| userId    | string     | user identifier


- The json file 'mini_sparkify_event_data.json' was loaded into a Spark dataframe
- From a dataframe exploration resulted in:
    - 286500 entries in the dataset
    - 226 unique users, 189 unique last names and 56 unique first names
    - 2354 unique sessionIds
    - 8346 users with an 'empty string userId' and without first, last name and gender
    - 0 null userIds
    - 0 null sessionIds
    - 52 'churned' user
    - 173 'non-churned' user
    
    ![image1]

## DataFrame preparation <a name="DataFrame_preparation"></a>
- For feature engineering purposes a function called **create_dataframe_for_modeling** was created. It contains the feature extraction process and returns a dataframe for modeling. This is workflow of this function.
    - Read the dataframe from a json file
    - Clean the dataframe (i.e. drop entries with empty string userIds)
    - Define churn and create a label column called ‘churn’
    - Create feature columns (via feature function calls)
    - Normalize features by membership duration
    - Join features together in df_model
    - Remove columns which are not needed anymore
    - Fill NaN with 0 (if there would be any)
    - Cast features to float
    - Rename churn with ‘label’
    - Return a model dataframe called **df_model**

- The following feature set seems to be promising with regard to predict 'churn' (for further information please take a look into the notebook of this repo). Each feature is placed in an Apache Spark DataFrame (col1 = userId, col2 = feature). Each row contains the feature data for one unique user.
    - **gender**: The gender of the user
    - ***membership_duration***: The duration of the membership 
    - ***rate_songs_played***: The total number of songs played per day and user
    - ***avg_num_songs_session***: The average number of songs played during one session
    - ***rate_thumbs_up***: The total number of thumbs up per day and user
    - ***rate_thumbs_down***: The total numer of thumbs down per day and user
    - ***rate_errors***: The total number of occurred errors per day and user
    - ***rate_add_playlist***: Number of songs added to the playlist per day and user
    - ***rate_add_friends***: Number of friends added per day and user

- After feature extraction, features had to be transformed to scaled Spark Vectors. Then the new dataframe was split into training and validation sets. Those actions were implemeted in the notebook function **prepare_model_dataframe** with the following steps:
    - Convert numeric columns to Spark's vector type via **VectorAssembler**
    - Scale vectorized data via Spark's **MinMaxScaler**
    - Split dataframe into training and validation sets via **randomSplit**

## Modeling <a name="modeling"></a>
- After features were succesfully extracted and prepared by vectorization, scaling and further cleaning, different Machine Learning models based on **Logistic Regression**, **Random Forest Classification** and **Gradient Boosting Trees** were trained and applied to the dataset with the aim to predict churn.
- Thereby, the model was created by using Spark's Pipeline module. Features were first vectorized, then scaled and than fit by classification or regression.
- By using Spark's ParamGridBuilder different classifier/regression model hyperparameters were tuned, e.g. maximum number of iterations or number of trees.
- Via Spark's CrossValidator the differnt pipeline estimators were evaluated. Thereby The number of folds was fixed to 3. 

## Evaluation <a name="evaluation"></a>
-  As the dataset is small and **churned (label=1)** and **non churned (label=0)** users are not evenly distributed the f1 score should be the best metric for predicting churn.
- The highest f1 score was achieved for the RandomForestClassifer approach. 
- Thereby the f1 score is highly sensitiv to the choice of hyperparameters. Hence it is believed, there is still room for optimizations. 
- All three models show that the duration of membership is a good feature for predicting churn. For all three moodels this feature has realtively high feature importance values.


## Further optimizations <a name="further_optimizations"></a>
- Tune the the hyperparameters of the corresponding classifier and regression models in a wider range.  However, take care about overfitting especially when tuning maxIter.
- Implement further hyperparameters for model tuning (see above).
- Use a larger dataset. 225 unique users is just a small subset. It is believed that a transfer to the 12GB dataset could result in higher f1 scores.
- Implement further features for modeling.
- Integrate other databases in order to link song names with genre as an example. Maybe dissapointed, churned user may like to listen to a special kind of genre which is only weakly supported by Sparkify at the moment. This result could be also used as a baseline for a Recommendation Engine in future optimizations.


## Files in the repo <a name="files_in_repo"></a>
- **Sparkify.ipynb** - the Jupyter Notebook containing all Data Understanding, Preparation as well as Modeling and Evaluation results.
- **assets** - folder containing images depicted in this readme.
- Please pay attention: the json dataset file (128MB) **mini_sparkify_event_data.json** containing the investigated subset of Sparkify data is not provided in this repo. This file is part of the [Udacity Nanodegree program 'Data Science'](https://www.udacity.com) . 

## Setup Instructions <a name="Setup_Instructions"></a>
The following is a brief set of instructions on setting up a cloned repository.

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Prerequisites: Installation of Python via Anaconda and Command Line Interaface <a name="Prerequisites"></a>
- Install [Anaconda](https://www.anaconda.com/distribution/). Install Python 3.7 - 64 Bit

- Upgrade Anaconda via
```
$ conda upgrade conda
$ conda upgrade --all
```

- Optional: In case of trouble add Anaconda to your system path. Write in your CLI
```
$ export PATH="/path/to/anaconda/bin:$PATH"
```

### Clone the project <a name="Clone_the_project"></a>
- Open your Command Line Interface
- Change Directory to your project older, e.g. `cd my_github_projects`
- Clone the Github Project inside this folder with Git Bash (Terminal) via:
```
$ git clone https://github.com/ddhartma/Sparkify-Project.git
```

- Change Directory
```
$ cd Sparkify-Project
```

- Create a new Python environment, e.g. spark_env. Inside Git Bash (Terminal) write:
```
$ conda create --name spark_env
```

- Activate the installed environment via
```
$ conda activate spark_env
```

- Install the following packages (via pip or conda)
```
numpy = 1.12.1
pandas = 0.23.3
matplotlib = 2.1.0
seaborn = 0.8.1
pyspark = 2.4.3
```

- Check the environment installation via
```
$ conda env list
```

## Acknowledgments <a name="Acknowledgments"></a>
* This project is part of the Udacity Nanodegree program 'Data Science'. Please check this [link](https://www.udacity.com) for more information.

## Further Links <a name="Further_Links"></a>

Git/Github
* [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)
* [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)
* [5 types of Git workflows](https://buddy.works/blog/5-types-of-git-workflows)

Docstrings, DRY, PEP8
* [Python Docstrings](https://www.geeksforgeeks.org/python-docstrings/)
* [DRY](https://www.youtube.com/watch?v=IGH4-ZhfVDk)
* [PEP 8 -- Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/)

Apache Spark
* [Random Forest Models With Python and Spark ML](https://www.silect.is/blog/random-forest-models-in-spark-ml/)
* [Logistic Regression in Spark ML](https://medium.com/@dhiraj.p.rai/logistic-regression-in-spark-ml-8a95b5f5434c)
* [Building A Machine Learning Model With PySpark [A Step-by-Step Guide]](https://hackernoon.com/building-a-machine-learning-model-with-pyspark-a-step-by-step-guide-1z2d3ycd)
* [Apache.org: Classification and regression](https://spark.apache.org/docs/2.2.0/ml-classification-regression.html)