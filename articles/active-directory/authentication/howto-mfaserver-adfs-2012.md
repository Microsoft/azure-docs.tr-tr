---
title: Windows Server 'da AD FS Azure MFA sunucusu-Azure Active Directory
description: Bu makale Windows Server 2012 R2 ve 2016’da Azure Multi-Factor Authentication ve AD FS’yi kullanmaya başlama işlemini açıklamaktadır.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd1bf4f9c463973d70f5289f7a82f372d0156cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742554"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-in-windows-server"></a>Azure Multi-Factor Authentication Sunucusunu Windows Server’da AD FS ile çalışacak şekilde yapılandırma

Active Directory Federasyon Hizmetleri (AD FS) kullanıyorsanız ve bulut ya da şirket içi kaynaklarınızı güvenli hale getirmek istiyorsanız Azure Multi-Factor Authentication Sunucusunu AD FS ile çalışmak üzere yapılandırabilirsiniz. Bu yapılandırma yüksek değerli uç noktalar için iki aşamalı doğrulamayı tetikler.

Bu makale Windows Server 2012 R2 veya Windows Server 2016’da AD FS ile Multi-Factor Authentication Sunucusu kullanmayı ele alır. Daha fazla bilgi için [AD FS 2.0 ile Azure Multi-Factor Authentication Sunucusu kullanarak bulut ve şirket içi kaynakları güvenli hale getirme](howto-mfaserver-adfs-2.md) konusunu okuyun.

> [!IMPORTANT]
> 1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sağlamamaktadır. Oturum açma olayları sırasında çok faktörlü kimlik doğrulaması (MFA) gerektirmek isteyen yeni müşteriler, bulut tabanlı Azure AD Multi-Factor Authentication kullanmalıdır.
>
> Bulut tabanlı MFA 'yı kullanmaya başlamak için bkz. [öğretici: Azure Multi-Factor Authentication Ile güvenli Kullanıcı oturum açma olayları](tutorial-enable-azure-mfa.md).
>
> Bulut tabanlı MFA kullanıyorsanız bkz. [Azure AD Multi-Factor Authentication ve AD FS bulut kaynaklarını güvenli hale getirme](howto-mfa-adfs.md).
>
> MFA sunucusunu 1 Temmuz 2019 tarihinden önce etkinleştiren mevcut müşteriler, en son sürümü, gelecekteki güncelleştirmeleri indirebilir ve her zamanki gibi etkinleştirme kimlik bilgilerini oluşturabilir.

## <a name="secure-windows-server-ad-fs-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Sunucusu ile Windows Server AD FS’yi güvenli hale getirme

Azure Multi-Factor Authentication Sunucusu’nu yüklerken aşağıdaki seçeneklere sahip olursunuz:

* Azure Multi-Factor Authentication Sunucusu’nu yerel olarak AD FS ile aynı sunucuya yükleme
* Azure Multi-Factor Authentication Bağdaştırıcısı’nı yerel olarak AD FS Sunucusu’na yükleme ve ardından farklı bir bilgisayara Multi-Factor Authentication Sunucusu yükleme

Başlamadan önce, aşağıdaki bilgileri unutmayın:

* Azure Multi-Factor Authentication Sunucusu’nu AD FS sunucunuza yüklemeniz gerekmez. Ancak, AD FS için Multi-Factor Authentication bağdaştırıcısını AD FS çalıştıran bir Windows Server 2012 R2 veya Windows Server 2016’ya yüklemeniz gerekir. Sunucuyu farklı bir bilgisayara yükleyebilirsiniz ve AD FS bağdaştırıcısını ayrı olarak AD FS federasyon sunucusuna yükleyebilirsiniz. Bağdaştırıcıyı ayrıca yükleme hakkında bilgi edinmek için aşağıdaki yordamlara bakın.
* Kuruluşunuz SMS mesajı ya da mobil uygulama doğrulama yöntemlerini kullanıyorsa, Şirket Ayarları’nda tanımlanan dizeler <$*uygulama_adı*$> şeklinde bir yer tutucu içerir. MFA Sunucusu v7.1’de bu yer tutucunun yerini alan bir uygulama adı belirtebilirsiniz. v7.0 veya daha eski sürümlerde, AD FS bağdaştırıcısı kullandığınızda bu yer tutucu otomatik olarak değiştirilmez. Bu eski sürümler için AD FS’yi güvenli hale getirdiğinizde yer tutucuyu uygun dizelerden kaldırın.
* Oturum açmak için kullandığınız hesap, Active Directory hizmetinde güvenlik grupları oluşturmak için kullanıcı haklarına sahip olmalıdır.
* Multi-Factor Authentication AD FS bağdaştırıcısı yükleme sihirbazı, Active Directory örneğinizde PhoneFactor Admins adlı bir güvenlik grubu oluşturur. Ardından federasyon hizmetinizin AD FS hizmet hesabını bu gruba ekler. Etki alanı denetleyicinizde PhoneFactor Admins grubunun oluşturulduğunu ve AD FS hizmet hesabının bu gruba üye olduğunu doğrulayın. Gerekirse, AD FS hizmeti hesabınızı etki alanı denetleyicinizde PhoneFactor Admins grubuna el ile ekleyin.
* Kullanıcı portalıyla Hizmet SDK’sını yükleme hakkında bilgi edinmek için bkz. [Azure Multi-Factor Authentication Sunucusu için kullanıcı portalını dağıtma.](howto-mfaserver-deploy-userportal.md)

### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Azure Multi-Factor Authentication Sunucusu’nu yerel olarak AD FS sunucusuna yükleme

1. Azure Multi-Factor Authentication Sunucusu’nu AD FS sunucunuza indirin ve yükleyin. Yükleme bilgileri için bkz. [Azure Multi-Factor Authentication Sunucusunu kullanmaya başlama](howto-mfaserver-deploy.md).
2. Azure Multi-Factor Authentication Sunucusu yönetim konsolunda **AD FS** simgesine tıklayın. **Kullanıcı kaydına izin ver** ve **Kullanıcıların yöntemi seçmesine izin ver** seçeneklerini belirleyin.
3. Kuruluşunuz için belirtmek istediğiniz ek seçenekleri belirleyin.
4. **AD FS Bağdaştırıcısı’nı Yükle**'ye tıklayın.

   ![MFA sunucusu konsolundan ADFS bağdaştırıcısını yükler](./media/howto-mfaserver-adfs-2012/server.png)

