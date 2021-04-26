---
title: Azure Güvenlik Duvarı günlüklerini ve ölçümlerini izleme
description: Bu makalede, Azure Güvenlik Duvarı günlüklerinin ve ölçümlerinin nasıl etkinleştirileceğini ve yönetileceğini öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 52c6ef9edfc42bf1ad3b3279e0fa4e19b4cf502c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788273"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Azure Güvenlik Duvarı günlüklerini ve ölçümlerini izleme

Güvenlik duvarı günlüklerini kullanarak Azure Güvenlik Duvarı'nı izleyebilirsiniz. Ayrıca etkinlik günlüklerini kullanarak Azure Güvenlik Duvarı kaynaklarıyla ilgili işlemleri denetleyebilirsiniz. Ölçümleri kullanarak portalda performans sayaçlarını görüntüleyebilirsiniz.

Bu günlüklerden bazılarına portaldan erişebilirsiniz. Günlükler [Azure izleyici](../azure-monitor/insights/azure-networking-analytics.md)günlüklerine, depolamaya ve Event Hubs gönderilebilir ve Azure izleyici günlüklerinde veya Excel ve Power BI gibi farklı araçlarla analiz edilebilir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce Azure Güvenlik Duvarı için kullanılabilen tanılama günlüklerine ve ölçümlere genel bir bakış için [Azure Güvenlik Duvarı günlüklerini ve ölçümlerini](logs-and-metrics.md) okumalısınız.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Azure portaldan tanılama günlüğüne kaydetmeyi etkinleştirme

Tanılama günlüğüne kaydetme işlemi etkinleştirildikten sonra verilerin günlükte görünmesi birkaç dakika sürebilir. İlk seferde görünen veri olmazsa birkaç dakika sonra tekrar deneyin.

1. Azure portal, güvenlik duvarı kaynak grubunuzu açın ve güvenlik duvarını seçin.
2. **İzleme** bölümünde **Tanılama ayarları**'nı seçin.

   Azure Güvenlik Duvarı için hizmete özgü dört günlük kullanılabilir:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. **Tanılama ayarı ekle**’yi seçin. **Tanılama ayarları** sayfasında tanılama günlükleriyle ilgili ayarlar bulunur.
5. Bu örnekte, Azure Izleyici günlükleri günlükleri depolar, bu nedenle ad için **güvenlik duvarı Log Analytics** yazın.
6. **Günlük** altında, günlükleri toplamak için **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule**, **AzureFirewallThreatIntelLog** ve **AzureFirewallDnsProxy** öğesini seçin.
7. Çalışma alanınızı yapılandırmak için **Log Analytics gönder** ' i seçin.
8. Aboneliğinizi seçin.
9. **Kaydet**’i seçin.

## <a name="enable-diagnostic-logging-by-using-powershell"></a>PowerShell kullanarak tanılama günlüğünü etkinleştirme

Etkinlik günlüğü tüm Kaynak Yöneticisi kaynakları için otomatik olarak etkinleştirilir. Bu günlükler aracılığıyla sunulan verileri toplamaya başlamak için tanılama günlüğüne kaydetme işlevinin etkinleştirilmesi gerekir.

Tanılama günlük kaydını PowerShell ile etkinleştirmek için aşağıdaki adımları kullanın:

