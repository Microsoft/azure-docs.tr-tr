---
title: 'R betiğini Yürüt: modül başvurusu'
titleSuffix: Azure Machine Learning
description: R kodunu çalıştırmak için Azure Machine Learning R betiği yürütme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ms.openlocfilehash: f9aae1302f0d83c27d5d8f01745ddecbaeea9467
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497876"
---
# <a name="execute-r-script"></a>R Betiği yürütme

Bu makalede, Azure Machine Learning tasarımcı (Önizleme) ardışık düzeninde R kodu çalıştırmak için **r betiği yürütme** modülünün nasıl kullanılacağı açıklanır.

R ile Şu anda mevcut modüller tarafından desteklenmeyen görevleri gerçekleştirebilirsiniz: 
- Özel veri dönüştürmeleri oluşturma
- Tahminleri değerlendirmek için kendi ölçümlerinizi kullanın
- Tasarımcıda tek başına modüller olarak uygulanmayan algoritmaları kullanarak modeller oluşturun

## <a name="r-version-support"></a>R sürüm desteği

Azure Machine Learning tasarımcı, R 'nin CRAN (kapsamlı R arşiv ağı) dağıtımını kullanır. Şu anda kullanılan sürüm, CRAN 3.5.1 sürümüdür.

## <a name="supported-r-packages"></a>Desteklenen R paketleri

R ortamı, 100 ' den fazla pakete önceden yüklenir. Tam liste için, [önceden yüklenmiş R paketleri](#pre-installed-r-packages)bölümüne bakın.

Ayrıca, herhangi bir **R betik modülünü yürütmek** ve yüklü paketleri görmek için aşağıdaki kodu da ekleyebilirsiniz.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>R paketlerini yükleme
Ek R paketleri yüklemek için `install.packages()` yöntemi kullanın. CRAN deposunu belirttiğinizden emin olun. Paketler her bir **r betik** modülü için yüklenir ve diğer **yürütme r betik** modülleri arasında paylaşılmaz.

Bu örnek, Zoo 'nin nasıl yükleneceğini göstermektedir:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
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
  > Yüklemeyi tekrardan kaçınmak için, yüklemeden önce paketin zaten mevcut olup olmadığını denetleyin. Yukarıdaki örnek kodda `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` gibi. Yüklemeyi tekrarlamak, Web hizmeti istek zaman aşımına neden olabilir.     

## <a name="how-to-configure-execute-r-script"></a>Execute R betiğini yapılandırma

**R betiği Yürüt** modülü, başlangıç noktası olarak kullanabileceğiniz örnek kodu içerir. **R betiğini Yürüt** modülünü yapılandırmak için, yürütülecek bir giriş ve kod kümesi sağlayın.

![R-modülü](media/module/execute-r-script.png)

Tasarımcıda depolanan veri kümeleri, bu modülle yüklendiğinde otomatik olarak R veri çerçevesine dönüştürülür.

1.  İşlem hattınızla **R betiği yürütme** modülünü ekleyin.

  

1. Betiği için gereken tüm girdileri bağlayın. Girişler isteğe bağlıdır ve veri ve ek R kodu içerebilir.

    * **DataSet1**: ilk girişe `dataframe1`olarak başvur. Giriş veri kümesi, CSV, TSV, ARFF olarak biçimlendirilmelidir veya bir Azure Machine Learning veri kümesini bağlayabilmeniz gerekir.

    * **DataSet2**: ikinci girişe `dataframe2`olarak başvurun. Bu veri kümesi aynı zamanda CSV, TSV, ARFF dosyası veya Azure Machine Learning veri kümesi olarak biçimlendirilmelidir.

    * **Betik paketi**: Üçüncü giriş ZIP dosyalarını kabul eder. Daraltılmış dosya birden çok dosya ve birden çok dosya türü içerebilir.

