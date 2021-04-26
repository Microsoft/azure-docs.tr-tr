---
title: Azure Dosya Eşitleme'ye StorSimple 1200 geçişi
description: StorSimple 1200 serisi sanal gerecini Azure Dosya Eşitleme 'a geçirmeyi öğrenin.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8562d63bf227fff665c70674c7fe66922bce9992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98882289"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Azure Dosya Eşitleme'ye StorSimple 1200 geçişi

StorSimple 1200 serisi, şirket içi veri merkezinde çalışan bir sanal gereç. Bu gerecten verileri bir Azure Dosya Eşitleme ortamına geçirmek mümkündür. Azure Dosya Eşitleme, StorSimple gereçlerinin geçirilebilmesi için varsayılan ve stratejik, uzun süreli Azure hizmetidir.

StorSimple 1200 serisi, adım 2022 ' de [yaşam sonuna](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) ulaşacaktır.  Geçişinizi mümkün olan en kısa sürede planlamaya başlamak önemlidir. Bu makalede, Azure Dosya Eşitleme başarılı bir geçiş için gereken arka plan bilgisi ve geçiş adımları sağlanmaktadır. 

## <a name="azure-file-sync"></a>Azure Dosya Eşitleme

> [!IMPORTANT]
> Microsoft, müşterilerine kendi geçişlerinde yardımcı olmaya kararlıdır. AzureFilesMigration@microsoftÖzelleştirilmiş bir geçiş planı için e-posta. com ve geçiş sırasında yardım.

Azure Dosya Eşitleme, iki ana bileşeni temel alan bir Microsoft bulut hizmetidir:

* Dosya eşitleme ve bulut katmanlama.
* Azure 'da, SMB ve dosya geri kalanı gibi birden çok protokol üzerinden erişilebilen yerel depolama olarak dosya paylaşımları. Azure dosya paylaşımının bir Windows Server üzerindeki dosya paylaşımıyla karşılaştırılabilen bir ağ sürücüsü olarak yerel olarak takabilirsiniz. Öznitelikler, izinler ve zaman damgaları gibi önemli dosya uygunluk yönlerini destekler. StorSimple 'tan farklı olarak, bulutta depolanan dosya ve klasörleri yorumlamak için uygulama/hizmet gerekmez. Genel amaçlı dosya sunucusu verilerinin yanı sıra bazı uygulama verilerini bulutta depolamak için ideal ve en esnek yaklaşım.

Bu makalede geçiş adımları ele alınmaktadır. Azure Dosya Eşitleme hakkında daha fazla bilgi edinmek istiyorsanız, aşağıdaki makaleleri öneririz:

* [Azure Dosya Eşitleme-genel bakış](./storage-sync-files-planning.md "Genel Bakış")
* [Azure Dosya Eşitleme dağıtım kılavuzu](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Geçiş hedefleri

Amaç, üretim verilerinin bütünlüğünü güvence altına almak ve kullanılabilirliği güvence altına almak sağlamaktır. İkinci olarak, kapalı kalma süresinin en az bir süre içinde tutulması gerekir, böylece normal bakım pencereleri içine sığacak veya yalnızca biraz daha fazla olabilir.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200 geçiş yolu Azure Dosya Eşitleme

Azure Dosya Eşitleme aracısını çalıştırmak için yerel bir Windows Server gereklidir. Windows Server en az bir 2012R2 sunucusu olabilir, ancak ideal bir Windows Server 2019 ' dir.

Birçok farklı geçiş yolu vardır ve bunların tümünü belgelemek ve bu makalede en iyi yöntem olarak önerdiğimiz yol üzerinde neden risk veya olumsuz yönleri oluşturduğunu göstermek için çok uzun bir makale oluşturur.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Bu makalede aşağıdaki adımları izleyerek geçiş yoluna genel bakış.":::

Önceki görüntüde, bu makaledeki bölümlere karşılık gelen adımlar gösterilmektedir.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>1. Adım: şirket içi Windows Server ve depolama alanınızı sağlama

1. Bir sanal makine veya fiziksel sunucu olarak en az 2012R2 ' de bir Windows Server 2019 oluşturun. Windows Server yük devretme kümesi de desteklenir.
2. Doğrudan bağlı depolama alanı sağlayın veya ekleyin (desteklenmeyen bir şekilde, NAS ile karşılaştırıldığında). Windows Server depolama alanı boyutu, sanal StorSimple 1200 gerecinizin kullanılabilir kapasitesinin boyutuna eşit veya ondan daha büyük olmalıdır.

### <a name="step-2-configure-your-windows-server-storage"></a>2. Adım: Windows Server depolama alanınızı yapılandırma

Bu adımda, StorSimple depolama yapınızı (birimler ve paylaşımlar) Windows Server depolama yapınızı eşlersiniz.
Depolama yapınız üzerinde değişiklik yapmayı planlıyorsanız, birim sayısını, veri klasörlerinin birimlerle ilişkilendirmesini veya geçerli SMB/NFS paylaşımlarınızın üzerindeki veya altındaki alt klasör yapısını, şimdi bu değişikliklerin dikkate alınması zaman alır.
Dosya ve klasör yapınızı Azure Dosya Eşitleme yapılandırıldıktan sonra değiştirmek, kısabera ve kaçınılması gerekir.
Bu makalede 1:1 eşleştirdiğinizi varsayılmaktadır, bu nedenle bu makaledeki adımları uyguladığınızda eşleme değişikliklerinizi göz önünde bulundurmanız gerekir.

* Üretim verilerinizin hiçbiri Windows Server sistem biriminde bitmemelidir. Bulut katmanlaması sistem birimlerinde desteklenmez. Ancak, bu özellik geçiş için ve bir StorSimple değişikliği olarak sürekli işlemler için gereklidir.
* Windows Server 'da StorSimple 1200 sanal gerecinizde sahip olduğunuz sayıda birimi sağlayın.
* İhtiyacınız olan tüm Windows Server rollerini, özelliklerini ve ayarlarını yapılandırın. İşletim sistemini güvenli ve güncel tutmak için Windows Server güncelleştirmelerini kullanmanızı öneririz. Benzer şekilde, Azure Dosya Eşitleme Aracısı da dahil olmak üzere Microsoft uygulamalarını güncel tutmak için Microsoft Update önerilir.
* Aşağıdaki adımları okumadan önce herhangi bir klasörü veya paylaşımı yapılandırmayın.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>3. Adım: ilk Azure Dosya Eşitleme bulut kaynağını dağıtma

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>4. Adım: yerel birim ve klasör yapınızı Azure Dosya Eşitleme ve Azure dosya paylaşma kaynaklarıyla eşleştirin

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>5. Adım: Azure dosya paylaşımlarını sağlama

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>6. Adım: Windows Server hedef klasörlerini yapılandırma

Önceki adımlarda, eşitleme topolojlerinizin bileşenlerini belirleyecek tüm yönleri kabul edersiniz. Şimdi, sunucu yükleme için dosyaları alacak şekilde hazırlanmaya hazırdır.

Her bir Azure dosya paylaşımıyla eşitlenecek **Tüm** klasörleri oluşturun.
Daha önce belgelenen klasör yapısını izlemeniz önemlidir. Örneğin, birden çok, yerel SMB paylaşımını tek bir Azure dosya paylaşımında eşitlemeye karar verdiyseniz, bunları birimdeki ortak bir kök klasöre yerleştirmeniz gerekir. Bu hedef kök klasörü şimdi birimde oluşturun.

Sağladığınız Azure dosya paylaşımı sayısı, bu adımda oluşturduğunuz klasörlerin sayısıyla ve kök düzeyinde eşitlenecek birim sayısına uygun olmalıdır.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>7. Adım: Azure Dosya Eşitleme aracısını dağıtma

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>8. Adım: eşitleme yapılandırma

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Bulut katmanlaması 'nı etkinleştirdiğinizden emin olun!** Bu, yerel sunucunuzun, StorSimple Bulut depolamada verilerinizin toplam boyutunu depolamak için yeterli alana sahip olmaması durumunda gereklidir. Geçiş için geçici olarak %99 birim boş alana kadar katmanlama ilkenizi ayarlayın.

Eşitleme grubu oluşturma adımlarını ve eşleşen sunucu klasörünün, eşitleme için yapılandırılması gereken tüm Azure dosya paylaşımları/sunucu konumları için sunucu uç noktası olarak eklenmesi ' nı tekrarlayın.

### <a name="step-9-copy-your-files"></a>9. Adım: dosyalarınızı kopyalama

Temel geçiş yaklaşımı, StorSimple Sanal gerecinizden Windows sunucunuza yönelik bir RoboCopy ve Azure dosya paylaşımlarına Azure Dosya Eşitleme.

İlk yerel kopyayı Windows Server hedef klasörünüze çalıştırın:

* Sanal StorSimple gerecinizdeki ilk konumu belirler.
* Windows Server 'da zaten Azure Dosya Eşitleme yapılandırılmış olan eşleşen klasörü belirler.
* RoboCopy kullanarak kopyayı başlatma

Aşağıdaki RoboCopy komutu, StorSimple Azure depolamaınızdan dosyaları yerel StorSimple 'a geri alacak ve sonra Windows Server hedef klasörüne taşıyacaktır. Windows Server onu Azure dosya paylaşımıyla eşitler. Yerel Windows Server birimi tam aldığından, bulut katmanlaması, zaten zaten eşitlenmiş olan ve katman dosyalarını açacaktır. Bulut katmanlaması, StorSimple Sanal gerecinden kopyalamaya devam etmek için yeterli alan oluşturacak. Bulut katmanlaması, %99 birim boş alana ulaşmak için ne eşitlendiğini ve disk alanını boşaltmak için bir saatte bir kez kontrol eder.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Arka plan

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      RoboCopy 'nin çoklu iş parçacıklı çalıştırmasına izin verir. Varsayılan 8 ' dir, Max 128 ' dir.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      GÜNLÜK dosyasının durumunu UNICODE olarak çıkış (varolan günlüğün üzerine yaz).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /T
   :::column-end:::
   :::column span="1":::
      Konsol penceresine çıkış. Günlük dosyasına çıktılarla birlikte kullanılır.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      RoboCopy, bir yedekleme uygulamasının kullanacağı modda çalıştırır. RoboCopy 'nin geçerli kullanıcının izinleri olmayan dosyaları taşımasına izin verir.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MıR
   :::column-end:::
   :::column span="1":::
      Aynı hedefte/hedefte sırayla bu RoboCopy komutunu birkaç kez çalıştırmaya izin verir. Ne önce kopyalanmış olduğunu tanımlar ve onu atlar. Yalnızca değişiklikler, ekler ve "*silmeler*" işlenir. Bu, son çalıştırmasından bu yana yapılır. Komut daha önce çalıştırılmadıysa hiçbir şey atlanamaz. Bu, hala etkin olarak kullanılan ve değişen kaynak konumları için mükemmel bir seçenektir.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      dosya kopyasının doğruluğu (varsayılan:/COPY: DAT), kopya bayrakları: D = veri, A = öznitelikler, T = zaman damgaları, S = güvenlik = NTFS ACL 'Leri, O = sahip bilgileri, U = denetim bilgileri
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Tüm dosya bilgilerini kopyala (/COPY: DATSOU ile eşdeğer)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      dizinlerin kopyasına uygunluk (varsayılan:/DCOPY: DA), kopyalama bayrakları: D = veri, A = öznitelikler, T = zaman damgaları
   :::column-end:::
:::row-end:::

RoboCopy komutunu ilk kez çalıştırdığınızda, kullanıcılarınız ve uygulamalarınız StorSimple dosya ve klasörlerine erişmeye devam eder ve muhtemelen değiştirebilir. Bu, RoboCopy bir dizini işledi, bir sonrakine geçer ve ardından kaynak konumdaki (StorSimple) bir Kullanıcı, bu geçerli RoboCopy çalıştırmasında işlenmeyecek bir dosyayı ekler, değiştirir veya siler. Bu çok uygundur.

İlk çalıştırma, verilerin toplu olarak Windows sunucunuza geri taşınması ve Azure Dosya Eşitleme aracılığıyla buluta yedeklemenize bağlıdır. Bu, şu zamana bağlı olarak uzun sürebilir:

* indirme bant genişliğiniz
* StorSimple bulut hizmeti 'nin geri çekme hızı
* karşıya yükleme bant genişliği
* iki hizmet tarafından işlenmesi gereken öğe sayısı (dosya ve klasörler)

İlk çalıştırma tamamlandıktan sonra, komutu yeniden çalıştırın.

İkinci kez daha hızlı bitecektir çünkü yalnızca son çalıştırmasından bu yana gerçekleşen değişiklikleri taşıması yeterlidir. Bu değişiklikler, en yakın zamanda StorSimple ile yereldir. Bu süre daha fazla azaltılarak, buluttan geri çekme gereksinimi azaltılmıştır. Bu ikinci çalıştırma sırasında, yeni değişiklikler birikmeye devam edebilir.

İşlemin tamamlanma süresi, kabul edilebilir bir kesinti olması memnun olana kadar bu işlemi tekrarlayın.

Kapalı kalma süresini kabul ediyorsanız ve StorSimple konumunu çevrimdışına almak için hazır olduğunuzda, bunu şimdi yapın: Örneğin, bir kullanıcının klasöre erişememesi için SMB payını kaldırın veya içeriğin StorSimple üzerinde bu klasörde değiştirilmesini engelleyen herhangi bir uygun adımı uygulamanız gerekmez.

Bir son RoboCopy Round çalıştırın. Bu işlem, kaçırılmış olabilecek tüm değişiklikleri seçer.
Bu son adımın ne kadar süreceği, RoboCopy taramasının hızına bağlıdır. Önceki çalıştırmanın ne kadar sürdüğünü ölçerek süreyi tahmin edebilirsiniz (kapalı kalma süresine eşittir).

Windows Server klasöründe bir paylaşma oluşturun ve büyük olasılıkla DFS-N dağıtımınızı bunu işaret etmek üzere ayarlayın. StorSimple SMB paylaşımınızda aynı paylaşma düzeyi izinleri ayarladığınızdan emin olun.

Paylaşımı/paylaşım gruplarını ortak bir köke veya birime geçirmeyi tamamladınız. (Eşleştirdiklerinize bağlı olarak, aynı Azure dosya paylaşımında yer almak için gereken kararı vermiştir.)

Bu kopyaların birkaçını paralel olarak çalıştırmayı deneyebilirsiniz. Tek seferde bir Azure dosya paylaşımının kapsamını işlemeyi öneririz.

> [!WARNING]
> StorSimple 'tan tüm verileri Windows Server 'a taşıdınız ve geçiş işlemi tamamlandıktan sonra: Azure portal ***Tüm***  eşitleme gruplarına geri dönün ve bulut katmanlaması birimi boş alan yüzdesi değerini önbellek kullanımına daha uygun bir şekilde ayarlayın, %20 deyin. 

Bulut katmanlama birimi boş alan ilkesi, büyük olasılıkla birden çok sunucu uç noktası ile eşitlenmesi olan bir birim düzeyinde çalışır. Tek bir sunucu uç noktasında boş alanı ayarlamayı unutursanız, eşitleme en kısıtlayıcı kuralı uygulamaya devam eder ve %99 boş disk alanı tutmaya çalışır ve bu da, yerel önbelleğin bekleneceği gibi yapılmadığından bu işlemi gerçekleştirmeyecektir. Amacınız yalnızca nadiren erişilen, arşiv verileri içeren bir birimin ad alanına sahip olmadığı müddetçe.

## <a name="troubleshoot"></a>Sorun giderme

' De çalıştırabileceğiniz en olası sorun, RoboCopy komutunun Windows Server tarafında *"Volume Full"* ile başarısız olmasına neden olur. Bu durumda, yükleme hızlarınız büyük olasılıkla karşıya yükleme hızınıza göre daha iyidir. Bulut katmanlaması, eşitlenen yerel Windows Server diskinden içerik boşaltmak için saatte bir kez davranır.

Eşitleme ilerleme durumu ve bulut katmanlaması için disk alanının serbest olmasına izin verin. Bunu, Windows sunucunuzdaki dosya Gezgini ' nde gözlemleyebilirsiniz.

Windows Server 'da yeterli kullanılabilir kapasite varsa, komutun yeniden çalıştırılması sorunu çözmeyecektir. Bu durumla karşılaşdığınızda hiçbir şey kesilmez ve güvenle ilerlemebilirsiniz. Komutu yeniden çalıştırmanın bir nedeni tek sonucudur.

Ayrıca, diğer Azure Dosya Eşitleme sorunlarıyla de çalıştırabilirsiniz.
Bu durumda olacakları gibi, **bağlantı Azure dosya eşitleme sorun giderme kılavuzuna** göz atın.

## <a name="relevant-links"></a>İlgili bağlantılar

Geçiş içeriği:

* [StorSimple 8000 serisi geçiş kılavuzu](storage-files-migration-storsimple-8000.md)

Azure Dosya Eşitleme içerik:

* [AFS genel bakış](./storage-sync-files-planning.md)
* [AFS dağıtım kılavuzu](./storage-how-to-create-file-share.md)
* [AFS sorunlarını giderme](storage-sync-files-troubleshoot.md)