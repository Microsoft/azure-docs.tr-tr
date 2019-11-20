---
title: Azure 'da SAP HANA için bir olağanüstü durum sitesine HANA yük devretme yordamı (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA için bir olağanüstü durum kurtarma sitesine yük devretme gerçekleştirme (büyük örnekler)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2c8483948deae41edbe3922dc77361ba2c58a94
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099859"
---
# <a name="disaster-recovery-failover-procedure"></a>Olağanüstü durum kurtarma yük devretme yordamı


>[!IMPORTANT]
>Bu makale SAP HANA yönetimi belgelerinin veya SAP notlarının yerini almaz. Özellikle yedekleme, geri yükleme, yüksek kullanılabilirlik ve olağanüstü durum kurtarma (DR) için SAP HANA yönetim ve işlemlerde, uzmanlığın iyi bir şekilde anlaşılmasını umduk. Bu makalede SAP HANA Studio 'daki ekran görüntüleri gösterilir. İçerik, yapı ve SAP yönetim araçlarının ekranlarının doğası ve araçların kendisi SAP HANA sürümden sürüme değişebilir.

Bir DR sitesine yük devretmek için göz önünde bulundurmanız gereken iki durum vardır:

- Verilerin en son durumuna geri dönmek için SAP HANA veritabanına ihtiyacınız vardır. Bu durumda, Microsoft 'a başvurmanız gerekmeden yük devretmeyi gerçekleştirebileceğiniz bir self servis betiğine sahip olabilirsiniz. Yeniden çalışma için Microsoft ile çalışmanız gerekir.
- En son çoğaltılan anlık görüntü olmayan bir depolama anlık görüntüsüne geri yüklemek istiyorsunuz. Bu durumda, Microsoft ile çalışmanız gerekir. 

>[!NOTE]
>Aşağıdaki adımlar, DR birimini temsil eden HANA büyük örnek biriminde yapılmalıdır. 
 
