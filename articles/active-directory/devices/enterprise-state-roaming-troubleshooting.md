---
title: Azure Active Directory Enterprise State Roaming sorunlarını giderme
description: Yöneticilerin ayarlar ve uygulama verilerinin eşitlenmesi hakkında sahip olabileceği bazı soruların yanıtlarını sağlar.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: df70891ef090d44769aadbc235273e3193bc780e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94837218"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Azure Active Directory Enterprise State Roaming ayarları sorunlarını giderme

Bu konu, Enterprise State Roaming sorunları giderme ve tanılama hakkında bilgi sağlar ve bilinen sorunların bir listesini sağlar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Bu makale, 2015 Temmuz sürümünde Windows 10 ile başlatılan Microsoft Edge eski HTML tabanlı tarayıcı için geçerlidir. Makale, 15 Ocak 2020 ' de yayınlanan yeni Microsoft Edge Bermıum tabanlı tarayıcı için de geçerlidir. Yeni Microsoft Edge için eşitleme davranışı hakkında daha fazla bilgi için [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)makalesine bakın.

## <a name="preliminary-steps-for-troubleshooting"></a>Sorun giderme için ön adımlar 

Sorun gidermeye başlamadan önce, Kullanıcı ve cihazın düzgün şekilde yapılandırıldığını ve tüm Enterprise State Roaming gereksinimlerinin cihaz ve Kullanıcı tarafından karşılandığını doğrulayın. 

