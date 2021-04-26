---
title: RHEL üzerinde Azure VM 'lerinde SAP HANA yüksek kullanılabilirlik | Microsoft Docs
description: Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirlik sağlayın.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 3d1b05560c02f3bf4de199a3d5cad48907ee16fb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365816"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux üzerinde Azure VM 'lerinde SAP HANA yüksek kullanılabilirliği

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Şirket içi geliştirme için, her iki HANA sistem çoğaltmasını kullanabilir veya SAP HANA için yüksek kullanılabilirlik sağlamak üzere paylaşılan depolama alanını kullanabilirsiniz.
Azure sanal makinelerinde (VM), Azure 'da HANA sistem çoğaltması Şu anda desteklenen tek yüksek kullanılabilirlik işlevidir.
SAP HANA çoğaltma bir birincil düğümden ve en az bir ikincil düğümden oluşur. Birincil düğümdeki verilerde yapılan değişiklikler ikincil düğüme zaman uyumlu veya zaman uyumsuz olarak çoğaltılır.

Bu makalede, sanal makinelerin nasıl dağıtılacağı ve yapılandırılacağı, küme çerçevesinin nasıl yükleneceği ve SAP HANA sistem çoğaltmasının nasıl yükleneceği ve yapılandırılacağı açıklanmaktadır.
Örnek yapılandırmalarda, yükleme komutları, örnek numarası **03** ve Hana sistem kimliği **HN1** kullanılır.

Önce aşağıdaki SAP notlarını ve kağıtları okuyun:

* SAP Note [1928533], şunları içerir:
  * SAP yazılımının dağıtımı için desteklenen Azure VM boyutlarının listesi.
  * Azure VM boyutları için önemli kapasite bilgileri.
  * Desteklenen SAP yazılımı ve işletim sistemi (OS) ve veritabanı birleşimleri.
  * Microsoft Azure üzerinde Windows ve Linux için gereken SAP çekirdek sürümü.
