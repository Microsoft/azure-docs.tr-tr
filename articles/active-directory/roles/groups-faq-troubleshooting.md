---
title: Bulut grubuna atanan rol sorunlarını giderme SSS-Azure Active Directory | Microsoft Docs
description: Azure Active Directory gruplara roller atamaya yönelik bazı yaygın sorular ve sorun giderme ipuçları hakkında bilgi edinin.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ff906654dc91c7b1980292efd2a737503e684e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742922"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>Bulut gruplarına atanan rollerle ilgili sorunları giderme

Azure Active Directory (Azure AD) içindeki gruplara rol atamaya yönelik bazı yaygın sorular ve sorun giderme ipuçları aşağıda verilmiştir.

**S:** Bir grup yöneticiyim, ancak **Azure AD rollerinin grup anahtarına atanabileceği** hakkında göremiyorum.

Y **:** Yalnızca ayrıcalıklı rol yöneticileri veya genel Yöneticiler, rol ataması için uygun bir grup oluşturabilir. Bu denetimi yalnızca bu rollerdeki kullanıcılar görebilir.

**S:** Azure AD rollerine atanan grupların üyeliğini kim değiştirebilir?

Y **:** Varsayılan olarak, yalnızca ayrıcalıklı rol yöneticisi ve genel yönetici, rol atanabilir bir grubun üyeliğini yönetir, ancak Grup sahipleri ekleyerek rol atanabilir grupların yönetimine temsilci atayabilirsiniz.

**S**: kuruluşumdaki bir yardım masası yöneticim, ancak bir dizin okuyucusu olan kullanıcının parolasını güncelleştiremiyorum. Neden meydana gelir?

Y **: Kullanıcı**, rol atanabilir bir grup yoluyla Dizin okuyucuyu alabilir. Rol atanabilir grupların tüm üyeleri ve sahipleri korunur. Yalnızca ayrıcalıklı kimlik doğrulama Yöneticisi veya genel yönetici rollerindeki kullanıcılar, korumalı bir kullanıcının kimlik bilgilerini sıfırlayabilir.

**S:** Bir kullanıcının parolasını güncelleştiremiyorum. Bunlara daha fazla ayrıcalıklı rol atanmaz. Neden gerçekleşiyor?

Y **:** Kullanıcı, rol atanabilir bir grubun sahibi olabilir. Ayrıcalık yükselmesine engel olmak için rol atanabilir grupların sahiplerini koruyoruz. Bir grup Contoso_Security_Admins güvenlik yöneticisi rolüne atanırsa, Bob 'un Grup sahibi ve Gamze 'nin kuruluşta parola Yöneticisi olduğu durumlarda bu örnek olabilir. Bu koruma yoksa, Gamze Bob 'un kimlik bilgilerini sıfırlayabilir ve kimliğini alabilir. Bundan sonra, Çiğdem, kuruluştaki bir güvenlik yöneticisi olmak üzere Grup Contoso_Security_Admins grubuna belirtmemiş veya herkes ekleyebilir. Bir kullanıcının grup sahibi olup olmadığını öğrenmek için, kullanıcının sahip olduğu nesnelerin listesini alın ve herhangi bir grubun ıastifbletorole değerini doğru olarak ayarlayıp görmediğinden emin olmanız gerekir. Yanıt Evet ise, bu kullanıcı korunur ve davranış tasarıma göre yapılır. Sahip olunan nesneleri almak için şu belgeleri inceleyin:

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject)  
- [OwnedObjects listesini Listele](/graph/api/user-list-ownedobjects?tabs=http)

**S:** Azure AD rollerine atanabilecek gruplar üzerinde bir erişim incelemesi oluşturabilir miyim (özellikle, ısastifbletorole özelliği true olarak ayarlanan gruplar)?  

