---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5105df5793d37b166b017585a62c962933a0b019
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081032"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Zamanlanan WebJob oluşturma


1. [Azure Portal](https://portal.azure.com)alındı.
1. **App Service** gidin <abbr title="Uygulama kaynağınız bir Web uygulaması, API uygulaması veya mobil uygulama olabilir.">Uygulama kaynağı</abbr>.
1. **Web işleri**' ni seçin.

   ![Web Işleri seçin](../media/web-sites-create-web-jobs/select-webjobs.png)

1. **WebJobs** sayfasında **Ekle**' yi seçin.

    ![WebJob sayfası](../media/web-sites-create-web-jobs/wjblade.png)

1. Tabloda belirtilen **WebJob Ekle** ayarlarını kullanın.

    ![WebJob ekleme sayfası](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | Ayar      | Örnek değer   |
    | ------------ | ----------------- | 
    | <abbr title="App Service uygulaması içinde benzersiz olan bir ad. Bir harf veya sayı ile başlamalı ve ve dışında özel karakterler içermemelidir `-` `_` .">Name</a> | myScheduledWebJob | 
    | <abbr title="Yürütülebilir dosyayı veya betik dosyanızı içeren bir *. zip* dosyası ve program veya betiği çalıştırmak için gereken destekleyici dosyalar.">Karşıya dosya yükleme</abbr> | ConsoleApp.zip |
    | <abbr title="Türler arasında sürekli, tetiklenen.">Tür</abbr> | Diğini |
    | <abbr title="Zamanlamanın güvenilir bir şekilde çalışması için Always On özelliğini etkinleştirin. Her zaman açık, yalnızca temel, standart ve Premium fiyatlandırma katmanlarında kullanılabilir.">Tetikleyiciler</a> | Zamanlanan |
    | CRON İfadesi</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>CRON Ifadeleri hakkında daha fazla bilgi edinin</summary>
     <a name="#ncrontab-expressions"></a>
    
     Portala bir [Ncrontab ifadesi](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions) girebilir veya `settings.job` WebJob *. zip* dosyanızın köküne bir dosya ekleyebilirsiniz, örneğin, aşağıdaki örnekte olduğu gibi:
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     Daha fazla bilgi için bkz. [tetiklenen bir WebJob zamanlama](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. **Tamam**'a tıklayın.

    Yeni WebJob, **WebJobs** sayfasında görünür.
    
    ![Web Işleri listesi](../media/web-sites-create-web-jobs/listallwebjobs.png)
