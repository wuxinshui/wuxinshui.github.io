---
layout: post
title: Word2vec java实战
category: NLP-自然语言处理 
tags: [NLP-自然语言处理]
---

>在学习了[word2vec](https://my.oschina.net/wuxinshui/blog/3133173)的牛逼后，开始进入实战，解决问题了。


## 前言

在学习了[word2vec](https://my.oschina.net/wuxinshui/blog/3133173)的牛逼后，开始进入实战，解决问题了。

## 实战

### 添加依赖

```
        <dependency>
            <groupId>com.medallia.word2vec</groupId>
            <artifactId>word2vecjava_2.11</artifactId>
            <version>1.0-ALLENAI-4</version>
        </dependency>
```

### 训练模型

由于语料比较小，各项参数，都调小了。

```
@Service
@Slf4j
public class Word2vecService {

    public Word2VecModel train() {
        try {
            List<String> data = List.of("anarchism originated as a term of abuse first used against early working class radicals including the diggers of the english anarchism originated as a term of abuse first");
            List list = Lists.transform(data, var11 -> Arrays.asList(var11.split(" ")));
            Word2VecModel word2VecModel = Word2VecModel.trainer().setMinVocabFrequency(1).useNumThreads(4).setWindowSize(1).type(NeuralNetworkType.CBOW).setLayerSize(12).useNegativeSamples(5).setDownSamplingRate(1.0E-4D).setNumIterations(5).setListener((var1, var2) -> System.out.println(String.format("%s is %.2f%% complete", Format.formatEnum(var1), var2 * 100.0D))).train(list);
            return word2VecModel;
        } catch (InterruptedException e) {
            log.error("exception:{}", e);
            return null;
        }
    }

}
```

### 验证模型

`curl http://localhost:8080/w2v/model`

```
{"vocab":["of","a","abuse","anarchism","as","first","originated","term","the","against","class","diggers","early","english","including","radicals","used","working"]}
```

`curl http://localhost:8080/w2v/matches?name=originated`

```
[{"originated":1.0},{"used":0.31949775120966817},{"against":0.2685951628434606},{"working":0.243240735844125},{"first":0.21188438070768922},{"as":0.14340927658891997},{"anarchism":0.14088247689095754},{"a":0.11150486710031617},{"early":-0.03650820548138082},{"radicals":-0.08134581648415767}]R
```