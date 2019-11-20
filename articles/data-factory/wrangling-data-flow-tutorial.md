---
title: Azure Data Factory denetimi veri akışı ile çalışmaya başlama
description: Denetimi veri akışını kullanarak Azure Data Factory veri hazırlama hakkında bir öğretici
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8ae109045381dba77610dedc5bb95e233b213eee
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682273"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Denetimi veri akışı ile veri hazırlama

## <a name="create-a-wrangling-data-flow"></a>Denetimi veri akışı oluşturma

Azure Data Factory içinde bir denetimi veri akışı oluşturmanın iki yolu vardır. Tek yönlü, artı simgesine tıklamanız ve fabrika kaynakları bölmesinde **veri akışı** ' nı seçmesidir.

![Denetimi](media/wrangling-data-flow/tutorial7.png)

Diğer yöntem, işlem hattı tuvalinin Etkinlikler bölmesinde bulunur. **Taşıma ve dönüştürme** Accordion açın ve **veri akışı** etkinliğini tuvale sürükleyin.

Her iki yöntemde, açılan bölmede **Yeni veri akışı oluştur** ' u seçin ve **Wrangling veri akışı**' nı seçin. Tamam'a tıklayın.

![Denetimi](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Denetimi veri akışı yazma

Denetimi veri akışınız için bir **kaynak veri kümesi** ekleyin. Mevcut bir veri kümesini seçebilir ya da yeni bir veri kümesi oluşturabilirsiniz. Havuz veri kümesi de seçebilirsiniz. Bir veya daha fazla kaynak veri kümesi seçebilirsiniz, ancak şu anda yalnızca bir havuza izin verilir. Bir havuz veri kümesi seçilmesi isteğe bağlıdır, ancak en az bir kaynak veri kümesi gereklidir.

> [!NOTE]
> Sınırlı önizleme için yalnızca ADLS Gen 2 ile ayrılmış metin desteklenir. 

![Denetimi](media/wrangling-data-flow/tutorial4.png)

Power Query çevrimiçi mashup düzenleyicisini açmak için **Oluştur** ' a tıklayın.

![Denetimi](media/wrangling-data-flow/tutorial5.png)

Kod içermeyen veri hazırlama kullanarak denetimi veri akışınızı yazın. Kullanılabilir işlevlerin listesi için bkz. [dönüştürme işlevleri](wrangling-data-flow-functions.md)/

![Denetimi](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Bir denetimi veri akışını çalıştırma ve izleme

Bir denetimi veri akışının işlem hattı hata ayıklama çalıştırmasını yürütmek için işlem hattı tuvalinde **Hata Ayıkla** ' ya tıklayın. Veri akışınızı yayımladığınızda, **tetikleyici artık** son yayımlanan işlem hattının isteğe bağlı bir çalıştırmasını yürütür. Wrangling veri akışları, tüm mevcut Azure Data Factory tetikleyicilerle zaman alabilir.

![Denetimi](media/wrangling-data-flow/tutorial3.png)

Tetiklenen bir denetimi veri akışı etkinliği çalışmasının çıkışını görselleştirmek için **izleyici** sekmesine gidin.

![Denetimi](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Sonraki adımlar

[Eşleme veri akışı oluşturmayı](tutorial-data-flow.md)öğrenin.