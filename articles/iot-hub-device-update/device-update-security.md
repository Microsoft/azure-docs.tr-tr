---
title: Azure IoT Hub için cihaz güncelleştirmesine yönelik güvenlik | Microsoft Docs
description: Cihaz güncelleştirme IoT Hub cihazların güvenli bir şekilde güncelleştirilmesini sağlar.
author: lichris
ms.author: lichris
ms.date: 4/15/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: b10049e03e26cfe8da2bd57cc9f69dd933af706b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567307"
---
# <a name="device-update-security-model"></a>Cihaz güncelleştirme güvenlik modeli

IoT Hub cihaz güncelleştirmesi, IoT cihazlarınıza cihaz üretici yazılımı, görüntüler ve uygulamalar için güncelleştirmeleri dağıtmak üzere güvenli bir yöntem sunar. İş akışı, bir cihazın bir güncelleştirmenin güvenilir, değiştirilmemiş ve bilerek olduğunu kanıtlamak için kullanabileceği tam bir koruyucu zinciri modeliyle uçtan uca güvenli bir kanal sağlar.

Cihaz güncelleştirme iş akışındaki her adım, işlem hattındaki her adımın bir sonrakine güvenli bir iletim gerçekleştirdiğinden emin olmak için çeşitli güvenlik özellikleri ve süreçleriyle korunur. Cihaz güncelleştirme istemcisi, tüm ilgili güncelleştirme isteklerini tanımlar ve düzgün bir şekilde yönetir. İstemci Ayrıca içeriğin güvenilir, değiştirilmemiş ve bilerek yapıldığından emin olmak için her indirmeyi denetler.

## <a name="for-solution-operators"></a>Çözüm Işleçleri için

Çözüm Işletmenleri güncelleştirmeleri cihaz güncelleştirme örneğiyle içeri aktarırken, hizmet, güncelleştirme ikili dosyalarını karşıya yükleyerek, kötü niyetli bir kullanıcı tarafından değiştirilmemesini veya yerleştirmemesini sağlar. Bir kez doğrulandıktan sonra, cihaz güncelleştirme hizmeti içeri aktarma bildiriminden ve diğer meta verilerden dosya karmalarına sahip bir iç [güncelleştirme bildirimi](./update-manifest.md) oluşturur. Bu güncelleştirme bildirimi daha sonra cihaz güncelleştirme hizmeti tarafından imzalanır.

Hizmete giriş yapıldıktan ve Azure 'da depolandıktan sonra, güncelleştirme ikili dosyaları ve ilişkili müşteri meta verileri Azure depolama hizmeti tarafından geri kalanında otomatik olarak şifrelenir. Cihaz Güncelleştirme hizmeti otomatik olarak ek şifreleme sağlamaz, ancak içerik cihaz güncelleştirme hizmetine ulaşmadan önce geliştiricilerin içeriği şifrelemesine izin verir.

Çözüm Işletmeni bir cihazı güncelleştirmek istediğinde, korunan IoT Hub kanal üzerinden cihaza imzalı bir ileti gönderilir. İsteğin imzası, cihazın Cihaz Güncelleştirme Aracısı tarafından gerçek olarak onaylanır. 

Ortaya çıkan herhangi bir ikili indirmenin, güncelleştirme bildirimi imzasının doğrulaması aracılığıyla güvenliği sağlanır. Güncelleştirme bildirimi ikili dosya karmalarını içerir, bu nedenle bildirim güvenilir olduktan sonra cihaz Güncelleştirme Aracısı karmalar ile güvenir ve ikililerin ikililer ile eşleşir. Güncelleştirme ikilisi indirilip doğrulandıktan sonra cihazdaki yükleyiciye güvenle gönderilir.

## <a name="for-device-builders"></a>Cihaz oluşturucular için

Cihaz Güncelleştirme hizmetinin basit, düşük performanslı cihazlara ölçeklendirdiğinden emin olmak için güvenlik modeli, ham asimetrik anahtarlar ve ham imzalar kullanır. JSON Web belirteçleri & JSON Web belirteçleri gibi JSON tabanlı biçimleri kullanır.

### <a name="securing-update-content-via-the-update-manifest"></a>Güncelleştirme listesi aracılığıyla güncelleştirme içeriğini güvenli hale getirme

Güncelleştirme bildirimi iki imza kullanılarak onaylanır. İmzalar, *imzalama* anahtarları ve *kök* anahtarlar içeren bir yapı kullanılarak oluşturulur.

Cihaz Güncelleştirme Aracısı, tüm cihaz güncelleştirme uyumlu cihazlar için kullanılan gömülü ortak anahtarlara sahiptir. Bunlar *kök* anahtarlardır. Karşılık gelen özel anahtarlar Microsoft tarafından denetlenir.

Microsoft ayrıca, cihaz güncelleştirme aracısına dahil olmayan veya cihazda depolanan bir ortak/özel anahtar çifti de oluşturur. Bu, *imzalama* anahtarıdır.

Bir güncelleştirme IoT Hub için cihaz güncelleştirmesine aktarıldığında ve güncelleştirme bildirimi hizmet tarafından oluşturulursa, hizmet, imzalama anahtarını kullanarak bildirimi imzalar ve kök anahtar tarafından imzalanan İmzalama anahtarının kendisini içerir. Güncelleştirme bildirimi cihaza gönderildiğinde, cihaz Güncelleştirme Aracısı aşağıdaki imza verilerini alır:

1. İmza değeri.
2. #1 oluşturmak için kullanılan algoritma.
3. #1 oluşturmak için kullanılan İmzalama anahtarının ortak anahtar bilgileri.
4. #3 içindeki ortak İmzalama anahtarının imzası.
5. #3 oluşturmak için kullanılan kök anahtarın ortak anahtar KIMLIĞI.
6. #4 oluşturmak için kullanılan algoritma.

Cihaz Güncelleştirme Aracısı, ortak İmzalama anahtarının imzasının kök anahtar tarafından imzalandığını doğrulamak için yukarıda tanımlanan bilgileri kullanır. Cihaz Güncelleştirme Aracısı daha sonra güncelleştirme bildirimi imzasının imzalama anahtarı tarafından imzalandığını doğrular. Tüm imzalar doğruysa, güncelleştirme bildirimine Cihaz Güncelleştirme Aracısı tarafından güvenililir. Güncelleştirme bildirimi, güncelleştirme dosyalarına karşılık gelen dosya karmalarını içerdiğinden, karma değerleri eşleşiyorsa güncelleştirme dosyaları da güvenilir olabilir.

Kök ve imzalama anahtarlarının olması, Microsoft 'un bir güvenlik en iyi uygulaması olan imzalama anahtarını düzenli olarak almasına izin verir.

### <a name="json-web-signature-jws"></a>JSON Web Imzası (JWS)

, `updateManifestSignature` İçinde yer alan bilgilerin değiştirilmediğinden emin olmak için kullanılır `updateManifest` . , `updateManifestSignature` JSON Web anahtarları Ile JSON Web imzası kullanılarak oluşturulur ve kaynak doğrulaması için izin verilir. İmza, "." tarafından düzenlenen üç bölümden oluşan Base64Url kodlu bir dizedir.  JSON anahtarlarını ve belirteçlerini ayrıştırmak ve doğrulamak için [jws_util. h yardımcı yöntemlerine](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) bakın.

