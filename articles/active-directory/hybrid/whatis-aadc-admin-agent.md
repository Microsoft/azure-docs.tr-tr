---
title: Azure AD Connect yönetici Aracısı nedir? Azure AD Connect | Microsoft Docs
description: Eşitleme ve Azure AD ile şirket içi ortamınızı izlemek için kullanılan araçları açıklar.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 222dab87ee71870e564e426d7466555893cc565b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305214"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Azure AD Connect Yönetim Aracısı nedir? 
Azure AD Connect Yönetim Aracısı, bir Azure Active Directory Connect sunucusuna yüklenebilen yeni bir Azure Active Directory Connect bileşenidir. Bir destek talebi açtığınızda Microsoft destek mühendisine sorunları gidermeye yardımcı olan Active Directory ortamınızdan belirli verileri toplamak için kullanılır. 

>[!NOTE]
>Yönetici aracısı yüklenmez ve varsayılan olarak etkin değildir.  Destek durumlarında yardımcı olacak verileri toplamak için aracıyı yüklemelisiniz.

Yüklendiğinde, Azure AD Connect Yönetim Aracısı Azure Active Directory verileri için belirli istekleri bekler, eşitleme ortamından istenen verileri alır ve Microsoft desteği 'ne sunulan Azure Active Directory gönderir in. 

Azure AD Connect yönetim aracısının ortamınızdan aldığı bilgiler hiçbir şekilde depolanmaz; bu, yalnızca Microsoft destek mühendisine, Azure Active Directory Connect araştırıp sorunları gidermeye yardımcı olur Azure AD Connect yönetim aracısını açtığınız ilgili destek, Azure AD Connect sunucusunda varsayılan olarak yüklü değildir. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Azure AD Connect sunucusuna Azure AD Connect yönetim aracısını yükler 

Önkoşullar:
1.  Azure AD Connect sunucuya yüklendi
2.  Azure AD Connect Health sunucuya yüklendi

![Yönetim Aracısı](media/whatis-aadc-admin-agent/adminagent0.png)

Azure AD Connect Yönetim Aracısı ikilileri AAD Connect sunucusuna yerleştirilir. Aracıyı yüklemek için aşağıdakileri yapın:

1.  PowerShell 'i yönetici modunda aç
2.  Uygulamanın CD 'nin bulunduğu dizine gidin "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.  ConfigureAdminAgent. ps1 öğesini çalıştırın

İstendiğinde lütfen Azure AD Genel yönetici kimlik bilgilerinizi girin. Bu, Azure AD Connect yüklemesi sırasında girilen kimlik bilgileri ile aynı olmalıdır.

Aracı yüklendikten sonra, sunucunuzun Denetim Masası 'ndaki "Program Ekle/Kaldır" listesinde aşağıdaki iki yeni programı görürsünüz: 

![Yönetim Aracısı](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Eşitleme hizmetimde hangi veriler Microsoft hizmet mühendisine gösteriliyor? 
Microsoft Desteği mühendisinin bir destek talebi açtığınızda, belirli bir kullanıcı için, Active Directory içindeki ilgili veriler, Azure Active Directory Connect sunucusundaki Active Directory bağlayıcı alanı, Azure 'daki Azure Active Directory bağlayıcı alanı için görebilir. Active Directory sunucuya ve meta veri deposuna Azure Active Directory Connect sunucusunda bağlanın. 

Microsoft Desteği mühendisi sisteminizdeki verileri değiştiremez ve herhangi bir parolayı göremez. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Microsoft destek mühendisinin verilerinize erişimini istemiyorum ne yapmalıyım? 
Aracı yüklendikten sonra, Microsoft hizmet mühendisinin bir destek çağrısı için verilerinize erişmesini istemiyorsanız, hizmet yapılandırma dosyasını aşağıda açıklandığı gibi değiştirerek işlevselliği devre dışı bırakabilirsiniz: 

1.  Not defteri 'nde **C:\Program Files\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** öğesini açın.
2.  **Userdataenabled** ayarını aşağıda gösterildiği gibi devre dışı bırakın. **Userdataenabled** ayarı varsa ve true olarak ayarlanırsa, false olarak ayarlayın. Ayar yoksa, ayarı aşağıda gösterildiği gibi ekleyin.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Yapılandırma dosyasını kaydedin.
4.  Azure AD Connect Yönetim Aracısı hizmetini aşağıda gösterildiği gibi yeniden başlatın

![Yönetim Aracısı](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
