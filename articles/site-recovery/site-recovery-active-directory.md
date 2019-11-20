---
title: Azure Site Recovery ile Active Directory/DNS olağanüstü durum kurtarma ayarlama
description: Bu makalede Azure Site Recovery ile Active Directory ve DNS için bir olağanüstü durum kurtarma çözümünün nasıl uygulanacağı açıklanır.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8c1f85217db12b60cdcd8ea0bdb65792b8d02648
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084593"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Active Directory ve DNS için olağanüstü durum kurtarmayı ayarlama

SharePoint, Dynamics AX ve SAP gibi kurumsal uygulamaların Active Directory ve bir DNS altyapısının düzgün şekilde çalışması için bağımlıdır. Uygulamalar için olağanüstü durum kurtarmayı ayarlarken, doğru uygulama işlevlerini sağlamak için, diğer uygulama bileşenlerini kurtarmadan önce, genellikle Active Directory ve DNS 'yi kurtarmanız gerekir.

Active Directory için bir olağanüstü durum kurtarma planı oluşturmak için [Site Recovery](site-recovery-overview.md) kullanabilirsiniz. Bir kesinti oluştuğunda, bir yük devretme işlemi başlatabilirsiniz. Birkaç dakika içinde Active Directory çalışır duruma getirebilirsiniz. Birincil sitenizde birden çok uygulama için Active Directory dağıttıysanız (örneğin, SharePoint ve SAP için), tüm site yükünü devretmek isteyebilirsiniz. Active Directory önce Site Recovery kullanarak yük devredebilirler. Ardından, uygulamaya özgü kurtarma planlarını kullanarak diğer uygulamaların yükünü devreder.

Bu makalede, Active Directory için bir olağanüstü durum kurtarma çözümünün nasıl oluşturulacağı açıklanmaktadır. Önkoşulları ve yük devretme yönergelerini içerir. Başlamadan önce Active Directory ve Site Recovery hakkında bilgi sahibi olmanız gerekir.

## <a name="prerequisites"></a>Ön koşullar

* Azure 'a çoğaltma yapıyorsanız abonelik, Azure sanal ağı, depolama hesabı ve kurtarma hizmetleri Kasası dahil olmak üzere [Azure kaynaklarını hazırlayın](tutorial-prepare-azure.md).
* Tüm bileşenler için [destek gereksinimlerini](site-recovery-support-matrix-to-azure.md) gözden geçirin.

## <a name="replicate-the-domain-controller"></a>Etki alanı denetleyicisini çoğaltma

