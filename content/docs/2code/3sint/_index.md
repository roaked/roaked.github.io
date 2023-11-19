---
title: Predicting Fake News with ML 
weight: 3
---

# A Growing Case

Fake news, a term emblematic of fabricated information intentionally disseminated across traditional news outlets or online social platforms, embodies deliberate disinformation strategies. These falsehoods aim to tarnish individuals, entities, or gain financial or political advantages, often employing misleading, attention-grabbing headlines. Some counterfeit news pieces disguise themselves as satirical content, sounding incredulous to the point of absurdity, yet managing to deceive unsuspecting audiences.

The proliferation of digital media and social networks has led to a rampant increase in fake news dissemination, presenting a contemporary societal challenge. Misinformation, with its potential to adversely impact lives, demands the crucial ability to differentiate between genuine and counterfeit news. This task is intricate; genuine news might appear implausible to the average reader, while fake news endeavours to appear credible.

Addressing this contemporary issue involves the automatic identification and prevention of fake news dissemination. Efforts by digital corporations and journalistic agencies have attempted to combat fake news, but these solutions have shown imperfections. Academic research has delved into understanding the propagation of fake news, recognizing language usage as a vital parameter in these investigations.

Studies such as those by Mahyoob in his paper titled [Linguistic-Based Detection of Fake News in Social Media](https://www.researchgate.net/publication/345997025_Linguistic-Based_Detection_of_Fake_News_in_Social_Media) and by Preston ![Detecting fake news on Facebook: The role of emotional intelligence](https://pubmed.ncbi.nlm.nih.gov/33705405/) shed light on the analysis of language characteristics in detecting fake news, providing insights, particularly for this application within the context of Portuguese news.

Additionally, reports by [Facebook and FactCheck.org](https://www.factcheck.org/fake-news/) detail the challenges and strategies in combatting misinformation, emphasizing the significance of linguistic analysis in verifying news authenticity.

In this project, drawing upon a meticulously curated corpus comprising 3600 true and 3600 fake Portuguese news samples, collected from January 2016 to January 2018, I aimed to automatically identify fake news using aforementioned language characteristics. This endeavour relied on analyzing 21 specific language traits meticulously classified by the corpus' authors to transform news articles into metadata, aligning with methodologies outlined by academic works and industry efforts in the field.

The goal of this project is to utilize established machine learning techniques, as previously outlined in research by Mahyoob and Preston, employing each language characteristic as a metadata feature, to effectively identify and mitigate the spread of fake news within Portuguese-language news sources.


{{< katex display >}}

\begin{table}[hbt!]
\begin{center}
\begin{tabular}{ |c|c| } 
\hline
Number & Feature    \\
\hline
1 & number of tokens \\
\hline
2& number of words without punctuation \\
\hline
3& number of types  \\
\hline
4& number of links inside the news\\
\hline
5& number of words in upper case\\
\hline
6& number of verbs\\
\hline
7& number of subjunctive and imperative verbs \\
\hline
8& number of nouns \\
\hline
9& number of adjectives\\
\hline
10& number of adverbs \\
\hline
11& number of modal verbs (mainly auxiliary verbs) \\
\hline
12& number of singular first and second personal pronouns \\
\hline
13& number of plural first personal pronouns \\
\hline
14& number of pronouns\\
\hline
15& pausality\\
\hline
16& number of characters\\
\hline
17& average sentence length \\
\hline
18& average word length\\
\hline
19& percentage of news with spelling errors\\
\hline
20& emotiveness \\
\hline
21& diversity \\
\hline

\end{tabular}
\end{center}
\caption{Features}
\label{table:finalresults}
\end{table}

{{< /katex >}} 