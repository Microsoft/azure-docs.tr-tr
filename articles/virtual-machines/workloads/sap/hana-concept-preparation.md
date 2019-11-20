---
title: Azure 'da SAP HANA olağanüstü durum kurtarma ilkeleri ve hazırlığı (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA olağanüstü durum kurtarma ilkeleri ve hazırlığı (büyük örnekler)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d52f871de75a7f7d34016b040e44d6f1623fd8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101252"
---
# <a name="disaster-recovery-principles"></a>Olağanüstü durum kurtarma ilkeleri

HANA büyük örnekler, farklı Azure bölgelerindeki HANA büyük örnek damgaları arasında bir olağanüstü durum kurtarma işlevselliği sunar. Örneğin, Azure 'un ABD Batı bölgesinde HANA büyük örnek birimleri dağıtırsanız, ABD Doğu bölgesindeki HANA büyük örnek birimlerini olağanüstü durum kurtarma birimleri olarak kullanabilirsiniz. Daha önce belirtildiği gibi, olağanüstü durum kurtarma, DR bölgesindeki başka bir HANA büyük örnek birimi için ödeme yapmanızı gerektirdiğinden otomatik olarak yapılandırılmaz. Olağanüstü durum kurtarma kurulumu, ölçek artırma ve genişleme kurulumları için de çalışır. 

Şimdiye kadar dağıtılan senaryolarda, müşteriler, yüklü bir HANA örneği kullanan üretim dışı sistemleri çalıştırmak için DR bölgesindeki birimi kullanır. HANA büyük örnek birimi, üretim amaçları için kullanılan SKU ile aynı SKU 'da olmalıdır. Aşağıdaki görüntüde, Azure üretim bölgesindeki sunucu birimi ile olağanüstü durum kurtarma bölgesi arasındaki disk yapılandırmasının nasıl göründüğü gösterilmektedir:

