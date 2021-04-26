---
title: Web uygulaması sorunlarını bulmak için Azure Izleyici 'de uygulama değişikliği analizini kullanma | Microsoft Docs
description: Azure App Service üzerindeki canlı sitelerde uygulama sorunlarını gidermek için Azure Izleyici 'de uygulama değişikliği analizini kullanın.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 70a9e3a69ec9e9a12e2d9ecb765bc995c82c00b6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010842"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Azure Izleyici 'de uygulama değişikliği analizini (Önizleme) kullanma

Canlı bir site sorunu veya kesintisi oluştuğunda, kök nedenin hızla belirlenmesi kritik öneme sahiptir. Standart izleme çözümleri sizi bir sorunla ilgili olarak uyarabilir. Bunlar, hangi bileşenin başarısız olduğunu bile gösterebilir. Ancak bu uyarı hatanın nedenini her zaman açıklamayacaktır. Sitenizde beş dakika önce çalıştık ve artık bozulmuş. Son beş dakika içinde ne değişti? Bu, uygulama değişikliği analizinin Azure Izleyici 'de yanıtlamak üzere tasarlandığına yönelik sorudır.

[Azure Kaynak Grafiği](../../governance/resource-graph/overview.md)'nin gücüyle çalışırken, değişiklik Analizi Observability artırmak ve MTTR 'i azaltmak için Azure uygulamanızın değişiklikleriyle ilgili öngörüler sağlar (ortalama onarım süresi).

> [!IMPORTANT]
> Değişiklik Analizi Şu anda önizleme aşamasındadır. Bu önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sunulmaktadır. Bu sürüm, üretim iş yükleri için önerilmez. Bazı özellikler desteklenmeyebilir veya kısıtlı özelliklere sahip olabilir. Daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Genel Bakış

Değişiklik analizi, altyapı katmanından uygulama dağıtımına kadar olan çeşitli değişiklik türlerini algılar. Bu, abonelikteki kaynak değişikliklerini denetleyen abonelik düzeyinde bir Azure Kaynak sağlayıcısıdır. Değişiklik analizi, kullanıcıların sorunlara neden olabilecek değişiklikler olduğunu anlamalarına yardımcı olmak için çeşitli tanılama araçları için veri sağlar.

Aşağıdaki diyagramda değişiklik analizinin mimarisi gösterilmektedir:

