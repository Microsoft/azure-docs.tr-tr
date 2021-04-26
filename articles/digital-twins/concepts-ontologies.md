---
title: Ontology nedir?
titleSuffix: Azure Digital Twins
description: Belirli bir etki alanında modelleme için DTDL sektör ontolobahi hakkında bilgi edinin
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3393856b25040cff603ea2ef51e8adbcba78dc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102034702"
---
# <a name="what-is-an-ontology"></a>Ontology nedir? 

Bir Azure dijital TWINS çözümünün sözlüğü, ortamınızda var olan varlık türlerini açıklayan [modeller](concepts-models.md)kullanılarak tanımlanır.

Bazen çözümünüz belirli bir sektöre bağlı olduğunda, kendi modelinizi sıfırdan yazmak yerine zaten var olan sektör için bir dizi modelle başlamak daha kolay ve daha etkili olabilir. Önceden var olan bu model kümelerine **ontoloaktive** adı verilir. 

Genel olarak, bir ontology, bir yapı yapısı, IoT sistemi, akıllı şehir, enerji Kılavuzu, Web içeriği vb. gibi belirli bir etki alanı için bir modeller kümesidir. Ontolomen, genellikle Bilgi Bankası grafikleri için şema olarak kullanılır, ancak şunları yapabilir:
* Yazılım bileşenleri, belgeler, sorgu kitaplıkları vb. uyumlama
* Kavramsal modelleme ve sistem geliştirmede daha az yatırım
* Anlamsal düzeyde daha kolay veri birlikte çalışabilirliği
* Sıfırdan başlamak yerine en iyi yöntem yeniden kullanım veya "tekerleği yeniden oluşturma"

Bu makalede, Azure dijital TWINS modelleriniz için nasıl ve nasıl kullanılacağı ve bunlara yönelik hangi onlarda ve araçların bugün kullanılabildiği açıklanmaktadır.

## <a name="using-ontologies-for-azure-digital-twins"></a>Azure dijital TWINS için ontolobir kullanma

Ontolo, Digital ikizi çözümleri için harika bir başlangıç noktası sağlar. Bir dijital ikizi grafiğini tasarlama, oluşturma ve ayrıştırma için varlıklar arasında etki alanına özgü model ve ilişkilerin bir kümesini kapsayırlar. Ontolo, Solution Developers 'ın kanıtlanmış bir başlangıç noktasından dijital bir TWINS çözümüne başlamasını ve iş sorunlarını çözmeye odaklanmasını sağlar. Microsoft tarafından sunulan ontolode kolayca genişletilebilir olacak şekilde tasarlanmıştır. bu sayede bunları çözümünüz için özelleştirebilmenizi sağlayabilirsiniz. 

Ayrıca, çözümlerinizde bu ontoloiler 'in kullanılması, farklı iş ortakları ve satıcılar arasında daha sorunsuz tümleştirme için ayarlanabilir, çünkü ontoloiler çözümler genelinde ortak bir sözlük sağlayabilir.

Azure dijital TWINS 'deki modeller [dijital TWINS tanım dili (dtdl)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)içinde gösterildiği Için, Azure Digital TWINS ile kullanım için ontolo, dtdl içinde de yazılmıştır. 

## <a name="strategies-for-integrating-ontologies"></a>Ontololezoni tümleştirme stratejileri

Bir DTDL ile sektör standardı ontoloon 'ı tümleştirmek için üç olası strateji vardır. Gereksinimlerinize bağlı olarak en iyi şekilde uygun olanı seçebilirsiniz:

| Strateji | Description | Kaynaklar |
| --- | --- | --- |
| **Benimseme** | Çözümünüzü, yaygın olarak benimsenen endüstri standartlarına göre geliştirilen açık kaynaklı bir DTDL ontology ile başlatabilirsiniz. Bu model kümelerini kullanıma hazır olarak kullanabilir ya da özelleştirilmiş bir çözüm için kendi eklemelerinizi genişletebilirsiniz. | [*Kavramlar: &nbsp; &nbsp; Endüstri &nbsp; standardı ontoloon benimseme*](concepts-ontologies-adopt.md)<br><br>[*Kavramlar: &nbsp; &nbsp; ontololojileri genişletme*](concepts-ontologies-extend.md) |
| **Melidir** | Zaten başka bir standart biçimde temsil edilen mevcut modelleriniz varsa, bunları Azure dijital TWINS ile kullanmak için DTDL 'e dönüştürebilirsiniz. | [*Kavramlar: &nbsp; &nbsp; ontolo, dönüştürme*](concepts-ontologies-convert.md)<br><br>[*Kavramlar: &nbsp; &nbsp; ontololojileri genişletme*](concepts-ontologies-extend.md) |
| **Yazma** | Kendi özel DTDL modellerinizi her zaman sıfırdan geliştirebilirsiniz. | [*Kavramlar: DTDL modelleri*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Model geliştirme yolunda ontology stratejilerini kullanma

Bir ontology 'i Azure dijital TWINS 'e tümleştirmek için hangi stratejiyi tercih ettiğinize bakılmaksızın, ontology 'i DTDL modelleri olarak oluşturma ve karşıya yükleme işlemi boyunca size rehberlik etmek için aşağıdaki yolun tamamını izleyebilirsiniz.

1. [Azure dijital TWINS 'de Dtdl modellemesini](concepts-models.md)inceleyerek ve anlayarak başlayın.
1. Yukarıdaki ontology tümleştirme stratejinizle devam edin: ontology 'nize göre modellerinizi [**benimseyin**](concepts-ontologies-adopt.md), [**dönüştürün**](concepts-ontologies-convert.md)veya [**Yazar**](concepts-models.md) .
    1. Gerekirse, ontology 'nizi gereksinimlerinize göre özelleştirmek için [genişletin](concepts-ontologies-extend.md) .
1. DTDL belgelerinin [çalıştığını doğrulamak için modellerinizi doğrulayın](how-to-parse-models.md) .
1. Tamamlanmış modellerinizi Azure dijital TWINS 'e, [API 'leri](how-to-manage-model.md#upload-models) veya [Azure dijital TWINS modeli yükleyicisi](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader)gibi bir örneği kullanarak karşıya yükleyin.

Bundan sonra, modellerinizi Azure dijital TWINS örneğiniz üzerinde kullanabilmeniz gerekir. 

Bunları [Azure Digital TWINS Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) veya [Azure Digital TWINS model görselleştiricisi](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)gibi örneklerle görselleştirebilir veya [dijital TWINS](concepts-twins-graph.md)oluşturmak için bunları kullanmaya geçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Ontolo, dönüştürme ve yazma stratejileri hakkında daha fazla bilgi edinin:
* [*Kavramlar: sektör standardı ontoloon benimseme*](concepts-ontologies-adopt.md)
* [*Kavramlar: ontolo, dönüştürme*](concepts-ontologies-convert.md)
* [*Nasıl yapılır: DTDL modellerini yönetme*](how-to-manage-model.md)

Ya da modellerin dijital TWINS oluşturmak için nasıl kullanıldığı hakkında bilgi edinin: [*Kavramlar: dijital TWINS ve ikizi grafiği*](concepts-twins-graph.md).