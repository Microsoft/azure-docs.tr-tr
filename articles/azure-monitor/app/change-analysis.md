---
title: Web uygulaması sorunlarını bulmak için Azure Izleyici 'de uygulama değişikliği analizini kullanma | Microsoft Docs
description: Azure App Service üzerindeki canlı sitelerde uygulama sorunlarını gidermek için Azure Izleyici 'de uygulama değişikliği analizini kullanın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: ed297a1005f67a14db1da15aba2c47c98e83df9c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885055"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Azure Izleyici 'de uygulama değişikliği analizini (Önizleme) kullanma

Canlı bir site sorunu veya kesintisi oluştuğunda, kök nedenin hızla belirlenmesi kritik öneme sahiptir. Standart izleme çözümleri sizi bir sorunla ilgili olarak uyarabilir. Bunlar, hangi bileşenin başarısız olduğunu bile gösterebilir. Ancak bu uyarı hatanın nedenini her zaman açıklamayacaktır. Sitenizde beş dakika önce çalıştık ve artık bozulmuş. Son beş dakika içinde ne değişti? Bu, uygulama değişikliği analizinin Azure Izleyici 'de yanıtlamak üzere tasarlandığına yönelik sorudır.

[Azure Kaynak Grafiği](https://docs.microsoft.com/azure/governance/resource-graph/overview)'nin gücüyle çalışırken, değişiklik Analizi Observability artırmak ve MTTR 'i azaltmak için Azure uygulamanızın değişiklikleriyle ilgili öngörüler sağlar (ortalama onarım süresi).

> [!IMPORTANT]
> Değişiklik Analizi Şu anda önizleme aşamasındadır. Bu önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sunulmaktadır. Bu sürüm, üretim iş yükleri için önerilmez. Bazı özellikler desteklenmeyebilir veya kısıtlı özelliklere sahip olabilir. Daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Genel Bakış

Değişiklik analizi, altyapı katmanından uygulama dağıtımına kadar olan çeşitli değişiklik türlerini algılar. Bu, abonelikteki kaynak değişikliklerini denetleyen abonelik düzeyinde bir Azure Kaynak sağlayıcısıdır. Değişiklik analizi, kullanıcıların sorunlara neden olabilecek değişiklikler olduğunu anlamalarına yardımcı olmak için çeşitli tanılama araçları için veri sağlar.

Aşağıdaki diyagramda değişiklik analizinin mimarisi gösterilmektedir:

![Değişiklik analizinin değişiklik verilerini nasıl aldığı ve istemci araçlarına sağladığı mimari diyagramı](./media/change-analysis/overview.png)

Şu anda değişiklik analizi, App Service Web uygulamasındaki **sorunları Tanıla ve çöz** deneyimiyle tümleşiktir ve Azure Portal bir tek başına dikey pencere olarak kullanılabilir.
Değişiklik Analizi dikey penceresine erişmek için *Azure 'daki tüm kaynaklarla ilgili değişiklikleri görüntüleme* bölümüne ve bu makalenin Ilerleyen kısımlarında Web App Portal içinde kullanmak üzere *Web Apps özelliği için değişiklik analizini* bölümüne bakın.

### <a name="azure-resource-manager-tracked-properties-changes"></a>İzlenen Özellikler değişikliklerini Azure Resource Manager

[Azure Kaynak Grafiği](https://docs.microsoft.com/azure/governance/resource-graph/overview)'ni kullanarak, değişiklik analizi, uygulamanızı barındıran Azure kaynaklarının zaman içinde nasıl değiştiğini gösteren bir geçmiş kaydı sağlar. Yönetilen kimlikler, Platform işletim sistemi yükseltmesi ve ana bilgisayar adları gibi izlenen ayarlar algılanabilir.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager proxy ayar değişiklikleri
IP yapılandırma kuralı, SSL ayarları ve uzantı sürümleri gibi ayarlar bağımsız değişken içinde henüz kullanılamamaktadır, bu nedenle analiz sorgularını değiştirin ve uygulamada nelerin değiştiğini daha fazla ayrıntı sağlamak için bu değişiklikleri güvenli bir şekilde hesaplar. Bu bilgiler henüz Azure Kaynak grafiğinde kullanılamaz, ancak yakında kullanıma sunulacaktır.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Web uygulaması dağıtımı ve yapılandırmasındaki değişiklikler (konuk içi değişiklikler)

Değişiklik analizi, bir uygulamanın dağıtım ve yapılandırma durumunu her 4 saatte bir yakalar. Uygulama ortamı değişkenlerindeki değişiklikleri algılayabilir. Araç farkları hesaplar ve nelerin değiştiğini gösterir. Kaynak Yöneticisi değişikliklerden farklı olarak, kod dağıtımı değişiklik bilgileri araç içinde hemen kullanılamayabilir. Değişiklik analizinde yapılan en son değişiklikleri görüntülemek için **değişiklikleri şimdi Tara**' yı seçin.

!["Değişiklikleri şimdi Tara" düğmesinin ekran görüntüsü](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Bağımlılık değişiklikleri

Kaynak bağımlılıklarındaki değişiklikler de bir Web uygulamasında sorunlara neden olabilir. Örneğin, bir Web uygulaması Redsıs önbelleğine çağırırsa, Redsıs Cache SKU 'SU Web uygulaması performansını etkileyebilir. Bağımlılıklarda yapılan değişiklikleri algılamak için, değişiklik Analizi Web uygulamasının DNS kaydını denetler. Bu şekilde, tüm uygulama bileşenlerinde sorunlara neden olabilecek değişiklikler tanımlanmaktadır.
Şu anda aşağıdaki bağımlılıklar desteklenir:
- Web Apps
- Azure Storage
- Azure SQL

### <a name="enablement"></a>Etkinleştirme
"Microsoft. ChangeAnalysis" kaynak sağlayıcısının, Azure Resource Manager izlenen özellikler için bir aboneliğe kayıtlı olması ve proxy ayarları değişiklik verilerinin kullanılabilir olması gerekir. Web uygulamasını girerken sorunları tanılayın ve çözün veya değişiklik Analizi tek başına dikey penceresini getirin, bu kaynak sağlayıcı otomatik olarak kaydedilir. Aboneliğiniz için herhangi bir performans ve maliyet uygulaması yoktur.
Web uygulaması Konuk içi değişiklikler için, bir Web uygulaması içindeki kod dosyalarını taramak üzere ayrı etkinleştirme gerekir. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında bulunan *sorunları Tanıla ve çöz aracında bulunan değişiklik analizini etkinleştir* bölümüne bakın.

## <a name="viewing-changes-for-all-resources-in-azure"></a>Azure 'daki tüm kaynaklar için değişiklikleri görüntüleme
Azure Izleyici 'de, Öngörüler ve uygulama bağımlılıkları kaynaklarıyla ilgili tüm değişiklikleri görüntülemek için değişiklik analizinin tek başına dikey penceresi vardır.

Dikey pencereyi başlatmak için Azure portal arama çubuğunda bulunan değişiklik analizini arayın.

![Azure portal değişiklik analizini aramanın ekran görüntüsü](./media/change-analysis/search-change-analysis.png)

Değişiklikleri görüntülemeye başlamak için kaynak grubu ve kaynaklar ' ı seçin.

![Azure portal değişiklik Analizi dikey penceresinin ekran görüntüsü](./media/change-analysis/change-analysis-standalone-blade.png)

Uygulamanızı barındıran öngörüleri ve ilgili bağımlılıklar kaynaklarını görebilirsiniz. Bu görünüm, geliştiricilerin sorunları gidermelerine yönelik uygulama odaklı olacak şekilde tasarlanmıştır.

Şu anda desteklenen kaynaklar şunlardır:
- Virtual Machines
- Sanal makine ölçek kümesi
- Azure ağ kaynakları
- Konuk içi dosya izleme ve ortam değişkenleri değişiklikleri içeren Web uygulaması

Geri bildirimde bulunmak için lütfen dikey pencere veya e-posta changeanalysisteam@microsoft.comgeri bildirim gönder düğmesini kullanın.

![Değişiklik Analizi dikey penceresinde geri bildirim düğmesinin ekran görüntüsü](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Web Apps özelliği için değişiklik Analizi

Azure Izleyici 'de, değişiklik Analizi Ayrıca self servis **Tanılama ve sorun sorunları** deneyiminde yerleşik olarak bulunur. App Service uygulamanızın **genel bakış** sayfasından bu deneyimle erişin.

!["Genel bakış" düğmesinin ve "sorunları tanılama ve çözme" düğmesinin ekran görüntüsü](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Sorunları tanılama ve çözme aracında değişiklik analizini etkinleştirme

1. **Kullanılabilirlik ve performans ' ı**seçin.

    !["Kullanılabilirlik ve performans" sorun giderme seçeneklerinin ekran görüntüsü](./media/change-analysis/availability-and-performance.png)

1. **Uygulama değişikliklerini**seçin. Özelliği **uygulama Kilitlenmelerinde**da kullanılabilir değildir.

   !["Uygulama kilitlenmeler" düğmesinin ekran görüntüsü](./media/change-analysis/application-changes.png)

1. Değişiklik analizini etkinleştirmek için **Şimdi etkinleştir**' i seçin.

   !["Uygulama kilitlenmeler" seçeneklerinin ekran görüntüsü](./media/change-analysis/enable-changeanalysis.png)

1. **Değişiklik analizini** açın ve **Kaydet**' i seçin. Araç, tüm Web uygulamalarını bir App Services planı altında görüntüler. Plan düzeyi anahtarını, bir plandaki tüm Web uygulamalarının değişiklik analizini açmak için kullanabilirsiniz.

    !["Değişiklik analizini etkinleştir" Kullanıcı arabiriminin ekran görüntüsü](./media/change-analysis/change-analysis-on.png)


1. Değişiklik analizine erişmek için, **kullanılabilirlik ve performans** > **uygulama kilitlenmelerine** > **Tanılama ve çözme sorunlarını çözün** . Zaman içindeki değişikliklerin türünü ve bu değişikliklerle ilgili ayrıntıları özetleyen bir grafik görürsünüz. Varsayılan olarak, son 24 saat içindeki değişiklikler anında sorunla ilgili yardım almak için görüntülenir.

     ![Değişiklik fark görünümünün ekran görüntüsü](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Ölçek üzerinde değişiklik analizini etkinleştir

Aboneliğiniz çok sayıda Web uygulaması içeriyorsa, hizmeti Web uygulaması düzeyinde etkinleştirmek verimsiz olur. Aboneliğinizdeki tüm Web uygulamalarını etkinleştirmek için aşağıdaki betiği çalıştırın.

Önkoşulların önkoşulları:
* PowerShell az Module. [Azure PowerShell modülünü yüklerken](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0) yönergeleri izleyin

Şu betiği çalıştırın:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>Sonraki adımlar

- [Azure Uygulama Hizmetleri uygulamaları](azure-web-apps.md)için Application Insights etkinleştirin.
- [Azure VM ve Azure sanal makine ölçek kümesi için Application Insights ETKINLEŞTIRME IIS tarafından barındırılan uygulamalar](azure-vm-vmss-apps.md).
- Power Change analizine yardımcı olan [Azure Kaynak Grafiği](https://docs.microsoft.com/azure/governance/resource-graph/overview)hakkında daha fazla bilgi edinin.
