---
title: 'Azure AD Connect: sorunsuz tek Sign-On-sık sorulan sorular | Microsoft Docs'
description: Azure Active Directory sorunsuz çoklu oturum açma hakkında sık sorulan soruların yanıtları.
services: active-directory
keywords: Azure AD Connect nedir, yükler Active Directory, Azure AD, SSO, çoklu oturum açma için gerekli bileşenler
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87b9371bfe3b0bd8ff8b3bd509cc1431f0c2e1c6
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305778"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Kesintisiz çoklu oturum açma Azure Active Directory: sık sorulan sorular

Bu makalede, Azure Active Directory kesintisiz tek Sign-On (sorunsuz SSO) hakkında sık sorulan soruları ele aldık. Yeni içerik için yeniden kullanıma devam edin.

**S: hangi oturum açma yöntemlerinin sorunsuz SSO iş yaptığını**

Kesintisiz SSO, [Parola karması eşitlemesi](how-to-connect-password-hash-synchronization.md) veya [geçişli kimlik doğrulaması](how-to-connect-pta.md) oturum açma yöntemleriyle birleştirilebilir. Ancak bu özellik Active Directory Federasyon Hizmetleri (AD FS) (ADFS) ile kullanılamaz.

**S: sorunsuz SSO ücretsiz bir özellik mi?**

Sorunsuz SSO ücretsiz bir özelliktir ve Azure AD 'nin bu sürümü kullanabilmesi için ücretli bir sürümü gerekmez.

