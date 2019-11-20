---
title: Azure NetApp Files ile Red Hat Enterprise Linux on SAP NetWeaver için Azure sanal makineler yüksek kullanılabilirliği | Microsoft Docs
description: Red Hat Enterprise Linux SAP NetWeaver için Azure sanal makineleri yüksek kullanılabilirliği
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: radeltch
ms.openlocfilehash: ba8dc3080f3b584ae3a60576e4cc670dc60c28a0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151815"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>SAP NetWeaver için Azure sanal makineler yüksek kullanılabilirlik, SAP uygulamaları için Azure NetApp Files Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

Bu makalede, [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)kullanarak sanal makinelerin nasıl dağıtılacağı, sanal makinelerin nasıl yapılandırılacağı, küme çerçevesinin nasıl yükleneceği ve yüksek oranda KULLANILABILIR bir SAP NetWeaver 7,50 sisteminin nasıl yükleneceği açıklanır.
Örnek yapılandırmalarda, yükleme komutları vb. ASCS örneği 00, ERS örneği 01, birincil uygulama örneği (PAS) 02 ve uygulama örneği (AAS) 03 ' dir. SAP sistem KIMLIĞI QAS kullanılır. 

Veritabanı katmanı Bu makalede ayrıntılı olarak ele alınmıyor.  

Önce aşağıdaki SAP notlarını ve kağıtları okuyun:

* [Azure NetApp Files belgeleri][anf-azure-doc] 
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
* [Microsoft Azure Azure NetApp Files kullanarak NetApp SAP uygulamaları][anf-sap-applications-azure]

## <a name="overview"></a>Genel Bakış

SAP NetWeaver Merkezi Hizmetleri için yüksek kullanılabilirlik (HA), paylaşılan depolama gerektirir.
Şimdiye kadar Red Hat Linux 'ta bu işlemi gerçekleştirmek için yüksek oranda kullanılabilir GlusterFS kümesi oluşturulması gerekir. 

Artık Azure NetApp Files dağıtılan paylaşılan depolamayı kullanarak SAP NetWeaver HA elde etmek mümkündür. Paylaşılan depolama için Azure NetApp Files kullanmak, ek [GlusterFS kümesi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)gereksinimini ortadan kaldırır. SAP NetWeaver Merkezi Hizmetleri (yoks/SCS) HA için pacemaker hala gereklidir.

