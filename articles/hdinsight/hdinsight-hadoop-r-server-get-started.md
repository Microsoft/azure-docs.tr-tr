---
title: "HDInsight’ta R Server ile çalışmaya başlama | Microsoft Docs"
description: "HDInsight üzerinde R Server içeren bir Apache Spark oluşturma ve ardından küme üzerinde bir R Server gönderme hakkında bilgi edinin."
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f816a6972c0e80c6a7063705917ecf18debc75f6
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-using-r-server-on-hdinsight"></a>HDInsight üzerinde R Server kullanmaya başlayın
HDInsight, HDInsight kümenizle tümleştirilecek bir R Server seçeneği içerir. Bu seçenek, R betiklerinin dağıtılmış hesaplamaları çalıştırmak için Spark ve MapReduce kullanmasına olanak tanır. Bu belgede, HDInsight kümesi üzerinde bir R Server oluşturma ve ardından dağıtılmış R hesaplamaları için Spark kullanmayı gönderen bir R betiği çalıştırma hakkında bilgi alacaksınız.

## <a name="prerequisites"></a>Ön koşullar
* **Bir Azure aboneliği**: Bu öğreticiye başlamadan önce bir Azure aboneliğinizin olması gerekir. Daha fazla bilgi için [Ücretsiz Azure deneme sürümü edinin](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) bölümüne gidin.
* **Güvenli Kabuk (SSH) istemcisi**: HDInsight kümesine uzaktan bağlanmak ve komutları doğrudan küme üzerinde çalıştırmak için bir SSH istemcisi kullanılır. Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

  * **SSH anahtarları (isteğe bağlı)**: Bir parola veya ortak anahtar kullanarak, kümeye bağlanmak için kullanılan SSH hesabını güvenli hale getirebilirsiniz. Bir parola kullanılması daha kolaydır ve ortak/özel anahtar çifti oluşturmak zorunda kalmadan çalışmaya başlamanızı sağlar. Ancak, bir anahtar kullanılması daha güvenlidir.

      Bu belgedeki adımlarda parola kullandığınız kabul edilmiştir.

### <a name="access-control-requirements"></a>Erişim denetimi gereksinimleri
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-the-cluster"></a>Kümeyi oluşturma
> [!NOTE]
> Bu belgedeki adımlar, temel yapılandırma bilgilerini kullanarak HDInsight kümesi üzerinde R Server oluşturma işleminde size kılavuzluk eder. Diğer küme yapılandırma ayarları için (başka depolama hesapları ekleme, Azure Sanal Ağı kullanma veya Hive için meta veri deposu oluşturma gibi) bkz. [Linux tabanlı HDInsight kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md). Azure Kaynak Yönetimi şablonu ile R Server oluşturmak için bkz. [R-server HDInsight kümesi dağıtma](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).
>
>

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. **YENİ**, **Zeka+Analiz** ve ardından **HDInsight**’ı seçin.

    ![Yeni küme oluşturma görüntüsü](./media/hdinsight-getting-started-with-r/newcluster.png)

3. **Hızlı oluşturma** deneyiminde **Küme Adı** alanına küme için bir ad girin. Birden fazla Azure aboneliğiniz varsa, kullanmak istediğiniz aboneliği seçmek için **Abonelik** girişini kullanın.

    ![Küme adı ve abonelik seçimleri](./media/hdinsight-getting-started-with-r/clustername.png)

