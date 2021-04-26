---
title: Azure Site Recovery ile olağanüstü durum kurtarma için Azure VM 'lerinin yükünü ikincil bir bölgeye devretmek için öğretici.
description: Azure Site Recovery hizmeti ile olağanüstü durum kurtarma için ikincil bir Azure bölgesine çoğaltılan Azure VM 'lerinden yük devretme ve yeniden koruma hakkında bilgi edinmek için öğretici.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392869"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>Öğretici: Azure VM 'lerinden bir ikincil bölgeye yük devretme

[Azure Site Recovery](site-recovery-overview.md)ile olağanüstü durum kurtarma Için etkinleştirilen Azure VM 'lerinin Ikincil bir Azure bölgesine nasıl yük devredebileceğinizi öğrenin. Yük devretmeden sonra, hedef bölgedeki VM 'Leri birincil bölgeye tekrar çoğaltılacak şekilde yeniden korumalısınız. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Önkoşulları denetleme
> * VM ayarlarını doğrulama
> * İkincil bölgeye yük devretme çalıştırma
> * VM 'yi birincil bölgeye yeniden çoğaltmaya başlayın.


> [!NOTE]
> Bu öğreticide, en az adımla VM 'Lerin yük devretme işlemi gösterilmektedir. Tam ayarlarla yük devretme çalıştırmak istiyorsanız, Azure VM [ağı](azure-to-azure-about-networking.md), [Otomasyonu](azure-to-azure-powershell.md)ve [sorun giderme](azure-to-azure-troubleshoot-errors.md)hakkında bilgi edinin.



## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce şunları yapmalısınız:

1. Bir veya daha fazla Azure sanal makinesi için çoğaltmayı ayarlayın. Yapmadıysanız, bunu yapmak için bu serideki [ilk öğreticiyi doldurun](azure-to-azure-tutorial-enable-replication.md) .
2. Çoğaltılan VM 'Ler için [bir olağanüstü durum kurtarma ayrıntısı çalıştırmanızı](azure-to-azure-tutorial-dr-drill.md) öneririz. Tam yük devretme çalıştırmadan önce detaya gitme çalıştırmak, üretim ortamınızı etkilemeden her şeyin beklendiği gibi çalıştığından emin olmanıza yardımcı olur. 


## <a name="verify-the-vm-settings"></a>VM ayarlarını doğrulama

