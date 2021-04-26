---
title: Azure Active Directory Kullanıcılar, gruplar, lisanslama ve roller
description: Azure Active Directory'deki kullanıcılar ile atanan lisanslar, yönetici rolleri ve grup üyelikleri arasındaki ilişki
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.reviewer: krbain
ms.date: 12/02/2020
ms.topic: overview
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c253e75c7924a05c940b1d09b6f0c3fd281f696
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056955"
---
# <a name="what-is-enterprise-user-management"></a>Kurumsal Kullanıcı yönetimi nedir?

Bu makale Azure AD yöneticilerine gruplar, lisanslar, dağıtılan kurumsal uygulamalar ve yönetici rolleri açısından kullanıcılara yönelik yaygın [kimlik yönetimi](../fundamentals/active-directory-whatis.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) görevleri arasındaki ilişki hakkında bilgi sunmaktadır. Kuruluşunuz büyüdükçe Azure AD grupları ve yönetici rollerini kullanarak şu işlemleri gerçekleştirebilirsiniz:

* Lisansları kişiler yerine gruplara atama
* Azure AD yönetim görevlerini daha az ayrıcalıklı rollere dağıtmak için izinleri devretme
* Gruplara kurumsal uygulama erişimi atama

## <a name="assign-users-to-groups"></a>Kullanıcıları gruplara atama

Azure AD'deki grupları kullanarak çok sayıda kullanıcıya lisans atayabilir veya dağıtılan kurumsal uygulamalara kullanıcı erişimi verebilirsiniz. Azure AD 'de Genel yönetici hariç tüm yönetici rollerini atamak için grupları kullanabilir veya SaaS uygulamaları veya SharePoint siteleri gibi dış kaynaklara erişim izni verebilirsiniz.

Daha fazla esneklik sağlamak veya grup üyeliği yönetim yükünü azaltmak için Azure AD'deki [dinamik grupları](groups-create-rule.md) kullanarak grup üyeliklerinin otomatik olarak verilmesini ve alınmasını sağlayabilirsiniz. Bir veya daha fazla dinamik gruba üye olan her benzersiz kullanıcı için bir Azure AD Premium P1 lisansına ihtiyacınız vardır.

## <a name="assign-licenses-to-groups"></a>Gruplara lisans atama

Lisansları kullanıcılara tek tek atama veya kaldırma işlemleri zaman ve dikkat gerektirebilir. Bunun yerine [lisansları gruplara atayarak](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) büyük ölçekli lisans yönetim işlemlerini daha kolay gerçekleştirebilirsiniz.

Azure AD'de lisans atanan bir gruba katılan kullanıcılara uygun lisanslar otomatik olarak atanır. Azure AD gruptan ayrılan kullanıcıların lisans atamalarını kaldırır. Azure AD gruplarını kullanmadığınızda kuruluşa katılan veya ayrılan kullanıcılara toplu olarak lisans eklemek ya da lisansları kaldırmak için PowerShell betiği yazmanız veya Graph API'sini kullanmanız gerekir.

Yeterli sayıda lisans olmadığında veya aynı anda atanamayan hizmet planları gibi durumlar oluştuğunda Azure portalda lisanslama sorunlarının durumunu görebilirsiniz.

>[!NOTE]
>Grup tabanlı lisanslama özelliği şu anda genel önizleme sürümündedir. Önizleme sırasında bu özellik tüm ücretli Azure Active Directory (Azure AD) lisans planlarında veya deneme sürümlerinde sağlanır.

## <a name="delegate-administrator-roles"></a>Yönetici rollerini devretme

Çoğu büyük kuruluş, uygulama kaydetmesi gereken kullanıcılar gibi çalışanlarına güçlü Genel Yönetici rolünü atamadan işlerini yapma imkanı sağlayacak yeterli izinler sunmayı istemektedir. Uygulama yönetimi görevlerini daha ayrıntılı bir şekilde dağıtmanıza yardımcı olacak yeni Azure AD yönetici rolleri aşağıda verilmiştir:

 Rol adı | İzin özeti
 --------- | -------------------
 **Uygulama Yöneticisi** | Kurumsal uygulamalar ve uygulama kayıtları ekleyip yönetebilir, uygulama proxy'si ayarlarını yapılandırabilir. Uygulama yöneticileri, koşullu erişim ilkelerini ve cihazlarını görüntüleyebilir, ancak yönetmez.
 **Bulut Uygulaması Yöneticisi** | Kurumsal uygulamalar ve kurumsal uygulama kayıtları ekleyip yönetebilir. Bu rol, Uygulama Yöneticisi rolünün tüm izinlerine sahiptir ancak uygulama proxy'si ayarlarını yönetemez.
**Uygulama Geliştirici** | Uygulama kayıtlarını ekleyip yönetebilir ancak kurumsal uygulamaları yönetemez ve uygulama proxy'si yapılandıramaz.

Yeni Azure AD yönetici rolleri eklenmektedir. Kullanılabilir rollerin listesi için Azure portala veya [yönetici rolü izin başvurusuna](../roles/permissions-reference.md) bakın.

## <a name="assign-app-access"></a>Uygulama erişimi atama

Azure AD [kuruluşunuzda dağıtılan kurumsal uygulamalara](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)grup erişimi atamak IÇIN Azure ad kullanabilirsiniz. Dinamik grupları uygulamalara grup atama özelliğiyle birlikte kullanarak kuruluşunuz büyürken kullanıcıların uygulama erişimi atamalarını otomatikleştirebilirsiniz. Kurumsal uygulamalara erişim atamak için Azure Active Directory Premium P1 veya Premium P2 lisansına ihtiyacınız olacaktır.

Azure AD ayrıca uygulama ile erişim atadığınız gruplar arasındaki veri akışı üzerinde ayrıntılı denetime sahip olmanızı da sağlar. [Kurumsal Uygulamalar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) sayfasında bir uygulamayı açıp **Sağlama**'yı seçerek:

* Destekleyen uygulamalar için otomatik sağlamayı ayarlayabilirsiniz
* Uygulamanın kullanıcı yönetim API'sine bağlanmak için kimlik bilgileri girebilirsiniz
* Kullanıcı hesapları sağlandığında veya güncelleştirildiğinde Azure AD ile uygulama arasında akışı gerçekleştirilen kullanıcı özniteliklerini denetleyen eşlemeler ayarlayabilirsiniz
* Bir uygulama için Azure AD sağlama hizmetini başlatıp durdurabilir, sağlama önbelleğini silebilir veya hizmeti yeniden başlatabilirsiniz
* Azure AD ile uygulama arasında oluşturulan, güncelleştirilen ve kaldırılan tüm kullanıcıların ve grupların günlüğünü sağlayan **Sağlama etkinlik raporunu** ve daha ayrıntılı hata iletilerini içeren **Sağlama hata raporunu** görüntüleyebilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

Yeni bir Azure AD yöneticisiyseniz temel bilgiler için bkz. [Azure Active Directory Temel Konuları](../fundamentals/index.yml).

Dilerseniz [grup oluşturma](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [lisans atama](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [uygulama erişimi atama](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) veya [yönetici rolü atama](../roles/permissions-reference.md) işlemlerine başlayabilirsiniz.