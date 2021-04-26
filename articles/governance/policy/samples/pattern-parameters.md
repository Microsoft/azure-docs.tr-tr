---
title: 'Model: bir ilke tanımındaki parametreler'
description: Bu Azure Ilke düzeni bir ilke tanımında dize ve dizi parametrelerinin nasıl kullanılacağına ve efektin nasıl parametreleştirilemez hakkında bir örnek sağlar.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: b742aaaf950e2b5670edbaa1f0134da144e675b6
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092781"
---
# <a name="azure-policy-pattern-parameters"></a>Azure Ilke stili: parametreler

Bir ilke tanımı, [parametreleri](../concepts/definition-structure.md#parameters)kullanarak gereken ilke tanımlarının sayısını azaltmak için dinamik hale getirilebilir. Parametresi, ilke ataması sırasında tanımlanmıştır. Parametreler, parametreyi ve nasıl kullanıldığını tanımlayan önceden tanımlı özellikler kümesine sahiptir.

## <a name="sample-1-string-parameters"></a>Örnek 1: dize parametreleri

Bu ilke tanımı, ilke atamasının kaynaklar üzerinde ne olduğunu belirlemek için iki parametre, **TagName** ve **tagvalue** kullanır. Bu biçim, ilke tanımının herhangi bir sayıda etiket adı ve etiket değeri bileşimi için kullanılmasına izin verir, ancak yalnızca tek bir ilke tanımını korur.

> [!NOTE]
> **Modu** _Tümünü_ kullanan ve bir kaynak grubuyla birlikte çalıştırılan bir etiket örneği için bkz. [model: Etiketler-örnek #1](./pattern-tags.md#sample-1-parameterize-tags).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Örnek 1: Açıklama

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

İlke tanımının bu bölümünde, **TagName** parametresi bir _dize_ olarak tanımlanır ve kullanımı için bir açıklama sağlanır.

Daha sonra parametresi **Policyrule. If** bloğunda, ilkeyi dinamik hale getirmek için kullanılır. Burada, **TagName** değeri olan bir etiket olan değerlendirilen alanı tanımlamak için kullanılır.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Örnek 2: dizi parametreleri

Bu ilke tanımı, Express Route bağlantı hattı ' nın bant genişliği ayarını onaylanan değerlerden birine yapılandırdığından emin olmak için **listOfBandwidthinMbps** tek bir parametresini kullanır. Eşleşmiyorsa, kaynağa yönelik oluşturma veya güncelleştirme [reddedilir](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Örnek 2: Açıklama

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

İlke tanımının bu bölümünde, **listOfBandwidthinMbps** parametresi bir _dizi_ olarak tanımlanır ve kullanımı için bir açıklama sağlanır. Bir _dizi_ olarak eşleşmesi için birden çok değer vardır.

Daha sonra parametresi **Policyrule. If** bloğunda kullanılır. Bir _dizi_ parametresi olarak, bir _dizi_ 
 [koşulunun](../concepts/definition-structure.md#conditions)veya  **notın** 'in kullanılması gerekir.
Burada, tanımlı değerlerden biri olarak **ServiceProvider. bandwidthInMbps** diğer adı kullanılır.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="sample-3-parameterized-effect"></a>Örnek 3: parametreli efekt

İlke tanımlarını yeniden kullanılabilir yapmanın yaygın bir yolu, efektin kendisini parametreleştirilemez. Bu örnek tek bir parametre, **efekt** kullanır. Efektin parametreleştirilmesi, farklı etkilere sahip farklı kapsamlara aynı tanımın atanmasını olanaklı kılar.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json":::

### <a name="sample-3-explanation"></a>Örnek 3: Açıklama

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="11-25":::

İlke tanımının bu bölümünde, **efekt** parametresi _dize_ olarak tanımlanır. İlke tanımı, bir atamanın varsayılan değerini _Denetim_ için ayarlar ve diğer seçenekleri _devre dışı_ ve _Reddet_ olarak kısıtlar.

Daha sonra parametresi Policyrule içinde kullanılır **. daha sonra** _efekt_ için blok olur.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="38-40" highlight="2":::

## <a name="next-steps"></a>Sonraki adımlar

- Diğer [desenleri ve yerleşik tanımları](./index.md)gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.