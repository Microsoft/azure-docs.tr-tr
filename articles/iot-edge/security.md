---
title: Güvenlik çerçevesi - Azure IOT Edge | Microsoft Docs
description: Güvenlik, kimlik doğrulaması ve Azure IOT Edge geliştirmek için kullanılan ve çözümünüzü tasarlarken düşünülmesi gereken yetkilendirme standartları hakkında bilgi edinin
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 37d5288389c7b602eb0d13a736e289010d7e0f80
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208195"
---
# <a name="security-standards-for-azure-iot-edge"></a>Azure IOT Edge güvenlik standartlarını

Azure IoT Edge, verilerinizi ve analizlerinizi akıllı bir kenara taşırken devralınan riskleri ele alınmaktadır. IoT Edge güvenlik standartları, tüm Azure hizmetlerinden bekleeceğiniz korumaya göre farklı dağıtım senaryoları için esneklik sağlar. 

IoT Edge, donanımların çeşitli ve modellerinde çalışır, çeşitli işletim sistemlerini destekler ve çeşitli dağıtım senaryoları için geçerlidir. Dağıtım senaryosunun riski, çözüm sahipliğini, dağıtım coğrafi konumunu, veri duyarlılığını, gizliliği, uygulama dikey ve mevzuat gereksinimlerini içeren faktörlere bağlıdır. IoT Edge, belirli senaryolar için somut çözümler sunmak yerine, ölçek için tasarlanan iyi topraklanmış kurallara dayanan genişletilebilir bir güvenlik çerçevesidir. 
 
