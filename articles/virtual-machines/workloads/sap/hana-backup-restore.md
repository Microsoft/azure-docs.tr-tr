---
title: Azure 'da SAP HANA HANA yedekleme ve geri yükleme (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA HANA yedeklemesini gerçekleştirme ve geri yükleme (büyük örnekler)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430084"
---
# <a name="backup-and-restore"></a>Yedekleme ve geri yükleme

>[!IMPORTANT]
>Bu makale SAP HANA yönetimi belgelerinin veya SAP notlarının yerini almaz. Özellikle yedekleme, geri yükleme, yüksek kullanılabilirlik ve olağanüstü durum kurtarma için SAP HANA yönetim ve işlemlerde, uzmanlığın iyi bir şekilde anlaşılmasını umuz. Bu makalede SAP HANA Studio 'daki ekran görüntüleri gösterilir. İçerik, yapı ve SAP yönetim araçlarının ekranlarının doğası ve araçların kendisi SAP HANA sürümden sürüme değişebilir.

Ortamınızda ve HANA sürümlerinizle ve yayınlarınız ile alınan adımları ve işlemleri uygulamanız önemlidir. Bu makalede açıklanan bazı süreçler daha iyi bir genel anlamak için basitleştirilmiştir. Bu kişiler, son işlem el kitapları için ayrıntılı adımlar olarak kullanılmak üzere tasarlanmamıştır. Yapılandırmalara yönelik işlem el defterleri oluşturmak istiyorsanız işlemlerinizi test edin ve yapın ve özel Yapılandırmalarınızla ilgili işlemleri belgeleyin. 

İşletim veritabanlarının en önemli yönlerinden biri, onları çok zararlı etkinliklerden korumalıdır. Bu olayların nedeni doğal felaketlerden basit kullanıcı hatalarına kadar herhangi bir şey olabilir.

Bir veritabanını yedekleme, bazı önemli verileri silmeden önce, bu dosyayı zaman içinde herhangi bir noktaya geri yükleme olanağı vererek, kesintiye uğramadan önceki bir duruma geri yükleme olanağı sağlar.

Geri yükleme özelliğini sağlamak için iki yedekleme türü gerçekleştirilmelidir:

- Veritabanı yedeklemeleri: tam, artımlı veya fark yedeklemeleri
- İşlem günlüğü yedeklemeleri

Bir uygulama düzeyinde gerçekleştirilen tam veritabanı yedeklerinin yanı sıra, depolama anlık görüntüleri ile yedeklemeler de gerçekleştirebilirsiniz. Depolama anlık görüntüleri, işlem günlüğü yedeklemelerini değiştirmez. İşlem günlüğü yedeklemeleri, veritabanını belirli bir noktaya geri yüklemek veya zaten kaydedilmiş işlemlerden günlükleri boşaltmak için önemli olmaya devam eder. Depolama anlık görüntüleri, veritabanının geri alma görüntüsünü hızlı bir şekilde sağlayarak kurtarmayı hızlandırabilir. 

Azure 'da SAP HANA (büyük örnekler) iki yedekleme ve geri yükleme seçeneği sunar:

- **Kendiniz yapın (DIY).** Yeterli disk alanı olduğundan emin olduktan sonra, aşağıdaki disk yedekleme yöntemlerinden birini kullanarak tam veritabanı ve günlük yedeklemeleri gerçekleştirin. Yalnızca HANA büyük örnek birimlerine veya bir Azure sanal makinesinde (VM) ayarlanan NFS paylaşımlarına bağlı birimlere doğrudan yedekleyebilirsiniz. İkinci durumda, müşteriler Azure 'da bir Linux sanal makinesi ayarlayıp VM 'ye Azure Storage 'ı ekler ve bu VM 'deki yapılandırılmış bir NFS sunucusu aracılığıyla depolama alanını paylaşır. Yedeklemeyi doğrudan HANA büyük örnek birimlerine eklenen birimlerde gerçekleştirirseniz, yedeklemeleri bir Azure depolama hesabına kopyalayın. Azure depolama alanını temel alan NFS paylaşımlarını dışarı aktaran bir Azure VM ayarladıktan sonra bunu yapın. Azure Backup kasasını veya Azure soğuk depolamayı da kullanabilirsiniz. 

   Diğer bir seçenek de, bir Azure depolama hesabına kopyalandıktan sonra yedeklemeleri depolamak için üçüncü taraf veri koruma aracı kullanmaktır. Ayrıca, uyumluluk ve denetim amaçlarıyla daha uzun süreler için depolamanız gereken veriler için DIY yedekleme seçeneği de gerekebilir. Her durumda yedeklemeler, VM ve Azure depolama aracılığıyla temsil edilen NFS paylaşımlarına kopyalanır.

- **Altyapı yedekleme ve geri yükleme işlevselliği.** Ayrıca, Azure 'daki SAP HANA temel altyapısının (büyük örnekler) sağladığı yedekleme ve geri yükleme işlevini de kullanabilirsiniz. Bu seçenek, yedeklemeler ve hızlı geri yüklemeler gereksinimini karşılar. Bu bölümün geri kalanı, HANA büyük örneklerle sunulan yedekleme ve geri yükleme işlevlerine yöneliktir. Bu bölüm ayrıca, yedekleme ve geri yükleme 'nin HANA büyük örnekler tarafından sunulan olağanüstü durum kurtarma işlevselliğine sahip olduğu ilişkiyi de ele alır.

