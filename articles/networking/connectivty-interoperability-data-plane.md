---
title: "Azure 'da birlikte çalışabilirlik: veri düzlemi Analizi"
description: Bu makalede, Azure 'da ExpressRoute, siteden siteye VPN ve sanal ağ eşlemesi arasındaki birlikte çalışabilirliği çözümlemek için kullanabileceğiniz test kurulumunun veri düzlemi Analizi sağlanmaktadır.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4797fca5b4a756ca502147172e6f1590b9eacbe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234180"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Azure 'da birlikte çalışabilirlik: veri düzlemi Analizi

Bu makalede, [Test kurulumunun][Setup]veri düzlemi Analizi açıklanmaktadır. Test Kurulum [yapılandırmasını][Configuration] ve test kurulumunun [Denetim düzlemi analizini][Control-Analysis] de gözden geçirebilirsiniz.

Veri düzlemi analizi, bir yerel ağdan (LAN veya sanal ağ) bir topoloji içinde diğerine çapraz geçiş yapan paketler tarafından alınan yolu inceler. İki yerel ağ arasındaki veri yolu simetrik değildir. Bu nedenle, bu makalede, yerel bir ağdan bir iletme yolunu, geriye doğru yoldan ayrı olan başka bir ağa çözümliyoruz.

## <a name="data-path-from-the-hub-vnet"></a>Hub VNet 'ten veri yolu

### <a name="path-to-the-spoke-vnet"></a>Bağlı olan VNet 'in yolu

Sanal ağ (VNet) eşlemesi, eşlenen iki VNET arasında ağ köprüsü işlevine öykünür. Hub VNet 'ten sanal ağ VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı şurada gösterilmiştir:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.11.30.4

Trace complete.
```

Aşağıdaki şekilde, Merkez VNet 'in ve Azure ağ Izleyicisi 'nin perspektifinden bağlı olan VNet 'in grafik bağlantı görünümü gösterilmektedir:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Dal VNet 'in yolu

Hub VNet 'ten şube VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı burada gösterilmektedir:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms     1 ms     1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     2 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

Bu izleme işlemi sırasında, ilk atlama hub VNet 'in Azure VPN Gateway içindeki VPN ağ geçidindir. İkinci atlama, şube VNet 'in VPN ağ geçididir. Şube VNet 'in VPN ağ geçidinin IP adresi hub VNet 'te tanıtılmıyor. Üçüncü atlama, şube VNet 'teki VM 'dir.

Aşağıdaki şekilde, hub VNet 'in ve ağ izleyicisinin perspektifinden şube VNet 'in grafik bağlantı görünümü gösterilmektedir:

![2][2]

Aynı bağlantı için aşağıdaki şekilde, ağ izleyicilerinde kılavuz görünümü gösterilmektedir:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Şirket içi konumun yolu 1

Hub VNet 'ten şirket içi konum 1 ' deki bir VM 'ye bir sanal ağ üzerinden izleme işlemi çıkışı aşağıda gösterilmiştir:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     2 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

Bu izleme yolunda ilk atlama, Microsoft kurumsal bir yönlendirici (MSEE) için Azure ExpressRoute ağ geçidi tünel uç noktasıdır. İkinci ve üçüncü atlama müşteri kenarı (CE) yönlendiricisidir ve şirket içi konum 1 LAN IP 'lardır. Bu IP adresleri hub VNet 'te tanıtılmaz. Dördüncü atlama, şirket içi konum 1 ' deki VM 'dir.


### <a name="path-to-on-premises-location-2"></a>Şirket içi konumun yolu 2

Hub VNet 'ten şirket içi konum 2 ' deki bir VM 'ye yönelik izleme işlemi çıkışı şurada gösterilmiştir:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    75 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

Bu izleme yolunda ilk atlama, bir MSEE ExpressRoute ağ geçidi tünel uç noktasıdır. İkinci ve üçüncü atlama, CE yönlendiricisidir ve şirket içi konum 2 LAN IP 'lardır. Bu IP adresleri hub VNet 'te tanıtılmaz. Dördüncü atlama, şirket içi konum 2 ' deki VM 'dir.

### <a name="path-to-the-remote-vnet"></a>Uzak VNet 'in yolu

Bir hub VNet 'ten uzak VNet 'teki bir VM 'ye bir sanal ağ üzerinden izleme işlemi çıkışı aşağıda gösterilmiştir:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    69 ms  10.17.30.4

Trace complete.
```

