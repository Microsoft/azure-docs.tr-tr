---
title: Havuz ve düğüm hatalarını denetle
description: Bu makalede, ve havuzları ve düğümleri oluştururken bunların nasıl önleneceğini denetlemek için hatalarla birlikte gerçekleşebileceğini gösteren arka plan işlemleri ele alınmaktadır.
author: mscurrell
ms.author: markscu
ms.date: 03/15/2021
ms.topic: how-to
ms.openlocfilehash: 86ea4ce4d596875e455d7b86250882713a14337f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720160"
---
# <a name="check-for-pool-and-node-errors"></a>Havuz ve düğüm hatalarını denetle

Azure Batch havuzları oluştururken ve yönetirken, bazı işlemler hemen gerçekleşir. Bu işlemler için hataların algılanması genellikle basittir, çünkü bunlar API, CLı veya Kullanıcı arabirimi tarafından hemen döndürülür. Ancak bazı işlemler zaman uyumsuzdur ve arka planda çalışır ve birkaç dakika sürer.

Uygulamalarınızı, özellikle zaman uyumsuz işlemler için kapsamlı hata denetimi uygulayacak şekilde ayarlamış olduğumdan emin olun. Bu, sorunları hemen tanımanıza ve tanılamanıza yardımcı olabilir.

Bu makalede havuzlar ve havuz düğümleri için gerçekleşebileceğini arka plan işlemlerinde oluşan sorunları tespit etmek ve önlemek için yollar açıklanmaktadır.

## <a name="pool-errors"></a>Havuz hataları

### <a name="resize-timeout-or-failure"></a>Zaman aşımını yeniden boyutlandır veya hata

Yeni bir havuz oluştururken veya var olan bir havuzu yeniden boyutlandırdığınızda, hedef düğüm sayısını belirtirsiniz. Oluşturma veya yeniden boyutlandırma işlemi hemen tamamlanır, ancak yeni düğümlerin gerçek ayırması veya mevcut düğümlerin kaldırılması birkaç dakika sürebilir. Yeniden boyutlandırma zaman aşımını [oluşturma](/rest/api/batchservice/pool/add) veya [yeniden boyutlandırma](/rest/api/batchservice/pool/resize) API 'sinde belirtebilirsiniz. Yığın, yeniden boyutlandırma zaman aşımı süresi boyunca hedef düğüm sayısını alamazlarsa, havuz kararlı duruma geçer ve raporların yeniden boyutlandırılması hataları görüntülenir.