JSON Web Imzası, JSON tabanlı veri yapılarını kullanarak içerik imzalama için yaygın olarak kullanılan bir [IETF standardıdır](https://tools.ietf.org/html/rfc7515) . Verilerin imzasını doğrulayarak verilerin bütünlüğünü sağlamaya yönelik bir yoldur. JSON Web Imzası (JWS) [RFC 7515](https://www.rfc-editor.org/info/rfc7515)' de daha fazla bilgi bulabilirsiniz.

### <a name="json-web-token"></a>JSON Web Token

JSON Web belirteçleri, talepleri iki taraf arasında güvenli bir şekilde temsil etmeye yönelik açık, sektör [standardı](https://tools.ietf.org/html/rfc7519) bir yöntemdir.

### <a name="root-keys"></a>Kök anahtarlar

Her cihaz güncelleştirme aygıtı bir kök Anahtarlar kümesi içerir. Bu anahtarlar, tüm cihaz güncelleştirme imzaları için güvenin köküdür. Herhangi bir imzanın meşru kabul edilmesi için bu kök anahtarlardan biri üzerinden zincirleme olması gerekir.

Kök anahtar kümesi, imzalama anahtarlarını güvenlik amacıyla düzenli aralıklarla döndürmek için uygun olduğu için zaman içinde değişir. Sonuç olarak, cihaz Güncelleştirme Aracısı yazılımının kendisini en son kök Anahtarlar kümesiyle güncelleştirmesi gerekecektir. 

### <a name="signatures"></a>İmzalar

Tüm imzalar kök anahtarlardan biri tarafından imzalanmış bir imzalama (ortak) anahtarı tarafından kullanıma sunulacaktır. İmza, imzalama anahtarını imzalamak için hangi kök anahtarın kullanıldığını belirler. 

Bir cihaz güncelleştirme aracısının imza (genel) anahtarının imzasının uygun, geçerli ve onaylanmış kök anahtarlardan biri tarafından imzalanmış olduğunu doğrulayarak imzaları doğrulaması gerekir. İmzalama anahtarı başarılı bir şekilde doğrulandıktan sonra imza, şimdi güvenilen imzalama ortak anahtarı kullanılarak doğrulanabilir.

İmzalama anahtarları kök anahtardan çok daha hızlı bir temposunda döndürülür, bu nedenle çeşitli farklı İmzalama anahtarları tarafından imzalanmış iletileri bekler. 

İmzalama anahtarının iptali, cihaz güncelleştirme hizmeti tarafından yönetilir, böylece kullanıcılar imzalama anahtarlarını önbelleğe alma girişiminde bulunmalıdır. Her zaman imza eşlik eden imzalama anahtarını kullanın.

### <a name="receiving-updates"></a>Güncelleştirmeler alınıyor

Bir cihaz Güncelleştirme Aracısı tarafından alınan güncelleştirme istekleri, imzalı bir güncelleştirme bildirimi (UM) belgesi içerir. Aracının, UM imzasının doğru ve sağlam olduğunu doğrulaması gerekir. Bu, UM imzasının İmzalama anahtarının doğru bir kök anahtarla imzalandığını doğrulayarak yapılır. İşlem yapıldıktan sonra, aracı, UM imzasını imza anahtarına göre doğrular.

UM imzası doğrulandıktan sonra cihaz Güncelleştirme Aracısı Buna bir "Truth kaynağı" olarak güvenebileceği. Daha fazla güvenlik güveni bu kaynaktan daha fazla. 

UM, indirme ve yükleme için içerik URL 'Leri ve dosya karmalarını içerir. Aracı bir güncelleştirme ikilisini indirdikten sonra, güncelleştirmeyi UM 'da bulunan dosya karmasında bir kez doğrulaması gerekir. Bu, indirme doğrulaması için geçişli bir güven modeli sağlar. Yalnızca içeriğin bozulmadan (değiştirilmemiştir) emin olmaz, ancak indirilen nelerin gerçekten İndirilme amacını de onaylar. 

### <a name="securing-the-device"></a>Cihazın güvenliğini sağlama

Cihaz güncelleştirmesiyle ilgili güvenlik varlıklarının cihazınızda güvenli ve korumalı olduğundan emin olmak önemlidir. Kök anahtarların gibi varlıkların değişikliklere karşı korunması gerekir. Bunu yapmak için güvenlik cihazlarını (TPM, SGX, HSM, diğer güvenlik cihazları) kullanma gibi çeşitli yollar vardır ve bunları cihaz güncelleştirme aracısında sabit kodlardır. İkincisi, cihaz Güncelleştirme Aracısı kodunun dijital olarak imzalanmasını ve aracı kodunun kötü amaçlı değişimine karşı koruma sağlamak için sistemin kod bütünlüğü desteğinin etkinleştirilmesini gerektirir.

Bileşenden bileşene iletim güvenli bir şekilde gerçekleştirildiğinden emin olmak gibi ek güvenlik önlemleri garanti edilebilir. Örneğin, belirli bir yalıtılmış hesabı çeşitli bileşenleri çalıştıracak şekilde kaydetme. Ve ağ tabanlı iletişimleri (örn. REST API çağrılar) yalnızca localhost 'a kısıtlar.

**[Sonraki adım: cihaz güncelleştirmesinin Azure RBAC kullanma hakkında daha fazla bilgi edinin](.\device-update-control-access.md)**