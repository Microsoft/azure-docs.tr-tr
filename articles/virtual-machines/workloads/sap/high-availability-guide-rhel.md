---
title: Azure sanal makineler Red Hat Enterprise Linux SAP NetWeaver için yüksek kullanılabilirlik | Microsoft Docs
description: Red Hat Enterprise Linux SAP NetWeaver için Azure sanal makineleri yüksek kullanılabilirliği
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: sedusch
ms.openlocfilehash: a618a2cb976c90174125e54af645123c6b0a9dcd
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905028"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux SAP NetWeaver için Azure sanal makineleri yüksek kullanılabilirliği

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Bu makalede, sanal makinelerin nasıl dağıtılacağı, sanal makinelerin nasıl yapılandırılacağı, küme çerçevesinin nasıl yükleneceği ve yüksek oranda kullanılabilir bir SAP NetWeaver 7,50 sisteminin nasıl yükleneceği açıklanır.
Örnek yapılandırmalarda, yükleme komutları vb. ASCS örnek numarası 00, örnek numarası 02 ve SAP sistem KIMLIĞI NW1 kullanılır. Örnekteki kaynakların (örneğin, sanal makineler, sanal ağlar) adları, kaynakları oluşturmak için NW1 kaynak öneki ile [yoks/SCS şablonunu][template-multisid-xscs] kullandığınızı varsayar.

Önce aşağıdaki SAP notlarını ve kağıtları okuyun

