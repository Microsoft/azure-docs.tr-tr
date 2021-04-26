---
title: Mesajlaşma Ilkesi
titleSuffix: An Azure Communication Services concept document
description: SMS mesajlaşma ilkeleri hakkında bilgi edinin.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/19/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bb9765c2620f45d67bf888f8bfe8a4dee450cfd6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646888"
---
# <a name="azure-communication-services-messaging-policy"></a>Azure Iletişim Hizmetleri Ileti Ilkesi

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Iletişim Hizmetleri, Microsoft ekiplerini ve Skype 'ı geri yükleyen kurumsal sınıf hizmetlerden yararlanan zengin, özel iletişim deneyimleri oluşturarak müşterilerimizin istemcilerle karşılaştığı şekilde dönüştürülüyor. Müşterilerinize her zaman ve desteğe ihtiyaç duydukları her yerden ulaşmak için SMS mesajlaşma işlevselliğini iletişim çözümlerinizle tümleştirin. Başlamak için birkaç mesajlaşma gereksinimini göz önünde bulundurmanız yeterlidir.

Mesajlaşma gereksinimlerinin öğrenilmesi, ancak "COMS" anımsanacak kadar kolay olduğunu biliyoruz.

- C-onay
- O-Opt-Out
- A-Ileti Içeriği
- S-sahtekarlığı

Bu mesajlaşma ilkesini, yasal gereksinimleri karşılamanıza ve önerilen en iyi yöntemlerle hizalamanıza yardımcı olmak için geliştirdik. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Onay 

### <a name="what-is-consent"></a>İzin nedir?

Onay, sizin ve ileti alıcısı arasında otomatik iletiler göndermenizi sağlayan bir anlaşmadır. İlk iletiyi göndermeden önce onay edinmeniz gerekir ve alıcının sizi ileti almak için kabul etmiş olduklarını açık hale getiriyorsunuz. Bu yordam, iletinizi planladığınız bireden "önceki hızlı onay" alma olarak bilinir.

Gönderdiğiniz iletilerin, alıcının almak için kabul ettiğverdiği ileti türü ve yalnızca alıcının size verdiği numaraya gönderilmesi gerekir. Randevu anımsatıcıları veya uyarıları gibi bilgilendirici iletiler göndermek istiyorsanız, izin yazılabilir veya oral olabilir. Bir ürünü veya hizmeti destekleyen satış veya pazarlama iletileri gibi promosyon iletileri göndermek istiyorsanız, izin yazılması gerekir.

### <a name="how-do-you-obtain-consent"></a>Onay nasıl elde edersiniz?

Onay, çeşitli yollarla elde edilebilir:

- bir Kullanıcı bir Web sitesine telefon numarası girdiğinde, 
- bir Kullanıcı bir SMS mesajı alışverişi başlattığında veya
- bir kullanıcı telefon numaranız için bir kaydolma anahtar sözcüğü gönderdiğinde. 
 
İzin alma şeklinden bağımsız olarak, siz ve müşterilerinizin onay olmadığından emin olmanız gerekir. Onay kapsamı alıcıya açık olmalıdır.


### <a name="consent-requirements"></a>Onay gereksinimleri:

- Onayı almadan önce bir "eyleme çağrı" sağlayın. Siz ve müşterileriniz, ileti alıcılarına "eylem çağrısı" yapan ve bunları ileti programınızı kabul etmek üzere davet eden olası ileti alıcıları sağlamalıdır. Yapılacak çağrı en az bir değer içermelidir: (1) ileti göndericisinin kimliği, (2) kabul etme yönergeleri, (3) geri çevirme yönergeleri ve (4) ilişkili tüm mesajlaşma ücretleri.
- İzin aktarılamaz veya atanabilir değil. Bir bireyin sizin için sağladığı her türlü onay, bağlantılı olmayan bir üçüncü tarafa aktarılamaz veya satılamaz. Bir üçüncü taraf için bireyin iznini topluyorsanız, üçüncü taraf için bireyin açıkça tanımlamalısınız. Ayrıca, aldığınız izin yalnızca üçüncü taraf iletişimler için geçerlidir.
- Onay, amaç açısından sınırlıdır. Belirli bir amaç için numaralarını, yalnızca belirli bir amaca ve bu belirli ileti göndericiden gelen iletişimleri alacak şekilde sağlayan bir kişi. Onayı almadan önce, bir bağlı olarak yinelenen iletiler veya iletiler gönderiyorsanız, istenen ileti alıcısını açıkça bilgilendirmelisiniz.

