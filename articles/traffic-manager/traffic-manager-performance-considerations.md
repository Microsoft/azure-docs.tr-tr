---
title: Azure Traffic Manager için performans konuları | Microsoft Docs
description: Traffic Manager performansını anlayın ve Traffic Manager kullanırken Web sitenizin performansını test edin
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 5a0de772598877029649f8a985c79e28f5264535
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98186184"
---
# <a name="performance-considerations-for-traffic-manager"></a>Traffic Manager için performans konuları

Bu sayfada, Traffic Manager ile ilgili performans konuları açıklanmaktadır. Şu senaryoyu göz önünde bulundurun:

WestUS ve Eastasıya bölgelerinde Web sitenizin örneklerine sahipsiniz. Örneklerden biri Traffic Manager araştırması için sistem durumu denetiminde başarısız oluyor. Uygulama trafiği sağlıklı bölgeye yönlendirilir. Bu yük devretme beklenmektedir, ancak artık uzak bir bölgeye seyahat eden trafiğin gecikmesi temelinde performans sorunu olabilir.

## <a name="performance-considerations-for-traffic-manager"></a>Traffic Manager için performans konuları

Traffic Manager yalnızca, Web sitenizde bulunan performans etkisi ilk DNS aradır. Traffic Manager profilinizin adı için bir DNS isteği, trafficmanager.net bölgesini barındıran Microsoft DNS kök sunucusu tarafından işlenir. Traffic Manager, Microsoft 'un DNS kök sunucularını Traffic Manager ilkesi ve araştırma sonuçlarına göre doldurur ve düzenli olarak güncelleştirir. Bu nedenle, ilk DNS araması sırasında bile Traffic Manager 'e hiçbir DNS sorgusu gönderilmez.

Traffic Manager birkaç bileşenden oluşur: DNS ad sunucuları, bir API hizmeti, depolama katmanı ve bir uç nokta izleme hizmeti. Bir Traffic Manager hizmeti bileşeni başarısız olursa, Traffic Manager profilinizle ilişkili DNS adı üzerinde hiçbir etkisi olmaz. Microsoft DNS sunucularındaki kayıtlar değişmeden kalır. Ancak, uç nokta izleme ve DNS güncelleştirmesi gerçekleşmez. Bu nedenle, Traffic Manager birincil siteniz çalışırken yük devretme sitenize işaret eden DNS 'i güncelleştiremeyebilir.

DNS ad çözümlemesi hızlıdır ve sonuçlar önbelleğe alınır. İlk DNS aramasının hızı istemcinin ad çözümlemesi için kullandığı DNS sunucularına bağlıdır. Genellikle, bir istemci ~ 50 ms içinde bir DNS aramasını tamamlayabilir. Aramanın sonuçları, DNS yaşam süresi (TTL) süresince önbelleğe alınır. Traffic Manager için varsayılan TTL 300 saniyedir.

Trafik Traffic Manager üzerinden akış yapmaz. DNS araması tamamlandıktan sonra, istemci, Web sitenizin bir örneği için bir IP adresine sahiptir. İstemci doğrudan bu adrese bağlanır ve Traffic Manager geçiş yapmaz. Seçtiğiniz Traffic Manager ilkesi DNS performansını etkilemez. Ancak, bir performans yönlendirme yöntemi uygulama deneyimini olumsuz etkileyebilir. Örneğin, ilkeniz trafiği Kuzey Amerika trafiği Asya 'da barındırılan bir örneğe yeniden yönlendirirse, bu oturumlara yönelik ağ gecikmesi bir performans sorunu olabilir.

## <a name="measuring-traffic-manager-performance"></a>Traffic Manager performansını ölçme

Bir Traffic Manager profilinin performansını ve davranışını anlamak için kullanabileceğiniz çeşitli web siteleri vardır. Bu sitelerin birçoğu ücretsizdir, ancak kısıtlamaları olabilir. Bazı sitelerde, bir ücret için Gelişmiş izleme ve raporlama olanağı sunulur.

Bu sitelerdeki araçlar DNS gecikmeleri ölçer ve dünyanın dört bir yanındaki istemci konumları için çözümlenen IP adreslerini görüntüler. Bu araçların çoğu DNS sonuçlarını önbelleğe vermez. Bu nedenle, Araçlar her bir test çalıştırıldığında tam DNS aramasını gösterir. Kendi istemcinizden test ettiğinizde, yalnızca TTL süresi boyunca tam DNS arama performansını bir kez deneyolursunuz.

## <a name="sample-tools-to-measure-dns-performance"></a>DNS performansını ölçmek için örnek araçlar

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS birçok performans aracı sunar. DNS karşılaştırma aracı, DNS adınızı ne kadar süreyle çözdüğüne ve bunun diğer DNS hizmeti sağlayıcılarıyla nasıl Karşılaştırıldığı hakkında sizi gösterebilir.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    En basit araçlardan biri WebSitePulse. DNS çözümleme süresi, Ilk bayt, son bayt ve diğer performans istatistiklerini görmek için URL 'YI girin. Üç farklı test konumu arasından seçim yapabilirsiniz. Bu örnekte, ilk yürütmenin DNS aramasının 0,204 sn aldığını gösterdiğini görürsünüz.

    !["DNS" arama sonucu vurgulanmış "WebSitePulse" aracını gösteren ekran görüntüsü.](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Sonuçlar önbelleğe alındığından, DNS aramasında aynı Traffic Manager uç noktası için ikinci test 0,002 sn alır.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [CA uygulaması yapay Izleyici](https://asm.ca.com/en/checkit.php)

    Daha önce Izle-fare denetimi Web sitesi aracı olarak bilindiğinde, bu site birden çok coğrafi bölgeden aynı anda DNS çözümleme süresi gösterir. DNS çözümleme süresini, bağlantı süresini ve birkaç coğrafi konumdan hızı görmek için URL 'YI girin. Dünyanın dört bir yanındaki farklı konumlar için hangi barındırılan hizmetin döndürüleceğini görmek için bu testi kullanın.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Bu araç, bir Web sayfasının her öğesi için performans istatistikleri sağlar. Sayfa analizi sekmesi, DNS aramasında harcanan sürenin yüzdesini gösterir.

* [DNS nedir?](https://www.whatsmydns.net/)

    Bu site, 20 farklı konumdan bir DNS araması yapar ve sonuçları bir haritada görüntüler.

* [Dig Web arabirimi](https://www.digwebinterface.com)

    Bu site, CNAMEs ve A kayıtları gibi daha ayrıntılı DNS bilgileri gösterir. Seçenekler bölümünde ' colorize çıkış ' ve ' stats ' seçeneklerini denetlediğinizden emin olun ve nameservers altında ' All ' seçeneğini belirleyin.

## <a name="next-steps"></a>Sonraki Adımlar

[Traffic Manager trafik yönlendirme yöntemleri hakkında](traffic-manager-routing-methods.md)

[Traffic Manager ayarlarınızı test etme](traffic-manager-testing-settings.md)

[Traffic Manager üzerindeki işlemler (REST API Başvurusu)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Azure Traffic Manager cmdlet 'Leri](/powershell/module/az.trafficmanager)