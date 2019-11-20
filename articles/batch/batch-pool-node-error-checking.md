---
title: Havuz ve düğüm hatalarını denetleme-Azure Batch
description: Havuzları ve düğümleri oluştururken denetlenecek hatalar ve bunların nasıl önleneceğini
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: 3c8e189e84e0a467125995b3e2d633c285eb7367
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350054"
---
# <a name="check-for-pool-and-node-errors"></a>Havuz ve düğüm hatalarını denetle

Azure Batch havuzları oluştururken ve yönetirken, bazı işlemler hemen gerçekleşir. Ancak bazı işlemler zaman uyumsuzdur ve arka planda çalışır ve birkaç dakika sürer.

API, CLı veya Kullanıcı arabirimi tarafından hemen herhangi bir başarısızlık döndürüldüğünden, hemen yapılacak işlemler için hataların algılanması basittir.

Bu makalede havuzlar ve havuz düğümleri için gerçekleşebileceğini arka plan işlemleri ele alınmaktadır. Bu, hatalardan nasıl algılanacağını ve kaçınmanızı belirler.

## <a name="pool-errors"></a>Havuz hataları

### <a name="resize-timeout-or-failure"></a>Zaman aşımını yeniden boyutlandır veya hata

Yeni bir havuz oluştururken veya var olan bir havuzu yeniden boyutlandırdığınızda, hedef düğüm sayısını belirtirsiniz.  Oluşturma veya yeniden boyutlandırma işlemi hemen tamamlanır, ancak yeni düğümlerin gerçek ayırması veya mevcut düğümlerin kaldırılması birkaç dakika sürebilir.  Yeniden boyutlandırma zaman aşımını [oluşturma](https://docs.microsoft.com/rest/api/batchservice/pool/add) veya [yeniden boyutlandırma](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API 'sinde belirtirsiniz. Yığın yeniden boyutlandırma zaman aşımı süresi boyunca düğüm hedef sayısını edinemez havuz kararlı duruma geçer ve raporların yeniden boyutlandırılması hataları görüntülenir.

En son değerlendirme için [Resizeerror](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) özelliği oluşan hataları listeler.

Yeniden boyutlandırma hatalarının yaygın nedenleri şunlardır:

- Yeniden boyutlandırma zaman aşımı çok kısa
  - Çoğu durumda, varsayılan 15 dakikalık zaman aşımı, havuz düğümlerinin ayrılması veya kaldırılması için yeterince uzun olur.
  - Çok sayıda düğüm ayırdıysanız, yeniden boyutlandırma zaman aşımını 30 dakikaya ayarlamayı öneririz. Örneğin, bir Azure Marketi görüntüsünden 1.000 ' den fazla düğüme veya özel bir VM görüntüsünden 300 ' den fazla düğüme yeniden boyutlandırdığınızda.
- Yetersiz çekirdek kotası
  - Bir Batch hesabı, tüm havuzlarda ayırabilecek çekirdek sayısıyla sınırlıdır. Toplu işlem, kotaya ulaşıldığında düğüm ayırmayı durduruyor. Toplu Işlemin daha fazla düğüm ayırabilmesi için çekirdek kotayı [artırabilirsiniz](https://docs.microsoft.com/azure/batch/batch-quota-limit) .
- Bir [havuz bir sanal ağda olduğunda](https://docs.microsoft.com/azure/batch/batch-virtual-network) yetersiz alt ağ IP 'leri
  - Bir sanal ağ alt ağı, istenen her havuz düğümüne ayrılacak yeterli sayıda atanmamış IP adresine sahip olmalıdır. Aksi takdirde düğümler oluşturulamaz.
- Bir [havuz bir sanal ağda olduğunda](https://docs.microsoft.com/azure/batch/batch-virtual-network) yetersiz kaynak
  - Batch hesabıyla aynı abonelikte yük dengeleyiciler, genel IP 'Ler ve ağ güvenlik grupları gibi kaynaklar oluşturabilirsiniz. Abonelik kotalarının bu kaynaklar için yeterli olup olmadığını denetleyin.
- Özel VM görüntüleri içeren büyük havuzlar
  - Özel VM görüntülerini kullanan büyük havuzların ayrılması ve yeniden boyutlandırılması daha uzun sürebilir.  Sınırlara ve yapılandırmaya yönelik öneriler için bkz. [paylaşılan görüntü Galerisi ile havuz oluşturma](batch-sig-images.md) .

### <a name="automatic-scaling-failures"></a>Otomatik ölçeklendirme sorunları

Ayrıca, bir havuzdaki düğümlerin sayısını otomatik olarak ölçeklendirmek için Azure Batch ayarlayabilirsiniz. [Bir havuz için otomatik ölçeklendirme formülünün](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)parametrelerini tanımlarsınız. Batch hizmeti, havuzdaki düğümlerin sayısını düzenli aralıklarla değerlendirmek ve yeni bir hedef numara ayarlamak için formülünü kullanır. Aşağıdaki tür sorunlar oluşabilir:

- Otomatik ölçeklendirme değerlendirmesi başarısız olur.
- Elde edilen yeniden boyutlandırma işlemi başarısız olur ve zaman aşımına uğrar.
- Otomatik ölçeklendirme formülüyle ilgili bir sorun, yanlış düğüm hedef değerlerine yol açar. Yeniden boyutlandırma, çalışma veya zaman aşımına uğruyor.

Otomatik ölçeklendirme değerlendirmesi hakkında daha fazla bilgi edinmek için otomatik [Scalerun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) özelliğini kullanın. Bu özellik değerlendirme süresini, değerleri ve sonucu ve tüm performans hatalarını bildirir.

[Havuz yeniden boyutlandırma Tamam olayı](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) , Tüm değerlendirmelere ilişkin bilgileri yakalar.

### <a name="delete"></a>Sil

Düğüm içeren bir havuzu sildiğinizde, ilk toplu Işlem düğümleri siler. Daha sonra havuz nesnesinin kendisini siler. Havuz düğümlerinin silinmesi birkaç dakika sürebilir.

Toplu işlem **, silme işlemi sırasında** [havuzun durumunu](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) silinmek üzere ayarlar. Çağıran uygulama, **durum** ve **Stateattiontime** özelliklerini kullanarak havuz silmenin çok uzun sürdüğünü algılayabilir.

## <a name="pool-compute-node-errors"></a>Havuz işlem düğümü hataları

Toplu Işlem, bir havuzdaki düğümleri başarıyla ayırdığında bile çeşitli sorunlar bazı düğümlerin sağlıksız olmasına ve görevleri çalıştıramamasına neden olabilir. Bu düğümler ücretlendirmeye devam etmektedir, bu nedenle kullanılamayan düğümler için ödeme yapmaktan kaçınmak için sorunları tespit etmek önemlidir. Yaygın düğüm hatalarına ek olarak, geçerli [iş durumunu](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate) bilmek sorun giderme için yararlıdır.

### <a name="start-task-failures"></a>Başlangıç görevi arızaları

Bir havuz için isteğe bağlı bir [Başlangıç görevi](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) belirtmek isteyebilirsiniz. Herhangi bir görevde olduğu gibi, depolama alanından indirmek için bir komut satırı ve kaynak dosyalarını kullanabilirsiniz. Başlangıç görevi, başlatıldıktan sonra her düğüm için çalıştırılır. **Waitforsuccess** özelliği, bir düğümdeki herhangi bir görevi zamanlamasından önce, toplu işin başlangıç görevinin başarıyla tamamlanmasını bekleyip beklemediğini belirtir.

Düğümü başarıyla başlangıç görevinin tamamlanmasını bekleyecek şekilde yapılandırdıysanız, ancak başlangıç görevi başarısız olur mu? Bu durumda, düğüm kullanılamaz, ancak ücretlendirmeye devam edecektir.

En üst düzey [Starttaskınfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) düğüm özelliğinin [Result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) ve [failureınfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) özelliklerini kullanarak başlangıç görevi başarısızlıklarını tespit edebilirsiniz.

**Waitforsuccess** **değeri true**olarak ayarlandıysa, başarısız bir başlangıç görevi, yığın [durumunun](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) **starttaskfailed** olarak ayarlanmasına neden olur.

Herhangi bir görevde olduğu gibi, başlangıç görevinin başarısız olmasının birçok nedeni olabilir.  Sorun gidermek için stdout, stderr ve görevlere özgü diğer günlük dosyalarını denetleyin.

Başlangıç görevi, aynı düğümde birden çok kez çalıştırıldığı için, başlangıç görevleri yeniden entrant olmalıdır; başlangıç görevi, bir düğüm yeniden görüntüsü oluşturulduğunda veya yeniden başlatıldığında çalıştırılır. Nadir durumlarda, bir olay yeniden başlatmaya neden olduktan sonra bir başlangıç görevi çalıştırılır. Bu, işletim sistemi veya kısa ömürlü disklerden birinin diğeri olmadığı sırada yeniden yansıma olduğu durumdur. Batch başlatma görevleri (tüm Batch görevleri gibi) geçici diskten çalıştırıldıklarından, bu normalde bir sorun değildir, ancak başlangıç görevinin işletim sistemi diskine bir uygulama yüklendiği ve diğer verileri geçici diskte tutarak bu durum, bu nedenle işlemler eşitlenmemiş olduğundan sorunlar. Her iki disk kullanıyorsanız uygulamanızı uygun şekilde koruyun.

### <a name="application-package-download-failure"></a>Uygulama paketi indirme hatası

Bir havuz için bir veya daha fazla uygulama paketi belirtebilirsiniz. Batch, belirtilen paket dosyalarını her bir düğüme indirir ve düğüm başladıktan sonra, ancak görevler zamanlandıktan sonra dosyaların sıkıştırmasını kaldırır. Uygulama paketleriyle birlikte başlangıç görevi komut satırı kullanılması yaygındır. Örneğin, dosyaları farklı bir konuma kopyalamak veya kurulumu çalıştırmak için.

Node [Errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) özelliği bir uygulama paketini indirip sıkıştırmak için bir hata bildiriyor; düğüm durumu **kullanılamaz**olarak ayarlanır.

### <a name="container-download-failure"></a>Kapsayıcı indirme hatası

Bir havuzda bir veya daha fazla kapsayıcı başvurusu belirtebilirsiniz. Batch, belirtilen kapsayıcıları her bir düğüme indirir. Node [Errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) özelliği bir kapsayıcıyı indirme hatası bildiriyor ve düğüm durumunu **kullanılamaz**olarak ayarlar.

### <a name="node-in-unusable-state"></a>Kullanılamayan durumda düğüm

Azure Batch, [düğüm durumunu](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) birçok nedenden dolayı **kullanılamaz** olarak ayarlayabilir. Düğüm durumunun kullanım **dışı olarak ayarlandığı, görevler**düğüme zamanlanamaz ancak ücretlendirmeye devam eder.

**Kullanılamaz** durumdaki düğümler, ancak [hata](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) olmadan, toplu iş VM ile iletişim kuramayan anlamına gelir. Bu durumda Batch her zaman VM 'yi kurtarmaya çalışır. Toplu işlem, durumları **kullanılamaz**olsa bile uygulama paketlerini veya kapsayıcıları yükleyememiş VM 'leri otomatik olarak kurtarmaya çalışmaz.

Batch, nedeni tespit edebilir, düğüm [hataları](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) özelliği bunu raporlar.

**Kullanılamayan** düğümlere yönelik ek örneklere örnek olarak şunlar verilebilir:

- Özel bir VM görüntüsü geçersiz. Örneğin, düzgün şekilde hazırlanmayan bir görüntü.

- Bir altyapı arızası veya alt düzey yükseltme nedeniyle VM taşınır. Batch, düğümü kurtarır.

- Bir VM görüntüsü onu desteklemeyen bir donanımda dağıtıldı. Örneğin, bir [Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series) sanal makinesinde CENTOS HPC görüntüsü çalıştırmaya çalışılıyor.

- VM 'Ler bir [Azure sanal ağında](batch-virtual-network.md)ve trafik anahtar bağlantı noktalarına engellenmiştir.

- VM 'Ler bir sanal ağda bulunur, ancak Azure depolama 'ya giden trafik engellenir.

- VM 'Ler, müşteri DNS yapılandırmasına sahip bir sanal ağda ve DNS sunucusu Azure Storage 'ı çözümleyemiyor.

### <a name="node-agent-log-files"></a>Düğüm Aracısı günlük dosyaları

Her havuz düğümünde çalışan Batch Aracısı işlemi, havuz düğümü sorunu hakkında desteğe başvurmanız gerekirse yararlı olabilecek günlük dosyaları sağlayabilir. Bir düğümün günlük dosyaları Azure portal, Batch Explorer veya bir [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs)aracılığıyla karşıya yüklenebilir. Günlük dosyalarını karşıya yüklemek ve kaydetmek yararlı olur. Daha sonra, çalışan düğümlerin maliyetini kaydetmek için düğümü veya havuzu silebilirsiniz.

### <a name="node-disk-full"></a>Düğüm diski dolu

Havuz düğümü VM 'si için geçici sürücü iş dosyaları, görev dosyaları ve paylaşılan dosyalar için Batch tarafından kullanılır.

- Uygulama paketleri dosyaları
- Görev kaynak dosyaları
- Toplu Iş klasörlerinden birine indirilen uygulamaya özgü dosyalar
- Her görev uygulaması yürütmesi için stdout ve stderr dosyaları
- Uygulamaya özel çıkış dosyaları

Bu dosyalardan bazıları yalnızca havuz düğümleri oluşturulduğunda (havuz uygulama paketleri veya havuz başlangıç görevi kaynak dosyaları gibi) bir kez yazılır. Düğüm oluşturulduğunda yalnızca bir kez yazılmışsa bile, bu dosyalar geçici sürücüyü doldurabilirler.

Stdout ve stderr gibi bir düğümde çalıştırılan her görev için diğer dosyalar yazılır. Aynı düğüm üzerinde çok sayıda görev çalışıyorsa ve/veya görev dosyaları çok büyükse, geçici sürücüyü doldurabilir.

Geçici sürücünün boyutu VM boyutuna bağlıdır. Bir VM boyutu seçerken, geçici sürücüde yeterli alan olduğundan emin olmak önemlidir.

- Havuz eklenirken Azure portal, VM boyutlarının tam listesi görüntülenebilir ve ' kaynak disk boyutu ' sütunu vardır.
- Tüm VM boyutlarını açıklayan makalelerde ' Temp Storage ' sütunu olan tablolar vardır; Örneğin, [işlem Için IYILEŞTIRILMIŞ VM boyutları](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute)

Her bir görev tarafından yazılan dosyalar için, görev dosyalarının otomatik olarak temizlenmeden önce ne kadar süreyle tutulacağını belirleyen her bir görev için bir bekletme süresi belirtilebilir. Saklama süresi, depolama gereksinimlerini düşürmek için azaltılabilir.

Geçici disk alanı doldurulabiliyor, şu anda düğüm çalışan görevleri durdurur. Gelecekte bir [düğüm hatası](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) bildirilir.


## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızı, özellikle zaman uyumsuz işlemler için kapsamlı hata denetimi uygulayacak şekilde ayarlamış olun. Sorunları saptamak ve tanılamak için kritik öneme sahip olabilir.