Bu izleme yolunda ilk atlama, bir MSEE ExpressRoute ağ geçidi tünel uç noktasıdır. İkinci atlama, uzak VNet 'in ağ geçidi IP 'dır. İkinci atlama IP aralığı, hub VNet 'te tanıtılmaz. Üçüncü atlama, uzak VNet 'teki VM 'dir.

## <a name="data-path-from-the-spoke-vnet"></a>Bağlı olan VNet 'ten veri yolu

Bağlı olan VNet, hub VNet 'in ağ görünümünü paylaşır. VNet eşlemesi ile, bağlı olan VNet, hub VNET 'in uzak ağ geçidi bağlantısını, doğrudan bağlı olan VNet 'e bağlı gibi kullanır.

### <a name="path-to-the-hub-vnet"></a>Hub VNet 'in yolu

Bağlı olan VNet 'ten hub VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı şurada gösterilmiştir:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet"></a>Dal VNet 'in yolu

Bağlı olan VNet 'ten şube VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı burada gösterilmektedir:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

Bu izleme işlemi sırasında, ilk atlama hub VNet 'in VPN ağ geçidindir. İkinci atlama, şube VNet 'in VPN ağ geçididir. Şube VNet 'in VPN ağ geçidinin IP adresi hub/bağlı ağ VNet içinde tanıtılmıyor. Üçüncü atlama, şube VNet 'teki VM 'dir.

### <a name="path-to-on-premises-location-1"></a>Şirket içi konumun yolu 1

Bağlı olan VNet 'ten şirket içi konum 1 ' deki bir VM 'ye yönelik izleme işlemi çıkışı burada gösterilmektedir:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    24 ms     2 ms     3 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     3 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

Bu izleme yolu 'nda ilk atlama, Merkez VNet 'in ExpressRoute ağ geçidi tüneli bitiş noktasıdır ve bir MSEE. İkinci ve üçüncü atlama, CE yönlendiricisidir ve şirket içi konum 1 LAN IP 'lardır. Bu IP adresleri hub/bağlı ağ VNet 'inde tanıtılmaz. Dördüncü atlama, şirket içi konum 1 ' deki VM 'dir.

### <a name="path-to-on-premises-location-2"></a>Şirket içi konumun yolu 2

Bağlı olan VNet 'ten şirket içi konum 2 ' deki bir VM 'ye yönelik izleme işlemi çıkışı şurada gösterilmiştir:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    76 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

Bu izleme yolu 'nda ilk atlama, Merkez VNet 'in ExpressRoute ağ geçidi tüneli bitiş noktasıdır ve bir MSEE. İkinci ve üçüncü atlama, CE yönlendiricisidir ve şirket içi konum 2 LAN IP 'lardır. Bu IP adresleri hub/bağlı ağ sanal ağları 'nda tanıtılmaz. Dördüncü atlama, şirket içi konum 2 ' deki VM 'dir.

### <a name="path-to-the-remote-vnet"></a>Uzak VNet 'in yolu

Bağlı olan VNet 'ten uzak VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı burada gösterilmektedir:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.10.30.133
  2     *        *        *     Request timed out.
  3    71 ms    70 ms    70 ms  10.17.30.4

Trace complete.
```

Bu izleme yolu 'nda ilk atlama, Merkez VNet 'in ExpressRoute ağ geçidi tüneli bitiş noktasıdır ve bir MSEE. İkinci atlama, uzak VNet 'in ağ geçidi IP 'dır. İkinci atlama IP aralığı, hub/bağlı ağ VNet 'te tanıtılmaz. Üçüncü atlama, uzak VNet 'teki VM 'dir.

## <a name="data-path-from-the-branch-vnet"></a>Şube VNet 'ten veri yolu

### <a name="path-to-the-hub-vnet"></a>Hub VNet 'in yolu

Şube VNet 'ten hub VNet 'teki bir VM 'ye olan izleme işlemi çıkışı şurada gösterilmiştir:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     3 ms  10.10.30.4

Trace complete.
```

Bu izleme yolu 'nda ilk atlama, şube VNet 'in VPN ağ geçididir. İkinci atlama, hub VNet 'in VPN ağ geçidindir. Hub VNet 'in VPN ağ geçidinin IP adresi uzak VNet 'te tanıtılmıyor. Üçüncü atlama, hub VNet 'teki VM 'dir.

### <a name="path-to-the-spoke-vnet"></a>Bağlı olan VNet 'in yolu