En son değerlendirme için [Resizeerror](/rest/api/batchservice/pool/get#resizeerror) özelliği oluşan hataları listeler.

Yeniden boyutlandırma hatalarının yaygın nedenleri şunlardır:

- Yeniden boyutlandırma zaman aşımı çok kısa
  - Çoğu durumda, varsayılan 15 dakikalık zaman aşımı, havuz düğümlerinin ayrılması veya kaldırılması için yeterince uzun olur.
  - Çok sayıda düğüm ayırdıysanız, yeniden boyutlandırma zaman aşımını 30 dakikaya ayarlamayı öneririz. Örneğin, bir Azure Marketi görüntüsünden 1.000 ' den fazla düğüme veya özel bir VM görüntüsünden 300 ' den fazla düğüme yeniden boyutlandırdığınızda.
- Yetersiz çekirdek kotası
  - Bir Batch hesabı, tüm havuzlarda ayırabilecek çekirdek sayısıyla sınırlıdır. Toplu işlem, kotaya ulaşıldığında düğüm ayırmayı durduruyor. Toplu Işlemin daha fazla düğüm ayırabilmesi için çekirdek kotayı [artırabilirsiniz](./batch-quota-limit.md) .
- Bir [havuz bir sanal ağda olduğunda](./batch-virtual-network.md) yetersiz alt ağ IP 'leri
  - Bir sanal ağ alt ağı, istenen her havuz düğümüne ayrılacak yeterli sayıda atanmamış IP adresine sahip olmalıdır. Aksi takdirde düğümler oluşturulamaz.
- Bir [havuz bir sanal ağda olduğunda](./batch-virtual-network.md) yetersiz kaynak
  - Batch hesabıyla aynı abonelikte yük dengeleyiciler, genel IP 'Ler ve ağ güvenlik grupları gibi kaynaklar oluşturabilirsiniz. Abonelik kotalarının bu kaynaklar için yeterli olup olmadığını denetleyin.
- Özel VM görüntüleri içeren büyük havuzlar
  - Özel VM görüntülerini kullanan büyük havuzların ayrılması ve yeniden boyutlandırılması daha uzun sürebilir.  Sınırlara ve yapılandırmaya yönelik öneriler için bkz. [paylaşılan görüntü Galerisi ile havuz oluşturma](batch-sig-images.md) .

### <a name="automatic-scaling-failures"></a>Otomatik ölçeklendirme sorunları

Bir havuzdaki düğümlerin sayısını otomatik olarak ölçeklendirmek için Azure Batch belirleyebilirsiniz. [Bir havuz için otomatik ölçeklendirme formülünün](./batch-automatic-scaling.md)parametrelerini tanımlarsınız. Batch hizmeti daha sonra, havuzdaki düğümlerin sayısını düzenli aralıklarla değerlendirmek ve yeni bir hedef numara ayarlamak için formülünü kullanacaktır.

Otomatik ölçeklendirme kullanılırken aşağıdaki tür sorunlar meydana gelebilir:

- Otomatik ölçeklendirme değerlendirmesi başarısız olur.
- Elde edilen yeniden boyutlandırma işlemi başarısız olur ve zaman aşımına uğrar.
- Otomatik ölçeklendirme formülüyle ilgili bir sorun, yanlış düğüm hedef değerlerine yol açar. Yeniden boyutlandırma, çalışma veya zaman aşımına uğruyor.

Son otomatik ölçeklendirme değerlendirmesi hakkında bilgi almak için, [Otomatik Scalerun](/rest/api/batchservice/pool/get#autoscalerun) özelliğini kullanın. Bu özellik değerlendirme süresini, değerleri ve sonucu ve tüm performans hatalarını bildirir.

[Havuz yeniden boyutlandırma Tamam olayı](./batch-pool-resize-complete-event.md) , Tüm değerlendirmelere ilişkin bilgileri yakalar.

### <a name="pool-deletion-failures"></a>Havuz silme sorunları

Düğüm içeren bir havuzu sildiğinizde, ilk toplu Işlem düğümleri siler. Bu işlemin tamamlanması birkaç dakika sürebilir. Bundan sonra Batch, havuz nesnesinin kendisini siler.

Toplu işlem **, silme işlemi sırasında** [havuzun durumunu](/rest/api/batchservice/pool/get#poolstate) silinmek üzere ayarlar. Çağıran uygulama, **durum** ve **Stateattiontime** özelliklerini kullanarak havuz silmenin çok uzun sürdüğünü algılayabilir.

Havuz beklenenden uzun sürüyorsa, havuz başarıyla silinene kadar Işlem düzenli aralıklarla yeniden dener. Bazı durumlarda, gecikme bir Azure hizmet kesintisi veya diğer geçici sorunlardan kaynaklanır. Bir havuzun başarıyla silinmesini engelleyebilen diğer faktörler, sorunu gidermek için işlem yapmanız gerekebilir. Bu etkenler şunları içerir:

- Kaynak kilitleri toplu olarak oluşturulan kaynaklara veya toplu Işlem tarafından kullanılan ağ kaynaklarına yerleştirildi.
- Oluşturduğunuz kaynakların, toplu olarak oluşturulan bir kaynağa bağımlılığı vardır. Örneğin, bir [Sanal ağda havuz oluşturursanız](batch-virtual-network.md)Batch bir ağ güvenlik grubu (NSG), genel bir IP adresi ve bir yük dengeleyici oluşturur. Bu kaynakları havuz dışında kullanırsanız, havuz bu bağımlılık kaldırılana kadar silinemez.
- Microsoft.Batch kaynak sağlayıcısı, havuzunuzu içeren abonelikte kaydı silindi.
- "Microsoft Azure Batch" artık, havuzunuzu içeren aboneliğe [katkıda bulunan veya sahip rolü](batch-account-create-portal.md#allow-azure-batch-to-access-the-subscription-one-time-operation) yok (Kullanıcı aboneliği modu Batch hesapları için).

## <a name="node-errors"></a>Düğüm hataları

Toplu Işlem, bir havuzdaki düğümleri başarıyla ayırdığında bile çeşitli sorunlar bazı düğümlerin sağlıksız olmasına ve görevleri çalıştıramamasına neden olabilir. Bu düğümler ücretlendirmeye devam etmektedir, bu nedenle kullanılamayan düğümler için ödeme yapmaktan kaçınmak için sorunları tespit etmek önemlidir. Yaygın düğüm hatalarına ek olarak, geçerli [iş durumunu](/rest/api/batchservice/job/get#jobstate) bilmek sorun giderme için yararlıdır.

### <a name="start-task-failures"></a>Başlangıç görevi arızaları

Bir havuz için isteğe bağlı bir [Başlangıç görevi](/rest/api/batchservice/pool/add#starttask) belirtmek isteyebilirsiniz. Herhangi bir görevde olduğu gibi, depolama alanından indirmek için bir komut satırı ve kaynak dosyalarını kullanabilirsiniz. Başlangıç görevi, başlatıldıktan sonra her düğüm için çalıştırılır. **Waitforsuccess** özelliği, bir düğümdeki herhangi bir görevi zamanlamasından önce, toplu işin başlangıç görevinin başarıyla tamamlanmasını bekleyip beklemediğini belirtir.

Düğümü başarıyla başlangıç görevinin tamamlanmasını bekleyecek şekilde yapılandırdıysanız, ancak başlangıç görevi başarısız olur mu? Bu durumda, düğüm kullanılamaz, ancak ücretlendirmeye devam edecektir.

En üst düzey [Starttaskınfo](/rest/api/batchservice/computenode/get#starttaskinformation) düğüm özelliğinin [Result](/rest/api/batchservice/computenode/get#taskexecutionresult) ve [failureınfo](/rest/api/batchservice/computenode/get#taskfailureinformation) özelliklerini kullanarak başlangıç görevi başarısızlıklarını tespit edebilirsiniz.

**Waitforsuccess** **değeri true** olarak ayarlandıysa, başarısız bir başlangıç görevi, yığın [durumunun](/rest/api/batchservice/computenode/get#computenodestate) **starttaskfailed** olarak ayarlanmasına neden olur.

Herhangi bir görevde olduğu gibi, bir başlangıç görevi hatasının pek çok nedeni olabilir. Sorun gidermek için stdout, stderr ve görevlere özgü diğer günlük dosyalarını denetleyin.

Başlangıç görevi, aynı düğümde birden çok kez çalıştırıldığı için, başlangıç görevleri yeniden entrant olmalıdır; başlangıç görevi, bir düğüm yeniden görüntüsü oluşturulduğunda veya yeniden başlatıldığında çalıştırılır. Nadir durumlarda, bir olay yeniden başlatmaya neden olduktan sonra bir başlangıç görevi çalıştırılır. Bu, işletim sistemi veya kısa ömürlü disklerden birinin diğeri olmadığı sırada yeniden yansıma olduğu durumdur. Batch başlatma görevleri (tüm Batch görevleri gibi) geçici diskten çalıştığından, bu normalde bir sorun değildir, ancak başlangıç görevinin işletim sistemi diskine bir uygulama yüklendiği ve diğer verileri geçici diskte tutarak, bu durum bazı durumlarda sorunlara neden olabilir. Her iki disk kullanıyorsanız uygulamanızı uygun şekilde koruyun.

### <a name="application-package-download-failure"></a>Uygulama paketi indirme hatası

Bir havuz için bir veya daha fazla uygulama paketi belirtebilirsiniz. Batch, belirtilen paket dosyalarını her bir düğüme indirir ve düğüm başladıktan sonra, ancak görevler zamanlandıktan sonra dosyaların sıkıştırmasını kaldırır. Uygulama paketleriyle birlikte başlangıç görevi komut satırı kullanılması yaygındır. Örneğin, dosyaları farklı bir konuma kopyalamak veya kurulumu çalıştırmak için.

Node [Errors](/rest/api/batchservice/computenode/get#computenodeerror) özelliği bir uygulama paketini indirip sıkıştırmak için bir hata bildiriyor; düğüm durumu **kullanılamaz** olarak ayarlanır.

### <a name="container-download-failure"></a>Kapsayıcı indirme hatası

Bir havuzda bir veya daha fazla kapsayıcı başvurusu belirtebilirsiniz. Batch, belirtilen kapsayıcıları her bir düğüme indirir. Node [Errors](/rest/api/batchservice/computenode/get#computenodeerror) özelliği bir kapsayıcıyı indirme hatası bildiriyor ve düğüm durumunu **kullanılamaz** olarak ayarlar.

### <a name="node-os-updates"></a>Düğüm işletim sistemi güncelleştirmeleri

Windows havuzları için `enableAutomaticUpdates` Varsayılan olarak olarak ayarlanır `true` . Otomatik güncelleştirmelere izin verilmesi önerilir, ancak özellikle görevler uzun süre çalışıyorsa, görev ilerlemesini kesintiye uğrabilirler. `false`Bir işletim sistemi güncelleştirmesinin beklenmedik bir şekilde gerçekleşmediğinden emin olmanız gerekiyorsa, bu değeri olarak ayarlayabilirsiniz.

### <a name="node-in-unusable-state"></a>Kullanılamayan durumda düğüm

Azure Batch, [düğüm durumunu](/rest/api/batchservice/computenode/get#computenodestate) birçok nedenden dolayı **kullanılamaz** olarak ayarlayabilir. Düğüm durumunun kullanım **dışı olarak ayarlandığı, görevler** düğüme zamanlanamaz ancak ücretlendirmeye devam eder.

**Kullanılamaz** durumdaki düğümler, ancak [hata](/rest/api/batchservice/computenode/get#computenodeerror) olmadan, toplu iş VM ile iletişim kuramayan anlamına gelir. Bu durumda Batch her zaman VM 'yi kurtarmaya çalışır. Toplu işlem, durumları **kullanılamaz** olsa bile uygulama paketlerini veya kapsayıcıları yükleyememiş VM 'leri otomatik olarak kurtarmaya çalışmaz.

Batch, nedeni tespit edebilir, düğüm [hataları](/rest/api/batchservice/computenode/get#computenodeerror) özelliği bunu raporlar.

**Kullanılamayan** düğümlere yönelik ek örneklere örnek olarak şunlar verilebilir:

- Özel bir VM görüntüsü geçersiz. Örneğin, düzgün şekilde hazırlanmayan bir görüntü.
- Bir altyapı arızası veya alt düzey yükseltme nedeniyle VM taşınır. Batch, düğümü kurtarır.
- Bir VM görüntüsü onu desteklemeyen bir donanımda dağıtıldı. Örneğin, bir [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) VM 'de CENTOS HPC görüntüsü çalıştırmaya çalışılıyor.
- VM 'Ler bir [Azure sanal ağında](batch-virtual-network.md)ve trafik anahtar bağlantı noktalarına engellenmiştir.
- VM 'Ler bir sanal ağda bulunur, ancak Azure depolama 'ya giden trafik engellenir.
- VM 'Ler, müşteri DNS yapılandırmasına sahip bir sanal ağda ve DNS sunucusu Azure Storage 'ı çözümleyemiyor.

### <a name="node-agent-log-files"></a>Düğüm Aracısı günlük dosyaları

Her havuz düğümünde çalışan Batch Aracısı işlemi, havuz düğümü sorunu hakkında desteğe başvurmanız gerekirse yararlı olabilecek günlük dosyaları sağlayabilir. Bir düğümün günlük dosyaları Azure portal, Batch Explorer veya bir [API](/rest/api/batchservice/computenode/uploadbatchservicelogs)aracılığıyla karşıya yüklenebilir. Günlük dosyalarını karşıya yüklemek ve kaydetmek yararlı olur. Daha sonra, çalışan düğümlerin maliyetini kaydetmek için düğümü veya havuzu silebilirsiniz.

### <a name="node-disk-full"></a>Düğüm diski dolu

Havuz düğümü sanal makinesi için geçici sürücü, aşağıdakiler gibi iş dosyaları, görev dosyaları ve paylaşılan dosyalar için Batch tarafından kullanılır:

- Uygulama paketleri dosyaları
- Görev kaynak dosyaları
- Toplu Iş klasörlerinden birine indirilen uygulamaya özgü dosyalar
- Her görev uygulaması yürütmesi için stdout ve stderr dosyaları
- Uygulamaya özel çıkış dosyaları

Bu dosyalardan bazıları yalnızca havuz düğümleri oluşturulduğunda (havuz uygulama paketleri veya havuz başlangıç görevi kaynak dosyaları gibi) bir kez yazılır. Düğüm oluşturulduğunda yalnızca bir kez yazılmışsa bile, bu dosyalar geçici sürücüyü doldurabilirler.

Stdout ve stderr gibi bir düğümde çalıştırılan her görev için diğer dosyalar yazılır. Aynı düğüm üzerinde çok sayıda görev çalışıyorsa ve/veya görev dosyaları çok büyükse, geçici sürücüyü doldurabilir.

Ayrıca, düğüm başladıktan sonra, Kullanıcı oluşturmak için işletim sistemi diskinde az miktarda alan gerekir.

Geçici sürücünün boyutu VM boyutuna bağlıdır. Bir VM boyutu seçerken bir göz önünde bulundurmanız, geçici sürücüde planlanmış iş yükü için yeterli alan olmasını sağlamaktır.

- Havuz eklenirken Azure portal, VM boyutlarının tam listesi görüntülenebilir ve ' kaynak disk boyutu ' sütunu vardır.
- Tüm VM boyutlarını açıklayan makalelerde ' Temp Storage ' sütunu olan tablolar vardır; Örneğin, [işlem Için IYILEŞTIRILMIŞ VM boyutları](../virtual-machines/sizes-compute.md)

Her bir görev tarafından yazılan dosyalar için, görev dosyalarının otomatik olarak temizlenmeden önce ne kadar süreyle tutulacağını belirleyen her bir görev için bir bekletme süresi belirtilebilir. Saklama süresi, depolama gereksinimlerini düşürmek için azaltılabilir.

Geçici ya da işletim sistemi diskinde boş alan [tükeniyor](/rest/api/batchservice/computenode/get#computenodestate) (veya alan tükendiğinden çok yakın), düğüm kullanılamaz duruma geçer ve diskin dolu olduğunu bildiren bir düğüm hatası bildirilir.

Düğüm üzerinde ne kadar alan olduğunu bilmiyorsanız, düğüm üzerinde uzaktan iletişim kurun ve alanın geçmiş olduğu konusunda el ile araştırma yapın. Batch tarafından yönetilen klasörlerdeki dosyaları incelemek için [toplu Işlem listesi dosyaları API](/rest/api/batchservice/file/listfromcomputenode) 'sini de kullanabilirsiniz (örneğin, görev çıkışları). Bu API 'nin yalnızca Batch yönetilen dizinlerindeki dosyaları listelediğinden emin olmanız gerektiğini unutmayın. Görevleriniz dosyaları başka bir yerde oluşturduysanız, bunları görmezsiniz.

İhtiyacınız olan tüm verilerin düğümden alındığından veya dayanıklı bir depoya yüklendiğinden emin olun, sonra alanı boşaltmak için verileri gereken şekilde silin.

Görev verileri hala düğümlerde olan eski tamamlanmış işleri veya eski tamamlanmış görevleri silebilirsiniz. Düğümdeki [Recenttasks koleksiyonuna](/rest/api/batchservice/computenode/get#taskinformation) veya [düğümdeki dosyalardaki](/rest/api/batchservice/file/listfromcomputenode)bölümüne bakın. Bir işin silinmesi işteki tüm görevleri siler; İşteki görevlerin silinmesi, düğümdeki görev dizinlerindeki verilerin silinmesine, böylece alan boşaltmasını sağlayacaktır. Yeterli alan boşaltdıktan sonra, düğümü yeniden başlatın ve "kullanılamaz" durumundan sonra "boşta" ' dan "boş" durumuna geçer.

[Virtualmachineconfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) havuzlarındaki kullanılamayan bir düğümü kurtarmak için [düğümleri kaldır API](/rest/api/batchservice/pool/removenodes)'sini kullanarak havuzdan bir düğümü kaldırabilirsiniz. Sonra, bozuk düğümü yeni bir düğüm ile değiştirmek için havuzu yeniden büyüyebilirsiniz. [Cloudserviceconfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) havuzları Için, [Batch yeniden görüntü API 'si](/rest/api/batchservice/computenode/reimage)aracılığıyla düğümü yeniden görüntüleyebilirsiniz. Bu, diskin tamamını temizler. [Virtualmachineconfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) havuzları için yeniden görüntü şu anda desteklenmiyor.

## <a name="next-steps"></a>Sonraki adımlar

- [İş ve görev hatası denetimi](batch-job-task-error-checking.md)hakkında bilgi edinin.
- Azure Batch ile çalışmaya yönelik [en iyi yöntemler](best-practices.md) hakkında bilgi edinin.