- Bir etki alanı denetleyicisi veya DNS barındıran en az bir VM üzerinde Site Recovery çoğaltma ayarlamanız gerekir.
- Ortamınızda birden çok etki alanı denetleyiciniz varsa, hedef sitede ek bir etki alanı denetleyicisi de ayarlamanız gerekir. Ek etki alanı denetleyicisi Azure 'da veya ikincil bir şirket içi veri merkezinde olabilir.
- Yalnızca birkaç uygulamanız ve bir etki alanı denetleyiciniz varsa, tüm siteyi birlikte devretmek isteyebilirsiniz. Bu durumda, etki alanı denetleyicisini hedef siteye (Azure 'da veya ikincil bir şirket içi veri merkezinde) çoğaltmak için Site Recovery kullanmanızı öneririz. [Yük devretme testi](#test-failover-considerations)için aynı çoğaltılan etki alanı DENETLEYICISINI veya DNS sanal makinesini kullanabilirsiniz.
- - Ortamınızda çok sayıda uygulamanız ve birden çok etki alanı denetleyicisi varsa veya aynı anda birkaç uygulamayı yük devretmek istiyorsanız, etki alanı denetleyicisi sanal makinesini Site Recovery çoğaltmaya ek olarak, bir hedef sitede ek etki alanı denetleyicisi (Azure 'da veya ikincil bir şirket içi veri merkezinde). [Yük devretme testi](#test-failover-considerations)için, Site Recovery tarafından çoğaltılan etki alanı denetleyicisini kullanabilirsiniz. Yük devretme için, hedef sitede ek etki alanı denetleyicisini kullanabilirsiniz.

## <a name="enable-protection-with-site-recovery"></a>Site Recovery ile korumayı etkinleştir

Etki alanı denetleyicisini veya DNS 'yi barındıran sanal makineyi korumak için Site Recovery kullanabilirsiniz.

### <a name="protect-the-vm"></a>VM 'yi koruma
Site Recovery kullanılarak çoğaltılan etki alanı denetleyicisi, [Yük devretme testi](#test-failover-considerations)için kullanılır. Aşağıdaki gereksinimleri karşıladığından emin olun:

1. Etki alanı denetleyicisi Genel Katalog sunucusudur.
2. Yük devretme testi sırasında gereken roller için etki alanı denetleyicisi, FSMO rolü sahibi olmalıdır. Aksi takdirde, yük devretmeden sonra bu rollerin [alınması](https://aka.ms/ad_seize_fsmo) gerekir.

### <a name="configure-vm-network-settings"></a>VM ağ ayarlarını yapılandırma
Etki alanı denetleyicisini veya DNS 'yi barındıran sanal makine için, Site Recovery, ağ ayarlarını çoğaltılan sanal makinenin **işlem ve ağ** ayarları altında yapılandırın. Bu, yük devretmeden sonra sanal makinenin doğru ağa bağlı olmasını sağlar.

## <a name="protect-active-directory"></a>Active Directory koru

### <a name="site-to-site-protection"></a>Siteden siteye koruma
İkincil sitede bir etki alanı denetleyicisi oluşturun. Sunucuyu bir etki alanı denetleyicisi rolüne yükselttiğinizde, birincil sitede kullanılmakta olan etki alanının adını belirtin. Sitelerin eklendiği site bağlantı nesnesindeki ayarları yapılandırmak için **Active Directory Siteleri ve Hizmetleri** ek bileşenini kullanabilirsiniz. Bir site bağlantısı üzerindeki ayarları yapılandırarak, iki veya daha fazla site arasında çoğaltmanın ne zaman gerçekleşeceğini ve ne sıklıkta oluştuğunu kontrol edebilirsiniz. Daha fazla bilgi için bkz. [siteler arasında çoğaltma zamanlama](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Siteden Azure 'a koruma
İlk olarak, bir Azure sanal ağında bir etki alanı denetleyicisi oluşturun. Sunucuyu bir etki alanı denetleyicisi rolüne yükselttiğinizde, birincil sitede kullanılan etki alanı adını belirtin.

Ardından, sanal ağın DNS sunucusunu Azure 'da DNS sunucusunu kullanacak şekilde yeniden yapılandırın.

![Azure Ağı](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure 'dan Azure 'a koruma
İlk olarak, bir Azure sanal ağında bir etki alanı denetleyicisi oluşturun. Sunucuyu bir etki alanı denetleyicisi rolüne yükselttiğinizde, birincil sitede kullanılan etki alanı adını belirtin.

Ardından, sanal ağın DNS sunucusunu Azure 'da DNS sunucusunu kullanacak şekilde yeniden yapılandırın.

## <a name="test-failover-considerations"></a>Yük devretme sınaması konuları
Üretim iş yükleri üzerindeki etkileri önlemek için, test yük devretmesi üretim ağından yalıtılmış bir ağda gerçekleşir.

Çoğu uygulama, etki alanı denetleyicisi veya DNS sunucusu varlığını gerektirir. Bu nedenle, uygulama yük devretmeden önce, sınama yük devretmesi için kullanılacak yalıtılmış ağda bir etki alanı denetleyicisi oluşturmanız gerekir. Bunu yapmanın en kolay yolu, etki alanı denetleyicisi veya DNS barındıran bir sanal makineyi çoğaltmak için Site Recovery kullanmaktır. Ardından, uygulama için kurtarma planının yük devretmesini çalıştırmadan önce, etki alanı denetleyicisi sanal makinesinin yük devretme testini çalıştırın. Şunları yapabilirsiniz:

1. Etki alanı denetleyicisini veya DNS 'yi barındıran sanal makineyi [çoğaltmak](vmware-azure-tutorial.md) için Site Recovery kullanın.
2. Yalıtılmış ağ oluşturun. Azure 'da oluşturduğunuz herhangi bir sanal ağ, varsayılan olarak diğer ağlardan yalıtılmıştır. Üretim ağınızda kullandığınız bu ağ için aynı IP adresi aralığını kullanmanızı öneririz. Bu ağda siteden siteye bağlantıyı etkinleştirmeyin.
3. Yalıtılmış ağda bir DNS IP adresi sağlayın. DNS sanal makinesinin almasını beklediğinizi belirten IP adresini kullanın. Azure 'a çoğaltma yapıyorsanız, yük devretmede kullanılan sanal makinenin IP adresini belirtin. IP adresini girmek için, çoğaltılan sanal makinede, **işlem ve ağ** ayarları ' nda **hedef IP** ayarlarını seçin.

    ![Azure test ağı](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery, aynı ada sahip bir alt ağda ve sanal makinenin **işlem ve ağ** AYARLARıNDA belirtilen IP adresini kullanarak test sanal makineleri oluşturmaya çalışır. Yük devretme testi için sağlanan Azure sanal ağında aynı ada sahip bir alt ağ yoksa, test sanal makinesi alfabetik olarak ilk alt ağda oluşturulur.
    >
    > Hedef IP adresi seçili alt ağın parçasıysa, Site Recovery hedef IP adresini kullanarak test yük devretmesi sanal makinesini oluşturmaya çalışır. Hedef IP seçili alt ağın bir parçası değilse, test yük devretmesi sanal makinesi seçili alt ağdaki bir sonraki kullanılabilir IP kullanılarak oluşturulur.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>İkincil bir siteye yük devretme testi

1. Başka bir şirket içi siteye çoğaltıyorsanız ve DHCP kullanıyorsanız, [Yük devretme testi IÇIN DNS ve DHCP 'yi ayarlayın](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Yalıtılmış ağda çalışan etki alanı denetleyicisi sanal makinesi için yük devretme testi yapın. Yük devretme sınamasını yapmak için etki alanı denetleyicisi sanal makinesinin kullanılabilir en son *uygulamayla tutarlı* kurtarma noktasını kullanın.
3. Uygulamanın çalıştığı sanal makineleri içeren kurtarma planı için yük devretme testi çalıştırın.
4. Test tamamlandığında, etki alanı denetleyicisi sanal makinesindeki *Yük devretme testini temizleyin* . Bu adım, yük devretme testi için oluşturulan etki alanı denetleyicisini siler.


### <a name="remove-references-to-other-domain-controllers"></a>Diğer etki alanı denetleyicilerine başvuruları kaldır
Yük devretme testi başlattığınızda, test ağındaki tüm etki alanı denetleyicilerini eklemeyin. Üretim ortamınızda bulunan diğer etki alanı denetleyicilerine yönelik başvuruları kaldırmak için, [FSMO Active Directory rollerini](https://aka.ms/ad_seize_fsmo) almanız ve eksik etki alanı denetleyicileri için [meta veri temizleme işlemini](https://technet.microsoft.com/library/cc816907.aspx) yapmanız gerekebilir.


### <a name="issues-caused-by-virtualization-safeguards"></a>Sanallaştırma korumalarının neden olduğu sorunlar

> [!IMPORTANT]
> Bu bölümde açıklanan yapılandırmalardan bazıları standart veya varsayılan etki alanı denetleyicisi yapılandırması değildir. Bu değişiklikleri bir üretim etki alanı denetleyicisinde yapmak istemiyorsanız, yük devretme testi için kullanmak üzere Site Recovery için ayrılmış bir etki alanı denetleyicisi oluşturabilirsiniz. Bu değişiklikleri yalnızca bu etki alanı denetleyicisi için yapın.  
>
>

Windows Server 2012 ' den başlayarak [ek korumalar Active Directory Domain Services (AD DS) yerleşik olarak bulunur](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Bu korumalar, temeldeki hiper yönetici platformu **VM-GenerationID**' i DESTEKLIYORSA, USN rollyedekler için sanallaştırılmış etki alanı denetleyicilerini korumanıza yardımcı Azure, **VM-GenerationID 'yi**destekler. Bu nedenle, Azure sanal makinelerinde Windows Server 2012 veya üstünü çalıştıran etki alanı denetleyicilerinin bu ek korumaları vardır.


**VM-GenerationID** sıfırlandığında AD DS veritabanının **ınvocationıd** değeri de sıfırlanır. Ayrıca, RID havuzu atılır ve SYSVOL klasörü yetkili olmayan olarak işaretlenir. Daha fazla bilgi için bkz. [Active Directory Domain Services sanallaştırmaya giriş](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) ve [DFSR 'yi güvenle sanallaştırın](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Azure 'a yük devretmek **VM-GenerationID** 'nin sıfırlanmasına neden olabilir. **VM 'yi sıfırlama-GenerationID** , etki alanı denetleyicisi sanal makinesi Azure 'da başladığında ek korumalar tetikler. Bu durum, etki alanı denetleyicisi sanal makinesinde oturum açabilmede *önemli bir gecikmeye* neden olacak.

Bu etki alanı denetleyicisi yalnızca bir test yük devretmesinde kullanıldığından, sanallaştırma korumaları gerekli değildir. Etki alanı denetleyicisi sanal makinesi için **VM-GenerationID** değerinin değişmediğinden emin olmak için, aşağıdaki DWORD değerini şirket içi etki alanı denetleyicisinde **4** olarak değiştirebilirsiniz:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Sanallaştırma korumalarının belirtileri

Bir test yük devretmesinin ardından sanallaştırma korumaları tetikleniyorsa, aşağıdaki belirtilerden bir veya daha fazlasını görebilirsiniz:  

* **GenerationID** değeri değişir.

    ![Oluşturma KIMLIĞI değişikliği](./media/site-recovery-active-directory/Event2170.png)

* **Invocationıd** değeri değişir.

    ![Çağırma KIMLIĞI değişikliği](./media/site-recovery-active-directory/Event1109.png)

* SYSVOL klasörü ve NETLOGON paylaşımları kullanılamıyor.

    ![SYSVOL klasörü paylaşma](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs SYSVOL klasörü](./media/site-recovery-active-directory/Event13565.png)

* DFSR veritabanları silinir.

    ![DFSR veritabanları silindi](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Yük devretme testi sırasında etki alanı denetleyicisi sorunlarını giderme

> [!IMPORTANT]
> Bu bölümde açıklanan yapılandırmalardan bazıları standart veya varsayılan etki alanı denetleyicisi yapılandırması değildir. Bu değişiklikleri bir üretim etki alanı denetleyicisinde yapmak istemiyorsanız, Site Recovery yük devretme testi için ayrılmış bir etki alanı denetleyicisi oluşturabilirsiniz. Değişiklikleri yalnızca bu ayrılmış etki alanı denetleyicisinde yapın.  
>
>

1. SYSVOL klasörünün ve NETLOGON klasörünün paylaşılıp paylaşılmadığını denetlemek için komut isteminde aşağıdaki komutu çalıştırın:

    `NET SHARE`

2. Komut isteminde, etki alanı denetleyicisinin düzgün şekilde çalıştığından emin olmak için aşağıdaki komutu çalıştırın:

    `dcdiag /v > dcdiag.txt`

3. Çıktı günlüğünde, aşağıdaki metni arayın. Metin, etki alanı denetleyicisinin düzgün çalıştığını onaylar.

    * "test bağlantısı geçildi"
    * "test tanıtımı başarılı oldu"
    * "test MachineAccount geçildi"

Yukarıdaki koşullar karşılanmıyorsa, büyük olasılıkla etki alanı denetleyicisinin düzgün şekilde çalışıyor olması olasıdır. Aksi takdirde, aşağıdaki adımları izleyin:

1. Etki alanı denetleyicisinin yetkili bir geri yüklemesini yapın. Aşağıdaki bilgileri aklınızda bulundurun:
    * FRS çoğaltması önerilmese [de, FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)çoğaltmasını kullanırsanız, yetkili geri yükleme adımlarını izleyin. İşlem, [Dosya Çoğaltma hizmetini yeniden başlatmak Için BurFlags kayıt defteri anahtarını kullanma](https://support.microsoft.com/kb/290762)bölümünde açıklanmıştır.

        BurFlags hakkında daha fazla bilgi için bkz. [D2 ve D4 blog gönderisi: nedir?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * DFSR çoğaltmasını kullanıyorsanız, yetkili geri yükleme adımlarını izleyin. Bu işlem, [DFSR ile çoğaltılan SYSVOL klasörü (örneğin, FRS için "D4/D2") için yetkili ve yetkili olmayan bir eşitlemeye zorlama](https://support.microsoft.com/kb/2218556)konusunda açıklanmaktadır.

        PowerShell işlevlerini de kullanabilirsiniz. Daha fazla bilgi için bkz. [DFSR-SYSVOL yetkili/yetkili olmayan geri yükleme PowerShell işlevleri](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Şirket içi etki alanı denetleyicisinde aşağıdaki kayıt defteri anahtarını **0** olarak ayarlayarak ilk eşitleme gereksinimini atlayın. DWORD yoksa, **Parametreler** düğümü altında oluşturabilirsiniz.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Daha fazla bilgi için bkz. [DNS olay kimliği 4013 sorunlarını giderme: DNS sunucusu ad ile TÜMLEŞIK DNS bölgelerini yükleyemedi](https://support.microsoft.com/kb/2001093).

3. Kullanıcı oturum açma bilgilerini doğrulamak için bir genel katalog sunucusunun kullanılabilir olması gereksinimini devre dışı bırakın. Bunu yapmak için, şirket içi etki alanı denetleyicisinde aşağıdaki kayıt defteri anahtarını **1**olarak ayarlayın. DWORD yoksa, **LSA** düğümü altında oluşturabilirsiniz.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Daha fazla bilgi için bkz. [kullanıcı oturumlarını doğrulamak için bir genel katalog sunucusunun kullanılabileceği gereksinimi devre dışı bırakma](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>Farklı makinelerde DNS ve etki alanı denetleyicisi

Etki alanı denetleyicisi ve DNs 'yi aynı VM üzerinde çalıştırıyorsanız, bu yordamı atlayabilirsiniz.


DNS, etki alanı denetleyicisiyle aynı VM 'de değilse, yük devretme testi için bir DNS sanal makinesi oluşturmanız gerekir. Yeni bir DNS sunucusu kullanabilir ve tüm gerekli bölgeleri oluşturabilirsiniz. Örneğin, Active Directory etki alanınız contoso.com ise, contoso.com adında bir DNS bölgesi oluşturabilirsiniz. Active Directory karşılık gelen girişlerin DNS 'de aşağıdaki gibi güncelleştirilmeleri gerekir:

1. Kurtarma planındaki diğer sanal makinelerin başlamasından önce bu ayarların yerinde olduğundan emin olun:
   * Bölgenin, orman kök adından sonra adlandırılması gerekir.
   * Bölge, dosya yedekli olmalıdır.
   * Güvenli ve güvenli olmayan güncelleştirmeler için bölgenin etkinleştirilmiş olması gerekir.
   * Etki alanı denetleyicisini barındıran sanal makinenin çözümleyici, DNS sanal makinesinin IP adresini göstermelidir.

2. Etki alanı denetleyicisini barındıran VM 'de şu komutu çalıştırın:

    `nltest /dsregdns`

3. DNS sunucusuna bir bölge eklemek, güvenli olmayan güncelleştirmelere izin vermek ve bölgeye DNS için bir giriş eklemek için aşağıdaki komutları çalıştırın:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Sonraki adımlar
[Azure Site Recovery ile kurumsal iş yüklerini koruma](site-recovery-workload.md)hakkında daha fazla bilgi edinin.
