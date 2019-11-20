---
title: 'Azure AD Connect: Geçişli kimlik doğrulaması-sık sorulan sorular | Microsoft Docs'
description: Azure Active Directory geçişli kimlik doğrulaması hakkında sık sorulan soruların yanıtları
services: active-directory
keywords: Azure AD Connect geçişli kimlik doğrulaması, Active Directory yüklemesi, Azure AD, SSO, çoklu oturum açma için gerekli bileşenler
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d21bf0f2ba7c93a35952d2eb2dd4df49bb3260b
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71290768"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory geçişli kimlik doğrulaması: Sık sorulan sorular

Bu makalede Azure Active Directory (Azure AD) geçişli kimlik doğrulama hakkında sıkça sorulan sorular ele alınmaktadır. Güncelleştirilmiş içerik için yeniden kontrol edin.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Azure AD 'de oturum açma yöntemlerinden hangilerinin, geçişli kimlik doğrulaması, Parola karması eşitlemesi ve Active Directory Federasyon Hizmetleri (AD FS) (AD FS), seçmem gerekir mi?

Çeşitli Azure AD oturum açma yöntemlerinin ve kuruluşunuzun doğru oturum açma yönteminin bir karşılaştırması için [Bu kılavuzu](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) gözden geçirin.

## <a name="is-pass-through-authentication-a-free-feature"></a>Geçiş kimlik doğrulaması ücretsiz bir özellik mi?

Geçişli kimlik doğrulaması, ücretsiz bir özelliktir. Kullanmak için Azure AD 'nin ücretli sürümlerinin olması gerekmez.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>[Microsoft Azure Almanya bulutta](https://www.microsoft.de/cloud-deutschland) ve [Microsoft Azure Kamu bulutta](https://azure.microsoft.com/features/gov/)geçişli kimlik doğrulaması var mı?

Hayır. Doğrudan kimlik doğrulaması yalnızca dünya çapındaki Azure AD örneğinde kullanılabilir.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>[Koşullu erişim](../active-directory-conditional-access-azure-portal.md) doğrudan kimlik doğrulamasıyla çalışır mı?

Evet. Azure Multi-Factor Authentication dahil tüm koşullu erişim özellikleri doğrudan kimlik doğrulamasıyla çalışır.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>"UserPrincipalName" yerine Kullanıcı adı olarak "alternatif KIMLIK" olarak geçiş kimlik doğrulamasını destekliyor mu?
Sınırlı bir ölçüde, geçişli kimlik doğrulaması, Azure AD Connect ' de yapılandırıldığında Kullanıcı adı olarak alternatif KIMLIĞI destekler. Önkoşul olarak, Azure AD Connect şirket içi Active Directory `UserPrincipalName` özniteliğini Azure AD ile eşitlemeniz gerekir. Bu, `UserPrincipalName` şirket içi ad ve Azure AD 'nin aynı olmasını sağlar. Şirket içi AD 'den Azure AD 'ye UPN olarak eşitlemek için başka bir öznitelik kullanmak istiyorsanız, Parola karması eşitleme veya AD FS kullanmanız gerekir. Daha fazla bilgi için [Azure AD Connect özel yüklemesi](how-to-connect-install-custom.md). Office 365 uygulamalarının `Alternate ID`hepsi desteklenmez. Belirli uygulamanın belge desteği bildirimine bakın.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Parola karması eşitlemesi doğrudan kimlik doğrulamasına geri dönüş görevi görür mi?

Hayır. Geçişli kimlik doğrulaması, Parola karması eşitlemesine otomatik olarak yük _devretmez_ . Kullanıcı oturum açma hatalarından kaçınmak için, [yüksek kullanılabilirlik](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)Için geçişli kimlik doğrulaması yapılandırmanız gerekir.

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Parola karması eşitlemeden doğrudan kimlik doğrulamaya geçiş yaparken ne olur?

Parola karması eşitlemeden oturum açma yöntemini doğrudan kimlik doğrulamaya geçirmek için Azure AD Connect kullandığınızda, geçişli kimlik doğrulama, yönetilen etki alanlarında kullanıcılarınız için birincil oturum açma yöntemi olur. Lütfen daha önce parola karması eşitlemesi tarafından eşitlenen tüm kullanıcıların parola karmalarını Azure AD 'de depolandığına emin olun.

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Bir [Azure AD uygulama ara sunucusu](../manage-apps/application-proxy.md) bağlayıcısını doğrudan kimlik doğrulama aracısıyla aynı sunucuya yükleyebilir miyim?

