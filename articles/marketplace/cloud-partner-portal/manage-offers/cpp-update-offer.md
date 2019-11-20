---
title: Market tekliflerini güncelleştir | Azure Marketi
description: Bulut İş Ortağı Portalı kullanarak Azure ve AppSource marketlerindeki teklifleri güncelleştirme
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: b3f579dbdc943b2380c9de3dde6b2ebf4754d4d1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826720"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Azure Marketi ve AppSource tekliflerini güncelleştirme

Yayımlandıktan sonra teklifiniz için uygulayabileceğiniz çeşitli türlerde güncelleştirmeler vardır.  [Bulut iş ortağı portalı](https://cloudpartner.azure.com/) , aşağıdakiler de dahil olmak üzere bir teklifin özniteliklerini düzgün şekilde değiştirmenizi sağlar:

-  Mevcut bir SKU 'ya yeni sanal makine (VM) görüntüsü veya paket sürümü ekleniyor
-  SKU 'nun kullanılabildiği bölgeleri değiştirme
-  Yeni SKU 'Lar ekleme
-  Teklifler veya SKU 'Lar için Market meta verilerini güncelleştirme 
-  Kullandıkça Öde tekliflarında fiyatlandırmayı güncelleştirme

Portalda Ayrıca özellikleri karşılaştırma ve bir teklifin özelliklerinin geçmişini görüntüleme gibi özellikler de bulunur ve bu da değişiklikleri yönetmenize yardımcı olur.  Bir teklif veya SKU 'yu değiştirdikten sonra değişiklikler "canlı" olmadan önce yeniden yayımlanması gerekir.  Bu makalede Market teklifinizi güncelleştirmenin farklı yönleri açıklanmaktadır.

## <a name="unpermitted-changes-to-an-offersku"></a>Teklif/SKU 'ya izin verilmeyen değişiklikler

Bir teklifin veya SKU 'nun Market 'te yayımlandıktan sonra değiştirilemeyen bazı öznitelikleri vardır.  İlgili alanlar portalın **Düzenleyici** sekmesinde devre dışıdır, örneğin:  

- Teklif KIMLIĞI ve yayımcı KIMLIĞI
- SKU KIMLIĞI 
- Mevcut SKU 'ların veri diski sayısı
- Mevcut SKU 'ların faturalandırma/lisans modeli değişiklikleri
- Sürüm etiketleri, örneğin: `1.0.1`


## <a name="common-update-operations"></a>Ortak güncelleştirme işlemleri

Aşağıdaki bölümlerde, en çok güncelleştirme işlemlerinin nasıl gerçekleştirileceği açıklanmaktadır.  Bu işlemler tüm teklif türleri için kullanılamaz.  Bu işlemlerden herhangi birini başlatmak için Bulut İş Ortağı Portalı oturum açmalısınız.


### <a name="update-offer-contacts"></a>Teklif kişilerini Güncelleştir

Teklifinizin destek kişilerini güncelleştirmek için aşağıdaki adımları kullanın.
1. **Tüm teklifler** sayfasında teklifi seçin.
2. **Kişiler** sekmesini seçin. kişilerinizi güncelleştirin.
3. **Kaydet** düğmesini seçin.
4. Yayımlama işlemini başlatmak için **Yayımla** ' yı seçin.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Bir teklifin veya SKU 'nun kullanılabildiği bölgeleri değiştirme

Zaman içinde teklifinizin/SKU 'nuzu daha fazla bölgede kullanılabilir hale getirmek isteyebilirsiniz.
Alternatif olarak, belirli bir bölgedeki teklif/SKU 'YU desteklemeyi durdurmak isteyebilirsiniz.
Bu değişiklikleri uygulamak için aşağıdaki adımları izleyin.

1. **Tüm teklifler** sayfasında, güncelleştirmek istediğiniz teklifi bulun.

Azure Market için şunları sunar:

1. **SKU 'lar** sekmesini seçin.  Değiştirilecek SKU 'YU seçin.
1. **Ülke/bölge kullanılabilirliği** alanının altındaki **ülkeleri Seç** düğmesine tıklayın.
1. Bölge kullanılabilirliği iletişim kutusunda, bu SKU 'nun bölgelerini ekleyin veya kaldırın.

AppSource teklifleri için:

1. **Storefront details** sekmesini seçin.
1. **Desteklenen ülkeler/bölgeler** etiketinin yanındaki **Desteklenen ülkeler/bölgeler**' e tıklayın. 
1. Desteklenen ülkeler/bölgeler iletişim kutusunda bu teklifin bölgelerini ekleyin veya kaldırın.

Her iki Market için:

1. Yayımlama işlemini başlatmak için **Yayımla** ' ya tıklayın. 

Bir SKU yeni bir bölgede kullanılabilir duruma getiriliyorsa, **fiyatlandırma verilerini dışarı aktar** işlevini kullanarak söz konusu bölge için fiyatlandırma belirtebilirsiniz. Daha önce kullanılabilir olan bir bölgeyi geri ekliyorsanız fiyatlandırma değişikliklerine izin verilmediğinden fiyatlandırmayı güncelleştiremezsiniz.


### <a name="add-a-new-sku"></a>Yeni bir SKU Ekle 

Yeni bir SKU 'YU mevcut bir teklif için kullanılabilir hale getirmek için aşağıdaki adımları kullanın:

1. **Tüm teklifler** sayfasında, teklifi bulun.
3. **SKU 'lar** formu altında **Yeni SKU Ekle** ' ye tıklayın ve açılır pencerede bir **SKU kimliği** sağlayın.
4. [Bir sanal makineyi yayımlama teklifi](../virtual-machine/cpp-publish-offer.md)ile ayrıntılandırılan adımların geri kalanını izleyin.
5. Yayımlama işlemini başlatmak için **Yayımla** ' ya tıklayın.


### <a name="update-offer-marketplace-assets"></a>Teklif marketi varlıklarını Güncelleştir

Şirket logolarınız, teklif açıklaması vb. gibi Market metin tabanlı ve görüntü varlıklarını güncelleştirmeniz gereken senaryolara sahip olabilirsiniz. Bu varlıkları güncelleştirmek için aşağıdaki adımları kullanın.

1. **Tüm teklifler** sayfasında teklifinizi bulun. 
2. **Market** sekmesini seçin ve teklifinizin *Market sekmesi* konusundaki yönergeleri izleyin.
3. Yayımlama işlemini başlatmak için **Yayımla** ' ya tıklayın.


### <a name="update-pricing-on-published-offers"></a>Yayımlanmış tekliflerle ilgili fiyatlandırmayı güncelleştirme

Kullandıkça Öde teklifiniz yayımlandıktan sonra, mevcut SKU 'nun fiyatını artıramazsınız.  Bunun yerine, aynı teklif altında bir SKU oluşturun, eski SKU 'yu silin ve sonra teklifinizi yeniden yayımlayın. Daha önce yayımlanmış tekliflerle ilgili fiyatı azaltabilirsiniz. Teklif fiyatlarınızı azaltmak için:

1. Fiyatlandırmasını azaltmak istediğiniz SKU 'YU seçin.
2. Daha düşük fiyatı, ilk kullandığınız mekanizmaya göre ayarlamanız gerekir: doğrudan Portal Kullanıcı arabiriminde veya içeri/dışarı aktarma elektronik tablosu ile.
3. **Kaydet** düğmesine tıklayın.
4. Yayımlama işlemini başlatmak için **Yayımla** ' ya tıklayın.

Fiyatlandırma, Market 'te canlı olduktan sonra yeni müşterilere görünür hale gelir ve tüm yeni müşteriler daha sonra yeni azaltılan fiyata ödeme yapar.  Mevcut müşteriler için fiyat azalmasıyla, Fiyat azalmasıyla ilgili faturalandırma döngüsünün başlangıcına geriye dönük olarak yansıtılmıştır.  Bir fiyat azalmasıyla ilgili bir dönem için zaten faturalandırılmışsa, daha düşük bir fiyat elde etmek üzere bir sonraki faturalandırma sürecinde bir para iadesi alırlar.


## <a name="compare-feature"></a>Karşılaştırma özelliği

Yayımlanmış bir teklifte değişiklik yaptığınızda, değişiklikleri denetlemek için *Karşılaştır* özelliğini kullanabilirsiniz. Bu özelliği kullanmak için:

1. Düzenleme işleminin herhangi bir noktasında teklifiniz için **Düzenleyici** sekmesinde **Karşılaştır** düğmesine tıklayabilirsiniz.
2. Bir karşılaştırma penceresi, Market teklifiyle karşılaştırıldığında, bu teklif için kaydedilen değişikliklerin yan yana sürümlerini görüntüler. 

![Düzenleyici sekmesindeki teklifi Karşılaştır düğmesi](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Yayımlama eylemlerinin geçmişi

Geçmiş yayımlama etkinliğini görüntülemek için Bulut İş Ortağı Portalı sol dikey menü çubuğundan **Geçmiş** sekmesini seçin.  Geçmiş sayfası, birkaç özelliğe göre esnek filtreleme sağlar ve sütun sıralamasını destekler.  Her yayımlama olayı zaman damgalanır.  Daha fazla bilgi için bkz. [Denetim geçmişi sayfası](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Sonraki adımlar

[Yayınlanmış BIR SKU veya teklifi silmek](./cpp-delete-offer.md)için bulut iş ortağı portalı de kullanabilirsiniz.
