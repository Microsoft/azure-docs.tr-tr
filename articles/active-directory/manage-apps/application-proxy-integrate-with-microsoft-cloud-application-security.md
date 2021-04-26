---
title: Cloud App Security-Azure AD ile şirket içi uygulamaları tümleştirme
description: Azure Active Directory Microsoft Cloud App Security (MCAS) ile çalışmak için bir şirket içi uygulama yapılandırın. , Koşullu erişim ilkelerine bağlı olarak oturumları gerçek zamanlı izlemek ve denetlemek için MCAS Koşullu Erişim Uygulama Denetimi kullanın. Bu ilkeleri, Azure Active Directory (Azure AD) içinde uygulama proxy 'Si kullanan şirket içi uygulamalara uygulayabilirsiniz.
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/19/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139f347a3d1bce7a8f841cd1c39cd250b9a2182d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259364"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Microsoft Cloud App Security ve Azure Active Directory ile gerçek zamanlı uygulama erişimi izlemeyi yapılandırma
Azure Active Directory (Azure AD) içindeki şirket içi bir uygulamayı gerçek zamanlı izleme için Microsoft Cloud App Security (MCAS) kullanacak şekilde yapılandırın. MCAS, koşullu erişim ilkelerine bağlı olarak oturumları gerçek zamanlı olarak izlemek ve denetlemek için Koşullu Erişim Uygulama Denetimi kullanır. Bu ilkeleri, Azure Active Directory (Azure AD) içinde uygulama proxy 'Si kullanan şirket içi uygulamalara uygulayabilirsiniz.

Aşağıda MCAS ile oluşturabileceğiniz ilke türlerine ilişkin bazı örnekler verilmiştir:

- Yönetilmeyen cihazlarda hassas belgelerin indirilmesini engelleyin veya koruyun.
- Yüksek riskli kullanıcılar uygulamalarda oturum açtığında izleyin ve sonra eylemlerini oturum içinden günlüğe kaydeder. Bu bilgilerle, oturum ilkelerinin nasıl uygulanacağını öğrenmek için Kullanıcı davranışını çözümleyebilirsiniz.
- Yönetilmeyen cihazlardan belirli uygulamalara erişimi engellemek için istemci sertifikalarını veya cihaz uyumluluğunu kullanın.
- Şirket dışı ağlardan kullanıcı oturumlarını kısıtlayın. Şirket ağınızın dışından bir uygulamaya erişen kullanıcılara sınırlı erişim verebilirsiniz. Örneğin, bu sınırlı erişim kullanıcının gizli belgeleri karşıdan yüklemesini engelleyebilir.

Daha fazla bilgi için bkz. [uygulamaları Microsoft Cloud App Security koşullu erişim uygulama denetimi koruma](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Gereksinimler

Lisan

- EMS E5 lisansı veya 
- P1 ve MCAS tek başına Azure Active Directory Premium.

Şirket içi uygulama:

- Şirket içi uygulama Kerberos kısıtlı temsilcisini (KCD) kullanmalıdır

Uygulama proxy 'Sini Yapılandır:

- Azure AD 'yi, ortamınızı hazırlama ve uygulama proxy bağlayıcısını yükleme dahil olmak üzere uygulama proxy 'Si kullanacak şekilde yapılandırın. Öğretici için bkz. [Azure AD 'de uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulamalar ekleme](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Azure AD 'ye şirket içi uygulama ekleme

Azure AD 'ye şirket içi bir uygulama ekleyin. Hızlı başlangıç için bkz. [Azure AD 'ye şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Uygulamayı eklerken, Şirket **içi uygulamanızı ekleme** dikey penceresinde aşağıdaki iki ayarı ayarladığınızdan emin olun:

- **Ön kimlik doğrulaması**: **Azure Active Directory** girin.
- **Uygulama gövdesinde URL 'Leri çevir**: **Evet**' i seçin.

Uygulamanın MCAS ile çalışması için bu iki ayar gereklidir.

## <a name="test-the-on-premises-application"></a>Şirket içi uygulamayı test etme

Uygulamanızı Azure AD 'ye ekledikten sonra, test için bir kullanıcı eklemek ve oturum açmayı test etmek için [uygulamayı test](application-proxy-add-on-premises-application.md#test-the-application) etme bölümündeki adımları kullanın. 

## <a name="deploy-conditional-access-app-control"></a>Koşullu Erişim Uygulama Denetimi dağıt

Uygulamanızı koşullu erişim uygulaması denetimiyle yapılandırmak için [Azure AD uygulamaları Için koşullu erişim uygulama denetimi dağıtma](/cloud-app-security/proxy-deployment-aad)bölümündeki yönergeleri izleyin.


## <a name="test-conditional-access-app-control"></a>Test Koşullu Erişim Uygulama Denetimi

Koşullu erişim uygulama denetimi ile Azure AD uygulamalarının dağıtımını test etmek için [Azure AD uygulamaları için dağıtımı test](/cloud-app-security/proxy-deployment-aad)etme bölümündeki yönergeleri izleyin.