5. Active Directory penceresinin açılması iki anlama gelir. Bilgisayarınız bir etki alanına katıldıysa ve AD FS Bağdaştırıcısı ile Multi-Factor Authentication hizmeti arasındaki hizmeti güvenli hale getirmek üzere Active Directory yapılandırması tamamlanmamıştır. Bu yapılandırmayı otomatik olarak tamamlamak için **İleri** düğmesine tıklayın ya da **Otomatik Active Directory yapılandırmasını atla ve ayarları el ile yapılandır** onay kutusunu işaretleyin. **İleri**’ye tıklayın.
6. Yerel Grup pencerelerinin açılması iki anlama gelir. Bilgisayarınız bir etki alanına katılmadıysa ve AD FS bağdaştırıcısı ile Multi-Factor Authentication hizmeti arasındaki hizmeti güvenli hale getirmek üzere yerel grup yapılandırması tamamlanmamıştır. Bu yapılandırmayı otomatik olarak tamamlamak için **İleri** düğmesine tıklayın ya da **Otomatik Yerel Grup yapılandırmasını atla ve ayarları el ile yapılandır** onay kutusunu işaretleyin. **İleri**’ye tıklayın.
7. Yükleme sihirbazında **İleri**’ye tıklayın. Azure Multi-Factor Authentication Sunucusu PhoneFactor Admins grubunu oluşturur ve AD FS hizmeti hesabını PhoneFactor Admins grubuna ekler.
8. **Yükleyiciyi Başlat** sayfasında **İleri**’ye tıklayın.
9. Multi-Factor Authentication AD FS bağdaştırıcısı yükleyicisinden **İleri**' ye tıklayın.
10. Yükleme tamamlandığında **Kapat**'a tıklayın.
11. Bağdaştırıcı yüklendiğinde AD FS’ye kaydetmeniz gerekir. Windows PowerShell’i açın ve aşağıdaki komutu çalıştırın:

    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`

12. Yeni kaydettiğiniz bağdaştırıcıyı kullanmak için AD FS’deki genel kimlik doğrulama ilkesini düzenleyin. AD FS yönetim konsolunda **Kimlik Doğrulama İlkeleri** düğümüne gidin. **Multi-factor Authentication** bölümünde **Genel Ayarlar** bölümünün yanındaki **Düzenle** bağlantısına tıklayın. **Genel kimlik doğrulama Ilkesini Düzenle** penceresinde, ek kimlik doğrulama yöntemi olarak **Multi-Factor Authentication** öğesini seçin ve ardından **Tamam**' a tıklayın. Bağdaştırıcı WindowsAzureMultiFactorAuthentication olarak kaydedilir. Kaydın etkili olması için AD FS hizmetini yeniden başlatın.

![Genel kimlik doğrulama ilkesini Düzenle](./media/howto-mfaserver-adfs-2012/global.png)

Bu noktada Multi-Factor Authentication Sunucusu, AD FS ile birlikte kullanım amacıyla ek kimlik doğrulama sağlayıcısı olmak üzere kurulur.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Web Hizmeti SDK’sını kullanarak AD FS bağdaştırıcısının tek başına örneğini yükleme

1. Web Hizmeti SDK’sını Multi-Factor Authentication Sunucusu çalıştıran sunucuya yükleyin.
2. Aşağıdaki dosyaları \Program Files\Multi-Factor Authentication Server dizininden AD FS bağdaştırıcısını yüklemeyi planladığınız sunucuya kopyalayın:
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. MultiFactorAuthenticationAdfsAdapterSetup64.msi yükleme dosyasını çalıştırın.
4. Yükleme işlemini başlatmak için Multi-Factor Authentication AD FS bağdaştırıcısı yükleyicisinde **İleri**’ye tıklayın.
5. Yükleme tamamlandığında **Kapat**'a tıklayın.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>MultiFactorAuthenticationAdfsAdapter.config dosyasını düzenleme

MultiFactorAuthenticationAdfsAdapter.config dosyasını düzenlemek için aşağıdaki adımları izleyin:

1. **UseWebServiceSdk** düğümünü **true** olarak ayarlayın.  
2. **WebServiceSdkUrl** değerini Multi-Factor Authentication Web Hizmeti SDK URL’sine ayarlayın. Örneğin: *https: \/ \/ contoso.com/ \<certificatename> /MultiFactorAuthWebServiceSdk/PfWsSdk.asmx*, burada *\<certificatename>* sertifikanızın adıdır.  
3. Komutun sonuna ekleyerek Register-MultiFactorAuthenticationAdfsAdapter.ps1 betiğini düzenleyin `-ConfigurationFilePath &lt;path&gt;` `Register-AdfsAuthenticationProvider` , burada *&lt; &gt; yol* MultiFactorAuthenticationAdfsAdapter.config dosyasının tam yoludur.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Web Hizmeti SDK’sını bir kullanıcı adı ve parola kullanarak yapılandırma

Web Hizmeti SDK’sını yapılandırmaya yönelik iki seçenek vardır. Birincisi kullanıcı adı ve parola, ikincisi ise istemci sertifikası ile yapılır. Birinci seçenek için bu adımları izleyin veya ikinci seçenek için bu adımları atlayın.  

1. **Webservicesdkusername** değerini phonefactor Admins güvenlik grubunun üyesi olan bir hesaba ayarlayın. &lt;Etki alanı&gt;&#92;&lt;kullanıcı adı&gt; biçimini kullanın.  
2. **WebServiceSdkPassword** değerini uygun hesap parolası olarak ayarlayın.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Web Hizmeti SDK’sını bir istemci sertifikası ile yapılandırma

Bir kullanıcı adı ve parola kullanmak istemiyorsanız Web Hizmeti SDK’sını bir istemci sertifikası ile yapılandırmak için aşağıdaki adımları izleyin.

1. Web Hizmeti SDK’sı çalıştıran sunucu için sertifika yetkilisinden bir istemci sertifikası alın. [İstemci sertifikalarını alma](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770328(v=ws.10)) hakkında bilgi edinin.  
2. İstemci sertifikasını Web Hizmeti SDK’sı çalıştıran sunucudaki yerel bilgisayar kişisel sertifika deposuna aktarın. Sertifika yetkilisinin genel sertifikasının Güvenilen Kök Sertifikalar sertifika deposunda olduğundan emin olun.  
3. İstemci sertifikasının ortak ve özel anahtarlarını bir .pfx dosyasına aktarın.  
4. Base64 biçimindeki ortak anahtarı bir .cer dosyasına aktarın.  
5. Sunucu Yöneticisi'nde, Web Server (IIS)\Web Server\Security\IIS İstemci Sertifikası Eşleme Kimlik doğrulaması özelliğinin yüklü olduğunu doğrulayın. Yüklü değilse, bu özelliğe eklemek üzere **Rol ve Özellik Ekle**’yi seçin.  
6. IIS Yöneticisi'nde, Web Hizmeti SDK’sı sanal dizinini içeren web sitesinde **Yapılandırma Düzenleyicisi**'ne çift tıklayın. Sanal dizinin değil, web sitesinin seçilmesi gerekir.  
7. **System.webServer/security/authentication/iisClientCertificateMappingAuthentication** bölümüne gidin.  
8. Etkin’i **true** olarak ayarlayın.  
9. oneToOneCertificateMappingsEnabled değerini **true** olarak ayarlayın.  
10. OneToOneMappings öğesinin yanındaki **...** düğmesine tıklayın ve ardından **Ekle** bağlantısına tıklayın.  
11. Önceden dışarı aktardığınız Base64 .cer dosyasını açın. *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* ifadelerini ve tüm satır sonlarını kaldırın. Sonuç dizesini kopyalayın.  
12. certificate değerini önceki adımda kopyaladığınız dizeye ayarlayın.  
13. Etkin’i **true** olarak ayarlayın.  
14. userName değerini PhoneFactor Admins güvenlik grubunun üyesi olan bir hesaba ayarlayın. &lt;Etki alanı&gt;&#92;&lt;kullanıcı adı&gt; biçimini kullanın.  
15. Parolayı uygun hesap parolasına ayarlayın ve ardından Yapılandırma Düzenleyicisi’ni kapatın.  
16. **Uygula** bağlantısına tıklayın.  
17. Web Hizmeti SDK’sı sanal dizininde **Kimlik doğrulama** öğesine çift tıklayın.  
18. ASP.NET Kimliğe Bürünme ve Temel Kimlik Doğrulaması’nın **Etkin** olarak ve diğer tüm öğelerin **Devre Dışı** olarak ayarlandığını doğrulayın.  
19. Web Hizmeti SDK’sı sanal dizininde **SSL Ayarları** öğesine çift tıklayın.  
20. İstemci Sertifikaları’nı **Kabul Et** olarak ayarlayın ve ardından **Uygula**’ya tıklayın.  
21. Önceden dışarı aktardığınız .pfx dosyasını AD FS bağdaştırıcısı çalıştıran sunucuya kopyalayın.  
22. .pfx dosyasını yerel bilgisayar kişisel sertifika deposuna aktarın.  
23. Sağ tıklayıp **Özel Anahtarları Yönet**’i seçin ve ardından AD FS hizmetinde oturum açmak için kullandığınız hesaba okuma erişimi verin.  
24. İstemci sertifikasını açın ve **Ayrıntılar** sekmesinden parmak izini kopyalayın.  
25. MultiFactorAuthenticationAdfsAdapter.config dosyasında, **Webservicesdkcertificatethumbprint** değerini önceki adımda kopyaladığınız dizeye ayarlayın.  

Son olarak, bağdaştırıcıyı kaydetmek için PowerShell’de \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 betiğini çalıştırın. Bağdaştırıcı WindowsAzureMultiFactorAuthentication olarak kaydedilir. Kaydın etkili olması için AD FS hizmetini yeniden başlatın.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>AD FS kullanarak Azure AD kaynaklarını güvenli hale getirme

Bulut kaynağınızın güvenliğini sağlamak için, kullanıcı iki adımlı doğrulamayı başarıyla gerçekleştirdiğinde Active Directory Federation Services tarafından multipleauthn talebinin yayılması için bir talep kuralı oluşturun. Bu talep Azure AD'ye aktarılır. İlerlemek için bu yordamı izleyin:

1. AD FS Yönetimi'ni açın.
2. Solda, **Bağlı Olan Taraf Güvenleri**’ni seçin.
3. **Microsoft Office 365 Kimlik Platformu**’na sağ tıklayın ve **Talep Kurallarını Düzenle…** seçeneğini belirleyin

   ![ADFS konsolundaki talep kurallarını düzenleme](./media/howto-mfaserver-adfs-2012/trustedip1.png)

4. Verme Dönüştürme Kuralları’nda **Kural Ekle**’ye tıklayın.

   ![ADFS konsolundaki dönüşüm kurallarını düzenleme](./media/howto-mfaserver-adfs-2012/trustedip2.png)

5. Dönüştürme Kuralı Ekleme Sihirbazı’nda, açılır menüde **Gelen Talep için Geçiş ya da Filtre**’yi seçin ve **İleri**’ye tıklayın.

   ![Dönüşüm talebi kuralı Ekle Sihirbazı](./media/howto-mfaserver-adfs-2012/trustedip3.png)

6. Kuralınıza bir ad verin.
7. Gelen talep türü olarak **Kimlik Doğrulama Yöntemleri Başvuruları**’nı seçin.
8. **Tüm talep değerlerini geçir**’i seçin.

    ![Dönüşüm Talep Kuralı Ekleme Sihirbazı](./media/howto-mfaserver-adfs-2012/configurewizard.png)

9. **Finish (Son)** düğmesine tıklayın. AD FS Yönetim Konsolu'nu kapatın.

## <a name="troubleshooting-logs"></a>Sorun giderme günlükleri

MFA Sunucusu AD FS Bağdaştırıcısı'yla ilgili sorunları gidermenize yardımcı olması, aşağıdaki ek günlük kaydını etkinleştirme adımlarını kullanın.

1. MFA Sunucusu arabiriminde AD FS bölümünü açın ve **Günlük kaydını etkinleştir** onay kutusunu işaretleyin.
2. Her AD FS sunucusunda, **regedit.exe**'yi kullanarak `C:\Program Files\Multi-Factor Authentication Server\` değeriyle (veya seçtiğiniz başka bir dizin) dize değeri kayıt defteri anahtarını `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Positive Networks\PhoneFactor\InstallPath` oluşturun.  **Sondaki ters eğik çizgi önemli olduğuna dikkat edin.**
3. `C:\Program Files\Multi-Factor Authentication Server\Logs` dizini (veya **2. Adım**’da belirtildiği gibi başka bir dizin) oluşturun.
4. Logs dizininde, AD FS hizmet hesabına Değiştirme erişimi verin.
5. AD FS hizmetini yeniden başlatın.
6. `MultiFactorAuthAdfsAdapter.log` dosyasının Logs dizininde oluşturulduğunu doğrulayın.

## <a name="related-topics"></a>İlgili konular

Sorun giderme yardımı için bkz. [Azure Multi-Factor Authentication SSS](multi-factor-authentication-faq.md)