![SAP NetWeaver yüksek kullanılabilirliğe genel bakış](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver yoks, SAP NetWeaver SCS, SAP NetWeaver ERS ve SAP HANA veritabanı sanal konak adı ve sanal IP adreslerini kullanır. Azure 'da bir sanal IP adresi kullanmak için bir yük dengeleyici gereklidir. [Standart yük dengeleyici](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)kullanmanızı öneririz. Aşağıdaki listede, (A) SCS ve ERS için ayrı ön uç IP 'Leri ile yük dengeleyicinin yapılandırması gösterilmektedir.

> [!IMPORTANT]
> Azure VM 'lerinde Konuk işletim sistemi olarak Red Hat Linux içeren SAP yoks/ERS 'in çoklu SID Kümelemesi **desteklenmez**. Çoklu SID Kümelemesi, tek bir Paceoluşturucu kümesinde farklı SID 'Leri olan birden fazla SAP ASCS/ERS örneğinin yüklenmesini açıklar.

### <a name="ascs"></a>(A)SCS

* Ön uç yapılandırması
  * IP adresi 192.168.14.9
* Arka uç yapılandırması
  * (A) SCS/ERS kümesinin parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
* Araştırma bağlantı noktası
  * Bağlantı noktası 620<strong>&lt;nr&gt;</strong>
* Yük Dengeleme kuralları
  * Standart Load Balancer kullanıyorsanız **ha bağlantı noktaları** ' nı seçin.
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>SÖZCÜKLERI

* Ön uç yapılandırması
  * IP adresi 192.168.14.10
* Arka uç yapılandırması
  * (A) SCS/ERS kümesinin parçası olması gereken tüm sanal makinelerin birincil ağ arabirimlerine bağlanıldı
* Araştırma bağlantı noktası
  * Bağlantı noktası 621<strong>&lt;nr&gt;</strong>
* Yük Dengeleme kuralları
  * Standart Load Balancer kullanıyorsanız **ha bağlantı noktaları** ' nı seçin.
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files altyapısını ayarlama 

SAP NetWeaver, aktarım ve profil dizini için paylaşılan depolama gerektirir.  Azure NetApp dosyaları altyapısına yönelik kuruluma devam etmeden önce [Azure NetApp Files belgelerini inceleyin][anf-azure-doc]. Seçtiğiniz Azure bölgesinin Azure NetApp Files sunmadığını denetleyin. Aşağıdaki bağlantıda Azure bölgesine göre Azure NetApp Files kullanılabilirliği gösterilmektedir: [Azure bölgesine göre Azure NetApp Files kullanılabilirliği][anf-avail-matrix].

Azure NetApp dosyaları çeşitli [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)kullanılabilir. Azure NetApp Files dağıtılmadan önce, [Azure NetApp dosyaları Için kaydolmayı][anf-register]izleyerek Azure NetApp Files ekleme isteği yapın. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files kaynaklarını dağıtma  

Adımlarda zaten [Azure sanal ağını](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)dağıttığınız varsayılmaktadır. Azure NetApp Files kaynaklarının bağlanmasının gerektiği Azure NetApp Files kaynakları ve VM 'Ler aynı Azure sanal ağında veya eşlenmiş Azure sanal ağlarında dağıtılmalıdır.  

1. Henüz yapmadıysanız, [Azure NetApp Files ekleme](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)isteyin.  
2. [Newtapp hesabını oluşturma yönergelerini](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)izleyerek, seçili Azure bölgesinde NetApp hesabını oluşturun.  
3. [Azure NetApp Files kapasite havuzunu ayarlama yönergelerini](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)izleyerek Azure NetApp Files kapasite havuzunu ayarlayın.  
Bu makalede sunulan SAP NetWeaver mimarisi, tek Azure NetApp Files kapasite havuzu, Premium SKU kullanır. Azure 'da SAP NetWeaver uygulama iş yükü için Premium SKU Azure NetApp Files önerilir.  
4. [Azure NetApp Files için bir alt ağ temsilcisine yönelik yönergeler](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)bölümünde açıklandığı gibi Azure NetApp dosyalarına bir alt ağ atayın.  

5. [Azure NetApp Files için bir birim oluşturma yönergelerini](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)izleyerek Azure NetApp Files birimleri dağıtın. Birimleri belirlenen Azure NetApp Files [alt ağına](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)dağıtın. Azure NetApp Files kaynaklarının ve Azure VM 'lerinin aynı Azure sanal ağında veya eşlenmiş Azure sanal ağlarında olması gerektiğini unutmayın. Bu örnekte iki Azure NetApp Files birimi kullanıyoruz: SAP<b>QAS</b> ve transsap. Karşılık gelen bağlama noktalarına bağlanan dosya yolları/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS/usrsap</b><b>QAS</b>sys, vb. ' dir.  

   1. toplu SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/Sapmnt<b>QAS</b>)
   2. toplu SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ascs)
   3. toplu SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. toplu SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ERS)
   5. birim transSAP (nfs://192.168.24.4/transSAP)
   6. toplu SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas)
   7. toplu SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)
  
Bu örnekte, Azure NetApp Files nasıl kullanılabileceğini göstermek üzere tüm SAP NetWeaver dosya sistemleri için Azure NetApp Files kullandık. NFS aracılığıyla bağlanması gerekmeyen SAP dosya sistemleri de [Azure disk depolama](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) olarak dağıtılabilir. Bu örnekte <b>bir-e</b> 'nin Azure NetApp Files olması gerekir ve <b>f-g</b> (diğer bir deyişle,/usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) Azure disk depolama olarak dağıtılabilir. 

### <a name="important-considerations"></a>Önemli konular

SUSE yüksek kullanılabilirlik mimarisinde SAP NetWeaver için Azure NetApp Files düşünürken, aşağıdaki önemli noktalara dikkat edin:

