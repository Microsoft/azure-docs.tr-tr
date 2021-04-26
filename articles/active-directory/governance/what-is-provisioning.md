---
title: Azure Active Directory ile sağlama nedir? | Microsoft Belgeleri
description: Kimlik sağlama ve ıLA senaryolarına genel bakış açıklanmaktadır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 640367d1b833f61e8a83fe9ce6b14d6d799cf9b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172461"
---
# <a name="what-is-provisioning"></a>Sağlama nedir?

Sağlama ve sağlamayı kaldırma, birden fazla sistem genelinde dijital kimliklerin tutarlılığını sağlayan işlemlerdir.  Bu süreçler genellikle [kimlik yaşam döngüsü yönetiminin](what-is-identity-lifecycle-management.md)bir parçası olarak yararlanılabilir.

**Sağlama** , belirli koşullara göre hedef sistemde kimlik oluşturma işlemlerdir.  **Serbest sağlama** , koşulların artık karşılanmamasından sonra hedef sistemden kimlik kaldırma işlemidir. **Eşitleme** , kaynak nesne ve hedef nesnenin benzer olması için sağlanan nesneyi güncel tutma işlemidir.

Örneğin, yeni bir çalışan kuruluşunuza katıldığında bu çalışan, HR sistemine girilir.  Bu noktada, HR **'dan** Azure Active Directory **'ye** (Azure AD) sağlama, Azure AD 'de karşılık gelen bir kullanıcı hesabı oluşturabilir. Azure AD 'yi sorgulayan uygulamalar, bu yeni çalışanın hesabını görebilir.  Azure AD kullanmayan uygulamalar varsa ve ardından Azure AD **'den** **Bu uygulamaların** veritabanlarına sağlama yaparsanız, kullanıcının kullanıcının erişmesi gereken tüm uygulamalara erişebilmesi gerekir.  Bu işlem, kullanıcının çalışmaya başlamasını ve gün içinde ihtiyaç duydukları uygulamalara ve sistemlere erişmesini sağlar.  Benzer şekilde, bölümleri veya iş durumu gibi özellikleri, HR sisteminde değişiklik, HR sisteminden Azure AD 'ye eşitleme ve diğer uygulamalar ve hedef veritabanlarına ek olarak, tutarlılık sağlar.

Azure AD Şu anda otomatik sağlamanın üç alanını sunmaktadır.  Bunlar:  