![Değişiklik analizinin değişiklik verilerini nasıl aldığı ve istemci araçlarına sağladığı mimari diyagramı](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>Desteklenen kaynak türleri

Uygulama değişiklik Analizi hizmeti, aşağıdakiler gibi ortak kaynaklar dahil olmak üzere tüm Azure Kaynak türlerinde kaynak özelliği düzeyi değişikliklerini destekler:
- Sanal Makine
- Sanal makine ölçek kümesi
- App Service
- Azure Kubernetes hizmeti
- Azure İşlevi
- Ağ kaynakları: ağ güvenlik grubu, sanal ağ, Application Gateway, vb.
- Veri Hizmetleri: depolama, SQL, Redis Cache, Cosmos DB, vb.

## <a name="data-sources"></a>Veri kaynakları

Uygulama değişikliği analiz sorguları, Azure Resource Manager izlenen özellikler, proxy kullanan yapılandırma ve Web uygulaması Konuk içi değişiklikler için. Ayrıca, hizmet, bir uygulamanın uçtan uca tanılama ve izleme için kaynak bağımlılığı değişikliklerini izler.

### <a name="azure-resource-manager-tracked-properties-changes"></a>İzlenen Özellikler değişikliklerini Azure Resource Manager

[Azure Kaynak Grafiği](../../governance/resource-graph/overview.md)'ni kullanarak, değişiklik analizi, uygulamanızı barındıran Azure kaynaklarının zaman içinde nasıl değiştiğini gösteren bir geçmiş kaydı sağlar. Yönetilen kimlikler, Platform işletim sistemi yükseltmesi ve ana bilgisayar adları gibi izlenen ayarlar algılanabilir.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager proxy ayar değişiklikleri

IP yapılandırma kuralı, TLS ayarları ve uzantı sürümleri gibi ayarlar henüz Azure Kaynak grafiğinde kullanılamıyor, bu nedenle analiz sorgularını değiştirin ve uygulamada nelerin değiştiğini daha fazla ayrıntı sağlamak için bu değişiklikleri güvenli bir şekilde hesaplar.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Web uygulaması dağıtımı ve yapılandırmasındaki değişiklikler (konuk içi değişiklikler)

Değişiklik analizi, bir uygulamanın dağıtım ve yapılandırma durumunu her 4 saatte bir yakalar. Uygulama ortamı değişkenlerindeki değişiklikleri algılayabilir. Araç farkları hesaplar ve nelerin değiştiğini gösterir. Kaynak Yöneticisi değişikliklerden farklı olarak, kod dağıtımı değişiklik bilgileri araç içinde hemen kullanılamayabilir. Değişiklik analizinde en son değişiklikleri görüntülemek için **Yenile**' yi seçin.

!["Değişiklikleri şimdi Tara" düğmesinin ekran görüntüsü](./media/change-analysis/scan-changes.png)

Şu anda aşağıdaki uzantılara sahip site kökü **Wwwroot** altındaki tüm metin tabanlı dosyalar desteklenir:
- *. JSON
- *. xml
- *. ini
- *. yıml
- *. config
- *. Özellikler
- *. html
- *. cshtml
- *. js
- requirements.txt
- Gemfile
- Gemfile. Lock
- config. gemspec

### <a name="dependency-changes"></a>Bağımlılık değişiklikleri

Kaynak bağımlılıklarındaki değişiklikler, bir kaynaktaki sorunlara da neden olabilir. Örneğin, bir Web uygulaması Redsıs önbelleğine çağırırsa, Redsıs Cache SKU 'SU Web uygulaması performansını etkileyebilir. Diğer bir örnek, bir sanal makinenin ağ güvenlik grubunda bağlantı noktası 22 ' nin kapatılmadığı durumlarda bağlantı hatalarına neden olur.

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Web uygulaması sorun Gezginini tanılama ve çözme (Önizleme)

Bağımlılıklarda yapılan değişiklikleri algılamak için, değişiklik Analizi Web uygulamasının DNS kaydını denetler. Bu şekilde, tüm uygulama bileşenlerinde sorunlara neden olabilecek değişiklikler tanımlanmaktadır.
Şu anda **Web uygulaması tanılama ve çözme sorunlarını aşağıdaki bağımlılıklar destekler | Gezgin (Önizleme)**:

- Web Apps
- Azure Storage
- Azure SQL

#### <a name="related-resources"></a>İlgili kaynaklar

Uygulama değişiklik Analizi ilgili kaynakları algılar. Ortak örnekler, ağ güvenlik grubu, sanal ağ, Application Gateway ve bir sanal makineyle ilgili Load Balancer.
Ağ kaynakları genellikle onu kullanan kaynaklarla aynı kaynak grubunda otomatik olarak sağlanır, bu nedenle değişiklikleri kaynak grubuna göre filtrelemek, sanal makine ve ilgili ağ kaynakları için tüm değişiklikleri gösterir.

![Ağ değişikliklerinin ekran görüntüsü](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>Uygulama değişiklik Analizi hizmeti etkinleştirme

Uygulama değişikliği çözümleme hizmeti, yukarıda belirtilen veri kaynaklarından verileri hesaplar ve toplar. Kullanıcıların tüm kaynak değişikliklerinde kolayca gezinecek ve sorun giderme veya izleme bağlamında hangi değişikliğin ilgili olduğunu belirleyebilecekleri bir analiz kümesi sağlar.
"Microsoft. ChangeAnalysis" kaynak sağlayıcısının, Azure Resource Manager izlenen özellikler için bir aboneliğe kayıtlı olması ve proxy ayarları değişiklik verilerinin kullanılabilir olması gerekir. Web uygulaması tanılama ve çözme sorunları aracını girerken veya değişiklik Analizi tek başına sekmesini getirdiğinizde, bu kaynak sağlayıcı otomatik olarak kaydedilir.
Web uygulaması Konuk içi değişiklikler için, bir Web uygulaması içindeki kod dosyalarını taramak üzere ayrı etkinleştirme gerekir. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında bulunan [sorunları Tanıla ve çöz araç bölümündeki değişiklik Analizi](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool) bölümüne bakın.

## <a name="cost"></a>Maliyet
Uygulama değişikliği Analizi ücretsiz bir hizmettir; BT etkin olan Aboneliklerle ilgili faturalandırma maliyeti yoktur. Ayrıca hizmetin Azure Kaynak özellikleri değişikliklerini taramak için herhangi bir performans etkisi yoktur. Web uygulamaları için değişiklik analizini Konuk dosya değişikliklerine etkinleştirdiğinizde (veya sorunları Tanıla ve çöz aracını etkinleştirirseniz), Web uygulaması üzerinde daha fazla performans etkisi olur ve fatura maliyeti yoktur.


## <a name="enable-change-analysis-at-scale-for-web-app-in-guest-file-and-environment-variable-changes"></a>Web uygulaması Konuk dosya ve ortam değişkeni değişiklikleri için ölçekte değişiklik analizini etkinleştirin

Aboneliğiniz çok sayıda Web uygulaması içeriyorsa, hizmeti Web uygulaması düzeyinde etkinleştirmek verimsiz olur. Aboneliğinizdeki tüm Web uygulamalarını etkinleştirmek için aşağıdaki betiği çalıştırın.

Önkoşullar:

- PowerShell az Module. [Azure PowerShell modülünü yüklerken](/powershell/azure/install-az-ps) yönergeleri izleyin

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

- [Değişiklik çözümlemede görselleştirmeler](change-analysis-visualizations.md) hakkında bilgi edinin
- [Değişiklik çözümlemede sorunları nasıl giderebileceğinizi](change-analysis-troubleshoot.md) öğrenin
- [Azure Uygulama Hizmetleri uygulamaları](azure-web-apps.md)için Application Insights etkinleştirin.
- [Azure VM ve Azure sanal makine ölçek kümesi için Application Insights ETKINLEŞTIRME IIS tarafından barındırılan uygulamalar](azure-vm-vmss-apps.md).
