---
title: Uygulama Yöneticisi Yönetim yönetimi için temsilci seçme-Azure AD | Microsoft Docs
description: Azure Active Directory 'de uygulama erişim yönetimi için izin verme
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 461ee1fc73448f16ba68850d6137fb396c658abd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024866"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Azure Active Directory 'de uygulama kayıt izinleri verme

Bu makalede, uygulama yönetimi ihtiyaçlarınızı karşılamak üzere Azure Active Directory (Azure AD) içinde özel roller tarafından verilen izinlerin nasıl kullanılacağı açıklanır. Azure AD 'de, uygulama oluşturma ve yönetim izinlerinin aşağıdaki yollarla temsilciliğini sağlayabilirsiniz:

- [Kimlerin uygulama oluşturup oluşturdukları uygulamaları yönetebileceğini sınırlandırma](#restrict-who-can-create-applications) . Azure AD 'de varsayılan olarak, tüm kullanıcılar uygulama kayıtlarını kaydedebilir ve oluşturdukları uygulamaların tüm yönlerini yönetebilir. Bu, yalnızca izin verilen kullanıcılara izin vermek için kısıtlanabilir.
- Bir [uygulamaya bir veya daha fazla sahip atama](#assign-application-owners). Bu, birine belirli bir uygulama için Azure AD yapılandırmasının tüm yönlerini yönetme olanağı sağlamanın basit bir yoludur.
- Tüm uygulamalar için Azure AD 'de yapılandırmayı yönetme erişimi veren [yerleşik bir yönetim rolü atama](#assign-built-in-application-admin-roles) . Bu, BT uzmanlarına, Azure AD 'nin uygulama yapılandırmasıyla ilgili olmayan diğer kısımlarını yönetmek için erişim izni vermeden, geniş uygulama yapılandırma izinlerini yönetmek üzere erişim vermek için önerilen yoldur.
- [Özel bir rol oluşturmak](#create-and-assign-a-custom-role-preview) ve bunu, tek bir uygulamanın sınırlı bir sahip olarak veya dizin kapsamında (tüm uygulamalar) sınırlı yönetici olarak bir kişiye atamak.

Yukarıdaki yöntemlerden birini kullanarak iki nedenden dolayı erişim vermek önemlidir. İlk olarak, yönetim görevlerini gerçekleştirmek için temsilci seçme, genel yönetici ek yükünü azaltır. İkincisi, sınırlı izinleri kullanmak güvenlik duruşunuzu iyileştirir ve yetkisiz erişim potansiyelini azaltır. Temsilci seçme sorunları ve genel yönergeler, [Azure Active Directory sürümünde temsilci yönetimi](roles-concept-delegation.md)bölümünde ele alınmıştır.

## <a name="restrict-who-can-create-applications"></a>Kimlerin uygulama oluşturacağını kısıtla

Azure AD 'de varsayılan olarak, tüm kullanıcılar uygulama kayıtlarını kaydedebilir ve oluşturdukları uygulamaların tüm yönlerini yönetebilir. Herkes Ayrıca şirket verilerine kendi adına erişen uygulamalar için izin verebilir. Genel anahtarları ' Hayır ' olarak ayarlayarak ve seçilen kullanıcıları uygulama geliştirici rolüne ekleyerek, bu izinleri seçmeli olarak vermeyi tercih edebilirsiniz.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Uygulama kayıtları veya uygulamalara onay oluşturma için varsayılan özelliği devre dışı bırakmak için

1. Azure AD kuruluşunuzda genel yönetici rolüne uygun bir hesapla Azure AD kuruluşunuzda oturum açın.
1. Aşağıdakilerden birini veya her ikisini birden ayarlayın:

    -  [Kuruluşunuzun Kullanıcı ayarları sayfasında](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings), **kullanıcılar uygulamaları kaydedebilir** ayarını Hayır olarak ayarlayın. Bu, kullanıcıların uygulama kayıtları oluşturmalarına yönelik varsayılan özelliği devre dışı bırakır.
    -  [Kurumsal uygulamalar için Kullanıcı ayarları](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)' na, **kullanıcıların kendi adına şirket verilerine erişen uygulamalara Izin verebilir** ayarını Hayır olarak ayarlayın. Bu, kullanıcıların kendi adına şirket verilerine erişen uygulamalara izin vermesini sağlayacak varsayılan özelliği devre dışı bırakır.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Varsayılan özellik devre dışı bırakıldığında uygulamaları oluşturmak ve kabul etmek için ayrı ayrı izinler verin

**Kullanıcıların uygulamaları kaydedebileceği** durumlarda uygulama kayıtları oluşturma özelliği Hayır olarak ayarlanırsa uygulama geliştirici rolünü atayın. Bu rol Ayrıca, **Kullanıcılar, Kullanıcı adına şirket verilerine erişen uygulamalara izin verebilmeleri** durumunda kendi adına izin vermek için izin verir. Bir sistem davranışı olarak, bir Kullanıcı yeni bir uygulama kaydı oluşturduğunda, bunlar otomatik olarak ilk sahip olarak eklenir. Sahiplik izinleri, kullanıcıya sahip oldukları bir uygulama kaydı veya kurumsal uygulamanın tüm yönlerini yönetme olanağı sunar.

## <a name="assign-application-owners"></a>Uygulama sahipleri atama

Sahipleri atamak, belirli bir uygulama kaydı veya kurumsal uygulama için Azure AD yapılandırmasının tüm yönlerini yönetme olanağı sağlamanın basit bir yoludur. Bir sistem davranışı olarak, bir Kullanıcı yeni bir uygulama kaydı oluşturduğunda ilk sahip olarak otomatik olarak eklenir. Sahiplik izinleri, kullanıcıya sahip oldukları bir uygulama kaydı veya kurumsal uygulamanın tüm yönlerini yönetme olanağı sunar. Özgün sahip kaldırılabilir ve ek sahipler eklenebilir.

### <a name="enterprise-application-owners"></a>Kurumsal uygulama sahipleri

Bir Kullanıcı bir sahip olarak, kurumsal uygulama için çoklu oturum açma yapılandırması, sağlama ve Kullanıcı atamaları gibi kuruluşa özgü yapılandırmayı yönetebilir. Sahipler başka kullanıcıları sahip olarak ekleyebilir veya kaldırabilir. Küresel yöneticilerin aksine, sahipler yalnızca sahip oldukları kurumsal uygulamaları yönetebilir.

Bazı durumlarda, uygulama galerisinden oluşturulan kurumsal uygulamalarda hem kurumsal uygulama hem de uygulama kaydı bulunur. Bu doğru olduğunda, kurumsal uygulamaya sahip eklemek, sahibi bir sahip olarak ilgili uygulama kaydına otomatik olarak ekler.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Bir kuruluş uygulamasına sahip atamak için

1. [Azure AD kuruluşunuzda](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) , uygulama yöneticisine veya kuruluşun bulut uygulama yöneticisine uygun bir hesapla oturum açın.
1. Kuruluşun  [uygulama kayıtları sayfasında](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) , uygulamanın genel bakış sayfasını açmak için bir uygulama seçin.
1. Uygulama sahiplerinin listesini görmek için **sahipler** ' i seçin.
1. Uygulamaya eklenecek bir veya daha fazla sahip seçmek için **Ekle** ' yi seçin.

> [!IMPORTANT]
> Kullanıcılar ve hizmet sorumluları, uygulama kayıtlarının sahipleri olabilir. Yalnızca kullanıcılar kurumsal uygulamaların sahipleri olabilir. Gruplar, birinin sahipleri olarak atanamaz.
>
> Sahipler bir uygulamaya kimlik bilgileri ekleyebilir ve bu kimlik bilgilerini uygulamanın kimliğini taklit etmek için kullanabilir. Uygulamanın sahibinden daha fazla izni olabilir ve bu nedenle, sahibin Kullanıcı veya hizmet sorumlusu olarak erişimine sahip olduğu konusunda ayrıcalık yükselmesine neden olabilir. Uygulama sahibi, uygulamanın izinlerine bağlı olarak uygulamayı taklit ederken kullanıcıları veya diğer nesneleri oluşturabilir ya da güncelleştirebilir.

## <a name="assign-built-in-application-admin-roles"></a>Yerleşik uygulama yöneticisi rolleri atama

Azure AD 'nin tüm uygulamalar için Azure AD 'de yapılandırmayı yönetme erişimi vermek üzere yerleşik bir yönetim rolleri kümesi vardır. Bu roller, BT uzmanlarına, Azure AD 'nin uygulama yapılandırmasıyla ilgili olmayan diğer kısımlarını yönetmek için erişim izni vermeden, geniş uygulama yapılandırma izinlerini yönetmek için önerilen yoldur.

- Uygulama Yöneticisi: Bu roldeki kullanıcılar kurumsal uygulamaların, uygulama kayıtlarının ve uygulama proxy ayarlarının tüm yönlerini oluşturabilir ve yönetebilir. Bu rol Ayrıca, Microsoft Graph ve Azure AD grafiğini dışlayarak uygulama izinleri için izin verme izni verir. Bu role atanan kullanıcılar, yeni uygulama kayıtları veya kurumsal uygulamalar oluştururken sahip olarak eklenmez.
- Bulut uygulaması Yöneticisi: Bu roldeki kullanıcılar uygulama proxy 'si rolüyle aynı izinlere sahiptir ve uygulama ara sunucusunu yönetme imkanını dışlar. Bu role atanan kullanıcılar, yeni uygulama kayıtları veya kurumsal uygulamalar oluştururken sahip olarak eklenmez.

Daha fazla bilgi edinmek ve bu rollerin açıklamasını görüntülemek için bkz. [kullanılabilir roller](directory-assign-admin-roles.md#available-roles).

Uygulama Yöneticisi veya bulut uygulama yöneticisi rollerini atamak için Azure Active Directory nasıl yapılır Kılavuzu [ile kullanıcılara roller atama](../fundamentals/active-directory-users-assign-role-azure-portal.md) bölümündeki yönergeleri izleyin.

> [!IMPORTANT]
> Uygulama yöneticileri ve bulut uygulaması yöneticileri bir uygulamaya kimlik bilgileri ekleyebilir ve bu kimlik bilgilerini uygulamanın kimliğini taklit etmek için kullanabilir. Uygulamanın, yönetici rolü izinleri üzerinde ayrıcalık yükseltmesi olan izinleri olabilir. Bu roldeki bir yönetici, uygulamanın izinlerine bağlı olarak uygulamayı taklit ederken kullanıcıları veya diğer nesneleri oluşturabilir ya da güncelleştirebilir.
> Hiçbiri rol, koşullu erişim ayarlarını yönetme olanağı vermez.

## <a name="create-and-assign-a-custom-role-preview"></a>Özel bir rol oluşturma ve atama (Önizleme)

Özel Roller oluşturmak ve özel roller atamak ayrı adımlardır:

- [Özel bir *rol tanımı* oluşturun](roles-create-custom.md) ve [önceden ayarlanmış bir listeden izinleri ekleyin](roles-custom-available-permissions.md). Bunlar, yerleşik rollerde kullanılan izinlerdir.
- Özel rolü atamak için [bir *rol ataması* oluşturun](roles-assign-powershell.md) .

Bu ayrım, tek bir rol tanımı oluşturmanızı ve sonra farklı *kapsamlara*birçok kez atamanızı sağlar. Özel bir rol kuruluş genelinde bir kapsamda atanabilir veya tek bir Azure AD nesnesi ise kapsamda atanabilir. Bir nesne kapsamına örnek olarak tek bir uygulama kaydı vardır. Farklı kapsamları kullanarak, aynı rol tanımı kuruluştaki tüm uygulama kayıtları üzerinden Sally ve ardından yalnızca contoso gider raporları uygulama kaydı üzerinden değiştirilebilir.

Uygulama yönetiminin yetkisini oluşturmak için özel roller oluşturma ve kullanma ipuçları:
- Özel roller yalnızca Azure AD portalının en güncel uygulama kayıt dikey pencerelerinde erişim izni verir. Bunlar, eski uygulama kayıtları dikey pencerelerinde erişim vermez.
- Özel roller, "Azure AD Yönetim portalına erişimi kısıtla" Kullanıcı ayarı Evet olarak ayarlandığında Azure AD portalına erişim izni vermez.
- Kullanıcı, rol atamalarını kullanma erişimine sahip Uygulama kayıtları yalnızca uygulama kayıt sayfasındaki ' tüm uygulamalar ' sekmesinde görünür. Bunlar, ' sahip olan uygulamalar ' sekmesinde gösterilmez.

Özel rollerin temelleri hakkında daha fazla bilgi için bkz. [özel rollere genel bakış](roles-custom-overview.md)ve [özel rol oluşturma](roles-create-custom.md) ve [rol atama](roles-assign-powershell.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama kaydı alt türleri ve izinleri](roles-custom-available-permissions.md)
- [Azure AD yönetici rolü başvurusu](directory-assign-admin-roles.md)
