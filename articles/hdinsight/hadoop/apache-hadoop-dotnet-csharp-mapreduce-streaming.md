---
title: HDInsight C# 'ta Hadoop 'Ta MapReduce ile birlikte kullanma-Azure
description: Azure HDInsight 'ta Apache Hadoop C# MapReduce çözümleri oluşturmak için kullanmayı öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: hrasheed
ms.openlocfilehash: 1cdf029d296bd6ff11b6531cd47dc6a7fd3163c3
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930273"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>HDInsight C# 'Ta Apache Hadoop MapReduce streaming ile kullanma

HDInsight 'ta MapReduce çözümü oluşturmak için kullanmayı C# öğrenin.

> [!IMPORTANT]
> Linux, HDInsight sürüm 3.4 ve üzerinde kullanılan tek işletim sistemidir. Daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop bileşenleri](../hdinsight-component-versioning.md).

Apache Hadoop akışı, MapReduce işlerini bir betik veya yürütülebilir dosya kullanarak çalıştırmanıza olanak tanıyan bir yardımcı programdır. Bu örnekte, bir sözcük sayısı çözümü için Mapper ve Reducer uygulamak üzere .NET kullanılır.

## <a name="net-on-hdinsight"></a>HDInsight üzerinde .NET

*Linux tabanlı HDInsight* kümeleri, .NET uygulamalarını çalıştırmak için [Mono (https://mono-project.com)](https://mono-project.com) kullanır. Tek bir sürüm 4.2.1, HDInsight sürüm 3,6 ' ye dahildir. HDInsight 'ta bulunan mono sürümü hakkında daha fazla bilgi için bkz. [farklı HDInsight sürümleriyle kullanılabilen Apache Hadoop bileşenleri](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions). 

.NET Framework sürümleriyle mono uyumluluğu hakkında daha fazla bilgi için bkz. [mono uyumluluk](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Hadoop akışı nasıl kullanılır?

Bu belgede akış için kullanılan temel işlem aşağıdaki gibidir:

1. Hadoop, STDIN 'de verileri eşleştiriciyle (Bu örnekteki*Mapper. exe* ) geçirir.
2. Eşleyici, verileri işler ve STDOUT için sekmeyle ayrılmış anahtar/değer çiftlerini yayar.
3. Çıktı, Hadoop tarafından okunarak (Bu örnekte*Reducer. exe* ) Reducer 'e geçirilir.
4. Reducer, sekmeyle ayrılmış anahtar/değer çiftlerini okur, verileri işler ve sonra sonucu STDOUT üzerinde sekmeyle ayrılmış anahtar/değer çiftleri olarak yayar.
5. Çıktı, Hadoop tarafından okunmakta ve çıkış dizinine yazılır.

Akış hakkında daha fazla bilgi için bkz. [Hadoop akışı](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio.

* .NET Framework 4,5 ' i hedefleyen kod C# yazma ve oluşturma hakkında bir benzerlik.

* . Exe dosyalarını kümeye yüklemek için bir yol. Bu belgedeki adımlarda, dosyaları küme için birincil depolamaya yüklemek üzere Visual Studio için Data Lake araçları kullanılır.

* Azure PowerShell veya bir Secure Shell (SSH) istemcisi.

* HDInsight kümesi üzerinde Hadoop. Küme oluşturma hakkında daha fazla bilgi için bkz. [HDInsight kümesi oluşturma](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Eşleyici oluşturma

Visual Studio 'da *Eşleyici*adlı yeni bir .NET Framework konsol uygulaması oluşturun. Uygulama için aşağıdaki kodu kullanın:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Uygulamayı oluşturduktan sonra, */bin/Debug/Mapper.exe* dosyasını proje dizininde oluşturmak için oluşturun.

## <a name="create-the-reducer"></a>Reducer oluşturma

Visual Studio 'da, *Reducer*adlı yeni bir .NET Framework konsol uygulaması oluşturun. Uygulama için aşağıdaki kodu kullanın:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Uygulamayı oluşturduktan sonra, */bin/Debug/Reducer.exe* dosyasını proje dizininde oluşturmak için oluşturun.

## <a name="upload-to-storage"></a>Depolama alanına yükleme

Ardından, *Eşleyici* ve *Reducer* uygulamalarını HDInsight depolama alanına yüklemeniz gerekir.

1. Visual Studio 'da > Sunucu Gezgini **görüntüle** 'yi seçin.

2. **Azure** seçeneğini ve sonra **HDInsight** seçeneğini genişletin.

3. İstenirse, Azure aboneliği kimlik bilgilerinizi girin ve **oturum aç**' ı seçin.

4. Bu uygulamayı dağıtmak istediğiniz HDInsight kümesini genişletin. Metin **(varsayılan depolama hesabı)** içeren bir giriş listelenir.

   ![Depolama hesabı, HDInsight kümesi, Sunucu Gezgini, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * **(Varsayılan depolama hesabı)** girişi genişletilirse, küme için varsayılan depolama alanı olarak bir **Azure depolama hesabı** kullanıyorsunuz demektir. Küme için varsayılan depolamada bulunan dosyaları görüntülemek için, girdiyi genişletin ve **(varsayılan kapsayıcı)** öğesine çift tıklayın.

   * **(Varsayılan depolama hesabı)** girdisi genişletilirse, küme için varsayılan depolama alanı olarak **Azure Data Lake Storage** kullanırsınız. Küme için varsayılan depolamada bulunan dosyaları görüntülemek için **(varsayılan depolama hesabı)** girişine çift tıklayın.

5. . Exe dosyalarını karşıya yüklemek için aşağıdaki yöntemlerden birini kullanın:

    * Bir **Azure depolama hesabı**kullanıyorsanız, **karşıya yükleme blobu** simgesini seçin. 

        ![Eşleyici için HDInsight karşıya yükleme simgesi, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        **Yeni dosyayı karşıya yükle** Iletişim kutusundaki **dosya adı**altında, **Araştır**' ı seçin. **Blobu karşıya yükle** iletişim kutusunda *Eşleyici* projesi için *bin\Debug* klasörüne gidin ve ardından *Mapper. exe* dosyasını seçin. Son olarak, **Aç** ' ı ve ardından **Tamam** ' ı seçerek karşıya yüklemeyi tamamlayabilirsiniz. 

    * **Azure Data Lake Storage**için, dosya listesinde boş bir alana sağ tıklayın ve ardından **karşıya yükle**' yi seçin. Son olarak, *Mapper. exe* dosyasını seçin ve sonra **Aç**' ı seçin.

    *Eşleyici. exe* karşıya yükleme işlemi tamamlandıktan sonra, *Reducer. exe* dosyası için karşıya yükleme işlemini tekrarlayın.

## <a name="run-a-job-using-an-ssh-session"></a>İş çalıştırma: SSH oturumu kullanma

Aşağıdaki yordam bir SSH oturumu kullanarak MapReduce işinin nasıl çalıştırılacağını açıklamaktadır:

1. HDInsight kümesine bağlanmak için SSH kullanın. (Örneğin, `ssh sshuser@<clustername>-ssh.azurehdinsight.net`komutunu çalıştırın.) Daha fazla bilgi için bkz. [HDInsight Ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. MapReduce işini başlatmak için aşağıdaki komutlardan birini kullanın:

   * Varsayılan depolama alanı **Azure depolama**ise:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasb:///mapper.exe,wasb:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Varsayılan depolama **Data Lake Storage 1.** :

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Varsayılan depolama **Data Lake Storage 2.** :

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   Aşağıdaki listede her bir parametre ve seçeneğin neyi gösterdiği açıklanmaktadır:

   * *Hadoop-streaming. jar*: akış MapReduce işlevini içeren jar dosyasını belirtir.
   * `-files`: Bu iş için *Mapper. exe* ve *Reducer. exe* dosyalarını belirtir. Her dosyadan önce `wasb:///`, `adl:///`veya `abfs:///` protokol bildirimi, kümenin varsayılan depolama kökünün yoludur.
   * `-mapper`: Eşleyici 'yi uygulayan dosyayı belirtir.
   * `-reducer`: Reducer uygulayan dosyayı belirtir.
   * `-input`: giriş verilerini belirtir.
   * `-output`: çıkış dizinini belirtir.

3. MapReduce işi tamamlandığında, sonuçları görüntülemek için aşağıdaki komutu kullanın:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   Aşağıdaki metin, bu komutun döndürdüğü verilerin bir örneğidir:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>İş çalıştırma: PowerShell kullanma

Bir MapReduce işini çalıştırmak ve sonuçları indirmek için aşağıdaki PowerShell betiğini kullanın.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Bu betik, HDInsight küme adı ile birlikte küme oturum açma hesabı adı ve parolasını ister. İş tamamlandıktan sonra çıktı *çıktı. txt*adlı bir dosyaya indirilir. Aşağıdaki metin `output.txt` dosyadaki verilerin bir örneğidir:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Sonraki adımlar

HDInsight ile MapReduce kullanma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop MapReduce](hdinsight-use-mapreduce.md)kullanma.

Hive ve Pig ile C# kullanma hakkında bilgi için bkz. [Apache Hive ve C# Apache Pig Ile Kullanıcı tanımlı bir işlev kullanma](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

HDInsight 'ta fırtınası ile C# kullanma hakkında bilgi için bkz. [HDInsight C# 'Ta Apache Storm için topolojiler geliştirme](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
