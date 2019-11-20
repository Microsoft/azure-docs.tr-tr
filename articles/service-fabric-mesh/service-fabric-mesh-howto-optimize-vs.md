---
title: Azure Service Fabric kafes projeleri için Visual Studio performansını iyileştirin | Microsoft Docs
description: Bu makalede, ilk hata ayıklama çalıştırmasının (F5) çok daha hızlı olması için Service Fabric ağı projelerinin Visual Studio performansının nasıl iyileştirileceği gösterilmektedir.
services: service-fabric-mesh
keywords: hata ayıklama performansını iyileştirme
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: 04aeaa6b008b50789f4380e4bb98beba3957c2e9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663429"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Service Fabric kafes projeleri için Visual Studio performansını iyileştirin

Bu makalede, ilk hata ayıklama çalıştırmasının (F5) çok daha hızlı olması için Service Fabric ağı projelerinin Visual Studio performansının nasıl iyileştirileceği gösterilmektedir.  

## <a name="change-visual-studio-settings"></a>Visual Studio ayarlarını değiştir
 
Visual Studio 'da **araçlar** > **Seçenekler**  > **Service Fabric ağ > araçları** ' nın altında, **genel**olarak aşağıdaki ayarları yapabilirsiniz:

- Projede **çekme gerekli Docker görüntüleri** , proje yüklenirken görüntü indirme işlemini başlatarak ilk hata ayıklamanın (F5) daha hızlı çalışmasını sağlar.  
- **Projeyi Project Open üzerinde dağıt** , proje açıldıktan sonra dağıtım işlemini başlatarak ilk hata ayıklamanın çalışmasını (F5) daha hızlı hale getirir.  
- Proje kapatıldığında, uygulamayı kaldırarak uygulama için ayrılmış geri kazanır kaynaklarını (CPU, RAM) **kaldırın** .  

Service Fabric Araçları çıktı penceresinde, Visual Studio 'nun ' görüntü çekme ', ' hazırlama ' veya ' uygulamayı kaldırma ' olduğunu belirten iletiler, yukarıdaki ayarlara başvurmakta olur. Bu ayarları devre dışı bırakabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Bir kafes uygulamasının hatalarını ayıkla öğreticisini](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md) okuyun