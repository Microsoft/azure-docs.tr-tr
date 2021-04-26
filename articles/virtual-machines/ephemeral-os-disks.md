---
title: Kısa ömürlü işletim sistemi diskleri
description: Azure VM 'Leri için geçici işletim sistemi diskleri hakkında daha fazla bilgi edinin.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 24b1be2ca55b057c887c8782ce7eea1150f143da
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762632"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Azure VM 'Leri için kısa ömürlü işletim sistemi diskleri

Geçici işletim sistemi diskleri, yerel sanal makine (VM) depolama alanı üzerinde oluşturulur ve uzak Azure depolama 'ya kaydedilmez. Kısa ömürlü işletim sistemi diskleri, uygulamaların tek tek VM hatalarından dayanıklı olduğu, ancak VM dağıtım zamanından daha fazla etkilediği veya tek tek sanal makine örneklerinin yeniden oluşturulduğu durum bilgisiz iş yükleri için iyi çalışır. Kısa ömürlü IŞLETIM sistemi diski ile işletim sistemi diskine daha hızlı okuma/yazma gecikme süresi ve daha hızlı VM yeniden görüntüsü alırsınız. 
 
Kısa ömürlü disklerin temel özellikleri şunlardır: 
- Durum bilgisi olmayan uygulamalar için idealdir.
- Bunlar hem Market hem de özel görüntülerle kullanılabilir.
- VM 'Leri ve ölçek kümesi örneklerini orijinal önyükleme durumuna hızla sıfırlama veya yeniden görüntü oluşturma yeteneği.  
- Geçici bir diske benzer şekilde daha düşük gecikme süresi. 
- Kısa ömürlü işletim sistemi diskleri ücretsizdir, işletim sistemi diski için depolama maliyeti yoktur.
- Tüm Azure bölgelerinde kullanılabilir. 
- Kısa ömürlü işletim sistemi diski [paylaşılan görüntü Galerisi](./shared-image-galleries.md)tarafından desteklenir. 
 

 
Kalıcı ve kısa ömürlü işletim sistemi diskleri arasındaki önemli farklılıklar:

|                             | Kalıcı işletim sistemi diski                          | Kısa Ömürlü İşletim Sistemi Diski                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **İşletim sistemi diski için boyut sınırı**      | 2 TiB                                                                                        | VM boyutu için önbellek boyutu veya 2TiB, hangisi daha küçüktür. **GiB 'deki önbellek boyutu** için bkz. [DS](sizes-general.md), [es](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md)ve [GS](sizes-previous-gen.md#gs-series)              |
| **Desteklenen VM boyutları**          | Tümü                                                                                          | DSv1, DSv2, DSv3, Esv3, FS, FsV2, GS, d gibi Premium depolamayı destekleyen VM boyutları                                               |
| **Disk türü desteği**           | Yönetilen ve yönetilmeyen işletim sistemi diski                                                                | Yalnızca yönetilen işletim sistemi diski                                                               |
| **Bölge desteği**              | Tüm bölgeler                                                                                  | Tüm bölgeler                              |
| **Veri kalıcılığı**            | IŞLETIM sistemi diskine yazılan işletim sistemi diski verileri Azure depolama 'da depolanıyor                                  | İşletim sistemi diskine yazılan veriler yerel VM depolama alanında depolanır ve Azure Storage 'da kalıcı değildir. |
| **Serbest bırakılmış durumu durdur**      | VM 'Ler ve ölçek kümesi örnekleri, durdurma-serbest bırakıldı durumundan durdurulup yeniden başlatılabilir | VM 'Ler ve ölçek kümesi örnekleri durdurulamıyor-serbest bırakılamaz                                  |
| **Özelleştirilmiş işletim sistemi diski desteği** | Yes                                                                                          | Hayır                                                                                 |
| **İşletim sistemi diski yeniden boyutlandırma**              | VM oluşturma sırasında ve VM durdurulduktan sonra desteklenir-serbest bırakıldı                                | Yalnızca VM oluşturma sırasında desteklenir                                                  |
| **Yeni bir VM boyutuna yeniden boyutlandırma**   | İşletim sistemi diski verileri korunur                                                                    | İşletim sistemi diskindeki veriler silinir, işletim sistemi yeniden sağlandı       
| **Sayfa dosyası yerleşimi**   | Windows için, sayfa dosyası kaynak diskte depolanıyor                                              | Windows için sayfa dosyası işletim sistemi diskinde depolanır   |

