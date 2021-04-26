---
title: Azure Marketi 'nde yönetilen hizmet teklifiniz için planlar oluşturma
description: Microsoft Iş Ortağı Merkezi 'ni kullanarak Azure Marketi 'nde yönetilen hizmet teklifiniz için planlar oluşturmayı öğrenin.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 9b5526af03bdbefeb54633c49bbd43743555f60b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383237"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>Yönetilen hizmet teklifiniz için planlar oluşturma

Microsoft Commercial Market aracılığıyla satılan yönetilen hizmet teklifleri en az bir plana sahip olmalıdır. Aynı teklifde farklı seçeneklere sahip çeşitli planlar oluşturabilirsiniz. Bu planlar (bazen SKU 'Lar olarak adlandırılır) sürüm, para veya hizmet katmanları bakımından farklılık gösterebilir. Planlar hakkında ayrıntılı yönergeler için bkz. [ticari Market teklifleri Için planlar ve fiyatlandırma](./plans-pricing.md).

## <a name="create-a-plan"></a>Plan oluşturma

1. Iş Ortağı Merkezi 'nde teklifinizin **plana genel bakış** sekmesinde **+ Yeni plan oluştur**' u seçin.
2. Görüntülenen iletişim kutusunda **plan kimliği** altına benzersiz BIR plan kimliği girin. En fazla 50 küçük alfasayısal karakter, çizgi veya alt çizgi kullanın. **Oluştur**' u seçtikten sonra plan kimliğini değiştiremezsiniz. Bu KIMLIK, müşterileriniz tarafından görülebilir.
3. **Plan adı** kutusuna bu plan için benzersiz bir ad girin. En fazla 50 karakter kullanın. Bu ad, müşterileriniz tarafından görülebilir.
4. **Oluştur**’u seçin.

## <a name="define-the-plan-listing"></a>Plan listesini tanımlama

**Plan listeleme** sekmesinde, plan adı ve açıklamasını ticari Market 'te görünmesini istediğiniz şekilde tanımlayın.

1. **Plan adı** kutusu, bu plan için daha önce verdiğiniz adı görüntüler. İstediğiniz zaman değiştirebilirsiniz. Bu ad, ticari Market 'te teklif planının başlığı olarak görünür.
2. **Plan Özeti** kutusunda, planınızdan Market arama sonuçlarında kullanılabilecek kısa bir açıklama sağlayın.
3. **Plan açıklaması** kutusunda, bu planı teklifinizin içindeki diğer planlardan benzersiz ve farklı hale getiren şeyleri açıklayın.
4. Sonraki sekmeye geçmeden önce **Taslağı kaydet** ' i seçin.

## <a name="define-pricing-and-availability"></a>Fiyatlandırma ve kullanılabilirlik tanımlama

Yönetilen hizmet teklifleri için kullanılabilen tek fiyatlandırma modeli, **kendi lisansını getir (KLG)**. Bu, müşterilerinize bu teklifle ilgili maliyetleri doğrudan faturalayeceğiniz ve Microsoft size herhangi bir ücret ödemediği anlamına gelir.

Her planı herkese (genel) veya yalnızca belirli bir hedef kitleye (özel) görünür olacak şekilde yapılandırabilirsiniz.

> [!NOTE]
> Özel planlar, bulut çözümü sağlayıcısı (CSP) programının satıcısı aracılığıyla oluşturulan aboneliklerle desteklenmez.

> [!IMPORTANT]
> Bir plan ortak olarak yayımlandıktan sonra, özel olarak değiştiremezsiniz. Hangi müşterilerin teklif ve temsilci kaynaklarınızı kabul edeceğini denetlemek için özel bir plan kullanın. Ortak bir plan sayesinde, kullanılabilirliği belirli müşterilerle veya hatta belirli sayıda müşteriye kısıtlayamazsınız (ancak bunu yapmak isterseniz planı tamamen satmaya devam edebilirsiniz). Bir müşteri bir teklifi kabul ettikten sonra, teklifi yayımladığınızda rol tanımıyla yönetilen hizmetler kayıt ataması silme rolü olarak ayarlanmış bir yetkilendirme eklediyseniz, bir temsilciye erişimi kaldırabilirsiniz. Ayrıca müşteriye ulaşabilir ve erişiminizi kaldırmasını isteyebilirsiniz.

## <a name="make-your-plan-public"></a>Planınızı genel yapın

1. **Plan görünürlüğü** altında **ortak**' ı seçin.
2. **Taslağı kaydet**'i seçin. Plana genel bakış sekmesine dönmek için sol üst köşedeki **plana genel bakış** ' ı seçin.
3. Bu teklif için başka bir plan oluşturmak üzere **plana genel bakış** sekmesinde **+ Yeni plan oluştur** ' u seçin.

## <a name="make-your-plan-private"></a>Planınızı özel yapın

Azure abonelik kimliklerini kullanarak özel bir plana erişim verirsiniz. Bir kullanarak en fazla 10 abonelik kimliği el ile veya 10.000 abonelik kimliği ekleyebilirsiniz. CSV dosyası.

10 adede kadar abonelik kimliğini el ile eklemek için:

1. **Plan görünürlüğü** altında **özel**' i seçin.
2. Erişim vermek istediğiniz hedef kitlesinin Azure abonelik KIMLIĞINI girin.
3. İsteğe bağlı olarak, **Açıklama** kutusuna bu hedef kitle için bir açıklama girin.
4. Başka bir KIMLIK eklemek için **Kimlik Ekle (en fazla 10)** seçeneğini belirleyin.
5. Kimlikleri eklemeyi bitirdiğinizde **Taslağı kaydet**' i seçin.

Bir ile 10.000 adede kadar abonelik kimliği eklemek için. CSV dosyası:

1. **Plan görünürlüğü** altında **özel**' i seçin.
2. **Izleyiciyi dışarı aktar (CSV)** bağlantısını seçin. Bu, bir yükler. CSV dosyası.
3. Öğesini açın. CSV dosyası. **Kimlik** sütununda, erişim vermek istediğiniz Azure abonelik kimliklerini girin.
4.  **Açıklama**   sütununda, her giriş için bir açıklama ekleme seçeneğiniz vardır.
5.  **Tür**   sütununda, ****   kimliği olan her satıra SubscriptionID ekleyin.
6. Dosyayı olarak kaydedin. CSV dosyası.
7. Iş Ortağı Merkezi 'nde **Izleyiciyi Içeri aktar (CSV)** bağlantısını seçin.
8.  **Onayla**   iletişim kutusunda **Evet**' i seçin ve ardından öğesini karşıya yükleyin. CSV dosyası.
9.  **Taslağı kaydet**' i seçin.

## <a name="technical-configuration"></a>Teknik yapılandırma

Bu bölüm, müşteri kaynaklarını yönetmeye yönelik yetkilendirme bilgilerini içeren bir bildirim oluşturur. [Azure temsilci kaynak yönetimini](../lighthouse/concepts/azure-delegated-resource-management.md) etkinleştirmek için bu bilgiler gereklidir.

Hangi rollerin desteklendiğini ve yetkilendirmelerinizi tanımlamaya yönelik en iyi yöntemleri anlamak için [Azure Use senaryolarında kiracılar, roller ve kullanıcıları](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) gözden geçirin.

> [!NOTE]
> Yetkilendirme girdilerinizdeki kullanıcılar ve roller, planı etkinleştiren her müşteriye uygulanır. Erişimi belirli bir müşteriyle sınırlamak istiyorsanız, o müşterinin özel kullanımı için özel bir plan yayımlamanız gerekir.

### <a name="manifest"></a>Bildirim

1. **Bildirim**' ın altında, bildirim Için bir **Sürüm** belirtin. N. n. n biçimini kullanın (örneğin, 1.2.5).
2. **KIRACı kimliğinizi** girin. Bu, kuruluşunuzun Azure Active Directory (Azure AD) kiracı KIMLIĞIYLE ilişkili bir GUID 'dir; diğer bir deyişle, müşterilerinizin kaynaklarına erişecek olan kiracı yönetimi. Bu kullanışlı bir sahip değilseniz, Azure portal sağ üst tarafındaki hesap adınızın üzerine gelerek veya **Dizin Değiştir**' i seçerek bulabilirsiniz.

Teklifinizin yeni bir sürümünü yayımladığınızda ve güncelleştirilmiş bir bildirim oluşturmanız gerekiyorsa **+ Yeni bildirim**' ı seçin. Önceki bildirim sürümünden sürüm numarasını artırdığınızdan emin olun.

### <a name="authorizations"></a>Yetkilendirmeler

Yetkilendirmeler, yönetim kiracınızda, planı satın alan müşteriler için kaynaklara ve aboneliklere erişebilecek olan varlıkları tanımlar. Bu varlıkların her birine, belirli erişim düzeyleri veren bir yerleşik rol atanır.

Her plan için en fazla 20 yetkilendirmeler oluşturabilirsiniz.

> [!TIP]
> Çoğu durumda, bir dizi bireysel kullanıcı hesabı yerine bir Azure AD kullanıcı grubuna veya hizmet sorumlusuna roller atamak isteyeceksiniz. Bu, erişim gereksinimleriniz değiştiğinde planı güncelleştirmek ve yeniden yayınlamak zorunda kalmadan bireysel kullanıcılar için erişim eklemenize veya kaldırmanıza olanak sağlar. Azure AD gruplarına rol atarken, [Grup türü Office 365 değil, güvenlik olmalıdır](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Ek öneriler için bkz. [Azure 'Da kiracılar, roller ve kullanıcılar ile Ilgili kullanım senaryoları](../lighthouse/concepts/tenants-users-roles.md).

Her yetkilendirme için aşağıdaki bilgileri sağlamanız gerekir. Daha sonra, daha fazla Kullanıcı ve rol tanımı eklemek için gereken sayıda **Yetkilendirme** seçeneğini belirleyin.

* **AAD nesne kimliği**: bir kullanıcının, Kullanıcı grubunun veya UYGULAMANıN Azure AD tanımlayıcısı (rol tanımı tarafından tanımlandığı gibi) müşterilerinizin kaynaklarına verilecektir.
* **AAD nesne görünen adı**: müşterinin bu yetkilendirmenin amacını anlamasına yardımcı olacak kolay bir ad. Müşteri, kaynakları yetkilendirirken bu adı görür.
* **Rol tanımı**: listeden mevcut Azure AD yerleşik rollerinden birini seçin. Bu rol, **sorumlu kimliği** alanındaki kullanıcının, müşterilerinizin kaynaklarına sahip olacağı izinleri belirleyecek. Bu rollerin açıklamaları için bkz. [Azure Use Için](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse) [yerleşik roller](../role-based-access-control/built-in-roles.md) ve rol desteği.

> [!NOTE]
> Uygun yeni yerleşik roller Azure 'a eklendiğinden, bunlar buradan kullanılabilir hale gelir. Görünmeden önce biraz gecikme olabilir.

* **Atanabilir roller**: Bu seçenek yalnızca, bu yetkilendirme Için **rol tanımında** Kullanıcı erişimi Yöneticisi ' ni seçtiyseniz görünür. Bu durumda, buraya bir veya daha fazla atanabilir rol eklemeniz gerekir. **Azure AD nesne kimliği** alanındaki Kullanıcı, bu rolleri, düzeltilebilecek [ilkeleri dağıtmak](../lighthouse/how-to/deploy-policy-remediation.md)için gereken yönetilen kimliklere atayabilecektir. Normalde Kullanıcı erişimi Yöneticisi rolüyle ilişkili başka hiçbir izin bu kullanıcı için uygulanacaktır.

> [!TIP]
> Gerekirse, [bir temsilciye erişimi kaldırabilmeniz](../lighthouse/how-to/remove-delegation.md) Için, [yönetilen hizmetler kayıt ataması silme rolü](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)' ne ayarlanmış **rol tanımıyla** bir **Yetkilendirme** ekleyin. Bu rol atanmamışsa, atanan kaynaklar yalnızca müşterinin kiracısındaki bir kullanıcı tarafından kaldırılabilir.

Planınız için tüm bölümleri tamamladıktan sonra ek planlar oluşturmak için **+ Yeni plan oluştur** seçeneğini belirleyebilirsiniz. İşiniz bittiğinde devam etmeden önce **Taslağı kaydet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [İnceleme ve yayımlama](review-publish-offer.md)
