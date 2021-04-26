---
title: Uygulama değişikliği analizi için görselleştirmeler-Azure Izleyici
description: Azure Izleyici 'de uygulama değişikliği analizinde görselleştirmeleri nasıl kullanacağınızı öğrenin.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 8319885de26bf79f5e402c4d06b29e9dd94894de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655860"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>Uygulama değişikliği analizi için görselleştirmeler (Önizleme)

## <a name="standalone-ui"></a>Tek başına kullanıcı arabirimi

Azure Izleyici 'de, uygulama bağımlılıkları ve kaynakları hakkındaki öngörülere sahip tüm değişiklikleri görüntülemek için değişiklik analizinin tek başına bir bölmesi vardır.

Deneyimi başlatmak için Azure portal arama çubuğunda bulunan değişiklik analizini arayın.

![Azure portal değişiklik analizini aramanın ekran görüntüsü](./media/change-analysis/search-change-analysis.png)

Seçili bir abonelik kapsamındaki tüm kaynaklar son 24 saat içindeki değişikliklerle birlikte görüntülenir. Tüm değişiklikler, bir bakışta Öngörüler sağlamak için eski değer ve yeni değer ile görüntülenir.

![Azure portal değişiklik Analizi dikey penceresinin ekran görüntüsü](./media/change-analysis/change-analysis-standalone-blade.png)

Tam Kaynak Yöneticisi parçacığı ve diğer özellikleri görüntülemek için değişikliğe tıklanın.

![Değişiklik ayrıntılarının ekran görüntüsü](./media/change-analysis/change-details.png)

Geri bildirimde bulunmak için geri bildirim gönder düğmesini veya e-postayı kullanın changeanalysisteam@microsoft.com .

