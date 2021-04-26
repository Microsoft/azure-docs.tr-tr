---
title: Kimlik doğrulaması, istekler ve yanıtlar
description: Azure Key Vault JSON ile biçimlendirilen istekleri ve yanıtları nasıl kullandığını ve anahtar kasası kullanmak için gereken kimlik doğrulamasını öğrenin.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 58616b647affd33e96357e556ab61f85d1c62129
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96752286"
---
# <a name="authentication-requests-and-responses"></a>Kimlik doğrulaması, istekler ve yanıtlar

Azure Key Vault bulut uygulamalarınız için gizli dizileri depolamak ve yönetmek üzere iki tür kapsayıcı sağlar:

|Kapsayıcı türü|Desteklenen nesne türleri|Veri düzlemi uç noktası|
|--|--|--|
| **Kasalar**|<ul><li>Yazılım korumalı anahtarlar</li><li>HSM korumalı anahtarlar (Premium SKU ile)</li><li>Sertifikalar</li><li>Depolama hesabı anahtarları</li></ul> | https://{kasa-adı}. kasa. Azure. net
|**Yönetilen HSM** |<ul><li>HSM ile korunan anahtarlar</li></ul> | https://{HSM-adı}. managedhsm. Azure. net

Her nesne türüne erişmek için kullanılan URL sonekleri aşağıda verilmiştir

|Nesne türü|URL son eki|
|--|--|
|Yazılım korumalı anahtarlar| /Keys |
|HSM ile korunan anahtarlar| /Keys |
|Gizli Diziler|/gizlilikler|
|Sertifikalar| /Certificates|
|Depolama hesabı anahtarları|/storageaccounts
||

Azure Key Vault JSON biçimli istekleri ve yanıtları destekler. Azure Key Vault istekleri, URL parametreleri ve JSON kodlamalı istek ve yanıt gövdeleriyle HTTPS kullanılarak geçerli bir Azure Key Vault URL 'sine yönlendirilir.

Bu konu, Azure Key Vault hizmetinin özelliklerini içerir. Kimlik doğrulama/yetkilendirme ve erişim belirteci alma gibi Azure REST arabirimlerini kullanma hakkında genel bilgi için bkz. [azure REST API başvurusu](/rest/api/azure).

## <a name="request-url"></a>İstek URL’si  
 Anahtar yönetim işlemleri, mevcut anahtar nesnelerinde http DELETE, GET, PATCH, PUT ve HTTP POST ve şifreleme işlemlerini kullanır. Belirli HTTP fiillerini destekleyemediği istemciler, amaçlanan fiili belirtmek için X-HTTP-REQUEST üst bilgisini kullanarak da HTTP POST kullanabilir. Normalde bir gövde gerektirmeyen istekler HTTP POST kullanılırken boş bir gövde içermelidir, örneğin DELETE yerine POST kullanılıyor.  

 Azure Key Vault nesneleriyle çalışmak için aşağıdaki örnek URL 'Ler verilmiştir:  

- Key Vault kullanımı içinde TESTKEY adlı bir anahtar oluşturmak için `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- IMPORTEDKEY adlı bir anahtarı Key Vault kullanımı-içine aktarmak için `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Bir Key Vault kullanımı ile MYSECRET adlı bir gizli dizi almak için `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Key Vault kullanımı içinde TESTKEY adlı bir anahtarı kullanarak bir özeti IMZALAMAK için `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

- Bir Key Vault isteği için olan yetki her zaman aşağıdaki gibidir.
  - Kasa için: `https://{keyvault-name}.vault.azure.net/`
  - Yönetilen HSM 'ler için: `https://{HSM-name}.managedhsm.azure.net/`

  Anahtarlar her zaman/Keys yolu altında depolanır, gizlilikler her zaman/gizlilikler yolu altında depolanır.  

## <a name="api-version"></a>API Sürümü  
 Azure Key Vault hizmeti, alt düzey istemcilerle uyumluluk sağlamak için protokol sürümü oluşturmayı destekler, ancak tüm yetenekler bu istemciler için kullanılabilir olmaz. İstemcilerin `api-version` , desteklediği protokol sürümünü belirtmek için sorgu dizesi parametresini kullanması gerekir, çünkü varsayılan yoktur.  

 Azure Key Vault protokol sürümleri, {YYYY} kullanarak bir tarih numaralandırma şemasını izler. {MM}. {DD} biçimi.  

