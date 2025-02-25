---
layout: post
title: What is cluster analysis
description: What is cluster analysis
tag: Data
---

# Intelligent customer service system (cluster analysis)

### What is TF-IDF

1. TF-IDF is a weighting technique that is commonly used technique in information retrieval and data mining. There are two terms. The first one is term frequency and another one is inverse document frequency.

2. What TF-IDF can help us do is to filter those high frequency but non-meaningful word such as "a", "the", "of", "for". By using TF, we can gather those word information and filter them out. For those words that has same frequency but does not have same importance, IDF will help us prioritize their importance. It will give high frequency word less weight. When we time TF and IDF together, we get the value TF-IDF. By sorting the value, we will the most important words in this article or paragraph.

3. To be more specific, TF is the times a word appear in this article or paragraph. In order to compare two different articles who have different length, we need to upgrade our TF = the times a word appear in this article/total words in the article. Then calculate the IDF = log(Total number of documents in the corpus/ Number of documents containing this term).

4. In python, what we can use is

   1. ``````python
      vectorizer = CountVectorizer()  # It is used to construct corpus (vocabulary)
      transformer = TfidfTransformer() # Define
      ``````

      1. `CountVectorizer()`  transform the text to word frequency matrix (number of occurrences of each word)
         1. ![image-20250224231626504](C:\Users\junha\AppData\Roaming\Typora\typora-user-images\image-20250224231626504.png)
      2. `TfidfTransformer()` calculate the weight of TF-IDF based on frequency matrix.

   2. ``````python
      tfidf = transformer.fit_transform(vectorizer.fit_transform(data['result']))
      tfidf_weight = tfidf.toarray()
      ``````

      1. `vectorizer.fit_transform(data['result'])` transform text to word frequency matrix
      2. ` transformer.fit_transform(x)`  calculate the weight of TF-IDF based on frequency matrix.
      3. eventually, `tfidf_weight` will have two dimension(3, 15). Three text, fifteen words. Each column represents a unique word (feature word), and the number of columns = the total number of words after deduplication

### What is Sentence Embedding

1. TF-IDF is based on term frequency, which mainly focus on what the occurrences of words. However, sentence embedding on text semantic feature, which means that it focus on the meaning and the logic of the sentence.
2. Normally, we use deep learning model like `BERT`, `SentenceTransformer` to generate sentence embedding.
3. The shape of sentence embedding, similar to TF-IDF, is also two dimensional. The first dimension is the number of text, the second dimension is semantic vector.

### TF-IDF & Sentence Embedding

1. TF-IDF -> calculating the word frequency, cannot capture semantic (meaning and logic of the sentence)
2. Sentence Embedding -> Using deep learning technique, could better understand semantic

### What is data processing step

1. I write a function to preprocessing Chinese text. Since in a paragraph or in a text, there are lots of meaningless words. Therefore, we can use existence stop words file. This function takes two parameters. The first one is plain text, the second one is stop_words file. First we need to get rid of English words and punctuations. Then we need to use space to join all the characters together.

2. Then I use a Chinese library called jieba that can separate as many words as possible. Because Chinese paragraph is constructed by each single word. By using jieba, it takes out all possible combinations of those characters that can make to a word.

3. Then we just check whether the word we take out is in stop words or not. If it is not in the stop words, then we put it into the result words list.

4. ``````
   def chinese_word_cut(mytext,stop_words):
       new_data = re.findall('[\u4e00-\u9fa5]+', mytext, re.S)
       new_data = " ".join(new_data)

       seg_list_exact = jieba.cut(new_data, cut_all=True)
       result_list = []

       for word in seg_list_exact:
           if word not in stop_words and len(word) > 1:
               result_list.append(word)
       return " ".join(result_list)
   ``````

5. After defining it, we can use that function to analyze each text I get. First of all, I need to load the stop words file and then apply `chinese_word_cut` into each text.

6. ``````Python
   def get_tfidf(data):
       with open('stop_words.txt', encoding='utf-8') as f:
           stop_words = set()
           for i in f.readlines():
               stop_words.add(i.replace("\n", ""))
       data['分词结果'] = data['txt'].apply(lambda x: chinese_word_cut(x, stop_words)) # apply `chinese_word_cut` into each text.
       vectorizer = CountVectorizer()
       transformer = TfidfTransformer()
       tfidf = transformer.fit_transform(vectorizer.fit_transform(data['分词结果']))
       tfidf_weight = tfidf.toarray()
       print(tfidf_weight.shape)
       return tfidf_weight
   ``````

   1. Normally `vectorizer.fit_transform` requires its input be an array or pandas containing the text after tokenization (string)

### Basic function set up

