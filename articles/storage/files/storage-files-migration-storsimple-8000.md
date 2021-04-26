---
title: Azure Dosya Eşitleme 'e StorSimple 8000 serisi geçişi
description: StorSimple 8100 veya 8600 gerecini Azure Dosya Eşitleme 'e geçirmeyi öğrenin.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7bde8fe404e0839bf14500bff4fb92ce8cc4ea04
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717360"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 ve 8600 Azure Dosya Eşitleme 'e geçiş

StorSimple 8000 serisi, 8100 veya 8600 fiziksel, şirket içi gereçlerle ve bulut hizmeti bileşenleriyle temsil edilir. Bu gereçlerden birindeki verileri bir Azure Dosya Eşitleme ortamına geçirmek mümkündür. Azure Dosya Eşitleme, StorSimple gereçlerinin geçirilebilmesi için varsayılan ve stratejik, uzun süreli Azure hizmetidir.

StorSimple 8000 serisi, Aralık 2022 ' de [yaşam sonuna](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) ulaşacaktır. Geçişinizi mümkün olan en kısa sürede planlamaya başlamak önemlidir. Bu makalede, Azure Dosya Eşitleme başarılı bir geçiş için gereken arka plan bilgisi ve geçiş adımları sağlanmaktadır.

## <a name="phase-1-prepare-for-migration"></a>1. Aşama: geçişe hazırlanma

Bu bölüm, StorSimple birimlerinden Azure dosya paylaşımlarına geçişinizin başlangıcında gerçekleştirmeniz gereken adımları içerir.

### <a name="inventory"></a>Envanter

Geçişinizi planlamaya başladığınızda öncelikle geçirmeniz gereken tüm StorSimple gereçlerini ve birimlerini belirlemeniz gerekir. Bunu tamamladıktan sonra, sizin için en iyi geçiş yoluna karar verebilirsiniz.

