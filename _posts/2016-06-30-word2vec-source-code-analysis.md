---
layout: post
title: word2vec源码阅读-词典的建立
description: ""
category: machine learning
avatarimg: "/img/head.png"
tags : [word2vec]
duoshuo: true
---

word2vec采用数组的形式存储词典，在词典中查找某个词采用hash的形式，将字符串映射到数组的下标。  
下面先给出词典的基本数据结构

{% highlight cpp %}
struct vocab_word {
  long long cn;
  int *point;
  char *word, *code, codelen;
};

const int vocab_hash_size = 30000000;
struct vocab_word *vocab;
int *vocab_hash;
long long vocab_max_size = 1000, vocab_size = 0;
{% endhighlight %}

首先，开设一个长度是vocab_max_size的数组，用以记录词语信息；  
开设一个长度是vocab_hash_size的整形数据vocab_hash，保存词语和hash值的对应关系，并将每个分量初始化为-1。  
当有新的词语加入词典时，调用下面的函数。

{% highlight cpp %}
int AddWordToVocab(char *word) {
  unsigned int hash, length = strlen(word) + 1;
  if (length > MAX_STRING) length = MAX_STRING;
  vocab[vocab_size].word = (char *)calloc(length, sizeof(char));
  strcpy(vocab[vocab_size].word, word);
  vocab[vocab_size].cn = 0;
  vocab_size++;
  // Reallocate memory if needed
  if (vocab_size + 2 >= vocab_max_size) {
    vocab_max_size += 1000;
    vocab = (struct vocab_word *)realloc(vocab, vocab_max_size * sizeof(struct vocab_word));
  }
  hash = GetWordHash(word);
  while (vocab_hash[hash] != -1) hash = (hash + 1) % vocab_hash_size;
  vocab_hash[hash] = vocab_size - 1;
  return vocab_size - 1;
}
{% endhighlight %}