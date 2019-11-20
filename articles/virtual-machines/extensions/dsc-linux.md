---
title: Linux için Azure DSC Uzantısı
description: Azure Linux VM 'sinin Istenen durum yapılandırması kullanılarak yapılandırılmasını sağlamak için OMı ve DSC paketlerini yükleyerek.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 1825f9f0f5d525c0129341d800ca5949136ae633
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750080"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Linux için DSC Uzantısı (Microsoft. OSTCExtensions. DSCForLinux)

İstenen Durum Yapılandırması (DSC), BT ve geliştirme altyapınızı kod olarak yapılandırma ile yönetmenizi sağlayan bir yönetim platformudur.

> ! Not Linux için DSC Uzantısı ve [Linux Için Azure izleyici sanal makine uzantısı](/azure/virtual-machines/extensions/oms-linux) Şu anda bir çakışma var ve yan yana yapılandırmada desteklenmez.  Bu, iki çözümü aynı VM 'de birlikte kullanmamalısınız anlamına gelir.

DSCForLinux uzantısı Microsoft tarafından yayımlanır ve desteklenir. Uzantı, Azure sanal makinelerine OMı ve DSC aracısını yükleme. DSC Uzantısı aşağıdaki işlemleri de yapabilir


- Azure Otomasyonu hizmetinden (ExtensionAction 'ı Kaydet) yapılandırmaların çekme işlemini yapmak için Linux VM 'yi Azure Otomasyonu hesabına kaydedin
- MOF yapılandırmasını Linux VM 'ye gönderme (ExtensionAction 'ı Gönder)
- Düğüm yapılandırmasını çekmek için çekme sunucusunu yapılandırmak üzere Linux VM 'ye meta MOF yapılandırması uygulayın (ExtensionAction çekme)
- Linux VM 'ye özel DSC modülleri yükler (ExtensionAction 'ı Install)
- Özel DSC modüllerini Linux VM 'ye kaldırma (ExtensionAction 'ı Kaldır)

 

## <a name="prerequisites"></a>Ön koşullar

### <a name="operating-system"></a>İşletim sistemi

DSC Linux uzantısı, aşağıdakiler dışında [Azure 'da onaylı tüm Linux dağıtımlarını](/azure/virtual-machines/linux/endorsed-distros) destekler:

| Dağıtım | Sürüm |
|---|---|
| Debian | tüm sürümler |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>İnternet bağlantısı

DSCForLinux uzantısı, hedef sanal makinenin Internet 'e bağlı olmasını gerektirir. Örneğin, YAZMAÇ uzantısının Automation hizmetine bağlantısı olması gerekir. Çekme, çekme, çekme gibi diğer eylemler için Azure Storage/GitHub bağlantısı gerekir. Bu, müşteri tarafından sunulan ayarlara bağlıdır.

## <a name="extension-schema"></a>Uzantı şeması

### <a name="11-public-configuration"></a>1,1 ortak yapılandırma

Desteklenen tüm ortak yapılandırma parametreleri şunlardır:

* `FileUri`: (isteğe bağlı, dize) MOF dosyası/meta MOF dosyası/özel kaynak ZIP dosyasının URI 'si.
* `ResourceName`: (isteğe bağlı, dize) özel kaynak modülünün adı
* `ExtensionAction`: (isteğe bağlı, dize) bir uzantının ne yaptığını belirtir. geçerli değerler: Kaydet, gönder, Çek, yükleme, kaldır. Belirtilmemişse, varsayılan olarak gönderme eylemi olarak kabul edilir.
* `NodeConfigurationName`: (isteğe bağlı, dize) uygulanacak düğüm yapılandırmasının adı.
* `RefreshFrequencyMins`: (isteğe bağlı, int) DSC 'nin yapılandırmayı çekme sunucusundan edinmeye ne sıklıkta (dakika cinsinden) çalıştığını belirtir. 
       Çekme sunucusundaki yapılandırma hedef düğümdeki geçerli olandan farklıysa, bu, bekleyen depoya kopyalanır ve uygulanır.
* `ConfigurationMode`: (isteğe bağlı, dize) DSC 'nin yapılandırmayı nasıl uygulanacağını belirtir. Geçerli değerler: ApplyOnly, ApplyAndMonitor, Applyandadutocorrect.
* `ConfigurationModeFrequencyMins`: (isteğe bağlı, int), ne sıklıkta (dakika cinsinden) DSC 'nin yapılandırmanın istenen durumda olmasını sağlar.

> [!NOTE]
> 2,3 < sürümünü kullanıyorsanız, mode parametresi ExtensionAction ile aynı olur. Mod aşırı yüklenmiş bir terim gibi görünüyor. Bu nedenle, karışıklığın önüne geçmek için, 2,3 sürümden sonraki sürümlerde ExtensionAction kullanılır. Uzantı, geriye dönük uyumluluk için hem modu hem de ExtensionAction 'ı destekler. 
>

### <a name="12-protected-configuration"></a>1,2 korumalı yapılandırma

Desteklenen tüm korumalı yapılandırma parametreleri aşağıda verilmiştir:

* `StorageAccountName`: (isteğe bağlı, dize) dosyayı içeren depolama hesabının adı
* `StorageAccountKey`: (isteğe bağlı, dize) dosyayı içeren depolama hesabının anahtarı
* `RegistrationUrl`: (isteğe bağlı, dize) Azure Otomasyonu hesabının URL 'SI
* `RegistrationKey`: (isteğe bağlı, dize) Azure Otomasyonu hesabının erişim anahtarı


## <a name="scenarios"></a>Senaryolar

### <a name="register-to-azure-automation-account"></a>Azure Otomasyonu hesabına kaydolun
Protected. JSON
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public. JSON
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell biçimi
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>SANAL makineye bir MOF yapılandırma dosyası (Azure Storage hesabı 'nda) uygulama

Protected. JSON
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public. JSON
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell biçimi
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>SANAL makineye bir MOF yapılandırma dosyası (ortak depolamada) uygulama

public. JSON
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell biçimi
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>VM 'ye bir meta MOF yapılandırma dosyası (Azure Storage hesabı 'nda) uygulama

Protected. JSON
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public. JSON
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell biçimi
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>VM 'ye bir meta MOF yapılandırma dosyası (genel depolamada) uygulama
public. JSON
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
PowerShell biçimi
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>VM 'ye özel bir kaynak modülü (Azure Storage hesabı 'nda ZIP dosyası) yüklemek
Protected. JSON
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public. JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell biçimi
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>VM 'ye özel bir kaynak modülü (ortak depolamaya ZIP dosyası) yüklemek
public. JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell biçimi
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Özel bir kaynak modülünü VM 'den kaldırma
public. JSON
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
PowerShell biçimi
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları, Azure Resource Manager şablonlarıyla dağıtılabilir. Azure Otomasyonu 'na ekleme gibi dağıtım sonrası yapılandırma gerektiren bir veya daha fazla sanal makine dağıtıldığında şablonlar idealdir. 