- En düşük kapasite havuzu 4 TiB 'dir. Kapasite havuzu boyutu 1 TiB 'lik artışlarla artırılabilir.
- Minimum birim 100 GiB 'dir
- Azure NetApp Files ve Azure NetApp Files birimlerinin takılabileceği tüm sanal makineler aynı bölgedeki aynı Azure sanal ağında veya eşlenmiş [sanal ağlarda](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) olmalıdır. Aynı bölgedeki VNET eşlemesi üzerinden Azure NetApp Files erişim artık desteklenir. Azure NetApp erişimi genel eşleme üzerinden henüz desteklenmiyor.
- Seçilen sanal ağ, Azure NetApp Files atanmış bir alt ağa sahip olmalıdır.
- Azure NetApp Files, [dışarı aktarma ilkesi](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)sunar: izin verilen istemcileri, erişim türünü (okuma & yazma, salt okuma, vb.) denetleyebilirsiniz. 
- Azure NetApp Files Özellik henüz bölge farkında değildir. Şu anda Azure NetApp Files özelliği bir Azure bölgesindeki tüm kullanılabilirlik bölgelerinde dağıtılmaz. Bazı Azure bölgelerindeki olası gecikme etkilerine yönelik etkileri göz önünde bulundurun. 

## <a name="setting-up-ascs"></a>(A) SCS ayarlama

Bu örnekte, kaynaklar [Azure Portal](https://portal.azure.com/#home)aracılığıyla el ile dağıtıldı.

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux 'u Azure portal aracılığıyla el ile dağıtın

Öncelikle Azure NetApp Files birimleri oluşturmanız gerekir. VM 'Leri dağıtın. Daha sonra, bir yük dengeleyici oluşturur ve arka uç havuzlarındaki sanal makineleri kullanırsınız.

1. Yük dengeleyici oluşturma (iç, standart):  
   1. Ön uç IP adreslerini oluşturma
      1. YOKS için IP adresi 192.168.14.9
         1. Yük dengeleyiciyi açın, ön uç IP havuzu ' nu seçin ve Ekle ' ye tıklayın
         1. Yeni ön uç IP havuzunun adını girin (örneğin **ön uç. QAS. YOKS**)
         1. Atamayı statik olarak ayarlayın ve IP adresini girin (örneğin, **192.168.14.9**)
         1. Tamam 'a tıklayın
      1. YOKLAR için IP adresi 192.168.14.10
         * "A" altında bulunan adımları tekrarlar için bir IP adresi oluşturmak için (örneğin, **192.168.14.10** ve **ön uç). QAS. ERS**)
   1. Arka uç havuzlarını oluşturma
      1. YOKS için bir arka uç havuzu oluşturun
         1. Yük dengeleyiciyi açın, arka uç havuzları ' nı seçin ve Ekle ' ye tıklayın
         1. Yeni arka uç havuzunun adını girin (örneğin, **arka uç. QAS**)
         1. Sanal makine Ekle ' ye tıklayın.
         1. Sanal makineyi seçin. 
         1. (A) SCS kümesinin sanal makinelerini ve IP adreslerini seçin.
         1. Ekle'ye tıklayın.
   1. Sistem durumu araştırmalarını oluşturma
      1. YOKS için bağlantı noktası 620**00**
         1. Yük dengeleyiciyi açın, sistem durumu Araştırmaları ' nı seçin ve Ekle ' ye tıklayın
         1. Yeni sistem durumu araştırmasının adını (örneğin, **sistem durumu) girin. QAS. YOKS**)
         1. TCP as Protocol, bağlantı noktası 620**00**, zaman aralığını 5 ve sağlıksız eşik 2 ' yi seçin
         1. Tamam 'a tıklayın
      1. YOKLAR için bağlantı noktası 621**01**
            * ERS için bir sistem durumu araştırması oluşturmak için yukarıdaki adımları "c" altında yineleyin (örneğin, 621**01** ve **sistem durumu). QAS. ERS**)
   1. Yük Dengeleme kuralları
      1. YOKS için Yük Dengeleme kuralları
         1. Yük dengeleyiciyi açın, Yük Dengeleme kuralları ' nı seçin ve Ekle ' ye tıklayın.
         1. Yeni yük dengeleyici kuralının adını girin (örneğin, **lb. QAS. YOKS**)
         1. Daha önce oluşturduğunuz Ass, arka uç havuzu ve sistem durumu araştırması için ön uç IP adresini seçin (örneğin **ön uç. QAS. YOKLAR**, **arka uç. QAS** ve **sağlık. QAS. YOKS**)
         1. **Ha bağlantı noktalarını** seçin
         1. Boşta kalma zaman aşımını 30 dakikaya yükselt
         1. **Kayan IP 'yi etkinleştirdiğinizden emin olun**
         1. Tamam 'a tıklayın
         * ÇÖZÜMLEYICILER için Yük Dengeleme kuralları oluşturmak için yukarıdaki adımları tekrarlayın (örneğin, **lb. QAS. ERS**)
