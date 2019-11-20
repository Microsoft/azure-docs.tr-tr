---
title: Jupyıter 'ı yerel olarak yükleyip Azure HDInsight 'ta Spark 'a bağlanma
description: Jupyter Not defterini bilgisayarınıza yerel olarak yüklemeyi ve bir Apache Spark kümesine bağlamayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 225ee7028b9610a4974f9bee05da667d78d3355e
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903745"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Jupyter Not defterini bilgisayarınıza yükleyip HDInsight üzerinde Apache Spark bağlanın

Bu makalede, Jupyter Not defterini, Spark Magic ile özel PySpark (Python için) ve Apache Spark (Scala için) ile nasıl yükleyeceğinizi öğrenirsiniz ve Not defteri bir HDInsight kümesine bağlanır. Yerel bilgisayarınıza jupi 'yi yüklemek için çeşitli nedenlerden bazıları olabilir ve bazı sorunlar da oluşabilir. Bu konuda daha fazla bilgi için, bu makalenin sonunda [Bilgisayarımda jupi 'Yi neden yüklemeliyim](#why-should-i-install-jupyter-on-my-computer) bölümüne bakın.

Jupyıter yükleme ve HDInsight üzerinde Apache Spark bağlanma konusunda dört temel adım vardır.

* Spark kümesini yapılandırın.
* Jupyter Not defterini yükler.
* Spark Magic ile pyspark ve Spark çekirdekler 'i yükler.
* Spark Magic 'i HDInsight 'ta Spark kümesine erişecek şekilde yapılandırın.

HDInsight kümesi ile Jupyter Not defterleri için kullanılabilen özel çekirdekler ve Spark Magic hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Spark Linux kümeleri olan Jupyter Not defterleri için sunulan çekirdekler](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md). Bu, bir not defteri yüklendikten sonra Jupyter Not defterini HDInsight kümesine bağlamak için bir önkoşuldur.

* HDInsight üzerinde Spark ile Jupyter Notebook kullanma bilgisi.

## <a name="install-jupyter-notebook-on-your-computer"></a>Jupyter Not defterini bilgisayarınıza yükler

Jupyıter not defterlerini yükleyebilmek için Python 'u yüklemelisiniz. [Anaconda dağıtımı](https://www.anaconda.com/download/) , hem Python hem de Jupyter Notebook yükler.

