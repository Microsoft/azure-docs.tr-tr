---
title: 'Hızlı başlangıç: Azure sanal makinelerinde tek örnekli SAP HANA El Ile yüklenmesi | Microsoft Docs'
description: Azure sanal makinelerinde tek örnekli SAP HANA el ile yüklenmesi için hızlı başlangıç kılavuzu
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 630f094ffc6c57a0137d1abc46476f5abe64f616
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750375"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Hızlı başlangıç: Azure sanal makinelerinde tek örnekli SAP HANA El Ile yüklenmesi
## <a name="introduction"></a>Tanıtım
Bu kılavuz, SAP NetWeaver 7,5 ve SAP HANA 1,0 SP12 SAP HANA el ile yüklerken Azure sanal makinelerinde tek örnekli bir ayarlamanıza yardımcı olur. Bu kılavuzun odaklanarak Azure 'da SAP HANA dağıtma işlemi yapılır. SAP belgelerinin yerini almaz. 

> [!NOTE]
> Bu kılavuzda, Azure VM 'lerine SAP HANA dağıtımları açıklanmaktadır. SAP HANA HANA büyük örneklerine dağıtma hakkında daha fazla bilgi için bkz. [Azure sanal MAKINELERINDE SAP kullanma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Önkoşullar
Bu kılavuzda, aşağıdaki gibi hizmet olarak altyapı (IaaS) hakkında bilgi sahibi olduğunuz varsayılır.
 * Azure portal veya PowerShell aracılığıyla sanal makineleri (VM) veya sanal ağları dağıtma.
 * JavaScript Nesne Gösterimi (JSON) şablonlarını kullanma seçeneğini içeren Azure platformlar arası komut satırı arabirimi (CLı).

