---
title: include dosyası
description: include dosyası
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793120"
---
Nesnelerin İnterneti (IoT) altyapısının güvenliğini sağlamak, ayrıntılı bir güvenlik kapsamlı strateji gerektirir. Bu strateji, bulutta verilerin güvenliğini sağlamanızı, genel İnternet üzerinden aktarım sırasında veri bütünlüğünü korumanızı ve cihazları güvenli bir şekilde sağlamanızı gerektirir. Her katman, genel altyapıda daha fazla güvenlik güvencesi oluşturur.

## <a name="secure-an-iot-infrastructure"></a>IoT altyapısının güvenliğini sağlama

Bu derinlemesine güvenlik stratejisi, IoT cihazlarının ve altyapısının üretimi, geliştirilmesi ve dağıtılması ile ilgili çeşitli oyuncuların etkin katılımı ile geliştirilebilir ve Yürütülebilirler. Aşağıda bu oyuncuların üst düzey bir açıklaması verilmiştir.

* **IoT donanım üreticisi/tümleştirici**: genellikle bu oyuncular dağıtılan IoT donanımının üreticileri, çeşitli üreticilerden donanım montaj tümleştiricileri veya IoT dağıtımı için donanım sağlayan tedarikçilerle ya da diğer tedarikçilerle tümleşiktir.

* **IoT çözüm geliştiricisi**: bir IoT çözümünün geliştirilmesi genellikle bir çözüm geliştiricisi tarafından yapılır. Bu geliştirici, bu etkinlikte bir şirket içi ekibin veya Sistem Tümleştirici (sı) 'nin bir parçası olabilir. IoT çözüm geliştiricisi, IoT çözümünün çeşitli bileşenlerini sıfırdan geliştirebilir, çeşitli raf veya açık kaynaklı bileşenleri tümleştirebilir veya küçük bir uyarlama ile çözüm hızlandırıcılarını benimseyebilirler.

* **IoT çözüm dağıtıcı**: bir IoT çözümü geliştirildiğinde, bu, alanına dağıtılması gerekir. Bu işlem, donanım cihazlarındaki veya buluttaki çözümlerin dağıtımını, cihazların iç bağlantısı ve çözümlerin dağıtımını içerir.

* **IoT çözüm operatörü**: IoT çözümü dağıtıldıktan sonra uzun süreli işlemler, izleme, yükseltmeler ve bakım gerektirir. Bu görevler, bilgi teknolojisi uzmanları, donanım işlemleri ve bakım takımları ve genel IoT altyapısının doğru davranışını izleyen etki alanı uzmanları içeren bir şirket içi ekip tarafından yapılabilir.

Aşağıdaki bölümler, bu oyuncuların her biri için güvenli bir IoT altyapısı geliştirmeye, dağıtmaya ve çalıştırmaya yardımcı olmak üzere en iyi uygulamaları sunmaktadır.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT donanım üreticisi/tümleştirici

IoT donanım üreticileri ve donanım tümleştiricileri için en iyi uygulamalar aşağıda verilmiştir.

* **En düşük gereksinimlere sahip donanım kapsamı**: donanım tasarımı, donanımın çalışması için gereken en düşük özellikleri ve hiçbir şeyi içermelidir. Yalnızca cihazın çalışması için gerekliyse USB bağlantı noktalarını dahil etmek bir örnektir. Bu ek özellikler, kaçınılması gereken istenmeyen saldırı vektörlerine yönelik cihazı açar.

* **Donanım üzerinde oynama sağlaması yapın**: cihaz kapağını açma veya cihazın bir kısmını kaldırma gibi fiziksel değişiklik algılama mekanizmaları oluşturun. Bu izin sinyalleri, buluta yüklenen veri akışının bir parçası olabilir ve bu olayların işleçlerini uyarabilir.

* **Güvenli donanım etrafında derleyin**: SMM izin veriyorsa, güvenli ve şifreli depolama gibi güvenlik özellikleri veya GÜVENILIR Platform Modülü (TPM) tabanlı önyükleme işlevleri oluşturun. Bu özellikler, cihazları daha güvenli hale getirir ve genel IoT altyapısının korunmasına yardımcı olur.

* **Yükseltmeleri güvenli yapın**: cihazın ömrü boyunca bellenim yükseltmeleri, çok daha fazla tablodur. Bellenim sürümlerinin yükseltmeleri ve şifreleme Güvencesi için güvenli yollarla cihaz oluşturma, cihazın yükseltmelerde ve sonrasında güvenli olmasını sağlar.

## <a name="iot-solution-developer"></a>IoT çözüm geliştiricisi

IoT çözüm geliştiricileri için en iyi uygulamalar şunlardır:

* **Güvenli yazılım geliştirme metodolojisini takip edin**: güvenli yazılım geliştirme, projenin uygulama, test ve dağıtımına kadar her şey için, güvenlik hakkında bilgi sahibi olmanızı gerektirir. Platformların, dillerin ve araçların seçimleri bu metodolojide etkilendi. Microsoft güvenlik geliştirme yaşam döngüsü, güvenli yazılım oluşturmaya yönelik adım adım bir yaklaşım sağlar.

* **Dikkatli açık kaynak yazılım seçin**: açık kaynaklı yazılım, çözümleri hızlı bir şekilde geliştirmeye yönelik bir fırsat sağlar. Açık kaynaklı yazılım seçerken, her bir açık kaynaklı bileşen için topluluğun etkinlik düzeyini göz önünde bulundurun. Etkin bir topluluk, yazılımın desteklendiğini ve sorunların keşfedildiğini ve sağlandığını sağlar. Alternatif olarak, gizlemeli ve etkin olmayan bir açık kaynaklı yazılım projesi desteklenmeyebilir ve sorunlar büyük olasılıkla bulunamamalıdır.

