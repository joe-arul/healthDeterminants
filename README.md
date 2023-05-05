## Determining financial, situational and demographic factors affecting the health of an individual

### ISM6562.Big Data for Business Project - Group 2


#### Approach:

We had the following objectives when starting this project:

- Identifying a business problem that adds value in a real-time setting .
- Achieving efficiency in distributed (big data) processing during the whole project.
- Including the tools learnt in class to complete our analysis.

We initially discussed collecting live data, but owing to time and availability constraints, we restricted ourselves to data already available. We allocated a reasonable amount of time to identify a potential business problem and to collect its associated data - During this time we explored open data repositories such as data.gov, data.worldbank.org , dataverse.harvard.edu and also explored possibilities of scraping web data if we find a suitable problem for analysis. 

In this process, we came across this gem of a dataset [(consumerfinance.gov/data-research/financial-well-being-survey-data)](https://www.consumerfinance.gov/data-research/financial-well-being-survey-data/) : A public use file containing information on a survey conducted by the Consumer Financial Protection Bureau(CFPB) in 2016 to assess the financial well-being of people living in the United States. The survey is well conducted with a comprehensive coverage of audience from different backgrounds and demographics. The survey questionnaire is also very well-designed to ensure that an individual's exact financial state is captured along with relevant circumstances based on studies. This data seems to have been used in several studies and experiments, but with a wealth of information in it, there is so much more to extract from this data.

We took time to study each variable represented in this survey and CFPB's intent in capturing them. We also studied the response labels for each variable and designed our data pre-processing accordingly. The dataset(217 columns) can be categorized into the following sections for easier understanding:

- individual and household characteristics
- income, employment and saving characteristics
- financial experiences and behaviours

As a group, we looked into existing materials available on this dataset (as time allowed) and proposed several statistical and predictive analytics to be done on this data. We picked one which is most relevant and continued our analysis based on that proposal, which will be explained in the coming sections.



#### Objective:

The survey had some very interesting features - the individual's financial knowledge, their family situation, their financial preferences to name a few. One that caught our attention is the feature 'health'. This is the individual's general take on their personal health and they were allowed to pick one among 5 options (ranging from 1-Poor to 5-Excellent). The intent of CFPB behind capturing this field is to see if and how an individual's health affects their financial well-being and the extent of it. While this is a good assumption, there is also the possibility that a person's health can be affected by their current financial status and by some other factors that have been captured in this dataset. Some interesting ones that stood out to us are - 

- 'MATERIALISM' : How much a person values material things such as cars and social status
- 'CONNECT' : Psychological connectedness of a person to their environment
- 'MORTGAGE' and 'SAVINGS'
- 'FWB' : The person is ahead or behind with their finances

While these fields may show interesting patterns, there could be so many other financial factors in the 217 columns that contribute to the overall health of an individual. To get a better correlation and to make the distinction lines a bit more clear, we club the existing categories to form 2 final categories. 

- 1(Poor) and 2(Fair) will be clubbed into (0-Poor health)
- 3(Good), 4(Very Good), 5(Excellent) will be clubbed into (1-Good health)


#### Use of Technology:

The primary focus in our analysis was on achieving efficiency and scalability with processing - as is the intent of the ISM6562 course. While this data is not necessarily considered as 'big' in comparison to today's computing and storage systems' abilities, we ensured that this approach would work even if the data is scaled up. To achieve this we used specific technologies and made some assumptions which will be explained further.

- We picked RDD-based Apache Spark over Apache Hadoop MapReduce for parallel computation owing to its better performance in iterative jobs which we would need for our project.
- We will be working with a single-node cluster, but our analysis will assume operating on a multi-node cluster in all stages - the technicalities hidden by Spark's interface (We assume our program runs in the master node, and that the data is present in the data node) 
- To demonstrate our abilities in setting up and querying a distributed storage system, we plan to store our data in a Cassandra database before reading it into an RDD-based Spark Dataframe which is also distributed. (Cancelled)
- In the future, we also plan to add streaming and stream-processing systems such as Spark/Kafka Streaming, Flink etc. to demonstrate and strengthen our understanding of Big Data Systems and their integration.

<div>
<img src="https://drive.google.com/uc?export=view&id=1Z-iz0-FPZ2cNVZuej9cMqD8ii6S3LAxz" width="800" align="middle">
</div>

#### Data Pre-processing methodology

The survey codebook available at [files.consumerfinance.gov/documents/cfpb_nfwbs-puf-codebook.pdf](https://files.consumerfinance.gov/f/documents/cfpb_nfwbs-puf-codebook.pdf) gives a comprehensive overview of all the variables available in this dataset including the unique labels associated with each variable and their frequencies. We did an exhaustive study on all variables available in this dataset and developed strategies on how to deal with every single one of them. This was time-consuming but required, as it was important to know the purpose of every variable and its distribution, so as to engineer it in a way the model can understand.
Our pre-processing strategy includes the following steps:

  - Drop columns not required for the model
  - Drop rows which may contribute negatively to the learning
  - Impute missing values wherever necessary (multiple strategies according to column type)
  - Scale numerical columns
  - Encode categorical columns
  - Identify Target categories
  - Address any imbalance in data
  - Train - test split
  
  
#### Model Evaluation and Interpretation based on metrics

We have trained three tree-based models to predict an individual's health status. Out of the three models, the Random Forest model performs the best with an accuracy of 0.907, sensitivity of 0.896, specificity of 0.919, and precision of 0.921.

Accuracy: (TP+TN) / (TP+FP+TN+FN) = 0.907 Sensitivity (Recall): TP / (TP+FN) = 0.896 Specificity: TN / (TN+FP) = 0.919 Precision: TP / (TP+FP) = 0.921

Here, TP (True Positive) represents the number of correctly predicted 'Good health' instances, TN (True Negative) represents the number of correctly predicted 'Poor health' instances, FP (False Positive) represents the number of 'Poor health' instances incorrectly predicted as 'Good health', and FN (False Negative) represents the number of 'Good health' instances incorrectly predicted as 'Poor health'.

The high accuracy, sensitivity, specificity, and precision of the Random Forest model indicate that it performs well in predicting both 'Good health' and 'Poor health' instances. High sensitivity (0.896) means that the model is effective in identifying 'Good health' instances, whereas high specificity (0.919) shows the model's ability to correctly classify 'Poor health' instances. High precision (0.921) suggests that the model has a low rate of false positives, meaning it is effective in correctly predicting 'Good health' instances without misclassifying 'Poor health' instances.

An AUC score of 0.5 indicates a model that performs no better than random chance, and a score of 1 represents a perfect classifier. Here our Random Forest model has an AUC score of 0.91267, which is a strong indicator of the model's ability to differentiate between the two health categories (0-Poor health and 1-Good health). This high AUC score signifies that the model has a high probability of correctly classifying individuals into their respective health categories based on the given features.