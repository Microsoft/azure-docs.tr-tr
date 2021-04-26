---
title: Azure Resource Manager şablonu kullanarak VM uzantısını etkinleştirme
description: Bu makalede, karma bulut ortamlarında çalışan Azure Arc etkin sunucularına bir Azure Resource Manager şablonu kullanılarak sanal makine uzantılarının nasıl dağıtılacağı açıklanır.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d32be184a7e5bb713aee83cd3023f271299d3872
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832869"
---
# <a name="enable-azure-vm-extensions-by-using-arm-template"></a>ARM şablonunu kullanarak Azure VM uzantılarını etkinleştirme

Bu makalede, Azure Arc özellikli sunucular tarafından desteklenen Azure VM uzantıları 'nı dağıtmak için bir Azure Resource Manager şablonu (ARM şablonu) nasıl kullanacağınız gösterilmektedir.

VM uzantıları, bir Azure Resource Manager şablonuna eklenebilir ve şablonun dağıtımıyla birlikte yürütülür. Yay etkin sunucular tarafından desteklenen VM uzantıları ile, desteklenen VM uzantısını Azure PowerShell kullanarak Linux veya Windows makinelerde dağıtabilirsiniz. Aşağıdaki her örnek, şablona sağlanacak örnek değerleri içeren bir şablon dosyası ve bir parametre dosyası içerir.

>[!NOTE]
>Birden çok uzantı birlikte toplanmış ve işlenebilir olsa da, bunlar hizmet temelli olarak yüklenir. İlk uzantı yüklemesi tamamlandıktan sonra, sonraki uzantının yüklenmesi denenir.

> [!NOTE]
> Azure yay özellikli sunucular, VM uzantılarının Azure sanal makinelerine dağıtılmasını ve yönetilmesini desteklemez. Azure VM 'Leri için aşağıdaki [VM uzantısına genel bakış](../../virtual-machines/extensions/overview.md) makalesine bakın.

## <a name="deploy-the-log-analytics-vm-extension"></a>Log Analytics VM uzantısını dağıtma

Log Analytics aracısını kolayca dağıtmak için, aracıyı Windows veya Linux 'a yüklemek üzere aşağıdaki örnek verilmiştir.

### <a name="template-file-for-linux"></a>Linux için şablon dosyası

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>Windows için şablon dosyası

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parametre dosyası

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Şablon ve parametre dosyalarını diske kaydedin ve parametre dosyasını dağıtımınız için uygun değerlerle düzenleyin. Daha sonra uzantıyı aşağıdaki komutla bir kaynak grubu içindeki tüm bağlı makinelere yükleyebilirsiniz. Komut, parametreleri ve parametre değerlerini içeren bir dosyayı belirtmek için Template ve *Templateparameterfile* parametresini belirtmek üzere *TemplateFile* parametresini kullanır.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgent.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentParms.json"
```

## <a name="deploy-the-custom-script-extension"></a>Özel Betik uzantısını dağıtma

Özel Betik uzantısını kullanmak için aşağıdaki örnek Windows ve Linux 'ta çalışacak şekilde sunulmaktadır. Özel Betik uzantısı hakkında bilginiz yoksa, bkz. [Windows Için özel Betik uzantısı](../../virtual-machines/extensions/custom-script-windows.md) veya [Linux için özel Betik uzantısı](../../virtual-machines/extensions/custom-script-linux.md). Bu uzantıyı karma makinelerle kullanırken anlamanız gereken farklı özellikler vardır:

* Azure sanal makine özel Betik uzantısı ile desteklenen işletim sistemlerinin listesi, Azure Arc özellikli sunucular için geçerli değildir. Yay etkin sunucular için desteklenen OSs listesi [burada](agent-overview.md#supported-operating-systems)bulunabilir.

* Azure sanal makine ölçek kümeleri veya klasik VM 'Lerle ilgili yapılandırma ayrıntıları geçerli değildir.

* Makinelerinizin dışarıdan bir betiği indirmesi ve yalnızca bir ara sunucu üzerinden iletişim kurabilmesi gerekiyorsa, proxy sunucusu çevresel değişkenini ayarlamak için [bağlı makine aracısını yapılandırmanız](manage-agent.md#update-or-remove-proxy-settings) gerekir.

Özel Betik uzantısı yapılandırması, betik konumu ve çalıştırılacak komut gibi şeyleri belirtir. Bu yapılandırma, hem Linux hem de Windows karma makineleri için aşağıda sağlanmış olan bir Azure Resource Manager şablonunda belirtilmiştir.

### <a name="template-file-for-linux"></a>Linux için şablon dosyası

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

### <a name="template-file-for-windows"></a>Windows için şablon dosyası

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parametre dosyası

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

## <a name="deploy-the-dependency-agent-extension"></a>Bağımlılık Aracısı uzantısını dağıtma

Azure Izleyici bağımlılık Aracısı uzantısını kullanmak için, Windows ve Linux 'ta çalışmak üzere aşağıdaki örnek verilmiştir. Bağımlılık aracısına alışkın değilseniz bkz. [Azure izleyici aracılarına genel bakış](../../azure-monitor/agents/agents-overview.md#dependency-agent).

### <a name="template-file-for-linux"></a>Linux için şablon dosyası

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-file-for-windows"></a>Windows için şablon dosyası

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-deployment"></a>Şablon dağıtımı

Şablon dosyasını diske kaydedin. Ardından, uzantıyı bağlı makineye aşağıdaki komutla dağıtabilirsiniz.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\DependencyAgent.json"
```

