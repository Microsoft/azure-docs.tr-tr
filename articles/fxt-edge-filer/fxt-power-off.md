---
title: Microsoft Azure FXT Edge Filer birimini kapatma
description: Azure FXT Edge Filer düğümünü başlatma ve güvenle kapanmaya yönelik yordamlar
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255991"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Azure FXT Edge Filer donanımını güvenle kapatma

Tek bir düğüme geçiş yapmak için fiziksel güç düğmesini kullanabilseniz de, birimi normal koşullarda kapatmak için kullanmamalısınız.

Bir Azure FXT Edge Filer düğümü bir kümenin parçası olarak kullanıldıktan sonra, donanımı kapatmak için küme Denetim Masası yazılımını kullanmanız gerekir. 

> [!NOTE] 
> Olası veri kaybını veya bozulmasını önlemek için, her zaman Denetim Masası yazılımını kullanarak bir Azure FXT Edge filu 'ni kapatın. Microsoft Müşteri Hizmetleri ve destek ile ilgili olarak belirtilmedikçe, kapatma için fiziksel güç düğmesini kullanmayın.
> 
> Elektrik acil bir durumda, güç bağlantılarının bağlantısını kesin veya veri merkezinizi elektrik bağlantısı kesme mekanizmasını kullanın.

## <a name="shut-down-a-node-from-the-control-panel"></a>Denetim masasından bir düğümü kapatma

Azure FXT Edge Filer düğümünü güvenle kapatmak için bu yönergeleri izleyin:

1. Küme denetim masasında oturum açın. ( [Ayarlar sayfalarında açık](fxt-cluster-create.md#open-the-settings-pages)olan yönergeler)
1. **Ayarlar** sekmesine tıklayın, sonra **küme** > **FXT düğümleri** sayfasını yükleyin.
1. Küme düğümleri listesinde, kapatmak istediğiniz bir tane bulun. **Eylemler** sütununda, **Güç aşağı** düğmesine tıklayın. 
1. Birkaç dakika bekleyin. Düğüm kapanır ve devre dışı bırakılır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure FXT Edge Filer donanım durumunu izlemek](fxt-monitor.md)Için durum LED 'leri ve diğer göstergeler hakkında bilgi edinin.
* [Power kabloları](fxt-network-power.md#connect-power-cables)ile Azure FXT Edge Filer güç kaynakları hakkında daha fazla bilgi edinin.
