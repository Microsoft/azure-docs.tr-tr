---
title: Koşullu erişim ilkesindeki koşullar-Azure Active Directory
description: Azure AD koşullu erişim ilkesinde koşullar nelerdir?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab840b46503aed1a318e3b39a4e8fe3e4d11735c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579136"
---
# <a name="conditional-access-conditions"></a>Koşullu erişim: koşullar

Bir yönetici, koşullu erişim ilkesinde, ilke kararlarını iyileştirmek için risk, cihaz platformu veya konum gibi koşullardan gelen sinyalleri kullanabilir. 

[![Koşullu erişim ilkesi tanımlama ve koşulları belirtme](./media/concept-conditional-access-conditions/conditional-access-conditions.png)](./media/concept-conditional-access-conditions/conditional-access-conditions.png#lightbox)

Ayrıntılı ve belirli koşullu erişim ilkeleri oluşturmak için birden çok koşul birleştirilebilir.

Örneğin, hassas bir uygulamaya erişirken, yönetici, kimlik koruması ve konumdan çok faktörlü kimlik doğrulaması gibi diğer denetimlere ek olarak erişim kararlarına yönelik oturum açma risk bilgilerini çarpanlara katmayabilir.

## <a name="sign-in-risk"></a>Oturum açma riski

[Kimlik korumasına](../identity-protection/overview-identity-protection.md)erişimi olan müşteriler için, oturum açma riski koşullu erişim ilkesinin bir parçası olarak değerlendirilebilirler. Oturum açma riski, belirli bir kimlik doğrulama isteğinin kimlik sahibi tarafından yetkilendirilmemiş olma olasılığını temsil eder. Makalelerde oturum [açma riski hakkında](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) daha fazla bilgi bulabilirsiniz, risk ve [nasıl yapılır: risk ilkelerini yapılandırma ve etkinleştirme](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="user-risk"></a>Kullanıcı riski 

[Kimlik korumasına](../identity-protection/overview-identity-protection.md)erişimi olan müşteriler için, Kullanıcı riski koşullu erişim ilkesinin bir parçası olarak değerlendirilebilirler. Kullanıcı riski, belirli bir kimliğin veya hesabın güvenliğinin aşıldığına ilişkin olasılığı temsil eder. Makalelerde Kullanıcı riski hakkında daha fazla bilgi [bulabilirsiniz, risk](../identity-protection/concept-identity-protection-risks.md#user-risk) ve [nasıl yapılır: risk ilkelerini yapılandırma ve etkinleştirme](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Cihaz platformları

Cihaz platformu, bir cihazda çalışan işletim sistemine göre belirlenir. Azure AD, cihaz tarafından sunulan Kullanıcı Aracısı dizeleri gibi bilgileri kullanarak platformu tanımlar. Kullanıcı Aracısı dizeleri değiştirilebilen için bu bilgiler doğrulanmamış olur. Cihaz platformu Microsoft Intune cihaz uyumluluk ilkeleriyle veya bir blok bildiriminin parçası olarak konser 'da kullanılmalıdır. Varsayılan değer tüm cihaz platformları için geçerlidir.

Azure AD koşullu erişim aşağıdaki cihaz platformlarını destekler:

- Android
- iOS
- Windows Phone
- Windows
- Mac OS

**Diğer istemciler** koşulunu kullanarak eski kimlik doğrulamasını engellerseniz, cihaz platformu koşulunu da ayarlayabilirsiniz.

> [!IMPORTANT]
> Microsoft, desteklenmeyen cihaz platformları için bir koşullu erişim ilkeniz olmasını önerir. Örnek olarak, Linux veya diğer desteklenmeyen istemcilerden şirket kaynaklarınıza erişimi engellemek istiyorsanız, herhangi bir cihazı içeren ve desteklenen cihaz platformlarını dışlayan ve denetim erişimi engelleyecek şekilde ayarlanmış bir cihaz platformları durumuyla bir ilke yapılandırmanız gerekir.

## <a name="locations"></a>Konumlar

Konum bir koşul olarak yapılandırılırken, kuruluşlar konumları dahil etmek veya hariç tutmak seçebilirler. Bu adlandırılmış konumlar, genel IPv4 ağ bilgilerini, ülkeyi veya bölgeyi, hatta belirli ülkelere veya bölgelere eşlenmeyen bilinmeyen alanları içerebilir. Yalnızca IP aralıkları güvenilir bir konum olarak işaretlenebilir.

**Herhangi bir konum** dahil edildiğinde, bu seçenek yalnızca adlandırılmış konumlar yapılandırılmamış Internet üzerindeki IP adreslerini içerir. **Herhangi bir konum** seçerken, Yöneticiler **tüm güvenilen** veya **Seçilen konumların** dışlanmasını seçebilirler.

Örneğin, bazı kuruluşlar, kullanıcıları ağa fiziksel Merkez gibi güvenilir bir konumda bağlıyken çok faktörlü kimlik doğrulaması gerektirmeyebilir. Yöneticiler herhangi bir konum içeren bir ilke oluşturabilir, ancak bu kişilerin Merkez ağları için seçili konumları dışlar.

Konumlar hakkında daha fazla bilgi için, [Azure Active Directory Koşullu erişim içindeki konum koşulunun ne olduğunu](location-condition.md)bulabilirsiniz.

## <a name="client-apps"></a>İstemci uygulamaları

Varsayılan olarak, tüm yeni oluşturulan koşullu erişim ilkeleri, istemci uygulamaları koşulu yapılandırılmamışsa bile tüm istemci uygulama türleri için geçerlidir. 

> [!NOTE]
> İstemci uygulamaları koşulunun davranışı, Ağustos 2020 ' de güncelleştirildi. Koşullu erişim ilkelerinize sahipseniz, bunlar değişmeden kalır. Ancak, var olan bir ilkeye tıkladığınızda, yapılandırma geçişi kaldırılmıştır ve ilke geçerli olan istemci uygulamaları seçilir.

> [!IMPORTANT]
> Eski kimlik doğrulama istemcilerinden yapılan oturum açma işlemleri MFA 'yı desteklemez ve cihaz durum bilgilerini Azure AD 'ye geçirmez, bu nedenle MFA veya uyumlu cihazlar gerektirme gibi koşullu erişim izni denetimleri tarafından engellenecektir. Eski kimlik doğrulamasını kullanması gereken hesaplarınız varsa, bu hesapları ilkeden hariç tutmalısınız veya ilkeyi yalnızca modern kimlik doğrulama istemcilerine uygulanacak şekilde yapılandırmalısınız.

**Evet** olarak ayarlandığında **yapılandırma** geçişi, işaretlenmiş öğeler için geçerli olduğunda, modern ve eski kimlik doğrulama istemcileri de dahil **olmak üzere tüm** istemci uygulamalarına uygulanmaz. Bu geçiş, 2020 Ağustos 'Tan önce oluşturulan ilkelerde görünmez.

- Modern kimlik doğrulama istemcileri
   - Tarayıcı
      - Bunlar SAML, WS-Federation, OpenID Connect gibi protokolleri kullanan Web tabanlı uygulamaları veya OAuth gizli istemci olarak kaydedilmiş hizmetleri içerir.
   - Mobil uygulamalar ve Masaüstü istemcileri
      -  Bu seçenek, Office Masaüstü ve telefon uygulamaları gibi uygulamaları içerir.
- Eski kimlik doğrulama istemcileri
   - Exchange ActiveSync istemcileri
      - Bu, Exchange ActiveSync (EAS) protokolünün tüm kullanımını içerir.
      - İlke Exchange ActiveSync kullanımını engellediğinde, etkilenen Kullanıcı tek bir karantina e-postası alır. Bu e-posta, neden engellendikleri hakkında bilgi sağlar ve mümkünse düzeltme yönergelerini içerir.
      - Yöneticiler, koşullu erişim MS Graph API aracılığıyla yalnızca desteklenen platformlara (iOS, Android ve Windows) ilke uygulayabilir.
   - Diğer istemciler
      - Bu seçenek, modern kimlik doğrulamasını desteklemeyen temel/eski kimlik doğrulama protokollerini kullanan istemcileri içerir.
         - Kimliği doğrulanmış SMTP-e-posta iletileri göndermek için POP ve IMAP istemci tarafından kullanılır.
         - Otomatik bulma-Exchange Online 'da posta kutularını bulmak ve bağlamak için Outlook ve EAS istemcileri tarafından kullanılır.
         - Exchange Online PowerShell-uzak PowerShell ile Exchange Online 'a bağlanmak için kullanılır. Exchange Online PowerShell için temel kimlik doğrulamasını engellerseniz, bağlanmak için Exchange Online PowerShell modülünü kullanmanız gerekir. Yönergeler için bkz. [Multi-Factor Authentication kullanarak Exchange Online PowerShell 'e bağlanma](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Exchange Web Hizmetleri (EWS)-Outlook, Mac için Outlook ve üçüncü taraf uygulamalar tarafından kullanılan bir programlama arabirimidir.
         - IMAP4-IMAP e-posta istemcileri tarafından kullanılır.
         - HTTP üzerinden MAPI (MAPI/HTTP)-Outlook 2010 ve üzeri tarafından kullanılır.
         - Çevrimdışı adres defteri (OAB)-Outlook tarafından indirilen ve kullanılan adres listesi koleksiyonlarının bir kopyası.
         - Outlook her yerde (HTTP üzerinden RPC)-Outlook 2016 ve öncesi tarafından kullanılır.
         - Outlook hizmeti-Windows 10 için posta ve takvim uygulaması tarafından kullanılır.
         - POP3-POP e-posta istemcileri tarafından kullanılır.
         - Raporlama Web Hizmetleri-Exchange Online 'daki rapor verilerini almak için kullanılır.

Bu koşullar genellikle yönetilen bir cihaz gerektirdiğinde, eski kimlik doğrulamasını engelleyerek ve mobil veya masaüstü uygulamalarına izin veren bir şekilde kullanılır.

### <a name="supported-browsers"></a>Desteklenen tarayıcılar

Bu ayar tüm tarayıcılarla birlikte kullanılabilir. Bununla birlikte, uyumlu bir cihaz gereksinimi gibi bir cihaz ilkesini karşılamak için, aşağıdaki işletim sistemleri ve tarayıcılar desteklenir:

| İşletim Sistemi | Browsers (Tarayıcılar) |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8/8,1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| Mac OS | Chrome, Safari |

> [!NOTE]
> Edge 85 +, cihazın kimliğini düzgün bir şekilde geçirmek için kullanıcının tarayıcıda oturum açabilmesi gerekir. Aksi takdirde, hesaplar uzantısı olmadan Chrome gibi davranır. Bu oturum açma, karma Azure AD JOIN senaryosunda otomatik olarak gerçekleşmeyebilir. 

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Neden tarayıcıda bir sertifika istemi görüyorum?

Windows 7, iOS, Android ve macOS Azure AD, cihaz Azure AD 'ye kaydedildiğinde sağlanan bir istemci sertifikası kullanarak cihazı tanımlar.  Kullanıcı tarayıcıda ilk kez oturum açtığında, kullanıcıdan sertifikayı seçmesi istenir. Kullanıcının tarayıcıyı kullanmadan önce bu sertifikayı seçmeniz gerekir.

#### <a name="chrome-support"></a>Chrome desteği

**Windows 10 Creators Update (sürüm 1703)** veya sonraki sürümlerde Chrome desteği için [Windows 10 hesapları uzantısını](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)yükler. Koşullu erişim ilkesi cihaza özgü ayrıntılar gerektirdiğinde bu uzantı gereklidir.

Bu uzantıyı Chrome tarayıcılarına otomatik olarak dağıtmak için aşağıdaki kayıt defteri anahtarını oluşturun:

- Yol HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist
- Ad 1
- Tür REG_SZ (dize)
- Veri ppnbnpeolgkicgegkbkbjmhlideopiji; https \: //clients2.Google.com/service/update2/crx

**Windows 8.1 ve 7**' de Chrome desteği için aşağıdaki kayıt defteri anahtarını oluşturun:

- Yol HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls
- Ad 1
- Tür REG_SZ (dize)
- Veri {"model": " https://device.login.microsoftonline.com ", "filtre": {"veren": {"CN": "MS-Organization-Access"}}}

Bu tarayıcılar cihaz kimlik doğrulamasını destekler, bu da cihazın bir ilkeye göre tanımlanmasına ve doğrulanmasını sağlar. Tarayıcı özel modda çalışıyorsa cihaz denetimi başarısız olur.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Desteklenen mobil uygulamalar ve Masaüstü istemcileri

Kuruluşlar, **mobil uygulamaları ve Masaüstü istemcileri** istemci uygulaması olarak seçebilir.

Bu ayar, aşağıdaki mobil uygulamalardan ve Masaüstü istemcilerinden yapılan erişim girişimlerini etkiler:

| İstemci uygulamaları | Hedef hizmet | Platform |
| --- | --- | --- |
| Dynamics CRM uygulaması | Dynamics CRM | Windows 10, Windows 8.1, iOS ve Android |
| Posta/Takvim/Kişiler uygulaması, Outlook 2016, Outlook 2013 (modern kimlik doğrulaması ile)| Exchange Online | Windows 10 |
| Uygulamalar için MFA ve konum ilkesi. Cihaz tabanlı ilkeler desteklenmez.| Tüm Apps App Service | Android ve iOS |
| Microsoft ekipleri Hizmetleri-bu, Microsoft ekiplerini ve tüm Istemci uygulamalarını (Windows Masaüstü, iOS, Android, WP ve Web istemcisi) destekleyen tüm hizmetleri denetler | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android ve macOS |
| Office 2016 uygulamaları, Office 2013 (modern kimlik doğrulaması ile), [OneDrive eşitleme istemcisi](/onedrive/enable-conditional-access) | SharePoint | Windows 8.1, Windows 7 |
| Office 2016 uygulamaları, evrensel Office uygulamaları, Office 2013 (modern kimlik doğrulaması ile), [OneDrive eşitleme istemcisi](/onedrive/enable-conditional-access) | SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, yalnızca OneNote). | SharePoint | Mac OS |
| Office 2019| SharePoint | Windows 10, macOS |
| Office mobil uygulamaları | SharePoint | Android, iOS |
| Office Yammer uygulaması | Yammer | Windows 10, iOS, Android |
| Outlook 2019 | SharePoint | Windows 10, macOS |
| Outlook 2016 (macOS için Office) | Exchange Online | Mac OS |
| Outlook 2016, Outlook 2013 (modern kimlik doğrulaması ile), Skype Kurumsal (modern kimlik doğrulaması ile) | Exchange Online | Windows 8.1, Windows 7 |
| Outlook mobil uygulaması | Exchange Online | Android, iOS |
| Power BI uygulaması | Power BI hizmeti | Windows 10, Windows 8.1, Windows 7, Android ve iOS |
| Skype Kurumsal | Exchange Online| Android, iOS |
| Visual Studio Team Services uygulaması | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS ve Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync istemcileri

- Kuruluşlar kullanıcılara veya gruplara ilke atarken yalnızca Exchange ActiveSync istemcileri seçebilir. Tüm **Kullanıcılar**, tüm **Konuk ve dış kullanıcılar** ya da **Dizin rolleri** seçildiğinde, tüm kullanıcıların ilke konusu olmasına neden olur.
- Exchange ActiveSync istemcilerine atanan bir ilke oluştururken, **Exchange Online** , ilkeye atanmış tek bulut uygulaması olmalıdır. 
- Kuruluşlar, cihaz platformları koşulunu kullanarak bu ilkenin kapsamını belirli platformlara **daraltabilirler** .

İlkeye atanan erişim denetimi **onaylanan istemci uygulaması gerektir**' i kullanıyorsa, Kullanıcı Outlook Mobil istemcisini yüklemek ve kullanmak üzere yönlendirilir. **Multi-Factor Authentication**, **kullanım koşulları** veya **Özel denetimlerin** gerekli olduğu durumlarda, temel kimlik doğrulaması bu denetimleri desteklemediğinden, etkilenen kullanıcılar engellenir.

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Koşullu erişimle eski kimlik doğrulamasını engelleyin](block-legacy-authentication.md)
- [Koşullu erişim ile onaylanan istemci uygulamaları gerektirme](app-based-conditional-access.md)

### <a name="other-clients"></a>Diğer istemciler

**Diğer istemcileri** seçerek, modern kimlik doğrulaması kullanmayan IMAP, MAPI, pop, SMTP ve eski Office uygulamaları gibi posta protokolleriyle temel kimlik doğrulaması kullanan uygulamaları etkileyen bir koşul belirtebilirsiniz.

## <a name="device-state-preview"></a>Cihaz durumu (Önizleme)

Cihaz durumu koşulu, karma Azure AD 'ye katılmış ve/veya cihazların bir kuruluşun koşullu erişim ilkelerinden bir Microsoft Intune uyumluluk ilkesiyle uyumlu olarak işaretlenmiş cihazları dışlamak için kullanılabilir.

Örneğin, **cihazın karma Azure AD 'ye katılmış** ve **uyumlu olarak işaretlenmiş** cihaz **durumu** dahil olmak üzere *Microsoft Azure yönetimi* bulut uygulamasına erişen *tüm kullanıcılar* ve *erişim denetimleri* için **bloğu**. 
   - Bu örnek, karma Azure AD 'ye katılmış veya uyumlu olarak işaretlenmiş cihazların Microsoft Azure yönetimine yalnızca erişim sağlayan bir ilke oluşturur.

## <a name="next-steps"></a>Sonraki adımlar

- [Koşullu erişim: ver](concept-conditional-access-grant.md)

- [Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)