1. Windows 10, en son güncelleştirmelerle ve en düşük sürüm 1511 (OS Build 10586 veya üzeri) cihazda yüklü. 
1. Cihaz Azure AD 'ye katılmış veya hibrit Azure AD 'ye katılmış. Daha fazla bilgi için bkz. [Azure AD 'nin denetimi altında cihaz alma](overview.md).
1. Azure AD 'de kiracı için **Enterprise State Roaming** [Enterprise State Roaming etkinleştirme](enterprise-state-roaming-enable.md)bölümünde açıklandığı gibi etkinleştirildiğinden emin olun. Tüm kullanıcılar için veya yalnızca seçili bir Kullanıcı grubu için dolaşımı etkinleştirebilirsiniz.
1. Kullanıcıya bir Azure Active Directory Premium Lisansı atanır.  
1. Cihazın yeniden başlatılması ve kullanıcının Enterprise State Roaming özelliklere erişmek için yeniden oturum açması gerekir.

## <a name="information-to-include-when-you-need-help"></a>Yardıma ihtiyacınız olduğunda eklenecek bilgiler
Aşağıdaki kılavuzdan sorununuzu çözemezseniz destek mühendislerimize başvurabilirsiniz. Bunlara iletişim kuradığınızda, aşağıdaki bilgileri ekleyin:

* **Hatanın genel açıklaması**: Kullanıcı tarafından görülen hata iletileri var mı? Hata iletisi yoksa, fark ettiğiniz beklenmedik davranışı ayrıntılı olarak tanıtın. Eşitleme için hangi özellikler etkinleştirilmiştir ve kullanıcının eşitlenmesi bekleniyor mu? Birden çok özellik eşitlenmiyor veya bir tane ile yalıtılmış mi?
* **Etkilenen kullanıcılar** – bir kullanıcı veya birden çok kullanıcı için eşitleme çalışıyor/başarısız mi? Kullanıcı başına kaç cihaz katıldı? Bunların tümü eşitlenmiyor veya bir kısmı eşitlenmiyor ve bazıları eşitlenmiyor mu?
* **Kullanıcı hakkında bilgiler** – cihazda oturum açmak için kullanıcının hangi kimlik olduğunu kullanıyor? Kullanıcı cihazda nasıl oturum açılıyor? Seçili bir güvenlik grubunun eşitlemesine izin veriliyor mu? 
* **Cihaz hakkında bilgi** – bu CIHAZ Azure AD 'ye katılmış veya etki alanına katılmış mi? Cihaz hangi yapıda? En son güncelleştirmeler nelerdir?
* **Tarih/saat/saat dilimi** – hatayı gördüğünüz kesin tarih ve saat (saat dilimini dahil)?

Bu bilgilerin dahil edilmesi, sorununuzu olabildiğince çabuk çözmemize yardımcı olur.

## <a name="troubleshooting-and-diagnosing-issues"></a>Sorunları tanılama ve giderme

Bu bölüm Enterprise State Roaming ilgili sorunları gidermeye ve tanılamaya yönelik öneriler sağlar.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Eşitlemeyi doğrulama ve "ayarlarınızı eşitleme" ayarları sayfası 

1. Windows 10 PC 'nizi Enterprise State Roaming izin verecek şekilde yapılandırılmış bir etki alanına katıldıktan sonra iş hesabınızla oturum açın. **Ayarlar**  >  **hesaplar**'  >  a gidin,**Ayarlarınızı eşitleyin** ve eşitlemenin ve bireysel ayarların açık olduğunu ve Ayarlar sayfasının en üst tarafında iş hesabınızla eşitlediğiniz olduğunu doğrulayın. Aynı hesabın, **Ayarlar**  >  **hesaplar**  >  **bilgileriniz** içinde oturum açma hesabınız olarak da kullanıldığını onaylayın. 
1. Görev çubuğunu ekranın sağ veya üst tarafına taşıma gibi bazı değişiklikler yaparak, eşitlemenin birden çok makine arasında çalıştığını doğrulayın. İkinci makineye beş dakika içinde yayan değişikliği izleyin. 

   * Ekranı kilitlemek ve kilidini açmak (Win + L), eşitlemenin tetiklenmesine yardımcı olabilir.
   * Eşitleme için her iki bilgisayarda da aynı hesapla oturum açmış olmanız gerekir; Enterprise State Roaming, makine hesabına değil Kullanıcı hesabına bağlıdır.

**Olası sorun**: **Ayarlar** sayfasındaki denetimler kullanılamıyorsa ve "bazı Windows özellikleri yalnızca bir Microsoft hesabı veya iş hesabı kullanıyorsanız kullanılabilir" iletisini görürseniz. Bu sorun, etki alanına katılmış ve Azure AD 'ye kayıtlı olacak şekilde ayarlanmış cihazlarda, ancak cihazın Azure AD 'ye başarıyla kimlik doğrulaması yapılmamış olabilir. Olası bir neden cihaz ilkesinin uygulanması gerekir, ancak bu uygulama zaman uyumsuz olarak gerçekleşir ve birkaç saat geciktirilebilir. 

### <a name="verify-the-device-registration-status"></a>Cihaz kayıt durumunu doğrulama

Enterprise State Roaming, cihazın Azure AD 'ye kaydedilmesini gerektirir. Enterprise State Roaming özgü olmamakla birlikte, aşağıdaki yönergeleri izleyerek Windows 10 Istemcisinin kaydedildiğini ve parmak izini, Azure AD ayarları URL 'sini, NGC durumunu ve diğer bilgileri onaylamanız yardımcı olabilir.

1. Yükseltbırakılmamış komut istemi ' ni açın. Bunu Windows 'ta yapmak için, çalıştırma başlatıcısı 'nı (Win + R) açın ve açmak için "cmd" yazın.
1. Komut istemi açıldıktan sonra, "*dsregcmd.exe/Status*" yazın.
1. Beklenen çıkış için, **Azureadkatılmış** alan DEĞERI "Yes" olmalıdır, **Wamdefaultset** alan değeri "Yes" olmalıdır ve **wamdefaultguid** alan değeri sonunda "(AZUREAD)" ile bir GUID olmalıdır.

**Olası sorun**: **wamdefaultset** ve **azureadkatılmış** her ikisi de alan değerinde "Hayır", CIHAZıN etki alanına katılmış ve Azure AD 'ye kayıtlı olduğu ve cihaz eşitlenmediği için. Bunu gösteriyorsa cihazın, ilkenin uygulanmasını beklemesi veya Azure AD 'ye bağlanılırken cihazın kimlik doğrulaması başarısız olması gerekebilir. Kullanıcının ilkenin uygulanması için birkaç saat beklemeniz gerekebilir. Diğer sorun giderme adımları, yeniden oturum açıp tekrar oturum açarak veya Görev Zamanlayıcı görevi başlatarak, tekrar kaydolmayı yeniden denemeyi içerebilir. Bazı durumlarda, yükseltilmiş bir komut istemi penceresinde "*dsregcmd.exe/Leave*" komutunu çalıştırın, yeniden başlatma ve kayıt yeniden deneniyor bu sorunla ilgili yardımcı olabilir.

**Olası sorun**: **settingsurl** alanı boş ve cihaz eşitlenmedi. Azure Active Directory portalında Enterprise State Roaming etkinleştirilmeden önce Kullanıcı cihazda en son oturum açmış olabilir. Cihazı yeniden başlatın ve kullanıcının oturum açmasını sağlayabilirsiniz. İsteğe bağlı olarak, portalda BT yöneticisinin **Azure Active Directory**  >  **cihazlara** gitmesini  >  **Enterprise State Roaming** , **kullanıcıların ayarları ve uygulama verilerini cihazlar arasında eşitlemesine izin verebilir**. Yeniden etkinleştirildikten sonra, cihazı yeniden başlatın ve kullanıcının oturum açmasını sağlayabilirsiniz. Bu sorunu çözmezse, hatalı bir cihaz sertifikası varsa **Settingsurl** boş olabilir. Bu durumda, yükseltilmiş bir komut istemi penceresinde "*dsregcmd.exe/Leave*" komutunu çalıştırın, yeniden başlatma ve kayıt yeniden deneniyor bu sorunla ilgili yardımcı olabilir.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming ve Multi-Factor Authentication 

Bazı koşullar altında, Enterprise State Roaming Azure AD Multi-Factor Authentication yapılandırılmışsa verileri eşitleyemeyebilir. Bu belirtiler hakkında daha fazla bilgi için bkz. destek belgesi [KB3193683](https://support.microsoft.com/kb/3193683). 

**Olası sorun**: cihazınız Azure Active Directory portalında Multi-Factor Authentication gerektirecek şekilde yapılandırıldıysa, parolayı kullanarak bir Windows 10 cihazında oturum açarken ayarları eşitleyemeyebilirsiniz. Bu tür Multi-Factor Authentication yapılandırma, bir Azure yönetici hesabını korumak için tasarlanmıştır. Yönetici kullanıcılar Windows 10 cihazlarında İş için Microsoft Passport PIN 'i ile oturum açarak veya Microsoft 365 gibi diğer Azure hizmetlerine erişirken Multi-Factor Authentication tamamlayarak eşitleme yapabilir.

**Olası sorun**: yönetici Active Directory Federasyon Hizmetleri (AD FS) Multi-Factor Authentication koşullu erişim ilkesi ve cihazdaki erişim belirtecinin süresi dolarsa eşitleme başarısız olabilir. Microsoft 365 gibi diğer Azure hizmetlerine erişirken İş için Microsoft Passport PIN 'ı kullanarak oturum açıp oturumunuzu kapatıp Multi-Factor Authentication.

### <a name="event-viewer"></a>Olay Görüntüleyicisi

Gelişmiş sorun giderme için Olay Görüntüleyicisi belirli hataları bulmak için kullanılabilir. Bunlar aşağıdaki tabloda belgelenmiştir. Olaylar Olay Görüntüleyicisi altında bulunabilir > **uygulamalar ve hizmetler**,  >  **Microsoft**  >  **Windows**  >  **settingsync-Azure** ' a kaydedilir ve eşitleme **Uygulamaları ve Hizmetleri günlükleriyle** ilgili kimlik sorunları için  >  **Microsoft**  >  **Windows**  >  **AAD**.

## <a name="known-issues"></a>Bilinen sorunlar

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Eşitleme, uygulamaların MDM yazılımını kullanarak dışarıdan yüklendiği cihazlarda çalışmaz

Windows 10 yıldönümü Güncelleştirmesi (sürüm 1607) çalıştıran cihazları etkiler. SettingSync-Azure günlüklerinin altındaki Olay Görüntüleyicisi, 6013 80070259 hatası ile olay KIMLIĞI sıklıkla görülür.

**Önerilen eylem**  
Windows 10 v1607 istemcisinde 23 Ağustos 2016 tarihli toplu güncelleştirme ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393,82) olduğundan emin olun. 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Internet Explorer sık kullanılanları eşitleme

Windows 10 Kasım Güncelleştirmesi (sürüm 1511) çalıştıran cihazları etkiler.

**Önerilen eylem**  
Windows 10 v1511 istemcisinin Temmuz 2016 toplu güncelleştirmesine ([KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586,494) sahip olduğundan emin olun.

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Tema eşitlenmiyor ve Windows Information Protection ile korunan veriler 

Veri sızıntısını engellemek için [windows Information Protection](/windows/security/information-protection/windows-information-protection/protect-enterprise-data-using-wip) ile korunan veriler, Windows 10 yıldönümü güncelleştirmesi kullanılarak cihazların Enterprise State Roaming üzerinden eşitlenmez.

**Önerilen eylem**  
Yok. Windows için gelecekteki güncelleştirmeler bu sorunu çözebilir.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Tarih, saat ve bölge ayarları, etki alanına katılmış cihazda eşitlenmedi 
  
Etki alanına katılmış cihazlar, tarih, saat ve bölge ayarları için eşitleme deneyimmeyecektir: otomatik saat. Otomatik saatin kullanılması diğer tarih, saat ve bölge ayarlarını geçersiz kılabilir ve bu ayarların eşitlenmemesine neden olabilir. 

**Önerilen eylem**  
Yok. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>Parolaları eşitlerken UAC Istemleri

Windows 10 Kasım Güncelleştirmesi (sürüm 1511) çalıştıran cihazları, parolaları eşitlemek üzere yapılandırılmış bir kablosuz NIC ile etkiler.

**Önerilen eylem**  
Windows 10 v1511 istemcisinin toplu güncelleştirme ([KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586,494) içerdiğinden emin olun.

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Eşitleme, oturum açma için akıllı kart kullanan cihazlarda çalışmıyor

Windows cihazınızda bir akıllı kart veya sanal akıllı kart kullanarak oturum açmaya çalışırsanız, ayarlar eşitlemesi çalışmayı durdurur.     

**Önerilen eylem**  
Yok. Windows için gelecekteki güncelleştirmeler bu sorunu çözebilir.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Şirket ağından ayrıldıktan sonra etki alanına katılmış cihaz eşitlenmiyor     

Azure AD 'ye kayıtlı etki alanına katılmış cihazlar, cihaz uzun süreler boyunca yerinde değilse ve etki alanı kimlik doğrulaması tamamlanmazsa eşitleme hatası yaşayabilir.

**Önerilen eylem**  
Eşitlemenin sürdürülabilmesi için cihazı bir kurumsal ağa bağlayın.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Azure AD 'ye katılmış cihaz eşitlenmiyor ve kullanıcının karma bir örnek Kullanıcı asıl adı vardır.

Kullanıcının karma bir durum UPN 'si varsa (örneğin, Kullanıcı adı yerine Kullanıcı adı) ve Kullanıcı Windows 10 Build 10586 ' den 14393 ' e yükseltilen bir Azure AD 'ye katılmış cihazdaysa, kullanıcının cihazı eşitlenemeyebilir. 

**Önerilen eylem**  
Kullanıcının cihaza katılmayacak ve buluta yeniden katılması gerekir. Bunu yapmak için, yerel yönetici kullanıcı olarak oturum açın ve **Ayarlar**  >  **sistemine** giderek cihazın  >   bağlantısını kaldırın ve "iş veya okuldan yönetme veya bağlantıyı kes" seçeneğini belirleyin. Aşağıdaki dosyaları temizleyin ve ardından Azure AD ' ın **Ayarlar** sisteminde cihazı yeniden birleştirin  >    >   ve "iş veya okula Bağlan" seçeneğini seçin. Azure Active Directory cihaza katılmayı ve akışı tamamlamayı sürdürün.

Temizleme adımında, aşağıdaki dosyaları temizleyin:
- İçindeki Settings. dat `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Klasörün altındaki tüm dosyalar `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Olay KIMLIĞI 6065:80070533 bu hesap şu anda devre dışı olduğundan bu kullanıcı oturum açamıyor  

SettingSync/Debug Günlükleri altındaki Olay Görüntüleyicisi, kullanıcının kimlik bilgilerinin kullanım zamanı dolduğunda bu hata görülebilir. Buna ek olarak, kiracı AzureRMS tarafından otomatik olarak sağlanmamışsa meydana gelebilir. 

**Önerilen eylem**  
İlk durumda, kullanıcının kimlik bilgilerini güncelleştirmesini ve yeni kimlik bilgileriyle cihazda oturum açmasını sağlayabilirsiniz. AzureRMS sorununu gidermek için, [KB3193791](https://support.microsoft.com/kb/3193791)' de listelenen adımlara ilerleyin. 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Olay KIMLIĞI 1098: hata: 0Xcaa500 1C belirteç Aracısı işlemi başarısız oldu  

AAD/Işletimsel Günlükleri altındaki Olay Görüntüleyicisi, bu hata olay 1104: AAD Cloud AP eklenti çağrısı Get Token hatası döndürdü: 0xC000005F. Bu sorun, izin veya sahiplik özniteliklerinin eksik olması durumunda oluşur.  

**Önerilen eylem**  
[KB3196528](https://support.microsoft.com/kb/3196528)listelenen adımlarla devam edin.  

## <a name="next-steps"></a>Sonraki adımlar

Genel bakış için bkz. [kuruluş durumu dolaşımına genel bakış](enterprise-state-roaming-overview.md).