1. **R betiği** metin kutusuna geçerli R betiği yazın veya yapıştırın.

    Başlamanıza yardımcı olmak için **R betiği** metin kutusu, düzenlenebilir veya değiştirilebilir örnek kodla önceden doldurulur.
    
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.

  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

 * Betik, Bu modülün giriş noktası olan `azureml_main`adlı bir işlev içermelidir.

 * Giriş noktası işlevi, en fazla iki giriş bağımsız değişkeni içerebilir: `Param<dataframe1>` ve `Param<dataframe2>`
 
   > [!NOTE]
    > **Execute R betik** modülüne geçirilen verilere `dataframe1` ve Azure Machine Learning `dataframe2`olarak başvurulur (tasarımcı başvurusu `dataset1`, `dataset2`). Giriş verilerinin betiğe doğru şekilde yazıldığından emin olmak için lütfen kontrol edin.  
 
    > [!NOTE]
    >  Varolan R kodunun bir tasarımcı işlem hattında çalıştırmak için küçük değişiklikler gerekebilir. Örneğin, CSV biçiminde sağladığınız giriş verileri, kodunuzda kullanabilmeniz için açıkça bir veri kümesine dönüştürülmelidir. R dilinde kullanılan veri ve sütun türleri, tasarımcıda kullanılan veri ve sütun türlerinden bazı yollarla da farklılık gösterir.

1.  **Rastgele çekirdek**: R ortamının içinde rastgele çekirdek değeri olarak kullanılacak bir değer yazın. Bu parametre, R kodundaki `set.seed(value)` çağırma ile eşdeğerdir.  

1. İşlem hattını çalıştırma.  

## <a name="results"></a>Sonuçlar

**Execute r betik** modülleri birden çok çıkış döndürebilir, ancak r veri çerçevesi olarak sağlanmalıdır. Veri çerçeveleri, diğer modüllerle uyumluluk için otomatik olarak tasarımcıda veri kümelerine dönüştürülür.

R 'deki standart iletiler ve hatalar modülün günlüğüne döndürülür.

## <a name="sample-scripts"></a>Örnek komut dosyaları

Özel R betiği kullanarak işlem hattınızı genişletebilmeniz için birçok yol vardır.  Bu bölüm ortak görevler için örnek kod sağlar.


### <a name="add-r-script-as-an-input"></a>R betiğini giriş olarak ekle

**Execute r betiği** modülü, giriş olarak rastgele R betik dosyalarını destekler. Bunu yapmak için, ZIP dosyasının bir parçası olarak çalışma alanınıza yüklenmesi gerekir.

1. R kodu içeren bir ZIP dosyasını çalışma alanınıza yüklemek için **Yeni**' ye tıklayın, **veri kümesi**' ne tıklayın ve ardından **yerel dosya** ve **ZIP dosyası** seçeneğini belirleyin.  

1. Sıkıştırılmış dosyanın **kayıtlı veri kümeleri** listesinde kullanılabilir olduğunu doğrulayın.

1.  Veri kümesini **betik paketi** giriş bağlantı noktasına bağlayın.

1. ZIP dosyasında yer alan tüm dosyalar işlem hattı çalışma zamanı sırasında kullanılabilir. 

    Betik paketi dosyası bir dizin yapısı içeriyorsa, yapı korunur. Bununla birlikte, Dizin **./betik** paketini yola eklemek için kodunuzu değiştirmeniz gerekir.

### <a name="process-data"></a>Veri işleme

Aşağıdaki örnek, giriş verilerinin nasıl ölçeklendirip Normalleştirilecek gösterilmektedir:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>ZIP dosyasını girdi olarak oku

Bu örnek, bir ZIP dosyasında bir veri kümesinin, **R betiği yürütme** modülüne giriş olarak nasıl kullanılacağını gösterir.

1. Veri dosyasını CSV biçiminde oluşturun ve "myveri. csv" olarak adlandırın.
1. Bir ZIP dosyası oluşturun ve CSV dosyasını arşive ekleyin.
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

R nesnelerini, iç serileştirme mekanizmasını kullanarak **r betiği yürütme** modülünün örnekleri arasında geçirebilirsiniz. Bu örnek, iki **yürütme r betik** modülü arasında `A` adlı r nesnesini taşımak istediğinizi varsayar.

