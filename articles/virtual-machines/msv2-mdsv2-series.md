---
title: Msv2/Mdsv2 orta bellek serisi-Azure sanal makineleri
description: Msv2 serisi VM 'Ler için Özellikler.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: d85623184ad52fb0d4acd4c49d08badfaf886b30
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728239"
---
# <a name="msv2-and-mdsv2-series-medium-memory"></a>Msv2 ve Mdsv2 serisi orta bellek

Msv2 ve Mdsv2 orta bellek sanal makinesi serisi, 2,7 GHz ve 4,0 GHz tek çekirdekli Turbo sıklığının tüm çekirdek temel sıklığı ile Intel® Xeon® Platinum 8280 (Cascade Lake) işlemcisini Özellikler. Bu VM 'lerle, müşteriler yerel disk ve disksiz seçeneklerle daha fazla esneklik elde etmenizi sağlar. Müşteriler ayrıca 4 TiB bellek ile 192 vCPU 'ya kadar daha fazla CPU ve bellek içeren bir dizi yeni yalıtılmış VM boyutuna erişebilir. 


Msv2 ve Mdsv2 serisi VM 'Ler yalnızca 2. nesil ve 2. nesil desteklenen görüntülerin bir alt kümesini destekler. Msv2 ve Mdsv2 serisi için desteklenen görüntülerin tam listesi için aşağıya bakın.  

- Windows Server 2019 veya üzeri
- SUSE Linux Enterprise Server 12 SP4 ve üzeri ya da SUSE Linux Enterprise Server 15 SP1 ve üzeri
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 veya üzeri 
- Oracle Enterprise Linux 7,7 veya üzeri

2. nesil sanal makineler hakkında daha fazla bilgi için bkz. [Azure 'da 2. nesil VM 'ler Için destek](./generation-2.md).



[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Dinamik geçiş](maintenance-and-updates.md): desteklenmiyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenmiyor<br>
[VM oluşturma desteği](generation-2.md): 2. nesil<br>
[Yazma Hızlandırıcısı](./how-to-enable-write-accelerator.md): destekleniyor<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 orta bellek disksiz 

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Önbelleğe alınmamış maksimum disk aktarım hızı: ıOPS/MBps | En fazla NIC | Beklenen ağ bant genişliği (MB/sn) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Disk ile Mdsv2 orta bellek  

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | En fazla veri diski | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: ıOPS/MBps | Önbelleğe alınmamış maksimum disk aktarım hızı: ıOPS/MBps | En fazla NIC | Beklenen ağ bant genişliği (MB/sn) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 4096 | 64 |160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2048 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Diğer boyutlar ve bilgiler

- [Genel amaçlı](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

Fiyatlandırma Hesaplayıcı: [Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
