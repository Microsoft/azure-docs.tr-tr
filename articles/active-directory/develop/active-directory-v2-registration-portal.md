---
title: Uygulama kayıt portalı yardım konuları
titleSuffix: Microsoft identity platform
description: Microsoft uygulama kayıt portalındaki çeşitli özelliklerin açıklaması.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61787e7a86a1c40ec04fb7a3d605158c85782d94
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803399"
---
# <a name="app-registration-reference"></a>Uygulama kayıt başvurusu
Bu belge, Azure portal [uygulama kayıtları](https://aka.ms/appregistrations) deneyiminde bulunan çeşitli özelliklerin bağlamını ve açıklamalarını sağlar.

## <a name="my-applications-or-converged-applications"></a>Uygulamalarım veya yakınsanmış uygulamalar
Bu liste, Microsoft Identity platform (v 2.0) uç noktasıyla birlikte kullanılmak üzere kaydedilen tüm uygulamalarınızı içerir. Bu uygulamalar, kullanıcıların hem kişisel Microsoft hesapları hem de iş/okul hesaplarıyla Azure Active Directory oturum açabilme özelliğine sahiptir. Kimlik platformu uç noktası hakkında daha fazla bilgi edinmek için bkz. [v 2.0 genel bakış](active-directory-appmodel-v2-overview.md). Bu uygulamalar, `https://login.live.com`Microsoft hesabı kimlik doğrulama uç noktası ile tümleştirme için de kullanılabilir.

## <a name="azure-ad-only-applications"></a>Yalnızca Azure AD uygulamaları
Bu liste, Azure AD v 1.0 uç noktasıyla birlikte kullanılmak üzere kaydedilen tüm uygulamalarınızı içerir. Bu uygulamalar yalnızca Azure Active Directory ' den iş/okul hesaplarıyla kullanıcıları oturum açabilme özelliğine sahiptir. Bu liste, [Azure portal](https://portal.azure.com) **uygulama kayıtları** deneyimi kullanılarak kaydedilmiş uygulamaları içerir.

## <a name="live-sdk-applications"></a>Canlı SDK Uygulamaları
Bu liste, yalnızca Microsoft hesabı ile kullanım için kaydedilmiş tüm uygulamalarınızı içerir. Azure Active Directory ile kullanım için etkinleştirilmemelidir. Bu, daha önce `https://account.live.com/developers/applications`adresinden MSA geliştirici portalına kayıtlı olan herhangi bir uygulamayı bulabileceğiniz yerdir. Daha önce `https://account.live.com/developers/applications` gerçekleştirdiğiniz tüm işlevler artık [uygulama kayıtları](https://aka.ms/appregistrations)gerçekleştirilebilir.

## <a name="application-secrets"></a>Uygulama gizli dizileri
Uygulama gizli dizileri, uygulamanızın Azure AD ile güvenilir [istemci kimlik doğrulaması](https://tools.ietf.org/html/rfc6749#section-2.3) gerçekleştirmesini sağlayan kimlik bilgileridir. OAuth & OpenID Connect içinde, uygulama gizli dizisi genellikle `client_secret`olarak adlandırılır. V 2.0 protokolünde, bir Web adreslenebilir konumda (`https` şeması kullanarak) bir güvenlik belirteci alan tüm uygulamalar, bu güvenlik belirtecinin kullanıma alınması sırasında kendisini Azure AD 'ye tanıtmak için bir uygulama gizli anahtarı kullanmalıdır. Ayrıca, bir cihazda belirteçleri alan tüm yerel istemcilerin, istemci kimlik doğrulaması gerçekleştirmek için bir uygulama gizli anahtarı kullanılması yasaktır. Bu etkilenmeden, güvenli olmayan ortamlarda gizli dizi depolanmasını ister.

Her bir uygulama, belirli bir zamanda iki geçerli uygulama parolası içerebilir. İki gizli dizi tutarak, uygulamanızın tamamı boyunca düzenli anahtar geçişi gerçekleştirebilirsiniz. Uygulamanızı tamamen yeni bir gizli dizi ile geçirdikten sonra, eski parolayı silip yeni bir parola sağlayabilirsiniz.

Şu anda, uygulama kayıt portalında yalnızca iki tür uygulama gizli listesine izin verilir. **Yeni parola oluştur** seçeneğinin belirlenmesi, uygulamanızda kullanabileceğiniz ilgili veri deposunda paylaşılan bir gizli dizi oluşturur ve depolar. **Yeni anahtar çifti oluştur** seçildiğinde, Azure AD 'de istemci kimlik doğrulaması için indirilebilen ve kullanılabilecek yeni bir ortak/özel anahtar çifti oluşturulur. **Ortak anahtarı karşıya yükle** seçeneğinin belirlenmesi, kendi ortak/özel anahtar çiftini kullanmanıza olanak sağlar.
Ortak anahtar içeren bir sertifikayı karşıya yüklemeniz gerekir.

## <a name="profile"></a>Profil
Uygulama kayıt portalının profil bölümü, uygulamanız için oturum açma sayfasını özelleştirmek üzere kullanılabilir. Bu sırada, oturum açma sayfasının uygulama logosunu, hizmet koşullarını ve gizlilik bildirimi URL 'sini değiştirebilirsiniz. Logo, 15 KB veya daha küçük bir GIF, PNG veya JPEG dosyasında bir saydam 48 x 48 veya 50 x 50 piksel görüntüsü olmalıdır. Değerleri değiştirmeyi ve ortaya çıkan oturum açma sayfasını görüntülemeyi deneyin!

## <a name="live-sdk-support"></a>Canlı SDK desteği
"Canlı SDK desteği" ni etkinleştirdiğinizde, oluşturduğunuz tüm uygulama gizli dizileri hem Azure AD hem de Microsoft hesabı veri depolarına sağlanacak. Bu, uygulamanızın doğrudan Microsoft hesabı hizmeti (login.live.com) ile tümleşmesini sağlar. Doğrudan Microsoft hesabı 'nı kullanarak bir uygulama oluşturmak istiyorsanız (v 2.0 uç noktasını kullanmanın aksine) canlı SDK desteğinin etkinleştirildiğinden emin olun.

Canlı SDK desteğini devre dışı bırakmak, uygulama gizliliğinin yalnızca Azure AD veri deposuna yazılmasını sağlar. Azure AD veri deposu, bu BT 'nin FMA uyumluluğu gibi belirli standartları karşıladığından izin veren kurumsal düzeyde yönetmelikler içerir. Canlı SDK desteğini etkinleştirirseniz, uygulamanız bu standartlardan bazıları ile uyumluluk elde edebilir.

Yalnızca v 2.0 uç noktasını kullanmayı planlıyorsanız, canlı SDK desteğini güvenle devre dışı bırakabilirsiniz.

