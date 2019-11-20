---
title: BuildingBlocks-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C bir özel ilkenin BuildingBlocks öğesini belirtin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ab2b11749aa57065a1a4d688b02fed97731ab7c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464739"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Buildingblocks** öğesi [TrustFrameworkPolicy](trustframeworkpolicy.md) öğesinin içine eklenir.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
 </BuildingBlocks>
```

**Buildingblocks** öğesi, tanımlı sırada belirtilmesi gereken aşağıdaki öğeleri içerir:

- [Claimsschema](claimsschema.md) -ilkenin bir parçası olarak başvurulabilen talep türlerini tanımlar. Talep şeması, talep türlerinizi bildirdiğiniz yerdir. Talep türü birçok programlama dilinde bir değişkene benzerdir. Uygulamanızın kullanıcısının verilerini toplamak, sosyal kimlik sağlayıcılarından talepler almak, özel bir REST API veri göndermek ve almak veya özel ilkeniz tarafından kullanılan tüm dahili verileri depolamak için talep türünü kullanabilirsiniz. 

- [Koşullar ve PredicateValidationsInput](predicates.md) -bir talebe yalnızca düzgün biçimlendirilmiş verilerin girildiğinden emin olmak için bir doğrulama işlemi gerçekleştirmenizi sağlar.
 
- [Claimstransformations](claimstransformations.md) -ilkenizde kullanılabilecek talep dönüştürmelerinin bir listesini içerir.  Talep dönüştürmesi bir talebi diğerine dönüştürür. Talep dönüşümünde, şöyle bir dönüşüm yöntemi belirtirsiniz: 
    - Bir dize talebinin durumunu belirtilen bir şekilde değiştirme. Örneğin, bir dizeyi küçük harfle büyük harfe değiştirme.
    - İki talebi karşılaştırma ve taleplerin eşleştiğini belirten true ile talep döndürme, aksi takdirde false.
    - İlkede belirtilen parametreden bir dize talebi oluşturuluyor.
    - Rastgele sayı oluşturucuyu kullanarak rastgele dize oluşturma.
    - Talep, belirtilen biçim dizesine göre biçimlendiriliyor. Bu dönüşüm C# `String.Format` yöntemini kullanır.
    
- Inputvalidation-bu öğe, *ve* ile benzer Boole toplamaları gerçekleştirmenize olanak *tanır.*

- [ContentDefinitions](contentdefinitions.md) -Kullanıcı yolculuğunda kullanmak üzere HTML5 şablonlarının URL 'lerini içerir. Bir özel ilkede, içerik tanımı Kullanıcı yolculuğunda belirtilen bir adım için kullanılan HTML5 sayfa URI 'sini tanımlar. Örneğin, oturum açma veya kaydolma, parola sıfırlama veya hata sayfaları. HTML5 dosyası için LoadUri 'yi geçersiz kılarak görünümü değiştirebilirsiniz. Veya gereksinimlerinize göre yeni içerik tanımları da oluşturabilirsiniz. Bu öğe, yerelleştirme KIMLIĞI kullanılarak yerelleştirilmiş bir kaynak başvurusu içerebilir.

- [Yerelleştirme](localization.md) -birden çok dili desteketmenize olanak tanır. İlkelerde yerelleştirme desteği, bir ilkede desteklenen dillerin listesini ayarlamanıza ve varsayılan bir dil seçmenizi sağlar. Dile özgü dizeler ve koleksiyonlar da desteklenir.


