---
title: Azure VM 'lerinde SAP NW için yüksek kullanılabilirlik Azure NetApp Files | Microsoft Docs
description: SAP NetWeaver için SUSE Linux Enterprise Server yüksek kullanılabilirlik Kılavuzu, SAP uygulamaları için Azure NetApp Files
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
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: eed971471ad8a91f09a812830eb83ac17bc59a01
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307580"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>SAP uygulamaları için Azure NetApp Files SUSE Linux Enterprise Server üzerindeki Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik

[dbms-guide]:dbms_guide_general.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
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

Bu makalede, [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)kullanarak sanal makinelerin nasıl dağıtılacağı, sanal makinelerin nasıl yapılandırılacağı, küme çerçevesinin nasıl yükleneceği ve yüksek oranda KULLANILABILIR bir SAP NetWeaver 7,50 sisteminin nasıl yükleneceği açıklanır.
Örnek yapılandırmalarda, yükleme komutlarında vb., Ass örneği 00, örnek numarası 01, birincil uygulama örneği (PAS) 02 ve uygulama örneği (AAS) 03 ' dir. SAP sistem KIMLIĞI QAS kullanılır. 

Bu makalede, Azure NetApp Files ile SAP NetWeaver uygulaması için yüksek kullanılabilirlik elde etmek açıklanır. Veritabanı katmanı Bu makalede ayrıntılı olarak ele alınmıyor.

Önce aşağıdaki SAP notlarını ve kağıtları okuyun:

* [Azure NetApp Files belgeleri][anf-azure-doc] 
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
* SAP Community WIKI] ( https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) Linux için tüm gereklı SAP notları vardır.
* [Linux 'ta SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [Linux 'ta SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [Linux üzerinde SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [SUSE SAP ha En Iyi Yöntem Kılavuzu][suse-ha-guide] Kılavuzlar, NetWeaver HA ayarlamak için gerekli tüm bilgileri ve şirket içi SAP HANA sistem çoğaltmasını içerir. Bu kılavuzlarınızı genel bir taban çizgisi olarak kullanın. Çok daha ayrıntılı bilgi sağlar.
* [SUSE yüksek kullanılabilirlik uzantısı 12 SP3 sürüm notları][suse-ha-12sp3-relnotes]
* [Microsoft Azure Azure NetApp Files kullanarak NetApp SAP uygulamaları][anf-sap-applications-azure]

## <a name="overview"></a>Genel Bakış

SAP NetWeaver Merkezi Hizmetleri için yüksek kullanılabilirlik (HA), paylaşılan depolama gerektirir.
Yüksek oranda kullanılabilir NFS kümesi oluşturmak için SUSE Linux 'ta bu işlemi gerçekleştirmek için gereklidir. 

Artık Azure NetApp Files dağıtılan paylaşılan depolamayı kullanarak SAP NetWeaver HA elde etmek mümkündür. Paylaşılan depolama için Azure NetApp Files kullanmak, ek [NFS kümesi](./high-availability-guide-suse-nfs.md)gereksinimini ortadan kaldırır. SAP NetWeaver Merkezi Hizmetleri (yoks/SCS) HA için pacemaker hala gereklidir.


![SAP NetWeaver yüksek kullanılabilirliğe genel bakış](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.png)

SAP NetWeaver yoks, SAP NetWeaver SCS, SAP NetWeaver ERS ve SAP HANA veritabanı sanal konak adı ve sanal IP adreslerini kullanır. Azure 'da bir sanal IP adresi kullanmak için bir [yük dengeleyici](../../../load-balancer/load-balancer-overview.md) gereklidir. [Standart yük dengeleyici](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)kullanmanızı öneririz. Aşağıdaki listede, (A) SCS ve ERS yük dengeleyicinin yapılandırması gösterilmektedir.

### <a name="ascs"></a>A PSC

* Ön uç yapılandırması
  * IP adresi 10.1.1.20
* Araştırma bağlantı noktası
  * Bağlantı noktası 620<strong> &lt; NR &gt; </strong>
* Yük Dengeleme kuralları
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
  * IP adresi 10.1.1.21
* Araştırma bağlantı noktası
  * Bağlantı noktası 621<strong> &lt; NR &gt; </strong>
* Yük Dengeleme kuralları
  * Standart Load Balancer kullanıyorsanız **ha bağlantı noktaları** ' nı seçin.
  * Temel Load Balancer kullanıyorsanız, aşağıdaki bağlantı noktaları için Yük Dengeleme kuralları oluşturun
    * 32<strong> &lt; NR &gt; </strong> TCP
    * 33<strong> &lt; NR &gt; </strong> TCP
    * 5<strong> &lt; NR &gt; </strong>13 TCP
    * 5<strong> &lt; NR &gt; </strong>14 TCP
    * 5<strong> &lt; NR &gt; </strong>16 TCP

* Arka uç yapılandırması
  * (A) SCS/ERS kümesinin parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı


## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files altyapısını ayarlama 

SAP NetWeaver, aktarım ve profil dizini için paylaşılan depolama gerektirir.  Azure NetApp dosyaları altyapısına yönelik kuruluma devam etmeden önce [Azure NetApp Files belgelerini inceleyin][anf-azure-doc]. Seçtiğiniz Azure bölgesinin Azure NetApp Files sunmadığını denetleyin. Aşağıdaki bağlantıda Azure bölgesine göre Azure NetApp Files kullanılabilirliği gösterilmektedir: [Azure bölgesine göre Azure NetApp Files kullanılabilirliği][anf-avail-matrix].

Azure NetApp dosyaları çeşitli [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)kullanılabilir. Azure NetApp Files dağıtılmadan önce, [Azure NetApp dosyaları Için kaydolmayı][anf-register]izleyerek Azure NetApp Files ekleme isteği yapın. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files kaynaklarını dağıtma  

Adımlarda zaten [Azure sanal ağını](../../../virtual-network/virtual-networks-overview.md)dağıttığınız varsayılmaktadır. Azure NetApp Files kaynaklarının bağlanmasının gerektiği Azure NetApp Files kaynakları ve VM 'Ler aynı Azure sanal ağında veya eşlenmiş Azure sanal ağlarında dağıtılmalıdır.  

1. Henüz yapmadıysanız, [Azure NetApp Files ekleme](../../../azure-netapp-files/azure-netapp-files-register.md)isteyin.  

2. [Newtapp hesabını oluşturma yönergelerini](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)izleyerek, seçili Azure bölgesinde NetApp hesabını oluşturun.  
3. [Azure NetApp Files kapasite havuzunu ayarlama yönergelerini](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)izleyerek Azure NetApp Files kapasite havuzunu ayarlayın.  
Bu makalede sunulan SAP NetWeaver mimarisi, tek Azure NetApp Files kapasite havuzu, Premium SKU kullanır. Azure 'da SAP NetWeaver uygulama iş yükü için Premium SKU Azure NetApp Files önerilir.  

4. [Azure NetApp Files için bir alt ağ temsilcisine yönelik yönergeler](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)bölümünde açıklandığı gibi Azure NetApp dosyalarına bir alt ağ atayın.  

5. [Azure NetApp Files için bir birim oluşturma yönergelerini](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)izleyerek Azure NetApp Files birimleri dağıtın. Birimleri belirlenen Azure NetApp Files [alt ağına](/rest/api/virtualnetwork/subnets)dağıtın. Azure NetApp birimlerinin IP adresleri otomatik olarak atanır. Azure NetApp Files kaynaklarının ve Azure VM 'lerinin aynı Azure sanal ağında veya eşlenmiş Azure sanal ağlarında olması gerektiğini unutmayın. Bu örnekte iki Azure NetApp Files birimi kullanıyoruz: SAP<b>QAS</b> ve Trans. Karşılık gelen bağlama noktalarına bağlanan dosya yolları/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS/usrsap</b><b>QAS</b>sys, vb. ' dir.  

   1. toplu SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/Sapmnt<b>QAS</b>)
   2. toplu SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>ascs)
   3. toplu SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. toplu SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>ERS)
   5. Volume Trans (nfs://10.1.0.4/trans)
   6. toplu SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas)
   7. toplu SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)

   
