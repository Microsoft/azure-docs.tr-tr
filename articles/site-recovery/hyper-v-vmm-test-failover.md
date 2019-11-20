---
title: Azure Site Recovery bir ikincil siteye bir NHyper-V olağanüstü durum kurtarma detayına git çalıştır
description: Azure Site Recovery kullanarak ikincil şirket içi veri merkezine VMM bulutlarında Hyper-V VM 'Leri için bir DR detayına nasıl çalıştırılacağını öğrenin.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 0363911574a076b13cb72591fb2564364e096c76
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132948"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>İkincil siteye Hyper-V VM 'Leri için bir DR ayrıntısı çalıştırın


Bu makalede, System Center Virtual Machine Manager V (MM) bulutlarında yönetilen Hyper-V VM 'lerinin, [Azure Site Recovery](site-recovery-overview.md)kullanarak ikincil şirket içi bir siteye nasıl bir olağanüstü durum kurtarma (Dr) detayına nasıl yapılacağı açıklanır.

Çoğaltma stratejinizi doğrulamak için bir yük devretme testi çalıştırırsınız ve veri kaybı veya kapalı kalma süresi olmadan bir DR detayına sahip olursunuz. Yük devretme testi, devam eden çoğaltma üzerinde veya üretim ortamınızda herhangi bir etkiye sahip değildir. 

## <a name="how-do-test-failovers-work"></a>Yük devretme testi nasıl çalışır?

Birincil siteden ikincil siteye yük devretme testi çalıştırırsınız. Yalnızca bir VM 'nin yük devretmesinin başarısız olduğunu denetlemek istiyorsanız, ikincil sitede herhangi bir şeyi ayarlamadan test yük devretmesini çalıştırabilirsiniz. Uygulamanın yük devretmesini beklendiği gibi çalıştığını doğrulamak istiyorsanız, ikincil konumda ağ ve altyapı ayarlamanız gerekir.
- Yük devretme testini tek bir VM 'de veya bir [kurtarma planında](site-recovery-create-recovery-plans.md)çalıştırabilirsiniz.
- Ağ olmadan, mevcut bir ağla veya otomatik olarak oluşturulan bir ağla yük devretme testi çalıştırabilirsiniz. Bu seçenekler hakkında daha fazla ayrıntı aşağıdaki tabloda verilmiştir.
    - Ağ olmadan yük devretme testi çalıştırabilirsiniz. Bu seçenek, yalnızca bir VM 'nin yük devredebildiğini denetlemek istiyorsanız yararlıdır, ancak herhangi bir ağ yapılandırmasını doğrulayamayacağız.
    - Yük devretmeyi mevcut bir ağla çalıştırın. Üretim ağını kullanmanızı öneririz.
    - Yük devretmeyi çalıştırın ve Site Recovery otomatik olarak bir test ağı oluşturmasını sağlayın. Bu durumda Site Recovery, ağ otomatik olarak oluşturulur ve test yük devretmesi tamamlandığında bu işlemi temizler.
- Yük devretme testi için bir kurtarma noktası seçmeniz gerekir: 
    - **En son işlenen**: Bu seçenek, Site Recovery tarafından işlenen en son kurtarma noktasına yük devreder. İşlenmemiş verileri işlemek için zaman harcanmadığından bu seçenekte düşük bir RTO (Kurtarma Süresi Hedefi) sağlanır.
    - **En son uygulamayla tutarlı**: Bu seçenek, Site Recovery tarafından işlenen en son uygulamayla tutarlı kurtarma noktasına bir VM 'nin yükünü devreder. 
    - **En son**: Bu seçenek ilk olarak Site Recovery hizmetine gönderilen tüm verileri işleyerek, yük devretmadan önce her VM için bir kurtarma noktası oluşturur. Yük devretmeden sonra oluşturulan VM 'nin, yük devretme tetiklendiğinde Site Recovery için tüm verileri çoğaltılacağı için, bu seçenek en düşük RPO (kurtarma noktası hedefi) sağlar.
    - **Işlenen en son çoklu VM**: çoklu VM tutarlılığı etkinleştirilmiş bir veya daha fazla VM içeren kurtarma planları için kullanılabilir. Ayarı etkin olan VM 'Ler, en son ortak çoklu VM tutarlı kurtarma noktasına yük devreder. Diğer VM 'Ler, işlenen en son kurtarma noktasına yük devreder.
    - **En son çoklu VM ile uygulamayla tutarlı**: Bu seçenek, çoklu VM tutarlılığı etkinleştirilmiş bir veya daha fazla VM 'ye sahip kurtarma planları için kullanılabilir. Bir çoğaltma grubunun parçası olan VM 'Ler, en son ortak çoklu VM uygulamayla tutarlı kurtarma noktasına yük devreder. Diğer VM 'Ler, uygulamayla tutarlı en son kurtarma noktasına yük devreder.
    - **Özel**: belırlı bir VM 'nin yükünü belirli bir kurtarma noktasına devretmek için bu seçeneği kullanın.



