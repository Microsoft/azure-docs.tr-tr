---
title: Azure Uygulama teklifi için SKU 'Ları yapılandırma | Azure Marketi
description: Azure yönetilen uygulaması ve bir Azure çözüm şablonu için SKU 'Ları yapılandırma.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 29b69499b708726b10947bd3202d3a52893f5c90
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826171"
---
# <a name="azure-application-skus-tab"></a>Azure Uygulama SKU 'Ları sekmesi

Bu makalede, Azure uygulamanız için SKU 'Lar oluşturmak üzere SKU 'Ların nasıl kullanılacağı açıklanır. 

> [!IMPORTANT]
> Bir SKU yapılandırma adımları, yönetilen uygulama teklifi ve bir çözüm şablonu teklifi için farklıdır. Bu farklılıklar Bu makalede belgelenmiştir. 

## <a name="configure-azure-application-skus"></a>Azure Uygulama SKU 'Larını yapılandırma

### <a name="create-a-new-sku"></a>Yeni bir SKU oluştur

Yeni bir SKU oluşturmak için aşağıdaki adımları kullanın:

1. **SKU 'lar** sekmesini seçin.
2. SKU 'Lar altında **+ yenı SKU**' yı seçin.

    ![Yeni SKU istemi](./media/azureapp-plus-sku.png)

3. Yeni SKU açılır penceresinde bir **SKU kimliği**yazın. Bu kimlik 50 karakterle sınırlıdır ve yalnızca küçük harf, alfasayısal karakter, kısa çizgi veya alt çizgi içermelidir. SKU KIMLIĞI bir kısa çizgi ile bitemez.
4. SKU KIMLIĞI, ürün URL 'Lerinde, Kaynak Yöneticisi şablonlarda (varsa) ve faturalandırma raporlarında müşteriler tarafından görülebilir. Teklifin yayımlandıktan sonra bu kimliği değiştiremezsiniz.

### <a name="sku-details-for-a-solution-template"></a>Çözüm şablonu için SKU ayrıntıları

Sonraki ekran yakalama, bir çözüm şablonu için SKU ayrıntıları formunu gösterir.

![Çözüm şablonu için SKU Ayrıntıları formu](./media/azureapp-sku-details-solutiontemplate.png)

Aşağıdaki SKU değerlerini sağlayın.  Bir yıldız işaretiyle eklenen alanlar gereklidir.

|    Alan         |       Açıklama                                                            |
|  ---------       |     ---------------                                                          |
|  **Başlık\***     | SKU için bir başlık. Bu başlık bu öğenin galerisinde görüntülenir.   |
| **Özet\***    | SKU 'nun kısa Özet açıklaması. (En fazla uzunluk 100 karakterdir.)  |
| **Açıklama\*** | SKU 'nun ayrıntılı bir açıklaması. Temel HTML desteklenir.                 | 
| **SKU türü\***   | Azure Uygulama çözümünün türü bu senaryo için ***çözüm şablonu** ' nu seçin. |
| **Bulut kullanılabilirliği\*** | SKU 'nun konumu. Varsayılan değer **Genel Azure**' dır.  <b/>**Genel Azure** -uygulama, Market tümleştirmesi olan tüm genel Azure bölgelerinde müşterilere dağıtılabilir.  <b/>**Azure Kamu Bulutu** -uygulama, Azure Kamu bulutunda dağıtılır. Microsoft, [Azure Kamu](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)'ya yayımlamadan önce yayımcılar testini ve bu ortamda beklendiği şekilde çözümlerini doğrulamayı önerir. Hazırlamak ve test etmek için bir [deneme hesabı](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)isteyin.  |
| **Bu özel bir SKU mı?\*** | Bu SKU yalnızca belirli bir müşteri grubu için kullanılabiliyorsa **Evet** ' i seçin. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Kamu, ABD Federal, eyalet, yerel veya kaş ve iş ortakları tarafından bu varlıkları sunmaya uygun olan müşteriler için denetimli erişime sahip bir Devlet kurumunun bulutunu sunar.


### <a name="sku-details-for-managed-application"></a>Yönetilen uygulama için SKU ayrıntıları

Sonraki ekran yakalama, yönetilen bir uygulama için SKU ayrıntıları formunu gösterir.

   ![Yönetilen uygulama için SKU Ayrıntıları formu](./media/azureapp-sku-details-managedapplication.png)

Aşağıdaki SKU ayarlarını yapılandırın. Bir yıldız işaretiyle eklenen alanlar gereklidir.