Bu örnekte, Azure NetApp Files nasıl kullanılabileceğini göstermek üzere tüm SAP NetWeaver dosya sistemleri için Azure NetApp Files kullandık. NFS aracılığıyla bağlanması gerekmeyen SAP dosya sistemleri de [Azure disk depolama](../../disks-types.md#premium-ssd) olarak dağıtılabilir. Bu örnekte <b>bir-e</b> 'nin Azure NetApp Files olması gerekir ve <b>f-g</b> (diğer bir deyişle,/usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) Azure disk depolama olarak dağıtılabilir. 

### <a name="important-considerations"></a>Önemli noktalar

SUSE yüksek kullanılabilirlik mimarisinde SAP NetWeaver için Azure NetApp Files düşünürken, aşağıdaki önemli noktalara dikkat edin:

- En düşük kapasite havuzu 4 TiB 'dir. Kapasite havuzu boyutu 1 TiB 'lik artışlarla artırılabilir.
- Minimum birim 100 GiB 'dir
- Azure NetApp Files ve Azure NetApp Files birimlerinin takılabileceği tüm sanal makineler aynı bölgedeki aynı Azure sanal ağında veya eşlenmiş [sanal ağlarda](../../../virtual-network/virtual-network-peering-overview.md) olmalıdır. Aynı bölgedeki VNET eşlemesi üzerinden Azure NetApp Files erişim artık desteklenir. Azure NetApp erişimi genel eşleme üzerinden henüz desteklenmiyor.
- Seçilen sanal ağ, Azure NetApp Files atanmış bir alt ağa sahip olmalıdır.
- Azure NetApp Files, [dışarı aktarma ilkesi](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)sunar: izin verilen istemcileri, erişim türünü (okuma&yazma, salt okuma, vb.) denetleyebilirsiniz. 
- Azure NetApp Files Özellik henüz bölge farkında değildir. Şu anda Azure NetApp Files özelliği bir Azure bölgesindeki tüm kullanılabilirlik bölgelerinde dağıtılmaz. Bazı Azure bölgelerindeki olası gecikme etkilerine yönelik etkileri göz önünde bulundurun. 
- Azure NetApp Files birimler, NFSv3 veya NFSv 4.1 birimleri olarak dağıtılabilir. SAP uygulama katmanı (ASCS/ERS, SAP uygulama sunucuları) için her iki protokol de desteklenir. 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Linux sanal makinelerini Azure portal aracılığıyla el ile dağıtın

Öncelikle Azure NetApp Files birimleri oluşturmanız gerekir. VM 'Leri dağıtın. Daha sonra, bir yük dengeleyici oluşturur ve arka uç havuzlarındaki sanal makineleri kullanırsınız.

1. Kaynak Grubu oluşturma
1. Sanal Ağ Oluşturma
1. YOKS için bir kullanılabilirlik kümesi oluşturma  
   En fazla güncelleştirme etki alanını ayarla
1. Sanal makine oluştur 1  
   En az SLES4SAP 12 SP3 kullanın, bu örnekte SLES4SAP 12 SP3 görüntüsü kullanılır  
   Daha önce oluşturulan kullanılabilirlik kümesini seçme  
1. Sanal makine oluştur 2  
   En az SLES4SAP 12 SP3 kullanın, bu örnekte SLES4SAP 12 SP3 görüntüsü kullanılır  
   Daha önce oluşturulan kullanılabilirlik kümesini seçme  
1. SAP uygulama örnekleri (PAS, AAS) için bir kullanılabilirlik kümesi oluşturma    
   En fazla güncelleştirme etki alanını ayarla
1. Sanal makine oluştur 3  
   En az SLES4SAP 12 SP3 kullanın, bu örnekte SLES4SAP 12 SP3 görüntüsü kullanılır  
   Daha önce PAS/AAS için oluşturulan kullanılabilirlik kümesini seçin   
1. Sanal makine oluştur 4  
   En az SLES4SAP 12 SP3 kullanın, bu örnekte SLES4SAP 12 SP3 görüntüsü kullanılır  
   Daha önce PAS/AAS için oluşturulan kullanılabilirlik kümesini seçin  

## <a name="disable-id-mapping-if-using-nfsv41"></a>KIMLIK eşlemesini devre dışı bırak (NFSv 4.1 kullanılıyorsa)

Bu bölümdeki yönergeler yalnızca NFSv 4.1 protokolüyle Azure NetApp Files birimleri kullanılıyorsa geçerlidir. Azure NetApp Files NFSv 4.1 birimlerinin takılmasını gerektiren tüm VM 'lerde yapılandırmayı gerçekleştirin.  

1. NFS etki alanı ayarını doğrulayın. Etki alanının varsayılan Azure NetApp Files etki alanı olarak yapılandırıldığından emin olun, yani **`defaultv4iddomain.com`** ve eşleme **hiç kimse** olarak ayarlanmıştır.  

    > [!IMPORTANT]
    > VM 'de NFS etki alanını `/etc/idmapd.conf` Azure NetApp Files ' deki varsayılan etki alanı yapılandırmasıyla eşleşecek şekilde ayarladığınızdan emin olun: **`defaultv4iddomain.com`** . NFS istemcisindeki (yani, VM) ve NFS sunucusunun etki alanı yapılandırması arasında uyuşmazlık varsa (örneğin, Azure NetApp yapılandırması), VM 'Lere bağlı Azure NetApp birimlerinde dosya izinleri olarak görüntülenir `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** doğrula `nfs4_disable_idmapping` . **Y** olarak ayarlanmalıdır. Bulunduğu dizin yapısını oluşturmak için `nfs4_disable_idmapping` Mount komutunu yürütün. Erişim çekirdek/sürücü için ayrıldığından,/sys/modules altında dizini el ile oluşturamazsınız.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.1.0.4:/sapmnt/<b>qas</b> /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>


## <a name="setting-up-ascs"></a>(A) SCS ayarlama

Bu örnekte, kaynaklar [Azure Portal](https://portal.azure.com/#home) aracılığıyla el ile dağıtıldı.

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Azure portal aracılığıyla Azure Load Balancer el ile dağıtın

Öncelikle Azure NetApp Files birimleri oluşturmanız gerekir. VM 'Leri dağıtın. Daha sonra, bir yük dengeleyici oluşturur ve arka uç havuzundaki sanal makineleri kullanırsınız.

1. Yük dengeleyici oluşturma (iç, standart):  
   1. Ön uç IP adreslerini oluşturma
      1. YOKS için IP adresi 10.1.1.20
         1. Yük dengeleyiciyi açın, ön uç IP havuzu ' nu seçin ve Ekle ' ye tıklayın
         1. Yeni ön uç IP havuzunun adını girin (örneğin **ön uç. QAS. YOKS**)
         1. Atamayı statik olarak ayarlayın ve IP adresini girin (örneğin, **10.1.1.20**)
         1. Tamam 'a tıklayın
      1. YOKLAR için IP adresi 10.1.1.21
         * "A" altında bulunan adımları tekrarlar için bir IP adresi oluşturmak için (örneğin, **10.1.1.21** ve **ön uç). QAS. ERS**)
   1. Arka uç havuzunu oluşturma
      1. Yük dengeleyiciyi açın, arka uç havuzları ' nı seçin ve Ekle ' ye tıklayın
      1. Yeni arka uç havuzunun adını girin (örneğin, **arka uç. QAS**)
      1. Sanal makine Ekle ' ye tıklayın.
      1. Sanal makine seçin
      1. (A) SCS kümesinin sanal makinelerini ve IP adreslerini seçin.
      1. Ekle'ye tıklayın.
   1. Sistem durumu araştırmalarını oluşturma
      1. YOKS için bağlantı noktası 620 **00**
         1. Yük dengeleyiciyi açın, sistem durumu Araştırmaları ' nı seçin ve Ekle ' ye tıklayın
         1. Yeni sistem durumu araştırmasının adını (örneğin, **sistem durumu) girin. QAS. YOKS**)
         1. TCP as Protocol, bağlantı noktası 620 **00**, zaman aralığını 5 ve sağlıksız eşik 2 ' yi seçin
         1. Tamam 'a tıklayın
      1. YOKLAR için bağlantı noktası 621 **01**
            * ERS için bir sistem durumu araştırması oluşturmak için yukarıdaki adımları "c" altında yineleyin (örneğin, 621 **01** ve **sistem durumu). QAS. ERS**)
   1. Yük Dengeleme kuralları
      1. YOKS için bir arka uç havuzu oluşturun
         1. Yük dengeleyiciyi açın, Yük Dengeleme kuralları ' nı seçin ve Ekle ' ye tıklayın.
         1. Yeni yük dengeleyici kuralının adını girin (örneğin, **lb. QAS. YOKS**)
         1. Daha önce oluşturduğunuz Ass, arka uç havuzu ve sistem durumu araştırması için ön uç IP adresini seçin (örneğin **ön uç. QAS. YOKLAR**, **arka uç. QAS** ve **sağlık. QAS. YOKS**)
         1. **Ha bağlantı noktalarını** seçin
         1. **Kayan IP 'yi etkinleştirdiğinizden emin olun**
         1. Tamam 'a tıklayın
         * ÇÖZÜMLEYICILER için Yük Dengeleme kuralları oluşturmak için yukarıdaki adımları tekrarlayın (örneğin, **lb. QAS. ERS**)
