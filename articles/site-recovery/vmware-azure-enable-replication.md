---
title: Azure Site Recovery kullanarak olağanüstü durum kurtarma için VMware VM 'lerini etkinleştirin
description: Bu makalede, Azure Site Recovery hizmeti kullanılarak olağanüstü durum kurtarma için VMware VM çoğaltmasının nasıl etkinleştirileceği açıklanır.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 1cc1ee82b45ecab17e4bcfb3a909fc90b33a1545
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954447"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>VMware VM 'Leri için Azure 'a çoğaltmayı etkinleştirme

Bu makalede, şirket içi VMware VM 'lerinin Azure 'a nasıl çoğaltılmasının nasıl etkinleştirileceği açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, olduğunu varsayar:

- Şirket [içi kaynak ortamınızı ayarlayın](vmware-azure-set-up-source.md).
- [Azure 'da hedef ortamınızı ayarlayın](vmware-azure-set-up-target.md).

## <a name="before-you-start"></a>Başlamadan önce
VMware sanal makinelerini çoğaltdığınızda, bu bilgileri göz önünde bulundurun:

* Azure Kullanıcı hesabınızın yeni bir sanal makinenin Azure 'a çoğaltılmasını sağlamak için belirli [izinlere](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) sahip olması gerekir.
* VMware VM 'Leri 15 dakikada bir bulunur. VM 'Lerin bulma sonrasında Azure portal görünmesi 15 dakika veya daha uzun sürebilir. Benzer şekilde, yeni bir vCenter Server veya vSphere Konağı eklediğinizde bulma işlemi 15 dakika veya daha uzun sürebilir.
* Sanal makinede (VMware araçları yüklemesi gibi) ortam değişikliklerinin portalda güncelleştirilmesini 15 dakika veya daha uzun sürebilir.
* VMware VM 'Leri için en son bulunan saati denetleyebilirsiniz: vCenter Server/vSphere konağının **yapılandırma sunucuları** sayfasında **yer alan son iletişim** bölümüne bakın.
* Zamanlanan bulmayı beklemeden sanal makineleri çoğaltmaya eklemek için yapılandırma sunucusunu vurgulayın (ancak tıklamayın) ve **Yenile**' yi seçin.
* Çoğaltmayı etkinleştirdiğinizde, sanal makine hazırlanmışsa, işlem sunucusu Azure Site Recovery Mobility hizmetini otomatik olarak yüklenir.

## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

Bu bölümdeki adımları izlemeden önce, aşağıdaki bilgileri aklınızda bulabilirsiniz:
* Azure Site Recovery artık tüm yeni çoğaltmalar için yönetilen disklere doğrudan çoğaltılır. İşlem sunucusu, çoğaltma günlüklerini hedef bölgedeki bir önbellek depolama hesabına yazar. Bu Günlükler, asrseeddisk adlandırma kuralına sahip olan çoğaltma tarafından yönetilen disklerde kurtarma noktaları oluşturmak için kullanılır.
* Yönetilen disklere çoğaltmaya yönelik PowerShell desteği [az. RecoveryServices Modül sürümü 2.0.0 onlenlerinden](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) edinilebilir 
* Yük devretme sırasında, seçtiğiniz kurtarma noktası, hedef tarafından yönetilen diski oluşturmak için kullanılır.
* Daha önce hedef depolama hesaplarına çoğaltılmak üzere yapılandırılmış VM 'Ler etkilenmez.
* Yeni bir sanal makine için depolama hesaplarına çoğaltma yalnızca bir temsili durum aktarımı (REST) API 'SI ve PowerShell ile kullanılabilir. Depolama hesaplarına çoğaltmak için Azure REST API 2016-08-10 veya 2018-01-10 sürümünü kullanın.

Çoğaltmayı etkinleştirmek için lütfen aşağıdaki adımları izleyin:
1. 2\. **Adım: uygulamayı çoğaltma** > **kaynağına**gidin. Çoğaltmayı ilk kez etkinleştirdikten sonra ek sanal makineler için çoğaltmayı etkinleştirmek üzere kasada **+ Çoğalt** ' ı seçin.
2. **Kaynak** sayfada > **kaynak**bölümünde yapılandırma sunucusunu seçin.
3. **Makine türü**Için **sanal makineler** veya **fiziksel makineler**' i seçin.
4. **vCenter/vSphere Hypervisor** bölümünde vSphere konağını yöneten vCenter sunucusunu veya konağı seçin. Fiziksel bilgisayarları çoğaltdıysanız Bu ayar ilgili değildir.
5. İşlem sunucusunu seçin. Başka bir işlem sunucusu oluşturulmadıysa, yapılandırma sunucusunun oluşturulmuş işlem sunucusu açılan menüde kullanılabilir olacaktır. Her işlem sunucusunun sistem durumu, önerilen sınırlara ve diğer parametrelere göre belirtilir. Sağlıklı bir işlem sunucusu seçin. [Kritik](vmware-physical-azure-monitor-process-server.md#process-server-alerts) bir işlem sunucusu seçilemez. Hataları [giderebilir ve çözümleyebilir](vmware-physical-azure-troubleshoot-process-server.md) **ya** da bir [genişleme işlem sunucusu](vmware-azure-set-up-process-server-scale.md)ayarlayabilirsiniz.
    Çoğaltma kaynağı penceresini etkinleştirmek ![](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> [9,24 sürümden](service-updates-how-to.md#links-to-currently-supported-update-rollups), işlem sunucusunun sistem durumu uyarılarını iyileştirmek için ek uyarılar sunulmuştur. Tüm uyarıların üretilmesi için Site Recovery bileşenlerini 9,24 sürüme veya üstüne yükseltin.

6. **Hedef**için, yük devredilen sanal makineleri oluşturmak istediğiniz aboneliği ve kaynak grubunu seçin. Yük devredilen VM 'Ler için Azure 'da kullanmak istediğiniz dağıtım modelini seçin.
2. Yük devretmeden sonra Azure VM 'lerinin bağlanacağı Azure ağını ve alt ağını seçin. Ağın Site Recovery hizmet kasası ile aynı bölgede olması gerekir.

   Koruma için seçtiğiniz tüm sanal makinelere ağ ayarını uygulamak için **Seçili makineler için Şimdi Yapılandır** ' ı seçin. Sanal makine başına Azure ağı seçmek için **daha sonra Yapılandır** ' ı seçin. Ağınız yoksa bir tane oluşturmanız gerekir. Azure Resource Manager kullanarak bir ağ oluşturmak için **Yeni oluştur**' u seçin. Uygulanabiliyorsa bir alt ağ seçin ve ardından **Tamam**' ı seçin.
   
   ![Çoğaltma hedefi penceresini etkinleştir](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Sanal **makineler > ** **sanal makineler**' i seçin, çoğaltmak istediğiniz her sanal makineyi seçin. Yalnızca çoğaltmanın etkinleştiribileceği sanal makineleri seçebilirsiniz. Sonra **Tamam**’ı seçin. Belirli bir sanal makineyi göremiyorsanız veya seçmezseniz, bu sorunu çözmek için [Azure Portal kaynak makine listede listelenmiyor](https://aka.ms/doc-plugin-VM-not-showing) bölümüne bakın.

    ![Çoğaltmayı etkinleştir sanal makineler penceresi seçin](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Özellikleri **yapılandırmak** > **Özellikler** için, işlem sunucusunun sanal makineye Site Recovery Mobility hizmetini otomatik olarak yüklemek için kullandığı hesabı seçin. Ayrıca, veri dalgalanma desenlerinize göre çoğaltılacak hedef yönetilen disk türünü seçin.
10. Varsayılan olarak, bir kaynak sanal makinenin tüm diskleri çoğaltılır. Diskleri çoğaltmanın dışında tutmak için, çoğaltmak istemediğiniz disklerin **dahil** etme onay kutusunu temizleyin. Sonra **Tamam**’ı seçin. Daha sonra ek özellikleri ayarlayabilirsiniz. [Diskleri dışarıda bırakma](vmware-azure-exclude-disk.md)hakkında daha fazla bilgi edinin.

    ![Çoğaltmayı etkinleştir özellikleri yapılandırma penceresi](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Çoğaltma **ayarları > ** **çoğaltma ayarlarını yapılandır**' da, doğru çoğaltma ilkesinin seçildiğini doğrulayın. Çoğaltma İlkesi ayarlarını **ayarlar** > **çoğaltma ilkeleri** > ***ilke adı*** > **Ayarları Düzenle**' de değiştirebilirsiniz. Bir ilkeye uyguladığınız değişiklikler, çoğaltma ve yeni sanal makineler için de geçerlidir.
1. Sanal makineleri bir çoğaltma grubuna toplamak istiyorsanız **Çoklu VM tutarlılığını** etkinleştirin. Grup için bir ad belirtin ve ardından **Tamam**' ı seçin.

    > [!NOTE]
    >    * Bir çoğaltma grubundaki sanal makineler birlikte çoğaltılır ve yük devreder, kilitlenmeyle tutarlı ve uygulamayla tutarlı kurtarma noktalarına sahip olur.
    >    * VM 'Leri ve fiziksel sunucuları birlikte iş yüklerinizi yansıtacak şekilde toplayın. Çoklu VM tutarlılığını etkinleştirmek, iş yükü performansını etkileyebilir. Bunu yalnızca sanal makineler aynı iş yükünü çalıştırıyorsa ve tutarlılığa ihtiyaç duyuyorsanız yapın.

    ![Çoğaltma penceresini etkinleştir](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. **Çoğaltmayı Etkinleştir** seçeneğini belirleyin. **Koruma** işinin ilerlemesini, **Site Recovery işler** > **Ayarlar** > **işler** ' de izleyebilirsiniz. **Korumayı Sonlandır** işi çalıştıktan sonra, sanal makine yük devretmeye hazırsa.

## <a name="view-and-manage-vm-properties"></a>VM özelliklerini görüntüleme ve yönetme

Ardından, kaynak sanal makinenin özelliklerini doğrulayın. Azure VM adının [Azure sanal makine gereksinimleriyle](vmware-physical-azure-support-matrix.md#replicated-machines)uyumlu olması gerektiğini unutmayın.

1. **Çoğaltılan öğeler** > **Ayarlar** ' a gidin ve ardından sanal makineyi seçin. **Essentials** SAYFASıNDA, sanal makinenin ayarları ve durumuyla ilgili bilgiler gösterilir.
1. **Özellikler** kısmında VM'nin çoğaltma ve yük devretme bilgilerini inceleyebilirsiniz.
1. **İşlem ve ağ** > **işlem özellikleri**' nde, birden çok VM özelliğini değiştirebilirsiniz. 

    ![İşlem ve ağ özellikleri penceresi](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM adı: gerekirse, Azure gereksinimlerine uyacak şekilde adı değiştirin.
    * Hedef VM boyutu veya VM türü: varsayılan VM boyutu, hedef Azure bölgesindeki disk sayısı, NIC sayısı, CPU çekirdek sayısı, bellek ve kullanılabilir VM rolü boyutları içeren birkaç parametreye göre seçilir. Azure Site Recovery tüm ölçütleri karşılayan kullanılabilir ilk VM boyutunu seçer. Yük devretmeden önce istediğiniz zaman, gereksinimlerinize göre farklı bir VM boyutu seçebilirsiniz. VM disk boyutunun aynı zamanda kaynak disk boyutuna dayandığına ve yalnızca yük devretmeden sonra değiştirilebileceğine unutmayın. [Windows ÜZERINDE VM diskleri Için ölçeklenebilirlik ve performans hedeflerinde](../virtual-machines/windows/disk-scalability-targets.md)disk BOYUTLARı ve IOPS ücretleri hakkında daha fazla bilgi edinin.

    *  Kaynak grubu: bir sanal makinenin yük devretmenin bir parçası haline geldiği bir [kaynak grubu](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)seçebilirsiniz. Bu ayarı, yük devretmeden önce istediğiniz zaman değiştirebilirsiniz. Yük devretmeden sonra, sanal makineyi farklı bir kaynak grubuna geçirirseniz bu sanal makine için koruma ayarları kesilir.
    * Kullanılabilirlik kümesi: sanal makinenizin yük devretmenin bir parçası olması gerekiyorsa bir [kullanılabilirlik kümesi](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) seçebilirsiniz. Bir kullanılabilirlik kümesi seçtiğinizde, aşağıdaki bilgileri aklınızda bulundurun:

        * Yalnızca belirtilen kaynak grubuna ait kullanılabilirlik kümeleri listelenir.  
        * Farklı sanal ağlardaki VM 'Ler aynı Kullanılabilirlik kümesinin parçası olamaz.
        * Yalnızca aynı boyuttaki sanal makineler bir kullanılabilirlik kümesinin parçası olabilir.
1. Hedef ağ, alt ağ ve Azure VM 'sine atanan IP adresi hakkında bilgi de ekleyebilirsiniz.
2. **Diskler**' de, çoğaltılacak sanal makinede işletim sistemi ve veri disklerini görebilirsiniz.

### <a name="configure-networks-and-ip-addresses"></a>Ağları ve IP adreslerini yapılandırma

Hedef IP adresini ayarlayabilirsiniz. Bir adres sağlamazsanız, yük devredilen sanal makine DHCP kullanır. Yük devretmede kullanılamayan bir adres ayarlarsanız, yük devretme çalışmaz. Adres, yük devretme testi ağında kullanılabiliyorsa, yük devretme testi için aynı hedef IP adresini kullanabilirsiniz.

Ağ bağdaştırıcılarının sayısı, hedef sanal makine için belirttiğiniz boyuta göre dikte edilir ve aşağıdaki gibi:

- Kaynak sanal makinedeki ağ bağdaştırıcılarının sayısı, hedef VM 'nin boyutu için izin verilen bağdaştırıcıların sayısından küçük veya bu sayıya eşitse, hedef, kaynak ile aynı sayıda bağdaştırıcıya sahiptir.
- Kaynak sanal makinenin bağdaştırıcı sayısı hedef VM 'nin boyutu için izin verilen sayıyı aşarsa, en fazla hedef boyutu kullanılır. Örneğin, bir kaynak sanal makinede iki ağ bağdaştırıcısı varsa ve hedef VM boyutu dördü destekliyorsa, hedef sanal makinenin iki bağdaştırıcısı vardır. Kaynak VM 'nin iki bağdaştırıcısı varsa ancak hedef boyutu yalnızca birini destekliyorsa, hedef VM 'nin yalnızca bir bağdaştırıcısı vardır.
- Sanal makinenin birden çok ağ bağdaştırıcısı varsa, hepsi aynı ağa bağlanır. Ayrıca, listede gösterilen ilk bağdaştırıcı, Azure sanal makinesinde *varsayılan* ağ bağdaştırıcısı olur. 

### <a name="azure-hybrid-benefit"></a>Azure Hibrit Avantajı

Microsoft yazılım güvencesi müşterileri, Azure 'a geçirilen Windows Server bilgisayarları için lisanslama maliyetlerinden tasarruf etmek üzere Azure Hibrit Avantajı kullanabilir. Bu avantaj Ayrıca Azure olağanüstü durum kurtarma için de geçerlidir. Uygunsanız, bir yük devretme işlemi varsa Site Recovery oluşturduğu sanal makineye avantajınızı atayabilirsiniz. Bunu yapmak için şu adımları uygulayın:
1. Çoğaltılan sanal makinenin **bilgisayar ve ağ özelliklerine** gidin.
2. Azure Hibrit Avantajı uygun hale getirmenizi sağlayan bir Windows Server lisansınız olup olmadığı sorulduğunda yanıtlayın.
3. Yük devretmede oluşturulacak VM 'nin avantajını uygulamak için kullanabileceğiniz, Yazılım Güvencesi kapsamındaki uygun bir Windows Server lisansınızın olduğunu doğrulayın.
4. Çoğaltılan sanal makinenin ayarlarını kaydedin.

[Azure hibrit avantajı](https://aka.ms/azure-hybrid-benefit-pricing)hakkında daha fazla bilgi edinin.

## <a name="resolve-common-issues"></a>Yaygın sorunları çözme

* Her disk 4 TB 'tan küçük olmalıdır.
* İşletim sistemi diski, dinamik disk değil, temel bir disk olmalıdır.
* 2\. nesil/UEFı özellikli sanal makineler için işletim sistemi ailesi Windows olmalıdır ve önyükleme diski 300 GB 'tan küçük olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Sanal makine korumalı duruma ulaştıktan sonra uygulamanızın Azure 'da görünüp görüntülenmediğini denetlemek için bir [Yük devretme](site-recovery-failover.md) işlemi deneyin.

* Çoğaltmayı devre dışı bırakmak için [kayıt ve koruma ayarlarını temizlemeyi](site-recovery-manage-registration-and-protection.md) öğrenin.
* [PowerShell kullanarak sanal makineleriniz için çoğaltmayı otomatik hale](vmware-azure-disaster-recovery-powershell.md)getirmeyi öğrenin.
