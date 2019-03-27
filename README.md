# Proposal

## Main question
How does your previous level of education before MDS influence how difficult (Bachelors or other) you find the MDS program?

## Confounding Variables

#### Potential confounder: Most recent degree is/is not related to CS - X2
Justification: We think that whether or not someone’s degree is in computer science could be a confounding factor in the relationship between degree attained and difficulty of the MDS program because 1) having a background in CS effects how difficult one finds the program and 2) studying Computer Science will have an effect on the likelihood of a student who is admitted into the MDS program having a more advanced degree.

#### Potential confounder: Most recent degree is/is not related to Mathematics (Other than Statistics) - X3
X3: binary (levels: Most recent degree related to Mathematics (other than Statistics), not related)
Justification: We think that whether or not someone’s degree is in Mathematics could be a confounding factor in the relationship between degree attained and difficulty of the MDS program because 1) having a background in Mathematics effects how difficult one finds the program and 2) studying Mathematics will have an effect on the likelihood of a student who is admitted into the MDS program having a more advanced degree.

#### Potential confounder: Most recent degree is/is not related to Statistics - X4
Justification: We think that whether or not someone’s degree is in Statistics could be a confounding factor in the relationship between degree attained and difficulty of the MDS program because 1) having a background in Statistics effects how difficult one finds the program and 2) studying Statistics will have an effect on the likelihood of a student who is admitted into the MDS program having a more advanced degree.

#### Potential confounder: Most recent degree is/is not related to Engineering (Other than Computer Science) - X5
Justification: We think that whether or not someone’s degree is in Engineering could be a confounding factor in the relationship between degree attained and difficulty of the MDS program because 1) having a background in Engineering effects how difficult one finds the program and 2) studying Engineering will have an effect on the likelihood of a student who is admitted into the MDS program having a more advanced degree.

#### Potential confounder: Having/not having work experience - X6
Justification: We assume that having work experience makes it less likely that a student has received a degree higher than a Bachelor’s, so it could be a confounding factor when looking at the relationship between degree attained and difficulty of the MDS program.

#### Potential confounder: Having/not having programming experience - X6
Justification: We assume that having prior programming experience would make the MDS program much easier, and it’s a reasonable assumption that there is a correlation between degree attained and knowing a programming language, so it could be a confounding factor.

## Analyzing Survey Results

Besides the confounding variables we have our predictor and response:

- X1: binary (levels: Bachelor, more than Bachelor)
Justification: we are trying to understand the relationship between highest degree attained (either a Bachelor’s or higher than a Bachelor’s) and the difficulty of the MDS program. This is the variable we’ll use to make claims about whether or not these things are correlated.

- Y: categorical (question: How difficult have you found the MDS program? levels: 1-Very Easy, 2-Easy, 3-Neutral, 4-Difficult, 5-Very Difficult)
Justification: This is the variable we’re trying to predict, namely, how easy is the program (and is that influenced by degree attainment). We will use a likert scale to determine how difficult a student finds the program: https://www.simplypsychology.org/likert-scale.html.

The data from the survey will be divided into predicting variables and target variables. The prediction variables include X1, which is the level of education, and X2 to X7, which corresponds to the confounding variables. The target will be Y, the level of difficulty the student find in the MDS program.

First we will do EDA, probably in bar charts, to show the distribution of each variables. In general, we want to find out how X1 affects Y, and also how does X2 to X7 have influences between them. So we will also do EDA to show the relationship between confounding variables and target, and manually do some feature selection to decide which confounding variables we want to bring into our model.

Then we will divide the data into training and testing portion, and use the training portion to fit the models. We will use X1 and the selected confounding variables to estimate the target. We will compare some models, such as logistic regression and KNN, to see which model works best in our case. We can also do some further feature selection to explore which confounding variables actually affects the relationship between X1 and Y.

After getting a comparatively reasonable model, we can test the model on testing portion. We can also analyze the relationship between X1 and Y based on the model we get.


## Relevance to the UBC Office of Research Ethics document

According to the [UBC Office of Research Ethics document on Using Online Surveys](https://ethics.research.ubc.ca/sites/ore.ubc.ca/files/documents/Online_Survey-GN.pdf), we would make sure the data collected from the surveys are stored in local repos in Canada. The raw data would not be uploaded to Github or other online services.

Also since our population is current MDS students and the information about background could possibly identify a student if combined with the application information in MDS program, we would add a cover letter to disclose this potential for identification.