1. Alternatif olarak, senaryonuz temel yük dengeleyici (iç) gerektiriyorsa, şu adımları izleyin:  
   1. Ön uç IP adreslerini oluşturma
      1. YOKS için IP adresi 10.1.1.20
         1. Yük dengeleyiciyi açın, ön uç IP havuzu ' nu seçin ve Ekle ' ye tıklayın
         1. Yeni ön uç IP havuzunun adını girin (örneğin **ön uç. QAS. YOKS**)
         1. Atamayı statik olarak ayarlayın ve IP adresini girin (örneğin, **10.1.1.20**)
         1. Tamam 'a tıklayın
      1. YOKLAR için IP adresi 10.1.1.21
         * "A" altında bulunan adımları tekrarlar için bir IP adresi oluşturmak için (örneğin, **10.1.1.21** ve **ön uç). QAS. ERS**)
   1. Arka uç havuzunu oluşturma
      1. Yük dengeleyiciyi açın, arka uç havuzları ' nı seçin ve Ekle ' ye tıklayın
      1. Yeni arka uç havuzunun adını girin (örneğin, **arka uç. QAS**)
      1. Sanal makine Ekle ' ye tıklayın.
      1. Daha önce yoks için oluşturduğunuz kullanılabilirlik kümesini seçin 
      1. (A) SCS kümesinin sanal makinelerini seçin
      1. Tamam 'a tıklayın
   1. Sistem durumu araştırmalarını oluşturma
      1. YOKS için bağlantı noktası 620 **00**
         1. Yük dengeleyiciyi açın, sistem durumu Araştırmaları ' nı seçin ve Ekle ' ye tıklayın
         1. Yeni sistem durumu araştırmasının adını (örneğin, **sistem durumu) girin. QAS. YOKS**)
         1. TCP as Protocol, bağlantı noktası 620 **00**, zaman aralığını 5 ve sağlıksız eşik 2 ' yi seçin
         1. Tamam 'a tıklayın
      1. YOKLAR için bağlantı noktası 621 **01**
            * ERS için bir sistem durumu araştırması oluşturmak için yukarıdaki adımları "c" altında yineleyin (örneğin, 621 **01** ve **sistem durumu). QAS. ERS**)
   1. Yük Dengeleme kuralları
      1. YOKS için 32 **00** TCP
         1. Yük dengeleyiciyi açın, Yük Dengeleme kuralları ' nı seçin ve Ekle ' ye tıklayın.
         1. Yeni yük dengeleyici kuralının adını girin (örneğin, **lb. QAS. YOKS. 3200**)
         1. Daha önce oluşturduğunuz Ass, arka uç havuzu ve sistem durumu araştırması için ön uç IP adresini seçin (örneğin **ön uç. QAS. YOKS**)
         1. Protokol **TCP**'yi tut, bağlantı noktası **3200** girin
         1. Boşta kalma zaman aşımını 30 dakikaya yükselt
         1. **Kayan IP 'yi etkinleştirdiğinizden emin olun**
         1. Tamam 'a tıklayın
      1. YOKS için ek bağlantı noktaları
         * 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 ve TCP bağlantı noktaları için yukarıdaki adımları "d" altında yineleyin
      1. YOKLAR için ek bağlantı noktaları
         * 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** 16 ve d/sn için TCP bağlantı noktaları için yukarıdaki adımları yineleyin

      
      > [!IMPORTANT]
      > Kayan IP, Yük Dengeleme senaryolarında NIC ikincil IP yapılandırmasında desteklenmez. Ayrıntılar için bkz. [Azure yük dengeleyici sınırlamaları](../../../load-balancer/load-balancer-multivip-overview.md#limitations). VM için ek IP adresine ihtiyacınız varsa ikinci bir NIC dağıtın.  

      > [!Note]
      > Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyicisine yerleştirildiğinde, genel uç noktalara yönlendirmeye izin vermek için ek yapılandırma gerçekleştirilmediği takdirde giden internet bağlantısı olmaz. Giden bağlantıyı elde etme hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer kullanan sanal makineler Için genel uç nokta BAĞLANTıSı SAP yüksek kullanılabilirlik senaryolarında](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

      > [!IMPORTANT]
      > Azure Load Balancer arkasına yerleştirilmiş Azure VM 'lerinde TCP zaman damgalarını etkinleştirmeyin. TCP zaman damgalarını etkinleştirmek, sistem durumu araştırmalarının başarısız olmasına neden olur. Parametre **net.ipv4.tcp_timestamps** **0** olarak ayarlayın. Ayrıntılar için bkz. [Load Balancer sistem durumu araştırmaları](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Pacemaker kümesi oluşturma

Bu (A) SCS sunucusu için temel bir Paceoluşturucu kümesi oluşturmak üzere [Azure 'daki SUSE Linux Enterprise Server Paceyapıcısı ayarlama](high-availability-guide-suse-pacemaker.md) bölümündeki adımları izleyin.

### <a name="installation"></a>Yükleme

Şu öğeler, **[A]** ön eki olan tüm düğümlere uygulanabilir, **[1]** -yalnızca düğüm 1 veya **[2]** için geçerlidir-yalnızca node 2 için geçerlidir.

1. **[A]** SUSE bağlayıcısını Install

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Ana bilgisayar adlarında tire kullanmayla ilgili bilinen sorun, **SAP-SUSE-Cluster-Connector** paketinin **3.1.1** sürümü ile düzeltilir. Küme düğümlerini ana bilgisayar adında Dash ile kullanıyorsanız SAP-SUSE-Cluster-Connector ' ın en az sürüm 3.1.1 kullandığınızdan emin olun. Aksi takdirde, kümeniz çalışmaz. 

   SAP SUSE Cluster bağlayıcısının yeni sürümünü yüklediğinizden emin olun. Eski bir tane sap_suse_cluster_connector çağrıldı ve yeni bir tane **SAP-SUSE-Cluster-Connector** olarak adlandırılır.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
    # Information for package sap-suse-cluster-connector:
    # ---------------------------------------------------
    # Repository     : SLE-12-SP3-SAP-Updates
    # Name           : sap-suse-cluster-connector
    # Version        : 3.1.0-8.1
    # Arch           : noarch
    # Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
    # Support Level  : Level 3
    # Installed Size : 45.6 KiB
    # Installed      : Yes
    # Status         : up-to-date
    # Source package : sap-suse-cluster-connector-3.1.0-8.1.src
    # Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** SAP kaynak aracılarını güncelleştirme  
   
   Kaynak aracıları paketine yönelik bir düzeltme eki, bu makalede açıklanan yeni yapılandırmayı kullanmak için gereklidir. Düzeltme ekinin aşağıdaki komutla zaten yüklenmiş olup olmadığını kontrol edebilirsiniz

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Çıktının şuna benzer olması gerekir

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   GREP komutu IS_ERS parametresini bulamazsa, [SUSE Download sayfasında](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents) listelenen düzeltme ekini yüklemeniz gerekir.

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]** kurulum konak adı çözümlemesi

   Bir DNS sunucusu kullanabilir veya tüm düğümlerdeki/etc/Konakları değiştirebilirsiniz. Bu örnek,/etc/hosts dosyasının nasıl kullanılacağını gösterir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   /Etc/hostklasörüne aşağıdaki satırları ekleyin. IP adresini ve ana bilgisayar adını ortamınıza uyacak şekilde değiştirin   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

4. **[1]** Azure NetApp FILES biriminde sap dizinleri oluşturun.  
   VM 'lerden birine Azure NetApp Files birimi geçici olarak bağlayın ve SAP dizinlerini (dosya yolları) oluşturun.  

   ```
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    # If using NFSv3
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 10.1.0.4:/sapQAS /saptmp
    # If using NFSv4.1
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 10.1.0.4:/sapQAS /saptmp
    # create the SAP directories
    sudo cd /saptmp
    sudo mkdir -p sapmntQAS
    sudo mkdir -p usrsapQASascs
    sudo mkdir -p usrsapQASers
    sudo mkdir -p usrsapQASsys
    sudo mkdir -p usrsapQASpas
    sudo mkdir -p usrsapQASaas
    # unmount the volume and delete the temporary directory
    sudo cd ..
    sudo umount /saptmp
    sudo rmdir /saptmp
    ``` 

## <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver yüklemesi için hazırlanma

1. **[A]** paylaşılan dizinler oluşturma

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]** Yapılandır `autofs`

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   NFSv3 kullanıyorsanız, şunu içeren bir dosya oluşturun:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   NFSv 4.1 kullanılıyorsa, şunu içeren bir dosya oluşturun:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   > [!NOTE]
   > Birimleri bağlama sırasında Azure NetApp Files birimlerinin NFS protokol sürümüyle eşleştiğinden emin olun. Azure NetApp Files birimleri NFSv3 birimleri olarak oluşturulduysa, ilgili NFSv3 yapılandırmasını kullanın. Azure NetApp Files birimleri NFSv 4.1 birimleri olarak oluşturulduysa, KIMLIK eşlemesini devre dışı bırakmak için yönergeleri izleyin ve karşılık gelen NFSv 4.1 yapılandırmasını kullandığınızdan emin olun. Bu örnekte Azure NetApp Files birimleri NFSv3 birimleri olarak oluşturulmuştur.  
   
   `autofs`Yeni paylaşımları bağlamak için yeniden başlatın
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]** takas dosyası yapılandırma

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

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver yoks/ERS yükleme

