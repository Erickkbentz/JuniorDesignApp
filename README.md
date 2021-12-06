# JuniorDesignApp 

 

# Version 1.0 IVIE: Persuasion Dashboard

### General Information 

This is a web-application that can be hosted locally on your computer. This application takes in user-inputted data as a URL to a subreddit. It outputs the data after identifying any persuasion used in the posts form the reddit page and categorizes each instance of persuasion into one of the 3 main forms of rhetoric: Ethos, Logos, and Pathos. The analysis is then displayed as a “Job” on the Jobs page where the user can view graphical displays of the persuasion identified. 

### Machine Learning Info 

*-- Stage 1: Persuasion Detection --* 

For the persuasion detection file (located at JuniorDesignML/Models/PersuasionDetector/modelv3.ipyynb) we are doing a supervised text classification algorithm. We are primarily using the nltk, panas, and numpy libraries for data setup, preprocessing, and processing. To train the model, we used a test set generated by PRAW (using prawScript in the /OldOrTest folder). For persuasive examples we scraped the post bodies of r/UnpopularOpinion, and the comments of r/AmITheAsshole and r/ChangeMyView. For non persuasive examples we scraped the post comments from r/Gaming, r/WritingPrompts, and the post bodies of r/NoSleep. From there we created three feature sets (a count vector, a word vector, and an n-gram vector), and trained them on two different model types (Naive-Bayes and Linear Regression). At the bottom of the file we run each of the 6 models created against a manually created test set to gauge their accuracies. We found the Naive-Bayes Count Vector model performed best so that is the one being used in the dashboard currently. The file is set up so any new data set can be inputted and used to retrain the model in an attempt to increase its accuracy. 

*-- Stage 2: Persuasion Classification --* 

The most notable omission in our project is the lack of an unsupervised machine learning model to place persuasive text into groups. The ways we attempted to implement the unsupervised model were initially centered around a convolutional neural network. Such systems excel at finding abstractions within large bodies of data. This technique is most frequently used in tasks such as computer vision but from our research has been making moves into text classification. The way we attempted to implement this was to break down a paragraph, as an example, into a group of vectorized data know as parse tree, which represent the natual ways a body of text can be broken down through interpretation. This collection of vectors form a 2 dimensional array around which we convolve. This did not work through various attempts. 

We tried multiple strategies to utilize a k-means clustering algorithm to classify persuasion. Nothing we did seemed to organize the resulting clusters in a productive way. We tried introducing bias by searching for keywords within the text that might indicate logos, ethos, or pathos, and concatenated "logos", "ethos", "pathos" to the end of the string accordingly. (i.e. if it is rainy, then you need an umbrella logos). We thought it would help the algorithm better find a pattern within texts containing keywords. This did not work. 

Ultimately what we use is a system that scans the bodies of text for the most basic attributes of the type of classification. For logos we count the usage of the words, "if/then" and count the number of digits used. Similarly for Ethos we count the use of Proper nouns. For pathos we ran each body of text through a sentiment analysis api know as text2emotion. This sentiment analysis provided a score for 5 attributes whose sum is equal to 1 unless none of the prescribed emotions were found. These emotions were: Anger, Fear, Happy, Surprise and Sad. To use this data as a categorization tool we summed the scores for Anger and Fear from a belief these would be the emotions most likely used in a persuasive context. With some baseline heuristic for Logos, Ethos and Pathos we normalized our scores to provide a score for each category as a percentage. 

 

## Release Notes 

### New Software Features 

Ability to input a link to any subreddit or specific reddit page 

Ability to scrape and store comment data from reddit pages and  

Analyze inputted data through the machine learning algorithm 

Post-Process analyzed data to categorize persuasive text as Logos, Ethos or Pathos 

Display analyzed data for each dataset as pie charts showing persuasion and persuasive categories 

Download displays of analyzed data from machine learning model 

### Bugs Found and Fixed in Development 

There was a bug in the ML Server where if comments were added to a post as we were scraping the contents of it, the job would fail. This was ‘fixed’ by creating a temporary buffer of 10,000 to store new comments in. This could theoretically still crash if more than 10k comments are added while scraping. 

