---
title: Azure 'da VM 'Ler için önyükleme tanılaması | Microsoft doc
description: Azure 'daki sanal makineler için iki hata ayıklama özelliğine genel bakış
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: d34182e09bf453dbec1e9592f131cb9b3b78a086
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086480"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Azure 'da sanal makinelerde sorun gidermek için önyükleme tanılamayı kullanma

Bir sanal makinenin önyüklenebilir olmayan bir duruma girmesinin pek çok nedeni olabilir. Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulan sanal makinelerinizdeki sorunları gidermek için aşağıdaki hata ayıklama özelliklerini kullanabilirsiniz: Azure sanal makineleri için konsol çıkışı ve ekran görüntüsü desteği. 

Linux sanal makineleri için, portaldan konsol günlüğlerinizin çıkışını görüntüleyebilirsiniz. Azure, hem Windows hem de Linux sanal makineleri için Hiper yöneticide VM 'nin ekran görüntüsünü görmenizi sağlar. Her iki özellik de tüm bölgelerdeki Azure sanal makineleri için desteklenir. Not, ekran görüntüleri ve çıktının depolama hesabınızda görünmesi 10 dakika sürebilir.

Günlüğü ve ekran görüntüsünü görüntülemek için **önyükleme tanılama** seçeneğini belirleyebilirsiniz.

![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Sık karşılaşılan önyükleme hataları

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Bir işletim sistemi bulunamadı](https://support.microsoft.com/help/4010142)
- [Önyükleme hatası veya INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Azure portalını kullanarak oluşturulan bir sanal makinede tanılamayı etkinleştirme

Aşağıdaki yordam, Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulan bir sanal makine içindir.

**Yönetim** sekmesinde, **Izleme** bölümünde, **önyükleme Tanılamanın** açık olduğundan emin olun. **Tanılama depolama hesabı** açılır listesinden, tanılama dosyalarının yerleştirileceği bir depolama hesabı seçin.
 
![VM Oluştur](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> Önyükleme tanılaması özelliği Premium Depolama hesabını desteklemez. Önyükleme tanılaması için Premium depolama hesabı kullanıyorsanız, VM 'yi başlattığınızda StorageAccountTypeNotSupported hatasını alabilirsiniz.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Azure Resource Manager şablonundan dağıtma

Azure Resource Manager şablondan dağıtıyorsanız, sanal makine kaynağınız ' ne gidin ve tanılama profili bölümüne ekleyin. API sürüm üstbilgisini "2015-06-15" veya üzeri olarak ayarlayın. En son sürüm "2018-10-01" dir.

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

Tanılama profili, bu günlükleri yerleştirmek istediğiniz depolama hesabını seçmenize olanak sağlar.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Şablonları kullanarak kaynakları dağıtma hakkında daha fazla bilgi için bkz [. hızlı başlangıç: Azure portal](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)kullanarak Azure Resource Manager şablonları oluşturun ve dağıtın.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Mevcut sanal makinede önyükleme tanılamayı etkinleştir 

Mevcut bir sanal makinede önyükleme tanılamayı etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com)oturum açın ve sonra sanal makineyi seçin.
2. **Destek + sorun giderme** bölümünde **önyükleme tanılaması**' nı seçin, sonra **Ayarlar** sekmesini seçin.
3. **Önyükleme tanılaması** ayarları ' nda, durumu **Açık**olarak değiştirin ve **depolama hesabı** açılır listesinden bir depolama hesabı seçin. 
4. Değişikliği kaydedin.

    ![Mevcut VM’yi güncelleştirme](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

Değişikliğin etkili olması için sanal makineyi yeniden başlatmanız gerekir.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Azure CLı kullanarak önyükleme tanılamayı etkinleştirme

Azure CLı 'yi, var olan bir Azure sanal makinesinde önyükleme tanılamayı etkinleştirmek için kullanabilirsiniz. Daha fazla bilgi için bkz [. az VM Boot-](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest)Diagnostics.
