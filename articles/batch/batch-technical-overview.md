---
title: Azure Batch, bulutta büyük paralel işler çalıştırır
description: Büyük ölçekli paralel ve HPC iş yükleri için Azure Batch hizmetini kullanma hakkında bilgi edinin
ms.topic: overview
ms.date: 07/30/2020
ms.openlocfilehash: dbd27dc1a00966a2d71952335cfb47c7ca55bc24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743109"
---
# <a name="what-is-azure-batch"></a>Azure Batch nedir?

Büyük ölçekli paralel ve yüksek performanslı bilgi işlem (HPC) toplu işlerini Azure’da verimli bir şekilde çalıştırmak için Azure Batch’i kullanın. Azure Batch bir işlem düğümleri (sanal makine) havuzu oluşturup yönetir, çalıştırmak istediğiniz uygulamaları yükler ve düğümler üzerinde çalışacak işleri zamanlar. Yüklemek, yönetmek veya ölçeklendirmek için küme veya iş Zamanlayıcı yazılımı yoktur. Bunun yerine, işlerinizi yapılandırmak, yönetmek ve izlemek için [Batch API ve araçları](batch-apis-tools.md), komut satırı betikleri veya Azure portalını kullanırsınız.

Geliştiriciler, büyük ölçekli yürütmenin gerekli olduğu SaaS uygulamalarını veya istemci uygulamaları derlemek için Batch’i bir platform hizmeti olarak kullanabilir. Örneğin, bir finans hizmetleri şirketi için bir Monte Carlo risk simülasyonu veya birçok görüntüyü işlemek üzere bir hizmeti çalıştırmak için Batch ile bir hizmet oluşturabilirsiniz.

Batch kullanımından ek ücret alınmaz. Yalnızca sanal makineler, depolama ve ağ gibi kullanılan temel kaynaklar için ödeme yaparsınız.

Azure 'da Batch ve diğer HPC çözüm seçenekleri arasında bir karşılaştırma için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).

## <a name="run-parallel-workloads"></a>Paralel iş yükleri çalıştırma

Batch, doğası gereği paralel ("utandırıcı derecede paralel" olarak da bilinir) iş yükleriyle düzgün çalışır. Doğası gereği paralel iş yükleri, her örnek iş parçasını tamamlamada bağımsız olarak çalışabilen uygulamalara sahiptir. Uygulamalar yürütülerek bazı ortak verilere erişebilirler, ancak uygulamanın diğer örnekleriyle iletişim kurmaz. Bu nedenle doğası gereği paralel iş yükleri, uygulamaları eşzamanlı çalıştırmak için kullanılabilen işlem kaynaklarının miktarına göre belirlenen büyük bir ölçekte çalışır.

Batch ile kullanabileceğiniz doğası gereği paralel iş yüklerinin bazı örnekleri şunlardır:

- Monte Carlo simülasyonlarını kullanarak finansal risk modelleme
- VFX ve 3B görüntü işleme
- Görüntü analizi ve işleme
- Medya kodlama dönüştürme
- Genetik dizi analizi
- Optik karakter tanıma (OCR)
- Veri alımı, işleme ve ETL işlemleri
- Yazılım testi yürütme

Batch 'i, çalıştırdığınız uygulamaların bağımsız olarak çalıştırmak yerine birbirleriyle iletişim kurması gereken [sıkı şekilde bağlanmış iş yüklerini çalıştırmak](batch-mpi.md)için de kullanabilirsiniz. Sıkıca bağlı uygulamalar normalde İleti Geçirme Arabirimi (MPI) API’sini kullanır. Sıkıca bağlı iş yüklerinizi, [Microsoft MPI](/message-passing-interface/microsoft-mpi) veya Intel MPI kullanarak Batch ile çalıştırabilirsiniz. Özel [HPC](../virtual-machines/sizes-hpc.md) ve [GPU bakımından iyileştirilmiş](../virtual-machines/sizes-gpu.md) VM boyutları ile uygulama performansını artırın.

Sıkıca bağlı iş yüklerinin bazı örnekleri şunlardır:

- Sonlu eleman analizi
- Sıvı dinamiği
- Çok düğümlü AI eğitimi

Sıkıca bağlı işlerin çoğu, Batch kullanılarak paralel olarak çalıştırılabilir. Örneğin, kanal genişlikleri değişen bir kanal içinden sıvı akışının birden fazla simülasyonunu gerçekleştirin.

## <a name="additional-batch-capabilities"></a>Ek Batch özellikleri

Azure Batch için daha yüksek düzeyli, iş yüküne özel özellikler de kullanılabilir:

