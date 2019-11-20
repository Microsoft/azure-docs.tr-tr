---
title: Azure AD Galeri uygulamasına kullanıcı sağlamayı yapılandırma sorunu | Microsoft Docs
description: Azure AD uygulama galerisinde zaten listelenen bir uygulamaya Kullanıcı sağlamayı yapılandırırken karşılaştığı yaygın sorunları giderme
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a8eaa46b46551f9b6075ec10b38de80f84c22a0
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034164"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Azure AD Galeri uygulamasına kullanıcı sağlamayı yapılandırma sorunu

Bir uygulama için [Otomatik Kullanıcı sağlamayı](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) yapılandırma (desteklenir), uygulamayı otomatik sağlama için hazırlamak üzere belirli yönergelerin izlenmesini gerektirir. Daha sonra, Kullanıcı hesaplarını uygulamayla eşitlemeye yönelik sağlama hizmetini yapılandırmak için Azure portal kullanabilirsiniz.

Uygulamanız için sağlamayı ayarlamaya özgü kurulum öğreticisini bularak her zaman başlamanız gerekir. Ardından, sağlama bağlantısı oluşturmak için hem uygulamayı hem de Azure AD 'yi yapılandırmak için bu adımları izleyin. [SaaS uygulamalarını Azure Active Directory Ile tümleştirme hakkında öğreticiler listesinde](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)uygulama öğreticilerinin listesi bulunabilir.

## <a name="how-to-see-if-provisioning-is-working"></a>Sağlamanın çalışıp çalışmadığını nasıl görebileceğinizi belirleme 

Hizmet yapılandırıldıktan sonra, hizmetin çalışması ile ilgili birçok öngörü iki konumdan çizilebilirler:

-   **Sağlama günlükleri (Önizleme)** – sağlama [günlüğü](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) , sağlama kapsamında olan ATANMıŞ kullanıcılar için Azure AD sorgulama da dahil olmak üzere, sağlama hizmeti tarafından gerçekleştirilen tüm işlemleri kaydeder. Bu kullanıcıların varlığı için hedef uygulamayı sorgulayın ve sistem arasındaki kullanıcı nesnelerini karşılaştırır. Ardından, karşılaştırma temelinde hedef sistemde Kullanıcı hesabını ekleyin, güncelleştirin veya devre dışı bırakın. &gt; **Etkinlik** bölümünde **Kurumsal uygulamalar** &gt; **sağlama günlükleri (Önizleme)** **Azure Active Directory** ' ni seçerek Azure Portal sağlama günlüklerine erişebilirsiniz.

-   **Geçerli durum –** Belirli bir uygulama için en son sağlama çalıştırmasının Özeti, ' nin en altındaki **Azure Active Directory &gt; kurumsal uygulamalar\] &gt; \[ &gt;uygulama adı sağlama** bölümünde görülebilir. Hizmet ayarları altındaki ekran. Geçerli durum bölümü, bir sağlama döngüsünün Kullanıcı hesapları sağlamaya başlayıp başlamadığına ilişkin gösterir. Döngüsünün ilerlemesini izleyebilir, kaç Kullanıcı ve grup sağlandığını görebilir ve kaç rolün oluşturulduğunu görebilirsiniz. Herhangi bir hata oluşursa, Ayrıntılar [sağlama günlüklerinde (Önizleme)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)bulunabilir.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Dikkate alınması gereken genel sorun alanı

Aşağıda, nereden başlayabileceğiniz hakkında bir fikriniz varsa, detaya gidebileceğiniz genel sorun alanlarının bir listesi verilmiştir.

