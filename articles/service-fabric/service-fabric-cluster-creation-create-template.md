---
title: Azure Service Fabric küme şablonu oluşturma | Microsoft Docs
description: Bir Service Fabric kümesi için Kaynak Yöneticisi şablonu oluşturmayı öğrenin. İstemci kimlik doğrulaması için güvenliği, Azure Key Vault ve Azure Active Directory (Azure AD) yapılandırın.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: atsenthi
ms.openlocfilehash: 4a2fe8238a1ac6f668450aca4e2fd6d2b4ba04a5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901542"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Service Fabric kümesi Kaynak Yöneticisi şablonu oluşturma

[Azure Service Fabric kümesi](service-fabric-deploy-anywhere.md) , mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal makineler kümesidir. Azure 'da çalışan bir Service Fabric kümesi, Azure kaynağıdır ve Kaynak Yöneticisi kullanılarak dağıtılır, yönetilir ve izlenir.  Bu makalede, Azure 'da çalışan bir Service Fabric kümesi için Kaynak Yöneticisi şablonu oluşturma açıklanır.  Şablon tamamlandığında, [kümeyi Azure 'da dağıtabilirsiniz](service-fabric-cluster-creation-via-arm.md).

Küme güvenliği, küme ilk kez ayarlandığında yapılandırılır ve daha sonra değiştirilemez. Bir küme ayarlamadan önce [Service Fabric küme güvenliği senaryolarını][service-fabric-cluster-security]okuyun. Azure 'da Service Fabric, kümenizin ve uç noktalarının güvenliğini sağlamak, istemcilerin kimliğini doğrulamak ve verileri şifrelemek için x509 sertifikası kullanır. Yönetim uç noktalarına erişimin güvenliğini sağlamak için de Azure Active Directory önerilir. Kümeyi oluşturmadan önce Azure AD kiracılarının ve kullanıcılarının oluşturulması gerekir.  Daha fazla bilgi için, [istemcilerin kimliğini doğrulamak üzere Azure AD ayarlama](service-fabric-cluster-creation-setup-aad.md)makalesini okuyun.

Üretim iş yüklerini çalıştırmak için bir üretim kümesi dağıtılmadan önce, ilk olarak [Üretim hazırlığı denetim listesini](service-fabric-production-readiness-checklist.md)okuduğunuzdan emin olun.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Resource Manager şablonu oluşturma
Örnek Kaynak Yöneticisi şablonlar [GitHub 'Daki Azure örneklerinde](https://github.com/Azure-Samples/service-fabric-cluster-templates)bulunabilir. Bu şablonlar, küme şablonunuz için bir başlangıç noktası olarak kullanılabilir.

Bu makale [beş düğümlü güvenli küme][service-fabric-secure-cluster-5-node-1-nodetype] örnek şablonunu ve şablon parametrelerini kullanır. *Azuredeploy. JSON* ve *azuredeploy. Parameters. JSON* dosyasını bilgisayarınıza indirin ve her iki dosyayı da en sevdiğiniz metin düzenleyicisinde açın.

> [!NOTE]
> Ulusal bulutlar (Azure Kamu, Azure Çin, Azure Almanya) için şu `fabricSettings` şablonunuza de eklemeniz gerekir: `AADLoginEndpoint`, `AADTokenEndpointFormat` ve `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Sertifika ekle
Sertifika anahtarlarını içeren anahtar kasasına başvurarak bir küme Kaynak Yöneticisi şablonuna sertifikalar eklersiniz. Bu Anahtar Kasası parametrelerini ve değerlerini bir Kaynak Yöneticisi şablon parametreleri dosyasına (*azuredeploy. Parameters. JSON*) ekleyin.

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Tüm sertifikaları sanal makine ölçek kümesi osProfile 'e Ekle
Kümede yüklü olan her sertifikanın, ölçek kümesi kaynağının **Osprofile** bölümünde (Microsoft. COMPUTE/virtualMachineScaleSets) yapılandırılması gerekir. Bu eylem, kaynak sağlayıcısına sertifikayı VM 'Lere yüklemesini söyler. Bu yükleme, hem küme sertifikasını hem de uygulamalarınız için kullanmayı planladığınız uygulama güvenlik sertifikalarını içerir:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Service Fabric kümesi sertifikasını yapılandırma

Küme kimlik doğrulama sertifikası, sanal makine ölçek kümesi kaynağındaki sanal makine ölçek kümeleri için hem Service Fabric küme kaynağında (Microsoft. ServiceFabric/kümeler) hem de Service Fabric uzantısında yapılandırılmalıdır. Bu düzenleme Service Fabric kaynak sağlayıcısının yönetim uç noktaları için küme kimlik doğrulaması ve sunucu kimlik doğrulaması için kullanılmak üzere yapılandırmasını sağlar.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Sanal Makine Ölçek Kümesi kaynağı sertifika bilgilerini ekleyin

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Service Fabric küme kaynağına sertifika bilgilerini ekleyin

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>İstemci erişimi için Azure AD 'yi kullanmak üzere Azure AD yapılandırması ekleme

Azure AD yapılandırmasını, sertifika anahtarlarını içeren anahtar kasasına başvurarak bir küme Kaynak Yöneticisi şablonuna eklersiniz. Bu Azure AD parametrelerini ve değerlerini bir Kaynak Yöneticisi şablon parametreleri dosyasına (*azuredeploy. Parameters. JSON*) ekleyin. 

> [!NOTE]
> Linux 'ta, kümeyi oluşturmadan önce Azure AD kiracılarının ve kullanıcılarının oluşturulması gerekir.  Daha fazla bilgi için, [istemcilerin kimliğini doğrulamak üzere Azure AD ayarlama](service-fabric-cluster-creation-setup-aad.md)makalesini okuyun.

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Parametre dosyasını değerlerle doldurun

Son olarak, parametreler dosyasını doldurmak için anahtar kasası ve Azure AD PowerShell komutlarıyla çıkış değerlerini kullanın.

Azure Service Fabric RM PowerShell modüllerini kullanmayı planlıyorsanız, küme sertifikası bilgilerini doldurmanıza gerek yoktur. Sistemin küme güvenliği için otomatik olarak imzalanan sertifika oluşturmasını istiyorsanız, bunları null olarak tutmanız yeterlidir. 

> [!NOTE]
> RM modüllerinin bu boş parametre değerlerini seçmesini ve doldurması için, parametre adları aşağıdaki adlarla çok eşleşir

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Uygulama sertifikalarını kullanıyorsanız veya anahtar kasasında karşıya yüklediğiniz mevcut bir kümeyi kullanıyorsanız, bu bilgileri almanız ve doldurmanız gerekir.

RM modüllerinin sizin için Azure AD yapılandırması oluşturma yeteneği yoktur; bu nedenle, istemci erişimi için Azure AD 'yi kullanmayı planlıyorsanız, doldurmanız gerekir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Şablonunuzu test etme
Kaynak Yöneticisi şablonunuzu bir parametre dosyası ile test etmek için aşağıdaki PowerShell komutunu kullanın:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Sorunlarla karşılaşmanıza ve şifreli mesajlar almanıza ve ardından "-Debug" seçeneğini bir seçenek olarak kullanmanız gerekir.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Aşağıdaki diyagramda, anahtar kasanızın ve Azure AD yapılandırmanızın Kaynak Yöneticisi şablonunuz nerede olduğu gösterilmektedir.

![Kaynak Yöneticisi bağımlılık eşlemesi][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Sonraki adımlar
Kümeniz için bir şablonunuz olduğuna göre, [kümeyi Azure 'a dağıtmayı](service-fabric-cluster-creation-via-arm.md)öğrenin.  Henüz yapmadıysanız, üretim kümesi dağıtılmadan önce [Üretim hazırlığı denetim listesini](service-fabric-production-readiness-checklist.md) okuyun.

Bu makalede dağıtılan kaynakların JSON sözdizimi ve özellikleri hakkında bilgi edinmek için bkz.:

* [Microsoft. ServiceFabric/kümeler](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/Publicıpaddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft. COMPUTE/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