4. **Küme yapılandırması** dikey penceresini açmak için **Küme türü**’nü seçin. **Küme Yapılandırması** dikey penceresinde aşağıdaki seçenekleri belirleyin:

   * **Küme Türü**: R Server
   * **Sürüm**: Kümeye yüklenecek R Server sürümünü seçin. En son özellikler için en yeni sürümü seçin. Uyumluluk için gerekirse diğer sürümler kullanılabilir. Kullanılabilir sürümlerin her birine ait sürüm notları [burada](https://msdn.microsoft.com/en-us/microsoft-r/notes/r-server-notes) bulunabilir.
   * **R Server için R Studio topluluk sürümü**: tarayıcı tabanlı bu IDE, kenar düğümüne varsayılan olarak yüklenir.  Yüklememeyi tercih ederseniz, onay kutusunun işaretini kaldırın. Yüklemeyi seçerseniz, RStudio Server oturum açma sayfasına erişim URL’sini, oluşturulan kümenizin portal uygulaması dikey penceresinde bulabilirsiniz.

   Diğer seçenekleri varsayılan değerlerinde bırakın ve küme türünü kaydetmek için **Seç** düğmesini kullanın.

   ![Küme türü dikey penceresi ekran görüntüsü](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)

5. Bir **Küme oturum açma kullanıcı adı** ve **Küme oturum açma parolası** girin.

   Bir **SSH Kullanıcı Adı** belirtin.  SSH, bir **Güvenli Kabuk (SSH)** istemcisi kullanarak kümeye uzaktan bağlanmak için kullanılır. SSH kullanıcısını bu iletişim kutusunda veya küme oluşturulduktan sonra (kümenin Yapılandırma sekmesinde) belirtebilirsiniz. R Server, “remoteuser” şeklinde bir **SSH kullanıcı adı** bekleyecek şekilde yapılandırılmıştır.  **Farklı bir kullanıcı adı kullanırsanız, küme oluşturulduktan sonra ek bir adım gerçekleştirmeniz gerekir.**

   Bir ortak anahtarın kullanılmasını tercih etmiyorsanız, kimlik doğrulama türü olarak **PAROLA** kullanmak için **Kümede oturum açarken kullanılan parolayı kullan** kutusunun işaretini kaldırmayın.  Küme üzerindeki R Server’a RTVS, RStudio veya başka bir masaüstü IDE gibi bir uzak istemci üzerinden erişmek istiyorsanız ortak/özel anahtar çifti gerekir. RStudio Server topluluk sürümünü yüklerseniz bir SSH parolası seçmeniz gerekir.     

   Bir ortak/özel anahtar çifti oluşturmak için **Kümede oturum açarken kullanılan parolayı kullan** kutusunun işaretini kaldırın ve **ORTAK ANAHTAR**’ı seçip aşağıdaki işlemlerle devam edin.  Bu yönergelerde, ssh-keygen veya eşdeğeri ile birlikte Cygwin’in yüklü olduğu varsayılır.

   * Dizüstü bilgisayarınızda komut isteminden bir genel/özel anahtar çifti oluşturun:

   `ssh-keygen -t rsa -b 2048`

   * Anahtar dosyasını adlandırmak için istemleri izleyin ve daha fazla güvenlik için bir parola girin. Ekranınız aşağıdaki gibi görünmelidir:

   ![Windows'da SSH cmd satırı](./media/hdinsight-getting-started-with-r/sshcmdline.png)

   * Bu satır <private-key-filename>.pub adıyla bir özel anahtar dosyası ve ortak anahtar dosyası oluşturur, örneğin furiosa ve furiosa.pub.

   ![SSH dir](./media/hdinsight-getting-started-with-r/dir.png)

   * HDI küme kimlik bilgilerini atarken ortak anahtar dosyasını (*.pub) belirtin ve son olarak kaynak grubunuz ile bölgenizi onaylayıp **İleri**’yi seçin

   ![Kimlik Bilgileri dikey penceresi](./media/hdinsight-getting-started-with-r/publickeyfile.png)  

   * Dizüstü bilgisayarınızda özel anahtar dosyasına ilişkin izinleri değiştirme

   `chmod 600 <private-key-filename>`

   * Özel anahtar dosyasını uzaktan oturum açma için SSH ile birlikte

   `ssh –i <private-key-filename> remoteuser@<hostname public ip>`

   veya istemci üzerinde R Server için Hadoop Spark işlem bağlamınızın tanımı kapsamında kullanın (çevrimiçi [Apache Spark üzerinde SacaleR kullanmaya başlama belgesinin](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) [Spark için İşlem Bağlamı Oluşturma](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) bölümünde Microsoft R Server’ı Hadoop İstemcisi Olarak Kullanma kısmına bakın.)

6. Hızlı oluşturma işlemi, küme tarafından kullanılan HDFS dosya sisteminin birincil konumu olarak kullanılacak depolama hesabı ayarlarını seçmek için sizi **Depolama** dikey penceresine geçirir. Yeni veya mevcut bir Azure Depolama hesabını ya da mevcut bir Data Lake Storage hesabını seçin.

   1. Bir Azure Depolama hesabı seçerseniz, **Depolama hesabı seçin** öğesini ve ardından hesabı seçerek mevcut bir depolama hesabını seçebilirsiniz. Veya **Depolama hesabı seçin** bölümündeki **Yeni Oluştur** bağlantısını kullanarak yeni bir hesap oluşturabilirsiniz.

      > [!NOTE]
      > **Yeni**’yi seçerseniz, yeni depolama hesabınız için bir ad girmeniz gerekir. Ad kabul edilirse yeşil bir onay işareti görünür.

      **Varsayılan Kapsayıcı**, varsayılan olarak kümenin adıdır. Bu değeri olduğu gibi bırakın.

      Yeni depolama hesabı seçeneği belirlendiyse, depolama hesabının oluşturulacağı bölgeyi seçmek için **Konum** belirlemeniz istenir.  

         ![Veri kaynağı dikey penceresi](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > Varsayılan veri kaynağı için konum seçildiğinde, HDInsight kümesinin konumu da ayarlanır. Küme ve varsayılan veri kaynağı aynı bölgede olmalıdır.

   2. Mevcut bir Data Lake Store hesabı kullanmayı seçerseniz, kullanılacak ADLS depolama hesabını belirleyin ve depo erişimine izin vermek üzere küme ADD kimliğini kümenize ekleyin. Bu işlem hakkında daha fazla bilgi için [Azure portalı kullanarak Data Lake Store ile HDInsight kümesi oluşturma](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal) bölümünü gözden geçirin.

   Veri kaynağı yapılandırmasını kaydetmek için **Seç** düğmesini kullanın.


7. Bu durumda tüm ayarlarını doğrulamak için **Özet** dikey penceresi görüntülenir. Bu pencerede, kümenizdeki sunucu sayısını değiştirmek ve aynı zamanda çalıştırmak istediğiniz **Betik eylemlerini** belirtmek için **Küme boyutunuzu** değiştirebilirsiniz. Daha büyük bir kümeye ihtiyaç duymadıkça, çalışan düğümleri sayısını varsayılan `4` değerinde bırakın. Kümenin tahmini maliyeti, dikey pencerede gösterilir.

   ![küme özeti](./media/hdinsight-getting-started-with-r/clustersummary.png)

   > [!NOTE]
   > Gerekirse, çalışan düğümlerinin sayısını artırmak veya azaltmak üzere Portal üzerinden (Küme -> Ayarlar -> Küme Ölçeklendirme) kümenizi yeniden boyutlandırabilirsiniz.  Bunun yapılması, kullanımda olmadığında kümeyi boşa almak veya daha büyük görev gereksinimlerini karşılamak üzere kapasite artırmak için faydalı olabilir.
   >
   >

    Kümenizi, veri düğümlerini ve kenar düğümünü boyutlandırırken göz önünde bulundurulması gereken bazı faktörler aşağıda verilmiştir:  

   * Spark üzerinde dağıtılmış R Server analizlerinin performansı, veriler büyük olduğunda çalışan düğümlerinin sayısıyla doğru orantılıdır.  

   * R Server analizlerinin performansı, analiz edilmekte olan verilerin boyutu ile doğrusal yönde ilerler. Örneğin:  

     * Küçük ila orta büyüklükteki veriler için performans, kenar düğümündeki yerel bir işlem bağlamında analiz edildiğinde en üst seviyededir.  Yerel ve Spark işlem bağlamlarının en iyi şekilde çalıştığı senaryolar hakkında daha fazla bilgi için bkz. HDInsight üzerinde R Server için işlem bağlamı seçenekleri.<br>
     * Kenar düğümünde oturum açıp R betiğinizi çalıştırırsanız, ScaleR rx-haricindeki tüm işlevler kenar düğümünde <strong>yerel olarak</strong> yürütülür, bu nedenle kenar düğümünün belleği ve çekirdek sayısı buna uygun olarak boyutlandırılmalıdır. Aynı durum, HDI üzerinde R Server’ı dizüstü bilgisayarınızdan uzak bir işlem bağlamı olarak çalıştırdığınızda geçerli olur.

     ![Düğüm fiyatlandırma katmanları dikey penceresi](./media/hdinsight-getting-started-with-r/pricingtier.png)

     Düğüm fiyatlandırma yapılandırmasını kaydetmek için **Seç** düğmesini kullanın.

   Burada **Şablon ve parametreleri indirme** bağlantısının da bulunduğunu fark edeceksiniz. Bu bağlantıya tıkladığınızda, seçili yapılandırma ile küme oluşturma işlemini otomatik hale getirmek için kullanılabilecek betikler gösterilir. Bu betikler, kümeniz oluşturulduktan sonra Azure portalı girişinde de bulunabilir.

   > [!NOTE]
   > Kümenin oluşturulması genelikle yaklaşık 20 dakika sürer. Oluşturma işlemini denetlemek için Başlangıç Panosu’ndaki kutucuğu veya sayfanın solundaki **Bildirimler** girişini kullanın.
   >
   >

## <a name="connect-to-rstudio-server"></a>RStudio Server’a bağlanma

Yüklemenize RStudio Server topluluk sürümünü eklemeyi seçtiyseniz, RStudio oturum açma sayfasına iki farklı yöntemle erişebilirsiniz.

1. Aşağıdaki URL’ye giderek (burada **CLUSTERNAME**, oluşturduğunuz kümenin adıdır):

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. Veya Azure portalında kümenizin girişini açıp, R Server Panoları hızlı bağlantısını seçip, R Studio Panosu’nu seçerek:

     ![R studio panosuna erişim](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![R studio panosuna erişim](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > Yöntem ne olursa olsun, ilk kez oturum açtığınızda iki kez kimlik doğrulaması yapmanız gerekir.  İlk kimlik doğrulamasında kümenin Yönetici kullanıcı kimliğini ve parolasını belirtin. İkinci istemde SSH kullanıcı kimliği ve parolasını sağlayın. Sonraki oturumlarda yalnızca SSH parolası ve kullanıcı kimliği gerekli olacaktır.

## <a name="connect-to-the-r-server-edge-node"></a>R Server kenar düğümüne bağlanma
HDInsight kümesinin R Server kenar düğümüne SSH kullanarak bağlanma:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> Ayrıca, kümenizi ve ardından **Tüm Ayarlar**, **Uygulamalar** ve **RServer**’ı seçerek `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` adresini Azure portalında bulabilirsiniz. Bu seçim, kenar düğümünün SSH Uç Noktası bilgilerini gösterir.
>
> ![Kenar düğümünün SSH Uç Noktası görüntüsü](./media/hdinsight-getting-started-with-r/sshendpoint.png)
>
>

SSH kullanıcı hesabınızın güvenliğini sağlamak için parola kullandıysanız parolayı girmeniz istenir. Bir ortak anahtar kullandıysanız eşleşen özel anahtarı belirtmek için `-i` parametresini kullanmanız gerekebilir. Örneğin, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`.

Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

Bağlantı kurulduktan sonra aşağıdakine benzer bir isteme ulaşırsınız.

`username@ed00-myrser:~$`

## <a name="use-the-r-console"></a>R konsolunu kullanma

1. R konsolunu başlatmak için SSH oturumunda aşağıdaki komutu kullanın.  

   ```
   R

   You will see output similar to the following.
   R version 3.2.2 (2015-08-14) -- "Fire Safety"
   Copyright (C) 2015 The R Foundation for Statistical Computing
   Platform: x86_64-pc-linux-gnu (64-bit)

   R is free software and comes with ABSOLUTELY NO WARRANTY.
   You are welcome to redistribute it under certain conditions.
   Type 'license()' or 'licence()' for distribution details.

   Natural language support but running in an English locale

   R is a collaborative project with many contributors.
   Type 'contributors()' for more information and
   'citation()' on how to cite R or R packages in publications.

   Type 'demo()' for some demos, 'help()' for on-line help, or
   'help.start()' for an HTML browser interface to help.
   Type 'q()' to quit R.

   Microsoft R Server version 8.0: an enhanced distribution of R
   Microsoft packages Copyright (C) 2016 Microsoft Corporation

   Type 'readme()' for release notes.
   >
   ```

2. `>` isteminde R kodunu girebilirsiniz. R server, Hadoop ile kolayca etkileşim kurup dağıtılmış hesaplamaları çalıştırmanıza olanak tanıyan paketler içerir. Örneğin, HDInsight kümesinin varsayılan dosya sisteminin kökünü görüntülemek için aşağıdaki komutu kullanın.

`rxHadoopListFiles("/")`

WASB stil adreslemesini de kullanabilirsiniz.

`rxHadoopListFiles("wasbs:///")`

## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Microsoft R Server veya Microsoft R Client uzak örneğinden HDI üzerinde R Server kullanma
Kümeye erişim için ortak/özel anahtar çiftleri kullanmayla ilgili yukarıdaki bölüm uyarınca, masaüstü veya dizüstü bilgisayarda çalışan uzak bir Microsoft R Server veya Microsoft R Client örneğinden HDI Hadoop Spark işlem bağlamına erişimin ayarlanması mümkündür (çevrimiçi [RevoScaleR Hadoop Spark Başlarken kılavuzunun](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) [Spark için İşlem Bağlamı Oluşturma](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) bölümündeki Microsoft R Server’ı Hadoop İstemcisi Olarak Kullanma kısmına bakın).  Bunu yapmak için dizüstü bilgisayarınızda RxSpark işlem bağlamını tanımlarken şu seçenekleri belirtmeniz gerekir: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches ve sshProfileScript. Örneğin:

```
    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )
```


## <a name="use-a-compute-context"></a>İşlem bağlamı kullanma
Bir işlem bağlamı, hesaplamanın kenar düğümünde yerel olarak yapılmasını veya HDInsight kümesindeki düğümlere dağıtılmasını denetlemenize olanak tanır.

1. RStudio Server veya R konsolundan (bir SSH oturumunda), varsayılan HDInsight depolama alanına örnek verileri yüklemek için aşağıdakileri kullanın.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Bundan sonra, verilerle çalışabilmek için bazı veri bilgileri oluşturup iki veri kaynağı tanımlayabiliriz.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Yerel işlem bağlamını kullanarak, veriler üzerinde lojistik regresyon gerçekleştirelim.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Aşağıdakilere benzer satırlarla sona eren bir çıktı görmeniz gerekir:

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. Şimdi de Spark bağlamını kullanarak aynı lojistik regresyonu gerçekleştirelim. Spark bağlamı, işlemi HDInsight kümesindeki tüm çalışan düğümlerine dağıtır.

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        
        # Display a summary
        summary(modelSpark)


   > [!NOTE]
   > Ayrıca, MapReduce kullanarak hesaplamayı küme düğümleri arasında dağıtabilirsiniz. İşlem bağlamı hakkında daha fazla bilgi için bkz. [HDInsight üzerinde R Server için işlem bağlamı seçenekleri](hdinsight-hadoop-r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>R kodunu birden fazla düğüme dağıtma
R Server ile mevcut R kodunu kolayca alabilir ve `rxExec` kullanarak kümedeki birden fazla düğümde çalıştırabilirsiniz. Bunun yapılması bir parametre tarama veya benzetme işlemi sırasında yararlıdır. `rxExec` kullanımını gösteren bir örnek aşağıda verilmiştir.

`rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )`

Hala Spark veya MapReduce bağlamını kullanıyorsanız, bu işlem, üzerinde `(Sys.info()["nodename"])` kodunun çalıştığı çalışan düğümleri için nodename değerini döndürür. Örneğin, dört düğümlü bir kümede aşağıdakine benzer bir çıktı alırsınız.


    ```
    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"
    ```

## <a name="accessing-data-in-hive-and-parquet"></a>Hive ve Parquet Verilerine Erişim
R Server 9.0 ve sonraki sürümlerinde sunulan yeni bir özellik, Spark işlem bağlamındaki ScaleR işlevleri tarafından kullanım için Hive ve Parquet içindeki verilere doğrudan erişime olanak tanır. Bu özellikler, ScaleR tarafından analiz edilmek üzere bir Spart DataFrame’e doğrudan veri yüklemek için Spark SQL kullanarak çalışan RxHiveData ve RxParquetData adlı yeni ScaleR veri kaynağı işlevleriyle kullanılabilir.  

Yeni işlevlerin kullanımına ilişkin bazı örnek kodlar aşağıda verilmiştir:



    ```
    #..create a Spark compute context

    myHadoopCluster <- rxSparkConnect(reset = TRUE)
    ```


    ```
    #..retrieve some sample data from Hive and run a model

    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)
    ```

    ```
    #..retrieve some sample data from Parquet and run a model

    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)
    ```


    ```
    #..check on Spark data objects, cleanup, and close the Spark session

    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)
    ```

Bu yeni işlevlerin kullanımına ilişkin ek bilgi için, ?RxHivedata ve ?RxParquetData komutlarını kullanarak R Server içindeki çevrimiçi yardıma bakın.  


## <a name="install-r-packages"></a>R paketlerini yükleme
Kenar düğümüne ek R paketleri yüklemek isterseniz, SSH ile kenar düğümüne bağlı olduğunda doğrudan R konsolu içinden `install.packages()` kullanabilirsiniz. Ancak, kümenin çalışan düğümlerine R paketleri yüklemeniz gerekiyorsa bir Betik Eylemi kullanmanız gerekir.

Betik Eylemleri, HDInsight kümesinde yapılandırma değişiklikleri yapmak veya ek yazılım yüklemek için kullanılan Bash betikleridir. Bu örnekte, ek R paketleri yüklemek için kullanılır. Betik Eylemi kullanarak ek paketler yüklemek için aşağıdaki adımları kullanın.

> [!IMPORTANT]
> Ek R paketleri yüklemek için Betik Eylemleri yalnızca küme oluşturulduktan sonra kullanılabilir. Betik R Server’ın tamamen yüklü ve yapılandırılmış olmasına bağlı olduğundan, küme oluşturma sırasında kullanılmamalıdır.
>
>

1. [Azure portalı](https://portal.azure.com)’ndan, HDInsight kümesindeki R Server’ınızı seçin.

2. **Ayarlar** dikey penceresinde **Betik Eylemleri** ve ardından **Yeni Gönder**’i seçerek yeni Betik Eylemini gönderin.

   ![Betik eylemleri dikey penceresinin görüntüsü](./media/hdinsight-getting-started-with-r/scriptaction.png)

3. **Betik eylemini gönder** dikey penceresinde aşağıdaki bilgileri sağlayın.

   * **Ad**: Betiği tanımlayan kolay ad

   * **Bash betiği URI’si**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Üst bilgi**: Bu değer **işaretlenmemiş** olmalıdır

   * **Çalışan**: Bu değer **işaretlenmiş** olmalıdır

   * **Kenar düğümleri**: Bu değer **işaretlenmemiş** olmalıdır.

   * **Zookeeper**: Bu değer **işaretlenmemiş** olmalıdır

   * **Parametreler**: Yüklenecek R paketleri. Örneğin, `bitops stringr arules`

   * **Bu betiği kalıcı yap...**: Bu değer **İşaretlenmiş** olmalıdır  

   > [!NOTE]
   > 1. Varsayılan olarak, tüm R paketleri Microsoft MRAN deposunun yüklü olan R Server sürümüyle tutarlı bir anlık görüntüsünden yüklenir.  Paketlerin yeni sürümlerini yüklemek isterseniz bir uyumsuzluk riski oluşur, ancak paket listesinin ilk öğesi olarak `useCRAN` değerini belirterek bunu yapabilirsiniz (örneğin `useCRAN bitops, stringr, arules`).  
   > 2. Bazı R paketleri için ek Linux sistem kitaplıkları gerekir. Kolaylık olması için en popüler 100 R paketi için gerekli olan bağımlılıklar önceden yüklenmiştir. Ancak, yüklediğiniz R paketleri bunların dışında kitaplıklar gerektirirse, burada kullanılan temel betiği indirmeniz ve adımları ekleyerek sistem kitaplıklarını yüklemeniz gerekir. Ardından, değiştirilmiş betiği Azure depolama hizmetindeki ortak bir blob kapsayıcıya yüklemeniz ve değiştirilmiş betiği kullanarak paketleri yüklemeniz gerekir.
   >    Betik Eylemleri geliştirme hakkında bilgi için bkz. [Betik Eylemi geliştirme](hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Betik eylemi ekleme](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. Betiği çalıştırmak için **Oluştur**’u seçin. Betik tamamlandıktan sonra R paketleri tüm çalışan düğümlerinde kullanılabilir.

## <a name="using-microsoft-r-server-operationalization"></a>Microsoft R Server ile Kullanıma Hazır Hale Getirme
Veri modellemesi tamamlandığında, tahminlerde bulunmak üzere modelinizi kullanıma hazır hale getirebilirsiniz. Microsoft R Server ile kullanıma hazır hale getirme özelliğini yapılandırmak için aşağıdaki adımları uygulayın.

İlk olarak, ssh’yi Kenar düğümüne gönderin. Örneğin, ```ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net```.

Ssh kullandıktan sonra dizini aşağıdaki dizinle değiştirin ve dotnet dll dosyasına aşağıda gösterildiği gibi sudo uygulayın.

```
   cd /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil
   sudo dotnet Microsoft.DeployR.Utils.AdminUtil.dll
