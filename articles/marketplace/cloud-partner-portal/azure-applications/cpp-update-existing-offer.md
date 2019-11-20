---
title: Mevcut bir Azure uygulaması teklifini güncelleştirme | Azure Marketi
description: Azure Marketi 'nde mevcut bir Azure uygulaması teklifini güncelleştirme.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pabutler
ms.openlocfilehash: 6854e445ea63639866e9e39a6afc725237bbc8fe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826107"
---
# <a name="update-an-existing-azure-application-offer"></a>Mevcut bir Azure uygulaması teklifini güncelleştirme

Yayımlandıktan ve canlı olduktan sonra teklifiniz için yapmak isteyebileceğiniz çeşitli türlerde güncelleştirmeler vardır. Teklifinizin yeni sürümünüzde yaptığınız tüm değişiklikler, Market 'te yansıtacak şekilde kaydedilmelidir ve yeniden yayımlanmalıdır. Bu makalede, [bulut iş ortağı portalı](https://cloudpartner.azure.com/)yönetilen uygulama teklifinizi güncelleştirmenin farklı yönleri adım adım anlatılmaktadır.

Teklifinizi güncelleştirmek isteyebileceğiniz bazı nedenler vardır; örneğin:

- Mevcut SKU 'Lara yeni bir görüntü sürümü ekleniyor.
- Yeni SKU 'Lar ekleniyor.
- Teklif veya bağımsız SKU 'Lar için Market meta verilerini güncelleştirme.

Bu değişiklikler konusunda size yardımcı olmak için Portal **karşılaştırma** ve **geçmiş** özellikleri sağlar.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Azure uygulaması teklifinde veya SKU 'sunda izin verilmeyen değişiklikler

Teklif Azure Marketi 'nde canlı olduktan sonra değiştirilemeyen bir kapsayıcı teklifinin veya SKU 'sunun öznitelikleri vardır. Aşağıdaki ayarları değiştiremezsiniz:

- Teklifin teklif KIMLIĞI ve yayımcı KIMLIĞI
- Mevcut SKU 'ların SKU KIMLIĞI
- Sürüm etiketleri, örneğin: `1.0.1`
- Faturalandırma/lisans modeli, mevcut SKU 'Larda değişir

## <a name="common-update-operations"></a>Ortak güncelleştirme işlemleri

Aşağıdaki güncelleştirme işlemleri yaygındır.

### <a name="update-image-version-for-a-sku"></a>SKU için güncelleştirme görüntüsü sürümü

Bir resmin güvenlik düzeltme ekleri, ek özellikler vb. ile düzenli olarak güncelleştirilmesini yaygındır. Bu senaryoda, aşağıdaki adımları kullanarak SKU 'sunun başvurduğu görüntüyü güncellemek istersiniz:

1. [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.
2. **Tüm teklifler**altında, güncelleştirmek istediğiniz teklifi bulun.
3. **SKU 'lar** sekmesinde güncelleştirilecek görüntüyle ilişkili SKU 'yu seçin.
4. Yeni görüntü eklemek için **+ yeni görüntü sürümü** ' nü seçin.
5. Yeni görüntü sürümlerini sağlayın. Görüntü sürümünün önceki sürümlerle aynı etiket yönergeleriyle izlenmesi gerekir. Sürüm etiketleri X. Y. Z biçiminde olmalıdır; burada X, Y ve Z tamsayılardır. Sağladığınız yeni sürümün önceki sürümlerden daha büyük olduğunu doğrulayın.
6. Yeni kapsayıcı görüntüsü sürümünüzü Azure Market 'Te yayımlamak için iş akışını başlatmak üzere **Yayımla** ' yı seçin.

### <a name="add-a-new-sku"></a>Yeni bir SKU Ekle

Teklifinizin yeni bir SKU 'SU için kullanılabilir olmasını sağlamak için aşağıdaki adımları kullanın:

1. [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.
2. **Tüm teklifler**altında, güncelleştirmek istediğiniz teklifi bulun.
3. **SKU 'lar** sekmesinde, **Yeni SKU Ekle** ' yi seçin ve açılır pencerede bir **SKU kimliği** sağlayın.
4. [Azure Uygulama teklifi yayımlama](./cpp-publish-offer.md)bölümünde açıklanan adımları kullanarak teklifi yeniden yayımlayın.
5. Yeni SKU 'nuzu yayımlamak üzere iş akışını başlatmak için **Yayımla** ' yı seçin.

### <a name="update-offer-marketplace-metadata"></a>Teklif marketi meta verilerini Güncelleştir

Teklifinizle ilişkili Market meta verilerini güncelleştirmek için aşağıdaki adımları kullanın. (Örneğin: şirket adı, logolar, vb.)

1. [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.
2. **Tüm teklifler**altında, güncelleştirmek istediğiniz teklifi bulun.
3. **Market** sekmesine gidin. meta verileri değişiklik yapmak için [Azure Uygulama teklifini yayımlama](./cpp-publish-offer.md) bölümündeki yönergeleri kullanın.
4. Değişikliklerinizi yayımlamak için iş akışını başlatmak üzere **Yayımla** ' yı seçin.
 
>[!Note]
>Bulut çözümü sağlayıcıları (CSP) iş ortağı kanalı kabul etme artık kullanılabilir.  Teklifinizi Microsoft CSP iş ortağı kanalları aracılığıyla pazarlama hakkında daha fazla bilgi için lütfen bkz. [bulut çözümü sağlayıcıları](../../cloud-solution-providers.md) .

## <a name="deleting-an-existing-offer"></a>Mevcut bir teklifi silme

Teklifinizi Market 'ten kaldırmaya karar verebilirsiniz. Bir teklifin silinmesi, bu teklifin geçerli satınalmalarını etkilemez. Bu müşteri satın alımları, daha önce olduğu gibi çalışmaya devam edecektir. Ancak, bu teklif, silme işlemi tamamlandıktan sonra yeni satın almalar için kullanılamaz.

Teklif Sonlandırma, sizinle mevcut müşterileriniz arasındaki hizmet ve/veya lisans sözleşmesini iptal etme işlemidir.
Teklif kaldırma ve sonlandırma ile ilgili rehberlik ve ilkeler, Microsoft Market Yayımcı Sözleşmesi (bkz. Bölüm 7) ve katılım Ilkelerine tabidir (bkz. Bölüm 6,2).

Daha fazla bilgi için bkz. [Azure Marketi 'Nden silme ve teklif/SKU](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Karşılaştırma özelliği

Yayımlanmış bir teklifte değişiklik yaptığınızda, yaptığınız değişiklikleri denetlemek için Karşılaştır özelliğini kullanabilirsiniz.

Karşılaştırma özelliğini kullanmak için:

1. Herhangi bir noktada, sizin teklifiniz için karşılaştırma ' yı seçin.
2. Pazarlama varlıklarının ve meta verilerin yan yana sürümlerine bakın.

## <a name="history-of-publishing-actions"></a>Yayımlama eylemlerinin geçmişi

Geçmiş yayımlama etkinliğini görmek için Bulut İş Ortağı Portalı sol gezinti menü çubuğunda **Geçmiş** sekmesini seçin. Azure Market tekliflerinizin kullanım ömrü boyunca alınan zaman damgamış eylemleri görebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure uygulama teklifi](./cpp-azure-app-offer.md)