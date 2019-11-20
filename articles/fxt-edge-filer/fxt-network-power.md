---
title: Microsoft Azure FXT Edge filigran ağ bağlantıları ve güç kaynağı
description: Azure FXT Edge Filer donanımı için ağ bağlantı noktalarını kablo ve ek güç iliştirme
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 474172284383bc9ba0e5b5c11c66e1b990010184
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254864"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Öğretici: Azure FXT Edge Filer düğümüne ağ bağlantıları oluşturma ve güç sağlama

Bu öğreticide, bir Azure FXT Edge Filer donanım düğümü için ağ bağlantılarını nasıl kablolu olarak kullanabileceğiniz öğretilir.

Bu öğreticide şunları öğreneceksiniz: 

> [!div class="checklist"]
> * Ortamınız için ağ kablosu türünü seçme
> * Azure FXT Edge Filer düğümünü veri merkezi ağınıza bağlama
> * Kabloları kablo yönetimi ARM (CMA) aracılığıyla yönlendirme
> * Gücü çıkarılan cihaza bağlama ve şirket içinde açma

## <a name="prerequisites"></a>Prerequisites

Bu öğreticiye başlamadan önce, Azure FXT Edge Filer 'ın standart bir ekipman rafıyla yüklenmesi gerekir. CMA 'nın, dosyalayıcı düğümüne yüklenmesi gerekir. 

## <a name="identify-ports"></a>Bağlantı noktalarını tanımla

Azure FXT Edge filin arkasında yer alan çeşitli bağlantı noktalarını belirler. 
 