* **Dikkatli tümleştirin**: kitaplıkların ve API 'lerin sınırında birçok yazılım güvenlik kusuri vardır. Geçerli dağıtım için gerekli olmayabilir işlevselliği bir API katmanı ile hala kullanılabilir olabilir. Genel güvenliği sağlamak için, güvenlik kusurda tümleştirildiği bileşenlerin tüm arabirimlerini denetlediğinizden emin olun.

## <a name="iot-solution-deployer"></a>IoT çözüm dağıtıcı

IoT çözüm dağıtımcıları için en iyi yöntemler şunlardır:

* **Donanımı güvenli bir şekilde dağıtın**: IoT dağıtımları, donanımın, genel boşluklar veya denetimli yerel ayarlarda olduğu gibi güvenli olmayan konumlara dağıtılması gerekebilir. Bu gibi durumlarda, donanım dağıtımının en yüksek ölçüde mümkün olup olmadığından emin olun. Donanımda USB veya diğer bağlantı noktaları varsa, bunların güvenli bir şekilde kapsandıklarından emin olun. Birçok saldırı vektörü, bunları giriş noktaları olarak kullanabilir.

* **Kimlik doğrulama anahtarlarını güvenli tut**: dağıtım sırasında her cihaz, bulut hizmeti tarafından oluşturulan cihaz kimliklerini ve ilişkili kimlik doğrulama anahtarlarını gerektirir. Dağıtımdan sonra bile bu anahtarları fiziksel olarak güvende tutun. Kötü amaçlı bir cihaz, var olan bir cihaz olarak maske sağlamak için kullanılabilir.

## <a name="iot-solution-operator"></a>IoT çözüm operatörü

IoT çözüm işleçleri için en iyi uygulamalar şunlardır:

* **Sistemi güncel tutun**: cihaz işletim sistemlerinin ve tüm cihaz sürücülerinin en son sürümlere yükseltildiğinden emin olun. Windows 10 ' da (IoT veya diğer SKU 'Lar) otomatik güncelleştirmeleri açarsanız, Microsoft, IoT cihazları için güvenli bir işletim sistemi sağlayan BT 'yi güncel tutar. Diğer işletim sistemlerinin (Linux gibi) güncel tutulması, kötü niyetli saldırılara karşı korunmasını sağlar.

* **Kötü amaçlı etkinliğe karşı koruma**: işletim sistemi izin veriyorsa, her cihaz işletim sistemine en son virüsten koruma ve kötü amaçlı yazılımdan koruma özelliklerini yükler. Bu uygulama, çoğu dış tehdidi azaltmaya yardımcı olabilir. Uygun adımları gerçekleştirerek, çoğu modern işletim sistemini tehditlere karşı koruyabilirsiniz.

* **Denetim genellikle**: güvenlik olaylarına yanıt verme sırasında güvenlikle ilgili sorunlar için IoT altyapısını denetleme anahtarıdır. Çoğu işletim sistemi, güvenlik ihlali gerçekleşmeyen emin olmak için sık incelenmesi gereken yerleşik olay günlüğü sağlar. Denetim bilgileri, bulut hizmetine çözümlenebileceği ayrı bir telemetri akışı olarak gönderilebilir.

* **IoT altyapısını fiziksel olarak koruyun**: IoT altyapısına karşı en kötü güvenlik saldırıları cihazlara fiziksel erişim kullanılarak başlatılır. Önemli bir güvenlik uygulaması, USB bağlantı noktalarının ve diğer fiziksel erişimin kötü amaçlı kullanımına karşı koruma sağlar. Oluşmuş olabilecek ihlallerinin kapsamadan bir anahtar, USB bağlantı noktası kullanımı gibi fiziksel erişimin günlüğe kaydedilmesini sağlar. Yine, Windows 10 (IoT ve diğer SKU 'Lar) bu olayların ayrıntılı günlüğe kaydedilmesini mümkün bir şekilde sunar.

* **Bulut kimlik bilgilerini koruma**: IoT dağıtımını yapılandırmak ve çalıştırmak için kullanılan bulut kimlik doğrulama kimlik bilgileri, büyük olasılıkla bir IoT sistemine erişmek ve bu sisteme zarar vermek için en kolay yoldur. Parolayı sık sık değiştirerek kimlik bilgilerini koruyun ve bu kimlik bilgilerini genel makinelerde kullanmaktan kaçının.

Farklı IoT cihazlarının özellikleri farklılık gösterir. Bazı cihazlar ortak masaüstü işletim sistemlerini çalıştıran bilgisayarlar olabilir ve bazı cihazlar çok hafif işletim sistemlerini çalıştırıyor olabilir. Daha önce açıklanan en iyi güvenlik uygulamaları, bu cihazlar için değişen derecelerde uygulanabilir olabilir. Sağlanmışsa, bu cihazların üreticilerinden ek güvenlik ve dağıtım en iyi uygulamaları izlenmelidir.

Bazı eski ve kısıtlanmış cihazlar IoT dağıtımı için özel olarak tasarlanmayabilir. Bu cihazlar, verileri şifrelemek, Internet 'e bağlanmak veya Gelişmiş denetim sağlamak için yeterli olanalabilir. Bu durumlarda, modern ve güvenli bir alan ağ geçidi eski cihazlardan verileri toplayabilir ve bu cihazları Internet üzerinden bağlamak için gereken güvenliği sağlayabilir. Alan ağ geçitleri, güvenli kimlik doğrulaması, şifreli oturumların anlaşması, buluttan komutların alınması ve diğer birçok güvenlik özelliği sağlayabilir.