## <a name="request-body"></a>İstek Gövdesi  
 HTTP belirtimine göre, GET işlemlerinde bir istek gövdesi olmaması ve POST ve PUT işlemlerinin bir istek gövdesine sahip olması gerekir. DELETE işlemlerinde gövde, HTTP 'de isteğe bağlıdır.  

 İşlem açıklamasında aksi belirtilmedikçe, istek gövdesi içerik türü Application/JSON olmalı ve içerik türüne uyumlu serileştirilmiş JSON nesnesi içermelidir.  

 İşlem açıklamasında aksi belirtilmedikçe, Accept istek üst bilgisi Application/JSON medya türünü içermelidir.  

## <a name="response-body"></a>Yanıt gövdesi  
 İşlem açıklamasında aksi belirtilmedikçe, hem başarılı hem de başarısız olan işlemlerin yanıt gövdesi içerik türü Application/JSON olur ve ayrıntılı hata bilgilerini içerir.  

## <a name="using-http-post"></a>HTTP POST kullanma  
 Bazı istemciler, düzeltme eki veya SILME gibi bazı HTTP fiillerini kullanmayabilir. Azure Key Vault, istemcinin özgün HTTP fiiline özgü "X-HTTP-METHOD" üst bilgisini de içermesi kaydıyla, bu istemcilere alternatif olarak HTTP POST 'un kullanılmasını destekler. Bu belgede tanımlanan her API için HTTP POST için destek belirtilmiştir.  

## <a name="error-responses"></a>Hata yanıtları  
 Hata işleme HTTP durum kodlarını kullanacaktır. Tipik sonuçlar şunlardır:  

- 2xx – başarılı: normal işlem için kullanılır. Yanıt gövdesinde beklenen sonuç yer alacak  

- 3xx – yeniden yönlendirme: koşullu bir GET yerine getirmek için 304 "değiştirilmedi" geri döndürülebilir. Diğer 3xx kodları gelecekte DNS ve yol değişikliklerini göstermek için kullanılabilir.  

- 4xx – Istemci hatası: Hatalı istekler, eksik anahtarlar, sözdizimi hataları, geçersiz parametreler, kimlik doğrulama hataları vb. için kullanılır. Yanıt gövdesinde ayrıntılı hata açıklaması yer alacak.  

- 5xx – sunucu hatası: iç sunucu hataları için kullanılır. Yanıt gövdesi özetlenen hata bilgilerini içerir.  

  Sistem bir proxy veya güvenlik duvarının arkasında çalışacak şekilde tasarlanmıştır. Bu nedenle, bir istemci diğer hata kodlarını alabilir.  

  Azure Key Vault, bir sorun oluştuğunda yanıt gövdesinde hata bilgilerini de döndürür. Yanıt gövdesi JSON olarak biçimlendirilir ve şu biçimde olur:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Kimlik Doğrulaması  
 Azure Key Vault tüm isteklerin kimliğinin doğrulanması gerekır. Azure Key Vault, OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)] kullanılarak elde edilebilir Azure Active Directory erişim belirteçlerini destekler. 
 
 Uygulamanızı kaydetme ve Azure Key Vault kullanmak için kimlik doğrulama hakkında daha fazla bilgi için bkz. [istemci uygulamanızı Azure AD 'ye kaydetme](/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Erişim belirteçlerinin HTTP yetkilendirme üst bilgisi kullanılarak hizmete gönderilmesi gerekir:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Bir erişim belirteci sağlanmadığında veya bir belirteç hizmet tarafından kabul edilmediğinde, istemciye bir HTTP 401 hatası döndürülür ve WWW-Authenticate üst bilgisini dahil eder. Örneğin:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 WWW-Authenticate üstbilgisindeki parametreler şunlardır:  

-   Yetkilendirme: istek için bir erişim belirteci almak üzere kullanılabilecek OAuth2 yetkilendirme hizmetinin adresi.  

-   Kaynak: yetkilendirme isteğinde kullanılacak kaynağın adı ( `https://vault.azure.net` ).

> [!NOTE]
> Key Vault ilk çağrıda bulunan gizli dizileri, sertifikalar ve anahtarlar için SDK istemcileri Key Vault kiracı bilgilerini almak için bir erişim belirteci sağlamaz. Key Vault SDK istemcisi kullanılarak bir HTTP 401 alınması beklenir; burada Key Vault, uygulamayı içeren WWW-Authenticate üst bilgisini ve belirteci sormak için gereken kiracıyı gösterir. Her şey doğru yapılandırılmışsa, uygulamadan Key Vault ikinci çağrı geçerli bir belirteç içerir ve başarılı olur. 