Evet. Doğrudan kimlik doğrulama aracısının yeniden markalı sürümleri, sürüm 1.5.193.0 veya üzeri, bu yapılandırmayı destekler.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Azure AD Connect ve doğrudan kimlik doğrulama aracısının hangi sürümlerine ihtiyacınız var?

Bu özelliğin çalışması için, doğrudan kimlik doğrulama aracısına yönelik Azure AD Connect ve 1.5.193.0 veya üzeri sürümler için sürüm 1.1.750.0 veya sonraki bir sürüme ihtiyacınız vardır. Windows Server 2012 R2 veya üzeri bir sürümü olan sunuculara tüm yazılımları yükler.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Kullanıcının parolasının süresi dolmuşsa ve doğrudan kimlik doğrulaması kullanarak oturum açmaya çalıştıklarında ne olur?

Belirli bir kullanıcı için [parola geri yazma](../authentication/concept-sspr-writeback.md) 'yı yapılandırdıysanız ve Kullanıcı geçişli kimlik doğrulaması kullanarak oturum açarsa, parolalarını değiştirebilirler veya sıfırlayabilirler. Parolalar, beklendiği gibi şirket içi Active Directory geri yazılır.

Belirli bir kullanıcı için parola geri yazma 'yı yapılandırmadıysanız veya kullanıcıya geçerli bir Azure AD lisansı atanmamışsa, Kullanıcı buluttaki parolasını güncelleştiremez. Parolasının süresi dolmuşsa bile parolalarını güncelleştiremez. Bunun yerine Kullanıcı şu iletiyi görür: "Kuruluşunuz bu sitede parolanızı güncelleştirmenize izin vermiyor. Kuruluşunuzun önerdiği yönteme göre güncelleştirin veya yardıma ihtiyacınız varsa yöneticinize sorun. " Kullanıcı veya yönetici, şirket içi Active Directory parolalarını sıfırlamalıdır.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Geçiş kimlik doğrulaması, deneme yanılma, parola saldırılarına karşı sizi nasıl koruyabilirim?

