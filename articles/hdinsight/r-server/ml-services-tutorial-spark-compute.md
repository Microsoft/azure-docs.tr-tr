---
title: "Öğretici: Azure HDInsight 'ta Spark işlem bağlamında R kullanma"
description: Öğretici-Azure HDInsight Machine Learning Hizmetleri kümesinde R ve Spark ile çalışmaya başlayın.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 73ca0d089ab758fb13e69d341337139d79194cc5
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121931"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Öğretici: Azure HDInsight 'ta Spark işlem bağlamında R kullanma

Bu öğreticide, Azure HDInsight Machine Learning Hizmetleri kümesinde çalışan Apache Spark R işlevlerinin kullanılmasına yönelik adım adım bir giriş sunulmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Örnek verileri yerel depolamaya indirme
> * Verileri varsayılan depolamaya Kopyala
> * Veri kümesi ayarlama
> * Veri kaynakları oluşturma
> * Spark için işlem bağlamı oluşturma
> * Doğrusal bir modeli sığdırma
> * Bileşik XDF dosyalarını kullan
> * XDF 'u CSV 'ye Dönüştür

## <a name="prerequisites"></a>Önkoşullar

* Azure HDInsight Machine Learning Hizmetleri kümesi. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) bölümüne gidin ve **küme türü**için **ml Hizmetleri**' ni seçin.

## <a name="connect-to-rstudio-server"></a>RStudio Server’a bağlanma