The JSON output from the ML Server was unusable to chart.js. This was fixed by removing ‘lines=true’ from the .to_json parameters in app.py in the ML Server. 

Initially, when attempting to connect the front end user elements to the prisma database, we encountered many run time errors. This bug was resolved by implementing an API that sends requests to post to prisma. 

### Known Bugs and Defects 

The file input does not work fully from front to back end, the file input form creates a fake directory path before the file name. 

The Log in screen and log out button sometimes show errors on the screen when used, refreshing the page will resume the application without error and achieve the desired result from the log in screen or log out button. 

### Unimplemented Features 

We gave a more thorough explanation of lacking features in the Machine Learning Info section. We provided a more detailed explanation of our development there which includes what we use in our finished product.  

As for the frontend, there is still some data in the JSON output that is currently being unused. More charts can be made using some of that data. Additionally, machine learning jobs cannot be deleted by users. Currently, a developer must access the Prisma database in order to delete jobs. Another feature that is not yet implemented fully is the ability for different users to be able to access different jobs. As of right now, logging in to the website is only needed to access the homepage. Anyone can access the full list of jobs and view/download any job. 

 

## Installation Guide 

### -- Pre-requisites -- 

Our app was made using Next.js which relies on the React framework. Our database is handled using the Prisma library. Our machine learning model uses the NLTK API. The charts in the job-view-page are created using the Chart.js library.  

For our application, we used the service Auth0 for authentication. We decided to use Auth0 due to its extensive combability with many development frameworks and SAML. As of right now, the user data is stored on a simple application that is a part of Auth0’s servers.  In our application, the information which determines which Auth0 application should be used for logging in is stored in the .env file. This file should be edited in the future, as the current Auth0 application being used is solely for testing and development purposes. For SAML, Auth0’s documentation includes how to configure SAML with Auth0. 

 

Node.js 12.22.0 or later - https://nodejs.org/en/  

Python 2.7 or later - https://www.python.org/downloads/  

 

### -- Install App -- 

``` 
git clone https://github.com/Erickkbentz/JuniorDesignApp.git 
cd JuniorDesignApp 
git submodule init 
git submodule update 
```

### -- Setup Frontend and Prisma database client -- 
```
cd JuniorDesignFE 
npm install 
npm i -g prisma@latest 
npm i @prisma/client@latest 
prisma generate 
``` 
### -- Setup Machine Learning -- 
```
cd ../JuniorDesignML 
pip install Flask praw pandas numpy PyPDF2 nltk text2emotion datetime 
``` 

NOTE: If running into any bugs 'module xxx does not exist' run 'pip install xxx' 

NOTE: If getting an error when installing Flask, try running:  
```
python3 –m venv venv 
. venv/bin/activate 
pip install Flask 
```

## How to Run App 

### -- Start up Frontend Server -- 

CD to ‘/JuniorDesignApp/JuniorDesignFE’ 
 
```
npm run build 
npm run start 
```

### -- Start up Machine Learning Server -- 

On a separate terminal session/tab  

CD to ‘/JuniorDesignApp/JuniorDesignML’ 
```
python app.py 
```

### -- Open database Prisma UI -- 

On a separate terminal session/tab 

CD to ‘/JuniorDesignApp/JuniorDesignFE’ 
```
prisma studio 
```

### -- Troubleshooting -- 

#### Prisma #### 

If the prisma is causing errors and prisma generate is also not working, try adding  
```
generator client { 

  provider = "prisma-client-js" 

} 
```
To the schema.prisma file under the prisma folder in the JuniorDesignFE repository.  

Then run: 
```
npx prisma generate
```

If the submodules are not updating correctly, try running: 
```
git submodule update – remote 
```
 
#### Log In and Log Out #### 

If a window appears on the screen alerting the user of a runtime error after pressing the “Log In” button, refreshing the screen will resume the application and log the user in. Similarly, when the user is logging out, there might be an alert immediately after pressing the “Log Out” button. Refreshing the page should fix this problem. 
