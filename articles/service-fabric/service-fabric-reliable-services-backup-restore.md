---
title: Service Fabric Yedekleme ve Geri Yükleme
description: Service Fabric yedekleme ve geri yükleme için kavramsal belgeler, güvenilir durum bilgisi olan hizmet ve Reliable Actors yedeklemesini yapılandırmaya yönelik bir hizmettir.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: 2674d1285544e4bc9b6fcb3d0b2e6f4b607786a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791620"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Reliable Services ve Reliable Actors yedekleme ve geri yükleme
Azure Service Fabric, bu yüksek kullanılabilirliği korumak için durumu birden çok düğüm genelinde çoğaltan yüksek kullanılabilirliğe sahip bir platformdur.  Bu nedenle, kümedeki bir düğüm başarısız olsa bile hizmetler kullanılabilir olmaya devam eder. Platform tarafından sunulan bu yerleşik yedeklik, bazı durumlarda hizmetin verileri (bir dış depoya) yedeklemesi için gerekli olabilir.

> [!NOTE]
> Veri kaybı senaryolarından kurtarabilmeniz için verilerinizi yedeklemek ve geri yüklemek (ve beklendiği gibi çalıştığını test etmek) önemlidir.
> 

> [!NOTE]
> Microsoft, güvenilir durum bilgisi olan hizmetler ve Reliable Actors veri yedeklemesini yapılandırmak için [düzenli yedekleme ve geri yükleme](service-fabric-backuprestoreservice-quickstart-azurecluster.md) kullanmayı önerir. 
> 


Örneğin, bir hizmet aşağıdaki senaryolardan korunmak için verileri yedeklemek isteyebilir:

- Service Fabric kümesinin tamamının kalıcı kaybolması durumunda.
- Bir hizmet bölümünün çoğaltmalarının çoğunluğunun kalıcı kaybolması
- Durumun yanlışlıkla silindiği veya bozulduğu yönetim hataları. Örneğin, yeterli ayrıcalığa sahip bir yönetici hizmeti yanlışlıkla silerse bu durum oluşabilir.
- Hizmette veri bozulmasına neden olan hatalar. Örneğin, bu durum bir hizmet kodu yükseltmesinin hatalı verileri güvenilir bir koleksiyona yazmaya başladığında meydana gelebilir. Böyle bir durumda, hem kod hem de verilerin önceki bir duruma döndürülmesi gerekebilir.
- Çevrimdışı veri işleme. Veri üreten hizmetten ayrı olarak gerçekleşen iş zekası için verilerin çevrimdışı işlenmesini sağlamak uygun olabilir.

Yedekleme/geri yükleme özelliği, Reliable Services API üzerinde oluşturulan hizmetlerin yedeklemeleri oluşturup geri yüklemesine olanak tanır. Platform tarafından sunulan yedekleme API 'Leri, okuma veya yazma işlemlerini engellemeden bir hizmet bölümünün durumunun yedeğine izin verir. Geri yükleme API 'Leri, bir hizmet bölümünün durumunun seçili bir yedekten geri yüklenmesine izin verir.

## <a name="types-of-backup"></a>Yedekleme türleri
İki yedekleme seçeneği vardır: tam ve artımlı.
Tam yedekleme, çoğaltma durumunu yeniden oluşturmak için gereken tüm verileri içeren bir yedeklemedir: kontrol noktaları ve tüm günlük kayıtları.
Denetim noktaları ve günlüğe sahip olduğundan, tam yedekleme kendi kendine geri yüklenebilir.

Tam yedeklemelerle ilgili sorun, kontrol noktaları büyük olduğunda ortaya çıkar.
Örneğin, 16 GB durumuna sahip bir çoğaltma, yaklaşık 16 GB 'a kadar olan denetim noktalarına sahip olur.
Beş dakikalık bir kurtarma noktası hedefi varsa, çoğaltmanın her beş dakikada bir yedeklenmesi gerekir.
Her yedeklediğinde, 16 GB 'lık denetim noktalarının yanı sıra 50 MB 'lık (yapılandırılabilir kullanılarak yapılandırılabilir) günlük olarak kopyalanması gerekir `CheckpointThresholdInMB` .

