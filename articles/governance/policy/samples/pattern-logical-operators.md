---
title: 'Model: bir ilke tanımındaki mantıksal işleçler'
description: Bu Azure Ilke modelinde, mantıksal işleçlerin bir ilke tanımında nasıl kullanılacağına ilişkin örnekler verilmektedir.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: feb9e50b0c73c19027b747cf0f95fa1cb6fbd47c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093359"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure Ilke stili: mantıksal işleçler

Bir ilke tanımı, birkaç koşullu deyim içerebilir. Her deyimin doğru olması veya yalnızca bazılarının doğru olması gerekir. Bu ihtiyaçları desteklemek için, dilin [mantıksal işleçleri](../concepts/definition-structure.md#logical-operators) **değildir**, **allof** ve **anyfor**. Bunlar isteğe bağlıdır ve karmaşık senaryolar oluşturmak için iç içe olabilir.

## <a name="sample-1-one-logical-operator"></a>Örnek 1: bir mantıksal işleç

Bu ilke tanımı, otomatik yük devretme ve birden fazla yazma konumu yapılandırılıp yapılandırılmadığını görmek için [Azure Cosmos DB](../../../cosmos-db/introduction.md) hesapları değerlendirir. Olmadığında [Denetim](../concepts/effects.md#audit) tetiklenir ve uyumlu olmayan kaynak oluşturulduğunda veya güncelleştirilirken bir günlük girişi oluşturur.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Örnek 1: Açıklama

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

**Policyrule. If** bloğu, üç koşulun de doğru olmasını sağlamak için tek bir **allof** kullanır.
Yalnızca bu koşulların tümü doğru olarak değerlendirilse **Denetim** efekti tetikler.

## <a name="sample-2-multiple-logical-operators"></a>Örnek 2: birden çok mantıksal işleçler

Bu ilke tanımı, bir adlandırma deseninin kaynaklarını değerlendirir. Bir kaynak eşleşmezse, [reddedilir](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Örnek 2: Açıklama

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Bu **Policyrule. If** bloğu tek bir **allof** de içeriyorsa, ancak her koşul mantıksal **olmayan** işleçle sarmalanır. **Not** Logical işlecinin içindeki koşul ilk olarak değerlendirilir ve sonra yan tümcesinin doğru mi yoksa yanlış mi olduğunu belirlememe sonucunu **vermez** . Her iki mantıksal işleç de true olarak **değerlendirilmiyorsa,** ilke efekti tetikler.

## <a name="sample-3-combining-logical-operators"></a>Örnek 3: mantıksal işleçleri birleştirme

Bu ilke tanımı, izlemenin etkin olup olmadığını ya da izlemenin başarılı bir durumda olup olmadığını görmek için [Azure hesaplarında yayı](/azure/developer/java/spring-framework) değerlendirir.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>Örnek 3: Açıklama

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

Bu **Policyrule. If** bloğu hem **allof** hem de **anyOf** mantıksal işleçleri içeriyorsa. Dahil edilen bir koşul doğru olduğu sürece, mantıksal işlecin **anydeğeri** true olarak değerlendirilir. _Türü_ , **allof** öğesinin çekirdeğünde olduğundan her zaman true olarak değerlendirilmelidir. _Türü_ ve **herhangi** bir koşuldaki koşullardan biri doğru ise, ilke efekti tetiklenir.

## <a name="next-steps"></a>Sonraki adımlar

- Diğer [desenleri ve yerleşik tanımları](./index.md)gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.