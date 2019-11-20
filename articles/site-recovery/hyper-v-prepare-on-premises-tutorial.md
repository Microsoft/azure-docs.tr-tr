---
title: Azure Site Recovery ile Hyper-V VM 'lerini Azure 'a olağanüstü durum kurtarmaya hazırlanma
description: Azure Site Recovery ile Azure 'a olağanüstü durum kurtarma için şirket içi Hyper-V VM 'lerini nasıl hazırlayacağınızı öğrenin.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b36e4688ebd6e929a56869a6bb191d98b2f1f432
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953998"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Azure 'a olağanüstü durum kurtarma için şirket içi Hyper-V sunucuları hazırlama

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak Hyper-VM 'lerinin olağanüstü durum kurtarmasını ayarlamak istediğinizde şirket içi Hyper-V altyapınızı nasıl hazırlayacağınız açıklanır.


Bu, şirket içi Hyper-V VM 'Leri için Azure 'da olağanüstü durum kurtarmanın nasıl ayarlanacağını gösteren bir serinin ikinci öğreticisidir. İlk öğreticide, Hyper-V olağanüstü durum kurtarma için gereken [Azure bileşenlerini ayarladık](tutorial-prepare-azure.md) .

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Hyper-v konaklarınız System Center VMM tarafından yönetiliyorsa, Hyper-V gereksinimlerini ve VMM gereksinimlerini gözden geçirin.
> * Uygunsa VMM 'yi hazırlayın.
> * Azure konumlarına Internet erişimini doğrulayın.
> * Azure 'a yük devretmeden sonra erişebilmek için VM 'Leri hazırlayın.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir. Mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için Site Recovery Içindekiler tablosunun nasıl yapılır bölümündeki makaleyi gözden geçirin.

## <a name="before-you-start"></a>Başlamadan önce

[Bu serinin ilk öğreticisinde](tutorial-prepare-azure.md)açıklandığı gibi Azure 'ı hazırladığınızdan emin olun.

## <a name="review-requirements-and-prerequisites"></a>Gereksinimleri ve önkoşulları gözden geçirin

Hyper-V konaklarının ve VM 'Lerin gereksinimlere uyduğundan emin olun.

