---
title: Mevcut bir Azure veri paylaşımında veri kümeleri ekleme
description: Azure veri paylaşımında var olan bir veri paylaşımında veri kümelerini nasıl ekleyeceğinizi ve aynı alıcılara nasıl paylaşacağınızı öğrenin.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 3bfa911921e9bacde2649ee8c4f0d4bc31b56f54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910551"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Azure veri paylaşımında var olan bir paylaşıma veri kümeleri ekleme

Bu makalede, Azure veri paylaşımının kullanıldığı önceden var olan bir veri paylaşımında veri kümelerinin nasıl ekleneceği açıklanmaktadır. Bu, yeni bir paylaşma oluşturmak zorunda kalmadan aynı alıcılara daha fazla veri paylaşmanızı sağlar.

Bir paylaşma oluştururken veri kümeleri ekleme hakkında daha fazla bilgi için bkz. [Share Data](share-your-data.md) öğreticisi.

## <a name="navigate-to-a-sent-data-share"></a>Gönderilen veri paylaşımında gezin

Azure veri paylaşımında, gönderilen paylaşımınıza gidin ve **veri kümeleri** sekmesini seçin. Daha fazla veri kümesi eklemek için **+ veri kümesi Ekle** düğmesine tıklayın.

![Ekran görüntüsü seçili veri kümesi Ekle ' ye gösterilir.](./media/how-to/how-to-add-datasets/add-datasets.png)

Sağdaki panelde, eklemek istediğiniz veri kümesi türünü seçin ve ardından **İleri**' ye tıklayın. Eklemek istediğiniz verilerin aboneliğini ve kaynak grubunu seçin. Açılan okları kullanarak eklenecek verilerin yanındaki kutuyu bulup işaretleyin.

![Ekran görüntüsü, veri seçebileceğiniz BLOB depolama alanı Ekle bölmesini gösterir.](./media/how-to/how-to-add-datasets/add-datasets-side.png)

**Veri kümeleri Ekle**' ye tıkladığınızda veri kümeleri paylaşımınıza eklenecektir. Note: yeni veri kümelerini görmesini sağlamak için Tüketicileriniz tarafından bir anlık görüntü tetiklenmesi gerekir. Anlık görüntü ayarları yapılandırılmışsa, müşteriler bir sonraki zamanlanmış anlık görüntü tamamlandıktan sonra yeni veri kümelerini görür. Anlık görüntü ayarları yapılandırıldığında, Tüketici güncelleştirmeleri almak için verilerin tam veya artımlı bir kopyasını el ile tetiklemelidir. Anlık görüntüler hakkında daha fazla bilgi için bkz. [anlık görüntüler](terminology.md).

## <a name="next-steps"></a>Sonraki adımlar
[Mevcut bir veri paylaşımında alıcı ekleme](how-to-add-recipients.md)hakkında daha fazla bilgi edinin.