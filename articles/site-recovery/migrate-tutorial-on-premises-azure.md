---
title: Azure Site Recovery ile şirket içi makineleri geçirme
description: Bu makalede, Azure Site Recovery kullanarak şirket içi makineleri Azure’a geçirme işlemi açıklanmaktadır.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 24015810a295ef88b7d3e63bfc464ddddef6b55f
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73939628"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Şirket içi makineleri Azure’a geçirme


Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak şirket Içi makinelerin Azure 'a nasıl geçirileceği açıklanır. Genellikle Site Recovery, şirket içi makinelerin ve Azure VM 'lerinin olağanüstü durum kurtarma işlemlerini yönetmek ve düzenlemek için kullanılır. Ancak, geçiş için de kullanılabilir. Geçiş, tek bir özel durumla olağanüstü durum kurtarma ile aynı adımları kullanır. Bir geçişte, şirket içi sitenizin üzerinde başarısız olan makineler son adımdır. Olağanüstü durum kurtarma işleminden farklı olarak, bir geçiş senaryosunda şirket içinde yeniden oturum açamazsınız.


Bu öğreticide, şirket içi VM’ler ve fiziksel sunucuları Azure’a geçirme gösterilmektedir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Geçiş için kaynak ve hedef ortamı ayarlama
> * Çoğaltma ilkesi ayarlama
> * Çoğaltmayı etkinleştirme
> * Her şeyin beklendiği gibi çalıştığından emin olmak için bir geçiş testi çalıştırma
> * Azure’a bir defalık yük devretme çalıştırma


> [!TIP]
> Azure geçişi hizmetini kullanarak, artık şirket içi sunucuları Azure 'a geçirebilirsiniz. [Daha fazla bilgi](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Başlamadan önce

Parasanallaştırılmış sürücüler tarafından dışarıya aktarılmış cihazların desteklenmediğini unutmayın.


## <a name="prepare-azure-and-on-premises"></a>Azure 'u ve şirket içi hazırlama

1. [Bu makalede](tutorial-prepare-azure.md)açıklandığı gibi Azure 'u hazırlayın. Bu makalede olağanüstü durum kurtarma için hazırlık adımları açıklanmakta olsa da, adımlar geçiş için de geçerlidir.
2. Şirket içi [VMware](vmware-azure-tutorial-prepare-on-premises.md) veya [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) sunucularını hazırlayın. Fiziksel makineleri geçiriyorsanız, herhangi bir şeyi hazırlamanız gerekmez. Yalnızca [destek matrisini](vmware-physical-azure-support-matrix.md)doğrulayın.


## <a name="select-a-protection-goal"></a>Koruma hedefi seçme

Neleri çoğaltmak istediğinizi ve bunları nereye çoğaltacağınızı seçin.
1. **Kurtarma Hizmetleri kasaları** > kasa öğesine tıklayın.
2. Kaynak Menüsünde, **Site Recovery** > **Altyapıyı Hazırlama** > **Koruma hedefi** seçeneklerine tıklayın.
3. **Koruma hedefi**’nde, geçişini yapmak istediğiniz öğeyi seçin.
    - **VMware**: **Azure’a** > **Evet, VMWare vSphere Hiper Yöneticisi ile** öğelerini seçin.
    - **Fiziksel makine**: **Azure’a** > **Sanallaştırılmamış/Diğer** öğelerini seçin.
    - **Hyper-V**: **Azure’a** > **Evet, Hyper-V ile** öğelerini seçin. Hyper-V VM’leri VMM tarafından yönetiliyorsa, **Evet**’i seçin.


## <a name="set-up-the-source-environment"></a>Kaynak ortamı ayarlama