1. Şirket içi sunucu gereksinimlerini [doğrulayın](hyper-v-azure-support-matrix.md#on-premises-servers) .
2. Azure 'a çoğaltmak istediğiniz Hyper-V VM 'lerinin [gereksinimlerini denetleyin](hyper-v-azure-support-matrix.md#replicated-vms) .
3. Hyper-V konak [ağını](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)denetle; ve şirket içi Hyper-V konakları için konak ve Konuk [depolama](hyper-v-azure-support-matrix.md#hyper-v-host-storage) desteği.
4. Yük devretmenin ardından [Azure ağ](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [depolama](hyper-v-azure-support-matrix.md#azure-storage) ve [işlem](hyper-v-azure-support-matrix.md#azure-compute-features) için nelerin desteklendiğini denetleyin.
5. Azure’a çoğalttığınız şirket içi sanal makineleriniz, [Azure sanal makinesi gereksinimleri](hyper-v-azure-support-matrix.md#azure-vm-requirements) ile uyumlu olmalıdır.


## <a name="prepare-vmm-optional"></a>VMM 'yi hazırlama (isteğe bağlı)

Hyper-V konakları VMM tarafından yönetiliyorsa, şirket içi VMM sunucusunu hazırlamanız gerekir. 

- VMM sunucusunun bir veya daha fazla konak grubu ile en az bir buluta sahip olduğundan emin olun. VM 'Lerin üzerinde çalıştığı Hyper-V konağı bulutta bulunmalıdır.
- VMM sunucusunu ağ eşleme için hazırlayın.

### <a name="prepare-vmm-for-network-mapping"></a>VMM 'yi ağ eşleme için hazırlama

VMM kullanıyorsanız, şirket içi VMM VM ağları ve Azure sanal ağları arasında [ağ eşleme](site-recovery-network-mapping.md) haritaları. Eşleme, yük devretmeden sonra oluşturulan Azure VM 'lerinin doğru ağa bağlanmasını sağlar.

Ağ eşlemesi için VMM 'yi aşağıdaki şekilde hazırlayın:

1. Hyper-V konaklarının bulunduğu bulutla ilişkili bir [VMM mantıksal ağınıza](https://docs.microsoft.com/system-center/vmm/network-logical) sahip olduğunuzdan emin olun.
2. Mantıksal ağa bağlı bir [VM ağına](https://docs.microsoft.com/system-center/vmm/network-virtual) sahip olduğunuzdan emin olun.
3. VMM 'de VM 'Leri VM ağına bağlayın.

## <a name="verify-internet-access"></a>İnternet erişimini doğrulama

1. Öğreticinin amaçları doğrultusunda, en basit yapılandırma Hyper-V konaklarının ve VMM sunucusunun bir ara sunucu kullanılmadan internet 'e doğrudan erişmesini sağlar. 
2. Hyper-V konaklarının ve uygunsa VMM sunucusunun aşağıdaki gerekli URL 'Lere erişebilmeleri için emin olun.   
3. Erişimi IP adresine göre denetyorsanız şunları yaptığınızdan emin olun:
    - IP adresi tabanlı güvenlik duvarı kuralları, [Azure veri MERKEZI IP aralıklarına](https://www.microsoft.com/download/confirmation.aspx?id=41653)ve HTTPS (443) bağlantı noktasına bağlanabilir.
    - Aboneliğinizin Azure bölgesi için IP adresi aralıklarına izin verin.
    
### <a name="required-urls"></a>Gerekli URL 'Ler


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Yük devretmeden sonra Azure VM'lerine bağlanmak için hazırlık yapma

Yük devretme senaryosu sırasında, çoğaltılan şirket içi ağınıza bağlanmak isteyebilirsiniz.

Yük devretmeden sonra RDP kullanarak Windows VM 'lerine bağlanmak için aşağıdaki gibi erişime izin verin:

1. İnternet üzerinden erişmek için, yük devretmeden önce şirket içi VM’de RDP’yi etkinleştirin. TCP ve UDP kurallarının **Ortak** profil için eklendiğinden ve tüm profillerde **Windows Güvenlik Duvarı** > **İzin Verilen Uygulamalar** içinde RDP’ye izin verildiğinden emin olun.
2. Siteden siteye VPN üzerinden erişmek için, şirket içi makinede RDP’yi etkinleştirin. **Etki Alanı ve Özel** ağlar için  -> Windows Güvenlik Duvarı**İzin verilen uygulama ve özellikler içinde** RDP’ye izin verilmelidir.
   İşletim sisteminin SAN ilkesinin **OnlineAll** olarak ayarlandığından emin olun. [Daha fazla bilgi edinin](https://support.microsoft.com/kb/3031135). Bir yük devretme tetiklediğinizde VM’de bekleyen Windows güncelleştirmelerinin olmaması gerekir. Varsa, güncelleştirme tamamlanana kadar sanal makinede oturum açamazsınız.
3. Yük devretmeden sonra Windows Azure VM’sinde, VM’nin bir ekran görüntüsünü görmek için **Önyükleme tanılaması**’nı kontrol edin. Bağlanamıyorsanız, VM’nin çalıştığından emin olun ve şu [sorun giderme ipuçlarını](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) gözden geçirin.

Yük devretmeden sonra, çoğaltılan şirket içi VM ile aynı IP adresini veya farklı bir IP adresini kullanarak Azure VM 'lerine erişebilirsiniz. Yük devretme için IP adresleme ayarlama hakkında [daha fazla bilgi edinin](concepts-on-premises-to-azure-networking.md) .

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Hyper-v VM 'leri Için Azure 'da olağanüstü durum kurtarmayı ayarlama](tutorial-hyper-v-to-azure.md)
> [VMM bulutlarındaki Hyper-v VM 'leri için olağanüstü durum kurtarmayı ayarlama](tutorial-hyper-v-vmm-to-azure.md)
