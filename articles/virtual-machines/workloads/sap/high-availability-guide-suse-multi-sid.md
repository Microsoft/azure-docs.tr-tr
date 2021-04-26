---
title: SLES çoklu SID Kılavuzu 'nda SAP NetWeaver için Azure VM 'Ler yüksek kullanılabilirliği | Microsoft Docs
description: SAP NetWeaver için SUSE Linux Enterprise Server Çoklu SID yüksek kullanılabilirlik Kılavuzu, SAP uygulamaları için
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 74606909a0bc87caa6acfb0eaf35c05cf35b1858
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676944"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>SAP NetWeaver için SUSE Linux Enterprise Server Azure VM 'lerde yüksek kullanılabilirlik çoklu SID Kılavuzu

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Bu makalede, SAP uygulamaları için SUSE Linux Enterprise Server ile Azure VM 'lerinde birden çok SAP NetWeaver veya S4HANA yüksek oranda kullanılabilir sistemlerin (yani çoklu SID) nasıl dağıtılacağı açıklanır.  

Örnek yapılandırmalarda, yükleme komutları vb. üç SAP NetWeaver 7,50 sistemi tek ve iki düğümlü yüksek kullanılabilirlik kümesine dağıtılır. SAP sistemleri SID 'Leri şunlardır:
* **NW1**: ascs örnek numarası **00** ve sanal ana bilgisayar adı **msnw1ascs**; ERS örnek numarası **02** ve sanal ana bilgisayar adı **msnw1ers**.  
* **NW2**: ascs örnek numarası **10** ve sanal konak adı **msnw2ascs**; MS örnek numarası **12** ve sanal ana bilgisayar adı **msnw2ers**.  
* **NW3**: ascs örnek numarası **20** ve sanal konak adı **msnw3ascs**; , **22** örnek numarası ve sanal ana bilgisayar adı **msnw3ers**.  

Makale, veritabanı katmanını ve SAP NFS paylaşımlarının dağıtımını kapsamaz. Bu makaledeki örneklerde, NFS kümesinin dağıtıldığı varsayılarak, NW3 NFS paylaşımları için NW2 NFS paylaşımları ve NW3-NFS için NW2-NFS sanal adlarını kullanıyoruz.  

Başlamadan önce, önce aşağıdaki SAP notları ve incelemeleri inceleyin:

* SAP Note [1928533][1928533], şunları içerir:
  * SAP yazılımının dağıtımı için desteklenen Azure VM boyutlarının listesi
  * Azure VM boyutları için önemli kapasite bilgileri
  * Desteklenen SAP yazılımı ve işletim sistemi (OS) ve veritabanı birleşimleri
  * Microsoft Azure 'de Windows ve Linux için gereken SAP Kernel sürümü

