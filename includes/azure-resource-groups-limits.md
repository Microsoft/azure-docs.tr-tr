---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 1190798b234f9c73e02fda41c03ffa296246be63
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71975352"
---
| Kaynak | Varsayılan sınır | Maksimum sınır |
| --- | --- | --- |
| Kaynak [grubu](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)başına kaynaklar, kaynak türü başına |800 |Bazı kaynak türleri 800 sınırını aşabilir. [Kaynak grubu başına 800 örnek için sınırlı kaynaklara](../articles/azure-resource-manager/resources-without-rg-limit.md)bakın. |
| Dağıtım geçmişinde kaynak grubu başına dağıtımlar |800<sup>1</sup> |800 |
| Dağıtım başına kaynaklar |800 |800 |
| Benzersiz kapsam başına yönetim kilitleri |20 |20 |
| Kaynak veya kaynak grubu başına etiket sayısı |50 |50 |
| Etiket anahtarı uzunluğu |512 |512 |
| Etiket değeri uzunluğu |256 |256 |

<sup>1</sup> Kaynak grubu başına 800 dağıtım sınırına ulaşırsanız, artık gerekli olmayan geçmişten dağıtımları silin. Dağıtım geçmişinden bir girişin silinmesi dağıtılan kaynakları etkilemez. Daha fazla bilgi için bkz. [dağıtım sayısı 800 ' i aştığında hata çözümleme](../articles/azure-resource-manager/deployment-quota-exceeded.md).

#### <a name="template-limits"></a>Şablon sınırları

| Değer | Varsayılan sınır | Maksimum sınır |
| --- | --- | --- |
| Parametrelere |256 |256 |
| Değişkenlerinin |256 |256 |
| Kaynaklar (kopya sayısı dahil) |800 |800 |
| Çıkışı |64 |64 |
| Şablon ifadesi |24.576 karakter |24.576 karakter |
| Aktarılmış şablonlarda kaynaklar |200 |200 | 
| Şablon boyutu |4 MB |4 MB |
| Parametre dosyası boyutu |64 KB |64 KB |

İç içe geçmiş bir şablon kullanarak bazı şablon sınırlarını aşabilirsiniz. Daha fazla bilgi için bkz. [Azure kaynaklarını dağıtırken bağlı şablonları kullanma](../articles/azure-resource-manager/resource-group-linked-templates.md). Parametre, değişken veya çıkış sayısını azaltmak için, birkaç değeri bir nesne içinde birleştirebilirsiniz. Daha fazla bilgi için bkz. [nesneler parametreler olarak](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
