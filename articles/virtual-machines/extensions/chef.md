---
title: Azure VM 'Leri için Chef uzantısı
description: Chef Istemcisini bir sanal makineye Chef VM uzantısını kullanarak dağıtın.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 09/21/2018
ms.openlocfilehash: e316bf9763dd7c2cbbab21992086eac52d108912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554794"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Linux ve Windows için Chef VM Uzantısı

Chef Software, Linux ve Windows için fiziksel ve sanal sunucu yapılandırmalarının yönetilmesine olanak sağlayan bir DevOps otomasyon platformu sunar. Chef VM uzantısı, sanal makinelerde Chef sağlayan bir uzantıdır.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Chef VM uzantısı, Azure 'da [desteklenen tüm uzantı](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) sistemlerinde desteklenir.

### <a name="internet-connectivity"></a>İnternet bağlantısı

Chef VM uzantısı, içerik teslim ağından (CDN) Chef Istemci yükünü almak için hedef sanal makinenin internet 'e bağlanmasını gerektirir.  

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON, Chef VM uzantısının şemasını gösterir. Uzantı en az Chef sunucu URL 'SI, doğrulama Istemci adı ve Chef sunucusu için doğrulama anahtarı gerektirir; Bu değerler `knife.rb` , [Chef otomatikleştir](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) veya tek başına [Chef sunucusu](https://downloads.chef.io/chef-server)yüklediğinizde indirilen starter-kit.zip dosyasında bulunabilir. Doğrulama anahtarı gizli veriler olarak değerlendirilmelidir, ancak bu, yalnızca hedef sanal makinede şifresi çözülebilecek olan **Protectedsettings** öğesi altında yapılandırılmalıdır.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.13",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Çekirdek özellik değerleri

| Name | Değer/örnek | Veri Türü
| ---- | ---- | ----
| apiVersion | `2017-12-01` | dize (Tarih) |
| yayımcı | `Chef.Bootstrap.WindowsAzure` | string |
| tür | `LinuxChefClient` (Linux), `ChefClient` (Windows) | string |
| typeHandlerVersion | `1210.13` | dize (çift) |

### <a name="settings"></a>Ayarlar

| Name | Değer/örnek | Veri Türü | Gerekli mi?
| ---- | ---- | ---- | ----
| ayarlar/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | dize (URL) | Y |
| ayarlar/bootstrap_options/validation_client_name | `myorg-validator` | string | Y |
| ayarlar/runlist | `recipe[mycookbook::default]` | string | Y |

### <a name="protected-settings"></a>Korumalı ayarlar

| Name | Örnek | Veri Türü | Gerekli mi?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | Y |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları, Azure Resource Manager şablonlarıyla dağıtılabilir. Şablonlar bir veya daha fazla sanal makineyi dağıtmak, Chef Istemcisini yüklemek, Chef sunucusuna bağlanmak ve [çalışma listesi](https://docs.chef.io/run_lists.html) tarafından tanımlandığı şekilde sunucuda ilk yapılandırmayı gerçekleştirmek için kullanılabilir

Chef VM uzantısını içeren örnek bir Kaynak Yöneticisi şablonu [Azure hızlı başlangıç galerisinde](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm)bulunabilir.

Bir sanal makine uzantısının JSON yapılandırması, sanal makine kaynağının içinde iç içe veya Kaynak Yöneticisi JSON şablonunun kök veya üst düzeyine yerleştirilmiş olabilir. JSON yapılandırmasının yerleştirilmesi, kaynak adının ve türün değerini etkiler. Daha fazla bilgi için bkz. [alt kaynaklar için ad ve tür ayarlama](../../azure-resource-manager/templates/child-resource-name-type.md).

## <a name="azure-cli-deployment"></a>Azure CLı dağıtımı

Azure CLı, Chef VM uzantısını mevcut bir VM 'ye dağıtmak için kullanılabilir. **Validation_key** , doğrulama anahtarınızın içeriğiyle değiştirin (Bu dosya `.pem` uzantı olarak).  **Validation_client_name**, **chef_server_url** ve **run_list** yerine, `knife.rb` Başlangıç seinizdeki dosyadaki değerleri değiştirin.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Sorun giderme ve destek

Uzantı dağıtımlarının durumu hakkındaki veriler Azure portal ve Azure CLı kullanılarak alınabilir. Belirli bir VM için uzantıların dağıtım durumunu görmek için, Azure CLı 'yı kullanarak aşağıdaki komutu çalıştırın.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Uzantı yürütme çıkışı aşağıdaki dosyaya kaydedilir:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Hata kodları ve anlamları

| Hata Kodu | Anlamı | Olası eylem |
| :---: | --- | --- |
| 51 | Bu uzantı, sanal makinenin işletim sisteminde desteklenmiyor | |

Ek sorun giderme bilgileri [Chef VM Uzantısı Benioku](https://github.com/chef-partners/azure-chef-extension)dosyasında bulunabilir.

> [!NOTE]
> Chef ile doğrudan ilgili başka herhangi bir şey için [Chef desteğiyle](https://www.chef.io/support/)iletişim kurun.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve Destek Al ' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.