![Değişiklik Analizi sekmesindeki geri bildirim düğmesinin ekran görüntüsü](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>Çoklu abonelik desteği

UI, kaynak değişikliklerini görüntülemek için birden çok abonelik seçmeyi destekler. Abonelik filtresini kullanın:

![Birden çok abonelik seçmeyi destekleyen abonelik filtresi ekran görüntüsü](./media/change-analysis/multiple-subscriptions-support.png)


## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Sorunları tanılama ve çözme aracında uygulama değişikliği Analizi

Uygulama değişikliği analizi, Web uygulamasındaki tek başına bir algılayıcı olan sorunları tanılamanıza ve çözmenize yardımcı olur. Ayrıca **uygulama Kilitlenmelerinde** ve **Web uygulaması, algılayıcıları**'nda da toplanır. Sorunları Tanıla ve çöz aracını girerken, **Microsoft. ChangeAnalysis** kaynak sağlayıcısı otomatik olarak kaydedilir. Web uygulamasını Konuk değişiklik izlemeyi etkinleştirmek için bu yönergeleri izleyin.

1. **Kullanılabilirlik ve performans ' ı** seçin.

    !["Kullanılabilirlik ve performans" sorun giderme seçeneklerinin ekran görüntüsü](./media/change-analysis/availability-and-performance.png)

2. **Uygulama değişikliklerini** seçin. Özellik **uygulama Kilitlenmelerinde** da kullanılabilir.

   !["Uygulama kilitlenmeler" düğmesinin ekran görüntüsü](./media/change-analysis/application-changes.png)

3. Bağlantı, Web uygulaması kapsamındaki uygulama değişikliği Analizi Kullanıcı arabirimine yol açar. Web uygulaması Konuk değişiklik izleme etkin değilse, dosya ve uygulama ayarları değişikliklerini almak için başlığı izleyin.

   !["Uygulama kilitlenmeler" seçeneklerinin ekran görüntüsü](./media/change-analysis/enable-changeanalysis.png)

4. **Değişiklik analizini** açın ve **Kaydet**' i seçin. Araç, tüm Web uygulamalarını bir App Service planı altında görüntüler. Plan düzeyi anahtarını, bir plandaki tüm Web uygulamalarının değişiklik analizini açmak için kullanabilirsiniz.

    !["Değişiklik analizini etkinleştir" Kullanıcı arabiriminin ekran görüntüsü](./media/change-analysis/change-analysis-on.png)

5. Değişiklik verileri, Select **Web App** ı ve **uygulama kilitlenmesi** algılayıcıları içinde de kullanılabilir. Zaman içindeki değişikliklerin türünü ve bu değişikliklerle ilgili ayrıntıları özetleyen bir grafik görürsünüz. Varsayılan olarak, son 24 saat içindeki değişiklikler anında sorunla ilgili yardım almak için görüntülenir.

     ![Değişiklik fark görünümünün ekran görüntüsü](./media/change-analysis/change-view.png)

## <a name="diagnose-and-solve-problems-tool"></a>Sorunları tanılama ve çözme aracı
Değişiklik analizi, sorun aracında tanılama ve çözme konusunda bir öngörü kartı olarak sunulmaktadır. Bir kaynak sorunlarla karşılaşırsa ve Son 72 saat içinde bulunan değişiklikler varsa, Öngörüler kartında değişiklik sayısı görüntülenir. Görünüm değişiklik ayrıntıları bağlantısına tıklanması, değişiklik Analizi tek başına kullanıcı arabiriminden filtrelenmiş görünüme neden olur.

![Sorunları tanılamaya ve çözmeye yönelik değişiklik öngörülerini görüntüleme ekran görüntüsü.](./media/change-analysis/change-insight-diagnose-and-solve.png)



## <a name="virtual-machine-diagnose-and-solve-problems"></a>Sanal makine tanılama ve çözme sorunları

Bir sanal makine için sorunları tanılama ve çözme aracını ziyaret edin.  **Sorun giderme araçları**' na gidin, sayfayı Inceleyin ve sanal makinedeki değişiklikleri görüntülemek için **son değişiklikleri çözümle** ' yi seçin.

![VM tanılama ve çözme sorunlarının ekran görüntüsü](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Sorun giderme araçlarında çözümleyici 'yi değiştirme](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>Etkinlik günlüğü değişiklik geçmişi

Etkinlik günlüğündeki değişiklik [geçmişini görüntüle](../essentials/activity-log.md#view-change-history) özelliği, bir işlemle ilişkili değişiklikleri almak Için uygulama değişiklik Analizi hizmeti arka ucunu çağırır. [Azure Kaynak grafiğini](../../governance/resource-graph/overview.md) doğrudan çağırmak Için kullanılan **değişiklik geçmişi** , ancak döndürülen değişiklikler, [Azure Kaynak grafiğinden](../../governance/resource-graph/overview.md)kaynak düzeyindeki değişiklikleri, [Azure Resource Manager](../../azure-resource-manager/management/overview.md)kaynak özelliklerini ve uygulama Hizmetleri Web uygulaması gibi PaaS hizmetlerinden gelen Konuk değişiklikleri içerir. Uygulama değişikliği çözümleme hizmeti 'nin Kullanıcı aboneliklerinde değişiklik taraması yapabilmesi için, bir kaynak sağlayıcısının kaydedilmesi gerekir. **Değişiklik geçmişi** sekmesine ilk kez girerken araç otomatik olarak **Microsoft. changeanalysis** kaynak sağlayıcısını kaydetmeye başlayacaktır. Kaydolduktan sonra **Azure Kaynak grafındaki** değişiklikler hemen kullanılabilir ve son 14 güne ait olur. Abonelik eklendikten sonra diğer kaynaklardaki değişiklikler ~ 4 saat sonra kullanılabilir olacaktır.

![Etkinlik günlüğü değişiklik geçmişi tümleştirmesi](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>VM öngörüleri tümleştirmesi

[VM](../vm/vminsights-overview.md) içgörüleri etkin olan kullanıcılar, sanal MAKINELERINIZDE, CPU veya bellek gibi bir ölçüm grafiğinde ani artışlar oluşmasına neden olabilecek değişiklikleri görüntüleyebilir. Değişiklik verileri, VM öngörüleri tarafında gezinme çubuğunda tümleştirilir. Kullanıcı, VM 'de herhangi bir değişiklik olup olmadığını görüntüleyebilir ve uygulama değişiklik Analizi tek başına Kullanıcı arabirimindeki değişiklik ayrıntılarını görüntülemek için **değişiklikleri araştır** ' ı seçin.

[![VM öngörüleri tümleştirmesi](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

- [Değişiklik çözümlemede sorunları nasıl giderebileceğinizi](change-analysis-troubleshoot.md) öğrenin