- Batch; Autodesk Maya, 3ds Max, Arnold ve V-Ray gibi işleme araçları ile büyük ölçekli [işleme iş yüklerini](batch-rendering-service.md) destekler. 
- R kullanıcıları, Batch havuzlarında R algoritmalarının yürütülmesini kolayca ölçeklendirmek için [doAzureParallel R paketi](https://github.com/Azure/doAzureParallel) yükleyebilir.

[Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md) gibi araçlarla yönetilen Batch işlerini, veri dönüştürmeye yönelik daha büyük bir Azure iş akışının parçası olarak da kullanabilirsiniz.

## <a name="how-it-works"></a>Nasıl çalışır?

Batch için yaygın bir senaryo, işlem düğümlerinin havuzunda 3B görüntülerin işlenmesi gibi paralel işi aslında ölçeklendirmeyi kapsar. Bu havuz, işleme işinize onlarca, yüzlerce, hatta binlerce çekirdek sağlayan "oluşturma grubunuz" olabilir.

Aşağıdaki diyagramda, istemci uygulamasının yanı sıra paralel iş yükünü çalıştıracak Batch’i kullanan barındırma hizmetiyle birlikte yaygın bir Batch iş akışının adımları gösterilmektedir.

![Batch çözümündeki adımların diyagramı.](./media/batch-technical-overview/tech_overview_03.png)

|Adım  |Description  |
|---------|---------|
|1. bu dosyaları Azure depolama hesabınıza işlemek için **giriş dosyalarını** ve **uygulamaları** karşıya yükleyin.     |Giriş dosyaları uygulamanızın işleyeceği herhangi bir veri olabilir; örneğin, finansal modelleme verileri veya dönüştürülecek video dosyaları. Uygulama dosyaları, medya kod dönüştürücüsü gibi veri işleyen betik ya da uygulamaları içerebilir.|
|2. Batch hesabınızda işlem düğümleri için bir Batch **havuzu** , havuzda iş yükünü çalıştırmak için bir **iş** ve işteki **Görevler** oluşturun.     | [İşlem düğümleri](nodes-and-pools.md) , [görevlerinizi](jobs-and-tasks.md)yürütecek sanal makinelerlerdir. Havuzlarınızın sayısı ve boyutu, bir Windows veya Linux VM görüntüsü ve düğümler havuza katılırsanız yüklenecek bir uygulama gibi özellikleri belirtin. [Düşük öncelikli VM 'leri](batch-low-pri-vms.md) kullanarak veya iş yükü değiştikçe düğümlerin sayısını [otomatik olarak ölçeklendirerek](batch-automatic-scaling.md) , havuzun maliyetini ve boyutunu yönetin. <br/><br/>Bir işe görev eklediğinizde, Batch hizmeti havuzundaki işlem düğümlerinde yürütülmesi için görevleri otomatik olarak zamanlar. Her görev, girdi dosyalarını işlemek için yüklediğiniz uygulamayı kullanır. |
|3. **giriş dosyalarını** ve **uygulamaları** toplu işe indirin     |Her görev yürütülmeden önce, atanan düğüme işlem yaptığı giriş verilerini indirebilir. Uygulama henüz havuz düğümlerine yüklenmediyse, burada da indirilebilir. Azure Depolama’dan indirme işlemleri tamamlandığında, görev atanan düğüm üzerinde yürütülür.|
|4. **görev yürütmeyi** izleme     |Görevler çalışırken, işin ve ona ait görevlerin ilerleyişini izlemek için Batch’i sorgulayın. İstemci uygulamanız veya hizmetiniz, Batch hizmetiyle HTTPS üzerinden iletişim kurabilir. Binlerce işlem düğümünde çalışan binlerce görevi izliyor olabileceğinizden [Batch hizmetini verimli şekilde sorguladığınızdan](batch-efficient-list-queries.md) emin olun.|
|5. **görev çıkışını** karşıya yükleyin     |Görevler tamamlanınca sonuç verilerini Azure Storage’a yükleyebilirler. Dosyaları doğrudan bir işlem düğümündeki dosya sisteminden de alabilirsiniz.|
|6. **çıktı dosyalarını** indirin     |İzleme işleminiz işinizdeki görevlerin tamamlandığını algıladığında, istemci uygulamanız veya hizmetiniz daha fazla işleme için çıktı verilerini indirebilir.|

Yukarıda açıklanan iş akışının Batch kullanmanın yalnızca bir yolu olduğunu ve birçok başka özellik ve seçenek olduğunu unutmayın. Örneğin, her işlem düğümünde [birden fazla görevi paralel olarak](batch-parallel-node-tasks.md) yürütebilirsiniz. Ya da [iş hazırlama ve tamamlama görevlerini](batch-job-prep-release.md) kullanarak işleriniz için düğümleri hazırlayın, ardından daha sonra temizleyebilirsiniz.

Havuzlar, düğümler, işler ve görevler gibi özelliklere genel bir bakış için bkz. [Batch hizmeti iş akışı ve kaynakları](batch-service-workflow-features.md) . Ayrıca en son [Toplu İşlem hizmet güncelleştirmeleri](https://azure.microsoft.com/updates/?product=batch)’ne bakın.

## <a name="in-region-data-residency"></a>Bölge içi veri yerleşimi
Azure Batch, müşteri verilerini dağıtıldığı bölgenin dışına taşımaz veya depolamaz. 

## <a name="next-steps"></a>Sonraki adımlar

Şu hızlı başlangıçlardan biriyle Azure Batch kullanmaya başlayın:
- [Azure CLI ile ilk Batch işinizi çalıştırma](quick-create-cli.md)
- [Azure portalı ile ilk Batch işinizi çalıştırma](quick-create-portal.md)
- [.NET API kullanarak ilk Batch işinizi çalıştırma](quick-run-dotnet.md)
- [Python API kullanarak ilk Batch işinizi çalıştırma](quick-run-python.md)