### <a name="consent-best-practices"></a>İzin en iyi yöntemleri:

Yukarıda açıklanan mesajlaşma gereksinimlerine ek olarak, aşağıdakiler de dahil olmak üzere birkaç yaygın en iyi yöntem uygulamak isteyebilirsiniz: 

- Ayrıntılı "eyleme çağrı" bilgileri. Uygun izin aldığınızdan emin olmak için şunu sağlayın
  - Mesajlaşma programınızın veya ürününüzün adı veya açıklaması
  - alıcıların iletileri alacağı sayı (ler) ve 
  - bir bireyin gelen ve onlardan ileti almak için geçerli hüküm ve koşullar.
- Kesin onay kayıtları. Bir kişinin size en az dört yıl boyunca sağladığı her türlü onay kaydını tutmanız gerekir. Onay kayıtları şunlar olabilir:
  - zaman damgaları
  - izin alınan orta
  - izin alınan belirli kampanya
  - ekran yakalamaları
  - bireyin oturum KIMLIĞI veya IP adresi.
- Gizlilik ve güvenlik ilkeleri. Geliştiricilerin, izin alınmadan önce ileti alıcılarının gözden geçirebildiği basit gizlilik ilkeleri sağlaması önerilir. Ayrıca, kişilerin özel bilgilerini korumak için proaktif güvenlik denetimlerinin sürdürülmesi önerilir.


## <a name="double-opt-in-consent"></a>Çift kabul etme onayı:

Azure Iletişim Hizmetleri tüm mesajlaşma kampanyaları için çift kabul onayı kullanmanızı önerir. Çift kabul etme onayı, bir bireyin ilk olarak belirli türden iletileri alma onayı sağlayan iki adımlı bir işlemdir. Daha sonra bunun onayını onaylamak için bir izleme katılım iletisi gönderirsiniz. İleti alıcısı onay onayını onayladığında daha fazla ileti göndermeniz gerekir.

Göndereceğiniz ilk doğrulama iletisi, kimliğinizi, gelecekteki iletileri ("durdurma" komutu kullanımı), ücretsiz bir numarayı veya "yardım" komutunu, ek bilgiler için ücretsiz bir numara veya "yardım" komutunu, her birinin yinelenen bir ileti programına kaydolduğunu belirten bildirimi, programın kısa bir açıklamasını, yinelenen iletileri göndermeyi planladığınız sıklığı içermelidir ve tüm ilişkili ücretler. 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>Azure Iletişim Hizmetleri, her zaman çift katılım onayı gerektirsin mi?
Evet, çift kabul etme izni her zaman önerilir, Azure Iletişim Hizmetleri, kimlik avı düzenlerinde sık kullanılan kullanımları veya müşteri şikayetleriyle sonuçlanmasının nedeni, bazı ileti kampanyası türleri için çift kabul etme onayı kullanmanızı gerektirir. Bu kampanyalar şunları içerir:
- Otomatik garanti iletileri
- Kısa vadeli sağlık sigortası planları
- Bir mali kurum tarafından yapılmayan borç yeniden finansmanı veya faiz oranı azaltma iletileri
- Lider oluşturma iletileri
- Sweepstakes, yarışmalar ve giveter
- Evden çalışma teklifleri

Çift kabul etme izninin gerekli olduğu kampanyalar, Azure Iletişim Hizmetleri açısından değiştirilebilir.

### <a name="exceptions-to-traditional-consent-rules"></a>Geleneksel onay kuralları için özel durumlar:
Bir ileti gönderilmeden önce, önceki hızlı onay normalde gerekli olsa da, bir bireyin izin verinin açık olduğu iki durum vardır.

