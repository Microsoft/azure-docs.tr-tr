---
title: Portalda hizmet yönetimi
titleSuffix: Azure Cognitive Search
description: Azure portal kullanarak Microsoft Azure barındırılan bir bulut arama hizmeti olan Azure Bilişsel Arama hizmetini yönetin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e00a810e7977e1c45c1833e0b901ff6804f7fb32
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113295"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Azure portal Azure Bilişsel Arama için hizmet yönetimi
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Bilişsel Arama, özel uygulamalara zengin arama deneyimi oluşturmak için kullanılan, tam olarak yönetilen, bulut tabanlı bir arama hizmetidir. Bu makalede, önceden sağladığınız bir arama hizmeti için [Azure Portal](https://portal.azure.com) gerçekleştirebileceğiniz hizmet yönetim görevleri ele alınmaktadır. Hizmet yönetimi, tasarım açısından hafif ve aşağıdaki görevlerle sınırlıdır:

> [!div class="checklist"]
> * Hizmetinize okuma veya yazma erişimi için kullanılan *api anahtarlarına* erişimi yönetin.
> * Bölümleri ve çoğaltmaları ayırmayı değiştirerek hizmet kapasitesini ayarlayın.
> * Hizmet katmanınızın en fazla sınırlarına göre kaynak kullanımını izleyin.

*Yükseltmenin* bir yönetim görevi olarak listelenmediğine dikkat edin. Hizmet sağlandığında kaynaklar ayrıldığından, farklı bir katmana taşımak yeni bir hizmet gerektirir. Ayrıntılar için bkz. [Azure bilişsel arama hizmeti oluşturma](search-create-service-portal.md).

Sorgu hacmi ve diğer ölçümleri izleyebilir ve bu öngörüleri kullanarak hizmetinizi daha hızlı yanıt süreleri için ayarlayabilirsiniz. Daha fazla bilgi için bkz. [kullanımı ve sorgu ölçümlerini](search-monitor-usage.md) ve [performansı ve iyileştirmeyi](search-performance-optimization.md)izleme.

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Yönetici hakları
Hizmetin kendisi için sağlanması veya kullanımdan kaldırılması, bir Azure abonelik Yöneticisi veya ortak yönetici tarafından yapılabilir.

Hizmet içinde, hizmet URL 'sine ve yönetici API 'sine erişimi olan herkesin hizmete okuma yazma erişimi vardır. Okuma-yazma erişimi, [RBAC tarafından tanımlanan roller](search-security-rbac.md)aracılığıyla uygulanan API anahtarları, dizinler, Dizin oluşturucular, veri kaynakları, zamanlamalar ve rol atamaları dahil sunucu nesnelerini ekleme, silme veya değiştirme olanağı sağlar.

Azure Bilişsel Arama ile tüm Kullanıcı etkileşimi şu modlardan birine denk gelir: hizmete okuma/yazma erişimi (yönetici hakları) veya hizmete salt okuma erişimi (sorgu hakları). Daha fazla bilgi için bkz. [API anahtarlarını yönetme](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Günlüğe kaydetme ve sistem bilgileri
Azure Bilişsel Arama, portal veya programlı arabirimler aracılığıyla tek bir hizmet için günlük dosyalarını sunmaz. Temel katmanda ve yukarıda, Microsoft, hizmet düzeyi sözleşmeleri (SLA) başına% 99,9 kullanılabilirlik için tüm Azure Bilişsel Arama hizmetlerini izler. Hizmet yavaşsa veya istek işleme SLA eşiklerinin altına düşerse, destek ekipleri, bu dosyaların kullanabildiği günlük dosyalarını gözden geçirir ve sorunu ele geçirebilir.

Hizmetiniz hakkındaki genel bilgiler açısından aşağıdaki yollarla bilgi edinebilirsiniz:

* Portalda, hizmet panosunda, bildirimler, Özellikler ve durum iletileri aracılığıyla.
* [Hizmet özelliklerini almak](https://docs.microsoft.com/rest/api/searchmanagement/services)için [PowerShell](search-manage-powershell.md) veya [Yönetim REST API](https://docs.microsoft.com/rest/api/searchmanagement/) kullanma veya dizin kaynağı kullanımında durum.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Kaynak kullanımını izleme
Panoda, kaynak izleme hizmet panosunda gösterilen bilgilerle ve hizmeti sorgulayarak elde ettiğiniz birkaç ölçümle sınırlıdır. Hizmet panosunda, kullanım bölümünde, Bölüm kaynak düzeylerinin uygulamanız için yeterli olup olmadığını hızlı bir şekilde belirleyebilirsiniz. Günlüğe kaydedilen olayları yakalamak ve sürdürmek istiyorsanız, Azure izleme gibi dış kaynakları sağlayabilirsiniz. Daha fazla bilgi için bkz. [Azure bilişsel arama izleme](search-monitor-usage.md).

Arama hizmeti REST API kullanarak belgeler ve dizinlerde program aracılığıyla bir sayı alabilir: 

* [Dizin istatistiklerini alma](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Belge sayısı](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Olağanüstü durum kurtarma ve hizmet kesintileri

Verilerinizi, veri merkezi düzeyinde bir kesinti varsa Azure Bilişsel Arama, hizmetin anında yük devretmesini sağlamaz. Veri merkezinde bir küme başarısız olursa, işlemler ekibi, hizmeti algılar ve geri yükleme işlemini çalışır. Hizmet geri yükleme sırasında kapalı kalma süresi yaşarsınız, ancak [hizmet düzeyi sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)başına hizmet kullanım dışı kalması için hizmet kredileri isteyebilirsiniz. 

Microsoft 'un denetimi dışındaki çok sayıda hata oluşması durumunda sürekli hizmet gerekliyse, farklı bir bölgede [ek bir hizmet](search-create-service-portal.md) sağlayabilir ve dizinlerin tüm hizmetlerde tamamen yedekli olmasını sağlamak için coğrafi çoğaltma stratejisi uygulayabilirsiniz.

Dizinleri doldurmak ve yenilemek için [Dizin oluşturucular](search-indexer-overview.md) kullanan müşteriler, aynı veri kaynağından yararlanan coğrafi olarak özel Dizin oluşturucular aracılığıyla olağanüstü durum kurtarmayı işleyebilir. Her biri Dizin Oluşturucu çalıştıran farklı bölgelerdeki iki hizmet, coğrafi yedeklilik sağlamak için aynı veri kaynağını dizinlede olabilir. Aynı zamanda coğrafi olarak yedekli veri kaynaklarından Dizin oluşturdıysanız Azure Bilişsel Arama dizin oluşturucularının yalnızca birincil çoğaltmalardan artımlı Dizin gerçekleştirebillerinin farkında olun. Bir yük devretme olayında, Dizin oluşturucuyu yeni birincil çoğaltmaya yeniden işaret ettiğinizden emin olun. 

Dizin oluşturucular kullanmıyorsanız, nesneleri ve verileri farklı arama hizmetlerine paralel olarak göndermek için uygulama kodunuzu kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure bilişsel arama performans ve iyileştirme](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Yedekleme ve geri yükleme

Azure Bilişsel Arama bir birincil veri depolama çözümü olmadığından, self servis yedekleme ve geri yükleme için biçimsel bir mekanizma sağlamayız. Bununla birlikte, Dizin tanımınızı ve anlık görüntüsünü bir dizi JSON dosyasına yedeklemek için bu [Azure bilişsel arama .NET örnek deposu](https://github.com/Azure-Samples/azure-search-dotnet-samples) ' nda **Dizin-yedekleme-geri yükleme** örnek kodunu kullanabilir ve gerekirse dizini geri yüklemek için bu dosyaları kullanabilirsiniz. Bu araç Ayrıca, dizinleri hizmet katmanları arasında taşıyabilir.

Aksi halde, dizin oluşturmak ve doldurmak için kullanılan uygulama kodunuz, yanlışlıkla bir dizini silerseniz geri yükleme ve geri yükleme seçeneğidir. Bir dizini yeniden oluşturmak için (varsa) onu siler, hizmette dizini yeniden oluşturun ve birincil veri deponuzdan verileri alarak yeniden yükleyin.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Ölçek büyütme veya küçültme
Her arama hizmeti en az bir çoğaltma ve bir bölüm ile başlar. [Adanmış kaynaklar sağlayan bir katmana](search-limits-quotas-capacity.md)kaydolduysanız, kaynak kullanımını ayarlamak için hizmet panosundaki **Ölçek** kutucuğuna tıklayın.

Her iki kaynaktan da kapasite eklediğinizde, hizmet bunları otomatik olarak kullanır. Biraz başka eylem gerekmez, ancak yeni kaynağın etkisinden önce küçük bir gecikme olur. Ek kaynakların sağlanması 15 dakika veya daha uzun sürebilir.

 ![][10]

### <a name="add-replicas"></a>Çoğaltmalar ekleme
Saniye başına sorgu (QPS) veya yüksek kullanılabilirlik elde etmek çoğaltmalar eklenerek yapılır. Her çoğaltma bir dizinin kopyasına sahiptir, bu nedenle bir çoğaltma eklemek, hizmet sorgu isteklerini işlemek için kullanılabilir bir dizin daha arar. Yüksek kullanılabilirlik için en az 3 çoğaltma gerekir (Ayrıntılar için [Kapasite planlaması](search-capacity-planning.md) konusuna bakın).

Daha fazla çoğaltmaya sahip bir arama hizmeti, daha fazla sayıda dizin üzerinde sorgu isteklerinin yükünü dengeleyebilir. Sorgu hacmi düzeyi verildiğinde, isteğin hizmet için kullanılabilir dizinin daha fazla kopyası olduğunda sorgu üretimi daha hızlı olur. Sorgu gecikmesi yaşıyorsanız, ek çoğaltmalar çevrimiçi olduktan sonra performans üzerinde olumlu bir etkisi bekleyebilir.

Yineleme eklerken sorgu işleme yukarı gidebilse de, hizmetinize çoğaltmalar eklerken tam olarak Double veya üçlü değildir. Tüm arama uygulamaları, sorgu performansını engelleyebilecek dış faktörlere tabidir. Karmaşık sorgular ve ağ gecikmesi, sorgu yanıt sürelerindeki çeşitlere katkıda bulunan iki etmendir.

### <a name="add-partitions"></a>Bölüm Ekle
Çoğu hizmet uygulamasının bölümleri yerine daha fazla çoğaltma için yerleşik bir ihtiyacı vardır. Daha fazla belge sayısının gerekli olduğu durumlarda, standart hizmete kaydolduysanız bölüm ekleyebilirsiniz. Temel katman ek bölümler sağlamaz.

Standart katmanda, bölümler 12 ' ye (özellikle, 1, 2, 3, 4, 6 veya 12) katlara eklenir. Bu bir parça yapıtı. Hepsi 1 bölümde depolanabilen veya eşit olarak 2, 3, 4, 6 veya 12 bölüme (bölüm başına bir parça) bölünebilen 12 parçalı bir dizin oluşturulur.

### <a name="remove-replicas"></a>Çoğaltmaları kaldır
Yüksek sorgu birimleri dönemlerinden sonra, arama sorgusu yükleri normalleştirildikten sonra (örneğin, tatil satışları kapatıldıktan sonra) çoğaltmaları azaltmak için kaydırıcıyı kullanabilirsiniz. Sizin bölüminizdeki başka bir adım gerekli değildir. Çoğaltma sayısını azaltmak veri merkezindeki sanal makineleri yeniden oluşturur. Sorgunuz ve veri alma işlemleri artık daha az sayıda VM 'de çalışacak. En düşük gereksinim bir yinelemedir.

### <a name="remove-partitions"></a>Bölümleri kaldır
İş üzerinde fazladan çaba gerektirmeyen çoğaltmaları kaldırmanın aksine, daha fazla depolama kullanıyorsanız daha fazla depolama kullanıyor olabilirsiniz. Örneğin, çözümünüz üç bölüm kullanıyorsa, yeni depolama alanı dizininizi barındırmak için gerekenden küçükse bir veya iki bölüme yeniden boyutlandırma bir hata oluşturur. Tahmin edebileceğiniz gibi seçenekleriniz, alanı boşaltmak için ilişkili bir dizin içindeki dizinleri veya belgeleri siler veya geçerli yapılandırmayı tutacağız.

Belirli bölümlerde hangi dizin parçaları depolandığını belirten bir algılama yöntemi yoktur. Her bölüm, depolama alanı üzerinde yaklaşık 25 GB sağlar, bu nedenle depolama alanını sahip olduğunuz bölüm sayısına göre daha fazla bir boyuta düşürmeniz gerekecektir. Bir bölüme geri dönmek istiyorsanız, tüm 12 parçaların sığması gerekir.

Gelecekteki planlamaya yardımcı olmak için, gerçekten ne kadar kullandığınızı görmek üzere depolamayı ( [Dizin Istatistiklerini al](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)' ı kullanarak) denetlemek isteyebilirsiniz. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Sonraki adımlar
Hizmet yönetiminin arkasındaki kavramları anladıktan sonra, görevleri otomatikleştirmek için [PowerShell](search-manage-powershell.md) 'i kullanmayı göz önünde bulundurun.

Ayrıca [performans ve iyileştirme makalesinin](search-performance-optimization.md)gözden geçirilmesini öneririz.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



