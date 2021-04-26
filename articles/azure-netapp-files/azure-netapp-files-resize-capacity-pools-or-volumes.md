---
title: Azure NetApp Files için kapasite havuzunu veya birimi yeniden boyutlandırın | Microsoft Docs
description: Bir kapasite havuzunun veya birimin boyutunu değiştirme hakkında bilgi edinin. Kapasite havuzunun yeniden boyutlandırılması, satın alınan Azure NetApp Files kapasitesini değiştirir.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594487"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Kapasitesi havuzunu veya birimi yeniden boyutlandırma
Bir kapasite havuzunun veya birimin boyutunu gerektiği gibi değiştirebilirsiniz. 

## <a name="resize-the-capacity-pool"></a>Kapasite havuzunu yeniden boyutlandır 

Kapasite havuzu boyutunu 1-TiB artışlarla değiştirebilir veya azaltır. Ancak, kapasite havuzu boyutu 4 TiB 'den küçük olamaz. Kapasite havuzunun yeniden boyutlandırılması, satın alınan Azure NetApp Files kapasitesini değiştirir.

1. NetApp hesabını Yönet dikey penceresinde, yeniden boyutlandırmak istediğiniz kapasite havuzuna tıklayın. 
2. Kapasite havuzu adına sağ tıklayın veya "..." düğmesine tıklayın. bağlam menüsünü göstermek için kapasite havuzu satırının sonundaki simge. 
3. Kapasite havuzunu yeniden boyutlandırmak veya silmek için bağlam menüsü seçeneklerini kullanın.

## <a name="resize-a-volume"></a>Bir birimi yeniden boyutlandırma

Bir birimin boyutunu gerektiği gibi değiştirebilirsiniz. Birimin kapasite kullanımı, havuzunun sağlanan kapasitesinden sayılır.

1. NetApp hesabını Yönet dikey penceresinde **birimler**' e tıklayın. 
2. Yeniden boyutlandırmak istediğiniz birimin adına sağ tıklayın veya "..." düğmesine tıklayın. bağlam menüsünü göstermek için birimin satırının sonundaki simge.
3. Birimi yeniden boyutlandırmak veya silmek için bağlam menüsü seçeneklerini kullanın.

## <a name="resize-a-cross-region-replication-destination-volume"></a>Çapraz bölge çoğaltma hedef birimini yeniden boyutlandırma 

Bir [çapraz bölge çoğaltma](cross-region-replication-introduction.md) ilişkisinde, hedef birim, kaynak birimin boyutuna göre otomatik olarak yeniden boyutlandırılır. Bu nedenle, hedef birimi ayrı olarak yeniden boyutlandırmanıza gerek yoktur. Bu otomatik yeniden boyutlandırma davranışı, birimler etkin bir çoğaltma ilişkisinde olduğunda veya çoğaltma eşlemesi yeniden [eşitleme işlemiyle](cross-region-replication-manage-disaster-recovery.md#resync-replication)kesildiğinde geçerlidir. 

Aşağıdaki tabloda, [yansıtma durumuna](cross-region-replication-display-health-status.md)göre hedef birim yeniden boyutlandırma davranışı açıklanmaktadır:

|  Yansıtma durumu  | Hedef birim yeniden boyutlandırma davranışı |
|-|-|
| *Yansıtıldı* | Hedef birim başlatıldığında ve yansıtma güncelleştirmelerini almaya hazırsa, kaynak birimin yeniden boyutlandırılması hedef birimleri otomatik olarak yeniden boyutlandırır. |
| *UK* | Kaynak birimi yeniden boyutlandırdığınızda ve yansıtma durumu *bozulur*, hedef birim yeniden [eşitleme işlemi](cross-region-replication-manage-disaster-recovery.md#resync-replication)ile otomatik olarak yeniden boyutlandırılır.  |
| *Başlatılmadı* | Kaynak birimi yeniden boyutlandırdığınızda ve yansıtma durumu hala *başlatılmadığında*, hedef birimin el ile yapılması gerekir. Bu nedenle, kaynak birimi yeniden boyutlandırmak için başlatmanın tamamlanmasını beklemeniz önerilir (yani, yansıtma durumu *yansıtılmalıdır*). | 

> [!IMPORTANT]
> Bölgeler arası çoğaltmanın hem kaynak hem de hedef birimleri için kapasite havuzlarında yeterli sayıda yer bulunduğundan emin olun. Kaynak birimi yeniden boyutlandırdığınızda, hedef birim otomatik olarak yeniden boyutlandırılır. Ancak hedef birimi barındıran kapasite havuzunda yeterli sayıda yer yoksa, hem kaynak hem de hedef birimlerin yeniden boyutlandırılması başarısız olur.

## <a name="next-steps"></a>Sonraki adımlar

- [Kapasite havuzunu ayarlama](azure-netapp-files-set-up-capacity-pool.md)
- [El ile QoS kapasite havuzu yönetme](manage-manual-qos-capacity-pool.md)
- [Birimin hizmet düzeyini dinamik olarak değiştirme](dynamic-change-volume-service-level.md) 