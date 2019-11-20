---
title: Visual Studio şablonlarıyla çözüm oluşturma-Azure Batch | Microsoft Docs
description: Visual Studio proje şablonlarının Azure Batch üzerinde yoğun işlem yoğunluğu olan iş yüklerinizi uygulamanıza ve çalıştırmanıza nasıl yardımcı olabileceğini öğrenin.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 60662e723a55c969fdd4b70e732303c90bbf9e8b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094352"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Toplu Iş çözümlerini başlatmak için Visual Studio proje şablonlarını kullanın

Batch için **Iş Yöneticisi** ve **görev işlemcisi Visual Studio şablonları** , işlem için yoğun iş yüklerinizi en az çaba miktarı ile uygulamanıza ve çalıştırmanıza yardımcı olacak kod sağlar. Bu belgede bu şablonlar açıklanmakta ve bunların nasıl kullanılacağına ilişkin yönergeler sağlanmaktadır.

> [!IMPORTANT]
> Bu makalede yalnızca bu iki şablon için geçerli bilgiler ele alınmaktadır ve Batch hizmeti ile bununla ilgili önemli kavramlar hakkında bilgi sahibi olduğunuz varsayılır: havuzlar, işlem düğümleri, işler ve görevler, İş Yöneticisi görevleri, ortam değişkenleri ve diğer ilgili bilgi. [Azure Batch temel](batch-technical-overview.md) bilgileri ve [geliştiriciler için Batch özelliğine genel bakış](batch-api-basics.md)hakkında daha fazla bilgi edinebilirsiniz.
> 
> 

## <a name="high-level-overview"></a>Yüksek düzey genel bakış
Iş Yöneticisi ve görev Işlemcisi şablonları, iki yararlı bileşen oluşturmak için kullanılabilir:

* Bir iş bölümlendiricisini uygulayan ve bağımsız olarak, paralel olarak çalışabilen birden fazla görevde bir işi bozabilecek bir iş Yöneticisi görevi.
* Uygulama komut satırı etrafında ön işleme ve işlem sonrası işlemleri gerçekleştirmek için kullanılabilen bir görev işlemcisi.

Örneğin, bir film işleme senaryosunda, iş bölümlendiricisi tek bir film işini ayrı ayrı çerçeveleri ayrı olarak işleyen yüzlerce veya binlerce ayrı göreve dönüştürür. Görev işlemcisi, işleme uygulamasını ve her çerçeveyi işlemek için gereken tüm bağımlı işlemleri ve ek eylemler gerçekleştirmek (örneğin, işlenen çerçeveyi bir depolama konumuna kopyalamak) için çağırır.

> [!NOTE]
> Iş Yöneticisi ve görev Işlemcisi şablonları birbirinden bağımsızdır, bu sayede işlem işinizin gereksinimlerine ve tercihlerinize göre her ikisini de veya yalnızca birini kullanmayı seçebilirsiniz.
> 
> 

Aşağıdaki diyagramda gösterildiği gibi, bu şablonları kullanan bir işlem işi üç aşamaya geçer:

1. İstemci kodu (örn. uygulama, Web hizmeti vb.), Azure 'da Batch hizmetine iş gönderir ve iş Yöneticisi görevi iş Yöneticisi programını belirterek.
2. Batch hizmeti, iş yöneticisi görevini bir işlem düğümünde çalıştırır ve iş bölümlendiricisi, iş ayırıcı kodundaki parametrelere ve belirtimlere göre gereken sayıda işlem düğümü üzerinde belirtilen sayıda görev işlemcisi görevini başlatır.
3. Görev işlemcisi görevleri, giriş verilerini işlemek ve çıktı verilerini oluşturmak için bağımsız olarak paralel olarak çalışır.

![İstemci kodunun Batch hizmetiyle nasıl etkileşime gireceğini gösteren diyagram][diagram01]

## <a name="prerequisites"></a>Önkoşullar
Batch şablonlarını kullanmak için şunlar gerekir:

* Visual Studio 2015 yüklü bir bilgisayar. Batch şablonları Şu anda yalnızca Visual Studio 2015 için desteklenmektedir.
* Visual Studio [galerisinden][vs_gallery] Visual Studio uzantıları olarak kullanılabilen Batch şablonları. Şablonları almanın iki yolu vardır:
  
  * Şablonları Visual Studio 'daki **Uzantılar ve güncelleştirmeler** iletişim kutusunu kullanarak yüklemeyi (daha fazla bilgi için bkz. [Visual Studio uzantılarını bulma ve kullanma][vs_find_use_ext]). **Uzantılar ve güncelleştirmeler** iletişim kutusunda aşağıdaki iki uzantıyı arayın ve indirin:
    
    * İş ayırıcı ile iş Yöneticisi Azure Batch
    * Azure Batch görev Işlemcisi
  * Visual Studio Online galerisinden şablonları indirin: [Microsoft Azure Batch proje şablonları][vs_gallery_templates]
