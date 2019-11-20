---
title: VMware VM 'lerini/fiziksel sunucuları Azure Site Recovery ile şirket içi bir siteye yeniden koruma
description: VMware VM 'Leri ve fiziksel sunucular için olağanüstü durum kurtarma sırasında Azure 'a yük devretmeden sonra, Azure 'dan şirket içi siteye geri dönme işlemini öğrenin.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: mayg
ms.openlocfilehash: f3d5f38d940b99c6a74d784f174c91d4127353dc
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961339"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Azure 'a yük devretmeden sonra makineleri şirket içi bir siteye yeniden koruma ve geri dönme

Şirket içi VMware VM 'lerini veya fiziksel sunucuları Azure 'a [yük devrettikten](site-recovery-failover.md) sonra, şirket içi sitenize geri dönme aşamasında ilk adım yük devretme sırasında oluşturulan Azure VM 'lerinin yeniden korunmasında olur. Bu makalede bunun nasıl yapılacağı açıklanır. 

Hızlı bir genel bakış için, Azure 'dan şirket içi bir siteye yük devretme hakkında aşağıdaki videoları izleyin.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Başlamadan önce

Sanal makinelerinizi oluşturmak için bir şablon kullandıysanız, her bir sanal makinenin diskler için kendi UUID 'sine sahip olduğundan emin olun. Şirket içi sanal makinenin UUID 'SI ana hedefin UUID 'SI ile çakışıyor, her ikisi de aynı şablondan oluşturulduğundan, yeniden koruma başarısız olur. Aynı şablondan oluşturulmamış başka bir ana hedef dağıtın. Aşağıdaki bilgileri not edin:
- Alternatif bir vCenter 'a geri dönme yapmaya çalışıyorsanız, yeni vCenter ve ana hedef sunucunuzun bulunduğundan emin olun. Tipik bir belirti, veri depolarının erişilebilir olmadığı veya **yeniden koru** iletişim kutusunda görünmeyen bir belirtidir.
- Şirket içi uygulamasına geri yinelemek için bir yeniden çalışma ilkesi gerekir. Bu ilke, bir iletme yönü ilkesi oluşturduğunuzda otomatik olarak oluşturulur. Aşağıdaki bilgileri not edin:
    - Bu ilke, oluşturma sırasında yapılandırma sunucusu ile otomatik olarak ilişkilendirilir.
    - Bu ilke düzenlenebilir değildir.
    - İlkenin ayarlanan değerleri şunlardır (RPO eşiği = 15 dakika, kurtarma noktası bekletme = 24 saat, uygulama tutarlılığı anlık görüntü sıklığı = 60 dakika).
- Yeniden koruma ve yeniden çalışma sırasında, şirket içi yapılandırma sunucusunun çalışıyor ve bağlı olması gerekir.
- Bir vCenter sunucusu, geri dönecek sanal makineleri yönetirse, vCenter sunucularındaki VM 'Leri bulmak için [gerekli izinlere](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) sahip olduğunuzdan emin olun.
- Yeniden korumadan önce ana hedef sunucuda anlık görüntüleri silin. Şirket içi ana hedefte veya sanal makinede anlık görüntüler varsa, yeniden koruma başarısız olur. Sanal makinedeki anlık görüntüler, yeniden koruma işleri sırasında otomatik olarak birleştirilir.
- Bir çoğaltma grubunun tüm sanal makineleri aynı işletim sistemi türünde olmalıdır (tüm Windows veya tüm Linux). Karma işletim sistemlerine sahip bir çoğaltma grubu, şu anda şirket içinde yeniden koruma ve yeniden çalışma için desteklenmez. Bunun nedeni, ana hedefin sanal makineyle aynı işletim sisteminde olması gerekir. Bir çoğaltma grubunun tüm sanal makineleri aynı ana hedefe sahip olmalıdır. 
- Ana hedefin, çoğaltılan öğelerin işletim sistemi sürümlerinden aynı veya daha yüksek işletim sistemi sürümüne sahip olması gerekir.
- Bir yapılandırma sunucusu, yeniden başarısız olduğunuzda şirket içi olarak gereklidir. Yeniden çalışma sırasında, sanal makinenin yapılandırma sunucusu veritabanında mevcut olması gerekir. Aksi takdirde, yeniden çalışma başarısız olur. Yapılandırma sunucunuz için düzenli olarak zamanlanmış yedeklemeler yaptığınızdan emin olun. Bir olağanüstü durum varsa, yeniden çalışma çalışması için sunucuyu aynı IP adresiyle geri yükleyin. 
- Yeniden koruma ve yeniden çalışma, verileri çoğaltmak için siteden siteye (S2S) VPN veya ExpressRoute özel eşlemesi gerektirir. Azure 'daki başarısız olan sanal makinelerin şirket içi yapılandırma sunucusuna ulaşabilmesi için ağı sağlayın. Yük devredilen sanal makinelerin Azure ağında bir işlem sunucusu dağıtmanız gerekir. Bu işlem sunucusu, ayrıca şirket içi yapılandırma sunucusu ve ana hedef sunucu ile iletişim kurabilmelidir.
- Çoğaltılan öğelerin IP adreslerinin yük devretmede korunması durumunda, Azure sanal makineler ile yapılandırma sunucusunun yeniden çalışma NIC 'i arasında S2S veya ExpressRoute bağlantısı yapılmalıdır. IP adresi saklama 'nın, kaynak makine bağlantısı için ve diğeri de Azure yeniden çalışma bağlantısı için iki NIC 'ye sahip olmasını gerektirdiğini unutmayın. Bunun nedeni, kaynak ve yük devredilen sanal makinelerin alt ağ adres aralıklarının çakışmasını önmaktır.
- Yük devretme ve yeniden çalışma için aşağıdaki bağlantı noktalarını açmış olduğunuzdan emin olun:

    ![Yük devretme ve yeniden çalışma için bağlantı noktaları](./media/vmware-azure-reprotect/failover-failback.png)

