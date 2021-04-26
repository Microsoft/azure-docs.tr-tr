---
title: Linux için OpenSSL’yi yapılandırma
titleSuffix: Azure Cognitive Services
description: Linux için OpenSSL 'yi nasıl yapılandıracağınızı öğrenin.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577453"
---
# <a name="configure-openssl-for-linux"></a>Linux için OpenSSL’yi yapılandırma

1.9.0 öncesinde herhangi bir konuşma SDK sürümü kullanılırken, [OpenSSL](https://www.openssl.org) dinamik olarak konak sistemi sürümüne yapılandırılır. Konuşma SDK 'sının sonraki sürümlerinde OpenSSL (sürüm [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)), konuşma SDK 'sının temel kitaplığına statik olarak bağlanır.

Bağlantıyı sağlamak için, OpenSSL sertifikalarının sisteminizde yüklü olduğunu doğrulayın. Bir komut çalıştırın:
```bash
openssl version -d
```

Ubuntu/de, tabanlı sistemlerdeki çıkış şu şekilde olmalıdır:
```
OPENSSLDIR: "/usr/lib/ssl"
```

`certs`OPENSSLDIR altında alt dizin olup olmadığını denetleyin. Yukarıdaki örnekte, olacaktır `/usr/lib/ssl/certs` .

* Varsa `/usr/lib/ssl/certs` ve çok sayıda ayrı sertifika dosyası ( `.crt` veya `.pem` uzantısı) içeriyorsa, başka eylemlere gerek yoktur.

* OPENSSLDIR, ve/' den başka bir şeydir `/usr/lib/ssl` veya birden çok ayrı dosya yerine tek bir sertifika paketi dosyası varsa, sertifikaların nerede bulunabileceği göstermek için uygun BIR SSL ortam değişkeni ayarlamanız gerekir.

## <a name="examples"></a>Örnekler

- OPENSSLDIR `/opt/ssl` . `certs`Çok veya dosyaları olan alt dizin vardır `.crt` `.pem` .
Ortam değişkenini `SSL_CERT_DIR` `/opt/ssl/certs` , konuşma SDK 'sını kullanan bir programı çalıştırmadan önce işaret etmek üzere ayarlayın. Örnek:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR, `/etc/pki/tls` (RHEL/CentOS tabanlı sistemlerde olduğu gibi). `certs`Örneğin, bir sertifika paketi dosyası olan alt dizin bulunur `ca-bundle.crt` .
`SSL_CERT_FILE`Konuşma SDK 'sını kullanan bir programı çalıştırmadan önce bu dosyaya işaret etmek için ortam değişkenini ayarlayın. Örnek:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>Sertifika Iptal denetimleri
Konuşma hizmetine bağlanırken, konuşma SDK 'Sı, konuşma hizmeti tarafından kullanılan TLS sertifikasının iptal edilmediğini doğrular. Bu denetimi gerçekleştirmek için, konuşma SDK 'sının Azure tarafından kullanılan sertifika yetkilileri için CRL dağıtım noktalarına erişmesi gerekir. [Bu belgede](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes)olası CRL indirme konumlarının listesi bulunabilir. Bir sertifika iptal edildiyse veya CRL indirilemez konuşma SDK 'Sı bağlantıyı iptal eder ve Iptal edilen olayı yükseltir.

Konuşma SDK 'sının kullanıldığı ağ, CRL indirme konumlarına erişime izin vermeyen bir biçimde yapılandırıldığında, CRL denetimi devre dışı bırakılabilir veya CRL alınamadığından başarısız olarak ayarlanabilir. Bu yapılandırma, bir tanıyıcı nesnesi oluşturmak için kullanılan yapılandırma nesnesi aracılığıyla yapılır.

CRL alınamadığından bağlantı ile devam etmek için OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE özelliğini ayarlayın.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
"True" olarak ayarlandığında CRL 'YI almak için bir deneme yapılır ve alma başarılı olursa sertifika iptal edilmek üzere denetlenir, bu, alma işlemi başarısız olursa bağlantının devam etmesine izin verilir.

Sertifika iptal denetimlerini tamamen devre dışı bırakmak için OPENSSL_DISABLE_CRL_CHECK özelliğini "true" olarak ayarlayın.
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Ayrıca, bazı Linux dağıtımlarının tanımlı bir TMP veya TMPDIR ortam değişkeni olmadığından da buna dikkat edin. Bu, konuşma SDK 'sının, sertifika Iptal listesini (CRL) her seferinde karşıdan indirmesini, ancak süreleri dolana kadar yeniden kullanmak üzere CRL 'yi diske almasını sağlar. İlk bağlantı performansını geliştirmek için, [TMPDIR adlı bir ortam değişkeni oluşturabilir ve bunu seçtiğiniz geçici dizininizin yoluna ayarlayabilirsiniz.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konuşma SDK'sı hakkında](speech-sdk.md)
