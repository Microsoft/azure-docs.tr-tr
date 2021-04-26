---
title: En iyi uygulamalar
description: Azure Batch çözümlerinizi geliştirmek için en iyi uygulamaları ve yararlı ipuçlarını öğrenin.
ms.date: 03/11/2020
ms.topic: conceptual
ms.openlocfilehash: 7ef94b07a5131726c42a94088fd3ee1f413dbec7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802361"
---
# <a name="azure-batch-best-practices"></a>En iyi Azure Batch uygulamalar

Bu makalede, Batch ile gerçek yaşam deneyimlerine bağlı olarak Azure Batch hizmeti etkin bir şekilde kullanmak için en iyi yöntemler ve yararlı ipuçları açıklanmaktadır. Bu ipuçları, performansı geliştirmenize ve Azure Batch çözümlerinizde tasarımı ortadan önlemenize yardımcı olabilir.

> [!TIP]
> Azure Batch güvenlik hakkında rehberlik için bkz. [Batch güvenlik ve uyumluluk en iyi uygulamaları](security-best-practices.md).

## <a name="pools"></a>Havuzlar

[Havuzlar](nodes-and-pools.md#pools) , Batch hizmetinde işlerin yürütülmesi için işlem kaynaklarıdır. Aşağıdaki bölümler, Batch havuzlarıyla çalışma önerileri sağlar.

### <a name="pool-configuration-and-naming"></a>Havuz yapılandırması ve adlandırma

- **Havuz ayırma modu:** Batch hesabı oluştururken iki havuz ayırma modu arasından seçim yapabilirsiniz: **Batch hizmeti** veya **Kullanıcı aboneliği**. Çoğu durumda, toplu yönetilen aboneliklerde havuzların arkasında ayrıldığı varsayılan Batch hizmeti modunu kullanmanız gerekir. Alternatif Kullanıcı aboneliği modunda, bir havuz oluşturulduğunda Batch VM'leri ve diğer kaynaklar doğrudan aboneliğinizde oluşturulur. Kullanıcı aboneliği hesapları, önemli, ancak küçük bir senaryo alt kümesini etkinleştirmek için öncelikli olarak kullanılır. Kullanıcı aboneliği modu hakkında daha fazla bilgi için kullanıcı aboneliği [modu Için ek yapılandırma](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode)makalesini okuyun.

- **' Virtualmachineconfiguration ' veya ' cloudServiceConfiguration ':** Şu anda iki yapılandırmayı kullanarak havuzlar oluşturabilirsiniz, ancak ' cloudServiceConfiguration ' değil, yeni havuzlar ' virtualMachineConfiguration ' kullanılarak yapılandırılmalıdır. Tüm geçerli ve yeni toplu Işlem özellikleri, sanal makine yapılandırma havuzları tarafından desteklenecektir. Cloud Services yapılandırma havuzları tüm özellikleri desteklemez ve yeni bir özellik planlanmaz. Yeni ' cloudServiceConfiguration ' havuzları oluşturamaz veya [29 şubat 2024 ' den sonra](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)var olan havuzlara yeni düğümler ekleyemeyeceksiniz. Daha fazla bilgi için bkz. [Cloud Services Batch havuzu yapılandırmasını sanal makineye geçirme](batch-pool-cloud-service-to-virtual-machine-configuration.md).

- **İş ve görev çalışma süresini, havuzdan eşleme işi belirlenirken göz önünde bulundurun:** Öncelikle kısa süreli görevlerden oluşan işleriniz varsa ve beklenen toplam görev sayısı küçük olduğundan, işin genel beklenen çalışma zamanının uzun olmaması ve her iş için yeni bir havuz ayrılmaması gerekir. Düğümlerin ayırma süresi, işin çalışma süresini azaledecektir.

- **Havuzlar birden çok işlem düğümüne sahip olmalıdır:** Bağımsız düğümlerin her zaman kullanılabilir olması garanti edilmez. Yaygın olarak, donanım hataları, işletim sistemi güncelleştirmeleri ve diğer sorunların bir konağı, tek tek düğümlerin çevrimdışı olmasına neden olabilir. Batch iş yükünüz belirleyici gerektiriyorsa, garantili ilerleme durumunda birden çok düğüm içeren havuzlar ayırmanız gerekir.

- **Yaklaşan yaşam süresi (EOL) tarihleriyle görüntüleri kullanmayın.**
    Toplu Iş desteği ömrü sonu (EOL) tarihleri olan görüntülerden kaçınmamak kesinlikle önerilir. Bu tarihler [ `ListSupportedImages` API](https://docs.microsoft.com/rest/api/batchservice/account/listsupportedimages), [PowerShell](https://docs.microsoft.com/powershell/module/az.batch/get-azbatchsupportedimage)veya [Azure CLI](https://docs.microsoft.com/cli/azure/batch/pool/supported-images)aracılığıyla bulunabilir. Havuzlarınız için uygun olan EOL tarihlerinin görünümünü düzenli olarak yenilemek ve EOL tarihi gerçekleşmeden önce iş yüklerinizi geçirmek sizin sorumluluğunuzdadır. Belirtilen bir düğüm aracısına sahip özel bir görüntü kullanıyorsanız, özel görüntünüzün türetildiği veya ile hizalandığı görüntü için toplu Iş desteğini, yaşam bitiş tarihlerini izlediğinizden emin olmanız gerekir.

- **Kaynak adlarını yeniden kullanmayın.**
    Batch kaynakları (işler, havuzlar vb.) genellikle zaman içinde gelir ve zaman içinde gider. Örneğin, Pazartesi günü bir havuz oluşturabilir, Salı günü silebilir ve ardından Perşembe üzerinde başka bir havuz oluşturabilirsiniz. Oluşturduğunuz her yeni kaynağa, daha önce kullanmadığınız benzersiz bir ad verilmelidir. Bu, bir GUID kullanılarak (tüm kaynak adı olarak veya bunun bir parçası olarak) ya da kaynağın kaynak adında oluşturulduğu zamanı katıştırarak yapılabilir. Batch, gerçek kaynak KIMLIĞI insana sahip olmayan bir şey olsa da kaynağa okunabilir bir ad vermek için kullanılabilen [DisplayName](/dotnet/api/microsoft.azure.batch.jobspecification.displayname)'i destekler. Benzersiz adların kullanılması, hangi belirli kaynağın günlüklerde ve ölçümlerde bir şeyler olduğunu ayırt etmenize daha kolay hale getirir. Ayrıca, bir kaynak için bir destek durumu dosyası oluşturmanız gerekiyorsa belirsizlik kaldırılır.


- **Havuz bakımı sırasında süreklilik ve hata:** İşlerinizin havuzları dinamik olarak kullanması en iyisidir. İşleriniz her şey için aynı havuzu kullanıyorsa, havuzda bir sorun oluşursa işlerin çalıştırılmayabileceği bir şansınız vardır. Bu, özellikle zamana duyarlı iş yükleri için önemlidir. Bunu çözmek için, her bir işi zamanlarken bir havuzu dinamik olarak seçin veya oluşturun veya uygun olmayan bir havuzu atlayabilmeniz için havuz adını geçersiz kılmak üzere bir yol belirtin.

- **Havuz bakımı sırasında iş sürekliliği ve hata:** Havuzun, iç hatalar, kapasite kısıtlamaları vb. için istediğiniz boyuta büyüyebileceği birçok neden vardır. Bu nedenle, işleri farklı bir havuzda yeniden hedeflemeniz (muhtemelen farklı bir VM boyutu ile) gerekirse bu Işlemi [Updatejob](/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update)aracılığıyla destekler. Bir statik havuz KIMLIĞI kullanmaktan kaçının hiçbir şekilde silinmeyeceğinden ve hiçbir şekilde değişmeyeceğinden, beklenmez.

### <a name="pool-lifetime-and-billing"></a>Havuz ömrü ve faturalama

Havuz ömrü, havuz yapılandırmasına uygulanan ayırma ve seçenekler yöntemine bağlı olarak farklılık gösterebilir. Havuzlar, zaman içinde herhangi bir zamanda, havuzda rastgele bir yaşam süresine ve farklı sayıda işlem düğümüne sahip olabilir. Havuzdaki işlem düğümlerini açıkça veya hizmet tarafından sunulan özellikler aracılığıyla ([Otomatik ölçeklendirme](nodes-and-pools.md#automatic-scaling-policy) veya [Otomatik havuz](nodes-and-pools.md#autopools)) yönetmek sizin sorumluluğunuzdadır.

- **Havuzları yeni tut:** [En son düğüm Aracısı güncelleştirmelerini ve hata düzeltmelerini](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md)aldığınızdan emin olmak için havuzlarınızı her bir ayda bir sıfır olacak şekilde yeniden boyutlandırın. Havuzunuz, yeniden oluşturulup 0 işlem düğümlerine yeniden boyutlandırılana kadar düğüm Aracısı güncelleştirmeleri almaz. Havuzunuzu yeniden oluşturmadan veya yeniden boyutlandırabilmeniz için, [düğümler](#nodes) bölümünde anlatıldığı gibi, hata ayıklama amacıyla herhangi bir düğüm Aracısı günlüğünü indirmeniz önerilir.

- **Havuz yeniden oluşturma:** Benzer bir notta havuzlarınızın günlük olarak silinmesi ve yeniden oluşturulması önerilmez. Bunun yerine, yeni bir havuz oluşturun, mevcut işlerinizi yeni havuza işaret etmek üzere güncelleştirin. Tüm görevler yeni havuza taşındıktan sonra eski havuzu silin.

- **Havuz verimliliği ve faturalama:** Batch 'in kendisi ek ücret vermez, ancak kullanılan işlem kaynakları için ücret ödemeniz gerekir. İçindeki durum ne olursa olsun, havuzdaki her işlem düğümü için faturalandırılırsınız. Bu, düğüm için gereken depolama ve ağ maliyetleri gibi tüm ücretleri içerir. En iyi uygulamalar hakkında daha fazla bilgi edinmek için bkz. [Azure Batch Için maliyet analizi ve bütçeleri](budget.md).

### <a name="pool-allocation-failures"></a>Havuz ayırma sorunları

Havuz ayırma arızaları, ilk ayırma veya sonraki yeniden boyutlandırmalarda herhangi bir noktada gerçekleşebilir. Bunun nedeni, bir bölgedeki geçici kapasiteden veya toplu Işin bağımlı olduğu diğer Azure hizmetlerindeki hatalardan kaynaklanabilir. Çekirdek kotanızın garantisi, ancak bunun yerine bir sınır değildir.

### <a name="unplanned-downtime"></a>Planlanmamış kapalı kalma süresi

Toplu Iş havuzlarının Azure 'da kesinti süresi olaylarıyla karşılaşması mümkündür. Senaryonuzu veya Batch için iş akışınızı planlarken ve geliştirirken bunu göz önünde bulundurun.

Bir düğümün başarısız olması durumunda, Batch otomatik olarak bu işlem düğümlerini sizin adınıza kurtarmaya çalışır. Bu, kurtarılan düğüm üzerinde çalışan herhangi bir görevin yeniden çizelgelenmesi tetiklenebilir. Kesilen görevler hakkında daha fazla bilgi edinmek için bkz. [yeniden denemeler tasarlama](#design-for-retries-and-re-execution) .

### <a name="custom-image-pools"></a>Özel görüntü havuzları

Sanal makine yapılandırmasını kullanarak bir Azure Batch havuzu oluşturduğunuzda, havuzdaki her bir işlem düğümü için işletim sistemini sağlayan bir VM görüntüsü belirtirsiniz. Havuzu desteklenen bir Azure Marketi görüntüsüyle oluşturabilir veya [paylaşılan görüntü Galerisi görüntüsüyle özel bir görüntü oluşturabilirsiniz](batch-sig-images.md). Özel bir görüntü havuzu oluşturmak için [yönetilen bir görüntü](batch-custom-images.md) de kullanabilirsiniz, ancak mümkün olduğunda paylaşılan görüntü galerisini kullanarak özel görüntüler oluşturmanız önerilir. Paylaşılan görüntü galerisinin kullanılması, havuzları daha hızlı sağlamanıza, büyük miktarlarda VM 'Lere göre ölçeklenmenize ve VM 'Ler sağlanırken güvenilirliği artırmanıza yardımcı olur.

### <a name="third-party-images"></a>Üçüncü taraf görüntüleri

Havuzlar, Azure Marketi 'Nde yayınlanan üçüncü taraf görüntüleri kullanılarak oluşturulabilir. Kullanıcı aboneliği modu Batch hesaplarıyla, bazı üçüncü taraf görüntülerle bir havuz oluştururken "Market satın alma uygunluğu denetimi nedeniyle ayrılamadı" hatasını görebilirsiniz. Bu hatayı çözmek için görüntünün yayımcısı tarafından ayarlanan koşulları kabul edin. [Azure PowerShell](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) veya [Azure CLI](/cli/azure/vm/image/terms)kullanarak bunu yapabilirsiniz.

### <a name="azure-region-dependency"></a>Azure bölge bağımlılığı

Zamana duyarlı veya üretim iş yükünüz varsa, tek bir Azure bölgesine güvenmemelisiniz. Nadir olarak, bir bölgenin tamamını etkileyebilecek sorunlar vardır. Örneğin, işlemelerinizin belirli bir zamanda başlaması gerekiyorsa, birincil bölgenizdeki havuzun ölçeğini *başlangıç zamanından önce da* ölçeklendirin. Havuz ölçeği başarısız olursa, bir yedekleme bölgesindeki (veya bölgelerde) bir havuzu ölçeklendirmeye geri dönebilirsiniz. Farklı bölgelerdeki birden çok hesap genelinde havuzlar, başka bir havuz ile yanlış bir sorun varsa, daha kolay erişilebilir bir yedekleme sağlar. Daha fazla bilgi için bkz. [uygulamanızı yüksek kullanılabilirlik Için tasarlama](high-availability-disaster-recovery.md).

## <a name="jobs"></a>İşler

[İş](jobs-and-tasks.md#jobs) , yüzlerce, binlerce veya hatta milyonlarca görevi içerecek şekilde tasarlanan bir kapsayıcıdır. İşleri oluştururken bu yönergeleri izleyin.

### <a name="fewer-jobs-more-tasks"></a>Daha az iş, daha fazla görev

Tek bir görevi çalıştırmak için bir iş kullanmak verimsiz değildir. Örneğin, her biri 10 görev içeren 100 iş oluşturmak yerine 1000 görevi içeren tek bir işi kullanmak daha etkilidir. Her biri tek bir görev içeren 1000 iş çalıştırmak, en az verimli, en yavaş ve en pahalı yaklaşım olacaktır.

Bu nedenle, binlerce eşzamanlı etkin iş gerektiren bir Batch çözümü tasarlayamadığından emin olun. Görevler için kota yoktur, bu nedenle çok sayıda görevi olabildiğince az iş olarak yürütmek, [işinizi ve iş zamanlaması kotalarını](batch-quota-limit.md#resource-quotas)verimli bir şekilde kullanır.

### <a name="job-lifetime"></a>İş ömrü

Toplu işin, sistemden silinene kadar sınırsız bir ömrü vardır. Durumu, zamanlama için daha fazla görevi kabul edip edemeyeceğini belirler.

Açık olarak sonlandırılmadığı takdirde bir iş otomatik olarak tamamlandı durumuna taşınamaz. Bu, [Onalltasksall](/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete) özelliği veya [Maxduvara Clocktime](/rest/api/batchservice/job/add#jobconstraints)aracılığıyla otomatik olarak tetiklenebilir.

Varsayılan bir [etkin iş ve iş zamanlaması kotası](batch-quota-limit.md#resource-quotas)vardır. İşlerin ve iş zamanlamalarının tamamlandı durumunda bu kotaya dahil sayılmaz.

## <a name="tasks"></a>Görevler

[Görevler](jobs-and-tasks.md#tasks) , bir işi oluşturan bireysel iş birimleridir. Görevler kullanıcı tarafından gönderilir ve işlem düğümleri için toplu Iş tarafından zamanlanır. Görevleri oluştururken ve yürütürken yapmanız gereken çeşitli tasarım konuları vardır. Aşağıdaki bölümlerde, sorunları ele almak ve verimli bir şekilde gerçekleştirmek için genel senaryolar ve görevlerinizi tasarlamak açıklanmaktadır.

### <a name="save-task-data"></a>Görev verilerini Kaydet

İşlem düğümleri doğası gereği daha kısa. Yığın içinde [Otomatik havuz](nodes-and-pools.md#autopools) ve [Otomatik ölçeklendirme](nodes-and-pools.md#automatic-scaling-policy) gibi birçok özellik vardır ve bu da düğümlerin kaybolmasını kolaylaştırır. Düğümler bir havuzdan ayrıldığında (bir yeniden boyutlandırma veya havuz silme nedeniyle), bu düğümlerdeki tüm dosyalar da silinir. Bu nedenle, bir görev tamamlanmadan önce üzerinde çalıştığı düğümün çıkışını ve kalıcı bir depoya taşımalıdır. Benzer şekilde, bir görev başarısız olursa, dayanıklı bir depoya hatayı tanılamak için gereken günlükleri taşımalıdır.

Toplu işlem, Azure depolama 'yı kullanarak verileri [OutputFiles](batch-task-output-files.md)aracılığıyla karşıya yüklemeyi, ayrıca çeşitli paylaşılan dosya sistemlerini de veya kendi görevlerinizde karşıya yüklemeyi gerçekleştirmek için tümleşik destek içerir.

### <a name="manage-task-lifetime"></a>Görev ömrünü yönetme

Görevleri artık gerekli olmadığında silin veya bir [retentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) görev kısıtlaması ayarlayın. Bir `retentionTime` ayarlanmışsa, toplu iş süresi dolduktan sonra görev tarafından kullanılan disk alanını otomatik olarak temizler `retentionTime` .

Görevleri silme işlemi iki şeyi gerçekleştirir. İşte, ilgilendiğiniz görevi sorgulamayı/bulmayı daha da zorlamanızı sağlayan (tamamlanan görevleri filtrelemeniz gerektiği için) iş içinde görevlerin bir oluşturma işlemi olmamasını sağlar. Ayrıca, düğümdeki karşılık gelen görev verilerini de temizler (belirtilen `retentionTime` zaten isabet mıştır). Bu, düğümlerinizin görev verileriyle doldurulup disk alanı tükenmemesini sağlamaya yardımcı olur.

### <a name="submit-large-numbers-of-tasks-in-collection"></a>Koleksiyonda çok sayıda görev gönder

Görevler ayrı ayrı veya koleksiyonlarda gönderilebilir. Ek yükü ve gönderim süresini azaltmak için görevler toplu gönderimi yaparken bir seferde en fazla 100 olan [koleksiyonlara](/rest/api/batchservice/task/addcollection) görev gönderebilirsiniz.

### <a name="set-max-tasks-per-node-appropriately"></a>Düğüm başına en fazla görevi uygun şekilde ayarlayın

Batch, düğümlerde fazla abone olan görevleri destekler (bir düğümden daha fazla görev çalıştırmak çekirdekler vardır). Görevlerinizin, havuzunuzdaki düğümlere "sığması" durumunda olduğundan emin olmanız gerekir. Örneğin, her birinin tek bir düğümde (ile bir havuzda) %25 CPU kullanımı tükettiği sekiz görevi zamanlamaya çalışırsanız, düzeyi düşürülmüş bir deneyimle karşılaşabilirsiniz `taskSlotsPerNode = 8` .

### <a name="design-for-retries-and-re-execution"></a>Yeniden denemeler ve yeniden yürütme için tasarım

Görevler, toplu Işlem tarafından otomatik olarak yeniden denenebilir. İki tür yeniden deneme vardır: Kullanıcı denetimli ve dahili. Kullanıcı denetimli yeniden denemeler, görevin [Maxtaskretrycount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount)tarafından belirtilir. Görevde belirtilen bir program sıfır dışında bir çıkış kodu ile çıktığında, görev değerine kadar yeniden denenir `maxTaskRetryCount` .

Nadir olarak bir görev, işlem düğümündeki hatalar nedeniyle, görev çalışırken düğüm üzerinde iç durumu veya bir başarısızlığı güncelleştirmeme gibi yeniden deneniyor olabilir. Görev, mümkünse aynı işlem düğümünde yeniden denenecektir, bu, görevde bir süre önce bir iç sınıra kadar ve bir toplu Işlem tarafından, potansiyel olarak farklı bir işlem düğümünde yeniden zamanlanmasını ertelenir.

Görevlerinizi adanmış veya düşük öncelikli düğümlerde yürütürken hiçbir tasarım farkı yoktur. Bir görevin düşük öncelikli bir düğümde çalışırken geçersiz hale gelmiş veya ayrılmış bir düğümdeki bir hata nedeniyle kesintiye uğratıldığı, her iki durum da görevi withfailure hatası tasarlayarak azaltılmıştır.

### <a name="build-durable-tasks"></a>Dayanıklı Görevler oluşturun

Görevler hatalı ve yeniden denemeye yetecek şekilde tasarlanmalıdır. Bu, özellikle uzun süre çalışan görevler için önemlidir. Bunu yapmak için, görevlerin aynı anda birden çok kez çalıştırılsa bile aynı, tek bir sonuç oluşturmasını sağlayın. Bunu gerçekleştirmenin bir yolu, görevlerinizi "hedef aramaktır". Diğer bir yol da görevlerinizin ıdempotent olduğundan emin olmak (görevler kaç kez çalıştırıldıklarından bağımsız olarak aynı sonuca sahip olacaktır).

Ortak bir örnek, dosyaları bir işlem düğümüne kopyalamak için kullanılan bir görevdir. Basit yaklaşım, her çalıştığında belirtilen her dosyayı kopyalayan, verimsiz olan ve geldiğinde hatası için oluşturulmamış bir görevdir. Bunun yerine, dosyaların işlem düğümünde olduğundan emin olmak için bir görev oluşturun; zaten mevcut olan dosyaları yeniden kopyalamamış bir görev. Bu şekilde, görev kesintiye uğratıldığında, görev ayrıldıktan sonra açılır.

### <a name="avoid-short-execution-time"></a>Kısa yürütme zamanından kaçının

Yalnızca bir ile iki saniye çalışan görevler ideal değildir. Tek bir görevde önemli miktarda iş yapmayı deneyin (en az 10 saniye, saat veya güne kadar). Her görev bir dakika (veya daha fazla) boyunca yürütülüyordur, genel işlem zamanının bir bölümü olarak zamanlama yükü küçüktür.

### <a name="use-pool-scope-for-short-tasks-on-windows-nodes"></a>Windows düğümlerinde kısa görevler için havuz kapsamını kullanma

Toplu Iş düğümlerinde bir görevi zamanlarken, görev kapsamı veya havuz kapsamıyla çalıştırmayı seçebilirsiniz. Görev yalnızca kısa bir süre için çalışıyorsa, bu görevin otomatik Kullanıcı hesabını oluşturmak için gereken kaynaklar nedeniyle görev kapsamı verimsiz olabilir. Daha fazla verimlilik için, bu görevleri havuz kapsamına ayarlamayı göz önünde bulundurun. Daha fazla bilgi için bkz. [bir görevi havuz kapsamı ile otomatik Kullanıcı olarak çalıştırma](batch-user-accounts.md#run-a-task-as-an-auto-user-with-pool-scope).

## <a name="nodes"></a>Düğümler

[İşlem düğümü](nodes-and-pools.md#nodes) , uygulamanızın iş yükünün bir kısmını işlemeye ayrılmış bir Azure sanal MAKINESI (VM) veya bulut hizmeti sanal makinedir. Düğümlerle çalışırken bu yönergeleri izleyin.

### <a name="idempotent-start-tasks"></a>Idempotent başlangıç görevleri

Diğer görevlerde olduğu gibi, düğüm [Başlangıç görevi](jobs-and-tasks.md#start-task) , düğüm her önyüklendiğinde yeniden çalıştırılacak şekilde ıdempotent olmalıdır. Bir ıdempotent görevi, birden çok kez çalıştırıldığında tutarlı bir sonuç üreten bir görevdir.

### <a name="isolated-nodes"></a>Yalıtılmış düğümler

Uyumluluk ve mevzuat gereksinimleri olan iş yükleri için yalıtılmış VM boyutlarını kullanmayı düşünün. Sanal makine yapılandırma modundaki desteklenen yalıtılmış Boyutlar,,,, `Standard_E80ids_v4` `Standard_M128ms` ve içerir `Standard_F72s_v2` `Standard_G5` `Standard_GS5` `Standard_E64i_v3` . Yalıtılmış VM boyutları hakkında daha fazla bilgi için bkz. [Azure 'Da sanal makine yalıtımı](../virtual-machines/isolation.md).

### <a name="manage-long-running-services-via-the-operating-system-services-interface"></a>İşletim sistemi Hizmetleri arabirimi aracılığıyla uzun süre çalışan hizmetleri yönetme

Bazen, düğümdeki Batch aracısının yanı sıra başka bir aracı da çalıştırmanız gerekir. Örneğin, düğümden veri toplamak ve bunu raporlamak isteyebilirsiniz. Bu aracıların bir Windows hizmeti veya Linux hizmeti gibi işletim sistemi hizmetleri olarak dağıtılmasını öneririz `systemd` .

Bu Hizmetleri çalıştırırken, düğüm üzerindeki toplu yönetilen dizinlerde bulunan dosyalarda dosya kilitleri almalıdır, aksi takdirde toplu Işlem dosya kilitleri nedeniyle bu dizinlerin silinmesine neden olur. Örneğin, bir Windows hizmetini başlangıç görevinde yüklüyorsanız, hizmeti doğrudan başlangıç görevi çalışma dizininden başlatmak yerine, dosyaları başka bir yere kopyalayın (veya dosyalar varsa kopyayı atlayın). Ardından bu konumdan hizmeti yüklersiniz. Batch, başlangıç görevinizi yeniden çalıştırdığınızda, başlangıç görevi çalışma dizinini siler ve yeniden oluşturur. Bu, hizmetin başlangıç görevi çalışma dizinini değil diğer dizinde dosya kilitleri olduğu için geçerlidir.

### <a name="avoid-creating-directory-junctions-in-windows"></a>Windows 'da Dizin geçişlerini oluşturmaktan kaçının

Bazen Dizin sabit bağlantıları olarak adlandırılan dizin junler, görev ve iş temizliği sırasında uğraşmak zordur. Sabit bağlantılar yerine çözümlemeyin (geçici bağlantılar) kullanın.

### <a name="collect-the-batch-agent-logs"></a>Batch Aracısı günlüklerini toplayın

Düğüm üzerinde çalışan bir düğümün veya görevlerin davranışını içeren bir sorun fark ederseniz, söz konusu düğümlerin ayırmayı kaldırma işleminden önce Batch Aracısı günlüklerini toplayın. Batch Aracısı günlükleri, Batch Hizmeti günlüklerini karşıya yükle API 'SI kullanılarak toplanabilir. Bu Günlükler, Microsoft 'a destek bileti kapsamında sağlanabilir ve sorun giderme ve çözümleme konularında yardımcı olur.

### <a name="manage-os-upgrades"></a>İşletim sistemi yükseltmelerini yönetme

Kullanıcı aboneliği modu Batch hesaplarında, özellikle görevler uzun süre çalışıyorsa, otomatik işletim sistemi yükseltmeleri görev ilerlemesini kesintiye uğratabilir. [Idempotent görevlerinin oluşturulması](#build-durable-tasks) , bu kesintiler nedeniyle oluşan hataları azaltmaya yardımcı olabilir. Ayrıca, [görevlerin çalıştırılmamakta olması beklenmediği durumlar için işletim sistemi görüntüsü yükseltmelerini zamanlamayı](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#manually-trigger-os-image-upgrades)öneririz.

Windows havuzları için `enableAutomaticUpdates` Varsayılan olarak olarak ayarlanır `true` . Otomatik güncelleştirmelere izin verilmesi önerilir, ancak `false` bir işletim sistemi güncelleştirmesinin beklenmedik şekilde gerçekleşmediğinden emin olmanız gerekiyorsa bu değeri olarak ayarlayabilirsiniz.

## <a name="isolation-security"></a>Yalıtım güvenliği

Yalıtım amaçları doğrultusunda, senaryonuz işlerin birbirinden yalıtılması gerekiyorsa, bunları ayrı havuzlarda bulundurarak bunu yapın. Havuz, toplu Işteki güvenlik yalıtımı sınırıdır ve varsayılan olarak, iki havuz görünür değildir veya birbirleriyle iletişim kuramaz. Yalıtım yöntemi olarak ayrı Batch hesapları kullanmaktan kaçının.

## <a name="moving-batch-accounts-across-regions"></a>Toplu hesapları bölgeler arasında taşıma

Mevcut bir Batch hesabını bir bölgeden diğerine taşımak yararlı olabilecek senaryolar vardır. Örneğin, olağanüstü durum kurtarma planlamasının bir parçası olarak başka bir bölgeye geçmek isteyebilirsiniz.

Azure Batch hesapları doğrudan bir bölgeden diğerine taşınamaz. Ancak, Batch hesabınızın mevcut yapılandırmasını dışarı aktarmak için bir Azure Resource Manager şablonu kullanabilirsiniz. Daha sonra, Batch hesabını bir şablona dışarı aktararak, hedef bölgeyle eşleşecek parametreleri değiştirerek ve sonra şablonu yeni bölgeye dağıtarak kaynağı başka bir bölgede oluşturabilirsiniz.

Şablonu yeni bölgeye yükledikten sonra sertifikaların, iş zamanlamalarının ve uygulama paketlerinin yeniden oluşturulması gerekir. Değişiklikleri uygulamak ve Batch hesabını taşımayı tamamlamak için, özgün Batch hesabını veya kaynak grubunu silmeyi unutmayın.

Kaynak Yöneticisi ve şablonlar hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Resource Manager şablonları oluşturma ve dağıtma](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="connectivity"></a>Bağlantı

Batch çözümlerinizde bağlantı ile ilgili aşağıdaki kılavuzu gözden geçirin.

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Ağ güvenlik grupları (NSG 'ler) ve Kullanıcı tanımlı yollar (UDRs)

[Bir sanal ağda Batch havuzlarını](batch-virtual-network.md)sağlarken, `BatchNodeManagement` hizmet etiketi, bağlantı noktaları, protokoller ve kuralın kullanımıyla ilgili yönergeleri yakından takip edin. Hizmet etiketinin kullanımı, temel alınan Batch hizmeti IP adresleri yerine kullanılması önemle önerilir. Bunun nedeni, IP adreslerinin zaman içinde değiştirebileceğinden kaynaklanır. Batch hizmeti IP adreslerini doğrudan kullanmak, Batch havuzlarınız için kararsızlığa, kesintilere veya kesintilere neden olabilir.

Kullanıcı tanımlı yollar (UDRs) için, bu adresler zaman içinde değişdiklerinden, Batch hizmeti IP adreslerini düzenli aralıklarla bir şekilde güncelleştirmek için bir işlem yapıldığından emin olun. Batch hizmeti IP adresleri listesini edinme hakkında bilgi edinmek için bkz. [Şirket Içi hizmet etiketleri](../virtual-network/service-tags-overview.md). Batch hizmeti IP adresleri, `BatchNodeManagement` hizmet etiketiyle (veya Batch hesabı bölgesiyle eşleşen bölgesel değişkenle) ilişkilendirilir.

### <a name="honoring-dns"></a>DNS 'i manlama

Sistemlerinizin Batch hesabı hizmet URL 'niz için DNS yaşam süresi (TTL) olduğundan emin olun. Ayrıca, Batch hizmeti istemcilerinizin ve Batch hizmetine yönelik diğer bağlantı mekanizmalarının IP adreslerine dayanmadığından emin olun (veya aşağıda açıklandığı gibi [statik genel IP adreslerine sahip bir havuz oluşturun](create-pool-public-ip.md) ).

İstekleriniz 5 xx düzeyinde HTTP yanıtı alıyorsa ve yanıtta bir "bağlantı: kapatma" üst bilgisi varsa, Batch hizmeti istemciniz var olan bağlantıyı kapatarak öneriyi gözlemlemelidir, Batch hesabı hizmet URL 'SI için DNS 'i yeniden çözümleyip yeni bir bağlantı isteği gerçekleştirmeye devam etmelidir.

### <a name="retry-requests-automatically"></a>İstekleri otomatik olarak yeniden dene

Batch hizmeti istemcilerinizde, normal işlem sırasında bile isteklerinizi otomatik olarak yeniden denemek için uygun yeniden deneme ilkelerine sahip olduğundan emin olun. Bu yeniden deneme ilkeleri, en az 5 dakikalık bir aralığı kapsamalıdır. Otomatik yeniden deneme özellikleri, [.net RetryPolicyProvider sınıfı](/dotnet/api/microsoft.azure.batch.retrypolicyprovider)gibi çeşitli Batch SDK 'leriyle sağlanır.

### <a name="static-public-ip-addresses"></a>Statik genel IP adresleri

Genellikle, bir Batch havuzundaki sanal makinelere, havuzun kullanım ömrü boyunca değişebilir genel IP adresleri üzerinden erişilir. Bu, belirli IP adreslerine erişimi sınırlayan bir veritabanı veya başka bir dış hizmetle etkileşim kurmayı zorlaştırır. Havuzunuzdaki genel IP adreslerinin beklenmedik şekilde değişmemesini sağlamak için, denetlediğiniz bir statik genel IP adresleri kümesini kullanarak bir havuz oluşturabilirsiniz. Daha fazla bilgi için bkz. [belirtilen genel IP adreslerine sahip bir Azure Batch havuzu oluşturma](create-pool-public-ip.md).

### <a name="testing-connectivity-with-cloud-services-configuration"></a>Cloud Services yapılandırmasıyla bağlantı test ediliyor

Azure Yük dengeleyicisinden ıCMP protokolüne izin verilmediğinden, normal "ping"/ıCMP protokolünü Cloud Services ile kullanamazsınız. Daha fazla bilgi için bkz. [Azure Cloud Services Için bağlantı ve ağ](../cloud-services/cloud-services-connectivity-and-networking-faq.md#can-i-ping-a-cloud-service).

## <a name="batch-node-underlying-dependencies"></a>Toplu düğüm temel alınan bağımlılıklar

Batch çözümlerinizi tasarlarken aşağıdaki bağımlılıkları ve kısıtlamaları göz önünde bulundurun.

### <a name="system-created-resources"></a>Sistem tarafından oluşturulan kaynaklar

Azure Batch, sanal makinede değiştirilmemelidir bir Kullanıcı ve grup kümesi oluşturur ve yönetir. Bunlar şu şekildedir:

Windows:

- **Poolnonadmin** adlı Kullanıcı
- **Wataskcommon** adlı bir Kullanıcı grubu

Linux:

- **_Azbatch** adlı bir Kullanıcı

### <a name="file-cleanup"></a>Dosya temizleme

Toplu işlem, saklama süresi sona erdiğinde görevlerin çalıştırıldığı çalışma dizinini etkin bir şekilde temizlemeye çalışır. Bu dizinin dışında yazılan tüm dosyalar, disk alanını doldurmaktan kaçınmak için [Temizleme sorumluluğudur](#manage-task-lifetime) .

Windows üzerinde bir hizmeti, startTask çalışma dizininden hala kullanımda olması nedeniyle çalıştırırsanız, çalışma dizini için otomatik temizleme engellenir. Bu, performansın düşmesine neden olur. Bunu yapmak için, bu hizmetin dizinini Batch tarafından yönetilmeyen ayrı bir dizinle değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

- [Batch hizmeti iş akışı ve](batch-service-workflow-features.md) havuzlar, düğümler, işler ve görevler gibi birincil kaynaklar hakkında bilgi edinin.
- [Varsayılan Azure Batch kotaları, sınırları ve kısıtlamaları hakkında bilgi edinin ve kota artışlarının nasıl isteneceğini](batch-quota-limit.md)öğrenin.
- [Havuz ve düğüm arka plan işlemlerinde oluşan sorunları algılamaya ve önlemenize ](batch-pool-node-error-checking.md)öğrenin.
