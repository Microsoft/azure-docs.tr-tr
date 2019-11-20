---
title: Azure Identity & Access Security en iyi uygulamaları | Microsoft Docs
description: Bu makalede, yerleşik Azure özelliklerini kullanarak kimlik yönetimi ve erişim denetimi için en iyi yöntemler sunulmaktadır.
services: security
documentationcenter: na
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 52ef3a9b1df058d5d2e954b424094f9dbaeba15b
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053350"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure kimlik yönetimi ve erişim denetimi en iyi güvenlik uygulamaları

Bu makalede, bir Azure kimlik yönetimi ve erişim denetimi güvenliği en iyi yöntemleri koleksiyonu ele alınmaktadır. Bu en iyi uygulamalar, [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) ile deneyimimizden ve bizim gibi müşterilerin deneyimlerinden türetilir.

Her bir en iyi uygulama için şunları açıkladık:

* En iyi yöntem
* Bu en iyi yöntemi neden etkinleştirmek istiyorsunuz?
* En iyi uygulamayı etkinleştiremeyebilirsiniz ne olur?
* En iyi yöntem için olası alternatifler
* En iyi uygulamayı nasıl etkinleştirebileceğinizi öğrenin

Bu Azure kimlik yönetimi ve erişim denetimi güvenlik en iyi uygulamaları makalesi, bu makalede yazıldığı sırada mevcut olduğu için konsensus görüşlerini ve Azure platform özelliklerini ve özellik kümelerini temel alır.

Bu makaleye yazma işlemi, dağıtım sonrasında "[kimlik altyapınızı güvenli hale getirmek için 5 adımla](steps-secure-identity.md)", temel özelliklerden bazıları konusunda size yol gösteren ve servislere.

Değişiklikler ve teknolojiler zamana göre değişir ve bu makale, bu değişiklikleri yansıtacak şekilde düzenli olarak güncelleştirilir.

Bu makalede ele alınan Azure kimlik yönetimi ve erişim denetimi güvenlik en iyi uygulamaları şunlardır:

* Kimliği birincil güvenlik çevre birimi olarak değerlendir
* Kimlik yönetimini merkezileştirme
* Bağlı kiracılar yönetme
* Çoklu oturum açmayı etkinleştir
* Koşullu erişimi aç
* Rutin güvenlik iyileştirmeleri için plan yapın
* Parola yönetimini etkinleştirme
* Kullanıcılar için Multi-Factor doğrulamasını zorunlu kıl
* Rol tabanlı erişim denetimi kullanma
* Ayrıcalıklı hesapların daha düşük pozlaması
* Kaynakların bulunduğu denetim konumları
* Depolama kimlik doğrulaması için Azure AD kullanma

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Kimliği birincil güvenlik çevre birimi olarak değerlendir

