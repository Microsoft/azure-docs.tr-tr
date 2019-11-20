---
title: Özel MapReduce programlarını çalıştırma-Azure HDInsight
description: Azure HDInsight kümelerinde özel Apache MapReduce programlarını ne zaman ve nasıl çalıştıracağınızı öğrenin.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 305eefbaa674e414ab8134986e6cd526abe8208e
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810749"
---
# <a name="run-custom-mapreduce-programs"></a>Özel MapReduce programlarını çalıştırma

HDInsight gibi Apache Hadoop tabanlı büyük veri sistemleri, çok çeşitli araçlar ve teknolojiler kullanarak veri işlemeyi etkinleştirir. Aşağıdaki tabloda her biri için başlıca avantajlar ve noktalar açıklanmaktadır.

| Sorgu mekanizması | Yararları | Dikkat edilmesi gerekenler |
| --- | --- | --- |
| **HiveQL kullanarak Apache Hive** | <ul><li>Veri özetlemesi ve isteğe bağlı sorgulama için, büyük miktarlarda sabit verilerin toplu işleme ve analizine yönelik mükemmel bir çözümdür. Tanıdık bir SQL benzeri sözdizimi kullanır.</li><li>Bu, kolayca bölümlenebilir ve dizine alınmış kalıcı veri tabloları oluşturmak için kullanılabilir.</li><li>Aynı verilerden birden çok dış tablo ve görünüm oluşturulabilir.</li><li>Veri depolama ve işleme için büyük ölçüde genişleme ve hataya dayanıklı yetenekler sağlayan basit bir veri ambarı uygulamasını destekler.</li></ul> | <ul><li>Kaynak verilerinde en az bir tanımlanabilir yapıya sahip olmasını gerektirir.</li><li>Gerçek zamanlı sorgular ve satır düzeyi güncelleştirmeler için uygun değildir. En iyi yöntem, büyük veri kümeleri üzerinde toplu iş işleri için kullanılır.</li><li>Karmaşık işlem görevlerinin bazı türlerini gerçekleştiremeyebilir.</li></ul> |
| **Pig Latin kullanarak Apache Pig** | <ul><li>Verileri kümeler halinde işlemek, veri kümelerini birleştirmek ve filtrelemek, kayıtlar veya kayıt gruplarına işlevler uygulamak ve sütunları tanımlayarak, değerleri gruplayarak veya sütunları satırlara dönüştürerek yeniden yapılandırma için harika bir çözümdür.</li><li>Veri üzerinde işlem dizisi olarak iş akışı tabanlı bir yaklaşım kullanabilir.</li></ul> | <ul><li>SQL kullanıcıları Pig, HiveQL 'tan daha az tanıdık ve kullanımı daha zor olabilir.</li><li>Varsayılan çıktı genellikle bir metin dosyasıdır ve bu nedenle Excel gibi görselleştirme araçlarıyla kullanılması daha zor olabilir. Genellikle çıkış üzerine bir Hive tablosu katmanlanıyor olursunuz.</li></ul> |
| **Özel harita/azaltma** | <ul><li>Eşleme üzerinde tam denetim sağlar ve aşamaları ve yürütmeyi azaltır.</li><li>Sorguların, kümeden en yüksek performansı elde etmek veya sunucular ve ağ üzerindeki yükü en aza indirmek için iyileştirilme olanağı sağlar.</li><li>Bileşenler bir dizi iyi bilinen dilde yazılabilir.</li></ul> | <ul><li>Kendi eşlemenizi oluşturmanız ve bileşenleri azaltmanız gerektiğinden Pig veya Hive kullanmaktan daha zordur.</li><li>Veri kümelerinin katılması gereken işlemlerin uygulanması daha zordur.</li><li>Kullanılabilir test çerçeveleri olsa da, kod hata ayıklama kodu normal bir uygulamadan daha karmaşıktır çünkü kod, Hadoop iş Zamanlayıcı denetimi altında bir toplu iş olarak çalışır.</li></ul> |
| **Apache HCatalog** | <ul><li>Depolama alanının yol ayrıntılarını soyutlar, yönetimi kolaylaştırır ve kullanıcıların verilerin depolandığı yeri bilmesini sağlama ihtiyacını ortadan kaldırır.</li><li>Veri kullanılabilirliği gibi olayların bildirilmesini sağlar. Bu, örneğin, Oozie gibi diğer araçların işlemlerin ne zaman oluştuğunu algılamasına izin verir.</li><li>Anahtara göre bölümlendirme dahil olmak üzere verilerin ilişkisel bir görünümünü sunar ve verilerin erişimi kolaylaştırır.</li></ul> | <ul><li>Varsayılan olarak RCFile, CSV metni, JSON metni, SequenceFile ve ORC dosya biçimlerini destekler, ancak diğer biçimler için özel bir SerDe yazmanız gerekebilir.</li><li>HCatalog iş parçacığı açısından güvenli değil.</li><li>Pig betiklerde HCatalog yükleyicisi kullanılırken sütunlar için veri türlerinde bazı kısıtlamalar vardır. Daha fazla bilgi için bkz. Apache HCatalog belgelerindeki [Hcatloader veri türleri](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) .</li></ul> |

