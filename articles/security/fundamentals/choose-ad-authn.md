---
title: Azure AD karma kimlik çözümünüz için doğru kimlik doğrulama yöntemini seçin | Microsoft Docs
description: Bu kılavuz, orta ve büyük ölçekli kuruluşlarda Azure AD karma kimlik çözümü için bir kimlik doğrulama yöntemi seçmekten sorumlu CEOs, Cıos, Cıos, kimlik mimarları, kuruluş mimarları ve güvenlik ve BT karar mekanizmalarına yardımcı olur.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 71b4a8abc641a3ab11d6b17bbc8de3b42b61c34c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820551"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Azure Active Directory karma kimlik çözümünüz için doğru kimlik doğrulama yöntemini seçin

Doğru kimlik doğrulama yöntemini seçmek, kuruluşların uygulamalarını buluta taşıması için ilk konudur. Bu kararı aşağıdaki nedenlerden dolayı aydınlamayın:

1. Bu, buluta taşımak isteyen bir kuruluşa yönelik ilk karardır.

2. Kimlik doğrulama yöntemi, kuruluşun buluttaki varlığına ait kritik bir bileşendir. Tüm bulut verilerine ve kaynaklarına erişimi denetler.

3. Bu, Azure AD 'deki diğer tüm gelişmiş güvenlik ve Kullanıcı deneyimi özelliklerinin temelidir.

4. Kimlik doğrulama yöntemi uygulandıktan sonra değiştirilebilir.

Kimlik, BT güvenliğinin yeni denetim düzledir. Bu nedenle, kimlik doğrulaması, kuruluşun yeni bulut dünyasına erişim koruyucusu olur. Kuruluşların, güvenliğini güçlendirir ve bulut uygulamalarını yetkisiz kişilerden güvende tutan bir kimlik denetim düzlemi gerekir.

### <a name="out-of-scope"></a>Kapsam dışı
Mevcut bir şirket içi dizin ayak izine sahip olmayan kuruluşlar, bu makalenin konusu değildir. Genellikle, bu işletmeler yalnızca Bulutta kimlik oluşturur ve bu da karma kimlik çözümü gerektirmez. Yalnızca bulutta bulunan kimlikler yalnızca bulutta bulunur ve ilgili şirket içi kimliklerle ilişkili değildir.

## <a name="authentication-methods"></a>Kimlik doğrulama yöntemleri
Azure AD karma kimlik çözümü yeni denetim düzletim olduğunda, kimlik doğrulaması bulut erişiminin temelidir. Doğru kimlik doğrulama yönteminin seçilmesi, bir Azure AD karma kimlik çözümü ayarlamaya yönelik önemli bir ilk karardır. Bulut içindeki kullanıcıları da sağlayan Azure AD Connect kullanılarak yapılandırılan kimlik doğrulama yöntemini uygulayın.

Bir kimlik doğrulama yöntemi seçmek için, seçiminizi uygulayan süreyi, var olan altyapıyı, karmaşıklığı ve maliyeti göz önünde bulundurmanız gerekir. Bu faktörler her kuruluş için farklılık gösterebilir ve zaman içinde değişebilir.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD, karma kimlik çözümleri için aşağıdaki kimlik doğrulama yöntemlerini destekler.

### <a name="cloud-authentication"></a>Bulut kimlik doğrulaması
Bu kimlik doğrulama yöntemini seçtiğinizde, Azure AD kullanıcıların oturum açma işlemini işler. Sorunsuz çoklu oturum açma (SSO) ile bağlanmış kullanıcılar, kimlik bilgilerini yeniden girmeye gerek kalmadan bulut uygulamalarında oturum açabilirler. Bulut kimlik doğrulaması ile iki seçenekten birini seçebilirsiniz:

**Azure AD Parola karması eşitleme**. Azure AD 'de şirket içi dizin nesneleri için kimlik doğrulamasını etkinleştirmenin en kolay yolu. Kullanıcılar, ek altyapı dağıtmak zorunda kalmadan şirket içinde kullandıkları aynı kullanıcı adını ve parolayı kullanabilir. Azure AD 'nin kimlik koruması ve [Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)gibi bazı Premium özellikleri, seçtiğiniz kimlik doğrulama yöntemi ne olduğuna bakılmaksızın Parola karması eşitlemesi gerektirir.

