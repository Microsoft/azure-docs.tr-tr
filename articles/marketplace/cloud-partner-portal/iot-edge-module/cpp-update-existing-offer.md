---
title: Mevcut bir Azure IoT Edge modülü teklifini güncelleştirme | Azure Marketi
description: Azure Marketi 'nde mevcut bir IoT Edge modülü teklifini güncelleştirme.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: cd0167e1af5bf8ef667df88237d83e9f33ed41f9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813386"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Mevcut bir IoT Edge modülü teklifini güncelleştirme

Bu makalede, [Bulut İş Ortağı Portalı](https://cloudpartner.azure.com/) IoT Edge modülünüzü güncelleştirme ve ardından teklifi yeniden yayınlama adımları adım adım anlatılmaktadır.

Teklifinizi güncelleştirmek isteyebileceğiniz bazı nedenler vardır; örneğin:

-  Mevcut SKU 'Lara yeni bir IoT Edge modülü görüntü sürümü ekleniyor.
-  Yeni SKU 'Lar ekleniyor.
-  Teklif veya bağımsız SKU 'Lar için Market meta verilerini güncelleştirme.

Bu değişiklikler konusunda size yardımcı olmak için Portal **karşılaştırma** ve **geçmiş** özelliklerini sunmaktadır.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>IoT Edge modül teklifinde veya SKU 'suna izin verilmeyen değişiklikler

Teklif Azure Marketi 'nde canlı olduktan sonra değiştirilemeyen bir IoT Edge modülü teklifinin veya SKU 'sunun öznitelikleri vardır. Aşağıdaki ayarları değiştiremezsiniz:

-  Teklifin **TEKLIF kimliği** ve **Yayımcı kimliği**
-  Mevcut SKU 'ların **SKU kimliği**
-  Sürüm etiketleri, örneğin: `1.0.1`
-  Faturalandırma/lisans modeli, mevcut SKU 'Larda değişir

## <a name="common-update-operations"></a>Ortak güncelleştirme işlemleri

Aşağıdaki güncelleştirme işlemleri yaygındır.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>SKU için IoT Edge modülü görüntü sürümünü güncelleştirme

IoT Edge modül görüntüsünün, güvenlik düzeltme ekleri, ek özellikler ve benzeri düzenli olarak güncelleştirilmesini sağlamak yaygındır. Bu senaryoda, aşağıdaki adımları kullanarak SKU 'nuzun IoT Edge modül görüntüsünü güncelleştirmek istersiniz:

1.  [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.

2.  **Tüm teklifler**altında, güncelleştirmek istediğiniz teklifi bulun.

3.  **SKU 'lar** sekmesinde, güncelleştirmek için IoT Edge modülü görüntüsüyle ilişkili SKU 'yu seçin.

4.  Yeni bir IoT Edge modül görüntüsü eklemek için **Edge modülü görüntüsü**' nün altında **+ yeni görüntü sürümü** ' nü seçin.

5.  Yeni IoT Edge modülü **görüntü sürümlerini**sağlayın. Görüntü sürümünün önceki sürümlerle aynı etiket yönergeleriyle izlenmesi gerekir. Sürüm etiketleri X. Y. Z biçiminde olmalıdır; burada X, Y ve Z tamsayılardır. Sağladığınız yeni sürümün önceki sürümlerden daha büyük olduğunu doğrulayın.

6.  Yeni IoT Edge modülü sürümünüzü Azure Marketi 'Nde yayımlamak için iş akışını başlatmak üzere **Yayımla** ' yı seçin.

### <a name="add-a-new-sku"></a>Yeni bir SKU Ekle

Teklifinizin yeni bir SKU 'SU için kullanılabilir olmasını sağlamak için aşağıdaki adımları kullanın: 

1.  [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.

2.  **Tüm teklifler**altında, güncelleştirmek istediğiniz teklifi bulun.

3.  **SKU 'lar** sekmesinde, **Yeni SKU Ekle** ' yi seçin ve açılır pencerede bir **SKU kimliği** sağlayın.

4.  IoT Edge modülünü [Azure Marketi 'nde IoT Edge modülü yayımlama](./cpp-publish-offer.md)bölümünde açıklanan adımları kullanarak yeniden yayımlayın.

5.  Yeni SKU 'nuzu yayımlamak üzere iş akışını başlatmak için **Yayımla** ' yı seçin.


### <a name="update-offer-marketplace-metadata"></a>Teklif marketi meta verilerini Güncelleştir

Teklifinizle ilişkili Market meta verilerini güncelleştirmek için aşağıdaki adımları kullanın. (Örneğin: şirket adı, logolar, vb.)

1.  [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.

2.  **Tüm teklifler**altında, güncelleştirmek istediğiniz teklifi bulun.

3.  **Market** sekmesine gidin. meta verileri değişiklikleri yapmak Için [Azure Market 'Te IoT Edge modülü yayımlama](./cpp-publish-offer.md) makalesindeki yönergeleri kullanın.

4.  Değişikliklerinizi yayımlamak için iş akışını başlatmak üzere **Yayımla** ' yı seçin.

## <a name="compare-feature"></a>Karşılaştırma özelliği

Yayımlanmış bir teklifte değişiklik yaptığınızda, yaptığınız değişiklikleri denetlemek için **Karşılaştır** özelliğini kullanabilirsiniz. 

**Karşılaştırma özelliğini kullanmak için:**

1.  Herhangi bir noktada, sizin teklifiniz için **karşılaştırma** ' yı seçin.

    ![Özellik düğmesini Karşılaştır](./media/iot-edge-module-compare.png)


2.  Pazarlama varlıklarının ve meta verilerin yan yana sürümlerine bakın.


## <a name="history-of-publishing-actions"></a>Yayımlama eylemlerinin geçmişi

Geçmiş yayımlama etkinliğini görmek için Bulut İş Ortağı Portalı sol gezinti menü çubuğunda **Geçmiş** sekmesini seçin. Azure Market tekliflerinizin kullanım ömrü boyunca alınan zaman damgamış eylemleri görebilirsiniz.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
