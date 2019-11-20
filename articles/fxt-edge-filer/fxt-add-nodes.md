---
title: Microsoft Azure FXT Edge Filer küme yapılandırması-düğüm ekleme
description: Azure FXT Edge Filer depolama önbelleğine düğüm ekleme
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 85ab9aaa3e184af7aa71a31eb3d8de1a20639c2a
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254934"
---
# <a name="tutorial-add-cluster-nodes"></a>Öğretici: küme düğümleri ekleme 

Yeni bir Azure FXT Edge Filer kümesi yalnızca bir düğüm ile oluşturulur. Diğer yapılandırmayı yapmadan önce en az iki düğüm eklemeli ve yüksek kullanılabilirliği etkinleştirmelisiniz. 

Bu öğretici, küme düğümlerinin nasıl ekleneceğini ve yüksek kullanılabilirlik (HA) özelliğinin nasıl etkinleştirileceğini açıklar. 

Bu öğreticide şunları öğreneceksiniz: 

> [!div class="checklist"]
> * FXT kümesine düğüm ekleme
> * HA 'yi etkinleştirme

Bu öğreticideki adımların tamamlanması yaklaşık 45 dakika sürer.

Bu öğreticiye başlamadan önce, eklemek istediğiniz düğümleri ve [ilk parolalarını ayarlayın](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. küme düğümleri sayfasını yükleme

Kümenin Denetim Masası ' nı bir Web tarayıcısında açın ve yönetici olarak oturum açın. (Ayrıntılı yönergeler genel bakış makalesinde, [ayarlar sayfalarını aç](fxt-cluster-create.md#open-the-settings-pages)' ın altında bulunur.)

Denetim masası açıldığında **Pano** sekmesini gösterir. 

![Denetim Masası panosu (ilk sekme)](media/fxt-cluster-config/dashboard-1-node.png)

Bu görüntüde, yeni oluşturulan kümenin tek bir düğüm ile panosu gösterilmektedir.

## <a name="2-locate-the-node-to-add"></a>2. eklenecek düğümü bulun

Düğüm eklemek için **Ayarlar** sekmesine tıklayın ve **küme** bölümünde **FXT düğümleri** sayfasını seçin.

![Küme > FXT düğümleri yüklü olan denetim masası ayarları sekmesi (ikinci sekme)](media/fxt-cluster-config/settings-fxt-nodes.png)

