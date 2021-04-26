---
title: Azure portal kullanarak Cloud Services Izlemeyi etkinleştirme (genişletilmiş destek)
description: Azure portal kullanarak Cloud Services (genişletilmiş destek) örnekleri için izlemeyi etkinleştir
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744857"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Azure portal kullanarak Cloud Services için izlemeyi etkinleştirin (genişletilmiş destek)

Bu makalede, mevcut bulut hizmeti (genişletilmiş destek) dağıtımlarında uyarıların nasıl etkinleştirileceği açıklanır. 

## <a name="add-monitoring-rules"></a>İzleme kuralları ekleme
1. [Azure portalında](https://portal.azure.com) oturum açın. 
2. Uyarılarını etkinleştirmek istediğiniz bulut hizmeti (genişletilmiş destek) dağıtımını seçin. 
3. **Uyarılar** dikey penceresini seçin. 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="Görüntüde Azure portal uyarılar sekmesinin seçilmesi gösterilmektedir.":::

4. **Yeni uyarı** simgesini seçin.
     :::image type="content" source="media/enable-alerts-2.png" alt-text="Görüntüde yeni uyarı ekle seçeneğinin seçilmesi gösterilmektedir.":::

5. İzlemek istediğiniz ölçümleri temel alarak istenen koşulları ve gerekli eylemleri girin. Kuralları bireysel ölçümlere veya etkinlik günlüğüne göre tanımlayabilirsiniz. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="Görüntüde koşulların nereye ekleneceğini gösterir.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="Görüntü sinyal mantığını yapılandırmayı gösterir.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="Görüntü eylem grubu mantığını yapılandırmayı gösterir.":::

6. Uyarıları ayarlamayı bitirdiğinizde, değişiklikleri kaydedin ve yapılandırılan ölçümlere göre **Uyarı** dikey penceresinin zaman içinde doldurulmasını görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar 
- Cloud Services için [sık sorulan soruları](faq.md) gözden geçirin (genişletilmiş destek).
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [şablon](deploy-template.md) veya [Visual Studio](deploy-visual-studio.md)kullanarak bir bulut hizmeti (genişletilmiş destek) dağıtın.