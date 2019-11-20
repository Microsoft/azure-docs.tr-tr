---
title: Azure 'da Linux VM 'lerde Özel betikler çalıştırma
description: Özel Betik uzantısı v2 'yi kullanarak Linux VM yapılandırma görevlerini otomatikleştirme
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: akjosh
ms.openlocfilehash: 87826b5bec4294ce45355ab0cfc4df373895563b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073221"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Linux sanal makineleri ile Azure Özel Betik uzantısı sürüm 2 kullanın
Özel Betik uzantısı sürüm 2, Azure sanal makinelerinde betikleri indirir ve çalıştırır. Bu uzantı, dağıtım sonrası yapılandırma, yazılım yükleme veya başka bir yapılandırma/yönetim görevi için yararlıdır. Azure depolama veya başka bir erişilebilir internet konumundan betikleri indirebilir veya onları uzantı çalışma zamanına verebilirsiniz. 

Özel Betik uzantısı Azure Resource Manager şablonlarıyla tümleştirilir. Azure CLı, PowerShell veya Azure sanal makinelerini REST API kullanarak da çalıştırabilirsiniz.

Bu makalede, Azure CLı 'deki özel betik uzantısının nasıl kullanılacağı ve bir Azure Resource Manager şablonu kullanılarak uzantının nasıl çalıştırılacağı açıklanır. Bu makale, Linux sistemleri için sorun giderme adımları da sağlar.


İki Linux özel Betik uzantısı vardır:
* Sürüm 1-Microsoft. OSTCExtensions. CustomScriptForLinux
* Sürüm 2-Microsoft. Azure. Extensions. CustomScript

Lütfen yeni ve mevcut dağıtımları yeni sürüm 2 kullanmak üzere değiştirin. Yeni sürümün öncekinin yerine bırakılması hedeflenmiştir. Dolayısıyla geçiş işlemi adı ve sürümü değiştirmek kadar kolaydır; uzantı yapılandırmanızı değiştirmeniz gerekmez.


### <a name="operating-system"></a>İşletim Sistemi

Linux için özel Betik uzantısı, uzantının desteklenen uzantısı işletim sisteminde çalışır, daha fazla bilgi için bu [makaleye](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)bakın.

### <a name="script-location"></a>Betik konumu

Azure Blob depolamaya erişmek için Azure Blob depolama kimlik bilgilerinizi kullanmak üzere uzantıyı kullanabilirsiniz. Alternatif olarak, betik konumu, sanal makinenin GitHub, dahili dosya sunucusu vb. gibi uç noktaya yönlendirilebileceği sürece herhangi bir yerde olabilir.

### <a name="internet-connectivity"></a>Internet bağlantısı
GitHub veya Azure depolama gibi dışarıdan bir betiği indirmeniz gerekiyorsa, ek güvenlik duvarı/ağ güvenlik grubu bağlantı noktalarının açılması gerekir. Örneğin, betiğinizin Azure Storage 'da bulunması halinde [depolama](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)için Azure NSG hizmet etiketlerini kullanarak erişime izin verebilirsiniz.

Betiğiniz yerel bir sunucu üzerinde ise, hala ek güvenlik duvarı/ağ güvenlik grubu bağlantı noktalarının açılması gerekir.

### <a name="tips-and-tricks"></a>İpuçları ve Püf Noktaları
* Bu uzantı için en çok karşılaşılan hatalar, betikteki söz dizimi hatalarıdır. Betiğin hatasız bir şekilde çalıştığından emin olun ve ayrıca hata noktalarını daha kolay bir şekilde belirlemek için betiğe ek günlük kaydı işlevleri ekleyin.
* Bir kez etkili betikler yazın. Bu sayede yanlışlıkla birden fazla çalıştırılan betikler, sistemde değişiklik gerçekleştirilmesine neden olmaz.
* Betiklerin çalıştıklarında Kullanıcı girişi gerektirmediğinden emin olun.
* Betiğin çalışması için izin verilen 90 dakika, daha uzun bir süre uzantının başarısız olmasına neden olur.
* Yeniden başlatmalar betiğin içine yerleştirmeyin, bu, yüklenmekte olan diğer uzantılarla ilgili sorunlara neden olur ve yeniden başlatma sonrası, uzantı yeniden başlatmadan sonra devam etmez. 
* Yeniden başlatmaya neden olacak bir betiğe sahipseniz, uygulamaları yükleyip komut dosyalarını çalıştır. Bir cron işi kullanarak ya da DSC veya Chef, Pupevcil hayvan uzantıları gibi araçları kullanarak yeniden başlatmayı zamanlamanız gerekir.
* Uzantı yalnızca bir kez betik çalıştırır, her önyüklemede bir betik çalıştırmak istiyorsanız, [Cloud-init görüntüsünü](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) kullanabilir ve [önyükleme modülü başına betikleri](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) kullanabilirsiniz. Alternatif olarak, komut dosyasını bir systemd hizmet birimi oluşturmak için de kullanabilirsiniz.
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
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Özellik değerleri

