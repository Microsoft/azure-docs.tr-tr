---
title: Azure NetApp Files anlık görüntüleri nasıl çalışır? | Microsoft Docs
description: Anlık görüntü oluşturma yöntemleri, anlık görüntüleri geri yükleme yolları, siteler arası çoğaltma ayarlarında anlık görüntülerin nasıl kullanılacağı dahil olmak üzere Azure NetApp Files anlık görüntülerinin nasıl çalıştığını açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 526ef0af08833954aef4136716930cec0df40eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625256"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Azure NetApp Files anlık görüntülerinin işleyiş biçimi

Bu makalede Azure NetApp Files anlık görüntülerinin nasıl çalıştığı açıklanır. Azure NetApp Files anlık görüntü teknolojisi, performansı etkilemeden kararlılık, ölçeklenebilirlik ve daha hızlı kurtarılabilirlik sağlar. Azure NetApp Files Snapshot Technology, tek dosya geri yüklemeleri, toplu geri yüklemeler ve kopyalar ve çapraz bölge çoğaltma dahil olmak üzere veri koruma çözümleri için temel sağlar. 

Birim anlık görüntülerini kullanma hakkında adımlar için, bkz. [Azure NetApp Files kullanarak anlık görüntüleri yönetme](azure-netapp-files-manage-snapshots.md). Çapraz bölge çoğaltmasında anlık görüntü yönetimiyle ilgili konular için bkz. [çapraz bölge çoğaltmasını kullanma gereksinimleri ve konuları](cross-region-replication-requirements-considerations.md).

## <a name="what-volume-snapshots-are"></a>Birim anlık görüntülerinin  

Azure NetApp Files anlık görüntüsü, bir zaman noktası dosya sistemi (ses) görüntüsüdür. İdeal bir çevrimiçi yedekleme işlevi görebilir. [Yeni bir birim oluşturmak](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume), [bir dosyayı geri yüklemek](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)veya [bir birimi geri döndürmek](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)için bir anlık görüntü kullanabilirsiniz. Azure NetApp Files birimlerde depolanan belirli uygulama verilerinde, uygulama tutarlılığı sağlamak için ek adımlar gerekli olabilir.  

Düşük iş yükü anlık görüntüleri, Azure NetApp Files bir parçası olan yetersiz birim sanallaştırma teknolojisinin benzersiz özellikleri tarafından mümkün hale getirilir. Bir veritabanı gibi, bu katman diskte gerçek veri blokları için işaretçiler kullanır. Ancak, bir veritabanının aksine, var olan blokları yeniden yazmaz; güncelleştirilmiş verileri yeni bir bloğa yazar ve işaretçiyi değiştirir. Azure NetApp Files anlık görüntüsü yalnızca blok işaretçilerini yönetir, bir birimin "dondurulmuş" ve salt okunurdur bir görünüm oluşturarak uygulamaların özel programlama olmadan dosyaların ve Dizin hiyerarşilerinin daha eski sürümlerine erişmesini sağlar. Gerçek veri blokları kopyalanmaz. Bu nedenle, anlık görüntüler, bunları oluşturmak için gereken süre içinde etkilidir; birim boyutundan bağımsız olarak, neredeyse anında yaklaşırlar. Anlık görüntüler, depolama alanında da etkilidir. Bunlar kendilerine boşluk vermez ve yalnızca anlık görüntüler ile etkin birim arasındaki Delta blokları tutulur. 

Aşağıdaki diyagramlarda kavramlar gösterilmektedir:  

![Anlık görüntülerin temel kavramlarını gösteren diyagramlar](../media/azure-netapp-files/snapshot-concepts.png)

Diyagramlarda Şekil 1a 'da bir anlık görüntü alınır. Şekil 1B, değiştirilen veriler *Yeni bir bloğa* yazılır ve işaretçi güncellenir. Ancak anlık görüntü işaretçisi, verilerin canlı ve geçmiş bir görünümünü sunarak *daha önce yazılmış bloğa* işaret eder. Şekil 1C ' da başka bir anlık görüntü alınır. Artık üç tam kopyanın gerektirdiği birim alanını oluşturmadan üç veri kuşakına (canlı veriler, anlık görüntü 2 ve Snapshot 1) erişebilirsiniz. 

