---
title: Web Işleri ile arka plan görevleri çalıştırma-Azure App Service
description: Web uygulamalarında Azure App Service Web Apps, API Apps veya Mobile Apps 'te arka plan görevleri çalıştırmak için nasıl kullanacağınızı öğrenin.
author: ggailey777
manager: gwallace
s.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;david.ebbo;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 293227352f27a724228136532c88e35fe877feb2
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312174"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Azure App Service Web Işleri ile arka plan görevleri çalıştırma

Bu makalede, bir yürütülebilir dosyayı veya betiği karşıya yüklemek için [Azure Portal](https://portal.azure.com) kullanarak WebJobs dağıtımı gösterilmektedir. Visual Studio kullanarak Web Işleri geliştirme ve dağıtma hakkında bilgi için bkz. [Visual Studio kullanarak Web Işleri dağıtma](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Genel Bakış
WebJobs, bir programı veya betiği bir Web uygulaması, API uygulaması veya mobil uygulama ile aynı bağlamda çalıştırmanızı sağlayan bir [Azure App Service](index.yml) özelliğidir. WebJobs kullanmanın başka bir maliyeti yoktur.

> [!IMPORTANT]
> Web Işleri, Linux üzerinde App Service için henüz desteklenmiyor.

Azure WebJobs SDK, birçok programlama görevini basitleştirmek için WebJobs ile birlikte kullanılabilir. Daha fazla bilgi için bkz. [WebJobs SDK nedir?](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Işlevleri, programları ve betikleri çalıştırmak için başka bir yol sağlar. WebJobs ve Işlevleri arasında bir karşılaştırma için bkz. [Flow, Logic Apps, işlevler ve WebJobs arasında seçim](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)yapın.

## <a name="webjob-types"></a>WebJob türleri

Aşağıdaki tabloda, *sürekli* ve *tetiklenen* Web işleri arasındaki farklar açıklanmaktadır.


|Sürekli  |Diğini  |
|---------|---------|
| WebJob oluşturulduğunda hemen başlar. İşin sonlandırmasına devam etmek için program veya komut dosyası genellikle sonsuz bir döngü içinde çalışır. İş sona erdirmek için yeniden başlatabilirsiniz. | Yalnızca el ile veya bir zamanlamaya göre tetiklendiğinde başlatılır. |
| Web uygulamasının üzerinde çalıştığı tüm örneklerde çalışır. WebJob 'u isteğe bağlı olarak tek bir örnekle kısıtlayabilirsiniz. |Yük Dengeleme için Azure 'un seçtiği tek bir örnek üzerinde çalışır.|
| Uzaktan hata ayıklamayı destekler. | Uzaktan hata ayıklamayı desteklemez.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="acceptablefiles"></a>Betikler veya programlar için desteklenen dosya türleri

Aşağıdaki dosya türleri desteklenir:

* . cmd,. bat,. exe (Windows cmd kullanarak)
* . ps1 (PowerShell kullanarak)
* . sh (Bash kullanarak)
* . php (PHP kullanarak)
* . Kopyala (Python kullanarak)
* . js (node. js kullanarak)
* . jar (Java kullanarak)

## <a name="CreateContinuous"></a>Sürekli WebJob oluşturma

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure Portal](https://portal.azure.com), App Service Web UYGULAMANıZıN, API uygulamanızın veya mobil uygulamanızın **App Service** sayfasına gidin.

2. **Web işleri**' ni seçin.

   ![Web Işleri seçin](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **WebJobs** sayfasında **Ekle**' yi seçin.

    ![WebJob sayfası](./media/web-sites-create-web-jobs/wjblade.png)

3. Tabloda belirtilen **WebJob Ekle** ayarlarını kullanın.

   ![WebJob ekleme sayfası](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Ayar      | Örnek değer   | Açıklama  |
   | ------------ | ----------------- | ------------ |
   | **Adı** | myContinuousWebJob | App Service uygulaması içinde benzersiz olan bir ad. Bir harf veya sayı ile başlamalı ve "-" ve "_" dışında özel karakterler içermemelidir. |
   | **Karşıya dosya yükleme** | ConsoleApp. zip | Yürütülebilir dosyayı veya betik dosyanızı içeren bir *. zip* dosyası ve program veya betiği çalıştırmak için gereken destekleyici dosyalar. Desteklenen yürütülebilir dosya veya betik dosyası türleri, [Desteklenen dosya türleri](#acceptablefiles) bölümünde listelenir. |
   | **Tür** | Sürekli | [WebJob türleri](#webjob-types) Bu makalenin önceki kısımlarında açıklanmıştır. |
   | **Ölçeklendirme** | Çoklu örnek | Yalnızca sürekli WebJobs için kullanılabilir. Programın veya betiğin tüm örneklerde mı yoksa yalnızca bir örnek üzerinde mi çalışacağını belirler. Birden çok örnek üzerinde çalıştırma seçeneği ücretsiz veya paylaşılan [fiyatlandırma katmanlarına](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)uygulanmaz. | 

4. **Tamam**’a tıklayın.

   Yeni WebJob, **WebJobs** sayfasında görünür.

   ![Web Işleri listesi](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Sürekli bir WebJob 'ı durdurmak veya yeniden başlatmak için, listeden WebJob 'a sağ tıklayın ve **Durdur** veya **Başlat**' a tıklayın.

    ![Sürekli WebJob 'u durdur](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a>El ile tetiklenen WebJob oluşturma

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure Portal](https://portal.azure.com), App Service Web UYGULAMANıZıN, API uygulamanızın veya mobil uygulamanızın **App Service** sayfasına gidin.

2. **Web işleri**' ni seçin.

   ![Web Işleri seçin](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **WebJobs** sayfasında **Ekle**' yi seçin.

    ![WebJob sayfası](./media/web-sites-create-web-jobs/wjblade.png)

3. Tabloda belirtilen **WebJob Ekle** ayarlarını kullanın.

   ![WebJob ekleme sayfası](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Ayar      | Örnek değer   | Açıklama  |
   | ------------ | ----------------- | ------------ |
   | **Adı** | myTriggeredWebJob | App Service uygulaması içinde benzersiz olan bir ad. Bir harf veya sayı ile başlamalı ve "-" ve "_" dışında özel karakterler içermemelidir.|
   | **Karşıya dosya yükleme** | ConsoleApp. zip | Yürütülebilir dosyayı veya betik dosyanızı içeren bir *. zip* dosyası ve program veya betiği çalıştırmak için gereken destekleyici dosyalar. Desteklenen yürütülebilir dosya veya betik dosyası türleri, [Desteklenen dosya türleri](#acceptablefiles) bölümünde listelenir. |
   | **Tür** | Diğini | [WebJob türleri](#webjob-types) Bu makalenin önceki kısımlarında açıklanmıştır. |
   | **Tetikleyiciler** | Manual | |

4. **Tamam**’a tıklayın.

   Yeni WebJob, **WebJobs** sayfasında görünür.

   ![Web Işleri listesi](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. WebJob 'ı çalıştırmak için, listede adına sağ tıklayın ve **Çalıştır**' a tıklayın.
   
    ![WebJob Çalıştır](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a>Zamanlanan WebJob oluşturma

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure Portal](https://portal.azure.com), App Service Web UYGULAMANıZıN, API uygulamanızın veya mobil uygulamanızın **App Service** sayfasına gidin.

2. **Web işleri**' ni seçin.

   ![Web Işleri seçin](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **WebJobs** sayfasında **Ekle**' yi seçin.

   ![WebJob sayfası](./media/web-sites-create-web-jobs/wjblade.png)

3. Tabloda belirtilen **WebJob Ekle** ayarlarını kullanın.

   ![WebJob ekleme sayfası](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Ayar      | Örnek değer   | Açıklama  |
   | ------------ | ----------------- | ------------ |
   | **Adı** | myScheduledWebJob | App Service uygulaması içinde benzersiz olan bir ad. Bir harf veya sayı ile başlamalı ve "-" ve "_" dışında özel karakterler içermemelidir. |
   | **Karşıya dosya yükleme** | ConsoleApp. zip | Yürütülebilir dosyayı veya betik dosyanızı içeren bir *. zip* dosyası ve program veya betiği çalıştırmak için gereken destekleyici dosyalar. Desteklenen yürütülebilir dosya veya betik dosyası türleri, [Desteklenen dosya türleri](#acceptablefiles) bölümünde listelenir. |
   | **Tür** | Diğini | [WebJob türleri](#webjob-types) Bu makalenin önceki kısımlarında açıklanmıştır. |
   | **Tetikleyiciler** | Zamanlanan | Zamanlamanın güvenilir bir şekilde çalışması için Always On özelliğini etkinleştirin. Her zaman açık, yalnızca temel, standart ve Premium fiyatlandırma katmanlarında kullanılabilir.|
   | **CRON Ifadesi** | 0 0/20 * * * * | [Cron ifadeleri](#ncrontab-expressions) aşağıdaki bölümde açıklanmıştır. |

4. **Tamam**’a tıklayın.

   Yeni WebJob, **WebJobs** sayfasında görünür.

   ![Web Işleri listesi](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>NCRONTAB ifadeleri

Portala bir [Ncrontab ifadesi](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) girebilir veya aşağıdaki örnekte olduğu gibi WebJob *. zip* dosyanızın köküne bir `settings.job` dosyası ekleyebilirsiniz:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Daha fazla bilgi için bkz. [tetiklenen bir WebJob zamanlama](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="ViewJobHistory"></a>İş geçmişini görüntüleme

1. Geçmişini görmek istediğiniz WebJob 'u seçin ve ardından **Günlükler** düğmesini seçin.
   
   ![Günlükler düğmesi](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. **WebJob ayrıntıları** sayfasında, bir çalıştırmanın ayrıntılarını görmek için bir zaman seçin.
   
   ![WebJob ayrıntıları](./media/web-sites-create-web-jobs/webjobdetails.png)

3. **WebJob çalıştırma ayrıntıları** sayfasında, günlük içeriğinin metnini görmek Için **çıktıyı geç** ' i seçin.
   
    ![Web işi çalıştırma ayrıntıları](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Çıkış metnini ayrı bir tarayıcı penceresinde görmek için **İndir**' i seçin. Metnin kendisini indirmek için **İndir** ' e sağ tıklayın ve dosya içeriğini kaydetmek için tarayıcı seçeneklerinizi kullanın.
   
5. Web Işleri listesine gitmek için sayfanın üst kısmındaki **WebJobs** içerik haritası bağlantısını seçin.

    ![WebJob içerik haritası](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Geçmiş panosundaki Web Işlerinin listesi](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Sonraki adımlar

Azure WebJobs SDK, birçok programlama görevini basitleştirmek için WebJobs ile birlikte kullanılabilir. Daha fazla bilgi için bkz. [WebJobs SDK nedir?](https://github.com/Azure/azure-webjobs-sdk/wiki).
