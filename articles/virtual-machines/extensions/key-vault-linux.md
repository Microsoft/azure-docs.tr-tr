---
title: Linux için Azure Key Vault VM Uzantısı
description: Sanal makine uzantısı kullanarak sanal makinelerde Key Vault sertifikalarının otomatik olarak yenilenmesini gerçekleştiren bir aracı dağıtın.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 9032bfca30ead56c91d7904e18b76753cf3b6dfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582179"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Linux için sanal makine uzantısı Key Vault

Key Vault VM uzantısı, Azure Anahtar Kasası 'nda depolanan sertifikaların otomatik olarak yenilenmesini sağlar. Özellikle uzantı, anahtar kasalarında depolanan gözlemlenen sertifikaların listesini izler.  Bir değişiklik algılandıktan sonra uzantı ilgili sertifikaları alır ve kurar. Key Vault VM uzantısı, şu anda Linux VM 'lerinde Microsoft tarafından yayımlanır ve desteklenir. Bu belgede, Linux için Key Vault VM uzantısı için desteklenen platformlar, konfigürasyonlar ve dağıtım seçenekleri ayrıntılı olarak bulunmaktadır. 

### <a name="operating-system"></a>İşletim sistemi

Key Vault VM Uzantısı şu Linux dağıtımlarını destekler:

- Ubuntu-1804
- SUSE-15 

> [!NOTE]
> Genişletilmiş güvenlik özellikleri almak için, bu sürümler belirlenen destek döneminin sonuna ulaşmakta olduğundan Ubuntu-1604 ve detem-9 sistemlerini yükseltmeye hazırlanın.
> 

### <a name="supported-certificate-content-types"></a>Desteklenen sertifika içerik türleri

- PKCS #12
- PEM


## <a name="prerequisities"></a>Ön koşullarını sağlarken
  - Sertifikayı içeren Key Vault örneği. Bkz. [Key Vault oluşturma](../../key-vault/general/quick-create-portal.md)
  - VM/VMSS 'nin [yönetilen kimliği](../../active-directory/managed-identities-azure-resources/overview.md) atanmış olması gerekir
  - Key Vault erişim Ilkesi, parolaların `get` `list` sertifikanın bir bölümünü almak için VM/VMSS yönetilen kimlik ile gizli dizi ve izinle ayarlanmalıdır. [Key Vault Için kimlik doğrulama](../../key-vault/general/authentication.md) ve [Key Vault erişim ilkesi atama](../../key-vault/general/assign-access-policy-cli.md)konusuna bakın.
  -  VMSS 'nin aşağıdaki kimlik ayarı olmalıdır: ` 
  "identity": {
  "type": "UserAssigned",
  "userAssignedIdentities": {
  "[parameters('userAssignedIdentityResourceId')]": {}
  }
  }
  `
  
 - AKV uzantısında Bu ayar olmalıdır: `
                 "authenticationSettings": {
                    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
                    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
                  }
   `
## <a name="key-vault-vm-extension-version"></a>Key Vault VM Uzantısı sürümü
* Ubuntu-18,04 ve SUSE-15 kullanıcıları Anahtar Kasası VM Uzantısı sürümünü, `V2.0` kullanılabilir tam sertifika zinciri indirme özelliğine yükseltmeyi seçebilir. Verenin sertifikaları (ara ve kök), ped dosyasındaki yaprak sertifikaya eklenecektir.

* ' A yükseltmeyi tercih ediyorsanız `v2.0` , önce öğesini silmeniz `v1.0` ve sonra yüklemeniz gerekir `v2.0` .
```
  az vm extension delete --name KeyVaultForLinux --resource-group ${resourceGroup} --vm-name ${vmName}
  az vm extension set -n "KeyVaultForLinux" --publisher Microsoft.Azure.KeyVault --resource-group "${resourceGroup}" --vm-name "${vmName}" –settings .\akvvm.json –version 2.0
```  
  En son sürüm varsayılan olarak yüklenebildiğinden,--2,0 bayrağı isteğe bağlıdır.   

* VM 'nin v 1.0 tarafından indirilen sertifikaları varsa, v 1.0 AKVVM uzantısının silinmesi indirilen sertifikaları silmez.  V 2.0 'ı yükledikten sonra mevcut sertifikalar değiştirilmez.  SANAL makinede tam zincirle ped dosyasını almak için sertifika dosyalarını silmeniz veya sertifikayı almanız gerekir.




## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON Key Vault VM uzantısının şemasını gösterir. Uzantı korumalı ayarlar gerektirmez. tüm ayarları güvenlik etkisi olmadan bilgi olarak kabul edilir. Uzantı, izlenen gizli dizi, yoklama sıklığı ve hedef sertifika depolama alanı için bir liste gerektirir. Özellikle:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
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
| tür | KeyVaultForLinux | string |
| typeHandlerVersion | 2.0 | int |
| Pollingınterinterval bileşenleri | 3600 | string |
| certificateStoreName | Linux üzerinde yok sayılır | string |
| Linkonyenilemeye | yanlış | boolean |
| certificateStoreLocation  | /var/lib/waagent/Microsoft.Azure.KeyVault | string |
| Requireınitialsync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | dize dizisi
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
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
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
> Notun Bu özelliğin kullanılması, sistem tarafından atanan bir kimlik oluşturan ve bu kimlikle Key Vault erişim ilkesini güncelleştiren bir ARM şablonuyla uyumlu değildir. Bunun yapılması, tüm uzantılar başlatılana kadar kasa erişimi ilkesi güncelleştirilene kadar kilitlenmeyle sonuçlanır. Bunun yerine, dağıtımını yapmadan önce *tek bir kullanıcı tarafından atanmış BIR MSI kimliği* ve bu kimlikle kasalar IÇIN bir ACL ön eki kullanmanız gerekir.

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
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "2.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Uzantıyı bir sanal makine ölçek kümesine dağıtmak için:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "2.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLı dağıtımı

Azure CLı, Key Vault VM uzantısını var olan bir sanal makineye veya sanal makine ölçek kümesine dağıtmak için kullanılabilir. 
 
* Uzantıyı bir sanal makineye dağıtmak için:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --version 2.0 `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Uzantıyı bir sanal makine ölçek kümesine dağıtmak için:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vmss-name "<vmssName>" `
        --version 2.0 `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```
Lütfen aşağıdaki kısıtlamalara/gereksinimlere dikkat edin:
- Key Vault kısıtlamaları:
  - Dağıtım sırasında var olmalıdır 
  - Key Vault erişim Ilkesi, yönetilen bir kimlik kullanılarak VM/VMSS kimliği için ayarlanmalıdır. [Key Vault Için kimlik doğrulama](../../key-vault/general/authentication.md) ve [Key Vault erişim ilkesi atama](../../key-vault/general/assign-access-policy-cli.md)konusuna bakın.

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
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```
### <a name="using-symlink"></a>Symlink kullanma

Sembolik bağlantılar veya Symbağlantılar temelde gelişmiş kısayollardır. Klasörü izlemeyi önlemek ve en son sertifikayı otomatik olarak almak için bu oluşturmaksızın 'i kullanarak `([VaultName].[CertificateName])` Linux üzerinde sertifikanın en son sürümünü edinebilirsiniz.

### <a name="frequently-asked-questions"></a>Sık Sorulan Sorular

* Ayarlayabilmeniz için observedCertificates sayısında bir sınır var mı?
  Hayır, Key Vault VM uzantısının observedCertificates sayısı üzerinde sınırı yok.

### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve Destek Al ' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.
