---
title: 'Azure AD Connect: Kullanıcı oturum açma | Microsoft Docs'
description: Özel ayarlar için Kullanıcı oturum açma Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbcc05093d801261493745c61dc5f68878d338b0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607672"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect Kullanıcı oturum açma seçenekleri
Azure Active Directory (Azure AD) Connect, kullanıcılarınızın aynı parolaları kullanarak hem bulutta hem de şirket içi kaynaklarda oturum açmalarına olanak tanır. Bu makalede, Azure AD 'de oturum açmak için kullanmak istediğiniz kimliği seçmenize yardımcı olmak üzere her bir kimlik modeli için temel kavramlar açıklanır.

Zaten Azure AD kimlik modeli hakkında bilginiz varsa ve belirli bir yöntem hakkında daha fazla bilgi edinmek istiyorsanız uygun bağlantıya bakın:

* [Sorunsuz çoklu oturum açma](how-to-connect-sso.md) ile [parola KARMASı eşitlemesi](#password-hash-synchronization) (SSO)
* [Sorunsuz çoklu oturum açma (SSO)](how-to-connect-sso.md) ile [geçişli kimlik doğrulaması](how-to-connect-pta.md)
* [Federasyon SSO 'SU (Active Directory Federasyon Hizmetleri (AD FS) (AD FS) ile)](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [PingFederate ile Federasyon](#federation-with-pingfederate)

> [!NOTE] 
> Azure AD için Federasyonu yapılandırarak, Azure AD kiracınız ile Federal etki alanlarınız arasında güven kurarsınız. Bu güvene sahip olan Federasyon etki alanı kullanıcıları kiracının içindeki Azure AD bulut kaynaklarına erişebilir.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Kuruluşunuz için Kullanıcı oturum açma yöntemini seçme
Azure AD Connect uygulama ilk kararı, kullanıcılarınızın oturum açmak için kullanacağı kimlik doğrulama yöntemini seçmektir. Kuruluşunuzun güvenlik ve gelişmiş gereksinimlerini karşılayan doğru yöntemi seçtiğinizden emin olmak önemlidir. Kimlik doğrulaması kritik öneme sahiptir, çünkü bulutta uygulamalara ve verilere erişmek için kullanıcıların kimliklerini doğrulayacaktır. Doğru kimlik doğrulama yöntemini seçmek için, istediğiniz zaman, mevcut altyapıyı, karmaşıklığı ve tercih ettiğiniz maliyeti göz önünde bulundurmanız gerekir. Bu faktörler her kuruluş için farklılık gösterebilir ve zaman içinde değişebilir.

Azure AD aşağıdaki kimlik doğrulama yöntemlerini destekler: 

* **Bulut kimlik doğrulaması** -bu kimlik doğrulama yöntemini seçtiğinizde, kullanıcının oturum açması için kimlik doğrulama IŞLEMI Azure AD tarafından ele alır. Bulut kimlik doğrulamasıyla, iki seçenekten birini seçebilirsiniz: 
   * **Parola karması eşitleme (PHS)** -Parola karması eşitleme, kullanıcıların Azure AD Connect dışında ek bir altyapı dağıtmak zorunda kalmadan şirket içinde kullandıkları aynı kullanıcı adını ve parolayı kullanmasına olanak sağlar. 
   * **Geçişli kimlik doğrulaması (PTA)** -Bu seçenek Parola karması eşitlemesine benzerdir, ancak güçlü güvenlik ve uyumluluk ilkelerine sahip kuruluşlar için şirket içi yazılım aracılarını kullanarak basit bir parola doğrulaması sağlar.
* **Federasyon kimlik doğrulaması** -bu kimlik doğrulama yöntemini SEÇTIĞINIZDE Azure AD, kullanıcının oturum açma kimliğini doğrulamak için AD FS veya üçüncü taraf Federasyon sistemi gibi ayrı bir güvenilen kimlik doğrulama sistemine kimlik doğrulama işlemini kapatır. 

Yalnızca Office 365, SaaS uygulamaları ve diğer Azure AD tabanlı kaynaklarda Kullanıcı oturumu açmayı etkinleştirmek isteyen çoğu kuruluş için varsayılan parola karması eşitleme seçeneğini öneririz.
 
Kimlik doğrulama yöntemi seçme hakkında ayrıntılı bilgi için bkz [. Azure Active Directory karma kimlik çözümünüz için doğru kimlik doğrulama yöntemini seçme](../../security/fundamentals/choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Parola karması eşitleme
Parola karması eşitlemeyle, kullanıcı parolalarının karmaları şirket içi Active Directory Azure AD 'ye eşitlenir. Parolalar değiştirildiğinde veya şirket içinde sıfırlandığında, kullanıcılarınızın bulut kaynakları ve şirket içi kaynaklar için her zaman aynı parolayı kullanabilmesi için yeni parola karmaları hemen Azure AD ile eşitlenir. Parolalar hiçbir şekilde Azure AD 'ye gönderilmez veya Azure AD 'de açık metin olarak depolanmaz. Azure AD 'de self servis parola sıfırlama özelliğini etkinleştirmek için parola karma eşitlemesini parola geri yazma özelliğiyle birlikte kullanabilirsiniz.

Ayrıca, şirket ağındaki etki alanına katılmış makinelerde bulunan kullanıcılar için [sorunsuz SSO](how-to-connect-sso.md) 'yu etkinleştirebilirsiniz. Çoklu oturum açma özelliğiyle, etkin kullanıcıların bulut kaynaklarına güvenli bir şekilde erişmesini sağlamak için yalnızca bir Kullanıcı adı girmesi gerekir.

![Parola karması eşitleme](./media/plan-connect-user-signin/passwordhash.png)

Daha fazla bilgi için bkz. [Parola karması eşitleme](how-to-connect-password-hash-synchronization.md) makalesi.

### <a name="pass-through-authentication"></a>Geçişli kimlik doğrulama
Geçişli kimlik doğrulaması ile kullanıcının parolası, şirket içi Active Directory denetleyicisine göre onaylanır. Parolanın herhangi bir biçimde Azure AD 'de mevcut olması gerekmez. Bu, oturum açma saati kısıtlamaları gibi şirket içi ilkelerin, bulut hizmetlerinde kimlik doğrulaması sırasında değerlendirilmesini sağlar.

Geçişli kimlik doğrulaması, şirket içi ortamda Windows Server 2012 R2 etki alanına katılmış bir makinede basit bir aracı kullanır. Bu aracı parola doğrulama isteklerini dinler. Herhangi bir gelen bağlantı noktasının Internet 'e açık olmasını gerektirmez.

Ayrıca, şirket ağındaki etki alanına katılmış makinelerde bulunan kullanıcılar için çoklu oturum açmayı etkinleştirebilirsiniz. Çoklu oturum açma özelliğiyle, etkin kullanıcıların bulut kaynaklarına güvenli bir şekilde erişmesini sağlamak için yalnızca bir Kullanıcı adı girmesi gerekir.
![Doğrudan kimlik doğrulama](./media/plan-connect-user-signin/pta.png)

Daha fazla bilgi için bkz.
- [Doğrudan kimlik doğrulama](how-to-connect-pta.md)
- [Çoklu oturum açma](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Windows Server 2012 R2 'de AD FS yeni veya var olan bir grubu kullanan federasyon
Federasyon oturum açma ile kullanıcılarınız, şirket içi parolalarla Azure AD tabanlı hizmetlerde oturum açabilirler. Şirket ağı üzerinde olduklarından, parolalarının girilmesi bile gerekmez. AD FS ile Federasyon seçeneğini kullanarak, Windows Server 2012 R2 'de AD FS yeni veya var olan bir grubu dağıtabilirsiniz. Var olan bir grubu belirtmeyi seçerseniz, kullanıcılarınızın oturum açmasını sağlamak için grubunuz ve Azure AD arasındaki güveni yapılandırır Azure AD Connect.

<center>

![Windows Server 2012 R2 'de AD FS Federasyonu](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Windows Server 2012 R2 'de AD FS Federasyon dağıtma

Yeni bir grup dağıtıyorsanız şunlar gerekir:

* Federasyon sunucusu için bir Windows Server 2012 R2 sunucusu.
* Web uygulaması ara sunucusu için bir Windows Server 2012 R2 sunucusu.
* Hedeflenen Federasyon Hizmeti adınız için bir SSL sertifikası olan bir. pfx dosyası. Örneğin: fs.contoso.com.

Yeni bir grup dağıtıyorsanız veya var olan bir grubu kullanıyorsanız, şunlar gerekir:

* Federasyon sunucularınızda yerel yönetici kimlik bilgileri.
* Üzerinde Web uygulaması ara sunucusu rolünü dağıtmayı planladığınız herhangi bir çalışma grubu sunucusunda (etki alanına katılmamış) yerel yönetici kimlik bilgileri.
* Windows Uzaktan Yönetimi kullanarak üzerinde AD FS veya Web uygulaması proxy 'Si yüklemek istediğiniz diğer makinelere bağlanabiliyor olması için Sihirbazı çalıştırdığınız makine.

Daha fazla bilgi için bkz. [SSO 'yu AD FS Ile yapılandırma](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>PingFederate ile federasyon
Federasyon oturum açma ile kullanıcılarınız, şirket içi parolalarla Azure AD tabanlı hizmetlerde oturum açabilirler. Şirket ağı üzerinde olduklarından, parolalarının girilmesi bile gerekmez.

PingFederate 'i Azure Active Directory ile kullanım için yapılandırma hakkında daha fazla bilgi için bkz. [Azure Active Directory ve Office 365 Ile Pingfederate tümleştirmesi](https://www.pingidentity.com/AzureADConnect)

PingFederate kullanarak Azure AD Connect ayarlama hakkında daha fazla bilgi için bkz. [Azure AD Connect özel yükleme](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>AD FS önceki bir sürümünü veya üçüncü taraf çözümünü kullanarak oturum açın
AD FS önceki bir sürümünü (örneğin, AD FS 2,0) veya bir üçüncü taraf Federasyon sağlayıcısını kullanarak bulutta oturum açmayı zaten yapılandırdıysanız, Azure AD Connect aracılığıyla Kullanıcı oturum açma yapılandırmasını atlamayı seçebilirsiniz. Bu, oturum açma çözümünüzü kullanmaya devam ederken Azure AD Connect en son eşitlemeyi ve diğer yeteneklerini almanızı sağlar.

Daha fazla bilgi için bkz. [Azure AD üçüncü taraf Federasyon uyumluluğu listesi](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Kullanıcı oturum açma ve Kullanıcı asıl adı
### <a name="understanding-user-principal-name"></a>Kullanıcı asıl adını anlama
Active Directory, varsayılan kullanıcı asıl adı (UPN) soneki, Kullanıcı hesabının oluşturulduğu etki alanının DNS adıdır. Çoğu durumda bu, Internet 'teki kuruluş etki alanı olarak kayıtlı etki alanı adıdır. Ancak, Active Directory etki alanları ve güvenleri kullanarak daha fazla UPN soneki ekleyebilirsiniz.

Kullanıcının UPN 'si biçimindedir username@domain. Örneğin, "contoso.com" adlı bir etki alanı Active Directory için John adlı bir Kullanıcı "john@contoso.com" UPN 'sine sahip olabilir. Kullanıcının UPN 'si, RFC 822 ' i temel alır. UPN ve e-posta aynı biçimi paylaşsa da, bir kullanıcı için UPN değeri kullanıcının e-posta adresiyle aynı olabilir veya olmayabilir.

### <a name="user-principal-name-in-azure-ad"></a>Azure AD 'de Kullanıcı asıl adı
Azure AD Connect Sihirbazı userPrincipalName özniteliğini kullanır veya Azure AD 'de Kullanıcı asıl adı olarak şirket içi olarak kullanılacak özniteliği (özel yüklemede) belirtmenize izin verir. Bu, Azure AD 'de oturum açmak için kullanılan değerdir. UserPrincipalName özniteliğinin değeri Azure AD 'de doğrulanmış bir etki alanına karşılık gelmiyorsa, Azure AD bunu varsayılan bir. onmicrosoft.com değeri ile değiştirir.

Azure Active Directory içindeki her dizin, Azure 'u veya diğer Microsoft hizmetlerini kullanmaya başlamanıza olanak sağlayan, contoso.onmicrosoft.com biçimindeki yerleşik bir etki alanı adı ile birlikte gelir. Özel etki alanları kullanarak oturum açma deneyimini geliştirebileceğiniz ve basitleştirebiliriz. Azure AD 'de özel etki alanı adları ve bir etki alanını doğrulama hakkında daha fazla bilgi için, bkz. [Azure Active Directory için özel etki alanı adınızı ekleme](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Azure AD oturum açma yapılandırması
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD Connect ile Azure AD oturum açma yapılandırması
Azure AD oturum açma deneyimi, Azure AD 'nin Azure AD dizininde doğrulanan özel etki alanlarından biriyle eşitlenen bir kullanıcının Kullanıcı asıl adı sonekiyle eşleşip eşleşmediğine bağlıdır. Azure AD Connect, Azure AD oturum açma ayarlarını yapılandırırken yardım sağlar, böylece buluttaki Kullanıcı oturum açma deneyimi şirket içi deneyimle benzerdir.

Azure AD Connect, etki alanları için tanımlanan UPN soneklerini listeler ve bunları Azure AD 'de özel bir etki alanıyla eşleştirmeye çalışır. Daha sonra, alınması gereken uygun eylemde size yardımcı olur.
Azure AD oturum açma sayfası, şirket içi Active Directory için tanımlanan UPN soneklerini listeler ve her bir sonekine karşılık gelen durumu görüntüler. Durum değerleri aşağıdakilerden biri olabilir:

| Durum | Açıklama | Eylem gerekli |
|:--- |:--- |:--- |
| Doğrulandı |Azure AD Connect, Azure AD 'de eşleşen doğrulanmış bir etki alanı buldu. Bu etki alanı için tüm kullanıcılar şirket içi kimlik bilgilerini kullanarak oturum açabilir. |Herhangi bir eylemde bulunmanız gerekmez. |
| Doğrulanmadı |Azure AD Connect Azure AD 'de eşleşen bir özel etki alanı buldu, ancak bu, doğrulanmadı. Etki alanı doğrulanmadıysa, bu etki alanının kullanıcılarının UPN soneki, eşitlemeden sonra varsayılan. onmicrosoft.com sonekine değiştirilir. | [Azure AD 'de özel etki alanını doğrulayın.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Eklenmemiş |Azure AD Connect, UPN sonekine karşılık gelen özel bir etki alanı bulamadı. Etki alanı Azure 'da eklenmemişse ve doğrulanmadıysa, bu etki alanının kullanıcılarının UPN soneki default. onmicrosoft.com sonekine değiştirilir. | [UPN sonekine karşılık gelen özel bir etki alanı ekleyin ve doğrulayın.](../fundamentals/add-custom-domain.md) |

Azure AD oturum açma sayfasında, şirket içi Active Directory için tanımlanan UPN sonekleri ve Azure AD 'de karşılık gelen özel etki alanı geçerli doğrulama durumuyla birlikte listelenir. Özel bir yüklemede artık **Azure AD oturum açma** sayfasında Kullanıcı asıl adı için olan özniteliği seçebilirsiniz.

![Azure AD oturum açma sayfası](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Azure AD 'den özel etki alanlarının en son durumunu yeniden getirmek için Yenile düğmesine tıklayabilirsiniz.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Azure AD 'de Kullanıcı asıl adı için özniteliği seçme
UserPrincipalName özniteliği, kullanıcıların Azure AD 'de ve Office 365 ' de oturum açtıklarında kullandıkları özniteliktir. Kullanıcılar eşitlenmeden önce Azure AD 'de kullanılan etki alanlarını (UPN sonekleri olarak da bilinir) doğrulamanız gerekir.

Varsayılan öznitelik userPrincipalName ' i tutmanız önemle tavsiye ederiz. Bu öznitelik yönlendirilemez ve doğrulanamazsa, oturum açma KIMLIĞINI tutan öznitelik olarak başka bir öznitelik (örneğin, e-posta) seçmek mümkündür. Bu, alternatif KIMLIK olarak bilinir. Alternatif KIMLIK özniteliği değeri, RFC 822 standardına uymalıdır. Oturum açma çözümü olarak hem parola SSO 'SU hem de Federasyon SSO 'SU ile alternatif bir KIMLIK kullanabilirsiniz.

> [!NOTE]
> Alternatif KIMLIK kullanılması tüm Office 365 iş yükleri ile uyumlu değildir. Daha fazla bilgi için bkz. [Alternatif oturum açma kimliğini yapılandırma](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Farklı özel etki alanı durumları ve bunların Azure oturum açma deneyimine etkileri
Azure AD dizininizde özel etki alanı durumları ve şirket içinde tanımlı UPN sonekleri arasındaki ilişkiyi anlamak çok önemlidir. Azure AD Connect kullanarak eşitleme ayarlarken farklı olası Azure oturum açma deneyimlerinden ilerlim.

Aşağıdaki bilgiler için, UPN 'nin bir parçası olarak şirket içi dizinde kullanılan UPN soneki contoso.com ile ilgilendiğimiz varsayıyız. Örneğin, örneğin user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Hızlı ayarlar/parola karması eşitleme

| Durum | Kullanıcı Azure oturum açma deneyimi üzerindeki etki |
|:---:|:--- |
| Eklenmemiş |Bu durumda, Azure AD dizinine contoso.com için özel etki alanı eklenmedi. Soneki @contoso.com olan şirket içi UPN 'si olan kullanıcılar, Azure 'da oturum açmak için şirket içi UPN 'sini kullanamaz. Bunun yerine, varsayılan Azure AD dizini için son ek eklenerek Azure AD tarafından sunulan yeni bir UPN kullanması gerekir. Örneğin, kullanıcıları Azure ad dizini azurecontoso.onmicrosoft.com ile eşityorsanız şirket içi kullanıcıya user@contoso.com bir UPN 'si user@azurecontoso.onmicrosoft.comverilir. |
| Doğrulanmadı |Bu durumda, Azure AD dizinine eklenen özel bir etki alanı contoso.com vardır. Ancak henüz doğrulanmamıştır. Etki alanını doğrulamadan kullanıcıları eşitlemeye devam ederseniz, kullanıcılara tıpkı "eklenmemiş" senaryosunda olduğu gibi Azure AD tarafından yeni bir UPN atanır. |
| Doğrulandı |Bu durumda, UPN soneki için Azure AD 'de zaten eklenmiş ve doğrulanan özel bir etki alanı contoso.com vardır. Kullanıcılar şirket içi Kullanıcı asıl adını (örneğin user@contoso.com, Azure AD ile eşitlendikten sonra Azure 'da oturum açmak) kullanabilecek. |

###### <a name="ad-fs-federation"></a>AD FS Federasyonu
Azure AD 'de default. onmicrosoft.com etki alanı veya Azure AD 'de doğrulanmamış bir özel etki alanı ile bir Federasyon oluşturamazsınız. Azure AD Connect sihirbazını çalıştırırken, ile bir federasyon oluşturmak için doğrulanmamış bir etki alanını seçerseniz, Azure AD Connect DNS 'nizin etki alanı için barındırıldığı gerekli kayıtları ister. Daha fazla bilgi için bkz. [Federasyon için seçili Azure AD etki alanını doğrulama](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

**AD FS ile**Kullanıcı oturum açma seçeneğini belirlediyseniz, Azure AD 'de bir Federasyon oluşturmaya devam etmek için özel bir etki alanına sahip olmanız gerekir. Tartışmamız için bu, Azure AD dizininde özel bir etki alanı contoso.com eklenmiş olması gerektiği anlamına gelir.

| Durum | Kullanıcı Azure oturum açma deneyiminde etki |
|:---:|:--- |
| Eklenmemiş |Bu durumda Azure AD Connect Azure AD dizininde UPN soneki contoso.com için eşleşen bir özel etki alanı bulmadı. Kullanıcıların şirket içi UPN (gibi user@contoso.com) ile AD FS kullanarak oturum açmasını gerekiyorsa, özel bir etki alanı contoso.com eklemeniz gerekir. |
| Doğrulanmadı |Bu durumda Azure AD Connect, etki alanınızı daha sonraki bir aşamada nasıl doğrulayabildiğinizi öğrenmek için sizden uygun ayrıntıları ister. |
| Doğrulandı |Bu durumda, başka bir eylem yapmadan yapılandırma ile devam edebilirsiniz. |

## <a name="changing-the-user-sign-in-method"></a>Kullanıcı oturum açma yöntemini değiştirme
Sihirbazla Azure AD Connect ilk yapılandırmasından sonra Azure AD Connect ' de bulunan görevleri kullanarak, Kullanıcı oturum açma yöntemini Federasyon, Parola karması eşitleme veya doğrudan kimlik doğrulamasından dönüştürebilirsiniz. Azure AD Connect Sihirbazı 'nı yeniden çalıştırın ve gerçekleştirebileceğiniz görevlerin bir listesini görürsünüz. Görev listesinden **Kullanıcı oturum açmayı Değiştir '** i seçin.

![Kullanıcı oturumunu değiştir](./media/plan-connect-user-signin/changeusersignin.png)

Sonraki sayfada Azure AD kimlik bilgilerini sağlamanız istenir.

![Azure AD'ye bağlan](./media/plan-connect-user-signin/changeusersignin2.png)

**Kullanıcı oturum açma** sayfasında, istenen kullanıcı oturumunu seçin.

![Azure AD'ye bağlan](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Parola karması eşitlemeye yalnızca geçici bir anahtar oluşturuyorsanız, **Kullanıcı hesaplarını dönüştürmeyin** onay kutusunu seçin. Seçeneğinin işaretlenmesi, her bir kullanıcıyı federe 'a dönüştürecek ve bu işlem birkaç saat sürebilir.
>
>

## <a name="next-steps"></a>Sonraki adımlar
- [Şirket içi kimliklerinizi Azure Active Directory tümleştirme](whatis-hybrid-identity.md)hakkında daha fazla bilgi edinin.
- [Azure AD Connect tasarım kavramları](plan-connect-design-concepts.md)hakkında daha fazla bilgi edinin.
