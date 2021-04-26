---
title: Başvuru mimarisi diyagramı | Azure Marketi
description: Microsoft Commercial Market 'teki bir teklif için başvuru mimarisi diyagramı oluşturma.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 66e4d498ff7584188d680e35c89c6f10cc43c9cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604500"
---
# <a name="reference-architecture-diagram"></a>Başvuru mimarisi diyagramı

Başvuru mimarisi diyagramı, teklifinizin dayandığı altyapıyı temsil eden bir modeldir. Azure IP çözümleri için diyagramda, teklifinizin IP ortak satışı teknik gereksinimleri uyarınca Microsoft 'un bulut hizmetlerini nasıl kullandığını da göstermesi gerekir. Mimarinin kalitesini değerlendirmek için tasarlanmamıştır. Çözümünüzün Microsoft hizmetlerini nasıl kullandığını göstermek için tasarlanmıştır.

Başvuru mimarisi diyagramı birden çok araç aracılığıyla oluşturulabilir. Azure mimari modellerini belirleyen birden çok kalıbı içerdiğinden, Microsoft Visio 'yu öneririz.

Başvuru mimarisi diyagramları oluşturmak için faydalı bir başlangıç noktası, [Azure mimarisi modellerinden](/azure/architecture/browse/)faydalanır.

## <a name="typical-components-of-a-reference-architecture-diagram"></a>Başvuru mimarisi diyagramının tipik bileşenleri

- Azure kaynaklarını kullanan ve teklifinizle etkileşime geçen bulut Hizmetleri
- Teklifiniz tarafından tüketilen veri bağlantıları, veri katmanları ve veri Hizmetleri
- Güvenlik, kimlik doğrulaması ve teklifin kullanıcılarını denetlemek için kullanılan bulut Hizmetleri
- Kullanıcılara teklifleri sunan kullanıcı arabirimleri ve diğer hizmetler
- Karma veya şirket içi bağlantı ve tümleştirmeler veya her ikisinin birleşimi

Bu ekran görüntüsünde, başvuru mimarisi diyagramına bir örnek gösterilir.

[![Bu görüntü, örnek bir ortak satış mimarisi diyagramı örneğidir.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Bu örnek başvuru mimarisi diyagramı, bir makine öğrenimi algoritması aracılığıyla tahmin talep senaryolarıyla yardımcı olmak üzere intranet siteleriyle tümleştirilebilen bir dikey sektör sohbet botu içindir. Bu çözüm, farklı ERP sistemlerinden tedarik zinciri ve üretim zamanlaması verilerini kullanır. Botu, bir satışçının bir sipariş için olası teslimat tarihlerine ne zaman teslim edildiğini öğrenmek üzere tasarlanmıştır.

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market teklifi için ortak satışı yapılandırma](commercial-marketplace-co-sell.md)
