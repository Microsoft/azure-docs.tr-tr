---
title: HDInsight üzerinde Apache Hadoop MapReduce
description: HDInsight kümelerinde Apache Hadoop Apache MapReduce işlerini nasıl çalıştıracağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 33d96262ca2e2ff3003fbf2b40ce9ceb496337ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944262"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>HDInsight üzerinde Apache Hadoop ile MapReduce'u kullanma

HDInsight kümelerinde MapReduce işlerinin nasıl çalıştırılacağını öğrenin.

## <a name="example-data"></a>Örnek veriler

HDInsight, ve dizininde depolanan çeşitli örnek veri kümeleri sağlar `/example/data` `/HdiSamples` . Bu dizinler, kümeniz için varsayılan depolardır. Bu belgede, `/example/data/gutenberg/davinci.txt` dosyasını kullanırız. Bu dosya, Leonardo da Vinci not defterlerini içerir.

## <a name="example-mapreduce"></a>Örnek MapReduce

HDInsight kümenize örnek bir MapReduce sözcük sayısı uygulaması dahildir. Bu örnek, `/example/jars/hadoop-mapreduce-examples.jar` kümenizin varsayılan depolama alanında bulunur.

Aşağıdaki Java kodu, dosyasında bulunan MapReduce uygulamasının kaynağıdır `hadoop-mapreduce-examples.jar` :

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Kendi MapReduce uygulamalarınızı yazma yönergeleri için bkz. [HDInsight Için Java MapReduce uygulamaları geliştirme](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

## <a name="run-the-mapreduce"></a>MapReduce 'yi çalıştırma

HDInsight, çeşitli yöntemler kullanarak HiveQL işleri çalıştırabilir. Size hangi yöntemin doğru olduğuna karar vermek için aşağıdaki tabloyu kullanın, ardından bir izlenecek yol için bağlantıyı izleyin.

| **Bunu kullan**... | **... Bunu yapmak için** |  ... Bu **istemci işletim sisteminden** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |**SSH** kullanarak Hadoop komutunu kullanma |Linux, Unix, Mac OS X veya Windows |
| [Kıvr](apache-hadoop-use-mapreduce-curl.md) |**Rest** kullanarak işi uzaktan gönderme |Linux, Unix, Mac OS X veya Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |**Windows PowerShell** kullanarak işi uzaktan gönderme  |Windows |

## <a name="next-steps"></a>Sonraki adımlar

HDInsight 'ta verilerle çalışma hakkında daha fazla bilgi edinmek için aşağıdaki belgelere bakın:

* [HDInsight için Java MapReduce programları geliştirme](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [HDInsight ile Apache Hive kullanma](./hdinsight-use-hive.md)