```

Microsoft R Server ile kullanıma hazır hale getirme özelliğini One-box yapılandırması ile yapılandırmak için aşağıdaki işlemleri yapın;

* “1. R Server’ı Kullanıma Hazır Hale Getirmek için Yapılandır” öğesini seçin
* “A. One-box (web + işlem düğümleri)” öğesini seçin
* **Yönetici** kullanıcı için bir parola girin

![one box işlemi](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

İsteğe bağlı bir adım olarak aşağıda gösterildiği gibi bir tanılama testi çalıştırarak Tanılama denetimleri gerçekleştirebilirsiniz.

* “6. Tanılama testleri çalıştır” öğesini seçin
* “A. Test yapılandırması” öğesini seçin
* Kullanıcı adı olarak “admin” değerini ve yukarıdaki yapılandırma adımında belirtilen parolayı girin
* Genel Sistem Durumunu Onayla = başarılı
* Yönetim Yardımcı Programından çıkın
* SSH’den çıkın

![İşlem için tanılama](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)

Bu aşamada kullanıma hazır hale getirme yapılandırması tamamlanmıştır. Bundan sonra Kenar düğümünde Kullanıma Hazır Hale Getirmeye bağlanmak üzere RClient üzerindeki ‘mrsdeploy’ paketini kullanabilir ve [uzaktan yürütme](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) ile [web hizmetleri](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette) gibi özellikleri kullanmaya başlayabilirsiniz. Kümenizin bir sanal ağda ayarlanıp ayarlanmamasına bağlı olarak, aşağıda açıklandığı gibi SSH oturumu aracılığıyla bağlantı noktası iletme tüneli ayarlamanız gerekebilir:

### <a name="rserver-cluster-on-virtual-network"></a>Sanal ağda RServer Kümesi

12800 bağlantı noktası üzerinden Kenar düğümüne trafik akışına izin verdiğinizden emin olun. Bu şekilde, Kullanıma Hazır Hale Getirme özelliğine bağlanmak için Kenar düğümünü kullanabilirsiniz.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

remoteLogin() öğesi Kenar düğümüne bağlanamadığı halde Kenar düğümüne SSH uygulayabiliyorsanız, 12800 bağlantı noktası üzerinde trafiğe izin veren kuralın doğru şekilde ayarlanıp ayarlanmadığını doğrulamanız gerekir. Sorunla karşılaşmaya devam ederseniz, SSH üzerinden bağlantı noktası iletme tüneli oluşturarak bir geçici çözüm uygulayabilirsiniz.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>Sanal ağda RServer Kümesi ayarlanmamış

Kümeniz sanal üzerinde ayarlanmamışsa VEYA sanal ağ üzerinden bağlantı kurma sorunları yaşıyorsanız, SSH bağlantı noktası iletme tünelini aşağıdaki gibi kullanabilirsiniz:

```
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Putty üzerinde de bu ayarı yapabilirsiniz.