Örnek Kaynak Yöneticisi şablonu [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) ve [201-DSC-Linux-genel-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu)olur.

Azure Resource Manager şablonu hakkında daha fazla ayrıntı için, [yazma Azure Resource Manager şablonları](../../azure-resource-manager/resource-group-authoring-templates.md)' nı ziyaret edin.


## <a name="azure-cli-deployment"></a>Azure CLı dağıtımı

### <a name="21-using-azure-cliazure-cli"></a>2,1. [**Azure CLI**] [Azure-CLI] kullanma
DSCForLinux uzantısını dağıtılmadan önce, Bölüm 3 ' teki farklı senaryolara göre `public.json` ve `protected.json`yapılandırmanız gerekir.

#### <a name="211-classic"></a>2.1.1. Klasik
Klasik moda Azure hizmet yönetimi modu da denir. Şunu çalıştırarak geçiş yapabilirsiniz:
```
$ azure config mode asm
```

Şu işlemi çalıştırarak DSCForLinux uzantısını dağıtabilirsiniz:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Kullanılabilir en son uzantı sürümünü öğrenmek için şunu çalıştırın:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>2.1.2 'yi. Resource Manager
Şunu çalıştırarak Azure Resource Manager moduna geçebilirsiniz:
```
$ azure config mode arm
```

Şu işlemi çalıştırarak DSCForLinux uzantısını dağıtabilirsiniz:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> Azure Resource Manager modunda `azure vm extension list` şimdilik kullanılamaz.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2,2. [**Azure PowerShell**] [Azure-PowerShell] kullanma

#### <a name="221-classic"></a>2.2.1 klasik

Aşağıdakileri çalıştırarak Azure hesabınızda (Azure hizmet yönetimi modu) oturum açabilirsiniz:

```powershell>
Add-AzureAccount
```

Ve şunu çalıştırarak DSCForLinux uzantısını dağıtın:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Yukarıdaki bölümde bulunan farklı senaryolara göre $privateConfig ve $publicConfig içeriğini değiştirmeniz gerekir 
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="222resource-manager"></a>2.2.2. Kaynak Yöneticisi

Aşağıdakileri çalıştırarak Azure hesabınızda (Azure Resource Manager modunda) oturum açabilirsiniz:

```powershell>
Login-AzAccount
```

Azure Resource Manager ile Azure PowerShell kullanma hakkında daha fazla bilgi edinmek için [**buraya**](../../azure-resource-manager/manage-resources-powershell.md) tıklayın.

Şu işlemi çalıştırarak DSCForLinux uzantısını dağıtabilirsiniz:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Yukarıdaki bölümde bulunan farklı senaryolara göre $privateConfig ve $publicConfig içeriğini değiştirmeniz gerekir 
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Sorun giderme ve destek

### <a name="troubleshoot"></a>Sorun giderme

Uzantı dağıtımlarının durumu hakkındaki veriler Azure portal ve Azure CLı kullanılarak alınabilir. Belirli bir VM için uzantıların dağıtım durumunu görmek için, Azure CLı 'yı kullanarak aşağıdaki komutu çalıştırın.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uzantı yürütme çıkışı aşağıdaki dosyaya kaydedilir:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Hata kodu: 51, desteklenmeyen ya da desteklenmeyen uzantı eylemini temsil eder.
Bazı durumlarda DSC Linux uzantısı, makinede OMı 'nın daha yüksek bir sürümü zaten mevcut olduğunda OMı 'yi yükleyemez. [hata yanıtı: (000003) Indirgeme izin verilmiyor]



### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/community/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve Destek Al ' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.

## <a name="next-steps"></a>Sonraki adımlar
Uzantılar hakkında daha fazla bilgi için bkz. [Linux Için sanal makine uzantıları ve özellikleri](features-linux.md).