* İş Yöneticisi ve görev işlemcisini Batch işlem düğümlerine dağıtmak için [uygulama paketleri](batch-application-packages.md) özelliğini kullanmayı planlıyorsanız, Batch hesabınıza bir depolama hesabı bağlamanız gerekir.

## <a name="preparation"></a>Hazırlık
İş yöneticiniz ve görev işlemcisi programları arasında kod paylaşmayı kolaylaştırabileceğinden, iş yöneticinizin yanı sıra görev işlemcinizi içerebilen bir çözüm oluşturmanızı öneririz. Bu çözümü oluşturmak için aşağıdaki adımları izleyin:

1. Visual Studio 'yu açın ve **Dosya** > **Yeni** > **Proje**' yi seçin.
2. **Şablonlar**altında **diğer proje türleri**' ni genişletin, **Visual Studio çözümleri**' ne tıklayın ve ardından **boş çözüm**' ü seçin.
3. Uygulamanızı açıklayan bir ad yazın (örneğin, "LitwareBatchTaskPrograms").
4. Yeni çözümü oluşturmak için **Tamam**' a tıklayın.

## <a name="job-manager-template"></a>İş Yöneticisi şablonu
Iş Yöneticisi şablonu, aşağıdaki eylemleri gerçekleştirebilecek bir iş Yöneticisi görevi uygulamanıza yardımcı olur:

* İşi birden çok göreve ayırın.
* Bu görevleri toplu Iş üzerinde çalışacak şekilde gönderebilirsiniz.

