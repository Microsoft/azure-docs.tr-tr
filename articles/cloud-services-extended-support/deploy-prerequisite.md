---
title: Azure Cloud Services dağıtımı önkoşulları (genişletilmiş destek)
description: Azure Cloud Services dağıtımı önkoşulları (genişletilmiş destek)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: bce09fad6ffa169a019628498a686226eff266c7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384995"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Azure Cloud Services dağıtımı önkoşulları (genişletilmiş destek)

Başarılı bir Cloud Services (genişletilmiş destek) dağıtımı sağlamak için aşağıdaki adımları gözden geçirin ve herhangi bir dağıtımı denemeden önce her bir öğeyi doldurun. 

## <a name="required-service-configuration-cscfg-file-updates"></a>Gerekli hizmet yapılandırma (. cscfg) dosya güncelleştirmeleri

### <a name="1-virtual-network"></a>1) sanal ağ
Bulut hizmeti (genişletilmiş destek) dağıtımları bir sanal ağda olmalıdır. Sanal ağ [Azure Portal](../virtual-network/quick-create-portal.md), [POWERSHELL](../virtual-network/quick-create-powershell.md), [Azure CLI](../virtual-network/quick-create-cli.md) veya [ARM şablonu](../virtual-network/quick-create-template.md)aracılığıyla oluşturulabilir. Sanal ağ ve alt ağlara, [NetworkConfiguration](schema-cscfg-networkconfiguration.md) bölümünde hizmet yapılandırması 'nda (. cscfg) de başvurulmalıdır. 

Bulut hizmeti ile aynı kaynak grubuna ait olan sanal ağlar için, yalnızca hizmet yapılandırma (. cscfg) dosyasındaki sanal ağ adına başvurulmaya yeterlidir. Sanal ağ ve bulut hizmeti iki farklı kaynak grubunda yer alıyorsa, sanal ağın tüm Azure Resource Manager KIMLIĞININ hizmet yapılandırma (. cscfg) dosyasında belirtilmesi gerekir.
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Aynı kaynak grubunda bulunan sanal ağ
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>Farklı kaynak grubunda bulunan sanal ağ
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) eski eklentileri kaldır

Eski uzak masaüstü ayarlarını hizmet yapılandırma (. cscfg) dosyasından kaldırın.  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```
Hizmet yapılandırma (. cscfg) dosyasındaki her bir rol için eski tanılama ayarlarını kaldırın.

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
```

## <a name="required-service-definition-file-csdef-updates"></a>Gerekli hizmet tanımı dosyası (. csdef) güncelleştirmeleri

> [!NOTE]
> Hizmet tanımı dosyası (. csdef) içindeki değişiklikler, paket dosyasının (. cspkg) yeniden oluşturulmasını gerektirir. Lütfen bulut hizmetiniz için en son ayarları almak üzere. cspkg gönderinizi oluşturup yeniden paketleyin. csdef dosyasında aşağıdaki değişiklikleri yapın

### <a name="1-virtual-machine-sizes"></a>1) sanal makine boyutları
Aşağıdaki boyutlar Azure Resource Manager kullanım dışıdır. Ancak, bunları kullanmaya devam etmek istiyorsanız, `vmsize` adı ilişkili Azure Resource Manager adlandırma kuralına göre güncelleştirin.  

| Önceki Boyut adı | Güncelleştirilmiş boyut adı | 
|---|---|
| ExtraSmall | Standard_A0 | 
| Küçük | Standard_A1 |
| Orta | Standard_A2 | 
| Büyük | Standard_A3 | 
| Çok büyük | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 Örneğin, `<WorkerRole name="WorkerRole1" vmsize="Medium"` olur `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"` .
 
> [!NOTE]
> Kullanılabilir boyutların bir listesini almak için bkz. [kaynak SKU 'ları-Listele](/rest/api/compute/resourceskus/list) ve aşağıdaki filtreleri Uygula: <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) eski uzak masaüstü eklentilerini kaldır
Eski uzak masaüstü eklentilerini kullanan dağıtımlar, modüllerin hizmet tanımı (. csdef) dosyasından ve ilişkili sertifikalardan kaldırılmasına gerek duyar. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```
Eski tanılama eklentilerini kullanan dağıtımlar, hizmet tanımı (. csdef) dosyasındaki her bir rol için kaldırılan ayarlara sahip olmalıdır

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

## <a name="key-vault-creation"></a>Key Vault oluşturma 

Key Vault, Cloud Services ilişkili sertifikaları (genişletilmiş destek) depolamak için kullanılır. Sertifikaları Key Vault ekleyin ve ardından hizmet yapılandırma dosyasında sertifika parmak izleriyle referans yapın. Ayrıca, Cloud Services (genişletilmiş destek) kaynağı Key Vault gizli dizi olarak depolanan sertifikayı alabilmesi için ' dağıtım için Azure sanal makineler ' için Key Vault ' erişim ilkelerini ' (portalda) etkinleştirmeniz gerekir. [Azure Portal](../key-vault/general/quick-create-portal.md) veya [PowerShell](../key-vault/general/quick-create-powershell.md)kullanarak bir Anahtar Kasası oluşturabilirsiniz. Anahtar Kasası, bulut hizmeti ile aynı bölgede ve abonelikte oluşturulmalıdır. Daha fazla bilgi için bkz. [Azure Cloud Services sertifikaları kullanma (genişletilmiş destek)](certificates-and-key-vault.md).

## <a name="next-steps"></a>Sonraki adımlar 
- Cloud Services için [dağıtım önkoşullarını](deploy-prerequisite.md) gözden geçirin (genişletilmiş destek).
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [şablon](deploy-template.md) veya [Visual Studio](deploy-visual-studio.md)kullanarak bir bulut hizmeti (genişletilmiş destek) dağıtın.
- Cloud Services için [sık sorulan soruları](faq.md) gözden geçirin (genişletilmiş destek).
- [Cloud Services (genişletilmiş destek) örnekleri deposunu](https://github.com/Azure-Samples/cloud-services-extended-support) ziyaret edin
