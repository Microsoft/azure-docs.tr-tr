---
title: Özel form modeliniz-form tanıyıcısını eğitebilmeniz için tablo etiketleri kullanma
titleSuffix: Azure Cognitive Services
description: Denetimli tablo etiketi etiketlemeyi etkin bir şekilde kullanmayı öğrenin.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5422520c6a863876091d7820a5c07fa2413346c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467913"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Özel form modelinizi eğitebilmeniz için tablo etiketlerini kullanma

Bu makalede, özel form modelinizi tablo etiketleriyle (Etiketler) eğitme hakkında bilgi edineceksiniz. Bazı senaryolar yalnızca anahtar-değer çiftlerini hizalayarak daha karmaşık etiketleme gerektirir. Bu tür senaryolar, karmaşık hiyerarşik yapılara sahip formlardan bilgi ayıklamayı veya hizmet tarafından otomatik olarak algılanmayan ve ayıklanan öğelerle karşılaşmanızı içerir. Bu durumlarda, özel form modelinizi eğitebilmeniz için tablo etiketlerini kullanabilirsiniz.

## <a name="when-should-i-use-table-tags"></a>Tablo etiketlerini ne zaman kullanmalıyım?

Aşağıda tablo etiketlerinin kullanılması için bazı örnekler verilmiştir:

- Formlarınızda tablo olarak gösterilmesini istediğiniz veriler vardır ve tabloların yapısı anlamlı değildir. Örneğin, tablodaki her satır bir öğeyi temsil eder ve satırın her sütunu o öğenin belirli bir özelliğini temsil eder. Bu durumda, bir sütunun özellikleri gösterdiği ve bir satır her özellik hakkındaki bilgileri temsil eden bir tablo etiketi kullanabilirsiniz.
- Ayıklamak istediğiniz veriler, belirli form alanlarında sunulmayan ancak anlamsal olarak, verilerin iki boyutlu bir kılavuza sığamayacak olabilir. Örneğin, formunuz bir kişi listesine sahiptir ve bir ad, soyadı, soyadı ve e-posta adresi içerir. Bu bilgileri ayıklamak istiyorsunuz. Bu durumda, sütun olarak ilk adı, soyadı ve e-posta adresini içeren bir tablo etiketi kullanabilirsiniz ve her satır, listenizden bir kişi hakkındaki bilgilerle doldurulur.

> [!NOTE]
> Form tanıyıcı, tabloların etiketlenmesi veya etiketlenmediğini otomatik olarak belgenizdeki tüm tabloları bulur ve ayıklar. Bu nedenle, formunuzdaki her tabloyu bir tablo etiketiyle etiketlemenize gerek kalmaz ve tablo etiketlerinizin formunuzda bulunan çok sayıda tablonun yapısını çoğaltmak zorunda kalmaz. Form tanıyıcı tarafından otomatik olarak ayıklanan tablolar, JSON çıktısının pageResults bölümüne dahil edilir.

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>Form OCR test aracı (FOTT) ile tablo etiketi oluşturma
<!-- markdownlint-disable MD004 -->
* **Dinamik** veya **sabit boyutlu** bir tablo etiketi isteyip istemediğinizi belirleme. Satır sayısı belgeden belgeye farklılık gösterdiğinden, dinamik tablo etiketi kullanın. Satır sayısı belgeleriniz arasında tutarlı ise sabit boyutlu bir tablo etiketi kullanın.
* Tablo etiketinizdeki değer dinamik ise, her sütun için sütun adlarını ve veri türünü ve biçimini tanımlayın.
* Tablonuz sabit boyutlu ise, her etiket için sütun adı, satır adı, veri türü ve biçim tanımlayın.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Tablo etiketi yapılandırma":::

## <a name="label-your-table-tag-data"></a>Tablo etiket verilerinizi etiketleme

* Projenizde bir tablo etiketi varsa etiketleme panelini açabilir ve anahtar-değer alanlarını etiketleyerek etiketi doldurabilirsiniz.
:::image type="content" source="media/table-labeling.png" alt-text="Tablo etiketleriyle etiketle":::

## <a name="next-steps"></a>Sonraki adımlar

Özel form tanıyıcı modelinizi eğitme ve kullanmaya yönelik hızlı başlangıç hızımızı izleyin:

> [!div class="nextstepaction"]
> [Örnek etiketleme aracını kullanarak etiketlerle eğitme](quickstarts/label-tool.md)

## <a name="see-also"></a>Ayrıca bkz.

* [Form Tanıma nedir?](overview.md)
>