Bu makalede, IoT Edge güvenlik çerçevesine bir genel bakış sunulmaktadır. Daha fazla bilgi için [akıllı uç güvenliğini sağlama](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standartları

Standartlar, her ikisi de her ikisi de bir dizi güvenlik olan uygulama kolaylığını ve kolay kullanımını yükseltir. Bir güvenlik çözümü, güven oluşturmak için değerlendirme kapsamında kendisini ve dağıtıma karşı bir sanal olmaması gerekir. Azure IoT Edge güvenli hale getirmek için Framework tasarımı, daha fazla bilme ve yeniden kullanım için zaman sınanmış ve sektörde kanıtlanmış güvenlik protokollerine dayanır. 

## <a name="authentication"></a>Authentication

Bir IoT çözümünü dağıtırken, çözümünüze yalnızca güvenilir aktörlerin, cihazların ve modüllerin erişiminin olduğunu bilmeniz gerekir. Sertifika tabanlı kimlik doğrulaması, Azure IoT Edge platformu için kimlik doğrulaması için birincil mekanizmadır. Bu mekanizma, Internet Mühendisliği görev gücü (IETF) tarafından ortak anahtar altyapısını (PKiX) yöneten bir standartlar kümesinden türetilir.     

Azure IoT Edge cihazla etkileşime geçen tüm cihazlar, modüller ve aktörler, fiziksel olarak veya bir ağ bağlantısı üzerinden benzersiz sertifika kimliklerine sahip olmalıdır. Her senaryo veya bileşen sertifikayı sertifika tabanlı kimlik doğrulamasına verebilir, böylece güvenlik çerçevesinin genişletilebilirliği güvenli alternatifler sunar. 

Daha fazla bilgi için bkz. [Azure IoT Edge sertifika kullanımı](iot-edge-certs.md).

## <a name="authorization"></a>Authorization

En az ayrıcalık ilkesi, bir sistemin kullanıcılarının ve bileşenlerinin yalnızca rollerini gerçekleştirmek için gereken en düşük kaynak ve veri kümesine erişiminin olması gerektiğini belirtir. Cihazlar, modüller ve aktör yalnızca izin kapsamındaki kaynaklara ve verilere ve yalnızca mimarel olarak izin verilebilir hale geldiğinde erişmelidir. Bazı izinler yeterli ayrıcalıklara sahip yapılandırılabilir ve diğerleri mimari olarak zorlanır.  Örneğin, bazı modüllerin Azure IoT Hub 'ye bağlanma yetkisi olabilir. Ancak, bir IoT Edge cihazdaki bir modülün başka bir IoT Edge cihazdaki bir modülün ikizi 'e erişmesi neden yoktur.

Diğer yetkilendirme şemaları, sertifika imzalama hakları ve rol tabanlı erişim denetimi (RBAC) içerir. 

## <a name="attestation"></a>Kanıtlama

Kanıtlama, kötü amaçlı yazılımların algılanması ve önlenmesi açısından önemli olan yazılım bitlerinin bütünlüğünü sağlar.  Azure IOT Edge güvenlik framework üç ana kategori altında kanıtlama sınıflandırır:

* Statik kanıtlama
* Çalışma zamanı kanıtlama
* Yazılım kanıtlama

### <a name="static-attestation"></a>Statik kanıtlama

Statik kanıtlama, işletim sistemi, tüm çalışma zamanları ve yapılandırma bilgileri de dahil olmak üzere, bir cihazdaki tüm yazılımların bütünlüğünü doğrular. , Power up sırasında statik kanıtlama yapıldığından, genellikle güvenli önyükleme olarak adlandırılır. IoT Edge cihazların güvenlik çerçevesi üreticilerine genişletilir ve statik kanıtlama süreçlerini güvence altına alan güvenli donanım özelliklerini içerir. Bu süreçler güvenli önyükleme ve güvenli bellenim yükseltmesi içerir.  Silicon satıcılarıyla yakın işbirliğinde çalışmak gereksiz bellenim katmanlarını ortadan kaldırır, bu nedenle tehdit yüzeyini en aza indirir. 

### <a name="runtime-attestation"></a>Çalışma zamanı kanıtlama

Bir sistem güvenli önyükleme işlemini tamamladıktan sonra iyi tasarlanmış sistemler, kötü amaçlı yazılım ekleme ve doğru önlemler alma girişimlerini algılamamalıdır. Kötü amaçlı yazılım saldırıları sistemin bağlantı noktalarını ve arabirimlerini hedefleyebilir. Kötü amaçlı aktörlerin bir cihaza fiziksel erişimi varsa, bu cihazlar cihazın kendisini oynayabilir veya erişim kazanmak için yan kanallı saldırıları kullanabilir. Kötü amaçlı yazılım veya yetkisiz yapılandırma değişikliklerinden bağımsız olarak, bu tür bir içerik önyükleme işleminden sonra eklenmiş olduğundan statik kanıtlama tarafından algılanamayacağını belirtir. Bu tür tehditlere karşı cihazın donanım yardımı tarafından sunulan veya uygulanan önlemler.  İçin güvenlik çerçevesi, çalışma zamanı tehditlerine açık olan uzantılara açıkça çağrı IoT Edge.  

### <a name="software-attestation"></a>Yazılım kanıtlama

Akıllı uç sistemleri de dahil tüm sağlıklı sistemler, düzeltme eki ve yükseltmelere gerek duyar.  Güvenlik, güncelleştirme işlemlerinde önemlidir, aksi takdirde olası tehdit vektörleri olabilir.  İçin güvenlik çerçevesi, paketlerin kaynağını doğrulamak ve bunların doğrulanması için ölçülen ve imzalı paketler aracılığıyla güncelleştirmeler için çağrı IoT Edge.  Bu standart tüm işletim sistemleri ve uygulama yazılım bitleri için geçerlidir. 

## <a name="hardware-root-of-trust"></a>Donanım hiyerarşik güven kökü

Birçok akıllı sınır aygıtı için, özellikle potansiyel kötü amaçlı aktörler tarafından fiziksel olarak erişilebilen cihazlara donanım güvenliği, koruma için son savunma ' tır. Bu tür dağıtımlar için, yetkisiz dayanıklı donanım çok önemlidir. Azure IoT Edge, güvenli Silicon Hardware satıcıları, çeşitli risk profillerine ve dağıtım senaryolarına uyum sağlamak amacıyla, farklı donanım güvenine sahip farklı özellikleri sunacak şekilde teşvik eder. Donanım güveni, Güvenilir Platform Modülü (ISO/ıEC 11889) ve Trusted Computing Group cihaz tanımlayıcısı bileşim altyapısı (zar) gibi yaygın güvenlik protokolü standartlarından gelebilir. TrustZones ve Software Guard uzantıları (SGX) gibi güvenli şifreleme teknolojileri de donanım güveni sağlar. 

## <a name="certification"></a>Sertifika

Cihazlara Azure IoT Edge cihazları oluştururken müşterilerin bilinçli kararlar almasına yardımcı olmak için IoT Edge Framework 'ün sertifika gereksinimleri vardır.  Güvenlik talepleri ilişkin sertifikaları ve ilgili güvenlik uygulamasının doğrulama sertifikaları için bu gereksinimleri derinlerine dalın.  Örneğin, bir güvenlik talebi sertifikası, IoT Edge cihazın önyükleme saldırılarına karşı bilinen güvenli donanımı kullandığı anlamına gelir. Doğrulama sertifikası, güvenli donanımın cihazda bu değeri sunmak üzere düzgün şekilde uygulandığı anlamına gelir.  Basitlik ilkesiyle birlikte, Framework en düşük sertifika yükünü tutmaya çalışır.   

## <a name="extensibility"></a>Genişletilebilirlik

Farklı türlerde iş dönüştürmeleri sunan IoT teknolojisi sayesinde, güvenlik, gelişen senaryolara yönelik olarak paralel olarak gelişmelidir.  Azure IOT Edge güvenlik çerçevesi, sağlam bir temel üzerinde genişletilebilirlik dahil etmek için farklı boyut içinde derlemeler ile başlar: 

* Birinci taraf güvenlik hizmetleri için Azure IOT Hub cihaz sağlama hizmeti ister.
* Farklı uygulamalar için yönetilen güvenlik hizmetleri gibi üçüncü taraf hizmetler (endüstriyel veya sağlık gibi) veya teknoloji odaklı (ağ ağlarında güvenlik izleme veya Silicon Hardware kanıtlama hizmetleri gibi), zengin bir ağ ortaklarınıza.
* Eski sistemleri ile diğer güvenlik stratejileri, retrofitting eklemek gibi kimlik doğrulaması ve kimlik yönetimi için sertifikalar dışında güvenli teknolojisini kullanarak.
* Gelişen güvenli donanım teknolojileri ve Silicon partner katkıları benimseme için güvenli donanım.

Son olarak, akıllı bir kenara güvenli hale getirmek, IoT 'yi güvenli hale getirmenin yaygın ilgi çekici bir şekilde çalışan bir açık topluluk aracılığıyla işbirliğine dayalı katkıları gerektirir  Bu Katkıları güvenli teknolojiler ve hizmetler biçiminde olabilir.  Azure IOT Edge güvenlik çerçevesi, güven ve Azure bulut ile akıllı uç bütünlüğü aynı düzeyde sunmak en fazla kapsamı için genişletilebilir güvenlik için sağlam bir temel sunar.  

## <a name="next-steps"></a>Sonraki adımlar

Azure IOT Edge nasıl olduğu hakkında daha fazla okuma [akıllı uç güvenliğini sağlama](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
