---
title: Azure Active Directory B2C Kullanıcı akışları | Microsoft Docs
description: Azure Active Directory B2C Genişletilebilir ilke çerçevesi ve çeşitli Kullanıcı akışları oluşturma hakkında daha fazla bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 08da04a8bf167c99ef2384a9714034ae1865bec1
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065368"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C Kullanıcı akışları

Azure Active Directory B2C Genişletilebilir ilke çerçevesi (Azure AD B2C), hizmetin temel kuvvetsidir. İlkeler kaydolma, oturum açma veya profil düzenlemesi gibi kimlik deneyimlerini tamamen anlatmaktadır. En yaygın kimlik görevlerini ayarlamanıza yardımcı olması için Azure AD B2C portalı, **Kullanıcı akışları**adlı önceden tanımlanmış ve yapılandırılabilir ilkeler içerir.

## <a name="what-are-user-flows"></a>Kullanıcı akışları nelerdir?

Bir Kullanıcı akışı, aşağıdaki ayarları yapılandırarak uygulamalarınızdaki davranışları denetlemenizi sağlar:

- Facebook veya yerel hesaplar gibi sosyal hesaplar gibi, oturum açma için kullanılan hesap türleri
- Tüketiciden toplanacak öznitelikler (örneğin, ilk ad, posta kodu ve ayakkabı boyutu)
- Azure Multi-Factor Authentication
- Kullanıcı arabiriminin özelleştirilmesi
- Uygulamanın bir belirteçte talepler olarak aldığı bilgiler

Kiracınızda farklı türlerde birçok kullanıcı akışı oluşturabilir ve bunları gerektiği şekilde uygulamalarınızda kullanabilirsiniz. Kullanıcı akışları, uygulamalar arasında yeniden kullanılabilir. Bu esneklik, kodunuzda en az değişiklik yapmadan kimlik deneyimlerini tanımlamanızı ve değiştirmenizi sağlar. Uygulamanız, bir Kullanıcı akış parametresi içeren standart bir HTTP kimlik doğrulama isteği kullanarak bir Kullanıcı akışı tetikler. Özelleştirilmiş bir [belirteç](active-directory-b2c-reference-tokens.md) yanıt olarak alınır.

Aşağıdaki örneklerde, kullanılacak kullanıcı akışını belirten "p" sorgu dizesi parametresi gösterilmektedir:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Kullanıcı akışı sürümleri

Azure portal, [Kullanıcı akışlarının yeni sürümleri](user-flow-versions.md) her zaman ekleniyor. Azure AD B2C kullanmaya başladığınızda, test ettiğiniz Kullanıcı akışları kullanmanız önerilir. Yeni bir Kullanıcı akışı oluşturduğunuzda **Önerilen** sekmesinden ihtiyacınız olan kullanıcı akışını seçersiniz.

Şu anda aşağıdaki Kullanıcı akışları önerilir:

- Kaydolma ve oturum açma deneyimlerinin her ikisi de tek bir yapılandırmayla kaydolun **ve oturum açın** . Kullanıcılar, bağlama göre doğru yolun altına alınır. Bu kullanıcı akışını bir **kaydolma** Kullanıcı akışı veya **oturum açma** Kullanıcı akışı üzerinden kullanmanız önerilir.
- **Profil düzenleme** -kullanıcıların profil bilgilerini düzenlemesine olanak sağlar.
- **Parola sıfırlama** -kullanıcıların parolalarını sıfırlayıp sıfırlayamayacağını ve bunların nasıl sıfırlanamayacağını yapılandırmanıza olanak sağlar.

## <a name="linking-user-flows"></a>Kullanıcı akışlarını bağlama

Yerel hesaplarla **kaydolma veya oturum açma** Kullanıcı akışı, deneyimin ilk sayfasında **parolayı unuttum?** bağlantısını içerir. Bu bağlantıya tıkladığınızda parola sıfırlama Kullanıcı akışı otomatik olarak tetiklenemez.

Bunun yerine, hata kodu `AADB2C90118` uygulamanıza döndürülür. Uygulamanızın, parolayı sıfırlayan belirli bir kullanıcı akışını çalıştırarak bu hata kodunu işlemesi gerekir. Bir örneği görmek için, Kullanıcı akışlarının bağlantısını gösteren [basit bir ASP.net örneğine](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) göz atın.

## <a name="email-address-storage"></a>E-posta adresi depolaması

Bir Kullanıcı akışının parçası olarak bir e-posta adresi gerekebilir. Kullanıcı bir sosyal kimlik sağlayıcısı ile kimlik doğrulaması gerçekleştiriyorsa, e-posta adresi, **Otherpostalarını** özelliğinde depolanır. Yerel bir hesap Kullanıcı adını temel alıyorsa, e-posta adresi güçlü bir kimlik doğrulama ayrıntısı özelliğinde depolanır. Yerel bir hesap bir e-posta adresini temel alıyorsa, e-posta adresi **Signınnames** özelliğinde depolanır.

Bu durumda e-posta adresinin doğrulanması garanti edilmez. Bir kiracı yöneticisi, yerel hesaplar için temel ilkelerde e-posta doğrulamayı devre dışı bırakabilir. E-posta adresi doğrulama etkin olsa bile, bir sosyal kimlik sağlayıcısından geliyorsa ve değiştirilmedikleri durumlarda adresler doğrulanmaz.

Yalnızca **Otherpostalarını** ve **signınnames** özellikleri Active Directory Graph API aracılığıyla sunulur. Güçlü kimlik doğrulama ayrıntısı özelliğindeki e-posta adresi kullanılamıyor.

## <a name="next-steps"></a>Sonraki adımlar

Önerilen Kullanıcı akışlarını oluşturmak için [öğreticideki yönergeleri izleyin: Bir Kullanıcı akışı](tutorial-create-user-flows.md)oluşturun.