1. Alternatif olarak, senaryonuz temel yük dengeleyici (iç) gerektiriyorsa, şu adımları izleyin:  
   1. Ön uç IP adreslerini oluşturma
      1. YOKS için IP adresi 192.168.14.9
         1. Yük dengeleyiciyi açın, ön uç IP havuzu ' nu seçin ve Ekle ' ye tıklayın
         1. Yeni ön uç IP havuzunun adını girin (örneğin **ön uç. QAS. YOKS**)
         1. Atamayı statik olarak ayarlayın ve IP adresini girin (örneğin, **192.168.14.9**)
         1. Tamam 'a tıklayın
      1. YOKLAR için IP adresi 192.168.14.10
         * "A" altında bulunan adımları tekrarlar için bir IP adresi oluşturmak için (örneğin, **192.168.14.10** ve **ön uç). QAS. ERS**)
   1. Arka uç havuzlarını oluşturma
      1. YOKS için bir arka uç havuzu oluşturun
         1. Yük dengeleyiciyi açın, arka uç havuzları ' nı seçin ve Ekle ' ye tıklayın
         1. Yeni arka uç havuzunun adını girin (örneğin, **arka uç. QAS**)
         1. Sanal makine Ekle ' ye tıklayın.
         1. Daha önce yoks için oluşturduğunuz kullanılabilirlik kümesini seçin 
         1. (A) SCS kümesinin sanal makinelerini seçin
         1. Tamam 'a tıklayın
   1. Sistem durumu araştırmalarını oluşturma
      1. YOKS için bağlantı noktası 620**00**
         1. Yük dengeleyiciyi açın, sistem durumu Araştırmaları ' nı seçin ve Ekle ' ye tıklayın
         1. Yeni sistem durumu araştırmasının adını (örneğin, **sistem durumu) girin. QAS. YOKS**)
         1. TCP as Protocol, bağlantı noktası 620**00**, zaman aralığını 5 ve sağlıksız eşik 2 ' yi seçin
         1. Tamam 'a tıklayın
      1. YOKLAR için bağlantı noktası 621**01**
            * ERS için bir sistem durumu araştırması oluşturmak için yukarıdaki adımları "c" altında yineleyin (örneğin, 621**01** ve **sistem durumu). QAS. ERS**)
   1. Yük Dengeleme kuralları
      1. YOKS için 32**00** TCP
         1. Yük dengeleyiciyi açın, Yük Dengeleme kuralları ' nı seçin ve Ekle ' ye tıklayın.
         1. Yeni yük dengeleyici kuralının adını girin (örneğin, **lb. QAS. YOKS. 3200**)
         1. Daha önce oluşturduğunuz Ass, arka uç havuzu ve sistem durumu araştırması için ön uç IP adresini seçin (örneğin **ön uç. QAS. YOKS**)
         1. Protokol **TCP**'yi tut, bağlantı noktası **3200** girin
         1. Boşta kalma zaman aşımını 30 dakikaya yükselt
         1. **Kayan IP 'yi etkinleştirdiğinizden emin olun**
         1. Tamam 'a tıklayın
      1. YOKS için ek bağlantı noktaları
         * 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 ve TCP bağlantı noktaları için yukarıdaki adımları "d" altında yineleyin
      1. YOKLAR için ek bağlantı noktaları
         * 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 ve d/sn için TCP bağlantı noktaları için yukarıdaki adımları yineleyin

