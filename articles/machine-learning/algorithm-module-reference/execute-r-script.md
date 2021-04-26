---
title: 'R betiğini Yürüt: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Özel R kodunu çalıştırmak için Azure Machine Learning tasarımcısında R betiği Yürüt modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/17/2020
ms.openlocfilehash: bdd7fd8e19bf2de6d0b3c6b2edd4515771fae237
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98119040"
---
# <a name="execute-r-script-module"></a>R betik modülünü Yürüt

Bu makalede, Azure Machine Learning tasarımcı işlem hattınızda R kodunu çalıştırmak için R betiği çalıştırma modülünün nasıl kullanılacağı açıklanır.

R ile, mevcut modüller tarafından desteklenmeyen görevleri yapabilirsiniz, örneğin: 
- Özel veri dönüştürmeleri oluşturma
- Tahminleri değerlendirmek için kendi ölçümlerinizi kullanın
- Tasarımcıda tek başına modüller olarak uygulanmayan algoritmaları kullanarak modeller oluşturun

## <a name="r-version-support"></a>R sürüm desteği

Azure Machine Learning tasarımcı, R 'nin CRAN (kapsamlı R arşiv ağı) dağıtımını kullanır. Şu anda kullanılan sürüm, CRAN 3.5.1 sürümüdür.

## <a name="supported-r-packages"></a>Desteklenen R paketleri

R ortamı 100 'den fazla pakete önceden yüklenir. Tüm liste için, [önceden yüklenmiş R paketleri](#preinstalled-r-packages)bölümüne bakın.

Ayrıca yüklü paketleri görmek için, aşağıdaki kodu herhangi bir Execute R betik modülüne ekleyebilirsiniz.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> İşlem hatlarınız, önceden yüklenmiş listesinde olmayan paketlere ihtiyacı olan birden çok Execute R betik modülü içeriyorsa, paketleri her modüle yükler. 

## <a name="installing-r-packages"></a>R paketlerini yükleme
Ek R paketleri yüklemek için `install.packages()` yöntemini kullanın. Paketler, her bir R betiği yürütme modülü için yüklenir. Diğer yürütülen R betiği modülleri arasında paylaşılmaz.

> [!NOTE]
> Betik paketinden R paketi **yüklenmesi önerilmez.** Paketlerin doğrudan betik Düzenleyicisi 'ne yüklenmesi önerilir.
> Paketleri yüklerken CRAN deposunu belirtin, örneğin `install.packages("zoo",repos = "http://cran.us.r-project.org")` .

