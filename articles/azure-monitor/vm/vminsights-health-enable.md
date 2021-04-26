---
title: VM öngörüleri Konuk durumunu etkinleştirme (Önizleme)
description: Aboneliğinizdeki VM öngörüleri Konuk sistem durumunun nasıl etkinleştirileceğini ve VM 'Lerin nasıl ekleneceğini açıklar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/05/2021
ms.custom: references_regions
ms.openlocfilehash: 6f6c67e0e9ef1dd2a4092033a9479801cd0e8e7d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449316"
---
# <a name="enable-vm-insights-guest-health-preview"></a>VM öngörüleri Konuk durumunu etkinleştirme (Önizleme)
VM öngörüleri Konuk durumu, bir sanal makinenin durumunu, düzenli aralıklarla örneklendiği bir dizi performans ölçümlerine göre tanımlanan şekilde görüntülemenize olanak sağlar. Bu makalede, aboneliğinizde bu özelliğin nasıl etkinleştirileceği ve her sanal makine için konuk izlemenin nasıl etkinleştirileceği açıklanır.

## <a name="current-limitations"></a>Geçerli sınırlamalar
VM öngörüleri Konuk durumu, genel önizlemede aşağıdaki sınırlamalara sahiptir:

- Şu anda yalnızca Azure sanal makineleri desteklenmektedir. Sunucular için Azure Arc şu anda desteklenmemektedir.
- Ağ proxy 'leri Şu anda desteklenmiyor.


## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri
Sanal makinenin aşağıdaki işletim sistemlerinden birini çalıştırması gerekir: 

  - CentOS 7,5, 7,6, 7,7, 7,8, 7,9
  - RedHat 7,5, 7,6, 7,7, 7,8, 7,9
  - Ubuntu 16,04 LTS, Ubuntu 18,04 LTS
  - Windows Server 2012 veya üzeri

## <a name="supported-regions"></a>Desteklenen bölgeler

Sanal makinenin aşağıdaki bölgelerden birinde bulunması gerekir:

- Orta Avustralya
- Doğu Avustralya
- Güneydoğu Avustralya
- Orta Kanada
- Orta Hindistan
- Central US
- Doğu Asya
- Doğu ABD
- Doğu ABD 2
- EUAP Doğu ABD 2
- Orta Fransa
- Almanya Orta Batı
- Doğu Japonya
- Güney Kore - Orta
- Orta Kuzey ABD
- Kuzey Avrupa
- Orta Güney ABD
- Güney Afrika - Kuzey
- Güneydoğu Asya
- İsviçre Kuzey
- Güney Birleşik Krallık
- Batı Birleşik Krallık
- Orta Batı ABD
- West Europe
- Batı ABD
- Batı ABD 2


Log Analytics çalışma alanı aşağıdaki bölgelerden birinde bulunmalıdır:

- Orta Avustralya
- Doğu Avustralya
- Güneydoğu Avustralya
- Orta Kanada
- Kanada Hindistan
- Central US
- Doğu Asya
- Doğu ABD
- Doğu ABD 2
- EUAP Doğu ABD 2
- Orta Fransa
- Doğu Japonya
- Orta Kuzey ABD
- Kuzey Avrupa
- Orta Güney ABD
- Güneydoğu Asya
- İsviçre Kuzey
- Güney Birleşik Krallık
- Batı Avrupa Bölgesi
- Batı ABD
- Batı ABD 2

## <a name="prerequisites"></a>Önkoşullar

- Sanal makine, VM öngörülerine eklendi olmalıdır.
- Ekleme adımlarının yürütüldüğü kullanıcının, sanal makine ve veri toplama kuralının bulunduğu aboneliğe en az katkıda bulunan düzeyinde erişimi olmalıdır.
- Gerekli Azure Kaynak sağlayıcılarının aşağıdaki bölümde açıklandığı gibi kayıtlı olması gerekir.

## <a name="register-required-azure-resource-providers"></a>Gerekli Azure Kaynak sağlayıcılarını kaydetme
VM öngörüleri Konuk sistem durumunu etkinleştirmek üzere aboneliğiniz için aşağıdaki Azure kaynak sağlayıcıları kaydedilir. 

- Microsoft. WorkloadMonitor
- Microsoft. Insights