RStudio sunucusu kümenin Edge düğümünde çalışır. Aşağıdaki siteye gidin (URL 'deki *clustername* , oluşturduğunuz HDInsight Machine Learning Services kümesinin adıdır):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

İlk kez oturum açtığınızda iki kez kimlik doğrulaması yapmanız gerekir. İlk kimlik doğrulaması isteminde, Küme Yöneticisi Kullanıcı adı ve parolasını (varsayılan *yönetici*) sağlayın. İkinci kimlik doğrulama isteminde SSH kullanıcı adını ve parolasını belirtin (varsayılan olarak *sshuser*' dir). Sonraki oturum açma işlemleri yalnızca SSH kimlik bilgilerini gerektirir.

## <a name="download-the-sample-data-to-local-storage"></a>Örnek verileri yerel depolamaya indirme

*Airline 2012 zamanında veri kümesi* , 2012 ABD 'deki tüm ticari fışıklara yönelik Uçuş ve adım adım ayrıntıları içeren 12 virgülle ayrılmış dosyadan oluşur. Bu veri kümesi, 6.000.000 ' den fazla gözlemle aynıdır.

1. Birkaç ortam değişkenini başlatın. RStudio sunucu konsolunda aşağıdaki kodu girin:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. Sağ bölmede **ortam** sekmesini seçin. Değişkenler **değerler**altında görüntülenir.

    ![HDInsight R Studio Web Konsolu](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Yerel bir dizin oluşturun ve örnek verileri indirin. RStudio ' da aşağıdaki kodu girin:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    İndirme işleminin yaklaşık 9,5 dakika içinde tamamlanmış olması gerekir.

## <a name="copy-the-data-to-default-storage"></a>Verileri varsayılan depolamaya Kopyala

Hadoop Dağıtılmış dosya sistemi (bir) konumu `airDataDir` değişkenle belirtilir. RStudio ' da aşağıdaki kodu girin:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

Adım yaklaşık 10 saniye içinde tamamlanmalıdır.

## <a name="set-up-a-dataset"></a>Veri kümesi ayarlama

1. Varsayılan değerleri kullanan bir dosya sistemi nesnesi oluşturun. RStudio ' da aşağıdaki kodu girin:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Özgün CSV dosyaları çok daha fazla değişken adı kullandığından, daha yönetilebilir hale getirmek için bir *COLINFO* listesi sağlarsınız. RStudio ' da aşağıdaki kodu girin:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Veri kaynakları oluşturma

Spark işlem bağlamında, aşağıdaki işlevleri kullanarak veri kaynakları oluşturabilirsiniz:

|İşlev | Açıklama |
|---------|-------------|
|`RxTextData` | Virgülle ayrılmış metin veri kaynağı. |
|`RxXdfData` | XDF veri dosyası biçimindeki veriler. Iptal durumunda XDF dosya biçimi, Hadoop 'un verileri tek bir dosya yerine bileşik bir dosya kümesinde depolaması için değiştirilmiştir. |
|`RxHiveData` | Hive veri kaynağı nesnesi oluşturur.|
|`RxParquetData` | Bir Parquet veri kaynağı nesnesi oluşturur.|
|`RxOrcData` | Bir Orc veri kaynağı nesnesi oluşturur.|

Bir [Rxtextdata](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) nesnesi oluşturun ve bu dosyaya kopyaladığınız dosyaları kullanın. RStudio ' da aşağıdaki kodu girin:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Spark için işlem bağlamı oluşturma

Çalışan düğümlerinde veri yüklemek ve çözümlemeler çalıştırmak için, komut dosyanızı işlem bağlamını [Rxspark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark)olarak ayarlarsınız. Bu bağlamda, R işlevleri işleri yönetmek için yerleşik gereksinim olmadan iş yükünü tüm çalışan düğümlerine otomatik olarak dağıtır. Spark işlem bağlamı, Spark işlem bağlamını `RxSpark` oluşturmak `rxSparkConnect()` için veya kullanılarak oluşturulur ve yerel bir işlem bağlamına dönmek `rxSparkDisconnect()` için kullanır. RStudio ' da aşağıdaki kodu girin:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Doğrusal bir modeli sığdırma

1. `airDS` Veri kaynağınızı kullanarak doğrusal bir modele uydurmak için [rxlinmod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) işlevini kullanın. RStudio ' da aşağıdaki kodu girin:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Bu adım 2 ila 3 dakika içinde tamamlanmalıdır.

1. Sonuçlara bakın. RStudio ' da aşağıdaki kodu girin:

    ```R
    summary(delayArr)
    ```

    Aşağıdaki sonuçları görmeniz gerekir:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    Sonuçlar, belirtilen dizindeki tüm CSV dosyalarını kullanarak tüm verileri, 6.000.000 gözlemlerinizi işlemişseniz olduğunu gösterir. Belirttiğiniz `cube = TRUE`için, haftanın her günü için tahmini bir katsayı (kesme noktası değil) vardır.

## <a name="use-composite-xdf-files"></a>Bileşik XDF dosyalarını kullan

Gördüğünüz gibi, doğrudan Hadoop üzerinde R ile CSV dosyalarını analiz edebilirsiniz. Ancak, verileri daha verimli bir biçimde depoluyaparsanız analizini daha hızlı yapabilirsiniz. R XDF dosya biçimi etkilidir, ancak tek bir dosyayı tek bir bir (Bu, yükleme durumundan yüklemeye değişir ancak genellikle 64 MB veya 128 MB olur.) 

Bir bileşik XDF dosyaları kümesi oluşturmak için Hadoop üzerinde [rxımport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) kullandığınızda, InData gibi bir `RxTextData` `AirDS` veri kaynağı ve `RxXdfData` dosya sistemi dosyası, bir bir dosya kaynağı olarak bir bir sunucusu, çıkışdosyası bağımsız değişkeni olarak bir. Daha sonra `RxXdfData` nesneyi sonraki R analizlerde veri bağımsız değişkeni olarak kullanabilirsiniz.

1. Bir `RxXdfData` nesne tanımlayın. RStudio ' da aşağıdaki kodu girin:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. 250000 satırlık bir blok boyutu ayarlayın ve tüm verileri okuduğunuzu belirtin. RStudio ' da aşağıdaki kodu girin:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Kullanarak `rxImport`verileri içeri aktarın. RStudio ' da aşağıdaki kodu girin:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Bu adım birkaç dakika içinde tamamlanmalıdır.

1. Yeni ve daha hızlı veri kaynağını kullanarak aynı doğrusal modeli yeniden tahmin edin. RStudio ' da aşağıdaki kodu girin:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Adım bir dakikadan kısa bir sürede tamamlanmalıdır.

1. Sonuçlara bakın. Sonuçlar CSV dosyalarındaki ile aynı olmalıdır. RStudio ' da aşağıdaki kodu girin:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>XDF 'u CSV 'ye Dönüştür

### <a name="in-a-spark-context"></a>Spark bağlamında

Analiz çalıştırırken CSV dosyalarınızı daha fazla verimlilik için XDF dosya biçimine dönüştürdüyseniz, ancak şimdi verilerinizi CSV 'ye dönüştürmek istiyorsanız [Rxdatastep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep)kullanarak bunu yapabilirsiniz.

CSV dosyalarının bir klasörünü oluşturmak için, önce dosya bağımsız değişkeni `RxTextData` olarak bir dizin adı kullanarak bir nesne oluşturun. Bu nesne, CSV dosyalarının oluşturulacağı klasörü temsil eder. Bu dizin, `rxDataStep`çalıştırdığınızda oluşturulur. Ardından, `RxTextData` `outFile` bağımsız değişkeninde `rxDataStep`bu nesnenin üzerine gelin. Oluşturulan her CSV, dizin adına göre ve ardından bir sayı ile adlandırılır.

Lojistik regresyon ve tahmin gerçekleştirdikten sonra, yeni csv dosyalarının tahmin edilen değerler ve fazlalıklar `airDataXdf` içermesi için gerileme ve tahmin gerçekleştirdikten sonra, bir CSV dosyaları klasörünü, bileşik XDF 'ınızdan RStudio ' da aşağıdaki kodu girin:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Bu adım yaklaşık 2,5 dakika içinde tamamlanmalıdır.

Giriş bileşik XDF dosyasındaki her xdfd dosyası için bir CSV dosyası yazdı.`rxDataStep` Bu,, işlem bağlamı olarak `RxSpark`ayarlandığında bileşik XDF dosyalarından CSV dosyalarını, "' ye yazma için varsayılan davranıştır.

### <a name="in-a-local-context"></a>Yerel bağlamda

Alternatif olarak, analizlerinizi gerçekleştirmeyi tamamladıktan sonra, içindeki iki bağımsız değişkenin avantajlarından yararlanmak `local` için işlem bağlamını sürümüne geri geçirebilirsiniz. Bu, içinde `RxTextData` CSV dosyalarını yazarken biraz daha fazla denetim sağlar: `createFileSet` ve `rowsPerOutFile`. ' I ' `createFileSet` a ayarladığınızda, bir CSV dosyaları klasörü belirttiğiniz dizine yazılır. `TRUE` `FALSE`' A ayarladığınızda `createFileSet` , tek bir CSV dosyası yazılır. `rowsPerOutFile`Olduğunda ,herCSVdosyasınayazılacaksatırsayısınıgöstermekiçinikincibağımsızdeğişkenini`createFileSet` bir tamsayıya ayarlayabilirsiniz. `TRUE`

RStudio ' da aşağıdaki kodu girin:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Bu adım yaklaşık 10 dakika içinde tamamlanmalıdır.

Bir `RxSpark` işlem `TRUE` bağlamı kullandığınızda `rowsPerOutFile` varsayılan olarak ' dir ve etkisizdir. `createFileSet` Bu nedenle, tek bir CSV oluşturmak veya dosya başına satır sayısını özelleştirmek istiyorsanız, bir `rxDataStep` `local` işlem bağlamında gerçekleştirin (veriler yine de devam edebilir).

## <a name="final-steps"></a>Son adımlar

1. Verileri temizleyin. RStudio ' da aşağıdaki kodu girin:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Uzak Spark uygulamasını durdurun. RStudio ' da aşağıdaki kodu girin:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. R oturumundan çıkın. RStudio ' da aşağıdaki kodu girin:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiyi tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile Verileriniz Azure Storage 'da depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmadığı halde bir HDInsight kümesi için de ücretlendirilirsiniz. Küme ücretleri depolama ücretinden çok daha fazla olduğu için, kullanımda olmadıkları sırada kümeleri silmek ekonomik bir anlam sağlar.

Bir kümeyi silmek için bkz. [tarayıcınızı, PowerShell 'i veya Azure CLI 'yı kullanarak HDInsight kümesini silme](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir HDInsight Machine Learning Hizmetleri kümesinde çalışan Apache Spark R işlevlerinin nasıl kullanılacağını öğrendiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure HDInsight Machine Learning Hizmetleri kümesi için işlem bağlamı seçenekleri](r-server-compute-contexts.md)
* [Hadoop üzerinde Spark için R Işlevleri](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
