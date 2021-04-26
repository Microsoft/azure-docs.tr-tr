---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98951552"
---
## <a name="configure-the-relying-party-policy"></a>Bağlı olan taraf ilkesini yapılandırma

Bağlı olan taraf ilkesi, örneğin [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), Azure AD B2C yürütecektir Kullanıcı yolculuğu belirtir. [Bağlı olan taraf](../articles/active-directory-b2c/relyingparty.md)Içinde **Defaultuseryolculuney** öğesini bulun. **Referenceıd** 'yi, kimlik sağlayıcısını eklediğiniz kullanıcı yolculuğu kimliğiyle eşleşecek şekilde güncelleştirin. 

Aşağıdaki örnekte, `CustomSignUpOrSignIn` Kullanıcı yolculuğu Için **referenceıd** şu şekilde ayarlanır `CustomSignUpOrSignIn` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Özel ilkeyi karşıya yükle

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **İlkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.
1. **Özel Ilkeyi karşıya yükle**' yi seçin ve ardından, değiştirdiğiniz iki ilke dosyasını aşağıdaki sırada karşıya yükleyin: uzantı ilkesi, örneğin, `TrustFrameworkExtensions.xml` `SignUpSignIn.xml` .



