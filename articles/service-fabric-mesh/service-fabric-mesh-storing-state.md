---
title: Azure Service Fabric ağı 'nda durum depolama seçenekleri
description: Azure Service Fabric ağı üzerinde çalışan Service Fabric kafes uygulamalarında durumu güvenilir bir şekilde depolama hakkında bilgi edinin.
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 651329b1b061f2cf5a06dbdc9d60f73cec64e7d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625558"
---
# <a name="state-management-with-service-fabric"></a>Service Fabric ile durum yönetimi

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Service Fabric, durum depolama için birçok farklı seçeneği destekler. Durum yönetimi desenleri ve Service Fabric kavramsal bir genel bakış için bkz. [Service Fabric kavramlar: State](../service-fabric/service-fabric-concepts-state.md). Bu tüm kavramlar, hizmetlerinizin Service Fabric kafesin içinde veya dışında çalışmasına bakılmaksızın geçerlidir. 

Service Fabric ağ ile, kolayca yeni bir uygulamayı dağıtabilir ve Azure 'da barındırılan mevcut bir veri deposuna bağlayabilirsiniz. Herhangi bir uzak veritabanını kullanmanın yanı sıra, hizmetin yerel veya uzak depolama alanını desimi etmediğine bağlı olarak verilerin depolanması için çeşitli seçenekler vardır. 

## <a name="volumes"></a>Birimler

Kapsayıcılar genellikle geçici diskleri kullanır. Ancak geçici diskler kısa ömürlü olduğundan, yeni bir geçici disk alır ve bir kapsayıcı kilitlenirse bilgileri kaybedersiniz. Ayrıca, diğer kapsayıcılarla geçici disklerde bilgi paylaşmak da zordur. Birimler, durumu kalıcı hale getirmek için kullanabileceğiniz kapsayıcı örneklerinizin içine bağlanmış olan dizinlerdir. Birimler, genel amaçlı dosya depolaması sağlar ve normal disk g/ç dosya API 'Lerini kullanarak dosyaları okumanızı/yazmanızı sağlar. Birim kaynağı, bir dizinin nasıl bağlanacağını ve hangi depolamanın kullanılacağını açıklar. Verileri depolamak için Azure dosya depolama veya Service Fabric birim diski seçebilirsiniz.

![Diyagramda, her ikisi de çoğaltılan bir yerel diskte Service Fabric güvenilir birimde ve ağ depolamada bulunan Azure dosyaları biriminde akan bir birimi akışa akan hizmet gösterilmektedir.][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric güvenilir birim

Service Fabric güvenilir birim, yerel bir birimi bir kapsayıcıya bağlamak için kullanılan bir Docker birimi sürücüsüdür. Okuma ve yazma işlemleri yerel işlemlerdir ve hızlıdır. Veriler ikincil düğümlere çoğaltılır ve yüksek oranda kullanılabilir hale gelir. Yük devretme da hızlıdır. Bir kapsayıcı kilitlenirse, verilerin bir kopyasına zaten sahip olan bir düğüme yük devreder. Bir örnek için bkz. [Service Fabric güvenilir bir birimle uygulama dağıtma](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Azure dosyaları hacmi

Azure dosyaları birimi, bir Azure dosya paylaşımının bir kapsayıcıya bağlanması için kullanılan bir Docker birimi sürücüsüdür. Azure dosyaları depolaması ağ depolama alanını kullanır, bu nedenle ağ üzerinden okuma ve yazma işlemleri gerçekleşir. Azure dosya depolaması, Service Fabric güvenilir birimle karşılaştırıldığında daha az performans sağlar, ancak bir ucuz ve tam olarak güvenilir bir veri seçeneği sağlar. Bir örnek için bkz. [Azure dosyaları hacminde uygulama dağıtma](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Sonraki adımlar

Uygulama modeli hakkında daha fazla bilgi için bkz. [Service Fabric Resources](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
