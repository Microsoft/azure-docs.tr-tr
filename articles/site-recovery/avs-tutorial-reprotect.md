---
title: Azure Site Recovery ile Azure VM 'lerini Azure VMware çözümü özel bulutuna yeniden koruma
description: Azure Site Recovery ile Azure 'a yük devretmeden sonra Azure VMware Çözüm VM 'lerini yeniden nasıl koruyacağınızı öğrenin.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 80ff2f3f3d5fdcf61770889dcdaaf075941b90ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91814618"
---
# <a name="reprotect-from-azure-to-azure-vmware-solution-private-cloud"></a>Azure 'dan Azure VMware çözümü özel bulutuna yeniden koruma

Azure VMware Çözüm VM 'lerinden Azure 'a [yük devrettikten](avs-tutorial-failover.md) sonra, Ilk adım Azure VMware çözümünüz özel bulutuna geri dönme işlemi, yük devretme sırasında oluşturulan Azure VM 'lerinin yeniden korunması. Bu makalede bunun nasıl yapılacağı açıklanır. 

## <a name="before-you-begin"></a>Başlamadan önce

1. Azure 'da bir işlem sunucusu ayarlama ve bir Azure VMware çözümü özel bulut ana hedef sunucusu ve bir siteden siteye VPN veya ExpressRoute özel eşlemesini yeniden çalışma için yapılandırma dahil, yeniden koruma ve yeniden çalışma için hazırlanmak üzere [Bu makaledeki](vmware-azure-prepare-failback.md) adımları izleyin.
2. Azure VMware çözümü özel bulut yapılandırma sunucusunun çalıştığından ve Azure 'a bağlı olduğundan emin olun. Yeniden çalışma sırasında VM 'nin yapılandırma sunucusu veritabanında mevcut olması gerekir. Aksi takdirde, yeniden çalışma başarısız olur.
3. Azure VMware çözümü özel bulut ana hedef sunucusu üzerindeki tüm anlık görüntüleri silin. Anlık görüntüler varsa yeniden koruma çalışmaz.  Yeniden koruma işi sırasında VM 'deki anlık görüntüler otomatik olarak birleştirilir.
4. Çoklu VM tutarlılığı için bir çoğaltma grubuna toplanan VM 'Leri yeniden koruyorsanız, tümünün aynı işletim sistemine (Windows veya Linux) sahip olduklarından emin olun ve dağıttığınız ana hedef sunucunun aynı işletim sistemi türüne sahip olduğundan emin olun. Bir çoğaltma grubundaki tüm VM 'Lerin aynı ana hedef sunucuyu kullanması gerekir.
5. Yeniden çalışma için [gereken bağlantı noktalarını](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) açın.
6. Yeniden çalışma öncesinde vCenter Server bağlı olduğundan emin olun. Aksi takdirde, disklerin bağlantısının kesilmesi ve sanal makineye geri eklenmesi başarısız olur.
7. Bir vCenter sunucusu, geri dönecek VM 'Leri yönetirse, gerekli izinlere sahip olduğunuzdan emin olun. Salt okuma Kullanıcı vCenter bulmayı ve sanal makineleri koruyorsa, koruma başarılı olur ve yük devretme işlemi gerçekleştirilir. Bununla birlikte, yeniden koruma sırasında, veri depoları keşfedilmeden ve yeniden koruma sırasında listelenmediği için yük devretme başarısız olur. Bu sorunu çözmek için vCenter kimlik bilgilerini [uygun bir hesap/izinlerle](avs-tutorial-prepare-avs.md#prepare-an-account-for-automatic-discovery)güncelleştirebilir ve ardından işi yeniden deneyebilirsiniz. 
8. Sanal makinelerinizi oluşturmak için bir şablon kullandıysanız, her VM 'nin diskler için kendi UUID 'sine sahip olduğundan emin olun. Aynı şablondan oluşturulduğundan, Azure VMware Çözüm VM UUID 'SI ana hedef sunucunun UUID 'SI ile çakışıyor, yeniden koruma başarısız olur. Farklı bir şablondan dağıtın.
9. Alternatif bir vCenter Server geri yüklüyorsanız, yeni vCenter Server ve ana hedef sunucunun bulunduğundan emin olun. Genellikle veri depoları erişilemez veya **yeniden koruma** bölümünde görünmez.
10. Geri gerçekleştiremeyecek aşağıdaki senaryoları doğrulayın:
    - ESXi 5,5 Free Edition ya da vSphere 6 hiper yönetici ücretsiz sürümü kullanıyorsanız. Farklı bir sürüme yükseltin.
    - Windows Server 2008 R2 SP1 fiziksel sunucunuz varsa.
    - VMware VM 'Leri Hyper-V ' d e geri yük devredemeyebilir.
    - Geçirilen VM 'Ler.
    - Başka bir kaynak grubuna taşınan bir VM.
    - Silinen bir çoğaltma Azure VM 'si.
    - Korunmayan bir çoğaltma Azure VM 'si.
