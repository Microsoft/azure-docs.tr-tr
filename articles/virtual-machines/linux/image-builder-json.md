---
title: Azure görüntü Oluşturucu şablonu oluşturma (Önizleme)
description: Azure Image Builder ile kullanmak üzere şablon oluşturmayı öğrenin.
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: 77460d1675b806e04c72e5f46da0ec4274d99d41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762542"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Önizleme: Azure görüntü Oluşturucu şablonu oluşturma 

Azure Image Builder, görüntü Oluşturucu hizmetine bilgi geçirmek için bir. JSON dosyası kullanır. Bu makalede, JSON dosyasının bölümlerine gidecektir, bu sayede kendinizinkini oluşturabilirsiniz. Tam. JSON dosyaları örneklerini görmek için bkz. [Azure görüntü Oluşturucu GitHub](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts).

Bu, temel şablon biçimidir:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Tür ve API sürümü

, `type` Olması gereken kaynak türüdür `"Microsoft.VirtualMachineImages/imageTemplates"` . `apiVersion`API değiştiğinde zaman içinde değişir, ancak `"2020-02-14"` Önizleme için olmalıdır.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Konum

Konum, özel görüntünün oluşturulacağı bölgedir. Image Builder önizlemesi için aşağıdaki bölgeler desteklenir:

- Doğu ABD
- Doğu ABD 2
- Orta Batı ABD
- Batı ABD
- Batı ABD 2
- Kuzey Avrupa
- West Europe


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Varsayılan olarak, görüntü Oluşturucu bir "Standard_D1_v2" derleme VM 'si kullanır, bunu geçersiz kılabilirsiniz; Örneğin, bir GPU VM 'si için bir görüntüyü özelleştirmek istiyorsanız, bir GPU VM boyutu gerekir. Bu isteğe bağlıdır.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Varsayılan olarak, görüntü Oluşturucu görüntünün boyutunu değiştirmez, kaynak görüntüden boyutu kullanır. **Yalnızca** Işletim sistemi diskinin boyutunu (Win ve Linux) artırabilirsiniz, bu isteğe bağlıdır ve 0 değeri kaynak görüntüyle aynı boyutu bırakır. İşletim sistemi diskinin boyutunu kaynak görüntüden daha küçük olacak şekilde azaltamezsiniz.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
VNET özellikleri belirtmezseniz, görüntü Oluşturucu kendi sanal ağ, genel IP ve NSG 'yi oluşturur. Genel IP, hizmet için derleme VM ile iletişim kurmak üzere kullanılır, ancak genel bir IP istemiyorsanız veya görüntü oluşturucunun yapılandırma sunucuları (DSC, Chef, Pupevcil hayvan, anormal), dosya paylaşımları vb. gibi var olan VNET kaynaklarınıza erişimi olmasını istiyorsanız, VNET belirtebilirsiniz. Daha fazla bilgi için, [ağ belgelerini](image-builder-networking.md)gözden geçirin, bu isteğe bağlıdır.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Etiketler

Bunlar, oluşturulan görüntü için belirtebileceğiniz anahtar/değer çiftleridir.

## <a name="depends-on-optional"></a>Bağımlı (isteğe bağlı)

Bu isteğe bağlı bölüm, devam etmeden önce bağımlılıkların tamamlandığından emin olmak için kullanılabilir. 

```json
    "dependsOn": [],
```

Daha fazla bilgi için bkz. [Kaynak bağımlılıklarını tanımlama](../../azure-resource-manager/templates/define-resource-dependency.md#dependson).

## <a name="identity"></a>Kimlik

Gerekli-görüntü Oluşturucu 'nun görüntü okuma/yazma izinlerine sahip olması için Azure Storage 'da betiklerin okunduğu bir Azure User-Assigned kimliği oluşturmanız gerekir. Görüntü Oluşturucu izinlerinin nasıl çalıştığı ve ilgili adımların ayrıntıları için lütfen [belgeleri](image-builder-user-assigned-identity.md)gözden geçirin.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


User-Assigned kimliği için görüntü Oluşturucu desteği:
* Yalnızca tek bir kimliği destekler
* Özel etki alanı adlarını desteklemez

Daha fazla bilgi edinmek için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../../active-directory/managed-identities-azure-resources/overview.md).
Bu özelliği dağıtma hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity).

