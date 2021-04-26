---
title: Azure Veri Kataloğu 'nda ilgili veri varlıklarını görüntüleme
description: Bu makalede, Azure Veri Kataloğu 'nda seçili bir veri varlığının ilgili veri varlıklarının nasıl görüntüleneceği açıklanır.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: c1c5ddcacfc529f8fc4ab9a70cea540c44da9fa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674793"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Azure Veri Kataloğu 'nda ilgili veri varlıkları nasıl görüntülenir?

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Azure Veri Kataloğu, seçili bir veri varlığı ile ilgili veri varlıklarını görüntülemenize ve aralarındaki ilişkileri görüntülemenize olanak sağlar. 

## <a name="supported-data-sources"></a>Desteklenen veri kaynakları 
Veri varlıklarını aşağıdaki veri kaynaklarından kaydettiğinizde Azure Veri Kataloğu, seçili veri varlıkları arasındaki JOIN ilişkilerine ilişkin meta verileri otomatik olarak kaydeder. 

- SQL Server
- Azure SQL Veritabanı
- MySQL
- Oracle

> [!NOTE]
> İki veri varlığı arasındaki ilişkiyi içeri aktarmak üzere veri kataloğu için, her iki varlığı de aynı anda kaydetmeniz gerekir. Bunlardan birini ayrı olarak eklediyseniz, aralarındaki ilişkiyi içeri aktarmak için yeniden ekleyin ve diğer veri varlığını ekleyin.

## <a name="view-related-data-assets"></a>İlgili veri varlıklarını görüntüleme
Seçili bir veri kümesiyle ilgili veri varlıklarını görüntülemek için, aşağıdaki görüntüde gösterildiği gibi **ilişkiler** sekmesini kullanın: 

![Azure Veri Kataloğu-ilgili veri varlıklarını görüntüleme](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

Bu örnekte, seçili **ProductSubcategory** veri varlığı için iki ilişki vardır: 

- Ürün tablosunun ProductSubcategoryID sütununda, seçili ProductSubcategory tablosunun ProductSubcategoryID sütunuyla bir yabancı anahtar ilişkisi vardır. 
- ProductSubCategory tablosunun ProductCategoryID sütununda, seçili ProductCategory tablosunun ProductCategoryID sütunuyla bir yabancı anahtar ilişkisi vardır.

> [!NOTE]
> İlişki ağacı görünümündeki okun yönüne dikkat edin.  

Sütunun tam adı gibi daha fazla ayrıntı görmek için fareyi üzerine taşıyın ve aşağıdaki görüntüye benzer bir açılan pencere görürsünüz: 

![Azure Veri Kataloğu-Ilişki açılan kutusu](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Zaten kaydedilmiş varlıklar arasındaki ilişkileri dahil etmek için, bu varlıkları yeniden kaydedin.

## <a name="next-steps"></a>Sonraki adımlar
- [Veri varlıklarını yönetme](data-catalog-how-to-manage.md)
