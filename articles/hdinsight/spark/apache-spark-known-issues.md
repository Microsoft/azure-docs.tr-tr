---
title: Azure HDInsight 'ta Apache Spark kümesiyle ilgili sorunları giderme
description: Azure HDInsight 'ta Apache Spark kümeleriyle ilgili sorunlar ve bu sorunları geçici olarak çözmek hakkında bilgi edinin.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: c92b55d3ac7f4476b7b74d25b40150a74c6ea1cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930150"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>HDInsight üzerinde Apache Spark kümesi için bilinen sorunlar

Bu belge, HDInsight Spark genel önizlemesinin tüm bilinen sorunlarını izler.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy etkileşimli oturumu sızdırıyor
[Apache Lıvy](https://livy.incubator.apache.org/) yeniden başlatıldığında ( [Apache ambarı](https://ambari.apache.org/) 'ndan veya iş düğümü 0 sanal makine yeniden başlatma nedeniyle), etkileşimli bir oturum hala etkin olduğunda, etkileşimli bir iş oturumu sızdırılmaz. Sonuç olarak, yeni işler kabul edilen durumda kalmış olabilir.

**Mayı**

Sorunu geçici olarak çözmek için aşağıdaki yordamı kullanın:

1. Yayın düğümüne SSH. Bilgi için bkz. [HDInsight ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Livy ile başlatılan etkileşimli işlerin uygulama kimliklerini bulmak için aşağıdaki komutu çalıştırın.

   ```bash
   yarn application –list
   ```

    İşlerin açık adı olmayan bir etkileşimli oturumla başlatılmış olması halinde varsayılan iş adları kesin olur. [Jupyter Notebook](https://jupyter.org/)tarafından başlatılan bir oturum için iş adı ile başlar `remotesparkmagics_*` .

3. Bu işleri sonlandırmak için aşağıdaki komutu çalıştırın.

   ```bash
   yarn application –kill <Application ID>
   ```

Yeni işler çalışmaya başladı.

## <a name="spark-history-server-not-started"></a>Spark geçmiş sunucusu başlatılmadı
Spark geçmiş sunucusu, bir küme oluşturulduktan sonra otomatik olarak başlatılmaz.  

**Mayı**

Geçmiş sunucusunu ambarı 'ndan el ile başlatın.

## <a name="permission-issue-in-spark-log-directory"></a>Spark günlük dizininde izin sorunu
hdiuser Spark-gönder kullanarak bir işi gönderirken aşağıdaki hatayı alır:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

Ve hiçbir sürücü günlüğü yazılmaz.

**Mayı**

1. Hadoop grubuna hdiuser ekleyin.
2. Küme oluşturulduktan sonra/var/log/Spark üzerinde 777 izinleri sağlayın.
3. Ambarı kullanarak Spark günlük konumunu, 777 izni olan bir dizin olacak şekilde güncelleştirin.  
4. Spark-sudo olarak gönder ' i çalıştırın.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark-Phoenix Bağlayıcısı desteklenmiyor

HDInsight Spark kümeleri Spark-Phoenix bağlayıcısını desteklemez.

**Mayı**

Bunun yerine Spark-HBase bağlayıcısını kullanmanız gerekir. Yönergeler için bkz. [Spark-HBase Bağlayıcısı kullanma](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Jupyıter Not defterleri ile ilgili sorunlar

Jupi Not defterleri ile ilgili bazı bilinen sorunlar aşağıda verilmiştir.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Dosya adlarında ASCII olmayan karakterler içeren Not defterleri

Jupyter Notebook dosya adlarında ASCII olmayan karakterler kullanmayın. ASCII olmayan bir dosya adına sahip Jupyter kullanıcı arabiriminden bir dosya yüklemeye çalışırsanız, hata iletisi olmadan başarısız olur. Jupyter dosyayı karşıya yüklemeye izin vermez, ancak görünür bir hata oluşturmaz.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Daha büyük boyutlarda Not defterleri yüklenirken hata oluştu

**`Error loading notebook`** Boyutu daha büyük olan not defterlerini yüklerken bir hata görebilirsiniz.  

**Mayı**

Bu hatayı alırsanız, verileriniz bozuk veya kayıp anlamına gelmez.  Not defterleriniz hala üzerinde disk üzerinde kalır `/var/lib/jupyter` ve bunlara erişmek için KÜMEYE SSH ekleyebilirsiniz. Bilgi için bkz. [HDInsight ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

SSH kullanarak kümeye bağlandıktan sonra, Not defterinizdeki önemli verilerin kaybolmasını engellemek için not defterlerinizi kümenizdeki yerel makinenize (SCP veya WinSCP kullanarak) bir yedekleme olarak kopyalayabilirsiniz. Daha sonra, bağlantı noktası 8001 ' de, ağ geçidine geçmeden jupi 'ye erişmek için bağlantı düğümünüz için SSH tünelini kullanabilirsiniz.  Buradan, dizüstü bilgisayarınızın çıktısını temizleyebilir ve Not defterinin boyutunu en aza indirmek için yeniden kaydedebilirsiniz.

Bu hatanın gelecekte oluşmasını engellemek için bazı en iyi yöntemleri izlemeniz gerekir:

* Not defteri boyutunun küçük tutulması önemlidir. Jupyter 'a geri gönderilen Spark işlerinizin tüm çıktıları not defterinde kalıcıdır.  Büyük RDD 'ler veya veri çerçeveleri üzerinde çalışmayı önlemek için genel olarak jupi ile ilgili en iyi uygulamadır `.collect()` . bunun yerine, BIR RDD 'nin içeriğine gözatmak istiyorsanız `.take()` veya `.sample()` çıktınızın çok büyük olmaması için veya kullanmayı düşünün.
* Ayrıca, bir not defterini kaydettiğinizde, boyutu azaltmak için tüm çıkış hücrelerini temizleyebilirsiniz.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Not defteri ilk başlatması beklenenden uzun sürüyor

Spark Magic kullanarak Jupyter Notebook ilk kod açıklaması bir dakikadan uzun sürebilir.  

**Açıklamalarla**

Bu, ilk kod hücresi çalıştırıldığında meydana gelir. Arka planda bu, oturum yapılandırma ve Spark, SQL ve Hive bağlamlarının ayarlandığı bir şekilde başlatılır. Bu bağlamlar ayarlandıktan sonra, ilk ifade çalıştırılır ve bu, deyimin tamamlanması uzun zaman aldığı izlenimi verir.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Oturum oluşturma sırasında zaman aşımı Jupyter Notebook

Spark kümesi kaynak dışında olduğunda, Jupyter Notebook Spark ve pyspark çekirdekler oturumlarını oluşturmaya çalışırken zaman aşımına uğrar.

**Karşı**

1. Spark kümenizdeki bazı kaynakları şu şekilde boşaltın:

   * Kapat ve Durdur menüsüne gidip Not defteri Gezgini 'nde Kapat ' a tıklayarak diğer Spark Not defterleri durduruluyor.
   * YARN 'den diğer Spark uygulamaları durduruluyor.

2. Başlatmaya çalıştığınız Not defterini yeniden başlatın. Şimdi bir oturum oluşturmanız için yeterli kaynak olmalıdır.

## <a name="see-also"></a>Ayrıca bkz.

* [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>Senaryolar

* [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](apache-spark-use-bi-tools.md)
* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 'ta Apache Spark kullanarak Web sitesi günlüğü Analizi](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Uygulamaları oluşturma ve çalıştırma

* [Scala kullanarak tek başına uygulama oluşturma](apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Araçlar ve uzantılar

* [Spark Scala uygulamaları oluşturmak ve göndermek amacıyla IntelliJ IDEA için HDInsight Araçları Eklentisini kullanma](apache-spark-intellij-tool-plugin.md)
* [Apache Spark uygulamalarında uzaktan hata ayıklama için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesinde Jupyter Notebook için kullanılabilir olan kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyıter Not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)
