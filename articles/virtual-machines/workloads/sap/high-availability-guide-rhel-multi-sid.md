---
title: Azure VM 'lerinde SAP NW için yüksek kullanılabilirlik çoklu SID Kılavuzu | Microsoft Docs
description: Azure sanal makinelerinde (VM) RHEL çoklu SID 'de SAP NW için yüksek kullanılabilirlik kurun.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/11/2021
ms.author: radeltch
ms.openlocfilehash: ec2121754a24a44288c158e630a4e84219c744e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676926"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>SAP NetWeaver için Red Hat Enterprise Linux Azure VM 'lerde yüksek kullanılabilirlik çoklu SID Kılavuzu

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Bu makalede, SAP uygulamaları için Red Hat Enterprise Linux ile Azure VM 'lerinde birden fazla SAP NetWeaver yüksek kullanılabilir sisteminin (yani çoklu SID) nasıl dağıtılacağı açıklanır.  

Örnek yapılandırmalarda, yükleme komutları vb. üç SAP NetWeaver 7,50 sistemi tek ve iki düğümlü yüksek kullanılabilirlik kümesine dağıtılır. SAP sistemleri SID 'Leri şunlardır:
* **NW1**: ascs örnek numarası **00** ve sanal ana bilgisayar adı **msnw1ascs**; ERS örnek numarası **02** ve sanal ana bilgisayar adı **msnw1ers**.  
* **NW2**: ascs örnek numarası **10** ve sanal konak adı **msnw2ascs**; MS örnek numarası **12** ve sanal ana bilgisayar adı **msnw2ers**.  
* **NW3**: ascs örnek numarası **20** ve sanal konak adı **msnw3ascs**; , **22** örnek numarası ve sanal ana bilgisayar adı **msnw3ers**.  

Makale, veritabanı katmanını ve SAP NFS paylaşımlarının dağıtımını kapsamaz. Bu makaledeki örneklerde, birimin zaten dağıtıldığı varsayılarak, NFS paylaşımları için [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)  Volume **sapmsıd 'si** kullanılmaktadır. Ayrıca, Azure NetApp Files biriminin NFSv3 protokolü ile dağıtıldığını ve SAP Systems NW1, NW2 ve NW3 örneklerinin yoks ve ERS örnekleri için aşağıdaki dosya yollarının mevcut olduğunu de varsayıyoruz.  

