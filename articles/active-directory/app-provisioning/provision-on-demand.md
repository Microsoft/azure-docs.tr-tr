---
title: Azure Active Directory kullanarak talep üzerine Kullanıcı sağlama
description: Eşitlemeyi zorla
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: be03a149f34c16621905081a2f9bb663d85bc53c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255670"
---
# <a name="on-demand-provisioning"></a>İsteğe bağlı sağlama
Bir kullanıcıyı bir uygulamaya Saniyeler içinde sağlamak için isteğe bağlı sağlama kullanın. Diğer şeyler arasında bu özelliği kullanarak şunları yapabilirsiniz:

* Yapılandırma sorunlarını hızla giderin.
* Tanımladığınız ifadeleri doğrulayın.
* Test kapsamı filtreleri.

## <a name="how-to-use-on-demand-provisioning"></a>İsteğe bağlı sağlama kullanma

1. **Azure portalında** oturum açın.
1. **Tüm hizmetler**  >  **kurumsal uygulamalarına** gidin.
1. Uygulamanızı seçin ve ardından sağlama yapılandırma sayfasına gidin.
1. Yönetici kimlik bilgilerinizi sağlayarak sağlamayı yapılandırın.
1. **İsteğe bağlı sağlama** seçeneğini belirleyin.
1. Kullanıcıyı ad, soyadı, görünen ad, Kullanıcı asıl adı veya e-posta adresi ile arayın.
   > [!NOTE]
   > Cloud HR sağlama uygulaması için (AD/Azure AD 'ye yönelik Workday/başarılı faktörleri), giriş değeri farklıdır. Workday senaryosu için lütfen Workday 'de kullanıcının "WıD" adını belirtin. Başarılı bir senaryo için lütfen kullanıcının "Personıdexternal" adını başarılı bir şekilde belirtin. 
 
1. Sayfanın alt kısmındaki **sağla** ' yı seçin.

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="İsteğe bağlı bir kullanıcı sağlamak için Azure portal Kullanıcı ARABIRIMINI gösteren ekran görüntüsü.":::

## <a name="understand-the-provisioning-steps"></a>Sağlama adımlarını anlayın

İsteğe bağlı sağlama işlemi, bir Kullanıcı sağlanırken sağlama hizmetinin aldığı adımları göstermeye çalışır. Bir kullanıcı sağlamak için genellikle beş adım vardır. Aşağıdaki bölümlerde açıklanan adımlardan bir veya daha fazlası isteğe bağlı sağlama deneyimi sırasında gösterilir.

### <a name="step-1-test-connection"></a>1. Adım: bağlantıyı test etme

Sağlama Hizmeti, "test kullanıcısı" için bir istek yaparak hedef uygulamaya erişim yetkisi kurmaya çalışır. Sağlama Hizmeti, hizmetin sağlama adımlarıyla devam etmesine izin verildiğini belirten bir yanıt bekler. Bu adım yalnızca başarısız olduğunda gösterilir. Adım başarılı olduğunda isteğe bağlı sağlama deneyimi sırasında gösterilmez.

#### <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

* Hedef uygulamaya gizli belirteç ve kiracı URL 'SI gibi geçerli kimlik bilgileri sağladığınızdan emin olun. Gerekli kimlik bilgileri uygulamaya göre farklılık gösterir. Ayrıntılı yapılandırma öğreticileri için bkz. [öğretici listesi](../saas-apps/tutorial-list.md). 
* Hedef uygulamanın, **öznitelik eşlemeleri** bölmesinde tanımlanan eşleşen özniteliklere göre filtrelemeyi desteklediğinden emin olun. Desteklenen filtreleri anlamak için uygulama geliştiricisi tarafından sunulan API belgelerini denetlemeniz gerekebilir.
* Etki alanları arası kimlik yönetimi (SCıM) uygulamalarına yönelik sistem için Postman gibi bir araç kullanabilirsiniz. Bu tür araçlar, Azure Active Directory (Azure AD) sağlama hizmeti 'nin beklediği şekilde uygulamanın yetkilendirme isteklerine yanıt vermesini sağlamanıza yardımcı olur. [Örnek bir isteğe](./use-scim-to-provision-users-and-groups.md#request-3)göz atın.

### <a name="step-2-import-user"></a>2. Adım: Kullanıcı Içeri aktarma

Ardından, sağlama hizmeti kullanıcıyı kaynak sistemden alır. Hizmetin aldığı Kullanıcı öznitelikleri daha sonra aşağıdakileri yapmak için kullanılır:

* Kullanıcının sağlama kapsamında olup olmadığını değerlendirin.
* Mevcut bir kullanıcı için hedef sistemi denetleyin.
* Hedef sisteme hangi kullanıcı özniteliklerinin dışarı aktarılacağını belirleme.

#### <a name="view-details"></a>Ayrıntıları görüntüle


**Ayrıntıları görüntüle** bölümü, kaynak sistemden içeri aktarılan kullanıcının özelliklerini gösterir (örneğin, Azure AD).

#### <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

* Kaynak sistemdeki kullanıcı nesnesinde eşleşen özniteliği eksik olduğunda Kullanıcı içeri aktarma başarısız olabilir. Bu hatayı çözmek için şu yaklaşımlardan birini deneyin:

  * Kullanıcı nesnesini eşleşen öznitelik için bir değerle güncelleştirin.
  * Sağlama yapılandırmanızda eşleşen özniteliği değiştirin.

* Beklediğiniz bir öznitelik içeri aktarılan listede eksikse, özniteliğin kaynak sistemdeki kullanıcı nesnesinde bir değere sahip olduğundan emin olun. Sağlama hizmeti şu anda null öznitelikleri sağlamayı desteklemiyor.
* Sağlama yapılandırmanızın **öznitelik eşleme** sayfasının, beklediğinizi özniteliği içerdiğinden emin olun.

### <a name="step-3-determine-if-user-is-in-scope"></a>3. Adım: kullanıcının kapsamda olup olmadığını belirleme

Ardından, sağlama hizmeti kullanıcının sağlama [kapsamında](./how-provisioning-works.md#scoping) olup olmadığını belirler. Hizmet şöyle dikkate alır:

* Kullanıcının uygulamaya atanıp atanmayacağı.
* Kapsamın **atandı** veya **Tümünü Eşitle** olarak ayarlanmış olup olmadığı.
* Sağlama yapılandırmanızda tanımlanan kapsam filtreleri.  

#### <a name="view-details"></a>Ayrıntıları görüntüle

**Ayrıntıları görüntüle** bölümü değerlendirilen kapsam koşullarını gösterir. Aşağıdaki özelliklerden bir veya daha fazlasını görebilirsiniz:

* **Kaynak sistemde etkin** , kullanıcının `IsActive` Azure AD 'de özelliği **true** olarak ayarlandığını gösterir.
* **Uygulamaya atanan** , kullanıcının Azure AD 'de uygulamaya atandığını gösterir.
* **Kapsam eşitleme** , kapsam ayarının Kiracıdaki tüm kullanıcılara ve gruplara izin verdiğini belirtir.
* **Kullanıcının gerekli rolü** , kullanıcının uygulamaya sağlanması için gerekli rollerin olduğunu gösterir. 
* Uygulama için kapsam filtrelerini tanımlamış olmanız durumunda **kapsam filtreleri** de görüntülenir. Filtre şu biçimle görüntülenir: {kapsam filtresi başlığı} {kapsam Filtresi özniteliği} {kapsam filtre işleci} {kapsam filtresi değeri}.

#### <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

* Geçerli bir kapsam rolü tanımlamış olduğunuzdan emin olun. Örneğin, [Greater_Than işlecini](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) tamsayı olmayan bir değerle kullanmaktan kaçının.
* Kullanıcının gerekli rolü yoksa, [varsayılan erişim rolüne atanmış kullanıcıları sağlama ipuçlarını](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role)gözden geçirin.

### <a name="step-4-match-user-between-source-and-target"></a>4. Adım: kullanıcıdan kaynak ve hedef arasında eşleşme

Bu adımda, hizmet içeri aktarma adımında alınan kullanıcıyla hedef sistemdeki bir kullanıcıyla eşleştirmeye çalışır.

#### <a name="view-details"></a>Ayrıntıları görüntüle

**Ayrıntıları görüntüle** sayfası, hedef sistemde eşleşen kullanıcıların özelliklerini gösterir. Bağlam bölmesinde gördüğünüz Özellikler aşağıdaki gibi değişir:

* Hedef sistemde hiçbir Kullanıcı eşleştirilmez, hiçbir özellik görmezsiniz.
* Hedef sistemde eşleşen bir kullanıcı varsa, hedef sistemden bu eşleşen kullanıcının özelliklerini görürsünüz.
* Birden çok Kullanıcı eşleşirse, eşleşen kullanıcıların özelliklerini görürsünüz.
* Birden çok eşleşen öznitelik öznitelik eşlemelerinizin parçasıysa, eşleşen her öznitelik sırayla değerlendirilir ve bu öznitelik için eşleşen kullanıcılar gösterilir.

#### <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

* Sağlama Hizmeti, kaynak sistemdeki bir kullanıcıyla, hedefteki bir kullanıcıyla benzersiz bir şekilde eşleşmeyebilir. Eşleşen özniteliğin benzersiz olduğundan emin olarak bu sorunu çözün.
* Hedef uygulamanın, eşleşen öznitelik olarak tanımlanan öznitelik üzerinde filtrelemeyi desteklediğinden emin olun.  

### <a name="step-5-perform-action"></a>5. Adım: eylemi gerçekleştirme

Son olarak, sağlama hizmeti kullanıcı oluşturma, güncelleştirme, silme veya atlama gibi bir eylem gerçekleştirir.

Bir kullanıcının başarılı bir şekilde sağlanmasından sonra neleri görebileceğinize ilişkin bir örnek aşağıda verilmiştir:

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="Bir kullanıcının başarılı istek üzerine sağlamasını gösteren ekran görüntüsü.":::

#### <a name="view-details"></a>Ayrıntıları görüntüle

**Ayrıntıları görüntüle** bölümü, hedef uygulamada değiştirilen öznitelikleri görüntüler. Bu görüntü, sağlama hizmeti etkinliğinin son çıkışını ve aktarılmış öznitelikleri temsil eder. Bu adım başarısız olursa, görüntülenen öznitelikler sağlama hizmetinin değiştirme girişiminde bulunduğu öznitelikleri temsil eder.

#### <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

* Değişiklikleri dışarı aktarma sorunları büyük ölçüde farklılık gösterebilir. Genel hatalara yönelik [günlükleri sağlama belgelerini](../reports-monitoring/concept-provisioning-logs.md#error-codes) denetleyin.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

* **İsteğe bağlı sağlama kullanmak için sağlama özelliğini açmanız gerekiyor mu?** Uzun süreli bir taşıyıcı belirteci veya yetkilendirme için Kullanıcı adı ve parola kullanan uygulamalar için ek bir adım gerekmez. Yetkilendirme için OAuth kullanan uygulamalar şu anda sağlama işinin isteğe bağlı sağlama kullanılmadan önce durdurulmasını gerektirir. G Suite, Box, Facebook tarafından çalışma alanı ve bolluk bu kategoriye girer. Sağlama işlerinin hazırlanması gerekmeden tüm uygulamalar için isteğe bağlı sağlamayı desteklemek üzere iş devam etmektedir.

* **İsteğe bağlı sağlama ne kadar sürer?** İsteğe bağlı sağlama genellikle 30 saniyeden daha az sürer.

## <a name="known-limitations"></a>Bilinen sınırlamalar

Şu anda isteğe bağlı sağlama için bazı bilinen sınırlamalar vardır. [Önerileri ve geri bildirimleri](https://aka.ms/appprovisioningfeaturerequest) gönderin, böylece daha sonra yapılacak geliştirmeleri daha iyi belirleyebiliriz.

> [!NOTE]
> Aşağıdaki sınırlamalar isteğe bağlı sağlama özelliğine özeldir. Bir uygulamanın sağlama gruplarını, silmeleri veya diğer özellikleri destekleyip desteklemediğini öğrenmek için, bu uygulamanın öğreticisini denetleyin.

* Amazon Web Services (AWS) uygulaması isteğe bağlı sağlamayı desteklemez. 
* Grupların ve rollerin talep üzerine sağlanması desteklenmez.
* İsteğe bağlı sağlama, uygulamadan atanmamış kullanıcıların devre dışı bırakılmasını destekler. Ancak, Azure AD 'de devre dışı bırakılmış veya silinmiş olan kullanıcıların devre dışı bırakılmasını veya silinmesini desteklemez. Kullanıcı ararken bu kullanıcılar görünmez.

## <a name="next-steps"></a>Sonraki adımlar

* [Sağlama sorunlarını giderme](./application-provisioning-config-problem.md)