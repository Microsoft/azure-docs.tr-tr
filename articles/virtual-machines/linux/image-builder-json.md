---
title: Azure görüntü Oluşturucu şablonu oluşturma (Önizleme)
description: Azure Image Builder ile kullanmak üzere şablon oluşturmayı öğrenin.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 13392644ebe5e163e946deceeec5fcab8f5085cc
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159725"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Önizleme: Azure görüntü Oluşturucu şablonu oluşturma 

Azure Image Builder, görüntü Oluşturucu hizmetine bilgi geçirmek için bir. JSON dosyası kullanır. Bu makalede, JSON dosyasının bölümlerine gidecektir, bu sayede kendinizinkini oluşturabilirsiniz. Tam. JSON dosyaları örneklerini görmek için bkz. [Azure görüntü Oluşturucu GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Bu, temel şablon biçimidir:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Tür ve API sürümü

`type`, `"Microsoft.VirtualMachineImages/imageTemplates"`olması gereken kaynak türüdür. `apiVersion`, API değiştiğinde zaman içinde değişecektir, ancak önizleme için `"2019-05-01-preview"` olması gerekir.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Konum

Konum, özel görüntünün oluşturulacağı bölgedir. Image Builder önizlemesi için aşağıdaki bölgeler desteklenir:

- Doğu ABD
- Doğu ABD 2
- Orta Batı ABD
- Batı ABD
- Batı ABD 2


```json
    "location": "<region>",
```

## <a name="tags"></a>Etiketler

Bunlar, oluşturulan görüntü için belirtebileceğiniz anahtar/değer çiftleridir.

## <a name="depends-on-optional"></a>Bağımlı (isteğe bağlı)

Bu isteğe bağlı bölüm, devam etmeden önce bağımlılıkların tamamlandığından emin olmak için kullanılabilir. 

```json
    "dependsOn": [],
```

Daha fazla bilgi için bkz. [Kaynak bağımlılıklarını tanımlama](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Kimlik
Varsayılan olarak, görüntü Oluşturucu betikleri kullanmayı veya GitHub ve Azure depolama gibi birden çok konumdan dosya kopyalamayı destekler. Bunları kullanmak için herkese açık bir şekilde erişilebilir olmaları gerekir.

Azure depolama hesabında, kimliğe en az ' Depolama Blobu veri okuyucu ' değeri verildiği sürece, görüntü oluşturucunun Azure Storage 'a erişmesine izin vermek için, sizin tarafınızdan tanımlanan Azure Kullanıcı tarafından atanan yönetilen bir kimliği de kullanabilirsiniz. Bu, depolama bloblarını dışarıdan erişilebilir hale getirmeniz veya SAS belirteçleri oluşturmanız gerekmediği anlamına gelir.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Tam bir örnek için bkz. Azure [depolama 'da dosyalara erişmek Için Azure Kullanıcı tarafından atanan bir yönetilen kimlik kullanma](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Kullanıcı tarafından atanan bir kimlik için görüntü Oluşturucu desteği: • yalnızca tek bir kimliği destekler • özel etki alanı adlarını desteklemez

Daha fazla bilgi edinmek için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Bu özelliği dağıtma hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Özellikler: kaynak

`source` bölümü, görüntü Oluşturucu tarafından kullanılacak kaynak görüntüyle ilgili bilgiler içerir.

API, görüntü derlemesi için kaynağı tanımlayan bir ' SourceType ' gerektirir; şu anda üç tür vardır:
- ISO-kaynak bir RHEL ISO olduğunda bunu kullanın.
- Platformımage-kaynak görüntünün Market görüntüsü olduğunu gösterdi.
- Managedımage-normal yönetilen görüntüden itibaren bunu kullanın.
- Parça sürümü-bu, paylaşılan bir görüntü galerisinde kaynak olarak bir görüntü sürümü kullanırken kullanılır.

### <a name="iso-source"></a>ISO kaynağı

Azure Image Builder, önizleme için yalnızca yayınlanmış Red Hat Enterprise Linux 7. x Ikili DVD IOS kullanımını destekler. Görüntü Oluşturucu şunları destekler:
- RHEL 7,3 
- RHEL 7,4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

`sourceURI` ve `sha256Checksum` değerlerini almak için `https://access.redhat.com/downloads` ' e gidin, ardından ürün **Red Hat Enterprise Linux**ve desteklenen bir sürümü seçin. 

**Red Hat Enterprise Linux sunucuya yönelik yükleyiciler ve görüntüler**listesinde, Red Hat Enterprise Linux 7. x ikili DVD ve sağlama toplamı için bağlantıyı kopyalamanız gerekir.

> [!NOTE]
> Bağlantıların erişim belirteçleri sık aralıklarla yenilenir, bu nedenle bir şablonu her göndermek istediğinizde, RH bağlantı adresinin değişip değişmediğini denetlemeniz gerekir.
 
### <a name="platformimage-source"></a>Platformımage kaynağı 
Azure görüntü Oluşturucu aşağıdaki Azure Marketi görüntülerini destekler:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7,6
* CentOS 7,6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


Buradaki özellikler, VM 'leri oluşturmak için kullanılan, AZ CLı kullanarak, özellikleri almak için aşağıdaki adımları çalıştırmıştır. 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Sürüm ' Latest ' olamaz, sürüm numarası almak için yukarıdaki komutu kullanmanız gerekir. 

### <a name="managedimage-source"></a>Managedımage kaynağı

Kaynak görüntüyü genelleştirilmiş bir VHD veya VM 'nin mevcut bir yönetilen görüntüsü olarak ayarlar. Kaynak yönetilen görüntü desteklenen bir işletim sistemi olmalıdır ve Azure Image Builder şablonunuz ile aynı bölgede olmalıdır. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` yönetilen görüntünün RESOURCEID olmalıdır. Kullanılabilir görüntüleri listelemek için `az image list` kullanın.


### <a name="sharedimageversion-source"></a>Parça sürümü kaynağı
Kaynak görüntüyü paylaşılan görüntü galerisinde var olan bir görüntü sürümü olarak ayarlar. Görüntü sürümü desteklenen bir işletim sistemi olmalıdır ve görüntünün Azure Image Builder şablonunuz ile aynı bölgeye çoğaltılması gerekir. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`, görüntü sürümünün RESOURCEID olmalıdır. Resim sürümlerini listelemek için [az Sig Image-Version List](/cli/azure/sig/image-version#az-sig-image-version-list) kullanın.

## <a name="properties-buildtimeoutinminutes"></a>Özellikler: Buildtimeoutınminutes

Varsayılan olarak, görüntü Oluşturucu 240 dakika boyunca çalışır. Bundan sonra, görüntü yapısının tamamlanıp tamamlanmayacağı, zaman aşımına uğrayacaktır ve durdurulur. Zaman aşımı isabet alıyorsa şuna benzer bir hata görürsünüz:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Buildtimeoutınminutes değeri belirtmez veya 0 olarak ayarlarsanız varsayılan değer kullanılır. Değeri en fazla 960dakika (16sa) kadar artırabilir veya azaltabilirsiniz. Windows için, bu ayarı 60 dakika içinde ayarlamayı önermiyoruz. Zaman aşımına uğramış olduğunu fark ederseniz, özelleştirme adımının Kullanıcı girişi gibi bir şeyi beklediğini görmek için [günlükleri](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)gözden geçirin. 

Özelleştirmelerin tamamlaması için daha fazla zaman gerektiğini fark ederseniz, bunu, ihtiyacınız olan ve küçük bir ek yük ile istediğiniz şekilde ayarlayın. Ancak bir hata görüntülemeden önce zaman aşımına uğramamasını beklemeniz gerekebilecek için çok yüksek bir ayarlama yapın. 


## <a name="properties-customize"></a>Özellikler: özelleştirme

Görüntü Oluşturucu çoklu ' özelleştiriciler ' destekler. Özelleştiriciler, resminizi özelleştirmek için kullanılan işlevlerdir (örneğin, betikleri çalıştırma veya sunucuları yeniden başlatma). 

`customize`kullanırken: 
- Birden çok özelleştiriciler kullanabilirsiniz, ancak benzersiz bir `name`olmalıdır.
- Özelleştiriciler, şablonda belirtilen sırada yürütülür.
- Bir Özelleştirici başarısız olursa, tüm özelleştirme bileşeni başarısız olur ve bir hatayı geri bildirir.
- Bir şablonda kullanmadan önce betiği iyice sınamanız önemle tavsiye edilir. Kendi sanal makinenizde betik hata ayıklaması daha kolay olacaktır.
- Gizli verileri betiklerine yerleştirmeyin. 
- [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)kullanmadığınız durumlar dışında, betik konumlarının herkese açık bir şekilde erişilebilir olması gerekir.

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
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
 
 
### <a name="shell-customizer"></a>Kabuk Özelleştirici

Kabuk Özelleştirici, kabuk betikleri çalıştırmayı destekler, bu, ıB 'nin bunlara erişebilmesi için herkese açık bir şekilde erişilebilir olması gerekir.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
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
 
> [!NOTE]
> RHEL ISO kaynağı ile kabuk Özelleştirici çalıştırırken, özelleştirme gerçekleşmeden önce, ilk özelleştirme kabuğunuzun bir Red Hat yetkilendirme sunucusuyla kaydolduğunu güvence altına almanız gerekir. Özelleştirme tamamlandıktan sonra betiğin, yetkilendirme sunucusuyla kaydı yapılmalıdır.

### <a name="windows-restart-customizer"></a>Windows yeniden başlatma Özelleştirici 
Yeniden başlatma Özelleştirici, bir Windows sanal makinesini yeniden başlatmanızı ve yeniden çevrimiçi hale gelmesini bekleyebilir, bu sayede yeniden başlatma gerektiren yazılımları yükleyebilirsiniz.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

İşletim sistemi desteği: Windows
 
Özellikleri Özelleştir:
- **Yazın**: windowsrestart
- yeniden başlatma işlemini yürütmek için **restartCommand** komutu (isteğe bağlı). Varsayılan değer: `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – yeniden başlatmanın başarılı olup olmadığını denetlemek için komut (isteğe bağlı). 
- **restartTimeout** -yeniden başlatma zaman aşımı, bir büyüklük ve birim dizesi olarak belirtildi. Örneğin, `5m` (5 dakika) veya `2h` (2 saat). Varsayılan değer: ' 5dk '


### <a name="powershell-customizer"></a>PowerShell Özelleştirici 
Kabuk Özelleştirici PowerShell betikleri ve satır içi komutunu çalıştırmayı destekler, ıB 'nin bunlara erişebilmesi için betiklerin herkese açık bir şekilde erişilebilir olması gerekir.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

İşletim sistemi desteği: Windows ve Linux

Özellikleri Özelleştir:

- **tür** – PowerShell.
- **Scripturi** -URI, PowerShell betik dosyasının konumuna. 
- **satır içi** – çalıştırılacak olan satır içi komutlar, virgülle ayrılır.
- **valid_exit_codes** : isteğe bağlı, betiğe/satır içi komuttan döndürülebilecek geçerli kodlar, bu komut dosyası/satır içi komutun bildirilen başarısızlığından kaçınacaktır.

### <a name="file-customizer"></a>Dosya Özelleştirici

Dosya Özelleştirici, görüntü oluşturucunun bir GitHub veya Azure depolamadan dosya indirmesini sağlar. Yapı yapıtlarına dayanan bir görüntü derleme işlem hattına sahipseniz, derleme paylaşımından dosya Özelleştirici 'yi indirmek için ayarlayabilir ve yapıtları görüntüye taşıyabilirsiniz.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

İşletim sistemi desteği: Linux ve Windows 

Dosya Özelleştirici özellikleri:

- **sourceURI** -erişilebilir bir depolama uç noktası, GitHub veya Azure depolama olabilir. Bir dizinin tamamını değil yalnızca bir dosyayı indirebilirsiniz. Bir dizin indirmeniz gerekiyorsa, sıkıştırılmış bir dosya kullanın, ardından Shell veya PowerShell özelleştiricileri kullanarak bu dosyayı açın. 
- **hedef** : Bu, tam hedef yolu ve dosya adıdır. Başvurulan tüm yol ve alt dizinler var olmalıdır, bunu önceden ayarlamak için Shell veya PowerShell özelleştiricileri kullanın. Yolu oluşturmak için özelleştiriciler ' i kullanabilirsiniz. 

Bu, Windows dizinleri ve Linux yolları tarafından desteklenir, ancak bazı farklılıklar vardır: 
- Linux işletim sistemi – tek yol Image Builder,/t MP.
- Windows – yol kısıtlaması yok, ancak yol var olmalıdır.
 
 
Dosyayı indirmeye çalışırken bir hata varsa veya belirtilen bir dizine yerleştirirseniz, özelleştirme adımı başarısız olur ve bu, özelleştirme. log ' da olur.

> [!NOTE]
> Dosya Özelleştirici yalnızca küçük dosya indirmeleri için uygundur < 20 MB. Daha büyük dosya indirmeleri için bir betik veya satır içi komutu, Linux `wget` veya `curl`, Windows `Invoke-WebRequest`gibi dosyaları indirmek için kullanılan kodu kullanın.

Dosya Özelleştirici içindeki dosyalar, [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)kullanılarak Azure Storage 'dan indirilebilir.

### <a name="generalize"></a>Genelleştir 
Varsayılan olarak, Azure Image Builder, görüntüyü ' genelleştirmek ' için her görüntü özelleştirme aşamasının sonunda ' deprovision ' kodunu da çalıştıracak. Genelleştirmek, görüntünün ayarlandığı bir işlemdir, bu sayede birden fazla VM oluşturmak için yeniden kullanılabilir. Windows VM 'Leri için Azure Image Builder, Sysprep kullanır. Linux için Azure görüntü Oluşturucu ' waagent-deprovision ' çalıştırır. 

Genelleştirilecek komutlar Image Builder kullanıcıları her durum için uygun olmayabilir, bu nedenle Azure Image Builder gerekirse bu komutu özelleştirmenize olanak tanır. 

Mevcut özelleştirmeyi geçiriyorsanız ve farklı Sysprep/waagent komutları kullanıyorsanız, görüntü Oluşturucu genel komutlarını kullanabilir ve VM oluşturma işlemi başarısız olursa, kendi Sysprep veya waagent komutlarınızı kullanın.

Azure Image Builder başarıyla bir Windows özel görüntüsü oluşturursa ve bundan bir VM oluşturursanız, VM oluşturma işleminin başarısız olduğunu veya başarıyla tamamlanmayacağını bulun, Windows Server Sysprep belgelerini gözden geçirmeniz veya bir destek isteği oluşturmanız gerekir Windows Server Sysprep müşteri hizmetleri destek ekibi, kimler sorun giderebilir ve doğru Sysprep kullanımını tavsiye edebilir.


#### <a name="default-sysprep-command"></a>Varsayılan Sysprep komutu
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Varsayılan Linux sağlamayı kaldırma komutu

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Komutları geçersiz kılma
Komutları geçersiz kılmak için PowerShell veya Shell betik hazırlayıcılar ' ni kullanarak tam dosya adına sahip komut dosyalarını oluşturun ve bunları doğru dizinlere yerleştirin:

* Windows: C:\deprovisioningscript.exe. ps1
* Linux:/tmp/deprovisioningscript.exe

Görüntü Oluşturucu bu komutları okuyacak, bunlar ' özelleştirme. log ' AıB günlüklerine yazılır. Bkz. günlüklerin nasıl toplanacağı hakkında [sorun giderme](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) .
 
## <a name="properties-distribute"></a>Özellikler: dağıt

Azure görüntü Oluşturucu üç dağıtım hedefini destekler: 

- **Managedımage** tarafından yönetilen görüntü.
- **Sharedimage** -paylaşılan görüntü Galerisi.
- Depolama hesabında **VHD** VHD.

Aynı yapılandırmadaki her iki hedef türüne bir görüntü dağıtabilirsiniz, lütfen [örneklere](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)bakın.

' Ye dağıtım için birden fazla hedef olabileceğinden, görüntü Oluşturucu `runOutputName`sorgulayarak erişilebilen her dağıtım hedefi için bir durum tutar.  `runOutputName`, bu dağıtım hakkında daha fazla bilgi için gönderi dağıtımını Sorgulayabileceğiniz bir nesnedir. Örneğin, VHD konumunu veya görüntü sürümünün çoğaltılacağı bölgeleri sorgulayabilirsiniz. Bu, her dağıtım hedefinin bir özelliğidir. `runOutputName` her dağıtım hedefi için benzersiz olmalıdır.
 
### <a name="distribute-managedimage"></a>Dağıt: Managedımage

Görüntü çıkışı yönetilen bir görüntü kaynağı olacaktır.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Özellikleri dağıtma:
- **tür** – managedımage 
- **ImageID** – hedef görüntünün kaynak kimliği; beklenen biçim:/Subscriptions/\<SubscriptionID >/resourceGroups/\<destinationResourceGroupName >/Providers/Microsoft.COMPUTE/images/\<görüntüadı >
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
 
Görüntü galerisine dağıtabilmeniz için önce bir galeri ve görüntü tanımı oluşturmanız gerekir, [paylaşılan görüntüler](shared-images.md)' e bakın. 

```json
{
    "type": "sharedImage",
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
- **Gallerımageıd** : paylaşılan görüntü galerisinin kimliği. Biçim:/Subscriptions/\<SubscriptionID >/resourceGroups/\<resourceGroupName >/providers/Microsoft.Compute/galleries/\<Sharedimagegallername >/images/\<ımagegallername >.
- **Runoutputname** : dağıtımı tanımlamak için benzersiz ad.  
- **Artifacttags** -isteğe bağlı kullanıcı tarafından belirtilen anahtar değer çifti etiketleri.
- **replicationregion** -çoğaltma için bölgelerin dizisi. Bölgelerden biri, galerinin dağıtıldığı bölge olmalıdır.
 
> [!NOTE]
> Azure Image Builder 'ı galeriye farklı bir bölgede kullanabilirsiniz, ancak Azure Image Builder hizmetinin görüntüyü veri merkezleri arasında aktarması gerekir ve bu daha uzun sürer. Image Builder, bir monoton tamsayıya göre otomatik olarak görüntü sürümüne sahip olur, bunu şu anda belirtemezsiniz. 

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
 
Azure Image Builder, kullanıcının bir depolama hesabı konumu belirtilmesine izin vermez, ancak konumu almak için `runOutputs` durumunu sorgulayabilirsiniz.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> VHD oluşturulduktan sonra, bunu farklı bir konuma (mümkün olan en kısa sürede) kopyalayın. VHD, görüntü şablonu Azure görüntü Oluşturucu hizmetine gönderildiğinde oluşturulan geçici kaynak grubundaki bir depolama hesabında depolanır. Görüntü şablonunu silerseniz, VHD 'yi kaybedersiniz. 
 
## <a name="next-steps"></a>Sonraki adımlar

[Azure görüntü Oluşturucu GitHub](https://github.com/danielsollondon/azvmimagebuilder)'da farklı senaryolar için Sample. JSON dosyaları vardır.
 
 
 
 
 
 
 
 
 
 