**Senaryo** | **Ayrıntılar**
--- | --- 
VMware | [Kaynak ortamı](vmware-azure-set-up-source.md)ayarlayın ve [yapılandırma sunucusunu](vmware-azure-deploy-configuration-server.md)ayarlayın.
Fiziksel makine | Kaynak ortamı ve yapılandırma sunucusunu [ayarlayın](physical-azure-set-up-source.md) .
Hyper-V | [Kaynak ortamı](hyper-v-azure-tutorial.md#set-up-the-source-environment) ayarlama<br/><br/> System Center VMM ile dağıtılan Hyper-V için [kaynak ortamı](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) ayarlayın.

## <a name="set-up-the-target-environment"></a>Hedef ortamı ayarlama

Hedef kaynaklarını seçin ve doğrulayın.

1. **Altyapıyı hazırlama** > **Hedef** seçeneklerine tıklayıp kullanmak istediğiniz Azure aboneliğini seçin.
2. Kaynak Yöneticisi dağıtım modelini belirtin.
3. Site Recovery Azure kaynaklarını denetler.
    - VMware VM 'lerini veya fiziksel sunucuları geçiriyorsanız Site Recovery, yük devretmeden sonra oluşturulan Azure sanal makinelerinin bulunduğu bir Azure ağınızın olduğunu doğrular.
    - Hyper-V VM 'lerini geçiriyorsanız, Site Recovery uyumlu bir Azure depolama hesabınız ve ağınız olduğunu doğrular.
4. System Center VMM tarafından yönetilen Hyper-V VM 'lerini geçiriyorsanız [ağ eşlemesi](hyper-v-vmm-azure-tutorial.md#configure-network-mapping)ayarlayın.

## <a name="set-up-a-replication-policy"></a>Çoğaltma ilkesi ayarlama

**Senaryo** | **Ayrıntılar**
--- | --- 
VMware | VMware VM 'Leri için [Çoğaltma İlkesi](vmware-azure-set-up-replication.md) ayarlayın.
Fiziksel makine | Fiziksel makineler için bir [Çoğaltma İlkesi](physical-azure-disaster-recovery.md#create-a-replication-policy) ayarlayın.
Hyper-V | [Çoğaltma İlkesi](hyper-v-azure-tutorial.md#set-up-a-replication-policy) ayarlama<br/><br/> System Center VMM ile dağıtılan Hyper-V için bir [Çoğaltma İlkesi](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) ayarlayın.

## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

**Senaryo** | **Ayrıntılar**
--- | --- 
VMware | VMware VM’leri için [çoğaltmayı etkinleştirin](vmware-azure-enable-replication.md).
Fiziksel makine | Fiziksel makineler için [çoğaltmayı etkinleştirin](physical-azure-disaster-recovery.md#enable-replication) .
Hyper-V | [Çoğaltmayı etkinleştirme](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> System Center VMM ile dağıtılan Hyper-V için [çoğaltmayı etkinleştirin](hyper-v-vmm-azure-tutorial.md#enable-replication) .


## <a name="run-a-test-migration"></a>Geçiş testi çalıştırma

Her şeyin beklendiği gibi çalıştığından emin olmak için bir Azure’a [yük devretme testi](tutorial-dr-drill-azure.md) çalıştırın.


## <a name="migrate-to-azure"></a>Azure’a geçiş

Geçirmek istediğiniz makineler için yük devretmeyi çalıştırın.

1. **Ayarlar** > **Çoğaltılan öğeler** bölümünde makine > **Yük devretme**’ye tıklayın.
2. **Yük devretme**’de yük devretmenin yapılacağı bir **Kurtarma Noktası** seçin. En son kurtarma noktasını seçin.
3. Şifreleme anahtarı ayarı, bu senaryo için geçerli değildir.
4. **Yük devretmeyi başlatmadan önce makineyi kapatın** seçeneğini belirleyin. Site Recovery yük devretmeyi tetiklemeden önce sanal makineleri kapatmayı dener. Kapatma işlemi başarısız olsa bile yük devretme devam eder. Yük devretme işlemini **İşler** sayfasında takip edebilirsiniz.
5. Azure VM’nin Azure’da beklendiği gibi görüntülenip görüntülenmediğini kontrol edin.
6. **Çoğaltılan öğeler** bölümünde VM’ye sağ tıklayıp **Geçişi Tamamla**’ya tıklayın. Bu, şunları yapar:

   - Geçiş işlemini sonlandırır, şirket içi VM için çoğaltmayı durduruyor ve VM için Site Recovery faturalandırmayı durduruyor.
   - Bu adım, çoğaltma verilerini temizler. Geçirilen VM 'Leri silmez.

     ![Geçişi tamamlama](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Devam eden yük devretme işlemini iptal etmeyin**: Yük devretme başlatılmadan önce VM çoğaltma durdurulur. Devam eden bir yük devretme işlemini iptal ederseniz yük devretme durdurulur, ancak VM yeniden çoğaltılamaz.

Bazı senaryolarda yük devretme için sekiz ila on dakikada tamamlanan ek işlem gerekebilir. Fiziksel sunucular, VMware Linux makineleri, DHCP hizmeti etkinleştirilmemiş VMware VM 'Leri ve şu önyükleme sürücülerine sahip olmayan VMware VM 'Leri için daha uzun test yük devretme süreleri görebilirsiniz: storvsc, VMBus, storflt, intelide, Atapi.

## <a name="after-migration"></a>Geçişten sonra

Makineler Azure’a geçirildikten sonra, tamamlamanız gereken birkaç adım vardır.

Bazı adımlar, [kurtarma planlarındaki](site-recovery-runbook-automation.md) yerleşik otomasyon betikleri kullanılarak geçiş işleminin parçası olarak otomatikleştirilebilir.   


### <a name="post-migration-steps-in-azure"></a>Azure’da geçiş sonrası adımlar

- Veritabanı bağlantısı dizelerini ve web sunucusu yapılandırmalarını güncelleştirme gibi herhangi bir geçiş sonrası uygulama ayarı gerçekleştirin. 
- Geçirilen uygulamada son uygulama ve geçiş kabul testi gerçekleştirme işlemi şimdi Azure’da çalıştırılmaktadır.
- [Azure VM aracısı](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows), Azure Yapı Denetleyicisi ile sanal makine etkileşimini yönetir. Azure Backup, Site Recovery ve Azure Güvenliği gibi bazı Azure hizmetleri için gereklidir.
    - VMware makinelerini ve fiziksel sunucuları geçiriyorsanız Mobility Hizmeti yükleyicisi, Windows makinelere kullanılabilir Azure sanal makine aracısını yükler. Linux sanal makineleri üzerinde, yük devretmeden sonra aracıyı yüklemeniz önerilir.
    - Azure sanal makinelerini ikincil bölgeye geçiriyorsanız Azure sanal makine aracısı, geçişten önce sanal makinede sağlanmalıdır.
    - Hyper-V sanal makinelerini Azure’a geçiriyorsanız, geçişten sonra Azure sanal makinesine Azure sanal makine aracısını yükleyin.
- Sanal makineden Site Recovery sağlayıcısını/aracısını kendiniz kaldırın. VMware VM 'lerini veya fiziksel sunucuları geçirirseniz, Mobility hizmetini sanal makineden kaldırın.
- Daha fazla esneklik için:
    - Azure Backup hizmetini kullanarak Azure sanal makinelerini yedekleyip verileri güvende tutun. [Daha fazla bilgi edinin]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Site Recovery ile Azure sanal makinelerini ikincil bölgeye çoğaltarak iş yüklerinin çalışmaya devam etmesini ve sürekli kullanılabilir olmasını sağlayın. [Daha fazla bilgi edinin](azure-to-azure-quickstart.md).
- Daha fazla güvenlik için:
    - Azure Güvenlik Merkezi [Anlık yönetim]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time) özelliği ile gelen trafik erişimini kilitleme ve sınırlama
    - [Ağ Güvenlik Grupları](https://docs.microsoft.com/azure/virtual-network/security-overview) ile ağ trafiğini yönetim uç noktaları ile kısıtlayın.
    - [Azure Disk Şifrelemesi](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)’ni dağıtarak disklerin güvenliğinin sağlanmasına yardımcı olun ve verileri hırsızlık ve yetkisiz erişime karşı koruyun.
    - [IaaS kaynaklarının güvenliğini sağlama]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) hakkında daha fazla bilgi edinin ve [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/ )’ni ziyaret edin.
- İzleme ve yönetim için:
    - [Azure Maliyet Yönetimi](https://docs.microsoft.com/azure/cost-management/overview)’ni dağıtarak kaynak kullanımını ve harcamayı izleyin.

### <a name="post-migration-steps-on-premises"></a>Şirket içi geçiş sonrası adımlar

- Uygulama trafiğini, geçirilen Azure sanal makinesi örneğinde çalıştırılan uygulamaya taşıyın.
- Yerel sanal makine envanterinizden şirket içi sanal makineleri kaldırın.
- Yerel yedeklemelerden şirket içi sanal makineleri kaldırın.
- Azure sanal makinelerinin yeni konumunu ve IP adresini göstermek için herhangi bir iç belgeyi güncelleştirin.




## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şirket içi VM’leri Azure VM’lerine taşıdınız. Sunuldu

> [!div class="nextstepaction"]
> Azure VM 'Leri için ikincil bir Azure bölgesine [olağanüstü durum kurtarmayı ayarlayın](azure-to-azure-replicate-after-migration.md) .

  