- Alıcı bir iletişim başlatır. Bir kişi size ileti göndererek bir iletişim başlatırsa, iletide bulunan belirli bir sorgu veya isteğe yanıt olarak ilgili bilgileri sağlayabilirsiniz. Bununla birlikte, sunulan bireyin, daha fazla iletişim için izin edinmediğiniz müddetçe, tek tek tarafından başlatılan görüşme ile sınırlıdır.
- Belirli hizmetler için muafiyet. İleti başlatma izniniz olan birkaç belirli hizmet vardır. Bunların en yaygın olarak bazıları şunlardır: 
- paket teslim iletileri
- zamana duyarlı konulara (potansiyel olarak sahte işlemler veya veri ihlalleri gibi) sorun veren finans kurumu iletileri
- zamana duyarlı bilgileri ve bir işleme amacını (örneğin, randevu veya sınava anımsatıcıları, laboratuvar sonuçları ve ilgili bildirimler) içeren sağlık sağlayıcısı iletileri. 
 
Bu iletilerden hiçbiri, bağlantıları veya reklamları içerebilir.


## <a name="opt-out"></a>Devre dışı bırak

İleti alıcıları onayı iptal edebilir ve gelecekteki iletileri makul yollarla almayı geri alabilir. İleti alıcılarının onayı iptal etmeleri için özel bir yol belirleyemeyebilirsiniz. 

### <a name="opt-out-requirements"></a>Kabul etme gereksinimleri:

İleti alıcılarının, gelecekteki iletileri dilediğiniz zaman devre dışı bırakabilirsiniz. Ayrıca, birden çok kabul etme seçeneği de sunmalısınız. İleti alıcısından sonra, bireysel olarak yenilenmiş onay sunmadığı takdirde ek iletiler göndermemelisiniz.

En yaygın olarak kullanılan kabul eden mekanizmalardan biri, her yeni görüşmenin ilk iletisinde bir "STOP" anahtar sözcüğünü içermelidir. "Abonelik" veya "iptal" gibi küçük harfli "Durdur" veya diğer yaygın anahtar sözcüklerle yanıt veren müşterileri kaldırmaya hazırlıklı olun. Bir bireyin onay verdikten sonra, belirli bir programdan ileti almaya devam etmeyi tercih etmedikleri müddetçe, bunları yinelenen tüm mesajlaşma kampanyalarından kaldırmanız gerekir.

### <a name="opt-out-best-practices"></a>En iyi uygulamaları kabul etme:

Anahtar sözcüklere ek olarak, diğer yaygın kabul etme mekanizmaları, müşterilerin belirli bir geri alma e-posta adresi, müşteri destek personeli telefon numarası veya Web sayfanıza aboneliği kaldırma bağlantısı sağlayan bir bağlantı içerir. 


### <a name="how-we-handle-opt-out-requests"></a>Kabul etme isteklerini nasıl işleyeceğiz:

Tek bir Azure Iletişim hizmetlerinde gelecek iletileri ücretsiz olarak devre dışı bırakmak isterse, bu sayıdan gelen tüm trafik otomatik olarak durdurulur. Ancak, bu mesajlaşma kampanyası için yeni veya farklı numaralardan ek iletiler gönderdiğinizden emin olmanız gerekir. Farklı bir mesajlaşma kampanyası için ayrı olarak hızlı onay aldıysanız, bu kampanya için farklı bir sayıdan ileti gönderilmeye devam edebilirsiniz. Geri [alma işlemi](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services) hakkında daha fazla bilgi edınmek için SSS bölümüne göz atın

## <a name="message-content"></a>İleti içeriği

### <a name="adult-content"></a>Yetişkin içeriği:

Cinsel, nefret, alkol, firekoller, tütün, kumar veya sweve yarışmalar öğelerini içeren ileti içeriği ek gereksinimleri tetikleyebilir. Bu içerik, bazı dairelerde açıkça yasaktır. Bu içeriği içeren bir ileti gönderirseniz, bu durumda iletişimin alındığı vergi daireleri için geçerli olan tüm yasaların yasaları ile harsat olur. Yasa zorlaması veya Azure Iletişim Hizmetleri isteğinde, yetişkinlere yönelik içeriği düzenleyen yerel yasalar için onay kanıtı sağlamaya hazır olmanız gerekir.

