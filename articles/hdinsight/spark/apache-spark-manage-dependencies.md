---
title: Azure HDInsight 'ta Spark uygulama bağımlılıklarını yönetme
description: Bu makalede, PySpark ve Scala uygulamaları için HDInsight Spark kümesinde Spark bağımlılıklarını yönetme hakkında bir giriş sunulmaktadır.
author: yanancai
ms.author: yanacai
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: f4940da47b832c2b3c39ab2fa225a229d1d730bf
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062718"
---
# <a name="manage-spark-application-dependencies"></a>Spark uygulama bağımlılıklarını yönetme

Bu makalede, HDInsight üzerinde çalışan Spark uygulamalarınızın bağımlılıklarını yönetmeyi öğreneceksiniz. Spark uygulaması ve küme kapsamında hem Scala hem de PySpark ele alınmaktadır.

Kullanıcı çalışmanıza göre bölüme geçmek için hızlı bağlantıları kullanın:
* [Jupyter Notebook kullanarak Spark iş jar bağımlılıklarını ayarlama](#use-jupyter-notebook)
* [Use Azure Toolkit for IntelliJ kullanarak Spark iş jar bağımlılıklarını ayarlama](#use-azure-toolkit-for-intellij)
* [Spark kümesi için jar bağımlılıklarını yapılandırma](#jar-libs-for-cluster)
* [Jar bağımlılıklarını güvenli şekilde yönetme](#safely-manage-jar-dependencies)
* [Jupyter Notebook kullanarak Spark işi Python paketlerini ayarlama](#use-jupyter-notebook-1)
* [Spark kümesi için Python paketlerini güvenle yönetin](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>Bir Spark işi için jar LIBS
### <a name="use-jupyter-notebook"></a>Jupyter Notebook kullan
Bir Spark oturumu, Scala için Spark çekirdekte Jupyter Notebook başlatıldığında paketleri şu şekilde yapılandırabilirsiniz:

* [Spark paketlerinde](https://spark-packages.org/) [Maven deposu](https://search.maven.org/)veya topluluk tarafından katkıda bulunulan paketler.
* Kümenizin birincil deposunda depolanan jar dosyaları.

`%%configure`Not defterini harici bir paket kullanacak şekilde yapılandırmak için Magic 'i kullanacaksınız. Dış paketleri kullanan not defterlerinde, `%%configure` ilk kod hücresinde Magic ' i çağırdığınızdan emin olun. Bu, çekirdeğin oturum başlamadan önce paketi kullanacak şekilde yapılandırılmasını sağlar.

>
>[!IMPORTANT]  
>İlk hücrede çekirdeği yapılandırmayı unutursanız, `%%configure` `-f` parametresini parametresiyle kullanabilirsiniz, ancak bu işlem oturumu yeniden başlatır ve tüm ilerleme kaybedilir.

**Maven deposundan veya Spark paketlerindeki paketler için örnek**

Maven deposundan paketi bulduktan sonra **GroupID**, **ArtifactId** ve **Version** değerlerini toplayın. İki nokta üst üste (**:**) ayırarak üç değeri birleştirir.

   :::image type="content" source="./media/apache-spark-manage-dependencies/spark-package-schema.png " alt-text="Birleştirme paketi şema" border="true":::Kage şeması "Border =" true ":::

Toplamakta olduğunuz değerlerin kümenize eşleştiğinden emin olun. Bu durumda, HDInsight 3,6 Spark kümesi için Scala 2,11 ve Spark 2,3 için Spark Cosmos DB bağlayıcı paketini kullanıyoruz. Emin değilseniz, `scala.util.Properties.versionString` küme Scala sürümünü almak Için Spark çekirdeğindeki kod hücresinde çalıştırın. `sc.version`Cluster Spark sürümünü almak için öğesini çalıştırın.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**Birincil depolamada depolanan jars örneği**

Kümelerinizdeki jar dosyaları için [URI şemasını](../hdinsight-hadoop-linux-information.md#URI-and-scheme) kullanın birincil depolama alanı. Bu `wasb://` , `abfs://` Azure Data Lake Storage 2. veya Azure Data Lake Storage 1. Için Azure depolama için olacaktır `adl://` . Azure depolama veya Data Lake Storage 2. için güvenli aktarım etkinse, URI `wasbs://` veya olur `abfss://` . Bkz. [Güvenli aktarım](../../storage/common/storage-require-secure-transfer.md).

Birden çok jar dosyası için jar yollarının virgülle ayrılmış listesini kullanın, genelleştirmeler izin verilir. Jar dosyaları dışındaki, sürücü ve yürütücü classpaths 'a dahildir.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

Dış paketleri yapılandırdıktan sonra, paketlerin doğru şekilde yerleştirilmiş olduğunu doğrulamak için kod hücresinde içeri aktarma çalıştırabilirsiniz.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ kullan
[Azure Toolkit for IntelliJ eklenti](./apache-spark-intellij-tool-plugin.md) , Spark Scala uygulamasını bir HDInsight kümesine göndermek için UI deneyimi sağlar. `Referenced Jars` `Referenced Files` Spark uygulamasını gönderirken jar kitaplıklar yollarını yapılandırmak için ve özelliklerini sağlar. [HDInsight için Azure Toolkit for IntelliJ eklentisinin kullanımı](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)hakkında daha fazla ayrıntı için bkz..

:::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png" alt-text="Spark gönderimi iletişim kutusu" border="true":::

## <a name="jar-libs-for-cluster"></a>Küme için jar LIBS
Bazı durumlarda, her uygulamanın varsayılan olarak aynı bağımlılıklarla ayarlanabilmesi için, küme düzeyinde jar bağımlılıklarını yapılandırmak isteyebilirsiniz. Yaklaşım, jar yollarınızın Spark sürücüsüne ve yürütücü sınıfı yoluna eklenmesi.

1. Birincil depolamadan `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` küme yerel dosya sistemine jar dosyalarını kopyalamak için aşağıdaki örnek betik eylemlerini çalıştırın `/usr/libs/sparklibs` . Bu adım, `:` sınıf yol listesini ayırmak için Linux kullanır, ancak HDInsight yalnızca benzer şemayla depolama yollarını destekler `wasb://` . Doğrudan sınıf yoluna eklerseniz, uzak depolama yolu düzgün çalışmaz.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Sınıf yolunu güncelleştirmek için Spark hizmeti yapılandırmasını ambarı 'ndan değiştirin. **Özel Spark2-varsayılanlar > > Spark > configs**'a gidin. **Özelliği** aşağıdaki şekilde ekleyin. `:`Eklemek için birden fazla yolunuz varsa, yolları ayırmak için kullanın. Genelleştirmeler izin verilir.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   :::image type="content" source="./media/apache-spark-manage-dependencies/change-spark-default-config.png " alt-text="Spark varsayılan yapılandırmasının" border="true":::ULT yapılandırmasını değiştir "Border =" true ":::

3. Değiştirilen konfigürasyonları kaydedin ve etkilenen hizmetleri yeniden başlatın.

   :::image type="content" source="./media/apache-spark-manage-dependencies/restart-impacted-services.png " alt-text="Etkilenen hizmetler hizmetlerini yeniden Başlat" border="true":::"Border =" true ":::

[Betik eylemlerini](../hdinsight-hadoop-customize-cluster-linux.md)kullanarak adımları otomatikleştirebilirsiniz. [Hive özel kitaplıklarını eklemek](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) için betik eylemi iyi bir başvurudur. Spark hizmeti yapılandırması değiştirilirken, yapılandırma dosyalarını doğrudan değiştirmek yerine, ambarı API 'Lerini kullandığınızdan emin olun. 

## <a name="safely-manage-jar-dependencies"></a>Jar bağımlılıklarını güvenli şekilde yönetme
HDInsight kümesinde yerleşik jar bağımlılıkları bulunur ve bu jar sürümleri için güncelleştirmeler zaman zaman gerçekleşir. Başvuru için yaptığınız yerleşik jliler ve jliler arasında sürüm çakışmasını önlemek için [uygulama bağımlılıklarınızı gölgelendirmeyi](./safely-manage-jar-dependency.md)göz önünde bulundurun.

## <a name="python-packages-for-one-spark-job"></a>Bir Spark işi için Python paketleri
### <a name="use-jupyter-notebook"></a>Jupyter Notebook kullan
HDInsight Jupyter Notebook PySpark çekirdeği, PyPi veya Anaconda paket deposundan doğrudan Python paketlerinin yüklenmesini desteklemez. `.zip`, `.egg` Veya `.py` bağımlılıklarınız varsa ve bir Spark oturumunda bunlara başvurmak isterseniz, aşağıdaki adımları izleyin:

1. Aşağıdaki örnek betik eylemlerini `.zip` `.egg` veya `.py` birincil depolamadan `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` küme yerel dosya sistemine dosya `/usr/libs/pylibs` kopyalamak için çalıştırın. Bu adım, `:` arama yolu listesini ayırmak için Linux kullanır, ancak HDInsight yalnızca benzer şemayla depolama yollarını destekler `wasb://` . Uzak depolama yolu kullandığınızda doğru çalışmaz `sys.path.insert` .

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. Not defterinizde, PySpark çekirdeğiyle bir kod hücresinde aşağıdaki kodu çalıştırın:

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. `import`Paketlerinizin başarıyla dahil edilip edilmediğini denetlemek için ' i çalıştırın.  

## <a name="python-packages-for-cluster"></a>Küme için Python paketleri
Komut dosyası eylemleri aracılığıyla Conda komutunu kullanarak Anaconda ile Python paketlerini kümeye yükleyebilirsiniz. Yüklenen paketler küme düzeyindedir ve tüm uygulamalara uygulanır. 

HDInsight Spark kümesinde iki yerleşik Python yüklemesi, Anaconda Python 2,7 ve Anaconda Python 3,5 vardır. Hizmetler için varsayılan Python ayarları hakkında daha fazla bilgi edinmek ve kümeyi bozmadan dış Python paketlerinin güvenli bir şekilde yüklenmesi hakkında daha fazla bilgi için bkz. [kümeniz Için Python bağımlılıklarını güvenle yönetme](./apache-spark-python-package-installation.md).