| Ad | Değer / örnek | Veri Türü | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publisher | Microsoft. COMPUTE. uzantıları | string |
| type | CustomScript | string |
| typeHandlerVersion | 2.0 | int |
| Dosya URI 'leri (ör.) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (ör.) | Python MyPythonScript.py \<My-Param1 > | string |
| betiğini çalıştırın | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix (ör.) | yanlış | boole |
| timestamp (örn.) | 123456789 | 32 bit tamsayı |
| storageAccountName (ör.) | örnek storageacct | string |
| storageAccountKey (ör.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Özellik değeri ayrıntıları
* `skipDos2Unix`: (isteğe bağlı, Boole) betik tabanlı dosya URL 'Leri veya betiği dos2unix dönüştürmeyi atlayın.
* `timestamp` (isteğe bağlı, 32-bit tamsayı) bu alanı yalnızca bu alanın değerini değiştirerek betiğin yeniden çalıştırılmasını tetiklemek için kullanın.  Herhangi bir tamsayı değeri kabul edilebilir; yalnızca önceki değerden farklı olmalıdır.
  * `commandToExecute`: (betik ayarlanmamışsa**gereklidir** , dize) ve yürütülecek giriş noktası betiği. Komutunuz parolalar gibi gizli dizileri içeriyorsa bunun yerine bu alanı kullanın.
* `script` **: (** commandToExecute ayarlanmadı, String)/bin/sh. tarafından yürütülen bir Base64 kodlamalı (ve isteğe bağlı olarak gziped) betiği
* `fileUris`: (isteğe bağlı, dize dizisi) indirilecek dosya (ler) in URL 'Leri.
* `storageAccountName`: (isteğe bağlı, dize) depolama hesabının adı. Depolama kimlik bilgilerini belirtirseniz, tüm `fileUris` Azure Blob 'larının URL 'Si olması gerekir.
* `storageAccountKey`: (isteğe bağlı, dize) depolama hesabının erişim anahtarı


Aşağıdaki değerler ortak veya korumalı ayarlarda ayarlanabilir, uzantı, aşağıdaki değerlerin hem genel hem de korumalı ayarlarda ayarlandığı tüm yapılandırmaları reddeder.
* `commandToExecute`
* `script`
* `fileUris`

Genel ayarların kullanılması hata ayıklama için yararlı olabilir, ancak korumalı ayarları kullanmanız önemle tavsiye edilir.

Ortak ayarlar, betiğin yürütüleceği sanal makineye şifresiz metin olarak gönderilir.  Korumalı ayarlar yalnızca Azure ve VM için bilinen bir anahtar kullanılarak şifrelenir. Ayarlar, gönderilen VM 'ye kaydedilir, yani ayarlar şifrelendiyse VM 'de şifreli olarak kaydedilir. Şifrelenmiş değerlerin şifresini çözmek için kullanılan sertifika VM 'de depolanır ve çalışma zamanında ayarların (gerekliyse) şifresini çözmek için kullanılır.

#### <a name="property-skipdos2unix"></a>Özellik: skipDos2Unix

Varsayılan değer false 'dur, bu da **dos2unix dönüştürmenin yürütüldüğü** anlamına gelir.

CustomScript 'in önceki sürümü olan Microsoft. OSTCExtensions. CustomScriptForLinux, `\r\n` `\n`olarak çevirerek DOS dosyalarını otomatik olarak UNIX dosyalarına dönüştürür. Bu çeviri hala mevcuttur ve varsayılan olarak açık olur. Bu dönüştürme, aşağıdaki ölçütlerden birine bağlı olarak, fileUris veya betik ayarından indirilen tüm dosyalara uygulanır.

* Uzantı `.sh`, `.txt`, `.py`veya `.pl` dönüştürülecektir. Betik ayarı,/bin/sh ile yürütülen bir betik olduğu varsayıcağından ve sanal makinede script.sh olarak kaydedildiği için her zaman bu ölçütlerle eşleşir.
* Dosya `#!`başlıyorsa.

Dos2unix dönüştürmesi, skipDos2Unix true olarak ayarlanarak atlanabilir.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Özellik: betik

CustomScript, Kullanıcı tanımlı bir betiğin yürütülmesini destekler. CommandToExecute ve fileUris öğelerini tek bir ayarda birleştirmek için betik ayarları. Azure depolama veya GitHub GİST 'den indirmek üzere bir dosya kurmak zorunda kalmak yerine, betiği bir ayar olarak kodlayabilirsiniz. Betik, commandToExecute ve fileUris ' i değiştirdi.

Betiğin Base64 kodlamalı olması **gerekir** .  Betik, **isteğe bağlı olarak** gziplenebilir. Komut dosyası ayarı, genel veya korumalı ayarlarda kullanılabilir. Betik parametresi verilerinin en büyük boyutu 256 KB 'dir. Betik bu boyutu aşarsa yürütülemeyecek.

Örneğin, aşağıdaki komut dosyası/script.sh/dosyasına kaydedildi.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Aşağıdaki komutun çıktısı alınarak doğru CustomScript betiği ayarı oluşturulur.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Bu komut dosyası, daha fazla boyut azaltmak için isteğe bağlı olarak gzipde olabilir (çoğu durumda). (CustomScript otomatik-gzip sıkıştırması kullanımını algılar.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript bir betiği yürütmek için aşağıdaki algoritmayı kullanır.

 1. onay betiği değerinin uzunluğu 256 KB 'yi aşamaz.
 1. Base64 dosyasının değerinin kodunu çözme
 1. Base64 kodu çözülen değeri gunzip 'e _dene_
 1. kodu çözülmüş (ve isteğe bağlı olarak açılan) değerini diske yaz (/var/lib/waagent/Custom-Script/#/Script.exe)
 1. _/bin/sh-c/var/lib/waagent/Custom-Script/#/Script.exe kullanarak betiği yürütün.


## <a name="template-deployment"></a>Şablon dağıtımı
Azure VM uzantıları Azure Resource Manager şablonları ile dağıtılabilir. Önceki bölümde ayrıntılı JSON şeması, bir Azure Resource Manager şablon dağıtımı sırasında Özel Betik uzantısı çalıştırmak için bir Azure Resource Manager şablonunda kullanılabilir. Özel Betik uzantısını içeren örnek bir şablon burada [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)'da bulunabilir.


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Bu özellik adları büyük/küçük harfe duyarlıdır. Dağıtım sorunlarından kaçınmak için, adları burada gösterildiği gibi kullanın.

## <a name="azure-cli"></a>Azure CLI
Özel Betik uzantısı 'nı çalıştırmak için Azure CLı kullanırken, bir yapılandırma dosyası veya dosyalar oluşturun. En azından ' commandToExecute ' olmalıdır.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

İsteğe bağlı olarak, komutta ayarları JSON biçimli dize olarak belirtebilirsiniz. Bu, yapılandırmanın yürütme sırasında ve ayrı bir yapılandırma dosyası olmadan belirtilmesini sağlar.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI örnekleri

#### <a name="public-configuration-with-script-file"></a>Betik dosyası ile ortak yapılandırma

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLı komutu:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Sorun giderme
Özel Betik uzantısı çalıştırıldığında, komut dosyası oluşturulur veya aşağıdaki örneğe benzer bir dizine indirilir. Komut çıktısı Ayrıca bu dizine `stdout` ve `stderr` dosyalarında kaydedilir.

```bash
/var/lib/waagent/custom-script/download/0/
```

Sorunu gidermek için, önce Linux aracı günlüğünü denetleyin, uzantının çalıştırıldığından emin olun, aşağıdakileri denetleyin:

```bash
/var/log/waagent.log 
```

Uzantı yürütmeyi aramanız gerekir, şöyle bir şey görünür:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Bazı noktalara işaret eder:
1. Etkinleştirme, komutun çalışmaya başladığı zaman olur.
2. İndirme, dosya URI 'lerinde belirtilen komut dosyalarını değil, Azure 'dan CustomScript uzantı paketinin indirileceği ile ilgilidir.


Azure Betik uzantısı, burada bulabileceğiniz bir günlük üretir:

```bash
/var/log/azure/custom-script/handler.log
```

Tek tek yürütmeye bakmanız gerekir, şöyle bir şey görünür:
```text
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
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
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Sonraki adımlar
Kodu, geçerli sorunları ve sürümleri görmek için bkz. [Özel Betik uzantısı-Linux deposu](https://github.com/Azure/custom-script-extension-linux).

