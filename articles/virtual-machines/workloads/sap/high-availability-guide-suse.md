---
title: SAP uygulamaları için SUSE Linux Enterprise Server on SAP NetWeaver için Azure sanal makineler yüksek kullanılabilirliği | Microsoft Docs
description: SAP uygulamaları için SUSE Linux Enterprise Server SAP NetWeaver için yüksek kullanılabilirlik Kılavuzu
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: sedusch
ms.openlocfilehash: d08f17bd22188f3d969261d8626d47a9e0faf08e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839603"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>SAP uygulamaları için SUSE Linux Enterprise Server Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik

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

Bu makalede, sanal makinelerin nasıl dağıtılacağı, sanal makinelerin nasıl yapılandırılacağı, küme çerçevesinin nasıl yükleneceği ve yüksek oranda kullanılabilir bir SAP NetWeaver 7,50 sisteminin nasıl yükleneceği açıklanır.
Örnek yapılandırmalarda, yükleme komutları vb. ASCS örnek numarası 00, örnek numarası 02 ve SAP sistem KIMLIĞI NW1 kullanılır. Örnekteki kaynakların (örneğin, sanal makineler, sanal ağlar) adları, kaynakları oluşturmak için SAP sistem KIMLIĞI NW1 ile [yakınsanmış şablonu][template-converged] kullandığınızı varsayar.

Önce aşağıdaki SAP notlarını ve kağıtları okuyun

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

## <a name="overview"></a>Genel Bakış

SAP NetWeaver, yüksek kullanılabilirlik elde etmek için bir NFS sunucusu gerektirir. NFS sunucusu ayrı bir kümede yapılandırılır ve birden çok SAP sistemi tarafından kullanılabilir.

![SAP NetWeaver yüksek kullanılabilirliğe genel bakış](./media/high-availability-guide-suse/ha-suse.png)

NFS sunucusu, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS ve SAP HANA veritabanı sanal konak adı ve sanal IP adreslerini kullanır. Azure 'da bir sanal IP adresi kullanmak için bir yük dengeleyici gereklidir. [Standart yük dengeleyici](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)kullanmanızı öneririz. Aşağıdaki listede, (A) SCS ve ERS yük dengeleyicinin yapılandırması gösterilmektedir.

> [!IMPORTANT]
> Azure VM 'lerinde Konuk işletim sistemi olarak SUSE Linux içeren SAP yoks/ERS 'in çoklu SID Kümelemesi **desteklenmez**. Çoklu SID Kümelemesi, tek bir pacemaker kümesinde farklı SID 'Leri olan birden fazla SAP ASCS/ERS örneğinin yüklenmesini açıklar

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

## <a name="setting-up-a-highly-available-nfs-server"></a>Yüksek oranda kullanılabilir bir NFS sunucusu ayarlama