![putty ssh bağlantısı](./media/hdinsight-hadoop-r-server-get-started/putty.png)

SSH oturumunuz etkin hale geldikten sonra, makinenizin 12800 bağlantı noktasından giden trafik, SSH aracılığıyla Kenar düğümünün 12800 bağlantı noktasına iletilir. remoteLogin() yönteminizde `127.0.0.1:12800` kullandığınızdan emin olun. Bunun yapılması, bağlantı noktası iletme yoluyla Kenar düğümünün kullanıma hazır hale getirme özelliğinde oturum açar.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>HDInsight çalışan düğümlerinde Microsoft R Server Kullanıma Hazır Hale Getirme işlem düğümleri nasıl ölçeklendirilir?


### <a name="decommission-the-worker-nodes"></a>Çalışan düğümlerinin yetkisini alma
Microsoft R Server şu anda Yarn üzerinden yönetilmemektedir. Çalışan düğümlerinin yetkisi alınmazsa, Yarn kaynak yöneticisi sunucu tarafından alınan kaynakları fark edemeyeceği için beklendiği gibi çalışmaz. Bunu önlemek için, işlem düğümlerini ölçeklendirmek istediğiniz çalışan düğümlerinin yetkisinin alınması önerilir.

Çalışan düğümlerinin yetkisini alma adımları:

* HDI kümesinin Ambari konsolunda oturum açıp "ana bilgisayarlar" sekmesine tıklayın
* Çalışan düğümlerini (yetkisi alınacak olanlar) seçin, "Eylemler" > "Seçili Ana Bilgisayarlar" > "Ana Bilgisayarlar" > "Bakım Modunu Aç" öğesine tıklayın. Örneğin, aşağıdaki görüntüde yetkisini almak üzere wn3 ve wn4 seçilmiştir.  

   ![çalışan düğümlerinin yetkisini alma](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* "Eylemler" > "Seçili Ana Bilgisayarlar" > "DataNodes" > öğesini seçip "Yetkisini Al" öğesine tıklayın
* "Eylemler" > "Seçili Ana Bilgisayarlar" > "NodeManagers" > öğesini seçip "Yetkisini Al" öğesine tıklayın
* "Eylemler" > "Seçili Ana Bilgisayarlar" > "DataNodes" > öğesini seçip "Durdur" öğesine tıklayın
* "Eylemler" > "Seçili Ana Bilgisayarlar" > "NodeManagers" > öğesini seçip "Durdur" öğesine tıklayın
* "Eylemler" > "Seçili Ana Bilgisayarlar" > "Ana Bilgisayarlar" > öğesini seçip "Tüm Bileşenleri Durdur" öğesine tıklayın
* Çalışan düğümlerinin seçimini kaldırın ve baş düğümleri seçin
* "Eylemler" > "Seçili Ana Bilgisayarlar" > "Ana Bilgisayarlar" > "Tüm Bileşenleri Yeniden Başlat” öğesini seçin


###    <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Yetkisi alınan her çalışan düğümü üzerinde İşlem düğümlerini yapılandırın

* Yetkisi alınan her çalışan düğümüne SSH uygulayın
* `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll` kullanarak yönetici yardımcı programını çalıştırın
* "1" girerek "1. R Server’ı Kullanıma Hazır Hale Getirmek için Yapılandır" seçeneğini belirleyin
* "c" girerek "C. İşlem düğümü" öğesini seçin. Bunun yapılması çalışan düğümündeki işlem düğümünü yapılandırır.
* Yönetim Yardımcı Programından çıkın

### <a name="add-compute-nodes-details-on-web-node"></a>Web Düğümüne işlem düğümleri ekleme
Yetkisi alınan tüm çalışan düğümleri işlem düğümü üzerinde çalışacak şekilde yapılandırıldıktan sonra, Kenar düğümüne geri dönün ve yetkisi alınmış çalışan düğümlerinin IP adreslerini Microsoft R Server web düğümünün yapılandırmasına ekleyin:

* Kenar düğümüne SSH uygulayın
* `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` öğesini çalıştırın
* "URI’ler" bölümüne bakın ve çalışan düğümünün IP ve bağlantı noktası bilgilerini ekleyin.

![çalışan düğümlerinin yetkisini alma komut satırı](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)

## <a name="next-steps"></a>Sonraki adımlar
R Server içeren yeni bir HDInsight kümesi oluşturmayı ve bir SSH oturumunda R konsolu kullanmaya ilişkin temel bilgileri öğrendiğinize göre, HDInsight üzerinde R Server ile çalışmanın diğer yollarını bulmak için aşağıdakileri kullanın.

* [HDInsight’a RStudio Server Ekleme (küme oluşturma sırasında yüklenmediyse)](hdinsight-hadoop-r-server-install-r-studio.md)
* [HDInsight üzerinde R Server için işlem bağlamı seçenekleri](hdinsight-hadoop-r-server-compute-contexts.md)
* [HDInsight üzerinde R Server için Azure Depolama seçenekleri](hdinsight-hadoop-r-server-storage.md)

