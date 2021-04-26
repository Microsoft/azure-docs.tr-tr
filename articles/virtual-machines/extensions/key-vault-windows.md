---
title: Windows için Azure Key Vault VM Uzantısı
description: Sanal makine uzantısı kullanarak sanal makinelerde Key Vault gizliliklerin otomatik olarak yenilenmesini gerçekleştirerek bir aracı dağıtın.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: a984d044134dbd775bacb653f8590ee78724f15b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563566"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Windows için Key Vault sanal makine uzantısı

Key Vault VM uzantısı, Azure Anahtar Kasası 'nda depolanan sertifikaların otomatik olarak yenilenmesini sağlar. Özellikle uzantı, anahtar kasalarında depolanan gözlemlenen sertifikaların listesini izler ve bir değişikliği tespit etmek için ilgili sertifikaları alır ve kurar. Bu belgede, Windows için Key Vault VM uzantısı için desteklenen platformlar, konfigürasyonlar ve dağıtım seçenekleri ayrıntılı olarak bulunmaktadır. 

### <a name="operating-system"></a>İşletim sistemi

Key Vault VM uzantısı, Windows 'un aşağıdaki sürümlerini destekler:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

Key Vault VM uzantısı, Windows Server 2019 Core yüklemesi kullanılarak Azure 'da kullanılmak üzere yüklenmiş ve özelleştirilmiş bir görüntüye dönüştürülen özel yerel VM 'de de desteklenir.

### <a name="supported-certificate-content-types"></a>Desteklenen sertifika içerik türleri

- PKCS #12
- PEM

