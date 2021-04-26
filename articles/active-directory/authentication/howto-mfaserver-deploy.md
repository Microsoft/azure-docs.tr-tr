---
title: Azure MFA sunucusu ile çalışmaya başlama-Azure Active Directory
description: Adım adım Azure MFA Server şirket içi kullanmaya başlama
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7bd9913cccbe077a4deed9a7c5bfdc601f3dd5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742349"
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Sunucusu’nu kullanmaya başlama

<center>

![Şirket içi MFA sunucusu ile çalışmaya başlama](./media/howto-mfaserver-deploy/server2.png)</center>

Bu sayfa yeni bir sunucu yüklemeyi ve şirket içi Active Directory’de kurulumunu yapmayı ele alır. MFA sunucusu zaten yüklüyse ve yükseltmek istiyorsanız bkz. [En yeni Azure Multi-Factor Authentication Sunucusu’na yükseltme](howto-mfaserver-deploy-upgrade.md). Yalnızca web hizmetini yükleme hakkında bilgi almak istiyorsanız bkz. [Azure Multi-Factor Authentication Sunucusu Mobil Uygulama Web Hizmeti’ni dağıtma](howto-mfaserver-deploy-mobileapp.md).

> [!IMPORTANT]
> 1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sağlamamaktadır. Oturum açma olayları sırasında çok faktörlü kimlik doğrulaması (MFA) gerektirmek isteyen yeni müşteriler, bulut tabanlı Azure AD Multi-Factor Authentication kullanmalıdır.
>
> Bulut tabanlı MFA 'yı kullanmaya başlamak için bkz. [öğretici: Azure AD Multi-Factor Authentication Ile güvenli Kullanıcı oturum açma olayları](tutorial-enable-azure-mfa.md).
>
> MFA sunucusunu 1 Temmuz 2019 tarihinden önce etkinleştiren mevcut müşteriler, en son sürümü, gelecekteki güncelleştirmeleri indirebilir ve her zamanki gibi etkinleştirme kimlik bilgilerini oluşturabilir.

## <a name="plan-your-deployment"></a>Dağıtımınızı planlama

Azure Multi-Factor Authentication Sunucusu'nu indirmeden önce yük ve yüksek kullanılabilirlik gereksinimlerinizi göz önünde bulundurun. Bu bilgileri kullanarak nasıl ve nereye dağıtım gerçekleştireceğinize karar verin.

Düzenli olarak kimlik doğrulaması yapmasını beklediğiniz kullanıcı sayısı, ihtiyacınız olan bellek miktarı için iyi bir yol gösterici olabilir.

| Kullanıcılar | RAM |
| ----- | --- |
| 1-10.000 | 4 GB |
| 10.001-50.000 | 8 GB |
| 50.001-100.000 | 12 GB |
| 100.000-200.001 | 16 GB |
| 200.001+ | 32 GB |

Yüksek kullanılabilirlik veya yük dengeleme için birden çok sunucu ayarlamanız mı gerekiyor? Azure MFA Sunucusu ile bu yapılandırmayı ayarlamanın birçok yolu vardır. İlk Azure MFA Sunucunuzu yüklediğinizde bu, ana sunucunuz olur. Diğer tüm sunucular, alt sunucu haline gelir ve kullanıcılarının yanı sıra yapılandırmayı ana sunucuyla otomatik olarak eşitler. Ardından, bir birincil sunucu yapılandırabilir ve geri kalanları yedek sunucu olarak belirleyebilir veya tüm sunucular arasında yük dengeleme ayarlayabilirsiniz.

Ana MFA Sunucusu çevrimdışı olsa bile alt sunucular iki aşamalı doğrulama isteklerini işleyebilir. Ancak yeni kullanıcı ekleyemezsiniz ve mevcut kullanıcılar, ana sunucu yeniden çevrimiçi olana veya alt sunucu yükseltilene kadar ayarlarını güncelleştiremezler.

### <a name="prepare-your-environment"></a>Ortamınızı hazırlama