* Volume Sapmsıd (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* Volume Sapmsıd (nfs://10.42.0.4/usrsap<b>NW1</b>ascs)
* Volume Sapmsıd (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* Volume Sapmsıd (nfs://10.42.0.4/usrsap<b>NW1</b>ERS)
* Volume Sapmsıd (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* Volume Sapmsıd (nfs://10.42.0.4/usrsap<b>NW2</b>ascs)
* Volume Sapmsıd (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* Volume Sapmsıd (nfs://10.42.0.4/usrsap<b>NW2</b>ERS)
* Volume Sapmsıd (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* Volume Sapmsıd (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* Volume Sapmsıd (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* Volume Sapmsıd (nfs://10.42.0.4/usrsap<b>NW3</b>ERS)

Başlamadan önce, önce aşağıdaki SAP notları ve incelemeleri inceleyin:

* SAP Note [1928533], şunları içerir:
  * SAP yazılımının dağıtımı için desteklenen Azure VM boyutlarının listesi
  * Azure VM boyutları için önemli kapasite bilgileri
  * Desteklenen SAP yazılımı ve işletim sistemi (OS) ve veritabanı birleşimleri
  * Microsoft Azure 'de Windows ve Linux için gereken SAP Kernel sürümü
* [Azure NetApp Files belgeleri][anf-azure-doc]
* SAP Note [2015553] , Azure 'da SAP tarafından desteklenen SAP yazılım dağıtımları için önkoşulları listeler.
* SAP Note [2002167] Red Hat Enterprise Linux için önerilen işletim sistemi ayarlarına sahiptir
* SAP Note [2009879] , Red Hat Enterprise Linux Için SAP HANA yönergelerine sahiptir
* SAP Note [2178632] , Azure 'da SAP için raporlanan tüm izleme ölçümleriyle ilgili ayrıntılı bilgiler içerir.
* SAP Note [2191498] , Azure 'da Linux IÇIN gereken SAP konak Aracısı sürümüne sahiptir.
* SAP Note [2243692] , Azure 'da LINUX üzerinde SAP lisanslama hakkında bilgi içerir.
* SAP Note [1999351] , SAP Için Azure Gelişmiş izleme uzantısı için ek sorun giderme bilgilerine sahiptir.
* [SAP COMMUNITY WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 'nin Linux için gereklı tüm sap notları vardır.
* [Linux 'ta SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [Linux 'ta SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [Linux üzerinde SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [Pacemaker kümesinde SAP NetWeaver](https://access.redhat.com/articles/3150081)
* Genel RHEL belgeleri
  * [Yüksek kullanılabilirlik Add-On genel bakış](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Yüksek kullanılabilirlik Add-On yönetimi](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Yüksek kullanılabilirlik Add-On başvurusu](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL 7,5 ' de tek başına kaynaklarla SAP NetWeaver için yoks/ERS yapılandırma](https://access.redhat.com/articles/3569681)
  * [RHEL üzerinde pacemaker 'da tek başına sıraya alma sunucu 2 (ENSA2) ile SAP S/4HANA yoks/ERS yapılandırma](https://access.redhat.com/articles/3974941)
* Azure 'a özgü RHEL belgeleri:
  * [RHEL yüksek kullanılabilirlik kümeleri için destek Ilkeleri-küme üyesi olarak Microsoft Azure Sanal Makineler](https://access.redhat.com/articles/3131341)
  * [Microsoft Azure Red Hat Enterprise Linux 7,4 (ve üzeri) High-Availability kümesini yükleme ve yapılandırma](https://access.redhat.com/articles/3252491)
* [Microsoft Azure Azure NetApp Files kullanarak NetApp SAP uygulamaları][anf-sap-applications-azure]

## <a name="overview"></a>Genel Bakış

Kümede yer alan sanal makinelerin tüm kaynakları çalıştırabilmeleri için boyutlandırılması gerekir, bu durumda yük devretme gerçekleşmelidir. Her SAP SID, çok düzeyli yüksek kullanılabilirlik kümesinde birbirinden bağımsız yük devredebilirler.  

SAP NetWeaver yüksek kullanılabilirlik elde etmek için yüksek oranda kullanılabilir paylaşımlar gerektirir. Bu belgede, [Azure NetApp FILES NFS birimlerinde](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)dağıtılan SAP paylaşımlarına yönelik örnekler sunuyoruz. Ayrıca, paylaşımları çoklu SAP sistemleri tarafından kullanılabilen yüksek oranda kullanılabilir [GlusterFS kümesinde](./high-availability-guide-rhel-glusterfs.md)barındırmak da mümkündür.  

![SAP NetWeaver yüksek kullanılabilirliğe genel bakış](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Azure VM 'lerinde, Konuk işletim sistemi olarak Red Hat Linux ile çok düzeyli SAP yoks/ERS desteği, aynı kümede **beş** SAP SID ile sınırlıdır. Her yeni SID karmaşıklığı artırır. Aynı kümedeki SAP sıraya alma çoğaltma sunucusu 1 ve sıraya alma çoğaltma sunucusu 2 ' nin bir karışımı **desteklenmez**. Çoklu SID Kümelemesi, tek bir Paceoluşturucu kümesinde farklı SID 'Leri olan birden fazla SAP ASCS/ERS örneğinin yüklenmesini açıklar. Şu anda çoklu SID Kümelemesi yalnızca yoks/ERS için desteklenir.  

> [!TIP]
> SAP yoks/ERS 'in çoklu SID Kümelemesi, daha yüksek karmaşıklığa sahip bir çözümdür. Uygulamak daha karmaşıktır. Ayrıca bakım etkinliklerini yürütürken (işletim sistemi düzeltme eki gibi) yönetim çabasını de kapsar. Gerçek uygulamayı çalıştırmadan önce, dağıtımı ve VM 'Ler, NFS takmalar, VIP 'Ler, yük dengeleyici yapılandırması vb. gibi tüm ilgili bileşenleri dikkatle planlamak için zaman alın.  

SAP NetWeaver yoks, SAP NetWeaver SCS ve SAP NetWeaver ERS sanal konak adı ve sanal IP adreslerini kullanır. Azure 'da bir sanal IP adresi kullanmak için bir yük dengeleyici gereklidir. [Standart yük dengeleyici](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)kullanmanızı öneririz.  

Aşağıdaki listede, bu çok düzeyli küme örneği için (A) SCS ve, üç SAP sistemiyle ilgili yük dengeleyici yapılandırması gösterilmektedir. SID 'lerin her biri için her bir ASCS ve bir örnek için ayrı ön uç IP, sistem durumu araştırmaları ve yük dengeleme kuralları gerekir. YOKS/ASCS kümesinin bir parçası olan tüm VM 'Leri tek bir ıLB 'nin arka uç havuzuna atayın.  

### <a name="ascs"></a>A PSC

* Ön uç yapılandırması
  * NW1 için IP adresi: 10.3.1.50
  * NW2 için IP adresi: 10.3.1.52
  * NW3 için IP adresi: 10.3.1.54

* Araştırma bağlantı noktaları
  * Bağlantı noktası 620 <strong> &lt; &gt; NR</strong>, bu nedenle NW1, NW2 ve NW3 araştırma bağlantı noktaları 620 **00**, 620 **10** ve 620 **20**
* Yük Dengeleme kuralları-her örnek için bir tane oluşturun, diğer bir deyişle, NW1/ASCS, NW2/yoks ve NW3/yoks.
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
  * NW1 10.3.1.51 için IP adresi
  * NW2 10.3.1.53 için IP adresi
  * NW3 10.3.1.55 için IP adresi

* Araştırma bağlantı noktası
  * Bağlantı noktası 621 <strong> &lt; &gt; NR</strong>, bu nedenle NW1, NW2 ve N3 araştırma bağlantı noktaları 621 **02**, 621 **12** ve 621 **22**
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

## <a name="sap-shares"></a>SAP paylaşımları

SAP NetWeaver, aktarım, profil dizini vb. için paylaşılan depolama alanı gerektirir. Yüksek oranda kullanılabilir SAP sisteminde, yüksek oranda kullanılabilir paylaşımlar olması önemlidir. SAP paylaşımlarınızın mimarisine karar vermeniz gerekir. Bir seçenek, paylaşımları [Azure NetApp FILES NFS birimlerinde](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)dağıtmaktır.  Azure NetApp Files, SAP NFS paylaşımları için yerleşik yüksek kullanılabilirlik alacaksınız.

Diğer bir seçenek de, birden fazla SAP sistemi arasında paylaşılabilen [SAP NetWeaver için Red Hat Enterprise Linux Azure VM 'Lerinde GlusterFS](./high-availability-guide-rhel-glusterfs.md)'yi oluşturmak için kullanılır. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>İlk SAP sistemini kümede dağıtma

SAP paylaşımlarına yönelik mimarinin ne olduğuna karar verdikten sonra, ilgili belgeleri izleyerek kümedeki ilk SAP sistemini dağıtın.

* Azure NetApp Files NFS birimleri kullanıyorsanız, [SAP uygulamaları için Azure NetApp Files ile Red Hat Enterprise Linux açık SAP NetWeaver Için Azure VM 'leri yüksek kullanılabilirliği](./high-availability-guide-rhel-netapp-files.md) izleyin  
* GlusterFS kümesi kullanılıyorsa, [SAP NetWeaver için Red Hat Enterprise Linux Azure VM 'Lerinde GlusterFS](./high-availability-guide-rhel-glusterfs.md)'yi izleyin.  

Yukarıda listelenen belgeler, gerekli altyapıyı hazırlama, kümeyi oluşturma, SAP uygulamasını çalıştırmak için işletim sistemini hazırlama adımlarında size kılavuzluk eder.  

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
* Küme yük devretme işlevselliği test edilmiştir.  
* Tüm SAP sistemleri için NFS paylaşımları dağıtılır.  

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver yüklemesi için hazırlanma

1. Yeni dağıtılan sistem (yani, **NW2**, **NW3**) için yapılandırma ' yı [Azure Portal aracılığıyla Azure Load Balancer dağıtım](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal)yönergelerini izleyerek mevcut Azure Load Balancer ekleyin. Yapılandırmanız için IP adreslerini, durum araştırma bağlantı noktalarını, Yük Dengeleme kurallarını ayarlayın.  

2. **[A]** ek SAP sistemleri için kurulum ad çözümlemesi. DNS sunucusu kullanabilir veya tüm düğümlerde değişiklik yapabilirsiniz `/etc/hosts` . Bu örnek, dosyanın nasıl kullanılacağını gösterir `/etc/hosts` .  IP adreslerini ve ana bilgisayar adlarını ortamınıza uyarlayın. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
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

4. **[A]** kümeye DAĞıTTıĞıNıZ ek SAP sistemleri için/Sapmnt/SID ve/usr/SAP/Sid/mı/SID/, dosya sistemlerine yönelik bağlama girdilerini ekleyin. Bu örnekte **NW2** ve **NW3**.  

   Dosyayı `/etc/fstab` , kümeye dağıttığınız ek SAP sistemleri için dosya sistemleriyle güncelleştirin.  

   * Azure NetApp Files kullanıyorsanız [buradaki](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation) yönergeleri izleyin  
   * GlusterFS kümesi kullanılıyorsa [buradaki](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation) yönergeleri izleyin  

### <a name="install-ascs--ers"></a>YOKS/ERS yüklemesi

1. Kümeye dağıttığınız ek SAP sistemlerinin yoks örnekleri için sanal IP ve durum araştırma kümesi kaynaklarını oluşturun. Burada gösterilen örnek, NFSv3 protokolüyle Azure NetApp Files birimlerde NFS kullanılarak **NW2** ve **NW3** ascs içindir.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.  

2. **[1]** SAP NetWeaver yoks 'yi yükler  

   Ass için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adı kullanarak SAP NetWeaver yoks 'yi kök olarak yükleyin. Örneğin, System **NW2** için, sanal konak adı <b>msnw2ascs</b>, <b>10.3.1.52</b> ve yük dengeleyici araştırması için kullandığınız örnek numarası, örneğin <b>10</b>. System **NW3** için, sanal konak adı <b>msnw3ascs</b>, <b>10.3.1.54</b> ve yük dengeleyici araştırması için kullandığınız örnek numarası, örneğin <b>20</b>. Her SAP SID için hangi küme düğümünü yüklebileceğinizi göz önünde KALıN.  

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz. Sanal ana bilgisayar adını kullanarak SAP 'yi yüklemek için SAPINST_USE_HOSTNAME parametresini kullanabilirsiniz.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Yükleme,/usr/SAP/**SID**/ascs **Instance #** içinde bir alt klasör oluşturamazsa, sahibini **SID** adm ve grup olarak belirlemes **örneğinin** sapsys olarak ayarlamayı deneyin ve yeniden deneyin.

3. **[1]** kümeye DAĞıTTıĞıNıZ ek SAP sisteminin ers örneği IÇIN sanal IP ve durum araştırma kümesi kaynakları oluşturun. Burada gösterilen örnek, NFSv3 protokolüyle Azure NetApp Files birimlerde NFS kullanılarak **NW2** ve **NW3** ers içindir.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun.  

   Daha sonra, yeni oluşturulan ERS grubunun kaynaklarının, aynı SAP sistemi için olan ASCS örneğinin yüklü olduğu küme düğümüne karşı, küme düğümünde çalıştığından emin olun.  Örneğin, üzerine NW2 ASCS yüklenmişse `rhelmsscl1` , NW2 ers grubunun üzerinde çalıştığından emin olun `rhelmsscl2` .  `rhelmsscl2`Gruptaki küme kaynaklarından biri için aşağıdaki komutu ÇALıŞTıRARAK NW2 ers grubunu öğesine geçirebilirsiniz: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** SAP NetWeaver iciler 'ı yükler

   SAP NetWeaver ERS 'ı diğer düğüme kök olarak yükleyin. Bu, bir sanal ana bilgisayar adını kullanarak, ERS için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşleşir. Örneğin, System **NW2** için sanal ana bilgisayar adı <b>msnw2ers</b>, <b>10.3.1.53</b> ve yük dengeleyici araştırması için kullandığınız örnek numarası olur, örneğin <b>12</b>. System **NW3** için, sanal ana bilgisayar adı <b>msnw3ers</b>, <b>10.3.1.55</b> ve yük dengeleyicinin araştırması için kullandığınız örnek numarası, örneğin <b>22</b>. 

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz. Sanal ana bilgisayar adını kullanarak SAP 'yi yüklemek için SAPINST_USE_HOSTNAME parametresini kullanabilirsiniz.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > SWPM SP 20 PL 05 veya üzeri bir sürümü kullanın. Düşük sürümler izinleri doğru olarak ayarlamayın ve yükleme başarısız olur.

   Yükleme,/usr/SAP/**NW2**/ers **örnek #**' da bir alt klasör oluşturamazsa, sahibi **SID** adm olarak ayarlamayı ve grup **#** klasörünün değerini sapsys olarak ayarlamayı deneyin ve yeniden deneyin.

   Yeni dağıtılan SAP sisteminin ERS grubunu farklı bir küme düğümüne geçirmeniz gerekiyorsa, ERS grubu için konum kısıtlamasını kaldırmayı unutmayın. Aşağıdaki komutu çalıştırarak kısıtlamayı kaldırabilirsiniz (örneğin, SAP Systems **NW2** ve **NW3** için verilmiştir). Bu küme grubunu taşımak için komutta kullandığınız kaynağa yönelik geçici kısıtlamaları kaldırdığınızdan emin olun.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** yenı yüklenen SAP sistemleri için yoks/SCS ve ers örnek profillerini uyarlayın. Aşağıda gösterilen örnek NW2 içindir. Kümeye eklenen tüm SAP örnekleri için yoks/SCS ve ERS profillerini uyarlamanız gerekir.  
 
   * YOKS/SCS profili

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

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
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** /usr/SAP/sapservices dosyasını güncelleştirme

   Sapinit başlangıç betiğinin örneklerinin başlamasını engellemek için, pacemaker tarafından yönetilen tüm örneklere dosyadan açıklama eklenmelidir `/usr/sap/sapservices` .  Aşağıda gösterilen örnek, SAP Systems **NW2** ve **NW3** içindir.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** yenı yüklenen SAP SISTEMI için SAP küme kaynaklarını oluşturun.  

   Sıraya alma sunucusu 1 mimarisini (ENSA1) kullanıyorsanız, SAP Systems **NW2** ve **NW3** için kaynakları aşağıdaki gibi tanımlayın:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP, SAP NW 7,52 itibariyle çoğaltma dahil olmak üzere sıraya alma sunucusu 2 için destek sunmuştur. ABAP platform 1809 ' den başlayarak, sıraya alma sunucusu 2 varsayılan olarak yüklenir. Sıraya alma sunucusu 2 desteği için bkz. SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
   Sıraya alma sunucusu 2 mimarisini ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) KULLANıYORSANıZ, SAP Systems **NW2** ve **NW3** için kaynakları aşağıdaki gibi tanımlayın:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Daha eski bir sürümden yükseltiyorsanız ve sıraya alma sunucusu 2 ' ye geçiş yapıyorsanız bkz. SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > Yukarıdaki yapılandırmadaki zaman aşımları yalnızca örnektir ve belirli SAP kurulumuna uyarlanmasını gerektirebilir. 

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.
   Aşağıdaki örnekte, SAP Systems **NW2** ve **NW3** 'ın kümeye eklendikten sonra küme kaynakları durumu gösterilmektedir. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** her iki düğümdeki yoks ve ers için güvenlik duvarı kuralları ekleyin.  Aşağıdaki örnekte, hem SAP Systems **NW2** hem de **NW3** için güvenlik duvarı kuralları gösterilmektedir.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3212/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3212/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3222/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3222/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>SAP yüklemesine devam edin 

SAP yüklemenizi şu şekilde doldurun:

* [SAP NetWeaver uygulama sunucularınızı hazırlama](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Bir DBMS örneği yükleme](./high-availability-guide-rhel-netapp-files.md#install-database)
* [Birincil SAP uygulama sunucusu yükleme](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* Bir veya daha fazla ek SAP uygulaması örneği yükleme

## <a name="test-the-multi-sid-cluster-setup"></a>Çoklu SID kümesi kurulumunu test etme

Aşağıdaki testler, Red Hat 'in en iyi yöntemler kılavuzlarındaki test çalışmalarının bir alt kümesidir. Bunlar kolaylık sağlaması için dahil edilmiştir. Küme testlerinin tam listesi için aşağıdaki belgelere başvurun:

* Azure NetApp Files NFS birimleri kullanıyorsanız, [SAP NetWeaver Için Azure VM 'leri yüksek kullanılabilirlik Ile RHEL, SAP uygulamaları için Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) kullanın
* Yüksek oranda kullanılabilir kullanılıyorsa `GlusterFS` , [SAP NetWeaver için RHEL üzerinde SAP Için Azure VM 'lerin yüksek kullanılabilirliğini](./high-availability-guide-rhel.md)izleyin.  

Her zaman Red Hat en iyi yöntemler kılavuzlarını okuyun ve eklenmiş olabilecek tüm ek testleri gerçekleştirin.  
Sunulan testler, üç adet SAP sistemi yüklü olan çok düzeyli bir küme olan iki düğümde bulunur.  

1. ASCS örneğini el ile geçirin. Örnekte, SAP System NW3 için ASCS örneğinin geçirilmesi gösterilmektedir.

   Teste başlamadan önce kaynak durumu:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   NW3 ASCS örneğini geçirmek için aşağıdaki komutları kök olarak çalıştırın.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Testten sonra kaynak durumu:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Düğüm kilitlenmesinin benzetimini yap

   Teste başlamadan önce kaynak durumu:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Aşağıdaki komutu, en az bir ASCS örneğinin çalıştırıldığı bir düğümde kök olarak çalıştırın. Bu örnekte, `rhelmsscl1` NW1, NW2 ve NW3 IÇIN ASCS örneklerinin çalıştığı üzerinde komutunu yürütüyoruz.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   Testten sonraki durum ve kilitlenen düğüm sonrasında yeniden başlatıldı, şöyle görünmelidir.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Hatalı kaynaklar için iletiler varsa, başarısız kaynakların durumunu temizleyin. Örnek:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan planı oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]