---
title: Azure Market 'Te yönetilen hizmet teklifi yayımlama
description: Onpanolar müşterilerinin Azure tarafından yetkilendirilen kaynak yönetimine yönelik bir yönetilen hizmet teklifini yayımlamayı öğrenin.
ms.date: 11/15/2019
ms.topic: overview
ms.openlocfilehash: 29f17e6227d3c50a4d9fe13f7525ac71f7550632
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131298"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Azure Market 'Te yönetilen hizmet teklifi yayımlama

Bu makalede, Azure Market 'e kaynak eklemek için teklifi satın alan bir müşteriyi etkinleştirmek üzere [bulut iş ortağı portalı](https://cloudpartner.azure.com/)kullanarak [Azure Marketi](https://azuremarketplace.microsoft.com) 'ne genel veya özel yönetilen bir hizmet teklifini yayımlamayı öğreneceksiniz. yönetme.

> [!NOTE]
> Bu teklifleri oluşturmak ve yayımlamak için [Iş Ortağı Merkezi 'nde](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) geçerli bir hesabınız olması gerekir. Zaten bir hesabınız yoksa [kaydolma işlemi](https://aka.ms/joinmarketplace) , Iş Ortağı Merkezi 'nde hesap oluşturma ve ticari Market programına kaydolma adımlarında size yol açacaktır. Microsoft İş Ortağı Ağı (MPN) KIMLIĞINIZ, müşteri görevlendirmeleri genelinde etkilerini izlemek için yayımladığınız tekliflerle [otomatik olarak ilişkilendirilir](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) .
>
> Bir teklifi Azure Marketi 'ne yayımlamak istemiyorsanız, Azure Resource Manager şablonları kullanarak müşterileri el ile ekleyebilirsiniz. Daha fazla bilgi için bkz. [Azure tarafından atanan kaynak yönetimine müşteri ekleme](onboard-customer.md).

Yönetilen bir hizmet teklifini yayımlamak, Azure Marketi 'ne başka bir teklif türü yayımlamaya benzer. Bu işlem hakkında bilgi edinmek için bkz. [Azure Marketi ve Appsource Yayımlama Kılavuzu](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) ve [Azure ve appsource Market tekliflerini yönetme](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers). Ayrıca, özellikle [yönetilen hizmetler](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) bölümünü [ticari Market sertifika ilkelerini](https://docs.microsoft.com/legal/marketplace/certification-policies)gözden geçirmeniz gerekir.

Müşteri teklifinizi eklediğinde bir veya daha fazla belirli abonelik veya kaynak grubu temsilciliğini alacak ve daha sonra [Azure tarafından yetkilendirilen kaynak yönetimi için eklendi](#the-customer-onboarding-process)olacaktır. Aboneliğin (veya abonelik içindeki kaynak gruplarının) eklendi olabileceğini, **Microsoft. ManagedServices** kaynak sağlayıcısı 'nı el ile kaydederek aboneliğin ekleme için yetkilendirilmiş olması gerektiğini unutmayın.

> [!IMPORTANT]
> Yönetilen bir hizmet teklifinin her bir planı, kiracınızdaki Azure Active Directory (Azure AD) varlıklarını tanımladığınız bir **bildirim ayrıntıları** bölümü içerir. bu noktada, kiralarınızda atanan kaynak gruplarına ve/veya aboneliklerine erişen müşteriler için Bu planı satın alın. Buraya eklediğiniz herhangi bir grup (veya Kullanıcı veya hizmet sorumlusu), planı satın alan her müşteri için aynı izinlere sahip olacağını unutmamak önemlidir. Her müşteriyle çalışacak farklı gruplar atamak için, her müşteri için özel olarak özel bir plan yayımlamanız gerekir.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Bulut İş Ortağı Portalı teklifinizi oluşturun

1. [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.
2. Sol gezinti menüsünden **yeni teklif**' i seçin ve ardından **yönetilen hizmetler**' i seçin.
3. Doldurmanız gereken dört bölümden oluşan bir **Düzenleyici** bölümü görürsünüz: **teklif ayarları**, **planlar**, **Market**ve **destek**. Bu bölümleri tamamlamaya ilişkin yönergeler için okumaya devam edin.

## <a name="enter-offer-settings"></a>Teklif ayarlarını girin

**Teklif ayarları** bölümünde aşağıdakileri sağlayın:

|Alan  |Açıklama  |
|---------|---------|
|**Teklif KIMLIĞI**     | Teklifiniz için benzersiz bir tanımlayıcı (yayımcı profiliniz dahilinde). Bu KIMLIK yalnızca küçük harfli alfasayısal karakterler, tireler ve alt çizgiler içerebilir. en fazla 50 karakter olabilir. Teklif KIMLIĞININ, ürün URL 'Leri ve faturalandırma raporlarında olduğu yerlerde müşterilere görünür olabileceğini aklınızda bulundurun. Teklifi yayımladıktan sonra bu değeri değiştiremezsiniz.        |
|**Yayımcı KIMLIĞI**     | Teklifle ilişkilendirilecek Yayımcı Kımlığı. Birden fazla yayımcı KIMLIĞINIZ varsa, bu teklif için kullanmak istediğiniz birini seçebilirsiniz.       |
|**Adı**     | Müşterilerin Azure Marketi 'nde ve Azure portal Teklifinizle ilgili olarak göreceği ad (en fazla 50 karakter). Müşterilerin anlayabileceği tanınabilir bir marka adı kullanın — bu teklifi kendi web siteniz aracılığıyla yükseltiyorsanız, burada tam olarak aynı adı kullandığınızdan emin olun.        |

İşiniz bittiğinde **Kaydet**' i seçin. Artık **planlar** bölümüne taşımaya hazırsınız.

## <a name="create-plans"></a>Plan oluşturma

Her teklifin bir veya daha fazla planı olmalıdır (bazen SKU 'Lar olarak adlandırılır). Farklı fiyatlarla farklı özellik kümelerini desteklemek için veya belirli bir müşterinin sınırlı bir kitlesi için belirli bir planı özelleştirmek üzere birden çok plan ekleyebilirsiniz. Müşteriler, kendileri için kullanılabilir olan planları üst teklif altında görüntüleyebilir.

Planlar bölümünde **Yeni plan**' ı seçin. Sonra bir **plan kimliği**girin. Bu KIMLIK yalnızca küçük harfli alfasayısal karakterler, tireler ve alt çizgiler içerebilir. en fazla 50 karakter olabilir. Plan KIMLIĞI, müşterilerin ürün URL 'Leri ve faturalandırma raporlarında olduğu yerlerde görülebilir. Teklifi yayımladıktan sonra bu değeri değiştiremezsiniz.

### <a name="plan-details"></a>Plan ayrıntıları

**Plan ayrıntıları** bölümünde aşağıdaki bölümleri doldurun:

|Alan  |Açıklama  |
|---------|---------|
|**Başlık**     | Görüntüleme planının kolay adı. En fazla 50 karakter uzunluğunda.        |
|**Özet**     | Başlık altında görüntülenecek planın kısa açıklaması. En fazla 100 karakter uzunluğunda.        |
|**Açıklama**     | Planın daha ayrıntılı bir açıklamasını sağlayan açıklama metni.         |
|**Faturalandırma modeli**     | Burada gösterilen 2 faturalandırma modeli vardır, ancak yönetilen hizmet teklifleri için **kendi lisansınızı getir** ' i seçmeniz gerekir. Bu, müşterilerinize bu teklifle ilgili maliyetleri doğrudan faturalayeceğiniz ve Microsoft 'un size herhangi bir ücret ödemeyeceği anlamına gelir.   |
|**Bu özel bir plan mi?**     | SKU 'nun özel mi yoksa genel mi olduğunu gösterir. Varsayılan **değer (genel) değildir.** Bu seçimi bırakırsanız, planınız belirli müşteriler (veya belirli bir sayıda müşteri) ile sınırlandırılır; ortak bir plan yayımladıktan sonra, daha sonra özel olarak değiştiremezsiniz. Bu planı yalnızca belirli müşteriler için kullanılabilir hale getirmek için **Evet**' i seçin. Bunu yaptığınızda, abonelik kimliklerini sağlayarak müşterileri belirlemeniz gerekir. Bunlar tek bir (10 abonelik için) veya bir. csv dosyası (20.000 ' e kadar abonelik için) karşıya yüklenebilir. Teklifi sınayabilmeniz ve doğrulayabilmeniz için kendi aboneliklerinizi buraya eklediğinizden emin olun. Daha fazla bilgi için bkz. [özel SKU 'lar ve planlar](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

### <a name="manifest-details"></a>Bildirim ayrıntıları

Planınız için **bildirim ayrıntıları** bölümünü doldurun. Bu, müşteri kaynaklarını yönetmeye yönelik yetkilendirme bilgilerini içeren bir bildirim oluşturur. Azure tarafından atanan kaynak yönetimini etkinleştirmek için bu bilgiler gereklidir.

> [!NOTE]
> Yukarıda belirtildiği gibi, **Yetkilendirme** girdinizdeki kullanıcılar ve roller planı satın alan her müşteri için de geçerlidir. Belirli bir müşteriye erişimi sınırlandırmak istiyorsanız, özel kullanım için özel bir plan yayımlamanız gerekir.

İlk olarak, bildirim için bir **Sürüm** belirtin. *N. n. n* biçimini kullanın (örneğin, 1.2.5).

Ardından, **KIRACı kimliğinizi**girin. Bu, kuruluşunuzun Azure Active Directory kiracı KIMLIĞIYLE ilişkili bir GUID 'dir (yani, müşterilerinizin kaynaklarını yönetmek için üzerinde çalıştığınız kiracı). Bu kullanışlı bir sahip değilseniz, Azure portal sağ üst tarafındaki hesap adınızın üzerine gelerek veya **Dizin Değiştir**' i seçerek bulabilirsiniz.

Son olarak, planınıza bir veya daha fazla **Yetkilendirme** girişi ekleyin. Yetkilendirmeler, planı satın alan müşteriler için kaynaklara ve aboneliklere erişebilen varlıkları tanımlar ve belirli düzeylerde erişim izinleri veren roller atayabilir. Desteklenen roller hakkında daha fazla bilgi için bkz. [Azure 'Da kiracılar, roller ve kullanıcılar ile ilgili senaryolar](../concepts/tenants-users-roles.md).

Her **Yetkilendirme**için aşağıdakileri sağlamanız gerekir. Daha sonra, daha fazla Kullanıcı ve rol tanımı eklemek için gerektiği kadar **Yeni yetkilendirme** seçebilirsiniz.

- **Azure AD nesne kimliği**: bir kullanıcının, Kullanıcı grubunun veya UYGULAMANıN Azure AD tanımlayıcısı (rol tanımında açıklandığı gibi) müşterilerinizin kaynaklarına verilecektir.
- **Azure AD nesne görünen adı**: müşterinin bu yetkilendirmenin amacını anlamasına yardımcı olacak kolay bir ad. Müşteri, kaynakları yetkilendirirken bu adı görür.
- **Rol tanımı**: listeden mevcut Azure AD yerleşik rollerinden birini seçin. Bu rol, **Azure AD nesne kimliği** alanındaki kullanıcının kaynaklarınızın kaynaklarına sahip olacağı izinleri tespit eder. Bu rollerin açıklamaları için bkz. [Azure için yetkilendirilmiş kaynak yönetimi Için](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management) [yerleşik roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) ve rol desteği
- **Atanabilir roller**: Bu, yalnızca bu yetkilendirme Için **rol tanımında** Kullanıcı erişimi Yöneticisi ' ni seçtiyseniz gereklidir. Bu durumda, buraya bir veya daha fazla atanabilir rol eklemeniz gerekir. **Azure AD nesne kimliği** alanındaki Kullanıcı, bu **atanabilir rolleri** , [düzeltilebilir ilkeleri dağıtmak](deploy-policy-remediation.md)için gereken [yönetilen kimliklere](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)atayabilecektir. Normalde Kullanıcı erişimi Yöneticisi rolüyle ilişkili başka hiçbir izin bu kullanıcı için uygulanmayacak şekilde unutmayın. Burada bir veya daha fazla rol seçmezseniz gönderiminiz sertifikayı geçirmez. (Bu kullanıcının rol tanımı için Kullanıcı erişimi Yöneticisi ' ni seçmediyseniz, bu alanın bir etkisi yoktur.)

> [!TIP]
> Çoğu durumda, tek bir kullanıcı hesabı serisi yerine bir Azure AD kullanıcı grubuna veya hizmet sorumlusuna izin atamak isteyeceksiniz. Bu, erişim gereksinimleriniz değiştiğinde planı güncelleştirmek ve yeniden yayınlamak zorunda kalmadan bireysel kullanıcılar için erişim eklemenize veya kaldırmanıza olanak sağlar. Ek öneriler için bkz. [Azure 'Da kiracılar, roller ve kullanıcılar ile Ilgili kullanım senaryoları](../concepts/tenants-users-roles.md).

Bilgileri tamamladıktan sonra, ek planlar oluşturmak için ihtiyaç duyduğunuz kadar **Yeni plan** seçeneğini belirleyebilirsiniz. İşiniz bittiğinde **Kaydet**' i seçin ve ardından **Market** bölümüne devam edin.

## <a name="provide-marketplace-text-and-images"></a>Market metin ve görüntüleri sağlama

**Market** bölümü, müşterilerin Azure Marketi 'nde göreceği metin ve görüntüleri ve Azure Portal sağlayacaksınız.

**Genel bakış** bölümünde aşağıdaki alanları doldurun:

|Alan  |Açıklama  |
|---------|---------|
|**Başlık**     |  Teklifin başlığı, genellikle uzun, resmi adı. Bu başlık Market 'te göze çarpacak olarak görüntülenecektir. En fazla 50 karakter uzunluğunda. Çoğu durumda bu, **teklif ayarları** bölümüne girdiğiniz **adla** aynı olmalıdır.       |
|**Özet**     | Teklifinizin kısa bir amacı veya işlevi. Bu genellikle başlık altında görüntülenir. En fazla 100 karakter uzunluğunda.        |
|**Uzun Özet**     | Teklifinizin amacının veya işlevinin uzun bir özeti. En fazla 256 karakter uzunluğunda.        |
|**Açıklama**     | Teklifiniz hakkında daha fazla bilgi. Bu alan en fazla 3000 karakter uzunluğunda ve basit HTML biçimlendirmesini destekler. Açıklamasında "yönetilen hizmet" veya "yönetilen hizmetler" sözcüklerini dahil etmeniz gerekir.       |
|**Pazarlama tanımlayıcısı**     | Benzersiz bir URL kullanımı kolay tanımlayıcı. Bu teklif için Market URL 'Lerinde kullanılacaktır. Örneğin, yayımcı KIMLIĞINIZ *contoso* ise ve pazarlama tanımınız *SampleApp*ise Azure Marketi 'nde teklifinizin URL 'si *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* olacaktır.        |
|**Abonelik kimliklerini Önizle**     | 100 abonelik tanımlayıcılarına bir tane ekleyin. Bu aboneliklerle ilişkili müşteriler, teklifi canlı olmadan önce Azure Marketi 'nde görüntüleyebilecektir. Müşterilerinizin müşterilere hazır hale getirmeden önce Azure Marketi 'nde teklifinizin önizlemesini yapabilmeniz için kendi aboneliklerinizi burada da dahil etmeyi öneririz.  (Microsoft destek ve mühendislik ekipleri bu önizleme döneminde teklifinizi da görüntüleyebilecektir.)   |
|**Faydalı bağlantılar**     | Teklifinizle ilgili belgeler, sürüm notları, SSS vb. gibi URL 'Ler.        |
|**Önerilen Kategoriler (en fazla 5)**     | Teklifiniz için uygulanan bir veya daha fazla kategori (en fazla beş). Bu kategoriler, müşterilerin teklifinizi Azure Marketi 'nde ve Azure portal bulmasına yardımcı olur.        |

**Pazarlama yapıtları** bölümünde, teklifinizle birlikte gösterilecek logoları ve diğer varlıkları karşıya yükleyebilirsiniz. İsteğe bağlı olarak, müşterilerinizin teklifinizi anlamalarına yardımcı olabilecek videoların ekran görüntülerini veya bağlantılarını yükleyebilirsiniz.

Dört logo boyutu gereklidir: **küçük (40x40)** , **Orta (90x90)** , **büyük (115X115)** ve **geniş (25 5x155)** . Logolarınız için şu yönergeleri izleyin:

- Azure tasarımının basit bir renk paleti vardır. Logonuzdaki birincil ve ikincil renklerinin sayısını sınırlandırın.
- Portalın tema renkleri siyah ve beyazdır. Bu renkleri logonuzun arka plan rengi olarak kullanmayın. Logonuzun portalda öne çıkmasını sağlayan bir renk kullanın. Basit birincil renkleri öneririz.
- Saydam bir arka plan kullanıyorsanız, amblem ve metnin beyaz, siyah veya mavi olmadığından emin olun.
- Logonuzun genel görünümü düz olmalı ve gradyanlardan kaçınmalıdır. Logoda gradyan arka plan kullanmayın.
- Şirket veya marka adınız dahil olmak üzere logoya metin yerleştirmeyin.
- Logonuzun esnetilmediğinden emin olun.

**Hero (815x290)** logosu isteğe bağlıdır, ancak önerilir. Bir Hero logosu eklerseniz, aşağıdaki yönergeleri izleyin:

- Hero logouna herhangi bir metin eklemeyin ve logonun sağ tarafında 415 piksel boş alana ayrıldığınızdan emin olun. Bu, programlı olarak eklenecek metin öğelerinin odasını bırakmak için gereklidir: Publisher görünen adı, plan başlığı, uzun Özet teklif.
- Hero logosunun arka planı siyah, beyaz veya saydam olmayabilir. Gömülü metin beyaz olarak görüntülenebildiğinden arka plan rengin çok açık olmadığından emin olun.
- Teklifinizi bir Hero simgesiyle yayımladığınızda, onu kaldıramazsınız (ancak isterseniz farklı bir sürümle güncelleştirebilirsiniz).

**Müşteri adayı yönetimi** bölümünde, müşteri ADAYLARıNıZıN depolanacağı CRM sistemini seçebilirsiniz. [Yönetilen hizmetler sertifika ilkelerine](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)göre, bir **müşteri adayı hedefinin** gerekli olduğunu unutmayın.

Son olarak, **Gizlilik ILKESI URL** 'Nizi ve **yasal** bölümde **kullanım koşulları** sağlayın. Ayrıca, bu teklif için [Standart sözleşmenin](https://docs.microsoft.com/azure/marketplace/standard-contract) kullanılıp kullanılmayacağını da belirtebilirsiniz.

**Destek** bölümüne geçmeden önce değişikliklerinizi kaydettiğinizden emin olun.

## <a name="add-support-info"></a>Destek bilgisi ekle

**Destek** bölümünde, bir mühendislik kişisi ve müşteri desteği ile ilgili kişi için ad, e-posta ve telefon numarası belirtin. Ayrıca destek URL 'Leri sağlamanız gerekir. Microsoft bu bilgileri iş ve destek sorunları hakkında sizinle iletişim kurabilmesi gerektiğinde kullanabilir.

Bu bilgileri ekledikten sonra Kaydet ' i seçin **.**

## <a name="publish-your-offer"></a>Teklifinizi yayımlayın

Tüm bölümleri tamamladıktan sonra, bir sonraki adımınız teklifi Azure Marketi 'ne yayımlamaktır. Teklifinizi canlı hale getirme sürecini başlatmak için **Yayımla** düğmesini seçin. Bu süreç hakkında daha fazla bilgi için bkz. [Azure Marketi ve AppSource tekliflerini yayımlama](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

[Teklifinizin güncelleştirilmiş bir sürümünü](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-update-offer) dilediğiniz zaman yayımlayabilirsiniz. Örneğin, daha önce yayımlanmış bir teklifine yeni bir rol tanımı eklemek isteyebilirsiniz. Bunu yaptığınızda, teklifi zaten eklemiş olan müşteriler, bir güncelleştirmenin kullanılabildiğini bilmesini sağlayan Azure portal [**hizmet sağlayıcıları**](view-manage-service-providers.md) sayfasında bir simge görür. Her müşteri [, değişiklikleri gözden](view-manage-service-providers.md#update-service-provider-offers) geçirebilecek ve yeni sürüme güncelleştirmek isteyip istemediğinize karar verecek. 

## <a name="the-customer-onboarding-process"></a>Müşteri ekleme işlemi

Müşteri teklifinizi ekledikten sonra, [bir veya daha fazla belirli abonelik veya kaynak grubu temsilciliğini](view-manage-service-providers.md#delegate-resources)sağlar ve bu, Azure tarafından yetkilendirilen kaynak yönetimi için eklendi olacaktır. Bir müşteri bir teklifi kabul etmiş, ancak henüz hiç kaynak temsilcisi yoksa, Azure portal [**hizmet sağlayıcılar**](view-manage-service-providers.md) sayfasının en üstünde **sağlayıcı teklifleri** bölümünün üst kısmında bir not görür.

> [!IMPORTANT]
> Temsilciyi, müşterinin kiracısında, eklendi olan abonelik (veya eklendi olan kaynak gruplarını içeren) için [sahip yerleşik rolüne](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) sahip konuk olmayan bir hesap tarafından yapılmalıdır. Aboneliği temsil edebilen tüm kullanıcıları görmek için, müşterinin kiracısındaki bir Kullanıcı Azure portal aboneliği seçebilir, **erişim denetimini (IAM)** açabilir ve [sahip rolüne sahip tüm kullanıcıları görüntüleyebilir](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#view-roles-and-permissions).

Müşteri bir aboneliği (veya bir abonelik içinde bir veya daha fazla kaynak grubu) temsilcduktan sonra, bu abonelik için **Microsoft. ManagedServices** kaynak sağlayıcısı kaydedilir ve kiracınızdaki kullanıcılar, teklifinizdeki Yetkilendirmelere göre Temsilcili kaynaklara erişebilir.

> [!NOTE]
> Şu anda, abonelik Azure Databricks kullanıyorsa abonelikler (veya bir abonelik içindeki kaynak grupları) devredilemez. Benzer şekilde, bir abonelik (veya abonelik içindeki kaynak grupları) zaten Temsilcili ise, o abonelikte Databricks çalışma alanları oluşturmak mümkün değildir.

## <a name="next-steps"></a>Sonraki adımlar

- [Çapraz kiracı yönetim deneyimleri](../concepts/cross-tenant-management-experience.md)hakkında bilgi edinin.
- Azure portal **müşterilerime** giderek [müşterileri görüntüleyin ve yönetin](view-manage-customers.md) .