Çok sayıda kimliği, güvenliğin güvenliği için birincil çevre olarak kabul etmeyi düşünün. Bu, ağ güvenliğine ilişkin geleneksel odadan bir kaydırmadır. Ağ duvarlar daha fazla bilgi edinmeleri ve bu çevre savunması, KCG [cihazlarının ve](https://aka.ms/byodcg) bulut uygulamalarının açılımına daha önce olduğu kadar etkili olamaz.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) , kimlik ve erişim yönetimi için Azure çözümüdür. Azure AD, Microsoft 'tan çok kiracılı, bulut tabanlı bir dizin ve kimlik yönetimi hizmetidir. Temel Dizin Hizmetleri, uygulama erişim yönetimi ve kimlik korumasını tek bir çözümde birleştirir.

Aşağıdaki bölümlerde Azure AD kullanarak kimlik ve erişim güvenliği için en iyi uygulamalar listelenmektedir.

**En iyi yöntem**: güvenlik denetimlerini ve Kullanıcı ve hizmet kimliklerindeki algılamaları ortalayın.
**Ayrıntı**: denetimleri ve kimlikleri birlikte bulmak IÇIN Azure ad kullanın.

## <a name="centralize-identity-management"></a>Kimlik yönetimini merkezileştirme

[Karma kimlik](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) senaryosunda, şirket içi ve bulut dizinlerinizi tümleştirmenizi öneririz. Tümleştirme, BT ekibinizin bir hesabın oluşturulduğu yere bakılmaksızın hesapları tek bir konumdan yönetmesine olanak sağlar. Tümleştirme Ayrıca, hem bulutta hem de şirket içi kaynaklara erişmek için ortak bir kimlik sağlayarak kullanıcılarınızın daha üretken olmasını sağlar.

**En iyi yöntem**: tek BIR Azure AD örneği oluşturun. Tutarlılık ve tek bir yetkili Kaynak, netliği artırır ve insan hatalarından ve yapılandırma karmasından güvenlik risklerini azaltır.
**Ayrıntı**: tek BIR Azure AD dizinini kurumsal ve kurumsal hesaplar için yetkili kaynak olarak belirleyin.

**En iyi yöntem**: şirket içi DIZINLERINIZI Azure AD ile tümleştirin.  
**Ayrıntı**: şirket içi dizininizi bulut dizininizle eşleştirmek için [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect) kullanın.

> [!Note]
> [Azure AD Connect performansını etkileyen faktörler](../../active-directory/hybrid/plan-connect-performance-factors.md)vardır. Azure AD Connect düşük performanslı sistemleri güvenlik ve üretkenliğe karşı korumak için yeterli kapasiteye sahip olduğundan emin olun. Büyük veya karmaşık kuruluşlar (100.000 'den fazla nesne sağlama), Azure AD Connect uygulamasını iyileştirmek için [önerileri](../../active-directory/hybrid/whatis-hybrid-identity.md) izlemelidir.

**En iyi yöntem**: hesapları mevcut Active Directory örneğiniz üzerinde yüksek ayrıcalıklara sahıp Azure AD ile eşitlememe.
**Ayrıntı**: bu hesapları filtreleyerek varsayılan [Azure AD Connect yapılandırmayı](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) değiştirmeyin. Bu yapılandırma, buluttan şirket içi varlıklara kadar olan (büyük bir olay oluşturabilecek) bir saldırgan olan duyuru riskini azaltır.

**En iyi yöntem**: Parola karması eşitlemesini açın.  
**Ayrıntı**: Parola karması eşitleme, Kullanıcı parolası karmalarını şirket içi Active Directory örneğinden bulut tabanlı BIR Azure AD örneğine eşitlemek için kullanılan bir özelliktir. Bu eşitleme, sızan kimlik bilgilerinin önceki saldırılardan yeniden çalınmasına karşı korunmaya yardımcı olur.

Active Directory Federasyon Hizmetleri (AD FS) (AD FS) veya diğer kimlik sağlayıcılarıyla Federasyonu kullanmaya karar verseniz bile, şirket içi sunucularınızın başarısız olması veya geçici olarak kullanılamaması olasılığına karşı, isteğe bağlı olarak parola karması eşitlemesini bir yedekleme olarak ayarlayabilirsiniz. Bu eşitleme, kullanıcıların şirket içi Active Directory örneğinde oturum açmak için kullandıkları aynı parolayı kullanarak hizmette oturum açmasını sağlar. Ayrıca, bir Kullanıcı Azure AD 'ye bağlı olmayan diğer hizmetlerde aynı e-posta adresini ve parolayı kullanmışsa, kimlik koruması 'nın güvenliği aşılmış olduğu bilinen parolalarla karşılaştırarak riskli kimlik bilgilerini algılamasını sağlar.

Daha fazla bilgi için bkz. [Azure AD Connect eşitleme ile parola karması eşitlemeyi uygulama](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).

**En iyi**uygulama: yeni uygulama geliştirme için kimlik doğrulaması IÇIN Azure ad kullanın.
**Ayrıntı**: kimlik doğrulamasını desteklemek için doğru özellikleri kullanın:

  - Çalışanlar için Azure AD
  - Konuk kullanıcılar ve dış iş ortakları için [Azure AD B2B](../../active-directory/b2b/index.yml)
  - Uygulamalarınızın, uygulamalarınızı kullandıklarında, oturum açma ve profillerini yönetme şeklini denetlemek için [Azure AD B2C](../../active-directory-b2c/index.yml)

Şirket içi kimliklerini bulut kimliğiyle tümleştirmeyin kuruluşlar, hesapların yönetiminde daha fazla yüke neden olabilir. Bu ek yük, hataların ve güvenlik ihlallerinin olasılığını artırır.

> [!Note]
> Kritik hesapların hangi dizinlerde yer alacağı ve kullanılan yönetici iş istasyonunun yeni bulut hizmetleri veya mevcut süreçler tarafından yönetilip yönetilmeyeceğini seçmeniz gerekir. Var olan yönetim ve kimlik sağlama işlemlerinin kullanılması bazı riskleri azaltabilir, ancak aynı zamanda bir saldırganın şirket içi bir hesabı tehlikeye atması ve buluta özetleme riskini de oluşturabilir. Farklı roller (örneğin, BT yöneticileri ile iş birimi yöneticileri) için farklı bir strateji kullanmak isteyebilirsiniz. İki seçeneğiniz vardır. İlk seçenek, şirket içi Active Directory örneğiniz ile eşitlenmemiş Azure AD hesapları oluşturmaktır. Yönetici iş istasyonunuza Azure AD 'ye katılarak Microsoft Intune kullanarak yönetebilir ve düzeltme eki uygulayabilirsiniz. İkinci seçenek, şirket içi Active Directory örneğiniz ile eşitleme yaparak var olan yönetici hesaplarını kullanmaktır. Active Directory etki alanında yönetim ve güvenlik için mevcut iş istasyonlarını kullanın.

## <a name="manage-connected-tenants"></a>Bağlı kiracılar yönetme
Güvenlik kuruluşunuz, riski değerlendirmek ve kuruluşunuzun ilkelerine ve herhangi bir mevzuat gereksinimlerine uyulmadığını belirlemek için görünürlüğe ihtiyaç duyuyor. Güvenlik kuruluşunuzun üretim ortamınıza ve ağa bağlı tüm aboneliklerde görünürlüğe sahip olduğundan emin olmanız gerekir ( [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) veya [siteden siteye VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)aracılığıyla). Azure AD 'deki [genel yönetici/Şirket Yöneticisi](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) , [Kullanıcı erişimi Yöneticisi](../../role-based-access-control/built-in-roles.md#user-access-administrator) rolüne erişimi yükseltebilir ve ortamınıza bağlı tüm abonelikleri ve yönetilen grupları görebilir.

, Ve güvenlik grubunuzun ortamınıza bağlı tüm abonelikleri veya Yönetim gruplarını görüntüleyebileceğini sağlamak için [tüm Azure aboneliklerini ve Yönetim gruplarını yönetmek üzere erişimi yükseltme](../../role-based-access-control/elevate-access-global-admin.md) bölümüne bakın. Riskleri değerlendirdikten sonra bu yükseltilmiş erişimi kaldırmalısınız.

## <a name="enable-single-sign-on"></a>Çoklu oturum açmayı etkinleştir

Mobil ve bulut öncelikli bir dünyada, kullanıcılarınızın her yerde ve her yerde üretken olmalarını sağlamak için her yerden cihazlara, uygulamalara ve hizmetlere çoklu oturum açma (SSO) özelliğini etkinleştirmek istiyorsunuz. Yönetmek için birden fazla kimlik çözümünden söz konusu olduğunda, bu, yalnızca bunun değil, birden çok parolayı hatırlamaları gereken kullanıcılar için de bir yönetim sorunu haline gelir.

Tüm uygulamalarınız ve kaynaklarınız için aynı kimlik çözümünü kullanarak SSO elde edebilirsiniz. Ayrıca kullanıcılarınız, kaynakların şirket içinde mi yoksa bulutta mı bulunduğu, oturum açmak ve ihtiyaç duydukları kaynaklara erişmek için aynı kimlik bilgileri kümesini kullanabilir.

**En iyi yöntem**: SSO 'yu etkinleştirin.  
**Ayrıntı**: Azure AD, [Şirket içi Active Directory buluta genişletir](/azure/active-directory/connect/active-directory-aadconnect) . Kullanıcılar, etki alanına katılmış cihazlar, Şirket kaynakları ve işlerini yapmak için ihtiyaç duydukları tüm Web ve SaaS uygulamaları için birincil iş veya okul hesaplarını kullanabilir. Kullanıcıların birden çok Kullanıcı adı ve parola kümesini hatırlamaları gerekmez ve bunların uygulama erişimi, kuruluş grubu üyeliklerine ve bir çalışan olarak durumlarına göre otomatik olarak sağlanabilir (veya temin edilebilir). Galeri uygulamaları veya geliştirip [Azure AD Application Proxy](/azure/active-directory/active-directory-application-proxy-get-started) ile yayımladığınız şirket içi uygulamalar için bu erişimi siz denetleyebilirsiniz.

Kullanıcıların Azure AD 'de iş veya okul hesaplarına göre [SaaS uygulamalarına](/azure/active-directory/active-directory-appssoaccess-whatis) erişmesini sağlamak için SSO 'yu kullanın. Bu, yalnızca Microsoft SaaS uygulamaları için değil, [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) ve [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial)gibi diğer uygulamalar için de geçerlidir. Uygulamanızı, Azure AD 'yi [SAML tabanlı bir kimlik](/azure/active-directory/fundamentals-identity) sağlayıcısı olarak kullanacak şekilde yapılandırabilirsiniz. Güvenlik denetimi olarak, Azure AD, kullanıcıların Azure AD ile erişim izni verilmediği sürece uygulamada oturum açmasına izin veren bir belirteç vermez. Doğrudan veya kullanıcıların üyesi olduğu bir grup aracılığıyla erişim izni verebilirsiniz.

Kullanıcılara ve uygulamalarına yönelik SSO oluşturmak için ortak bir kimlik oluşturmayan kuruluşlar, kullanıcıların birden çok parolası olan senaryolara açıktır. Bu senaryolar, kullanıcıların parolaları yeniden kullanma olasılığını veya zayıf parolalar kullanmasını arttırır.

## <a name="turn-on-conditional-access"></a>Koşullu erişimi aç

Kullanıcılar, her yerden çeşitli cihaz ve uygulamaları kullanarak kuruluşunuzun kaynaklarına erişebilir. BT Yöneticisi olarak, bu cihazların güvenlik ve uyumluluk için standartlarınızı karşıladığından emin olmak istersiniz. Yalnızca kaynağa erişebilecek olan kaynaklara odaklanmaya artık yeterli değildir.

Güvenlik ve verimliliği dengelemek için, Access Control ile ilgili bir karar vermeden önce bir kaynağa nasıl erişildiğini düşünmeniz gerekir. Azure AD koşullu erişim ile bu gereksinimi ele alabilirsiniz. Koşullu erişimle, bulut uygulamalarınıza erişim koşullarına bağlı olarak otomatik erişim denetimi kararları alabilirsiniz.

**En iyi yöntem**: şirket kaynaklarına erişimi yönetme ve denetleme.  
**Ayrıntı**: SaaS uygulamaları ve Azure AD ile bağlantılı uygulamalar için bir grup, konum ve uygulama duyarlılığı TEMELINDE Azure AD [koşullu erişimini](/azure/active-directory/active-directory-conditional-access-azure-portal) yapılandırın.

**En iyi yöntem**: eski kimlik doğrulama protokollerini engelleyin.
**Ayrıntı**: saldırganlar, özellikle parola spreyi saldırılarına karşı, her gün daha eski protokollerde zayıf noktalar yararlanabilir. Eski protokolleri engellemek için koşullu erişimi yapılandırın. Daha fazla bilgi için bkz. [Azure AD: Yapılacaklar ve yapılmayacak](https://www.youtube.com/watch?v=wGk0J4z90GI) .

## <a name="plan-for-routine-security-improvements"></a>Rutin güvenlik iyileştirmeleri için plan yapın

Güvenlik her zaman gelişiyor ve bulut ve kimlik yönetimi çatısını düzenli olarak göstermek ve ortamınızı güvenli hale getirmenin yeni yollarını öğrenmek için bir yol oluşturmanız önemlidir.

Kimlik güvenli puanı, Microsoft 'un, güvenlik duruşunuzu uygun bir şekilde ölçmenize ve gelecekteki güvenlik geliştirmeleri planına yardım eden bir sayısal puan sunmasının sağladığı önerilen güvenlik denetimlerinin bir kümesidir. Ayrıca puanınızı, diğer sektörlerdeki ve zaman içinde kendi eğilimlerini ile karşılaştırma olarak da görüntüleyebilirsiniz.

**En iyi yöntem**: sektördeki en iyi yöntemlere göre rutin güvenlik incelemelerini ve geliştirmelerini planlayın.
**Ayrıntı**: zaman içinde geliştirmelerinizi derecelendirmek Için kimlik güvenli puanı özelliğini kullanın.

## <a name="enable-password-management"></a>Parola yönetimini etkinleştirme

Birden çok kiracı varsa veya kullanıcıların [kendi parolalarını sıfırlamalarını](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)sağlamak istiyorsanız, uygunsuz kullanımı engellemek için uygun güvenlik ilkelerini kullanmanız önemlidir.

**En iyi yöntem**: kullanıcılarınız için self servis parola sıfırlama (SSPR) ayarlayın.  
**Ayrıntı**: Azure AD [self servis parola sıfırlama](/azure/active-directory-b2c/active-directory-b2c-reference-sspr) özelliğini kullanın.

**En iyi yöntem**: SSPR 'nin gerçekten mi kullanıldığını izleyin.  
**Ayrıntı**: Azure AD [parola sıfırlama kaydı Etkinlik raporunu](/azure/active-directory/active-directory-passwords-get-insights)kullanarak kaydeden kullanıcıları izleyin. Azure AD 'nin sağladığı raporlama özelliği, önceden oluşturulmuş raporları kullanarak soruları yanıtlamanıza yardımcı olur. Uygun şekilde lisanslandıysanız özel sorgular da oluşturabilirsiniz.

**En iyi yöntem**: bulut tabanlı parola ilkelerini şirket içi altyapınıza genişletin.
**Ayrıntı**: bulut tabanlı parola değişiklikleri için yaptığınız gibi şirket içi parola değişikliklerine yönelik aynı denetimleri gerçekleştirerek kuruluşunuzdaki parola ilkelerini geliştirin. Yasaklanmış parola listelerini mevcut altyapınıza genişletmek için şirket içinde Windows Server Active Directory aracıları için [Azure AD parola korumasını](/azure/active-directory/authentication/concept-password-ban-bad) yükler. Şirket içinde parolaları değiştiren, ayarlamış veya sıfırlayan kullanıcılar ve Yöneticiler, yalnızca bulutta bulunan kullanıcılarla aynı parola ilkesiyle uyum sağlamak için gereklidir.

## <a name="enforce-multi-factor-verification-for-users"></a>Kullanıcılar için Multi-Factor doğrulamasını zorunlu kıl

Tüm kullanıcılarınız için iki aşamalı doğrulama gerektirmenizi öneririz. Bu, kuruluşunuzda daha fazla etkisi olan yöneticiler ve diğer kullanıcıları (örneğin, finans Officers) içerir.

İki adımlı doğrulama gerektirmek için birden çok seçenek vardır. Sizin için en iyi seçenek, hedeflerinize, çalıştırmakta olduğunuz Azure AD sürümüne ve lisanslama programınıza bağlıdır. Bir kullanıcının sizin için en iyi seçeneği belirlemesi için [iki adımlı doğrulama gerektirme](/azure/active-directory/authentication/howto-mfa-userstates) bölümüne bakın. Lisanslar ve fiyatlandırma hakkında daha fazla bilgi için [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) ve [Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) fiyatlandırma sayfalarına bakın.

İki aşamalı doğrulamayı etkinleştirmenin seçenekleri ve avantajları aşağıda verilmiştir:

**Seçenek 1**: Azure AD **güvenlik varsayılanlarını kullanarak**tüm kullanıcılar ve oturum açma yöntemleri için MFA 'yı etkinleştirme: Bu seçenek, ORTAMıNıZDAKI tüm kullanıcılar için MFA 'yı kolayca ve hızlı bir şekilde zorlamanıza olanak sağlar:

* Karşılıklı yönetim hesapları ve yönetici oturum açma mekanizmaları
* Tüm kullanıcılar için Microsoft Authenticator MFA sınaması gerektir
* Eski kimlik doğrulama protokollerini kısıtla.

Bu yöntem tüm lisanslama katmanlarında kullanılabilir ancak mevcut koşullu erişim ilkeleriyle karıştırılamaz. Azure AD güvenlik Varsayılanları ' nda daha fazla bilgi edinebilirsiniz

**2. seçenek**: [kullanıcı durumunu değiştirerek Multi-Factor Authentication etkinleştirin](../../active-directory/authentication/howto-mfa-userstates.md).   
**Avantaj**: Bu, iki adımlı doğrulama gerektirmek için geleneksel bir yöntemdir. Hem bulutta hem de [azure Multi-Factor Authentication sunucusu birlikte çalışarak azure Multi-Factor Authentication](/azure/active-directory/authentication/concept-mfa-whichversion). Bu yöntemin kullanılması, kullanıcıların her oturum açtıklarında iki aşamalı doğrulama gerçekleştirmesini gerektirir ve koşullu erişim ilkelerini geçersiz kılar.

Multi-Factor Authentication etkinleştirilmesi gerektiğini öğrenmek için, [Kuruluşum Için hangi Azure MFA sürümünün doğru olduğunu](/azure/active-directory/authentication/concept-mfa-whichversion)görün.

**Seçenek 3**: [koşullu erişim ilkesiyle Multi-Factor Authentication etkinleştirin](/azure/active-directory/authentication/howto-mfa-getstarted).
**Avantaj**: Bu seçenek, [koşullu erişim](/azure/active-directory/active-directory-conditional-access-azure-portal)kullanarak belirli koşullarda iki adımlı doğrulama isteyip istememe olanak tanır. Belirli koşullar, farklı konumlardan, güvenilmeyen cihazlardan veya riskli olarak düşündüğünüz uygulamalardan Kullanıcı oturumu açabilir. İki adımlı doğrulamanın gerekli olduğu belirli koşulları tanımlama, kullanıcılarınız için sabit istem yapmaktan kaçınmanızı sağlar; Bu, bir saldırgan Kullanıcı deneyimi olabilir.

Bu, kullanıcılarınız için iki aşamalı doğrulamayı etkinleştirmenin en esnek yoludur. Koşullu erişim ilkesinin etkinleştirilmesi yalnızca Bulutta Azure Multi-Factor Authentication için geçerlidir ve Azure AD 'nin Premium bir özelliğidir. Bu yöntemle [bulut tabanlı Azure Multi-Factor Authentication dağıtma](/azure/active-directory/authentication/howto-mfa-getstarted)bölümünde daha fazla bilgi edinebilirsiniz.

**4. seçenek**: [Azure AD kimlik koruması](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa)Kullanıcı ve oturum açma riskini değerlendirerek koşullu erişim ilkeleriyle Multi-Factor Authentication etkinleştirin.   
**Avantaj**: Bu seçenek şunları yapmanızı sağlar:

* Kuruluşunuzun kimliklerini etkileyen olası güvenlik açıklarını tespit edin.
* Otomatik yanıtları, kuruluşunuzun kimlikleri ile ilgili şüpheli, algılanan şüpheli eylemler için yapılandırın.
* Şüpheli olayları araştırın ve bunları çözmek için gerekli işlemleri yapın.

Bu yöntem, tüm bulut uygulamaları için Kullanıcı ve oturum açma riskini temel alarak iki adımlı doğrulamanın gerekip gerekmediğini belirleme Azure AD Kimlik Koruması risk değerlendirmesini kullanır. Bu yöntem Azure Active Directory P2 lisanslama gerektirir. Bu yöntem hakkında daha fazla bilgi için [Azure Active Directory kimlik koruması](/azure/active-directory/identity-protection/overview)bulabilirsiniz.

> [!Note]
> Seçenek 1, Kullanıcı durumunu değiştirerek Multi-Factor Authentication etkinleştirme, koşullu erişim ilkelerini geçersiz kılar. Seçenekler 2 ve 3 koşullu erişim ilkeleri kullandığından, 1 seçeneğini bunlarla birlikte kullanamazsınız.

İki adımlı doğrulama gibi ek bir kimlik koruması katmanı eklememe kurumları, kimlik bilgisi hırsızlığı saldırısında daha açıktır. Kimlik bilgisi hırsızlığı saldırısı, verilerin güvenliğinin aşılmasına yol açabilir.

## <a name="use-role-based-access-control"></a>Rol tabanlı erişim denetimi kullanma

Bulut kaynakları için erişim yönetimi, bulutu kullanan her kuruluş için kritik öneme sahiptir. [Rol tabanlı erişim denetimi (RBAC)](/azure/role-based-access-control/overview), Azure kaynaklarına kimlerin erişebileceğini, bu kaynaklarla neler yapabileceğini ve hangi alanların erişebileceğini yönetmenize yardımcı olur.

Azure 'daki belirli işlevlerden sorumlu grupları veya ayrı rolleri atamak, güvenlik riskleri oluşturan insan ve otomasyon hatalarına yol açabilecek karışıklıklara karşı yardımcı olur. Erişimi, veri erişimi için güvenlik ilkeleri zorlamak isteyen kuruluşlar için [bilmelidir](https://en.wikipedia.org/wiki/Need_to_know) ve [en az ayrıcalık](https://en.wikipedia.org/wiki/Principle_of_least_privilege) güvenlik ilkelerine göre kısıtlamak zorunludur.

Güvenlik takımınız, riski değerlendirmek ve düzeltmek için Azure kaynaklarınızın görünürlüğünü gerektirir. Güvenlik ekibinin işlem sorumlulukları varsa, işlerini yapmak için ek izinlere sahip olmaları gerekir.

Belirli bir kapsamdaki kullanıcılara, gruplara ve uygulamalara izinler atamak için [RBAC](/azure/role-based-access-control/overview) kullanabilirsiniz. Rol atamasının kapsamı abonelik, kaynak grubu veya tek bir kaynak olabilir.

**En iyi yöntem**: takımınızın içindeki görevleri ayırabilirsiniz ve yalnızca işlerini gerçekleştirmesi için gereken kullanıcılara erişim miktarını verin. Azure aboneliğinizde veya kaynaklarında herkes için sınırsız izin vermek yerine, belirli bir kapsamdaki yalnızca belirli eylemlere izin verin.
**Ayrıntı**: kullanıcılara ayrıcalık atamak için Azure 'DAKI [yerleşik RBAC rollerini](/azure/role-based-access-control/built-in-roles) kullanın.

> [!Note]
> Belirli izinler gereksiz karmaşıklık ve karışıklık oluşturarak, bir şeyi bozmadan düzeltilmesi zor olan "eski" bir yapılandırmada biriktirilir. Kaynağa özgü izinlerden kaçının. Bunun yerine, abonelikler içindeki izinler için kurumsal çapta izinler ve kaynak grupları için yönetim grupları kullanın. Kullanıcıya özgü izinlerden kaçının. Bunun yerine, Azure AD 'de gruplara erişim atayın.

**En iyi yöntem**: Azure sorumluluklarına sahip güvenlik ekiplerine, riski değerlendirebilmesi ve düzeltebilmeleri için Azure ile ilgili bir erişim verin.
**Ayrıntı**: güvenlik EKIPLERINI RBAC [güvenlik okuyucusu](/azure/role-based-access-control/built-in-roles#security-reader) rolüne verin. Sorumluluk kapsamına bağlı olarak kök yönetim grubunu veya segment yönetimi grubunu kullanabilirsiniz:

* Tüm kurumsal kaynaklardan sorumlu takımlar için **kök yönetim grubu**
* Sınırlı kapsama sahip takımlar için **segment yönetimi grubu** (genellikle mevzuata veya diğer kuruluş sınırları nedeniyle)

**En iyi yöntem**: doğrudan işletimsel sorumluluklarına sahip güvenlik ekiplerine uygun izinleri verin.
**Ayrıntı**: uygun rol ATAMASı için RBAC yerleşik rollerini gözden geçirin. Yerleşik roller kuruluşunuzun belirli ihtiyaçlarını karşılamıyorsa, [Azure kaynakları için özel roller](/azure/role-based-access-control/custom-roles)oluşturabilirsiniz. Yerleşik rollerde olduğu gibi, abonelik, kaynak grubu ve kaynak kapsamları 'nda kullanıcılara, gruplara ve hizmet sorumlularına özel roller atayabilirsiniz.

**En iyi uygulamalar**: Azure Güvenlik Merkezi 'ne ihtiyacı olan güvenlik rollerine erişim izni verin. Güvenlik Merkezi, güvenlik ekiplerinin riskleri hızla belirlemesine ve düzeltmesine olanak tanır.
**Ayrıntı**: bu gereksinimlere sahip güvenlik EKIPLERINI RBAC [Güvenlik Yöneticisi](/azure/role-based-access-control/built-in-roles#security-admin) rolüne ekleyerek güvenlik ilkelerini görüntüleyebilir, güvenlik durumlarını görüntüleyebilir, güvenlik ilkelerini düzenleyebilir, uyarıları ve önerileri görüntüleyebilir, uyarıları ve önerileri kapatabilir. Bu bunu, sorumlulukların kapsamına bağlı olarak kök yönetim grubunu veya segment yönetimi grubunu kullanarak yapabilirsiniz.

RBAC gibi özellikleri kullanarak veri erişimi denetimi zorlamadığı kuruluşlar, kullanıcılarına gerekenden daha fazla ayrıcalık verebilir. Bu, kullanıcıların sahip olmadıkları veri türlerine (örneğin, yüksek iş etkisi) erişmesine izin vererek veri güvenliğinin aşılmasına yol açabilir.

## <a name="lower-exposure-of-privileged-accounts"></a>Ayrıcalıklı hesapların daha düşük pozlaması

Ayrıcalıklı erişimin güvenliğini sağlamak, iş varlıklarını korumak için önemli bir ilk adımdır. Güvenli bilgi veya kaynaklara erişimi olan kişilerin sayısını en aza indirmek, kötü niyetli bir kullanıcının erişim sağlama olasılığını azaltır veya bir yetkili kullanıcı yanlışlıkla hassas bir kaynağı etkilemektedir.

Ayrıcalıklı hesaplar, BT sistemlerini yöneten ve yöneten hesaplardır. Cyber saldırganlar, bir kuruluşun verilerine ve sistemlerine erişim kazanmak için bu hesapları hedefleyin. Ayrıcalıklı erişimin güvenliğini sağlamak için, hesabı ve sistemleri kötü niyetli bir kullanıcıya açık olma riskini yalıtmanız gerekir.

Siber saldırganlarına karşı ayrıcalıklı erişimin güvenliğini sağlamak için bir yol haritası geliştirmenizi ve izlemenizi öneririz. Azure AD, Microsoft Azure, Office 365 ve diğer bulut hizmetlerinde yönetilen veya bildirilen kimliklerin ve erişimin güvenliğini sağlamak için ayrıntılı bir yol haritası oluşturma hakkında bilgi için [Azure AD 'de karma ve bulut dağıtımları için ayrıcalıklı erişim güvenliğini gözden geçirin ](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

Aşağıda, [Azure AD 'de karma ve bulut dağıtımları için ayrıcalıklı erişimin güvenliğini sağlama](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)konusunda bulunan en iyi yöntemler özetlenmektedir:

**En iyi yöntem**: ayrıcalıklı hesaplara erişimi yönetin, denetleyin ve izleyin.   
**Ayrıntı**: [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)açın. Privileged Identity Management açtıktan sonra, ayrıcalıklı erişim rolü değişiklikleri için bildirim e-posta iletilerini alacaksınız. Bu bildirimler, dizininizde son derece ayrıcalıklı rollere ek kullanıcılar eklendiğinde erken uyarı sağlar.

**En iyi yöntem**: tüm kritik yönetici HESAPLARıNıN Azure AD hesapları olarak yönetildiğinden emin olun.
**Ayrıntı**: herhangi bir tüketici hesabını kritik yönetici rolünden kaldırın (örneğin, hotmail.com, live.com ve Outlook.com gibi Microsoft hesapları).

**En iyi yöntem**: yönetim ayrıcalıklarına engel olmak için tüm kritik Yönetici rollerinin yönetim görevleri için ayrı bir hesaba sahip olduğundan emin olun.
**Ayrıntı**: yönetim görevlerini gerçekleştirmek için gereken ayrıcalıklara atanmış ayrı bir yönetici hesabı oluşturun. Microsoft Office 365 e-postası veya rastgele Web Gözatma gibi günlük üretkenlik araçları için bu yönetim hesaplarının kullanımını engelleyin.

**En iyi yöntem**: yüksek ayrıcalıklı rollerdeki hesapları belirleme ve kategorilere ayırma.   
**Ayrıntı**: Azure AD Privileged Identity Management açtıktan sonra, genel yönetici, ayrıcalıklı rol yöneticisi ve diğer yüksek ayrıcalıklı rollerdeki kullanıcıları görüntüleyin. Artık bu rollerde gerekli olmayan hesapları kaldırın ve yönetici rollerine atanan kalan hesapları kategorilere ayırın:

* Yönetici kullanıcılara ayrı ayrı atanır ve yönetici olmayan amaçlar için kullanılabilir (örneğin, kişisel e-posta)
* Tek tek yönetici kullanıcılara atanır ve yalnızca yönetim amacıyla belirlenir
* Birden çok kullanıcı arasında paylaşılıyor
* Acil durum erişim senaryoları için
* Otomatikleştirilmiş betikler için
* Dış kullanıcılar için

**En iyi yöntem**: ayrıcalıkların etkilenme süresini daha da azaltmak ve ayrıcalıklı hesapların kullanımı hakkında görünürlüğünüzü artırmak için "zamanında anında" (JIT) erişimi uygulayın.   
**Ayrıntı**: Azure AD Privileged Identity Management şunları yapmanızı sağlar:

* Kullanıcıları yalnızca kendi ayrıcalıklarını JıT ' den ayırarak sınırlayın.
* Ayrıcalıkların otomatik olarak iptal edildiğinden emin olmak için, kısa süreli bir süre için roller atayın.

**En iyi yöntem**: en az iki acil durum erişim hesabı tanımlayın.   
**Ayrıntı**: acil erişim hesapları, kuruluşların mevcut bir Azure Active Directory ortamında ayrıcalıklı erişimi kısıtlamalarına yardımcı olur. Bu hesaplar son derece ayrıcalıklı ve belirli kişilere atanmamıştır. Acil durum erişim hesapları, normal yönetim hesaplarının kullanılabileceği senaryolarla sınırlıdır. Kuruluşların acil durum hesabının kullanımını yalnızca gerekli süre ile sınırlandırmalıdır.

Genel yönetici rolü için atanan veya uygun olan hesapları değerlendirin. `*.onmicrosoft.com` etki alanını (acil erişime yönelik olarak) kullanarak yalnızca herhangi bir bulut hesabı görmüyorsanız, bunları oluşturun. Daha fazla bilgi için bkz. [Azure AD 'de acil durum erişimi yönetim hesaplarını yönetme](/azure/active-directory/users-groups-roles/directory-emergency-access).

**En iyi yöntem**: acil durumda bir "cam" işlemine sahiptir.
**Ayrıntı**: [Azure AD 'de karma ve bulut dağıtımları Için ayrıcalıklı erişimin güvenliğini sağlama konusundaki](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)adımları izleyin.

**En iyi yöntem**: tüm kritik yönetici hesaplarının parola-daha az (tercih edilen) olmasını veya Multi-Factor Authentication gerektirmesini gerektirir.
**Ayrıntı**: herhangi BIR Azure AD hesabında parola kullanmadan oturum açmak için [Microsoft Authenticator uygulamasını](/azure/active-directory/authentication/howto-authentication-phone-sign-in) kullanın. [İş Için Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)gibi Microsoft Authenticator, bir cihaza bağlı bir kullanıcı kimlik bilgisini etkinleştirmek ve biyometrik kimlik doğrulaması veya PIN 'i kullanması için anahtar tabanlı kimlik doğrulaması kullanır.

Azure AD yönetici rollerine bir veya daha fazla kalıcı olarak atanan her bireysel kullanıcı için oturum açma sırasında Azure Multi-Factor Authentication gerektir: genel yönetici, ayrıcalıklı rol yöneticisi, Exchange Online Yöneticisi ve SharePoint Online Danışın. [Yönetici hesaplarınız için Multi-Factor Authentication](/azure/active-directory/authentication/howto-mfa-userstates) etkinleştirin ve yönetici hesabı kullanıcılarının kayıtlı olduğundan emin olun.

**En iyi yöntem**: kritik yönetici hesapları için, üretim görevlerine izin verilmeyen bir yönetim iş istasyonuna (örneğin, göz atma ve e-posta) sahiptir. Bu, yönetici hesaplarınızı göz atma ve e-posta kullanan saldırı vektörlerinden korur ve önemli bir olay riskini önemli ölçüde azaltır.
**Ayrıntı**: yönetici iş istasyonu kullanın. Bir iş istasyonu güvenliği düzeyi seçin:

- Son derece güvenli üretkenlik cihazları, gözatma ve diğer üretkenlik görevlerine yönelik gelişmiş güvenlik sağlar.
- [Ayrıcalıklı erişim Iş istasyonları (Paw 'lar)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) , hassas görevler için internet saldırılarına ve tehdit vektörlerinden korunan adanmış bir işletim sistemi sağlar.

**En iyi yöntem**: Çalışanlar kuruluştan ayrıldığında yönetici hesaplarının sağlamasını kaldırma.
**Ayrıntı**: Çalışanlar kuruluştan ayrıldığında yönetici hesaplarını devre dışı bırakan veya silen bir işlem yapın.

**En iyi yöntem**: geçerli saldırı tekniklerini kullanarak yönetim hesaplarını düzenli olarak test edin.
**Ayrıntı**: kuruluşunuzda gerçekçi saldırı senaryoları çalıştırmak için Office 365 saldırı simülatörü veya bir üçüncü taraf teklifi kullanın. Bu, gerçek bir saldırı gerçekleşmeden önce savunmasız kullanıcıları bulmanıza yardımcı olabilir.

**En iyi yöntem**: en sık kullanılan saldırıya uğrayan teknikleri azaltmaya yönelik adımları uygulayın.  
**Ayrıntı**: [Yönetim rollerinde iş veya okul hesaplarına geçiş yapması gereken Microsoft hesaplarını belirler](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Genel yönetici hesapları için ayrı kullanıcı hesapları ve posta iletimi sağlayın](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Yönetim hesaplarının parolalarının yakın zamanda değiştiğinden emin olun](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Parola karması eşitlemesini aç](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Tüm ayrıcalıklı rollerdeki kullanıcıların yanı sıra sunulan kullanıcılar için Multi-Factor Authentication gerektir](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Office 365 güvenli puanınızı edinin (Office 365 kullanıyorsanız)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Office 365 güvenlik ve Uyumluluk kılavuzunu gözden geçirin (Office 365 kullanıyorsanız)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Office 365 etkinlik Izlemeyi yapılandırma (Office 365 kullanıyorsanız)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[Olay/acil durum yanıt planı sahipleri oluşturma](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[Şirket içi ayrıcalıklı yönetim hesaplarının güvenliğini sağlama](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

Ayrıcalıklı erişimi güvenli hale getiremezseniz, yüksek ayrıcalıklı rollerde çok fazla kullanıcınız olduğunu ve saldırılara karşı daha savunmasız olduğunu fark edebilirsiniz. Siber saldırganlar de dahil olmak üzere kötü amaçlı aktörler, genellikle kimlik bilgileri hırsızlığı kullanarak hassas verilere ve sistemlere erişim kazanmak için yönetici hesaplarını ve ayrıcalıklı erişim 'in diğer öğelerini hedefleyin.

## <a name="control-locations-where-resources-are-created"></a>Kaynakların oluşturulduğu denetim konumları

Bulut operatörlerinin, kuruluşunuzun kaynaklarını yönetmek için gerekli olan kuralları bozmasını engellerken görevleri gerçekleştirmesini sağlama çok önemlidir. Kaynakların oluşturulduğu konumları denetlemek isteyen kuruluşlar bu konumları sabit olarak kodlemelidir.

Tanımları, özel olarak reddedilen eylemleri veya kaynakları tanımlayan güvenlik ilkeleri oluşturmak için [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) kullanabilirsiniz. Bu ilke tanımlarını, istenen kapsamda (abonelik, kaynak grubu veya tek bir kaynak gibi) atarsınız.

> [!NOTE]
> Güvenlik ilkeleri RBAC ile aynı değildir. Aslında bu kaynakları oluşturmak için Kullanıcıları yetkilendirmek üzere RBAC kullanırlar.
>
>

Kaynakların nasıl oluşturulduğunu denetmayan kuruluşlar, ihtiyaç duyduklarında daha fazla kaynak oluşturarak hizmeti kötüye kullanan kullanıcılara daha açıktır. Kaynak oluşturma sürecini sağlamlaştırma, çok kiracılı bir senaryonun güvenliğini sağlamaya yönelik önemli bir adımdır.

## <a name="actively-monitor-for-suspicious-activities"></a>Şüpheli etkinlikler için etkin bir şekilde izleme

Etkin bir kimlik izleme sistemi, şüpheli davranışı hızla algılayabilir ve daha fazla araştırma için bir uyarı tetikleyebilir. Aşağıdaki tabloda, kuruluşların kimliklerini izlemelerine yardımcı olabilecek iki Azure AD özelliği listelenmektedir:

**En iyi yöntem**: şunları tanımlamak için bir yönteme sahiptir:

- [İzlenmeden](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources)oturum açmaya çalışır.
- Belirli bir hesaba karşı [deneme yanılma](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures) saldırıları.
- Birden çok konumdan oturum açmaya çalışır.
- [Virüslü cihazlardan](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices)oturum açma işlemleri.
- Şüpheli IP adresleri.

**Ayrıntı**: Azure AD Premium [anomali raporlarını](/azure/active-directory/active-directory-view-access-usage-reports)kullanın. BT yöneticilerinin bu raporları günlük olarak veya isteğe bağlı olarak (genellikle bir olay yanıtı senaryosunda) çalıştırmasına yönelik işlemler ve yordamlar vardır.

**En iyi yöntem**: riskleri bildiren ve risk düzeyini (yüksek, orta veya düşük) iş gereksinimlerinize göre ayarlayabildiğiniz etkin bir izleme sistemine sahiptir.   
**Ayrıntı**: kendi panosundaki geçerli riskleri işaret eden [Azure AD kimlik koruması](/azure/active-directory/active-directory-identityprotection)kullanın ve e-posta aracılığıyla günlük Özet bildirimleri gönderir. Kuruluşunuzun kimliklerinin korunmasına yardımcı olmak için, belirtilen risk düzeyine ulaşıldığında algılanan sorunlara otomatik olarak yanıt veren risk tabanlı ilkeler yapılandırabilirsiniz.

Kimlik sistemlerini etkin bir şekilde izlemediği kuruluşlar, Kullanıcı kimlik bilgilerinin güvenliğinin aşılmasına neden olma risklidir. Bu kimlik bilgileriyle şüpheli etkinliklerin gerçekleşmesi konusunda bilgi sahibi olmayan kuruluşlar, bu tür tehdidi azaltamıyorum.

## <a name="use-azure-ad-for-storage-authentication"></a>Depolama kimlik doğrulaması için Azure AD kullanma
[Azure depolama](/azure/storage/common/storage-auth-aad) , BLOB depolama ve kuyruk depolama IÇIN Azure AD ile kimlik doğrulaması ve yetkilendirmeyi destekler. Azure AD kimlik doğrulamasıyla, kullanıcılara, gruplara ve uygulamalara tek bir blob kapsayıcısının veya kuyruğunun kapsamına belirli izinler vermek için Azure rol tabanlı erişim denetimini kullanabilirsiniz.

[Depolama erişimi için kimlik doğrulaması yapmak üzere Azure AD](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)kullanmanızı öneririz.

## <a name="next-step"></a>Sonraki adım

Azure 'u kullanarak bulut çözümlerinizi tasarlarken, dağıttığınızda ve yönetirken en iyi güvenlik uygulamaları için bkz. [Azure Güvenlik en iyi uygulamaları ve desenleri](best-practices-and-patterns.md) .