- [Bağlantı noktaları ve URL beyaz listesinin tüm önkoşullarını](vmware-azure-deploy-configuration-server.md#prerequisites)okuyun.

## <a name="deploy-a-process-server-in-azure"></a>Azure 'da bir işlem sunucusu dağıtma

Şirket içi sitenize geri yük devredebilmeniz için önce Azure 'da bir işlem sunucusu gerekir:

- İşlem sunucusu, Azure 'daki korumalı sanal makineden verileri alır ve sonra verileri şirket içi siteye gönderir.
- İşlem sunucusu ve korumalı sanal makine arasında düşük gecikmeli bir ağ gerekir. Bu nedenle, Azure 'da bir işlem sunucusu dağıtmanız önerilir. İşlem sunucusu, çoğaltılan sanal makineye daha yakınsa (Azure 'daki yük devredilen makine) çoğaltma performansı daha yüksektir. 
- Kavram kanıtı için, şirket içi işlem sunucusunu ve ExpressRoute 'u özel eşleme ile kullanabilirsiniz.

Azure 'da bir işlem sunucusu dağıtmak için:

1. Azure 'da bir işlem sunucusu dağıtmanız gerekiyorsa, yeniden [çalışma Için Azure 'da bir işlem sunucusu ayarlama](vmware-azure-set-up-process-server-azure.md)bölümüne bakın.
2. Azure VM 'Leri, çoğaltma verilerini işlem sunucusuna gönderir. Azure VM 'lerinin işlem sunucusuna erişebilmesi için ağları yapılandırın.
3. Azure 'dan şirket içine çoğaltmanın yalnızca S2S VPN üzerinden veya ExpressRoute ağınızın özel eşlemesi üzerinden gerçekleşemeyeceğini unutmayın. Bu ağ kanalında yeterli bant genişliği olduğundan emin olun.

## <a name="deploy-a-separate-master-target-server"></a>Ayrı bir ana hedef sunucu dağıtma

Ana hedef sunucu yeniden çalışma verilerini alır. Ana hedef sunucu varsayılan olarak şirket için yapılandırma sunucusunda çalışır. Ancak, başarısız olan trafik hacmine bağlı olarak, yeniden çalışma için ayrı bir ana hedef sunucu oluşturmanız gerekebilir. Bunun nasıl oluşturulacağı aşağıda verilmiştir:

* Linux VM 'lerinin yeniden çalışma için [bir Linux ana hedef sunucusu oluşturun](vmware-azure-install-linux-master-target.md) . Bu gereklidir. LVM üzerinde ana hedef sunucunun desteklenmediğini unutmayın.
* İsteğe bağlı olarak, Windows VM yeniden çalışma için ayrı bir ana hedef sunucu oluşturun. Bunu yapmak için Birleşik kurulum 'u yeniden çalıştırın ve bir ana hedef sunucu oluşturmayı seçin. [Daha fazla bilgi edinin](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers). 

Ana hedef sunucu oluşturduktan sonra, aşağıdaki görevleri yapın:

- Sanal makine vCenter sunucusunda şirket içinde mevcutsa, ana hedef sunucunun şirket içi sanal makinenin sanal makine diski (VMDK) dosyasına erişmesi gerekir. Çoğaltılan verileri sanal makinenin diskine yazmak için erişim gerekir. Şirket içi sanal makinenin veri deposunun, okuma/yazma erişimiyle ana hedefin konağına bağlı olduğundan emin olun.
- Sanal makine vCenter sunucusunda şirket içinde yoksa, Site Recovery hizmetinin yeniden koruma sırasında yeni bir sanal makine oluşturması gerekir. Bu sanal makine, üzerinde ana hedefi oluşturduğunuz ESX konağında oluşturulur. Yeniden çalışma sanal makinesinin istediğiniz konakta oluşturulması için ESX konağını dikkatle seçin.
- Ana hedef sunucusu için Depolama vMotion kullanamazsınız. Ana hedef sunucu için Depolama vMotion kullanılması, yeniden çalışma başarısız olmasına neden olabilir. Diskler kullanılabilir olmadığından sanal makine başlatılamıyor. Bunun oluşmasını önlemek için, ana hedef sunucuları vMotion listenizden dışlayın.
- Bir ana hedef, yeniden korumadan sonra bir Depolama vMotion görevi içeriyorsa, ana hedefe bağlı korunan sanal makine diskleri vMotion görevinin hedefine geçirilir. Bu işlemi yaptıktan sonra yeniden denemeye çalışırsanız, diskler bulunamadığı için diskin kesilmesi başarısız olur. Daha sonra depolama hesaplarınızdaki diskleri bulmak zor olur. Bunları el ile bulmanız ve sanal makineye bağlamanız gerekir. Bundan sonra, şirket içi sanal makine önyüklenebilir.
- Mevcut Windows ana hedef sunucunuza bir bekletme sürücüsü ekleyin. Yeni bir disk ekleyin ve sürücüyü biçimlendirin. Bekletme sürücüsü, sanal makinenin şirket içi siteye geri çoğaltıldığı zaman noktalarını durdurmak için kullanılır. Aşağıda bir bekletme sürücüsüne ilişkin bazı ölçütler verilmiştir. Bu ölçütler karşılanmazsa, sürücü ana hedef sunucusu için listelenmez:
    - Birim, çoğaltma hedefi gibi başka herhangi bir amaçla kullanılmaz.
    - Birim kilit modunda değil.
    - Birim bir önbellek birimi değil. Ana hedef yüklemesi bu birimde mevcut olamaz. İşlem sunucusu ve ana hedef için özel yükleme birimi, bekletme birimi için uygun değil. İşlem sunucusu ve ana hedef bir birime yüklendiğinde, birim ana hedefin önbellek birimidir.
    - Birimin dosya sistemi türü FAT veya FAT32 değildir.
    - Birim kapasitesi sıfır dışı.
    - Windows için varsayılan bekletme birimi, R birimidir.
    - Linux için varsayılan saklama birimi/mnt/retention
- Mevcut bir işlem sunucusu/yapılandırma sunucusu makinesi veya bir ölçek veya işlem sunucusu/ana hedef sunucu makinesi kullanıyorsanız yeni bir sürücü eklemeniz gerekir. Yeni sürücünün önceki gereksinimleri karşılaması gerekir. Bekletme sürücüsü yoksa, portalda seçim açılan listesinde görünmez. Şirket içi ana hedefe bir sürücü ekledikten sonra, sürücünün portalda seçimde görünmesi 15 dakika kadar sürer. Ayrıca, sürücü 15 dakikadan sonra görünmezse yapılandırma sunucusunu yenileyebilirsiniz.
- Ana hedef sunucuda VMware araçları veya açık VM araçları 'nı yükler. Araçlar olmadan ana hedefin ESXi ana bilgisayarındaki veri depoları algılanamıyor.
- VMware 'de ana hedef sanal makinesinin yapılandırma parametrelerindeki `disk.EnableUUID=true` ayarını belirleyin. Bu satır yoksa, ekleyin. Bu ayar, doğru bir şekilde takılabilmesi için VMDK için tutarlı bir UUID sağlamak üzere gereklidir.
- Ana hedefin oluşturulduğu ESX konağına, en az bir sanal makine dosya sistemi (VMFS) veri deposu eklenmiş olmalıdır. Hiçbir VMFS veri deposu iliştirilmişse, yeniden koru sayfasındaki **veri deposu** girişi boştur ve devam edemezsiniz.
- Ana hedef sunucu, disklerde anlık görüntülere sahip olamaz. Anlık görüntüler varsa, yeniden koruma ve yeniden çalışma başarısız olur.
- Ana hedefin bir Paravirtual SCSI denetleyicisi olamaz. Denetleyici yalnızca bir LSI Logic Controller olabilir. Bir LSI Logic Controller olmadan yeniden koruma başarısız olur.
- Herhangi bir örnek için, ana hedefe en fazla 60 disk eklenmiş olabilir. Şirket içi ana hedefte yeniden korunan sanal makinelerin sayısı toplam 60 disk sayısına sahipse, ana hedefe yeniden koruma başarısız olur. Yeterli ana hedef disk yuvalarının bulunduğundan emin olun veya ek ana hedef sunucuları dağıtın.
    

## <a name="enable-reprotection"></a>Yeniden korumayı etkinleştir

Azure 'da bir sanal makine önyüklendikten sonra, aracının yapılandırma sunucusuna (15 dakikaya kadar) kaydolması biraz zaman alır. Bu süre boyunca yeniden koruyamazsınız ve bir hata mesajı aracının yüklenmediğini gösterir. Bu durumda, birkaç dakika bekleyin ve sonra yeniden korumayı tekrar deneyin:


1. **Çoğaltılan öğeleri** > **kasa** seçin. Yük devredilen sanal makineye sağ tıklayın ve ardından **yeniden koru**' yı seçin. Ya da, komut düğmelerinden makineyi seçip **yeniden koru**' yı seçin.
2. **Azure 'dan şirket içi** koruma yönünün seçildiğini doğrulayın.
3. **Ana hedef sunucusu** ve **işlem sunucusu**' nda, şirket içi ana hedef sunucusunu ve işlem sunucusunu seçin.  
4. **Veri deposu**için, şirket içi diskleri kurtarmak istediğiniz veri deposunu seçin. Bu seçenek, şirket içi sanal makine silindiğinde ve yeni diskler oluşturmanız gerektiğinde kullanılır. Diskler zaten mevcutsa bu seçenek yoksayılır. Yine de bir değer belirtmeniz gerekir.
5. Bekletme sürücüsünü seçin.
6. Yeniden çalışma ilkesi otomatik olarak seçilir.
7. Yeniden korumaya başlamak için **Tamam ' ı** seçin. Sanal makineyi Azure’dan şirket içi siteye çoğaltan bir iş başlar. İlerlemeyi **işler** sekmesinde izleyebilirsiniz. Yeniden koruma başarılı olduğunda, sanal makine korumalı bir duruma girer.

Aşağıdaki bilgileri not edin:
- Alternatif bir konuma kurtarmak istiyorsanız (Şirket içi sanal makine silindiğinde), ana hedef sunucusu için yapılandırılan bekletme sürücüsünü ve veri deposunu seçin. Şirket içi siteye geri döndüğünüzde, yeniden çalışma koruma planındaki VMware sanal makineleri, ana hedef sunucuyla aynı veri deposunu kullanır. Daha sonra vCenter 'da yeni bir sanal makine oluşturulur.
- Azure 'daki sanal makineyi mevcut bir şirket içi sanal makineye kurtarmak istiyorsanız, şirket içi sanal makinenin veri depolarını, ana hedef sunucunun ESXi konağına okuma/yazma erişimiyle bağlayın.

    ![Yeniden Koru iletişim kutusu](./media/vmware-azure-reprotect/reprotectinputs.png)

