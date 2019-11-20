---
title: Azure Multi-Factor Authentication Yapılandırma-Azure Active Directory
description: Bu makalede, Azure portal Azure Multi-Factor Authentication ayarlarının nasıl yapılandırılacağı açıklanmaktadır
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26744791dd3abe732f56f69ca05dd7c781755e9b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167982"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Azure Multi-Factor Authentication ayarlarını yapılandırma

Bu makale, Azure portal Multi-Factor Authentication ayarlarını yönetmenize yardımcı olur. Azure Multi-Factor Authentication en iyi şekilde yararlanmanıza yardımcı olacak çeşitli konular ele alınmaktadır. Her Azure Multi-Factor Authentication sürümünde tüm özellikler kullanılamaz.

Azure Multi-Factor Authentication ile ilgili ayarlara **Azure Active Directory** > **güvenlik** > **MFA**' ya giderek Azure Portal erişebilirsiniz.

![Azure portal-Azure AD Multi-Factor Authentication ayarları](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Ayarlar

Bu ayarlardan bazıları MFA sunucusu, Azure MFA veya her ikisi için de geçerlidir.

| Özellik | Açıklama |
| ------- | ----------- |
| Hesap kilitleme | Bir satırda çok fazla Reddedilen kimlik doğrulama denemesi varsa, Multi-Factor Authentication hizmetindeki hesapları geçici olarak kilitleyin. Bu özellik yalnızca kimlik doğrulaması için PIN girişi yapan kullanıcılar için geçerlidir. (MFA sunucusu) |
| [Kullanıcıları engelle/engelini kaldır](#block-and-unblock-users) | Belirli kullanıcıların Multi-Factor Authentication isteklerini almasına engel olmak için kullanılır. Engellenen kullanıcılar için tüm kimlik doğrulaması denemeleri otomatik olarak reddedilir. Kullanıcılar engellendikleri tarihten 90 gün sonrasına kadar engellenmiş olarak kalır. |
| [Sahtekarlık uyarısı](#fraud-alert) | Kullanıcılara sahte doğrulama isteklerini bildirme yeteneği ile ilgili ayarları yapılandırın |
| [Bildirimler](#notifications) | MFA sunucusundan olayların bildirimlerini etkinleştirin. |
| [OATH belirteçleri](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Kullanıcılar için OATH belirteçlerini yönetmek üzere bulut tabanlı Azure MFA ortamlarında kullanılır. |
| [Telefon araması ayarları](#phone-call-settings) | Bulut ve şirket içi ortamlar için telefon çağrılarıyla ve tebrikler ilgili ayarları yapılandırın. |
| Sağlayıcılar | Bu, hesabınızla ilişkilendirdiğiniz mevcut herhangi bir kimlik doğrulama sağlayıcısını gösterir. Yeni kimlik doğrulama sağlayıcıları 1 Eylül 2018 itibariyle oluşturulmayabilir |

## <a name="manage-mfa-server"></a>MFA sunucusunu yönet

Bu bölümdeki ayarlar yalnızca MFA sunucusu içindir.

| Özellik | Açıklama |
| ------- | ----------- |
| Sunucu ayarları | Ortamınızı başlatmak için MFA sunucusunu indirin ve etkinleştirme kimlik bilgilerini oluşturun |
| [Bir kerelik geçiş](#one-time-bypass) | Kullanıcının sınırlı bir süre için iki aşamalı doğrulama gerçekleştirmeden kimlik doğrulamasına izin verin. |
| [Önbelleğe alma kuralları](#caching-rules) |  Önbelleğe alma, birincil olarak ilk istek devam ederken VPN gibi şirket içi sistemler, birden çok doğrulama isteği gönderirken kullanılır. Bu özellik, sonraki isteklerin otomatik olarak başarılı olmasını sağlar ve Kullanıcı devam eden ilk doğrulamayı başarılı olduktan sonra. |
| Sunucu durumu | Sürüm, durum, IP ve son iletişim saat ve tarih dahil olmak üzere şirket içi MFA sunucularınızın durumuna bakın. |

## <a name="activity-report"></a>Etkinlik raporu

Burada kullanılabilir olan raporlama MFA sunucusuna (Şirket içi) özgüdür. Azure MFA (bulut) raporlarında, Azure AD 'de oturum açma işlemleri raporuna bakın.

## <a name="block-and-unblock-users"></a>Kullanıcıları engelle ve engellemeyi kaldır

Kullanıcıların kimlik doğrulama isteklerini almasını engellemek için _kullanıcıları engelle ve engellemeyi kaldır_ özelliğini kullanın. Engellenen kullanıcılar için tüm kimlik doğrulaması denemeleri otomatik olarak reddedilir. Kullanıcılar engellendikleri tarihten 90 gün sonrasına kadar engellenmiş olarak kalır.

### <a name="block-a-user"></a>Kullanıcıyı engelle

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
2. **Azure Active Directory** > **güvenlik** > **MFA** > , **kullanıcıları engelle/Engeli kaldır**' a gidin.
3. Kullanıcıyı engellemek için **Ekle** ' yi seçin.
4. **Çoğaltma grubunu**seçin. Engellenen kullanıcının Kullanıcı adını Kullanıcı **adı\@Domain.com**olarak girin. **Neden** alanına bir açıklama girin.
5. Kullanıcıyı engellemeyi durdurmak için **Ekle** ' yi seçin.

### <a name="unblock-a-user"></a>Kullanıcının engellemesini kaldırma

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
2. **Azure Active Directory** > **güvenlik** > **MFA** > , **kullanıcıları engelle/Engeli kaldır**' a gidin.
3. Engellemeyi kaldırmak için kullanıcının yanındaki **eylem** sütununda **Engellemeyi kaldır** ' ı seçin.
4. **Engellemeyi kaldırma nedeni** alanına bir açıklama girin.
5. Kullanıcının engellemesini kaldırma işleminin bitmesini **kaldırmak Için engellemeyi kaldır** seçeneğini belirleyin.

## <a name="fraud-alert"></a>Sahtekarlık uyarısı

Kullanıcılarınızın kaynaklarına erişmek için sahte denemeleri bildirebilmesi için _sahtekarlık uyarı_ özelliğini yapılandırın. Kullanıcılar, mobil uygulamayı veya telefonlarını kullanarak sahtekarlık girişimlerini rapor edebilir.

### <a name="turn-on-fraud-alerts"></a>Sahtekarlık uyarılarını aç

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
2. **Azure Active Directory** > **güvenlik** > **MFA** > **sahtekarlık uyarısı**' na gidin.
3. **Kullanıcıların sahtekarlık uyarıları göndermesine Izin ver** ayarını **Açık**olarak ayarlayın.
4. **Kaydet**’i seçin.

### <a name="configuration-options"></a>Yapılandırma seçenekleri

* **Sahtekarlık bildirildiğinde kullanıcıyı engelle**: bir Kullanıcı sahtekarlık bildirirse, hesabı 90 gün boyunca engellenir veya bir yönetici kendi hesabını engellemez. Yönetici, oturum açma raporunu kullanarak oturum açma işlemlerini gözden geçirebilir ve ileride sahtekarlık oluşmasını önlemek için gerekli işlemleri gerçekleştirebilir. Yönetici daha sonra kullanıcının hesabının [engellemesini kaldırabilir](#unblock-a-user) .
* **İlk tebrik sırasında sahtekarlık raporlamak Için kod**: kullanıcılar iki adımlı doğrulama gerçekleştirmek için bir telefon araması aldıklarında, oturum açma işlemini onaylamak için normalde **#** ' a basın. Sahtekarlık raporlamak için Kullanıcı **#** basmadan önce bir kod girer. Bu kod varsayılan olarak **0** ' dır, ancak özelleştirebilirsiniz.

   >[!NOTE]
   >Microsoft 'un varsayılan ses tebrikleri, kullanıcılardan bir sahtekarlık uyarısı göndermesi için **0 #** tuşlarına basmalarını ister. **0**dışında bir kod kullanmak istiyorsanız, kullanıcılarınız için uygun yönergeler ile kendi özel ses tebrikleri kaydedin ve yükleyin.
   >

### <a name="view-fraud-reports"></a>Sahtekarlık raporlarını görüntüleme

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Azure Active Directory** > **oturum açma**işlemlerini seçin. Sahtekarlık raporu artık standart Azure AD oturum açma işlemleri raporunun bir parçasıdır.

## <a name="notifications"></a>Bildirimler

Sahtekarlık uyarı e-postaları alacak kullanıcılar için buradaki e-posta adreslerini yapılandırın.

![Bildirim sahtekarlık uyarısı e-posta örneği](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Telefon araması ayarları

### <a name="caller-id"></a>Arayan KIMLIĞI

**MFA arayan kimlik numarası** -kullanıcılarınızın telefonlarındaki göreceği sayıdır. Yalnızca ABD tabanlı sayılara izin verilir.

>[!NOTE]
>Multi-Factor Authentication çağrıları genel telefon ağı üzerinden yerleştirildiğinde, bazen arayan KIMLIĞINI desteklemeyen bir taşıyıcı aracılığıyla yönlendirilir. Bu nedenle, Multi-Factor Authentication sistemi her zaman tarafından gönderilse de arayan KIMLIĞI garanti edilmez.

Birleşik Devletler, MFA arayanın KIMLIĞINI yapılandırmadıysanız, Microsoft 'tan sesli aramalar şu numaralardan gelir: + 1 (866) 539 4191, + 1 (855) 330 8653 ve + 1 (877) 668 6536. İstenmeyen posta filtreleri kullanıyorsanız, bu sayıları dışlediğinizden emin olun.

### <a name="custom-voice-messages"></a>Özel ses iletileri

_Özel sesli iletiler_ özelliğiyle, iki adımlı doğrulama için kendi kayıtlarınızı veya selamlarınızı kullanabilirsiniz. Bu iletiler, Microsoft kayıtlarının yanı sıra veya değiştirmek için de kullanılabilir.

Başlamadan önce aşağıdaki kısıtlamalara dikkat edin:

* Desteklenen dosya biçimleri. wav ve. mp3.
* Dosya boyutu sınırı 1 MB 'tır.
* Kimlik doğrulama iletileri 20 saniyeden kısa olmalıdır. 20 saniyeden uzun olan iletiler doğrulamanın başarısız olmasına neden olabilir. İleti tamamlanmadan önce Kullanıcı yanıt vermemeyebilir ve doğrulama zaman aşımına uğrar.

### <a name="custom-message-language-behavior"></a>Özel ileti dili davranışı

Kullanıcıya özel bir sesli mesaj yürütüldüğünde, iletinin dili şu etkenlere bağlıdır:

* Geçerli kullanıcının dili.
  * Kullanıcının tarayıcısı tarafından algılanan dil.
  * Diğer kimlik doğrulama senaryoları farklı davranabilirler.
* Kullanılabilir özel iletilerin dili.
  * Bu dil, özel bir ileti eklendiğinde yönetici tarafından seçilir.

Örneğin, yalnızca bir özel ileti varsa, Almanca dili vardır:

* Almanca dilinde kimlik doğrulaması yapan bir Kullanıcı, özel Alman iletisini duyacaktır.
* Ingilizce 'de kimlik doğrulaması yapan bir Kullanıcı standart Ingilizce iletisini duyacaktır.

### <a name="set-up-a-custom-message"></a>Özel bir ileti ayarlama

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
1. **Azure Active Directory** > **güvenlik** > **MFA** > **telefon araması ayarları**' na gidin.
1. **Karşılama Ekle**' yi seçin.
1. Selamlama türünü seçin.
1. Dili seçin.
1. Karşıya yüklenecek bir. mp3 veya. wav ses dosyası seçin.
1. **Add (Ekle)** seçeneğini belirleyin.

### <a name="custom-voice-message-defaults"></a>Özel sesli ileti Varsayılanları

Özel iletiler oluşturmak için örnek betikler.

| İleti adı | Betik |
| --- | --- |
| Kimlik doğrulama başarılı | Oturum açma bilgileriniz başarıyla doğrulandı. Goodbye. |
| Uzantı istemi | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Devam etmek için lütfen kare tuşuna basın. |
| Sahtekarlık onayı | Bir sahtekarlık uyarısı gönderildi. Hesabınızın engelini kaldırmak için lütfen şirketinizin BT yardım masasına başvurun. |
| Sahtekarlık karşılama (Standart) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen kare tuşuna basın. Bu doğrulamayı başlatmadıysanız birisi hesabınıza erişmeye çalışıyor olabilir. Sahtekarlık uyarısı göndermek için lütfen 0 kare tuşuna basın. Bu, şirketinizin BT ekibine bildirir ve daha fazla doğrulama denemesini engeller. |
| Sahtekarlık bir sahtekarlık uyarısı gönderildiğini bildirdi. | Hesabınızın engelini kaldırmak için lütfen şirketinizin BT yardım masasına başvurun. |
| Etkinleştirme | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen kare tuşuna basın. |
| Kimlik doğrulaması yeniden denensin | Doğrulama reddedildi. |
| Yeniden dene (Standart) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen kare tuşuna basın. |
| Karşılama (Standart) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen kare tuşuna basın. |
| Karşılama (PIN) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen PIN 'inizi girip kare tuşuna basın. |
| Sahtekarlık karşılama (PIN) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler.  Doğrulamanızı tamamlaması için lütfen PIN 'inizi girip kare tuşuna basın. Bu doğrulamayı başlatmadıysanız birisi hesabınıza erişmeye çalışıyor olabilir. Sahtekarlık uyarısı göndermek için lütfen 0 kare tuşuna basın. Bu, şirketinizin BT ekibine bildirir ve daha fazla doğrulama denemesini engeller. |
| Yeniden dene (PIN) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen PIN 'inizi girip kare tuşuna basın. |
| Rakamlardan sonra uzantı istemi | Zaten bu uzantıda varsa, devam etmek için kare tuşuna basın. |
| Kimlik doğrulaması reddedildi | Ne yazık ki şu anda oturumunuzu açamıyorum. Lütfen daha sonra tekrar deneyin. |
| Etkinleştirme karşılama (Standart) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen kare tuşuna basın. |
| Etkinleştirme yeniden deneme (Standart) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen kare tuşuna basın. |
| Etkinleştirme karşılama (PIN) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen PIN 'inizi girip kare tuşuna basın. |
| Rakamlardan önce uzantı istemi | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Lütfen bu çağrıyı uzantıya aktarın... |

## <a name="one-time-bypass"></a>Bir kerelik geçiş

_Tek seferlik atlama_ özelliği, bir kullanıcının iki adımlı doğrulama gerçekleştirmeksizin tek bir kez kimlik doğrulaması yapmasına olanak sağlar. Atlama geçicidir ve belirtilen sayıda saniye sonra süresi dolar. Mobil uygulamanın veya telefonun bir bildirim veya telefon araması almaışında, kullanıcının istenen kaynağa erişebilmesi için bir kerelik geçişe izin verebilirsiniz.

### <a name="create-a-one-time-bypass"></a>Tek seferlik atlama oluşturma

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
2. **Azure Active Directory** > **güvenlik** > **MFA** > **tek seferlik atlama**' ye gidin.
3. **Add (Ekle)** seçeneğini belirleyin.
4. Gerekirse, atlama için çoğaltma grubunu seçin.
5. Kullanıcı adını **Kullanıcı adı\@Domain.com**olarak girin. Atlatma 'nin en son kaç saniye sayısını girin. Atlama nedenini girin.
6. **Add (Ekle)** seçeneğini belirleyin. Zaman sınırı hemen yürürlüğe girer. Kullanıcının bir kerelik atlama süresi dolmadan önce oturum açması gerekir.

### <a name="view-the-one-time-bypass-report"></a>Tek seferlik atlama raporunu görüntüleme

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Azure Active Directory** > **güvenlik** > **MFA** > **tek seferlik atlama**' ye gidin.

## <a name="caching-rules"></a>Önbelleğe alma kuralları

_Önbelleğe alma_ özelliğini kullanarak bir kullanıcının kimliği doğrulandıktan sonra kimlik doğrulama girişimlerine izin vermek için bir zaman aralığı ayarlayabilirsiniz. Belirli bir süre içinde Kullanıcı için sonraki kimlik doğrulama girişimleri otomatik olarak başarılı olur. Önbelleğe alma, birincil olarak ilk istek devam ederken VPN gibi şirket içi sistemler, birden çok doğrulama isteği gönderirken kullanılır. Bu özellik, sonraki isteklerin otomatik olarak başarılı olmasını sağlar ve Kullanıcı devam eden ilk doğrulamayı başarılı olduktan sonra.

>[!NOTE]
>Önbelleğe alma özelliği, Azure Active Directory (Azure AD) için oturum açma işlemleri için kullanılmak üzere tasarlanmamıştır.

### <a name="set-up-caching"></a>Önbelleğe almayı ayarla

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
2. **Azure Active Directory** > **güvenlik** > **MFA** > **önbelleğe alma kurallarını**inceleyin.
3. **Add (Ekle)** seçeneğini belirleyin.
4. Açılan listeden **önbellek türünü** seçin. En fazla **önbellek saniyesi**sayısını girin.
5. Gerekirse, bir kimlik doğrulama türü seçin ve bir uygulama belirtin.
6. **Add (Ekle)** seçeneğini belirleyin.

## <a name="mfa-service-settings"></a>MFA hizmet ayarları

Uygulama parolaları, güvenilir IP 'Ler, doğrulama seçenekleri ve Azure Multi-Factor Authentication için çok faktörlü kimlik doğrulamasını hatırlama ayarları, hizmet ayarları ' nda bulunabilir. Hizmet ayarlarına **Azure Active Directory** > **güvenlik** > **MFA** 'ya göz atarak Azure Portal erişilebilir ve **bulut tabanlı diğer MFA ayarlarını** > **yapılandırma** ** >  > ** .

![Azure Multi-Factor Authentication hizmet ayarları](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

Güvenilen IP adresi aralıkları özel veya genel olabilir.

## <a name="app-passwords"></a>Uygulama parolaları

Office 2010 veya önceki bir sürümü ve iOS 11 ' den önceki Apple Mail gibi bazı uygulamalar, iki aşamalı doğrulamayı desteklemezler. Uygulamalar, ikinci bir doğrulamayı kabul edecek şekilde yapılandırılmamış. Bu uygulamaları kullanmak için _Uygulama parolaları_ özelliğinden yararlanın. Uygulamanın iki aşamalı doğrulamayı atlamasına ve çalışmaya devam etmesine izin vermek için, geleneksel parolanızın yerine bir uygulama parolası kullanabilirsiniz.

Modern kimlik doğrulaması Microsoft Office 2013 istemcileri ve sonraki sürümler için desteklenir. Outlook dahil Office 2013 istemcileri, modern kimlik doğrulama protokollerini destekler ve iki adımlı doğrulamayla çalışmak üzere etkinleştirilebilir. İstemci etkinleştirildikten sonra istemci için uygulama parolaları gerekli değildir.

>[!NOTE]
>Uygulama parolaları, koşullu erişim tabanlı Multi-Factor Authentication ilkeleriyle ve modern kimlik doğrulamasıyla çalışmaz.

### <a name="considerations-about-app-passwords"></a>Uygulama parolalarıyla ilgili önemli noktalar

Uygulama parolaları kullanırken aşağıdaki önemli noktaları göz önünde bulundurun:

* Uygulama parolaları, uygulama başına yalnızca bir kez girilir. Kullanıcıların parolaları izlemek veya her seferinde onları girmesi gerekmez.
* Asıl parola otomatik olarak oluşturulur ve Kullanıcı tarafından sağlanmaz. Otomatik olarak oluşturulan parolanın bir saldırgan tarafından tahmin edilmesi daha zordur ve daha güvenlidir.
* Kullanıcı başına 40 parola sınırı vardır.
* Uygulama parolası iş veya okul hesabı dışında olmadığından, parolaları önbelleğe alan ve bunları şirket içi senaryolarda kullanan uygulamalar başarısız olabilir. Bu senaryoya bir örnek, şirket içi Exchange e-postalarının bir örneğidir, ancak Arşivlenmiş posta bulutta yer alır. Bu senaryoda, aynı parola çalışmaz.
* Kullanıcı hesabında Multi-Factor Authentication etkinleştirildikten sonra, uygulama parolaları Outlook ve Microsoft Skype Kurumsal gibi tarayıcı olmayan birçok istemci ile birlikte kullanılabilir. Windows PowerShell gibi tarayıcı olmayan uygulamalar aracılığıyla Uygulama parolaları kullanılarak yönetim eylemleri gerçekleştirilemez. Kullanıcı bir yönetici hesabına sahip olsa bile eylemler gerçekleştirilemez. PowerShell betikleri çalıştırmak için güçlü parolalı bir hizmet hesabı oluşturun ve iki adımlı doğrulama için hesabı etkinleştirmeyin.

>[!WARNING]
>Uygulama parolaları, istemcilerin hem şirket içi hem de bulut otomatik bulma uç noktaları ile iletişim kurduğu karma ortamlarda çalışmaz. Şirket içinde kimlik doğrulaması yapmak için etki alanı parolaları gereklidir. Bulutta kimlik doğrulaması yapmak için uygulama parolaları gereklidir.

### <a name="guidance-for-app-password-names"></a>Uygulama parolası adları için rehberlik

Uygulama parolası adları, kullanıldıkları aygıtı yansıtmalıdır. Outlook, Word ve Excel gibi tarayıcı olmayan uygulamalar içeren bir dizüstü bilgisayarınız varsa, bu uygulamalar için **dizüstü bilgisayar** adlı bir uygulama parolası oluşturun. Masaüstü bilgisayarınızda çalışan uygulamalar için **Masaüstü** adlı başka bir uygulama parolası oluşturun.

>[!NOTE]
>Her uygulama için bir uygulama parolası yerine her cihaz için bir uygulama parolası oluşturmanızı öneririz.

### <a name="federated-or-single-sign-on-app-passwords"></a>Federasyon veya çoklu oturum açma uygulama parolaları

Azure AD, şirket içi Windows Server Active Directory Domain Services (AD DS) ile Federasyon veya çoklu oturum açmayı (SSO) destekler. Kuruluşunuz Azure AD ile federe ise ve Azure Multi-Factor Authentication kullanıyorsanız, uygulama parolaları hakkında aşağıdaki noktaları dikkate alın.

>[!NOTE]
>Aşağıdaki noktaları yalnızca Federal (SSO) müşteriler için geçerlidir.

* Uygulama parolaları Azure AD tarafından doğrulanır ve bu nedenle, Federasyonu atlayın. Federasyon yalnızca uygulama parolaları ayarlanırken etkin bir şekilde kullanılır.
* Pasif akışın aksine, kimlik sağlayıcısı (IDP), Federasyon (SSO) kullanıcıları için bağlantı kurulmadı. Uygulama parolaları iş veya okul hesabında depolanır. Bir kullanıcı şirketten ayrılırsa, kullanıcının bilgileri, **DirSync** 'i gerçek zamanlı olarak kullanarak iş veya okul hesabına akar. Hesabı devre dışı bırakma/silme işlemi üç saate kadar sürebilir ve bu, Azure AD 'de uygulama parolasının devre dışı/silinmesini erteleyebilir.
* Şirket içi istemci Access Control ayarları uygulama parolaları özelliği tarafından kabul edilmez.
* Uygulama parolaları özelliği ile kullanabileceğiniz şirket içi kimlik doğrulaması günlüğü/Denetim yeteneği kullanılamaz.
* Bazı gelişmiş mimarilerin, istemcilerle iki adımlı doğrulama için kimlik bilgilerinin birleşimini gerektirir. Bu kimlik bilgileri, iş veya okul hesabı Kullanıcı adı ve parolaları ile uygulama parolalarını içerebilir. Gereksinimler, kimlik doğrulamanın nasıl gerçekleştirilebileceğini temel alır. Şirket içi altyapıya karşı kimlik doğrulaması yapan istemciler için bir iş veya okul hesabı Kullanıcı adı ve parola gerekir. Azure AD kimlik doğrulaması yapan istemciler için bir uygulama parolası gerekir.

  Örneğin, aşağıdaki mimarinin olduğunu varsayalım:

  * Şirket içi Active Directory örneğiniz Azure AD ile federe olarak sağlanır.
  * Exchange Online kullanıyorsunuz.
  * Skype Kurumsal Şirket içi kullanıyorsunuz.
  * Azure Multi-Factor Authentication kullanıyorsunuz.

  Bu senaryoda, aşağıdaki kimlik bilgilerini kullanırsınız:

  * Skype Kurumsal 'da oturum açmak için iş veya okul hesabınızın Kullanıcı adı ve parolasını kullanın.
  * Exchange Online 'a bağlanan bir Outlook istemcisinden adres defterine erişmek için bir uygulama parolası kullanın.

### <a name="allow-users-to-create-app-passwords"></a>Kullanıcıların uygulama parolaları oluşturmalarına izin ver

Varsayılan olarak, kullanıcılar uygulama parolaları oluşturamaz. Uygulama parolaları özelliğinin etkinleştirilmesi gerekir. Kullanıcılara uygulama parolaları oluşturma yeteneği vermek için aşağıdaki yordamı kullanın:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol tarafta **Azure Active Directory** > **kullanıcıları**' nı seçin.
3. **Multi-Factor Authentication**seçin.
4. Multi-Factor Authentication altında **hizmet ayarları**' nı seçin.
5. **Hizmet ayarları** sayfasında, **kullanıcıların tarayıcı olmayan uygulamalara oturum açmak için uygulama parolaları oluşturmasına izin ver** seçeneğini belirleyin.

### <a name="create-app-passwords"></a>Uygulama parolaları oluşturma

Kullanıcılar, ilk kaydı sırasında uygulama parolaları oluşturabilir. Kullanıcının kayıt işleminin sonunda uygulama parolaları oluşturma seçeneği vardır.

Kullanıcılar, kayıt sonrasında Uygulama parolaları da oluşturabilir. Kullanıcılarınıza yönelik daha fazla bilgi ve ayrıntılı adımlar için bkz. [Azure Multi-Factor Authentication uygulama parolaları nelerdir?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Güvenilen IP'ler

Azure Multi-Factor Authentication 'nin _güvenilir IP 'leri_ özelliği, yönetilen veya federal bir kiracının yöneticileri tarafından kullanılır. Özelliği, şirket intranetinden oturum açan kullanıcılar için iki aşamalı doğrulamayı atlar. Özelliği, yöneticilerin ücretsiz sürümü değil, Azure Multi-Factor Authentication 'nin tam sürümüyle kullanılabilir. Azure Multi-Factor Authentication 'nin tam sürümünü alma hakkında daha fazla bilgi için bkz. [azure Multi-Factor Authentication](multi-factor-authentication.md).

> [!NOTE]
> MFA güvenilir IP 'Leri ve koşullu erişim adlandırılmış konumlar yalnızca ıPV4 adresleriyle çalışır.

Kuruluşunuz, şirket içi uygulamalara MFA sağlamak üzere NPS uzantısını dağıttığında, kaynak IP adresi her zaman kimlik doğrulama girişiminin akışa aldığı NPS sunucusu olacak şekilde görünür.

| Azure AD kiracı türü | Güvenilen IP 'Ler özellik seçenekleri |
|:--- |:--- |
| Yönetilen |**Belırlı IP adresi aralığı**: Yöneticiler, şirket intranetinden oturum açan kullanıcılar için iki aşamalı doğrulamayı atlayabilen bir IP adresi aralığı belirtir. En fazla 50 güvenilen IP aralığı yapılandırılabilir.|
| Federasyon |**Tüm Federasyon kullanıcıları**: kuruluşun içinden oturum açan tüm Federasyon kullanıcıları, iki aşamalı doğrulamayı atlayabilir. Kullanıcılar, Active Directory Federasyon Hizmetleri (AD FS) (AD FS) tarafından verilen bir talep kullanarak doğrulamayı atlar.<br/>**Belırlı IP adresi aralığı**: Yöneticiler, şirket intranetinden oturum açan kullanıcılar için iki aşamalı doğrulamayı atlayabilen bir IP adresi aralığı belirtir. |

Güvenilen IP 'Leri atlama yalnızca şirket intraneti 'nin içinden çalışacak. **Tüm Federasyon kullanıcıları** seçeneğini belirlerseniz ve Kullanıcı şirket intranet dışından oturum açarsa, kullanıcının kimlik doğrulaması iki aşamalı doğrulamayı kullanarak yapılır. Kullanıcı bir AD FS talebi sunsa bile, işlem aynıdır. 

### <a name="end-user-experience-inside-of-corpnet"></a>Corpnet içinde son kullanıcı deneyimi

Güvenilir IP 'Ler özelliği devre dışı bırakıldığında, tarayıcı akışları için iki aşamalı doğrulama gerekir. Daha eski zengin istemci uygulamaları için uygulama parolaları gereklidir.

Güvenilen IP 'Ler özelliği etkinleştirildiğinde, tarayıcı akışları için iki aşamalı doğrulama gerekli *değildir* . Kullanıcı bir uygulama parolası *oluşturmadığı* için, daha eski zengin istemci uygulamaları için uygulama parolaları gerekli değildir. Bir uygulama parolası kullanımda olduktan sonra parola gerekli kalır. 

### <a name="end-user-experience-outside-corpnet"></a>Corpnet dışında son kullanıcı deneyimi

Güvenilen IP 'Lerin özelliğinin etkinleştirilip etkinleştirilmediği bağımsız olarak, tarayıcı akışları için iki aşamalı doğrulama gerekir. Daha eski zengin istemci uygulamaları için uygulama parolaları gereklidir.

### <a name="enable-named-locations-by-using-conditional-access"></a>Koşullu erişimi kullanarak adlandırılmış konumları etkinleştir

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol tarafta, **adlandırılmış konumlar** > **Azure Active Directory** > **güvenlik** > **koşullu erişim** ' i seçin.
3. **Yeni konum**' u seçin.
4. Konum için bir ad girin.
5. **Güvenilen konum olarak işaretle**' yi seçin.
6. IP aralığını **192.168.1.1/24**gibi CIDR gösteriminde girin.
7. **Oluştur**'u seçin.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Koşullu erişimi kullanarak güvenilir IP 'Ler özelliğini etkinleştirme

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol tarafta, **adlandırılmış konumlar** > **Azure Active Directory** > **güvenlik** >  **koşullu erişim** ' i seçin.
3. **MFA güvenilir IP 'Lerini Yapılandır**' ı seçin.
4. **Hizmet ayarları** sayfasında, **güvenilir IP 'ler**altında, aşağıdaki iki seçenekten birini seçin:

   * **İntranetten kaynaklanan Federal kullanıcılardan gelen Istekler için**: Bu seçeneği belirlemek için onay kutusunu seçin. Şirket ağından oturum açan tüm Federasyon kullanıcıları, AD FS tarafından verilen bir talebi kullanarak iki aşamalı doğrulamayı atlar. AD FS, intranet talebini uygun trafiğe eklemek için bir kural içerdiğinden emin olun. Kural yoksa AD FS ' de aşağıdaki kuralı oluşturun:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Belirli bir genel IP aralığından gelen Istekler için**: Bu seçeneği belirlemek IÇIN, CIDR gösterimini kullanarak metın kutusuna IP adreslerini girin.
      * Xxx. xxx. xxx. 1 ile xxx. xxx. xxx. 254 aralığındaki IP adresleri için, **xxx. xxx. xxx. 0/24**gibi bir gösterim kullanın.
      * Tek bir IP adresi için **xxx.xxx.xxx.xxx/32**gibi bir gösterim kullanın.
      * En fazla 50 IP adresi aralığı girin. Bu IP adreslerinden oturum açan kullanıcılar iki aşamalı doğrulamayı atlar.

5. **Kaydet**’i seçin.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Hizmet ayarlarını kullanarak güvenilir IP 'Leri özelliğini etkinleştirme

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol tarafta **Azure Active Directory** > **kullanıcıları**' nı seçin.
3. **Multi-Factor Authentication**seçin.
4. Multi-Factor Authentication altında **hizmet ayarları**' nı seçin.
5. **Hizmet ayarları** sayfasında, **güvenilir IP 'ler**altında, aşağıdaki iki seçenekten birini (veya her ikisini) seçin:

   * **İntranetimde Federal kullanıcılardan gelen Istekler için**: Bu seçeneği belirlemek için onay kutusunu işaretleyin. Şirket ağından oturum açan tüm Federasyon kullanıcıları, AD FS tarafından verilen bir talebi kullanarak iki aşamalı doğrulamayı atlar. AD FS, intranet talebini uygun trafiğe eklemek için bir kural içerdiğinden emin olun. Kural yoksa AD FS ' de aşağıdaki kuralı oluşturun:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Belirli BIR IP adresi alt ağı aralığından gelen Istekler için**: Bu seçeneği belirlemek IÇIN, CIDR gösterimini kullanarak metın kutusuna IP adreslerini girin.
      * Xxx. xxx. xxx. 1 ile xxx. xxx. xxx. 254 aralığındaki IP adresleri için, **xxx. xxx. xxx. 0/24**gibi bir gösterim kullanın.
      * Tek bir IP adresi için **xxx.xxx.xxx.xxx/32**gibi bir gösterim kullanın.
      * En fazla 50 IP adresi aralığı girin. Bu IP adreslerinden oturum açan kullanıcılar iki aşamalı doğrulamayı atlar.

6. **Kaydet**’i seçin.

## <a name="verification-methods"></a>Doğrulama yöntemleri

Kullanıcılarınız için kullanılabilir doğrulama yöntemlerini seçebilirsiniz. Aşağıdaki tabloda yöntemlere kısa bir genel bakış sunulmaktadır.

Kullanıcılarınız Azure Multi-Factor Authentication hesaplarını kaydettiğinde, etkinleştirdiğiniz seçeneklerden tercih edilen doğrulama yöntemini seçer. Kullanıcı kayıt işlemine kılavuzluk, [iki adımlı doğrulama için hesabımı ayarlama](../user-help/multi-factor-authentication-end-user-first-time.md)bölümünde verilmiştir.

| Yöntem | Açıklama |
|:--- |:--- |
| Telefon çağrısı |Otomatik bir sesli çağrı koyar. Kullanıcı, aramaya yanıt verir ve telefon tuş takımında # tuşuna basar. Telefon numarası şirket içi Active Directory ile eşitlenmez. |
| Telefona kısa mesaj |Doğrulama kodu içeren bir kısa mesaj gönderir. Kullanıcıdan, doğrulama kodunu oturum açma arabirimine girmesi istenir. Bu işlem tek yönlü SMS olarak adlandırılır. İki yönlü SMS, kullanıcının belirli bir kodu doğru bir şekilde geri metin döndürmelidir. İki yönlü SMS kullanım dışıdır ve 14 Kasım 2018 sonrasında desteklenmez. Yöneticiler, daha önce iki yönlü SMS kullanan kullanıcılar için başka bir yöntemi etkinleştirmelidir.|
| Mobil uygulama aracılığıyla bildirim |Telefonunuza veya kayıtlı cihazınıza anında iletme bildirimi gönderir. Kullanıcı bildirimi görüntüler ve doğrulamayı tamamlamaya yönelik **Doğrula** ' yı seçer. Microsoft Authenticator uygulaması [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)ve [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)için kullanılabilir. |
| Mobil uygulama veya donanım belirtecinden doğrulama kodu |Microsoft Authenticator uygulaması, her 30 saniyede yeni bir OATH doğrulama kodu oluşturur. Kullanıcı doğrulama kodunu oturum açma arabirimine girer. Microsoft Authenticator uygulaması [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)ve [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)için kullanılabilir. |

### <a name="enable-and-disable-verification-methods"></a>Doğrulama yöntemlerini etkinleştirme ve devre dışı bırakma

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol tarafta **Azure Active Directory** > **kullanıcıları**' nı seçin.
3. **Multi-Factor Authentication**seçin.
4. Multi-Factor Authentication altında **hizmet ayarları**' nı seçin.
5. **Hizmet ayarları** sayfasında, **doğrulama seçenekleri**altında kullanıcılarınıza sağlanacak yöntemlerin seçin/seçimini kaldırın.
6. **Save (Kaydet)** düğmesine tıklayın.

Kimlik doğrulama yöntemlerinin kullanımıyla ilgili ek ayrıntılar makalesinde, [kimlik doğrulama yöntemleri nelerdir](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Multi-Factor Authentication anımsa

Kullanıcı tarafından güvenilen cihazlar ve tarayıcılar için _Multi-Factor Authentication hatırla_ özelliği, tüm Multi-Factor Authentication kullanıcılar için ücretsiz bir özelliktir. Kullanıcılar, Multi-Factor Authentication kullanarak bir cihaza başarıyla oturum açtıktan sonra, belirtilen gün sayısı için sonraki doğrulamaları atlayabilir. Özelliği, bir kullanıcının aynı cihazda iki aşamalı doğrulamayı kaç kez gerçekleştirmesi gerektiğini en aza indirerek kullanılabilirliği geliştirir.

>[!IMPORTANT]
>Bir hesap veya cihazın güvenliği tehlikeye girerse, güvenilir cihazlar için Multi-Factor Authentication hatırlama güvenliği etkileyebilir. Bir şirket hesabının güvenliği tehlikeye girerse veya güvenilir bir cihaz kaybedilirse veya çalınırsa, [MFA oturumlarını iptal](howto-mfa-userdevicesettings.md)etmeniz gerekir.
>
>Geri yükleme eylemi tüm cihazlardan gelen güvenilen durumu iptal eder ve kullanıcının iki aşamalı doğrulamayı yeniden gerçekleştirmesi gerekir. Ayrıca, kullanıcılarınızın kendi cihazlarındaki Multi-Factor Authentication geri yüklemesini, [ayarlarınızı iki adımlı doğrulama Için yönetme](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)konusundaki yönergelerle de söyleyebilirsiniz.

### <a name="how-the-feature-works"></a>Özelliğin çalışma şekli

Multi-Factor Authentication hatırlayın özelliği, Kullanıcı oturum açma sırasında **X gün için yeniden sorma** seçeneğini seçtiğinde tarayıcıda kalıcı bir tanımlama bilgisi ayarlıyor. Tanımlama bilgisinin süresi dolana kadar Kullanıcı aynı tarayıcıdan Multi-Factor Authentication için bir daha istenmez. Kullanıcı aynı cihazda farklı bir tarayıcı açarsa veya tanımlama bilgilerini temizlerse, bunları doğrulamak için bir kez daha istenir.

Uygulamanın modern kimlik doğrulamasını destekleyip desteklemediğini fark etmeksizin, **X gün boyunca yeniden sorma** seçeneği, tarayıcı olmayan uygulamalarda gösterilmez. Bu uygulamalar, her saat yeni erişim belirteçleri sağlayan _yenileme belirteçlerini_ kullanır. Yenileme belirteci doğrulandıktan sonra, Azure AD, son iki adımlı doğrulamanın belirtilen gün sayısı içinde gerçekleştiğini denetler.

Özelliği, Web Apps 'teki kimlik doğrulama sayısını azaltır ve bu, normalde her seferinde uyarır. Özelliği, normalde her 90 günde bir bilgi veren modern kimlik doğrulama istemcileri için kimlik doğrulama sayısını artırır. , Koşullu erişim ilkeleriyle birleştirildiğinde kimlik doğrulama sayısını da artırabilir.

>[!IMPORTANT]
>**Multi-Factor Authentication anımsa** özelliği, kullanıcılar Azure Multi-Factor Authentication sunucusu veya üçüncü taraf Multi-Factor ile AD FS için iki aşamalı doğrulama gerçekleştirirken, AD FS Oturumumu **açık tut** özelliği ile uyumlu değildir kimlik doğrulama çözümü.
>
>Kullanıcılarınız Oturumumu AD FS açık **tut** ' u seçer ve ayrıca cihazlarını Multi-Factor Authentication için güvenilir olarak işaretledikten sonra, **Multi-Factor Authentication** gün sayısı ' nın süresinin dolmasını doğruladıktan sonra Kullanıcı otomatik olarak doğrulanmaz. Azure AD, iki adımlı bir doğrulama ister, ancak AD FS iki adımlı doğrulamayı yeniden gerçekleştirmek yerine özgün Multi-Factor Authentication talep ve Tarih ile bir belirteç döndürür. **Bu yeniden eylem, Azure AD ile AD FS arasındaki bir doğrulama döngüsünü belirler.**
>
>**Multi-Factor Authentication anımsa** özelliği B2B kullanıcılarıyla uyumlu değildir ve davet edilen kiracılarda oturum açarken B2B kullanıcıları için görünür olmayacaktır.
>

### <a name="enable-remember-multi-factor-authentication"></a>Enable Multi-Factor Authentication anımsa

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol tarafta **Azure Active Directory** > **kullanıcıları**' nı seçin.
3. **Multi-Factor Authentication**seçin.
4. Multi-Factor Authentication altında **hizmet ayarları**' nı seçin.
5. **Hizmet ayarları** sayfasında, **çok faktörlü kimlik doğrulamasını anımsa**' yı yönetin, **kullanıcıların güvendikleri cihazlarda Multi-Factor Authentication 'ı anımsamasını izin ver** seçeneğini belirleyin.
6. Güvenilen cihazların iki aşamalı doğrulamayı atlamasına izin vermek için gün sayısını ayarlayın. Varsayılan değer 14 gündür.
7. **Kaydet**’i seçin.

### <a name="mark-a-device-as-trusted"></a>Bir cihazı güvenilir olarak işaretle

Multi-Factor Authentication hatırla özelliğini etkinleştirdikten sonra, kullanıcılar **yeniden sorma**seçeneğini belirleyerek bir cihazı güvenilir olarak işaretleyebilir.

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD oturum açma sayfası markalamasını değiştirme](../fundamentals/customize-branding.md)
