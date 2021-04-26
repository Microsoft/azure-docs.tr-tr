---
title: Azure AD Multi-Factor Authentication Yapılandırma-Azure Active Directory
description: Azure portal Azure AD Multi-Factor Authentication ayarlarını yapılandırma hakkında bilgi edinin
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: c067dba3a8af87e354019154fad8304fe9edfbbc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829665"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Azure AD Multi-Factor Authentication ayarlarını yapılandırma

Azure AD Multi-Factor Authentication için son kullanıcı deneyimini özelleştirmek için hesap kilitleme eşikleri veya sahtekarlık uyarıları ve bildirimleri gibi ayarların seçeneklerini yapılandırabilirsiniz. Bazı ayarlar, Azure Active Directory (Azure AD) ve bazıları ayrı bir Azure AD Multi-Factor Authentication portalında Azure portal.

Aşağıdaki Azure AD Multi-Factor Authentication ayarları Azure portal sunulmaktadır:

| Özellik | Açıklama |
| ------- | ----------- |
| [Hesap kilitleme](#account-lockout) | Bir satırda çok fazla Reddedilen kimlik doğrulama denemesi varsa, hesapları Azure AD Multi-Factor Authentication kullanarak geçici olarak kilitleyin. Bu özellik yalnızca kimlik doğrulaması için PIN girişi yapan kullanıcılar için geçerlidir. (MFA sunucusu) |
| [Kullanıcıları engelle/engelini kaldır](#block-and-unblock-users) | Belirli kullanıcıların Azure AD Multi-Factor Authentication isteklerini almasına engel olmak. Engellenen kullanıcılar için tüm kimlik doğrulaması denemeleri otomatik olarak reddedilir. Kullanıcılar engellendikleri zamandan itibaren 90 gün boyunca engellenmeye devam eder veya el ile engellenmemiş. |
| [Sahtekarlık uyarısı](#fraud-alert) | Kullanıcıların sahte doğrulama isteklerini rapordeğiştirmesine izin veren ayarları yapılandırın. |
| [Bildirimler](#notifications) | MFA sunucusundan olayların bildirimlerini etkinleştirin. |
| [OATH belirteçleri](concept-authentication-oath-tokens.md) | Kullanıcılar için OATH belirteçlerini yönetmek üzere bulut tabanlı Azure AD MFA ortamlarında kullanılır. |
| [Telefon araması ayarları](#phone-call-settings) | Bulut ve şirket içi ortamlar için telefon çağrılarıyla ve tebrikler ilgili ayarları yapılandırın. |
| Sağlayıcılar | Bu, hesabınızla ilişkilendirdiğiniz mevcut herhangi bir kimlik doğrulama sağlayıcısını gösterir. Yeni kimlik doğrulama sağlayıcıları 1 Eylül 2018 itibariyle oluşturulmayabilir |

![Azure portal-Azure AD Multi-Factor Authentication ayarları](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Hesap kilitleme

Bir saldırının parçası olarak yinelenen MFA girişimlerini engellemek için, hesap kilitleme ayarları, hesap bir süre kilitlenmeden önce kaç tane başarısız izin vermek istediğinizi belirtmenizi sağlar. Hesap kilitleme ayarları, yalnızca MFA istemi için bir PIN kodu girildiğinde uygulanır.

Aşağıdaki ayarlar kullanılabilir:

* Hesap kilitlemeyi tetiklemek için MFA reddetme sayısı sayısı
* Hesap kilitleme sayacı sıfırlanana kadar dakika
* Hesabın otomatik olarak engellemesi tamamlanana kadar geçen dakika

Hesap kilitleme ayarlarını yapılandırmak için aşağıdaki ayarları doldurun:

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
1. **Azure Active Directory**  >  **güvenlik**  >  **MFA**  >  **hesabı kilitlemesini** inceleyin.
1. Ortamınız için gerekli değerleri girin ve ardından **Kaydet**' i seçin.

    ![Azure portal hesap kilitleme ayarlarının ekran görüntüsü](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Kullanıcıları engelle ve engellemeyi kaldır

Bir kullanıcının cihazı kaybolduysa veya çalındıysa, ilişkili hesap için Azure AD Multi-Factor Authentication girişimlerini engelleyebilirsiniz. Engellenen kullanıcılar için Azure AD Multi-Factor Authentication girişimleri otomatik olarak reddedilir. Kullanıcılar engellendikleri tarihten 90 gün sonrasına kadar engellenmiş olarak kalır. Bunu nasıl yapılacağını göstermek için [kiracınızdaki kullanıcıları engelleme ve engellemesini kaldırma](https://www.youtube.com/watch?v=WdeE1On4S1o) hakkında bir video yayımladık.

### <a name="block-a-user"></a>Kullanıcıyı engelle

Bir kullanıcıyı engellemek için aşağıdaki adımları izleyin:

1. **Azure Active Directory**  >  **güvenlik**  >  **MFA**  >  **blok/kullanıcıları engellemeyi kaldır**' a gidin.
1. Kullanıcıyı engellemek için **Ekle** ' yi seçin.
1. Engellenen Kullanıcı için Kullanıcı adını girin ve `username@domain.com` sonra *neden* alanında bir açıklama sağlayın.
1. Hazırsanız, kullanıcıyı engellemek için **Tamam** ' ı seçin.

### <a name="unblock-a-user"></a>Kullanıcının engellemesini kaldırma

Bir kullanıcının engelini kaldırmak için aşağıdaki adımları izleyin:

1. **Azure Active Directory**  >  **güvenlik**  >  **MFA**  >  **blok/kullanıcıları engellemeyi kaldır**' a gidin.
1. İstenen kullanıcının yanındaki *eylem* sütununda **Engellemeyi kaldır**' ı seçin.
1. *Engellemeyi kaldırma nedeni* alanına bir açıklama girin.
1. Hazırsanız, kullanıcının engelini kaldırmak için **Tamam** ' ı seçin.

## <a name="fraud-alert"></a>Sahtekarlık uyarısı

Sahtekarlık uyarısı özelliği, kullanıcıların, kaynaklarına erişmek için sahte girişimleri raporlamalarını sağlar. Bilinmeyen ve şüpheli bir MFA istemi alındığında, kullanıcılar Microsoft Authenticator uygulamasını veya telefonlarını kullanarak sahtekarlık girişimini rapor edebilir.

Aşağıdaki sahtekarlık uyarısı yapılandırma seçenekleri mevcuttur:

* **Sahtekarlık raporlayan kullanıcıları otomatik olarak engelle**: bir Kullanıcı sahtekarlık bildirirse, Kullanıcı hesabı IÇIN Azure AD MFA kimlik doğrulama denemeleri 90 gün boyunca engellenir veya bir yönetici kendi hesabını engellemez. Yönetici, oturum açma raporunu kullanarak oturum açma işlemlerini gözden geçirebilir ve ileride sahtekarlık oluşmasını önlemek için gerekli işlemleri gerçekleştirebilir. Yönetici daha sonra kullanıcının hesabının [engellemesini kaldırabilir](#unblock-a-user) .
* **İlk tebrik sırasında sahtekarlık raporlamak Için kod**: kullanıcılar Multi-Factor Authentication 'ı gerçekleştirmek üzere bir telefon araması aldıklarında, normalde **#** oturum açma işlemini onaylamak için Basırlar. Sahtekarlık raporlamak için Kullanıcı, basmadan önce bir kod girer **#** . Bu kod varsayılan olarak **0** ' dır, ancak özelleştirebilirsiniz.

   > [!NOTE]
   > Microsoft 'un varsayılan ses tebrikleri, kullanıcılardan bir sahtekarlık uyarısı göndermesi için **0 #** tuşlarına basmalarını ister. **0** dışında bir kod kullanmak istiyorsanız, kullanıcılarınız için uygun yönergeler ile kendi özel ses tebrikleri kaydedin ve yükleyin.

Sahtekarlık uyarılarını etkinleştirmek ve yapılandırmak için aşağıdaki adımları izleyin:

1. **Azure Active Directory**  >  **güvenliği**  >  **MFA**  >  **sahtekarlığı uyarısı**' na gidin.
1. *Kullanıcıların sahtekarlık uyarıları göndermesine Izin ver* ayarını **Açık** olarak ayarlayın.
1. *İlk tebrik ayarı sırasında* , istediğiniz gibi sahtekarlık veya kod *rapor veren kullanıcıları otomatik olarak engelle* ' i yapılandırın.
1. Hazırlanıyor, **Kaydet**' i seçin.

### <a name="view-fraud-reports"></a>Sahtekarlık raporlarını görüntüleme

**Azure Active Directory**  >  **oturum açma**  >  **kimlik doğrulaması ayrıntıları**' nı seçin. Sahtekarlık raporu artık standart Azure AD oturum açma raporunun bir parçasıdır ve MFA reddedildi olarak, sahtekarlık kodu girildiğinde **"sonuç Ayrıntısı"** içinde görünür.
 
## <a name="notifications"></a>Bildirimler

Kullanıcılar sahtekarlık uyarıları raporladıklarında e-posta bildirimleri yapılandırılabilir. Bu bildirimler genellikle kimlik yöneticilerine gönderilir, kullanıcının hesap kimlik bilgileri büyük olasılıkla tehlikeye girebilir. Aşağıdaki örnekte, bir sahtekarlık uyarı bildirimi e-postası şöyle görünür:

![Örnek sahtekarlık uyarı bildirimi e-postası](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Sahtekarlık uyarı bildirimlerini yapılandırmak için aşağıdaki ayarları doldurun:

1. **Azure Active Directory**  >  **güvenlik**  >  **Multi-Factor Authentication**  >  **bildirimlerine** gidin.
1. Sonraki kutuya eklenecek e-posta adresini girin.
1. Var olan bir e-posta adresini kaldırmak için, istenen e-posta adresinin yanındaki **...** seçeneğini belirleyin ve **Sil**' i seçin.
1. Hazırlanıyor, **Kaydet**' i seçin.

## <a name="oath-tokens"></a>OATH belirteçleri

Azure AD, her 30 veya 60 saniyede bir kodu yenileyen OATH-TOTP SHA-1 belirteçleri kullanımını destekler. Müşteriler bu belirteçleri kendi tercih ettiği satıcıdan satın alabilir.

OATH TOTP Donanım belirteçleri genellikle, belirteçte önceden programlanabilir bir gizli anahtar veya çekirdek ile gelir. Bu anahtarların aşağıdaki adımlarda açıklandığı gibi Azure AD 'ye giriş olması gerekir. Gizli anahtarlar 128 karakterle sınırlıdır ve bu, tüm belirteçlerle uyumlu olmayabilir. Gizli anahtar yalnızca *a-z* veya *a-z* karakterleri ve *1-7* rakamları içerebilir ve *Base32* içinde kodlanmalıdır.

Yeniden kullanılabilen programlanabilir OATH TOTP Donanım belirteçleri, yazılım belirteci kurulum akışında Azure AD ile de ayarlanabilir.

OATH Donanım belirteçleri, genel önizlemenin bir parçası olarak desteklenir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![OATH belirteçlerini MFA OATH belirteçleri dikey penceresine yükleme](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Belirteçler alındıktan sonra, aşağıdaki örnekte gösterildiği gibi, UPN, seri numarası, gizli anahtar, zaman aralığı, üretici ve model dahil olmak üzere bir virgülle ayrılmış değerler (CSV) dosya biçiminde karşıya yüklenmelidir:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Üst bilgi satırını CSV dosyanıza eklediğinizden emin olun.

Bir CSV dosyası olarak düzgün biçimlendirildikten sonra, bir yönetici Azure portal oturum açabilir, **Azure Active Directory > güvenlik > MFA > Oath belirteçleri**' ne gidebilir ve elde edilen CSV dosyasını karşıya yükleyebilir.

CSV dosyasının boyutuna bağlı olarak, işlem birkaç dakika sürebilir. Geçerli durumu almak için **Yenile** düğmesini seçin. Dosyada herhangi bir hata varsa, çözmeniz için herhangi bir hatayı listeleyen bir CSV dosyası indirebilirsiniz. İndirilen CSV dosyasındaki alan adları karşıya yüklenen sürümden farklı.

Herhangi bir hata çözüldükten sonra, yönetici belirteç için **Etkinleştir** ' i seçip BELIRTEÇTE görünen OTP 'yi girerek her anahtarı etkinleştirebilir.

Kullanıcılar, her zaman kullanılmak üzere yapılandırılmış Microsoft Authenticator uygulaması gibi beş OATH donanım belirtecinin veya Authenticator uygulamasının bir birleşimine sahip olabilir.

## <a name="phone-call-settings"></a>Telefon araması ayarları

Kullanıcılar MFA istemleri için telefon görüşmeleri alıyorsa, kendi deneyimlerini yapılandırabilirsiniz; Örneğin, duydukları arayan KIMLIĞI veya sesli tebrik.

Birleşik Devletler, MFA arayanın KIMLIĞINI yapılandırmadıysanız, Microsoft 'tan gelen sesli çağrılar aşağıdaki numaralardan gelir. İstenmeyen posta filtreleri kullanıyorsanız, bu sayıları dışlediğinizden emin olun:

* *+ 1 (866) 539 4191*
* *+ 1 (855) 330 8653*
* *+ 1 (877) 668 6536*

> [!NOTE]
> Azure AD Multi-Factor Authentication çağrıları genel telefon ağı üzerinden yerleştirildiğinde, bazı durumlarda çağrılar arayan KIMLIĞINI desteklemeyen bir taşıyıcı aracılığıyla yönlendirilir. Bu nedenle, Azure AD Multi-Factor Authentication her zaman göndermesi mümkün olsa da arayan KIMLIĞI garanti edilmez. Bu, hem telefon aramalarına hem de Azure AD Multi-Factor Authentication tarafından sunulan SMS iletilerine uygulanır. Bir SMS iletisinin Azure AD Multi-Factor Authentication 'den olduğunu doğrulamanız gerekiyorsa, [ileti göndermek Için hangi SMS kısa kodlarının kullanıldığını görün?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

Kendi arayanın KIMLIK numaranızı yapılandırmak için aşağıdaki adımları izleyin:

1. **Azure Active Directory**  >  **Security**  >  **MFA**  >  **telefon araması ayarları**' na gidin.
1. **MFA arayan kimliği numarasını** kullanıcıların telefonlarındaki görmesini istediğiniz sayı olarak ayarlayın. Yalnızca ABD tabanlı sayılara izin verilir.
1. Hazırlanıyor, **Kaydet**' i seçin.

### <a name="custom-voice-messages"></a>Özel ses iletileri

Özel sesli iletiler özelliği ile Azure AD Multi-Factor Authentication için kendi kayıtlarınızı veya selamlarınızı kullanabilirsiniz. Bu iletiler, varsayılan Microsoft kayıtlarının yanı sıra veya değiştirmek için de kullanılabilir.

Başlamadan önce aşağıdaki kısıtlamalara dikkat edin:

* Desteklenen dosya biçimleri *. wav* ve *. mp3*.
* Dosya boyutu sınırı 1 MB 'tır.
* Kimlik doğrulama iletileri 20 saniyeden kısa olmalıdır. 20 saniyeden uzun olan iletiler doğrulamanın başarısız olmasına neden olabilir. İleti tamamlanmadan önce Kullanıcı yanıt vermemeyebilir ve doğrulama zaman aşımına uğrar.

### <a name="custom-message-language-behavior"></a>Özel ileti dili davranışı

Kullanıcıya özel bir sesli mesaj yürütüldüğünde, iletinin dili aşağıdaki etkenlere bağlıdır:

* Geçerli kullanıcının dili.
  * Kullanıcının tarayıcısı tarafından algılanan dil.
  * Diğer kimlik doğrulama senaryoları farklı davranabilirler.
* Kullanılabilir özel iletilerin dili.
  * Bu dil, özel bir ileti eklendiğinde yönetici tarafından seçilir.

Örneğin, yalnızca bir özel ileti varsa, Almanca dili vardır:

* Almanca dilinde kimlik doğrulaması yapan bir Kullanıcı, özel Alman iletisini duyacaktır.
* Ingilizce 'de kimlik doğrulaması yapan bir Kullanıcı standart Ingilizce iletisini duyacaktır.

### <a name="custom-voice-message-defaults"></a>Özel sesli ileti Varsayılanları

Aşağıdaki örnek betikler kendi özel iletilerinizi oluşturmak için kullanılabilir. Kendi özel iletilerinizi yapılandırmadıysanız bu ifadeler varsayılandır:

| İleti adı | Komut Dosyası |
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
| Kimlik doğrulaması reddedildi | Ne yazık ki şu anda oturumunuzu açamıyorum. Lütfen daha sonra yeniden deneyin. |
| Etkinleştirme karşılama (Standart) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen kare tuşuna basın. |
| Etkinleştirme yeniden deneme (Standart) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen kare tuşuna basın. |
| Etkinleştirme karşılama (PIN) | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Doğrulamanızı tamamlaması için lütfen PIN 'inizi girip kare tuşuna basın. |
| Rakamlardan önce uzantı istemi | Microsoft 'un oturum açma doğrulama sistemini kullandığınız için teşekkürler. Lütfen bu çağrıyı uzantıya aktarın... |

### <a name="set-up-a-custom-message"></a>Özel bir ileti ayarlama

Kendi özel iletilerinizi kullanmak için aşağıdaki adımları izleyin:

1. **Azure Active Directory**  >  **Security**  >  **MFA**  >  **telefon araması ayarları**' na gidin.
1. **Karşılama Ekle**' yi seçin.
1. Selamlama **türü** *(Standart)* veya  *kimlik doğrulama başarılı*' ı seçin.
1. [Özel ileti dili davranışının](#custom-message-language-behavior)önceki bölümüne göre **dili** seçin.
1. Karşıya yüklenecek bir *. mp3* veya *. wav* ses dosyasına gözatıp seçin.
1. Hazırsanız, **Ekle**' yi ve ardından **Kaydet**' i seçin.

## <a name="mfa-service-settings"></a>MFA hizmet ayarları

Uygulama parolaları, güvenilir IP 'Ler, doğrulama seçenekleri ve Azure AD Multi-Factor Authentication için Multi-Factor Authentication 'ı anımsa ayarları hizmet ayarları 'nda bulunabilir. Bu, eski portaldan daha fazlasına sahiptir ve normal Azure AD portalının bir parçası değildir.

Hizmet ayarlarına **Azure Active Directory** güvenlik MFA 'ya giderek Azure Portal erişilebilir ve  >    >    >    >    >  **bulut tabanlı ek MFA ayarlarını** yapılandırma ile çalışmaya başlama. Ek *hizmet ayarları* seçenekleriyle yeni bir pencere veya sekme açılır.

## <a name="trusted-ips"></a>Güvenilen IP'ler

Azure AD Multi-Factor Authentication 'nin _güvenilir IP 'leri_ özelliği, TANıMLı bir IP adresi aralığından oturum açan kullanıcılar için Multi-Factor Authentication istemlerini atlar. Şirket içi ortamlarınız için güvenilen IP aralıklarını ayarlayabilirsiniz, böylece kullanıcılar bu konumlardan birinde olduğunda Azure AD Multi-Factor Authentication istemi yoktur. Azure AD Multi-Factor Authentication 'nin _güvenilir IP 'leri_ özelliği Azure AD Premium P1 sürümü gerektirir. 

> [!NOTE]
> Güvenilen IP 'Ler yalnızca MFA sunucusu kullandığınızda özel IP aralıkları içerebilir. Bulut tabanlı Azure AD Multi-Factor Authentication için yalnızca genel IP adresi aralıklarını kullanabilirsiniz.
>
> IPv6 aralıkları yalnızca [adlandırılmış konum (Önizleme)](../conditional-access/location-condition.md) arabiriminde desteklenir.

Kuruluşunuz, şirket içi uygulamalara MFA sağlamak üzere NPS uzantısını dağıttığında, kaynak IP adresi her zaman kimlik doğrulama girişiminin akışa aldığı NPS sunucusu olacak şekilde görünür.

| Azure AD kiracı türü | Güvenilen IP özelliği seçenekleri |
|:--- |:--- |
| Yönetilen |**Belırlı IP adresi aralığı**: Yöneticiler, şirket intranetinden oturum açan kullanıcılar için Multi-Factor Authentication 'ı atlayabilen bir IP adresleri aralığı belirtir. En fazla 50 güvenilen IP aralığı yapılandırılabilir.|
| Federe |**Tüm Federasyon kullanıcıları**: kuruluşun içinden oturum açan tüm Federasyon kullanıcıları Multi-Factor Authentication 'ı atlayabilir. Kullanıcılar, Active Directory Federasyon Hizmetleri (AD FS) (AD FS) tarafından verilen bir talep kullanarak doğrulamayı atlar.<br/>**Belırlı IP adresi aralığı**: Yöneticiler, şirket intranetinden oturum açan kullanıcılar için Multi-Factor Authentication 'ı atlayabilen bir IP adresleri aralığı belirtir. |

Güvenilen IP atlama yalnızca şirket intraneti 'nin içinden işe yarar. **Tüm Federasyon kullanıcıları** seçeneğini belirlerseniz ve Kullanıcı şirket intranet dışından oturum açarsa, kullanıcının Multi-Factor Authentication kullanarak kimlik doğrulaması yapması gerekir. Kullanıcı bir AD FS talebi sunsa bile, işlem aynıdır.

### <a name="end-user-experience-inside-of-corpnet"></a>Corpnet içinde son kullanıcı deneyimi

Güvenilir IP 'Ler özelliği devre dışı bırakıldığında, tarayıcı akışları için çok faktörlü kimlik doğrulaması gerekir. Daha eski zengin istemci uygulamaları için uygulama parolaları gereklidir.

Güvenilen IP 'Ler kullanıldığında, tarayıcı akışları için çok faktörlü kimlik doğrulaması gerekli değildir. Kullanıcı bir uygulama parolası oluşturmadığı için, daha eski zengin istemci uygulamaları için uygulama parolaları gerekli değildir. Bir uygulama parolası kullanımda olduktan sonra parola gerekli kalır.

### <a name="end-user-experience-outside-corpnet"></a>Corpnet dışında son kullanıcı deneyimi

Güvenilen IP 'nin tanımlanıp tanımlanmadığına bakılmaksızın, tarayıcı akışları için çok faktörlü kimlik doğrulaması gerekir. Daha eski zengin istemci uygulamaları için uygulama parolaları gereklidir.

### <a name="enable-named-locations-by-using-conditional-access"></a>Koşullu erişimi kullanarak adlandırılmış konumları etkinleştir

Aşağıdaki adımları kullanarak adlandırılmış konumları tanımlamak için koşullu erişim kurallarını kullanabilirsiniz:

1. Azure Portal, **Azure Active Directory** arayıp seçin, ardından **güvenlik**  >  **koşullu erişim**  >  **adlı konumlara** gidin.
1. **Yeni konum**' u seçin.
1. Konum için bir ad girin.
1. **Güvenilen konum olarak işaretle**' yi seçin.
1. Ortamınız için *40.77.182.32/27* gibi CıDR gösteriminde IP aralığını girin.
1. **Oluştur**’u seçin.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Koşullu erişimi kullanarak güvenilir IP 'Ler özelliğini etkinleştirme

Koşullu erişim ilkelerini kullanarak güvenilen IP 'Leri etkinleştirmek için aşağıdaki adımları izleyin:

1. Azure Portal, **Azure Active Directory** arayıp seçin, ardından **güvenlik**  >   **koşullu erişim**  >  **adlı konumlara** gidin.
1. **MFA güvenilir IP 'Lerini Yapılandır**' ı seçin.
1. **Hizmet ayarları** sayfasında, **güvenilir IP 'ler** altında, aşağıdaki iki seçenekten birini seçin:

   * **İntranetten kaynaklanan Federal kullanıcılardan gelen Istekler için**: Bu seçeneği belirlemek için onay kutusunu seçin. Şirket ağından oturum açan tüm Federasyon kullanıcıları, AD FS tarafından verilen bir talebi kullanarak çok faktörlü kimlik doğrulamasını atlar. AD FS, intranet talebini uygun trafiğe eklemek için bir kural içerdiğinden emin olun. Kural yoksa AD FS ' de aşağıdaki kuralı oluşturun:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Belirli bir genel IP aralığından gelen Istekler için**: Bu seçeneği belirlemek IÇIN, CIDR gösterimini kullanarak metın kutusuna IP adreslerini girin.
      * Xxx. xxx. xxx. 1 ile xxx. xxx. xxx. 254 aralığındaki IP adresleri için, **xxx. xxx. xxx. 0/24** gibi bir gösterim kullanın.
      * Tek bir IP adresi için **xxx.xxx.xxx.xxx/32** gibi bir gösterim kullanın.
      * En fazla 50 IP adresi aralığı girin. Bu IP adreslerinden oturum açan kullanıcılar Multi-Factor Authentication 'ı atlar.

1. **Kaydet**’i seçin.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Hizmet ayarlarını kullanarak güvenilir IP 'Leri özelliğini etkinleştirme

Güvenilen IP 'Leri etkinleştirmek için koşullu erişim ilkeleri kullanmak istemiyorsanız, aşağıdaki adımları kullanarak Azure AD Multi-Factor Authentication için *hizmet ayarlarını* yapılandırabilirsiniz:

1. Azure portal, **Azure Active Directory** arayıp seçin ve ardından **Kullanıcılar**' ı seçin.
1. **Multi-Factor Authentication**'ı seçin.
1. Multi-Factor Authentication altında **hizmet ayarları**' nı seçin.
1. **Hizmet ayarları** sayfasında, **güvenilir IP 'ler** altında, aşağıdaki iki seçenekten birini (veya her ikisini) seçin:

   * **İntranetimde Federal kullanıcılardan gelen Istekler için**: Bu seçeneği belirlemek için onay kutusunu işaretleyin. Şirket ağından oturum açan tüm Federasyon kullanıcıları, AD FS tarafından verilen bir talebi kullanarak çok faktörlü kimlik doğrulamasını atlar. AD FS, intranet talebini uygun trafiğe eklemek için bir kural içerdiğinden emin olun. Kural yoksa AD FS ' de aşağıdaki kuralı oluşturun:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Belirli BIR IP adresi alt ağı aralığından gelen Istekler için**: Bu seçeneği belirlemek IÇIN, CIDR gösterimini kullanarak metın kutusuna IP adreslerini girin.
      * Xxx. xxx. xxx. 1 ile xxx. xxx. xxx. 254 aralığındaki IP adresleri için, **xxx. xxx. xxx. 0/24** gibi bir gösterim kullanın.
      * Tek bir IP adresi için **xxx.xxx.xxx.xxx/32** gibi bir gösterim kullanın.
      * En fazla 50 IP adresi aralığı girin. Bu IP adreslerinden oturum açan kullanıcılar Multi-Factor Authentication 'ı atlar.

1. **Kaydet**’i seçin.

## <a name="verification-methods"></a>Doğrulama yöntemleri

Hizmet ayarları portalındaki kullanıcılarınız için kullanılabilir doğrulama yöntemlerini seçebilirsiniz. Kullanıcılarınız Azure AD Multi-Factor Authentication hesaplarını kaydettiğinde, etkinleştirdiğiniz seçeneklerden tercih edilen doğrulama yöntemini seçer. Kullanıcı kayıt işlemine kılavuzluk, [Multi-Factor Authentication için hesabımı ayarlama](../user-help/multi-factor-authentication-end-user-first-time.md)bölümünde verilmiştir.

Aşağıdaki doğrulama yöntemleri kullanılabilir:

| Yöntem | Açıklama |
|:--- |:--- |
| Telefon çağrısı |Otomatik bir sesli çağrı koyar. Kullanıcı aramaya yanıt verir ve kimliğini doğrulamak için telefonda # tuşuna basar. Telefon numarası şirket içi Active Directory ile eşitlenmez. |
| Telefona kısa mesaj |Doğrulama kodu içeren bir kısa mesaj gönderir. Kullanıcıdan, doğrulama kodunu oturum açma arabirimine girmesi istenir. Bu işlem tek yönlü SMS olarak adlandırılır. İki yönlü SMS, kullanıcının belirli bir kodu doğru bir şekilde geri metin döndürmelidir. İki yönlü SMS kullanım dışıdır ve 14 Kasım 2018 sonrasında desteklenmez. Yöneticiler, daha önce iki yönlü SMS kullanan kullanıcılar için başka bir yöntemi etkinleştirmelidir.|
| Mobil uygulama aracılığıyla bildirim |Telefonunuza veya kayıtlı cihazınıza anında iletme bildirimi gönderir. Kullanıcı bildirimi görüntüler ve doğrulamayı tamamlamaya yönelik **Doğrula** ' yı seçer. Microsoft Authenticator uygulaması [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)ve [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)için kullanılabilir. |
| Mobil uygulama veya donanım belirtecinden doğrulama kodu |Microsoft Authenticator uygulaması, her 30 saniyede yeni bir OATH doğrulama kodu oluşturur. Kullanıcı doğrulama kodunu oturum açma arabirimine girer. Microsoft Authenticator uygulaması [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)ve [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)için kullanılabilir. |

Daha fazla bilgi için bkz. [Azure AD 'de hangi kimlik doğrulama ve doğrulama yöntemlerinin kullanılabildiği](concept-authentication-methods.md) .

### <a name="enable-and-disable-verification-methods"></a>Doğrulama yöntemlerini etkinleştirme ve devre dışı bırakma

Doğrulama yöntemlerini etkinleştirmek veya devre dışı bırakmak için aşağıdaki adımları izleyin:

1. Azure portal, **Azure Active Directory** arayıp seçin ve ardından **Kullanıcılar**' ı seçin.
1. **Multi-Factor Authentication**'ı seçin.
1. Multi-Factor Authentication altında **hizmet ayarları**' nı seçin.
1. **Hizmet ayarları** sayfasında, **doğrulama seçenekleri** altında kullanıcılarınıza sağlanacak yöntemlerin seçin/seçimini kaldırın.
1. **Kaydet**’e tıklayın.

## <a name="remember-multi-factor-authentication"></a>Multi-Factor Authentication anımsa

_Multi-Factor Authentication anımsa_ özelliği, kullanıcıların, Multi-Factor Authentication kullanarak bir cihaza başarıyla oturum açtıktan sonra, belirtilen gün sayısı için sonraki doğrulamaları atlayabilecekleri şekilde izin verir. Kullanılabilirliği geliştirmek ve bir kullanıcının aynı cihazda MFA 'yı kaç kez gerçekleştirmesi gerektiğini en aza indirmek için, 90 gün veya daha uzun bir süre seçin.

> [!IMPORTANT]
> Bir hesap veya cihazın güvenliği tehlikeye girerse, güvenilir cihazlar için Multi-Factor Authentication hatırlama güvenliği etkileyebilir. Bir şirket hesabının güvenliği tehlikeye girerse veya güvenilir bir cihaz kaybedilirse veya çalınırsa, [MFA oturumlarını iptal](howto-mfa-userdevicesettings.md)etmeniz gerekir.
>
> Geri yükleme eylemi tüm cihazlardan gelen güvenilen durumu iptal eder ve kullanıcının Multi-Factor Authentication 'ı yeniden gerçekleştirmesi gerekir. Kullanıcılarınıza, [çok faktörlü kimlik doğrulaması için ayarlarınızı yönetme](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)bölümünde belirtildiği gibi, kendi cihazlarındaki Multi-Factor Authentication geri yüklemesini de söyleyebilirsiniz.

### <a name="how-the-feature-works"></a>Özelliğin çalışma şekli

Multi-Factor Authentication hatırlayın özelliği, Kullanıcı oturum açma sırasında **X gün için yeniden sorma** seçeneğini seçtiğinde tarayıcıda kalıcı bir tanımlama bilgisi ayarlıyor. Tanımlama bilgisinin süresi dolana kadar Kullanıcı aynı tarayıcıdan Multi-Factor Authentication için bir daha istenmez. Kullanıcı aynı cihazda farklı bir tarayıcı açarsa veya tanımlama bilgilerini temizlerse, bunları doğrulamak için bir kez daha istenir.

Uygulamanın modern kimlik doğrulamasını destekleyip desteklemediğini fark etmeksizin, **X gün boyunca yeniden sorma** seçeneği, tarayıcı olmayan uygulamalarda gösterilmez. Bu uygulamalar, her saat yeni erişim belirteçleri sağlayan _yenileme belirteçlerini_ kullanır. Yenileme belirteci doğrulandıktan sonra Azure AD, son Multi-Factor Authentication 'ın belirtilen gün sayısı içinde gerçekleştiğini denetler.

Özelliği, Web Apps 'teki kimlik doğrulama sayısını azaltır ve bu, normalde her seferinde uyarır. Bu özellik, daha düşük bir süre yapılandırılırsa, normalde her 180 günde bir istemde bulunan modern kimlik doğrulama istemcilerinin kimlik doğrulama sayısını artırabilir. , Koşullu erişim ilkeleriyle birleştirildiğinde kimlik doğrulama sayısını da artırabilir.

> [!IMPORTANT]
> **Multi-Factor Authentication anımsa** özelliği, kullanıcılar Azure Multi-Factor Authentication sunucusu veya üçüncü taraf bir multi-factor authentication çözümü AD FS için multi-factor authentication gerçekleştirirken AD FS Oturumumu **açık tut** özelliği ile uyumlu değildir.
>
> Kullanıcılarınız Oturumumu AD FS açık **tut** ' u seçer ve ayrıca cihazlarını Multi-Factor Authentication için güvenilir olarak işaretledikten sonra, **Multi-Factor Authentication** gün sayısı ' nın süresinin dolmasını doğruladıktan sonra Kullanıcı otomatik olarak doğrulanmaz. Azure AD, yeni bir Multi-Factor Authentication ister, ancak AD FS Multi-Factor Authentication 'ı yeniden gerçekleştirmek yerine özgün Multi-Factor Authentication talep ve Tarih ile bir belirteç döndürür. **Bu yeniden eylem, Azure AD ile AD FS arasındaki bir doğrulama döngüsünü belirler.**
>
> **Multi-Factor Authentication anımsa** özelliği B2B kullanıcılarıyla uyumlu değildir ve davet edilen kiracılarda oturum açarken B2B kullanıcıları için görünür olmayacaktır.
>

### <a name="enable-remember-multi-factor-authentication"></a>Enable Multi-Factor Authentication anımsa

Kullanıcıların MFA durumunu hatırlamaları ve istemleri atlaması için seçeneğini etkinleştirmek ve yapılandırmak için aşağıdaki adımları izleyin:

1. Azure portal, **Azure Active Directory** arayıp seçin ve ardından **Kullanıcılar**' ı seçin.
1. **Multi-Factor Authentication**'ı seçin.
1. Multi-Factor Authentication altında **hizmet ayarları**' nı seçin.
1. **Hizmet ayarları** sayfasında, **Multi-Factor Authentication 'ı anımsa** altında, **kullanıcıların güvendikleri cihazlarda Multi-Factor Authentication 'ı anımsamasını izin ver** seçeneğini belirleyin.
1. Güvenilen cihazların Multi-Factor Authentication 'ı atlamasına izin verilecek gün sayısını ayarlayın. En iyi kullanıcı deneyimi için süreyi *90* veya daha fazla gün uzatın.
1. **Kaydet**’i seçin.

### <a name="mark-a-device-as-trusted"></a>Bir cihazı güvenilir olarak işaretle

Multi-Factor Authentication hatırla özelliğini etkinleştirdikten sonra, kullanıcılar bir **daha sorma** seçeneğini belirleyerek oturum açtıklarında bir cihazı güvenilir olarak işaretleyebilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD Multi-Factor Authentication 'da kullanılabilecek yöntemler hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory hangi kimlik doğrulama ve doğrulama yöntemlerinin kullanılabildiği](concept-authentication-methods.md) .