![Tam yedekleme örneği.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Bu sorunun çözümü, arka arkaya yalnızca son yedeklemeden bu yana değiştirilen günlük kayıtlarını içeren artımlı yedeklemedir.

![Artımlı yedekleme örneği.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Artımlı yedeklemeler yalnızca son yedeklemeden (kontrol noktaları dahil değil) beri değiştiği için, bu, daha hızlı bir şekilde, ancak kendi kendilerine geri yüklenemez.
Artımlı bir yedeklemeyi geri yüklemek için tüm yedekleme zinciri gereklidir.
Yedekleme zinciri, bir tam yedekleme ile başlayan ve ardından bir dizi bitişik artımlı yedekleme ile bir yedekleme zinciridir.

## <a name="backup-reliable-services"></a>Yedekleme Reliable Services
Hizmet yazarı, yedeklemelerin ne zaman ve yedeklemelerin depolanacağı üzerinde tam denetime sahip olur.

Bir yedekleme başlatmak için, hizmetin devralınan üye işlevini çağırması gerekir `BackupAsync` .  
Yedeklemeler yalnızca birincil çoğaltmalardan yapılabilir ve yazma durumunun verilmesini gerektirir.

Aşağıda gösterildiği gibi, bir `BackupAsync` nesnesi, `BackupDescription` bir, `Func<< BackupInfo, CancellationToken, Task<bool>>>` yedekleme klasörü yerel olarak oluşturulduğunda ve bir dış depolamaya taşımaya hazır olduğunda çağrılan bir geri çağırma işlevi ve bir tam veya artımlı yedekleme belirtebileceğiniz bir nesneyi alır.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Artımlı yedekleme alma isteği ile başarısız olabilir `FabricMissingFullBackupException` . Bu özel durum aşağıdakilerden biri olduğunu gösterir:

- çoğaltma, birincil hale geldiği için hiçbir şekilde tam yedekleme gerçekleştirmiştir,
- son yedeklemeden bu yana olan günlük kayıtlarının bazıları kesildi veya
- çoğaltma sınırı geçti `MaxAccumulatedBackupLogSizeInMB` .

Kullanıcılar, veya yapılandırarak artımlı yedeklemeler yapma olasılığını artırabilir `MinLogSizeInMB` `TruncationThresholdFactor` .
Bu değerlerin artırılması, çoğaltma diski kullanımı başına artar.
Daha fazla bilgi için bkz. [Reliable Services Configuration](service-fabric-reliable-services-configuration.md)

`BackupInfo` çalışma zamanının yedeklemeyi () kaydettiği klasörün konumu dahil olmak üzere yedeklemeyle ilgili bilgiler sağlar `BackupInfo.Directory` . Geri çağırma işlevi, öğesini bir `BackupInfo.Directory` dış depoya veya başka bir konuma taşıyabilir.  Bu işlev ayrıca yedekleme klasörünü hedef konumuna başarıyla taşıyıp taşıyamadığını belirten bir bool döndürür.

Aşağıdaki kod, `BackupCallbackAsync` Azure depolama 'ya yedekleme yüklemek için yönteminin nasıl kullanılabileceğini göstermektedir:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

Önceki örnekte, `ExternalBackupStore` Azure Blob depolama ile arabirim sağlamak için kullanılan örnek sınıftır ve, `UploadBackupFolderAsync` klasörü sıkıştırır ve Azure Blob deposuna yerleştirir yöntemidir.

Şunlara dikkat edin:

  - Herhangi bir zamanda çoğaltma başına yalnızca bir yedekleme işlemi olabilir. `BackupAsync`Aynı anda birden fazla çağrı, `FabricBackupInProgressException` Esnek yedeklemeleri bir ile sınırlamak için oluşturulur.
  - Bir yedekleme devam ederken bir çoğaltma yük devreder, yedekleme tamamlanmamış olabilir. Bu nedenle, yük devretme işlemi tamamlandıktan sonra, gereken şekilde çağırarak yedeklemenin yeniden başlatılması sorumluluğu hizmetin sorumluluğundadır `BackupAsync` .

## <a name="restore-reliable-services"></a>Geri yükleme Reliable Services
Genel olarak, bir geri yükleme işlemi gerçekleştirmeniz gerekebilecek durumlar şu kategorilerden birine girer:

  - Hizmet bölümü kayıp verileri. Örneğin, bir bölüm (birincil çoğaltma dahil) için üç çoğaltma için olan disk bozulur veya silinir. Yeni birincili verilerin bir yedekten geri yüklenmesi gerekebilir.
  - Hizmetin tamamı kaybedilir. Örneğin, bir yönetici tüm hizmeti kaldırır ve bu nedenle hizmetin ve verilerin geri yüklenmesi gerekir.
  - Hizmet bozuk uygulama verilerini (örneğin, bir uygulama hatası nedeniyle) çoğalttı. Bu durumda, bozulmanın nedenini kaldırmak için hizmetin yükseltilmesi veya döndürülmesi gerekir ve bozuk olmayan veriler geri yüklenmelidir.

Birçok yaklaşım mümkün olsa da, `RestoreAsync` Yukarıdaki senaryolardan kurtarmak için kullanırken bazı örnekler sunuyoruz.

## <a name="partition-data-loss-in-reliable-services"></a>Reliable Services veri kaybını bölümle
Bu durumda, çalışma zamanı otomatik olarak veri kaybını algılar ve `OnDataLossAsync` API 'yi çağırır.

Kurtarmak için hizmet yazarının aşağıdakileri gerçekleştirmesi gerekir:

  - Sanal temel sınıf yöntemini geçersiz kılın `OnDataLossAsync` .
  - Hizmetin yedeklemelerini içeren dış konumda en son yedeklemeyi bulun.
  - En son yedeklemeyi indirin (ve sıkıştırılmış dosyayı yedekleme klasörüne açın).
  - `OnDataLossAsync`Yöntemi bir sağlar `RestoreContext` . `RestoreAsync`Sağlanmış olan API 'yi çağırın `RestoreContext` .
  - Geri yükleme başarılı olduysa doğru döndürün.

Aşağıda, yönteminin örnek bir uygulamasıdır `OnDataLossAsync` :

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription``RestoreContext.RestoreAsync`çağrıya geçilen adlı bir üye içeriyor `BackupFolderPath` .
Tek bir tam yedeklemeyi geri yüklerken, bu, `BackupFolderPath` tam yedeklemenizi içeren klasörün yerel yolu olarak ayarlanmalıdır.
Tam yedeklemeyi ve bir dizi artımlı yedeklemeyi geri yüklerken, `BackupFolderPath` yalnızca tam yedeklemeyi içermeyen klasörün yerel yoluna ve ayrıca tüm artımlı yedeklemelere ayarlanmalıdır.
`RestoreAsync``FabricMissingFullBackupException` `BackupFolderPath` belirtilen bir tam yedekleme içermiyorsa, çağrı oluşturabilir.
Ayrıca, `ArgumentException` `BackupFolderPath` bir artımlı yedekleme zinciri varsa, bu da oluşturulabilir.
Örneğin, tam yedeklemeyi içeriyorsa, ilk artımlı ve üçüncü artımlı yedekleme, ancak ikinci artımlı yedekleme yok.

> [!NOTE]
> RestorePolicy varsayılan olarak güvenli olarak ayarlanır.  Bu, `RestoreAsync` Yedekleme klasörünün bu çoğaltmada bulunan duruma eşit veya ondan daha eski bir durum içerdiğini algıladığında, bu API 'Nin ArgumentException ile başarısız olacağı anlamına gelir.  `RestorePolicy.Force` Bu güvenlik denetimini atlamak için kullanılabilir. Bu bir parçası olarak belirtilir `RestoreDescription` .
> 

## <a name="deleted-or-lost-service"></a>Silinen veya kayıp hizmet
Bir hizmet kaldırılırsa, verilerin geri yüklenebilmesi için önce hizmeti yeniden oluşturmanız gerekir.  Verilerin sorunsuz bir şekilde geri yüklenebilmesi için, aynı yapılandırmayla (örneğin bölümlendirme şeması) birlikte hizmetin oluşturulması önemlidir.  Hizmet kurulduktan sonra, bu hizmetin her bölümünde verileri geri yükleme ( `OnDataLossAsync` yukarıda) API 'sinin çağrılması gerekir. Bunu sağlamanın bir yolu, her bölümde [FabricClient. TestManagementClient. StartPartitionDataLossAsync](/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartPartitionDataLossAsync_System_Guid_System_Fabric_PartitionSelector_System_Fabric_DataLossMode_) ' i kullanmaktır.  

Bu noktada, uygulama yukarıdaki senaryodakiyle aynıdır. Her bölümün, son ilgili yedeklemenin dış depodan geri yüklenmesi gerekir. Tek bir desteklenmediği uyarısıyla, çalışma zamanı bölüm kimliklerini dinamik olarak oluşturduğundan bölüm KIMLIĞININ artık değişmiş olabilir. Bu nedenle, her bölüm için geri yüklenecek doğru en son yedeği tanımlamak üzere hizmetin uygun bölüm bilgilerini ve hizmet adını depolaması gerekir.

> [!NOTE]
> `FabricClient.ServiceManager.InvokeDataLossAsync`Hizmetin tamamını geri yüklemek için her bölümde kullanılması önerilmez, çünkü bu durum küme durumlarınızın bozulmasına neden olabilir.
> 

## <a name="replication-of-corrupt-application-data"></a>Bozuk uygulama verilerinin çoğaltılması
Yeni dağıtılan uygulama yükseltmesinin bir hatası varsa, bu durum verilerin bozulmasına neden olabilir. Örneğin, bir uygulama yükseltmesi, güvenilir bir Sözlükteki her telefon numarası kaydını geçersiz bir alan kodu ile güncelleştirmeye başlayabilir.  Bu durumda, Service Fabric depolanan verilerin doğası farkında olmadığından, geçersiz telefon numaraları çoğaltılır.

Veri bozulmasına neden olan bir egregious hatasını algıladıktan sonra yapmanız gereken ilk şey, uygulamayı uygulama düzeyinde dondurmasıdır ve mümkünse hata olmayan uygulama kodu sürümüne yükseltilir.  Ancak, hizmet kodu sabitledikten sonra bile veriler bozuk olabilir ve bu nedenle verilerin geri yüklenmesi gerekebilir.  Bu gibi durumlarda, en son yedeklemeler de bozuk olabileceğinden en son yedeklemeyi geri yüklemek yeterli olmayabilir.  Bu nedenle, veriler bozuk olmadan önce yapılan son yedeklemeyi bulmanız gerekir.

Hangi yedeklemelerin bozuk olduğundan emin değilseniz, yeni bir Service Fabric kümesi dağıtabilir ve etkilenen bölümlerin yedeklerini yukarıdaki "silinen veya kayıp hizmet" senaryosunda olduğu gibi geri yükleyebilirsiniz.  Her bölüm için en son yedeklemeleri en en en en üst düzeyde geri yüklemeyi başlatın. Bozulmaya sahip olmayan bir yedekleme bulduktan sonra bu bölümün daha yeni olan (Bu yedeklemeden) tüm yedeklerini taşıyın/silin. Her bölüm için bu işlemi tekrarlayın. Artık, `OnDataLossAsync` Üretim kümesindeki bölümde çağrıldığında, dış depoda bulunan son yedekleme, yukarıdaki işlem tarafından seçilen bir yer olacaktır.

Şimdi, "silinen veya kayıp hizmet" bölümündeki adımlar, hata durumu bozulması için hizmetin durumunu geri yüklemek üzere kullanılabilir.

Şunlara dikkat edin:

  - Geri yükleme yaptığınızda, geri yüklenen yedeklemenin, verilerin kaybedilmeden önce bölümün durumundan daha eski olması olasılığı vardır. Bu nedenle, mümkün olduğunca çok veri kurtarmak için en son çare olarak geri yüklemeniz gerekir.
  - Yedekleme klasörü yolunu ve yedekleme klasörü içindeki dosyaların yollarını temsil eden dize, FabricDataRoot yoluna ve uygulama türü adının uzunluğuna bağlı olarak 255 karakterden daha büyük olabilir. Bu, gibi bazı .NET yöntemlerinin `Directory.Move` özel durumu oluşturması için bir yol açabilir `PathTooLongException` . Bir geçici çözüm, gibi Kernel32 API 'Leri doğrudan çağırdır `CopyFile` .

## <a name="back-up-and-restore-reliable-actors"></a>Yedekleme ve geri yükleme Reliable Actors


Reliable Actors Framework Reliable Services üzerine kurulmuştur. Aktörleri barındıran ActorService, durum bilgisi olan güvenilir bir hizmettir. Bu nedenle Reliable Services ' de kullanılabilen tüm yedekleme ve geri yükleme işlevleri Reliable Actors (durum sağlayıcıya özgü davranışlar hariç) için de kullanılabilir. Yedeklemeler bölüm başına temelinde alınacağından, bu bölümdeki tüm aktörlerin yedekleri yedeklenir (ve geri yükleme benzerdir ve bölüm başına temelinde olur). Yedekleme/geri yükleme gerçekleştirmek için, hizmet sahibi ActorService sınıfından türetilen özel bir aktör hizmeti sınıfı oluşturmalı ve daha sonra önceki bölümlerde açıklandığı gibi yedekleme/geri yükleme işlemini Reliable Services benzer hale getirmeniz gerekir.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Özel bir aktör hizmeti sınıfı oluşturduğunuzda, bu, aktör kaydı sırasında da kaydettirmeniz gerekir.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Reliable Actors için varsayılan durum sağlayıcısı `KvsActorStateProvider` . Artımlı yedekleme için varsayılan olarak etkin değildir `KvsActorStateProvider` . `KvsActorStateProvider`Aşağıdaki kod parçacığında gösterildiği gibi, oluşturucusunda uygun ayarla oluşturup daha sonra ActorService oluşturucusuna geçirerek artımlı yedeklemeyi etkinleştirebilirsiniz:

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Artımlı yedekleme etkinleştirildikten sonra, artımlı yedekleme gerçekleştirmek aşağıdaki nedenlerden biri için FabricMissingFullBackupException ile başarısız olabilir ve artımlı yedekleme (ler) almadan önce tam yedekleme yapmanız gerekir:

  - Çoğaltma, birincil hale geldiğinden dolayı hiçbir şekilde tam yedekleme gerçekleştirmez.
  - Son yedeklemeden itibaren günlük kayıtlarının bazıları kesildi.

Artımlı yedekleme etkinleştirildiğinde, `KvsActorStateProvider` günlük kayıtlarını yönetmek için döngüsel arabellek kullanmaz ve düzenli aralıklarla keser. Kullanıcı tarafından 45 dakikalık bir süre boyunca bir yedekleme alınkalmadığında, sistem günlük kayıtlarını otomatik olarak keser. Bu Aralık, Oluşturucu içinde belirtilerek yapılandırılabilir `logTruncationIntervalInMinutes` `KvsActorStateProvider` (artımlı yedeklemeyi etkinleştirirken buna benzer). Birincil çoğaltmanın, tüm verilerini göndererek başka bir çoğaltma oluşturması gerekiyorsa, günlük kayıtları da kesilebilir.

Yedekleme zincirinden, Reliable Services benzer şekilde geri yükleme yaparken, BackupFolderPath, tam yedekleme içeren bir alt dizine ve artımlı yedekleme içeren diğer alt dizinlere sahip alt dizinler içermelidir. Geri yükleme API 'SI, yedekleme zinciri doğrulaması başarısız olursa uygun hata iletisiyle FabricException oluşturur. 

> [!NOTE]
> `KvsActorStateProvider` Şu anda RestorePolicy. Safe seçeneğini yok sayar. Bu özellik için destek, gelecek bir sürümde planlanmaktadır.
> 

## <a name="testing-back-up-and-restore"></a>Yedekleme ve geri yükleme sınanıyor
Kritik verilerin yedeklenmekte olduğundan ve ' den geri yüklenebildiğinden emin olmak önemlidir. Bu, `Start-ServiceFabricPartitionDataLoss` hizmetinize yönelik veri yedekleme ve geri yükleme işlevselliğinin beklendiği gibi çalışıp çalışmadığını test etmek üzere belirli bir bölümdeki veri kaybını karşılayan PowerShell 'deki cmdlet 'i çağırarak yapılabilir.  Ayrıca, veri kaybını ve bu olaydan geri yüklemeyi de programlı bir şekilde çağırmak mümkündür.

> [!NOTE]
> GitHub 'da Web başvuru uygulamasında yedekleme ve geri yükleme işlevselliğinin örnek bir uygulamasını bulabilirsiniz. `Inventory.Service`Daha fazla ayrıntı için lütfen hizmete bakın.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Gelişmiş: yedekleme ve geri yükleme hakkında daha fazla ayrıntı
Yedekleme ve geri yükleme ile ilgili bazı ayrıntılar aşağıda verilmiştir.

### <a name="backup"></a>Backup
Güvenilir durum Yöneticisi herhangi bir okuma veya yazma işlemini engellemeden tutarlı yedeklemeler oluşturma olanağı sağlar. Bunu yapmak için, bir denetim noktası ve günlük Kalıcılık mekanizması kullanır.  Güvenilir durum Yöneticisi, işlem günlüğünden basıncını ortadan kaldırmak ve kurtarma sürelerini geliştirmek için belirli noktalarda belirsiz (hafif) denetim noktaları alır.  `BackupAsync`Çağrıldığında, güvenilir durum Yöneticisi tüm güvenilir nesneleri, en son denetim noktası dosyalarını yerel bir yedekleme klasörüne kopyalayacak şekilde yönlendirir.  Ardından, güvenilir durum Yöneticisi "Başlangıç işaretçisi" den başlayarak yedekleme klasörüne en son günlük kaydına kadar tüm günlük kayıtlarını kopyalar.  En son günlük kaydına kadar olan tüm günlük kayıtları yedeklemeye dahil edildiğinden ve güvenilir durum Yöneticisi sonradan yazma günlüğünü koruyacağı için güvenilir durum Yöneticisi, kaydedilen ( `CommitAsync` başarıyla döndürülen) tüm işlemlerin yedeğe dahil edildiğini garanti eder.

Sonrasında işleme yapılan tüm işlemler `BackupAsync` yedeklenmeyebilir veya yedeklenmeyebilir.  Yerel yedekleme klasörü platform tarafından doldurulduktan sonra (diğer bir deyişle, çalışma zamanı tarafından yerel yedekleme tamamlanır), hizmetin yedekleme geri çağırması çağrılır.  Bu geri çağırma, yedekleme klasörünü Azure depolama gibi bir dış konuma taşımaktan sorumludur.

### <a name="restore"></a>Geri Yükleme
Güvenilir durum Yöneticisi, API 'yi kullanarak bir yedekten geri yükleme özelliği sağlar `RestoreAsync` .  
`RestoreAsync`Üzerinde yöntemi `RestoreContext` yalnızca yöntemi içinde çağrılabilir `OnDataLossAsync` .
Tarafından döndürülen bool, `OnDataLossAsync` hizmetin durumunu dış bir kaynaktan geri yükleyip döndürmediğini belirtir.
`OnDataLossAsync`True değerini döndürürse Service Fabric tüm diğer çoğaltmaları bu birinciyle yeniden oluşturacak. Service Fabric, `OnDataLossAsync` birincil role ilk geçiş çağrısı alacak ancak okuma durumu veya yazma durumu verilmeyecek olan çoğaltmaların olmasını sağlar.
Bu, StatefulService uygulayıcıları için `RunAsync` başarıyla bitene kadar çağrılmayacak anlamına gelir `OnDataLossAsync` .
Ardından, `OnDataLossAsync` Yeni birincil üzerinde çağrılacaktır.
Bir hizmet bu API 'YI başarılı bir şekilde tamamlayana kadar (doğru veya yanlış döndürerek) ve ilgili yeniden yapılandırma işlemini tamamladıktan sonra, API her seferinde bir kez çağrılacaktır.

`RestoreAsync` İlk olarak, çağrıldığı birincil çoğaltmadaki tüm mevcut durumu bırakır. Ardından güvenilir durum Yöneticisi, yedekleme klasöründe bulunan tüm güvenilir nesneleri oluşturur. Ardından, güvenilir nesneler 'in, yedekleme klasöründeki denetim noktalarından geri yüklenmesi istendi. Son olarak, güvenilir durum Yöneticisi yedekleme klasöründeki günlük kayıtlarından kendi durumunu kurtarır ve kurtarma gerçekleştirir. Kurtarma işleminin bir parçası olarak, yedekleme klasöründe kaydedilmiş günlük kayıtları olan "başlangıç noktasından" başlatılan işlemler güvenilir nesnelere yeniden yürütülür. Bu adım kurtarılan durumun tutarlı olmasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar
  - [Güvenilir Koleksiyonlar](service-fabric-work-with-reliable-collections.md)
  - [Hızlı başlangıç Reliable Services](service-fabric-reliable-services-quick-start.md)
  - [Reliable Services bildirimleri](service-fabric-reliable-services-notifications.md)
  - [Reliable Services yapılandırması](service-fabric-reliable-services-configuration.md)
  - [Güvenilir koleksiyonlar için geliştirici başvurusu](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  - [Azure Service Fabric’te düzenli yedekleme ve geri yükleme](service-fabric-backuprestoreservice-quickstart-azurecluster.md)
