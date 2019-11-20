---
title: Azure Site Recovery kullanarak VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarmaya yönelik çoğaltma sorunlarını giderme | Microsoft Docs
description: Bu makalede, Azure Site Recovery kullanarak VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarma sırasında sık karşılaşılan çoğaltma sorunlarıyla ilgili sorun giderme bilgileri verilmektedir.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: b02e819255db0cdf8b9d241f2ec0d41df7494162
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71844353"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware VM 'Leri ve fiziksel sunucular için çoğaltma sorunlarını giderme

Bu makalede, [Site Recovery](site-recovery-overview.md)kullanarak şirket Içi VMware VM 'lerini ve fiziksel sunucuları Azure 'a çoğaltırken karşılaşabileceğiniz bazı yaygın sorunlar ve belirli hatalar açıklanmaktadır.

## <a name="step-1-monitor-process-server-health"></a>1\. Adım: işlem sunucusu sistem durumunu Izleme

Site Recovery, çoğaltılan verileri almak ve iyileştirmek ve Azure 'a göndermek için [işlem sunucusunu](vmware-physical-azure-config-process-server-overview.md#process-server) kullanır.

Bağlı olduklarından ve düzgün çalıştığından ve işlem sunucusuyla ilişkili kaynak makinelerde çoğaltmanın ilerlemesinde emin olmak için portalda işlem sunucularının sistem durumunu izlemenizi öneririz.

