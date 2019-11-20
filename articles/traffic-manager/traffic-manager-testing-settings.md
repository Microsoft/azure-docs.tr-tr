---
title: Azure Traffic Manager ayarlarını doğrulama
description: Bu makalede, Traffic Manager ayarlarınızı doğrulamayı ve trafik yönlendirme yöntemini test yapmayı öğrenin.
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: allensu
ms.openlocfilehash: ad74e5c51d5939218ebb546993d416b3df1cd04b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023521"
---
# <a name="verify-traffic-manager-settings"></a>Traffic Manager ayarlarını doğrulama

Traffic Manager ayarlarınızı test etmek için, çeşitli konumlarda testlerinizi çalıştırabileceğiniz birden fazla istemciniz olması gerekir. Ardından, Traffic Manager profilinizde uç noktaları tek seferde aşağı taşıyın.

* Değişikliklerin hızlıca yayılması için DNS TTL değerini düşük olarak ayarlayın (örneğin, 30 saniye).
* Azure bulut hizmetlerinizin ve test ettiğiniz profildeki Web sitelerinin IP adreslerini öğrenin.
* DNS adını bir IP adresine çözümleyecek ve bu adresi görüntüleyecek olan araçları kullanın.

DNS adlarının profilinizdeki uç noktaların IP adreslerine çözümlenip çözümlenmeyeceği denetleniyor. Adlar, Traffic Manager profilinde tanımlanan trafik yönlendirme yöntemiyle tutarlı bir şekilde çözümlenmelidir. DNS adlarını çözümlemek için **nslookup** veya **derinlemesine** gibi araçları kullanabilirsiniz.

Aşağıdaki örnekler Traffic Manager profilinizi test etmenize yardımcı olur.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Windows 'da nslookup ve ipconfig kullanarak Traffic Manager profilini denetleme

1. Yönetici olarak bir komut veya Windows PowerShell istemi açın.
2. DNS çözümleyici önbelleğini temizlemek için `ipconfig /flushdns` yazın.
3. `nslookup <your Traffic Manager domain name>` yazın. Örneğin, aşağıdaki komut etki alanı adını *MyApp. contoso* önekiyle denetler

        nslookup myapp.contoso.trafficmanager.net

    Tipik bir sonuç aşağıdaki bilgileri gösterir:

    + Bu Traffic Manager etki alanı adını çözümlemek için erişilmekte olan DNS sunucusunun DNS adı ve IP adresi.
    + Komut satırına yazdığınız Traffic Manager etki alanı adı "nslookup" ve Traffic Manager etki alanının çözümlediği IP adresi. İkinci IP adresi, denetlenecek önemli bir adrestir. Test ettiğiniz Traffic Manager profilindeki bulut hizmetlerinden veya Web sitelerinden biri için genel bir sanal IP (VIP) adresi ile eşleşmelidir.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Yük devretme trafiği yönlendirme yöntemini test etme

1. Tüm uç noktaları çalışır durumda bırakın.
2. Tek bir istemci kullanarak, nslookup veya benzer bir yardımcı programı kullanarak şirketinizin etki alanı adınız için DNS çözümlemesi isteyin.
3. Çözümlenen IP adresinin birincil uç noktayla eşleştiğinden emin olun.
4. Uygulamanın aşağı doğru olmasını sağlamak için birincil uç noktanızı getirin veya izleme Traffic Manager dosyasını kaldırın.
5. Traffic Manager profilinin DNS yaşam süresi (TTL) değerini ve ek olarak iki dakika bekleyin. Örneğin, DNS TTL 'niz 300 saniye (5 dakika) ise yedi dakika beklemeniz gerekir.
6. DNS istemci önbelleğinizi boşaltıp nslookup kullanarak DNS çözümlemesi isteyin. Windows 'da, Ipconfig/flushdns komutuyla DNS önbelleğinizi boşaltabilirsiniz.
7. Çözümlenen IP adresinin ikincil uç noktanızla eşleştiğinden emin olun.
8. İşlemi tekrarlayarak her bir uç noktayı sırayla getirin. DNS 'nin listedeki bir sonraki bitiş noktasının IP adresini döndürdüğünden emin olun. Tüm uç noktalar kapatıldığında, birincil uç noktanın IP adresini yeniden edinmeniz gerekir.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Ağırlıklı trafik yönlendirme yöntemini test etme

1. Tüm uç noktaları çalışır durumda bırakın.
2. Tek bir istemci kullanarak, nslookup veya benzer bir yardımcı programı kullanarak şirketinizin etki alanı adınız için DNS çözümlemesi isteyin.
3. Çözümlenen IP adresinin bitiş noktalarından biriyle eşleştiğinden emin olun.
4. DNS istemci önbelleğinizi boşaltıp her bir uç nokta için 2 ve 3. adımları yineleyin. Uç noktalarınızın her biri için farklı IP adresleri döndürüldüğünü görmeniz gerekir.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Performans trafiği yönlendirme yöntemini test etme

Performans trafiği yönlendirme yöntemini etkili bir şekilde test etmek için, istemcilerin dünyanın farklı bölümlerinde yer alan istemcilere sahip olmanız gerekir. Hizmetlerinizi test etmek için kullanılabilecek farklı Azure bölgelerinde istemciler oluşturabilirsiniz. Küresel bir ağınız varsa, dünyanın diğer bölümlerinde bulunan istemcilerde uzaktan oturum açabilir ve testlerinizi buradan çalıştırabilirsiniz.

Alternatif olarak, Web tabanlı ücretsiz DNS araması ve derinlemesine hizmetleri mevcuttur. Bu araçlardan bazıları, dünyanın dört bir yanındaki çeşitli konumlardan DNS ad çözümlemesini denetleme olanağı sunar. Örnekler için "DNS araması" üzerinde arama yapın. Gomez veya Keynote gibi üçüncü taraf hizmetler, profillerin trafiği beklendiği gibi dağıttığını doğrulamak için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Traffic Manager trafik yönlendirme yöntemleri hakkında](traffic-manager-routing-methods.md)
* [Traffic Manager için performans konuları](traffic-manager-performance-considerations.md)
* [Düzeyi düşürülmüş Traffic Manager durumu için sorun giderme](traffic-manager-troubleshooting-degraded.md)
