---
title: Azure Batch düğümler ve havuzlar
description: İşlem düğümleri ve havuzlar hakkında bilgi edinin ve bunların bir geliştirme açısından Azure Batch iş akışında nasıl kullanıldığını öğrenin.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 7d4c2d45849deb011498efe4c8a1ae91724b9acd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563904"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Azure Batch düğümler ve havuzlar

Azure Batch bir iş akışında, bir *işlem düğümü* (veya *düğümü*), uygulamanızın iş yükünün bir bölümünü işleyen bir sanal makinedir. *Havuz* , uygulamanızın üzerinde çalışacağı bu düğümlerin bir koleksiyonudur. Bu makalede düğümler ve havuzlar hakkında daha fazla bilgi verilmektedir ve bunları bir Azure Batch iş akışında oluşturma ve kullanma konusunda dikkat edilecek noktalar açıklanmaktadır.

## <a name="nodes"></a>Düğümler

Düğüm, uygulamanızın iş yükünün bir kısmını işlemeye ayrılmış bir Azure sanal makinesi (VM) veya bulut hizmeti sanal makinedir. Bir düğümün boyutu CPU çekirdeklerinin sayısını, bellek kapasitesini ve düğüme ayrılan yerel dosya sistemi boyutunu belirler.

Azure Cloud Services’ı, [Microsoft Azure Sanal Makineler Market görüntülerini](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) veya kendi hazırladığınız özel görüntüleri kullanarak Windows veya Linux düğümleri içeren havuzlar oluşturabilirsiniz.

Düğümler, düğümün işletim sistemi ortamı tarafından desteklenen herhangi bir yürütülebilir dosyayı ya da komut dosyasını çalıştırabilir. Yürütülebilir dosyalar veya betikler \* . exe, \* . cmd, \* . bat ve PowerShell betikleri (Windows için) ve ikili dosyalar, kabuk ve Python betikleri (Linux için) içerir.

Batch içindeki tüm işlem düğümleri ayrıca şunları içerir:

- Standart bir [klasör yapısı](files-and-directories.md) ve görevlere göre başvurulabilen ilişkili [ortam değişkenleri](jobs-and-tasks.md).
- Erişimi denetlemek için yapılandırılan **Güvenlik Duvarı** ayarları.
- Hem Windows (Uzak Masaüstü Protokolü (RDP)) hem de Linux (Secure Shell (SSH)) düğümlerine [Uzaktan erişim](error-handling.md#connect-to-compute-nodes) ( [havuzunuzu uzaktan erişim devre dışı olarak oluşturmadığınız](pool-endpoint-configuration.md)müddetçe).

Varsayılan olarak, düğümler birbirleriyle iletişim kurabilir, ancak aynı havuzun parçası olmayan sanal makinelerle iletişim kuramaz. Düğümlerin diğer sanal makinelerle veya şirket içi bir ağla güvenli bir şekilde iletişim kurmasına izin vermek için, havuzu [bir Azure sanal ağı (VNet) alt ağında](batch-virtual-network.md)sağlayabilirsiniz. Bunu yaptığınızda, düğümlerine genel IP adreslerinden erişebilirsiniz. Bu genel IP adresleri Batch tarafından oluşturulur ve havuzun ömrü boyunca değişebilir. Ayrıca, denetlediğiniz [statik genel IP adreslerine sahip bir havuz oluşturabilir](create-pool-public-ip.md) ve bu sayede beklenmedik şekilde değişmemesini sağlayabilirsiniz.

## <a name="pools"></a>Havuzlar

Havuz, uygulamanızın üzerinde çalıştığı düğümlerin koleksiyonudur.

Azure Batch havuzları temel Azure işlem platformu üzerinde derlenir. Bunlar, bir havuzdaki işlem düğümlerinin sayısı için büyük ölçekli ayırma, uygulama yüklemesi, veri dağıtımı, sistem durumu izleme ve esnek ayarlama ([ölçeklendirme](#automatic-scaling-policy)) sağlar.

Bir havuza eklenen her düğüme benzersiz bir ad ve IP adresi atanır. Bir düğüm havuzdan kaldırıldığında, işletim sistemi ya da dosyalara yapılan tüm değişiklikler kaybedilir ve düğümün adı ile IP adresi gelecekte kullanım için boşta kalır. Bir düğüm havuzdan ayrıldığında ömrü sona erer.

Bir havuz yalnızca içinde oluşturulduğu Batch hesabı tarafından kullanılabilir. Bir Batch hesabı, çalıştırılacağı uygulamaların kaynak gereksinimlerini karşılamak için birden çok havuz oluşturabilir.

Havuz el ile veya yapılacak işleri belirttiğinizde [Batch hizmeti tarafından otomatik olarak](#autopools) oluşturulabilir. Bir havuz oluşturduğunuzda aşağıdaki öznitelikleri belirtebilirsiniz:

- [Düğüm işletim sistemi ve sürümü](#operating-system-and-version)
- [Düğüm türü ve hedef düğüm sayısı](#node-type-and-target)
- [Düğüm boyutu](#node-size)
- [Otomatik ölçeklendirme ilkesi](#automatic-scaling-policy)
- [Görev zamanlama ilkesi](#task-scheduling-policy)
- [İletişim durumu](#communication-status)
- [Başlangıç görevleri](#start-tasks)
- [Uygulama paketleri](#application-packages)
- [Sanal ağ ve güvenlik duvarı yapılandırması](#virtual-network-vnet-and-firewall-configuration)
- [Ömür](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> Batch hesaplarının, bir Batch hesabındaki çekirdek sayısını sınırlayan varsayılan bir kotası vardır. Çekirdek sayısı, işlem düğümü sayısına karşılık gelir. Varsayılan kotaları ve [kota artırma](batch-quota-limit.md#increase-a-quota) yönergelerini [Azure Batch hizmeti için kotalar ve limitler](batch-quota-limit.md) bölümünde bulabilirsiniz. Havuzunuzun hedef düğüm sayısına ulaşmamasının nedeni çekirdek kotası olabilir.

## <a name="operating-system-and-version"></a>İşletim sistemi ve sürümü

Bir Batch havuzu oluşturduğunuzda, havuzdaki her bir işlem düğümünde çalıştırmak istediğiniz Azure sanal makine yapılandırmasını ve işletim sistemi türünü belirtirsiniz.

## <a name="configurations"></a>Yapılandırmalar

Toplu Işte kullanılabilen iki tür havuz yapılandırması vardır.

> [!IMPORTANT]
> Şu anda iki yapılandırmayı kullanarak havuzlar oluşturmadığınıza karşın, yeni havuzların yapılandırma Cloud Services değil, sanal makine yapılandırması kullanılarak yapılandırılması gerekir. Tüm geçerli ve yeni toplu Işlem özellikleri, sanal makine yapılandırma havuzları tarafından desteklenecektir. Cloud Services yapılandırma havuzları tüm özellikleri desteklemez ve yeni bir özellik planlanmaz. Yeni ' CloudServiceConfiguration ' havuzları oluşturamaz veya [29 şubat 2024 ' den sonra](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)var olan havuzlara yeni düğümler ekleyemeyeceksiniz.

### <a name="virtual-machine-configuration"></a>Sanal makine yapılandırması

**Sanal makine yapılandırması** , havuzun Azure sanal makinelerinden oluştuğunu belirtir. Bu VM'ler Linux veya Windows görüntülerinden oluşturulabilir.

[Batch düğüm Aracısı](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) , havuzdaki her düğüm üzerinde çalışan ve düğüm ile Batch hizmeti arasında komut ve denetim arabirimini sağlayan bir programdır. Farklı işletim sistemleri için SKU olarak bilinen düğüm aracısının farklı uygulamaları vardır. Sanal Makine Yapılandırmasını temel alan bir havuz oluşturduğunuzda, yalnızca düğümlerin boyutunu ve onları oluşturmak için kullanılan görüntülerin kaynağını değil, aynı zamanda **sanal makine görüntü başvurusunu** ve düğümlere yüklenecek Batch **düğümü aracı SKU'sunu** da belirtmeniz gerekir. Bu havuz özelliklerini belirtme hakkında daha fazla bilgi için bkz. [Azure Batch havuzlarında Linux işlem düğümlerini hazırlama](batch-linux-nodes.md). İsteğe bağlı olarak Market görüntülerinden oluşturulmuş havuz VM'lerine bir veya daha fazla boş veri diski ekleyebilir veya VM'leri oluşturmak için kullanılan özel görüntülere veri diskleri dahil edebilirsiniz. Veri disklerini dahil etmek için, diskleri kullanmak üzere bir VM içinden bağlamanız ve biçimlendirmeniz gerekir.

### <a name="cloud-services-configuration"></a>Cloud Services yapılandırması

> [!WARNING]
> Cloud Services yapılandırma havuzları [kullanım dışıdır](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Lütfen bunun yerine sanal makine yapılandırma havuzlarını kullanın. Daha fazla bilgi için bkz. [Cloud Services Batch havuzu yapılandırmasını sanal makineye geçirme](batch-pool-cloud-service-to-virtual-machine-configuration.md).

**Cloud Services yapılandırması** , havuzun Azure Cloud Services düğümlerinden oluştuğunu belirtir. Cloud Services yalnızca Windows işlem düğümleri sağlar.

Cloud Services yapılandırma havuzları için kullanılabilir işletim sistemleri, [Azure Konuk işletim sistemi sürümleri ve SDK uyumluluk matrisi](../cloud-services/cloud-services-guestos-update-matrix.md)' nda listelenir ve kullanılabilir işlem düğümü boyutları, [Cloud Services için Boyutlar](../cloud-services/cloud-services-sizes-specs.md)bölümünde listelenmiştir. Cloud Services düğümleri içeren bir havuz oluşturduğunuzda, düğüm boyutunu ve *Işletim sistemi ailesini* belirtirsiniz (hangi .NET sürümlerinin işletim sistemine yükleneceğini belirler). Cloud Services, Windows çalıştıran sanal makinelerden daha hızlı bir şekilde Azure 'a dağıtılır. Windows işlem düğümlerinden oluşan havuzlar oluşturmak istiyorsanız, Cloud Services'ın dağıtım süresi açısından daha iyi bir performans sunduğunu görebilirsiniz.

Cloud Services içindeki çalışan rollerinde olduğu gibi bir *Işletim sistemi sürümü* de belirtebilirsiniz. `Latest (*)`Düğümlerin otomatik olarak yükseltilmesi Için *Işletim sistemi sürümü* için belirtmenizi öneririz ve yeni yayınlanan sürümlere geçmek için gereken bir iş yoktur. Belirli bir işletim sistemi sürümünün seçildiği birincil kullanım durumu, sürümün güncelleştirilmesine izin vermeden önce geriye dönük uyumluluk testinin gerçekleştirilmesine izin vererek uygulama uyumluluğunun sağlandığından emin olmaktır. Doğrulamadan sonra, havuzun *Işletim sistemi sürümü* güncelleştirilemeyebilir ve yeni işletim sistemi görüntüsü yüklenebilirler. Çalışan tüm görevler kesintiye uğrar ve yeniden kuyruğa.

### <a name="node-agent-skus"></a>Düğüm Aracısı SKU 'Ları

Havuz oluştururken VHD'nizin temel görüntüsünün işletim sistemine bağlı olarak uygun **nodeAgentSkuId** değerini seçmeniz gerekir. [Desteklenen düğüm Aracısı SKU 'Larını Listele](/rest/api/batchservice/list-supported-node-agent-skus) işlemini çağırarak, Işletim sistemi görüntüsü başvurularına kullanılabilir düğüm Aracısı SKU kimliklerinin eşlemesini alabilirsiniz.

### <a name="custom-images-for-virtual-machine-pools"></a>Sanal Makine havuzları için özel görüntüler

Özel görüntülerle havuz oluşturma hakkında bilgi edinmek için bkz. [paylaşılan görüntü galerisini kullanarak özel bir havuz oluşturma](batch-sig-images.md).

Alternatif olarak, [yönetilen bir görüntü](batch-custom-images.md) kaynağı kullanarak sanal makineler için özel bir havuz oluşturabilirsiniz. Azure VM'lerinden özel Linux görüntüleri hazırlama hakkında bilgi için bkz. [Sanal makine veya VHD görüntüsü oluşturma](../virtual-machines/linux/capture-image.md). Azure sanal makinelerinden özel Windows görüntüleri hazırlama hakkında daha fazla bilgi için bkz. [Azure'da genelleştirilmiş VM'nin yönetilen görüntüsünü oluşturma](../virtual-machines/windows/capture-image-resource.md).

### <a name="container-support-in-virtual-machine-pools"></a>Sanal Makine havuzlarında kapsayıcı desteği

Batch API'lerini kullanarak Sanal Makine Yapılandırma havuzu oluştururken havuzu görevleri Docker kapsayıcılarında çalıştıracak şekilde ayarlayabilirsiniz. Şu anda Docker kapsayıcılarını destekleyen bir görüntü kullanarak havuz oluşturmanız gerekir. Azure Market'teki Windows Server 2016 Datacenter with Containers görüntüsünü kullanın veya Docker Community Edition ya da Enterprise Edition ile gerekli sürücüleri içeren özel bir VM görüntüsü sağlayın. Havuz ayarları, kapsayıcı görüntülerini havuz oluşturulduğunda VM'lere kopyalayan bir [kapsayıcı yapılandırması](/rest/api/batchservice/pool/add) içermelidir. Havuzda çalışan görevler, kapsayıcı görüntülerine ve kapsayıcı çalıştırma seçeneklerine başvurabilir.

Daha fazla bilgi için bkz. [Azure Batch’te Docker kapsayıcı uygulamaları çalıştırma](batch-docker-container-workloads.md).

## <a name="node-type-and-target"></a>Düğüm türü ve hedefi

Bir havuz oluşturduğunuzda istediğiniz düğüm türlerini ve her biri için hedef numarayı belirtebilirsiniz. İki düğüm türü şunlardır:

- **Ayrılmış düğümler.** Adanmış bir işlem düğümleri, iş yükleriniz için ayrılmıştır. Bunlar düşük öncelikli düğümlerinden daha pahalıdır, ancak hiçbir zaman etkisiz hale getirilmeyeceği garanti edilir.
- **Düşük öncelikli düğümler.** Düşük öncelikli düğümler, Batch iş yüklerinizi çalıştırmak için Azure’daki fazla kapasiteden yararlanır. Düşük öncelikli düğümler, özel düğümlerden saat başına daha ucuz ve önemli işlem gücü gerektiren iş yüklerini etkinleştirir. Daha fazla bilgi için bkz. [Batch ile düşük öncelikli VM’ler kullanma](batch-low-pri-vms.md).

Düşük öncelikli düğümler, Azure 'un fazlalık kapasitesi yetersiz olduğunda bozulabilir. Görevler çalıştırılırken bir düğüm etkisiz hale getirilirse, işlem düğümü yeniden kullanılabilir olduğunda görevler yeniden kuyruğa alınır ve tekrar çalıştırılır. Düşük öncelikli düğümler, iş tamamlama süresinin esnek olduğu ve işin çok sayıda düğüme dağıtıldığı iş yükleri için iyi bir seçenektir. Senaryonuzda düşük öncelikli düğümleri kullanmaya karar vermeden önce, önalım kaynaklı iş kaybının minimum düzeyde olacağından ve kolayca yeniden oluşturulabileceğinden emin olun.

Aynı havuzda hem düşük öncelikli hem de adanmış işlem düğümleri olabilir. Her düğüm türünün kendi hedef ayarı vardır ve bu, istenen sayıda düğüm belirtebilirsiniz.

Bazı durumlarda havuzunuz istenilen düğüm sayısına ulaşmayabileceğinden işlem düğümleri sayısı *hedef* olarak adlandırılır. Örneğin, bir havuz ilk olarak Batch hesabınızın [çekirdek kotasına](batch-quota-limit.md) ulaşırsa hedefe ulaşamayabilir. Veya, havuza en fazla düğüm sayısını sınırlayan bir otomatik ölçeklendirme formülü uyguladıysanız, havuz hedefe ulaşamayabilir.

Hem düşük öncelikli hem de adanmış düğümlere yönelik fiyatlandırma bilgileri için bkz. [Batch fiyatlandırması](https://azure.microsoft.com/pricing/details/batch/).

## <a name="node-size"></a>Düğüm boyutu

Azure Batch havuzu oluşturduğunuzda, neredeyse Azure'da bulunan tüm VM aileleri ve boyutları arasından seçim yapabilirsiniz. Azure farklı iş yükleri için [HPC](../virtual-machines/sizes-hpc.md) veya [GPU etkin](../virtual-machines/sizes-gpu.md) VM boyutları da dahil olmak üzere çeşitli VM boyutları sunar. Düğüm boyutlarının yalnızca bir havuz oluşturulduğu sırada seçilebilir olduğunu unutmayın. Diğer bir deyişle, havuz oluşturulduktan sonra düğüm boyutu değiştirilemez.

Daha fazla bilgi için bkz. [Azure Batch havuzunda işlem düğümleri için VM boyutunu seçme](batch-pool-vm-sizes.md).

## <a name="automatic-scaling-policy"></a>Otomatik ölçeklendirme ilkesi

Dinamik iş yükleri için bir havuza otomatik ölçeklendirme ilkesi uygulayabilirsiniz. Batch hizmeti formülünüzü düzenli olarak değerlendirir ve işlem senaryonuz için geçerli iş yüküne ve kaynak kullanımına göre havuzdaki düğümlerin sayısını dinamik olarak ayarlar. Bu, yalnızca ihtiyacınız olan kaynakları kullanarak ve ihtiyacınız olmayanları bırakarak uygulamanızı çalıştırmaya ilişkin genel maliyeti düşürmenizi sağlar.

Bir [otomatik ölçeklendirme formülü](batch-automatic-scaling.md#autoscale-formulas) yazıp bu formülü bir havuzla ilişkilendirerek otomatik ölçeklendirmeyi etkinleştirebilirsiniz. Batch hizmeti, sonraki ölçeklendirme aralığı (sizin yapılandırabileceğiniz bir aralık) için havuzdaki düğümlerin hedef sayısını belirlemek amacıyla bu formülü kullanır. Bir havuzu oluştururken otomatik ölçeklendirme ayarlarını belirtebilir ya da bir havuz üzerinde ölçeklendirmeyi daha sonra etkinleştirebilirsiniz. Ölçeklendirme özellikli bir havuz üzerinde ölçeklendirme ayarlarını da güncelleştirebilirsiniz.

Örnek olarak, belki de bir iş yürütülmesi için çok sayıda görev göndermeniz gerekir. Havuza, kuyruğa alınmış görevlerin mevcut sayısı ve iş içindeki görevlerin tamamlanma oranı temelinde havuzdaki düğüm sayısını ayarlayan bir ölçeklendirme formülü atayabilirsiniz. Batch hizmeti düzenli aralıklarla formülü değerlendirir ve havuzu, iş yükü ile diğer formül ayarlarınız temelinde yeniden boyutlandırır. Hizmet, kuyrukta çok sayıda görev olduğunda düğüm ekler, kuyrukta veya çalışan görev olmadığında ise düğümleri kaldırır.

Ölçeklendirme formülü aşağıdaki ölçümleri temel alabilir:

- **Zaman ölçümleri** belirtilen saat sayısınca beş dakikada bir toplanan istatistikleri temel alır.
- **Kaynak ölçümleri** CPU kullanımı, bant genişliği kullanımı, bellek kullanımı ve düğüm sayısını temel alır.
- **Görev ölçümleri**; *Etkin* (kuyruğa alınmış) *Çalışıyor* veya *Tamamlandı* gibi görev durumlarını temel alır.

Otomatik ölçeklendirme bir havuzdaki işlem düğümlerinin sayısını azalttığında, azaltma işlemi sırasında çalışan görevlerin nasıl ele alınacağını göz önünde bulundurmanız gerekir. Toplu Işlem buna uyum sağlamak için formüllerinize dahil ettiğiniz [*düğüm ayırmayı kaldırma seçeneğini*](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption) sağlar. Örneğin, çalışmakta olan görevlerin hemen durdurulacağını, ardından başka bir düğüm üzerinde yeniden kuyruğa alınacağını veya düğüm havuzdan kaldırılmadan önce bitmesine izin verileceğini belirtebilirsiniz. Düğüm ayırmayı kaldırma seçeneğini olarak ayarlamanın `taskcompletion` veya `retaineddata` Tüm görevler tamamlanana kadar havuz yeniden boyutlandırma işlemlerini önlemesine veya sırasıyla tüm görev saklama dönemlerinin süresi dolmaya engel olacağını unutmayın.

Bir uygulamayı otomatik olarak ölçeklendirme hakkında daha fazla bilgi için bkz. [Azure Batch havuzunda işlem düğümlerini otomatik olarak ölçeklendirme](batch-automatic-scaling.md).

> [!TIP]
> İşlem kaynağından en iyi şekilde faydalanabilmek için işin sonundaki düğüm sayısını sıfır olarak ayarlayın ancak çalışan görevlerin bitmesine izin verin.

## <a name="task-scheduling-policy"></a>Görev zamanlama ilkesi

[Düğüm başına en fazla görev](batch-parallel-node-tasks.md) yapılandırma seçeneği havuzdaki her bir işlem düğümünde paralel olarak çalıştırabilecek en fazla görev sayısını belirler.

Varsayılan yapılandırma bir düğümde tek seferde bir görevin çalışacağını belirtir, ancak bir düğümde aynı anda iki veya daha fazla görev yürütülmesinin faydalı olduğu senaryolar da vardır. Düğüm başına birden fazla görevden nasıl yararlanabileceğinizi görmek için [eşzamanlı düğüm görevleri](batch-parallel-node-tasks.md) makalesindeki [örnek senaryoya](batch-parallel-node-tasks.md#example-scenario) bakın.

Ayrıca, Batch 'in görevleri bir havuzdaki tüm düğümlerde eşit olarak paketleyemeyeceğini veya bir düğümdeki görevleri başka bir düğüme atamadan önce en fazla sayıda göreve paketleyemeyeceğini belirleyen bir *Fill türü* de belirtebilirsiniz.

## <a name="communication-status"></a>İletişim durumu

Çoğu senaryoda görevler bağımsız olarak çalışır ve birbirleriyle iletişim kurmaları gerekmez. Ancak, [MPI senaryolarında](batch-mpi.md) olduğu gibi içinde görevlerin iletişim kurması gereken bazı uygulamalar olabilir.

Bir havuz içindeki düğümlerin çalışma zamanında iletişim kurabilmesi için, düğümler **arası iletişime** izin vermek üzere bir havuz yapılandırabilirsiniz. Düğümler arası iletişim etkinleştirildiğinde, Cloud Services havuzlarındaki düğümler 1100'den büyük bağlantı noktaları üzerinde birbiriyle iletişim kurabilir ve Sanal Makine Yapılandırması havuzları hiçbir bağlantı noktası üzerinde trafiği kısıtlamaz.

Düğümler arası iletişimin etkinleştirilmesi, düğümlerin kümeler içindeki yerleşimini da etkiler ve dağıtım kısıtlamaları nedeniyle havuzdaki en fazla düğüm sayısını sınırlayabilir. Uygulamanız düğümler arasında iletişim gerektirmiyorsa Batch hizmeti, artan paralel işleme gücünü etkinleştirmek amacıyla birçok kümeden ve veri merkezinden çok sayıda düğümü havuza ayırabilir.

## <a name="start-tasks"></a>Başlangıç görevleri

İsterseniz, düğüm havuza katıldığında ve düğüm yeniden başlatıldığında veya yeniden oluşturulduğunda her düğümde çalıştırılacak bir [Başlangıç görevi](jobs-and-tasks.md#start-task) ekleyebilirsiniz. Başlangıç görevi, görevlerinizin işlem düğümlerinde çalıştıracağı uygulamaları yükleme gibi görevlerin yürütülmesi için özellikle işlem düğümlerinin hazırlanmasında yararlıdır.

## <a name="application-packages"></a>Uygulama paketleri

Havuzdaki işlem düğümlerine dağıtım yapacak uygulama paketlerini belirtebilirsiniz. Uygulama paketleri, görevlerinizin çalıştırdığı uygulamaların dağıtımını ve sürüm oluşturma işlemlerini basitleştirir. Havuz için belirttiğiniz uygulama paketleri, bir düğüm her yeniden başlatıldığında veya görüntüsü yeniden oluşturduğunda o havuza katılan her düğüme yüklenir.

Uygulama paketlerini kullanarak uygulamalarınızı Batch düğümlerine dağıtma hakkında daha fazla bilgi için bkz. [Batch uygulama paketleriyle işlem düğümlerine uygulama dağıtımı](batch-application-packages.md).

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Sanal ağ ve güvenlik duvarı yapılandırması

Batch'te işlem düğümlerinden oluşan bir havuz sağladığınızda, havuzu bir Azure [sanal ağının](../virtual-network/virtual-networks-overview.md) alt ağı ile ilişkilendirebilirsiniz. Azure sanal ağı kullanmak için Batch istemci API'sinin Azure Active Directory (AD) kimlik doğrulamasını kullanması gerekir. Azure AD için Azure Batch desteği, [Batch hizmeti çözümlerinin kimliğini Active Directory ile doğrulama](batch-aad-auth.md) makalesinde belirtilmiştir.

### <a name="vnet-requirements"></a>Sanal ağ gereksinimleri

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Bir sanal ağda Batch havuzu oluşturma hakkında daha fazla bilgi için bkz. [Sanal ağınızda sanal makine havuzu oluşturma](batch-virtual-network.md).

> [!TIP]
> Düğümlere erişmek için kullanılan genel IP adreslerinin değişmemesini sağlamak için, [denetlediğiniz belirtilen genel IP adreslerine sahip bir havuz oluşturabilirsiniz](create-pool-public-ip.md).

## <a name="pool-and-compute-node-lifetime"></a>Havuz ve işlem düğümü ömrü

Azure Batch çözümünüzü tasarlarken, havuzların nasıl ve ne zaman oluşturulduğunu ve bu havuzlardaki işlem düğümlerinin ne kadar süreyle kullanılabilir tutulacağını belirtmeniz gerekir.

Spektrumun bir ucunda, gönderdiğiniz her iş için bir havuz oluşturabilir ve görevlerin yürütülmesi biter bitmez havuzu silebilirsiniz. Bu, düğümler yalnızca gerektiğinde ayrıldığından ve boşta kaldıktan sonra kapatılabileceği için kullanımı en üst düzeye çıkarır. Bu, işin düğümlerin ayrılmasını beklemesi gerektiği anlamına geldiğinden, düğümlerin ayrı ayrı ayrıldığı ve başlangıç görevinin tamamlandığı anda görevlerin yürütme için zamanlandığını aklınızda bulundurulmalıdır. Batch, görevler düğümlere atamadan önce havuzdaki tüm düğümlerin kullanılabilir olmasını *beklemez*. Böylece kullanılabilir tüm düğümlerden en iyi şekilde faydalanılmasını sağlar.

Spektrumun diğer ucunda, işlerin hemen başlatılması en yüksek önceliğe sahipse işler gönderilmeden önce bir havuz oluşturabilir ve bu havuzun düğümlerini kullanıma sunabilirsiniz. Bu senaryoda görevler hemen başlayabilir, ancak görevlerin atanmasını beklerken düğümler boşta kalmaya devam edebilir.

Birleşik yaklaşım genellikle bir değişkeni işlemek için kullanılır, ancak devam eden yük. Birden çok işin gönderildiği bir havuzunuz olabilir ve iş yüküne göre düğüm sayısını yukarı veya aşağı ölçeklendirebilirler. Mevcut yüke bağlı olarak reaktif bir şekilde ya da yük öngörülebiliyorsa proaktif olarak bu işlemi yapabilirsiniz. Daha fazla bilgi için bkz. [Otomatik ölçeklendirme ilkesi](#automatic-scaling-policy).

## <a name="autopools"></a>Oto havuzları

Bir [oto havuzu](/rest/api/batchservice/job/add#autopoolspecification) , bir iş gönderildiğinde, havuzda çalışacak işlerden önce oluşturulması yerine Batch hizmeti tarafından oluşturulan bir havuzudur. Batch hizmeti, belirttiğiniz özelliklere göre bir oto havuzunun ömrünü yönetir. Çoğu zaman, bu havuzlar işleri tamamlandıktan sonra otomatik olarak silinecek şekilde ayarlanır.

## <a name="security-with-certificates"></a>Sertifikalar ile güvenlik

[Azure Depolama hesabı](accounts.md#azure-storage-accounts) anahtarı gibi, görevler için hassas bilgileri şifrelerken ya da bunların şifrelerini çözerken genelde sertifikalar kullanmanız gerekir. Bunu desteklemek için düğümlere sertifikalar yükleyebilirsiniz. Şifrelenmiş parolalar komut satırı parametreleri aracılığıyla düğümlere geçirilir ya da görev kaynaklarından birine eklenir ve yüklü sertifikalar bunların şifrelerini çözmek için kullanılabilir.

Batch hesabına bir sertifika eklemek için [Sertifika ekle](/rest/api/batchservice/certificate/add) işlemini (Batch REST) ya da [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) yöntemini (Batch .NET) kullanırsınız. Sonra sertifikayı yeni ya da mevcut bir havuzla ilişkilendirebilirsiniz.

Sertifika bir havuzla ilişkilendirildiğinde, Batch hizmeti havuzdaki her düğüme sertifikayı yükler. Batch hizmeti, düğüm başlatıldığında uygun sertifikaları ( [Başlangıç görevi](jobs-and-tasks.md#start-task) ve [İş Yöneticisi görevi](jobs-and-tasks.md#job-manager-task)dahil) başlatmadan önce yüklenir.

Mevcut bir havuza bir sertifika eklerseniz, sertifikanın düğümlere uygulanması için işlem düğümlerini yeniden başlatmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [İşler ve görevler](jobs-and-tasks.md)hakkında bilgi edinin.
- [Havuz ve düğüm arka plan işlemlerinde oluşan sorunları algılamaya ve önlemenize ](batch-pool-node-error-checking.md)öğrenin.
