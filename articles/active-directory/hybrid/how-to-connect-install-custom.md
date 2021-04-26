---
title: Azure Active Directory Connect yüklemesini özelleştirme
description: Bu makalede Azure AD Connect için özel yükleme seçenekleri açıklanmaktadır. Active Directory'yi Azure AD Connect aracılığı ile yüklemek için bu yönergeleri kullanın.
services: active-directory
keywords: Azure AD Connect nedir, Active Directory yükleme, Azure AD için gerekli bileşenler
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92096411"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Azure Active Directory Connect özel yüklemesi
Yükleme için daha fazla seçenek istediğinizde Azure Active Directory (Azure AD) Connect 'teki *özel ayarları* kullanın. Örneğin, birden çok ormanınız varsa veya isteğe bağlı özellikleri yapılandırmak istiyorsanız bu ayarları kullanın. [Hızlı yüklemenin](how-to-connect-install-express.md) dağıtım veya topoloji ihtiyaçlarınızı karşılayamadığı tüm durumlarda özel ayarları kullanın.

Ön koşullar:
- [Azure AD Connect indirin](https://go.microsoft.com/fwlink/?LinkId=615771).
- [Azure AD Connect: donanım ve Önkoşullar](how-to-connect-install-prerequisites.md)'daki önkoşul adımlarını doldurun. 
- [Azure AD Connect hesaplar ve izinler](reference-connect-accounts-permissions.md)bölümünde açıklanan hesaplara sahip olduğunuzdan emin olun.

## <a name="custom-installation-settings"></a>Özel yükleme ayarları 

Azure AD Connect için özel bir yükleme kurmak üzere aşağıdaki bölümlerde açıklanan sihirbaz sayfalarına gidin.

### <a name="express-settings"></a>Hızlı ayarlar
Bir özelleştirilmiş ayarlar yüklemesi başlatmak için **Hızlı ayarlar** sayfasında **Özelleştir** ' i seçin.  Bu makalenin geri kalanı, özel yükleme sürecinde size rehberlik eder. Belirli bir sayfaya yönelik bilgilere hızlıca gitmek için aşağıdaki bağlantıları kullanın:

- [Gerekli Bileşenler](#install-required-components)
- [Kullanıcı oturumu açma](#user-sign-in)
- [Azure AD 'ye bağlanma](#connect-to-azure-ad)
- [Eşitle](#sync-pages)

### <a name="install-required-components"></a>Gerekli bileşenleri yükleme
Eşitleme hizmetlerini yüklerken isteğe bağlı yapılandırma bölümünü seçilmemiş şekilde bırakabilirsiniz. Azure AD Connect her şeyi otomatik olarak ayarlar. SQL Server 2012 Express LocalDB örneğini ayarlar, uygun grupları oluşturur ve izinleri atar. Varsayılanları değiştirmek istiyorsanız, uygun kutuları temizleyin.  Aşağıdaki tablo bu seçenekleri özetler ve ek bilgilere bağlantılar sağlar. 

![Azure AD Connect gerekli yükleme bileşenlerine yönelik isteğe bağlı seçimleri gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| İsteğe bağlı yapılandırma | Description |
| --- | --- |
|Özel bir yükleme konumu belirtin| Azure AD Connect için varsayılan yükleme yolunu değiştirmenize izin verir.|
| Mevcut bir SQL Server'ı kullanma |SQL Server adı ve örnek adını belirtmenize olanak tanır. Kullanmak istediğiniz bir veritabanı sunucusu zaten varsa bu seçeneği belirleyin. Örnek **adı** için, SQL Server Örneğinizde göz atma özelliği etkinleştirilmemişse örnek adını, bir virgül ve bağlantı noktası numarasını girin.  Sonra Azure AD Connect veritabanının adını belirtin.  SQL ayrıcalıklarınız, yeni bir veritabanının oluşturulup oluşturulmayacağını veya SQL yöneticinizin veritabanını önceden oluşturması gerektiğini belirtir.  SQL Server Yöneticisi (SA) izinleriniz varsa bkz. [var olan bir veritabanını kullanarak Azure AD Connect yüklemesi](how-to-connect-install-existing-database.md).  Temsilci izinleri (DBO) varsa bkz. [SQL temsilcisi yönetici izinleri kullanarak Azure AD Connect yüklemesi](how-to-connect-install-sql-delegation.md). |
| Mevcut bir hizmet hesabını kullanma |Varsayılan olarak, Azure AD Connect Eşitleme Hizmetleri için bir sanal hizmet hesabı sağlar. SQL Server uzak bir örneğini kullanıyorsanız veya kimlik doğrulaması gerektiren bir proxy kullanıyorsanız, bir *yönetilen hizmet hesabı* veya etki alanında parola korumalı bir hizmet hesabı kullanabilirsiniz. Bu durumlarda, kullanmak istediğiniz hesabı girin. Yüklemeyi çalıştırmak için, SQL 'de bir SA olmanız gerekir, bu sayede hizmet hesabı için oturum açma kimlik bilgileri oluşturabilirsiniz. Daha fazla bilgi için bkz. [Azure AD Connect hesapları ve izinleri](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>En son derlemeyi kullanarak, SQL Yöneticisi artık veritabanını bant dışı temin edebilir. Azure AD Connect Yöneticisi, veritabanını veritabanı sahibi haklarıyla yükleyebilir.  Daha fazla bilgi için bkz. [SQL Temsilcili yönetici izinleri kullanarak Azure AD Connect yüklemesi](how-to-connect-install-sql-delegation.md).|
| Özel eşitleme grubu belirtme |Varsayılan olarak, Eşitleme Hizmetleri yüklendiğinde Azure AD Connect sunucuda yerel olan dört grup oluşturur. Bu gruplar Yöneticiler, Işleçler, gözatmak ve parola Sıfırlamalardır. Kendi gruplarınızı burada belirtebilirsiniz. Grupların sunucuda yerel olması gerekir. Etki alanında yer amazlar. |
|Eşitleme ayarlarını içeri aktar (Önizleme)|Azure AD Connect diğer sürümlerinden ayarları içeri aktarmanıza izin verir.  Daha fazla bilgi için bkz. [Azure AD Connect yapılandırma ayarlarını içeri ve dışarı aktarma](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Kullanıcı oturumu açma
Gerekli bileşenleri yükledikten sonra kullanıcılarınızın çoklu oturum açma yöntemini seçin. Aşağıdaki tabloda, kullanılabilir seçenekler kısaca açıklanmaktadır. Oturum açma yöntemleriyle ilgili tam açıklama için bkz. [Kullanıcı oturumu açma](plan-connect-user-signin.md).

!["Kullanıcı oturum açma" sayfasını gösteren ekran görüntüsü. "Parola karması eşitleme" seçeneği seçilidir.](./media/how-to-connect-install-custom/usersignin4.png)

| Çoklu oturum açma seçeneği | Description |
| --- | --- |
| Parola karması eşitleme |Kullanıcılar, Microsoft 365 gibi Microsoft bulut hizmetlerinde, şirket içi ağda kullandıkları aynı parolayı kullanarak oturum açabilirler. Kullanıcı parolaları, Azure AD ile bir parola karması olarak eşitlenir. Kimlik doğrulaması bulutta oluşur. Daha fazla bilgi için bkz. [Parola karması eşitleme](how-to-connect-password-hash-synchronization.md). |
|Doğrudan kimlik doğrulama|Kullanıcılar, Microsoft 365 gibi Microsoft bulut hizmetlerinde, şirket içi ağda kullandıkları aynı parolayı kullanarak oturum açabilirler.  Kullanıcı parolaları, şirket içi Active Directory etki alanı denetleyicisine geçirilerek onaylanır.
| AD FS ile Federasyon |Kullanıcılar, Microsoft 365 gibi Microsoft bulut hizmetlerinde, şirket içi ağda kullandıkları aynı parolayı kullanarak oturum açabilirler.  Kullanıcılar, oturum açmak için şirket içi Azure Dizin Federasyon Hizmetleri (AD FS) örneğine yönlendirilir. Kimlik doğrulaması şirket içinde meydana gelir. |
| PingFederate ile federasyon|Kullanıcılar, Microsoft 365 gibi Microsoft bulut hizmetlerinde, şirket içi ağda kullandıkları aynı parolayı kullanarak oturum açabilirler.  Kullanıcılar, oturum açmak için şirket içi PingFederate örneğine yönlendirilir. Kimlik doğrulaması şirket içinde meydana gelir. |
| Yapılandırmayın |Kullanıcı oturum açma özelliği yüklü değil veya yapılandırılmadı. Zaten bir üçüncü taraf Federasyon sunucusu veya başka bir çözümünüz varsa bu seçeneği belirleyin. |
|Çoklu oturum açmayı etkinleştir|Bu seçenek hem Parola karması eşitlemesi hem de geçişli kimlik doğrulaması ile kullanılabilir. Şirket ağlarında masaüstü kullanıcıları için çoklu oturum açma deneyimi sağlar. Daha fazla bilgi için bkz. [Çoklu oturum açma](how-to-connect-sso.md). </br></br>**Note:** AD FS müşteriler için bu seçenek kullanılamaz. AD FS, çoklu oturum açma düzeyini zaten sunmaktadır.</br>

### <a name="connect-to-azure-ad"></a>Azure AD'ye Bağlanma
**Azure AD 'ye Bağlan** sayfasında, bir genel yönetici hesabı ve parolası girin. Önceki sayfada **AD FS Ile Federasyon** ' yı seçtiyseniz, Federasyon için etkinleştirmeyi planladığınız bir etki alanında bulunan bir hesapla oturum açın. 

Varsayılan *onmicrosoft.com* etki alanında Azure AD kiracınızla birlikte gelen bir hesap kullanmak isteyebilirsiniz. Bu hesap yalnızca Azure AD 'de bir hizmet hesabı oluşturmak için kullanılır. Yükleme tamamlandıktan sonra kullanılmaz.
  
!["Azure AD 'ye Bağlan" sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/connectaad.png)

Genel yönetici hesabınızda çok faktörlü kimlik doğrulaması etkinse, oturum açma penceresinde parolayı yeniden sağlarsınız ve çok faktörlü kimlik doğrulama sınamasını tamamlamalısınız. Sınama bir doğrulama kodu veya telefon araması olabilir.  

!["Azure AD 'ye Bağlan" sayfasını gösteren ekran görüntüsü. Bir çok faktörlü kimlik doğrulama alanı kullanıcıdan bir kod ister.](./media/how-to-connect-install-custom/connectaadmfa.png)

Genel yönetici hesabında de [ayrıcalıklı kimlik yönetimi](../privileged-identity-management/pim-getting-started.md) etkinleştirilebilir.

Bir hata görürseniz veya bağlantı sorunlarıyla karşılaşırsanız bkz. [bağlantı sorunlarını giderme](tshoot-connect-connectivity.md).

## <a name="sync-pages"></a>Sayfaları Eşitle

Aşağıdaki bölümlerde, **eşitleme** bölümündeki sayfalar açıklanır.

### <a name="connect-your-directories"></a>Dizinlerinizi bağlama
Active Directory Domain Services (Azure AD DS) ' e bağlanmak için, Azure AD Connect yeterli izinlere sahip bir hesabın Orman adı ve kimlik bilgileri gerekir.

!["Dizinlerinizi bağlama" sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/connectdir01.png)

Orman adını girdikten ve  **Dizin Ekle**' yi seçtikten sonra bir pencere görüntülenir. Aşağıdaki tabloda seçeneklerinizi açıklanmaktadır.

| Seçenek | Açıklama |
| --- | --- |
| Yeni hesap oluştur | Dizin eşitleme sırasında Active Directory ormana bağlanması için Azure AD Connect gereken Azure AD DS hesabını oluşturun. Bu seçeneği belirledikten sonra, bir kurumsal yönetici hesabının kullanıcı adını ve parolasını girin.  Azure AD Connect, gerekli Azure AD DS hesabını oluşturmak için, belirtilen Kurumsal Yönetici hesabını kullanır. Etki alanı bölümünü NetBIOS biçiminde veya FQDN biçiminde girebilirsiniz. Diğer bir deyişle, *FABRIKAM \ yönetici* veya *FABRIKAM. com\administrator* yazın. |
| Mevcut hesabı kullan | Azure AD Connect, Dizin eşitleme sırasında Active Directory ormanına bağlanmak için kullanabileceği mevcut bir Azure AD DS hesabı sağlayın. Etki alanı bölümünü NetBIOS biçiminde veya FQDN biçiminde girebilirsiniz. Diğer bir deyişle, *FABRIKAM\syncuser* veya *FABRIKAM. com\syncuser* girin. Bu hesap, yalnızca varsayılan okuma izinlerine ihtiyacı olduğundan normal bir kullanıcı hesabı olabilir. Ancak senaryonuza bağlı olarak daha fazla izne sahip olabilirsiniz. Daha fazla bilgi için bkz. [Azure AD Connect hesapları ve izinleri](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Yeni bir hesap oluşturmayı veya mevcut bir hesabı kullanmayı seçebileceğiniz "Dizin bağla" sayfasını ve D orman hesabı penceresini gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> Derleme 1.4.18.0 itibariyle, Azure AD DS bağlayıcı hesabı olarak Kurumsal Yönetici veya etki alanı yöneticisi hesabı kullanamazsınız. **Mevcut hesabı kullan**' ı seçtiğinizde, bir Kurumsal Yönetici hesabı veya bir etki alanı yönetici hesabı girmeyi denerseniz, şu hatayı görürsünüz: "ad ormanı hesabınız için kurumsal veya etki alanı yönetici hesabı kullanılmasına izin verilmiyor. Hesabı sizin için Azure AD Connect oluşturmaya veya doğru izinlerle bir eşitleme hesabı belirtmenize izin verin. "
>

### <a name="azure-ad-sign-in-configuration"></a>Azure AD oturum açma yapılandırması
**Azure AD oturum açma yapılandırması** sayfasında, şirket içi Azure AD DS 'daki Kullanıcı asıl adı (UPN) etki alanlarını gözden geçirin. Bu UPN etki alanları Azure AD 'de doğrulandı. Bu sayfada, userPrincipalName için kullanılacak özniteliği yapılandırırsınız.

!["Azure A D oturum açma yapılandırması" sayfasında doğrulanmamış etki alanlarını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

**Eklenmemiş** veya **doğrulanmadı** olarak işaretlenen her etki alanını gözden geçirin. Kullandığınız etki alanlarının Azure AD 'de doğrulandığından emin olun. Etki alanlarınızı doğruladıktan sonra, dairesel yenileme simgesini seçin. Daha fazla bilgi için bkz. [etki alanı ekleme ve doğrulama](../fundamentals/add-custom-domain.md).

Kullanıcılar, Azure AD 'de oturum açtıklarında veya Microsoft 365, *userPrincipalName* özniteliğini kullanır. Kullanıcılar eşitlenmeden önce Azure AD, UPN soneki olarak da bilinen etki alanlarını doğrulamalıdır. Microsoft varsayılan öznitelik userPrincipalName değerini tutmanızı önerir. 

UserPrincipalName özniteliği yönlendirilemez ve doğrulanamazsa, başka bir öznitelik seçebilirsiniz. Örneğin, oturum açma KIMLIĞINI tutan öznitelik olarak e-posta ' i seçebilirsiniz. UserPrincipalName dışında bir öznitelik kullandığınızda, bu bir *ALTERNATIF kimlik* olarak bilinir. 

Alternatif KIMLIK özniteliği değeri, RFC 822 standardına uymalıdır. Parola karması eşitleme, geçişli kimlik doğrulaması ve Federasyon ile alternatif KIMLIK kullanabilirsiniz. Active Directory, özniteliği yalnızca tek bir değere sahip olsa bile çok değerli olarak tanımlanamaz. Alternatif kimlik hakkında daha fazla bilgi için bkz. [geçişli kimlik doğrulaması: sık sorulan sorular](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname).

>[!NOTE]
> Doğrudan kimlik doğrulamayı etkinleştirdiğinizde, özel yükleme işleminde devam etmek için en az bir doğrulanmış etki alanınız olmalıdır.

> [!WARNING]
> Alternatif kimlikler tüm Microsoft 365 iş yükleri ile uyumlu değildir. Daha fazla bilgi için bkz. [Alternatif oturum açma kimliklerini yapılandırma](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>

### <a name="domain-and-ou-filtering"></a>Etki alanı ve OU filtreleme
Varsayılan olarak, tüm etki alanları ve kuruluş birimleri (OU 'Lar) eşitlenir. Bazı etki alanlarını veya OU 'Ları Azure AD ile eşleştirmek istemiyorsanız, uygun seçimleri temizleyebilirsiniz.  

![Etki alanı ve O U filtreleme sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/domainoufiltering.png)  

Bu sayfa, etki alanı tabanlı ve OU tabanlı filtrelemeyi yapılandırır. Değişiklik yapmayı planlıyorsanız, bkz. [etki alanı tabanlı filtreleme](how-to-connect-sync-configure-filtering.md#domain-based-filtering) ve [OU tabanlı filtreleme](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Bazı OU 'Lar işlevsellik açısından önemlidir, bu nedenle seçili durumda bırakmanız gerekir.

1.1.524.0 ' den eski bir Azure AD Connect sürümünde OU tabanlı filtreleme kullanıyorsanız, yeni OU 'Lar varsayılan olarak eşitlenir. Yeni OU 'Ların eşitlenmesini istemiyorsanız, varsayılan davranışı [OU tabanlı filtreleme](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) adımından sonra ayarlayabilirsiniz. Azure AD Connect 1.1.524.0 veya üzeri için yeni OU 'Ların eşitlenmesini isteyip istemediğinizi belirtebilirsiniz.

[Grup tabanlı filtreleme](#sync-filtering-based-on-groups)kullanmayı planlıyorsanız, gruba sahıp olan OU 'nun dahil EDILDIĞINDEN ve OU filtreleme kullanılarak filtrelenmediğinden emin olun. Grup tabanlı filtreleme değerlendirilmeden önce OU filtrelemesi değerlendirilir.

Güvenlik duvarı kısıtlamaları nedeniyle bazı etki alanlarına ulaşılamıyor de mümkündür. Bu etki alanları varsayılan olarak seçilmemiş ve bir uyarı görüntüler.  

![Erişilemeyen etki alanlarını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/unreachable.png)  

Bu uyarıyı görürseniz, bu etki alanlarının gerçekten ulaşılamaz olduğundan ve uyarının beklendiğinden emin olun.

### <a name="uniquely-identifying-your-users"></a>Kullanıcılarınızı benzersiz olarak tanımlama

**Kullanıcıları tanımlama** sayfasında, şirket içi dizinlerinizde bulunan kullanıcıları tanımlamayı ve sourcetutturucu özniteliğini kullanarak bunları tanımlamayı seçin.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Şirket içi dizinlerinizde kullanıcıların nasıl tanımlanması gerektiğini seçin
*Ormanlar arası eşleşme* özelliğini kullanarak Azure AD DS ORMANLARıNıZıN Azure AD 'de nasıl temsil edileceğini tanımlayabilirsiniz. Bir Kullanıcı tüm ormanlarda yalnızca bir kez temsil edilebilir veya etkin ve devre dışı hesapları birleşimine sahip olabilir. Ayrıca kullanıcı, bazı ormanlarda kişi olarak da temsil edilebilir.

![Kullanıcılarınızı benzersiz olarak tanımlayabileceğiniz sayfayı gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/unique2.png)

| Ayar | Açıklama |
| --- | --- |
| [Kullanıcılar tüm ormanlarda yalnızca bir kez temsil edilir](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Tüm kullanıcılar Azure AD'de bireysel nesne olarak oluşturulur. Nesneler meta veri deposuna katılmadı. |
| [Posta özniteliği](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Bu seçenek, posta özniteliğinin farklı ormanlarda aynı değere sahip olması halinde kullanıcıları ve kişileri birleştirir. Kişileriniz GALSync kullanılarak oluşturulduysa bu seçeneği kullanın. Bu seçeneği belirlerseniz, posta özniteliği doldurulmamış olan kullanıcı nesneleri Azure AD ile eşitlenmez. |
| [ObjectSID ve Msexchangemasteraccountsıd/msRTCSIP-OriginatorSID öznitelikleri](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Bu seçenek, hesap ormanındaki etkin bir kullanıcıyla kaynak ormandaki devre dışı bırakılmış bir kullanıcıyı birleştirir. Bu yapılandırma, Exchange'de bağlı posta kutusu olarak bilinir. Yalnızca Lync kullanıyorsanız ve kaynak ormanında Exchange yoksa, bu seçeneği kullanabilirsiniz. |
| SAMAccountName ve Mailtakma ad öznitelikleri |Bu seçenek, Kullanıcı için oturum açma KIMLIĞININ bulunması beklenen öznitelikleri birleştirir. |
| Belirli bir öznitelik seçin |Bu seçenek, kendi özniteliğinizi seçmenize olanak tanır. Bu seçeneği belirlerseniz, (seçili) özniteliği doldurulmamış olan kullanıcı nesneleri Azure AD ile eşitlenmez. **Sınırlama:** Bu seçenek için yalnızca metadizesinde zaten olan öznitelikler kullanılabilir. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Kullanıcıların bir kaynak Bağlayıcısı kullanarak nasıl tanımlanması gerektiğini seçin
*Sourcetutturucu* özniteliği, bir Kullanıcı nesnesinin kullanım ömrü boyunca sabittir. Bu, şirket içi kullanıcıyı Azure AD 'deki kullanıcıyla bağlayan birincil anahtardır.

| Ayar | Açıklama |
| --- | --- |
| Kaynak bağlayıcısını Azure 'un yönetmesine izin ver | Azure AD’nin sizin için özniteliği seçmesini istiyorsanız bu seçeneği belirleyin. Bu seçeneği belirlerseniz Azure AD Connect, [MS-DS-ımıbu GUID ' i sourcetutturucu olarak kullanma](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)bölümünde açıklanan sourcetutturucu öznitelik seçme mantığını uygular. Özel yükleme tamamlandıktan sonra, hangi özniteliğin Sourcetutturucu özniteliği olarak çekilmiş olduğunu görürsünüz. |
| Belirli bir öznitelik seçin | Var olan bir AD özniteliğini Sourcetutturucu özniteliği olarak belirtmek istiyorsanız bu seçeneği belirleyin. |

Sourcetutturucu özniteliği değiştirilemediğinden uygun bir öznitelik seçmelisiniz. ObjectGUID iyi bir seçenektir. Kullanıcı hesabı ormanlar veya etki alanları arasında taşınmamışsa bu öznitelik değiştirilmez. Bir kişi marrıes veya atamaları değiştirdiğinde değiştirebir şekilde öznitelik seçmeyin. 

At işareti (@) içeren öznitelikleri kullanamazsınız, bu nedenle e-posta ve userPrincipalName kullanamazsınız. Özniteliği de büyük/küçük harfe duyarlıdır, bu nedenle bir nesneyi ormanlar arasında taşıdığınızda büyük ve küçük harfleri koruduğunuzdan emin olun. İkili öznitelikler Base64 kodlardır, ancak diğer öznitelik türleri kodlanmamış durumda kalır. 

Federasyon senaryolarında ve bazı Azure AD arabirimlerinde, Sourcetutturucu özniteliği de *ImmutableID* olarak bilinir. 

Kaynak Bağlayıcısı hakkında daha fazla bilgi için bkz. [tasarım kavramları](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Grup tabanlı eşitleme filtrelemesi
Gruplar arası filtreleme özelliği, bir pilot için yalnızca küçük bir nesne alt kümesini eşitlemenize olanak tanır. Bu özelliği kullanmak için şirket içi Active Directory örneğinde bu amaçla bir grup oluşturun. Ardından, doğrudan üye olarak Azure AD ile eşitlenecek kullanıcıları ve grupları ekleyin. Daha sonra, Azure AD 'de bulunması gereken nesnelerin listesini korumak için kullanıcıları ekleyebilir veya bu gruptan kullanıcıları kaldırabilirsiniz. 

Eşitlenmesini istediğiniz tüm nesneler doğrudan grubun üyesi olmalıdır. Kullanıcıların, grupların, kişilerin ve bilgisayarların ya da cihazların hepsi doğrudan üye olmalıdır. İç içe Grup üyeliği çözülmedi. Bir grubu üye olarak eklediğinizde, yalnızca grubun kendisi eklenir. Üyeleri eklenmez.

![Kullanıcıları ve cihazları nasıl filtreleyebileceğiniz sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Bu özellik yalnızca bir pilot dağıtımı desteklemeye yöneliktir. Bunu tam bir üretim dağıtımında kullanmayın.
>

Tam bir üretim dağıtımında, tek bir grubu ve tüm nesnelerini eşitlenmek üzere sürdürmek zor olabilir. Gruplar arası filtreleme özelliği yerine, [filtrelemeyi yapılandırma](how-to-connect-sync-configure-filtering.md)bölümünde açıklanan yöntemlerden birini kullanın.

### <a name="optional-features"></a>İsteğe bağlı özellikler
Bir sonraki sayfada, senaryonuza yönelik isteğe bağlı Özellikler ' i seçebilirsiniz.

>[!WARNING]
>Azure AD Connect sürümlerinin 1.0.8641.0 ve önceki sürümleri, parola geri yazma için Azure Access Control Service bağımlıdır.  Bu hizmet 7 Kasım 2018 tarihinde kullanımdan kaldırıldı.  Bu Azure AD Connect sürümlerinden birini kullanıyorsanız ve parola geri yazma özelliğini etkinleştirdiyseniz, kullanıcılar, hizmet devre dışı bırakıldığında parolalarını değiştirme veya sıfırlama olanağını kaybedebilir. Azure AD Connect bu sürümleri parola geri yazmayı desteklemez.
>
>Daha fazla bilgi için bkz. [Azure Access Control Service 'Den geçiş](../azuread-dev/active-directory-acs-migration.md).
>
>Parola geri yazma özelliğini kullanmak istiyorsanız [en son Azure AD Connect sürümünü](https://www.microsoft.com/download/details.aspx?id=47594)indirin.

!["Isteğe bağlı özellikler" sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Azure AD Eşitleme veya doğrudan eşitleme (DirSync) etkinse, Azure AD Connect geri yazma özelliklerini etkinleştirmez.



| İsteğe bağlı özellikler | Description |
| --- | --- |
| Exchange karma dağıtımı |Exchange karma dağıtımı özelliği, Exchange posta kutularının hem şirket içinde hem de Microsoft 365 birlikte kullanılmasına izin verir. Azure AD Connect, belirli bir [öznitelik](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) KÜMESINI Azure AD 'den şirket içi dizininize geri eşitler. |
| Exchange posta ortak klasörleri | Exchange posta ortak klasörleri özelliği, posta etkin ortak klasör nesnelerini şirket içi Active Directory Azure AD 'den eşitlemenize olanak tanır. |
| Azure AD uygulaması ve öznitelik filtreleme |Azure AD uygulaması ve öznitelik filtrelemeyi etkinleştirerek, eşitlenmiş özniteliklerin kümesini uyarlayabilirsiniz. Bu seçenek sihirbaza iki yapılandırma sayfası daha ekler. Daha fazla bilgi için bkz. [Azure AD uygulaması ve öznitelik filtreleme](#azure-ad-app-and-attribute-filtering). |
| Parola karması eşitleme |Oturum açma çözümü olarak Federasyon ' yı seçtiyseniz, Parola karması eşitlemesini etkinleştirebilirsiniz. Bu durumda, bunu bir yedekleme seçeneği olarak kullanabilirsiniz.  </br></br>Doğrudan kimlik doğrulaması ' nı seçtiyseniz, eski istemcilere yönelik destek sağlamak ve bir yedekleme sağlamak için bu seçeneği etkinleştirebilirsiniz.</br></br> Daha fazla bilgi için bkz. [Parola karması eşitleme](how-to-connect-password-hash-synchronization.md).|
| Parola geri yazma |Azure AD 'deki parola değişikliklerinin şirket içi dizininize geri yazıldığından emin olmak için bu seçeneği kullanın. Daha fazla bilgi için bkz. [Parola yönetimine başlarken](../authentication/tutorial-enable-sspr.md). |
| Grup geri yazma |Microsoft 365 grupları kullanıyorsanız, Active Directory Şirket içi örneğindeki grupları temsil edebilirsiniz. Bu seçenek yalnızca şirket içi Active Directory örneğinde Exchange 'niz varsa kullanılabilir. Daha fazla bilgi için [Azure AD Connect grup geri yazma](how-to-connect-group-writeback.md)konusuna bakın.|
| Cihaz geri yazma |Koşullu erişim senaryolarında, Azure AD 'deki cihaz nesnelerini Active Directory Şirket içi örneğine geri yazmak için bu seçeneği kullanın. Daha fazla bilgi için bkz. [Azure AD Connect'te cihaz geri yazma özelliğini etkinleştirme](how-to-connect-device-writeback.md). |
| Dizin genişletme öznitelik eşitlemesi |Belirtilen öznitelikleri Azure AD 'ye eşitlemek için bu seçeneği belirleyin. Daha fazla bilgi için bkz. [Dizin genişletmeleri](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD uygulaması ve öznitelik filtreleme
Hangi özniteliklerin Azure AD ile eşitleneceğini sınırlandırmak istiyorsanız, kullandığınız hizmetleri seçerek başlayın. Bu sayfadaki seçimleri değiştirirseniz, yükleme sihirbazını yeniden çalıştırarak, açıkça yeni bir hizmet seçmeniz gerekir.

![İsteğe bağlı Azure A D Apps özelliklerini gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/azureadapps2.png)

Önceki adımda seçtiğiniz hizmetlere bağlı olarak, Bu sayfa eşitlenen tüm öznitelikleri gösterir. Bu liste, eşitlenmekte olan tüm nesne türlerinin bir birleşimidir. Bazı özniteliklerin eşitlenmemiş kalması gerekiyorsa, seçimi bu özniteliklerden temizleyebilirsiniz.

![İsteğe bağlı Azure A D öznitelikleri özelliklerini gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Özniteliklerin kaldırılması işlevselliği etkileyebilir. En iyi uygulamalar ve öneriler için bkz. [eşitlenmek üzere öznitelikler](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>

### <a name="directory-extension-attribute-sync"></a>Dizin Genişletme öznitelik eşitlemesi
Azure AD 'de şemayı, kuruluşunuzun eklediği özel öznitelikleri veya Active Directory diğer özniteliklerini kullanarak genişletebilirsiniz. Bu özelliği kullanmak için, **Isteğe bağlı özellikler** sayfasında, **dizin uzantısı öznitelik eşitleme**' yi seçin. **Dizin uzantıları** sayfasında, eşitlenecek daha fazla öznitelik seçebilirsiniz.

>[!NOTE]
>**Kullanılabilir öznitelikler** alanı büyük/küçük harfe duyarlıdır.

!["Dizin uzantıları" sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/extension2.png)

Daha fazla bilgi için bkz. [Dizin genişletmeleri](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Çoklu oturum açmayı etkinleştirme
**Çoklu oturum** açma sayfasında, parola eşitleme veya doğrudan kimlik doğrulama ile kullanmak için çoklu oturum açma 'yı yapılandırırsınız. Bu adımı, Azure AD ile eşitlenen her orman için bir kez yapabilirsiniz. Yapılandırma iki adımdan oluşur:

1.  Şirket içi Active Directory örneğinde gerekli bilgisayar hesabını oluşturun.
2.  İstemci makinelerin intranet bölgesini çoklu oturum açmayı destekleyecek şekilde yapılandırın.

#### <a name="create-the-computer-account-in-active-directory"></a>Active Directory'de bilgisayar hesabını oluşturma
Azure AD Connect eklenen her orman için, bilgisayar hesabının her ormanda oluşturulabilmesi için etki alanı yöneticisi kimlik bilgilerini sağlamanız gerekir. Kimlik bilgileri yalnızca hesabı oluşturmak için kullanılır. Bunlar başka bir işlem için depolanmaz veya kullanılmaz. Aşağıdaki görüntüde gösterildiği gibi **Çoklu oturum açma** sayfasında kimlik bilgilerini ekleyin.

!["Çoklu oturum açmayı etkinleştir" sayfasını gösteren ekran görüntüsü. Orman kimlik bilgileri eklendi.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Çoklu oturum açma kullanmak istemediğiniz ormanları atlayabilirsiniz.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>İstemci makineler için Intranet bölgesini yapılandırma
İstemcinin intranet bölgesinde otomatik olarak oturum açtığından emin olmak için URL 'nin intranet bölgesinin bir parçası olduğundan emin olun. Bu adım, etki alanına katılmış bilgisayarın kurumsal ağa bağlıyken Azure AD 'ye otomatik olarak bir Kerberos bileti göndermesi sağlar.

Grup ilkesi yönetim araçları olan bir bilgisayarda:

1.  Grup ilkesi Yönetim Araçları ' nı açın.
2.  Tüm kullanıcılara uygulanacak grup ilkesini düzenleyin. Örneğin, varsayılan etki alanı ilkesi.
3.    >    >  **Windows bileşenleri**  >  **Internet Explorer**  >  **Internet Denetim Masası**  >  **Güvenlik sayfasına** Yönetim Şablonları Kullanıcı Yapılandırması ' na gidin. Ardından **siteden bölgeye atama listesi**' ni seçin.
4.  İlkeyi etkinleştirin. Ardından, iletişim kutusunda bir değer adı `https://autologon.microsoftazuread-sso.com` ve değeri girin `1` . Kurulumlarınızın aşağıdaki görüntü gibi görünmesi gerekir.
  
    ![İntranet bölgelerini gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/sitezone.png)

6.  İki kez **Tamam 'ı** seçin.

## <a name="configuring-federation-with-ad-fs"></a>AD FS ile federasyonu yapılandırma
Yalnızca birkaç tıklamayla Azure AD Connect ile AD FS yapılandırabilirsiniz. Başlamadan önce şunlar gerekir:

* Federasyon sunucusu için Windows Server 2012 R2 veya üzeri. Uzaktan Yönetim etkinleştirilmelidir.
* Web uygulaması ara sunucusu için Windows Server 2012 R2 veya üzeri. Uzaktan Yönetim etkinleştirilmelidir.
* Kullanmayı düşündüğünüz Federasyon Hizmeti adı için bir TLS/SSL sertifikası (örneğin, sts.contoso.com).

>[!NOTE]
>AD FS grubunuz için bir TLS/SSL sertifikasını, Federasyon güveninizi yönetmek için kullanmasanız bile Azure AD Connect kullanarak güncelleştirebilirsiniz.

### <a name="ad-fs-configuration-prerequisites"></a>AD FS yapılandırma önkoşulları
Azure AD Connect kullanarak AD FS grubunuzu yapılandırmak için, uzak sunucularda WinRM 'nin etkinleştirildiğinden emin olun. [Federasyon önkoşulları](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration)'ndaki diğer görevleri tamamladığınızdan emin olun. Ayrıca [Azure AD Connect ve Federasyon/WAP sunucuları](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) tablosunda listelenen bağlantı noktaları gereksinimlerini izlediğinizden emin olun.

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Yeni bir AD FS grubu oluşturma veya var olan bir AD FS grubunu kullanma
Mevcut bir AD FS grubunu kullanabilir veya yeni bir grup oluşturabilirsiniz. Yeni bir tane oluşturmayı seçerseniz, TLS/SSL sertifikasını sağlamanız gerekir. TLS/SSL sertifikası bir parolayla korunuyorsa, parolayı girmeniz istenir.

!["A D F S grubu" sayfasını gösteren ekran görüntüsü](./media/how-to-connect-install-custom/adfs1.png)

Mevcut bir AD FS grubunu kullanmayı seçerseniz, AD FS ile Azure AD arasındaki güven ilişkisini yapılandırabileceğiniz sayfayı görürsünüz.

>[!NOTE]
>Yalnızca bir AD FS grubunu yönetmek için Azure AD Connect kullanabilirsiniz. Azure AD 'nin seçili AD FS grubunda yapılandırıldığı mevcut bir Federasyon güveniniz varsa, güveni sıfırdan yeniden oluşturur Azure AD Connect.

### <a name="specify-the-ad-fs-servers"></a>AD FS sunucularını belirtme
AD FS yüklemek istediğiniz sunucuları belirtin. Kapasite gereksinimlerinize bağlı olarak bir veya daha fazla sunucu ekleyebilirsiniz. Bu yapılandırmayı ayarlamadan önce, tüm AD FS sunucularına Active Directory ekleyin. Web uygulaması ara sunucuları için bu adım gerekli değildir. 

Microsoft, test ve pilot dağıtımlar için tek bir AD FS sunucusunun yüklenmesini önerir. İlk yapılandırmadan sonra, Azure AD Connect yeniden çalıştırarak ölçekleme gereksinimlerinizi karşılayacak daha fazla sunucu ekleyebilir ve dağıtabilirsiniz.

> [!NOTE]
> Bu yapılandırmayı ayarlamadan önce, tüm sunucularınızın bir Azure AD etki alanına katılmış olduğundan emin olun.
>


!["Federasyon sunucuları" sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Web Uygulaması Ara Sunucularını belirtme
Web uygulaması ara sunucularını belirtin. Web uygulaması ara sunucusu, extranet 'e bakan çevre ağınızda dağıtılır. Extranet 'ten gelen kimlik doğrulama isteklerini destekler. Kapasite gereksinimlerinize bağlı olarak bir veya daha fazla sunucu ekleyebilirsiniz. 

Microsoft, test ve pilot dağıtımlar için tek bir Web uygulaması ara sunucusunun yüklenmesini önerir. İlk yapılandırmadan sonra, Azure AD Connect yeniden çalıştırarak ölçekleme gereksinimlerinizi karşılayacak daha fazla sunucu ekleyebilir ve dağıtabilirsiniz. İntranetten kimlik doğrulamasını karşılamak için eşit sayıda proxy sunucunuz olması önerilir.

> [!NOTE]
> - Kullandığınız hesap Web uygulaması ara sunucularında yerel yönetici değilse, yönetici kimlik bilgileri istenir.
> - Web uygulaması ara sunucularını belirlemeden önce, Azure AD Connect sunucusu ile Web uygulaması ara sunucusu arasında HTTP/HTTPS bağlantısının olduğundan emin olun.
> - Web uygulaması sunucusu ve AD FS sunucusu arasında, kimlik doğrulama isteklerinin akmasını sağlamak için HTTP/HTTPS bağlantısının olduğundan emin olun.
>


![Web uygulaması proxy sunucuları sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/adfs3.png)

Web uygulaması sunucusunun AD FS sunucuya güvenli bir bağlantı kurmasını sağlamak için kimlik bilgilerini girmeniz istenir. Bu kimlik bilgileri AD FS sunucusundaki bir yerel yönetici hesabı için olmalıdır.

!["Kimlik bilgileri" sayfasını gösteren ekran görüntüsü. Yönetici kimlik bilgileri, Kullanıcı adı alanına ve parola alanına girilir.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>AD FS hizmetine ilişkin hizmet hesabını belirtme
AD FS hizmeti, kullanıcıların kimliğini doğrulamak ve Active Directory Kullanıcı bilgilerini aramak için bir etki alanı hizmet hesabı gerektirir. AD FS hizmeti, iki hizmet hesabı türünü destekler:

* **Grup tarafından yönetilen hizmet hesabı**: Bu hesap türü Windows Server 2012 tarafından AD DS eklenmiştir. Bu tür bir hesap, AD FS gibi hizmetler sağlar. Parolayı düzenli olarak güncelleştirmeniz gerekmeyen tek bir hesaptır. AD FS sunucularınızın ait olduğu etki alanında Windows Server 2012 etki alanı denetleyicileriniz varsa bu seçeneği kullanın.
* **Etki alanı kullanıcı hesabı**: Bu hesap türü, bir parola sağlamanızı ve süresi sona erdiğinde düzenli olarak güncelleştirmenizi gerektirir. Bu seçeneği yalnızca AD FS sunucularınızın ait olduğu etki alanında Windows Server 2012 etki alanı denetleyicileri yoksa kullanın.

**Grup tarafından yönetilen hizmet hesabı oluştur** ' u seçtiyseniz ve bu özellik Active Directory hiç kullanılmadıysa, kurumsal yönetici kimlik bilgilerinizi girin. Bu kimlik bilgileri, anahtar deposunu başlatmak ve Active Directory'de ilgili özelliği etkinleştirmek için kullanılır.

> [!NOTE]
> Azure AD Connect, AD FS hizmetinin etki alanında zaten bir hizmet asıl adı (SPN) olarak kayıtlı olup olmadığını denetler.  Azure AD DS, yinelenen SPN 'Lerin aynı anda kaydedilmesine izin vermez.  Yinelenen bir SPN bulunursa, SPN kaldırılana kadar devam edemezsiniz.

!["A D F S hizmet hesabı" sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Federasyona eklemek istediğiniz Azure AD etki alanını seçin
AD FS ile Azure AD arasında federasyon ilişkisi ayarlamak için **Azure AD etki alanı** sayfasını kullanın. Burada Azure AD 'ye güvenlik belirteçleri sağlamak için AD FS yapılandırırsınız. Ayrıca, Azure AD 'yi bu AD FS örneğinden belirteçlere güvenecek şekilde yapılandırırsınız. 

Bu sayfada, ilk yüklemede yalnızca tek bir etki alanı yapılandırabilirsiniz. Daha sonra Azure AD Connect'i tekrar çalıştırarak daha fazla etki alanını yapılandırabilirsiniz.

!["Azure A etki alanı" sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Federasyon için seçilen Azure AD etki alanını doğrulama
Federasyona eklemek istediğiniz etki alanını seçtiğinizde, Azure AD Connect doğrulanmamış bir etki alanını doğrulamak için kullanabileceğiniz bilgiler sağlar. Daha fazla bilgi için bkz. [etki alanı ekleme ve doğrulama](../fundamentals/add-custom-domain.md).

![Etki alanını doğrulamak için kullanabileceğiniz bilgiler de dahil olmak üzere, "Azure A etki alanı" sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect, yapılandırma aşamasında etki alanını doğrulamaya çalışır. Gerekli etki alanı adı sistemi (DNS) kayıtlarını eklememeniz durumunda yapılandırma tamamlanamaz.
>


## <a name="configuring-federation-with-pingfederate"></a>PingFederate ile federasyonu yapılandırma
PingFederate 'i yalnızca birkaç tıklamayla Azure AD Connect ile yapılandırabilirsiniz. Aşağıdaki Önkoşullar gereklidir:
- PingFederate 8,4 veya sonraki bir sürümü.  Daha fazla bilgi için bkz. [Azure Active Directory ve Microsoft 365 Ile Pingfederate tümleştirmesi](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- Kullanmayı düşündüğünüz Federasyon Hizmeti adı için bir TLS/SSL sertifikası (örneğin, sts.contoso.com).

### <a name="verify-the-domain"></a>Etki alanını doğrulama
PingFederate 'i kullanarak Federasyonu ayarlamayı seçtikten sonra, Federasyona eklemek istediğiniz etki alanını doğrulamanız istenir.  Açılır menüden etki alanını seçin.

!["Azure A etki alanı" sayfasını gösteren ekran görüntüsü. "Contoso.com" örnek etki alanı seçilidir.](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>PingFederate ayarlarını dışarı aktarma


Her bir federasyon Azure etki alanı için Federasyon sunucusu olarak PingFederate 'i yapılandırın.  Bu bilgileri PingFederate yöneticisiyle paylaşmak için **ayarları dışarı aktar** ' ı seçin.  Federasyon sunucusu Yöneticisi yapılandırmayı güncelleştirir ve ardından Azure AD Connect meta veri ayarlarını doğrulayabilmesi için PingFederate sunucu URL 'sini ve bağlantı noktası numarasını sağlar.  

!["PingFederate ayarları" sayfasını gösteren ekran görüntüsü. Sayfanın üst kısmındaki "ayarları dışarı aktar" düğmesi görünür.](./media/how-to-connect-install-custom/ping2.png)

Doğrulama sorunlarınızı çözmek için PingFederate yöneticinize başvurun.  Aşağıdaki görüntüde, Azure ile geçerli bir güven ilişkisine sahip olmayan bir PingFederate sunucusuyla ilgili bilgiler gösterilmektedir.

![Sunucu bilgilerini gösteren ekran görüntüsü: PingFederate sunucusu bulundu, ancak Azure için hizmet sağlayıcı bağlantısı eksik veya devre dışı.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Federasyon bağlantısını doğrulama
Azure AD Connect, önceki adımda yer alan PingFederate meta verilerinden aldığı kimlik doğrulama uç noktalarını doğrulamaya çalışır.  Azure AD Connect ilk olarak yerel DNS sunucularınızı kullanarak uç noktaları çözümlemeye çalışır.  Sonra, dış bir DNS sağlayıcısı kullanarak uç noktaları çözümlemeye çalışır.  Doğrulama sorunlarınızı çözmek için PingFederate yöneticinize başvurun.  

!["Bağlantıyı doğrula" sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Federasyon oturum açmayı doğrulama
Son olarak, federasyon etki alanında oturum açarak yeni yapılandırılan federasyon oturum açma akışını doğrulayabilirsiniz. Oturum açma işlemi başarılı olursa, PingFederate ile Federasyon başarıyla yapılandırılır.

!["Federe oturum açmayı Doğrula" sayfasını gösteren ekran görüntüsü. En alttaki ileti, başarılı bir oturum açma olduğunu gösterir.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Yapılandırma ve doğrulama sayfaları
**Yapılandırma yapılandırma sayfasında gerçekleşir** .

> [!NOTE]
> Federasyonu yapılandırdıysanız, yüklemeye devam etmeden önce [Federasyon sunucuları Için ad çözümlemesi](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) de yapılandırdığınızdan emin olun.
>



!["Yapılandırmaya hazırlanın" sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Hazırlama modunu kullan
Hazırlama moduyla paralel olarak yeni bir eşitleme sunucusu kurmak mümkündür. Bu kurulumu kullanmak istiyorsanız, buluttaki tek bir dizine yalnızca bir eşitleme sunucusu dışarı aktarabilirsiniz. Ancak başka bir sunucudan taşımak istiyorsanız (örneğin, DirSync çalıştıran bir sunucu), hazırlama modunda Azure AD Connect etkinleştirebilirsiniz. 

Hazırlama kurulumunu etkinleştirdiğinizde, eşitleme altyapısı verileri normal olarak içeri aktarır ve eşitler. Ancak, Azure AD 'ye veya Active Directory hiçbir veri dışa aktarır. Hazırlama modunda parola eşitleme özelliği ve parola geri yazma özelliği devre dışıdır.

!["Hazırlama modunu etkinleştir" seçeneğini gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/stagingmode.png)

Hazırlama modunda, eşitleme altyapısında gerekli değişiklikleri yapabilir ve nelerin aktarılacağını inceleyebilirsiniz. Yapılandırmayla ilgili bir sorun yoksa yükleme sihirbazını tekrar çalıştırın ve hazırlama modunu devre dışı bırakın. 

Veriler artık sunucudan Azure AD 'ye aktarılmıştır. Yalnızca bir sunucunun etkin şekilde dışarı aktarma işlemi gerçekleştirmesini sağlamak için, diğer sunucuyu devre dışı bıraktığınızdan emin olun.

Daha fazla bilgi için bkz. [Hazırlama modu](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Federasyon yapılandırmanızı doğrulama
Azure AD Connect **Doğrula** DÜĞMESINI seçtiğinizde DNS ayarlarını doğrular. Aşağıdaki ayarları denetler:

* **Intranet bağlantısı**
    * Federasyon FQDN 'sini çözümle: Azure AD Connect, DNS 'in bağlantı sağlamak için Federasyon FQDN 'sini çözümleyip çözemeyeceğini denetler. Azure AD Connect FQDN 'yi çözümleyemezse doğrulama başarısız olur. Doğrulamayı gerçekleştirmek için, Federasyon hizmeti FQDN 'SI için bir DNS kaydı bulunduğundan emin olun.
    * DNS A kaydı: Federasyon hizmetinizin bir kayıt olup olmadığını denetler Azure AD Connect. Bir kayıt yokluğunda doğrulama başarısız olur. Doğrulamayı gerçekleştirmek için, Federasyon FQDN 'niz için bir kayıt (CNAME kaydı değil) oluşturun.
* **Extranet bağlantısı**
    * Federasyon FQDN 'sini çözümle: Azure AD Connect, DNS 'in bağlantı sağlamak için Federasyon FQDN 'sini çözümleyip çözemeyeceğini denetler.

      !["Yükleme Tamam" sayfasını gösteren ekran görüntüsü.](./media/how-to-connect-install-custom/completed.png)

      !["Yükleme Tamam" sayfasını gösteren ekran görüntüsü. Bir ileti, intranet yapılandırmasının doğrulandığını gösterir.](./media/how-to-connect-install-custom/adfs7.png)

Uçtan uca kimlik doğrulamayı doğrulamak için aşağıdaki testlerin bir veya daha fazlasını el ile gerçekleştirin:

* Eşitleme tamamlandığında, Azure AD Connect ' de, seçtiğiniz şirket içi kullanıcı hesabının kimlik doğrulamasını doğrulamak için **Federasyon oturum açmayı doğrula** ek görevini kullanın.
* İntranetteki etki alanına katılmış bir makineden, bir tarayıcıdan oturum açabildiğinizden emin olun. Bağlantısını yapın https://myapps.microsoft.com . Sonra oturum açmayı doğrulamak için oturum açmış hesabınızı kullanın. Yerleşik Azure AD DS yönetici hesabı eşitlenmez ve doğrulama için kullanamazsınız.
* Extranet 'teki bir cihazdan oturum açabildiğinizden emin olun. Bir giriş makinesinde veya bir mobil cihazda ' e bağlanın https://myapps.microsoft.com . Ardından kimlik bilgilerinizi sağlayın.
* Zengin istemci oturumu açma işlemini doğrulayın. Bağlantısını yapın https://testconnectivity.microsoft.com . Ardından **Office 365**  >  **Office 365 tek Sign-On test**' i seçin.

## <a name="troubleshoot"></a>Sorun giderme
Bu bölüm, Azure AD Connect yüklenirken bir sorununuz varsa kullanabileceğiniz sorun giderme bilgilerini içerir.

Bir Azure AD Connect yüklemesini özelleştirdiğinizde, **gerekli bileşenleri yükleme** sayfasında, **var olan bir SQL Server kullan** seçeneğini belirleyebilirsiniz. Şu hatayla karşılaşabilirsiniz: "ADSync veritabanı zaten veri içeriyor ve üzerine yazılamaz. Lütfen var olan veritabanını kaldırın ve yeniden deneyin. "

!["Gerekli bileşenleri yüklensin" sayfasını gösteren ekran görüntüsü. Sayfanın alt kısmında bir hata görünür.](./media/how-to-connect-install-custom/error1.png)

Belirttiğiniz SQL Server SQL örneğinde *ADSync* adlı bir veritabanı zaten var olduğundan bu hatayı görürsünüz.

Azure AD Connect kaldırıldıktan sonra genellikle bu hatayı görürsünüz.  Veritabanı, Azure AD Connect kaldırdığınızda SQL Server çalıştıran bilgisayardan silinmez.

Bu sorunu gidermek için:

1. Kaldırılmadan önce Azure AD Connect kullanılan ADSync veritabanını denetleyin. Veritabanının artık kullanılmadığından emin olun.

2. Veritabanını yedekleyin.

3. Veritabanını silin:
    1. SQL örneğine bağlanmak için **Microsoft SQL Server Management Studio** kullanın. 
    1. **ADSync** veritabanını bulun ve sağ tıklayın.
    1. Bağlam menüsünde **Sil**' i seçin.
    1. Veritabanını silmek için **Tamam ' ı** seçin.

![Microsoft SQL Server Management Studio gösteren ekran görüntüsü. D eşitlemesi seçildi.](./media/how-to-connect-install-custom/error2.png)

ADSync veritabanını sildikten sonra yüklemeyi yeniden denemek için **yükleme** ' yi seçin.

## <a name="next-steps"></a>Sonraki adımlar
Yükleme bittikten sonra, Windows oturumunu kapatın. Synchronization Service Manager veya eşitleme kuralı düzenleyicisini kullanmadan önce tekrar oturum açın.

Azure AD Connect yükleolduğunuza göre, [yükleme ve lisansları atama](how-to-connect-post-installation.md)' yı doğrulayabilirsiniz.

Yükleme sırasında etkinleştirdiğiniz özellikler hakkında daha fazla bilgi için bkz. [yanlışlıkla silmeleri](how-to-connect-sync-feature-prevent-accidental-deletes.md) ve [Azure AD Connect Health](how-to-connect-health-sync.md)önleme.

Diğer yaygın konular hakkında daha fazla bilgi için bkz. [Azure AD Connect eşitleme: Zamanlayıcı](how-to-connect-sync-feature-scheduler.md) ve şirket [Içi KIMLIKLERINIZI Azure AD ile tümleştirme](whatis-hybrid-identity.md).
