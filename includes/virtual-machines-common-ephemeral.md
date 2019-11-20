---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: c3a7fb14dbd22730d95a5aaed146b59ad790ce6b
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775904"
---
Geçici işletim sistemi diskleri, yerel sanal makine (VM) depolama alanı üzerinde oluşturulur ve uzak Azure depolama 'ya kaydedilmez. Kısa ömürlü işletim sistemi diskleri, uygulamaların tek tek VM hatalarından dayanıklı olduğu, ancak VM dağıtım zamanından daha fazla etkilediği veya tek tek sanal makine örneklerinin yeniden oluşturulduğu durum bilgisiz iş yükleri için iyi çalışır. Kısa ömürlü IŞLETIM sistemi diski ile işletim sistemi diskine daha hızlı okuma/yazma gecikme süresi ve daha hızlı VM yeniden görüntüsü alırsınız. 
 
Kısa ömürlü disklerin temel özellikleri şunlardır: 
- Durum bilgisi olmayan uygulamalar için idealdir.
- Bunlar hem Market hem de özel görüntülerle kullanılabilir.
- VM 'Leri ve ölçek kümesi örneklerini orijinal önyükleme durumuna hızla sıfırlama veya yeniden görüntü oluşturma yeteneği.  
- Geçici bir diske benzer şekilde daha düşük gecikme süresi. 
- Kısa ömürlü işletim sistemi diskleri ücretsizdir, işletim sistemi diski için depolama maliyeti yoktur.
- Tüm Azure bölgelerinde kullanılabilir. 
- Kısa ömürlü işletim sistemi diski [paylaşılan görüntü Galerisi](/azure/virtual-machines/linux/shared-image-galleries)tarafından desteklenir. 
 

 
Kalıcı ve kısa ömürlü işletim sistemi diskleri arasındaki önemli farklılıklar:

|                             | Kalıcı işletim sistemi diski                          | Kısa Ömürlü İşletim Sistemi Diski                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| İşletim sistemi diski için boyut sınırı      | 2 TiB                                                                                        | VM boyutu için önbellek boyutu veya 2TiB, hangisi daha küçüktür. **GiB 'deki önbellek boyutu**için bkz. [DS](../articles/virtual-machines/linux/sizes-general.md), [es](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md)ve [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Desteklenen VM boyutları          | Tümü                                                                                          | DSv1, DSv2, DSv3, Esv3, FS, FsV2, GS, ı                                               |
| Disk türü desteği           | Yönetilen ve yönetilmeyen işletim sistemi diski                                                                | Yalnızca yönetilen işletim sistemi diski                                                               |
| Bölge desteği              | Tüm bölgeler                                                                                  | Tüm bölgeler                              |
| Veri kalıcılığı            | IŞLETIM sistemi diskine yazılan işletim sistemi diski verileri Azure depolama 'da depolanıyor                                  | İşletim sistemi diskine yazılan veriler yerel VM depolama alanında depolanır ve Azure Storage 'da kalıcı değildir. |
| Serbest bırakılmış durumu durdur      | VM 'Ler ve ölçek kümesi örnekleri, durdurma-serbest bırakıldı durumundan durdurulup yeniden başlatılabilir | VM 'Ler ve ölçek kümesi örnekleri durdurulamıyor-serbest bırakılamaz                                  |
| Özelleştirilmiş işletim sistemi diski desteği | Evet                                                                                          | Hayır                                                                                 |
| İşletim sistemi diski yeniden boyutlandırma              | VM oluşturma sırasında ve VM durdurulduktan sonra desteklenir-serbest bırakıldı                                | Yalnızca VM oluşturma sırasında desteklenir                                                  |
| Yeni bir VM boyutuna yeniden boyutlandırma   | İşletim sistemi diski verileri korunur                                                                    | İşletim sistemi diskindeki veriler silinir, işletim sistemi yeniden sağlandı                                      |

## <a name="size-requirements"></a>Boyut gereksinimleri

VM ve örnek görüntülerini VM önbelleğinin boyutuna kadar dağıtabilirsiniz. Örneğin, marketten standart Windows Server görüntüleri 127 GiB hakkında, bu da 127 GiB 'den daha büyük bir önbelleğe sahip bir VM boyutuna ihtiyacınız olduğu anlamına gelir. Bu durumda [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) , yeterince büyük olmayan 86 gib önbellek boyutuna sahiptir. Standard_DS3_v2, 172 GiB önbellek boyutuna sahiptir ve bu değer yeterince büyük olur. Bu durumda, Standard_DS3_v2, bu görüntüyle kullanabileceğiniz DSv2 serisinde en küçük boyutdir. Market ve Windows Server görüntülerinde, tarafından `[smallsize]` belirtilen temel Linux görüntüleri 30 gib etrafında olur ve kullanılabilir VM boyutlarının çoğunu kullanabilir.

Kısa ömürlü diskler Ayrıca, VM boyutunun Premium depolamayı desteklemesini gerektirir. Boyutlar genellikle (her zaman değil), DSv2 ve `s` EsV3 gibi bir ada sahiptir. Daha fazla bilgi için bkz. [Azure VM boyutları](../articles/virtual-machines/linux/sizes.md) , hangi boyutlarda Premium depolamayı destekledikleri hakkındaki ayrıntılar için.

## <a name="powershell"></a>PowerShell

PowerShell VM dağıtımı için kısa ömürlü bir disk kullanmak istiyorsanız, VM yapılandırmanızda [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) ' i kullanın. ' İ ve '`-Caching` olarak ayarlayın .`ReadOnly` `-DiffDiskSetting` `Local`     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Ölçek kümesi dağıtımları için, yapılandırmanızda [set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet 'ini kullanın. ' İ ve '`-Caching` olarak ayarlayın .`ReadOnly` `-DiffDiskSetting` `Local`


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

