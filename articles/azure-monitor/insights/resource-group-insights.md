---
title: Azure Izleyici kaynak grubu öngörüleri | Microsoft Docs
description: Azure Izleyici ile kaynak grubu düzeyinde dağıtılmış uygulamalarınızın ve hizmetlerinizin sistem durumunu ve performansını anlayın
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: fba94a5e723bfbc2719b3b4cf5cd130eda382276
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553978"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Azure Izleyici ile kaynak gruplarını izleme (Önizleme)

Modern uygulamalar genellikle karmaşık ve yüksek oranda bir hizmet sunmak için birlikte çalışan çok sayıda ayrı bölümden dağıtılır. Bu karmaşıklığı tanıma, Azure Izleyici, kaynak grupları için izleme öngörüleri sağlar. Bu, bireysel kaynaklarınızın karşılaştığı her türlü sorunu önceliklendirme ve tanılamanıza ve uygulamanızın bir bütün &mdash;as &mdash;and kaynak grubunun sistem durumu ve performansına göre bir bağlam sunabilmesini kolaylaştırır.

## <a name="access-insights-for-resource-groups"></a>Kaynak grupları için Öngörüler 'e erişin

1. Sol taraftaki Gezinti çubuğundan **kaynak grupları** ' nı seçin.
2. Araştırmak istediğiniz kaynak gruplarınızdan birini seçin. (Aboneliğe göre filtrelemeye göre çok sayıda kaynak grubunuz varsa bazen yararlı olabilir.)
3. Bir kaynak grubuna yönelik Öngörüler 'e erişmek için herhangi bir kaynak grubunun sol taraftaki menüsünde **Öngörüler** ' e tıklayın.

