---
title: Azure sanal makinelerinde SLES 12 SP3 ile SAP HANA 2,0 genişleme HSR-pacemaker kurulumunu sorun giderme | Microsoft Docs
description: Azure sanal makineler 'de çalışan SLES 12 SP3 üzerinde SAP HANA sistem çoğaltması (HSR) ve Paceyapıcısı temelinde, karmaşık SAP HANA genişleme yüksek kullanılabilirlik yapılandırmasını denetleme ve sorunlarını giderme kılavuzu
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: 299fba8a082f19f17ab581a6ac2bfac9fd3f8cf1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099655"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>SLES 12 SP3 üzerinde yüksek kullanılabilirliğe sahip ayarları SAP HANA doğrulama ve sorun giderme 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Bu makale, Azure sanal makinelerinde (VM 'Ler) çalışan SAP HANA ölçeğini genişletmek için Paceoluşturucu kümesi yapılandırmasını denetlemenize yardımcı olur. Küme kurulumu, SAP HANA sistem çoğaltması (HSR) ve SUSE RPM paketi SAPHanaSR-ScaleOut ile birlikte gerçekleştirildi. Tüm testler yalnızca SUSE SLES 12 SP3 üzerinde gerçekleştirildi. Makalenin bölümleri farklı alanlara sahiptir ve yapılandırma dosyalarından örnek komutları ve alıntıları 'leri içerir. Tüm küme kurulumunu doğrulama ve denetleme yöntemi olarak bu örnekleri öneririz.



## <a name="important-notes"></a>Önemli notlar

SAP HANA sistem çoğaltma ve Paceyapıcısı ile birlikte SAP HANA genişleme için tüm testler yalnızca SAP HANA 2,0 ile gerçekleştirildi. İşletim sistemi sürümü, SAP uygulamaları için 12 SP3 SUSE Linux Enterprise Server. SUSE 'den en son RPM paketi olan SAPHanaSR-ScaleOut, pacemaker kümesini ayarlamak için kullanılmıştır.
SUSE [, bu performans için iyileştirilmiş kurulumun ayrıntılı bir açıklamasını][sles-hana-scale-out-ha-paper]yayımladı.

SAP HANA genişleme için desteklenen sanal makine türleri için [SAP HANA sertifikalı IaaS dizinini][sap-hana-iaas-list]denetleyin.

Birden çok alt ağ ve vNIC ile birlikte SAP HANA genişleme ile ilgili teknik bir sorun vardı ve HSR ayarlanıyor. Bu sorunun düzeltildiği en son SAP HANA 2,0 düzeltme eklerini kullanmak zorunludur. Aşağıdaki SAP HANA sürümleri desteklenir: 

* Rev 2.00.024.04 veya üzeri 
* Rev 2.00.032 veya üzeri

SUSE 'den desteğe ihtiyacınız varsa, bu [Kılavuzu][suse-pacemaker-support-log-files]izleyin. Makalede açıklandığı gibi SAP HANA yüksek kullanılabilirlik (HA) kümesiyle ilgili tüm bilgileri toplayın. SUSE desteği, daha fazla analiz için bu bilgiye ihtiyaç duyuyor.

Dahili test sırasında küme kurulumu, Azure portal aracılığıyla normal düzgün bir VM kapatması ile karıştırılır. Bu nedenle, diğer yöntemlerle küme yük devretmesini test etmenizi öneririz. Bir çekirdek Panic 'i zorlama veya ağları kapatma ya da **MSL** kaynağını geçirme gibi yöntemleri kullanın. Aşağıdaki bölümlerde ayrıntılara bakın. Varsayım, standart bir kapatmanın amaç ile gerçekleşmesidir. Kasıtlı olarak kapanmaya en iyi örnek, bakım içindir. [Planlı bakımın](#planned-maintenance)ayrıntılarına bakın.