1. **[1]** ascs örneği için BIR sanal IP kaynağı ve sistem durumu araştırması oluşturun

   > [!IMPORTANT]
   > En son test, Netcat 'in biriktirme listesi ve yalnızca bir bağlantıyı işleme sınırlaması nedeniyle isteklere yanıt vermeyi durdurduğu ortaya çıkarılan durumlardır. Netcat kaynağı Azure yük dengeleyici isteklerini dinlemeyi durduruyor ve kayan IP kullanılamaz hale gelir.  
   > Mevcut Paceyapıcısı kümelerinde, Netcat 'i socat ile değiştirme konusunda tavsiye ederiz. Şu anda paket kaynak aracılarının bir parçası olan Azure-lb kaynak Aracısı 'nı şu paket sürümü gereksinimleriyle kullanmanızı öneririz:
   > - SLES 12 SP4/SP5 için sürüm en az Resource-Agents-4.3.018. a7fb5035-3.30.1 olmalıdır.  
   > - SLES 15/15 SP1 için sürüm en az Resource-Agents-4.3.0184.6 ee15eb2-4.13.1 olmalıdır.  
   >
   > Değişikliğin kısa kapalı kalma süresinin gerekli olacağını unutmayın.  
   > Mevcut pacemaker kümelerinde, yapılandırma [azure Load-Balancer algılama sağlamlaştırma](https://www.suse.com/support/kb/doc/?id=7024128)bölümünde açıklandığı gibi socat kullanmak üzere zaten değiştirilmişse, Azure-lb Resource Agent 'a hemen geçiş yapma gereksinimi yoktur.

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** SAP NetWeaver yoks 'yi yükler  

   Ass için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar <b>adını ve örneğin</b> <b>00</b>gibi yük dengeleyicinin araştırması için KULLANDıĞıNıZ örnek numarasını kullanarak SAP NetWeaver yoks <b>'yi ilk</b>düğümde kök olarak yükleyin.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz. Sanal konak adı kullanarak SAP 'yi yüklemek için SAPINST_USE_HOSTNAME parametresini kullanabilirsiniz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Yükleme/usr/SAP/**QAS**/ascs **00**' da bir alt klasör oluşturamazsa, Ass **00**  klasörünün sahibini ve grubunu ayarlamayı deneyin ve yeniden deneyin. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** ers örneği için BIR sanal IP kaynağı ve sistem durumu araştırması oluşturun

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS azure-lb port=621<b>01</b>
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** SAP NetWeaver iciler 'ı yükler

   SAP NetWeaver <b>ers 'ı,</b>ers için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adı ve örneğin <b>01</b>gibi yük dengeleyicinin araştırması için kullandığınız örnek <b>numarası ile ikinci</b> düğüme kök olarak yükleyin.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz. Sanal konak adı kullanarak SAP 'yi yüklemek için SAPINST_USE_HOSTNAME parametresini kullanabilirsiniz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > SWPM SP 20 PL 05 veya üzeri bir sürümü kullanın. Düşük sürümler izinleri doğru olarak ayarlamayın ve yükleme başarısız olur.

   Yükleme/usr/SAP/**QAS**/ers **01**' de bir alt klasör oluşturamazsa, ers **01** klasörünün sahibini ve grubunu ayarlamayı deneyin ve yeniden deneyin.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** yoks/SCS ve ers örnek profillerini uyarlayın
 
   * YOKS/SCS profili

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   Hem ENSA1 hem de ENSA2 için, `keepalive` işletim sistemi PARAMETRELERININ SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)' de açıklandığı gibi ayarlandığından emin olun.  

   * ERS profili

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]** canlı tutmayı yapılandırma

   SAP NetWeaver uygulama sunucusu ve yoks/SCS arasındaki iletişim, bir yazılım yük dengeleyici aracılığıyla yönlendirilir. Yük dengeleyici, yapılandırılabilir bir zaman aşımından sonra etkin olmayan bağlantıları keser. Bunu engellemek için, ENSA1 kullanıyorsanız SAP NetWeaver ASCS/SCS profilinde bir parametre ayarlamanız ve `keepalive` her IKI ENSA1/ENSA2 için tüm sap sunucularındaki Linux sistem ayarlarını değiştirmeniz gerekir. Daha fazla bilgi için [SAP Note 1410736][1410736] makalesini okuyun.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

