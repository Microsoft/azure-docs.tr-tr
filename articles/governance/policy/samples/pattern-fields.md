---
title: 'Model: bir ilke tanımındaki alan özellikleri'
description: Bu Azure Ilke düzeninde, alan özelliklerinin bir ilke tanımında nasıl kullanılacağına ilişkin bir örnek verilmiştir.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: b0b998084c9acf664d4e3e8dd9483af1ee986677
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093342"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure Ilke deseninin: alan özellikleri

[Alan](../concepts/definition-structure.md#fields) işleci belirtilen bir [koşul](../concepts/definition-structure.md#conditions)için belirtilen özelliği veya [diğer adı](../concepts/definition-structure.md#aliases) bir sağlanan değere değerlendirir.

## <a name="sample-policy-definition"></a>Örnek ilke tanımı

Bu ilke tanımı, kuruluşunuzun coğrafi konum gereksinimlerini karşılayan izin verilen bölgeleri tanımlamanızı sağlar. İzin verilen kaynaklar, **Lıfallodilimlocations** (_dizi_) parametresinde tanımlanmıştır. Tanımıyla eşleşen kaynaklar [reddedilir](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Açıklama

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

**Alan** işleci, **allof** [mantıksal işleci](../concepts/definition-structure.md#logical-operators) içinde üç kez kullanılır.

- İlk kullanım, `location` özelliği **notın** koşulu Ile **Lıfallodilimlocations** parametresine göre değerlendirir. **Notın** , bir _dizi_ beklediği ve parametresi bir _dizi_ olduğu için çalışıyor. `location`Oluşturulan veya güncellenen kaynak onaylanan listede yoksa, bu öğe true olarak değerlendirilir.
- İkinci kullanım özelliği de değerlendirir `location` , ancak kaynağın _Global_ olup olmadığını görmek için **Not al** koşulunu kullanır. `location`Oluşturulan veya güncellenen kaynağın _genel_ olmaması durumunda bu öğe true olarak değerlendirilir.
- Son kullanım, özelliği değerlendirir `type` ve kaynak türünün _Microsoft. AzureActiveDirectory/b2cDirectories_ olmadığını doğrulamak Için **notquals** koşulunu kullanır. Değilse, bu öğe true olarak değerlendirilir.

**Allof** Logical işlecinin tüm üç koşul deyimleri doğru olarak değerlendirilüyorsa, kaynak oluşturma veya güncelleştirme Azure ilkesi tarafından engellenir.

## <a name="next-steps"></a>Sonraki adımlar

- Diğer [desenleri ve yerleşik tanımları](./index.md)gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.