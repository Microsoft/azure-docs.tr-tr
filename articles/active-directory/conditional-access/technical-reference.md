---
title: Koşullu erişim ayarları başvurusunu Azure Active Directory | Microsoft Docs
description: Azure Active Directory Koşullu erişim ilkesindeki desteklenen ayarlara genel bakış alın.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: reference
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a3518dfcad3678dc298ba8529e731d48ec1d195
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893469"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Azure Active Directory Koşullu erişim ayarları başvurusu

Yetkili kullanıcıların kaynaklarınıza nasıl erişebileceğini denetlemek için [Azure Active Directory (Azure AD) koşullu erişim](../active-directory-conditional-access-azure-portal.md) ' i kullanabilirsiniz.

Bu makale, bir koşullu erişim ilkesinde aşağıdaki yapılandırma seçenekleri için destek bilgileri sağlar:

- Bulut uygulamaları atamaları
- Cihaz platformu koşulu
- İstemci uygulamaları koşulu
- Onaylanan istemci uygulaması gereksinimi

Aradığınız bilgiler bu değilse, lütfen bu makalenin sonunda bir yorum bırakın.

## <a name="cloud-apps-assignments"></a>Bulut uygulamaları atamaları

Koşullu erişim ilkeleriyle, kullanıcılarınızın [bulut uygulamalarınıza](conditions.md#cloud-apps-and-actions)nasıl erişebileceğini kontrol edersiniz. Bir koşullu erişim ilkesi yapılandırdığınızda, en az bir bulut uygulaması seçmeniz gerekir. 

![İlkenize yönelik bulut uygulamalarını seçin](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Microsoft bulut uygulamaları

Microsoft 'tan aşağıdaki bulut uygulamalarına bir koşullu erişim ilkesi atayabilirsiniz:

- Azure Analysis Services
- Azure DevOps
- Azure SQL veritabanı ve veri ambarı- [daha fazla bilgi](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- Microsoft Azure Information Protection- [daha fazla bilgi](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Microsoft Azure Yönetimi- [daha fazla bilgi](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure abonelik yönetimi
- Microsoft Cloud App Security
- Microsoft ticaret araçları Access Control portalı
- Microsoft ticaret araçları kimlik doğrulama hizmeti
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Kayıt Microsoft Intune
- Microsoft Planner
- Microsoft PowerApps
- Bing 'de Microsoft Search
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI hizmeti
- Project Online
- Skype Kurumsal Çevrimiçi Sürüm
- Sanal özel ağ (VPN)
- Windows Defender ATP

### <a name="other-applications"></a>Diğer uygulamalar

Microsoft bulut uygulamalarına ek olarak, aşağıdaki bulut uygulamaları türlerine bir koşullu erişim ilkesi de atayabilirsiniz:

- Azure AD ile bağlantılı uygulamalar
- Önceden tümleştirilmiş Federasyon yazılım hizmeti (SaaS) uygulaması
- Parola çoklu oturum açma (SSO) kullanan uygulamalar
- İş kolu uygulamaları
- Azure AD Uygulama Ara Sunucusu kullanan uygulamalar

## <a name="device-platform-condition"></a>Cihaz platformu koşulu

Koşullu erişim ilkesinde, cihaz platformu koşulunu, ilkeyi bir istemcideki işletim sistemine bağlamak için yapılandırabilirsiniz. Azure AD koşullu erişim aşağıdaki cihaz platformlarını destekler:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Erişim ilkesini istemci işletim sistemine bağlama](./media/technical-reference/41.png)

**Diğer istemciler** koşulunu kullanarak eski kimlik doğrulamasını engellerseniz, cihaz platformu koşulunu da ayarlayabilirsiniz.

## <a name="client-apps-condition"></a>İstemci uygulamaları koşulu

Koşullu erişim ilkenizde, bir erişim denemesi Başlatan istemci uygulamasına ilkeyi bağlamak için [istemci uygulamaları](conditions.md#client-apps) koşulunu yapılandırabilirsiniz. Aşağıdaki istemci uygulamaları türlerinden bir erişim denemesi yapıldığında, istemci uygulamaları koşulunu erişim izni vermek veya erişimi engellemek için ayarlayın:

- Tarayıcı
- Mobil uygulamalar ve Masaüstü uygulamaları

![İstemci uygulamaları için erişimi denetleme](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Desteklenen tarayıcılar

Koşullu erişim ilkenizde **tarayıcıları** istemci uygulaması olarak seçebilirsiniz.

![Desteklenen tarayıcılar için erişimi denetleme](./media/technical-reference/05.png)

Bu ayar tüm tarayıcılarla birlikte kullanılabilir. Bununla birlikte, uyumlu bir cihaz gereksinimi gibi bir cihaz ilkesini karşılamak için, aşağıdaki işletim sistemleri ve tarayıcılar desteklenir:

| İşletim Sistemi                     | Tarayıcısında                                        |
| :--                    | :--                                             |
| Windows 10             | Microsoft Edge, Internet Explorer, Chrome       |
| Windows 8/8,1        | Internet Explorer, Chrome                       |
| Windows 7              | Internet Explorer, Chrome                       |
| iOS                    | Microsoft Edge, Intune Managed Browser, Safari  |
| Android                | Microsoft Edge, Intune Managed Browser, Chrome  |
| Windows Phone          | Microsoft Edge, Internet Explorer               |
| Windows Server 2019    | Microsoft Edge, Internet Explorer, Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| macOS                  | Chrome, Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Neden tarayıcıda bir sertifika istemi görüyorum?

Windows 7, iOS, Android ve macOS Azure AD, cihaz Azure AD 'ye kaydedildiğinde sağlanan bir istemci sertifikası kullanarak cihazı tanımlar.  Kullanıcı tarayıcıda ilk kez oturum açtığında, kullanıcıdan sertifikayı seçmesi istenir. Kullanıcının tarayıcıyı kullanmadan önce bu sertifikayı seçmeniz gerekir.

#### <a name="chrome-support"></a>Chrome desteği

**Windows 10 Creators Update (sürüm 1703)** veya sonraki sürümlerde Chrome desteği için [Windows 10 hesapları uzantısını](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)yükler. Koşullu erişim ilkesi cihaza özgü ayrıntılar gerektirdiğinde bu uzantı gereklidir.

Bu uzantıyı Chrome tarayıcılarına otomatik olarak dağıtmak için aşağıdaki kayıt defteri anahtarını oluşturun:

|    |    |
| --- | --- |
| Yol | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Adı | 1 |
| Tür | REG_SZ (dize) |
| Veriler | ppnbnpeolgkicgegkbkbjmhlideopiji; https\://clients2.google.com/service/update2/crx |

**Windows 8.1 ve 7**' de Chrome desteği için aşağıdaki kayıt defteri anahtarını oluşturun:

|    |    |
| --- | --- |
| Yol | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Adı | 1 |
| Tür | REG_SZ (dize) |
| Veriler | {"model": "https://device.login.microsoftonline.com", "Filter": {"ıSSUER": {"CN": "MS-Organization-Access"}}} |

Bu tarayıcılar cihaz kimlik doğrulamasını destekler, bu da cihazın bir ilkeye göre tanımlanmasına ve doğrulanmasını sağlar. Tarayıcı özel modda çalışıyorsa cihaz denetimi başarısız olur.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Desteklenen mobil uygulamalar ve Masaüstü istemcileri

Koşullu erişim ilkenizde, **mobil uygulamalar ve Masaüstü istemcileri** istemci uygulaması olarak seçebilirsiniz.

![Desteklenen mobil uygulamalar veya masaüstü istemcileri için erişimi denetleme](./media/technical-reference/06.png)

Bu ayar, aşağıdaki mobil uygulamalardan ve Masaüstü istemcilerinden yapılan erişim girişimlerini etkiler:

| İstemci uygulamaları | Hedef hizmet | Platform |
| --- | --- | --- |
| Dynamics CRM uygulaması | Dynamics CRM | Windows 10, Windows 8.1, iOS ve Android |
| Posta/Takvim/Kişiler uygulaması, Outlook 2016, Outlook 2013 (modern kimlik doğrulaması ile)| Office 365 Exchange Online | Windows 10 |
| Uygulamalar için MFA ve konum ilkesi. Cihaz tabanlı ilkeler desteklenmez.| Tüm Apps App Service| Android ve iOS |
| Microsoft ekipleri Hizmetleri-bu, Microsoft ekiplerini ve tüm Istemci uygulamalarını (Windows Masaüstü, iOS, Android, WP ve Web istemcisi) destekleyen tüm hizmetleri denetler | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android ve macOS |
| Office 2016 uygulamaları, Office 2013 (modern kimlik doğrulaması ile), OneDrive eşitleme istemcisi (bkz. [Notlar](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Office 2016 uygulamaları, evrensel Office uygulamaları, Office 2013 (modern kimlik doğrulaması ile), OneDrive eşitleme istemcisi (bkz. [Notlar](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), Office grupları desteği gelecek için planlanmaktadır ve gelecekte SharePoint uygulama desteği planlanmaktadır | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, yalnızca OneNote). Geleceğe yönelik OneDrive Iş desteği| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Office mobil uygulamaları | Office 365 SharePoint Online | Android, iOS |
| Office Yammer uygulaması | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (macOS için Office) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (modern kimlik doğrulaması ile), Skype Kurumsal (modern kimlik doğrulaması ile) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Outlook mobil uygulaması | Office 365 Exchange Online | Android, iOS |
| Power BI uygulaması | Power BI hizmeti | Windows 10, Windows 8.1, Windows 7, Android ve iOS |
| Skype Kurumsal | Office 365 Exchange Online| Android, IOS |
| Visual Studio Team Services uygulaması | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS ve Android |

## <a name="support-for-legacy-authentication"></a>Eski kimlik doğrulama desteği

**Diğer istemcileri**seçerek, modern kimlik doğrulaması kullanmayan IMAP, MAPI, pop, SMTP ve eski Office uygulamaları gibi posta protokolleriyle temel kimlik doğrulaması kullanan uygulamaları etkileyen bir koşul belirtebilirsiniz.  

![Diğer istemciler](./media/technical-reference/11.png)

Daha fazla bilgi için bkz. [istemci uygulamaları](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Onaylanan istemci uygulaması gereksinimi

Koşullu erişim ilkenizde, seçili bulut uygulamalarına yönelik bir erişim denemesinin onaylanan bir istemci uygulamasından yapılması gerekir. 

![Onaylanan istemci uygulamaları için erişimi denetleme](./media/technical-reference/21.png)

Bu ayar, aşağıdaki istemci uygulamaları için geçerlidir:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype Kurumsal
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Açıklamalar**

- Onaylanan istemci uygulamaları, Intune mobil uygulama yönetimi özelliğini destekler.
- **Onaylanan istemci uygulaması gereksinimini gerektir** :
   - Yalnızca iOS ve Android for [Device platform koşulunu](#device-platform-condition)destekler.

## <a name="app-protection-policy-requirement"></a>Uygulama koruma ilkesi gereksinimi 

Koşullu erişim ilkenizde, seçili bulut uygulamalarına erişim izni vermeden önce istemci uygulamada bir uygulama koruma ilkesinin mevcut olmasını zorunlu kılabilirsiniz. 

![Uygulama koruma ilkesiyle erişimi denetleme](./media/technical-reference/22.png)

Bu ayar, aşağıdaki istemci uygulamaları için geçerlidir:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Açıklamalar**

- Uygulama koruma ilkesi için uygulamalar, ilke korumasıyla Intune mobil uygulama yönetimi özelliğini destekler.
- **Uygulama koruma ilkesi gereksinimlerini gerektir** :
    - Yalnızca iOS ve Android for [Device platform koşulunu](#device-platform-condition)destekler.

## <a name="next-steps"></a>Sonraki adımlar

- Koşullu erişime genel bakış için bkz. [Azure Active Directory Koşullu erişim nedir?](../active-directory-conditional-access-azure-portal.md)
- Ortamınızda koşullu erişim ilkelerini yapılandırmaya hazırsanız, [Azure Active Directory Koşullu erişim için önerilen uygulamalara](best-practices.md)bakın.

<!--Image references-->
[1]: ./media/technical-reference/01.png
