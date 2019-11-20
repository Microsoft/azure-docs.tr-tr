---
title: Azure Izleyici 'de Azure ağ analizi çözümü | Microsoft Docs
description: Azure ağ güvenlik grubu günlüklerini ve Azure Application Gateway günlüklerini gözden geçirmek için Azure Izleyici 'de Azure ağ analizi çözümünü kullanabilirsiniz.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: 8a8a2f32de905ab7c12f4886d889b2a6fc20c449
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899144"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Azure Izleyici 'de Azure ağ izleme çözümleri

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Izleyici, ağlarınızı izlemek için aşağıdaki çözümleri sunar:
* Ağ Performansı İzleyicisi (NPM)
    * Ağınızın sistem durumunu izleme
* Gözden geçirilecek Azure Application Gateway Analizi
    * Azure Application Gateway günlükleri
    * Azure Application Gateway ölçümleri
* Bulut ağınızda ağ etkinliğini izlemeye ve denetlemeye yönelik çözümler
    * [Trafik Analizi](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Azure Ağ Güvenlik Grubu Analizi

## <a name="network-performance-monitor-npm"></a>Ağ Performansı İzleyicisi (NPM)

[Ağ performansı İzleyicisi](https://docs.microsoft.com/azure/networking/network-monitoring-overview) yönetim çözümü, ağların sistem durumunu, kullanılabilirliğini ve erişilebilirliğini izleyen bir ağ izleme çözümüdür.  Arasındaki bağlantıyı izlemek için kullanılır:

* Genel bulut ve şirket içi
* Veri merkezleri ve kullanıcı konumları (şube ofisleri)
* Çok katmanlı bir uygulamanın çeşitli katmanlarını barındıran alt ağlar.

Daha fazla bilgi için bkz. [ağ performansı İzleyicisi](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure Application Gateway ve ağ güvenlik grubu Analizi
Çözümleri kullanmak için:
1. Yönetim çözümünü Azure Izleyici 'ye ekleyin ve
2. Tanılamayı Azure Izleyici 'de bir Log Analytics çalışma alanına yönlendirmek için tanılamayı etkinleştirin. Günlükleri Azure Blob depolamaya yazmak gerekli değildir.

Tanılamayı ve ilgili çözümü Application Gateway ve ağ güvenlik gruplarından birini ya da her ikisini de etkinleştirebilirsiniz.

Belirli bir kaynak türü için tanılama günlüğünü etkinleştirmezseniz, ancak çözümü yüklerseniz, bu kaynağın Pano dikey pencereleri boştur ve bir hata iletisi görüntüler.

> [!NOTE]
> 2017 Ocak 'ta uygulama ağ geçitlerinden ve ağ güvenlik gruplarından günlüklerin Log Analytics çalışma alanına gönderilmesi için desteklenen yol değişti. **Azure Ağ Analizi (kullanım dışı)** çözümünü görürseniz, izlemeniz gereken adımlar Için [eski ağ analizi çözümünden geçiş](#migrating-from-the-old-networking-analytics-solution) konusuna bakın.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Azure ağ verileri toplama ayrıntılarını gözden geçirme
Azure Application Gateway Analytics ve ağ güvenlik grubu Analizi yönetim çözümleri, tanılama günlüklerini doğrudan Azure uygulama ağ geçitleri ve ağ güvenlik gruplarından toplar. Günlükleri Azure Blob depolama alanına yazmak gerekli değildir ve veri toplama için hiçbir aracı gerekmez.

Aşağıdaki tabloda, veri toplama yöntemleri ve Azure Application Gateway Analytics ve ağ güvenlik grubu analizi için verilerin toplanmasına ilişkin diğer ayrıntılar gösterilmektedir.

| Platform | Doğrudan aracı | Systems Center Operations Manager Aracısı | Azure | Operations Manager gerekli mi? | Yönetim grubu aracılığıyla gönderilen aracı verileri Operations Manager | Toplama sıklığı |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |günlüğe kaydedildiğinde |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Azure Izleyici 'de Azure Application Gateway Analytics çözümü

![Azure Application Gateway Analytics simgesi](media/azure-networking-analytics/azure-analytics-symbol.png)

Uygulama ağ geçitleri için aşağıdaki Günlükler desteklenir:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Uygulama ağ geçitleri için aşağıdaki ölçümler desteklenir: tekrar


* 5 dakikalık aktarım hızı

### <a name="install-and-configure-the-solution"></a>Çözümü yükleyip yapılandırma
Azure Application Gateway Analytics çözümünü yüklemek ve yapılandırmak için aşağıdaki yönergeleri kullanın:

1. Azure Application Gateway Analytics çözümünü [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) 'nden veya [Çözüm Galerisi Azure izleyici çözümlerini ekleme](../../azure-monitor/insights/solutions.md)bölümünde açıklanan işlemi kullanarak etkinleştirin.
2. İzlemek istediğiniz [uygulama ağ geçitleri](../../application-gateway/application-gateway-diagnostics.md) için tanılama günlüğünü etkinleştirin.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Portalda Azure Application Gateway tanılamayı etkinleştirme

1. Azure portal izlemek için Application Gateway kaynağına gidin.
2. *Tanılama günlükleri* ' ni seçerek aşağıdaki sayfayı açın.

   ![Azure Application Gateway kaynağının görüntüsü](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. *Tanılamayı* aç ' a tıklayarak aşağıdaki sayfayı açın.

   ![Azure Application Gateway kaynağının görüntüsü](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Tanılamayı açmak için, *durum*' *a tıklayın.*
5. *Log Analytics gönder*onay kutusuna tıklayın.
6. Mevcut bir Log Analytics çalışma alanını seçin veya bir çalışma alanı oluşturun.
7. Toplanacak günlük türlerinin her biri için **günlük** altında onay kutusuna tıklayın.
8. Azure Izleyici 'de tanılamayı günlüğe kaydetmeyi etkinleştirmek için *Kaydet* ' e tıklayın.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>PowerShell kullanarak Azure Ağ Tanılama 'yı etkinleştirme

Aşağıdaki PowerShell betiği, uygulama ağ geçitleri için tanılama günlüğü 'nün nasıl etkinleştirileceği hakkında bir örnek sağlar.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Azure Application Gateway Analytics 'i kullanma
![Azure Application Gateway Analytics kutucuğunun görüntüsü](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

Genel bakışta **Azure Application Gateway Analytics** kutucuğuna tıkladıktan sonra, günlüklerinizin özetlerini görüntüleyebilir ve ardından aşağıdaki kategorilerde Ayrıntılar için ayrıntıya gidebilirsiniz:

* Application Gateway erişim günlükleri
  * Application Gateway erişim günlükleri için istemci ve sunucu hataları
  * Her bir Application Gateway için saat başına istek
  * Her bir Application Gateway için saat başına başarısız istek
  * Uygulama ağ geçitleri için kullanıcı aracısına göre hatalar
* Application Gateway performans
  * Application Gateway için konak durumu
  * Application Gateway başarısız istekler için maksimum ve 95. yüzdebirlik

![Azure Application Gateway Analytics panosunun görüntüsü](media/azure-networking-analytics/log-analytics-appgateway01.png)

![Azure Application Gateway Analytics panosunun görüntüsü](media/azure-networking-analytics/log-analytics-appgateway02.png)

**Azure Application Gateway Analytics** panosunda, dikey pencerelerden birindeki Özet bilgilerini gözden geçirin ve günlük araması sayfasında ayrıntılı bilgileri görüntülemek için bir tane tıklatın.

Günlük arama sayfalarında, sonuçları zamana, ayrıntılı sonuçlara ve günlük arama geçmişinize göre görüntüleyebilirsiniz. Ayrıca, sonuçları daraltmak için de modellerle filtre uygulayabilirsiniz.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Azure Izleyici 'de Azure ağ güvenlik grubu analizi çözümü

![Azure Ağ Güvenlik Grubu Analizi simgesi](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Ağ güvenlik grubu analizi çözümü, işlevselliği [Trafik Analizi](../../network-watcher/traffic-analytics.md)tarafından değiştirildiğinden topluluk desteğine taşınıyor.
> - Bu çözüm artık [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) kullanılabilir ve yakında Azure Marketi 'nde kullanılamaz.
> - Çözümü çalışma alanına zaten ekleyen mevcut müşteriler için hiçbir değişiklik yapmadan çalışmaya devam edecektir.
> - Microsoft, tanılama ayarlarını kullanarak çalışma alanınıza NSG tanılama günlükleri göndermeyi desteklemeye devam edecektir.

Aşağıdaki Günlükler ağ güvenlik grupları için desteklenir:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Çözümü yükleyip yapılandırma
Azure ağ analizi çözümünü yüklemek ve yapılandırmak için aşağıdaki yönergeleri kullanın:

1. Azure ağ güvenlik grubu Analizi çözümünü [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) 'nden veya [Çözüm Galerisi Azure izleyici çözümlerini ekleme](../../azure-monitor/insights/solutions.md)bölümünde açıklanan işlemi kullanarak etkinleştirin.
2. İzlemek istediğiniz [ağ güvenlik grubu](../../virtual-network/virtual-network-nsg-manage-log.md) kaynakları için tanılama günlüğünü etkinleştirin.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Portalda Azure ağ güvenlik grubu tanılamayı etkinleştirme

1. Azure portal izlemek için ağ güvenlik grubu kaynağına gidin
2. *Tanılama günlüklerini* seçerek aşağıdaki sayfayı açın

   ![Azure ağ güvenlik grubu kaynağının görüntüsü](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. *Tanılamayı* aç ' a tıklayarak aşağıdaki sayfayı açın

   ![Azure ağ güvenlik grubu kaynağının görüntüsü](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Tanılamayı açmak için, *durum* ' *a tıklayın.*
5. *Log Analytics gönder* onay kutusuna tıklayın
6. Mevcut bir Log Analytics çalışma alanını seçin veya bir çalışma alanı oluşturun
7. Toplanacak günlük türlerinin her biri için **günlük** altında onay kutusuna tıklayın
8. Tanılamayı Log Analytics için günlüğe kaydetmeyi etkinleştirmek üzere *Kaydet* ' e tıklayın

### <a name="enable-azure-network-diagnostics-using-powershell"></a>PowerShell kullanarak Azure Ağ Tanılama 'yı etkinleştirme

Aşağıdaki PowerShell betiği, ağ güvenlik grupları için tanılama günlüğünü etkinleştirme hakkında bir örnek sağlar
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Azure ağ güvenlik grubu Analizi 'ni kullanma
Genel bakışta **Azure ağ güvenlik grubu Analizi** kutucuğuna tıkladıktan sonra günlüklerinizin özetlerini görüntüleyebilir ve ardından aşağıdaki kategoriler için ayrıntıya gidebilirsiniz:

* Ağ güvenlik grubu engellenen akışlar
  * Engellenen akışlarla ağ güvenlik grubu kuralları
  * Engellenen akışlar içeren MAC adresleri
* Ağ güvenlik grubu izin verilen akışlar
  * İzin verilen akışlara sahip ağ güvenlik grubu kuralları
  * İzin verilen akışlar içeren MAC adresleri

![Azure ağ güvenlik grubu Analizi panosu görüntüsü](media/azure-networking-analytics/log-analytics-nsg01.png)

![Azure ağ güvenlik grubu Analizi panosu görüntüsü](media/azure-networking-analytics/log-analytics-nsg02.png)

**Azure ağ güvenlik grubu Analizi** panosunda, dikey pencerelerden birindeki Özet bilgilerini gözden geçirin ve günlük araması sayfasında ayrıntılı bilgileri görüntülemek için bir tane tıklatın.

Günlük arama sayfalarında, sonuçları zamana, ayrıntılı sonuçlara ve günlük arama geçmişinize göre görüntüleyebilirsiniz. Ayrıca, sonuçları daraltmak için de modellerle filtre uygulayabilirsiniz.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Eski ağ analizi çözümünden geçiş
2017 Ocak 'ta, Azure Application Gateway ve Azure ağ güvenlik gruplarından günlüklerin bir Log Analytics çalışma alanına gönderilmesi için desteklenen yol değişti. Bu değişiklikler aşağıdaki avantajları sağlar:
+ Günlükler, depolama hesabı kullanılmasına gerek kalmadan doğrudan Azure Izleyici 'ye yazılır
+ Günlüklerin Azure Izleyici 'de kullanılabilir olması için üretilen zamandan daha az gecikme süresi
+ Daha az yapılandırma adımı
+ Tüm Azure tanılama türleri için ortak bir biçim

Güncelleştirilmiş çözümleri kullanmak için:

1. [Tanılamayı Azure Application Gateway 'lerin Azure Izleyici 'ye doğrudan gönderilecek şekilde yapılandırın](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Tanılamayı Azure ağ güvenlik gruplarından Azure Izleyici 'ye doğrudan gönderilecek şekilde yapılandırın](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. [Çözüm Galerisi Azure izleyici çözümlerini ekleme](solutions.md) bölümünde açıklanan işlemi kullanarak *Azure Application Gateway Analytics* ve *Azure ağ güvenlik grubu Analizi* çözümünü etkinleştirin
3. Yeni veri türünü kullanmak için kaydedilen sorguları, panoları veya uyarıları güncelleştirme
   + Tür AzureDiagnostics. Kaynak türü ' nu kullanarak Azure ağ günlüklerine filtre uygulayabilirsiniz.

     | Onun yerine: | Kullanırsınız |
     | --- | --- |
     | &#124; Burada OperationName = = "ApplicationGatewayAccess" olduğunda networkapplicationgateway | AzureDiagnostics &#124; WHERE ResourceType = = "applicationgateway" ve OperationName = = "ApplicationGatewayAccess" |
     | &#124; Burada OperationName = = "ApplicationGatewayPerformance" olduğunda networkapplicationgateway | AzureDiagnostics &#124; WHERE ResourceType = = "applicationgateway" ve OperationName = = "ApplicationGatewayPerformance" |
     | NetworkSecuritygroups | AzureDiagnostics &#124; WHERE ResourceType = = "NETWORKSECURITYGROUPS" |

   + \_s, \_d veya \_bir sonekine sahip herhangi bir alan için ad içinde ilk karakteri küçük harfe değiştirin
   + Adında \_o sonekine sahip herhangi bir alan için, veriler iç içe geçmiş alan adlarına göre tek tek alanlara bölünür.
4. *Azure Ağ Analizi (kullanım dışı)* çözümünü kaldırın.
   + PowerShell kullanıyorsanız, `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false` kullanın

Değişiklik, yeni çözümde görüntülenmeden önce toplanan veriler görünmez. Eski tür ve alan adlarını kullanarak bu verileri sorgulamaya devam edebilirsiniz.

## <a name="troubleshooting"></a>Sorun giderme
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Sonraki adımlar
* Ayrıntılı Azure tanılama verilerini görüntülemek için [Azure izleyici 'de günlük sorguları '](../log-query/log-query-overview.md) nı kullanın.