> [!Note]
> Ortak IP adresleri olmayan VM 'Ler, iç (genel IP adresi olmayan) standart Azure yük dengeleyicisine yerleştirildiğinde, genel uç noktalara yönlendirmeye izin vermek için ek yapılandırma gerçekleştirilmediği takdirde giden internet bağlantısı olmaz. Giden bağlantıyı elde etme hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer kullanan sanal makineler Için genel uç nokta BAĞLANTıSı SAP yüksek kullanılabilirlik senaryolarında](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Azure Load Balancer arkasına yerleştirilmiş Azure VM 'lerinde TCP zaman damgalarını etkinleştirmeyin. TCP zaman damgalarını etkinleştirmek, sistem durumu araştırmalarının başarısız olmasına neden olur. **Net. IPv4. tcp_timestamps** parametresini **0**olarak ayarlayın. Ayrıntılar için bkz. [Load Balancer sistem durumu araştırmaları](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Pacemaker kümesi oluşturma

Bu (A) SCS sunucusu için temel bir Paceoluşturucu kümesi oluşturmak üzere [Azure 'daki Red Hat Enterprise Linux Paceyapıcısı ayarlama](high-availability-guide-rhel-pacemaker.md) bölümündeki adımları izleyin.

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver yüklemesi için hazırlanma

Aşağıdaki öğeler ile önek **[A]** - tüm düğümler için geçerli **[1]** - düğüm 1 yalnızca uygulanabilir veya **[2]** - yalnızca düğüm 2 için geçerlidir.

1. **[A]**  Kurulum ana bilgisayar adı çözümlemesi

   Bir DNS sunucusu kullanabilir veya/etc/hosts tüm düğümlerde değiştirin. Bu örnek/Etc/Hosts dosyasının nasıl kullanılacağını gösterir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin

    ```
    sudo vi /etc/hosts
    ```

   / Etc/hosts aşağıdaki satırları ekleyin. IP adresi ve ana bilgisayar adını, ortamınızla eşleşecek şekilde değiştirin.

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** Azure NetApp FILES biriminde sap dizinleri oluşturun.  
   VM 'lerden birine Azure NetApp Files birimi geçici olarak bağlayın ve SAP dizinlerini (dosya yolları) oluşturun.  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
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

1. **[A]** paylaşılan dizinler oluşturma

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** NFS istemcisini ve diğer gereksinimleri yükler

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** kaynak-aracıların denetim sürümü-SAP

   Yüklü kaynak-Agents-SAP paketinin sürümünün en az 3.9.5 -124. EL7 olduğundan emin olun
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** bağlama girdileri ekleme

   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   > [!NOTE]
   > Birimleri bağlama sırasında Azure NetApp Files birimlerinin NFS protokol sürümüyle eşleştiğinden emin olun. Bu örnekte Azure NetApp Files birimleri NFSv3 birimleri olarak oluşturulmuştur.  

   Yeni paylaşımları bağlama

   ```
   sudo mount -a  
   ```

1. **[A]** takas dosyası yapılandırma

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Değişikliği etkinleştirmek için aracıyı yeniden başlatın

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL yapılandırması

   SAP Note [2002167] ' de açıklandığı gibi RHEL 'yi yapılandırma

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver yoks/ERS yükleme

1. **[1]** ascs örneği için BIR sanal IP kaynağı ve sistem durumu araştırması oluşturun

   ```
   sudo pcs node standby anftstsapcl2
   
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** SAP NetWeaver yoks 'yi yükler  

   Ass için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adını kullanarak SAP NetWeaver yoks 'yi ilk düğümde kök olarak yükleyin; Örneğin, <b>anftstsapvh</b>, <b>192.168.14.9</b> ve için kullandığınız örnek numarası yük dengeleyici araştırması, örneğin <b>00</b>.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Yükleme/usr/SAP/**QAS**/ascs**00**' da bir alt klasör oluşturamazsa, Ass**00** klasörünün sahibini ve grubunu ayarlamayı deneyin ve yeniden deneyin.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** ers örneği için BIR sanal IP kaynağı ve sistem durumu araştırması oluşturun

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' \
    --group g-QAS_AERS

   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** SAP NetWeaver iciler 'ı yükler  

   SAP NetWeaver ERS 'ı, ERS için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adı, örneğin <b>anftstsa,</b> <b>192.168.14.10</b> ve için kullandığınız örnek numarası ile yük dengeleyici araştırması, örneğin <b>01</b>.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Yükleme/usr/SAP/**QAS**/ers**01**' de bir alt klasör oluşturamazsa, ers**01** klasörünün sahibini ve grubunu ayarlamayı deneyin ve yeniden deneyin.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** yoks/SCS ve ers örnek profillerini uyarlayın

   * YOKS/SCS profili

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * ERS profili

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** canlı tutmayı yapılandırma

   SAP NetWeaver uygulama sunucusu ve yoks/SCS arasındaki iletişim, bir yazılım yük dengeleyici aracılığıyla yönlendirilir. Yük dengeleyici, yapılandırılabilir bir zaman aşımından sonra etkin olmayan bağlantıları keser. Bunu engellemek için SAP NetWeaver ASCS/SCS profilinde bir parametre ayarlamanız ve Linux sistem ayarlarını değiştirmeniz gerekir. Daha fazla bilgi için [SAP Note 1410736][1410736] makalesini okuyun.

   Ass/SCS profil parametresi EnQue/encnı/set_so_keepalive son adımla zaten eklendi.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** /usr/SAP/sapservices dosyasını güncelleştirme

   Sapinit başlangıç betiğinin örneklerinin başlamasını engellemek için, pacemaker tarafından yönetilen tüm örneklere/usr/SAP/sapservices dosyasından açıklama eklenmelidir. HANA SR ile birlikte kullanılacaksa SAP HANA örneğini açıklama olarak kullanmayın.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** SAP küme kaynaklarını oluşturma

   Sıraya alma sunucusu 1 mimarisini (ENSA1) kullanıyorsanız, kaynakları aşağıdaki gibi tanımlayın:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP, SAP NW 7,52 itibariyle çoğaltma dahil olmak üzere sıraya alma sunucusu 2 için destek sunmuştur. ABAP platform 1809 ' den başlayarak, sıraya alma sunucusu 2 varsayılan olarak yüklenir. Sıraya alma sunucusu 2 desteği için bkz. SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
   Sıraya alma sunucusu 2 mimarisini ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) kullanıyorsanız, Resource Agent Resource-Agents-sap-4.1.1 -12. el7. x86_64 veya sonraki bir sürümünü yükleyip kaynakları aşağıdaki gibi tanımlayın:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Daha eski bir sürümden yükseltiyorsanız ve sıraya alma sunucusu 2 ' ye geçiş yapıyorsanız bkz. SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** her iki düğümdeki yoks ve ers için güvenlik duvarı kuralları ekleme her iki düğümdeki yoks ve ers için güvenlik duvarı kuralları ekleyin.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver uygulama sunucusu hazırlığı

   Bazı veritabanları, veritabanı örneği yüklemesinin bir uygulama sunucusunda yürütülmesini gerektirir. Uygulama sunucusu sanal makinelerini bu durumlarda kullanabilecek şekilde hazırlayın.  

   Bu adımlar, uygulama sunucusunu yoks/SCS ve HANA sunucularından farklı bir sunucuya yüklediğinizi varsaymaktadır. Aksi takdirde, aşağıdaki adımlardan bazıları (konak adı çözümlemesini yapılandırma gibi) gerekli değildir.  

   Şu öğeler için **[A]** ön eki **EKLENDI-hem** pas hem de AAS Için geçerlidir, **[P]**  

1. **[A]** kurulum konak adı ÇÖZÜMLEMESI bir DNS sunucusu kullanabilir ya da tüm düğümlerdeki/etc/konaklarınızı değiştirebilirsiniz. Bu örnek/Etc/Hosts dosyasının nasıl kullanılacağını gösterir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin:  

   ```
   sudo vi /etc/hosts
   ```

   / Etc/hosts aşağıdaki satırları ekleyin. IP adresini ve ana bilgisayar adını ortamınızla eşleşecek şekilde değiştirin.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** sapmnt dizinini oluşturma sapmnt dizinini oluşturun.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** NFS istemcisini ve diğer gereksinimleri yükler  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** bağlama girdileri ekleme  

   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Yeni paylaşımları bağlama

   ```
   sudo mount -a
   ```

1. **[P]** pas dizinini oluşturma ve bağlama  

   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

1. **[S]** AAS dizinini oluşturma ve bağlama  

   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```


1. **[A]** takas dosyası yapılandırma
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Değişikliği etkinleştirmek için aracıyı yeniden başlatın

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Veritabanını yükler

Bu örnekte, SAP HANA SAP NetWeaver yüklüdür. Bu yükleme için desteklenen her veritabanını kullanabilirsiniz. SAP HANA Azure 'da nasıl yükleyeceğiniz hakkında daha fazla bilgi için Red Hat Enterprise Linux. For a list of supported databases, see [SAP Note 1928533][1928533][Azure VM 'lerinde SAP HANA yüksek kullanılabilirlik][sap-hana-ha] bölümüne bakın.

1. SAP veritabanı örnek yüklemesini çalıştırma

   SAP NetWeaver veritabanı örneğini, veritabanı için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adını kullanarak kök olarak yükleyin.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver uygulama sunucusu yüklemesi

SAP uygulama sunucusu yüklemek için aşağıdaki adımları izleyin.

1. Uygulama sunucusunu hazırla

   Uygulama sunucusunu hazırlamak için yukarıdaki Bölüm [SAP NetWeaver uygulama sunucusu hazırlama](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) bölümündeki adımları izleyin.

1. SAP NetWeaver uygulama sunucusunu yükler

   Birincil veya ek SAP NetWeaver uygulamaları sunucusu yükler.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. SAP HANA güvenli depoyu Güncelleştir

   SAP HANA güvenli mağazayı, SAP HANA sistem çoğaltması kurulumunun sanal adını gösterecek şekilde güncelleştirin.

   Girdileri \<sapsıd > adm olarak listelemek için aşağıdaki komutu çalıştırın

   ```
   hdbuserstore List
   ```

   Bu, tüm girdileri listelemelidir ve şuna benzer görünmelidir
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   Çıktı, varsayılan girdinin IP adresinin, yük dengeleyicinin IP adresine değil, sanal makineye işaret ettiği gösterir. Bu girdinin, yük dengeleyicinin sanal ana bilgisayar adına işaret eden şekilde değiştirilmesi gerekir. Aynı bağlantı noktasını (yukarıdaki çıktıda**30313** ) ve veritabanı adını (yukarıdaki çıktıda**QAS** ) kullandığınızdan emin olun!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Küme kurulumunu test etme

1. ASCS örneğini el ile geçirme

   Teste başlamadan önce kaynak durumu:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   ASCS örneğini geçirmek için aşağıdaki komutları kök olarak çalıştırın.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Testten sonra kaynak durumu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Düğüm kilitlenmesinin benzetimini yap

   Teste başlamadan önce kaynak durumu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   ASCS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutu çalıştırın

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   Düğüm yeniden başlatıldıktan sonra durum şöyle görünmelidir.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Hatalı kaynakları temizlemek için aşağıdaki komutu kullanın.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Testten sonra kaynak durumu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. İleti sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   İleti sunucusu sürecini belirlemek ve sonlandırmak için aşağıdaki komutları kök olarak çalıştırın.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   İleti sunucusunu yalnızca bir kez sonlandırdıysanız, `sapstart`tarafından yeniden başlatılır. Bunu yeterince fazla sonlandırdıysanız, Paceyapıcısı sonunda yoks örneğini diğer düğüme taşıyacaktır. Testten sonra Ass ve ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Testten sonra kaynak durumu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Sıraya alma sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Sıraya alma sunucusunu sonlandırmak için ASCS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutları çalıştırın.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   ASCS örneği hemen diğer düğüme yük devreder. Ass örneği başlatıldıktan sonra, ERS örneği de yük devreder. Testten sonra Ass ve ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Testten sonra kaynak durumu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Sıraya alma çoğaltma sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Sıraya alma çoğaltma sunucusu işlemini sonlandırmak için ERS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutu çalıştırın.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Komutu yalnızca bir kez çalıştırırsanız `sapstart` işlem yeniden başlatılır. Bunu yeterince sık çalıştırırsanız, `sapstart` işlemi yeniden başlatmaz ve kaynak durdurulmuş durumda olur. Testten sonra ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Testten sonra kaynak durumu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Sıraya alma sapstartsrv işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   ASCS 'nin çalıştığı düğümde kök olarak aşağıdaki komutları çalıştırın.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Sapstartsrv işlemi, izlemenin bir parçası olarak her zaman pacemaker kaynak Aracısı tarafından yeniden başlatılmalıdır. Testten sonra kaynak durumu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* Azure 'da SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan (büyük örnekler) oluşturma hakkında bilgi edinmek için bkz. [Azure 'da SAP HANA (büyük örnekler) yüksek kullanılabilirlik ve olağanüstü durum kurtarma](hana-overview-high-availability-disaster-recovery.md).
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan planı oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]
