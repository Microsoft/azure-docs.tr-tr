---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746674"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Sürekli WebJob oluşturma

1. [Azure Portal](https://portal.azure.com)alındı.
1. **App Service** gidin <abbr title="Uygulama kaynağınız bir Web uygulaması, API uygulaması veya mobil uygulama olabilir.">Uygulama kaynağı</abbr>.
1. **Web işleri**' ni seçin.

   ![Web Işleri seçin](../media/web-sites-create-web-jobs/select-webjobs.png)

1. **WebJobs** sayfasında **Ekle**' yi seçin.

    ![WebJob sayfası](../media/web-sites-create-web-jobs/wjblade.png)

1. Tabloda belirtilen **WebJob Ekle** ayarlarını kullanın.

   ![Yapılandırmanız gereken WebJob ekleme ayarlarını gösteren ekran görüntüsü.](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Ayar      | Örnek değer   | 
   | ------------ | ----------------- | 
   | <abbr title="App Service uygulaması içinde benzersiz olan bir ad. Bir harf veya sayı ile başlamalı ve ve dışında özel karakterler içermemelidir `-` `_` .">Name</abbr> | myContinuousWebJob | 
   | <abbr title=" Yürütülebilir dosyayı veya betik dosyanızı içeren bir *. zip* dosyası ve program veya betiği çalıştırmak için gereken destekleyici dosyalar.">Karşıya dosya yükleme</abbr> | ConsoleApp.zip |
   | <abbr title="Türler arasında sürekli, tetiklenen.">Tür</abbr> | Sürekli | 
   | <abbr title="Yalnızca sürekli WebJobs için kullanılabilir. Programın veya betiğin tüm örneklerde mı yoksa yalnızca bir örnek üzerinde mi çalışacağını belirler. Birden çok örnek üzerinde çalıştırma seçeneği ücretsiz veya paylaşılan fiyatlandırma katmanlarına uygulanmaz.">Ölçek</abbr> | Çoklu örnek | 

1. **Tamam**'a tıklayın.

    Yeni WebJob, **WebJobs** sayfasında görünür.

    ![Web Işleri listesi](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. Sürekli bir WebJob 'ı **durdurmak veya yeniden başlatmak için** , listeden WebJob 'a sağ tıklayın ve **Durdur** veya **Başlat**' a tıklayın.

   ![Sürekli WebJob 'u durdur](../media/web-sites-create-web-jobs/continuousstop.png)
