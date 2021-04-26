---
title: Öğretici-Azure Batch ile paralel R simülasyonu çalıştırma
description: Öğretici - Azure Batch’te R doAzureParallel paketi kullanılarak bir Monte Carlo finansal simülasyonu çalıştırmaya yönelik adım adım yönergeler
ms.devlang: r
ms.topic: tutorial
ms.date: 10/08/2020
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 7e74a3b02be35b40e1a92bef269a3aed0b2313e4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491784"
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>Öğretici: Azure Batch ile paralel R simülasyonu çalıştırma

Ölçekli paralel R iş yüklerinizi, Azure Batch’i doğrudan R oturumunuzdan kullanmanıza olanak tanıyan hafif bir R paketi olan [doAzureParallel](https://www.github.com/Azure/doAzureParallel) kullanarak çalıştırın. doAzureParallel paketi, popüler [foreach](https://cran.r-project.org/web/packages/foreach/index.html) R paketi temel alınarak oluşturulmuştur. doAzureParallel, foreach döngüsünün her bir yinelemesini alır ve bir Azure Batch görevi olarak gönderir.

Bu öğreticide bir Batch havuzu dağıtma ve doğrudan RStudio içinde Azure Batch’te paralel bir R işi çalıştırma işlemi gösterilmektedir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:


> [!div class="checklist"]
> * doAzureParallel paketini yükleme ve Batch ile depolama hesaplarınıza erişecek şekilde yapılandırma
> * R oturumunuz için bir paralel arka uç olarak Batch havuzu oluşturma
> * Havuz üzerinde örnek bir paralel simülasyon çalıştırma

## <a name="prerequisites"></a>Önkoşullar

* [Microsoft R Open](https://mran.microsoft.com/open) gibi yüklü bir [R](https://www.r-project.org/) dağıtımı. R 3.3.1 veya sonraki bir sürümü kullanın.

* [RStudio](https://www.rstudio.com/) ticari sürümü veya açık kaynak [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop).

* Bir Azure Batch hesabı ve bir Azure Depolama hesabı. Bu hesapları oluşturmak için [Azure portalı](quick-create-portal.md) veya [Azure CLI](quick-create-cli.md) kullanan Batch hızlı başlangıçlarına bakın.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]
## <a name="install-doazureparallel"></a>doAzureParallel yükleme

RStudio konsolunda, [DoAzureParallel GitHub paketini](https://www.github.com/Azure/doAzureParallel)yükledikten sonra. Aşağıdaki komutlar, paketi ve bağımlılıklarını geçerli R oturumunuzda indirip yükler:

```R
# Install the devtools package
install.packages("devtools")

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch")

# Install the doAzureParallel package
devtools::install_github("Azure/doAzureParallel")

# Load the doAzureParallel library
library(doAzureParallel)
```
Yükleme işlemi birkaç dakika sürebilir.

doAzureParallel paketini daha önce edindiğiniz kimlik bilgileri ile yapılandırmak için, çalışma dizininizde *credentials.json* adlı bir yapılandırma dosyası oluşturun:

```R
generateCredentialsConfig("credentials.json")
```

Bu dosyayı, Batch ve depolama hesabınızın adları ve anahtarları ile doldurun. `githubAuthenticationToken` ayarını değiştirmeden bırakın.

İşlem tamamlandığında, kimlik bilgileri dosyası aşağıdakine benzer:

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

Dosyayı kaydedin. Sonra, geçerli R oturumunuz için kimlik bilgilerini ayarlamak üzere aşağıdaki komutu çalıştırın:

```R
setCredentials("credentials.json")
```

## <a name="create-a-batch-pool"></a>Batch havuzu oluşturma

doAzureParallel, paralel R işlerini çalıştırmak için bir Azure Batch havuzu (küme) oluşturma işlevi içerir. Düğümler, Ubuntu tabanlı bir [Azure Veri Bilimi Sanal Makinesi](../machine-learning/data-science-virtual-machine/overview.md) çalıştırır. Microsoft R Open ve popüler R paketleri bu görüntüye önceden yüklenmiş durumdadır. Düğüm sayısı ve boyutu gibi bazı küme ayarlarını görüntüleyebilir ya da özelleştirebilirsiniz.

Çalışma dizininizde bir küme yapılandırma JSON dosyası oluşturmak için:

```R
generateClusterConfig("cluster.json")
```

3 adanmış düğüm ve 3 [düşük öncelikli](batch-low-pri-vms.md) düğüm içeren varsayılan yapılandırmayı görüntülemek üzere dosyayı açın. Bu ayarlar, deneyebileceğiniz veya değişiklik yapabileceğiniz örneklerden ibarettir. Adanmış düğümler, havuzunuz için ayrılmıştır. Düşük öncelikli düğümler ise Azure’daki fazlalık VM kapasitesinden indirimli bir fiyat karşılığında sunulur. Azure’da yeterli kapasite yoksa düşük öncelikli düğümler kullanılamaz duruma gelir.

Bu öğretici için yapılandırmayı aşağıdaki gibi değiştirin:

* Her bir düğümdeki her iki çekirdekten de yararlanmak için `taskSlotsPerNode` değerini *2* olarak artırın
* Batch’te mevcut olan düşük öncelikli VM’leri deneyebilmek için `dedicatedNodes` değerini *0* olarak ayarlayın. `lowPriorityNodes` içinde `min` değerini *5* olarak ayarlayın. ve `max` değerini *10* olarak ayarlayın ya da isterseniz daha küçük sayılar seçin.

Diğer ayarlar için varsayılan değerleri bırakın ve dosyayı kaydedin. Şunun gibi görünmelidir:

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "taskSlotsPerNode": 2,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

Şimdi kümeyi oluşturun. Batch, havuzu hemen oluşturur ancak işlem düğümlerinin ayrılması ve başlatılması birkaç dakika sürer. Küme kullanılabilir duruma geldikten sonra R oturumunuz için paralel arka uç olarak kaydedin.

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json")

# Register your parallel backend
registerDoAzureParallel(cluster)

# Check that the nodes are running
getDoParWorkers()
```

Çıktıda doAzureParallel için "yürütme çalışanları" sayısı gösterilir. Bu sayı, düğüm sayısının `taskSlotsPerNode` değeri ile çarpımıdır. Küme yapılandırmasını daha önce açıklandığı gibi değiştirdiyseniz, bu sayı *10*’dur.

## <a name="run-a-parallel-simulation"></a>Paralel simülasyon çalıştırma

Kümeniz oluşturulduktan sonra, kayıtlı paralel arka ucunuz (Azure Batch havuzu) ile foreach döngünüzü çalıştırmaya hazır olursunuz. Örnek olarak, öncelikle standart foreach döngüsü kullanarak, daha sonra da foreach’i Batch ile çalıştırarak bir Monte Carlo finansal simülasyonu çalıştırın. Bu örnek, 5 yıl sonra çok sayıda farklı sonucun benzetimini yaparak stok fiyatını tahmin etmenin basitleştirilmiş bir sürümüdür.

Contoso Corporation stoğunun her gün açılış fiyatına göre ortalama 1,001 kat arttığını, ancak 0,01 dalgalanmaya (standart sapma) sahip olduğunu varsayalım. 100$ başlangıç fiyatını göz önünde bulundurarak, 5 yıl sonra Contoso'nun stok fiyatını bulmak üzere bir Monte Carlo fiyatlandırma simülasyonu kullanın.

Monte Carlo simülasyonu için parametreler:

```R
mean_change = 1.001
volatility = 0.01
opening_price = 100
```

Kapanış fiyatlarını benzetmek için aşağıdaki işlevi tanımlayın:

```R
getClosingPrice <- function() {
  days <- 1825 # ~ 5 years
  movement <- rnorm(days, mean=mean_change, sd=volatility)
  path <- cumprod(c(opening_price, movement))
  closingPrice <- path[days]
  return(closingPrice)
}
```

İlk olarak `%do%` anahtar sözcüğü ile standart bir foreach döngüsü kullanarak 10.000 simülasyonu yerel olarak çalıştırın:

```R
start_s <- Sys.time()
# Run 10,000 simulations in series
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% {
  replicate(1000, getClosingPrice())
}
end_s <- Sys.time()
```


Kapanış fiyatlarını bir histogramda çizerek sonuçların dağılımını gösterin:

```R
hist(closingPrices_s)
```

Çıktı aşağıdakine benzer:

![Ekran görüntüsü, kapanış fiyatlarının histogramını gösterir](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
Yerel bir simülasyon birkaç saniye veya daha kısa bir sürede tamamlanır:

```R
difftime(end_s, start_s)
```

Doğrusal bir yaklaştırma ile yerel olarak 10 milyon sonuç için tahmin edilen çalışma zamanı 30 dakika civarındadır:

```R
1000 * difftime(end_s, start_s, unit = "min")
```


Şimdi, Azure’da 10 milyon simülasyonu çalıştırmanın ne kadar sürdüğünü karşılaştırmak üzere `%dopar%` anahtar sözcüğü ile `foreach` kullanarak kodu çalıştırın. Simülasyonu Batch ile paralel hale getirmek için 100.000 simülasyonun 100 yinelemesini çalıştırın:

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10)
start_p <- Sys.time()
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% {
  replicate(100000, getClosingPrice())
}
end_p <- Sys.time()
```

Simülasyon, görevleri Batch havuzundaki düğümlere dağıtır. Etkinliği, Azure portal havuzun ısı haritasında görebilirsiniz. **Batch hesapları**  >  *mybatchaccount*' a gidin. **Havuzlar**  >  *mypoolname* öğesine tıklayın.

![Paralel R görevleri çalıştıran havuzun ısı haritası](media/tutorial-r-doazureparallel/pool.png)

Birkaç dakika sonra simülasyon tamamlanır. Paket, sonuçları otomatik olarak birleştirir ve düğümlerden aşağı çeker. Bundan sonra, sonuçları R oturumunuzda kullanmaya hazır olursunuz.

```R
hist(closingPrices_p)
```

Çıktı aşağıdakine benzer:

![Kapanış fiyatlarının dağılımı](media/tutorial-r-doazureparallel/closing-prices.png)

Paralel simülasyon işlemi ne kadar sürdü?

```R
difftime(end_p, start_p, unit = "min")
```

Simülasyonu Batch havuzunda çalıştırmanın, simülasyonu yerel olarak çalıştırmaya kıyasla beklenen süre içinde önemli bir performans artışı sağladığını görmüş olmanız gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

İş tamamlandıktan sonra otomatik olarak silinir. Küme artık gerekli değilse, doAzureParallel paketinde `stopCluster` işlevini çağırarak kümeyi silin:

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> doAzureParallel paketini yükleme ve Batch ile depolama hesaplarınıza erişecek şekilde yapılandırma
> * R oturumunuz için bir paralel arka uç olarak Batch havuzu oluşturma
> * Havuz üzerinde örnek bir paralel simülasyon çalıştırma


doAzureParallel hakkında daha fazla bilgi için GitHub üzerindeki belgelere ve örneklere bakın.

> [!div class="nextstepaction"]
> [doAzureParallel paketi](https://github.com/Azure/doAzureParallel/)