7. **[A]** yükleme sonrasında SAP kullanıcılarını yapılandırma

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** Ass ve ers SAP hizmetlerini `sapservice` dosyaya ekleyin

   ASCS hizmeti girişini ikinci düğüme ekleyin ve ilk düğüme ERS hizmet girişini kopyalayın.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** SAP küme kaynaklarını oluşturma

Sıraya alma sunucusu 1 mimarisini (ENSA1) kullanıyorsanız, kaynakları aşağıdaki gibi tanımlayın:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   SAP, SAP NW 7,52 itibariyle çoğaltma dahil olmak üzere sıraya alma sunucusu 2 için destek sunmuştur. ABAP platform 1809 ' den başlayarak, sıraya alma sunucusu 2 varsayılan olarak yüklenir. Sıraya alma sunucusu 2 desteği için bkz. SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
Sıraya alma sunucusu 2 mimarisini ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) kullanıyorsanız, kaynakları aşağıdaki gibi tanımlayın:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Daha eski bir sürümden yükseltiyorsanız ve sıraya alma sunucusu 2 ' ye geçiş yapıyorsanız bkz. SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver uygulama sunucusu hazırlığı 

Bazı veritabanları, veritabanı örneği yüklemesinin bir uygulama sunucusunda yürütülmesini gerektirir. Uygulama sunucusu sanal makinelerini bu durumlarda kullanabilecek şekilde hazırlayın.