- İsteğe bağlı kaynak olmayan bir kayıt sisteminden Azure AD 'ye, **[HR odaklı sağlama](#hr-driven-provisioning) aracılığıyla sağlama**  
- **[Uygulama sağlama](#app-provisioning) aracılığıyla Azure AD 'den uygulamalara sağlama**  
- Azure AD ve Active Directory etki alanı Hizmetleri arasında, **[Dizin içi sağlama](#inter-directory-provisioning) aracılığıyla sağlama** 

![kimlik yaşam döngüsü yönetimi](media/what-is-provisioning/provisioning.png)

## <a name="hr-driven-provisioning"></a>HR odaklı sağlama

![HR sağlama](media/what-is-provisioning/cloud-2a.png)

HR 'dan Azure AD 'ye sağlama, nesnelerin oluşturulmasını, genellikle her bir çalışanı temsil eden kullanıcı kimliklerini, ancak bazı durumlarda, diğer nesneleri de HR sisteminizdeki bilgilere bağlı olarak departmanları veya diğer yapıları temsil eder.  

En yaygın senaryo, yeni bir çalışan şirketinize katıldığında bu durumda HR sistemine girilmiştir.  Bu durum gerçekleştiğinde, her yeni işe yönelik yönetim katılımına gerek kalmadan Azure AD 'de yeni bir kullanıcı olarak otomatik olarak sağlanırlar.  Genel olarak, HR 'ın sağlanması aşağıdaki senaryoları kapsayabilir.

- **Yeni çalışanların işe** Alım-bir HR sistemine yeni bir çalışan eklendiğinde, bir kullanıcı hesabı Active Directory, Azure AD 'de ve isteğe bağlı olarak, Azure AD tarafından desteklenen diğer uygulamaların dizinlerinde, e-posta adresini HR sistemine geri yazma ile otomatik olarak oluşturulur.
- **Çalışan özniteliği ve profil güncelleştirmeleri** -bu HR sisteminde (ad, başlık veya yönetici gibi) bir çalışan kaydı güncelleştirildiğinde, kullanıcı hesabı Active Directory, Azure AD ve isteğe bağlı olarak Azure AD tarafından desteklenen diğer uygulamalarda otomatik olarak güncelleştirilir.
- **Çalışan sonlandırmaları** -BIR çalışan HR 'da sonlandırıldığında, kullanıcı hesaplarının Active Directory, Azure AD ve diğer uygulamalarda oturum açması veya kaldırılması otomatik olarak engellenir.
- **Çalışan rehires** -bulut HR ' de bir çalışan yeniden çalıştırıldığında, eski hesapları otomatik olarak yeniden etkinleştirilebilir veya tercihinize bağlı olarak yeniden sağlanabilir.

Azure AD ile ık odaklı sağlama için üç dağıtım seçeneği vardır:

1. Tek bir aboneliğe, Workday veya başarılı etkenlere sahip kuruluşlar için Active Directory kullanmayın.
1. İş günü veya başarılı etkenlere yönelik tek bir aboneliğe sahip kuruluşlar için ve hem Active Directory hem de Azure AD
1. Birden çok IK sistemi olan kuruluşlar veya SAP, Oracle eBusiness veya PeopleSoft gibi şirket içi bir ık sistem

Daha fazla bilgi için bkz. [İK odaklı sağlama nedir?](what-is-hr-driven-provisioning.md)

## <a name="app-provisioning"></a>Uygulama sağlama

![uygulama sağlama](media/what-is-provisioning/cloud-3b.png)

Azure AD 'de, **[uygulama sağlama](../app-provisioning/user-provisioning.md)** terimi, kullanıcıların erişim ihtiyacı olan uygulamalarda, kendi veri deposuna sahip olan uygulamalar Için, Azure ad veya Active Directory birbirinden farklı şekilde kullanıcı kimliklerinin kopyalarını otomatik olarak oluşturmayı ifade eder. Kullanıcı kimlikleri oluşturmaya ek olarak, uygulama sağlama, kullanıcının durumu veya rolleri değiştikçe, bu uygulamalardan Kullanıcı kimliklerinin bakımını ve kaldırılmasını içerir. Yaygın senaryolar, bu uygulamaların her biri Azure AD 'den farklı olan kendi Kullanıcı deposuna sahip olduğu için [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)gibi uygulamalarda bir Azure AD kullanıcısı sağlamayı içerir.

Daha fazla bilgi için bkz. [uygulama sağlama nedir?](what-is-app-provisioning.md)

## <a name="inter-directory-provisioning"></a>Dizin içi sağlama

![Dizin içi sağlama](media/what-is-provisioning/cloud-4a.png)

Birçok kuruluş hem Active Directory hem de Azure AD 'ye bağlıdır ve şirket içi dosya sunucuları gibi Active Directory bağlı uygulamalar içerebilir.

Tarihsel olarak şirket içi ölçülü kaynak sağlama dağıtmış olan kuruluşlar, Active Directory ' deki tüm çalışanları için zaten Kullanıcı kimliklerine sahip olabilirler.   Dizin içi sağlama için en yaygın senaryo, Azure AD 'de zaten Active Directory bir kullanıcının sağlandığı bir kullanıcı.  Bu sağlama genellikle Azure AD Connect eşitleme veya Azure AD Connect bulut sağlama tarafından gerçekleştirilir. 

Ayrıca kuruluşlar, Azure AD 'den şirket içi sistemler sağlamak isteyebilir.  Örneğin, bir kuruluş Azure AD dizinine konuklar getirmiş olabilir, ancak bu konukların uygulama proxy 'si aracılığıyla şirket içi Windows tümleşik kimlik doğrulaması (WIA) tabanlı Web uygulamalarına erişmesi gerekir.  Bunun için Azure AD 'de bu kullanıcılar için şirket içi AD hesaplarının sağlanması gerekir.

Daha fazla bilgi için bkz. [Inter-Directory sağlama nedir?](what-is-inter-directory-provisioning.md)

 
## <a name="next-steps"></a>Sonraki adımlar 
- [Kimlik yaşam döngüsü yönetimi nedir?](what-is-identity-lifecycle-management.md)
- [HR odaklı sağlama nedir?](what-is-hr-driven-provisioning.md)
- [Uygulama sağlama nedir?](what-is-app-provisioning.md)
- [Dizinler arası sağlama nedir?](what-is-inter-directory-provisioning.md)