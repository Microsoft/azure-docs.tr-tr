---
title: Azure 'da SAP HANA HANA yedekleme ve geri yükleme (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA HANA yedeklemesini gerçekleştirme ve geri yükleme (büyük örnekler)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3244cbca3df717d3432670a366147408cf2b2c0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669109"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>HANA büyük örneklerde SAP HANA yedekleme ve geri yükleme

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
1. \_ _Mac HMAC-SHA1_ satırını eklemek için/etc/ssh/SSH yapılandırmasını değiştirin.
1. Varsa, çalıştırdığınız her bir SAP HANA örneği için ana düğümde SAP HANA bir yedekleme Kullanıcı hesabı oluşturun.
1. SAP HANA HDB istemcisini tüm SAP HANA Büyük Örnekleri sunucularına yükler.
1. Her bölgenin ilk SAP HANA Büyük Örnekleri sunucusunda, anlık görüntü oluşturmayı denetleyen temeldeki depolama altyapısına erişmek için ortak bir anahtar oluşturun.
1. Komut dosyalarını ve yapılandırma dosyasını [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 'dan SAP HANA yüklemesinde **hdbsql** konumuna kopyalayın.
1. *HANABackupDetails.txt* dosyasını uygun müşteri belirtimleri için gereken şekilde değiştirin.

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)'dan en son anlık görüntü betiklerini ve belgelerini alın. Daha önce listelenen adımlar için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>MCOD senaryolarında dikkate alınması gereken
Tek bir HANA büyük örnek biriminde birden çok SAP HANA örneğiyle [Mcod senaryosu](https://launchpad.support.sap.com/#/notes/1681092) çalıştırırsanız, SAP HANA örneklerinin her biri için sağlanan ayrı depolama birimleriniz vardır. MDC ve diğer hususlar hakkında daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "Anımsanması gereken önemli noktalar".
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1. Adım: SAP HANA HDB istemcisini yüklemeyi

Azure 'da SAP HANA yüklü Linux işletim sistemi (büyük örnekler), yedekleme ve olağanüstü durum kurtarma amacıyla SAP HANA depolama anlık görüntülerini çalıştırmak için gereken klasörleri ve betikleri içerir. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)'da daha yeni sürümler olup olmadığını denetleyin. 

SAP HANA yüklerken SAP HANA HDB istemcisini HANA büyük örnek birimlerine yüklemek sizin sorumluluğunuzdadır.

### <a name="step-2-change-the-etcsshssh_config"></a>2. Adım:/etc/ssh/SSH \_ yapılandırmasını değiştirme

Bu adım, [Azure 'daki SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "depolama Ile iletişimi etkinleştir" bölümünde açıklanmaktadır.


### <a name="step-3-create-a-public-key"></a>3. Adım: ortak anahtar oluşturma

HANA büyük örnek kiracınızın depolama anlık görüntü arabirimlerine erişimi etkinleştirmek için, ortak anahtar aracılığıyla bir oturum açma yordamı oluşturun. 

Kiracınızdaki Azure (büyük örnekler) sunucusundaki ilk SAP HANA, depolama altyapısına erişmek için bir ortak anahtar oluşturun. Ortak anahtarla, depolama anlık görüntü arabirimlerinde oturum açmak için bir parola gerekli değildir. Ayrıca, bir ortak anahtarla parola kimlik bilgilerini korumanız gerekmez. 

Ortak anahtar oluşturmak için bkz. [Azure üzerinde SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "depolama Ile iletişimi etkinleştirme".


### <a name="step-4-create-an-sap-hana-user-account"></a>4. Adım: SAP HANA Kullanıcı hesabı oluşturma

SAP HANA anlık görüntülerinin oluşturulmasını başlatmak için, depolama anlık görüntü betiklerinin kullanabileceği SAP HANA bir kullanıcı hesabı oluşturun. Bu amaçla SAP HANA Studio içinde SAP HANA bir kullanıcı hesabı oluşturun. Kullanıcının, MDC için SID veritabanı altında *DEĞIL* SystemDB altında oluşturulması gerekir. Tek kapsayıcı ortamında, Kullanıcı kiracı veritabanında oluşturulur. Bu hesabın **Yedekleme Yöneticisi** ve **Katalog okuma** ayrıcalıklarına sahip olması gerekir. 

Bir kullanıcı hesabı ayarlamak ve kullanmak için [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'da "SAP HANA Ile iletişimi etkinleştir" konusuna bakın.


### <a name="step-5-authorize-the-sap-hana-user-account"></a>5. Adım: SAP HANA Kullanıcı hesabını yetkilendirme

Bu adımda, betiklerin çalışma zamanında parola göndermesi gerekmeyen SAP HANA Kullanıcı hesabını yetkilendirirsiniz. SAP HANA komutu `hdbuserstore` bir SAP HANA Kullanıcı anahtarı oluşturulmasına izin vermez. Anahtar bir veya daha fazla SAP HANA düğümünde depolanır. Kullanıcı anahtarı, komut dosyası işlemi içinden parolaları yönetmek zorunda kalmadan kullanıcının SAP HANA erişmesini sağlar. Betik işleme Bu makalenin ilerleyen kısımlarında ele alınmıştır.

>[!IMPORTANT]
>Bu yapılandırma komutlarını, anlık görüntü komutlarının çalıştırıldığı Kullanıcı bağlamı ile çalıştırın. Aksi takdirde, anlık görüntü komutları düzgün çalışmaz.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6. Adım: anlık görüntü betikleri alma, anlık görüntüleri yapılandırma ve yapılandırmayı ve bağlantıyı test etme

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)'dan betiklerin en son sürümünü indirin. Betiklerin yüklenme şekli betiklerin 4,1 sürümü ile değiştirilmiştir. Daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "SAP HANA Ile iletişimi etkinleştirme".

Komutların tam sırası için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "anlık görüntü araçlarının kolay yüklenmesi (varsayılan)". Varsayılan yüklemenin kullanılmasını öneririz. 

3. x sürümünden 4,1 sürümüne yükseltmek için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "var olan yüklemeyi yükseltme". 4,1 aracı kümesini kaldırmak için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "anlık görüntü araçlarının kaldırılması".

[Azure 'daki SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'ndaki "anlık görüntü araçlarının kurulumunu tamamen ayarlama" bölümünde açıklanan adımları çalıştırmayı unutmayın.

Farklı betiklerin ve dosyaların yüklendikleri haliyle amacı, "Bu anlık görüntü araçları nelerdir?" bölümünde açıklanmaktadır. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)' nda.

Anlık görüntü araçlarını yapılandırmadan önce, HANA yedekleme konumlarını ve ayarlarını da doğru yapılandırdığınızdan emin olun. Daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "SAP HANA yapılandırma".

Anlık görüntü araç kümesinin yapılandırması, [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'Nda "config file-HANABackupCustomerDetails.txt" bölümünde açıklanmaktadır.

#### <a name="test-connectivity-with-sap-hana"></a>SAP HANA ile bağlantıyı test etme

Tüm yapılandırma verilerini *HANABackupCustomerDetails.txt* dosyasına koyduktan sonra, yapılandırmaların Hana örnek verileri için doğru olup olmadığını denetleyin. `testHANAConnection`Bir SAP HANA ölçeği büyütme veya genişleme yapılandırmasından bağımsız olan betiği kullanın.

Daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)içindeki "SAP HANA-testHANAConnection Ile bağlantıyı denetleme".

#### <a name="test-storage-connectivity"></a>Test depolama bağlantısı

Sonraki test adımı, *HANABackupCustomerDetails.txt* yapılandırma dosyasına yerleştirdiğiniz verilere göre depolamanın bağlantısını denetme adımıdır. Ardından bir test anlık görüntüsü çalıştırın. Komutu çalıştırmadan önce `azure_hana_backup` , bu testi çalıştırmanız gerekir. Bu test için komut dizisi için bkz. [Azure üzerinde SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "Storage-testStorageSnapshotConnection Ile bağlantıyı denetleme" ".

Depolama sanal makine arabirimlerinde başarılı bir oturum açma işleminden sonra, betik 2. aşama ile devam eder ve bir test anlık görüntüsü oluşturur. Çıktı, SAP HANA üç düğümlü bir genişleme yapılandırması için burada gösterilir.

Test anlık görüntüsü komut dosyasıyla başarıyla çalışırsa, gerçek depolama anlık görüntülerini zamanlayabilirsiniz. Başarılı olmazsa, ilerlememeden önce sorunları araştırın. Test anlık görüntüsü, ilk gerçek anlık görüntüler tamamlanana kadar etrafında kalır.


### <a name="step-7-perform-snapshots"></a>7. Adım: anlık görüntü gerçekleştirme

Hazırlama adımları bittiğinde, gerçek depolama anlık görüntülerini yapılandırmaya ve zamanlamaya başlayabilirsiniz. Zamanlanan betik, SAP HANA ölçeği artırma ve genişleme yapılandırmalarına göre çalışır. Yedekleme betiğini düzenli ve düzenli olarak yürütmek için cron yardımcı programını kullanarak betiği zamanlayın. 

Tam komut sözdizimi ve işlevselliği için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "anlık görüntü yedeklemesi gerçekleştirme-azure_hana_backup". 

Betik `azure_hana_backup` çalıştırıldığında, aşağıdaki üç aşamada depolama anlık görüntüsünü oluşturur:

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

Belirli bir depolama biriminde, anlık görüntü sayısını ve bu anlık görüntülerin depolama tüketimini izleyebilirsiniz. `ls`Komut, anlık görüntü dizinini veya dosyalarını göstermez. Linux işletim sistemi komutu, `du` aynı birimlerde depolandığından, bu depolama anlık görüntüleri hakkındaki ayrıntıları gösterir. Aşağıdaki seçeneklerle komutunu kullanın:

- `du –sh .snapshot`: Bu seçenek, anlık görüntü dizini içindeki tüm anlık görüntülerin toplam sayısını sağlar.
- `du –sh --max-depth=1`: Bu seçenek, **. Snapshot** klasörüne kaydedilen tüm anlık görüntüleri ve her anlık görüntünün boyutunu listeler.
- `du –hc`: Bu seçenek, tüm anlık görüntüler tarafından kullanılan toplam boyutu sağlar.

Alınan ve depolanan anlık görüntülerin birimlerdeki tüm depolamayı tükettiğinden emin olmak için bu komutları kullanın.

>[!NOTE]
>Önyükleme LUN ' nin anlık görüntüleri, önceki komutlarla birlikte görünmez.

### <a name="get-details-of-snapshots"></a>Anlık görüntülerin ayrıntılarını al
Anlık görüntüler hakkında daha fazla bilgi edinmek için betiği kullanın `azure_hana_snapshot_details` . Olağanüstü durum kurtarma konumunda etkin bir sunucu varsa, bu betiği herhangi bir konumda çalıştırabilirsiniz. Betiği, anlık görüntüler içeren her bir birime göre ayrılmış aşağıdaki çıktıyı sağlar: 
   * Bir birimdeki toplam anlık görüntülerin boyutu
   * Bu birimdeki her anlık görüntüde aşağıdaki Ayrıntılar: 
      - Anlık görüntü adı 
      - Oluşturma zamanı 
      - Anlık görüntünün boyutu
      - Anlık görüntünün sıklığı
      - Uygunsa, bu anlık görüntüyle ilişkili HANA yedekleme KIMLIĞI

Komut ve çıktıların sözdizimi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "anlık görüntüleri listele-azure_hana_snapshot_details". 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Bir sunucudaki anlık görüntü sayısını azaltma

Daha önce açıklandığı gibi, depoladığınız bazı anlık görüntü etiketlerinin sayısını azaltabilirsiniz. Bir anlık görüntü başlatmak için komutun son iki parametresi, bu etiketlerin ve kalmasını istediğiniz anlık görüntülerin sayısından oluşur.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Önceki örnekte, Snapshot etiketi **dailyhana** olur. Bu etikete sahip olacak anlık görüntü sayısı **28**' dir. Disk alanı tüketimine yanıt verme sırasında, depolanan anlık görüntü sayısını azaltmak isteyebilirsiniz. Anlık görüntü sayısını 15 ' e düşürmenin kolay bir yolu, örneğin, betiği **15**' e ayarlanan son parametre ile çalıştırmaktır:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Betiği bu ayarla çalıştırırsanız, yeni depolama anlık görüntüsünü içeren anlık görüntü sayısı 15 ' tir. En son 15 anlık görüntü tutulur ve 15 daha eski anlık görüntü silinir.

 >[!NOTE]
 > Bu betik, yalnızca bir saatten daha eski anlık görüntüler varsa, anlık görüntü sayısını azaltır. Betik, bir saatten daha eski olan anlık görüntüleri silmez. Bu kısıtlamalar, sunulan isteğe bağlı olağanüstü durum kurtarma işleviyle ilgilidir.

Bir anlık görüntü kümesini artık, {1} **yedekleme ön eki** ile korumak istemiyorsanız, komut dosyasını saklama numarası olarak **0** ile çalıştırın. Bu etiketle eşleşen tüm anlık görüntüler daha sonra kaldırılır. Tüm anlık görüntülerin kaldırılması, HANA büyük örnek olağanüstü durum kurtarma işlevinin yeteneklerini etkileyebilir.

Belirli anlık görüntüleri silmek için ikinci bir seçenek betiği kullanmaktır `azure_hana_snapshot_delete` . Bu betik, HANA Studio 'da bulunan ya da anlık görüntü adının kendisi aracılığıyla HANA yedekleme KIMLIĞI kullanılarak bir anlık görüntüyü veya anlık görüntü kümesini silmek için tasarlanmıştır. Şu anda yedekleme KIMLIĞI yalnızca **Hana** anlık görüntü türü için oluşturulan anlık görüntülere bağlıdır. Tür **günlüklerinin** anlık görüntü yedeklemeleri ve **önyüklemesi** SAP HANA bir anlık görüntü gerçekleştirmez, bu nedenle bu anlık görüntüler için hiçbir yedekleme kimliği bulunamadı. Anlık görüntü adı girilirse, girilen anlık görüntü adıyla eşleşen farklı birimlerdeki tüm anlık görüntüleri arar. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Betiği hakkında daha fazla bilgi için bkz. [Azure 'da SAP HANA Için Microsoft Snapshot araçları](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)'nda "anlık görüntü silme-azure_hana_snapshot_delete".

Betiği Kullanıcı **kökü** olarak çalıştırın.

>[!IMPORTANT]
>Yalnızca silmeyi planladığınız anlık görüntüde bulunan veriler varsa, anlık görüntü silindikten sonra bu veriler süresiz olarak kaybedilir.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Depolama anlık görüntüsünden dosya düzeyinde geri yükleme

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
**Hana** ve **Günlükler** anlık görüntü türleri için anlık görüntülere doğrudan **. Snapshot** dizinindeki birimlerde erişebilirsiniz. Her anlık görüntü için bir alt dizin bulunur. Bu alt dizindeki anlık görüntü noktasındaki her bir dosyayı gerçek dizin yapısına kopyalayın. 

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





## <a name="snapcenter-integration-in-sap-hana-large-instances"></a>SAP HANA büyük örneklerde SnapCenter tümleştirmesi

Bu bölümde, Microsoft Azure HANA büyük örneklerde (HLI) barındırılan bir anlık görüntü, yedekleme ve geri yükleme SAP HANA veritabanlarını almak için müşterilerin NetApp SnapCenter yazılımını nasıl kullanabileceği açıklanmaktadır. 

Anlık görüntü Merkezi, yedekleme/kurtarma, olağanüstü durum kurtarma (DR) ve zaman uyumsuz depolama çoğaltma, sistem çoğaltma ve sistem kopyalama gibi senaryolar için çözümler sunar. Azure 'da SAP HANA Büyük Örnekleri ile tümleşik hale, müşteriler artık yedekleme ve kurtarma işlemleri için ek Me merkezi 'ni kullanabilirler.

Ek başvurular için, bkz. NetApp TR-4614 ve TR-4646 in SnapCenter.

- [Ek/merkezi ile yedekleme/kurtarma SAP HANA (TR-4614)](https://www.netapp.com/us/media/tr-4614.pdf)
- [Depolama çoğaltması ile olağanüstü durum kurtarma SAP HANA (TR-4646)](https://www.netapp.com/us/media/tr-4646.pdf)
- [SAP HANA HSR with SnapCenter (TR-4719)](https://www.netapp.com/us/media/tr-4719.pdf)
- [Anlık görüntü merkezinden SAP kopyalama (TR-4667)](https://www.netapp.com/us/media/tr-4667.pdf)

### <a name="system-requirements-and-prerequisites"></a>Sistem gereksinimleri ve önkoşulları

Azure HLI 'da Snapın 'u çalıştırmak için sistem gereksinimleri şunları içerir:
* 4-vCPU, 16 GB RAM ve en az 650 GB yönetilen Premium SSD depolama ile Azure Windows 2016 veya daha yeni bir sürümü olan SnapCenter Server.
* 1,5 TB – 24 TB RAM ile SAP HANA Büyük Örnekleri sistem. Kopyalama işlemleri ve testler için iki SAP HANA büyük örnek sistemi kullanmanız önerilir.

SAP HANA 'da Snapın tümleştirme adımları şunlardır: 

1. Kullanıcı tarafından oluşturulan ortak anahtarı Microsoft Ops ekibine iletmek için bir destek bileti isteği yükseltin. Bu, anlık görüntü kullanıcısını depolama sistemine erişecek şekilde ayarlamak için gereklidir.
1. VNET 'iniz içinde HLI 'ya erişimi olan bir VM oluşturma Bu VM, SnapCenter için kullanılır. 
1. Ek yükleme merkezini indirin ve yükleyin. 
1. Yedekleme ve kurtarma işlemleri. 

### <a name="create-a-support-ticket-for-user-role-storage-setup"></a>Kullanıcı rolü depolama kurulumu için bir destek bileti oluşturma

1. Azure portal açın ve **abonelikler** sayfasına gidin. "Abonelikler" sayfasında, aşağıda kırmızı renkle özetlenen SAP HANA aboneliğinizi seçin.

   :::image type="content" source="./media/snapcenter/create-support-case-for-user-role-storage-setup.png" alt-text="Kullanıcı depolaması kurulumu için destek durumu oluşturma":::

1. SAP HANA abonelik sayfasında, **kaynak grupları** alt sayfasını seçin.

   :::image type="content" source="./media/snapcenter/solution-lab-subscription-resource-groups.png" alt-text="Çözüm Laboratuvarı abonelik kaynak grubu" lightbox="./media/snapcenter/solution-lab-subscription-resource-groups.png":::

1. Bir bölgede uygun bir kaynak grubu seçin.

   :::image type="content" source="./media/snapcenter/select-appropriate-resource-group-in-region.png" alt-text="Bölgede uygun kaynak grubunu seçin" lightbox="./media/snapcenter/select-appropriate-resource-group-in-region.png":::

1. Azure depolama üzerinde SAP HANA karşılık gelen bir SKU girişi seçin.

   :::image type="content" source="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png" alt-text="SAP HANA karşılık gelen SKU girişi seçin" lightbox="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png":::

1. Kırmızı renkle özetlenen **Yeni bir destek bileti** isteği açın.

   :::image type="content" source="./media/snapcenter/open-new-support-ticket-request.png" alt-text="Yeni destek bileti isteği aç":::

1. **Temel bilgiler** sekmesinde bilet için aşağıdaki bilgileri sağlayın:

   * **Sorun türü:** Teknik
   * **Abonelik:** Aboneliğiniz
   * **Hizmet:** SAP HANA büyük örnek
   * **Kaynak:** Kaynak grubunuz
   * **Özet:** Kullanıcı tarafından oluşturulan ortak anahtarı belirtin
   * **Sorun türü:** Yapılandırma ve kurulum
   * **Sorun alt türü:** HLI için SnapCenter ayarlama


1. Destek bileti **açıklamasında** , **Ayrıntılar** sekmesinde şunları belirtin: 
   
   * HLI için SnapCenter ayarlama
   * SnapCenter kullanıcısı (snapcenter. ped) için ortak anahtarınız-aşağıdaki ortak anahtar oluştur örneğine bakın

     :::image type="content" source="./media/snapcenter/new-support-request-details.png" alt-text="Yeni destek isteği-Ayrıntılar sekmesi" lightbox="./media/snapcenter/new-support-request-details.png":::

1. Destek biletinizi gözden geçirmek için **gözden geçir + oluştur** ' u seçin. 

1. HANA büyük örneğinde veya herhangi bir Linux sunucusunda, SnapCenter Kullanıcı adı için bir sertifika oluşturun.

   SnapCenter, depolama sanal makinesine (SVM) erişmek ve HANA veritabanının anlık görüntülerini oluşturmak için bir Kullanıcı adı ve parola gerektirir. Microsoft, (müşteri) depolama sistemine erişim için parola ayarlamanıza olanak tanımak için ortak anahtarı kullanır.

   ```bash
   openssl req -x509 -nodes -days 1095 -newkey rsa:2048 -keyout snapcenter.key -out snapcenter.pem -subj "/C=US/ST=WA/L=BEL/O=NetApp/CN=snapcenter"
   Generating a 2048 bit RSA private key
   ................................................................................................................................................+++++
   ...............................+++++
   writing new private key to 'snapcenter.key'
   -----

   sollabsjct31:~ # ls -l cl25*
   -rw-r--r-- 1 root root 1704 Jul 22 09:59 snapcenter.key
   -rw-r--r-- 1 root root 1253 Jul 22 09:59 snapcenter.pem

   ```

1. Snapcenter. PEI dosyasını destek bileti 'ne ekleyin ve ardından **Oluştur** ' u seçin.

   Ortak anahtar sertifikası gönderildikten sonra, Microsoft, kiracınız için SVM IP adresi ile birlikte SnapCenter Kullanıcı adını ayarlar.   

1. SVM IP 'sini aldıktan sonra, kontrol ettiğiniz SVM 'ye erişmek için bir parola ayarlayın.

   Aşağıda, HANA büyük örnek ortamına erişimi olan ve, parolayı ayarlamak için kullanılacak olan sanal ağ 'daki bir REST çağrısının veya VM 'deki REST çağrısının (belgelerinin) bir örneği verilmiştir.

   ```bash
   curl --cert snapcenter.pem --key snapcenter.key -X POST -k "https://10.0.40.11/api/security/authentication/password" -d '{"name":"snapcenter","password":"test1234"}'
   ```

   HANA DB sisteminde etkin bir proxy değişkeni olmadığından emin olun.

   ```bash
   sollabsjct31:/tmp # unset http_proxy
   sollabsjct31:/tmp # unset https_proxy
   ```

### <a name="download-and-install-snapcenter"></a>Ek yükleme merkezini indirme ve yükleme
Artık Kullanıcı adı, depolama sistemine ek yükleme merkezi erişimi için ayarlanmış olduğuna göre, bir kez Snapın 'i yükledikten sonra yeniden yükleme merkezi Kullanıcı adını kullanacaksınız. 

SnapCenter 'ı yüklemeden önce, yedekleme stratejinizi tanımlamak için, ek yükleme [Merkezi Ile yedekleme/kurtarma SAP HANA](https://www.netapp.com/us/media/tr-4614.pdf) gözden geçirin. 

1. En son SnapCenter sürümünü [indirmek](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fmysupport.netapp.com%2Fsite%2Fproducts%2Fall%2Fdetails%2Fsnapcenter%2Fdownloads-tab&data=02%7C01%7Cmadhukan%40microsoft.com%7Ca53f5e2f245a4e36933008d816efbb54%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637284566603265503&sdata=TOANWNYoAr1q5z1opu70%2FUDPHjluvovqR9AKplYpcpk%3D&reserved=0) Için [NetApp](https://mysupport.netapp.com) ' de oturum açın.

1. Windows Azure VM 'ye ek anlık görüntü yüklemesi.

   Yükleyici, sanal makinenin önkoşullarını kontrol eder. 

   >[!IMPORTANT]
   >Özellikle daha büyük ortamlarda VM 'nin boyutuna dikkat edin.

1. Anlık görüntü için Kullanıcı kimlik bilgilerini yapılandırın. Varsayılan olarak, uygulamayı yüklemek için kullanılan Windows Kullanıcı kimlik bilgilerini doldurur. 

   :::image type="content" source="media/snapcenter/installation-user-inputs-dialog.png" alt-text="Yükleme Kullanıcı girdileri iletişim kutusu"::: 

1. Oturumu başlattığınızda, güvenlik muafiyetini kaydedin ve GUI başlatılır.

1. VM 'de ( https://snapcenter-vm:8146) ortamı yapılandırmak Için Windows kimlik bilgilerini kullanarak) anlık görüntü hizmetinde oturum açın.


### <a name="set-up-the-storage-system"></a>Depolama sistemini ayarlama

1. Anlık görüntü merkezinde **depolama sistemi**' ni seçin ve **+ Yeni**' yi seçin. 

   :::image type="content" source="./media/snapcenter/snapcenter-storage-connections-window.png" alt-text="SnapCenter depolama bağlantıları" lightbox="./media/snapcenter/snapcenter-storage-connections-window.png":::

   Varsayılan değer, kiracı başına bir SVM 'dir. Bir müşterinin birden çok bölgede birden çok kiracıya veya HLIs 'e sahip olması durumunda, ek yük merkezinde tüm SVMs 'Leri yapılandırmak için öneri

1. Depolama sistemi Ekle ' de, eklemek istediğiniz depolama sistemi, anlık görüntü Kullanıcı adı ve parola bilgilerini girip **Gönder**' i seçin.

   :::image type="content" source="./media/snapcenter/new-storage-connection.png" alt-text="Yeni depolama bağlantısı":::

   >[!NOTE]
   >Varsayılan değer, kiracı başına bir SVM 'dir.  Birden çok kiracı varsa, bu durumda tüm SVMs 'Leri ek izin merkezinde yapılandırmak önerilir. 

1. Ek bileşen merkezi 'nde, **ana bilgisayarlar** ' ı seçin ve ardından seçin **+ Ekle** ' yi seçerek Hana eklentisini ve Hana DB ana bilgisayarlarını ayarlayın.  SnapCenter 'ın en son sürümü, konaktaki HANA veritabanını otomatik olarak algılar.

   :::image type="content" source="media/snapcenter/managed-hosts-new-host.png" alt-text="Anlık görüntü merkezi 'nde Konaklar ' ı ve ardından Ekle ' yi seçin." lightbox="media/snapcenter/managed-hosts-new-host.png":::

1. Yeni konakla ilgili bilgileri belirtin:
   1. Konak türü için işletim sistemini seçin.
   1. SnapCenter VM ana bilgisayar adını girin.
   1. Kullanmak istediğiniz kimlik bilgilerini sağlayın.
   1. **Microsoft Windows** ve **SAP HANA** seçeneklerini belirleyip **Gönder**' i seçin.

   :::image type="content" source="media/snapcenter/add-new-host-operating-system-credentials.png" alt-text="Yeni ana bilgisayar için bilgiler":::

   >[!IMPORTANT]
   >İlk düğümü yüklemeden önce, SnapCenter, kök olmayan bir kullanıcının veritabanına eklentiler yüklemesine izin verir.  Kök olmayan bir kullanıcının nasıl etkinleştirileceği hakkında bilgi için, bkz. [kök olmayan kullanıcı ekleme ve sudo ayrıcalıklarını yapılandırma](https://library.netapp.com/ecmdocs/ECMLP2590889/html/GUID-A3EEB5FC-242B-4C2C-B407-510E48A8F131.html).

1. Konak ayrıntılarını gözden geçirin ve eklentiyi SnapCenter sunucusuna yüklemek için **Gönder** ' i seçin.

1. Eklenti yüklendikten sonra, ek bileşen Merkezi ' nde **konaklar** ' ı seçin ve ardından **+ Ekle** ' yi seçerek bir hana düğümü ekleyin.

   :::image type="content" source="media/snapcenter/add-hana-node.png" alt-text="HANA düğümü ekleme" lightbox="media/snapcenter/add-hana-node.png":::

1. HANA düğümü için bilgi sağlayın:
   1. Konak türü için işletim sistemini seçin.
   1. HANA DB ana bilgisayar adını veya IP adresini girin.
   1. **+** Hana DB konak işletim sisteminde yapılandırılan kimlik bilgilerini eklemek için seçin ve ardından **Tamam**' ı seçin.
   1. **SAP HANA** seçip **Gönder**' i seçin.

   :::image type="content" source="media/snapcenter/add-hana-node-details.png" alt-text="SAP HANA düğüm ayrıntıları":::

1. Parmak izini onaylayın ve **Onayla ve Gönder '** i seçin.

   :::image type="content" source="media/snapcenter/confirm-submit-fingerprint.png" alt-text="Parmak izini Onayla ve Gönder":::

1. Hana düğümünde, sistem veritabanı altında **güvenlik** kullanıcıları ek bileşenleri ' ni seçerek  >    >   snapcenter kullanıcısını oluşturun.

   :::image type="content" source="media/snapcenter/create-snapcenter-user-hana-system-db.png" alt-text="HANA 'da (sistem DB) Snapın kullanıcısı oluşturma":::



### <a name="auto-discovery"></a>Otomatik bulma
SnapCenter 4,3 otomatik bulma işlevini varsayılan olarak sunar.  HANA sistem çoğaltması (HSR) özellikli HANA örnekleri için otomatik bulma desteklenmez. Örneği, Snapın sunucusuna el ile eklemeniz gerekir.


### <a name="hana-setup-manual"></a>HANA kurulumu (El Ile)
HSR 'yi yapılandırdıysanız sistemi el ile yapılandırmanız gerekir.  

1. Anlık görüntü merkezinde **kaynaklar** ve **San Hana** (en üstte) öğesini seçin ve **+ SAP HANA veritabanı Ekle** (sağda) seçeneğini belirleyin.

   :::image type="content" source="media/snapcenter/manual-hana-setup.png" alt-text="El ile HANA kurulumu" lightbox="media/snapcenter/manual-hana-setup.png":::

1. Linux konağında veya eklentilerin yüklendiği konakta yapılandırılan HANA yönetici kullanıcısının kaynak ayrıntılarını belirtin. Yedekleme, Linux sistemindeki eklentiden yönetilir.

   :::image type="content" source="media/snapcenter/provide-resource-details-sap-hana-database.png" alt-text="Linux konağında yapılandırılan HANA yönetici kullanıcısının kaynak ayrıntılarını belirtin.":::

1. Anlık görüntü almak için gereken veri birimini seçin, **Kaydet** ' i seçin ve ardından **son**' u seçin.

   :::image type="content" source="media/snapcenter/provide-storage-footprint.png" alt-text="Anlık görüntü almak için gereken veri birimini seçin, Kaydet ' i seçin ve ardından son ' u seçin.":::

### <a name="create-a-snapshot-policy"></a>Anlık görüntü ilkesi oluşturma

SAP HANA veritabanı kaynaklarını yedeklemek için SnapCenter 'ı kullanmadan önce, yedeklemek istediğiniz kaynak veya kaynak grubu için bir yedekleme ilkesi oluşturmanız gerekir. Anlık görüntü ilkesi oluşturma işlemi sırasında, ön/son komutlarını ve özel SSL anahtarlarını yapılandırma seçeneği sunulur. Anlık görüntü ilkesi oluşturma hakkında daha fazla bilgi için, bkz. [SAP HANA veritabanları için yedekleme Ilkeleri oluşturma](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html).

1. Anlık görüntü merkezi 'nde **kaynaklar** ' ı seçin ve ardından bir veritabanı seçin.

   :::image type="content" source="media/snapcenter/select-database-create-policy.png" alt-text="Anlık görüntü merkezi 'nde kaynaklar ' ı seçin ve ardından bir veritabanı seçin.":::

1. Anlık görüntü zamanlayıcısını yapılandırmak için yapılandırma sihirbazının iş akışını izleyin.

   :::image type="content" source="media/snapcenter/follow-workflow-configuration-wizard.png" alt-text="Anlık görüntü zamanlayıcısını yapılandırmak için yapılandırma sihirbazının iş akışını izleyin." lightbox="media/snapcenter/follow-workflow-configuration-wizard.png":::

1. Ön/son komutlarını ve özel SSL anahtarlarını yapılandırma seçeneklerini belirtin.  Bu örnekte, özel ayar kullandık.

   :::image type="content" source="media/snapcenter/configuration-options-pre-post-commands.png" alt-text="Post öncesi komutlarını ve özel SSL anahtarlarını yapılandırma seçeneklerini belirtin." lightbox="media/snapcenter/configuration-options-pre-post-commands.png":::

1. Diğer HANA veritabanları için de kullanılabilecek bir anlık görüntü ilkesi oluşturmak için **Ekle** ' yi seçin. 

   :::image type="content" source="media/snapcenter/select-one-or-more-policies.png" alt-text="Diğer HANA veritabanları için de kullanılabilecek bir anlık görüntü ilkesi oluşturmak için Ekle ' yi seçin.":::

1. İlke adını ve açıklamasını girin.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy.png" alt-text="İlke adını ve açıklamasını girin.":::


1. Yedekleme türünü ve sıklığını seçin.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-settings.png" alt-text="Yedekleme türünü ve sıklığını seçin.":::

1. İsteğe bağlı **yedekleme bekletme ayarlarını** yapılandırın.  Bizim örneğimizde, saklama için bekletme üç anlık görüntü kopyasına ayarlıyoruz.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-retention-settings.png" alt-text="İsteğe bağlı yedekleme bekletme ayarlarını yapılandırın.":::

1. **Saatlik bekletme ayarlarını** yapılandırın. 

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-hourly-retention-settings.png" alt-text="Saatlik bekletme ayarlarını yapılandırın.":::

1. Bir anlık görüntü görüntüsü yapılandırılmışsa, **Yerel anlık görüntü kopyası oluşturduktan sonra anlık görüntü görüntüsünü Güncelleştir**' i seçin.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-snapmirror.png" alt-text="Bir anlık görüntü görüntüsü gerekiyorsa, yerel bir anlık görüntü kopyası oluşturduktan sonra, anlık görüntü görüntüsünü Güncelleştir ' i seçin.":::

1. Yeni yedekleme ilkesinin özetini gözden geçirmek için **son** ' u seçin. 
1. **Zamanlamayı Yapılandır** altında **Ekle**' yi seçin.

   :::image type="content" source="media/snapcenter/configure-schedules-for-selected-policies.png" alt-text="Zamanlamayı Yapılandır altında Ekle ' yi seçin.":::

1. **Başlangıç tarihi** **, tarihi bitiş tarihi ve** sıklığı seçin.

   :::image type="content" source="media/snapcenter/add-schedules-for-policy.png" alt-text="Başlangıç tarihi, tarihi bitiş tarihi ve sıklığı seçin.":::

1. Bildirimler için e-posta ayrıntılarını sağlayın.

   :::image type="content" source="media/snapcenter/backup-policy-notification-settings.png" alt-text="Bildirimler için e-posta ayrıntılarını sağlayın.":::

1.  Yedekleme ilkesini oluşturmak için **son** ' u seçin.

### <a name="disable-ems-message-to-netapp-autosupport"></a>EMS iletisini NetApp oto desteğine devre dışı bırak
Varsayılan olarak, EMS veri toplama etkinleştirilir ve yükleme Tarihinizdeki her yedi günde bir çalışır.  PowerShell cmdlet 'i ile veri toplamayı devre dışı bırakabilirsiniz `Disable-SmDataCollectionEms` .

1. PowerShell 'de, ek yük merkeziyle bir oturum oluşturun.

   ```powershell
   Open-SmConnection
   ```

1. Kimlik bilgilerinizle oturum açın.
1. EMS iletileri koleksiyonunu devre dışı bırakın.

   ```powershell
   Disable-SmCollectionEms
   ```

### <a name="restore-database-after-crash"></a>Kilitlenmeden sonra veritabanını geri yükle
Veritabanını geri yüklemek için Snapın merkezini kullanabilirsiniz.  Bu bölümde, üst düzey adımları ele alacağız, ancak daha fazla bilgi için bkz. [snapı Ile yedekleme/kurtarma SAP HANA](https://www.netapp.com/us/media/tr-4614.pdf).


1. Veritabanını durdurun ve tüm veritabanı dosyalarını silin.

   ```
   su - h31adm
   > sapcontrol -nr 00 -function StopSystem
   StopSystem
   OK
   > sapcontrol -nr 00 -function GetProcessList
   OK
   name, description, dispstatus, textstatus, starttime, elapsedtime, pid
   hdbdaemon, HDB Daemon, GRAY, Stopped, , , 35902
 
   ```

1. Veritabanı birimini çıkarın.

   ```bash
   unmount /hana/data/H31/mnt00001
   ```


1. Veritabanı dosyalarını SnapCenter aracılığıyla geri yükleyin.  Veritabanını seçin ve ardından **geri yükle**' yi seçin.  

   :::image type="content" source="media/snapcenter/restore-database-via-snapcenter.png" alt-text="Bir veritabanı seçin ve geri yükle ' yi seçin." lightbox="media/snapcenter/restore-database-via-snapcenter.png":::

1. Geri yükleme türünü seçin.  Bizim örneğimizde, kaynağın tamamını geri göndereceğiz. 

   :::image type="content" source="media/snapcenter/restore-database-select-restore-type.png" alt-text="Geri yükleme türünü seçin.":::

   >[!NOTE]
   >Varsayılan bir kurulumla, disk üzerindeki anlık görüntüden yerel geri yükleme yapmak için komutlar belirtmeniz gerekmez. 

   >[!TIP]
   >Birimin içindeki belirli bir LUN 'yi geri yüklemek isterseniz **Dosya düzeyi**' ni seçin.

1. Yapılandırma Sihirbazı aracılığıyla iş akışını izleyin.
   
   SnapCenter, HANA 'da geri yükleme işlemini başlatmak için verileri özgün konuma geri yükler. Ayrıca, SnapCenter yedekleme kataloğunu değiştiremediğinden (veritabanı kapalıysa) bir uyarı görüntülenir.

   :::image type="content" source="media/snapcenter/restore-database-job-details-warning.png" alt-text="SnapCenter, yedekleme kataloğunu değiştiremediğinden bir uyarı görüntülenir. ":::

1. Tüm veritabanı dosyaları geri yüklendiği için, HANA 'daki geri yükleme işlemini başlatın. Hana Studio 'da **sistemler** altında sistem veritabanına sağ tıklayın ve **yedekleme ve kurtarma**  >  **sistem veritabanını kurtar**' ı seçin.

   :::image type="content" source="media/snapcenter/hana-studio-backup-recovery.png" alt-text="HANA 'da geri yükleme işlemini başlatın.":::

1. Bir kurtarma türü seçin.

   :::image type="content" source="media/snapcenter/restore-database-select-recovery-type.png" alt-text="Kurtarma türünü seçin.":::

1. Yedekleme kataloğunun konumunu seçin.

   :::image type="content" source="media/snapcenter/restore-database-select-location-backup-catalog.png" alt-text="Yedekleme kataloğunun konumunu seçin.":::

1. SAP HANA veritabanını kurtarmak için bir yedekleme seçin.

   :::image type="content" source="media/snapcenter/restore-database-select-backup.png" alt-text="SAP HANA veritabanını kurtarmak için bir yedekleme seçin.":::

   Veritabanı kurtarıldıktan sonra, bir **kez kurtarılan** ve **günlük konum** damgasına kurtarılan bir ileti görüntülenir.

1. **Sistemler** altında sistem veritabanına sağ tıklayın ve **yedekleme ve kurtarma**  >  **kiracı veritabanını kurtar**' ı seçin.
1. Kiracı veritabanının kurtarmasını gerçekleştirmek için sihirbazın iş akışını izleyin. 

Bir veritabanını geri yükleme hakkında daha fazla bilgi için bkz. [snapı Ile yedekleme/kurtarma SAP HANA](https://www.netapp.com/us/media/tr-4614.pdf).


### <a name="non-database-backups"></a>Veritabanı olmayan yedeklemeler
Veri olmayan birimleri (örneğin, bir ağ dosya paylaşımından (/Hana/Shared) veya bir işletim sistemi yedeklemesini geri yükleyebilirsiniz.  Veri olmayan bir birimi geri yükleme hakkında daha fazla bilgi için, bkz. [SAP HANA BACKUP/WITH Snapın](https://www.netapp.com/us/media/tr-4614.pdf).

### <a name="sap-hana-system-cloning"></a>Sistem kopyalama SAP HANA

' Yi kopyalayabilmeniz için, kaynak veritabanı ile aynı HANA sürümünü yüklemiş olmanız gerekir. SID ve ID farklı olabilir. 

:::image type="content" source="media/snapcenter/system-cloning-diagram.png" alt-text="Sistem kopyalama SAP HANA" lightbox="media/snapcenter/system-cloning-diagram.png" border="false":::

1. /Usr/SAP/H34/hdb40H34 veritabanı için bir HANA veritabanı kullanıcı deposu oluşturun.

   ```
   hdbuserstore set H34KEY sollabsjct34:34013 system manager
   ```
 
1. Güvenlik duvarını devre dışı bırakın.

   ```bash
   systemctl disable SuSEfirewall2
   systemctl stop  SuSEfirewall2
   ```

1. Java SDK 'sını yükler.

   ```bash
   zypper in java-1_8_0-openjdk
   ```

1. Anlık görüntü merkezinde, kopyanın bağlanacağı hedef Konağı ekleyin. Daha fazla bilgi için bkz. [uzak konaklara ana bilgisayar ekleme ve eklenti paketleri yükleme](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html).
   1. Eklemek istediğiniz farklı çalıştır kimlik bilgileri için bilgi sağlayın. 
   1. Konak işletim sistemini seçin ve ana bilgisayar bilgilerini girin.
   1. **Yüklemek için** eklentiler altında sürümü seçin, yüklemek için yolu girin ve **SAP HANA**' yi seçin.
   1. Yüklemeyi önyükleme denetimlerini çalıştırmak için **Doğrula** ' yı seçin.

1. HANA 'yı durdurun ve eski veri birimini çıkarın.  Kopyayı SnapCenter 'dan bağlayacaksınız.  

   ```bash
   sapcontrol -nr 40 -function StopSystem
   umount /hana/data/H34/mnt00001

   ```
 1. Hedef için yapılandırma ve kabuk betik dosyalarını oluşturun.
 
    ```bash
    mkdir /NetApp
    chmod 777 /NetApp
    cd NetApp
    chmod 777 sc-system-refresh-H34.cfg
    chmod 777 sc-system-refresh.sh

    ```

    >[!TIP]
    >Komut dosyalarını, anlık görüntü [merkezinden SAP kopyalaması](https://www.netapp.com/us/media/tr-4667.pdf)içinden kopyalayabilirsiniz.

1. Yapılandırma dosyasını değiştirin. 

   ```bash
   vi sc-system-refresh-H34.cfg
   ```

   * HANA_ARCHITECTURE = "MDC_single_tenant"
   * ANAHTAR = "H34KEY"
   * TIME_OUT_START = 18
   * TIME_OUT_STOP = 18
   * INSTANCENO = "40"
   * DEPOLAMA = "10.250.101.33"

1. Kabuk betik dosyasını değiştirin.

   ```bash
   vi sc-system-refresh.sh
   ```  

   * VERBOSE = Hayır
   * MY_NAME = " `basename $0` "
   * BASE_SCRIPT_DIR = " `dirname $0` "
   * MOUNT_OPTIONS = "RW, vers = 4, hard, Timeo = 600, rsize = 1048576, wsize = 1048576, INTR, noatime, nolock"

1. Kopyayı bir yedekleme işleminden başlatın. Kopyanın oluşturulacağı Konağı seçin. 

   >[!NOTE]
   >Daha fazla bilgi için bkz. [bir yedekten kopyalama](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html).

1. **Betikler** altında aşağıdakileri sağlayın:

   * **Mount komutu:** /NetApp/SC-System-Refresh.exe Mount H34% hana_data_h31_mnt00001_t250_vol_Clone
   * **Kopyalama sonrası komutu:** /NetApp/SC-System-Refresh.exe Recover H34

1. Önceden yüklenmiş veritabanının veri hacmi gerekli olmadığından/etc/fstab otomatik bağlama özelliğini devre dışı bırakın (kilitleyin). 

   ```bash
   vi /etc/fstab
   ```

### <a name="delete-a-clone"></a>Bir kopyayı silme

Artık gerekli değilse bir kopyayı silebilirsiniz. Daha fazla bilgi için bkz. [klonları silme](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html).

Kopyalama silinmeden önce yürütmek için kullanılan komutlar şunlardır:
* **Kopyalama öncesi silme:** /NetApp/SC-System-Refresh.exe kapat H34
* **Çıkarma:** /NetApp/SC-System-Refresh.exe H34

Bu komutlar, SnapCenter 'ın veritabanını görüntülemesine, birimden çıkmasına ve fstab girişini silmesine izin verir.  Bundan sonra, esnek kopya silinir. 

### <a name="cloning-database-logfile"></a>Veritabanı günlük dosyası kopyalanıyor

```   
20190502025323###sollabsjct34###sc-system-refresh.sh: Adding entry in /etc/fstab.
20190502025323###sollabsjct34###sc-system-refresh.sh: 10.250.101.31:/Sc21186309-ee57-41a3-8584-8210297f791d /hana/data/H34/mnt00001 nfs rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
20190502025323###sollabsjct34###sc-system-refresh.sh: Mounting data volume.
20190502025323###sollabsjct34###sc-system-refresh.sh: mount /hana/data/H34/mnt00001
20190502025323###sollabsjct34###sc-system-refresh.sh: Data volume mounted successfully.
20190502025323###sollabsjct34###sc-system-refresh.sh: chown -R h34adm:sapsys /hana/data/H34/mnt00001
20190502025333###sollabsjct34###sc-system-refresh.sh: Recover system database.
20190502025333###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/HDB40/exe/Python/bin/python /usr/sap/H34/HDB40/exe/python_support/recoverSys.py --command "RECOVER DATA USING SNAPSHOT CLEAR LOG"
[140278542735104, 0.005] >> starting recoverSys (at Thu May  2 02:53:33 2019)
[140278542735104, 0.005] args: ()
[140278542735104, 0.005] keys: {'command': 'RECOVER DATA USING SNAPSHOT CLEAR LOG'}
recoverSys started: ============2019-05-02 02:53:33 ============
testing master: sollabsjct34
sollabsjct34 is master
shutdown database, timeout is 120
stop system
stop system: sollabsjct34
stopping system: 2019-05-02 02:53:33
stopped system: 2019-05-02 02:53:33
creating file recoverInstance.sql
restart database
restart master nameserver: 2019-05-02 02:53:38
start system: sollabsjct34
2019-05-02T02:53:59-07:00  P010976      16a77f6c8a2 INFO    RECOVERY state of service: nameserver, sollabsjct34:34001, volume: 1, RecoveryPrepared
recoverSys finished successfully: 2019-05-02 02:54:00
[140278542735104, 26.490] 0
[140278542735104, 26.490] << ending recoverSys, rc = 0 (RC_TEST_OK), after 26.485 secs
20190502025400###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is started ....
20190502025400###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025410###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025420###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025430###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025440###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025451###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502025451###sollabsjct34###sc-system-refresh.sh: SAP HANA database is started.
20190502025451###sollabsjct34###sc-system-refresh.sh: Recover tenant database H34.
20190502025451###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/SYS/exe/hdb/hdbsql -U H34KEY RECOVER DATA FOR H34 USING SNAPSHOT CLEAR LOG
0 rows affected (overall time 69.584135 sec; server time 69.582835 sec)
20190502025600###sollabsjct34###sc-system-refresh.sh: Checking availability of Indexserver for tenant H34.
20190502025601###sollabsjct34###sc-system-refresh.sh: Recovery of tenant database H34 succesfully finished.
20190502025601###sollabsjct34###sc-system-refresh.sh: Status: GREEN
Deleting the DB Clone – Logfile
20190502030312###sollabsjct34###sc-system-refresh.sh: Stopping HANA database.
20190502030312###sollabsjct34###sc-system-refresh.sh: sapcontrol -nr 40 -function StopSystem HDB

02.05.2019 03:03:12
StopSystem
OK
20190502030312###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is stopped ....
20190502030312###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030322###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030332###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030342###sollabsjct34###sc-system-refresh.sh: Status:  GRAY
20190502030342###sollabsjct34###sc-system-refresh.sh: SAP HANA database is stopped.
20190502030347###sollabsjct34###sc-system-refresh.sh: Unmounting data volume.
20190502030347###sollabsjct34###sc-system-refresh.sh: Junction path: Sc21186309-ee57-41a3-8584-8210297f791d
20190502030347###sollabsjct34###sc-system-refresh.sh: umount /hana/data/H34/mnt00001
20190502030347###sollabsjct34###sc-system-refresh.sh: Deleting /etc/fstab entry.
20190502030347###sollabsjct34###sc-system-refresh.sh: Data volume unmounted successfully.

```

### <a name="uninstall-snapcenter-plug-ins-package-for-linux"></a>Linux için SnapCenter eklentileri paketini kaldırma

Linux eklentileri paketini komut satırından kaldırabilirsiniz. Otomatik dağıtım, yeni bir sistem beklediği için eklentinin kaldırılması kolaydır.  

>[!NOTE]
>Eklentinin daha eski bir sürümünü el ile kaldırmanız gerekebilir. 

Eklentileri kaldırın.

```bash
cd /opt/NetApp/snapcenter/spl/installation/plugins
./uninstall
```

Artık yeni düğümdeki en son HANA eklentisini, ek bileşen merkezi 'nde **Gönder** ' i seçerek yükleyebilirsiniz. 




## <a name="next-steps"></a>Sonraki adımlar
- Bkz. [olağanüstü durum kurtarma ilkeleri ve hazırlığı](hana-concept-preparation.md).
