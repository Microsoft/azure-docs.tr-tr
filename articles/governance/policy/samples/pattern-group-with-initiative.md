---
title: 'Model: girişimlerle Grup İlkesi tanımları'
description: Bu Azure Ilke düzeninde ilke tanımlarının bir girişimde nasıl gruplandırılmasına ilişkin bir örnek verilmiştir.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: 7bbb2efdd27ead942fa0ef48f7785eec8bce9378
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092866"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure Ilke deseninin: Grup İlkesi tanımları

Girişim bir ilke tanımları grubudur. İlgili ilke tanımlarını tek bir nesne olarak gruplandırarak, birden çok ataması olacak tek bir atama oluşturabilirsiniz.

## <a name="sample-initiative-definition"></a>Örnek girişim tanımı

Bu girişim, her biri **TagName** ve **tagvalue** parametrelerini alan iki ilke tanımını dağıtır. Girişimin kendisi iki parametreye sahiptir: **Costcentervalue** ve **productnamevalue**.
Bu girişim parametreleri her biri gruplanmış ilke tanımlarının her biri için sağlanır. Bu tasarım, gerektiğinde bunları uygulamak üzere oluşturulan atamaların sayısını sınırlayarak mevcut ilke tanımlarının yeniden kullanımını en üst düzeye çıkarır.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Açıklama

#### <a name="initiative-parameters"></a>Girişim parametreleri

Bir girişim, gruplanmış ilke tanımlarına geçirilen kendi parametrelerini tanımlayabilir.
Bu örnekte, hem **Costcentervalue** hem de **productnamevalue** , girişim parametreleri olarak tanımlanmıştır. Değer, girişim atandığında sağlanır.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>İlke tanımlarını içerir

İlke tanımı parametreleri kabul ediyorsa, dahil edilen her ilke tanımının **Policydefinitionıd** ve **Parameters** dizisi sağlaması gerekir. Aşağıdaki kod parçacığında, dahil edilen ilke tanımı iki parametre alır: **TagName** ve **tagvalue**. **TagName** bir değişmez değer ile tanımlanır, ancak **tagvalue** , girişim tarafından tanımlanan **costcentervalue** parametresini kullanır. Değerlerin bu geçişi yeniden kullanımını geliştirir.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Sonraki adımlar

- Diğer [desenleri ve yerleşik tanımları](./index.md)gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.