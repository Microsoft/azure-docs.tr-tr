---
title: Öğretici-Azure IoT Hub için şifreleme ve X. 509.440 sertifikalarını anlayın | Microsoft Docs
description: Öğretici-Azure IoT Hub için şifrelemeyi ve X. 509.440 PKI 'sını anlayın
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 2c375a02f534572826e1ebd6b8549e59f6e83640
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378389"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Öğretici: ortak anahtar şifrelemesini ve X. 509.440 ortak anahtar altyapısını anlama

Cihazların kimliğini Azure IoT Hub için doğrulamak üzere X. 509.952 sertifikalarını kullanabilirsiniz. Sertifika, cihazın ortak anahtarını içeren dijital bir belgedir ve cihazın ne talep ettiğini doğrulamak için kullanılabilir. X. 509.440 sertifikaları ve sertifika iptal listeleri (CRL 'Ler), [RFC 5280](https://tools.ietf.org/html/rfc5280)tarafından belgelenmiştir. Sertifikalar bir X. 509.440 ortak anahtar altyapısının (PKI) yalnızca bir parçasıdır. X. 509.440 PKI 'sını anlamak için şifreleme algoritmaları, şifreleme anahtarları, sertifikalar ve sertifika yetkililerini (CA 'Lar) anlamanız gerekir:

* **Algoritmalar** , özgün düz metin verilerinin, şifreli ve düz metin olarak nasıl dönüştürüldüğünü tanımlar.
* **Anahtarlar** , bir algoritmaya giriş olarak kullanılan rastgele veya pseudportaıdom veri dizeleridir.
* **Sertifikalar** , bir varlığın ortak anahtarını içeren dijital belgelerdir ve sertifika konusunun ne olduğunu veya ne talep etmediğini belirlemenizi sağlar.
* Sertifika **yetkilileri** sertifika konularının orijinalliğini onaylar.

Bir sertifika yetkilisinden (CA) bir sertifika satın alabilirsiniz. Ayrıca, test ve geliştirme için ya da kendi içindeki bir ortamda çalışıyorsanız otomatik olarak imzalanan bir kök CA oluşturabilirsiniz. Örneğin, bir veya daha fazla cihaz sahibiyseniz ve IoT Hub kimlik doğrulamasını test ediyorsanız, kök sertifika yetkilinizi kendinden imzalayabilir ve cihaz sertifikaları vermek için kullanabilirsiniz. Ayrıca kendinden imzalı cihaz sertifikaları da verebilirsiniz. Bu, sonraki makalelerde ele alınmıştır.

X. 509.440 sertifikalarını daha ayrıntılı bir şekilde tartışmadan ve IoT Hub cihazları kimlik doğrulaması için kullanarak, sertifikaların temel aldığı şifrelemeyi tartıştık.

## <a name="cryptography"></a>Şifreleme

Şifreleme bilgileri ve iletişimleri korumak için kullanılır. Bu, genellikle düz metin (normal metin) ile şifreli olarak (kodlanmış metin) ve yeniden geri doğru bir şekilde düzlebileceğiniz şifreleme teknikleri kullanılarak yapılır. Bu karıştırma işlemine şifreleme adı verilir. Ters işlem şifre çözme olarak adlandırılır. Şifreleme, aşağıdaki hedeflere göre düşünüldüğünde:

* **Gizlilik**: bilgiler yalnızca amaçlanan kitleci tarafından anlaşılabilirler.
* **Bütünlük**: bilgiler depolama veya aktarım sırasında değiştirilemez.
* **Red olmayan**: bilgi Oluşturucusu daha sonra bu oluşturmayı reddedemez.
* **Kimlik doğrulaması**: gönderen ve alıcı birbirlerinin kimliğini doğrulayabilirler.

## <a name="encryption"></a>Şifreleme

Şifreleme işlemi bir algoritma ve anahtar gerektirir. Algoritma, verilerin düz metin 'den şifreli ve düz metin olarak nasıl dönüştürüleceğini tanımlar. Anahtar, algoritmaya girdi olarak kullanılan rastgele bir veri dizesidir. İşlemin tüm güvenliği anahtarda yer alır. Bu nedenle, anahtarın güvenli bir şekilde depolanması gerekir. Bununla birlikte en popüler algoritmaların ayrıntıları herkese açık bir şekilde sunulmaktadır.

İki tür şifreleme vardır. Simetrik şifreleme, hem şifreleme hem de şifre çözme için aynı anahtarı kullanır. Asimetrik şifreleme, şifreleme ve şifre çözme işlemleri gerçekleştirmek için farklı ancak matematik ile ilgili anahtarlar kullanır.

### <a name="symmetric-encryption"></a>Simetrik şifreleme

Simetrik şifreleme, düz metin şifrelemek için aynı anahtarı kullanır ve şifreli olarak düz metin şifresini çözer. Bit sayısı olarak ifade edilen anahtarın gerekli uzunluğu, algoritma tarafından belirlenir. Düz metin şifrelemek için anahtar kullanıldıktan sonra şifreli ileti, daha sonra şifreli \ ' ı çözdüğü alıcıya gönderilir. Simetrik anahtar, alıcıya güvenli bir şekilde aktarılmalıdır. Simetrik algoritma kullanılırken anahtarın gönderilmesi en yüksek güvenlik riskidir.

![Simetrik şifreleme örneği](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Asimetrik şifreleme

Yalnızca simetrik şifreleme kullanılıyorsa, bu sorun, iletişimdeki tüm tarafların özel anahtara sahip olması gerekir. Ancak, yetkisiz üçüncü taraflar yetkili kullanıcılara iletim sırasında anahtarı yakalayabilir. Bu sorunu gidermek için, bunun yerine asimetrik veya ortak anahtar şifrelemesi kullanın.

Asimetrik şifrelemede, her kullanıcının bir anahtar çifti olarak adlandırılan iki matematik ile ilgili anahtarı vardır. Bir anahtar geneldir ve diğer anahtar özeldir. Anahtar çifti, yalnızca alıcının, verilerin şifresini çözmek için gereken özel anahtara erişiminin olmasını sağlar. Aşağıdaki çizimde asimetrik şifreleme işlemi özetlenmektedir.

![Asimetrik şifreleme örneği](media/tutorial-x509-introduction/asymmetric-keys.png)

1. Alıcı bir ortak özel anahtar çifti oluşturur ve ortak anahtarı bir CA 'ya gönderir. CA, bir X. 509.440 sertifikasındaki ortak anahtarı paketler.

1. Gönderen taraf alıcının ortak anahtarını CA 'dan alır.

1. Gönderici, bir şifreleme algoritması kullanarak düz metin verileri şifreler. Alıcının ortak anahtarı, şifrelemeyi gerçekleştirmek için kullanılır.

1. Gönderen, şifreli olarak alıcıya iletir. Alıcının şifresini çözmek için gerekli özel anahtara zaten sahip olduğundan, anahtarın gönderilmesi gerekli değildir.

1. Alıcı, belirtilen asimetrik algoritmayı ve özel anahtarı kullanarak şifreli verilerin şifresini çözer.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Simetrik ve asimetrik şifrelemeyi birleştirme

Simetrik ve asimetrik şifreleme, göreli güçlerinin avantajlarından yararlanmak için birleştirilebilir. Simetrik şifreleme, asimetrik olandan çok daha hızlıdır, ancak özel anahtarların diğer taraflara gönderilmesi zorunludur, güvenli değildir. İki türü birlikte birleştirmek için, simetrik şifreleme düz metin olarak şifreli metne dönüştürmek için kullanılabilir. Asimetrik şifreleme, simetrik anahtarı değiş tokuş etmek için kullanılır. Bu, aşağıdaki diyagram tarafından gösterilmiştir.

![Simetrik ve assymetric şifrelemesi](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. Gönderen alıcının ortak anahtarını alır.

1. Gönderici bir simetrik anahtar oluşturur ve özgün verileri şifrelemek için onu kullanır.

1. Gönderen, simetrik anahtarı şifrelemek için alıcının ortak anahtarını kullanır.

1. Gönderen şifreli simetrik anahtarı ve şifreli anahtarı hedeflenen alıcıya iletir.

1. Alıcı, gönderenin simetrik anahtarının şifresini çözmek için alıcının ortak anahtarıyla eşleşen özel anahtarı kullanır.

1. Alıcı, şifreli anahtarı şifrelemek için simetrik anahtar kullanır.

### <a name="asymmetric-signing"></a>Asimetrik imzalama

Asimetrik algoritmalar, verilerin değiştirilmesini ve veri oluşturucunun kimliğini kanıtlamasını sağlamak için kullanılabilir. Aşağıdaki çizimde, asimetrik imzalama, gönderenin kimliğini kanıtlamaya nasıl yardımcı olduğunu gösterir.

![Asimetrik imzalama örneği](media/tutorial-x509-introduction/asymmetric-signing.png)

1. Gönderen, şifreleme için özel anahtar kullanarak bir asimetrik şifreleme algoritması aracılığıyla düz metin verileri geçirir. Bu senaryonun, önceki bölümde açıklanan özel ve ortak anahtarların kullanımını, ayrıntılı asimetrik şifrelemeyi geri çevirdiğine dikkat edin.

1. Elde edilen şifreli metin alıcıya gönderilir.

1. Alıcı, bir dizinden gönderenin ortak anahtarını alır.

1. Alıcı, gönderenin ortak anahtarını kullanarak şifreli verilerin şifresini çözer. Ortaya çıkan düz metin, yalnızca özgün metni ilk olarak şifrelenen özel anahtara erişime sahip olduğundan, gönderenin kimliğini kanıtlar.

## <a name="signing"></a>İmzalama

Dijital imzalama, verilerin aktarım sırasında mi yoksa bekleyen olarak mi değiştirildiğini belirlemede kullanılabilir. Veriler, belirtilen iletiden matematiksel sonuç üreten tek yönlü bir işlev olan karma algoritmadan geçirilir. Sonuç *karma değeri*, *ileti özeti*, *Özet*, *imza* veya *parmak izi* olarak adlandırılır. Özgün iletiyi almak için bir karma değeri tersine çevrilemez. İletideki küçük bir değişiklik, *parmak izine* göre önemli bir değişikliğe neden olduğundan, karma değeri bir iletinin değiştirilip değiştirilmediğini anlamak için kullanılabilir. Aşağıdaki çizimde, bir iletinin değiştirilmediğini doğrulamak için asimetrik şifreleme ve karma algoritmaların nasıl kullanılabileceği gösterilmektedir.

![İmzalama örneği](media/tutorial-x509-introduction/signing.png)

1. Gönderen bir düz metin iletisi oluşturur.

1. Gönderen, bir ileti özeti oluşturmak için düz metin iletisini karma hale getirir.

1. Gönderen, Özeti özel bir anahtar kullanarak şifreler.

1. Gönderen, düz metin iletisini ve şifrelenmiş Özeti istenen alıcıya iletir.

1. Alıcı, gönderenin ortak anahtarını kullanarak Özet şifresini çözer.

1. Alıcı, gönderenin ileti üzerinde kullandığı karma algoritmasını aynı şekilde çalıştırır.

1. Alıcı, sonuçtaki imzayı şifresi çözülmüş imzaya karşılaştırır. Özetleri aynı ise, ileti iletim sırasında değiştirilmez.

## <a name="next-steps"></a>Sonraki adımlar

Bir sertifikayı oluşturan alanlar hakkında daha fazla bilgi edinmek için bkz. [X. 509.440 ortak anahtar sertifikalarını anlama](tutorial-x509-certificates.md).

X. 509.952 sertifikaları hakkında daha fazla bilginiz varsa ve IoT Hub kimlik doğrulaması yapmak için kullanabileceğiniz test sürümlerini oluşturmak istiyorsanız aşağıdaki konulara bakın:

* [Test sertifikaları oluşturmak için Microsoft-Supplied betikleri kullanma](tutorial-x509-scripts.md)
* [Test sertifikaları oluşturmak için OpenSSL kullanma](tutorial-x509-openssl.md)
* [Self-Signed test sertifikaları oluşturmak için OpenSSL kullanma](tutorial-x509-self-sign.md)

Bir sertifika yetkilisi (CA) sertifikanız veya alt CA sertifikanız varsa ve bunu IoT Hub 'ınıza yüklemek ve sahip olduğunuzu kanıtlamak istiyorsanız, bkz. [CA sertifikasını](tutorial-x509-prove-possession.md)kanıtlama.
