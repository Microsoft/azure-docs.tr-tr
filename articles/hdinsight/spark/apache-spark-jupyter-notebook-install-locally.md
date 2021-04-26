---
title: Jupyıter 'ı yerel olarak yükleyip Azure HDInsight 'ta Spark 'a bağlanma
description: Jupyter Notebook bilgisayara yerel olarak yüklemeyi ve bir Apache Spark kümesine bağlamayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: 6dc91dc07d11f195092343e657911a884d8bf475
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866004"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Jupyter Notebook bilgisayarınıza yükleyip HDInsight üzerinde Apache Spark bağlanın

Bu makalede, Spark Magic ile özel PySpark (Python için) ve Apache Spark (Scala için) Jupyter Notebook yüklemeyi öğreneceksiniz. Ardından, Not defterini bir HDInsight kümesine bağlayabilirsiniz.

Jupyıter yükleme ve HDInsight üzerinde Apache Spark bağlanma konusunda dört temel adım vardır.

* Spark kümesini yapılandırın.
* Jupyter Notebook 'i yükler.
* Spark Magic ile pyspark ve Spark çekirdekler 'i yükler.
* Spark Magic 'i HDInsight 'ta Spark kümesine erişecek şekilde yapılandırın.

Özel çekirdekler ve Spark Magic hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Spark Linux kümeleri olan Jupyter Not defterleri için sunulan çekirdekler](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md). Yerel Not defteri HDInsight kümesine bağlanır.

* HDInsight üzerinde Spark ile Jupyter Notebook kullanma bilgisi.

## <a name="install-jupyter-notebook-on-your-computer"></a>Jupyter Notebook bilgisayarınıza yükleyip

Jupyıter not defterlerini yüklemeden önce Python 'u yükleyebilirsiniz. [Anaconda dağıtımı](https://www.anaconda.com/download/) , hem Python hem de Jupyter Notebook yükler.