## <a name="prepare-networking"></a>Ağı hazırlama

Yük devretme testi çalıştırdığınızda, tabloda özetlenen test çoğaltma makineleri için ağ ayarları ' nı seçmeniz istenir.

| **Seçenek** | **Ayrıntılar** | |
| --- | --- | --- |
| **Yok.** | Test VM 'si, çoğaltma sanal makinesinin bulunduğu konakta oluşturulur. Buluta eklenmez ve herhangi bir ağa bağlı değildir.<br/><br/> Oluşturulduktan sonra makinenin bir VM ağına bağlanmasını sağlayabilirsiniz.| |
| **Mevcut olanı kullan** | Test VM 'si, çoğaltma sanal makinesinin bulunduğu konakta oluşturulur. Buluta eklenmez.<br/><br/>Üretim ağınızdan yalıtılmış bir VM ağı oluşturun.<br/><br/>VLAN tabanlı bir ağ kullanıyorsanız, bu amaçla VMM 'de ayrı bir mantıksal ağ (üretimde kullanılmamış) oluşturmanızı öneririz. Bu mantıksal ağ yük devretme testi için VM ağları oluşturmak için kullanılır.<br/><br/>Mantıksal ağ, sanal makineleri barındıran tüm Hyper-V sunucularının en az bir ağ bağdaştırıcısı ile ilişkilendirilmelidir.<br/><br/>VLAN mantıksal ağları için, mantıksal ağa eklediğiniz ağ siteleri yalıtılmalıdır.<br/><br/>Windows ağ sanallaştırma tabanlı bir mantıksal ağ kullanıyorsanız, Azure Site Recovery otomatik olarak yalıtılmış VM ağları oluşturur. | |
| **Ağ oluşturma** | Geçici bir test ağı, **mantıksal ağda** ve ilgili ağ sitelerinde belirttiğiniz ayara göre otomatik olarak oluşturulur.<br/><br/> Yük devretme, VM 'Lerin oluşturulduğunu denetler.<br/><br/> Bir kurtarma planı birden fazla VM ağı kullanıyorsa bu seçeneği kullanmanız gerekir.<br/><br/> Windows ağ sanallaştırma ağlarını kullanıyorsanız, bu seçenek çoğaltma sanal makinesi ağındaki aynı ayarlarla (alt ağlar ve IP adresi havuzları) VM ağları otomatik olarak oluşturabilir. Bu VM ağları, sınama yük devretmesi tamamlandıktan sonra otomatik olarak temizlenir.<br/><br/> Test VM 'si, çoğaltma sanal makinesinin bulunduğu konakta oluşturulur. Buluta eklenmez.|

### <a name="best-practices"></a>En iyi uygulamalar

- Üretim ağının sınanması, üretim iş yükleri için kapalı kalma süresine neden olur. Olağanüstü durum kurtarma detayına devam edildiğinde kullanıcılarınızın ilgili uygulamaları kullanmamasını isteyin.

- Sınama, yük devretme testi için kullanılan VMM mantıksal ağ türüyle eşleşmek zorunda değildir. Ancak bazı birleşimler çalışmaz:

     - Çoğaltma DHCP ve VLAN tabanlı yalıtım kullanıyorsa, çoğaltma için VM ağının statik bir IP adresi havuzu olması gerekmez. Bu nedenle, kullanılabilir bir adres havuzu bulunmadığından, yük devretme testi için Windows ağ sanallaştırma 'nin kullanılması çalışmaz. 
        
     - Çoğaltma ağı bir yalıtım kullanıyorsa ve test ağı Windows ağ sanallaştırma kullanıyorsa, yük devretme testi çalışmaz. Bunun nedeni, yalıtımsız ağın Windows ağ sanallaştırma ağı oluşturmak için gereken alt ağları yoktur.
        
