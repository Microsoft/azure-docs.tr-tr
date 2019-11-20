---
title: Azure 'da Linux VM 'lerde Özel betikler çalıştırma
description: Özel Betik uzantısı v1 kullanarak Linux VM yapılandırma görevlerini otomatikleştirme
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: b7dbabf5be8b1f223f6e39f294b9d7022b83c4f8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073177"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Linux sanal makineleri ile Azure Özel Betik uzantısı sürüm 1 ' i kullanın

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Özel Betik uzantısı sürüm 1, Azure sanal makinelerinde betikleri indirir ve çalıştırır. Bu uzantı, dağıtım sonrası yapılandırma, yazılım yükleme veya başka bir yapılandırma/yönetim görevi için yararlıdır. Azure depolama veya başka bir erişilebilir internet konumundan betikleri indirebilir veya onları uzantı çalışma zamanına verebilirsiniz.

Özel Betik uzantısı Azure Resource Manager şablonlarıyla tümleştirilir. Azure CLı, PowerShell, Azure portal veya Azure sanal makineleri REST API kullanarak da çalıştırabilirsiniz.

Bu makalede, Azure CLı 'deki özel betik uzantısının nasıl kullanılacağı ve bir Azure Resource Manager şablonu kullanılarak uzantının nasıl çalıştırılacağı açıklanır. Bu makale, Linux sistemleri için sorun giderme adımları da sağlar.

İki Linux özel Betik uzantısı vardır:

* Sürüm 1-Microsoft. OSTCExtensions. CustomScriptForLinux

* Sürüm 2-Microsoft. Azure. Extensions. CustomScript

Lütfen yeni ve mevcut dağıtımları yeni sürümü ([Microsoft. Azure. Extensions. CustomScript](custom-script-linux.md)) kullanmak üzere değiştirin. Yeni sürümün öncekinin yerine bırakılması hedeflenmiştir. Dolayısıyla geçiş işlemi adı ve sürümü değiştirmek kadar kolaydır; uzantı yapılandırmanızı değiştirmeniz gerekmez.

### <a name="operating-system"></a>İşletim Sistemi

Desteklenen Linux dağıtımları:

* CentOS 6,5 ve üzeri
* 8 ve üzeri
  * Deki 8,7, CustomScriptForLinux 'u kesen en son görüntülerde Python2 ile birlikte gelmez.
* FreeBSD
* OpenSUSE 13,1 ve üzeri
* Oracle Linux 6,4 ve üzeri
* SUSE Linux Enterprise Server 11 SP3 ve üzeri
* Ubuntu 12,04 ve üzeri

### <a name="script-location"></a>Betik konumu

Azure Blob depolamaya erişmek için Azure Blob depolama kimlik bilgilerinizi kullanmak üzere uzantıyı kullanabilirsiniz. Alternatif olarak, betik konumu, sanal makinenin GitHub, dahili dosya sunucusu vb. gibi uç noktaya yönlendirilebileceği sürece herhangi bir yerde olabilir.

### <a name="internet-connectivity"></a>Internet bağlantısı

GitHub veya Azure depolama gibi dışarıdan bir betiği indirmeniz gerekiyorsa, ek güvenlik duvarı/ağ güvenlik grubu bağlantı noktalarının açılması gerekir. Örneğin, betiğinizin Azure Storage 'da bulunması halinde [depolama](../../virtual-network/security-overview.md#service-tags)için Azure NSG hizmet etiketlerini kullanarak erişime izin verebilirsiniz.

Betiğiniz yerel bir sunucu üzerinde ise, hala ek güvenlik duvarı/ağ güvenlik grubu bağlantı noktalarının açılması gerekir.

### <a name="tips-and-tricks"></a>İpuçları ve Püf Noktaları

