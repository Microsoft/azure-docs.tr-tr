---
title: Azure Multi-Factor Authentication SSS-Azure Active Directory
description: Azure Multi-Factor Authentication ile ilgili sık sorulan sorular ve yanıtlar.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37f375b6d4284b4728b2337dc5ab5186ce22772c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167773"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication hakkında sık sorulan sorular

Bu SSS, Azure Multi-Factor Authentication ve Multi-Factor Authentication hizmetini kullanarak ilgili sık sorulan soruları yanıtlar. Genel, faturalandırma modelleri, kullanıcı deneyimleri ve sorun giderme hizmeti hakkındaki sorulara bölünmüştür.

## <a name="general"></a>Genel

**S: Azure Multi-Factor Authentication Sunucusu Kullanıcı verilerini nasıl işler?**

Multi-Factor Authentication Sunucusu, Kullanıcı verileri yalnızca şirket içi sunucularda depolanır. Kalıcı kullanıcı verileri bulutta depolanmaz. Kullanıcı iki aşamalı doğrulama gerçekleştirdiğinde, Multi-Factor Authentication Sunucusu kimlik doğrulaması için verileri Azure Multi-Factor Authentication bulut hizmetine gönderir. Multi-Factor Authentication Sunucusu ile Multi-Factor Authentication bulut hizmeti arasındaki iletişim 443 giden bağlantı noktası üzerinden Güvenli Yuva Katmanı (SSL) veya Aktarım Katmanı Güvenliği (TLS) kullanır.

Bulut hizmetine kimlik doğrulama istekleri gönderildiğinde, veriler kimlik doğrulama ve kullanım raporları için toplanır. İki adımlı doğrulama günlüklerinde bulunan veri alanları aşağıdaki gibidir:

* **BENZERSIZ kimlik** (Kullanıcı adı ya da şirket ıçı Multi-Factor Authentication sunucusu kimliği)
* **First ve Last adı** (isteğe bağlı)
* **E-posta adresi** (isteğe bağlı)
* **Telefon numarası** (bir sesli arama veya SMS kimlik doğrulaması kullanırken)
* **Cihaz belirteci** (mobil uygulama kimlik doğrulaması kullanılırken)
* **Kimlik doğrulama modu**
* **Kimlik doğrulama sonucu**
* **Multi-Factor Authentication Sunucusu adı**
* **Multi-Factor Authentication Sunucusu IP**
* **İstemci IP 'si** (varsa)

İsteğe bağlı alanlar Multi-Factor Authentication Sunucusu yapılandırılabilir.

Doğrulama sonucu (başarı veya reddetme) ve reddedilme nedeni kimlik doğrulama verileriyle birlikte depolanır. Bu veriler, kimlik doğrulama ve kullanım raporlarında kullanılabilir.

**S: kullanıcılarıma SMS iletileri göndermek için hangi SMS kısa kodları kullanılır?**

Birleşik Devletler, Microsoft aşağıdaki SMS kısa kodlarını kullanır:

   * 97671
   * 69829
   * 51789
   * 99399

Kanada 'daki Microsoft, aşağıdaki SMS kısa kodlarını kullanır:

   * 759731 
   * 673801

Microsoft, tutarlı SMS veya sesli tabanlı Multi-Factor Authentication istemi teslimini aynı numarayla garanti etmez. Kullanıcılarımız konusunda, Microsoft, SMS teslimat yeteneğini geliştirmek üzere rota ayarlamaları yaptığımız için, her zaman kısa kodlar ekleyebilir veya kaldırabilir. Microsoft, Birleşik Devletler ve Kanada yanı sıra ülkeler/bölgeler için kısa kodları desteklemez.

## <a name="billing"></a>Faturalandırma

Çoğu faturalandırma sorusu, [Multi-Factor Authentication fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) veya [Azure Multi-Factor Authentication alma](concept-mfa-licensing.md)hakkında belgelere başvurarak cevaplanır.

**S: Kuruluşum, kimlik doğrulaması için kullanılan telefon çağrılarını ve SMS iletilerini göndermek için ücretlendiriyor mu?**

Hayır, Azure Multi-Factor Authentication aracılığıyla kullanıcılara gönderilen tek tek telefon aramaları veya SMS iletileri için ücretlendirilirsiniz. Kimlik doğrulaması başına MFA sağlayıcısı kullanıyorsanız, her kimlik doğrulaması için faturalandırılır, ancak kullanılan yöntem için bu desteklenmez.