Şube VNet 'ten sanal ağ VNet 'teki bir VM 'ye olan izleme işlemi çıkışı şurada gösterilmiştir:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     1 ms    <1 ms     1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     2 ms  10.11.30.4

Trace complete.
```

Bu izleme yolu 'nda ilk atlama, şube VNet 'in VPN ağ geçididir. İkinci atlama, hub VNet 'in VPN ağ geçidindir. Hub VNet 'in VPN ağ geçidinin IP adresi uzak VNet 'te tanıtılmıyor. Üçüncü atlama, bağlı olan VNet 'teki VM 'dir.

### <a name="path-to-on-premises-location-1"></a>Şirket içi konumun yolu 1

Şube VNet 'ten şirket içi konum 1 ' deki bir VM 'ye yönelik izleme işlemi çıkışı burada gösterilmektedir:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.2.30.125
  4     *        *        *     Request timed out.
  5     3 ms     3 ms     3 ms  10.2.30.10

Trace complete.
```

Bu izleme yolu 'nda ilk atlama, şube VNet 'in VPN ağ geçididir. İkinci atlama, hub VNet 'in VPN ağ geçidindir. Hub VNet 'in VPN ağ geçidinin IP adresi uzak VNet 'te tanıtılmıyor. Üçüncü atlama, birincil CE yönlendiricisinde VPN tüneli sonlandırma noktasıdır. Dördüncü atlama, şirket içi konum 1 ' in iç IP adresidir. Bu LAN IP adresi, CE yönlendiricisinin dışında tanıtılmaz. Beşinci atlama, şirket içi konum 1 ' deki hedef VM 'dir.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Şirket içi konum 2 ve uzak VNet 'in yolu

Denetim düzlemi analizinde anlatıldığı gibi, şube VNet 'in şirket içi konum 2 ' ye veya ağ yapılandırması başına uzak VNet 'e görünürlüğü yoktur. Aşağıdaki ping sonuçları şunları onaylayın: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

C:\Users\rb>ping 10.17.30.4

Pinging 10.17.30.4 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.17.30.4:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

## <a name="data-path-from-on-premises-location-1"></a>Şirket içi konumdan veri yolu 1

### <a name="path-to-the-hub-vnet"></a>Hub VNet 'in yolu

Şirket içi konum 1 ' den Merkez VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı burada gösterilmektedir:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     2 ms     2 ms     2 ms  10.10.30.4

Trace complete.
```

Bu izleme için, ilk iki atlama şirket içi ağın bir parçasıdır. Üçüncü atlama, CE yönlendiricisinin yüzlerine yönelik birincil MSEE arabirimidir. Dördüncü atlama, hub VNet 'in ExpressRoute ağ geçidindir. Hub VNet 'in ExpressRoute ağ geçidinin IP aralığı, şirket içi ağa tanıtılmaz. Beşinci atlama hedef VM 'dir.

Ağ Izleyicisi yalnızca Azure merkezli bir görünüm sağlar. Şirket içi bir perspektifte Azure Ağ Performansı İzleyicisi kullanırız. Ağ Performansı İzleyicisi, veri yolu analizi için Azure dışındaki ağlarda bulunan sunuculara yükleyebileceğiniz aracılar sağlar.

Aşağıdaki şekilde, ExpressRoute aracılığıyla hub VNet 'teki VM 'ye yönelik şirket içi konum 1 VM bağlantısının topoloji görünümü gösterilmektedir:

![4][4]

Daha önce anlatıldığı gibi, test kurulumu, şirket içi konum 1 ile hub VNet arasında ExpressRoute için yedekleme bağlantısı olarak siteden siteye VPN kullanır. Yedekleme veri yolunu test etmek için şirket içi konum 1 birincil CE yönlendirici ve ilgili MSEE arasında bir ExpressRoute bağlantı hatasına yol açalım. Bir ExpressRoute bağlantı başarısızlığını yapmak için, MSEE 'yi sunan CE arabirimini kapatın:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     3 ms  10.10.30.4

Trace complete.
```

Aşağıdaki şekilde, ExpressRoute bağlantısı kapatıldığında siteden siteye VPN bağlantısı aracılığıyla hub VNet 'teki VM 'ye yönelik şirket içi konum 1 VM bağlantısının topoloji görünümü gösterilmektedir:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Bağlı olan VNet 'in yolu

