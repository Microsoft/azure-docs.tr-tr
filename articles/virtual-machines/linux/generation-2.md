---
title: 2\. nesil VM 'Ler için Azure desteği
description: 2\. nesil sanal makineler için Azure desteğine genel bakış
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: lahugh
ms.openlocfilehash: 02ab7cdcbf0f150157eb44ef5e28e8284621b813
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173964"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Azure 'da 2. nesil VM 'Ler için destek

2\. nesil sanal makineler (VM 'Ler) için destek artık Azure 'da kullanıma sunuldu. Oluşturduktan sonra bir sanal makinenin neslini değiştiremezsiniz, bu nedenle bir oluşturma seçmeden önce bu sayfadaki konuları gözden geçirin.

2\. nesil VM 'ler, 1. nesil VM 'lerde desteklenmeyen önemli özellikleri destekler. Bu özellikler, artan bellek, Intel Software Guard uzantıları (Intel SGX) ve sanallaştırılmış kalıcı bellek (vPMEM) içerir. Şirket içinde çalışan 2. nesil VM 'lerde, henüz Azure 'da desteklenmeyen bazı özellikler var. Daha fazla bilgi için [Özellikler ve yetenekler](#features-and-capabilities) bölümüne bakın.

2\. nesil sanal makineler, 1. nesil VM 'Ler tarafından kullanılan BIOS tabanlı mimaride değil, yeni UEFı tabanlı önyükleme mimarisini kullanır. 1\. nesil VM 'Lerle karşılaştırıldığında 2. nesil VM 'Ler geliştirilmiş önyükleme ve yükleme süreleriyle aynı olabilir. 2\. nesil VM 'Lere genel bakış ve 1. nesil ve 2. nesil arasındaki farklılıklar için bkz. [Hyper-V ' d e 1. nesil veya 2 sanal makine oluşturmalıyım?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>2\. nesil VM boyutları

1\. nesil sanal makineler, Azure 'daki tüm VM boyutları tarafından desteklenir. Azure şimdi aşağıdaki seçili VM serileri için 2. nesil destek sunuyor:

* [B serisi](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [DC Serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dc-series)
* [Dsv2-Series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) ve [Dsv3 serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3 serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2 serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [HB Serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hb-series)
* [HC Serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hc-series)
* [LS-Serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) ve [Lsv2 serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)
* [NCv2-Series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv2-series) ve [NCv3 serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv3-series)
* [ND serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nd-series)
* [NVv3 serisi](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

> [!NOTE]
> Mv2 serisi VM 'Ler için 2. nesil VM görüntülerinin kullanımı, Mv2 serisi yalnızca 2. nesil VM görüntüleriyle çalıştığından genel kullanıma sunulmuştur. 1\. nesil VM görüntüleri Mv2 serisi VM 'lerde desteklenmez. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketi 'nde 2. nesil VM görüntüleri

2\. nesil VM 'Ler aşağıdaki Market görüntülerini destekler:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19,04, 19,10 
* RHEL 8,0

## <a name="on-premises-vs-azure-generation-2-vms"></a>Şirket içi ve Azure 2. nesil VM 'Ler

Azure, 2. nesil VM 'Ler için şirket içi Hyper-V ' d i destekleyen bazı özellikleri desteklememektedir.

| 2\. nesil özelliği                | Şirket içi Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Güvenli önyükleme                         | :heavy_check_mark:  | sayı   |
| Korumalı VM                         | :heavy_check_mark:  | sayı   |
| vTPM                                | :heavy_check_mark:  | sayı   |
| Sanallaştırma tabanlı güvenlik (VBS) | :heavy_check_mark:  | sayı   |
| VHDX biçimi                         | :heavy_check_mark:  | sayı   |

## <a name="features-and-capabilities"></a>Özellikler ve yetenekler

### <a name="generation-1-vs-generation-2-features"></a>1\. nesil ve 2. nesil Özellikler

| Özellik | 1\. nesil | 2\. nesil |
|---------|--------------|--------------|
| Önyükleme             | PCAT         | UEFI |
| Disk denetleyicileri | IDE          | SCSI |
| VM boyutları         | Tüm VM boyutları | Yalnızca Premium depolamayı destekleyen VM 'Ler |

### <a name="generation-1-vs-generation-2-capabilities"></a>1\. nesil ve 2. nesil yetenekler

| Özellik | 1\. nesil | 2\. nesil |
|------------|--------------|--------------|
| İşletim sistemi diski > 2 TB                    | sayı                | :heavy_check_mark: |
| Özel disk/görüntü/takas OS         | :heavy_check_mark: | :heavy_check_mark: |
| Sanal makine ölçek kümesi desteği | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Yedekleme/geri yükleme                    | :heavy_check_mark: | :heavy_check_mark: |
| Paylaşılan görüntü Galerisi              | :heavy_check_mark: | :heavy_check_mark: |
| Azure disk şifrelemesi             | :heavy_check_mark: | sayı                |

## <a name="creating-a-generation-2-vm"></a>2\. nesil VM oluşturma

### <a name="marketplace-image"></a>Market görüntüsü

Azure portal veya Azure CLı 'de, UEFı önyüklemesini destekleyen bir market görüntüsünden 2. nesil VM 'Ler oluşturabilirsiniz.

#### <a name="azure-portal"></a>Azure portal

Windows ve SLES için 2. nesil görüntüler, Gen1 görüntülerle aynı sunucu teklifine dahildir. Flow perspektifinden bu şekilde, VM 'niz için portaldan teklif ve SKU 'YU seçersiniz. SKU hem 1. kuşak hem de 2. nesil görüntüleri destekliyorsa, VM oluşturma akışındaki *Gelişmiş* sekmesinden 2. nesıl bir VM oluşturmayı seçebilirsiniz.

Şu anda, aşağıdaki SKU 'Lar 1. kuşak ve 2. nesil görüntüleri destekler:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

Teklif olarak bir Windows Server SKU seçtiğinizde, **Gelişmiş** sekmesinde, bir **Gen 1** (BIOS) veya **Gen 2** (UEFI) VM oluşturma seçeneği vardır. **Gen 2**' yi seçerseniz, **temel kavramlar** sekmesinde seçilen VM boyutunun [2. nesil VM 'ler için desteklendiğinden](#generation-2-vm-sizes)emin olun.

![Gen 1 veya Gen 2 VM seçin](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Ayrıca, 1. nesil veya 2. nesil SKU 'ya doğrudan başvurarak bir VM oluşturmak için PowerShell 'i de kullanabilirsiniz.

Örneğin, `WindowsServer` teklifinde SKU 'ların bir listesini almak için aşağıdaki PowerShell cmdlet 'ini kullanın.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

İşletim sistemi olarak Windows Server 2012 ile bir VM oluşturuyorsanız, şunun gibi bir 1. nesil (BIOS) veya 2. nesil (UEFı) VM SKU 'SU seçersiniz:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Desteklenen Market görüntülerinin geçerli listesi için [Özellikler ve yetenekler](#features-and-capabilities) bölümüne bakın.

### <a name="managed-image-or-managed-disk"></a>Yönetilen görüntü veya yönetilen disk

Yönetilen bir görüntüden veya yönetilen diskten, 1. nesil bir VM oluşturduğunuz şekilde bir 2. nesil VM oluşturabilirsiniz.

### <a name="virtual-machine-scale-sets"></a>Sanal makine ölçek kümeleri

Ayrıca, sanal makine ölçek kümelerini kullanarak 2. nesil VM 'Ler oluşturabilirsiniz. Azure CLı 'da 2. nesil VM 'Ler oluşturmak için Azure Ölçek Kümeleri ' ni kullanın.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

* **2. nesil VM 'Ler tüm Azure bölgelerinde kullanılabilir mi?**  
    Evet. Her bölgede [2. nesıl VM boyutları](#generation-2-vm-sizes) kullanılamaz. 2\. nesil VM 'nin kullanılabilirliği, VM boyutunun kullanılabilirliğine bağlıdır.

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
    2\. nesil VM oluşturulduğunda, bu VM 'nin görüntüsünü isteğe bağlı olarak genelleştirmenize de sağlayabilirsiniz. Görüntüyü genelleştirerek birden çok VM oluşturmak için kullanabilirsiniz.

* **Nasıl yaparım? işletim sistemi disk boyutu artsın mı?**  
  2 TB 'den büyük işletim sistemi diskleri 2. nesil VM 'lere yenidir. Varsayılan olarak, 1. nesil VM 'Ler için işletim sistemi diskleri 2 TB 'den daha küçüktür. Disk boyutunu önerilen en fazla 4 TB 'a kadar artırabilirsiniz. İşletim sistemi disk boyutunu artırmak için Azure CLı veya Azure portal kullanın. Diskleri program aracılığıyla genişletme hakkında daha fazla bilgi için bkz. [diski yeniden boyutlandırma](expand-disks.md).

  İşletim sistemi diskinin boyutunu Azure portal artırmak için:

  1. Azure portal VM özellikleri sayfasına gidin.
  1. VM 'yi kapatmak ve serbest bırakmak için **Durdur** düğmesini seçin.
  1. **Diskler** bölümünde, yükseltmek istediğiniz işletim sistemi diskini seçin.
  1. **Diskler** bölümünde **yapılandırma**' yı seçin ve **boyutu** istediğiniz değerle güncelleştirin.
  1. VM özellikleri sayfasına dönün ve VM 'yi **başlatın** .

  2 TB 'den büyük işletim sistemi diskleri için bir uyarı görebilirsiniz. Uyarı 2. nesil VM 'lere uygulanmaz. Ancak, 4 TB 'tan büyük işletim sistemi disk boyutları *önerilmez.*

* **2. nesil VM 'Ler hızlandırılmış ağı destekliyor mu?**  
    Evet. Daha fazla bilgi için bkz. [hızlandırılmış ağ Ile VM oluşturma](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **VHDX 2. nesil üzerinde destekleniyor mu?**  
    Hayır, 2. nesil VM 'Ler yalnızca VHD 'YI destekler.

* **2. nesil VM 'Ler Azure Ultra Disk Depolama 'yi destekliyor mu?**  
    Evet.

* **1. nesil bir VM 'yi 2. nesil 'e geçirebilir miyim?**  
    Hayır, bir VM 'yi oluşturduktan sonra oluşturmayı değiştiremezsiniz. VM nesilleri arasında geçiş yapmanız gerekiyorsa, farklı nesil yeni bir VM oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

* [Hyper-V ' d a 2. nesil sanal makineler](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)hakkında bilgi edinin.
