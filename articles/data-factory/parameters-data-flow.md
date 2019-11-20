---
title: Azure Data Factory eşleme veri akışı parametreleri
description: Data Factory işlem hatlarından eşleme veri akışını Parametreleştirme hakkında bilgi edinin
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a1051d67bf45e96f82833ef8190008204cdc90b
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "72387546"
---
# <a name="mapping-data-flow-parameters"></a>Veri akışı parametrelerini eşleme



Azure Data Factory veri akışlarını eşleme, parametrelerin kullanımını destekler. Daha sonra deyimlerinizin tamamında kullanabileceğiniz veri akışı tanımınızın içinde parametreler tanımlayabilirsiniz. Parametre değerleri, veri akışını Yürüt etkinliği aracılığıyla çağıran işlem hattı tarafından ayarlanabilir. Veri akışı etkinlik ifadelerindeki değerleri ayarlamak için üç seçeneğiniz vardır:

* Ardışık düzen denetim akışı ifade dilini kullanarak dinamik bir değer ayarlayın
* Dinamik bir değer ayarlamak için veri akışı ifade dilini kullanın
* Statik değişmez değer ayarlamak için ifade dilini kullanın

Verilerinizi genel amaçlı, esnek ve yeniden kullanılabilir hale getirmek için bu özelliği kullanın. Veri akışı ayarlarını ve deyimlerini bu parametrelerle parametreleştirebilirsiniz.

> [!NOTE]
> İşlem hattı denetim akışı ifadelerini kullanmak için, veri akışı parametresi dize türünde olmalıdır.

## <a name="create-parameters-in-mapping-data-flow"></a>Eşleme veri akışında parametreler oluşturma

Veri akışınıza parametreler eklemek için, genel özellikleri görmek üzere veri akışı tuvalinin boş kısmına tıklayın. Ayarlar bölmesinde ' Parametreler ' adlı bir sekme görürsünüz. Yeni bir parametre oluşturmak için ' yeni ' düğmesine tıklayın. Her parametre için bir ad atamanız, bir tür seçmeniz ve isteğe bağlı olarak varsayılan bir değer ayarlamanız gerekir.

![Veri akışı parametreleri oluşturma](media/data-flow/create-params.png "Veri akışı parametreleri oluşturma")

Parametreler herhangi bir veri akışı ifadesinde kullanılabilir. Parametreler $ ile başlar ve sabittir. ' Parametreler ' sekmesi altında, Ifade oluşturucusunun içindeki kullanılabilir parametrelerin listesini bulacaksınız.

![Veri akışı parametre ifadesi](media/data-flow/parameter-expression.png "Veri akışı parametre ifadesi")

## <a name="use-parameters-in-your-data-flow"></a>Veri akışındaki parametreleri kullanma

* Dönüştürme ifadelerinizin içindeki parametre değerlerini kullanabilirsiniz. Parametre listesini Ifade oluşturucusunun Parametreler sekmesinde bulabilirsiniz. ![Veri akışı parametrelerini kullanma](media/data-flow/params9.png "Uo veri akışı parametreleri ")

* Ayrıca parametreler, kaynak ve havuz dönüştürme ayarlarınıza yönelik dinamik değerleri yapılandırmak için de kullanılır. Yapılandırılabilir alanların içine tıkladığınızda, "dinamik contect Ekle" bağlantısının göründüğünü görürsünüz. Buraya tıkladığınızda, dinamik değerleri kullanmak için parametreleri kullanabileceğiniz bir Ifade oluşturucusuna götürür. ![Veri akışı dinamik içeriği](media/data-flow/params6.png "Data akış dinamik içeriği ")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>İşlem hattından eşleme veri akışı parametrelerini ayarlama

Veri akışınızı parametrelerle oluşturduktan sonra, veri akışını Yürüt etkinliğinin bulunduğu bir işlem hattından çalıştırabilirsiniz. Etkinliği işlem hattı Tuvalinize ekledikten sonra, etkinliğin ' Parametreler ' sekmesinde kullanılabilir veri akışı parametreleri görüntülenir.

![Veri akışı parametresini ayarlama](media/data-flow/parameter-assign.png "Veri akışı parametresini ayarlama")

Parametre veri türü dize ise, parametre değerlerini ayarlamak için metin kutusuna tıkladığınızda bir işlem hattı veya bir veri akışı ifadesi girmeyi seçebilirsiniz. Ardışık düzen ifadesini seçerseniz, işlem hattı ifade paneli sunulur. Dize ilişkilendirme sözdiziminin içinde `'@{<expression>}'` kullanarak işlem hattı işlevlerini eklediğinizden emin olun; örneğin:

```'@{pipeline().RunId}'```

Parametreniz dize türünde değilse, her zaman veri akışı Ifade Oluşturucusu görüntülenir. Burada, parametresinin veri türüyle eşleşen herhangi bir ifade veya sabit değer girebilirsiniz. Aşağıda, veri akışı ifadesi ve ifade oluşturucusunun bir sabit dizesi örnekleri verilmiştir:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Her eşleme veri akışı, ardışık düzen ve veri akışı ifade parametrelerinin herhangi bir birleşimini içerebilir. 

![Veri akışı parametreleri örneği](media/data-flow/parameter-example.png "Veri akışı parametreleri örneği")



## <a name="next-steps"></a>Sonraki adımlar
* [Veri akışı yürütme etkinliği](control-flow-execute-data-flow-activity.md)
* [Denetim akışı ifadeleri](control-flow-expression-language-functions.md)
