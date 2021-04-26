---
title: Azure IoT Hub X. 509.440 güvenliği kavramları | Microsoft Docs
description: Kavram-IoT cihaz üretimi ve kimlik doğrulaması içindeki X. 509.440 sertifika yetkilisi sertifikalarını anlamak.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 877200cbafbe68fa6161025572abfddad651e172
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96490729"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>IoT sektöründe X. 509.440 CA sertifikalarının kavramsal olarak anlaşılmasına

Bu makalede, IoT cihaz üretimi ve kimlik doğrulama IoT Hub için X. 509.440 sertifika yetkilisi (CA) sertifikalarının kullanılması değeri açıklanmaktadır. Tedarik zinciri kurulumu ve vurgulamanın avantajları hakkında bilgi içerir.

Bu makalede şunları açıklanmaktadır:

* X. 509.440 CA sertifikalarının ne olduğu ve nasıl alınacağı

* X. 509.952 CA sertifikanızı IoT Hub 'e kaydetme

* X. 509.440 CA tabanlı kimlik doğrulaması için üretim tedarik zinciri ayarlama

* X. 509.440 CA ile imzalanan cihazların IoT Hub 'e bağlanması

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Genel Bakış

X. 509.440 sertifika yetkilisi (CA) kimlik doğrulaması, sağlama zincirindeki cihaz kimliği oluşturma ve yaşam döngüsü yönetimini önemli ölçüde basitleştiren bir yöntemi kullanarak IoT Hub, cihazların kimliğini doğrulamaya yönelik bir yaklaşımdır.

X. 509.440 CA kimlik doğrulamasının ayırt edici özniteliği bir CA sertifikasının aşağı akış cihazlarına sahip olan bire çok ilişkidir. Bu ilişki, bir X. 509.952 CA sertifikasını bir kez kaydederek IoT Hub herhangi bir sayıda cihazın kaydedilmesine izin verebilir; Aksi takdirde, cihazın bağlanabilmesi için her cihaz için benzersiz sertifikaların önceden kaydolmalıdır. Bu bire çok ilişki Ayrıca cihaz sertifikalarının yaşam döngüsü yönetimi işlemlerini basitleştirir.

X. 509.440 CA kimlik doğrulamasının diğer önemli bir özniteliği, tedarik zinciri lojistik 'nın basitleştiridir. Cihazların güvenli kimlik doğrulaması, her cihazın güven için temel olarak bir anahtar gibi benzersiz bir gizli dizi bulundurmayı gerektirir. Sertifika tabanlı kimlik doğrulaması ' nda, bu gizli anahtar özel bir anahtardır. Tipik bir cihaz üretim akışı, birden çok adım ve custodıans içerir. Cihaz özel anahtarlarının birden çok koruyucu genelinde güvenli bir şekilde yönetilmesi ve güvenin sürdürülmesi zor ve pahalıdır. Sertifika yetkililerini kullanmak bu sorunu, her bir koruyucu, cihaz özel anahtarlarıyla güvenmek yerine güven zincirine imzalayarak çözer. İçindeki her bir koruyucu, cihazları üretim akışının ilgili işlem adımından kapatır. Genel sonuç, şifreleme zincirinin kullanılmasıyla birlikte yerleşik sorumluluire sahip en iyi tedarik zinciridir. Bu işlem, cihazlar benzersiz özel anahtarlarını koruduğu sırada bu işlemin en çok güvenliği oluştuğunu belirtmekte de yararlıdır. Bu uçta, günün ışığını hiçbir şekilde görmeyecek özel anahtarlar oluşturma yeteneğine sahip donanım güvenli modülleri 'nin (HSM) kullanımını inceleyeceğiz.

Bu makalede, sağlama zinciri kurulumundan cihaz bağlantısına kadar X. 509.952 CA kimlik doğrulamasını kullanmanın uçtan uca bir görünümü sunulmaktadır. Bu arada, anlamak için gerçek bir dünya örneğini kullanabilirsiniz.

Cihazların hub 'lara hazırlanmasını idare etmek için Azure IoT Hub cihaz sağlama hizmeti (DPS) ile kayıt grupları da kullanabilirsiniz. X. 509.440 sertifika cihazlarını sağlamak için DPS kullanma hakkında daha fazla bilgi için bkz. [öğretici: kayıt grupları kullanarak birden çok X. 509.440 cihazı sağlama](../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md).

## <a name="introduction"></a>Giriş

