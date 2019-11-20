---
title: SUSE Linux Enterprise Server 'de Azure VM 'lerinde NFS için yüksek kullanılabilirlik | Microsoft Docs
description: SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde NFS için yüksek kullanılabilirlik
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: c20fc2142718d3cc49d4b80c6a5e22e26a350335
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824873"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server üzerinde Azure VM 'lerinde NFS için yüksek kullanılabilirlik

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
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Bu makalede, sanal makinelerin nasıl dağıtılacağı, sanal makinelerin nasıl yapılandırılacağı, küme çerçevesinin nasıl yükleneceği ve yüksek oranda kullanılabilir bir SAP sisteminin paylaşılan verilerini depolamak için kullanılabilecek yüksek oranda kullanılabilir bir NFS sunucusunun nasıl yükleneceği açıklanır.
Bu kılavuzda iki SAP sistemi, NW1 ve NW2 tarafından kullanılan yüksek düzeyde kullanılabilir bir NFS sunucusunun nasıl ayarlanacağı açıklanır. Örnekteki kaynakların (örneğin, sanal makineler, sanal ağlar) adları, kaynak öneki **Üretim**kaynağı ile [SAP dosya sunucusu şablonunu][template-file-server] kullandığınızı varsayar.

Önce aşağıdaki SAP notlarını ve kağıtları okuyun

* SAP Note [1928533], şunları içerir:
  * SAP yazılımının dağıtımı için desteklenen Azure VM boyutlarının listesi
  * Azure VM boyutları için önemli kapasite bilgileri
  * Desteklenen SAP yazılımı ve işletim sistemi (OS) ve veritabanı birleşimleri
  * Microsoft Azure 'de Windows ve Linux için gereken SAP Kernel sürümü