Böyle içerikler sınırsız olmasa bile, yetişkinlere yönelik içeriklerden istenen ileti alıcısının yaş kapısını kabul etmek için bir yaş doğrulama mekanizması dahil etmelisiniz. Birleşik Devletler, ek yasal gereksinimler, 13 yaşın altındaki alt öğelere yönlendirilmiş pazarlama iletişimleri için geçerlidir. 

### <a name="prohibited-content"></a>Yasaklanmış içerik:

Azure Iletişim Hizmetleri, onay olmadan bağımsız olarak belirli ileti içeriğini yasaklar. Yasaklanmış içerik şunları içerir:
- Avsuz Etkinlikleri (örn. vergi Evasion veya Birleşik Devletler bir hayvan Cruelty) destekleyen içerik
- Nefret eden konuşma, mazmatory konuşma, tacsment veya diğer bir konuşma patently rahatsız edici olduğunu belirledi
- Pornografik içeriği
- Obsahne veya kaba içerik
- Initialmiber ve tehditler
- Erteçud, deceive, zarar verme veya yanlışlıkla bir değer elde etmek için gereken içerik 
- Zarar, ayrımcılığı veya şiddet kullanan içerikler
- Kötü amaçlı yazılımı yayın içeriği
- Atlatabilir süresi aşımları gereksinimlerini amaçlayan içerik

Yasaklanmış ileti içeriği listesini dilediğiniz zaman değiştirme hakkını saklı tutarız.

## <a name="spoofing"></a>Spoofing (Kimlik Sahtekarlığı)

Aldatmacası, bir ileti alıcısının cihazında yanıltıcı veya hatalı bir kaynak numarasının görüntülenmesine neden olur. Sizi ve sahte iletiler göndermeden kullandığınız hizmet sağlayıcınızı kesinlikle yaptık. İleti göndericisinin kimliğini yanıltma kalkan ve ileti alıcılarının istenmeyen iletişimleri kolayca gerçekleştirmesini engeller. Ayrıca, tüm geçerli sahtekarlık yasaları tarafından IDE 'yi de gerektiririz.

## <a name="final-thoughts"></a>Son düşünce

### <a name="legal-responsibility"></a>Yasal sorumluluk:

Bu mesajlaşma Ilkesi yasal tavsiyeler oluşturmaz ve ilkeyi dilediğiniz zaman değiştirme hakkını saklı tutarız. Azure Iletişim Hizmetleri, müşterilerin iletilerinin içeriğinin, zamanlamaların veya alıcıların tüm geçerli yasal gereksinimleri karşıladığından emin olmak için sorumlu değildir. 

Müşterilerimiz tüm mesajlaşma gereksinimlerinden sorumludur. Mesajlaşma amaçları için Azure Iletişim Hizmetleri 'ni kullanan bir platform veya yazılım sağlayıcısıysanız, müşterilerinizin bu mesajlaşma Ilkesinde ele alınan tüm gereksinimlere göre de bir IDE olması gerekir. Daha fazla rehberlik için CTıO, yararlı [mesajlaşma ilkeleri ve En Iyi uygulamalar](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf)sağlar.

### <a name="penalties"></a>Ceza

Müşterilerimizin tüm mesajlaşma gereksinimleriyle uyumluluğunu sağlamak için tasarlanan ilkeleri ve yordamları geliştirip uygulamamızı öneririz. Mesajlaşma gereksinimlerinin ihlalleri, hızla balon karabilir önemli ölçüde ortaya çıkabilir. Geçerli tüm mesajlaşma gereksinimlerine göre bilgi edinmek ve bunları öğrenmek ve yayılmaları öncesinde ihlalleri içermesi ve ortadan kaldırmak için etkili risk azaltma korumaları geliştirmek için en iyi ilgilendiğiniz bir yöntemdir. Mesajlaşma Ilkenizi veya diğer yasal gereksinimlerinizi ihlal ederseniz gelecek uyumluluğu sağlamak için sizinle birlikte çalışacağız. Ancak, Azure Iletişim Hizmetleri platformundan, mesajlaşma Ilkenize veya yasal gereksinimlerimize yönelik uyumsuzluk düzenlerini gösteren herhangi bir müşteriyi kaldırma hakkını saklı tutarız.