Ayrıca, iç test sırasında küme kurulumu, küme bakım modundayken el ile SAP HANA yük devri sonrasında karıştırılır. Küme bakım modunu sonlandırmadan önce yeniden el ile geri geçmeniz önerilir. Başka bir seçenek de, kümeyi bakım moduna almadan önce yük devretme tetiklemelidir. Daha fazla bilgi için bkz. [Planlı bakım](#planned-maintenance). SUSE belgelerindeki belgeler, **CRM** komutunu kullanarak kümeyi bu şekilde nasıl sıfırlayabileceğinizi açıklar. Ancak, daha önce bahsedilen yaklaşım iç test sırasında sağlam ve beklenmedik yan etkileri hiçbir şekilde gösterilmişti.

**CRM migrate** komutunu kullandığınızda, küme yapılandırmasını temizlediğinizden emin olun. Farkında olmayabilirsiniz konum kısıtlamalarını ekler. Bu kısıtlamalar, küme davranışını etkiler. [Planlı bakımda](#planned-maintenance)daha fazla ayrıntı görüntüleyin.



## <a name="test-system-description"></a>Test sistemi açıklaması

 SAP HANA genişleme HA doğrulaması ve sertifikasyon için bir kurulum kullanıldı. Üç SAP HANA düğümü olan iki sistem tarafından yapılır: bir ana ve iki çalışan. Aşağıdaki tabloda VM adları ve iç IP adresleri listelenmektedir. Bu VM 'lerde izleyen tüm doğrulama örnekleri yapıldı. Komut örneklerinde bu sanal makine adlarını ve IP adreslerini kullanarak komutları ve çıktılarını daha iyi anlayabilirsiniz:


| Düğüm türü | VM adı | IP adresi |
| --- | --- | --- |
| Site 1 üzerinde ana düğüm | Hso-Hana-VM-S1-0 | 10.0.0.30 |
| Site 1 üzerinde çalışan düğümü 1 | Hso-Hana-VM-S1-1 | 10.0.0.31 |
| Site 1 üzerinde çalışan düğümü 2 | Hso-Hana-VM-S1-2 | 10.0.0.32 |
| | | |
| Site 2 üzerinde ana düğüm | Hso-Hana-VM-S2-0 | 10.0.0.40 |
| Site 2 üzerinde çalışan düğümü 1 | Hso-Hana-VM-S2-1 | 10.0.0.41 |
| Site 2 üzerinde çalışan düğümü 2 | Hso-Hana-VM-S2-2  | 10.0.0.42 |
| | | |
| Çoğunluk Oluşturucu düğümü | Hso-Hana-DM | 10.0.0.13 |
| SBD cihaz sunucusu | Hso-Hana-SBD | 10.0.0.19 |
| | | |
| NFS sunucusu 1 | Hso-NFS-VM-0 | 10.0.0.15 |
| NFS sunucusu 2 | Hso-NFS-VM-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Birden çok alt ağ ve vNIC

Aşağıdaki SAP HANA ağ önerilerini, bir Azure sanal ağı içinde üç alt ağ oluşturulmuştur. Azure 'da SAP HANA ölçeği, paylaşılmayan modda yüklenmelidir. Yani, her düğüm **/Hana/Data** ve **/Hana/log**için yerel disk birimleri kullanır. Düğümler yalnızca yerel disk birimleri kullandığından, depolama için ayrı bir alt ağ tanımlamanız gerekli değildir:

- SAP HANA Internode iletişimi için 10.0.2.0/24
- SAP HANA sistem çoğaltması (HSR) için 10.0.1.0/24
- diğer her şey için 10.0.0.0/24

Birden çok ağı kullanmayla ilgili SAP HANA yapılandırma hakkında daha fazla bilgi için bkz. [SAP HANA Global. ini](#sap-hana-globalini).

Kümedeki her VM 'nin alt ağ sayısına karşılık gelen üç vNIC 'i vardır. [Azure 'da birden çok ağ arabirimi kartı Ile Linux sanal makinesi oluşturma][azure-linux-multiple-nics] BIR Linux VM dağıtımı sırasında Azure 'da olası bir yönlendirme sorununu açıklar. Bu özel yönlendirme makalesi yalnızca birden fazla sanal NIC kullanımı için geçerlidir. Bu sorun, SLES 12 SP3'TE varsayılan olarak SUSE tarafından çözülür. Daha fazla bilgi için bkz. [EC2 ve Azure 'da Cloud-netconfig Ile çoklu NIC][suse-cloud-netconfig].


SAP HANA birden çok ağı kullanacak şekilde doğru yapılandırıldığını doğrulamak için aşağıdaki komutları çalıştırın. İlk olarak, üç alt ağın tüm üç iç IP adreslerinin etkin olduğu işletim sistemi düzeyini kontrol edin. Farklı IP adresi aralıklarına sahip alt ağları tanımladıysanız, komutları uyarlamanız gerekir:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Aşağıdaki örnek çıktı, site 2 ' deki ikinci çalışan düğümüdür. ETH0, eth1 ve eth2 öğesinden üç farklı iç IP adresi görebilirsiniz:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Daha sonra, ad sunucusu ve HSR için SAP HANA bağlantı noktalarını doğrulayın. SAP HANA, ilgili alt ağlarda dinleme yapmanız gerekir. SAP HANA örnek numarasına bağlı olarak, komutları uyarlamanız gerekir. Test sistemi için örnek numarası **00**' dır. Hangi bağlantı noktalarının kullanıldığını bulmanın farklı yolları vardır. 

Aşağıdaki SQL ifadesinde örnek KIMLIĞI, örnek numarası ve diğer bilgiler döndürülür:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Doğru bağlantı noktası numaralarını bulmak için, örneğin, **Configuration** veya bir SQL beyanı aracılığıyla Hana Studio 'ya bakabilirsiniz:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

SAP yazılım yığınında kullanılan her bağlantı noktasını bulmak için SAP HANA dahil olmak üzere [Tüm sap ürünlerinin TCP/IP bağlantı noktalarını][sap-list-port-numbers]arayın.

SAP HANA 2,0 test sisteminde **00** örnek numarası verildiğinde, ad sunucusu için bağlantı noktası numarası **30001**' dir. HSR meta veri iletişimi için bağlantı noktası numarası **40002**' dir. Bir seçenek, bir çalışan düğümünde oturum açmak ve ardından ana düğüm hizmetlerini denetbir seçenektir. Bu makalede, site 2 üzerinde çalışan düğümü 2 ' yi, site 2 ' deki ana düğüme bağlanmaya çalışırken denetliyoruz.

Ad sunucusu bağlantı noktasını denetleyin:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Internode iletişiminin **10.0.2.0/24**alt ağını kullandığını kanıtlamak için, sonuç aşağıdaki örnek çıkışa benzer şekilde görünmelidir.
Yalnızca **10.0.2.0/24** alt ağı aracılığıyla kurulan bağlantı başarılı olmalıdır:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Şimdi HSR bağlantı noktası **40002**' i kontrol edin:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

HSR iletişiminin **10.0.1.0/24**alt ağını kullandığını kanıtlamak için, sonuç aşağıdaki örnek çıktı gibi görünmelidir.
Yalnızca **10.0.1.0/24** alt ağı aracılığıyla kurulan bağlantı başarılı olmalıdır:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


**Corosync** yapılandırma dosyasının, çoğunluk Oluşturucu düğümü dahil olmak üzere kümedeki her düğümde doğru olması gerekebilir. Bir düğümün küme birleştirmesi beklenen şekilde çalışmazsa, **/etc/Corosync/Corosync.exe conf** dosyasını tüm düğümlerde el ile oluşturun veya kopyalayın ve hizmeti yeniden başlatın. 

Test sisteminden **Corosync. conf** içeriği bir örnektir.

İlk bölüm, [küme yükleme](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation), adım 11 ' de açıklandığı gibi **totıtem**' dır. **Mcastaddr**için değeri yoksayabilirsiniz. Mevcut girişi tutmanız yeterlidir. **Belirteç** ve **konsensus** girdilerinin [Microsoft Azure SAP HANA belgelerine][sles-pacemaker-ha-guide]göre ayarlanması gerekir.

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

İkinci bölüm, **günlüğe kaydetme**, verilen varsayılandan değiştirilmedi:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

Üçüncü bölümde **NodeList**gösterilmektedir. Kümenin tüm düğümlerinin **NodeId 'si**ile gösterilmesi gerekir:

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

Son bölümde, **çekirdek**, **expected_votes** için değeri doğru şekilde ayarlamanız önemlidir. Bu, çoğunluk Oluşturucu düğümü dahil olmak üzere düğümlerin sayısı olmalıdır. Ve **two_node** değeri **0**olmalıdır. Girişi tamamen kaldırmayın. Yalnızca değeri **0**olarak ayarlayın.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


**Systemctl**aracılığıyla hizmeti yeniden başlatın:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD cihazı

Bir Azure VM 'de bir SBD cihazının nasıl ayarlanacağı, [SBD balıklesi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing)konusunda açıklanmaktadır.

İlk olarak, kümedeki her düğüm için ACL girişi varsa SBD sunucu VM 'sini kontrol edin. SBD sunucu sanal makinesinde aşağıdaki komutu çalıştırın:


<pre><code>
targetcli ls
</code></pre>


Test sisteminde, komutun çıktısı aşağıdaki örneğe benzer şekilde görünür. **IQN. 2006-04. Hso-DB-0. Local: Hso-DB-0** gibi ACL adları VM 'lerde karşılık gelen Başlatıcı adları olarak girilmelidir. Her VM 'nin farklı bir tane olması gerekir.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Ardından, tüm VM 'lerde Başlatıcı adlarının farklı olduğunu ve daha önce gösterilen girişlere karşılık geldiğini kontrol edin. Bu örnek, site 1 ' deki çalışan düğümü 1 ' dir:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

Çıktı aşağıdaki örneğe benzer şekilde görünür:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Sonra, **bulmanın** düzgün çalıştığını doğrulayın. SBD sunucu VM 'sinin IP adresini kullanarak her küme düğümünde aşağıdaki komutu çalıştırın:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

Çıktı aşağıdaki örneğe benzer şekilde görünmelidir:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Sonraki kanıt noktası, düğümün SDB cihazını göreceğini doğrulamadır. Çoğunluk Oluşturucu düğümü dahil olmak üzere her düğümde onu kontrol edin:

<pre><code>
lsscsi | grep dbhso
</code></pre>

Çıktı aşağıdaki örneğe benzer şekilde görünmelidir. Ancak, adlar farklılık gösterebilir. VM yeniden başlatıldıktan sonra cihaz adı da değişebilir:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

Sistem durumuna bağlı olarak, sorunları çözmek için bazen Iscsı hizmetlerini yeniden başlatmanıza yardımcı olur. Sonra aşağıdaki komutları çalıştırın:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Herhangi bir düğümden tüm düğümlerin **Açık**olup olmadığını kontrol edebilirsiniz. Belirli bir düğümde doğru cihaz adını kullandığınızdan emin olun:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Çıktının kümedeki her düğüm için **clear** gösterilmesi gerekir:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Başka bir SBD denetimi, **SBD** komutunun **döküm** seçeneğidir. Bu örnek komutta ve çoğunluk Oluşturucu düğümünden gelen çıktıda, cihaz adı **SDM**değil **SSD**idi:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Cihazın adından ayrı olarak, tüm düğümlerde aynı görünmelidir:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

SBD için bir veya daha fazla denetim başka bir düğüme ileti gönderme olasılığının bir daha vardır. Site 2 ' de çalışan düğümü 2 ' ye bir ileti göndermek için, site 2 ' de çalışan düğümü 1 ' de aşağıdaki komutu çalıştırın:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

Hedef VM tarafında, **Hso-Hana-VM-S2-2** Bu örnekte, **/var/log/messages**dosyasında aşağıdaki girişi bulabilirsiniz:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

**/Etc/sysconfig/SBD** ' deki girdilerin [Azure 'Daki SUSE Linux Enterprise Server paceyapıcısı ayarlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing)bölümündeki açıklamaya karşılık geldiğinden emin olun. **/Etc/IDL/SCC SID.conf** içindeki başlangıç ayarının otomatik olarak ayarlandığını doğrulayın.

Aşağıdaki girişler **/Etc/sysconfig/SBD**içinde önemlidir. Gerekirse, **kimlik** değerini uyarlayın:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


/Etc/IBU **/SCC** daki başlangıç ayarını kontrol edin. Gerekli ayar, belgelerde açıklanan aşağıdaki IBir ı Farklı olursa, **VI** ile el ile doğrulayın ve bu öğeyi el ile uyarlayabilirsiniz.

Bu komut başlangıç davranışını ayarlar:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Bu girdiyi **/etc/ITS/SCC** da yap:

<pre><code>
node.startup = automatic
</code></pre>

Sınama ve doğrulama işlemleri sırasında, bir VM 'nin yeniden başlatıldıktan sonra, bazı durumlarda SBD cihazı artık görünmez. Başlangıç ayarı ve YaST2 gösterilme arasında bir tutarsızlık vardı. Ayarları denetlemek için şu adımları uygulayın:

1. YaST2 'i başlatın.
2. Sol taraftaki **Ağ Hizmetleri** ' ni seçin.
3. Sağ tarafta **Iscsı Başlatıcısı** ' nı aşağı kaydırın ve seçin.
4. **Hizmet** sekmesinin altındaki bir sonraki ekranda, düğüm için benzersiz Başlatıcı adı görürsünüz.
5. Başlatıcı adının üzerinde, **hizmet başlangıç** değerinin **önyükleme sırasında**olarak ayarlandığından emin olun.
6. Aksi takdirde, **el ile**yerine **önyükleme sırasında** olarak ayarlayın.
7. Sonra, üst sekmeyi **bağlı hedeflere**geçirin.
8. **Bağlı hedefler** ekranında, bu örnekte olduğu gıbı, SBD cihazı için bir giriş görmeniz gerekir: **10.0.0.19:3260 IQN. 2006-04. dbhso. Yerel: dbhso**.
9. **Başlangıç** değerinin **önyükleme sırasında**olarak ayarlandığından emin olun.
10. Aksi takdirde, **Düzenle** ' yi seçin ve değiştirin.
11. Değişiklikleri kaydedin ve Exit YaST2.



## <a name="pacemaker"></a>Paceyapıcısı

Her şey doğru şekilde kurulduktan sonra, Paceoluşturucu hizmetinin durumunu denetlemek için her düğümde aşağıdaki komutu çalıştırabilirsiniz:

<pre><code>
systemctl status pacemaker
</code></pre>

Çıktının en üstü aşağıdaki örnekteki gibi görünmelidir. **Etkin** olduktan sonraki durum, **yüklendi** ve **etkin (çalışıyor)** olarak gösterilmelidir. **Yüklenen** durum, **etkin**olarak gösterilmelidir.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Ayar hala **devre dışıysa**, şu komutu çalıştırın:

<pre><code>
systemctl enable pacemaker
</code></pre>

Pacemaker 'da yapılandırılan tüm kaynakları görmek için şu komutu çalıştırın:

<pre><code>
crm status
</code></pre>

Çıktı aşağıdaki örneğe benzer şekilde görünmelidir. Bu, **CLN** ve **MSL** kaynaklarının, çoğunluk Oluşturucu VM 'sinde ( **Hso-Hana-DM**) durdurulmuş olarak gösterildiğine bağlıdır. Çoğunluk Oluşturucu düğümünde SAP HANA yüklemesi yoktur. Bu nedenle, **CLN** ve **MSL** kaynakları durdurulmuş olarak gösterilir. Toplam VM sayısı olan **7**' nin doğru olması önemlidir. Kümenin parçası olan tüm VM 'Lerin **çevrimiçi**durumuyla listelenmesi gerekir. Geçerli birincil ana düğümün doğru şekilde tanınması gerekir. Bu örnekte, **Hso-Hana-VM-S1-0**:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Pacemaker 'ın önemli bir özelliği bakım modudur. Bu modda, anında bir küme eylemi gerçekleştirmeden önce değişiklikler yapabilirsiniz. Örnek olarak bir VM yeniden başlatılır. Tipik bir kullanım durumu planlanmış işletim sistemi veya Azure altyapı bakımı olacaktır. Bkz. [Planlı bakım](#planned-maintenance). Pacemaker 'ı bakım moduna almak için aşağıdaki komutu kullanın:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

**CRM durumu**' nu denetlediğinizde, çıktıda tüm kaynakların **yönetilmeyen**olarak işaretlendiğini görürsünüz. Bu durumda, küme SAP HANA başlatma veya durdurma gibi değişiklikler üzerinde tepki vermez.
Aşağıdaki örnek, küme bakım modundayken **CRM durum** komutunun çıkışını gösterir:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


Bu komut örneği, küme bakım modunun nasıl sonlandıralınacağını gösterir:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Başka bir **CRM** komutu, tüm küme yapılandırmasını bir düzenleyicide alır, böylece düzenleyebilirsiniz. Değişiklikler kaydedildikten sonra, küme uygun eylemleri başlatır:

<pre><code>
crm configure edit
</code></pre>

Tüm küme yapılandırmasına bakmak için **CRM göster** seçeneğini kullanın:

<pre><code>
crm configure show
</code></pre>



Küme kaynaklarının arızasından sonra, **CRM durumu** komutu **başarısız eylemlerin**bir listesini gösterir. Bu çıkışın aşağıdaki örneğine bakın:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

Hatalardan sonra bir küme temizliği yapmanız gerekir. **CRM** komutunu yeniden kullanın ve bu başarısız eylem girişlerinden kurtulmak için komut seçeneğini **Temizleme** komutunu kullanın. Karşılık gelen küme kaynağını aşağıdaki şekilde adlandırın:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Komut aşağıdaki örnekteki gibi bir çıktı döndürmelidir:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Yük devretme veya devralma

[Önemli notlarda](#important-notes)anlatıldığı gibi, küme yük devretmesini veya SAP HANA HSR 'leri test etmek için standart bir düzgün kapanma kullanmamanız gerekir. Bunun yerine, bir çekirdek PANIC tetiklemenizi, bir kaynak geçişi zorlamanıza veya bir VM 'nin işletim sistemi düzeyindeki tüm ağları kapatmanızı öneririz. Başka bir yöntem de **CRM \<düğümü\> bekleme** komutunuz. [SUSE belgesine][sles-12-ha-paper]bakın. 

Aşağıdaki üç örnek komut, küme yük devretmesini zorlayabilir:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

[Planlı bakım](#planned-maintenance)bölümünde açıklandığı gibi, küme etkinliklerini izlemenin iyi bir yolu, **Watch** komutuyla **Saphanasr-showattr** ' i çalıştırmak olur:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Ayrıca, bir SAP Python betiğiyle gelen SAP HANA yatay duruma bakmanıza de yardımcı olur. Küme kurulumu, bu durum değerini arıyor. Bir çalışan düğümü hatası olduğunu düşündüğünüzde bu açık olur. Bir çalışan düğümü kapalıysa SAP HANA, tüm genişleme sisteminin sistem durumu için hemen bir hata döndürmez. 

Gereksiz yük devretme yapmaktan kaçınmak için bazı yeniden denemeler vardır. Küme, yalnızca durum **Tamam**' dan, değer **4**' e, **hata**' a geri dönerek çalışır. Bu nedenle, **Saphanasr-showAttr** çıktısındaki çıkış, durumu **çevrimdışı**olan bir VM 'yi gösteriyorsa doğrudur. Ancak birincil ve ikincil anahtar geçişi için henüz etkinlik yok. SAP HANA bir hata döndürmeyen sürece hiçbir küme etkinliği tetiklenmez.

SAP Python betiğini aşağıdaki gibi çağırarak, SAP HANA yatay sistem durumunu Kullanıcı  **\<\>Hana SID adm** olarak izleyebilirsiniz. Yolu uyarlamanız gerekebilir:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Bu komutun çıktısı aşağıdaki örnekteki gibi görünmelidir. **Konak durumu** sütunu ve **genel ana bilgisayar durumu** önemlidir. Gerçek çıktı ek sütunlarla daha geniştir.
Çıkış tablosunun bu belge içinde daha okunaklı olmasını sağlamak için, sağ taraftaki çoğu sütun çıkarılır:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Geçerli küme etkinliklerini denetlemek için başka bir komut vardır. Birincil sitenin ana düğümü sonlandırıldıktan sonra aşağıdaki komuta ve çıkış kuyruğu ' na bakın. Önceki ikincil ana düğümünü ( **Hso-Hana-VM-S2-0**) yeni birincil ana öğe olarak **yükseltme** gibi geçiş eylemlerinin listesini görebilirsiniz. Her şey iyidir ve tüm etkinlikler tamamlandıysa, bu **geçiş Özet** listesinin boş olması gerekir.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Planlı bakım 

Planlı bakımın geldiği durumlarda farklı kullanım durumları vardır. Tek bir soru, işletim sistemi düzeyi ve disk yapılandırması ya da bir HANA yükseltmesinde değişiklik gibi yalnızca altyapı bakımının yapılıp yapılmayacağını belirtir.
SUSE 'deki belgelerde, [sıfır kesinti][sles-zero-downtime-paper] veya [SAP HANA SR performansına iyileştirilmiş senaryo][sles-12-for-sap]gibi ek bilgiler bulabilirsiniz. Bu belgeler ayrıca, bir birincili el ile geçişi gösteren örnekleri içerir.

Altyapı Bakımı kullanım durumunu doğrulamak için yoğun iç test gerçekleştirildi. Birincili geçirmeyle ilgili sorunlardan kaçınmak için, bir kümeyi bakım moduna geçirmeden önce her zaman bir birincili geçirmeye karar verdik. Bu şekilde, kümenin önceki durum hakkında unutmasını gerekli değildir: hangi taraf birincili ve ikinciydi.

Bu şekilde iki farklı durum vardır:

- **Geçerli ikincil üzerinde planlı bakım**. Bu durumda yalnızca kümeyi bakım moduna alabilir ve kümeyi etkilemeden ikincil üzerinde iş yapabilirsiniz.

- **Geçerli birincil üzerinde planlanmış bakım**. Kullanıcılar bakım sırasında çalışmaya devam edebilmeleri için bir yük devretme zorlamanız gerekir. Bu yaklaşımda, yalnızca SAP HANA HSR düzeyinde değil, pacemaker tarafından küme yük devretmesini tetiklemeniz gerekir. Pacemaker kurulumu, SAP HANA devralmayı otomatik olarak tetikler. Ayrıca, kümeyi bakım moduna almadan önce yük devretmeyi gerçekleştirmeniz gerekir.

Geçerli ikincil sitede bakım prosedürü aşağıdaki gibidir:

1. Kümeyi bakım moduna alın.
2. İkincil sitede çalışmayı gerçekleştirin. 
3. Küme bakım modunu sonlandırın.

Geçerli birincil sitede bakım prosedürü daha karmaşıktır:

1. Bir yük devretmeyi veya SAP HANA bir Paceüreticisi kaynak geçişi aracılığıyla el ile tetikleyin. Aşağıdaki ayrıntılara bakın.
2. Eski birincil sitedeki SAP HANA, küme kurulumu tarafından kapatılır.
3. Kümeyi bakım moduna alın.
4. Bakım işi yapıldıktan sonra, önceki birincili yeni ikincil site olarak kaydedin.
5. Küme yapılandırmasını temizleyin. Aşağıdaki ayrıntılara bakın.
6. Küme bakım modunu sonlandırın.


Bir kaynağın geçirilmesi, küme yapılandırmasına bir giriş ekler. Bir yük devretmeyi zorluyor bir örnektir. Bakım modunu son yapmadan önce bu girişleri temizlemeniz gerekir. Aşağıdaki örneğe bakın.

İlk olarak, **MSL** kaynağını geçerli ikincil ana düğüme geçirerek küme yük devretmesini zorunlu kılın. Bu komut, bir **taşıma kısıtlamasının** oluşturulduğunu belirten bir uyarı verir:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


**Saphanasr-showAttr**komutu aracılığıyla yük devretme sürecini denetleyin. Küme durumunu izlemek için, adanmış bir kabuk penceresi açın ve **Gözcü**ile komutu başlatın:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Çıktıda el ile yük devretme gösterilmelidir. Önceki ikincil ana düğüm, bu örnekte **Hso-Hana-VM-S2-0**' a **yükseltilir**. Eski birincil site durduruldu, önceki birincil ana düğüm için **LSS** değeri **1** **Hso-Hana-VM-S1-0**: 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Küme yük devretmesi ve SAP HANA devralındıktan sonra, [paket pacemaker](#pacemaker)' da anlatıldığı şekilde bakım moduna alın.

**Saphanasr-showAttr** ve **CRM durumu** komutları kaynak geçişi tarafından oluşturulan kısıtlamalarla ilgili hiçbir şeyi göstermez. Bu kısıtlamaları görünür hale getirmek için bir seçenek, aşağıdaki komutla birlikte tüm küme kaynağı yapılandırmasını gösterir:

<pre><code>
crm configure show
</code></pre>

Küme yapılandırması içinde, önceki el ile kaynak geçişinin neden olduğu yeni bir konum kısıtlaması bulabilirsiniz. Bu örnek, **konum CLI**ile başlar-:

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Ne yazık ki, bu tür kısıtlamalar genel küme davranışını etkileyebilir. Bu nedenle, tüm sistem yedeklemesini getirmeden önce bunları yeniden kaldırmak zorunludur. **Unmigrate** komutuyla, daha önce oluşturulan konum kısıtlamalarını temizlemek mümkündür. Adlandırma biraz kafa karıştırıcı olabilir. Kaynak, geçirildiği orijinal sanal makineye geri geçirmeye çalışır. Yalnızca konum kısıtlamalarını kaldırır ve komutu çalıştırdığınızda karşılık gelen bilgileri de döndürür:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Bakım işinin sonunda, [pacemaker](#pacemaker)' da gösterildiği gibi küme bakım modunu durdurursunuz.



## <a name="hb_report-to-collect-log-files"></a>günlük dosyalarını toplamak için hb_report

Paceyapıcısı küme sorunlarını analiz etmek için, **hb_report** yardımcı programını ÇALıŞTıRMAK üzere SUSE Support tarafından da yararlıdır. Ne olduğunu analiz etmeniz için ihtiyacınız olan tüm önemli günlük dosyalarını toplar. Bu örnek çağrı, belirli bir olayın gerçekleştiği başlangıç ve bitiş zamanını kullanır. Ayrıca bkz. [önemli notlar](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Komut, sizi sıkıştırılmış günlük dosyalarını nereye yerleştirmiş olduğunu söyler:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Böylece, standart **tar** komutu aracılığıyla tek tek dosyaları ayıklayabilirsiniz:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Ayıklanan dosyalara baktığınızda, tüm günlük dosyalarını bulabilirsiniz. Bunların çoğu kümedeki her düğüm için ayrı dizinlere yerleştirildi:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


Belirtilen zaman aralığı içinde, geçerli ana düğüm **Hso-Hana-VM-S1-0** sonlandırıldı. Bu olayla ilişkili girdileri **günlük. GNLK**bulabilirsiniz:

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Diğer bir örnek, ikincil yöneticisinde yeni birincil ana ana öğe haline gelen Paceoluşturucu günlük dosyasıdır. Bu alıntı, sonlandırılan birincil ana düğümün durumunun **çevrimdışı**olarak ayarlandığını gösterir:

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA Global. ini


Aşağıdaki alıntıları, küme site 2 ' deki SAP HANA **Global. ini** dosyasından alınmıştır. Bu örnek, SAP HANA Internode iletişimi ve HSR için farklı ağlar kullanmak üzere konak adı çözümleme girdilerini gösterir:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Havk dili

Küme çözümü, menü ve grafiklerin kabuk düzeyinde tüm komutlara sahip olmasını tercih eden kullanıcılar için GUI sağlayan bir tarayıcı arabirimi sağlar.
Tarayıcı arabirimini kullanmak için, **\<düğümü\>** aşağıdaki URL 'deki gerçek bir SAP HANA düğümü ile değiştirin. Sonra kümenin (Kullanıcı **kümesi**) kimlik bilgilerini girin:

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Bu ekran görüntüsünde küme panosu gösterilmektedir:


![Havk kümesi panosu](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Bu örnek, [planlı bakımda](#planned-maintenance)açıklandığı şekilde küme kaynağı geçişinin neden olduğu konum kısıtlamalarını gösterir:


![Havk listesi kısıtlamaları](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Ayrıca, aşağıdaki gibi gösterildiği gibi, **hb_report** çıkışını **geçmiş**kapsamında da yükleyebilirsiniz. Günlük dosyalarını toplamak için bkz. hb_report: 

![Havk karşıya yükleme hb_report çıkışı](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

**Geçmiş Gezgini**ile, **hb_report** çıktısına dahil olan tüm küme geçişlerini izleyebilirsiniz:

![Hb_report çıktısında havk geçişleri](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Bu son ekran görüntüsü, tek bir geçişin **Ayrıntılar** bölümünü gösterir. Küme, birincil ana düğüm kilitlenmesi üzerinde yeniden işlem yapan düğüm **Hso-Hana-VM-S1-0**. Artık ikincil düğümü yeni ana, **Hso-Hana-VM-S2-0**olarak yükseltmektedir:

![Havk tek geçiş](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Sonraki adımlar

Bu sorun giderme kılavuzunda, genişleme yapılandırmasındaki SAP HANA için yüksek kullanılabilirlik açıklanmaktadır. Veritabanına ek olarak, SAP yatay içindeki diğer önemli bir bileşen SAP NetWeaver Stack ' dir. [Suse Enterprise Linux Server kullanan Azure sanal MAKINELERINDE SAP NetWeaver için yüksek kullanılabilirlik][sap-nw-ha-guide-sles]hakkında bilgi edinin.

