---
title: Azure Application Gateway karşılıklı kimlik doğrulaması sorunlarını giderme
description: Application Gateway karşılıklı kimlik doğrulaması sorunlarını giderme hakkında bilgi edinin
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 623addd253b3eb28bdf70db02ddfbe4b320cbae7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231616"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>Application Gateway karşılıklı kimlik doğrulama hatalarında sorun giderme (Önizleme)

Application Gateway kullanırken karşılıklı kimlik doğrulamasıyla ilgili sorunları nasıl giderebileceğinizi öğrenin. 

## <a name="overview"></a>Genel Bakış 

Application Gateway karşılıklı kimlik doğrulamasını yapılandırdıktan sonra, karşılıklı kimlik doğrulaması kullanılmaya çalışıldığında görüntülenen bir dizi hata olabilir. Hataların bazı yaygın nedenleri şunlardır:

* Kök CA sertifikası olmadan bir sertifika veya sertifika zinciri karşıya yüklendi
* Birden çok kök CA sertifikası olan bir sertifika zinciri karşıya yüklendi
* Yalnızca CA sertifikası olmayan bir yaprak sertifikası içeren bir sertifika zinciri karşıya yüklendi 
* Veren DN uyumsuzluğu nedeniyle doğrulama hataları  

' De çalıştırabileceğiniz farklı senaryolara ve bu senaryolara nasıl sorun giderileceğine gideceğiz. Daha sonra hata kodları adresliyoruz ve karşılıklı kimlik doğrulamasıyla gördüğünüz belirli hata kodlarının olası nedenlerine değineceğiz. 

## <a name="scenario-troubleshooting---configuration-problems"></a>Senaryo sorunlarını giderme-yapılandırma sorunları
Karşılıklı kimlik doğrulamayı yapılandırmaya çalışırken çok sayıda senaryo söz konusu olabilir. En yaygın olarak karşılaşılan bazı sorunlar hakkında izlenecek yol göstereceğiz. 

### <a name="self-signed-certificate"></a>Otomatik olarak imzalanan sertifika

#### <a name="problem"></a>Sorun 

Karşıya yüklediğiniz istemci sertifikası kendinden imzalı bir sertifikadır ve Applicationgatewaytrustedclientcertificateunnotcontainanycacertificate hata kodu ile sonuçlanır.

#### <a name="solution"></a>Çözüm 

Kullandığınız otomatik olarak imzalanan sertifikanın *BasicConstraintsOid* = "2.5.29.19" uzantısına sahip olduğunu ve konunun bir CA gibi davrandığını gösterir. Bu, kullanılan sertifikanın bir CA sertifikası olmasını sağlayacaktır. Otomatik olarak imzalanan istemci sertifikaları oluşturma hakkında daha fazla bilgi için [Güvenilen istemci sertifikalarına](./mutual-authentication-certificate-management.md)göz atın.

## <a name="scenario-troubleshooting---connectivity-problems"></a>Senaryo sorunlarını giderme-bağlantı sorunları

Karşılıklı kimlik doğrulamayı herhangi bir sorun olmadan yapılandırabilir, ancak Application Gateway istek gönderirken sorunlarla karşılaşrsınız. Aşağıdaki bölümde bazı yaygın sorunlar ve çözümleri ele aldık. *Sslclientverify* özelliğini Application Gateway erişim günlüklerine bulabilirsiniz. 

### <a name="sslclientverify-is-none"></a>SslClientVerify yok

#### <a name="problem"></a>Sorun 

*Sslclientverify* özelliği, erişim günlüklerinizin IÇINDE "none" olarak görünüyor. 

#### <a name="solution"></a>Çözüm 

Bu, istemci Application Gateway istek gönderirken istemci sertifikası göndermediği zaman görülür. Bu durum, isteği Application Gateway gönderen istemci istemci sertifikalarını kullanmak üzere doğru şekilde yapılandırılmamışsa meydana gelebilir. Application Gateway istemci kimlik doğrulama kurulumunun beklenen şekilde çalıştığını doğrulamanın bir yolu, aşağıdaki OpenSSL komutu aracılığıyla yapılır:

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

`-cert`Bayrak, yaprak sertifikadır, `-key` bayrak istemci özel anahtar dosyasıdır. 

