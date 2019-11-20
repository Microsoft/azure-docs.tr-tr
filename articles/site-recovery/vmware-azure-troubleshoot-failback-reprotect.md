---
title: Azure Site Recovery ile VMware VM olağanüstü durum kurtarma 'da yeniden çalışma sorunlarını giderme
description: Bu makalede, Azure Site Recovery ile Azure 'a VMware VM olağanüstü durum kurtarma sırasında yeniden çalışma ve yeniden koruma sorunlarını gidermeye yönelik yollar açıklanmaktadır.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b597ecb67ab30c8617029fe741af1014444a9b70
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693156"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Azure 'dan şirket içine yeniden çalışma sorunlarını giderme

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak Azure 'a yük devretmeden sonra Azure VM 'lerinizi şirket içi VMware altyapınıza geri döndüğünüzde karşılaşabileceğiniz sorunları nasıl giderebileceğiniz açıklanır.

Yeniden çalışma temelde iki ana adımdan oluşur. İlk adımda, yük devretmeden sonra çoğaltmaya başlayabilmeleri için Azure VM 'lerini şirket içinde yeniden korumanız gerekir. İkinci adım, Azure 'dan bir yük devretme işlemini şirket içi sitenize geri dönecek şekilde çalıştıralım.

## <a name="troubleshoot-reprotection-errors"></a>Yeniden koruma hatalarıyla ilgili sorunları giderme

Bu bölümde yaygın yeniden koruma hataları ve bunların nasıl düzeltilme ayrıntıları ayrıntılı olarak anlatılmaktadır.

### <a name="error-code-95226"></a>Hata kodu 95226

**Azure sanal makinesi Şirket içi yapılandırma sunucusuna erişemediğinden yeniden koruma başarısız oldu.**

Bu hata şu durumlarda oluşur:

* Azure VM, şirket içi yapılandırma sunucusuna erişemiyor. VM, yapılandırma sunucusuna bulunamıyor ve kayıtlı değil.
* InMage Scout uygulama hizmeti, yük devretmeden sonra Azure VM 'de çalışmıyor. Hizmet, şirket içi yapılandırma sunucusu ile iletişim için gereklidir.

Bu sorunu çözmek için:

* Azure VM ağının, Azure VM 'nin şirket içi yapılandırma sunucusuyla iletişim kurmasına izin verdiğinden emin olun. Şirket içi veri merkeziniz için bir siteden siteye VPN ayarlayabilir veya Azure VM 'nin sanal ağı üzerinde özel eşleme ile bir Azure ExpressRoute bağlantısı yapılandırabilirsiniz.
* VM, şirket içi yapılandırma sunucusuyla iletişim kurabiliyorsa, VM 'de oturum açın. Sonra InMage Scout uygulama hizmetini kontrol edin. Çalışmadığını görürseniz, hizmeti el ile başlatın. Hizmet başlatma türünün **Otomatik**olarak ayarlandığından emin olun.

### <a name="error-code-78052"></a>Hata kodu 78052

**Sanal makine için koruma tamamlanamadı.**

Bu sorun, üzerinde işlem yaptığınız ana hedef sunucuda aynı ada sahip bir VM zaten varsa meydana gelebilir.

Bu sorunu çözmek için:

* Farklı bir ana bilgisayar üzerinde farklı bir ana hedef sunucu seçin, böylece yeniden koruma, makineyi farklı bir konakta oluşturur, burada da adlar çakışmıyor.
* Ayrıca, vMotion 'ı kullanarak ana hedefi ad çarpışmanın gerçekleşmeyeceği farklı bir konağa taşıyabilirsiniz. Mevcut VM bir boş makinedir, yeni VM 'nin aynı ESXi konağında oluşturulabilmesi için yeniden adlandırın.


### <a name="error-code-78093"></a>Hata kodu 78093

**VM çalışmıyor, askıda değil veya erişilebilir durumda değil.**

Bu sorunu çözmek için:

Yük devri yapılan bir VM 'yi yeniden korumak için, Mobility hizmetinin şirket içi yapılandırma sunucusu 'na kaydolduğu ve işlem sunucusuyla iletişim kurarak çoğaltmaya başlayabileceği şekilde Azure VM 'nin çalışıyor olması gerekir. Makine yanlış bir ağ üzerinde veya çalışmıyorsa (yanıt vermiyor veya kapatıldı), yapılandırma sunucusu yeniden korumaya başlamak için VM 'deki Mobility hizmetine ulaşamaz.

* Şirket içinde yeniden iletişim başlatmasına başlayabilmesi için VM 'yi yeniden başlatın.
* Azure sanal makinesini başlattıktan sonra yeniden koruma işini yeniden başlatın.

### <a name="error-code-8061"></a>Hata kodu 8061

**Veri deposuna ESXi konağından erişilemiyor.**

Yeniden çalışma için [ana hedef önkoşullarını ve desteklenen veri depolarını](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) denetleyin.


## <a name="troubleshoot-failback-errors"></a>Yeniden çalışma hatalarında sorun giderme

Bu bölümde, yeniden çalışma sırasında karşılaşabileceğiniz yaygın hatalar açıklanmaktadır.

### <a name="error-code-8038"></a>Hata kodu 8038

**Hata nedeniyle Şirket içi sanal makine alınamadı.**

Bu sorun, şirket içi VM, yeterli belleğe sahip olmayan bir konağa getirildiğinde oluşur. 

Bu sorunu çözmek için:

* ESXi konağında daha fazla bellek sağlayın.
* Ayrıca, vMotion kullanarak VM 'yi önyüklemek için yeterli belleğe sahip başka bir ESXi konağına sanal makineyi taşıyabilirsiniz.
