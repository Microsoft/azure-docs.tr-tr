---
title: Video Indexer iş sürekliliği ve olağanüstü durum kurtarma (BCDR)-Azure | Microsoft Docs
description: Bölgesel bir veri merkezi kesintisi veya hatası oluşursa ikincil Video Indexer hesabına yük devretmeyi öğrenin.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: ce788b4640f0a6c6f25b3280ce4f52fd018d1699
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668312"
---
# <a name="handle-video-indexer-business-continuity-and-disaster-recovery"></a>İşle Video Indexer iş sürekliliği ve olağanüstü durum kurtarma

Bölgesel bir veri merkezi kesintisi veya arızası varsa Azure Media Services Video Indexer hizmetin anında yük devretmesini sağlamaz. Bu makalede, bir olağanüstü durum oluşursa uygulamalar için en iyi kullanılabilirliği sağlamak üzere bir yük devretme için ortamınızın nasıl yapılandırılacağı ve en aza küçültülmüş kurtarma süresi açıklanır.

Azure 'un yalıtım ve kullanılabilirlik ilkelerinden faydalanmasını sağlamak için iş sürekliliği olağanüstü durum kurtarma (BCDR) ' i bölgesel çiftler arasında yapılandırmanızı öneririz. Daha fazla bilgi için bkz. [Azure eşlenmiş bölgeler](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Önkoşullar 

Azure aboneliği. Henüz bir Azure aboneliğiniz yoksa [Azure Ücretsiz deneme sürümü](https://azure.microsoft.com/free/)için kaydolun.

## <a name="failover-to-a-secondary-account"></a>İkincil bir hesaba yük devretme

BCDR 'yi uygulamak için, artıklığı işlemek üzere iki Video Indexer hesabınızın olması gerekir.

1. Azure 'a bağlı iki Video Indexer hesabı oluşturun (bkz. [Hesap oluşturma](connect-to-azure.md)). Biri birincil bölgesiniz ve diğeri eşleştirilmiş Azure bölgesi. 
1. Birincil bölgenizde bir hata varsa, ikincil hesabı kullanarak dizin oluşturma ' ya geçin.

> [!TIP]
> Hizmet [bildirimlerinde etkinlik günlüğü uyarıları oluşturma](../../service-health/alerts-activity-log-service-notifications.md)başına hizmet durumu bildirimleri için etkinlik günlüğü uyarılarını ayarlayarak BCDR 'yi otomatikleştirebilirsiniz.

Birden çok kiracı kullanma hakkında bilgi için bkz. [birden çok kiracıyı yönetme](manage-multiple-tenants.md). BCDR 'yi uygulamak için şu iki seçenekten birini seçin: Her kiracı veya kiracı başına [Azure aboneliği](manage-multiple-tenants.md#azure-subscription-per-tenant) [video Indexer hesap](manage-multiple-tenants.md#video-indexer-account-per-tenant) .

## <a name="next-steps"></a>Sonraki adımlar

[Azure 'a bağlı bir video Indexer hesabını yönetin](manage-account-connected-to-azure.md).
