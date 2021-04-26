---
title: Web Işleri ile arka plan görevleri çalıştırma
description: Azure App Service arka plan görevlerini çalıştırmak için WebJobs kullanmayı öğrenin. Çeşitli betik biçimlerinden birini seçin ve bunları CRON ifadeleriyle çalıştırın.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./webjobs-create-ieux
ms.openlocfilehash: 8afc8ca9b0dedb10ecdb30e8abb22a5d0986de5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723886"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Azure App Service Web Işleri ile arka plan görevleri çalıştırma

Bu makalede, bir yürütülebilir dosyayı veya betiği karşıya yüklemek için [Azure Portal](https://portal.azure.com) kullanarak WebJobs dağıtımı gösterilmektedir. Visual Studio kullanarak Web Işleri geliştirme ve dağıtma hakkında bilgi için bkz. [Visual Studio kullanarak Web Işleri dağıtma](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Genel Bakış
WebJobs, bir program veya betiği Web uygulaması, API uygulaması veya mobil uygulama ile aynı örnekte çalıştırmanızı sağlayan bir [Azure App Service](index.yml) özelliğidir. WebJobs kullanmanın başka bir maliyeti yoktur.

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

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Betikler veya programlar için desteklenen dosya türleri

Aşağıdaki dosya türleri desteklenir:

* . cmd,. bat,. exe (Windows cmd kullanarak)
* . ps1 (PowerShell kullanarak)
* . sh (Bash kullanarak)
* . php (PHP kullanarak)
* . Kopyala (Python kullanarak)
* . js (Node.js kullanarak)
* . jar (Java kullanarak)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Sürekli WebJob oluşturma

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

> [!IMPORTANT]
> Uygulamanızla yapılandırılmış kaynak denetiminiz varsa, WebJobs 'lar kaynak denetimi tümleştirmesinin parçası olarak dağıtılmalıdır. Kaynak denetimi uygulamanızla yapılandırıldıktan sonra, Azure portalından WebJob eklenemez.

1. [Azure Portal](https://portal.azure.com), App Service Web UYGULAMANıZıN, API uygulamanızın veya mobil uygulamanızın **App Service** sayfasına gidin.

2. **Web işleri**' ni seçin.

   ![Web Işleri seçin](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **WebJobs** sayfasında **Ekle**' yi seçin.

    ![WebJob sayfası](./media/web-sites-create-web-jobs/wjblade.png)

3. Tabloda belirtilen **WebJob Ekle** ayarlarını kullanın.

   ![Yapılandırmanız gereken WebJob ekleme ayarlarını gösteren ekran görüntüsü.](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Ayar      | Örnek değer   | Açıklama  |
   | ------------ | ----------------- | ------------ |
   | **Ad** | myContinuousWebJob | App Service uygulaması içinde benzersiz olan bir ad. Bir harf veya sayı ile başlamalı ve "-" ve "_" dışında özel karakterler içermemelidir. |
   | **Karşıya dosya yükleme** | ConsoleApp.zip | Yürütülebilir dosyayı veya betik dosyanızı içeren bir *. zip* dosyası ve program veya betiği çalıştırmak için gereken destekleyici dosyalar. Desteklenen yürütülebilir dosya veya betik dosyası türleri, [Desteklenen dosya türleri](#acceptablefiles) bölümünde listelenir. |
   | **Tür** | Sürekli | [WebJob türleri](#webjob-types) Bu makalenin önceki kısımlarında açıklanmıştır. |
   | **Ölçeklendirme** | Çoklu örnek | Yalnızca sürekli WebJobs için kullanılabilir. Programın veya betiğin tüm örneklerde mı yoksa yalnızca bir örnek üzerinde mi çalışacağını belirler. Birden çok örnek üzerinde çalıştırma seçeneği ücretsiz veya paylaşılan [fiyatlandırma katmanlarına](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)uygulanmaz. | 

4. **Tamam**'a tıklayın.

   Yeni WebJob, **WebJobs** sayfasında görünür.

   ![Web Işleri listesi](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Sürekli bir WebJob 'ı durdurmak veya yeniden başlatmak için, listeden WebJob 'a sağ tıklayın ve **Durdur** veya **Başlat**' a tıklayın.

    ![Sürekli WebJob 'u durdur](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> El ile tetiklenen WebJob oluşturma

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

   ![El ile tetiklenen bir WebJob oluşturmak için ayarlanması gereken ayarları gösteren ekran görüntüsü.](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Ayar      | Örnek değer   | Açıklama  |
   | ------------ | ----------------- | ------------ |
   | **Ad** | myTriggeredWebJob | App Service uygulaması içinde benzersiz olan bir ad. Bir harf veya sayı ile başlamalı ve "-" ve "_" dışında özel karakterler içermemelidir.|
   | **Karşıya dosya yükleme** | ConsoleApp.zip | Yürütülebilir dosyayı veya betik dosyanızı içeren bir *. zip* dosyası ve program veya betiği çalıştırmak için gereken destekleyici dosyalar. Desteklenen yürütülebilir dosya veya betik dosyası türleri, [Desteklenen dosya türleri](#acceptablefiles) bölümünde listelenir. |
   | **Tür** | Diğini | [WebJob türleri](#webjob-types) Bu makalenin önceki kısımlarında açıklanmıştır. |
   | **Tetikleyiciler** | El ile | |

4. **Tamam**'a tıklayın.

   Yeni WebJob, **WebJobs** sayfasında görünür.

   ![Web Işleri listesi](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. WebJob 'ı çalıştırmak için, listede adına sağ tıklayın ve **Çalıştır**' a tıklayın.
   
    ![WebJob Çalıştır](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Zamanlanan WebJob oluşturma

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

   | Ayar      | Örnek değer   | Açıklama  |
   | ------------ | ----------------- | ------------ |
   | **Ad** | myScheduledWebJob | App Service uygulaması içinde benzersiz olan bir ad. Bir harf veya sayı ile başlamalı ve "-" ve "_" dışında özel karakterler içermemelidir. |
   | **Karşıya dosya yükleme** | ConsoleApp.zip | Yürütülebilir dosyayı veya betik dosyanızı içeren bir *. zip* dosyası ve program veya betiği çalıştırmak için gereken destekleyici dosyalar. Desteklenen yürütülebilir dosya veya betik dosyası türleri, [Desteklenen dosya türleri](#acceptablefiles) bölümünde listelenir. |
   | **Tür** | Diğini | [WebJob türleri](#webjob-types) Bu makalenin önceki kısımlarında açıklanmıştır. |
   | **Tetikleyiciler** | Zamanlanan | Zamanlamanın güvenilir bir şekilde çalışması için Always On özelliğini etkinleştirin. Her zaman açık, yalnızca temel, standart ve Premium fiyatlandırma katmanlarında kullanılabilir.|
   | **CRON İfadesi** | 0 0/20 * * * * | [Cron ifadeleri](#ncrontab-expressions) aşağıdaki bölümde açıklanmıştır. |

4. **Tamam**'a tıklayın.

   Yeni WebJob, **WebJobs** sayfasında görünür.

   ![Web Işleri listesi](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>NCRONTAB ifadeleri

Portala bir [Ncrontab ifadesi](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) girebilir veya `settings.job` WebJob *. zip* dosyanızın köküne bir dosya ekleyebilirsiniz, örneğin, aşağıdaki örnekte olduğu gibi:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Daha fazla bilgi için bkz. [tetiklenen bir WebJob zamanlama](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a> İş geçmişini görüntüleme

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
   
## <a name="next-steps"></a><a name="NextSteps"></a> Sonraki adımlar

Azure WebJobs SDK, birçok programlama görevini basitleştirmek için WebJobs ile birlikte kullanılabilir. Daha fazla bilgi için bkz. [WebJobs SDK nedir?](https://github.com/Azure/azure-webjobs-sdk/wiki).
