---
title: Azure Active Directory Koşullu erişimle dış erişimi yönetme
description: Kaynaklara dışarıdan erişimi güvenli hale getirmek için Azure Active Directory Koşullu erişim ilkeleri kullanma.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222318"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>Koşullu erişim ilkeleriyle dış erişimi yönetme 

[Koşullu erişim](../conditional-access/overview.md) , Azure AD 'nin sinyalleri bir araya getirmek, ilkeleri zorlamak ve bir kullanıcının kaynaklara erişmesine izin verilip verilmeyeceğini tespit etmek için kullandığı araçtır. Koşullu erişim ilkeleri oluşturma ve kullanma hakkında ayrıntılı bilgi için bkz. [bir koşullu erişim dağıtımı planlaması](../conditional-access/plan-conditional-access.md). 

![Koşullu erişim sinyallerinin ve kararların diyagramı](media/secure-external-access//7-conditional-access-signals.png)



Bu makalede, dış kullanıcılara koşullu erişim ilkelerinin uygulanması ve [Yetkilendirme Yönetimi](../governance/entitlement-management-overview.md) işlevlerine erişiminiz olmadığı varsayılır. Koşullu erişim ilkeleri, Yetkilendirme Yönetimi ile birlikte kullanılabilir ve kullanılabilir.

Bu belge kümesinde daha önce özetlenen [bir güvenlik planı oluşturdunuz](3-secure-access-plan.md) :

* Uygulamalar ve kaynaklar aynı güvenlik gereksinimlerine sahiptir ve erişim için gruplandırılabilir.

* Dış kullanıcılar için oturum açma gereksinimleri.

Bu planı, dış erişim için koşullu erişim ilkelerinizi oluşturmak üzere kullanacaksınız. 

> [!IMPORTANT]
> Tüm dış kullanıcılara uygulamadan önce oluşturduğunuz ilkeleri test edebilmeniz için birkaç dış Kullanıcı test hesabı oluşturun.

## <a name="conditional-access-policies-for-external-access"></a>Dış erişim için koşullu erişim ilkeleri

Koşullu erişim ilkeleriyle dış erişimi yöneten en iyi yöntemler aşağıda verilmiştir.

* Yetkilendirme yönetiminde bağlı kuruluşları kullanmadıysanız, birlikte çalıştığınız her iş ortağı kuruluşu için bir Azure AD güvenlik grubu veya Microsoft 365 grubu oluşturun. Bu iş ortağından tüm kullanıcıları gruba atayın. Bu grupları, koşullu erişim ilkelerinde kullanabilirsiniz.

* Mümkün olduğunca az sayıda koşullu erişim ilkesi oluşturun. Aynı erişim gereksinimlerine sahip uygulamalar için, tümünü aynı ilkeye ekleyin.  
‎ 
   > [!NOTE]
   > Koşullu erişim ilkeleri, en fazla 250 uygulama için uygulanabilir. 250 ' den fazla uygulama aynı erişim ihtiyaçlarına sahip ise, yinelenen ilkeler oluşturun. A ilkesi uygulamalar 1-250 ' e uygulanır, ilke B, uygulamalar 251-500, vb. için de geçerlidir.

* Bir adlandırma kuralına sahip dış erişime özel olarak ilkeleri açıkça adlandırın. Tek bir adlandırma kuralı *ExternalAccess_actiontaken_AppGroup*. Örneğin ExternalAccess_Block_FinanceApps.

## <a name="block-all-external-users-from-resources"></a>Tüm dış kullanıcıları kaynaklardan engelle

Dış kullanıcıların, koşullu erişim ilkeleriyle belirli kaynak kümelerine erişmesini engelleyebilirsiniz. Erişimini engellemek istediğiniz kaynak kümesini belirledikten sonra bir ilke oluşturun.

Dış kullanıcıların bir dizi uygulamaya erişimini engelleyen bir ilke oluşturmak için:

1. **Azure Portal** erişin, **Azure Active Directory**' i seçin,**güvenlik**' i seçin ve **koşullu erişim**' i seçin.

2. **Yeni ilke**' yi seçin ve bir **ad** girin, örneğin ExternalAccess_Block_FinanceApps

3. **Kullanıcılar ve gruplar ' ı** seçin. Dahil et sekmesinde **kullanıcıları ve grupları seç**' i seçin ve ardından **Tüm konuklar ve dış kullanıcılar**' ı seçin. 

4. **Dışla** ' yı seçin ve yönetici gruplarınızı ve acil durum erişimi (kesme camı) hesaplarını girin.

5. **Bulut uygulamaları veya eylemler**' i seçin, **Uygulama Seç**' i seçin, dış erişimi engellemek istediğiniz tüm uygulamaları seçin ve ardından **Seç**' i seçin.

6. **Koşullar**' ı seçin, **konum**' u seçin, Yapılandır **' ı seçin** ve **herhangi bir konum** seçin.

7. Erişim denetimleri altında, **Izin ver**' i seçin, geçişi **Engelle**' yi değiştirin ve **Seç**' i seçin.

8. İlkeyi etkinleştir ayarının **yalnızca rapor** olarak ayarlandığından emin olun, ardından **Oluştur**' u seçin.

## <a name="block-external-access-to-all-except-specific-external-users"></a>Belirli dış kullanıcılar hariç tüm dış erişimi engelle

Belirli bir grup dışında dış kullanıcıları engellemek istediğiniz zaman olabilir. Örneğin finans uygulamalarından finans ekibinin çalışmalarından hariç tüm dış kullanıcıları engellemek isteyebilirsiniz. Bunu yapmak için:

1. Finans grubuna erişmesi gereken dış kullanıcıları barındıracak bir güvenlik grubu oluşturun.

2. Yukarıdaki kaynaklardan gelen dış erişimi engelleyin 1-3 adımlarını izleyin.

3. 4. adımda, hariç tutmak istediğiniz güvenlik grubunu finans uygulamalarından bloke etmek üzere ekleyin.

4. Kalan adımları gerçekleştirin.

## <a name="implement-conditional-access"></a>Koşullu erişim uygulama

Birçok genel koşullu erişim ilkesi belgelenmiştir. Dış kullanıcılara uyarlayabileceğiniz aşağıdaki bölümüne bakın.

* [Tüm kullanıcılar için Multi-Factor Authentication gerektir](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [Kullanıcı risk tabanlı Koşullu Erişim](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [Güvenilmeyen ağlardan erişim için Multi-Factor Authentication gerektir](../conditional-access/untrusted-networks.md) 

* [Kullanım koşulları gerektir](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>Sonraki adımlar

Kaynaklara dışarıdan erişimi güvenli hale getirmek için aşağıdaki makalelere bakın. Eylemleri listelenen sırayla yapmanızı öneririz.

1. [Dış erişim için istediğiniz güvenlik duruşunuzu belirleme](1-secure-access-posture.md)

2. [Geçerli durumlarınızı bulma](2-secure-access-current-state.md)

3. [İdare planı oluşturma](3-secure-access-plan.md)

4. [Güvenlik için grupları kullanma](4-secure-access-groups.md)

5. [Azure AD B2B 'ye geçiş](5-secure-access-b2b.md)

6. [Yetkilendirme yönetimiyle güvenli erişim](6-secure-access-entitlement-managment.md)

7. [Koşullu erişim Ilkeleriyle güvenli erişim](7-secure-access-conditional-access.md) (buradan)

8. [Duyarlılık etiketleriyle güvenli erişim](8-secure-access-sensitivity-labels.md)

9. [Microsoft ekipleri, OneDrive ve SharePoint 'e güvenli erişim](9-secure-access-teams-sharepoint.md)
