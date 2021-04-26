---
title: B serisi Burstable-Azure sanal makineleri
description: Koyu Azure VM boyutlarının B serisini açıklar.
services: virtual-machines
ms.subservice: vm-sizes-general
author: rishabv90
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: risverma
ms.openlocfilehash: e0797dfd8c4bf1cf86e2eeafb824298c412e5f1f
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491823"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>B serisi Burstable sanal makine boyutları

B serisi VM 'Ler, Web sunucuları, kavram kanıtı, küçük veritabanları ve geliştirme derlemesi ortamları gibi her zaman CPU 'nun tam performansına gerek olmayan iş yükleri için idealdir. Bu iş yükleri genellikle ani performans gereksinimlerine sahiptir. B serisi, taban çizgisini daha az kullanırken krediler oluşturabileceğiniz ana hat performansı ile bir VM boyutu satın alma olanağı sağlar. VM 'nin birikmiş krediler olduğunda, uygulamanız daha yüksek CPU performansı gerektirdiğinde, sanal makinenin en fazla %100 ' i kullanarak VM 'nin üstüne veri bloğu oluşturabilir.

B serisi aşağıdaki VM boyutlarında gelir:

[Azure Işlem birimi (ACU)](./acu.md): farklılık gösterir *<br>
[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenmiyor<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenen * *<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>

* B serisi VM 'Ler Burstable olur ve bu nedenle ACU numaraları iş yüklerine ve temel kullanıma bağlı olarak farklılık gösterir.<br>
* * Hızlandırılmış ağ yalnızca *Standard_B12ms*, *Standard_B16ms* ve *Standard_B20ms* için desteklenir.
<br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | VM 'nin temel CPU performansı | VM 'nin en yüksek CPU performansı | İlk krediler | Bankaya açık/saat | Maksimum bankaya ait krediler | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: ıOPS/MBps | Önbelleğe alınmamış maksimum disk aktarım hızı: ıOPS/MBps | En fazla NIC |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0,5 | 4   | %5   | %100  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | %10  | %100  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | %20  | %100  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | %40  | %200  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | %60  | %200  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920/22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | %90  | %400  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | %135 | %800  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | %202 | %1200 | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | %270 | %1600 | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | %337 | %2000 | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls yalnızca Linux üzerinde desteklenir

## <a name="workload-example"></a>İş yükü örneği

Bir Office iade/çıkış uygulaması düşünün. Uygulama, çalışma saatlerinde CPU patları gerektirir, ancak saatler dışında çok fazla bilgi işlem gücü gerektirmez. Bu örnekte, iş yükü 64GiB RAM 'e sahip bir 16 vCPU sanal makinesinin verimli bir şekilde çalışmasını gerektirir.

Tabloda saatlik trafik verileri gösterilir ve grafik bu trafiğin görsel bir gösterimidir.

B16 özellikleri:

En yüksek CPU performansı: 16vCPU * 100% = 1600%

Taban çizgisi: %270

![Saatlik trafik verileri grafiği](./media/b-series-burstable/office-workload.png)

| Senaryo | Saat | CPU kullanımı (%) | Birikmiş jenerik<sup>1</sup> | Kredi var |
| --- | --- | --- | --- | --- |
| B16ms dağıtımı | Dağıtım | Dağıtım  | 480 (ilk krediler) | 480 |
| Trafik yok | 0:00 | 0 | 162 | 642 |
| Trafik yok | 1:00 | 0 | 162 | 804 |
| Trafik yok | 2:00 | 0 | 162 | 966 |
| Trafik yok | 3:00 | 0 | 162 | 1128 |
| Trafik yok | 4:00 | 0 | 162 | 1290 |
| Trafik yok | 5:00 | 0 | 162 | 1452 |
| Düşük trafik | 6:00 | 270 | 0 | 1452 |
| Çalışanlar Office 'e geliyor (uygulama ihtiyaçları %80 vCPU) | 7:00 | 1280 | -606 | 846 |
| Çalışanlar Office 'e gelmeye devam eder (uygulama %80 vCPU gerekiyor) | 8:00 | 1280 | -606 | 240 |
| Düşük trafik | 9:00 | 270 | 0 | 240 |
| Düşük trafik | 10:00 | 100 | 102 | 342 |
| Düşük trafik | 11:00 | 50 | 132 | 474 |
| Düşük trafik | 12:00 | 100 | 102 | 576 |
| Düşük trafik | 13:00 | 100 | 102 | 678 |
| Düşük trafik | 14:00 | 50 | 132 | 810 |
| Düşük trafik | 15:00 | 100 | 102 | 912 |
| Düşük trafik | 16:00 | 100 | 102 | 1014 |
| Çalışanların kullanıma aldığı (uygulama %100 vCPU olması gerekir) | 17:00 | 1600 | -798 | 216 |
| Düşük trafik | 18:00 | 270 | 0 | 216 |
| Düşük trafik | 19:00 | 270 | 0 | 216 |
| Düşük trafik | 20:00 | 50 | 132 | 348 |
| Düşük trafik | 21:00 | 50 | 132 | 480 |
| Trafik yok | 22:00 | 0 | 162 | 642 |
| Trafik yok | 23:00 | 0 | 162 | 804 |

<sup>1</sup> bir saat içinde kullanılan birikmiş krediler, şu şekilde eşdeğerdir: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes` .  

16 vCPU ve 64 GiB belleğe sahip bir D16s_v3 için saatlik ücret saat başına $0,936 (aylık $673,92) ve 16 vCPU ve 64 GiB belleği ile B16ms için Fiyat, saat başına ' dir (aylık $0,794). <b> Bu durum %15 tasarruf sonucu!</b>

## <a name="q--a"></a>Soru-Cevap

### <a name="q-what-happens-when-my-credits-run-out"></a>S: kredilerimin tükendiğinde ne olur?
Y: krediler TÜKENDIĞINDE, VM taban çizgisi performansına geri döner.

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>S: bir VM 'den %135 temel performansı nasıl alırsınız?

Y **:%** 135, VM boyutunu oluşturan 8 vCPU arasında paylaşılır. Örneğin, uygulamanız toplu işleme üzerinde çalışan 8 çekirdeğin 4 ' ü kullanıyorsa ve bu 4 vCPU 'nun her biri %30 ' da çalışıyorsa, toplam VM CPU performansı miktarı %120 değerine eşittir.  VM 'nizin, temel performansından %15 Delta temelinde kredi süresi oluşturması anlamına gelir.  Aynı zamanda, aynı VM 'nin tüm 8 vCPU 'nun %100 ' ü kullanmasını sağlayan kredilerin, %800 ' nin en yüksek CPU performansına sahip olduğu anlamına gelir.

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>S: kredi bakiyenizi ve tüketimi nasıl izleyebilirim?

Y: **KREDI** ölçümü, sanal makinenizin kaç kredinin bankaya ve **TÜKETIM kredisi** ölçümünün, sanal makinenizin bankadan kaç tane kaç tane tükettiğini görmenizi sağlar.    Bu ölçümleri portalda ölçümler bölmesinden veya Azure Izleyici API 'Leri aracılığıyla programlı bir şekilde görüntüleyebileceksiniz.

Azure için ölçüm verilerine erişme hakkında daha fazla bilgi için, bkz. [Microsoft Azure ölçümlere genel bakış](../azure-monitor/data-platform.md).

### <a name="q-how-are-credits-accumulated-and-consumed"></a>S: krediler nasıl birikmiş ve tüketildi?

Y **: VM** birikmesi ve tüketim ücretleri, tam olarak kendi temel performans düzeyinde çalışan bir VM 'nin net birikmesi veya ücretsiz kredi tüketimine sahip olacağı şekilde ayarlanır.  Bir VM, temel performans düzeyinin altında çalıştığında kredilerin net artışına sahip olur ve VM CPU 'YU temel performans düzeyinden daha fazla kullandığında kredilerin net bir düşüş olur.

**Örnek**: küçük zaman ve katılımcı veritabanı uygulamamın B1ms boyutunu kullanarak bir VM dağıttım. Bu boyut, uygulamamın bir vCPU 'nun en fazla %20 ' sini kullanmasına izin verir. Bu, dakikada 0,2 kredi kullanabilir miyim veya banka.

Uygulamam, çalışanlarınızın iş günlerimin başında ve sonunda meşgul, 7:00-9:00 ile 4:00-6 arasında: 00PM. Günün 20 saati boyunca Uygulamam genellikle yalnızca vCPU 'nun %10 ' u kullanılarak boşta kalır. Yoğun olmayan saatler için, dakikada 0,2 kredileri kazandım, ancak dakikada yalnızca 0,1 kredileri tüketiyorum. bu nedenle, VM 'im her saat için 0,1 x 60 = 6 kredileri alacak.  Yoğun olmadığım 20 saat boyunca 120 kredi olarak sunulacaktır.  

Uygulamamın %60 vCPU kullanımının ortalamasını aldım, ancak dakika başına 0,2 kredileri kazandım, 0,6 ancak dakikada yıllık 0,4 0,4 krediler elde ediyorum. Maksimum kullanım gününde 4 saat sürer. bu nedenle, yoğun kullanımlarım için 4 x 24 = 96 kredi maliyetlerini ücretlendirildim.

120 kredileri yoğun bir şekilde kazandım ve yoğun zamanlarım için kullandığım 96 kredilerini çıkardığımda, diğer etkinlik tekrarları için kullandığım gün başına ek 24 kredi görüyorum.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>S: birikmiş kredileri nasıl hesaplayabilirim ve kullandınız?

Y **: aşağıdaki** formülü kullanabilirsiniz:

(VM CPU kullanımı temel CPU performansı)/100 = jenerik Bankası veya dakika başına kullanım

Örneğin yukarıdaki örnekte, taban çizginiz %20 ' dir ve biriktirçalıştığınız CPU %10 ' un (%20 %10%/100 = 0,1 kredi/dakika) kullanabilirsiniz.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>S: B serisi Premium depolama veri disklerini destekliyor mu?

Y **: Evet**, tüm B serisi boyutlar Premium depolama veri disklerini destekler.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>S: yeniden dağıtma veya durdurma/başlatma sonrasında neden kalan kredim 0 olarak ayarlandı?

Y **: BIR** VM yeniden DAĞıTıLDıĞıNDA ve VM başka bir düğüme taşınırsa birikmiş kredi kaybedilir. VM durdurulur/başlatılır, ancak aynı düğümde kalırsa, VM birikmiş krediyi korur. Sanal makine bir düğümde yeni bir kez başlatıldığında, 240 Standard_B8ms için bir başlangıç kredisi alır.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>S: B1ls üzerinde desteklenmeyen bir işletim sistemi görüntüsü dağıttım ne olur?

Y **: B1ls** yalnızca Linux görüntülerini destekler ve başka bir işletim sistemi görüntüsünü dağıtırsanız en iyi müşteri deneyimini elde edemeyebilirsiniz.

## <a name="other-sizes-and-information"></a>Diğer boyutlar ve bilgiler

- [Genel amaçlı](sizes-general.md)
- [İşlem için iyileştirilmiş](sizes-compute.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)

Fiyatlandırma Hesaplayıcı: [Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/)

Disk türleri hakkında daha fazla bilgi: [disk türleri](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