[Azure kaynak sağlayıcıları ve türleri](../../azure-resource-manager/management/resource-providers-and-types.md)bölümünde açıklandığı gibi, bir kaynak sağlayıcısını kaydetmek için kullanılabilir yöntemlerden herhangi birini kullanabilirsiniz. Ayrıca, Azure Resource Manager kimliği doğrulanmış çağrı yapmak için armclient, Postman veya başka bir yöntem kullanarak aşağıdaki örnek komutu da kullanabilirsiniz:

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Azure portalını kullanarak sanal makineyi etkinleştirme
Sanal makine için konuk durumunu etkinleştirme amacıyla Azure portalını kullandığınızda gerekli tüm yapılandırma adımları sizin yerinize tamamlanır. Bu, veri toplama kuralının oluşturulmasını, sanal makineye konuk sistem durumu uzantısının yüklenmesini ve veri toplama kuralıyla bir ilişki oluşturmayı içerir.

VM öngörüleri içindeki **Başlarken** görünümünden, bir sanal makine için yükseltme iletisinin yanındaki bağlantıya tıklayın ve ardından **Yükselt** düğmesine tıklayın. İsterseniz birden çok sanal makineyi seçip bunları aynı anda yükseltebilirsiniz.

![Sanal makinede sağlık özelliğini etkinleştir](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Resource Manager şablonu kullanarak sanal makineyi etkinleştirme
Azure Resource Manager kullanarak sanal makinelerin etkinleştirilmesi için gereken üç adım vardır.

- Veri toplama kuralı oluştur.
- Her sanal makineye konuk sistem durumu uzantısını yükler
- Sanal makine ile veri toplama kuralı arasında bir ilişki kurun.

### <a name="create-data-collection-rule-dcr"></a>Veri toplama kuralı (DCR) oluştur

> [!NOTE]
> Bir sanal makineyi Azure portal kullanarak etkinleştirirseniz, burada açıklanan veri toplama kuralı sizin için oluşturulur. Bu durumda, bu adımı gerçekleştirmeniz gerekmez.

VM öngörüleri Konuk sistem durumu 'nda izleyicilerin yapılandırması, [veri toplama kurallarında (DCR)](../agents/data-collection-rule-overview.md)depolanır. Konuk sistem durumu uzantısına sahip her sanal makinenin bu kuralla bir ilişkilendirilmesi gerekir.

> [!NOTE]
> İzleyicilerin varsayılan yapılandırmasını, [VM öngörüleri Konuk sistem durumu 'nda Izlemeyi yapılandırma (Önizleme)](vminsights-health-configure.md)bölümünde açıklandığı gibi değiştirmek için ek veri toplama kuralları oluşturabilirsiniz.

Şablon aşağıdaki parametreler için değer gerektirir:

- **Defaulthealthdatacollectionrulename**: şablonda tanımlanmış varsayılan adı tut.
- **destinationWorkspaceResourceId**: sanal makine veri toplama için kullanılan Log Analytics çalışma ALANıNıN kaynak kimliği.
- **Datacollectionrulelocation**: veri toplama kuralının bölgesi. Bunun Log Analytics çalışma alanının bölgesiyle eşleşmesi gerekir.


[Kaynak Yöneticisi şablonlar için herhangi bir dağıtım yöntemini](../../azure-resource-manager/templates/deploy-powershell.md)kullanarak şablonu dağıtın. Aşağıdaki komutlar, PowerShell veya Azure CLı kullanarak şablon ve parametre dosyalarını dağıtır.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---

Aşağıdaki Kaynak Yöneticisi şablonunda tanımlanan veri toplama kuralı, Konuk sistem durumu uzantısına sahip sanal makinelerin tüm izleyicilerini sunar. İzleyiciler tarafından kullanılan performans sayaçlarının her biri için veri kaynakları içermelidir.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>Örnek parametre dosyası

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "destinationWorkspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



### <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>Konuk durum uzantısını yükler ve veri toplama kuralıyla ilişkilendir
Bir sanal makineyi Konuk sistem durumu için etkinleştirmek üzere aşağıdaki Kaynak Yöneticisi şablonunu kullanın. Bu, Konuk sistem durumu uzantısını yükleyerek, veri toplama kuralıyla ilişki oluşturur. Bu şablonu, [Kaynak Yöneticisi şablonları için herhangi bir dağıtım yöntemi](../../azure-resource-manager/templates/deploy-powershell.md)kullanarak dağıtabilirsiniz.


Örneğin, PowerShell veya Azure CLı kullanarak bir kaynak grubuna şablon ve parametre dosyasını dağıtmak için aşağıdaki komutları kullanın.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>Şablon dosyası

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>Örnek parametre dosyası

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [VM öngörüleri tarafından etkinleştirilen izleyicileri özelleştirme](vminsights-health-configure.md)