> [!NOTE]
>   HANA büyük örneklerinin temel altyapısı tarafından kullanılan anlık görüntü teknolojisinin SAP HANA anlık görüntülerine bağımlılığı vardır. Bu noktada, SAP HANA anlık görüntüler SAP HANA çok kiracılı veritabanı kapsayıcılarının birden fazla kiracısıyla birlikte çalışmaz. Yalnızca bir kiracı dağıtılmışsa SAP HANA anlık görüntüler çalışır ve bu yöntemi kullanabilirsiniz.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Azure 'da SAP HANA depolama anlık görüntülerini kullanın (büyük örnekler)

Azure 'da SAP HANA temel alan depolama altyapısı (büyük örnekler) birimlerin depolama anlık görüntülerini destekler. Birimleri yedekleme ve geri yükleme işlemleri aşağıdaki noktalara göre desteklenir:

- Tam veritabanı yedeklemeleri yerine, depolama birimi anlık görüntüleri sıklıkla alınır.
- /Hana/Data üzerinden bir anlık görüntü tetiklenir ve/usr/SAP, birimler içeren/Hana/Shared ise, anlık görüntü teknolojisi depolama anlık görüntüsünü çalıştırmadan önce bir SAP HANA anlık görüntüsü başlatır. Bu SAP HANA anlık görüntüsü, depolama anlık görüntüsünün kurtarmasından sonra nihai günlük geri yüklemeler için kurulum noktasıdır. Bir HANA anlık görüntüsünün başarılı olabilmesi için etkin bir HANA örneğine ihtiyacınız vardır. Bir HSR senaryosunda, bir HANA anlık görüntüsünün gerçekleştirilemediği geçerli bir ikincil düğümde depolama anlık görüntüsü desteklenmez.
- Depolama anlık görüntüsü başarıyla çalıştıktan sonra SAP HANA anlık görüntüsü silinir.
- İşlem günlüğü yedeklemeleri sıklıkla alınır ve/Hana/logbackups biriminde veya Azure 'da depolanır. İşlem günlüğü yedeklerini içeren/Hana/logbackups birimini ayrı olarak bir anlık görüntü alacak şekilde tetikleyebilirsiniz. Bu durumda, bir HANA anlık görüntüsü çalıştırmanız gerekmez.
- Bir veritabanını belirli bir zaman noktasına geri yüklemeniz gerekiyorsa, bir üretim kesintisi için Azure geri yükleme üzerinde Microsoft Azure destek veya SAP HANA belirli bir depolama anlık görüntüsüne ister. Bir korumalı alan sisteminin özgün durumuna yönelik planlı bir geri yükleme örneğidir.
- Depolama anlık görüntüsüne dahil olan SAP HANA anlık görüntüsü, çalıştıran ve depolama anlık görüntüsü alındıktan sonra depolanan işlem günlüğü yedeklemelerini uygulamak için bir konum noktasıdır.
- Bu işlem günlüğü yedeklemeleri, veritabanını belirli bir zaman noktasına geri yüklemek için alınır.

Üç birim sınıfını hedefleyen depolama anlık görüntüleri gerçekleştirebilirsiniz:

- /Hana/Data ve/Hana/Shared ile/usr/sapde içeren Birleşik bir anlık görüntü. Bu anlık görüntü, depolama anlık görüntüsüne hazırlık olarak bir SAP HANA anlık görüntüsünün oluşturulmasını gerektirir. SAP HANA anlık görüntüsü, veritabanının bir depolama noktasından tutarlı bir durumda olmasını sağlar. Geri yükleme işlemi için, bu, üzerine ayarlanmış bir noktasıdır.
- /Hana/logbackupkonumunda ayrı bir anlık görüntü.
- Bir işletim sistemi bölümü.

En son anlık görüntü betikleri ve belgeleri almak için bkz. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Snapshot betik paketini [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)'dan indirdiğinizde, üç dosya alırsınız. Dosyalardan biri, belirtilen işlevsellik için bir PDF 'de belgelenmiştir. Araç kümesini indirdikten sonra, "anlık görüntü araçlarını alma" konusundaki yönergeleri izleyin.

## <a name="storage-snapshot-considerations"></a>Depolama anlık görüntüsü konuları

>[!NOTE]
>Depolama anlık görüntüleri, HANA büyük örnek birimlerine ayrılan depolama alanını kullanır. Depolama anlık görüntülerinin zamanlanması ve kaç depolama anlık görüntüsünü tutacağınızı göz önünde bulundurun. 

Azure 'da SAP HANA için depolama anlık görüntülerinin belirli bir mekanizması (büyük örnekler) şunları içerir:

- Zaman içindeki belirli bir depolama anlık görüntüsü çok az depolama alanı tüketir.
- Veri içeriği değiştikçe ve SAP HANA veri dosyalarındaki içerik depolama biriminde değişdikleri için, anlık görüntünün özgün blok içeriğini depolaması ve veri değişiklikleri olması gerekir.
- Sonuç olarak, depolama anlık görüntüsü boyut olarak artar. Anlık görüntü daha uzun olduğunda, depolama anlık görüntüsü ne kadar büyük olur.
- Bir depolama anlık görüntüsünün ömrü boyunca SAP HANA veritabanı biriminde yapılan daha fazla değişiklik, depolama anlık görüntüsünün alan tüketimine daha büyük olur.

Azure 'daki SAP HANA (büyük örnekler), SAP HANA veri ve günlük birimlerinin sabit birim boyutlarıyla birlikte gelir. Birim alanınızda bu birimlerin anlık görüntülerini gerçekleştirme. Şunları yapmanız gerekir:

- Depolama anlık görüntülerinin ne zaman zamanlançalıştırılacağını belirleme.
- Depolama birimlerinin alan tüketimini izleyin. 
- Depoladığınız anlık görüntü sayısını yönetin. 

Verileri içeri aktardığınızda veya HANA veritabanında başka önemli değişiklikler gerçekleştirdiğinizde depolama anlık görüntülerini devre dışı bırakabilirsiniz. 


Aşağıdaki bölümler, bu anlık görüntüleri gerçekleştirme ve genel önerileri ekleme hakkında bilgi sağlar:

- Donanım, birim başına 255 anlık görüntüye dayanabilir, ancak bu sayının altında iyi kalmak istersiniz. Öneri 250 veya daha küçüktür.
- Depolama anlık görüntülerini gerçekleştirmeden önce, boş alanı izleyin ve takip edin.
- Depolama anlık görüntülerinin sayısını boş alana göre düşürün. Devam ettiğiniz anlık görüntü sayısını düşürebileceğinizi veya birimleri genişletebilirsiniz. 1 terabaytlık birimlerde ek depolama alanı sıralaması yapabilirsiniz.
- Verileri SAP platform geçiş araçları (R3load) veya yedeklerden SAP HANA veritabanlarını geri yükleme gibi etkinliklerde SAP HANA,/Hana/Data biriminde depolama anlık görüntülerini devre dışı bırakın. 
- SAP HANA tablolarının daha büyük yeniden kuruluşları sırasında, mümkünse depolama anlık görüntülerinden kaçının.
- Depolama anlık görüntüleri, Azure 'daki SAP HANA olağanüstü durum kurtarma özelliğinden (büyük örnekler) yararlanmak için bir önkoşuldur.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Self Servis depolama anlık görüntülerini kullanma önkoşulları

Snapshot betiğinin başarıyla çalıştığından emin olmak için, Perl 'nin HANA büyük örnekler sunucusunda Linux işletim sisteminde yüklü olduğundan emin olun. Perl, HANA büyük örnek biriminizde önceden yüklenmiş olarak gelir. Perl sürümünü denetlemek için şu komutu kullanın:

`perl -v`

