---
title: Azure Site Recovery 'de Azure 'a yük devretme testi (olağanüstü durum kurtarma detayına) çalıştırın
description: Azure Site Recovery hizmetini kullanarak şirket içinden Azure 'a yük devretme testi çalıştırma hakkında bilgi edinin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 26c734b7a2e9f5592ee6d51dfee4650a3998ab1a
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091735"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Azure 'a yük devretme testi (olağanüstü durum kurtarma detayına) çalıştırma 


Bu makalede, bir Site Recovery yük devretme testi kullanarak Azure 'da olağanüstü durum kurtarma detayının nasıl çalıştırılacağı açıklanmaktadır.  

Veri kaybı veya kapalı kalma süresi olmadan çoğaltma ve olağanüstü durum kurtarma stratejinizi doğrulamak için bir yük devretme testi çalıştırırsınız. Yük devretme testi, devam eden çoğaltmayı veya üretim ortamınızı etkilemez. Belirli bir sanal makinede (VM) veya birden çok VM içeren bir [kurtarma planında](site-recovery-create-recovery-plans.md) yük devretme testi çalıştırabilirsiniz.


## <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma
Bu yordam, bir kurtarma planı için yük devretme testi çalıştırma işlemini açıklar. Tek bir VM için yük devretme testi çalıştırmak istiyorsanız, [burada](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm) açıklanan adımları izleyin.

