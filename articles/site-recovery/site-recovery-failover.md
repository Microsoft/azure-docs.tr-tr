---
title: Azure Site Recovery ile olağanüstü durum kurtarma sırasında yük devretme çalıştırma
description: Azure Site Recovery ile VM 'Leri/fiziksel sunucuları Azure 'a devretmek.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 6737f64773f91ede1631d42cd7f28c7d961c0454
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368630"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Şirket içinden Azure’a yük devretme çalıştırma

Bu makalede [Azure Site Recovery](site-recovery-overview.md) ' de şirket Içi makinelerin Azure 'a yük devretme işlemi açıklanır

## <a name="before-you-start"></a>Başlamadan önce

- Olağanüstü durum kurtarma 'daki yük devretme süreci hakkında [bilgi edinin](failover-failback-overview.md) .
- Birden fazla makinenin yükünü devretmek istiyorsanız, bir kurtarma planında makineleri nasıl [toplayacağınızı öğrenin](recovery-plan-overview.md) .
- Tam yük devretme yapmadan önce, her şeyin beklendiği gibi çalıştığından emin olmak için bir [olağanüstü durum kurtarma detayına](site-recovery-test-failover-to-azure.md) sahip olun.

## <a name="prepare-to-connect-after-failover"></a>Yük devretmeden sonra bağlanmaya hazırlanma

Yük devretmeden sonra oluşturulan Azure VM 'lerine bağlanabildiğinizden emin olmak için, yük devretmeden önce şirket içi yapmanız gereken birkaç şey vardır.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Yük devretmeden sonra bağlanmak için şirket içi hazırlama

Yük devretmenin ardından RDP/SSH kullanarak Azure VM 'lerine bağlanmak isterseniz, yük devretmeden önce şirket içi yapmanız gereken birkaç şey vardır.

