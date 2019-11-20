---
title: Kimlik altyapınızın güvenliğini sağlamak için beş adım Azure Active Directory
description: Bu belgede, yöneticilerin Azure AD yeteneklerini kullanarak kuruluşlarını güvenli hale getirmeye yardımcı olmak için uygulaması gereken önemli eylemlerin bir listesi özetlenmektedir
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/28/2019
ms.author: martinco
ms.openlocfilehash: 9ea9bea83de0a177fa37d9a186f8962bac1394a4
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73101417"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Kimlik altyapınızı güvenli hale getirmenin beş adımı

Bu belgeyi okuyorsanız güvenliğin önemini farkındayız. Büyük olasılıkla kuruluşunuzun güvenliğini sağlama sorumluluğunu zaten taşıyor olabilirsiniz. Diğerlerinin güvenliğin önemini ikna etmeniz gerekiyorsa, en son [Microsoft Güvenlik Zekası raporunu](https://go.microsoft.com/fwlink/p/?linkid=2073747)okumak için bunları gönderin.

Bu belge, kuruluşunuzun siber saldırılara karşı yük almasını sağlamak için Beş adımlı bir denetim listesi kullanarak Azure Active Directory yeteneklerini kullanarak daha güvenli bir duruma sahip olmanıza yardımcı olur.

Bu denetim listesi, aşağıdaki adımları izleyerek kuruluşunuzu hemen korumak için kritik önerilen eylemleri hızlı bir şekilde dağıtmanıza yardımcı olur:

* Kimlik bilgilerinizi güçlendirin.
* Saldırı yüzeyi alanınızı küçültün.
* Tehdit yanıtını otomatikleştirin.
* Denetim ve izleme hakkında farkınızı artırın.
* Kendi kendine yardım ile daha öngörülebilir ve son kullanıcı güvenliğini sağlayın.

Bu denetim listesini okurken hangi özellik ve adımların tamamlandığını izlemediğinizden emin olun.

> [!NOTE]
> Bu belgedeki önerilerin birçoğu yalnızca kimlik sağlayıcısı olarak Azure Active Directory kullanmak üzere yapılandırılan uygulamalar için geçerlidir. Uygulamaları çoklu oturum açma için yapılandırmak, kimlik bilgileri ilkelerinin, tehdit algılama, denetim, günlük kaydı ve diğer özelliklerin bu uygulamalara eklenmesi için faydaların avantajlarından yararlanmanızı sağlar. [Azure Active Directory üzerinden çoklu oturum açma](../../active-directory/manage-apps/configure-single-sign-on-portal.md) , tüm bu önerilerin temel aldığı temelidir.

Bu belgedeki öneriler, Azure AD kiracının kimlik güvenlik yapılandırmasının otomatik değerlendirmesi olan [kimlik güvenli puanı](../../active-directory/fundamentals/identity-secure-score.md)ile hizalanır. Kuruluşlar, Azure AD portalındaki kimlik güvenli puanı sayfasını kullanarak, güvenlik için geçerli Microsoft [en iyi yöntemlerini](identity-management-best-practices.md) izlediklerinden emin olmak üzere geçerli güvenlik yapılandırmasındaki boşlukları bulabilir. Her öneriyi güvenli skor sayfasında uygulamak puanınızı artırır ve ilerleme durumunu izlemenize olanak tanır ve uygulamanızı diğer benzer büyüklükte kuruluşlara veya sektöre karşı karşılaştırmanıza yardımcı olur.

![Kimlik güvenli puanı](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Burada açıklanan özelliklerin birçoğu Azure AD Premium bir abonelik gerektirir, bazıları ücretsizdir. Daha fazla bilgi için lütfen [Azure Active Directory fiyatlandırımızı](https://azure.microsoft.com/pricing/details/active-directory/) ve [Azure AD Dağıtım denetim listesini](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2) gözden geçirin.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Başlamadan önce: MFA ile ayrıcalıklı hesapları koruma

Bu denetim listesine başlamadan önce, bu denetim listesini okurken güvenliği ihlal ettiğinizden emin olun. Önce ayrıcalıklı hesaplarınızı korumanız gerekir.

Ayrıcalıklı hesapların denetimini alan saldırganlar çok fazla zarar verebilir, bu yüzden öncelikle bu hesapların korunması önemlidir. [Azure AD güvenlik varsayılanlarını](../../active-directory/conditional-access/concept-conditional-access-security-defaults.md) veya [koşullu erişim](../../active-directory/conditional-access/plan-conditional-access.md)'i kullanarak kuruluşunuzdaki tüm yöneticiler için [Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md) (MFA) özelliğini etkinleştirin ve gerekli kılın. MFA uygulamadıysanız şimdi yapın! Bu önemlidir.

Tüm ayarlar mı? Denetim listesini kullanmaya başlayalım.

## <a name="step-1---strengthen-your-credentials"></a>Adım 1-kimlik bilgilerinizi güçlendirin 

Çoğu kurumsal güvenlik ihlali, parola spreyi, ihlal yürütme veya kimlik avı gibi birçok yöntemden biriyle güvenliği aşılmış bir hesapla yapılır. Bu videoda bu saldırılar hakkında daha fazla bilgi edinin (45 dk):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Kuruluşunuzun güçlü kimlik doğrulaması kullandığından emin olun

Tahmin edilen parola sıklığı, çok kötü amaçlı yazılımlara karşı koruma veya yeniden kullanım için, parolayı bir güçlü kimlik bilgisi biçimiyle yedeklemeniz kritik öneme sahiptir. [Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md)hakkında daha fazla bilgi edinin.

Temel düzeyde kimlik güvenliğini kolayca etkinleştirmek için [Azure AD güvenlik Varsayılanları](../../active-directory/conditional-access/concept-conditional-access-security-defaults.md)ile tek tıklamayla etkinleştirme ' yi kullanabilirsiniz. Güvenlik Varsayılanları, bir Kiracıdaki tüm kullanıcılar için Azure MFA 'yı uygular ve eski protokollerdeki oturum açma işlemlerini kiracı genelinde engeller.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Yaygın olarak saldırıya uğratan parolaları başlatın ve geleneksel karmaşıklığı ve süre sonu kurallarını devre dışı bırakın.

Birçok kuruluş geleneksel karmaşıklığı kullanır (özel karakter, sayı, büyük harf ve küçük harf ve parola süre sonu kuralları gerektirir). [Microsoft 'un araştırması](https://aka.ms/passwordguidance) , kullanıcıların tahmin etmek daha kolay olan parolaları seçmesini sağlar.

Azure AD 'nin [dinamik yasaklanmış parola](../../active-directory/authentication/concept-password-ban-bad.md) özelliği, kullanıcıların kolayca tahmin edebileceği parolalar ayarlamamasını engellemek için geçerli saldırgan davranışını kullanır. Bu özellik, kullanıcılar bulutta oluşturulduğunda her zaman açıktır, ancak artık [Windows Server Active Directory Için Azure AD parola korumasını](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)dağıtırken karma kuruluşlar için de kullanılabilir. Azure AD parola koruması, kullanıcıların bu ortak parolaları seçmelerini engeller ve belirttiğiniz özel anahtar sözcükleri içeren parolayı engellemek için genişletilebilir. Örneğin, kullanıcılarınızın şirketinizin ürün adlarını veya yerel bir spor ekibini içeren parolaları seçmelerini engelleyebilirsiniz.

Microsoft, [NIST kılavuzumuzu](https://pages.nist.gov/800-63-3/sp800-63b.html)temel alan aşağıdaki modern parola ilkesini benimsemenizi önerir:

1. Parolaların en az 8 karakter uzunluğunda olması gerekir. Daha uzun sürme, kullanıcıların öngörülebilir parolalar seçmesini, parolaları dosyalarda kaydetmesini veya onları aşağı yazmasını sağlar.
2. Kullanıcıları **Spring2019** gibi kolayca tahmin etmek için gereken süre sonu kurallarını devre dışı bırakın.
3. Karakter oluşturma gereksinimlerini devre dışı bırakın ve kullanıcıların parolalarında öngörülebilir karakter alternatifleri seçmesini sağlamak üzere genellikle saldırıya uğrayan parolalar seçmesini engelleyin.

Azure AD 'de doğrudan kimlik oluşturursanız, kullanıcıların kullanıcılar için kullanım [süresinin dolmasını engellemek Için PowerShell](../../active-directory/authentication/concept-sspr-policy.md) kullanabilirsiniz. Hibrit kuruluşlar, [etki alanı Grup İlkesi ayarlarını](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) veya [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy)'i kullanarak bu ilkeleri uygulamalıdır.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Sızdırılan kimlik bilgilerine karşı koruma ve kesintilere karşı esnekliği ekleme

Kuruluşunuz doğrudan kimlik doğrulama veya Federasyon ile karma kimlik çözümü kullanıyorsa, aşağıdaki iki nedenden dolayı Parola karması eşitlemesini etkinleştirmelisiniz:

* Azure AD yönetiminde [sızdırılan kimlik bilgilerine sahip kullanıcılar](../../active-directory/reports-monitoring/concept-risk-events.md) , "koyu Web" üzerinde sunulan Kullanıcı adı ve parola çiftleri hakkında sizi uyarır. Daha sonra ihlal edilen üçüncü taraf sitelerde kimlik avı, kötü amaçlı yazılım ve parola yeniden kullanımı aracılığıyla inanılmaz bir parola miktarı sızdırıldı. Microsoft bu sızdırılan kimlik bilgilerinin birçoğunu bulur ve bu raporda, kuruluşunuzda kimlik bilgileriyle eşleşiyorsa, ancak yalnızca [parola karma eşitlemesini etkinleştirdiğinizde](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)size bildirir!
* Şirket içi kesinti durumunda (örneğin, fidye yazılımı saldırısında), [Parola karması eşitlemesini kullanarak bulut kimlik doğrulamasını](choose-ad-authn.md)kullanmaya geçebilirsiniz. Bu yedekleme kimlik doğrulama yöntemi, Office 365 dahil Azure Active Directory kimlik doğrulaması için yapılandırılan uygulamalara erişmeye devam etmeyi sağlar. Bu durumda, şirket içi kesinti çözümlenene kadar BT personelinin, verileri paylaşmak için kişisel e-posta hesapları çare olması gerekmez.

[Parola karması eşitleme](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) 'nin nasıl çalıştığı hakkında daha fazla bilgi edinin.

> [!NOTE]
> Parola karma eşitlemesini etkinleştirir ve Azure AD etki alanı Hizmetleri 'ni kullanıyorsanız, Kerberos (AES 256) karmaları ve isteğe bağlı olarak NTLM (RC4, anahtar yok) karmaları da şifrelenir ve Azure AD 'ye eşitlenir. 

### <a name="implement-ad-fs-extranet-smart-lockout"></a>AD FS Extranet akıllı kilitleme uygulama

Uygulamaları [Azure AD akıllı kilitleme](../../active-directory/authentication/concept-sspr-howitworks.md)'den Azure AD avantajına doğrudan kimlik doğrulaması yapacak şekilde yapılandıran kuruluşlar. Windows Server 2012R2 ' de AD FS kullanıyorsanız, AD FS [Extranet kilitleme koruması](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)uygulayın. Windows Server 2016 üzerinde AD FS kullanıyorsanız, [extranet akıllı kilitleme](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)uygulayın. Akıllı extranet kilitleme AD FS, kullanıcıların Active Directory kilitlenmesini sağlarken AD FS hedef olan deneme yanılma saldırılarına karşı koruma sağlar.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Doğası gereği güvenli, kimlik bilgilerinin kullanımı daha kolay yararlanın

[Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)kullanarak, bilgisayarlarda ve mobil cihazlarda güçlü iki öğeli kimlik doğrulaması ile parolaları değiştirebilirsiniz. Bu kimlik doğrulaması, bir cihaza güvenli bir şekilde bağlanan ve biyometrik ya da PIN kullanan yeni bir kullanıcı kimlik bilgisi türü içerir.

## <a name="step-2---reduce-your-attack-surface"></a>2\. adım-saldırı yüzeyini azaltma

Parola güvenliğinin tehlikeye düşmesi sırasında, kuruluşunuzda saldırı yüzeyini en aza indirmek kritik öneme sahiptir. Daha eski, daha az güvenli protokollerin kullanımını ortadan kaldırma, erişim giriş noktalarını sınırlandırma ve kaynaklara yönetim erişiminin daha önemli bir denetimini kullanmak saldırı yüzeyi alanını azaltmaya yardımcı olabilir.

### <a name="block-legacy-authentication"></a>Eski kimlik doğrulamasını engelleme

Azure AD ile kimlik doğrulaması yapmak ve şirket verilerine erişmek için kendi eski yöntemlerini kullanan uygulamalar kuruluşlar için başka bir risk oluşturur. Eski kimlik doğrulaması kullanan uygulama örnekleri, POP3, ıMAP4 veya SMTP istemcileridir. Eski kimlik doğrulama uygulamaları kullanıcı adına kimlik doğrular ve Azure AD 'nin gelişmiş güvenlik değerlendirmeleri yapmasını önler. Çok faktörlü kimlik doğrulaması ve koşullu erişimi desteklediğinden, alternatif, modern kimlik doğrulama, güvenlik riskinizi azaltır. Aşağıdaki üç eylemi öneriyoruz:

1. [AD FS kullanıyorsanız, eski kimlik doğrulamasını](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)engelleyin.
2. [SharePoint Online ve Exchange Online 'ı modern kimlik doğrulaması kullanacak şekilde](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md)ayarlayın.
3. Azure AD Premium sahipseniz, eski kimlik doğrulamasını engellemek için [koşullu erişim ilkelerini](../../active-directory/conditional-access/conditions.md) kullanın, aksi takdırde [Azure AD güvenlik varsayılanlarını](../../active-directory/conditional-access/concept-conditional-access-security-defaults.md)kullanın.

### <a name="block-invalid-authentication-entry-points"></a>Geçersiz kimlik doğrulama giriş noktalarını engelle

İhlal durumunu varsay ' ı kullanarak, riskli Kullanıcı kimlik bilgilerinin ne zaman ortaya çıktığını azaltmanız gerekir. Ortamınızdaki her uygulama için geçerli kullanım durumları göz önünde bulundurun: hangi gruplar, hangi ağlar, hangi cihazlar ve diğer öğeler yetkilendirilir, sonra Rest 'i engeller. [Azure AD koşullu erişimi](../../active-directory/conditional-access/overview.md)sayesinde, yetkili kullanıcıların, sizin tanımladığınız belirli koşullara göre uygulamalarına ve kaynaklarına nasıl erişdiğini denetleyebilirsiniz.

### <a name="block-end-user-consent"></a>Son Kullanıcı onayını engelle

Varsayılan olarak, Azure AD 'deki tüm kullanıcılara, OAuth 2,0 'den yararlanan uygulamalar ve şirket verilerine erişmek için Microsoft Identity Grant [Framework](../../active-directory/develop/consent-framework.md) izinleri verilebilir. Tebrikler, kullanıcıların Microsoft 365 ve Azure ile tümleştirilen faydalı uygulamaları kolayca almasına izin verirken, bu, kullanılmıyorsa ve izlenmezse bir riski temsil edebilir. [Tüm gelecek Kullanıcı onay Işlemlerini devre dışı bırakmak](../../active-directory/manage-apps/methods-for-removing-user-access.md) , yüzey alanınızı azaltmanıza ve bu riski azaltmanıza yardımcı olabilir. Son Kullanıcı onayı devre dışı bırakılmışsa, önceki onay onayları yine de kabul edilir, ancak gelecekteki tüm onay işlemleri bir yönetici tarafından gerçekleştirilmelidir. Bu işlevi devre dışı bırakmadan önce, kullanıcıların yeni uygulamalar için yönetici onayını nasıl isteyeceğini anlayabilmesi önerilir; Bunun yapılması, Kullanıcı uyumsuzlukmasını azaltmaya, destek birimini en aza indirmenize ve kullanıcıların Azure dışı AD kimlik bilgilerini kullanarak uygulamalara kaydolmayın olmasını sağlamalıdır.

### <a name="implement-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management Uygula

"İhlal varsay" ' ın başka bir etkisi, güvenliği aşılmış bir hesabın ayrıcalıklı bir rolle çalışabilme olasılığını en aza indirmektir. 

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) size yardımcı olurken hesap ayrıcalıklarını en aza indirmenize yardımcı olur:

* Yönetim rollerine atanan kullanıcıları belirleyip yönetin.
* Kaldırmanız gereken kullanılmayan veya aşırı ayrıcalık rollerini anlayın.
* Ayrıcalıklı rollerin Multi-Factor Authentication tarafından korunduğundan emin olmak için kurallar oluşturun.
* Ayrıcalıklı rollerin yalnızca ayrıcalıklı görevi gerçekleştirmek için yeterince uzun verildiğinden emin olmak için kurallar oluşturun.

Azure AD PıM 'yi etkinleştirin, ardından Yönetici rolleri atanmış kullanıcıları görüntüleyin ve bu rollerdeki gereksiz hesapları kaldırın. Kalan ayrıcalıklı kullanıcılar için bunları kalıcı 'dan uygun hale taşıyın. Son olarak, bu ayrıcalıklı rollere erişim kazanmak istediklerinde, gerekli değişiklik denetimiyle bu şekilde güvenli bir şekilde yapabilecekleri konusunda emin olmak için uygun ilkeler oluşturun.

Ayrıcalıklı hesap işleminizi dağıtmanın bir parçası olarak, kendinizi kilitlerseniz Azure AD 'ye erişiminizin olduğundan emin olmak için [en iyi iki acil durum hesabı oluşturmak için en iyi](../../active-directory/users-groups-roles/directory-admin-roles-secure.md) uygulamayı izleyin.

## <a name="step-3---automate-threat-response"></a>3\. adım-tehdit yanıtını otomatikleştirin

Azure Active Directory, algılama ve yanıt arasındaki gecikmeyi kaldırmak için saldırıları otomatik olarak ele alan birçok özelliğe sahiptir. Dolandırıcıların ortamınıza eklemek için kullandıkları zamanı azaltdığınızda maliyetleri ve riskleri azaltabilirsiniz. Gerçekleştirebileceğiniz somut adımlar aşağıda verilmiştir.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Azure AD Kimlik Koruması kullanarak Kullanıcı risk güvenlik ilkesi uygulama

Kullanıcı riski, kullanıcının kimliğinin tehlikeye girdiği ve kullanıcının kimliğiyle ilişkili [Kullanıcı risk algılamaları](../../active-directory/identity-protection/overview.md) temelinde hesaplanma olasılığını gösterir. Kullanıcı risk ilkesi, risk düzeyini belirli bir kullanıcı veya gruba değerlendiren bir koşullu erişim ilkesidir. Düşük, orta ve yüksek risk düzeyine bağlı olarak, bir ilke, erişimi engelleyecek veya Multi-Factor Authentication kullanarak güvenli bir parola değişikliği gerektirecek şekilde yapılandırılabilir. Microsoft 'un önerisi, yüksek riskli kullanıcılar için güvenli bir parola değişikliği yapmanız gerekir.

![Riskli oldukları belirlenen kullanıcılar](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Azure AD Kimlik Koruması kullanarak oturum açma risk ilkesi uygulama

Oturum açma riski, hesap sahibi dışında birisinin kimliği kullanarak oturum açmaya çalışıyor olma olasılığının bir olasılığıdır. [Oturum açma riski ilkesi](../../active-directory/identity-protection/overview.md) , risk düzeyini belirli bir kullanıcı veya gruba değerlendiren bir koşullu erişim ilkesidir. Risk düzeyine (yüksek/orta/düşük) göre bir ilke, erişimi engelleyecek veya Multi-Factor Authentication 'ı zorlamaya olanak verecek şekilde yapılandırılabilir. Orta veya daha fazla risk içinde Multi-Factor Authentication 'ı zorlayadığınızdan emin olun.

![Anonim IP 'lerden oturum açma](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>4\. adım-tanımayı artırma

Güvenlikle ilgili olayları ve ilgili uyarıları denetleme ve günlüğe kaydetme, etkili bir koruma stratejisinin önemli bileşenleridir. Güvenlik günlükleri ve raporları, size bir elektronik şüpheli etkinlik kaydı sağlar ve ağın denendiği veya başarılı bir şekilde dışarıdan sızma ve iç saldırılar olduğunu gösterebilen desenleri tespit etmenize yardımcı olur. Kullanıcı etkinliğini izlemek, mevzuat uyumluluğu, analiz yapmak ve daha fazlasını yapmak için denetimi kullanabilirsiniz. Uyarılar güvenlik olaylarının bildirimleri sağlar.

### <a name="monitor-azure-ad"></a>Azure AD 'yi izleme

Microsoft Azure hizmetleri ve özellikleri, güvenlik ilkeleriniz ve mekanizmalarda boşlukları belirlemenize ve binleri önlemeye yardımcı olmak üzere bu boşlukları gidermenize yardımcı olmak için yapılandırılabilir güvenlik denetimi ve günlüğe kaydetme seçenekleri sağlar. [Azure günlük kaydı ve](log-audit.md) denetim [, Azure Active Directory portalındaki denetim etkinliği raporları](../../active-directory/reports-monitoring/concept-audit-logs.md)kullanabilirsiniz.

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Karma ortamlarda Azure AD Connect Health izleme

[Azure AD Connect Health ile AD FS izleme](../../active-directory/hybrid/how-to-connect-health-adfs.md) , AD FS altyapınızdaki olası sorunlar ve saldırı hakkında daha fazla bilgi sağlar. Azure AD Connect Health ayrıntılar, çözüm adımları ve ilgili belgelerin bağlantılarıyla birlikte uyarılar sunar; kimlik doğrulama trafiğiyle ilgili birkaç ölçüm için Kullanım Analizi; performans izleme ve raporlar.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Azure AD Kimlik Koruması olaylarını izleme

[Azure AD kimlik koruması](../../active-directory/identity-protection/overview.md) , kuruluşunuzun kimliklerini etkileyen olası güvenlik açıklarını algılamak için kullanabileceğiniz bir bildirim, izleme ve raporlama aracıdır. Sızan kimlik bilgileri, imkansız seyahat ve virüs bulaşmış cihazlardan, anonim IP adreslerinden, şüpheli etkinlikle ilişkili IP adreslerinden ve bilinmeyen konumlardan yapılan oturum açma işlemleri gibi risk algılamalarını algılar. Risk ve/veya haftalık özet e-postalarında kullanıcıların e-postalarını almak için bildirim uyarılarını etkinleştirin.

Azure AD Kimlik Koruması, günlük izlemeniz gereken iki önemli rapor sağlar:
1. Riskli oturum açma raporları, araştırmanız gereken kullanıcı oturum açma etkinliklerini açacaktır, meşru sahip, oturum açma gerçekleştirmemiş olabilir.
2. Riskli Kullanıcı raporları, algılanan sızdırılan kimlik bilgisi veya farklı konumlardan oturum açmış olan Kullanıcı, mümkün olmayan bir seyahat olayına neden olan kullanıcı hesaplarını açığa çıkabilir. 

![Riskli oldukları belirlenen kullanıcılar](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Uygulamaları denetleme ve onaylanan izinler

Kullanıcılar, e-postaları gibi profil bilgilerine ve kullanıcı verilerine erişim kazanabilmek için güvenliği aşılmış bir Web sitesine veya uygulamalarına gidiliyor olabilir. Kötü amaçlı bir aktör, posta kutusu içeriğini şifrelemek için aldığı onaylı izinleri kullanabilir ve posta kutusu verilerinizi yeniden kazanmak için bir ransoh talep edebilir. Yöneticiler, kullanıcılar tarafından verilen izinleri [gözden geçirmeli ve denetlemelidir](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) .

## <a name="step-5---enable-end-user-self-service"></a>5\. adım-Son Kullanıcı self servis hizmetini etkinleştirme

Mümkün olduğunca verimlilik ile güvenliği dengelemek isteyeceksiniz. Uzun çalıştırmada güvenlik için bir temel ayarlamanıza olanak tanıyan anlayış ile yolculuğuna yaklaşmayı sağlayan aynı satırlarda, kalan dikkatli sırasında kullanıcılarınızı güçlendirin.

### <a name="implement-self-service-password-reset"></a>Self servis parola sıfırlama uygulama

Azure AD 'nin [self servis parola sıfırlama (SSPR)](../../active-directory/authentication/quickstart-sspr.md) , BT yöneticilerinin, yardım masası veya yönetici müdahalesi olmadan parolalarını veya hesaplarını sıfırlamasına veya bunların kilidini açmalarına izin veren basit bir yol sunar. Sistem, kullanıcılar parolalarını sıfırlarken ve kötüye kullanma veya kötüye kullanma hakkında uyarı veren bildirimlerle birlikte, izleyen ayrıntılı raporlama içerir.

### <a name="implement-self-service-group-and-application-access"></a>Self Servis grubunu ve uygulama erişimini Uygula

Azure AD, yönetici olmayan güvenlik gruplarını, Office 365 gruplarını, uygulama rollerini ve erişim paketi kataloglarını kullanarak kaynaklara erişimi yönetme yeteneği sağlar.  [Self servis grup yönetimi](../../active-directory/users-groups-roles/groups-self-service-management.md) , Grup sahiplerinin bir yönetim rolü atanmasına gerek kalmadan kendi gruplarını yönetmesine olanak sağlar. Kullanıcılar, isteklerini işlemek için yöneticilere güvenmek zorunda kalmadan Office 365 grupları oluşturup yönetebilir ve kullanılmayan gruplar otomatik olarak sona erer.  [Azure AD Yetkilendirme Yönetimi](../../active-directory/governance/entitlement-management-overview.md) , kapsamlı erişim isteği iş akışlarıyla ve otomatik süre dolduktan sonra temsilciyi ve görünürlüğü daha da sunar.  Yönetici olmayan kullanıcılara, kendilerine ait oldukları gruplar, takımlar, uygulamalar ve SharePoint Online siteleri için kendi erişim paketlerini yapılandırma olanağı, örneğin, çalışanın, erişimi onaylaması gereken özel ilkelerle onaylayan olarak Yöneticiler ve iş ortağı sponsorları.

### <a name="implement-azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmeleri uygulama

[Azure AD erişim gözden geçirmeleriyle](../../active-directory/governance/access-reviews-overview.md), bir güvenlik standardı bulundurduğunuzdan emin olmak için erişim paketi ve grup üyeliklerini yönetebilir, kurumsal uygulamalara ve ayrıcalıklı rol atamalarına erişebilirsiniz.  Kullanıcıların kendileri tarafından düzenli olarak ortaya çıkarması, kaynak sahipleri ve diğer gözden geçirenler, kullanıcıların artık ihtiyaç duymuyorsa uzun süre boyunca erişimi korumalarına de dikkat edin.

## <a name="summary"></a>Özet

Güvenli bir kimlik altyapısının birçok yönü vardır, ancak bu beş adımlı denetim listesi, daha güvenli ve güvenli bir kimlik altyapısını hızlı bir şekilde gerçekleştirmenize yardımcı olur:

* Kimlik bilgilerinizi güçlendirin.
* Saldırı yüzeyi alanınızı küçültün.
* Tehdit yanıtını otomatikleştirin.
* Denetim ve izleme hakkında farkınızı artırın.
* Kendi kendine yardım ile daha öngörülebilir ve son kullanıcı güvenliğini sağlayın.

Kimlik güvenliğini ne kadar ciddi hale getirmek istediğinizi biliyoruz ve bu belgede kuruluşunuz için daha güvenli bir zaman Kılavuzu olması önerilir.

## <a name="next-steps"></a>Sonraki adımlar

Önerileri planlamak ve dağıtmak için yardıma ihtiyacınız varsa yardım için [Azure AD proje dağıtım planlarına](https://aka.ms/deploymentplans) bakın.

Tüm bu adımlar tamamlandıysa, Microsoft 'un [kimlik güvenli puanı](../../active-directory/fundamentals/identity-secure-score.md)'nı kullanın. Bu, [en son en iyi uygulamalar](identity-management-best-practices.md) ve güvenlik tehditleri ile güncel tutmaya devam edecektir.
