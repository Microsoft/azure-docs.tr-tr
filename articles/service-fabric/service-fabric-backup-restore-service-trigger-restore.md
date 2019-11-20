---
title: Azure Service Fabric yedeklemeyi geri yükleme | Microsoft Docs
description: Uygulama verilerinizin bir yedeklemesinden verileri geri yüklemek için Service Fabric içindeki düzenli yedekleme ve geri yükleme özelliğini kullanın.
services: service-fabric
documentationcenter: .net
author: aagup
manager: chackdan
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: ff705eabde111b5ebac1e2d714e3ece221c36e90
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819313"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Azure Service Fabric yedeklemeyi geri yükleme

Azure Service Fabric 'de, güvenilir durum bilgisi olan güvenilir hizmetler ve Reliable Actors bir istek ve yanıt işlemi tamamlandıktan sonra kesilebilir, yetkili bir durum tutabilir. Durum bilgisi olan bir hizmet uzun bir süre geçebilir veya bir olağanüstü durum nedeniyle bilgi kaybı verebilir. Bu durumda, hizmetin çalışmaya devam edebilmesi için kabul edilebilir en son yedekten geri yüklenmesi gerekir.

Örneğin, aşağıdaki senaryolara karşı korunmak üzere bir hizmeti, verilerini yedekleyecek şekilde yapılandırabilirsiniz:

- **Olağanüstü durum kurtarma durumu**: Service Fabric kümesinin tamamının kalıcı kaybolması.
- **Veri kaybı durumu**: bir hizmet bölümünün çoğaltmalarının çoğunluğunun kalıcı kaybedilmesi.
- **Veri kaybı durumu**: yanlışlıkla silme veya hizmetin bozulması. Örneğin, bir yönetici hizmeti yanlışlıkla siler.
- **Veri bozulması durumunda**: hizmette hatalar veri bozulmasına neden olur. Örneğin, bir hizmet kodu yükseltmesi hatalı verileri güvenilir bir koleksiyona yazdığında veri bozulması meydana gelebilir. Böyle bir durumda, hem kodu hem de verileri önceki bir duruma geri yüklemeniz gerekebilir.

## <a name="prerequisites"></a>Ön koşullar

- Bir geri yüklemeyi tetiklemek için, küme için _hata analiz hizmeti 'nin (Fas)_ etkinleştirilmesi gerekir.
- Yedekleme _geri yükleme hizmeti (BRS)_ yedeklemeyi oluşturdu.
- Geri yükleme yalnızca bir bölümde tetiklenebilir.
- Yapılandırma çağrıları yapmak için Microsoft. ServiceFabric. PowerShell. http modülünü [önizlemede] yüklersiniz.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Microsoft. ServiceFabric. PowerShell. http modülünü kullanarak herhangi bir yapılandırma isteği yapmadan önce kümenin `Connect-SFCluster` komutunu kullanarak bağlandığından emin olun.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Geri yükleme tetiklendi

Aşağıdaki senaryolardan herhangi biri için bir geri yükleme tetiklenebilir:

- _Olağanüstü durum kurtarma_için veri geri yükleme.
- Veri _bozulması/veri kaybı_için veri geri yükleme.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Olağanüstü durum kurtarma durumunda veri geri yükleme

Service Fabric kümesinin tamamı kaybedilmişse, güvenilir durum bilgisi olan hizmet ve Reliable Actors bölümlerinin verilerini kurtarabilirsiniz. [Yedekleme depolama ayrıntıları Ile Getbackupapı](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation)kullandığınızda, istenen yedekleme listeden seçilebilir. Yedekleme numaralandırması bir uygulama, hizmet veya bölüm için olabilir.

Aşağıdaki örnek için kayıp kümenin, [güvenilir durum bilgisi olan ve Reliable Actors düzenli aralıklarla yedeklemeyi etkinleştirme](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)bölümünde başvurulan kümeyle aynı olduğunu varsayalım. Bu durumda, `SampleApp` yedekleme ilkesi etkinken dağıtılır ve yedeklemeler Azure Storage olarak yapılandırılır.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

`SampleApp` uygulamasının içindeki tüm bölümler için oluşturulan yedeklemelerin listesini döndürmek üzere REST API kullanmak için bir PowerShell betiği yürütün. API, kullanılabilir yedeklemeleri listelemek için yedekleme depolama bilgileri gerektirir.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Yukarıdaki çalışma için örnek çıktı:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Geri yüklemeyi tetiklemek için Yedeklerden birini seçin. Örneğin, olağanüstü durum kurtarma için geçerli yedekleme aşağıdaki yedekleme olabilir:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Restore API 'SI için _BackupID_ ve _BackupLocation_ ayrıntılarını sağlamanız gerekir.

