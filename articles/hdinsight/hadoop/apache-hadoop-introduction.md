---
title: Apache Hadoop ve MapReduce-Azure HDInsight nedir?
description: HDInsight 'a giriş ve Apache Hadoop teknoloji yığını ve bileşenleri.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: ad1f7422919f224889db84a2599ad3c1c48efcc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863819"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Hadoop nedir?

Kümelerde büyük veri kümelerinin dağıtılmış işlenmesi ve analizine yönelik ilk açık kaynak çerçeve [Apache Hadoop](https://hadoop.apache.org/)’tu. Hadoop ekosistemi Apache Hive, Apache HBase, Spark, Kafka ve diğer birçok tane dahil olmak üzere ilgili yazılım ve yardımcı programları içerir.

Azure HDInsight, kuruluşlar için bulutta tam olarak yönetilen, tam bir açık kaynaklı analiz hizmetidir. Azure HDInsight 'taki Apache Hadoop kümesi türü, toplu verileri paralel olarak işlemek ve analiz etmek için [Apache Hadoop Dağıtılmış dosya sistemi (bir)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) [Apache Hadoop, Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) kaynak yönetimini ve basit bir [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) programlama modelini kullanmanıza olanak sağlar.  HDInsight 'ta Hadoop kümeleri [Azure Blob depolama](../../storage/common/storage-introduction.md), [Azure Data Lake Storage 1.](../../data-lake-store/data-lake-store-overview.md)veya [Azure Data Lake Storage 2.](../../storage/blobs/data-lake-storage-introduction.md)uyumludur.

HDInsight üzerindeki kullanılabilir Hadoop teknolojisi yığını bileşenlerini görmek için, bkz. [HDInsight ile sağlanan bileşenler ve sürümler](../hdinsight-component-versioning.md). HDInsight'ta Hadoop hakkında daha fazla bilgi edinmek için bkz. [HDInsight için Azure özellikleri sayfası](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>MapReduce nedir?

[MapReduce Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) , çok miktarda veriyi işleyen işleri yazmaya yönelik bir yazılım çerçevesidir. Giriş verileri bağımsız parçalara bölünür. Her bir öbek, kümenizdeki düğümler arasında paralel olarak işlenir. MapReduce işi iki işlevden oluşur:

* **Eşleyici**: giriş verilerini kullanır, analiz eder (genellikle filtre ve sıralama işlemleriyle birlikte) ve tanımlama gruplarını yayar (anahtar-değer çiftleri)

* **Reducer**: Eşleyici tarafından yayılan başlıkları kullanır ve Eşleyici verilerinden daha küçük ve birleştirilmiş bir sonuç oluşturan bir Özet işlem gerçekleştirir

Temel bir sözcük sayısı MapReduce iş örneği aşağıdaki diyagramda gösterilmiştir:

 :::image type="content" source="./media/apache-hadoop-introduction/hdi-word-count-diagram.gif" alt-text="HDI. WordCountDiagram" border="true":::

Bu işin çıkışı, metinde her bir sözcüğün kaç kez oluştuğunu gösteren bir saydır.

* Eşleyici her satırı giriş metinden girdi olarak alır ve sözcüklere ayırır. Bir sözcüğün her oluşması durumunda 1 ' in ardından bir anahtar/değer çifti yayar. Çıktı, Reducer 'e gönderilmeden önce sıralanır.
* Reducer, her sözcük için bu ayrı sayıları toplar ve sonra tekrarlarının toplamına göre, sözcüğü içeren tek bir anahtar/değer çifti yayar.

MapReduce, çeşitli dillerde uygulanabilir. Java en yaygın uygulamasıdır ve bu belgede tanıtım amacıyla kullanılır.

## <a name="development-languages"></a>Geliştirme dilleri

Java ve Java Sanal Makinesi temel alan diller veya çerçeveler doğrudan [MapReduce işi](..//hadoop/submit-apache-hadoop-jobs-programmatically.md)olarak çalıştırılabilir. Bu belgede kullanılan örnek bir Java MapReduce uygulamasıdır. C#, Python veya tek başına yürütülebilir dosyalar gibi Java olmayan diller **Hadoop akışını** kullanmalıdır.

Hadoop akışı Eşleyici ve Reducer üzerinden STDıN ve STDOUT üzerinden iletişim kurar. Eşleyici ve Reducer verileri STDIN 'den bir kerede bir satıra okur ve çıktıyı STDOUT 'a yazar. Eşleyici ve Reducer tarafından okunan veya yayılan her satır, bir sekme karakteriyle ayrılmış bir anahtar/değer çifti biçiminde olmalıdır:

`[key]\t[value]`

Daha fazla bilgi için bkz. [Hadoop akışı](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

HDInsight ile Hadoop akışını kullanma örnekleri için aşağıdaki belgeye bakın:

* [C# MapReduce işleri geliştirme](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="where-do-i-start"></a>Nereden başlayabilirim?

* [Hızlı başlangıç: Azure portal kullanarak Azure HDInsight 'ta Apache Hadoop kümesi oluşturma](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Öğretici: HDInsight 'ta Apache Hadoop işleri gönderme](../hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight üzerinde Apache Hadoop için Java MapReduce programları geliştirme](../hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Ayıklama, dönüştürme ve yükleme (ETL) aracı olarak Apache Hive kullanma](../hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Ölçeklendirerek ayıklama, dönüştürme ve yükleme (ETL)](../hadoop/apache-hadoop-etl-at-scale.md)
* [Veri analizi işlem hattını kullanıma hazır hale getirme](../hdinsight-operationalize-data-pipeline.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Portalı kullanarak HDInsight 'ta Apache Hadoop kümesi oluşturma](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [ARM şablonunu kullanarak HDInsight 'ta Apache Hadoop kümesi oluşturma](../hadoop/apache-hadoop-linux-tutorial-get-started.md)