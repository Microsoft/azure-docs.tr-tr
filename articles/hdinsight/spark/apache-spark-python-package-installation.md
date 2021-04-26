---
title: Azure HDInsight üzerinde jupi ile Python paketleri için betik eylemi
description: Betik eyleminin nasıl kullanılacağına ilişkin adım adım yönergeler, HDInsight Spark kümeleri ile birlikte sunulan jupi not defterlerini dış Python paketleri kullanacak şekilde yapılandırma.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: c3f912b4f4c2e78c44425f489927cee185b3d312
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868724"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Betik Eylemi kullanarak Azure HDInsight üzerinde Python ortamını güvenli bir şekilde yönetin

HDInsight, Spark kümesinde, Anaconda Python 2,7 ve Python 3,5 ' de iki yerleşik Python yüklemelerine sahiptir. Müşterilerin, dış Python paketlerini yükleme gibi Python ortamını özelleştirmesi gerekebilir. Burada, HDInsight 'ta Apache Spark kümeleri için Python ortamlarını güvenli bir şekilde yönetmeye yönelik en iyi uygulama gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md). HDInsight üzerinde zaten bir Spark kümeniz yoksa, küme oluşturma sırasında betik eylemleri çalıştırabilirsiniz. [Özel Betik eylemlerinin kullanımı](../hdinsight-hadoop-customize-cluster-linux.md)hakkındaki belgeleri ziyaret edin.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight kümelerinde kullanılan açık kaynaklı yazılım desteği