OpenSSL komutunun nasıl kullanılacağı hakkında daha fazla bilgi için `s_client` [el ile sayfasını](https://www.openssl.org/docs/man1.0.2/man1/openssl-s_client.html)inceleyin.

### <a name="sslclientverify-is-failed"></a>SslClientVerify başarısız oldu

#### <a name="problem"></a>Sorun

*Sslclientverify* özelliği, erişim GÜNLÜKLERINIZI "başarısız" olarak görünüyor. 

#### <a name="solution"></a>Çözüm

Erişim günlüklerindeki hataların birçok olası nedeni vardır. Hatanın yaygın nedenlerine ilişkin bir liste aşağıda verilmiştir:
* **Veren sertifikası alınamıyor:** İstemci sertifikasının veren sertifikası bulunamadı. Bu normalde, güvenilen istemci CA Sertifika zincirinin Application Gateway tamamlanmamış olduğu anlamına gelir. Application Gateway karşıya yüklenen güvenilen istemci CA Sertifika zincirinin tamamlandığını doğrulayın.  
* **Yerel veren sertifikası alınamıyor:** Verenin sertifikası alınamadı, istemci sertifikasının veren sertifikası bulunamıyor. Bu normalde, güvenilen istemci CA Sertifika zincirinin Application Gateway tamamlanmamış olduğu anlamına gelir. Application Gateway karşıya yüklenen güvenilen istemci CA Sertifika zincirinin tamamlandığını doğrulayın.
* **İlk sertifika doğrulanamıyor:** İstemci sertifikası doğrulanamıyor. Bu hata özellikle, istemci, verenler güvenilir olmayan yalnızca yaprak sertifikayı sunduğunda oluşur. Application Gateway karşıya yüklenen güvenilen istemci CA Sertifika zincirinin tamamlandığını doğrulayın.
* **İstemci sertifikasını veren doğrulanamıyor:** Bu hata, *Verifyclientcertısuerdn* yapılandırması true olarak ayarlandığında oluşur. Bu durum genellikle, istemci sertifikasının veren DN 'si müşteri tarafından karşıya yüklenen güvenilen istemci CA 'sı sertifika zincirinden ayıklanan *Clientcertificateıssuerdn* ile eşleşmediği zaman gerçekleşir. Application Gateway *Clientcertificateıssuerdn*'nin nasıl ayıklanacağı hakkında daha fazla bilgi için, [sertifikayı veren DN 'yi ayıklama Application Gateway](./mutual-authentication-overview.md#verify-client-certificate-dn)yok edin. En iyi uygulama olarak, Application Gateway dosya başına bir sertifika zinciri yüklediğinizden emin olun. 

Application Gateway karşıya yüklemek üzere tüm güvenilen istemci CA sertifika zincirini ayıklama hakkında daha fazla bilgi için bkz. [Güvenilen ISTEMCI CA sertifika zincirlerini ayıklama](./mutual-authentication-certificate-management.md).

## <a name="error-code-troubleshooting"></a>Hata kodu sorun giderme
Aşağıdaki hata kodlarından herhangi birini görüyorsanız, karşılaştığınız sorunu çözmeye yardımcı olmak için önerilen birkaç çözüm vardır. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>Hata kodu: ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>Nedeni

Eksik sertifika verileri var. Karşıya yüklenen sertifika, herhangi bir sertifika verisi olmadan boş bir dosya olabilir. 

#### <a name="solution"></a>Çözüm

Karşıya yüklenen sertifika dosyasının eksik verileri olmadığını doğrulayın. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>Hata kodu: ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>Nedeni

Sertifika zincirinde özel bir anahtar vardır. Sertifika zincirinde özel bir anahtar olmamalıdır. 

#### <a name="solution"></a>Çözüm

Karşıya yüklenen sertifika zincirini iki kez kontrol edin ve zincirde bir parçası olan özel anahtarı kaldırın. Zinciri özel anahtar olmadan yeniden karşıya yükleyin. 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>Hata kodu: Applicationgatewaytrustedclientcertificateınvaliddata

#### <a name="cause"></a>Nedeni

Bu hata kodunun arkasında iki olası neden vardır.
1. Zincir doğru biçimde sunulmadığı için ayrıştırma başarısız oldu. Application Gateway, bir Sertifika zincirinin ped biçiminde olmasını bekler ve ayrıca tek tek sertifika verilerinin sınırlandırıldığından bekliyor. 
2. Ayrıştırıcı ayrıştırılacak hiçbir şey bulamadı. Karşıya yüklenen dosya muhtemelen yalnızca sınırlayıcıları içeriyordu, ancak sertifika verisi yok. 

#### <a name="solution"></a>Çözüm

Bu hatanın nedenine bağlı olarak, iki olası çözüm vardır. 
* Karşıya yüklenen Sertifika zincirinin doğru biçimde (Pez) olduğunu ve sertifika verilerinin düzgün şekilde sınırlandırıldığından emin olmak. 
* Karşıya yüklenen sertifika dosyasının, sınırlandırıcıların yanı sıra sertifika verilerini içerdiğinden emin olun. 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>Hata kodu: Applicationgatewaytrustedclientcertificateunnotcontainanycacertificate

#### <a name="cause"></a>Nedeni

Karşıya yüklenen sertifika, yalnızca CA sertifikası olmayan bir yaprak sertifika içeriyordu. Yaprak sertifikası yalnızca yok sayılacak, ancak bir sertifikanın CA 'sı olması gerektiği için, CA sertifikaları ve yaprak sertifikası içeren bir sertifika zincirini karşıya yüklemek kabul edilebilir. 

#### <a name="solution"></a>Çözüm 

Karşıya yüklenen sertifika zincirinde, yalnızca yaprak sertifikadan daha fazla yer olduğunu iki kez denetleyin. *BasicConstraintsOid* = "2.5.29.19" uzantısının mevcut olması gerekir ve konunun bir CA olarak davranabileceği anlamına gelebilir.

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>Hata kodu: ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>Nedeni

Sertifika zinciri birden çok kök CA sertifikası içeriyordu *veya* sıfır kök CA sertifikası içeriyordu. 

#### <a name="solution"></a>Çözüm

Karşıya yüklenen sertifikalar, tam olarak bir kök CA sertifikası (ve gerektiğinde çok sayıda ara CA sertifikası) içermelidir.


