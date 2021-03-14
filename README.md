[image1]: assets/Distribution_of_churned_and_non_churned_users.png "image1"
[image2]: assets/feature_importance_rf.png "image2"


# Sparkify Project Workspace
Apache Spark Big Data Project to predict users of a web service who are going to downgrade or by cancel their web accounts.

This project is part of the Udacity Nanodegree program 'Data Science'. Please check this [link](https://www.udacity.com) for more information.

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

Spark is currently one of the most popular tools for big data analytics. You might have heard of other tools such as Hadoop. Hadoop is a slightly older technology although still in use by some companies. Spark is generally faster than Hadoop, which is why Spark has become more popular over the last few years.

## Business Understanding - The Goal of the Project <a name="project_goal"></a>

Imagine, we are working in the data team of a popular digital music service similar to Spotify or Pandora. Let's call it Sparkify. Users of Sparkify can stream their favourite songs either by using a free tier that places advertisements between the songs or by using the premium subscription model. The latter one is a flat rate account, where they stream music as free but pay a fixed price on a monthly base. Users can upgrade, downgrade or cancel their service at any time. So it's crucial to make sure our users love the service.

A user can interact with the service in multiple ways, e.g. playing songs, logging out, liking a song with a thumbs up, hearing an ad, downgrade the service etc. Every timne a user interacts with the service data is generated. All this data contains the key insights for keeping your users happy and helping your business thrive. It's your job on the data team to predict which users are at risk to churn either downgrading from premium to free tier or cancelling their services altogether. If you could accurately **predict these users before they leave your business** could offer them discounts and incentives potentially saving your business millions in revenue.

The web pages a user can intect with are:
- Cancel
- Submit Downgrade
- Thumbs Down
- Home
- Downgrade
- Roll Advert
- Logout
- Save Settings
- Cancellation Confirmation
- About
- Submit Registration
- Settings
- Login
- Register
- Add to Playlist
- Add Friend
- NextSong
- Thumbs Up
- Help
- Upgrade

To tackle this project to predict "churn users", there is a full dataset of 12GB. A depoyment of a Spark cluster on a cloud service using AWS or IBM Cloud to analyze a larger amount of data would be possible. Here, for prototyping purposes using Spark's DataFrame techniques, a small 128MB subset of this data is analysed.

A user who churned submitted a **Submit Downgrade** or **Cancellation Confirmation** event.

In order to predict **churn** the followings steps need to be addressed:
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
- The json file 'mini_sparkify_event_data.json' has been loadded into a Spark dataframe
- A dataframe explanatory gave the following results:
    - 286500 entries in the dataset
    - 226 unique users, 189 unique last names and 56 unique first names.
    - 2354 unique sessionIds.
    - 8346 users with an 'empty string userId' and without first, last name and gender. 
    - 0 null userIds
    - 0 null sessionIds
    
    ![image1]

## DataFrame preparation <a name="DataFrame_preparation"></a>
- For feature engineering purposes a function called **create_dataframe_for_modeling** was created. It contains the feature extraction process and returns a dataframe for modeling to predict **churn**. This is workflow of this function.
    - Read the dataframe from a json file
    - Clean the Dataframe
    - Define churn, create a label column
    - Create feature columns (via further function calls)
    - Remove unnecassary columns
    - Fill NaN with 0
    - Cast features to float
    - Rename churn with label
    - Return model dataframe **df_model**

- The following feature set seems to be promising with regard to predict 'churn' (for further information please take a look into the notebook of this repo). Each feature is a column within an Apache Spark DataFrame. Each row contains the feature data for one unique user.

    - ***membership_duration***: The duration of the membership 
    - ***total_num_songs_played***: The total number of songs played
    - ***avg_num_songs_session***: The average number of songs played during one session
    - ***num_thumbs_up***: The total number of thumbs up
    - ***num_thumbs_down***: The total numer of thumbs down
    - ***num_add_playlist***: Number of songs added to the playlist
    - ***num_add_friends***: Number of friends added 

- After this extraction step features had to be transformed to scaled Spark Vectors and the new dataframe was split into training and validation sets. Those actions were implemeted in the function **prepare_model_dataframe** and included the following steps:
    - Convert numeric columns to Spark's vector type via **VectorAssembler**
    - Scale vectorized data via Spark's **MinMaxScaler**
    - Split dataframe into training and validation sets via **randomSplit**


## Modeling <a name="modeling"></a>
- After features were succesfully extracted and prepared, the following models were used in order to predict **churn**
    - Logistic Regression
    - Random Forest Classifier
    - Gradient Boosting Trees
- The model was created by using Spark's Pipeline module and chaining input vectorization, minmax scaling and classification/regression.
- By using Spark's ParamGridBuilder different classifier/regression model hyperparameters were tuned, e.g. maximum number of iterations or number of trees.
- Via Spark's CrossValidator the differnt pipeline estimators were evaluated. Thereby The number of folds was fixed to 3. 

## Evaluation <a name="evaluation"></a>
-  As the dataset is small and **churned (label=1)** and **non churned (label=0)** users are not evenly distributed the f1 score should be the best metric for predicting churn.
- The highest f1 score was achieved for the RandomForestClassifer approach. 
- Thereby the f1 score is highly sensitiv to the choice of hyperparameters. Hence it is believed, there is still room for optimizations. 
- All three models show that the duration of membership is a good feature in predicting churn. In all three moodels this feature has high feature importance values.

    ![image2]

## Further optimizations <a name="further_optimizations"></a>
- Tune the the hyperparameters of the corresponding classifier and regression models in a wider range.  However, take care about overfitting especially when tuning maxIter.
- Implement further hyperparameters (see above).
- Use a larger dataset. 225 unique users is just a small subset. It is believed that a transfer to the 12GB dataset could result in higher f1 scores. In addition, other metrics like accuracy could then be used as the binary label set (1 or 0) is more evenly distributed.
- Implement further features for modeling.
- Integrate other databases in order to link song names with genre. Maybe churned user may like to listen to a special kind of genre which is only weakly supported by Sparkify at the moment. This result could be also used as a baseline for a Recommendation Engine in future optimizations.


## Files in the repo <a name="files_in_repo"></a>
- **Sparkify.ipynb** - the Jupyter Notebook containing all Data Understanding, Preparation as well as Modeling and Evaluation results.
- **assets** - folder containing images depicted in this readme.
- Please pay attention: the json dataset file (128MB) **mini_sparkify_event_data.json** containing the investigated subset of Sparkify data is not in this repo. This file is part of the [Udacity Nanodegree program 'Data Science'](https://www.udacity.com) . 

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