X. 509.440 CA sertifikası, sahibi diğer sertifikaları imzalayabileceği dijital bir sertifikadır. Bu dijital sertifika, IETF 'nin RFC 5280 standardına göre belirtilen bir sertifika biçimlendirme standardına uyduğundan ve sahibi diğer sertifikaları imzalayabildiğinden bir sertifika yetkilisi (CA) olduğundan, X. 509.440 olur.

X. 509.440 CA kullanımı, somut bir örnekle ilişkili olarak en iyi şekilde anlaşılmıştır. Profesyonel yükleme için tasarlanan bir akıllı X pencere öğesi Oluşturucu olan şirket-X ' i göz önünde bulundurun. Şirket-X outsources hem üretim hem de yükleme. BT sözleşmeleri üretici üreticisi-Y 'yi, akıllı X Pencere öğelerinin yanı sıra yüklemek için hizmet sağlayıcı teknisyeni-Z olarak üretme. Şirket-x, akıllı-x-pencere öğesinin, yükleme için doğrudan Factory-Y ' d e n ve şirket-x ' d e n daha fazla müdahale olmadan yüklemeden sonra şirket X ' in IoT Hub örneğine doğrudan bağlanmasını sağlar. Bunun gerçekleşmesini sağlamak için, şirket-X ' in otomatik bağlantı için birkaç tek seferlik kurulum işlemini tamamlaması gerekir. Uçtan uca senaryo göz önünde bulundurularak, bu makalenin geri kalanı aşağıdaki şekilde yapılandırılmıştır:

* X. 509.440 CA sertifikasını alma

* X. 509.440 CA sertifikasını IoT Hub kaydetme

* Cihazları bir sertifika güven zincirinde imzala

* Cihaz bağlantısı

## <a name="acquire-the-x509-ca-certificate"></a>X. 509.440 CA sertifikasını alma

Şirket-X ' i bir genel kök sertifika yetkilisinden bir X. 509.952 CA sertifikası satın alma veya otomatik olarak imzalanan bir işlem aracılığıyla bir tane oluşturma seçeneği vardır. Bir seçenek, uygulama senaryosuna bağlı olarak, diğeri üzerinde en iyi seçenektir. Bu seçenek ne olursa olsun, ortak/özel anahtar çifti oluşturan ve ortak anahtarı bir sertifikaya imzalayan iki temel adımı da kapsar.

![X509CA sertifikaları oluşturmak için akış](./media/iot-hub-x509ca-concept/csr-flow.png)

Bu adımların nasıl yerine getirileceğini gösteren Ayrıntılar çeşitli hizmet sağlayıcılarıyla farklıdır.

### <a name="purchasing-an-x509-ca-certificate"></a>X. 509.440 CA sertifikası satın alma

CA sertifikasının satın alınması iyi bilinen bir kök CA 'nın, Cihazlar bağlandığında IoT cihazlarının yasallığı için güvenilir bir üçüncü taraf görevi gören avantajına sahip olma avantajına sahiptir. IoT Hub ilk bağlantıdan sonra, şirket-X, üçüncü taraf ürün veya hizmetleriyle etkileşim kurmak için akıllı X pencere öğesi istiyorsanız bu seçeneği seçer.

X. 509.952 CA sertifikası satın almak için, şirket-X bir kök sertifika hizmetleri sağlayıcısı seçer. ' Kök CA ' ifadesi için bir internet araması iyi müşteri adayları elde eder. Kök CA, şirket-X ' i ortak/özel anahtar çiftinin nasıl oluşturulacağını ve hizmetleri için bir sertifika Imzalama Isteği (CSR) oluşturmayı yönlendirecektir. CSR, sertifika yetkilisinden bir sertifika için uygulama ile ilgili biçimsel bir işlemdir. Bu satınalmanın sonucu, yetkili sertifika olarak kullanılacak bir sertifikadır. X. 509.440 sertifikalarınızın kolaylaşmıştır 'e verildiğine göre, sertifika, IETF 'nin RFC 5280 standardına göre düzgün şekilde biçimlendirildi.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Self-Signed X. 509.440 CA sertifikası oluşturma

