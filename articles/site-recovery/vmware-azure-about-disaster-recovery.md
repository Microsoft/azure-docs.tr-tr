---
title: Azure Site Recovery ile VMware olağanüstü durum kurtarma
description: Bu makalede, Azure Site Recovery hizmeti kullanılarak Azure 'a VMware VM 'lerinin olağanüstü durum kurtarma konusuna genel bakış sunulmaktadır.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 589dda80d68fba73a729da4b6e59270cc09c18cb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954384"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>VMware VM 'lerinin Azure 'a olağanüstü durum kurtarması hakkında

Bu makalede, şirket içi VMware VM 'lerinin [Azure Site Recovery](site-recovery-overview.md) hizmeti kullanılarak Azure 'a olağanüstü durum kurtarma hakkında genel bakış sunulmaktadır.

## <a name="what-is-bcdr"></a>BCDR nedir?

İş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejisi, işinizi çalışır durumda tutmanıza yardımcı olur. Planlanan kapalı kalma süresi ve beklenmedik kesintiler sırasında, BCDR verileri güvenli ve kullanılabilir tutar ve uygulamaların çalışmaya devam etmesini sağlar. Azure, bölgesel eşleştirme ve yüksek kullanılabilirliğe sahip depolama gibi platform BCDR özelliklerine ek olarak, kurtarma hizmetleri için BCDR çözümünüzün bir parçası olarak sağlanır. Kurtarma Hizmetleri şunları içerir: 

- [Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) , şirket Içi ve Azure VM verilerinizi yedekler. Bir dosya ve klasörleri, belirli iş yüklerini veya tüm VM 'leri yedekleyebilirsiniz. 
- [Azure Site Recovery](site-recovery-overview.md) , şirket içi makinelerde çalışan uygulamalar ve iş yükleri ya da Azure IaaS VM 'leri için esnekliği ve olağanüstü durum kurtarma sağlar. Site Recovery çoğaltmayı düzenler ve kesintiler gerçekleştiğinde Azure 'a yük devretmeyi işler. Ayrıca, Azure 'dan birincil sitenize kurtarmayı işler. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Site Recovery olağanüstü durum kurtarma nasıl yapılır?

1. Azure 'u ve şirket içi sitenizi hazırladıktan sonra, şirket içi makineleriniz için çoğaltmayı ayarlayıp etkinleştirin.
2. Site Recovery, ilke ayarlarınıza uygun olarak makinenin ilk çoğaltmasını düzenler.
3. İlk çoğaltmadan sonra, Site Recovery Delta değişikliklerini Azure 'a çoğaltır. 
4. Her şey beklendiği gibi çoğaltıldığında, bir olağanüstü durum kurtarma detayına sahip olursunuz.
    - Detaya gitme işlemi, gerçek bir ihtiyaç olduğunda yük devretmenin beklendiği gibi çalışmasını sağlamaya yardımcı olur.
    - Detaya gitme, üretim ortamınızı etkilemeden yük devretme testi gerçekleştirir.
5. Bir kesinti oluşursa, Azure 'a tam yük devretme çalıştırırsınız. Tek bir makinenin yükünü devretmek veya aynı anda birden fazla makine üzerinde başarısız olan bir kurtarma planı da oluşturabilirsiniz.
6. Yük devretmede, yönetilen diskler veya depolama hesaplarındaki VM verilerinden Azure VM 'Leri oluşturulur. Kullanıcılar, Azure VM 'den uygulama ve iş yüklerine erişmeye devam edebilir
7. Şirket içi siteniz yeniden kullanılabilir olduğunda Azure 'dan yeniden yük devreolursunuz.
8. Yeniden başarısız olduktan ve birincil sitenizden daha sonra çalıştıktan sonra şirket içi VM 'Leri tekrar Azure 'a çoğaltmaya başlayabilirsiniz.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Nasıl yaparım?, ortamınızın Azure 'a olağanüstü durum kurtarma için uygun olup olmadığını bilmelidir mi?

Site Recovery, desteklenen bir VMware VM veya fiziksel sunucu üzerinde çalışan herhangi bir iş yükünü çoğaltabilir. Ortamınızda denetlemeniz gereken işlemler şunlardır:

- VMware VM 'lerini çoğaltdıysanız, VMware sanallaştırma sunucularının doğru sürümlerini çalıştırıyor musunuz? [Buraya bakın](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Çoğaltmak istediğiniz makineler desteklenen bir işletim sistemi çalıştırıyor mu? [Buraya bakın](vmware-physical-azure-support-matrix.md#replicated-machines).
- Linux olağanüstü durum kurtarma için desteklenen bir dosya sistemi/Konuk depolama çalıştıran makineler mi? [Buraya bakın](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Çoğaltmak istediğiniz makineler Azure gereksinimleriyle uyumlu değil mi? [Buraya bakın](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Ağ yapılandırmanız destekleniyor mu? [Buraya bakın](vmware-physical-azure-support-matrix.md#network).
- Depolama yapılandırmanız destekleniyor mu? [Buraya bakın](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Başlamadan önce Azure 'da ayarlama yapmam gerekenler nelerdir?

Azure 'da şunları hazırlamanız gerekir:

1. Azure hesabınızın Azure 'da VM oluşturma izinlerine sahip olduğunu doğrulayın.
2. Azure VM 'lerinin, yük devretmeden sonra depolama hesaplarından veya yönetilen disklerden oluşturulduklarında katılabilecekleri bir Azure ağı oluşturun.
3. Site Recovery için bir Azure kurtarma hizmetleri Kasası ayarlayın. Kasa Azure portal bulunur ve Site Recovery dağıtımınızı dağıtmak, yapılandırmak, düzenlemek, izlemek ve sorunlarını gidermek için kullanılır.

*Daha fazla yardım mı gerekiyor?*

[Hesabınızı doğrulayarak](tutorial-prepare-azure.md#verify-account-permissions), bir [ağ](tutorial-prepare-azure.md#set-up-an-azure-network)oluşturup [bir kasa ayarlayarak](tutorial-prepare-azure.md#create-a-recovery-services-vault)Azure 'u ayarlamayı öğrenin.



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Başlamadan önce şirket içi olarak ne yapmam gerekir?

Şirket içinde şunları yapmanız gerekir:

1. Birkaç hesap ayarlamanız gerekir:

    - VMware VM 'lerini çoğaltırken, VM 'Leri otomatik olarak bulması için Site Recovery vCenter Server veya vSphere ESXi konaklarına erişim için bir hesap gerekir.
    - Çoğaltmak istediğiniz her fiziksel makineye veya VM 'ye Site Recovery Mobility hizmet Aracısı 'nı yüklemek için bir hesap gerekir.

2. Daha önce yapmadıysanız, VMware altyapınızın uyumluluğunu denetlemeniz gerekir.
3. Yük devretmeden sonra Azure VM 'lerine bağlanabildiğinizden emin olun. Şirket içi Windows makinelerde RDP veya Linux makinelerde SSH ayarlarsınız.

*Daha fazla yardım mı gerekiyor?*
- [Otomatik bulma](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) ve [Mobility hizmetinin yüklenmesi](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation)için hesapları hazırlayın.
- VMware ayarlarınızın uyumlu olduğunu [doğrulayın](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) .
- Yük devretmeden sonra Azure 'a bağlanmanız için [hazırlayın](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) .
- Yük devretmeden sonra Azure VM 'Leri için IP adresleme ayarlama hakkında daha ayrıntılı yardım istiyorsanız [Bu makaleyi okuyun](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Olağanüstü durum kurtarma Nasıl yaparım? mı?

Azure ve şirket içi altyapınızı aldıktan sonra olağanüstü durum kurtarma ayarlayabilirsiniz.

1. Dağıtmanız gereken bileşenleri anlamak için, [VMware 'Den Azure mimarisine](vmware-azure-architecture.md)ve [fizikselden Azure mimarisine](physical-azure-architecture.md)gözden geçirin. Birçok bileşen vardır. bu nedenle, bunların tümünün nasıl bir araya uyduğunu anlamak önemlidir.
2. **Kaynak ortam**: dağıtımda ilk bir adım olarak, çoğaltma kaynak ortamınızı ayarlarsınız. Ne çoğaltmak istediğinizi, ne de çoğaltmak istediğinizi belirtirsiniz.
3. **Yapılandırma sunucusu**: şirket içi kaynak ortamınızda bir yapılandırma sunucusu ayarlamanız gerekir:
    - Yapılandırma sunucusu, tek bir şirket içi makinedir. VMware olağanüstü durum kurtarma için, dosyayı indirilebilir bir OVF şablonundan dağıtılabilecek bir VMware VM olarak dağıtmanızı öneririz.
    - Yapılandırma sunucusu, şirket içi ve Azure arasındaki iletişimleri koordine eder
    - Yapılandırma sunucusu makinesinde çalışan birkaç bileşen vardır.
        - İşlem sunucusu, Azure 'daki önbellek depolama hesabına çoğaltma verileri alır, iyileştirir ve gönderir. Ayrıca, çoğaltmak istediğiniz makinelere Mobility hizmetinin otomatik olarak yüklenmesini işler ve VMware sunucularında sanal makinelerin otomatik olarak bulunmasını gerçekleştirir.
        - Ana hedef sunucu, Azure'dan yeniden çalışma sırasında çoğaltma verilerini işler.
    - Ayarla ayarı, kasadaki yapılandırma sunucusunu kaydetmek, MySQL Server ve VMware PowerCLI 'yi indirmek ve otomatik bulma ve Mobility hizmeti yüklemesi için oluşturulan hesapları belirtmek içerir.
4. **Hedef ortam**: Azure aboneliğinizi ve ağ ayarlarınızı belirterek hedef Azure ortamınızı ayarlarsınız.
5. **Çoğaltma İlkesi**: çoğaltmanın nasıl gerçekleşmesi gerektiğini belirtirsiniz. Ayarlar, kurtarma noktalarının ne sıklıkta oluşturulup depolandığını ve uygulamayla tutarlı anlık görüntülerin oluşturulup oluşturulmayacağını içerir.
6. **Çoğaltmayı etkinleştirin**. Şirket içi makineler için çoğaltmayı etkinleştirirsiniz. Mobility hizmetini yüklemek için bir hesap oluşturduysanız, bir makine için çoğaltmayı etkinleştirdiğinizde yüklenir. 

*Daha fazla yardım mı gerekiyor?*

- Bu adımlara hızlı bir şekilde bir anlatım için, [VMware Öğreticimizi](vmware-azure-tutorial.md)ve [fiziksel sunucu gözden geçirmeyi](physical-azure-disaster-recovery.md)deneyebilirsiniz.
- Kaynak ortamınızı ayarlama hakkında [daha fazla bilgi edinin](vmware-azure-set-up-source.md) .
- Yapılandırma sunucusu gereksinimleri [hakkında bilgi edinin](vmware-azure-deploy-configuration-server.md) ve VMware çoğaltması için bir OVF şablonuyla yapılandırma sunucusunu ayarlama. Bir şablon kullanmıyorsanız veya fiziksel sunucuları çoğaltdıysanız, [Bu yönergeleri kullanın](physical-azure-set-up-source.md#set-up-the-source-environment).
- Hedef ayarları hakkında [daha fazla bilgi edinin](vmware-azure-set-up-target.md) .
- Çoğaltma İlkesi ayarlama hakkında [daha fazla bilgi alın](vmware-azure-set-up-replication.md) .
- Çoğaltmayı etkinleştirme ve diskleri çoğaltmanın [dışında bırakma](vmware-azure-exclude-disk.md) hakkında [bilgi edinin](vmware-azure-enable-replication.md) .


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Bir sorun oluştu, nasıl sorun giderebilirim?

- İlk adım olarak, çoğaltılan öğelerin, işlerin ve altyapı sorunlarının durumunu doğrulamak ve hataları belirlemek için [dağıtımınızı izlemeyi](site-recovery-monitor-and-troubleshoot.md) deneyin.
- İlk çoğaltmayı tamamlayamazsa veya devam eden çoğaltma beklendiği gibi çalışmıyorsa, yaygın hatalar ve sorun giderme ipuçları için [Bu makaleyi gözden geçirin](vmware-azure-troubleshoot-replication.md) .
- Çoğaltmak istediğiniz makinelere Mobility hizmetinin otomatik yüklemesiyle ilgili sorun yaşıyorsanız, [Bu makaledeki](vmware-azure-troubleshoot-push-install.md)yaygın hataları gözden geçirin.
- Yük devretme beklendiği gibi çalışmıyorsa, [Bu makaledeki](site-recovery-failover-to-azure-troubleshoot.md)yaygın hataları kontrol edin.
- Yeniden çalışma çalışmıyorsa, sorununuzun [Bu makalede](vmware-azure-troubleshoot-failback-reprotect.md)görünüp görüntülenmediğini denetleyin.



## <a name="next-steps"></a>Sonraki adımlar

Çoğaltma artık yerinde olduğunda, yük devretmenin beklendiği gibi çalıştığından emin olmak için [bir olağanüstü durum kurtarma detayına](tutorial-dr-drill-azure.md) sahip olmanız gerekir. 