Y **:** Evet, şunları yapabilirsiniz. En yeni erişim gözden geçirmesi sürümüne sahipseniz, gözden geçirenler varsayılan olarak erişimime yönlendirilir ve yalnızca genel Yöneticiler rol atanabilir gruplar üzerinde erişim gözden geçirmeleri oluşturabilir. Ancak, erişim gözden geçireni 'nin eski bir sürümü kullanıyorsanız, gözden geçirenler varsayılan olarak erişim paneline yönlendirilir ve hem genel Yöneticiler hem de Kullanıcı Yöneticisi, rol atanabilir gruplar üzerinde erişim gözden geçirmeleri oluşturabilir. Yeni deneyim 28 Temmuz 2020 ' de tüm müşterilere alınacaktır, ancak daha önce yükseltmek istiyorsanız, [Azure AD erişim gözden geçirmeleri için bir istek oluşturun ve erişim izni ' nda güncelleştirilmiş gözden geçiren deneyimi](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)yapın.

**S:** Bir erişim paketi oluşturabilir ve içindeki Azure AD rollerine atanabilecek grupları ekleyebilir miyim?

Y **:** Evet, şunları yapabilirsiniz. Genel yönetici ve Kullanıcı Yöneticisi 'nin bir erişim paketine herhangi bir grup koyma gücü vardır. Genel yönetici için hiçbir değişiklik yapılmadı, ancak Kullanıcı Yöneticisi rol izinlerinde küçük bir değişiklik vardır. Rol atanabilir grubu bir erişim paketine eklemek için, Kullanıcı Yöneticisi ve rol atanabilir grubun sahibi olmanız gerekir. Kurumsal Lisans yönetiminde kimlerin erişim paketi oluşturerişebileceğini gösteren tam tablo aşağıda verilmiştir:

Azure AD dizin rolü | Yetkilendirme Yönetimi rolü | Güvenlik grubu eklenebilir\* | Microsoft 365 grubu eklenebilir\* | Uygulama eklenebilir | SharePoint Online sitesi eklenebilir
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
Genel yönetici | yok | ✔️ | ✔️ | ✔️  | ✔️
Kullanıcı yöneticisi  | yok  | ✔️  | ✔️  | ✔️
Intune yöneticisi | Katalog sahibi | ✔️  | ✔️  | &nbsp;  | &nbsp;
Exchange yöneticisi  | Katalog sahibi  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Takımlar Hizmet Yöneticisi | Katalog sahibi  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
SharePoint yöneticisi | Katalog sahibi | &nbsp; | ✔️  | &nbsp;  | ✔️ 
Uygulama Yöneticisi | Katalog sahibi  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Bulut uygulaması Yöneticisi | Katalog sahibi  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Kullanıcı | Katalog sahibi | Yalnızca Grup sahibi ise | Yalnızca Grup sahibi ise | Yalnızca uygulama sahibi ise  | &nbsp;

\*Grup rol atanabilir değil; Yani, ısastifbletorole = false. Bir grup rol atanabilir ise, erişim paketini oluşturan kişinin de rol atanabilir grubun sahibi olması gerekir.

**S:** "Atanan roller" içinde "atamayı kaldır" seçeneğini bulamıyorum. Rol atamasını bir kullanıcıya silmek mi Nasıl yaparım??

Y **:** Bu yanıt yalnızca Azure AD Premium P1 kuruluşlarda geçerlidir.

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Azure Active Directory** açın.
1. Kullanıcıları seçin ve bir kullanıcı profili açın.
1. **Atanan roller**' i seçin.
1. Dişli simgesini seçin. Bu bilgileri veren bir bölme açılır. Doğrudan atamalar ' ın yanındaki bir "Kaldır" düğmesi vardır. Dolaylı rol atamasını kaldırmak için, rolü atanmış olan gruptan kullanıcıyı kaldırın.

**S:** Nasıl yaparım? rol atanabilir olan tüm grupları görün mi?

Y **:** Şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Azure Active Directory** açın.
1. **Gruplar**  >  **tüm gruplar**' ı seçin.
1. **Filtre Ekle**' yi seçin.
1. **Rol atanabilir** olarak filtreleyin.

**S:** Nasıl yaparım? hangi rolün bir sorumlu 'e doğrudan ve dolaylı olarak atandığını öğrensin mi?

Y **:** Şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Azure Active Directory** açın.
1. Kullanıcıları seçin ve bir kullanıcı profili açın.
1. **Atanan roller**' i seçin ve ardından:

    - Azure AD Premium P1 lisanslı kuruluşlar: dişli simgesini seçin. Bu bilgileri veren bir bölme açılır.
    - Azure AD Premium P2 lisanslı kuruluşlar: **Üyelik** sütununda doğrudan ve devralınan lisans bilgilerini bulacaksınız.

**S:** Rol atamak için neden yeni bir bulut grubu oluşturulmasını zorlıyoruz?  

Y **:** Var olan bir grubu bir role atarsanız, var olan Grup sahibi, yeni üyeler role sahip olmaları gerekmeden bu gruba başka Üyeler ekleyebilir. Rol atanabilir gruplar güçlü olduğundan, bunları korumak için çok sayıda kısıtlama koyuyoruz. Grubu yöneten kişiye göz önünde bulundurularak, grupta değişiklik yapmak istemezsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Rol atamalarını yönetmek için bulut gruplarını kullanma](groups-concept.md)
- [Rol atanabilir grup oluşturma](groups-create-eligible.md)