Bir Self-Signed X. 509.952 CA sertifikası oluşturma işlemi, kök sertifika yetkilisi gibi üçüncü taraf bir imzalayan ile satın almaya benzer. Bizim örneğimizde, şirket-X kök sertifika yetkilisi yerine yetkili sertifikasını imzalayacaktır. Şirket-X, bir yetkili sertifika satın almaya hazırlanana kadar bu seçeneği test etmek için seçebilirler. Şirket-X, akıllı-X-pencere öğesinin IoT Hub dışındaki herhangi bir üçüncü taraf hizmete bağlanması amaçlanmamışsa, üretimde otomatik olarak imzalanan bir X. 509.440 CA sertifikası da kullanabilir.

## <a name="register-the-x509-certificate-to-iot-hub"></a>X. 509.440 sertifikasını kaydedin IoT Hub

Şirket-X ' e, bağlandıkları gibi akıllı X Pencere öğelerinin kimliğini doğrulamak için kullanacağı IoT Hub, X. 509.440 CA 'sını kaydetmesi gerekir. Bu, herhangi bir sayıda akıllı X-pencere öğesi cihazını kimlik doğrulaması ve yönetme olanağı sağlayan tek seferlik bir işlemdir. Bu, CA sertifikası ile CA sertifikası veya ara sertifika tarafından imzalanan cihaz sertifikaları arasındaki bire çok ilişki nedeniyle tek seferlik bir işlemdir. Bu ilişki, X. 509.440 CA kimlik doğrulama yöntemini kullanmanın başlıca avantajlarından birini oluşturur. Diğer bir deyişle, her bir ve her akıllı X pencere öğesi için tek tek sertifika parmak izlerini karşıya yükleme işlemi, böylece işletimsel maliyetlere ekleniyor.

X. 509.440 CA sertifikasını kaydetmek, iki adımlı bir işlemdir, sertifika karşıya yükleme ve sertifika geçirmez.