En son çoğaltılan depolama anlık görüntülerine geri yüklemek için, [Azure 'daki SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)'nda "tam Dr yük devretmesi-azure_hana_dr_failover" bölümündeki adımları izleyin. 

Yük devretmesinin birden fazla SAP HANA olması istiyorsanız, azure_hana_dr_failover komutunu birkaç kez çalıştırın. İstendiğinde, yük devretmek ve geri yüklemek istediğiniz SAP HANA SID 'sini girin. 


Ayrıca, gerçek çoğaltma ilişkisini etkilemeden DR yük devretmesini test edebilirsiniz. Yük devretme testi gerçekleştirmek için, [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)'nda "test Dr yük devretmesi-azure_hana_test_dr_failover" bölümündeki adımları izleyin. 

>[!IMPORTANT]
>**Yük devretmeyi test etme**IŞLEMI aracılığıyla Dr sitesinde oluşturduğunuz örnek üzerinde herhangi bir üretim işlemini çalıştırmayın. Komut azure_hana_test_dr_failover, birincil siteyle ilişkisi olmayan bir birim kümesi oluşturur. Sonuç olarak, birincil siteye geri eşitleme mümkün *değildir* . 

Sınanacak birden çok SAP HANA örneğine sahip olmak istiyorsanız, betiği birkaç kez çalıştırın. İstendiğinde, yük devretme için sınamak istediğiniz örneğin SAP HANA SID 'sini girin. 

>[!NOTE]
>Daha önce silinen bazı verileri kurtarma için DR sitesine yük devretmek ve DR birimlerinin daha önceki bir anlık görüntüye ayarlanmasının gerekli olması gerekiyorsa, bu yordam geçerli olur. 

1. Çalıştırmakta olduğunuz HANA büyük örneklerinin olağanüstü durum kurtarma biriminde, HANA 'nın üretim dışı örneğini kapatın. Bir etkinliği olmayan HANA üretim örneği önceden yüklenir.
1. SAP HANA işlem olmadığından emin olun. Bu denetim için aşağıdaki komutu kullanın:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      Çıktı, bir durdurulmuş durumda **hdbdaemon** işlemini ve çalışan ya da başlatılmış bir durumda başka bir hana işlemini göstermemelidir.
1. Olağanüstü durum kurtarma sitesinin geri yüklenmesini istediğiniz anlık görüntü adını veya SAP HANA yedekleme KIMLIĞINI belirleme. Gerçek olağanüstü durum kurtarma durumlarında, bu anlık görüntü genellikle en son anlık görüntüdür. Kayıp verileri kurtarmanız gerekiyorsa, önceki bir anlık görüntü seçin.
1. Yüksek öncelikli destek isteğiyle Azure desteğine başvurun. Anlık görüntünün adını ve tarihini veya DR sitesindeki HANA yedekleme KIMLIĞINI kullanarak bu anlık görüntünün geri yüklenmesini isteyin. Varsayılan olarak, işlem tarafı yalnızca/Hana/Data birimini geri yükler. /Hana/logbackups birimlerine da sahip olmak istiyorsanız, özel olarak bu durumu belirlemeniz gerekir. */Hana/Shared birimini geri yüklemeyin.* Bunun yerine, PRD için/Hana/Shared birimini kaldırdıktan sonra **. Snapshot** dizininden ve alt dizinlerinde Global. ini gibi belirli dosyaları seçin. 

   İşlemler tarafında aşağıdaki adımlar oluşur:

   a. Üretim biriminden olağanüstü durum kurtarma birimlerine anlık görüntülerin çoğaltılması durdurulur. Üretim sitesindeki bir kesinti, olağanüstü durum kurtarma yordamını gerçekleştirmeniz gereken neden olursa bu kesinti zaten olmuş olabilir.
   
   b. Seçtiğiniz yedekleme KIMLIĞINE sahip depolama anlık görüntüsü adı veya anlık görüntü, olağanüstü durum kurtarma birimlerine geri yüklenir.
   
   c. Geri yüklemeden sonra, olağanüstü durum kurtarma birimleri olağanüstü durum kurtarma bölgesindeki HANA büyük örnek birimlerine bağlanabilir.
      
1. Olağanüstü durum kurtarma birimlerini olağanüstü durum kurtarma sitesindeki HANA büyük örnek birimine bağlayın. 
1. Etkinliği SAP HANA üretim örneğini başlatın.
1. RPO süresini azaltmak için işlem günlüğü yedekleme günlüklerini kopyalamayı seçerseniz, işlem günlüğü yedeklerini yeni bağlanan DR/Hana/logbackups diziniyle birleştirin. Mevcut yedeklemelerin üzerine yazma. Depolama anlık görüntüsünün en son çoğaltmasıyla çoğaltılmamış daha yeni yedeklemeler kopyalayın.
1. Ayrıca, DR Azure bölgesindeki/hana/shared/PRD birimine çoğaltılmamış olan anlık görüntülerden tek dosyaları geri yükleyebilirsiniz.

Aşağıdaki adımlarda, geri yüklenen depolama anlık görüntüsüne ve kullanılabilir işlem günlüğü yedeklemelerine göre SAP HANA üretim örneğinin nasıl kurtarılacağı gösterilmektedir.

1. Yedekleme konumunu, SAP HANA Studio 'Yu kullanarak **/Hana/logbackups** olarak değiştirin.

   ![DR kurtarması için yedekleme konumunu değiştirme](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA yedekleme dosyası konumlarını tarar ve geri yüklenecek en son işlem günlüğü yedeklemesini önerir. Tarama, aşağıdaki gibi bir ekran görünene kadar birkaç dakika sürebilir:

   ![DR kurtarması için işlem günlüğü yedeklemelerinin listesi](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Varsayılan ayarlardan bazılarını ayarlayın:

      - **Delta yedeklemeleri kullan**seçimini kaldırın.
      - **Günlük alanını Başlat**' ı seçin.

   ![Başlatma günlüğü alanını ayarla](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. **Son**’u seçin.

   ![DR geri yüklemeyi tamamlama](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Burada gösterildiği gibi bir ilerleme penceresi görünmelidir. Örneğin, üç düğümlü bir genişleme SAP HANA yapılandırmasının olağanüstü durum kurtarma geri yükleme işlemi olduğunu aklınızda bulundurun.

![Geri yükleme ilerleme durumu](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Geri yükleme işlemi **son** ekranda yanıt vermemeye devam ediyorsa ve ilerleme durumunu göstermezse, çalışan düğümlerindeki tüm SAP HANA örneklerinin çalıştığını onaylayın. Gerekirse, SAP HANA örneklerini el ile başlatın.


## <a name="failback-from-a-dr-to-a-production-site"></a>Bir DR 'den üretim sitesine yeniden çalışma
Bir DR 'den üretim sitesine yeniden yük devreolursunuz. Olağanüstü durum kurtarma sitesine yük devretmenin, kayıp verileri kurtarma gereksiniminizi değil, üretim Azure bölgesindeki sorunlardan kaynaklandığını bir senaryoya bakalım. 

Olağanüstü durum kurtarma sitesinde SAP üretim iş yükünüzü bir süredir çalıştırıyorsunuz. Üretim sitesindeki sorunlar çözümlendikten sonra üretim sitenize yeniden yük devretmek istiyorsunuz. Verileri kaybedeceğinizi, üretim sitesine geri döndüğünüzde birkaç adım ve Azure Operations Team SAP HANA ile birlikte kapatma işlemi için bir adım daha vardır. Operations ekibinin, sorunlar çözümlendikten sonra üretim sitesiyle yeniden eşitleme başlatması için tetiklenmesi en iyisidir.

Şu adımları uygulayın:

1. Azure işlemler ekibinin SAP HANA, üretim depolama birimlerini, artık üretim durumunu temsil eden olağanüstü durum kurtarma depolama birimlerinden eşitlemeye yönelik tetikleyiciyi alır. Bu durumda, üretim sitesindeki HANA büyük örnek birimi kapatılır.
1. Azure işlemler ekibi üzerindeki SAP HANA çoğaltmayı izler ve sizi bilgilendirmeye başlamadan önce yakalandığından emin olur.
1. Olağanüstü durum kurtarma sitesinde üretim HANA örneğini kullanan uygulamaları kapaolursunuz. Daha sonra bir HANA işlem günlüğü yedeklemesi gerçekleştirirsiniz. Daha sonra, olağanüstü durum kurtarma sitesindeki HANA büyük örnek birimlerinde çalışan HANA örneğini durdurabilirsiniz.
1. Olağanüstü durum kurtarma sitesindeki HANA büyük örnek biriminde çalışan HANA örneği kapatıldıktan sonra, işlem ekibi disk birimlerini yeniden el ile eşitler.
1. Azure işlemler ekibinin SAP HANA, üretim sitesindeki HANA büyük örnek birimini yeniden başlatır. BT, size ait olurlar. SAP HANA örneğinin, HANA büyük örnek biriminin başlangıç saatinde bir kapalı durumda olduğundan emin olun.
1. Daha önce olağanüstü durum kurtarma sitesine yük devretmeye çalıştığınızda yaptığınız veritabanı geri yükleme adımlarını gerçekleştirin.

## <a name="monitor-disaster-recovery-replication"></a>Olağanüstü durum kurtarma çoğaltmasını izleme

Depolama çoğaltma ilerleme durumunun durumunu izlemek için betiği `azure_hana_replication_status`çalıştırın. Bu komutun beklenen şekilde çalışması için olağanüstü durum kurtarma konumunda çalışan bir birimden çalıştırılması gerekir. Bu komut, çoğaltmanın etkin olup olmadığı bağımsız olarak işe yarar. Komut, olağanüstü durum kurtarma konumunda kiracınızın her HANA büyük örnek birimi için çalıştırılabilir. Önyükleme birimiyle ilgili ayrıntıları almak için kullanılamaz. 

Komut ve çıktısı hakkında daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)'NDA "Dr çoğaltma durumunu alma-azure_hana_replication_status".


## <a name="next-steps"></a>Sonraki adımlar
- Bkz. [Hana tarafında izleyici ve sorun giderme](hana-monitor-troubleshoot.md).
