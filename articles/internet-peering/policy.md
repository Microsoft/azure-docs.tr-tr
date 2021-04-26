---
title: Microsoft eşleme ilkesi
titleSuffix: Azure
description: Microsoft eşleme ilkesi
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bee41bb8e5beb4df3086ab50499cb185a83e4efe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592339"
---
# <a name="peering-policy"></a>Eşleme ilkesi
Microsoft, sektör standartları ve en iyi uygulamalar tarafından desteklenen en iyi müşteri deneyimini sağlamak için tasarlanan seçmeli bir eşleme ilkesi sağlar ve gelecek talep ve daha fazla eşleme için ölçeklendirin. Bu nedenle, Microsoft, ilkede gerekli olduğu gibi özel durumlar yapma hakkını saklı tutar. Microsoft 'un ağınız için genel gereksinimleri aşağıdaki bölümlerde açıklanmıştır. Bunlar hem doğrudan eşleme hem de Exchange eşleme istekleri için geçerlidir. 

## <a name="technical-requirements"></a>Teknik gereksinimler

* Sıkışıklık olmadan trafiği değiş tokuş etmek için yeterli kapasiteye sahip tamamen yedekli bir ağ.
* Eşin ortak yönlendirilebilir bir otonom sistem numarası (ASN) olacaktır.
* Hem IPv4 hem de IPv6 desteklenir ve Microsoft her eşleme konumunda her iki türde oturum kurmayı bekler.
* MD5 desteklenmez.
* **ASN ayrıntıları:**

    * Microsoft, AS8075 ve AS8068, AS8069, AS12076 ile birlikte şunları yönetir. AS8075 eşlemesi olan ASNs 'lerin tam listesi için, MICROSOFT tarafından ayarlanan MICROSOFT.
    * Tüm taraflar Microsoft ile eşler, AS12076 (Express Route) kaynağından herhangi bir koşullarda yolları kabul etmemelidir ve tüm eşlere AS12076 filtrelemelidir.

* **Yönlendirme ilkesi:**
    * Eş, en az bir ortak yönlendirilebilir/24 olur.
    * Microsoft, alınan çok çıkış ayrımlarını (MED) üzerine yazacak.
    * Microsoft, rota almamayı göstermek için eşlerden BGP topluluk etiketleri almayı tercih eder.
    * Eşler, Microsoft ile eşleme oturumlarında 2000 (IPv4) ve 500 (IPv6) yollarının maksimum ön ekini ayarlamanızı öneririz.
    * Özellikle de önceden kabul etmediğiniz müddetçe, eşler Microsoft ile meslektaşlarındaki tüm konumlarda tutarlı yollar duyurmaları beklenir.
    * Genel olarak, AS8075 ile eşleme oturumları, tüm MICROSOFT yollarını duyuracaktır. Microsoft bazı bölgesel özellikleri duyurmayabilir.
    * Taraf, bir statik yol, en son çare bir yol veya diğer tarafa BGP aracılığıyla duyurulmayan bir yolun diğer tarafına trafik göndermeyecektir.
    * Eşler, filtre uygulamak amacıyla yollarını ortak bir Internet yönlendirme kayıt defteri (IÇ_VERIM_ORANı) veritabanına kaydetmek için gereklidir ve bu bilgileri güncel tutacaktır.      
    * Eşler, rota güvenliği için MANRS sektör standartlarına uyar.  Microsoft, kendi takdirine bağlı olarak şu şekilde seçim yapamayabilir: 1.) rotalar imzalı ve kayıtlı olmayan şirketlerle eşleme kurmamalıdır; 2.) geçersiz RPKı yollarını kaldırmak için; 3.) kayıtlı ve imzalı olmayan oluşturulan eşlerden yolları kabul etmemelidir. 

## <a name="operational-requirements"></a>İşletimsel gereksinimler
* Tüm teknik ve performans sorunlarını, güvenlik ihlallerini, hizmet reddi saldırılarını veya eşler ya da müşterileri içindeki başka bir kötüye kullanımı ele almak için, tam olarak bir çok 7/24 ağ Işlem Merkezi (NOC).
* Eşler, şirket etki alanı ve telefon numarasından 7/24 NOC e-postası dahil olmak üzere [Peeringdb](https://www.peeringdb.com) üzerinde tam ve güncel bir profile sahip olması beklenir. Bu bilgileri, NOC bilgileri, teknik iletişim bilgileri ve eşleme tesislerinde bulunan bu kişilerin varlığı gibi eşdüzey ayrıntılarını doğrulamak için kullanırız. Personal Yahoo, Gmail ve hotmail hesapları kabul edilmez.

## <a name="physical-connection-requirements"></a>Fiziksel bağlantı gereksinimleri
* Doğrudan eşleme veya Exchange eşlemesi için Microsoft ile bağlantı oluşturabileceğiniz konumlar, [Peeringdb](https://www.peeringdb.com/net/694) 'de listelenir

* **Exchange eşlemesi:**
    * Eşlerin Exchange 'e en az 10 GB bağlantısı olması beklenir.
    * Yoğun kullanım %50 ' ü aştığında eşler bağlantı noktalarını yükseltmesi beklenir.
    * Microsoft, yük devretme senaryolarını desteklemek üzere Exchange 'e farklı bağlantıların korunmasını sağlamak için eşleri teşvik etmektedir.

* **Doğrudan eşleme:**
    * İç bağlantı, 100 Gbps optik kullanan tek modlu fiber üzerinde olmalıdır.
    * Microsoft, yalnızca ISS veya ağ hizmeti sağlayıcılarıyla doğrudan eşleme oluşturacak.
    * Yoğun kullanım %50 ' i aştığında ve her bir metro 'da tek bir konum içinde veya bir metro 'daki çeşitli konumlarda farklı kapasiteyi korumadığında eşler bağlantı noktalarını yükseltmesi beklenir.
    * Her bir doğrudan eşleme, eşin kenarında bulunan eşler arası yönlendiricilerden iki Microsoft Edge yönlendiricilerine iki bağlantılardan oluşur. Microsoft bu bağlantılar genelinde iki BGP oturumu gerektirir. Eş, yedekli cihazları kendi sonunda dağıtmayabilir.


## <a name="traffic-requirements"></a>Trafik gereksinimleri

* Exchange eşlemesi üzerindeki Peers en az 500 MB trafik ve 2 GB 'den az olmalıdır. 2 GB doğrudan eşleme 'nin aşıldığı trafik için oluşturulmalıdır.
* Microsoft, doğrudan eşleme için en az 2 GB gerektirir. Eşleme konumu birbirini kabul eden her ikisi de, yük devretme senaryosu sırasında eşlemenin yerelleştirilmesini sağlayan yük devretmeyi desteklemelidir. 

## <a name="next-steps"></a>Sonraki adımlar

* Microsoft ile doğrudan eşleme ayarlama adımları hakkında bilgi edinmek için [doğrudan eşleme](walkthrough-direct-all.md)Kılavuzu ' nu izleyin.
* Exchange eşlemesini Microsoft ile ayarlamaya yönelik adımlar hakkında bilgi edinmek için [Exchange eşleme](walkthrough-exchange-all.md)Kılavuzu ' nu izleyin.