> [!WARNING]
> Excute R betik modülü, `qdap` C++ GEREKTIREN Java ve paket gerektiren paket gibi yerel derleme gerektiren paketlerin yüklenmesini desteklemez `drc` . Bunun nedeni, Bu modülün yönetici olmayan izne sahip önceden yüklenmiş bir ortamda yürütülmesi olur.
> Tasarımcı modülleri Ubuntu üzerinde çalıştığından, Windows üzerinde/için önceden derlenmiş paketleri yüklemeyin. Bir paketin Windows üzerinde önceden oluşturulup oluşturulmayacağını denetlemek için, [Cran](https://cran.r-project.org/) ' a gidebilir ve paketinizi arayabılır, işletim sisteminize göre bir ikili dosya Indirebilir ve **Açıklama** dosyasındaki **yerleşik:** bölümünü kontrol edebilirsiniz. Aşağıda bir örnek verilmiştir: :::image type="content" source="media/module/r-package-description.png" alt-text="R paketi açıklaması" lightbox="media/module/r-package-page.png":::

Bu örnek, Zoo 'nin nasıl yükleneceğini göstermektedir:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
 > Bir paketi yüklemeden önce, bir yükleme tekrarlamanız için zaten mevcut olup olmadığını denetleyin. Yineleme yüklemeleri, Web hizmeti isteklerinin zaman aşımına gelmesine neden olabilir.     

## <a name="access-to-registered-dataset"></a>Kayıtlı veri kümesine erişim

Çalışma alanınızdaki [kayıtlı veri kümelerine](../how-to-create-register-datasets.md) erişmek için aşağıdaki örnek koda başvurabilirsiniz:

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  run = get_current_run()
  ws = run$experiment$workspace
  dataset = azureml$core$dataset$Dataset$get_by_name(ws, "YOUR DATASET NAME")
  dataframe2 <- dataset$to_pandas_dataframe()
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="uploading-files"></a>Karşıya dosya yükleme
Execute R betiği modülü, Azure Machine Learning R SDK kullanarak dosyaları karşıya yüklemeyi destekler.

Aşağıdaki örnek, R betiğindeki bir görüntü dosyasının nasıl karşıya yükleneceğini göstermektedir:
```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

İşlem hattı çalıştırması tamamlandıktan sonra, modülün sağ panelinde görüntünün önizlemesini yapabilirsiniz.

> [!div class="mx-imgBorder"]
> ![Karşıya yüklenen görüntünün önizlemesi](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Execute R betiğini yapılandırma

Execute R betiği modülü, başlangıç noktası olarak örnek kod içerir.

![R modülü için giriş diyagramı](media/module/execute-r-script.png)

Tasarımcıda depolanan veri kümeleri, bu modülle yüklendiğinde otomatik olarak R veri çerçevesine dönüştürülür.

1.  İşlem hattınızla **R betiği yürütme** modülünü ekleyin.  

1. Betikte gereken tüm girdileri bağlayın. Girişler isteğe bağlıdır ve veri ve ek R kodu içerebilir.

    * **DataSet1**: ilk girişe olarak başvuru yapın `dataframe1` . Giriş veri kümesi, CSV, TSV veya ARFF dosyası olarak biçimlendirilmelidir. Ya da bir Azure Machine Learning veri kümesini bağlayabilirsiniz.

    * **DataSet2**: ikinci girişe olarak başvuru yapın `dataframe2` . Bu veri kümesi aynı zamanda CSV, TSV veya ARFF dosyası ya da bir Azure Machine Learning veri kümesi olarak biçimlendirilmelidir.

    * **Betik paketi**: Üçüncü giriş. zip dosyalarını kabul eder. Daraltılmış bir dosya, birden çok dosya ve birden çok dosya türü içerebilir.

1. **R betiği** metin kutusuna geçerli R betiği yazın veya yapıştırın.

    > [!NOTE]
    > Betiğinizi yazarken dikkatli olun. Bildirilmemiş değişkenleri veya içeri aktarılmayan modülleri veya işlevleri kullanma gibi sözdizimi hatası olmadığından emin olun. Bu makalenin sonundaki önceden yüklenmiş paket listesine daha fazla dikkat ödeyin. Listelenmeyen paketleri kullanmak için, onları betiğe yüklersiniz. `install.packages("zoo&quot;,repos = &quot;http://cran.us.r-project.org")` bunun bir örneğidir.
    
    Başlamanıza yardımcı olmak için **R betiği** metin kutusu, düzenlenebilir veya değiştirilebilir örnek kodla önceden doldurulur.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main,
    # which is the entry point for this module.

    # Note that functions dependent on the X11 library,
    # such as "View," are not supported because the X11 library
    # is not preinstalled.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a .zip file is connected to the third input port, it's
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

    Giriş noktası işlevi, `Param<dataframe1>` `Param<dataframe2>` Bu bağımsız değişkenler işlevde kullanılmasa bile giriş bağımsız değişkenlerine sahip olmalıdır.

    > [!NOTE]
    > Execute R betiği modülüne geçirilen verilere `dataframe1` ve `dataframe2` Azure Machine Learning tasarımcı 'dan (tasarımcı başvurusu, olarak) farklı olarak başvurulur `dataset1` `dataset2` . Betiğinizdeki giriş verilerine doğru şekilde başvurulduğundan emin olun.  
 
    > [!NOTE]
    > Varolan R kodunun bir tasarımcı işlem hattında çalıştırmak için küçük değişikliklere ihtiyacı vardır. Örneğin, CSV biçiminde sağladığınız giriş verileri, kodunuzda kullanabilmeniz için açıkça bir veri kümesine dönüştürülmelidir. R dilinde kullanılan veri ve sütun türleri, tasarımcıda kullanılan veri ve sütun türlerinden bazı yollarla da farklılık gösterir.

1. Betiğinizin 16 KB 'den büyük olması durumunda, CommandLine gibi hataların *16597 karakter sınırını aşması* Için **betik paketi** bağlantı noktasını kullanın. 
    
    1. Betiği ve diğer özel kaynakları bir ZIP dosyasına paketleyin.
    1. Zip dosyasını bir **dosya veri kümesi** olarak Studio 'ya yükleyin. 
    1. Veri kümesi modülünü tasarımcı yazma sayfasındaki sol modül bölmesinde bulunan *veri kümeleri* listesinden sürükleyin. 
    1. Veri kümesi modülünü, **R betiği yürütme** modülünün **betik paketi** bağlantı noktasına bağlayın.
    
    Komut dosyası paketindeki betiği tüketmek için örnek kod aşağıda verilmiştir:

    ```R
    azureml_main <- function(dataframe1, dataframe2){
    # Source the custom R script: my_script.R
    source("./Script Bundle/my_script.R")

    # Use the function that defined in my_script.R
    dataframe1 <- my_func(dataframe1)

    sample <- readLines("./Script Bundle/my_sample.txt")
    return (list(dataset1=dataframe1, dataset2=data.frame("Sample"=sample)))
    }
    ```

1.  **Rastgele çekirdek** Için, R ortamının içinde rastgele çekirdek değeri olarak kullanılacak bir değer girin. Bu parametre, `set.seed(value)` R Code 'da çağırma ile eşdeğerdir.  

1. İşlem hattını gönderme.  

## <a name="results"></a>Sonuçlar

Yürütme R betiği modülleri birden çok çıkış döndürebilir, ancak R veri çerçevesi olarak sağlanmalıdır. Tasarımcı, veri çerçevelerini diğer modüllerle uyumluluk için otomatik olarak veri kümelerine dönüştürür.

R 'deki standart iletiler ve hatalar modülün günlüğüne döndürülür.

Sonuçları R betiğine yazdırmanız gerekirse, yazdırılan sonuçları modülün sağ panelindeki **çıktılar + Günlükler** sekmesinde bulunan **70_driver_log** bulabilirsiniz.

## <a name="sample-scripts"></a>Örnek betikler

Özel R betiklerini kullanarak işlem hattınızı genişletmenin birçok yolu vardır. Bu bölüm ortak görevler için örnek kod sağlar.


### <a name="add-an-r-script-as-an-input"></a>Giriş olarak R betiği ekleme

Execute R betiği modülü, giriş olarak rastgele R betik dosyalarını destekler. Bunları kullanmak için,. zip dosyasının bir parçası olarak çalışma alanınıza yüklemeniz gerekir.

1. R kodu içeren bir. zip dosyasını çalışma alanınıza yüklemek için **veri kümeleri** varlık sayfasına gidin. **Veri kümesi oluştur**' u seçin ve ardından **yerel dosya** ve **Dosya** veri kümesi türü seçeneğini belirleyin.  

1. Daraltılmış dosyanın, sol modül ağacındaki **veri kümeleri** kategorisi altındaki **veri kümelerinde** göründüğünü doğrulayın.

1.  Veri kümesini **betik paketi** giriş bağlantı noktasına bağlayın.

1. . Zip dosyasındaki tüm dosyalar işlem hattı çalışma zamanında kullanılabilir. 

    Betik paketi dosyası bir dizin yapısı içeriyorsa, yapı korunur. Ancak, Dizin **./betik** paketini yola eklemek için kodunuzu değiştirmeniz gerekir.

### <a name="process-data"></a>Veri işleme

Aşağıdaki örnek, giriş verilerinin nasıl ölçeklendirip Normalleştirilecek gösterilmektedir:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a .zip file is connected to the third input port, it's
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # Find the maximum and minimum values of the width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # Calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # Apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Bir. zip dosyasını girdi olarak oku

Bu örnek, bir. zip dosyasında bir veri kümesinin R betiği Yürüt modülüne giriş olarak nasıl kullanılacağını gösterir.

1. Veri dosyasını CSV biçiminde oluşturun ve **mydatafile.csv** olarak adlandırın.
1. Bir. zip dosyası oluşturun ve CSV dosyasını arşive ekleyin.
1. Daraltılmış dosyayı Azure Machine Learning çalışma alanınıza yükleyin. 
1. Elde edilen veri kümesini **yürütme R betik** modülünüzün **scriptpaket** girişine bağlayın.
1. Sıkıştırılmış dosyadan CSV verilerini okumak için aşağıdaki kodu kullanın.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Satırları Çoğalt

Bu örnek, örneği dengelemek için bir veri kümesindeki olumlu kayıtların nasıl çoğaltılacağını gösterir:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>R nesnelerini Execute R betik modülleri arasında geçirme

R nesnelerini, iç serileştirme mekanizmasını kullanarak R betiği yürütme modülünün örnekleri arasında geçirebilirsiniz. Bu örnek, `A` Iki yürütme r betiği modülü arasında adlı r nesnesini taşımak istediğinizi varsayar.

1. İlk **çalıştırma R betik** modülünü ardışık düzene ekleyin. Ardından, modülün çıkış verileri tablosunda bir sütun olarak seri hale getirilmiş bir nesne oluşturmak için **R betiği** metin kutusuna aşağıdaki kodu girin `A` :  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Tamsayı türüne açık dönüştürme işlemi, serileştirme işlevi, tasarımcı tarafından desteklenmeyen R biçiminde veri çıktısı oluşturduğundan yapılır `Raw` .

1. **Execute R betik** modülünün ikinci bir örneğini ekleyin ve önceki modülün çıkış bağlantı noktasına bağlayın.

1. Giriş verileri tablosundan nesne ayıklamak için **R betiği** metin kutusuna aşağıdaki kodu yazın `A` . 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="preinstalled-r-packages"></a>Önceden yüklenmiş R paketleri

Aşağıdaki önceden yüklenmiş R paketleri şu anda kullanılabilir:

| Paket      | Sürüm    | 
|--------------|------------| 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| başlatma         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| düzeltme işareti        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| sınıf        | 7.3-15     | 
| cli          | 1.1.0      | 
| Clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| veri kümeleri     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| değerlendir     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| FS           | 1.3.1      | 
| gdata        | 2.18.0     | 
| tür     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| grafikler     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1,5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matris       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| PS           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| araçlar        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.