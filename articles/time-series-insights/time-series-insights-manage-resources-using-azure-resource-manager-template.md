---
title: Azure Resource Manager şablonlarını kullanarak ortamınızı yönetin-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights ortamınızı Azure Resource Manager kullanarak programlı bir şekilde yönetmeyi öğrenin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: acafb9aab756507bb073b3e883ee190c72b4f9f8
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006763"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanarak Time Series Insights kaynakları oluşturma

Bu makalede [Azure Resource Manager şablonları](https://docs.microsoft.com/azure/azure-resource-manager/), PowerShell ve Time Series Insights kaynak sağlayıcısı kullanılarak Time Series Insights kaynaklarının nasıl oluşturulacağı ve dağıtılacağı açıklanır.

Time Series Insights aşağıdaki kaynakları destekler:

   | Kaynak | Açıklama |
   | --- | --- |
   | Ortam | Time Series Insights ortamı, olay aracılarından okunan, depolanan ve sorgu için kullanılabilir hale getirilen olayların mantıksal gruplandırmasıdır. Daha fazla bilgi için bkz. [Azure Time Series Insights ortamınızı planlayın](time-series-insights-environment-planning.md) |
   | Olay kaynağı | Olay kaynağı, Time Series Insights tarafından ortama okuma ve olayları geri almak için gereken bir olay aracısına bağlantıdır. Şu anda desteklenen olay kaynakları IoT Hub ve Olay Hub 'ı. |
   | Başvuru veri kümesi | Başvuru veri kümeleri, ortamdaki olaylar hakkında meta veriler sağlar. Başvuru verileri kümelerindeki meta veriler, giriş sırasında olaylarla birleştirilir. Başvuru veri kümeleri, olay anahtarı özelliklerine göre kaynak olarak tanımlanır. Başvuru veri kümesini oluşturan gerçek meta veriler, veri düzlemi API 'Leri aracılığıyla karşıya yüklenir veya değiştirilir. |
   | Erişim Ilkesi | Erişim ilkeleri, veri sorguları verme, ortamdaki başvuru verilerini işleme ve ortamla ilişkili Kaydedilmiş sorguları ve perspektifleri paylaşma izinleri verir. Daha fazla bilgi için [Azure Portal kullanarak Time Series Insights ortamına veri erişimi verme](time-series-insights-data-access.md) konusunu okuyun |

Kaynak Yöneticisi şablonu, bir kaynak grubundaki kaynakların altyapısını ve yapılandırmasını tanımlayan bir JSON dosyasıdır. Aşağıdaki belgeler şablon dosyalarını daha ayrıntılı bir şekilde anlatmaktadır:

- [Azure Resource Manager şablonu dağıtımı](../azure-resource-manager/template-deployment-overview.md)
- [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../azure-resource-manager/resource-group-template-deploy.md)
- [Microsoft. Timeseriesınsights kaynak türleri](/azure/templates/microsoft.timeseriesinsights/allversions)

[201-timeseriesınsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) hızlı başlangıç şablonu GitHub 'da yayımlanır. Bu şablon bir Time Series Insights ortamı, bir olay hub 'ından olayları kullanmak üzere yapılandırılmış bir alt olay kaynağı ve ortam verilerine erişim izni veren ilkelere erişim ilkeleri oluşturur. Mevcut bir olay hub 'ı belirtilmemişse, dağıtım ile bir tane oluşturulur.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Dağıtım şablonu ve parametrelerini belirtin

Aşağıdaki yordamda, PowerShell kullanarak bir Time Series Insights ortamı oluşturan bir Azure Resource Manager şablonu, bir olay hub 'ından olayları tüketmek üzere yapılandırılmış bir alt olay kaynağı ve erişim izni veren ortamın verileri. Mevcut bir olay hub 'ı belirtilmemişse, dağıtım ile bir tane oluşturulur.

1. [Azure PowerShell kullanmaya](https://docs.microsoft.com/powershell/azure/get-started-azureps)başlama yönergelerini izleyerek Azure PowerShell ' i yükler.

1. GitHub 'dan [201-timeseriesınsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) şablonunu kopyalayın veya kopyalayın.

   * Parametre dosyası oluşturma

     Bir parametre dosyası oluşturmak için [201-timeseriesınsights-Environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) dosyasını kopyalayın.

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Gerekli parametreler

     | Parametre | Açıklama |
     | --- | --- |
     | eventHubNamespaceName | Kaynak olay hub 'ının ad alanı. |
     | eventHubName | Kaynak olay hub 'ının adı. |
     | consumerGroupName | Time Series Insights hizmetinin Olay Hub 'ından verileri okumak için kullanacağı tüketici grubunun adı. **Note:** Kaynak çekişmesini önlemek için, bu tüketici grubu Time Series Insights hizmetine ayrılmalıdır ve diğer okuyucular ile paylaşılmaz. |
     | environmentName | Ortamın adı. Ad: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`ve herhangi bir denetim karakteri içeremez. Diğer tüm karakterlere izin verilir.|
     | eventSourceName | Olay kaynağı alt kaynağının adı. Ad: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`ve herhangi bir denetim karakteri içeremez. Diğer tüm karakterlere izin verilir. |

    <div id="optional-parameters"></div>

   * İsteğe bağlı parametreler

     | Parametre | Açıklama |
     | --- | --- |
     | Mevcut ınmageventhubresourceıd | Olay kaynağı aracılığıyla Time Series Insights ortamına bağlanacak mevcut bir olay hub 'ının isteğe bağlı kaynak KIMLIĞI. **Note:** Şablonu dağıtmanın Kullanıcı olay hub 'ında ListKeys 'i Al işlemini gerçekleştirme ayrıcalıklarına sahip olmalıdır. Hiçbir değer geçirilmemişse, şablon tarafından yeni bir olay hub 'ı oluşturulur. |
     | environmentDisplayName | Ortam adı yerine araçları veya kullanıcı arabirimlerini göstermek için isteğe bağlı bir kolay ad. |
     | environmentSkuName | Sku'nun adı. Daha fazla bilgi için [Time Series Insights fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/time-series-insights/)bakın.  |
     | Environmentskukapasitesi | SKU 'nun birim kapasitesi. Daha fazla bilgi için [Time Series Insights fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/time-series-insights/)bakın.|
     | environmentDataRetentionTime | Ortamın olayları sorgu için kullanılabilecek en az TimeSpan değeri. Değerin ISO 8601 biçiminde belirtilmesi gerekir, örneğin 30 günlük bir bekletme ilkesi için `P30D`. |
     | eventSourceDisplayName | Araç veya olay kaynağı adı yerine kullanıcı arabirimlerinde göstermek için isteğe bağlı bir kolay ad. |
     | eventSourceTimestampPropertyName | Olay kaynağının zaman damgası olarak kullanılacak olay özelliği. TimestampPropertyName için bir değer belirtilmemişse veya null ya da boş dize belirtilmişse, olay oluşturma saati kullanılacaktır. |
     | eventSourceKeyName | Time Series Insights hizmetinin Olay Hub 'ına bağlanmak için kullanacağı paylaşılan erişim anahtarının adı. |
     | Accesspolicyreaderobjectıds | Azure AD 'de, ortama okuyucu erişimi olması gereken kullanıcıların veya uygulamaların nesne kimliklerinin listesi. Hizmet sorumlusu objectID, **Get-AzADUser** veya **Get-AzADServicePrincipal** cmdlet 'leri çağırarak elde edilebilir. Azure AD grupları için bir erişim ilkesi oluşturma henüz desteklenmiyor. |
     | accessPolicyContributorObjectIds | Azure AD 'deki kullanıcıların veya uygulamaların, ortama katkıda bulunan erişimine sahip olması gereken nesne kimliklerinin listesi. Hizmet sorumlusu objectID, **Get-AzADUser** veya **Get-AzADServicePrincipal** cmdlet 'leri çağırarak elde edilebilir. Azure AD grupları için bir erişim ilkesi oluşturma henüz desteklenmiyor. |

   * Örnek olarak, aşağıdaki parametreler dosyası, mevcut bir olay hub 'ından olayları okuyan bir ortam ve olay kaynağı oluşturmak için kullanılır. Ayrıca, ortama katkıda bulunan erişimi veren iki erişim ilkesi de oluşturur.

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }    
         }
     }
     ```
  
    * Daha fazla bilgi için bkz. [Parametreler](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) makalesi.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>PowerShell kullanarak hızlı başlangıç şablonunu yerel olarak dağıtma

> [!IMPORTANT]
> Aşağıda gösterildiği komut satırı işlemleri [az PowerShell modülünü](https://docs.microsoft.com/powershell/azure/overview)anlatmaktadır.

1. PowerShell 'de Azure hesabınızda oturum açın.

    * Bir PowerShell isteminden aşağıdaki komutu çalıştırın:

      ```powershell
      Connect-AzAccount
      ```

    * Azure hesabınızda oturum açmanız istenir. Oturum açtıktan sonra, kullanılabilir aboneliklerinizi görüntülemek için aşağıdaki komutu çalıştırın:

      ```powershell
      Get-AzSubscription
      ```

    * Bu komut, kullanılabilir Azure aboneliklerinin bir listesini döndürür. Aşağıdaki komutu çalıştırarak geçerli oturum için bir abonelik seçin. `<YourSubscriptionId>`, kullanmak istediğiniz Azure aboneliğinin GUID 'SI ile değiştirin:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Mevcut değilse yeni bir kaynak grubu oluşturun.

   * Mevcut bir kaynak grubunuz yoksa, **New-AzResourceGroup** komutuyla yeni bir kaynak grubu oluşturun. Kaynak grubunun ve kullanmak istediğiniz konumun adını belirtin. Örneğin:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * Başarılı olursa, yeni kaynak grubunun bir özeti görüntülenir.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Dağıtımı test edin.

   * `Test-AzResourceGroupDeployment` cmdlet 'ini çalıştırarak dağıtımınızı doğrulayın. Dağıtımı sınarken, dağıtımı yürütürken yaptığınız gibi parametreleri tam olarak sağlayın.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Dağıtımı oluşturma

    * Yeni dağıtımı oluşturmak için `New-AzResourceGroupDeployment` cmdlet 'ini çalıştırın ve istendiğinde gerekli parametreleri sağlayın. Parametreler dağıtımınız için bir ad, kaynak grubunuzun adı ve şablon dosyasının yolunu veya URL 'sini içerir. **Mode** parametresi belirtilmemişse, varsayılan **artımlı** değeri kullanılır. Daha fazla bilgi için bkz. [artımlı ve tamamlanmış dağıtımlar](../azure-resource-manager/deployment-modes.md).

    * Aşağıdaki komut, PowerShell penceresinde gereken beş parametreyi ister:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
      ```

    * Bunun yerine bir parametre dosyası belirtmek için aşağıdaki komutu kullanın:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * Ayrıca, dağıtım cmdlet 'ini çalıştırdığınızda satır içi parametreleri de kullanabilirsiniz. Komut aşağıdaki gibidir:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * [Tüm](../azure-resource-manager/deployment-modes.md) bir dağıtımı çalıştırmak Için, **Mode** parametresini **Tamam**olarak ayarlayın:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Dağıtımı doğrulama

    * Kaynaklar başarıyla dağıtılırsa, PowerShell penceresinde dağıtımın bir özeti görüntülenir:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Hızlı başlangıç şablonunu Azure portal aracılığıyla dağıtma

   * GitHub 'daki hızlı başlangıç şablonunun ana sayfasında ayrıca bir **Azure dağıtımı** düğmesi de bulunur. Tıklandığında, Azure portal özel bir dağıtım sayfası açılır. Bu sayfadan, [gerekli parametrelerden](#required-parameters) veya [isteğe bağlı parametre](#optional-parameters) tablolarından her bir parametre için değer girebilir veya seçebilirsiniz. Ayarları doldurduktan sonra, **satın al** düğmesine tıkladığınızda şablon dağıtımı başlatılır.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Sonraki adımlar

- REST API 'Leri kullanarak Time Series Insights kaynaklarını programlı bir şekilde yönetme hakkında bilgi için bkz. [Time Series Insights Management](https://docs.microsoft.com/rest/api/time-series-insights-management/).
