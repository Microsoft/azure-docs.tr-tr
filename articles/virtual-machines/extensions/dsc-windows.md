---
title: Azure Istenen durum yapılandırma uzantısı Işleyicisi
description: DSC uzantısını kullanarak bir Azure VM 'de PowerShell DSC yapılandırmasını karşıya yükleme ve uygulama
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 592c731d1851ac36cf9b57864750df0603b6c3fd
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073798"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC Uzantısı

## <a name="overview"></a>Genel Bakış

Windows için PowerShell DSC Uzantısı Microsoft tarafından yayımlanır ve desteklenir. Uzantı, bir Azure VM 'de PowerShell DSC yapılandırmasını karşıya yükler ve uygular. DSC Uzantısı, VM 'de alınan DSC yapılandırmasını uygulamak için PowerShell DSC 'ye çağrı görür. Bu belgede, Windows için DSC sanal makine uzantısı için desteklenen platformlar, konfigürasyonlar ve dağıtım seçenekleri ayrıntılı olarak bulunmaktadır.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

DSC Uzantısı şu işletim sisteminin

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>İnternet bağlantısı

Windows için DSC Uzantısı, Azure dışında bir konumda depolanıyorsa hedef sanal makinenin Azure ile iletişim kurabildiğini ve yapılandırma paketinin (. zip dosyası) konumunu gerektirir. 

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON, bir Azure Resource Manager şablonunda DSC uzantısının Ayarlar bölümünün şemasını gösterir. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Özellik değerleri

| Ad | Değer / örnek | Veri Türü |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| publisher | Microsoft. PowerShell. DSC | string |
| type | DSC | string |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Ayarlar özellik değerleri

| Ad | Veri Türü | Açıklama
| ---- | ---- | ---- |
| Settings. wmfVersion | string | Sanal makinenize yüklenmesi gereken Windows Management Framework sürümünü belirtir. Bu özelliğin ' en son ' olarak ayarlanması, WMF 'nin en güncel sürümünü yükler. Bu özellik için geçerli olası değerler şunlardır. ' 4,0 ', ' 5,0 ' ve ' Latest '. Bu olası değerler güncelleştirmelere tabidir. Varsayılan değer ' Latest '. |
| Settings. Configuration. URL | string | DSC yapılandırma ZIP dosyanızın indirileceği URL konumunu belirtir. Belirtilen URL erişim için bir SAS belirteci gerektiriyorsa, protectedSettings. configurationUrlSasToken özelliğini SAS belirtecinizin değerine ayarlamanız gerekir. Settings. Configuration. Script ve/veya Settings. Configuration. Function tanımlanmışsa bu özellik gereklidir.
| Settings. Configuration. Script | string | DSC yapılandırmanızın tanımını içeren betiğin dosya adını belirtir. Bu betik, Configuration. URL özelliği tarafından belirtilen URL 'den indirilen ZIP dosyasının kök klasöründe olmalıdır. Settings. Configuration. URL ve/veya Settings. Configuration. Script tanımlanmışsa bu özellik gereklidir.
| Settings. Configuration. Function | string | DSC yapılandırmanızın adını belirtir. Adlı yapılandırma, Configuration. Script tarafından tanımlanan betikte bulunmalıdır. Settings. Configuration. URL ve/veya Settings. Configuration. Function tanımlanmışsa bu özellik gereklidir.
| Settings. configurationArguments | Koleksiyon | DSC yapılandırmanıza geçirmek istediğiniz parametreleri tanımlar. Bu özellik şifrelenmeyecektir.
| Settings. configurationData. URL | string | DSC yapılandırmanız için giriş olarak kullanılacak yapılandırma verileri (. pds1) dosyanızın indirileceği URL 'YI belirtir. Belirtilen URL erişim için bir SAS belirteci gerektiriyorsa, protectedSettings. configurationDataUrlSasToken özelliğini SAS belirtecinizin değerine ayarlamanız gerekir.
| Settings. privacy. dataEnabled | string | Telemetri toplamayı etkinleştirilir veya devre dışı bırakır. Bu özellik için olası tek değerler ' Enable ', ' Disable ', "veya $null. Bu özelliğin boş veya null bırakılması telemetri sağlayacak
| Settings. Advancedoçen. forcePullAndApply | Bool | Bu ayar, Azure Automation DSC ile düğümleri kaydetmek için uzantı ile çalışma deneyimini iyileştirmek üzere tasarlanmıştır.  Değer `$true`ise, uzantı başarı/başarısızlık döndürmeden önce yapılandırmanın ilk kez çalıştırılmasını bekler.  Değer $false olarak ayarlandıysa, uzantının döndürdüğü durum yalnızca düğümün Azure Otomasyonu durum yapılandırması 'na başarıyla kaydedilip kaydedilmediği ve düğüm yapılandırmasının kayıt sırasında çalıştırılmayacağı anlamına olur.
| Settings. Advancedoçen. downloadMappings | Koleksiyon | WMF ve .NET gibi bağımlılıkları indirmek için alternatif konumlar tanımlar

