---
title: Azure Marketi 'nde sanal makine teklifi oluşturma
description: Azure Marketi için yeni bir sanal makine (VM) teklifi oluşturmak için gereken adımları listeler.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 1e10bd22b91b51fb811601e49fad06d8f8b30127
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817094"
---
# <a name="create-virtual-machine-offer"></a>Sanal Makine teklifi oluştur

Bu bölümde, Azure Marketi için yeni bir sanal makine (VM) teklif isteği oluşturmak için gereken adımlar listelenmektedir.  Her teklif Azure Marketi 'nde kendi varlığı olarak görünür ve bir veya daha fazla SKU ile ilişkilendirilir.  Bir VM teklifi, aşağıdaki varlık ve destekleyici hizmetler gruplarından oluşur: 

![Bir VM teklifi için varlıklar](./media/publishvm_002.png)

Olmadığı

|  **Varlık grubu**   |  **Açıklama**  |
|  ---------------   |  ---------------  |
|    SKU            |  Bir teklifin en küçük satın alınabilir alınırken birimi. Tek bir teklif (ürün sınıfı) ile ilişkili birden fazla SKU, desteklenen özellikler, VM görüntü türleri ve faturalandırma modelleri arasında ayrım yapmak için kullanılabilir. |
|  Market       | Pazarlama, yasal ve lider yönetimi varlıkları ve belirtimleri içerir.  <ul><li> Pazarlama varlıkları, teklif adı, açıklama ve logo içerir</li> <li> Yasal varlıklar, bir gizlilik ilkesi, kullanım koşulları ve diğer yasal belgeleri içerir</li>  <li> Lider yönetimi ilkesi, Azure Marketi Son Kullanıcı portalından müşteri adaylarını nasıl işleyeceğinizi belirtmenizi sağlar.</li> </ul> |
| Destek            | Destek iletişim ve ilke bilgilerini içerir |
| Test Sürüşü         | Son kullanıcıların satın almadan önce teklifinizi test edebilmesi için varlık tanımlar |
|  |  |


## <a name="new-offer-form"></a>Yeni teklif formu

[Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açtıktan sonra sol menü çubuğunda **+ yeni teklif** öğesine tıklayın. Elde edilen menüde, **sanal makineler** ' e tıklayarak **yeni teklif** formunu GÖRÜNTÜLEYIN ve yeni bir VM teklifi için varlık tanımlama sürecini başlatın. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Yeni sanal makine teklifi Kullanıcı arabirimi seçimi](./media/publishvm_003.png)

> [!WARNING]
> **Sanal makineler** seçeneği gösterilmezse veya etkinleştirilmemişse, hesabınız bu teklif türünü oluşturmak için izne sahip olmaz.  Lütfen bir Geliştirici hesabına kaydolma dahil olmak üzere bu teklif türü için tüm [önkoşulları](./cpp-prerequisites.md) karşıladığınızı kontrol edin.


## <a name="next-steps"></a>Sonraki adımlar

Bu bölümdeki sonraki konular, **yeni teklif** sayfasındaki (bir VM teklif türü için) sekmeleri yansıtır.  Her makalede, yeni VM teklifiniz için varlık gruplarını ve destekleyici hizmetleri tanımlamak üzere ilişkili sekmenin nasıl kullanılacağı açıklanmaktadır.

- [Teklif Ayarları sekmesi](./cpp-offer-settings-tab.md)
- [SKU'lar sekmesi](./cpp-skus-tab.md)
- [Test Sürüşü sekmesi](./cpp-test-drive-tab.md)
- [Market sekmesi](./cpp-marketplace-tab.md)
- [Destek sekmesi](./cpp-support-tab.md)
