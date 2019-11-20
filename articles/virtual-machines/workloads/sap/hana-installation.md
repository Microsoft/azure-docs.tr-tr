---
title: Azure 'da SAP HANA SAP HANA yüklemesi (büyük örnekler) | Microsoft Docs
description: Azure 'da bir SAP HANA SAP HANA nasıl yüklenir (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 516f61775060b3e4073ed9d623545d4f227563ed
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750352"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Azure 'da SAP HANA (büyük örnekler) yüklemek ve yapılandırmak

Bu makaleyi okumadan önce, [Hana büyük örnekler ortak şartlarını](hana-know-terms.md) ve [Hana büyük örnekler SKU 'larını](hana-available-skus.md)öğrenirsiniz.

SAP HANA yüklemesi sizin sorumluluğunuzdadır. Azure sanal ağlarınız ve HANA büyük örnek birimleri arasında bağlantı kurduktan sonra Azure (büyük örnekler) sunucusuna yeni bir SAP HANA yüklemeye başlayabilirsiniz. 

> [!Note]
> SAP ilkesi başına, SAP HANA yüklemesi, sertifikalı SAP teknolojisinin sınavına, yükleme sertifika sınavına SAP HANA veya SAP sertifikalı Sistem Tümleştirici (sı) olan kim olduğunu düzenleyen bir kişi tarafından gerçekleştirilmelidir.

HANA 2,0 yüklemeyi planladığınızda, bkz. [sap destek notunun #2235581-SAP HANA: desteklenen işletim sistemleri](https://launchpad.support.sap.com/#/notes/2235581/E) , işletim sistemlerinin yüklemekte olduğunuz SAP HANA sürümü ile desteklendiğinden emin olun. HANA 2,0 için desteklenen işletim sistemi, HANA 1,0 için desteklenen işletim sisteminden daha kısıtlayıcıdır. 

> [!IMPORTANT] 
> Tür II birimleri için şu anda yalnızca SLES 12 SP2 işletim sistemi sürümü destekleniyor. 

HANA yüklemesine başlamadan önce aşağıdakileri doğrulayın:
- [HLI birim](#validate-the-hana-large-instance-units)
- [İşletim sistemi yapılandırması](#operating-system)
- [Ağ yapılandırması](#networking)
- [Depolama yapılandırması](#storage)


## <a name="validate-the-hana-large-instance-units"></a>HANA büyük örnek birimlerini doğrulama

Microsoft 'tan HANA büyük örnek birimini aldıktan sonra, aşağıdaki ayarları doğrulayın ve gereken şekilde ayarlayın.

**İlk adım** , Hana büyük örneğini aldıktan ve örneklere erişim ve bağlantı kurmaya başladıktan sonra örneklerin doğru SKU ve işletim sistemi ile görüntülenip görüntülenmeyeceğini Azure Portal iade ediyor. Denetimleri gerçekleştirmek için gerekli adımlar için [Azure Portal aracılığıyla Azure Hana büyük örnekler denetimini](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal) okuyun.

**İkinci adım** , Hana büyük örneğini aldıktan ve örneklere erişim ve bağlantı kurmaya başladıktan sonra örnek işletim sistemini işletim sistemi sağlayıcınıza kaydetmedir. Bu adım, SUSE Linux işletim sistemini Azure 'da bir VM 'ye dağıtılan SUSE SMT örneğine kaydetmeyi içerir. 

HANA büyük örnek birimi bu SMT örneğine bağlanabilir. (Daha fazla bilgi için bkz. [SUSE Linux IÇIN smt Server ayarlama](hana-setup-smt.md)). Alternatif olarak, kırmızı hat işletim sisteminin bağlanmanız gereken Red Hat abonelik Yöneticisi 'ne kayıtlı olması gerekir. Daha fazla bilgi için bkz. Azure 'da [SAP HANA nedir (büyük örnekler)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Bu adım, müşterinin sorumluluğu olan işletim sistemine yönelik düzeltme eki uygulama için gereklidir. SUSE için, SMT [yüklemesi](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)hakkında bu sayfada smt yükleme ve yapılandırma belgelerini bulun.

**Üçüncü adım** , belirli işletim sistemi sürümü/sürümünün yeni düzeltme eklerini ve düzeltmelerini denetmasıdır. HANA büyük örneğinin düzeltme eki düzeyinin en son durumda olduğunu doğrulayın. En son düzeltme eklerinin dahil olmadığı durumlar olabilir. HANA büyük örnek birimini aldıktan sonra, düzeltme eklerinin uygulanıp uygulanamayacağını denetlemeniz zorunludur.

**Dördüncü adım** , belirli işletim sistemi sürümüne/sürümüne SAP HANA yüklemek ve yapılandırmak IÇIN ilgili SAP notlarını denetletmelidir. Microsoft, tek tek yükleme senaryolarına bağlı olan veya SAP notları veya yapılandırmalarında değişiklik yaptığı öneriler ya da değişiklikler nedeniyle her zaman bir HANA büyük örnek birimini kusursuz bir şekilde yapılandıramaz. 

Bu nedenle, tam Linux sürümü için SAP HANA ilgili SAP notlarını okumak için bir müşteri olarak gereklidir. Ayrıca, işletim sistemi sürümü/sürümünün yapılandırmalarını denetleyin ve henüz yapmadıysanız yapılandırma ayarlarını uygulayın.

Özellikle, aşağıdaki parametreleri kontrol edin ve şu şekilde ayarlayın:

- net. Core. rmem_max = 16777216
- net. Core. wmem_max = 16777216
- net. Core. rmem_default = 16777216
- net. Core. wmem_default = 16777216
- net. Core. optmem_max = 16777216
- net. IPv4. TCP _rmem = 65536 16777216 16777216
- net. IPv4. TCP _wmem = 65536 16777216 16777216

SLES12 SP1 ve RHEL 7,2 ile başlayarak, bu parametrelerin/etc/sysctl.exe dizinindeki bir yapılandırma dosyasında ayarlanması gerekir. Örneğin, 91-NetApp-HANA. conf adına sahip bir yapılandırma dosyası oluşturulmalıdır. Daha eski SLES ve RHEL yayınları için bu parametrelerin ayarlandığı/vs/sysctl. conf olması gerekir.

RHEL 6,3 ile başlayan tüm RHEL yayınları için şunları göz önünde bulundurun: 
- Sunrpc. TCP _slot_table_entries = 128 parametresi/vs/modaraştırması. d/sunrpc-Local. conf olarak ayarlanmalıdır. Dosya yoksa, önce girdiyi ekleyerek oluşturmanız gerekir: 
    - Seçenekler sunrpc tcp_max_slot_table_entries = 128

**Beşinci adım** , Hana büyük örnek biriminizdeki sistem saatini denetünündir. Örnekler bir sistem saat dilimiyle dağıtılır. Bu saat dilimi, HANA büyük örnek damgasının bulunduğu Azure bölgesinin konumunu temsil eder. Sahip olduğunuz örneklerin sistem saatini veya saat dilimini değiştirebilirsiniz. 

Kiracınıza daha fazla örnek sipariş ederseniz, yeni teslim edilen örneklerin saat dilimini uyarlamanız gerekir. Microsoft, devreden Multipath sonrasında örneklerle ayarladığınız sistem saati dilimine ilişkin bir öngörü yoktur. Bu nedenle, yeni dağıtılan örnekler, değiştirdiğiniz ile aynı saat diliminde ayarlanmayabilir. Bu, gerektiğinde geçen örneklerin saat dilimini uyarlamak için müşteri olarak sizin sorumluluğunuzdadır. 

**Altıncı adım** , vb/Konakları denetme. Dikey pencereler üzerinden devredildiği gibi, farklı amaçlar için atanan farklı IP adresleri vardır. Vb/Hosts dosyasını denetleyin. Birimler var olan bir kiracıya eklendiğinde, daha önce teslim edilen sistemlerin IP adresleriyle yeni dağıtılan sistemlerin vb/konaklarının doğru şekilde tutulmasını beklemeyin. Müşteri, yeni dağıtılan bir örneğin kiracınızda daha önce dağıttığınız birimlerin adlarını etkileşime girmesine ve çözümleyebildiğinden emin olmak için müşterinin sorumluluğundadır. 


## <a name="operating-system"></a>İşletim sistemi

> [!IMPORTANT] 
> Tür II birimleri için şu anda yalnızca SLES 12 SP2 işletim sistemi sürümü desteklenmektedir. 

Teslim edilen işletim sistemi görüntüsünün takas alanı, [sap destek notuna #1999997-SSS: SAP HANA bellek](https://launchpad.support.sap.com/#/notes/1999997/E)' e göre 2 GB olarak ayarlanır. Bir müşteri olarak, farklı bir ayar istiyorsanız kendiniz ayarlamanız gerekir.

[SAP uygulamaları için SUSE Linux Enterprise Server 12 SP1](https://www.suse.com/products/sles-for-sap/download/) , Azure 'da SAP HANA Için yüklenen Linux 'un (büyük örnekler) dağıtımı olur. Bu dağıtım, SAP 'ye özgü "kullanıma hazır" özellikleri (SLES 'de SAP çalıştırmaya yönelik önceden ayarlanmış parametreler dahil) sağlar.

SLES 'de SAP HANA dağıtımıyla ilgili çok sayıda faydalı kaynak (yüksek kullanılabilirlik ve güvenlik sağlamlaştırma dahil) için bkz. SUSE Web sitesinde [Kaynak Kitaplığı/teknik incelemeler](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) ve SAP Community ağı 'NDA (SCN) [SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) belirli SAP işlemlerine ve daha fazlasına).

Aşağıda SUSE ile ilgili bağlantılarda ek ve yararlı SAP verilmiştir:

- [SUSE Linux sitesinde SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP için en iyi uygulamalar: sıraya alma çoğaltması-SUSE Linux Enterprise 12 üzerinde SAP NetWeaver](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [Clamsap – SAP IÇIN SLES Virus koruması](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (SAP uygulamaları için SLES 12 dahil)

Aşağıda, SLES 12 ' de SAP HANA uygulamak için geçerli olan SAP destek notları verilmiştir:

- [SAP destek notuna #1944799 – SLES işletim sistemi yüklemesi için SAP HANA yönergeleri](http://service.sap.com/sap/support/notes/1944799)
- [SAP destek notunun #2205917 – SAP uygulamaları için SLES 12 için önerilen işletim sistemi ayarlarını SAP HANA](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP destek notu #1984787 – SUSE Linux Enterprise Server 12: yükleme notları](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP destek notunun #171356 – Linux 'ta SAP Software: genel bilgiler](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP destek notunun #1391070 – Linux UUID çözümleri](https://launchpad.support.sap.com/#/notes/1391070)

[SAP HANA için Red Hat Enterprise Linux](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) , Hana büyük örneklerde SAP HANA çalıştırmaya yönelik başka bir tekliftir. RHEL 6,7 ve 7,2 sürümleri mevcuttur. Yalnızca RHEL 7,2 ve daha yeni sürümlerin desteklendiği yerel Azure VM 'lerinin tersidir, HANA büyük örnekleri de RHEL 6,7 ' i destekler. Ancak, bir RHEL 7. x sürümü kullanmanızı öneririz.

Aşağıda, Red Hat ile ilgili bağlantılar üzerinde ek faydalı SAP verilmiştir:
- [Red Hat Linux sitesinde SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Aşağıda, Red hat üzerinde SAP HANA uygulamak için geçerli olan SAP destek notları verilmiştir:

- [SAP destek notunun #2009879-Red Hat Enterprise Linux (RHEL) işletim sistemi için SAP HANA yönergeleri](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP destek notunun #2292690-SAP HANA DB: RHEL 7 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP destek notunun #2247020-SAP HANA DB: RHEL 6,7 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/2247020)
- [SAP destek notunun #1391070 – Linux UUID çözümleri](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP destek notunun #2228351-Linux: SAP HANA veritabanı SPS 11 Düzeltme 110 (veya üzeri) RHEL 6 veya SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [SAP destek notunun #2397039-SSS: RHEL üzerinde SAP](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP destek notunun #1496410-Red Hat Enterprise Linux 6. x: yükleme ve yükseltme](https://launchpad.support.sap.com/#/notes/1496410)
- [SAP destek notunun #2002167-Red Hat Enterprise Linux 7. x: yükleme ve yükseltme](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Zaman eşitleme

SAP NetWeaver mimarisinde oluşturulan SAP uygulamaları, SAP sistemini oluşturan çeşitli bileşenlere yönelik zaman farklılıklarına duyarlıdır. SAP ABAP kısa dökümler\_\_ZDATE hata başlığına sahiptir\_fark büyük olasılıkla tanıdık gelecektir. Çünkü bu kısa dökümler, farklı sunucuların veya VM 'lerin sistem saatinin çok fazla ayrı olarak çizmediği durumlarda görüntülenmesidir.

Azure 'da (büyük örnekler) SAP HANA için, Azure 'da gerçekleştirilen zaman eşitlemesi, büyük örnek Damgalarında işlem birimlerine uygulanmaz. Bu eşitleme, yerel Azure VM 'lerinde SAP uygulamalarının çalıştırılması için geçerli değildir, çünkü Azure bir sistem zamanının doğru şekilde eşitlendiğinden emin olmanızı sağlar. 

Sonuç olarak, Azure VM 'lerde çalışan SAP uygulama sunucuları tarafından ve HANA büyük örneklerde çalışan SAP HANA veritabanı örnekleri tarafından kullanılabilecek ayrı bir zaman sunucusu ayarlamanız gerekir. Büyük örnek damgalarındaki depolama altyapısı zaman zaman NTP sunucularıyla eşitlenir.


## <a name="networking"></a>Networking (Ağ İletişimi)
Aşağıdaki belgelerde açıklandığı gibi, Azure sanal ağlarınızı tasarlama ve bu sanal ağları HANA büyük örneklerine bağlama bölümündeki önerileri izletireceğiz varsayılmaktadır:

- [Azure 'da SAP HANA (büyük örnek) genel bakış ve mimari](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure 'da SAP HANA (büyük örnekler) altyapı ve bağlantı](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Tek birimlerin ağıyla ilgili bazı ayrıntılar vardır. Her HANA büyük örnek birimi iki veya üç NIC bağlantı noktasına atanan iki veya üç IP adresiyle birlikte gelir. HANA genişleme yapılandırmalarında ve HANA sistem çoğaltma senaryosunda üç IP adresi kullanılır. Birimin NIC 'sine atanan IP adreslerinden biri, [Azure 'daki SAP HANA (büyük örnekler) genel bakış ve mimaride](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)AÇıKLANAN sunucu IP havuzunun dışındadır.

Mimarinizin Ethernet ayrıntıları hakkında daha fazla bilgi için bkz. [HLI desteklenen senaryolar](hana-supported-scenario.md).

## <a name="storage"></a>Depolama

Azure 'da SAP HANA için depolama düzeni (büyük örnekler), SAP tarafından önerilen yönergeler aracılığıyla SAP HANA Azure `service management` ile yapılandırılır. Bu yönergeler [SAP HANA depolama gereksinimleri](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) teknik incelemesi bölümünde belgelenmiştir. 

Farklı HANA büyük örnekler SKU 'Larının bulunduğu farklı birimlerin kaba boyutları, [Azure 'daki SAP HANA (büyük örnekler) genel bakış ve mimaride](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)açıklanmaktadır.

Depolama birimlerinin adlandırma kuralları aşağıdaki tabloda listelenmiştir:

| Depolama alanı kullanımı | Bağlama adı | Birim adı | 
| --- | --- | ---|
| HANA verileri | /Hana/Data/SID/mnt0000\<a > | Depolama IP 'si:/hana_data_SID_mnt00001_tenant_vol |
| HANA günlüğü | /Hana/log/SID/mnt0000\<a > | Depolama IP 'si:/hana_log_SID_mnt00001_tenant_vol |
| HANA günlük yedeklemesi | /Hana/log/Backups | Depolama IP 'si:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA paylaşılan | /hana/shared/SID | Depolama IP 'si:/hana_shared_SID_mnt00001_tenant_vol/Shared |
| usr/SAP | /Usr/SAP/SID | Depolama IP 'si:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* , Hana örnek sistem kimliğidir. 

*Kiracı* , kiracı dağıtımında bir işlem iç numaralandırmadır.

HANA usr/SAP aynı birimi paylaşır. Bağlama noktalarının terminolojisi, HANA örneklerinin sistem KIMLIĞINI ve takma numarasını içerir. Ölçek dağıtımında, mnt00001 gibi yalnızca bir bağlama vardır. Genişleme dağıtımlarında, diğer yandan, çalışan ve ana düğümlere sahip olduğunuz kadar çok sayıda takma olduğunu görürsünüz. 

Genişleme ortamları, veri, günlük ve günlük yedekleme birimleri, genişleme yapılandırmasındaki her bir düğüme paylaşılır ve eklenir. Birden çok SAP örneği olan yapılandırmalarda, farklı bir birim kümesi oluşturulup HANA büyük örnek birimine iliştirilir. Senaryonuza yönelik depolama düzeni ayrıntıları için bkz. [hLi tarafından desteklenen senaryolar](hana-supported-scenario.md).

Bir HANA büyük örnek birimine baktığınızda, birimlerin HANA/veriler için genel disk hacminde ve bir birim HANA/günlük/yedekleme olduğunu fark etmiş olursunuz. HANA/verileri çok büyük hale yaptığımız nedeni, size müşteri olarak sunduğumuz depolama anlık görüntülerinin aynı disk birimini kullanıyor olması nedenidir. Gerçekleştirdiğiniz daha fazla depolama anlık görüntüsü, atanan depolama birimlerinizde anlık görüntüler tarafından daha fazla alan tüketilecektir. 

HANA/günlük/yedekleme biriminin veritabanı yedeklemeleri için birim olmaması gerekir. HANA işlem günlüğü yedeklemeleri için yedekleme birimi olarak kullanılacak şekilde boyutlandırılır. Daha fazla bilgi için bkz. [Azure 'da SAP HANA (büyük örnekler) yüksek kullanılabilirlik ve olağanüstü durum kurtarma](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Sağlanmış depolama alanının yanı sıra, 1 TB 'lik artışlarla ek depolama kapasitesi satın alabilirsiniz. Bu ek depolama alanı, bir HANA büyük örneğine yeni birimler olarak eklenebilir.

Azure `service management`SAP HANA ile ekleme sırasında, müşteri sıdaddm kullanıcısı ve sapsys grubu için bir kullanıcı KIMLIĞI (UID) ve Grup KIMLIĞI (GID) belirtir (örneğin: 1.000.500). SAP HANA sisteminin yüklenmesi sırasında aynı değerleri kullanmanız gerekir. Bir birimde birden çok HANA örneği dağıtmak istiyorsanız, birden çok birim kümesi (her örnek için bir küme) alırsınız. Sonuç olarak, dağıtım zamanında şunu tanımlamanız gerekir:

- Farklı HANA örneklerinin SID 'SI (sıdaddm bundan türetilir).
- Farklı HANA örneklerinin bellek boyutları. Örnek başına bellek boyutu, her bir birim kümesindeki birimlerin boyutunu tanımlar.

Depolama sağlayıcısı önerilerine bağlı olarak, tüm takılan birimler için aşağıdaki bağlama seçenekleri yapılandırılır (önyükleme LUN 'U hariç tutar):

- NFS RW, sunucular = 4, hard, Timeo = 600, rboyut = 1048576, wsize = 1048576, INTR, noatime, Lock 0 0

Bu bağlama noktaları, aşağıdaki grafiklerde gösterildiği gibi/etc/fstab içinde yapılandırılır:

![HANA büyük örnek birimi 'nde bağlı birimlerin fstab](./media/hana-installation/image1_fstab.PNG)

S72m HANA büyük örnek birimi üzerindeki df-h komutunun çıktısı şöyle görünür:

![HANA büyük örnek birimi 'nde bağlı birimlerin fstab](./media/hana-installation/image2_df_output.PNG)


Depolama denetleyicisi ve büyük örnek damgalarının düğümleri NTP sunucularıyla eşitlenir. Azure (büyük örnekler) birimleri ve Azure VM 'lerinde bir NTP sunucusuna karşı SAP HANA eşitlediğinizde, altyapı ile Azure veya büyük örnek damgalar arasında önemli bir zaman kayması olmamalıdır.

SAP HANA aşağıda kullanılan depolama alanına iyileştirmek için aşağıdaki SAP HANA yapılandırma parametrelerini ayarlayın:

- max_parallel_io_requests 128
- async_read_submit
- async_write_submit_active
- async_write_submit_blocks
 
SPS12 SAP HANA 1,0 sürümleri için, bu parametreler SAP HANA veritabanının yüklemesi sırasında, [SAP note #2267798-SAP HANA veritabanının yapılandırması](https://launchpad.support.sap.com/#/notes/2267798)bölümünde açıklandığı gibi ayarlanabilir.

Ayrıca, hdbparam çerçevesini kullanarak SAP HANA veritabanı yüklemesinden sonra parametreleri yapılandırabilirsiniz. 

HANA büyük örneklerde kullanılan depolamanın dosya boyutu sınırlaması vardır. [Boyut sınırlaması dosya başına 16 TB 'tır](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) . EXT3 dosya sistemlerindeki dosya boyutu sınırlamalarından farklı olarak, HANA, HANA büyük örnekler depolaması tarafından zorlanan depolama sınırlamasını açıkça bilmez. Sonuç olarak, 16 TB dosya boyutu sınırına ulaşıldığında HANA otomatik olarak yeni bir veri dosyası oluşturmaz. HANA, 16 TB 'ın ötesinde dosyayı büyümeye çalışır, HANA hataları rapor eder ve dizin sunucusu sonda çökecektir.

> [!IMPORTANT]
> HANA büyük örnek depolama alanının 16 TB 'lık dosya boyutu sınırının ötesinde veri dosyalarını büyümeye çalışmasını engellemek için SAP HANA Global. ini yapılandırma dosyasında aşağıdaki parametreleri ayarlamanız gerekir
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - Ayrıca bkz. SAP Note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285) farkında olun


SAP HANA 2,0 ile hdbparam çerçevesi kullanım dışı bırakılmıştır. Sonuç olarak, parametrelerin SQL komutları kullanılarak ayarlanması gerekir. Daha fazla bilgi için bkz. [SAP note #2399079: Hana 2 ' de hdbparam 'ın eleme](https://launchpad.support.sap.com/#/notes/2399079).

Mimarinizin depolama düzeni hakkında daha fazla bilgi edinmek için, [HLI desteklenen senaryolar](hana-supported-scenario.md) bölümüne bakın.


**Sonraki adımlar**

- [HLI 'Da Hana yüklemesine](hana-example-installation.md) başvurun










































 







 