Azure Multi-Factor Authentication için kullandığınız sunucunun aşağıdaki gereksinimleri karşıladığından emin olun:

| Azure Multi-Factor Authentication Sunucusu Gereksinimleri | Description |
|:--- |:--- |
| Donanım |<li>200 MB boş sabit disk alanı</li><li>x32 veya x64 özellikli işlemci</li><li>1 GB veya daha fazla RAM</li> |
| Yazılım |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008/R2 (yalnızca [ESU](/lifecycle/faq/extended-security-updates) ile)</li><li>Windows 10</li><li>Windows 8.1, tüm sürümler</li><li>Windows 8, tüm sürümler</li><li>Windows 7, tüm sürümler (yalnızca [ESU](/lifecycle/faq/extended-security-updates) ile)</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 veya üst sürümü, kullanıcı portalı veya web hizmeti SDK’sı yüklüyorsanız</li> |
| İzinler | Active Directory kaydedileceği etki alanı yöneticisi veya kuruluş yöneticisi hesabı |

### <a name="azure-mfa-server-components"></a>Azure MFA Sunucusu Bileşenleri

Azure MFA sunucusunu oluşturan üç web bileşeni şunlardır:

* Web Hizmeti SDK - Diğer bileşenlerle iletişimi etkinleştirir ve Azure MFA uygulama sunucusuna yüklenir
* Kullanıcı portalı - Kullanıcıların Azure Multi-Factor Authentication’a (MFA) kaydolmasını ve hesaplarını korumalarını sağlayan bir IIS web sitesidir.
* Mobil Uygulama Web hizmeti - Microsoft Authenticator uygulaması gibi bir mobil uygulama için iki aşamalı doğrulamayı kullanmaya olanak verir.

Sunucu İnternet'e yönelik ise, üç bileşen de aynı sunucuya yüklenebilir. Bileşenleri parçalarına ayırıyorsanız, Web hizmeti SDK'sı Azure MFA uygulama sunucusuna yüklenir ve Kullanıcı Portalı ile Mobil Uygulama Web Hizmeti İnternet'e yönelik bir sunucuya yüklenir.

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Azure Multi-Factor Authentication Sunucusu güvenlik duvarı gereksinimleri

Her MFA sunucusunun bağlantı noktası 443’te aşağıdaki adreslere giden iletişim kurabilmesi gerekir:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Giden güvenlik duvarları bağlantı noktası 443’te kısıtlı ise aşağıdaki IP adresi aralıklarını açın:

| IP Alt ağı | Ağ maskesi | IP aralığı |
|:---: |:---: |:---: |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254   |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

Olay Onayı özelliğini kullanmıyorsanız ve kullanıcılarınız şirket ağındaki cihazlardan doğrulama yapmak için mobil uygulamalar kullanmıyorsa, yalnızca aşağıdaki aralıklar gereklidir:

| IP Alt ağı | Ağ maskesi | IP aralığı |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79|
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79|
| 70.37.154.200/29 |255.255.255.248  |70.37.154.201 – 70.37.154.206  |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

## <a name="download-the-mfa-server"></a>MFA Sunucusu'nu indirme

Azure portalından Azure Multi-Factor Authentication Sunucusu'nu indirmek için aşağıdaki adımları izleyin:

> [!IMPORTANT]
> 1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sağlamamaktadır. Kullanıcılardan Multi-Factor Authentication (MFA) istemek isteyen yeni müşteriler bulut tabanlı Azure AD Multi-Factor Authentication kullanmalıdır.
>
> Bulut tabanlı MFA 'yı kullanmaya başlamak için bkz. [öğretici: Azure AD Multi-Factor Authentication Ile güvenli Kullanıcı oturum açma olayları](tutorial-enable-azure-mfa.md).
>
> MFA sunucusunu 1 Temmuz 2019 tarihinden önce etkinleştiren mevcut müşteriler, en son sürümü, gelecekteki güncelleştirmeleri indirebilir ve her zamanki gibi etkinleştirme kimlik bilgilerini oluşturabilir. Aşağıdaki adımlar yalnızca mevcut bir MFA sunucusu müşterisiyseniz çalışır.

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
2. *Azure Active Directory*'yi bulun ve seçin. **Güvenlik**  >  **MFA** seçeneğini belirleyin.
3. **Yönetıcı MFA sunucusu** altında **sunucu ayarları**' nı seçin.
4. **İndir**'i seçin ve indirme sayfasındaki talimatları izleyerek yükleyiciyi kaydedin. 

   ![MFA sunucusunu Azure portal indirin](./media/howto-mfaserver-deploy/downloadportal.png)

