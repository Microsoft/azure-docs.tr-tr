---
title: Profil Oluşturucu & için KCG 'LERI (kendi depolama alanınızı getir) yapılandırma Snapshot Debugger
description: Profil Oluşturucu & için KCG 'LERI (kendi depolama alanınızı getir) yapılandırma Snapshot Debugger
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 01/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 9c3ff91cbfb6423099040a6ea46eeb66f5461f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589666"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>Application Insights Profiler ve Snapshot Debugger için kendi depolama alanınızı getir (BYOS) yapılandırma

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>Kendi depolama alanınızı (BYOS) getir ve neden gerekli olabilir? 
Application Insights Profiler veya Snapshot Debugger kullandığınızda, uygulamanız tarafından oluşturulan yapıtlar, genel Internet üzerinden Azure depolama hesaplarına yüklenir. Bu hesaplar, işlenmek ve analiz amacıyla Microsoft tarafından ücretli ve denetlenir. Microsoft, bu yapıtlar için REST ve ömür yönetimi ilkelerini denetler.

Kendi depolama alanınızı getir ile, bu yapılar denetlediğiniz bir depolama hesabına yüklenir. Bu, bekleyen şifreleme ilkesini, ömür yönetimi ilkesini ve ağ erişimini kontrol ettiğiniz anlamına gelir. Bununla birlikte, bu depolama hesabıyla ilişkili maliyetlerden de sorumlu olursunuz.

> [!NOTE]
> Özel bağlantıyı etkinleştirirseniz, kendi depolama alanınızı getirin. Application Insights özel bağlantısı hakkında daha fazla bilgi için [belgelerine bakın.](../logs/private-link-security.md)
>
> Customer-Managed anahtarlarını etkinleştirirseniz kendi depolama alanınızı getirin. Application Insights Customer-Managed anahtarları hakkında daha fazla bilgi için [belgelerine bakın.](../logs/customer-managed-keys.md)

## <a name="how-will-my-storage-account-be-accessed"></a>Depolama hesabımın erişim sıklığı nedir?
1. Sanal makinelerinizde veya App Service çalışan aracılar, hesabınızdaki blob kapsayıcılarına yapıtları (profiller, anlık görüntüler ve semboller) karşıya yükleyecek. Bu işlem, depolama hesabınızda yeni bir blob 'a SAS (paylaşılan erişim Imzası) belirteci almak için Application Insights Profiler veya Snapshot Debugger hizmetine başvurmayı içerir.
1. Application Insights Profiler veya Snapshot Debugger hizmeti gelen blobu analiz eder ve analiz sonuçlarını ve günlük dosyalarını blob depolamaya geri yazar. Kullanılabilir işlem kapasitesine bağlı olarak, bu işlem karşıya yüklendikten sonra herhangi bir zamanda gerçekleşebilir.
1. Profil Oluşturucu izlemelerini veya anlık görüntü hata ayıklayıcısı analizini görüntülediğinizde, hizmet, blob depolamadan analiz sonuçlarını getirir.

## <a name="prerequisites"></a>Önkoşullar
* Depolama hesabınızı Application Insights kaynağınız ile aynı konumda oluşturmaya dikkat edin. Örn. Application Insights kaynağınız Batı ABD 2, depolama hesabınızın da Batı ABD 2 olması gerekir. 
* Depolama hesabınızda Access Control (ıAM) Kullanıcı arabirimi aracılığıyla "Tanılama Hizmetleri güvenilen depolama erişimi" AAD uygulamasına "Depolama Blobu verileri katılımcısı" rolünü verin.
* Özel bağlantı etkinleştirilirse, sanal ağınızdan güvenilir Microsoft hizmetimize bağlantı sağlamak için ek ayarı yapılandırın. 

## <a name="how-to-enable-byos"></a>KCG 'yi etkinleştirme

### <a name="create-storage-account"></a>Depolama Hesabı Oluştur
Application Insights kaynağınız ile aynı konumda yer alan marka yeni bir depolama hesabı oluşturun.
Application Insights kaynağınız varsa, `West US 2` depolama hesabınız içinde olmalıdır `West US 2` .

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>Tanılama hizmetlerine depolama hesabınıza erişim izni verin
Bir KCG depolama hesabı, bir Application Insights kaynağına bağlanır. Application Insights kaynak başına yalnızca bir depolama hesabı olabilir ve her ikisi de aynı konumda olmalıdır. Birden fazla Application Insights kaynağı ile aynı depolama hesabını kullanabilirsiniz.

İlk olarak, Application Insights Profiler ve Snapshot Debugger hizmetine depolama hesabına erişim verilmesi gerekir. Erişim vermek için, `Storage Blob Data Contributor` `Diagnostic Services Trusted Storage Access` Şekil 1,0 ' de gösterildiği gibi, rolü depolama hesabınızdaki Access Control (IAM) sayfası aracılığıyla adlı AAD uygulamasına ekleyin. 

Adımlar: 
1. "Rol ataması ekleme" bölümünde "Ekle" düğmesine tıklayın 
1. "Depolama Blobu verileri katılımcısı" rolünü seçin 
1. "Erişim atama" bölümünde "Azure AD Kullanıcı, Grup veya hizmet sorumlusu" seçeneğini belirleyin 
1. Arama & "Tanılama Hizmetleri güvenilen depolama erişimi" uygulamasını seçin 
1. Değişiklikleri kaydetme

