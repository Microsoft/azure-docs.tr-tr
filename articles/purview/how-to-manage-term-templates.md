---
title: İş sözlüğü için terim şablonlarını yönetme
description: Azure purview veri kataloğunda iş sözlüğü için terim şablonlarının nasıl yönetileceğini öğrenin.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555552"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>İş sözlüğü için terim şablonlarını yönetme

Azure purview, verilerinizi zenginleştirme için önemli bir terimler sözlüğü oluşturmanıza olanak sağlar. Purview Veri Kataloğu Sözlüğunuza eklenen her yeni terim, terime ait alanları belirleyen bir terim şablonunu temel alır. Bu makalede, bir terim şablonunun ve sözlük koşullarına ilişkilendirilebilen özel özniteliklerin nasıl oluşturulacağı açıklanır.

## <a name="manage-term-templates-and-custom-attributes"></a>Terim şablonlarını ve özel öznitelikleri yönetme

Terim şablonları kullanarak özel öznitelikler oluşturabilir, bunları birlikte gruplandırabilir ve terim oluştururken bir şablon uygulayabilirsiniz. Bir terim oluşturulduktan sonra, terimle ilişkili şablon değiştirilemez.

1. **Sözlük terimleri** sayfasında, **terim şablonlarını yönet**' i seçin.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="Terim şablonlarını yönet > sözlük koşullarının ekran görüntüsü.":::

2. Sayfada hem sistem hem de özel öznitelikler gösterilmektedir. Terim şablonları oluşturmak veya düzenlemek için **özel** sekmesini seçin.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="Terim şablonlarını yönetme > sözlük koşullarının ekran görüntüsü.":::

3. **+ Yeni terim şablonu** ' nu seçin ve bir şablon adı ve açıklama girin.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="Yeni terim şablonları > terim şablonlarını yönetme > sözlük koşullarının ekran görüntüsü":::

4. Terim şablonu için yeni bir özel öznitelik oluşturmak için **+ Yeni özniteliğini** seçin. Bir öznitelik adı girin, açıklama. Özel öznitelik adı, bir terim şablonu içinde benzersiz olmalıdır, ancak aynı ad, şablonlar genelinde yeniden kullanılabilir.

   Seçenekler **metin**, **tek seçim**, **Çoklu seçim** veya  **Tarih** listesinden alan türünü seçin. Metin alanı türleri için de varsayılan bir değer dizesi sağlayabilirsiniz.  Öznitelik **gerekli** olarak da işaretlenebilir.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="Sözlük koşullarının yeni öznitelik sayfası > ekran görüntüsü.":::

5. Tüm özel öznitelikler oluşturulduktan sonra, özel özniteliklerin sırasını düzenlemek için **Önizleme** ' yi seçin. Özel öznitelikleri sürükleyip istediğiniz sırada bırakabilirsiniz.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="> önizleme terimi şablonunun Sözlük terimleri ekran görüntüsü.":::

6. Tüm özel öznitelikler tanımlandıktan sonra, özel özniteliklere sahip bir terim şablonu oluşturmak için **Oluştur** ' u seçin.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="Yeni terim şablonu > sözlük koşullarının ekran görüntüsü-oluştur düğmesi.":::

7. Var olan özel öznitelikler, **zaman aşımına uğradı olarak işaretle** olarak işaretlenmiş olarak işaretlenebilir. Süre dolduktan sonra öznitelik yeniden etkinleştirilemez. Kullanım dışı olan öznitelik, var olan terimler için gri olacak. Bu terim şablonuyla oluşturulan gelecek yeni terimler artık süresi doldu olarak işaretlenmiş özniteliği göstermez.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="Sözlük koşullarının ekran görüntüsü > zaman aşımına uğradı şekilde işaretlemek için özniteliği düzenleyin.":::

## <a name="next-steps"></a>Sonraki adımlar

Öğreticiyi izleyin: daha fazla bilgi edinmek için [Sözlük terimleri oluşturun ve içeri aktarın](tutorial-import-create-glossary-terms.md) .