### <a name="protected-settings-property-values"></a>Korumalı ayarlar özellik değerleri

| Ad | Veri Türü | Açıklama
| ---- | ---- | ---- |
| protectedSettings. configurationArguments | string | DSC yapılandırmanıza geçirmek istediğiniz parametreleri tanımlar. Bu özellik şifrelenir. |
| protectedSettings.configurationUrlSasToken | string | Configuration. URL tarafından tanımlanan URL 'ye erişmek için SAS belirtecini belirtir. Bu özellik şifrelenir. |
| protectedSettings. configurationDataUrlSasToken | string | ConfigurationData. URL tarafından tanımlanan URL 'ye erişmek için SAS belirtecini belirtir. Bu özellik şifrelenir. |


## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları Azure Resource Manager şablonları ile dağıtılabilir.
Dağıtım sonrası yapılandırması gerektiren bir veya daha fazla sanal makine dağıtıldığında şablonlar idealdir.
Windows için DSC uzantısını içeren örnek bir Kaynak Yöneticisi şablonu [Azure hızlı başlangıç galerisinde](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91)bulunabilir.

## <a name="troubleshoot-and-support"></a>Sorun giderme ve Destek

### <a name="troubleshoot"></a>Sorun giderme

Uzantı dağıtım durumuyla ilgili veriler, Azure portalından ve Azure CLI kullanılarak alınabilir. Belirli bir VM'nin için uzantıları dağıtım durumunu görmek için Azure CLI kullanarak aşağıdaki komutu çalıştırın.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uzantı paketi indirildi ve Azure VM 'de bu konuma dağıtıldı
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Uzantı durum dosyası, her bir uzantı çalıştırması için ayrıntılı hata ve açıklama ile birlikte alt durum ve durum başarı/hata kodlarını içerir.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Uzantı çıkış günlükleri şu dizine kaydedilir:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Hata kodları ve anlamları

| Hata Kodu | Anlamı | Olası eylemi |
| :---: | --- | --- |
| 1000 | Genel hata | Bu hatanın iletisi, uzantı günlüklerinde belirli özel durum tarafından sağlanır |
| 52 | Uzantı yüklemesi hatası | Bu hatanın iletisi belirli özel durum tarafından sağlanır |
| 1002 | WMF yüklemesi hatası | WMF yüklenirken hata oluştu. |
| 1004 | Geçersiz zip paketi | Geçersiz zip; ZIP açılırken hata oluştu |
| 1100 | Bağımsız değişken hatası | Kullanıcı tarafından belirtilen girişte bir sorun olduğunu gösterir. Hatanın iletisi belirli özel durum tarafından sağlanır|


### <a name="support"></a>Destek

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/forums/). Alternatif olarak, bir Azure destek olayına dosya. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) ve Destek Al'ı seçin. Azure desteği hakkında daha fazla bilgi için okuma [Microsoft Azure desteği SSS](https://azure.microsoft.com/support/faq/).