**Yük devretmeden sonra** | **Konum** | **Eylemler**
--- | --- | ---
**Windows çalıştıran Azure VM** | Yük devretmeden önce şirket içi makine | Azure VM 'ye internet üzerinden erişmek için, RDP 'yi etkinleştirin ve TCP ve UDP kurallarının **genel**'e eklendiğinden emin olun ve **Windows Güvenlik Duvarı**  >  **izin verilen uygulamalar**'daki tüm profiller için RDP 'ye izin verildiğinden emin olun.<br/><br/> Azure VM 'ye siteden siteye bağlantı üzerinden erişmek için makinede RDP 'yi etkinleştirin ve   ->  **etki alanı ve özel** ağlar için Windows Güvenlik Duvarı **izin verilen uygulamalar ve Özellikler**'de RDP 'ye izin verildiğinden emin olun.<br/><br/> <br/><br/> Tüm statik kalıcı rotaları ve WinHTTP proxy 'yi kaldırın. İşletim sistemi SAN ilkesinin **OnlineAll** olarak ayarlandığından emin olun. [Daha fazla bilgi edinin](https://support.microsoft.com/kb/3031135).<br/><br/> Yük devretme tetiklemeniz sırasında VM 'de bekleyen bir Windows güncelleştirmesi olmadığından emin olun. Yük devretmek için Windows Update başlayabilir ve güncelleştirme tamamlanana kadar VM 'de oturum açamazsınız.
**Linux çalıştıran Azure VM** | Yük devretmeden önce şirket içi makine | VM 'deki Secure Shell hizmetinin sistem önyüklemesi üzerinde otomatik olarak başlayacak şekilde ayarlandığından emin olun.<br/><br/> Güvenlik duvarı kurallarının gerçekleştirilecek SSH bağlantısına izin verdiğinden emin olun.


## <a name="run-a-failover"></a>Yük devretme çalıştırma

Bu yordam, bir [kurtarma planı](site-recovery-create-recovery-plans.md)için yük devretmeyi nasıl çalıştıracağınızı açıklar. Tek bir VM için yük devretme çalıştırmak istiyorsanız, bir [VMware VM](vmware-azure-tutorial-failover-failback.md), [fiziksel sunucu](physical-to-azure-failover-failback.md)veya bir [Hyper-V sanal makinesi](hyper-v-azure-failover-failback-tutorial.md)için yönergeleri izleyin.


Kurtarma planı yük devretmesini aşağıdaki gibi çalıştırın:

1. Site Recovery kasasında **kurtarma planları**  >  *recoveryplan_name*' nı seçin.
2. **Yük devretme**' ye tıklayın.

    ![Daha fazla menüden seçili yük devretme ile ADRP bölmesini gösteren Azure Site Recovery ekran görüntüsü.](./media/site-recovery-failover/Failover.png)

3. **Yük** devretme  >  **yük devretmesi yönü**' nde, Azure 'a çoğaltma yapıyorsanız varsayılan olarak bırakın.
4. **Yük devretme** bölümünde yük devretmek Için bir **Kurtarma noktası** seçin.

    - **En son**: en son noktayı kullanın. Bu, Site Recovery hizmetine gönderilen tüm verileri işler ve her makine için bir kurtarma noktası oluşturur. Yük devretmeden sonra oluşturulan VM, yük devretme tetiklendiğinde Site Recovery çoğaltılan tüm verilere sahip olduğundan, bu seçenek en düşük RPO (kurtarma noktası hedefi) sağlar.
    Kaynak bölgesi kaldığında, mümkün olan daha fazla günlük işlemi olmadığını lütfen unutmayın. Bu nedenle, en son Işlenen kurtarma noktasına yük devretmeye ihtiyacınız olacak. Daha fazla anlamak için sonraki noktaya bakın.
   - **En son işlenen**: Site Recovery tarafından zaten işlenen en son kurtarma noktasına VM 'lerin yükünü devretmek için bu seçeneği kullanın. En son işlenen kurtarma noktasını VM **en son kurtarma noktalarında** görebilirsiniz. Bu seçenek, işlenmemiş verileri işlemek için bir süre harcanması için düşük bir RTO sağlar
   - **En son uygulamayla tutarlı**: Site Recovery tarafından işlenen en son uygulamayla tutarlı kurtarma noktasına VM 'leri devretmek için bu seçeneği kullanın.
   - **En son çoklu VM işlendi**: Bu seçenek, çoğaltma grubunun bir parçası olan VM 'lerin en son ortak çoklu VM tutarlı kurtarma noktasına devredilmesine sahiptir. Diğer sanal makineler, son işlenen kurtarma noktasına yük devreder. Bu seçenek yalnızca çoklu VM tutarlılığı etkinleştirilmiş en az bir VM içeren kurtarma planları içindir.
   - **En son çoklu VM uygulaması-tutarlı**: Bu seçenek, bir çoğaltma grubunun parçası olan VM 'ler, en son ortak çoklu VM ile uygulamayla tutarlı kurtarma noktasına yük devreder. Diğer sanal makineler, uygulamayla tutarlı en son kurtarma noktasına yük devreder. Yalnızca çoklu VM tutarlılığı etkinleştirilmiş en az bir VM 'ye sahip kurtarma planları için.
   - **Özel**: kurtarma planları için kullanılamaz. Bu seçenek yalnızca tek VM 'lerin yük devretmesi içindir.

5. Yük devretmeye başlamadan önce kaynak VM 'Leri kapatmak Site Recovery isterseniz, **yük devretmeye başlamadan önce kapatma makinesini** seçin. Kapatma işlemi başarısız olsa bile yük devretme devam eder.  

    > [!NOTE]
    > Hyper-V VM 'lerinin yükünü devretmek, kapatıldı, yük devretmeyi tetiklemeden önce henüz hizmete gönderilmemiş olan şirket içi verileri eşitlemeye ve çoğaltmaya çalışır. 

6. **İşler** sayfasında yük devretme ilerlemesini izleyin. Hatalar gerçekleşse bile kurtarma planı tamamlanana kadar çalışır.
7. Yük devretmeden sonra, doğrulamak için VM 'de oturum açın. 
8. Yük devretme için kullanılacak farklı bir kurtarma noktasına geçiş yapmak istiyorsanız, **değişiklik kurtarma noktasını** kullanın.
9. Hazırsanız, yük devretmeyi gerçekleştirebilirsiniz. **Kaydetme** eylemi, hizmetle kullanılabilen tüm kurtarma noktalarını siler. **Kurtarma noktasını Değiştir** seçeneği artık kullanılabilir olmayacaktır.

## <a name="run-a-planned-failover-hyper-v"></a>Planlı Yük devretmeyi çalıştırma (Hyper-V)

Hyper-V VM 'Leri için planlı bir yük devretme çalıştırabilirsiniz.

- Planlanmış bir yük devretme, sıfır veri kaybı yük devretme seçeneğidir.
- Planlı bir yük devretme tetiklendiğinde, ilk olarak kaynak sanal makineler kapatılır, en son veriler eşitlenir ve ardından bir yük devretme tetiklenir.
- Planlanmış **Yük** devretme seçeneğini kullanarak planlı yük devretme çalıştırırsınız. Düzenli yük devretmede benzer bir şekilde çalışır.
 
## <a name="track-failovers"></a>Yük devretme izleme

Yük devretmeyle ilişkili bir dizi iş vardır.

![Ad sütununda genişletilmiş Grup 1: başlangıç (1) olan Işlerin listesini gösteren Işler sayfasının ekran görüntüsü. SQLServer işinin satırı vurgulanır.](./media/site-recovery-failover/FailoverJob.png)

- **Önkoşul denetimi**: yük devretme için gereken tüm koşulların karşılanmasını sağlar.
- **Yük devretme**: verileri BIR Azure VM 'den oluşturulabilmesi için işler. **En son** kurtarma noktasını seçtiyseniz, hizmete gönderilen verilerden bir kurtarma noktası oluşturulur.
- **Başlat**: önceki adımda işlenen verileri kullanarak BIR Azure VM oluşturur.

> [!WARNING]
> **Devam eden bir yük devretme işlemini Iptal etmeyin**: yük devretme BAŞLATıLMADAN önce VM için çoğaltma durduruldu. Devam eden bir işi iptal ederseniz, yük devretme işlemi duraklar, ancak VM çoğaltılmak üzere başlamaz. Çoğaltma yeniden başlatılamaz.


### <a name="extra-failover-time"></a>Ek yük devretme süresi

Bazı durumlarda VM yük devretmesi, genellikle yaklaşık sekiz ila 10 dakika boyunca geçen ara adım gerektirir. Bunlar, bu ek adım/zamandan etkilenen makinelerdir:

* 9,8 'den eski bir Mobility hizmeti sürümü çalıştıran VMware sanal makineleri.
* Fiziksel sunucular ve fiziksel sunucu olarak korunan Hyper-V VM 'Leri.
* VMware Linux VM 'Leri.
* Bu sürücülerin önyükleme sürücüleri olarak bulunmadığı VMware VM 'Leri:
    * storvsc
    * VMBus
    * storflt
    * intelide
    * ATAPI
* DHCP veya statik IP adresleri kullanıp kullanmadığını bağımsız olarak, DHCP 'nin etkin olmadığı VMware VM 'Leri.


## <a name="automate-actions-during-failover"></a>Yük devretme sırasında eylemleri otomatikleştirme

Yük devretme sırasında eylemleri otomatikleştirmek isteyebilirsiniz. Bunu yapmak için kurtarma planlarında betikleri veya Azure Otomasyonu runbook 'larını kullanabilirsiniz.

- Betik ekleme dahil olmak üzere Kurtarma planlarını oluşturma ve özelleştirme hakkında [bilgi edinin](site-recovery-create-recovery-plans.md) .
- Kurtarma planlarına Azure Otomasyonu runbook 'ları ekleme hakkında [bilgi edinin](site-recovery-runbook-automation.md) .


## <a name="configure-settings-after-failover"></a>Yük devretmeden sonra ayarları Yapılandır

### <a name="retain-drive-letters-after-failover"></a>Yük devretme sonrasında sürücü harflerini sakla

Site Recovery sürücü harflerinin bekletilmesini yönetir. VM çoğaltma sırasında diskleri dışaktarıyorsanız, bunun nasıl çalıştığına ilişkin [bir örnek inceleyin](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) .

### <a name="prepare-in-azure-to-connect-after-failover"></a>Yük devretmeden sonra bağlanmak için Azure 'da hazırlama

RDP veya SSH kullanarak yük devretmeden sonra oluşturulan Azure VM 'lerine bağlanmak istiyorsanız, tabloda özetlenen gereksinimleri izleyin.

**Yük devretme** | **Konum** | **Eylemler**
--- | --- | ---
**Windows çalıştıran Azure VM** | Yük devretmeden sonra Azure VM |  VM için bir [ortak IP adresi ekleyin](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr).<br/><br/> Yük devredilen VM 'deki (ve bağlı olduğu Azure alt ağı) ağ güvenlik grubu kurallarının, RDP bağlantı noktasına gelen bağlantılara izin vermeniz gerekir.<br/><br/> VM 'nin ekran görüntüsünü doğrulamak için **önyükleme tanılamalarını** denetleyin.<br/><br/> Bağlanamıyorsanız, sanal makinenin çalıştığından emin olun ve bu [sorun giderme ipuçlarını](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)gözden geçirin.
**Linux çalıştıran Azure VM** | Yük devretmeden sonra Azure VM | Yük devredilen VM 'deki (ve bağlı olduğu Azure alt ağı) ağ güvenlik grubu kurallarının SSH bağlantı noktasına gelen bağlantılara izin vermeniz gerekir.<br/><br/> VM için bir [ortak IP adresi ekleyin](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr).<br/><br/> VM 'nin ekran görüntüsü için **önyükleme tanılamayı** denetleyin.<br/><br/>

Yük devretme sonrasında karşılaştığınız bağlantı sorunlarını gidermek için [burada](site-recovery-failover-to-azure-troubleshoot.md) anlatılan adımları izleyin.

## <a name="set-up-ip-addressing"></a>IP adresini ayarlama

- **Iç IP adresleri**: yük devretmeden sonra BIR Azure VM 'SININ iç IP adresini ayarlamak için birkaç seçeneğiniz vardır:
    - Aynı IP adresini sakla: Azure VM 'de şirket içi makineye ayrılan IP adresini de kullanabilirsiniz.
    - Farklı IP adresi kullan: Azure VM için farklı bir IP adresi kullanabilirsiniz.
    - İç IP adreslerini ayarlama hakkında [daha fazla bilgi edinin](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) .
- **Dış IP adresleri**: yük devretmede genel IP adreslerini koruyabilirsiniz. Yük devretme işleminin bir parçası olarak oluşturulan Azure VM 'lerine Azure bölgesinde kullanılabilir bir Azure genel IP adresi atanmalıdır. Genel bir IP adresini el ile veya işlemi bir kurtarma planıyla otomatikleştirerek atayabilirsiniz. [Daha fazla bilgi edinin](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Sonraki adımlar

Yük devretmesini tamamladıktan sonra, Azure sanal makinelerini şirket içi siteye tekrar Çoğaltmaya başlamak için yeniden korumanız gerekir. Çoğaltma çalışıyor ve çalıştıktan sonra, hazırsanız şirket içi oturum açma işlemi geri alabilirsiniz.

- Yeniden koruma ve yeniden çalışma hakkında [daha fazla bilgi edinin](failover-failback-overview.md#reprotectionfailback) .
- VMware yeniden koruma ve yeniden çalışma için [hazırlanma](vmware-azure-reprotect.md) .
- [Yeniden başarısız oldu](hyper-v-azure-failback.md) Hyper-V VM 'Leri.
- Fiziksel sunucular için yük devretme ve yeniden çalışma işlemi [hakkında bilgi edinin](physical-to-azure-failover-failback.md) .