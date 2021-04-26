---
title: Azure NetApp Files için kapasite havuzu sorunlarını giderme | Microsoft Docs
description: Kapasite havuzlarını yönetirken oluşabilecek olası sorunları açıklar ve sorunlar için çözümler sağlar.
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
ms.topic: troubleshooting
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 759759b67582b241d0bab1e043dd15e54a804faf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251548"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Kapasite havuzu sorunlarını giderme

Bu makalede, havuz değiştirme işlemi de dahil olmak üzere kapasite havuzlarını yönetirken karşılaşabileceğiniz sorunlara yönelik çözümler açıklanmaktadır. 

## <a name="issues-managing-a-capacity-pool"></a>Kapasite havuzunu yönetme sorunları 

|     Hata koşulu    |     Çözüm    |
|-|-|
| Kapasite havuzu oluşturma sorunları |  Kapasite havuzu sayısının sınırı aşmadığından emin olun. [Azure NetApp Files Için kaynak sınırlarına](azure-netapp-files-resource-limits.md)bakın.  Sayı sınırdan küçükse ve sorun yaşamaya devam ediyorsanız, bir destek bileti dosyası ve kapasite havuzu adını belirtin. |
| Bir kapasite havuzunu silme sorunları  |  Kapasite havuzunu silmeye çalıştığınız abonelikte tüm Azure NetApp Files birimlerini ve anlık görüntüleri kaldırdığınızdan emin olun. <br> Tüm birimleri ve anlık görüntüleri zaten kaldırdıysanız ve yine de kapasite havuzunu silemiyorsanız, kaynak başvuruları portalda gösterilmeksizin yine de mevcut olabilir. Bu durumda, bir destek bileti dosyası ve yukarıdaki önerilen adımları gerçekleştirdiğini belirtin. |
| Birim oluşturma veya değiştirme hata vererek başarısız oluyor `Requested throughput not available` | Bir birim için kullanılabilir verimlilik, kapasite havuzunun boyutu ve hizmet düzeyi tarafından belirlenir. Yeterli üretilen iş yoksa, havuz boyutunu artırmanız veya var olan birim aktarım hızını ayarlamanız gerekir. | 

## <a name="issues-when-changing-the-capacity-pool-of-a-volume"></a>Bir birimin kapasite havuzunu değiştirirken oluşan sorunlar 

|     Hata koşulu    |     Çözüm    |
|-|-|
| Bir birim için kapasite havuzunun değiştirilmesine izin verilmiyor. | Henüz bu özelliği kullanmak için yetkiniz olmayabilir. <br> Bir birimi başka bir kapasite havuzuna taşıma özelliği şu anda önizleme aşamasındadır. Bu özelliği ilk kez kullanıyorsanız, önce özelliği kaydetmeniz ve ayarlamanız gerekir `-FeatureName ANFTierChange` . [Bir birimin hizmet düzeyini dinamik olarak değiştirme](dynamic-change-volume-service-level.md)içindeki kayıt adımlarına bakın. |
| Kapasite havuzu boyutu toplam birim boyutu için çok küçük. |  Bu hata, hedef kapasite havuzunun, taşınmakta olan birim için kullanılabilir kapasiteye sahip olmayan bir sonucudur.  <br> Hedef havuzun boyutunu artırın veya daha büyük olan başka bir havuz seçin.  Bkz. [bir kapasite havuzunu veya birimi yeniden boyutlandırma](azure-netapp-files-resize-capacity-pools-or-volumes.md).   |
|  Hedef havuzda zaten adlandırılmış bir birim bulunduğundan havuz değişikliği tamamlanamıyor `'{source pool name}'``'{target pool name}'` | Bu hata, aynı ada sahip bir birim hedef kapasite havuzunda zaten varolduğu için oluşur.  Aynı ada sahip bir birimi olmayan başka bir kapasite havuzu seçin.   | 

## <a name="next-steps"></a>Sonraki adımlar  

* [Kapasite havuzunu ayarlama](azure-netapp-files-set-up-capacity-pool.md)
* [El ile QoS kapasite havuzu yönetme](manage-manual-qos-capacity-pool.md)
* [Birimin hizmet düzeyini dinamik olarak değiştirme](dynamic-change-volume-service-level.md)
* [Kapasitesi havuzunu veya birimi yeniden boyutlandırma](azure-netapp-files-resize-capacity-pools-or-volumes.md)
