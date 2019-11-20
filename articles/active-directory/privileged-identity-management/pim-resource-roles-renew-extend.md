---
title: PıM-Azure AD 'de Azure Kaynak rolü atamalarını yenileme | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure Kaynak rol atamalarını genişletmeyi veya yenilemeyi öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 577c028582bc3b23d13e71522bb83db558065ee2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022907"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Privileged Identity Management Azure Kaynak rol atamalarını genişletme veya yenileme

Azure Active Directory (Azure AD) Privileged Identity Management (PıM), Azure kaynakları için erişim ve atama yaşam döngüsünü yönetmek üzere denetimler sağlar. Yöneticiler, başlangıç ve bitiş tarih-saat özelliklerini kullanarak roller atayabilir. Atama sonu yaklaşırsa, Privileged Identity Management etkilenen kullanıcılara veya gruplara e-posta bildirimleri gönderir. Ayrıca, uygun erişimin korunduğundan emin olmak için kaynağın yöneticilerine e-posta bildirimleri gönderir. Atamalar yenilenebilir ve erişim genişletilmese bile 30 güne kadar, zaman aşımına uğradı.

## <a name="who-can-extend-and-renew"></a>Kimler genişletebilir ve yenileyebilirim?

Rol atamalarını yalnızca kaynağın yöneticileri genişletebilir veya yenileyebilirler. Etkilenen Kullanıcı veya Grup, sona ermek üzere olan rolleri genişletmeyi isteyebilir ve zaten kullanım süreleri dolan rolleri yenileme isteğinde bulunabilir.

## <a name="when-are-notifications-sent"></a>Bildirimler ne zaman gönderilir?

Privileged Identity Management, e-posta bildirimlerini kullanıcılara ve süresi dolmadan önce 14 gün içinde ve bir gün içinde süresi dolan rol gruplarını gönderir. Atama resmi olarak sona erdiğinde ek bir e-posta gönderir.

Yöneticiler, bir kullanıcı veya Grup, uzatmak veya yenilemek için süresi dolan veya süresi dolan bir rol isteği atandığında bildirim alır. Belirli bir yönetici isteği çözümlediğinde, diğer tüm yöneticilere çözümleme kararı (onaylanan veya reddedildi) bildirilir. Daha sonra, istenen kullanıcı veya gruba karar bildirilir.

## <a name="extend-role-assignments"></a>Rol atamalarını uzat

Aşağıdaki adımlarda bir rol atamasının uzantısı veya yenilenmesini isteme, çözme veya yönetme işlemi ana hatlarıyla verilmiştir.

### <a name="self-extend-expiring-assignments"></a>Süresi dolan atamaları kendi kendine uzat

Bir role atanan kullanıcılar veya gruplar, süresi dolan rol atamalarını bir kaynağın **rollerim** sayfasındaki **uygun** veya **etkin** sekmesinden veya Privileged Identity Management portalının en üst düzey **Roller** sayfasından genişletebilir. Kullanıcılar veya gruplar, sonraki 14 gün içinde süresi dolan uygun ve etkin (atanan) rolleri genişletmeyi isteyebilir.

