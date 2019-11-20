---
title: 'Azure AD Connect: sorunsuz çoklu oturum açma-sık sorulan sorular | Microsoft Docs'
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
ms.topic: conceptual
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7241c8dfbedb24f95c29ea9e1c3f763218a5668d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025665"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Kesintisiz çoklu oturum açma Azure Active Directory: sık sorulan sorular

Bu makalede, sorunsuz çoklu oturum açma Azure Active Directory (sorunsuz SSO) hakkında sık sorulan soruları ele aldık. Yeni içerik için yeniden kullanıma devam edin.

**S: hangi oturum açma yöntemlerinin sorunsuz SSO iş yaptığını**

Kesintisiz SSO, [Parola karması eşitlemesi](how-to-connect-password-hash-synchronization.md) veya [geçişli kimlik doğrulaması](how-to-connect-pta.md) oturum açma yöntemleriyle birleştirilebilir. Ancak bu özellik Active Directory Federasyon Hizmetleri (AD FS) (ADFS) ile kullanılamaz.

**S: sorunsuz SSO ücretsiz bir özellik mi?**

Sorunsuz SSO ücretsiz bir özelliktir ve Azure AD 'nin bu sürümü kullanabilmesi için ücretli bir sürümü gerekmez.

**S: [Microsoft Azure Almanya bulutta](https://www.microsoft.de/cloud-deutschland) ve [MICROSOFT Azure Kamu bulutta](https://azure.microsoft.com/features/gov/)sorunsuz SSO bulunabilir mi?**

Hayır. Sorunsuz SSO yalnızca dünya çapındaki Azure AD örneğinde bulunabilir.

**S: sorunsuz SSO için `domain_hint` veya `login_hint` parametre özelliğinden yararlanan uygulamalar nelerdir?**

Aşağıda, bu parametreleri Azure AD 'ye gönderebilen kapsamlı bir uygulama listesi verilmiştir ve bu nedenle kullanıcılara sorunsuz SSO (yani, kullanıcılarınızın Kullanıcı adlarını veya parolalarını girmesi gerekmez) kullanarak sessiz bir oturum açma deneyimi sağlar:

| Uygulama adı | Kullanılacak uygulama URL 'SI |
| -- | -- |
| Erişim paneli | https: \//uygulamaps. Microsoft. com/contoso. com |
| Web 'de Outlook | https: \//Outlook. office365. com/contoso. com |
| Office 365 portalları | https: \//Portal. Office. com? domain_hint = contoso. com, https: \//www. Office. com? domain_hint = contoso. com |

Ayrıca, bir uygulama Azure AD uç noktalarına kiracı olarak ayarlanan oturum açma istekleri gönderirse, bu, https: \//Login. microsoftonline. com/contoso. com/<... > veya https: \//Login. microsoftonline. com/< tenant_ID >/<.. >-Azure AD 'nin ortak uç noktası yerine, https: \//Login. microsoftonline. com/Common/<... >. Aşağıda, bu tür oturum açma isteklerini yapan, kapsamlı bir uygulamalar listesi aşağıda verilmiştir.

| Uygulama adı | Kullanılacak uygulama URL 'SI |
| -- | -- |
| SharePoint Online | https: \//contoso. SharePoint. com |
| Azure portalı | https: \//Portal. Azure. com/contoso. com |

Yukarıdaki tablolarda, kiracınızın doğru uygulama URL 'Lerine ulaşmak için "contoso.com" yerine etki alanı adınızı koyun.

Diğer uygulamaların sessiz oturum açma deneyimimizi kullanmasını istiyorsanız geri bildirim bölümünde bize bilgi verin.

**S: `userPrincipalName` yerine Kullanıcı adı olarak @no__t, sorunsuz SSO Desteği ister misiniz?**

Evet. Sorunsuz SSO, [burada](how-to-connect-install-custom.md)gösterildiği gibi Azure AD Connect yapılandırıldığında Kullanıcı adı olarak @no__t destekler. Tüm Office 365 uygulamaları `Alternate ID` desteklemez. Destek bildirimiyle ilgili uygulamanın belgelerine bakın.

**S: [Azure AD JOIN](../active-directory-azureadjoin-overview.md) ve sorunsuz SSO tarafından sunulan çoklu oturum açma deneyimi arasındaki fark nedir?**

[Azure AD JOIN](../active-directory-azureadjoin-overview.md) , CIHAZLARı Azure AD 'ye KAYıTLıYSA kullanıcılara SSO sağlar. Bu cihazların etki alanına katılmış olması gerekmez. SSO, *birincil yenileme belirteçleri* veya *prts*'Ler kullanılarak sağlanır ve Kerberos değildir. Kullanıcı deneyimi Windows 10 cihazlarında en iyi şekilde kullanılır. SSO, Microsoft Edge tarayıcısında otomatik olarak gerçekleşir. Ayrıca, tarayıcı uzantısı kullanılarak Chrome üzerinde de kullanılabilir.

Kiracınızda Azure AD JOIN ve sorunsuz SSO kullanabilirsiniz. Bu iki özellik tamamlayıcı bir özelliklerdir. Her iki özellik de açıksa, Azure AD birleştirmelerinin SSO 'SU sorunsuz SSO 'dan önceliklidir.

**S: Windows 10 olmayan cihazları Azure AD 'ye AD FS kullanmadan kaydetmek istiyorum. Bunun yerine sorunsuz SSO kullanabilir miyim?**

Evet, bu senaryo, [çalışma alanına katılma istemcisinin](https://www.microsoft.com/download/details.aspx?id=53554)2,1 veya sonraki bir sürümünü gerektirir.

**S: `AZUREADSSOACC` bilgisayar hesabının Kerberos şifre çözme anahtarını nasıl alabilirim?**

Şirket içi AD ormanınızda oluşturulan `AZUREADSSOACC` bilgisayar hesabının (Azure AD 'yi temsil eden) Kerberos şifre çözme anahtarını çok sık almak önemlidir.

>[!IMPORTANT]
>Kerberos şifre çözme anahtarını en az 30 günde bir almanızı önemle tavsiye ederiz.

Azure AD Connect çalıştırdığınız şirket içi sunucuda bu adımları izleyin:

   **1. adım. Sorunsuz SSO 'nun etkinleştirildiği AD ormanları listesini alın**

   1. İlk olarak, [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)'i indirin ve yükleyin.
   2. `%programfiles%\Microsoft Azure Active Directory Connect` klasörüne gidin.
   3. Şu komutu kullanarak sorunsuz SSO PowerShell modülünü içeri aktarın: `Import-Module .\AzureADSSO.psd1`.
   4. PowerShell 'i yönetici olarak çalıştırın. PowerShell 'de `New-AzureADSSOAuthenticationContext` ' ı çağırın. Bu komut, kiracınızın genel yönetici kimlik bilgilerini girebileceğiniz bir açılan menü vermelidir.
   5. @No__t-0 çağrısı yapın. Bu komut, bu özelliğin etkinleştirildiği AD ormanları listesini ("etki alanları" listesine bakın) sağlar.

   **2. adım. Üzerinde ayarlandığı her bir AD ormanında Kerberos şifre çözme anahtarını güncelleştirme**

   1. @No__t-0 çağrısı yapın. İstendiğinde, hedeflenen AD ormanının etki alanı yönetici kimlik bilgilerini girin.

   > [!NOTE]
   >Etki alanı yöneticisi kimlik bilgileri Kullanıcı adı, SAM hesap adı biçiminde girilmelidir (contoso\johntikan veya contoso. com\johntikan). DNS kullanarak etki alanı yöneticisinin etki alanı denetleyicisini bulmak için Kullanıcı adının etki alanı bölümünü kullanıyoruz.

   >[!NOTE]
   >Kullanılan etki alanı yönetici hesabı, protected Users grubunun bir üyesi olmamalıdır. Öyleyse, işlem başarısız olur.

   2. @No__t-0 çağrısı yapın. Bu komut, bu belirli AD ormanındaki `AZUREADSSOACC` bilgisayar hesabı için Kerberos şifre çözme anahtarını güncelleştirir ve Azure AD 'de güncelleştirir.
   3. Özelliği ayarladığınız her AD Ormanı için önceki adımları tekrarlayın.

   >[!IMPORTANT]
   >@No__t-1 komutunu birden çok kez _çalıştırmayın_ emin olun. Aksi takdirde, kullanıcılarınızın Kerberos biletlerinin süresi dolana ve şirket içi Active Directory tarafından yeniden yayımlanıncaya kadar özellik çalışmayı sonlandırır.

**S: sorunsuz SSO 'yu nasıl devre dışı bırakabilirim?**

   **1. adım. Kiracınızda özelliği devre dışı bırakın**

   **Seçenek A: Azure AD Connect kullanarak devre dışı bırak**
    
   1. Azure AD Connect çalıştırın, **Kullanıcı oturum açma sayfasını Değiştir** ' i seçin ve **İleri**' ye tıklayın.
   2. **Çoklu oturum açmayı etkinleştir** seçeneğinin işaretini kaldırın. Sihirbaza devam edin.

   Sihirbazı tamamladıktan sonra, kiracınızda sorunsuz SSO devre dışı bırakılacaktır. Bununla birlikte, ekranda şu şekilde okuyan bir ileti görürsünüz:

   "Çoklu oturum açma artık devre dışı bırakıldı, ancak temizleme işlemini tamamlamak için el ile gerçekleştirilecek ek adımlar var. Daha fazla bilgi "

   Temizleme işlemini gerçekleştirmek için Azure AD Connect çalıştırdığınız şirket içi sunucuda adım 2 ve 3 ' ü izleyin.

   **Seçenek B: PowerShell kullanarak devre dışı bırak**

   Azure AD Connect çalıştırdığınız şirket içi sunucuda aşağıdaki adımları çalıştırın:

   1. İlk olarak, [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)'i indirin ve yükleyin.
   2. `%programfiles%\Microsoft Azure Active Directory Connect` klasörüne gidin.
   3. Şu komutu kullanarak sorunsuz SSO PowerShell modülünü içeri aktarın: `Import-Module .\AzureADSSO.psd1`.
   4. PowerShell 'i yönetici olarak çalıştırın. PowerShell 'de `New-AzureADSSOAuthenticationContext` ' ı çağırın. Bu komut, kiracınızın genel yönetici kimlik bilgilerini girebileceğiniz bir açılan menü vermelidir.
   5. @No__t-0 çağrısı yapın.

   >[!IMPORTANT]
   >PowerShell kullanarak sorunsuz SSO devre dışı bırakmak, Azure AD Connect durumunu değiştirmez. Sorunsuz SSO, **Kullanıcı oturum açma** sayfasında etkin olarak görünür.

   **2. adım. Sorunsuz SSO 'nun etkinleştirildiği AD ormanları listesini alın**

   Azure AD Connect kullanarak sorunsuz SSO 'yu devre dışı bıraktığınız takdirde, aşağıdaki görevleri 1 ' den izleyin. Bunun yerine PowerShell kullanarak sorunsuz SSO 'yu devre dışı bırakırsanız, aşağıdaki görev 5 ' e atlayın.

   1. İlk olarak, [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)'i indirin ve yükleyin.
   2. `%programfiles%\Microsoft Azure Active Directory Connect` klasörüne gidin.
   3. Şu komutu kullanarak sorunsuz SSO PowerShell modülünü içeri aktarın: `Import-Module .\AzureADSSO.psd1`.
   4. PowerShell 'i yönetici olarak çalıştırın. PowerShell 'de `New-AzureADSSOAuthenticationContext` ' ı çağırın. Bu komut, kiracınızın genel yönetici kimlik bilgilerini girebileceğiniz bir açılan menü vermelidir.
   5. @No__t-0 çağrısı yapın. Bu komut, bu özelliğin etkinleştirildiği AD ormanları listesini ("etki alanları" listesine bakın) sağlar.

   **3. adım. @No__t-1 bilgisayar hesabını, listede gördüğünüz her AD ormandan el ile silin.**

## <a name="next-steps"></a>Sonraki adımlar

- [**Hızlı başlangıç**](how-to-connect-sso-quick-start.md) -Azure AD sorunsuz SSO 'yu alın ve çalıştırın.
- [**Teknik kapsamlı**](how-to-connect-sso-how-it-works.md) bakış-bu özelliğin nasıl çalıştığını anlayın.
- [**Sorun giderme**](tshoot-connect-sso.md) -özellikle ilgili yaygın sorunları çözmeyi öğrenin.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -yeni özellik isteklerini dosyalama.