> [!NOTE]
> Parolalar hiçbir şekilde şifresiz metin halinde depolanmaz veya Azure AD 'de ters çevrilebilir algoritmayla şifrelenir. Parola karması eşitleme işleminin gerçek süreci hakkında daha fazla bilgi için bkz. [Azure AD Connect Sync ile parola karması eşitlemeyi uygulama](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Azure AD geçişli kimlik doğrulaması**. , Bir veya daha fazla şirket içi sunucuda çalışan bir yazılım Aracısı kullanarak Azure AD kimlik doğrulaması Hizmetleri için basit bir parola doğrulaması sağlar. Sunucular, kullanıcıları doğrudan şirket içi Active Directory doğrular ve bu da parola doğrulamasının bulutta gerçekleşmemesini sağlar.

Şirket içi kullanıcı hesap durumlarını, parola ilkelerini ve oturum açma saatlerini hemen zorlamak için güvenlik gereksinimi olan şirketlerin bu kimlik doğrulama yöntemini kullanması gerekebilir. Gerçek geçişli kimlik doğrulama işlemi hakkında daha fazla bilgi için bkz. [Azure AD geçişli kimlik doğrulaması Ile Kullanıcı oturum açma](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Federal kimlik doğrulaması
Bu kimlik doğrulama yöntemini seçtiğinizde, Azure AD, kullanıcının parolasını doğrulamak için şirket içi Active Directory Federasyon Hizmetleri (AD FS) (AD FS) gibi ayrı bir güvenilir kimlik doğrulama sistemine kimlik doğrulama işlemini devre dışı bırakır.

Kimlik doğrulama sistemi, ek Gelişmiş kimlik doğrulama gereksinimleri sağlayabilir. Örnek olarak, akıllı kart tabanlı kimlik doğrulaması veya üçüncü taraf çok faktörlü kimlik doğrulamalardır. Daha fazla bilgi için bkz. [dağıtma Active Directory Federasyon Hizmetleri (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Aşağıdaki bölüm, karar ağacı kullanarak sizin için hangi kimlik doğrulama yönteminin doğru olduğuna karar vermenize yardımcı olur. Azure AD hibrit kimlik çözümünüz için bulut veya federal kimlik doğrulama dağıtımı yapılıp yapılmayacağını belirlemenize yardımcı olur.

## <a name="decision-tree"></a>Karar ağacı

![Azure AD kimlik doğrulaması karar ağacı](./media/choose-ad-authn/azure-ad-authn-image1.png)

Karar sorularına ilişkin ayrıntılar:

1. Azure AD, parolaları doğrulamak için şirket içi bileşenlere bağlı olmayan kullanıcılar için oturum açma işleyebilir.
2. Azure AD, Microsoft 'un AD FS gibi güvenilir bir kimlik doğrulama sağlayıcısında Kullanıcı oturumunu kapatabilir.
3. Uygulamanız gerekiyorsa, hesap süresi doldu, devre dışı hesap, parola süresi doldu, hesap kilitli ve her kullanıcı oturum açma için oturum açma saatleri gibi Kullanıcı düzeyinde Active Directory güvenlik ilkeleri, Azure AD 'nin bazı şirket içi bileşenleri olması gerekir.
4. Azure AD tarafından yerel olarak desteklenmeyen oturum açma özellikleri:
   * Akıllı kartlar veya sertifikalar kullanarak oturum açın.
   * Şirket içi MFA sunucusu kullanarak oturum açın.
   * Üçüncü taraf kimlik doğrulama çözümünü kullanarak oturum açın.
   * Çok siteli şirket içi kimlik doğrulama çözümü.
5. Azure AD Kimlik Koruması, *kullanıcılara sızdırılan kimlik bilgileri* raporunu sağlamak için hangi oturum açma yönteminden bağımsız olarak parola karması eşitlemesi gerektirir. Birincil oturum açma yöntemi başarısız olursa ve hata olayından önce yapılandırıldıysa, kuruluşlar parola karması eşitlemesine yük devredebilirler.

> [!NOTE]
> Azure AD Kimlik Koruması [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) lisansları gerekir.

## <a name="detailed-considerations"></a>Ayrıntılı konular

### <a name="cloud-authentication-password-hash-synchronization"></a>Bulut kimlik doğrulaması: Parola karması eşitleme

* **Çaba**. Parola karması eşitleme, dağıtım, bakım ve altyapıyla ilgili en az çaba gerektirir.  Bu çaba düzeyi genellikle yalnızca kullanıcılarına Office 365, SaaS uygulamalarında ve diğer Azure AD tabanlı kaynaklarda oturum açmasını gerektiren kuruluşlar için geçerlidir. Oturum açıldığında, Parola karması eşitleme Azure AD Connect eşitleme işleminin bir parçasıdır ve iki dakikada bir çalışır.

* **Kullanıcı deneyimi**. Kullanıcıların oturum açma deneyimini geliştirmek için Parola karması eşitlemeyle sorunsuz SSO dağıtın. Kullanıcılar oturum açıldığında sorunsuz SSO gereksiz istemleri ortadan kaldırır.

* **Gelişmiş senaryolar**. Kuruluşlar tercih ederseniz, Azure AD Premium P2 ile Azure AD Kimlik Koruması raporları olan kimliklerden Öngörüler kullanmak mümkündür. Sızdırılan kimlik bilgileri raporu bir örnektir. Windows Hello for Business, [Parola karması eşitlemesini kullandığınızda belirli gereksinimlere](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification)sahiptir. [Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) , kullanıcılara yönetilen etki alanında kurumsal kimlik bilgilerini sağlamak için Parola karması eşitlemesi gerektirir.

    Parola karması eşitlemeyle çok faktörlü kimlik doğrulaması gerektiren kuruluşların Azure AD çok faktörlü kimlik doğrulaması veya [koşullu erişim özel denetimlerini](../../active-directory/conditional-access/controls.md#custom-controls-preview)kullanması gerekir. Bu kuruluşlar, Federasyonu kullanan üçüncü taraf veya şirket içi çok faktörlü kimlik doğrulama yöntemlerini kullanamaz.

> [!NOTE]
> Azure AD koşullu erişim [Azure AD Premium P1](https://azure.microsoft.com/pricing/details/active-directory/) lisansı gerektirir.

* **İş sürekliliği**. Bulut kimlik doğrulamasıyla Parola karması eşitlemesini kullanmak, tüm Microsoft veri merkezlerine ölçeklenebilen bir bulut hizmeti olarak yüksek oranda kullanılabilir. Parola karması eşitlemesinin genişletilmiş dönemler için geri gitmediğinden emin olmak için, bir bekleme yapılandırmasında ikinci bir Azure AD Connect sunucusunu hazırlama modunda dağıtın.

* **Dikkat edilecek noktalar**. Şu anda, Parola karması eşitlemesi şirket içi hesap durumlarındaki değişiklikleri hemen zorlamaz. Bu durumda, Kullanıcı hesabı durumu Azure AD 'ye eşitlenene kadar bir kullanıcının bulut uygulamalarına erişimi vardır. Kuruluşlar, şirket içi kullanıcı hesabı durumlarında toplu güncelleştirmeler yaptıktan sonra yeni bir eşitleme döngüsünü çalıştırarak bu kısıtlamayı aşmak isteyebilir. Hesapların devre dışı bırakılması örnek bir örnektir.

> [!NOTE]
> Parola zaman aşımına uğradı ve hesap kilitli olmayan durumlar şu anda Azure AD Connect ile Azure AD ile eşitlenmemiş. Bir kullanıcının parolasını değiştirdiğinizde ve *Kullanıcı bir sonraki oturum açma bayrağıyla parolayı değiştirmeli* seçeneğini belirlediğinizde, Parola karması, Kullanıcı parolasını değiştirene kadar Azure AD Connect Azure AD ile eşitlenmez.

Dağıtım adımları için [Parola karması eşitlemesini uygulama](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) bölümüne bakın.

### <a name="cloud-authentication-pass-through-authentication"></a>Bulut kimlik doğrulaması: geçişli kimlik doğrulaması  

* **Çaba**. Geçişli kimlik doğrulaması için, var olan sunucularda bir veya daha fazla basit aracı yüklü olmalıdır (üç). Bu aracıların şirket içi AD etki alanı denetleyicileriniz dahil olmak üzere şirket içi Active Directory Domain Services erişimi olmalıdır. Internet 'e giden erişim ve etki alanı denetleyicilerinize erişim gerekir. Bu nedenle, aracıların bir çevre ağında dağıtılması desteklenmez.

    Geçişli kimlik doğrulaması, etki alanı denetleyicilerine kısıtlı olmayan ağ erişimi gerektirir. Tüm ağ trafiği şifrelenir ve kimlik doğrulama istekleriyle sınırlıdır. Bu işlemle ilgili daha fazla bilgi için bkz. doğrudan kimlik doğrulama üzerinde [güvenlik derinlemesine](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) bakış.

* **Kullanıcı deneyimi**. Kullanıcıların oturum açma deneyimini geliştirmek için doğrudan kimlik doğrulamasıyla sorunsuz SSO dağıtın. Kullanıcı oturum açtıktan sonra sorunsuz SSO gereksiz istemleri ortadan kaldırır.

* **Gelişmiş senaryolar**. Doğrudan kimlik doğrulaması, oturum açma sırasında şirket içi hesap ilkesini uygular. Örneğin, şirket içi bir kullanıcının hesap durumu devre dışı bırakıldığında, kilitlendiğinde veya [parolanın süresi dolduğunda](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) veya kullanıcının oturum açmasına izin verilen saatlerin dışında kaldığında erişim reddedilir.

    Doğrudan kimlik doğrulaması ile çok faktörlü kimlik doğrulaması gerektiren kuruluşların Azure Multi-Factor Authentication (MFA) veya [koşullu erişim özel denetimlerini](../../active-directory/conditional-access/controls.md#custom-controls-preview)kullanması gerekir. Bu kuruluşlar, Federasyona dayanan bir üçüncü taraf veya şirket içi çok faktörlü kimlik doğrulama yöntemi kullanamaz. Gelişmiş özellikler, Parola karması eşitlemesinin doğrudan geçiş kimlik doğrulaması seçmenize bakılmaksızın dağıtılmasını gerektirir. Kimlik koruması kimlik bilgilerinin sızdırılan kimlik bilgileri rapor örneğidir.

* **İş sürekliliği**. İki ek geçişli kimlik doğrulama Aracısı dağıtmanızı öneririz. Bu ek özellikler Azure AD Connect sunucusundaki ilk aracıya ek niteliğindedir. Bu ek dağıtım, kimlik doğrulama isteklerinin yüksek kullanılabilirliğini sağlar. Dağıtılan üç aracınız varsa, başka bir aracı bakım için kapalıysa bir aracı yine de başarısız olabilir.

    Doğrudan kimlik doğrulamasına ek olarak parola karması eşitlemesini dağıtmaya yönelik başka bir avantaj de vardır. Birincil kimlik doğrulama yöntemi artık kullanılabilir olmadığında bir yedekleme kimlik doğrulama yöntemi işlevi görür.

* **Dikkat edilecek noktalar**. Geçiş kimlik doğrulaması için yedekleme kimlik doğrulama yöntemi olarak parola karması eşitlemesini kullanabilirsiniz, çünkü aracılar, önemli bir şirket içi arıza nedeniyle kullanıcının kimlik bilgilerini doğrulayamaz. Parola karması eşitlemesine yük devretmek otomatik olarak gerçekleşmez ve oturum açma yöntemini el ile değiştirmek için Azure AD Connect kullanmanız gerekir.

    Diğer kimlik desteği de dahil olmak üzere geçişli kimlik doğrulaması hakkında diğer konular için bkz. [sık sorulan sorular](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Dağıtım adımları için [doğrudan kimlik doğrulamasını uygulama](../../active-directory/hybrid/how-to-connect-pta.md) bölümüne bakın.

### <a name="federated-authentication"></a>Federal kimlik doğrulaması

* **Çaba**. Federasyon kimlik doğrulama sistemi, kullanıcıların kimliğini doğrulamak için bir dış güvenilen sisteme bağımlıdır. Bazı şirketler mevcut Federasyon sistem yatırımlarını Azure AD karma kimlik çözümüyle birlikte yeniden kullanmak ister. Federasyon sisteminin bakımı ve yönetimi, Azure AD 'nin denetimini dışındadır. Bu, kuruluşun güvenli bir şekilde dağıtıldığından ve kimlik doğrulama yükünü işleyebildiği için federe sistem kullanılarak kuruluşa kadar olur.

* **Kullanıcı deneyimi**. Federal kimlik doğrulamasının Kullanıcı deneyimi, Federasyon grubunun özellikleri, topolojisi ve yapılandırması uygulamasına bağlıdır. Bazı kuruluşların, güvenlik gereksinimlerine uyacak şekilde Federasyon grubuna erişimi uyarlamak ve yapılandırmak için bu esnekliğe ihtiyacı vardır. Örneğin, dahili olarak bağlı kullanıcıları ve cihazları kullanıcıların kimlik bilgileri için sormadan otomatik olarak oturum açmasını yapılandırmak mümkündür. Bu yapılandırma cihazlarında zaten oturum açtıkları için işe yarar. Gerekirse, bazı gelişmiş güvenlik özellikleri kullanıcıların oturum açma işlemini daha zor hale getirir.

* **Gelişmiş senaryolar**. Müşteriler Azure AD 'nin yerel olarak desteklemediği bir kimlik doğrulama gereksinimine sahip olduğunda, genellikle bir Federasyon kimlik doğrulama çözümü gereklidir. [Sağ oturum açma seçeneğini seçmenize](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/)yardımcı olacak ayrıntılı bilgilere bakın. Aşağıdaki ortak gereksinimleri göz önünde bulundurun:

  * Akıllı kartlar veya sertifikalar gerektiren kimlik doğrulaması.
  * Şirket içi MFA sunucuları veya federal kimlik sağlayıcısı gerektiren üçüncü taraf çok faktörlü sağlayıcıları.
  * Üçüncü taraf kimlik doğrulama çözümlerini kullanarak kimlik doğrulaması. Bkz. [Azure AD Federasyon uyumluluğu listesi](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Örneğin, bir Kullanıcı asıl adı (UPN) yerine bir sAMAccountName (örneğin, etkialanı \ KullanıcıAdı) gerektiren oturum açma, örneğin, user@domain.com.

* **İş sürekliliği**. Federasyon sistemleri genellikle grup olarak bilinen, yük dengeli bir sunucu dizisi gerektirir. Bu grup, kimlik doğrulama istekleri için yüksek kullanılabilirlik sağlamak amacıyla bir iç ağ ve çevre ağ topolojisinde yapılandırılır.

    Birincil kimlik doğrulama yöntemi artık kullanılabilir olmadığında, bir yedekleme kimlik doğrulama yöntemi olarak, Federasyon kimlik doğrulaması ile birlikte Parola karması eşitlemesini dağıtın. Şirket içi sunucuların kullanılamadığı bir örnek vardır. Bazı büyük kurumsal kuruluşlar, düşük Gecikmeli kimlik doğrulama istekleri için coğrafi DNS ile yapılandırılmış birden çok Internet giriş noktasını desteklemek için bir Federasyon çözümü gerektirir.

* **Dikkat edilecek noktalar**. Federasyon sistemleri, genellikle şirket içi altyapıda daha önemli bir yatırım gerektirir. Çoğu kuruluş, zaten şirket içi bir Federasyon yatırımı varsa bu seçeneği seçer. Ve bu, tek kimlik sağlayıcısını kullanmak için güçlü bir iş gereksinimidir. Federasyon, bulut kimlik doğrulaması çözümleriyle karşılaştırıldığında çalıştırmak ve sorunlarını gidermek için daha karmaşıktır.

Azure AD 'de doğrulanamayan yönlendirilebilir olmayan bir etki alanı için Kullanıcı KIMLIĞI oturum açma 'yı uygulamak üzere ek yapılandırma gerekir. Bu gereksinim alternatif oturum açma KIMLIĞI desteği olarak bilinir. Sınırlamalar ve gereksinimler için [Alternatif oturum açma kimliğini yapılandırma](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) konusuna bakın. Federasyon ile bir üçüncü taraf Multi-Factor Authentication sağlayıcısı kullanmayı seçerseniz, sağlayıcının cihazların Azure AD 'ye katılmasına izin vermek için WS-Trust ' ı desteklediğinden emin olun.

Dağıtım adımları için [Federasyon Sunucularını Dağıtma](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) konusuna bakın.

> [!NOTE]
> Azure AD hibrit kimlik çözümünüzü dağıttığınızda desteklenen Azure AD Connect topolojilerden birini uygulamanız gerekir. [Azure AD Connect Için topolojilerde](../../active-directory/hybrid/plan-connect-topologies.md)desteklenen ve desteklenmeyen yapılandırmalar hakkında daha fazla bilgi edinin.

## <a name="architecture-diagrams"></a>Mimari diyagramları

Aşağıdaki diyagramlarda, Azure AD hibrit kimlik çözümünüz ile kullanabileceğiniz her bir kimlik doğrulama yöntemi için gereken üst düzey mimari bileşenleri ana hatlarıyla verilmiştir. Çözümler arasındaki farkları karşılaştırmanıza yardımcı olmak için bir genel bakış sağlar.

* Parola karması eşitleme çözümünün basitliği:

    ![Parola karması eşitleme ile Azure AD karma kimliği](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Artıklığı için iki aracı kullanarak doğrudan geçiş kimlik doğrulamasının Aracı gereksinimleri:

    ![Geçişli kimlik doğrulaması ile Azure AD karma kimliği](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Ortamınızdaki Federasyon ve kuruluşunuzun iç ağınızdaki Federasyon için gereken bileşenler:

    ![Federal kimlik doğrulaması ile Azure AD karma kimliği](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Yöntemleri karşılaştırma

|US|Parola karması eşitleme + sorunsuz SSO|Geçişli kimlik doğrulaması + sorunsuz SSO|AD FS ile Federasyon|
|:-----|:-----|:-----|:-----|
|Kimlik doğrulaması nerede gerçekleşir?|Bulutta|Şirket içi kimlik doğrulama Aracısı ile güvenli bir parola doğrulaması alışverişi sonrasında bulutta|Şirket içi|
|Sağlama sisteminin ötesinde şirket içi sunucu gereksinimleri nelerdir: Azure AD Connect?|None|Her ek kimlik doğrulama Aracısı için bir sunucu|İki veya daha fazla AD FS sunucusu<br><br>Çevre/DMZ ağında iki veya daha fazla WAP sunucusu|
|Sağlama sisteminin ötesinde şirket içi Internet ve ağ gereksinimleri nelerdir?|None|Kimlik doğrulama aracılarını çalıştıran sunuculardan [giden Internet erişimi](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)|Çevre 'te WAP sunucularına [gelen Internet erişimi](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements)<br><br>Çevre 'teki WAP sunucularından sunucuya AD FS sunuculara gelen ağ erişimi<br><br>Ağ yük dengeleme|
|Bir SSL sertifikası gereksinimi var mı?|Hayır|Hayır|Evet|
|Bir sistem durumu izleme çözümü var mı?|Gerekli değil|[Azure Active Directory Yönetim Merkezi](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md) tarafından sunulan Aracı durumu|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Kullanıcılar şirket ağı içindeki etki alanına katılmış cihazlardan bulut kaynaklarına çoklu oturum açma izni veriyor mu?|[Sorunsuz SSO](../../active-directory/hybrid/how-to-connect-sso.md) ile Evet|[Sorunsuz SSO](../../active-directory/hybrid/how-to-connect-sso.md) ile Evet|Evet|
|Hangi oturum açma türleri desteklenir?|UserPrincipalName + parola<br><br>[Sorunsuz SSO](../../active-directory/hybrid/how-to-connect-sso.md) kullanarak Windows Ile tümleşik kimlik doğrulaması<br><br>[Alternatif oturum açma KIMLIĞI](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + parola<br><br>[Sorunsuz SSO](../../active-directory/hybrid/how-to-connect-sso.md) kullanarak Windows Ile tümleşik kimlik doğrulaması<br><br>[Alternatif oturum açma KIMLIĞI](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + parola<br><br>sAMAccountName + Password<br><br>Windows-tümleşik kimlik doğrulaması<br><br>[Sertifika ve akıllı kart kimlik doğrulaması](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternatif oturum açma KIMLIĞI](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Iş için Windows Hello destekleniyor mu?|[Anahtar güven modeli](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Anahtar güven modeli](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Windows Server 2016 etki alanı işlev düzeyi gerektirir*|[Anahtar güven modeli](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Sertifika güven modeli](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Çok faktörlü kimlik doğrulama seçenekleri nelerdir?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Koşullu erişimle özel denetimler *](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Koşullu erişimle özel denetimler *](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Azure MFA sunucusu](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[Üçüncü taraf MFA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Koşullu erişimle özel denetimler *](../../active-directory/conditional-access/controls.md)|
|Hangi kullanıcı hesabı durumları destekleniyor?|Devre dışı hesaplar<br>(en fazla 30 dakikalık gecikme)|Devre dışı hesaplar<br><br>Hesap kilitlendi<br><br>Hesap zaman aşımına uğradı<br><br>Parolanın geçerliliği geçildi<br><br>Oturum açma saatleri|Devre dışı hesaplar<br><br>Hesap kilitlendi<br><br>Hesap zaman aşımına uğradı<br><br>Parolanın geçerliliği geçildi<br><br>Oturum açma saatleri|
|Koşullu erişim seçenekleri nelerdir?|[Azure AD Premium ile Azure AD koşullu erişim](../../active-directory/conditional-access/overview.md)|[Azure AD Premium ile Azure AD koşullu erişim](../../active-directory/conditional-access/overview.md)|[Azure AD Premium ile Azure AD koşullu erişim](../../active-directory/conditional-access/overview.md)<br><br>[AD FS talep kuralları](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Eski protokollerin engellenmesi destekleniyor mu?|[Evet](../../active-directory/conditional-access/conditions.md)|[Evet](../../active-directory/conditional-access/conditions.md)|[Evet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Oturum açma sayfalarında logoyu, resmi ve açıklamayı özelleştirebilir miyim?|[Evet, Azure AD Premium ile](../../active-directory/fundamentals/customize-branding.md)|[Evet, Azure AD Premium ile](../../active-directory/fundamentals/customize-branding.md)|[Evet](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Hangi gelişmiş senaryolar destekleniyor?|[Akıllı parola kilitleme](../../active-directory/authentication/concept-sspr-howitworks.md)<br><br>[Azure AD Premium P2 ile sızdırılan kimlik bilgileri raporları](../../active-directory/reports-monitoring/concept-risk-events.md)|[Akıllı parola kilitleme](../../active-directory/authentication/howto-password-smart-lockout.md)|Çok siteli düşük Gecikmeli kimlik doğrulama sistemi<br><br>[Extranet kilitlemeyi AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Üçüncü taraf kimlik sistemleriyle tümleştirme](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Azure AD koşullu erişim 'deki özel denetimler Şu anda cihaz kaydını desteklememektedir.

## <a name="recommendations"></a>Öneriler
Kimlik sisteminiz, kullanıcılarınızın bulut uygulamalarına ve bu iş kolu uygulamalarına erişmesini ve bulutta kullanıma hazır olmasını sağlar. Yetkili kullanıcıların, kuruluşunuzun hassas verilerinden üretken ve kötü aktörlere sahip olmalarını sağlamak için, kimlik doğrulaması uygulamalara erişimi denetler.

Aşağıdaki nedenlerden dolayı seçtiğiniz kimlik doğrulama yöntemi için Parola karması eşitlemesini kullanın veya etkinleştirin:

1. **Yüksek kullanılabilirlik ve olağanüstü durum kurtarma**. Doğrudan kimlik doğrulama ve Federasyon, şirket içi altyapıyı kullanır. Geçişli kimlik doğrulaması için şirket içi parmak izi, doğrudan kimlik doğrulama aracılarının gerektirdiği sunucu donanımını ve ağını içerir. Federasyon için şirket içi parmak izi de daha büyük olur. Çevre ağınızdaki sunucuların proxy kimlik doğrulama isteklerine ve iç federasyon sunucularına sahip olmasını gerektirir.

    Tek başarısızlık noktalarından kaçınmak için, yedekli sunucular dağıtın. Ardından, herhangi bir bileşen başarısız olursa kimlik doğrulama istekleri her zaman hizmet olarak gönderilir. Hem geçişli kimlik doğrulaması hem de Federasyon, kimlik doğrulama isteklerine yanıt vermek için etki alanı denetleyicilerine de dayanır, bu da başarısız olabilir. Bu bileşenlerin çoğunun sağlıklı kalması için bakım gerekir. Bakım planlanmadığı ve doğru şekilde uygulanmadıklarında kesintiler daha yüksektir. Microsoft Azure AD bulut kimlik doğrulama hizmeti küresel olarak ölçeklendirilirken ve her zaman kullanılabilir olduğundan, Parola karması eşitlemesini kullanarak kesintilerden kaçının.

2. **Şirket içi kesinti acil değer**.  Bir siber saldırı veya olağanüstü durum nedeniyle Şirket içi kesintiden kaynaklanan sonuçlar önemli olabilir. reputational marka zararlarından Paralyzed bir kuruluşa kadar olan saldırılara karşı kesintiye uğrammakla uğraşmayı başaramadı. Yakın zamanda, çoğu kuruluş, hedeflenen fidye yazılımı dahil olmak üzere şirket içi sunucuların açık kalmasına neden olan kötü amaçlı yazılım saldırılarına sahiptir. Microsoft, müşterilerin bu tür saldırılara uğramasına yardımcı olduğunda iki kuruluş kategorisi görür:

   * Daha önce parola karması eşitlemesine açık olan kuruluşlar, kimlik doğrulama yöntemini Parola karması eşitlemesini kullanacak şekilde değiştirdi. Bunlar birkaç saat içinde çevrimiçi olarak geri getirilir. Office 365 aracılığıyla e-postaya erişim kullanarak, sorunları çözmeye ve diğer bulut tabanlı iş yüklerine erişmeye çalışmış olurlar.

   * Daha önce parola karması eşitlemesini etkinleştirmediğiniz kuruluşların, iletişim için sorunları çözmek üzere güvenilmeyen dış tüketici e-posta sistemlerine çare olması gerekiyordu. Bu gibi durumlarda, kullanıcılar bulut tabanlı uygulamalarda yeniden oturum açabilmeniz için, bu kullanıcıların kendi şirket içi kimlik altyapısını geri yüklemesi için haftalar sürmesidir.

3. **Kimlik koruması**. Buluttaki kullanıcıları korumanın en iyi yöntemlerinden biri Azure AD Premium P2 Azure AD Kimlik Koruması. Microsoft, kötü aktörlerin sat ve koyu Web üzerinde kullanılabilir olmasını sağlayan Kullanıcı ve parola listelerine sürekli olarak Interneti tarar. Azure AD, kuruluşunuzdaki herhangi bir Kullanıcı adı ve parolanın güvenliğinin aşıldığını doğrulamak için bu bilgileri kullanabilir. Bu nedenle, hangi kimlik doğrulama yöntemini kullanırsanız kullanın, bu, Federasyon veya geçişli kimlik doğrulaması olsun, Parola karması eşitlemesini etkinleştirmek önemlidir. Sızdırılan kimlik bilgileri rapor olarak sunulur. Kullanıcıların, sızdırılan parolalarla oturum açmaya çalıştıklarında parolalarını değiştirmesine izin vermek veya bunları engellemek için bu bilgileri kullanın.

## <a name="conclusion"></a>Sonuç

Bu makalede, kuruluşların bulut uygulamalarına erişimi desteklemek için yapılandırabileceği ve dağıtabileceği çeşitli kimlik doğrulama seçenekleri özetlenmektedir. Kuruluşlar, çeşitli iş, güvenlik ve teknik gereksinimleri karşılamak için Parola karması eşitlemesi, geçişli kimlik doğrulaması ve Federasyon arasında seçim yapabilir.

Her kimlik doğrulama yöntemini göz önünde bulundurun. Çözümü dağıtmaya yönelik efor ve kullanıcının oturum açma işlemi deneyimi, iş gereksinimlerinizi karşılasın mı? Kuruluşunuzun her bir kimlik doğrulama yönteminin gelişmiş senaryolarına ve iş sürekliliği özelliklerine ihtiyacı olup olmadığını değerlendirin. Son olarak, her kimlik doğrulama yönteminin dikkate alınmasını değerlendirin. Bunlardan herhangi birini, seçiminizi uygulamanızı engelliyor mu?

## <a name="next-steps"></a>Sonraki adımlar

Günümüzde dünya dünyasında tehditler günde 24 saat ve her yerden gelir. Doğru kimlik doğrulama yöntemini uygulayın ve güvenlik risklerinizi azaltmalı ve kimliklerinizi koruacaktır.

Azure AD 'yi [kullanmaya](../../active-directory/fundamentals/get-started-azure-ad.md) başlayın ve kuruluşunuz için doğru kimlik doğrulama çözümünü dağıtın.

Federe 'dan bulut kimlik doğrulamasına geçiş yapmayı düşünüyorsanız, [oturum açma yöntemini değiştirme](../../active-directory/hybrid/plan-connect-user-signin.md)hakkında daha fazla bilgi edinin. Geçişi planlayıp uygulamanıza yardımcı olması için [Bu proje dağıtım planlarını](https://aka.ms/deploymentplans) kullanın veya yeni [hazırlanmış piyasaya çıkma](../../active-directory/hybrid/how-to-connect-staged-rollout.md) özelliğini kullanarak Federasyon kullanıcılarını, hazırlanmış bir yaklaşımda bulut kimlik doğrulamasını kullanmaya geçirin.
