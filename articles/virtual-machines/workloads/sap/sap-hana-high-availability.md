---
title: SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde SAP HANA yüksek kullanılabilirliği | Microsoft Docs
description: SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde SAP HANA yüksek kullanılabilirliği
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: sedusch
ms.openlocfilehash: ffa2f937a14aa14750480d1c45498fb4c49fcc30
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721508"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde SAP HANA yüksek kullanılabilirliği

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]: https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Şirket içi geliştirme için, her iki HANA sistem çoğaltmasını kullanabilir veya SAP HANA için yüksek kullanılabilirlik sağlamak üzere paylaşılan depolama alanını kullanabilirsiniz.
Azure sanal makinelerinde (VM), Azure 'da HANA sistem çoğaltması Şu anda desteklenen tek yüksek kullanılabilirlik işlevidir. SAP HANA çoğaltma bir birincil düğümden ve en az bir ikincil düğümden oluşur. Birincil düğümdeki verilerde yapılan değişiklikler ikincil düğüme zaman uyumlu veya zaman uyumsuz olarak çoğaltılır.

Bu makalede, sanal makinelerin nasıl dağıtılacağı ve yapılandırılacağı, küme çerçevesinin nasıl yükleneceği ve SAP HANA sistem çoğaltmasının nasıl yükleneceği ve yapılandırılacağı açıklanmaktadır.
Örnek yapılandırmalarda, yükleme komutları, örnek numarası **03**ve Hana sistem kimliği **HN1** kullanılır.

Önce aşağıdaki SAP notlarını ve kağıtları okuyun:

* SAP Note [1928533], şunları içerir:
  * SAP yazılımının dağıtımı için desteklenen Azure VM boyutlarının listesi.
  * Azure VM boyutları için önemli kapasite bilgileri.
  * Desteklenen SAP yazılımı ve işletim sistemi (OS) ve veritabanı birleşimleri.
  * Microsoft Azure üzerinde Windows ve Linux için gereken SAP çekirdek sürümü.