Kullanıcılarınız, kendi kişisel telefon hizmetine göre aldıkları telefon aramaları veya mesaj iletileri için ücretlendirilir.

**S: Kullanıcı başına faturalandırma modeli, etkin olan tüm kullanıcılar için mi, yoksa yalnızca iki adımlı doğrulama gerçekleştirenler için mi ücretlendirilir?**

Faturalandırma, ayda iki adımlı doğrulama gerçekleştirdiklerinden bağımsız olarak Multi-Factor Authentication kullanmak üzere yapılandırılan kullanıcı sayısını temel alır.

**S: Multi-Factor Authentication Faturalandırma nasıl çalışır?**

Kullanıcı başına veya kimlik doğrulaması başına MFA sağlayıcısı oluşturduğunuzda, kuruluşunuzun Azure aboneliği kullanım için aylık olarak faturalandırılır. Bu Faturalandırma modeli, Azure 'un sanal makinelerin ve Web sitelerinin kullanımıyla ilgili olarak benzerdir.

Azure Multi-Factor Authentication için bir abonelik satın aldığınızda, kuruluşunuz yalnızca her bir kullanıcı için yıllık lisans ücretini ödersiniz. MFA lisansları ve Office 365, Azure AD Premium veya Enterprise Mobility + Security paketleri bu şekilde faturalandırılır. 

[Azure Multi-Factor Authentication alma](concept-mfa-licensing.md)seçenekleriniz hakkında daha fazla bilgi edinin.

**S: Azure Multi-Factor Authentication 'in ücretsiz bir sürümü var mı?**

Bazı örneklerde, evet.

