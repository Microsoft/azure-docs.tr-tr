---
title: Öğretici-Azure IoT Hub için X. 509.440 ortak anahtar sertifikalarını anlayın | Microsoft Docs
description: Öğretici-Azure IoT Hub için X. 509.440 ortak anahtar sertifikalarını anlayın
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 5503f9ad57180146c25a01c133a27b34e643496c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378355"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Öğretici: X. 509.440 ortak anahtar sertifikalarını anlama

X. 509.440 sertifikaları, bir Kullanıcı, bilgisayar, hizmet veya cihazı temsil eden dijital belgelerdir. Bunlar, bir sertifika yetkilisi (CA), alt CA veya kayıt yetkilisi tarafından verilir ve sertifika konusunun ortak anahtarını içerir. Bunlar, güvenli bir şekilde depolanması gereken konunun özel anahtarını içermez. Ortak anahtar sertifikaları, [RFC 5280](https://tools.ietf.org/html/rfc5280)tarafından belgelenmiştir. Bunlar dijital olarak imzalanırlar ve genel olarak aşağıdaki bilgileri içerir:

* Sertifika konusu hakkında bilgi
* Konunun özel anahtarına karşılık gelen ortak anahtar
* Veren CA hakkında bilgi
* Desteklenen şifreleme ve/veya dijital imzalama algoritmaları
* Sertifikanın iptal ve geçerlilik durumunu belirleme bilgileri

## <a name="certificate-fields"></a>Sertifika alanları

Zaman içinde üç sertifika sürümü vardır. Her sürüm daha önce bir alana alanlar ekler. Sürüm 3 geçerli ve sürüm 3 alanlarının yanı sıra sürüm 1 ve sürüm 2 alanları içerir. Sürüm 1 aşağıdaki alanları tanımladı:

* **Sürüm**: sertifikanın sürüm numarasını belirten bir değer (1, 2 veya 3)
* **Seri numarası**: CA tarafından verilen her sertifika için benzersiz bir sayı
* **CA Imza algoritması**: CA 'nın sertifika içeriğini imzalamak için kullandığı algoritmanın adı
* **Verenin adı**: SERTIFIKANıN veren CA 'nın ayırt edici adı (DN)
* **Geçerlilik süresi**: sertifikanın geçerli kabul edildiği zaman dilimi
* **Konu adı**: sertifikayla temsil edilen varlığın adı
* **Konu ortak anahtar bilgisi**: sertifika konusuna ait ortak anahtar

Sürüm 2, sertifikayı veren hakkında bilgi içeren aşağıdaki alanları ekledi. Ancak, bu alanlar nadiren kullanılır.

* **Verenin BENZERSIZ kimliği**: CA tarafından tanımlanan veren CA için benzersiz bir tanımlayıcı
* **Konu BENZERSIZ kimliği**: veren CA tarafından tanımlanan sertifika konusu için benzersiz bir tanımlayıcı

Sürüm 3 sertifikaları aşağıdaki uzantıları ekledi:

* **Yetkili anahtar tanımlayıcısı**: Bu iki değerden biri olabilir:
  * CA 'nın ve bu sertifikayı veren CA sertifikasının seri numarası konusu
  * Bu sertifikayı veren CA 'nın ortak anahtar karması
* **Konu anahtarı tanımlayıcısı**: geçerli sertifikanın ortak anahtarının karması
* **Anahtar kullanımı** Bir sertifikanın kullanılabileceği hizmeti tanımlar. Bu, aşağıdaki değerlerden biri veya daha fazlası olabilir:
  * **Dijital İmza**
  * **Red olmayan**
  * **Anahtar Şifrelemesi**
  * **Veri şifreleme**
  * **Anahtar anlaşması**
  * **Anahtar sertifikası Işareti**
  * **CRL Işareti**
  * **Yalnızca şifreler**
  * **Yalnızca şifre çöz**
* **Özel anahtar kullanım süresi**: bir anahtar çiftinin özel anahtar bölümü için geçerlilik süresi
* **Sertifika ilkeleri**: sertifika konusunu doğrulamak için kullanılan ilkeler
* **Ilke eşlemeleri**: bir kuruluştaki ilkeyi başka bir kuruluşta ilkeye eşler
* **Konu alternatif adı**: konu için alternatif adların listesi
* **Verenin alternatif adı**: veren CA için alternatif adların listesi
* **Konu dizini özniteliği**: X. 500 veya LDAP dizininden öznitelikler
* **Temel kısıtlamalar**: SERTIFIKANıN bir CA 'ya mi yoksa bir Kullanıcı, bilgisayar, cihaz veya hizmet için mi verildiğini belirlemesine izin verir. Bu uzantı Ayrıca var olan alt CA sayısını sınırlayan bir yol uzunluğu kısıtlaması içerir.
* **Ad kısıtlamaları**: CA tarafından verilen sertifikada hangi ad alanlarına izin verileceğini belirtir
* **Ilke kısıtlamaları**: CA 'lar arasında ilke eşlemelerini engellemek için kullanılabilir
* **Genişletilmiş anahtar kullanımı**: bir sertifikanın ortak anahtarının, **anahtar kullanımı** uzantısında tanımlanan amaçlardan daha fazla nasıl kullanılabileceğini gösterir
* **CRL dağıtım noktaları**: temel sertifika iptal LISTESININ (CRL) yayımlandığı bir veya daha fazla URL içerir
* **AnyPolicy engelle**: alt CA sertifikalarındaki **tüm verme ilkeleri** OID (2.5.29.32.0) kullanımını engeller
* En güncel **CRL**: veren CA 'NıN Delta CRL 'nin yayımlandığı bir veya daha fazla URL içeriyor
* **Yetkili bilgileri erişimi**: veren CA sertifikasının yayımlandığı bir veya daha fazla URL içeriyor
* **Konu bilgileri erişimi**: bir sertifika konusu için ek ayrıntıların nasıl alınacağını gösteren bilgiler içerir

## <a name="certificate-formats"></a>Sertifika biçimleri

Sertifikalar, çeşitli biçimlerde kaydedilebilir. Azure IoT Hub kimlik doğrulaması genellikle pek ve PFX biçimlerini kullanır.

### <a name="binary-certificate"></a>İkili sertifika

Bu, DER ASN. 1 kodlamasını kullanan bir ham form ikili sertifikası içerir.

### <a name="ascii-pem-format"></a>ASCII pek biçimi

PEK sertifikası (. ped uzantısı),-----başlangıç sertifikası-----ve-----son SERTIFIKAYLA biten bir base64 kodlu sertifika içerir-----. PEK biçimi çok yaygındır ve belirli sertifikaları karşıya yüklerken IoT Hub gerekir.

### <a name="ascii-pem-key"></a>ASCII (pek) anahtarı

Parola koruması için kullanılan algoritmayla ilgili ek meta verilerle Base64 kodlamalı bir DER anahtarı içerir.

### <a name="pkcs7-certificate"></a>PKCS # 7 sertifikası

İmzalanmış veya şifrelenmiş verilerin taşınması için tasarlanan bir biçim. [RFC 2315](https://tools.ietf.org/html/rfc2315)tarafından tanımlanır. Bu, tüm sertifika zincirini içerebilir.

### <a name="pkcs8-key"></a>PKCS # 8 anahtarı

[RFC 5208](https://tools.ietf.org/html/rfc5208)tarafından tanımlanan özel anahtar deposunun biçimi.

### <a name="pkcs12-key-and-certificate"></a>PKCS # 12 anahtar ve sertifika

Bir anahtarı ve tüm sertifika zincirini depolayabilen ve koruyabilen karmaşık bir biçim. Genellikle. pfx uzantısıyla kullanılır. PKCS # 12, PFX biçimiyle eşanlamlıdır.

## <a name="for-more-information"></a>Daha fazla bilgi edinmek için

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [Layman 'nın X. 509.440 sertifikası öğretmek Kılavuzu](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [IoT sektöründe X. 509.440 CA sertifikalarının kavramsal olarak anlaşılmasına](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub cihazların kimliğini doğrulamak için kullanabileceğiniz test sertifikaları oluşturmak istiyorsanız aşağıdaki konulara bakın:

* [Test sertifikaları oluşturmak için Microsoft-Supplied betikleri kullanma](tutorial-x509-scripts.md)
* [Test sertifikaları oluşturmak için OpenSSL kullanma](tutorial-x509-openssl.md)
* [Self-Signed test sertifikaları oluşturmak için OpenSSL kullanma](tutorial-x509-self-sign.md)

Bir sertifika yetkilisi (CA) sertifikanız veya alt CA sertifikanız varsa ve bunu IoT Hub 'ınıza yüklemek ve sahip olduğunuzu kanıtlamak istiyorsanız, bkz. [CA sertifikasını](tutorial-x509-prove-possession.md)kanıtlama.
