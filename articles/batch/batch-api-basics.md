---
title: Geliştiricilere genel bakış-Azure Batch | Microsoft Docs
description: Batch hizmeti özelliklerini ve API’lerini geliştirme açısından öğrenin.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 416b95f8-2d7b-4111-8012-679b0f60d204
ms.service: batch
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 08/29/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 364861e57f37192a3ae454e27fedf732ee8d513e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350174"
---
# <a name="develop-large-scale-parallel-compute-solutions-with-batch"></a>Batch içe büyük ölçekli paralel işlem çözümleri geliştirme

Azure Batch hizmetinin temel bileşenlerine ilişkin bu genel bakışta, Batch geliştiricilerinin büyük ölçekli paralel işlem çözümleri derlemek üzere kullanabileceği birincil hizmetler ve kaynaklar ele alınmaktadır.

Dağıtılmış bir hesaplama uygulaması veya doğrudan [REST API][batch_rest_api] çağrıları yapan bir hizmet geliştirirken ya da [Batch SDK 'lardan](batch-apis-tools.md#azure-accounts-for-batch-development)birini kullanıyorsanız, bu makalede ele alınan kaynak ve özelliklerin birçoğunu kullanırsınız.

> [!TIP]
> Batch hizmetine daha yüksek düzeyde bir giriş için bkz. [Azure Batch temel bilgileri](batch-technical-overview.md). Ayrıca en son [Toplu İşlem hizmet güncelleştirmeleri](https://azure.microsoft.com/updates/?product=batch)’ne bakın.
>
>

## <a name="batch-service-workflow"></a>Batch hizmeti iş akışı

Aşağıdaki üst düzey iş akışı, paralel iş yüklerini işlemek üzere Batch hizmetini kullanan neredeyse tüm uygulamalar ve hizmetler için tipiktir:

1. İşlemek istediğiniz **veri dosyalarını** bir [Azure depolama][azure_storage] hesabına yükleyin. Batch, Azure Blob depolama alanına erişim için yerleşik destek içerir ve görevler çalıştırıldığında görevleriniz bu dosyaları [işlem düğümlerine](#compute-node) indirebilir.
2. Görevlerinizin çalıştıracağı **uygulama dosyalarını** karşıya yükleyin. Bu dosyalar ikili dosyalar ya da komut dosyaları ve onların bağımlılıkları olabilir ve işlerinizdeki görevler tarafından yürütülür. Görevleriniz bu dosyaları Storage hesabınızdan indirebilir veya uygulama yönetimi ve dağıtımı için Batch hizmetinin [uygulama paketleri](#application-packages) özelliğini kullanabilirsiniz.
3. Bir işlem düğümleri [havuzu](#pool) oluşturun. Bir havuz oluşturduğunuzda havuz için işlem düğümü sayısını, boyutlarını ve işletim sistemini belirtin. İşinizdeki her bir görev çalıştığında havuzunuzdaki düğümlerden birini yürütmek üzere atanır.
4. Bir [iş](#job) oluşturun. İş bir görev koleksiyonunu yönetir. Her işi, işin görevlerinin çalışacağı belirli bir havuz ile ilişkilendirin.
5. İşe [görevler](#task) ekleyin. Her görev, Storage hesabınızdan indirdiği veri dosyalarını işlemek üzere karşıya yüklediğiniz uygulamayı ve komut dosyasını çalıştırır. Her görev tamamlandığında çıktısını Azure Depolama hizmetine yükleyebilir.
6. İşin ilerleme durumunu izleyin ve görev çıktısını Azure Depolama’dan alın.

Aşağıdaki bölümlerde, dağıtılmış hesaplama senaryonuzu etkinleştiren toplu Iş kaynakları ele alınmaktadır.

> [!NOTE]
> Batch hizmetini kullanmak için bir [Batch hesabı](#account) gereklidir. Ayrıca, Batch çözümlerinin çoğu dosya depolama ve alma için ilişkili bir [Azure depolama][azure_storage] hesabı kullanır.
>
>

## <a name="batch-service-resources"></a>Batch hizmet kaynakları

Aşağıdaki kaynaklardan--hesaplar, işlem düğümleri, havuzlar, işler ve görevler--bazıları Batch hizmetini kullanan tüm çözümler için gereklidir. İş zamanlamaları ve uygulama paketleri gibi diğer özellikler de faydalıdır, ancak isteğe bağlıdır.

* [Hesap](#account)
* [İşlem düğümü](#compute-node)
* [Havuz](#pool)
* [İş](#job)
  * [İş zamanlamaları](#scheduled-jobs)
* [Görev](#task)
  * [Başlangıç görevi](#start-task)
  * [İş yöneticisi görevi](#job-manager-task)
  * [İş hazırlama ve bırakma görevleri](#job-preparation-and-release-tasks)
  * Çok örnekli görev (MPI)
  * [Görev bağımlılıkları](#task-dependencies)
* [Uygulama paketleri](#application-packages)

## <a name="account"></a>Hesap

Bir Batch hesabı Batch hizmeti dahilinde benzersiz şekilde tanımlanan bir varlıktır. Tüm işlemler bir Batch hesabıyla ilişkilendirilir.

[Azure portalını](batch-account-create-portal.md) kullanarak ya da [Toplu Yönetim .NET kitaplığı](batch-management-dotnet.md) ile olduğu gibi program aracılığıyla bir Azure Batch hesabı oluşturabilirsiniz. Hesabı oluştururken işle ilgili giriş veya çıkış verilerini veya uygulamaları kaydetmek için bir Azure depolama hesabı ilişkilendirebilirsiniz.

Tek bir Batch hesabında birden fazla Batch iş yükü çalıştırabilir ya da iş yüklerinizi aynı abonelik ve farklı Azure bölgelerindeki Batch hesapları arasında dağıtabilirsiniz.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

## <a name="azure-storage-account"></a>Azure Storage hesabı

Batch çözümlerinin çoğu, kaynak dosyalarını ve çıkış dosyalarını depolamak için Azure Depolama kullanır. Örneğin, Batch görevleriniz (standart görevler, başlangıç görevleri, iş hazırlama görevleri ve iş bırakma görevleri dahil) genellikle depolama hesabında yer alan kaynak dosyalarını belirtir.

Batch, aşağıdaki Azure Depolama hesap türlerini destekler:

* Genel amaçlı v2 (GPv2) hesapları 
* Genel amaçlı v1 (GPv1) hesapları
* Blob depolama hesapları (şu anda Sanal Makine yapılandırmasındaki havuzlar için desteklenmektedir)

Depolama hesapları hakkında daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../storage/common/storage-account-overview.md).

Batch hesabını oluşturduğunuzda veya daha sonra Batch hesabınızla bir depolama hesabını ilişkilendirebilirsiniz. Bir depolama hesabı seçerken, maliyet ve performans gereksinimlerinizi göz önünde bulundurun. Örneğin, GPv2 ve blob depolama hesabı seçenekleri, GPv1’e kıyasla daha büyük [kapasite ve ölçeklenebilirlik sınırlarını](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) destekler. (Depolama sınırında artış istemek için Azure Desteğine başvurun.) Bu hesap seçenekleri, depolama hesabından okuma veya depolama hesabına yazma işlemi gerçekleştiren çok sayıda paralel görev içeren Batch çözümlerinin performansını artırabilir.

## <a name="compute-node"></a>İşlem düğümü

İşlem düğümü, uygulama iş yükünüzün bir kısmını işlemeye ayrılmış bir Azure sanal makinesi (VM) veya bulut hizmeti sanal makinesidir. Bir düğümün boyutu CPU çekirdeklerinin sayısını, bellek kapasitesini ve düğüme ayrılan yerel dosya sistemi boyutunu belirler. Azure Cloud Services, [Azure sanal makineler marketi][vm_marketplace]'ndeki görüntüleri veya hazırladığınız özel görüntüleri kullanarak Windows veya Linux düğümleri için havuzlar oluşturabilirsiniz. Bu seçenekler hakkında daha fazla bilgi için aşağıdaki [Havuz](#pool) bölümüne bakın.

Düğümler, düğümün işletim sistemi ortamı tarafından desteklenen herhangi bir yürütülebilir dosyayı ya da komut dosyasını çalıştırabilir. Yürütülebilir dosyalar veya betikler \*; Windows için \*. exe, \*. cmd,. bat ve PowerShell betikleri ve Linux için ikili dosyalar, Shell ve Python betikleri içerir.

Batch içindeki tüm işlem düğümleri ayrıca şunları içerir:

* Standart bir [klasör yapısı](#files-and-directories) ve görevlere göre başvurulabilen ilişkili [ortam değişkenleri](#environment-settings-for-tasks).
* Erişimi denetlemek için yapılandırılan **Güvenlik Duvarı** ayarları.
* Hem Windows (Uzak Masaüstü Protokolü (RDP)) hem de Linux (Güvenli Kabuk (SSH)) düğümlerine [uzaktan erişim](#connecting-to-compute-nodes).

## <a name="pool"></a>Havuz

Havuz, uygulamanızın üzerinde çalıştığı bir düğüm koleksiyonudur. Havuz sizin tarafınızdan elle ya da siz yapılacak işleri belirttiğinizde Batch hizmeti tarafından otomatik olarak oluşturulabilir. Uygulamanızın kaynak gereksinimlerini karşılayan bir havuz oluşturup yönetebilirsiniz. Bir havuz yalnızca içinde oluşturulduğu Batch hesabı tarafından kullanılabilir. Bir Batch hesabı birden fazla havuza sahip olabilir.

Azure Batch havuzları temel Azure işlem platformu üzerinde derlenir. Bu havuzlar büyük ölçekli ayırma, uygulama yüklemesi, veri dağıtımı, durum izleme ve bir havuzdaki işlem düğümü sayısının esnek şekilde ayarlanmasını ([ölçeklendirme](#scaling-compute-resources)) sağlar.

Bir havuza eklenen her düğüme benzersiz bir ad ve IP adresi atanır. Bir düğüm havuzdan kaldırıldığında, işletim sistemi ya da dosyalara yapılan tüm değişiklikler kaybedilir ve düğümün adı ile IP adresi gelecekte kullanım için boşta kalır. Bir düğüm havuzdan ayrıldığında ömrü sona erer.

Bir havuz oluşturduğunuzda aşağıdaki öznitelikleri belirtebilirsiniz:

* İşlem düğümü işletim sistemi ve sürümü
* İşlem düğümü türü ve hedef düğüm sayısı
* İşlem düğümlerinin boyutu
* Ölçeklendirme ilkesi
* Görev zamanlama ilkesi
* İşlem düğümlerinin iletişim durumu
* İşlem düğümleri için başlangıç görevleri
* Uygulama paketleri
* Ağ yapılandırması

Bu ayarlar aşağıdaki bölümlerde ayrıntılı şekilde açıklanmıştır.

> [!IMPORTANT]
> Batch hesaplarının, bir Batch hesabındaki çekirdek sayısını sınırlayan varsayılan bir kotası vardır. Çekirdek sayısı, işlem düğümü sayısına karşılık gelir. Varsayılan kotaları ve [kota artırma](batch-quota-limit.md#increase-a-quota) yönergelerini [Azure Batch hizmeti için kotalar ve limitler](batch-quota-limit.md) bölümünde bulabilirsiniz. Havuzunuzun hedef düğüm sayısına ulaşmamasının nedeni çekirdek kotası olabilir.
>

### <a name="compute-node-operating-system-and-version"></a>İşlem düğümü işletim sistemi ve sürümü

Batch havuzu oluştururken Azure sanal makine yapılandırmasını ve havuzdaki her bir işlem düğümünde çalıştırmak istediğiniz işletim sistemi türünü belirtebilirsiniz. Batch ile birlikte kullanılabilen iki yapılandırma türü şunlardır:

* Havuzun Azure sanal makinelerinden oluştuğunu belirten **sanal makine yapılandırması**. Bu VM'ler Linux veya Windows görüntülerinden oluşturulabilir.

    Sanal Makine Yapılandırmasını temel alan bir havuz oluşturduğunuzda, yalnızca düğümlerin boyutunu ve onları oluşturmak için kullanılan görüntülerin kaynağını değil, aynı zamanda **sanal makine görüntü başvurusunu** ve düğümlere yüklenecek Batch **düğümü aracı SKU'sunu** da belirtmeniz gerekir. Bu havuz özelliklerini belirtme hakkında daha fazla bilgi için bkz. [Azure Batch havuzlarında Linux işlem düğümlerini hazırlama](batch-linux-nodes.md). İsteğe bağlı olarak Market görüntülerinden oluşturulmuş havuz VM'lerine bir veya daha fazla boş veri diski ekleyebilir veya VM'leri oluşturmak için kullanılan özel görüntülere veri diskleri dahil edebilirsiniz. Veri disklerini dahil etmek için, diskleri kullanmak üzere bir VM içinden bağlamanız ve biçimlendirmeniz gerekir.

* Havuzun Azure Cloud Services düğümlerinden oluştuğunu belirten **Cloud Services yapılandırması**. Cloud Services *yalnızca*Windows işlem düğümleri sağlar.

    Cloud Services Yapılandırması havuzları için kullanılabilen işletim sistemleri [Azure Konuk işletim sistemi sürümleri ve SDK uyumluluk matrisi](../cloud-services/cloud-services-guestos-update-matrix.md) içinde listelenmiştir. Cloud Services düğümleri içeren bir havuz oluşturduğunuzda düğüm boyutunu ve *İşletim Sistemi Ailesi*'ni belirtmeniz gerekir. Cloud Services, Windows çalıştıran sanal makinelerden daha hızlı bir şekilde Azure 'a dağıtılır. Windows işlem düğümlerinden oluşan havuzlar oluşturmak istiyorsanız, Cloud Services'ın dağıtım süresi açısından daha iyi bir performans sunduğunu görebilirsiniz.

    * *İşletim Sistemi Ailesi*, işletim sistemiyle hangi .NET sürümlerinin yüklendiğini de belirler.
    * Cloud Services dahilindeki çalışan rollerinde olduğu gibi bir *İşletim Sistemi Sürümü* belirtebilirsiniz (çalışan rolleri hakkında daha fazla bilgi için bkz. [Cloud Services’e genel bakış](../cloud-services/cloud-services-choose-me.md)).
    * Çalışan rollerinde olduğu gibi düğümlerin otomatik olarak yükseltilmesi için *İşletim Sistemi Sürümü* ’ne yönelik `*` belirtilmesi önerilir ve yeni yayımlanmış sürümlerin gereksinimini karşılamak için çalışma yapılması gerekmez. Belirli bir işletim sistemi sürümünün seçildiği birincil kullanım durumu, sürümün güncelleştirilmesine izin vermeden önce geriye dönük uyumluluk testinin gerçekleştirilmesine izin vererek uygulama uyumluluğunun sağlandığından emin olmaktır. Doğrulama sonrasında havuzun *İşletim Sistemi Sürümü* güncelleştirilebilir ve yeni işletim sistemi görüntüsü yüklenebilir; çalışan tüm görevler kesilir ve yeniden kuyruğa alınır.

Havuz oluştururken VHD'nizin temel görüntüsünün işletim sistemine bağlı olarak uygun **nodeAgentSkuId** değerini seçmeniz gerekir. [Desteklenen düğüm Aracısı SKU 'Larını Listele](https://docs.microsoft.com/rest/api/batchservice/list-supported-node-agent-skus) işlemini çağırarak, Işletim sistemi görüntüsü başvurularına kullanılabilir düğüm Aracısı SKU kimliklerinin eşlemesini alabilirsiniz.

#### <a name="custom-images-for-virtual-machine-pools"></a>Sanal Makine havuzları için özel görüntüler

Özel görüntülerle havuz oluşturma hakkında bilgi edinmek için bkz. [paylaşılan görüntü galerisini kullanarak özel bir havuz oluşturma](batch-sig-images.md).

Alternatif olarak, [yönetilen bir görüntü](batch-custom-images.md) kaynağı kullanarak sanal makineler için özel bir havuz oluşturabilirsiniz. Azure VM'lerinden özel Linux görüntüleri hazırlama hakkında bilgi için bkz. [Sanal makine veya VHD görüntüsü oluşturma](../virtual-machines/linux/capture-image.md). Azure sanal makinelerinden özel Windows görüntüleri hazırlama hakkında daha fazla bilgi için bkz. [Azure'da genelleştirilmiş VM'nin yönetilen görüntüsünü oluşturma](../virtual-machines/windows/capture-image-resource.md).

#### <a name="container-support-in-virtual-machine-pools"></a>Sanal Makine havuzlarında kapsayıcı desteği

Batch API'lerini kullanarak Sanal Makine Yapılandırma havuzu oluştururken havuzu görevleri Docker kapsayıcılarında çalıştıracak şekilde ayarlayabilirsiniz. Şu anda Docker kapsayıcılarını destekleyen bir görüntü kullanarak havuz oluşturmanız gerekir. Azure Market'teki Windows Server 2016 Datacenter with Containers görüntüsünü kullanın veya Docker Community Edition ya da Enterprise Edition ile gerekli sürücüleri içeren özel bir VM görüntüsü sağlayın. Havuz ayarları, kapsayıcı görüntülerini havuz oluşturulduğunda VM'lere kopyalayan bir [kapsayıcı yapılandırması](/rest/api/batchservice/pool/add) içermelidir. Havuzda çalışan görevler, kapsayıcı görüntülerine ve kapsayıcı çalıştırma seçeneklerine başvurabilir.

Daha fazla bilgi için bkz. [Azure Batch’te Docker kapsayıcı uygulamaları çalıştırma](batch-docker-container-workloads.md).

## <a name="compute-node-type-and-target-number-of-nodes"></a>İşlem düğümü türü ve hedef düğüm sayısı

Bir havuz oluşturduğunuzda, istediğiniz işlem düğümü türlerini ve her biri için hedeflenen sayıyı belirtebilirsiniz. İki tür işlem düğümü vardır:

* **Adanmış işlem düğümleri.** Adanmış bir işlem düğümleri, iş yükleriniz için ayrılmıştır. Bunlar düşük öncelikli düğümlerinden daha pahalıdır, ancak hiçbir zaman etkisiz hale getirilmeyeceği garanti edilir.

* **Düşük öncelikli işlem düğümleri.** Düşük öncelikli düğümler, Batch iş yüklerinizi çalıştırmak için Azure’daki fazla kapasiteden yararlanır. Düşük öncelikli düğümler, özel düğümlerden saat başına daha ucuz ve önemli işlem gücü gerektiren iş yüklerini etkinleştirir. Daha fazla bilgi için bkz. [Batch ile düşük öncelikli VM’ler kullanma](batch-low-pri-vms.md).

    Azure’daki fazla kapasite yetersiz olduğunda, düşük öncelikli işlem düğümleri etkisiz hale getirilebilir. Görevler çalıştırılırken bir düğüm etkisiz hale getirilirse, işlem düğümü yeniden kullanılabilir olduğunda görevler yeniden kuyruğa alınır ve tekrar çalıştırılır. Düşük öncelikli düğümler, iş tamamlama süresinin esnek olduğu ve işin çok sayıda düğüme dağıtıldığı iş yükleri için iyi bir seçenektir. Senaryonuz için düşük öncelikli düğümleri kullanmaya karar vermeden önce, ön işleme nedeniyle kaybolan tüm çalışmanın en az ve yeniden oluşturulması kolay olacağından emin olun.

Aynı havuzda hem düşük öncelikli hem de adanmış işlem düğümleri olabilir. &mdash;Düşük öncelikli ve adanmış&mdash; düğüm türlerinin her biri, istediğiniz işlem sayısını belirtebileceğiniz kendi hedef ayarına sahiptir.

Bazı durumlarda havuzunuz istenilen düğüm sayısına ulaşmayabileceğinden işlem düğümleri sayısı *hedef* olarak adlandırılır. Örneğin, bir havuz ilk olarak Batch hesabınızın [çekirdek kotasına](batch-quota-limit.md) ulaşırsa hedefe ulaşamayabilir. Veya, havuza en fazla düğüm sayısını sınırlayan bir otomatik ölçeklendirme formülü uyguladıysanız, havuz hedefe ulaşamayabilir.

Hem düşük öncelikli hem de adanmış işlem düğümlerinin fiyatlandırma bilgileri için bkz. [Batch Fiyatlandırması](https://azure.microsoft.com/pricing/details/batch/).

### <a name="size-of-the-compute-nodes"></a>İşlem düğümlerinin boyutu

Azure Batch havuzu oluşturduğunuzda, neredeyse Azure'da bulunan tüm VM aileleri ve boyutları arasından seçim yapabilirsiniz. Azure farklı iş yükleri için [HPC](../virtual-machines/linux/sizes-hpc.md) veya [GPU etkin](../virtual-machines/linux/sizes-gpu.md) VM boyutları da dahil olmak üzere çeşitli VM boyutları sunar. 

Daha fazla bilgi için bkz. [Azure Batch havuzunda işlem düğümleri için VM boyutunu seçme](batch-pool-vm-sizes.md).

### <a name="scaling-policy"></a>Ölçeklendirme ilkesi

Dinamik iş yükleri için bir havuza bir [Otomatik ölçeklendirme formülü](#scaling-compute-resources) yazabilir ve uygulayabilirsiniz. Batch hizmeti formülünüzü düzenli olarak değerlendirir ve belirtebileceğiniz çeşitli havuz, iş ve görev parametrelerine göre düğüm sayısını ayarlar.

### <a name="task-scheduling-policy"></a>Görev zamanlama ilkesi

[Düğüm başına en fazla görev](batch-parallel-node-tasks.md) yapılandırma seçeneği havuzdaki her bir işlem düğümünde paralel olarak çalıştırabilecek en fazla görev sayısını belirler.

Varsayılan yapılandırma bir düğümde tek seferde bir görevin çalışacağını belirtir, ancak bir düğümde aynı anda iki veya daha fazla görev yürütülmesinin faydalı olduğu senaryolar da vardır. Düğüm başına birden fazla görevden nasıl yararlanabileceğinizi görmek için [eşzamanlı düğüm görevleri](batch-parallel-node-tasks.md) makalesindeki [örnek senaryoya](batch-parallel-node-tasks.md#example-scenario) bakın.

Ayrıca, Batch 'in görevleri bir havuzdaki tüm düğümlerde eşit olarak paketleyemeyeceğini veya bir düğümdeki görevleri başka bir düğüme atamadan önce en fazla sayıda göreve paketleyemeyeceğini belirleyen bir *Fill türü*de belirtebilirsiniz.

### <a name="communication-status-for-compute-nodes"></a>İşlem düğümlerinin iletişim durumu

Çoğu senaryoda görevler bağımsız olarak çalışır ve birbirleriyle iletişim kurmaları gerekmez. Ancak, [MPI senaryolarında](batch-mpi.md) olduğu gibi içinde görevlerin iletişim kurması gereken bazı uygulamalar olabilir.

Bir havuzu **düğümler arasında iletişim kurmaya** izin verecek şekilde yapılandırarak bir havuzdaki düğümlerin çalışma zamanında iletişim kurmasını sağlayabilirsiniz. Düğümler arası iletişim etkinleştirildiğinde, Cloud Services havuzlarındaki düğümler 1100'den büyük bağlantı noktaları üzerinde birbiriyle iletişim kurabilir ve Sanal Makine Yapılandırması havuzları hiçbir bağlantı noktası üzerinde trafiği kısıtlamaz.

Düğümler arası iletişimin etkinleştirilmesi, düğümlerin kümeler içindeki yerleşimini da etkiler ve dağıtım kısıtlamaları nedeniyle havuzdaki en fazla düğüm sayısını sınırlayabilir. Uygulamanız düğümler arasında iletişim gerektirmiyorsa Batch hizmeti, artan paralel işleme gücünü etkinleştirmek amacıyla birçok kümeden ve veri merkezinden çok sayıda düğümü havuza ayırabilir.

### <a name="start-tasks-for-compute-nodes"></a>İşlem düğümleri için başlangıç görevleri

Bir düğüm havuza katıldığında ve bir düğümün yeniden başlatıldığı ya da görüntüsünün yeniden oluşturulduğu her durumda, düğüm üzerinde isteğe bağlı *başlangıç görevi* yürütülür. Başlangıç görevi, görevlerinizin işlem düğümlerinde çalıştıracağı uygulamaları yükleme gibi görevlerin yürütülmesi için özellikle işlem düğümlerinin hazırlanmasında yararlıdır.

### <a name="application-packages"></a>Uygulama paketleri

Havuzdaki işlem düğümlerine dağıtım yapacak [uygulama paketlerini](#application-packages) belirtebilirsiniz. Uygulama paketleri, görevlerinizin çalıştırdığı uygulamaların dağıtımını ve sürüm oluşturma işlemlerini basitleştirir. Havuz için belirttiğiniz uygulama paketleri, bir düğüm her yeniden başlatıldığında veya görüntüsü yeniden oluşturduğunda o havuza katılan her düğüme yüklenir.

> [!NOTE]
> Uygulama paketleri 5 Temmuz 2017’den sonra oluşturulmuş tüm Batch havuzlarında desteklenir. Bunların 10 Mart 2016 ve 5 Haziran 2017 arasında oluşturulmuş Batch havuzlarında desteklenebilmesi için, havuzun Bulut Hizmeti yapılandırması kullanılarak oluşturulmuş olması gerekir. 10 Mart 2016’dan önce oluşturulan Batch havuzları uygulama paketlerini desteklemez. Uygulama paketlerini kullanarak uygulamalarınızı Batch düğümlerine dağıtma hakkında daha fazla bilgi için bkz. [Batch uygulama paketleriyle işlem düğümlerine uygulama dağıtımı](batch-application-packages.md).
>
>

### <a name="network-configuration"></a>Ağ yapılandırması

Havuz işlem düğümlerinin oluşturulması gereken Azure [sanal ağın (VNet)](../virtual-network/virtual-networks-overview.md) alt ağını belirtebilirsiniz. Daha fazla bilgi için havuz ağ yapılandırması bölümüne bakın.

## <a name="job"></a>İş

İş bir görev koleksiyonudur. Bir havuzdaki işlem düğümleri üzerindeki görevleri tarafından hesaplamanın nasıl gerçekleştirildiğini yönetir.

* İş, çalışmanın gerçekleştirileceği **havuzu** belirtir. Her iş için yeni havuz oluşturabilir veya çok sayıda iş için bir havuz kullanabilirsiniz. Bir iş zamanlaması ile ilişkili her iş için veya bir iş zamanlaması ile ilişkili tüm işler için havuz oluşturabilirsiniz.
* İsteğe bağlı bir **iş önceliği** belirtebilirsiniz. Bir iş devam eden işlerden daha yüksek öncelikle gönderilirse yüksek önceliğe sahip işin görevleri düşük önceliğe sahip iş görevlerinin önünde kuyruğa eklenir. Çalışmakta olan düşük öncelikli işlerdeki görevler engellenmez.
* İşleriniz için bazı sınırlar belirtmek üzere iş **kısıtlamaları** kullanabilirsiniz:

    Bir **duvar saati zamanı üst sınırı** ayarlayabilirsiniz; böylece bir iş belirtilen duvar saati zamanı üst sınırından daha uzun süre çalışırsa iş ve tüm görevleri sonlandırılır.

    Batch başarısız olan görevleri algılayabilir ve sonra yeniden deneyebilir. Bir görevin *her zaman* yeniden deneneceği veya *hiçbir zaman* yeniden denenmeyeceği gibi kısıtlama olarak **en fazla görev yeniden deneme sayısı** belirtebilirsiniz. Bir görevi yeniden denemek, görevin tekrar çalıştırmak için yeniden kuyruğa olduğu anlamına gelir.

* İstemci uygulamanız bir işe görevler ekleyebilir ya da bir [iş yöneticisi görevi](#job-manager-task) belirtebilirsiniz. Bir iş yöneticisi görevi havuzdaki işlem düğümlerinden birinde çalıştırılan görevle birlikte bir iş için gereken görevleri oluşturmak üzere gerekli bilgileri içerir. İş yöneticisi görevi özellikle Batch tarafından işlenir; işin oluşturulmasının hemen ardından kuyruğa alınır ve başarısız olursa yeniden başlatılır. İş örneği oluşturulmadan görevleri tanımlamanın tek yolu olduğundan iş yöneticisi görevi, bir [iş zamanlaması](#scheduled-jobs) tarafından oluşturulan işler için *gereklidir*.
* Varsayılan olarak, işteki tüm görevler tamamlandığında iş etkin durumda kalır. Bu davranışı, işteki tüm görevler tamamlandığında işin otomatik olarak sonlandırılacağı şekilde değiştirebilirsiniz. Tüm görevleri tamamlandı durumundaysa işi otomatik olarak sonlandırmak için işin **onalltaskscompleted** özelliğini (Batch .net 'te[onalltaskscompleted][net_onalltaskscomplete] ) *terminatejob 'un engellenmesi* olarak ayarlayın.

    Batch hizmeti, tüm görevlerinin tamamlanması için görev *içermeyen* bir işi kabul eder. Bu nedenle, bu seçenek genellikle [iş yöneticisi görevi](#job-manager-task) ile kullanılır. İş yöneticisi olmadan otomatik iş sonlandırmayı kullanmak istiyorsanız başlangıçta yeni işin **onAllTasksComplete** özelliğini *noaction* olarak ayarlamanız ve işe görev eklemeyi bitirdiğinizde bu ayarı *terminatejob* olarak değiştirmeniz gerekir.

### <a name="job-priority"></a>İş önceliği

Batch’de oluşturduğunuz işlere öncelik atayabilirsiniz. Batch hizmeti bir hesaptaki iş zamanlama sırasını belirlemek üzere işin öncelik değerini kullanır ([zamanlanmış iş](#scheduled-jobs) ile karıştırılmamalıdır). Öncelik değeri, -1000 en düşük öncelik ve 1000 en yüksek öncelik olmak üzere, -1000 ile 1000 aralığındadır. Bir işin önceliğini güncelleştirmek için, [bir iş işleminin özelliklerini güncelleştirme][rest_update_job] (Batch REST) ya da [Cloudjob. Priority][net_cloudjob_priority] özelliğini (Batch .net) çağırın.

Aynı hesapta, yüksek öncelikli işlerin düşük öncelikli işlere göre zamanlama üstünlüğü vardır. Bir hesaptaki yüksek öncelik değerine sahip iş farklı bir hesaptaki düşük öncelik değerine sahip başka bir işe karşı zamanlama üstünlüğüne sahip değildir.

Havuzlardaki iş zamanlaması bağımsızdır. Farklı havuzlar arasında, ilişkili havuzunda boşta düğüm eksikliği olması durumunda yüksek öncelikli işin önce zamanlanacağı kesin değildir. Aynı havuzda, aynı öncelik değerine sahip işlerin zamanlanma şansı eşittir.

### <a name="scheduled-jobs"></a>Zamanlanan işler

[İş zamanlamaları][rest_job_schedules] , Batch hizmeti içinde yinelenen işler oluşturmanızı sağlar. Bir iş zamanlaması işlerin ne zaman çalıştırılacağını belirtir ve çalıştırılacak işlerin özelliklerini içerir. Zamanlama süresini (zamanlamanın ne kadar süreyle ve ne zaman etkin olacağını) ve bu zamanlanan sürede işlerin ne sıklıkta oluşturulacağını belirtebilirsiniz.

## <a name="task"></a>Görev

Görev bir işle ilişkili hesaplama birimidir. Bir düğüm üzerinde çalışır. Görevler yürütülmek için bir düğüme atanır veya bir düğüm serbest kalana kadar kuyruğa alınır. Kısacası görev, bitmesi gereken çalışmayı gerçekleştirmek üzere bir veya daha fazla program ya da komut dosyasını bir işlem düğümü üzerinde çalıştırır.

Bir görev oluşturduğunuzda aşağıdakileri belirtebilirsiniz:

* Görevin **komut satırı**. Uygulamanızı veya komut dosyanızı işlem düğümü üzerinde çalıştıran komut satırıdır.

    Komut satırının bir kabukta çalıştırılmadığını unutmamak önemlidir. Bu nedenle, [ortam değişkeni](#environment-settings-for-tasks) genişletmesi gibi kabuk özelliklerinden (buna `PATH` dahildir) yerel olarak yararlanamaz. Bu özelliklerden yararlanmak için kabuğu komut satırında çağırmanız gerekir (örneğin Windows düğümlerinde `cmd.exe` veya Linux'ta `/bin/sh` başlatarak):

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Görevlerinizin, düğümün `PATH` veya başvuru ortamı değişkenlerinde olmayan bir uygulama ya da komut dosyasını çalıştırması gerekiyorsa kabuğu görev komut satırında açıkça çağırın.
* İşlenecek verileri içeren **kaynak dosyalar**. Bu dosyalar görevin komut satırı yürütülmeden önce bir Azure Depolama hesabındaki Blob depolamadan düğüme otomatik olarak kopyalanır. Daha fazla bilgi için [Başlangıç görevi](#start-task) ve [Dosyalar ve dizinler](#files-and-directories) bölümlerine bakın.
* Uygulamanızın gerektirdiği **ortam değişkenleri**. Daha fazla bilgi için [Görevler için ortam ayarları](#environment-settings-for-tasks) bölümüne bakın.
* Görev yürütülürken tabi olunan **kısıtlamalar**. Örneğin, görevin çalışmasına izin verilen en uzun süre, başarısız olan bir görevin en fazla yeniden deneme sayısı ve görevin çalışma dizinindeki dosyaların elde tutulduğu en uzun süre kısıtlamalardan bazılarıdır.
* Görevin çalışmak üzere zamanlandığı işlem düğümünü dağıtmak için kullanılan **uygulama paketleri**. [Uygulama paketleri](#application-packages), görevlerinizin çalıştırdığı uygulamaların dağıtımını ve sürüm oluşturma işlemlerini basitleştirir. Görev düzeyinde uygulama paketleri, özellikle paylaşılan havuz ortamlarında çok yararlıdır. Bu ortamlarda, tek havuzda farklı işler çalıştırılır ve bir iş tamamlandığında havuz silinmez. İşinizin havuzdaki görevleri, düğümlerinden azsa uygulamanız yalnızca görevleri çalıştıran düğümlere dağıtıldığı için görev uygulama paketleri veri aktarımını azaltabilir.
* Düğümdeki görevin çalıştığı Docker kapsayıcısını oluşturmak için Docker Hub içinde bir **kapsayıcı görüntüsü** başvurusu veya özel kayıt defteri ile ek ayarlar. Bu bilgileri yalnızca havuz kapsayıcı yapılandırmasıyla kurulduysa belirtmeniz gerekir.

> [!NOTE]
> Görevin ne zaman tamamlandığını, ne zaman tamamlanana kadar en fazla yaşam süresi 180 gündür. Tamamlanan görevler 7 gün boyunca devam ederse; Maksimum yaşam süresi içinde tamamlanmayan görevlere yönelik verilere erişilemiyor.

Bir düğümde hesaplama yapmak üzere tanımladığınız görevlere ek olarak, aşağıdaki özel görevler de Batch hizmeti tarafından sağlanır:

* [Başlangıç görevi](#start-task)
* [İş yöneticisi görevi](#job-manager-task)
* [İş hazırlama ve bırakma görevleri](#job-preparation-and-release-tasks)
* Çok örnekli görevler (MPI)
* [Görev bağımlılıkları](#task-dependencies)

### <a name="start-task"></a>Başlangıç görevi

**Başlangıç görevini** bir havuz ile ilişkilendirerek düğümlerinin işletim sistemi ortamını hazırlayabilirsiniz. Örneğin, görevlerinizin çalıştıracağı uygulamaları yükleme veya arka plan işlemlerini başlatma gibi eylemleri gerçekleştirebilirsiniz. Başlangıç görevi, havuzda kaldığı sürece düğümün havuza ilk eklendiği ve yeniden başlatıldığı ya da görüntüsünün yeniden oluşturulduğu zaman dahil olmak üzere, bir düğüm her başlatıldığında çalışır.

Başlangıç görevinin birincil avantajı, bir işlem düğümünü yapılandırmak ve görev yürütmede gereken uygulamaları yüklemek için gerekli tüm bilgileri içerebilmesidir. Bu nedenle bir havuzdaki düğüm sayısını artırmak, yeni bir hedef düğüm sayısı belirtmek kadar kolaydır. Başlangıç görevi, Batch hizmetine yeni düğümleri yapılandırmak ve onları görev kabul etmeye hazır hale getirmek için gerekli olan bilgileri sunar.

Herhangi bir Azure Batch görevinde olduğu gibi, [Azure Storage][azure_storage]'da **kaynak dosyalarının** bir listesini de yürütülecek bir **komut satırına** belirtebilirsiniz. Batch hizmeti ilk olarak kaynak dosyaları düğümden Azure Depolama’ya kopyalar ve ardından komut satırını çalıştırır. Bir havuz başlangıç görevinde dosya listesi genellikle görev uygulamasını ve onun bağımlılıklarını içerir.

Ancak başlangıç görevi, işlem düğümü üzerinde çalışan tüm görevler tarafından kullanılacak başvuru verilerini de içerebilir. Örneğin, bir başlangıç görevinin komut satırı, uygulama dosyalarını (kaynak dosya olarak belirtilir ve düğüme indirilir) başlangıç görevinin [çalışma dizininden](#files-and-directories) [paylaşılan klasöre](#files-and-directories) kopyalamak üzere bir `robocopy` işlemi gerçekleştirebilir ve ardından bir MSI ya da `setup.exe` çalıştırabilir.

Bu, düğümün görevlere atanmak üzere hazır olduğunu düşünmeden önce başlangıç görevinin tamamlanmasını beklemek amacıyla Batch hizmeti için genelde istenen bir durumdur, ancak bunu yapılandırabilirsiniz.

Bir işlem düğümünde başlangıç görevi başarısız olursa, düğümün durumu hatayı yansıtacak şekilde güncelleştirilir ve düğüm hiçbir göreve atanmaz. Bir başlangıç görevi, depolamadan kaynak dosya kopyalamada bir sorun olması ya da komut satırı tarafından yürütülen işlemin sıfır olmayan bir çıkış kodu döndürmesi durumunda başarısız olabilir.

Mevcut bir havuz için başlangıç görevi ekler veya güncelleştirirseniz, başlangıç görevinin düğümlere uygulanması için işlem düğümlerini yeniden başlatmanız gerekir.

>[!NOTE]
> Batch, kaynak dosyalarını ve ortam değişkenlerini içeren başlangıç görevinin toplam boyutunu sınırlar. Bir başlangıç görevinin boyutunu azaltmanız gerekirse aşağıdaki iki yaklaşımdan birini kullanabilirsiniz:
>
> 1. Uygulamaları veya verileri Batch havuzunuzdaki tüm düğümlere dağıtmak için uygulama paketlerini kullanabilirsiniz. Uygulama paketleri hakkında daha fazla bilgi için bkz. [Batch uygulama paketleriyle işlem düğümlerine uygulama dağıtımı](batch-application-packages.md).
> 2. El ile uygulama dosyalarınızı içeren bir sıkıştırılmış arşiv oluşturabilirsiniz. Sıkıştırılmış arşivinizi Azure Depolama hesabına blob olarak karşıya yükleyin. Sıkıştırılmış arşivi başlangıç göreviniz için kaynak dosyası olarak belirleyin. Başlangıç göreviniz için komut satırını çalıştırmadan önce sıkıştırılmış arşivi komut satırından açın. 
>
>    Sıkıştırılmış arşivi açmak için istediğiniz arşivleme aracını kullanabilirsiniz. Sıkıştırılmış arşivi açmak için kullandığınız aracı, başlangıç görevinin kaynak dosyalarına eklemeniz gerekir.
>
>

### <a name="job-manager-task"></a>İş yöneticisi görevi

İş yürütmeyi denetlemek ve/veya izlemek için genellikle bir **iş yöneticisi görevi** kullanırsınız (örneğin, bir işin görevlerini oluşturmak ve göndermek, çalıştırılacak ek görevleri belirlemek ve çalışmanın ne zaman tamamlandığını belirlemek için). Ancak, bir iş yöneticisi görevi bu etkinliklerle sınırlı değildir. İş için gerekli olan tüm eylemleri gerçekleştirebilen tam kapsamlı bir görevdir. Örneğin, bir iş yöneticisi görevi parametre olarak belirtilen bir dosyayı indirebilir, dosyanın içeriğini çözümleyebilir ve bu içeriğe göre ek görevler gönderebilir.

Bir iş yöneticisi görevi diğer tüm görevlerden önce başlatılır. Aşağıdaki özellikleri sağlar:

* İş oluşturulduğunda, Batch hizmeti tarafından bir görev olarak otomatik olarak gönderilir.
* Bir işte diğer görevlerden önce yürütülecek şekilde zamanlanır.
* Havuzun boyutu küçültülürken bu görevin ilişkili düğümü havuzdan en son kaldırılacak düğümdür.
* Görevin sonlandırılması, işteki tüm görevlerin sonlandırılmasına bağlıdır.
* Yeniden başlatılması gerektiğinde iş yöneticisi görevine en yüksek öncelik verilir. Boş bir düğüm yoksa Batch hizmeti, iş yöneticisi görevinin çalışması için yer açmak amacıyla havuzundaki çalışan diğer görevlerden birini sonlandırabilir.
* Bir işteki iş yöneticisi görevinin, diğer işlerin görevleri üzerinde önceliği yoktur. İşlerde, yalnızca iş düzeyinde öncelikler gözetilir.

### <a name="job-preparation-and-release-tasks"></a>İş hazırlama ve bırakma görevleri

Batch, iş öncesi yürütme kurulumu için iş hazırlama görevleri sağlar. İş bırakma görevleri iş sonrası bakım ve temizlemeye yöneliktir.

* **İş hazırlama görevi**: İş hazırlama görevi, diğer iş görevlerinden herhangi biri yürütülmeden önce, görevleri çalıştırmak için zamanlanan tüm işlem düğümlerinde çalışır. Örneğin, tüm görevler tarafından paylaşılan ancak işe özel olan verileri kopyalamak için iş hazırlama görevini kullanabilirsiniz.
* **İş bırakma görevi**: Bir iş tamamlandığında, havuzda en az bir görevi yürüten her düğüm üzerinde bir iş bırakma görevi çalışır. Örneğin, iş hazırlama görevi tarafından kopyalanan verileri silmek ya da tanılama günlük verilerini sıkıştırmak veya karşıya yüklemek için iş bırakma görevini kullanabilirsiniz.

Hem iş hazırlama hem de bırakma görevleri, görev çağrıldığında çalıştırılacak bir komut satırı belirtmenize imkan tanır. Bunlar dosya indirme, yükseltilmiş yürütme, özel ortam değişkenleri, en uzun yürütme süresi, yeniden deneme sayısı ve dosyayı elde tutma süresi gibi özellikler sağlar.

İş hazırlama ve bırakma görevleri hakkında daha fazla bilgi için bkz. [Azure Batch işlem düğümlerinde iş hazırlama ve tamamlama görevlerini çalıştırma](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Çok örnekli görev

[Çok örnekli görev](batch-mpi.md) aynı anda birden fazla işlem düğümü üzerinde çalışacak şekilde yapılandırılmış bir görevdir. Çok örnekli görevlerle, bir grup işlem düğümünün tek bir iş yükünü işlemek üzere bir arada ayrılmasını gerektiren yüksek performanslı bilgi işlem senaryolarına olanak sağlayabilirsiniz (İleti Geçirme Arabirimi (MPI) gibi).

Batch .NET kitaplığını kullanarak MPI işlerini Batch’de çalıştırma hakkında ayrıntılı bilgi için bkz. [Azure Batch’de İleti Geçirme Arabirimi (MPI) uygulamalarını çalıştırmak için çok örnekli görevleri kullanma](batch-mpi.md).

### <a name="task-dependencies"></a>Görev bağımlılıkları

Adından da anlaşılacağı gibi [görev bağımlılıkları](batch-task-dependencies.md), bir görevin yürütülmesinin diğer görevlerin tamamlanmasına bağlı olduğunu belirtmenizi sağlar. Bu özellik “yukarı akış” görevinin çıktısını kullanan bir “aşağı akış” görevi durumları ya da bir yukarı akış görevi, aşağı akış görevi tarafından istenen bazı başlatma işlemlerini gerçekleştirdiğinde destek sağlar. Bu özelliği kullanmak için önce Batch işinizde görev bağımlılıklarını etkinleştirmelisiniz. Daha sonra, başka bir (veya daha fazla diğer) bağımlı olan her görev için, görevin bağımlı olduğu görevleri belirlersiniz.

Görev bağımlılıkları ile aşağıdaki gibi senaryoları yapılandırabilirsiniz:

* *görevB* *görevA* ’ya bağlıdır (*görevB*, *görevA* tamamlanana kadar yürütülmeye başlamaz).
* *görevC* hem *görevA* hem de *görevB* ’ye bağlıdır.
* *görevD* yürütülmeden önce bir dizi göreve (örneğin görev *1* ile *10* arası) bağlıdır.

Bu özellik hakkında daha ayrıntılı bilgi için [Azure-Batch-Samples][github_samples] GitHub deposundaki Azure Batch ve [taskdependencies][github_sample_taskdeps] kod örneğindeki [görev bağımlılıklarını](batch-task-dependencies.md) inceleyin.

## <a name="environment-settings-for-tasks"></a>Görevler için ortam ayarları

Batch hizmeti tarafından yürütülen her görevin, işlem düğümleri üzerinde ayarladığı ortam değişkenlerine erişimi vardır. Bu, Batch hizmeti tarafından tanımlanan ortam değişkenlerini ([hizmet tanımlı][msdn_env_vars]) ve görevleriniz için tanımlayabileceğiniz özel ortam değişkenlerini içerir. Görevleriniz tarafından yürütülen uygulamalar ve komut dosyaları yürütme sırasında bu ortam değişkenlerine erişebilir.

Bu varlıkların *ortam ayarları* özelliğini doldurarak görev ya da iş düzeyinde özel ortam değişkenleri ayarlayabilirsiniz. Örneğin, Batch .NET içindeki [bir iş işlemine görev ekleme][rest_add_task] (Batch REST API) veya [Cloudtask. EnvironmentSettings][net_cloudtask_env] ve [Cloudjob. commonenvironmentsettings][net_job_env] özelliklerine bakın.

İstemci uygulamanız veya hizmetiniz [bir görevin ortam][rest_get_task_info] değişkenlerini (toplu REST) veya [Cloudtask. EnvironmentSettings][net_cloudtask_env] özelliğine erişerek bir görevin hem hizmet tanımlı hem de özel olarak elde edebilir ( Batch .NET). Bir işlem düğümünde yürütülen işlemler bu ve düğümdeki diğer ortam değişkenlerine erişebilir, örneğin bilinen bir `%VARIABLE_NAME%` (Windows) veya `$VARIABLE_NAME` (Linux) söz dizimini kullanarak.

[İşlem düğümü ortam değişkenlerinde][msdn_env_vars], hizmet tarafından tanımlanan tüm ortam değişkenlerinin tam listesini bulabilirsiniz.

## <a name="files-and-directories"></a>Dosyalar ve dizinler

Her görev, altında sıfır veya daha fazla dosya ve dizin oluşturduğu bir *çalışma dizinine* sahiptir. Bu çalışma dizini, görev tarafından çalıştırılan programı, işlediği verileri ve gerçekleştirdiği işlemin çıktısını depolamak için kullanılabilir. Bir görevin tüm dosyaları ve dizinleri görev kullanıcısına aittir.

Batch hizmeti bir düğümdeki dosya sisteminin bir bölümünü *kök dizin* olarak kullanıma sunar. Görevler `AZ_BATCH_NODE_ROOT_DIR` ortam değişkenine başvurarak kök dizine erişebilir. Ortam değişkenlerini kullanma hakkında daha fazla bilgi için bkz. [Görevler için ortam ayarları](#environment-settings-for-tasks).

Kök dizin aşağıdaki dizin yapısını içerir:

![İşlem düğümü dizin yapısı][1]

* **uygulamalar**: İşlem düğümünde yüklü olan uygulama paketlerinin ayrıntıları hakkındaki bilgileri içerir. Görevler `AZ_BATCH_APP_PACKAGE` ortam değişkenine başvurarak bu dizine erişebilir.

* **fstakal**: Dizin, bir işlem düğümüne bağlı herhangi bir dosya sistemini içerir. Görevler `AZ_BATCH_NODE_MOUNTS_DIR` ortam değişkenine başvurarak bu dizine erişebilir. Daha fazla bilgi için bkz. bir [Batch havuzunda sanal dosya sistemi bağlama](virtual-file-mount.md).

* **paylaşılan**: Bu dizin, bir düğümde çalışan *Tüm* görevlere okuma/yazma erişimi sağlar. Düğüm üzerinde çalışan herhangi bir görev bu dizinde dosya oluşturabilir, okuyabilir, güncelleştirebilir ve silebilir. Görevler `AZ_BATCH_NODE_SHARED_DIR` ortam değişkenine başvurarak bu dizine erişebilir.

* **Başlangıç**: Bu dizin, bir başlangıç görevi tarafından çalışma dizini olarak kullanılır. Başlangıç görevi tarafından düğüme indirilen tüm dosyalar buraya depolanır. Başlangıç görevleri bu dizin altında dosya oluşturabilir, okuyabilir, güncelleştirebilir ve silebilir. Görevler `AZ_BATCH_NODE_STARTUP_DIR` ortam değişkenine başvurarak bu dizine erişebilir.

* **geçici**: Bu dizin iç amaçlar içindir. Bu dizindeki dosyaların veya dizinin kendisinin gelecekte mevcut olacağı garanti yoktur.

* **WORKITEMS**: Bu dizin, işlem düğümündeki işlerin ve görevlerinin dizinlerini içerir.

* **Görevler**: **Workitems** dizininde, düğüm üzerinde çalışan her görev için bir dizin oluşturulur. `AZ_BATCH_TASK_DIR` Ortam değişkenine başvurarak erişilir.

    Her bir görev dizininde Batch hizmeti, bir çalışma dizini (`wd`) oluşturur. Dizinin benzersiz yolu `AZ_BATCH_TASK_WORKING_DIR` ortam değişkeni tarafından belirtilir. Bu dizin göreve okuma/yazma erişimi sağlar. Görev bu dizin altında dosya oluşturabilir, okuyabilir, güncelleştirebilir ve silebilir. Bu dizin, görev için belirtilen *RetentionTime* kısıtlamasına göre tutulur.

    `stdout.txt`ve `stderr.txt`: Bu dosyalar, görevin yürütülmesi sırasında görev klasörüne yazılır.

> [!IMPORTANT]
> Bir düğüm havuzdan kaldırıldığında düğümde depolanan dosyaların *tümü* kaldırılır.
>
>

## <a name="application-packages"></a>Uygulama paketleri

[Uygulama paketleri](batch-application-packages.md) özelliği kolay uygulama yönetimi ve havuzlarınızdaki işlem düğümlerine kolay uygulama dağıtımı sağlar. Görevleriniz tarafından çalıştırılan uygulamaların birden fazla sürümünü (ikili dosyalar ve destek dosyalarıyla birlikte) kolayca karşıya yükleyebilir ve yönetebilirsiniz. Sonra bu uygulamalardan bir ya da daha fazlasını havuzunuzdaki işlem düğümlerine otomatik olarak dağıtabilirsiniz.

Uygulama paketlerini havuz ve görev düzeyinde belirtebilirsiniz. Havuz uygulama paketleri belirttiğinizde uygulama, havuzdaki her düğüme dağıtılır. Görev uygulama paketlerini belirttiğinizde uygulama, görevin komut satırı çalıştırılmadan hemen önce işteki görevlerden en az birinde çalışacak şekilde zamanlanan düğümlere dağıtılır.

Batch, uygulama paketlerinizi depolamak ve işlem düğümlerine dağıtmak amacıyla Azure Depolama ile çalışma ayrıntılarını işler, böylece hem kodunuz hem de yönetim ek yükünüz basitleştirilebilir.

Uygulama paketi özelliği hakkında daha fazla bilgi almak için bkz. [Batch uygulama paketleriyle işlem düğümlerine uygulama dağıtımı](batch-application-packages.md).

> [!NOTE]
> *Mevcut* bir havuza havuz uygulama paketleri eklerseniz uygulama paketlerinin düğümlere dağıtılması için işlem düğümlerini yeniden başlatmanız gerekir.
>
>

## <a name="pool-and-compute-node-lifetime"></a>Havuz ve işlem düğümü ömrü

Azure Batch çözümünüzü tasarlarken havuzların nasıl ve ne zaman oluşturulacağı ve bu havuzlardaki işlem düğümlerinin ne kadar süre kullanımda tutulacağına ilişkin bir tasarım kararı vermeniz gerekir.

Spektrumun bir ucunda, gönderdiğiniz her iş için bir havuz oluşturabilir ve görevlerin yürütülmesi biter bitmez havuzu silebilirsiniz. Düğümler yalnızca gerektiğinde ayrıldığından ve boşta kalır kalmaz kapatıldığından bunun yapılması kullanımı en iyi duruma getirir. Bu durum işin, düğümlerin ayrılmasını beklemesi gerektiği anlamına gelmekle birlikte, tek tek kullanılabilir, ayrılmış olmalarının ve başlangıç görevinin tamamlanmasının hemen ardından görevlerin yürütülmek üzere zamanlanacağını bilmek önemlidir. Batch, görevler düğümlere atamadan önce havuzdaki tüm düğümlerin kullanılabilir olmasını *beklemez*. Böylece kullanılabilir tüm düğümlerden en iyi şekilde faydalanılmasını sağlar.

Spektrumun diğer ucunda, işlerin hemen başlatılması en yüksek önceliğe sahipse işler gönderilmeden önce bir havuz oluşturabilir ve bu havuzun düğümlerini kullanıma sunabilirsiniz. Bu senaryoda görevler hemen başlayabilir, ancak görevlerin atanmasını beklerken düğümler boşta kalmaya devam edebilir.

Değişken ancak devam eden bir yükü işlemek için genellikle birleştirilmiş bir yaklaşım kullanılır. Birden fazla işin gönderildiği bir havuzunuz olabilir, ancak düğüm sayısını iş yüküne uygun olarak artırabilir veya azaltabilirsiniz (aşağıdaki bölümde yer alan [İşlem kaynaklarını ölçeklendirme](#scaling-compute-resources) kısmına bakın). Mevcut yüke bağlı olarak reaktif bir şekilde ya da yük öngörülebiliyorsa proaktif olarak bu işlemi yapabilirsiniz.

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Sanal ağ ve güvenlik duvarı yapılandırması 

Batch'te işlem düğümlerinden oluşan bir havuz sağladığınızda, havuzu bir Azure [sanal ağının](../virtual-network/virtual-networks-overview.md) alt ağı ile ilişkilendirebilirsiniz. Azure sanal ağı kullanmak için Batch istemci API'sinin Azure Active Directory (AD) kimlik doğrulamasını kullanması gerekir. Azure AD için Azure Batch desteği, [Batch hizmeti çözümlerinin kimliğini Active Directory ile doğrulama](batch-aad-auth.md) makalesinde belirtilmiştir.  

### <a name="vnet-requirements"></a>Sanal ağ gereksinimleri

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Bir sanal ağda Batch havuzu oluşturma hakkında daha fazla bilgi için bkz. [Sanal ağınızda sanal makine havuzu oluşturma](batch-virtual-network.md).

## <a name="scaling-compute-resources"></a>İşlem kaynaklarını ölçeklendirme

[Otomatik ölçeklendirme](batch-automatic-scaling.md) kullanarak, Batch hizmetinin, bir havuzdaki işlem düğümleri sayısını mevcut iş yüküne ve işlem senaryonuzun kaynak kullanımına göre dinamik olarak ayarlamasını sağlayabilirsiniz. Bu, yalnızca ihtiyacınız olan kaynakları kullanarak ve ihtiyacınız olmayanları bırakarak uygulamanızı çalıştırmaya ilişkin genel maliyeti düşürmenizi sağlar.

Bir [otomatik ölçeklendirme formülü](batch-automatic-scaling.md#automatic-scaling-formulas) yazıp bu formülü bir havuzla ilişkilendirerek otomatik ölçeklendirmeyi etkinleştirebilirsiniz. Batch hizmeti, sonraki ölçeklendirme aralığı (sizin yapılandırabileceğiniz bir aralık) için havuzdaki düğümlerin hedef sayısını belirlemek amacıyla bu formülü kullanır. Bir havuzu oluştururken otomatik ölçeklendirme ayarlarını belirtebilir ya da bir havuz üzerinde ölçeklendirmeyi daha sonra etkinleştirebilirsiniz. Ölçeklendirme özellikli bir havuz üzerinde ölçeklendirme ayarlarını da güncelleştirebilirsiniz.

Örnek olarak, belki de bir iş yürütülmesi için çok sayıda görev göndermeniz gerekir. Havuza, kuyruğa alınmış görevlerin mevcut sayısı ve iş içindeki görevlerin tamamlanma oranı temelinde havuzdaki düğüm sayısını ayarlayan bir ölçeklendirme formülü atayabilirsiniz. Batch hizmeti düzenli aralıklarla formülü değerlendirir ve havuzu, iş yükü ile diğer formül ayarlarınız temelinde yeniden boyutlandırır. Hizmet, kuyrukta çok sayıda görev olduğunda düğüm ekler, kuyrukta veya çalışan görev olmadığında ise düğümleri kaldırır.

Ölçeklendirme formülü aşağıdaki ölçümleri temel alabilir:

* **Zaman ölçümleri** belirtilen saat sayısınca beş dakikada bir toplanan istatistikleri temel alır.
* **Kaynak ölçümleri** CPU kullanımı, bant genişliği kullanımı, bellek kullanımı ve düğüm sayısını temel alır.
* **Görev ölçümleri**; *Etkin* (kuyruğa alınmış) *Çalışıyor* veya *Tamamlandı* gibi görev durumlarını temel alır.

Otomatik ölçeklendirme bir havuzdaki işlem düğümlerinin sayısını azalttığında, azaltma işlemi sırasında çalışan görevlerin nasıl ele alınacağını göz önünde bulundurmanız gerekir. Batch bunu yapabilmek için formüllerinize dahil edebileceğiniz bir *düğüm ayırmasını kaldırma seçeneği* sağlar. Örneğin, çalışmakta olan görevlerin hemen durdurulacağını, ardından başka bir düğüm üzerinde yeniden kuyruğa alınacağını veya düğüm havuzdan kaldırılmadan önce bitmesine izin verileceğini belirtebilirsiniz.

Bir uygulamayı otomatik olarak ölçeklendirme hakkında daha fazla bilgi için bkz. [Azure Batch havuzunda işlem düğümlerini otomatik olarak ölçeklendirme](batch-automatic-scaling.md).

> [!TIP]
> İşlem kaynağından en iyi şekilde faydalanabilmek için işin sonundaki düğüm sayısını sıfır olarak ayarlayın ancak çalışan görevlerin bitmesine izin verin.
>
>

## <a name="security-with-certificates"></a>Sertifikalar ile güvenlik

Bir [Azure depolama hesabı][azure_storage]anahtarı gibi, görevler için hassas bilgileri şifrelerken veya şifrelerini çözdüğünde genellikle sertifikalar kullanmanız gerekir. Bunu desteklemek için düğümlere sertifikalar yükleyebilirsiniz. Şifrelenmiş parolalar komut satırı parametreleri aracılığıyla düğümlere geçirilir ya da görev kaynaklarından birine eklenir ve yüklü sertifikalar bunların şifrelerini çözmek için kullanılabilir.

Batch hesabına bir sertifika eklemek için [sertifika ekle][rest_add_cert] Işlemini (Batch REST) veya [Certificateoperations. CreateCertificate][net_create_cert] yöntemini (Batch .net) kullanırsınız. Sonra sertifikayı yeni ya da mevcut bir havuzla ilişkilendirebilirsiniz. Sertifika bir havuzla ilişkilendirildiğinde, Batch hizmeti havuzdaki her düğüme sertifikayı yükler. Batch hizmeti düğüm başlatıldığında başlangıç görevi ve iş yöneticisi görevi dahil herhangi bir görevi başlatmadan önce, uygun sertifikaları yükler.

*Mevcut* bir havuza sertifika eklerseniz sertifikaların düğümlere uygulanması için işlem düğümlerini yeniden başlatmanız gerekir.

## <a name="error-handling"></a>Hata işleme

Batch çözümündeki görev ve uygulama hatalarını işlemeyi gerekli görebilirsiniz.

### <a name="task-failure-handling"></a>Görev hatası işleme

Görev hataları kategorileri şunlardır:

* **Ön işleme hataları**

    Bir görev çalışmadığında ön işleme hatası belirlenir.  

    Görevin kaynak dosyalarının taşınmış olması, Storage hesabının artık kullanılmaması veya dosyaların düğüme başarıyla kopyalanmasını önleyen bir sorunla karşılaşılması durumunda ön işleme hataları oluşabilir.

* **Karşıya dosya yükleme hataları**

    Bir görev için belirtilen karşıya dosya yükleme işlemi herhangi bir nedenle başarısız olursa, görev için dosya yükleme hatası belirlenir.

    Azure Depolama erişimi için verilen SAS geçersiz olduğunda veya yazma izni vermediğinde, depolama hesabı kullanılabilir durumda olmadığında veya dosyaların düğümden başarıyla kopyalanmasını önleyen bir sorunla karşılaşıldığında karşıya dosya yükleme hataları oluşabilir.    

* **Uygulama hataları**

    Görevin komut satırı tarafından belirtilen işlem de başarısız olabilir. Görev tarafından yürütülen işlem sıfır olmayan çıkış kodu döndürdüğünde işlemin başarısız olduğu kabul edilir (sonraki bölümde yer alan *Görev çıkış kodları* kısmına bakın).

    Uygulama hataları için Batch’i, görevi belirlenen sayıya kadar otomatik olarak yeniden deneyecek şekilde yapılandırabilirsiniz.

* **Kısıtlama hataları**

    Bir iş ya da görev için maksimum yürütme süresini belirleyen bir kısıtlama (*maxWallClockTime*) ayarlayabilirsiniz. Bu ilerleme göstermeyen görevlerin sonlandırılması için faydalı olabilir.

    Maksimum süre aşıldığında görev *tamamlandı* olarak işaretlenir ancak çıkış kodu `0xC000013A` olarak ayarlanır ve *schedulingError* alanı `{ category:"ServerError", code="TaskEnded"}` olarak işaretlenir.

### <a name="debugging-application-failures"></a>Uygulama hatalarını ayıklama

* `stderr` ve `stdout`

    Yürütme sırasında bir uygulama, sorunları gidermek için kullanabileceğiniz tanılama çıktıları üretebilir. Önceki [Dosyalar ve dizinler](#files-and-directories) bölümünde belirtildiği gibi, Batch hizmeti işlem düğümündeki görev dizininde yer alan `stdout.txt` ve `stderr.txt` dosyalarına standart çıktı ve standart hata çıktısı yazar. Bu dosyaları indirmek için Azure portalını veya toplu SDK'lardan birini kullanabilirsiniz. Örneğin, Batch .NET kitaplığı 'nda [ComputeNode. GetNodeFile][net_getfile_node] ve [Cloudtask. getnodefile][net_getfile_task] kullanarak bu ve diğer dosyaları sorun giderme amacıyla alabilirsiniz.

* **Görev çıkış kodları**

    Daha önce belirtildiği gibi, görev tarafından yürütülen işlem sıfır olmayan bir çıkış kodu döndürürse görev Batch hizmeti tarafından başarısız olarak işaretlenir. Bir görev bir işlemi yürüttüğünde Batch, görevin çıkış kodu özelliğini *işlemin dönüş kodu* ile doldurur. Bir görevin çıkış kodunun Batch hizmeti tarafından **belirlenmediğine** dikkat etmeniz önemlidir. Bir görevin çıkış kodu işlemin kendisi ya da işlemin yürütüldüğü işletim sistemi tarafından belirlenir.

### <a name="accounting-for-task-failures-or-interruptions"></a>Görev hataları veya kesintilerinin sebepleri

Görevler zaman zaman başarısız olabilir veya kesintiye uğrayabilir. Görevin kendisi başarısız olabilir, görevin üzerinde çalıştığı düğüm yeniden başlatılabilir ya da havuzun ayırmayı kaldırma ilkesi görevin bitmesini beklemeden düğümleri kaldırmak üzere ayarlandıysa, yeniden boyutlandırma işlemi sırasında düğüm havuzdan kaldırılabilir. Her durumda, görev başka bir düğümde yürütülmek üzere Batch tarafından otomatik olarak yeniden kuyruğa alınabilir.

Bir görevin yanıt vermemesine veya yürütülmesi çok uzun sürmesine neden olan aralıklı bir sorun da mümkündür. Bir görev için en fazla yürütme aralığını ayarlayabilirsiniz. Maksimum yürütme aralığı aşılırsa Batch hizmeti görev uygulamasını kesintiye uğratır.

### <a name="connecting-to-compute-nodes"></a>İşlem düğümlerine bağlanma

Bir işlem düğümünde uzaktan oturum açarak ek hata ayıklama ve sorun giderme işlemlerini gerçekleştirebilirsiniz. Windows düğümleri için bir Uzak Masaüstü Protokolü (RDP) dosyası indirmek ve Linux düğümleri için Güvenli Kabuk (SSH) bağlantı bilgilerini elde etmek üzere Azure portalını kullanabilirsiniz. Bu Işlemi Batch API 'Lerini kullanarak da yapabilirsiniz; Örneğin, [Batch .net][net_rdpfile] veya [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh)ile.

> [!IMPORTANT]
> RDP veya SSH aracılığıyla bir düğüme bağlanmak için düğümde bir kullanıcı oluşturmanız gerekir. Bunu yapmak için Azure portal kullanabilir, Batch REST API kullanarak [bir düğüme bir kullanıcı hesabı ekleyebilir][rest_create_user] , Batch .NET içinde [ComputeNode. CreateComputeNodeUser][net_create_user] metodunu çağırabilir veya Batch Python modülünde [add_user][py_add_user] metodunu çağırabilirsiniz.
>
>

İşlem düğümlerini kısıtlamanız veya bu düğümlere RDP ya da SSH erişimini devre dışı bırakmanız gerekiyorsa, bkz. [Azure Batch havuzunda işlem düğümlerine uzaktan erişimi yapılandırma veya devre dışı bırakma](pool-endpoint-configuration.md).

### <a name="troubleshooting-problematic-compute-nodes"></a>Sorunlu işlem düğümleriyle ilgili sorunları giderme

Bazı görevlerinizin başarısız olduğu durumlarda, Batch istemci uygulamanız ya da hizmetiniz, hatalı davranan düğümü tanımlamak üzere başarısız görevlerin meta verilerini inceleyebilir. Bir havuzdaki her düğüme benzersiz bir kimlik verilir ve bir görevin çalıştığı düğüm görev meta verilerine eklenir. Bir sorun düğümünü tanımladıktan sonra bununla birkaç eylem gerçekleştirebilirsiniz:

* **Düğümü yeniden başlatma** ([rest][rest_reboot] | [.net][net_reboot])

    Düğümü yeniden başlatmak bazen takılan veya çöken işlemler gibi görünmeyen sorunları temizleyebilir. Havuzunuz bir başlangıç görevi kullanıyorsa ya da işiniz iş hazırlama görevi kullanıyorsa, düğüm yeniden başlatıldığında yürütülür.
* **Düğümü yeniden görüntü** oluşturma ([rest][rest_reimage] | [.net][net_reimage])

    Bu işlem, işletim sistemini düğüme yeniden yükler. Düğümün yeniden başlatılmasıyla düğümün görüntüsünün yeniden oluşturulmasının ardından başlangıç görevleri ve iş hazırlama görevleri yeniden çalıştırılır.
* **Düğümü havuzdan kaldırma** ([rest][rest_remove] | [.net][net_remove])

    Bazen düğümün havuzdan tamamen kaldırılması gereklidir.
* **Düğümde görev zamanlamayı devre dışı bırakma** ([rest][rest_offline] | [.net][net_offline])

    Bu, başka görev atanmayacak şekilde düğümü çevrimdışı durumuna alır, ancak düğümün çalışır durumda ve havuzda kalmasına izin verir. Bu, başarısız olan görevin verilerini kaybetmeden ve düğüm, başka görev hatalarına neden olmadan hatalara ilişkin ayrıntılı araştırma gerçekleştirmenizi sağlar. Örneğin düğümde görev zamanlamayı devre dışı bırakabilir, sonra düğümün olay günlüklerini incelemek üzere [uzaktan oturum açabilir](#connecting-to-compute-nodes) ya da başka sorun giderme işlemleri uygulayabilirsiniz. Araştırmanızı bitirdikten sonra görev zamanlamayı ([REST][rest_online] | [.net][net_online]) etkinleştirerek düğümü yeniden çevrimiçi duruma getirebilir veya daha önce ele alınan diğer eylemlerden birini gerçekleştirebilirsiniz.

> [!IMPORTANT]
> Bu bölümde açıklanan her bir eylemde (yeniden başlatma, yeniden görüntü oluşturma, görev zamanlamayı kaldırma ve devre dışı bırakma), eylemi gerçekleştirdiğinizde düğümde çalışmakta olan görevlerin nasıl işleneceğini belirtebilirsiniz. Örneğin, Batch .NET istemci kitaplığını kullanarak bir düğümde görev zamanlamayı devre dışı bıraktığınızda, çalışan görevlerin **sonlanıp sonlandırılmayacağını** belirtmek Için bir [DisableComputeNodeSchedulingOption][net_offline_option] Enum değeri belirtebilirsiniz. diğer düğümlerde zamanlama yapın veya eylemi gerçekleştirmeden önce çalıştırma görevlerinin tamamlanmasına izin verin (**Taskcompletion**).
>
>

## <a name="next-steps"></a>Sonraki adımlar

* Batch çözümleri oluşturmak için kullanılabilen [Batch API’leri ve araçları](batch-apis-tools.md) hakkında bilgi alın.
* [Batch .NET istemci kitaplığı](quick-run-dotnet.md) veya [Python](quick-run-python.md) kullanarak Batch özellikli bir uygulama geliştirmenin temellerini öğrenin. Bu hızlı başlangıçlar, bir iş yükünü birden fazla işlem düğümünde yürütmek üzere Batch hizmetini kullanan örnek uygulamalar konusunda size rehberlik sağlamanın yanı sıra, iş yükü dosyası hazırlama ve alma işlemleri için Azure Depolama kullanma ile ilgili bilgiler de içerir.
* Batch çözümlerinizi geliştirirken kullanmak üzere [Batch Explorer][batch_labs] indirin ve yükleyin. Batch Explorer; Azure uygulamalarıyla ilgili oluşturma, hata ayıklama ve izleme işlemlerinde yardımcı olabilir. 
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch), [Batch Community deposunun](https://github.com/Azure/Batch)yanı sıra MSDN 'deki [Azure Batch Forumu][batch_forum] gibi topluluk kaynaklarına bakın. 

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[batch_labs]: https://azure.github.io/BatchExplorer/
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: https://docs.microsoft.com/azure/python/

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
