---
title: Koşullu erişim ilkesindeki bulut uygulamaları veya eylemleri-Azure Active Directory
description: Azure AD koşullu erişim ilkesinde bulut uygulamaları veya eylemleri nelerdir?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80ec5133ad12dda4a6883c663007b8b7fec2e81a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551943"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Koşullu erişim: bulut uygulamaları veya eylemleri

Bulut uygulamaları veya eylemleri, koşullu erişim ilkesindeki önemli sinyaldir. Koşullu erişim ilkeleri, yöneticilerin belirli uygulamalara veya eylemlere denetim atamasını sağlar.

- Yöneticiler, yerleşik Microsoft uygulamaları ve Galeri, Galeri olmayan uygulamalar ve [uygulama proxy 'si](../manage-apps/what-is-application-proxy.md)aracılığıyla yayınlanan uygulamalar dahil olmak üzere [Azure AD ile tümleştirilmiş](../manage-apps/what-is-application-management.md) uygulamalar içeren uygulamalar listesinden seçim yapabilir.
- Yöneticiler, bir bulut uygulamasına göre değil, bir Kullanıcı eyleminde ilke tanımlamaya seçim gösterebilir. İki kullanıcı eylemini destekliyoruz
   - [Birleşik güvenlik bilgileri kayıt deneyimi](../authentication/howto-registration-mfa-sspr-combined.md) etrafında denetimleri zorlamak için güvenlik bilgilerini (Önizleme) kaydedin 
   - [Kullanıcılar cihazları kaydederken veya](../devices/concept-azure-ad-register.md) Azure AD 'ye eklerken denetimleri zorlamak için cihazları [kaydetme veya katma](../devices/concept-azure-ad-join.md) (Önizleme). 

![Koşullu erişim ilkesi tanımlama ve bulut uygulamaları belirtme](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft bulut uygulamaları

Mevcut Microsoft bulut uygulamalarının birçoğu, arasından seçim yapmak için kullanabileceğiniz uygulamalar listesine dahildir. 

Yöneticiler, Microsoft 'tan aşağıdaki bulut uygulamalarına koşullu erişim ilkesi atayabilir. Office 365 ve Microsoft Azure yönetimi gibi bazı uygulamalar birden çok ilgili alt uygulama veya hizmet içerir. Sürekli olarak daha fazla uygulama ekleyeceğiz, bu nedenle aşağıdaki liste ayrıntılı değildir ve değişikliğe tabidir.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL veritabanı ve Azure SYNAPSE Analytics](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure yönetimi](#microsoft-azure-management)
- Microsoft Azure abonelik yönetimi
- Microsoft Cloud App Security
- Microsoft ticaret araçları Access Control portalı
- Microsoft ticaret araçları kimlik doğrulama hizmeti
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Kayıt Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Bing 'de Microsoft Search
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI Hizmeti
- Project Online
- Skype Kurumsal Çevrimiçi Sürüm
- Sanal özel ağ (VPN)
- Windows Defender ATP

Koşullu erişim için kullanılabilen uygulamalar, bir ekleme ve doğrulama işleminden geçer. Bu, arka uç hizmetleri olmak üzere tüm Microsoft uygulamalarını içermez ve ilkenin doğrudan uygulanmasına izin vermez. Eksik bir uygulama arıyorsanız, belirli uygulama takımına başvurabilirsiniz veya [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=167259)üzerinde bir istek yapabilirsiniz.

### <a name="office-365"></a>Office 365

Microsoft 365, Exchange, SharePoint ve Microsoft ekipleri gibi bulut tabanlı üretkenlik ve işbirliği hizmetleri sağlar. Microsoft 365 Bulut Hizmetleri, sorunsuz ve işbirliğine dayalı deneyimler sağlamak için derin bir tümleştirilmiştir. Bu tümleştirme, Microsoft ekipleri gibi bazı uygulamaların SharePoint veya Exchange gibi diğer kullanıcılara bağımlılıkları olduğundan, ilke oluştururken karışıklıklara neden olabilir.

Office 365 uygulaması bu hizmetleri tek seferde hedeflemesini mümkün kılar. [Hizmet bağımlılıklarıyla](service-dependencies.md)ilgili sorunları önlemek için tek bulut uygulamalarını hedeflemek yerine yeni Office 365 uygulamasını kullanmanızı öneririz. Bu uygulama grubunun hedeflenmesi, tutarsız ilkeler ve bağımlılıklar nedeniyle ortaya çıkabilecek sorunlardan kaçınmaya yardımcı olur.

Yöneticiler, Office 365 uygulamasını dahil etmek ve seçtikleri belirli uygulamaları hariç tutmak istediklerinde belirli uygulamaları ilkeden dışlamalarını seçebilirler.

Office 365 istemci uygulamasında bulunan önemli uygulamalar:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Microsoft 365 Arama Hizmeti
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype Kurumsal Çevrimiçi Sürüm
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure yönetimi

Microsoft Azure yönetim uygulaması birden çok temel hizmet içerir. 

   - Azure portalı
   - Azure Resource Manager sağlayıcı
   - Klasik dağıtım modeli API 'Leri
   - Azure PowerShell
   - Azure CLI
   - Visual Studio abonelikleri Yönetici portalı
   - Azure DevOps
   - Azure Data Factory portalı

> [!NOTE]
> Microsoft Azure yönetim uygulaması, Azure Resource Manager API 'sini çağıran Azure PowerShell için geçerlidir. Microsoft Graph çağıran Azure AD PowerShell için de geçerlidir.

## <a name="other-applications"></a>Diğer uygulamalar

Yöneticiler, Microsoft uygulamalarına ek olarak, koşullu erişim ilkelerine herhangi bir Azure AD kayıtlı uygulaması ekleyebilir. Bu uygulamalar şunlar olabilir: 

- [Azure AD uygulama ara sunucusu](../manage-apps/what-is-application-proxy.md) aracılığıyla yayımlanan uygulamalar
- [Galeriden eklenen uygulamalar](../manage-apps/add-application-portal.md)
- [Galeride bulunmayan özel uygulamalar](../manage-apps/view-applications-portal.md)
- [Uygulama teslim denetleyicileri ve ağları aracılığıyla yayınlanan eski uygulamalar](../manage-apps/secure-hybrid-access.md)
- [Parola tabanlı çoklu oturum açma](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md) kullanan uygulamalar

> [!NOTE]
> Koşullu erişim ilkesi bir hizmete erişim gereksinimlerini ayarladığından, bir istemci (genel/yerel) uygulamasına uygulayamazsınız. Diğer bir deyişle, ilke doğrudan istemci (ortak/yerel) uygulama üzerinde ayarlanmamış ancak istemci bir hizmet çağırdığında uygulanır. Örneğin, SharePoint hizmetinde ayarlanan bir ilke, SharePoint 'i çağıran istemciler için geçerlidir. Exchange üzerinde ayarlanan bir ilke, e-postaya Outlook istemcisi kullanılarak erişme girişimi için geçerlidir. İstemci (genel/yerel) uygulamalarının bulut uygulamaları seçicisinden seçilemediği ve koşullu erişim seçeneğinin, kiracınızda kayıtlı olan istemci (genel/yerel) uygulamanın uygulama ayarlarında kullanılamaz olması bu budur. 

## <a name="user-actions"></a>Kullanıcı eylemleri

Kullanıcı eylemleri, bir kullanıcı tarafından gerçekleştirilebilecek görevlerdir. Şu anda koşullu erişim iki kullanıcı eylemini destekler: 

- **Güvenlik bilgilerini kaydet**: bu kullanıcı eylemi, Birleşik kayıt için etkinleştirilen kullanıcılar güvenlik bilgilerini kaydetmeye çalışırken koşullu erişim ilkesinin uygulanmasını sağlar. Daha fazla bilgi için, [Birleşik güvenlik bilgileri kaydı](../authentication/concept-registration-mfa-sspr-combined.md)başlıklı makaleye ulaşabilirsiniz.

- **Cihazları Kaydet veya birleştir (Önizleme)**: bu kullanıcı eylemi, [Kullanıcılar cihazları](../devices/concept-azure-ad-join.md) [kaydederken](../devices/concept-azure-ad-register.md) veya Azure AD 'ye eklerken koşullu erişim ilkesi uygulanmasını sağlar. Mevcut olan kiracı genelindeki bir ilke yerine, cihazları kaydetmek veya birleştirmek için çok faktörlü kimlik doğrulamasını yapılandırma konusunda ayrıntı düzeyi sağlar. Bu kullanıcı eylemiyle ilgili üç önemli şey vardır: 
   - `Require multi-factor authentication` Bu kullanıcı eylemiyle kullanılabilen tek erişim denetimi ve diğerleri devre dışı bırakıldı. Bu kısıtlama, Azure AD cihaz kaydına bağlı olan veya Azure AD cihaz kaydı için geçerli olmayan erişim denetimleriyle ilgili çakışmaları engeller. 
   - `Client apps` ve `Device state` koşullar, koşullu erişim ilkelerini zorlamak üzere Azure AD cihaz kaydına bağımlı olduklarından bu kullanıcı eylemiyle birlikte kullanılamaz.
   - Bu kullanıcı eylemiyle bir koşullu erişim ilkesi etkinleştirildiğinde, **Azure Active Directory**  >  **cihazları**  >  **cihaz ayarlarını**  -  `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` **Hayır** olarak ayarlamanız gerekir. Aksi takdirde, bu kullanıcı eylemiyle koşullu erişim ilkesi düzgün zorlanmaz. Bu cihaz ayarıyla ilgili daha fazla bilgiyi [cihaz ayarlarını yapılandırma](../devices/device-management-azure-portal.md#configure-device-settings)bölümünde bulabilirsiniz. 
   
## <a name="next-steps"></a>Sonraki adımlar

- [Koşullu erişim: koşullar](concept-conditional-access-conditions.md)

- [Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)
- [İstemci uygulaması bağımlılıkları](service-dependencies.md)
