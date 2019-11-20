---
title: Azure Site Recovery izleme hakkında sık sorulan sorular
description: Yerleşik izleme ve Azure Izleyici (Log Analytics) kullanarak Azure Site Recovery izleme hakkında sık sorulan sorulara yanıtlar alın
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718268"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Site Recovery izleme hakkında sık sorulan sorular

Bu makalede, Azure [Site Recovery](site-recovery-overview.md)izleme hakkında, yerleşik Site Recovery Izleme ve Azure izleyici (Log Analytics) kullanılarak gerçekleştirilen genel sorular yanıtlanmaktadır.

## <a name="general"></a>Genel

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>RPO değeri, kullanılabilir en son kurtarma noktasından farklı günlüğe kaydedilir mi?

Site Recovery, makineleri Azure 'a çoğaltmak için çok adımlı, zaman uyumsuz bir işlem kullanır.

- Çoğaltmanın Penultimate adımında, meta verilerle birlikte makinedeki son değişiklikler bir günlük/önbellek depolama hesabına kopyalanır.
- Bu değişiklikler, kurtarılabilir bir noktayı tanımlayan etiketle birlikte, hedef bölgedeki depolama hesabı/yönetilen diske yazılır.
- Site Recovery, artık makine için kurtarılabilir bir nokta oluşturabilir.
- Bu noktada, şu ana kadar depolama hesabına yüklenen değişiklikler için RPO karşılandı. Diğer bir deyişle, bu noktadaki RPO makinesi, kurtarılabilir noktaya karşılık gelen zaman damgasından geçen zaman miktarına eşittir.
- Artık Site Recovery, depolama hesabından karşıya yüklenen verileri seçer ve makine için oluşturulan çoğaltma disklerine uygular.
- Site Recovery, bir kurtarma noktası oluşturur ve yük devretmede bu noktayı kurtarmaya kullanılabilir hale getirir.
- Bu nedenle, en son kullanılabilir kurtarma noktası, zaten işlenmiş ve çoğaltma disklerine uygulanan en son kurtarma noktasına karşılık gelen zaman damgasını gösterir.


Çoğaltma kaynak makinesinde veya şirket içi altyapı sunucularında yanlış bir sistem saati, hesaplanan RPO değerini çarpıtacaktır. Doğru RPO raporlaması için sistem saatinin tüm sunucular ve makineler üzerinde doğru olduğundan emin olun.



## <a name="inbuilt-site-recovery-logging"></a>Yerleşik Site Recovery günlüğe kaydetme


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Kasa altyapısı görünümündeki VM sayısı, çoğaltılan öğelerde gösterilen toplam saymadan farklı mıdır?

Kasa altyapısı görünümü, çoğaltma senaryolarına göre kapsamlandırılır. Yalnızca şu anda seçili olan çoğaltma senaryosundaki makineler görünüm sayısına dahil edilir. Ayrıca, yalnızca Azure 'a çoğaltmak üzere yapılandırılmış VM 'Leri saydık. Yük devredilmeyen makineler veya şirket içi bir siteye geri çoğaltılan makineler görünümde sayılmaz.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Temel olarak bulunan çoğaltılan öğe sayısı, panodaki çoğaltılan öğelerin toplam sayısından farklı mıdır?

Yalnızca ilk çoğaltmanın tamamlandığı makineler temel parçalar bölümünde gösterilen sayıma dahildir. Çoğaltılan öğe toplamı, kasadaki ilk çoğaltmanın devam ettiği kişiler de dahil tüm makineleri içerir.

## <a name="azure-monitor-logging"></a>Azure İzleyici günlükleri


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>Site Recovery ne sıklıkla tanılama günlüklerini Azure Izleyici günlüğüne gönderiyor? 

- AzureSiteRecoveryReplicationStats ve AzureSiteRecoveryRecoveryPoints her 15 dakikada bir gönderilir.  
- AzureSiteRecoveryReplicationDataUploadRate ve AzureSiteRecoveryProtectedDiskDataChurn her beş dakikada bir gönderilir. 
- AzureSiteRecoveryJobs, bir işin tetiklenmesi ve tamamlanmasıyla gönderilir.
- Bir olay oluşturulduğunda Azuresterebirleþtiryevents gönderilir. 
- AzureSiteRecoveryReplicatedItems, herhangi bir ortam değişikliği olduğunda gönderilir. Genellikle, veri yenileme süresi bir değişiklikten sonraki 15 dakikadır. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Azure Izleyici günlüklerinde veri ne kadar süreyle tutulur? 

Varsayılan olarak, bekletme 31 gündür. Log Analytics çalışma alanındaki **kullanım ve tahmini maliyet** bölümündeki dönemi artırabilirsiniz. **Veri saklama**' ye tıklayın ve aralığı seçin.

### <a name="whats-the-size-of-the-diagnostic-logs"></a>Tanılama günlüklerinin boyutu nedir? 

Genellikle bir günlüğün boyutu 15-20 KB 'tır. 


## <a name="next-steps"></a>Sonraki adımlar

[Site Recovery yerleşik izleme](site-recovery-monitor-and-troubleshoot.md)veya [Azure izleyici](monitor-log-analytics.md)ile nasıl izleneceğini öğrenin.