**S: [Microsoft Azure Almanya bulutta](https://www.microsoft.de/cloud-deutschland) ve [MICROSOFT Azure Kamu bulutta](https://azure.microsoft.com/features/gov/)sorunsuz SSO bulunabilir mi?**

Azure Kamu Bulutu için sorunsuz SSO vardır. Ayrıntılar için [Azure Kamu 'Nun karma kimlik konularını](./reference-connect-government-cloud.md)görüntüleyin.

**S: `domain_hint` sorunsuz SSO 'nun veya parametre özelliğinden yararlanan uygulamalar nelerdir `login_hint` ?**

Aşağıda, bu parametreleri Azure AD 'ye gönderebilen kapsamlı bir uygulama listesi verilmiştir ve bu nedenle kullanıcılara sorunsuz SSO (yani, kullanıcılarınızın Kullanıcı adlarını veya parolalarını girmesi gerekmez) kullanarak sessiz bir oturum açma deneyimi sağlar:

| Uygulama adı | Kullanılacak uygulama URL 'SI |
| -- | -- |
| Erişim paneli | https: \/ /myapps.Microsoft.com/contoso.com |
| Web 'de Outlook | https: \/ /Outlook.office365.com/contoso.com |
| Office 365 portalları | https: \/ /Portal.Office.com? domain_hint = contoso. com, https: \/ /www.office.com? domain_hint = contoso. com |

Ayrıca, bir uygulama Azure AD uç noktalarına kiracı olarak ayarlanan bir oturum açma isteği gönderirse, bu, https: \/ /login.microsoftonline.com/contoso.com/<.. > veya https: \/ /login.microsoftonline.com/<tenant_ID>/<.. >-Azure AD 'nin ortak uç noktası yerine, https: \/ /login.microsoftonline.com/common/<... >. Aşağıda, bu tür oturum açma isteklerini yapan, kapsamlı bir uygulamalar listesi aşağıda verilmiştir.

| Uygulama adı | Kullanılacak uygulama URL 'SI |
| -- | -- |
| SharePoint Online | https: \/ /contoso.SharePoint.com |
| Azure portalı | https: \/ /Portal.Azure.com/contoso.com |

Yukarıdaki tablolarda, kiracınızın doğru uygulama URL 'Lerine ulaşmak için "contoso.com" yerine etki alanı adınızı koyun.

Diğer uygulamaların sessiz oturum açma deneyimimizi kullanmasını istiyorsanız geri bildirim bölümünde bize bilgi verin.

**S: `Alternate ID` Kullanıcı adı yerine sorunsuz SSO Desteği `userPrincipalName` ister misiniz?**

Evet. Sorunsuz SSO `Alternate ID` , [burada](how-to-connect-install-custom.md)gösterildiği gibi Azure AD Connect ' de yapılandırıldığında Kullanıcı adı olarak destekler. Tüm Microsoft 365 uygulamaları desteklenmez `Alternate ID` . Destek bildirimiyle ilgili uygulamanın belgelerine bakın.

**S: [Azure AD JOIN](../devices/overview.md) ve sorunsuz SSO tarafından sunulan çoklu oturum açma deneyimi arasındaki fark nedir?**

[Azure AD JOIN](../devices/overview.md) , CIHAZLARı Azure AD 'ye KAYıTLıYSA kullanıcılara SSO sağlar. Bu cihazların etki alanına katılmış olması gerekmez. SSO, *birincil yenileme belirteçleri* veya *prts*'Ler kullanılarak sağlanır ve Kerberos değildir. Kullanıcı deneyimi Windows 10 cihazlarında en iyi şekilde kullanılır. SSO, Microsoft Edge tarayıcısında otomatik olarak gerçekleşir. Ayrıca, tarayıcı uzantısı kullanılarak Chrome üzerinde de kullanılabilir.

Kiracınızda Azure AD JOIN ve sorunsuz SSO kullanabilirsiniz. Bu iki özellik tamamlayıcı bir özelliklerdir. Her iki özellik de açıksa, Azure AD birleştirmelerinin SSO 'SU sorunsuz SSO 'dan önceliklidir.

**S: Windows 10 olmayan cihazları Azure AD 'ye AD FS kullanmadan kaydetmek istiyorum. Bunun yerine sorunsuz SSO kullanabilir miyim?**

Evet, bu senaryo, [çalışma alanına katılma istemcisinin](https://www.microsoft.com/download/details.aspx?id=53554)2,1 veya sonraki bir sürümünü gerektirir.

**S: bilgisayar hesabının Kerberos şifre çözme anahtarını nasıl alabilirim `AZUREADSSO` ?**

Şirket `AZUREADSSO` ıçı ad ormanınızda oluşturulan bilgisayar hesabının (Azure AD 'yi temsil eden) Kerberos şifre çözme anahtarını çok sık almak önemlidir.

>[!IMPORTANT]
>Kerberos şifre çözme anahtarını en az 30 günde bir almanızı önemle tavsiye ederiz.

Azure AD Connect çalıştırdığınız şirket içi sunucuda bu adımları izleyin:

   > [!NOTE]
   >Aşağıdaki adımlar için hem etki alanı yöneticisi hem de genel yönetici kimlik bilgilerine ihtiyacınız olacaktır.
   >Bir etki alanı yöneticisi değilseniz ve etki alanı yöneticisi tarafından izinler atadıysanız, `Update-AzureADSSOForest -OnPremCredentials $creds -PreserveCustomPermissionsOnDesktopSsoAccount`

   **1. adım. Sorunsuz SSO 'nun etkinleştirildiği AD ormanları listesini alın**

   1. İlk olarak, [Azure AD PowerShell](/powershell/azure/active-directory/overview)'i indirin ve yükleyin.
   2. `$env:programfiles\Microsoft Azure Active Directory Connect` klasörüne gidin.
   3. Bu komutu kullanarak sorunsuz SSO PowerShell modülünü içeri aktarın: `Import-Module .\AzureADSSO.psd1` .
   4. PowerShell 'i yönetici olarak çalıştırın. PowerShell 'de, çağırın `New-AzureADSSOAuthenticationContext` . Bu komut, kiracınızın genel yönetici kimlik bilgilerini girebileceğiniz bir açılan menü vermelidir.
   5. Çağrısı yapın `Get-AzureADSSOStatus | ConvertFrom-Json` . Bu komut, bu özelliğin etkinleştirildiği AD ormanları listesini ("etki alanları" listesine bakın) sağlar.

   **2. adım. Üzerinde ayarlandığı her bir AD ormanında Kerberos şifre çözme anahtarını güncelleştirme**

   1. Çağrısı yapın `$creds = Get-Credential` . İstendiğinde, hedeflenen AD ormanının etki alanı yönetici kimlik bilgilerini girin.

   > [!NOTE]
   >Etki alanı yöneticisi kimlik bilgileri Kullanıcı adı, SAM hesap adı biçiminde girilmelidir (contoso\johntikan veya contoso. com\johntikan). DNS kullanarak etki alanı yöneticisinin etki alanı denetleyicisini bulmak için Kullanıcı adının etki alanı bölümünü kullanıyoruz.

   >[!NOTE]
   >Kullanılan etki alanı yönetici hesabı, protected Users grubunun bir üyesi olmamalıdır. Öyleyse, işlem başarısız olur.

   2. Çağrısı yapın `Update-AzureADSSOForest -OnPremCredentials $creds` . Bu komut, `AZUREADSSO` Bu belırlı ad ormanındaki bilgisayar hesabının Kerberos şifre çözme anahtarını güncelleştirir ve Azure AD 'de güncelleştirir.
   
   3. Özelliği ayarladığınız her AD Ormanı için önceki adımları tekrarlayın.
   
  >[!NOTE]
   >Azure AD Connect dışında bir ormanı güncelleştiriyorsanız, genel katalog sunucusu (TCP 3268 ve TCP 3269) bağlantısının kullanılabilir olduğundan emin olun.

   >[!IMPORTANT]
   >Komutu birden çok kez _çalıştırmayın_ emin olun `Update-AzureADSSOForest` . Aksi takdirde, kullanıcılarınızın Kerberos biletlerinin süresi dolana ve şirket içi Active Directory tarafından yeniden yayımlanıncaya kadar özellik çalışmayı sonlandırır.

**S: sorunsuz SSO 'yu nasıl devre dışı bırakabilirim?**

   **1. adım. Kiracınızda özelliği devre dışı bırakın**

   **Seçenek A: Azure AD Connect kullanarak devre dışı bırak**
    
   1. Azure AD Connect çalıştırın, **Kullanıcı oturum açma sayfasını Değiştir** ' i seçin ve **İleri**' ye tıklayın.
   2. **Çoklu oturum açmayı etkinleştir** seçeneğinin işaretini kaldırın. Sihirbaza devam edin.

   Sihirbazı tamamladıktan sonra, kiracınızda sorunsuz SSO devre dışı bırakılacaktır. Bununla birlikte, ekranda şu şekilde okuyan bir ileti görürsünüz:

   "Çoklu oturum açma artık devre dışı bırakıldı, ancak temizleme işlemini tamamlamak için el ile gerçekleştirilecek ek adımlar var. [Daha fazla bilgi](tshoot-connect-sso.md#step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature)"

   Temizleme işlemini gerçekleştirmek için Azure AD Connect çalıştırdığınız şirket içi sunucuda adım 2 ve 3 ' ü izleyin.

   **Seçenek B: PowerShell kullanarak devre dışı bırak**

   Azure AD Connect çalıştırdığınız şirket içi sunucuda aşağıdaki adımları çalıştırın:

   1. İlk olarak, [Azure AD PowerShell](/powershell/azure/active-directory/overview)'i indirin ve yükleyin.
   2. `$env:ProgramFiles\Microsoft Azure Active Directory Connect` klasörüne gidin.
   3. Bu komutu kullanarak sorunsuz SSO PowerShell modülünü içeri aktarın: `Import-Module .\AzureADSSO.psd1` .
   4. PowerShell 'i yönetici olarak çalıştırın. PowerShell 'de, çağırın `New-AzureADSSOAuthenticationContext` . Bu komut, kiracınızın genel yönetici kimlik bilgilerini girebileceğiniz bir açılan menü vermelidir.
   5. Çağrısı yapın `Enable-AzureADSSO -Enable $false` .
   
   Bu noktada, sorunsuz SSO devre dışıdır, ancak sorunsuz SSO 'yu etkinleştirmek istediğiniz durumlarda etki alanları yapılandırılmış olarak kalır. Etki alanlarını sorunsuz SSO yapılandırmasından tamamen kaldırmak isterseniz yukarıdaki 5. adımı tamamladıktan sonra aşağıdaki cmdlet 'i çağırın: `Disable-AzureADSSOForest -DomainFqdn <fqdn>` .

   >[!IMPORTANT]
   >PowerShell kullanarak sorunsuz SSO devre dışı bırakmak, Azure AD Connect durumunu değiştirmez. Sorunsuz SSO, **Kullanıcı oturum açma** sayfasında etkin olarak görünür.

   **2. adım. Sorunsuz SSO 'nun etkinleştirildiği AD ormanları listesini alın**

   Azure AD Connect kullanarak sorunsuz SSO 'yu devre dışı bıraktığınız takdirde, aşağıdaki görevleri 1 ' den izleyin. Bunun yerine PowerShell kullanarak sorunsuz SSO 'yu devre dışı bırakırsanız, aşağıdaki görev 5 ' e atlayın.

   1. İlk olarak, [Azure AD PowerShell](/powershell/azure/active-directory/overview)'i indirin ve yükleyin.
   2. `$env:ProgramFiles\Microsoft Azure Active Directory Connect` klasörüne gidin.
   3. Bu komutu kullanarak sorunsuz SSO PowerShell modülünü içeri aktarın: `Import-Module .\AzureADSSO.psd1` .
   4. PowerShell 'i yönetici olarak çalıştırın. PowerShell 'de, çağırın `New-AzureADSSOAuthenticationContext` . Bu komut, kiracınızın genel yönetici kimlik bilgilerini girebileceğiniz bir açılan menü vermelidir.
   5. Çağrısı yapın `Get-AzureADSSOStatus | ConvertFrom-Json` . Bu komut, bu özelliğin etkinleştirildiği AD ormanları listesini ("etki alanları" listesine bakın) sağlar.

   **3. adım. `AZUREADSSO` Listede gördüğünüz her ad ormanındaki bilgisayar hesabını el ile silin.**

## <a name="next-steps"></a>Sonraki adımlar

- [**Hızlı başlangıç**](how-to-connect-sso-quick-start.md) -Azure AD sorunsuz SSO 'yu alın ve çalıştırın.
- [**Teknik kapsamlı**](how-to-connect-sso-how-it-works.md) bakış-bu özelliğin nasıl çalıştığını anlayın.
- [**Sorun giderme**](tshoot-connect-sso.md) -özellikle ilgili yaygın sorunları çözmeyi öğrenin.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -yeni özellik isteklerini dosyalama.
