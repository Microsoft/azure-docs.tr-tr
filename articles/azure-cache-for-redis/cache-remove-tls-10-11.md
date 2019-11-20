---
title: TLS 1,0 ve 1,1 ' i Redsıs için Azure Cache ile birlikte kullanarak kaldırma
description: Redsıs için Azure önbelleğiyle iletişim kurarken uygulamanızın TLS 1,0 ve 1,1 ' i nasıl kaldıracağınızı öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 585828855cdbd3a585ced1aa6803482c3f26f0f4
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121587"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>TLS 1,0 ve 1,1 ' i Redsıs için Azure Cache ile birlikte kullanarak kaldırma

Aktarım Katmanı Güvenliği (TLS) sürüm 1,2 veya üzeri için özel kullanıma yönelik sektör genelinde bir gönderim vardır. TLS sürümleri 1,0 ve 1,1, BEAST ve POOTEKı gibi saldırılara açıktır ve diğer yaygın güvenlik açıkları ve Etkilenmeler (CVE) zayıflıklarına sahip olur. Ayrıca, ödeme kartı sektör (PCI) uyumluluk standartları tarafından önerilen modern şifreleme yöntemlerini ve şifre paketlerini desteklemezler. Bu [TLS güvenlik blogu](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) , bu güvenlik açıklarından bazılarını daha ayrıntılı bir şekilde açıklamaktadır.

Bu önemli noktaların hiçbiri bir sorun oluştursa da yakında TLS 1,0 ve 1,1 kullanımını durdurmanız önerilir. Redsıs için Azure önbelleği, bu TLS sürümlerinin 31 Mart 2020 ' de desteklenmesini durdurur. Bu tarihten sonra, uygulamanızın önbelleğiyle iletişim kurması için TLS 1,2 veya üstünü kullanması gerekecektir.

Bu makalede, bu önceki TLS sürümlerindeki bağımlılıkların nasıl algılanacağı ve uygulamanızdan kaldırılacağı hakkında genel yönergeler sunulmaktadır.

## <a name="check-whether-your-application-is-already-compliant"></a>Uygulamanızın zaten uyumlu olup olmadığını denetleyin

Uygulamanızın TLS 1,2 ile çalışıp çalışmadığını bulmanın en kolay yolu, kullandığı bir test veya hazırlama önbelleğinde **En düşük TLS sürümü** değerini TLS 1,2 olarak ayarlayacaktır. **En düşük TLS sürümü** ayarı, Azure Portal önbellek örneğinizin [Gelişmiş ayarlarından](cache-configure.md#advanced-settings) oluşur. Bu değişiklikten sonra uygulama beklendiği gibi çalışmaya devam ederse büyük olasılıkla uyumludur. Özellikle TLS 1,2 ' i etkinleştirmek için, uygulamanız tarafından kullanılan bazı Reddir istemci kitaplıklarını yapılandırmanız gerekebilir. bu nedenle, bu güvenlik protokolünün üzerinden Red. Azure önbelleğine bağlanabilirler.

## <a name="configure-your-application-to-use-tls-12"></a>Uygulamanızı TLS 1,2 kullanacak şekilde yapılandırma

Çoğu uygulama, önbellekleri ile iletişimi işlemek için Redsıs istemci kitaplıklarını kullanır. Aşağıda, çeşitli programlama dillerinde ve çerçevelerinizdeki bazı popüler istemci kitaplıklarını, TLS 1,2 kullanacak şekilde yapılandırma yönergeleri verilmiştir.

### <a name="net-framework"></a>.NET Framework

Redsıs .NET istemcileri, en eski TLS sürümünü varsayılan olarak .NET Framework 4.5.2 veya önceki sürümlerde kullanır ve .NET Framework 4,6 veya sonraki sürümlerde en son TLS sürümünü kullanır. .NET Framework eski bir sürümünü kullanıyorsanız, TLS 1,2 'yi el ile etkinleştirebilirsiniz:

* **StackExchange. Reddir:** Bağlantı dizesinde `ssl=true` ve `sslprotocls=tls12` ayarlayın.
* **Servicestack. redsıs:** [Servicestack. redin yönergelerini](https://github.com/ServiceStack/ServiceStack.Redis/pull/247)izleyin.

### <a name="net-core"></a>.NET Core

Redsıs .NET Core istemcileri varsayılan olarak en son TLS sürümünü kullanır.

### <a name="java"></a>Java

Redsıs Java istemcileri Java sürüm 6 veya daha önceki bir sürümünde TLS 1,0 kullanır. TLS 1,0, önbellekte devre dışı bırakılmışsa, jedsıs, Lettuce ve Sedisson, redin için Azure önbelleğine bağlanamaz. Şu anda bilinen bir geçici çözüm yok.

Java 7 veya sonraki sürümlerde, Redsıs istemcileri varsayılan olarak TLS 1,2 kullanmaz ancak bu için yapılandırılabilir. Bu yapılandırmayı şu an için Lettuce ve Oydisson desteklemez. Önbellek yalnızca TLS 1,2 bağlantısı kabul ediyorsa bunlar kesilir. Jedsıs, aşağıdaki kod parçacığı ile temeldeki TLS ayarlarını belirtmenize olanak sağlar:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

Redsıs ve ıoredin Node, varsayılan olarak TLS 1,2 kullanır.

### <a name="php"></a>PHP

PHP 7 yalnızca TLS 1,0 ' i desteklediği için PHP 7 üzerinde ön dis çalışmaz. PHP 7.2.1 veya önceki sürümlerde, Predıs varsayılan olarak TLS 1,0 veya 1,1 kullanır. İstemci örneğini oluştururken TLS 1,2 ' i belirtebilirsiniz:

``` PHP
$redis=newPredis\Client([
    'scheme'=>'tls',
    'host'=>'host',
    'port'=>6380,
    'password'=>'password',
    'ssl'=>[
        'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
    ],
]);
```

PHP 7,3 veya üzeri sürümlerde, Predıs en son TLS sürümünü kullanır.

Phpredıs, herhangi bir PHP sürümünde TLS 'yi desteklemez.

### <a name="python"></a>Python

Redin-Kopyala varsayılan olarak TLS 1,2 kullanır.

### <a name="go"></a>GO

Redigo varsayılan olarak TLS 1,2 kullanır.

## <a name="additional-information"></a>Ek bilgiler

- [Redsıs için Azure önbelleğini yapılandırma](cache-configure.md)
