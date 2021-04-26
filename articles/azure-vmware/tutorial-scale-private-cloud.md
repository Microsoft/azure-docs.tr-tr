---
title: Öğretici-özel bulutu ölçeklendirme
description: Bu öğreticide, Azure VMware çözümü özel bulutunu ölçeklendirmek için Azure portal kullanırsınız.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 2129a3f5d04311883369b7b708689a13f07ec118
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463638"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Öğretici: Azure VMware çözümünü özel bulutu ölçeklendirme

Azure VMware Çözüm özel bulut deneyiminizden en iyi şekilde yararlanmak için kümeleri ve Konakları, planlı iş yükleri için gerekenleri yansıtacak şekilde ölçeklendirin. Özel bir buluttaki kümeleri ve Konakları, uygulama iş yükünüz için gereken şekilde ölçeklendirebilirsiniz. Belirli hizmetler için performans ve kullanılabilirlik sınırlamaları, büyük/küçük harfe göre değinilmesi gerekir. Küme ve konak sınırları, [özel bulut kavramı](concepts-private-clouds-clusters.md) makalesinde sunulmaktadır.

Bu öğreticide, Azure portal için kullanacaksınız:

> [!div class="checklist"]
> * Mevcut bir özel buluta küme ekleme
> * Mevcut bir kümeye konaklar ekleme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için mevcut bir özel buluta ihtiyacınız olacaktır. Özel bir bulut oluşturmadıysanız, bir [özel bulut Oluşturma öğreticisini](tutorial-create-private-cloud.md) kullanarak bir tane oluşturun. 

## <a name="add-a-new-cluster"></a>Yeni küme ekleme

1. Mevcut bir özel bulutun Genel Bakış sayfasında, **Yönet** altında, **özel bulutu Ölçeklendir**' i seçin. Ardından **+ küme Ekle**' yi seçin.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Küme Ekle ' yi seçin" border="true":::

1. **Küme Ekle** sayfasında, ana bilgisayar sayısını seçmek için kaydırıcıyı kullanın. **Kaydet**’i seçin.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Küme Ekle sayfasında, ana bilgisayar sayısını seçmek için kaydırıcıyı kullanın. Kaydet ' i seçin." border="true":::

   Yeni kümenin dağıtımı başlar.

## <a name="scale-a-cluster"></a>Kümeyi ölçeklendirme 

1. Mevcut bir özel bulutun Genel Bakış sayfasında, **özel bulutu Ölçeklendir** ' i seçin ve kümeyi düzenlemek için kurşun kalem simgesini seçin.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Genel görünümde ölçek özel bulutu seçin" border="true":::

1. **Kümeyi Düzenle** sayfasında, ana bilgisayar sayısını seçmek için kaydırıcıyı kullanın. **Kaydet**’i seçin.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Kümeyi Düzenle sayfasında, ana bilgisayar sayısını seçmek için kaydırıcıyı kullanın. Kaydet ' i seçin." border="true":::

   Ana bilgisayarların kümeye eklenmesi başlar.

## <a name="next-steps"></a>Sonraki adımlar

Başka bir Azure VMware çözümü özel buluta ihtiyacınız varsa, aynı ağ önkoşullarını, kümeyi ve konak sınırlarını izleyerek [başka bir özel bulut oluşturun](tutorial-create-private-cloud.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
