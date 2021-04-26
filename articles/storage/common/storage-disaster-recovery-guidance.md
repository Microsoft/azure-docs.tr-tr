---
title: Olağanüstü durum kurtarma ve depolama hesabı yükünü devretme
titleSuffix: Azure Storage
description: Azure depolama, coğrafi olarak yedekli depolama hesapları için hesap yük devretmesini destekler. Hesap yük devretmeyle, birincil uç nokta kullanılamaz hale gelirse depolama hesabınız için yük devretme işlemini başlatabilirsiniz.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 11d9b38d71d428a3c6c829b508318389338f5a15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800372"
---
# <a name="disaster-recovery-and-storage-account-failover"></a>Olağanüstü durum kurtarma ve depolama hesabı yükünü devretme

Microsoft, Azure hizmetlerinin her zaman kullanılabilir olduğundan emin olmaya çalışır. Ancak, planlanmamış hizmet kesintileri meydana gelebilir. Uygulamanız dayanıklılık gerektiriyorsa, Microsoft, verilerinizin ikinci bir bölgeye kopyalanması için coğrafi olarak yedekli depolamayı kullanmanızı önerir. Ayrıca, müşterilerin bölgesel hizmet kesintisi için bir olağanüstü durum kurtarma planı olması gerekir. Olağanüstü durum kurtarma planının önemli bir kısmı, birincil uç noktanın kullanılamaz hale geldiği olaydaki ikincil uç noktaya yük devretmek için hazırlanmalıdır.

Azure depolama, coğrafi olarak yedekli depolama hesapları için hesap yük devretmesini destekler. Hesap yük devretmeyle, birincil uç nokta kullanılamaz hale gelirse depolama hesabınız için yük devretme işlemini başlatabilirsiniz. Yük devretme, ikincil bitiş noktasını, depolama hesabınız için birincil uç nokta olacak şekilde güncelleştirir. Yük devretme işlemi tamamlandıktan sonra istemciler yeni birincil uç noktaya yazmaya başlayabilir.

Hesap yük devretme özelliği Azure Resource Manager dağıtımlarıyla genel amaçlı v1, genel amaçlı v2 ve Blob depolama hesabı türlerinde kullanılabilir. Hesap yük devretmesi tüm ortak bölgeler için desteklenir, ancak şu anda sogeign veya National bulutlar içinde kullanılamaz. Hiyerarşik bir ad alanı etkin olan depolama hesapları için hesap yük devretmesi desteklenmez.

Bu makalede, hesap yük devretmesi ile ilgili kavramlar ve işlemler açıklanmakta ve depolama hesabınızın en az müşteri etkisi miktarına göre kurtarmaya nasıl hazırlanacağı anlatılmaktadır. Azure portal veya PowerShell 'de hesap yük devretmesini başlatmayı öğrenmek için bkz. [Hesap yük devretmesi başlatma](storage-initiate-account-failover.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Doğru artıklık seçeneğini belirleyin

Azure depolama, dayanıklılık ve yüksek kullanılabilirlik sağlamak için depolama hesabınızın birden çok kopyasını tutar. Hesabınız için seçtiğiniz artıklık seçeneği, ihtiyacınız olan dayanıklılık derecesine bağlıdır. Bölgesel kesintilere karşı koruma için hesabınızı, ikincil bölgeden okuma erişimi seçeneği olmadan veya olmayan coğrafi olarak yedekli depolama için yapılandırın:  

**Coğrafi olarak yedekli depolama (GRS) veya coğrafi bölge yedekli depolama (GZRS)** , verilerinizi en az yüzlerce mil olan iki coğrafi bölgede zaman uyumsuz olarak kopyalar. Birincil bölge bir kesinti olursa, ikincil bölge verileriniz için yedek bir kaynak görevi görür. İkincil uç noktayı birincil uç noktaya dönüştürmek için bir yük devretme işlemi başlatabilirsiniz.

**Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) veya Okuma Erişimli Coğrafi bölge-yedekli depolama (ra-GZRS)** , ikincil uç noktaya okuma erişiminin ek avantajına sahip coğrafi olarak yedekli depolama sağlar. Birincil uç noktada bir kesinti oluşursa, ikinciye okuma erişimi için yapılandırılan ve yüksek kullanılabilirlik için tasarlanan uygulamalar ikincil uç noktadan okumaya devam edebilir. Microsoft, uygulamalarınız için maksimum kullanılabilirlik ve dayanıklılık için RA-GZRS önerir.

Azure depolama 'daki artıklık hakkında daha fazla bilgi için bkz. [Azure Storage yedekliği](storage-redundancy.md).

> [!WARNING]
> Coğrafi olarak yedekli depolama, veri kaybı riskini taşır. Veriler ikincil bölgeye zaman uyumsuz olarak kopyalanır, yani birincil bölgeye yazılan veriler ikincil bölgeye yazıldığında bir gecikme olur. Kesinti durumunda, henüz ikincil uç noktaya kopyalanmamış birincil uç noktaya yazma işlemleri kaybedilir.

## <a name="design-for-high-availability"></a>Yüksek kullanılabilirliğe yönelik tasarım

Uygulamanızı, başlangıçtan itibaren yüksek kullanılabilirlik için tasarlamak önemlidir. Uygulamanızı tasarlama ve olağanüstü durum kurtarmayı planlama konusunda rehberlik için bu Azure kaynaklarına başvurun:

- [Azure için dayanıklı uygulamalar tasarlama](/azure/architecture/framework/resiliency/app-design): Azure 'da yüksek oranda kullanılabilir uygulamalar oluşturmaya yönelik temel kavramlara genel bakış.
- [Dayanıklılık denetim listesi](/azure/architecture/checklist/resiliency-per-service): uygulamanızın yüksek kullanılabilirlik için en iyi tasarım uygulamalarını uyguladığını doğrulamak için bir denetim listesi.
- [Yüksek oranda kullanılabilir uygulamalar tasarlamak için coğrafi yedeklilik kullanın](geo-redundant-design.md): coğrafi olarak yedekli depolama alanının avantajlarından yararlanmak için uygulama oluşturmaya yönelik tasarım kılavuzu.
- [Öğretici: BLOB depolama ile yüksek oranda kullanılabilir bir uygulama oluşturma](../blobs/storage-create-geo-redundant-storage.md): uç noktalar arasında otomatik olarak bir şekilde geçiş yapan ve kurtarmaların benzetilyilmiş olduğu yüksek oranda kullanılabilir bir uygulamanın nasıl oluşturulacağını gösteren bir öğretici. 

Ayrıca, Azure depolama verileriniz için yüksek kullanılabilirlik sağlamak üzere bu en iyi yöntemleri göz önünde bulundurun:

