---
title: Azure AD 'de otomatik SaaS uygulaması Kullanıcı hazırlama | Microsoft Docs
description: Birden çok üçüncü taraf SaaS uygulamasında kullanıcı hesaplarını otomatik olarak sağlamak, devre dışı bırakmak ve sürekli güncelleştirmek için Azure AD 'yi nasıl kullanabileceğinizi gösteren bir giriş.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144516"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Azure Active Directory olan uygulamalara Kullanıcı hazırlama ve sağlamayı kaldırma işlemlerini otomatikleştirme

Azure Active Directory (Azure AD) ' de, **uygulama sağlama** terimi, kullanıcıların erişmesi gereken bulut ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) uygulamalarında kullanıcı kimliklerinin ve rollerinin otomatik olarak oluşturulmasını ifade eder. Otomatik sağlama, kullanıcı kimliklerinin oluşturulmasına ek olarak, durum veya rol değişikliği olarak Kullanıcı kimliklerinin bakımını ve kaldırılmasını içerir. Yaygın senaryolar, bir Azure AD kullanıcısını [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)gibi uygulamalarda sağlamayı içerir.

![Sağlama genel bakış Diyagramı](media/user-provisioning/provisioning-overview.png)

Bu özellik şunları yapmanızı sağlar:

- **Sağlama otomatik hale**getirme: takımınıza veya kuruluşunuza katılımlarsa yeni kişiler için doğru sistemlerde otomatik olarak yeni hesaplar oluşturun.
- **Sağlamayı kaldırmayı otomatikleştirme:** Kullanıcılar takımdan veya kuruluştan ayrıldığında doğru sistemlerdeki hesapları otomatik olarak devre dışı bırakır.
- **Sistemler arasında veri eşitlemesi:** Uygulama ve sistemlerinizdeki kimliklerin, dizin veya insan kaynakları sisteminizdeki değişikliklere göre güncel tutulduğundan emin olun.
- **Grupları sağla:** Grupları destekleyen uygulamalara gruplar sağlayın.
- **Erişimi yönetir:** Uygulamalarınıza kimin sağlandığını izleyin ve denetleyin.
- **Kahverengi alan senaryolarında sorunsuz bir şekilde dağıtın:** Kullanıcılar hedef sistemde zaten mevcut olsa bile, sistemler arasında varolan kimlikleri eşleştirin ve kolay tümleştirme için izin verin.
- **Zengin özelleştirme kullan:** Kaynak sistemden hedef sisteme hangi kullanıcı verilerinin akışı gerektiğini tanımlayan özelleştirilebilir öznitelik eşlemelerinden yararlanın.
- **Kritik olaylar için uyarı alın:** Sağlama hizmeti kritik olaylara yönelik uyarılar sağlar ve iş gereksinimlerinizi karşılamak için özel uyarılar tanımlayabileceğiniz Log Analytics tümleştirmeye olanak tanır.

## <a name="benefits-of-automatic-provisioning"></a>Otomatik sağlamanın avantajları

Modern kuruluşlarda kullanılan uygulama sayısı büyümeye devam ettiğinden, BT yöneticileri ölçeğinde erişim yönetimi ile birlikte yapılır. Güvenlik onayları biçimlendirme dili (SAML) veya açık KIMLIK Connect (OıDC) gibi standartlar, yöneticilerin çoklu oturum açmayı (SSO) hızla ayarlayabilmesine izin verir, ancak erişim, kullanıcıların uygulamaya sağlanmasını de gerektirir. Birçok yönetici için, sağlama her kullanıcı hesabını el ile oluşturma veya CSV dosyalarını her hafta karşıya yükleme anlamına gelir, ancak bu süreçler zaman alan, pahalı ve hataya açık bir işlemdir. SAML Just-In-Time (JıT) gibi çözümler sağlamayı otomatik hale getirmeye başlamıştır, ancak kuruluşların kuruluştan ayrıldıklarında veya rol değişikliğine göre belirli uygulamalara erişim gerektirdiklerinde kullanıcıların sağlamasını kaldırmak için bir çözüme ihtiyacı vardır.

