---
title: Yenilikler Sürüm notları-Azure Active Directory | Microsoft Docs
description: En son sürüm notları, bilinen sorunlar, hata düzeltmeleri, kullanım dışı işlevler ve yaklaşan değişiklikler gibi Azure Active Directory yenilikleri öğrenin.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7db35b89903de6c5027bb7ea445245d84b96d6ef
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473300"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory yenilikler nelerdir?

>Bu URL 'YI kopyalayıp yapıştırarak güncelleştirmeler için ne zaman geri ziyaret ettikinizle ilgili bildirim alın: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` ![RSS akışı okuyucu simgenizi](./media/whats-new/feed-icon-16x16.png) akış okuyucusu.

Azure AD, iyileştirmeleri sürekli olarak alır. En son gelişmelerden haberdar olmak için, bu makalede hakkında bilgi verilmektedir:

- En son yayınlar
- Bilinen sorunlar
- Hata düzeltmeleri
- Kullanım dışı işlev
- Değişiklik planları

Bu sayfa aylık olarak güncelleştirildiğinden düzenli olarak yeniden ziyaret edin. Altı aydan eski olan öğelere bakıyorsanız, [Azure Active Directory yenilikler Için arşivde](whats-new-archive.md)bulabilirsiniz.

---

## <a name="october-2019"></a>2019 Ekim

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Azure AD Kimlik Koruması risk algılamaları için ıdentityriskevent API 'sinin kullanımdan kaldırılması  

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Geliştirici geri bildirimlerine yanıt olarak Azure AD Premium P2 aboneleri artık Microsoft Graph için yeni riskDetection API 'sini kullanarak Azure AD Kimlik Koruması riskli algılama verilerinde karmaşık sorgular gerçekleştirebilir. Mevcut [ıdentityriskevent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API beta sürümü **10 Ocak 2020 '** den itibaren veri döndürmeyi durdurur. Kuruluşunuz ıdentityriskevent API 'sini kullanıyorsa, yeni riskDetection API 'sine geçiş yapmanız gerekir.

