---
title: Azure AD Uygulama Ara Sunucusu ile Power BI uzaktan erişimi etkinleştirme
description: Şirket içi Power BI Azure AD Uygulama Ara Sunucusu ile tümleştirme hakkında temel bilgileri içerir.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/25/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9b0cce11abe1076d26ac8d4c4dc57c9b57c4737
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625590"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu ile Power BI Mobil’e uzaktan erişimi etkinleştirme

Bu makalede, Power BI mobil uygulamanın Power BI Rapor Sunucusu (PBIRS) ve SQL Server Reporting Services (SSRS) 2016 ve üzeri bir sürüme bağlanmasını sağlamak için Azure AD Uygulama Ara Sunucusu 'nin nasıl kullanılacağı açıklanır. Bu tümleştirme sayesinde, kurumsal ağdan uzakta olan kullanıcılar Power BI raporlarına Power BI mobil uygulamadan erişebilir ve Azure AD kimlik doğrulaması tarafından korunabilir. Bu koruma, koşullu erişim ve çok faktörlü kimlik doğrulaması gibi [güvenlik avantajlarını](application-proxy-security.md#security-benefits) içerir.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, zaten rapor Hizmetleri dağıttığınız ve [uygulama ara sunucusu 'nu etkinleştirmiş](application-proxy-add-on-premises-application.md)olduğunuz varsayılmaktadır.

- Uygulama proxy 'Sini etkinleştirmek için bir Windows Server 'a bağlayıcı yüklenmesi ve bağlayıcının Azure AD hizmetleriyle iletişim kurabilmesi için [önkoşulları](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) tamamlaması gerekir.
- Power BI yayımlarken, aynı iç ve dış etki alanlarını kullanmanızı öneririz. Özel etki alanları hakkında daha fazla bilgi edinmek için bkz. [uygulama proxy 'sinde özel etki alanları Ile çalışma](./application-proxy-configure-custom-domain.md).
- Bu tümleştirme, **Power BI mobil iOS ve Android** uygulaması için kullanılabilir.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>1. Adım: Kerberos kısıtlı temsilcisini yapılandırma (KCD)

Windows kimlik doğrulaması kullanan şirket içi uygulamalarda, Kerberos kimlik doğrulama protokolü ve Kerberos kısıtlanmış temsili (KCD) adlı bir özellik ile çoklu oturum açma (SSO) elde edebilirsiniz. Yapılandırıldığında, KCD, Kullanıcı Windows 'da doğrudan oturum açmamış olsa bile, uygulama proxy bağlayıcısının Kullanıcı için bir Windows belirteci almasına izin verir. KCD hakkında daha fazla bilgi edinmek için bkz. [Kerberos kısıtlanmış temsilciye genel bakış](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)) ve [uygulama proxy 'si ile uygulamalarınıza çoklu oturum açma Için Kerberos kısıtlanmış temsili](application-proxy-configure-single-sign-on-with-kcd.md).

Reporting Services tarafında yapılandırılacak çok fazla ayar yoktur. Doğru Kerberos kimlik doğrulamasının gerçekleşmesini sağlamak için geçerli bir hizmet asıl adına (SPN) sahip olduğunuzdan emin olun. Ayrıca Raporlama Hizmetleri sunucusunun, anlaşma kimlik doğrulaması için etkinleştirildiğinden emin olun.

Reporting Services için KCD 'yi ayarlamak için aşağıdaki adımlarla devam edin.

### <a name="configure-the-service-principal-name-spn"></a>Hizmet asıl adını (SPN) yapılandırma

SPN, Kerberos kimlik doğrulaması kullanan bir hizmet için benzersiz bir tanımlayıcıdır. Rapor sunucunuz için uygun bir HTTP SPN 'si olduğundan emin olmanız gerekir. Rapor sunucunuz için doğru Hizmet Asıl Adı'nı (SPN) yapılandırma hakkında bilgi almak isterseniz bkz. [Register a Service Principal Name (SPN) for a Report Server (Rapor Sunucusu için bir Hizmet Asıl Adı (SPN) kaydı yapma)](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server).
Setspn komutunu-L seçeneğiyle çalıştırarak SPN 'nin eklendiğini doğrulayabilirsiniz. Bu komut hakkında daha fazla bilgi edinmek için bkz. [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Negotiate kimlik doğrulamasını etkinleştir

