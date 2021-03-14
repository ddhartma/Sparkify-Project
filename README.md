[image1]: assets/Distribution_of_churned_and_non_churned_users.png "image1"

# Sparkify Project Workspace
Apache Spark Big Data Project to predict users of a web service who are going to downgrade or by cancel their web accounts.

## Content
- [Spark - a Big Data Framework](#spark_big_data)
- [The Goal of the Project](#project_goal)
- [DataFrame Understanding](#DataFrame_Understanding)
- [Setup Instructions](#Setup_Instructions)
- [Acknowledgments](#Acknowledgments)
- [Further Links](#Further_Links)


# Spark - a Big Data Framework <a name="spark_big_data"></a>

Why is Spark a powerful tool for **Big Data** Analytics? 

Spark is currently one of the most popular tools for big data analytics. You might have heard of other tools such as Hadoop. Hadoop is a slightly older technology although still in use by some companies. Spark is generally faster than Hadoop, which is why Spark has become more popular over the last few years.

# The Goal of the Project <a name="project_goal"></a>

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

DataFrame Understanding](#DataFrame_Understanding)
### Exploratory Data Analysis
There are:
- 286500 entries in the dataset
- 226 unique users, 189 unique last names and 56 unique first names.
- 2354 unique seeionIds.
- 8346 users without first and last name and without gender. They seem to be anonymous.
- 0 null userIds
- 0 null sessionIds
- 8346 userId with empty string


    ![image1]

The following feature set seems to be promising with regard to predict 'churn' (for further information please take a look into the notebook of this repo). Each feature is a column within an Apache Spark DataFrame. Each row contains the feature data for one unique user.

- ***membership_duration***: The duration of the membership 
- ***total_num_songs_played***: The total number of songs played
- ***avg_num_songs_session***: The average number of songs played during one session
- ***num_thumbs_up***: The total number of thumbs up
- ***num_thumbs_down***: The total numer of thumbs down
- ***num_add_playlist***: Number of songs added to the playlist
- ***num_add_friends***: Number of friends added 

After features are succesfully extracted and prepared, the following models are used in order to predict **churn**

- Logistic Regression
- Random Forest Classifier
- Decision Tree
- Gradient Boosting Trees

As a metric for model evaluation F1 score was calculated. As the subset is small and the two classes **churned (label=1)** and **non churned (label=0)** are uneven distributed.

Since the small number of churned users is small the models are evaluated using F1 score. F1 score is a good metric in case of an uneven class distribution. In fact, in this subset two classes **churned (label=1)** and **non churned (label=0)** are uneven distributed. As a result Gradient Boosted Tree Classifier shows the highest F1 score.

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
$ git clone https://github.com/ddhartma/Spark-Big-Data-Analytics.git
```

- Change Directory
```
$ cd Spark-Big-Data-Analytics
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
numpy = 1.17.4
pandas = 0.24.2
pyspark
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
