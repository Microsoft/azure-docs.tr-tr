---
title: Azure Cloud Services için CSCFG/CSDEF üzerinde SKU bilgilerini geçersiz kıl (genişletilmiş destek)
description: Azure Cloud Services için CSCFG/CSDEF üzerinde SKU bilgilerini geçersiz kıl (genişletilmiş destek)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: d5dfae4b5cfee8f61e11e418a05e86017d119410
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739269"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Cloud Services 'de CSCFG/CSDEF üzerinde SKU bilgilerini geçersiz kıl (genişletilmiş destek) 

Bu özellik, kullanıcının bulut hizmetindeki rol boyutunu ve örnek sayısını, hizmet yapılandırma ve hizmet tanımı dosyalarını güncelleştirmek zorunda kalmadan **Allowmodeloverride** özelliğini kullanarak güncelleştirmesine olanak tanıyacak ve böylece bulut hizmetinin bir yeniden paketleme ve yeniden dağıtmaya gerek kalmadan ölçeği artırma/azaltma/dışarı/dışarı

## <a name="set-allowmodeloverride-property"></a>AllowModelOverride özelliğini ayarla
AllowModelOverride özelliği aşağıdaki yollarla ayarlanabilir:
* AllowModelOverride = true olduğunda, API çağrısı, değeri, csdef ve cscfg dosyalarıyla doğrulayarak bulut hizmeti için rol boyutunu ve örnek sayısını güncelleştirir. 
> [!Note]
> Cscfg, rol örneği sayısını yansıtacak şekilde güncelleştirilir, ancak csdef (cspkg dahilinde) eski değerleri tutar
* AllowModelOverride = false olduğunda, rol boyutu ve örnek sayısı değerleri sırasıyla csdef ve cscfg dosyalarıyla eşleşmezse API çağrısı bir hata oluşturur

Varsayılan değer false olarak ayarlanır. Özellik true değerinden false değerine sıfırlandığında, csdef ve cscfg dosyaları yeniden doğrulama için denetlenir.

Bu özelliği PowerShell, şablon ve SDK 'da uygulamak için lütfen aşağıdaki örnekleri gözden geçirin

### <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu
Burada "allowModelOverride" = true özelliği ayarlandığında, bulut hizmeti roleProfile bölümünde tanımlanan rol özellikleriyle güncelleştirilecek
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
Yeni New-AzCloudService cmdlet 'inde "AllowModelOverride" anahtarı ayarlandığında, bulut hizmeti RoleProfile tarafından tanımlanan SKU özellikleriyle güncelleştirilecek
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK
AllowModelOverride = true değişkeninin ayarlanması, bulut hizmetinin RoleProfile içinde tanımlanan SKU özellikleriyle güncelleştirilmesini sağlayacak

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>Azure portalı
Portal, yukarıdaki özelliğin, csdef ve cscfg içindeki rol boyutunu ve örnek sayısını geçersiz kılmasına izin vermez. 


## <a name="next-steps"></a>Sonraki adımlar 
- Cloud Services için [dağıtım önkoşullarını](deploy-prerequisite.md) gözden geçirin (genişletilmiş destek).
- Cloud Services için [sık sorulan soruları](faq.md) gözden geçirin (genişletilmiş destek).