![X509CA sertifikasını kaydetme](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>X. 509.440 CA sertifikasını karşıya yükleme

X. 509.440 CA sertifikası karşıya yükleme işlemi yalnızca IoT Hub, CA sertifikasını karşıya yükler. IoT Hub bir dosyadaki sertifikayı bekliyor. Şirket-X yalnızca sertifika dosyasını karşıya yükler. Sertifika dosyası herhangi bir koşulda herhangi bir özel anahtar içermemelidir. Ortak anahtar altyapısını (PKI) yöneten standartlardan en iyi uygulamalar, bu durumda Şirket-X ' i z i bu örnekte özel olarak şirket-X ' in içinde yer alır.

### <a name="proof-of-possession-of-the-certificate"></a>Sertifika kanıtı

X. 509.440 CA sertifikası, tıpkı her dijital sertifika gibi, gizlice dinleme saldırılarına açık olan genel bilgiler. Bu nedenle, bir gizlice dinber bir sertifikayı ele geçirebilir ve kendi kendine yüklemeyi deneyebilir. Bizim örneğimizde, IoT Hub Company-X CA sertifikasının gerçekten şirket-X ' e ait olduğundan emin olmak istersiniz. Bu sayede, zorlayıcı şirket-X ' i kullanarak sertifika [kanıtlama (pop) akışı](https://tools.ietf.org/html/rfc5280#section-3.1)aracılığıyla sertifikaya sahip olduklarını kanıtlayın. Bu arada bulunan akış, özel anahtarını kullanarak şirket-X tarafından imzalanmış rastgele bir sayı oluşturmak IoT Hub gerektirir. Şirket-X ' i takip eden PKI en iyi uygulamaları ve özel anahtarlarını koruduktan sonra, yalnızca BT, elinde bulunan zorlukları doğru bir şekilde yanıtlamak için bir konumda olacaktır. IoT Hub, X. 509.440 CA sertifikasını, elinde yer kanıtlama zorluğu başarılı bir yanıtı üzerine kaydetmeye devam eder.

IoT Hub ' dan elinde bulunan bir çekişme için başarılı bir yanıt, X. 509.440 CA kaydını tamamlar.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Cihazları bir sertifika güven zincirinde imzala

IoT, her cihazın benzersiz bir kimliğe sahip olmasını gerektirir. Bu kimlikler sertifika tabanlı kimlik doğrulama şemaları için sertifikalardır. Örneğimizde bu, her Smart-X pencere öğesinin benzersiz bir cihaz sertifikasına sahip olması gerektiği anlamına gelir. Şirket-X ' i tedarik zincirinde bu için nasıl ayarlar?

Bunu yapmanın bir yolu, akıllı-X pencere öğeleri için önceden sertifika oluşturmak ve tedarik zinciri ortaklarıyla ilgili benzersiz cihaz özel anahtarları hakkında bilgi sahibi olmak içindir. Şirket-X için bu, Factory-Y ve teknisyen-Z ' Y i güvenilen bir araçtır. Bu geçerli bir yöntem olsa da, güven sağlamak için aşağıdaki şekilde ele alınması gereken zorluk sunar:

1. Özel anahtarların hiçbir şekilde paylaşılmaması için PKI en iyi yöntemlerini göz ardı etmekle birlikte, cihaz özel anahtarlarını tedarik zinciri ortaklarıyla paylaşma Bu, şirket içi cihaz özel anahtarları ve düzenli güvenlik denetimleri gibi işlemlerin yüklenmesi için güvenli odalar gibi büyük sistemler anlamına gelir. Her ikisi de tedarik zincirine maliyet ekleyin.

2. Tedarik zincirindeki cihazların güvenli bir şekilde oluşturulması ve daha sonra dağıtımda yönetilmesi, cihaz benzersiz sertifikası (Bu nedenle özel anahtar) ile cihaz emekliliğe kadar her anahtar-cihaz çifti için bire bir görev haline gelir. Bu, Grup kavramı işleme açık bir şekilde doğrudan derlenmediği sürece cihazların Grup yönetimini daha fazla halden ayırır. Bu nedenle, güvenli muhasebe ve cihaz yaşam döngüsü yönetimi ağır bir işlem yükü haline gelir. Bizim örneğimizde, şirket-X bu yükü taşır.

X. 509.440 CA sertifikası kimlik doğrulaması, sertifika zincirlerinin kullanımı aracılığıyla listelenen güçlüklere yönelik zarif çözümler sunar. Bir sertifika zinciri, başka bir ara CA 'yı açan bir ara CA 'yı imzalayan bir CA 'dan kaynaklanır ve son ara CA bir cihazı imzalana kadar devam eder. Bizim örneğimizde, şirket-X ' i işaret eden, bu, son olarak, akıllı-X-pencere öğesini imzaladığında teknisyen-Z ' Y i kapatır.

![Sertifika zinciri hiyerarşisi](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Zincirdeki sertifikaların üzerinde Cascade 'ın mantıksal el ile kullanımını gösterir. Birçok Arz Zinciri, tüm yukarı akış CA sertifikalarını alırken her bir ara CA 'nın zincirde oturum açtığı ve son ara CA 'nın her bir cihazı imzaladığı ve tüm yetkili sertifikalarını cihaza açtığı bir şekilde bu mantıksal izlemeyi izler. Bu, bir fabrika hiyerarşisine sahip şirket üretim şirketi, üretimi yapmak için belirli bir fabrikaya yönelik bir fabrikasının bulunduğu durumlarda yaygın bir sözleşmedir. Hiyerarşi, çeşitli seviyeler (örneğin, Coğrafya/ürün türü/üretim satırına göre) olabileceğinden, yalnızca uçtaki fabrika cihazla etkileşime geçebilir, ancak zincir hiyerarşinin en üstünde tutulur.

Diğer zincirlerde farklı ara CA 'lar cihaz ile etkileşime geçerek, bu durumda CA cihaz ile etkileşim kurar ve bu noktada sertifika zinciri içeriğini çıkartır. Karma modeller Ayrıca, CA 'nın yalnızca bir kısmının cihazla fiziksel etkileşimi olduğu durumlarda da mümkündür.

Bizim örneğimizde, hem Factory-Y hem de teknisyen-Z, akıllı-X pencere öğesiyle etkileşime geçin. Şirket-X ' i z X pencere öğesine sahip olsa da, bu, tüm tedarik zincirinde fiziksel olarak etkileşime girmiyor. Akıllı-X-pencere öğesinin sertifika zinciri, bu nedenle, teknisyen-Z ' Y i, ardından da akıllı-X pencere öğesi için son imza sağlayacak olan şirket-X imzalama fabrikasını oluşturur. Akıllı-X pencere öğesinin üretimi ve yüklemesi, ilgili ara CA sertifikalarını kullanarak her bir ve her akıllı X pencere öğesinin imzalanmamasını sağlar. Bu işlemin nihai sonucu, benzersiz cihaz sertifikaları ve şirket-X CA sertifikasına giden sertifika güven zinciri ile birlikte akıllı X pencere öğeleri sağlar.

![Bir şirketin sertifikalarından başka bir şirketin sertifikalarını güven zinciri](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Bu, X. 509.440 CA yönteminin değerini gözden geçirmek için iyi bir noktasıdır. Şirket-X ' i her akıllı X-pencere öğesi için önceden oluşturma ve teslim etme yerine yalnızca bir kez Factory-Y ' Y i imzalamıştır. Şirket-X, cihazın yaşam döngüsü boyunca her aygıtı izlemek zorunda kalmak yerine, tedarik zinciri işleminden doğal olarak oluşan gruplar aracılığıyla cihazları izleyebilir ve yönetebilir (örneğin, bir yıldan sonra teknisyen-Z tarafından yüklenen cihazlar).

Son olarak, en azından CA kimlik doğrulama yöntemi, cihaz üretim tedarik zinciri ile güvenli sorumluluk kullanır. Sertifika zinciri işlemi nedeniyle, zincirdeki her üyenin eylemleri şifreli olarak kaydedilir ve doğrulanabilir.

Bu işlem, tamamlanma açısından ortaya çıkması gereken belirli varsayımlar kullanır. Bağımsız olarak cihaz benzersiz ortak/özel anahtar çifti oluşturulmasını ve özel anahtarın cihaz içinde korunmasını gerektirir. Neyse ki, anahtar üreten ve özel anahtarları koruyan donanım güvenli modülleri (HSM) biçimindeki güvenli bir Silicon yongalar mevcuttur. Şirket-X ' in yalnızca bu tür yongalardan birini, akıllı X pencere öğesinin bileşen ürün reçetelerine eklemesi gerekir.

## <a name="device-connection"></a>Cihaz bağlantısı

Yukarıdaki önceki bölümler cihaz bağlantısı oluşturuyor. Yalnızca bir kez IoT Hub bir X. 509.440 CA sertifikası kaydederek, milyonlarca cihaz nasıl bağlanır ve ilk kez kimlik doğrulamasından geçer?  MPLE aynı sertifika karşıya yüklemesi ve daha önce X. 509.440 CA sertifikasını kaydetme konusunda daha önce karşılaştık.

X. 509.440 CA kimlik doğrulaması için üretilen cihazlar, cihaz benzersiz sertifikaları ve ilgili üretim tedarik zincirinden bir sertifika zinciri ile donatılmıştır. Cihaz bağlantısı, çok ilk kez bile, iki adımlı bir işlemde gerçekleşir: sertifika zinciri yüklemesi ve sahip olma kanıtı.

Sertifika zincirini karşıya yükleme sırasında, cihaz, onun içinde yüklü olan sertifika zinciriyle birlikte cihaz benzersiz sertifikasını karşıya yükler IoT Hub. Önceden kaydedilmiş X. 509.952 CA sertifikasını kullanarak IoT Hub, karşıya yüklenen Sertifika zincirinin dahili olarak tutarlı olduğunu ve bu zincirinin X. 509.440 CA sertifikasının geçerli sahibi tarafından geldiğini şifreli olarak doğrulayabilirler. X. 509.440 CA kayıt süreciyle birlikte IoT Hub, zincirin ve bu nedenle cihaz sertifikasının gerçekten onu karşıya yükleyen cihaza ait olduğunu belirlemek için, birlikte bulunan bir bilgi işlem kanıtlama işlemi başlatır. Bu, IoT Hub tarafından doğrulanmak üzere özel anahtarı kullanılarak cihaz tarafından imzalanmış rastgele bir sınama oluşturarak bunu yapar. Başarılı bir yanıt, cihazı gerçek olarak kabul etmek ve bağlantıya vermek için IoT Hub tetikler.

Örneğimizde, her bir Smart-X-pencere öğesi, üretici sürümü ve teknisyen-Z X. 509.440 CA sertifikaları ile cihaz benzersiz sertifikasını karşıya yükler ve ardından IoT Hub sahip olma zorluğu sınamasına yanıt verir.

![Bir CERT diğerine akış, hub 'dan bir pop Challenge](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Güven temelini, cihaz özel anahtarları dahil özel anahtarları korumaya göre bekletildiğine dikkat edin. Bu nedenle, cihaz özel anahtarlarını korumak için donanım güvenli modülleri (HSM) biçimindeki güvenli Silicon yongalarının önemini önemli bir şekilde düşüremez ve tek bir fabrika gibi özel anahtarı olan bir fabrika gibi özel anahtarların hiçbir şekilde paylaşılmaması için genel en iyi uygulamadır.