5. Yükleyiciyi çalıştırdıktan sonra bakacağımızdan bu sayfayı açık tutun.

## <a name="install-and-configure-the-mfa-server"></a>MFA Sunucusu'nu yükleme ve yapılandırma

Artık sunucuyu indirdiğinize göre, yükleyebilir ve yapılandırabilirsiniz. Yükleme yaptığınız sunucunun, planlama bölümünde listelenen gereksinimleri karşıladığından emin olun.

1. Yürütülebilir dosyaya çift tıklayın.
2. Yükleme Klasörünü Seçin ekranında klasörün doğru olduğundan emin olun ve **İleri**’ye tıklayın.
3. Yükleme tamamlandıktan sonra **Son**'a tıklayın. Yapılandırma sihirbazı başlatılır.
4. Yapılandırma sihirbazı karşılama ekranında **Kimlik Doğrulaması Yapılandırma Sihirbazı kullanmayı atla** seçeneğini işaretleyin ve **İleri**’ye tıklayın. Sihirbaz kapatılır ve sunucu başlatılır.

   ![Kimlik doğrulama Yapılandırma Sihirbazı 'Nı kullanarak atlayın](./media/howto-mfaserver-deploy/skip2.png)

5. Sunucuyu indirdiğiniz sayfaya dönerek, **Etkinleştirme Kimlik Bilgileri Oluştur** düğmesine tıklayın. Bu bilgileri verilen kutularda Azure MFA Sunucusu’na kopyalayın ve **Etkinleştir**’e tıklayın.

> [!NOTE]
> Azure portal etkinleştirme kimlik bilgilerini yalnızca genel Yöneticiler oluşturabiliyor.

## <a name="send-users-an-email"></a>Kullanıcılara e-posta gönderme

Kullanmaya başlamayı kolaylaştırmak için MFA sunucusunun kullanıcılarınızla iletişim kurmasına izin verin. MFA Sunucusu, iki aşamalı doğrulamaya kaydolduklarını bildirmek amacıyla kullanıcılara e-posta gönderebilir.

Gönderdiğiniz e-posta, kullanıcılarınızı iki aşamalı doğrulama için nasıl yapılandırdığınıza göre belirlenir. Örneğin, telefon numaralarını şirket dizininden alabildiyseniz, kullanıcıların beklentilerini bilebilmesi için e-posta varsayılan telefon numaralarını içermelidir. Telefon numaralarını içeri aktarmadıysanız veya kullanıcılarınız mobil uygulamayı kullanacaksa kullanıcılara hesap kaydını tamamlama yönergelerinin sağlandığı bir e-posta gönderin. E-postaya Azure Multi-Factor Authentication Kullanıcı Portalı’nın köprü bağlantısını ekleyin.

E-postanın içeriği aynı zamanda kullanıcı için ayarlanmış doğrulama yöntemine (telefonla arama, SMS veya mobil uygulama) bağlı olarak değişir. Örneğin, kullanıcının kimlik doğrularken PIN kullanması gerekiyorsa, e-posta kullanıcıya ilk PIN’ini bildirir. Kullanıcıların ilk doğrulama sırasında kendi PIN'lerini değiştirmesi gerekir.

### <a name="configure-email-and-email-templates"></a>E-posta ve e-posta şablonlarını yapılandırma

Soldaki e-posta simgesine tıklayarak bu e-postaları gönderme ayarlarını yapabilirsiniz. Bu sayfada, posta sunucunuzun SMTP bilgilerini girebilir ve **Kullanıcılara e-posta gönder** onay kutusunu işaretleyerek e-posta gönderebilirsiniz.

![MFA Sunucusu E-posta yapılandırması](./media/howto-mfaserver-deploy/email1.png)

E-posta İçeriği sekmesinde, seçim yapabileceğiniz e-posta şablonlarını görebilirsiniz. Kullanıcılarınızı iki adımlı doğrulama için nasıl yapılandırdığınıza bağlı olarak, size en uygun şablonu seçin.

![Konsolundaki MFA sunucusu e-posta şablonları](./media/howto-mfaserver-deploy/email2.png)

## <a name="import-users-from-active-directory"></a>Kullanıcıları Active Directory'den içeri aktarma

Artık sunucu yüklendiğine göre kullanıcıları eklemek istersiniz. Kullanıcıları el ile oluşturmayı, Active Directory'den içe aktarmayı ya da Active Directory ile otomatik eşitleme yapılandırmayı seçebilirsiniz.

### <a name="manual-import-from-active-directory"></a>Active Directory'den elle içeri aktarma

1. Azure MFA Sunucusu’nda, solda, **Kullanıcılar**’ı seçin.
2. Alt kısımda, **Active Directory’den içeri aktar**’ı seçin.
3. Artık kullanıcıları tek tek arayabilir ya da içindeki kullanıcılarla birlikte OU’lar için AD dizininde arama yapabilirsiniz. Bu durumda kullanıcıların OU’su belirtilir.
4. Sağ tarafta tüm kullanıcıları vurgulayın ve **İçeri Aktar**’a tıklayın. Başarılı olduğunuzu belirten bir açılır pencere görmeniz gerekir. İçeri aktarma penceresini kapatın.

   ![MFA sunucusu kullanıcı içeri aktarma Active Directory](./media/howto-mfaserver-deploy/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>Active Directory ile otomatik eşitleme

1. Azure MFA Sunucusu’nda, soldan **Dizin Tümleştirme**’yi seçin.
2. **Eşitleme** sekmesine gidin.
3. Alt kısımdan **Ekle**’yi seçin.
4. Açılan **Eşitleme Öğesi Ekle** kutusunda bu eşitleme görevi için etki alanını, OU’yu **veya** güvenlik grubunu, Ayarlar’ı, Yöntem Varsayılanları’nı ve Dil Varsayılanları’nı seçip **Ekle**’ye tıklayın.
5. **Active Directory ile eşitlemeyi etkinleştir** başlıklı kutuyu işaretleyin ve bir dakika ile 24 saat arasında bir **Eşitleme aralığı** belirleyin.

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Azure Multi-Factor Authentication Sunucusu kullanıcı verileri nasıl işler?

Şirket içi Multi-Factor Authentication (MFA) sunucusunu kullandığınızda, kullanıcının verileri şirket içi sunucularda depolanır. Kalıcı kullanıcı verileri bulutta depolanmaz. Kullanıcı iki adımlı kimlik doğrulama gerçekleştirdiğinde MFA Sunucusu doğrulamayı gerçekleştirmek üzere Azure MFA bulut hizmetine veri gönderir. Bu kimlik doğrulaması istekleri bulut hizmetine gönderildiğinde, aşağıdaki alanlar istekte ve günlüklerde gönderilir, böylece bunlar müşterinin kimlik doğrulama/kullanım raporlarında kullanılabilir. Multi-Factor Authentication Sunucusu’nda etkinleştirilebilecek ya da devre dışı bırakılabilecek şekilde, bazı alanlar isteğe bağıldır. MFA Sunucusu’ndan MFA bulut hizmetlerine iletişim 443 giden bağlantı noktası üzerinden SSL/TLS kullanır. Bu alanlar aşağıdaki gibidir:

* Benzersiz Kimlik - kullanıcı adı veya iç MFA sunucusu kimliği
* Adı ve soyadı (isteğe bağlı)
* E-posta adresi (isteğe bağlı)
* Telefon numarası - sesli arama veya SMS kimlik doğrulaması yapılırken
* Cihaz belirteci - Mobil uygulama kimlik doğrulaması yaparken
* Kimlik doğrulaması modu
* Kimlik doğrulaması sonucu
* MFA Sunucusu adı
* MFA Sunucusu IP’si
* İstemci IP’si - varsa

Yukarıdaki alanlara ek olarak, doğrulama sonucu (başarılı/reddedildi) ve reddetme nedeni kimlik doğrulama verileriyle birlikte depolanır ve kimlik doğrulama/kullanım raporlarıyla kullanıma sunulur.

> [!IMPORTANT]
> Mart 2019 ' den itibaren telefon araması seçenekleri, ücretsiz/deneme Azure AD kiracılarındaki sunucu kullanıcıları için mevcut olmayacaktır. SMS iletileri bu değişiklikten etkilenmez. Telefon araması, ücretli Azure AD kiracılarındaki kullanıcılar için kullanılabilir olmaya devam edecektir. Bu değişiklik yalnızca ücretsiz/deneme Azure AD kiracılarını etkiler.

## <a name="back-up-and-restore-azure-mfa-server"></a>Azure MFA Sunucusunu yedekleme ve geri yükleme

İyi bir yedeğe sahip olduğunuzdan emin olmak, tüm sistemler için önemli bir adımdır.

Azure MFA sunucusunu yedeklemek için, **PhoneFactor.pfdata** dosyası dahil olmak üzere **C:\Program Files\Multi-Factor Authentication Server\Data** klasörünün bir kopyasını alın. 

Geri yükleme gerekmesi durumunda aşağıdaki adımları takip edin:

1. Azure MFA sunucusunu yeni bir sunucuya yeniden yükleyin.
2. Yeni Azure MFA Sunucusunu etkinleştirin.
3. **MultiFactorAuth** hizmetini durdurun.
4. Yedeklediğiniz kopyadan **PhoneFactor.pfdata** dosyasının üzerine yazın.
5. **MultiFactorAuth** hizmetini başlatın.

Yeni Sunucu artık özgün yedeklenen yapılandırması ve kullanıcı verileriyle hazır ve çalışır durumdadır.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>TLS/SSL Protokollerini ve Şifre Paketlerini yönetme

MFA Server sürüm 8.x veya üzerini yükledikten veya yükselttikten sonra kuruluşunuzda ihtiyaç duyulmaması halinde eski ve daha zayıf şifre paketlerinin devre dışı bırakılması veya kaldırılması önerilir. Bu görevin nasıl gerçekleştirileceği hakkında bilgiler [AD FS için SSL/TLS Protokollerini ve Şifre Paketlerini Yönetme](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) makalesine bakın

## <a name="next-steps"></a>Sonraki adımlar

- Kullanıcı self servis işlemleri için [Kullanıcı Portalı](howto-mfaserver-deploy-userportal.md)’nı ayarlayın ve yapılandırın.
- Azure MFA Sunucusunu [Active Directory Federasyon Hizmeti](multi-factor-authentication-get-started-adfs.md), [RADIUS Kimlik Doğrulaması](howto-mfaserver-dir-radius.md) veya [LDAP Kimlik Doğrulaması](howto-mfaserver-dir-ldap.md) ile ayarlayıp yapılandırın.
- [RADIUS kullanan Uzak Masaüstü Ağ Geçidi ve Azure Multi-Factor Authentication Sunucusu](howto-mfaserver-nps-rdg.md)’nu kurun ve yapılandırın.
- [Azure Multi-Factor Authentication Sunucusu Mobil Uygulama Web Hizmeti’ni dağıtın](howto-mfaserver-deploy-mobileapp.md).
- [Azure Multi-Factor Authentication ve üçüncü taraf VPN’ler ile gelişmiş senaryolar](howto-mfaserver-nps-vpn.md).