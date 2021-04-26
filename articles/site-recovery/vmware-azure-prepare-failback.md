---
title: VMware VM 'lerini yeniden koruma için hazırlama ve Azure Site Recovery yeniden çalışma
description: Azure Site Recovery ile yük devretmeden sonra VMware VM 'lerinin geri dönmesi için hazırlanma
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: f3e352577d65bda021f886d6612c8c0101922003
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727515"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>VMware VM'leri yeniden koruma ve yeniden çalışma için hazırlama

Şirket içi VMware VM 'lerinin veya fiziksel sunucularının Azure 'a [yük devretmesinin](site-recovery-failover.md) ardından, yük devretme işleminden sonra oluşturulan Azure VM 'leri yeniden koruyorlar, böylece şirket içi siteye tekrar çoğaltılır. Azure 'dan şirket içine çoğaltma yaparak, daha sonra, hazırsanız Azure 'dan şirket içine yük devretme çalıştırarak yeniden yük devreder.

Devam etmeden önce, Azure 'dan şirket içi bir siteye yeniden yük devretme hakkında bu videoya hızlı bir genel bakış alın.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Yeniden koruma/yeniden çalışma bileşenleri

Azure 'dan yeniden koruma ve geri yük devredebilmeniz için birkaç bileşen ve ayar yapmanız gerekir.

| **Bileşen**| **Ayrıntılar** |
| --- | --- |
| **Şirket içi yapılandırma sunucusu** | Şirket içi yapılandırma sunucusunun çalışıyor ve Azure 'a bağlı olması gerekir.<br/><br/> Geri aldığınız VM 'nin yapılandırma sunucusu veritabanında mevcut olması gerekir. Olağanüstü durum yapılandırma sunucusunu etkiliyorsa, yeniden çalışma 'nin çalıştığından emin olmak için aynı IP adresini geri yükleyin.<br/><br/>  Çoğaltılan makinelerin IP adresleri yük devretmede tutulursa, Azure VM makineleri ve yapılandırma sunucusunun yeniden çalışma NIC 'i arasında siteden siteye bağlantı (veya ExpressRoute bağlantısı) yapılmalıdır. Korunan IP adresleri için yapılandırma sunucusunun, kaynak makine bağlantısı için bir tane olmak üzere iki NIC olması ve bir diğeri de Azure yeniden çalışma bağlantısı olması gerekir. Bu, kaynak ve yük devredilen VM 'Ler için alt ağ adres aralıklarının çakışmasını önler. |
| **Azure 'da işlem sunucusu** | Şirket içi sitenizde yük devredebilmeniz için önce Azure 'da bir işlem sunucusuna ihtiyacınız vardır.<br/><br/> İşlem sunucusu korumalı Azure VM 'den verileri alır ve şirket içi siteye gönderir.<br/><br/> İşlem sunucusu ile korumalı VM arasında düşük gecikmeli bir ağ olması gerekir, bu nedenle daha yüksek çoğaltma performansı için işlem sunucusunu Azure 'a dağıtmanızı öneririz.<br/><br/> Kavram kanıtı için, şirket içi işlem sunucusunu ve özel eşleme ile ExpressRoute 'u kullanabilirsiniz.<br/><br/> İşlem sunucusu, yük devredilen VM 'nin bulunduğu Azure ağında olmalıdır. İşlem sunucusu aynı zamanda şirket içi yapılandırma sunucusu ve ana hedef sunucusuyla iletişim kurabilmelidir. |
| **Ana hedef sunucuyu ayır** | Ana hedef sunucu, yeniden çalışma verileri alır ve varsayılan olarak, bir Windows ana hedef sunucusu şirket içi yapılandırma sunucusunda çalışır.<br/><br/> Ana hedef sunucusunda en fazla 60 disk bağlı olabilir. Yeniden başarısız olan VM 'Ler toplam 60 disk sayısına sahip veya çok büyük miktarlarda trafiği geri yüklüyorsanız, yeniden çalışma için ayrı bir ana hedef sunucu oluşturun.<br/><br/> Makineler çoklu VM tutarlılığı için bir çoğaltma grubunda toplandıysa, VM 'Lerin hepsi Windows olmalıdır veya hepsi Linux olmalıdır. Neden? Bir çoğaltma grubundaki tüm VM 'Lerin aynı ana hedef sunucuyu kullanması ve ana hedef sunucunun, çoğaltılan makinelerindekilerle aynı işletim sistemine (aynı veya daha yüksek bir sürümle) sahip olması gerekir.<br/><br/> Ana hedef sunucu, disklerinde anlık görüntü içermemelidir, aksi takdirde yeniden koruma ve yeniden çalışma çalışmaz.<br/><br/> Ana hedefin bir Paravirtual SCSI denetleyicisi olamaz. Denetleyici yalnızca bir LSI Logic Controller olabilir. Bir LSI Logic Controller olmadan yeniden koruma başarısız olur. |
| **Yeniden çalışma çoğaltma ilkesi** | Şirket içi siteye geri yinelemek için bir yeniden çalışma ilkesi gerekir. Bu ilke, Azure 'a bir çoğaltma ilkesi oluşturduğunuzda otomatik olarak oluşturulur.<br/><br/> İlke, yapılandırma sunucusu ile otomatik olarak ilişkilendirilir. Bu, 15 dakikalık bir RPO eşiğine, kurtarma noktası bekletmesini 24 saate ve uygulamayla tutarlı anlık görüntü sıklığını 60 dakika olarak ayarlar. İlke düzenlenemiyor. |
| **Siteden siteye VPN/ExpressRoute özel eşlemesi** | Yeniden koruma ve yeniden çalışma, verileri çoğaltmak için bir siteden siteye VPN bağlantısı veya ExpressRoute özel eşlemesi gerektirir. |