Microsoft Azure HDInsight hizmeti Apache Hadoop etrafında oluşturulan açık kaynaklı teknolojilerin bir ortamını kullanır. Microsoft Azure, açık kaynaklı teknolojiler için genel bir destek düzeyi sağlar. Daha fazla bilgi için bkz. [Azure DESTEĞI SSS Web sitesi](https://azure.microsoft.com/support/faq/). HDInsight hizmeti, yerleşik bileşenler için ek bir destek düzeyi sağlar.

HDInsight hizmetinde bulunan iki tür açık kaynaklı bileşen vardır:

|Bileşen |Açıklama |
|---|---|
|Yerleşik|Bu bileşenler HDInsight kümelerinde önceden yüklenir ve kümenin temel işlevlerini sağlar. Örneğin, Apache Hadoop YARN Kaynak Yöneticisi, Apache Hive sorgu dili (HiveQL) ve Mahout kitaplığı bu kategoriye aittir. Tüm küme bileşenleri listesi, [HDInsight tarafından sağlanan Apache Hadoop kümesi sürümlerindeki yenilikler](../hdinsight-component-versioning.md)bölümünde bulunur.|
|Özel|Kümenin bir kullanıcısı olarak, kuruluşunuzda bulunan veya sizin tarafınızdan oluşturulan herhangi bir bileşeni iş yükünüze yükleyebilir veya kullanabilirsiniz.|

> [!IMPORTANT]
> HDInsight kümesiyle birlikte sunulan bileşenler tam olarak desteklenmektedir. Microsoft Desteği, bu bileşenlerle ilgili sorunları yalıtmaya ve çözmeye yardımcı olur.
>
> Özel bileşenler, sorunu gidermeye yardımcı olmak için ticari açıdan makul destek alır. Microsoft desteği sorunu çözebiliyor olabilir veya bu teknoloji için derin uzmanlığın bulunduğu açık kaynaklı teknolojiler için kullanılabilir kanalları ister. Örneğin, şu şekilde kullanılabilecek birçok topluluk sitesi vardır: [Microsoft Q&HDInsight için soru sayfası](/answers/topics/azure-hdinsight.html) `https://stackoverflow.com` . Ayrıca Apache projelerinin üzerinde proje siteleri vardır `https://apache.org` .

## <a name="understand-default-python-installation"></a>Varsayılan Python yüklemesini anlama

HDInsight Spark kümesi, Anaconda yüklemesiyle oluşturulur. Kümede, Anaconda Python 2,7 ve Python 3,5 olmak üzere iki Python yüklemesi vardır. Aşağıdaki tabloda Spark, Livy ve Jupyıter için varsayılan Python ayarları gösterilmektedir.

|Ayar |Python 2,7|Python 3,5|
|----|----|----|
|Yol|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark sürümü|Varsayılan olarak 2,7 ayarlanır|Yapılandırmayı 3,5 olarak değiştirebilir|
|Livy sürümü|Varsayılan olarak 2,7 ayarlanır|Yapılandırmayı 3,5 olarak değiştirebilir|
|Jupyter|PySpark çekirdeği|PySpark3 çekirdeği|

## <a name="safely-install-external-python-packages"></a>Dış Python paketlerini güvenle yükler

HDInsight kümesi, Python 2,7 ve Python 3,5 yerleşik Python ortamına bağlıdır. Özel paketleri bu varsayılan yerleşik ortamlara doğrudan yüklemek beklenmeyen kitaplık sürümü değişikliklerine neden olabilir. Ve kümeyi daha fazla bölün. Spark uygulamalarınız için özel dış Python paketlerini güvenli bir şekilde yüklemek için aşağıdaki adımları izleyin.

1. Conda kullanarak Python sanal ortamı oluşturun. Sanal bir ortam, başka bir yere kırçıkmadan projeleriniz için yalıtılmış bir alan sağlar. Python sanal ortamını oluştururken, kullanmak istediğiniz Python sürümünü belirtebilirsiniz. Python 2,7 ve 3,5 ' i kullanmak istiyor olsanız bile, hala sanal ortam oluşturmanız gerekir. Bu gereksinim, kümenin varsayılan ortamının Brode gerçekleştirmediğinden emin olmak için gereklidir. Python sanal ortamı oluşturmak için aşağıdaki betiği içeren tüm düğümler için kümenizde betik eylemleri çalıştırın.

    -   `--prefix` bir Conda sanal ortamının yaşadığı bir yolu belirtir. Burada belirtilen yola göre daha fazla değiştirilmesi gereken birkaç yapılandırma vardır. Bu örnekte, küme zaten py35 adlı mevcut bir sanal ortama sahip olduğu için py35new kullanıyoruz.
    -   `python=` sanal ortam için Python sürümünü belirtir. Bu örnekte, içinde yerleşik olarak bulunan kümeyle aynı sürüme sahip sürüm 3,5 ' i kullanırız. Sanal ortam oluşturmak için diğer Python sürümlerini de kullanabilirsiniz.
    -   `anaconda` sanal ortama Anaconda paketleri yüklemek için package_spec, Anaconda olarak belirtir.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Gerekirse, oluşturulan sanal ortama dış Python paketleri yükler. Dış Python paketlerini yüklemek için aşağıdaki betiği içeren tüm düğümler için kümenizde betik eylemleri çalıştırın. Sanal ortam klasörüne dosya yazmak için burada sudo ayrıcalığına sahip olmanız gerekir.

    Kullanılabilir paketlerin tüm listesi için [paket dizininde](https://pypi.python.org/pypi) arama yapın. Ayrıca, diğer kaynaklardan kullanılabilir paketlerin bir listesini alabilirsiniz. Örneğin, [Conda-Forge](https://conda-forge.org/feedstocks/)aracılığıyla kullanıma sunulan paketleri yükleyebilirsiniz.

    En son sürümü olan bir kitaplığı yüklemek istiyorsanız aşağıdaki komutu kullanın:

    - Conda kanalını kullanın:

        -   `seaborn` , yüklemek istediğiniz paket adıdır.
        -   `-n py35new` Yeni oluşturulan sanal ortam adını belirtin. Sanal ortam oluşturmaya göre adı karşılık geldiğinden emin olun.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Ya da PyPi depoyu, değişiklik `seaborn` ve `py35new` karşılık geleni kullanın:
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    Belirli bir sürümü olan bir kitaplık yüklemek istiyorsanız aşağıdaki komutu kullanın:

    - Conda kanalını kullanın:

        -   `numpy=1.16.1` , yüklemek istediğiniz paket adı ve sürümdür.
        -   `-n py35new` Yeni oluşturulan sanal ortam adını belirtin. Sanal ortam oluşturmaya göre adı karşılık geldiğinden emin olun.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Ya da PyPi depoyu, değişiklik `numpy==1.16.1` ve `py35new` karşılık geleni kullanın:

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    sanal ortam adını bilmiyorsanız, kümenin baş düğümüne SSH gönderebilir ve `/usr/bin/anaconda/bin/conda info -e` tüm sanal ortamları göstermek için çalıştırabilirsiniz.

3. Spark ve Livy yapılandırmalarını değiştirip oluşturulan sanal ortama işaret edin.

    1. Ambarı Kullanıcı arabirimini açın, Spark2 Page, configs sekmesine gidin.

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png" alt-text="Spark ve Livy yapılandırmasını, ambarı aracılığıyla değiştirme" border="true":::

    2. Advanced livy2-env ' yi genişletin, altta aşağıdaki deyimleri ekleyin. Sanal ortamı farklı bir önek ile yüklediyseniz, yolu karşılık gelenle değiştirin.

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-livy-config.png" alt-text="Ambarı aracılığıyla Livy yapılandırmasını değiştirme" border="true":::

    3. Advanced spark2-env ' ı genişletin, en altta bulunan dışarı aktarma PYSPARK_PYTHON ifadesini değiştirin. Sanal ortamı farklı bir önek ile yüklediyseniz, yolu karşılık gelenle değiştirin.

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-spark-config.png" alt-text="Spark config 'i ambarı aracılığıyla değiştirme" border="true":::

    4. Değişiklikleri kaydedin ve etkilenen hizmetleri yeniden başlatın. Bu değişikliklerin Spark2 hizmetinin yeniden başlatılması gerekir. Ambarı Kullanıcı arabirimi gerekli bir yeniden başlatma anımsatıcısı ister, tüm etkilenen hizmetleri yeniden başlatmak için yeniden Başlat 'a tıklayın.

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-restart-services.png" alt-text="Hizmetleri yeniden Başlat" border="true":::

    5. İşin güncelleştirilmiş Spark yapılandırmasına işaret ettiğini sağlamak için Spark oturumunuzla iki özellik ayarlayın: `spark.yarn.appMasterEnv.PYSPARK_PYTHON` ve `spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON` . 

        Terminal veya Not defterini kullanarak `spark.conf.set` işlevini kullanın.

        ```spark
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        ```

        Livy kullanıyorsanız, istek gövdesine aşağıdaki özellikleri ekleyin:

        ```
        “conf” : {
        “spark.yarn.appMasterEnv.PYSPARK_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”,
        “spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”
        }
        ```

4. Jupyıter üzerinde yeni oluşturulan sanal ortamı kullanmak istiyorsanız. Jupi yapılandırmalarını 'yi değiştirip jupyıter 'ı yeniden başlatın. Jupi 'yi yeni oluşturulan sanal ortama işaret etmek için aşağıdaki deyimle tüm üst bilgi düğümlerinde betik eylemlerini çalıştırın. Sanal ortamınız için belirttiğiniz önek için yolu değiştirdiğinizden emin olun. Bu betik eylemini çalıştırdıktan sonra, bu değişikliği kullanılabilir hale getirmek için ambarı Kullanıcı arabirimi aracılığıyla Jupyıter hizmetini yeniden başlatın.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Aşağıdaki kodu çalıştırarak Jupyter Notebook Python ortamını iki kez doğrulayabilirsiniz:

    :::image type="content" source="./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png" alt-text="Jupyter Notebook Python sürümünü denetle" border="true":::

## <a name="known-issue"></a>Bilinen sorun

Anaconda sürümü, ve için bilinen bir hata `4.7.11` var `4.7.12` `4.8.0` . Betik eylemlerinizin yanıt verdiğini `"Collecting package metadata (repodata.json): ...working..."` ve ile başarısız olduğunu görürseniz `"Python script has been killed due to timeout after waiting 3600 secs"` . Sorunu çözebilmeniz için [bu betiği](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) indirebilir ve tüm düğümlerde betik eylemleri olarak çalıştırabilirsiniz.

Anaconda sürümünüzü denetlemek için, küme üst bilgisi düğümüne SSH oluşturabilir ve çalıştırabilirsiniz `/usr/bin/anaconda/bin/conda --v` .

## <a name="next-steps"></a>Sonraki adımlar

* [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)
* [Apache Spark 'de jupi Notebook içeren dış paketler](apache-spark-jupyter-notebook-use-external-packages.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)
