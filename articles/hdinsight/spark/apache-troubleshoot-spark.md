---
title: Azure HDInsight 'ta Apache Spark sorunlarını giderme
description: Apache Spark ve Azure HDInsight ile çalışma hakkında sık sorulan soruların yanıtlarını alın.
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: b54b9d932505ada890ac21c1b8de3178ad2f0042
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867517"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Azure HDInsight kullanarak Apache Spark sorunlarını giderme

[Apache ambarı](https://ambari.apache.org/)'nda Apache Spark yükleri ile çalışırken en üstteki sorunlar ve çözümleri hakkında bilgi edinin.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Kümeler üzerinde Apache Ambari kullanarak bir Apache Spark uygulamasını nasıl yapılandırabilirim?

Spark yapılandırma değerleri, Apache Spark bir uygulama özel durumuyla karşılaşmamak için ayarlanabilir `OutofMemoryError` . Aşağıdaki adımlar, Azure HDInsight 'ta varsayılan Spark yapılandırma değerlerini göstermektedir:

1. Küme kimlik bilgilerinizle, ambarı ' nda oturum açın `https://CLUSTERNAME.azurehdidnsight.net` . İlk ekranda Genel Bakış Panosu görüntülenir. HDInsight 3,6 ve 4,0 arasında hafif yüzeysel farklılıkları vardır.

1. **Spark2**  >  **configs** sayfasına gidin.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png" alt-text="Configs sekmesini seçin" border="true":::

1. Yapılandırma listesinde **Custom-spark2-Defaults**' ı seçin ve genişletin.

1. **spark.executor. Memory** gibi ayarlamanız gereken değer ayarını bulun. Bu durumda, **9728dk** değeri çok yüksektir.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png" alt-text="Özel-Spark-varsayılanlar seçin" border="true":::

1. Değeri önerilen ayarı olarak ayarlayın. Bu ayar için **2048m** değeri önerilir.

1. Değeri kaydedin ve sonra yapılandırmayı kaydedin. **Kaydet**’i seçin.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png" alt-text="Değeri 20 48m olarak değiştir" border="true":::

    Yapılandırma değişiklikleriyle ilgili bir konum yazın ve ardından **Kaydet**' i seçin.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png" alt-text="Yaptığınız değişiklikler hakkında bir bilgi girin" border="true":::

    Herhangi bir yapılandırmaya dikkat edilmesi gerekiyorsa size bildirilir. Öğeleri aklınızda ve **yine de devam et**' i seçin.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png" alt-text="Yine de devam et 'i seçin" border="true":::

1. Bir yapılandırma her kaydedildiğinde hizmeti yeniden başlatmanız istenir. **Yeniden Başlat**' ı seçin.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png" alt-text="Yeniden Başlat 'ı seçin" border="true":::

    Yeniden başlatmayı onaylayın.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png" alt-text="Tümünü yeniden başlatmayı Onayla seçeneğini belirleyin" border="true":::

    Çalıştıran işlemlerin gözden geçirilmesini sağlayabilirsiniz.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png" alt-text="Çalışan işlemlerin gözden geçirilmesi" border="true":::

1. Yapılandırma ekleyebilirsiniz. Yapılandırma listesinde **Custom-spark2-Defaults**' ı seçin ve ardından **Özellik Ekle**' yi seçin.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png" alt-text="Özellik Ekle ' yi seçin" border="true":::

1. Yeni bir özellik tanımlayın. Veri türü gibi belirli ayarlar için bir iletişim kutusu kullanarak tek bir özellik tanımlayabilirsiniz. Ya da her satır için bir tanım kullanarak birden çok özellik tanımlayabilirsiniz.

    Bu örnekte, **spark. Driver. Memory** özelliği **4g** değeri ile tanımlanmıştır.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png" alt-text="Yeni özellik tanımla" border="true":::

1. Yapılandırmayı kaydedin ve ardından 6. ve 7. adımlarda açıklandığı gibi hizmeti yeniden başlatın.

Bu değişiklikler küme genelinde olmakla kalmaz, Spark işini gönderdiğinizde geçersiz kılınabilir.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Kümeler üzerinde bir Jupyter Notebook kullanarak Apache Spark uygulamasını yapılandırmak Nasıl yaparım??

Jupyter Notebook ilk hücresinde, **%% Configure** direktifinden sonra Spark YAPıLANDıRMALARıNıN geçerli JSON biçiminde belirtilmesi gerekir. Gerçek değerleri gerektiği gibi değiştirin:

:::image type="content" source="./media/apache-troubleshoot-spark/add-configuration-cell.png" alt-text="Yapılandırma ekleme" border="true":::

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Kümeler üzerinde Apache Livy kullanarak bir Apache Spark uygulamasını nasıl yapılandırabilirim?

Benzer bir REST istemcisini kıvrımlı olarak kullanarak Spark uygulamasını Livy 'a gönderme. Aşağıdakine benzer bir komut kullanın. Gerçek değerleri gerektiği gibi değiştirin:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Kümeler üzerinde spark-submit kullanarak bir Apache Spark uygulamasını nasıl yapılandırabilirim?

Aşağıdakine benzer bir komut kullanarak Spark-Shell 'i başlatın. Yapılandırmaların gerçek değerini gerektiği gibi değiştirin:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Ek okuma

[HDInsight kümelerinde iş gönderimi Apache Spark](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* [Spark bellek yönetimine genel bakış](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [HDInsight kümelerinde Spark uygulamasında hata ayıklama](/archive/blogs/azuredatalake/spark-debugging-101).

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.