Genellikle, ihtiyacınız olan sonuçlara neden olan bu yaklaşımlardan en kolay şekilde yararlanabilirsiniz. Örneğin, yalnızca Hive kullanarak bu sonuçlara ulaşabiliyor olabilirsiniz ancak daha karmaşık senaryolar için Pig kullanmanız veya hatta kendi eşlemenizi yazmanız ve bileşenleri azaltmanız gerekebilir. Ayrıca, Hive veya Pig ile denemeler yaptıktan sonra, özel harita ve bileşenleri azaltma işlemlerinin, işleme için ince ayar yaparak daha iyi performans sağlayabileceğine karar verebilirsiniz.

## <a name="custom-mapreduce-components"></a>Özel harita/bileşenleri azaltma

Eşleme/azaltma kodu, **eşleme** olarak uygulanan iki ayrı işlevden oluşur ve bileşenleri **azaltır** . **Map** bileşeni, birden çok küme düğümünde paralel olarak çalıştırılır, her düğüm, düğümün kendi alt kümesine eşlemesini uyguluyor. **Azaltma** bileşeni, tüm harita işlevlerinin sonuçlarını harmanlar ve özetler. Bu iki bileşen hakkında daha fazla bilgi için bkz. [HDInsight 'Ta Hadoop 'Ta MapReduce kullanma](hdinsight-use-mapreduce.md).

Çoğu HDInsight işleme senaryosunda, Pig veya Hive gibi daha yüksek düzeyde bir soyutlama kullanmak daha basit ve daha verimlidir. Ayrıca, özel harita oluşturabilir ve daha gelişmiş işleme gerçekleştirmek için Hive betiklerinin içinde kullanım için bileşenleri azaltabilirsiniz.

Özel harita/azaltma bileşenleri genellikle Java 'da yazılmıştır. Hadoop C#, Ayrıca, F#, Visual Basic, Python ve JavaScript gibi diğer dillerde geliştirilen bileşenlerin kullanılmasına izin veren bir akış arabirimi sağlar.

* Özel Java MapReduce programları geliştirmeye yönelik bir anlatım için bkz. [HDInsight 'Ta Hadoop Için Java MapReduce programları geliştirme](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Kendi eşlemenizi oluşturmayı düşünün ve aşağıdaki koşullara göre bileşenleri azaltabilirsiniz:

* Verileri ayrıştırarak ve bundan sonra yapılandırılmış bilgileri elde etmek için özel mantık kullanarak, tamamen yapılandırılmamış verileri işlemek gerekir.
* UDF oluşturmaya gerek kalmadan Pig veya Hive içinde hızlı bir şekilde ifade etmek için zor (veya imkansız) karmaşık görevler gerçekleştirmek istiyorsunuz. Örneğin, enlem ve boylam koordinatlarını veya kaynak verilerdeki IP adreslerini coğrafi konum adlarına dönüştürmek için bir dış coğrafi kodlama hizmeti kullanmanız gerekebilir.
* Hadoop akış arabirimini kullanarak harita/azaltma bileşenlerinde mevcut .NET, Python veya JavaScript kodunuzu yeniden kullanmak istiyorsunuz.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Özel MapReduce programınızı karşıya yükleyin ve çalıştırın

En yaygın MapReduce programları Java dilinde yazılır ve bir jar dosyasına derlenir.

1. MapReduce programınızı geliştirip, derledikten ve test ettikten sonra jar dosyanızı headnode 'a `scp` yüklemek için komutunu kullanın.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    **Kullanıcı adını** KÜMENIZ için SSH kullanıcı hesabı ile değiştirin. **Clustername** değerini küme adıyla değiştirin. SSH hesabının güvenliğini sağlamak için bir parola kullandıysanız parolayı girmeniz istenir. Bir sertifika kullandıysanız, özel anahtar dosyasını belirtmek için `-i` parametresini kullanmanız gerekebilir.

2. [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)kullanarak kümeye bağlanın.

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. SSH oturumunda MapReduce programınızı YARN ile yürütün.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Bu komut MapReduce işini YARN 'ye gönderir. Giriş dosyası `/example/data/sample.log`ve çıkış `/example/data/logoutput`dizini. Giriş dosyası ve herhangi bir çıkış dosyası, küme için varsayılan depolama alanında depolanır.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight C# 'Ta Apache Hadoop MapReduce streaming ile kullanma](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [HDInsight üzerinde Apache Hadoop için Java MapReduce programları geliştirme](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [HDInsight kümesi için Apache Spark uygulamalar oluşturmak üzere Azure Toolkit for Eclipse kullanma](../spark/apache-spark-eclipse-tool-plugin.md)
* [HDInsight 'ta Apache Hive ve Apache Pig ile Python Kullanıcı tanımlı Işlevleri (UDF) kullanma](python-udf-hdinsight.md)