* SAP Note [1928533], şunları içerir:
  * SAP yazılımının dağıtımı için desteklenen Azure VM boyutlarının listesi
  * Azure VM boyutları için önemli kapasite bilgileri
  * Desteklenen SAP yazılımı ve işletim sistemi (OS) ve veritabanı birleşimleri
  * Microsoft Azure 'de Windows ve Linux için gereken SAP Kernel sürümü

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
* [Red Hat Gluster depolaması için ürün belgeleri](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [Pacemaker kümesinde SAP NetWeaver](https://access.redhat.com/articles/3150081)
* Genel RHEL belgeleri
  * [Yüksek kullanılabilirlik eklentisi genel bakış](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Yüksek kullanılabilirlik eklentisi Yönetimi](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Yüksek kullanılabilirlik eklentisi başvurusu](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL 7,5 ' de tek başına kaynaklarla SAP NetWeaver için yoks/ERS yapılandırma](https://access.redhat.com/articles/3569681)
  * [RHEL üzerinde pacemaker 'da tek başına sıraya alma sunucu 2 (ENSA2) ile SAP S/4HANA yoks/ERS yapılandırma](https://access.redhat.com/articles/3974941)
* Azure 'a özgü RHEL belgeleri:
  * [RHEL yüksek kullanılabilirlik kümeleri için destek Ilkeleri-küme üyesi olarak Microsoft Azure Sanal Makineler](https://access.redhat.com/articles/3131341)
  * [Microsoft Azure üzerinde Red Hat Enterprise Linux 7,4 (ve üzeri) yüksek kullanılabilirlik kümesi yükleme ve yapılandırma](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Genel Bakış

SAP NetWeaver, yüksek kullanılabilirlik elde etmek için paylaşılan depolama gerektirir. GlusterFS ayrı bir kümede yapılandırılır ve birden çok SAP sistemi tarafından kullanılabilir.

![SAP NetWeaver yüksek kullanılabilirliğe genel bakış](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver yoks, SAP NetWeaver SCS, SAP NetWeaver ERS ve SAP HANA veritabanı sanal konak adı ve sanal IP adreslerini kullanır. Azure 'da bir sanal IP adresi kullanmak için bir yük dengeleyici gereklidir. [Standart yük dengeleyici](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)kullanmanızı öneririz. Aşağıdaki listede, (A) SCS ve ERS yük dengeleyicinin yapılandırması gösterilmektedir.

> [!IMPORTANT]
> Azure VM 'lerinde Konuk işletim sistemi olarak Red Hat Linux içeren SAP yoks/ERS 'in çoklu SID Kümelemesi **desteklenmez**. Çoklu SID Kümelemesi, tek bir Paceoluşturucu kümesinde farklı SID 'Leri olan birden fazla SAP ASCS/ERS örneğinin yüklenmesini açıklar.

### <a name="ascs"></a>A PSC

* Ön uç yapılandırması
  * IP adresi kısmına 10.0.0.7
* Arka uç yapılandırması
  * (A) SCS/ERS kümesinin parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
* Araştırma bağlantı noktası
  * Bağlantı noktası 620<strong>&lt;nr&gt;</strong>
* Yük Dengeleme kuralları
  * Standart Load Balancer kullanıyorsanız **ha bağlantı noktaları** ' nı seçin.
  * Temel Load Balancer kullanıyorsanız, aşağıdaki bağlantı noktaları için Yük Dengeleme kuralları oluşturun
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>SÖZCÜKLERI

* Ön uç yapılandırması
  * IP adresi 10.0.0.8
* Arka uç yapılandırması
  * (A) SCS/ERS kümesinin parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
* Araştırma bağlantı noktası
  * Bağlantı noktası 621<strong>&lt;nr&gt;</strong>
* Yük Dengeleme kuralları
  * Standart Load Balancer kullanıyorsanız **ha bağlantı noktaları** ' nı seçin.
  * Temel Load Balancer kullanıyorsanız, aşağıdaki bağlantı noktaları için Yük Dengeleme kuralları oluşturun
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-glusterfs"></a>GlusterFS ayarlama

SAP NetWeaver, aktarım ve profil dizini için paylaşılan depolama gerektirir. SAP NetWeaver için GlusterFS ayarlama hakkında [SAP NetWeaver için Red Hat Enterprise Linux üzerindeki Azure VM 'Lerinde GlusterFS 'yi][glusterfs-ha] okuyun.

## <a name="setting-up-ascs"></a>(A) SCS ayarlama

Sanal makineler, kullanılabilirlik kümesi ve yük dengeleyici dahil olmak üzere tüm gerekli Azure kaynaklarını dağıtmak için GitHub 'dan bir Azure şablonu kullanabilir ya da kaynakları el ile dağıtabilirsiniz.

### <a name="deploy-linux-via-azure-template"></a>Azure şablonu aracılığıyla Linux dağıtma

Azure Marketi, yeni sanal makineler dağıtmak için kullanabileceğiniz Red Hat Enterprise Linux bir görüntü içerir. Tüm gerekli kaynakları dağıtmak için GitHub 'daki hızlı başlangıç şablonlarından birini kullanabilirsiniz. Şablon, sanal makineleri, yük dengeleyiciyi, kullanılabilirlik kümesini vb. dağıtır. Şablonu dağıtmak için aşağıdaki adımları izleyin:

1. Azure portal [yoks/SCS şablonunu][template-multisid-xscs] açın  
1. Aşağıdaki parametreleri girin
   1. Kaynak ön eki  
      Kullanmak istediğiniz ön eki girin. Değer, dağıtılan kaynaklar için bir ön ek olarak kullanılır.
   1. Yığın türü  
      SAP NetWeaver yığın türünü seçin
   1. İşletim sistemi türü  
      Linux dağıtımlardan birini seçin. Bu örnekte, RHEL 7 ' yi seçin
   1. DB türü  
      HANA seçin
   1. SAP sistem sayısı  
      Bu kümede çalışan SAP sisteminin sayısı. 1 öğesini seçin.
   1. Sistem kullanılabilirliği  
      HA seçin
   1. Yönetici Kullanıcı adı, yönetici parolası veya SSH anahtarı  
      Makinede oturum açmak için kullanılabilecek yeni bir Kullanıcı oluşturulur.
   1. Alt ağ KIMLIĞI  
   VM 'yi tanımlanmış VM 'ye atanmış bir alt ağa sahip olduğunuz mevcut bir VNet 'e dağıtmak istiyorsanız, söz konusu alt ağın KIMLIĞINI adlandırın. KIMLIK genellikle/Subscriptions/ **&lt;ABONELIK kimliği&gt;** /ResourceGroups/ **&lt;kaynak grubu adı&gt;** /Providers/Microsoft.Network/virtualNetworks/ **&lt;sanal ağ adı&gt;** /Subnets/ **&lt;alt ağ adı&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux 'u Azure portal aracılığıyla el ile dağıtın

Önce bu küme için sanal makineleri oluşturmanız gerekir. Daha sonra, bir yük dengeleyici oluşturur ve arka uç havuzlarındaki sanal makineleri kullanırsınız.

1. Kaynak Grubu oluşturma
1. Sanal ağ oluşturma
1. Kullanılabilirlik kümesi oluşturma  
   En fazla güncelleştirme etki alanını ayarla
1. Sanal makine oluştur 1  
   En az RHEL 7 kullanın, bu örnekte Red Hat Enterprise Linux 7,4 görüntüsü <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Daha önce oluşturulan kullanılabilirlik kümesini seçin  
1. Sanal makine oluştur 2  
   En az RHEL 7 kullanın, bu örnekte Red Hat Enterprise Linux 7,4 görüntüsü <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Daha önce oluşturulan kullanılabilirlik kümesini seçin  
1. Her iki sanal makineye en az bir veri diski ekleyin  
   Veri diskleri/usr/SAP/`<SAPSID`> dizini için kullanılır
1. Yük dengeleyici oluşturma (iç, standart):  
   1. Ön uç IP adreslerini oluşturma
      1. YOKS için IP adresi kısmına 10.0.0.7
         1. Yük dengeleyiciyi açın, ön uç IP havuzu ' nu seçin ve Ekle ' ye tıklayın
         1. Yeni ön uç IP havuzunun adını girin (örneğin, **NW1-ascs-ön uç**)
         1. Atamayı statik olarak ayarlayın ve IP adresini girin (örneğin, **kısmına 10.0.0.7**)
         1. Tamam 'a tıklayın
      1. YOKLAR için IP adresi 10.0.0.8
         * ERS için bir IP adresi oluşturmak üzere yukarıdaki adımları tekrarlayın (örneğin, **10.0.0.8** ve **NW1-aers-arka uç**)
   1. Arka uç havuzlarını oluşturma
      1. YOKS için bir arka uç havuzu oluşturun
         1. Yük dengeleyiciyi açın, arka uç havuzları ' nı seçin ve Ekle ' ye tıklayın
         1. Yeni arka uç havuzunun adını girin (örneğin, **NW1-ascs-arka uç**)
         1. Sanal makine Ekle ' ye tıklayın.
         1. Sanal makineyi seçin.
         1. (A) SCS kümesinin sanal makinelerini ve IP adreslerini seçin.
         1. Ekle'ye tıklayın.
      1. YOKLAR için bir arka uç havuzu oluşturma
         * ERS için bir arka uç havuzu oluşturmak için yukarıdaki adımları tekrarlayın (örneğin, **NW1-aers-arka uç**)
   1. Sistem durumu araştırmalarını oluşturma
      1. YOKS için bağlantı noktası 620**00**
         1. Yük dengeleyiciyi açın, sistem durumu Araştırmaları ' nı seçin ve Ekle ' ye tıklayın
         1. Yeni sistem durumu araştırmasının adını girin (örneğin, **NW1-ascs-HP**)
         1. TCP as Protocol, bağlantı noktası 620**00**, zaman aralığını 5 ve sağlıksız eşik 2 ' yi seçin
         1. Tamam 'a tıklayın
      1. YOKLAR için bağlantı noktası 621**02**
         * ERS için bir sistem durumu araştırması oluşturmak için yukarıdaki adımları tekrarlayın (örneğin, 621**02** ve **NW1-aers-HP**)
   1. Yük Dengeleme kuralları
      1. YOKS için Yük Dengeleme kuralları
         1. Yük dengeleyiciyi açın, Yük Dengeleme kuralları ' nı seçin ve Ekle ' ye tıklayın.
         1. Yeni yük dengeleyici kuralının adını girin (örneğin, **NW1-lb-ascs**)
         1. Daha önce oluşturduğunuz ön uç IP adresini, arka uç havuzunu ve sistem durumu araştırmasını seçin (örneğin, **NW1-ascs-ön uç**, **NW1-ascs-arka uç** ve **NW1-ascs-HP**)
         1. **Ha bağlantı noktalarını** seçin
         1. Boşta kalma zaman aşımını 30 dakikaya yükselt
         1. **Kayan IP 'yi etkinleştirdiğinizden emin olun**
         1. Tamam 'a tıklayın
         * ÇÖZÜMLEYICILER için Yük Dengeleme kuralları oluşturmak için yukarıdaki adımları tekrarlayın (örneğin, **NW1-lb-ers**)
1. Alternatif olarak, senaryonuz temel yük dengeleyici (iç) gerektiriyorsa, şu adımları izleyin:  
   1. Ön uç IP adreslerini oluşturma
      1. YOKS için IP adresi kısmına 10.0.0.7
         1. Yük dengeleyiciyi açın, ön uç IP havuzu ' nu seçin ve Ekle ' ye tıklayın
         1. Yeni ön uç IP havuzunun adını girin (örneğin, **NW1-ascs-ön uç**)
         1. Atamayı statik olarak ayarlayın ve IP adresini girin (örneğin, **kısmına 10.0.0.7**)
         1. Tamam 'a tıklayın
      1. YOKLAR için IP adresi 10.0.0.8
         * ERS için bir IP adresi oluşturmak üzere yukarıdaki adımları tekrarlayın (örneğin, **10.0.0.8** ve **NW1-aers-arka uç**)
   1. Arka uç havuzlarını oluşturma
      1. YOKS için bir arka uç havuzu oluşturun
         1. Yük dengeleyiciyi açın, arka uç havuzları ' nı seçin ve Ekle ' ye tıklayın
         1. Yeni arka uç havuzunun adını girin (örneğin, **NW1-ascs-arka uç**)
         1. Sanal makine Ekle ' ye tıklayın.
         1. Daha önce oluşturduğunuz kullanılabilirlik kümesini seçin
         1. (A) SCS kümesinin sanal makinelerini seçin
         1. Tamam 'a tıklayın
      1. YOKLAR için bir arka uç havuzu oluşturma
         * ERS için bir arka uç havuzu oluşturmak için yukarıdaki adımları tekrarlayın (örneğin, **NW1-aers-arka uç**)
   1. Sistem durumu araştırmalarını oluşturma
      1. YOKS için bağlantı noktası 620**00**
         1. Yük dengeleyiciyi açın, sistem durumu Araştırmaları ' nı seçin ve Ekle ' ye tıklayın
         1. Yeni sistem durumu araştırmasının adını girin (örneğin, **NW1-ascs-HP**)
         1. TCP as Protocol, bağlantı noktası 620**00**, zaman aralığını 5 ve sağlıksız eşik 2 ' yi seçin
         1. Tamam 'a tıklayın
      1. YOKLAR için bağlantı noktası 621**02**
         * ERS için bir sistem durumu araştırması oluşturmak için yukarıdaki adımları tekrarlayın (örneğin, 621**02** ve **NW1-aers-HP**)
   1. Yük Dengeleme kuralları
      1. YOKS için 32**00** TCP
         1. Yük dengeleyiciyi açın, Yük Dengeleme kuralları ' nı seçin ve Ekle ' ye tıklayın.
         1. Yeni yük dengeleyici kuralının adını girin (örneğin, **NW1-lb-3200**)
         1. Daha önce oluşturduğunuz ön uç IP adresini, arka uç havuzunu ve sistem durumu araştırmasını seçin (örneğin, **NW1-ascs-ön uç**)
         1. Protokol **TCP**'yi tut, bağlantı noktası **3200** girin
         1. Boşta kalma zaman aşımını 30 dakikaya yükselt
         1. **Kayan IP 'yi etkinleştirdiğinizden emin olun**
         1. Tamam 'a tıklayın
      1. YOKS için ek bağlantı noktaları
         * 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 ve TCP bağlantı noktaları için yukarıdaki adımları tekrarlayın
      1. YOKLAR için ek bağlantı noktaları
         * 33**02**, 5**02**13, 5**02 14, 5** **02**16 ve TCP bağlantı noktaları için yukarıdaki adımları yineleyin

> [!Note]
> Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyicisine yerleştirildiğinde, genel uç noktalara yönlendirmeye izin vermek için ek yapılandırma gerçekleştirilmediği takdirde giden internet bağlantısı olmaz. Giden bağlantıyı elde etme hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer kullanan sanal makineler Için genel uç nokta BAĞLANTıSı SAP yüksek kullanılabilirlik senaryolarında](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Azure Load Balancer arkasına yerleştirilmiş Azure VM 'lerinde TCP zaman damgalarını etkinleştirmeyin. TCP zaman damgalarını etkinleştirmek, sistem durumu araştırmalarının başarısız olmasına neden olur. **Net. IPv4. tcp_timestamps** parametresini **0**olarak ayarlayın. Ayrıntılar için bkz. [Load Balancer sistem durumu araştırmaları](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Pacemaker kümesi oluşturma

Bu (A) SCS sunucusu için temel bir Paceoluşturucu kümesi oluşturmak üzere [Azure 'daki Red Hat Enterprise Linux Paceyapıcısı ayarlama](high-availability-guide-rhel-pacemaker.md) bölümündeki adımları izleyin.

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver yüklemesi için hazırlanma

Şu öğeler, **[A]** ön eki olan tüm düğümlere uygulanabilir, **[1]** -yalnızca düğüm 1 veya **[2]** için geçerlidir-yalnızca node 2 için geçerlidir.

1. **[A]** kurulum konak adı çözümlemesi

   Bir DNS sunucusu kullanabilir veya tüm düğümlerdeki/etc/Konakları değiştirebilirsiniz. Bu örnek,/etc/hosts dosyasının nasıl kullanılacağını gösterir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   /Etc/hostklasörüne aşağıdaki satırları ekleyin. IP adresini ve ana bilgisayar adını ortamınıza uyacak şekilde değiştirin

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** paylaşılan dizinler oluşturma

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** GlusterFS istemcisi ve diğer gereksinimleri yükler

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** kaynak-aracıların denetim sürümü-SAP

   Yüklü kaynak-Agents-SAP paketinin sürümünün en az 3.9.5 -124. EL7 olduğundan emin olun
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** bağlama girdileri ekleme

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Yeni paylaşımları bağlama

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** takas dosyası yapılandırma

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Değişikliği etkinleştirmek için aracıyı yeniden başlatın

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]** RHEL yapılandırması

   SAP Note [2002167] ' de açıklandığı gibi RHEL 'yi yapılandırma

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver yoks/ERS yükleme

1. **[1]** ascs örneği için BIR sanal IP kaynağı ve sistem durumu araştırması oluşturun

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** SAP NetWeaver yoks 'yi yükler  

   Ass için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adını kullanarak SAP NetWeaver yoks 'yi ilk düğümde kök olarak yükleyin; Örneğin, <b>NW1-ascs</b>, <b>kısmına 10.0.0.7</b> ve araştırması için kullandığınız örnek numarası. yük dengeleyici, örneğin <b>00</b>.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Yükleme/usr/SAP/**NW1**/ascs**00**' da bir alt klasör oluşturamazsa, Ass**00** klasörünün sahibini ve grubunu ayarlamayı deneyin ve yeniden deneyin.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** ers örneği için BIR sanal IP kaynağı ve sistem durumu araştırması oluşturun

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** SAP NetWeaver iciler 'ı yükler  

   SAP NetWeaver ERS 'ı, ERS için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adı ile, örneğin, <b>NW1-aers</b>, <b>10.0.0.8</b> ve araştırma için kullandığınız örnek numarası gibi bir kök olarak yükleyin. Yük Dengeleyici (örneğin <b>02</b>).

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Yükleme/usr/SAP/**NW1**/ers**02**' de bir alt klasör oluşturamazsa, ers**02** klasörünün sahibini ve grubunu ayarlamayı deneyin ve yeniden deneyin.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** yoks/SCS ve ers örnek profillerini uyarlayın

   * YOKS/SCS profili

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS profili

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** canlı tutmayı yapılandırma

   SAP NetWeaver uygulama sunucusu ve yoks/SCS arasındaki iletişim, bir yazılım yük dengeleyici aracılığıyla yönlendirilir. Yük dengeleyici, yapılandırılabilir bir zaman aşımından sonra etkin olmayan bağlantıları keser. Bunu engellemek için SAP NetWeaver ASCS/SCS profilinde bir parametre ayarlamanız ve Linux sistem ayarlarını değiştirmeniz gerekir. Daha fazla bilgi için [SAP Note 1410736][1410736] makalesini okuyun.

   Ass/SCS profil parametresi EnQue/encnı/set_so_keepalive son adımla zaten eklendi.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** /usr/SAP/sapservices dosyasını güncelleştirme

   Sapinit başlangıç betiğinin örneklerinin başlamasını engellemek için, pacemaker tarafından yönetilen tüm örneklere/usr/SAP/sapservices dosyasından açıklama eklenmelidir. HANA SR ile birlikte kullanılacaksa SAP HANA örneğini açıklama olarak kullanmayın.

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** SAP küme kaynaklarını oluşturma

  Sıraya alma sunucusu 1 mimarisini (ENSA1) kullanıyorsanız, kaynakları aşağıdaki gibi tanımlayın:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   SAP, SAP NW 7,52 itibariyle çoğaltma dahil olmak üzere sıraya alma sunucusu 2 için destek sunmuştur. ABAP platform 1809 ' den başlayarak, sıraya alma sunucusu 2 varsayılan olarak yüklenir. Sıraya alma sunucusu 2 desteği için bkz. SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
   Sıraya alma sunucusu 2 mimarisini ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) kullanıyorsanız, Resource Agent Resource-Agents-sap-4.1.1 -12. el7. x86_64 veya sonraki bir sürümünü yükleyip kaynakları aşağıdaki gibi tanımlayın:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Daha eski bir sürümden yükseltiyorsanız ve sıraya alma sunucusu 2 ' ye geçiş yapıyorsanız bkz. SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** her iki düğümdeki yoks ve ers için güvenlik duvarı kuralları ekleme

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver uygulama sunucusu hazırlığı

Bazı veritabanları, veritabanı örneği yüklemesinin bir uygulama sunucusunda yürütülmesini gerektirir. Uygulama sunucusu sanal makinelerini bu durumlarda kullanabilecek şekilde hazırlayın.

Bu adımlar, uygulama sunucusunu yoks/SCS ve HANA sunucularından farklı bir sunucuya yüklediğinizi varsaymaktadır. Aksi takdirde, aşağıdaki adımlardan bazıları (konak adı çözümlemesini yapılandırma gibi) gerekli değildir.

1. Kurulum konak adı çözümlemesi

   Bir DNS sunucusu kullanabilir veya tüm düğümlerdeki/etc/Konakları değiştirebilirsiniz. Bu örnek,/etc/hosts dosyasının nasıl kullanılacağını gösterir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   /Etc/hostklasörüne aşağıdaki satırları ekleyin. IP adresini ve ana bilgisayar adını ortamınıza uyacak şekilde değiştirin

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. Sapmnt dizinini oluşturma

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. GlusterFS istemcisi ve diğer gereksinimleri yükler

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Bağlama girişleri ekleme

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Yeni paylaşımları bağlama

   <pre><code>sudo mount -a
   </code></pre>

1. Takas dosyasını Yapılandır
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Değişikliği etkinleştirmek için aracıyı yeniden başlatın

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Veritabanını yükler

Bu örnekte, SAP HANA SAP NetWeaver yüklüdür. Bu yükleme için desteklenen her veritabanını kullanabilirsiniz. SAP HANA Azure 'da nasıl yükleyeceğiniz hakkında daha fazla bilgi için Red Hat Enterprise Linux. For a list of supported databases, see [SAP Note 1928533][1928533][Azure VM 'lerinde SAP HANA yüksek kullanılabilirlik][sap-hana-ha] bölümüne bakın.

1. SAP veritabanı örnek yüklemesini çalıştırma

   SAP NetWeaver veritabanı örneğini, veritabanı için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adı kullanarak kök olarak yükleyin. Örneğin, <b>NW1-DB</b> ve <b>10.0.0.13</b>.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver uygulama sunucusu yüklemesi

SAP uygulama sunucusu yüklemek için aşağıdaki adımları izleyin.

1. Uygulama sunucusunu hazırla

   Uygulama sunucusunu hazırlamak için yukarıdaki Bölüm [SAP NetWeaver uygulama sunucusu hazırlama](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) bölümündeki adımları izleyin.

1. SAP NetWeaver uygulama sunucusunu yükler

   Birincil veya ek SAP NetWeaver uygulamaları sunucusu yükler.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA güvenli depoyu Güncelleştir

   SAP HANA güvenli mağazayı, SAP HANA sistem çoğaltması kurulumunun sanal adını gösterecek şekilde güncelleştirin.

   Girdileri \<sapsıd > adm olarak listelemek için aşağıdaki komutu çalıştırın

   <pre><code>hdbuserstore List
   </code></pre>

   Bu, tüm girdileri listelemelidir ve şuna benzer görünmelidir
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   Çıktı, varsayılan girdinin IP adresinin, yük dengeleyicinin IP adresine değil, sanal makineye işaret ettiği gösterir. Bu girdinin, yük dengeleyicinin sanal ana bilgisayar adına işaret eden şekilde değiştirilmesi gerekir. Aynı bağlantı noktasını (yukarıdaki çıktıda**30313** ) ve veritabanı adını (yukarıdaki çıktıda bulunan**HN1** ) kullandığınızdan emin olun!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Küme kurulumunu test etme

1. ASCS örneğini el ile geçirme

   Teste başlamadan önce kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   ASCS örneğini geçirmek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Düğüm kilitlenmesinin benzetimini yap

   Teste başlamadan önce kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   ASCS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutu çalıştırın

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   Düğüm yeniden başlatıldıktan sonra durum şöyle görünmelidir.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   Hatalı kaynakları temizlemek için aşağıdaki komutu kullanın.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. İleti sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   İleti sunucusu sürecini belirlemek ve sonlandırmak için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   İleti sunucusunu yalnızca bir kez sonlandırdıysanız, sapstart tarafından yeniden başlatılır. Bunu yeterince fazla sonlandırdıysanız, Paceyapıcısı sonunda yoks örneğini diğer düğüme taşıyacaktır. Testten sonra Ass ve ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Sıraya alma sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Sıraya alma sunucusunu sonlandırmak için ASCS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutları çalıştırın.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   ASCS örneği hemen diğer düğüme yük devreder. Ass örneği başlatıldıktan sonra, ERS örneği de yük devreder. Testten sonra Ass ve ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Sıraya alma çoğaltma sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Sıraya alma çoğaltma sunucusu işlemini sonlandırmak için ERS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutu çalıştırın.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Komutu yalnızca bir kez çalıştırırsanız, sapbaşlat işlemi yeniden başlatır. Bunu yeterince sık çalıştırırsanız, sapstart işlemi yeniden başlatmaz ve kaynak durdurulmuş durumda olur. Testten sonra ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Sıraya alma sapstartsrv işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   ASCS 'nin çalıştığı düğümde kök olarak aşağıdaki komutları çalıştırın.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   Sapstartsrv işlemi, izlemenin bir parçası olarak her zaman pacemaker kaynak Aracısı tarafından yeniden başlatılmalıdır. Testten sonra kaynak durumu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* Azure 'da SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan (büyük örnekler) oluşturma hakkında bilgi edinmek için bkz. [Azure 'da SAP HANA (büyük örnekler) yüksek kullanılabilirlik ve olağanüstü durum kurtarma](hana-overview-high-availability-disaster-recovery.md).
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan planı oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]