Bu adımlar, uygulama sunucusunu yoks/SCS ve HANA sunucularından farklı bir sunucuya yüklediğinizi varsaymaktadır. Aksi takdirde, aşağıdaki adımlardan bazıları (konak adı çözümlemesini yapılandırma gibi) gerekli değildir.

Şu öğeler için **[A]** ön eki **EKLENDI-hem** pas hem de AAS Için geçerlidir, **[P]**


1. **[A]** işletim sistemini yapılandırma

   Kirli önbelleğin boyutunu küçültün. Daha fazla bilgi için bkz. [büyük RAM Ile SLES 11/12 sunucularında düşük yazma performansı](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** kurulum konak adı çözümlemesi

   Bir DNS sunucusu kullanabilir veya tüm düğümlerdeki/etc/Konakları değiştirebilirsiniz. Bu örnek,/etc/hosts dosyasının nasıl kullanılacağını gösterir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin

   ```bash
   sudo vi /etc/hosts
   ```

   /Etc/hostklasörüne aşağıdaki satırları ekleyin. IP adresini ve ana bilgisayar adını ortamınıza uyacak şekilde değiştirin

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]** sapmnt dizinini oluşturma

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** pas dizinini oluşturma

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** AAS dizinini oluşturma

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** `autofs` pas üzerinde yapılandırma

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   NFSv3 kullanıyorsanız, ile yeni bir dosya oluşturun:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   NFSv 4.1 kullanılıyorsa, ile yeni bir dosya oluşturun:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   `autofs`Yeni paylaşımları bağlamak için yeniden başlatın

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** `autofs` AAS üzerinde yapılandırma

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   NFSv3 kullanıyorsanız, ile yeni bir dosya oluşturun:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   NFSv 4.1 kullanılıyorsa, ile yeni bir dosya oluşturun:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   `autofs`Yeni paylaşımları bağlamak için yeniden başlatın

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** takas dosyası yapılandırma

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

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Veritabanını yükleme

