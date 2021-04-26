---
title: Güvenlik çerçevesi-Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge geliştirmek için kullanılan güvenlik, kimlik doğrulaması ve yetkilendirme standartları hakkında bilgi edinin ve çözümünüzü tasarlarken göz önünde bulundurulmalıdır
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ff154eee6c6174a8f1a3aa7bea37ef62273bb1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489511"
---
# <a name="security-standards-for-azure-iot-edge"></a>Azure IoT Edge için güvenlik standartları

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge, verilerinizi ve analizlerinizi akıllı bir kenara taşırken devralınan riskleri ele alınmaktadır. IoT Edge güvenlik standartları, tüm Azure hizmetlerinden bekleeceğiniz korumaya göre farklı dağıtım senaryoları için esneklik sağlar.

IoT Edge, donanımların çeşitli ve modellerinde çalışır, çeşitli işletim sistemlerini destekler ve çeşitli dağıtım senaryoları için geçerlidir. IoT Edge, belirli senaryolar için somut çözümler sunmak yerine, ölçek için tasarlanan iyi topraklanmış kurallara dayanan genişletilebilir bir güvenlik çerçevesidir. Dağıtım senaryosunun riski, aşağıdakiler dahil olmak üzere birçok etkene bağlıdır:

* Çözüm sahipliği
* Dağıtım Coğrafya
* Veri duyarlılığı
* Gizlilik
* Dikey uygulama
* Mevzuat gereksinimleri

