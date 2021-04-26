---
title: Kullanıcılar uygulamamda sağlanmıyor
description: Azure AD ile Kullanıcı sağlaması için yapılandırdığınız bir Azure AD Galeri uygulamasında görünen kullanıcıları görmezseniz karşılaşılan yaygın sorunları giderme
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 429f993764273d6b6eb23d75d0132b374d5d0ca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256363"
---
# <a name="no-users-are-being-provisioned"></a>Hiçbir kullanıcı sağlanmıyor 
>[!NOTE]
>Başlangıç 04/16/2020, varsayılan erişim rolüne atanan kullanıcıların davranışını değiştirmiş. Ayrıntılar için lütfen aşağıdaki bölüme bakın. 
>
Bir uygulama için otomatik sağlama yapılandırıldıktan sonra (Azure AD 'ye uygulamaya bağlanmak için sağlanan uygulama kimlik bilgilerinin geçerli olduğunu doğrulama dahil), kullanıcılar ve/veya grupları uygulamaya sağlanır. Sağlama aşağıdaki noktalara göre belirlenir:

-   Uygulamaya **atanan** kullanıcılar ve gruplar. İç içe grupların sağlanması desteklenmediğini unutmayın. Atama hakkında daha fazla bilgi için bkz. [Azure Active Directory bir kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md).
-   **Öznitelik eşlemelerinin** etkinleştirilip etkinleştirilmediği ve geçerli ÖZNITELIKLERI Azure AD 'den uygulamaya eşitlemek üzere yapılandırılıp yapılandırılmadığını belirtir. Öznitelik eşlemeleri hakkında daha fazla bilgi için bkz. [Azure Active Directory SaaS uygulamaları Için Kullanıcı hazırlama öznitelik eşlemelerini özelleştirme](customize-application-attributes.md).
-   Belirli öznitelik değerlerine göre kullanıcılara filtre uygulayan bir **kapsam filtresi** olup olmadığı. Kapsam filtreleri hakkında daha fazla bilgi için bkz. [kapsam filtreleriyle öznitelik tabanlı uygulama sağlama](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
  
Kullanıcıların sağlanmakta olmadığını gözlemlerseniz, Azure AD 'de [sağlama günlükleri (Önizleme)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) bölümüne başvurun. Belirli bir kullanıcı için günlük girişlerini arayın.

 &gt; Etkinlik bölümünde **Kurumsal uygulamalar** &gt; **sağlama günlükleri (Önizleme)**  Azure Active Directory ' ni seçerek Azure Portal sağlama günlüklerine erişebilirsiniz. Sağlama verilerini kullanıcı adına veya kaynak sistemde ya da hedef sistemde tanımlayıcı temelinde arayabilirsiniz. Ayrıntılar için bkz. [sağlama günlükleri (Önizleme)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Sağlama günlükleri, sağlama hizmeti tarafından gerçekleştirilen tüm işlemleri kaydeder ve sağlama için kapsamdaki kullanıcılara yönelik Azure AD sorgulama, hedef uygulamayı bu kullanıcıların varlığına sorgulama ve sistem arasındaki kullanıcı nesnelerini karşılaştırma gibi. Ardından, karşılaştırma temelinde hedef sistemde Kullanıcı hesabını ekleyin, güncelleştirin veya devre dışı bırakın.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Dikkate alınması gereken genel sorun alanı
Aşağıda, nereden başlayabileceğiniz hakkında bir fikriniz varsa, detaya gidebileceğiniz genel sorun alanlarının bir listesi verilmiştir.

- [Sağlama hizmeti başlatma olarak görünmüyor](#provisioning-service-does-not-appear-to-start)
- [Sağlama günlükleri, atanmış olsalar dahi, kullanıcıların atlandığını ve sağlanmadığını belirtdiklerini varsayalım](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Sağlama hizmeti başlatma olarak görünmüyor
**Sağlama durumunu** , Azure Portal **Azure Active Directory &gt; Enterprise Apps &gt; \[ uygulama adı \] &gt; sağlama** **bölümünde olacak şekilde** ayarlarsanız. Ancak, sonraki yeniden yüklemeden sonra bu sayfada başka bir durum ayrıntısı gösterilmez, büyük olasılıkla hizmetin çalışıyor olması ancak henüz bir başlangıç döngüsünü tamamlamamamasından kaynaklanıyor olabilir. Hizmetin hangi işlemleri yaptığını ve herhangi bir hata olup olmadığını belirlemek için yukarıda açıklanan **sağlama günlüklerini (Önizleme)** denetleyin.

>[!NOTE]
>Bir başlangıç döngüsünün, Azure AD dizininin boyutuna ve sağlama kapsamındaki kullanıcı sayısına bağlı olarak 20 dakikadan birkaç saate kadar herhangi bir zaman geçmesi gerekir. İlk döngüden sonraki eşitlemeler daha hızlı olduktan sonra, sağlama hizmeti ilk döngüden sonra her iki sistemin durumunu temsil eden filigranları depolar. Başlangıç çevrimi, sonraki eşitlemeler performansını geliştirir.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Kaynak sağlama, kullanıcıların atlandığını ve bunlara atanmış olsalar da sağlanmadığını söyleyin

Bir Kullanıcı sağlama günlüklerinde "atlandı" olarak görünüyorsa, nedenini öğrenmek için günlüğün **adımlar** sekmesini gözden geçirmeniz önemlidir. Aşağıda yaygın nedenler ve çözümler verilmiştir:

- Bir **öznitelik değerine göre kullanıcıya filtre** uygulanan **bir kapsam filtresi yapılandırıldı** . Filtrelerin kapsamını belirleme hakkında daha fazla bilgi için bkz. [kapsam filtreleri](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **Kullanıcı "etkin değil" olarak belirlenir.** Bu özel hata iletisini görürseniz, bunun nedeni Azure AD 'de depolanan kullanıcı atama kaydında bir sorun olması olabilir. Bu sorunu onarmak için, kullanıcının (veya grup) uygulamadan atamasını kaldırıp yeniden atayın. Atama hakkında daha fazla bilgi için bkz. [Kullanıcı veya grup erişimi atama](../manage-apps/assign-user-or-group-access-portal.md).
- **Gerekli bir öznitelik eksik veya Kullanıcı için doldurulmamış.** Sağlamayı ayarlarken göz önünde bulundurmanız gereken önemli bir şey, Azure AD 'den uygulamaya hangi kullanıcı (veya grup) özelliklerinin akmasını tanımlayan öznitelik eşlemelerini ve iş akışlarını gözden geçirmeniz ve yapılandırmaktır. Bu yapılandırma, iki sistem arasındaki kullanıcıları/grupları benzersiz şekilde tanımlamak ve eşleştirmek için kullanılan "eşleşen özelliği" ayarlamayı içerir. Bu önemli süreç hakkında daha fazla bilgi için bkz. [Azure Active Directory SaaS uygulamaları Için Kullanıcı hazırlama öznitelik eşlemelerini özelleştirme](customize-application-attributes.md).
- **Gruplar Için öznitelik eşlemeleri:** Bazı uygulamalarda destekleniyorsa, üyelere ek olarak grup adı ve grup ayrıntılarının sağlanması. **Sağlama** sekmesinde gösterilen Grup nesneleri **eşlemesini** etkinleştirerek veya devre dışı bırakarak bu işlevselliği etkinleştirebilir veya devre dışı bırakabilirsiniz. Sağlama grupları etkinleştirilmişse, "eşleşen KIMLIK" için uygun bir alanın kullanıldığından emin olmak üzere öznitelik eşlemelerini gözden geçirdiğinizden emin olun. Eşleşen KIMLIK görünen ad veya e-posta diğer adı olabilir. Eşleşen özellik boşsa veya Azure AD 'de bir grup için doldurulmamışsa, Grup ve üyeleri sağlanmadı.
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>Varsayılan erişim rolüne atanan kullanıcıları sağlama
Galeriden bir uygulamadaki varsayılan role "varsayılan erişim" rolü denir. Geçmişte, bu role atanan kullanıcılar sağlanmadı ve [sağlama günlüklerinde](../reports-monitoring/concept-provisioning-logs.md) "etkin değil" olması nedeniyle atlanan olarak işaretlendi. 

**04/16/2020 sonra oluşturulan yapılandırmaların sağlanması Için davranış:** Varsayılan erişim rolüne atanan kullanıcılar, diğer tüm rollerle aynı şekilde değerlendirilir. Varsayılan erişim atanmış bir Kullanıcı, "etkin değil" olarak atlanmaz. 

**04/16/2020 öncesi oluşturulan yapılandırmaların sağlanması Için davranış:** Sonraki 3 ay boyunca, davranış bugün olduğu gibi devam edecektir. Varsayılan erişim rolüne sahip kullanıcılar, etkin olmayan şekilde atlanır. 2020 Temmuz 'dan sonra, davranış tüm uygulamalar için Tekdüzen olur. "Etkin değil" olması nedeniyle, varsayılan erişim rolüne sahip kullanıcıları sağlamayı atlayacağız. Bu değişiklik, hiçbir müşteri eylemi gerekmeden Microsoft tarafından yapılır. Bu değişiklikten sonra bile bu kullanıcıların atlanmasını sürdürmek istiyorsanız, lütfen kapsam dışı olduklarından emin olmak için uygun kapsam filtrelerini uygulayın veya kullanıcının atamasını kaldırmanız gerekir.  

Bu değişiklikler hakkında sorularınız için lütfen provisioningfeedback@microsoft.com
## <a name="next-steps"></a>Sonraki adımlar

[Azure AD Connect eşitleme: bildirime dayalı sağlamayı anlama](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
