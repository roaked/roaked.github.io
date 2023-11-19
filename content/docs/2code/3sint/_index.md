---
title: Predicting Fake News with ML 
weight: 3
---

# Language Characteristics for Detection of Fake News 

## 1.1 A Growing Case

Fake news, a term emblematic of fabricated information intentionally disseminated across traditional news outlets or online social platforms, embodies deliberate disinformation strategies. These falsehoods aim to tarnish individuals, entities, or gain financial or political advantages, often employing misleading, attention-grabbing headlines. Some counterfeit news pieces disguise themselves as satirical content, sounding incredulous to the point of absurdity, yet managing to deceive unsuspecting audiences.

The proliferation of digital media and social networks has led to a rampant increase in fake news dissemination, presenting a contemporary societal challenge. Misinformation, with its potential to adversely impact lives, demands the crucial ability to differentiate between genuine and counterfeit news. This task is intricate; genuine news might appear implausible to the average reader, while fake news endeavours to appear credible.

Addressing this contemporary issue involves the automatic identification and prevention of fake news dissemination. Efforts by digital corporations and journalistic agencies have attempted to combat fake news, but these solutions have shown imperfections. Academic research has delved into understanding the propagation of fake news, recognizing language usage as a vital parameter in these investigations.

Studies such as those by Mahyoob in his paper titled [Linguistic-Based Detection of Fake News in Social Media](https://www.researchgate.net/publication/345997025_Linguistic-Based_Detection_of_Fake_News_in_Social_Media) and by Preston [Detecting fake news on Facebook: The role of emotional intelligence](https://pubmed.ncbi.nlm.nih.gov/33705405/) shed light on the analysis of language characteristics in detecting fake news, providing insights, particularly for this application within the context of Portuguese news.

Additionally, reports by [Facebook and FactCheck.org](https://www.factcheck.org/fake-news/) detail the challenges and strategies in combatting misinformation, emphasizing the significance of linguistic analysis in verifying news authenticity.

In this project, drawing upon a meticulously curated corpus comprising 3600 true and 3600 fake Portuguese news samples, collected from January 2016 to January 2018, I aimed to automatically identify fake news using aforementioned language characteristics. This endeavour relied on analyzing 21 specific language traits meticulously classified by the corpus' authors to transform news articles into metadata, aligning with methodologies outlined by academic works and industry efforts in the field.

The goal of this project is to utilize established machine learning techniques, as previously outlined in research by Mahyoob and Preston, employing each language characteristic as a metadata feature, to effectively identify and mitigate the spread of fake news within Portuguese-language news sources.

## 1.2 Approach Brainstorming

Following the problem description, there are 21 features to be analyzed:

{{< details "Features (click to expand)" close >}}
| Number | Feature                                             |
| ------ | ---------------------------------------------------- |
| 1      | number of tokens                                    |
| 2      | number of words without punctuation                  |
| 3      | number of types                                     |
| 4      | number of links inside the news                      |
| 5      | number of words in upper case                        |
| 6      | number of verbs                                     |
| 7      | number of subjunctive and imperative verbs           |
| 8      | number of nouns                                     |
| 9      | number of adjectives                                |
| 10     | number of adverbs                                   |
| 11     | number of modal verbs (mainly auxiliary verbs)       |
| 12     | number of singular first and second personal pronouns|
| 13     | number of plural first personal pronouns             |
| 14     | number of pronouns                                  |
| 15     | pausality                                           |
| 16     | number of characters                                |
| 17     | average sentence length                             |
| 18     | average word length                                 |
| 19     | percentage of news with spelling errors              |
| 20     | emotiveness                                         |
| 21     | diversity                                           |
{{< /details >}}


The initial step towards obtaining meaningful results involves preprocessing the available data. Determining which features to utilize for further division into training and testing sets was a crucial decision point, expounded upon in the following section. **How were these features selected?**

A thorough study was conducted employing statistical methods to assess the variability within each feature's dataset. Features demonstrating minimal variation, essentially stagnant in their values, were deemed non-contributory and subsequently excluded. Once these less informative features were removed, attention shifted to observing how these features varied between fake and true news samples. **Four specific linguistic features** were ultimately chosen, drawing from both statistical analysis and intuitive considerations.

Two distinct datasets were created: one encompassing all *21 features* and another featuring *only the selected linguistic features*. Throughout the project, these sets were compared, and the resultant differences were discussed. It was anticipated that utilizing a mere four features, compared to the full 21, might yield inferior outcomes due to the reduced dataset facilitating the differentiation between fake and genuine news.

Consideration for computational resources remained pivotal. The project emphasized optimizing computational efficiency, recognizing that certain models, such as clustering or neural networks, could demand substantial computation power. Maintaining a balance between model complexity and computational demand was crucial. Efficiency was prioritized without compromising noticeable accuracy outcomes.

Finally, acknowledging the variance in model results across simulations and the potential for parameter customization, efforts focused on identifying optimal parameter values for maximizing accuracy within each model. This iterative approach aimed to fine-tune model parameters for improved performance, considering the inherent variability in results across different simulations.

## 1.3 Data Preprocessing

The dataset utilized in this project originated from the ["Fake.Br Corpus" directly available at *Roney Santos'* github page](https://github.com/roneysco/Fake.br-Corpus) specifically curated to encompass both true and false news in Brazilian Portuguese.

This corpus originally contained complete news articles. However, the focus narrowed down to extract the essential features embedded within each news piece. All data was initially formatted in .txt files, necessitating the development of a [MATLAB script](https://github.com/roaked/fake-news-machine-learning/blob/main/Preprocessing.m) to convert it into a more manageable .mat format.

```
# Preprocessing data

# N = number of news
N = 1:3602

# Remove news that for some reason don't exist
N(697) = []
N(1467) = []

for i in N:
    # metaInputs will be the input for the models
    # importfile opens the txt files and saves them as mat variables
    # metaInputsTrue[:,i] = importfile(sprintf('%d-meta.txt',i))
    metaInputsFake[:,i] = importfile(sprintf('%d-meta.txt',i))

    # metaTargets will be the targets of the neural network
    # metaTargetsTrue[:,i] = [1, 0]
    metaTargetsFake[:,i] = [0, 1]

metaInputs = [metaInputsTrue metaInputsFake]
metaTargets = [metaTargetsTrue metaTargetsFake]
```

As it is seen, this transformation yielded two primary files: 'metaInputs.mat,' housing parameters for all news articles, and 'metaTargets.mat,' distinguishing true news (indicated by a '1' in the first row) from false news (marked with a '0' in the second row). To simplify navigation, a structural layout was adopted: the first half of the parameter files consistently represented true news, while the subsequent half constituted false news. This deliberate arrangement facilitated easier comprehension through the interpretation of variables and generated plots.

The dataset underwent a division into training and validation subsets. Employing a random selection method, **75% of the dataset was allocated for model training, while the remaining 25% served as a validation set**.

Analyzing the pivotal features responsible for differentiating between authentic and deceptive news involved employing various statistical methods such as 'corrplot,' 'matrixplot,' and 'boxplot'. However, the outcomes indicated that many features exhibited high non-linearity, posing a challenge in extracting meaningful correlations and insights.

The only meaningful contribution came from the [Boxplot.py Python function]((https://github.com/roaked/fake-news-machine-learning/blob/main/Boxplot.py)) given its concise visualization using key statistics like the minimum, quartiles, median, and maximum values, providing insights into data distribution. It efficiently identifies outliers, assesses symmetry, measures data clustering, and detects potential skewness in the dataset.

## 1.4 Methodology

Previously, all methods were initially applied to the entire set of features, followed by a re-execution using only the linguistic features for comparison. This approach aimed to gauge the potential trade-off between accuracy and computational efficiency, as eliminating numerous features could expedite processing time. Moreover, the objective shifted from merely identifying blatantly obvious fake news (e.g., those with poor punctuation or grammar) to developing a model adept at detecting less instances of misinformation, as indicated by the selected linguistic features.

### 1.4.1 Clustering

The clustering classification method involves creating distinct clusters based on the available features and assigning each cluster a class label, distinguishing between true and fake news.

Two types of clustering techniques, fuzzy c-means and k-means clustering, were employed. Crisp clustering algorithms allocate each data point to a single cluster based on quantified similarity, while fuzzy clustering allows varying degrees of membership to multiple clusters, reflecting diverse similarities.

Determining the optimal number of clusters was an initial consideration. Initially, there was a belief that higher cluster counts might yield better results, supported by a [MATLAB function 'evalclusters'](https://www.mathworks.com/help/stats/evalclusters.html). However, a comprehensive study later revealed this wasn't always the case.

Commencing with K-Means clustering, an algorithm using centroids and distance metrics, data points are associated with the nearest centroid, often calculated using squared Euclidean distances.

K-means clustering partitions observations into sets to minimize the within-cluster sum of squares. The **objective function** minimizes the variance by grouping observations into clusters.

{{< katex display >}}
\text{Cost Function} = \text{argmin}_S k \sum_{i=1}^{k} \sum_{x \in S_i} \| x - \mu_i \|_2^2 = \text{argmin}_S k \sum_{i=1}^{k} |S_i| \text{Var}(S_i)
{{< /katex >}} 


{{< details "Variables Description for K-means clustering (click to expand)" close >}}
- S denotes the set of clusters.
- k represents the number of clusters.
- x is a data point.
- {{< katex display >}}\mu_i{{< /katex >}}  signifies the centroid associated with cluster i.
- {{< katex display >}}S_i{{< /katex >}} indicates the {{< katex display >}}i^{th}{{< /katex >}}  cluster.
- {{< katex display >}}\|\|_2^2{{< /katex >}}  denotes the squared Euclidean distance.
- {{< katex display >}}\text{Var}(S_i){{< /katex >}}  represents the variance of cluster i.
{{< /details >}}


Identifying clusters containing fake news varied across simulations due to differing cluster numbering. To resolve this, the mode was employed to determine the cluster with the most data points, logically corresponding to fake news, given an equal split between true and fake data points.

Subsequently, fuzzy c-means clustering was executed, allowing data points to belong to multiple clusters with varying degrees of membership. Parameters such as the initial number of clusters 'c' and the exponent controlling fuzzy overlap 'm' were fine-tuned to optimize accuracy.

The FCM algorithm partitions a collection of data into fuzzy clusters, returning cluster centers and a partition matrix indicating each data point's degree of belonging to clusters.

{{< katex display >}}
\text{Cost Function} = \text{argmin}_C \sum_{i=1}^{n} \sum_{i=1}^{c} w_{ij}^m \| x_i - c_j \|_2^2
{{< /katex >}} 


{{< details "Variables Description for FCM clustering (click to expand)" close >}}
- C signifies the collection of clusters.
- n represents the number of data elements.
- c denotes the number of fuzzy clusters.
- x_i  represents a data point.
- {{< katex display >}}c_j{{< /katex >}}  signifies the {{< katex display >}}j^{th}{{< /katex >}}  cluster center.
- {{< katex display >}}w_{ij}{{< /katex >}}  represents the degree to which {{< katex display >}}x_i{{< /katex >}}  belongs to cluster {{< katex display >}}j{{< /katex >}} .
- m represents the fuzzifier controlling cluster fuzziness.
- {{< katex display >}}\|\|_2^2{{< /katex >}}  denotes the squared Euclidean distance.
{{< /details >}}

Both FCM and k-means aim to minimize objective functions; however, the addition of membership values and the fuzzifier parameter in FCM allows for fuzzier clustering. The fuzzifier 'm' determines the level of cluster fuzziness, with larger 'm' values resulting in fuzzier clusters, while 'm=1' implies crisp partitioning.

{{< details "Study on the exponent 'm' for all features and linguistic features (click to expand)" close >}}

The observed trend indicates that the peak accuracy aligns with the lowest exponent value of 'm,' typically slightly above one unit. Moreover, as the value of 'm' increases, there is an observable exponential decrease in accuracy.

![Max Accuracy vs. Fuzzy Partition Exponent (m) for all features](https://live.staticflickr.com/65535/53342293266_18477c93e5_c.jpg)

Applying clustering to linguistic features followed a similar process. The analysis revealed a maximum accuracy of 87.51% concerning the exponent value. 

![Max Accuracy vs. Fuzzy Partition Exponent (m) for linguistic features](https://live.staticflickr.com/65535/53342617799_f2443f4c18_c.jpg)

{{< /details >}}

### 1.4.2 Fuzzy Modelling

Fuzzy modeling uses rules that are like "if-then" statements in everyday language. These rules connect input to output in systems that work with vague or uncertain information. There are two main types of rules: one that's easier for people to understand and gives fuzzy (not exact) outputs, and another that's more mathematical, precise, and better for complex systems.

To make a fuzzy system, you start by grouping similar data together, allowing for some overlap between the groups. There are different ways to do this grouping. The number of groups usually matches the number of rules used in the system.

In these systems, the choice of rules affects how well they work for different tasks. Considering these differences, one type of rule, the Takagi-Sugeno model, was chosen for a specific case because it's better suited for complex systems and provides precise outputs.

{{< details "**Example:** Car's adaptive cruise control system with fuzzy logic - (click to expand)" close >}}

- If the distance to the car in front is relatively close and the speed is moderately high, then reduce acceleration slightly.
- If the distance to the car in front is quite far and the speed is low, then increase acceleration moderately.

In this scenario, fuzzy logic allows the system to interpret vague terms like "relatively close" or "quite far" regarding the distance to the car ahead. If the system were based on the Takagi-Sugeno model, it would precisely adjust acceleration based on these conditions, ensuring smoother driving and safer distance management.

{{< /details >}}