> [!NOTE]
> İş Yöneticisi görevleri hakkında daha fazla bilgi için bkz. [geliştiriciler Için Batch özelliğine genel bakış](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Şablonu kullanarak bir Iş Yöneticisi oluşturma
Daha önce oluşturduğunuz çözüme bir iş yöneticisi eklemek için aşağıdaki adımları izleyin:

1. Visual Studio 'da mevcut çözümünüzü açın.
2. Çözüm Gezgini, çözüme sağ tıklayın,**Yeni proje** **Ekle** > ' ye tıklayın.
3. **C#Görsel**altında **bulut**' a ve ardından iş **ayırıcı ile Azure Batch iş Yöneticisi**' ne tıklayın.
4. Uygulamanızı açıklayan bir ad yazın ve bu projeyi iş yöneticisi olarak tanımlar (ör. "LitwareJobManager").
5. Projeyi oluşturmak için **Tamam**' a tıklayın.
6. Son olarak, Visual Studio 'Yu başvurulan tüm NuGet paketlerini yüklemeye zorlamak ve projeyi değiştirmeye başlamadan önce geçerli olduğunu doğrulamak için projeyi derleyin.

### <a name="job-manager-template-files-and-their-purpose"></a>İş Yöneticisi Şablon dosyaları ve amaçları
Iş Yöneticisi şablonunu kullanarak bir proje oluşturduğunuzda, üç kod dosyası grubu oluşturur:

* Ana program dosyası (Program.cs). Bu, program giriş noktasını ve üst düzey özel durum işlemeyi içerir. Normalde bunu değiştirmeniz gerekmez.
* Çerçeve dizini. Bu, İş Yöneticisi programı tarafından gerçekleştirilen ' demirbaş ' işinden sorumlu olan dosyaları içerir: parametreleri açma, Batch işine görev ekleme vb. Normalde bu dosyaları değiştirmeniz gerekmez.
* İş Bölümlendirici dosyası (JobSplitter.cs). İşte bir işi görevlere bölmek için uygulamaya özgü mantığınızı koyacaksınız.

Elbette, iş ayırma mantığının karmaşıklığına bağlı olarak iş Bölümlendirici kodunuzu desteklemek için gereken ek dosyalar ekleyebilirsiniz.

Şablon,. csproj dosyası, App. config, Packages. config vb. gibi standart .NET proje dosyaları da oluşturur.

Bu bölümün geri kalanı farklı dosyaları ve kod yapılarını açıklar ve her bir sınıfın ne yaptığını açıklar.

![Visual Studio Çözüm Gezgini Iş Yöneticisi şablonu çözümünü gösterme][solution_explorer01]

**Çerçeve dosyaları**

* `Configuration.cs`: Batch hesabı ayrıntıları, bağlantılı depolama hesabı kimlik bilgileri, iş ve görev bilgileri ve iş parametreleri gibi iş yapılandırma verilerinin yüklenmesini kapsar. Ayrıca Batch tanımlı ortam değişkenlerine erişim sağlar (Batch belgelerindeki görevler için ortam ayarları bölümüne bakın. EnvironmentVariable sınıfı aracılığıyla).
* `IConfiguration.cs`: Yapılandırma sınıfının uygulamasını soyutlayan, bu sayede iş bölümlendiricinizi sahte veya sahte bir yapılandırma nesnesi kullanarak test edebilirsiniz.
* `JobManager.cs`: İş Yöneticisi programının bileşenlerini düzenleyin. İş bölümlendiricisini başlatma, iş bölümlendiricisini çağırma ve iş bölümlendiricisi tarafından döndürülen görevleri görev göndericisinden gönderen sorumludur.
* `JobManagerException.cs`: İş yöneticisinin sonlandırılması gereken bir hatayı temsil eder. JobManagerException, sonlandırma kapsamında belirli tanılama bilgilerinin sağlandığı ' beklenen ' hataları kaydırmak için kullanılır.
* `TaskSubmitter.cs`: Bu sınıf, iş bölümlendiricisi tarafından döndürülen görevleri Batch işine eklemekten sorumludur. JobManager sınıfı, görev sırasını verimli bir şekilde işler için toplu olarak toplar, ardından her toplu iş için bir arka plan iş parçacığında Taskgönderenin. SubmitTasks öğesini çağırır.

**İş bölümlendiricisi**

`JobSplitter.cs`: Bu sınıf, işi görevlere bölmek için uygulamaya özel mantık içerir. Framework, yöntem tarafından döndürülen bir dizi görevi elde etmek için JobSplitter. Split yöntemini çağırır. Bu, işinizin mantığını ekleyebileceğiniz sınıftır. İşi bölümlemek istediğiniz görevleri temsil eden bir CloudTask örneği dizisi döndürmek için Split yöntemini uygulayın.

**Standart .NET komut satırı proje dosyaları**

* `App.config`: Standart .NET uygulama yapılandırma dosyası.
* `Packages.config`: Standart NuGet paket bağımlılığı dosyası.
* `Program.cs`: Program giriş noktasını ve üst düzey özel durum işlemeyi içerir.

### <a name="implementing-the-job-splitter"></a>İş bölümlendiricisini uygulama
Iş Yöneticisi Şablon projesini açtığınızda, proje varsayılan olarak JobSplitter.cs dosyası açılır. Split () metodunu aşağıda göster ' i kullanarak, iş yükünüze görevler için bölünmüş mantığı uygulayabilirsiniz:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> `Split()` Yöntemi içindeki açıklamalı bölüm, işlerinizi farklı görevlere bölmek için mantık ekleyerek değiştirmeniz amaçlanan iş Yöneticisi şablonu kodunun tek bölümüdür. Şablonun farklı bir bölümünü değiştirmek istiyorsanız, lütfen familiarized olduğunuzdan emin olun ve [Batch kodu örneklerinin][github_samples]birkaçını deneyin.
> 
> 

Bölünmüş () uygulamanızın erişimi vardır:

* `_parameters` Alan aracılığıyla iş parametreleri.
* `_job` Alanı aracılığıyla işi temsil eden cloudjob nesnesi.
* `_jobManagerTask` Alan aracılığıyla iş yöneticisi görevini temsil eden cloudtask nesnesi.

`Split()` Uygulamanızın projeye doğrudan görev eklemesi gerekmez. Bunun yerine, kodunuz CloudTask nesnelerinin bir dizisini döndürmelidir ve bunlar iş bölümlendiricisini çağıran çerçeve sınıfları tarafından otomatik olarak işe eklenir. Bu, görevlerin tüm görevlerin C#hesaplanmasını beklemek yerine`yield return`mümkün olan en kısa sürede çalışmaya başlamasını sağlamak üzere iş bölümlendiricileri uygulamak için Yineleyici () özelliği kullanılması yaygındır.

**İş Bölümlendirici hatası**

İş bölümlendiricinizi bir hatayla karşılaşırsa, aşağıdakilerden biri olmalıdır:

* Sırayı kullanarak C# `yield break` sırayı sonlandırın, bu durumda iş Yöneticisi başarılı olarak kabul edilir veya
* Bir özel durum oluşturun, bu durumda iş yöneticisi başarısız olarak kabul edilir ve istemcinin onu nasıl yapılandırdığına bağlı olarak yeniden denenebilir).

Her iki durumda da, iş bölümlendiricisi tarafından zaten döndürülen ve Batch işine eklenen tüm görevler çalışmaya uygun olacaktır. Bunun gerçekleşmesini istemiyorsanız, şunları yapabilirsiniz:

* İş bölümlendiricisini döndürmeden önce işi Sonlandır
* Tüm görev koleksiyonunu döndürmeden önce formüle koyun `ICollection<CloudTask>` C# (yani, bir yineleyici kullanarak iş bölümlendiricisini `IList<CloudTask>` uygulamak yerine bir veya döndürür)
* Tüm görevlerin iş yöneticisinin başarılı bir şekilde tamamlanmasına bağlı olması için görev bağımlılıklarını kullanın

**İş Yöneticisi yeniden denemeleri**

İş Yöneticisi başarısız olursa, istemci yeniden deneme ayarlarına bağlı olarak Batch hizmeti tarafından yeniden denenebilir. Genel olarak, bu güvenli bir uygulamadır çünkü Framework işe görevler eklediğinde, zaten mevcut olan tüm görevleri yoksayar. Ancak, görevlerin hesaplanması pahalı ise, işe zaten eklenmiş olan yeniden hesaplama görevlerinin maliyetine tabi olmak istemiyor olabilirsiniz. Buna karşılık, yeniden çalıştırmanın aynı görev kimliklerini üretme garantisi yoksa, ' yinelenenleri Yoksay ' davranışı iade etmez. Bu gibi durumlarda, daha önce yapılmış olan ve tekrarlamamış işi tespit etmek için iş bölümlendiricinizi tasarlamanız gerekir, örneğin, görevleri gerçekleştirmeye başlamadan önce CloudJob. ListTasks gerçekleştirerek.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Iş Yöneticisi şablonundaki çıkış kodları ve özel durumlar
Çıkış kodları ve özel durumlar, bir program çalıştırmanın sonucunu belirlemek için bir mekanizma sağlar ve programın yürütülmesiyle ilgili sorunları belirlemenize yardımcı olabilir. Iş Yöneticisi şablonu, bu bölümde açıklanan çıkış kodlarını ve özel durumları uygular.

Iş Yöneticisi şablonuyla uygulanan bir iş Yöneticisi görevi olası üç çıkış kodu döndürebilir:

| Kod | Açıklama |
| --- | --- |
| 0 |İş Yöneticisi başarıyla tamamlandı. İş Bölümlendirici kodunuz tamamlanana kadar çalıştırıldı ve işe tüm görevler eklendi. |
| 1\. |İş Yöneticisi görevi, programın "beklenen" bölümünde bir özel durumla başarısız oldu. Özel durum, tanılama bilgileri ve mümkün olduğunda hatayı çözümlemek için öneriler içeren bir JobManagerException 'a çevrilmiştir. |
| 2 |İş Yöneticisi görevi ' beklenmeyen ' özel durumuyla başarısız oldu. Özel durum standart çıktıya oturum açtı, ancak iş Yöneticisi herhangi bir ek tanılama veya düzeltme bilgisi ekleyemedi. |

İş Yöneticisi görev hatası durumunda, bazı görevler yine de hata yapılmadan önce hizmete eklenmiş olabilir. Bu görevler normal olarak çalışır. Bu kod yolu hakkında tartışmak için yukarıdaki "Iş Bölümlendirici hatası" başlığına bakın.