Yeni riskDetection API 'SI hakkında daha fazla bilgi için [risk algılama API 'si başvuru belgelerine](https://aka.ms/RiskDetectionsAPI)bakın.

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>SameSite özniteliği ve Chrome 80 için uygulama ara sunucusu desteği

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Uygulama proxy 'Si  
**Ürün yeteneği:** Access Control

Chrome 80 tarayıcı sürümünden önceki birkaç hafta önce, uygulama proxy tanımlama bilgilerinin **SameSite** özniteliğini nasıl değerlendirdiğinin güncelleştirilmesini planlıyoruz. Chrome 80 ' nin yayınlanmasıyla, **SameSite** özniteliğini belirtmeyen tüm tanımlama bilgileri `SameSite=Lax`olarak ayarlanmış gibi kabul edilir.

Bu değişiklik nedeniyle olumsuz olumsuz etkileri önlemeye yardımcı olmak için uygulama proxy 'Si erişimi ve oturum tanımlama bilgilerini şu şekilde güncelleştiriyoruz:

- **Güvenli tanımlama bilgisi kullan** ayarı için varsayılan değer **Evet**olarak ayarlanıyor.

- **SameSite** özniteliği için varsayılan değer **none**olarak ayarlanıyor.

    >[!NOTE]
    > Uygulama proxy 'Si erişimi tanımlama bilgileri her zaman güvenli kanallar üzerinden iletilir. Bu değişiklikler yalnızca oturum tanımlama bilgileri için geçerlidir.

Uygulama proxy 'Si tanımlama bilgisi ayarları hakkında daha fazla bilgi için bkz. [Azure Active Directory ' de şirket içi uygulamalara erişmek Için tanımlama bilgisi ayarları](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Uygulama kayıt portalından (apps.dev.microsoft.com) Uygulama kayıtları (eski) ve yakınsama uygulama yönetimi artık kullanılamayacak

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** yok  
**Ürün yeteneği:** Geliştirici deneyimi

Yakın gelecekte, Azure AD hesapları olan kullanıcılar artık uygulama kayıt portalı 'nı (apps.dev.microsoft.com) kullanarak Yakınsanan uygulamaları kaydedemez veya Uygulama kayıtları (eski) uygulamaları kaydedebilir ve yönetebilir Azure portal deneyim.

Yeni Uygulama kayıtları deneyimi hakkında daha fazla bilgi edinmek için [Azure Portal eğitim kılavuzundaki uygulama kayıtları](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide)bakın.

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Kullanıcı başına MFA 'dan koşullu erişim tabanlı MFA 'ya geçiş sırasında kullanıcıların artık yeniden kaydedilmesi gerekmez

**Şunu yazın:** Düzenle  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Kullanıcıların Kullanıcı başına Multi-Factor Authentication (MFA) için devre dışı bırakıldıklarında ve sonra koşullu erişim ilkesi aracılığıyla MFA için etkinleştirildiklerinde yeniden kaydolmaları gereken bilinen bir sorunu düzelttik.

Kullanıcıların yeniden kaydolmesini gerektirmek için, Azure AD portalındaki kullanıcının kimlik doğrulama yöntemlerinden **gereken MFA 'yı yeniden kaydet** seçeneğini belirleyebilirsiniz. Kullanıcı başına MFA 'dan koşullu erişim tabanlı MFA 'ya Kullanıcı geçirme hakkında daha fazla bilgi için bkz. [Kullanıcı BAŞıNA MFA 'Dan koşullu erişim tabanlı MFA 'ya Kullanıcı dönüştürme](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>SAML belirtecinizdeki talepleri dönüştürmek ve göndermek için yeni yetenekler

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** SSO

SAML belirteçinizdeki talepleri özelleştirmenize ve göndermenize yardımcı olacak ek yetenekler ekledik. Bu yeni yetenekler şunlardır:

- Ek talep dönüştürme işlevleri, talepte göndereceğiniz değeri değiştirmenize yardımcı olur.

- Tek bir talebe birden çok dönüşüm uygulama özelliği.

- Kullanıcı türüne ve kullanıcının ait olduğu gruba göre talep kaynağını belirtme özelliği.

Bu yeni yetenekler hakkında daha fazla bilgi için, bkz. [Kurumsal uygulamalar IÇIN SAML belirtecinde verilen talepleri özelleştirme](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD 'de son kullanıcılar için yeni oturum açma görevlerim sayfası

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** & Raporlamayı izleme

Kuruluşunuzun kullanıcılarının olağandışı etkinlikleri denetlemek için en son oturum açma geçmişini görüntülemesine olanak sağlamak üzere yeni bir **oturum açma** sayfası (https://mysignins.microsoft.com)) ekledik. Bu yeni sayfa, kullanıcılarınızın şunları görmesini sağlar:

- Herkes parolasını tahmin etmeye çalışıyorsa.

- Bir saldırgan hesabında ve bu konumdan başarıyla oturum açtı.

- Saldırganın erişmeye çalıştığı uygulamalar.

Daha fazla bilgi için, bkz. [Kullanıcılar artık olağan dışı etkinlik bloguna ait oturum açma geçmişini denetleyebilir](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Azure AD Domain Services (Azure AD DS) klasik 'ten Azure Resource Manager sanal ağlara geçirme

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD Domain Services  
**Ürün yeteneği:** Azure AD Domain Services

Klasik sanal ağlarda takılı olan müşterilerimiz için çok güzel bir haberimiz var! Artık, klasik bir sanal ağdan mevcut bir Kaynak Yöneticisi sanal ağa tek seferlik bir geçiş gerçekleştirebilirsiniz. Kaynak Yöneticisi sanal ağa taşıdıktan sonra, hassas parola ilkeleri, e-posta bildirimleri ve denetim günlükleri gibi ek ve yükseltilmiş özelliklerden yararlanabilirsiniz.

Daha fazla bilgi için bkz. [Klasik sanal ağ modelinden Kaynak Yöneticisi Için önizleme-geçirme Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C sayfa sözleşmesi düzenine yönelik güncelleştirmeler

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C

Azure AD B2C için sayfa sözleşmesinin sürüm 1.2.0 için bazı yeni değişiklikler ekledik. Bu güncelleştirilmiş sürümde, artık öğeleriniz için yükleme sırasını denetleyebilir ve bu da, stil sayfası (CSS) yüklenirken gerçekleşen titreşimi durdurmaya da yardımcı olabilir.

Sayfa sözleşmesinde yapılan değişikliklerin tam listesi için bkz. [sürüm değişiklik günlüğü](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Yeni çalışma alanlarıyla birlikte uygulamalarım sayfasına güncelleştir (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulamalarım  
**Ürün yeteneği:** Access Control

Artık, kuruluşunuzun kullanıcılarının, uygulamaları bulmasını kolaylaştırmak için yeni çalışma alanları özelliğini kullanma dahil olmak üzere yepyeni uygulamalara görüntüleme ve bu deneyimle erişme biçimini özelleştirebilirsiniz. Yeni çalışma alanları işlevselliği, kuruluşunuzun kullanıcılarının zaten erişimi olan uygulamalar için bir filtre işlevi görür.

Yeni uygulamalarım deneyimini kullanıma alma ve çalışma alanları oluşturma hakkında daha fazla bilgi için bkz. [My Apps (Önizleme) portalında çalışma alanları oluşturma](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Aylık etkin kullanıcı tabanlı faturalandırma modeli için destek (genel kullanılabilirlik)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C

Azure AD B2C artık aylık etkin kullanıcıların (MAU) faturalandırmasını desteklemektedir. MAU faturalandırma, bir takvim ayı sırasında kimlik doğrulama etkinliği olan benzersiz kullanıcı sayısını temel alır. Mevcut müşteriler, bu yeni faturalandırma yöntemine dilediğiniz zaman geçebilir.

1 Kasım 2019 ' den itibaren, tüm yeni müşteriler otomatik olarak bu yöntem kullanılarak faturalandırılacaktır. Bu Faturalandırma yöntemi, müşterilerin maliyet avantajları ve ilerleyebilme aracılığıyla faydalanır.

Daha fazla bilgi için bkz. [aylık etkin kullanıcıları faturalama modeline yükseltme](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD portalındaki birleştirilmiş güvenlik menü öğesi

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Artık, mevcut Azure AD güvenlik özelliklerinin tümüne yeni **güvenlik** menü öğesinden ve **arama** çubuğundan Azure Portal erişebilirsiniz. Ayrıca, **güvenlik-Başlarken**adlı yeni **güvenlik** giriş sayfası, ortak belgelerimize, güvenlik kılavuzumuza ve dağıtım kılavuzlarımıza bağlantılar sağlar.

Yeni **güvenlik** menüsü şunları içerir:

- Koşullu Erişim
- Kimlik Koruması
- Güvenlik Merkezi
- Kimlik güvenli puanı
- Kimlik doğrulama yöntemleri
- MFA
- Risk raporları-riskli kullanıcılar, riskli oturum açmalar, risk algılamaları
- Ve daha fazlası...

Daha fazla bilgi için bkz. [güvenlik-Başlarken](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Otomatik yenileme ile geliştirilmiş Office 365 grupları süre sonu ilkesi

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi

Office 365 grupları süre sonu ilkesi, üyeleri tarafından etkin olarak kullanılan grupları otomatik olarak yenilemek üzere geliştirilmiştir. Gruplar, Outlook, SharePoint ve takımlar dahil olmak üzere tüm Office 365 uygulamaları genelinde Kullanıcı etkinliğine göre autorenewed olacaktır.

Bu geliştirme, Grup süre sonu bildirimlerinizi azaltmaya yardımcı olur ve etkin grupların kullanılabilir olmaya devam etmesine yardımcı olur. Office 365 gruplarınız için zaten etkin bir süre sonu ilkeniz varsa, bu yeni işlevselliği açmak için herhangi bir şey yapmanız gerekmez.

Daha fazla bilgi için bkz. [Office 365 grupları için süre sonu Ilkesini yapılandırma](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Azure AD Domain Services (Azure AD DS) oluşturma deneyimi güncelleştirildi

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Azure AD Domain Services  
**Ürün yeteneği:** Azure AD Domain Services

Azure AD Domain Services (Azure AD DS) yeni ve geliştirilmiş bir oluşturma deneyimi içerecek şekilde güncelleştirdik ve yalnızca üç tıklamayla yönetilen bir etki alanı oluşturmanıza yardımcı olur! Ayrıca, artık Azure AD DS bir şablondan karşıya yükleyebilir ve dağıtabilirsiniz.

Daha fazla bilgi için bkz. [öğretici: Azure Active Directory Domain Services örneği oluşturma ve yapılandırma](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>Eylül 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Değişiklik planı: Power BI içerik paketlerinin kullanımdan kaldırılması

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & Raporlamayı izleme

1 Ekim 2019 ' den başlayarak Power BI, Azure AD Power BI içerik paketi de dahil olmak üzere tüm içerik paketlerini kullanımdan kaldırmaya başlayacaktır. Bu içerik paketine alternatif olarak Azure AD çalışma kitaplarını kullanarak Azure AD ile ilgili hizmetleriniz hakkında öngörüler elde edebilirsiniz. Yalnızca rapor modundaki koşullu erişim ilkeleriyle ilgili çalışma kitapları, uygulama onayı tabanlı Öngörüler ve daha fazlası dahil olmak üzere ek çalışma kitapları geliyor.

Çalışma kitapları hakkında daha fazla bilgi için bkz. [Azure izleyici çalışma kitaplarını Azure Active Directory raporları için kullanma](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). İçerik paketlerinin kullanımdan kaldırılması hakkında daha fazla bilgi için bkz. [Power BI Template Apps genel kullanılabilirliği](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) blog gönderisi.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Profilimi Microsoft Office hesabı sayfasıyla yeniden adlandırma ve Tümleştirme

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Profilim/Hesabım  
**Ürün yeteneği:** İş

Ekim 'den itibaren profilim deneyimi Hesabım olur. Bu değişikliğin bir parçası olarak, şu anda şöyle ki, **Profilim** **Hesabım**olarak değişecektir. Adlandırma değişikliğinin en üstünde ve bazı tasarım geliştirmelerinden, güncelleştirilmiş deneyim Microsoft Office hesabı sayfasıyla ek tümleştirme sunacak. Özellikle, **genel bakış hesabı** sayfasından Office yüklemelerine ve aboneliklerine, **Gizlilik** sayfasından Office ile ilgili iletişim tercihlerini de erişebileceksiniz.

Profilim (Önizleme) deneyimi hakkında daha fazla bilgi için bkz. [Profilim (Önizleme) portalına genel bakış](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Azure AD portalındaki CSV dosyalarını kullanarak grupları ve üyeleri toplu olarak yönetme (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün yeteneği:** İş

Azure AD portalındaki toplu Grup Yönetimi deneyimlerinin genel önizleme kullanılabilirliğine duyurmaktan mutluluk duyuyoruz. Artık aşağıdakiler dahil olmak üzere grupları ve üye listelerini yönetmek için bir CSV dosyası ve Azure AD portalını kullanabilirsiniz:

- Gruptan üye ekleme veya kaldırma.

- Dizindeki grupların listesi indiriliyor.

- Belirli bir grup için Grup üyeleri listesi indiriliyor.

Daha fazla bilgi için bkz. [toplu üye ekleme](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [üyeleri toplu kaldırma](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [üyeleri toplu indirme listesi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)ve [Toplu indirme grupları listesi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dinamik izin artık yeni bir yönetici onay uç noktası aracılığıyla destekleniyor

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması

Microsoft Identity platformunda dinamik izin modelini kullanmak isteyen uygulamalar için yararlı olan dinamik izni desteklemek için yeni bir yönetici onay uç noktası oluşturduk.

Bu yeni uç noktanın nasıl kullanılacağı hakkında daha fazla bilgi için, bkz. [Yönetici onay uç noktası kullanma](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-azure-ad-global-reader-role"></a>Yeni Azure AD Genel okuyucu rolü

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** RAPORDAKI  
**Ürün yeteneği:** Access Control

24 Eylül 2019 ' den itibaren, genel okuyucu adlı yeni bir Azure Active Directory (AD) rolü kullanıma sunacağız. Bu piyasaya çıkma üretim ve küresel bulut müşterileri (GCC) ile başlar ve Ekim 'de dünya çapında bir işlem görür.

Genel okuyucu rolü, genel yöneticiye salt okunurdur. Bu roldeki kullanıcılar Microsoft 365 hizmetleri genelinde ayarları ve yönetim bilgilerini okuyabilir, ancak yönetim eylemleri alamaz. Kuruluşunuzdaki genel yönetici sayısını azaltmaya yardımcı olmak için genel okuyucu rolünü oluşturduk. Genel yönetici hesapları güçlü ve saldırılara karşı savunmasız olduğundan, beşten az genel yönetici olmasını öneririz. Planlama, denetim veya araştırmalar için genel okuyucu rolünü kullanmanızı öneririz. Ayrıca, küresel bir yönetici rolü gerekmeden çalışmanın yapılmasına yardımcı olması için Exchange Yöneticisi gibi diğer sınırlı yönetici rolleriyle birlikte genel okuyucu rolünü kullanmanızı öneririz.

Genel okuyucu rolü, yeni Microsoft 365 Yönetim Merkezi, Exchange Yönetim Merkezi, takımlar Yönetim Merkezi, Güvenlik Merkezi, Uyumluluk Merkezi, Azure AD Yönetim Merkezi ve cihaz yönetimi Yönetim Merkezi ile birlikte kullanılabilir.

>[!NOTE]
> Genel Önizleme başlangıcında, genel okuyucu rolü şu şekilde çalışmaz: SharePoint, Privileged Access Management, Müşteri Kasası, duyarlılık etiketleri, takımlar yaşam döngüsü, takımlar raporlama & Çağrı Analizi, takımlar IP telefon cihazı yönetimi ve takımlar uygulaması Kataloglarını. Bu hizmetlerin tümü, gelecekte rolle çalışmak üzere tasarlanmıştır.

Daha fazla bilgi için [Azure Active Directory Içindeki yönetici rolü izinleri](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)bölümüne bakın.

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Azure Active Directory Uygulama Ara Sunucusu kullanarak Power BI Mobil uygulamanızdan şirket içi rapor sunucusuna erişin

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama proxy 'Si  
**Ürün yeteneği:** Access Control

Power BI Mobile App ile Azure AD Uygulama Ara Sunucusu arasında yeni tümleştirme, Power BI mobil uygulamada güvenli bir şekilde oturum açmanızı ve şirket içi Power BI Rapor Sunucusu barındırılan kuruluşunuzun tüm raporlarını görüntülemenize olanak sağlar.

Uygulamanın indirileceği dahil Power BI Mobil uygulama hakkında daha fazla bilgi için [Power BI sitesine](https://powerbi.microsoft.com/mobile/)bakın. Power BI mobil uygulamayı Azure AD Uygulama Ara Sunucusu ile ayarlama hakkında daha fazla bilgi için bkz. [azure ad uygulama ara sunucusu ile Power BI mobil uzaktan erişimi etkinleştirme](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>AzureADPreview PowerShell modülünün yeni sürümü kullanılabilir

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Dizinden

Yeni cmdlet 'ler, Azure AD 'de aşağıdakiler de dahil olmak üzere özel rolleri tanımlamaya ve atamaya yardımcı olmak için AzureADPreview modülüne eklenmiştir:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect yeni sürümü

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Dizinden

Otomatik yükseltme müşterileri için Azure AD Connect güncelleştirilmiş bir sürümünü yayımladık. Bu yeni sürüm bazı yeni özellikler, geliştirmeler ve hata düzeltmeleri içerir. Bu yeni sürüm hakkında daha fazla bilgi için bkz. [Azure AD Connect: sürüm yayınlama geçmişi](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA) sunucusu, sürüm 8.0.2 artık kullanılabilir

**Şunu yazın:** Düzenle  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması

MFA sunucusunu 1 Temmuz 2019 ' den önce etkinleştiren mevcut bir müşteriyseniz, artık MFA sunucusu 'nun en son sürümünü (sürüm 8.0.2) indirebilirsiniz. Bu yeni sürümde şunları yaptık:

- Bir sorun düzeltildi böylece Azure AD eşitleme bir kullanıcıyı devre dışı durumundan etkin olarak değiştirdiğinde kullanıcıya bir e-posta gönderilir.

- Etiketler işlevini kullanmaya devam ederken müşterilerin başarılı bir şekilde yükseltebilmesi için bir sorun düzeltildi.

- Kosovo (+ 383) ülke kodu eklendi.

- MultiFactorAuthSvc. log dosyasına bir kerelik atlama denetim günlüğü eklendi.

- Web hizmeti SDK 'sının performansı geliştirildi.

- Diğer küçük hatalar düzeltildi.

Microsoft, 1 Temmuz 2019 ' den itibaren Yeni dağıtımlar için MFA sunucusunu sunmayı durdurdu. Multi-Factor Authentication gerektiren yeni müşterilerin bulut tabanlı Azure Multi-Factor Authentication kullanması gerekir. Daha fazla bilgi için bkz. [bulut tabanlı Azure Multi-Factor Authentication dağıtımı planlama](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>Ağustos 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Azure AD portalında (Genel Önizleme), gruplar için gelişmiş arama, filtreleme ve sıralama kullanılabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün yeteneği:** İş

Azure AD portalındaki gelişmiş gruplar ile ilgili deneyimlerin genel önizleme kullanılabilirliğine duyurmaktan mutluluk duyuyoruz. Bu geliştirmeler, aşağıdakileri sağlayarak grupları ve üye listelerini daha iyi yönetmenize yardımcı olur:

- Gruplar listesinde alt dize arama gibi gelişmiş arama özellikleri.
- Üye ve sahip listelerindeki Gelişmiş filtreleme ve sıralama seçenekleri.
- Üye ve sahip listeleri için yeni arama özellikleri.
- Büyük gruplar için daha doğru grup sayısı.

Daha fazla bilgi için [Azure Portal grupları yönetme](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)bölümüne bakın.

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Yeni özel roller uygulama kayıt yönetimi için kullanılabilir (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** RAPORDAKI  
**Ürün yeteneği:** Access Control

Özel Roller (bir Azure AD P1 veya P2 aboneliği ile kullanılabilir) artık, belirli izinlerle rol tanımları oluşturmanıza ve ardından bu rolleri belirli kaynaklara atamaya izin vererek size ayrıntılı erişim sağlamanıza yardımcı olabilir. Şu anda, uygulama kayıtlarını yönetmek ve ardından rolü belirli bir uygulamaya atamak için izinleri kullanarak özel roller oluşturursunuz. Özel roller hakkında daha fazla bilgi için, bkz. [Azure Active Directory (Önizleme) Içinde özel yönetici rolleri](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Şu anda görmezseniz ek izinlere veya kaynaklara ihtiyacınız varsa, [Azure geri bildirim sitenize](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) geri bildirimde bulunabilir ve isteğinizi güncelleştirme yol haritemiz olarak ekleyeceğiz.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Yeni sağlama günlükleri, uygulama sağlama dağıtımınızı izlemenize ve sorunlarını gidermenize yardımcı olabilir (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi

Kullanıcı ve grup sağlama dağıtımını izlemenize ve sorunlarını gidermenize yardımcı olacak yeni sağlama günlükleri mevcuttur. Bu yeni günlük dosyaları aşağıdakiler hakkında bilgiler içerir:

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial) 'da başarıyla oluşturulan gruplar
- [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws) öğesinden alınan roller
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) 'den hangi çalışanların içeri aktarılmadığı

Daha fazla bilgi için, [Azure Active Directory portalında (Önizleme) raporları sağlama](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)konusuna bakın.

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Tüm Azure AD yöneticileri için yeni güvenlik raporları (genel kullanılabilirlik)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Varsayılan olarak, Azure AD yöneticileri, Azure AD içindeki modern güvenlik raporlarına yakında erişebilecektir. Eylül sonuna kadar, eski raporlara geri dönmek için modern güvenlik raporlarının en üstündeki başlığı kullanabileceksiniz.

Modern güvenlik raporları, aşağıdakiler de dahil olmak üzere eski sürümlerden ek yetenekler sağlayacaktır:

- Gelişmiş filtreleme ve sıralama
- Eksik Kullanıcı riski gibi toplu eylemler
- Güvenliği aşılmış veya güvenli varlıkların onayı
- Risk durumu, kapsayan: risk, kapatılan, düzeltilen ve onaylanan güvenliği aşılmış
- Risk ile ilgili yeni algılamalar (Azure AD Premium aboneler için kullanılabilir)

Daha fazla bilgi için bkz. [riskli kullanıcılar](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [riskli oturum açma](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)işlemleri ve [risk algılamaları](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Kullanıcı tarafından atanan yönetilen kimlik, sanal makineler ve sanal makine ölçek kümeleri (genel kullanılabilirlik) için kullanılabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure kaynakları için Yönetilen kimlikler  
**Ürün yeteneği:** Geliştirici deneyimi

Kullanıcı tarafından atanan Yönetilen kimlikler artık sanal makineler ve sanal makine ölçek kümeleri için genel kullanıma sunulmuştur. Bunun bir parçası olarak Azure, Azure AD kiracısında kullanımda olan aboneliğin güvendiği ve bir veya daha fazla Azure hizmet örneğine atanabilecek bir kimlik oluşturabilir. Kullanıcı tarafından atanan Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Kullanıcılar bir mobil uygulama veya donanım belirteci (genel kullanım) kullanarak parolalarını sıfırlayabilir

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Self servis parola sıfırlama  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması

Kuruluşunuz ile bir mobil uygulama kaydeden kullanıcılar artık Microsoft Authenticator uygulamasından bir bildirim seçerek veya mobil uygulamasından veya donanım belirtecinden bir kod girerek kendi parolalarını sıfırlayabilir.

Daha fazla bilgi için bkz. [nasıl çalıştığını öğrenin: Azure AD self servis parola sıfırlama](https://aka.ms/authappsspr). Kullanıcı deneyimi hakkında daha fazla bilgi için bkz. [kendi iş veya okul parolanızı sıfırlama genel bakış](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET, yerinde senaryolar için MSAL.NET paylaşılan önbelleğini yoksayar

**Şunu yazın:** Düzenle  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD kimlik doğrulama kitaplığı (ADAL.NET) sürüm 5.0.0-Preview sürümünden itibaren, uygulama geliştiricileri [Web uygulamaları ve Web API 'leri için hesap başına bir önbellek serileştirmelidir](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Aksi takdirde, bazı belirli kullanım durumları ile birlikte, `UserAssertion`[Şirket adına](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)kullanan bazı senaryolar ayrıcalık yükselmesine neden olabilir. Bu güvenlik açığından kaçınmak için, ADAL.NET artık, için Microsoft kimlik doğrulama kitaplığı için DotNet (MSAL.NET) paylaşılan önbellek için Microsoft kimlik doğrulama kitaplığını yok sayar.

Bu sorun hakkında daha fazla bilgi için bkz. [Azure Active Directory kimlik doğrulaması kitaplığı ayrıcalık yükselmesi güvenlik açığı](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Azure AD Uygulaması galerisinde yeni Federasyon uygulamaları kullanılabilir-Ağustos 2019

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

Ağustos 2019 ' de, uygulama galerisine federasyon desteğiyle bu 26 yeni uygulamalar ekledik:

[Cıvic platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [bilitodoks](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [viareport 'ın Inativ Portal (Avrupa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [deneme](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [akademik katılım](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Öncelik matrisi](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [uploadilgilenme](https://uploadcare.com/accounts/signup/), [Carbonite uç noktası yedeklemesi](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [Incom ile cpqsync](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [sunma. Media™ Portal](https://portal.deliver.media), [Frontline eğitim](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stakıcat ad Connect](https://www.stashcat.com), [BLINK](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [renklere göre izleme](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial) [mana, EAB, stratejik bakım](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](https://aka.ms/azureadapprequest)listeleme bölümüne bakın.

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>AzureAD PowerShell ve AzureADPreview PowerShell modüllerinin yeni sürümleri mevcuttur

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Dizinden

AzureAD ve AzureAD Preview PowerShell modüllerine yönelik yeni güncelleştirmeler mevcuttur:

- AzureAD modülündeki `Get-AzureADDirectoryRole` parametresine yeni bir `-Filter` parametresi eklendi. Bu parametre, cmdlet tarafından döndürülen dizin rollerini filtrelemenize yardımcı olur.
- Yeni cmdlet 'ler, Azure AD 'de aşağıdakiler de dahil olmak üzere özel rolleri tanımlamaya ve atamaya yardımcı olmak için AzureADPreview modülüne eklenmiştir:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Azure portal dinamik grup kuralı oluşturucusunun Kullanıcı arabirimine yönelik iyileştirmeler

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün yeteneği:** İş

Yeni bir kuralı daha kolay ayarlamanıza veya mevcut kuralları değiştirmenize yardımcı olması için Azure portal kullanılabilir olan dinamik grup kuralı Oluşturucusu 'nda bazı Kullanıcı arabirimi geliştirmeleri yaptık. Bu tasarım geliştirmesi, yalnızca bir tane yerine en fazla beş ifadeye sahip kurallar oluşturmanıza olanak sağlar. Ayrıca, kullanım dışı bırakılan cihaz özelliklerini kaldırmak için cihaz özelliği listesini güncelleştirdik.

Daha fazla bilgi için bkz. [dinamik üyelik kurallarını yönetme](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Yeni Microsoft Graph uygulama izni, erişim gözden geçirmeleri ile kullanıma sunuldu

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Erişim Incelemeleri  
**Ürün yeteneği:** Kimlik yönetimi

Uygulamaların grup üyelikleri ve uygulama atamaları için otomatik olarak erişim gözden geçirmeleri oluşturmasına ve almasına izin veren yeni bir Microsoft Graph uygulama izni (`AccessReview.ReadWrite.Membership`) sunuyoruz. Bu izin, zamanlanmış işleriniz veya Otomasyon kapsamında oturum açmış bir kullanıcı bağlamı gerekmeden kullanılabilir.

Daha fazla bilgi için bkz. [PowerShell blogu ile Microsoft Graph uygulama izinleri kullanarak Azure AD erişim incelemeleri oluşturma örneği](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD etkinlik günlükleri artık Azure Izleyici 'de kamu bulutu örnekleri için kullanılabilir

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & Raporlamayı izleme

Azure AD etkinlik günlüklerinin artık Azure Izleyici 'deki kamu bulutu örnekleri için kullanılabilir olduğunu duyurmaktan mutluluk duyuyoruz. Artık [SumoLogic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)ve [arctıma](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight)gibi SIEM araçlarınızla tümleştirebilmek için depolama hesabınıza veya bir olay hub 'ına Azure AD günlükleri gönderebilirsiniz. 

Azure Izleyici 'yi ayarlama hakkında daha fazla bilgi için bkz. Azure [izleyici 'de Azure ad etkinlik günlükleri](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Kullanıcılarınızı yeni, geliştirilmiş güvenlik bilgileri deneyimiyle güncelleştirin

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:**  Kimlik doğrulamaları (oturum açma)   
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması

25 Eylül 2019 ' de, Kullanıcı güvenlik bilgilerini kaydetmek ve yönetmek ve yalnızca yeni ve [geliştirilmiş sürümü](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)açmak için eski, gelişmiş olmayan güvenlik bilgileri deneyimini kapatıyoruz. Bu, kullanıcılarınızın artık eski deneyimi kullanamayacağı anlamına gelir.

Gelişmiş güvenlik bilgileri deneyimi hakkında daha fazla bilgi için [yönetici belgelerimize](https://aka.ms/securityinfodocs) ve [Kullanıcı belgelerimize](https://aka.ms/securityinfoguide)bakın.

#### <a name="to-turn-on-this-new-experience-you-must"></a>Bu yeni deneyimi etkinleştirmek için şunları yapmanız gerekir:

1. Azure portal genel yönetici veya Kullanıcı Yöneticisi olarak oturum açın.

2.  **Azure Active Directory > Kullanıcı ayarları ' na gidin > erişim paneli Önizleme özellikleri ayarlarını yönetin**.

3. Kullanıcılar, **güvenlik bilgilerini kaydetmek ve yönetmek için Önizleme özelliklerini kullanabilir-gelişmiş** alanı, **Seçili**' i seçin ve ardından bir Kullanıcı grubu seçebilir ya da bu özelliği kiracının tüm kullanıcıları için açmak üzere **Tümünü** Seç ' i seçin.

4. \* * Kullanıcılar, güvenliği kaydetmek ve yönetmek için Önizleme özelliklerini kullanabilir * * bilgi * * * * alanı, **hiçbiri**' ni seçin.

5. Ayarlarınızı kaydedin.

    Ayarlarınızı kaydettikten sonra eski güvenlik bilgileri deneyimine artık erişemezsiniz.

>[!Important]
>25 Eylül 2019 tarihinden önce bu adımları tamamlamazsanız, Azure Active Directory kiracınız gelişmiş deneyim için otomatik olarak etkinleştirilir. Sorularınız varsa lütfen registrationpreview@microsoft.comadresinden bizimle iletişime geçin.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Oturum açma işlemleri kullanan kimlik doğrulama istekleri daha kesin olarak doğrulanacak

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Standartlarına

2 Eylül 2019 ' den itibaren POST yöntemini kullanan kimlik doğrulama istekleri HTTP standartlarına karşı daha kesin olarak onaylanır. Özellikle, boşluklar ve çift tırnak işaretleri (") artık istek formu değerlerinden kaldırılmayacak. Bu değişikliklerin mevcut istemcileri bozmak beklenmez ve Azure AD 'ye gönderilen isteklerin her seferinde güvenilir bir şekilde işlendiğinden emin olmaya yardımcı olur.

Daha fazla bilgi için bkz. [Azure AD bölünmesi değişiklikleri bildirimleri](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Temmuz 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Değişiklik planı: yalnızca TLS 1,2 ' i destekleyecek uygulama proxy hizmeti güncelleştirmesi

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Uygulama proxy 'Si  
**Ürün yeteneği:** Access Control

En güçlü şifreimizi size sağlamaya yardımcı olmak için, uygulama proxy hizmeti erişimini yalnızca TLS 1,2 protokollerine sınırlandırmaya başlayacağız. Bu sınırlama, başlangıçta zaten TLS 1,2 protokollerini kullanan müşterilere dağıtılır, bu nedenle etkiyi görmezsiniz. TLS 1,0 ve TLS 1,1 protokollerinin kullanım dışı bırakılması, 31 Ağustos 2019 tarihinde tamamlanacaktır. Hala TLS 1,0 ve TLS 1,1 kullanan müşteriler, bu değişikliğe hazırlanmak için gelişmiş bir bildirim alır.

Bu değişiklik boyunca uygulama ara sunucusu hizmetiyle bağlantıyı sürdürmek için, istemci-sunucu ve tarayıcı-sunucu birleşimlerinizin TLS 1,2 kullanacak şekilde güncelleştirildiğinden emin olmanızı öneririz. Ayrıca, uygulama proxy 'Si hizmeti aracılığıyla yayınlanan uygulamalara erişmek için çalışanlarınızın kullandığı tüm istemci sistemlerini dahil ettiğinizden emin olmanızı öneririz.

Daha fazla bilgi için bkz. [Azure Active Directory Içindeki uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Değişiklik planı: uygulama galerisine yönelik tasarım güncelleştirmeleri geliyor

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** SSO

Yeni Kullanıcı arabirimi değişiklikleri, **Uygulama Ekle** dikey penceresinin Galeri alanından **Ekle** ' nin tasarımına geliyor. Bu değişiklikler, otomatik sağlamayı, OpenID Connect, Security Assertion Markup Language (SAML) ve parola çoklu oturum açmayı (SSO) destekleyen uygulamalarınızı daha kolay bir şekilde bulmanıza yardımcı olur.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Değişiklik planı: MFA sunucusu IP adresinin Office 365 IP adresinden kaldırılması

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması

MFA sunucusu IP adresini [Office 365 IP adresi ve URL Web hizmetinden](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service)kaldırıyoruz. Şu anda güvenlik duvarı ayarlarınızı güncelleştirmek için bu sayfalara güveniyorsanız, Başlarken 'in **Azure Multi-Factor Authentication sunucusu güvenlik duvarı gereksinimleri** bölümünde belgelenen IP adreslerinin listesini de dahil ettiğinizden emin olmanız gerekir. [ Azure Multi-Factor Authentication Sunucusu](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) makalesinde.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Yalnızca uygulama belirteçleri, artık istemci uygulamasının kaynak kiracısında var olmasını gerektiriyor

**Şunu yazın:** Düzenle  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması

26 Temmuz 2019 ' de, [istemci kimlik bilgileri verme](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)yoluyla yalnızca uygulama belirteçleri sağlıyoruz. Daha önce, uygulamalar, istemci uygulamanın kiracıda olup olmamasına bakılmaksızın diğer uygulamaları çağırmak için belirteçleri alabilir. Bu davranışı, bazen Web API 'Leri olarak adlandırılan tek kiracılı kaynakların yalnızca kaynak kiracısında bulunan istemci uygulamaları tarafından çağrılabilir şekilde güncelleştirdik.

Uygulamanız kaynak kiracısında değilse, bu sorunu çözmek Için `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` bildiren bir hata iletisi alırsınız, [Yönetici onay uç noktasını](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) veya [PowerShell aracılığıyla](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)kiracı 'da istemci uygulama hizmeti sorumlusunu oluşturmanız gerekir, Bu, kiracınızın kiracı içinde çalışmaya yönelik uygulama iznini vermiş olmasını sağlar.

Daha fazla bilgi için bkz. [kimlik doğrulaması yenilikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> İstemci ve API arasındaki mevcut onay gerekli olmaya devam eder. Uygulamalar yine de kendi yetkilendirme denetimlerini yapmakta olmalıdır.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>FIDO2 güvenlik anahtarları kullanılarak Azure AD 'de yeni parolasız oturum açma

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD müşterileri artık, kuruluşlarının kullanıcıları ve grupları için FIDO2 güvenlik anahtarlarını yönetmek üzere ilkeler ayarlayabilir. Son kullanıcılar aynı zamanda güvenlik anahtarlarını kendi kendine kaydedebilir, FIDO özellikli cihazlarda web sitelerindeki Microsoft hesaplarında oturum açmak için anahtarları kullanabilir ve Azure AD 'ye katılmış Windows 10 cihazlarında oturum açabilirler.

Daha fazla bilgi için bkz. yönetici ile ilgili bilgiler için [Azure AD 'de passwordless oturum açma 'Yı etkinleştirme (Önizleme)](/azure/active-directory/authentication/concept-authentication-passwordless) ve son kullanıcı ile ilgili bilgiler için Güvenlik [anahtarı (Önizleme) kullanmak üzere güvenlik bilgilerini ayarlama](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD Uygulaması galerisinde yeni Federasyon uygulamaları kullanılabilir-Temmuz 2019

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

Haziran 2019 ' de, uygulama galerisine federasyon desteği olan bu 18 yeni uygulamayı ekledik:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [parlak model omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Clever Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [acquireıo](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Gevop](https://www.looop.co/schedule-a-demo/), [Productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO erişimi, nııdex uyumluluk ofisi™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallifer](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [hyperanna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [ilaç, wastetanık](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](https://aka.ms/azureadapprequest)listeleme bölümüne bakın.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Bu yeni desteklenen SaaS uygulamaları için Kullanıcı hesabı sağlamayı otomatikleştirin

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** & Raporlamayı izleme

Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Tuş takımı](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federasyon dizini](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Ağ güvenlik grubu için yeni Azure AD Domain Services hizmet etiketi

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD Domain Services  
**Ürün yeteneği:** Azure AD Domain Services

IP adresi ve aralıklarının uzun listesini yönetmeyi yorsanız, Azure ağ güvenlik grubunuzdaki yeni **AzureActiveDirectoryDomainServices** Network Service etiketini kullanarak Azure AD Domain Services sanal ağınıza gelen trafiğin güvenliğini sağlayabilirsiniz ağ alt ağı.

Bu yeni hizmet etiketi hakkında daha fazla bilgi için bkz. [Azure AD Domain Services Için ağ güvenlik grupları](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services için yeni güvenlik denetimleri (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD Domain Services  
**Ürün yeteneği:** Azure AD Domain Services

Azure AD etki alanı hizmeti güvenlik denetimi 'nin genel önizlemeye sunulduğunu duyurmaktan mutluluk duyuyoruz. Güvenlik denetimi, Azure AD etki alanı hizmeti 'ni kullanarak Azure depolama, Azure Log Analytics çalışma alanları ve Azure Olay Hub 'ı dahil olmak üzere hedeflenen kaynaklara güvenlik denetim olayları akışı yaparak size kimlik doğrulama hizmetlerinize yönelik kritik Öngörüler sağlamanıza yardımcı olur. Portal.

Daha fazla bilgi için bkz. [Azure AD Domain Services Için güvenlik denetimlerini etkinleştirme (Önizleme)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Yeni kimlik doğrulama yöntemleri kullanım & öngörüleri (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Self servis parola sıfırlama  
**Ürün yeteneği:** & Raporlamayı izleme

Yeni kimlik doğrulama yöntemleri kullanım & öngörüleri raporları, Azure Multi-Factor Authentication ve self servis parola sıfırlamasının, kayıtlı sayısı dahil olmak üzere kuruluşunuzda nasıl kaydedildiğini ve kullanıldığını anlamanıza yardımcı olabilir her özelliğe yönelik kullanıcılar, parolaları sıfırlamak için self servis parola sıfırlamasının ne sıklıkta kullanıldığı ve hangi yöntemin sıfırlanacağı.

Daha fazla bilgi için bkz. [kimlik doğrulama yöntemleri kullanımı & Öngörüler (Önizleme)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Tüm Azure AD yöneticileri için yeni güvenlik raporları kullanılabilir (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Tüm Azure AD yöneticileri artık riskli **Kullanıcılar** ve **riskli oturum açma** raporlarında gösterildiği gibi yeni güvenlik deneyimini kullanmaya başlamak için, **risk için işaretlenmiş kullanıcılar** raporu gibi mevcut güvenlik raporlarının en üstündeki başlığı seçebilirler. . Zaman içinde, tüm güvenlik raporları eski sürümlerden yeni sürümlere taşınır ve yeni raporlar aşağıdaki ek olanakları sağlar:

- Gelişmiş filtreleme ve sıralama

- Eksik Kullanıcı riski gibi toplu eylemler

- Güvenliği aşılmış veya güvenli varlıkların onayı

- Risk durumu, kapsayan: risk, kapatılan, düzeltilen ve onaylanan güvenliği aşılmış

Daha fazla bilgi için bkz. [riskli kullanıcılar raporu](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) ve [riskli oturum açma bileşenleri raporu](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services için yeni güvenlik denetimleri (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD Domain Services  
**Ürün yeteneği:** Azure AD Domain Services

Azure AD etki alanı hizmeti güvenlik denetimi 'nin genel önizlemeye sunulduğunu duyurmaktan mutluluk duyuyoruz. Güvenlik denetimi, Azure AD etki alanı hizmeti 'ni kullanarak Azure depolama, Azure Log Analytics çalışma alanları ve Azure Olay Hub 'ı dahil olmak üzere hedeflenen kaynaklara güvenlik denetim olayları akışı yaparak size kimlik doğrulama hizmetlerinize yönelik kritik Öngörüler sağlamanıza yardımcı olur. Portal.

Daha fazla bilgi için bkz. [Azure AD Domain Services Için güvenlik denetimlerini etkinleştirme (Önizleme)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>SAML/WS-beslemesiyle yeni B2B Direct Federation (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C

Doğrudan Federasyon, SAML veya WS-Beslikli standartları destekleyen kimlik sistemleriyle çalışarak, BT tarafından yönetilen kimlik çözümü Azure AD olmayan iş ortaklarıyla çalışmanızı kolaylaştırır. Bir iş ortağıyla doğrudan Federasyon ilişkisi ayarladıktan sonra, bu etki alanından davet ettiğiniz tüm yeni Konuk kullanıcıları mevcut kurumsal hesaplarını kullanarak sizinle işbirliği yapabilir ve konuklarınız için Kullanıcı deneyimini daha sorunsuz hale getirir.

Daha fazla bilgi için bkz. [Konuk kullanıcılar için AD FS ve üçüncü taraf sağlayıcılarla doğrudan Federasyon (Önizleme)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Bu yeni desteklenen SaaS uygulamaları için Kullanıcı hesabı sağlamayı otomatikleştirin

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** & Raporlamayı izleme

Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Tuş takımı](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federasyon dizini](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Azure AD portalındaki yinelenen grup adları için yeni denetim

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün yeteneği:** İş

Artık Azure AD portalından bir grup adı oluşturduğunuzda veya güncelleştirdiğinizde, kaynakta mevcut bir grup adını çoğaltdığınıza ilişkin bir denetim yapacağız. Adın zaten başka bir grup tarafından kullanımda olduğunu tespit ettik, adınızı değiştirmeniz istenir.

Daha fazla bilgi için bkz. [Azure AD portalındaki grupları yönetme](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD artık yanıt (yeniden yönlendirme) URI 'Lerinde statik sorgu parametrelerini desteklemektedir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD uygulamaları artık OAuth 2,0 istekleri için statik sorgu parametreleriyle (örneğin, `https://contoso.com/oauth2?idp=microsoft`) Yanıtla (yeniden yönlendirme) URI 'Leri kaydedebilir ve kullanabilir. Statik sorgu parametresi, yanıt URI 'sinin diğer herhangi bir bölümünde olduğu gibi, yanıt URI 'Leri için dize eşlemeye tabidir. URL kodu çözülmüş yeniden yönlendirme URI 'si ile eşleşen kayıtlı bir dize yoksa, istek reddedilir. Yanıt URI 'SI bulunursa, statik sorgu parametresi dahil olmak üzere, kullanıcının yeniden yönlendirileceği tüm dize kullanılır.

Bir güvenlik riskini temsil ettiğinden dinamik yanıt URI 'Leri hala yasaktır ve bir kimlik doğrulama isteği genelinde durum bilgilerini sürdürmek için kullanılamaz. Bu amaçla `state` parametresini kullanın.

Şu anda Azure portal uygulama kayıt ekranları hala sorgu parametrelerini engelliyor. Ancak, uygulamanıza sorgu parametrelerini eklemek ve test etmek için uygulama bildirimini el ile düzenleyebilirsiniz. Daha fazla bilgi için bkz. [kimlik doğrulaması yenilikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Azure AD için etkinlik günlükleri (MS Graph API 'Leri) artık PowerShell cmdlet 'Leri aracılığıyla kullanılabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & Raporlamayı izleme

Azure AD etkinlik günlüklerinin (denetim ve oturum açma raporları) artık Azure AD PowerShell modülü aracılığıyla kullanılabildiğini duyurmak için heyecanlıyız. Daha önce, MS Graph API uç noktaları kullanarak kendi betiklerinizi oluşturabilir ve şimdi bu özelliği PowerShell cmdlet 'lerine genişlettik.

Bu cmdlet 'leri kullanma hakkında daha fazla bilgi için bkz. [Raporlama Için Azure AD PowerShell cmdlet 'leri](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Azure AD 'de denetim ve oturum açma günlükleri için filtre denetimleri güncelleştirildi

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & Raporlamayı izleme

Denetim ve oturum açma günlüğü raporlarını, artık rapor ekranlarında sütun olarak eklemek zorunda kalmadan çeşitli filtreler uygulayabilmeniz için güncelleştirdik. Ayrıca, ekranda kaç filtre göstermek istediğinizi de seçebilirsiniz. Bu güncelleştirmeler, raporların daha kolay okunmasını ve gereksinimlerinize göre daha fazla kapsamlanmasını kolaylaştırmak için birlikte çalışır.

Bu güncelleştirmeler hakkında daha fazla bilgi için bkz. [Denetim günlüklerini filtreleme](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) ve [oturum açma etkinliklerini filtreleme](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Haziran 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Microsoft Graph için yeni riskDetections API (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Microsoft Graph için yeni riskDetections API 'sini duyurmaktan mutluluk duyuyoruz, artık genel önizlemede. Bu yeni API 'YI, kuruluşunuzun kimlik korumasıyla ilgili Kullanıcı ve oturum açma riski algılamalarının bir listesini görüntülemek için kullanabilirsiniz. Bu API 'yi, algılama türü, durumu, düzeyi ve daha fazlası hakkındaki ayrıntılar dahil, risk algılamalarınızı daha verimli bir şekilde sorgulamak için de kullanabilirsiniz.

Daha fazla bilgi için [risk algılama API 'si başvuru belgelerine](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)bakın.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Haziran 2019

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

Haziran 2019 ' de, uygulama galerisine federasyon desteğiyle bu 22 yeni uygulamaları ekledik:

[Azure AD SAML araç seti](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN istemcisi](https://portal.azure.com/), [expenseın](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [yardımcı Yardımcısı](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz-otomobil ofisi](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), Oracle [bulut altyapısı konsolu](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [Siark SAML kimlik doğrulaması](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), bir [satın alınabilen edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [PERCEPTYX](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [proptibir OS](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle için Oracle Erişim Yöneticisi Oracle E-Iş paketi için perakende merchandising, Oracle Erişim Yöneticisi, E-Iş paketi için Oracle ıDCS, PeopleSoft için Oracle IDCs, JD Edler için Oracle ıDCS

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](https://aka.ms/azureadapprequest)listeleme bölümüne bakın.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Bu yeni desteklenen SaaS uygulamaları için Kullanıcı hesabı sağlamayı otomatikleştirin

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** & Raporlamayı izleme

Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Yakınlaştırma](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Haberci](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4.](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Azure AD sağlama hizmeti 'nin gerçek zamanlı ilerlemesini görüntüleme

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi

Azure AD sağlama deneyimini, Kullanıcı sağlama sürecinde ne kadar uzakta olduğunu gösteren yeni bir ilerleme çubuğu içerecek şekilde güncelleştirdik. Bu güncelleştirilmiş deneyim Ayrıca, geçerli döngüyle sağlanan kullanıcı sayısı ve ne kadar Kullanıcı sağlandığını gösteren bilgileri de sağlar.

Daha fazla bilgi için bkz. [Kullanıcı hazırlama durumunu denetleme](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Şirket markası artık oturum kapatma ve hata ekranları üzerinde görünüyor

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD 'yi, şirket markasının artık oturum açma ve hata ekranlarında, oturum açma sayfasından da görünmesini sağlayacak şekilde güncelleştirdik. Bu özelliği açmak için herhangi bir şey yapmanız gerekmez, Azure AD yalnızca Azure portal **Şirket markası** alanında ayarlamış olduğunuz varlıkları kullanır.

Şirket markanızı ayarlama hakkında daha fazla bilgi için bkz. [kuruluşunuzun Azure Active Directory sayfalarına marka ekleme](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure Multi-Factor Authentication (MFA) sunucusu artık Yeni dağıtımlar için kullanılamaz

**Şunu yazın:** Kullanım dışı  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması

1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sunmaz. Kuruluşunuzda Multi-Factor Authentication istemek isteyen yeni müşterilerin artık bulut tabanlı Azure Multi-Factor Authentication kullanması gerekir. MFA sunucusunu 1 Temmuz 'dan önce etkinleştiren müşteriler bir değişiklik görmez. Hala en son sürümü indirebilir, gelecekteki güncelleştirmeleri alabilir ve etkinleştirme kimlik bilgileri oluşturabilirsiniz.

Daha fazla bilgi için bkz. [Azure Multi-Factor Authentication sunucusu](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)kullanmaya başlama. Bulut tabanlı Azure Multi-Factor Authentication hakkında daha fazla bilgi için bkz. [bulut tabanlı azure Multi-Factor Authentication dağıtımı planlama](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Mayıs 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Hizmet değişikliği: uygulama proxy 'Si hizmetinde yalnızca TLS 1,2 protokolleri için gelecekteki destek

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Uygulama proxy 'Si  
**Ürün yeteneği:** Access Control

Müşterilerimiz için en iyi sınıf şifrelemeyi sağlamaya yardımcı olmak için, yalnızca uygulama proxy 'Si hizmetindeki TLS 1,2 protokollerine erişimi sınırlıyoruz. Bu değişiklik, yalnızca TLS 1,2 protokollerini kullanan müşterilere yavaş bir şekilde dağıtılır, bu nedenle herhangi bir değişiklik göremezsiniz.

TLS 1,0 ve TLS 1,1 ' nin kullanımdan kaldırılması 31 Ağustos 2019 tarihinde yapılır, ancak bu değişikliğe hazırlanmak için zaman duyarsınız. Bu değişikliğe hazırlanmak için, kullanıcılarınızın uygulama proxy 'Si aracılığıyla yayınlanan uygulamalara erişmek için kullanacağı istemciler dahil olmak üzere, istemci-sunucu ve tarayıcı-sunucu birleşimlerinizin, uygulamayla bağlantıyı sürdürmek için TLS 1,2 protokolünü kullanacak şekilde güncelleştirildiğinden emin olun. Ara sunucu hizmeti. Daha fazla bilgi için bkz. [Azure Active Directory Içindeki uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Uygulamayla ilgili oturum açma verilerinizi görüntülemek için kullanım ve Öngörüler raporunu kullanın

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** & Raporlamayı izleme

Artık aşağıdakiler hakkında bilgiler de dahil olmak üzere oturum açma verilerinizin uygulama merkezli bir görünümünü almak için Azure portal **Kurumsal uygulamalar** alanında bulunan kullanım ve Öngörüler raporunu kullanabilirsiniz:

- Kuruluşunuz için kullanılan en popüler uygulamalar

- En fazla başarısız oturum açma işlemleri içeren uygulamalar

- Her uygulama için ilk oturum açma hataları

Bu özellik hakkında daha fazla bilgi için [Azure Active Directory portalındaki Kullanım ve Öngörüler raporuna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) bakın

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Azure AD kullanarak bulut uygulamalarına Kullanıcı hazırlığını otomatikleştirin

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** & Raporlamayı izleme

Aşağıdaki bulut tabanlı uygulamalar için Kullanıcı hesaplarının oluşturulmasını, silinmesini ve güncelleştirilmesini otomatik hale getirmek için Azure AD sağlama hizmetini kullanmak üzere bu yeni öğreticilerini izleyin:

- [Birlikte karşılayın](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [Permi güvenliği](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Ayrıca, Grup nesneleri sağlama hakkında bilgi sağlayan bu yeni [Dropbox öğreticisini](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)de izleyebilirsiniz.

Otomatik Kullanıcı hesabı sağlama aracılığıyla kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Kimlik güvenli puanı artık Azure AD 'de kullanılabilir (genel kullanılabilirlik)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** yok  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Artık Azure AD 'de kimlik güvenli puanı özelliğini kullanarak kimlik güvenliğini izleyip geliştirebilirsiniz. Kimlik güvenli puanı özelliği, size yardımcı olması için tek bir pano kullanır:

- NesneKimliği, 1 ile 223 arasında bir puan temelinde kimlik güvenliği önlemidir.

- Kimlik güvenlik geliştirmelerinizi planlayın

- Güvenlik geliştirmelerinizin başarısını inceleyin

Kimlik güvenliği puanı özelliği hakkında daha fazla bilgi için bkz. [Azure Active Directory kimlik güvenli puanı nedir?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Yeni Uygulama kayıtları deneyimi artık kullanıma sunuldu (genel kullanılabilirlik)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Geliştirici deneyimi

Yeni [uygulama kayıtları](https://aka.ms/appregistrations) deneyimi artık genel kullanıma sunulmuştur. Bu yeni deneyim, Azure portal ve uygulama kayıt Portalı ' ndan bildiğiniz tüm önemli özellikleri içerir ve bunları aracılığıyla geliştirir:

- **Daha iyi uygulama yönetimi.** Uygulamalarınızı farklı portallarda görmek yerine, artık tüm uygulamalarınızı tek bir konumda görebilirsiniz.

- **Basitleştirilmiş uygulama kaydı.** Geliştirilmiş gezinti deneyiminden, yeniden kullanılabilir izin seçimi deneyimine yönelik olarak, uygulamalarınızı kaydetmek ve yönetmek artık daha kolay.

- **Daha ayrıntılı bilgi.** Hızlı başlangıç kılavuzlarınız ve daha fazlası dahil olmak üzere uygulamanız hakkında daha fazla ayrıntı bulabilirsiniz.

Daha fazla bilgi için bkz. [Microsoft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/) ve [uygulama kayıtları deneyimi artık genel kullanıma sunuldu!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) Blog duyurusu.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Kimlik koruması için riskli kullanıcılar API 'sinde sunulan yeni yetenekler

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Kullanıcıların risk geçmişini almak, riskli kullanıcıları kapatmak ve kullanıcıları tehlikeye atılmak üzere doğrulamak için riskli kullanıcılar API 'sini artık kullanacağınızı duyurmaktan memnuniyet duyuyoruz. Bu değişiklik, kullanıcılarınızın risk durumunu daha verimli bir şekilde güncelleştirmenize ve risk geçmişini anlamanıza yardımcı olur.

Daha fazla bilgi için [riskli KULLANıCıLAR API başvuru belgelerine](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)bakın.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Mayıs 2019

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

Mayıs 2019 ' de, uygulama galerisine federasyon desteğiyle bu 21 yeni uygulama ekledik:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [gerçek bağlantılar](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kida](https://app.kianda.com/sso/OpenID/AzureAD/), [basit imza](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [BRAZE](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [templamy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales](https://toutapp.com/login)katılım, [aclp](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [META4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [hisse Çalışma alanı](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [WebMethods API bulutu](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [redflag](https://pocketstop.com/redflag/), [whatdüzeltmesini](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [denetimi](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [jobhub](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [neogov](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [pdee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [myvr](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](https://aka.ms/azureadapprequest)listeleme bölümüne bakın.

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Azure AD portalında geliştirilmiş Grup oluşturma ve yönetim deneyimleri

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün yeteneği:** İş

Azure AD portalındaki gruplarla ilgili deneyimler için geliştirmeler yaptık. Bu geliştirmeler, yöneticilerin grup listelerini, üye listelerini daha iyi yönetmesine ve ek oluşturma seçenekleri sağlamasına imkan tanır.

Geliştirmeler şunları içerir:

- Üyelik türüne ve grup türüne göre temel filtreleme.

- Kaynak ve e-posta adresi gibi yeni sütunların eklenmesi.

- Kolay bir şekilde silinmek üzere grupları, üyeleri ve sahip listelerini çoklu seçme özelliği.

- Grup oluşturma sırasında e-posta adresi seçme ve sahipler ekleme özelliği.

Daha fazla bilgi için bkz. [temel Grup oluşturma ve Azure Active Directory kullanarak üye ekleme](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Azure AD portalındaki Office 365 grupları için adlandırma ilkesi yapılandırma (genel kullanılabilirlik)

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün yeteneği:** İş

Yöneticiler artık Azure AD portalını kullanarak Office 365 grupları için bir adlandırma ilkesi yapılandırabilir. Bu değişiklik, kuruluşunuzdaki kullanıcılar tarafından oluşturulan veya düzenlenen Office 365 grupları için tutarlı adlandırma kuralları uygulanmasını sağlar.

Office 365 grupları için adlandırma ilkesini iki farklı yolla yapılandırabilirsiniz:

- Otomatik olarak bir grup adına eklenen ön ekleri veya sonekleri tanımlayın.

- Kuruluşunuz için Grup adlarında izin verilmeyen özelleştirilmiş bir engellenen sözcük kümesini karşıya yükleyin (örneğin, "CEO, bordro, HR").

Daha fazla bilgi için bkz. [Office 365 grupları Için adlandırma Ilkesi zorlama](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API uç noktaları artık Azure AD etkinlik günlükleri (genel kullanılabilirlik) için kullanılabilir

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & Raporlamayı izleme

Azure AD etkinlik günlükleri için Microsoft Graph API uç noktası desteğinin genel kullanıma sunulduğunu duyurmaktan mutluluk duyuyoruz. Bu sürümle birlikte artık hem Azure AD denetim günlüklerinin hem de oturum açma günlüğü API 'Lerinin 1,0 sürümünü kullanabilirsiniz.

Daha fazla bilgi için bkz. [Azure AD denetim günlüğü API 'sine genel bakış](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Yöneticiler artık Birleşik kayıt işlemi (Genel Önizleme) için koşullu erişimi kullanabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Koşullu erişim  
**Ürün yeteneği:** Kimlik güvenliği & koruması  

Yöneticiler artık Birleşik kayıt sayfası tarafından kullanılmak üzere koşullu erişim ilkeleri oluşturabilir. Bu, şu durumlarda kayda izin vermek için ilkeleri uygulamayı içerir:

- Kullanıcılar güvenilir bir ağ üzerinde.

- Kullanıcılar, düşük bir oturum açma riskidir.

- Kullanıcılar yönetilen bir cihazlardır.

- Kullanıcılar kuruluşun kullanım koşulları 'nı (TOU) kabul etmiş olursunuz.

Koşullu erişim ve parola sıfırlama hakkında daha fazla bilgi için [Azure AD BIRLEŞIK MFA ve parola sıfırlama kayıt deneyimi blog gönderisine yönelik koşullu erişimi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)görebilirsiniz. Birleşik kayıt işlemi için koşullu erişim ilkeleri hakkında daha fazla bilgi için bkz. [Birleşik kayıt Için koşullu erişim ilkeleri](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Azure AD kullanım koşulları özelliği hakkında daha fazla bilgi için bkz. [kullanım koşulları özelliği Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