1. İlk **çalıştırma r betiği** modülünü işlem hattınızı ekleyin ve modülün çıkış verileri tablosunda bir sütun olarak `A` seri hale getirilmiş bir nesne oluşturmak Için **r betiği** metin kutusuna aşağıdaki kodu yazın:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Tamsayı türüne açık dönüştürme işlemi, serileştirme işlevi, tasarımcı tarafından desteklenmeyen R `Raw` biçiminde veri çıktısı oluşturduğundan yapılır.

1. **Execute R betik** modülünün ikinci bir örneğini ekleyin ve önceki modülün çıkış bağlantı noktasına bağlayın.

1. Giriş verileri tablosundan nesne `A` çıkarmak için **R betiği** metin kutusuna aşağıdaki kodu yazın. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Önceden yüklenmiş R paketleri

Kullanılabilecek önceden yüklenmiş R paketlerinin geçerli listesi:

|              |            | 
|--------------|------------| 
| Paket      | Sürüm    | 
| askpass      | 1.1        | 
| AssertBu   | 0.2.1      | 
| seçilmiş    | 1.1.4      | 
| temel         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| iki kez       | 1.0-6      | 
| başlatma         | 1.3-22     | 
| Brob        | 0.5.2      | 
| callr        | 3.2.0      | 
| kar        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| sınıfı        | 7.3-15     | 
| CLI          | 1.1.0      | 
| Clipr        | 0.6.0      | 
| içi      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| Derleyici     | 3.5.1      | 
| Crayon       | 1.3.4      | 
| Kıvr         | 3,3        | 
| Data. Table   | 1.12.2     | 
| kümelerinin     | 3.5.1      | 
| DBı          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| bilgisi       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| değerlendir     | 0,14       | 
| fansi        | 0.4.0      | 
| forkediler      | 0.3.0      | 
| Foreach      | 1.4.4      | 
| yabancı      | 0.8-71     | 
| FS           | 1.3.1      | 
| GDATA        | 2.18.0     | 
| Tür     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| Tutkal         | 1.3.1      | 
| Gower        | 0.2.1      | 
| gplotlar       | 3.0.1.1    | 
| grafik     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| Çizgisi         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| Haven        | 2.1.0      | 
| highr        | 0,8        | 
| HMS          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| yineleyiciler    | 1.0.10     | 
| jsonlite     | 1,6        | 
| Kernpürüzsüz   | 2.23-15    | 
| knitr        | 1,23       | 
| kapatma     | 0,3        | 
| kafes      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubria tarihi    | 1.7.4      | 
| magrittr     | 1,5        | 
| MARKDOWN     | 1          | 
| ALıCıSıNA         | 7.3-51.4   | 
| Matrisin       | 1.2-17     | 
| Yöntem      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0,7        | 
| Modelölçümleri | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsatışı      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| OpenSSL      | 1,4        | 
| parallel     | 3.5.1      | 
| sütun       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| Lemesine     | 1.2.2      | 
| PS           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| miktar TMOD     | 0,4-15     | 
| R6           | 2.4.0      | 
| Rastgele orman | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| yemek tarifleri      | 0.1.5      | 
| yeniden eşleştir      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| recekte   | 1,12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1,13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| ölçeklendirir       | 1.0.0      | 
| selectr      | 0,4-1      | 
| Uzay      | 7.3-11     | 
| daki cetvelleri      | 3.5.1      | 
| SQUAREMILYON      | 2017.10-1  | 
| STA        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringı      | 1.4.3      | 
| stringr      | 1.3.1      | 
| acil ihtiyaç     | 2.44-1.1   | 
| dosyasında          | 3,2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1'i      | 
| timeDate     | 3043,102   | 
| tinytex      | 0,13       | 
| araçlar        | 3.5.1      | 
| Tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| UTF8         | 1.1.4      | 
| paketindeki        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| yatay çizgi      | 0,3-2      | 
| withr        | 2.1.2 'yi      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| XTS          | 0.11-2     | 
| YAML         | 2.2.0      | 
| zealpartisi      | 0.1.0      | 
| Zoo          | 1.8-6      | 

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
