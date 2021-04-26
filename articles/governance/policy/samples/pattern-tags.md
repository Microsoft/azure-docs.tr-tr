---
title: 'Model: bir ilke tanımında etiketleri kullanma'
description: Bu Azure Ilke düzeninde, parametreli etiketlerin nasıl ekleneceği veya bir ilke tanımındaki bir kaynak grubundaki etiketlerin nasıl devraldığı gösterilmektedir.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: c748eb9b8ea795f9725082ec0aa0b8065ada8c65
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093409"
---
# <a name="azure-policy-pattern-tags"></a>Azure Ilke stili: Etiketler

[Etiketler](../../..//azure-resource-manager/management/tag-resources.md) , Azure kaynaklarınızı yönetme, düzenleme ve yönetmenin önemli bir parçasıdır. Azure Ilkesi, yeni ve mevcut kaynaklardaki etiketleri [değiştirme](../concepts/effects.md#modify) efekti ve [Düzeltme görevleriyle](../how-to/remediate-resources.md)birlikte yapılandırma olanağı sağlar.

## <a name="sample-1-parameterize-tags"></a>Örnek 1: etiketleri Parametreleştirme

Bu ilke tanımı, ilke atamasının kaynak grupları üzerinde ne kadar arama yaptığını ayarlamak için iki parametre, **TagName** ve **tagvalue** kullanır. Bu biçim, ilke tanımının herhangi bir sayıda etiket adı ve etiket değeri bileşimi için kullanılmasına izin verir, ancak yalnızca tek bir ilke tanımını korur.

> [!NOTE]
> Bu ilke tanımı deseninin [#1 örüntekine](./pattern-parameters.md#sample-1-string-parameters)benzemekle aynı olsa da, bu örnekte **mod** _All_ ve targets kaynak grupları kullanılmaktadır.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>Örnek 1: Açıklama

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

Bu örnekte, bir kaynak grubunu hedeflediğinden bu yana **mod** _Tümü_ olarak ayarlanır. Çoğu durumda, etiketlerle çalışırken **mod** _dizinli_ olarak ayarlanmalıdır. Daha fazla bilgi için bkz. [modlar](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

İlke tanımının bu bölümünde `concat` parametreli **TagName** parametresini ve `tags['name']` söyleme biçimini, Tag **değeri** parametresi için bu etiketi  değerlendirmek üzere birleştirir.
**Not Dörtleleri** kullanıldığında, **Etiketler \[ \] TagName** , **tagvalue** değerine eşit değilse, **değişiklik** etkisi tetiklenir.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

Burada, parametreli etiket değerlerini kullanmak için aynı biçim **Addorreplace** işlemi tarafından, hesaplanan kaynak grubundaki istenen değere etiketi oluşturmak veya güncelleştirmek için kullanılır.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>Örnek 2: etiket değerini kaynak grubundan devralma

Bu ilke tanımı, üst kaynak grubundan hangi etiketin değerini devralmasını belirleyen **TagName** parametresini kullanır.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>Örnek 2: Açıklama

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

Bu örnekte, bir kaynak grubundaki değeri alan bir kaynak grubunu veya aboneliği hedeflemediğinden **mod** _dizinli_ olarak ayarlanır. Daha fazla bilgi için bkz. [modlar](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

**Policyrule. If** , `concat` **TagName**'in değerini değerlendirmek için [Örnek #1](#sample-1-parameterize-tags) gibi kullanılıyorsa, ancak `resourceGroup()` bunu üst kaynak grubundaki aynı etiket değeriyle karşılaştırmak için işlevini kullanır. Buradaki ikinci yan tümce, kaynak grubundaki etiketin bir değere sahip olduğunu ve null olmadığını kontrol eder.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

Burada, kaynaktaki **TagName** etiketine atanmakta olan değer, `resourceGroup()` üst kaynak grubundan değeri almak için işlevini de kullanır. Bu şekilde, üst kaynak gruplarından Etiketler kalýtýmla aktarabilirsiniz. Kaynağı zaten oluşturduysanız ancak etiketi eklemediğiniz takdirde, aynı ilke tanımı ve bir [Düzeltme görevi](../how-to/remediate-resources.md) mevcut kaynakları güncelleştirebilir.

## <a name="next-steps"></a>Sonraki adımlar

- Diğer [desenleri ve yerleşik tanımları](./index.md)gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.