Ayrıca, [bölüm düzeninde](service-fabric-concepts-partitioning.md#get-started-with-partitioning)açıklandığı şekilde alternatif kümede bir hedef bölüm seçmeniz gerekir. Alternatif küme yedeklemesi, özgün kayıp kümeden bölüm düzeninde belirtilen bölüme geri yüklendi.

Alternatif kümedeki bölüm KIMLIĞI `1c42c47f-439e-4e09-98b9-88b8f60800c6`ise, _Sıralaştırılmış bölümlendirme (UniformInt64Partition)_ için yüksek anahtar ve düşük anahtarı karşılaştırarak özgün küme bölüm kimliği `974bd92a-b395-4631-8a7f-53bd4ae9cf22` eşleyebilirsiniz.

_Adlandırılmış bölümlendirme_için ad değeri, alternatif kümede hedef bölümü tanımlamak üzere karşılaştırılır.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

Aşağıdaki [restore API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)'sini kullanarak yedekleme kümesi bölümüne karşı geri yüklemeyi isteyebilirsiniz:

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Geri yüklemenin ilerlemesini TrackRestoreProgress ile izleyebilirsiniz.

### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer kullanma
Service Fabric Explorer bir geri yükleme tetikleyebilirsiniz. Service Fabric Explorer ayarlarında gelişmiş modun etkinleştirildiğinden emin olun.
1. İstediğiniz bölümleri seçin ve Eylemler ' e tıklayın. 
2. Bölüm geri yüklemeyi tetikleme ve Azure için bilgi doldur ' u seçin:

    ![Bölüm geri yüklemeyi Tetikle][2]

    veya FileShare:

    ![Bölüm geri yükleme dosya yüklemesini tetikleme][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Veri _kaybı_/veri _bozulması_ için veri geri yükleme

_Veri kaybı_ veya _veri bozulması_Için, güvenilir durum bilgisi olan hizmet ve Reliable Actors bölümlerinin yedeklenen bölümleri seçilen yedeklemelerin hiçbirine geri yüklenebilir.

Aşağıdaki örnek, [güvenilir durum bilgisi olan hizmet ve Reliable Actors için düzenli yedeklemeyi etkinleştirmenin bir devamıdır](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Bu örnekte, bölüm için bir yedekleme ilkesi etkinleştirilir ve hizmet Azure Storage 'da istenen bir sıklıkta yedeklemeler yapıyor.

[Getbackupapı](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups)çıktısından bir yedekleme seçin. Bu senaryoda, yedekleme daha önceki ile aynı kümeden oluşturulur.

Geri yüklemeyi tetiklemek için listeden bir yedekleme seçin. _Veri bozulması_/geçerli _veri kaybı_ için aşağıdaki yedeklemeyi seçin:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Restore API 'SI için _BackupID_ ve _BackupLocation_ ayrıntılarını sağlayın. Küme, Service Fabric _yedekleme geri yükleme hizmeti 'nin (BRS)_ ilişkili yedekleme ilkesinden doğru depolama konumunu tanımladığı şekilde yedekleme 'yi etkinleştirdi.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Geri yükleme ilerlemesini TrackRestoreProgress kullanarak izleyebilirsiniz.

## <a name="track-restore-progress"></a>Geri yükleme ilerlemesini izleme

Güvenilir bir durum bilgisi olan hizmetin veya güvenilir aktörün bir bölümü aynı anda yalnızca bir geri yükleme isteğini kabul eder. Bir bölüm, geçerli geri yükleme isteği tamamlandıktan sonra yalnızca başka bir isteği kabul eder. Birden fazla geri yükleme isteği aynı anda farklı bölümlerde tetiklenebilir.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Geri yükleme isteği aşağıdaki sırada ilerler:

1. **Kabul edildi**: _kabul edilen_ geri yükleme durumu, istenen bölümün doğru istek parametreleriyle tetiklendiğini gösterir.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **Sürüyor**: bir _sürüyor_ geri yükleme durumu, bölümde belirtilen yedeklemenin bulunduğu bölümde geri yükleme gerçekleştiğini gösterir. Bölüm, _DataLoss_ durumunu raporlar.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Başarı**, **hata**veya **zaman aşımı**: istenen geri yükleme aşağıdaki durumların hiçbirinde tamamlanabilir. Her durum aşağıdaki anlam ve yanıt ayrıntılarına sahiptir:
    - **Başarılı**: _başarılı_ geri yükleme durumu, bir bölüm durumunun durumunu gösterir. Bölüm, UTC 'deki zamanla birlikte _RestoredEpoch_ ve _restoredlsn_ durumlarını raporlar.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Hata**: _hata_ geri yükleme durumu geri yükleme isteğinin başarısızlığını gösterir. Hatanın nedeni raporlanır.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Zaman aşımı**: _zaman_ aşımı geri yükleme durumu, isteğin zaman aşımına uğradığını gösterir. Daha fazla [Restoretimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout)ile yeni bir geri yükleme isteği oluşturun. Varsayılan zaman aşımı 10 dakikadır. Yeniden geri yükleme istenmeden önce bölümün bir veri kaybı durumunda olmadığından emin olun.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Otomatik geri yükleme

_Otomatik geri yükleme_için Service Fabric kümesinde güvenilir durum bilgisi olan hizmet ve Reliable Actors bölümleri yapılandırabilirsiniz. Yedekleme ilkesi kümesinde `AutoRestore` _true_olarak ayarlayın. _Otomatik geri yüklemeyi_ etkinleştirmek, veri kaybı bildirildiğinde en son bölüm yedeklemesinden verileri otomatik olarak geri yükler. Daha fazla bilgi için bkz.

- [Yedekleme Ilkesinde etkinleştirme otomatik geri yükleme](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Restobölümlemek API başvurusu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [GetPartitionRestoreProgress API başvurusu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Sonraki adımlar
- [Düzenli yedekleme yapılandırmasını anlama](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Yedekleme geri yükleme REST API başvurusu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png