* SAP Note [2015553] , Azure 'da SAP tarafından desteklenen SAP yazılım dağıtımları için önkoşulları listeler.
* SAP Note [2205917] , SAP uygulamaları için SUSE Linux Enterprise Server için önerilen işletim sistemi ayarlarını içerir.
* SAP Note [1944799] , SAP uygulamaları için SUSE Linux Enterprise Server SAP HANA kılavuz içerir.
* SAP Note [2178632] , Azure 'da SAP için raporlanan tüm izleme ölçümleriyle ilgili ayrıntılı bilgiler içerir.
* SAP Note [2191498] , Azure 'da Linux IÇIN gereken SAP konak Aracısı sürümüne sahiptir.
* SAP Note [2243692] , Azure 'da LINUX üzerinde SAP lisanslama hakkında bilgi içerir.
* SAP Note [1984787] , SUSE Linux Enterprise Server 12 hakkında genel bilgiler içerir.
* SAP Note [1999351] , SAP Için Azure Gelişmiş izleme uzantısı için ek sorun giderme bilgilerine sahiptir.
* SAP Note [401162] , Hana sistem çoğaltmasını ayarlarken "adresin zaten kullanımda" olmaması hakkında bilgi içerir.
* [SAP COMMUNITY WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) , Linux için gereken tüm sap notlarını içerir.
* [Sertifikalı IaaS platformlarını SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Linux 'TA SAP Için Azure sanal makineleri planlama ve uygulama][planning-guide] .
* [Linux 'TA SAP Için Azure sanal makineler dağıtımı][deployment-guide] (Bu makale).
* [Linux 'TA SAP Için Azure sanal MAKINELER DBMS dağıtımı][dbms-guide] .
* [SAP uygulamaları için SUSE Linux Enterprise Server 12 SP3 en iyi yöntemler Kılavuzu][sles-for-sap-bp]
  * SAP HANA SR performansı için Iyileştirilmiş altyapıyı ayarlama (SLES for SAP Applications 12 SP1). Rehber, şirket içi geliştirme için SAP HANA sistem çoğaltmasını ayarlamak üzere gerekli tüm bilgileri içerir. Bu kılavuzu temel olarak kullanın.
  * SAP HANA SR maliyetle Iyileştirilmiş altyapıyı ayarlama (SLES for SAP Applications 12 SP1)

## <a name="overview"></a>Genel Bakış

Yüksek kullanılabilirlik elde etmek için SAP HANA iki sanal makineye yüklenir. Veriler, HANA sistem çoğaltması kullanılarak çoğaltılır.

![SAP HANA yüksek kullanılabilirliğe genel bakış](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA sistem çoğaltma Kurulumu, ayrılmış bir sanal konak adı ve sanal IP adresleri kullanır. Azure 'da bir sanal IP adresi kullanmak için bir yük dengeleyici gereklidir. Aşağıdaki listede yük dengeleyicinin yapılandırması gösterilmektedir:

* Ön uç yapılandırması: hn1-DB için IP adresi 10.0.0.13
* Arka uç yapılandırması: HANA sistem çoğaltmasının parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
* Araştırma bağlantı noktası: bağlantı noktası 62503
* Yük Dengeleme kuralları: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Linux için dağıtma

SAP HANA için kaynak Aracısı, SAP uygulamalarına yönelik SUSE Linux Enterprise Server eklenmiştir.
Azure Marketi, yeni sanal makineler dağıtmak için kullanabileceğiniz, SAP uygulamaları için SUSE Linux Enterprise Server bir görüntü içerir.

### <a name="deploy-with-a-template"></a>Şablon ile dağıtma

GitHub üzerinde olan hızlı başlangıç şablonlarından birini, gerekli tüm kaynakları dağıtmak için kullanabilirsiniz. Şablon, sanal makineleri, yük dengeleyiciyi, kullanılabilirlik kümesini ve benzerlerini dağıtır.
Şablonu dağıtmak için aşağıdaki adımları izleyin:

1. Azure portal [veritabanı şablonunu][template-multisid-db] veya [yakınsanmış şablonu][template-converged] açın. 
    Veritabanı şablonu yalnızca bir veritabanı için Yük Dengeleme kuralları oluşturur. Yakınsama şablonu, bir yoks/SCS ve ERS (yalnızca Linux) örneği için Yük Dengeleme kuralları da oluşturur. SAP NetWeaver tabanlı bir sistem yüklemeyi planlıyorsanız ve yoks/SCS örneğini aynı makinelere yüklemek istiyorsanız [yakınsama şablonunu][template-converged]kullanın.

1. Aşağıdaki parametreleri girin:
    - **SAP SISTEM kimliği**: yüklemek istediğiniz SAP sisteminin SAP sistem kimliğini girin. KIMLIK, dağıtılan kaynakların ön eki olarak kullanılır.
    - **Yığın türü**: (Bu parametre yalnızca yakınsama şablonu kullanıyorsanız uygulanabilir.) SAP NetWeaver Stack türünü seçin.
    - **Işletim sistemi türü**: Linux dağılımından birini seçin. Bu örnek için **SLES 12**' yi seçin.
    - **Veritabanı türü**: **Hana**seçin.
    - **SAP sistem boyutu**: yeni sistemin SAĞLAYACAĞı SAPS sayısını girin. Sistemin kaç tane için gerekli olduğundan emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun.
    - **Sistem kullanılabilirliği**: **ha**seçeneğini belirleyin.
    - **Yönetici Kullanıcı adı ve yönetici parolası**: makinede oturum açmak için kullanılabilecek yeni bir Kullanıcı oluşturulur.
    - **Yeni veya var olan alt ağ**: yeni bir sanal ağın ve alt ağın oluşturulması gerekip gerekmediğini veya mevcut bir alt ağın kullanıldığını belirler. Şirket içi ağınıza bağlı bir sanal ağınız zaten varsa, **mevcut**' ı seçin.
    - **Alt ağ kimliği**: VM 'yi tanımlanmış bir alt ağa sahip olduğunuz mevcut bir VNET 'e dağıtmak istiyorsanız, söz konusu alt ağın kimliğini adlandırın. KIMLIK genellikle **/Subscriptions/\<ABONELIK kimliği >/resourceGroups/\<kaynak grubu adı >/providers/Microsoft.Network/virtualNetworks/\<sanal ağ adı >/Subnets/\<alt ağ adı >** .

### <a name="manual-deployment"></a>El ile dağıtım

> [!IMPORTANT]
> Seçtiğiniz işletim sisteminin, kullanmakta olduğunuz belirli VM türlerinde SAP HANA için SAP sertifikalı olduğundan emin olun. Bu kişiler için SAP HANA sertifikalı VM türlerinin ve işletim sistemi sürümlerinin listesi, [SAP HANA sertifikalı IaaS platformlarında](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)aranabilir. Belirli bir sanal makine türü için desteklenen SAP HANA işletim sistemi sürümlerinin tüm listesini almak üzere listelenen VM türünün ayrıntılarına tıkladığınızdan emin olun
>  

1. Bir kaynak grubu oluşturun.
1. Sanal ağ oluşturun.
1. Bir kullanılabilirlik kümesi oluşturun.
   - En fazla güncelleştirme etki alanını ayarlayın.
1. Yük Dengeleyici (iç) oluşturun. [Standart yük dengeleyiciyi](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)öneririz.
   - 2\. adımda oluşturulan sanal ağı seçin.
1. Sanal makine oluşturun 1.
   - Seçtiğiniz VM türü üzerinde SAP HANA için desteklenen Azure galerisinde bir SLES4SAP görüntüsü kullanın.
   - Adım 3 ' te oluşturulan kullanılabilirlik kümesini seçin.
1. Sanal makine oluştur 2.
   - Seçtiğiniz VM türü üzerinde SAP HANA için desteklenen Azure galerisinde bir SLES4SAP görüntüsü kullanın.
   - Adım 3 ' te oluşturulan kullanılabilirlik kümesini seçin. 
1. Veri diskleri ekleyin.
1. Standart yük dengeleyici kullanıyorsanız, bu yapılandırma adımlarını izleyin:
   1. İlk olarak, bir ön uç IP havuzu oluşturun:
   
      1. Yük dengeleyiciyi açın, **ön uç IP havuzu**' nu seçin ve **Ekle**' yi seçin.
      1. Yeni ön uç IP havuzunun adını girin (örneğin, **Hana-ön uç**).
      1. **Atamayı** **statik** olarak ayarlayın ve IP adresini (örneğin, **10.0.0.13**) girin.
      1. **Tamam**’ı seçin.
      1. Yeni ön uç IP havuzu oluşturulduktan sonra, havuzun IP adresini aklınızda edin.
   
   1. Sonra, bir arka uç havuzu oluşturun:
   
      1. Yük dengeleyiciyi açın, **arka uç havuzları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni arka uç havuzunun adını girin (örneğin, **Hana arka ucu**).
      1. **Sanal ağ**' ı seçin.
      1. **Sanal makine Ekle**' yi seçin.
      1. \* * Sanal makine * * öğesini seçin.
      1. SAP HANA kümesinin sanal makinelerini ve IP adreslerini seçin.
      1. **Add (Ekle)** seçeneğini belirleyin.
   
   1. Sonra, bir sistem durumu araştırması oluşturun:
   
      1. Yük dengeleyiciyi açın, **sistem durumu araştırmaları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni sistem durumu araştırmasının adını girin (örneğin, **Hana-HP**).
      1. Protokol ve bağlantı noktası 625**03**olarak **TCP** ' yi seçin. **Aralık** değerini 5 olarak ve **sağlıksız eşik** değerini 2 olarak ayarlayın.
      1. **Tamam**’ı seçin.
   
   1. Sonra, Yük Dengeleme kurallarını oluşturun:
   
      1. Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni yük dengeleyici kuralının adını girin (örneğin, **Hana-lb**).
      1. Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **Hana-ön uç**, **Hana-arka uç** ve **Hana-HP**) seçin.
      1. **Ha bağlantı noktalarını**seçin.
      1. **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
      1. **Kayan IP**'yi etkinleştirdiğinizden emin olun.
      1. **Tamam**’ı seçin.

   > [!Note]
   > Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyicisine yerleştirildiğinde, genel uç noktalara yönlendirmeye izin vermek için ek yapılandırma gerçekleştirilmediği takdirde giden internet bağlantısı olmaz. Giden bağlantıyı elde etme hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer kullanan sanal makineler Için genel uç nokta BAĞLANTıSı SAP yüksek kullanılabilirlik senaryolarında](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

1. Alternatif olarak, senaryonuz temel yük dengeleyiciyi kullanmayı belirlemesi durumunda aşağıdaki yapılandırma adımlarını izleyin:
   1. İlk olarak, bir ön uç IP havuzu oluşturun:
   
      1. Yük dengeleyiciyi açın, **ön uç IP havuzu**' nu seçin ve **Ekle**' yi seçin.
      1. Yeni ön uç IP havuzunun adını girin (örneğin, **Hana-ön uç**).
      1. **Atamayı** **statik** olarak ayarlayın ve IP adresini (örneğin, **10.0.0.13**) girin.
      1. **Tamam**’ı seçin.
      1. Yeni ön uç IP havuzu oluşturulduktan sonra, havuzun IP adresini aklınızda edin.
   
   1. Sonra, bir arka uç havuzu oluşturun:
   
      1. Yük dengeleyiciyi açın, **arka uç havuzları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni arka uç havuzunun adını girin (örneğin, **Hana arka ucu**).
      1. **Sanal makine Ekle**' yi seçin.
      1. Adım 3 ' te oluşturulan kullanılabilirlik kümesini seçin.
      1. SAP HANA kümesinin sanal makinelerini seçin.
      1. **Tamam**’ı seçin.
   
   1. Sonra, bir sistem durumu araştırması oluşturun:
   
      1. Yük dengeleyiciyi açın, **sistem durumu araştırmaları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni sistem durumu araştırmasının adını girin (örneğin, **Hana-HP**).
      1. Protokol ve bağlantı noktası 625**03**olarak **TCP** ' yi seçin. **Aralık** değerini 5 olarak ve **sağlıksız eşik** değerini 2 olarak ayarlayın.
      1. **Tamam**’ı seçin.
   
   1. SAP HANA 1,0 için, Yük Dengeleme kurallarını oluşturun:
   
      1. Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni yük dengeleyici kuralının adını girin (örneğin, Hana-lb-3**03**15).
      1. Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **Hana-ön uç**) seçin.
      1. **Protokolü** **TCP**olarak ayarlayın ve bağlantı noktası 3**03**15 girin.
      1. **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
      1. **Kayan IP**'yi etkinleştirdiğinizden emin olun.
      1. **Tamam**’ı seçin.
      1. Bağlantı noktası 3**03**17 için bu adımları tekrarlayın.
   
   1. SAP HANA 2,0 için, sistem veritabanı için Yük Dengeleme kurallarını oluşturun:
   
      1. Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni yük dengeleyici kuralının adını girin (örneğin, Hana-lb-3**03**13).
      1. Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **Hana-ön uç**) seçin.
      1. **Protokolü** **TCP**olarak ayarlayın ve bağlantı noktası 3**03**13 yazın.
      1. **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
      1. **Kayan IP**'yi etkinleştirdiğinizden emin olun.
      1. **Tamam**’ı seçin.
      1. Bağlantı noktası 3**03**14 için bu adımları tekrarlayın.
   
   1. SAP HANA 2,0 için, önce Kiracı veritabanı için Yük Dengeleme kurallarını oluşturun:
   
      1. Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni yük dengeleyici kuralının adını girin (örneğin, Hana-lb-3**03**40).
      1. Daha önce oluşturduğunuz ön uç IP adresini, arka uç havuzunu ve sistem durumu araştırmasını seçin (örneğin, **Hana-ön uç**).
      1. **Protokolü** **TCP**olarak ayarlayın ve bağlantı noktası 3**03**40 yazın.
      1. **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
      1. **Kayan IP**'yi etkinleştirdiğinizden emin olun.
      1. **Tamam**’ı seçin.
      1. Bağlantı noktaları 3**03**41 ve 3**03**42 için bu adımları yineleyin.

   SAP HANA için gereken bağlantı noktaları hakkında daha fazla bilgi için, [SAP HANA kiracı veritabanları](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) Kılavuzu veya [SAP Note 2388694][2388694]' de [kiracı veritabanlarına yönelik bölüm bağlantılarını](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) okuyun.

> [!IMPORTANT]
> Azure Load Balancer arkasına yerleştirilmiş Azure VM 'lerinde TCP zaman damgalarını etkinleştirmeyin. TCP zaman damgalarını etkinleştirmek, sistem durumu araştırmalarının başarısız olmasına neden olur. **Net. IPv4. TCP _Zaman damgaları** parametresini **0**olarak ayarlayın. Ayrıntılar için bkz. [Load Balancer sistem durumu araştırmaları](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).
> Ayrıca bkz. SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Paceoluşturucu kümesi oluşturma

Bu HANA sunucusu için temel bir Paceoluşturucu kümesi oluşturmak üzere [Azure 'daki SUSE Linux Enterprise Server Paceyapıcısı ayarlama](high-availability-guide-suse-pacemaker.md) bölümündeki adımları izleyin. SAP HANA ve SAP NetWeaver (A) SCS için aynı Paceoluşturucu kümesini kullanabilirsiniz.

## <a name="install-sap-hana"></a>SAP HANA yükleme

Bu bölümdeki adımlarda aşağıdaki ön ekler kullanılır:
- **[A]** : adım tüm düğümler için geçerlidir.
- **[1]** : adım yalnızca düğüm 1 ' e uygulanır.
- **[2]** : adım yalnızca Paceoluşturucu kümesinin node 2 ' de geçerlidir.

1. **[A]** disk düzeni ayarlama: **mantıksal birim Yöneticisi (LVM)** .

   Veri ve günlük dosyalarını depolayan birimlerde LVM kullanmanızı öneririz. Aşağıdaki örnek, sanal makinelerin iki birim oluşturmak için kullanılan dört veri diskine sahip olduğunu varsayar.

   Tüm kullanılabilir diskleri listeleyin:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Örnek çıktı:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Kullanmak istediğiniz tüm diskler için fiziksel birimler oluşturun:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Veri dosyaları için bir birim grubu oluşturun. Günlük dosyaları için bir birim grubu ve SAP HANA paylaşılan dizinine yönelik bir tane kullanın:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Mantıksal birimleri oluşturun. `-i` anahtarı olmadan `lvcreate` kullandığınızda doğrusal bir birim oluşturulur. Daha iyi g/ç performansı için, `-i` bağımsız değişkeninin temeldeki fiziksel birimin sayısı olması gereken bir şeritli birim oluşturmanızı öneririz. Bu belgede, veri hacmi için iki fiziksel birim kullanılır, bu nedenle `-i` Switch bağımsız değişkeni **2**olarak ayarlanır. Günlük birimi için bir fiziksel birim kullanıldığından, hiçbir `-i` anahtarı açık olarak kullanılmaz. Her bir veri, günlük veya paylaşılan birim için birden fazla fiziksel birim kullandığınızda `-i` anahtarını kullanın ve temel alınan fiziksel birimin numarasını ayarlayın.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Bağlama dizinlerini oluşturun ve tüm mantıksal birimlerin UUID 'sini kopyalayın:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Üç mantıksal birim için `fstab` girişleri oluşturun:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   `/etc/fstab` dosyasına aşağıdaki satırı ekleyin:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Yeni birimleri bağlama:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** disk düzeni ayarlama: **düz diskler**.

   Demo sistemlerinde, HANA verilerinizi ve günlük dosyalarınızı bir diske yerleştirebilirsiniz. /Dev/disk/Azure/scsi1/lun0 üzerinde bir bölüm oluşturun ve XFS ile biçimlendirin:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Bu satırı/etc/fstab dosyasına ekleyin:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Hedef dizini oluşturun ve diski bağlayın:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** tüm konaklar için konak adı çözümlemesi ayarlayın.

   Bir DNS sunucusu kullanabilir ya da tüm düğümlerdeki/etc/hosts dosyasını değiştirebilirsiniz. Bu örnekte,/etc/hosts dosyasının nasıl kullanılacağı gösterilmektedir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   /Etc/hosts dosyasına aşağıdaki satırları ekleyin. IP adresini ve ana bilgisayar adını ortamınızla eşleşecek şekilde değiştirin:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** SAP HANA yüksek kullanılabilirlik paketlerini yükler:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

SAP HANA sistem çoğaltmasını yüklemek için, [SAP HANA SR performansı Iyileştirilmiş senaryo kılavuzunun](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/)4. bölümünü izleyin.

1. **[A]** Hana DVD 'den **hdblcm** programını çalıştırın. Komut istemine aşağıdaki değerleri girin:
   * Yükleme seç: **1**yazın.
   * Yükleme için ek bileşenler seçin: **1**girin.
   * Yükleme yolunu girin [/Hana/Shared]: ENTER ' ı seçin.
   * Yerel ana bilgisayar adı [..] girin: ENTER ' u seçin.
   * Sisteme ek konaklar eklemek istiyor musunuz? (e/h) [n]: ENTER ' ı seçin.
   * SAP HANA sistem KIMLIĞINI girin: HANA 'nın SID 'sini girin, örneğin: **HN1**.
   * Örnek numarasını girin [00]: HANA örnek numarasını girin. Azure şablonunu kullandıysanız veya bu makalenin el ile dağıtım bölümünü izlediyseniz **03** girin.
   * Veritabanı modunu seçin/Dizin [1] girin: ENTER ' u seçin.
   * Sistem kullanımı/dizin girin [4]: sistem kullanım değerini seçin.
   * [/Hana/data/HN1] veri birimlerinin konumunu girin: ENTER ' u seçin.
   * [/Hana/log/HN1] günlük birimlerinin konumunu girin: ENTER ' u seçin.
   * Maksimum bellek ayırmayı kısıtla mı? [n]: ENTER ' ı seçin.
   * '... ' Konağının sertifika ana bilgisayar adını girin [...]: ENTER ' ı seçin.
   * SAP konak Aracısı Kullanıcı (sapadm) parolasını girin: konak Aracısı Kullanıcı parolasını girin.
   * SAP konak aracısı kullanıcısı (sapadm) parolasını onaylayın: onaylamak için konak Aracısı Kullanıcı parolasını yeniden girin.
   * Sistem Yöneticisi (hdbadm) parolasını girin: Sistem Yöneticisi parolasını girin.
   * Sistem Yöneticisi (hdbadm) parolasını onaylayın: onaylamak için sistem yöneticisi parolasını yeniden girin.
   * Sistem Yöneticisi giriş dizinini girin [/usr/sap/HN1/home]: ENTER ' ı seçin.
   * Sistem Yöneticisi oturum açma kabuğunu girin [/bin/sh]: ENTER ' u seçin.
   * Sistem Yöneticisi kullanıcı KIMLIĞINI girin [1001]: ENTER ' u seçin.
   * Kullanıcı grubunun KIMLIĞINI girin (sapsys) [79]: ENTER ' u seçin.
   * Veritabanı kullanıcı (SISTEM) parolasını girin: veritabanı kullanıcı parolasını girin.
   * Veritabanı kullanıcı (SISTEM) parolasını onaylayın: onaylamak için veritabanı kullanıcı parolasını yeniden girin.
   * Makine yeniden başlatıldıktan sonra sistem yeniden başlatılsın mı? [n]: ENTER ' ı seçin.
   * Devam etmek istiyor musunuz? (e/h): özeti doğrulayın. Devam etmek için **y** girin.

1. **[A]** SAP konak aracısını yükseltin.

   [SAP yazılım merkezi][sap-swcenter] 'nden en son SAP konak Aracısı arşivini indirin ve aracıyı yükseltmek için aşağıdaki komutu çalıştırın. Arşiv yolunu, indirdiğiniz dosyayı işaret etmek için değiştirin:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2,0 sistem çoğaltmasını yapılandırma

Bu bölümdeki adımlarda aşağıdaki ön ekler kullanılır:

* **[A]** : adım tüm düğümler için geçerlidir.
* **[1]** : adım yalnızca düğüm 1 ' e uygulanır.
* **[2]** : adım yalnızca Paceoluşturucu kümesinin node 2 ' de geçerlidir.

1. **[1]** kiracı veritabanını oluşturun.

   SAP HANA 2,0 veya MDC kullanıyorsanız, SAP NetWeaver sisteminiz için bir kiracı veritabanı oluşturun. **NW1** değerini SAP sisteminizin SID 'si ile değiştirin.

   < Hanasıd\>adm olarak aşağıdaki komutu yürütün:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Ilk düğümde sistem çoğaltmasını yapılandırın:

   Veritabanlarını < hanasıd\>adm olarak yedekleyin:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Sistem PKI dosyalarını ikincil siteye kopyalayın:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Birincil siteyi oluşturun:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Ikinci düğümde sistem çoğaltmasını yapılandırın:
    
   Sistem çoğaltmasını başlatmak için ikinci düğümü kaydedin. < Hanasıd\>adm olarak aşağıdaki komutu çalıştırın:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1,0 sistem çoğaltmasını yapılandırma

Bu bölümdeki adımlarda aşağıdaki ön ekler kullanılır:

* **[A]** : adım tüm düğümler için geçerlidir.
* **[1]** : adım yalnızca düğüm 1 ' e uygulanır.
* **[2]** : adım yalnızca Paceoluşturucu kümesinin node 2 ' de geçerlidir.

1. **[1]** gerekli kullanıcıları oluşturun.

   Aşağıdaki komutu kök olarak çalıştırın. Kalın dizeleri (HANA sistem KIMLIĞI **HN1** ve örnek numarası **03**) SAP HANA yüklemenizin değerleriyle değiştirdiğinizden emin olun:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** anahtar deposu girişi oluşturun.

   Yeni bir anahtar deposu girişi oluşturmak için aşağıdaki komutu kök olarak çalıştırın:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** veritabanını yedekleyin.

   Veritabanlarını kök olarak yedekleme:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Çok kiracılı bir yükleme kullanıyorsanız, kiracı veritabanını da yedekleyin:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Ilk düğümde sistem çoğaltmasını yapılandırın.

   Birincil siteyi < hanasıd\>adm olarak oluşturun:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Ikincil düğümde sistem çoğaltmasını yapılandırın.

   İkincil siteyi < hanasıd\>adm olarak Kaydet:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA kümesi kaynakları oluşturma

İlk olarak, HANA topolojisini oluşturun. Aşağıdaki komutları Paceyapıcısı küme düğümlerinden birinde çalıştırın:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Ardından, HANA kaynaklarını oluşturun:

> [!IMPORTANT]
> En son test, Netcat 'in biriktirme listesi ve yalnızca bir bağlantıyı işleme sınırlaması nedeniyle isteklere yanıt vermeyi durdurduğu ortaya çıkarılan durumlardır. Netcat kaynağı Azure yük dengeleyici isteklerini dinlemeyi durduruyor ve kayan IP kullanılamaz hale gelir.  
> Mevcut Paceüreticisi kümeleri için, [Azure yük dengeleyici algılama sağlamlaştırma](https://www.suse.com/support/kb/doc/?id=7024128)içindeki yönergeleri izleyerek netcat 'i socat ile değiştirmeyi öneririz. Değişikliğin kısa kapalı kalma süresinin gerekli olacağını unutmayın.  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" is-managed="true" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
  params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:625<b>03</b>,backlog=10,fork,reuseaddr /dev/null" \
  op monitor timeout=20s interval=10 depth=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Küme kurulumunu test etme

Bu bölüm, kurulumunuzu nasıl test kullanabileceğinizi açıklar. Her test, kök olduğunuzu ve SAP HANA yöneticisinin **hn1-DB-0** sanal makinesinde çalıştığını varsayar.

### <a name="test-the-migration"></a>Geçişi test etme

Teste başlamadan önce, pacemaker 'ın başarısız bir eylem (crm_mon-r aracılığıyla) olmadığından emin olun, beklenmeyen bir konum kısıtlaması olmadığından (örneğin, bir geçiş testinin kalan kısmını) ve HANA 'nın eşitleme durumu olduğundan (örneğin SAPHanaSR-showAttr) emin olun:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Aşağıdaki komutu yürüterek SAP HANA ana düğümünü geçirebilirsiniz:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

`AUTOMATED_REGISTER="false"`ayarlarsanız, bu komut dizisi SAP HANA ana düğümünü ve sanal IP adresini içeren grubu hn1-DB-1 ' e geçirmelidir.

Geçiş yapıldıktan sonra, crm_mon-r çıktısı şuna benzer

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

Hn1-DB-0 üzerindeki SAP HANA kaynak ikincil olarak başlayamaz. Bu durumda, aşağıdaki komutu yürüterek HANA örneğini ikincil olarak yapılandırın:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Geçiş, yeniden silinmesi gereken konum kısıtlamalarını oluşturur:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

İkincil düğüm kaynağının durumunu da temizlemeniz gerekir:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Crm_mon-r kullanarak HANA kaynağının durumunu izleyin. Hn1-DB-0 ' da HANA başlatıldıktan sonra çıktı şöyle görünmelidir

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Azure ile sınırlama aracısını test etme (SBD değil)

Hn1-DB-0 düğümündeki ağ arabirimini devre dışı bırakarak Azure uçulama aracısının kurulumunu test edebilirsiniz:

<pre><code>sudo ifdown eth0
</code></pre>

Sanal makinenin artık küme yapılandırmanıza bağlı olarak yeniden başlatılması veya durdurulması gerekir.
`stonith-action` ayarını Kapalı olarak ayarlarsanız, sanal makine durdurulur ve kaynaklar çalışan sanal makineye geçirilir.

Sanal makineyi yeniden başlattıktan sonra, `AUTOMATED_REGISTER="false"`ayarlarsanız SAP HANA kaynak ikincil olarak başlayamaz. Bu durumda, aşağıdaki komutu yürüterek HANA örneğini ikincil olarak yapılandırın:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Test SBD sınırlama

İnquisitor işlemini sonlandırarak SBD kurulumunu test edebilirsiniz.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Hn1-DB-0 küme düğümü yeniden başlatılmalıdır. Paceoluşturucu hizmeti daha sonra başlamamayabilir. Yeniden başlattığınızdan emin olun.

### <a name="test-a-manual-failover"></a>El ile yük devretmeyi test etme

Hn1-DB-0 düğümündeki `pacemaker` hizmetini durdurarak el ile yük devretmeyi test edebilirsiniz:

<pre><code>service pacemaker stop
</code></pre>

Yük devretmeden sonra hizmeti yeniden başlatabilirsiniz. `AUTOMATED_REGISTER="false"`ayarlarsanız, hn1-DB-0 düğümündeki SAP HANA kaynak ikincil olarak başlayamaz. Bu durumda, aşağıdaki komutu yürüterek HANA örneğini ikincil olarak yapılandırın:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE testleri

> [!IMPORTANT]
> Seçtiğiniz işletim sisteminin, kullanmakta olduğunuz belirli VM türlerinde SAP HANA için SAP sertifikalı olduğundan emin olun. Bu kişiler için SAP HANA sertifikalı VM türlerinin ve işletim sistemi sürümlerinin listesi, [SAP HANA sertifikalı IaaS platformlarında](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)aranabilir. Belirli bir sanal makine türü için desteklenen SAP HANA işletim sistemi sürümlerinin tüm listesini almak üzere listelenen VM türünün ayrıntılarına tıkladığınızdan emin olun

Kullanım durumunuza bağlı olarak SAP HANA SR performansı için Iyileştirilmiş senaryoda veya SAP HANA SR maliyeti Iyileştirilmiş senaryo kılavuzunda listelenen tüm test çalışmalarını çalıştırın. Kılavuzlar, [SLES for SAP en iyi uygulamalar sayfasında][sles-for-sap-bp]bulunabilir.

Aşağıdaki testler, SAP Applications 12 SP1 Kılavuzu için SAP HANA SR performansı için Iyileştirilmiş senaryo SUSE Linux Enterprise Server test açıklamalarının bir kopyasıdır. Güncel bir sürüm için her zaman kılavuzun kendisini de okuyun. Sınamayı başlatmadan önce HANA 'nın eşitlenmiş olduğundan emin olun ve ayrıca Paceyapıcısı yapılandırmasının doğru olduğundan emin olun.

Aşağıdaki test açıklamalarında PREFER_SITE_TAKEOVER = "true" ve AUTOMATED_REGISTER = "false" varsayıyoruz.
NOTE: aşağıdaki testler sırayla çalıştırılmak üzere tasarlanmıştır ve önceki testlerin çıkış durumuna bağlıdır.

1. TEST 1: DÜĞÜM 1 ' DE BIRINCIL VERITABANıNı DURDUR

   Teste başlamadan önce kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Hn1-DB-0 düğümünde < hanasid\>adm olarak aşağıdaki komutları çalıştırın:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker durdurulmuş HANA örneğini ve diğer düğüme yük devretmeyi algılamalıdır. Yük devretme işlemi tamamlandıktan sonra, hn1-DB-0 düğümündeki HANA örneği durdurulur çünkü pacemaker düğümü HANA ikincil olarak otomatik olarak kaydetmez.

   Node hn1-DB-0 ' i ikincil olarak kaydetmek ve başarısız kaynağı temizlemek için aşağıdaki komutları çalıştırın.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 2: DÜĞÜM 2 ' DE BIRINCIL VERITABANıNı DURDUR

   Teste başlamadan önce kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Hn1-DB-1 düğümünde < hanasid\>adm olarak aşağıdaki komutları çalıştırın:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker durdurulmuş HANA örneğini ve diğer düğüme yük devretmeyi algılamalıdır. Yük devretme işlemi tamamlandıktan sonra, hn1-DB-1 düğümündeki HANA örneği durdurulur çünkü pacemaker düğümü HANA ikincil olarak otomatik olarak kaydetmez.

   Node hn1-DB-1 ' i ikincil olarak kaydetmek ve başarısız kaynağı temizlemek için aşağıdaki komutları çalıştırın.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 3: DÜĞÜM ÜZERINDE KILITLENME BIRINCIL VERITABANı

   Teste başlamadan önce kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Hn1-DB-0 düğümünde < hanasid\>adm olarak aşağıdaki komutları çalıştırın:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Paceyapıcısı, sonlandırılan HANA örneğini ve diğer düğüme yük devretmeyi algılamamalıdır. Yük devretme işlemi tamamlandıktan sonra, hn1-DB-0 düğümündeki HANA örneği durdurulur çünkü pacemaker düğümü HANA ikincil olarak otomatik olarak kaydetmez.

   Node hn1-DB-0 ' i ikincil olarak kaydetmek ve başarısız kaynağı temizlemek için aşağıdaki komutları çalıştırın.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 4: DÜĞÜM 2 ÜZERINDE KILITLENME BIRINCIL VERITABANı

   Teste başlamadan önce kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Hn1-DB-1 düğümünde < hanasid\>adm olarak aşağıdaki komutları çalıştırın:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Paceyapıcısı, sonlandırılan HANA örneğini ve diğer düğüme yük devretmeyi algılamamalıdır. Yük devretme işlemi tamamlandıktan sonra, hn1-DB-1 düğümündeki HANA örneği durdurulur çünkü pacemaker düğümü HANA ikincil olarak otomatik olarak kaydetmez.

   Node hn1-DB-1 ' i ikincil olarak kaydetmek ve başarısız kaynağı temizlemek için aşağıdaki komutları çalıştırın.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 5: KILITLENME BIRINCIL SITE DÜĞÜMÜ (DÜĞÜM 1)

   Teste başlamadan önce kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Hn1-DB-0 düğümünde kök olarak aşağıdaki komutları çalıştırın:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Paceyapıcısı, sonlandırılan küme düğümünü ve düğümü dilimini algılamamalıdır. Düğüm doğrulandıktan sonra Paceyapıcısı, HANA örneğinin bir listesini tetikleyecektir. Fbaşvurunun düğümü yeniden başlatıldığında pacemaker otomatik olarak başlatılmaz.

   Pacemaker ' ı başlatmak için aşağıdaki komutları çalıştırın, hn1-DB-0 düğümünün SBD iletilerini temizleyin, Node hn1-DB-0 ' ı ikincil olarak kaydedin ve başarısız kaynağı temizleyin.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 6: KILITLENME IKINCIL SITE DÜĞÜMÜ (DÜĞÜM 2)

   Teste başlamadan önce kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Aşağıdaki komutları hn1-DB-1 düğümünde kök olarak çalıştırın:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Paceyapıcısı, sonlandırılan küme düğümünü ve düğümü dilimini algılamamalıdır. Düğüm doğrulandıktan sonra Paceyapıcısı, HANA örneğinin bir listesini tetikleyecektir. Fbaşvurunun düğümü yeniden başlatıldığında pacemaker otomatik olarak başlatılmaz.

   Pacemaker ' ı başlatmak için aşağıdaki komutları çalıştırın, hn1-DB-1 düğümü için SBD iletilerini temizleyin, Node hn1-DB-1 ' i ikincil olarak kaydedin ve başarısız kaynağı temizleyin.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 7: DÜĞÜM 2 ' DE IKINCIL VERITABANıNı DURDUR

   Teste başlamadan önce kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Hn1-DB-1 düğümünde < hanasid\>adm olarak aşağıdaki komutları çalıştırın:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Paceyapıcısı durdurulmuş HANA örneğini algılayacak ve hn1-DB-1 düğümünde kaynağı başarısız olarak işaretleyecek. Pacemaker, HANA örneğini otomatik olarak yeniden başlatmalıdır. Başarısız durumu temizlemek için aşağıdaki komutu çalıştırın.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 8: DÜĞÜM 2 ' DE IKINCIL VERITABANıNı KILITLENME

   Teste başlamadan önce kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Hn1-DB-1 düğümünde < hanasid\>adm olarak aşağıdaki komutları çalıştırın:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Paceyapıcısı, sonlandırılan HANA örneğini algılayacak ve hn1-DB-1 düğümünde kaynağı başarısız olarak işaretleyecek. Başarısız durumu temizlemek için aşağıdaki komutu çalıştırın. Paceyapıcısı daha sonra HANA örneğini otomatik olarak yeniden başlatmalıdır.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 9: IKINCIL HANA VERITABANıNı ÇALıŞTıRAN KILITLENME IKINCIL SITE DÜĞÜMÜ (DÜĞÜM 2)

   Teste başlamadan önce kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Aşağıdaki komutları hn1-DB-1 düğümünde kök olarak çalıştırın:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Paceyapıcısı, sonlandırılan küme düğümünü ve düğümü dilimini algılamamalıdır. Fbaşvurunun düğümü yeniden başlatıldığında pacemaker otomatik olarak başlatılmaz.

   Pacemaker ' ı başlatmak için aşağıdaki komutları çalıştırın, hn1-DB-1 düğümü için SBD iletilerini temizleyin ve başarısız kaynağı temizleyin.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* Azure 'da SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan (büyük örnekler) oluşturma hakkında bilgi edinmek için bkz. [Azure 'da SAP HANA (büyük örnekler) yüksek kullanılabilirlik ve olağanüstü durum kurtarma](hana-overview-high-availability-disaster-recovery.md)
