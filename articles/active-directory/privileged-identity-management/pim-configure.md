---
title: Privileged Identity Management nedir? -Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) için genel bir bakış sağlar.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578f45a7bc870874319a8238c7c8c50bf7a37998
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023016"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management nedir?

Azure Active Directory (Azure AD) Privileged Identity Management (PıM), kuruluşunuzdaki önemli kaynaklara erişimi yönetmenizi, denetlemenizi ve izlemenizi sağlayan bir hizmettir. Bu kaynaklar Azure AD, Azure ve Office 365 veya Microsoft Intune gibi diğer Microsoft çevrimiçi hizmetlerindeki kaynakları içerir.

## <a name="reasons-to-use"></a>Kullanım nedenleri

Kuruluşlar, kötü niyetli bir aktörün bu erişimi veya yetkili bir kullanıcının yanlışlıkla hassas bir kaynağı etkileme olasılığını azalttığından, güvenli bilgi veya kaynaklara erişimi olan kişilerin sayısını en aza indirmek ister. Ancak kullanıcıların Azure AD, Azure, Office 365 veya SaaS uygulamalarında yine de ayrıcalıklı işlemler gerçekleştirmesi gerekir. Kuruluşlar, kullanıcıların Azure kaynaklarına ve Azure AD 'ye tam zamanında (JıT) ayrıcalıklı erişim izni verebilir. Bu kullanıcıların yönetici ayrıcalıklarıyla ne yaptığına ilişkin daha fazla bakış için bir gereksinim vardır.

## <a name="what-does-it-do"></a>Uygulama ne işe yarıyor?

Privileged Identity Management, önem verdiğiniz kaynaklarda aşırı, gereksiz veya kötüye erişim izinlerinin riskini azaltmak için zamana dayalı ve onay tabanlı rol etkinleştirmesi sağlar. Privileged Identity Management temel özelliklerinden bazıları şunlardır:

- Azure AD ve Azure kaynaklarına **tam zamanında** ayrıcalıklı erişim sağlama
- Başlangıç ve bitiş tarihlerini kullanarak kaynaklara **zamana bağlıysa** erişim atama
- Ayrıcalıklı rolleri etkinleştirmek için **onay** gerektir
- Herhangi bir rolü etkinleştirmek için **Multi-Factor Authentication** 'ı zorlayın
- Kullanıcıların neden etkinleştireceğinize anlamak için **gerekçe** kullanın
- Ayrıcalıklı roller etkinleştirildiğinde **bildirim** alın
- Kullanıcıların hala rollere ihtiyacı olduğundan emin olmak için **erişim gözden geçirmeleri** gerçekleştirin
- İç veya dış denetim için **denetim geçmişini** indirin

## <a name="what-can-i-do-with-it"></a>Bununla ne yapabilirim?

Privileged Identity Management ayarladıktan sonra sol gezinti menüsünde **Görevler**, **Yönet**ve **etkinlik** seçeneklerini görürsünüz. Yönetici olarak, **Azure AD rollerini** ve **Azure Kaynak** rollerini yönetme arasında seçim yapabilirsiniz. Yönetilecek rol türünü seçtiğinizde, bu rol türü için benzer bir seçenek kümesi görürsünüz.

![Azure portal Privileged Identity Management ekran görüntüsü](./media/pim-configure/pim-overview.png)

## <a name="who-can-do-what"></a>Kimler ne yapabilir?

Privileged Identity Management ilk kişiyseniz, otomatik olarak dizinde [Güvenlik Yöneticisi](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) ve [ayrıcalıklı rol yöneticisi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) rolleri atanır.

Privileged Identity Management Azure AD rolleri için, yalnızca ayrıcalıklı rol yöneticisi rolünde olan bir kullanıcı diğer yöneticiler için atamaları yönetebilir. [Privileged Identity Management yönetmek için diğer yöneticilere erişim izni](pim-how-to-give-access-to-pim.md)verebilirsiniz. Genel Yöneticiler, güvenlik yöneticileri, küresel okuyucular ve güvenlik okuyucuları Ayrıca Privileged Identity Management Azure AD rollerine atamaları görüntüleyebilir.

Privileged Identity Management 'daki Azure Kaynak rolleri için, yalnızca bir abonelik Yöneticisi, bir kaynak sahibi veya bir kaynak Kullanıcı erişimi Yöneticisi, diğer yöneticiler için atamaları yönetebilir. Ayrıcalıklı rol yöneticileri, güvenlik yöneticileri veya güvenlik okuyucuları olan kullanıcılar varsayılan olarak Privileged Identity Management Azure Kaynak rollerine atamaları görüntüleme erişimine sahip değildir.

## <a name="scenarios"></a>Senaryolar

Privileged Identity Management aşağıdaki senaryoları destekler:

### <a name="privileged-role-administrator-permissions"></a>Ayrıcalıklı rol Yöneticisi izinleri

- Belirli roller için onay etkinleştirmek
- İstekleri onaylamak için onaylayan kullanıcıları veya grupları belirtin
- Tüm ayrıcalıklı roller için istek ve onay geçmişini görüntülemek

### <a name="approver-permissions"></a>Onaylayan izinleri

- Bekleyen onayları (istekler) görüntülemek
- Rol yükseltme için istekleri onaylama veya reddetme (tek ve toplu)
- Onay veya reddetme için gerekçe sağlayın

### <a name="eligible-role-user-permissions"></a>Uygun rol Kullanıcı izinleri

- Onay gerektiren bir rolün etkinleştirilmesini istemek
- Etkinleştirme isteğinizin durumunu görüntülemek
- İstek onaylanmışsa Azure AD'deki görevinizi tamamlamak

## <a name="terminology"></a>Terminoloji

Privileged Identity Management ve belgelerini daha iyi anlamak için aşağıdaki koşulları gözden geçirmeniz gerekir.

| Kavram veya sözleşme | Rol atama kategorisi | Açıklama |
| --- | --- | --- |
| lebilecek | Tür | Bir kullanıcının rolü kullanmak için bir veya daha fazla eylem gerçekleştirmesini gerektiren bir rol ataması. Bir Kullanıcı bir rol için uygun hale getirildiğinde, ayrıcalıklı görevleri gerçekleştirmeleri gerektiğinde rolü etkinleştirebilecekleri anlamına gelir. Kalıcı ve uygun bir rol atamasına karşılık gelen bir kişiye verilen erişimde fark yoktur. Tek fark, bazı kişilerin her zaman erişim gereksinimi yoktur. |
| etkin | Tür | Rolü kullanmak için bir kullanıcının herhangi bir eylem gerçekleştirmesini gerektirmeyen bir rol ataması. Etkin olarak atanan kullanıcılara role atanmış ayrıcalıklar vardır. |
| etkinleştirme |  | Kullanıcının uygun olduğu bir rolü kullanmak için bir veya daha fazla eylem gerçekleştirme işlemi. Eylemler, bir Multi-Factor Authentication (MFA) denetimi gerçekleştirmeye, iş gerekçesinin sağlanmasından veya belirlenen onaylayanlardan onay isteğinde bulunabilir. |
| atanan | Durum | Etkin rol ataması olan bir kullanıcı. |
| etkinleştirilemediğini | Durum | Uygun rol atamasına sahip bir Kullanıcı, rolü etkinleştirmek için eylemleri gerçekleştirmiştir ve artık etkindir.  Etkinleştirildikten sonra, Kullanıcı yeniden etkinleştirilmeleri için önceden yapılandırılmış bir süre için rolü kullanabilir. |
| kalıcı uygun | Süre | Kullanıcının her zaman rolü etkinleştirmesine uygun olduğu rol ataması. |
| kalıcı etkin | Süre | Bir kullanıcının herhangi bir eylem yapmadan her zaman rol kullanabileceği rol ataması. |
| kullanım süresi uygun | Süre | Bir kullanıcının belirtilen başlangıç ve bitiş tarihi içinde rolü etkinleştirmesine uygun olduğu rol ataması. |
| etkin süre sonu | Süre | Bir kullanıcının belirtilen bir başlangıç ve bitiş tarihi içinde herhangi bir eylem gerçekleştirmeden rolü kullanabileceği rol ataması. |
| tam zamanında (JıT) erişim |  | Kullanıcıların ayrıcalıklı görevleri gerçekleştirmek için geçici izinleri aldığı, kötü amaçlı veya yetkisiz kullanıcıların izinlerin süre dolduktan sonra erişim kazanmasını önleyen bir model. Erişim yalnızca kullanıcılara ihtiyaç duymadan verilir. |
| en az ayrıcalık erişimi ilkesi |  | Her kullanıcının yalnızca, gerçekleştirme yetkisine sahip oldukları görevleri gerçekleştirmek için gereken en düşük ayrıcalıklarla sağlandığı önerilen bir güvenlik uygulaması. Bu uygulama, genel yönetici sayısını en aza indirir ve bunun yerine belirli senaryolar için belirli yönetici rollerini kullanır. |

## <a name="license-requirements"></a>Lisans gereksinimleri

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Kullanıcılar için lisanslar hakkında daha fazla bilgi için bkz. [Privileged Identity Management kullanılacak lisans gereksinimleri](subscription-requirements.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management kullanılacak lisans gereksinimleri](subscription-requirements.md)
- [Azure AD'de karma ve bulut dağıtımları için ayrıcalıklı erişim güvenliğini sağlama](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management dağıt](pim-deployment-plan.md)
