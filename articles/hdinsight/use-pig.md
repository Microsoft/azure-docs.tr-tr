---
title: Apache Pig kullanma
titleSuffix: Azure HDInsight
description: HDInsight 'ta Apache Hadoop ile Pig kullanmayı öğrenin.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 4cbb7b96610a56f3b6049038bb5c9c6bc0870b57
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871372"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>HDInsight üzerinde Apache Hadoop Apache Pig kullanma

HDInsight ile [Apache Pig](https://pig.apache.org/) kullanmayı öğrenin.

Apache Pig, *Pig Latin* olarak bilinen bir yordamsal dil kullanarak Apache Hadoop için program oluşturmaya yönelik bir platformdur. Pig, *MapReduce* çözümlerini oluşturmak için Java 'ya alternatiftir ve Azure HDInsight 'a dahildir. HDInsight ile Pig 'in kullanılabileceği çeşitli yolları öğrenmek için aşağıdaki tabloyu kullanın:

## <a name="why-use-apache-pig"></a><a id="why"></a>Apache Pig neden kullanılmalıdır?

Hadoop 'ta MapReduce kullanarak veri işleme güçlüklerinden biri, yalnızca bir eşlem ve bir azaltma işlevi kullanarak işlem mantığınızı uygumaktır. Karmaşık işleme için, genellikle işleme, istenen sonuca ulaşmak için zincirleme olan birden çok MapReduce işlemlerine bir araya kesmeniz gerekir.

Pig, istenen çıktıyı üretmek için verilerin akışını yerine geçen bir dizi dönüştürme olarak tanımlamanızı sağlar.

Pig Latin Dili, istenen çıktıyı oluşturmak için bir veya daha fazla dönüşümle ham girişten veri akışını açıklamanıza olanak sağlar. Pig Latin programları bu genel kalıbı izler:

* **Load**: dosya sisteminden değiştirilecek verileri okuyun.

* **Dönüştürme**: verileri işleme.

* **Döküm veya mağaza**: verileri ekranda çıktı olarak veya işlenmek üzere depolar.

### <a name="user-defined-functions"></a>Kullanıcı tanımlı işlevler

Pig Latin Ayrıca, Pig Latin 'de modelleşmesi zor olan mantığı uygulayan dış bileşenleri çağırmada izin veren kullanıcı tanımlı işlevleri (UDF) destekler.

Pig Latin hakkında daha fazla bilgi için bkz. [Pig Latin başvurusu el ile 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) ve [Pig Latin başvurusu el ile 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Örnek veriler

HDInsight, ve dizinlerinde depolanan çeşitli örnek veri kümeleri sağlar `/example/data` `/HdiSamples` . Bu dizinler, kümeniz için varsayılan depolardır. Bu belgedeki Pig örneği, öğesinden *Log4J* dosyasını kullanır `/example/data/sample.log` .

Dosya içindeki her bir günlük, `[LOG LEVEL]` türü ve önem derecesini gösteren bir alan içeren alanlardan oluşur, örneğin:

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

Önceki örnekte, günlük düzeyi HATADıR.

> [!NOTE]  
> [Apache Log4J](https://en.wikipedia.org/wiki/Log4j) Logging aracını kullanarak da bir Log4J dosyası oluşturabilir ve sonra bu dosyayı bloba karşıya yükleyebilirsiniz. Yönergeler için bkz. [HDInsight 'A veri yükleme](hdinsight-upload-data.md) . Azure depolama 'daki Blobların HDInsight ile nasıl kullanıldığı hakkında daha fazla bilgi için bkz. [HDInsight Ile Azure Blob depolamayı kullanma](hdinsight-hadoop-use-blob-storage.md).

## <a name="example-job"></a><a id="job"></a>Örnek iş

Aşağıdaki Pig Latin işi, `sample.log` dosyayı HDInsight kümeniz için varsayılan depolamadan yükler. Ardından, giriş verilerinde her bir günlük düzeyinin kaç kez oluştuğunu belirten bir dizi dönüştürme gerçekleştirir. Sonuçlar STDOUT 'a yazılır.

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

Aşağıdaki görüntüde, her dönüştürmenin veriye ne kadar yaptığı hakkında bir Özet gösterilmektedir.

:::image type="content" source="./media/use-pig/hdi-data-transformation.gif" alt-text="Dönüşümlerinin grafik gösterimi" border="false":::

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Pig Latin işini çalıştırma

HDInsight, çeşitli yöntemler kullanarak Pig Latin işleri çalıştırabilir. Size hangi yöntemin doğru olduğuna karar vermek için aşağıdaki tabloyu kullanın, ardından bir izlenecek yol için bağlantıyı izleyin.

## <a name="pig-and-sql-server-integration-services"></a>Pig ve SQL Server Integration Services

Bir Pig işini çalıştırmak için SQL Server Integration Services (SSSıS) kullanabilirsiniz. SSIS için Azure Özellik paketi, HDInsight üzerinde Pig işlerinde çalışan aşağıdaki bileşenleri sağlar.

* [Azure HDInsight Pig görevi][pigtask]

* [Azure aboneliği bağlantı Yöneticisi][connectionmanager]

SSIS için Azure Feature Pack hakkında daha fazla bilgi [edinin.][ssispack]

## <a name="next-steps"></a><a id="nextsteps"></a>Sonraki adımlar

HDInsight ile Pig kullanmayı öğrendiğinize göre, Azure HDInsight ile çalışmanın diğer yollarını araştırmak için aşağıdaki bağlantıları kullanın.

* [HDInsight'a veri yükleme](hdinsight-upload-data.md)
* [HDInsight ile Apache Hive kullanma](./hadoop/hdinsight-use-hive.md)
* [HDInsight ile Apache Sqoop kullanma](./hadoop/hdinsight-use-sqoop.md)
* [HDInsight ile MapReduce işleri kullanma](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: /sql/integration-services/control-flow/azure-hdinsight-pig-task?viewFallbackFrom=sql-server-2014
[connectionmanager]: /sql/integration-services/connection-manager/azure-subscription-connection-manager?viewFallbackFrom=sql-server-2014
[ssispack]: /sql/integration-services/azure-feature-pack-for-integration-services-ssis?viewFallbackFrom=sql-server-2014
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