## <a name="prerequisites"></a>Önkoşullar

  - Sertifikayı içeren Key Vault örneği. Bkz. [Key Vault oluşturma](../../key-vault/general/quick-create-portal.md)
  - VM 'nin [yönetilen kimliği](../../active-directory/managed-identities-azure-resources/overview.md) atanmış olması gerekir
  - Key Vault erişim Ilkesi, parolaların `get` `list` sertifikanın bir bölümünü almak için VM/VMSS yönetilen kimlik ile gizli dizi ve izinle ayarlanmalıdır. [Key Vault Için kimlik doğrulama](../../key-vault/general/authentication.md) ve [Key Vault erişim ilkesi atama](../../key-vault/general/assign-access-policy-cli.md)konusuna bakın.
  -  Sanal Makine Ölçek Kümeleri aşağıdaki kimlik ayarlarına sahip olmalıdır:

  ``` 
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "[parameters('userAssignedIdentityResourceId')]": {}
    }
  }
  ```
  
  - AKV uzantısında Bu ayar olmalıdır:

  ```
  "authenticationSettings": {
    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
  }
  ```

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON Key Vault VM uzantısının şemasını gösterir. Uzantı, korumalı ayarlar gerektirmez. tüm ayarları ortak bilgiler olarak kabul edilir. Uzantı, izlenen sertifikaların, yoklama sıklığının ve hedef sertifika deposunun bir listesini gerektirir. Özellikle:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> Gözlemlenen sertifikalarınızın URL 'Leri form olmalıdır `https://myVaultName.vault.azure.net/secrets/myCertName` .
> 
> Bunun nedeni, `/secrets` yol değil, özel anahtar dahil olmak üzere tam sertifikayı döndürmektedir `/certificates` . Sertifikalar hakkında daha fazla bilgi için şurada bulunabilir: [Key Vault sertifikaları](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> ' AuthenticationSettings ' özelliği yalnızca **Kullanıcı tarafından atanan kimlikleri** olan VM 'ler için **gereklidir** .
> Key Vault kimlik doğrulaması için kullanılacak kimliği belirtir.


### <a name="property-values"></a>Özellik değerleri

| Name | Değer/örnek | Veri Türü |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| yayımcı | Microsoft.Azure.KeyVault | string |
| tür | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | int |
| Pollingınterinterval bileşenleri | 3600 | string |
| certificateStoreName | MY | string |
| Linkonyenilemeye | yanlış | boolean |
| certificateStoreLocation  | LocalMachine veya CurrentUser (büyük/küçük harfe duyarlı) | string |
| Requireınitialsync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate","https://myvault.vault.azure.net/secrets/mycertificate2"] | dize dizisi
| Msıendpoint | http://169.254.169.254/metadata/identity | string |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | string |


## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları, Azure Resource Manager şablonlarıyla dağıtılabilir. Sertifikaların dağıtım sonrası yenilenmesini gerektiren bir veya daha fazla sanal makine dağıtıldığında şablonlar idealdir. Uzantı ayrı VM 'lere veya sanal makine ölçek kümelerine dağıtılabilir. Şema ve yapılandırma her iki şablon türü için ortaktır. 

Bir sanal makine uzantısının JSON yapılandırması, şablonun sanal makine kaynak parçasının içinde, özellikle de `"resources": []` sanal makine şablonu için nesne ve nesne altında sanal makine ölçek kümesi olması halinde iç içe olmalıdır `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` .

 > [!NOTE]
> VM uzantısı, Anahtar Kasası kimlik doğrulaması için sistem veya Kullanıcı tarafından yönetilen kimliğin atanmasını gerektirir.  [Key Vault için kimlik doğrulama ve Key Vault erişim ilkesi atama](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) konusuna bakın.
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>Uzantı bağımlılığı sıralaması
Key Vault VM Uzantısı yapılandırıldıysa uzantı sıralamasını destekler. Varsayılan olarak, uzantı yoklamaya başladığı anda başarıyla başlatıldığını bildirir. Ancak, başarılı bir başlangıç raporlanmadan önce sertifikaların tüm listesini başarıyla indirene kadar bekleyecek şekilde yapılandırılabilir. Diğer uzantılar, başlamadan önce tam sertifika kümesine sahip olmaya bağımlıysa, bu ayarın etkinleştirilmesi, bu uzantının Key Vault uzantısında bir bağımlılık tanımlamasına olanak tanır. Bu, bağımlı oldukları tüm sertifikaların yüklenene kadar bu uzantıların başlamasını engeller. Uzantı ilk indirmeyi süresiz olarak yeniden dener ve bir `Transitioning` durumda kalır.

Bunu açmak için aşağıdakileri ayarlayın:
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```

> [!Note] 
> Bu özelliğin kullanılması, sistem tarafından atanan bir kimlik oluşturan ve bu kimlikle Key Vault erişim ilkesini güncelleştiren bir ARM şablonuyla uyumlu değildir. Bunun yapılması, tüm uzantılar başlatılana kadar kasa erişimi ilkesi güncelleştirilene kadar kilitlenmeyle sonuçlanır. Bunun yerine, dağıtımını yapmadan önce *tek bir kullanıcı tarafından atanmış BIR MSI kimliği* ve bu kimlikle kasalar IÇIN bir ACL ön eki kullanmanız gerekir.

## <a name="azure-powershell-deployment"></a>Azure PowerShell dağıtımı
> [!WARNING]
> PowerShell istemcileri genellikle `\` `"` üzerinde settings.js, akvvm_service hata vererek başarısız olmasına neden olacak. `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Azure PowerShell, Key Vault VM uzantısını var olan bir sanal makineye veya sanal makine ölçek kümesine dağıtmak için kullanılabilir. 

* Uzantıyı bir sanal makineye dağıtmak için:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Uzantıyı bir sanal makine ölçek kümesine dağıtmak için:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLı dağıtımı

Azure CLı, Key Vault VM uzantısını var olan bir sanal makineye veya sanal makine ölçek kümesine dağıtmak için kullanılabilir. 
 
* Uzantıyı bir sanal makineye dağıtmak için:
    
    ```azurecli
       # Start the deployment
         az vm extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Uzantıyı bir sanal makine ölçek kümesine dağıtmak için:

   ```azurecli
        # Start the deployment
        az vmss extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

Lütfen aşağıdaki kısıtlamalara/gereksinimlere dikkat edin:
- Key Vault kısıtlamaları:
  - Dağıtım sırasında var olmalıdır 
  - Key Vault erişim Ilkesi, yönetilen bir kimlik kullanılarak VM/VMSS kimliği için ayarlanmalıdır. [Key Vault Için kimlik doğrulama](../../key-vault/general/authentication.md) ve [Key Vault erişim ilkesi atama](../../key-vault/general/assign-access-policy-cli.md)konusuna bakın.

## <a name="troubleshoot-and-support"></a>Sorun giderme ve destek

### <a name="frequently-asked-questions"></a>Sık Sorulan Sorular

* Ayarlayabilmeniz için observedCertificates sayısında bir sınır var mı?
  Hayır, Key Vault VM uzantısının observedCertificates sayısı üzerinde sınırı yok.

### <a name="troubleshoot"></a>Sorun giderme

Uzantı dağıtımlarının durumuyla ilgili veriler, Azure portal alabilir ve Azure PowerShell kullanılarak elde edilebilir. Belirli bir VM için uzantıların dağıtım durumunu görmek için, Azure PowerShell kullanarak aşağıdaki komutu çalıştırın.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Azure CLI**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

#### <a name="logs-and-configuration"></a>Günlükler ve yapılandırma

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```

### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve Destek Al ' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.