* SAP Note [2015553] , Azure 'da SAP tarafından desteklenen SAP yazılım dağıtımları için önkoşulları listeler.
* SAP Note [2205917] , SAP uygulamaları için SUSE Linux Enterprise Server önerilen işletim sistemi ayarlarına sahiptir
* SAP Note [1944799] , SUSE Linux Enterprise Server SAP uygulamaları Için SAP HANA kılavuz içerir
* SAP Note [2178632] , Azure 'da SAP için raporlanan tüm izleme ölçümleriyle ilgili ayrıntılı bilgiler içerir.
* SAP Note [2191498] , Azure 'da Linux IÇIN gereken SAP konak Aracısı sürümüne sahiptir.
* SAP Note [2243692] , Azure 'da LINUX üzerinde SAP lisanslama hakkında bilgi içerir.
* SAP Note [1984787] , SUSE Linux Enterprise Server 12 hakkında genel bilgiler içerir.
* SAP Note [1999351] , SAP Için Azure Gelişmiş izleme uzantısı için ek sorun giderme bilgilerine sahiptir.
* [SAP COMMUNITY WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 'nin Linux için gereklı tüm sap notları vardır.
* [Linux 'ta SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [Linux 'ta SAP için Azure sanal makineleri dağıtımı (Bu makale)][deployment-guide]
* [Linux üzerinde SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [SUSE Linux Enterprise High kullanılabilirlik uzantısı 12 SP3 en iyi yöntemler Kılavuzu][sles-hae-guides]
  * DRBD ve Paceyapıcısı ile yüksek oranda kullanılabilir NFS depolaması
* [SAP uygulamaları için SUSE Linux Enterprise Server 12 SP3 en iyi yöntemler Kılavuzu][sles-for-sap-bp]
* [SUSE yüksek kullanılabilirlik uzantısı 12 SP3 sürüm notları][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Genel Bakış

SAP NetWeaver, yüksek kullanılabilirlik elde etmek için bir NFS sunucusu gerektirir. NFS sunucusu ayrı bir kümede yapılandırılır ve birden çok SAP sistemi tarafından kullanılabilir.

![SAP NetWeaver yüksek kullanılabilirliğe genel bakış](./media/high-availability-guide-nfs/ha-suse-nfs.png)

NFS sunucusu, bu NFS sunucusunu kullanan her SAP sistemi için ayrılmış bir sanal ana bilgisayar adını ve sanal IP adreslerini kullanır. Azure 'da bir sanal IP adresi kullanmak için bir yük dengeleyici gereklidir. Aşağıdaki listede yük dengeleyicinin yapılandırması gösterilmektedir.        

* Ön uç yapılandırması
  * NW1 için IP adresi 10.0.0.4
  * NW2 için IP adresi 10.0.0.5
* Arka uç yapılandırması
  * NFS kümesinin parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
* Araştırma bağlantı noktası
  * NW1 için bağlantı noktası 61000
  * NW2 için bağlantı noktası 61001
* Yük Dengeleme kuralları (temel yük dengeleyici kullanılıyorsa)
  * NW1 için 2049 TCP
  * NW1 için 2049 UDP
  * NW2 için 2049 TCP
  * NW2 için 2049 UDP

## <a name="set-up-a-highly-available-nfs-server"></a>Yüksek oranda kullanılabilir bir NFS sunucusu ayarlama

Sanal makineler, kullanılabilirlik kümesi ve yük dengeleyici dahil olmak üzere tüm gerekli Azure kaynaklarını dağıtmak için GitHub 'dan bir Azure şablonu kullanabilir ya da kaynakları el ile dağıtabilirsiniz.

### <a name="deploy-linux-via-azure-template"></a>Azure şablonu aracılığıyla Linux dağıtma

Azure Marketi, yeni sanal makineler dağıtmak için kullanabileceğiniz, SAP uygulamaları için SUSE Linux Enterprise Server bir görüntü içerir.
Tüm gerekli kaynakları dağıtmak için GitHub 'daki hızlı başlangıç şablonlarından birini kullanabilirsiniz. Şablon, sanal makineleri, yük dengeleyiciyi, kullanılabilirlik kümesini vb. dağıtır. Şablonu dağıtmak için aşağıdaki adımları izleyin:

1. Azure portal [SAP dosya sunucusu şablonunu][template-file-server] açın   
1. Aşağıdaki parametreleri girin
   1. Kaynak ön eki  
      Kullanmak istediğiniz ön eki girin. Değer, dağıtılan kaynaklar için bir ön ek olarak kullanılır.
   2. SAP sistem sayısı  
      Bu dosya sunucusunu kullanacak SAP sistemlerinin sayısını girin. Bu işlem, gerekli ön uç yapılandırması, Yük Dengeleme kuralları, araştırma bağlantı noktaları, diskler vb. için dağıtım sayısını dağıtır.
   3. İşletim sistemi türü  
      Linux dağıtımlardan birini seçin. Bu örnek için SLES 12 ' yi seçin
   4. Yönetici Kullanıcı adı ve yönetici parolası  
      Makinede oturum açmak için kullanılabilecek yeni bir Kullanıcı oluşturulur.
   5. Alt ağ KIMLIĞI  
      VM 'yi tanımlanmış VM 'ye atanmış bir alt ağa sahip olduğunuz mevcut bir VNet 'e dağıtmak istiyorsanız, söz konusu alt ağın KIMLIĞINI adlandırın. KIMLIK genellikle/Subscriptions/ **&lt;ABONELIK kimliği&gt;** /ResourceGroups/ **&lt;kaynak grubu adı&gt;** /Providers/Microsoft.Network/virtualNetworks/ **&lt;sanal ağ adı&gt;** /Subnets/ **&lt;alt ağ adı&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux 'u Azure portal aracılığıyla el ile dağıtın

Önce bu NFS kümesi için sanal makineleri oluşturmanız gerekir. Daha sonra, bir yük dengeleyici oluşturur ve arka uç havuzlarındaki sanal makineleri kullanırsınız.

1. Kaynak Grubu oluşturma
1. Sanal ağ oluşturma
1. Kullanılabilirlik kümesi oluşturma  
   En fazla güncelleştirme etki alanını ayarla
1. Sanal makine oluşturma 1 en az SLES4SAP 12 SP3 kullanın, bu örnekte SLES4SAP 12 SP3 BYOS Image SLES for SAP Applications 12 SP3 (BYOS) kullanılır  
   Daha önce oluşturulan kullanılabilirlik kümesini seçin  
1. Sanal makine oluşturma 2 en az SLES4SAP 12 SP3 kullanın, bu örnekte SLES4SAP 12 SP3 BYOS görüntüsü  
   SLES for SAP Applications 12 SP3 (BYOS) kullanılır  
   Daha önce oluşturulan kullanılabilirlik kümesini seçin  
1. Her iki sanal makineye de her SAP sistemi için bir veri diski ekleyin.
1. Load Balancer oluşturun (iç). [Standart yük dengeleyiciyi](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)öneririz.  
   1. Standart yük dengeleyici oluşturmak için aşağıdaki yönergeleri izleyin:
      1. Ön uç IP adreslerini oluşturma
         1. NW1 için IP adresi 10.0.0.4
            1. Yük dengeleyiciyi açın, ön uç IP havuzu ' nu seçin ve Ekle ' ye tıklayın
            1. Yeni ön uç IP havuzunun adını girin (örneğin, **NW1-ön uç**)
            1. Atamayı statik olarak ayarlayın ve IP adresini girin (örneğin, **10.0.0.4**)
            1. Tamam 'a tıklayın
         1. NW2 için IP adresi 10.0.0.5
            * NW2 için yukarıdaki adımları yineleyin
      1. Arka uç havuzlarını oluşturma
         1. NW1 için NFS kümesinin bir parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
            1. Yük dengeleyiciyi açın, arka uç havuzları ' nı seçin ve Ekle ' ye tıklayın
            1. Yeni arka uç havuzunun adını girin (örneğin, **NW1-arka uç**)
            1. Sanal ağ seçin
            1. Sanal makine Ekle 'ye tıklayın
            1. NFS kümesinin sanal makinelerini ve IP adreslerini seçin.
            1. Ekle'ye tıklayın.
         1. NW2 için NFS kümesinin bir parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
            * NW2 için bir arka uç havuzu oluşturmak için yukarıdaki adımları yineleyin
      1. Sistem durumu araştırmalarını oluşturma
         1. NW1 için bağlantı noktası 61000
            1. Yük dengeleyiciyi açın, sistem durumu Araştırmaları ' nı seçin ve Ekle ' ye tıklayın
            1. Yeni sistem durumu araştırmasının adını girin (örneğin, **NW1-HP**)
            1. TCP as Protocol, bağlantı noktası 610**00**, zaman aralığını 5 ve sağlıksız eşik 2 ' yi seçin
            1. Tamam 'a tıklayın
         1. NW2 için bağlantı noktası 61001
            * NW2 için bir sistem durumu araştırması oluşturmak için yukarıdaki adımları yineleyin
      1. Yük Dengeleme kuralları
         1. Yük dengeleyiciyi açın, Yük Dengeleme kuralları ' nı seçin ve Ekle ' ye tıklayın.
         1. Yeni yük dengeleyici kuralının adını girin (örneğin, **NW1-lb**)
         1. Daha önce oluşturduğunuz ön uç IP adresini, arka uç havuzunu ve sistem durumu araştırmasını seçin (örneğin, **NW1-ön uç**. **NW1-arka uç** ve **NW1-HP**)
         1. **Ha bağlantı noktalarını**seçin.
         1. Boşta kalma zaman aşımını 30 dakikaya yükselt
         1. **Kayan IP 'yi etkinleştirdiğinizden emin olun**
         1. Tamam 'a tıklayın
         * NW2 için Yük Dengeleme kuralı oluşturmak için yukarıdaki adımları yineleyin
   1. Alternatif olarak, senaryonuz temel yük dengeleyici gerektiriyorsa, şu yönergeleri izleyin:
      1. Ön uç IP adreslerini oluşturma
         1. NW1 için IP adresi 10.0.0.4
            1. Yük dengeleyiciyi açın, ön uç IP havuzu ' nu seçin ve Ekle ' ye tıklayın
            1. Yeni ön uç IP havuzunun adını girin (örneğin, **NW1-ön uç**)
            1. Atamayı statik olarak ayarlayın ve IP adresini girin (örneğin, **10.0.0.4**)
            1. Tamam 'a tıklayın
         1. NW2 için IP adresi 10.0.0.5
            * NW2 için yukarıdaki adımları yineleyin
      1. Arka uç havuzlarını oluşturma
         1. NW1 için NFS kümesinin bir parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
            1. Yük dengeleyiciyi açın, arka uç havuzları ' nı seçin ve Ekle ' ye tıklayın
            1. Yeni arka uç havuzunun adını girin (örneğin, **NW1-arka uç**)
            1. Sanal makine Ekle 'ye tıklayın
            1. Daha önce oluşturduğunuz kullanılabilirlik kümesini seçin
            1. NFS kümesinin sanal makinelerini seçin
            1. Tamam 'a tıklayın
         1. NW2 için NFS kümesinin bir parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
            * NW2 için bir arka uç havuzu oluşturmak için yukarıdaki adımları yineleyin
      1. Sistem durumu araştırmalarını oluşturma
         1. NW1 için bağlantı noktası 61000
            1. Yük dengeleyiciyi açın, sistem durumu Araştırmaları ' nı seçin ve Ekle ' ye tıklayın
            1. Yeni sistem durumu araştırmasının adını girin (örneğin, **NW1-HP**)
            1. TCP as Protocol, bağlantı noktası 610**00**, zaman aralığını 5 ve sağlıksız eşik 2 ' yi seçin
            1. Tamam 'a tıklayın
         1. NW2 için bağlantı noktası 61001
            * NW2 için bir sistem durumu araştırması oluşturmak için yukarıdaki adımları yineleyin
      1. Yük Dengeleme kuralları
         1. NW1 için 2049 TCP
            1. Yük dengeleyiciyi açın, Yük Dengeleme kuralları ' nı seçin ve Ekle ' ye tıklayın
            1. Yeni yük dengeleyici kuralının adını girin (örneğin, **NW1-lb-2049**)
            1. Daha önce oluşturduğunuz ön uç IP adresini, arka uç havuzunu ve sistem durumu araştırmasını seçin (örneğin, **NW1-ön uç**)
            1. Protokol **TCP**'yi tut, bağlantı noktası **2049** girin
            1. Boşta kalma zaman aşımını 30 dakikaya yükselt
            1. **Kayan IP 'yi etkinleştirdiğinizden emin olun**
            1. Tamam 'a tıklayın
         1. NW1 için 2049 UDP
            * NW1 için bağlantı noktası 2049 ve UDP için yukarıdaki adımları yineleyin
         1. NW2 için 2049 TCP
            * NW2 için bağlantı noktası 2049 ve TCP için yukarıdaki adımları yineleyin
         1. NW2 için 2049 UDP
            * NW2 için bağlantı noktası 2049 ve UDP için yukarıdaki adımları yineleyin

> [!Note]
> Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyicisine yerleştirildiğinde, genel uç noktalara yönlendirmeye izin vermek için ek yapılandırma gerçekleştirilmediği takdirde giden internet bağlantısı olmaz. Giden bağlantıyı elde etme hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer kullanan sanal makineler Için genel uç nokta BAĞLANTıSı SAP yüksek kullanılabilirlik senaryolarında](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Azure Load Balancer arkasına yerleştirilmiş Azure VM 'lerinde TCP zaman damgalarını etkinleştirmeyin. TCP zaman damgalarını etkinleştirmek, sistem durumu araştırmalarının başarısız olmasına neden olur. **Net. IPv4. tcp_timestamps** parametresini **0**olarak ayarlayın. Ayrıntılar için bkz. [Load Balancer sistem durumu araştırmaları](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Pacemaker kümesi oluşturma

Bu NFS sunucusu için temel bir Paceoluşturucu kümesi oluşturmak üzere [Azure 'daki SUSE Linux Enterprise Server Paceyapıcısı ayarlama](high-availability-guide-suse-pacemaker.md) bölümündeki adımları izleyin.

### <a name="configure-nfs-server"></a>NFS sunucusunu yapılandırma

Şu öğeler, **[A]** ön eki olan tüm düğümlere uygulanabilir, **[1]** -yalnızca düğüm 1 veya **[2]** için geçerlidir-yalnızca node 2 için geçerlidir.

1. **[A]** kurulum konak adı çözümlemesi

   Bir DNS sunucusu kullanabilir veya tüm düğümlerdeki/etc/Konakları değiştirebilirsiniz. Bu örnek,/etc/hosts dosyasının nasıl kullanılacağını gösterir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   /Etc/hostklasörüne aşağıdaki satırları ekleyin. IP adresini ve ana bilgisayar adını ortamınıza uyacak şekilde değiştirin
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** NFS sunucusunu etkinleştirme

   Kök NFS dışarı aktarma girişini oluşturma

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** DRBD bileşenlerini yükler

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** DRBD cihazları için bölüm oluşturma

   Tüm kullanılabilir veri disklerini listeleme

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Örnek çıkış
   
   ```
   lun0  lun1
   ```

   Her veri diski için bölüm oluşturma

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** LVM yapılandırması oluşturma

   Tüm kullanılabilir bölümleri listeleme

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Örnek çıkış
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Her bölüm için LVM birimleri oluşturma

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** DRBD yapılandırma

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   DRBD. conf dosyasının aşağıdaki iki satırı içerdiğinden emin olun

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Global DRBD yapılandırmasını değiştirme

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Aşağıdaki girdileri Handler ve net bölümüne ekleyin.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** NFS DRBD cihazlarını oluşturma

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Yeni DRBD cihazının yapılandırmasını ekleme ve çıkış

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Yeni DRBD cihazının yapılandırmasını ekleme ve çıkış

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   DRBD cihazını oluşturma ve başlatma

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** ilk eşitlemeyi atla

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** birincil düğümü ayarla

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** yeni DRBD cihazları eşitlenene kadar bekleyin

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** DRBD cihazlarda dosya sistemleri oluşturma

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** DRBD Split-beybulunan algılama kurulumu

   Verileri bir ana bilgisayardan diğerine eşitlerken DRBD kullanılırken, bölünmüş beyinde denir. Bölünmüş beyin, her iki küme düğümünün DRBD cihazını birincil olacak şekilde yükseltildiği ve eşitlenmemiş olduğu bir senaryodur. Nadir bir durum olabilir, ancak yine de bir bölünmüş Bemek 'yi mümkün olduğunca hızlı bir şekilde işlemek ve çözümlemek istiyorsunuz. Bu nedenle, bölünmüş bir beyinmeydana geldiğinde bildirilmesi önemlidir.

   Bölünmüş bir beyinme bildirimi ayarlama hakkında [resmi DRBD belgelerini](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) okuyun.

   Ayrıca bölünmüş bir beyinden otomatik olarak kurtarmak mümkündür. Daha fazla bilgi için [Otomatik bölünmüş Bemek kurtarma ilkelerini](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration) okuyun
   
### <a name="configure-cluster-framework"></a>Küme çerçevesini yapılandırma

1. **[1]** SAP System NW1 için NFS DRBD cihazlarını küme yapılandırmasına ekleyin

   > [!IMPORTANT]
   > En son test, Netcat 'in biriktirme listesi ve yalnızca bir bağlantıyı işleme sınırlaması nedeniyle isteklere yanıt vermeyi durdurduğu ortaya çıkarılan durumlardır. Netcat kaynağı Azure yük dengeleyici isteklerini dinlemeyi durduruyor ve kayan IP kullanılamaz hale gelir.  
   > Mevcut Paceüreticisi kümeleri için, [Azure yük dengeleyici algılama sağlamlaştırma](https://www.suse.com/support/kb/doc/?id=7024128)içindeki yönergeleri izleyerek netcat 'i socat ile değiştirmeyi öneririz. Değişikliğin kısa kapalı kalma süresinin gerekli olacağını unutmayın.  

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:<b>61000</b>,backlog=10,fork,reuseaddr /dev/null" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** SAP System NW2 için NFS DRBD cihazlarını küme yapılandırmasına ekleyin

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:<b>61001</b>,backlog=10,fork,reuseaddr /dev/null" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]** bakım modunu devre dışı bırak
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Sonraki adımlar

* [SAP yoks ve veritabanını yükler](high-availability-guide-suse.md)
* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* Azure 'da SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan (büyük örnekler) oluşturma hakkında bilgi edinmek için bkz. [Azure 'da SAP HANA (büyük örnekler) yüksek kullanılabilirlik ve olağanüstü durum kurtarma](hana-overview-high-availability-disaster-recovery.md).
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan planı oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]