_![ Şekil 1,0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _Şekil 1,0_ 

Rolü ekledikten sonra, aşağıdaki Şekil 1,1 gibi "rol atamaları" bölümünün altında görüntülenir. 
_![ Şekil 1,1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _Şekil 1,1_ 

Ayrıca özel bağlantı kullanıyorsanız, sanal ağınızdan güvenilir Microsoft hizmetimize bağlantı sağlamak için bir ek yapılandırma gerekir. [Depolama ağı güvenliği belgelerine](../../storage/common/storage-network-security.md#trusted-microsoft-services)bakın.

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>Depolama hesabınızı Application Insights kaynağına bağlama
KCG 'yi kod düzeyi tanılama (Profiler/Debugger) için yapılandırmak için üç seçenek vardır:

* Azure PowerShell cmdlet 'Leri kullanma
* Azure komut satırı arabirimini (CLı) kullanma
* Azure Resource Manager şablonu kullanma

#### <a name="configure-using-azure-powershell-cmdlets"></a>Azure PowerShell cmdlet 'Lerini kullanarak yapılandırma

1. Az PowerShell 4.2.0 veya üstünü yüklediğinizden emin olun.

    Azure PowerShell yüklemek için [resmi Azure PowerShell belgelerine](/powershell/azure/install-az-ps)bakın.

1. Application Insights PowerShell uzantısını yükler.
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Azure hesabınızla oturum açın
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    Oturum açma hakkında daha fazla bilgi için [Connect-AzAccount belgelerine](/powershell/module/az.accounts/connect-azaccount)bakın.

1. Application Insights kaynağınız ile bağlantılı önceki depolama hesabını kaldırın.

    Kalıp
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    Örnek:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. Depolama hesabınızı Application Insights kaynağına bağlayın.
    
    Kalıp
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    Örnek:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Azure CLı kullanarak yapılandırma

1. Azure CLı 'yi yüklediğinizden emin olun.

    Azure CLı 'yı yüklemek için [resmi Azure CLI belgelerine](/cli/azure/install-azure-cli)bakın.

1. Application Insights CLı uzantısını yükler.
    ```powershell
    az extension add -n application-insights
    ```

1. Depolama hesabınızı Application Insights kaynağına bağlayın.

    Kalıp
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    Örnek:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    Beklenen çıktı:
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > Application Insights kaynağına bağlı depolama hesaplarında güncelleştirmeler gerçekleştirmek için [APPLICATION INSIGHTS CLI belgelerine](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage)bakın.

#### <a name="configure-using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak yapılandırma

1. Aşağıdaki içerikle bir Azure Resource Manager şablon dosyası oluşturun (byos.template.jsüzerinde).
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Önceki şablonu dağıtmak için aşağıdaki PowerShell komutunu çalıştırın (bağlantılı depolama hesabı oluşturun).

    Kalıp
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    Örnek:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. PowerShell konsolunda sorulduğunda aşağıdaki parametreleri sağlayın:
    
    |           Parametre           |                                Açıklama                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | KCG 'yi etkinleştirmek için Application Insights kaynağın adı.            |
    | storage_account_name          | KCG olarak kullanacağınız depolama hesabı kaynağının adı. |
    
    Beklenen çıktı:
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. Azure portal aracılığıyla ilgilendiğiniz iş yükünde kod düzeyi tanılamayı (Profiler/Debugger) etkinleştirin. (App Service > Application Insights) _![ Şekil 2,0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _Şekil 2,0_

## <a name="troubleshooting"></a>Sorun giderme
### <a name="template-schema-schema_uri-isnt-supported"></a>' {Schema_uri} ' şablon şeması desteklenmiyor.
* `$schema`Şablonun özelliğinin geçerli olduğundan emin olun. Aşağıdaki düzene uymalıdır: `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* `schema_version`Şablonun geçerli değerler içinde olduğundan emin olun: `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` .
    Hata iletisi:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>' {Location} ' konumu için kayıtlı kaynak sağlayıcısı bulunamadı.
* Kaynağının olduğundan emin olun `apiVersion` `microsoft.insights/components` `2015-05-01` .
* Kaynağının olduğundan emin olun `apiVersion` `linkedStorageAccount` `2020-03-01-preview` .
    Hata iletisi:
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>Depolama hesabı konumu AI bileşeni konumuyla eşleşmelidir.
* Application Insights kaynağının konumunun depolama hesabıyla aynı olduğundan emin olun.
    Hata iletisi:
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

Genel profil oluşturucu sorunlarını giderme için, [Profil Oluşturucu sorun giderme belgelerine](profiler-troubleshooting.md)bakın.

Genel Snapshot Debugger sorun giderme için [Snapshot Debugger sorun giderme belgelerine](snapshot-debugger-troubleshoot.md)bakın. 

## <a name="faqs"></a>SSS
* Profil Oluşturucu veya anlık görüntü etkinse ve sonra BYOS 'yi etkinleştirdiğimde, verilerim depolama Hesabma geçirilecektir mi?
    _Hayır, bu değil._

* KCG, REST ve Customer-Managed anahtarındaki şifrelemeyle birlikte çalışır mı?
    _Evet, kesin olması için, BYOS, Customer-Manager anahtarlarla profil oluşturucu/hata ayıklayıcı 'nın etkinleştirilmesi için bir önkoşuldur._

* KCG, Internet 'ten yalıtılmış bir ortamda çalışır mı?
    _Yes. Aslında, BYOS yalıtılmış ağ senaryoları için bir gereksinimdir._

* Her ikisi de Customer-Managed anahtarlar ve özel bağlantı etkinleştirildiğinde BYOS işleri çalışır mı? 
    _Evet, mümkün olabilir._

* KCG 'yi etkinleştirdiğimde, veri toplandığım verileri depolamak için tanılama hizmetleri depolama hesaplarını kullanarak geri dönebilir miyim? 
    _Evet, ancak şu anda KCG 'ınızdan veri geçişini desteklemiyoruz._

* KCG etkinleştirildikten sonra, bu, depolama ve ağ ile ilgili tüm maliyetlerden devralınacaktır. 
    _Evet_
