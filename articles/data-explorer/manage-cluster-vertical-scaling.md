---
title: Değişiklik talebini karşılamak için Azure Veri Gezgini küme dikey ölçeklendirmeyi (ölçeği büyütme) yönetme
description: Bu makalede, değişen talebe göre bir Azure Veri Gezgini kümesini ölçeklendirmek ve ölçeklendirmek için adımlar açıklanmaktadır.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 80bbdf3a5d936719b06782cd78d56088b36cb21d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985474"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Değişiklik talebini karşılamak için Azure Veri Gezgini küme dikey ölçeklendirmeyi (ölçeği büyütme) yönetme

Bir kümeyi uygun şekilde boyutlandırmak, Azure Veri Gezgini performansı açısından önemlidir. Statik küme boyutu, ne kadar ideal bir deyişle, kullanımı veya kullanımı aşırı olabilir.

Bir kümedeki talep mutlak doğrulukla tahmin edilebileceği için daha iyi bir yaklaşım, değişen talebe sahip  KAPASITE ve CPU kaynaklarını ekleme ve kaldırma, bir kümeyi ölçeklendirmektir. 

Azure Veri Gezgini kümesinin ölçeklendirilmesi için iki iş akışı vardır:

* [Yatay ölçekleme](manage-cluster-horizontal-scaling.md), Ayrıca, ölçeklendirilmesi ve çıkışı da denir.
* Dikey ölçekleme, ölçeği artırma ve azaltma olarak da bilinir.

Bu makalede dikey ölçeklendirme iş akışı açıklanmaktadır:

## <a name="configure-vertical-scaling"></a>Dikey ölçeklendirmeyi yapılandırma

1. Azure portal Azure Veri Gezgini küme kaynağına gidin. **Ayarlar**altında **Ölçek yukarı**' yı seçin.

1. **Ölçeği artırma** penceresinde kümeniz Için kullanılabilir SKU 'ların bir listesini görürsünüz. Örneğin, aşağıdaki şekilde yalnızca dört SKU vardır.

    ![Ölçeği artırma](media/manage-cluster-vertical-scaling/scale-up.png)

    SKU 'Lar geçerli SKU olduklarından veya kümenin bulunduğu bölgede kullanılamadığından devre dışı bırakılır.

1. SKU 'nuzu değiştirmek için yeni bir SKU seçin ve **Seç**' e tıklayın.

> [!NOTE]
> * Dikey ölçeklendirme işlemi birkaç dakika sürebilir ve bu süre boyunca kümeniz askıya alınır. 
> * Ölçeği azaltma, kümenizin performansına zarar verebilir.
> * Fiyat, kümenin sanal makinelerinin ve Azure Veri Gezgini hizmet maliyetlerinin tahminidir. Diğer maliyetler dahil değildir. Tam fiyatlandırma bilgileri için tahmin ve Azure Veri Gezgini [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/data-explorer/) için bkz. Azure Veri Gezgini [Cost tahmin aracı](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) sayfası.

Artık Azure Veri Gezgini kümeniz için dikey ölçeklendirmeyi yapılandırdınız. Yatay ölçeklendirme için başka bir kural ekleyin. Küme ölçeklendirme sorunlarıyla ilgili yardıma ihtiyacınız varsa Azure portal [bir destek isteği açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) .

## <a name="next-steps"></a>Sonraki adımlar

* Belirttiğiniz ölçümlere göre örnek sayısını dinamik olarak ölçeklendirmek için [küme yatay ölçeklendirmeyi yönetin](manage-cluster-horizontal-scaling.md) .

* Bu makaleyi izleyerek kaynak kullanımınızı izleyin: [Azure Veri Gezgini performansını, sistem durumunu ve kullanım ölçümlerini](using-metrics.md)izleyin.

