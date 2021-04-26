---
title: Azure Data Factory denetimi veri akışı ile çalışmaya başlama
description: Denetimi veri akışını kullanarak Azure Data Factory veri hazırlama hakkında bir öğretici
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: cf15d6f669718cca8b99d67a7912d3959d9c191f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732515"
---
# <a name="prepare-data-with-data-wrangling"></a>Veri denetimi ile veri hazırlama

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Data Factory 'deki veri denetimi, ADF 'de yerel olarak etkileşimli Power Query Mash-ups oluşturmanızı ve ardından bunları ADF işlem hattının içinde ölçeklendirmenizi sağlar.

> [!NOTE]
> ADF 'de Power Query etkinliği şu anda genel önizlemede kullanılabilir

## <a name="create-a-power-query-activity"></a>Power Query etkinliği oluşturma

Azure Data Factory Power Query oluşturmanın iki yolu vardır. Tek yönlü, artı simgesine tıklamanız ve fabrika kaynakları bölmesinde **veri akışı** ' nı seçmesidir.

> [!NOTE]
> Daha önce veri akış iş akışında veri denetimi özelliği bulunur. Şimdi, veri denetimi Mash 'i şuradan oluşturacaksınız ```New > Power query```

![Fabrika kaynakları bölmesinde Power Query gösteren ekran görüntüsü.](media/data-flow/power-query-wrangling.png)

Diğer yöntem, işlem hattı tuvalinin Etkinlikler bölmesinde bulunur. **Power Query** Accordion açın ve **Power Query** etkinliğini tuval üzerine sürükleyin.

![Veri denetimi seçeneğini vurgulayan ekran görüntüsü.](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>Power Query veri bir denetimi etkinliği yazın

Power Query Mash için bir **kaynak veri kümesi** ekleyin. Mevcut bir veri kümesini seçebilir ya da yeni bir veri kümesi oluşturabilirsiniz. Havuz veri kümesi de seçebilirsiniz. Bir veya daha fazla kaynak veri kümesi seçebilirsiniz, ancak şu anda yalnızca bir havuza izin verilir. Bir havuz veri kümesi seçilmesi isteğe bağlıdır, ancak en az bir kaynak veri kümesi gereklidir.

![Denetimi](media/wrangling-data-flow/tutorial4.png)

Power Query çevrimiçi mashup düzenleyicisini açmak için **Oluştur** ' a tıklayın.

![Power Query çevrimiçi karma düzenleyici 'yi açan Oluştur düğmesini gösteren ekran görüntüsü.](media/wrangling-data-flow/tutorial5.png)

Kod içermeyen veri hazırlama kullanarak denetimi Power Query yazın. Kullanılabilir işlevlerin listesi için bkz. [dönüştürme işlevleri](wrangling-functions.md). ADF, Azure Data Factory veri akışı Spark ortamını kullanarak Power Query ölçekli bir şekilde yürütebilmeniz için, a betiğini bir veri akışı betiğine çevirir.

![Veri denetimi Power Query yazma sürecini gösteren ekran görüntüsü.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Power Query veri bir denetimi etkinliği çalıştırma ve izleme

Bir Power Query etkinliğinin işlem hattı hata ayıklamasını yürütmek için işlem hattı tuvalindeki **Hata Ayıkla** ' ya tıklayın. İşlem hattınızı yayımladıktan sonra, **tetikleyici artık** son yayımlanan işlem hattının isteğe bağlı bir çalıştırmasını yürütür. Power Query işlem hatları, tüm mevcut Azure Data Factory tetikleyicilerle zaman alabilir.

![Power Query veri bir denetimi etkinliğinin nasıl ekleneceğini gösteren ekran görüntüsü.](media/wrangling-data-flow/tutorial3.png)

Tetiklenen bir Power Query etkinliği çalışmasının çıkışını görselleştirmek için **izleyici** sekmesine gidin.

![Tetiklenen bir denetimi Power Query etkinliğin çalışmasının çıkışını gösteren ekran görüntüsü.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Sonraki adımlar

[Eşleme veri akışı oluşturmayı](tutorial-data-flow.md)öğrenin.