* SAP Note [2015553] , Azure 'da SAP tarafından desteklenen SAP yazılım dağıtımları için önkoşulları listeler.
* SAP Note [2002167] Red Hat Enterprise Linux için önerilen işletim sistemi ayarlarına sahiptir
* SAP Note [2009879] , Red Hat Enterprise Linux Için SAP HANA yönergelerine sahiptir
* SAP Note [2178632] , Azure 'da SAP için raporlanan tüm izleme ölçümleriyle ilgili ayrıntılı bilgiler içerir.
* SAP Note [2191498] , Azure 'da Linux IÇIN gereken SAP konak Aracısı sürümüne sahiptir.
* SAP Note [2243692] , Azure 'da LINUX üzerinde SAP lisanslama hakkında bilgi içerir.
* SAP Note [1999351] , SAP Için Azure Gelişmiş izleme uzantısı için ek sorun giderme bilgilerine sahiptir.
* [SAP COMMUNITY WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 'nin Linux için gereklı tüm sap notları vardır.
* [Linux 'ta SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [Linux 'ta SAP için Azure sanal makineleri dağıtımı (Bu makale)][deployment-guide]
* [Linux üzerinde SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [Pacemaker kümesinde sistem çoğaltmasını SAP HANA](https://access.redhat.com/articles/3004101)
* Genel RHEL belgeleri
  * [Yüksek kullanılabilirlik Add-On genel bakış](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Yüksek kullanılabilirlik Add-On yönetimi](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Yüksek kullanılabilirlik Add-On başvurusu](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure 'a özgü RHEL belgeleri:
  * [RHEL yüksek kullanılabilirlik kümeleri için destek Ilkeleri-küme üyesi olarak Microsoft Azure Sanal Makineler](https://access.redhat.com/articles/3131341)
  * [Microsoft Azure Red Hat Enterprise Linux 7,4 (ve üzeri) High-Availability kümesini yükleme ve yapılandırma](https://access.redhat.com/articles/3252491)
  * [Microsoft Azure kullanım için Red Hat Enterprise Linux SAP HANA yüklemesi](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Genel Bakış

Yüksek kullanılabilirlik elde etmek için SAP HANA iki sanal makineye yüklenir. Veriler, HANA sistem çoğaltması kullanılarak çoğaltılır.

![SAP HANA yüksek kullanılabilirliğe genel bakış](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA sistem çoğaltma Kurulumu, ayrılmış bir sanal konak adı ve sanal IP adresleri kullanır. Azure 'da bir sanal IP adresi kullanmak için bir yük dengeleyici gereklidir. Aşağıdaki listede yük dengeleyicinin yapılandırması gösterilmektedir:

* Ön uç yapılandırması: hn1-DB için IP adresi 10.0.0.13
* Arka uç yapılandırması: HANA sistem çoğaltmasının parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
* Araştırma bağlantı noktası: bağlantı noktası 62503
* Yük Dengeleme kuralları: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Linux için dağıtma

Azure Marketi, yeni sanal makineler dağıtmak için kullanabileceğiniz SAP HANA için Red Hat Enterprise Linux 7,4 için bir görüntü içerir.

### <a name="deploy-with-a-template"></a>Şablonla dağıtma

GitHub üzerinde olan hızlı başlangıç şablonlarından birini, gerekli tüm kaynakları dağıtmak için kullanabilirsiniz. Şablon, sanal makineleri, yük dengeleyiciyi, kullanılabilirlik kümesini ve benzerlerini dağıtır.
Şablonu dağıtmak için aşağıdaki adımları izleyin:

1. Azure portal [veritabanı şablonunu][template-multisid-db] açın.
1. Aşağıdaki parametreleri girin:
    * **SAP SISTEM kimliği**: yüklemek istediğiniz SAP sisteminin SAP sistem kimliğini girin. KIMLIK, dağıtılan kaynakların ön eki olarak kullanılır.
    * **Işletim sistemi türü**: Linux dağılımından birini seçin. Bu örnek için **RHEL 7**' yi seçin.
    * **Veritabanı türü**: **Hana** seçin.
    * **SAP sistem boyutu**: yeni sistemin SAĞLAYACAĞı SAPS sayısını girin. Sistemin kaç tane için gerekli olduğundan emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun.
    * **Sistem kullanılabilirliği**: **ha** seçeneğini belirleyin.
    * **Yönetici Kullanıcı adı, yönetici parolası veya SSH anahtarı**: makinede oturum açmak için kullanılabilecek yeni bir Kullanıcı oluşturulur.
    * **Alt ağ kimliği**: VM 'yi tanımlanmış bir alt ağa sahip olduğunuz mevcut bir VNET 'e dağıtmak istiyorsanız, söz konusu alt ağın kimliğini adlandırın. KIMLIK genellikle **/Subscriptions/ \<subscription ID> /ResourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /Subnets/ \<subnet name>** şeklinde görünür. Yeni bir sanal ağ oluşturmak istiyorsanız boş bırakın

### <a name="manual-deployment"></a>El ile dağıtım

1. Bir kaynak grubu oluşturun.
1. Sanal ağ oluşturun.
1. Bir kullanılabilirlik kümesi oluşturun.  
   En fazla güncelleştirme etki alanını ayarlayın.
1. Yük Dengeleyici (iç) oluşturun. [Standart yük dengeleyiciyi](../../../load-balancer/load-balancer-overview.md)öneririz.
   * 2. adımda oluşturulan sanal ağı seçin.
1. Sanal makine oluşturun 1.  
   SAP HANA için en az Red Hat Enterprise Linux 7,4 kullanın. Bu örnek, <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Adım 3 ' te oluşturulan kullanılabilirlik kümesini seçmek SAP HANA görüntüsü için Red Hat Enterprise Linux 7,4 kullanır.
1. Sanal makine oluştur 2.  
   SAP HANA için en az Red Hat Enterprise Linux 7,4 kullanın. Bu örnek, <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Adım 3 ' te oluşturulan kullanılabilirlik kümesini seçmek SAP HANA görüntüsü için Red Hat Enterprise Linux 7,4 kullanır.
1. Veri diskleri ekleyin.

> [!IMPORTANT]
> Kayan IP, Yük Dengeleme senaryolarında NIC ikincil IP yapılandırmasında desteklenmez. Ayrıntılar için bkz. [Azure yük dengeleyici sınırlamaları](../../../load-balancer/load-balancer-multivip-overview.md#limitations). VM için ek IP adresine ihtiyacınız varsa ikinci bir NIC dağıtın.    

> [!Note]
> Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyicisine yerleştirildiğinde, genel uç noktalara yönlendirmeye izin vermek için ek yapılandırma gerçekleştirilmediği takdirde giden internet bağlantısı olmaz. Giden bağlantıyı elde etme hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer kullanan sanal makineler Için genel uç nokta BAĞLANTıSı SAP yüksek kullanılabilirlik senaryolarında](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

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
      1. **Sanal makine Ekle**' yi seçin.
      1. * * Sanal makine * * öğesini seçin.
      1. SAP HANA kümesinin sanal makinelerini ve IP adreslerini seçin.
      1. **Add (Ekle)** seçeneğini belirleyin.

   1. Sonra, bir sistem durumu araştırması oluşturun:

      1. Yük dengeleyiciyi açın, **sistem durumu araştırmaları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni sistem durumu araştırmasının adını girin (örneğin, **Hana-HP**).
      1. Protokol ve bağlantı noktası 625 **03** olarak **TCP** ' yi seçin. **Aralık** değerini 5 olarak ve **sağlıksız eşik** değerini 2 olarak ayarlayın.
      1. **Tamam**’ı seçin.

   1. Sonra, Yük Dengeleme kurallarını oluşturun:
   
      1. Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni yük dengeleyici kuralının adını girin (örneğin, **Hana-lb**).
      1. Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **Hana-ön uç**, **Hana-arka uç** ve **Hana-HP**) seçin.
      1. **Ha bağlantı noktalarını** seçin.
      1. **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
      1. **Kayan IP**'yi etkinleştirdiğinizden emin olun.
      1. **Tamam**’ı seçin.


1. Alternatif olarak, senaryonuz temel yük dengeleyiciyi kullanmayı belirlemesi durumunda aşağıdaki yapılandırma adımlarını izleyin:
   1. Yük dengeleyiciyi yapılandırın. İlk olarak, bir ön uç IP havuzu oluşturun:

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
      1. Protokol ve bağlantı noktası 625 **03** olarak **TCP** ' yi seçin. **Aralık** değerini 5 olarak ve **sağlıksız eşik** değerini 2 olarak ayarlayın.
      1. **Tamam**’ı seçin.

   1. SAP HANA 1,0 için, Yük Dengeleme kurallarını oluşturun:

      1. Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni yük dengeleyici kuralının adını girin (örneğin, Hana-lb-3 **03** 15).
      1. Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **Hana-ön uç**) seçin.
      1. **Protokolü** **TCP** olarak ayarlayın ve bağlantı noktası 3 **03** 15 girin.
      1. **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
      1. **Kayan IP**'yi etkinleştirdiğinizden emin olun.
      1. **Tamam**’ı seçin.
      1. Bağlantı noktası 3 **03** 17 için bu adımları tekrarlayın.

   1. SAP HANA 2,0 için, sistem veritabanı için Yük Dengeleme kurallarını oluşturun:

      1. Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni yük dengeleyici kuralının adını girin (örneğin, Hana-lb-3 **03** 13).
      1. Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **Hana-ön uç**) seçin.
      1. **Protokolü** **TCP** olarak ayarlayın ve bağlantı noktası 3 **03** 13 yazın.
      1. **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
      1. **Kayan IP**'yi etkinleştirdiğinizden emin olun.
      1. **Tamam**’ı seçin.
      1. Bağlantı noktası 3 **03** 14 için bu adımları tekrarlayın.

   1. SAP HANA 2,0 için, önce Kiracı veritabanı için Yük Dengeleme kurallarını oluşturun:

      1. Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
      1. Yeni yük dengeleyici kuralının adını girin (örneğin, Hana-lb-3 **03** 40).
      1. Daha önce oluşturduğunuz ön uç IP adresini, arka uç havuzunu ve sistem durumu araştırmasını seçin (örneğin, **Hana-ön uç**).
      1. **Protokolü** **TCP** olarak ayarlayın ve bağlantı noktası 3 **03** 40 yazın.
      1. **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.
      1. **Kayan IP**'yi etkinleştirdiğinizden emin olun.
      1. **Tamam**’ı seçin.
      1. Bağlantı noktaları 3 **03** 41 ve 3 **03** 42 için bu adımları yineleyin.

SAP HANA için gereken bağlantı noktaları hakkında daha fazla bilgi için, [SAP HANA kiracı veritabanları](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) Kılavuzu veya [SAP Note 2388694][2388694]' de [kiracı veritabanlarına yönelik bölüm bağlantılarını](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) okuyun.

> [!IMPORTANT]
> Azure Load Balancer arkasına yerleştirilmiş Azure VM 'lerinde TCP zaman damgalarını etkinleştirmeyin. TCP zaman damgalarını etkinleştirmek, sistem durumu araştırmalarının başarısız olmasına neden olur. Parametre **net.ipv4.tcp_timestamps** **0** olarak ayarlayın. Ayrıntılar için bkz. [Load Balancer sistem durumu araştırmaları](../../../load-balancer/load-balancer-custom-probe-overview.md).
> Ayrıca bkz. SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>SAP HANA yükleme

Bu bölümdeki adımlarda aşağıdaki ön ekler kullanılır:

* **[A]**: adım tüm düğümler için geçerlidir.
* **[1]**: adım yalnızca düğüm 1 ' e uygulanır.
* **[2]**: adım yalnızca Paceoluşturucu kümesinin node 2 ' de geçerlidir.

1. **[A]** disk düzeni ayarlama: **mantıksal birim Yöneticisi (LVM)**.

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

   Mantıksal birimleri oluşturun. Anahtar olmadan kullandığınızda doğrusal bir birim oluşturulur `lvcreate` `-i` . Daha iyi g/ç performansı için şeritli bir birim oluşturmanızı ve şeritli boyutları [SAP HANA VM depolama yapılandırmalarında](./hana-vm-operations-storage.md)belgelenen değerlere hizalamanızı öneririz. `-i`Bağımsız değişken, temeldeki fiziksel birimlerin sayısı olmalıdır ve `-I` bağımsız değişken Stripe boyutudur. Bu belgede, veri hacmi için iki fiziksel birim kullanılır, bu nedenle `-i` anahtar bağımsız değişkeni **2** olarak ayarlanır. Veri hacmi için Şerit boyutu **256Kıb**'dir. Günlük birimi için bir fiziksel birim kullanılır, bu nedenle `-i` `-I` günlük birimi komutları için hiçbir veya anahtar açık olarak kullanılmaz.  

   > [!IMPORTANT]
   > `-i`Her bir veri, günlük veya paylaşılan birim için birden fazla fiziksel birim kullandığınızda anahtarı kullanın ve temel alınan fiziksel birimin numarasını ayarlayın. `-I`Şeritli birim oluştururken Stripe boyutunu belirtmek için anahtarını kullanın.  
   > Bkz. şerit boyutları ve disk sayısı dahil olmak üzere önerilen depolama yapılandırmalarının [SAP HANA VM depolama yapılandırması](./hana-vm-operations-storage.md) .  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
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

   `fstab`Üç mantıksal birim için girdi oluşturun:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Aşağıdaki satırı `/etc/fstab` dosyasına ekleyin:

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

1. **[A]** Hana yapılandırması için RHEL

   Aşağıdaki SAP notlarında ve ' de açıklandığı gibi RHEL 'yi yapılandırın <https://access.redhat.com/solutions/2447641> :  
   - [2292690-SAP HANA DB: RHEL 7 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: RHEL 8 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: GCC 6. x ile derlenen SAP uygulamaları çalıştırma](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: GCC 7. x ile derlenen SAP uygulamalarını çalıştırma](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: GCC 9. x ile derlenen SAP uygulamalarını çalıştırma](https://launchpad.support.sap.com/#/notes/2886607)

1. **[A]** SAP HANA yüklemesi

   SAP HANA sistem çoğaltmasını yüklemek için izleyin <https://access.redhat.com/articles/3004101> .

   * HANA DVD 'sindeki **hdblcm** programını çalıştırın. Komut istemine aşağıdaki değerleri girin:
   * Yükleme seç: **1** yazın.
   * Yükleme için ek bileşenler seçin: **1** girin.
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

1. **[A]** güvenlik duvarını yapılandırma

   Azure yük dengeleyici araştırma bağlantı noktası için güvenlik duvarı kuralı oluşturun.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2,0 sistem çoğaltmasını yapılandırma

Bu bölümdeki adımlarda aşağıdaki ön ekler kullanılır:

* **[A]**: adım tüm düğümler için geçerlidir.
* **[1]**: adım yalnızca düğüm 1 ' e uygulanır.
* **[2]**: adım yalnızca Paceoluşturucu kümesinin node 2 ' de geçerlidir.

1. **[A]** güvenlik duvarını yapılandırma

   HANA sistem çoğaltmasına ve istemci trafiğine izin vermek için güvenlik duvarı kuralları oluşturun. Gerekli bağlantı noktaları, [Tüm sap ürünlerinin TCP/IP bağlantı noktalarında](https://help.sap.com/viewer/ports)listelenir. Aşağıdaki komutlar, HANA 2,0 sistem çoğaltmasının ve istemci trafiğinin SYSTEMDB, HN1 ve NW1 veritabanına erişmesine izin veren bir örnektir.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** kiracı veritabanını oluşturun.

   SAP HANA 2,0 veya MDC kullanıyorsanız, SAP NetWeaver sisteminiz için bir kiracı veritabanı oluşturun. **NW1** değerini SAP sisteminizin SID 'si ile değiştirin.

   Farklı Çalıştır <hanasıd \> ADM aşağıdaki komutu:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Ilk düğümde sistem çoğaltmasını yapılandırın:

   Veritabanlarını <hanasıd adm olarak yedekleyin \> :

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
    
   Sistem çoğaltmasını başlatmak için ikinci düğümü kaydedin. <hanasid adm olarak aşağıdaki komutu çalıştırın \> :

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** çoğaltma durumunu denetleme

   Çoğaltma durumunu denetleyin ve tüm veritabanları eşitlenene kadar bekleyin. Durum bılınmıyor olarak kalırsa güvenlik duvarı ayarlarınızı denetleyin.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1,0 sistem çoğaltmasını yapılandırma

Bu bölümdeki adımlarda aşağıdaki ön ekler kullanılır:

* **[A]**: adım tüm düğümler için geçerlidir.
* **[1]**: adım yalnızca düğüm 1 ' e uygulanır.
* **[2]**: adım yalnızca Paceoluşturucu kümesinin node 2 ' de geçerlidir.

1. **[A]** güvenlik duvarını yapılandırma

   HANA sistem çoğaltmasına ve istemci trafiğine izin vermek için güvenlik duvarı kuralları oluşturun. Gerekli bağlantı noktaları, [Tüm sap ürünlerinin TCP/IP bağlantı noktalarında](https://help.sap.com/viewer/ports)listelenir. Aşağıdaki komutlar, HANA 2,0 sistem çoğaltmasına izin veren yalnızca bir örnektir. SAP HANA 1,0 yüklemenize uyarlayabilirsiniz.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   Birincil siteyi <hanasıd adm olarak oluşturun \> :

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Ikincil düğümde sistem çoğaltmasını yapılandırın.

   İkincil siteyi <hanasıd adm olarak Kaydet \> :

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Paceoluşturucu kümesi oluşturma

Bu HANA sunucusu için temel bir Paceoluşturucu kümesi oluşturmak üzere [Azure 'daki Red Hat Enterprise Linux Paceyapıcısı ayarlama](high-availability-guide-rhel-pacemaker.md) bölümündeki adımları izleyin.

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>Python sistem çoğaltma kancasını uygulama

Bu, kümeyle tümleştirmeyi iyileştirmek ve bir küme yük devretmesi gerektiğinde algılamayı iyileştirmek için önemli bir adımdır. SAPHanaSR Python kancasını yapılandırmak kesinlikle önerilir.    

1. **[A]** Hana "sistem çoğaltma kancasını" yükler. Kanca 'nin hem HANA DB düğümlerine yüklenmesi gerekir.           

   > [!TIP]
   > Python kancası yalnızca HANA 2,0 için uygulanabilir.        

   1. Kancasını olarak hazırlayın `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Her iki düğümde de HANA 'yı durdurun. <SID adm olarak Çalıştır \> :  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. `global.ini`Her küme düğümünde ayarlayın.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** küme, <SID adm için her küme düğümünde susers yapılandırması gerektirir \> . Bu örnekte, yeni bir dosya oluşturularak elde edilen. Komutları olarak yürütün `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```

3. **[A]** her iki düğümde de SAP HANA başlatın. <SID adm olarak yürütün \> .  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** kanca yüklemesini doğrulayın. \>ETKIN Hana sistem çoğaltma sitesinde <SID adm olarak yürütün.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

SAP HANA sistem çoğaltma kancasını uygulamayla ilgili daha fazla bilgi için bkz. [SAP ha/Dr sağlayıcı kancasını etkinleştirme](https://access.redhat.com/articles/3004101#enable-srhook).  
 
## <a name="create-sap-hana-cluster-resources"></a>SAP HANA kümesi kaynakları oluşturma

SAP HANA kaynak aracılarını **tüm düğümlere** yükler. Paketi içeren bir depoyu etkinleştirdiğinizden emin olun. RHEL 8. x HA özellikli görüntü kullanılıyorsa ek depoları etkinleştirmeniz gerekmez.  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Ardından, HANA topolojisini oluşturun. Aşağıdaki komutları Paceyapıcısı küme düğümlerinden birinde çalıştırın:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Ardından, HANA kaynaklarını oluşturun.

> [!NOTE]
> Bu makale, Microsoft 'un artık kullandığı bir terim olan *bağımlı* dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.

**RHEL 7. x** üzerinde bir küme oluşturuyorsanız aşağıdaki komutları kullanın:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

**RHEL 8. x** üzerinde bir küme oluşturuyorsanız aşağıdaki komutları kullanın:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
promotable meta notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-clone symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-clone 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

> [!NOTE]
> Yukarıdaki yapılandırmadaki zaman aşımları yalnızca örnektir ve belirli HANA kurulumuna uyarlanmasını gerektirebilir. Örneğin, SAP HANA veritabanını başlatmak daha uzun sürerse Başlangıç zaman aşımını artırmanız gerekebilir.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>


## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Pacemaker kümesinde HANA etkin/Read özellikli sistem çoğaltmasını yapılandırma

SAP HANA 2,0 ' den başlayarak, SAP HANA sistem çoğaltması için etkin/okuma etkin kurulum yapılmasına izin verir; burada, SAP HANA sistem çoğaltmasının ikincil sistemleri, okuma açısından yoğun iş yükleri için etkin bir şekilde kullanılabilir. Bir küme içinde bu tür kurulumu desteklemek için, istemcilerin ikincil okuma özellikli SAP HANA veritabanına erişmesini sağlayan ikinci bir sanal IP adresi gereklidir. Yük devralındıktan sonra ikincil çoğaltma sitesine hala erişilebildiğinden emin olmak için, kümenin sanal IP adresini SAPHana kaynağının ikincili ile taşıması gerekir.

Bu bölümde, ikinci sanal IP 'ye sahip Red Hat yüksek kullanılabilirlik kümesinde HANA etkin/Read özellikli sistem çoğaltmasını yönetmek için gereken ek adımlar açıklanmaktadır.    

Devam etmeden önce, belgenin yukarıdaki segmentlerinde anlatıldığı gibi SAP HANA veritabanını yöneten Red Hat yüksek kullanılabilirlik kümesini tam olarak yapılandırdığınızdan emin olun.  

![Okuma etkin ikincil ile yüksek kullanılabilirlik SAP HANA](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Etkin/Read özellikli kurulum için Azure Yük dengeleyicisinde ek kurulum

İkinci sanal IP sağlamaya yönelik ek adımlara devam etmek için Azure Load Balancer [El Ile dağıtım](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#manual-deployment) bölümünde açıklanan şekilde yapılandırdığınızdan emin olun.

1. **Standart** yük dengeleyici için, önceki bölümde oluşturduğunuz yük dengeleyicideki diğer ek adımları izleyin.

   a. İkinci bir ön uç IP havuzu oluşturun: 

   - Yük dengeleyiciyi açın, **ön uç IP havuzu**' nu seçin ve **Ekle**' yi seçin.
   - İkinci ön uç IP havuzunun adını girin (örneğin, **Hana-secondaryıp**).
   - **Atamayı** **statik** olarak ayarlayın ve IP adresini (örneğin, **10.0.0.14**) girin.
   - **Tamam**’ı seçin.
   - Yeni ön uç IP havuzu oluşturulduktan sonra, havuzun IP adresini aklınızda edin.

   b. Sonra, bir sistem durumu araştırması oluşturun:

   - Yük dengeleyiciyi açın, **sistem durumu araştırmaları**' nı seçin ve **Ekle**' yi seçin.
   - Yeni sistem durumu araştırmasının adını girin (örneğin, **Hana-secondaryhp**).
   - Protokol ve bağlantı noktası **62603** olarak **TCP** ' yi seçin. **Aralık** değerini 5 olarak ve **sağlıksız eşik** değerini 2 olarak ayarlayın.
   - **Tamam**’ı seçin.

   c. Sonra, Yük Dengeleme kurallarını oluşturun:

   - Yük dengeleyiciyi açın, **Yük Dengeleme kuralları**' nı seçin ve **Ekle**' yi seçin.
   - Yeni yük dengeleyici kuralının adını girin (örneğin, **Hana-secondarylb**).
   - Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **Hana-Secondaryıp**, **Hana-arka uç** ve **Hana-secondaryhp**) seçin.
   - **Ha bağlantı noktalarını** seçin.
   - **Kayan IP**'yi etkinleştirdiğinizden emin olun.
   - **Tamam**’ı seçin.

### <a name="configure-hana-activeread-enabled-system-replication"></a>HANA etkin/Read özellikli sistem çoğaltmasını yapılandırma

HANA sistem çoğaltmasını yapılandırma adımları [SAP HANA 2,0 sistem çoğaltmasını yapılandırma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-sap-hana-20-system-replication) bölümünde açıklanmaktadır. Okuma etkin ikincil senaryo dağıtıyorsanız, ikinci düğümde sistem çoğaltmasını yapılandırırken, **hanasıd** adm olarak aşağıdaki komutu yürütün:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Etkin/Read özellikli bir kurulum için ikincil sanal IP adresi kaynağı ekleme

İkinci sanal IP ve uygun birlikte bulundurma kısıtlaması aşağıdaki komutlarla yapılandırılabilir:

```
pcs property set maintenance-mode=true

pcs resource create secvip_HN1_03 ocf:heartbeat:IPaddr2 ip="10.40.0.16"

pcs resource create secnc_HN1_03 ocf:heartbeat:azure-lb port=62603

pcs resource group add g_secip_HN1_03 secnc_HN1_03 secvip_HN1_03

RHEL 8.x: 
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-clone 4000
RHEL 7.x:
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-master 4000

pcs property set maintenance-mode=false
```
Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. İkinci sanal IP, SAPHana ikincil kaynağıyla birlikte ikincil sitede çalıştırılır.

```
sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full List of Resources:
#   rsc_hdb_azr_agt     (stonith:fence_azure_arm):      Started hn1-db-0
#   Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]:
#     Started: [ hn1-db-0 hn1-db-1 ]
#   Clone Set: SAPHana_HN1_03-clone [SAPHana_HN1_03] (promotable):
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
#   Resource Group: g_ip_HN1_03:
#     nc_HN1_03         (ocf::heartbeat:azure-lb):      Started hn1-db-0
#     vip_HN1_03        (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#   Resource Group: g_secip_HN1_03:
#     secnc_HN1_03      (ocf::heartbeat:azure-lb):      Started hn1-db-1
#     secvip_HN1_03     (ocf::heartbeat:IPaddr2):       Started hn1-db-1
```

Sonraki bölümde, yürütülmesi için tipik yük devretme testi kümesini bulabilirsiniz.

İkinci sanal IP davranışının farkında olun, Read özellikli ikincil ile yapılandırılmış bir HANA kümesini test ederken:

1. **SAPHana_HN1_HDB03** küme kaynağını **HN1-DB-1**' e geçirdiğinizde ikinci sanal IP, **HN1-DB-0** diğer sunucusuna taşınır. AUTOMATED_REGISTER = "false" ve HANA sistem çoğaltması otomatik olarak kaydettirilmemişse, sunucu kullanılabilir olduğunda ve Küme Hizmetleri çevrimiçi olduğu için ikinci sanal IP **hn1-DB-0** üzerinde çalışır.  

2. Sunucu kilitlenmesinin test edilirken ikinci sanal IP kaynakları (**rsc_secip_HN1_HDB03**) ve Azure yük dengeleyici bağlantı noktası kaynağı (**rsc_secnc_HN1_HDB03**) birincil sanal IP kaynaklarıyla birlikte birincil sunucuda çalıştırılır.  İkincil sunucu devre dışı olsa da, okuma etkin HANA veritabanına bağlı olan uygulamalar, birincil HANA veritabanına bağlanır. İkincil sunucu kullanılamadığında, okuma etkin HANA veritabanına bağlı uygulamaların erişilemez olmasını istemediğiniz için davranış beklenmektedir.

3. İkincil sunucu kullanılabilir olduğunda ve Küme Hizmetleri çevrimiçi olduğunda, HANA sistem çoğaltması ikincil olarak kaydedilmemesine rağmen ikinci sanal IP ve bağlantı noktası kaynakları otomatik olarak ikincil sunucuya taşınır. Bu sunucuda küme hizmetleri 'ni başlatmak için ikincil HANA veritabanını okuma etkin olarak kaydettiğinizden emin olmanız gerekir. HANA örnek kümesi kaynağını, parametre AUTOMATED_REGISTER = true olarak ayarlayarak ikincili otomatik olarak kaydedecek şekilde yapılandırabilirsiniz.
   
4. Yük devretme ve geri dönüş sırasında, HANA veritabanına bağlanmak için ikinci sanal IP 'yi kullanan uygulamalar için mevcut bağlantılar kesintiye uğramış olabilir.  

## <a name="test-the-cluster-setup"></a>Küme kurulumunu test etme

Bu bölüm, kurulumunuzu nasıl test kullanabileceğinizi açıklar. Bir teste başlamadan önce, pacemaker 'ın başarısız bir eyleme (bilgisayarların durumu aracılığıyla) sahip olmadığından emin olun, beklenmedik bir konum kısıtlaması olmadığından (örneğin, bir geçiş testinin kalan kısmını) ve HANA 'nın eşitleme durumu olduğundan (örneğin, systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Geçişi test etme

Teste başlamadan önce kaynak durumu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Aşağıdaki komutu yürüterek SAP HANA ana düğümünü geçirebilirsiniz:

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

Ayarlarsanız `AUTOMATED_REGISTER="false"` , bu komut SAP HANA ana düğümünü ve sanal IP adresini içeren grubu hn1-DB-1 ' e geçirmelidir.

Geçiş yapıldıktan sonra, ' sudo bilgisayarları durumu ' çıkışı şöyle görünür

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Hn1-DB-0 üzerindeki SAP HANA kaynağı durduruldu. Bu durumda, aşağıdaki komutu yürüterek HANA örneğini ikincil olarak yapılandırın:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Geçiş, yeniden silinmesi gereken konum kısıtlamalarını oluşturur:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

' PC Status ' kullanarak HANA kaynağının durumunu izleyin. Hn1-DB-0 ' da HANA başlatıldıktan sonra çıktı şöyle görünmelidir

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Azure ile sınırlama aracısını test etme

> [!NOTE]
> Bu makale, Microsoft 'un artık kullandığı bir terim olan *bağımlı* dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.  

Teste başlamadan önce kaynak durumu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

SAP HANA ana öğe olarak çalıştığı düğümdeki ağ arabirimini devre dışı bırakarak Azure uçulama aracısının kurulumunu test edebilirsiniz.
Ağ hatasının benzetimini yapma hakkında bir açıklama için bkz. [Red Hat Bilgi Bankası makalesi 79523](https://access.redhat.com/solutions/79523) . Bu örnekte, ağa erişimi engellemek için net_breaker betiğini kullanırız.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Sanal makinenin artık küme yapılandırmanıza bağlı olarak yeniden başlatılması veya durdurulması gerekir.
`stonith-action`Ayarı kapalı olarak ayarlarsanız, sanal makine durdurulur ve kaynaklar çalışan sanal makineye geçirilir.

Sanal makineyi yeniden başlattıktan sonra, ayarlarsanız SAP HANA kaynak ikincil olarak başlayamaz `AUTOMATED_REGISTER="false"` . Bu durumda, aşağıdaki komutu yürüterek HANA örneğini ikincil olarak yapılandırın:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Testten sonra kaynak durumu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>El ile yük devretmeyi test etme

Teste başlamadan önce kaynak durumu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Hn1-DB-0 düğümündeki kümeyi durdurarak el ile yük devretmeyi test edebilirsiniz:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Yük devretmeden sonra kümeyi yeniden başlatabilirsiniz. Ayarlarsanız `AUTOMATED_REGISTER="false"` , hn1-DB-0 düğümündeki SAP HANA kaynak ikincil olarak başlayamaz. Bu durumda, aşağıdaki komutu yürüterek HANA örneğini ikincil olarak yapılandırın:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Testten sonra kaynak durumu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>El ile yük devretmeyi test etme

Teste başlamadan önce kaynak durumu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Hn1-DB-0 düğümündeki kümeyi durdurarak el ile yük devretmeyi test edebilirsiniz:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [SAP HANA VM depolama yapılandırması](./hana-vm-operations-storage.md)