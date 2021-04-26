---
title: Azure Active Directory 'de yönetim birimleri | Microsoft Docs
description: Azure Active Directory izinlerin daha ayrıntılı şekilde temsili için yönetim birimleri kullanın.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49f2c290c69fcadd594d6cbd5879e7d9f5304a42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558024"
---
# <a name="administrative-units-in-azure-active-directory"></a>Azure Active Directory 'de yönetim birimleri

Bu makalede Azure Active Directory (Azure AD) içindeki yönetim birimleri açıklanmaktadır. Yönetim birimi, diğer Azure AD kaynakları için bir kapsayıcı olabilecek bir Azure AD kaynağıdır. Yönetim birimi yalnızca kullanıcılar ve gruplar içerebilir.

Yönetim birimleri, bir roldeki izinleri sizin tanımladığınız kuruluşunuzun herhangi bir bölümüne kısıtlar. Örneğin, [Yardım Masası yönetici](permissions-reference.md#helpdesk-administrator) rolünü bölgesel destek uzmanlarına devretmek için yönetim birimlerini kullanabilir, böylece yalnızca destekledikleri bölgedeki kullanıcıları yönetebilirler.

## <a name="deployment-scenario"></a>Dağıtım senaryosu

Her türlü bağımsız bölüm oluşturan kuruluşlarda yönetim birimlerini kullanarak yönetim kapsamını kısıtlamak yararlı olabilir. Birçok otonom okuldan (Iş Okulu, mühendisin Okulu vb.) oluşan büyük bir üniversite örneğini göz önünde bulundurun. Her okulda, erişimi denetleyen, kullanıcıları yöneten ve okullarına yönelik ilkeler kuran bir BT yöneticileri ekibi vardır.

Merkezi yönetici:

- İş okul yönetim birimindeki yalnızca Azure AD kullanıcıları üzerinde yönetici izinlerine sahip bir rol oluşturun.
- Iş Okulu için bir yönetim birimi oluşturun.
- Yönetim birimini yalnızca iş okul öğrencilerine ve personeline göre doldurun.
- İş Okulu BT ekibini, kapsamıyla birlikte rolüne ekleyin.

## <a name="license-requirements"></a>Lisans gereksinimleri

Yönetim birimlerini kullanmak için, her yönetim birimi Yöneticisi için bir Azure Active Directory Premium lisansa ve yönetim birimi üyeleri için Azure Active Directory Ücretsiz lisanslarına sahip olmanız gerekir. Daha fazla bilgi için bkz. [Azure AD Premium kullanmaya](../fundamentals/active-directory-get-started-premium.md)başlama.

## <a name="manage-administrative-units"></a>Yönetim birimlerini yönetme

Azure portal, PowerShell cmdlet 'leri ve betikleri veya Microsoft Graph kullanarak yönetim birimlerini yönetebilirsiniz. Daha fazla bilgi için bkz.

- [Yönetim birimlerine roller oluşturma, kaldırma, doldurma ve ekleme](admin-units-manage.md): tüm nasıl yapılır yordamlarını içerir.
- [Yönetim birimleriyle çalışma](/powershell/azure/active-directory/working-with-administrative-units): PowerShell kullanarak yönetim birimleriyle çalışmayı ele alır.
- [Yönetim birimi grafik desteği](/graph/api/resources/administrativeunit): yönetim birimleri için Microsoft Graph ayrıntılı belgeler sağlar.

### <a name="plan-your-administrative-units"></a>Yönetim birimlerinizi planlayın

Azure AD kaynaklarını mantıksal olarak gruplandırabilmeniz için yönetim birimlerini kullanabilirsiniz. BT departmanı Global olarak dağınık olan bir kuruluş, ilgili coğrafi sınırları tanımlayan yönetim birimleri oluşturabilir. Genel bir kuruluşun işlemlerinde yarı otonom olan alt kuruluşların bulunduğu başka bir senaryoda, yönetim birimleri alt kuruluşları temsil ediyor olabilir.

Yönetim birimlerinin oluşturulduğu ölçütler, bir kuruluşun benzersiz gereksinimlerine göre yapılır. Yönetim birimleri Microsoft 365 hizmetleri genelinde yapıyı tanımlamanın yaygın bir yoludur. Yönetim birimlerinizi, Microsoft 365 hizmetleri genelinde kullanımı göz önüne alarak hazırlamanızı öneririz. Genel kaynakları bir yönetim birimi altında Microsoft 365 arasında ilişkilendirebileceğiniz zaman, yönetim birimlerinden maksimum değeri alabilirsiniz.

Aşağıdaki aşamaları izleyerek kuruluştaki yönetim birimlerinin oluşturulmasını bekleyebilir:

1. **İlk benimseme**: Kuruluşunuz, ilk ölçütlere göre yönetim birimleri oluşturmaya başlar ve ölçütler iyileştirilyerek yönetim birimlerinin sayısı artacaktır.
1. **Ayıklama**: ölçütler tanımlandıktan sonra artık gerekli olmayan yönetim birimleri silinir.
1. **Sabitlemeyi**: Kurumsal yapınız tanımlanmıştır ve kısa dönemde yönetim birimlerinin sayısı önemli ölçüde değişmeyeceklerdir.

## <a name="currently-supported-scenarios"></a>Şu anda desteklenen senaryolar

Genel yönetici veya ayrıcalıklı rol yöneticisi olarak, Azure AD portalını kullanarak şunları yapabilirsiniz:

- Yönetim birimleri oluşturma
- Yönetim birimlerinin Kullanıcı ve grup üyelerini ekleyin
- BT personelini yönetim birimi kapsamındaki yönetici rollerine atayın.

Yönetim birimi kapsamlı Yöneticiler, kullanıcıların yönetim birimlerindeki temel yönetimi için Microsoft 365 Yönetim merkezini kullanabilir. Yönetim birimi kapsamına sahip bir Grup Yöneticisi, PowerShell, Microsoft Graph ve Microsoft 365 Yönetim merkezlerini kullanarak grupları yönetebilir.

>[!Note]
>Yalnızca bu bölümde açıklanan özellikler Microsoft 365 Yönetim merkezinde mevcuttur. Yönetim birimi kapsamına sahip bir Azure AD rolü için kuruluş düzeyindeki özellikler kullanılamaz.

Aşağıdaki bölümlerde, yönetim birimi senaryoları için geçerli destek açıklanır.

### <a name="administrative-unit-management"></a>Yönetim Birimi Yönetimi

| İzinler |   Grafik/PowerShell   | Azure AD portalı | Microsoft 365 yönetici merkezi |
| --- | --- | --- | --- |
| Yönetim birimleri oluşturma ve silme   |    Desteklenir    |   Desteklenir   |    Desteklenmez |
| Yönetim birimi üyelerini tek tek ekleme ve kaldırma    |   Desteklenir    |   Desteklenir   |    Desteklenmez |
| CSV dosyalarını kullanarak yönetim birimi üyelerini toplu olarak ekleme ve kaldırma   |    Desteklenmez     |  Desteklenir   |    Destekedilecek plan yok |
| Yönetim birimi kapsamlı Yöneticiler atama  |     Desteklenir    |   Desteklenir    |   Desteklenmez |
| Öznitelikleri temel alarak yönetim birimi üyelerini dinamik olarak ekleme ve kaldırma | Desteklenmez | Desteklenmez | Desteklenmez

### <a name="user-management"></a>Kullanıcı yönetimi

| İzinler |   Grafik/PowerShell   | Azure AD portalı | Microsoft 365 yönetici merkezi |
| --- | --- | --- | --- |
| Kullanıcı özelliklerinin, parolalarının ve lisansların yönetim birimi kapsamlı yönetimi   |    Desteklenir     |  Desteklenir   |   Desteklenir |
| Yönetim birimi kapsamlı engelleme ve Kullanıcı oturum açma işlemleri engellemesini kaldırma    |   Desteklenir   |    Desteklenir   |    Desteklenir |
| Kullanıcı çok faktörlü kimlik doğrulaması kimlik bilgilerinin yönetim birimi kapsamlı yönetimi   |    Desteklenir   |   Desteklenir   |   Desteklenmez |

### <a name="group-management"></a>Grup yönetimi

| İzinler |   Grafik/PowerShell   | Azure AD portalı | Microsoft 365 yönetici merkezi |
| --- | --- | --- | --- |
| Grup özelliklerinin ve üyelerinin yönetim birimi kapsamlı yönetimi     |  Desteklenir   |    Desteklenir    |  Desteklenmez |
| Grup lisanslamanın yönetim birimi kapsamlı yönetimi   |    Desteklenir  |    Desteklenir   |   Desteklenmez |

Yönetim birimleri kapsamı yalnızca yönetim izinlerine uygular. Üyelerin veya yöneticilerin, yönetim birimi dışındaki diğer kullanıcılara, gruplara veya kaynaklara gözatmaları için [Varsayılan Kullanıcı izinlerini](../fundamentals/users-default-permissions.md) kullanmalarını engellemez. Microsoft 365 Yönetim merkezinde, kapsamlı yönetici yönetim birimlerinin dışındaki kullanıcılar filtrelenmez. Ancak Azure AD portalı, PowerShell ve diğer Microsoft hizmetlerinde diğer kullanıcılara da gidebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Yönetim birimlerini yönetme](admin-units-manage.md)
- [Yönetici birimlerindeki kullanıcıları yönetme](admin-units-add-manage-users.md)
- [Grupları yönetim birimlerinde yönetme](admin-units-add-manage-groups.md)
- [Yönetim birimine kapsamlı roller atama](admin-units-assign-roles.md)
