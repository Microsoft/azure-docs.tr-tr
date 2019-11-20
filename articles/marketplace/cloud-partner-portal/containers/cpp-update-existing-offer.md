---
title: Mevcut bir Azure kapsayıcıları teklifini güncelleştirme | Azure Marketi
description: Azure Marketi 'nde mevcut bir kapsayıcı teklifini güncelleştirme.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 40cd375b11eb5b7ad5943fea9839b6339a7d002f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823077"
---
# <a name="update-an-existing-container-offer"></a>Mevcut bir kapsayıcı teklifini güncelleştirme

Bu makalede, [bulut iş ortağı portalı](https://cloudpartner.azure.com/)kapsayıcınızı güncelleştirme konusunda farklı yönleri adım adım anlatılmaktadır.

Teklifinizi güncelleştirmek isteyebileceğiniz bazı nedenler vardır; örneğin:

-  Mevcut SKU 'Lara yeni bir kapsayıcı görüntüsü sürümü ekleniyor.
-  Yeni SKU 'Lar ekleniyor.
-  Teklif veya bağımsız SKU 'Lar için Market meta verilerini güncelleştirme.

Bu değişiklikler konusunda size yardımcı olmak için Portal **karşılaştırma** ve **geçmiş** özellikleri sağlar.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Bir kapsayıcı teklifinde veya SKU 'sunda izin verilmeyen değişiklikler

Teklif Azure Marketi 'nde canlı olduktan sonra değiştirilemeyen bir kapsayıcı teklifinin veya SKU 'sunun öznitelikleri vardır. Aşağıdaki ayarları değiştiremezsiniz:

-  Teklifin **TEKLIF kimliği** ve **Yayımcı kimliği**
-  Mevcut SKU 'ların **SKU kimliği**
-  Sürüm etiketleri, örneğin: `1.0.1`
-  Faturalandırma/lisans modeli, mevcut SKU 'Larda değişir

## <a name="common-update-operations"></a>Ortak güncelleştirme işlemleri

Aşağıdaki güncelleştirme işlemleri yaygındır.

### <a name="update-container-image-version-for-a-sku"></a>SKU için kapsayıcı görüntüsü sürümünü güncelleştirme

Bir kapsayıcı görüntüsünün güvenlik düzeltme ekleri, ek özellikler vb. ile düzenli olarak güncelleştirilmesini yaygındır. Bu senaryoda, aşağıdaki adımları kullanarak SKU 'sunun başvurduğu kapsayıcı görüntüsünü güncelleştirmek istersiniz:

1. [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.
2. **Tüm teklifler**altında, güncelleştirmek istediğiniz teklifi bulun.
3. **SKU 'lar** sekmesinde, güncelleştirilecek kapsayıcı görüntüsüyle ilişkili SKU 'yu seçin.
4. **Kapsayıcı görüntüsü**altında yeni bir kapsayıcı görüntüsü eklemek Için **+ yeni görüntü sürümü** ' nü seçin.
5. Yeni kapsayıcı **görüntüsü sürümlerini**belirtin. Görüntü sürümünün önceki sürümlerle aynı etiket yönergeleriyle izlenmesi gerekir. Sürüm etiketleri X. Y. Z biçiminde olmalıdır; burada X, Y ve Z tamsayılardır. Sağladığınız yeni sürümün önceki sürümlerden daha büyük olduğunu doğrulayın.
6. Yeni kapsayıcı görüntüsü sürümünüzü Azure Market 'Te yayımlamak için iş akışını başlatmak üzere **Yayımla** ' yı seçin.

### <a name="add-a-new-sku"></a>Yeni bir SKU Ekle

Teklifinizin yeni bir SKU 'SU için kullanılabilir olmasını sağlamak için aşağıdaki adımları kullanın:

1. [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.
2. **Tüm teklifler**altında, güncelleştirmek istediğiniz teklifi bulun.
3. **SKU 'lar** sekmesinde, **Yeni SKU Ekle** ' yi seçin ve açılır pencerede bir **SKU kimliği** sağlayın.
4. Kapsayıcıyı [Yayımla teklifinde](./cpp-publish-offer.md)açıklanan adımları kullanarak kapsayıcıyı yeniden yayımlayın.
5. Yeni SKU 'nuzu yayımlamak üzere iş akışını başlatmak için **Yayımla** ' yı seçin.

### <a name="update-offer-marketplace-metadata"></a>Teklif marketi meta verilerini Güncelleştir

Teklifinizle ilişkili Market meta verilerini güncelleştirmek için aşağıdaki adımları kullanın. (Örneğin, şirket adı, logo ve vb.)

1. [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.
2. **Tüm teklifler**altında, güncelleştirmek istediğiniz teklifi bulun.
3. **Market** sekmesine gidin. meta verileri değişiklik yapmak Için [kapsayıcıyı Yayımla](./cpp-publish-offer.md) teklif teklifi makalesindeki yönergeleri kullanın.
4. Değişikliklerinizi yayımlamak için iş akışını başlatmak üzere **Yayımla** ' yı seçin.

## <a name="compare-feature"></a>Karşılaştırma özelliği

Yayımlanmış bir teklifte değişiklik yaptığınızda, yaptığınız değişiklikleri denetlemek için **Karşılaştır** özelliğini kullanabilirsiniz.

### <a name="to-use-the-compare-feature"></a>Karşılaştırma özelliğini kullanmak için:

1. Herhangi bir noktada, sizin teklifiniz için karşılaştırma ' yı seçin.
2. Pazarlama varlıklarının ve meta verilerin yan yana sürümlerine bakın.


## <a name="history-of-publishing-actions"></a>Yayımlama eylemlerinin geçmişi

Geçmiş yayımlama etkinliğini görmek için Bulut İş Ortağı Portalı sol gezinti menü çubuğunda **Geçmiş** sekmesini seçin. Azure Market tekliflerinizin kullanım ömrü boyunca alınan zaman damgamış eylemleri görebilirsiniz.