10. Yeniden [çalışma türlerini gözden geçirin](concepts-types-of-failback.md) -özgün konum kurtarma ve alternatif konum kurtarma kullanabilirsiniz.


## <a name="enable-reprotection"></a>Yeniden korumayı etkinleştir

Çoğaltmayı etkinleştirin. Belirli VM 'Leri veya bir kurtarma planını yeniden koruyabilirsiniz:

- Bir kurtarma planını yeniden koruyorduysanız, korunan her makinenin değerlerini sağlamanız gerekir.
- VM 'Ler çoklu VM tutarlılığı için bir çoğaltma grubuna aitse, yalnızca bir kurtarma planı kullanılarak yeniden korunabilir. Bir çoğaltma grubundaki VM 'Lerin aynı ana hedef sunucuyu kullanması gerekir

>[!NOTE]
>Yeniden koruma sırasında Azure 'dan Erstwhile kaynağına gönderilen veri miktarı, tüm korunan makineler için 0 bayt ve disk boyutu toplamı arasında herhangi bir şey olabilir ve hesaplanamaz.

### <a name="before-you-start"></a>Başlamadan önce

- Yük devretmeden sonra Azure 'da bir VM önyüklendiğinde, aracının yapılandırma sunucusuna (15 dakikaya kadar) kaydolması biraz zaman alır. Bu süre boyunca yeniden koruyamazsınız ve bir hata mesajı aracının yüklenmediğini gösterir. Bu durumda, birkaç dakika bekleyin ve sonra yeniden koruyun.
- Azure VM 'yi mevcut bir Azure VMware Çözüm VM 'sine geri yüklemek istiyorsanız, VM veri depolarını ana hedef sunucunun ESXi konağına okuma/yazma erişimiyle bağlayın.
- Alternatif bir konuma yeniden yük devretmek istiyorsanız, örneğin Azure VMware Çözüm VM 'si yoksa, ana hedef sunucusu için yapılandırılan bekletme sürücüsünü ve veri deposunu seçin. Azure VMware Çözüm özel bulutuna geri döndüğünüzde, yeniden çalışma koruma planındaki sanal makineler ana hedef sunucuyla aynı veri deposunu kullanır. Daha sonra vCenter 'da yeni bir VM oluşturulur.

Yeniden korumayı aşağıdaki şekilde etkinleştirin:

1. **Kasa**  >  **çoğaltılan öğeleri** seçin. Yük devredilen sanal makineye sağ tıklayın ve ardından **yeniden koru**' yı seçin. Ya da, komut düğmelerinden makineyi seçip **yeniden koru**' yı seçin.
2. **Azure 'dan şirket içi** koruma yönünün seçildiğini doğrulayın.
3. **Ana hedef sunucusu** ve **işlem sunucusu**' nda, şirket içi ana hedef sunucusunu ve işlem sunucusunu seçin.  
4. **Veri deposu** Için, Azure VMware çözümünde diskleri kurtarmak istediğiniz veri deposunu seçin. Bu seçenek, Azure VMware Çözüm VM 'si silindiğinde ve yeni diskler oluşturmanız gerektiğinde kullanılır. Diskler zaten mevcutsa bu seçenek yoksayılır. Yine de bir değer belirtmeniz gerekir.
5. Bekletme sürücüsünü seçin.
6. Yeniden çalışma ilkesi otomatik olarak seçilir.
7. Yeniden korumaya başlamak için **Tamam ' ı** seçin.

    ![Yeniden Koru iletişim kutusu](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Bir iş, Azure VMware Çözüm özel bulutuna Azure VM çoğaltmasını çoğaltmaya başlar. **İşler** sekmesinde ilerleme durumunu izleyebilirsiniz.
    - Yeniden koruma başarılı olduğunda, VM korumalı bir duruma girer.
    - Azure VMware Çözüm VM, yeniden koruma sırasında kapalıdır. Bu işlem, çoğaltma sırasında veri tutarlığını sağlar.
    - Yeniden koruma tamamlandıktan sonra Azure VMware Çözüm VM 'yi kapatmayın.
   

## <a name="next-steps"></a>Sonraki adımlar

- Herhangi bir sorunla karşılaşırsanız [sorun giderme makalesini](vmware-azure-troubleshoot-failback-reprotect.md)inceleyin.
- Azure VM 'Leri korunduktan sonra yeniden [çalışma çalıştırabilirsiniz](avs-tutorial-failback.md). Yeniden çalışma, Azure VM 'yi kapatır ve Azure VMware Çözüm VM 'sini önyükler. Uygulama için bazı kesinti süreleri bekliyor ve uygun bir yeniden çalışma süresi seçin.