[Akıllı kilitleme hakkındaki bilgileri okuyun](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Geçiş kimlik doğrulama aracıları 80 ve 443 bağlantı noktalarından nasıl iletişim kurar?

- Kimlik doğrulama aracıları, tüm özellik işlemleri için 443 numaralı bağlantı noktası üzerinden HTTPS istekleri yapar.
- Kimlik doğrulama aracıları, SSL sertifika iptal listelerini (CRL 'Ler) indirmek için 80 numaralı bağlantı noktası üzerinden HTTP istekleri yapar.

     >[!NOTE]
     >Son güncelleştirmeler, özelliğin gerektirdiği bağlantı noktası sayısını azalttı. Daha eski Azure AD Connect veya kimlik doğrulama Aracısı sürümüne sahipseniz, bu bağlantı noktalarını da açık tutun: 5671, 8080, 9090, 9091, 9350, 9352 ve 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Doğrudan kimlik doğrulama aracıları giden bir Web proxy sunucusu üzerinden iletişim kurabilir mi?

Evet. Şirket içi ortamınızda Web proxy otomatik bulma (WPAD) etkinleştirilirse, kimlik doğrulama aracıları otomatik olarak ağda bir Web proxy sunucusu bulmayı ve kullanmayı dener.

Ortamınızda WPAD yoksa, bir geçişli kimlik doğrulama aracısının Azure AD ile iletişim kurmasına izin vermek için proxy bilgilerini (aşağıda gösterildiği gibi) ekleyebilirsiniz:
- Sunucu üzerinde doğrudan kimlik doğrulama aracısını yüklemeden önce Internet Explorer 'da proxy bilgilerini yapılandırın. Bu, kimlik doğrulama aracısının yüklemesini tamamlamanızı sağlar, ancak yine de yönetici portalında **etkin değil** olarak görünür.
- Sunucusunda "C:\Program Files\Microsoft Azure AD Connect Authentication Agent" bölümüne gidin.
- "Azureadconnectauthenticationagentservice" yapılandırma dosyasını düzenleyin ve aşağıdaki satırları ekleyin ("http\://contosoproxy.com:8080" değerini gerçek proxy adresinizle değiştirin):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Aynı sunucuya iki veya daha fazla geçişli kimlik doğrulama Aracısı yükleyebilir miyim?

Hayır, tek bir sunucuya yalnızca bir geçişli kimlik doğrulama aracısı yükleyebilirsiniz. Yüksek kullanılabilirlik için geçişli kimlik doğrulaması yapılandırmak istiyorsanız [buradaki yönergeleri izleyin](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Doğrudan kimlik doğrulama aracıları tarafından kullanılan sertifikaları el ile yenilemem gerekir mi?

Her geçişli kimlik doğrulama Aracısı ve Azure AD arasındaki iletişimin sertifika tabanlı kimlik doğrulaması kullanılarak güvenliği sağlanır. Bu [Sertifikalar Azure AD tarafından her birkaç ayda bir otomatik olarak yenilenir](how-to-connect-pta-security-deep-dive.md#operational-security-of -the-authentication-agents). Bu sertifikaları el ile yenilemeniz gerekmez. Daha eski olan sertifikaları gerektiği şekilde temizleyebilirsiniz.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Doğrudan kimlik doğrulama aracısını kaldırmak Nasıl yaparım??

Bir geçişli kimlik doğrulama Aracısı çalıştığı sürece, etkin kalır ve Kullanıcı oturum açma isteklerini sürekli olarak işler. Bir kimlik doğrulama aracısını kaldırmak istiyorsanız, **Denetim Masası-> programlar-> programlar ve Özellikler** ' e gidin ve hem **Microsoft Azure AD Connect kimlik doğrulama aracısını** hem de **Microsoft Azure AD Connect Agent Güncelleştirici** öğesini kaldırın programlarınız.

Yukarıdaki adımı tamamladıktan sonra [Azure Active Directory Yönetim merkezinde](https://aad.portal.azure.com) doğrudan kimlik doğrulama dikey penceresini kontrol ederseniz, kimlik doğrulama aracısının **etkin değil**olarak gösterildiğini görürsünüz. Bu _beklenen_bir değer. Kimlik doğrulama Aracısı birkaç gün sonra otomatik olarak listeden bırakılır.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Azure AD 'de oturum açmak için zaten AD FS kullanıyorum. Nasıl yaparım? doğrudan kimlik doğrulamaya geçiş yapılsın mı?

AD FS (veya diğer Federasyon teknolojileri) üzerinden doğrudan kimlik doğrulamaya geçiş yapıyorsanız, [burada](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true)yayımlanan ayrıntılı dağıtım kılavuzumuzu izlemenizi kesinlikle öneririz.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Çoklu orman Active Directory ortamında doğrudan kimlik doğrulamasını kullanabilir miyim?

Evet. Active Directory ormanlarınız arasında orman güvenleri varsa ve ad soneki yönlendirmesi doğru yapılandırılmışsa, çok ormanlı ortamlar desteklenir.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Geçişli kimlik doğrulaması, birden fazla kimlik doğrulama aracısında yük dengelemesi sağlar mi?

Hayır, birden çok geçişli kimlik doğrulama Aracısı yüklemek yalnızca [yüksek kullanılabilirliği](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)sağlar. Kimlik doğrulama aracıları arasında belirleyici yük dengelemesi sağlamaz. Herhangi bir kimlik doğrulama Aracısı (rastgele), belirli bir Kullanıcı oturum açma isteğini işleyebilir.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Kaç tane geçişli kimlik doğrulama aracısının yüklenmesi gerekir?

Birden çok geçişli kimlik doğrulama Aracısı yükleme, [yüksek kullanılabilirlik](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)sağlar. Ancak, kimlik doğrulama aracıları arasında belirleyici yük dengelemesi sağlamaz.

Kiracınızda görmeyi düşündüğünüz oturum açma isteklerinin en yüksek ve ortalama yükünü göz önünde bulundurun. Bir kıyaslama olarak, tek bir kimlik doğrulama Aracısı standart 4 çekirdekli bir CPU, 16 GB RAM sunucusunda 300 ila 400 kimlik doğrulaması işleyebilir.

Ağ trafiğini tahmin etmek için aşağıdaki boyutlandırma kılavuzunu kullanın:
- Her isteğin yük boyutu (0,5 K + 1K * num_of_agents) bayt; Yani, Azure AD 'den kimlik doğrulama aracısına yönelik veriler. Burada, "num_of_agents", kiracınızda kayıtlı kimlik doğrulama aracılarının sayısını gösterir.
- Her yanıtın yük boyutu 1K bayttır; Yani, kimlik doğrulama aracısından Azure AD 'ye ait veriler.

Çoğu müşteri için toplamda iki veya üç kimlik doğrulama Aracısı yüksek kullanılabilirlik ve kapasite için yeterlidir. Oturum açma gecikmesini geliştirmek için, kimlik doğrulama aracılarını etki alanı denetleyicilerinize yakın bir şekilde yüklemelisiniz.

>[!NOTE]
>Kiracı başına 40 kimlik doğrulama aracısından oluşan bir sistem limiti vardır.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>İlk geçişli kimlik doğrulama aracısını Azure AD Connect çalıştıran bir sunucuya yükleyebilir miyim?

Hayır, bu _senaryo desteklenmiyor._

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Doğrudan kimlik doğrulamayı etkinleştirmek için neden yalnızca bulutta bir genel yönetici hesabına ihtiyacım var?

Yalnızca bulutta yer alan bir genel yönetici hesabı kullanarak doğrudan kimlik doğrulamayı etkinleştirmeniz veya devre dışı bırakmanız önerilir. [Yalnızca bulut genel yönetici hesabı ekleme](../active-directory-users-create-azure-portal.md)hakkında bilgi edinin. Bu şekilde yapmanız, kiracınızın kilitlenmemesini sağlar.

## <a name="how-can-i-disable-pass-through-authentication"></a>Doğrudan kimlik doğrulamasını nasıl devre dışı bırakabilirim?

Azure AD Connect Sihirbazı 'nı yeniden çalıştırın ve Kullanıcı oturum açma yöntemini doğrudan geçiş kimlik doğrulamasından başka bir yönteme değiştirin. Bu değişiklik, Kiracıdaki geçişli kimlik doğrulamasını devre dışı bırakır ve kimlik doğrulama aracısını sunucudan kaldırır. Diğer sunuculardan kimlik doğrulama aracılarını el ile kaldırmanız gerekir.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Bir geçişli kimlik doğrulama aracısını kaldırdığımda ne olur?

Bir sunucudan bir geçişli kimlik doğrulama aracısını kaldırırsanız, sunucunun oturum açma isteklerini kabul etmeyi durdurmasına neden olur. Kiracınızda Kullanıcı oturumu açma özelliğinin kesilmesini önlemek için, bir geçişli kimlik doğrulama aracısını kaldırmadan önce başka bir kimlik doğrulama aracısının çalıştığından emin olun.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>İlk olarak AD FS kullanılarak ayarlanan daha eski bir kiracıya sahibim.  Kısa süre önce PTA 'a geçirdik, ancak artık UPN değişikliklerimizi Azure AD 'ye eşitliyoruz.  UPN değişikliklerimiz neden eşitlenmemiş?

Y: Aşağıdaki koşullarda şirket içi UPN değişiklikleriniz şu durumlarda eşitlenemeyebilir:

- Azure AD kiracınız 15 Haziran 2015 ' den önce oluşturulmuştur
- Başlangıçta kimlik doğrulaması için AD FS kullanarak Azure AD kiracınız ile ilk kez federe olursunuz
- Kimlik doğrulaması olarak PTA kullanan yönetilen kullanıcılara sahip olmaya geçiş yapın

Bunun nedeni, yalnızca 15 Haziran 2015 ' den önce oluşturulan kiracıların varsayılan davranışının UPN değişikliklerinin engellenmesinden kaynaklanır.  UPN değişikliklerinin engellemesini kaldırmak için aşağıdaki PowerShell cmdlt ' i çalıştırmanız gerekir:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers-Enable $True`

15 Haziran 2015 ' den sonra oluşturulan kiracılar, UPN değişikliklerini eşitlemeye yönelik varsayılan davranışa sahiptir.   



## <a name="next-steps"></a>Sonraki adımlar
- [Geçerli sınırlamalar](how-to-connect-pta-current-limitations.md): Hangi senaryoların desteklendiğini ve hangilerinin desteklenmediğini öğrenin.
- [Hızlı başlangıç](how-to-connect-pta-quick-start.md): Azure AD geçişli kimlik doğrulaması ile çalışmaya başlayın.
- [AD FS 'den geçişli kimlik doğrulamaya geçiş](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) -AD FS (veya diğer Federasyon teknolojileri) üzerinden doğrudan kimlik doğrulamaya geçiş yapmak için ayrıntılı kılavuz.
- [Akıllı kilitleme](../authentication/howto-password-smart-lockout.md): Kullanıcı hesaplarını korumak için kiracınızda akıllı kilitleme özelliğini nasıl yapılandıracağınızı öğrenin.
- [Teknik kapsamlı](how-to-connect-pta-how-it-works.md)bakış: Geçişli kimlik doğrulama özelliğinin nasıl çalıştığını anlayın.
- [Sorun giderme](tshoot-connect-pass-through-authentication.md): Doğrudan kimlik doğrulama özelliğiyle yaygın sorunları çözmeyi öğrenin.
- [Güvenlik derinlemesine](how-to-connect-pta-security-deep-dive.md)bakış: Doğrudan kimlik doğrulama özelliği hakkında ayrıntılı teknik bilgiler alın.
- [Azure AD sorunsuz SSO](how-to-connect-sso.md): Bu tamamlayıcı özellik hakkında daha fazla bilgi edinin.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Yeni özellik isteklerini dosya olarak yüklemek için Azure Active Directory forumunu kullanın.