1. Kasada **çoğaltılan öğeleri**> VM 'yi seçin.

    ![Genel Bakış sayfasında VM özelliklerini açma seçeneği](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. VM **'ye genel bakış** sayfasında, yük devretme ÇALıŞTıRMADAN önce VM 'nin korumalı ve sağlıklı olduğunu denetleyin.
    ![VM özelliklerini ve durumunu doğrulama sayfası](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. Yük devretme yapmadan önce şunları denetleyin:
    - VM, desteklenen bir [Windows](azure-to-azure-support-matrix.md#windows) veya [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) işletim sistemi çalıştırıyor.
    - VM [işlem](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [depolama](azure-to-azure-support-matrix.md#replicated-machines---storage)ve [ağ](azure-to-azure-support-matrix.md#replicated-machines---networking) gereksinimleriyle uyumludur.

## <a name="run-a-failover"></a>Yük devretme çalıştırma


1. VM **'ye genel bakış** sayfasında, **Yük devretme**' yı seçin.

    ![Çoğaltılan öğe için yük devretme düğmesi](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. **Yük devretme** bölümünde bir kurtarma noktası seçin. Hedef bölgedeki Azure VM, bu kurtarma noktasındaki veriler kullanılarak oluşturulur.
  
   - **En son işlenen**: Site Recovery tarafından işlenen en son kurtarma noktasını kullanır. Zaman damgası gösterilir. Verileri işlemek için zaman harcanmadan, düşük bir kurtarma süresi hedefi (RTO) sağlar.
   -  **En son**: Site Recovery gönderilen tüm verileri, yük devretmadan önce her VM için bir kurtarma noktası oluşturmak üzere işler. , Yük devretme tetiklendiğinde tüm veriler Site Recovery çoğaltıldığından, en düşük kurtarma noktası hedefini (RPO) sağlar.
   - **En son uygulamayla tutarlı**: Bu seçenek VM 'lerin üzerinde en son uygulamayla tutarlı kurtarma noktasına devredildi. Zaman damgası gösterilir.
   - **Özel**: yük devretme, belirli bir kurtarma noktasına devredildi. Özel yalnızca tek bir VM 'nin yükünü devretmek ve kurtarma planı kullanmıyorsanız kullanılabilir.

    > [!NOTE]
    > Çoğaltmayı etkinleştirdikten sonra VM 'ye bir disk eklediyseniz çoğaltma noktaları, kurtarma için kullanılabilir diskleri gösterir. Örneğin, ikinci bir disk eklenmeden önce oluşturulan bir çoğaltma noktası, "1/2 disk" olarak görünür.

4. Yük devretmeye başlamadan önce Site Recovery kaynak VM 'Leri kapatmayı denemek istiyorsanız, **yük devretmeye başlamadan önce makineyi Kapat ' ı** seçin. Kapalı, veri kaybı olmamasını sağlamaya yardımcı olur. Kapatma işlemi başarısız olsa bile yük devretme devam eder. 

    ![Yük devretme ayarları sayfası](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. Yük devretmeyi başlatmak için **Tamam**' ı seçin.
4. Bildirimlerde yük devretmeyi izleyin.

    ![İlerleme durumu bildirimi ](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![ başarı bildirimi](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. Yük devretmeden sonra, hedef bölgede oluşturulan Azure VM **sanal makinelerde** görünür. VM 'nin çalıştığından ve uygun şekilde boyutlandırıldığından emin olun. VM için farklı bir kurtarma noktası kullanmak istiyorsanız, **Essentials** sayfasında **kurtarma noktasını Değiştir**' i seçin.
6. Yükü devredilen VM 'nin memnun olduğunuzda, yük devretmeyi tamamlamak için genel bakış sayfasında **Yürüt** ' ü seçin.

    ![Kaydet düğmesi](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. **Kaydet** bölümünde onaylamak için **Tamam** ' ı seçin. Yürüt, Site Recovery sanal makinenin kullanılabilir tüm kurtarma noktalarını siler ve kurtarma noktasını değiştiremezsiniz.

8. Bildirimlerin ilerleme ilerlemesini izleyin.

    ![İlerleme durumu bildirimi ](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![ kayıt başarı bildirimi](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. Site Recovery yük devretmeden sonra kaynak VM 'yi temizleyemiyor. Bunu el ile yapmanız gerekir.


## <a name="reprotect-the-vm"></a>VM 'yi yeniden koruma

Yük devretmeden sonra, birincil bölgeye geri çoğaltılacağını sağlamak için VM 'yi ikincil bölgede yeniden korumalısınız. 

1. Başlamadan önce VM **durumunun** *Yük devretme taahhütine* sahip olduğundan emin olun.
2. Birincil bölgeye erişebildiğinden ve içinde VM oluşturma izniniz olduğundan emin olun.
3. VM **genel bakış** sayfasında **yeniden koru**' yı seçin.

   ![Bir VM için VM için yeniden korumayı etkinleştirme düğmesi.](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. **Yeniden koruma** bölümünde, çoğaltma yönünü (ikincil birincil bölgeye) doğrulayın ve birincil bölgenin hedef ayarlarını gözden geçirin. Yeni olarak işaretlenen kaynaklar, yeniden koruma işleminin bir parçası olarak Site Recovery tarafından oluşturulur.

     ![Yeniden koruma ayarları sayfası](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. Yeniden koru işlemini başlatmak için **Tamam ' ı** seçin. İşlem, ilk verileri hedef konuma gönderir ve ardından sanal makinelerin Delta bilgilerini hedefe çoğaltır.
7. Bildirimlerin ilerleme durumunu izleyin. 

    ![İlerleme bildirimini yeniden koruma ](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![ başarı bildirimi](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, birincil bölgeden ikinciye yük devretti ve VM 'Leri birincil bölgeye yeniden çoğaltmaya başladıysanız. Artık ikincil bölgeden birincil bölgeye yeniden yük devreolursunuz.

> [!div class="nextstepaction"]
> [Birincil bölgeye geri dönme](azure-to-azure-tutorial-failback.md)
