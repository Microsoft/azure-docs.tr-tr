---
title: Azure Batch iş ve görevler
description: İşler ve görevler hakkında bilgi edinin ve bunların bir geliştirme açısından Azure Batch iş akışında nasıl kullanıldığını öğrenin.
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1ca721ec7527d9d042c129c22cf0266e57c32e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95808584"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Azure Batch iş ve görevler

Azure Batch, bir *görev* bir hesaplama birimini temsil eder. *İş* , bu görevlerin bir koleksiyonudur. İşler ve görevler hakkında daha fazla bilgi ve bunların Azure Batch iş akışında nasıl kullanıldığı hakkında daha fazla bilgi aşağıda açıklanmıştır.

## <a name="jobs"></a>İşler

İş bir görev koleksiyonudur. Bir havuzdaki işlem düğümleri üzerindeki görevleri tarafından hesaplamanın nasıl gerçekleştirildiğini yönetir.

Bir iş, çalışmanın çalıştırılacağı [havuzu](nodes-and-pools.md#pools) belirler. Her iş için yeni havuz oluşturabilir veya çok sayıda iş için bir havuz kullanabilirsiniz. Bir iş [zamanlaması](#scheduled-jobs)ile ilişkili her iş için bir havuz veya bir iş zamanlamasıyla ilişkili tüm işler için bir havuz oluşturabilirsiniz.

### <a name="job-priority"></a>İş önceliği

Oluşturduğunuz işlere isteğe bağlı bir iş önceliği atayabilirsiniz. Batch hizmeti, her havuzda zamanlama sırasını (iş içerisindeki tüm görevler için) anlamak üzere işin öncelik değerini kullanır.

Bir işin önceliğini güncelleştirmek için, [bir iş işleminin özelliklerini güncelleştirme](/rest/api/batchservice/job/update) (Batch REST) ya da [Cloudjob. Priority](/dotnet/api/microsoft.azure.batch.cloudjob) (Batch .net) öğesini çağırın. Öncelik değerleri-1000 (en düşük öncelik) ile 1000 (en yüksek öncelik) arasındadır.

Aynı havuz içinde, yüksek öncelikli işlerin düşük öncelikli işlere göre zamanlama önceliği vardır. Zaten çalışmakta olan düşük öncelikli işlerde bulunan görevler, daha yüksek öncelikli bir işteki görevler tarafından yok edilir. Aynı öncelik düzeyine sahip işlerin zamanlanabileceği eşittir ve görev yürütmenin sıralaması tanımlanmamıştır.

Tek bir havuzda çalışan yüksek öncelikli değere sahip bir iş, ayrı bir havuzda veya farklı bir Batch hesabında çalışan işlerin zamanlamasını etkilemez. İş önceliği, iş gönderildiğinde oluşturulan, [oto havuzlar](nodes-and-pools.md#autopools)için uygulanmaz.

### <a name="job-constraints"></a>İş kısıtlamaları

İşleriniz için bazı sınırlar belirtmek üzere iş kısıtlamaları kullanabilirsiniz:

- Bir **duvar saati zamanı üst sınırı** ayarlayabilirsiniz; böylece bir iş belirtilen duvar saati zamanı üst sınırından daha uzun süre çalışırsa iş ve tüm görevleri sonlandırılır.
- Bir görevin her zaman yeniden denenip yeniden denenmeyeceği da dahil olmak üzere **en fazla görev yeniden deneme sayısını** kısıtlama olarak belirtebilirsiniz. Bir görevi yeniden denemek, görevin başarısız olması durumunda yeniden kuyruğa olarak yeniden çalıştırılması anlamına gelir.

### <a name="job-manager-tasks-and-automatic-termination"></a>İş Yöneticisi görevleri ve otomatik sonlandırma

İstemci uygulamanız bir işe görevler ekleyebilir ya da bir [iş yöneticisi görevi](#job-manager-task) belirtebilirsiniz. Bir iş yöneticisi görevi havuzdaki işlem düğümlerinden birinde çalıştırılan görevle birlikte bir iş için gereken görevleri oluşturmak üzere gerekli bilgileri içerir. İş Yöneticisi görevi özellikle Batch tarafından işlenir; iş oluşturulduktan hemen sonra kuyruğa alınır ve başarısız olursa yeniden başlatılır. İş tarafından oluşturulan işler için iş Yöneticisi görevi [gerekir, çünkü](#scheduled-jobs)iş örneği oluşturulmadan önce görevleri tanımlamanın tek yoludur.

Varsayılan olarak, işteki tüm görevler tamamlandığında iş etkin durumda kalır. Bu davranışı, işteki tüm görevler tamamlandığında işin otomatik olarak sonlandırılacağı şekilde değiştirebilirsiniz. Tüm görevleri tamamlandı durumundaysa işi otomatik olarak sonlandırmak için işin **onalltaskscompleted** özelliğini (Batch .net 'Te [onalltaskscompleted](/dotnet/api/microsoft.azure.batch.cloudjob) ) `terminatejob` * ' olarak ayarlayın.

Batch hizmeti, tüm görevlerinin tamamlanması için görev *içermeyen* bir işi kabul eder. Bu nedenle, bu seçenek genellikle [iş yöneticisi görevi](#job-manager-task) ile kullanılır. İş Yöneticisi olmadan otomatik iş sonlandırmasını kullanmak istiyorsanız, başlangıçta yeni bir işin **Onalltasksfinished** özelliğini olarak ayarlamanız `noaction` , sonra da `terminatejob` işe görev eklemeyi bitirdikten sonra bunu * ' olarak ayarlamanız gerekir.

### <a name="scheduled-jobs"></a>Zamanlanan işler

[İş zamanlamaları](/rest/api/batchservice/jobschedule) , Batch hizmeti içinde yinelenen işler oluşturmanızı sağlar. Bir iş zamanlaması işlerin ne zaman çalıştırılacağını belirtir ve çalıştırılacak işlerin özelliklerini içerir. Zamanlamanın süresini (zamanlamanın ne kadar süreyle ve ne zaman etkin olduğunu) ve zamanlanan dönemde işlerin ne sıklıkta oluşturulacağını belirtebilirsiniz.

## <a name="tasks"></a>Görevler

Görev bir işle ilişkili hesaplama birimidir. Bir düğüm üzerinde çalışır. Görevler yürütülmek için bir düğüme atanır veya bir düğüm serbest kalana kadar kuyruğa alınır. Kısacası görev, bitmesi gereken çalışmayı gerçekleştirmek üzere bir veya daha fazla program ya da komut dosyasını bir işlem düğümü üzerinde çalıştırır.

Bir görev oluşturduğunuzda aşağıdakileri belirtebilirsiniz:

- Görevin **komut satırı**. Uygulamanızı veya komut dosyanızı işlem düğümü üzerinde çalıştıran komut satırıdır.

    Komut satırının bir kabukta çalıştırılmadığını unutmamak önemlidir. Bu nedenle, [ortam değişkeni](#environment-settings-for-tasks) genişletmesi gibi kabuk özelliklerinden (buna `PATH` dahildir) yerel olarak yararlanamaz. Bu özelliklerden faydalanmak için, `cmd.exe` Windows düğümleri veya Linux 'ta başlatarak gibi, komut satırında kabuğu çağırmanız gerekir `/bin/sh` :

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Görevlerinizin, düğümün `PATH` veya başvuru ortamı değişkenlerinde olmayan bir uygulama ya da komut dosyasını çalıştırması gerekiyorsa kabuğu görev komut satırında açıkça çağırın.
- İşlenecek verileri içeren **kaynak dosyalar**. Bu dosyalar görevin komut satırı yürütülmeden önce bir Azure Depolama hesabındaki Blob depolamadan düğüme otomatik olarak kopyalanır. Daha fazla bilgi için bkz. [Başlangıç görevi](#start-task) ve [Dosyalar ve dizinler](files-and-directories.md).
- Uygulamanızın gerektirdiği **ortam değişkenleri**. Daha fazla bilgi için bkz. [Görevler Için ortam ayarları](#environment-settings-for-tasks).
- Görev yürütülürken tabi olunan **kısıtlamalar**. Örneğin, görevin çalışmasına izin verilen en uzun süre, başarısız olan bir görevin en fazla yeniden deneme sayısı ve görevin çalışma dizinindeki dosyaların elde tutulduğu en uzun süre kısıtlamalardan bazılarıdır.
- Görevin çalışmak üzere zamanlandığı işlem düğümünü dağıtmak için kullanılan **uygulama paketleri**. [Uygulama paketleri](batch-application-packages.md) , görevlerinizin çalıştırdığı uygulamaların Basitleştirilmiş dağıtım ve sürüm oluşturmayı sağlar. Görev düzeyinde uygulama paketleri, özellikle paylaşılan havuz ortamlarında çok yararlıdır. Bu ortamlarda, tek havuzda farklı işler çalıştırılır ve bir iş tamamlandığında havuz silinmez. İşinizin havuzdaki görevleri, düğümlerinden azsa uygulamanız yalnızca görevleri çalıştıran düğümlere dağıtıldığı için görev uygulama paketleri veri aktarımını azaltabilir.
- Düğümdeki görevin çalıştığı Docker kapsayıcısını oluşturmak için Docker Hub içinde bir **kapsayıcı görüntüsü** başvurusu veya özel kayıt defteri ile ek ayarlar. Bu bilgileri yalnızca havuz kapsayıcı yapılandırmasıyla kurulduysa belirtmeniz gerekir.

> [!NOTE]
> Görevin ne zaman tamamlandığını, ne zaman tamamlanana kadar en fazla yaşam süresi 180 gündür. Tamamlanan görevler 7 gün boyunca devam ederse; Maksimum yaşam süresi içinde tamamlanmayan görevlere yönelik verilere erişilemiyor.

Bir düğümde hesaplama gerçekleştirmek için tanımladığınız görevlere ek olarak, Batch hizmeti tarafından da birkaç özel görev sağlanır:

- [Başlangıç görevi](#start-task)
- [İş yöneticisi görevi](#job-manager-task)
- [İş hazırlama ve bırakma görevleri](#job-preparation-and-release-tasks)
- [Çok örnekli görevler](#multi-instance-task)
- [Görev bağımlılıkları](#task-dependencies)

### <a name="start-task"></a>Başlangıç görevi

Başlangıç görevini bir havuz ile ilişkilendirerek düğümlerinin işletim sistemi ortamını hazırlayabilirsiniz. Örneğin, görevlerinizin çalıştıracağı uygulamaları yükleme veya arka plan işlemlerini başlatma gibi eylemleri gerçekleştirebilirsiniz. Başlangıç görevi, havuzda kaldığı sürece bir düğüm her başlatıldığında çalışır. Bu, düğümün havuza ilk eklendiği ve yeniden başlatılma veya yeniden oluşturulduğu zaman dahil olmak üzere.

Başlangıç görevinin birincil avantajı, bir işlem düğümünü yapılandırmak ve görev yürütmede gereken uygulamaları yüklemek için gerekli tüm bilgileri içerebilmesidir. Bu nedenle bir havuzdaki düğüm sayısını artırmak, yeni bir hedef düğüm sayısı belirtmek kadar kolaydır. Başlangıç görevi, Batch hizmeti için yeni düğümleri yapılandırmak ve görevleri kabul etmek için hazırlık sağlamak üzere gerekli bilgileri sağlar.

Her Azure Batch görevinde olduğu gibi, [Azure Depolama](../storage/index.yml)'daki kaynak dosyaların bir listesini, yürütülecek komut satırına ek olarak belirtebilirsiniz. Batch hizmeti ilk olarak kaynak dosyaları düğümden Azure Depolama’ya kopyalar ve ardından komut satırını çalıştırır. Bir havuz başlangıç görevinde dosya listesi genellikle görev uygulamasını ve onun bağımlılıklarını içerir.

Ancak başlangıç görevi, işlem düğümü üzerinde çalışan tüm görevler tarafından kullanılacak başvuru verilerini de içerebilir. Örneğin, bir başlangıç görevinin komut satırı, `robocopy` Başlangıç görevinin [çalışma dizininden](files-and-directories.md) **paylaşılan** klasöre ve ardından bir MSI veya çalıştıran uygulama dosyalarını (kaynak dosyaları olarak belirtilmiş ve düğüme indirilir) kopyalamak için bir işlem gerçekleştirebilir `setup.exe` .

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

### <a name="job-manager-task"></a>İş yöneticisi görevi

İş yürütmeyi denetlemek ve/veya izlemek için genellikle bir iş Yöneticisi görevi kullanırsınız. Örneğin, İş Yöneticisi görevleri genellikle bir işin görevlerini oluşturmak ve göndermek, çalıştırılacak ek görevleri tespit etmek ve çalışmanın ne zaman tamamlandığını belirlemede kullanılır.

Ancak, bir iş yöneticisi görevi bu etkinliklerle sınırlı değildir. Bu, iş için gerekli olan herhangi bir eylemi gerçekleştirebilecek bir tam kapsamlı görevdir. Örneğin, bir iş yöneticisi görevi parametre olarak belirtilen bir dosyayı indirebilir, dosyanın içeriğini çözümleyebilir ve bu içeriğe göre ek görevler gönderebilir.

Bir iş yöneticisi görevi diğer tüm görevlerden önce başlatılır. Aşağıdaki özellikleri sağlar:

- İş oluşturulduğunda, Batch hizmeti tarafından bir görev olarak otomatik olarak gönderilir.
- Bir işte diğer görevlerden önce yürütülecek şekilde zamanlanır.
- Havuzun boyutu küçültülürken bu görevin ilişkili düğümü havuzdan en son kaldırılacak düğümdür.
- Görevin sonlandırılması, işteki tüm görevlerin sonlandırılmasına bağlıdır.
- Yeniden başlatılması gerektiğinde iş yöneticisi görevine en yüksek öncelik verilir. Boş bir düğüm yoksa Batch hizmeti, iş yöneticisi görevinin çalışması için yer açmak amacıyla havuzundaki çalışan diğer görevlerden birini sonlandırabilir.
- Bir işteki iş yöneticisi görevinin, diğer işlerin görevleri üzerinde önceliği yoktur. İşlerde, yalnızca iş düzeyinde öncelikler gözetilir.

### <a name="job-preparation-and-release-tasks"></a>İş hazırlama ve bırakma görevleri

Batch, iş öncesi yürütme kurulumu için iş hazırlama görevleri ve iş sonrası bakım veya temizleme için iş bırakma görevleri sağlar.

İş hazırlama görevi, diğer iş görevlerinden herhangi biri yürütülmeden önce, görevleri çalıştırmak için zamanlanan tüm işlem düğümlerinde çalışır. Örneğin, tüm görevler tarafından paylaşılan ancak işe özel olan verileri kopyalamak için bir iş hazırlama görevi kullanabilirsiniz.

Bir iş tamamlandığında, havuzda en az bir görevi yürüten her düğüm üzerinde bir iş bırakma görevi çalışır. Örneğin, bir iş serbest bırakma görevi, iş hazırlama görevi tarafından kopyalanmış verileri silebilir veya tanılama günlük verilerini sıkıştırabilir ve karşıya yükleyebilir.

Hem iş hazırlama hem de bırakma görevleri, görev çağrıldığında çalıştırılacak bir komut satırı belirtmenize imkan tanır. Bunlar dosya indirme, yükseltilmiş yürütme, özel ortam değişkenleri, en uzun yürütme süresi, yeniden deneme sayısı ve dosyayı elde tutma süresi gibi özellikler sağlar.

İş hazırlama ve bırakma görevleri hakkında daha fazla bilgi için bkz. [Azure Batch işlem düğümlerinde iş hazırlama ve tamamlama görevlerini çalıştırma](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Çok örnekli görev

[Çok örnekli görev](batch-mpi.md) aynı anda birden fazla işlem düğümü üzerinde çalışacak şekilde yapılandırılmış bir görevdir. Çok örnekli görevlerle, Ileti geçirme arabirimi (MPı) gibi tek bir iş yükünü işlemek için birlikte ayrılan işlem düğümleri grubunu gerektiren yüksek performanslı bilgi işlem senaryolarına olanak sağlayabilirsiniz.

Batch .NET kitaplığını kullanarak MPI işlerini Batch’de çalıştırma hakkında ayrıntılı bilgi için bkz. [Azure Batch’de İleti Geçirme Arabirimi (MPI) uygulamalarını çalıştırmak için çok örnekli görevleri kullanma](batch-mpi.md).

### <a name="task-dependencies"></a>Görev bağımlılıkları

Adından da anlaşılacağı gibi [görev bağımlılıkları](batch-task-dependencies.md), bir görevin yürütülmesinin diğer görevlerin tamamlanmasına bağlı olduğunu belirtmenizi sağlar. Bu özellik “yukarı akış” görevinin çıktısını kullanan bir “aşağı akış” görevi durumları ya da bir yukarı akış görevi, aşağı akış görevi tarafından istenen bazı başlatma işlemlerini gerçekleştirdiğinde destek sağlar.

Bu özelliği kullanmak için, öncelikle toplu işte [görev bağımlılıklarını etkinleştirmeniz](batch-task-dependencies.md#enable-task-dependencies
) gerekir. Ardından, bir başka göreve (ya da birçok başka göreve) bağlı her görev için, görevin bağımlı olduğu görevleri belirtirsiniz.

Görev bağımlılıkları ile aşağıdaki gibi senaryoları yapılandırabilirsiniz:

- *görevB**görevA*’ya bağlıdır (*görevB*, *görevA* tamamlanana kadar yürütülmeye başlamaz).
- *görevC* hem *görevA* hem de *görevB* ’ye bağlıdır.
- *Taskd* , çalıştırılmadan önce *1* ile *10* arasındaki görevler gibi bir dizi göreve bağlıdır.

Daha fazla ayrıntı için bkz. [Azure Batch görev bağımlılıkları](batch-task-dependencies.md) ve [Azure-Batch-Samples](https://github.com/Azure-Samples/azure-batch-samples) GitHub deposundaki [taskdependencies](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) kodu örneği.

### <a name="environment-settings-for-tasks"></a>Görevler için ortam ayarları

Batch hizmeti tarafından yürütülen her görevin, işlem düğümleri üzerinde ayarladığı ortam değişkenlerine erişimi vardır. Bu, Batch hizmeti tarafından tanımlanan ortam değişkenlerini ([hizmet tanımlı](./batch-compute-node-environment-variables.md) ve görevleriniz için tanımlayabileceğiniz özel ortam değişkenleri) içerir. Görevleriniz tarafından yürütülen uygulamalar ve komut dosyaları yürütme sırasında bu ortam değişkenlerine erişebilir.

Bu varlıkların *ortam ayarları* özelliğini doldurarak görev ya da iş düzeyinde özel ortam değişkenleri ayarlayabilirsiniz. Daha fazla ayrıntı için Batch .NET içindeki [cloudtask. EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) ve [Cloudjob. commonenvironmentsettings](/dotnet/api/microsoft.azure.batch.cloudjob) özelliklerine bakın. [bir işe görev ekleme](/rest/api/batchservice/task/add?)] işlemi (Batch REST API)

[Bir görev hakkında bilgi alma](/rest/api/batchservice/task/get) işlemini (Batch REST) kullanarak veya [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) özelliğine (Batch .NET) erişerek istemci uygulamanız ya da hizmetiniz bir görevin hem hizmet tanımlı hem de özel ortam değişkenlerini elde edebilir. Bir işlem düğümünde yürütülen işlemler bu ve düğümdeki diğer ortam değişkenlerine erişebilir, örneğin bilinen bir `%VARIABLE_NAME%` (Windows) veya `$VARIABLE_NAME` (Linux) söz dizimini kullanarak.

[İşlem düğümü ortam değişkenleri](batch-compute-node-environment-variables.md) içinde hizmet tarafından tanımlanan tüm ortam değişkenlerinin tam listesini bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Dosyalar ve dizinler](files-and-directories.md)hakkında bilgi edinin.
