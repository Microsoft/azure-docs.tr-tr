---
title: Azure kanıtlama ilkesi yazma
description: Kanıtlama ilkesi yazma hakkında bir açıklama.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: aaf05b293fa5a13c5fe6e341f7de987c6002a5cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607975"
---
# <a name="how-to-author-an-attestation-policy"></a>Kanıtlama ilkesi yazma

Kanıtlama ilkesi, Microsoft Azure kanıtlama için karşıya yüklenen bir dosyadır. Azure kanıtlama, bir ilkeyi kanıtlamayı özgü bir ilke biçiminde karşıya yükleme esnekliği sunar. Alternatif olarak, JSON Web Imzasında, ilkenin kodlanmış bir sürümü de karşıya yüklenebilir. İlke Yöneticisi, kanıtlama ilkesini yazmadan sorumludur. Çoğu kanıtlama senaryosunda, bağlı olan taraf ilke Yöneticisi olarak görev yapar. Kanıtlama çağrısını yapan istemci, hizmetin gelen taleplere (özellikler, değer kümesi) ayrıştırdığı ve dönüştürdüğü kanıtlama kanıtını gönderir. Daha sonra hizmet, ilkede tanımlandığına göre talepleri işler ve hesaplanan sonucu döndürür.

İlke, yetkilendirme ölçütlerini, özelliklerini ve kanıtlama belirtecinin içeriğini belirleme kuralları içerir. Örnek ilke dosyası aşağıda gösterildiği gibi görünür:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnabled", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnabled", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
İlke dosyası, yukarıda görüldüğü gibi üç parçaya sahiptir:

- **Sürüm**: sürüm, izlenen dilbilgisinin sürüm numarasıdır. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    Şu anda desteklenen tek sürüm 1,0 sürümüdür.

- **authorizationrules**: Azure kanıtlama 'nın **ıssuancerules**'e ilerlemeniz gerekip gerekmediğini belirlemek için, ilk olarak denetlenecek talep kuralları koleksiyonu. Talep kuralları tanımlandıkları sırada geçerlidir.

- **ıssuancerules**: ilkede tanımlanan kanıtlama sonucuna ek bilgi eklemek için değerlendirilecek bir talep kuralları koleksiyonu. Talep kuralları tanımlandıkları sırada uygulanır ve ayrıca isteğe bağlıdır.

Daha fazla bilgi için bkz. [talep ve talep kuralları](claim-rule-grammar.md) .
   
## <a name="drafting-the-policy-file"></a>İlke dosyasını taslak

1. Yeni bir dosya oluşturun.
1. Sürüme dosya ekleyin.
1. **Authorizationrules** ve **ıssuancerules** için bölüm ekleyin.

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  Yetkilendirme kuralları, hiçbir verme kuralı işlenmediğinden emin olmak için herhangi bir koşul olmadan Deny () eylemini içerir. Alternatif olarak, yetkilendirme kuralı verme kurallarının işlenmesine izin vermek için izin ver () eylemini de içerebilir.
  
4. Yetkilendirme kurallarına talep kuralları ekleme

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Gelen talep kümesi, tür, değer ve veren ile eşleşen bir talep içeriyorsa, Permit () eylemi ilke altyapısına **ıssuancerules**'yi işlemesini söyler.
  
5. **Issuancerules**'e talep kuralları ekleyin.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  Giden talep kümesinde şu bir talep bulunur:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Karmaşık ilkeler benzer bir şekilde oluşturulabilir. Daha fazla bilgi için bkz. [kanıtlama ilkesi örnekleri](policy-examples.md).
  
6. Dosyayı kaydedin.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>JSON Web Imza biçiminde ilke dosyası oluşturma

Bir ilke dosyası oluşturduktan sonra, bir ilkeyi JWS biçiminde karşıya yüklemek için aşağıdaki adımları izleyin.

1. Yük olarak ilkeyle (UTF-8 kodlu) JWS, RFC 7515 oluşturun
     - Base64Url kodlamalı ilke için yük tanımlayıcısı "AttestationPolicy" olmalıdır.
     
     Örnek JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. Seçim İlkeyi imzalayın. Azure kanıtlama aşağıdaki algoritmaları destekler:
     - **Hiçbiri**: ilke yükünü imzamayın.
     - **RS256**: ilke yükünü imzalamak için desteklenen algoritma

3. JWS 'yi karşıya yükleyin ve ilkeyi doğrulayın.
     - İlke dosyası sözdizimi hatalarından muaf değilse, ilke dosyası hizmet tarafından kabul edilir.
     - İlke dosyası sözdizimi hataları içeriyorsa, ilke dosyası hizmet tarafından reddedilir.

## <a name="next-steps"></a>Sonraki adımlar
- [PowerShell kullanarak Azure kanıtlama ayarlama](quickstart-powershell.md)
- [Kod örneklerini kullanarak bir SGX kuşatma atturuntest](/samples/browse/?expanded=azure&terms=attestation)
