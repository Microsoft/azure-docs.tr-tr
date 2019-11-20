---
title: Market tekliflerini silme | Azure Marketi
description: Bulut İş Ortağı Portalı kullanarak Azure ve AppSource marketlerindeki teklifleri silme
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pabutler
ms.openlocfilehash: 6a75fbb0c4b9c364342a406a8076128346943101
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826775"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Azure Marketi ve AppSource tekliflerini veya SKU 'Larını silme

Çeşitli nedenlerle teklifinizin Microsoft Market 'ten geri çekmeye karar verebilir ve bu iki şekilde olabilir:

- *Teklif kaldırma* , yeni müşterilerin teklifinizi satın alıp dağıtmamasını sağlar, ancak lisans sözleşmenize ve ilgili kanunlarına göre desteklemeniz gereken mevcut müşterileri etkilemez. 
- *Teklif sonlandırma* , siz ve mevcut müşterileriniz arasındaki hizmet ve/veya lisans anlaşmasını sonlandırma işlemidir. 

Teklif kaldırma ve sonlandırma ile ilgili rehberlik ve ilkeler, [Microsoft Market Yayımcı Sözleşmesi](https://go.microsoft.com/fwlink/?LinkID=699560) ve [katılım ilkeleri](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) ( [askıya alma ve kaldırma sunan](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)bölüm) tarafından yönetilir. 

Bu makalede, desteklenen farklı silme senaryoları ve her bir işlemi gerçekleştirmek için gereken adımlar hakkında bilgi sağlar.  

> [!NOTE]
> Yalnızca **Düzenleyici** sekmesinin araç çubuğundaki **Sil** düğmesini seçerek, yayımlanmamış bir teklifi silebilirsiniz.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Yayımlanmış bir SKU 'YU Azure Marketi 'nden silme

Yayınlanmış bir SKU 'YU Azure Marketi 'nden aşağıdaki adımları kullanarak silebilirsiniz:

1.  [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.
2.  **Tüm teklifler** sayfasında teklifinizi seçin.  Teklifinizin **Düzenleyici** sekmesinde gösterilmesi gerekir.
3.  Sol araç çubuğunda **SKU 'lar** sekmesini seçin. 
4.  Silmek istediğiniz SKU 'YU seçin ve **Sil** düğmesine tıklayın.
5.  Teklifi Azure Marketi 'ne yeniden [yayımlayın](./cpp-publish-offer.md) .

Değiştirilen teklif Azure Marketi 'nde yayımlandıktan sonra, seçilen SKU artık Azure Marketi 'nde listelenmeyecektir ve Azure portal.


## <a name="roll-back-to-a-previous-sku-version"></a>Önceki bir SKU sürümüne geri dönme

Aşağıdaki adımları kullanarak yayımlanmış bir SKU 'nun geçerli sürümünü Azure Marketi 'nden silebilirsiniz. İşlem tamamlandığında, SKU önceki sürümüne geri döndürülür.

1. [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.
2. **Tüm teklifler** sayfasında teklifinizi seçin.  Teklifinizin **Düzenleyici** sekmesinde gösterilmesi gerekir.
3. Sol araç çubuğunda **SKU 'lar** sekmesini seçin. 
4. İlgili çözüm varlığının en son sürümünü disk sürümleri listesinden silin.  Teklif türüne bağlı olarak, bu alan **disk sürümü**, **paket sürümleri**veya benzer bir varlık olabilir. 
5. Teklifi Azure Marketi 'ne yeniden [yayımlayın](./cpp-publish-offer.md) .

Değiştirilen teklif, Adzure marketi 'nde yayımlandıktan sonra, listelenen SKU 'nun geçerli sürümü listelenmez. Azure Marketi 'nde ve Azure portal.  SKU 'SU önceki sürümüne geri döndürülür.


## <a name="delete-a-live-offer"></a>Canlı bir teklifi silme

Canlı bir teklifi kaldırmak için çeşitli yordamsal, iş ve yasal yönleri vardır. Azure Marketi 'nden canlı bir teklifi kaldırmak için destek ekibinden rehberlik almak üzere aşağıdaki adımları izleyin:

1.  [Bir olay oluştur](https://go.microsoft.com/fwlink/?linkid=844975) sayfasını kullanarak veya [bulut iş ortağı portalı](https://cloudpartner.azure.com/)sağ üst köşesinde bulunan **destek** ' e tıklayarak bir destek bileti oluşturun.

2.  **Sorun türü** listesinde özel teklif türünü seçin ve **Kategori** listesinden **yayımlanmış teklifi kaldır** ' ı seçin.

3.  İsteği gönderir.

Destek ekibi, teklif silme sürecinde size rehberlik eder.

> [!NOTE]
> Bir teklif (veya SKU) silindiğinde, bu teklifin (veya SKU) geçerli satınalmaları etkilenmeyecektir. Bu satın almalar daha önce olduğu gibi çalışmaya devam edecektir. Ancak, silinen teklifler veya SKU 'Lar gelecekteki satın alma işlemleri için kullanılamaz.


## <a name="next-steps"></a>Sonraki adımlar

Teklifleri yönetmek için kullanılan temel işlemlere alışdıktan sonra, bir Microsoft [Market teklifinin](../cpp-marketplace-offers.md)örneğini oluşturmaya hazırlanın.