## <a name="deploy-azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM uzantısını dağıtma (Önizleme)

Aşağıdaki JSON Key Vault VM uzantısının (Önizleme) şemasını gösterir. Uzantı, korumalı ayarlar gerektirmez. tüm ayarları ortak bilgiler olarak kabul edilir. Uzantı, izlenen sertifikaların, yoklama sıklığının ve hedef sertifika deposunun bir listesini gerektirir. Özellikle:

### <a name="template-file-for-linux"></a>Linux için şablon dosyası

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "autoUpgradeMinorVersion":{
            "type": "bool"
        },
        "pollingIntervalInS":{
          "type": "int"
        },
        "certificateStoreName":{
          "type": "string"
        },
        "certificateStoreLocation":{
          "type": "string"
        },
        "observedCertificates":{
          "type": "string"
        },
        "msiEndpoint":{
          "type": "string"
        },
        "msiClientId":{
          "type": "string"
        }
},
"resources": [
   {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/KVVMExtensionForLinux')]",
      "apiVersion": "2019-12-12",
      "location": "[parameters('location')]",
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ignored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
          },
          "authenticationSettings": {
                "msiEndpoint":  <MSI endpoint e.g.: "http://localhost:40342/metadata/identity">,
                "msiClientId":  <MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
    }
  }
 ]
}
```

### <a name="template-file-for-windows"></a>Windows için şablon dosyası

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "autoUpgradeMinorVersion":{
            "type": "bool"
        },
        "pollingIntervalInS":{
          "type": "int"
        },
        "certificateStoreName":{
          "type": "string"
        },
        "linkOnRenewal":{
          "type": "bool"
        },
        "certificateStoreLocation":{
          "type": "string"
        },
        "requireInitialSync":{
          "type": "bool"
        },
        "observedCertificates":{
          "type": "string"
        },
        "msiEndpoint":{
          "type": "string"
        },
        "msiClientId":{
          "type": "string"
        }
},
"resources": [
   {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/KVVMExtensionForWindows')]",
      "apiVersion": "2019-12-12",
      "location": "[parameters('location')]",
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": "3600",
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net"
        },
        "authenticationSettings": {
                "msiEndpoint": <MSI endpoint e.g.: "http://localhost:40342/metadata/identity">,
                "msiClientId": <MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
    }
  }
 ]
}
```

> [!NOTE]
> Gözlemlenen sertifikalarınızın URL 'Leri form olmalıdır `https://myVaultName.vault.azure.net/secrets/myCertName` .
>
> Bunun nedeni, `/secrets` yol değil, özel anahtar dahil olmak üzere tam sertifikayı döndürmektedir `/certificates` . Sertifikalar hakkında daha fazla bilgi için şurada bulunabilir: [Key Vault sertifikaları](../../key-vault/general/about-keys-secrets-certificates.md)

### <a name="template-deployment"></a>Şablon dağıtımı

Şablon dosyasını diske kaydedin. Ardından, uzantıyı bağlı makineye aşağıdaki komutla dağıtabilirsiniz.

> [!NOTE]
> VM uzantısı, Anahtar Kasası kimlik doğrulaması için bir sistem tarafından atanan kimliğin atanmasını gerektirir. Bkz. Windows ve Linux yay özellikli sunucular için [yönetilen kimlik kullanarak Key Vault için kimlik doğrulaması](managed-identity-authentication.md) yapma.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\KeyVaultExtension.json"
```

## <a name="deploy-the-azure-defender-integrated-scanner"></a>Azure Defender tümleşik tarayıcısını dağıtma

Azure Defender tümleşik tarayıcı uzantısını kullanmak için, Windows ve Linux 'ta çalışmak üzere aşağıdaki örnek verilmiştir. Tümleşik tarayıcı hakkında bilginiz varsa bkz. Azure Defender 'ın karma makineler için [güvenlik açığı değerlendirmesi çözümüne genel bakış](../../security-center/deploy-vulnerability-assessment-vm.md) .

### <a name="template-file-for-windows"></a>Windows için şablon dosyası

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/WindowsAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-file-for-linux"></a>Linux için şablon dosyası

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/LinuxAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-deployment"></a>Şablon dağıtımı

Şablon dosyasını diske kaydedin. Ardından, uzantıyı bağlı makineye aşağıdaki komutla dağıtabilirsiniz.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\AzureDefenderScanner.json"
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure PowerShell](manage-vm-extensions-powershell.md), [Azure Portal](manage-vm-extensions-portal.md)veya [Azure CLI](manage-vm-extensions-cli.md)aracılığıyla VM uzantılarını dağıtabilir, yönetebilir ve kaldırabilirsiniz.

* Sorun giderme bilgileri, [VM genişletmeleri sorunlarını gider kılavuzunda](troubleshoot-vm-extensions.md)bulunabilir.
