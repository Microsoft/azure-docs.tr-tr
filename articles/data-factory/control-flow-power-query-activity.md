---
title: Azure Data Factory etkinlik Power Query
description: Bir Data Factory işlem hattının veri denetimi özellikleri için Power Query etkinliğini nasıl kullanacağınızı öğrenin
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: fc4f20db55f8e7e0b2f92cb8309c1c128b235089
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385464"
---
# <a name="power-query-activity-in-data-factory"></a>Data Factory 'de Power Query etkinliği

Power Query etkinliği, bir Data Factory işlem hattında ölçeklendirmekte olan verileri yürütmek için Power Query Mash-ups oluşturup yürütmenize olanak tanır. Yeni kaynaklar menü seçeneğinden yeni bir Power Query Mash oluşturun veya işlem hattınızla bir güç etkinliği ekleyebilirsiniz.

![Fabrika kaynakları bölmesinde Power Query gösteren ekran görüntüsü.](media/data-flow/power-query-wrangling.png)

Daha önce Azure Data Factory içindeki veri denetimi veri akışı menü seçeneğinden yazıldı. Bu, yeni bir Power Query etkinliğinden yazmak üzere değiştirilmiştir. Etkileşimli veri araştırması gerçekleştirmek ve sonra çalışmanızı kaydetmek için Power Query Mash Düzenleyicisi içinde doğrudan çalışabilirsiniz. Tamamlandıktan sonra Power Query etkinliğinizi alabilir ve bir işlem hattına ekleyebilirsiniz. Azure Data Factory, verileri otomatik olarak ölçeklendirecek ve Azure Data Factory veri akışı Spark ortamını kullanarak veri wrangini kullanıma sunacaktır.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Veri akışı betiğine çeviri

Power Query etkinliğinizdeki ölçeklendirmeye ulaşmak için Azure Data Factory komut dosyanızı ```M``` bir veri akışı betiğine çevirir, böylece Azure Data Factory veri akışı Spark ortamını kullanarak Power Query ölçeklendirmenize olanak sağlayabilirsiniz. Kod içermeyen veri hazırlama kullanarak denetimi veri akışınızı yazın. Kullanılabilir işlevlerin listesi için bkz. [dönüştürme işlevleri](wrangling-functions.md).

## <a name="next-steps"></a>Sonraki adımlar

[Azure Data Factory Power Query](wrangling-tutorial.md) kullanarak veri denetimi kavramları hakkında daha fazla bilgi edinin