![Azure kaynakları-rollerim sayfası bir eylem sütunuyla uygun rolleri listeleme](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Atama bitiş tarihi-saati 14 gün içinde olduğunda, **genişletilecek** düğme Kullanıcı arabirimindeki etkin bir bağlantı haline gelir. Aşağıdaki örnekte, geçerli tarihin 27 Mart olduğunu varsayalım.

![Etkinleştirilecek veya genişletilir bağlantıları olan eylem sütunu](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Bu rol atamasının bir uzantısını istemek için **Genişlet** ' i seçerek istek formunu açın.

![Rol atama bölmesini bir neden kutusuyla Genişlet](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Orijinal atama hakkında bilgi görüntülemek için **atama ayrıntıları**' nı genişletin. Uzantı isteği için bir neden girin ve **Genişlet**' i seçin.

>[!NOTE]
>Uzantının neden gerekli olduğuna ilişkin ayrıntıları ve uzantının ne kadar süreyle verileceğini (Bu bilgilere sahipseniz) dahil etmenizi öneririz.

![Rol atama bölmesini, atama ayrıntıları genişletilmiş olarak Genişlet](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Kaynak yöneticileri, bir süre içinde, uzantı isteğini gözden geçirmesini isteyen bir e-posta bildirimi alır. Genişletme isteği zaten gönderilmezse portalda bir Azure bildirimi görüntülenir.

![Zaten var olan bir bekleyen rol atama uzantısının olduğunu açıklayan bildirim](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

İsteğiniz durumunu görüntülemek veya iptal etmek için **bekleyen istekler** sayfasına gidin.

![Azure kaynakları-bekleyen istenen ve Iptal için bir bağlantının listelendiği bekleyen istekler sayfası](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Yönetici onaylı uzantısı

Bir kullanıcı veya grup bir rol atamasını genişletme isteği gönderdiğinde, kaynak yöneticileri özgün atamanın ayrıntılarını ve isteğin nedenini içeren bir e-posta bildirimi alır. Bildirim, yöneticinin onaylama veya reddetme isteğine doğrudan bir bağlantı içerir.

Yöneticiler, e-postadaki bağlantıyı izleyen bir ek olarak, Privileged Identity Management yönetim portalına giderek ve sol bölmedeki **Istekleri Onayla** ' yı seçerek istekleri onaylayabilir veya reddedebilir.

![Azure kaynakları-istekleri onaylama sayfası istekleri ve onaylama veya reddetme bağlantıları](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Yönetici **Onayla** veya **Reddet**seçeneğini belirlediğinde, istek ayrıntıları, denetim günlükleri için bir iş denetimi sağlamak üzere bir alanla birlikte gösterilir.

![İstek sahibi nedeni, atama türü, başlangıç zamanı, bitiş zamanı ve nedenle rol atama isteğini onaylayın](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Rol atamasını genişletme isteği onaylarken, kaynak yöneticileri yeni bir başlangıç tarihi, bitiş tarihi ve atama türü seçebilir. Yönetici belirli bir görevi (örneğin, bir gün) tamamlamaya sınırlı erişim sağlamak isterse, atama türünü değiştirmek gerekli olabilir. Bu örnekte, yönetici atamayı **uygun** durumundan **etkin**olarak değiştirebilir. Bu, etkinleştirmelerine gerek kalmadan istek sahibine erişim sağlayabilecekleri anlamına gelir.

### <a name="admin-initiated-extension"></a>Yönetici tarafından başlatılan uzantı

Role atanan bir Kullanıcı rol ataması için bir uzantı istemezse, yönetici kullanıcı adına bir atamayı genişletebilir. Rol atamasının yönetim uzantılarında onay gerekmez, ancak bildirimler rol genişletildikten sonra diğer tüm yöneticilere gönderilir.

Rol atamasını genişletmek için Privileged Identity Management kaynak rolüne veya atama görünümüne gidin. Uzantı gerektiren atamayı bulun. Ardından Eylem sütununda **Genişlet** ' i seçin.

![Azure kaynakları-uzatma bağlantılarıyla uygun rolleri listelemeyi içeren atamalar sayfası](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Rol atamalarını Yenile

Kavramsal olarak bir uzantı isteme işlemine benzer olsa da, zaman aşımına uğradı bir rol atamasını yenileme işlemi farklıdır. Aşağıdaki adımları kullanarak, atamalar ve yöneticiler gerektiğinde, zaman aşımına uğradı rollere erişimi yenileyebilirler.

### <a name="self-renew"></a>Kendi kendine yenileme

Artık kaynaklara erişemeyen kullanıcılar, en fazla 30 günlük atama geçmişine erişebilir. Bunu yapmak için, sol bölmedeki **rollerim** 'e gözatıp, sonra da Azure Kaynak rolleri bölümünde **zaman aşımına uğradı roller** sekmesini seçin.

![Rollerim sayfası-zaman aşımına uğradı roller sekmesi](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

Gösterilen roller listesi, varsayılan olarak **uygun rollerdir**. Uygun ve etkin atanan roller arasında geçiş yapmak için açılan menüyü kullanın.

Listedeki rol atamalarından herhangi biri için yenileme **isteğinde bulunan yenileme eylemini seçin** . Ardından istek için bir neden belirtin. Kaynak yöneticisinin onaylama veya reddetme kararı vermesine yardımcı olabilecek ek bağlam veya bir iş gerekçesinin yanı sıra bir süre sağlamak yararlı olur.

![Neden kutusunu gösteren rol atama bölmesini Yenile](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

İstek gönderildikten sonra, kaynak yöneticilerine bir rol atamasını yenilemeye yönelik bekleyen bir istek bildirilir.

### <a name="admin-approves"></a>Yönetici onaylar

Kaynak yöneticileri, e-posta bildiriminde bulunan bağlantıdan veya Azure portal Privileged Identity Management erişerek ve sol bölmeden **Istekleri Onayla** ' yı seçerek yenileme isteğine erişebilir.

![Azure kaynakları-istekleri onaylama sayfası istekleri ve onaylama veya reddetme bağlantıları](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Yönetici **Onayla** veya **Reddet**seçeneğini belirlediğinde, denetim günlükleri için bir iş denetimi sağlamak üzere isteğin ayrıntıları bir alanla birlikte gösterilir.

![İstek sahibi nedeni, atama türü, başlangıç zamanı, bitiş zamanı ve nedenle rol atama isteğini onaylayın](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Rol atamasını yenileme isteği onaylanırken, kaynak yöneticilerinin yeni bir başlangıç tarihi, bitiş tarihi ve atama türü girmesi gerekir.

### <a name="admin-renew"></a>Yönetici yenileme

Kaynak yöneticileri, bir kaynağın sol gezinti menüsündeki **Üyeler** sekmesinden süre dolma rol atamalarını yenileyebilirler. Ayrıca, bir kaynak rolünün **süresi geçen** roller sekmesinden süresi geçen rol atamalarını yenileyebilirler.

Tüm süre dolma rolü atamalarının listesini görüntülemek için, **Üyeler** ekranında, **son kullanma tarihi olan roller**' i seçin.

![Azure kaynakları-roller sayfası, yenileme bağlantıları olan vadesi geçen rolleri listeleme](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rolleri için istekleri onaylama veya reddetme](pim-resource-roles-approval-workflow.md)
- [Privileged Identity Management Azure Kaynak rolü ayarlarını yapılandırma](pim-resource-roles-configure-role-settings.md)