Şirket içi konumundan 1 ' den bağlı olan VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı burada gösterilmektedir:

Veri yolu analizini, bağlı olan VNet 'e doğru yapmak için ExpressRoute birincil bağlantısını geri getirelim:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     3 ms     2 ms     2 ms  10.11.30.4

Trace complete.
```

Veri yolu analizinin geri kalanı için birincil ExpressRoute 1 bağlantısını getirin.

### <a name="path-to-the-branch-vnet"></a>Dal VNet 'in yolu

Şirket içi konum 1 ' den şube VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı burada gösterilmektedir:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

### <a name="path-to-on-premises-location-2"></a>Şirket içi konumun yolu 2

[Denetim düzlemi analizinde][Control-Analysis]tartıştığımız gibi, şirket içi konum 1 ' in ağ yapılandırmasına göre şirket içi konum 2 ' ye görünürlüğü yoktur. Aşağıdaki ping sonuçları şunları onaylayın: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

### <a name="path-to-the-remote-vnet"></a>Uzak VNet 'in yolu

Şirket içi konum 1 ' den uzak VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı burada gösterilmektedir:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2     2 ms     5 ms     7 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5    69 ms    70 ms    69 ms  10.17.30.4

Trace complete.
```

## <a name="data-path-from-on-premises-location-2"></a>Şirket içi konumdan veri yolu 2

### <a name="path-to-the-hub-vnet"></a>Hub VNet 'in yolu

Şirket içi konum 2 ' den Merkez VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı burada gösterilmektedir:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.4
  3    <1 ms    <1 ms    <1 ms  192.168.31.22
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Bağlı olan VNet 'in yolu

Şirket içi konum 2 ' den bağlı olan VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı burada gösterilmektedir:

```console
C:\Windows\system32>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops
  1    <1 ms    <1 ms     1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.0
  3    <1 ms    <1 ms    <1 ms  192.168.31.18
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Şube VNet 'in yolu, şirket içi konum 1 ve uzak VNet

[Denetim düzlemi analizinde][Control-Analysis]tartıştığımız gibi, şirket içi konum 1 ' de şube VNET 'e, şirket içi konuma 1 veya ağ yapılandırması başına uzak VNET 'e yönelik bir görünürlük yoktur. 

## <a name="data-path-from-the-remote-vnet"></a>Uzak VNet 'ten veri yolu

### <a name="path-to-the-hub-vnet"></a>Hub VNet 'in yolu

Uzak VNet 'ten hub VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı şurada gösterilmiştir:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    65 ms    65 ms    65 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    68 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Bağlı olan VNet 'in yolu

Uzak VNet 'ten bağlı olan VNet 'teki bir VM 'ye yönelik izleme işlemi çıkışı şurada gösterilmiştir:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    71 ms    69 ms    69 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Şube VNet 'in ve şirket içi konumun yolu 2

[Denetim düzlemi analizinde][Control-Analysis]tartıştığımız gibi, uzak VNET 'In şube VNET 'e veya ağ yapılandırmasına göre şirket içi konum 2 ' ye görünürlüğü yoktur. 

### <a name="path-to-on-premises-location-1"></a>Şirket içi konumun yolu 1

Uzak VNet 'ten şirket içi konum 1 ' deki bir VM 'ye yapılan izleme işlemi çıkışı şurada gösterilmektedir:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    69 ms    69 ms    69 ms  10.2.30.10

Trace complete.
```

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Kademeli olarak ExpressRoute ve siteden siteye VPN bağlantısı

###  <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute üzerinden siteden siteye VPN

Şirket içi ağınız ve Azure sanal ağlarınız arasında özel olarak veri alışverişi yapmak için ExpressRoute Microsoft eşlemesi kullanarak siteden siteye VPN yapılandırabilirsiniz. Bu yapılandırmayla, verileri gizlilik, özgünlük ve bütünlük ile değiş tokuş edebilirsiniz. Veri değişimi de yeniden oynama olur. ExpressRoute Microsoft eşlemesi kullanarak bir siteden siteye IPSec VPN 'yi tünel modunda yapılandırma hakkında daha fazla bilgi için bkz. [ExpressRoute üzerinden siteden sıteye VPN Microsoft eşlemesi][S2S-Over-ExR]. 