Azure yöneticileri için Multi-Factor Authentication, [Azure Portal](https://portal.azure.com) ve [Microsoft 365 Yönetim Merkezi](https://admin.microsoft.com)de dahil olmak üzere Microsoft çevrimiçi hizmetler ERIŞIM maliyeti olmadan Azure MFA özelliklerinin bir alt kümesini sunar. Bu teklif yalnızca bir MFA lisansı, paket veya tek başına tüketim tabanlı bir sağlayıcı aracılığıyla Azure MFA 'nın tam sürümüne sahip olmayan Azure Active Directory örneklerinde Genel Yöneticiler için geçerlidir. Yöneticileriniz ücretsiz sürümü kullanıyorsa ve Azure MFA 'nın tam sürümünü satın alırsanız tüm genel Yöneticiler otomatik olarak ücretli sürüme yükseltilir.

Office 365 kullanıcıları için Multi-Factor Authentication, Exchange Online ve SharePoint Online dahil olmak üzere Office 365 hizmetlerine erişim için ücretsiz olarak Azure MFA özelliklerinin bir alt kümesini sunmaktadır. Bu teklif, bir Office 365 lisansı atanmış kullanıcılar için geçerlidir. buna karşılık gelen Azure Active Directory örneği, MFA lisansı, paket veya tek başına tüketim tabanlı bir sağlayıcı aracılığıyla Azure MFA 'nın tam sürümüne sahip değildir.

**S: Kuruluşum, her zaman Kullanıcı başına ve kimlik doğrulaması başına tüketim faturalama modelleri arasında geçiş yapabilir mi?**

Kuruluşunuz, tüketim tabanlı faturalandırma ile tek başına bir hizmet olarak MFA satın alıyorsa, MFA sağlayıcısı oluştururken bir faturalandırma modeli seçersiniz. MFA sağlayıcısı oluşturulduktan sonra faturalandırma modelini değiştiremezsiniz. 

MFA sağlayıcınız bir Azure AD kiracısına bağlı *değilse* veya yeni MFA sağlayıcısını farklı bır Azure AD kiracısına bağlarsanız, Kullanıcı ayarları ve yapılandırma seçenekleri aktarılmaz. Ayrıca, yeni MFA Sağlayıcısı ile oluşturulan etkinleştirme kimlik bilgileri kullanılarak mevcut Azure MFA Sunucularının yeniden etkinleştirilmesi gerekir. MFA Sunucularını yeni MFA Sağlayıcısına bağlamak için yeniden etkinleştirmek, telefon çağrısı ve kısa mesaj kimlik doğrulamasını etkilemez, ancak mobil uygulama etkinleştirilinceye kadar tüm kullanıcılar için mobil uygulama bildirimleri çalışmaz.

[Azure Multi-Factor auth sağlayıcısı ile çalışmaya](concept-mfa-authprovider.md)başlama bölümünde MFA sağlayıcıları hakkında daha fazla bilgi edinin.

**S: Kuruluşum, tüketim tabanlı faturalandırma ve abonelikler arasında (lisans tabanlı bir model) istediğiniz zaman geçiş yapabilir mi?**

Bazı örneklerde, evet.

Dizininizde *Kullanıcı başına* Azure Multi-Factor Authentication sağlayıcısı varsa MFA lisansları ekleyebilirsiniz. Lisansları olan kullanıcılar, Kullanıcı başına tüketim tabanlı faturalandırma tarafından sayılmaz. Lisansları olmayan kullanıcılar MFA sağlayıcısı aracılığıyla MFA için de etkinleştirilebilir. Multi-Factor Authentication kullanmak üzere yapılandırılmış tüm kullanıcılarınız için lisans satın alıp atarsanız, Azure Multi-Factor Authentication sağlayıcısını silebilirsiniz. Gelecekte lisanslarından daha fazla kullanıcınız varsa, her zaman bir Kullanıcı başına MFA sağlayıcısı oluşturabilirsiniz.

Dizininizde bir *kimlik doğrulaması başına* Azure Multi-Factor Authentication sağlayıcısı varsa, MFA sağlayıcısı aboneliğinize bağlı olduğu sürece her bir kimlik doğrulaması için her zaman faturalandırılırsınız. Kullanıcılara MFA lisansları atayabilirsiniz, ancak her iki adımlı doğrulama isteği için faturalandırılırsınız ve bu, bir MFA lisansı atanmış birisinden gelir.

**S: Kuruluşumun Azure Multi-Factor Authentication kullanmak için kimlikleri kullanması ve eşitlenmesi gerekir mi?**

Kuruluşunuz tüketim tabanlı bir faturalandırma modeli kullanıyorsa, Azure Active Directory isteğe bağlıdır, ancak gerekli değildir. MFA sağlayıcınız bir Azure AD kiracısına bağlı değilse, yalnızca şirket içi Azure Multi-Factor Authentication Sunucusu dağıtabilirsiniz.

Lisans modeli için Azure Active Directory gereklidir çünkü bu lisansları, satın alırken ve dizindeki kullanıcılara atadığınızda Azure AD kiracısına eklenir.

## <a name="manage-and-support-user-accounts"></a>Kullanıcı hesaplarını yönetme ve destekleme

**S: Kullanıcılarımı telefonlarına yanıt almadıklarında ne söylemem gerekir?**

Kullanıcılarınızın kimlik doğrulaması için telefon araması veya SMS almak için 5 dakika içinde 5 kez deneme yapmaya çalışın. Microsoft, çağrılar ve SMS iletileri göndermek için birden çok sağlayıcı kullanır. Bu işe yaramazsa, daha fazla sorun gidermek için lütfen Microsoft ile bir destek talebi açın.

Yukarıdaki adımlar çalışmadıysa, tüm kullanıcılarınız birden fazla doğrulama yöntemi yapılandırdıysa. Kullanıcılarınıza oturum açma sayfasında farklı bir doğrulama yöntemini seçerek yeniden oturum açmayı denemesini söyleyin.

Kullanıcılarınızın [Son Kullanıcı sorun giderme kılavuzuna](../user-help/multi-factor-authentication-end-user-troubleshoot.md)işaret edebilirsiniz.

**S: Kullanıcılarım hesabında yoksa ne yapmam gerekir?**

Kullanıcının hesabını, kayıt işlemini tekrar ilerleyerek sıfırlayabilirsiniz. [Bulutta Azure Multi-Factor Authentication ile Kullanıcı ve cihaz ayarlarını yönetme](howto-mfa-userdevicesettings.md)hakkında daha fazla bilgi edinin.

**S: kullanıcılarımın biri uygulama parolalarını kullanan bir telefonu kaybederse ne yapmam gerekir?**

Yetkisiz erişimi engellemek için kullanıcının tüm uygulama parolalarını silin. Kullanıcı bir değiştirme cihazına sahip olduktan sonra parolaları yeniden oluşturabilir. [Bulutta Azure Multi-Factor Authentication ile Kullanıcı ve cihaz ayarlarını yönetme](howto-mfa-userdevicesettings.md)hakkında daha fazla bilgi edinin.

**S: Kullanıcı tarayıcı olmayan uygulamalarda oturum açamaz ne olursa?**

Kuruluşunuz hala eski istemcileri kullanıyorsa ve [uygulama parolalarının kullanımına izin](howto-mfa-mfasettings.md#app-passwords)verildiyse, kullanıcılarınız Kullanıcı adı ve parolasıyla bu eski istemcilerde oturum açamaz. Bunun yerine, [uygulama parolalarını ayarlamaları](../user-help/multi-factor-authentication-end-user-app-passwords.md)gerekir. Kullanıcılarınızın kendi oturum açma bilgilerini temizleme (silme), uygulamayı yeniden başlatması ve sonra kendi Kullanıcı adı ve *uygulama parolalarıyla* normal parolaları yerine oturum açması gerekir.

Kuruluşunuzda eski istemciler yoksa, kullanıcılarınızın uygulama parolaları oluşturmalarına izin vermeniz gerekir.

> [!NOTE]
> Office 2013 istemcileri için modern kimlik doğrulaması
>
> Uygulama parolaları yalnızca modern kimlik doğrulamayı desteklemeyen uygulamalar için gereklidir. Office 2013 istemcileri modern kimlik doğrulama protokollerini destekler, ancak yapılandırılması gerekir. Artık modern kimlik doğrulama, Office 2013 için Mart 2015 veya sonraki bir güncelleştirmeyi çalıştıran tüm müşteriler tarafından kullanılabilir. Daha fazla bilgi için bkz. [Office 365 modern kimlik doğrulaması güncelleştirilmiş](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)Web günlüğü.

**S: Kullanıcılarım bazen kısa mesaj almadıklarında veya doğrulama zaman aşımına uğrar.**

Hizmetin güvenilirliğini etkileyebilecek denetlenebilir faktörlere sahip olduğundan SMS iletilerinin teslimi garanti edilmez. Bu etkenlere, hedef ülke/bölge, cep telefonu taşıyıcısı ve sinyal gücü dahildir.

Kullanıcılarınız genellikle SMS mesajları güvenilir bir şekilde almaya yönelik sorunlar yaşıyorsanız, bunun yerine mobil uygulama veya telefon araması yöntemini kullanmasını söyleyin. Mobil uygulama, hem hücresel hem de Wi-Fi bağlantılarında bildirim alabilir. Ayrıca, mobil uygulama, cihazın hiç sinyali olmadığında bile doğrulama kodları oluşturabilir. Microsoft Authenticator uygulaması [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [IOS](https://go.microsoft.com/fwlink/?Linkid=825073)ve [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)için kullanılabilir.

**S: sistem zaman aşımına uğramadan önce kullanıcılarınızın bir SMS iletisinden doğrulama kodunu girmesi gereken süre miktarını değiştirebilir miyim?**

Bazı durumlarda, evet. 

Azure MFA Server v 7.0 veya üzeri bir tek yönlü SMS için bir kayıt defteri anahtarı ayarlayarak zaman aşımı ayarını yapılandırabilirsiniz. MFA bulut hizmeti SMS iletisini gönderdikten sonra, MFA sunucusuna doğrulama kodu (veya bir kerelik geçiş kodu) döndürülür. MFA sunucusu, kodu varsayılan olarak 300 saniye için belleğe depolar. Kullanıcı, 300 saniye geçtikten sonra kodu girmezse, kimlik doğrulaması reddedilir. Varsayılan zaman aşımı ayarını değiştirmek için bu adımları kullanın:

1. Hklm\software\wow6432node\pozitif Networks\phonefactora gidin.
2. **Pfsvc_pendingSmsTimeoutSeconds** ADLı bir DWORD kayıt defteri anahtarı oluşturun ve Azure MFA sunucusunun bir kerelik geçiş kodlarını depolamasını istediğiniz süreyi saniye cinsinden ayarlayın.

>[!TIP] 
>Birden çok MFA sunucunuz varsa, yalnızca özgün kimlik doğrulama isteğini işleyen bir Kullanıcı, kullanıcıya gönderilen doğrulama kodunu bilir. Kullanıcı koda girdiğinde, doğrulama isteğinin aynı sunucuya gönderilmesi gerekir. Kod doğrulaması farklı bir sunucuya gönderilirse, kimlik doğrulaması reddedilir. 

Kullanıcılar, tanımlanan zaman aşımı süresi içinde SMS 'ye yanıt vermezse, kimlik doğrulaması reddedilir. 

Bulutta Azure MFA ile tek yönlü bir SMS için (AD FS bağdaştırıcısı veya ağ Ilkesi sunucu uzantısı dahil), zaman aşımı ayarını yapılandıramazsınız. Azure AD, 180 saniyelik doğrulama kodunu depolar. 

**S: Azure Multi-Factor Authentication Sunucusu ile donanım belirteçlerini kullanabilir miyim?**

Azure Multi-Factor Authentication Sunucusu kullanıyorsanız, üçüncü taraf açık kimlik doğrulaması (OATH) zaman tabanlı, bir kerelik parola (TOTP) belirteçlerini içeri aktarabilir ve ardından bunları iki adımlı doğrulama için kullanabilirsiniz.

Gizli anahtarı bir CSV dosyasına yerleştirip Azure Multi-Factor Authentication Sunucusu içeri aktarırsanız, OATH TOTP belirteçleri olan Activeıdentity belirteçlerini kullanabilirsiniz. İstemci sistem kullanıcı girişini kabul edebileceği sürece Active Directory Federasyon Hizmetleri (AD FS) (ADFS), Internet Information Server (IIS) form tabanlı kimlik doğrulaması ve Arayan Kullanıcının Uzaktan Kimliğini Doğrulama Hizmeti (RADIUS) ile OATH belirteçleri kullanabilirsiniz.

Üçüncü taraf OATH TOTP belirteçlerini aşağıdaki biçimleriyle içeri aktarabilirsiniz:  

- Taşınabilir simetrik anahtar kapsayıcısı (PSKC)  
- Dosya bir seri numarası, temel 32 biçiminde bir gizli anahtar ve bir zaman aralığı içeriyorsa CSV  

**S: Terminal Hizmetleri 'ni güvenli hale getirmek için Azure Multi-Factor Authentication Sunucusu kullanabilir miyim?**

Evet, ancak Windows Server 2012 R2 veya sonraki bir sürümünü kullanıyorsanız, Terminal Hizmetleri 'ni yalnızca Uzak Masaüstü Ağ Geçidi (RD Ağ Geçidi) kullanarak güvenli hale getirebilirsiniz.

Windows Server 2012 R2 'deki güvenlik değişiklikleri, Azure Multi-Factor Authentication Sunucusu 'nin Windows Server 2012 ve önceki sürümlerde yerel güvenlik yetkilisi (LSA) Güvenlik paketine bağlanmasını değiştirdi. Windows Server 2012 veya önceki sürümlerde Terminal Hizmetleri sürümleri için [bir uygulamayı Windows kimlik doğrulaması ile güvenli hale](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)getirebilirsiniz. Windows Server 2012 R2 kullanıyorsanız, RD Ağ Geçidi gerekir.

**S: MFA sunucusunda arayan KIMLIĞINI yapılandırdım, ancak Kullanıcılarım anonim bir arayandan Multi-Factor Authentication çağrılar alıyor.**

Multi-Factor Authentication çağrıları genel telefon ağı üzerinden yerleştirildiğinde, bazen arayan KIMLIĞINI desteklemeyen bir taşıyıcı aracılığıyla yönlendirilir. Bu nedenle, Multi-Factor Authentication sistemi her zaman tarafından gönderilse de arayan KIMLIĞI garanti edilmez.

**S: Kullanıcılarım neden güvenlik bilgilerini kaydetmeliyim?**
Kullanıcılardan güvenlik bilgilerini kaydetmesi istenmesinin çeşitli nedenleri vardır:

- Kullanıcı, Azure AD 'de yöneticileri tarafından MFA için etkinleştirildi, ancak henüz hesabı için güvenlik bilgilerine kaydolmadı.
- Kullanıcı, Azure AD 'de self servis parola sıfırlama için etkinleştirildi. Güvenlik bilgileri, daha sonra unutmaları durumunda parolalarını sıfırlamasına yardımcı olur.
- Kullanıcı MFA 'yı zorunlu kılmak için koşullu erişim ilkesine sahip bir uygulamaya erişti ve daha önce MFA için kaydedilmemiş.
- Kullanıcı Azure AD 'ye (Azure AD katılımı dahil) sahip bir cihaz kaydediyor ve kuruluşunuz cihaz kaydı için MFA gerektiriyor, ancak Kullanıcı daha önce MFA 'ya kaydolmadı.
- Kullanıcı Windows 10 ' da (MFA gerektirir) Iş için Windows Hello 'Yu oluşturuyor ve daha önce MFA için kaydedilmemiş.
- Kuruluş, kullanıcıya uygulanan bir MFA kayıt ilkesi oluşturup etkinleştirdi.
- Kullanıcı MFA için önceden kaydoldu, ancak yöneticinin devre dışı bırakılmasından sonra bir doğrulama yöntemi seçti. Bu nedenle, yeni bir varsayılan doğrulama yöntemi seçmek için kullanıcının MFA kaydını yeniden geçmesi gerekir.

## <a name="errors"></a>Hatalar

**S: mobil uygulama bildirimleri kullanılırken kullanıcılar "etkinleştirilmiş bir hesap için kimlik doğrulama isteği değildir" hata iletisi görür.**

Bu yordama, hesaplarını mobil uygulamadan kaldırmak ve sonra yeniden eklemek için bu yordamı takip etmek istediğinizi söyleyin:

1. [Azure Portal profilinize](https://account.activedirectory.windowsazure.com/profile/) gidip kurumsal hesabınızla oturum açın.
2. **Ek güvenlik doğrulaması**' nı seçin.
3. Mevcut hesabı mobil uygulamadan kaldırın.
4. **Yapılandır**' a tıklayın ve ardından mobil uygulamayı yeniden yapılandırmak için yönergeleri izleyin.

**S: tarayıcı olmayan bir uygulamada oturum açarken kullanıcılara bir 0x800434D4L hata iletisi görürseniz ne yapması gerekir?**

Bir yerel bilgisayara yüklenmiş ve iki adımlı doğrulama gerektiren hesaplarla çalışmayan bir tarayıcı olmayan uygulamada oturum açmaya çalıştığınızda 0x800434D4L hatası oluşur.

Bu hata için geçici bir çözüm, yönetici ile ilgili ve yönetici olmayan işlemler için ayrı kullanıcı hesaplarına sahip olmalarıdır. Daha sonra, yönetici olmayan hesabınızı kullanarak Outlook 'ta oturum açabilmeniz için yönetici hesabınız ve yönetici olmayan hesap arasında posta kutuları bağlayabilirsiniz. Bu çözüm hakkında daha fazla ayrıntı için, yöneticiye bir [kullanıcının posta kutusunun içeriğini açma ve görüntüleme yeteneği verme](https://help.outlook.com/141/gg709759.aspx?sl=1)hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

Sorunuz burada yanıtlanmazsa, lütfen sayfanın altındaki açıklamalarda bırakın. Veya yardım almak için bazı ek seçenekler aşağıda verilmiştir:

* [Microsoft desteği bilgi bankasında](https://support.microsoft.com) , yaygın teknik sorunların çözümlerini arayın.
* Topluluk aracılığıyla teknik sorular ve yanıtlar arayın ve bunları inceleyin veya [Azure Active Directory forumlarında](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)kendi sorunuzu sorun.
* Eski bir PhoneFactor müşterisiyseniz ve sorularınız varsa veya bir parolayı sıfırlamanıza yardımcı olması gerekiyorsa, bir destek talebi açmak için [parola sıfırlama](mailto:phonefactorsupport@microsoft.com) bağlantısını kullanın.
* [Azure Multi-Factor Authentication sunucusu (PhoneFactor) desteğiyle](https://support.microsoft.com/oas/default.aspx?prid=14947)destek uzmanıyla iletişim kurun. Bizimle iletişim kurarken, sorun hakkında mümkün olduğunca fazla bilgi dahil edebiliyorsanız bu yararlı olur. Sağlayabileceğiniz bilgiler, hatayı gördüğünüz sayfayı, belirli hata kodunu, belirli oturum KIMLIĞINI ve hatayı seçen kullanıcının KIMLIĞINI içerir.
