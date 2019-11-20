---
title: Azure sanal makinelerinde (VM) IBM DB2 HADR 'yi ayarlama | Microsoft Docs
description: Azure sanal makinelerinde (VM) IBM DB2 LUW ile yüksek kullanılabilirlik sağlayın.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: e7de3e8026b15342c06eff9718242c08d33a53a4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72783776"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Pacemaker ile SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde IBM DB2 LUW 'ın yüksek kullanılabilirliği

[Yüksek kullanılabilirlik ve olağanüstü durum kurtarma (HADR) yapılandırmasında](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) LINUX, UNIX ve Windows (LUW) Için IBM DB2, birincil veritabanı örneği çalıştıran bir düğümden ve ikincil bir veritabanı örneği çalıştıran en az bir düğümden oluşur. Birincil veritabanı örneğindeki değişiklikler, yapılandırmanıza bağlı olarak, eş zamanlı olarak veya zaman uyumsuz olarak ikincil bir veritabanı örneğine çoğaltılır. 

Bu makalede, Azure sanal makinelerini (VM) dağıtma ve yapılandırma, küme çerçevesini yüklemek ve HADR yapılandırması ile IBM DB2 LUW yüklemesi açıklanmaktadır. 

Makalede, HADR veya SAP yazılım yüklemesi ile IBM DB2 LUW yükleme ve yapılandırma ele alınmaktadır. Bu görevleri gerçekleştirmenize yardımcı olmak için SAP ve IBM yükleme kılavuzlarına başvurular sağlıyoruz. Bu makale, Azure ortamına özgü bölümlere odaklanır. 

Desteklenen IBM DB2 sürümleri, SAP Note [1928533]' de belgelendiği gibi 10,5 ve üzeri sürümlerde bulunur.

Yüklemeye başlamadan önce, aşağıdaki SAP notları ve belgelerine bakın:

| SAP Note | Açıklama |
| --- | --- |
| [1928533] | Azure 'da SAP uygulamaları: Desteklenen Ürünler ve Azure VM türleri |
| [2015553] | Azure üzerinde SAP: destek önkoşulları |
| [2178632] | Azure 'da SAP için anahtar izleme ölçümleri |
| [2191498] | Azure ile Linux üzerinde SAP: Gelişmiş izleme |
| [2243692] | Azure 'da Linux (IaaS) VM: SAP lisans sorunları |
| [1984787] | SUSE LINUX Enterprise Server 12: yükleme notları |
| [1999351] | SAP için gelişmiş Azure izleme sorunlarını giderme |
| [2233094] | DB6: Azure 'da Linux, UNIX ve Windows için IBM DB2 kullanan SAP uygulamaları-ek bilgiler |
| [1612105] | DB6: HADR ile DB2 üzerinde SSS |


| Belgeler | 
| --- |
| [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux için gereken tüm sap notlarını içerir |
| [Linux 'TA SAP Için Azure sanal makineleri planlama ve uygulama][planning-guide] Kılavuzu |
| [Linux 'TA SAP Için Azure sanal makineleri dağıtımı][deployment-guide] (Bu makale) |
| [Linux 'TA SAP Için Azure sanal makineler veritabanı yönetim sistemi (DBMS) dağıtımı][dbms-guide] |
| [Azure planlama ve dağıtım denetim listesi üzerinde SAP iş yükü][azr-sap-plancheck] |
| [SAP uygulamaları için SUSE Linux Enterprise Server 12 SP4 en iyi yöntemler Kılavuzu][sles-for-sap-bp] |
| [SUSE Linux Enterprise yüksek kullanılabilirlik uzantısı 12 SP4][sles-ha-guide] |
| [IBM DB2 Azure sanal makineleri SAP iş yükü için DBMS dağıtımı][dbms-db2] |
| [IBM DB2 HADR 11,1][db2-hadr-11.1] |
| [IBM DB2 HADR R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Genel Bakış
Yüksek kullanılabilirlik elde etmek için, HADR ile IBM DB2 LUW, [Azure kullanılabilirlik kümesine](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) veya [Azure kullanılabilirlik alanları](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)göre dağıtılan en az iki Azure sanal makinesine yüklenir. 

Aşağıdaki grafiklerde iki veritabanı sunucusu Azure VM kurulumu görüntülenir. Veritabanı sunucusu Azure VM 'lerinin her ikisi de kendi depolamasına sahiptir ve çalışır. HADR 'de, Azure VM 'lerinden birindeki bir veritabanı örneği, birincil örnek rolüne sahiptir. Tüm istemciler bu birincil örneğe bağlanır. Veritabanı işlemlerinde yapılan tüm değişiklikler, DB2 işlem günlüğünde yerel olarak kalıcı hale getirilir. İşlem günlüğü kayıtları yerel olarak kalıcı olduğundan, kayıtlar TCP/IP aracılığıyla ikinci veritabanı sunucusundaki veritabanı örneğine, bekleme sunucusuna veya bekleme örneğine aktarılır. Bekleme örneği, aktarılan işlem günlüğü kayıtlarını ileri alarak yerel veritabanını güncelleştirir. Bu şekilde, bekleme sunucusu birincil sunucuyla eşitlenmiş olarak tutulur.

HADR yalnızca bir çoğaltma işlevidir. Hata algılaması yoktur ve otomatik yük devretme veya yük devretme tesislerini içermez. Bir veritabanı yöneticisi tarafından el ile başlatılan veya bekleyen sunucuya aktarma yapılmalıdır. Otomatik bir ele ve hata algılamayı başarmak için, Linux Paceoluşturucu kümeleme özelliğini kullanabilirsiniz. Paceyapıcısı iki veritabanı sunucusu örneğini izler. Birincil veritabanı sunucu örneği kilitlenirse, pacemaker bekleyen sunucu tarafından *Otomatik* bir HADR tarafından başlatılır. Paceyapıcısı Ayrıca sanal IP adresinin yeni birincil sunucuya atanmasını sağlar.

![IBM DB2 yüksek kullanılabilirliğe genel bakış](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

SAP uygulama sunucularının birincil veritabanına bağlanmasını sağlamak için bir sanal ana bilgisayar adına ve sanal IP adresine sahip olmanız gerekir. Yük devretme durumunda, SAP uygulama sunucuları yeni birincil veritabanı örneğine bağlanır. Bir Azure ortamında, bir sanal IP adresini IBM DB2 için gerekli olacak şekilde kullanmak için bir [Azure yük dengeleyici](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) gereklidir. 

IBM DB2 LUW ile HADR ve Paceyapıcısı 'ın yüksek oranda kullanılabilir bir SAP sistem kurulumuna nasıl uyduğunu tam olarak anlamanıza yardımcı olmak için aşağıdaki görüntüde IBM DB2 veritabanına dayalı bir SAP sisteminin yüksek kullanılabilirliğe sahip bir kurulumuna genel bakış sunulmaktadır. Bu makalede yalnızca IBM DB2 ele alınmaktadır, ancak SAP sisteminin diğer bileşenlerinin nasıl ayarlanacağı hakkında diğer makalelere başvurular sağlanmaktadır.

![IBM DB2 yüksek kullanılabilirlik tam ortamına genel bakış](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Gerekli adımlara üst düzey genel bakış
Bir IBM DB2 yapılandırması dağıtmak için aşağıdaki adımları izlemeniz gerekir:

  + Ortamınızı planlayın.
  + VM 'Leri dağıtın.
  + SUSE Linux 'u güncelleştirme ve dosya sistemlerini yapılandırma.
  + Pacemaker 'ı yükleyip yapılandırın.
  + [Yüksek oranda KULLANILABILIR NFS][nfs-ha]'yi yükler.
  + [Yoks/ers ayrı bir kümeye][ascs-ha]yüklenir.
  + Dağıtılmış/yüksek kullanılabilirlik seçeneği (SWPM) ile IBM DB2 veritabanını yükler.
  + İkincil bir veritabanı düğümü ve örneği yükleyip oluşturun ve HADR 'yi yapılandırın.
  + HADR 'nin çalıştığını doğrulayın.
  + IBM DB2 'yi denetlemek için Paceoluşturucu yapılandırmasını uygulayın.
  + Azure Load Balancer yapılandırın.
  + Birincil ve iletişim kutusu uygulama sunucularını yükler.
  + SAP uygulama sunucularının yapılandırmasını denetleyip uyarlayın.
  + Yük devretme ve yük testi gerçekleştirme.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>HADR ile IBM DB2 LUW barındırmak için Azure altyapısını planlayın

Dağıtımı yürütmeden önce planlama işlemini doldurun. Planlama, Azure 'da HADR ile bir DB2 yapılandırmasına dağıtım temelini oluşturur. IDB db2 LUW planlamasının parçası olması gereken anahtar öğeleri (SAP ortamının veritabanı bölümü) aşağıdaki tabloda listelenmiştir:

| Konu | Kısa açıklama |
| --- | --- |
| Azure kaynak gruplarını tanımlama | VM, VNet, Azure Load Balancer ve diğer kaynakları dağıttığınız kaynak grupları. Mevcut veya yeni olabilir. |
| Sanal ağ/alt ağ tanımı | IBM DB2 ve Azure Load Balancer VM 'lerinin dağıtıldığı yer. Var olan veya yeni oluşturulmuş olabilir. |
| IBM DB2 LUW barındıran sanal makineler | VM boyutu, depolama, ağ, IP adresi. |
| IBM DB2 veritabanı için sanal ana bilgisayar adı ve sanal IP| SAP uygulama sunucularının bağlantısında kullanılan sanal IP veya ana bilgisayar adı. **DB-vırt-hostname**, **DB-vırt-IP**. |
| Azure ile sınırlama | Azure sınırlama veya SBD sınırlama (önemle önerilir). Bölünmüş beyinli durumların önüne geçmek için yöntem. |
| SBD SANAL MAKINESI | SBD sanal makine boyutu, depolama, ağ. |
| Azure Load Balancer | Temel veya standart (önerilir) kullanımı, DB2 veritabanı için yoklama bağlantı noktası (öneri 62500) **araştırma-bağlantı noktasıdır**. |
| Ad çözümlemesi| Ad çözümlemenin ortamda nasıl çalıştığı. DNS hizmeti önemle önerilir. Yerel ana bilgisayarlar dosyası kullanılabilir. |
    
Azure 'da Linux Paceyapıcısı hakkında daha fazla bilgi için bkz. [Azure 'da SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)ayarlama.

## <a name="deployment-on-suse-linux"></a>SUSE Linux üzerinde dağıtım

IBM DB2 LUW için kaynak Aracısı, SAP uygulamalarına yönelik SUSE Linux Enterprise Server eklenmiştir. Bu belgede açıklanan kurulum için, SAP uygulamaları için SUSE Linux Server kullanmanız gerekir. Azure Marketi, yeni Azure sanal makinelerini dağıtmak için kullanabileceğiniz, SAP uygulamaları için SUSE Enterprise Server için bir görüntü içerir. Azure VM marketi 'nde bir VM görüntüsü seçtiğinizde Azure Marketi aracılığıyla SUSE tarafından sunulan çeşitli destek veya hizmet modelleriyle haberdar olun. 

### <a name="hosts-dns-updates"></a>Konaklar: DNS güncelleştirmeleri
Sanal ana bilgisayar adları da dahil olmak üzere tüm konak adlarının listesini oluşturun ve DNS sunucularınızı, konak adı çözümlemesi için doğru IP adresini etkinleştirmek üzere güncelleştirin. Bir DNS sunucusu yoksa veya DNS girdilerini güncelleştirip oluşturamıyoruz, bu senaryoya katılan ayrı VM 'lerin yerel ana bilgisayar dosyalarını kullanmanız gerekir. Ana bilgisayar dosyaları girişleri kullanıyorsanız, girdilerin SAP sistem ortamındaki tüm VM 'lere uygulandığından emin olun. Bununla birlikte, ideal olarak Azure 'a genişleyen DNS 'nizi kullanmanızı öneririz.


### <a name="manual-deployment"></a>El ile dağıtım

Seçilen işletim sisteminin IBM DB2 LUW için IBM/SAP tarafından desteklendiğinden emin olun. Azure VM 'Leri ve DB2 sürümleri için desteklenen işletim sistemi sürümlerinin listesi SAP Note [1928533]' de mevcuttur. Tek bir DB2 sürümüne göre işletim sistemi sürümlerinin listesi SAP ürün kullanılabilirliği matrisinde kullanılabilir. Bu veya sonraki SUSE Linux sürümlerindeki Azure ile ilgili performans iyileştirmeleri nedeniyle en az bir SLES 12 SP4 önerilir.

1. Bir kaynak grubu oluşturun veya seçin.
1. Bir sanal ağ ve alt ağ oluşturun veya seçin.
1. Bir Azure kullanılabilirlik kümesi oluşturun veya bir kullanılabilirlik bölgesi dağıtın.
    + Kullanılabilirlik kümesi için, en fazla güncelleştirme etki alanlarını 2 olarak ayarlayın.
1. Sanal makine oluşturun 1.
    + Azure Marketi 'nde SLES for SAP görüntüsünü kullanın.
    + Adım 3 ' te oluşturduğunuz Azure kullanılabilirlik kümesini seçin veya kullanılabilirlik Bölgesi ' ni seçin.
1.  Sanal makine oluştur 2.
    + Azure Marketi 'nde SLES for SAP görüntüsünü kullanın.
    + Adım 3 ' te oluşturduğunuz Azure kullanılabilirlik kümesini seçin veya kullanılabilirlik Bölgesi ' ni (adım 3 ' te ile aynı bölgeyi değil) seçin.
1. Veri disklerini VM 'lere ekleyin ve ardından [IBM DB2 Azure sanal makineleri IÇIN SAP iş yükü için][dbms-db2]bir dosya sistemi kurulumu önerisi ' ne bakın.

## <a name="create-the-pacemaker-cluster"></a>Paceoluşturucu kümesi oluşturma
    
Bu IBM DB2 sunucusu için temel bir Paceoluşturucu kümesi oluşturmak için bkz. [Azure 'da SUSE Linux Enterprise Server ayarlama][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>IBM DB2 LUW ve SAP ortamını yükler

IBM DB2 LUW 'yu temel alan bir SAP ortamının yüklemesine başlamadan önce, aşağıdaki belgeleri gözden geçirin:

+ Azure belgeleri
+ SAP belgeleri
+ IBM belgeleri

Bu belgenin bağlantıları, bu makalenin giriş bölümünde verilmiştir.

IBM DB2 LUW üzerinde NetWeaver tabanlı uygulamalar yüklemeyle ilgili SAP yükleme kılavuzlarını denetleyin.

SAP [Yükleme Kılavuzu bulucusu][sap-instfind]' nı kullanarak SAP yardım portalındaki kılavuzların bulabilirsiniz.

Aşağıdaki filtreleri ayarlayarak portalda görünen kılavuz sayısını azaltabilirsiniz:

- Şunu yapmak istiyorum: "yeni bir sistem yüklemek"
- Veritabanım: "Linux, Unix ve Windows için IBM DB2"
- SAP NetWeaver sürümleri, yığın yapılandırması veya işletim sistemi için ek filtreler

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>HADR ile IBM DB2 LUW ayarlamaya yönelik yükleme ipuçları

Birincil IBM DB2 LUW veritabanı örneğini ayarlamak için:

- Yüksek kullanılabilirlik veya dağıtılmış seçeneğini kullanın.
- SAP yoks/ERS ve veritabanı örneğini yükler.
- Yeni yüklenen veritabanının bir yedeğini alın.


> [!IMPORTANT] 
> Yükleme sırasında ayarlanan "veritabanı Iletişim bağlantı noktası" nı yazın. Her iki veritabanı örneği için aynı bağlantı noktası numarası olmalıdır

SAP homojen sistem kopyalama yordamını kullanarak bekleme veritabanı sunucusunu ayarlamak için aşağıdaki adımları yürütün:

1. > **Hedef sistemler** > **Dağıtılmış** > **veritabanı örneği**olan **sistem kopyalama** seçeneğini belirleyin.
1. Bir kopyalama yöntemi olarak, yedek sunucu örneğindeki bir yedeği geri yüklemek için yedekleme kullanabilmeniz için **homojen sistemi** ' ni seçin.
1. Homojen sistem kopyası için veritabanını geri yüklemek üzere çıkış adımına ulaştığınızda yükleyiciden çıkın. Veritabanını birincil ana bilgisayarın yedeğinden geri yükleyin. Sonraki yükleme aşamaları, birincil veritabanı sunucusunda zaten yürütüldü.
1. IBM DB2 için HADR 'yi ayarlayın.

   > [!NOTE]
   > Azure ve Paceyapıcısı 'e özgü yükleme ve yapılandırma için: SAP yazılım sağlama Yöneticisi aracılığıyla yükleme yordamı sırasında IBM DB2 LUW için yüksek kullanılabilirlik hakkında açık bir soru vardır:
   >+ **IBM DB2 pureScale**öğesini seçmeyin.
   >+ **Çoklu platformlar IÇIN IBM Tivoli sistem otomasyonu yüklemeyi**seçmeyin.
   >+ **Küme yapılandırma dosyalarını üret**' i seçmeyin.

   Linux Paceyapıcısı için bir SBD cihazı kullandığınızda, aşağıdaki DB2 HADR parametrelerini ayarlayın:
   + HADR eş pencere süresi (saniye) (HADR_PEER_WINDOW) = 300  
   + HADR zaman aşımı değeri (HADR_TIMEOUT) = 60

   Bir Azure pacemaker uçulama Aracısı kullandığınızda, aşağıdaki parametreleri ayarlayın:
   + HADR eş pencere süresi (saniye) (HADR_PEER_WINDOW) = 900  
   + HADR zaman aşımı değeri (HADR_TIMEOUT) = 60

İlk yük devretme/ön yük testi temelinde yukarıdaki parametreleri öneririz. Bu parametre ayarları ile yük devretme ve en uygun işlevsellik için test etmeniz zorunludur. Ayrı ayrı yapılandırmaların değişebildiğinden, parametreler ayarlama gerektirebilir. 

> [!IMPORTANT]
> Normal başlatma ile HADR yapılandırması ile IBM DB2 'e özgü: birincil veritabanı örneğini başlatmadan önce ikincil veya bekleme veritabanı örneği çalışır duruma gelmelidir.

Tanıtım amaçları ve bu makalede açıklanan yordamlar için veritabanı SID 'SI **PTR**'dir.

#### <a name="ibm-db2-hadr-check"></a>IBM DB2 HADR denetimi
HADR yapılandırıldıktan ve durum eş ve birincil ve bekleme düğümlerine BAĞLıYKEN, aşağıdaki denetimi gerçekleştirin:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>DB2 Paceoluşturucu yapılandırması

Düğüm arızası durumunda otomatik yük devretme için pacemaker kullandığınızda, DB2 örneklerinizi ve Pacedevcinizi uygun şekilde yapılandırmanız gerekir. Bu bölümde bu tür bir yapılandırma açıklanmaktadır.

Aşağıdaki öğelerin ön eki vardır:

- **[A]** : tüm düğümlere uygulanabilir
- **[1]** : yalnızca düğüm 1 için geçerlidir 
- **[2]** : yalnızca düğüm 2 ' de geçerlidir

**[A]** pacemaker yapılandırması için Önkoşullar:
1. Db2stop ile Kullanıcı DB2\<SID > her iki veritabanı sunucusunu da kapatın.
1. DB2\<SID > Kullanıcı için Kabuk ortamını */bin/ksh*olarak değiştirin. Yast aracını kullanmanızı öneririz. 


### <a name="pacemaker-configuration"></a>Pacemaker yapılandırması

> [!IMPORTANT]
> En son test, Netcat 'in biriktirme listesi ve yalnızca bir bağlantıyı işleme sınırlaması nedeniyle isteklere yanıt vermeyi durdurduğu ortaya çıkarılan durumlardır. Netcat kaynağı Azure yük dengeleyici isteklerini dinlemeyi durduruyor ve kayan IP kullanılamaz hale gelir.  
> Mevcut Paceüreticisi kümeleri için, [Azure yük dengeleyici algılama sağlamlaştırma](https://www.suse.com/support/kb/doc/?id=7024128)içindeki yönergeleri izleyerek netcat 'i socat ile değiştirmeyi öneririz. Değişikliğin kısa kapalı kalma süresinin gerekli olacağını unutmayın.  

**[1]** IBM DB2 HADR 'e özgü Paceyapıcısı yapılandırması:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** IBM DB2 kaynakları oluşturma:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:<b>62500</b>,backlog=10,fork,reuseaddr /dev/null" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** IBM DB2 kaynaklarını başlatın:
* Pacemaker 'ı bakım modundan çıkar.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların üzerinde çalıştığı düğüm önemli değildir.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 düğüm yapılandırıldı
# <a name="5-resources-configured"></a>5 kaynak yapılandırıldı

# <a name="online--azibmdb01-azibmdb02-"></a>Çevrimiçi: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Kaynakların tam listesi:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-SBD (stonith: dış/SBD): başlatıldı azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Kaynak grubu: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (OCF:: sinyal: IPaddr2): başlatıldı azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (OCF:: sinyal: herhangi bir şey): başlatıldı azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Ana/bağımlı kümesi: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Ana bilgisayarlar: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>SLA 'lar: [azibmdb01]
</pre>

> [!IMPORTANT]
> Paceoluşturucu aracını kullanarak pacemaker kümelenmiş DB2 örneğini yönetmeniz gerekir. Db2stop gibi DB2 komutları kullanıyorsanız Paceyapıcısı eylemi kaynak hatası olarak algılar. Bakım yapıyorsanız, düğümleri veya kaynakları bakım moduna alabilirsiniz. Paceyapıcısı izleme kaynaklarını askıya alır ve ardından normal DB2 yönetim komutlarını kullanabilirsiniz.


### <a name="configure-azure-load-balancer"></a>Azure Load Balancer'ı yapılandırma
Azure Load Balancer yapılandırmak için, [Azure Standart Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) 'sunu kullanmanızı ve ardından şunları yapmanızı öneririz.

1. Ön uç IP havuzu oluşturun:

   a. Azure portal, Azure Load Balancer açın, **ön uç IP havuzu**' nu seçin ve ardından **Ekle**' yi seçin.

   b. Yeni ön uç IP havuzunun adını girin (örneğin, **DB2-bağlantı**).

   c. **Atamayı** **statik**olarak AYARLAYıN ve başlangıçta tanımlanan IP adresini **sanal IP** adresi olarak girin.

   d. **Tamam**’ı seçin.

   e. Yeni ön uç IP havuzu oluşturulduktan sonra, havuzun IP adresini aklınızda edin.

1. Arka uç havuzu oluşturma:

   a. Azure portal, Azure Load Balancer açın, **arka uç havuzları**' nı seçin ve ardından **Ekle**' yi seçin.

   b. Yeni arka uç havuzunun adını girin (örneğin, **DB2 arka ucu**).

   c. **Sanal makine Ekle**' yi seçin.

   d. Önceki adımda oluşturulan kullanılabilirlik kümesini veya IBM DB2 veritabanını barındıran sanal makineleri seçin.

   e. IBM DB2 kümesinin sanal makinelerini seçin.

   f. **Tamam**’ı seçin.

1. Bir sistem durumu araştırması oluşturun:

   a. Azure portal, Azure Load Balancer açın, **sistem durumu araştırmaları**' nı seçin ve **Ekle**' yi seçin.

   b. Yeni sistem durumu araştırmasının adını girin (örneğin, **DB2-HP**).

   c. Protokol ve bağlantı noktası **62500**olarak **TCP** ' yi seçin. **Aralık** değerini **5**olarak ayarlayın ve **sağlıksız eşik** değerini **2**olarak ayarlayın.

   d. **Tamam**’ı seçin.

1. Yük Dengeleme kurallarını oluşturun:

   a. Azure portal, Azure Load Balancer açın, **Yük Dengeleme kuralları**' nı seçin ve ardından **Ekle**' yi seçin.

   b. Yeni Load Balancer kuralının adını girin (örneğin, **DB2-SID**).

   c. Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **DB2-ön uç**) seçin.

   d. **Protokolü** **TCP**olarak ayarlayın ve bağlantı noktası *veritabanı iletişim bağlantı noktasını*girin.

   e. **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.

   f. **Kayan IP**'yi etkinleştirdiğinizden emin olun.

   g. **Tamam**’ı seçin.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Bağlantı için sanal IP 'yi kullanmak üzere SAP profillerinde değişiklikler yapın
HADR yapılandırmasının birincil örneğine bağlanmak için, SAP uygulama katmanının Azure Load Balancer için tanımladığınız ve yapılandırdığınız sanal IP adresini kullanması gerekir. Aşağıdaki değişiklikler gereklidir:

/sapmnt/\<SıD >/profile/DEFAULT. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SıD >/Global/DB6/Db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Birincil ve iletişim kutusu uygulama sunucularını yükler

Birincil ve iletişim uygulama sunucularını bir DB2 HADR yapılandırmasına karşı yüklediğinizde, yapılandırma için seçtiğiniz sanal ana bilgisayar adını kullanın. 

DB2 HADR yapılandırmasını oluşturmadan önce yüklemeyi gerçekleştirdiyseniz, önceki bölümde açıklandığı gibi ve SAP Java yığınları için aşağıdaki gibi değişiklikleri yapın.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java ya da Java Stack Systems JDBC URL denetimi

JDBC URL 'sini denetlemek veya güncelleştirmek için J2EE yapılandırma aracını kullanın. J2EE yapılandırma aracı bir grafik araç olduğundan, X sunucusunun yüklü olması gerekir:
 
1. J2EE örneğinin birincil uygulama sunucusunda oturum açın ve şunu çalıştırın: `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. Sol çerçevede **Güvenlik deposu**' nu seçin.
1. Sağ çerçevede, anahtar JDBC/havuz/\<SAPSID >/urlu seçin.
1. JDBC URL 'sindeki ana bilgisayar adını sanal ana bilgisayar adıyla değiştirin.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. **Add (Ekle)** seçeneğini belirleyin.
1. Değişikliklerinizi kaydetmek için sol üst köşedeki disk simgesini seçin.
1. Yapılandırma aracını kapatın.
1. Java örneğini yeniden başlatın.

## <a name="configure-log-archiving-for-hadr-setup"></a>HADR kurulumu için Günlük arşivlemeyi yapılandırma
HADR kurulumu için DB2 Günlük arşivlemeyi yapılandırmak üzere, hem birincil hem de bekleme veritabanını tüm günlük Arşivi konumlarından otomatik günlük alma özelliğine sahip olacak şekilde yapılandırmanızı öneririz. Hem birincil hem de bekleme veritabanının günlük arşivi dosyalarını, veritabanı örneklerinden birinin günlük dosyalarını arşivleyebileceğiniz tüm günlük Arşivi konumlarından alabilmesi gerekir. 

Günlük arşivleme yalnızca birincil veritabanı tarafından gerçekleştirilir. Veritabanı sunucularının HADR rollerini değiştirirseniz veya bir hata oluşursa, yeni birincil veritabanı, günlük arşivlemeden sorumludur. Birden çok günlük arşiv konumu ayarladıysanız günlüklerinizin iki kez arşivlenmesi gerekebilir. Yerel veya uzak bir catch durumunda, arşivlenmiş günlükleri eski birincil sunucudan yeni birincil sunucunun etkin günlük konumuna el ile kopyalamanız de gerekebilir.

Günlüklerin her iki düğümden de yazıldığı ortak bir NFS paylaşımının yapılandırılmasını öneririz. NFS paylaşımının yüksek oranda kullanılabilir olması vardır. 

Aktarımlar için mevcut olan yüksek oranda kullanılabilir NFS paylaşımlarını veya bir profil dizini kullanabilirsiniz. Daha fazla bilgi için bkz.

- [SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde NFS için yüksek kullanılabilirlik][nfs-ha] 
- [SAP uygulamaları için Azure NetApp Files SUSE Linux Enterprise Server üzerindeki Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (NFS paylaşımları oluşturmak için)


## <a name="test-the-cluster-setup"></a>Küme kurulumunu test etme

Bu bölüm, DB2 HADR kurulumunuzu nasıl test kullanabileceğinizi açıklar. *Her test, Kullanıcı kökü olarak oturum açtığınızı* ve IBM DB2 birincili *azibmdb01* sanal makinesinde çalıştığını varsayar.

Tüm test çalışmalarının ilk durumu burada açıklanmıştır: (crm_mon-r veya CRM durumu)

- **CRM durumu** , yürütme sırasında pacemaker durumunun bir anlık görüntüsüdür 
- **crm_mon-r** , pacemaker durumunun sürekli çıkışı

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Bir SAP sistemindeki özgün durum, aşağıdaki görüntüde gösterildiği gibi, Işlem DBAKOKPIT > Yapılandırma > Genel Bakış ' da belgelenmiştir:

![Dbakokpit-geçiş öncesi](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>IBM DB2 'nin test etme


> [!IMPORTANT] 
> Teste başlamadan önce şunları yaptığınızdan emin olun:
> * Pacemaker başarısız olan eylemlere sahip değil (CRM durumu).
> * Hiçbir konum kısıtlaması yok (geçiş testinin sol üyesi ol)
> * IBM DB2 HADR eşitlemesi çalışıyor. Kullanıcı DB2\<SID > denetleyin <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Aşağıdaki komutu yürüterek birincil DB2 veritabanını çalıştıran düğümü geçirin:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Geçiş yapıldıktan sonra, CRM durum çıktısı şöyle görünür:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Bir SAP sistemindeki özgün durum, aşağıdaki görüntüde gösterildiği gibi, Işlem DBAKOKPIT > Yapılandırma > Genel Bakış ' da belgelenmiştir:

![Dbakokpit-geçiş sonrası](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

"CRM Resource Migrate" ile kaynak geçişi konum kısıtlamaları oluşturur. Konum kısıtlamaları silinmelidir. Konum kısıtlamaları silinmediği takdirde kaynak yeniden başarısız olabilir veya istenmeyen yük devretme işlemleri yaşayabilirsiniz. 

Kaynağı *azibmdb01* 'e geri geçirin ve konum kısıtlamalarını temizleyin
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **CRM kaynak geçişi \<res_name > \<konak >:** Konum kısıtlamaları oluşturur ve ele alınmasına neden olabilir
- **CRM kaynağı temizle \<res_name >** : konum kısıtlamalarını temizler
- **CRM kaynak temizleme \<res_name >** : kaynağın tüm hatalarını temizler

### <a name="test-the-fencing-agent"></a>Sınırlama aracısını test etme

Bu durumda, SUSE Linux kullandığınızda yapmanız önerilir, SBD uçuyoruz.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

*Azibmdb01* küme düğümü yeniden başlatılmalıdır. IBM DB2 birincil HADR rolü *azibmdb02*'e taşınacak. *Azibmdb01* yeniden çevrimiçi olduğunda, DB2 örneği bir ikincil veritabanı örneğinin rolünde hareket eteceğiz. 

Paceoluşturucu hizmeti, yeniden başlatılan eski birincil bilgisayarda otomatik olarak başlamazsa, ile el ile başlatmayı unutmayın:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>El ile devralmayı test etme

*Azibmdb01* düğümündeki Paceyapıcısı hizmetini durdurarak el ile bir yük testi test edebilirsiniz:
<pre><code>service pacemaker stop</code></pre>

*azibmdb02* üzerindeki durum
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Yük devretmeden sonra, *azibmdb01*'de hizmeti yeniden başlatabilirsiniz.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>HADR birincil veritabanını çalıştıran düğümde DB2 işlemini sonlandırın

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

DB2 örneği başarısız olur ve Paceyapıcısı şu durumu raporlar:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Paceyapıcısı, DB2 birincil veritabanı örneğini aynı düğümde yeniden başlatacak veya ikincil veritabanı örneğini çalıştıran düğüme yük devreder ve bir hata bildirilir.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>İkincil veritabanı örneğini çalıştıran düğümdeki DB2 işlemini Sonlandır

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Düğüm, hatalı bir şekilde ifade edildi ve bildirildi
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

DB2 örneği, daha önce atandığı ikincil rolde yeniden başlatılır.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>HADR birincil veritabanı örneğini çalıştıran düğümde db2stop zorlamalı aracılığıyla DB 'yi durdur

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Kullanıcı DB2\<SID > Execute komutu db2stop zorlama:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Hata algılandı
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

DB2 HADR ikincil veritabanı örneği, birincil role yükseltildi
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>HADR birincil veritabanı örneğini çalıştıran düğümde yeniden başlatma ile kilitlenme sanal makinesi

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Paceyapıcısı ikincil örneği birincil örnek rolüne yükseltir. Eski birincil örnek VM 'den sonra ikincil role taşınır ve VM yeniden başlatıldıktan sonra tüm hizmetler tamamen geri yüklenir:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>HADR birincil veritabanı örneğini çalıştıran VM 'yi "Durdur" ile çökme

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Böyle bir durumda, Paceyapıcısı, birincil veritabanı örneğini çalıştıran düğümün yanıt vermediğini algılar.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Sonraki adım bölünmüş bir beyinme durumu olup olmadığını *denetlemedir* . Kalan düğüm, birincil veritabanı örneğini son çalıştıran düğümün çalıştığını belirledikten sonra, kaynakların yük devretmesi yürütülür.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


Düğümün "durdurma" durumunda, başarısız olan düğüm Azure Yönetim Araçları (Azure portal, PowerShell veya Azure CLı) aracılığıyla yeniden başlatılmalıdır. Hatalı düğüm yeniden çevrimiçi olduktan sonra, DB2 örneğini ikincil role başlatır.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Sonraki adımlar
- [SAP NetWeaver için yüksek kullanılabilirliğe sahip mimari ve senaryolar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Azure 'da SUSE Linux Enterprise Server Paceyapıcısı ayarlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