## <a name="properties-source"></a>Özellikler: kaynak

`source`Bölümü, görüntü Oluşturucu tarafından kullanılacak kaynak görüntüyle ilgili bilgiler içerir. Image Builder Şu anda yalnızca yerel olarak Hyper-V oluşturma (Gen1) 1 görüntülerini Azure Paylaşılan görüntü Galerisi 'ne (SıG) veya yönetilen görüntüye oluşturmayı destekler. Gen2 görüntüleri oluşturmak istiyorsanız, bir kaynak Gen2 görüntüsü kullanmanız ve VHD 'ye dağıtmanız gerekir. Bundan sonra, VHD 'den yönetilen bir görüntü oluşturmanız ve bunu bir Gen2 görüntüsü olarak SıG 'a eklemeniz gerekecektir.

API, görüntü derlemesi için kaynağı tanımlayan bir ' SourceType ' gerektirir; şu anda üç tür vardır:
- Platformımage-kaynak görüntünün Market görüntüsü olduğunu gösterdi.
- Managedımage-normal yönetilen görüntüden itibaren bunu kullanın.
- Parça sürümü-bu, paylaşılan bir görüntü galerisinde kaynak olarak bir görüntü sürümü kullanırken kullanılır.


> [!NOTE]
> Var olan Windows özel görüntülerini kullanırken, Sysprep komutunu tek bir Windows görüntüsünde 8 kez çalıştırabilirsiniz. daha fazla bilgi için [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) belgelerine bakın.

