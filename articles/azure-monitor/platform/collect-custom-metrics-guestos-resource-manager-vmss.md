---
title: Windows sanal makine ölçek kümesi için Azure Resource Manager şablonu kullanarak Azure Izleyici ölçüm deposuna Konuk işletim sistemi ölçümleri gönderme
description: Windows sanal makine ölçek kümesi için Kaynak Yöneticisi şablonu kullanarak Azure Izleyici ölçüm deposuna Konuk işletim sistemi ölçümleri gönderme
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 8ee8c0c9d9724706f9b46013eba14e878832fd02
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844978"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Windows sanal makine ölçek kümesi için Azure Resource Manager şablonu kullanarak Azure Izleyici ölçüm deposuna Konuk işletim sistemi ölçümleri gönderme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor [Windows Azure tanılama (WAD) uzantısını](diagnostics-extension-overview.md)kullanarak, sanal makine, bulut hizmeti veya Azure Service Fabric kümesinin bir parçası olarak çalışan konuk işletim sisteminden (konuk işletim sistemi) ölçümleri ve günlükleri toplayabilirsiniz. Uzantı, daha önce bağlantılı makalede listelenen birçok farklı konuma telemetri gönderebilir.  

Bu makalede, Azure Izleyici veri deposuna bir Windows sanal makine ölçek kümesi için konuk işletim sistemi performans ölçümlerini gönderme işlemi açıklanır. Windows Azure Tanılama sürüm 1,11 ' den başlayarak, ölçümleri doğrudan standart platform ölçümleri toplanmış Azure Izleyici ölçümleri deposuna yazabilirsiniz. Bu konumda depolayarak, platform ölçümleri için kullanılabilir olan eylemlere erişebilirsiniz. Eylemler, neredeyse gerçek zamanlı uyarı, grafik, yönlendirme, REST API erişimi ve daha fazlasını içerir. Geçmişte Windows Azure Tanılama uzantısı Azure depolama 'ya yazdı, ancak Azure Izleyici veri deposuna değil.  

Kaynak Yöneticisi şablonlarına yeni başladıysanız, [şablon dağıtımları](../../azure-resource-manager/resource-group-overview.md) ve bunların yapısı ve sözdizimi hakkında bilgi edinin.  

## <a name="prerequisites"></a>Önkoşullar

- Aboneliğinizin [Microsoft. Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)'a kayıtlı olması gerekir. 

- [Azure PowerShell](/powershell/azure) yüklemiş olmanız veya [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)kullanmanız gerekir. 

