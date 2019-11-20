---
title: Azure AD Uygulama Ara Sunucusu ile Power BI uzaktan erişimi etkinleştirme | Microsoft Docs
description: Şirket içi Power BI Azure AD Uygulama Ara Sunucusu ile tümleştirme hakkında temel bilgileri içerir.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845ffda22cae9464870786cc5997b9f5521c03e1
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795619"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu ile Power BI Mobil uzaktan erişimi etkinleştirme

Bu makalede, Power BI mobil uygulamanın Power BI Rapor Sunucusu (PBIRS) ve SQL Server Reporting Services (SSRS) 2016 ve üzeri bir sürüme bağlanmasını sağlamak için Azure AD Uygulama Ara Sunucusu 'nin nasıl kullanılacağı açıklanır. Bu tümleştirme sayesinde, kurumsal ağdan uzakta olan kullanıcılar Power BI raporlarına Power BI mobil uygulamadan erişebilir ve Azure AD kimlik doğrulaması tarafından korunabilir. Bu koruma, koşullu erişim ve çok faktörlü kimlik doğrulaması gibi [güvenlik avantajlarını](application-proxy-security.md#security-benefits) içerir.  

## <a name="prerequisites"></a>Ön koşullar

Bu makalede, zaten rapor Hizmetleri dağıttığınız ve [uygulama ara sunucusu 'nu etkinleştirmiş](application-proxy-add-on-premises-application.md)olduğunuz varsayılmaktadır.

- Uygulama proxy 'Sini etkinleştirmek için bir Windows Server 'a bağlayıcı yüklenmesi ve bağlayıcının Azure AD hizmetleriyle iletişim kurabilmesi için [önkoşulları](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) tamamlaması gerekir.  
- Power BI yayımlarken, aynı iç ve dış etki alanlarını kullanmanızı öneririz. Özel etki alanları hakkında daha fazla bilgi edinmek için bkz. [uygulama proxy 'sinde özel etki alanları Ile çalışma](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Bu tümleştirme, **Power BI mobil iOS ve Android** uygulaması için kullanılabilir.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>1\. Adım: Kerberos kısıtlı temsilcisini yapılandırma (KCD)

Windows kimlik doğrulaması kullanan şirket içi uygulamalarda, Kerberos kimlik doğrulama protokolü ve Kerberos kısıtlanmış temsili (KCD) adlı bir özellik ile çoklu oturum açma (SSO) elde edebilirsiniz. Yapılandırıldığında, KCD, Kullanıcı Windows 'da doğrudan oturum açmamış olsa bile, uygulama proxy bağlayıcısının Kullanıcı için bir Windows belirteci almasına izin verir. KCD hakkında daha fazla bilgi edinmek için bkz. [Kerberos kısıtlanmış temsilciye genel bakış](https://technet.microsoft.com/library/jj553400.aspx) ve [uygulama proxy 'si ile uygulamalarınıza çoklu oturum açma Için Kerberos kısıtlanmış temsili](application-proxy-configure-single-sign-on-with-kcd.md).

Reporting Services tarafında yapılandırılması çok fazla olmaz. Doğru Kerberos kimlik doğrulamasının gerçekleşmesini sağlamak için geçerli bir hizmet asıl adına (SPN) sahip olduğunuzdan emin olun. Ayrıca Raporlama Hizmetleri sunucusunun, anlaşma kimlik doğrulaması için etkinleştirildiğinden emin olun.

Reporting Services için KCD 'yi ayarlamak için aşağıdaki adımlarla devam edin.

### <a name="configure-the-service-principal-name-spn"></a>Hizmet asıl adını (SPN) yapılandırma

SPN, Kerberos kimlik doğrulaması kullanan bir hizmetin benzersiz tanımlayıcısıdır. Rapor sunucunuz için uygun bir HTTP SPN 'si olduğundan emin olmanız gerekir. Rapor sunucunuz için uygun hizmet asıl adı 'nı (SPN) yapılandırma hakkında bilgi için bkz. [bir rapor sunucusu Için hizmet asıl adını (SPN) kaydetme](https://msdn.microsoft.com/library/cc281382.aspx).
Setspn komutunu-L seçeneğiyle çalıştırarak SPN 'nin eklendiğini doğrulayabilirsiniz. Bu komut hakkında daha fazla bilgi edinmek için bkz. [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Negotiate kimlik doğrulamasını etkinleştir

Bir rapor sunucusunun Kerberos kimlik doğrulamasını kullanmasını sağlamak için rapor sunucusunun kimlik doğrulama türünü RSWindowsNegotiate olacak şekilde yapılandırın. RSReportServer. config dosyasını kullanarak bu ayarı yapılandırın.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Daha fazla bilgi için bkz. [bir Reporting Services yapılandırma dosyasını değiştirme](https://msdn.microsoft.com/library/bb630448.aspx) ve [bir rapor sunucusunda Windows kimlik doğrulamasını yapılandırma](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Bağlayıcının, Reporting Services uygulama havuzu hesabına eklenen SPN 'ye devredilmek için güvenilir olduğundan emin olun
KCD 'YI, Azure AD Uygulama Ara Sunucusu hizmetinin Raporlama Hizmetleri uygulama havuzu hesabına Kullanıcı kimlikleri atayabilmesi için yapılandırın. Azure AD 'de kimlik doğrulamasından geçen kullanıcılarınız için Kerberos biletleri almak üzere uygulama proxy bağlayıcısını etkinleştirerek KCD 'yi yapılandırın. Bu durumda, bu sunucu bağlamı hedef uygulamaya veya Reporting Services 'e geçirir.

KCD 'yi yapılandırmak için, her bağlayıcı makinesi için aşağıdaki adımları yineleyin:

1. Etki alanı denetleyicisinde etki alanı yöneticisi olarak oturum açın ve ardından **Active Directory Kullanıcıları ve bilgisayarları**' nı açın.
2. Bağlayıcının üzerinde çalıştığı bilgisayarı bulun.  
3. Bilgisayara çift tıklayın ve ardından **temsili** sekmesini seçin.
4. **Bu bilgisayara yalnızca belirtilen hizmetlere atamak üzere güvenmek**için, yetkilendirme ayarlarını belirleyin. Ardından **herhangi bir kimlik doğrulama protokolünü kullan**' ı seçin.
5. **Ekle**' yi ve ardından **Kullanıcılar veya bilgisayarlar**' ı seçin.
6. Raporlama Hizmetleri için kullandığınız hizmet hesabını girin. Bu, SPN 'yi Raporlama Hizmetleri Yapılandırması içinde eklediğiniz hesaptır.
7. **Tamam** düğmesine tıklayın. Değişiklikleri kaydetmek için yeniden **Tamam** ' a tıklayın.

Daha fazla bilgi için bkz. [uygulama proxy 'si ile uygulamalarınızda çoklu oturum açma Için Kerberos kısıtlanmış temsili](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>2\. Adım: Azure AD Uygulama Ara Sunucusu aracılığıyla rapor hizmetleri yayımlama

Artık Azure AD Uygulama Ara Sunucusu 'yi yapılandırmaya hazırsınız.

1. Aşağıdaki ayarlarla rapor hizmetlerini uygulama proxy 'Si aracılığıyla yayımlayın. Uygulama proxy 'Si aracılığıyla bir uygulamayı yayımlama hakkında adım adım yönergeler için bkz. [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Iç URL**: bağlayıcının şirket ağında ulaşabileceği rapor sunucusunun URL 'sini girin. Bu URL 'ye bağlayıcının yüklendiği sunucudan erişilebildiğinden emin olun. En iyi yöntem, uygulama proxy 'Si aracılığıyla yayınlanan alt yollar ile ilgili sorunlardan kaçınmak için `https://servername/` gibi en üst düzey etki alanı kullanmaktır. Örneğin, `https://servername/reports/` veya `https://servername/reportserver/`değil `https://servername/` kullanın.
     > [!NOTE]
     > Rapor sunucusuna güvenli bir HTTPS bağlantısı kullanmanızı öneririz. Hakkında bilgi için bkz. [yerel mod rapor sunucusunda SSL bağlantılarını yapılandırma](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) .
   - **Dış URL**: Power BI Mobile App 'in bağlanacağı genel URL 'yi girin. Örneğin, özel bir etki alanı kullanılıyorsa `https://reports.contoso.com` gibi görünebilir. Özel bir etki alanı kullanmak için, etki alanı için bir sertifika yükleyin ve uygulamanız için varsayılan msappproxy.net etki alanına bir DNS kaydı getirin. Ayrıntılı adımlar için bkz. [Azure AD uygulama ara sunucusu özel etki alanları Ile çalışma](application-proxy-configure-custom-domain.md).

   - **Ön kimlik doğrulama yöntemi**: Azure Active Directory

2. Uygulamanız yayımlandıktan sonra, çoklu oturum açma ayarlarını aşağıdaki adımlarla yapılandırın:

   a. Portaldaki uygulama sayfasında, **Çoklu oturum açma**' yı seçin.

   b. **Çoklu oturum açma modu**Için **Tümleşik Windows kimlik doğrulaması**' nı seçin.

   c. **Iç uygulama SPN 'sini** daha önce ayarladığınız değere ayarlayın.  

   d. Bağlayıcının Kullanıcı adına kullanması için **yetkilendirilmiş oturum açma kimliğini** seçin. Daha fazla bilgi için bkz. [farklı şirket içi ve bulut kimlikleriyle çalışma](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Değişikliklerinizi kaydetmek için **Kaydet** ' e tıklayın.

Uygulamanızı ayarlamayı bitirmeden, **Kullanıcılar ve gruplar** bölümüne gidin ve bu uygulamaya erişmek için kullanıcıları atayın.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>3\. Adım: uygulama için yanıt URI 'sini değiştirme

Power BI mobil uygulamanın rapor hizmetlerine bağlanabilmesi ve erişebilmesi için önce adım 2 ' de otomatik olarak oluşturulan uygulama kaydını yapılandırmanız gerekir. 

1. Azure Active Directory **genel bakış** sayfasında **uygulama kayıtları**' i seçin.
2. **Tüm uygulamalar** sekmesinin altında 2. adımda oluşturduğunuz uygulamayı arayın.
3. Uygulamayı seçin ve **kimlik doğrulaması**' nı seçin.
4. Kullanmakta olduğunuz platforma göre aşağıdaki yeniden yönlendirme URI 'Lerini ekleyin.

   Uygulamayı Power BI Mobil **iOS**için yapılandırırken, genel istemci türünde aşağıdaki yeniden yönlendirme URI 'lerini ekleyin (mobil & Masaüstü):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Uygulamayı Power BI Mobil **Android**için yapılandırırken, genel istemci türünde aşağıdaki yeniden yönlendirme URI 'lerini ekleyin (mobil & Masaüstü):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Uygulamanın düzgün çalışması için yeniden yönlendirme URI 'Lerinin eklenmesi gerekir. Uygulamayı hem iOS hem de Android Power BI Mobil için yapılandırıyorsanız, genel Istemci türündeki şu yeniden yönlendirme URI 'sini (mobil & Masaüstü) iOS için yapılandırılan yeniden yönlendirme URI 'Leri listesine ekleyin: `urn:ietf:wg:oauth:2.0:oob`.

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>4\. Adım: Power BI Mobil uygulamadan bağlanma

1. Power BI mobil uygulamada, Reporting Services örneğinizi bağlayın. Bunu yapmak için, uygulama proxy 'Si aracılığıyla yayımladığınız uygulamanın **dış URL 'sini** girin.

   ![Dış URL ile mobil uygulama Power BI](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. **Bağlan**’ı seçin. Azure Active Directory oturum açma sayfasına yönlendirilirsiniz.

3. Kullanıcılarınız için geçerli kimlik bilgilerini girin ve **oturum aç '** ı seçin. Reporting Services sunucunuzdaki öğeleri görürsünüz.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>5\. Adım: yönetilen cihazlar için Intune ilkesini yapılandırma (isteğe bağlı)

> [!NOTE]
> Bu işlevsellik şu anda yalnızca iOS 'ta kullanılabilir.

Şirketinizin iş gücünün kullandığı istemci uygulamalarını yönetmek için Microsoft Intune kullanabilirsiniz. Intune, veri şifreleme ve ek erişim gereksinimleri gibi özellikleri kullanmanıza olanak sağlar. Intune ile uygulama yönetimi hakkında daha fazla bilgi edinmek için bkz. Intune uygulama yönetimi. Power BI mobil uygulamanın Intune ilkesiyle çalışmasını sağlamak için aşağıdaki adımları kullanın.

1. **Azure Active Directory** ve ardından **uygulama kayıtları**' na gidin.
2. Yerel istemci Uygulamanızı kaydederken adım 3 ' te yapılandırılan uygulamayı seçin.
3. Uygulamanın sayfasında, **API izinleri**' ni seçin.
4. **Izin Ekle**' ye tıklayın. 
5. **Kuruluşumun kullandığı API 'ler**altında "Microsoft Mobil uygulama yönetimi" ifadesini arayın ve seçin.
6. Uygulamaya **Devicemanagementmanagedapps. ReadWrite** iznini ekleyin
7. Uygulamaya izin erişimi vermek için **yönetici Izni ver** ' e tıklayın.
8. [Uygulama koruma ilkeleri oluşturma ve atama '](https://docs.microsoft.com/intune/app-protection-policies)ya başvurarak istediğiniz Intune ilkesini yapılandırın.

## <a name="troubleshooting"></a>Sorun giderme

Uygulama bir raporu birkaç dakikadan uzun bir süre yüklemeye çalıştıktan sonra bir hata sayfası döndürürse, zaman aşımı ayarını değiştirmeniz gerekebilir. Varsayılan olarak, uygulama proxy 'Si bir isteğe yanıt vermek için 85 saniyeye kadar geçen uygulamaları destekler. Bu ayarı 180 saniyeye uzatmak için, uygulama ara sunucusu ayarları sayfasında, arka uç zaman aşımını **uzun** olarak seçin. Hızlı ve güvenilir raporlar oluşturma hakkında ipuçları için bkz. [Power BI Reports En Iyi yöntemleri](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Sonraki adımlar

- [Yerel istemci uygulamalarının proxy uygulamalarıyla etkileşime geçmesini sağlama](application-proxy-configure-native-client-application.md)
- [Power BI Mobile Apps 'te şirket içi rapor sunucusu raporlarını ve KPI 'lerini görüntüleme](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
