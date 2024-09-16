# CMPS 6610  Lab 03

**Name (Team Member 1):**________Yan Zhu_________________  
**Name (Team Member 2):**________Yongbo Chen_________________


In this lab you will practice using the `map` and `reduce` functions. These functions are commonly used together in a `map-reduce` framework, used by Google and others to parallelize and scale common computations.


## Part 1: Counting Words

In the first part, we will use map-reduce to count how often each word appears in a sequence of documents. E.g. if the input is two documents:

```python
['i am sam i am', 'sam is ham']
```

then the output should be

```python
[('am', 2), ('ham', 1), ('i', 2), ('is', 1), ('sam', 2)]
```

We have given you the implementation of the main map-reduce logic
```python
def run_map_reduce(map_f, reduce_f, docs)
```

To use this function to count words, you'll need to implement your own `map_f` and `reduce_f` functions, described below.

1. Complete `word_count_map` and test it with `test_word_count_map`.

2. Complete `word_count_reduce` and test it with `test_word_count_reduce`.

3. If the above are correct, then you should now be able to test it the full solution `test_word_count`

4. Assume that a word `w` appears `n` times. What is the **work** and **span** of `word_count_reduce` for this word, assuming a parallel implementation of the `reduce` function?


**Enter answer here**

Work

Assuming we have an input: ('w', [1, 1, 1, ..., 1]), where w appears n times, and we need to use the reduce function to sum all the 1s and finally return ('w', n). The work is the total number of operations performed in this process. Since we are summing n values, the work involves a total of n-1 addition operations.

Thus, the work is  O(n) .

Span

In a parallel reduce operation, each step halves the remaining elements. For example, in the first step, the n elements are reduced to n/2 elements, then in the second step, those are reduced to n/4, and this process continues until only one element remains. The number of steps required to reduce the elements is logarithmic.

Thus, the span is  $O(log n)$.

5. Why are we going through all this trouble? Couldn't I just use this function to count words?

```python
docs = ['i am sam i am', 'sam is ham']
counts = {}
for doc in docs:
    for term in doc.split():
        counts[term] = counts.get(term, 0) + 1
# counts = {'i': 2, 'am': 2, 'sam': 2, 'is': 1, 'ham': 1}
```

What is the problem that prevents us from easily parallelizing this solution?

**Enter answer here**

The problem of above function is it can only handle small-scale computations, and it has no advantage for parallel computing. In terms of processing a large number of files, it will consume a lot of time, which is inefficient.

Besides, another issue arises with the counts' dictionary, which is used to store all the counts. In a parallel environment, it will cause problems if multiple processes access it simultaneously, potentially leading to errors. In such cases, locking would be required. However, locking means that only one thread can update the count at a time, which defeats the purpose of parallelization.

Additionally, the for doc in docs loop causes files to be processed sequentially, which prevents parallel execution. Effective parallelization requires breaking the problem into independent small tasks that can be executed concurrently without interfering with each other. But in this for loop, the tasks are tightly coupled to the shared counts' dictionary, severely impacting parallelization performance.

## Part 2: Sentiment analysis

Finally, we'll adapt our approach above to perform a simple type of sentiment analysis. Given a document, rather than counting words, we will instead count the number of positive and negative terms in the document, given a predefined list of terms. E.g., if the input sentence is `it was a terrible waste of time` and the terms `terrible` and `waste` are in our list of negative terms, then the output is

`[('negative', 1), ('negative', 1)]`

6. Complete the `sentiment_map` function to implement the above idea and test it with `test_sentiment_map`.

7. Since the output here is similar to the word count problem, we will reuse `word_count_reduce` to compute the total number of positive and negative terms in a sequence of documents. Confirm your results work by running `test_sentiment`.