Bir rapor sunucusunun Kerberos kimlik doğrulamasını kullanmasını sağlamak için rapor sunucusunun kimlik doğrulama türünü RSWindowsNegotiate olacak şekilde yapılandırın. rsreportserver.config dosyasını kullanarak bu ayarı yapılandırın.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Daha fazla bilgi için bkz. [Modify a Reporting Services Configuration File (Bir Reporting Services Yapılandırma Dosyasını değiştirme)](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config) ve [Configure Windows Authentication on a Report Server (Bir Rapor Sunucusunda Windows Kimlik Doğrulamasını yapılandırma)](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Bağlayıcının, Reporting Services uygulama havuzu hesabına eklenen SPN 'ye devredilmek için güvenilir olduğundan emin olun
KCD 'YI, Azure AD Uygulama Ara Sunucusu hizmetinin Raporlama Hizmetleri uygulama havuzu hesabına Kullanıcı kimlikleri atayabilmesi için yapılandırın. Uygulama Ara Sunucusu bağlayıcısının Azure AD’de kimliği doğrulanmış olan kullanıcılarınız için Kerberos biletleri almasını etkinleştirerek KCD’yi yapılandırın. Bu durumda, bu sunucu bağlamı hedef uygulamaya veya Reporting Services 'e geçirir.

KCD 'yi yapılandırmak için, her bağlayıcı makinesi için aşağıdaki adımları yineleyin:

1. Etki alanı denetleyicisinde etki alanı yöneticisi olarak oturum açın ve ardından **Active Directory Kullanıcıları ve bilgisayarları**' nı açın.
2. Bağlayıcının üzerinde çalıştırıldığı bilgisayarı bulun.
3. Bilgisayara çift tıklayın ve ardından **Temsilci** sekmesini seçin.
4. Temsilci ayarlarını **Bu bilgisayara yalnızca belirtilen hizmetlere temsilci seçmek için güven** olarak belirleyin. Ardından **herhangi bir kimlik doğrulama protokolünü kullan**' ı seçin.
5. **Ekle**’yi ve ardından **Kullanıcılar veya Bilgisayarlar**’ı seçin.
6. Raporlama Hizmetleri için kullandığınız hizmet hesabını girin. Bu hesap, Reporting Services yapılandırmasında SPN'yi eklediğiniz hesaptır.
7. **Tamam**'a tıklayın. Değişiklikleri kaydetmek için **Tamam**’a yeniden tıklayın.

Daha fazla bilgi için bkz. [uygulama proxy 'si ile uygulamalarınızda çoklu oturum açma Için Kerberos kısıtlanmış temsili](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>2. Adım: Azure AD Uygulama Ara Sunucusu aracılığıyla rapor hizmetleri yayımlama

Artık Azure AD Uygulaması Ara Sunucusu’nu yapılandırmaya hazırsınız.

1. Aşağıdaki ayarlarla rapor hizmetlerini uygulama proxy 'Si aracılığıyla yayımlayın. Uygulama Ara Sunucusu aracılığıyla uygulama yayımlama işleminin adım adım yönergeleri için bkz. [Azure AD Uygulama Ara Sunucusu’nu kullanarak uygulamaları yayımlama](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Iç URL**: bağlayıcının şirket ağında ulaşabileceği rapor sunucusunun URL 'sini girin. Bağlayıcının yüklendiği sunucudan bu URL’ye ulaşılabildiğinden emin olun. Uygulama Ara Sunucusu aracılığıyla yayımlanan alt yollarla ilgili sorunlardan kaçınmak için `https://servername/` gibi bir üst düzey etki alanı kullanmak iyi bir yöntemdir. Örneğin `https://servername/` kullanın ama `https://servername/reports/` veya `https://servername/reportserver/` kullanmayın.
     > [!NOTE]
     > Rapor sunucusuna güvenli bir HTTPS bağlantısı kullanmanızı öneririz. Nasıl yapılacağına ilişkin bilgi için bkz. [Yerel moddaki rapor sunucusunda SSL bağlantılarını yapılandırma](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server).
   - **Dış URL**: Power BI Mobile App 'in bağlanacağı genel URL 'yi girin. Özel etki alanı kullanılıyorsa örneğin `https://reports.contoso.com` gibi bir URL olabilir. Özel etki alanı kullanmak için etki alanının sertifikasını karşıya yükleyin ve bir DNS kaydının uygulamanız için varsayılan msappproxy.net etki alanına işaret etmesini sağlayın. Ayrıntılı adımlar için bkz. [Azure AD Uygulama Ara Sunucusu’nda özel etki alanlarıyla çalışma](application-proxy-configure-custom-domain.md).

   - **Ön kimlik doğrulama yöntemi**: Azure Active Directory

2. Uygulamanız yayımlandığında, aşağıdaki adımları kullanarak çoklu oturum açma ayarlarını yapılandırın:

   a. Portaldaki uygulama sayfasında **Çoklu oturum açma**’yı seçin.

   b. **Çoklu Oturum Açma Modu** olarak **Tümleşik Windows Kimlik Doğrulaması**’nı seçin.

   c. **İç Uygulama SPN'si** öğesini daha önce ayarladığınız değere ayarlayın.

   d. Bağlayıcıyı kullanıcılar adına kullanmak için **Temsilci Oturum Açma Kimliği**’ni seçin. Daha fazla bilgi için bkz. [Farklı şirket içi ve bulut kimlikleriyle çalışma](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. **Kaydet**’a tıklayarak değişikliklerinizi kaydedin.

Uygulamanızı ayarlamayı bitirmeden, **Kullanıcılar ve gruplar** bölümüne gidin ve bu uygulamaya erişmek için kullanıcıları atayın.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>3. Adım: uygulama için yanıt URI 'sini değiştirme

Power BI mobil uygulamanın rapor hizmetlerine bağlanabilmesi ve erişebilmesi için önce adım 2 ' de otomatik olarak oluşturulan uygulama kaydını yapılandırmanız gerekir.

1. Azure Active Directory **Genel Bakış** sayfasında **Uygulama kayıtları**'nı seçin.
2. **Tüm uygulamalar** sekmesinin altında 2. adımda oluşturduğunuz uygulamayı arayın.
3. Uygulamayı seçin ve sonra da **Kimlik Doğrulaması**’nı seçin.
4. Kullandığınız platforma bağlı olarak aşağıdaki Yeniden Yönlendirme URI’lerini ekleyin.

   Uygulamayı Power BI Mobil **iOS** için yapılandırırken, genel istemci türünde aşağıdaki yeniden yönlendirme URI 'lerini ekleyin (mobil & Masaüstü):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   Uygulamayı Power BI Mobil **Android** için yapılandırırken, genel istemci türünde aşağıdaki yeniden yönlendirme URI 'lerini ekleyin (mobil & Masaüstü):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Uygulamanın düzgün çalışması için Yeniden Yönlendirme URI’lerinin eklenmesi gerekir. Uygulamayı hem iOS hem de Android Power BI Mobil için yapılandırıyorsanız, genel Istemci türündeki aşağıdaki yeniden yönlendirme URI 'sini (mobil & Masaüstü) iOS için yapılandırılan yeniden yönlendirme URI 'Leri listesine ekleyin: `urn:ietf:wg:oauth:2.0:oob` .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>4. Adım: Power BI Mobil uygulamadan bağlanma

1. Power BI mobil uygulamada, Reporting Services örneğinizi bağlayın. Bunu yapmak için, uygulama proxy 'Si aracılığıyla yayımladığınız uygulamanın **dış URL 'sini** girin.

   ![Dış URL ile mobil uygulama Power BI](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. **Bağlan**’ı seçin. Azure Active Directory oturum açma sayfasına yönlendirilirsiniz.

3. Kullanıcınızın geçerli kimlik bilgilerini girin ve **Oturum aç**’ı seçin. Reporting Services sunucunuzdaki öğeleri görürsünüz.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>5. Adım: yönetilen cihazlar için Intune ilkesini yapılandırma (isteğe bağlı)

Şirketinizin iş gücünün kullandığı istemci uygulamalarını yönetmek için Microsoft Intune kullanabilirsiniz. Intune, veri şifreleme ve ek erişim gereksinimleri gibi özellikleri kullanmanıza olanak sağlar. Intune ile uygulama yönetimi hakkında daha fazla bilgi edinmek için bkz. Intune uygulama yönetimi. Power BI mobil uygulamanın Intune ilkesiyle çalışmasını sağlamak için aşağıdaki adımları kullanın.

1. **Azure Active Directory** ve ardından **uygulama kayıtları**' na gidin.
2. Yerel istemci Uygulamanızı kaydederken adım 3 ' te yapılandırılan uygulamayı seçin.
3. Uygulamanın sayfasında, **API izinleri**' ni seçin.
4. **Izin Ekle**' ye tıklayın.
5. **Kuruluşumun kullandığı API 'ler** altında "Microsoft Mobil uygulama yönetimi" ifadesini arayın ve seçin.
6. Uygulamaya **Devicemanagementmanagedapps. ReadWrite** iznini ekleyin
7. Uygulamaya izin erişimi vermek için **yönetici Izni ver** ' e tıklayın.
8. [Uygulama koruma ilkeleri oluşturma ve atama '](/intune/app-protection-policies)ya başvurarak istediğiniz Intune ilkesini yapılandırın.

## <a name="troubleshooting"></a>Sorun giderme

Uygulama bir raporu birkaç dakikadan uzun bir süre yüklemeye çalıştıktan sonra bir hata sayfası döndürürse, zaman aşımı ayarını değiştirmeniz gerekebilir. Varsayılan olarak, uygulama proxy 'Si bir isteğe yanıt vermek için 85 saniyeye kadar geçen uygulamaları destekler. Bu ayarı 180 saniyeye uzatmak için, uygulama ara sunucusu ayarları sayfasında, arka uç zaman aşımını **uzun** olarak seçin. Hızlı ve güvenilir raporlar oluşturma hakkında ipuçları için bkz. [Power BI Reports En Iyi yöntemleri](/power-bi/power-bi-reports-performance).

Power BI mobil uygulamanın şirket içi uygulamasına bağlanmasını sağlamak için Azure AD Uygulama Ara Sunucusu kullanma Power BI Rapor Sunucusu, Microsoft Power BI uygulaması onaylı bir istemci uygulaması olarak gerektiren koşullu erişim ilkeleriyle desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Yerel istemci uygulamalarının proxy uygulamalarıyla etkileşime geçmesini sağlama](application-proxy-configure-native-client-application.md)
- [Power BI mobil uygulamalarında şirket içi rapor sunucusu raporlarını ve KPI'lerini görüntüleme](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