* Bu uzantı için en çok karşılaşılan hatalar, betikteki söz dizimi hatalarıdır. Betiğin hatasız bir şekilde çalıştığından emin olun ve ayrıca hata noktalarını daha kolay bir şekilde belirlemek için betiğe ek günlük kaydı işlevleri ekleyin.
* Bir kez etkili betikler yazın. Bu sayede yanlışlıkla birden fazla çalıştırılan betikler, sistemde değişiklik gerçekleştirilmesine neden olmaz.
* Betiklerin çalıştıklarında Kullanıcı girişi gerektirmediğinden emin olun.
* Betiğin çalışması için izin verilen 90 dakika, daha uzun bir süre uzantının başarısız olmasına neden olur.
* Yeniden başlatmalar betiğin içine yerleştirmeyin, bu, yüklenmekte olan diğer uzantılarla ilgili sorunlara neden olur ve yeniden başlatma sonrası, uzantı yeniden başlatmadan sonra devam etmez. 
* Yeniden başlatmaya neden olacak bir betiğe sahipseniz, uygulamaları yükleyip komut dosyalarını çalıştır. Bir cron işi kullanarak ya da DSC veya Chef, Pupevcil hayvan uzantıları gibi araçları kullanarak yeniden başlatmayı zamanlamanız gerekir.
* Uzantı yalnızca bir kez betik çalıştırır, her önyüklemede bir betik çalıştırmak istiyorsanız, [Cloud-init görüntüsünü](../linux/using-cloud-init.md) kullanabilir ve [önyükleme modülü başına betikleri](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) kullanabilirsiniz. Alternatif olarak, komut dosyasını bir systemd hizmet birimi oluşturmak için de kullanabilirsiniz.
* Bir betiğin ne zaman çalışacağını zamanlamak isterseniz, bir cron işi oluşturmak için uzantısını kullanmanız gerekir.
* Betik çalışırken Azure portalı veya CLI üzerinden uzantı durumunu yalnızca "geçiş durumunda" şeklinde görürsünüz. Çalışan bir betikte daha sık durum güncelleştirmeleri istiyorsanız kendi çözümünüzü oluşturmanız gerekir.
* Özel Betik uzantısı, ara sunucuları yerel olarak desteklemez, ancak komut dosyanız içinde, *kıvrımlı*gibi proxy sunucularını destekleyen bir dosya aktarım aracı kullanabilirsiniz.
* Betiklerinizin veya komutlarınızın Güvenebildiği varsayılan olmayan dizin konumlarından haberdar olun, bunu işlemek için mantığın.

## <a name="extension-schema"></a>Uzantı şeması

Özel Betik uzantısı yapılandırması, betik konumu ve çalıştırılacak komut gibi şeyleri belirtir. Bu yapılandırmayı yapılandırma dosyalarında saklayabilir, komut satırında belirtebilir veya bir Azure Resource Manager şablonunda belirtebilirsiniz. 

Gizli verileri, şifrelenen ve yalnızca sanal makine içinde şifresi çözülen korunan bir yapılandırmada saklayabilirsiniz. Korunan yapılandırma, yürütme komutu parola gibi gizli dizileri içerdiğinde yararlıdır.

Bu öğeler gizli veriler olarak değerlendirilmeli ve uzantılar korumalı ayar yapılandırmasında belirtilmelidir. Azure VM uzantısının korumalı ayarı veriler şifrelenir ve yalnızca hedef sanal makinede şifresi.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Özellik değerleri

