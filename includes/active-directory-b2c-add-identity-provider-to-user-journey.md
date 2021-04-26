---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98674246"
---
## <a name="add-a-user-journey"></a>Kullanıcı yolculuğu ekleme 

Bu noktada, kimlik sağlayıcısı ayarlanmıştır, ancak oturum açma sayfalarında henüz mevcut değildir. Kendi özel Kullanıcı yolculuğu yoksa, var olan bir şablon Kullanıcı yolculuğunun bir yinelemesini oluşturun, aksi takdirde bir sonraki adımla devam edin. 

1. *TrustFrameworkBase.xml* dosyasını başlangıç paketinden açın.
1. Dahil olan **Userelde ney** öğesinin tüm içeriğini bulup kopyalayın `Id="SignUpOrSignIn"` .
1. *TrustFrameworkExtensions.xml* açın ve **User, neys** öğesini bulun. Öğe yoksa, bir tane ekleyin.
1. **User, neys** öğesinin bir alt öğesi olarak kopyaladığınız **User, ney** öğesinin tüm içeriğini yapıştırın.
1. Kullanıcı yolculuğunun kimliğini yeniden adlandırın. Örneğin, `Id="CustomSignUpSignIn"`.

## <a name="add-the-identity-provider-to-a-user-journey"></a>Kimlik sağlayıcısını Kullanıcı yolculuğuna ekleme 

Artık bir Kullanıcı yolculuğu olduğuna göre, yeni kimlik sağlayıcısını Kullanıcı yolculuğuna ekleyin. İlk olarak bir oturum açma düğmesi ekleyin ve ardından düğmeyi bir eyleme bağlayın. Bu eylem, daha önce oluşturduğunuz teknik profildir.

1. `Type="CombinedSignInAndSignUp"`Kullanıcı yolculuğunda veya dahil olmak üzere Orchestration Step öğesini bulun `Type="ClaimsProviderSelection"` . Genellikle ilk düzenleme adımıdır. **Claimsproviderseçimleri** öğesi, bir kullanıcının oturum açmasını sağlayan kimlik sağlayıcılarının bir listesini içerir. Öğelerin sırası, kullanıcıya sunulan oturum açma düğmelerinin sırasını denetler. Bir **Claimsproviderselection** XML öğesi ekleyin. **Targetclaimsexchangeıd** değerini kolay bir ad olarak ayarlayın.

1. Sonraki düzenleme adımında bir **Claimsexchange** öğesi ekleyin. **Kimliği** hedef talep değişim kimliği değerine ayarlayın. **TechnicalProfileReferenceId** değerini daha önce oluşturduğunuz teknik profilin kimliğiyle güncelleştirin.

Aşağıdaki XML, kimlik sağlayıcısıyla Kullanıcı yolculuğunun ilk iki düzenleme adımını göstermektedir: