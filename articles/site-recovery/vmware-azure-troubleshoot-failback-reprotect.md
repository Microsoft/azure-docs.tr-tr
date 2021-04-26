---
title: Azure Site Recovery ile VMware VM olağanüstü durum kurtarma 'da yeniden çalışma sorunlarını giderme
description: Bu makalede, Azure Site Recovery ile Azure 'a VMware VM olağanüstü durum kurtarma sırasında yeniden çalışma ve yeniden koruma sorunlarını gidermeye yönelik yollar açıklanmaktadır.
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: ed4e52470264441a99c5ccf0a736bb00233510c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87423125"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Azure’dan şirket içine yapılan yeniden çalışma işleminde sorun giderme

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak Azure 'a yük devretmeden sonra Azure VM 'lerinizi şirket içi VMware altyapınıza geri döndüğünüzde karşılaşabileceğiniz sorunları nasıl giderebileceğiniz açıklanır.

Yeniden çalışma temelde iki ana adımdan oluşur. İlk adımda, yük devretmeden sonra çoğaltmaya başlayabilmeleri için Azure VM 'lerini şirket içinde yeniden korumanız gerekir. İkinci adım, Azure 'dan bir yük devretme işlemini şirket içi sitenize geri dönecek şekilde çalıştıralım.

## <a name="common-issues"></a>Genel sorunlar

- Salt okuma Kullanıcı vCenter bulmayı ve sanal makineleri koruyorsa, koruma başarılı olur ve yük devretme işlemi gerçekleştirilir. Yeniden koruma sırasında, veri depoları bulunamadığı için yük devretme başarısız olur. Belirti, veri depolarının yeniden koruma sırasında listelenmemiştir. Bu sorunu çözmek için vCenter kimlik bilgilerini izinleri olan uygun bir hesapla güncelleştirebilir ve ardından işi yeniden deneyebilirsiniz.
- Bir Linux sanal makinesini kapatıp şirket içinde çalıştırdığınızda, ağ yöneticisi paketinin makineden kaldırıldığını görebilirsiniz. Bu kaldırma işlemi, sanal makine Azure 'da kurtarılırken ağ yöneticisi paketinin kaldırıldığı için oluşur.
- Bir Linux sanal makinesi statik IP adresiyle yapılandırıldığında ve Azure 'a yük devretildiği zaman, IP adresi DHCP 'den alınır. Şirket içinde yük devrediğinizde, sanal makine IP adresini almak için DHCP kullanmaya devam eder. Makinede el ile oturum açın ve gerekirse IP adresini bir statik adrese geri ayarlayın. Bir Windows sanal makinesi, statik IP adresini yeniden alabilir.
- ESXi 5,5 Free Edition veya vSphere 6 hiper yönetici ücretsiz sürümünü kullanıyorsanız, yük devretme başarılı olur ancak yeniden çalışma başarılı olmaz. Yeniden çalışmayı etkinleştirmek için programın değerlendirme lisansına yükseltin.
- Yapılandırma sunucusuna işlem sunucusundan ulaşamadıysanız, bağlantı noktası 443 üzerindeki yapılandırma sunucusuna bağlantıyı denetlemek için Telnet kullanın. Ayrıca, işlem sunucusundan yapılandırma sunucusuna ping göndermeye da çalışırsınız. Ayrıca, bir işlem sunucusu yapılandırma sunucusuna bağlıyken sinyal içermelidir.
- Fiziksel bir şirket içi sunucu olarak korunan bir Windows Server 2008 R2 SP1 sunucusu, Azure 'dan şirket içi bir siteye yeniden başarısız olabilir.
- Aşağıdaki koşullarda yeniden başarısız olabilirsiniz:
    - Makineleri Azure 'a geçirdiniz. 
    - Bir VM 'yi başka bir kaynak grubuna taşımış olursunuz.
    - Azure VM 'yi silmiş olursunuz.
    - VM 'nin korumasını devre dışı bırakmış olursunuz.
    - VM 'yi Azure 'da el ile oluşturdunuz. Makinenin ilk olarak şirket içinde korunması ve yeniden korumadan önce Azure 'a yük devretmeli olması gerekir.
    - Yalnızca bir ESXi konağına başarısız olabilirsiniz. VMware VM 'lerini veya fiziksel sunucuları Hyper-V konaklarına, fiziksel makinelere veya VMware iş istasyonlarına yeniden çalıştıramazsınız.


## <a name="troubleshoot-reprotection-errors"></a>Yeniden koruma hatalarıyla ilgili sorunları giderme

Bu bölümde yaygın yeniden koruma hataları ve bunların nasıl düzeltilme ayrıntıları ayrıntılı olarak anlatılmaktadır.

### <a name="error-code-95226"></a>Hata kodu 95226

**Azure sanal makinesi Şirket içi yapılandırma sunucusuna erişemediğinden yeniden koruma başarısız oldu.**

Bu hata şu durumlarda oluşur:

* Azure VM, şirket içi yapılandırma sunucusuna erişemiyor. VM, yapılandırma sunucusuna bulunamıyor ve kayıtlı değil.
* InMage Scout uygulama hizmeti, yük devretmeden sonra Azure VM 'de çalışmıyor. Hizmet, şirket içi yapılandırma sunucusu ile iletişim için gereklidir.

Bu sorunu çözmek için:

* Azure VM ağının, Azure VM 'nin şirket içi yapılandırma sunucusuyla iletişim kurmasına izin verdiğinden emin olun. Şirket içi veri merkeziniz için bir siteden siteye VPN ayarlayabilir veya Azure VM 'nin sanal ağı üzerinde özel eşleme ile bir Azure ExpressRoute bağlantısı yapılandırabilirsiniz.
* VM, şirket içi yapılandırma sunucusuyla iletişim kurabiliyorsa, VM 'de oturum açın. Sonra InMage Scout uygulama hizmetini kontrol edin. Çalışmadığını görürseniz, hizmeti el ile başlatın. Hizmet başlatma türünün **Otomatik** olarak ayarlandığından emin olun.

### <a name="error-code-78052"></a>Hata kodu 78052

**Sanal makine için koruma tamamlanamadı.**

Bu sorun, üzerinde işlem yaptığınız ana hedef sunucuda aynı ada sahip bir VM zaten varsa meydana gelebilir.

Bu sorunu çözmek için:

* Farklı bir ana bilgisayar üzerinde farklı bir ana hedef sunucu seçin, böylece yeniden koruma, makineyi farklı bir konakta oluşturur, burada da adlar çakışmıyor.
* Ayrıca, VMotion 'ı kullanarak ana hedefi ad çarpışmanın gerçekleşmeyeceği farklı bir konağa taşıyabilirsiniz. Mevcut VM bir boş makinedir, yeni VM 'nin aynı ESXi konağında oluşturulabilmesi için yeniden adlandırın.


### <a name="error-code-78093"></a>Hata kodu 78093

**VM çalışmıyor, yanıt vermiyor veya erişilebilir durumda değil.**

Bu sorunu çözmek için:

Yük devri yapılan bir VM 'yi yeniden korumak için, Mobility hizmetinin şirket içi yapılandırma sunucusu 'na kaydolduğu ve işlem sunucusuyla iletişim kurarak çoğaltmaya başlayabileceği şekilde Azure VM 'nin çalışıyor olması gerekir. Makine yanlış bir ağ üzerinde veya çalışmıyorsa (yanıt vermiyor veya kapatıldı), yapılandırma sunucusu yeniden korumaya başlamak için VM 'deki Mobility hizmetine ulaşamaz.

* Şirket içinde yeniden iletişim başlatmasına başlayabilmesi için VM 'yi yeniden başlatın.
* Azure sanal makinesini başlattıktan sonra yeniden koruma işini yeniden başlatın.

### <a name="error-code-8061"></a>Hata kodu 8061

**Veri deposuna ESXi konağından erişilemiyor.**

Yeniden çalışma için [ana hedef önkoşullarını ve desteklenen veri depolarını](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) denetleyin.


## <a name="troubleshoot-failback-errors"></a>Yeniden çalışma hatalarında sorun giderme

Bu bölümde, yeniden çalışma sırasında karşılaşabileceğiniz yaygın hatalar açıklanmaktadır.

### <a name="error-code-8038"></a>Hata kodu 8038

**Hata nedeniyle Şirket içi sanal makine alınamadı.**

Bu sorun, şirket içi VM, yeterli belleğe sahip olmayan bir konağa getirildiğinde oluşur. 

Bu sorunu çözmek için:

* ESXi konağında daha fazla bellek sağlayın.
* Ayrıca, VMotion kullanarak VM 'yi önyüklemek için yeterli belleğe sahip başka bir ESXi konağına sanal makineyi taşıyabilirsiniz.
