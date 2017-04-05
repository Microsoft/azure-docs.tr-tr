---
title: "Geliştiriciler için Azure Batch’e genel bakış | Microsoft Docs"
description: "Batch hizmeti özelliklerini ve API’lerini geliştirme açısından öğrenin."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 416b95f8-2d7b-4111-8012-679b0f60d204
ms.service: batch
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 03/08/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f323afdea34e973f3ecdd54022f04b3f0d86afb1
ms.lasthandoff: 04/03/2017


---
# <a name="develop-large-scale-parallel-compute-solutions-with-batch"></a>Batch içe büyük ölçekli paralel işlem çözümleri geliştirme

Azure Batch hizmetinin temel bileşenlerine ilişkin bu genel bakışta, Batch geliştiricilerinin büyük ölçekli paralel işlem çözümleri derlemek üzere kullanabileceği birincil hizmetler ve kaynaklar ele alınmaktadır.

Doğrudan [REST API][batch_rest_api] çağrıları yayınlayan dağıtılmış bir işlem uygulaması veya hizmet geliştirirken ya da [Batch SDK'ları](batch-apis-tools.md#batch-development-apis) kullanırken, bu makalede ele alınan kaynak ve özelliklerden yararlanabilirsiniz.

> [!TIP]
> Batch hizmetine daha yüksek düzeyde bir giriş için bkz. [Azure Batch temel bilgileri](batch-technical-overview.md).
>
>

## <a name="batch-service-workflow"></a>Batch hizmeti iş akışı
Aşağıdaki üst düzey iş akışı, paralel iş yüklerini işlemek üzere Batch hizmetini kullanan neredeyse tüm uygulamalar ve hizmetler için tipiktir:

1. İşlemek istediğiniz **veri dosyalarını** bir [Azure Depolama][azure_storage] hesabına yükleyin. Batch, Azure Blob depolama alanına erişim için yerleşik destek içerir ve görevler çalıştırıldığında görevleriniz bu dosyaları [işlem düğümlerine](#compute-node) indirebilir.
2. Görevlerinizin çalıştıracağı **uygulama dosyalarını** karşıya yükleyin. Bu dosyalar ikili dosyalar ya da komut dosyaları ve onların bağımlılıkları olabilir ve işlerinizdeki görevler tarafından yürütülür. Görevleriniz bu dosyaları Storage hesabınızdan indirebilir veya uygulama yönetimi ve dağıtımı için Batch hizmetinin [uygulama paketleri](#application-packages) özelliğini kullanabilirsiniz.
3. Bir işlem düğümleri [havuzu](#pool) oluşturun. Bir havuz oluşturduğunuzda havuz için işlem düğümü sayısını, boyutlarını ve işletim sistemini belirtin. İşinizdeki her bir görev çalıştığında havuzunuzdaki düğümlerden birini yürütmek üzere atanır.
4. Bir [iş](#job) oluşturun. İş bir görev koleksiyonunu yönetir. Her işi, işin görevlerinin çalışacağı belirli bir havuz ile ilişkilendirin.
5. İşe [görevler](#task) ekleyin. Her görev, Storage hesabınızdan indirdiği veri dosyalarını işlemek üzere karşıya yüklediğiniz uygulamayı ve komut dosyasını çalıştırır. Her görev tamamlandığında çıktısını Azure Depolama hizmetine yükleyebilir.
6. İşin ilerleme durumunu izleyin ve görev çıktısını Azure Depolama’dan alın.

Aşağıdaki bölümlerde, bunları ve dağıtılmış hesaplama senaryonuzu etkinleştirecek Batch’in diğer kaynakları ele alınmıştır.

> [!NOTE]
> Batch hizmetini kullanmak için bir [Batch hesabı](batch-account-create-portal.md) gereklidir. Ayrıca, neredeyse tüm çözümler dosya depolama ve alma amacıyla [Azure Depolama][azure_storage] hesabı kullanır. Batch şu anda, [Azure Depolama hesapları hakkında](../storage/storage-create-storage-account.md) bölümündeki [Depolama hesabı oluşturma](../storage/storage-create-storage-account.md#create-a-storage-account) adlı 5. adımda açıklandığı gibi, sadece **Genel amaçlı** depolama hesabı türünü destekler.
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
  * [Çok örnekli görev (MPI)](#multi-instance-tasks)
  * [Görev bağımlılıkları](#task-dependencies)
* [Uygulama paketleri](#application-packages)

## <a name="account"></a>Hesap
Bir Batch hesabı Batch hizmeti dahilinde benzersiz şekilde tanımlanan bir varlıktır. Tüm işlemler bir Batch hesabıyla ilişkilendirilir. Batch hizmeti ile işlemler gerçekleştirdiğinizde size hem hesap adı hem de hesap anahtarlarından biri gerekir. [Azure portalı kullanarak Azure Batch hesabı oluşturabilirsiniz](batch-account-create-portal.md).

## <a name="compute-node"></a>İşlem düğümü
İşlem düğümü, uygulama iş yükünüzün bir kısmını işlemeye ayrılmış bir Azure sanal makinesidir (VM). Bir düğümün boyutu CPU çekirdeklerinin sayısını, bellek kapasitesini ve düğüme ayrılan yerel dosya sistemi boyutunu belirler. Azure Cloud Services veya Virtual Machines Market görüntülerini kullanarak Windows veya Linux düğümleri içeren havuzlar oluşturabilirsiniz. Bu seçenekler hakkında daha fazla bilgi için aşağıdaki [Havuz](#pool) bölümüne bakın.

Düğümler, düğümün işletim sistemi ortamı tarafından desteklenen herhangi bir yürütülebilir dosyayı ya da komut dosyasını çalıştırabilir. Buna Windows için \*.exe, \*.cmd, \*.bat ve PowerShell komut dosyaları ile Linux için ikili dosyalar, kabuk ve Python komut dosyaları dahildir.

Batch içindeki tüm işlem düğümleri ayrıca şunları içerir:

* Standart bir [klasör yapısı](#files-and-directories) ve görevlere göre başvurulabilen ilişkili [ortam değişkenleri](#environment-settings-for-tasks).
* Erişimi denetlemek için yapılandırılan **Güvenlik Duvarı** ayarları.
* Hem Windows (Uzak Masaüstü Protokolü (RDP)) hem de Linux (Güvenli Kabuk (SSH)) düğümlerine [uzaktan erişim](#connecting-to-compute-nodes).

## <a name="pool"></a>Havuz
Havuz, uygulamanızın üzerinde çalıştığı bir düğüm koleksiyonudur. Havuz sizin tarafınızdan elle ya da siz yapılacak işleri belirttiğinizde Batch hizmeti tarafından otomatik olarak oluşturulabilir. Uygulamanızın kaynak gereksinimlerini karşılayan bir havuz oluşturup yönetebilirsiniz. Bir havuz yalnızca içinde oluşturulduğu Batch hesabı tarafından kullanılabilir. Bir Batch hesabı birden fazla havuza sahip olabilir.

Azure Batch havuzları temel Azure işlem platformu üzerinde derlenir. Bu havuzlar büyük ölçekli ayırma, uygulama yüklemesi, veri dağıtımı, durum izleme ve bir havuzdaki işlem düğümü sayısının esnek şekilde ayarlanmasını ([ölçeklendirme](#scaling-compute-resources)) sağlar.

Bir havuza eklenen her düğüme benzersiz bir ad ve IP adresi atanır. Bir düğüm havuzdan kaldırıldığında, işletim sistemi ya da dosyalara yapılan tüm değişiklikler kaybedilir ve düğümün adı ile IP adresi gelecekte kullanım için boşta kalır. Bir düğüm havuzdan ayrıldığında ömrü sona erer.

Bir havuz oluşturduğunuzda aşağıdaki öznitelikleri belirtebilirsiniz:

* İşlem düğümü **işletim sistemi** ve **sürümü**

    Havuzunuzdaki düğümler için bir işletim sistemi seçtiğiniz iki seçeneğiniz vardır: **Sanal Makine Yapılandırması** ve **Cloud Services Yapılandırması**.

    **Sanal Makine Yapılandırması**, [Azure Sanal Makineler Marketinden][vm_marketplace] işlem düğümleri için hem Linux hem de Windows görüntüleri sağlar.
    Sanal Makine Yapılandırması düğümleri içeren bir havuz oluşturduğunuzda yalnızca düğümlerin boyutunu değil, aynı zamanda **sanal makine görüntü başvurusunu** ve düğümlere yüklenecek Batch **düğümü aracı SKU’sunu** belirtmeniz gerekir. Bu havuz özelliklerini belirtme hakkında daha fazla bilgi için bkz. [Azure Batch havuzlarında Linux işlem düğümlerini hazırlama](batch-linux-nodes.md).

    **Cloud Services Yapılandırması** *yalnızca* Windows işlem düğümleri sağlar. Cloud Services Yapılandırması havuzları için kullanılabilen işletim sistemleri [Azure Konuk işletim sistemi sürümleri ve SDK uyumluluk matrisi](../cloud-services/cloud-services-guestos-update-matrix.md) içinde listelenmiştir. Cloud Services düğümleri içeren bir havuz oluşturduğunuzda yalnızca düğüm boyutunu ve *İşletim Sistemi Ailesi* ’ni belirtmeniz gerekir. Windows işlem düğümleri havuzları oluşturduğunuzda yaygın olarak Cloud Services kullanırsınız.

  * *İşletim Sistemi Ailesi*, işletim sistemiyle hangi .NET sürümlerinin yüklendiğini de belirler.
  * Cloud Services dahilindeki çalışan rollerinde olduğu gibi bir *İşletim Sistemi Sürümü* belirtebilirsiniz (çalışan rolleri hakkında daha fazla bilgi için [Cloud Services’e genel bakış](../cloud-services/cloud-services-choose-me.md) içindeki [Bana cloud services hakkında bilgi ver](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) bölümüne bakın).
  * Çalışan rollerinde olduğu gibi düğümlerin otomatik olarak yükseltilmesi için *İşletim Sistemi Sürümü* ’ne yönelik `*` belirtilmesi önerilir ve yeni yayımlanmış sürümlerin gereksinimini karşılamak için çalışma yapılması gerekmez. Belirli bir işletim sistemi sürümünün seçildiği birincil kullanım durumu, sürümün güncelleştirilmesine izin vermeden önce geriye dönük uyumluluk testinin gerçekleştirilmesine izin vererek uygulama uyumluluğunun sağlandığından emin olmaktır. Doğrulama sonrasında havuzun *İşletim Sistemi Sürümü* güncelleştirilebilir ve yeni işletim sistemi görüntüsü yüklenebilir; çalışan tüm görevler kesilir ve yeniden kuyruğa alınır.
* **Düğümlerin boyutu**

    **Cloud Services Yapılandırması** işlem düğümü boyutları [Cloud Services Boyutları](../cloud-services/cloud-services-sizes-specs.md) içinde listelenmiştir. Batch hizmeti `ExtraSmall`, `STANDARD_A1_V2` ve `STANDARD_A2_V2` dışında tüm Cloud Services boyutlarını destekler.

    **Sanal Makine Yapılandırması** işlem düğümü boyutları [Azure’da sanal makine boyutları](../virtual-machines/linux/sizes.md) (Linux) ve [Azure’da sanal makine boyutları](../virtual-machines/windows/sizes.md) (Windows) içinde listelenmiştir. Batch `STANDARD_A0` ve premium depolama alanına sahip olanlar (`STANDARD_GS`, `STANDARD_DS` ve `STANDARD_DSV2` serisi) dışında tüm Azure sanal makinelerini destekler.

    Bir işlem düğümü boyutu seçerken, düğümler üzerinde çalıştıracağınız uygulamaların özelliklerini ve gereksinimlerini göz önünde bulundurun. Uygulamanın çok iş parçacıklı olup olmadığı ve ne kadar bellek kullandığı gibi konular en uygun ve ekonomik düğüm boyutunu belirlemeye yardımcı olabilir. Genellikle düğümde aynı anda bir görevin çalışacağını varsayarak düğüm boyutu seçilir. Ancak, iş yürütme sırasında işlem düğümleri üzerinde birden fazla görevin (ve dolayısıyla birden fazla uygulama örneğinin) [paralel olarak çalışması](batch-parallel-node-tasks.md) mümkündür. Bu durumda, paralel görev yürütmeye yönelik artan talebi karşılamak üzere genellikle daha büyük bir düğüm boyutu seçilir. Daha fazla bilgi için bkz. [Görev zamanlama ilkesi](#task-scheduling-policy).

    Bir havuzdaki tüm düğümler aynı boyuttadır. Farklı sistem gereksinimlerine ve/veya yük düzeylerine sahip uygulamalar çalıştırmayı planlıyorsanız ayrı havuzlar oluşturmanız önerilir.
* **Hedef düğüm sayısı**

    Havuzda dağıtmak istediğiniz işlem düğümlerinin sayısıdır. Bazı durumlarda havuzunuz istenilen düğüm sayısına ulaşmayabileceğinden buna *hedef* adı verilir. Bir havuz Batch hesabınızın [çekirdek kotasına](batch-quota-limit.md) ulaşırsa veya havuza uyguladığınız en büyük düğüm sayısını sınırlandıran bir otomatik ölçeklendirme formülü varsa istenilen düğüm sayısına ulaşmayabilir (aşağıdaki "Ölçeklendirme ilkesi" bölümüne bakın).
* **Ölçeklendirme ilkesi**

    Dinamik iş yükleri için [otomatik ölçeklendirme formülü](#scaling-compute-resources) yazabilir ve bir havuza uygulayabilirsiniz. Batch hizmeti formülünüzü düzenli olarak değerlendirir ve belirtebileceğiniz çeşitli havuz, iş ve görev parametrelerine göre düğüm sayısını ayarlar.
* **Görev zamanlama ilkesi**

    [Düğüm başına en fazla görev](batch-parallel-node-tasks.md) yapılandırma seçeneği havuzdaki her bir işlem düğümünde paralel olarak çalıştırabilecek en fazla görev sayısını belirler.

    Varsayılan yapılandırma bir düğümde tek seferde bir görevin çalışacağını belirtir, ancak bir düğümde aynı anda iki veya daha fazla görev yürütülmesinin faydalı olduğu senaryolar da vardır. Düğüm başına birden fazla görevden nasıl yararlanabileceğinizi görmek için [eşzamanlı düğüm görevleri](batch-parallel-node-tasks.md) makalesindeki [örnek senaryoya](batch-parallel-node-tasks.md#example-scenario) bakın.

    Ayrıca Batch hizmetinin görevleri bir havuzdaki tüm düğümlere eşit olarak dağıtıp dağıtmadığını ya da görevleri başka bir düğüme atamadan önce her bir düğümü en fazla sayıda görev ile paketleyip paketlemediğini belirleyen bir *doldurma türü* belirleyebilirsiniz.
* İşlem düğümlerinin **iletişim durumu**

    Çoğu senaryoda görevler bağımsız olarak çalışır ve birbirleriyle iletişim kurmaları gerekmez. Ancak, [MPI senaryolarında](batch-mpi.md) olduğu gibi içinde görevlerin iletişim kurması gereken bazı uygulamalar olabilir.

    Bir havuzu **düğümler arasında iletişim kurmaya** izin verecek şekilde yapılandırarak bir havuzdaki düğümlerin çalışma zamanında iletişim kurmasını sağlayabilirsiniz. Düğümler arası iletişim etkinleştirildiğinde, Cloud Services havuzlarındaki düğümler 1100'den büyük bağlantı noktaları üzerinde birbiriyle iletişim kurabilir ve Sanal Makine Yapılandırması havuzları hiçbir bağlantı noktası üzerinde trafiği kısıtlamaz.

    Düğümler arası iletişimin etkinleştirilmesi kümelerin içindeki düğümlerin yerleşimini de etkiler ve dağıtım kısıtlamaları nedeniyle bir havuzdaki en fazla düğüm sayısını sınırlayabilir. Uygulamanız düğümler arasında iletişim gerektirmiyorsa Batch hizmeti artan paralel işleme gücünü etkinleştirmek amacıyla birçok kümeden ve veri merkezinden çok sayıda düğümü havuza ayırabilir.
* İşlem düğümleri için **başlangıç görevi**

    Bir düğüm havuza katıldığında ve bir düğümün yeniden başlatıldığı ya da görüntüsünün yeniden oluşturulduğu her durumda, düğüm üzerinde isteğe bağlı *başlangıç görevi* yürütülür. Başlangıç görevi, görevlerinizin işlem düğümlerinde çalıştıracağı uygulamaları yükleme gibi görevlerin yürütülmesi için özellikle işlem düğümlerinin hazırlanmasında yararlıdır.
* **Uygulama paketleri**

    Havuzdaki işlem düğümlerine dağıtım yapacak [uygulama paketlerini](#application-packages) belirtebilirsiniz. Uygulama paketleri, görevlerinizin çalıştırdığı uygulamaların dağıtımını ve sürüm oluşturma işlemlerini basitleştirir. Havuz için belirttiğiniz uygulama paketleri, bir düğüm her yeniden başlatıldığında veya görüntüsü yeniden oluşturduğunda o havuza katılan her düğüme yüklenir. Uygulama paketleri, Linux işlem düğümlerinde şu anda desteklenmemektedir.
* **Ağ yapılandırması**

    Havuz işlem düğümlerinin oluşturulması gereken Azure [sanal ağın (VNet)](../virtual-network/virtual-networks-overview.md) kimliğini belirtebilirsiniz. Daha fazla bilgi için [Havuz ağ yapılandırması](#pool-network-configuration) bölümüne bakın.

> [!IMPORTANT]
> Tüm Batch hesaplarının bir Batch hesabında bir dizi **çekirdekle** (ve dolayısıyla işlem düğümleriyle) sınırlanan varsayılan bir **kotası** vardır. [Kota artırma](batch-quota-limit.md#increase-a-quota) (Batch hesabınızdaki en yüksek çekirdek sayısı gibi) hakkında varsayılan kotalar ve yönergeleri [Azure Batch hizmeti için kotalar ve sınırlar](batch-quota-limit.md)’da bulabilirsiniz. Kendinizi "Neden havuzum X düğümden fazlasına ulaşamıyor?" sorusunu sorarken bulursanız nedeni bu çekirdek kotası olabilir.
>
>

## <a name="job"></a>İş
İş bir görev koleksiyonudur. Bir havuzdaki işlem düğümleri üzerindeki görevleri tarafından hesaplamanın nasıl gerçekleştirildiğini yönetir.

* İş, çalışmanın gerçekleştirileceği **havuzu** belirtir. Her iş için yeni havuz oluşturabilir veya çok sayıda iş için bir havuz kullanabilirsiniz. Bir iş zamanlaması ile ilişkili her iş için veya bir iş zamanlaması ile ilişkili tüm işler için havuz oluşturabilirsiniz.
* İsteğe bağlı bir **iş önceliği** belirtebilirsiniz. Bir iş devam eden işlerden daha yüksek öncelikle gönderilirse yüksek önceliğe sahip işin görevleri düşük önceliğe sahip iş görevlerinin önünde kuyruğa eklenir. Çalışmakta olan düşük öncelikli işlerdeki görevler engellenmez.
* İşleriniz için bazı sınırlar belirtmek üzere iş **kısıtlamaları** kullanabilirsiniz:

    Bir **duvar saati zamanı üst sınırı** ayarlayabilirsiniz; böylece bir iş belirtilen duvar saati zamanı üst sınırından daha uzun süre çalışırsa iş ve tüm görevleri sonlandırılır.

    Batch başarısız olan görevleri algılayabilir ve sonra yeniden deneyebilir. Bir görevin *her zaman* yeniden deneneceği veya *hiçbir zaman* yeniden denenmeyeceği gibi kısıtlama olarak **en fazla görev yeniden deneme sayısı** belirtebilirsiniz. Bir görevin yeniden denenmesi tekrar çalıştırmak üzere yeniden kuyruğa alınması anlamına gelir.
* İstemci uygulamanız bir işe görevler ekleyebilir ya da bir [iş yöneticisi görevi](#job-manager-task) belirtebilirsiniz. Bir iş yöneticisi görevi havuzdaki işlem düğümlerinden birinde çalıştırılan görevle birlikte bir iş için gereken görevleri oluşturmak üzere gerekli bilgileri içerir. İş yöneticisi görevi özellikle Batch tarafından işlenir; işin oluşturulmasının hemen ardından kuyruğa alınır ve başarısız olursa yeniden başlatılır. İş örneği oluşturulmadan görevleri tanımlamanın tek yolu olduğundan iş yöneticisi görevi, bir [iş zamanlaması](#scheduled-jobs) tarafından oluşturulan işler için *gereklidir*.
* Varsayılan olarak, işteki tüm görevler tamamlandığında iş etkin durumda kalır. Bu davranışı, işteki tüm görevler tamamlandığında işin otomatik olarak sonlandırılacağı şekilde değiştirebilirsiniz. Görevlerin hepsi tamamlanmış durumdayken işi otomatik olarak sonlandırmak için, işin **onAllTasksComplete** özelliğini (Batch .NET’te [OnAllTasksComplete][net_onalltaskscomplete]) *terminatejob* olarak ayarlayın.

    Batch hizmetinde, *hiç* görevi olmayan işler, tüm görevleri tamamlanmış işler olarak kabul edilir. Bu nedenle, bu seçenek genellikle [iş yöneticisi görevi](#job-manager-task) ile kullanılır. İş yöneticisi olmadan otomatik iş sonlandırmayı kullanmak istiyorsanız başlangıçta yeni işin **onAllTasksComplete** özelliğini *noaction* olarak ayarlamanız ve işe görev eklemeyi bitirdiğinizde bu ayarı *terminatejob* olarak değiştirmeniz gerekir.

### <a name="job-priority"></a>İş önceliği
Batch’de oluşturduğunuz işlere öncelik atayabilirsiniz. Batch hizmeti bir hesaptaki iş zamanlama sırasını belirlemek üzere işin öncelik değerini kullanır ([zamanlanmış iş](#scheduled-jobs) ile karıştırılmamalıdır). Öncelik değeri, -1000 en düşük öncelik ve 1000 en yüksek öncelik olmak üzere, -1000 ile 1000 aralığındadır. Bir işin önceliğini güncelleştirmek için [İşin önceliklerini güncelleştirme][rest_update_job] işlemini (Batch REST) ya da [CloudJob.Priority][net_cloudjob_priority] özelliğini (Batch .NET) çağırabilirsiniz.

Aynı hesapta, yüksek öncelikli işlerin düşük öncelikli işlere göre zamanlama üstünlüğü vardır. Bir hesaptaki yüksek öncelik değerine sahip iş farklı bir hesaptaki düşük öncelik değerine sahip başka bir işe karşı zamanlama üstünlüğüne sahip değildir.

Havuzlardaki iş zamanlaması bağımsızdır. Farklı havuzlar arasında, ilişkili havuzunda boşta düğüm eksikliği olması durumunda yüksek öncelikli işin önce zamanlanacağı kesin değildir. Aynı havuzda, aynı öncelik değerine sahip işlerin zamanlanma şansı eşittir.

### <a name="scheduled-jobs"></a>Zamanlanan işler
[İş zamanlamaları][rest_job_schedules] Batch hizmetinde yinelenen işler oluşturmanızı sağlar. Bir iş zamanlaması işlerin ne zaman çalıştırılacağını belirtir ve çalıştırılacak işlerin özelliklerini içerir. Zamanlama süresini (zamanlamanın ne kadar süreyle ve ne zaman etkin olacağını) ve bu zamanlanan sürede işlerin ne sıklıkta oluşturulacağını belirtebilirsiniz.

## <a name="task"></a>Görev
Görev bir işle ilişkili hesaplama birimidir. Bir düğüm üzerinde çalışır. Görevler yürütülmek için bir düğüme atanır veya bir düğüm serbest kalana kadar kuyruğa alınır. Kısacası görev, bitmesi gereken çalışmayı gerçekleştirmek üzere bir veya daha fazla program ya da komut dosyasını bir işlem düğümü üzerinde çalıştırır.

Bir görev oluşturduğunuzda aşağıdakileri belirtebilirsiniz:

* Görevin **komut satırı**. Uygulamanızı veya komut dosyanızı işlem düğümü üzerinde çalıştıran komut satırıdır.

    Komut satırı gerçekte bir kabuk altında çalışmaz. Bu nedenle, [ortam değişkeni](#environment-settings-for-tasks) genişletmesi gibi kabuk özelliklerinden (buna `PATH` dahildir) yerel olarak yararlanamaz. Bu özelliklerden yararlanmak için kabuğu komut satırında çağırmanız gerekir (örneğin Windows düğümlerinde `cmd.exe` veya Linux'ta `/bin/sh` başlatarak):

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Görevlerinizin, düğümün `PATH` veya başvuru ortamı değişkenlerinde olmayan bir uygulama ya da komut dosyasını çalıştırması gerekiyorsa kabuğu görev komut satırında açıkça çağırın.
* İşlenecek verileri içeren **kaynak dosyalar**. Bu dosyalar görevin komut satırı yürütülmeden önce genel amaçlı bir Azure Depolama hesabındaki Blob depolamadan düğüme otomatik olarak kopyalanır. Daha fazla bilgi için [Başlangıç görevi](#start-task) ve [Dosyalar ve dizinler](#files-and-directories) bölümlerine bakın.
* Uygulamanızın gerektirdiği **ortam değişkenleri**. Daha fazla bilgi için [Görevler için ortam ayarları](#environment-settings-for-tasks) bölümüne bakın.
* Görev yürütülürken tabi olunan **kısıtlamalar**. Örneğin, görevin çalışmasına izin verilen en uzun süre, başarısız olan bir görevin en fazla yeniden deneme sayısı ve görevin çalışma dizinindeki dosyaların elde tutulduğu en uzun süre kısıtlamalardan bazılarıdır.
* Görevin çalışmak üzere zamanlandığı işlem düğümünü dağıtmak için kullanılan **uygulama paketleri**. [Uygulama paketleri](#application-packages), görevlerinizin çalıştırdığı uygulamaların dağıtımını ve sürüm oluşturma işlemlerini basitleştirir. Görev düzeyinde uygulama paketleri, özellikle paylaşılan havuz ortamlarında çok yararlıdır. Bu ortamlarda, tek havuzda farklı işler çalıştırılır ve bir iş tamamlandığında havuz silinmez. İşinizin havuzdaki görevleri, düğümlerinden azsa uygulamanız yalnızca görevleri çalıştıran düğümlere dağıtıldığı için görev uygulama paketleri veri aktarımını azaltabilir.

Bir düğümde hesaplama yapmak üzere tanımladığınız görevlere ek olarak, aşağıdaki özel görevler de Batch hizmeti tarafından sağlanır:

* [Başlangıç görevi](#start-task)
* [İş yöneticisi görevi](#job-manager-task)
* [İş hazırlama ve bırakma görevleri](#job-preparation-and-release-tasks)
* [Çok örnekli görevler (MPI)](#multi-instance-tasks)
* [Görev bağımlılıkları](#task-dependencies)

### <a name="start-task"></a>Başlangıç görevi
**Başlangıç görevini** bir havuz ile ilişkilendirerek düğümlerinin işletim sistemi ortamını hazırlayabilirsiniz. Örneğin, görevlerinizin çalışacağı uygulamaları yükleme veya arka plan işlemlerini başlatma gibi eylemleri gerçekleştirebilirsiniz. Başlangıç görevi, havuzda kaldığı sürece düğümün havuza ilk eklendiği ve yeniden başlatıldığı ya da görüntüsünün yeniden oluşturulduğu zaman dahil olmak üzere, bir düğüm her başlatıldığında çalışır.

Başlangıç görevinin birincil avantajı, bir işlem düğümünü yapılandırmak ve görev yürütmede gereken uygulamaları yüklemek için gerekli tüm bilgileri içerebilmesidir. Bu nedenle bir havuzdaki düğüm sayısını artırmak, yeni bir hedef düğüm sayısı belirtmek kadar kolaydır. Başlangıç görevi, Batch hizmetine yeni düğümleri yapılandırmak ve onları görev kabul etmeye hazır hale getirmek için gerekli olan bilgileri sunar.

Her Azure Batch görevinde olduğu gibi, [Azure Depolama][azure_storage]'daki **kaynak dosyaların** bir listesini, yürütülecek **komut satırına** ek olarak belirtebilirsiniz. Batch hizmeti ilk olarak kaynak dosyaları düğümden Azure Depolama’ya kopyalar ve ardından komut satırını çalıştırır. Bir havuz başlangıç görevinde dosya listesi genellikle görev uygulamasını ve onun bağımlılıklarını içerir.

Ancak başlangıç görevi, işlem düğümü üzerinde çalışan tüm görevler tarafından kullanılacak başvuru verilerini de içerebilir. Örneğin, bir başlangıç görevinin komut satırı, uygulama dosyalarını (kaynak dosya olarak belirtilir ve düğüme indirilir) başlangıç görevinin [çalışma dizininden](#files-and-directories) [paylaşılan klasöre](#files-and-directories) kopyalamak üzere bir `robocopy` işlemi gerçekleştirebilir ve ardından bir MSI ya da `setup.exe` çalıştırabilir.

> [!IMPORTANT]
> Batch şu anda [Azure Depolama hesapları hakkında](../storage/storage-create-storage-account.md) bölümündeki [Depolama hesabı oluşturma](../storage/storage-create-storage-account.md#create-a-storage-account) adlı 5. adımda açıklandığı gibi *sadece* **Genel amaçlı** depolama hesabı türünü destekler. Batch görevleriniz (standart görevler, başlangıç görevleri, iş hazırlama görevleri ve iş sürüm görevleri dahil), *yalnızca* **Genel amaçlı** depolama hesaplarında yer alan kaynak dosyalarını belirtmelidir.
>
>

Bu, düğümün görevlere atanmak üzere hazır olduğunu düşünmeden önce başlangıç görevinin tamamlanmasını beklemek amacıyla Batch hizmeti için genelde istenen bir durumdur, ancak bunu yapılandırabilirsiniz.

Bir işlem düğümünde başlangıç görevi başarısız olursa, düğümün durumu hatayı yansıtacak şekilde güncelleştirilir ve düğüm hiçbir göreve atanmaz. Bir başlangıç görevi, depolamadan kaynak dosya kopyalamada bir sorun olması ya da komut satırı tarafından yürütülen işlemin sıfır olmayan bir çıkış kodu döndürmesi durumunda başarısız olabilir.

*Mevcut* bir havuz için başlangıç görevi ekler veya güncelleştirirseniz başlangıç görevinin düğümlere uygulanması için işlem düğümlerini yeniden başlatmanız gerekir.

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
* **İş bırakma görevi**: Bir iş tamamlandığında iş bırakma görevi, havuzun en az bir görev yürütmüş her düğümünde çalıştırılır. Örneğin, iş hazırlama görevi tarafından kopyalanan verileri silmek ya da tanılama günlük verilerini sıkıştırmak veya karşıya yüklemek için iş bırakma görevini kullanabilirsiniz.

Hem iş hazırlama hem de bırakma görevleri, görev çağrıldığında çalıştırılacak bir komut satırı belirtmenize imkan tanır. Bunlar dosya indirme, yükseltilmiş yürütme, özel ortam değişkenleri, en uzun yürütme süresi, yeniden deneme sayısı ve dosyayı elde tutma süresi gibi özellikler sağlar.

İş hazırlama ve bırakma görevleri hakkında daha fazla bilgi için bkz. [Azure Batch işlem düğümlerinde iş hazırlama ve tamamlama görevlerini çalıştırma](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Çok örnekli görev
[Çok örnekli görev](batch-mpi.md) aynı anda birden fazla işlem düğümü üzerinde çalışacak şekilde yapılandırılmış bir görevdir. Çok örnekli görevlerle, bir grup işlem düğümünün tek bir iş yükünü işlemek üzere bir arada ayrılmasını gerektiren yüksek performanslı bilgi işlem senaryolarına olanak sağlayabilirsiniz (İleti Geçirme Arabirimi (MPI) gibi).

Batch .NET kitaplığını kullanarak MPI işlerini Batch’de çalıştırma hakkında ayrıntılı bilgi için bkz. [Azure Batch’de İleti Geçirme Arabirimi (MPI) uygulamalarını çalıştırmak için çok örnekli görevleri kullanma](batch-mpi.md).

### <a name="task-dependencies"></a>Görev bağımlılıkları
Adından da anlaşılacağı gibi [görev bağımlılıkları](batch-task-dependencies.md), bir görevin yürütülmesinin diğer görevlerin tamamlanmasına bağlı olduğunu belirtmenizi sağlar. Bu özellik “yukarı akış” görevinin çıktısını kullanan bir “aşağı akış” görevi durumları ya da bir yukarı akış görevi, aşağı akış görevi tarafından istenen bazı başlatma işlemlerini gerçekleştirdiğinde destek sağlar. Bu özelliği kullanmak için önce Batch işinizde görev bağımlılıklarını etkinleştirmelisiniz. Ardından, bir başka göreve (ya da birçok başka göreve) bağlı her görev için, görevin bağımlı olduğu görevleri belirtirsiniz.

Görev bağımlılıkları ile aşağıdaki gibi senaryoları yapılandırabilirsiniz:

* *görevB* *görevA* ’ya bağlıdır (*görevB*, *görevA* tamamlanana kadar yürütülmeye başlamaz).
* *görevC* hem *görevA* hem de *görevB* ’ye bağlıdır.
* *görevD* yürütülmeden önce bir dizi göreve (örneğin görev *1* ile *10* arası) bağlıdır.

Bu özelliğe ilişkin daha kapsamlı bilgi için [azure-batch-samples][github_samples] GitHub deposundaki [Azure Batch'deki görev bağımlılıkları](batch-task-dependencies.md) ve [TaskDependencies][github_sample_taskdeps] kod örneğine bakın.

## <a name="environment-settings-for-tasks"></a>Görevler için ortam ayarları
Batch hizmeti tarafından yürütülen her görevin, işlem düğümleri üzerinde ayarladığı ortam değişkenlerine erişimi vardır. Buna Batch hizmeti tarafından tanımlanan ortam değişkenleri ([service-defined][msdn_env_vars]) ve görevleriniz için tanımlayabileceğiniz özel ortam değişkenleri dahildir. Görevleriniz tarafından yürütülen uygulamalar ve komut dosyaları yürütme sırasında bu ortam değişkenlerine erişebilir.

Bu varlıkların *ortam ayarları* özelliğini doldurarak görev ya da iş düzeyinde özel ortam değişkenleri ayarlayabilirsiniz. Örneğin, Batch .NET içindeki [Bir işe görev ekleme][rest_add_task] işlemine (Batch REST API'si) veya [CloudTask.EnvironmentSettings][net_cloudtask_env] ve [CloudJob.CommonEnvironmentSettings][net_job_env] özelliklerine bakın.

[Bir görev hakkında bilgi alma][rest_get_task_info] işlemini (Batch REST) kullanarak veya [CloudTask.EnvironmentSettings][net_cloudtask_env] özelliğine (Batch .NET) erişerek istemci uygulamanız ya da hizmetiniz bir görevin hem hizmet tanımlı hem de özel ortam değişkenlerini elde edebilir. Bir işlem düğümünde yürütülen işlemler bu ve düğümdeki diğer ortam değişkenlerine erişebilir, örneğin bilinen bir `%VARIABLE_NAME%` (Windows) veya `$VARIABLE_NAME` (Linux) söz dizimini kullanarak.

[İşlem düğümü ortam değişkenleri][msdn_env_vars] içinde hizmet tarafından tanımlanan tüm ortam değişkenlerinin tam listesini bulabilirsiniz.

## <a name="files-and-directories"></a>Dosyalar ve dizinler
Her görev, altında sıfır veya daha fazla dosya ve dizin oluşturduğu bir *çalışma dizinine* sahiptir. Bu çalışma dizini, görev tarafından çalıştırılan programı, işlediği verileri ve gerçekleştirdiği işlemin çıktısını depolamak için kullanılabilir. Bir görevin tüm dosyaları ve dizinleri görev kullanıcısına aittir.

Batch hizmeti bir düğümdeki dosya sisteminin bir bölümünü *kök dizin* olarak kullanıma sunar. Görevler `AZ_BATCH_NODE_ROOT_DIR` ortam değişkenine başvurarak kök dizine erişebilir. Ortam değişkenlerini kullanma hakkında daha fazla bilgi için bkz. [Görevler için ortam ayarları](#environment-settings-for-tasks).

Kök dizin aşağıdaki dizin yapısını içerir:

![İşlem düğümü dizin yapısı][1]

* **paylaşılan**: Bu dizin, düğüm üzerinde çalışan *tüm* görevler için okuma/yazma erişimi sağlar. Düğüm üzerinde çalışan herhangi bir görev bu dizinde dosya oluşturabilir, okuyabilir, güncelleştirebilir ve silebilir. Görevler `AZ_BATCH_NODE_SHARED_DIR` ortam değişkenine başvurarak bu dizine erişebilir.
* **başlangıç**: Bu dizin, bir başlangıç görevi tarafından çalışma dizini olarak kullanılır. Başlangıç görevi tarafından düğüme indirilen tüm dosyalar buraya depolanır. Başlangıç görevleri bu dizin altında dosya oluşturabilir, okuyabilir, güncelleştirebilir ve silebilir. Görevler `AZ_BATCH_NODE_STARTUP_DIR` ortam değişkenine başvurarak bu dizine erişebilir.
* **Görevler**: Düğümde çalışan her görev için bir dizin oluşturulur. `AZ_BATCH_TASK_DIR` ortam değişkenine başvurularak bu dizine erişilebilir.

    Her bir görev dizininde Batch hizmeti, bir çalışma dizini (`wd`) oluşturur. Dizinin benzersiz yolu `AZ_BATCH_TASK_WORKING_DIR` ortam değişkeni tarafından belirtilir. Bu dizin göreve okuma/yazma erişimi sağlar. Görev bu dizin altında dosya oluşturabilir, okuyabilir, güncelleştirebilir ve silebilir. Bu dizin, görev için belirtilen *RetentionTime* kısıtlamasına göre tutulur.

    `stdout.txt` ve `stderr.txt`: Bu dosyalar görevin yürütülmesi sırasında görev klasörüne yazılır.

> [!IMPORTANT]
> Bir düğüm havuzdan kaldırıldığında düğümde depolanan dosyaların *tümü* kaldırılır.
>
>

## <a name="application-packages"></a>Uygulama paketleri
[Uygulama paketleri](batch-application-packages.md) özelliği kolay uygulama yönetimi ve havuzlarınızdaki işlem düğümlerine kolay uygulama dağıtımı sağlar. Görevleriniz tarafından çalıştırılan uygulamaların birden fazla sürümünü (ikili dosyalar ve destek dosyalarıyla birlikte) kolayca karşıya yükleyebilir ve yönetebilirsiniz. Sonra bu uygulamalardan bir ya da daha fazlasını havuzunuzdaki işlem düğümlerine otomatik olarak dağıtabilirsiniz.

Uygulama paketlerini havuz ve görev düzeyinde belirtebilirsiniz. Havuz uygulama paketleri belirttiğinizde uygulama, havuzdaki her düğüme dağıtılır. Görev uygulama paketlerini belirttiğinizde uygulama, görevin komut satırı çalıştırılmadan hemen önce işteki görevlerden en az birinde çalışacak şekilde zamanlanan düğümlere dağıtılır.

Batch, uygulama paketlerinizi depolamak ve işlem düğümlerine dağıtmak amacıyla Azure Depolama ile çalışma ayrıntılarını işler, böylece hem kodunuz hem de yönetim ek yükünüz basitleştirilebilir.

Uygulama paketi özelliği hakkında daha fazla bilgi almak için bkz. [Azure Batch uygulama paketleri ile uygulama dağıtımı](batch-application-packages.md).

> [!NOTE]
> *Mevcut* bir havuza havuz uygulama paketleri eklerseniz uygulama paketlerinin düğümlere dağıtılması için işlem düğümlerini yeniden başlatmanız gerekir.
>
>

## <a name="pool-and-compute-node-lifetime"></a>Havuz ve işlem düğümü ömrü
Azure Batch çözümünüzü tasarlarken havuzların nasıl ve ne zaman oluşturulacağı ve bu havuzlardaki işlem düğümlerinin ne kadar süre kullanımda tutulacağına ilişkin bir tasarım kararı vermeniz gerekir.

Spektrumun bir ucunda, gönderdiğiniz her iş için bir havuz oluşturabilir ve görevlerin yürütülmesi biter bitmez havuzu silebilirsiniz. Düğümler yalnızca gerektiğinde ayrıldığından ve boşta kalır kalmaz kapatıldığından bunun yapılması kullanımı en iyi duruma getirir. Bu durum işin, düğümlerin ayrılmasını beklemesi gerektiği anlamına gelmekle birlikte, tek tek kullanılabilir, ayrılmış olmalarının ve başlangıç görevinin tamamlanmasının hemen ardından görevlerin yürütülmek üzere zamanlanacağını bilmek önemlidir. Batch, görevler düğümlere atamadan önce havuzdaki tüm düğümlerin kullanılabilir olmasını *beklemez*. Böylece kullanılabilir tüm düğümlerden en iyi şekilde faydalanılmasını sağlar.

Spektrumun diğer ucunda, işlerin hemen başlatılması en yüksek önceliğe sahipse işler gönderilmeden önce bir havuz oluşturabilir ve bu havuzun düğümlerini kullanıma sunabilirsiniz. Bu senaryoda görevler hemen başlayabilir, ancak görevlerin atanmasını beklerken düğümler boşta kalmaya devam edebilir.

Değişken ancak devam eden bir yükü işlemek için genellikle birleştirilmiş bir yaklaşım kullanılır. Birden fazla işin gönderildiği bir havuzunuz olabilir, ancak düğüm sayısını iş yüküne uygun olarak artırabilir veya azaltabilirsiniz (aşağıdaki bölümde yer alan [İşlem kaynaklarını ölçeklendirme](#scaling-compute-resources) kısmına bakın). Mevcut yüke bağlı olarak reaktif bir şekilde ya da yük öngörülebiliyorsa proaktif olarak bu işlemi yapabilirsiniz.

## <a name="pool-network-configuration"></a>Havuz ağ yapılandırması

Azure Batch hizmetinde işlem düğümü havuzu oluştururken, havuzun işlem düğümlerinin oluşturulması gereken Azure [sanal ağının](https://azure.microsoft.com/documentation/articles/virtual-networks-overview/) kimliğini belirtebilirsiniz.

* Yalnızca **Bulut Hizmetleri Yapılandırma** havuzlarına bir sanal ağ atanabilir.

* Sanal ağ şu şartları karşılamalıdır:

   * Azure Batch hesabıyla aynı Azure **bölgesinde** olmalıdır.
   * Azure Batch hesabıyla aynı **abonelikte** olmalıdır.
   * **Klasik** bir sanal ağ olmalıdır. Azure Resource Manager dağıtım modeliyle oluşturulan sanal ağlar desteklenmez.

* Sanal ağ, havuzun `targetDedicated` özelliği için yeterli olacak sayıda boş **IP adresi** içermelidir. Alt ağ yeterli sayıda boş IP adresi içermiyorsa Batch hizmeti, havuzdaki işlem düğümlerini kısmen ayırır ve bir yeniden boyutlandırma hatası döndürür.
* *MicrosoftAzureBatch* hizmet sorumlusu, belirtilen sanal ağ için [Klasik Sanal Makine Katılımcısı](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor) Rol Tabanlı Erişim Denetimi (RBAC) rolüne sahip olmalıdır. Azure portalında:

  * **Sanal Ağ**’ı ve ardından **Erişim denetimi (IAM)** > **Roller** > **Klasik Sanal Makine Katılımcısı** > **Ekle**’yi seçin
  * **Arama** kutusuna "MicrosoftAzureBatch" yazın
  * **MicrosoftAzureBatch** onay kutusunu işaretleyin
  * **Seç** düğmesini seçin

* İşlem düğümleriyle iletişim kurulması, sanal ağ ile ilişkili bir **Ağ Güvenlik Grubu (NSG)** tarafından reddedilirse Batch hizmeti, işlem düğümlerinin durumunu **kullanılamıyor** olarak ayarlar. İşlem düğümlerinde görev zamanlanabilmesi için alt ağın Azure Batch hizmetinden gelen iletişimlere izin vermesi gerekir.

## <a name="scaling-compute-resources"></a>İşlem kaynaklarını ölçeklendirme
[Otomatik ölçeklendirme](batch-automatic-scaling.md) kullanarak, Batch hizmetinin, bir havuzdaki işlem düğümleri sayısını mevcut iş yüküne ve işlem senaryonuzun kaynak kullanımına göre dinamik olarak ayarlamasını sağlayabilirsiniz. Bu, yalnızca ihtiyacınız olan kaynakları kullanarak ve ihtiyacınız olmayanları bırakarak uygulamanızı çalıştırmaya ilişkin genel maliyeti düşürmenizi sağlar.

Bir [otomatik ölçeklendirme formülü](batch-automatic-scaling.md#automatic-scaling-formulas) yazıp bu formülü bir havuzla ilişkilendirerek otomatik ölçeklendirmeyi etkinleştirebilirsiniz. Batch hizmeti, sonraki ölçeklendirme aralığı (sizin yapılandırabileceğiniz bir aralık) için havuzdaki düğümlerin hedef sayısını belirlemek amacıyla bu formülü kullanır. Bir havuzu oluştururken otomatik ölçeklendirme ayarlarını belirtebilir ya da bir havuz üzerinde ölçeklendirmeyi daha sonra etkinleştirebilirsiniz. Ölçeklendirme özellikli bir havuz üzerinde ölçeklendirme ayarlarını da güncelleştirebilirsiniz.

Örneğin bir iş, yürütülecek çok sayıda görev göndermenizi gerektirebilir. Havuza, kuyruğa alınmış görevlerin mevcut sayısı ve iş içindeki görevlerin tamamlanma oranı temelinde havuzdaki düğüm sayısını ayarlayan bir ölçeklendirme formülü atayabilirsiniz. Batch hizmeti düzenli aralıklarla formülü değerlendirir ve havuzu, iş yükü ile diğer formül ayarlarınız temelinde yeniden boyutlandırır. Hizmet, kuyrukta çok sayıda görev olduğunda düğüm ekler, kuyrukta veya çalışan görev olmadığında ise düğümleri kaldırır. 

Ölçeklendirme formülü aşağıdaki ölçümleri temel alabilir:

* **Zaman ölçümleri** belirtilen saat sayısınca beş dakikada bir toplanan istatistikleri temel alır.
* **Kaynak ölçümleri** CPU kullanımı, bant genişliği kullanımı, bellek kullanımı ve düğüm sayısını temel alır.
* **Görev ölçümleri**; *Etkin* (kuyruğa alınmış) *Çalışıyor* veya *Tamamlandı* gibi görev durumlarını temel alır.

Otomatik ölçeklendirme bir havuzdaki işlem düğümlerinin sayısını azalttığında, azaltma işlemi sırasında çalışan görevlerin nasıl ele alınacağını göz önünde bulundurmanız gerekir. Batch bunu yapabilmek için formüllerinize dahil edebileceğiniz bir *düğüm ayırmasını kaldırma seçeneği* sağlar. Örneğin, çalışmakta olan görevlerin hemen durdurulacağını, hemen durdurulup, ardından başka bir düğüm üzerinde yeniden kuyruğa alınacağını veya düğüm havuzdan kaldırılmadan önce bitmesine izin verileceğini belirtebilirsiniz.

Bir uygulamayı otomatik olarak ölçeklendirme hakkında daha fazla bilgi için bkz. [Azure Batch havuzunda işlem düğümlerini otomatik olarak ölçeklendirme](batch-automatic-scaling.md).

> [!TIP]
> İşlem kaynağından en iyi şekilde faydalanabilmek için işin sonundaki düğüm sayısını sıfır olarak ayarlayın ancak çalışan görevlerin bitmesine izin verin.
>
>

## <a name="security-with-certificates"></a>Sertifikalar ile güvenlik
[Azure Depolama hesabı][azure_storage] anahtarı gibi, görevler için hassas bilgileri şifrelerken ya da bunların şifrelerini çözerken genelde sertifikalar kullanmanız gerekir. Bunu desteklemek için düğümlere sertifikalar yükleyebilirsiniz. Şifrelenmiş parolalar komut satırı parametreleri aracılığıyla düğümlere geçirilir ya da görev kaynaklarından birine eklenir ve yüklü sertifikalar bunların şifrelerini çözmek için kullanılabilir.

Batch hesabına bir sertifika eklemek için [Sertifika ekle][rest_add_cert] işlemini (Batch REST) ya da [CertificateOperations.CreateCertificate][net_create_cert] yöntemini (Batch .NET) kullanırsınız. Sonra sertifikayı yeni ya da mevcut bir havuzla ilişkilendirebilirsiniz. Sertifika bir havuzla ilişkilendirildiğinde, Batch hizmeti havuzdaki her düğüme sertifikayı yükler. Batch hizmeti düğüm başlatıldığında başlangıç görevi ve iş yöneticisi görevi dahil herhangi bir görevi başlatmadan önce, uygun sertifikaları yükler.

*Mevcut* bir havuza sertifika eklerseniz sertifikaların düğümlere uygulanması için işlem düğümlerini yeniden başlatmanız gerekir.

## <a name="error-handling"></a>Hata işleme
Batch çözümündeki görev ve uygulama hatalarını işlemeyi gerekli görebilirsiniz.

### <a name="task-failure-handling"></a>Görev hatası işleme
Görev hataları kategorileri şunlardır:

* **Zamanlama hataları**

    Bir görev için belirtilen dosya aktarımı herhangi bir nedenle başarısız olursa, görev için *zamanlama hatası* ayarlanır.

    Görevin kaynak dosyalarının taşınmış olması, Storage hesabının artık kullanılmaması veya dosyaların düğüme başarıyla kopyalanmasını önleyen bir sorunla karşılaşılması durumunda zamanlama hataları oluşabilir.
* **Uygulama hataları**

    Görevin komut satırı tarafından belirtilen işlem de başarısız olabilir. Görev tarafından yürütülen işlem sıfır olmayan çıkış kodu döndürdüğünde işlemin başarısız olduğu kabul edilir (sonraki bölümde yer alan *Görev çıkış kodları* kısmına bakın).

    Uygulama hataları için Batch’i, görevi belirlenen sayıya kadar otomatik olarak yeniden deneyecek şekilde yapılandırabilirsiniz.
* **Kısıtlama hataları**

    Bir iş ya da görev için maksimum yürütme süresini belirleyen bir kısıtlama (*maxWallClockTime*) ayarlayabilirsiniz. Bu ilerleme göstermeyen görevlerin sonlandırılması için faydalı olabilir.

    Maksimum süre aşıldığında görev *tamamlandı* olarak işaretlenir ancak çıkış kodu `0xC000013A` olarak ayarlanır ve *schedulingError* alanı `{ category:"ServerError", code="TaskEnded"}` olarak işaretlenir.

### <a name="debugging-application-failures"></a>Uygulama hatalarını ayıklama
* `stderr` ve `stdout`

    Yürütme sırasında bir uygulama, sorunları gidermek için kullanabileceğiniz tanılama çıktıları üretebilir. Önceki [Dosyalar ve dizinler](#files-and-directories) bölümünde belirtildiği gibi, Batch hizmeti işlem düğümündeki görev dizininde yer alan `stdout.txt` ve `stderr.txt` dosyalarına standart çıktı ve standart hata çıktısı yazar. Bu dosyaları indirmek için Azure portalını veya toplu SDK'lardan birini kullanabilirsiniz. Örneğin, Batch .NET kitaplığındaki [ComputeNode.GetNodeFile][net_getfile_node] ve [CloudTask.GetNodeFile][net_getfile_task] öğelerini kullanarak bu dosyaları ve diğer dosyaları sorun giderme amacıyla alabilirsiniz.
* **Görev çıkış kodları**

    Daha önce belirtildiği gibi, görev tarafından yürütülen işlem sıfır olmayan bir çıkış kodu döndürürse görev Batch hizmeti tarafından başarısız olarak işaretlenir. Bir görev bir işlemi yürüttüğünde Batch, görevin çıkış kodu özelliğini *işlemin dönüş kodu* ile doldurur. Bir görevin çıkış kodunun Batch hizmeti tarafından **belirlenmediğine** dikkat etmeniz önemlidir. Bir görevin çıkış kodu işlemin kendisi ya da işlemin yürütüldüğü işletim sistemi tarafından belirlenir.

### <a name="accounting-for-task-failures-or-interruptions"></a>Görev hataları veya kesintilerinin sebepleri
Görevler zaman zaman başarısız olabilir veya kesintiye uğrayabilir. Görevin kendisi başarısız olabilir, görevin üzerinde çalıştığı düğüm yeniden başlatılabilir ya da havuzun ayırmayı kaldırma ilkesi görevin bitmesini beklemeden düğümleri kaldırmak üzere ayarlandıysa, yeniden boyutlandırma işlemi sırasında düğüm havuzdan kaldırılabilir. Her durumda, görev başka bir düğümde yürütülmek üzere Batch tarafından otomatik olarak yeniden kuyruğa alınabilir.

Aralıklı bir sorunun görevin askıda kalmasına ya da yürütülmesinin uzun sürmesine neden olması mümkündür. Bir görev için en fazla yürütme aralığını ayarlayabilirsiniz. Maksimum yürütme aralığı aşılırsa Batch hizmeti görev uygulamasını kesintiye uğratır.

### <a name="connecting-to-compute-nodes"></a>İşlem düğümlerine bağlanma
Bir işlem düğümünde uzaktan oturum açarak ek hata ayıklama ve sorun giderme işlemlerini gerçekleştirebilirsiniz. Windows düğümleri için bir Uzak Masaüstü Protokolü (RDP) dosyası indirmek ve Linux düğümleri için Güvenli Kabuk (SSH) bağlantı bilgilerini elde etmek üzere Azure portalını kullanabilirsiniz. Bu işlemi Batch API’lerini kullanarak da yapabilirsiniz; örneğin, [Batch .NET][net_rdpfile] veya [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes) ile.

> [!IMPORTANT]
> RDP veya SSH aracılığıyla bir düğüme bağlanmak için düğümde bir kullanıcı oluşturmanız gerekir. Bunu yapmak için Azure portalını kullanabilir, Batch REST API’sini kullanarak [bir düğüme kullanıcı hesabı ekleyebilir][rest_create_user], Batch .NET içinde [ComputeNode.CreateComputeNodeUser][net_create_user] yöntemini çağırabilir veya Batch Python modülünde [add_user][py_add_user] yöntemini çağırabilirsiniz.
>
>

### <a name="troubleshooting-problematic-compute-nodes"></a>Sorunlu işlem düğümleriyle ilgili sorunları giderme
Bazı görevlerinizin başarısız olduğu durumlarda, Batch istemci uygulamanız ya da hizmetiniz, hatalı davranan düğümü tanımlamak üzere başarısız görevlerin meta verilerini inceleyebilir. Bir havuzdaki her düğüme benzersiz bir kimlik verilir ve bir görevin çalıştığı düğüm görev meta verilerine eklenir. Bir sorun düğümünü tanımladıktan sonra bununla birkaç eylem gerçekleştirebilirsiniz:

* **Düğümü yeniden başlatma** ([REST][rest_reboot] | [.NET][net_reboot])

    Düğümü yeniden başlatmak bazen takılan veya çöken işlemler gibi görünmeyen sorunları temizleyebilir. Havuzunuz bir başlangıç görevi kullanıyorsa ya da işiniz iş hazırlama görevi kullanıyorsa, düğüm başlatıldığında bunlar yürütülür.
* **Düğümün görüntüsünü yeniden oluşturma** ([REST][rest_reimage] | [.NET][net_reimage])

    Bu işlem, işletim sistemini düğüme yeniden yükler. Düğümün yeniden başlatılmasıyla düğümün görüntüsünün yeniden oluşturulmasının ardından başlangıç görevleri ve iş hazırlama görevleri yeniden çalıştırılır.
* **Düğümü havuzdan kaldırma** ([REST][rest_remove] | [.NET][net_remove])

    Bazen düğümün havuzdan tamamen kaldırılması gereklidir.
* **Düğümde görev zamanlamasını devre dışı bırakma** ([REST][rest_offline] | [.NET][net_offline])

    Bu, başka görev atanmayacak şekilde düğümü çevrimdışı durumuna alır, ancak düğümün çalışır durumda ve havuzda kalmasına izin verir. Bu, başarısız olan görevin verilerini kaybetmeden ve düğüm, başka görev hatalarına neden olmadan hatalara ilişkin ayrıntılı araştırma gerçekleştirmenizi sağlar. Örneğin düğümde görev zamanlamayı devre dışı bırakabilir, sonra düğümün olay günlüklerini incelemek üzere [uzaktan oturum açabilir](#connecting-to-compute-nodes) ya da başka sorun giderme işlemleri uygulayabilirsiniz. Araştırmanızı bitirdikten sonra görev zamanlamayı tekrar çevrimiçi olarak etkinleştirebilir ([REST][rest_online] | [.NET][net_online]) ya da daha önce belirtilen diğer eylemlerden birini gerçekleştirebilirsiniz.

> [!IMPORTANT]
> Bu bölümde açıklanan her bir eylemde (yeniden başlatma, yeniden görüntü oluşturma, görev zamanlamayı kaldırma ve devre dışı bırakma), eylemi gerçekleştirdiğinizde düğümde çalışmakta olan görevlerin nasıl işleneceğini belirtebilirsiniz. Örneğin, Batch .NET istemci kitaplığını kullanarak görev zamanlamayı devre dışı bıraktığınızda, çalışan görevleri **Sonlandırma**, diğer düğümlerde zamanlama için **Yeniden kuyruğa alma** ya da eylemi gerçekleştirmeden önce çalışan görevlerin tamamlanmasına izin vermeyi (**TaskCompletion**) belirtmek üzere [DisableComputeNodeSchedulingOption][net_offline_option] listeleme değeri belirleyebilirsiniz.
>
>

## <a name="next-steps"></a>Sonraki adımlar
* Batch çözümleri oluşturmak için kullanılabilen [Batch API’leri ve araçları](batch-apis-tools.md) hakkında bilgi alın.
* [.NET için Azure Batch Kitaplığını kullanmaya başlama](batch-dotnet-get-started.md) bölümünde örnek bir Batch uygulaması hakkında adım adım yönergeler alın. Öğreticinin ayrıca Linux işlem düğümleri üzerinde iş yükü çalıştıran bir [Python sürümü](batch-python-tutorial.md) vardır.
* Batch çözümlerinizi geliştirirken kullanmak üzere [Batch Gezgini][github_batchexplorer] örnek projesini indirin ve derleyin. Batch Explorer’ı kullanarak, aşağıdakileri ve daha fazlasını gerçekleştirebilirsiniz:

  * Batch hesabınızdaki havuzlar, işler ve görevleri izleme ve düzenleme
  * Düğümlerden `stdout.txt`, `stderr.txt` ve diğer dosyaları indirme
  * Düğümlerde kullanıcı oluşturma ve uzaktan oturum açma için RDP dosyalarını indirme
* [Linux işlem düğümü havuzlarını oluşturma](batch-linux-nodes.md) hakkında bilgi edinin.
* MSDN'de [Azure Batch forumunu][batch_forum] ziyaret edin. Forum, Batch hizmetinde yeni veya uzman olmanıza bakılmaksızın sorular sormak için uygun bir yerdir.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
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

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

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