![Ortak anahtar, bu komut çalıştırılarak kopyalanır](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Depolama anlık görüntülerini ayarlama

HANA büyük örneklerle depolama anlık görüntülerini ayarlamak için aşağıdaki adımları izleyin.
1. Perl 'nin HANA büyük örnekler sunucusunda Linux işletim sisteminde yüklü olduğundan emin olun.
1. _Mac HMAC-SHA1_satırını eklemek için/etc/ssh/SSH @ no__t-0config öğesini değiştirin.
1. Varsa, çalıştırdığınız her bir SAP HANA örneği için ana düğümde SAP HANA bir yedekleme Kullanıcı hesabı oluşturun.
1. SAP HANA HDB istemcisini tüm SAP HANA Büyük Örnekleri sunucularına yükler.
1. Her bölgenin ilk SAP HANA Büyük Örnekleri sunucusunda, anlık görüntü oluşturmayı denetleyen temeldeki depolama altyapısına erişmek için ortak bir anahtar oluşturun.
1. Komut dosyalarını ve yapılandırma dosyasını [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 'dan SAP HANA yüklemesinde **hdbsql** konumuna kopyalayın.
1. *Hanabackupdetails. txt* dosyasını uygun müşteri belirtimleri için gereken şekilde değiştirin.

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)'dan en son anlık görüntü betiklerini ve belgelerini alın. Daha önce listelenen adımlar için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>MCOD senaryolarında dikkate alınması gereken
Tek bir HANA büyük örnek biriminde birden çok SAP HANA örneğiyle [Mcod senaryosu](https://launchpad.support.sap.com/#/notes/1681092) çalıştırırsanız, SAP HANA örneklerinin her biri için sağlanan ayrı depolama birimleriniz vardır. MDC ve diğer hususlar hakkında daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "Anımsanması gereken önemli noktalar".
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1\. Adım: SAP HANA HDB istemcisini yüklemeyi

Azure 'da SAP HANA yüklü Linux işletim sistemi (büyük örnekler), yedekleme ve olağanüstü durum kurtarma amacıyla SAP HANA depolama anlık görüntülerini çalıştırmak için gereken klasörleri ve betikleri içerir. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)'da daha yeni sürümler olup olmadığını denetleyin. 

SAP HANA yüklerken SAP HANA HDB istemcisini HANA büyük örnek birimlerine yüklemek sizin sorumluluğunuzdadır.

### <a name="step-2-change-the-etcsshssh_config"></a>2\. Adım:/etc/ssh/SSH @ no__t-0config öğesini değiştirme

Bu adım, [Azure 'daki SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "depolama Ile iletişimi etkinleştir" bölümünde açıklanmaktadır.


### <a name="step-3-create-a-public-key"></a>3\. Adım: ortak anahtar oluşturma

HANA büyük örnek kiracınızın depolama anlık görüntü arabirimlerine erişimi etkinleştirmek için, ortak anahtar aracılığıyla bir oturum açma yordamı oluşturun. 

Kiracınızdaki Azure (büyük örnekler) sunucusundaki ilk SAP HANA, depolama altyapısına erişmek için bir ortak anahtar oluşturun. Ortak anahtarla, depolama anlık görüntü arabirimlerinde oturum açmak için bir parola gerekli değildir. Ayrıca, bir ortak anahtarla parola kimlik bilgilerini korumanız gerekmez. 

Ortak anahtar oluşturmak için bkz. [Azure üzerinde SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "depolama Ile iletişimi etkinleştirme".


### <a name="step-4-create-an-sap-hana-user-account"></a>4\. Adım: SAP HANA Kullanıcı hesabı oluşturma

SAP HANA anlık görüntülerinin oluşturulmasını başlatmak için, depolama anlık görüntü betiklerinin kullanabileceği SAP HANA bir kullanıcı hesabı oluşturun. Bu amaçla SAP HANA Studio içinde SAP HANA bir kullanıcı hesabı oluşturun. Kullanıcının, MDC için SID veritabanı altında *DEĞIL* SystemDB altında oluşturulması gerekir. Tek kapsayıcı ortamında, Kullanıcı kiracı veritabanında oluşturulur. Bu hesabın **Yedekleme Yöneticisi** ve **Katalog okuma** ayrıcalıklarına sahip olması gerekir. 

Bir kullanıcı hesabı ayarlamak ve kullanmak için [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'da "SAP HANA Ile iletişimi etkinleştir" konusuna bakın.


### <a name="step-5-authorize-the-sap-hana-user-account"></a>5\. Adım: SAP HANA Kullanıcı hesabını yetkilendirme

Bu adımda, betiklerin çalışma zamanında parola göndermesi gerekmeyen SAP HANA Kullanıcı hesabını yetkilendirirsiniz. @No__t-0 SAP HANA komutu bir SAP HANA Kullanıcı anahtarı oluşturulmasına izin vermez. Anahtar bir veya daha fazla SAP HANA düğümünde depolanır. Kullanıcı anahtarı, komut dosyası işlemi içinden parolaları yönetmek zorunda kalmadan kullanıcının SAP HANA erişmesini sağlar. Betik işleme Bu makalenin ilerleyen kısımlarında ele alınmıştır.

>[!IMPORTANT]
>Bu yapılandırma komutlarını, anlık görüntü komutlarının çalıştırıldığı Kullanıcı bağlamı ile çalıştırın. Aksi takdirde, anlık görüntü komutları düzgün çalışmaz.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6\. Adım: anlık görüntü betikleri alma, anlık görüntüleri yapılandırma ve yapılandırmayı ve bağlantıyı test etme

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1)'dan betiklerin en son sürümünü indirin. Betiklerin yüklenme şekli betiklerin 4,1 sürümü ile değiştirilmiştir. Daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "SAP HANA Ile iletişimi etkinleştirme".

Komutların tam sırası için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "anlık görüntü araçlarının kolay yüklenmesi (varsayılan)". Varsayılan yüklemenin kullanılmasını öneririz. 

3\. x sürümünden 4,1 sürümüne yükseltmek için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "var olan yüklemeyi yükseltme". 4,1 aracı kümesini kaldırmak için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "anlık görüntü araçlarının kaldırılması".

[Azure 'daki SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'ndaki "anlık görüntü araçlarının kurulumunu tamamen ayarlama" bölümünde açıklanan adımları çalıştırmayı unutmayın.

Farklı betiklerin ve dosyaların yüklendikleri haliyle amacı, "Bu anlık görüntü araçları nelerdir?" bölümünde açıklanmaktadır. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)' nda.

Anlık görüntü araçlarını yapılandırmadan önce, HANA yedekleme konumlarını ve ayarlarını da doğru yapılandırdığınızdan emin olun. Daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "SAP HANA yapılandırma".

Anlık görüntü araç kümesinin yapılandırması, [Azure 'daki SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'Nda "config file-HANABackupCustomerDetails. txt" bölümünde açıklanmaktadır.

#### <a name="test-connectivity-with-sap-hana"></a>SAP HANA ile bağlantıyı test etme

Tüm yapılandırma verilerini *Hanabackupcustomerdetails. txt* dosyasına geçirdikten sonra, yapılandırmaların Hana örnek verileri için doğru olup olmadığını denetleyin. SAP HANA ölçeği büyütme veya genişleme yapılandırmasından bağımsız olan `testHANAConnection` betiğini kullanın.

Daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)içindeki "SAP HANA-testHANAConnection Ile bağlantıyı denetleme".

#### <a name="test-storage-connectivity"></a>Test depolama bağlantısı

Sonraki test adımı, *Hanabackupcustomerdetails. txt* yapılandırma dosyasına yerleştirdiğiniz verilere bağlı olarak depolama bağlantısını denetedebilirdir. Ardından bir test anlık görüntüsü çalıştırın. @No__t-0 komutunu çalıştırmadan önce, bu testi çalıştırmanız gerekir. Bu test için komut dizisi için bkz. [Azure üzerinde SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "Storage-testStorageSnapshotConnection Ile bağlantıyı denetleme" ".

Depolama sanal makine arabirimlerinde başarılı bir oturum açma işleminden sonra, betik 2. aşama ile devam eder ve bir test anlık görüntüsü oluşturur. Çıktı, SAP HANA üç düğümlü bir genişleme yapılandırması için burada gösterilir.

Test anlık görüntüsü komut dosyasıyla başarıyla çalışırsa, gerçek depolama anlık görüntülerini zamanlayabilirsiniz. Başarılı olmazsa, ilerlememeden önce sorunları araştırın. Test anlık görüntüsü, ilk gerçek anlık görüntüler tamamlanana kadar etrafında kalır.


### <a name="step-7-perform-snapshots"></a>7\. Adım: anlık görüntü gerçekleştirme

Hazırlama adımları bittiğinde, gerçek depolama anlık görüntülerini yapılandırmaya ve zamanlamaya başlayabilirsiniz. Zamanlanan betik, SAP HANA ölçeği artırma ve genişleme yapılandırmalarına göre çalışır. Yedekleme betiğini düzenli ve düzenli olarak yürütmek için cron yardımcı programını kullanarak betiği zamanlayın. 

Tam komut sözdizimi ve işlevselliği için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "Snapshot Backup-Azure_hana_backup" gerçekleştirme. 

@No__t-0 betiği çalıştırıldığında, aşağıdaki üç aşamada depolama anlık görüntüsünü oluşturur:

1. SAP HANA anlık görüntüsünü çalıştırır.
1. Bir depolama anlık görüntüsü çalıştırır.
1. Depolama anlık görüntüsü çalıştırılmadan önce oluşturulan SAP HANA anlık görüntüsünü kaldırır.

Betiği çalıştırmak için, kopyalandığı HDB çalıştırılabilir klasöründen çağırın. 

Saklama süresi, betiği çalıştırdığınızda parametre olarak gönderilen anlık görüntü sayısıyla yönetilir. Depolama anlık görüntülerinin kapsadığı zaman miktarı, yürütme döneminin bir işlevidir ve betik çalıştırıldığında parametre olarak gönderilen anlık görüntü sayısına göre yapılır. 

Tutulan anlık görüntü sayısı, komut dosyası çağrısında bir parametre olarak adlandırılan sayıyı aşarsa, yeni bir anlık görüntü çalıştırılmadan önce aynı etiketin en eski depolama anlık görüntüsü silinir. Çağrının son parametresi olarak verdiğiniz sayı, tutulan anlık görüntü sayısını denetlemek için kullanabileceğiniz sayıdır. Bu sayıyla, anlık görüntüler için kullanılan disk alanını da, dolaylı olarak kontrol edebilirsiniz. 


## <a name="snapshot-strategies"></a>Anlık görüntü stratejileri
Farklı türler için anlık görüntülerin sıklığı, HANA büyük örnek olağanüstü durum kurtarma işlevini kullanmanıza bakılmaksızın değişir. Bu işlevsellik, depolama anlık görüntülerinin sıklık ve yürütme dönemlerinde özel öneriler gerektirebilecek depolama anlık görüntülerini kullanır. 

Aşağıdaki önemli noktalar ve önerilerle, örnek olarak HANA büyük örneklerin sunduğu olağanüstü durum kurtarma *işlevini kullanmayın.* Bunun yerine, yedeklemelerin olması için depolama anlık görüntülerini kullanır ve son 30 gün için noktadan noktaya kurtarma sağlayabileceksiniz. Anlık görüntü ve boşluk sayısının sınırlamaları verildiğinde, aşağıdaki gereksinimleri göz önünde bulundurun:

- Noktadan noktaya kurtarma için kurtarma süresi.
- Kullanılan alan.
- Kurtarma noktası ve olağanüstü durum kurtarma süresi hedefleri.
- HANA tam veritabanı yedeklemelerini disklere karşı son yürütme. Disklere veya **backınt** arabirimine karşı tam veritabanı yedeklemesi yapıldığında, depolama anlık görüntülerinin yürütülmesi başarısız olur. Depolama anlık görüntülerinin üzerine tam veritabanı yedeklemeleri çalıştırmayı planlıyorsanız, depolama anlık görüntülerinin yürütülmesi bu süre içinde devre dışı bırakıldığından emin olun.
- Birim başına 250 ile sınırlı olan anlık görüntü sayısı.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

HANA büyük örneklerinin olağanüstü durum kurtarma işlevini kullanmıyorsanız, anlık görüntü süresi daha az sıklıkla olur. Bu gibi durumlarda,/Hana/Data ve/Hana/Shared ' de bulunan ve 12 saat veya 24 saatlik dönemlerde/usr/SAP içeren Birleşik anlık görüntüleri gerçekleştirin. Anlık görüntüleri bir ay boyunca tutun. Aynı, günlük yedekleme biriminin anlık görüntüleri için de geçerlidir. Günlük yedekleme birimine karşı SAP HANA işlem günlüğü yedeklemelerinin yürütülmesi 5 dakika ile 15 dakikalık dönemler arasında gerçekleşir.

Zamanlanmış Depolama anlık görüntüleri, cron kullanılarak en iyi şekilde gerçekleştirilir. Tüm yedeklemeler ve olağanüstü durum kurtarma ihtiyaçları için aynı betiği kullanın. Komut dosyası girişlerini, istenen çeşitli yedekleme süreleriyle eşleşecek şekilde değiştirin. Bu anlık görüntüler, yürütme zamanına bağlı olarak cron 'de farklı şekilde zamanlanır. Saatlik, her 12 saatte bir, günlük veya haftalık olabilir. 

Aşağıdaki örnekte,/etc/crontab içinde bir cron zamanlaması gösterilmektedir:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Önceki örnekte, saatlik Birleşik bir anlık görüntü,/Hana/Data ve/Hana/Shared/SID içeren ve/usr/SAP, konumlar içeren birimleri kapsar. Son iki gün içinde daha hızlı bir zaman aşımı kurtarması için bu anlık görüntü türünü kullanın. Ayrıca, bu birimlerde günlük bir anlık görüntü de vardır. Bu nedenle, saatlik anlık görüntülere ve günlük anlık görüntülere göre dört haftalık kapsama göre iki güne sahip olursunuz. İşlem günlüğü yedekleme birimi de her gün yedeklenir. Bu yedeklemeler dört hafta boyunca tutulur. 

Crontab üçüncü satırında gördüğünüz gibi, HANA işlem günlüğü yedeklemesi her 5 dakikada bir çalışacak şekilde zamanlanır. Depolama anlık görüntülerini çalıştıran farklı cron işlerinin başlangıç zamanları aşamalı olarak gerçekleştirilir. Bu şekilde, anlık görüntüler zaman içinde belirli bir noktada çalışmaz. 

Aşağıdaki örnekte,/Hana/Data ve/Hana/Shared/SID içeren ve/usr/SAP, saat başına konumlar içeren birimleri kapsayan Birleşik bir anlık görüntü gerçekleştirirsiniz. Bu anlık görüntüleri iki gün boyunca tutabilirsiniz. İşlem günlüğü yedekleme birimlerinin anlık görüntüleri 5 dakikalık bir esasına göre çalışır ve dört saat boyunca tutulur. Daha önce olduğu gibi, HANA işlem günlüğü dosyasının yedeklemesi her 5 dakikada bir çalışacak şekilde zamanlanır. 

İşlem günlüğü yedekleme biriminin anlık görüntüsü, işlem günlüğü yedeklemesi başlatıldıktan sonra 2 dakikalık bir gecikmeyle gerçekleştirilir. Normal koşullarda SAP HANA işlem günlüğü yedeklemesi bu 2 dakika içinde tamamlanır. Daha önce olduğu gibi, önyükleme LUN 'unu içeren birim bir depolama anlık görüntüsü tarafından günde bir kez yedeklenir ve dört hafta boyunca tutulur.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Aşağıdaki grafikte, önceki örneğin dizileri gösterilmektedir. Önyükleme LUN 'U dışlandı.

![Yedeklemeler ve anlık görüntüler arasındaki ilişki](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA, işlenen değişiklikleri veritabanına belgelemek için/Hana/log birimine göre düzenli yazma işlemleri gerçekleştirir. Düzenli aralıklarla, SAP HANA/Hana/Data birimine bir kayıt noktası yazar. Crontab bölümünde belirtildiği gibi, bir SAP HANA işlem günlüğü yedeklemesi 5 dakikada bir çalışır. 

Ayrıca,/Hana/Data ve/Hana/Shared/SID birimleri üzerinde Birleşik bir depolama anlık görüntüsünün tetiklenmesi sonucunda bir SAP HANA anlık görüntüsünün çalıştığını her saat görürsünüz. HANA anlık görüntüsü başarılı olduktan sonra, Birleşik depolama anlık görüntüsü çalışır. Cronm sekmesinde belirtildiği gibi,/Hana/Logbackup birimindeki depolama anlık görüntüsü, HANA işlem günlüğü yedeklemesinden 2 dakika sonra 5 dakikada bir çalışır.

> 

>[!IMPORTANT]
> SAP HANA yedeklemeleri için depolama anlık görüntülerinin kullanımı, yalnızca anlık görüntülerin SAP HANA işlem günlüğü yedeklemeleriyle birlikte gerçekleştirilmesinden değerlidir. Bu işlem günlüğü yedeklemelerinin, depolama anlık görüntüleri arasındaki zaman aralıklarını kapsaması gerekir. 

30 günlük bir noktadan noktaya kurtarma kullanıcılarına bir taahhüt ayarladıysanız şunları yapmanız gerekir:

- /Hana/Data ve/Hana/Shared/SID üzerinden, olağanüstü durumlarda 30 gün öncesine sahip bir birleştirilmiş depolama anlık görüntüsüne erişin. 
- Birleştirilmiş depolama anlık görüntülerinin arasındaki zamanı kapsayan bitişik işlem günlüğü yedekleri vardır. Bu nedenle, işlem günlüğü yedekleme biriminin en eski anlık görüntüsünün 30 gün öncesine ihtiyacı vardır. İşlem günlüğü yedeklerini Azure depolamada bulunan başka bir NFS paylaşımında kopyalarsanız bu durum böyle değildir. Bu durumda, bu NFS paylaşımından eski işlem günlüğü yedeklemelerini çekebilirsiniz.

Depolama anlık görüntülerinin ve işlem günlüğü yedeklemelerinin nihai depolama çoğaltmasının avantajlarından yararlanmak için SAP HANA işlem günlüğü yedeklemelerini yazdığı konumu değiştirin. Bu değişikliği HANA Studio 'da yapabilirsiniz. 

SAP HANA, tam günlük segmentlerini otomatik olarak yedeklese de, belirleyici olacak bir günlük yedekleme aralığı belirtin. Bu, genellikle olağanüstü durum kurtarma seçeneğini kullanarak, genellikle bir belirleyici süre ile günlük yedeklemeleri çalıştırmak istediğiniz için geçerlidir. Aşağıdaki örnekte, günlük yedekleme aralığı 15 dakika olarak ayarlanır.

![SAP HANA Studio 'da SAP HANA yedekleme günlüklerini zamanlama](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Ayrıca 15 dakikada bir daha sık yedeklemeler de seçebilirsiniz. Daha sık kullanılan bir ayar, genellikle HANA büyük örneklerin olağanüstü durum kurtarma işlevselliğiyle birlikte kullanılır. Bazı müşteriler her 5 dakikada bir işlem günlüğü yedeklemesi gerçekleştirir.

Veritabanı hiç yedeklenmediyse, son adım yedekleme kataloğunda olması gereken tek bir yedekleme girdisi oluşturmak için dosya tabanlı veritabanı yedeklemesi gerçekleştirmelidir. Aksi takdirde SAP HANA, belirtilen günlük yedeklemelerinizi başlatamaz.

![Tek bir yedekleme girdisi oluşturmak için dosya tabanlı yedekleme yapma](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


İlk başarılı depolama anlık görüntüleriniz çalıştıktan sonra, 6. adımda çalışan test anlık görüntüsünü silin. Daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "test anlık görüntülerini kaldır-removeTestStorageSnapshot". 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Disk birimindeki anlık görüntülerin sayısını ve boyutunu izleyin

Belirli bir depolama biriminde, anlık görüntü sayısını ve bu anlık görüntülerin depolama tüketimini izleyebilirsiniz. @No__t-0 komutu, anlık görüntü dizinini veya dosyalarını göstermez. Linux işletim sistemi komutu `du`, aynı birimlerde depolandığından, bu depolama anlık görüntüleri hakkındaki ayrıntıları gösterir. Aşağıdaki seçeneklerle komutunu kullanın:

- `du –sh .snapshot`: Bu seçenek, anlık görüntü dizini içindeki tüm anlık görüntülerin toplam sayısını sağlar.
- `du –sh --max-depth=1`: Bu seçenek, **. Snapshot** klasörüne kaydedilen tüm anlık görüntüleri ve her anlık görüntünün boyutunu listeler.
- `du –hc`: Bu seçenek, tüm anlık görüntüler tarafından kullanılan toplam boyutu sağlar.

Alınan ve depolanan anlık görüntülerin birimlerdeki tüm depolamayı tükettiğinden emin olmak için bu komutları kullanın.

>[!NOTE]
>Önyükleme LUN ' nin anlık görüntüleri, önceki komutlarla birlikte görünmez.

### <a name="get-details-of-snapshots"></a>Anlık görüntülerin ayrıntılarını al
Anlık görüntüler hakkında daha fazla bilgi edinmek için `azure_hana_snapshot_details` betiğini kullanın. Olağanüstü durum kurtarma konumunda etkin bir sunucu varsa, bu betiği herhangi bir konumda çalıştırabilirsiniz. Betiği, anlık görüntüler içeren her bir birime göre ayrılmış aşağıdaki çıktıyı sağlar: 
   * Bir birimdeki toplam anlık görüntülerin boyutu
   * Bu birimdeki her anlık görüntüde aşağıdaki Ayrıntılar: 
      - Anlık görüntü adı 
      - Oluşturma zamanı 
      - Anlık görüntünün boyutu
      - Anlık görüntünün sıklığı
      - Uygunsa, bu anlık görüntüyle ilişkili HANA yedekleme KIMLIĞI

Komut ve çıktıların sözdizimi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'Nda "List Snapshots-azure_hana_snapshot_details". 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Bir sunucudaki anlık görüntü sayısını azaltma

Daha önce açıklandığı gibi, depoladığınız bazı anlık görüntü etiketlerinin sayısını azaltabilirsiniz. Bir anlık görüntü başlatmak için komutun son iki parametresi, bu etiketlerin ve kalmasını istediğiniz anlık görüntülerin sayısından oluşur.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Önceki örnekte, Snapshot etiketi **dailyhana**olur. Bu etikete sahip olacak anlık görüntü sayısı **28**' dir. Disk alanı tüketimine yanıt verme sırasında, depolanan anlık görüntü sayısını azaltmak isteyebilirsiniz. Anlık görüntü sayısını 15 ' e düşürmenin kolay bir yolu, örneğin, betiği **15**' e ayarlanan son parametre ile çalıştırmaktır:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Betiği bu ayarla çalıştırırsanız, yeni depolama anlık görüntüsünü içeren anlık görüntü sayısı 15 ' tir. En son 15 anlık görüntü tutulur ve 15 daha eski anlık görüntü silinir.

 >[!NOTE]
 > Bu betik, yalnızca bir saatten daha eski anlık görüntüler varsa, anlık görüntü sayısını azaltır. Betik, bir saatten daha eski olan anlık görüntüleri silmez. Bu kısıtlamalar, sunulan isteğe bağlı olağanüstü durum kurtarma işleviyle ilgilidir.

Bir anlık görüntü kümesini artık, {1} **yedekleme ön eki** ile korumak istemiyorsanız, komut dosyasını saklama numarası olarak **0** ile çalıştırın. Bu etiketle eşleşen tüm anlık görüntüler daha sonra kaldırılır. Tüm anlık görüntülerin kaldırılması, HANA büyük örnek olağanüstü durum kurtarma işlevinin yeteneklerini etkileyebilir.

Belirli anlık görüntüleri silmek için ikinci bir seçenek, `azure_hana_snapshot_delete` betiğini kullanmaktır. Bu betik, HANA Studio 'da bulunan ya da anlık görüntü adının kendisi aracılığıyla HANA yedekleme KIMLIĞI kullanılarak bir anlık görüntüyü veya anlık görüntü kümesini silmek için tasarlanmıştır. Şu anda yedekleme KIMLIĞI yalnızca **Hana** anlık görüntü türü için oluşturulan anlık görüntülere bağlıdır. Tür **günlüklerinin** anlık görüntü yedeklemeleri ve **önyüklemesi** SAP HANA bir anlık görüntü gerçekleştirmez, bu nedenle bu anlık görüntüler için hiçbir yedekleme kimliği bulunamadı. Anlık görüntü adı girilirse, girilen anlık görüntü adıyla eşleşen farklı birimlerdeki tüm anlık görüntüleri arar. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Betiği hakkında daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "anlık görüntü-azure_hana_snapshot_delete silme".

Betiği Kullanıcı **kökü**olarak çalıştırın.

>[!IMPORTANT]
>Yalnızca silmeyi planladığınız anlık görüntüde bulunan veriler varsa, anlık görüntü silindikten sonra bu veriler süresiz olarak kaybedilir.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Depolama anlık görüntüsünden dosya düzeyinde geri yükleme

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
**Hana** ve **Günlükler**anlık görüntü türleri için anlık görüntülere doğrudan **. Snapshot** dizinindeki birimlerde erişebilirsiniz. Her anlık görüntü için bir alt dizin bulunur. Bu alt dizindeki anlık görüntü noktasındaki her bir dosyayı gerçek dizin yapısına kopyalayın. 

Betiğin geçerli sürümünde, anlık görüntü geri yükleme için self servis olarak belirtilen geri yükleme betiği *yoktur* . Anlık görüntü geri yükleme, yük devretme sırasında olağanüstü durum kurtarma sitesindeki self servis olağanüstü durum kurtarma betikleri kapsamında gerçekleştirilebilir. Mevcut kullanılabilir anlık görüntülerden istenen bir anlık görüntüyü geri yüklemek için bir hizmet isteği açarak Microsoft Operasyon ekibine başvurmanız gerekir.

>[!NOTE]
>Tek dosya geri yükleme, HANA büyük örnek birimlerinin türünden bağımsız olarak önyükleme LUN 'ları için çalışmaz. **. Snapshot** DIZINI önyükleme LUN 'da gösterilmez. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>En son HANA anlık görüntüsüne kurtar

Bir üretim senaryosunda, bir depolama anlık görüntüsünden kurtarma işlemi Microsoft Azure desteği olan bir müşteri olayı olarak başlatılabilir. Verilerin bir üretim sisteminde silinme ve bunu almanın tek yolu üretim veritabanının geri yüklenmesi çok yüksektir.

Farklı bir durumda, bir zaman içinde kurtarma düşük aciliyet ve planlanan gün önceden olabilir. Bu kurtarmayı, yüksek öncelikli bir bayrak yerine Azure 'da SAP HANA planlayabilirsiniz. Örneğin, yeni bir geliştirme paketi uygulayarak SAP yazılımını yükseltmeyi planlamanız gerekebilir. Daha sonra, geliştirme paketi yükseltmeden önce durumu temsil eden bir anlık görüntüye dönmeniz gerekir.

İsteği göndermeden önce hazırlamanız gerekir. Azure ekibinizdeki SAP HANA, isteği işleyebilir ve geri yüklenen birimleri sağlayabilir. Daha sonra, HANA veritabanını anlık görüntülere göre geri yükleyin.

Yeni araç kümesiyle geri yüklenen bir anlık görüntüyü alma olasılıkları için, [depolama anlık görüntüsünden Azure 'da SAP HANA Için el ile kurtarma Kılavuzu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'ndaki "anlık görüntü geri yükleme" konusuna bakın.

İstek için hazırlanmak üzere aşağıdaki adımları izleyin.

1. Hangi anlık görüntünün geri yükleneceğine karar verin. Aksi belirtilmedikçe yalnızca Hana/veri hacmi geri yüklenir. 

1. HANA örneğini kapatın.

   ![HANA örneğini kapatma](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Her bir HANA veritabanı düğümündeki veri birimlerini çıkarın. Veri birimleri hala işletim sistemine bağlanmışsa, anlık görüntünün geri yüklenmesi başarısız olur.

   ![Her bir HANA veritabanı düğümündeki veri birimlerini çıkarma](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Bir Azure destek isteği açın ve belirli bir anlık görüntünün geri yüklenmesi hakkında yönergeler ekleyin:

   - Geri yükleme sırasında: Azure hizmetinde SAP HANA, doğru depolama anlık görüntüsünün geri yüklendiğini, doğrulamak ve onaylamak için bir konferans çağrısına katılmayı isteyebilir. 

   - Geri yükleme sonrasında: Azure Service SAP HANA, depolama anlık görüntüsünün ne zaman geri yüklendiği konusunda sizi bilgilendirir.

1. Geri yükleme işlemi tamamlandıktan sonra tüm veri birimlerini yeniden bağlayın.

   ![Tüm veri birimlerini yeniden bağlama](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Depolama anlık görüntüsünden kurtarılan SAP HANA veri dosyalarını almak için başka bir olasılık, [depolama anlık görüntüsünden Azure 'da SAP HANA Için el ile kurtarma kılavuzundaki](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)adım 7 ' de belgelenmiştir.

Anlık görüntü yedeklemesinden geri yüklemek için, [depolama anlık görüntüsünden Azure 'da SAP HANA Için el ile kurtarma Kılavuzu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)' na bakın. 

>[!Note]
>Anlık görüntü Microsoft işlemleri tarafından geri yüklenirse, adım 7 ' yi yapmanız gerekmez.


### <a name="recover-to-another-point-in-time"></a>Zaman içinde başka bir noktaya kurtar
Belirli bir zaman noktasına geri yüklemek için, [depolama anlık görüntüsünden Azure 'da SAP HANA Için el ile kurtarma Kılavuzu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)' na bakın. "veritabanını şu anda aşağıdaki noktaya kurtar 


## <a name="next-steps"></a>Sonraki adımlar
- Bkz. [olağanüstü durum kurtarma ilkeleri ve hazırlığı](hana-concept-preparation.md).
