---
title: Eşleme veri akışında birden çok dal
description: Birden çok Dalla eşleme veri akışında veri akışlarını çoğaltma
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/16/2021
ms.openlocfilehash: f9f2bf2e2204e6b74bb8a31ac856dbe276a6e983
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588760"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Eşleme veri akışı 'nda yeni bir dal oluşturma

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aynı veri akışına karşı birden çok işlem ve dönüşüm kümesi yapmak için yeni bir dal ekleyin. Yeni bir dal eklemek, birden çok havuz için aynı kaynağı kullanmak veya bir arada verilerin birlikte katılmasını sağlamak istediğinizde faydalıdır.

Dönüştürme listesinden diğer dönüştürmelere benzer yeni bir dal eklenebilir. **Yeni dal** yalnızca, dala çalıştığınız dönüşümden sonra var olan bir dönüşüm olduğunda bir eylem olarak kullanılabilir olacaktır.

![Ekran görüntüsü birden çok giriş/çıkış menüsünde yeni dal seçeneğini gösterir.](media/data-flow/new-branch2.png "Yeni dal ekleme")

Aşağıdaki örnekte veri akışı, TAXI seyahat verilerini okuyor. Hem gün hem de satıcı tarafından toplanan çıkış gereklidir. Aynı kaynaktan okunan iki ayrı veri akışı oluşturmak yerine yeni bir dal eklenebilir. Bu şekilde, her iki toplamaları de aynı veri akışının bir parçası olarak yürütülebilir. 

![Ekran görüntüsü, kaynaktan iki Dalla veri akışını gösterir.](media/data-flow/new-branch.png "Yeni dal ekleme")

> [!NOTE]
> Grafiğinize dönüşümler eklemek için artı (+) simgesine tıkladığınızda yalnızca sonraki dönüştürme blokları olduğunda yeni dal seçeneğini görürsünüz. Bunun nedeni, yeni dalın mevcut akışa bir başvuru oluşturduğuna ve üzerinde çalışması için daha fazla yukarı akış işlemenin yapılmasını gerektirir. Yeni dal seçeneğini görmüyorsanız, önce bir türetilmiş sütun veya diğer dönüşüm ekleyin, sonra önceki bloğa geri dönün ve yeni dalı bir seçenek olarak görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

Dallandırdıktan sonra [veri akışı dönüşümlerini](data-flow-transformation-overview.md) kullanmak isteyebilirsiniz