| Ad | Değer / örnek | Veri Türü |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft. OSTCExtensions | string |
| type | CustomScriptForLinux | string |
| typeHandlerVersion | 1,5 | int |
| Dosya URI 'leri (ör.) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (ör.) | Python MyPythonScript.py \<My-Param1\> | string |
| Enableınternaldnscheck | true | boole |
| storageAccountName (ör.) | örnek storageacct | string |
| storageAccountKey (ör.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Özellik değeri ayrıntıları

* `fileUris`: (isteğe bağlı, dize dizisi) betiklerin URI listesi
* `enableInternalDNSCheck`: (isteğe bağlı, bool) varsayılan değeri, DNS denetimini devre dışı bırakmak için false olarak ayarlanır.
* `commandToExecute`: (isteğe bağlı, dize) yürütülecek giriş noktası betiği
* `storageAccountName`: (isteğe bağlı, dize) depolama hesabının adı
* `storageAccountKey`: (isteğe bağlı, dize) depolama hesabının erişim anahtarı

Aşağıdaki değerler ortak veya korumalı ayarlar ' da ayarlanabilir, hem genel hem de korumalı ayarlarda bu değerlerin ayarlanmış olması gerekir.

* `commandToExecute`

Genel ayarların kullanılması hata ayıklama için yararlı olabilir, ancak korumalı ayarları kullanmanız önemle tavsiye edilir.

Ortak ayarlar, betiğin yürütüleceği sanal makineye şifresiz metin olarak gönderilir.  Korumalı ayarlar yalnızca Azure ve VM için bilinen bir anahtar kullanılarak şifrelenir. Ayarlar, gönderilen VM 'ye kaydedilir, yani ayarlar şifrelendiyse VM 'de şifreli olarak kaydedilir. Şifrelenmiş değerlerin şifresini çözmek için kullanılan sertifika VM 'de depolanır ve çalışma zamanında ayarların (gerekliyse) şifresini çözmek için kullanılır.

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları Azure Resource Manager şablonları ile dağıtılabilir. Önceki bölümde ayrıntılı JSON şeması, bir Azure Resource Manager şablon dağıtımı sırasında Özel Betik uzantısı çalıştırmak için bir Azure Resource Manager şablonunda kullanılabilir.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>Bu özellik adları büyük/küçük harfe duyarlıdır. Dağıtım sorunlarından kaçınmak için, adları burada gösterildiği gibi kullanın.

## <a name="azure-cli"></a>Azure CLI

Özel Betik uzantısı 'nı çalıştırmak için Azure CLı kullanırken, bir yapılandırma dosyası veya dosyalar oluşturun. En azından ' commandToExecute ' olmalıdır.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

İsteğe bağlı olarak, komutta ayarları JSON biçimli dize olarak belirtebilirsiniz. Bu, yapılandırmanın yürütme sırasında ve ayrı bir yapılandırma dosyası olmadan belirtilmesini sağlar.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI örnekleri

#### <a name="public-configuration-with-no-script-file"></a>Betik dosyası olmayan ortak yapılandırma

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLı komutu:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Ortak ve korunan yapılandırma dosyaları

Betik dosyası URI 'sini belirtmek için bir ortak yapılandırma dosyası kullanın. Çalıştırılacak komutu belirtmek için korumalı bir yapılandırma dosyası kullanın.

Ortak yapılandırma dosyası:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Korumalı yapılandırma dosyası:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLı komutu:

```azurecli
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Sorun giderme

Özel Betik uzantısı çalıştırıldığında, komut dosyası oluşturulur veya aşağıdaki örneğe benzer bir dizine indirilir. Komut çıktısı Ayrıca bu dizine `stdout` ve `stderr` dosyalarında kaydedilir.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Sorunu gidermek için, önce Linux aracı günlüğünü denetleyin, uzantının çalıştırıldığından emin olun, aşağıdakileri denetleyin:

```bash
/var/log/waagent.log
```

Uzantı yürütmeyi aramanız gerekir, şöyle bir şey görünür:

```text
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

Bazı noktalara işaret eder:

1. Etkinleştirme, komutun çalışmaya başladığı zaman olur.
1. İndirme, dosya URI 'lerinde belirtilen komut dosyalarını değil, Azure 'dan CustomScript uzantı paketinin indirileceği ile ilgilidir.
1. Hangi günlük dosyasının yazıldığı `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log` de görebilirsiniz

Sonraki adım, günlük dosyasına göz atın ve şu biçimdedir:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Tek tek yürütmeye bakmanız gerekir, şöyle bir şey görünür:

```text
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
```

Burada şunları görebilirsiniz:

* Etkinleştir komutu başlangıç günlüğü
* Uzantıya geçirilen ayarlar
* Uzantı indirme dosyası ve sonucu.
* Çalıştırılan komut ve sonuç.

Ayrıca, Azure CLı kullanarak özel betik uzantısının yürütme durumunu alabilirsiniz:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Çıktı aşağıdaki metin gibi görünür:

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Sonraki adımlar

Kodu, geçerli sorunları ve sürümleri görmek için bkz. [CustomScript uzantı deposu](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
