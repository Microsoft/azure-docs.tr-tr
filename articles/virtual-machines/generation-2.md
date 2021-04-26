---
title: 2. nesil VM 'Ler için Azure desteği
description: 2. nesil sanal makineler için Azure desteğine genel bakış
author: ju-shim
ms.service: virtual-machines
ms.subservice: generation-2-vm
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/26/2021
ms.author: jushiman
ms.openlocfilehash: a9c832d9a5e90cb2c9ffcbe8a26cdb08ebcddfcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553128"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Azure'da 2. nesil VM'ler için destek

2. nesil sanal makineler (VM 'Ler) için destek artık Azure 'da kullanıma sunuldu. Oluşturduktan sonra bir sanal makinenin neslini değiştiremezsiniz, bu nedenle bir oluşturma seçmeden önce bu sayfadaki konuları gözden geçirin.

2. nesil VM 'ler, 1. nesil VM 'lerde desteklenmeyen önemli özellikleri destekler. Bu özellikler, artan bellek, Intel Software Guard uzantıları (Intel SGX) ve sanallaştırılmış kalıcı bellek (vPMEM) içerir. Şirket içinde çalışan 2. nesil VM 'lerde, henüz Azure 'da desteklenmeyen bazı özellikler var. Daha fazla bilgi için [Özellikler ve yetenekler](#features-and-capabilities) bölümüne bakın.

2. nesil sanal makineler, 1. nesil VM 'Ler tarafından kullanılan BIOS tabanlı mimaride değil, yeni UEFı tabanlı önyükleme mimarisini kullanır. 1. nesil VM 'Lerle karşılaştırıldığında 2. nesil VM 'Ler geliştirilmiş önyükleme ve yükleme süreleriyle aynı olabilir. 2. nesil VM 'Lere genel bakış ve 1. nesil ve 2. nesil arasındaki farklılıklar için bkz. [Hyper-V ' d e 1. nesil veya 2 sanal makine oluşturmalıyım?](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>2. nesil VM boyutları

1. nesil sanal makineler, Azure 'daki tüm VM boyutları tarafından desteklenir (Mv2 serisi VM 'Ler hariç). Azure şimdi aşağıdaki seçili VM serileri için 2. nesil destek sunuyor:


* [B serisi](sizes-b-series-burstable.md)
* [DCsv2 serisi](dcv2-series.md)
* [DSv2 serisi](dv2-dsv2-series.md)
* [Dsv3 serisi](dv3-dsv3-series.md)
* [Dsv4 serisi](dv4-dsv4-series.md)
* [Dasv4 serisi](dav4-dasv4-series.md)
* [Ddsv4 serisi](ddv4-ddsv4-series.md)
* [Esv3 serisi](ev3-esv3-series.md)
* [Esv4 serisi](ev4-esv4-series.md)
* [Easv4 serisi](eav4-easv4-series.md)
* [Edsv4 serisi](edv4-edsv4-series.md)
* [Fsv2 serisi](fsv2-series.md)
* [GS serisi](sizes-previous-gen.md#gs-series)
* [HB serisi](hb-series.md)
* [HC serisi](hc-series.md)
* [LS Serisi](sizes-previous-gen.md#ls-series) 
* [Lsv2 serisi](lsv2-series.md)
* [M serisi](m-series.md)
* [Mv2-serisi](mv2-series.md)<sup>1</sup>
* [NCv2 serisi](ncv2-series.md) 
* [NCv3 serisi](ncv3-series.md)
* [ND serisi](nd-series.md)
* [NVv3 serisi](nvv3-series.md)
* [NVv4 serisi](nvv4-series.md)
* [NCasT4_v3 serisi](nct4-v3-series.md)

<sup>1</sup> Mv2 serisi, 1. nesil VM görüntülerini desteklemez ve yalnızca 2. nesil görüntülerin bir alt kümesini destekler. Ayrıntılar için lütfen bkz. [Mv2-Series belgeleri](mv2-series.md) .


## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketi 'nde 2. nesil VM görüntüleri

2. nesil VM 'Ler aşağıdaki Market görüntülerini destekler:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Windows 10 Enterprise
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16,04, 18,04, 19,04, 19,10 
* RHEL 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 7,0
* Sent OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4
* Oracle Linux 7,7, 7,7-CI

> [!NOTE]
> Mv2-Series gibi belirli sanal makine boyutları yalnızca bu görüntülerin bir alt kümesini destekleyebilir. tüm ayrıntılar için lütfen ilgili sanal makine boyutu belgelerine bakın.

## <a name="on-premises-vs-azure-generation-2-vms"></a>Şirket içi ve Azure 2. nesil VM 'Ler

Azure, 2. nesil VM 'Ler için şirket içi Hyper-V ' d i destekleyen bazı özellikleri desteklememektedir.

| 2. nesil özelliği                | Şirket içi Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Güvenli önyükleme                         | :heavy_check_mark:  | Güvenilir başlatma ile (Önizleme)   |
| Korumalı VM                         | :heavy_check_mark:  | sayı   |
| vTPM                                | :heavy_check_mark:  | Güvenilir başlatma ile (Önizleme)  |
| Sanallaştırma tabanlı güvenlik (VBS) | :heavy_check_mark:  | Güvenilir başlatma ile (Önizleme)   |
| VHDX biçimi                         | :heavy_check_mark:  | sayı   |

Daha fazla bilgi için bkz. [güvenilir başlatma (Önizleme)](trusted-launch.md).

## <a name="features-and-capabilities"></a>Özellikler ve yetenekler

### <a name="generation-1-vs-generation-2-features"></a>1. nesil ve 2. nesil Özellikler

| Özellik | 1. nesil | 2. nesil |
|---------|--------------|--------------|
| Önyükleme             | PCAT                      | UEFı                               |
| Disk denetleyicileri | IDE                       | SCSI                               |
| VM boyutları         | Tüm VM boyutları | [Kullanılabilir boyutları gör](#generation-2-vm-sizes) |

### <a name="generation-1-vs-generation-2-capabilities"></a>1. nesil ve 2. nesil yetenekler

| Özellik | 1. nesil | 2. nesil |
|------------|--------------|--------------|
| İşletim sistemi diski > 2 TB                    | sayı                | :heavy_check_mark: |
| Özel disk/görüntü/takas OS         | :heavy_check_mark: | :heavy_check_mark: |
| Sanal makine ölçek kümesi desteği | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Yedekleme/geri yükleme                    | :heavy_check_mark: | :heavy_check_mark: |
| Paylaşılan görüntü Galerisi              | :heavy_check_mark: | :heavy_check_mark: |
| [Azure disk şifrelemesi](../security/fundamentals/azure-disk-encryption-vms-vmss.md)             | :heavy_check_mark: | :heavy_check_mark:                |
| [Sunucu tarafı şifrelemesi](disk-encryption.md)            | :heavy_check_mark: | :heavy_check_mark: |

## <a name="creating-a-generation-2-vm"></a>2. nesil VM oluşturma

### <a name="marketplace-image"></a>Market görüntüsü

Azure portal veya Azure CLı 'de, UEFı önyüklemesini destekleyen bir market görüntüsünden 2. nesil VM 'Ler oluşturabilirsiniz.

#### <a name="azure-portal"></a>Azure portalı

Aşağıda, Azure portal 'de 2. nesil (Gen2) VM oluşturma adımları verilmiştir.

1. https://portal.azure.com adresinden Azure portalında oturum açın.
1. **Kaynak oluştur**’u seçin.
1. Sol taraftaki Azure Marketi 'nden **Tümünü gör** ' e tıklayın.
1. Gen2 destekleyen bir görüntü seçin.
1. **Oluştur**’a tıklayın.
1. **Gelişmiş** sekmesinde, **VM oluşturma** bölümünde **Gen 2** seçeneğini belirleyin.
1. **Temel bilgiler** sekmesinde, **örnek ayrıntıları** altında **Boyut** ' a gidin ve **VM boyutu Seç** dikey penceresini açın.
1. [Desteklenen 2. nesil VM 'yi](#generation-2-vm-sizes)seçin.
1. VM oluşturma işleminin bitmesini sağlamak için sayfaların geri kalanına gidin.

![Gen 1 veya Gen 2 VM seçin](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Ayrıca, 1. nesil veya 2. nesil SKU 'ya doğrudan başvurarak bir VM oluşturmak için PowerShell 'i de kullanabilirsiniz.

Örneğin, teklifte SKU 'ların bir listesini almak için aşağıdaki PowerShell cmdlet 'ini kullanın `WindowsServer` .

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

İşletim sistemi olarak Windows Server 2012 ile bir VM oluşturuyorsanız, 1. nesil (BIOS) veya 2. nesil (UEFı) VM SKU 'SU seçersiniz, bu da şuna benzer:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Desteklenen Market görüntülerinin geçerli listesi için [Özellikler ve yetenekler](#features-and-capabilities) bölümüne bakın.

#### <a name="azure-cli"></a>Azure CLI’si

Alternatif olarak, **Yayımcı** tarafından listelenmiş olan 2. nesil görüntüleri görmek IÇIN Azure CLI ' yi de kullanabilirsiniz.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

### <a name="managed-image-or-managed-disk"></a>Yönetilen görüntü veya yönetilen disk

Yönetilen bir görüntüden veya yönetilen diskten, 1. nesil bir VM oluşturduğunuz şekilde bir 2. nesil VM oluşturabilirsiniz.

### <a name="virtual-machine-scale-sets"></a>Sanal makine ölçek kümeleri

Ayrıca, sanal makine ölçek kümelerini kullanarak 2. nesil VM 'Ler oluşturabilirsiniz. Azure CLı 'da 2. nesil VM 'Ler oluşturmak için Azure Ölçek Kümeleri ' ni kullanın.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

* **2. nesil VM 'Ler tüm Azure bölgelerinde kullanılabilir mi?**  
    Evet. Her bölgede [2. nesıl VM boyutları](#generation-2-vm-sizes) kullanılamaz. 2. nesil VM 'nin kullanılabilirliği, VM boyutunun kullanılabilirliğine bağlıdır.

* **1. nesil ve 2. nesil VM 'Ler arasında bir fiyat farkı var mı?**  
   Hayır.

* **Şirket içi nesil 2 VM 'den bir. vhd dosyası var. Azure 'da 2. nesil VM oluşturmak için bu. vhd dosyasını kullanabilir miyim?**
  Evet, 2. nesil. vhd dosyanızı Azure 'a getirebilir ve bunu kullanarak 2. nesil bir VM oluşturabilirsiniz. Bunu yapmak için aşağıdaki adımları kullanın:
    1. . Vhd 'yi, sanal makineyi oluşturmak istediğiniz bölgedeki bir depolama hesabına yükleyin.
    1. . Vhd dosyasından yönetilen disk oluşturun. Hyper-V oluşturma özelliğini v2 olarak ayarlayın. Aşağıdaki PowerShell komutları, yönetilen disk oluştururken Hyper-V oluşturma özelliğini ayarlar.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Disk kullanılabilir olduğunda, bu diski ekleyerek bir VM oluşturun. Oluşturulan VM bir 2. nesil VM olacaktır.
    2. nesil VM oluşturulduğunda, bu VM 'nin görüntüsünü isteğe bağlı olarak genelleştirmenize de sağlayabilirsiniz. Görüntüyü genelleştirerek, birden çok VM oluşturmak için kullanabilirsiniz.

* **Nasıl yaparım? işletim sistemi disk boyutu artsın mı?**  

  2 TiB 'den büyük işletim sistemi diskleri 2. nesil VM 'lere yenidir. Varsayılan olarak, işletim sistemi diskleri 2. nesil VM 'Ler için 2 TiB 'den daha küçüktür. Disk boyutunu önerilen en fazla 4 TiB 'a kadar artırabilirsiniz. İşletim sistemi disk boyutunu artırmak için Azure CLı veya Azure portal kullanın. Diskleri program aracılığıyla genişletme hakkında daha fazla bilgi için bkz. [Windows](./windows/expand-os-disk.md) veya [Linux](./linux/resize-os-disk-gpt-partition.md)için **diski yeniden boyutlandırma** .

  İşletim sistemi diskinin boyutunu Azure portal artırmak için:

  1. Azure portal VM özellikleri sayfasına gidin.
  1. VM 'yi kapatmak ve serbest bırakmak için **Durdur** düğmesini seçin.
  1. **Diskler** bölümünde, yükseltmek istediğiniz işletim sistemi diskini seçin.
  1. **Diskler** bölümünde **yapılandırma**' yı seçin ve **boyutu** istediğiniz değerle güncelleştirin.
  1. VM özellikleri sayfasına dönün ve VM 'yi **başlatın** .
  
  2 TiB 'den büyük işletim sistemi diskleri için bir uyarı görebilirsiniz. Uyarı 2. nesil VM 'lere uygulanmaz. Ancak, 4 TiB 'den büyük işletim sistemi disk boyutları desteklenmez.

* **2. nesil VM 'Ler hızlandırılmış ağı destekliyor mu?**  
    Evet. Daha fazla bilgi için bkz. [hızlandırılmış ağ Ile VM oluşturma](../virtual-network/create-vm-accelerated-networking-cli.md).

* **2. nesil VM 'Ler Azure 'da güvenli önyükleme veya vTPM 'yi destekliyor mu?**
    Hem vTPM hem de güvenli önyükleme, 2. nesil VM 'Ler için güvenilir başlatma (Önizleme) özellikleridir. Daha fazla bilgi için bkz. [güvenilir başlatma](trusted-launch.md).
    
* **VHDX 2. nesil üzerinde destekleniyor mu?**  
    Hayır, 2. nesil VM 'Ler yalnızca VHD 'YI destekler.

* **2. nesil VM 'Ler Azure Ultra Disk Depolama 'yi destekliyor mu?**  
    Evet.

* **1. nesil bir VM 'yi 2. nesil 'e geçirebilir miyim?**  
    Hayır, bir VM 'yi oluşturduktan sonra oluşturmayı değiştiremezsiniz. VM nesilleri arasında geçiş yapmanız gerekiyorsa, farklı nesil yeni bir VM oluşturun.

* **Gen2 sanal makinesi oluşturmaya çalıştığımda, neden VM boyutum boyut seçicide etkin değil mi?**

    Bu, aşağıdakiler gerçekleşerek çözülebilir:

    1. **Gelişmiş** sekmesinde **VM oluşturma** özelliğinin **Gen 2** olarak ayarlandığını doğrulayın.
    1. [Gen2 VM 'leri destekleyen bir VM boyutu](#generation-2-vm-sizes)arıyor olduğunuzdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Genel 2 VM 'lerle [güvenilir başlatma (Önizleme)](trusted-launch-portal.md) hakkında daha fazla bilgi edinin.

[Hyper-V ' d a 2. nesil sanal makineler](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)hakkında bilgi edinin.
