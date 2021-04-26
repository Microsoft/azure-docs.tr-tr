---
title: 'Azure AD Connect: ADConnectivityTool PowerShell modülü nedir? | Microsoft Docs'
description: Bu belge yeni ADConnectivity PowerShell modülünü ve sorun gidermeye yardımcı olmak için nasıl kullanılabileceğini tanıtır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7eed3e06ab42671d9674ad3893a88dfe9817e22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95973465"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>ADConnectivityTool PowerShell modülüyle Azure AD bağlantısı sorunlarını giderme

ADConnectivity Aracı, aşağıdakilerden birinde kullanılan bir PowerShell modülüdür:

- Bir ağ bağlantısı sorunu, yükleme sırasında, kullanıcının sihirbazda verdiği Active Directory kimlik bilgilerinin başarıyla doğrulanmasını engelliyorsa.
- Bir PowerShell oturumundan işlevleri çağıran bir kullanıcı tarafından yükleme sonrası.

Araç şu konumda bulunur: **C:\Program Files\Microsoft Azure Active Directory Connect\araçları \ ADConnectivityTool. psm1** 

## <a name="adconnectivitytool-during-installation"></a>Yükleme sırasında ADConnectivityTool

**Dizinlerinizi bağlama** sayfasında, Azure AD Connect sihirbazında, bir ağ sorunu oluşursa, ADConnectivityTool, ne olduğunu belirlemek için kendi işlevlerinden birini otomatik olarak kullanır.  Aşağıdakilerden herhangi biri ağ sorunları olarak kabul edilebilir:

- Kullanıcının verdiği ormanın adı yanlışlıkla yazılı veya orman yok 
- UDP bağlantı noktası 389, kullanıcının verdiği ormanla ilişkili etki alanı denetleyicilerinde kapalı
- ' AD orman hesabı ' penceresinde belirtilen kimlik bilgileri, hedef ormanla ilişkili etki alanı denetleyicilerini alma ayrıcalıklarına sahip değil
- 53, 88 veya 389 TCP bağlantı noktalarından herhangi biri, kullanıcının verdiği ormanla ilişkili etki alanı denetleyicilerinde kapalı 
- Hem UDP 389 hem de bir TCP bağlantı noktası (veya bağlantı noktaları) kapalı
- DNS, belirtilen orman ve \ veya ilişkili etki alanı denetleyicileri için çözümlenemedi

Bu sorunlardan herhangi biri bulunduğunda, AADConnect sihirbazında ilgili bir hata iletisi görüntülenir:


![Hata](media/how-to-connect-adconnectivitytools/error1.png)

Örneğin, **dizinlerinizi bağlama** ekranında bir dizin eklemeye çalışırken, Azure AD Connect bunun doğrulanması gerekir ve bağlantı noktası 389 üzerinden bir etki alanı denetleyicisiyle iletişim kurabilmeyi bekler.  Bu işlem, yukarıdaki ekran görüntüsünde gösterilen hatayı görüyoruz.  

Arka planda gerçekten neler oluyordu, Azure AD Connect işlevi çağırıyor olur `Start-NetworkConnectivityDiagnosisTools` .  Bu işlev, bir ağ bağlantısı sorunu nedeniyle kimlik bilgilerinin doğrulanması başarısız olduğunda çağrılır.

Son olarak, araç sihirbazdan her çağrıldığında ayrıntılı bir günlük dosyası oluşturulur. Günlük **c:\programdata\aadconnect\adconnectivitytool- \<date> - \<time> . log** konumunda bulunur

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools yükleme sonrası
Azure AD Connect yüklendikten sonra, ADConnectivityTools PowerShell modülündeki işlevlerden herhangi biri kullanılabilir.  

[Adconnectivitytools başvurusundaki](reference-connect-adconnectivitytools.md) işlevlerde başvuru bilgilerini bulabilirsiniz

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

ADConnectivityTool. psm1 PowerShell 'e aktarıldıktan sonra **yalnızca** el ile çağrılabilmesi için, bu işlevi çağıracağız. 

Bu işlev, Azure AD Connect Sihirbazı 'nın, belirtilen AD kimlik bilgilerini doğrulamak için çalıştırdığı mantığı yürütür.  Ancak, sorun ve önerilen çözüm hakkında daha ayrıntılı bir açıklama sağlar. 

Bağlantı doğrulaması aşağıdaki adımlardan oluşur:
-   Etki alanı FQDN 'SI al (tam etki alanı adı) nesnesi
-   Kullanıcı ' yeni AD hesabı oluştur ' seçeneğini belirlediyseniz bu kimlik bilgilerinin Enterprise Administrators grubuna ait olduğunu doğrulayın.
-   Orman FQDN 'SI nesnesi Al
-   Daha önce edinilen orman FQDN nesnesiyle ilişkili en az bir etki alanının erişilebilir olduğunu onaylayın
-   Ormanın işlev düzeyinin Windows Server 2003 veya sonraki bir sürümü olduğunu doğrulayın.

Tüm bu eylemler başarıyla yürütülürse, Kullanıcı bir dizin ekleyebilecektir.

Kullanıcı bir sorun çözümlendikten sonra bu işlevi çalıştırıyorsa (veya hiç bir sorun yoksa), çıktı kullanıcının Azure AD Connect sihirbazına geri dönüp kimlik bilgilerini yeniden eklemeye çalışır.



## <a name="next-steps"></a>Sonraki Adımlar
- [Azure AD Connect: Hesaplar ve izinler](reference-connect-accounts-permissions.md)
- [Hızlı yükleme](how-to-connect-install-express.md)
- [Özel yükleme](how-to-connect-install-custom.md)
- [ADConnectivityTools Başvurusu](reference-connect-adconnectivitytools.md)