* [Sağlama hizmeti başlatma olarak görünmüyor](#provisioning-service-does-not-appear-to-start)
* Uygulama kimlik bilgileri çalışmaması nedeniyle yapılandırma kaydedilemiyor
* [Sağlama günlükleri, atanmış olsalar dahi, kullanıcıların "atlandı" ve sağlanmamış olduğunu söylüyor](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Sağlama hizmeti başlatma olarak görünmüyor

**Sağlama durumunu** , Azure Portal **Azure Active Directory &gt; Enterprise Apps &gt;uygulamaadı \[\] sağlama&gt;** **bölümünde olacak şekilde** ayarlarsanız . Ancak, sonraki yeniden yüklemeden sonra bu sayfada başka bir durum ayrıntısı gösterilmez. Hizmetin çalışıyor olması, ancak henüz bir başlangıç döngüsünü tamamlamamasından kaynaklanıyor olabilir. Hizmetin hangi işlemleri yaptığını ve herhangi bir hata olup olmadığını belirlemek için yukarıda açıklanan **sağlama günlüklerini** denetleyin.

>[!NOTE]
>Bir başlangıç döngüsünün, Azure AD dizininin boyutuna ve sağlama kapsamındaki kullanıcı sayısına bağlı olarak 20 dakikadan birkaç saate kadar herhangi bir zaman geçmesi gerekir. İlk döngüden sonra sonraki eşitlemeler daha hızlı olur, çünkü sağlama hizmeti başlangıç döngüsünden sonra her iki sistemin durumunu temsil eden filigranları depolar, sonraki eşitlemeler performansını geliştirir.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Uygulama kimlik bilgileri çalışmaması nedeniyle yapılandırma kaydedilemiyor

Sağlamanın çalışması için Azure AD, uygulamanın bu uygulama tarafından verilen bir kullanıcı yönetimi API 'sine bağlanmasına izin veren geçerli kimlik bilgileri gerektirir. Bu kimlik bilgileri işe yaradığını veya ne olduğunu bilmiyorsanız, daha önce açıklanan bu uygulamayı ayarlama öğreticisini gözden geçirin.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Kaynak sağlama, kullanıcıların atlandığını ve bunlara atanmış olsalar da sağlanmadığını söyleyin

Bir Kullanıcı sağlama günlüklerinde "atlandı" olarak görünüyorsa, nedenini öğrenmek için günlük iletisindeki genişletilmiş ayrıntıları okumak çok önemlidir. Aşağıda yaygın nedenler ve çözümler verilmiştir:

- **Kapsam filtresi yapılandırıldı** **Bu, kullanıcıyı bir öznitelik değerine göre filtreleyerek**. Filtrelerin kapsamını belirleme hakkında daha fazla bilgi için <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>bkz.

- **Kullanıcı "etkin değil" olarak belirlenir.** Bu özel hata iletisini görürseniz, bunun nedeni Azure AD 'de depolanan kullanıcı atama kaydında bir sorun olması olabilir. Bu sorunu onarmak için, Kullanıcı (veya grubu) uygulamadan atamayı kaldırın ve yeniden atayın. Atama hakkında daha fazla bilgi için bkz <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

- **Gerekli bir öznitelik eksik veya Kullanıcı için doldurulmamış.** Sağlama yaparken göz önünde bulundurmanız gereken önemli bir şey, Azure AD 'den uygulamaya hangi kullanıcı (veya grup) özelliklerinin akmasını tanımlayan öznitelik eşlemelerini ve iş akışlarını gözden geçirmek ve yapılandırmak için gereken bir şeydir. Bu, iki sistem arasında kullanıcıları/grupları benzersiz şekilde tanımlamak ve eşleştirmek için kullanılan "eşleşen özelliği" ayarlamayı içerir. Bu önemli süreç hakkında daha fazla bilgi için bkz <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

  * **Gruplar için öznitelik eşlemeleri:** Bazı uygulamalarda destekleniyorsa, üyelere ek olarak grup adı ve grup ayrıntılarının sağlanması. **Sağlama** sekmesinde gösterilen Grup nesneleri **eşlemesini** etkinleştirerek veya devre dışı bırakarak bu işlevselliği etkinleştirebilir veya devre dışı bırakabilirsiniz. Sağlama grupları etkinleştirilmişse, "eşleşen KIMLIK" için uygun bir alanın kullanıldığından emin olmak üzere öznitelik eşlemelerini gözden geçirdiğinizden emin olun. Bu, görünen ad veya e-posta diğer adı olabilir), eşleşen özellik boşsa veya Azure AD 'de bir grup için doldurulmamışsa grup ve üyeleri sağlanmamıştır.

## <a name="next-steps"></a>Sonraki adımlar
[Azure Active Directory ile SaaS Uygulamalarına Kullanıcı Hazırlama ve Sağlamayı Kaldırma İşlemlerini Otomatik Hale Getirme](user-provisioning.md)
