---
layout: post
title: Leetcode 2114. Maximum Number of Words Found in Sentences
categories: [Leetcode, Challenge]
tags: [leetcode, challenge, easy]
date: 2024-05-02 12:03 +0700
---
[_Leet code problem 2114_](https://leetcode.com/problems/maximum-number-of-words-found-in-sentences/description/)\
_Difficulty: Easy_
---
## Solution
This is pretty straight forward.
- You can just count the number of words `text.split(" ")`
- Or count the spaces between letters because the question already mentioned
that there will be no trailing/leading spaces and separated by a single space character

**Implementation**\
Code from [_github_](https://github.com/nguyentaijs/Leetcode/blob/main/src/L2418_SortThePeople.java)
```java
public class L2114_MaxNumberOfWordsFoundInSentences {
  /***
   * Using Java builtin function split() to count words
   * @param sentences
   * @return
   */
  public int mostWordsFound(String[] sentences) {
    int maxCount = 0;
    for(int i = 0; i < sentences.length; i++) {
      int length = sentences[i].split(" ").length;
      if (length > maxCount) {
        maxCount = length;
      }
    }
    return maxCount;
  }

  /***
   * Faster by counting the spaces
   * @param sentences
   * @return
   */
  public int mostWordsFound_2ndApproach(String[] sentences) {
    if (sentences.length == 0) {
      return 0;
    }
    int maxCount = 0;
    for(int i = 0; i < sentences.length; i++) {
      int length = countSpaces(sentences[i]);
      if (length > maxCount) {
        maxCount = length;
      }
    }
    return ++maxCount;
  }

  private int countSpaces(String input) {
    int count = 0;
    for (int i = 0; i < input.length(); i++) {
      if (input.charAt(i) == ' ') {
        count++;
      }
    }
    return count;
  }
}
```