Platformunuz için [Anaconda yükleyicisini](https://www.anaconda.com/download/) indirin ve kurulumu çalıştırın. Kurulum sihirbazını çalıştırırken, yol değişkeniniz için Anaconda ekleme seçeneğini seçtiğinizden emin olun.  Ayrıca bkz. [Anaconda kullanarak Jupyıter yükleme](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Spark Magic 'i yükler

1. `pip install sparkmagic==0.13.1`HDInsight kümeleri sürüm 3,6 ve 4,0 Için Spark Magic ' i yüklemek için komutunu girin. Ayrıca bkz. [mini sihirli belgeler](https://github.com/jupyter-incubator/sparkmagic#installation).

1. `ipywidgets`Aşağıdaki komutu çalıştırarak emin olun:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Pyspark ve Spark çekirdekler 'i yükler

1. `sparkmagic`Aşağıdaki komutu girerek nerede yükleneceğini belirler:

    ```cmd
    pip show sparkmagic
    ```

    Sonra çalışma dizininizi yukarıdaki komutla belirtilen **konuma** göre değiştirin.

1. Yeni çalışma dizininizden istenen çekirdek (ler) i yüklemek için aşağıdaki komutlardan birini veya birkaçını girin:

    |Çekirdek | Komut |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. İsteğe bağlı. Sunucu uzantısını etkinleştirmek için aşağıdaki komutu girin:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Spark Magic 'i HDInsight Spark kümesine bağlanacak şekilde yapılandırma

Bu bölümde, daha önce yüklediğiniz Spark Magic 'i bir Apache Spark kümesine bağlanacak şekilde yapılandırırsınız.

1. Aşağıdaki komutla Python kabuğunu başlatın:

    ```cmd
    python
    ```

2. Jupyıter yapılandırma bilgileri genellikle kullanıcılar giriş dizininde depolanır. Giriş dizinini tanımlamak ve **\. mini sihirli** adlı bir klasör oluşturmak için aşağıdaki komutu girin.  Tam yol silinecek.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Klasörü içinde `.sparkmagic` , **üzerindeconfig.js** adlı bir dosya oluşturun ve içine aşağıdaki JSON kod parçacığını ekleyin.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Dosyada aşağıdaki düzenlemeleri yapın:

    |Şablon değeri | Yeni değer |
    |---|---|
    |NITELEN|Küme oturumu açma, varsayılan `admin` .|
    |CLUSTERDNSNAME|Küme adı|
    |{BASE64ENCODEDPASSWORD}|Gerçek parolanız için Base64 olarak kodlanmış bir parola.  ' De Base64 parolası oluşturabilirsiniz [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/) .|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Kullanılıyorsa koruyun `sparkmagic 0.12.7` (kümeler v 3.5 ve v 3.6).  Kullanıyorsanız `sparkmagic 0.2.3` (kümeler v 3.4), ile değiştirin `"should_heartbeat": true` .|

    [Üzerinde örnek config.js](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)tam bir örnek dosya görebilirsiniz.

   > [!TIP]  
   > Sinyaller, oturumların sızmasını sağlamak için gönderilir. Bir bilgisayar uyku moduna geçtiğinde veya kapatıldığında, sinyal gönderilmez ve bu da oturum temizlenmelidir. Kümeler v 3.4 için, bu davranışı devre dışı bırakmak istiyorsanız, Savy yapılandırmasını, `livy.server.interactive.heartbeat.timeout` `0` ambarı kullanıcı arabiriminden olarak ayarlayabilirsiniz. Kümeler v 3.5 için, yukarıdaki 3,5 yapılandırmasını ayarlanmamışsa oturum silinmez.

5. Jupyıter 'ı başlatın. Komut isteminden aşağıdaki komutu kullanın.

    ```cmd
    jupyter notebook
    ```

6. Kernels ile kullanılabilen Spark Magic 'i kullanabileceğiniz doğrulayın. Aşağıdaki adımları tamamlayın.

    a. Yeni bir not defteri oluşturun. Sağ köşedeki **Yeni**' yi seçin. Varsayılan Çekirdek **Python 2** veya **Python 3 ' ü** ve yüklediğiniz çekirdekleri görmeniz gerekir. Gerçek değerler, yükleme seçimlerinize bağlı olarak farklılık gösterebilir.  **Pyspark** seçin.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png " alt-text="Jupyter Notebook 'de kullanılabilir çekirdekler" border="true":::

    > [!IMPORTANT]  
    > **Yeni** seçildikten sonra, herhangi bir hata için kabuğunuz gözden geçirin.  Hatayı görürseniz, `TypeError: __init__() got an unexpected keyword argument 'io_loop'` belirli bir Tornado sürümü ile ilgili bilinen bir sorunla karşılaşmış olabilirsiniz.  Bu durumda, çekirdeği durdurun ve ardından aşağıdaki komutla Tornado yüklemenizi indirgemeniz gerekir: `pip install tornado==4.5.3` .

    b. Aşağıdaki kod parçacığını çalıştırın.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Çıktıyı başarıyla alabiliyorsanız, HDInsight kümesiyle olan bağlantınız test edilir.

    Not defteri yapılandırmasını farklı bir kümeye bağlanacak şekilde güncelleştirmek istiyorsanız, yukarıdaki adım 3 ' te gösterildiği gibi yeni değerler kümesiyle config.jsgüncelleştirin.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Neden Bilgisayarıma Jupyıter yüklemeliyim?

Bilgisayarınıza Jupyter 'yı yüklemeye ve sonra HDInsight 'ta bir Apache Spark kümesine bağlamaya neden olan nedenler:

* , Not defterlerinizi yerel olarak oluşturma, uygulamanızı çalışan bir kümede test etme ve sonra not defterlerini kümeye yükleme seçeneği sunar. Not defterlerini kümeye yüklemek için, veya kümesi çalıştıran Jupyter Notebook kullanarak bunları karşıya yükleyebilir ya da onları `/HdiNotebooks` kümeyle ilişkili depolama hesabındaki klasöre kaydedebilirsiniz. Not defterlerinin kümede nasıl depolandığı hakkında daha fazla bilgi için bkz. [nerede jupi Not defteri depolanıyor](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Yerel olarak kullanılabilir olan Not defterleri sayesinde, uygulama gereksiniminize göre farklı Spark kümelerine bağlanabilirsiniz.
* GitHub kullanarak bir kaynak denetim sistemi uygulayabilir ve Not defterleri için sürüm denetimine sahip olabilirsiniz. Aynı zamanda birden çok kullanıcının aynı not defteriyle çalışılabilecek işbirliği ortamınıza sahip olabilirsiniz.
* Bir kümeye sahip olmaksızın, Not defterlerle yerel olarak çalışabilirsiniz. Not defterlerinizi veya bir geliştirme ortamını el ile yönetmek için not defterlerinizi test etmek üzere bir kümeye ihtiyacınız vardır.
* Kendi yerel geliştirme ortamınızı, küme üzerinde Jupyıter yüklemesini yapılandırdığından yapılandırmak daha kolay olabilir.  Bir veya daha fazla uzak kümeyi yapılandırmadan yerel olarak yüklediğiniz tüm yazılımlardan faydalanabilirsiniz.

> [!WARNING]  
> Yerel bilgisayarınızda Jupyter yüklü olduğunda, birden çok kullanıcı aynı aynı not defterini aynı anda aynı Spark kümesinde çalıştırabilir. Böyle bir durumda, birden çok kıvrımlı oturum oluşturulur. Bir sorunla karşılaşırsanız ve hata ayıklamak istiyorsanız, bu oturum hangi kullanıcının hangi kullanıcıya ait olduğunu izlemek için karmaşık bir görev olacaktır.  

## <a name="next-steps"></a>Sonraki adımlar

* [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)
* [Apache Spark Jupyter Notebook için kernels](apache-spark-jupyter-notebook-kernels.md)
* [Apache Spark 'de jupi Notebook ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
