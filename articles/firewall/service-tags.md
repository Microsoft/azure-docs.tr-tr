---
title: Azure Güvenlik Duvarı hizmet etiketlerine genel bakış
description: Hizmet etiketi, güvenlik kuralı oluşturma sırasındaki karmaşıklığı en aza indirmeye yardımcı olmak için bir IP adresi ön eki grubunu temsil eder.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.openlocfilehash: 3cc1e85a18eab1adb0a1dd8307a074cb43ba0c70
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529556"
---
# <a name="azure-firewall-service-tags"></a>Azure Güvenlik Duvarı hizmeti etiketleri

Hizmet etiketi, güvenlik kuralı oluşturma sırasındaki karmaşıklığı en aza indirmeye yardımcı olmak için bir IP adresi ön eki grubunu temsil eder. Kendi hizmet etiketinizi oluşturamaz veya bir etiket içinde yer alacak IP adreslerini belirleyemezsiniz. Hizmet etiketine dahil olan adres ön ekleri Microsoft tarafından yönetilir ve hizmet etiketi adresler değiştikçe otomatik olarak güncelleştirilir.

Azure Güvenlik Duvarı hizmet etiketleri ağ kuralları hedef alanında kullanılabilir. Bunları belirli IP adresleri yerine kullanabilirsiniz.

## <a name="supported-service-tags"></a>Desteklenen hizmet etiketleri

Azure Güvenlik Duvarı ağ kurallarında kullanılabilecek hizmet etiketlerinin listesi için bkz. [sanal ağ hizmeti etiketleri](../virtual-network/service-tags-overview.md#available-service-tags) .

## <a name="configuration"></a>Yapılandırma

Azure Güvenlik Duvarı; PowerShell, Azure CLı veya Azure portal aracılığıyla hizmet etiketlerinin yapılandırılmasını destekler.

### <a name="configure-via-azure-powershell"></a>Azure PowerShell aracılığıyla yapılandırma

Bu örnekte, önce önceden oluşturulmuş Azure Güvenlik Duvarı örneğimizin bağlamını alması gerekir.

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

Sonra yeni bir kural oluşturacağız.  Kaynak veya hedef için, bu makalede daha önce bahsedildiği gibi, yararlanmak istediğiniz hizmet etiketinin metin değerini belirtebilirsiniz.

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

Daha sonra, Azure Güvenlik Duvarı tanımımızı içeren değişkeni oluşturduğumuz yeni ağ kurallarından güncelleştirmemiz gerekir.

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

Son olarak, ağ kuralı değişikliklerini çalışan Azure Güvenlik Duvarı örneğine yürütmemiz gerekir.
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik duvarı kuralları hakkında daha fazla bilgi edinmek için bkz. [Azure Güvenlik Duvarı kural işleme mantığı](rule-processing.md).