1. Log Analytics çalışma alanı kaynak KIMLIĞINIZI, burada günlük verilerinin depolandığını aklınızda yapın. Bu değer şu biçimdedir: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>` .

   Aboneliğinizdeki herhangi bir çalışma alanını kullanabilirsiniz. Bu bilgileri Azure portalda bulabilirsiniz. Bilgiler, kaynak **özellikleri** sayfasında bulunur.

2. Günlüğe kaydetmenin etkinleştirildiği güvenlik duvarının kaynak kimliğini not edin. Bu değer şu biçimdedir: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   Bu bilgileri portalda bulabilirsiniz.

3. Aşağıdaki PowerShell cmdlet 'ini kullanarak tüm Günlükler ve ölçümler için tanılama günlüğünü etkinleştirin:

   ```powershell
   $diagSettings = @{
      Name = 'toLogAnalytics'
      ResourceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      WorkspaceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      Enabled = $true
   }
   Set-AzDiagnosticSetting  @diagSettings 
   ```

## <a name="enable-diagnostic-logging-by-using-the-azure-cli"></a>Azure CLı kullanarak tanılama günlüğünü etkinleştirme

Etkinlik günlüğü tüm Kaynak Yöneticisi kaynakları için otomatik olarak etkinleştirilir. Bu günlükler aracılığıyla sunulan verileri toplamaya başlamak için tanılama günlüğüne kaydetme işlevinin etkinleştirilmesi gerekir.

Azure CLı ile tanılama günlüğünü etkinleştirmek için aşağıdaki adımları kullanın:

1. Log Analytics çalışma alanı kaynak KIMLIĞINIZI, burada günlük verilerinin depolandığını aklınızda yapın. Bu değer şu biçimdedir: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   Aboneliğinizdeki herhangi bir çalışma alanını kullanabilirsiniz. Bu bilgileri Azure portalda bulabilirsiniz. Bilgiler, kaynak **özellikleri** sayfasında bulunur.

2. Günlüğe kaydetmenin etkinleştirildiği güvenlik duvarının kaynak kimliğini not edin. Bu değer şu biçimdedir: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   Bu bilgileri portalda bulabilirsiniz.

3. Aşağıdaki Azure CLı komutunu kullanarak tüm Günlükler ve ölçümler için tanılama günlüğünü etkinleştirin:

   ```azurecli-interactive
   az monitor diagnostic-settings create -n 'toLogAnalytics'
      --resource '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      --workspace '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      --logs '[{\"category\":\"AzureFirewallApplicationRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallNetworkRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallDnsProxy\",\"Enabled\":true}]' 
      --metrics '[{\"category\": \"AllMetrics\",\"enabled\": true}]'
   ```

## <a name="view-and-analyze-the-activity-log"></a>Etkinlik günlüğünü görüntüleme ve analiz etme

Aşağıdaki yöntemlerden birini kullanarak etkinlik günlüğü verilerini görüntüleyebilir ve analiz edebilirsiniz:

* **Azure araçları**: Etkinlik günlüğü verilerini Azure PowerShell, Azure CLI, Azure REST API veya Azure portal üzerinden alabilirsiniz. Her yöntemle ilgili ayrıntılı adımlar [Kaynak Yöneticisi etkinlik işlemleri](../azure-resource-manager/management/view-activity-logs.md) makalesinde ayrıntılı bir şekilde anlatılmıştır.
* **Power BI**: [Power BI](https://powerbi.microsoft.com/pricing) hesabınız yoksa ücretsiz oluşturabilirsiniz. [Power BI için Azure Activity Logs içerik paketi](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) ile verilerinizi önceden yapılandırılmış panoları olduğu gibi veya değiştirerek kullanarak analiz edebilirsiniz.
* **Azure Sentinel**: Azure Güvenlik Duvarı günlüklerini Azure Sentinel 'e bağlanarak, çalışma kitaplarında günlük verilerini görüntülemenize, özel uyarılar oluşturmak için bu uygulamayı kullanmanıza ve araştırmanızı iyileştirebilecek şekilde katabilirsiniz. Azure Sentinel 'deki Azure Güvenlik Duvarı veri Bağlayıcısı Şu anda genel önizlemededir. Daha fazla bilgi için bkz. [Azure güvenlik duvarından verileri bağlama](../sentinel/connect-azure-firewall.md).

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Ağ ve uygulama kuralı günlüklerini görüntüleme ve analiz etme

[Azure izleyici günlükleri](../azure-monitor/insights/azure-networking-analytics.md) sayaç ve olay günlüğü dosyalarını toplar. Günlüklerinizi analiz etmek için görselleştirmelere ve güçlü arama özelliklerine sahiptir.

Azure Güvenlik Duvarı günlük Analizi örnek sorguları için bkz. [Azure Güvenlik Duvarı günlük Analizi örnekleri](./firewall-workbook.md).

[Azure Güvenlik Duvarı çalışma kitabı](firewall-workbook.md) , Azure Güvenlik Duvarı veri analizi için esnek bir tuval sağlar. Azure portal içinde zengin görsel raporlar oluşturmak için kullanabilirsiniz. Azure üzerinde dağıtılan birden çok Güvenlik duvarınıza dokunabilir ve bunları Birleşik etkileşimli deneyimler halinde birleştirebilirsiniz.

Dilerseniz depolama hesabınıza bağlanabilir ve JSON erişim günlüklerini ve performans günlüklerini alabilirsiniz. İndirdiğiniz JSON dosyalarını CSV biçimine dönüştürebilir ve Excel, Power BI veya diğer veri görselleştirme araçlarında görüntüleyebilirsiniz.

> [!TIP]
> Visual Studio ve C# ile sabit ve değişken değerlerini değiştirme konusunda temel kavramlara hakimseniz GitHub'daki [günlük dönüştürücü araçlarını](https://github.com/Azure-Samples/networking-dotnet-log-converter) kullanabilirsiniz.

## <a name="view-metrics"></a>Ölçümleri görüntüle
Azure Güvenlik Duvarı ' na giderek **Izleme** **ölçüm**' i seçin. Kullanılabilir değerleri görüntülemek için **ÖLÇÜM** açılan listesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Güvenlik duvarınızı günlükleri toplayacak şekilde yapılandırdığınıza göre, verilerinizi görüntülemek için Azure Izleyici günlüklerini keşfedebilirsiniz.

[Azure Güvenlik Duvarı çalışma kitabını kullanarak günlükleri izleme](firewall-workbook.md)

[Azure Izleyici günlüklerinde ağ izleme çözümleri](../azure-monitor/insights/azure-networking-analytics.md)