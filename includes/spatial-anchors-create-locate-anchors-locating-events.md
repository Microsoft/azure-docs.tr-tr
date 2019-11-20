---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180080"
---
İzleyici oluşturulduktan sonra, `AnchorLocated` istenen her tutturucu için olay harekete geçmeyecektir. Bu olay, bir bağlayıcı bulunduğunda veya bağlayıcı bulunamıyorsa ateşlenir. Bu durum oluşursa, nedeni durumunda belirtilecektir. Bir izleyici için tüm Tutturucuların işlenmesi, bulunması veya bulunamadıktan `LocateAnchorsCompleted` sonra olay harekete geçmeyecektir. İzleyici başına 35 tanımlayıcıda bir sınır vardır. 
