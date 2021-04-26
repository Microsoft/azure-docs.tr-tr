---
title: 'ML Studio (klasik): Azure Machine Learning taşıma-R betiğini yürütme'
description: Studio 'Yu yeniden derleyin (klasik) Azure Machine Learning 'de çalıştırmak için R betik modüllerini yürütün.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 18d9013b1a5afcf57b54cffc01a15948382210bb
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311048"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Studio 'da R betik modüllerini yürütme (klasik)

Bu makalede, Azure Machine Learning ' de bir Studio (klasik) **yürütme R betik** modülünü yeniden oluşturmayı öğreneceksiniz.

Studio 'dan (klasik) geçiş hakkında daha fazla bilgi için bkz. [geçişe genel bakış makalesi](migrate-overview.md).

## <a name="execute-r-script"></a>R Betiği yürütme

Azure Machine Learning tasarımcı artık Linux üzerinde çalışıyor. Studio (klasik) Windows üzerinde çalışır. Platform değişikliği nedeniyle, geçiş sırasında **yürütme R betiğini** ayarlamanız gerekir, aksi takdirde işlem hattı başarısız olur.

Bir **R betik** modülünü Studio 'dan (klasik) geçirmek için, `maml.mapInputPort` ve `maml.mapOutputPort` arabirimlerini standart işlevlerle değiştirmeniz gerekir.

Aşağıdaki tablo, R betik modülündeki değişiklikleri özetler:

|Özellik|Studio (klasik)|Azure Machine Learning tasarımcısı|
|---|---|---|
|Betik arabirimi|`maml.mapInputPort` ve `maml.mapOutputPort`|İşlev arabirimi|
|Platform|Windows|Linux|
|Internet 'e erişilebilir |Hayır|Yes|
|Bellek|14 GB|Işlem SKU 'suna bağımlıdır|

### <a name="how-to-update-the-r-script-interface"></a>R betik arabirimini güncelleştirme

İşte, Studio 'da bir örnek **yürütme R betik** modülünün içeriği (klasik):
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

Tasarımcıda güncelleştirilmiş içerik aşağıda verilmiştir. `maml.mapInputPort`Ve ' nin `maml.mapOutputPort` standart işlev arabirimiyle değiştirildiğini unutmayın `azureml_main` . 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
Daha fazla bilgi için bkz. Designer [Execute R Script Module başvurusu](../algorithm-module-reference/execute-r-script.md).

### <a name="install-r-packages-from-the-internet"></a>Internet 'ten R paketleri 'ni yükler

Azure Machine Learning tasarımcı, paketleri doğrudan CRAN 'ten yüklemenize olanak sağlar.

Bu, Studio üzerinden bir geliştirmedir (klasik). Studio (klasik), internet erişimi olmayan bir korumalı alan ortamında çalıştığından, daha fazla paket yüklemek için bir ZIP paketine betikleri karşıya yüklemeniz gerekiyordu. 

Tasarımcı 'nın **Execute R betik** modülünde Cran paketlerini yüklemek için aşağıdaki kodu kullanın:
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, yürütme R betik modüllerini Azure Machine Learning 'ye geçirmeyi öğrendiniz.

Studio (klasik) geçiş serisinde diğer makalelere bakın:

1. [Geçişe genel bakış](migrate-overview.md).
1. [Veri kümesini geçirin](migrate-register-dataset.md).
1. [Bir Studio (klasik) eğitim işlem hattını yeniden derleyin](migrate-rebuild-experiment.md).
1. [Bir Studio (klasik) Web hizmetini yeniden derleyin](migrate-rebuild-web-service.md).
1. [İstemci uygulamalarıyla bir Azure Machine Learning Web hizmetini tümleştirin](migrate-rebuild-integrate-with-client-app.md).
1. **Yürütülen R betik modüllerini geçirin**.