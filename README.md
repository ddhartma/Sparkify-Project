
[savinay](https://github.com/savinay/sparkify-udacity-dsnd/blob/master/Sparkify.ipynb)

# Sparkify Project Workspace
Streaming music is nowadays
Suppose, we are the data team of a popular digital music service similar to Spotify or Pandora. The users stream their favorite songs either using the free tier that place advertisements between songs or use the premium plan for which they pay a monthly fee without any advertisements between songs. Users can upgrade, downgrade or altogether cancel their services. The dataset given to us contains a log of the activities of each user on the service whether they are playing songs, logging out, upgrading their service or cancelling it. All this data contains key insights in keeping the users of the service happy. Our task in this project is to develop a model that predict which users are at risk. If we can identify users that are at risk to churn either by downgrading from premium or by cancelling their service, the business can offer them incentives and discounts potentially saving millions in revenues.

To tackle this project, we are given a subset of the dataset that contains a log of events mentioned below. The events include:

    Cancel
    Submit Downgrade
    Thumbs Down
    Home
    Downgrade
    Roll Advert
    Logout
    Save Settings
    Cancellation Conf...
    About
    Submit Registration
    Settings
    Login
    Register
    Add to Playlist
    Add Friend
    NextSong
    Thumbs Up
    Help
    Upgrade

To solve this problem, first we load the dataset into Apache Spark and delete any rows that have missing userIds or sessionIds. Then we calculate the below mentioned metrics from the given data for each user.

    average number of songs per session
    Total number of thumbs down
    Total number of thumbs up
    Total number of errors encountered
    Number of Add to Playlist events
    Number of Add Friends events
    Total number of songs played

The label which needs to be predicted is churn which is defined by a user who has performed either a "Submit Downgrade" event or "Cancellation Confirmation" event.

After the above features are calculated, we build the model using the following three algorithms:

    Logistic Regression
    Random Forest Classifier
    Gradient Boosting Trees

The dataset is split into training and testing sets. A machine learning pipeline is built in which we first convert each row into a single feature vector. Then data in each of the column is normalized and is fed into each of the above classifiers.
Metrics

The models are evaluated using the F1 score since we have a small number of churned users. F1 score is a better measure to use when there is an uneven class distribution. For our dataset, Gradient Boosted Tree Classifier gives the best F1 score. We also calculate the accuracy of each of the models but we base our decision of selecting a model based on the F1 score calculated.