- Yük devretme testi için ağ eşleme için seçtiğiniz ağı kullanmanız önerilmez.

- Yük devretme sonrasında, çoğaltma sanal makinelerinin eşlenmiş VM ağlarına bağlanması, VM ağının VMM konsolunda nasıl yapılandırıldığına bağlıdır.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>VM ağı, yalıtımsız veya VLAN yalıtımı olmadan yapılandırıldı

VMM 'de bir VM ağı yalıtımsız veya VLAN yalıtımsız yapılandırılmışsa, aşağıdakilere göz önünde bulunur:

- VM ağı için DHCP tanımlanmışsa, çoğaltma sanal makinesi, ilişkili mantıksal ağdaki ağ alanı için belirtilen ayarlar aracılığıyla VLAN KIMLIĞINE bağlanır. Sanal makine, IP adresini kullanılabilir DHCP sunucusundan alır.
- Hedef VM ağı için bir statik IP adresi havuzu tanımlamanız gerekmez. VM ağı için bir statik IP adresi havuzu kullanılıyorsa, çoğaltma sanal makinesi, ilişkili mantıksal ağdaki ağ alanı için belirtilen ayarlar aracılığıyla VLAN KIMLIĞINE bağlanır.
- Sanal makine, VM ağı için tanımlanan havuzdan IP adresini alır. Hedef VM ağında statik bir IP adresi havuzu tanımlanmamışsa, IP adresi ayırma başarısız olur. Koruma ve kurtarma için kullanacağınız kaynak ve hedef VMM sunucuları üzerinde IP adresi havuzu oluşturun.

### <a name="vm-network-with-windows-network-virtualization"></a>Windows ağ sanallaştırma ile VM ağı

VMM 'de Windows ağ sanallaştırma ile bir VM ağı yapılandırılmışsa, aşağıdakileri aklınızda yapın:

- Kaynak VM ağının DHCP veya statik bir IP adresi havuzu kullanacak şekilde yapılandırılıp yapılandırılmadığını fark etmeksizin, hedef VM ağı için bir statik havuz tanımlamanız gerekir. 
- DHCP tanımlarsanız, hedef VMM sunucusu bir DHCP sunucusu işlevi görür ve hedef VM ağı için tanımlanan havuzdan bir IP adresi sağlar.
- Kaynak sunucu için bir statik IP adresi havuzu kullanılıyorsa, hedef VMM sunucusu havuzdan bir IP adresi ayırır. Her iki durumda da statik bir IP adresi havuzu tanımlanmamışsa IP adresi ayırması başarısız olur.



## <a name="prepare-the-infrastructure"></a>Altyapıyı hazırlama

Yalnızca bir VM 'nin yük devredebildiğini denetlemek isterseniz, bir altyapı olmadan yük devretme testi çalıştırabilirsiniz. Uygulama yük devretmesini test etmek için tam bir DR detayına ulaşmak istiyorsanız altyapıyı ikincil sitede hazırlamanız gerekir:

- Var olan bir ağ kullanarak yük devretme testi çalıştırırsanız, bu ağda Active Directory, DHCP ve DNS 'yi hazırlayın.
- VM ağını otomatik olarak oluşturma seçeneğiyle bir yük devretme testi çalıştırırsanız, yük devretme testini çalıştırmadan önce otomatik olarak oluşturulan ağa altyapı kaynakları eklemeniz gerekir. Bir kurtarma planında, yük devretme testi için kullanacağınız kurtarma planında Grup 1 ' den önceki bir el ile adım ekleyerek bunu kolaylaştırabilirsiniz. Ardından, yük devretme testini çalıştırmadan önce altyapı kaynaklarını otomatik olarak oluşturulan ağa ekleyin.


### <a name="prepare-dhcp"></a>DHCP 'yi hazırla
Yük devretme testi 'ne dahil edilen sanal makineler DHCP kullanıyorsa, sınama yük devretmesi amacıyla yalıtılmış ağ içinde bir test DHCP sunucusu oluşturun.