## <a name="ports-for-reprotectionfailback"></a>Yeniden koruma/yeniden çalışma için bağlantı noktaları

Yeniden koruma/yeniden çalışma için bir dizi bağlantı noktası açık olmalıdır. Aşağıdaki grafikte, bağlantı noktaları ve yeniden koruma/yeniden çalışma akışı gösterilmektedir.

![Yük devretme ve yeniden çalışma için bağlantı noktaları](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Azure 'da bir işlem sunucusu dağıtma

1. Azure 'da yeniden çalışma için [bir işlem sunucusu ayarlayın](vmware-azure-set-up-process-server-azure.md) .
2. Azure VM 'lerinin işlem sunucusuna erişebildiğinden emin olun. 
3. Siteden siteye VPN bağlantısı veya ExpressRoute özel eşleme ağının, işlem sunucusundan şirket içi siteye veri göndermesini sağlamak için yeterli bant genişliğine sahip olduğundan emin olun.

## <a name="deploy-a-separate-master-target-server"></a>Ayrı bir ana hedef sunucu dağıtma

1. Ana hedef sunucu [gereksinimlerini ve sınırlamalarını](#reprotectionfailback-components)aklınızda edin.
2. Yeniden korumak ve geri yüklemek istediğiniz VM 'lerin işletim sistemiyle eşleştirmek için bir [Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) veya [Linux](vmware-azure-install-linux-master-target.md) ana hedef sunucusu oluşturun.
3. Ana hedef sunucu için Depolama vMotion ' ı kullandığınızdan emin olun veya yeniden çalışma başarısız olabilir. Diskler kullanılabilir olmadığından VM makinesi başlatılamıyor.
    - Bunu engellemek için, ana hedef sunucuyu vMotion listenizden dışlayın.
    - Bir ana hedef, yeniden korumadan sonra bir Depolama vMotion görevi içeriyorsa, ana hedef sunucuya bağlı korunan VM diskleri vMotion görevinin hedefine geçirilir. Bu işlemi yaptıktan sonra yeniden denemeye çalışırsanız, diskler bulunamadığı için disk kesilmesi başarısız olur. Daha sonra depolama hesaplarınızdaki diskleri bulmak zordur. Bu durumda, bunları el ile bulun ve sanal makineye ekleyin. Bundan sonra şirket içi VM önyüklenebilir.

4. Mevcut Windows ana hedef sunucusuna bir bekletme sürücüsü ekleyin. Yeni bir disk ekleyin ve sürücüyü biçimlendirin. Bekletme sürücüsü, VM 'nin şirket içi siteye geri çoğaltıldığı zaman noktalarını durdurmak için kullanılır. Bu kriterleri aklınızda edin. Bunlar karşılanmazsa, ana hedef sunucusu için sürücü listelenmez:
    - Birim, çoğaltma hedefi gibi başka herhangi bir amaçla kullanılmaz ve kilit modunda değildir.
    - Birim bir önbellek birimi değil. İşlem sunucusu ve ana hedef için özel yükleme birimi, bekletme birimi için uygun değil. İşlem sunucusu ve ana hedef bir birime yüklendiğinde, birim ana hedefin önbellek birimidir.
    - Birimin dosya sistemi türü FAT veya FAT32 değildir.
    - Birim kapasitesi sıfır dışı.
    - Windows için varsayılan bekletme birimi, R birimidir.
    - Linux için varsayılan saklama birimi/mnt/retention

5. Mevcut bir işlem sunucusu kullanıyorsanız bir sürücü ekleyin. Yeni sürücü, son adımdaki gereksinimlere uymalıdır. Bekletme sürücüsü yoksa, portalda seçim açılan listesinde görünmez. Şirket içi ana hedefe bir sürücü ekledikten sonra, sürücünün portalda seçimde görünmesi 15 dakika kadar sürer. Sürücü 15 dakikadan sonra görünmezse yapılandırma sunucusunu yenileyebilirsiniz.
6. Ana hedef sunucuda VMware araçları veya açık VM araçları 'nı yükler. Araçlar olmadan ana hedefin ESXi ana bilgisayarındaki veri depoları algılanamıyor.
7. Diski ayarlayın. Enableuuıd = VMware 'deki ana hedef VM 'nin yapılandırma parametrelerinde true ayarı. Bu satır yoksa, ekleyin. Bu ayar, doğru bir şekilde takılabilmesi için VMDK için tutarlı bir UUID sağlamak üzere gereklidir.
8. VCenter Server erişim gereksinimlerini denetle:
    - Geri aldığınız VM, VMware vCenter Server tarafından yönetilen bir ESXi ana bilgisayarı üzerinde ise, çoğaltılan verileri sanal makinenin diskine yazmak için ana hedef sunucunun şirket içi VM sanal makine diski (VMDK) dosyasına erişmesi gerekir. Şirket içi VM veri deposunun, okuma/yazma erişimiyle ana hedef ana bilgisayara bağlandığından emin olun.
    - VM, bir VMware vCenter Server tarafından yönetilen bir ESXi konağında değilse, Site Recovery yeniden koruma sırasında yeni bir sanal makine oluşturur. Bu VM, ana hedef sunucu VM 'sini oluşturduğunuz ESXi konağında oluşturulur. İstediğiniz konakta VM oluşturmak için ESXi konağını dikkatle seçin. VM’nin sabit diski, üzerinde ana hedef sunucunun çalıştığı ana bilgisayar tarafından erişilebilen bir veri deposunda olmalıdır.
    - Diğer bir seçenek olan şirket içi VM yeniden çalışma için zaten mevcutsa, yeniden çalışma yapmadan önce onu silmektir. Yeniden çalışma daha sonra ana hedef ESXi ana bilgisayarı ile aynı konakta yeni bir VM oluşturur. Alternatif bir konuma geri döndüğünüzde, veriler aynı veri deposuna ve şirket içi ana hedef sunucusu tarafından kullanılan aynı ESXi ana bilgisayarına kurtarılır.
9. VMware VM 'lerine geri yük devretmek için, makineyi yeniden koruyabilmeniz için önce ana hedef sunucunun çalıştırıldığı ana bilgisayarın bulunmasını tamamlamalısınız.
10. Ana hedef VM 'nin kendisine bağlı en az bir sanal makine dosya sistemi (VMFS) veri deposu olduğunu belirten ESXi konağına bakın. Hiçbir VMFS veri deposu iliştirilmişse, yeniden koruma ayarlarında veri deposu girişi boştur ve devam edemezsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Bir VM 'yi [yeniden koruma](vmware-azure-reprotect.md) .
