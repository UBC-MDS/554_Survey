How does previous level of degree influence your experience in MDS?
================

Contributors
------------

-   Richard Zitomer, `rzitomer`
-   Luo Yang, `lyiris22`

Introduction
------------

In this project, we want to find how does a person's previous level of degree attainment influence how difficult they find the MDS program. The data we use is collected from current MDS students anonymously. Throughout the analysis, we will study the relationship between levels of degree and difficulties people feel, as well as other confounding variables, such as major, working experience and programming experience. We will use METHOD\_OF\_ANALYSIS to analyze the data.

Survey Design
-------------

We use the service provided by [Hosted in Canada Surveys](https://www.hostedincanadasurveys.ca) to run our survey. According to [UBC Research Ethnics](https://ethics.research.ubc.ca/sites/ore.ubc.ca/files/documents/Online_Survey-GN.pdf), the data collected from the survey are stored in Canada, and the raw data would not be uploaded to public. Besides, we have a cover letter to disclose the potential risk of re-identification of the anonymous data when combining with application information in the MDS program. The survey is expected to be taken by current MDS students, and can be find here: [MDS Survey](https://trial.hostedincanadasurveys.ca/index.php/689877).

We have 5 questions in the survey, collecting the following information of the survey takers:

1.  The highest degree obtained before MDS
2.  The major of the most recent degree
3.  Whether they have programming experience
4.  How related is the work experience related to MDS
5.  How difficult they found the MDS program

The first question provides information of the main predictor, the level of degree. The respond of the last question would serve as the response. Questions 2-4 are related to the confounding variables, which are the major of the most recent degree, programming experience, and work experience.

We assume that the background in specific majors, such as computer science, statistics and mathematics, might affect both how difficult one finds the program and the likelihood of a student having a more advanced degree. Also, work experience might makes it less likely that a student has received a degree higher than a Bachelor’s, while prior programming experience might make the courses that requires coding easier. Therefore, we want to collect data from these aspects to study their relationship with the main prediction variable and the response.

To maximize the user's experience, all the questions are multiple choices, which is easier to take and less time consuming. We also considered to avoid asking questions that might be sensitive or have risk of involving in discrimination issues.

Data Collection
---------------

We have collected 56 observations from the survey. The data is stored in the private repo in github.ubc.ca, which can be found here: [survey\_results.csv](https://github.ubc.ca/MDS-2018-19/554_survey_results_rz_iy/blob/master/survey_results.csv).

For analysis, we carry out some basic data cleaning, providing the following data frame:

    ##   master         deg deg_cs deg_eng deg_stat deg_math prog_exp work_exp
    ## 1      1 engineering      0       1        0        0        1        1
    ## 2      0       other      0       0        0        0        1        1
    ## 3      0          cs      1       0        0        0        1        3
    ## 4      0       other      0       0        0        0        1        4
    ## 5      0        math      0       0        0        1        1        2
    ## 6      0 engineering      0       1        0        0        1        3
    ##   diff
    ## 1    3
    ## 2    3
    ## 3    4
    ## 4    4
    ## 5    2
    ## 6    2

The variables extract the following information from the survey takers:

-   `master`: whether the highest degree is more than Bachelors or not
-   `deg`: the major of the most recent degree
-   `deg_cs`: whether the most recent degree is related to computer science or not
-   `deg_stat`: whether the most recent degree is related to statistics or not
-   `deg_math`: whether the most recent degree is related to mathematics or not
-   `prog_exp`: whether having programming experience or not
-   `work_exp`: how the working experience is related to MDS (4 - very related, 3 - somewhat related, 2 - not related, 1 - no work experience)
-   `diff`: how diffcult they feel in MDS program (5 - very difficult, 4 - difficult, 3 - neutral, 2 - easy, 1 - very easy)

Exploratory Data Analysis
-------------------------

The plots below is based on the generated clean data. EDA based specifically on raw data can be found here: [554\_EDA.md](https://github.com/UBC-MDS/554_Survey/blob/master/docs/554_EDA.md)

<img src="Report_files/figure-markdown_github/EDA master-1.png" style="display: block; margin: auto;" />

From the distribution of the main variable `master`, we can see that the data is unbalanced, where "Master's of Higher" portion is only one third of "Bachelor's" portion. We would need to do some balancing in the analysis due to this.

<img src="Report_files/figure-markdown_github/EDA difficulty-1.png" style="display: block; margin: auto;" />

For the difficulty people find in MDS program, the distribution is not normal. It skews to the right, having the majority in 3 (Neutral) and 4 (Difficult), showing that most people find this program somewhat difficult.

<img src="Report_files/figure-markdown_github/EDA majors-1.png" style="display: block; margin: auto;" />

The boxplot with jitter shows the distribution of majors along the level of degree and difficulties people find in the program. For most of the current students in the program, their recent major is neither related to computer science, statistics or mathematics. It's hard to see the relationship between major and level of difficulty in this plot.

<img src="Report_files/figure-markdown_github/EDA programming experience-1.png" style="display: block; margin: auto;" />

However, when we compare those with programming experience and those do not, there is a more significant difference. Regardless their previous level of degree, those do not have programming experience seems to having more difficulty in the program. According to this, programming experience might be an important confounding variable that needs further attention in the analysis.