SAP NetWeaver, aktarım ve profil dizini için paylaşılan depolama gerektirir. SAP NetWeaver için NFS sunucusu kurma hakkında [SUSE Linux Enterprise Server üzerindeki Azure VM 'LERINDE NFS Için yüksek kullanılabilirliği][nfs-ha] okuyun.

## <a name="setting-up-ascs"></a>(A) SCS ayarlama

Sanal makineler, kullanılabilirlik kümesi ve yük dengeleyici dahil olmak üzere tüm gerekli Azure kaynaklarını dağıtmak için GitHub 'dan bir Azure şablonu kullanabilir ya da kaynakları el ile dağıtabilirsiniz.

### <a name="deploy-linux-via-azure-template"></a>Azure şablonu aracılığıyla Linux dağıtma

Azure Marketi, yeni sanal makineler dağıtmak için kullanabileceğiniz, SAP uygulamaları için SUSE Linux Enterprise Server bir görüntü içerir. Market görüntüsü SAP NetWeaver için kaynak aracısını içerir.

Tüm gerekli kaynakları dağıtmak için GitHub 'daki hızlı başlangıç şablonlarından birini kullanabilirsiniz. Şablon, sanal makineleri, yük dengeleyiciyi, kullanılabilirlik kümesini vb. dağıtır. Şablonu dağıtmak için aşağıdaki adımları izleyin:

1. [Ascs/SCS çoklu SID şablonunu][template-multisid-xscs] veya Azure Portal [yakınsama şablonunu][template-converged] açın. 
   YOKS/SCS şablonu yalnızca SAP NetWeaver yoks/SCS ve ERS (yalnızca Linux) örnekleri için Yük Dengeleme kuralları oluşturur, ancak yakınsama şablonu bir veritabanı için Yük Dengeleme kurallarını da (örneğin Microsoft SQL Server veya SAP HANA) oluşturur. SAP NetWeaver tabanlı bir sistem yüklemeyi planlıyorsanız ve aynı makinelere veritabanını da yüklemek istiyorsanız, [yakınsanmış şablonu][template-converged]kullanın.
1. Aşağıdaki parametreleri girin
   1. Kaynak öneki (yalnızca yoks/SCS çoklu SID şablonu)  
      Kullanmak istediğiniz ön eki girin. Değer, dağıtılan kaynaklar için bir ön ek olarak kullanılır.
   3. SAP sistem KIMLIĞI (yalnızca yakınsanmış şablon)  
      Yüklemek istediğiniz SAP sisteminin SAP sistem KIMLIĞINI girin. KIMLIK, dağıtılan kaynakların ön eki olarak kullanılır.
   4. Yığın türü  
      SAP NetWeaver yığın türünü seçin
   5. İşletim sistemi türü  
      Linux dağıtımlardan birini seçin. Bu örnekte, SLES 12 BYOS öğesini seçin
   6. DB türü  
      HANA seçin
   7. SAP sistem boyutu.  
      Yeni sistemin sağladığı SAPS miktarı. Sistemin kaç tane için gerekli olduğundan emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun
   8. Sistem kullanılabilirliği  
      HA seçin
   9. Yönetici Kullanıcı adı ve yönetici parolası  
      Makinede oturum açmak için kullanılabilecek yeni bir Kullanıcı oluşturulur.
   10. Alt ağ KIMLIĞI  
   VM 'yi tanımlanmış VM 'ye atanmış bir alt ağa sahip olduğunuz mevcut bir VNet 'e dağıtmak istiyorsanız, söz konusu alt ağın KIMLIĞINI adlandırın. KIMLIK genellikle/Subscriptions/ **&lt;ABONELIK kimliği&gt;** /ResourceGroups/ **&lt;kaynak grubu adı&gt;** /Providers/Microsoft.Network/virtualNetworks/ **&lt;sanal ağ adı&gt;** /Subnets/ **&lt;alt ağ adı&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux 'u Azure portal aracılığıyla el ile dağıtın

Önce bu NFS kümesi için sanal makineleri oluşturmanız gerekir. Daha sonra, bir yük dengeleyici oluşturur ve arka uç havuzlarındaki sanal makineleri kullanırsınız.

1. Kaynak Grubu oluşturma
1. Sanal ağ oluşturma
1. Kullanılabilirlik kümesi oluşturma  
   En fazla güncelleştirme etki alanını ayarla
1. Sanal makine oluştur 1  
   En az SLES4SAP 12 SP1, bu örnekte SLES4SAP 12 SP1 görüntüsünü kullanın https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES for SAP uygulamaları 12 SP1 kullanılır  
   Daha önce oluşturulan kullanılabilirlik kümesini seçin  
1. Sanal makine oluştur 2  
   En az SLES4SAP 12 SP1, bu örnekte SLES4SAP 12 SP1 görüntüsünü kullanın https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES for SAP uygulamaları 12 SP1 kullanılır  
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
         1. Sanal makine seçin
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

Bu (A) SCS sunucusu için temel bir Paceoluşturucu kümesi oluşturmak üzere [Azure 'daki SUSE Linux Enterprise Server Paceyapıcısı ayarlama](high-availability-guide-suse-pacemaker.md) bölümündeki adımları izleyin.

### <a name="installation"></a>Yükleme

Şu öğeler, **[A]** ön eki olan tüm düğümlere uygulanabilir, **[1]** -yalnızca düğüm 1 veya **[2]** için geçerlidir-yalnızca node 2 için geçerlidir.

1. **[A]** SUSE bağlayıcısını Install

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Küme düğümlerinizin ana bilgisayar adları içinde tire kullanmayın. Aksi takdirde, kümeniz çalışmaz. Bu bilinen bir sınırlamadır ve SUSE bir çözüm üzerinde çalışmaktadır. Bu düzeltme, SAP-SUSE-Cloud-Connector paketinin bir düzeltme eki olarak yayımlanacak.

   SAP SUSE Cluster bağlayıcısının yeni sürümünü yüklediğinizden emin olun. Eski bir tane sap_suse_cluster_connector çağrıldı ve yeni bir tane **SAP-SUSE-Cluster-Connector**olarak adlandırılır.

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** SAP kaynak aracılarını güncelleştirme  
   
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

1. **[A]** kurulum konak adı çözümlemesi

   Bir DNS sunucusu kullanabilir veya tüm düğümlerdeki/etc/Konakları değiştirebilirsiniz. Bu örnek,/etc/hosts dosyasının nasıl kullanılacağını gösterir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   /Etc/hostklasörüne aşağıdaki satırları ekleyin. IP adresini ve ana bilgisayar adını ortamınıza uyacak şekilde değiştirin   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver yüklemesi için hazırlanma

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

1. **[A]** bir oto yapılandırma

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   İle bir dosya oluşturun

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   Yeni paylaşımları bağlamak için oto yeniden başlatın

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
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


### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver yoks/ERS yükleme

1. **[1]** ascs örneği için BIR sanal IP kaynağı ve sistem durumu araştırması oluşturun

   > [!IMPORTANT]
   > En son test, Netcat 'in biriktirme listesi ve yalnızca bir bağlantıyı işleme sınırlaması nedeniyle isteklere yanıt vermeyi durdurduğu ortaya çıkarılan durumlardır. Netcat kaynağı Azure yük dengeleyici isteklerini dinlemeyi durduruyor ve kayan IP kullanılamaz hale gelir.  
   > Mevcut Paceüreticisi kümeleri için, [Azure yük dengeleyici algılama sağlamlaştırma](https://www.suse.com/support/kb/doc/?id=7024128)içindeki yönergeleri izleyerek netcat 'i socat ile değiştirmeyi öneririz. Değişikliğin kısa kapalı kalma süresinin gerekli olacağını unutmayın.  

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS anything \
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:620<b>00</b>,backlog=10,fork,reuseaddr /dev/null" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** SAP NetWeaver yoks 'yi yükler  

   Ass için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adını kullanarak SAP NetWeaver yoks 'yi ilk düğümde kök olarak yükleyin; Örneğin, <b>NW1-ascs</b>, <b>kısmına 10.0.0.7</b> ve araştırması için kullandığınız örnek numarası. yük dengeleyici, örneğin <b>00</b>.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Yükleme/usr/SAP/**NW1**/ascs**00**' da bir alt klasör oluşturamazsa, Ass**00** klasörünün sahibini ve grubunu ayarlamayı deneyin ve yeniden deneyin.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** ers örneği için BIR sanal IP kaynağı ve sistem durumu araştırması oluşturun

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS anything \
    params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:621<b>02</b>,backlog=10,fork,reuseaddr /dev/null" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_NW1_ASCS,nc_NW1_ERS violate uniqueness for parameter "binfile": "/usr/bin/socat"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** SAP NetWeaver iciler 'ı yükler

   SAP NetWeaver ERS 'ı, ERS için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adı ile, örneğin, <b>NW1-aers</b>, <b>10.0.0.8</b> ve araştırma için kullandığınız örnek numarası gibi bir kök olarak yükleyin. Yük Dengeleyici (örneğin <b>02</b>).

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > SWPM SP 20 PL 05 veya üzeri bir sürümü kullanın. Düşük sürümler izinleri doğru olarak ayarlamayın ve yükleme başarısız olur.

   Yükleme/usr/SAP/**NW1**/ers**02**' de bir alt klasör oluşturamazsa, ers**02** klasörünün sahibini ve grubunu ayarlamayı deneyin ve yeniden deneyin.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** yoks/SCS ve ers örnek profillerini uyarlayın
 
   * YOKS/SCS profili

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS profili

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** canlı tutmayı yapılandırma

   SAP NetWeaver uygulama sunucusu ve yoks/SCS arasındaki iletişim, bir yazılım yük dengeleyici aracılığıyla yönlendirilir. Yük dengeleyici, yapılandırılabilir bir zaman aşımından sonra etkin olmayan bağlantıları keser. Bunu engellemek için SAP NetWeaver ASCS/SCS profilinde bir parametre ayarlamanız ve Linux sistem ayarlarını değiştirmeniz gerekir. Daha fazla bilgi için [SAP Note 1410736][1410736] makalesini okuyun.

   Ass/SCS profil parametresi EnQue/encnı/set_so_keepalive son adımla zaten eklendi.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** yükleme sonrasında SAP kullanıcılarını yapılandırma

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** sapservıce DOSYASıNA ASCıS ve SAP hizmetlerini ekleyin

   ASCS hizmeti girişini ikinci düğüme ekleyin ve ilk düğüme ERS hizmet girişini kopyalayın.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** SAP küme kaynaklarını oluşturma

Sıraya alma sunucusu 1 mimarisini (ENSA1) kullanıyorsanız, kaynakları aşağıdaki gibi tanımlayın:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  SAP, SAP NW 7,52 itibariyle çoğaltma dahil olmak üzere sıraya alma sunucusu 2 için destek sunmuştur. ABAP platform 1809 ' den başlayarak, sıraya alma sunucusu 2 varsayılan olarak yüklenir. Sıraya alma sunucusu 2 desteği için bkz. SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
Sıraya alma sunucusu 2 mimarisini ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) kullanıyorsanız, kaynakları aşağıdaki gibi tanımlayın:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Daha eski bir sürümden yükseltiyorsanız ve sıraya alma sunucusu 2 ' ye geçiş yapıyorsanız bkz. SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların hangi düğümde çalıştığı önemli değildir.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver uygulama sunucusu hazırlığı

Bazı veritabanları, veritabanı örneği yüklemesinin bir uygulama sunucusunda yürütülmesini gerektirir. Uygulama sunucusu sanal makinelerini bu durumlarda kullanabilecek şekilde hazırlayın.

Bu adımlar, uygulama sunucusunu yoks/SCS ve HANA sunucularından farklı bir sunucuya yüklediğinizi varsaymaktadır. Aksi takdirde, aşağıdaki adımlardan bazıları (konak adı çözümlemesini yapılandırma gibi) gerekli değildir.

1. İşletim sistemini Yapılandır

   Kirli önbelleğin boyutunu küçültün. Daha fazla bilgi için bkz. [büyük RAM Ile SLES 11/12 sunucularında düşük yazma performansı](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Kurulum konak adı çözümlemesi

   Bir DNS sunucusu kullanabilir veya tüm düğümlerdeki/etc/Konakları değiştirebilirsiniz. Bu örnek,/etc/hosts dosyasının nasıl kullanılacağını gösterir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin

   ```bash
   sudo vi /etc/hosts
   ```

   /Etc/hostklasörüne aşağıdaki satırları ekleyin. IP adresini ve ana bilgisayar adını ortamınıza uyacak şekilde değiştirin

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Sapmnt dizinini oluşturma

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Oto 'yi yapılandırma

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   İle yeni bir dosya oluşturun

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Yeni paylaşımları bağlamak için oto yeniden başlatın

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Takas dosyasını Yapılandır

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

## <a name="install-database"></a>Veritabanını yükler

Bu örnekte, SAP HANA SAP NetWeaver yüklüdür. Bu yükleme için desteklenen her veritabanını kullanabilirsiniz. SAP HANA Azure 'da nasıl yükleyeceğiniz hakkında daha fazla bilgi için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]. Desteklenen veritabanlarının listesi için bkz. [SAP Note 1928533][1928533].

1. SAP veritabanı örnek yüklemesini çalıştırma

   SAP NetWeaver veritabanı örneğini, veritabanı için yük dengeleyici ön uç yapılandırmasının IP adresiyle eşlenen bir sanal ana bilgisayar adı kullanarak kök olarak yükleyin. Örneğin, <b>NW1-DB</b> ve <b>10.0.0.13</b>.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver uygulama sunucusu yüklemesi

SAP uygulama sunucusu yüklemek için aşağıdaki adımları izleyin.

1. Uygulama sunucusunu hazırla

   Uygulama sunucusunu hazırlamak için yukarıdaki Bölüm [SAP NetWeaver uygulama sunucusu hazırlama](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) bölümündeki adımları izleyin.

1. SAP NetWeaver uygulama sunucusunu yükler

   Birincil veya ek SAP NetWeaver uygulamaları sunucusu yükler.

   Kök olmayan bir kullanıcının sapinst 'ya bağlanmasına izin vermek için sapinst parametresini SAPINST_REMOTE_ACCESS_USER kullanabilirsiniz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA güvenli depoyu Güncelleştir

   SAP HANA güvenli mağazayı, SAP HANA sistem çoğaltması kurulumunun sanal adını gösterecek şekilde güncelleştirin.

   Girişleri listelemek için aşağıdaki komutu çalıştırın
   <pre><code>hdbuserstore List
   </code></pre>

   Bu, tüm girdileri listelemelidir ve şuna benzer görünmelidir
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   Çıktı, varsayılan girdinin IP adresinin, yük dengeleyicinin IP adresine değil, sanal makineye işaret ettiği gösterir. Bu girdinin, yük dengeleyicinin sanal ana bilgisayar adına işaret eden şekilde değiştirilmesi gerekir. Aynı bağlantı noktasını (yukarıdaki çıktıda**30313** ) ve veritabanı adını (yukarıdaki çıktıda bulunan**HN1** ) kullandığınızdan emin olun!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Küme kurulumunu test etme

Aşağıdaki testler, SUSE 'in en iyi yöntemler kılavuzlarındaki test çalışmalarının bir kopyasıdır. Kolaylık olması için bunlar kopyalanırlar. Ayrıca en iyi yöntem kılavuzlarını okuyun ve eklenmiş olabilecek tüm ek testleri gerçekleştirin.

1. Test HAGetFailoverConfig, HACheckConfig ve HACheckFailoverConfig

   Aşağıdaki komutları, ASCS örneğinin çalışmakta olduğu düğümde \<sapsıd > adm olarak çalıştırın. Komutlar hata vererek başarısız olursa, bu durum, ana bilgisayar bilgisayarınızdaki çizgilerden kaynaklanıyor olabilir. Bu bilinen bir sorundur ve SAP-SUSE-Cluster-Connector paketindeki SUSE tarafından düzeltilecektir.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. ASCS örneğini el ile geçirme

   Teste başlamadan önce kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   ASCS örneğini geçirmek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Test HAFailoverToNode

   Teste başlamadan önce kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   ASCS örneğini geçirmek için \<sapsıd > adm olarak aşağıdaki komutları çalıştırın.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Düğüm kilitlenmesinin benzetimini yap

   Teste başlamadan önce kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   ASCS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutu çalıştırın

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   SBD kullanırsanız, pacemaker, sonlandırılan düğümde otomatik olarak başlamamalıdır. Düğüm yeniden başlatıldıktan sonra durum şöyle görünmelidir.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   Sonlandırılan düğümde pacemaker ' ı başlatmak, SBD iletilerini temizlemek ve hatalı kaynakları temizlemek için aşağıdaki komutları kullanın.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Ass örneğinin el ile yeniden başlatılmasını test etme

   Teste başlamadan önce kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Bir sıraya alma kilidi oluşturun, örneğin işlem su01 içinde bir Kullanıcı düzenleyin. ASCS örneğinin çalıştırıldığı düğüm üzerinde \<sapsıd > adm olarak aşağıdaki komutları çalıştırın. Komutlar, yoks örneğini durdurur ve yeniden başlatır. Sıraya alma sunucusu 1 mimarisi kullanılıyorsa, bu testte sıraya alma kilidinin kaybedilmesi beklenir. Sıraya alma sunucusu 2 mimarisi kullanılıyorsa, sıraya alma bekletilecektir. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS örneği şimdi pacemaker 'da devre dışı bırakılmalıdır

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   ASCS örneğini aynı düğümde yeniden başlatın.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   İşlem su01 sıraya alma kilidi kaybedilir ve arka uç sıfırlanmalıdır. Testten sonra kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. İleti sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   İleti sunucusu sürecini belirlemek ve sonlandırmak için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   İleti sunucusunu yalnızca bir kez sonlandırdıysanız, sapstart tarafından yeniden başlatılır. Bunu yeterince fazla sonlandırdıysanız, Paceyapıcısı sonunda yoks örneğini diğer düğüme taşıyacaktır. Testten sonra Ass ve ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Sıraya alma sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Sıraya alma sunucusunu sonlandırmak için ASCS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutları çalıştırın.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   ASCS örneği hemen diğer düğüme yük devreder. Ass örneği başlatıldıktan sonra, ERS örneği de yük devreder. Testten sonra Ass ve ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Sıraya alma çoğaltma sunucusu işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Sıraya alma çoğaltma sunucusu işlemini sonlandırmak için ERS örneğinin çalıştırıldığı düğümde kök olarak aşağıdaki komutu çalıştırın.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Komutu yalnızca bir kez çalıştırırsanız, sapbaşlat işlemi yeniden başlatır. Bunu yeterince sık çalıştırırsanız, sapstart işlemi yeniden başlatmaz ve kaynak durdurulmuş durumda olur. Testten sonra ERS örneğinin kaynak durumunu temizlemek için aşağıdaki komutları kök olarak çalıştırın.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Testten sonra kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Sıraya alma sapstartsrv işlemini Sonlandır

   Teste başlamadan önce kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   ASCS 'nin çalıştığı düğümde kök olarak aşağıdaki komutları çalıştırın.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   Sapstartsrv işlemi, pacemaker kaynak Aracısı tarafından her zaman yeniden başlatılmalıdır. Testten sonra kaynak durumu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* Azure 'da SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan (büyük örnekler) oluşturma hakkında bilgi edinmek için bkz. [Azure 'da SAP HANA (büyük örnekler) yüksek kullanılabilirlik ve olağanüstü durum kurtarma](hana-overview-high-availability-disaster-recovery.md).
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan planı oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]
