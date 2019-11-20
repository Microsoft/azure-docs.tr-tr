---
title: Şirket içi kaynaklara yönelik SSO 'nun Azure AD 'ye katılmış cihazlarda nasıl çalıştığı. | Microsoft Docs
description: Hibrit Azure Active Directory'ye katılmış cihazları elle nasıl yapılandıracağınızı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14e7a4389c192dde8d086a69a35114f3b8b33e96
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562183"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Şirket içi kaynaklara yönelik SSO, Azure AD 'ye katılmış cihazlarda nasıl çalışacaktır?

Azure Active Directory (Azure AD) katılmış bir cihazın, kiracının bulut uygulamalarına çoklu oturum açma (SSO) deneyimi sunabilme olasılığı da vardır. Ortamınızda şirket içi Active Directory (AD) varsa, bu cihazlardaki SSO deneyimini bu cihazlarda genişletebilirsiniz.

Bu makalede bunun nasıl çalıştığı açıklanır.

## <a name="how-it-works"></a>Nasıl çalışır? 

Yalnızca tek bir kullanıcı adını ve parolayı hatırlamanız gerektiğinden, SSO kaynaklarınızın erişimini basitleştirir ve ortamınızın güvenliğini geliştirir. Azure AD 'ye katılmış bir cihazla, kullanıcılarınız ortamınızdaki bulut uygulamalarına yönelik SSO deneyimine sahiptir. Ortamınızda bir Azure AD ve şirket içi AD varsa, muhtemelen, SSO deneyiminizin kapsamını şirket içi Iş kolu (LOB) Uygulamalarınız, dosya paylaşımlarınız ve yazıcılar için genişletebilirsiniz.  

Azure AD 'ye katılmış cihazlarda, şirket içi AD ortamınız hakkında hiçbir bilgi bulunmamaktadır. Ancak, Azure AD Connect ile şirket içi AD 'niz hakkında bu cihazlara ek bilgiler sağlayabilirsiniz.
Hem Azure AD hem de şirket içi AD olan bir ortamda karma ortam de vardır. Karma bir ortamınız varsa, şirket içi kimlik bilgilerinizi buluta eşitlemeye yönelik Azure AD Connect zaten dağıtılmış olması olasıdır. Eşitleme sürecinin bir parçası olarak, şirket içi etki alanı bilgilerini Azure AD 'ye eşitler Azure AD Connect. Bir Kullanıcı Karma ortamda Azure AD 'ye katılmış bir cihazda oturum açtığında:

1. Azure AD, kullanıcının cihaza geri üye olduğu şirket içi etki alanının adını gönderir. 
1. Yerel güvenlik yetkilisi (LSA) hizmeti cihazda Kerberos kimlik doğrulamasına izin vermez.

Kullanıcının şirket içi etki alanındaki bir kaynağa erişim denemesi sırasında cihaz:

1. , Etki alanı denetleyicisini (DC) bulmak için etki alanı bilgilerini kullanır. 
1. Kullanıcının kimliğinin doğrulanmasını sağlamak için, bulunan DC 'ye şirket içi etki alanı bilgilerini ve Kullanıcı kimlik bilgilerini gönderir.
1. AD ile Birleşik kaynaklara erişmek için kullanılan bir Kerberos [anahtar verme anahtarı (TGT)](https://docs.microsoft.com/windows/desktop/secauthn/ticket-granting-tickets) alır.

**Windows Ile tümleşik kimlik doğrulaması** için yapılandırılan tüm uygulamalar, bir Kullanıcı ERIŞMEYE çalıştığında SSO 'yu sorunsuz bir şekilde alır.  

Iş için Windows Hello, Azure AD 'ye katılmış bir cihazdan şirket içi SSO 'yu etkinleştirmek üzere ek yapılandırma gerektirir. Daha fazla bilgi için bkz. [iş Için Windows Hello 'yu kullanarak şirket Içi çoklu oturum açma Için Azure AD 'ye katılmış cihazları yapılandırma](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Ne alacaksınız

SSO ile, bir Azure AD 'ye katılmış cihazda şunları yapabilirsiniz: 

- AD üye sunucusunda bir UNC yoluna erişme
- Windows ile tümleşik güvenlik için yapılandırılmış bir AD üyesi Web sunucusuna erişin 

Şirket içi AD 'nizi bir Windows cihazından yönetmek istiyorsanız, [uzak sunucu yönetim araçları Windows 10](https://www.microsoft.com/download/details.aspx?id=45520)' u yükleyebilirsiniz.

Şunu kullanabilirsiniz:

- Tüm AD nesnelerini yönetmek için Kullanıcı ve bilgisayar (ADUC) ek bileşeni Active Directory. Ancak, el ile bağlanmak istediğiniz etki alanını belirtmeniz gerekir.
- AD ile Birleşik bir DHCP sunucusunu yönetmek için DHCP ek bileşeni. Ancak, DHCP sunucusunun adını veya adresini belirtmeniz gerekebilir.
 
## <a name="what-you-should-know"></a>Bilmeniz gerekenler

Gerekli etki alanlarıyla ilgili verilerin eşitlendiğinden emin olmak için Azure AD Connect [etki alanı tabanlı filtrelemesini](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) ayarlamanız gerekebilir.

Active Directory makine kimlik doğrulamasına bağımlı olan uygulamalar ve kaynaklar, Azure AD 'ye katılmış cihazların AD 'de bir bilgisayar nesnesi olmadığından çalışmaz. 

Azure AD 'ye katılmış bir cihazdaki diğer kullanıcılarla dosya paylaşamazsınız.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure Active Directory cihaz yönetimi nedir?](overview.md) 