![Test Yük Devretmesi](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Azure portal Site Recovery > **Yük devretme testi** > *recoveryplan_name* **kurtarma planları** ' na tıklayın.
2. Yük devredilecek bir **Kurtarma noktası** seçin. Aşağıdaki seçeneklerden birini kullanabilirsiniz:
    - **En son işlenen**: Bu seçenek, plandaki tüm vm 'leri Site Recovery tarafından işlenen en son kurtarma noktasına devreder. Belirli bir sanal makine için en son kurtarma noktasını görmek üzere VM ayarlarındaki **en son kurtarma noktalarını** kontrol edin. İşlenmemiş verileri işlemek için zaman harcanmadığından bu seçenekte düşük bir RTO (Kurtarma Süresi Hedefi) sağlanır.
    - **En son uygulamayla tutarlı**: Bu seçenek, plandaki tüm VM 'leri, Site Recovery tarafından işlenen en son uygulamayla tutarlı kurtarma noktasına devreder. Belirli bir sanal makine için en son kurtarma noktasını görmek üzere VM ayarlarındaki **en son kurtarma noktalarını** kontrol edin.
    - **En son**: Bu seçenek ilk olarak Site Recovery hizmetine gönderilen tüm verileri işleyerek, yük devretmadan önce her VM için bir kurtarma noktası oluşturur. Yük devretmeden sonra oluşturulan VM 'nin, yük devretme tetiklendiğinde Site Recovery için tüm verileri çoğaltılacağı için, bu seçenek en düşük RPO (kurtarma noktası hedefi) sağlar.
    - **Işlenen en son çoklu VM**: Bu seçenek, çoklu VM tutarlılığı etkinleştirilmiş bir veya daha fazla VM 'ye sahip kurtarma planlarında kullanılabilir. Ayarı etkin olan VM 'Ler, en son ortak çoklu VM tutarlı kurtarma noktasına yük devreder. Diğer VM 'Ler, işlenen en son kurtarma noktasına yük devreder.  
    - **En son çoklu VM ile uygulamayla tutarlı**: Bu seçenek, çoklu VM tutarlılığı etkinleştirilmiş bir veya daha fazla VM 'ye sahip kurtarma planları için kullanılabilir. Bir çoğaltma grubunun parçası olan VM 'Ler, en son ortak çoklu VM uygulamayla tutarlı kurtarma noktasına yük devreder. Diğer VM 'Ler, uygulamayla tutarlı en son kurtarma noktasına yük devreder.
    - **Özel**: belırlı bir VM 'nin yükünü belirli bir kurtarma noktasına devretmek için bu seçeneği kullanın.
3. Test VM 'lerinin oluşturulacağı bir Azure sanal ağı seçin.

    - Site Recovery, VM 'nin **işlem ve ağ** ayarlarında belirtilen aynı ada ve aynı IP adresine sahip bir alt ağda test VM 'leri oluşturmaya çalışır.
    - Yük devretme testi için kullanılan Azure sanal ağında aynı ada sahip bir alt ağ yoksa, test sanal makinesi alfabetik olarak ilk alt ağda oluşturulur.
    - Alt ağda aynı IP adresi yoksa, VM alt ağda kullanılabilir başka bir IP adresi alır. [Daha fazla bilgi edinin](#create-a-network-for-test-failover).
4. Azure 'a yük devreyorsanız ve veri şifreleme etkinse, **şifreleme anahtarı**' nda, sağlayıcı yüklemesi sırasında şifrelemeyi etkinleştirdiğinizde verilen sertifikayı seçin. Şifreleme etkinleştirilmemişse, bu adımı yoksayabilirsiniz.
5. **İşler** sekmesinde yük devretme ilerlemesini izleyin. Test çoğaltma makinesini Azure portal görebilmeniz gerekir.
6. Azure VM 'ye bir RDP bağlantısı başlatmak için, yük devredilen VM 'nin ağ arabirimine [bir genel IP adresi eklemeniz](https://aka.ms/addpublicip) gerekir.
7. Her şey beklendiği gibi çalıştığında, **Yük devretme testini temizle**' ye tıklayın. Bu, yük devretme testi sırasında oluşturulan VM 'Leri siler.
8. Yük devretme testiyle ilişkili gözlemlerinizi **Notlar**’da kaydedin veya saklayın.


![Test Yük Devretmesi](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Yük devretme testi tetiklendiğinde aşağıdakiler gerçekleşir:

1. **Önkoşullar**: yük devretme için gereken tüm koşulların karşılandığından emin olmak için bir önkoşul denetimi çalıştırılır.
2. **Yük devretme**: yük devretme işlemi, verileri BIR Azure VM 'nin oluşturulabilmesi için işler ve hazırlandı.
3. **En son**: en son kurtarma noktasını seçtiyseniz, hizmete gönderilen verilerden bir kurtarma noktası oluşturulur.
4. **Başlangıç**: Bu adım, önceki adımda işlenen verileri kullanarak bir Azure sanal makinesi oluşturur.

### <a name="failover-timing"></a>Yük devretme zamanlaması

Aşağıdaki senaryolarda, yük devretme işlemi genellikle 8 ila 10 dakikalık bir süre içinde tamamlanır ek bir ara adım gerektirir:

* Mobility hizmetinin 9,8 'den eski bir sürümünü çalıştıran VMware VM 'Leri
* Fiziksel sunucular
* VMware Linux VM 'Leri
* Fiziksel sunucu olarak korunan Hyper-V VM
* Aşağıdaki sürücülerin önyükleme sürücüleri olmadığı VMware VM:
    * storvsc
    * VMBus
    * storflt
    * intelide
    * ATAPI
* DHCP veya statik IP adresleri mi kullandıklarından bağımsız olarak, DHCP 'nin etkin olmadığı VMware VM.

Diğer tüm durumlarda, ara adım gerekmez ve yük devretme önemli ölçüde daha az zaman alır.


## <a name="create-a-network-for-test-failover"></a>Yük devretme testi için ağ oluşturma

Yük devretme testi için her VM'nin **İşlem ve Ağ** ayarlarından üretim kurtarma sitesi ağınızdan yalıtılmış bir ağ seçmenizi öneririz. Varsayılan olarak yeni oluşturduğunuz Azure sanal ağları diğer ağlardan yalıtılmış olur. Test ağı üretim ağınıza benzer olmalıdır:

- Test ağında üretim ağınızdaki sayıda alt ağ olmalıdır. Alt ağların adı aynı olmalıdır.
- Test ağında aynı IP adresi aralığı kullanılmalıdır.
- Test ağının DNS sunucusunu **İşlem ve Ağ** ayarlarında DNS VM için belirtilen IP adresiyle güncelleştirin. Daha fazla ayrıntı için [Active Directory için yük devretme testi ile ilgili dikkat edilmesi gerekenler](site-recovery-active-directory.md#test-failover-considerations) sayfasını okuyun.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Kurtarma sitesindeki bir üretim ağına yük devretme testi

Üretim ağınızdan ayrı bir test ağı kullanmanız önerilse de, üretim ağınıza bir olağanüstü durum kurtarma detayına test etmek istiyorsanız, aşağıdakileri unutmayın:

- Yük devretme testini çalıştırdığınızda Birincil VM 'nin kapatıldığından emin olun. Aksi takdirde aynı kimliğe sahip iki sanal makine olur ve aynı anda aynı ağda çalışır. Bu, beklenmeyen sonuçlara neden olabilir.
- Yük devretme testi için oluşturulan VM 'Lerde yapılan değişiklikler, yük devretmeyi temizleyene kadar kaybedilir. Bu değişiklikler birincil VM 'ye geri çoğaltılmaz.
- Üretim ortamınızda test edilmesi, üretim uygulamanızın kapalı kalma süresine yol açar. Yük devretme testi devam ederken, kullanıcıların VM 'lerde çalışan uygulamaları kullanması gerekmez.  



## <a name="prepare-active-directory-and-dns"></a>Active Directory ve DNS 'yi hazırla

Uygulama testi için yük devretme testi çalıştırmak için, test ortamınızda üretim Active Directory ortamınızın bir kopyasına ihtiyacınız vardır. Daha fazla bilgi edinmek için [Active Directory için yük devretme testi konularını](site-recovery-active-directory.md#test-failover-considerations) okuyun.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Yük devretmeden sonra Azure VM'lerine bağlanmak için hazırlık yapma

Yük devretmeden sonra RDP/SSH kullanarak Azure VM 'lerine bağlanmak istiyorsanız, tabloda özetlenen gereksinimleri izleyin.

**Yük devretme** | **Konum** | **Eylemler**
--- | --- | ---
**Windows çalıştıran Azure VM** | Yük devretmeden önce şirket içi makine | Azure VM 'ye internet üzerinden erişmek için RDP 'yi etkinleştirin ve TCP ve UDP kurallarının **genel**'e eklendiğinden emin olun ve **Windows Güvenlik Duvarı** 'nda **izin verilen uygulamalar** > tüm profiller için RDP 'ye izin verildiğinden emin olun.<br/><br/> Azure VM 'ye siteden siteye bağlantı üzerinden erişmek için makinede RDP 'yi etkinleştirin ve **Windows Güvenlik Duvarı** 'Nda **etki alanı ve özel** ağlar için **izin VERILEN uygulamalar ve Özellikler** -> RDP 'ye izin verildiğinden emin olun.<br/><br/>  İşletim sistemi SAN ilkesinin **OnlineAll**olarak ayarlandığından emin olun. [Daha fazla bilgi edinin](https://support.microsoft.com/kb/3031135).<br/><br/> Yük devretme tetiklemeniz sırasında VM 'de bekleyen bir Windows güncelleştirmesi olmadığından emin olun. Yük devretmek için Windows Update başlayabilir ve güncelleştirme tamamlanana kadar VM 'de oturum açamazsınız.
**Windows çalıştıran Azure VM** | Yük devretmeden sonra Azure VM |  VM için bir [ortak IP adresi ekleyin](https://aka.ms/addpublicip).<br/><br/> Yük devredilen VM 'deki (ve bağlı olduğu Azure alt ağı) ağ güvenlik grubu kurallarının, RDP bağlantı noktasına gelen bağlantılara izin vermeniz gerekir.<br/><br/> VM 'nin ekran görüntüsünü doğrulamak için **önyükleme tanılamalarını** denetleyin.<br/><br/> Bağlanamıyorsanız, sanal makinenin çalıştığından emin olun ve bu [sorun giderme ipuçlarını](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)gözden geçirin.
**Linux çalıştıran Azure VM** | Yük devretmeden önce şirket içi makine | VM 'deki Secure Shell hizmetinin sistem önyüklemesi üzerinde otomatik olarak başlayacak şekilde ayarlandığından emin olun.<br/><br/> Güvenlik duvarı kurallarının gerçekleştirilecek SSH bağlantısına izin verdiğinden emin olun.
**Linux çalıştıran Azure VM** | Yük devretmeden sonra Azure VM | Yük devredilen VM 'deki (ve bağlı olduğu Azure alt ağı) ağ güvenlik grubu kurallarının SSH bağlantı noktasına gelen bağlantılara izin vermeniz gerekir.<br/><br/> VM için bir [ortak IP adresi ekleyin](https://aka.ms/addpublicip).<br/><br/> VM 'nin ekran görüntüsü için **önyükleme tanılamayı** denetleyin.<br/><br/>

Yük devretme sonrasında karşılaştığınız bağlantı sorunlarını gidermek için [burada](site-recovery-failover-to-azure-troubleshoot.md) anlatılan adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar
Olağanüstü durum kurtarma detaya gitmeyi tamamladıktan sonra diğer [Yük devretme](site-recovery-failover.md)türleri hakkında daha fazla bilgi edinin.