Bu örnekte, SAP HANA SAP NetWeaver yüklüdür. Bu yükleme için desteklenen her veritabanını kullanabilirsiniz. SAP HANA Azure 'da nasıl yükleyeceğiniz hakkında daha fazla bilgi için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]. Desteklenen veritabanlarının listesi için bkz. [SAP Note 1928533][1928533].

* SAP veritabanı örnek yüklemesini çalıştırma

   SAP NetWeaver veritabanı örneğini, veritabanı için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adını kullanarak kök olarak yükleyin.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver uygulama sunucusu yüklemesi

SAP uygulama sunucusu yüklemek için aşağıdaki adımları izleyin.

1. **[A]** uygulama sunucusunu hazırlama uygulama sunucusunu hazırlamak için yukarıdaki Bölüm [SAP NetWeaver uygulama sunucusu hazırlama](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) bölümündeki adımları izleyin.

2. **[A]** SAP NetWeaver uygulama sunucusu yüklemesi birincil veya ek SAP NetWeaver uygulamaları sunucusu yükler.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** güvenli depolama SAP HANA güncelleştirme

   SAP HANA güvenli mağazayı, SAP HANA sistem çoğaltması kurulumunun sanal adını gösterecek şekilde güncelleştirin.

   Girişleri listelemek için aşağıdaki komutu çalıştırın
   <pre><code>
   hdbuserstore List
   </code></pre>

   Bu, tüm girdileri listelemelidir ve şuna benzer görünmelidir
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   Çıktı, varsayılan girdinin IP adresinin, yük dengeleyicinin IP adresine değil, sanal makineye işaret ettiği gösterir. Bu girdinin, yük dengeleyicinin sanal ana bilgisayar adına işaret eden şekilde değiştirilmesi gerekir. Aynı bağlantı noktasını (yukarıdaki çıktıda **30313** ) ve veritabanı adını (yukarıdaki çıktıda **QAS** ) kullandığınızdan emin olun!

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Küme kurulumunu test etme