- İşlem sunucularını izleme [hakkında bilgi edinin](vmware-physical-azure-monitor-process-server.md) .
- [En iyi uygulamaları gözden geçirme](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- İşlem sunucusu sistem durumu [sorunlarını giderin](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) .

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>2\. Adım: bağlantı ve çoğaltma sorunlarını giderme

İlk ve devam eden çoğaltma hataları genellikle kaynak sunucu ile işlem sunucusu arasındaki veya işlem sunucusu ile Azure arasında bağlantı sorunlarından kaynaklanır. 

Bu sorunları çözmek için [bağlantı ve çoğaltmada sorun giderin](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>3\. Adım: çoğaltma için kullanılamayan kaynak makinelerin sorunlarını giderme

Site Recovery kullanarak çoğaltmayı etkinleştirmek için kaynak makineyi seçmeyi denediğinizde, makine aşağıdaki nedenlerden biri için kullanılamayabilir:

* **Aynı örnek UUID 'ye sahip iki sanal**makine: vCenter 'ın altındaki iki sanal makine aynı örnek UUID 'ye sahip ise, yapılandırma sunucusu tarafından bulunan ilk sanal makine Azure Portal gösterilir. Bu sorunu çözmek için, iki sanal makinenin aynı örnek UUID 'ye sahip olmadığından emin olun. Bu senaryo genellikle bir yedekleme VM 'sinin etkin olduğu ve bulma kayıtlarımızla oturum açtığı örneklerde görülür. VMware 'den [Azure 'a Azure Site Recovery başvurun: yinelenen veya eski girdileri](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) gidermek için temizleme.
* **Geçersiz vCenter kullanıcısı kimlik bilgileri**: ovf şablonunu veya Birleşik kurulumu kullanarak yapılandırma sunucusunu ayarlarken doğru vCenter kimlik bilgilerini seçtiğinizden emin olun. Kurulum sırasında eklediğiniz kimlik bilgilerini doğrulamak için bkz. [otomatik bulma için kimlik bilgilerini değiştirme](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **vCenter yetersiz ayrıcalıklar**: vCenter 'a erişim için belirtilen izinler gerekli izinlere sahip değilse, sanal makineleri bulma başarısız olabilir. [Otomatik bulma için bir hesap hazırlama](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) bölümünde açıklanan izinlerin vCenter Kullanıcı hesabına eklendiğinden emin olun.
* **Yönetim sunucuları Azure Site Recovery**: sanal makine, aşağıdaki roller-yapılandırma sunucusu/Scale-Out işlem sunucusu/ana hedef sunucusu altında bir veya daha fazla yönetim sunucusu olarak kullanılıyorsa, sanal makineyi seçemeyeceksiniz portaldan makine. Managements sunucuları çoğaltılamaz.
* **Azure Site Recovery Services Ile zaten korunuyor/** devredildi: sanal makine zaten korunuyor veya Site Recovery yük devretmemişse, sanal makine portalda koruma için seçilecek şekilde kullanılamaz. Portalda Aradığınız sanal makinenin başka bir kullanıcı veya başka bir abonelik kapsamında zaten korumalı olmadığından emin olun.
* **vCenter bağlanmadı**: vCenter 'ın bağlı durumda olup olmadığını denetleyin. Doğrulamak için, kurtarma hizmetleri Kasası > Site Recovery altyapı > yapılandırma sunucuları ' na gidin > ilgili yapılandırma sunucusuna tıklayın > ilişkili sunucuların ayrıntıları ile sağ tarafta bir dikey pencere açılır. VCenter 'ın bağlanıp bağlanmadığından emin olun. "Bağlı değil" durumundaysa, sorunu çözün ve ardından portalda [yapılandırma sunucusunu yenileyin](vmware-azure-manage-configuration-server.md#refresh-configuration-server) . Bu işlem sonrasında, sanal makine portalda listelenecektir.
* **ESXi kapalı**: altında sanal makinenin bulunduğu ESXi ana bilgisayarı kapalı durumdaysa, sanal makine listelenmez veya Azure Portal seçilemeyecektir. ESXi ana bilgisayarında güç, portalda [yapılandırma sunucusunu yenileyin](vmware-azure-manage-configuration-server.md#refresh-configuration-server) . Bu işlem sonrasında, sanal makine portalda listelenecektir.
* **Yeniden başlatma bekleniyor**: sanal makinede bekleyen bir yeniden başlatma varsa, Azure Portal makineyi seçemezsiniz. Bekleyen yeniden başlatma etkinliklerinin tamamlandığından emin olun, [yapılandırma sunucusunu yenileyin](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Bu işlem sonrasında, sanal makine portalda listelenecektir.
* **IP bulunamadı**: sanal makinenin kendisiyle ilişkili GEÇERLI bir IP adresi yoksa, Azure Portal makineyi seçemezsiniz. Sanal makineye geçerli bir IP adresi atadığınızdan emin olun, [yapılandırma sunucusunu yenileyin](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Bu işlem sonrasında, sanal makine portalda listelenecektir.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Portalda korunan korumalı sanal makinelerin sorunlarını giderme

Site Recovery altında çoğaltılan sanal makineler, sistemde yinelenen girdiler varsa Azure portal kullanılamaz. Eski girişleri silmeyi ve sorunu çözmeyi öğrenmek için, VMware 'den Azure 'a Azure Site Recovery bkz. [yinelenen veya eski girdileri Temizleme](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Son ' XXX ' dakika içinde VM için kilitlenmeyle tutarlı bir kurtarma noktası yok

En yaygın sorunlardan bazıları aşağıda listelenmiştir

### <a name="initial-replication-issues-error-78169"></a>İlk çoğaltma sorunları [hata 78169]

Yukarıdaki bir bağlantı, bant genişliği veya zaman eşitlemeye ilişkin sorunlar olmadığından emin olun:

- Virüsten koruma yazılımı Azure Site Recovery engelliyor. Azure Site Recovery için gereken klasör dışlamaları hakkında [daha fazla](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) bilgi edinin.

### <a name="source-machines-with-high-churn-error-78188"></a>Yüksek dalgalanma sahip kaynak makineler [hata 78188]

Olası nedenler:
- Sanal makinenin listelenen disklerindeki veri değişim oranı (yazılan bayt/sn), çoğaltma hedefi depolama hesabı türü için [desteklenen Azure Site Recovery limitlerden](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) fazla.
- Büyük miktardaki verilerin karşıya yüklenmesi beklendiğinden dolayı dalgalanma hızının ani bir ani artış vardır.

Sorunu çözmek için:
- Hedef depolama hesabı türünün (Standart veya Premium) kaynakta dalgalanma oranı gereksinimine göre sağlandığından emin olun.
- Zaten bir Premium yönetilen diske (asrseeddisk türü) çoğaltıyorsanız, diskin boyutunun Site Recovery limitlere göre gözlemlenen dalgalanma oranını desteklediğinden emin olun. Gerekirse, asrseeddisk boyutunu artırabilirsiniz. Aşağıdaki adımları izleyin:
    - Etkilenen çoğaltılan makinenin diskler dikey penceresine gidin ve çoğaltma diski adını kopyalayın
    - Bu çoğaltma yönetilen diskine git
    - Genel Bakış dikey penceresinde bir SAS URL 'sinin oluşturulduğunu söyleyen bir başlık görebilirsiniz. Bu başlık üzerine tıklayın ve dışarı aktarmayı iptal edin. Başlığı görmüyorsanız bu adımı yoksayın.
    - SAS URL 'SI iptal edildiğinde, yönetilen diskin yapılandırma dikey penceresine gidin ve ASR 'nin kaynak diskte gözlemlenen dalgalanma oranını desteklemesi için boyutu artırın
- Gözlenen karmaşıklık geçicidir, bekleyen veri yüklemesinin yakalanması ve kurtarma noktaları oluşturması için birkaç saat bekleyin.
- Disk geçici Günlükler gibi kritik olmayan veriler içeriyorsa, verileri test edin. bu verileri başka bir yere taşımayı veya bu diski çoğaltmanın tamamen dışlanmasını göz önünde bulundurun
- Sorun devam ederse, çoğaltmayı planlamaya yardımcı olması için Site Recovery [dağıtım planlayıcısı](site-recovery-deployment-planner.md#overview) ' nı kullanın.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Sinyali olmayan kaynak makineler [hata 78174]

Bu durum, kaynak makinedeki Azure Site Recovery Mobility Aracısı yapılandırma sunucusu (CS) ile iletişim kurmadığında oluşur.

Sorunu çözmek için, kaynak VM 'den yapılandırma sunucusuna ağ bağlantısını doğrulamak üzere aşağıdaki adımları kullanın:

1. Kaynak makinenin çalıştığını doğrulayın.
2. Yönetici ayrıcalıklarına sahip bir hesap kullanarak kaynak makinede oturum açın.
3. Aşağıdaki hizmetlerin çalıştığını ve hizmetleri yeniden başlatmadığından emin olun:
   - Svagents (InMage Scout, aracı)
   - InMage Scout uygulama hizmeti
4. Kaynak makinede, hata ayrıntıları için konumdaki günlükleri inceleyin:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Sinyal olmadan işlem sunucusu [Hata 806]
Işlem sunucusu 'ndan (PS) sinyal olmaması durumunda şunları kontrol edin:
1. PS sanal makinesi çalışıyor
2. Hata ayrıntıları için PS 'de aşağıdaki günlükleri kontrol edin:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Sinyal olmadan ana hedef sunucu [hata 78022]

Bu durum, ana hedefteki Azure Site Recovery Mobility Aracısı yapılandırma sunucusuyla iletişim kurmadığında oluşur.

Sorunu çözmek için, hizmet durumunu doğrulamak üzere aşağıdaki adımları kullanın:

1. Ana hedef VM 'nin çalıştığını doğrulayın.
2. Yönetici ayrıcalıklarına sahip bir hesap kullanarak ana hedef VM 'de oturum açın.
    - Svagents hizmetinin çalıştığını doğrulayın. Çalışıyorsa, hizmeti yeniden başlatın
    - Hata ayrıntıları için konumdaki günlükleri kontrol edin:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. Ana hedefi yapılandırma sunucusuna kaydetmek için **%ProgramData%\asr\agent**klasörüne gidin ve komut isteminde aşağıdaki komutu çalıştırın:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Hata KIMLIĞI 78144-son ' XXX ' dakika içinde VM için uygulamayla tutarlı bir kurtarma noktası yok

Mobility Agent [9,23](vmware-physical-mobility-service-overview.md##from-923-version-onwards) & [9,27](site-recovery-whats-new.md#update-rollup-39) sürümlerinde VSS yükleme hatası davranışlarını işleyecek geliştirmeler yapılmıştır. VSS hatalarında sorun giderme konusunda en iyi yönergeler için en son sürümlere sahip olduğunuzdan emin olun.

En yaygın sorunlardan bazıları aşağıda listelenmiştir

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Neden 1: SQL Server 2008/2008 R2 'de bilinen sorun 
**Nasıl düzeltileceğini öğrenin** : SQL Server 2008/2008 R2 ile ilgili bilinen bir sorun var. Lütfen bu KB makalesine bakın [Azure Site Recovery aracı veya bileşen olmayan DIĞER VSS yedeklemesi SQL Server 2008 R2 barındıran bir sunucu için başarısız oluyor](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>2\. neden: Azure Site Recovery işlerin, AUTO_CLOSE DBs ile SQL Server örneklerinin herhangi bir sürümünü barındıran sunucularda başarısız olması 
**Nasıl düzeltilir** : KB [makalesine](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) başvurun 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Neden 3: SQL Server 2016 ve 2017 ' de bilinen sorun
**Nasıl düzeltilir** : KB [makalesine](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) başvurun 


### <a name="more-causes-due-to-vss-related-issues"></a>VSS ile ilgili sorunlardan kaynaklanan nedenler:

Daha fazla sorun gidermek için, hata kodunu tam olarak almak için kaynak makinedeki dosyaları kontrol edin:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Dosyadaki hatalar nasıl konumlandırsın?
Bir düzenleyicide boş olan bir dosyayı açarak "boş" dizesini arayın
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Yukarıdaki örnekte **2147754994** , hatayı aşağıda gösterildiği gibi bildiren hata kodudur

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS yazıcısı yüklü değil-hata 2147221164 

*Nasıl düzeltilir*: uygulama tutarlılığı etiketi oluşturmak için, Azure Site Recovery Microsoft birim gölge kopyası hizmeti 'NI (VSS) kullanır. Uygulama tutarlılığı anlık görüntülerini almak için işlemi için bir VSS sağlayıcısı yüklenir. Bu VSS sağlayıcısı bir hizmet olarak yüklendi. VSS sağlayıcısı hizmetinin yüklü olmaması durumunda, uygulama tutarlılığı anlık görüntüsü oluşturma işlemi, 0x80040154 "sınıf kayıtlı değil" hata kimliğiyle başarısız olur. </br>
[VSS yazıcı yükleme sorunlarını giderme makalesine](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) bakın 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS yazıcı devre dışı-hata 2147943458

**Nasıl düzeltilir**: uygulama tutarlılığı etiketi oluşturmak için, Azure Site Recovery Microsoft birim gölge kopyası hizmeti 'NI (VSS) kullanır. Uygulama tutarlılığı anlık görüntülerini almak için işlemi için bir VSS sağlayıcısı yüklenir. Bu VSS sağlayıcısı bir hizmet olarak yüklendi. VSS sağlayıcısı hizmetinin devre dışı bırakılması durumunda, uygulama tutarlılığı anlık görüntüsü oluşturma işlemi hata kimliğiyle başarısız olur "belirtilen hizmet devre dışı bırakıldı ve başlatılamıyor (0x80070422)". </br>

- VSS devre dışıysa,
    - VSS sağlayıcı hizmetinin başlangıç türünün **Otomatik**olarak ayarlandığını doğrulayın.
    - Aşağıdaki hizmetleri yeniden başlatın:
        - VSS hizmeti
        - VSS sağlayıcısı Azure Site Recovery
        - VDS hizmeti

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS sağlayıcısı NOT_REGISTERED-hata 2147754756

**Nasıl düzeltilir**: uygulama tutarlılığı etiketi oluşturmak için, Azure Site Recovery Microsoft birim gölge kopyası hizmeti 'NI (VSS) kullanır. Azure Site Recovery VSS sağlayıcısı hizmeti 'nin yüklü olup olmadığını denetleyin. </br>

- Aşağıdaki komutları kullanarak sağlayıcı yüklemesini yeniden deneyin:
- Mevcut sağlayıcıyı kaldır: C:\Program Files (x86) \Microsoft Azure Site Recovery\lationt\ınmagevssprovider_uninstall.cmd
- Yeniden yükle: C:\Program Files (x86) \Microsoft Azure Site Recovery\lationt\ınmagevssprovider_ınstall.cmd
 
VSS sağlayıcı hizmetinin başlangıç türünün **Otomatik**olarak ayarlandığını doğrulayın.
    - Aşağıdaki hizmetleri yeniden başlatın:
        - VSS hizmeti
        - VSS sağlayıcısı Azure Site Recovery
        - VDS hizmeti

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla yardıma ihtiyacınız varsa sorunuzu [Azure Site Recovery forumuna](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)gönderin. Etkin bir topluluk sunuyoruz ve mühendislerimizden biri size yardımcı olabilir.