Otomatik sağlamayı kullanmaya yönelik bazı yaygın lamalar şunlardır:

- Sağlama işlemlerinin verimliliğini ve doğruluğunu en üst düzeye çıkarın.
- Barındırma ve özel olarak geliştirilmiş sağlama çözümlerinin ve betiklerinin korunmasıyla ilişkili maliyetlere kaydetme.
- Kuruluştan ayrıldıklarında kullanıcıların kimliklerini anahtar SaaS uygulamalarından anında kaldırarak kuruluşunuzun güvenliğini sağlama.
- Çok sayıda kullanıcıyı belirli bir SaaS uygulamasına veya sistemine kolayca içeri aktarma.
- Kimin sağlandığını ve bir uygulamada kimin oturum açmasını belirleyebilen tek bir ilke kümesine sahip olma.

Azure AD Kullanıcı sağlama, bu zorlukları ele almanıza yardımcı olabilir. Müşterilerin Azure AD Kullanıcı sağlamasını nasıl kullandığı hakkında daha fazla bilgi edinmek için [Asos örnek](https://aka.ms/asoscasestudy)olay incelemesini okuyabilirsiniz. Aşağıdaki videoda, Azure AD 'de Kullanıcı sağlamaya yönelik bir genel bakış sunulmaktadır:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD otomatik Kullanıcı sağlama ile hangi uygulamaları ve sistemleri kullanabilirim?

Azure AD, birçok popüler SaaS uygulaması ve insan kaynakları sistemi için önceden tümleşik destek ve [SCIM 2,0 standardının](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)belirli kısımlarını uygulayan uygulamalar için genel destek.

* **Önceden tümleştirilmiş uygulamalar (Galeri SaaS uygulamaları)** . Azure AD 'nin, [Kullanıcı hazırlama için uygulama öğreticileri listesinde](../saas-apps/tutorial-list.md)önceden tümleştirilmiş sağlama bağlayıcısını desteklediği tüm uygulamaları bulabilirsiniz. Galeride listelenen önceden tümleştirilmiş uygulamalar, sağlama için genellikle SCıM 2,0 tabanlı kullanıcı yönetimi API 'Lerini kullanır. 

   ![Salesforce logosu](media/user-provisioning/gallery-app-logos.png)

   Sağlama için yeni bir uygulama istemek istiyorsanız [uygulamanızın uygulama galerimiz ile tümleştirmesini](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)isteyebilirsiniz. Bir Kullanıcı sağlama isteği için uygulamanın SCıM ile uyumlu bir uç noktaya sahip olmasını gerektiririz. Uygulamayı platformumuza hızlı bir şekilde sunabilmeniz için lütfen uygulama satıcısının SCıM standardını izlediğinden emin olun.

* **SCıM 2,0 ' i destekleyen uygulamalar**. SCıM 2,0 tabanlı kullanıcı yönetimi API 'Lerini uygulayan uygulamaları genel olarak bağlama hakkında daha fazla bilgi için bkz. [SCIM kullanarak Azure Active Directory Kullanıcıları ve grupları uygulamalara otomatik olarak sağlama](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-scim"></a>SCıM nedir?

Uygulamalar, sağlama ve sağlamayı kaldırma işlemlerini otomatik hale getirmeye yardımcı olmak için özel kullanıcı ve Grup API 'Leri sunar. Ancak, birden fazla uygulamadaki kullanıcıları yönetmeye çalışmış olan herkes, her uygulamanın kullanıcı oluşturma veya güncelleştirme, gruplara kullanıcı ekleme veya kullanıcıları sağlamayı kaldırma gibi aynı basit eylemleri gerçekleştirmeye çalıştığını söyler. Henüz, tüm bu basit eylemler, farklı uç nokta yolları, Kullanıcı bilgilerini belirtmek için farklı yöntemler ve her bilgi öğesini temsil eden farklı bir şema kullanılarak yalnızca biraz farklı bir şekilde uygulanır.

Bu güçlükleri ele almak için, SCıM belirtimi, kullanıcıların uygulamaları uygulamasına, dışına ve bu uygulamalarda hareket ettirmek için ortak bir Kullanıcı şeması sağlar. SCıM, sağlama için standart bir standart haline geliyor ve SAML veya OpenID Connect gibi Federasyon standartlarıyla birlikte kullanıldığında, yöneticilere erişim yönetimi için uçtan uca standartlara dayalı bir çözüm sunar.

Kullanıcıları ve grupları bir uygulamaya sağlamayı ve sağlamayı otomatik hale getirmeyi otomatikleştirmek üzere SCıM kullanma hakkında ayrıntılı yönergeler için, [Azure Active Directory Ile SCIM Kullanıcı sağlama](use-scim-to-provision-users-and-groups.md)konusuna bakın.

## <a name="how-does-automatic-provisioning-work"></a>Otomatik sağlama nasıl çalışır?

**Azure AD sağlama hizmeti** , her bir uygulama satıcısı tarafından sağlanan kullanıcı yönetimi API uç noktalarına bağlanarak kullanıcıları ve diğer sistemleri SaaS uygulamaları sağlar. Bu Kullanıcı yönetimi API uç noktaları, Azure AD 'nin Kullanıcı aracılığıyla kullanıcıları oluşturmasına, güncelleştirmesine ve kaldırmasına izin verir. Seçilen uygulamalarda, sağlama hizmeti gruplar ve roller gibi kimlik ile ilgili diğer nesneleri de oluşturabilir, güncelleştirebilir ve kaldırabilir.

![Azure AD sağlama hizmeti](./media/user-provisioning/provisioning0.PNG)
*Şekil 1: Azure AD sağlama hizmeti*

Giden Kullanıcı sağlama iş akışı ![](./media/user-provisioning/provisioning1.PNG)
*Şekil 2: Azure AD 'den popüler SaaS uygulamalarına "giden" Kullanıcı sağlama iş akışı*

![gelen Kullanıcı sağlama iş akışı](./media/user-provisioning/provisioning2.PNG)
*Şekil 3: popüler ınsan büyük yönetim (HCM) uygulamalarından Azure Active Directory ve Windows Server 'a "gelen" Kullanıcı sağlama iş akışı Active Directory*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Nasıl yaparım? bir uygulamaya otomatik sağlama mi ayarlanacak?

Galeride listelenen önceden tümleştirilmiş uygulamalar için, otomatik sağlamayı ayarlamaya yönelik adım adım yönergeler sunulmaktadır. [Tümleşik Galeri uygulamaları için öğreticilerin listesine](https://docs.microsoft.com/azure/active-directory/saas-apps/)bakın. Aşağıdaki videoda SalesForce için otomatik Kullanıcı sağlamayı ayarlama işlemi gösterilmektedir.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

SCıM 2,0 ' ı destekleyen diğer uygulamalar için, [Azure Active Directory SCIM Kullanıcı sağlama](use-scim-to-provision-users-and-groups.md)makalesindeki adımları izleyin.

## <a name="what-happens-during-provisioning"></a>Sağlama sırasında ne olur?

Azure AD kaynak sistem olduğunda, sağlama hizmeti kullanıcıları ve grupları izlemek için [Azure ad Graph API 'ın fark sorgusu özelliğini](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) kullanır. Sağlama Hizmeti, kaynak sistem ve hedef sisteme karşı bir başlangıç döngüsü çalıştırır ve ardından düzenli artımlı Döngülerde çalışır.

### <a name="initial-cycle"></a>Başlangıç çevrimi

Sağlama hizmeti başlatıldığında, her zaman çalıştırılan ilk eşitleme:

1. [Öznitelik eşlemelerinde](customize-application-attributes.md)tanımlanmış tüm öznitelikleri alarak kaynak sistemden tüm kullanıcıları ve grupları sorgulayın.
1. Yapılandırılan [atamaları](assign-user-or-group-access-portal.md) veya [öznitelik tabanlı kapsam filtrelerini](define-conditional-rules-for-provisioning-user-accounts.md)kullanarak döndürülen kullanıcıları ve grupları filtreleyin.
1. Bir Kullanıcı atandığında veya sağlama kapsamında olduğunda, hizmet belirtilen [eşleşen öznitelikleri](customize-application-attributes.md#understanding-attribute-mapping-properties)kullanarak hedef sistemi eşleşen bir kullanıcı için sorgular. Örnek: Kaynak sistemdeki userPrincipal adı eşleşen özniteliktir ve hedef sistemdeki Kullanıcı adı ile eşleşiyorsa, sağlama hizmeti kaynak sistemdeki userPrincipal ad değerleriyle eşleşen kullanıcı adları için hedef sistemi sorgular.
1. Hedef sistemde eşleşen bir Kullanıcı bulunamazsa, kaynak sistemden döndürülen öznitelikler kullanılarak oluşturulur. Kullanıcı hesabı oluşturulduktan sonra, sağlama hizmeti yeni kullanıcı için hedef sistemin KIMLIĞINI algılar ve önbelleğe alır ve bu kullanıcı, gelecekteki tüm işlemleri çalıştırmak için kullanılır.
1. Eşleşen bir kullanıcı bulunursa, kaynak sistem tarafından belirtilen öznitelikler kullanılarak güncelleştirilir. Kullanıcı hesabı eşleştirdikten sonra, sağlama hizmeti yeni kullanıcı için hedef sistemin KIMLIĞINI algılar ve önbelleğe alır ve bu kullanıcı, gelecekteki tüm işlemleri çalıştırmak için kullanılır.
1. Öznitelik eşlemeleri "başvuru" öznitelikleri içeriyorsa, hizmet, başvurulan nesneleri oluşturmak ve bağlamak için hedef sistemde ek güncelleştirmeler yapar. Örneğin, bir Kullanıcı hedef sistemde bir "Yönetici" özniteliğine sahip olabilir ve bu, hedef sistemde oluşturulan başka bir kullanıcıyla bağlantılıdır.
1. İlk döngüsünün sonunda, sonraki artımlı döngüler için başlangıç noktasını sağlayan bir filigranı kalıcı hale getirin.

ServiceNow, G Suite ve Box gibi bazı uygulamalar yalnızca kullanıcıları sağlamaktan, ayrıca grupları ve üyelerini sağlamaktan de destek vermez. Bu durumlarda, [eşlemelerde](customize-application-attributes.md)grup sağlama etkinse, sağlama hizmeti kullanıcıları ve grupları eşitler ve daha sonra grup üyeliklerini eşitler.

### <a name="incremental-cycles"></a>Artımlı döngüler

İlk döngüden sonra diğer tüm döngüler şunları yapar:

1. Son eşik depolanmasından bu yana güncelleştirilmiş olan tüm kullanıcılar ve gruplar için kaynak sistemi sorgulayın.
1. Yapılandırılan [atamaları](assign-user-or-group-access-portal.md) veya [öznitelik tabanlı kapsam filtrelerini](define-conditional-rules-for-provisioning-user-accounts.md)kullanarak döndürülen kullanıcıları ve grupları filtreleyin.
1. Bir Kullanıcı atandığında veya sağlama kapsamında olduğunda, hizmet belirtilen [eşleşen öznitelikleri](customize-application-attributes.md#understanding-attribute-mapping-properties)kullanarak hedef sistemi eşleşen bir kullanıcı için sorgular.
1. Hedef sistemde eşleşen bir Kullanıcı bulunamazsa, kaynak sistemden döndürülen öznitelikler kullanılarak oluşturulur. Kullanıcı hesabı oluşturulduktan sonra, sağlama hizmeti yeni kullanıcı için hedef sistemin KIMLIĞINI algılar ve önbelleğe alır ve bu kullanıcı, gelecekteki tüm işlemleri çalıştırmak için kullanılır.
1. Eşleşen bir kullanıcı bulunursa, kaynak sistem tarafından belirtilen öznitelikler kullanılarak güncelleştirilir. Bu, eşleşen yeni bir hesap ise, sağlama hizmeti yeni kullanıcı için hedef sistemin KIMLIĞINI algılar ve önbelleğe alır ve bu kullanıcı, gelecekteki tüm işlemleri yürütmek için kullanılır.
1. Öznitelik eşlemeleri "başvuru" öznitelikleri içeriyorsa, hizmet, başvurulan nesneleri oluşturmak ve bağlamak için hedef sistemde ek güncelleştirmeler yapar. Örneğin, bir Kullanıcı hedef sistemde bir "Yönetici" özniteliğine sahip olabilir ve bu, hedef sistemde oluşturulan başka bir kullanıcıyla bağlantılıdır.
1. Önceden sağlama kapsamında olan bir Kullanıcı kapsamdan kaldırılırsa (atanmamış olanlar dahil), hizmet, hedef sistemdeki kullanıcıyı bir güncelleştirme aracılığıyla devre dışı bırakır.
1. Kaynak sistemde daha önce sağlama kapsamında olan bir kullanıcı devre dışıysa veya geçici olarak silinirse, hizmet, hedef sistemdeki kullanıcıyı bir güncelleştirme aracılığıyla devre dışı bırakır.
1. Daha önce sağlama kapsamında olan bir Kullanıcı kaynak sistemde sabit bir şekilde silinirse, hizmet kullanıcıyı hedef sistemde siler. Azure AD 'de, kullanıcılar, geçici olarak silindikten sonra 30 gün sonra kalıcı olarak silinir.
1. Daha sonraki artımlı döngüler için başlangıç noktasını sağlayan artımlı döngüsünün sonunda yeni bir filigranı kalıcı hale getirin.

> [!NOTE]
> İsteğe bağlı olarak, [eşlemeler](customize-application-attributes.md) bölümündeki **hedef nesne eylemleri** onay kutularını kullanarak **oluşturma**, **güncelleştirme**veya **silme** işlemlerini devre dışı bırakabilirsiniz. Bir güncelleştirme sırasında kullanıcıyı devre dışı bırakma mantığı, "accountEnabled" gibi bir alandan bir öznitelik eşlemesi aracılığıyla da denetlenir.

Sağlama Hizmeti, aşağıdaki olaylardan biri gerçekleşene kadar [her bir uygulamaya özgü öğreticide](../saas-apps/tutorial-list.md)tanımlanan aralıklarda sonsuza kadar geri dönüş artımlı döngülerinde çalışmaya devam eder:

- Hizmet, Azure portal kullanılarak veya uygun Graph API komutu kullanılarak el ile durdurulur 
- Yeni bir başlangıç çevrimi, Azure portal **durumu temizle ve yeniden Başlat** seçeneği kullanılarak veya uygun Graph API komutu kullanılarak tetiklenir. Bu eylem tüm depolanmış filigranı temizler ve tüm kaynak nesnelerinin yeniden değerlendirilmesini sağlar.
- Öznitelik eşlemelerinde veya kapsam filtrelerinde değişiklik nedeniyle yeni bir başlangıç çevrimi tetiklenir. Bu eylem Ayrıca, depolanan tüm filigranı temizler ve tüm kaynak nesnelerinin yeniden değerlendirilmesini sağlar.
- Sağlama işlemi, yüksek bir hata oranı nedeniyle karantinaya alınır (aşağıya bakın) ve dört haftadan uzun bir şekilde karantinayla kalır. Bu olay, hizmet otomatik olarak devre dışı bırakılır.

### <a name="errors-and-retries"></a>Hatalar ve yeniden denemeler

Hedef sistemdeki bir hata nedeniyle tek bir Kullanıcı hedef sistemde eklenemez, güncelleştirilemiyor veya silinemezse, işlem sonraki eşitleme döngüsüne yeniden denenir. Kullanıcı başarısız olmaya devam ederse, yeniden denemeler daha düşük bir sıklıkta gerçekleşecek ve aşamalı olarak günde yalnızca bir denemeye geri dönebilir. Sorunu çözmek için Yöneticiler, temel nedeni tespit etmek ve uygun eylemi gerçekleştirmek için [sağlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) denetlemelidir. Yaygın hatalarda şunlar olabilir:

- Hedef sistemde gerekli olan kaynak sistemde doldurulmuş bir özniteliği olmayan kullanıcılar
- Kaynak sistemde, hedef sistemde benzersiz bir kısıtlama olan ve aynı değer başka bir Kullanıcı kaydında bulunan bir öznitelik değeri olan kullanıcılar

Bu arızalar, kaynak sistemdeki etkilenen kullanıcının öznitelik değerleri ayarlanarak veya öznitelik eşlemeleri çakışmalara neden olmaz ayarlanarak çözülebilir.

### <a name="quarantine"></a>Ada

Hedef sisteme karşı yapılan çağrıların çoğu veya hepsi bir hata nedeniyle (örneğin, geçersiz yönetici kimlik bilgileri) başarısız olursa, sağlama işi "Karantina" durumuna geçer. Bu durum, [sağlama Özeti raporunda](check-status-user-account-provisioning.md) ve Azure Portal e-posta bildirimleri yapılandırılmışsa e-postayla belirtilir.

Karantinaya alma sırasında, artımlı döngülerin sıklığı günde bir kez yavaş şekilde azaltılır.

Sağlama işi, tüm sorunlu hatalar düzeltildikten ve sonraki eşitleme döngüsünün başlamasından sonra karantinadan çıkarılır. Sağlama işi dört haftadan uzun bir süreyle karantinayla kalırsa, sağlama işi devre dışı bırakılır. [Burada karantina durumu hakkında](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)daha fazla bilgi edinin.

## <a name="how-long-will-it-take-to-provision-users"></a>Kullanıcıları sağlamak için ne kadar sürer?

Performans, sağlama işinizin ilk sağlama döngüsünü mi yoksa artımlı bir döngüyü mi çalıştırdığına bağlıdır. Sağlama süresinin ne kadar süreceği ve sağlama hizmetinin durumunun nasıl izleneceği hakkında ayrıntılar için bkz. [Kullanıcı sağlama durumunu denetleme](application-provisioning-when-will-provisioning-finish-specific-user.md).

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Kullanıcıların düzgün şekilde sağlandığını nasıl anlayabilirim?

Kullanıcı sağlama hizmeti tarafından çalıştırılan tüm işlemler, Azure AD [sağlama günlüklerine (Önizleme)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)kaydedilir. Bu, kaynak ve hedef sistemlere yapılan tüm okuma ve yazma işlemlerini ve her işlem sırasında okunan veya yazılan kullanıcı verilerini içerir.

Azure portal sağlama günlüklerini okuma hakkında daha fazla bilgi için bkz. [sağlama Raporlama Kılavuzu](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Nasıl yaparım? Kullanıcı sağlama sorunlarını giderme

Otomatik Kullanıcı sağlama sorunlarını gidermeye yönelik senaryo tabanlı yönergeler için bkz. [kullanıcıları yapılandırma ve uygulama sağlama sorunları](application-provisioning-config-problem.md).

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Otomatik Kullanıcı sağlamayı kullanıma almak için en iyi uygulamalar nelerdir?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Bir uygulamaya giden Kullanıcı sağlama için örnek adım adım dağıtım planı için, bkz. [Kullanıcı hazırlama Için kimlik dağıtım kılavuzu](https://aka.ms/deploymentplans/userprovisioning).

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>SaaS uygulamalarına otomatik Kullanıcı sağlama, Azure AD 'de B2B kullanıcılarıyla çalışıyor mu?

Evet, Azure AD Kullanıcı sağlama hizmeti 'ni kullanarak Azure AD 'de SaaS uygulamalarına B2B (veya konuk) Kullanıcı sağlayabilirsiniz.

Ancak, B2B kullanıcılarının Azure AD 'yi kullanarak SaaS uygulamasında oturum açması için, SaaS uygulamasının SAML tabanlı çoklu oturum açma yeteneğine belirli bir şekilde yapılandırılmış olması gerekir. SaaS uygulamalarının B2B kullanıcılarından oturum açma işlemlerini desteklemesi için nasıl yapılandırılacağı hakkında daha fazla bilgi için bkz. [B2B işbirliği Için SaaS uygulamalarını yapılandırma]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>SaaS uygulamalarına otomatik Kullanıcı sağlama, Azure AD 'de dinamik gruplarla çalışıyor mu?

Evet. "Yalnızca atanmış kullanıcıları ve grupları Eşitle" olarak yapılandırıldığında, Azure AD Kullanıcı sağlama hizmeti bir SaaS uygulamasındaki kullanıcıları [dinamik bir grubun](../users-groups-roles/groups-create-rule.md)üyesi olup olmadıklarında temin edebilir veya devre dışı bırakabilirsiniz. Dinamik Gruplar ayrıca "tüm kullanıcıları ve grupları Eşitle" seçeneği ile de çalışır.

Ancak dinamik grupların kullanımı, Azure AD 'den SaaS uygulamalarına kadar uçtan uca Kullanıcı sağlamanın genel performansını etkileyebilir. Dinamik grupları kullanırken, bu uyarıları ve önerileri göz önünde bulundurun:

- Bir SaaS uygulamasında dinamik bir gruptaki bir kullanıcının ne kadar hızlı sağlandığı veya sağlanmasının ne kadar hızlı olması, dinamik grubun üyelik değişikliklerini nasıl değerlendirileceğini gösterir. Dinamik bir grubun işleme durumunu denetleme hakkında daha fazla bilgi için bkz. [bir üyelik kuralı için işleme durumunu denetleme](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Dinamik grupları kullanırken, bir sağlama olayında üyelik sonuçlarının kaybedilmesi halinde kuralların Kullanıcı sağlama ve yük sağlama için dikkatle değerlendirilmesi gerekir.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>SaaS uygulamalarına otomatik Kullanıcı hazırlama işlemi, Azure AD 'de iç içe gruplar ile çalışır mı?

Hayır. "Yalnızca atanmış kullanıcıları ve grupları Eşitle" olarak yapılandırıldığında, Azure AD Kullanıcı sağlama hizmeti iç içe gruplardaki kullanıcıları okuyamaz veya sağlayamaz. Yalnızca açıkça atanan grubun hemen üyesi olan kullanıcıları okuyabilir ve temin edebilir.

Bu, çoklu oturum açmayı da etkileyen ve [SaaS uygulamalarına erişimi yönetmek için bir grup kullanma](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps )bölümünde açıklanan "uygulamalara yönelik grup tabanlı atamaların" bir kısıtlamasıdır.

Geçici bir çözüm olarak, sağlanması gereken kullanıcıları içeren grupları açıkça (veya başka bir şekilde [kapsama](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) atamanız gerekir.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Şifrelenmiş kanal kullanılarak Azure AD ile hedef uygulama arasında sağlama mı var?

Evet. Sunucu hedefi için HTTPS SSL şifrelemesi kullanıyoruz.

## <a name="related-articles"></a>İlgili makaleler

- [SaaS uygulamalarının nasıl tümleştirileceği hakkında öğreticiler listesi](../saas-apps/tutorial-list.md)
- [Kullanıcı hazırlama için öznitelik eşlemelerini özelleştirme](customize-application-attributes.md)
- [Öznitelik eşlemeleri için ifadeler yazma](functions-for-customizing-application-data.md)
- [Kullanıcı hazırlama için kapsam filtreleri](define-conditional-rules-for-provisioning-user-accounts.md)
- [Azure AD 'den uygulamalara otomatik olarak Kullanıcı ve grup sağlamayı etkinleştirmek için SCıM kullanma](use-scim-to-provision-users-and-groups.md)
- [Azure AD eşitleme API 'sine genel bakış](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