Aşağıdaki testler, [SUSE 'in en iyi yöntemler kılavuzlarındaki][suse-ha-guide]test çalışmalarının bir kopyasıdır. Kolaylık olması için bunlar kopyalanırlar. Ayrıca en iyi yöntem kılavuzlarını okuyun ve eklenmiş olabilecek tüm ek testleri gerçekleştirin.

1. Test HAGetFailoverConfig, HACheckConfig ve HACheckFailoverConfig

   \<sapsid>ASCS örneğinin Şu anda çalıştırıldığı düğümde aşağıdaki komutları adm olarak çalıştırın. Komutlar hata vererek başarısız olursa, bu durum, ana bilgisayar bilgisayarınızdaki çizgilerden kaynaklanıyor olabilir. Bu bilinen bir sorundur ve SAP-SUSE-Cluster-Connector paketindeki SUSE tarafından düzeltilecektir.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. ASCS örneğini el ile geçirme

   Teste başlamadan önce kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   ASCS örneğini geçirmek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. Test HAFailoverToNode

   Teste başlamadan önce kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   \<sapsid>ASCS örneğini geçirmek için aşağıdaki komutları adm olarak çalıştırın.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Düğüm kilitlenmesinin benzetimini yap 

   Teste başlamadan önce kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   ASCS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutu çalıştırın

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   SBD kullanırsanız, pacemaker, sonlandırılan düğümde otomatik olarak başlamamalıdır. Düğüm yeniden başlatıldıktan sonra durum şöyle görünmelidir.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Sonlandırılan düğümde pacemaker ' ı başlatmak, SBD iletilerini temizlemek ve hatalı kaynakları temizlemek için aşağıdaki komutları kullanın.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Ass örneğinin el ile yeniden başlatılmasını test etme

   Teste başlamadan önce kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Bir sıraya alma kilidi oluşturun, örneğin işlem su01 içinde bir Kullanıcı düzenleyin. \>ASCS örneğinin çalıştırıldığı düğüm üzerinde <sapsid adm olarak aşağıdaki komutları çalıştırın. Komutlar, yoks örneğini durdurur ve yeniden başlatır. Sıraya alma sunucusu 1 mimarisi kullanılıyorsa, bu testte sıraya alma kilidinin kaybedilmesi beklenir. Sıraya alma sunucusu 2 mimarisi kullanılıyorsa, sıraya alma bekletilecektir. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS örneği şimdi pacemaker 'da devre dışı bırakılmalıdır

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   ASCS örneğini aynı düğümde yeniden başlatın.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Sıraya alınmış sunucu çoğaltma 1 mimarisi kullanılıyorsa ve arka ucun sıfırlanması gerekiyorsa, işlem su01 sıraya alma kilidi 'nin kaybedilmesi gerekir. Testten sonra kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. İleti sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   İleti sunucusu sürecini belirlemek ve sonlandırmak için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   İleti sunucusunu yalnızca bir kez sonlandırdıysanız, tarafından yeniden başlatılır `sapstart` . Bunu yeterince fazla sonlandırdıysanız, Paceyapıcısı sonunda yoks örneğini diğer düğüme taşıyacaktır. Testten sonra Ass ve ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Sıraya alma sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Sıraya alma sunucusunu sonlandırmak için ASCS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutları çalıştırın.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   ASCS örneği hemen diğer düğüme yük devreder. Ass örneği başlatıldıktan sonra, ERS örneği de yük devreder. Testten sonra Ass ve ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Sıraya alma çoğaltma sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Sıraya alma çoğaltma sunucusu işlemini sonlandırmak için ERS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutu çalıştırın.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Komutu yalnızca bir kez çalıştırırsanız, `sapstart` işlem yeniden başlatılır. Bunu yeterince sık çalıştırırsanız, `sapstart` işlem yeniden başlatmaz ve kaynak durdurulmuş durumda olur. Testten sonra ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Sıraya alma sapstartsrv işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   ASCS 'nin çalıştığı düğümde kök olarak aşağıdaki komutları çalıştırın.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Sapstartsrv işlemi, pacemaker kaynak Aracısı tarafından her zaman yeniden başlatılmalıdır. Testten sonra kaynak durumu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Sonraki adımlar

* [SLES for SAP için Azure VM 'lerde bir HA for SAP NW çoklu SID Kılavuzu](./high-availability-guide-suse-multi-sid.md)
* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan planı oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]