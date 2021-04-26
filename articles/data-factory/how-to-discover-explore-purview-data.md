---
title: Purview kullanarak ADF 'de veri bulma ve keşfetme
description: Purview kullanarak Azure Data Factory verileri keşfetme, araştırma hakkında bilgi edinin
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: 031e7ceee739ffd666ad367bd6778865a297a25d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386280"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Purview kullanarak ADF 'de veri bulma ve keşfetme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede bir Azure purview hesabını bir Data Factory kaydedeceğinizi caksınız. Bu bağlantı, Azure purview varlıklarını keşfetmenize ve ADF özellikleri aracılığıyla bunlarla etkileşime geçmesini sağlar. 

ADF 'de aşağıdaki görevleri gerçekleştirebilirsiniz: 
- Anahtar sözcüklere göre varlıkları bulmak için üstteki arama kutusunu kullanın 
- Meta veriler, kökenini, ek açıklamalar temelinde verileri anlayın 
- Bu verileri, bağlı hizmetler veya veri kümeleri ile veri fabrikanıza bağlama 

## <a name="prerequisites"></a>Önkoşullar 
- [Azure purview hesabı](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Azure purview hesabını Data Factory 'a bağlama](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Data Factory 'de Azure purview kullanma 

Data Factory Azure purview kullanın, bu purview hesabına erişiminizin olmasını gerektirir. Data Factory geçirilir-purview izniniz aracılığıyla. Örnek olarak, bir Curator izin rolünüzün olması, Azure purview tarafından taranan meta verileri düzenleyebileceksiniz. 

### <a name="data-discovery-search-datasets"></a>Veri bulma: veri kümelerini ara 

Azure purview tarafından kaydedilen ve taranan verileri bulmak için Data Factory portalının üst merkezinde arama çubuğunu kullanabilirsiniz. Tüm kuruluş verilerinizi aramak için Azure takip görünümü ' nü seçtiğinizden emin olun. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Veri kümelerini yerine getirmek için ekran görüntüsü.":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Data Factory kaynaklarıyla veri kümeleri üzerinden gerçekleştirebileceğiniz eylemler 
Azure purview tarafından arama yaptığınız veriler üzerinde doğrudan bağlantılı hizmet, veri kümesi veya veri akışı oluşturabilirsiniz.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Azure purview tarafından arama yaptığınız veriler üzerinde bağlı hizmet, veri kümesi veya veri akışını doğrudan nasıl oluşturabileceğiniz gösteren ekran görüntüsü.":::

##  <a name="nextsteps"></a>Sonraki adımlar 

- [Azure purview 'da Azure Data Factory varlıkları kaydetme ve tarama](../purview/register-scan-azure-synapse-analytics.md)
- [Azure purview Veri Kataloğu 'nda veri arama](../purview/how-to-search-catalog.md)