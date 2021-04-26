---
title: B2B Konuk Kullanıcı Azure Active Directory özellikleri | Microsoft Docs
description: Azure Active Directory B2B Konuk kullanıcı özelliklerini ve durumlarını davet etmeden önce ve sonra yapın
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/18/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08ed02c65db201e18ce710633b421edb5ecb2db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655708"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B işbirliği kullanıcısının özellikleri

Bu makalede, davet yeniden gerçekleştirilmeden önce ve sonra Azure Active Directory (Azure AD) içindeki B2B Konuk Kullanıcı nesnesinin özellikleri ve durumları açıklanmaktadır. Bir Azure AD işletmeden işletmeye (B2B) işbirliği kullanıcısı UserType = konuğa sahip bir kullanıcıdır. Bu Konuk Kullanıcı genellikle bir iş ortağı kuruluştan yapılır ve varsayılan olarak, davet eden dizinde sınırlı ayrıcalıklara sahiptir.

Kuruluşun ihtiyaçlarına bağlı olarak, bir Azure AD B2B işbirliği kullanıcısı aşağıdaki hesap durumlarından birinde olabilir:

- Durum 1: Azure AD 'nin dış bir örneğinde bulunan ve davet edilen kuruluşta Konuk Kullanıcı olarak gösterilen. Bu durumda, B2B kullanıcısı, davet edilen kiracıya ait olan bir Azure AD hesabı kullanarak oturum açar. İş ortağı kuruluşu Azure AD kullanmıyorsa, Azure AD 'de Konuk Kullanıcı hala oluşturulur. Gereksinimler, davetlerinin yanı sıra Azure AD 'nin e-posta adreslerini doğrular. Bu düzenleme, tam zamanında (JıT) kiracı veya "viral" kiramı olarak da adlandırılır.

   > [!IMPORTANT]
   > Microsoft, **2021 Ekim 'Den itibaren**, B2B işbirliği senaryoları Için YÖNETILMEYEN Azure AD hesapları ve kiracılar oluşturarak artık davetlerin kullanımını desteklememektedir. Hazırlık aşamasında, müşterilerin [e-posta bir kerelik geçiş kodu kimlik doğrulamasını](one-time-passcode.md)kabul etmelerini öneririz. Bu genel önizleme özelliğiyle ilgili geri bildirimlerinize hoş geldiniz ve işbirliği yapmak için daha fazla yol oluşturmak heyecanlıyız.

- Durum 2: bir Microsoft veya diğer hesapta barındırın ve konak kuruluşunda Konuk Kullanıcı olarak temsil edilir. Bu durumda, Konuk Kullanıcı Microsoft hesabı veya sosyal hesap (google.com veya benzeri) ile oturum açar. Davet edilen kullanıcının kimliği, teklifi satın alma sırasında, davet edilen kuruluşun dizininde bir Microsoft hesabı olarak oluşturulur.

- Durum 3: konak kuruluşun şirket içi Active Directory barındırın ve ana bilgisayar kuruluşun Azure AD ile eşitlenmiş. İş ortağı hesaplarını, UserType = konuğa sahip Azure AD B2B kullanıcıları olarak buluta eşitlemek için Azure AD Connect kullanabilirsiniz. Bkz. [yerel olarak yönetilen iş ortağı hesaplarına bulut kaynaklarına erişim Izni verme](hybrid-on-premises-to-cloud.md).

- Durum 4: konak kuruluşun Azure AD 'de UserType = konuğa ve konak kuruluşun yönettiği kimlik bilgileriyle barının.

  ![Dört Kullanıcı durumunu gösteren diyagram](media/user-properties/redemption-diagram.png)


Şimdi Azure AD B2B işbirliği kullanıcısının Azure AD 'de nasıl göründüğünü görelim.

### <a name="before-invitation-redemption"></a>Davet almadan önce

Durum 1 ve durum 2 hesapları, konuk kullanıcıların kendi kimlik bilgilerini kullanarak, Konuk kullanıcıları işbirliği yapmaya davet etmenin sonucudur. Davet ilk olarak Konuk kullanıcıya gönderildiğinde, dizininizde bir hesap oluşturulur. Kimlik doğrulama, Konuk kullanıcının kimlik sağlayıcısı tarafından gerçekleştirildiğinden, bu hesabın kendisiyle ilişkili bir kimlik bilgisi yok. Dizininizdeki Konuk Kullanıcı hesabının **kaynak** özelliği, **davet edilen Kullanıcı** olarak ayarlanır. 