1. Calculate cosine similarity between two sample matrix x and y

   1. Notice that the function of calculating cosine similarity is $$ \text{Cosine Similarity} = \frac{X \cdot Y^T}{||X|| \cdot ||Y||} $$, X and Y is the row of each matrix

   2. ```python
      def calculate_sim_pair(X: np.ndarray, Y: np.ndarray) -> np.ndarray:
          X_norm = np.linalg.norm(X, axis=1) # Row vector magnitude of X, L2 Euclid disatance
          Y_norm = np.linalg.norm(Y, axis=1) # Row vector magnitude of Y
          sim_pair = np.dot(X, Y.T) / np.outer(X_norm, Y_norm) # formula of cosine similarity
          sim_pair[np.isnan(sim_pair)] = 0
          return sim_pair
      ```

   3. After finishing writing `calculate_sim_pair`, it is okay for us to calculate  the distance matrix between text samples

      1. ``````python
         distance_key_word = 1 - calculate_sim_pair(tfidf, tfidf) # dimension is (N,N)
         distance_meaning_word = 1 - calculate_sim_pair(sample_case_vec, sample_case_vec) # dimension is (N,N)
         print(distance_key_word.shape)
         print(distance_meaning_word.shape)
         ``````

      2. the reason why we use 1 - sim_pair is because if the similarity higher, the distance is lower. If the similarity is 1, then the distance is 0. It also return the distance between each text. For example, the element in row m, column n is actually the distance between m text and n text. Then we can find similar text and we can also use algorithm such as k-means to classify the text

2. Because in this project, I use two method: one is TF-IDF, another one is Sentence Embedding. We combine these two method together to see whether it can produce a better outcome. We use different ratio and calculate the final sum distance `sum_of_distance` in order to do the text clustering.

   1. `````python
      def generate_sum_of_distance(distance_key_word, distance_meaning_word, key_word_ratio):
          meaning_word_ratio = 1 - key_word_ratio
          sum_of_disatance = meaning_word_ratio* distance_meaning_word + key_word_ratio * distance_key_word

          median_value_ignore_nan = np.median(sum_of_disatance)
          return sum_of_disatance
      `````

3. After we get different combinations of two methods, we also need to decide parameter (`distance_threshold`) of `AgglomerativeClustering`. In this case, we are not using k-means, but using `AgglomerativeClustering`. If  `distance_threshold` is large, then it will put many different text into one group. If it is too small, then we will have lots of group. Therefore, we need to test different kind of `distance_threshold`. By doing this, we are actually choosing the quantile of `sum_of_disatance`.

   1. ``````python
      def get_new_df(sum_of_distance, quantile_percentage_lst, ratio) -> pd.DataFrame:
          sum_of_disatance_1d = sum_of_disatance.flatten()

          for k in quantile_percentage_lst:
              percentile = np.percentile(sum_of_disatance_1d, k)
              # print(train_cluster(sum_of_disatance)) #KMeans(n_clusters=5)

              cluster = AgglomerativeClustering(n_clusters=None, distance_threshold=percentile, metric='precomputed',
                                                linkage="average")

              cluster.fit(sum_of_disatance)
              predicted_label = cluster.labels_

              data['label'] = predicted_label
              return data
      ``````



### How can we choose the model

1. Gini index

   1. The definition of Gini index is in a meaning group, the discreteness of the label. If the Gini index is low, it means that same label is spread out in different group. It is not a good clustering quality.

   2. The definition o Inverse Gini index is in a same label group, the discreteness of the meaning. Also same as Gini index.

   3. The formula of Gini index is $G = 1 - \sum (p_i^2)$, after calculating this index, we can also calculating the weighted Gini index, and inverse Gini index

   4. ```````python
      def check_gini(df: pd.DataFrame, gini_lst, inverse_gini_lst):
          df_label_ratio_under_intent = df.groupby("意图名称").apply(
              lambda df_i: 1 - ((df_i["label"].value_counts() / df_i.shape[0]) ** 2).sum())

          df_intet_ratio = df["意图名称"].value_counts() / df.shape[0]

          gini_index = (df_intet_ratio * df_label_ratio_under_intent).sum()
          gini_lst.append(gini_index)

          df_label_ratio_under_intent = df.groupby("label").apply(
              lambda df_i: 1 - ((df_i["意图名称"].value_counts() / df_i.shape[0]) ** 2).sum())

          df_intet_ratio = df["label"].value_counts() / df.shape[0]

          inverse_gini_index = (df_intet_ratio * df_label_ratio_under_intent).sum()
          inverse_gini_lst.append(inverse_gini_index)


          print(gini_lst)
          print(inverse_gini_lst)
          return gini_lst, inverse_gini_lst
      ```````

   5. Then by observing the intersection of the graph of Gini index and inverse Gini index, we can find an optimal clustering trade-off point. This intersection can be used to determine the optimal clustering parameters.

​