- VM kaynağınızın [özel ölçümleri destekleyen bir bölgede](metrics-custom-overview.md#supported-regions)olması gerekir.

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Izleyiciyi bir veri havuzu olarak ayarlama 
Azure Tanılama uzantısı, ölçümleri ve günlükleri farklı konumlara yönlendirmek için **veri havuzları** adlı bir özellik kullanır. Aşağıdaki adımlarda, yeni Azure Izleyici veri havuzunu kullanarak bir VM dağıtmak için Kaynak Yöneticisi şablonu ve PowerShell 'in nasıl kullanılacağı gösterilmektedir. 

## <a name="author-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu yazma 
Bu örnekte, herkese açık bir [örnek şablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)kullanabilirsiniz:  

- **Azuredeploy. JSON** , bir sanal makine ölçek kümesinin dağıtılması için önceden yapılandırılmış bir kaynak yöneticisi şablonudur.

- **Azuredeploy. Parameters. JSON** , VM 'niz için ayarlamak istediğiniz Kullanıcı adı ve parola gibi bilgileri depolayan bir parametre dosyasıdır. Dağıtım sırasında Kaynak Yöneticisi şablonu bu dosyada ayarlanan parametreleri kullanır. 

Her iki dosyayı da yerel olarak indirin ve kaydedin. 

###  <a name="modify-azuredeployparametersjson"></a>Azuredeploy. Parameters. JSON öğesini Değiştir
**Azuredeploy. Parameters. JSON** dosyasını açın:  
 
- Dağıtmak istediğiniz bir **Vmsku** sağlayın. Standard_D2_v3 öneririz. 
- Sanal makine ölçek kümesi için istediğiniz bir **Windowsosversion** belirtin. 2016-Datacenter önerilir. 
- Sanal makine ölçek kümesi kaynağını bir **Vmssname** özelliği kullanılarak dağıtılacak şekilde adlandırın. Bir örnek, **VMSS-WAD-test**şeklindedir.    
- **InstanceCount** özelliğini kullanarak sanal makine ölçek kümesi üzerinde çalıştırmak istediğiniz VM sayısını belirtin.
- **AdminUserName** ve sanal makine ölçek kümesi için **adminPassword** değerlerini girin. Bu parametreler, ölçek kümesindeki VM 'lere uzaktan erişim için kullanılır. VM 'nizin ele geçirilmesini önlemek için bu şablondaki **olanları kullanmayın.** Botlar, genel GitHub depolarındaki Kullanıcı adları ve parolalar için interneti tarar. Bu varsayılanlar ile VM 'Leri test etmeleri olasıdır. 


###  <a name="modify-azuredeployjson"></a>Azuredeploy. JSON öğesini değiştirme
**Azuredeploy. JSON** dosyasını açın. 

Kaynak Yöneticisi şablonundaki depolama hesabı bilgilerini tutmak için bir değişken ekleyin. Tanılama yapılandırma dosyasında belirtilen tüm Günlükler veya performans sayaçları hem Azure Izleyici ölçüm deposuna hem de burada belirttiğiniz depolama hesabına yazılır: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Kaynaklar bölümünde sanal makine ölçek kümesi tanımını bulun ve **kimlik** bölümünü yapılandırmaya ekleyin. Bu ek, Azure 'un bir sistem kimliği atamasını sağlar. Bu adım Ayrıca ölçek kümesindeki VM 'Lerin Azure Izleyici 'ye yönelik Konuk ölçümleri yaymasını sağlar:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

Sanal makine ölçek kümesi kaynağında **Virtualmachineprofile** bölümünü bulun. Uzantıları yönetmek için **Extensionsprofile** adlı yeni bir profil ekleyin.  


**Extensionprofile**öğesinde, **VMSS-wad-Extension** bölümünde gösterildiği gibi şablona yeni bir uzantı ekleyin.  Bu bölüm, yayınlanan ölçümlerin Azure Izleyici tarafından kabul edilmesini sağlayan Azure kaynakları uzantısı için yönetilen kimliklerdir. **Ad** alanı, herhangi bir ad içerebilir. 

MSI uzantısından aşağıdaki kod ayrıca, tanılama uzantısını ve yapılandırmasını, sanal makine ölçek kümesi kaynağına uzantı kaynağı olarak ekler. Gerektiğinde performans sayaçlarını eklemek veya kaldırmak için yeterli fikir vardır: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Doğru sırada oluşturulduğundan emin olmak için depolama hesabı için bir **Bağımlıdson** ekleyin: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Şablonda bir depolama hesabı zaten oluşturulmadıysa, bir depolama hesabı oluşturun: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Her iki dosyayı da kaydedin ve kapatın. 

## <a name="deploy-the-resource-manager-template"></a>Kaynak Yöneticisi şablonu dağıtma 

> [!NOTE]  
> Azure Tanılama uzantısı sürüm 1,5 veya üstünü çalıştırıyor olmanız ve Kaynak Yöneticisi **şablonunuzda,** **'** **true** ' olarak ayarlanmış bir. Ardından Azure, VM 'yi başlattığında uygun uzantıyı yükler. Şablonunuzda bu ayarlara sahip değilseniz, onları değiştirin ve şablonu yeniden dağıtın. 


Kaynak Yöneticisi şablonunu dağıtmak için Azure PowerShell kullanın:  

1. PowerShell 'i başlatın. 
1. Kullanarak `Login-AzAccount`Azure 'da oturum açın.
1. Kullanarak `Get-AzSubscription`abonelik listenizi alın.
1. Oluşturacağınız aboneliği ayarlayın veya sanal makineyi güncelleştirin: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Dağıtılmakta olan VM için yeni bir kaynak grubu oluşturun. Şu komutu çalıştırın: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Özel ölçümler için etkinleştirilen bir Azure bölgesi kullanmayı unutmayın. [Özel ölçümler için etkinleştirilen bir Azure bölgesi](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions)kullanmayı unutmayın.
 
1. VM 'yi dağıtmak için aşağıdaki komutları çalıştırın:  

   > [!NOTE]  
   > Mevcut bir ölçek kümesini güncelleştirmek istiyorsanız, komutun sonuna artımlı ekleme **modu** . 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Dağıtımınız başarılı olduktan sonra, Azure portal sanal makine ölçek kümesini bulmanız gerekir. Ölçümleri Azure Izleyici 'ye yaymalıdır. 

   > [!NOTE]  
   > Seçili **Vmskusize**etrafında hatalarla karşılaşabilirsiniz. Bu durumda, **azuredeploy. JSON** dosyanıza dönün ve **Vmskusize** parametresinin varsayılan değerini güncelleştirin. **Standard_DS1_v2**denemenizi öneririz. 


## <a name="chart-your-metrics"></a>Ölçümlerinizi grafik yapın 

1. Azure Portal’da oturum açın. 

1. Sol taraftaki menüde **izleyici**' yi seçin. 

1. **İzleyici** sayfasında **ölçümler**' i seçin. 

   ![İzleme-ölçümler sayfası](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Toplama süresini **son 30 dakika**olarak değiştirin.  

1. Kaynak açılan menüsünde, oluşturduğunuz sanal makine ölçek kümesini seçin.  

1. Ad alanları açılan menüsünde **Azure. VM. Windows. Guest**' yi seçin. 

1. Ölçümler açılan menüsünde, **Kullanımdaki bellek\%kaydedilmiş bayt**' ı seçin.  

Ayrıca, bu ölçümdeki boyutları belirli bir VM için grafik olarak veya ölçek kümesindeki her bir VM 'yi çizmek için de kullanabilirsiniz. 



## <a name="next-steps"></a>Sonraki adımlar
- [Özel ölçümler](metrics-custom-overview.md)hakkında daha fazla bilgi edinin.