## <a name="size-requirements"></a>Boyut gereksinimleri

VM ve örnek görüntülerini VM önbelleğinin boyutuna kadar dağıtabilirsiniz. Örneğin, marketten standart Windows Server görüntüleri 127 GiB hakkında, bu da 127 GiB 'den daha büyük bir önbelleğe sahip bir VM boyutuna ihtiyacınız olduğu anlamına gelir. Bu durumda [Standard_DS2_v2](dv2-dsv2-series.md) , yeterince büyük olmayan 86 gib önbellek boyutuna sahiptir. Standard_DS3_v2, yeterince büyük olan 172 GiB önbellek boyutuna sahiptir. Bu durumda Standard_DS3_v2, bu görüntüyle kullanabileceğiniz DSv2 serisinde en küçük boyutdir. Market ve Windows Server görüntülerinde, tarafından belirtilen temel Linux görüntüleri `[smallsize]` 30 GiB etrafında olur ve KULLANILABILIR VM boyutlarının çoğunu kullanabilir.

Kısa ömürlü diskler Ayrıca, VM boyutunun Premium depolamayı desteklemesini gerektirir. Boyutlar genellikle (her zaman değil) `s` , DSv2 ve EsV3 gibi bir ada sahiptir. Daha fazla bilgi için bkz. [Azure VM boyutları](sizes.md) , hangi boyutlarda Premium depolamayı destekledikleri hakkındaki ayrıntılar için.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Önizleme-kısa ömürlü işletim sistemi diskleri artık geçici disklere depolanabilir
Kısa ömürlü işletim sistemi diskleri artık VM önbelleğine ek olarak VM geçici/kaynak diskine depolanabilir. Bu nedenle, artık önbelleğe sahip olmayan veya önbelleği olmayan, ancak Dav3, Dav4, Eav4 ve Eav3 gibi kısa ömürlü işletim sistemi diskini depolamak için bir geçici/kaynak diskine sahip olan VM ile kısa ömürlü işletim sistemi diskleri kullanabilirsiniz. Bir VM 'de yeterli önbellek ve geçici alan varsa, Ayrıca, [Dağıtılmış Diskyerleştirmesi](/rest/api/compute/virtualmachines/list#diffdiskplacement)adlı yeni bir özellik kullanarak kısa ömürlü Işletim sistemi diskini nerede depolamak istediğinizi de belirtebilirsiniz. Bu özellik ile bir Windows VM 'si sağlandığında, disk belleği dosyasını işletim sistemi diskinde bulunan olarak yapılandıracağız. Bu özellik şu anda önizleme sürümündedir. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Başlamak için [erişim isteyin](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u).

## <a name="powershell"></a>PowerShell

PowerShell VM dağıtımı için kısa ömürlü bir disk kullanmak istiyorsanız, VM yapılandırmanızda [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) ' i kullanın. `-DiffDiskSetting` `Local` ' İ ve ' olarak ayarlayın `-Caching` `ReadOnly` .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Ölçek kümesi dağıtımları için, yapılandırmanızda [set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet 'ini kullanın. `-DiffDiskSetting` `Local` ' İ ve ' olarak ayarlayın `-Caching` `ReadOnly` .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