Platformunuz için [Anaconda yükleyicisini](https://www.anaconda.com/download/) indirin ve kurulumu çalıştırın. Kurulum sihirbazını çalıştırırken, yol değişkeniniz için Anaconda ekleme seçeneğini seçtiğinizden emin olun.  Ayrıca bkz. [Anaconda kullanarak Jupyıter yükleme](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Spark Magic 'i yükler

1. Spark Magic 'i yüklemek için aşağıdaki komutlardan birini girin. Ayrıca bkz. [mini sihirli belgeler](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Küme sürümü | Komutu Install |
    |---|---|
    |v 3.6 ve v 3.5 |`pip install sparkmagic==0.13.1`|
    |v 3.4|`pip install sparkmagic==0.2.3`|

1. Aşağıdaki komutu çalıştırarak `ipywidgets` düzgün yüklendiğinden emin olun:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Pyspark ve Spark çekirdekler 'i yükler

1. Aşağıdaki komutu girerek `sparkmagic` nereye yükleneceğini belirler:

    ```cmd
    pip show sparkmagic
    ```

    Sonra çalışma dizininizi yukarıdaki komutla belirtilen konuma göre değiştirin.

1. Yeni çalışma dizininizden, istenen çekirdek (ler) i yüklemek için aşağıdaki komutlardan birini veya birkaçını girin:

    |İne | Komut |
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

2. Jupyıter yapılandırma bilgileri genellikle kullanıcılar giriş dizininde depolanır. Giriş dizinini tanımlamak için aşağıdaki komutu girin ve **. Mini sihirli**adlı bir klasör oluşturun.  Tam yol silinecek.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. `.sparkmagic`klasörü içinde **config. JSON** adlı bir dosya oluşturun ve IÇINE aşağıdaki JSON kod parçacığını ekleyin.  

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
    |NITELEN|Küme oturumu açma, varsayılan `admin`.|
    |CLUSTERDNSNAME|Küme adı|
    |{BASE64ENCODEDPASSWORD}|Gerçek parolanız için Base64 olarak kodlanmış bir parola.  [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/)bir Base64 parolası oluşturabilirsiniz.|
    |`"livy_server_heartbeat_timeout_seconds": 60`|`sparkmagic 0.12.7` (kümeler v 3.5 ve v 3.6) kullanıyorsanız koruyun.  `sparkmagic 0.2.3` (kümeler v 3.4) kullanıyorsanız, `"should_heartbeat": true`ile değiştirin.|

    [Örnek config. JSON](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)dosyasında tam bir örnek dosyası görebilirsiniz.

   > [!TIP]  
   > Sinyaller, oturumların sızmasını sağlamak için gönderilir. Bir bilgisayar uyku moduna geçtiğinde veya kapatıldığında, sinyal gönderilmez ve bu da oturum temizlenmelidir. Kümeler v 3.4 için, bu davranışı devre dışı bırakmak istiyorsanız, sanal kullanıcı arabiriminden `0` için Livy yapılandırma `livy.server.interactive.heartbeat.timeout` ayarlayabilirsiniz. Kümeler v 3.5 için, yukarıdaki 3,5 yapılandırmasını ayarlanmamışsa oturum silinmez.

5. Jupyıter 'ı başlatın. Komut isteminden aşağıdaki komutu kullanın.

    ```cmd
    jupyter notebook
    ```

6. Kernels ile kullanılabilen Spark Magic 'i kullanabileceğiniz doğrulayın. Aşağıdaki adımları uygulayın.

    a. Yeni bir not defteri oluşturun. Sağ köşedeki **Yeni**' yi seçin. Varsayılan Çekirdek **Python 2** veya **Python 3 ' ü** ve yüklediğiniz çekirdekleri görmeniz gerekir. Gerçek değerler, yükleme seçimlerinize bağlı olarak farklılık gösterebilir.  **Pyspark**seçin.

    ![Jupyter not defterinde kullanılabilir çekirdekler](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Jupyter not defterinde kernels")

    > [!IMPORTANT]  
    > **Yeni** seçildikten sonra, herhangi bir hata için kabuğunuz gözden geçirin.  `TypeError: __init__() got an unexpected keyword argument 'io_loop'` hatayı görürseniz, bazı Tornado sürümleriyle ilgili bilinen bir sorun yaşıyor olabilirsiniz.  Bu durumda, çekirdeği durdurun ve ardından Tornado yüklemenizin aşağıdaki komutla indirgenmesini sağlayın: `pip install tornado==4.5.3`.

    b. Aşağıdaki kod parçacığını çalıştırın.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Çıktıyı başarıyla alabiliyorsanız, HDInsight kümesiyle olan bağlantınız test edilir.

    Not defteri yapılandırmasını farklı bir kümeye bağlanacak şekilde güncelleştirmek istiyorsanız, config. json dosyasını yukarıdaki adım 3 ' te gösterildiği gibi yeni değerler kümesiyle güncelleştirin.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Neden Bilgisayarıma Jupyıter yüklemeliyim?

Bilgisayarınıza jupi 'yi yüklemek ve sonra HDInsight 'ta bir Apache Spark kümesine bağlamak isteyebileceğiniz birçok neden olabilir.

* Jupyter Not defterleri Azure HDInsight 'taki Spark kümesinde zaten mevcut olsa da, bilgisayarınıza Jupyter yükleme, yerel olarak not defterlerini oluşturma, uygulamanızı çalışan bir kümede test etme ve sonra kümedeki Not defterleri. Not defterlerini kümeye yüklemek için, veya kümesi çalıştıran Jupyter Not defterini kullanarak bunları karşıya yükleyebilir ya da bunları kümeyle ilişkili depolama hesabındaki/HdiNotebooks klasörüne kaydedebilirsiniz. Not defterlerinin kümede nasıl depolandığı hakkında daha fazla bilgi için bkz. [nerede jupi Not defteri depolanıyor](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Yerel olarak kullanılabilir olan Not defterleri sayesinde, uygulama gereksiniminize göre farklı Spark kümelerine bağlanabilirsiniz.
* GitHub kullanarak bir kaynak denetim sistemi uygulayabilir ve Not defterleri için sürüm denetimine sahip olabilirsiniz. Aynı zamanda birden çok kullanıcının aynı not defteriyle çalışılabilecek işbirliği ortamınıza sahip olabilirsiniz.
* Bir kümeye sahip olmaksızın, Not defterlerle yerel olarak çalışabilirsiniz. Not defterlerinizi veya bir geliştirme ortamını el ile yönetmek için not defterlerinizi test etmek üzere bir kümeye ihtiyacınız vardır.
* Kendi yerel geliştirme ortamınızı, küme üzerinde Jupyıter yüklemesini yapılandırdığından yapılandırmak daha kolay olabilir.  Bir veya daha fazla uzak kümeyi yapılandırmadan yerel olarak yüklediğiniz tüm yazılımlardan faydalanabilirsiniz.

> [!WARNING]  
> Yerel bilgisayarınızda Jupyter yüklü olduğunda, birden çok kullanıcı aynı aynı not defterini aynı anda aynı Spark kümesinde çalıştırabilir. Böyle bir durumda, birden çok kıvrımlı oturum oluşturulur. Bir sorunla karşılaşırsanız ve hata ayıklamak istiyorsanız, bu oturum hangi kullanıcının hangi kullanıcıya ait olduğunu izlemek için karmaşık bir görev olacaktır.  

## <a name="next-steps"></a>Sonraki adımlar

* [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)
* [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](apache-spark-use-bi-tools.md)
* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