### <a name="platformimage-source"></a>Platformımage kaynağı 
Azure Image Builder, Windows Server ve Client ve Linux Azure Marketi görüntülerini destekler, tam liste için [buraya](../image-builder-overview.md#os-support) bakın. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Buradaki özellikler, VM 'leri oluşturmak için kullanılan, AZ CLı kullanarak, özellikleri almak için aşağıdaki adımları çalıştırmıştır. 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Sürümünde ' en son ' seçeneğini kullanabilirsiniz. sürüm, şablon gönderildiğinde değil, görüntü oluşturma işlemi gerçekleştiğinde değerlendirilir. Bu işlevi paylaşılan görüntü Galerisi hedefi ile birlikte kullanıyorsanız, şablonu yeniden göndermeyi ve görüntü oluşturmayı yeniden çalıştırmayı önleyebilirsiniz, böylece görüntüleriniz en son görüntülerden yeniden oluşturulur.

#### <a name="support-for-market-place-plan-information"></a>Piyasa yeri planı bilgileri desteği
Plan bilgilerini de belirtebilirsiniz, örneğin:
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>Managedımage kaynağı

Kaynak görüntüyü genelleştirilmiş bir VHD veya VM 'nin mevcut bir yönetilen görüntüsü olarak ayarlar.

> [!NOTE]
> Kaynak yönetilen görüntü desteklenen bir işletim sistemi olmalıdır ve görüntünün Azure Image Builder şablonunuz ile aynı bölgeye sahip olması gerekir. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`Yönetilen görüntünün RESOURCEID olmalıdır. `az image list`Kullanılabilir görüntüleri listelemek için kullanın.


### <a name="sharedimageversion-source"></a>Parça sürümü kaynağı
Kaynak görüntüyü paylaşılan görüntü galerisinde var olan bir görüntü sürümü olarak ayarlar.

> [!NOTE]
> Kaynak yönetilen görüntü desteklenen bir işletim sistemi olmalıdır ve görüntünün Azure Image Builder şablonunuz ile aynı bölgeye sahip olması gerekir, aksi takdirde görüntü sürümünü görüntü Oluşturucu şablon bölgesine çoğaltın.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`Görüntü sürümünün RESOURCEID olmalıdır. Resim sürümlerini listelemek için [az Sig Image-Version List](/cli/azure/sig/image-version#az_sig_image_version_list) kullanın.


## <a name="properties-buildtimeoutinminutes"></a>Özellikler: Buildtimeoutınminutes

Varsayılan olarak, görüntü Oluşturucu 240 dakika boyunca çalışır. Bundan sonra, görüntü yapısının tamamlanıp tamamlanmayacağı, zaman aşımına uğrayacaktır ve durdurulur. Zaman aşımı isabet alıyorsa şuna benzer bir hata görürsünüz:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Buildtimeoutınminutes değeri belirtmez veya 0 olarak ayarlarsanız, bu varsayılan değeri kullanır. Değeri en fazla 960dakika (16sa) kadar artırabilir veya azaltabilirsiniz. Windows için, bu ayarı 60 dakika içinde ayarlamayı önermiyoruz. Zaman aşımına uğramış olduğunu fark ederseniz, özelleştirme adımının Kullanıcı girişi gibi bir şeyi beklediğini görmek için [günlükleri](image-builder-troubleshoot.md#customization-log)gözden geçirin. 

Özelleştirmelerin tamamlaması için daha fazla zaman gerektiğini fark ederseniz, bunu, ihtiyacınız olan ve küçük bir ek yük ile istediğiniz şekilde ayarlayın. Ancak bir hata görüntülemeden önce zaman aşımına uğramamasını beklemeniz gerekebilecek için çok yüksek bir ayarlama yapın. 


## <a name="properties-customize"></a>Özellikler: özelleştirme

Görüntü Oluşturucu çoklu ' özelleştiriciler ' destekler. Özelleştiriciler, resminizi özelleştirmek için kullanılan işlevlerdir (örneğin, betikleri çalıştırma veya sunucuları yeniden başlatma). 

Kullanırken `customize` : 
- Birden çok özelleştiriciler kullanabilirsiniz, ancak benzersiz olmalıdır `name` .
- Özelleştiriciler, şablonda belirtilen sırada yürütülür.
- Bir Özelleştirici başarısız olursa, tüm özelleştirme bileşeni başarısız olur ve bir hatayı geri bildirir.
- Bir şablonda kullanmadan önce betiği iyice sınamanız önemle tavsiye edilir. Kendi sanal makinenizde betik hata ayıklaması daha kolay olacaktır.
- Gizli verileri betiklerine yerleştirmeyin. 
- [MSI](./image-builder-user-assigned-identity.md)kullanmadığınız durumlar dışında, betik konumlarının herkese açık bir şekilde erişilebilir olması gerekir.

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
Customize bölümü bir dizidir. Azure Image Builder, özelleştiriciler aracılığıyla sıralı sırayla çalışacaktır. Herhangi bir Özelleştirici içindeki herhangi bir hata derleme işlemini başarısız olur. 

> [!NOTE]
> Satır içi komutlar, görüntü şablonu tanımında ve bir destek durumuyla birlikte yardım edildiğinde Microsoft Desteği görüntülenebilir. Hassas bilgileriniz varsa, erişimin kimlik doğrulaması gerektirdiği Azure Storage 'da betiklerine taşınması gerekir.
 
### <a name="shell-customizer"></a>Kabuk Özelleştirici

Kabuk Özelleştirici, kabuk betikleri çalıştırmayı destekler, bu, ıB 'nin bunlara erişebilmesi için herkese açık bir şekilde erişilebilir olması gerekir.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

İşletim sistemi desteği: Linux 
 
Özellikleri Özelleştir:

- **tür** – Shell 
- **ad** -özelleştirmeyi izlemek için ad 
- **Scripturi** -URI, dosyanın konumuna 
- noktalı virgülle ayrılmış kabuk komutlarının **satır içi** dizisi.
- **sha256Checksum** -dosyanın SHA256 sağlama toplamı değeri, bu yerel olarak oluşturulur ve ardından görüntü Oluşturucu sağlama toplamı ve doğrular.
    * Mac/Linux çalıştıran bir Terminal kullanarak sha256Checksum oluşturmak için: `sha256sum <fileName>`

> [!NOTE]
> Satır içi komutlar görüntü şablonu tanımının bir parçası olarak depolanır, görüntü tanımını dökümünü alırken bunları görebilirsiniz ve bunlar ayrıca sorun giderme amacıyla bir destek durumu olayında Microsoft Desteği görünür. Gizli komutlarınız veya değerleriniz varsa, bunların betiklerin içine taşınması ve Azure depolama 'da kimlik doğrulaması için bir kullanıcı kimliği kullanılması önemle önerilir.

#### <a name="super-user-privileges"></a>Süper kullanıcı ayrıcalıkları
Komutların süper kullanıcı ayrıcalıklarıyla çalışması için, bunların ön ekine sahip olmaları gerekir `sudo` , bunları betiklerine ekleyebilir veya örnek satır içi komutları kullanabilirsiniz, örneğin:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
ScriptUri kullanarak başvurkabilmeniz için sudo kullanan bir betik örneği:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Windows yeniden başlatma Özelleştirici 
Yeniden başlatma Özelleştirici, bir Windows sanal makinesini yeniden başlatmanızı ve yeniden çevrimiçi hale gelmesini bekleyebilir, bu sayede yeniden başlatma gerektiren yazılımları yükleyebilirsiniz.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

İşletim sistemi desteği: Windows
 
Özellikleri Özelleştir:
- **Yazın**: windowsrestart
- yeniden başlatma işlemini yürütmek için **restartCommand** komutu (isteğe bağlı). Varsayılan değer: `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – yeniden başlatmanın başarılı olup olmadığını denetlemek için komut (isteğe bağlı). 
- **restartTimeout** -yeniden başlatma zaman aşımı, bir büyüklük ve birim dizesi olarak belirtildi. Örneğin, `5m` (5 dakika) veya `2h` (2 saat). Varsayılan değer: ' 5dk '

### <a name="linux-restart"></a>Linux yeniden başlatma  
Linux yeniden başlatma Özelleştirici yoktur, ancak sürücüleri veya yeniden başlatma gerektiren bileşenleri yüklüyorsanız, yükleyebilir ve kabuk Özelleştirici kullanarak bir yeniden başlatma başlatabilirsiniz, derleme VM 'sine yönelik 20 dakikalık bir SSH zaman aşımı vardır.

### <a name="powershell-customizer"></a>PowerShell Özelleştirici 
Kabuk Özelleştirici PowerShell betikleri ve satır içi komutunu çalıştırmayı destekler, ıB 'nin bunlara erişebilmesi için betiklerin herkese açık bir şekilde erişilebilir olması gerekir.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

İşletim sistemi desteği: Windows ve Linux

Özellikleri Özelleştir:

- **tür** – PowerShell.
- **Scripturi** -URI, PowerShell betik dosyasının konumuna. 
- **satır içi** – çalıştırılacak olan satır içi komutlar, virgülle ayrılır.
- **Validexitcodes** : isteğe bağlı, betiğe/satır içi komuttan döndürülebilecek geçerli kodlar, bu, betik/satır içi komutunun bildirilen başarısızlığından kaçınacaktır.
- **runyükseltici** – isteğe bağlı, Boole, yükseltilmiş izinlerle komutları ve betikleri çalıştırmaya yönelik destek.
- **sha256Checksum** -dosyanın SHA256 sağlama toplamı değeri, bu yerel olarak oluşturulur ve ardından görüntü Oluşturucu sağlama toplamı ve doğrular.
    * Windows [Get-Hash](/powershell/module/microsoft.powershell.utility/get-filehash) üzerinde bir PowerShell kullanarak sha256Checksum oluşturmak için


### <a name="file-customizer"></a>Dosya Özelleştirici

Dosya Özelleştirici, görüntü oluşturucunun bir GitHub veya Azure depolamadan dosya indirmesini sağlar. Yapı yapıtlarına dayanan bir görüntü derleme işlem hattına sahipseniz, derleme paylaşımından dosya Özelleştirici 'yi indirmek için ayarlayabilir ve yapıtları görüntüye taşıyabilirsiniz.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

İşletim sistemi desteği: Linux ve Windows 

Dosya Özelleştirici özellikleri:

- **sourceURI** -erişilebilir bir depolama uç noktası, GitHub veya Azure depolama olabilir. Bir dizinin tamamını değil yalnızca bir dosyayı indirebilirsiniz. Bir dizin indirmeniz gerekiyorsa, sıkıştırılmış bir dosya kullanın, ardından Shell veya PowerShell özelleştiricileri kullanarak bu dosyayı açın. 

> [!NOTE]
> SourceURI bir Azure depolama hesabı ise, blob ortak olarak işaretlenmiş ise, blob üzerinde okuma erişimi için yönetilen Kullanıcı kimliği izinleri vermeniz gerekir. Depolama izinlerini ayarlamak için lütfen bu [örneğe](./image-builder-user-assigned-identity.md#create-a-resource-group) bakın.

- **hedef** : Bu, tam hedef yolu ve dosya adıdır. Başvurulan tüm yol ve alt dizinler var olmalıdır, bunu önceden ayarlamak için Shell veya PowerShell özelleştiricileri kullanın. Yolu oluşturmak için özelleştiriciler ' i kullanabilirsiniz. 

Bu, Windows dizinleri ve Linux yolları tarafından desteklenir, ancak bazı farklılıklar vardır: 
- Linux işletim sistemi – tek yol Image Builder,/t MP.
- Windows – yol kısıtlaması yok, ancak yol var olmalıdır.
 
 
Dosyayı indirmeye çalışırken bir hata varsa veya belirtilen bir dizine yerleştirirseniz, özelleştirme adımı başarısız olur ve bu, özelleştirme. log ' da olur.

> [!NOTE]
> Dosya Özelleştirici yalnızca küçük dosya indirmeleri için uygundur < 20 MB. Daha büyük dosya indirmeleri için bir betik veya satır içi komutu, Linux veya Windows gibi dosyaları indirmek için kullanılan kodu kullanın `wget` `curl` `Invoke-WebRequest` .

### <a name="windows-update-customizer"></a>Windows Update Özelleştirici
Bu Özelleştirici, Packer topluluğu tarafından tutulan açık kaynaklı bir proje olan Packer için [topluluk Windows Update hazırlayıcı](https://packer.io/docs/provisioners/community-supported.html) üzerine kurulmuştur. Microsoft, görüntü Oluşturucu hizmeti ile birlikte hizmeti sınar ve doğrular ve bunlarla ilgili sorunları araştırarak sorunları gidermeye çalışır, ancak açık kaynak proje Microsoft tarafından resmi olarak desteklenmez. Hakkında ayrıntılı belgeler ve Windows Update hazırlayıcı ile ilgili yardım için lütfen proje deposuna bakın.

```json
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS support: Windows
```

Özellikleri Özelleştir:
- **şunu yazın**  : windowsupdate.
- **SearchCriteria** -isteğe bağlı, hangi tür güncelleştirmelerin yüklü olduğunu tanımlar (önerilen, önemli vs.), BrowseOnly = 0 ve IsInstalled = 0 (önerilir) varsayılandır.
- **Filtreler** : isteğe bağlı, güncelleştirmeleri dahil etmek veya hariç tutmak için bir filtre belirtmenize izin verir.
- **Updatelimit** : isteğe bağlı, kaç güncelleştirme yüklenebileceğini tanımlar, varsayılan 1000.
 
> [!NOTE]
> Windows Update Özelleştirici, bekleyen bir Windows yeniden başlatmaları varsa veya uygulama yüklemeleri hala çalışıyorsa başarısız olabilir, genellikle bu hatayı özelleştirme. log ' da görebilirsiniz `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` . Bir Windows yeniden başlatmasına ekleme yapmayı ve/veya Windows Update çalıştırmadan önce iç satır komutlarında veya betiklerdeki [uyku](/powershell/module/microsoft.powershell.utility/start-sleep) veya bekleme komutlarını kullanarak yüklemelerin tamamlanmasını istediğiniz zaman izin vermenizi önemle tavsiye ederiz.

### <a name="generalize"></a>Genelleştir 
Varsayılan olarak, Azure Image Builder, görüntüyü ' genelleştirmek ' için her görüntü özelleştirme aşamasının sonunda ' deprovision ' kodunu da çalıştıracak. Genelleştirmek, görüntünün ayarlandığı bir işlemdir, bu sayede birden fazla VM oluşturmak için yeniden kullanılabilir. Windows VM 'Leri için Azure Image Builder, Sysprep kullanır. Linux için Azure görüntü Oluşturucu ' waagent-deprovision ' çalıştırır. 

Genelleştirilecek komutlar Image Builder kullanıcıları her durum için uygun olmayabilir, bu nedenle Azure Image Builder gerekirse bu komutu özelleştirmenize olanak tanır. 

Var olan özelleştirmeyi geçiriyorsanız ve farklı Sysprep/waagent komutları kullanıyorsanız, görüntü Oluşturucu genel komutlarını kullanabilir ve VM oluşturma işlemi başarısız olursa, kendi Sysprep veya waagent komutlarınızı kullanın.

Azure Image Builder başarıyla bir Windows özel görüntüsü oluşturursa ve bundan bir VM oluşturursanız, VM oluşturma işleminin başarısız olduğunu veya başarılı bir şekilde tamamlanmayacağını bulun, Windows Server Sysprep belgelerini gözden geçirmeniz veya Windows Server Sysprep müşteri hizmetleri destek ekibi ile bir destek isteği oluşturarak doğru Sysprep kullanımını giderebilen ve önermeniz gerekir.


#### <a name="default-sysprep-command"></a>Varsayılan Sysprep komutu
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>Varsayılan Linux sağlamayı kaldırma komutu

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Komutları geçersiz kılma
Komutları geçersiz kılmak için PowerShell veya Shell betik hazırlayıcılar ' ni kullanarak tam dosya adına sahip komut dosyalarını oluşturun ve bunları doğru dizinlere yerleştirin:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/deprovisioningscript.exe

Görüntü Oluşturucu bu komutları okuyacak, bunlar ' özelleştirme. log ' AıB günlüklerine yazılır. Bkz. günlüklerin nasıl toplanacağı hakkında [sorun giderme](image-builder-troubleshoot.md#customization-log) .
 
## <a name="properties-distribute"></a>Özellikler: dağıt

Azure görüntü Oluşturucu üç dağıtım hedefini destekler: 

- **Managedımage** tarafından yönetilen görüntü.
- **Sharedimage** -paylaşılan görüntü Galerisi.
- Depolama hesabında **VHD** VHD.

Aynı yapılandırma içindeki hedef türlerin her ikisine de bir görüntü dağıtabilirsiniz.

> [!NOTE]
> Varsayılan AıB Sysprep komutu "/Mode: VM" içermez, ancak bu, HyperV rolünün yüklü olduğu görüntüleri oluştururken gerekli olabilir. Bu komut bağımsız değişkenini eklemeniz gerekiyorsa, Sysprep komutunu geçersiz kılmanız gerekir.

' Ye dağıtım yapmak için birden fazla hedefe sahip olabilirsiniz, görüntü Oluşturucu, sorgulanarak erişilebilen her dağıtım hedefi için bir durum tutar `runOutputName` .  , `runOutputName` Bu dağıtım hakkında bilgi için gönderi dağıtımını sorgulayabilir. Örneğin, VHD konumunu veya görüntü sürümünün çoğaltılacağı bölgeleri veya SıG görüntü sürümü oluşturulmasını sorgulayabilirsiniz. Bu, her dağıtım hedefinin bir özelliğidir. `runOutputName`Her dağıtım hedefi için benzersiz olmalıdır. İşte bu, paylaşılan görüntü Galerisi dağıtımını sorgulmıştır:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

Çıkış:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Dağıt: Managedımage

Görüntü çıkışı yönetilen bir görüntü kaynağı olacaktır.

```json
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
```
 
Özellikleri dağıtma:
- **tür** – managedımage 
- **ImageID** – hedef görüntünün kaynak kimliği, beklenen biçim:/Subscriptions/ \<subscriptionId> /ResourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.COMPUTE/images/\<imageName>
- **konum** -yönetilen görüntünün konumu.  
- **Runoutputname** : dağıtımı tanımlamak için benzersiz ad.  
- **Artifacttags** -isteğe bağlı kullanıcı tarafından belirtilen anahtar değer çifti etiketleri.
 
 
> [!NOTE]
> Hedef kaynak grubu mevcut olmalıdır.
> Görüntünün farklı bir bölgeye dağıtılmasını istiyorsanız dağıtım süresini arttıracaktır. 

### <a name="distribute-sharedimage"></a>Dağıt: parça 
Azure Paylaşılan görüntü Galerisi, görüntü bölgesi çoğaltmasının yönetilmesine izin veren, özel görüntülerin sürümü oluşturma ve paylaşımı sağlayan yeni bir görüntü yönetim hizmetidir. Azure görüntü Oluşturucu bu hizmetle dağıtmayı destekler, böylece paylaşılan görüntü galerilerine göre desteklenen bölgelere görüntü dağıtabilirsiniz. 
 
Paylaşılan görüntü Galerisi şu şekilde yapılır: 
 
- Galeri-birden çok paylaşılan görüntü kapsayıcısı. Bir galeri tek bir bölgede dağıtılır.
- Görüntü tanımları-görüntüler için kavramsal gruplama. 
- Görüntü sürümleri-bu, VM veya ölçek kümesi dağıtmak için kullanılan bir görüntü türüdür. Görüntü sürümleri, VM 'Lerin dağıtılması gereken diğer bölgelere çoğaltılabilir.
 
Görüntü galerisine dağıtabilmeniz için önce bir galeri ve görüntü tanımı oluşturmanız gerekir, [paylaşılan görüntüler](../shared-images-cli.md)' e bakın. 

```json
{
    "type": "SharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Paylaşılan görüntü galerilerine yönelik özellikleri dağıtma:

- **tür** -sharedimage  
- **Gallerımageıd** – paylaşılan görüntü galerisinin kimliği, bu iki biçimde belirlenebilir:
    * Otomatik sürüm oluşturma-görüntü Oluşturucu sizin için bir monoton sürüm numarası oluşturacak, aynı şablondan görüntüleri yeniden oluşturmayı sürdürmek istediğinizde bu faydalıdır: biçim: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Açık sürüm oluşturma-Image Builder 'ın kullanmasını istediğiniz sürüm numarasını geçirebilirsiniz. Biçim: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **Runoutputname** : dağıtımı tanımlamak için benzersiz ad.  
- **Artifacttags** -isteğe bağlı kullanıcı tarafından belirtilen anahtar değer çifti etiketleri.
- **replicationregion** -çoğaltma için bölgelerin dizisi. Bölgelerden biri, galerinin dağıtıldığı bölge olmalıdır. Çoğaltma tamamlanana kadar, oluşturma işlemi tamamlanana kadar, bölge ekleme, derleme zamanının artışını ifade eder.
- **Excludefromlatest** (isteğe bağlı) Bu, oluşturduğunuz görüntü sürümünü SIG tanımında en son sürüm olarak işaretlemenizi sağlar, varsayılan değer ' false ' şeklindedir.
- **Storageaccounttype** (isteğe bağlı) AIB, oluşturulacak görüntü sürümü için bu depolama türlerini belirtmeyi destekler:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Görüntü şablonu ve başvurulan, `image definition` aynı konumda değilse, görüntü oluşturmak için ek zaman görürsünüz. Image Builder 'ın Şu anda `location` görüntü sürümü kaynağı için bir parametresi yok, bunu üst öğesinden aldık `image definition` . Örneğin, bir görüntü tanımı westus 'tadır ve görüntü sürümünün eastus 'e çoğaltılmasını istiyorsanız, bir blob, westus ' e kopyalanır, bu, westus içindeki bir görüntü sürümü kaynağı oluşturulur ve ardından eastus ' a çoğaltılır. Ek çoğaltma zamanından kaçınmak için, `image definition` ve görüntü şablonunun aynı konumda olduğundan emin olun.


### <a name="distribute-vhd"></a>Dağıtma: VHD  
Bir VHD 'ye çıkış yapabilirsiniz. Daha sonra VHD 'yi kopyalayabilir ve Azure Market 'Te yayımlamak için kullanabilir veya Azure Stack kullanabilirsiniz.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
İşletim sistemi desteği: Windows ve Linux

VHD parametrelerini dağıt:

- **tür** -VHD.
- **Runoutputname** : dağıtımı tanımlamak için benzersiz ad.  
- **Etiketler** -isteğe bağlı kullanıcı tarafından belirtilen anahtar değer çifti etiketleri.
 
Azure görüntü Oluşturucu, kullanıcının bir depolama hesabı konumu belirtilmesine izin vermez, ancak `runOutputs` konumunu almak için durumunu sorgulayabilirsiniz.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> VHD oluşturulduktan sonra, bunu farklı bir konuma (mümkün olan en kısa sürede) kopyalayın. VHD, görüntü şablonu Azure görüntü Oluşturucu hizmetine gönderildiğinde oluşturulan geçici kaynak grubundaki bir depolama hesabında depolanır. Görüntü şablonunu silerseniz, VHD 'yi kaybedersiniz. 

## <a name="image-template-operations"></a>Görüntü şablonu Işlemleri

### <a name="starting-an-image-build"></a>Görüntü derlemesi başlatılıyor
Bir derlemeyi başlatmak için, görüntü şablonu kaynağında ' Çalıştır ' öğesini çağırmanız gerekir, `run` komut örnekleri:

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>Görüntü derlemesini iptal etme
Yanlış olduğunu düşündüğünüz bir görüntü derlemesi çalıştırıyorsanız, Kullanıcı girişini bekliyor veya hiçbir zaman başarıyla tamamlanmayacağını düşünüyorsanız, derlemeyi iptal edebilirsiniz.

Derleme herhangi bir zamanda iptal edilebilir. Dağıtım aşaması başlatılmışsa yine de iptal edebilirsiniz, ancak tamamlanmamış görüntüleri temizlemeniz gerekir. İptal komutu iptal işleminin tamamlanmasını beklemez, lütfen `lastrunstatus.runstate` Bu durum [komutlarını](image-builder-troubleshoot.md#customization-log)kullanarak devam eden ilerlemeyi izleyin.


Komut örnekleri `cancel` :

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure görüntü Oluşturucu GitHub](https://github.com/azure/azvmimagebuilder)'da farklı senaryolar için Sample. JSON dosyaları vardır.