Özel durumlar tarafından döndürülen tüm bilgiler stdout. txt ve stderr. txt dosyalarına yazılır. Daha fazla bilgi için bkz. [hata işleme](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>İstemci konuları
Bu bölümde, bu şablona dayalı bir iş Yöneticisi çağrılırken bazı istemci uygulama gereksinimleri açıklanmaktadır. Parametreleri ve ortam ayarlarını geçirme hakkında ayrıntılı bilgi için [istemci kodundan parametreleri ve ortam değişkenlerini](#pass-environment-settings) geçirme konusuna bakın.

**Zorunlu kimlik bilgileri**

Azure Batch işine görev eklemek için, İş Yöneticisi görevi Azure Batch hesap URL 'SI ve anahtarınızı gerektirir. Bunları YOUR_BATCH_URL ve YOUR_BATCH_KEY adlı ortam değişkenlerine geçirmeniz gerekir. Bunları, Iş Yöneticisi görev ortamı ayarlarında ayarlayabilirsiniz. Örneğin, bir C# istemcide:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Depolama kimlik bilgileri**

Genellikle, istemci, (a) çoğu iş yöneticisinin bağlı depolama hesabına açık bir şekilde erişmesi gerekmeyen ve (b) bağlı depolama hesabı genellikle tüm görevlere bir olarak sağlandığı için, İş Yöneticisi görevine bağlı depolama hesabı kimlik bilgileri sağlaması gerekmez. iş için ortak ortam ayarı. Bağlı depolama hesabını ortak ortam ayarları aracılığıyla sağlamadıysanız ve iş yöneticisinin bağlı depolamaya erişmesi gerekiyorsa, bağlantılı depolama kimlik bilgilerini aşağıdaki gibi sağlamalısınız:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**İş Yöneticisi görev ayarları**

İstemci, İş Yöneticisi *killJobOnCompletion* bayrağını **false**olarak ayarlamış olmalıdır.

Genellikle istemcinin *Rundışlamalı* **değerini false**olarak ayarlaması güvenlidir.

İstemci, iş yöneticisinin yürütülebilir dosyasını (ve gerekli dll 'Leri) işlem düğümüne dağıtmaları için *ResourceFiles* veya *applicationpackagereferler* koleksiyonunu kullanmalıdır.

Varsayılan olarak, iş yöneticisi başarısız olursa yeniden denenmeyecektir. İş Yöneticisi mantığınıza bağlı olarak, istemci yeniden denemeleri*maxtaskretrycount*yöntemiyle/etkinleştirmek isteyebilir.

**İş ayarları**

İş Bölümlendirici görevleri bağımlılıklar halinde yayıyorsa, istemci, işin usesTaskDependencies değerini doğru olarak ayarlamış olmalıdır.

İş Bölümlendirici modelinde, istemcilerin iş bölümlendiricisi tarafından oluşturulan ve üzerinde görevlere görev eklemesi olağan dışı bir şeydir. Bu nedenle, istemci normalde işin *onalltaskstamamlamayı* **terminatejob 'un engellenmesi**olarak ayarlamış olmalıdır.

## <a name="task-processor-template"></a>Görev Işlemcisi şablonu
Bir görev Işlemcisi şablonu, aşağıdaki eylemleri gerçekleştirebilecek bir görev işlemcisi uygulamanıza yardımcı olur:

* Her Batch görevinin çalışması için gereken bilgileri ayarlayın.
* Her Batch görevinin gerektirdiği tüm eylemleri çalıştırın.
* Görev çıktılarını kalıcı depolamaya kaydedin.

Görev işlemcisinin görevleri toplu Iş üzerinde çalıştırması gerekli olmasa da, bir görev işlemcisi kullanmanın önemli avantajı, tüm görev yürütme eylemlerini tek bir konumda uygulamak için sarmalayıcı sunabilmeleridir. Örneğin, her bir görevin bağlamında birkaç uygulama çalıştırmanız gerekiyorsa veya her görevi tamamladıktan sonra verileri kalıcı depolamaya kopyalamanız gerekiyorsa.

Görev işlemcisi tarafından gerçekleştirilen eylemler, iş yükünüzün gerektirdiği kadar basit veya karmaşık ve çok az sayıda olabilir. Ayrıca, tüm görev eylemlerini tek bir görev işlemcisinde uygulayarak, uygulamalarda veya iş yükü gereksinimlerinde yapılan değişikliklere göre eylemleri kolayca güncelleştirebilir veya ekleyebilirsiniz. Ancak, bazı durumlarda, bir görev işlemcisi uygulamanızın en iyi çözümü olmayabilir, örneğin, basit bir komut satırından hızlı bir şekilde başlatılabilen işleri çalıştırırken.

### <a name="create-a-task-processor-using-the-template"></a>Şablonu kullanarak bir görev Işlemcisi oluşturma
Daha önce oluşturduğunuz çözüme bir görev işlemcisi eklemek için aşağıdaki adımları izleyin:

1. Visual Studio 'da mevcut çözümünüzü açın.
2. Çözüm Gezgini, çözüme sağ tıklayın, **Ekle**' ye tıklayın ve ardından **Yeni proje**' ye tıklayın.
3. **Görsel C#** altında **bulut**' a ve ardından **Azure Batch görev işlemcisi**' ne tıklayın.
4. Uygulamanızı açıklayan bir ad yazın ve bu projeyi görev işlemcisi olarak (ör. "LitwareTaskProcessor").
5. Projeyi oluşturmak için **Tamam**' a tıklayın.
6. Son olarak, Visual Studio 'Yu başvurulan tüm NuGet paketlerini yüklemeye zorlamak ve projeyi değiştirmeye başlamadan önce geçerli olduğunu doğrulamak için projeyi derleyin.

### <a name="task-processor-template-files-and-their-purpose"></a>Görev Işlemcisi şablon dosyaları ve amaçları
Görev işlemcisi şablonunu kullanarak bir proje oluşturduğunuzda, üç kod dosyası grubu oluşturur:

* Ana program dosyası (Program.cs). Bu, program giriş noktasını ve üst düzey özel durum işlemeyi içerir. Normalde bunu değiştirmeniz gerekmez.
* Çerçeve dizini. Bu, İş Yöneticisi programı tarafından gerçekleştirilen ' demirbaş ' işinden sorumlu olan dosyaları içerir: parametreleri açma, Batch işine görev ekleme vb. Normalde bu dosyaları değiştirmeniz gerekmez.
* Görev işlemcisi dosyası (TaskProcessor.cs). Bu, bir görevi yürütmek için uygulamaya özgü mantığınızı (genellikle varolan bir yürütülebiliri çağırarak) koyacaksınız. Ek verileri indirme veya sonuç dosyalarını karşıya yükleme gibi ön ve son işleme kodu da buraya gider.

Tabii ki, iş bölme mantığının karmaşıklığına bağlı olarak, görev işlemcisi kodunuzu desteklemek için gereken ek dosyalar ekleyebilirsiniz.

Şablon,. csproj dosyası, App. config, Packages. config vb. gibi standart .NET proje dosyaları da oluşturur.

Bu bölümün geri kalanı farklı dosyaları ve kod yapılarını açıklar ve her bir sınıfın ne yaptığını açıklar.

![Görev Işlemcisi şablonu çözümünü gösteren Visual Studio Çözüm Gezgini][solution_explorer02]

**Çerçeve dosyaları**

* `Configuration.cs`: Batch hesabı ayrıntıları, bağlantılı depolama hesabı kimlik bilgileri, iş ve görev bilgileri ve iş parametreleri gibi iş yapılandırma verilerinin yüklenmesini kapsar. Ayrıca Batch tanımlı ortam değişkenlerine erişim sağlar (Batch belgelerindeki görevler için ortam ayarları bölümüne bakın. EnvironmentVariable sınıfı aracılığıyla).
* `IConfiguration.cs`: Yapılandırma sınıfının uygulamasını soyutlayan, bu sayede iş bölümlendiricinizi sahte veya sahte bir yapılandırma nesnesi kullanarak test edebilirsiniz.
* `TaskProcessorException.cs`: İş yöneticisinin sonlandırılması gereken bir hatayı temsil eder. TaskProcessorException, belirli tanılama bilgilerinin sonlandırma kapsamında sağlandığı "beklenen" hataları kaydırmak için kullanılır.

**Görev Işlemcisi**

* `TaskProcessor.cs`: Görevi çalıştırır. Framework TaskProcessor. Run yöntemini çağırır. Bu, görevin uygulamaya özgü mantığını ekleyebileceğiniz sınıftır. Çalıştırma yöntemini şu şekilde uygulayın:
  * Tüm görev parametrelerini ayrıştırma ve doğrulama
  * Çağırmak istediğiniz herhangi bir dış program için komut satırını oluşturun
  * Hata ayıklama amacıyla gerekli olabilecek tüm tanılama bilgilerini günlüğe kaydedin
  * Bu komut satırını kullanarak bir işlem başlatın
  * İşlemin çıkış gelmesini bekle
  * İşlemin başarılı veya başarısız olup olmadığını anlamak için çıkış kodunu yakala
  * Kalıcı depolamaya devam etmek istediğiniz tüm çıktı dosyalarını kaydedin

**Standart .NET komut satırı proje dosyaları**

* `App.config`: Standart .NET uygulama yapılandırma dosyası.
* `Packages.config`: Standart NuGet paket bağımlılığı dosyası.
* `Program.cs`: Program giriş noktasını ve üst düzey özel durum işlemeyi içerir.

## <a name="implementing-the-task-processor"></a>Görev işlemcisini uygulama
Görev Işlemcisi şablonu projesini açtığınızda, proje varsayılan olarak TaskProcessor.cs dosyası açılır. Aşağıda gösterilen Run () yöntemini kullanarak, iş yükünüze görevler için çalıştırma mantığını uygulayabilirsiniz:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> Run () yönteminde açıklamalı bölümü, görev Işlemcisi şablon kodunun, iş yükünüze görevlere yönelik çalıştırma mantığı ekleyerek değiştirmeniz amaçlanan tek bölümüdür. Şablonun farklı bir bölümünü değiştirmek istiyorsanız, lütfen toplu iş belgelerini inceleyerek ve toplu Iş kodu örneklerinden birkaçını deneyerek Batch 'in nasıl çalıştığını öğrenin.
> 
> 

Run () yöntemi, komut satırını başlatma, bir veya daha fazla işlem başlatma, tüm işlemlerin tamamlanmasını bekleme, sonuçları kaydetme ve son olarak bir çıkış kodu ile döndürme işlemlerinden sorumludur. Run () yöntemi, görevleriniz için işleme mantığını uyguladığınız yerdir. Görev işlemcisi çerçevesi, sizin için Run () yöntemini çağırır; kendiniz çağırmanız gerekmez.

Run () uygulamanızın erişimi vardır:

* `_parameters` Alan aracılığıyla görev parametreleri.
* `_jobId` Ve`_taskId` alanları aracılığıyla iş ve görev kimlikleri.
* `_configuration` Alan aracılığıyla görev yapılandırması.

**Görev hatası**

Hata durumunda, bir özel durum oluşturarak Run () yönteminden çıkabilirsiniz, ancak bu, Görev çıkış kodunun denetiminde en üst düzey özel durum işleyicisini bırakır. Örneğin, tanılama amaçları gibi farklı hata türlerini ayırabilmeniz için çıkış kodunu kontrol etmeniz gerekiyorsa veya bazı başarısızlık modları işi sonlandırmalı ve diğerlerinin olmaması gerektiğinden, sıfır olmayan bir örnek döndürerek Run () yönteminden çıkmanız gerekir çıkış kodu. Bu, Görev çıkış kodu olur.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Görev Işlemcisi şablonundaki çıkış kodları ve özel durumlar
Çıkış kodları ve özel durumlar, bir program çalıştırmanın sonucunu belirlemek için bir mekanizma sağlar ve programın yürütülmesiyle ilgili sorunları belirlemenize yardımcı olabilir. Görev Işlemcisi şablonu, bu bölümde açıklanan çıkış kodlarını ve özel durumları uygular.

Görev Işlemcisi şablonuyla uygulanan bir görev işlemcisi görevi, olası üç çıkış kodu döndürebilir:

| Kod | Açıklama |
| --- | --- |
| [Process. ExitCode][process_exitcode] |Görev işlemcisi tamamlamaya çalıştı. Bu, çağırılan programın başarılı olduğunu göstermez; yalnızca görev işlemcisinin başarıyla çağırdığına ve özel durumlar olmadan işlem sonrası işlemleri gerçekleştirdiğine unutmayın. Çıkış kodunun anlamı çağrılan programa bağlıdır – genellikle çıkış kodu 0, programın başarılı olduğu ve diğer çıkış kodunun programın başarısız olduğu anlamına gelir. |
| 1\. |Görev işlemcisi, programın "beklenen" bölümünde bir özel durumla başarısız oldu. Özel durum, tanılama bilgileri ve `TaskProcessorException` mümkün olduğunda hata çözme önerilerini içeren bir öğesine çevrilmiştir. |
| 2 |Görev işlemcisi ' beklenmeyen ' bir özel durumla başarısız oldu. Özel durum standart çıktıya oturum açtı, ancak görev işlemcisi herhangi bir ek tanılama veya düzeltme bilgisi ekleyemedi. |

> [!NOTE]
> Çağırmak gerekirse, belirli başarısızlık modlarını göstermek için çıkış kodları 1 ve 2 ' yi kullanıyorsa, 1. ve 2. görev işlemcisi hataları için 2 çıkış kodlarını kullanmak belirsizdir. Bu görev işlemcisi hata kodlarını, Program.cs dosyasındaki özel durum durumlarını düzenleyerek, ayırt edici çıkış kodlarına değiştirebilirsiniz.
> 
> 

Özel durumlar tarafından döndürülen tüm bilgiler stdout. txt ve stderr. txt dosyalarına yazılır. Daha fazla bilgi için bkz. Batch belgelerindeki hata Işleme.

### <a name="client-considerations"></a>İstemci konuları
**Depolama kimlik bilgileri**

Görev işlemciniz çıktıları kalıcı hale getirmek için Azure Blob Storage kullanıyorsa, örneğin dosya kuralları yardımcı kitaplığını kullanarak, bulut depolama hesabı kimlik bilgilerine *veya* paylaşılan erişim IÇEREN bir blob kapsayıcı URL 'sine erişim gerekir imza (SAS). Şablon, ortak ortam değişkenleri aracılığıyla kimlik bilgileri sağlamaya yönelik destek içerir. İstemciniz depolama kimlik bilgilerini aşağıdaki gibi geçirebilir:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Daha sonra depolama hesabı, `_configuration.StorageAccount` özelliği aracılığıyla taskprocessor sınıfında kullanılabilir.

SAS ile bir kapsayıcı URL 'SI kullanmayı tercih ediyorsanız, bunu bir iş ortak ortamı ayarı aracılığıyla da geçirebilirsiniz, ancak görev işlemcisi şablonu şu anda bunun için yerleşik destek içermez.

**Depolama kurulumu**

Görevleri işe eklemeden önce, istemci veya iş yöneticisi görevinin görevler için gereken kapsayıcıları oluşturması önerilir. Bu, bir URL 'nin kapsayıcıyı oluşturma izni içermediği için SAS içeren bir kapsayıcı URL 'SI kullanırsanız zorunludur. Kapsayıcıda CloudBlobContainer. CreateIfNotExistsAsync çağrısı yapmak zorunda olan her görevi kaydettiği için, depolama hesabı kimlik bilgilerini geçirirseniz bile bu önerilir.

## <a name="pass-parameters-and-environment-variables"></a>Parametreleri ve ortam değişkenlerini geçir
### <a name="pass-environment-settings"></a>Ortam ayarlarını geçir
İstemci, ortam ayarları biçiminde iş Yöneticisi görevine bilgi geçirebilir. Bu bilgiler daha sonra, işlem işinin bir parçası olarak çalışacak görev işlemcisi görevleri oluştururken iş Yöneticisi görevi tarafından kullanılabilir. Ortam ayarları olarak geçirebilmeniz için bilgilere örnek olarak şunlar verilebilir:

* Depolama hesabı adı ve hesap anahtarları
* Batch hesabı URL 'SI
* Batch hesabı anahtarı

Batch hizmeti, `EnvironmentSettings` [Microsoft. Azure. Batch. jobmanagertask][net_jobmanagertask]özelliğini kullanarak ortam ayarlarını bir iş Yöneticisi görevine geçirmek için basit bir mekanizmaya sahiptir.

Örneğin, bir Batch hesabının `BatchClient` örneğini almak için, istemci kodundan ortam değişkenleri olarak, Batch hesabının URL 'sini ve paylaşılan anahtar kimlik bilgilerini aktarabilirsiniz. Benzer şekilde, Batch hesabına bağlı depolama hesabına erişmek için, depolama hesabı adını ve depolama hesabı anahtarını ortam değişkenleri olarak geçirebilirsiniz.

### <a name="pass-parameters-to-the-job-manager-template"></a>Parametreleri Iş Yöneticisi şablonuna geçirme
Çoğu durumda, iş ayırma işlemini denetlemek veya iş için görevleri yapılandırmak üzere iş başına parametreleri iş Yöneticisi görevine geçirmek yararlı olur. Bunu, İş Yöneticisi görevi için bir kaynak dosyası olarak Parameters. JSON adlı bir JSON dosyasını karşıya yükleyerek yapabilirsiniz. Parametreler daha sonra iş Yöneticisi şablonundaki `JobSplitter._parameters` alanda kullanılabilir hale gelebilir.

> [!NOTE]
> Yerleşik parametre işleyicisi yalnızca dize-dize sözlüklerini destekler. Karmaşık JSON değerlerini parametre değerleri olarak geçirmek istiyorsanız, bunları dizeler olarak iletmeniz ve bunları iş bölümlendiriculaşarak ayrıştırabilmeniz ya da Framework `Configuration.GetJobParameters` metodunu değiştirmeniz gerekir.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Parametreleri görev Işlemcisi şablonuna geçirme
Ayrıca, parametreleri görev Işlemcisi şablonu kullanarak uygulanan tek tek görevlere geçirebilirsiniz. İş Yöneticisi şablonunda olduğu gibi, görev işlemcisi şablonu, adlı bir kaynak dosyasını arar

Parameters. JSON, ve bulunursa parametre sözlüğü olarak yükler. Parametreleri görev işlemcisi görevlerine geçirmeye yönelik birkaç seçenek vardır:

* JSON iş parametrelerini yeniden kullanın. Yalnızca parametreler iş genelinde ise (örneğin, bir işleme yüksekliği ve genişliği), bu işlem iyi bir şekilde gerçekleşir. Bunu yapmak için, iş bölümlendiricbir cloudtask oluştururken, iş yöneticisi görevinin ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) öğesine Parameters. JSON kaynak dosyası nesnesine bir başvuru ekleyin.
* Göreve özgü parametreler. JSON belgesini iş Bölümlendirici yürütmesinin parçası olarak oluşturup karşıya yükleyin ve görevin kaynak dosyaları koleksiyonunda blob 'a başvurun. Farklı görevler farklı parametrelere sahip ise bu gereklidir. Örnek, çerçeve dizininin bir parametre olarak göreve geçirildiği bir 3B işleme senaryosu olabilir.

> [!NOTE]
> Yerleşik parametre işleyicisi yalnızca dize-dize sözlüklerini destekler. Karmaşık JSON değerlerini parametre değerleri olarak geçirmek istiyorsanız, bunları dizeler olarak iletmeniz ve bunları görev işlemcisinde ayrıştırabilmeniz ya da Framework `Configuration.GetTaskParameters` metodunu değiştirmeniz gerekir.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
### <a name="persist-job-and-task-output-to-azure-storage"></a>Azure Storage 'da iş ve görev çıkışını kalıcı hale getirme
Batch çözüm geliştirmede başka bir yararlı araç [Azure Batch dosya kuralları][nuget_package]. Azure depolama 'ya ve Azure Storage 'a yönelik görev çıkışlarını kolayca depolamak ve almak için Batch .NET uygulamalarınızda bu .NET sınıf kitaplığını (Şu anda önizleme aşamasında) kullanın. [Kalıcı Azure Batch işi ve görev çıktısı](batch-task-output.md) , kitaplığın ve kullanımının tam bir tartışmasını içerir.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