* StorSimple Fiziksel gereçlerine (8000 serisi) bu geçiş kılavuzunu kullanın.
* Sanal gereçler, [StorSimple 1200 serisi, farklı bir geçiş kılavuzu kullanın](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Geçiş maliyeti Özeti

StorSimple Veri Yöneticisi kaynaktaki geçiş işleri aracılığıyla StorSimple birimlerinden Azure dosya paylaşımlarına geçiş yapmak ücretsizdir. Geçiş sırasında ve sonrasında diğer maliyetler de tahakkuk edebilir:

* **Ağ çıkış:** StorSimple dosyalarınız, belirli bir Azure bölgesindeki bir depolama hesabında etkin. Azure dosya paylaşımlarını aynı Azure bölgesinde bulunan bir depolama hesabına geçirirseniz, çıkış maliyeti olmaz. Bu geçişin bir parçası olarak, dosyalarınızı farklı bir bölgedeki depolama hesabına taşıyabilirsiniz. Bu durumda, çıkış maliyetleri sizin için geçerlidir.
* **Azure dosya paylaşma işlemleri:** Dosyalar bir Azure dosya paylaşımında (bir geçişin parçası olarak veya birinin dışında) kopyalandığında, dosya ve meta veriler yazıldığı için işlem maliyetleri uygulanır. En iyi uygulama olarak, geçiş sırasında işlem için iyileştirilmiş katmanda Azure dosya paylaşımınızı başlatın. Geçiş bittikten sonra istediğiniz katmana geçiş yapın. Aşağıdaki aşamalar bunu uygun noktada çağıracaktır.
* **Azure dosya paylaşma katmanını değiştirme:** Azure dosya paylaşma maliyeti işlemlerinin katmanını değiştirme. Çoğu durumda, önceki noktadan gelen önerileri izlemek daha uygun maliyetli olacaktır.
* **Depolama maliyeti:** Bu geçiş dosyaları bir Azure dosya paylaşımında kopyalamaya başladığında Azure dosya depolaması tüketilir ve faturalandırılır. Geçirilen yedeklemeler [Azure dosya paylaşımında anlık görüntüler](storage-snapshots-files.md)olur. Dosya paylaşımının anlık görüntüleri yalnızca içerdikleri farklar için depolama kapasitesini kullanır.
* **StorSimple:** StorSimple cihazlarının ve depolama hesaplarının sağlanması ihtimaline kadar depolama, yedeklemeler ve Gereçler için StorSimple maliyeti gerçekleşmeye devam edecektir.

### <a name="direct-share-access-vs-azure-file-sync"></a>Doğrudan paylaşma-erişim ve Azure Dosya Eşitleme karşılaştırması

Azure dosya paylaşımları, dosya hizmetleri dağıtımınızı yapılandırmak için yepyeni bir fırsat dünyasını açar. Azure dosya paylaşımında yalnızca, kullanıcılar yerel olarak çalışan tanıdık Kerberos kimlik doğrulaması ve var olan NTFS izinleri (dosya ve klasör ACL 'Leri) ile SMB protokolü üzerinden doğrudan erişim sağlamak üzere ayarlayabildiğiniz bir SMB paylaşımıdır. [Azure dosya paylaşımlarına kimlik tabanlı erişim](storage-files-active-directory-overview.md)hakkında daha fazla bilgi edinin.

Doğrudan erişim için bir alternatif [Azure dosya eşitleme](./storage-sync-files-planning.md). Azure Dosya Eşitleme, StorSimple 'ın şirket içinde sık kullanılan dosyaları önbelleğe alma yeteneğine yönelik doğrudan bir örneksel işlemdir.

Azure Dosya Eşitleme, iki ana bileşeni temel alan bir Microsoft bulut hizmetidir:

* Herhangi bir Windows Server 'da performans erişim önbelleği oluşturmaya yönelik dosya eşitleme ve bulut katmanlama.
* Azure 'da, SMB ve Dosya REST gibi birden çok protokol üzerinden erişilebilen yerel depolama olarak dosya paylaşımları.

Azure dosya paylaşımları, öznitelikler, izinler ve zaman damgaları gibi depolanmış dosyalardaki önemli dosya uygunluk yönlerini korurlar. Azure dosya paylaşımları ile, artık bulutta depolanan dosya ve klasörleri yorumlamak için bir uygulama veya hizmet gereksinimi yoktur. Bunlara Windows Dosya Gezgini gibi tanıdık protokoller ve istemciler üzerinden yerel olarak erişebilirsiniz. Azure dosya paylaşımları, genel amaçlı dosya sunucusu verilerini ve uygulama verilerini bulutta depolamanıza olanak tanır. Azure dosya paylaşımının yedeklenmesi yerleşik bir işlevdir ve Azure Backup tarafından daha da geliştirilebilir.

Bu makalede geçiş adımları ele alınmaktadır. Geçirmeden önce Azure Dosya Eşitleme hakkında daha fazla bilgi edinmek istiyorsanız, aşağıdaki makalelere bakın:

* [Azure Dosya Eşitleme genel bakış](./storage-sync-files-planning.md "Genel Bakış")
* [Azure Dosya Eşitleme dağıtım kılavuzu](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple hizmeti veri şifreleme anahtarı

StorSimple gerecinizi ilk kez ayarladığınızda, "hizmet veri şifreleme anahtarı" oluşturmuş ve anahtarı güvenli bir şekilde depolamanızı istedi. Bu anahtar, StorSimple gerecinin dosyalarınızı depoladığı ilişkili Azure depolama hesabındaki tüm verileri şifrelemek için kullanılır.

Başarılı bir geçiş için "hizmet verileri şifreleme anahtarı" gereklidir. Bu anahtarı, stokunuzdaki her bir uygulama için bir tane olmak üzere kayıtlarınızdaki bu anahtarı almak için iyi bir zamandır.

Kayıtlarınızda anahtarları bulamıyorsanız, Gereç içinden yeni bir anahtar oluşturabilirsiniz. Her gereç benzersiz bir şifreleme anahtarına sahiptir.

#### <a name="change-the-service-data-encryption-key"></a>Hizmet veri şifreleme anahtarını değiştirme

[!INCLUDE [storage-files-migration-generate-key](../../../includes/storage-files-migration-generate-key.md)]

> [!CAUTION]
> StorSimple gerecinize nasıl bağlanacağınıza karar verirken aşağıdakileri göz önünde bulundurun:
>
> * HTTPS oturumu üzerinden bağlanmak en güvenli ve önerilen seçenektir.
> * Doğrudan cihaz seri konsoluna bağlanmak güvenlidir, ancak ağ anahtarları üzerinden seri konsoluna bağlanmak değildir.
> * HTTP oturum bağlantıları bir seçenektir, ancak *şifrelenmez*. Kapalı, güvenilir bir ağda kullanılmadıkları takdirde bu önerilmez.

### <a name="storsimple-volume-backups"></a>StorSimple birim yedeklemeleri

StorSimple, birim düzeyinde değişiklik yedeklemeleri sunmaktadır. Azure dosya paylaşımları, paylaşım anlık görüntüleri olarak da adlandırılan bu becerisine sahiptir.
Geçiş işleriniz, canlı birimden verileri değil yalnızca yedeklemeleri taşıyabilir. Bu nedenle en son yedekleme her zaman bir geçişte taşınan yedeklemeler listesinde olmalıdır.

Geçişiniz sırasında eski yedeklemeleri taşımaya gerek olup olmadığına karar verin.
En iyi yöntem, bu listenin olabildiğince küçük tutulması, böylece geçiş işleriniz daha hızlı tamamlanır.

Geçirilmesi gereken kritik yedeklemeleri belirlemek için yedekleme ilkelerinizin bir denetim listesini oluşturun. Örneğin:
* En son yedekleme. (Note: en son yedekleme her zaman bu listenin bir parçası olmalıdır.)
* 12 ay boyunca bir aylık yedekleme.
* Üç yıl boyunca bir adet yedekleme. 

Daha sonra, geçiş işlerinizi oluştururken, listenizdeki gereksinimleri karşılamak için geçirilmesi gereken tam StorSimple birim yedeklemelerini belirlemek için bu listeyi kullanabilirsiniz.

> [!CAUTION]
> **50** 'den fazla StorSimple birimi yedeklemesi seçilmesi desteklenmez.
> Geçiş işleriniz yalnızca yedeklemeleri, canlı birimden hiçbir şekilde veri taşıyabilir. Bu nedenle, en son yedekleme canlı verilere en yakın olduğundan, her zaman bir geçişe taşınacak yedeklemeler listesinin bir parçası olmalıdır.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Mevcut StorSimple birimlerinizi Azure dosya paylaşımlarına eşleyin

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Depolama hesabı sayısı

Geçişiniz, her biri daha az sayıda Azure dosya paylaşımı tutan birden çok depolama hesabı dağıtımından faydalanır.

Dosya paylaşımlarınız yüksek düzeyde etkin ise (birçok kullanıcı veya uygulama tarafından kullanılır), iki Azure dosya paylaşımı depolama hesabınızın performans sınırına ulaşabilirler. Bu nedenle, en iyi uygulama, her biri kendi tek dosya paylaşımlarına ve genellikle depolama hesabı başına ikiden fazla veya üç paylaşımdan daha fazla depolama hesabına geçiş yapmak olacaktır.

En iyi yöntem, depolama hesaplarını her bir dosya paylaşımıyla dağıtmaktır. İçinde arşiv paylaşımlarınız varsa, birden fazla Azure dosya paylaşımını aynı depolama hesabında havuza alabilirsiniz.

Bu konular, [buluta erişimi doğrudan](#direct-share-access-vs-azure-file-sync) (bır Azure VM veya hizmeti aracılığıyla) Azure dosya eşitleme kıyasla daha fazla geçerlidir. Bu paylaşımlar üzerinde Azure Dosya Eşitleme özel olarak kullanmayı planlıyorsanız, birkaçını tek bir Azure depolama hesabına göre gruplamak iyidir. Daha sonra, bir uygulamayı bir dosya paylaşımıyla doğrudan erişebilen buluta taşımak ve kaydırmak, bu senaryonun daha yüksek ıOPS ve aktarım hızı olması yararlı olabilir. Ya da Azure 'da, daha yüksek ıOPS ve aktarım hızı sahibi olmanın avantajlarından faydalanabileceğiniz bir hizmeti kullanmaya başlayabilirsiniz.

Paylaşımlarınızın bir listesini yaptıysanız, her bir paylaşımı bulunacağı depolama hesabıyla eşleyin.

> [!IMPORTANT]
> Bir Azure bölgesine karar verin ve her depolama hesabının ve Azure Dosya Eşitleme kaynağın seçtiğiniz bölgeyle eşleştiğinden emin olun.
> Depolama hesapları için ağ ve güvenlik duvarı ayarlarını şimdi yapılandırmayın. Bu yapılandırmalarda bu yapılandırmaların yapılması, geçişe olanaksız hale getirir. Geçiş işlemi tamamlandıktan sonra bu Azure depolama ayarlarını yapılandırın.

### <a name="phase-1-summary"></a>Aşama 1 Özeti

1. aşama sonunda:

* StorSimple cihazlarınız ve birimleriniz için iyi bir genel bakış sunulmaktadır.
* Her StorSimple cihazı için "hizmet veri şifreleme anahtarınızı" aldığınız için Veri Yöneticisi hizmeti buluttaki StorSimple birimlerine erişmeye hazır.
* Hangi birimlerin ve yedeklemelerin (en son dışında) geçirilmesi gereken bir planınız vardır.
* Birimlerinizi uygun sayıda Azure dosya paylaşımı ve depolama hesabı ile nasıl eşleyeceğinizi öğrenirsiniz.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>2. Aşama: Azure depolama ve geçiş kaynaklarını dağıtma

Bu bölümde, Azure 'da gereken farklı kaynak türlerini dağıtmaya yönelik konular açıklanmaktadır. Bazıları, veri geçişinizi tutacaktır ve bazıları yalnızca geçiş için gereklidir. Dağıtım planınızı sonlandırılana kadar kaynak dağıtmaya başlamamaya başlayın. Dağıtıldıktan sonra Azure kaynaklarınızın belirli yönlerini değiştirmek zor, bazen imkansız olabilir.

### <a name="deploy-storage-accounts"></a>Depolama hesaplarını dağıtma

Muhtemelen birçok Azure depolama hesabı dağıtmanız gerekecektir. Her biri dağıtım planınıza göre daha az sayıda Azure dosya paylaşımını tutar ve bu makalenin önceki bölümünde tamamlanır. [Planlı depolama hesaplarınızı dağıtmak](../common/storage-account-create.md#create-a-storage-account)için Azure Portal gidin. Tüm yeni depolama hesapları için aşağıdaki temel ayarlara uygunluğunu göz önünde bulundurun.

> [!IMPORTANT]
> Depolama hesaplarınız için ağ ve güvenlik duvarı ayarlarını şimdi yapılandırmayın. Bu noktada bu yapılandırmaların yapılması, geçişe olanaksız hale getirir. Geçiş işlemi tamamlandıktan sonra bu Azure depolama ayarlarını yapılandırın.

#### <a name="subscription"></a>Abonelik

StorSimple dağıtımınız veya farklı bir dağıtım için kullandığınız aboneliği kullanabilirsiniz. Tek sınırlama, aboneliğinizin StorSimple aboneliğiyle aynı Azure Active Directory kiracısında olması gerekir. Geçişe başlamadan önce StorSimple aboneliğini uygun kiracıya taşımayı düşünün. Yalnızca tüm aboneliği taşıyabilirsiniz, bireysel StorSimple kaynakları farklı bir kiracıya veya aboneliğe taşınamaz.

#### <a name="resource-group"></a>Kaynak grubu

Kaynak grupları, kaynakların ve yönetici yönetim izinlerinin organizasyonu ile ilgili yardım vardır. [Azure 'da kaynak grupları](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)hakkında daha fazla bilgi edinin.

#### <a name="storage-account-name"></a>Depolama hesabı adı

Depolama hesabınızın adı bir URL 'nin parçası olacak ve belirli karakter kısıtlamalarına sahip olacaktır. Adlandırma kuralınızın içinde, depolama hesabı adlarının dünya genelinde benzersiz olması, yalnızca küçük harf ve sayılara izin olması gerektiğini, 3 ila 24 karakter arasında olmasını gerektirdiğini ve tireler veya alt çizgi gibi özel karakterlere izin vermeyi göz önünde bulundurun. Daha fazla bilgi için bkz. [Azure depolama kaynak adlandırma kuralları](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

#### <a name="location"></a>Konum

Depolama hesabının konumu veya Azure bölgesi çok önemlidir. Azure Dosya Eşitleme kullanıyorsanız, tüm depolama hesaplarınız depolama eşitleme hizmeti kaynağınız ile aynı bölgede olmalıdır. Seçtiğiniz Azure bölgesi yerel sunucularınız ve kullanıcılarınıza yakın veya orta olmalıdır. Kaynağınız dağıtıldıktan sonra bölgesini değiştiremezsiniz.

StorSimple verilerinizin (depolama hesabı) Şu anda bulunduğu farklı bir bölge seçebilirsiniz.

> [!IMPORTANT]
> Geçerli StorSimple depolama hesabı konumundan farklı bir bölge seçerseniz, geçiş sırasında [Çıkış ücretleri uygulanır](https://azure.microsoft.com/pricing/details/bandwidth) . Veri StorSimple bölgesinden bırakılır ve yeni depolama hesabı bölgenizi girer. Aynı Azure bölgesinin içinde kaldıysanız bant genişliği ücretleri uygulanmaz.

#### <a name="performance"></a>Performans

Azure dosya paylaşımları veya standart depolama için Premium Depolama (SSD) seçme seçeneğiniz vardır. Standart depolama, [bir dosya paylaşımının çeşitli katmanlarını](storage-how-to-create-file-share.md#change-the-tier-of-an-azure-file-share)içerir. Standart depolama, çoğu müşterinin StorSimple 'dan geçiş yapma konusunda doğru seçenektir.

Hala emin değil misiniz?

* [Premium Azure dosya paylaşımının performansına](understanding-billing.md#provisioned-model)ihtiyacınız varsa Premium Depolama ' yı seçin.
* Sık erişimli veri ve arşiv verilerini içeren genel amaçlı dosya sunucusu iş yükleri için standart depolama ' yı seçin. Ayrıca, buluttaki paylaşımdaki tek iş yükünün Azure Dosya Eşitleme olması durumunda standart depolamayı da seçin.

#### <a name="account-kind"></a>Hesap türü

* Standart depolama için *StorageV2 (genel amaçlı v2)* öğesini seçin.
* Premium dosya paylaşımları için *FileStorage*' ı seçin.

#### <a name="replication"></a>Çoğaltma

Kullanılabilir birkaç çoğaltma ayarı vardır. Farklı çoğaltma türleri hakkında daha fazla bilgi edinin.

Yalnızca aşağıdaki iki seçenekten birini seçin:

* *Yerel olarak yedekli depolama (LRS)*.
* Bölgesel olarak *yedekli depolama (ZRS)*, tüm Azure bölgelerinde kullanılamaz.

> [!NOTE]
> Yalnızca LRS ve ZRS artıklık türleri, büyük 100-TiB kapasiteli Azure dosya paylaşımları ile uyumludur.

Tüm çeşitlemelerdeki coğrafi olarak yedekli depolama (GRS) Şu anda desteklenmiyor. Yedeklilik türünü daha sonra değiştirebilir ve Azure 'a yönelik destek geldiğinde GRS 'ye geçebilirsiniz.

#### <a name="enable-100-tib-capacity-file-shares"></a>100-TiB-kapasite dosya paylaşımlarını etkinleştir

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Bir depolama hesabı oluşturmak için Azure portal Gelişmiş sekmesini gösteren bir görüntü.":::
    :::column-end:::
    :::column:::
        Azure portal yeni depolama hesabı sihirbazının **Gelişmiş** bölümü altında, bu depolama hesabında **büyük dosya paylaşımları** desteğini etkinleştirebilirsiniz. Bu seçenek sizin için kullanılabilir değilse, büyük olasılıkla yanlış artıklık türünü seçmiş olursunuz. Bu seçeneğin kullanılabilir olması için yalnızca LRS veya ZRS seçeneklerini seçtiğinizden emin olun.
    :::column-end:::
:::row-end:::

Büyük, 100-TIB kapasiteli dosya paylaşımlarının birçok avantajı vardır:

* Daha küçük 5-TiB kapasiteli dosya paylaşımlarına kıyasla performansınız büyük ölçüde artmıştır (örneğin, ıOPS 'nin 10 katı).
* Geçişiniz önemli ölçüde daha hızlı tamamlanır.
* Bir dosya paylaşımının, kendisine geçirilecek tüm verileri tutmak için yeterli kapasiteye sahip olduğundan emin olmanız gerekir. Bu işlem, fark yedeklemeleri için gereken depolama kapasitesi de dahildir.
* Gelecekteki büyüme ele alınmıştır.

### <a name="azure-file-shares"></a>Azure dosya paylaşımları

Depolama hesaplarınız oluşturulduktan sonra depolama hesabının **dosya paylaşımı** bölümüne gidin ve 1. Aşama ' dan geçiş planınıza uygun sayıda Azure dosya paylaşımı dağıtın. Azure 'daki yeni dosya paylaşımlarınız için aşağıdaki temel ayarlara uymaları göz önünde bulundurun.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Yeni dosya paylaşma Kullanıcı arabirimini gösteren Azure portal ekran görüntüsü.":::
    :::column-end:::
    :::column:::
        </br>**Ad**</br>Küçük harfler, rakamlar ve tireler desteklenir.</br></br>**Kotasının**</br>Buradaki kota, bir Windows Server örneğindeki SMB sabit kotasına benzer. En iyi yöntem, kotaya ulaşıldığında geçiş ve diğer hizmetleriniz başarısız olacağı için burada bir kota ayarlanmamalıdır.</br></br>**Katmanlar**</br>Yeni dosya paylaşımınız için **Iyileştirilmiş işlem** seçin. Geçiş sırasında birçok işlem gerçekleşmeyecektir. Daha sonra, katmanınızı daha sonra iş yükünüze en uygun katmana değiştirme maliyeti daha verimli.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager

Geçiş işlerinizi tutacak Azure kaynağına **StorSimple veri Yöneticisi** denir. **Yeni kaynak**' ı seçin ve arama yapın. Ardından **Oluştur**’u seçin.

Bu geçici kaynak düzenleme için kullanılır. Geçiş işlemi tamamlandıktan sonra bunu siz temin edersiniz. StorSimple depolama hesabınızla aynı abonelikte, kaynak grubunda ve bölgede dağıtılmalıdır.

### <a name="azure-file-sync"></a>Azure Dosya Eşitleme

Azure Dosya Eşitleme ile, en sık erişilen dosyaları şirket içi önbelleğe alma özelliği ekleyebilirsiniz. StorSimple 'ın önbelleğe alma yeteneklerine benzer şekilde, Azure Dosya Eşitleme bulut katmanlama özelliği, Windows Server örneğindeki kullanılabilir önbellek kapasitesi ve çok siteli eşitleme üzerinde geliştirilmiş denetimle birlikte yerel erişim gecikme süresi sağlar. Hedefiniz şirket içi bir önbellekleriniz varsa, yerel ağınızda, yeterli doğrudan bağlanmış depolama kapasitesine sahip bir Windows Server VM (fiziksel sunucular ve yük devretme kümeleri de desteklenir) hazırlayın.

> [!IMPORTANT]
> Azure Dosya Eşitleme henüz ayarlanmamış. Paylaşımınızın geçişi tamamlandıktan sonra Azure Dosya Eşitleme ayarlamanız en iyisidir. Dağıtım Azure Dosya Eşitleme, geçişin 4. aşamasından önce başlamamamalıdır.

### <a name="phase-2-summary"></a>2. aşama Özeti

2. aşama sonunda, depolama hesaplarınızı ve tüm Azure dosya paylaşımlarını bunlar arasında dağıtmış olacaksınız. Ayrıca bir StorSimple Veri Yöneticisi kaynağınız olacaktır. Geçiş işlerinizi yapılandırırken ikincisini 3. aşama olarak kullanacaksınız.

## <a name="phase-3-create-and-run-a-migration-job"></a>3. Aşama: geçiş işi oluşturma ve çalıştırma

Bu bölümde, bir geçiş işinin nasıl ayarlanacağı ve seçtiğiniz hedef Azure dosya paylaşımında kopyalanması gereken bir StorSimple birimindeki dizinlerin dikkatle nasıl eşlenileceği açıklanmaktadır. Başlamak için StorSimple Veri Yöneticisi gidin, menüdeki **iş tanımlarını** bulun ve **+ iş tanımı**' nı seçin. Doğru hedef depolama türü varsayılan: **Azure dosya paylaşımıdır**.

![StorSimple 8000 serisi geçiş işi türleri.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "İş tanımlarının bir ekran görüntüsü, iş türünü isteyen yeni bir Iş tanımları iletişim kutusuyla açılır Azure portal: dosya paylaşımında veya blob kapsayıcısına kopyalama.")

:::row:::
    :::column:::
        ![StorSimple 8000 serisi geçiş işi.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Geçiş işi için yeni iş oluşturma formunun ekran görüntüsü.")
    :::column-end:::
    :::column:::
        **İş tanımı adı**</br>Bu ad, taşıdığınız dosya kümesini göstermelidir. Azure dosya paylaşımınıza benzer bir ad vermek iyi bir uygulamadır. </br></br>**İşin çalıştığı konum**</br>Bir bölge seçerken, StorSimple depolama hesabınızla aynı bölgeyi seçmeniz gerekir, aksi takdirde, bu durumda bir bölge de buraya yakın olur. </br></br><h3>Kaynak</h3>**Kaynak aboneliği**</br>StorSimple Device Manager kaynağınızı depoladığınız aboneliği seçin. </br></br>**StorSimple kaynağı**</br>Gerecinizin birlikte kaydedildiği StorSimple Device Manager seçin. </br></br>**Hizmet verileri şifreleme anahtarı**</br>Kayıtlarınızda anahtarı bulamıyorsanız bu [makalenin önceki bölümüne](#storsimple-service-data-encryption-key) bakın. </br></br>**Cihaz**</br>Geçirmek istediğiniz birimi tutan StorSimple cihazınızı seçin. </br></br>**Birim**</br>Kaynak birimi seçin. Daha sonra, tüm birim veya alt dizinleri hedef Azure dosya paylaşımında geçirmek istediğinize karar verirsiniz.</br></br> **Birim yedeklemeleri**</br>Bu işin bir parçası olarak taşınacak belirli yedeklemeleri seçmek için *birim yedeklemeleri Seç* ' i seçebilirsiniz. [Bu makaledeki](#selecting-volume-backups-to-migrate) yakında, ayrılmış bir bölümde işlem ayrıntılı olarak ele alınmaktadır.</br></br><h3>Hedef</h3>Bu geçiş işinin hedefi olarak abonelik, depolama hesabı ve Azure dosya paylaşımından birini seçin.</br></br><h3>Dizin eşleme</h3>[Bu makaledeki adanmış bir bölüm](#directory-mapping), tüm ilgili ayrıntıları ele alır.
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>Geçirilecek birim yedeklemeleri seçme

Geçirilmesi gereken yedeklemeler seçmenin önemli yönleri vardır:

- Geçiş işleriniz, canlı bir birimden verileri değil yalnızca yedeklemeleri taşıyabilir. En son yedekleme, canlı verilere en yakın olan ve her zaman bir geçişe taşınan yedeklemeler listesinde olmalıdır.
- En son yedeğinizin güncel olduğundan emin olun. Bir geçiş işi oluşturmadan önce el ile tetiklenmesi ve başka bir birim yedeklemesini tamamlamak yararlı olabilir. Canlı paylaşıma yönelik küçük bir Delta geçişi, geçiş deneyiminizi iyileştirir. Bu Delta sıfır olabilir = StorSimple biriminde en yeni yedekleme gerçekleştirildikten sonra gerçekleşen daha fazla değişiklik yok-sonra 5. Aşama: Kullanıcı kesildi, büyük ölçüde basitleşmez ve SED olur.
- Yedeklemeler Azure dosya paylaşımında **en eskiden en eskiye** doğru oynatılıp yürütülmelidir. Daha eski bir yedekleme, bir geçiş işi çalıştıktan sonra Azure dosya paylaşımındaki yedeklemelerin listesini "sıralanamaz". Bu nedenle, bir iş oluşturmadan *önce* yedekleme listenizin tamamlandığından emin olmanız gerekir. 
- İş oluşturulduktan sonra, iş hiç çalıştırılmasa bile, bir işteki yedeklemelerin bu listesi değiştirilemez. 

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="Yeni iş oluşturma formunun, StorSimple yedeklemelerinin geçiş için seçildiği bölümü açıklayan ekran görüntüsü." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        Geçiş işiniz için StorSimple biriminizin yedeklerini seçmek için, iş oluşturma formunda *birim yedeklemeleri Seç* ' i seçin.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="Yedeklemeleri seçme için dikey pencerenin üst yarısındaki tüm kullanılabilir yedeklemeleri listeleyen bir resim. Seçilen bir yedekleme bu listede gri renkte olacak ve dikey pencerenin alt yarısında ikinci bir listeye eklenecektir. Ayrıca, yeniden de silinebilir." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        Yedekleme seçimi dikey penceresi açıldığında, iki listeye ayrılır. İlk listede, tüm kullanılabilir yedeklemeler görüntülenir. Belirli bir zaman aralığı için filtre uygulayarak sonuç kümesini genişletebilir ve daraltabilirsiniz. (sonraki bölüme bakın) </br></br>Seçilen bir yedekleme gri renkte görünür ve dikey pencerenin alt yarısında ikinci bir listeye eklenir. İkinci liste, geçiş için seçilen tüm yedeklemeleri görüntüler. Hatada seçili bir yedek de yeniden kaldırılabilir.
        > [!CAUTION]
        > Geçirmek istediğiniz **Tüm** yedeklemeleri seçmelisiniz. Daha sonra üzerinde eski yedeklemeler ekleyemezsiniz. İş oluşturulduktan sonra seçiminizi değiştirmek için işi değiştiremezsiniz.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="Yedekleme seçimi dikey penceresinin zaman aralığının seçimini gösteren ekran görüntüsü." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        Varsayılan olarak, liste en son yedi gün içinde StorSimple birim yedeklemelerini gösterecek şekilde filtrelenmiştir ve en son yedeklemenin seçimini kolaylaştırır. Daha önceki yedeklemeler için dikey pencerenin en üstündeki zaman aralığı filtresini kullanın. Mevcut bir filtreden seçim yapabilir veya yalnızca bu süre boyunca gerçekleştirilen yedeklemeleri filtrelemek için özel bir zaman aralığı ayarlayabilirsiniz.
    :::column-end:::
:::row-end:::

> [!CAUTION]
> 50 'den fazla StorSimple birimi yedeklemesi seçilmesi desteklenmez. Çok sayıda yedekleme işi başarısız olabilir.

### <a name="directory-mapping"></a>Dizin eşleme

Geçiş işiniz için Dizin eşleme isteğe bağlıdır. Bölümü boş bırakırsanız, StorSimple biriminiz kökündeki *Tüm* dosyalar ve klasörler hedef Azure dosya paylaşımınızın köküne taşınır. Çoğu durumda, bir birimin içeriğinin tamamını bir Azure dosya paylaşımında depolamak en iyi yaklaşım değildir. Bir birimin içeriğini Azure 'da birden çok dosya paylaşımı arasında bölmeniz genellikle daha iyidir. Zaten bir plan yapmadıysanız, bkz. [StorSimple biriminizi önce Azure dosya paylaşımlarına eşleme](#map-your-existing-storsimple-volumes-to-azure-file-shares) .

Geçiş planınızın bir parçası olarak, StorSimple birimindeki klasörlerin birden çok Azure dosya paylaşımına bölünmesi gerektiğini kararmış olabilirsiniz. Böyle bir durum söz konusu olduğunda, bu bölmeyi şu şekilde yapabilirsiniz:

1. Klasörlerin bir birimde geçirilmesi için birden çok iş tanımlama. Her biri aynı StorSimple birim kaynağına, ancak hedefle aynı Azure dosya paylaşımıyla aynı olacaktır.
1. StorSimple biriminden tam olarak hangi klasörlerin, iş oluşturma formunun **Dizin eşleme** bölümü kullanılarak ve belirli [eşleme semantiğini](#semantic-elements)izleyerek belirtilen dosya paylaşımında geçirilmesi gerektiğini belirtme.

> [!IMPORTANT]
> Bu formdaki yollar ve eşleme ifadeleri form gönderildiğinde doğrulanamaz. Eşlemeler yanlış belirtilirse, bir iş tamamen başarısız olabilir ya da istenmeyen bir sonuç üretebilir. Bu durumda, genellikle en iyi şekilde Azure dosya paylaşımının silinmesi, yeniden oluşturulması ve ardından paylaşıma yönelik yeni bir geçiş işinde eşleme deyimlerini düzeltilmesi gerekir. Sabit eşleme deyimleriyle yeni bir işi çalıştırmak, atlanan klasörleri çözebilir ve var olan paylaşıma getirebilir. Ancak, yalnızca yol yazım hataları nedeniyle atlanan klasörler bu şekilde çözülebilir.

#### <a name="semantic-elements"></a>Anlam öğeleri

Bir eşleme soldan sağa ifade edilir: [\Source Path] \> [\target Path].

|Anlam karakteri          | Anlamı  |
|:---------------------------|:---------|
| **\\**                     | Kök düzeyi göstergesi.       |
| **\>**                     | [Kaynak] ve [hedef eşleme] işleci.     |
|**\|** veya RETURN (yeni satır) | İki klasör eşleme yönergelerinin ayırıcısı. </br>Alternatif olarak, bir sonraki eşleme ifadesini kendi satırına almak için bu karakteri atlayabilir ve **ENTER** ' u seçebilirsiniz.        |

### <a name="examples"></a>Örnekler
Klasör *Kullanıcı verilerinin* içeriğini hedef dosya paylaşımının köküne taşımalıdır:
``` console
\User data > \
```
Tüm birim içeriğini hedef dosya paylaşımında yeni bir yola taşımalıdır:
``` console
\ > \Apps\HR tracker
```
Kaynak klasör içeriğini hedef dosya paylaşımında yeni bir yola taşımalıdır:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Birden çok kaynak konumu yeni bir dizin yapısına sıralar:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Anlam kuralları

* Her zaman kök düzeyine göre klasör yolları belirtin.
* Her klasör yolunu bir kök düzeyi göstergesi "" ile başlatın \\ .
* Sürücü harflerini eklemeyin.
* Birden çok yol belirtirken, kaynak veya hedef yollar çakışamaz:</br>
   Geçersiz kaynak yolu örtüşme örneği:</br>
    *\\folder\1 > \\ klasörü*</br>
    *\\klasör \\ 1 \\ 2 > \\ klasör2*</br>
   Geçersiz hedef yolu örtüşme örneği:</br>
   *\\klasör > \\*</br>
   *\\klasör2 > \\*</br>
* Mevcut olmayan kaynak klasörleri yok sayılacak.
* Hedefte mevcut olmayan klasör yapıları oluşturulacaktır.
* Windows gibi klasör adları büyük/küçük harfe duyarlıdır ancak durum korunur.

> [!NOTE]
> Bu ve StorSimple biriminizdeki *$Recycle. bin* *içerikleri, geçiş* işi tarafından kopyalanmayacak.

### <a name="run-a-migration-job"></a>Geçiş işi çalıştırma

Geçiş işleriniz, kaynak grubuna dağıttığınız Veri Yöneticisi kaynaktaki *iş tanımları* altında listelenir.
İş tanımları listesinden, çalıştırmak istediğiniz işi seçin.

Açılan iş dikey penceresinde, iş çalışmalarınızın alt listede görebilirsiniz. Başlangıçta bu liste boş olur. Dikey pencerenin üst kısmında, *çalıştırma işi* adlı bir komut vardır. Bu komut işi hemen çalıştırmayacak, **iş çalıştırma** dikey penceresini açar:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job.png" alt-text="Geçirilecek seçili yedeklemeleri görüntüleyen bir açılan menü denetimi ile birlikte iş çalıştırma dikey penceresini gösteren resim. En eski yedekleme vurgulanmıştır, önce seçilmesi gerekir." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job-expanded.png":::
    :::column-end:::
    :::column:::
        Bu sürümde, her iş birkaç kez çalıştırılmalıdır. </br></br>**Geçirmek istediğiniz yedeklemeler listenizden en eski yedekten başlamanız gerekir.** (görüntüde vurgulanan)</br></br>Her seferinde, her zaman giderek daha yeni bir yedeklemeye karşı yedekleme seçildiği sürece işi yeniden çalıştırırsınız.
        </br></br>
        > [!CAUTION]
        > Geçiş işini önce en eski yedeklemeden, sonra da her zaman aşamalı olarak daha yeni bir yedeklemeye sahip olacak şekilde çalıştırmanız zorunludur. Yedeklemelerinizin sırasını en eskiye doğru el ile en yeniye korumanız gerekir.
    :::column-end:::
:::row-end:::

### <a name="phase-3-summary"></a>3. aşama Özeti

3. aşama sonunda, StorSimple birimlerinden Azure dosya paylaşımlarından geçiş işlerinizin en az birini çalıştırırsınız. Aynı geçiş işini, en eskiden geçirilmesi gereken en yeniye ve en son yedeklemelere çalıştıracaksınız. Artık, paylaşıma yönelik Azure Dosya Eşitleme ayarlama (bir paylaşımın geçiş işleri tamamlandığında) veya bilgi çalışanlarınızın ve uygulamalarınızın paylaşılan erişimini Azure dosya paylaşımında yönlendiren şekilde gerçekleştirebilirsiniz.

## <a name="phase-4-access-your-azure-file-shares"></a>4. Aşama: Azure dosya paylaşımlarınızı erişme

Azure dosya paylaşımlarınızın erişimine yönelik iki ana strateji vardır:

* **Azure dosya eşitleme**: [Azure dosya eşitleme](#deploy-azure-file-sync) şirket içi Windows Server örneğine dağıtın. Azure Dosya Eşitleme, tıpkı StorSimple gibi yerel bir önbelleğin tüm avantajlarına sahiptir.
* **Doğrudan paylaşma-erişim**: [doğrudan paylaşma-erişim dağıtma](#deploy-direct-share-access). Belirli bir Azure dosya paylaşımının erişim senaryonuz yerel önbelleğe alma işleminin avantajına sahip değilse veya şirket içi Windows Server örneğini barındırabilmeniz gerekmiyorsa bu stratejiyi kullanın. Burada, kullanıcılarınız ve uygulamalarınız SMB protokolü üzerinden SMB paylaşımlarına erişmeye devam edecektir. Bu paylaşımlar artık şirket içi bir sunucuda değil, doğrudan bulutta yer alır.

Bu kılavuzun [1. aşaması](#phase-1-prepare-for-migration) içinde sizin için en uygun seçeneği zaten kararmış olmanız gerekir.

Bu bölümün geri kalanı dağıtım yönergelerine odaklanır.

### <a name="deploy-azure-file-sync"></a>Azure Dosya Eşitleme’yi dağıtma

Azure Dosya Eşitleme bir parçasını dağıtmanın zamanı.

1. Azure Dosya Eşitleme bulut kaynağı oluşturun.
1. Azure Dosya Eşitleme aracısını şirket içi sunucunuza dağıtın.
1. Sunucuyu bulut kaynağına kaydedin.

Henüz hiçbir eşitleme grubu oluşturmayın. Bir Azure dosya paylaşımıyla eşitlemenin kurulması, yalnızca bir Azure dosya paylaşımında geçiş işleriniz tamamlandıktan sonra gerçekleşmelidir. Geçişiniz tamamlanmadan önce Azure Dosya Eşitleme kullanmaya başladıysanız geçişinizin daha fazla zor hale gelir. bu yana, daha fazla bir kesme başlatma zamanı olduğunu kolayca söyleyemiyoruz.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Azure Dosya Eşitleme bulut kaynağını dağıtma

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Geçiş tamamlandıktan sonra verilerinizin bulunduğu Azure bölgesini değiştirmek istiyorsanız, depolama eşitleme hizmeti 'ni bu geçiş için hedef depolama hesaplarıyla aynı bölgede dağıtın.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Şirket içi Windows Server örneğini dağıtma

* Bir sanal makine veya fiziksel sunucu olarak Windows Server 2019 (minimum 2012R2) oluşturun. Bir Windows Server yük devretme kümesi de desteklenir. StorSimple 8100 veya 8600 ' den muaf olan sunucuyu yeniden kullanmayın.
* Doğrudan bağlı depolama alanı sağlayın veya ekleyin. Ağa bağlı depolama desteklenmez.

Yeni Windows Server örneğinize, StorSimple 8100 veya 8600 gerecinin önbelleğe alma için yerel olarak kullanılabilir olandan eşit veya daha büyük bir depolama miktarı vermesi en iyi uygulamadır. Windows Server örneğini StorSimple gerecini kullandığınız şekilde kullanırsınız. Gereç ile aynı miktarda depolama alanı varsa, aynı değilse önbelleğe alma deneyiminin benzer olması gerekir. ' De Windows Server örneğinizden depolama ekleyebilir veya kaldırabilirsiniz. Bu özellik, yerel birim boyutunuzu ve önbelleğe alma için kullanılabilir yerel depolama miktarını ölçeklendirmenizi sağlar.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Windows Server örneğini dosya eşitleme için hazırlama

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Windows Server örneğindeki Azure Dosya Eşitleme yapılandırma

Kayıtlı şirket içi Windows Server Örneğiniz, bu işlem için önceden hazırlanmalıdır ve internet 'e bağlanmalıdır.

> [!IMPORTANT]
> Devam etmeden önce, dosya ve klasörlerin Azure dosya paylaşımında StorSimple geçişinin tamamlanmış olması gerekir. Dosya paylaşımında başka değişiklik yapıldığından emin olun.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Bulut katmanlaması 'nı etkinleştirdiğinizden emin olun. Bulut katmanlama, yerel sunucunun bulutta depolankıyasla daha az depolama kapasitesine sahip olmasına izin veren Azure Dosya Eşitleme özelliğidir, ancak tam ad alanı kullanılabilir. Yerel olarak ilginç veriler de hızlı, yerel erişim performansı için yerel olarak önbelleğe alınır. Bu adımda bulut katmanlamayı açmak için başka bir neden, dosya içeriğini bu aşamada eşitlemek istemedik. Şu anda yalnızca ad alanı taşınıyor olmalıdır.

### <a name="deploy-direct-share-access"></a>Doğrudan paylaşma-erişim dağıtma

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Bu video, Azure dosya paylaşımlarını doğrudan bilgi çalışanları ve uygulamaları için beş basit adımda güvenli bir şekilde kullanıma sunma kılavuzudur.</br>
        Video, bazı konular için adanmış belgelere başvurur:

* [Kimliğe genel bakış](storage-files-active-directory-overview.md)
* [Etki alanına bir depolama hesabına ekleme](storage-files-identity-auth-active-directory-enable.md)
* [Azure dosya paylaşımları için ağa genel bakış](storage-files-networking-overview.md)
* [Ortak ve özel uç noktaları yapılandırma](storage-files-networking-endpoints.md)
* [S2S VPN yapılandırma](storage-files-configure-s2s-vpn.md)
* [Windows P2S VPN 'yi yapılandırma](storage-files-configure-p2s-vpn-windows.md)
* [Linux P2S VPN yapılandırma](storage-files-configure-p2s-vpn-linux.md)
* [DNS iletmeyi yapılandırma](storage-files-networking-dns.md)
* [DFS 'yi yapılandırma-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>4. aşama Özeti

Bu aşamada StorSimple Veri Yöneticisi birden çok geçiş işi oluşturdunuz ve çalıştırdık. Bu işler, dosyalarınızı ve klasörlerinizi Azure dosya paylaşımlarına geçirdiniz. Ayrıca, Azure Dosya Eşitleme veya ağ ve depolama hesaplarınızı doğrudan Share-Access için hazırladınız.

## <a name="phase-5-user-cut-over"></a>5. Aşama: Kullanıcı tarafından kesilen

Bu aşama, geçişinizi sarmalandırmak için gereklidir:

* Kapalı kalma süresini planlayın.
* 2. aşama üzerinde geçiş işleri çalışırken, StorSimple tarafında üretilen kullanıcılarınızın ve uygulamalarınızın yaptığı değişikliklerle birlikte yakalayın.
* Doğrudan paylaşım erişimi aracılığıyla Azure Dosya Eşitleme veya Azure dosya paylaşımları ile kullanıcılarınızı yeni Windows Server örneğine devretmek.

### <a name="plan-your-downtime"></a>Kapalı kalma süresini planlayın

Bu geçiş yaklaşımı, kullanıcılarınız ve uygulamalarınız için bazı kapalı kalma süresi gerektirir. Amaç, kapalı kalma süresini en düşük tutmaya sağlamaktır. Aşağıdaki konular yardımcı olabilir:

* Geçiş işlerinizi çalıştırırken StorSimple birimlerinizi kullanılabilir tutun.
* Veri geçişi işlerinizi bir paylaşım için çalıştırmayı bitirdiğinizde, StorSimple birimlerinden veya paylaşımlarından Kullanıcı erişiminin (en azından yazma erişimi) kaldırılması zaman vardır. Son bir RoboCopy, Azure dosya paylaşımınızı yakalar. Daha sonra kullanıcılarınızı daha fazla izleyebilirsiniz. RoboCopy çalıştırdığınız yer, Azure Dosya Eşitleme veya doğrudan paylaşma erişimi seçtiğinizden bağımsız olarak değişir. RoboCopy üzerindeki yaklaşan bölüm ilgili konuyu ele alır.
* RoboCopy yakala 'yı tamamladıktan sonra, Azure dosya paylaşımından doğrudan veya Azure Dosya Eşitleme ile bir Windows Server örneğindeki bir SMB paylaşımından yeni konumu kullanıcılarınıza sunmaya hazırsınız demektir. Genellikle bir DFS-N dağıtımı, hızla ve verimli bir şekilde kesme gerçekleştirmeye yardımcı olur. Mevcut paylaşımlarınızın adresini tutarlı olarak tutacaksınız ve geçirilmiş dosya ve klasörlerinizi içeren yeni bir konuma yeniden işaret eder.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Ad alanınız, sunucunuza tam olarak eşitlendiğinde belirlenir

Bir Azure dosya paylaşımında Azure Dosya Eşitleme kullandığınızda, herhangi bir yerel RoboCopy başlamadan *önce* tüm ad alanınızı sunucuya indirmeyi tamamladığımıza dikkat etmeniz önemlidir. Ad alanınızı indirmek için gereken süre, Azure dosya paylaşımınızda bulunan öğelerin sayısına bağlıdır. Ad alanınızı sunucuda tam olarak ulaşıp ulaşmadığını belirlemek için iki yöntem vardır.

#### <a name="azure-portal"></a>Azure portalı

Ad alanınız ne zaman tam olarak geldiğini görmek için Azure portal kullanabilirsiniz.

* Azure portal oturum açın ve eşitleme grubunuza gidin. Eşitleme grubunuzun ve sunucu uç noktasının eşitleme durumunu denetleyin.
* İlginç bir yön indirilir. Sunucu uç noktası yeni sağlandıysa, ad alanının hala eski olduğunu belirten **ilk eşitleme** görüntülenir.
Bu, her şeye yapılan, ancak **ilk eşitleme** sonrasında, ad alanınız sunucuda tamamen doldurulur. Artık yerel bir RoboCopy ile devam edebilirsiniz.

#### <a name="windows-server-event-viewer"></a>Windows Server Olay Görüntüleyicisi

Ayrıca, ad alanının tam olarak ulaştığını söylemek için Windows Server örneğinizdeki Olay Görüntüleyicisi de kullanabilirsiniz.

1. **Olay Görüntüleyicisi** açın ve **uygulamalar ve hizmetler**' e gidin.
1. ' A gidin ve **Microsoft\filesync\, T\telemetri**' i açın.
1. Tamamlanan bir eşitleme oturumuna karşılık gelen en son **olay 9102**' i arayın.
1. **Ayrıntılar**' ı seçin ve **Syncdirection** değerinin **indirileceği** bir olaya baktığınızdan emin olun.
1. Ad uzayının sunucuya indirmesi tamamlandığında, **senaryoya** sahip tek bir olay, **FullGhostedSync** değeri ve **HRESULT**  =  **0** olur.
1. Bu olayı kaçırırsanız, **syncdirection**   =  **indirmesi** ve   =  **"RegularSync"** senaryosuyla diğer 9102 olaylarına de bakabilirsiniz. Bu olaylardan birini bulmak için aynı zamanda, ad alanının, eşitleme için ilerlemedi ve eşitleme tamamlandı.

### <a name="a-final-robocopy"></a>Son RoboCopy

Bu noktada, şirket içi Windows Server Örneğiniz ve StorSimple 8100 ya da 8600 gereci arasında farklılıklar vardır.

1. Geçiş devam ederken kullanıcıların veya uygulamaların StorSimple tarafında üretilen değişikliklerle ilgili değişiklikler yapmanız gerekir.
1. Azure Dosya Eşitleme kullandığınız durumlar için: StorSimple gereci, şu anda yerel olarak depolanmış dosya içeriği olmayan bir ad alanı olan Windows Server örneğine karşı doldurulmuş bir önbelleğe sahiptir. Son RoboCopy, yerel olarak önbelleğe alınmış dosya içeriğini kullanıma sunarak yerel Azure Dosya Eşitleme önbelleğinizi hızlı bir şekilde başlatabilir ve Azure Dosya Eşitleme sunucusuna uyabilirler.
1. Bazı dosyalar, geçersiz karakterler nedeniyle geçiş işi 'nin arkasında bırakılmış olabilir. Varsa, bunları Azure Dosya Eşitleme özellikli Windows Server örneğine kopyalayın. Daha sonra, bunları eşitlebilmeleri için ayarlayabilirsiniz. Belirli bir paylaşıma yönelik Azure Dosya Eşitleme kullanmıyorsanız, StorSimple biriminde geçersiz karakterlerle dosyaları yeniden adlandırmayı daha iyi bir hale getiriyorsunuz. Ardından, RoboCopy doğrudan Azure dosya paylaşımında çalıştırın.

> [!WARNING]
> Windows Server 2019 ' de Robocopy Şu anda, hedef sunucudaki Azure Dosya Eşitleme katmanlı dosyaların kaynaktan yeniden kopyalanmasını ve Robocopy 'nin/MıR işlevi kullanılırken Azure 'a yeniden yüklenmesini sağlayan bir sorunla karşılaşır. Robocopy 'nin 2019 dışında bir Windows Server üzerinde kullanılması zorunludur. Tercih edilen bir seçenek Windows Server 2016 ' dir. Bu notta, sorun Windows Update aracılığıyla çözümlenmelidir.

> [!WARNING]
> Sunucuda, tam olarak indirilen bir Azure dosya paylaşımının ad alanına sahip olması için RoboCopy *başlatmamalıdır* . Daha fazla bilgi için bkz. [ad alanınız sunucunuza tam olarak indirilne zaman belirlenir](#determine-when-your-namespace-has-fully-synced-to-your-server).

 Yalnızca geçiş işinin en son çalıştırıldıktan sonra değiştirilen dosyaları ve bu işlerden önce taşınmayan dosyaları kopyalamak istersiniz. Bu sorunu, geçiş işlemi tamamlandıktan sonra sunucuda daha sonra ilerlemeyen bir şekilde çözebilirsiniz. Daha fazla bilgi için bkz. [Azure dosya eşitleme sorun giderme](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

RoboCopy 'nin çeşitli parametreleri vardır. Aşağıdaki örnek, tamamlanmış bir komutu ve bu parametreleri seçme nedenlerinin bir listesini gösterir.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Arka plan

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      RoboCopy 'nin çoklu iş parçacıklı çalıştırmasına izin verir. Varsayılan 8 ' dir ve maksimum değer 128 ' dir.
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
      /NP
   :::column-end:::
   :::column span="1":::
      Günlük okunabilir halde tutulması için ilerleme durumunu atlar.
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
      Robocopy 'nin yalnızca kaynak (StorSimple gereci) ve hedef (Windows Server dizini) arasında değişimleri 'yi kabul etmesine izin verir.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /IT
   :::column-end:::
   :::column span="1":::
      Belirli yansıtma senaryolarında uygunluk korunmasını sağlar.</br>Örnek: iki Robocopy arasında çalışan bir dosya, bir ACL değişikliği ve bir öznitelik güncelleştirmesi deneyiminden sonra da *gizli* olarak işaretlenir. /IT olmadan ACL değişikliği Robocopy tarafından kaçırılarak hedef konuma aktarılmaz.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Dosya kopyasının doğruluğu (varsayılan:/COPY: DAT), kopya bayrakları: D = veri, A = öznitelikler, T = zaman damgaları, S = güvenlik = NTFS ACL 'Leri, O = sahip bilgileri, U = denetim bilgileri.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Tüm dosya bilgilerini KOPYALAYıN (/COPY: DATSOU ile eşdeğerdir).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Dizinlerin kopyası için uygunluk (varsayılan:/DCOPY: DA), kopya bayrakları: D = veri, A = öznitelikler, T = zaman damgaları.
   :::column-end:::
:::row-end:::

RoboCopy komutunun kaynak ve hedef konumlarını yapılandırırken, bunların eşleştiğinden emin olmak için kaynak ve hedefin yapısını gözden geçirdiğinizden emin olun. Geçiş işinin Dizin eşleme özelliğini kullandıysanız, kök dizin yapınız StorSimple biriminizin yapısıyla farklı olabilir. Bu durumda, her bir alt dizin için bir tane olmak üzere birden çok RoboCopy işine ihtiyaç duyabilirsiniz. Komutun beklenildiği gibi gerçekleştirip gerçekleştirmediği konusunda emin değilseniz, aslında hiçbir değişiklik yapmadan komutun benzetimini gerçekleştirecek */l* parametresini kullanabilirsiniz.

Bu RoboCopy komutu/MıR kullanır, bu nedenle aynı (katmanlı dosyalar, örneğin) dosyalarını taşımaz. Ancak kaynak ve hedef yolunu yanlış alırsanız,/MıR, Windows Server Örneğiniz veya StorSimple kaynak yolunda bulunmayan Azure dosya paylaşımınızda dizin yapılarını da temizler. Geçiş devam ederken yapılan en son değişikliklerle geçirilen içeriğinizi güncelleştirme amacına ulaşmak için, RoboCopy işi için tam olarak eşleşmesi gerekir.

Dosyaların gerisinde bırakılmış olup olmadığını görmek için RoboCopy günlük dosyasına başvurun. Sorun varsa, bunları giderin ve RoboCopy komutunu yeniden çalıştırın. İlgilendiğiniz dosyalar veya klasörler için bekleyen sorunları giderebilmeniz için önce herhangi bir StorSimple kaynağını sağlamamaya özen gösterin.

Söz konusu Azure dosya paylaşımının önbelleğe almak için Azure Dosya Eşitleme kullanmıyorsanız, ancak doğrudan paylaşma erişimini tercih ettiyseniz:

1. [Azure dosya paylaşımınızı](storage-how-to-use-files-windows.md#mount-the-azure-file-share) yerel bir Windows makinesine bir ağ sürücüsü olarak bağlayın.
1. StorSimple ile bağlı Azure dosya paylaşımınız arasındaki RoboCopy gerçekleştirin. Dosyalar kopyalamadıysanız, geçersiz karakterleri kaldırmak için StorSimple tarafında adlarını düzelmeyin. Ardından RoboCopy yeniden deneyin. Daha önce listelenen RoboCopy komutu, StorSimple 'a gereksiz geri çekmelere neden olmadan birden çok kez çalıştırılabilir.

### <a name="user-cut-over"></a>Kullanıcı tarafından kesilen

Azure Dosya Eşitleme kullanıyorsanız, büyük olasılıkla Azure Dosya Eşitleme özellikli Windows Server örneğinde StorSimple birimlerinde sahip olduğunuz paylaşımlarla eşleşen SMB paylaşımları oluşturmanız gerekir. Bu adımı önyükleyebilir ve daha önce zaman kaybetmemeniz için daha önce yapabilirsiniz. Ancak, bu noktadan önce, hiçbir kimsenin Windows Server örneğinde değişikliklere neden olan erişimi olmadığından emin olmanız gerekir.

Bir DFS-N dağıtımınız varsa, DFN-Namespaces yeni sunucu klasörü konumlarına işaret edebilirsiniz. Bir DFS-N dağıtımınız yoksa ve 8100 veya 8600 gerecinizi bir Windows Server örneğiyle yerel olarak aldıysanız, bu sunucuyu etki alanı dışına alabilirsiniz. Daha sonra etki alanına yeni Azure Dosya Eşitleme özellikli Windows Server örneğinizi ekleyin. Bu işlem sırasında, kesme, kullanıcılarınız, Grup ilkeniz ve betikleriniz için aynı sunucu adı ve paylaşma adlarını eski sunucuyla aynı şekilde sağlayın.

[DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)hakkında daha fazla bilgi edinin.

## <a name="deprovision"></a>Sağlamayı kaldırma

Bir kaynak sağladığınızda, bu kaynak ve verilerin yapılandırmasına erişiminizi kaybedersiniz. Sağlamayı kaldırma işlemi geri alınamaz. Şunları onaylaana kadar devam etmeyin:

* Geçişiniz tamamlanmıştır.
* Sağlamayı kaldırmak üzere olduğunuz StorSimple dosyalarında, klasörlerinde veya birim yedeklemelerinde hiç bir bağımlılık yoktur.

Başlamadan önce, üretim sırasında yeni Azure Dosya Eşitleme dağıtımınızı bir süre içinde gözlemlemek en iyi uygulamadır. Bu süre, karşılaşabileceğiniz sorunları gidermeye yönelik fırsat sağlar. Azure Dosya Eşitleme dağıtımınızı en az birkaç güne kadar gözlemledikten sonra kaynakları sağlamayı şu sırada yapmaya başlayabilirsiniz:

1. StorSimple Veri Yöneticisi kaynağınızın Azure portal aracılığıyla sağlamasını kaldırma. Tüm DTS işleriniz onunla birlikte silinecektir. Kopyalama günlüklerini kolayca alamazsınız. Kayıtlarınız için önemliyse, sağlamadan önce bunları alın.
1. StorSimple fiziksel gereçlerinizin geçirildiğinden emin olun ve ardından bunların kaydını kaldırın. Geçirildiklerinden tamamen emin değilseniz, devam etmeyin. Bu kaynakları hala gerekli olmaya devam ediyorsa, verileri veya yapılandırmalarını kurtarmanız mümkün olmayacaktır.<br>İsteğe bağlı olarak, ilk olarak StorSimple birim kaynağını temin edebilir ve bu da gereç üzerindeki verileri temizler. Bu işlem birkaç gün sürebilir ve bu işlem, Gereç üzerindeki verileri hiçbir şekilde **içermez** . Bu sizin için önemliyse, disk sıfırlama işlemini kaynak sağlamayı ve ilkelerinize göre ayrı olarak işleyin.
1. StorSimple Device Manager daha fazla kayıtlı cihaz yoksa, bu Device Manager kaynağının kendisini kaldırmaya devam edebilirsiniz.
1. Azure 'da StorSimple depolama hesabını silmek artık zaman alabilir. Daha sonra, devam etmeden önce geçişinizi durdurup ve hiçbir şey ve hiçbir şeyin bu verilere bağlı olmadığından emin olun.
1. StorSimple fiziksel gerecini veri merkezinizden çıkarın.
1. StorSimple gerecine sahipseniz, bılgısayar geri dönüşüm için ücretsiz olursunuz. Cihazınız kiralandıysanız, lessa bildirin ve cihazı uygun şekilde geri döndürün.

Geçişiniz tamamlanmıştır.

> [!NOTE]
> Hala sorularınız var mı veya herhangi bir sorunla karşılaşıldı mi?</br>
> Size yardımcı olmaya çalışıyoruz AzureFilesMigration@microsoft.com .

## <a name="next-steps"></a>Sonraki adımlar

* Azure Dosya Eşitleme hakkında daha fazla bilgi edinin [: aka.MS/AFS](./storage-sync-files-planning.md).
* [Bulut katmanlama](storage-sync-cloud-tiering-overview.md) ilkelerinin esnekliğini anlayın.
* Anlık görüntüleri zamanlamak ve yedekleme bekletme zamanlamalarını tanımlamak için Azure dosya paylaşımlarınız üzerinde [Azure Backup etkinleştirin](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) .
* Bazı dosyaların kalıcı olarak eşitlenmediğinden Azure portal görürseniz, bu sorunları gidermeye yönelik adımlar için [sorun giderme kılavuzunu](storage-sync-files-troubleshoot.md) gözden geçirin.
