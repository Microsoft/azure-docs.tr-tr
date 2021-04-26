---
title: Azure NetApp Files ölçümleri | Microsoft Docs
description: Azure NetApp Files, ayrılan depolama, gerçek depolama alanı kullanımı, hacim ıOPS ve gecikme hakkında ölçümler sağlar. Kullanım ve performansı anlamak için bu ölçümleri kullanın.
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
ms.date: 04/12/2021
ms.author: b-juche
ms.openlocfilehash: b581470a886ff73739edfee7f45c64295eeeb1f0
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388623"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files için ölçümler

Azure NetApp Files, ayrılan depolama, gerçek depolama alanı kullanımı, hacim ıOPS ve gecikme hakkında ölçümler sağlar. Bu ölçümleri çözümleyerek, NetApp hesaplarınızın kullanım deseninin ve birim performansının daha iyi şekilde öğrenilmesine sahip olabilirsiniz.  

Kapasite **havuzunu** veya **birimi** seçerek kapasite havuzu veya birimi için ölçümleri bulabilirsiniz.  Ardından, kullanılabilir ölçümleri görüntülemek için **ölçüm** ' e tıklayın: 

[![Ölçüm çekmesine nasıl gidebileceğiniz gösteren anlık görüntü. ](../media/azure-netapp-files/metrics-navigate-volume.png)](../media/azure-netapp-files/metrics-navigate-volume.png#lightbox)

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Kapasite havuzları için kullanım ölçümleri

- *Havuza ayrılan boyut*   
    Havuzun sağlanan boyutu.

- *Birim boyutuna ayrılan havuz*  
    Belirli bir kapasite havuzundaki birim kotasının (GiB) toplamı (diğer bir deyişle, kapasite havuzundaki birimlerin sağlanan boyutlarının toplamı).  
    Bu boyut, birim oluşturma sırasında seçtiğiniz boyutudur.  

- *Kullanılan havuz boyutu*  
    Bir kapasite havuzundaki birimlerde kullanılan mantıksal alan toplamı (GiB).  

- *Havuz için toplam anlık görüntü boyutu*    
    Havuzdaki tüm birimlerden anlık görüntü boyutu toplamı.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Birimler için kullanım ölçümleri

- *Tüketilen yüzde birimi boyutu*    
    Anlık görüntüler dahil olmak üzere tüketilen birimin yüzdesi.  
- *Birim ayırma boyutu*   
    Bir birimin sağlanan boyutu
- *Birim kotası boyutu*    
    Birimin birlikte sağlandığı kota boyutu (GiB).   
- *Tüketilen birim boyutu*   
    Birimin mantıksal boyutu (kullanılan baytlar).  
    Bu boyut, etkin dosya sistemleri ve anlık görüntüleri tarafından kullanılan mantıksal alanı içerir.  
- *Birim anlık görüntü boyutu*   
   Bir birimdeki tüm anlık görüntülerin boyutu.  

## <a name="performance-metrics-for-volumes"></a>Birimler için performans ölçümleri

- *Ortalama okuma gecikmesi*   
    Birimdeki ortalama okuma süresi (milisaniye).
- *Ortalama yazma gecikmesi*   
    Birimdeki ortalama yazma süresi (milisaniye).
- *IOPS 'yi oku*   
    Birime saniye başına okuma sayısı.
- *IOPS yaz*   
    Birime saniye başına yazma sayısı.
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>Birim çoğaltma ölçümleri

> [!NOTE] 
> * Ağ aktarım boyutu (örneğin, *birim çoğaltması toplam aktarım* ölçümleri), bir çapraz bölge çoğaltmasının kaynak veya hedef birimlerinden farklı olabilirler. Bu davranış, ağ aktarım maliyetini en aza indirmek için kullanılan etkili çoğaltma altyapısının bir sonucudur.
> * Birim çoğaltma ölçümleri Şu anda çoğaltma ilişkisinin kaynağı değil, çoğaltma hedef birimleri için doldurulmuştur.

- *Birim çoğaltma durumu sağlıklı*   
    Çoğaltma ilişkisinin koşulu. Sağlıklı bir durum tarafından gösterilir `1` . Sağlıksız bir durum tarafından gösterilir `0` .

- *Birim çoğaltma aktarılıyor*    
    Birim çoğaltmasının durumunun ' aktarma ' olup olmadığı. 
 
- *Birim çoğaltma gecikme süresi*   
    Yansıtmadaki verilerin kaynağın arkasında olduğu sürenin saniye cinsinden miktarı. 

- *Birim çoğaltma son aktarım süresi*   
    Son aktarımın tamamlanması için geçen saniye cinsinden süre. 

- *Birim çoğaltma son aktarım boyutu*    
    Son aktarımın bir parçası olarak aktarılan toplam bayt sayısı. 

- *Birim çoğaltma ilerleme durumu*    
    Geçerli aktarım işlemi için aktarılan toplam veri miktarı. 

- *Birim çoğaltma toplam aktarımı*   
    İlişki için aktarılan birikmeli baytlar. 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure NetApp Files’ın depolama hiyerarşisini anlama](azure-netapp-files-understand-storage-hierarchy.md)
* [Kapasite havuzunu ayarlama](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files için birim oluşturma](azure-netapp-files-create-volumes.md)