Bu kılavuzda, hakkında bilgi sahibi olduğunuz varsayılır:
* SAP HANA ve SAP NetWeaver ve bunların Şirket içinde nasıl yükleneceği.
* Azure 'da SAP HANA ve SAP uygulama örnekleri yüklemek ve çalıştırmak.
* Aşağıdaki kavramlar ve yordamlar:
   * Azure sanal ağ planlaması ve Azure depolama kullanımını içeren Azure 'da SAP dağıtımını planlama. Bkz. [Azure sanal makineler 'de SAP NetWeaver-planlama ve uygulama kılavuzu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Azure 'da VM dağıtmanın yolları ve dağıtım ilkeleri. Bkz. [SAP Için Azure sanal makineleri dağıtımı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * SAP NetWeaver ABAP SAP Merkezi Hizmetleri (ASCS), SAP Merkezi Hizmetleri (SCS) ve Azure üzerinde sıraya alma çoğaltma sunucusu (ERS) için yüksek kullanılabilirlik. Bkz. [Azure VM 'LERDE SAP NetWeaver Için yüksek kullanılabilirlik](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Azure üzerinde Ass/SCS 'nin çok düzeyli yüklemesinde verimliliği artırmaya ilişkin ayrıntılar. Bkz. [SAP NetWeaver çoklu SID yapılandırması oluşturma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Azure 'da Linux tabanlı VM 'Ler temelinde SAP NetWeaver çalıştırma ilkeleri. Bkz. [MICROSOFT Azure SUSE Linux VM 'LERINDE SAP NetWeaver çalıştırma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Bu kılavuz, Azure VM 'lerinde Linux için özel ayarlar sağlar. Ayrıca, Azure Storage disklerini Linux VM 'lerine doğru şekilde iliştirme hakkında bilgi de sağlar.

Üretim senaryolarında kullanılabilecek Azure sanal makine türleri, [ıAAS Için SAP belgelerinde](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)listelenmiştir. Üretim dışı senaryolar için, çok çeşitli yerel Azure VM türleri mevcuttur.
VM yapılandırması ve işlemleri hakkında daha fazla bilgi için bkz. [Azure 'da altyapı yapılandırmalarını ve işlemlerini SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Yüksek kullanılabilirlik SAP HANA için bkz. [Azure sanal makineler için SAP HANA yüksek kullanılabilirlik](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Hızlı bir şekilde dağıtılan bir SAP HANA örneği veya S/4HANA ya da en siyah beyaz/4HANA sistemi almak istiyorsanız [SAP Cloud gereç kitaplığı](https://cal.sap.com)kullanmayı düşünün. Azure 'da SAP Cloud gereç kitaplığı aracılığıyla bir S/4HANA sisteminin nasıl dağıtılacağı hakkında belge bulabilirsiniz. Örneğin, [Bu kılavuzda](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Tüm ihtiyacınız olan bir Azure aboneliği ve SAP bulut gereci kitaplığı ile kaydolerişebilecek bir SAP kullanıcısı.

## <a name="additional-resources"></a>Ek kaynaklar
### <a name="sap-hana-backup"></a>SAP HANA yedekleme
Azure VM 'lerinde SAP HANA veritabanlarının yedeklenmesi hakkında daha fazla bilgi için, bkz.:
* [Azure sanal makinelerinde SAP HANA Için Yedekleme Kılavuzu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [Dosya düzeyinde Azure Backup SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [Depolama anlık görüntülerine göre yedekleme SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>SAP bulut gereç kitaplığı
SAP Cloud gereç kitaplığı 'nı kullanarak S/4HANA veya siyah beyaz/4HANA dağıtma hakkında daha fazla bilgi için, bkz. [MICROSOFT Azure SAP S/4HANA veya siyah beyaz/4HANA dağıtma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA tarafından desteklenen işletim sistemleri
SAP HANA tarafından desteklenen işletim sistemleri hakkında bilgi için bkz. [SAP Note 2235581-SAP HANA: desteklenen işletim sistemleri](https://launchpad.support.sap.com/#/notes/2235581/E). Azure VM 'Leri bu işletim sistemlerinin yalnızca bir alt kümesini destekler. Azure 'da SAP HANA dağıtmak için aşağıdaki işletim sistemleri desteklenir: 

* SUSE Linux Enterprise Server 12. x
* Red Hat Enterprise Linux 7.2

SAP HANA ve farklı Linux işletim sistemleri hakkında ek SAP belgeleri için bkz.:

* [SAP Note 171356: Linux 'TA SAP Software: genel bilgiler](https://launchpad.support.sap.com/#/notes/1984787).
* [SAP Note 1944799: SLES işletim sistemi yükleme yönergelerini SAP HANA](http://service.sap.com/sap/support/notes/1944799).
* [SAP Note 2205917: SAP uygulamaları IÇIN SLES 12 için önerilen SAP HANA DB OS ayarları](https://launchpad.support.sap.com/#/notes/2205917/E).
* [SAP Note 1391070: LINUX UUID çözümleri](https://launchpad.support.sap.com/#/notes/1391070).
* [SAP Note 2009879: Red Hat Enterprise Linux (RHEL) işletim sistemi için SAP HANA yönergeleri](https://launchpad.support.sap.com/#/notes/2009879).
* [SAP Note 2292690: SAP HANA DB: RHEL 7 Için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>Azure 'da SAP izleme
Azure 'da SAP izleme hakkında bilgi için:

* [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) , Azure 'Da Linux VM 'lerle SAP gelişmiş izlemeyi ele alır. 
* [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) , LINUX 'TA SAPOSCOL hakkında bilgi tartışır. 
* [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) MICROSOFT Azure üzerindeki SAP için önemli izleme ölçümlerini ele alır.

### <a name="azure-vm-types"></a>Azure VM türleri
SAP HANA ile kullanılan Azure VM türleri ve SAP-desteklenen iş yükü senaryoları [SAP sertifikalı IaaS platformlarında](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)belgelenmiştir. 

SAP NetWeaver için SAP tarafından sertifikalı Azure sanal makine türleri veya S/4HANA uygulama katmanı, [SAP Note 1928533: Azure 'DA SAP uygulamaları: Desteklenen Ürünler ve Azure VM türleri](https://launchpad.support.sap.com/#/notes/1928533/E)bölümünde belgelenmiştir.

> [!NOTE]
> SAP-Linux-Azure tümleştirmesi, klasik dağıtım modelinde değil yalnızca Azure Resource Manager desteklenir. 

## <a name="manual-installation-of-sap-hana"></a>SAP HANA el ile yüklemesi

> [!IMPORTANT]
> Seçtiğiniz işletim sisteminin, kullandığınız belirli VM türlerinde SAP HANA için SAP sertifikalı olduğundan emin olun. Bu sanal makine türleri için SAP HANA sertifikalı VM türlerinin ve işletim sistemi sürümlerinin listesi, [SAP HANA sertifikalı IaaS platformlarında](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)aranabilir. Belirli bir sanal makine türü için SAP HANA tarafından desteklenen işletim sistemi sürümlerinin tam listesini almak üzere listelenen VM türünün ayrıntılarına tıkladığınızdan emin olun. Bu belgedeki örnek için, d serisi VM 'lerde SAP HANA için SAP tarafından desteklenmeyen bir SUSE Linux Enterprise Server (SLES) işletim sistemi sürümü kullandık.
>

Bu kılavuzda, Azure VM 'lerinde SAP HANA iki farklı şekilde el ile nasıl yükleneceği açıklanmaktadır:

* "Veritabanı örneğini yükleme" adımındaki dağıtılmış bir NetWeaver yüklemesinin parçası olarak SAP yazılım sağlama Yöneticisi 'Ni (SWPM) kullanın.
* SAP HANA veritabanı yaşam döngüsü Yöneticisi (HDBLCM) aracını kullanın ve ardından NetWeaver ' yi yükler.

Ayrıca, SAP HANA, SAP uygulama sunucusu ve yoks örneği gibi tüm bileşenleri tek bir VM 'de yüklemek için de SWPM kullanabilirsiniz. Adımlar bu [SAP HANA blog duyurusu](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)bölümünde açıklanmaktadır. Bu seçenek, bu hızlı başlangıç kılavuzunda açıklanmamaktadır, ancak dikkate almanız gereken sorunlar aynıdır.

Bir yüklemeye başlamadan önce, bu kılavuzun ilerleyen bölümlerindeki "SAP HANA el ile yüklemek için Azure VM 'lerini hazırlama" bölümünü okumanızı öneririz. Bunun yapılması, yalnızca varsayılan bir Azure VM yapılandırması kullandığınızda oluşabilecek birçok temel hata oluşmasını önlemeye yardımcı olabilir.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>SAP SWPM kullandığınızda SAP HANA yükleme için temel adımlar
Bu bölümde, bir dağıtılmış SAP NetWeaver 7,5 yüklemesi gerçekleştirmek üzere SAP SWPM kullandığınızda el ile, tek örnekli SAP HANA yüklemesi için önemli adımlar listelenmektedir. Bireysel adımlar, bu kılavuzun ilerleyen bölümlerinde ekran görüntüleri hakkında daha ayrıntılı olarak açıklanmıştır.

1. İki test sanal makinesi içeren bir Azure sanal ağı oluşturun.
2. Azure Resource Manager modeline göre işletim sistemleriyle iki Azure VM dağıtın. Bu örnekte, SUSE Linux Enterprise Server ve SLES for SAP Applications 12 SP1 kullanılmaktadır. 
3. Uygulama sunucusu VM 'sine, örneğin 75 GB veya 500 GB diskler gibi iki Azure Standart veya Premium Depolama diski ekleyin.
4. HANA DB sunucusu VM 'sine Premium depolama diskleri ekleyin. Daha fazla bilgi için bu kılavuzun ilerleyen bölümlerindeki "disk kurulumu" bölümüne bakın.
5. Boyut veya işleme gereksinimlerine bağlı olarak birden çok disk iliştirin. Sonra şeritli birimler oluşturun. VM 'nin içindeki işletim sistemi düzeyinde mantıksal birim yönetimi (LVM) veya çok cihazlı bir yönetim (mdaddm) aracını kullanın.
6. Eklenen disklerde veya mantıksal birimlerde XFS dosya sistemleri oluşturun.
7. Yeni XFS dosya sistemlerini işletim sistemi düzeyinde bağlayın. Tüm SAP yazılımları için bir dosya sistemi kullanın. /Sapmnt dizini ve yedeklemeleri için diğer dosya sistemini kullanın, örneğin. SAP HANA DB sunucusunda, XFS dosya sistemlerini/Hana ve/usr/sapas gibi Premium Depolama disklerine bağlayın. Bu işlem, kök dosya sisteminin doldurmasını engellemek için gereklidir. Kök dosya sistemi, Linux Azure VM 'lerinde büyük değildir. 
8. /Etc/hosts dosyasındaki test sanal makinelerinin yerel IP adreslerini girin.
9. /Etc/fstab dosyasına **NOFAIL** parametresini girin.
10. Linux çekirdek parametrelerini kullandığınız Linux işletim sistemi sürümüne göre ayarlayın. Daha fazla bilgi için bu kılavuzdaki HANA ve "çekirdek parametreleri" başlıklı SAP notlarına bakın.
11. Değiştirme alanı Ekle.
12. İsteğe bağlı olarak, test VM 'lerine bir grafik masaüstü yüklemesi yapın. Aksi takdirde, uzak bir SAPinst yüklemesi kullanın.
13. SAP hizmeti marketi 'nden SAP yazılımını indirin.
14. SAP ASCS örneğini App Server VM 'sine yükler.
15. NFS kullanarak/sapmnt dizinini test VM 'Leri arasında paylaşabilirsiniz. Uygulama sunucusu VM 'si, NFS sunucusudur.
16. Veritabanı sunucusu VM 'sinde SWPM 'yi kullanarak HANA içeren veritabanı örneğini yükler.
17. Birincil uygulama sunucusunu (PAS) uygulama sunucusu VM 'sine yükler.
18. SAP Management Console (SAP MC) öğesini başlatın. Örneğin, SAP GUI veya HANA Studio 'Ya bağlanın.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>HDBLCM kullandığınızda SAP HANA yükleme için temel adımlar
Bu bölümde, bir dağıtılmış SAP NetWeaver 7,5 yüklemesi gerçekleştirmek üzere SAP HDBLCM kullandığınızda el ile, tek örnekli SAP HANA yüklemesi için önemli adımlar listelenmektedir. Bireysel adımlar, bu kılavuzda ekran görüntüleri hakkında daha ayrıntılı olarak açıklanmıştır.

1. İki test sanal makinesi içeren bir Azure sanal ağı oluşturun.
2. Azure Resource Manager modeline göre işletim sistemleriyle iki Azure VM dağıtın. Bu örnek, SLES ve SLES for SAP Applications 12 SP1 kullanır.
3. App Server VM 'sine, örneğin 75 GB veya 500 GB diskler gibi iki Azure Standart veya Premium Depolama diski ekleyin.
4. HANA DB sunucusu VM 'sine Premium depolama diskleri ekleyin. Daha fazla bilgi için bu kılavuzun ilerleyen bölümlerindeki "disk kurulumu" bölümüne bakın.
5. Boyut veya işleme gereksinimlerine bağlı olarak birden çok disk iliştirin. Mantıksal birim yönetimini veya VM içindeki işletim sistemi düzeyinde bir mdaddm aracını kullanarak şeritli birimler oluşturun.
6. Eklenen disklerde veya mantıksal birimlerde XFS dosya sistemleri oluşturun.
7. Yeni XFS dosya sistemlerini işletim sistemi düzeyinde bağlayın. Tüm SAP yazılımları için bir dosya sistemi kullanın. /Sapmnt dizini ve yedeklemeleri için diğer dosya sistemini kullanın, örneğin. SAP HANA DB sunucusunda, XFS dosya sistemlerini/Hana ve/usr/sapas gibi Premium Depolama disklerine bağlayın. Bu işlem, kök dosya sisteminin doldurmasını önlemeye yardımcı olmak için gereklidir. Kök dosya sistemi, Linux Azure VM 'lerinde büyük değildir.
8. /Etc/hosts dosyasındaki test sanal makinelerinin yerel IP adreslerini girin.
9. /Etc/fstab dosyasına **NOFAIL** parametresini girin.
10. Çekirdek parametrelerini kullandığınız Linux işletim sistemi sürümüne göre ayarlayın. Daha fazla bilgi için bu kılavuzdaki HANA ve "çekirdek parametreleri" başlıklı SAP notlarına bakın.
11. Değiştirme alanı Ekle.
12. İsteğe bağlı olarak, test VM 'lerine bir grafik masaüstü yüklemesi yapın. Aksi takdirde, uzak bir SAPinst yüklemesi kullanın.
13. SAP hizmeti marketi 'nden SAP yazılımını indirin.
14. HANA DB sunucusu sanal makinesinde 1001 Grup KIMLIĞI ile bir sapsys grubu oluşturun.
15. HANA veritabanı yaşam döngüsü Yöneticisi 'ni kullanarak VERITABANı sunucusu VM 'sine SAP HANA.
16. SAP ASCS örneğini App Server VM 'sine yükler.
17. NFS kullanarak/sapmnt dizinini test VM 'Leri arasında paylaşabilirsiniz. Uygulama sunucusu VM 'si, NFS sunucusudur.
18. Hana DB sunucusu sanal makinesinde SWPM kullanarak HANA içeren veritabanı örneğini yükler.
19. Birincil uygulama sunucusunu uygulama sunucusu VM 'sine yükler.
20. SAP MC 'yi başlatın. SAP GUI veya HANA Studio aracılığıyla bağlanın.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>SAP HANA el ile yüklemesi için Azure VM 'Leri hazırlama
Bu bölümde aşağıdaki konular ele alınmaktadır:

* İşletim sistemi güncelleştirmeleri
* Disk kurulumu
* Çekirdek parametreleri
* Dosya sistemleri
* /Etc/hosts dosyası
* /Etc/fstab dosyası

### <a name="os-updates"></a>İşletim sistemi güncelleştirmeleri
Ek yazılım yüklemeden önce Linux işletim sistemi güncelleştirmelerini ve düzeltmeleri denetleyin. Bir düzeltme eki yükleyerek, destek masasına bir çağrıdan kaçınabilirsiniz.

Kullandığınızdan emin olun:
* SAP uygulamaları için SUSE Linux Enterprise Server.
* Red Hat Enterprise Linux SAP uygulamaları veya SAP HANA için Red Hat Enterprise Linux. 

Daha önce yapmadıysanız, Linux aboneliğinizdeki işletim sistemi dağıtımını Linux satıcısından kaydettirin. SUSE, zaten hizmetleri içeren ve otomatik olarak kaydedilen SAP uygulamaları için işletim sistemi görüntülerine sahiptir.

Aşağıda **zypper** komutunu kullanarak SUSE Linux için kullanılabilir düzeltme eklerinin nasıl denetduğun bir örneği verilmiştir:

 `sudo zypper list-patches`

Sorun türüne bağlı olarak, düzeltme ekleri kategoriye ve önem derecesine göre sınıflandırılmaktadır. Kategori için yaygın olarak kullanılan değerler şunlardır: 
- Güvenlik
- Önerilen
- İsteğe Bağlı
- Özellik
- Belge
- yast

Önem derecesi için yaygın olarak kullanılan değerler şunlardır:

- Kritik
- Önemli
- Orta
- Düşük
- Memesi

**Zypper** komutu yalnızca, yüklü paketlerinizin ihtiyaç duyduğu güncelleştirmeler için arama yapar. Örneğin, şu komutu kullanabilirsiniz:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Sistemi güncelleştirmeden güncelleştirmeyi test etmek için `--dry-run` parametresi ekleyebilirsiniz.


### <a name="disk-setup"></a>Disk kurulumu
Azure üzerindeki bir Linux sanal makinesinde bulunan kök dosya sisteminin boyut sınırlaması vardır. Bu nedenle, SAP çalıştırmak için bir Azure VM 'ye ek disk alanı eklemeniz gerekir. SAP uygulama sunucusu Azure VM 'Leri için Azure Standart depolama disklerinin kullanımı yeterince olabilir. SAP HANA DBMS Azure VM 'Leri için, üretim ve üretim dışı uygulamalar için Azure Premium Depolama disklerinin kullanımı zorunludur.

[SAP HANA TDI depolama gereksinimlerine](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)bağlı olarak, aşağıdaki Azure Premium Depolama yapılandırması önerilir: 

| VM SKU 'SU | RAM |  /Hana/Data ve/Hana/log <br /> LVM veya mdaddm ile şeritli | /Hana/Shared | /root birimi | /usr/SAP |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Önerilen disk yapılandırmasında, HANA veri hacmi ve günlük birimi, LVM veya mdaddm ile dizili aynı Azure Premium depolama diskleri kümesine yerleştirilir. Azure Premium Depolama, artıklık için disklerin üç görüntüsünü sakladığı için RAID artıklık düzeyi tanımlanması gerekmez. 

Yeterli depolama alanı yapılandırırken emin olmak için bkz. [SAP HANA TDI depolama gereksinimleri](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) ve [SAP HANA sunucusu yükleme ve güncelleştirme Kılavuzu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Ayrıca, [yüksek performanslı Premium Depolama ve VM 'ler için yönetilen disklerde](../../windows/disks-types.md)belgelenen farklı Azure Premium Depolama disklerinin farklı sanal sabit DISK (VHD) işleme hacimlerini de göz önünde bulundurun. 

Veritabanı veya işlem günlüğü yedeklemelerini depolamak için HANA DBMS VM 'lerine daha fazla Premium Depolama diski ekleyebilirsiniz.

Şeridi yapılandırmak için kullanılan iki ana araç hakkında daha fazla bilgi için bkz.:

* [Linux 'ta yazılım RAID yapılandırma](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Azure 'daki bir LINUX sanal makinesinde LVM 'Yi yapılandırma](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Konuk işletim sistemi olarak Linux çalıştıran Azure VM 'lerine disk iliştirme hakkında daha fazla bilgi için bkz. [LINUX VM 'ye disk ekleme](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure Premium SSD 'Ler ile disk önbelleğe alma modlarını tanımlayabilirsiniz. /Hana/Data ve/Hana/log tutan Şeritli küme için disk önbelleğe almayı devre dışı bırakın. Diğer birimler için, diskler, önbelleğe alma modunu **ReadOnly**olarak ayarlar.

VM 'Ler oluşturmak için kullanılacak örnek JSON şablonlarını bulmak için bkz. [Azure hızlı başlangıç şablonları](https://github.com/Azure/azure-quickstart-templates).
VM-Simple-SLES şablonu temel bir şablondur. Bu, ek 100 GB veri diski ile bir depolama bölümü içerir. Bu şablonu temel olarak kullanın. Şablonu özel yapılandırmanıza uyarlayabilirsiniz.

> [!NOTE]
> Azure depolama diskini, [MICROSOFT Azure SUSE Linux VM 'LERINDE SAP NetWeaver çalıştırma](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)bölümünde BELGELENEN bir UUID kullanarak eklemek önemlidir.

Test ortamında, aşağıdaki ekran görüntüsünde gösterildiği gibi SAP App Server sanal makinesine iki Azure Standart depolama diski eklenir. Bir disk, yükleme için NetWeaver 7,5, SAP GUI ve SAP HANA gibi tüm SAP yazılımlarını depolar. İkinci disk, ek gereksinimler için yeterli boş alanın kullanılabilir olmasını sağlar. Örneğin, yedekleme ve test verileri ile/sapmnt dizini, diğer bir deyişle SAP profillerinin aynı SAP yatay 'e ait tüm VM 'Ler arasında paylaşılması gerekir.

![İki veri diski ve boyutlarını görüntüleyen uygulama sunucusu diskleri penceresi SAP HANA](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Çekirdek parametreleri
SAP HANA için belirli Linux çekirdek ayarları gereklidir. Bu çekirdek ayarları, standart Azure Galeri görüntülerinin bir parçası değildir ve el ile ayarlanması gerekir. SUSE veya Red Hat kullanmanıza bakılmaksızın parametreler farklı olabilir. Daha önce listelenen SAP notları bu parametreler hakkında bilgi verir. Gösterilen ekran görüntülerinde, SUSE Linux 12 SP1 kullanılmıştır. 

SLES for SAP Applications 12 genel kullanılabilirlik ve SLES for SAP Applications 12 SP1, eski **sapconf** aracının yerini alan yeni bir araç olan **ayarlanabilir adm**. **Ayarlanmış adm**için özel bir SAP HANA profili kullanılabilir. SAP HANA sistemini ayarlamak için, kök kullanıcı olarak aşağıdaki profili girin:

   `tuned-adm profile sap-hana`

**Ayarlanmış adm**hakkında daha fazla bilgi için bkz. [ayarlanabilir-adm hakkında SUSE belgeleri](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Aşağıdaki ekran görüntüsünde, istenen SAP HANA ayarlarına göre, ayar **-ADM** ' nin `transparent_hugepage` ve `numa_balancing` değerlerini nasıl değiştirdiği hakkında bilgi alabilirsiniz:

![Ayarlanmış adm Aracı, değerleri gerekli SAP HANA ayarlarına göre değiştirir](./media/hana-get-started/image005.jpg)

Çekirdek ayarlarını SAP HANA kalıcı hale getirmek için SLES 12 üzerinde **GRUB2** kullanın. **GRUB2**hakkında daha fazla bılgı için SUSE belgelerinin [yapılandırma dosya yapısı](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) bölümüne bakın.

Aşağıdaki ekran görüntüsünde, çekirdek ayarlarının yapılandırma dosyasında nasıl değiştirildiği ve ardından **GRUB2-mkconfig**kullanılarak nasıl derlenmesi gösterilmektedir:

![Yapılandırma dosyasında ve GRUB2-mkconfig kullanılarak derlenen çekirdek ayarları değiştirildi](./media/hana-get-started/image006.jpg)

Diğer bir seçenek de, YaST ve **önyükleme yükleyicisi**  > **çekirdek parametreleri** ayarlarını kullanarak ayarları değiştirir:

![YaST Önyükleme yükleyicisindeki çekirdek parametreleri ayarları sekmesi](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Dosya sistemleri
Aşağıdaki ekran görüntüsünde, iki bağlı Azure Standart depolama diskinin en üstünde SAP App Server sanal makinesinde oluşturulan iki dosya sistemi gösterilmektedir. Her iki dosya sistemi de XFS türündedir ve/sapdata ve/sapsoftware'e bağlanır.

Dosya sistemlerinizi bu şekilde yapılandırmak zorunlu değildir. Disk alanını nasıl yapılandıracağınıza yönelik başka seçenekleriniz vardır. En önemli düşünce, kök dosya sisteminin boş alan boşaltmasını önlemektir.

![SAP App Server sanal makinesinde oluşturulan iki dosya sistemi](./media/hana-get-started/image008.jpg)

SAP HANA DB VM 'si için, bir veritabanı yüklemesi sırasında, SPM ve **tipik** yükleme seçeneği Ile sapinst kullandığınızda her şey/Hana ve/usr/SAP'nin altına yüklenir. SAP HANA günlük yedeklemesi için varsayılan konum/usr/sap. altındadır. Yine de, kök dosya sisteminin depolama alanı tükendiğinden kaçınmak önemlidir. SWPM kullanarak SAP HANA yüklemeden önce/Hana ve/usr/SAP altında yeterli boş alan olduğundan emin olun.

SAP HANA standart dosya sistemi düzeninin açıklaması için, [SAP HANA sunucusu yükleme ve güncelleştirme Kılavuzu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)' na bakın.

![SAP App Server VM 'de oluşturulan ek dosya sistemleri](./media/hana-get-started/image009.jpg)

SAP NetWeaver 'i standart bir SLES/SLES for SAP Applications 12 Azure Galeri görüntüsüne yüklediğinizde, aşağıdaki ekran görüntüsünde gösterildiği gibi bir değiştirme alanı olmadığını belirten bir ileti görüntülenir. Bu iletiyi kapatmak için, **gg**, **mkswap**ve **swapon**kullanarak el ile takas dosyası ekleyebilirsiniz. Nasıl yapılacağını öğrenmek için, SUSE belgelerinin [yast bölümleyici bölümünü kullanarak](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) "takas dosyasını el ile ekleme" araması yapın.

Diğer bir seçenek de Linux VM aracısını kullanarak takas alanını yapılandırmaktır. Daha fazla bilgi için bkz. [Azure Linux Aracısı kullanıcı kılavuzu](../../extensions/agent-linux.md).

![Yetersiz değiştirme alanı olduğunu bildiren açılan ileti](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>/Etc/hosts dosyası
SAP 'yı yüklemeye başlamadan önce,/etc/hosts dosyasına SAP VM 'lerinin ana bilgisayar adlarını ve IP adreslerini eklediğinizden emin olun. Tüm SAP VM 'lerini tek bir Azure sanal ağı içinde dağıtın. Ardından, burada gösterildiği gibi iç IP adreslerini kullanın:

![/Etc/hosts dosyasında listelenen SAP VM 'lerinin ana bilgisayar adları ve IP adresleri](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>/Etc/fstab dosyası

Fstab dosyasına **NOFAIL** parametresini eklemek yararlı olabilir. Bu şekilde, disklerle ilgili bir sorun varsa, VM önyükleme işleminde yanıt vermeyi durdurmaz. Ancak ek disk alanının kullanılabilir olamayacağını ve işlemlerin kök dosya sistemini doldurmasını olabileceğini unutmayın. /Hana eksikse SAP HANA başlatılmaz.

![Fstab dosyasına NOFAIL parametresini ekleme](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES on SAP uygulamaları için grafik GNOME masaüstü 12
Bu bölümde nasıl yapılacağı açıklanmaktadır:

* GNOME Desktop ve xrdp 'yi, SAP uygulamaları için SLES 12/SLES üzerine yükler.
* SAP uygulamaları 12 için SLES 12/SLES üzerinde Firefox kullanarak Java tabanlı SAP MC 'yi çalıştırın.

Ayrıca, bu kılavuzda açıklanmayan Xterminal veya VNC gibi alternatifleri de kullanabilirsiniz.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>GNOME Desktop ve xrdp 'yi SAP uygulamaları için SLES 12/SLES 'e yükler 12
Windows arka planınız varsa, Firefox, SAPinst, SAP GUI, SAP MC veya HANA Studio 'Yu çalıştırmak için doğrudan SAP Linux VM 'lerinde bir grafik masaüstünü kolayca kullanabilirsiniz. Ardından, bir Windows bilgisayarından Uzak Masaüstü Protokolü (RDP) üzerinden sanal makineye bağlanabilirsiniz. Üretim ve üretim dışı Linux tabanlı sistemlere gug ekleme hakkında şirketinizin ilkelerine bağlı olarak, sunucunuza GNOME yüklemek isteyebilirsiniz. GNOME Desktop 'ı bir Azure SLES 12/SLES for SAP Applications 12 VM 'ye yüklemek için bu adımları izleyin.

1. Aşağıdaki komutu girerek GNOME Desktop 'ı (örneğin, PuTTY penceresinde) yükle:

   `zypper in -t pattern gnome-basic`

2. RDP aracılığıyla VM 'ye bağlantı sağlamak için xrdp 'yi yükler:

   `zypper in xrdp`

3. /Etc/sysconfig/windowmanager 'ı düzenleyin ve varsayılan pencere yöneticisini GNOME olarak ayarlayın:

   `DEFAULT_WM="gnome"`

4. Yeniden başlatma sonrasında xrdp 'nin otomatik olarak başlatılmasını sağlamak için **chkconfig** komutunu çalıştırın:

   `chkconfig -level 3 xrdp on`

5. RDP bağlantısıyla ilgili bir sorununuz varsa, örneğin bir PuTTY penceresinden yeniden başlatmayı deneyin:

   `/etc/xrdp/xrdp.sh restart`

6. Önceki adımda bahsedilen bir xrdp yeniden başlatması işe yaramazsa, bir. pid dosyası olup olmadığını denetleyin:

   `check /var/run` 

   @No__t_0 bakın. Bulursanız, kaldırın ve yeniden başlatmayı deneyin.

### <a name="start-sap-mc"></a>SAP MC 'yi Başlat
GNOME Desktop 'ı yükledikten sonra Firefox 'tan grafik Java tabanlı SAP MC ' yi başlatın. Bir Azure SLES 12/SLES for SAP Applications 12 VM 'de çalışıyorsa, bir hata gösterebilir. Eksik Java tarayıcı eklentisi nedeniyle hata oluşur.

SAP MC 'YI başlatmak için URL `<server>:5<instance_number>13`.

Daha fazla bilgi için bkz. [Web tabanlı sap yönetim konsolunu başlatma](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Aşağıdaki ekran görüntüsünde, Java tarayıcı eklentisi eksik olduğunda görüntülenen hata iletisi gösterilmektedir:

![Eksik Java-Browser eklentisini belirten hata iletisi](./media/hana-get-started/image013.jpg)

Sorunu çözmenin bir yolu, aşağıdaki ekran görüntüsünde gösterildiği gibi, YaST kullanarak eksik eklentiyi yüklemektir:

![Eksik eklentiyi yüklemek için YaST kullanma](./media/hana-get-started/image014.jpg)

SAP yönetim konsolu URL 'sini yeniden girdiğinizde, eklentiyi etkinleştirmeniz istenir:

![Eklenti etkinleştirme isteyen iletişim kutusu](./media/hana-get-started/image015.jpg)

Eksik bir dosya (JavaFX. Properties) ile ilgili bir hata iletisi de alabilirsiniz. SAP GUI 7,4 için Oracle Java 1,8 gereksinimi ile ilgilidir. Daha fazla bilgi için bkz. [SAP Note 2059429](https://launchpad.support.sap.com/#/notes/2059424).
SAP uygulamaları için SLES/SLES ile birlikte sunulan IBM Java sürümü ve OpenJDK paketi, gereken JavaFX. Properties dosyasını içermez. Bu çözüm, Oracle ve 8 ' i Oracle 'dan indirip yüklemektir.

OpenSUSE üzerinde OpenJDK ile ilgili benzer bir sorun hakkında daha fazla bilgi için bkz. [opensuse 42,1 artık için Discussion iş parçacığı SAPGUI 7,4 Java](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>SAP HANA el ile yüklemesi: SWPM
Bu bölümdeki ekran görüntülerinin serisi, sapa ile SWPM kullandığınızda SAP NetWeaver 7,5 ve SAP HANA SP12 'nin nasıl yükleneceğine ilişkin temel adımları gösterir. NetWeaver 7,5 yüklemesinin bir parçası olarak, SWPM Ayrıca HANA veritabanını tek bir örnek olarak yükleyebilir.

Örnek bir test ortamında, bir Gelişmiş Iş uygulaması programlama (ABAP) uygulama sunucusu yükledik. Aşağıdaki ekran görüntüsünde gösterildiği gibi, bir Azure sanal makinesine Ass ve birincil uygulama sunucusu örneklerini yüklemek için **Dağıtılmış Sistem** seçeneğini kullanıyoruz. Başka bir Azure VM 'de veritabanı sistemi olarak SAP HANA kullandık.

![Dağıtılmış Sistem seçeneği kullanılarak yüklenen yoks ve birincil uygulama sunucusu örnekleri](./media/hana-get-started/image012.jpg)

ASCS örneği App Server VM 'sine yüklendikten sonra, SAP yönetim konsolunda yeşil simgeyle tanımlanır. SAP profili dizinini içeren/sapmnt dizininin SAP HANA DB sunucusu VM 'si ile paylaşılması gerekir. DB yükleme adımının bu bilgilere erişmesi gerekir. Erişim sağlamanın en iyi yolu, YaST kullanılarak yapılandırılabilen NFS kullanmaktır.

![Yeşil bir simge kullanarak App Server VM 'de yüklü olan ASCS örneğini gösteren SAP](./media/hana-get-started/image016.jpg)

App Server VM 'sinde,/sapmnt dizini, **RW** ve **NO_ROOT_SQUASH** seçenekleri kullanılarak NFS aracılığıyla paylaşılır. Varsayılan değerler **ro** ve **root_squash**' dir. Bu, veritabanı örneğini yüklerken sorunlara yol açabilir.

![RW ve no_root_squash seçeneklerini kullanarak/sapmnt dizinini NFS aracılığıyla paylaşma](./media/hana-get-started/image017b.jpg)

Sonraki ekran görüntüsünde gösterildiği gibi, App Server VM 'sinden/sapmnt paylaşımının, **NFS istemcisi** ve yast KULLANıLARAK SAP HANA DB sunucusu sanal makinesinde yapılandırılması gerekir:

![NFS istemcisi kullanılarak yapılandırılan/sapmnt paylaşma](./media/hana-get-started/image018b.jpg)

Dağıtılmış bir NetWeaver 7,5 yüklemesi, yani bir **veritabanı örneği**gerçekleştirmek IÇIN SAP HANA DB sunucusu VM 'de oturum açın ve swpm 'yi başlatın:

![SAP HANA DB sunucusu VM 'de oturum açarak ve SWPM 'yi başlatarak bir veritabanı örneği yükleme](./media/hana-get-started/image019.jpg)

**Normal** yükleme ve yükleme medyası yolunu seçtikten sonra, bir veritabanı SID 'si, ana bilgisayar adı, örnek numarası ve DB sistem yöneticisi parolasını girin:

![SAP HANA veritabanı Sistem Yöneticisi oturum açma sayfası](./media/hana-get-started/image035b.jpg)

DBAKOKPIT şeması için parola girin:

![DBAKOKPIT şeması için parola girişi kutusu](./media/hana-get-started/image036b.jpg)

SAPABAP1 şema parolası için bir soru girin:

![SAPABAP1 şema parolası için bir soru girin](./media/hana-get-started/image037b.jpg)

Her görev tamamlandıktan sonra, DB yükleme işleminin her aşamasının yanında yeşil bir onay işareti görüntülenir. "Yürütme... iletisi Veritabanı örneği tamamlandı "görüntülenir.

![Onay iletisiyle görev tamamlandı penceresi](./media/hana-get-started/image023.jpg)

Yükleme başarılı olduktan sonra SAP yönetim konsolu, bir yeşil simgeyle birlikte DB örneğini de gösterir. Hdbindexserver ve hdbcompileserver gibi SAP HANA işlemlerin tam listesini görüntüler.

![SAP HANA işlemlerin listesi ile SAP Yönetimi konsol penceresi](./media/hana-get-started/image024.jpg)

Aşağıdaki ekran görüntüsünde, HANA yüklemesi sırasında SWPM tarafından oluşturulan/Hana/Shared Directory altında dosya yapısının bölümleri gösterilmektedir. Farklı bir yol belirtme seçeneği olmadığından, SWPM kullanarak SAP HANA yüklemeden önce/Hana dizininin altına ek disk alanı bağlamanız önemlidir. Bu adım, kök dosya sisteminin boş alan boşaltmasını önler.

![HANA yüklemesi sırasında oluşturulan/Hana/Shared Dizin dosyası yapısı](./media/hana-get-started/image025.jpg)

Bu ekran görüntüsü/usr/SAP dizininin dosya yapısını gösterir:

![/Usr/SAP Dizin dosyası yapısı](./media/hana-get-started/image026.jpg)

Dağıtılmış ABAP yüklemesinin son adımı, birincil uygulama sunucusu örneğini yüklemektir:

![Son adım olarak birincil uygulama sunucusu örneğini gösteren ABAP yüklemesi](./media/hana-get-started/image027b.jpg)

Birincil uygulama sunucusu örneği ve SAP GUI yüklendikten sonra, SAP HANA yüklemesinin doğru şekilde tamamlandığını onaylamak için **DBA kokpiti** işlemini kullanın:

![Başarılı yüklemeyi onaylayan DBA kokpiti penceresi](./media/hana-get-started/image028b.jpg)

Son bir adım olarak, öncelikle SAP App Server VM 'ye HANA Studio 'Yu yüklemek isteyebilirsiniz. Ardından, VERITABANı sunucusu VM 'sinde çalışan SAP HANA örneğine bağlanın.

![SAP App Server VM 'de SAP HANA Studio 'Yu yükleme](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>SAP HANA el ile yüklemesi: HDBLCM
SWPM kullanarak dağıtılmış bir yüklemenin parçası olarak SAP HANA yüklemenin yanı sıra, ilk olarak, HDBLCM kullanarak HANA 'yı yükleyebilirsiniz. Örneğin, SAP NetWeaver 7,5 'yi yükleyebilirsiniz. Bu bölümdeki ekran görüntüleri, bu işlemin nasıl çalıştığını gösterir.

HANA HDBLCM aracı hakkında daha fazla bilgi için bkz.:

* [Göreviniz için doğru SAP HANA HDBLCM 'Yi seçin](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [SAP HANA yaşam döngüsü yönetim araçları](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [Sunucu yüklemesi ve güncelleştirme kılavuzu SAP HANA](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

HDBLCM aracı tarafından oluşturulan `\<HANA SID\>adm user` için varsayılan Grup KIMLIĞI ayarıyla ilgili sorunlardan kaçınmak istiyorsunuz. HDBLCM aracılığıyla SAP HANA yüklemeden önce, Grup KIMLIĞI `1001` kullanarak `sapsys` adlı yeni bir grup tanımlayın:

![Grup KIMLIĞI 1001 kullanılarak tanımlanan yeni "sapsys" grubu](./media/hana-get-started/image030.jpg)

HDBLCM 'yi ilk kez başlattığınızda basit bir başlangıç menüsü görüntülenir. Öğe 1 ' i seçin, **Yeni sistem 'ı yükler**:

![HDBLCM başlangıç penceresinde "yeni sistem 'i yükler" seçeneği](./media/hana-get-started/image031.jpg)

Aşağıdaki ekran görüntüsünde, daha önce seçtiğiniz tüm anahtar seçenekleri görüntülenir.

> [!IMPORTANT]
> HANA günlük ve veri birimleri için adlandırılmış dizinler ve bu örnekte/Hana/Shared olan yükleme yolu ve/usr/SAP kök dosya sisteminin bir parçası olmamalıdır. Bu dizinler, VM 'ye eklenmiş olan Azure Veri disklerine aittir. Daha fazla bilgi için "disk kurulumu" bölümüne bakın. 

Bu yaklaşım, kök dosya sisteminin yer kaplamasını önlemeye yardımcı olur. HANA sistem yöneticisinin Kullanıcı KIMLIĞI `1005` olduğunu ve yüklemeden önce tanımlanan KIMLIK `1001` olan `sapsys` grubunun bir parçası olduğunu unutmayın.

![Daha önce seçilen tüm anahtar SAP HANA bileşenleri listesi](./media/hana-get-started/image032.jpg)

/Etc/passwd dizinindeki `\<HANA SID\>adm user` ayrıntılarını kontrol edin. Aşağıdaki ekran görüntüsünde gösterildiği gibi `azdadm` arayın:

![HANA \<HANA SID \>adm/etc/passwd dizininde listelenen kullanıcı ayrıntıları](./media/hana-get-started/image033.jpg)

SAP HANA, HDBLCM kullanarak yükledikten sonra, aşağıdaki ekran görüntüsünde gösterildiği gibi, dosya yapısını SAP HANA Studio 'da görebilirsiniz. Tüm SAP NetWeaver tablolarını içeren SAPABAP1 şeması henüz kullanılabilir değil.

![SAP HANA Studio 'da SAP HANA dosya yapısı](./media/hana-get-started/image034.jpg)

SAP HANA yükledikten sonra, SAP NetWeaver 'yi üzerine yükleyebilirsiniz. Aşağıdaki ekran görüntüsünde gösterildiği gibi yükleme, SWPM kullanılarak dağıtılmış bir yükleme olarak gerçekleştirilmemiştir. Bu işlem, önceki bölümde açıklanmaktadır. SWPM kullanarak veritabanı örneğini yüklediğinizde, HDBLCM kullanarak aynı verileri girersiniz. Örneğin, ana bilgisayar adı, HANA SID ve örnek numarasını girersiniz. SWPM daha sonra mevcut HANA yüklemesini kullanır ve daha fazla şema ekler.

![SWPM kullanılarak gerçekleştirilen dağıtılmış yükleme](./media/hana-get-started/image035b.jpg)

Aşağıdaki ekran görüntüsünde, DBAKOKPIT şeması hakkında veri girebileceğiniz SWPM yükleme adımı gösterilmektedir:

![DBAKOKPIT şema verilerinin girildiği SWPM yükleme adımı](./media/hana-get-started/image036b.jpg)

SAPABAP1 şeması hakkında veri girin:

![SAPABAP1 şeması hakkında veri girme](./media/hana-get-started/image037b.jpg)

SWPM veritabanı örneği yüklemesi tamamlandıktan sonra, SAP HANA Studio 'da SAPABAP1 şemasını görebilirsiniz:

![SAP HANA Studio 'daki SAPABAP1 şeması](./media/hana-get-started/image038b.jpg)

Son olarak, SAP App Server ve SAP GUI yüklemeleri bittikten sonra, **DBA kokpiti** IŞLEMINI kullanarak Hana DB örneğini doğrulayın:

![HANA VERITABANı örneği, DBA kokpiti işlemi ile doğrulandı](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP Yazılım İndirmeleri
Aşağıdaki ekran görüntülerinde gösterildiği gibi SAP hizmeti marketi 'nden yazılım indirebilirsiniz.

Linux/HANA için NetWeaver 7,5 'yi indirin:

 ![NetWeaver 7,5 indirmek için SAP hizmeti yükleme ve yükseltme penceresi](./media/hana-get-started/image001.jpg)

HANA SP12 platform sürümünü indirin:

 ![HANA SP12 platform Edition indirmek için SAP hizmeti yükleme ve yükseltme penceresi](./media/hana-get-started/image002.jpg)