![Disk bakış noktasından DR kurulum yapılandırması](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Bu genel bakış grafiğinde gösterildiği gibi, ikinci bir disk birimi kümesi sipariş etmeniz gerekir. Hedef disk birimleri, olağanüstü durum kurtarma birimlerindeki üretim örneği için üretim birimleriyle aynı boyutlardır. Bu disk birimleri, olağanüstü durum kurtarma sitesindeki HANA büyük örnek sunucusu birimiyle ilişkilendirilir. Aşağıdaki birimler, üretim bölgesinden DR sitesine çoğaltılır:

- /Hana/Data
- /Hana/logbackups 
- /Hana/Shared (/usr/SAP içerir)

Bu birimlerden geri yükleme işleminin tamamlanması için SAP HANA işlem günlüğü gerekli olmadığından,/Hana/log birimi çoğaltılmaz. 

Sunulan olağanüstü durum kurtarma işlevinin temeli, HANA büyük örnek altyapısının sunduğu depolama çoğaltma işlevidir. Depolama tarafında kullanılan işlevsellik, depolama biriminde değişiklikler olduğu için zaman uyumsuz olarak çoğaltılan değişikliklerin sabit bir akışı değildir. Bunun yerine, bu birimlerin anlık görüntülerinin düzenli olarak oluşturulduğu olguyu temel alan bir mekanizmadır. Zaten çoğaltılan bir anlık görüntü ve henüz çoğaltılmamış yeni bir anlık görüntü arasındaki Delta, daha sonra olağanüstü durum kurtarma sitesine hedef disk birimlerine aktarılır.  Bu anlık görüntüler birimlerde depolanır ve bir olağanüstü durum kurtarma yük devretmesi varsa, bu birimlerde geri yüklenmesi gerekir.  

Birimin tüm verilerinin ilk aktarımı, veri miktarı, anlık görüntüler arasında değişimleri 'tan daha küçük hale gelmelidir. Sonuç olarak, DR sitesindeki birimler üretim sitesinde gerçekleştirilen birim anlık görüntülerinin her birini içerir. Son olarak, bu DR sistemini kullanarak, kayıp verileri kurtarmak için üretim sistemini geri almadan önceki bir duruma sahip olabilirsiniz.

Tek bir HANA büyük örnek biriminde birden çok bağımsız SAP HANA örneğine sahip bir MCOD dağıtımı varsa, tüm SAP HANA örneklerinin DR tarafında çoğaltılan depolamayı elde etmek beklenmektedir.

HANA sistem çoğaltmasını üretim sitenizde yüksek kullanılabilirliğe sahip bir işlev olarak kullandığınız ve DR sitesi için depolama tabanlı çoğaltma kullandığınız durumlarda, birincil siteden DR örneğine olan düğümlerin her iki düğümün birimi çoğaltılır. Hem birincil hem de ikincil bilgisayardan DR 'ye çoğaltma sağlamak için DR sitesinde ek depolama alanı (birincil düğümle aynı boyut) satın almalısınız. 



>[!NOTE]
>HANA büyük örnek depolama çoğaltma işlevi, depolama anlık görüntülerini yansıtmakta ve çoğaltmakta. Bu makalenin yedekleme ve geri yükleme bölümünde tanıtılan depolama anlık görüntülerini gerçekleştirmezseniz, olağanüstü durum kurtarma sitesine herhangi bir çoğaltma yapılamaz. Depolama anlık görüntüsü yürütme, olağanüstü durum kurtarma sitesine depolama çoğaltması için bir önkoşuldur.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Olağanüstü durum kurtarma senaryosunun hazırlanması
Bu senaryoda, üretim Azure bölgesindeki HANA büyük örneklerde çalışan bir üretim sisteminiz vardır. Aşağıdaki adımlarda, bu HANA sisteminin SID 'sinin "PRD" olduğunu ve DR Azure bölgesindeki HANA büyük örneklerinde çalışan bir üretim dışı sisteminiz olduğunu varsayalım. İkincisi için, SID 'sinin "TST" olduğunu varsayalım. Aşağıdaki görüntüde bu yapılandırma gösterilmektedir:

![DR kurulumu 'nu başlatma](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Sunucu örneği daha önce ek depolama birimi kümesi ile sıralanmışsa, Azure Service Management üzerindeki SAP HANA, ek birim kümesini, TST 'yi çalıştırdığınız HANA büyük örnek birimine üretim çoğaltması için hedef olarak ekler HANA örneği. Bu amaçla, üretim HANA örneğinizin SID 'sini sağlamanız gerekir. Azure hizmet yönetimi üzerindeki SAP HANA bu birimlerin eklenmesini onayladıktan sonra, bu birimleri HANA büyük örnek birimine bağlamanız gerekir.

![DR kurulumu sonraki adım](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Sonraki adım, ikinci SAP HANA örneğini, TST HANA örneğini çalıştırdığınız DR Azure bölgesindeki HANA büyük örnek birimine yüklemenize yöneliktir. Yeni yüklenen SAP HANA örneğinin aynı SID 'ye sahip olması gerekir. Oluşturulan kullanıcıların, üretim örneği ile aynı UID ve grup KIMLIĞINE sahip olması gerekir. Ayrıntılar için [yedekleme ve geri yükleme](hana-backup-restore.md) konusunu okuyun. Yükleme başarılı olursa şunları yapmanız gerekir:

- [Yedekleme ve geri yükleme](hana-backup-restore.md)bölümünde açıklanan depolama anlık görüntüsü hazırlığının 2. adımını yürütün.
- Henüz yapmadıysanız, HANA büyük örnek birimi DR birimi için ortak anahtar oluşturun. [Yedekleme ve geri yükleme](hana-backup-restore.md)bölümünde açıklanan depolama anlık görüntüsü hazırlığının 3. adımına bakın.
- *Hanabackupcustomerdetails. txt dosyasını* yeni Hana örneğiyle koruyun ve depolamanın bağlantısının düzgün çalışıp çalışmadığını test edin.  
- DR Azure bölgesindeki HANA büyük örnek biriminde yeni yüklenen SAP HANA örneğini durdurun.
- Bu PRD birimlerini çıkarın ve Azure hizmet yönetiminde SAP HANA başvurun. Depolama çoğaltma hedefi olarak çalışırken erişilebilir olmadıkları için birimler birime bağlı kalamazlar.  

![Çoğaltma kurulmadan önce DR Kurulum adımı](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

İşlemler ekibi, üretim Azure bölgesindeki PRD birimleri ve DR Azure bölgesindeki PRD birimleri arasındaki çoğaltma ilişkisini belirler.

>[!IMPORTANT]
>Yinelenen SAP HANA veritabanının olağanüstü durum kurtarma sitesinde tutarlı bir duruma geri yüklenmesi gerekli olmadığından,/Hana/log birimi çoğaltılmaz.

Sonra, olağanüstü durum durumunda RTO ve RPO 'nize ulaşmak için depolama anlık görüntü yedekleme zamanlamasını ayarlayın veya ayarlayın. Kurtarma noktası hedefini en aza indirmek için, HANA büyük örnek hizmetinde aşağıdaki çoğaltma aralıklarını ayarlayın:
- Birleşik anlık görüntü (Snapshot Type **Hana**) tarafından kapsanan birimler için, her 15 dakikada bir, olağanüstü durum kurtarma sitesindeki eşdeğer depolama birimi hedeflerine yineleme olarak ayarlanır.
- İşlem günlüğü yedekleme birimi (anlık görüntü türü **günlükleri**) için, olağanüstü durum kurtarma sitesindeki her 3 dakikada bir, aynı depolama birimi hedeflerine Yinele olarak ayarlayın.

Kurtarma noktası hedefini en aza indirmek için aşağıdakileri ayarlayın:
- **Hana** türü bir depolama anlık görüntüsü gerçekleştirin (bkz. "adım 7: Her 30 dakikada bir, 1 saate kadar anlık görüntü gerçekleştirin ").
- Her 5 dakikada bir SAP HANA işlem günlüğü yedeklemesi gerçekleştirin.
- Her 5-15 dakikada bir **günlük** türü depolama anlık görüntüsü gerçekleştirin. Bu Aralık döneminde 15-25 dakikalık bir RPO 'SU elde edersiniz.

Bu kurulumla, işlem günlüğü yedeklemeleri, depolama anlık görüntüleri ve HANA işlem günlüğü yedekleme birimi ve/Hana/Data 'ın çoğaltılması ve/Hana/Shared (/usr/SAP), bu grafikte gösterilen verilere benzeyebilir:

 ![Bir zaman eksenindeki bir işlem günlüğü yedekleme anlık görüntüsü ve yaslama görüntüsü arasındaki ilişki](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Olağanüstü durum kurtarma durumunda daha iyi bir RPO elde etmek için, Azure 'daki SAP HANA HANA işlem günlüğü yedeklemelerini diğer Azure bölgesine kopyalayabilirsiniz. Bu ek RPO azaltmasına ulaşmak için aşağıdaki adımları gerçekleştirin:

1. HANA işlem günlüğünü/Hana/logbackupiçin mümkün olduğunca sık yedekleyin.
1. NFS paylaşımında barındırılan Azure sanal makinelerine işlem günlüğü yedeklerini kopyalamak için rsync kullanın. VM 'Ler Azure sanal ağlarında ve DR bölgelerinde bulunur. Üretim HANA büyük örneklerini Azure 'a bağlayan bağlantı hattına her iki Azure sanal ağını de bağlamanız gerekir. [Hana büyük örneklerle olağanüstü durum kurtarma Için ağ hususları](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) bölümünde grafiklere bakın. 
1. İşlem günlüğü yedeklemelerini, sanal makinenin NFS tarafından içe aktarılmış depolama alanına bağlı bölgede saklayın.
1. Olağanüstü durum yük devretme durumunda, olağanüstü durum kurtarma sitesindeki NFS paylaşımında daha önce gerçekleştirilen işlem günlüğü yedeklemelerini daha fazla alan,/Hana/logbackups birimi üzerinde bulduğunuz işlem günlüğü yedeklerini tamamlar. 
1. DR bölgesine kaydedilebilecek en son yedeklemeye geri yüklemek için bir işlem günlüğü yedeklemesi başlatın.

HANA büyük örnek işlemleri çoğaltma ilişkisi kurulumunu doğrulamaktadır ve yürütme depolama anlık görüntü yedeklemelerini başlattığınızda, veri çoğaltma başlar.

![Çoğaltma kurulmadan önce DR Kurulum adımı](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Çoğaltma ilerledikçe, DR Azure bölgelerindeki PRD birimlerindeki anlık görüntüler geri yüklenmez. Bunlar yalnızca depolanır. Birimler böyle bir duruma bağlanmışsa, PRD SAP HANA örneği DR Azure bölgesindeki sunucu birimine yüklendikten sonra bu birimleri bağladığınız durumu temsil eder. Bunlar, henüz geri yüklenmemiş depolama yedeklemelerini de temsil eder.

Yük devretme işlemi varsa, en son depolama anlık görüntüsü yerine daha eski bir depolama anlık görüntüsüne geri yüklemeyi de tercih edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Olağanüstü durum kurtarma yük devretme yordamına](hana-failover-procedure.md)bakın.