### <a name="prepare-active-directory"></a>Active Directory hazırla
Uygulama testi için yük devretme testi çalıştırmak için, test ortamınızda üretim Active Directory ortamının bir kopyasına ihtiyacınız vardır. Daha fazla bilgi için, [Active Directory için yük devretme testi konularını](site-recovery-active-directory.md#test-failover-considerations)gözden geçirin.

### <a name="prepare-dns"></a>DNS hazırlama
Yük devretme testi için bir DNS sunucusunu aşağıdaki şekilde hazırlayın:

* **DHCP**: sanal makineler DHCP kullanıyorsa, Testın DNS IP adresı test DHCP sunucusunda güncelleştirilmeleri gerekir. Windows ağ sanallaştırma 'nin bir ağ türü kullanıyorsanız, VMM sunucusu DHCP sunucusu işlevi görür. Bu nedenle, DNS 'in IP adresi, sınama yük devretmesi ağında güncellenmelidir. Bu durumda, sanal makineler kendilerini ilgili DNS sunucusuna kaydeder.
* **Statik adres**: sanal makineler STATIK bir IP adresi kullanıyorsa, sınama yük devretmesi ağı 'NDA test DNS sunucusunun IP adresi güncellenmelidir. DNS 'yi test sanal makinelerinin IP adresiyle güncelleştirmeniz gerekebilir. Bu amaçla aşağıdaki örnek betiği kullanabilirsiniz:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma

Bu yordam, bir kurtarma planı için yük devretme testi çalıştırma işlemini açıklar. Alternatif olarak, **sanal makineler** sekmesinde tek bir sanal makine için yük devretmeyi çalıştırabilirsiniz.

1. *Recoveryplan_name* > **Kurtarma planlarını** seçin. Yük **devretme testi** > **Yük** devretme ' ye tıklayın.
2. **Yük devretme testi** dikey penceresinde, yük devretme testi sonrasında çoğaltma VM 'lerinin ağlara nasıl bağlanması gerektiğini belirtin.
3. **İşler** sekmesinde yük devretme ilerlemesini izleyin.
4. Yük devretme tamamlandıktan sonra, VM 'Lerin başarıyla başlatılacağını doğrulayın.
5. İşiniz bittiğinde kurtarma planında **Yük devretme testini temizle** ' ye tıklayın. Yük devretme testiyle ilişkili gözlemlerinizi **Notlar**’da kaydedin veya saklayın. Bu adım, yük devretme testi sırasında Site Recovery tarafından oluşturulan tüm VM 'Leri ve ağları siler. 

![Yük devretme testi](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> Yük devretme testi sırasında sanal makineye verilen IP adresi, sanal makinenin planlı veya planlanmamış bir yük devretme için alabileceği IP adresidir (IP adresinin test yük devretme ağında kullanılabilir olduğunu önleme). Yük devretme ağında aynı IP adresi yoksa, sanal makine test yük devretme ağında bulunan başka bir IP adresi alır.



### <a name="run-a-test-failover-to-a-production-network"></a>Üretim ağına yük devretme testi çalıştırma

Ağ eşleme sırasında belirttiğiniz üretim kurtarma sitesi ağınıza yük devretme testi çalıştırmanızı öneririz. Ancak, yük devredilen bir VM 'de uçtan uca ağ bağlantısını doğrulamanız gerekiyorsa aşağıdaki noktaları dikkate alın:

* Yük devretme testini yaparken Birincil VM 'nin kapatıldığından emin olun. Bunu yapmazsanız aynı kimliğe sahip iki sanal makine aynı anda aynı ağda çalışır. Bu durum istenmeyen sonuçlara neden olabilir.
* Sınama yük devretmesi sanal makinelerini temizleytiğiniz zaman test yük devretmesi VM 'lerinde yaptığınız tüm değişiklikler kaybolur. Bu değişiklikler birincil VM 'lere geri çoğaltılmaz.
* Bu şekilde test etmek, üretim uygulamanız için kapalı kalma süresine yol açar. DR detayına devam edildiğinde uygulamanın kullanıcılarına uygulamayı kullanmayı sorma.  


## <a name="next-steps"></a>Sonraki adımlar
DR detayına başarıyla çalıştıktan sonra, [tam yük devretme çalıştırabilirsiniz](site-recovery-failover.md).