![Kaynak grubu öngörülerine Genel Bakış sayfasının ekran görüntüsü](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Etkin uyarıları ve sistem durumu sorunlarını içeren kaynaklar

Genel Bakış sayfası, kaç uyarının tetiklendiğinin ve hala etkin olduğunu, her kaynağın geçerli Azure Kaynak Durumu gösterir. Bu bilgiler birlikte, sorun yaşayan kaynakları hızlıca belirlemenize yardımcı olabilir. Uyarılar kodunuzdaki sorunları ve altyapınızı nasıl yapılandırdığınızı tespit etmenize yardımcı olur. Tek tek uygulamalarınıza özgü olmayan Azure platformu ile yüzey sorunu Azure Kaynak Durumu.

![Azure Kaynak Durumu bölmesinin ekran görüntüsü](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Kaynak Durumu

Azure Kaynak Durumu görüntülemek için tablonun üzerindeki **Azure Kaynak durumu göster** kutusunu işaretleyin. Bu sütun, sayfanın hızlı yüklenmesine yardımcı olmak için varsayılan olarak gizlidir.

![Kaynak sistem durumu grafı eklenmiş ekran görüntüsü](./media/resource-group-insights/0003-overview.png)

Varsayılan olarak, kaynaklar uygulama katmanına ve kaynak türüne göre gruplandırılır. **Uygulama katmanı** , kaynak türlerinin yalnızca kaynak grubu öngörüleri Genel Bakış sayfasının bağlamı içinde bulunan basit bir kategorileştirildi. Uygulama kodu, işlem altyapısı, ağ, depolama + veritabanları ile ilgili kaynak türleri vardır. Yönetim Araçları kendi uygulama katmanlarını alır ve diğer her kaynak **diğer** uygulama katmanına ait olarak kategorize edilir. Bu gruplandırma, uygulamanızın hangi alt sistemlerinin sağlıklı ve sağlıksız olduğunu görmenize yardımcı olabilir.

## <a name="diagnose-issues-in-your-resource-group"></a>Kaynak grubunuzda sorunları tanılayın

Kaynak grubu öngörüleri sayfası, sorunları tanılamanıza yardımcı olacak çeşitli diğer araçlar sağlar

   |         |          |
   | ---------------- |:-----|
   | [**Larınız**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Uyarılarınızı görüntüleyin, oluşturun ve yönetin. |
   | [**Ölçümler**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Ölçüm tabanlı verilerinizi görselleştirin ve araştırın.    |
   | [**Etkinlik günlükleri**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Azure 'da oluşan abonelik düzeyindeki olaylar.  |
   | [**Uygulama eşlemesi**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Performans sorunlarını veya hata etkin noktalarını belirlemek için dağıtılmış uygulamanızın topolojisine gidin. |

## <a name="failures-and-performance"></a>Başarısızlıklar ve performans

Uygulamanızın yavaş çalıştığını fark etmişseniz veya kullanıcılar hata bildirdiyseniz ne olur? Sorunları yalıtmak için tüm kaynaklarınızın içinde arama yapmak zaman alır.

**Performans** ve **hatalar** sekmeleri, birçok ortak kaynak türü için performans ve başarısızlık tanılama görünümlerini bir araya getirerek bu işlemi basitleştirir.

Çoğu kaynak türü, Azure Izleyici çalışma kitabı şablonlarının bir galerisini açar. Oluşturduğunuz her çalışma kitabı özelleştirilebilir, kaydedilebilir, takımınızla paylaşılabilir ve ileride benzer sorunları tanılamak için yeniden kullanılabilir.

### <a name="investigate-failures"></a>Sorunları araştırın

Arızalar sekmesini sınamak için sol taraftaki menüde **Araştır** ' ın altındaki **başarısızlık** ' ı seçin.

Seçiminiz yapıldıktan sonra, sol taraftaki menü çubuğu değişir ve size yeni seçenekler sunar.

![Hata genel bakış bölmesinin ekran görüntüsü](./media/resource-group-insights/00004-failures.png)

App Service seçildiğinde, Azure Izleyici çalışma kitabı şablonlarının bir galerisiyle karşılaşırsınız.

![Uygulama çalışma kitabı galerisinin ekran görüntüsü](./media/resource-group-insights/0005-failure-insights-workbook.png)

Hata öngörüleri şablonunu seçmek çalışma kitabını açar.

![Hata raporunun ekran görüntüsü](./media/resource-group-insights/0006-failure-visual.png)

Satırlardan herhangi birini seçebilirsiniz. Seçim daha sonra bir grafik Ayrıntıları görünümünde görüntülenir.

![Hata ayrıntılarının ekran görüntüsü](./media/resource-group-insights/0007-failure-details.png)

Çalışma kitapları, özel raporlar ve görselleştirmeler oluşturmanın kolayca tüketilebilir bir biçimde soyutlaştırılması zor çalışmalardır. Bazı kullanıcılar yalnızca önceden oluşturulmuş parametreleri ayarlamak isteyebilir, ancak çalışma kitapları tamamen özelleştirilebilir.

Bu çalışma kitabının dahili olarak nasıl çalıştığını öğrenmek için üstteki çubukta **Düzenle** ' yi seçin.

![Ek düzenleme seçeneğinin ekran görüntüsü](./media/resource-group-insights/0008-failure-edit.png)

Çalışma kitabının çeşitli öğelerinin yakınında bir dizi **düzenleme** kutusu görünür. İşlem tablosunun altındaki **düzenleme** kutusunu seçin.

![Düzenleme kutularının ekran görüntüsü](./media/resource-group-insights/0009-failure-edit-graph.png)

Bu, tablo görselleştirmesini yönlendiren temel alınan günlük sorgusunu ortaya koyar.

 ![Günlük sorgusu penceresinin ekran görüntüsü](./media/resource-group-insights/0010-failure-edit-query.png)

Sorguyu doğrudan değiştirebilirsiniz. Ya da bunu bir başvuru olarak kullanabilir ve kendi özel parametreli çalışma kitabınızı tasarlarken bu kaynaktan ödünç alabilirsiniz.

### <a name="investigate-performance"></a>Performansı araştır

Performans kendi çalışma kitabı galerisini sunar. App Service önceden oluşturulmuş uygulama performansı çalışma kitabı aşağıdaki görünümü sunmaktadır:

 ![Performans görünümünün ekran görüntüsü](./media/resource-group-insights/0011-performance.png)

Bu durumda, Düzenle ' yi seçerseniz bu görselleştirme kümesinin Azure Izleyici ölçümleri tarafından gücünün olduğunu görürsünüz.

 ![Azure ölçümleriyle performans görünümünün ekran görüntüsü](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Sorun giderme

### <a name="enabling-access-to-alerts"></a>Uyarılara erişimi etkinleştirme

Kaynak grupları için Azure Izleyici 'deki uyarıları görmek için, bu aboneliğin sahibi veya katkıda bulunan rolü olan birinin, abonelikteki herhangi bir kaynak grubuna ait kaynak grupları için Azure Izleyicisini açması gerekir. Bu, okuma erişimi olan herkesin, abonelikteki tüm kaynak grupları için kaynak grupları için Azure Izleyici 'deki uyarıları görmesini sağlar. Bir sahip veya katkıda bulunan rolüne sahipseniz, bu sayfayı birkaç dakika içinde yenileyin.

Kaynak grupları için Azure Izleyici, uyarı durumunu almak için Azure Izleyici uyarıları yönetim sistemine bağımlıdır. Her kaynak grubu ve abonelik için varsayılan olarak uyarı yönetimi yapılandırılmamıştır ve yalnızca sahip veya katkıda bulunan rolü olan birisi tarafından etkinleştirilebilir. Bu, şu şekilde etkinleştirilebilir:
* Abonelikteki herhangi bir kaynak grubu için kaynak grupları için Azure Izleyici açılıyor.
* Ya da aboneliğe gidip **kaynak sağlayıcıları**' na tıklayıp **Uyarılar Için kaydol. Yönetim**' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Izleyici çalışma kitapları](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Kaynak Durumu](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Azure Izleyici uyarıları](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
