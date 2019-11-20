---
title: Olağanüstü durum kurtarma ve depolama hesabı yük devretme (Önizleme)-Azure depolama
description: Azure depolama, coğrafi olarak yedekli depolama hesapları için hesap yük devretmesini (Önizleme) destekler. Hesap yük devretmeyle, birincil uç nokta kullanılamaz hale gelirse depolama hesabınız için yük devretme işlemini başlatabilirsiniz.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b265ff8831275a9f4b84f7dac28b82ae75630f8b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889780"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Azure Storage 'da olağanüstü durum kurtarma ve depolama hesabı yük devretme (Önizleme)

Microsoft, Azure hizmetlerinin her zaman kullanılabilir olduğundan emin olmaya çalışır. Ancak, planlanmamış hizmet kesintileri meydana gelebilir. Uygulamanız dayanıklılık gerektiriyorsa, Microsoft, verilerinizin ikinci bir bölgede çoğaltılması için coğrafi olarak yedekli depolama kullanımını önerir. Ayrıca, müşterilerin bölgesel hizmet kesintisi için bir olağanüstü durum kurtarma planı olması gerekir. Olağanüstü durum kurtarma planının önemli bir kısmı, birincil uç noktanın kullanılamaz hale geldiği olaydaki ikincil uç noktaya yük devretmek için hazırlanmalıdır. 

Azure depolama, coğrafi olarak yedekli depolama hesapları için hesap yük devretmesini (Önizleme) destekler. Hesap yük devretmeyle, birincil uç nokta kullanılamaz hale gelirse depolama hesabınız için yük devretme işlemini başlatabilirsiniz. Yük devretme, ikincil bitiş noktasını, depolama hesabınız için birincil uç nokta olacak şekilde güncelleştirir. Yük devretme işlemi tamamlandıktan sonra istemciler yeni birincil uç noktaya yazmaya başlayabilir.

Bu makalede, hesap yük devretmesi ile ilgili kavramlar ve işlemler açıklanmakta ve depolama hesabınızın en az müşteri etkisi miktarına göre kurtarmaya nasıl hazırlanacağı anlatılmaktadır. Azure portal veya PowerShell 'de hesap yük devretmesini başlatmayı öğrenmek için bkz. [Hesap yük devretmesi başlatma (Önizleme)](storage-initiate-account-failover.md).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Doğru artıklık seçeneğini belirleyin

Tüm depolama hesapları yedeklilik için çoğaltılır. Hesabınız için seçtiğiniz artıklık seçeneği, ihtiyacınız olan dayanıklılık derecesine bağlıdır. Bölgesel kesintilere karşı koruma için, ikincil bölgeden okuma erişimi seçeneği olmadan veya bunlarla birlikte coğrafi olarak yedekli depolama ' yı seçin:  

**Coğrafi olarak yedekli depolama (GRS)** , verilerinizi en az yüzlerce mil olan iki coğrafi bölgede zaman uyumsuz olarak çoğaltır. Birincil bölge bir kesinti olursa, ikincil bölge verileriniz için yedek bir kaynak görevi görür. İkincil uç noktayı birincil uç noktaya dönüştürmek için bir yük devretme işlemi başlatabilirsiniz.

**Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS)** , ikincil uç noktaya okuma erişiminin ek avantajına sahip coğrafi olarak yedekli depolama sağlar. Birincil uç noktada bir kesinti oluşursa, RA-GRS için yapılandırılan ve yüksek kullanılabilirlik için tasarlanan uygulamalar ikincil uç noktadan okumaya devam edebilir. Microsoft, uygulamalarınız için en yüksek dayanıklılık için RA-GRS önerir.

Diğer Azure depolama artıklığı seçenekleri arasında, tek bir bölgedeki kullanılabilirlik bölgelerinde verilerinizi çoğaltan ve yerel olarak yedekli depolama (LRS), tek bir bölgedeki verileri tek bir veri merkezinde çoğaltan bölgesel olarak yedekli depolama (ZRS) bulunur. Depolama Hesabınız ZRS veya LRS için yapılandırılmışsa, bu hesabı GRS veya RA-GRS kullanacak şekilde dönüştürebilirsiniz. Coğrafi olarak yedekli depolama için hesabınızı yapılandırmak ek maliyetler doğurur. Daha fazla bilgi için bkz. [Azure Storage çoğaltma](storage-redundancy.md).