* SAP Note [2015553][2015553] , Azure 'da SAP tarafından desteklenen SAP yazılım dağıtımları için önkoşulları listeler.
* SAP Note [2205917][2205917] , SAP uygulamaları için SUSE Linux Enterprise Server önerilen işletim sistemi ayarlarına sahiptir
* SAP Note [1944799][1944799] , SUSE Linux Enterprise Server SAP uygulamaları Için SAP HANA kılavuz içerir
* SAP Note [2178632][2178632] , Azure 'da SAP için raporlanan tüm izleme ölçümleriyle ilgili ayrıntılı bilgiler içerir.
* SAP Note [2191498][2191498] , Azure 'da Linux IÇIN gereken SAP konak Aracısı sürümüne sahiptir.
* SAP Note [2243692][2243692] , Azure 'da LINUX üzerinde SAP lisanslama hakkında bilgi içerir.
* SAP Note [1984787][1984787] , SUSE Linux Enterprise Server 12 hakkında genel bilgiler içerir.
* SAP Note [1999351][1999351] , SAP Için Azure Gelişmiş izleme uzantısı için ek sorun giderme bilgilerine sahiptir.
* [SAP COMMUNITY WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 'nin Linux için gereklı tüm sap notları vardır.
* [Linux 'ta SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [Linux 'ta SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [Linux üzerinde SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [SUSE SAP ha En Iyi Yöntem Kılavuzu][suse-ha-guide] Kılavuzlar, NetWeaver HA ayarlamak için gerekli tüm bilgileri ve şirket içi SAP HANA sistem çoğaltmasını içerir. Bu kılavuzlarınızı genel bir taban çizgisi olarak kullanın. Çok daha ayrıntılı bilgi sağlar.
* [SUSE yüksek kullanılabilirlik uzantısı 12 SP3 sürüm notları][suse-ha-12sp3-relnotes]
* [SLES 12 ve SLES 15 için SUSE Multi-SID küme Kılavuzu](https://documentation.suse.com/sbp/all/html/SBP-SAP-MULTI-SID/index.html)
* [Microsoft Azure Azure NetApp Files kullanarak NetApp SAP uygulamaları][anf-sap-applications-azure]
## <a name="overview"></a>Genel Bakış

Kümede yer alan sanal makinelerin tüm kaynakları çalıştırabilmeleri için boyutlandırılması gerekir, bu durumda yük devretme gerçekleşmelidir. Her SAP SID, çok düzeyli yüksek kullanılabilirlik kümesinde birbirinden bağımsız yük devredebilirler.  SBD uçumlama kullanılıyorsa, SBD cihazları birden çok küme arasında paylaşılabilir.  

SAP NetWeaver yüksek kullanılabilirlik elde etmek için yüksek oranda kullanılabilir NFS paylaşımları gerektirir. Bu örnekte, SAP NFS paylaşımlarının, birden fazla SAP sistemi tarafından kullanılabilen, yüksek oranda kullanılabilir [NFS dosya sunucusu](./high-availability-guide-suse-nfs.md)üzerinde barındırıldığını varsayıyoruz. Ya da paylaşımlar [Azure NetApp FILES NFS birimlerinde](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)dağıtılır.  

![Paceyapıcısı kümesi, iki adet çok düzeyli küme, msidcl1 ve msidcl2 hakkında ayrıntılı bilgi gösterir.](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> Azure VM 'lerinde, Konuk işletim sistemi olarak SUSE Linux ile çok düzeyli SAP yoks/ERS desteği, aynı kümede **beş** SAP SID ile sınırlıdır. Her yeni SID karmaşıklığı artırır. Aynı kümedeki SAP sıraya alma çoğaltma sunucusu 1 ve sıraya alma çoğaltma sunucusu 2 ' nin bir karışımı **desteklenmez**. Çoklu SID Kümelemesi, tek bir Paceoluşturucu kümesinde farklı SID 'Leri olan birden fazla SAP ASCS/ERS örneğinin yüklenmesini açıklar. Şu anda çoklu SID Kümelemesi yalnızca yoks/ERS için desteklenir.  

> [!TIP]
> SAP yoks/ERS 'in çoklu SID Kümelemesi, daha yüksek karmaşıklığa sahip bir çözümdür. Uygulamak daha karmaşıktır. Ayrıca bakım etkinliklerini yürütürken (işletim sistemi düzeltme eki gibi) yönetim çabasını de kapsar. Gerçek uygulamayı çalıştırmadan önce, dağıtımı ve VM 'Ler, NFS takmalar, VIP 'Ler, yük dengeleyici yapılandırması vb. gibi tüm ilgili bileşenleri dikkatle planlamak için zaman alın.  

NFS sunucusu, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS ve SAP HANA veritabanı sanal konak adı ve sanal IP adreslerini kullanır. Azure 'da bir sanal IP adresi kullanmak için bir yük dengeleyici gereklidir. [Standart yük dengeleyici](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)kullanmanızı öneririz.  

Aşağıdaki listede, bu çok düzeyli küme örneği için (A) SCS ve, üç SAP sistemiyle ilgili yük dengeleyici yapılandırması gösterilmektedir. SID 'lerin her biri için her bir ASCS ve bir örnek için ayrı ön uç IP, sistem durumu araştırmaları ve yük dengeleme kuralları gerekir. YOKS/ASCS kümesinin bir parçası olan tüm VM 'Leri tek bir arka uç havuzuna atayın.  

### <a name="ascs"></a>A PSC

* Ön uç yapılandırması
  * NW1 için IP adresi: 10.3.1.14
  * NW2 için IP adresi: 10.3.1.16
  * NW3 için IP adresi: 10.3.1.13
* Araştırma bağlantı noktaları
  * Bağlantı noktası 620 <strong> &lt; &gt; NR</strong>, bu nedenle NW1, NW2 ve NW3 araştırma bağlantı noktaları 620 **00**, 620 **10** ve 620 **20**
* Yük Dengeleme kuralları- 
* Her örnek için bir tane oluşturun, diğer bir deyişle, NW1/ASCS, NW2/yoks ve NW3/yoks.
  * Standart Load Balancer kullanıyorsanız **ha bağlantı noktaları** ' nı seçin.
  * Temel Load Balancer kullanıyorsanız, aşağıdaki bağlantı noktaları için Yük Dengeleme kuralları oluşturun
    * 32<strong> &lt; NR &gt; </strong> TCP
    * 36<strong> &lt; NR &gt; </strong> TCP
    * 39<strong> &lt; NR &gt; </strong> TCP
    * 81<strong> &lt; NR &gt; </strong> TCP
    * 5<strong> &lt; NR &gt; </strong>13 TCP
    * 5<strong> &lt; NR &gt; </strong>14 TCP
    * 5<strong> &lt; NR &gt; </strong>16 TCP

### <a name="ers"></a>SÖZCÜKLERI

* Ön uç yapılandırması
  * NW1 10.3.1.15 için IP adresi
  * NW2 10.3.1.17 için IP adresi
  * NW3 10.3.1.19 için IP adresi
* Araştırma bağlantı noktası
  * Bağlantı noktası 621 <strong> &lt; &gt; NR</strong>, bu nedenle NW1, NW2 ve N # araştırma bağlantı noktaları 621 **02**, 621 **12** ve 621 **22**
* Yük Dengeleme kuralları-her örnek için, diğer bir deyişle, NW1/ERS, NW2/ERS ve NW3/ERS için bir tane oluşturun.
  * Standart Load Balancer kullanıyorsanız **ha bağlantı noktaları** ' nı seçin.
  * Temel Load Balancer kullanıyorsanız, aşağıdaki bağlantı noktaları için Yük Dengeleme kuralları oluşturun
    * 32<strong> &lt; NR &gt; </strong> TCP
    * 33<strong> &lt; NR &gt; </strong> TCP
    * 5<strong> &lt; NR &gt; </strong>13 TCP
    * 5<strong> &lt; NR &gt; </strong>14 TCP
    * 5<strong> &lt; NR &gt; </strong>16 TCP

* Arka uç yapılandırması
  * (A) SCS/ERS kümesinin parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı

> [!IMPORTANT]
> Kayan IP, Yük Dengeleme senaryolarında NIC ikincil IP yapılandırmasında desteklenmez. Ayrıntılar için bkz. [Azure yük dengeleyici sınırlamaları](../../../load-balancer/load-balancer-multivip-overview.md#limitations). VM için ek IP adresine ihtiyacınız varsa ikinci bir NIC dağıtın.  

> [!Note]
> Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyicisine yerleştirildiğinde, genel uç noktalara yönlendirmeye izin vermek için ek yapılandırma gerçekleştirilmediği takdirde giden internet bağlantısı olmaz. Giden bağlantıyı elde etme hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer kullanan sanal makineler Için genel uç nokta BAĞLANTıSı SAP yüksek kullanılabilirlik senaryolarında](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Azure Load Balancer arkasına yerleştirilmiş Azure VM 'lerinde TCP zaman damgalarını etkinleştirmeyin. TCP zaman damgalarını etkinleştirmek, sistem durumu araştırmalarının başarısız olmasına neden olur. Parametre **net.ipv4.tcp_timestamps** **0** olarak ayarlayın. Ayrıntılar için bkz. [Load Balancer sistem durumu araştırmaları](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="sap-nfs-shares"></a>SAP NFS paylaşımları

SAP NetWeaver, aktarım, profil dizini vb. için paylaşılan depolama alanı gerektirir. Yüksek oranda kullanılabilir SAP sisteminde, yüksek oranda kullanılabilir NFS paylaşımları olması önemlidir. SAP NFS paylaşımlarınızın mimarisine karar vermeniz gerekir. Tek bir seçenek, birden fazla SAP sistemi arasında paylaşılabilen [SUSE Linux Enterprise Server Azure VM 'Lerinde yüksek oranda KULLANILABILIR NFS kümesi][nfs-ha]oluşturmak için kullanılır. 

Diğer bir seçenek de paylaşımları [Azure NetApp FILES NFS birimlerinde](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)dağıtmaktır.  Azure NetApp Files, SAP NFS paylaşımları için yerleşik yüksek kullanılabilirlik alacaksınız.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>İlk SAP sistemini kümede dağıtma

SAP NFS paylaşımlarına yönelik mimarinin ne olduğuna karar verdikten sonra, ilgili belgeleri izleyerek kümedeki ilk SAP sistemini dağıtın.

* Yüksek oranda kullanılabilir NFS sunucusu kullanıyorsanız, [SAP uygulamaları için SUSE Linux Enterprise Server üzerindeki Azure VM 'LERINDE SAP NetWeaver Için yüksek kullanılabilirliği](./high-availability-guide-suse.md)izleyin.  
* Azure NetApp Files NFS birimleri kullanıyorsanız, [SAP uygulamaları için Azure NetApp Files ile SuSE Linux Enterprise Server Azure VM 'LERINDE SAP NetWeaver Için yüksek kullanılabilirliği](./high-availability-guide-suse-netapp-files.md) izleyin

Yukarıda listelenen belgeler, gerekli altyapıları hazırlama, kümeyi oluşturma, SAP uygulamasını çalıştırmak için işletim sistemini hazırlama adımlarında size kılavuzluk eder.  

> [!TIP]
> Kümeye ek SAP SID 'Leri eklemeden önce, ilk sistem dağıtıldıktan sonra kümenin yük devretme işlevini her zaman test edin. Bu şekilde, ek SAP sistemlerinin karmaşıklığını kümeye eklemeden önce küme işlevselliğinin çalışıp çalışmadığını bilirsiniz.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Kümeye ek SAP sistemleri dağıtma

Bu örnekte, sistem **NW1** zaten kümede dağıtıldığını varsaydık. Küme SAP Systems **NW2** ve **NW3**' de nasıl dağıtılacağı gösterilecektir. 

Şu öğeler, **[A]** ön eki olan tüm düğümlere uygulanabilir, **[1]** -yalnızca düğüm 1 veya **[2]** için geçerlidir-yalnızca node 2 için geçerlidir.

### <a name="prerequisites"></a>Önkoşullar 

> [!IMPORTANT]
> Kümeye ek SAP sistemleri dağıtmaya yönelik yönergeleri izlemeden önce, ilk sistem dağıtımı sırasında yalnızca gerekli adımlar olduğundan, kümedeki ilk SAP sistemini dağıtmak için yönergeleri izleyin.  

Bu belgede şu şekilde varsayılmaktadır:
* Pacemaker kümesi zaten yapılandırılmış ve çalışıyor.  
* En az bir SAP sistemi (yoks/ERS örneği) zaten dağıtılmış ve kümede çalışıyor.  
* Küme yük devretme işlevleri test edilmiştir.  
* Tüm SAP sistemleri için NFS paylaşımları dağıtılır.  

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver yüklemesi için hazırlanma

1. Yeni dağıtılan sistem (yani, **NW2**, **NW3**) için yapılandırma ' yı [Azure Portal aracılığıyla Azure Load Balancer dağıtım](./high-availability-guide-suse-netapp-files.md#deploy-azure-load-balancer-manually-via-azure-portal)yönergelerini izleyerek mevcut Azure Load Balancer ekleyin. Yapılandırmanız için IP adreslerini, durum araştırma bağlantı noktalarını, Yük Dengeleme kurallarını ayarlayın.  

2. **[A]** ek SAP sistemleri için ad çözümlemesi ayarlayın. DNS sunucusu kullanabilir veya tüm düğümlerde değişiklik yapabilirsiniz `/etc/hosts` . Bu örnek, dosyanın nasıl kullanılacağını gösterir `/etc/hosts` .  IP adreslerini ve ana bilgisayar adlarını ortamınıza uyarlayın. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
   ```

3. **[A]** kümeye dağıttığınız ek **NW2** ve **NW3** SAP sistemleri için paylaşılan dizinleri oluşturun. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** `autofs` kümeye dağıttığınız ek SAP sistemleri için/sapmnt/SID ve/usr/SAP/Sid/SID Bu örnekte **NW2** ve **NW3**.  

   Dosyayı `/etc/auto.direct` , kümeye dağıttığınız ek SAP sistemleri için dosya sistemleriyle güncelleştirin.  

   * NFS dosya sunucusu kullanıyorsanız [buradaki](./high-availability-guide-suse.md#prepare-for-sap-netweaver-installation) yönergeleri izleyin
   * Azure NetApp Files kullanıyorsanız [buradaki](./high-availability-guide-suse-netapp-files.md#prepare-for-sap-netweaver-installation) yönergeleri izleyin 

   `autofs`Yeni eklenen paylaşımları bağlamak için hizmeti yeniden başlatmanız gerekir.  

### <a name="install-ascs--ers"></a>YOKS/ERS yüklemesi

1. Kümeye dağıttığınız ek SAP sisteminin ASCS örneği için sanal IP ve durum araştırma kümesi kaynaklarını oluşturun. Burada gösterilen örnek, yüksek oranda kullanılabilir NFS sunucusu kullanılarak **NW2** ve **NW3** ascs içindir.  

   > [!IMPORTANT]
   > En son test, Netcat 'in biriktirme listesi ve yalnızca bir bağlantıyı işleme sınırlaması nedeniyle isteklere yanıt vermeyi durdurduğu ortaya çıkarılan durumlardır. Netcat kaynağı Azure yük dengeleyici isteklerini dinlemeyi durduruyor ve kayan IP kullanılamaz hale gelir.  
   > Mevcut Paceyapıcısı kümelerinde, Netcat 'i socat ile değiştirme konusunda tavsiye ederiz. Şu anda paket kaynak aracılarının bir parçası olan Azure-lb kaynak Aracısı 'nı şu paket sürümü gereksinimleriyle kullanmanızı öneririz:
   > - SLES 12 SP4/SP5 için sürüm en az Resource-Agents-4.3.018. a7fb5035-3.30.1 olmalıdır.  
   > - SLES 15/15 SP1 için sürüm en az Resource-Agents-4.3.0184.6 ee15eb2-4.13.1 olmalıdır.  
   >
   > Değişikliğin kısa kapalı kalma süresinin gerekli olacağını unutmayın.  
   > Mevcut pacemaker kümelerinde, yapılandırma [azure Load-Balancer algılama sağlamlaştırma](https://www.suse.com/support/kb/doc/?id=7024128)bölümünde açıklandığı gibi socat kullanmak üzere zaten değiştirilmişse, Azure-lb Resource Agent 'a hemen geçiş yapma gereksinimi yoktur.

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS azure-lb port=62010
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS azure-lb port=62020
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   Kaynakları oluştururken farklı küme kaynaklarına atanabilirler. Bunları gruplandırdığınızda, küme düğümlerinden birine geçiş yapılır. Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

2. **[1]** SAP NetWeaver yoks 'yi yükler  

   Ass için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adı kullanarak SAP NetWeaver yoks 'yi kök olarak yükleyin. Örneğin, System **NW2** için, sanal konak adı <b>msnw2ascs</b>, <b>10.3.1.16</b> ve yük dengeleyici araştırması için kullandığınız örnek numarası, örneğin <b>10</b>. System **NW3** için, sanal konak adı <b>msnw3ascs</b>, <b>10.3.1.13</b> ve yük dengeleyici araştırması için kullandığınız örnek numarası, örneğin <b>20</b>.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz. Sanal ana bilgisayar adını kullanarak SAP 'yi yüklemek için SAPINST_USE_HOSTNAME parametresini kullanabilirsiniz.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   Yükleme,/usr/SAP/**SID**/ascs **Instance #** içinde bir alt klasör oluşturamazsa, sahibini **SID** adm ve grup olarak belirlemes **örneğinin** sapsys olarak ayarlamayı deneyin ve yeniden deneyin.

3. **[1]** kümeye DAĞıTTıĞıNıZ ek SAP sisteminin ers örneği IÇIN sanal IP ve durum araştırma kümesi kaynakları oluşturun. Burada gösterilen örnek, yüksek oranda kullanılabilir NFS sunucusu kullanılarak **NW2** ve **NW3** ers içindir. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS azure-lb port=62112
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS azure-lb port=62122
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   Kaynakları oluştururken farklı küme düğümlerine atanabilirler. Bunları gruplandırdığınızda, küme düğümlerinden birine geçiş yapılır. Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun.  

   Daha sonra, yeni oluşturulan ERS grubunun kaynaklarının, aynı SAP sistemi için olan ASCS örneğinin yüklü olduğu küme düğümüne karşı, küme düğümünde çalıştığından emin olun.  Örneğin, üzerine NW2 ASCS yüklenmişse `slesmsscl1` , NW2 ers grubunun üzerinde çalıştığından emin olun `slesmsscl2` .  Aşağıdaki komutu çalıştırarak NW2 ERS grubunu öğesine geçirebilirsiniz `slesmsscl2` : 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** SAP NetWeaver iciler 'ı yükler

   SAP NetWeaver ERS 'ı diğer düğüme kök olarak yükleyin. Bu, bir sanal ana bilgisayar adını kullanarak, ERS için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşleşir. Örneğin, System **NW2** için sanal ana bilgisayar adı <b>msnw2ers</b>, <b>10.3.1.17</b> ve yük dengeleyici araştırması için kullandığınız örnek numarası olur, örneğin <b>12</b>. System **NW3** için, sanal ana bilgisayar adı <b>msnw3ers</b>, <b>10.3.1.19</b> ve yük dengeleyicinin araştırması için kullandığınız örnek numarası, örneğin <b>22</b>. 

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz. Sanal ana bilgisayar adını kullanarak SAP 'yi yüklemek için SAPINST_USE_HOSTNAME parametresini kullanabilirsiniz.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > SWPM SP 20 PL 05 veya üzeri bir sürümü kullanın. Düşük sürümler izinleri doğru olarak ayarlamayın ve yükleme başarısız olur.

   Yükleme,/usr/SAP/**NW2**/ers **örnek #**' da bir alt klasör oluşturamazsa, sahibi **SID** adm olarak ayarlamayı ve grup **#** klasörünün değerini sapsys olarak ayarlamayı deneyin ve yeniden deneyin.

   Yeni dağıtılan SAP sisteminin ERS grubunu farklı bir küme düğümüne geçirmeniz gerekiyorsa, ERS grubu için konum kısıtlamasını kaldırmayı unutmayın. Aşağıdaki komutu çalıştırarak kısıtlamayı kaldırabilirsiniz (örneğin, SAP Systems **NW2** ve **NW3** için verilmiştir).  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** yenı yüklenen SAP sistemleri için yoks/SCS ve ers örnek profillerini uyarlayın. Aşağıda gösterilen örnek NW2 içindir. Kümeye eklenen tüm SAP örnekleri için yoks/SCS ve ERS profillerini uyarlamanız gerekir.  
 
 * YOKS/SCS profili

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   Hem ENSA1 hem de ENSA2 için, `keepalive` işletim sistemi PARAMETRELERININ SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)' de açıklandığı gibi ayarlandığından emin olun.  

 * ERS profili

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** yenı dağıtılan SAP SISTEMI için SAP kullanıcılarını yapılandırın, bu örnekte **NW2** ve **NW3**. 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. Yeni yüklenen SAP sistemi için, bu dosya için ASCS ve Ise SAP hizmetlerini ekleyin `sapservice` . Aşağıda gösterilen örnek, SAP Systems **NW2** ve **NW3** içindir.  

   ASCS hizmeti girişini ikinci düğüme ekleyin ve ilk düğüme ERS hizmet girişini kopyalayın. Her SAP sistemi için, SAP sisteminin ASCS örneğinin yüklendiği düğüm üzerindeki komutları yürütün.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** yenı yüklenen SAP SISTEMI için SAP küme kaynaklarını oluşturun. 

   Sıraya alma sunucusu 1 mimarisini (ENSA1) kullanıyorsanız, SAP Systems **NW2** ve **NW3** için kaynakları aşağıdaki gibi tanımlayın:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   SAP, SAP NW 7,52 itibariyle çoğaltma dahil olmak üzere sıraya alma sunucusu 2 için destek sunmuştur. ABAP platform 1809 ' den başlayarak, sıraya alma sunucusu 2 varsayılan olarak yüklenir. Sıraya alma sunucusu 2 desteği için bkz. SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
   Sıraya alma sunucusu 2 mimarisini ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) KULLANıYORSANıZ, SAP Systems **NW2** ve **NW3** için kaynakları aşağıdaki gibi tanımlayın:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Daha eski bir sürümden yükseltiyorsanız ve sıraya alma sunucusu 2 ' ye geçiş yapıyorsanız bkz. SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.
   Aşağıdaki örnekte, SAP Systems **NW2** ve **NW3** 'ın kümeye eklendikten sonra küme kaynakları durumu gösterilmektedir. 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   Aşağıdaki resimde, kaynakların, SAP System **NW2** genişletilmiş kaynakları Ile ha Web Kontek (hak) içinde nasıl görünebilmeleri gösterilmektedir.  

   [![SAP NetWeaver yüksek kullanılabilirliğe genel bakış](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>SAP yüklemesine devam edin 

SAP yüklemenizi şu şekilde doldurun:

* [SAP NetWeaver uygulama sunucularınızı hazırlama](./high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Bir DBMS örneği yükleme](./high-availability-guide-suse.md#install-database)
* [Birincil SAP uygulama sunucusu yükleme](./high-availability-guide-suse.md#sap-netweaver-application-server-installation)
* Bir veya daha fazla ek SAP uygulaması örneği yükleme

## <a name="test-the-multi-sid-cluster-setup"></a>Çoklu SID kümesi kurulumunu test etme

Aşağıdaki testler, SUSE 'in en iyi yöntemler kılavuzlarındaki test çalışmalarının bir alt kümesidir. Bunlar kolaylık sağlaması için dahil edilmiştir. Küme testlerinin tam listesi için aşağıdaki belgelere başvurun:

* Yüksek oranda kullanılabilir NFS sunucusu kullanıyorsanız, [SAP uygulamaları için SUSE Linux Enterprise Server üzerindeki Azure VM 'LERINDE SAP NetWeaver Için yüksek kullanılabilirliği](./high-availability-guide-suse.md)izleyin.  
* Azure NetApp Files NFS birimleri kullanıyorsanız, [SAP uygulamaları için Azure NetApp Files ile SuSE Linux Enterprise Server Azure VM 'LERINDE SAP NetWeaver Için yüksek kullanılabilirliği](./high-availability-guide-suse-netapp-files.md) izleyin

Her zaman SUSE en iyi yöntemler kılavuzlarını okuyun ve eklenmiş olabilecek tüm ek testleri gerçekleştirin.  
Sunulan testler, üç adet SAP sistemi yüklü olan çok düzeyli bir küme olan iki düğümde bulunur.  

1. HAGetFailoverConfig ve HACheckFailoverConfig test et

   <sapsid>ASCS örneğinin Şu anda çalıştırıldığı düğümde aşağıdaki komutları adm olarak çalıştırın. Komutlar hata vererek başarısız olursa, bu durum, ana bilgisayar bilgisayarınızdaki çizgilerden kaynaklanıyor olabilir. Bu bilinen bir sorundur ve SAP-SUSE-Cluster-Connector paketindeki SUSE tarafından düzeltilecektir.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. ASCS örneğini el ile geçirin. Örnekte, SAP System NW2 için ASCS örneğinin geçirilmesi gösterilmektedir.  
   Testi başlatmadan önce kaynak durumu:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   NW2 ASCS örneğini geçirmek için aşağıdaki komutları kök olarak çalıştırın.

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   Testten sonra kaynak durumu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. HAFailoverToNode öğesini test edin. Burada sunulan test, SAP System NW2 için ASCS örneğinin geçirilmesini gösterir.  

   Teste başlamadan önce kaynak durumu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   NW2 ASCS örneğini geçirmek için aşağıdaki komutları **NW2** adm olarak çalıştırın.

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   Testten sonra kaynak durumu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Düğüm kilitlenmesinin benzetimini yap

   Teste başlamadan önce kaynak durumu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   En az bir ASCS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutu çalıştırın. Bu örnekte, `slesmsscl2` NW1 ve NW3 IÇIN ASCS örneklerinin çalıştığı üzerinde komutunu yürütüyoruz.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   SBD kullanırsanız, pacemaker, sonlandırılan düğümde otomatik olarak başlamamalıdır. Düğüm yeniden başlatıldıktan sonra durum şöyle görünmelidir.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   Sonlandırılan düğümde pacemaker ' ı başlatmak, SBD iletilerini temizlemek ve hatalı kaynakları temizlemek için aşağıdaki komutları kullanın.

   ```bash
   # run as root
   # list the SBD device(s)
   cat /etc/sysconfig/sbd | grep SBD_DEVICE=

   # output is like:
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   systemctl start pacemaker
   crm resource cleanup rsc_sap_NW1_ERS02
   crm resource cleanup rsc_sap_NW2_ERS12
   crm resource cleanup rsc_sap_NW3_ERS22
   ```

   Testten sonra kaynak durumu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan planı oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]