CLı VM dağıtımı için kısa ömürlü bir disk kullanmak istiyorsanız, `--ephemeral-os-disk` [az VM Create](/cli/azure/vm#az_vm_create) olarak parametresini `true` ve `--os-disk-caching` parametresini olarak ayarlayın `ReadOnly` .

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

Ölçek Kümeleri için, `--ephemeral-os-disk true` [az-VMSS-Create](/cli/azure/vmss#az_vmss_create) parametresini kullanın ve `--os-disk-caching` parametresini olarak ayarlayın `ReadOnly` .

## <a name="portal"></a>Portal

Azure portal, **diskler** sekmesinin **Gelişmiş** bölümünü açarak bir VM dağıtımı sırasında kısa ömürlü diskler kullanmayı seçebilirsiniz. **Kısa ömürlü işletim sistemi diski kullan** için **Evet**' i seçin.

![Kısa ömürlü işletim sistemi diski kullanmayı seçme için radyo düğmesini gösteren ekran görüntüsü](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Kısa ömürlü disk kullanma seçeneği gri ise, işletim sistemi görüntüsünden daha büyük bir önbellek boyutuna sahip olmayan veya Premium depolamayı desteklemeyen bir VM boyutu seçmiş olabilirsiniz. **Temel bilgiler** sayfasına dönün ve başka bir VM boyutu seçmeyi deneyin.

Ayrıca, Portal 'ı kullanarak kısa ömürlü işletim sistemi diskleri ile ölçek kümeleri de oluşturabilirsiniz. Yeterli büyüklükte bir önbellek boyutuna sahip bir VM boyutu seçtiğinizden emin olun ve ardından **kısa ömürlü işletim sistemi diski kullanın** ' de **Evet**' i seçin.

![Ölçek kümesi için kısa ömürlü bir işletim sistemi diski kullanmayı seçme radyo düğmesini gösteren ekran görüntüsü](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Ölçek kümesi şablonu dağıtımı  
Kısa ömürlü işletim sistemi diski kullanan bir ölçek kümesi oluşturma işlemi, `diffDiskSettings` özelliği `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` şablondaki kaynak türüne eklemektir. Ayrıca, önbelleğe alma ilkesi, `ReadOnly` kısa ömürlü işletim sistemi diski için olarak ayarlanmalıdır. 


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
Bir şablonu kullanarak, kısa ömürlü işletim sistemi diski ile bir VM dağıtabilirsiniz. Kısa ömürlü işletim sistemi diskleri kullanan bir VM oluşturma işlemi, `diffDiskSettings` özelliği şablondaki Microsoft. COMPUTE/virtualMachines kaynak türüne eklemektir. Ayrıca, önbelleğe alma ilkesi, `ReadOnly` kısa ömürlü işletim sistemi diski için olarak ayarlanmalıdır. 

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

**S: yerel işletim sistemi disklerinin boyutu nedir?**

Y: işletim sistemi diskine yönelik tüm okuma/yazma işlemleri, sanal makineyle aynı düğümde yerel olacak şekilde, platform ve özel görüntüleri destekliyoruz. 

**S: kısa ömürlü işletim sistemi diski yeniden boyutlandırılabilir mi?**

Y: Hayır, kısa ömürlü işletim sistemi diski sağlandıktan sonra, işletim sistemi diski yeniden boyutlandırılamaz. 

**S: yönetilen diskleri kısa ömürlü bir VM 'ye ekleyebilir miyim?**

Y: Evet, kısa ömürlü işletim sistemi diski kullanan bir VM 'ye yönetilen bir veri diski ekleyebilirsiniz. 

**S: tüm VM boyutları, kısa ömürlü işletim sistemi diskleri için desteklenecek mi?**

Y: Hayır, en Premium depolama VM boyutları desteklenir (DS, ES, FS, GS, d, vb.). Belirli bir VM boyutunun kısa ömürlü işletim sistemi disklerini destekleyip desteklemediğini bilmek için şunları yapabilirsiniz:

`Get-AzComputeResourceSku`PowerShell cmdlet 'ini çağırma
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**S: kısa ömürlü işletim sistemi diski var olan sanal makinelere ve ölçek kümelerine uygulanabilir mi?**

Y: Hayır, kısa ömürlü işletim sistemi diski yalnızca VM ve ölçek kümesi oluşturma sırasında kullanılabilir. 

**S: bir ölçek kümesinde kısa ömürlü ve normal işletim sistemi disklerini karıştırabilirsiniz misiniz?**

Y: Hayır, aynı ölçek kümesi içinde kısa ömürlü ve kalıcı işletim sistemi disk örneklerinin karışımı olamaz. 

**S: PowerShell veya CLı kullanılarak kısa ömürlü işletim sistemi diski oluşturulabilir mi?**

Y: Evet, REST, şablonlar, PowerShell ve CLı kullanarak kısa ömürlü işletim sistemi diski ile VM 'Ler oluşturabilirsiniz.

**S: kısa ömürlü işletim sistemi diski ile hangi özellikler desteklenmez?**

Y: kısa ömürlü diskler şunları desteklemez:
- VM görüntülerini yakalama
- Disk anlık görüntüleri 
- Azure Disk Şifrelemesi 
- Azure Backup
- Azure Site Recovery  
- İşletim sistemi diski değiştirme 

> [!NOTE]
> 
> Geçici diske Portal üzerinden erişilemeyecektir. Beklenen geçici diske erişirken "kaynak bulunamadı" veya "404" hatası alacaksınız.
> 
 
## <a name="next-steps"></a>Sonraki adımlar
[Azure CLI](/cli/azure/vm#az_vm_create)kullanarak kısa ömürlü işletim sistemi diski Ile bir VM oluşturabilirsiniz.
