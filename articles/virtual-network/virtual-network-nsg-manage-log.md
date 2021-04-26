---
title: Ağ güvenlik grubu için tanılama kaynağı günlüğü
titlesuffix: Azure Virtual Network
description: Azure ağ güvenlik grubu için olay ve kural sayacı tanılama kaynak günlüklerini etkinleştirmeyi öğrenin.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 0d171dee87a391c5e1d66db10363e6823ef387c1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774170"
---
# <a name="resource-logging-for-a-network-security-group"></a>Bir ağ güvenlik grubu için kaynak günlüğü

Bir ağ güvenlik grubu (NSG), bir sanal ağ alt ağına, ağ arabirimine veya her ikisine birden trafiğe izin veren veya reddeden kurallar içerir. 

Bir NSG için günlük kaydını etkinleştirdiğinizde aşağıdaki kaynak günlük bilgileri türlerini toplayabilirsiniz:

* **Olay:** Şirket, MAC adresine bağlı olarak VM 'lere uygulanan NSG kuralları için günlüğe kaydedilir.
* **Kural sayacı:** Her NSG kuralının, trafiği reddetme veya izin verme için kaç kez uygulanacağını gösteren girişleri içerir. Bu kuralların durumu her 300 saniyede toplanır.

Kaynak günlükleri yalnızca Azure Resource Manager dağıtım modeliyle dağıtılan NSG 'ler için kullanılabilir. Klasik dağıtım modeli aracılığıyla dağıtılan NSG 'ler için kaynak günlüğü etkinleştiremezsiniz. İki modeli daha iyi anlamak için bkz. [Azure dağıtım modellerini anlama](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Kaynak günlüğü, için tanılama verilerini toplamak istediğiniz *her* NSG için ayrı olarak etkinleştirilir. Bunun yerine etkinlik (işlemsel) günlükleri ile ilgileniyorsanız, bkz. Azure [etkinlik günlüğü](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). NSG 'ler aracılığıyla IP trafiği ile ilgileniyorsanız, bkz. Azure ağ Izleyicisi [NSG akış günlükleri](../network-watcher/network-watcher-nsg-flow-logging-overview.md) 

## <a name="enable-logging"></a>Günlü kaydını etkinleştir

Kaynak günlüğünü etkinleştirmek için [Azure Portal](#azure-portal), [POWERSHELL](#powershell)veya [Azure CLI](#azure-cli) kullanabilirsiniz.

### <a name="azure-portal"></a>Azure Portalı

1. [Portalda](https://portal.azure.com)oturum açın.
2. **Tüm hizmetler**' i seçin, ardından *ağ güvenlik grupları* yazın. Arama sonuçlarında **ağ güvenlik grupları** görüntülendiğinde, bunu seçin.
3. Günlük kaydını etkinleştirmek istediğiniz NSG 'yi seçin.
4. **İzleme** altında **tanılama günlükleri**' ni seçin ve ardından aşağıdaki resimde gösterildiği gibi **tanılamayı aç**' ı seçin:

   ![Tanılamayı açma](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. **Tanılama ayarları** altında aşağıdaki bilgileri girin veya seçin ve ardından **Kaydet**' i seçin:

    | Ayar                                                                                     | Değer                                                          |
    | ---------                                                                                   |---------                                                       |
    | Ad                                                                                        | Seçinizdeki bir ad.  Örneğin: *Mynsgdiagnostics*      |
    | **Bir depolama hesabına arşivleme**, **bir olay hub 'ına akış** ve **Log Analytics gönderme** | Seçtiğiniz sayıda hedef seçebilirsiniz. Her biri hakkında daha fazla bilgi için bkz. [günlük hedefleri](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Ya da her iki günlük kategorisini seçin. Her kategori için günlüğe kaydedilen veriler hakkında daha fazla bilgi edinmek için bkz. [günlük kategorileri](#log-categories).                                                                                                                                             |
6. Günlükleri görüntüleyin ve çözümleyin. Daha fazla bilgi için bkz. [günlükleri görüntüleme ve çözümleme](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Cloud Shell](https://shell.azure.com/powershell)izleyen komutları veya bilgisayarınızdan PowerShell 'i çalıştırarak çalıştırabilirsiniz. Azure Cloud Shell, ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. PowerShell 'i bilgisayarınızdan çalıştırırsanız, Azure PowerShell Module, sürüm 1.0.0 veya sonraki bir sürümü gerekir. `Get-Module -ListAvailable Az`Yüklü sürümü bulmak için bilgisayarınızda çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız, `Connect-AzAccount` [gerekli izinlere](virtual-network-network-interface.md#permissions)sahip bir hesapla Azure 'da oturum açmak için de çalıştırmanız gerekir.

Kaynak günlüğünü etkinleştirmek için mevcut bir NSG 'nin kimliğine sahip olmanız gerekir. Mevcut bir NSG yoksa, [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)ile bir tane oluşturabilirsiniz.

[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)ile için kaynak günlüğünü etkinleştirmek istediğiniz ağ güvenlik grubunu alın. Örneğin, *Myresourcegroup* adlı bir kaynak grubunda bulunan *mynsg* adlı bir NSG 'yi almak için aşağıdaki komutu girin:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Kaynak günlüklerini üç hedef türüne yazabilirsiniz. Daha fazla bilgi için bkz. [günlük hedefleri](#log-destinations). Bu makalede, Günlükler bir örnek olarak *Log Analytics* hedefine gönderilir. [Get-Azoperationalınsightsworkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace)ile mevcut bir Log Analytics çalışma alanını alın. Örneğin, *Myworkspaces* adlı bir kaynak grubunda *MyWorkspace* adlı var olan bir çalışma alanını almak için aşağıdaki komutu girin:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Mevcut bir çalışma alanınız yoksa, [New-Azoperationalınsightsworkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace)ile bir tane oluşturabilirsiniz.

Günlük kaydını etkinleştirmek için kullanabileceğiniz iki günlük kategorisi vardır. Daha fazla bilgi için bkz. [günlük kategorileri](#log-categories). [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting)ile NSG için kaynak günlüğünü etkinleştirin. Aşağıdaki örnek, daha önce aldığınız NSG ve çalışma alanının kimliklerini kullanarak hem olay hem de sayaç kategorisi verilerini bir NSG için çalışma alanına kaydeder:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Her ikisi yerine yalnızca bir kategori ya da diğeri için veri kaydetmek istiyorsanız, bu `-Categories` seçeneği önceki komuta, ardından *Networksecuritygroupevent* veya *Networksecuritygrouprulecounter*' a ekleyin. Log Analytics çalışma alanından farklı bir [hedefe](#log-destinations) oturum açmak Istiyorsanız, Azure [depolama hesabı](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) veya [Olay Hub](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)'ı için uygun parametreleri kullanın.

Günlükleri görüntüleyin ve çözümleyin. Daha fazla bilgi için bkz. [günlükleri görüntüleme ve çözümleme](#view-and-analyze-logs).

### <a name="azure-cli"></a>Azure CLI

[Azure Cloud Shell](https://shell.azure.com/bash)izleyen komutları veya BILGISAYARıNıZDAN Azure CLI 'yi çalıştırarak çalıştırabilirsiniz. Azure Cloud Shell, ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. CLı 'yı bilgisayarınızdan çalıştırırsanız, sürüm 2.0.38 veya daha yeni bir sürüme ihtiyacınız vardır. `az --version`Yüklü sürümü bulmak için bilgisayarınızda çalıştırın. Yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli). CLı 'yi yerel olarak çalıştırıyorsanız, `az login` Azure 'da [gerekli izinlere](virtual-network-network-interface.md#permissions)sahip bir hesapla oturum açmak için öğesini de çalıştırmanız gerekir.

Kaynak günlüğünü etkinleştirmek için mevcut bir NSG 'nin kimliğine sahip olmanız gerekir. Mevcut bir NSG yoksa, [az Network NSG Create](/cli/azure/network/nsg#az_network_nsg_create)komutuyla bir tane oluşturabilirsiniz.

[Az Network NSG Show](/cli/azure/network/nsg#az_network_nsg_show)ile için kaynak günlüğünü etkinleştirmek istediğiniz ağ güvenlik grubunu alın. Örneğin, *Myresourcegroup* adlı bir kaynak grubunda bulunan *mynsg* adlı bir NSG 'yi almak için aşağıdaki komutu girin:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Kaynak günlüklerini üç hedef türüne yazabilirsiniz. Daha fazla bilgi için bkz. [günlük hedefleri](#log-destinations). Bu makalede, Günlükler bir örnek olarak *Log Analytics* hedefine gönderilir. Daha fazla bilgi için bkz. [günlük kategorileri](#log-categories).

[Az Monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)komutuyla NSG için kaynak günlüğünü etkinleştirin. Aşağıdaki örnek hem olay hem de sayaç kategorisi verilerini *Myworkspaces* adlı bir kaynak grubunda bulunan *MyWorkspace* adlı mevcut bir çalışma alanına ve daha önce ALDıĞıNıZ NSG 'nin kimliğine sahip olur:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Mevcut bir çalışma alanınız yoksa [Azure Portal](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace)kullanarak bir tane oluşturabilirsiniz. Günlük kaydını etkinleştirmek için kullanabileceğiniz iki günlük kategorisi vardır.

Yalnızca bir kategori veya diğer için verileri günlüğe kaydetmek istiyorsanız, önceki komutta verileri günlüğe kaydetmek istemediğiniz kategoriyi kaldırın. Log Analytics çalışma alanından farklı bir [hedefe](#log-destinations) oturum açmak Istiyorsanız, Azure [depolama hesabı](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) veya [Olay Hub](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)'ı için uygun parametreleri kullanın.

Günlükleri görüntüleyin ve çözümleyin. Daha fazla bilgi için bkz. [günlükleri görüntüleme ve çözümleme](#view-and-analyze-logs).

## <a name="log-destinations"></a>Günlük hedefleri

Tanılama verileri şu olabilir:
- Denetim veya el ile inceleme için [bir Azure depolama hesabına yazıldı](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage). Kaynak tanılama ayarlarını kullanarak saklama süresini (gün olarak) belirtebilirsiniz.
- Üçüncü taraf bir hizmetin veya PowerBI gibi özel analiz çözümünün alımı için [bir olay hub 'ına akış](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) .
- [Azure izleyici günlüklerine yazılır](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).

## <a name="log-categories"></a>Günlük kategorileri

JSON biçimli veriler aşağıdaki günlük kategorileri için yazılır:

### <a name="event"></a>Olay

Olay günlüğü, MAC adresine bağlı olarak VM 'lere hangi NSG kurallarının uygulandığı hakkında bilgi içerir. Her olay için aşağıdaki veriler günlüğe kaydedilir. Aşağıdaki örnekte, IP adresi 192.168.1.4 ve bir MAC adresi 00-0D-3A-92-6A-7C olan bir sanal makine için veriler günlüğe kaydedilir:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Kural sayacı

Kural sayacı günlüğü, kaynaklara uygulanan her bir kuralla ilgili bilgiler içerir. Aşağıdaki örnek veriler bir kural uygulandığında günlüğe kaydedilir. Aşağıdaki örnekte, IP adresi 192.168.1.4 ve bir MAC adresi 00-0D-3A-92-6A-7C olan bir sanal makine için veriler günlüğe kaydedilir:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> İletişimin kaynak IP adresi günlüğe kaydedilmez. Bununla birlikte, tüm kural sayacı bilgilerini ve iletişim başlatan kaynak IP adresini günlüğe kaydeden bir NSG için [NSG akış günlüğü 'nü](../network-watcher/network-watcher-nsg-flow-logging-portal.md) etkinleştirebilirsiniz. NSG akış günlüğü verileri bir Azure Depolama hesabına yazılır. Azure ağ Izleyicisi 'nin [Trafik Analizi](../network-watcher/traffic-analytics.md) özelliği ile verileri çözümleyebilirsiniz.

## <a name="view-and-analyze-logs"></a>Günlükleri görüntüleme ve çözümleme

Kaynak günlüğü verilerini görüntülemeyi öğrenmek için bkz. [Azure platform günlüklerine genel bakış](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tanılama verilerini buraya gönderirseniz:
- **Azure izleyici günlükleri**: Gelişmiş Öngörüler için [ağ güvenlik grubu Analizi](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) çözümünü kullanabilirsiniz. Bu çözüm, bir sanal makinedeki ağ arabiriminin MAC adresine göre trafiğe izin veren veya reddeden NSG kuralları için görselleştirmeler sağlar.
- **Azure depolama hesabı**: veriler, dosyadaki bir PT1H.jsyazılır. Şunu bulabilirsiniz:
  - Aşağıdaki yolda olay günlüğü: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Aşağıdaki yolda kural sayacı günlüğü: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Sonraki adımlar

- [Etkinlik günlüğü](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)hakkında daha fazla bilgi edinin. Etkinlik günlüğü, Azure dağıtım modelinden oluşturulan NSG 'ler için varsayılan olarak etkindir. Etkinlik günlüğündeki NSG 'ler üzerinde hangi işlemlerin tamamlandığını öğrenmek için aşağıdaki kaynak türlerini içeren girdileri arayın:
  - Microsoft. ClassicNetwork/networkSecurityGroups
  - Microsoft. ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft. Network/networkSecurityGroups
  - Microsoft. Network/networkSecurityGroups/securityRules
- Her akışın kaynak IP adresini dahil etmek için tanılama bilgilerinin nasıl günlüğe alınacağını öğrenmek için bkz. [NSG akış günlüğü](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