> [!NOTE]
> Coğrafi bölge yedekli depolama (GZRS) ve Okuma Erişimli Coğrafi bölge yedekli depolama (RA-GZRS) Şu anda önizleme aşamasındadır ancak müşterinin yönettiği hesap yük devretmesi ile aynı bölgelerde henüz sunulmamaktadır. Bu nedenle, müşteriler şu anda GZRS ve RA-GZRS hesaplarıyla hesap yük devretme olaylarını yönetemez. Önizleme sırasında, Microsoft GZRS/RA-GZRS hesaplarını etkileyen tüm yük devretme olaylarını yönetir.

> [!WARNING]
> Coğrafi olarak yedekli depolama, veri kaybı riskini taşır. Veriler ikincil bölgeye zaman uyumsuz olarak çoğaltılır, yani birincil bölgeye yazılan veriler ikincil bölgeye yazıldığında bir gecikme olur. Kesinti durumunda, henüz ikincil uç noktaya çoğaltılmamış birincil uç noktaya yazma işlemleri kaybedilir.

## <a name="design-for-high-availability"></a>Yüksek kullanılabilirliğe yönelik tasarım

Uygulamanızı, başlangıçtan itibaren yüksek kullanılabilirlik için tasarlamak önemlidir. Uygulamanızı tasarlama ve olağanüstü durum kurtarmayı planlama konusunda rehberlik için bu Azure kaynaklarına başvurun:

* [Azure için dayanıklı uygulamalar tasarlama](/azure/architecture/checklist/resiliency-per-service): Azure 'da yüksek oranda kullanılabilir uygulamalar oluşturmaya yönelik temel kavramlara genel bakış.
* [Kullanılabilirlik denetim listesi](/azure/architecture/checklist/resiliency-per-service): uygulamanızın yüksek kullanılabilirlik için en iyi tasarım uygulamalarını uyguladığını doğrulamak için bir denetim listesi.
* [RA-GRS kullanarak yüksek oranda kullanılabilir uygulamalar tasarlama](storage-designing-ha-apps-with-ragrs.md): RA-GRS 'nin avantajlarından yararlanmak için uygulama oluşturmaya yönelik tasarım kılavuzu.
* [Öğretici: BLOB depolama ile yüksek oranda kullanılabilir bir uygulama oluşturma](../blobs/storage-create-geo-redundant-storage.md): uç noktalar arasında otomatik olarak bir şekilde geçiş yapan ve kurtarmaların benzetilyilmiş olduğu yüksek oranda kullanılabilir bir uygulamanın nasıl oluşturulacağını gösteren bir öğretici. 

Ayrıca, Azure depolama verileriniz için yüksek kullanılabilirlik sağlamak üzere bu en iyi yöntemleri göz önünde bulundurun:

* **Diskler:** Azure sanal makineleriniz tarafından kullanılan VM disklerini yedeklemek için [Azure Backup](https://azure.microsoft.com/services/backup/) kullanın. Ayrıca, bölgesel bir olağanüstü durum durumunda sanal makinelerinizi korumak için [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) kullanmayı göz önünde bulundurun.
* **Blok Blobları:** Nesne düzeyinde silme ve üzerine yazma işlemlerini korumak için [geçici silme](../blobs/storage-blob-soft-delete.md) özelliğini açın veya [azcopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)veya [Azure veri taşıma kitaplığı](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)'nı kullanarak blok Blobları farklı bir bölgedeki başka bir depolama hesabına kopyalayın.
* **Dosyalar:** Dosyalarınızı farklı bir bölgedeki başka bir depolama hesabına kopyalamak için [AzCopy](storage-use-azcopy.md) veya [Azure PowerShell](storage-powershell-guide-full.md) kullanın.
* **Tablolar:** farklı bir bölgedeki başka bir depolama hesabına tablo verilerini dışarı aktarmak Için [AzCopy](storage-use-azcopy.md) kullanın.

## <a name="track-outages"></a>Kesintileri izleme

Müşteriler, Azure depolama ve diğer Azure hizmetlerinin sistem durumunu ve durumunu izlemek için [Azure hizmet durumu panosuna](https://azure.microsoft.com/status/) abone olabilir.

Microsoft ayrıca, yazma hatalarıyla ilgili hazırlanabilmesi için uygulamanızı tasarlamanızı önerir. Uygulamanız, yazma başarısızlıklarını birincil bölgedeki bir kesinti olasılığa karşı uyaran şekilde kullanıma sunmalıdır.

## <a name="understand-the-account-failover-process"></a>Hesap yük devretme sürecini anlayın

Müşteri tarafından yönetilen hesap yük devretmesi (Önizleme), birincil bölge herhangi bir nedenle kullanılamaz hale gelirse tüm depolama hesabınızı ikincil bölgeye devredebilmenizi sağlar. İkincil bölgeye yük devretmeyi zorlarsanız, istemciler yük devretme tamamlandıktan sonra ikincil uç noktaya veri yazmaya başlayabilir. Yük devretme genellikle yaklaşık bir saat sürer.

### <a name="how-an-account-failover-works"></a>Hesap yük devretmesinin nasıl çalıştığı

Normal koşullarda, istemci birincil bölgedeki bir Azure depolama hesabına veri yazar ve bu veriler ikincil bölgeye zaman uyumsuz olarak çoğaltılır. Aşağıdaki görüntüde birincil bölge kullanılabilir olduğunda senaryo gösterilmektedir:

![İstemciler, verileri birincil bölgedeki depolama hesabına yazar](media/storage-disaster-recovery-guidance/primary-available.png)

Birincil uç nokta herhangi bir nedenle kullanılamaz duruma gelirse, istemci artık depolama hesabına yazamayacak. Aşağıdaki görüntüde, birincil olarak kullanılamayan senaryo gösterilir, ancak henüz kurtarma gerçekleşmemiştir:

![Birincil kullanılamıyor, bu nedenle istemciler veri yazamaz](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Müşteri, ikincil uç noktaya yük devretme hesabı başlatır. Yük devretme işlemi, aşağıdaki görüntüde gösterildiği gibi, ikincil bitiş noktasının depolama hesabınız için yeni birincil uç nokta haline gelmesi için Azure depolama tarafından verilen DNS girişini güncelleştirir:

![Müşteri, ikincil uç noktaya hesap yük devretmesini başlatır](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

DNS girdisi güncelleştirildikten ve istekler yeni birincil uç noktaya yönlendirildikten sonra, GRS ve RA-GRS hesapları için yazma erişimi geri yüklenir. Blob 'lar, tablolar, kuyruklar ve dosyalar için mevcut depolama hizmeti uç noktaları, yük devretmeden sonra aynı kalır.

> [!IMPORTANT]
> Yük devretme işlemi tamamlandıktan sonra, depolama hesabı yeni birincil uç noktada yerel olarak yedekli olacak şekilde yapılandırılır. Yeni ikincil çoğaltmayı yeniden başlatmak için, hesabı coğrafi olarak yedekli depolamayı (RA-GRS veya GRS) kullanacak şekilde yapılandırın.
>
> LRS hesabını RA-GRS veya GRS 'ye dönüştürmenin bir maliyet doğurur olduğunu unutmayın. Bu maliyet, yük devretmeden sonra RA-GRS veya GRS kullanmak üzere yeni birincil bölgedeki depolama hesabını güncelleştirmek için geçerlidir.  

### <a name="anticipate-data-loss"></a>Veri kaybını tahmin edin

> [!CAUTION]
> Hesap yük devretmesi genellikle bazı veri kaybını içerir. Hesap yük devretmesini başlatma etkilerine ilişkin etkileri anlamak önemlidir.  

Veriler birincil bölgeden ikincil bölgeye zaman uyumsuz olarak yazıldığı için, birincil bölgeye yazma işlemi ikinci bölgeye çoğaltılmadan önce her zaman bir gecikme vardır. Birincil bölge kullanılamaz duruma gelirse, en son yazma işlemleri henüz ikincil bölgeye çoğaltılmayabilir.

Bir yük devretmeyi zorlarsanız, ikincil bölge yeni birincil bölge olur ve depolama hesabı yerel olarak yedekli olacak şekilde yapılandırıldığında birincil bölgedeki tüm veriler kaybedilir. Yük devretme gerçekleştiğinde ikinciye çoğaltılan tüm veriler korunur. Ancak, birincil diske yazılan tüm veriler, ikinciye de kalıcı olarak kaybedilir. 

**Son eşitleme zamanı** özelliği, birincil bölgedeki verilerin ikincil bölgeye yazıldığı en son zamanı gösterir. Son eşitleme zamanından önce yazılan tüm veriler ikincil üzerinde kullanılabilir, ancak son eşitleme zamanından sonra yazılan veriler ikinciye yazılmayabilir ve kaybolabilir. Hesap yük devretmesini başlatarak, tabi olabilecek veri kaybı miktarını tahmin etmek için bir kesinti durumunda bu özelliği kullanın. 

En iyi uygulama olarak, beklenen veri kaybını değerlendirmek için son eşitleme süresini kullanabilmeniz için uygulamanızı tasarlayın. Örneğin, tüm yazma işlemlerini günlüğe koyduysanız, hangi yazma işlemlerinin ikinciye eşitlenmediğini öğrenmek için son yazma işlemlerinizin saatini son eşitleme zamanına göre karşılaştırabilirsiniz.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Özgün birincili geri yüklerken dikkatli olun

Birincil sunucudan ikincil bölgeye yük devretme yaptıktan sonra, depolama hesabınız yeni birincil bölgede yerel olarak yedekli olacak şekilde yapılandırılır. GRS veya RA-GRS kullanacak şekilde güncelleştirerek hesabı coğrafi yedeklilik için yeniden yapılandırabilirsiniz. Hesap, yük devretmeden sonra coğrafi artıklık için yapılandırıldığında yeni birincil bölge, ilk yük devretmeden önce birincil olan yeni ikincil bölgeye hemen veri çoğaltmaya başlar. Bununla birlikte, birincil verilerdeki mevcut verilerin yeni ikinciye tamamen çoğaltılmaması zaman alabilir.

Depolama hesabı coğrafi yedeklilik için yeniden yapılandırıldıktan sonra, yeni birincili yeni ikincil sunucudan başka bir yük devretme başlatmak mümkündür. Bu durumda, ilk birincil bölge yük devretmeden önce birincil bölge yeniden olur ve yerel olarak yedekli olacak şekilde yapılandırılır. Yük devretme sonrası birincil bölgesindeki (orijinal ikincil) tüm veriler kaybolur. Depolama hesabındaki verilerin büyük bir bölümünü yeniden denemeden önce yeni ikincil hesaba çoğaltılmamışsa, büyük bir veri kaybını etkilemeyebilir. 

Önemli veri kaybını önlemek için, geri yüklemeden önce **son eşitleme zamanı** özelliğinin değerini denetleyin. Beklenen veri kaybını değerlendirmek için son eşitleme zamanını, verilerin yeni birincili son kez yazıldığı zamana göre karşılaştırın. 

## <a name="initiate-an-account-failover"></a>Hesap yük devretmesi başlatma

Azure portal, PowerShell, Azure CLı veya Azure depolama kaynak sağlayıcısı API 'sinden bir hesap yük devretmesi başlatabilirsiniz. Yük devretme başlatma hakkında daha fazla bilgi için bkz. [Hesap yük devretmesi başlatma (Önizleme)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Önizleme hakkında

Hesap yük devretmesi, Azure Resource Manager dağıtımlarıyla GRS veya RA-GRS kullanan tüm müşteriler için önizleme aşamasında kullanılabilir. Genel amaçlı v1, genel amaçlı v2 ve BLOB depolama hesabı türleri desteklenir. Hesap yük devretmesi Şu anda şu bölgelerde kullanılabilir:

- Doğu Asya
- Güneydoğu Asya
- Avustralya Doğu
- Avustralya Güneydoğu
- ABD Orta
- ABD Doğu 2
- ABD Orta Batı
- ABD Batı 2

Önizleme yalnızca üretim dışı kullanım için tasarlanmıştır. Üretim hizmet düzeyi sözleşmeleri (SLA 'Lar) Şu anda kullanılamıyor.

### <a name="register-for-the-preview"></a>Önizlemeye kaydolun

Önizlemeye kaydolmak için PowerShell 'de aşağıdaki komutları çalıştırın. Köşeli ayracın yer tutucusunu kendi abonelik KIMLIĞINIZLE değiştirdiğinizden emin olun:

```powershell
Connect-AzAccount -SubscriptionId <subscription-id>
Register-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

Önizleme için onay alınması 1-2 gün sürebilir. Kaydlarınızın onaylandığını doğrulamak için şu komutu çalıştırın:

```powershell
Get-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Diğer konular 

Önizleme dönemi boyunca bir yük devretmeyi zorlarsanız uygulamalarınızın ve hizmetlerinizin nasıl etkilenebileceğini anlamak için bu bölümde açıklanan ek konuları gözden geçirin.

#### <a name="azure-virtual-machines"></a>Azure sanal makineleri

Azure sanal makineleri (VM 'Ler), hesap yük devretmesi kapsamında yük devreder. Birincil bölge kullanılamaz duruma gelirse ve ikincil bölgeye yük devretmek, yük devretmeden sonra VM 'Leri yeniden oluşturmanız gerekir. 

#### <a name="azure-unmanaged-disks"></a>Azure yönetilmeyen diskler

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

### <a name="unsupported-features-or-services"></a>Desteklenmeyen özellikler veya hizmetler
Önizleme sürümü için hesap yük devretmesi için aşağıdaki özellikler veya hizmetler desteklenmez:

- Azure Dosya Eşitleme, depolama hesabı yük devretmesini desteklemez. Azure Dosya Eşitleme bulut uç noktaları olarak kullanılan Azure dosya paylaşımlarını içeren depolama hesapları yük devretmemelidir. Bunun yapılması eşitlemenin durmasına neden olur ve yeni katmanlı dosyalar söz konusu olduğunda beklenmedik veri kaybına neden olabilir.  
- Arşivlenmiş blob 'ları içeren bir depolama hesabı yük devredilemez. Arşivlenmiş Blobları yük devri planlamadığınız ayrı bir depolama hesabında saklayın.
- Premium blok bloblarını içeren bir depolama hesabı yük devredilemez. Premium blok bloblarını destekleyen depolama hesapları Şu anda coğrafi artıklığı desteklemez.
- Yük devretme işlemi tamamlandıktan sonra, aşağıdaki özellikler, başlangıçta etkinse çalışmayı durdurur: [olay abonelikleri](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview), [yaşam döngüsü Ilkeleri](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts), [depolama Analizi günlüğü](https://docs.microsoft.com/rest/api/storageservices/about-storage-analytics-logging).

## <a name="copying-data-as-an-alternative-to-failover"></a>Yük devretmeye alternatif olarak verileri kopyalama

Depolama Hesabınız RA-GRS için yapılandırılmışsa, ikincil uç noktayı kullanarak verilerinize okuma erişiminizin olması gerekir. Birincil bölgedeki bir kesinti olması durumunda yük devredememeyi tercih ediyorsanız, İkincil bölgedeki depolama hesabınızdan verileri etkilenmeyen bir bölgedeki başka bir depolama hesabına kopyalamak için [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)veya [Azure veri taşıma kitaplığı](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) gibi araçları kullanabilirsiniz. Daha sonra hem okuma hem de yazma kullanılabilirliği için uygulamalarınızı bu depolama hesabına işaret edebilirsiniz.

## <a name="microsoft-managed-failover"></a>Microsoft tarafından yönetilen yük devretme

Önemli bir olağanüstü durum nedeniyle bölgenin kaybolması durumunda Microsoft, bölgesel bir yük devretme işlemi başlatabilir. Bu durumda, sizin bölüminizdeki hiçbir işlem yapmanız gerekmez. Microsoft tarafından yönetilen yük devretme tamamlanana kadar, depolama hesabınıza yazma erişiminiz olmayacaktır. Depolama Hesabınız RA-GRS için yapılandırılmışsa uygulamalarınız ikincil bölgeden okunabilir. 

## <a name="see-also"></a>Ayrıca bkz.

* [Hesap yük devretmesini başlatma (Önizleme)](storage-initiate-account-failover.md)
* [RA-GRS’yi kullanarak yüksek kullanılabilirliğe sahip uygulamalar tasarlama](storage-designing-ha-apps-with-ragrs.md)
* [Öğretici: BLOB depolama ile yüksek oranda kullanılabilir bir uygulama oluşturma](../blobs/storage-create-geo-redundant-storage.md) 