**FXT düğümleri-katılmamış** listede tüm atanmamış FXT düğümleri gösterilmektedir (çoğu veri merkezi yalnızca birkaç tane vardır. Kümeye eklemek istediğiniz FXT düğümlerini bulun.

> [!Tip] 
> **Katılmayan** listede istediğiniz düğümü bulamıyorsanız, bu gereksinimleri karşıladığından emin olun:
> 
> * Açık ve bir [kök parolası ayarlanmış](fxt-node-password.md).
> * Bu, erişebileceğiniz bir ağa bağlıdır. VLAN 'Lar kullanıyorsanız, kümeyle aynı VLAN 'da olması gerekir.
> * Bonjour protokolde algılanabilir. 
>
>   Bazı güvenlik duvarı ayarları Bonjour tarafından kullanılan TCP/UDP bağlantı noktalarını engeller ve bu, FXT işletim sisteminin düğümleri otomatik olarak algılamasını önler.
> 
> Eklemek istediğiniz düğüm listede yoksa, şu çözümleri deneyin: 
> 
> * **El Ile bulma** DÜĞMESINE tıklayarak IP adresine göre bulun.
> 
> * Geçici IP adreslerini el ile atayın. Bu çok nadir bir durumdur, ancak etiketli VLAN 'Lar kullandığınızda ve düğümler doğru ağda değilse veya ağınız kendi kendine atanan IP adreslerine izin vermediği durumlarda gerekli olabilir. [STATIK IP adresini el ile ayarlamak](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html)için bu belgenin eski sürümündeki yönergeleri izleyin.

Düğüm adı, IP adresi, yazılım sürümü ve uygunluk durumu listede görüntülenir. Genellikle, **durum** sütunu "katılmayı istiyor" diyor ya da düğümün kümeye katılması için uygun olmayan bir sistem veya donanım sorunu tanımlıyor.

**Eylemler** sütununda, düğümü kümeye eklemenize veya yazılımının yazılımını güncelleştirmenize olanak sağlayan düğmeler bulunur. Güncelleştirme düğmesi, kümede zaten bulunan düğümlerle eşleşen yazılım sürümünü otomatik olarak kurar.

Bir kümedeki tüm düğümlerin işletim sistemi aynı sürümünü kullanması gerekir, ancak bir düğüm eklemeden önce yazılımı güncelleştirmeniz gerekmez. **Birleşime Izin ver** düğmesine tıkladıktan sonra küme JOIN işlemi, kümedeki sürümle eşleşen işletim sistemi yazılımını otomatik olarak denetler ve kurar.

Bu sayfadaki seçenekler hakkında daha fazla bilgi edinmek için küme yapılandırma kılavuzunda [ **küme** > **FXT düğümlerini** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) okuyun.

## <a name="3-click-the-allow-to-join-button"></a>3. "katılmasına Izin ver" düğmesine tıklayın 

Eklemek istediğiniz düğümün **Eylemler sütunundaki eylemler** sütununda **katılığa izin ver*** düğmesine tıklayın.

Düğmeye tıkladıktan sonra, yazılımın durumu, yazılım, kümeye ekleme hazırlığı sırasında güncelleştirildiğinden değişir. 

Aşağıdaki görüntüde, kümeye katılma sürecinde olan bir düğüm gösterilmektedir (büyük olasılıkla, eklenmeden önce bir işletim sistemi güncelleştirmesi alıyor). Kümeye eklenmekte olan düğümler için **Eylemler** sütununda hiçbir düğme görünmez.

![düğüm tablosunun bir satırı, bir düğümün adını, IP adresini, yazılım sürümünü, "katılmasına Izin verildi" iletisini ve boş bir son sütunu gösterir](media/fxt-cluster-config/node-join-in-process.png)

Birkaç dakika sonra yeni düğüm, **FXT düğümleri** ayarları sayfasının en üstündeki küme düğümleri listesinde görünmelidir. 

Diğer düğümleri kümenize eklemek için bu işlemi tekrarlayın. Bir düğümün başka bir başlatmadan önce kümeye katılmasını bitirmesini beklemeniz gerekmez.

## <a name="enable-high-availability"></a>Yüksek kullanılabilirliği etkinleştir

Kümenize ikinci bir düğüm ekledikten sonra, Denetim Masası panosunda yüksek kullanılabilirlik özelliğinin yapılandırılmadığını belirten bir uyarı iletisi görebilirsiniz. 

Yüksek kullanılabilirlik (HA), küme düğümlerinin bir tane kaldığında birbirlerine telafi etmesine olanak tanır. HA varsayılan olarak etkin değildir.

!["Küme birden fazla düğüme sahip, ancak HA etkin değil..." iletisiyle Pano sekmesi Koşullar tablosunda](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Kümede en az üç düğüm olana kadar HA 'yi etkinleştirmeyin.

HA 'yi açmak için bu yordamı izleyin: 

1. **Ayarlar** sekmesinin **küme** bölümünde **yüksek kullanılabilirlik** sayfasını yükleyin.

   ![HA yapılandırma sayfası (küme > yüksek kullanılabilirlik). "HA 'yi etkinleştir" onay kutusu en üstte ve Gönder düğmesi en altta.](media/fxt-cluster-config/enable-ha.png)

2. **Ha etkinleştir** etiketli kutuya tıklayın ve **Gönder** düğmesine tıklayın. 

Söz konusu HA 'nın etkin olduğunu onaylamak için **panoda** bir uyarı belirir.

!["HA artık tam olarak yapılandırıldı" iletisini gösteren Pano tablosu](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Sonraki adımlar

Tüm düğümleri kümenize ekledikten sonra, kümenizin uzun vadeli depolama alanını yapılandırarak kuruluma devam edin.

> [!div class="nextstepaction"]
> [Arka uç depolama alanı ekleme ve sanal ad alanını ayarlama](fxt-add-storage.md)