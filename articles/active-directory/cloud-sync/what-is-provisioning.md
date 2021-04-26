---
title: Azure AD ile kimlik sağlama nedir? | Microsoft Belgeleri
description: Kimlik sağlamaya genel bakış açıklanmaktadır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1c85f2a6d58a5dbeae93b951cea812d6aa91326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614846"
---
# <a name="what-is-identity-provisioning"></a>Kimlik sağlama nedir?

Bugün, işletmeler ve şirketler, şirket içi ve bulut uygulamalarının daha fazla ve daha fazlasını haline geliyor.  Kullanıcıların hem şirket içinde hem de bulutta uygulamalara erişmesi gerekir. Bu çeşitli uygulamalar (Şirket içi ve bulut) genelinde tek bir kimliğe sahip olması gerekir.

Sağlama, belirli koşullara göre nesne oluşturma, nesneyi güncel tutma ve koşullar artık karşılanmadıklarından nesneyi silme işlemidir. Örneğin, yeni bir Kullanıcı kuruluşunuza katıldığında, bu kullanıcı HR sistemine girilir.  Bu noktada, sağlama bulutta karşılık gelen bir kullanıcı hesabı, Active Directory ve kullanıcının erişmesi gereken farklı uygulamalar oluşturabilir.  Bu, kullanıcının çalışmaya başlamasını ve gün içinde ihtiyaç duydukları uygulamalara ve sistemlere erişmesini sağlar. 

![Azure Active Directory ile bulut sağlamayı gösteren diyagram.](media/what-is-provisioning/cloud-1.png)

Azure Active Directory açısından, sağlama aşağıdaki temel senaryolara ayrılabilir.  

- **[HR odaklı sağlama](#hr-driven-provisioning)**  
- **[Uygulama sağlama](#app-provisioning)**  
- **[Dizin sağlama](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>HR odaklı sağlama

![Bulut HR, şirket içi HR ve Azure Active Directory ile ık odaklı sağlamayı gösteren diyagram.](media/what-is-provisioning/cloud-2.png)

HR 'dan buluta sağlama, HR sisteminizdeki bilgilere göre nesnelerin (kullanıcılar, roller, gruplar, vb.) oluşturulmasını içerir.  

En yaygın senaryo, yeni bir çalışan şirketinize katıldığında bu durumda HR sistemine girilmiştir.  Bu durumda, bulut tarafından sağlanır.  Bu durumda, Azure AD.  HR 'dan sağlama aşağıdaki senaryoları kapsayabilir. 

- **Yeni çalışanların işe** Alım-bulut HR 'a yeni bir çalışan eklendiğinde, Active Directory, Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve Azure AD tarafından desteklenen diğer SaaS uygulamalarında bir kullanıcı hesabı otomatik olarak oluşturulur. Bu, e-posta adresi Ile bulut HR 'ye geri yazılır.
- **Çalışan özniteliği ve profil güncelleştirmeleri** -bir çalışan kaydı bulut HR (ad, başlık veya yönetici gibi) güncelleştirildiğinde, kullanıcı hesabı Active Directory, Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve Azure AD tarafından desteklenen diğer SaaS uygulamalarında otomatik olarak güncelleştirilir.
- **Çalışan sonlandırmaları** -bulut HR ' de bir çalışan sonlandırıldığında, kullanıcı hesapları Active Directory, Azure Active Directory ve isteğe bağlı olarak Office 365 ve Azure AD tarafından desteklenen diğer SaaS uygulamalarında otomatik olarak devre dışı bırakılır.
- **Çalışan rehires** -bir çalışan bulut HR 'da yeniden çalıştırıldığında, eski hesapları Active Directory, Azure Active Directory ve isteğe bağlı olarak Microsoft 365 ve Azure AD tarafından desteklenen diğer SaaS uygulamaları için otomatik olarak yeniden etkinleştirilebilir veya yeniden sağlanabilir.


## <a name="app-provisioning"></a>Uygulama sağlama

![Şirket içi uygulamalar, Microsoft dışı bulut uygulamaları ve Azure Active Directory Uygulama sağlamayı gösteren diyagram.](media/what-is-provisioning/cloud-3.png)

Azure Active Directory (Azure AD) ' de, **[uygulama sağlama](../app-provisioning/user-provisioning.md)** terimi, kullanıcıların erişmesi gereken bulut uygulamalarında kullanıcı kimliklerinin ve rollerinin otomatik olarak oluşturulmasını ifade eder. Otomatik sağlama, kullanıcı kimliklerinin oluşturulmasına ek olarak, durum veya rol değişikliği olarak Kullanıcı kimliklerinin bakımını ve kaldırılmasını içerir. Yaygın senaryolar, bir Azure AD kullanıcısını [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)gibi uygulamalarda sağlamayı içerir.

## <a name="directory-provisioning"></a>Dizin sağlama

![bulut sağlama](media/what-is-provisioning/cloud-4.png)

Şirket içi sağlama, şirket içi kaynaklardan (Active Directory benzer) Azure AD 'ye sağlamayı içerir.  

En yaygın senaryo, Active Directory (AD) ' deki bir Kullanıcı Azure AD 'ye sağlandığında olur.

Bu, bulut sağlama ve Microsoft Identity Manager Azure AD Connect Azure AD Connect eşitleme tarafından gerçekleştirilir. 
 
## <a name="next-steps"></a>Sonraki adımlar 

- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)
- [Bulut sağlamasını yükler](how-to-install.md)