- Kurtarma planı düzeyinde de yeniden koruma sağlayabilirsiniz. Bir çoğaltma grubu, yalnızca bir kurtarma planı aracılığıyla yeniden korunabilir. Bir kurtarma planı kullanarak yeniden koruma sağladığınızda, korunan her makinenin değerlerini sağlamanız gerekir.
- Çoğaltma grubunu yeniden korumak için aynı hedef sunucuyu kullanın. Bir çoğaltma grubunu yeniden korumak için farklı bir ana hedef sunucu kullanıyorsanız, sunucu ortak bir zaman noktası sağlayamaz.
- Şirket içi sanal makine, yeniden koruma sırasında kapalıdır. Bu işlem, çoğaltma sırasında veri tutarlığını sağlar. Yeniden koruma bittikten sonra sanal makineyi açık duruma getirin.



## <a name="common-issues"></a>Genel sorunlar

- Salt okuma Kullanıcı vCenter bulmayı ve sanal makineleri koruyorsa, koruma başarılı olur ve yük devretme işlemi gerçekleştirilir. Yeniden koruma sırasında, veri depoları bulunamadığı için yük devretme başarısız olur. Belirti, veri depolarının yeniden koruma sırasında listelenmemiştir. Bu sorunu çözmek için vCenter kimlik bilgilerini izinleri olan uygun bir hesapla güncelleştirebilir ve ardından işi yeniden deneyebilirsiniz. 
- Bir Linux sanal makinesini kapatıp şirket içinde çalıştırdığınızda, ağ yöneticisi paketinin makineden kaldırıldığını görebilirsiniz. Bu kaldırma işlemi, sanal makine Azure 'da kurtarılırken ağ yöneticisi paketinin kaldırıldığı için oluşur.
- Bir Linux sanal makinesi statik IP adresiyle yapılandırıldığında ve Azure 'a yük devretildiği zaman, IP adresi DHCP 'den alınır. Şirket içinde yük devrediğinizde, sanal makine IP adresini almak için DHCP kullanmaya devam eder. Makinede el ile oturum açın ve gerekirse IP adresini bir statik adrese geri ayarlayın. Bir Windows sanal makinesi, statik IP adresini yeniden alabilir.
- ESXi 5,5 Free Edition veya vSphere 6 hiper yönetici ücretsiz sürümünü kullanıyorsanız, yük devretme başarılı olur ancak yeniden çalışma başarılı olmaz. Yeniden çalışmayı etkinleştirmek için programın değerlendirme lisansına yükseltin.
- Yapılandırma sunucusuna işlem sunucusundan ulaşamadıysanız, bağlantı noktası 443 üzerindeki yapılandırma sunucusuna bağlantıyı denetlemek için Telnet kullanın. Ayrıca, işlem sunucusundan yapılandırma sunucusuna ping göndermeye da çalışırsınız. Ayrıca, bir işlem sunucusu yapılandırma sunucusuna bağlıyken sinyal içermelidir.
- Fiziksel bir şirket içi sunucu olarak korunan bir Windows Server 2008 R2 SP1 sunucusu, Azure 'dan şirket içi bir siteye yeniden başarısız olabilir.
- Aşağıdaki koşullarda yeniden başarısız olabilirsiniz:
    - Makineleri Azure 'a geçirdiniz. [Daha fazla bilgi edinin](migrate-overview.md#what-do-we-mean-by-migration).
    - Bir VM 'yi başka bir kaynak grubuna taşımış olursunuz.
    - Azure VM 'yi silmiş olursunuz.
    - VM 'nin korumasını devre dışı bırakmış olursunuz.
    - VM 'yi Azure 'da el ile oluşturdunuz. Makinenin ilk olarak şirket içinde korunması ve yeniden korumadan önce Azure 'a yük devretmeli olması gerekir.
    - Yalnızca bir ESXi konağına başarısız olabilirsiniz. VMware VM 'lerini veya fiziksel sunucuları Hyper-V konaklarına, fiziksel makinelere veya VMware iş istasyonlarına yeniden çalıştıramazsınız.


## <a name="next-steps"></a>Sonraki adımlar

Sanal makine korumalı bir duruma girdikten sonra yeniden [çalışma başlatabilirsiniz](vmware-azure-failback.md). Yeniden çalışma, Azure 'daki sanal makineyi kapatır ve şirket içi sanal makineyi önyükler. Uygulama için bazı kapalı kalma süresi bekleniyor. Uygulamanın kapalı kalma süresini kabul edebildiği durumlarda yeniden çalışma için bir süre seçin.