`--ephemeral-os-disk` CLI VM dağıtımı için kısa ömürlü bir disk kullanmak istiyorsanız, [az](/cli/azure/vm#az-vm-create) `true` VM Create olarak parametresini ve `--os-disk-caching` parametresini olarak `ReadOnly`ayarlayın.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Ölçek Kümeleri için, `--ephemeral-os-disk true` [az-VMSS-Create](/cli/azure/vmss#az-vmss-create) parametresini kullanın ve `--os-disk-caching` parametresini olarak `ReadOnly`ayarlayın.

## <a name="portal"></a>Portal   

Azure portal, **diskler** sekmesinin **Gelişmiş** bölümünü açarak bir VM dağıtımı sırasında kısa ömürlü diskler kullanmayı seçebilirsiniz. **Kısa ömürlü işletim sistemi diski kullan** için **Evet**' i seçin.

![Kısa ömürlü işletim sistemi diski kullanmayı seçme için radyo düğmesini gösteren ekran görüntüsü](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Kısa ömürlü disk kullanma seçeneği gri ise, işletim sistemi görüntüsünden daha büyük bir önbellek boyutuna sahip olmayan veya Premium depolamayı desteklemeyen bir VM boyutu seçmiş olabilirsiniz. **Temel bilgiler** sayfasına dönün ve başka bir VM boyutu seçmeyi deneyin.

Ayrıca, Portal 'ı kullanarak kısa ömürlü işletim sistemi diskleri ile ölçek kümeleri de oluşturabilirsiniz. Yeterli büyüklükte bir önbellek boyutuna sahip bir VM boyutu seçtiğinizden emin olun ve ardından **kısa ömürlü işletim sistemi diski kullanın** ' de **Evet**' i seçin.

![Ölçek kümesi için kısa ömürlü bir işletim sistemi diski kullanmayı seçme radyo düğmesini gösteren ekran görüntüsü](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Ölçek kümesi şablonu dağıtımı  
Kısa ömürlü işletim sistemi diski kullanan bir ölçek kümesi oluşturma işlemi, `diffDiskSettings` özelliği `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` şablondaki kaynak türüne eklemektir. Ayrıca, önbelleğe alma ilkesi, kısa ömürlü işletim `ReadOnly` sistemi diski için olarak ayarlanmalıdır. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>VM şablonu dağıtımı 
Bir şablonu kullanarak, kısa ömürlü işletim sistemi diski ile bir VM dağıtabilirsiniz. Kısa ömürlü işletim sistemi diskleri kullanan bir VM oluşturma işlemi, `diffDiskSettings` özelliği şablondaki Microsoft. COMPUTE/virtualmachines kaynak türüne eklemektir. Ayrıca, önbelleğe alma ilkesi, kısa ömürlü işletim `ReadOnly` sistemi diski için olarak ayarlanmalıdır. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>REST kullanarak VM 'yi yeniden görüntü oluşturma
Bir sanal makine örneğini, aşağıda açıklandığı gibi REST API ve Azure Portal aracılığıyla VM 'nin genel bakış bölmesine giderek kullanarak kısa ömürlü işletim sistemi diski ile yeniden görüntüleyebilirsiniz. Ölçek Kümeleri için yeniden görüntüleme, PowerShell, CLı ve Portal aracılığıyla zaten kullanılabilir.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S: Yerel işletim sistemi disklerinin boyutu nedir?**

Y: İşletim sistemi diskine yönelik tüm okuma/yazma işlemleri, sanal makineyle aynı düğümde yerel olacak şekilde, platform ve özel görüntüleri destekliyoruz. 

**S: Kısa ömürlü işletim sistemi diski yeniden boyutlandırılabilir mi?**

Y: Hayır, kısa ömürlü işletim sistemi diski sağlandıktan sonra, işletim sistemi diski yeniden boyutlandırılamaz. 

**S: Bir yönetilen diskleri, kısa ömürlü bir VM 'ye ekleyebilir miyim?**

Y: Evet, bir yönetilen veri diskini, kısa ömürlü bir işletim sistemi diski kullanan bir VM 'ye iliştirebilirsiniz. 

**S: Kısa ömürlü işletim sistemi diskleri için tüm VM boyutları desteklenecek mi?**

Y: Hayır, tüm Premium depolama VM boyutları, B serisi, N serisi ve H serisi boyutları dışında, desteklenir (DS, ES, FS, GS ve M).  
 
**S: Kısa ömürlü işletim sistemi diski var olan sanal makinelere ve ölçek kümelerine uygulanabilir mi?**

Y: Hayır, kısa ömürlü işletim sistemi diski yalnızca VM ve ölçek kümesi oluşturma sırasında kullanılabilir. 

**S: Bir ölçek kümesinde kısa ömürlü ve normal işletim sistemi diskleri karışımı yapabilir miyim?**

Y: Hayır, aynı ölçek kümesi içinde kısa ömürlü ve kalıcı işletim sistemi disk örneklerinin karışımı olamaz. 

**S: Kısa ömürlü işletim sistemi diski PowerShell veya CLı kullanılarak oluşturulabilir mi?**

Y: Evet, REST, şablonlar, PowerShell ve CLı kullanarak kısa ömürlü işletim sistemi diski ile VM 'Ler oluşturabilirsiniz.

**S: Kısa ömürlü işletim sistemi diski ile hangi özellikler desteklenmez?**

Y: Kısa ömürlü diskler şunları desteklemez:
- VM görüntülerini yakalama
- Disk anlık görüntüleri 
- Azure Disk Şifrelemesi 
- Azure Backup
- Azure Site Recovery  
- İşletim sistemi diski değiştirme 