|    Alan         |       Açıklama                                                            |
|  ---------       |     ---------------                                                          |
|  **Başlık\***     | SKU için bir başlık. Bu başlık bu öğenin galerisinde görüntülenir.   |
| **Özet\***    | SKU 'nun kısa Özet açıklaması. (En fazla uzunluk 100 karakterdir.)  |
| **Açıklama\*** | SKU 'nun ayrıntılı bir açıklaması. Temel HTML desteklenir.                 | 
| **SKU türü\***   | Azure Uygulama çözümünün türü bu senaryo için ***yönetilen uygulama** ' yı seçin. 
| **Bulut kullanılabilirliği\*** | SKU 'nun konumu. Varsayılan değer **Genel Azure**' dır.  <b/>**Genel Azure** -uygulama, Market tümleştirmesi olan tüm genel Azure bölgelerinde müşterilere dağıtılabilir.  <b/>**Azure Kamu Bulutu** -uygulama, Azure Kamu bulutunda dağıtılır. Microsoft, [Azure Kamu](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)'ya yayımlamadan önce yayımcılar testini ve bu ortamda beklendiği şekilde çözümlerini doğrulamayı önerir. Hazırlamak ve test etmek için bir [deneme hesabı](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)isteyin.   Microsoft Azure Kamu, ABD Federal, eyalet, yerel veya kaş ve iş ortakları tarafından bu varlıkları sunmaya uygun olan müşteriler için denetimli erişime sahip bir Devlet kurumunun bulutunu sunar. |
| **Bu özel bir SKU mı?\*** | Bu SKU yalnızca belirli bir müşteri grubu için kullanılabiliyorsa **Evet** ' i seçin. |
| **Ülke/bölge kullanılabilirliği\*** | Kullanılabilir ülkelerin/bölgelerin listesini görüntülemek için **bölgeleri seçin ' i** kullanın. Her bir ülkeyi/bölgeyi kontrol edin ve ardından çeklerinizi kaydetmek için **Tamam** ' ı seçin.  <b/>![ülke ve bölge kullanılabilirlik listesi](./media/azure-app-select-country-region.png)  |
| **Eski fiyatlandırma\*** | SKU 'nun aylık ABD Doları cinsinden fiyatı. Fiyatlar, yapılandırma sonrasında geçerli döviz kurları kullanılarak yerel para birimi olarak ayarlanır. Bu ayarları son olarak sahipmenizden bu yana doğrulayın. Her ülke/bölgenin fiyatını ayrı ayrı ayarlamak veya görüntülemek için lütfen fiyatlandırma elektronik tablosunu dışarı aktarın ve özel fiyatlandırmayla içeri aktarın.  Fiyatlandırma verilerinin içeri/dışarı aktarılmasını etkinleştirmek için fiyatlandırma değişikliklerinizi kaydetmeniz gerekir.  |
| **Basitleştirilmiş para birimi fiyatlandırması\*** | SKU 'nun aylık ABD Doları cinsinden fiyatı. Bu, eski fiyatlandırmayla aynı olmalıdır. Daha fazla bilgi için bkz. [Basitleştirilmiş para birimi fiyatlandırması](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Çözüm şablonu için paket ayrıntıları

   ![Çözüm şablonu için paket ayrıntıları](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Aşağıdaki **paket ayrıntıları** değerlerini sağlayın.  Bir yıldız işaretiyle eklenen alanlar gereklidir.

- **Sürüm\*** -karşıya yükleyeceğiniz paketin sürümü. Sürüm etiketleri X. Y. Z biçiminde olmalıdır; burada X, Y ve Z tamsayılardır.
- **Paket dosyası (. zip)\*** -Bu paket, bir. zip dosyasına kaydedilen aşağıdaki dosyaları içerir.
  - MainTemplate. JSON-çözümü/uygulamayı dağıtmak ve çözüm için tanımlanan kaynakları oluşturmak için kullanılan dağıtım şablonu dosyası. Daha fazla bilgi için bkz. [dağıtım şablonu dosyalarını yazma](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - Createuıdefinition. JSON-bu dosya, bu çözümü/uygulamayı sağlamak için Kullanıcı arabirimi oluşturmak üzere Azure portal tarafından kullanılır. Daha fazla bilgi için bkz. [Yönetilen uygulamanız için Azure Portal Kullanıcı arabirimi oluşturma](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

  >[!IMPORTANT] 
  >Bu paketin, bu uygulamayı sağlamak için gereken iç içe geçmiş şablonları veya betikleri içermesi gerekir. MainTemplate. JSON dosyası ve Createuıdefinition. JSON dosyası kök klasörde olmalıdır.


### <a name="package-details-for-managed-application"></a>Yönetilen uygulama için paket ayrıntıları

   ![Yönetilen uygulama için paket ayrıntıları](./media/azureapp-sku-pkgdetails-managedapplication.png)

Aşağıdaki paket ayrıntılarını sağlayın.  Bir yıldız işaretiyle eklenen alanlar gereklidir.

- **Sürüm\*** -karşıya yükleyeceğiniz paketin sürümü. Sürüm etiketleri X. Y. Z biçiminde olmalıdır; burada X, Y ve Z tamsayılardır.
- **Paket dosyası (. zip)\*** -Bu paket, bir. zip dosyasına kaydedilen aşağıdaki dosyaları içerir.
  - applianceMainTemplate. JSON-çözümü/uygulamayı dağıtmak ve tanımlanan kaynakları oluşturmak için kullanılan dağıtım şablonu dosyası. Daha fazla bilgi için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Resource Manager şablonları oluşturma ve dağıtma](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition. JSON-bu dosya, bu çözümü/uygulamayı sağlamak için Kullanıcı arabirimi oluşturmak üzere Azure portal tarafından kullanılır. Daha fazla bilgi için bkz. [Yönetilen uygulamanız için Azure Portal Kullanıcı arabirimi oluşturma](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate. JSON-yalnızca Microsoft. Solution/Geremi kaynağını içeren şablon dosyası. Daha fazla bilgi için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Bu kaynağın aşağıdaki temel özelliklerine göz önünde edin:
    - "tür"-Market ile yönetilen uygulama söz konusu olduğunda değer "Market" olmalıdır.
    - "Managedresourcegroupıd"-müşterinin aboneliğindeki applianceMainTemplate. JSON içinde tanımlanan tüm kaynakların dağıtılacağı kaynak grubu.
    - "Publisherpackageıd"-paketi benzersiz bir şekilde tanımlayan dize. Bu değerin şu şekilde oluşturulması gerekir: [publisherID] öğesinin bir birleşimi. [OfferId]-Preview [SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Bu paketin, bu uygulamayı sağlamak için gereken iç içe geçmiş şablonları veya betikleri içermesi gerekir. Bu dosyalar kök klasöründe olmalıdır: MainTemplate. JSON, applianceMainTemplate. JSON ve applianceCreateUIDefinition. JSON.

- **Kiracı kimliği\*** -kuruluşunuzun Azure Active Directory Kiracı kimliği.
- **JIT erişimini etkinleştirmek istiyor musunuz?\*** -bu teklifi kullanarak müşteri dağıtımları Için tam zamanında yönetim erişimini etkinleştirmek üzere **Evet** ' i seçin.

  >[!NOTE] 
  >JıT 'i etkinleştirirseniz, JıT erişimini desteklemek için Createuıdefinition. json dosyasını güncelleştirmeniz gerekir.

Yönetilen bir uygulama için yetkilendirme ve Ilke ayarlarını yapılandırmanız gerekir.


#### <a name="authorization"></a>Yetkilendirme

Yönetilen kaynak grubu için izin vermek istediğiniz kullanıcı, Grup veya uygulamanın Azure Active Directory tanımlayıcısını ekleyin. Verilen izin rol tanımı kimliği tarafından belirtilir. Bir sahip, katkıda bulunan veya herhangi bir özel rol olabilir.


#### <a name="policy-settings"></a>İlke ayarları

Yönetilen uygulamanın uyumlu olduğu ilkeleri ekleyin. Azure Kaynak ilkeleri hakkında daha fazla bilgi edinin, bkz. [Azure ilkesi nedir?](../../../governance/policy/overview.md)

   ![Yönetilen bir uygulama için yetkilendirme ve ilke ayarları](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Yeni bir yetkilendirme oluşturmak için:**

1. **Yetkilendirme**altında **+ Yeni yetkilendirme**' yi seçin.
2. **Asıl kimlik**için, yönetilen kaynak grubu için izin vermek istediğiniz kullanıcı, Grup veya uygulamanın Azure Active Directory tanımlayıcısını yazın. Verilen izin rol tanımıyla belirtilir.
3. **Rol tanımı**için, açılan listeden şu seçeneklerden birini seçin: sahip veya katkıda bulunan. Daha fazla bilgi için bkz. [Azure kaynakları için yerleşik roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Birden çok yetkilendirmeler eklenebilir. Ancak, bir Active Directory Kullanıcı grubu oluşturmanız ve KIMLIĞINI "PrincipalId" içinde belirtmeniz önerilir. Bu, SKU 'YU güncelleştirmek zorunda kalmadan kullanıcı grubuna daha fazla Kullanıcı eklenmesini sağlayacaktır.

**Yeni bir ilke oluşturmak için:**

1. **Ilke ayarları**altında **+ Yeni ilke**' yi seçin.
2. **Ilke adı**için, ilke için bir ad girin. Ad uzunluğu en fazla 50 karakterdir.
3. **İlkeler**için, açılan listeden seçeneklerden birini belirleyin. Uygulama verileri kullandığında veri sağlayıcısının etkinleştirilmesini istediğini ilkeyi seçin. Daha fazla bilgi için bkz. [Azure Ilke örnekleri](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Yönetilen bir uygulama için ilke ayarları](./media/azureapp-sku-policy-settings.png)

4. **Ilke SKU**'su için, Ilke SKU 'su türü olarak ücretsiz veya standart ' ı seçin. Denetim ilkeleri için standart SKU gereklidir.


## <a name="next-steps"></a>Sonraki adımlar

Teklifinizi daha ayrıntılı olarak anlayacaksınız ve pazarlama varlıklarınızı [Market sekmesinden](./cpp-marketplace-tab.md)sağlayacaksınız. 