Anlık görüntü yalnızca birim meta verilerinin bir kopyasını alır (*INode tablosu*). Birim boyutundan, kullanılan kapasiteye veya birimdeki etkinlik düzeyine bakılmaksızın oluşturulması yalnızca birkaç saniye sürer. Bu nedenle, bir 100-TiB biriminin anlık görüntüsünü almak, 100-GiB biriminin anlık görüntüsünü alırken aynı zamanda (sıfır ' dan sonra) daha fazla sürer. Bir anlık görüntü oluşturulduktan sonra, veri dosyalarındaki değişiklikler normal olarak dosyaların etkin sürümünde yansıtılır.

Bu arada, bir anlık görüntüden işaret eden veri blokları kararlı ve sabit kalır. Azure NetApp Files birimlerinin "yazma sırasında yeniden yönlendir" yapısı nedeniyle, bir anlık görüntü hiçbir boşluk tüketmez. Her bir anlık görüntü arasındaki değiştirilen blokların sayısı kadar az kapasite tüketen, her zaman içinde birim başına en fazla 255 anlık görüntü saklayabilirsiniz. Değiştirilen bloklar etkin birimde depolanır. Anlık görüntülerde işaret edilen bloklar, yalnızca tüm işaretçiler (etkin birim ve anlık görüntüler) temizlenme amacıyla, safekeeping için birimde (salt okuma olarak) tutulur. Bu nedenle, toplu kullanım zaman içinde, yeni veri blokları veya (değiştirilen) anlık görüntülerde tutulan veri blokları tarafından artacaktır.

 Aşağıdaki diyagramda bir birimin anlık görüntüleri ve zaman içinde kullanılan alan gösterilmektedir: 

![Bir birimin anlık görüntülerini ve zaman içinde kullanılan alanı gösteren diyagram](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Bir birim anlık görüntüsü yalnızca en son anlık görüntüden bu yana yalnızca blok değişikliklerini kaydettiği için aşağıdaki önemli avantajları sağlar:

* Anlık görüntüler ***depolama etkilidir***.   
    Anlık görüntüler en az depolama alanını tükettiğinden tüm birimin veri bloklarını kopyalamaz. Sırayla alınan iki anlık görüntü yalnızca iki arasındaki zaman aralığında eklenen veya değiştirilen bloklara göre farklılık gösterir. Bu blok-artımlı davranış, ilişkili depolama kapasitesi tüketimini sınırlandırır. Birçok alternatif anlık görüntü uygulaması, depolama kapasitesi gereksinimlerini yükseltmek için etkin dosya sistemine eşit depolama birimleri kullanır. Uygulama günlük *blok düzeyinde* değişiklik oranlarına bağlı olarak Azure NetApp Files anlık görüntüler daha fazla veya daha az kapasite tüketir, ancak yalnızca değiştirilen verileri kullanır. Yalnızca birçok uygulama birimi için kullanılan birim kapasitesinin% 1-5 ' undan veya SAP HANA veritabanı birimleri gibi birimlerde% 20-30 ' e kadar günlük ortalama anlık görüntü tüketimi aralığı. Oluşturulan ve korunan anlık görüntü sayısına göre anlık görüntü kapasitesi tüketimi için [birim ve anlık görüntü kullanımınızı izlemenin](azure-netapp-files-metrics.md#volumes) emin olun.   

* Anlık görüntüler ***oluşturma, çoğaltma, geri yükleme veya kopyalama hızlı bir şekilde*** yapılır.   
    Birim boyutu ve etkinlik düzeyinden bağımsız olarak bir anlık görüntü oluşturmak, çoğaltmak, geri yüklemek veya klonlamak yalnızca birkaç saniye sürer. [İsteğe bağlı](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume)bir birim anlık görüntüsü oluşturabilirsiniz. Ayrıca, Azure NetApp Files otomatik olarak bir anlık görüntü oluşturmak ve bir birim için kaç anlık görüntü tutması gerektiğini belirtmek için [anlık görüntü ilkelerini](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) de kullanabilirsiniz.  Uygulama tutarlılığı, örneğin SAP HANA için [Azacsnap aracını](azacsnap-introduction.md) kullanarak, uygulama katmanıyla birlikte anlık görüntüler elde edilebilir.

* Anlık görüntülerin birim ***performansı*** üzerinde hiçbir etkisi yoktur.   
    Düşük düzenleme teknolojisinin "yazma sırasında yeniden yönlendir" yapısı nedeniyle Azure NetApp Files anlık görüntülerinin depolanması veya saklanması, yoğun veri etkinliğiyle birlikte performans etkisine sahip değildir. Bir anlık görüntüyü silmenin çoğu durumda bir performans etkisi de daha azdır. 

* Anlık görüntüler, sık oluşturulabildiğinden ve birçok Korunabileceğinden ***ölçeklenebilirlik*** sağlar.   
    Azure NetApp Files birimler 255 adede kadar anlık görüntüyü destekler. Çok sayıda düşük etki, sık oluşturulan anlık görüntü depolama özelliği, istenen veri sürümünün başarıyla kurtarılma olasılığını artırır.

* Anlık görüntüler ***Kullanıcı görünürlüğü** _ ve _ *_Dosya kurtarılabilirlik_* * sağlar.   
Azure NetApp Files anlık görüntü teknolojisinin yüksek performans, ölçeklenebilirlik ve kararlılığı, Kullanıcı tabanlı kurtarma için ideal bir çevrimiçi yedekleme sağlar. Anlık görüntüler, dosya, dizin veya birim geri yükleme amacıyla Kullanıcı tarafından erişilebilir hale getirilebilir. Ek çözümler, yedeklemeleri çevrimdışı depolamaya kopyalamanızı veya koruma ya da olağanüstü durum kurtarma amacıyla [çapraz bölge çoğaltmasını](cross-region-replication-introduction.md) sağlar.

## <a name="ways-to-create-snapshots"></a>Anlık görüntü oluşturma yolları   

Anlık görüntü oluşturmak ve sürdürmek için çeşitli yöntemler kullanabilirsiniz:

* Kullanarak el ile (isteğe bağlı):   
    * [Azure Portal](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [REST API](/rest/api/netapp/snapshots), [Azure CLI](/cli/azure/netappfiles/snapshot)veya [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot) araçları
    * Betikler (bkz. [örnekler](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts))

* Otomatik, kullanarak:
    * [Azure Portal](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), [REST API](/rest/api/netapp/snapshotpolicies), [Azure CLI](/cli/azure/netappfiles/snapshot/policy)veya [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy) araçları aracılığıyla anlık görüntü ilkeleri
    * [Azacsnap](azacsnap-introduction.md) gibi uygulamayla tutarlı anlık görüntü araçları

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>Birimler ve anlık görüntülerin DR için çapraz bölge olarak nasıl çoğaltılacağı  

Azure NetApp Files olağanüstü durum kurtarma (DR) amaçları için [bölgeler arası çoğaltmayı](cross-region-replication-introduction.md) destekler. Azure NetApp Files çapraz bölge çoğaltma, anlık görüntü teknolojisini kullanır. Yalnızca değiştirilen bloklar ağ üzerinden sıkıştırılmış, etkin bir biçimde gönderilir. Birimler arasında bir çapraz bölge çoğaltması başlatıldıktan sonra, tüm birim içerikleri (yani, gerçek depolanmış veri blokları) yalnızca bir kez aktarılır. Bu işleme, *temel aktarma* denir. İlk aktarımdan sonra, yalnızca değiştirilen bloklar (anlık görüntülerle yakalanan olarak) aktarılır. Sonuç olarak, tüm anlık görüntüler dahil olmak üzere kaynak birimin zaman uyumsuz 1:1 bir çoğaltmasıdır. Bu davranış, tam ve artımlı süresiz bir çoğaltma mekanizmasını izler. Bu teknoloji, bölgeler genelinde çoğaltmak için gereken veri miktarını en aza indirir, bu nedenle veri aktarımı maliyetleri kaydediliyor. Ayrıca çoğaltma süresini kısaltır. Daha küçük bir kurtarma noktası hedefi (RPO) elde edebilirsiniz, çünkü daha fazla anlık görüntü oluşturulabilir ve sınırlı veri aktarımlarıyla daha sık aktarılabilir. Ayrıca, ana bilgisayar tabanlı çoğaltma mekanizmaları gereksinimini ortadan kaldırmak, sanal makine ve yazılım lisansı maliyetinden kaçınmayı önler.

Aşağıdaki diyagramda, çapraz bölge çoğaltma senaryolarında anlık görüntü trafiği gösterilmektedir: 

![Çapraz bölge çoğaltma senaryolarında anlık görüntü trafiğini gösteren diyagram](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>Anlık görüntülerden verileri geri yükleme yolları  

Azure NetApp Files anlık görüntü teknolojisi, yedeklemelerin sıklığını ve güvenilirliğini büyük ölçüde geliştirir. En az performans yükü doğurur ve etkin bir birimde güvenli bir şekilde oluşturulabilir. Azure NetApp Files anlık görüntüleri, neredeyse anlık, güvenli ve isteğe bağlı olarak Kullanıcı tarafından yönetilen geri yüklemeler sağlar. Bu bölümde, verilerin Azure NetApp Files anlık görüntülerden erişilebilen veya geri yüklenebileceği çeşitli yollar açıklanmaktadır.

### <a name="restoring-files-or-directories-from-snapshots"></a>Anlık görüntülerden dosya veya dizinleri geri yükleme 

[Anlık görüntü yolu görünürlüğü](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) olarak ayarlanmamışsa `hidden` , kullanıcılar, verilerin yanlışlıkla silinmesini, bozulmasını veya değiştirilmesini kurtarmak için anlık görüntülere doğrudan erişebilir. Dosya ve dizinlerin güvenliği anlık görüntüde tutulur ve anlık görüntüler tasarım tarafından salt okunurdur. Bu nedenle, geri yükleme güvenli ve kolaydır. 

Aşağıdaki diyagramda bir anlık görüntüye dosya veya dizin erişimi gösterilmektedir: 

![Bir anlık görüntüye dosya veya dizin erişimi gösteren diyagram](../media/azure-netapp-files/snapshot-file-directory-access.png)

Diyagramda, anlık görüntü 1 yalnızca etkin birim ve anlık görüntü oluşturma sırasında olan Delta bloklarını kullanır. Ancak anlık görüntüye birim anlık görüntü yolu aracılığıyla eriştiğinizde, veriler anlık görüntü oluşturma sırasında tam birim kapasitesi gibi *görünür* . Kullanıcılar anlık görüntü klasörlerine erişerek, dosya ve dizinleri tercih ettiğiniz bir anlık görüntüden kopyalayarak verileri kendi kendine geri yükleyebilir.

Benzer şekilde, hedef çapraz bölge çoğaltma birimlerindeki anlık görüntülerle, DR bölgesindeki veri kurtarma için salt okunabilir olarak erişilebilir.  

Aşağıdaki diyagramda, bölgeler arası çoğaltma senaryolarında anlık görüntü erişimi gösterilmektedir: 

![Çapraz bölge çoğaltmasında anlık görüntü erişimini gösteren diyagram](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Anlık görüntülerden tek tek dosyaları veya dizinleri geri yükleme hakkında [istemci kullanarak bir dosyayı anlık görüntüden geri yükleme](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) bölümüne bakın.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>Bir anlık görüntüyü yeni bir birime geri yükleme (kopyalama)

Azure NetApp Files anlık görüntülerini ayrı, bağımsız bir birime geri yükleyebilirsiniz. Bu işlem, birim boyutundan ve tüketilen kapasiteden bağımsız olarak neredeyse yakın bir işlemdir. Yeni oluşturulan birim, gerçek birim ve anlık görüntü veri blokları üzerine kopyalanırken neredeyse hemen erişim için kullanılabilir. Birim boyutuna ve kapasiteye bağlı olarak, bu işlem üst birimin ve anlık görüntünün silinebileceği zaman alabilir. Ancak, birim ilk oluşturulduktan sonra zaten erişilebilir, ancak kopyalama işlemi arka planda devam ediyor. Bu özellik, test ve geliştirme için veri kurtarma veya birim kopyalama için hızlı birim oluşturmayı mümkün bir şekilde sunar. Veri kopyalama işleminin doğası gereği, geri yükleme tamamlandığında depolama kapasitesi havuzu tüketimi Double olur ve yeni birim orijinal anlık görüntünün tüm etkin kapasitesini gösterir. Bu işlem tamamlandıktan sonra, birim özgün birimle bağımsız olur ve kaynak birimler ve anlık görüntü, yeni birimden bağımsız olarak yönetilebilir veya kaldırılabilir.

Aşağıdaki diyagramda bir anlık görüntüyü geri yükleyerek (kopyalayarak) oluşturulan yeni bir birim gösterilmektedir:   

![Anlık görüntü geri yüklendikten sonra oluşturulan yeni bir birimi gösteren diyagram](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

Aynı işlemler, çoğaltılan anlık görüntülerle bir olağanüstü durum kurtarma (DR) birimine da uygulanabilir. Çapraz bölge çoğaltma etkin veya devam ediyor olsa bile, herhangi bir anlık görüntü yeni bir birime geri yüklenebilir. Bu özellik, bir DR bölgesindeki test ve geliştirme ortamlarının kesintiye uğramaması, verileri kullanmak üzere yerleştirirken, çoğaltılan birimler Aksi takdirde yalnızca DR amaçları için kullanılır. Bu kullanım örneği, üretim ortamlarında olası etkileri ortadan kaldıran test ve geliştirmenin üretimden yalıtılmasını sunar.  

Aşağıdaki diyagramda, çapraz bölge çoğaltması gerçekleşirken DR hedef birim anlık görüntüsü kullanılarak birim geri yüklemesi (kopyalama) gösterilmektedir:  

![DR hedef birim anlık görüntüsünü kullanarak birim geri yüklemeyi gösteren diyagram](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Bkz. toplu geri yükleme işlemleri hakkında [Yeni bir birime anlık görüntü geri yükleme](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) .

### <a name="restoring-reverting-a-snapshot-in-place"></a>Bir anlık görüntüyü yerinde geri yükleme (geri alma)

Bazı durumlarda, yeni birim depolama kapasitesini tükettiğinden, bir anlık görüntüden yeni bir birim oluşturmak gerekmeyebilir veya uygun olmayabilir. Veri bozulmasını hızla kurtarmak için (örneğin, veritabanı bozulmaları veya fidye saldırılarına karşı), bir anlık görüntüyü birimin içinde geri yüklemek daha uygun olabilir. Bu işlem, Azure NetApp Files anlık görüntü alma işlevselliği kullanılarak yapılabilir. Bu işlevsellik, belirli bir anlık görüntü alındığı sırada bir birimi hızlı bir şekilde bir duruma döndürmenizi sağlar. Çoğu durumda, bir birimin geri döndürülmesi, tek tek dosyaları bir anlık görüntüden etkin dosya sistemine geri yüklemeden çok daha hızlıdır, özellikle de büyük, çok TiB birimlerde. 

Bir birim anlık görüntüsünün geri döndürülmesi, en büyük birimler için bile neredeyse birkaç saniye sürer. Etkin birim meta verileri (*INode tablosu*), anlık görüntü oluşturma zamanından itibaren anlık görüntü meta verileriyle değiştirilmiştir, bu nedenle birimi o zaman belirli bir noktaya geri döndürülüyor. Dönüşün etkili olabilmesi için hiçbir veri bloğu kopyalanması gerekmez. Bu nedenle, bir anlık görüntüyü yeni bir birime geri yüklemeden daha verimli bir alandır. 

Aşağıdaki diyagramda daha önceki bir anlık görüntüye geri dönülecek bir birim gösterilmektedir:  

![Önceki bir anlık görüntüye geri dönme bir birimi gösteren diyagram](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> Yazılan etkin dosya sistemi verileri ve seçilen anlık görüntü alındıktan sonra alınan anlık görüntüler kaybedilir. Anlık görüntü değiştirme işlemi, Hedeflenen birimdeki tüm verilerin seçilen anlık görüntüdeki verilerle değiştirilir. Bir anlık görüntü seçtiğinizde anlık görüntü içeriği ve oluşturma tarihi ile ilgilenmelisiniz. Anlık görüntü geri alma işlemini geri alamazsınız.  

Bu özelliğin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [anlık görüntü alma kullanarak bir birimi döndürmeyi](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

## <a name="how-snapshots-are-deleted"></a>Anlık görüntülerin nasıl silindiği 

Anlık görüntüler, depolama kapasitesini kullanır. Bu nedenle, genellikle süresiz olarak tutulmazlar. Veri koruma, bekletme ve kurtarılabilirlik için, bir dizi anlık görüntü (zaman içinde çeşitli noktalarda oluşturulur), RPO, RTO ve bekletme SLA gereksinimlerine bağlı olarak belirli bir süre için genellikle çevrimiçi tutulur. Ancak, daha eski anlık görüntülerin depolama hizmetinde tutulması gerekmez ve alan boşaltmak için silinmesi gerekebilir. Herhangi bir zamanda yönetici tarafından herhangi bir anlık görüntü (oluşturma sırasında olması gerekmez) silinebilir. 

> [!IMPORTANT]
> Anlık görüntü silme işlemi geri alınamaz. Veri koruma ve bekletme amaçları için birimin çevrimdışı kopyalarını tutmanız gerekir. 

Anlık görüntü silindiğinde, bu anlık görüntüden varolan veri bloklarına kadar olan tüm işaretçiler kaldırılır. Bir veri bloğunun kendisine işaret eden işaretçileri olmadığında (etkin birim veya birimdeki diğer anlık görüntülerle), veri bloğu ileride kullanılmak üzere birim boş alanına döndürülür. Bu nedenle, anlık görüntülerin kaldırılması genellikle bir birimde etkin birimden verileri silmekten daha fazla kapasite bırakır çünkü veri blokları genellikle önceden oluşturulmuş anlık görüntülerle yakalanır. 

Aşağıdaki diyagramda, bir birimden Snapshot 3 silme işleminin depolama tüketimine etkisi gösterilmektedir:  

![Anlık görüntü silmenin depolama tüketimi efektini gösteren diyagram](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

[Birim ve anlık görüntü tüketimini izlemeye](azure-netapp-files-metrics.md#volumes) ve uygulamanın, etkin birimin ve anlık görüntü tüketiminin nasıl etkileşime gireceğini anladığınızdan emin olun. 

Anlık görüntü silmeyi yönetme hakkında [anlık görüntüleri silme](azure-netapp-files-manage-snapshots.md#delete-snapshots) bölümüne bakın. Bkz. bu işlemi otomatikleştirme hakkında [anlık görüntü Ilkelerini yönetme](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) .

## <a name="next-steps"></a>Sonraki adımlar

* [Azure NetApp Files kullanarak anlık görüntüleri yönetme](azure-netapp-files-manage-snapshots.md)
* [Birim ve anlık görüntü ölçümlerini izleme](azure-netapp-files-metrics.md#volumes)
* [Anlık görüntü ilkelerinde sorun giderme](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files için kaynak sınırları](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files Snapshot 101 videosu](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files anlık görüntüye genel bakış](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)