![Teklifin önermesinin önüne Kullanıcı özelliklerini gösteren ekran görüntüsü](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Davet yeniden satın alındıktan sonra

Konuk Kullanıcı daveti kabul ettikten sonra, **kaynak** özelliği, Konuk kullanıcının kimlik sağlayıcısına göre güncelleştirilir.

Durum 1 ' deki Konuk kullanıcılar için, **kaynak** **dış Azure Active Directory**.

![Eyalet 1 konuk kullanım teklifi](media/user-properties/after-redemption-state1.png)

Durum 2 ' deki Konuk kullanıcılar için, **kaynak** **Microsoft hesabıdır**.

![Durum 2 teklif kullanım sonrasında Konuk Kullanıcı](media/user-properties/after-redemption-state2.png)

Durum 3 ve durum 4 ' te Konuk kullanıcılar için, **kaynak** özelliği, sonraki bölümde açıklandığı gibi **Azure Active Directory** veya **Windows Server Active Directory** olarak ayarlanır.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Azure AD B2B işbirliği kullanıcısının temel özellikleri
### <a name="usertype"></a>UserType
Bu özellik kullanıcının konak kiralamanın ilişkisini gösterir. Bu özellik iki değere sahip olabilir:
- Üye: Bu değer, bir konak kuruluşun ve kuruluşun Bordro içindeki bir kullanıcının bir çalışanına işaret ediyor. Örneğin, bu kullanıcı yalnızca iç sitelere erişimi bekliyor. Bu Kullanıcı bir dış ortak çalışan olarak kabul edilmiyor.

- Konuk: Bu değer, şirket için dahili bir ortak çalışan, iş ortağı veya müşteri gibi kabul edilen bir kullanıcıyı gösterir. Bu tür bir kullanıcının, örneğin bir CEO 'nun iç faturasını alması veya şirket avantajlarından yararlanmak beklenmez.

  > [!NOTE]
  > UserType Kullanıcı oturum açma ile ilgili hiçbir ilişkiye, kullanıcının dizin rolüne ve bu şekilde devam eder. Bu özellik yalnızca kullanıcının konak kuruluşla ilişkisini belirtir ve kuruluşun bu özelliğe bağlı olan ilkeleri zorlayabilmesine izin verir.

Fiyatlandırmayla ilgili ayrıntılar için lütfen [Azure Active Directory fiyatlandırmaya](https://azure.microsoft.com/pricing/details/active-directory)başvurun.

### <a name="source"></a>Kaynak
Bu özellik kullanıcının nasıl oturum açdığını gösterir.

- Davet edilen Kullanıcı: bu kullanıcı davet edildi ancak henüz bir davet kullanılmadı.

- Dış Azure Active Directory: Bu Kullanıcı bir dış kuruluşta bulunur ve diğer kuruluşa ait bir Azure AD hesabı kullanarak kimlik doğrular. Bu tür bir oturum açma, 1. duruma karşılık gelir.

- Microsoft hesabı: Bu Kullanıcı bir Microsoft hesabı bağlı ve bir Microsoft hesabı kullanarak kimlik doğrular. Bu tür bir oturum açma, durum 2 ' ye karşılık gelir.

- Windows Server Active Directory: Bu Kullanıcı, bu kuruluşa ait olan şirket içi Active Directory oturum açtı. Bu tür bir oturum açma, durum 3 ' e karşılık gelir.

- Azure Active Directory: Bu Kullanıcı, bu kuruluşa ait bir Azure AD hesabı kullanarak kimlik doğrular. Bu tür bir oturum açma, durum 4 ' e karşılık gelir.
  > [!NOTE]
  > Kaynak ve UserType bağımsız özelliklerdir. Kaynak değeri, UserType için belirli bir değeri göstermez.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Azure AD B2B kullanıcıları Konuk yerine üye olarak eklenebilir mi?
Genellikle, bir Azure AD B2B kullanıcısı ve Konuk Kullanıcı eşanlamlı olarak anlamlıdır. Bu nedenle, bir Azure AD B2B işbirliği kullanıcısı varsayılan olarak UserType = konuğa sahip bir kullanıcı olarak eklenir. Ancak, bazı durumlarda iş ortağı organizasyonu, ana bilgisayar kuruluşunun de ait olduğu daha büyük bir kuruluşun üyesidir. Öyleyse, ana bilgisayar organizasyonu iş ortağı kuruluştaki kullanıcıları Konuk yerine üye olarak değerlendirmek isteyebilir. Bir kullanıcıyı iş ortağı kuruluştan üye olarak konak kuruluşa eklemek veya davet etmek için Azure AD B2B davet Yöneticisi API 'Lerini kullanın.

## <a name="filter-for-guest-users-in-the-directory"></a>Dizindeki Konuk kullanıcılar için filtre

![Konuk kullanıcılar için filtreyi gösteren ekran görüntüsü](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>UserType öğesini Dönüştür
UserType öğesini üye türünden konuğa, PowerShell kullanarak da tam tersi dönüştürmek mümkündür. Ancak, UserType özelliği kullanıcının kuruluşla ilişkisini temsil eder. Bu nedenle, bu özelliği yalnızca kullanıcının kuruluşa ilişkisi değişirse değiştirmelisiniz. Kullanıcı ilişkisi değişirse, Kullanıcı asıl adı (UPN) değişsin mi? Kullanıcı aynı kaynaklara erişime sahip olmaya devam etmelidir mi? Posta kutusu atanmalıdır mi? PowerShell kullanarak bir atomik etkinlik olarak UserType 'ın değiştirilmesini önermiyoruz. Ayrıca, bu özelliğin PowerShell kullanılarak sabit hale gelmesi durumunda bu değere bir bağımlılık alınması önerilmez.

## <a name="remove-guest-user-limitations"></a>Konuk Kullanıcı sınırlamalarını kaldır
Konuk kullanıcılarınıza daha fazla ayrıcalık vermek istediğiniz durumlar olabilir. Bir kullanıcı için herhangi bir role Konuk kullanıcı ekleyebilir ve hatta aynı ayrıcalıkları üyelere üye olarak vermek için dizindeki varsayılan Konuk Kullanıcı kısıtlamalarını kaldırabilirsiniz.

Şirket dizinindeki bir Konuk kullanıcının üye kullanıcıyla aynı izinlere sahip olması için varsayılan sınırlamaları devre dışı bırakmak mümkündür.

![Kullanıcı ayarlarındaki dış kullanıcılar seçeneğini gösteren ekran görüntüsü](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Konuk kullanıcıları Exchange genel adres listesinde görünür yapabilir miyim?
Evet. Varsayılan olarak, Konuk nesneler kuruluşunuzun genel adres listesinde görünmez, ancak Azure Active Directory PowerShell kullanarak bunları görünür hale getirebilirsiniz. Ayrıntılar için [Microsoft 365 grup başına Konuk erişim makalesindeki](/microsoft-365/solutions/per-group-guest-access)"küresel adres listesine Konuk ekleme" konusuna bakın.

## <a name="can-i-update-a-guest-users-email-address"></a>Konuk kullanıcının e-posta adresini güncelleştirebilir miyim?

Bir Konuk Kullanıcı davetinizi kabul ediyorsa ve daha sonra e-posta adreslerini değiştirmişse yeni e-posta, dizininizde Konuk kullanıcı nesnesiyle otomatik olarak eşitlenmez. Mail özelliği [MICROSOFT Graph API](/graph/api/resources/user)aracılığıyla oluşturulur. Posta özelliğini Microsoft Graph API, Exchange Yönetim Merkezi veya [Exchange Online PowerShell](/powershell/module/exchange/users-and-groups/set-mailuser)aracılığıyla güncelleştirebilirsiniz. Bu değişiklik Azure AD Konuk kullanıcı nesnesine yansıtılır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
* [B2B işbirliği Kullanıcı belirteçleri](user-token.md)
* [B2B işbirliği Kullanıcı talepleri eşleme](claims-mapping.md)
