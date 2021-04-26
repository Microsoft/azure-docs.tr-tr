---
title: Azure Data Box aracılığıyla Azure Dosya Eşitleme için şirket içi NAS geçişi
description: Şirket içi ağa bağlı depolama (NAS) konumundan Azure veri kutusu aracılığıyla Azure Dosya Eşitleme ile karma bulut dağıtımına dosya geçirmeyi öğrenin.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584220"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Ağ bağlantılı depolamadan (NAS) Azure Dosya Eşitleme ile karma bulut dağıtımına geçiş yapmak için veri kutusunu kullanın

Bu geçiş makalesi, NAS, Azure Dosya Eşitleme ve Azure veri kutusu anahtar sözcüklerini içeren birkaç bir biridir. Bu makalenin senaryonuz için geçerli olup olmadığını denetleyin:

> [!div class="checklist"]
> * Veri kaynağı: ağa bağlı depolama (NAS)
> * Geçiş rotası: NAS &rArr; veri kutusu &rArr; Azure dosya paylaşma &rArr; Windows Server ile eşitleme
> * Şirket içi dosyaları önbelleğe alma: Evet, son hedef, Azure Dosya Eşitleme bir dağıtımdır.

Senaryonuz farklıysa, [geçiş kılavuzlarındaki tabloya](storage-files-migration-overview.md#migration-guides)bakın.

Azure Dosya Eşitleme doğrudan bağlı depolama (DAS) konumlarında çalışarak ağa bağlı depolama (NAS) konumlarına eşitlemeyi desteklemez.
Bu olgu, dosyalarınızın bir geçişini yapar ve bu makalede böyle bir geçişin planlanması ve yürütülmesi sırasında size kılavuzluk eder.

## <a name="migration-goals"></a>Geçiş hedefleri

Amaç, NAS gerecinizde sahip olduğunuz paylaşımları bir Windows sunucusuna taşımaktır. Ardından karma bulut dağıtımı için Azure Dosya Eşitleme kullanın. Bu geçişin, geçiş sırasında üretim verilerinin bütünlüğünü ve kullanılabilirliğini garanti eden bir şekilde yapılması gerekir. İkinci olarak, kapalı kalma süresinin en az bir süre içinde tutulması gerekir, böylece normal bakım pencereleri içine sığacak veya yalnızca biraz daha fazla olabilir.

## <a name="migration-overview"></a>Geçişe genel bakış

Geçiş işlemi birkaç aşamadan oluşur. Azure depolama hesaplarını ve dosya paylaşımlarını dağıtmanız, bir şirket içi Windows Server dağıtmanız, Azure Dosya Eşitleme yapılandırmanız, RoboCopy kullanarak geçirmeniz ve son olarak, kesme yapmanız gerekir. Aşağıdaki bölümlerde, geçiş işleminin aşamaları ayrıntılı olarak açıklanır.

> [!TIP]
> Bu makaleye geri döndürülürken, kaldığınız yerden geçiş aşamasına geçmek için sağ taraftaki gezintiyi kullanın.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1. Aşama: kaç Azure dosya paylaşımına ihtiyacınız olduğunu belirleme

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>2. Aşama: Azure depolama kaynaklarını dağıtma

Bu aşamada, 1. Aşama ' daki eşleme tablosuna başvurun ve bunların içinde doğru sayıda Azure depolama hesabı ve dosya paylaşımı sağlamak için bunu kullanın.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>3. Aşama: kaç Azure veri kutusu gereçini gerektiğini belirleme

Bu adımı yalnızca önceki aşamayı tamamladığınızda başlatın. Azure depolama kaynaklarınızın (depolama hesapları ve dosya paylaşımları) Şu anda oluşturulması gerekir. Veri kutusu Siparişiniz sırasında, veri kutusu 'nun verileri taşımakta olduğu depolama hesaplarına belirtmeniz gerekir.

Bu aşamada, geçiş planının sonuçlarını önceki aşamasından, kullanılabilir veri kutusu seçeneklerinin sınırlarına eşlemeniz gerekir. Bu noktalar, hangi veri kutusu seçeneklerini belirlemeniz gerektiğine ve NAS paylaşımlarınızı Azure dosya paylaşımlarına taşımak için kaç tane ihtiyacınız olacağını bir plan yapmanıza yardımcı olur.

Ne tür cihazlara ihtiyacınız olduğunu öğrenmek için şu önemli limitleri göz önünde bulundurun:

* Herhangi bir Azure veri kutusu, verileri en fazla 10 depolama hesabına taşıyabilir. 
* Her bir veri kutusu seçeneği kendi kullanılabilir kapasitesine sahiptir. Bkz. [veri kutusu seçenekleri](#databox-options).

Oluşturmaya karar verdiğiniz depolama hesabı sayısı ve her birindeki paylaşımlar için geçiş planınıza danışın. Ardından, NAS 'inizdeki paylaşımların her birinin boyutuna bakın. Bu bilgileri birleştirmek, hangi gerecin hangi depolama hesaplarına veri göndermesi gerektiğini iyileştirmenize ve karar vermenize olanak tanır. İki veri kutusu cihazı, dosyaları aynı depolama hesabına taşıyabilir, ancak tek bir dosya paylaşımının içeriğini 2 veri kutularından bölemezsiniz.

### <a name="databox-options"></a>Veri kutusu seçenekleri

Standart geçiş için, bu üç DataBox seçeneğinin bir veya birleşimi seçilmelidir: 

* Veri kutusu diskleri Microsoft, en fazla 40 TiB için bir kapasiteye sahip bir ve en fazla beş SSD diski gönderecek. Şifreleme ve dosya sistemi ek yükü nedeniyle kullanılabilir kapasite yaklaşık %20 daha azdır. Daha fazla bilgi için bkz. [databox diskler belgeleri](../../databox/data-box-disk-overview.md).
* Veri kutusu bu en yaygın seçenektir. Bir NAS ile benzer şekilde çalışarak bir ruggekanlı DataBox gereci size gönderilir. Kullanılabilir 80 TiB kapasitesi vardır. Daha fazla bilgi için bkz. [databox belgeleri](../../databox/data-box-overview.md).
* Veri kutusu ağır bu seçenek, 1 PiB kapasitesine sahip bir NAS 'a benzer bir şekilde çalışma ve bir, bir NAS 'a benzer bir ruggekanlı veri kutusu gereci sunar. Şifreleme ve dosya sistemi ek yükü nedeniyle kullanılabilir kapasite yaklaşık %20 daha azdır. Daha fazla bilgi için bkz. [veri kutusu ağır belgeleri](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>4. Aşama: şirket içinde uygun bir Windows Server sağlama

Azure Data Box 'ın (es) gelmesini beklerseniz, Azure Dosya Eşitleme ile kullanacağınız bir veya daha fazla Windows sunucusunun ihtiyaçlarını gözden geçirmeye başlayabilirsiniz.

* Bir sanal makine veya fiziksel sunucu olarak en az 2012R2 ' de bir Windows Server 2019 oluşturun. Windows Server yük devretme kümesi de desteklenir.
* Doğrudan bağlı depolama alanı sağlayın veya ekleyin (desteklenmeyen bir şekilde, NAS ile karşılaştırıldığında).

Dağıttığınız Windows Server 'ın kaynak yapılandırması (işlem ve RAM), genellikle eşitlendirilecektir öğe sayısına (dosya ve klasör) bağlıdır. Herhangi bir endişeniz varsa daha yüksek performans yapılandırması önerilir.

[Eşitleme yapmanız gereken öğe sayısına (dosya ve klasör) göre bir Windows Server 'ın nasıl boyutlandıralınacağını öğrenin.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Daha önce bağlanılan makalede, sunucu belleği (RAM) için bir aralığa sahip bir tablo sunulmaktadır. Sunucunuz için daha küçük bir sayıya yaklaşmayı, ancak ilk eşitlemenin çok daha uzun sürmeyeceğini tahmin edebilirsiniz.

## <a name="phase-5-copy-files-onto-your-databox"></a>5. Aşama: dosyaları veri formunuza kopyalama

Veri kutusun ulaştığı zaman, NAS gerecinize yönelik görüş satırında veri kutusun kurulumunu yapmanız gerekir. Sipariş ettiğiniz veri kutusu türü için kurulum belgelerini izleyin.

* [Data Box’ı ayarlama](../../databox/data-box-quickstart-portal.md)
* [Data Box Disk’i ayarlama](../../databox/data-box-disk-quickstart-portal.md)
* [Data Box Heavy’yi ayarlama](../../databox/data-box-heavy-quickstart-portal.md)

Veri kutusu türüne bağlı olarak, sizin için kullanılabilen veri kutusu kopyalama araçları vardır. Bu noktada, dosyaları veri kutusuna tam uygunluk ile kopyalamadıklarından Azure dosya paylaşımlarına geçiş yapılması önerilmez. Bunun yerine RoboCopy kullanın.

Veri kutusun ulaştığı zaman, verileri sıralama sırasında belirttiğiniz her depolama hesabı için önceden sağlanmış SMB paylaşımlarına sahip olur.

* Dosyalarınız Premium bir Azure dosya paylaşımında yer alıyorsa Premium "dosya depolaması" depolama hesabı başına bir SMB paylaşımıyla karşılaşacaktır.
* Dosyalarınız standart bir depolama hesabına gidirsa standart (GPv1 ve GPv2) depolama hesabı başına üç SMB paylaşımı olacaktır. Geçişiniz için yalnızca ile biten dosya paylaşımları geçerlidir `_AzFiles` . Herhangi bir blok ve Sayfa Blobu paylaşımını yoksayın.

Azure veri kutusu belgelerindeki adımları izleyin:

1. [Data Box'a bağlanma](../../databox/data-box-deploy-copy-data.md)
1. Data Box'a veri kopyalama
1. [Veri kutusunu Azure 'a ayrılmaya hazırlayın](../../databox/data-box-deploy-picked-up.md)

Bağlantılı veri kutusu belgeleri bir RoboCopy komutu belirtir. Ancak, komut tam dosya ve klasör uygunluk kalitesini korumak için uygun değildir. Bunun yerine bu komutu kullanın:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>6. Aşama: Azure Dosya Eşitleme bulut kaynağını dağıtma

Bu kılavuza devam etmeden önce, tüm dosyalarınız doğru Azure dosya paylaşımlarında gelene kadar bekleyin. Veri kutusu verilerini gönderme ve alma işlemi zaman alır.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>7. Aşama: Azure Dosya Eşitleme aracısını dağıtma

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>8. Aşama: Windows Server 'da Azure Dosya Eşitleme yapılandırma

Kayıtlı şirket içi Windows Server, bu işlem için önceden ve internet 'e bağlı olmalıdır.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Bulut katmanlama özelliğini açın ve ilk indirme bölümünde "yalnızca ad alanı" seçeneğini belirleyin.

> [!IMPORTANT]
> Bulut katmanlaması, yerel sunucunun bulutta depolankıyasla daha az depolama kapasitesine sahip olmasına izin veren, ancak tam ad alanı kullanılabilir olan AFS özelliğidir. Yerel olarak ilginç veriler de hızlı erişim performansı için yerel olarak önbelleğe alınır. Bulut katmanlama, Azure Dosya Eşitleme "sunucu uç noktası" başına isteğe bağlı bir özelliktir. Tüm bulut verilerini barındıracak Windows Server üzerinde yeterli yerel disk kapasiteniz yoksa ve tüm verileri buluttan indirmemek istiyorsanız bu özelliği kullanmanız gerekir!

Eşitleme grubu oluşturma adımlarını ve eşleşen sunucu klasörünün, eşitleme için yapılandırılması gereken tüm Azure dosya paylaşımları/sunucu konumları için sunucu uç noktası olarak eklenmesi ' nı tekrarlayın. Ad alanının eşitlenmesi tamamlanana kadar bekleyin. Aşağıdaki bölüm, bunun nasıl emin olduğunu ayrıntılandırır.

> [!NOTE]
> Sunucu uç noktası oluşturulduktan sonra eşitleme çalışır. Bununla birlikte, eşitleme, DataBox aracılığıyla Azure dosya paylaşımında taşıdığınız dosya ve klasörleri numaralandırmak (bulmaları) için gereklidir. Ad alanının boyutuna bağlı olarak, bulutun ad alanı sunucuda görünmeye başlamadan önce bu çok uzun sürebilir.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>9. Aşama: ad alanının sunucuda tam olarak görünmesini bekleyin

Geçişin bir sonraki adımlarını, sunucunun ad alanını bulut paylaşımından tam olarak indirdiğini beklemeniz zorunludur. Dosyaları sunucuya çok erken taşımaya başladığınızda, gereksiz karşıya yüklemelere ve hatta dosya eşitleme çakışmalarına karşı risk uygulayabilirsiniz.

Sunucunuzun ilk indirme eşitlemesini tamamladığını söylemek için, Windows Server ile eşitleme Olay Görüntüleyicisi açın ve Azure Dosya Eşitleme telemetri olay günlüğünü kullanın.
Telemetri olay günlüğü, uygulamalar ve Services\microsoft\filesync\agentaltında Olay Görüntüleyicisi bulunur.

En son 9102 olayını arayın. Olay KIMLIĞI 9102, eşitleme oturumu tamamlandıktan sonra günlüğe kaydedilir. Olay metninde, indirme eşitleme yönü için bir alandır. ( `HResult` sıfır ve dosya indirilmesinin yanı sıra).
 
Sunucunun ad alanını indirmeyi bitirdiğinden emin olmak için bu tür ve içeriğin birbirini izleyen iki olayını görmek istiyorsunuz. 2 9102 olayları arasında farklı olay tetik, varsa bu tamam.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>10. Aşama: NAS 'ınızdan yakalama RoboCopy

Sunucunuz tüm ad alanının bulut paylaşımından ilk eşitlemesini tamamladıktan sonra, bu adımla devam edebilirsiniz. Bu adımla devam etmeden önce bu adımın tamamlanmış olması zorunludur. Ayrıntılar için önceki bölüme bakın.

Bu adımda, paylaşımlarınızın veri kutusu üzerinde olduğu zamandan beri, bulut paylaşımlarınızı, NAS 'inizdeki en son değişikliklerle yakalamak için RoboCopy işlerini çalıştıracaksınız.
Bu catch RoboCopy, NAS paylaşımlarınız üzerinde gerçekleşen dalgalanma miktarına bağlı olarak hızlı bir şekilde bitebileceği veya biraz zaman alabilir.

> [!WARNING]
> Windows Server 2019 ' de gerileyen bir RoboCopy davranışı nedeniyle, RoboCopy 'nin/MıR anahtarı katmanlı hedef dizinle uyumlu değildir. Geçişin bu aşamasında Windows Server 2019 veya Windows 10 istemcisini kullanmamalıdır. Bir ara Windows Server 2016 üzerinde RoboCopy kullanın.

Temel geçiş yaklaşımı, NAS gerecinizden Windows sunucunuza yönelik bir RoboCopy ve Azure dosya paylaşımlarına Azure Dosya Eşitleme.

İlk yerel kopyayı Windows Server hedef klasörünüze çalıştırın:

1. NAS gerecinizdeki ilk konumu belirler.
1. Windows Server 'da zaten Azure Dosya Eşitleme yapılandırılmış olan eşleşen klasörü belirler.
1. RoboCopy kullanarak kopyayı başlatma

Aşağıdaki RoboCopy komutu, yalnızca NAS depolama alanındaki farkları (güncelleştirilmiş dosya ve klasörler) Windows Server hedef klasörünüze kopyalayacaktır. Windows Server daha sonra bunları Azure dosya paylaşımıyla eşitler. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Windows Server 'da dosyalarınızın NAS gerecinden daha az depolama alanı sağladıysanız, bulut katmanlaması yapılandırmış olursunuz. Yerel Windows Server birimi tam aldığından, [bulut katmanlaması](storage-sync-cloud-tiering-overview.md) , zaten zaten eşitlenmiş olan ve katman dosyalarını açacaktır. Bulut katmanlaması, bir kopyanın NAS gerecinden devam etmesi için yeterli alan oluşturacak. Bulut katmanlaması, %99 birim boş alana ulaşmak için ne eşitlendiğini ve disk alanını boşaltmak için bir saatte bir kez kontrol eder.
Bu, RoboCopy 'nin çok sayıda dosyayı taşıması gerektiğinden, Windows Server 'da için yerel depolama alanını kullanmaktan daha fazladır. Ortalama olarak, RoboCopy 'nin çok daha hızlı hareket edebilecekleri Azure Dosya Eşitleme dosyalarınızı karşıya yükleyebilir ve bunları yerel haciminiz dışına taşıyabilirsiniz. RoboCopy başarısız olacak. Paylaşımlar üzerinde bunu önleyen bir sırayla çalışmanız önerilir. Örneğin, aynı anda tüm paylaşımlar için RoboCopy işlerini başlatmaktan veya yalnızca Windows Server 'daki geçerli boş alan miktarına uygun olan paylaşımları birkaç kez bahsetmez. Her şey,/MIR anahtarının yalnızca değişimleri taşımasını ve bir Delta taşındıktan sonra, yeniden başlatılan bir işin bu dosyayı yeniden taşımasına gerek kalmaz.

### <a name="user-cut-over"></a>Kullanıcı tarafından kesilen

RoboCopy komutunu ilk kez çalıştırdığınızda, kullanıcılarınız ve uygulamalarınız hala NAS 'daki dosyalara erişiyor ve bu dosyaları değiştirebilir. Bu, RoboCopy bir dizin işledi, bir sonrakine geçer ve ardından kaynak konumdaki (NAS) bir Kullanıcı, bu geçerli RoboCopy çalıştırmasında işlenmeyecek bir dosyayı ekler, değiştirir veya siler. Bu beklenen bir davranıştır.

İlk çalıştırma, verilerin toplu olarak Windows Server 'a ve Azure Dosya Eşitleme aracılığıyla buluta taşınması ile ilgilidir. Bu ilk kopya, şu değere bağlı olarak uzun zaman alabilir:

* karşıya yükleme bant genişliği
* Yerel ağ hızı ve RoboCopy iş parçacıklarının sayısının ne kadar en iyi şekilde eşleştiğini
* RoboCopy ve Azure Dosya Eşitleme tarafından işlenmesi gereken öğe sayısı (dosya ve klasörler)

İlk çalıştırma tamamlandıktan sonra, komutu yeniden çalıştırın.

Aynı paylaşıma yönelik olarak RoboCopy çalıştırdığınızda, yalnızca son çalıştırmasından bu yana gerçekleşen değişiklikleri taşıması gerektiğinden daha hızlı tamamlanır. Yinelenen işleri aynı paylaşımda çalıştırabilirsiniz.

Kapalı kalma süresini kabul ediyorsanız, NAS tabanlı paylaşımlarınız için Kullanıcı erişimini kaldırmanız gerekir. Bunu, kullanıcıların dosya ve klasör yapısını ve içeriğini değiştirmesini önleyen herhangi bir adımla yapabilirsiniz. DFS-Namespace, mevcut olmayan bir konuma işaret etmek veya paylaşımdaki kök ACL 'Leri değiştirmek örneğidir.

Bir son RoboCopy Round çalıştırın. Bu işlem, kaçırılmış olabilecek tüm değişiklikleri seçer.
Bu son adımın ne kadar süreceği, RoboCopy taramasının hızına bağlıdır. Önceki çalıştırmanın ne kadar sürdüğünü ölçerek süreyi tahmin edebilirsiniz (kapalı kalma süresine eşittir).

Windows Server klasöründe bir paylaşma oluşturun ve büyük olasılıkla DFS-N dağıtımınızı bunu işaret etmek üzere ayarlayın. Aynı paylaşma düzeyi izinlerini, NAS SMB paylaşımınızda olduğu gibi ayarladığınızdan emin olun. Kurumsal sınıf etki alanına katılmış bir NAS olsaydıysanız, kullanıcılar Active Directory ' de mevcut olduğu için Kullanıcı SID 'Leri otomatik olarak eşleştirecektir ve RoboCopy dosya ve meta verileri tam uygunlukta kopyalar. NAS 'inizdeki yerel kullanıcıları kullandıysanız, bu kullanıcıları Windows Server yerel kullanıcıları olarak yeniden oluşturmanız ve Windows Server 'a taşınan mevcut SID RoboCopy 'nin yeni, Windows Server yerel kullanıcılarınızın SID 'Lerine eşlenmesi gerekir.

Paylaşımı/paylaşım gruplarını ortak bir köke veya birime geçirmeyi tamamladınız. (1. aşama ile eşleştirmeye bağlı olarak)

Bu kopyaların birkaçını paralel olarak çalıştırmayı deneyebilirsiniz. Tek seferde bir Azure dosya paylaşımının kapsamını işlemeyi öneririz.

## <a name="troubleshoot"></a>Sorun giderme

' De çalıştırabileceğiniz en olası sorun, RoboCopy komutunun Windows Server tarafında *"Volume Full"* ile başarısız olmasına neden olur. Bulut katmanlaması, eşitlenen yerel Windows Server diskinden içerik boşaltmak için saatte bir kez davranır. Amaç, birimdeki %99 boş alana ulaşmaktır.

Eşitleme ilerleme durumu ve bulut katmanlaması için disk alanının serbest olmasına izin verin. Bunu, Windows sunucunuzdaki dosya Gezgini ' nde gözlemleyebilirsiniz.

Windows Server 'da yeterli kullanılabilir kapasite varsa, komutun yeniden çalıştırılması sorunu çözmeyecektir. Bu durumla karşılaşdığınızda hiçbir şey kesilmez ve güvenle ilerlemebilirsiniz. Komutu yeniden çalıştırmanın bir nedeni tek sonucudur.

Sorun giderme Azure Dosya Eşitleme sorunları için aşağıdaki bölümdeki bağlantıyı denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure dosya paylaşımları ve Azure Dosya Eşitleme hakkında daha fazla bilgi bulabilirsiniz. Aşağıdaki makalelerde gelişmiş seçenekler, en iyi uygulamalar ve ayrıca sorun giderme yardımı da yer almanıza yardımcı olur. Bu makaleler, [Azure dosya paylaşma belgelerini](storage-files-introduction.md) uygun şekilde bağlar.

* [Geçiş işlemine genel bakış](storage-files-migration-overview.md)
* [AFS genel bakış](./storage-sync-files-planning.md)
* [AFS dağıtım kılavuzu](./storage-how-to-create-file-share.md)
* [AFS sorunlarını giderme](storage-sync-files-troubleshoot.md)
