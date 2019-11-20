---
title: Azure NetApp Files ölçümleri | Microsoft Docs
description: Azure NetApp Files için ölçümleri açıklar.
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
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848801"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files için ölçümler

Azure NetApp Files, ayrılan depolama, gerçek depolama alanı kullanımı, birim üretimi, ıOPS ve gecikme hakkında ölçümler sağlar. Bu ölçümleri çözümleyerek, NetApp hesaplarınızın kullanım deseninin ve birim performansının daha iyi şekilde öğrenilmesine sahip olabilirsiniz.  

## <a name="capacity_pools"></a>Kapasite havuzları için kullanım ölçümleri

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *Ayrılan birim havuzu*  
    Belirli bir kapasite havuzundaki birim kotasının (GiB) toplamı (yani, kapasite havuzundaki birimlerin sağlanan boyutlarının toplamı)  
    Bu, birim oluşturma sırasında seçtiğiniz boyutudur.  
- *Birim havuzu toplam mantıksal boyut*  
    Bir kapasite havuzundaki birimlerde kullanılan toplam mantıksal alan (GiB)  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>Birimler için kullanım ölçümleri

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *Birim mantıksal boyutu*   
    Bir birimde kullanılan toplam mantıksal alan (GiB)  
    Bu boyut, etkin dosya sistemleri ve anlık görüntüleri tarafından kullanılan mantıksal alanı içerir.  
- *Birim anlık görüntü boyutu*   
   Bir birimdeki anlık görüntüler tarafından kullanılan artımlı mantıksal alan  

## <a name="performance-metrics-for-volumes"></a>Birimler için performans ölçümleri

- *AverageReadLatency*   
    Birimdeki ortalama okuma süresi (milisaniye)
- *AverageWriteLatency*   
    Birimdeki ortalama yazma süresi (milisaniye)
- *ReadIops*   
    Birime saniye başına okuma sayısı
- *Writeıops*   
    Birime saniye başına yazma sayısı

## <a name="next-steps"></a>Sonraki adımlar

* [Azure NetApp Files depolama hiyerarşisini anlayın](azure-netapp-files-understand-storage-hierarchy.md)
* [Kapasitesi havuzunu ayarlama](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files için birim oluşturma](azure-netapp-files-create-volumes.md)
