---
title: Ağ gereksinimleri
description: En iyi deneyim için ağ gereksinimleri ve en iyi ağ uygulamaları
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: fd8686cc396d5fcee20590fbac8bccaf187b024d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101735956"
---
# <a name="network-requirements"></a>Ağ gereksinimleri

Azure uzaktan Işlemede iyi bir kullanıcı deneyimi için, Azure veri merkezine yönelik kararlı, düşük gecikmeli bir ağ bağlantısı kritik öneme sahiptir. Kötü ağ koşulları, bağlantı, dengesiz, jaray veya ' atlama ' holograflarını ve sunucu tarafı sahne grafiğini güncelleştirirken dikkat çekici gecikme oluşmasına neden olabilir.

## <a name="guidelines-for-network-connectivity"></a>Ağ bağlantısı için yönergeler

Tam ağ gereksinimleri, uzaktan sahne grafiğinde yapılan değişikliklerin sayısı ve sıklığı ile işlenmiş görünümün karmaşıklığı gibi belirli bir kullanım örneğine bağlıdır, ancak deneyiminizin mümkün olduğunca iyi olduğundan emin olmak için çeşitli yönergeler vardır:

* Internet bağlantınızın, ağda rekabet eden bir trafik olmadığı varsayılarak, Azure uzaktan oluşturma işleminin tek bir kullanıcı oturumunda en az **40 Mbps 'lik aşağı akış** ve **5 Mbps 'lik yukarı** akış desteklemesi gerekir. Daha iyi deneyimler için daha yüksek ücretler öneririz. 
* Düşük gecikme süresi, yüksek bant ile ve kararlı bağlantı desteklediğinden, **Wi-Fi** önerilen ağ türüdür. Bazı mobil ağlar, zayıf bir deneyime yol açabilecek bir değişim sunar. 
* **5 ghz Wi-Fi bandı** kullanmak genellikle 2,4 GHz Wi-Fi bandından daha iyi sonuçlar üretir, ancak her ikisi de çalışır.
* Yakında başka Wi-Fi ağları varsa, bu diğer ağlar tarafından kullanılan Wi-Fi kanalları kullanmaktan kaçının. Wi-Fi ağınızın kullandığı kanalların, rekabet eden trafiğin ücretsizdir olup olmadığını doğrulamak için [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) gibi ağ tarama araçlarını kullanabilirsiniz.
* **Wi-Fi repeaters** veya ağ üzerinden-Powerline iletmeyi kullanmaktan kesinlikle kaçının.
* Aynı Wi-Fi ağı üzerinde video veya oyun akışı gibi **rekabet bant genişliğine sahip trafikten kaçının** .
* Aynı erişim noktasında birden çok cihaz varsa, gereksinimler ölçeği aynı şekilde ölçeklendirilir. Bir ortamda birden çok erişim noktanız varsa, erişim noktaları genelinde cihazları yük dengeleyebilir ve böylece eşit olarak dağıtılır.
* **Sinyal gücünün iyi Wi-Fi** olması önemlidir. Mümkünse, Wi-Fi erişim noktasına yakın kalın ve istemci cihazınız ile erişim noktaları arasında engelleri önleyin.
* [Bölgeniz](regions.md)için her zaman **en yakın Azure veri merkezine** bağlandığınızdan emin olun. Veri merkezine yaklaşarak, hologram kararlılığı üzerinde büyük bir etkiye sahip olan ağ gecikmesi düşüktür.

> [!NOTE]
> Aşağı akış bant genişliği çoğunlukla video akışı tarafından tüketilecektir. Bu, sırayla renk ve derinlik bilgileri (60 Hz, stereo) arasında bölünür.

## <a name="network-performance-tests"></a>Ağ performans testleri

Ağ bağlantınızın kalitesinin Azure uzaktan Işleme çalıştırmak için yeterli olup olmadığını anlamak istiyorsanız, kullanabileceğiniz mevcut çevrimiçi araçlar vardır. Azure uzaktan Işleme istemci uygulamanızı çalıştırmayı planladığınız cihazla aynı Wi-Fi bağlı makul bir dizüstü bilgisayardan bu çevrimiçi araçların çalıştırılmasını önemle öneririz. Bir cep telefonunda veya HoloLens2 testlerin çalıştırılması sonucunda elde edilen sonuçlar, düşük güç destekli uç nokta cihazlarında önemli bir çeşitleme göstermek için kanıtlanmış bir şekilde daha az yararlıdır. Dizüstü bilgisayarı yerleştirdiğiniz konum kabaca, Azure uzaktan Işleme istemci uygulamanızı çalıştıran cihazı kullanmayı düşündüğünüz yerde kabaca olmalıdır.

Ağ bağlantınızın hızlı bir testi için birkaç basit adım aşağıda verilmiştir:

1. **Ağ bağlantınızın genel gecikme süresi ve yukarı akış/aşağı akış bant genişliği hakkında veri almak için www.speedtest.net gibi bir ağ testi aracı çalıştırın.**
Size en yakın sunucuyu seçin ve testi çalıştırın. Sunucu, Azure uzaktan Işlemenin bağlanacağı Azure veri merkezi olmayacak olsa da, sonuçta elde edilen veriler Internet bağlantınızın ve Wi-Fi 'nizin performansını anlamak için hala yararlıdır.
   * Azure uzaktan Işleme için **En düşük gereksinim** : yaklaşık 40 Mbps aşağı akış ve 5 Mbps yukarı akış.
   * Azure uzaktan Işleme için **önerilir** : yaklaşık 100 Mbps aşağı akış ve 10 Mbps yukarı akış.
Testi birden çok kez çalıştırmayı ve en kötü sonuçları almayı öneririz.
1. **Azure veri merkezlerine gecikme süresi ölçen www.azurespeed.com gibi bir araç kullanın**. Size en yakın Azure uzaktan Işleme tarafından desteklenen Azure veri merkezini seçin ( [desteklenen bölgelere](regions.md)bakın) ve bir **gecikme süresi testi** çalıştırın. Gördüğünüz sayılarda çeşitleme varsa, sonuçların sabitlemesini biraz zaman sunun.
   * Azure uzaktan Işleme için **En düşük gereksinim** : gecikme süresi sürekli olarak 80 MS 'den az olmalıdır.
   * Azure uzaktan Işleme için **önerilir** : gecikme süresi sürekli olarak 40 ms 'den az olmalıdır.

Düşük gecikme süresi, Azure uzaktan Işlemenin ağınızda iyi çalışacağı garantisi olmadığından, genellikle bu testlerin başarıyla geçirildiği durumlarda iyi bir şekilde karşılaştık.
Azure uzaktan Işleme çalıştırırken dengesiz, jtery veya atlama gibi yapıtlardan karşılaşdıysanız, [sorun giderme kılavuzuna](../resources/troubleshoot.md)bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: Unity ile model Işleme](../quickstarts/render-model.md)
