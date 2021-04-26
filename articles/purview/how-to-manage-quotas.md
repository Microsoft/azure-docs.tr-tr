---
title: Kaynakları ve kotaları yönetme
titleSuffix: Azure Purview
description: Azure purview için kaynaklardaki kotalar ve sınırlamalar hakkında bilgi edinin ve kota artışlarının nasıl isteneceğini öğrenin.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96938843"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Azure purview ile kaynaklar için kotaları yönetme ve artırma
 
Azure purview, veri kullanıcıları tarafından kullanılmak üzere bir bulut hizmetidir. Hem bulut hem de şirket içi ortamları kapsayan veri idare genelinde veri yönetimini merkezi olarak yönetmek için Azure purview kullanın. Hizmet, iş analistlerinin anlamlı iş terimlerini kullanarak ilgili verileri aramasını sağlar. Sınırlandırmaları aboneliğinizin en büyük sınırına yükseltmek için desteğe başvurun.
 
## <a name="azure-purview-limits"></a>Azure Purview'un sınırları
 
|**Kaynak**|  **Varsayılan sınır**  |**Maksimum sınır**|
|---|---|---|
|Her kiracı için (tüm abonelikler Birleşik) her bölge için hesap görüntüleme|3|Destek birimine başvurma|
|tarama için kullanılabilir sanal çekirdekler, hesap başına *|160|160|
|Verilen bir noktada hesap başına eşzamanlı taramalar. Sınır, taranan veri kaynaklarının türüne göre belirlenir *|5 | 10 |
|Taramanın çalışacağı en uzun süre|7 gün|7 gün|
|API çağrıları, hesap başına|4 Kapasite birimi Platform boyutu için 10 milyon API/ay. <br>16 Kapasite birimi Platform boyutu için 40D API/ay |4 Kapasite birimi Platform boyutu için 10 milyon API/ay. <br>16 Kapasite birimi Platform boyutu için 40D API/ay|
|Depolama, hesap başına|4 Kapasite birimi Platform boyutu için 10 GB. <br>16 Kapasite birimi Platform boyutu için 40 GB |4 Kapasite birimi Platform boyutu için 10 GB. <br> 16 Kapasite birimi Platform boyutu için 40 GB |
|Hesap başına varlık boyutu|100 milyon fiziksel varlık |Destek birimine başvurma|
|Bir katalogdaki varlıkların en büyük boyutu|2 MB|2 MB|
|Bir varlık adının ve sınıflandırma adının maksimum uzunluğu|4 KB|4 KB|
|Varlık özelliği adı ve değerinin maksimum uzunluğu|32 KB|32 KB|
|Sınıflandırma özniteliği adı ve değerinin maksimum uzunluğu|32 KB|32 KB|
|Hesap başına en fazla sözlük terimi sayısı|100K|100K|
 
* Şirket içinde barındırılan tümleştirme çalışma zamanı senaryoları yukarıdaki tabloda tanımlanan sınırlara ait kapsamın dışındadır. 
 
## <a name="next-steps"></a>Sonraki adımlar
 
> [!div class="nextstepaction"]
>[Öğretici: Azure purview ile verileri tarama](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Öğretici: giriş sayfasında gezinme ve varlık arama](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Öğretici: varlıklara gözatıp kökenini görüntüleyin](tutorial-browse-and-view-lineage.md)