Microsoft eşlemesi kullanan bir siteden siteye VPN yapılandırmanın birincil sınırlaması aktarım hızına sahiptir. IPSec tüneli üzerinden aktarım hızı VPN Gateway kapasitesinden sınırlıdır. VPN ağ geçidi verimlilik ExpressRoute aktarım hızına göre daha düşüktür. Bu senaryoda, yüksek oranda güvenli trafik için IPSec tüneli kullanılması ve diğer tüm trafik için özel eşleme kullanılması, ExpressRoute bant genişliği kullanımının iyileştirmenize yardımcı olur.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute için güvenli bir yük devretme yolu olarak siteden siteye VPN

ExpressRoute, yüksek kullanılabilirlik sağlamak için yedekli bir devre çifti işlevi görür. Coğrafi olarak yedekli ExpressRoute bağlantısını, farklı Azure bölgelerinde yapılandırabilirsiniz. Ayrıca, test kurulumumuzda gösterildiği gibi, bir Azure bölgesi içinde, ExpressRoute bağlantınızın yük devretme yolunu oluşturmak için siteden siteye VPN kullanabilirsiniz. Aynı ön ekler hem ExpressRoute hem de siteden siteye VPN üzerinden tanıtıldığında Azure, ExpressRoute 'u önceliklendirir. ExpressRoute ve siteden siteye VPN arasındaki asimetrik yönlendirmeyi önlemek için, şirket içi ağ yapılandırması, siteden siteye VPN bağlantısı kullanmadan önce ExpressRoute bağlantısı kullanılarak da devrik olmalıdır.

ExpressRoute ve siteden siteye VPN için birlikte var olan bağlantıları yapılandırma hakkında daha fazla bilgi için bkz. [ExpressRoute ve siteden siteye birlikte bulunma][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Arka uç bağlantısını bağlı olan sanal ağlara ve dal konumlarına Genişlet

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet eşlemesi kullanarak bağlı olan VNet bağlantısı

Hub ve bağlı ağ VNet mimarisi yaygın olarak kullanılır. Hub, bağlı olan sanal ağlarınız ve şirket içi ağınız arasında merkezi bir bağlantı noktası görevi gören Azure 'da bulunan bir VNet ' dir. Bağlı bileşen, hub ile eş olan ve iş yüklerini yalıtmak için kullanabileceğiniz sanal ağlardır. ExpressRoute veya VPN bağlantısı aracılığıyla şirket içi veri merkezi ile hub arasındaki trafik akışları. Mimari hakkında daha fazla bilgi için bkz. [Azure 'da Merkez-uç ağ topolojisi uygulama][Hub-n-Spoke].

Bir bölgedeki VNet eşlemesi içinde, bağlı olan sanal ağlar, uzak ağlarla iletişim kurmak için hub VNet ağ geçitlerini (VPN ve ExpressRoute ağ geçitleri) kullanabilir.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Siteden siteye VPN kullanarak dal VNet bağlantısı

Farklı bölgelerde olan dal VNET 'leri ve şirket içi ağları bir hub VNet aracılığıyla birbirleriyle iletişim kurmak isteyebilirsiniz. Bu yapılandırma için yerel Azure çözümü, VPN kullanarak siteden siteye VPN bağlantısı olur. Alternatif, hub 'da yönlendirme için bir ağ sanal gereci (NVA) kullanmaktır.

Daha fazla bilgi için bkz. [ne VPN Gateway?][VPN] nedir ve [yüksek oranda kullanılabilir NVA dağıtma][Deploy-NVA].


## <a name="next-steps"></a>Sonraki adımlar

Bkz. [ExpressRoute SSS][ExR-FAQ] :
-   ExpressRoute ağ geçidine kaç ExpressRoute bağlantı hattı bağlayabileceğinizi öğrenin.
-   Bir ExpressRoute devresine kaç ExpressRoute ağ geçidi bağlayabileceğinizi öğrenin.
-   ExpressRoute 'un diğer ölçek sınırları hakkında bilgi edinin.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Hub VNet 'ten bağlı olan VNet 'e bağlantının ağ Izleyicisi görünümü"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Hub VNet 'ten bir dal VNet 'e bağlantının ağ Izleyicisi görünümü"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Ağ Izleyicisi, hub VNet 'ten bir dal VNet 'e bağlantının bağlantısını görüntüleme"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "ExpressRoute 1 VM ile hub VNet 'e giden bağlantı Ağ Performansı İzleyicisi"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Siteden siteye VPN aracılığıyla konum 1 VM 'den hub VNet 'e bağlantı Ağ Performansı İzleyicisi görünümü"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md