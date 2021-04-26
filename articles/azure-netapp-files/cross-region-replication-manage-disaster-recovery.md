---
title: Azure NetApp Files çapraz bölge çoğaltmasını kullanarak olağanüstü durum kurtarmayı yönetme | Microsoft Docs
description: Azure NetApp Files çapraz bölge çoğaltma kullanılarak olağanüstü durum kurtarmanın nasıl yönetileceğini açıklar.
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
ms.openlocfilehash: 5b1c1a5216b7a1ad5b23167e776f2b0bbb0a578f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591002"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Çapraz bölge çoğaltmasını kullanarak olağanüstü durum kurtarmayı yönetme 

Kaynak ve hedef birimler arasında devam eden bir çoğaltma (bkz. [birim çoğaltma oluşturma](cross-region-replication-create-peering.md)) sizi bir olağanüstü durum kurtarma etkinliği hazırlar. 

Böyle bir olay gerçekleştiğinde, [hedef birime yük devretmek](#fail-over-to-destination-volume)için istemcinin hedef birimde okuma ve yazma olanağı sağlayabilirsiniz. 

Olağanüstü durum kurtarma sonrasında, kaynak birime geri dönmek için bir yeniden [eşitleme](#resync-replication) işlemi gerçekleştirebilirsiniz. Ardından [kaynaktan hedefe çoğaltmayı](#reestablish-source-to-destination-replication) yeniden yükledikten sonra istemci için kaynak birimi uzaktan yeniden bağlayın. 

Ayrıntılar aşağıda açıklanmıştır. 

## <a name="fail-over-to-destination-volume"></a>Hedef birime yük devretme

Hedef birimi etkinleştirmeniz gerektiğinde (örneğin, hedef bölgeye yük devretmek istediğinizde), çoğaltma eşlemesini kesmeniz ve ardından hedef birimi bağlamanız gerekir.  

1. Çoğaltma eşlemesini bölmek için hedef birimi seçin. Depolama hizmeti altında **çoğaltma** ' ya tıklayın.  

2.  Devam etmeden önce aşağıdaki alanları kontrol edin:  
    * Yansıtma durumunun ***yansıtmalı*** olduğundan emin olun.   
        Yansıtma durumu *başlatılmamış* olarak görünüyorsa çoğaltma eşlemesini kesmeyi denemeyin.
    * Ilişki durumunun ***Boşta*** gösterildiğinden emin olun.   
        Ilişki durumu *aktarmayı* gösteriyorsa çoğaltma eşlemesini kesmeyi denemeyin.   

    Bkz. [çoğaltma ilişkisinin sistem durumunu görüntüleme](cross-region-replication-display-health-status.md). 

3.  **Eşlemeyi kes**' e tıklayın.  

4.  İstendiğinde **Evet** yazın ve **Kes** düğmesine tıklayın. 

    ![Çoğaltma eşlemesini kes](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  [Windows veya Linux sanal makineleri için bir birimi bağlama veya çıkarma](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)bölümündeki adımları izleyerek hedef birimi bağlayın.   
    Bu adım, bir istemcinin hedef birime erişmesine olanak sağlar.

## <a name="resync-volumes-after-disaster-recovery"></a><a name="resync-replication"></a>Olağanüstü durum kurtarma sonrasında birimleri yeniden eşitle

Olağanüstü durum kurtarma sonrasında, yeniden eşitleme işlemi gerçekleştirerek kaynak birimi yeniden etkinleştirebilirsiniz.  Yeniden eşitleme işlemi, çoğaltma işlemini tersine çevirir ve hedef birimdeki verileri kaynak birime eşitler.  

> [!IMPORTANT] 
> Yeniden eşitleme işlemi, kaynak birim verilerinin üzerine hedef birim verilerini yazar.  Kullanıcı arabirimi, veri kaybı potansiyeli hakkında sizi uyarır. İşlem başlamadan önce yeniden eşitleme eylemini onaylamanız istenir.

1. Çoğaltmayı yeniden eşitleme için *kaynak* birimi seçin. Depolama hizmeti altında **çoğaltma** ' ya tıklayın. Yeniden **Eşitle**' ye tıklayın.  

2. İstendiğinde **Evet** yazın ve yeniden **Eşitle**' ye tıklayın. 
 
    ![Çoğaltmayı yeniden eşitle](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. [Çoğaltma ilişkisinin sistem durumunu görüntüleme](cross-region-replication-display-health-status.md)bölümündeki adımları izleyerek kaynak birim sistem durumunu izleyin.   
    Kaynak birim sistem durumu aşağıdaki değerleri gösterdiğinde, yeniden eşitleme işlemi tamamlanır ve hedef birimde yapılan değişiklikler artık kaynak birimde yakalanır:   

    * Yansıtılmış durum: *yansıtılmış*  
    * Aktarım durumu: *Boşta*  

## <a name="reestablish-source-to-destination-replication"></a>Kaynaktan hedefe çoğaltmayı yeniden kurma

Hedefin kaynağa yeniden eşitleme işlemi tamamlandıktan sonra, kaynaktan hedefe çoğaltmayı yeniden oluşturmak için çoğaltma eşlemesini tekrar kesmeniz gerekir. Ayrıca, istemcinin erişebilmesi için kaynak birimini de yeniden bağlayın.  

1. Çoğaltma eşlemesini kes:  
    a. *Hedef* birimi seçin. Depolama hizmeti altında **çoğaltma** ' ya tıklayın.  
    b. Devam etmeden önce aşağıdaki alanları kontrol edin:   
    * Yansıtma durumunun ***yansıtmalı*** olduğundan emin olun.   
    Yansıtma durumu *başlatılmamış* olarak görünüyorsa çoğaltma eşlemesini kesmeyi denemeyin.  
    * Ilişki durumunun ***Boşta*** gösterildiğinden emin olun.   
    Ilişki durumu *aktarmayı* gösteriyorsa çoğaltma eşlemesini kesmeyi denemeyin.    

        Bkz. [çoğaltma ilişkisinin sistem durumunu görüntüleme](cross-region-replication-display-health-status.md). 

    c. **Eşlemeyi kes**' e tıklayın.   
    d. İstendiğinde **Evet** yazın ve **Kes** düğmesine tıklayın.  

2. Kaynak birimi hedef birimle yeniden eşitleyin:  
    a. *Hedef* birimi seçin. Depolama hizmeti altında **çoğaltma** ' ya tıklayın. Yeniden **Eşitle**' ye tıklayın.   
    b. İstendiğinde **Evet** yazın ve yeniden **Eşitle** düğmesine tıklayın.

3. [Windows veya Linux sanal makineleri için bir birimi bağlama veya çıkarma](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)bölümündeki adımları izleyerek kaynak birimi uzaktan yeniden bağlayın.  
    Bu adım, bir istemcinin kaynak birime erişmesine olanak sağlar.

## <a name="next-steps"></a>Sonraki adımlar  

* [Bölgeler arası çoğaltma](cross-region-replication-introduction.md)
* [Bölgeler arası çoğaltmayı kullanma gereksinimleri ve konuları](cross-region-replication-requirements-considerations.md)
* [Çoğaltma ilişkisinin uygunluk durumunu görüntüleme](cross-region-replication-display-health-status.md)
* [Çapraz bölge çoğaltma hedef birimini yeniden boyutlandırma](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Birim çoğaltma ölçümleri](azure-netapp-files-metrics.md#replication)
* [Birim çoğaltmalarını veya birimleri silme](cross-region-replication-delete.md)
* [Bölgeler arası çoğaltma sorunlarını giderme](troubleshoot-cross-region-replication.md)

