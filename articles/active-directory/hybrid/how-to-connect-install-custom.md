---
title: 'Azure AD Connect: Özel yükleme | Microsoft Belgeleri'
description: Bu belgede Azure AD Connect için özel yükleme seçenekleri ayrıntılı olarak açıklanmaktadır. Active Directory'yi Azure AD Connect aracılığı ile yüklemek için bu yönergeleri kullanın.
services: active-directory
keywords: Azure AD Connect nedir, Active Directory yükleme, Azure AD için gerekli bileşenler
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/14/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80b90a22a793c15104bba3eb91e88f851158e13f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106951"
---
# <a name="custom-installation-of-azure-ad-connect"></a>Azure AD Connect özel yüklemesi
Yükleme için daha fazla seçenek istediğinizde Azure AD Connect **Özel ayarları** kullanılır. Birden fazla ormanınız varsa veya hızlı yükleme kapsamında yer almayan isteğe bağlı özellikleri yapılandırmak istiyorsanız kullanılır. [**Hızlı yükleme**](how-to-connect-install-express.md) seçeneğinin dağıtımınız veya topolojiniz için uygun olmadığı tüm durumlarda kullanılır.

Azure AD Connect'i yüklemeye başlamadan önce [Azure AD Connect'i indirdiğinizden](https://go.microsoft.com/fwlink/?LinkId=615771) ve [Azure AD Connect: Donanım ve önkoşullar](how-to-connect-install-prerequisites.md) bölümündeki önkoşul adımlarını tamamladığınızdan emin olun. Ayrıca [Azure AD Connect hesapları ve izinleri](reference-connect-accounts-permissions.md) bölümünde açıklandığı üzere, gerekli hesaplara sahip olduğunuzdan olduğundan emin olun .

Özelleştirilmiş ayarlar topolojinizle eşleşmiyorsa (örneğin DirSync 'i yükseltmek için), diğer senaryolar için ilgili belgelere bakın.

## <a name="custom-settings-installation-of-azure-ad-connect"></a>Azure AD Connect özel ayarlarını yükleme
### <a name="express-settings"></a>Hızlı Ayarlar
Özelleştirilmiş ayarlar yüklemeyi başlatmak için bu sayfada **Özelleştir**'e tıklayın.

### <a name="install-required-components"></a>Gerekli bileşenleri yükleme
Eşitleme hizmetlerini yüklerken isteğe bağlı yapılandırma bölümünü işaretlenmemiş olarak bırakabilirsiniz. Bu durumda, Azure AD Connect her şeyi otomatik olarak ayarlar. SQL Server 2012 Express LocalDB örneğini ayarlar, uygun gruplar oluşturur ve izinleri atar. Varsayılanları değiştirmek isterseniz var olan isteğe bağlı yapılandırma seçeneklerini anlamak üzere aşağıdaki tabloya bakabilirsiniz.

![Gerekli Bileşenler](./media/how-to-connect-install-custom/requiredcomponents2.png)

| İsteğe Bağlı Yapılandırma | Açıklama |
| --- | --- |
| Mevcut bir SQL Server'ı kullanma |SQL Server adını ve örnek adını belirtebilirsiniz. Kullanmak istediğiniz bir veritabanı sunucusu zaten varsa bu seçeneği belirleyin. SQL Server'ınızda gözatma özelliği etkin değilse **Örnek Adı** alanına örnek adını girin, virgül ekleyin ve bağlantı noktası numarasını girin.  Sonra Azure AD Connect veritabanının adını belirtin.  SQL ayrıcalıklarınız, yeni bir veritabanının oluşturulup oluşturulmayacağını veya SQL yöneticinizin veritabanını önceden oluşturması gerektiğini belirtir.  SQL SA izinleriniz varsa, bkz. [var olan bir veritabanını kullanarak nasıl yükleneceğini öğrenin](how-to-connect-install-existing-database.md).  Temsilci izinleri (DBO) varsa bkz. [ınstall Azure AD Connect for SQL yetkilendirilmiş yönetici izinleri](how-to-connect-install-sql-delegation.md). |
| Mevcut bir hizmet hesabını kullanma |Varsayılan olarak Azure AD Connect, eşitleme hizmetleri tarafından kullanılmak üzere sanal bir hizmet hesabı kullanır. Kimlik doğrulaması gerektiren bir ara sunucu veya uzak bir SQL sunucusu kullanıyorsanız **yönetilen bir hizmet hesabı** kullanmanız veya etki alanında bir hizmet kullanıp parolayı biliyor olmanız gerekir. Bu gibi durumlarda kullanılacak olan hesabı girin. Hizmet hesabı için oturum açma seçeneğinin oluşturulabilmesi için, yüklemeyi çalıştıran kullanıcının SQL'de bir Sistem Yöneticisi olduğundan emin olun.  Bkz. [Azure AD Connect hesapları ve izinleri](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>En son sürümle, veritabanını sağlama, artık SQL yöneticisi tarafından bant dışında gerçekleştirilebilir ve ardından veritabanı sahibi haklarıyla Azure AD Connect yöneticisi tarafından yüklenebilir.  Daha fazla bilgi için bkz. [SQL yönetici temsilcisi izinlerini kullanarak Azure AD Connect'i yükleme](how-to-connect-install-sql-delegation.md).|
| Özel eşitleme grubu belirtme |Eşitleme hizmetleri yüklendiğinde Azure AD Connect varsayılan olarak sunucu için dört yerel grup oluşturur. Bunlar Yöneticiler grubu, İşleçler grubu, Gözatma grubu ve Parola Sıfırlama Grubudur. Kendi gruplarınızı burada belirtebilirsiniz. Gruplar sunucuda yerel olmalıdır ve etki alanında bulunamazlar. |

### <a name="user-sign-in"></a>Kullanıcı oturumu açma
Gerekli bileşenleri yükledikten sonra kullanıcı çoklu oturumu açma yönteminizi seçmeniz istenir. Aşağıdaki tabloda mevcut seçeneklerle ilgili kısa bir açıklama bulunmaktadır. Oturum açma yöntemleriyle ilgili tam açıklama için bkz. [Kullanıcı oturumu açma](plan-connect-user-signin.md).

![Kullanıcı Oturumu açma](./media/how-to-connect-install-custom/usersignin4.png)

| Çoklu Oturum Açma Seçeneği | Açıklama |
| --- | --- |
| Parola Karması Eşitleme |Kullanıcılar, Office 365 gibi Microsoft bulut hizmetlerinde kendi şirket içi ağlarında kullandıkları parolayla oturum açabilir. Kullanıcı parolaları, parola karması olarak Azure AD ile eşitlenir ve bulutta bir kimlik doğrulaması gerçekleşir. Daha fazla bilgi için bkz. [Parola karması eşitleme](how-to-connect-password-hash-synchronization.md). |
|Doğrudan Kimlik Doğrulama|Kullanıcılar, Office 365 gibi Microsoft bulut hizmetlerinde kendi şirket içi ağlarında kullandıkları parolayla oturum açabilir.  Kullanıcının parolası, doğrulanmak üzere şirket içi Active Directory etki alanı denetleyicisine geçirilir.
| AD FS ile Federasyon |Kullanıcılar, Office 365 gibi Microsoft bulut hizmetlerinde kendi şirket içi ağlarında kullandıkları parolayla oturum açabilir.  Kullanıcılar oturum açmak üzere kendi şirket içi AD FS örneklerine yönlendirilir ve şirket içi kimlik doğrulaması gerçekleşir. |
| PingFederate ile federasyon|Kullanıcılar, Office 365 gibi Microsoft bulut hizmetlerinde kendi şirket içi ağlarında kullandıkları parolayla oturum açabilir.  Kullanıcılar oturum açmak üzere kendi şirket içi PingFederate örneklerine yönlendirilir ve şirket içi kimlik doğrulaması gerçekleşir. |
| Yapılandırmayın |Kullanıcı oturum açma özelliği yüklenmez ve yapılandırılmaz. Zaten 3. taraf bir federasyon sunucunuz varsa veya başka bir çözümden faydalanıyorsanız bu seçeneği belirleyin. |
|Çoklu Oturum Açmayı Etkinleştir|Bu seçenek hem parola karması eşitleme hem de doğrudan kimlik doğrulaması ile kullanılabilir ve masaüstü kullanıcılarına kurumsal ağda çoklu oturum açma deneyimi sağlar. Daha fazla bilgi için bkz. [Çoklu oturum açma](how-to-connect-sso.md). </br>AD FS zaten aynı düzeyde çoklu oturum açma olanağı sağladığından, AD FS müşterilerinin bu seçeneği kullanamayacağı unutulmamalıdır.</br>

### <a name="connect-to-azure-ad"></a>Azure AD'ye Bağlanma
Azure AD'ye Bağlanma ekranında, genel yönetici hesabı ve parolasını girin. Önceki sayfada **AD FS ile Federasyon** seçeneğini belirlediyseniz etki alanında federasyon için etkinleştirmeyi düşündüğünüz bir hesap ile oturum açmayın. Azure AD kiracınızla sunulan, varsayılan **onmicrosoft.com** etki alanındaki bir hesabı kullanmanız önerilir.

Bu hesap yalnızca Azure AD'de hizmet hesabı oluşturmak için kullanılır ve sihirbaz tamamlandıktan sonra kullanılmaz.  
![Kullanıcı Oturumu açma](./media/how-to-connect-install-custom/connectaad.png)

Genel yönetici hesabınızda MFA etkinse açılır oturum açma penceresine parolayı tekrar girmeniz ve MFA testini tamamlamanız gerekir. Test için bir doğrulama kodu sağlanır veya telefon görüşmesi yapılır.  
![MFA’da kullanıcı Oturumu açma](./media/how-to-connect-install-custom/connectaadmfa.png)

Genel yönetici hesabında [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) seçeneği de etkin olabilir.

Bir hatayla karşılaştıysanız ve bağlantı sorunlarınız varsa bkz. [Bağlantı sorunlarını giderme](tshoot-connect-connectivity.md).

## <a name="pages-under-the-sync-section"></a>Eşitleme bölümünde yer alan sayfalar

### <a name="connect-your-directories"></a>Dizinlerinizi bağlama
Azure AD Connect'in, Active Directory Etki Alanı Hizmetinize bağlanabilmesi için yeterli izinlere sahip bir hesabın orman adı ve kimlik bilgilerine sahip olması gerekir.

![Connect Dizini](./media/how-to-connect-install-custom/connectdir01.png)

Orman adını girip **Dizin Ekle**’ye tıkladıktan sonra, bir iletişim kutusu açılır ve aşağıdaki seçenekler sunulur:

| Seçenek | Açıklama |
| --- | --- |
| Yeni hesap oluştur | Dizin eşitlemesi sırasında Azure AD Connect’in AD ormanına bağlanması için gereken AD DS hesabını Azure AD Connect sihirbazının oluşturmasını istiyorsanız bu seçeneği belirleyin. Bu seçenek belirlendiğinde, bir kuruluş yöneticisi hesabının kullanıcı adını ve parolasını girmeniz gerekir. Sağlanan kuruluş yöneticisi hesabı, Azure AD Connect sihirbazı tarafından gerekli AD DS hesabını oluşturmak için kullanılır. Etki alanı bölümünü NetBIOS veya FQDN biçiminde (örneğin, FABRIKAM\yönetici veya fabrikam.com\yönetici) girebilirsiniz. |
| Mevcut hesabı kullan | Dizin eşitlemesi sırasında Azure AD Connect tarafından AD ormanına bağlanmak için kullanılmak üzere mevcut bir AD DS hesabı sağlamak istiyorsanız bu seçeneği belirleyin. Etki alanı bölümünü NetBIOS veya FQDN biçiminde (ör. FABRIKAM\eşitlemekullanıcısı veya fabrikam.com\eşitlemekullanıcısı) girebilirsiniz. Yalnızca varsayılan okuma izinleri gerekli olduğundan, bu hesap normal bir kullanıcı hesabı olabilir. Ancak senaryonuza bağlı olarak daha fazla izin gerekebilir. Daha fazla bilgi için bkz. [Azure AD Connect Hesapları ve izinleri](reference-connect-accounts-permissions.md##create-the-ad-ds-connector-account). |

![Connect Dizini](./media/how-to-connect-install-custom/connectdir02.png)

#### <a name="enterprise-admin-and-domain-admin-accounts-not-supported"></a>Kurumsal Yönetici ve etki alanı yönetici hesapları desteklenmiyor
Derleme 1.4.18.0 itibariyle, artık AD DS bağlayıcı hesabı olarak bir kurumsal yönetici veya etki alanı yöneticisi hesabı kullanmak için desteklenmez.  **Mevcut hesabı kullan**' ı belirtirken Kurumsal Yönetici veya etki alanı yöneticisi olan bir hesap girmeyi denerseniz, şu hatayı alırsınız:

  **"AD Ormanı hesabınız için bir kuruluş veya etki alanı yönetici hesabı kullanılmasına izin verilmiyor.  Hesabı sizin için Azure AD Connect oluşturmaya veya doğru izinlere sahip bir eşitleme hesabı belirtmenize izin verin.  Daha fazla bilgi &lt;&gt;"**

### <a name="azure-ad-sign-in-configuration"></a>Azure AD oturum açma yapılandırması
Bu sayfa, Azure AD'de doğrulanmış olup şirket içi AD DS'de var olan UPN etki alanlarını gözden geçirmenize olanak sağlar. Ayrıca bu sayfa sayesinde userPrincipalName için kullanılacak özniteliği yapılandırabilirsiniz.

![Doğrulanmamış etki alanları](./media/how-to-connect-install-custom/aadsigninconfig2.png)  
**Eklenmedi** ve **Doğrulanmadı** olarak işaretlenen tüm etki alanlarını gözden geçirin. Kullandığınız etki alanlarının Azure AD'de doğrulanmış olduğundan emin olun. Etki alanlarınızı doğruladıktan sonra Yenile simgesine tıklayın. Daha fazla bilgi için bkz. [etki alanı ekleme ve doğrulama](../active-directory-domains-add-azure-portal.md)

**UserPrincipalName** - userPrincipalName özniteliği, kullanıcıların Azure AD'de ve Office 365'te oturum açarken kullandıkları özniteliktir. Kullanıcılar eşitlenmeden önce, UPN soneki olarak da bilinen kullanılan etki alanlarının Azure AD'de doğrulanması gerekir. Microsoft, userPrincipalName varsayılan özniteliğinin tutulmasını önerir. Bu öznitelik yönlendirilemeyen bir öznitelikse ve doğrulanamazsa başka bir öznitelik seçebilirsiniz. Örneğin, oturum açma kimliğinin bulunduğu öznitelik olarak e-postayı seçin. userPrincipalName dışında başka bir özniteliğin kullanılmasına **Alternatif kimlik** adı verilir. Alternatif kimlik öznitelik değeri, RFC822 standardına uygun olmalıdır. Alternatif bir kimlik parola karma eşitlemesi, doğrudan kimlik doğrulaması ve federasyon ile kullanılabilir. Öznitelik, tek bir değere sahip olsa bile, Active Directory'de birden çok değerli olarak tanımlanmamalıdır. Alternatif KIMLIK hakkında daha fazla bilgi için [lütfen buraya tıklayın.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname)

>[!NOTE]
> Doğrudan Kimlik Doğrulama’yı etkinleştirdiğinizde, sihirbazda devam edebilmeniz için en az bir doğrulanmış etki alanına sahip olmanız gerekir.

> [!WARNING]
> Alternatif kimlik kullanımı, hiçbir Office 365 iş yükü ile uyumlu değildir. Daha fazla bilgi için [Alternatif Oturum Açma Kimliğini Yapılandırma](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) bölümüne bakın.
>
>

### <a name="domain-and-ou-filtering"></a>Etki alanı ve OU filtreleme
Varsayılan olarak tüm etki alanları ve OU'lar eşitlenir. Azure AD ile eşitlemek istemediğiniz etki alanları veya OU'lar varsa bu etki alanlarının veya OU'ların işaretini kaldırabilirsiniz.  
![DomainOU filtreleme](./media/how-to-connect-install-custom/domainoufiltering.png)  
Sihirbazın bu sayfası, etki alanı tabanlı ve OU tabanlı filtrelemeyi yapılandırmaya yöneliktir. Değişiklik yapmayı planlıyorsanız, yapmadan önce [etki alanı tabanlı filtreleme](how-to-connect-sync-configure-filtering.md#domain-based-filtering) ve [OU tabanlı filtreleme](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) konularını inceleyin. Bazı OU’lar işlevsellik açısından gereklidir ve bunların seçimi kaldırılmamalıdır.

Azure AD Connect’in 1.1.524.0’dan önceki bir sürümü ile OU tabanlı filtreleme kullanıyorsanız, daha sonra eklenen yeni OU’lar varsayılan olarak eşitlenir. Yeni OU'ların eşitlenmesini istemezseniz, sihirbazın [ou tabanlı filtreleme](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) yapılandırması tamamlandıktan sonra gerekli ayarları yapabilirsiniz. Azure AD Connect sürüm 1.1.524.0 ve üzeri sürümlerde, yeni OU’ların eşitlenmesini isteyip istemediğinizi belirtebilirsiniz.

[Grup tabanlı filtreleme](#sync-filtering-based-on-groups) kullanmayı planlıyorsanız, gruba sahip OU'nun dahil olduğundan ve OU filtreleme ile filtrelenmediğinden emin olun. OU filtreleme, grup tabanlı filtrelemeden önce değerlendirilir.

Güvenlik duvarı kısıtlamaları nedeniyle bazı etki alanlarına erişilemeyebilir. Bu etki alanları varsayılan olarak seçili değildir ve uyarı içerir.  
![Erişilemeyen etki alanları](./media/how-to-connect-install-custom/unreachable.png)  
Bu uyarıyı görürseniz bu etki alanlarına gerçekten erişilemediğinden ve bir uyarının beklendiğinden emin olun.

### <a name="uniquely-identifying-your-users"></a>Kullanıcılarınızı benzersiz olarak tanımlama

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Şirket içi dizinlerinizde kullanıcıların nasıl tanımlanması gerektiğini seçin
Ormanlar arasında eşleştirme özelliği sayesinde, AD DS ormanlarındaki kullanıcıların Azure AD'de nasıl temsil edildiğini tanımlayabilirsiniz. Bir kullanıcı ya tüm ormanlarda yalnızca bir kez temsil edilebilir ya da etkin ve devre dışı hesapların birleşimine sahip olabilir. Ayrıca kullanıcı, bazı ormanlarda kişi olarak da temsil edilebilir.

![Benzersiz](./media/how-to-connect-install-custom/unique2.png)

| Ayar | Açıklama |
| --- | --- |
| [Kullanıcılar tüm ormanlarda yalnızca bir kez temsil edilir](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Tüm kullanıcılar Azure AD'de bireysel nesne olarak oluşturulur. Nesneler meta veri deposunda birleştirilmez. |
| [Posta özniteliği](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Bu seçenek, posta özniteliğinin farklı ormanlarda aynı değere sahip olması halinde kullanıcıları ve kişileri birleştirir. Kişileriniz GALSync kullanılarak oluşturulduysa bu seçeneği kullanın. Bu seçenek belirtildiyse, Posta özniteliği doldurulmamış olan Kullanıcı nesneleri Azure AD'ye eşitlenmez. |
| [ObjectSID ve msExchangeMasterAccountSID/ msRTCSIP-OriginatorSid](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Bu seçenek, hesap ormanındaki etkin bir kullanıcıyla kaynak ormandaki devre dışı bırakılmış bir kullanıcıyı birleştirir. Bu yapılandırma, Exchange'de bağlı posta kutusu olarak bilinir. Yalnızca Lync'i kullanıyor olmanız ve kaynak ormanda Exchange olmaması halinde de bu seçeneği kullanabilirsiniz. |
| sAMAccountName ve MailNickName |Bu seçenek, kullanıcı için oturum açma kimliğinin bulunması beklenen öznitelikleri birleştirir. |
| Belirli bir öznitelik |Bu seçenek, kendi özniteliğinizi seçmenize olanak tanır. Bu seçenek belirtildiyse, (seçili) özniteliği doldurulmamış olan Kullanıcı nesneleri Azure AD'ye eşitlenmez. **Sınırlama:** Meta veri deposunda bulabileceğiniz bir özniteliği seçtiğinizden emin olun. Özel bir öznitelik (meta veri deposunda olmayan) seçerseniz sihirbaz tamamlanamaz. |

#### <a name="select-how-users-should-be-identified-with-azure-ad---source-anchor"></a>Azure AD - Kaynak Bağlantısı ile kullanıcıların nasıl tanımlanması gerektiğini seçin
SourceAnchor özniteliği, kullanıcı nesnesinin yaşam süresi boyunca sabit olan bir özniteliktir. Şirket içi kullanıcıyı Azure AD'deki kullanıcıya bağlayan birincil anahtardır.

| Ayar | Açıklama |
| --- | --- |
| Kaynak bağlantısını benim için Azure yönetsin | Azure AD’nin sizin için özniteliği seçmesini istiyorsanız bu seçeneği belirleyin. Bu seçeneği belirlerseniz Azure AD Connect sihirbazı, [Azure AD Connect: Tasarım kavramları - ms-DS-ConsistencyGuid'i sourceAnchor olarak kullanma](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) başlıklı makale bölümünde açıklanan sourceAnchor özniteliği seçim mantığını uygular. Özel yükleme tamamlandıktan sonra sihirbaz, Kaynak Bağlantısı özniteliği olarak hangi özniteliğin seçildiğini size bildirir. |
| Belirli bir öznitelik | SourceAnchor özniteliği olarak mevcut bir AD özniteliğini belirtmek istiyorsanız bu seçeneği belirleyin. |

Öznitelik değiştirilemeyeceği için kullanmak üzere iyi bir öznitelik seçmeniz gerekir. ObjectGUID iyi bir seçenektir. Kullanıcı hesabı ormanlar/etki alanları arasında taşınmadığı sürece bu öznitelik değiştirilemez. Bir kişi evlendiğinde değişecek olan veya atamaları değiştirecek olan öznitelikleri kullanmaktan kaçının. @-sign içeren nitelikleri kullanamazsınız. Bu nedenle e-posta ve userPrincipalName seçeneği kullanılamaz. Ayrıca öznitelikler büyük küçük harfe duyarlıdır. Bu nedenle bir nesneyi ormanlar arasında taşıdığınızda, büyük/küçük harfleri doğru yazdığınızdan emin olun. İkili öznitelikler base64 kodludur ancak diğer öznitelik türleri kodlanmamış durumda kalır. Federasyon senaryolarında ve bazı Azure AD arabirimlerinde, bu öznitelik immutableID özniteliği olarak da bilinir. Kaynak bağlantısı hakkında daha fazla bilgi için bkz. [tasarım kavramları](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Grup tabanlı eşitleme filtrelemesi
Grup filtreleme özelliği, pilot için yalnızca küçük bir nesne alt kümesini eşitlemenize olanak sağlar. Bu özelliği kullanmak için şirket içi Active Directory'nizde bu amaca uygun bir grup oluşturun. Ardından, doğrudan üye olarak Azure AD ile eşitlenecek kullanıcıları ve grupları ekleyin. Daha sonra, Azure AD'de mevcut olması gereken nesnelerin listesini korumak için bu gruba kullanıcı ekleyebilir ve gruptan kullanıcı çıkarabilirsiniz. Eşitlemek istediğiniz tüm nesneler grubun doğrudan üyesi olmalıdır. Tüm kullanıcılar, gruplar, kişiler ve bilgisayarlar/cihazlar doğrudan üye olmalıdır. İç içe geçmiş grup üyelikleri çözümlenmez. Bir grubu üye olarak eklediğinizde, yalnızca grubun kendisi eklenir; üyeleri eklenmez.

![Eşitleme Filtreleme](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Bu özellik yalnızca pilot dağıtımı desteklemek üzere tasarlanmıştır. Bu özelliği tam gelişmiş üretim dağıtımında kullanmayın.
>
>

Tam gelişmiş üretim dağıtımında, tüm nesneleri eşitlenecek olan tek bir grubu kullanmak zordur. Bunun yerine, [Filtreleme yapılandırma](how-to-connect-sync-configure-filtering.md) bölümünde belirtilen yöntemlerden birini kullanın.

### <a name="optional-features"></a>İsteğe Bağlı Özellikler
Bu ekran, belirli senaryolarınız için isteğe bağlı özellikler seçmenizi sağlar.

>[!WARNING]
>Azure AD Connect sürüm **1.0.8641.0** ve öncesi, parola geri yazma özelliği için Azure Access Control Service kullanır.  Bu hizmet, **7 Kasım 2018** tarihinde kullanımdan kaldırılacaktır.  Bu Azure AD Connect sürümlerinden birini kullanıyorsanız ve parola geri yazma özelliğini etkinleştirdiyseniz, hizmet kullanımdan kaldırıldıktan sonra kullanıcılar parolalarını değiştirme veya sıfırlama olanağını kaybedebilir. Bu Azure AD Connect sürümleriyle parola geri yazma özelliği desteklenmeyecektir.
>
>Azure Access Control Service hakkında daha fazla bilgi için bkz. [Nasıl yapılır: Azure Access Control Service'ten geçiş](../develop/active-directory-acs-migration.md)
>
>Azure AD Connect'in en son sürümünü indirmek için [buraya](https://www.microsoft.com/en-us/download/details.aspx?id=47594) tıklayın.

![İsteğe bağlı özellikler](./media/how-to-connect-install-custom/optional2.png)

> [!WARNING]
> Şu anda DirSync veya Azure AD Eşitleme etkinse Azure AD Connect'te geri yazma özelliklerinden herhangi birini etkinleştirmeyin.



| İsteğe Bağlı Özellikler | Açıklama |
| --- | --- |
| Exchange Karma Dağıtımı |Exchange Karma Dağıtımı özelliği, Exchange posta kutularının hem şirket içinde hem de Office 365'te aynı anda var olmalarına olanak sağlar. Azure AD Connect, Azure AD'den belirli bir [öznitelikler](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) kümesini şirket içi dizininize geri eşitler. |
| Exchange Posta Ortak Klasörleri | Exchange Posta Ortak Klasörleri özelliğini kullanarak, posta özellikli Ortak Klasör nesnelerini şirket içi Active Directory’den Azure AD’ye eşitleyebilirsiniz. |
| Azure AD uygulaması ve öznitelik filtreleme |Azure AD uygulaması ve öznitelik filtreleme etkinleştirilerek, eşitlenen öznitelikler kümesi uyarlanabilir. Bu seçenek sihirbaza iki yapılandırma sayfası daha ekler. Daha fazla bilgi için bkz. [Azure AD uygulaması ve öznitelik filtreleme](#azure-ad-app-and-attribute-filtering). |
| Parola karması eşitleme |Oturum açma çözümü olarak federasyonu seçtiyseniz bu seçeneği etkinleştirebilirsiniz. Bu durumda parola karması eşitleme, bir yedekleme seçeneği olarak kullanılabilir. Ek bilgi için bkz. [Parola karması eşitleme](how-to-connect-password-hash-synchronization.md). </br></br>Doğrudan Kimlik Doğrulama’yı seçtiyseniz bu seçenek, eski istemcilere yönelik destek sağlanması ve yedek bir seçenek olarak kullanılması için etkinleştirilebilir. Ek bilgi için bkz. [Parola karması eşitleme](how-to-connect-password-hash-synchronization.md).|
| Parola geri yazma |Parola geri yazma etkinleştirildiğinde Azure AD'de gerçekleşen parola değişiklikleri şirket içi dizininize geri yazılır. Daha fazla bilgi için bkz. [Parola yönetimine başlarken](../authentication/quickstart-sspr.md). |
| Grup geri yazma |**Office 365 Grupları** özelliğini kullanıyorsanız bu gruplar şirket içi Active Directory'nizde de temsil edilir. Bu seçenek yalnızca şirket içi Active Directory'nizde Exchange varsa kullanılır. Daha fazla bilgi için bkz. [Grup geri yazma](how-to-connect-preview.md#group-writeback). |
| Cihaz geri yazma |Azure AD 'deki cihaz nesnelerini, koşullu erişim senaryolarında şirket içi Active Directory geri almanıza olanak sağlar. Daha fazla bilgi için bkz. [Azure AD Connect'te cihaz geri yazma özelliğini etkinleştirme](how-to-connect-device-writeback.md). |
| Dizin genişletme öznitelik eşitlemesi |Dizin genişletme öznitelik eşitlemesi etkinleştirildiğinde, belirtilen öznitelikler Azure AD ile eşitlenir. Daha fazla bilgi için bkz. [Dizin genişletmeleri](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD uygulaması ve öznitelik filtreleme
Hangi özniteliklerin Azure AD ile eşitleneceğini sınırlamak istiyorsanız kullanmakta olduğunuz hizmetleri seçerek başlatın. Bu sayfada yapılandırma değişikliği yaparsanız yükleme sihirbazını yeniden çalıştırarak doğrudan yeni bir hizmet seçmeniz gerekir.

![İsteğe bağlı özellikler Uygulamalar](./media/how-to-connect-install-custom/azureadapps2.png)

Bu sayfa, önceki adımda seçtiğiniz hizmetlere bağlı olarak eşitlenen tüm öznitelikleri gösterir. Bu liste, eşitlenmekte olan tüm nesne türlerinin birleşimidir. Eşitlememeniz gereken bazı öznitelikler varsa bunların seçimlerini kaldırın.

![İsteğe bağlı özellikler Öznitelikler](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Özniteliklerin kaldırılması, işlevselliği etkileyebilir. En iyi yöntemler ve öneriler için bkz. [eşitlenen öznitelikler](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>
>

### <a name="directory-extension-attribute-sync"></a>Dizin Genişletme öznitelik eşitlemesi
Kuruluşunuz tarafından eklenen özel öznitelikler veya Active Directory'deki diğer özniteliklerle Azure AD'deki şemayı genişletebilirsiniz. Bu özelliği kullanmak için **İsteğe Bağlı Özellikler** sayfasındaki **Dizin Genişletme öznitelik eşitlemesi** öğesini seçin. Bu sayfada eşitlemek için daha fazla öznitelik seçebilirsiniz.

>[!NOTE]
>Kullanılabilir öznitelikler kutusu büyük/küçük harfe duyarlıdır.

![Dizin genişletmeleri](./media/how-to-connect-install-custom/extension2.png)

Daha fazla bilgi için bkz. [Dizin genişletmeleri](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on-sso"></a>Çoklu oturum açmayı (SSO) etkinleştirme
Parola Eşitleme veya Doğrudan kimlik doğrulama ile birlikte kullanılmak üzere çoklu oturum açma özelliğinin yapılandırılması, Azure AD ile eşitlenen her ormanda bir kere tamamlamanız gereken basit bir işlemdir. Yapılandırma aşağıdaki iki adımdan oluşur:

1.  Şirket içi Active Directory'nizde gerekli bilgisayar hesabını oluşturma.
2.  İstemci makinelerin intranet bölgesini çoklu oturum açmayı destekleyecek şekilde yapılandırma.

#### <a name="create-the-computer-account-in-active-directory"></a>Active Directory'de bilgisayar hesabını oluşturma
Azure AD Connect'e bağlanan tüm ormanlarda bilgisayar hesabının oluşturulabilmesi için, her bir ormanda Etki Alanı Yöneticisi kimlik bilgilerini sağlamanız gerekir. Kimlik bilgileri yalnızca hesabı oluşturmak için kullanılır ve depolanmaz ya da başka bir işlem için kullanılmaz. Kimlik bilgisini Azure AD Connect sihirbazının **Çoklu oturum açmayı etkinleştirme** sayfasına eklemeniz gereklidir:

![Çoklu oturum açmayı etkinleştirme](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Belirli bir ormanda Çoklu oturum açma kullanmak istemiyorsanız o ormanı atlayabilirsiniz.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>İstemci makineler için Intranet Bölgesini yapılandırma
İstemcinin intranet bölgesinde otomatik olarak oturum açmasını sağlamak için, URL 'nin intranet bölgesinin bir parçası olduğundan emin olmanız gerekir. Bunun yapılması, etki alanına katılan bilgisayarın kurumsal ağa bağlandığında Azure AD'ye otomatik olarak bir Kerberos anahtarı göndermesini sağlar.
Grup İlkesi yönetim araçlarına sahip bir bilgisayarda.

1.  Grup İlkesi Yönetimi araçlarını açın
2.  Tüm kullanıcılara uygulanacak Grup ilkesini düzenleyin. Örneğin, Varsayılan Etki Alanı İlkesi.
3.  **User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security Page** bölümüne gidin ve aşağıdaki şekilde gösterildiği gibi **Siteden Bölgeye Atama Listesi**'ni seçin.
4.  İlkeyi etkinleştirin ve iletişim kutusuna aşağıdaki öğeyi girin.

        Value: `https://autologon.microsoftazuread-sso.com`  
        Data: 1  


5.  Şunun gibi görünmelidir:  
![Intranet Bölgeleri](./media/how-to-connect-install-custom/sitezone.png)

6.  İki kez **Tamam**'a tıklayın.

## <a name="configuring-federation-with-ad-fs"></a>AD FS ile federasyonu yapılandırma
Azure AD Connect ile AD FS'yi yalnızca birkaç tıklama ile kolayca yapılandırabilirsiniz. Yapılandırma için aşağıdakiler gereklidir.

* Federasyon sunucusu için uzaktan yönetimi etkinleştirilmiş bir Windows Server 2012 R2 veya üzeri sunucu
* Web Uygulaması Ara Sunucusu için uzaktan yönetimi etkinleştirilmiş bir Windows Server 2012 R2 veya üzeri sunucu
* Kullanmayı düşündüğünüz federasyon hizmeti adı (örneğin, sts.contoso.com) için bir SSL sertifikası

>[!NOTE]
>Azure AD Connect bileşenini federasyon güveninizi yönetmek için kullanmıyor olsanız dahi AD FS grubunuzun SSL sertifikasını güncelleştirmek için kullanabilirsiniz.

### <a name="ad-fs-configuration-pre-requisites"></a>AD FS yapılandırması önkoşulları
Azure AD Connect'i kullanarak AD FS grubunuzu yapılandırmak için uzak sunucularda WinRM'nin etkinleştirildiğinden emin olun. [Federasyon önkoşulları](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration) bölümündeki diğer görevleri tamamladığınızdan emin olun. Ayrıca, [Tablo 3 - Azure AD Connect ve Federasyon Sunucuları/WAP](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) bölümünde listelenen bağlantı noktaları gereksinimlerini inceleyin.

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Yeni bir AD FS grubu oluşturma veya var olan bir AD FS grubunu kullanma
Var olan bir AD FS grubunu kullanabilir veya yeni bir AD FS grubu oluşturmayı seçebilirsiniz. Yeni bir grup oluşturmayı seçerseniz SSL sertifikası sağlamanız gerekir. SSL sertifikası bir parolayla korunuyorsa parolayı girmeniz istenir.

![AD FS Grubu](./media/how-to-connect-install-custom/adfs1.png)

Var olan bir AD FS grubunu kullanmayı seçerseniz doğrudan AD FS ile Azure AD arasındaki güven ilişkisini yapılandırma ekranına gidersiniz.

>[!NOTE]
>Azure AD Connect tek bir AD FS grubunu yönetmek için kullanılabilir. Seçilen AD FS grubunda yapılandırılmış Azure AD federasyon güveni varsa bu güven Azure AD Connect tarafından sıfırdan yeniden oluşturulur.

### <a name="specify-the-ad-fs-servers"></a>AD FS sunucularını belirtme
AD FS'yi yüklemek istediğiniz sunucuları girin. Kapasite planlama gereksinimlerinize göre bir veya daha fazla sunucu ekleyebilirsiniz. Bu yapılandırmayı gerçekleştirmeden önce tüm AD FS sunucularının (WAP sunucuları için gerekli değildir) Active Directory'ye katılmasını sağlayın. Microsoft, test ve pilot dağıtımlar için tek bir AD FS sunucusunun yüklenmesini önerir. Ardından, ölçeklendirme gereksinimlerinizi karşılamak için ilk yapılandırmadan sonra Azure AD Connect'i tekrar çalıştırarak daha fazla sunucu ekleyebilir ve dağıtabilirsiniz.

> [!NOTE]
> Bu yapılandırmayı geçekleştirmeden önce tüm sunucularınızın bir AD etki alanına katıldığından emin olun.
>
>

![AD FS Sunucuları](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Web Uygulaması Ara Sunucularını belirtme
Web Uygulaması ara sunucusu olarak kullanmak istediğiniz sunucuları girin. Web uygulaması ara sunucusu, çevre ağınızda (extranete yönelik) dağıtılır ve extranetten gelen kimlik doğrulama isteklerini destekler. Kapasite planlama gereksinimlerinize göre bir veya daha fazla sunucu ekleyebilirsiniz. Microsoft, test ve pilot dağıtımlar için tek bir Web uygulaması ara sunucusunun yüklenmesini önerir. Ardından, ölçeklendirme gereksinimlerinizi karşılamak için ilk yapılandırmadan sonra Azure AD Connect'i tekrar çalıştırarak daha fazla sunucu ekleyebilir ve dağıtabilirsiniz. İntranetten gelen kimlik doğrulamalarını gerçekleştirebilmek için, aynı sayıda ara sunucuya sahip olmanızı öneririz.

> [!NOTE]
> <li> Kullandığınız hesap WAP sunucularında yerel yönetici değilse yönetici kimlik bilgileri girmeniz istenir.</li>
> <li> Bu adımı gerçekleştirmeden önce Azure AD Connect sunucusu ve Web Uygulaması Ara Sunucusu arasında HTTP/HTTPS bağlantısının olduğundan emin olun.</li>
> <li> Doğrulama isteklerinin akışına izin vermek için, Web Uygulaması Sunucusu ve AD FS sunucusu arasında HTTP/HTTPS bağlantısının olduğundan emin olun.</li>
>

![Web Uygulaması](./media/how-to-connect-install-custom/adfs3.png)

Web uygulama sunucusunun AD FS sunucusu ile güvenli bir bağlantı kurması için kimlik bilgileri girmeniz istenir. Bu kimlik bilgilerinin AD FS sunucusunda yerel bir yöneticiye ait olması gerekir.

![Ara sunucu](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>AD FS hizmetine ilişkin hizmet hesabını belirtme
AD FS hizmetinin kullanıcıların kimliklerini doğrulayabilmesi ve Active Directory'de kullanıcı bilgilerini arayabilmesi için bir etki alanı hizmet hesabı gerekir. AD FS hizmeti, iki hizmet hesabı türünü destekler:

* **Grup Tarafından Yönetilen Hizmet Hesabı** - Windows Server 2012'de Active Directory Etki Alanı Hizmetleri ile birlikte kullanıma sunuldu. Bu hesap türü, AD FS (hesap parolasının düzenli olarak güncelleştirilmesi gerekmeyen tek bir hesap) gibi hizmetler sağlar AD FS sunucularınızın ait olduğu etki alanında Windows Server 2012 etki alanı denetleyicileriniz varsa bu seçeneği kullanın.
* **Etki Alanı Kullanıcı Hesabı** - Bu hesap türü için bir parola sağlamanız ve parola değiştiğinde veya süresi dolduğunda parolayı düzenli olarak güncelleştirmeniz gerekir. AD FS sunucularınızın ait olduğu etki alanında Windows Server 2012 etki alanı denetleyicileriniz yoksa bu seçeneği kullanın.

Grup Tarafından Yönetilen Hizmet Hesabı'nı seçtiyseniz ve bu özellik Active Directory'de hiç kullanılmadıysa Kuruluş Yöneticisi kimlik bilgilerini girmeniz istenir. Bu kimlik bilgileri, anahtar deposunu başlatmak ve Active Directory'de ilgili özelliği etkinleştirmek için kullanılır.

> [!NOTE]
> Azure AD Connect, AD FS hizmetinin etki alanındaki zaten bir SPN olarak kayıtlı olup olmadığını algılamak için bir denetim gerçekleştirir.  AD DS, yinelenen SPN’lerin aynı anda kaydedilmesine izin vermez.  Yinelenen SPN bulunursa, SPN kaldırılana kadar devam etmek mümkün olmaz.

![AD FS Hizmet Hesabı](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-wish-to-federate"></a>Birleştirmek istediğiniz Azure AD etki alanını seçin
Bu yapılandırma, AD FS ile Azure AD arasındaki federasyon ilişkisini ayarlamak için kullanılır. AD FS'yi, Azure AD'ye güvenlik belirteçleri sağlamak üzere yapılandırır. Ayrıca Azure AD'yi bu belirli AD FS örneğinden gelen belirteçlere güvenecek şekilde yapılandırır. Bu sayfa, ilk yüklemede yalnızca bir etki alanını yapılandırmanıza izin verir. Daha sonra Azure AD Connect'i tekrar çalıştırarak daha fazla etki alanını yapılandırabilirsiniz.

![Azure AD Etki Alanı](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Federasyon için seçilen Azure AD etki alanını doğrulama
Birleştirilecek etki alanını seçtiğinizde Azure AD Connect, size doğrulanmamış bir etki alanını doğrulamak için gerekli olan bilgileri sağlar. Bu bilgileri nasıl kullanacağınız hakkında bilgi edinmek için bkz. [Etki alanı ekleme ve doğrulama](../active-directory-domains-add-azure-portal.md).

![Azure AD Etki Alanı](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> AD Connect, etki alanını yapılandırma aşamasında doğrulamaya çalışır. Gerekli DNS kayıtlarını eklemeden yapılandırmaya devam ederseniz sihirbaz, yapılandırmayı tamamlayamaz.
>
>

## <a name="configuring-federation-with-pingfederate"></a>PingFederate ile federasyonu yapılandırma
Azure AD Connect ile PingFederate’i yalnızca birkaç tıklama ile kolayca yapılandırabilirsiniz. Ancak aşağıdaki önkoşullar gereklidir.
- PingFederate 8.4 veya daha yüksek bir sürüm.  Daha fazla bilgi için bkz. [Azure Active Directory ve Office 365 ile PingFederate Tümleştirmesi](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html)
- Kullanmayı düşündüğünüz federasyon hizmeti adı (örneğin, sts.contoso.com) için bir SSL sertifikası

### <a name="verify-the-domain"></a>Etki alanını doğrulama
PingFederate ile Federasyonu seçtikten sonra birleştirmek istediğiniz etki alanını doğrulamanız istenir.  Açılan kutudan etki alanını seçin.

![Etki Alanını Doğrulama](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>PingFederate ayarlarını dışarı aktarma


PingFederate, her federasyon Azure etki alanı için federasyon sunucusu olarak yapılandırılmalıdır.  Ayarları Dışarı Aktar düğmesine tıklayıp bu bilgileri PingFederate yöneticinizle paylaşın.  Federasyon sunucusu yöneticisi, yapılandırmayı güncelleştirir ve ardından Azure AD Connect’in meta veri ayarlarını doğrulaması için PingFederate sunucusu URL’si ile bağlantı noktası numarasını sağlar.  

![Etki Alanını Doğrulama](./media/how-to-connect-install-custom/ping2.png)

Doğrulama sorunlarınızı çözmek için PingFederate yöneticinize başvurun.  Aşağıda, Azure ile geçerli bir güven ilişkisi olmayan PingFederate sunucusu örneği verilmiştir:

![Güven](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Federasyon bağlantısını doğrulama
Azure AD Connect, önceki adımda PingFederate meta verilerinden alınan kimlik doğrulama uç noktalarını doğrulamayı dener.  Azure AD Connect, ilk olarak yerel DNS sunucularınızı kullanarak uç noktaları çözmeyi dener.  Ardından, harici bir DNS sağlayıcısı kullanarak uç noktaları çözmeyi dener.  Doğrulama sorunlarınızı çözmek için PingFederate yöneticinize başvurun.  

![Bağlantıyı Doğrulama](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-login"></a>Federasyon oturum açma işlemini doğrulama
Son olarak, federasyon etki alanında oturum açarak yeni yapılandırılan federasyon oturum açma akışını doğrulayabilirsiniz. Bu işlem başarılı olduğunda, PingFederate’e sahip federasyon başarıyla yapılandırılır.
![Oturum açma işlemini doğrulayın](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Yapılandırma ve doğrulama sayfaları
Yapılandırma bu sayfada gerçekleşir.

> [!NOTE]
> Federasyonu yapılandırdıysanız ve yüklemeye devam etmek istiyorsanız [Federasyon sunucuları için ad çözümlemesi](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) yapılandırmasını tamamladığınızdan emin olun.
>
>


![Yapılandırma için hazır](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="staging-mode"></a>Hazırlama modu
Hazırlama modu ile paralel olarak yeni bir eşitleme sunucusu ayarlanabilir. Bir eşitleme sunucusunun buluttaki yalnızca bir dizine aktarım gerçekleştirmesi desteklenir. Ancak başka bir sunucudan öğe taşımak istiyorsanız (örneğin, çalışan bir DirSync'ten) Azure AD Connect'i hazırlama modunda etkinleştirebilirsiniz. Eşitleme altyapısı, etkinleştirildiğinde verileri normal olarak içeri aktarır ve eşitler ancak Azure AD'ye veya AD'ye aktarım gerçekleştirmez. Parola eşitleme ve parola geri yazma özellikleri hazırlama modunda devre dışı bırakılır.

![Hazırlama modu](./media/how-to-connect-install-custom/stagingmode.png)

Hazırlama modundayken, eşitleme altyapısında gerekli değişiklikleri yapabilir ve dışarı aktarılacak öğeleri gözden geçirebilirsiniz. Yapılandırmayla ilgili bir sorun yoksa yükleme sihirbazını tekrar çalıştırın ve hazırlama modunu devre dışı bırakın. Veriler artık Azure AD'ye bu sunucudan aktarılır. Yalnızca bir sunucunun etkin şekilde dışarı aktarma işlemi gerçekleştirmesini sağlamak için, diğer sunucuyu devre dışı bıraktığınızdan emin olun.

Daha fazla bilgi için bkz. [Hazırlama modu](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Federasyon yapılandırmanızı doğrulama
Doğrula düğmesine tıkladığınızda Azure AD Connect sizin için DNS ayarlarını doğrular.

**İntranet bağlantısı denetimleri**

* Federasyon FQDN'sini çözümleme: Azure AD, bağlantıdan emin olmak için Federasyon FQDN’sinin DNS ile çözümlenip çözümlenemeyeceğini denetler. Azure AD Connect’in FQDN'yi çözümleyememesi durumunda doğrulama başarısız olur. Doğrulamayı başarıyla tamamlamak için federasyon hizmeti FQDN’si için bir DNS kaydının mevcut olduğundan emin olun.
* DNS A kaydı: Azure AD Connect, federasyon hizmetiniz için bir A kaydı olup olmadığını denetler. Bir A kaydı olmaması durumunda doğrulama başarısız olur. Doğrulamayı başarıyla tamamlamak için federasyon FQDN’nize ait CNAME kaydı değil, bir A kaydı oluşturun.

**Extranet bağlantısı denetimleri**

* Federasyon FQDN'sini çözümleme: Azure AD, bağlantıdan emin olmak için Federasyon FQDN’sinin DNS ile çözümlenip çözümlenemeyeceğini denetler.

![Eksiksiz](./media/how-to-connect-install-custom/completed.png)

![Doğrulama](./media/how-to-connect-install-custom/adfs7.png)

Uçtan uca kimlik doğrulamasının başarılı olduğunu doğrulamak için aşağıdaki testlerden en az birini el ile gerçekleştirmelisiniz:

* Eşitleme işlemi tamamlandığında, Azure AD Connect’te Federasyon oturum açma işlemini doğrula ek görevini kullanarak tercih ettiğiniz şirket içi kullanıcı hesabının kimlik doğrulamasını doğrulayın.
* İntranet üzerinde etki alanına katılmış bir makinedeki tarayıcıdan oturum açabildiğinizi doğrulayın: https://myapps.microsoft.com adresine bağlanın ve giriş yaptığınız hesabınız ile oturum açma işlemini doğrulayın. Yerleşik AD DS yönetici hesabı eşitlenmez ve doğrulama için kullanılamaz.
* Extranet üzerinde bir cihazdan oturum açabildiğinizi doğrulayın. Ana makineden veya bir mobil cihazdan https://myapps.microsoft.com adresine bağlanın ve kimlik bilgilerinizi girin.
* Zengin istemci oturumu açma işlemini doğrulayın. https://testconnectivity.microsoft.com adresine bağlanın, **Office 365** sekmesini ve ardından **Office 365 Çoklu Oturum Açma Testi** seçeneğini belirleyin.

## <a name="troubleshooting"></a>Sorun giderme
Aşağıdaki bölümde Azure AD Connect yüklemesi sırasında karşılaşabileceğiniz sorunlarla ilgili sorun giderme adımları ve bilgiler bulunmaktadır.

### <a name="the-adsync-database-already-contains-data-and-cannot-be-overwritten"></a>“ADSync veritabanı veri içeriyor ve üzerine yazılamaz”
Özel install Azure AD Connect kullandığınızda ve **gerekli bileşenleri Install** sayfasında **var olan bir SQL Server kullan** seçeneğini belirlediğinizde, **ADSync veritabanının zaten veri içerdiğini ve üzerine yazılamayacağını bildiren bir hatayla karşılaşabilirsiniz. Lütfen var olan veritabanını kaldırın ve yeniden deneyin.**

![Hata](./media/how-to-connect-install-custom/error1.png)

Bunun nedeni yukarıdaki metin kutularında belirttiğiniz SQL Server örneğinde **ADSync** adlı bir veritabanının mevcut olmasıdır.

Bu durum genellikle Azure AD Connect'i kaldırdıktan sonra ortaya çıkar.  Uygulamayı kaldırdığınızda veritabanı SQL Server'dan silinmez.

Bu sorunu gidermek için kaldırma işlemi öncesinde Azure AD Connect tarafından kullanılan **ADSync** veritabanının kullanılmadığından emin olun.

Veritabanını silmeden önce yedeklemeniz önerilir.

Son olarak veritabanını silmeniz gerekir.  Bunun için **Microsoft SQL Server Management Studio**'yu kullanarak SQL örneğine bağlanabilirsiniz. **ADSync** veritabanını bulun, sağ tıklayın ve bağlam menüsünden **Sil**'i seçin.  Silmek için **Tamam** düğmesine tıklayın.

![Hata](./media/how-to-connect-install-custom/error2.png)

**ADSync** veritabanını sildikten sonra **yükle** düğmesine tıklayarak yüklemeyi yeniden deneyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Yükleme tamamlandıktan sonra Synchronization Service Manager'ı veya Synchronization Rule Editor'ı kullanmadan önce Windows oturumunuzu kapatıp tekrar açın.

Azure AD Connect'i yüklediniz, artık [yüklemeyi doğrulayabilir ve lisans atayabilirsiniz](how-to-connect-post-installation.md).

Yüklemeyle etkinleştirilen özellikler hakkında daha fazla bilgi edinin: [Yanlışlıkla silmeleri engelleme](how-to-connect-sync-feature-prevent-accidental-deletes.md) ve [Azure AD Connect Health](how-to-connect-health-sync.md).

Şu genel konu başlıkları hakkında daha fazla bilgi edinin: [Zamanlayıcı ve eşitleme tetikleme](how-to-connect-sync-feature-scheduler.md).

[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