![Kablolu bir cihazın geri dönmesi](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Cihazı kablo

* [Ağ bağlantı noktalarında](#network-ports)açıklandığı gıbı, RJ-45 bağlantı noktalarını veri merkezinize ait ağ kaynağına bağlayın.  
* [Idrac bağlantı noktasını](#idrac-port) GÜVENLI bir DHCP sunucusu ile ayrı bir ağa güvenli bir şekilde bağlayın. 
* İlk kurulum için düğüme bir klavye ve izleyici bağlamak üzere USB bağlantı noktalarını ve VGA bağlantı noktasını kullanın. Düğümün diğer bağlantı noktalarını etkinleştirmek için düğümü önyüklemeniz ve [bir başlangıç parolası ayarlamanız](fxt-node-password.md) gerekir. Ayrıntılar için [Başlangıç parolalarını ayarla](fxt-node-password.md) bölümünü okuyun. 

Bu makalede ayrıca düğüm için [AC gücünün nasıl bağlanacağı](#connect-power-cables) açıklanır. 

Bu makalede ayrıca, özel sorun giderme için gerekliyse düğümün [seri bağlantı noktasına](#serial-port-only-when-necessary)nasıl bağlanabileceğiniz açıklanır. 

### <a name="network-ports"></a>Ağ bağlantı noktaları 

Her Azure FXT Edge Filer düğümü aşağıdaki ağ bağlantı noktalarını içerir: 

* Altı yüksek hızlı 25GbE/10GbE çift hız veri bağlantı noktaları: 

  * İki Dual-Port eklentisi ağ bağdaştırıcısı tarafından sunulan dört bağlantı noktası
  * Ana kart Mezzanine ağ bağdaştırıcısı tarafından belirtilen iki bağlantı noktası 

* Anakart Mezzanine ağ bağdaştırıcısı tarafından iki adet 1GbE bağlantı noktası belirtildi 

Yüksek hızlı 25GbE/10GbE veri bağlantı noktalarında standart SFP28 uyumlu cages vardır. Optik kabloları kullanmak için SFP28 Optical ileticisi modülleri (sağlanmadı) yüklemelisiniz.

1GbE bağlantı noktalarında standart RJ-45 bağlayıcıları vardır.

Desteklenen kabloların, anahtarların ve alıcı sayısının tam listesi için, [Cavium Fastlınq 41000 serisi birlikte çalışabilirlik matrisine](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)başvurun.

Sisteminiz için kullanılacak bağlantı türü, veri merkezi ortamınıza bağlıdır.

* Bir 25GbE ağına bağlanılıyorsa, yüksek hızlı veri bağlantı noktalarından her birini aşağıdaki kablo türlerinden biri ile kablo olarak bağlayın:

  * 25GbE veya çift hız 25GbE/10GbE özelliğine sahip optik kablo ve SFP28 Optical alıcısı
  * SFP28 Type 25GbE-özellikli doğrudan Attach twinaxial kablosu

* Bir 10GbE ağa bağlanıyorsanız, yüksek hızlı veri bağlantı noktalarından her birini aşağıdakilerden biriyle kablo olarak bağlayın: 

  * 10GbE veya çift hız 25GbE/10GbE özelliğine sahip optik kablo ve SFP28 Optical alıcısı.
  * SFP28 Type 25GbE-özellikli doğrudan Attach twinaxial kablosu
  * SFP28 türü 10GbE özellikli doğrudan Attach twinaxial kablosu

* 1GbE ağ bağlantı noktaları, küme yönetimi trafiği için kullanılır. Küme yapılandırması için fiziksel olarak ayrı bir ağ oluşturmak istiyorsanız ( [Yönetim ağını yapılandırma](fxt-cluster-create.md#configure-the-management-network)bölümünde açıklanmıştır), kümeyi oluştururken **1 GB MGMT ağ kullan** seçeneğini işaretleyin. Desteklenen kablolar listesinde açıklandığı gibi standart Cat5 veya daha iyi bir kabloyla bağlantı noktalarını bağlayın.

  Tüm trafik için yüksek hızda bağlantı noktalarını kullanmayı planlıyorsanız, 1GbE bağlantı noktalarını kablolu olarak bırakabilirsiniz. Varsayılan olarak, 1 GbE ağ bağlantı noktaları, daha yüksek hızda bir veri bağlantı noktası varsa kullanılmaz.  

### <a name="idrac-port"></a>Idrac bağlantı noktası  

Idrac etiketli bağlantı noktası, donanım yönetimi ve izleme için kullanılan bir uzaktan erişim denetleyicisiyle iletişime izin veren bir 1Gb bağlantıdır. FXT yazılımı, sorun giderme ve kurtarma için bu denetleyiciyle akıllı platform yönetim arabirimi 'ni (ıPMı) kullanır. Bu bağlantı noktası aracılığıyla donanımı izlemek için yerleşik [Idrac arabirimini](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) kullanabilirsiniz. Idrac ve ıPMı erişimi varsayılan olarak etkindir. 

> [!Note]
> Idrac bağlantı noktası, işletim sistemini atlayabilir ve düğümdeki donanımla doğrudan etkileşim kurabilir. 

Idrac bağlantı noktasını bağlarken ve yapılandırırken bu güvenlik stratejilerini kullanın:

* Idrac bağlantı noktalarını yalnızca kümeye erişmek için kullanılan veri ağından fiziksel olarak ayrılmış bir ağa bağlayın.
* Her düğümde güvenli bir Idrac yönetici parolası ayarlayın. Bu parolayı, [donanım parolalarını ayarla](fxt-node-password.md)bölümündeki donanım izleme yönergelerini etkinleştirmek için ayarlamanız gerekir.
* Varsayılan Idrac bağlantı noktası yapılandırması, IP adresi ataması için DHCP ve IPv4 kullanır. DHCP ortamınızın iyi korunduğundan ve bağlantıların DHCP istemcileri ile DHCP sunucusu arasında kısıtlanmasını sağlayın. (Küme denetim masası, kümeyi oluşturduktan sonra düğümlerin adres yapılandırma yöntemini değiştirme ayarlarını içerir.)
* Idrac/ıPMı ağ trafiğini adanmış RJ-45 bağlantı noktasıyla sınırlayan Idrac bağlantı noktasını "adanmış mod" (varsayılan) olarak ayarlayın.

Idrac bağlantı noktası yüksek hızlı bir ağ bağlantısı gerektirmez.
  
### <a name="serial-port-only-when-necessary"></a>Seri bağlantı noktası (yalnızca gerekli olduğunda)

Bazı durumlarda, Microsoft hizmet ve destek bir sorunu tanılamak için bir terminali bir düğümün seri bağlantı noktasına bağlanmanızı söyleyebilir.  

Konsolunu eklemek için:

1. FXT Edge Filer düğümünün arka tarafında bulunan seri (COM1) bağlantı noktasını bulun.
1. Seri bağlantı noktasını ANSI-115200-8N1 için yapılandırılmış bir terminale bağlamak üzere boş bir modem kablosu kullanın.
1. Konsolunda oturum açın ve destek personeli tarafından yönlendirilmiş olarak diğer adımları uygulayın.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Kablo yönetimi ARM (CMA) içindeki yönlendirme kabloları

Her Azure FXT Edge Filer düğümü, isteğe bağlı bir kablo yönetimi ARM ile birlikte gelir. CMA, kablo yönlendirmeyi basitleştirir ve kabloların bağlantısını kesmenize gerek kalmadan kasanın arkasına daha kolay erişim sağlar. 

Kabloları CMA ile yönlendirmek için aşağıdaki yönergeleri izleyin: 

1. Belirtilen kravat 'yi kullanarak, kabloları girip, bitişik sistemlerle karışabilmeleri için (1), sepetleri bir araya koyun.
1. Hizmet konumundaki CMA 'yı kullanarak, kablo paketini iç ve dış sepetlerle (2) yönlendirin.
1. Kabloları (3) güvenli hale getirmek için sepetlerinin her iki ucunda da önceden yüklenmiş kanca ve döngü döngüsü kullanın.
1. CMA 'yı (4) tepsisinde geri dönüşümlü hale getirin.
1. Durum göstergesi kablosunu sistemin arkasına yükler ve kabloyu CMA aracılığıyla yönlendirerek güvenli hale getirin. Kablonun diğer sonunu dış CMA sepetinin (5) köşesine ekleyin. 

   > [!CAUTION]
   > Kablolar arasındaki kabloların olası hasarını önlemek için, bu kabloyu CMA aracılığıyla yönlendirdikten sonra durum göstergesi kablosunda herhangi bir bolluğu güvenli hale getirin. 

![Kablolar yüklü CMA çizimi](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  CMA 'yı yüklemediğinizde, kabloları sisteminizin arkasına yönlendirmek için, kıl setinde sunulan iki kanca ve döngü Straps 'yi kullanın.
> 
>  1. Her iki raf ayracın iç taraflarındaki dış CMA ayraçlarını bulun.
>  2. Kabloları yavaşça paketleyin ve bu, sol ve sağ tarafa sistem bağlayıcılarının seçimini çekmesini sağlar.
>  3. Kablo paketlerini güvenli hale getirmek için, bir yandan, sistemin her tarafındaki dış CMA köşeli ayraçları üzerinden kanca ve döngü, katman zincirler arasında geçiş yapın.
> 
>     ![CMA olmadan yönlendirilen kablolar](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>IP adresi gereksinimleri hakkında

Azure FXT Edge Filer karma depolama önbelleğinde bulunan donanım düğümleri için, IP adresleri küme yazılımı tarafından yönetilir.

Her düğüm en az bir IP adresi gerektirir, ancak kümeye düğüm eklendiğinde veya kümeden kaldırıldığında düğüm adresleri atanır. 

Gerekli olan IP adreslerinin toplam sayısı, önbelleğinizi oluşturan düğüm sayısına bağlıdır. 

Düğümler yüklendikten sonra Denetim Masası yazılımını kullanarak IP adresi aralığını yapılandırın. Daha fazla bilgi edinmek için, [küme için bilgi toplama bilgilerini](fxt-cluster-create.md#gather-information-for-the-cluster)okuyun.  

## <a name="connect-power-cables"></a>Güç kablolarını bağlama

Her Azure FXT Edge Filer düğümü iki güç kaynağı birimi (PSUs) kullanır. 

> [!TIP] 
> İki yedekli PSUs avantajlarından yararlanmak için, her AC güç kablosunu bağımsız bir dal devresine bir güç dağıtım birimine (PDU) ekleyin.  
> 
> Ek koruma için PTE 'leri desteklemek üzere bir UPS kullanabilirsiniz. 

1. Dahil edilen güç bağıntılarını kasadaki PSUs 'e bağlayın. Eş ve PSUs 'lerin tam olarak yerleştirildiğinden emin olun. 
1. Güç gücünü ekipman rafından güç dağıtım birimlerine ekleyin. Mümkünse, iki eş dizin için iki ayrı güç kaynağı kullanın. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Azure FXT Edge Filer düğümünü açma

Düğümü açmak için sistemin önündeki güç düğmesine basın. Düğme, sağ taraftaki denetim masasından yapılır. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Azure FXT Edge Filer düğümünü kapatma

Güç düğmesi, test sırasında ve bir kümeye eklenmeden önce sistemi kapatmak için kullanılabilir. Ancak, bir Azure FXT Edge Filer düğümü bir kümenin parçası olarak kullanıldıktan sonra, donanımı kapatmak için küme Denetim Masası yazılımını kullanmanız gerekir. Ayrıntılar için [Azure FXT Edge Filer donanımını güvenli bir şekilde kapatma hakkında](fxt-power-off.md) bilgi edinin. 

## <a name="next-steps"></a>Sonraki adımlar

Donanımı kablolama işini tamamladıktan sonra düğümlerin her birinde güç yapın ve kök parolalarını ayarlayarak onları başlatın. 
> [!div class="nextstepaction"]
> [Başlangıç parolalarını ayarla](fxt-node-password.md)