Bu makalede, IoT Edge güvenlik çerçevesine bir genel bakış sunulmaktadır. Daha fazla bilgi için bkz. [akıllı kenarı güvenli hale getirme](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standartlar

Standartlar, her ikisi de her ikisi de bir dizi güvenlik olan uygulama kolaylığını ve kolay kullanımını yükseltir. Bir güvenlik çözümü, güven oluşturmak için değerlendirme kapsamında kendisini ve dağıtıma karşı bir sanal olmaması gerekir. Azure IoT Edge güvenli hale getirmek için Framework tasarımı, daha fazla bilme ve yeniden kullanım için zaman sınanmış ve sektörde kanıtlanmış güvenlik protokollerine dayanır.

## <a name="authentication"></a>Kimlik Doğrulaması

Bir IoT çözümünü dağıtırken, çözümünüze yalnızca güvenilir aktörlerin, cihazların ve modüllerin erişiminin olduğunu bilmeniz gerekir. Sertifika tabanlı kimlik doğrulaması, Azure IoT Edge platformu için kimlik doğrulaması için birincil mekanizmadır. Bu mekanizma, Internet Mühendisliği görev gücü (IETF) tarafından ortak anahtar altyapısını (PKiX) yöneten bir standartlar kümesinden türetilir.

Azure IoT Edge cihazla etkileşime geçen tüm cihazlar, modüller ve aktörler benzersiz sertifika kimliklerine sahip olmalıdır. Bu kılavuz, etkileşimlerin fiziksel mi yoksa bir ağ bağlantısı üzerinden mi olduğunu uygular. Her senaryo veya bileşen sertifikayı sertifika tabanlı kimlik doğrulamasına verebilir, böylece güvenlik çerçevesinin genişletilebilirliği güvenli alternatifler sunar.

Daha fazla bilgi için bkz. [Azure IoT Edge sertifika kullanımı](iot-edge-certs.md).

## <a name="authorization"></a>Yetkilendirme

En az ayrıcalık ilkesi, bir sistemin kullanıcılarının ve bileşenlerinin yalnızca rollerini gerçekleştirmek için gereken en düşük kaynak ve veri kümesine erişiminin olması gerektiğini belirtir. Cihazlar, modüller ve aktör yalnızca izin kapsamındaki kaynaklara ve verilere ve yalnızca mimarel olarak izin verilebilir hale geldiğinde erişmelidir. Bazı izinler yeterli ayrıcalıklara sahip yapılandırılabilir ve diğerleri mimari olarak zorlanır. Örneğin, bazı modüllerin Azure IoT Hub 'ye bağlanma yetkisi olabilir. Ancak, bir IoT Edge cihazdaki bir modülün başka bir IoT Edge cihazdaki bir modülün ikizi 'e erişmesi neden yoktur.

Diğer yetkilendirme şemaları, sertifika imzalama hakları ve rol tabanlı erişim denetimi (RBAC) içerir.

## <a name="attestation"></a>Kanıtlama

Kanıtlama, kötü amaçlı yazılımların algılanması ve önlenmesi açısından önemli olan yazılım bitlerinin bütünlüğünü sağlar. Azure IoT Edge güvenlik çerçevesi, kanıtlama üç ana kategoride sınıflandırır:

* Statik kanıtlama
* Çalışma zamanı kanıtlama
* Yazılım kanıtlama

### <a name="static-attestation"></a>Statik kanıtlama

Statik kanıtlama, işletim sistemi, tüm çalışma zamanları ve yapılandırma bilgileri de dahil olmak üzere, bir cihazdaki tüm yazılımların bütünlüğünü doğrular. , Power up sırasında statik kanıtlama yapıldığından, genellikle güvenli önyükleme olarak adlandırılır. IoT Edge cihazların güvenlik çerçevesi üreticilerine genişletilir ve statik kanıtlama süreçlerini güvence altına alan güvenli donanım özelliklerini içerir. Bu süreçler güvenli önyükleme ve güvenli bellenim yükseltmesi içerir. Silicon satıcılarıyla yakın işbirliğinde çalışmak gereksiz bellenim katmanlarını ortadan kaldırır, bu nedenle tehdit yüzeyini en aza indirir.

### <a name="runtime-attestation"></a>Çalışma zamanı kanıtlama

Bir sistem güvenli önyükleme işlemini tamamladıktan sonra iyi tasarlanmış sistemler, kötü amaçlı yazılım ekleme ve doğru önlemler alma girişimlerini algılamamalıdır. Kötü amaçlı yazılım saldırıları sistemin bağlantı noktalarını ve arabirimlerini hedefleyebilir. Kötü amaçlı aktörlerin bir cihaza fiziksel erişimi varsa, bu cihazlar cihazın kendisini oynayabilir veya erişim kazanmak için yan kanallı saldırıları kullanabilir. Kötü amaçlı yazılım veya yetkisiz yapılandırma değişikliklerinden bağımsız olarak, bu tür bir içerik önyükleme işleminden sonra eklenmiş olduğundan statik kanıtlama tarafından algılanamayacağını belirtir. Bu tür tehditlere karşı cihazın donanım yardımı tarafından sunulan veya uygulanan önlemler. İçin güvenlik çerçevesi, çalışma zamanı tehditlerine açık olan uzantılara açıkça çağrı IoT Edge.  

### <a name="software-attestation"></a>Yazılım kanıtlama

Akıllı uç sistemleri de dahil tüm sağlıklı sistemler, düzeltme eki ve yükseltmelere gerek duyar. Güvenlik, güncelleştirme işlemlerinde önemlidir, aksi takdirde olası tehdit vektörleri olabilir. İçin güvenlik çerçevesi, paketlerin kaynağını doğrulamak ve bunların doğrulanması için ölçülen ve imzalı paketler aracılığıyla güncelleştirmeler için çağrı IoT Edge. Bu standart tüm işletim sistemleri ve uygulama yazılım bitleri için geçerlidir.

## <a name="hardware-root-of-trust"></a>Güvenin donanım kökü

Birçok akıllı sınır aygıtı için, özellikle potansiyel kötü amaçlı aktörler tarafından fiziksel olarak erişilebilen cihazlara donanım güvenliği, koruma için son savunma ' tır. Bu tür dağıtımlar için, yetkisiz dayanıklı donanım çok önemlidir. Azure IoT Edge, güvenli Silicon Hardware satıcıları, çeşitli risk profillerine ve dağıtım senaryolarına uyum sağlamak amacıyla, farklı donanım güvenine sahip farklı özellikleri sunacak şekilde teşvik eder. Donanım güveni, Güvenilir Platform Modülü (ISO/ıEC 11889) ve Trusted Computing Group cihaz tanımlayıcısı bileşim altyapısı (zar) gibi yaygın güvenlik protokolü standartlarından gelebilir. TrustZones ve Software Guard uzantıları (SGX) gibi güvenli şifreleme teknolojileri de donanım güveni sağlar.

## <a name="certification"></a>Sertifikasyon

Cihazlara Azure IoT Edge cihazları oluştururken müşterilerin bilinçli kararlar almasına yardımcı olmak için IoT Edge Framework 'ün sertifika gereksinimleri vardır. Bu gereksinimlere ek olarak, güvenlik uygulamalarının doğrulanması ile ilgili güvenlik talepleri ve sertifikaların sertifikalamaları vardır. Örneğin, bir güvenlik talebi sertifikası, IoT Edge cihazın önyükleme saldırılarına karşı bilinen güvenli donanımı kullandığı anlamına gelir. Doğrulama sertifikası, güvenli donanımın cihazda bu değeri sunmak üzere düzgün şekilde uygulandığı anlamına gelir. Basitlik ilkesiyle birlikte, Framework en düşük sertifika yükünü tutmaya çalışır.

## <a name="extensibility"></a>Genişletilebilirlik

Farklı türlerde iş dönüştürmeleri sunan IoT teknolojisi sayesinde, güvenlik, gelişen senaryolara yönelik olarak paralel olarak gelişmelidir. Azure IoT Edge güvenlik çerçevesi, genişletilebilirlik içinde farklı boyutlarda bulunan bir Solid Foundation ile başlar:

* Azure IoT Hub cihaz sağlama hizmeti gibi ilk taraf güvenlik hizmetleri.
* Farklı uygulamalar için yönetilen güvenlik hizmetleri gibi üçüncü taraf hizmetler (endüstriyel veya sağlık gibi) veya teknoloji odaklı (ağ ağlarında güvenlik izleme veya Silicon Hardware kanıtlama hizmetleri gibi), zengin bir iş ortakları üzerinden.
* Eski sistemler, kimlik doğrulama ve kimlik yönetimi için sertifikalar dışında güvenli teknoloji kullanma gibi alternatif güvenlik stratejileri ile geriye dönük olarak dahil edilecek.
* Gelişen güvenli donanım teknolojileri ve Silicon partner katkıları benimseme için güvenli donanım.

Son olarak, akıllı bir kenara güvenli hale getirmek, IoT 'yi güvenli hale getirmenin yaygın ilgi çekici bir şekilde çalışan bir açık topluluk aracılığıyla işbirliğine dayalı katkıları gerektirir Bu katkıların güvenliği, güvenli teknolojiler veya hizmetler biçiminde olabilir. Azure IoT Edge güvenlik çerçevesi, Azure bulutu ile olduğu gibi akıllı kenarda aynı güven düzeyini ve bütünlüğü sunmak üzere en yüksek kapsam için genişletilebilir bir güvenlik sunar.  

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Edge [akıllı kenarın güvenliğini sağlama](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)hakkında daha fazla bilgi edinin.
