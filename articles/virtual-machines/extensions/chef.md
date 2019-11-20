---
title: Azure VM 'Leri için Chef uzantısı
description: Chef Istemcisini bir sanal makineye Chef VM uzantısını kullanarak dağıtın.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: akjosh
ms.openlocfilehash: 2b69a17c7f9de62187d9dc99f7c1d5c5b74c25ad
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073198"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Linux ve Windows için Chef VM Uzantısı

Chef Software, Linux ve Windows için fiziksel ve sanal sunucu yapılandırmalarının yönetilmesine olanak sağlayan bir DevOps otomasyon platformu sunar. Chef VM uzantısı, sanal makinelerde Chef sağlayan bir uzantıdır.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Chef VM uzantısı, Azure 'da [desteklenen tüm uzantı](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) sistemlerinde desteklenir.

### <a name="internet-connectivity"></a>İnternet bağlantısı

Chef VM uzantısı, içerik teslim ağından (CDN) Chef Istemci yükünü almak için hedef sanal makinenin internet 'e bağlanmasını gerektirir.  

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON, Chef VM uzantısının şemasını gösterir. Uzantı en az Chef sunucu URL 'SI, doğrulama Istemci adı ve Chef sunucusu için doğrulama anahtarı gerektirir; Bu değerler, [Chef otomatikleştir](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) veya tek başına [Chef sunucusu](https://downloads.chef.io/chef-server)yüklediğinizde indirilen starter-kit. zip dosyasındaki `knife.rb` dosyasında bulunabilir. Doğrulama anahtarı gizli veriler olarak değerlendirilmelidir, ancak bu, yalnızca hedef sanal makinede şifresi çözülebilecek olan **Protectedsettings** öğesi altında yapılandırılmalıdır.

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
    "typeHandlerVersion": "1210.12",
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

| Ad | Değer / örnek | Veri Türü
| ---- | ---- | ----
| apiVersion | `2017-12-01` | string (date) |
| publisher | `Chef.Bootstrap.WindowsAzure` | string |
| type | `LinuxChefClient` (Linux), `ChefClient` (Windows) | string |
| typeHandlerVersion | `1210.12` | string (double) |

### <a name="settings"></a>Ayarlar

| Ad | Değer / örnek | Veri Türü | Gerekli mi?
| ---- | ---- | ---- | ----
| ayarlar/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | string (url) | E |
| ayarlar/bootstrap_options/validation_client_name | `myorg-validator` | string | E |
| settings/runlist | `recipe[mycookbook::default]` | string | E |

### <a name="protected-settings"></a>Korumalı ayarlar

| Ad | Örnek | Veri Türü | Gerekli mi?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | E |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları Azure Resource Manager şablonları ile dağıtılabilir. Şablonlar bir veya daha fazla sanal makineyi dağıtmak, Chef Istemcisini yüklemek, Chef sunucusuna bağlanmak ve [çalışma listesi](https://docs.chef.io/run_lists.html) tarafından tanımlandığı şekilde sunucuda ilk yapılandırmayı gerçekleştirmek için kullanılabilir

Chef VM uzantısını içeren örnek bir Kaynak Yöneticisi şablonu [Azure hızlı başlangıç galerisinde](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm)bulunabilir.

Sanal makine uzantısı için JSON yapılandırma içinde sanal makine kaynağı iç içe geçmiş veya kök veya bir Resource Manager JSON şablonunu üst düzey yerleştirilir. Kaynak adı ve türü değeri JSON yapılandırma yerleşimini etkiler. Daha fazla bilgi için [ayarlamak için alt kaynakları ad ve tür](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Azure CLI dağıtım

Azure CLı, Chef VM uzantısını mevcut bir VM 'ye dağıtmak için kullanılabilir. **Validation_key** , doğrulama anahtarınızın içeriğiyle değiştirin (bu dosya `.pem` uzantısı olarak).  **Validation_client_name**, **chef_server_url** ve **Run_list** yerine bu değerleri, başlangıç seinizdeki `knife.rb` dosyasından değiştirin.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.12 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Sorun giderme ve destek

Uzantı dağıtım durumuyla ilgili veriler, Azure portalından ve Azure CLI kullanılarak alınabilir. Belirli bir VM'nin için uzantıları dağıtım durumunu görmek için Azure CLI kullanarak aşağıdaki komutu çalıştırın.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Uzantı yürütme çıkış aşağıdaki dosyasına kaydedilir:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Hata kodları ve anlamları

| Hata Kodu | Anlamı | Olası eylemi |
| :---: | --- | --- |
| 51 | Bu uzantı, sanal makinenin işletim sisteminde desteklenmiyor | |

Ek sorun giderme bilgileri [Chef VM Uzantısı Benioku](https://github.com/chef-partners/azure-chef-extension)dosyasında bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/forums/). Alternatif olarak, bir Azure destek olayına dosya. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) ve Destek Al'ı seçin. Azure desteği hakkında daha fazla bilgi için okuma [Microsoft Azure desteği SSS](https://azure.microsoft.com/support/faq/).