- **Diskler:** Azure sanal makineleriniz tarafından kullanılan VM disklerini yedeklemek için [Azure Backup](https://azure.microsoft.com/services/backup/) kullanın. Ayrıca, bölgesel bir olağanüstü durum durumunda sanal makinelerinizi korumak için [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) kullanmayı göz önünde bulundurun.
- **Blok Blobları:** Nesne düzeyinde silme ve üzerine yazma işlemlerini korumak için [geçici silme](../blobs/soft-delete-blob-overview.md) özelliğini açın veya [azcopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/)veya [Azure veri taşıma kitaplığı](storage-use-data-movement-library.md)'nı kullanarak blok Blobları farklı bir bölgedeki başka bir depolama hesabına kopyalayın.
- **Dosyalar:** Dosya paylaşımlarınızı yedeklemek için [Azure Backup](../../backup/azure-file-share-backup-overview.md) kullanın. Yanlışlıkla dosya paylaşımının silinmesine karşı koruma sağlamak için de [geçici silme](../files/storage-files-prevent-file-share-deletion.md) özelliğini etkinleştirin. GRS kullanılabilir olmadığında coğrafi artıklık için [AzCopy](./storage-use-azcopy-v10.md) veya [Azure PowerShell](/powershell/module/az.storage/) kullanarak dosyalarınızı farklı bir bölgedeki başka bir depolama hesabına kopyalayın.
- **Tablolar:** farklı bir bölgedeki başka bir depolama hesabına tablo verilerini dışarı aktarmak Için [AzCopy](./storage-use-azcopy-v10.md) kullanın.

## <a name="track-outages"></a>Kesintileri izleme

Müşteriler, Azure depolama ve diğer Azure hizmetlerinin sistem durumunu ve durumunu izlemek için [Azure hizmet durumu panosuna](https://azure.microsoft.com/status/) abone olabilir.

Microsoft ayrıca, yazma hatalarıyla ilgili hazırlanabilmesi için uygulamanızı tasarlamanızı önerir. Uygulamanız, yazma başarısızlıklarını birincil bölgedeki bir kesinti olasılığa karşı uyaran şekilde kullanıma sunmalıdır.

## <a name="understand-the-account-failover-process"></a>Hesap yük devretme sürecini anlayın

Müşteri tarafından yönetilen hesap yük devretmesi, birincil bölge herhangi bir nedenden dolayı kullanılamaz hale gelirse tüm depolama hesabınızı ikincil bölgeye devrederlemenize olanak sağlar. İkincil bölgeye yük devretmeyi zorlarsanız, istemciler yük devretme tamamlandıktan sonra ikincil uç noktaya veri yazmaya başlayabilir. Yük devretme genellikle yaklaşık bir saat sürer.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

### <a name="how-an-account-failover-works"></a>Hesap yük devretmesi nasıl çalışır?

Normal koşullarda, istemci birincil bölgedeki bir Azure depolama hesabına veri yazar ve bu veriler ikincil bölgeye zaman uyumsuz olarak kopyalanır. Aşağıdaki görüntüde birincil bölge kullanılabilir olduğunda senaryo gösterilmektedir:

![İstemciler, verileri birincil bölgedeki depolama hesabına yazar](media/storage-disaster-recovery-guidance/primary-available.png)

Birincil uç nokta herhangi bir nedenle kullanılamaz duruma gelirse, istemci artık depolama hesabına yazamayacak. Aşağıdaki görüntüde, birincil olarak kullanılamayan senaryo gösterilir, ancak henüz kurtarma gerçekleşmemiştir:

![Birincil kullanılamıyor, bu nedenle istemciler veri yazamaz](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Müşteri, ikincil uç noktaya yük devretme hesabı başlatır. Yük devretme işlemi, aşağıdaki görüntüde gösterildiği gibi, ikincil bitiş noktasının depolama hesabınız için yeni birincil uç nokta haline gelmesi için Azure depolama tarafından verilen DNS girişini güncelleştirir:

![Müşteri, ikincil uç noktaya hesap yük devretmesini başlatır](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

DNS girdisi güncelleştirildikten ve istekler yeni birincil uç noktaya yönlendirildikten sonra, coğrafi olarak yedekli hesaplar için yazma erişimi geri yüklenir. Blob 'lar, tablolar, kuyruklar ve dosyalar için mevcut depolama hizmeti uç noktaları, yük devretmeden sonra aynı kalır.

> [!IMPORTANT]
> Yük devretme işlemi tamamlandıktan sonra, depolama hesabı yeni birincil uç noktada yerel olarak yedekli olacak şekilde yapılandırılır. Yeni ikincil çoğaltmayı yeniden başlatmak için, hesabı coğrafi artıklık için yeniden yapılandırın.
>
> Bir LRS hesabını coğrafi artıklık kullanacak şekilde dönüştürmenin bir maliyet doğurur olduğunu unutmayın. Bu maliyet, yük devretmeden sonra yeni birincil bölgedeki depolama hesabını güncelleştirmek için geçerlidir.  

### <a name="anticipate-data-loss"></a>Veri kaybını tahmin edin

> [!CAUTION]
> Hesap yük devretmesi genellikle bazı veri kaybını içerir. Hesap yük devretmesini başlatma etkilerine ilişkin etkileri anlamak önemlidir.  

Veriler birincil bölgeden ikincil bölgeye zaman uyumsuz olarak yazıldığı için, birincil bölgeye yazma işlemi ikinci bölgeye kopyalanmadan önce her zaman bir gecikme olur. Birincil bölge kullanılamaz duruma gelirse, en son yazma işlemleri henüz ikincil bölgeye kopyalanmamış olabilir.

Bir yük devretmeyi zorlarsanız, ikincil bölge yeni birincil bölge olur ve depolama hesabı yerel olarak yedekli olacak şekilde yapılandırıldığında birincil bölgedeki tüm veriler kaybedilir. Yük devretme gerçekleştiğinde ikinciye kopyalanmış olan tüm veriler korunur. Ancak, birincil diske yazılan tüm veriler, ikinciye kopyalanmamış olarak da kalıcı olarak kaybedilir.

**Son eşitleme zamanı** özelliği, birincil bölgedeki verilerin ikincil bölgeye yazıldığı en son zamanı gösterir. Son eşitleme zamanından önce yazılan tüm veriler ikincil üzerinde kullanılabilir, ancak son eşitleme zamanından sonra yazılan veriler ikinciye yazılmayabilir ve kaybolabilir. Hesap yük devretmesini başlatarak, tabi olabilecek veri kaybı miktarını tahmin etmek için bir kesinti durumunda bu özelliği kullanın.

En iyi uygulama olarak, beklenen veri kaybını değerlendirmek için son eşitleme süresini kullanabilmeniz için uygulamanızı tasarlayın. Örneğin, tüm yazma işlemlerini günlüğe koyduysanız, hangi yazma işlemlerinin ikinciye eşitlenmediğini öğrenmek için son yazma işlemlerinizin saatini son eşitleme zamanına göre karşılaştırabilirsiniz.

**Son eşitleme zamanı** özelliğini denetleme hakkında daha fazla bilgi için bkz. [bir depolama hesabı Için Son eşitleme zamanı özelliğini denetleme](last-sync-time-get.md).

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Özgün birincili geri yüklerken dikkatli olun

Birincil sunucudan ikincil bölgeye yük devretme yaptıktan sonra, depolama hesabınız yeni birincil bölgede yerel olarak yedekli olacak şekilde yapılandırılır. Daha sonra hesabı coğrafi artıklık için yeniden yapılandırabilirsiniz. Hesap, yük devretmeden sonra coğrafi yedeklilik için yapılandırıldığında yeni birincil bölge, ilk yük devretmeden önce birincil olan yeni ikincil bölgeye doğrudan veri kopyalamaya başlar. Ancak, birincil içindeki mevcut verilerin yeni ikincil öğesine tamamen kopyalanabilmesi için bu işlem biraz zaman alabilir.

Depolama hesabı coğrafi yedeklilik için yeniden yapılandırıldıktan sonra, yeni birincili yeni ikincil sunucudan başka bir yük devretme başlatmak mümkündür. Bu durumda, ilk birincil bölge yük devretmeden önce birincil bölge yeniden olur ve yerel olarak yedekli olacak şekilde yapılandırılır. Yük devretme sonrası birincil bölgesindeki (orijinal ikincil) tüm veriler kaybolur. Depolama hesabındaki verilerin büyük bir bölümünü yeniden denemeden önce yeni ikincil sürümüne kopyalanmamışsa, büyük bir veri kaybını etkilemeyebilir.

Önemli veri kaybını önlemek için, geri yüklemeden önce **son eşitleme zamanı** özelliğinin değerini denetleyin. Beklenen veri kaybını değerlendirmek için son eşitleme zamanını, verilerin yeni birincili son kez yazıldığı zamana göre karşılaştırın. 

## <a name="initiate-an-account-failover"></a>Hesap yük devretmesi başlatma

Azure portal, PowerShell, Azure CLı veya Azure depolama kaynak sağlayıcısı API 'sinden bir hesap yük devretmesi başlatabilirsiniz. Yük devretme başlatma hakkında daha fazla bilgi için bkz. [Hesap yük devretmesi başlatma](storage-initiate-account-failover.md).

## <a name="additional-considerations"></a>Diğer konular

Bir yük devretmeye zorlarsanız uygulamalarınızın ve hizmetlerinizin nasıl etkilenebileceğini anlamak için bu bölümde açıklanan ek konuları gözden geçirin.

### <a name="storage-account-containing-archived-blobs"></a>Arşivlenmiş blob 'ları içeren depolama hesabı

Arşivlenmiş blob 'ları içeren depolama hesapları, hesap yük devretmesini destekler. Yük devretme işlemi tamamlandıktan sonra, hesabın coğrafi artıklık için yapılandırılabilmesi için tüm arşivlenmiş Blobların çevrimiçi bir katmana yeniden doldurulması gerekir.

### <a name="storage-resource-provider"></a>Depolama kaynak sağlayıcısı

Yük devretme tamamlandıktan sonra, istemciler yeni birincil bölgedeki Azure depolama verilerini okuyup yazabilir. Ancak, Azure depolama kaynak sağlayıcısı yük devri yapmaz, bu nedenle kaynak yönetimi işlemleri birincil bölgede hala gerçekleşmelidir. Birincil bölge kullanılamıyorsa, depolama hesabında yönetim işlemleri gerçekleştiremezsiniz.

Azure depolama kaynak sağlayıcısı yük devretmediğinden, [konum](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) özelliği, yük devretme tamamlandıktan sonra orijinal birincil konumu döndürür.

### <a name="azure-virtual-machines"></a>Azure sanal makineleri

Azure sanal makineleri (VM 'Ler), hesap yük devretmesi kapsamında yük devreder. Birincil bölge kullanılamaz duruma gelirse ve ikincil bölgeye yük devretmek, yük devretmeden sonra VM 'Leri yeniden oluşturmanız gerekir. Ayrıca, hesap yük devretmesi ile ilişkili potansiyel bir veri kaybı vardır. Microsoft, Azure 'daki sanal makinelere özgü aşağıdaki [yüksek kullanılabilirlik](../../virtual-machines/availability.md) ve [olağanüstü durum kurtarma](../../virtual-machines/backup-recovery.md) kılavuzunu önerir.

### <a name="azure-unmanaged-disks"></a>Azure yönetilmeyen diskler

En iyi uygulama olarak, Microsoft yönetilmeyen disklerin yönetilen disklere dönüştürülmesini önerir. Ancak, Azure VM 'lerine bağlı yönetilmeyen diskler içeren bir hesabın yük devretmesinin gerekli olması gerekiyorsa, yük devretmeyi başlatmadan önce sanal makineyi kapatmanız gerekir.

Yönetilmeyen diskler, Azure depolama 'da sayfa Blobları olarak depolanır. Azure 'da bir VM çalışırken, VM 'ye bağlı tüm yönetilmeyen diskler kiralanır. Bir blob üzerinde kira olduğunda hesap yük devretmesi devam edemez. Yük devretmeyi gerçekleştirmek için şu adımları izleyin:

1. Başlamadan önce, yönetilmeyen disklerin adlarını, mantıksal birim numaralarını (LUN) ve bunların eklendiği VM 'yi aklınızda bulabilirsiniz. Bunun yapılması, yük devretmeden sonra disklerin yeniden iliştirmesinin daha kolay olmasını sağlar.
2. VM 'yi kapatın.
3. VM 'yi silin, ancak yönetilmeyen diskler için VHD dosyalarını koruyun. VM 'yi sildiğiniz saati göz önünde bulabilirsiniz.
4. **Son eşitleme saatinin** güncelleştirilmesini bekleyin ve VM 'yi sildiğiniz zamandan daha geç. Bu adım önemlidir, çünkü ikincil uç nokta yük devretme gerçekleştiğinde VHD dosyalarıyla tamamen güncelleştirilmemişse, sanal makine yeni birincil bölgede düzgün çalışmayabilir.
5. Hesap yük devretmesini başlatın.
6. Hesap yük devretmesi tamamlanana kadar bekleyin ve ikincil bölge yeni birincil bölge haline gelir.
7. Yeni birincil bölgede bir VM oluşturun ve VHD 'leri yeniden ekleyin.
8. Yeni VM 'yi başlatın.

VM kapatılırken geçici bir diskte depolanan tüm verilerin kaybedildiğini aklınızda bulundurun.

## <a name="unsupported-features-and-services"></a>Desteklenmeyen özellikler ve hizmetler

Hesap yük devretmesi için aşağıdaki özellikler ve hizmetler desteklenmez:

- Azure Dosya Eşitleme, depolama hesabı yük devretmesini desteklemez. Azure Dosya Eşitleme'de bulut uç noktaları olarak kullanılan Azure dosya paylaşımlarının bulunduğu depolama hesapları yük devretmemelidir. Bunun yapılması eşitlemenin çalışmayı durdurmasına neden olur ve yeni katmanlanmış dosyalar söz konusu olduğunda beklenmedik veri kaybına da yol açabilir.
- ADLS 2. depolama hesapları (hiyerarşik ad alanı etkin olan hesaplar) Şu anda desteklenmiyor.
- Premium blok bloblarını içeren bir depolama hesabı yük devredilemez. Premium blok bloblarını destekleyen depolama hesapları Şu anda coğrafi artıklığı desteklemez.
- [Solucan ve kullanılabilirlik ilkesi](../blobs/storage-blob-immutable-storage.md) etkinleştirilmiş kapsayıcıları içeren bir depolama hesabı yük devredilemez. Kilitlemeli/kilitlenmiş zamana dayalı saklama veya yasal saklama ilkeleri, uyumluluk sağlamak için yük devretmeyi önler.

## <a name="copying-data-as-an-alternative-to-failover"></a>Yük devretme alternatifi olarak verileri kopyalama

Depolama Hesabınız ikinciye okuma erişimi için yapılandırılmışsa, uygulamanızı ikincil uç noktadan okumak üzere tasarlayabilirsiniz. Birincil bölgedeki bir kesinti olması durumunda yük devredememeyi tercih ediyorsanız, İkincil bölgedeki depolama hesabınızdan verileri etkilenmeyen bir bölgedeki başka bir depolama hesabına kopyalamak için [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/)veya [Azure veri taşıma kitaplığı](../common/storage-use-data-movement-library.md) gibi araçları kullanabilirsiniz. Daha sonra hem okuma hem de yazma kullanılabilirliği için uygulamalarınızı bu depolama hesabına işaret edebilirsiniz.

> [!CAUTION]
> Hesap yük devretmesi, veri geçiş stratejinizin bir parçası olarak kullanılmamalıdır.

## <a name="microsoft-managed-failover"></a>Microsoft tarafından yönetilen yük devretme

Önemli bir olağanüstü durum nedeniyle bölgenin kaybolması durumunda Microsoft, bölgesel bir yük devretme işlemi başlatabilir. Bu durumda, sizin bölüminizdeki hiçbir işlem yapmanız gerekmez. Microsoft tarafından yönetilen yük devretme tamamlanana kadar, depolama hesabınıza yazma erişiminiz olmayacaktır. Depolama Hesabınız RA-GRS veya RA-GZRS için yapılandırılmışsa, uygulamalarınız ikincil bölgeden okunabilir.

## <a name="see-also"></a>Ayrıca bkz.

- [Yüksek oranda kullanılabilir uygulamalar tasarlamak için coğrafi artıklığı kullanın](geo-redundant-design.md)
- [Hesap yük devretmesi başlatma](storage-initiate-account-failover.md)
- [Bir depolama hesabı için son eşitleme zamanı özelliğini denetleyin](last-sync-time-get.md)
- [Öğretici: BLOB depolama ile yüksek oranda kullanılabilir bir uygulama oluşturma](../blobs/storage-create-geo-redundant-storage.md)