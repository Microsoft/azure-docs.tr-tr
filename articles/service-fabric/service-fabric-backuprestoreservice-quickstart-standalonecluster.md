---
title: Tek başına Azure Service Fabric düzenli yedekleme/geri yükleme
description: Uygulama verilerinizin düzenli veri yedeklemesini etkinleştirmek için Service Fabric düzenli yedekleme ve geri yükleme özelliğini kullanın.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: 559f6cce7044d57c0a17fc3fe17f1c61f710913a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013325"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Azure Service Fabric düzenli aralıklarla yedekleme ve geri yükleme
> [!div class="op_single_selector"]
> * [Azure 'da kümeler](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Tek başına kümeler](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric, güvenilir, dağıtılmış ve mikro hizmet tabanlı bulut uygulamaları geliştirmeyi ve yönetmeyi kolaylaştıran bir dağıtılmış sistemler platformudur. Hem durum bilgisi olmayan hem de durum bilgisi olan mikro hizmetlerin çalıştırılmasına izin verir. Durum bilgisi olan hizmetler, istek ve yanıtın ötesinde kesilebilir, yetkili durumunu veya tamamlanmış bir işlemi koruyabilir. Durum bilgisi olan bir hizmet uzun bir süre aşağı gittiğinde veya bir olağanüstü durum nedeniyle bilgileri kaybederse, bir kez daha başlatıldıktan sonra hizmet sağlamaya devam edebilmek için, durumunun son yedeklenmesinden geri yüklenmesi gerekebilir.

Service Fabric, hizmetin yüksek oranda kullanılabilir olduğundan emin olmak için durumu birden çok düğüm arasında çoğaltır. Kümedeki bir düğüm başarısız olsa bile, hizmet kullanılabilir olmaya devam eder. Ancak, bazı durumlarda hizmet verilerinin daha geniş hatalara karşı güvenilir olması tercih edilir.
 
Örneğin, bir hizmet aşağıdaki senaryolardan korunmak için verilerini yedeklemek isteyebilir:
- Service Fabric kümesinin tamamının kalıcı kaybedilmesi.
- Bir hizmet bölümünün çoğaltmalarının çoğunluğunun kalıcı kaybolması
- Durumun yanlışlıkla silindiği veya bozulduğu yönetim hataları. Örneğin, yeterli ayrıcalığa sahip bir yönetici yanlışlıkla hizmeti siler.
- Hizmette veri bozulmasına neden olan hatalar. Örneğin, bu durum bir hizmet kodu yükseltmesinin hatalı verileri güvenilir bir koleksiyona yazmaya başladığında meydana gelebilir. Böyle bir durumda, hem kod hem de verilerin önceki bir duruma döndürülmesi gerekebilir.
- Çevrimdışı veri işleme. Veri üreten hizmetten ayrı olarak gerçekleşen iş zekası için verilerin çevrimdışı işlenmesini sağlamak uygun olabilir.

Service Fabric, zaman [yedekleme ve geri yükleme](service-fabric-reliable-services-backup-restore.md)için oluşturulmuş bir API sağlar. Uygulama geliştiricileri bu API 'Leri, hizmetin durumunu düzenli aralıklarla yedeklemek için kullanabilir. Ayrıca, hizmet yöneticileri, uygulamayı yükseltmeden önce olduğu gibi, bir yedekleme özelliğini hizmetin dışından belirli bir zamanda tetiklemeyi tercih ediyorsanız, geliştiricilerin, hizmeti bir API olarak yedeklemeyi (ve geri yüklemeyi) kullanıma sunması gerekir. Yedeklemelerin saklanması bunun üzerinde ek bir maliyettir. Örneğin, her yarı saatte beş artımlı yedekleme yapmak ve ardından tam yedekleme yapmak isteyebilirsiniz. Tam yedeklemeden sonra, önceki artımlı yedeklemeleri silebilirsiniz. Bu yaklaşım, uygulama geliştirme sırasında ek kod lideri olmasını gerektirir.

Uygulama verilerinin düzenli olarak yedeklenmesi, dağıtılmış bir uygulamayı yönetmeye ve verilerin kaybedilmesine veya hizmet kullanılabilirliğinin kullanılamamasına karşı korunması için temel bir gereksinimdir. Service Fabric, ek kod yazmak zorunda kalmadan durum bilgisi olan Reliable Services (aktör hizmetleri dahil) düzenli olarak yedeklenmesini yapılandırmanıza olanak tanıyan, isteğe bağlı bir yedekleme ve geri yükleme hizmeti sağlar. Ayrıca, daha önce alınan yedeklemelerin geri yüklenmesini de kolaylaştırır. 

Service Fabric, düzenli yedekleme ve geri yükleme özelliğiyle ilgili aşağıdaki işlevlere ulaşmak için bir API kümesi sağlar:

- Güvenilir durum bilgisi olan ve Reliable Actors yedekleme 'yi (harici) depolama konumlarına yükleme desteğiyle düzenli olarak durum bilgisi olan ve düzenli olarak yedekleyin. Desteklenen depolama konumları
    - Azure Storage
    - Dosya paylaşma (Şirket içi)
- Yedeklemeleri listeleme
- Bir bölümün geçici yedeklemesini tetikleyin
- Önceki yedeklemeyi kullanarak bir bölümü geri yükleme
- Yedeklemeleri geçici olarak askıya al
- Yedeklemelerin bekletme yönetimi (yakında)

## <a name="prerequisites"></a>Önkoşullar
* Yapı sürümü 6,4 veya üzeri bir küme Service Fabric. Gerekli paketi indirme adımları için bu [makaleye](service-fabric-cluster-creation-for-windows-server.md) bakın.
* Yedeklemeleri depolamak üzere depolamaya bağlanmak için gereken gizli dizileri şifrelemek için X. 509.440 sertifikası. Otomatik olarak imzalanan bir X. 509.440 sertifikası edinmeyi veya oluşturmayı öğrenmek için [makaleye](service-fabric-windows-cluster-x509-security.md) bakın.

* Service Fabric SDK 3,0 veya üzeri sürümleri kullanılarak oluşturulmuş güvenilir durum bilgisi olan uygulamayı Service Fabric. .Net Core 2,0 ' i hedefleyen uygulamalar için, uygulama Service Fabric SDK sürümü 3,1 veya üzeri kullanılarak oluşturulmalıdır.
* Yapılandırma çağrıları yapmak için Microsoft. ServiceFabric. PowerShell. http modülünü [önizlemede] yüklersiniz.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Microsoft. ServiceFabric. PowerShell. http modülünü kullanarak herhangi bir yapılandırma isteği yapmadan önce kümenin `Connect-SFCluster` komutunu kullanarak bağlandığından emin olun.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Yedekleme ve geri yükleme hizmeti etkinleştiriliyor
İlk olarak, kümenizde _yedekleme ve geri yükleme hizmetini_ etkinleştirmeniz gerekir. Dağıtmak istediğiniz kümenin şablonunu alın. [Örnek şablonları](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)kullanabilirsiniz. _Yedekleme ve geri yükleme hizmetini_ aşağıdaki adımlarla etkinleştirin:

1. `apiversion` küme yapılandırma dosyasında `10-2017` olarak ayarlandığından emin olun ve yoksa, aşağıdaki kod parçacığında gösterildiği gibi güncelleştirin:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Şimdi aşağıdaki kod parçacığında gösterildiği gibi `properties` bölümü altına aşağıdaki `addonFeatures` bölümünü ekleyerek _yedekleme ve geri yükleme hizmetini_ etkinleştirin: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Kimlik bilgilerinin şifrelenmesi için X. 509.440 sertifikasını yapılandırın. Bu, varsa, depolama alanına bağlanmak için girilen kimlik bilgilerinin kalıcı hale geldiğinden emin olmak için önemlidir. Aşağıdaki kod parçacığında gösterildiği gibi `fabricSettings` bölümü altına aşağıdaki `BackupRestoreService` bölümünü ekleyerek şifreleme sertifikasını yapılandırın: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Küme yapılandırma dosyanızı önceki değişikliklerle güncelleştirdikten sonra, bunları uygulayın ve dağıtımın/yükseltmenin tamamlanmasını sağlayın. Tamamlandıktan sonra _yedekleme ve geri yükleme hizmeti_ kümenizde çalışmaya başlar. Bu hizmetin URI 'Si `fabric:/System/BackupRestoreService` ve hizmet, Service Fabric Gezgini 'ndeki sistem hizmeti bölümünde bulunabilir. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Güvenilir durum bilgisi olan hizmet ve Reliable Actors için düzenli yedeklemeyi etkinleştirme
Güvenilir durum bilgisi olan hizmet ve Reliable Actors için düzenli yedeklemeyi etkinleştirme adımlarını inceleyelim. Bu adımlarda varsayılmaktadır
- Küme, _yedekleme ve geri yükleme hizmeti_ile ayarlanır.
- Küme üzerinde güvenilir bir durum bilgisi olan hizmet dağıtılır. Bu hızlı başlangıç kılavuzunun amacı doğrultusunda, uygulama URI 'si `fabric:/SampleApp` ve bu uygulamaya ait güvenilir durum bilgisi olan hizmet URI 'Si `fabric:/SampleApp/MyStatefulService`. Bu hizmet tek bölüm ile dağıtılır ve bölüm KIMLIĞI `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Yedekleme İlkesi Oluştur

İlk adım Yedekleme zamanlamasını açıklayan yedekleme ilkesi, yedekleme verileri için hedef depolama, ilke adı, yedekleme depolaması için tam yedekleme ve bekletme ilkesi tetiklemeden önce izin verilen maksimum artımlı yedeklemeler oluşturmaktır. 

Yedekleme depolaması için, dosya paylaşma oluşturun ve tüm Service Fabric düğümü makineler için bu dosya paylaşımında ReadWrite erişimi verin. Bu örnek, `BackupStore` adlı paylaşımın `StorageServer`üzerinde bulunduğunu varsayar.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

Yeni ilke oluşturmak için gerekli REST API çağırmak üzere aşağıdaki PowerShell betiğini yürütün.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Düzenli yedeklemeyi etkinleştir
Uygulamanın veri koruma gereksinimlerini karşılamak için ilkeyi tanımladıktan sonra, yedekleme ilkesinin uygulamayla ilişkilendirilmesi gerekir. Gereksinime bağlı olarak, yedekleme ilkesi bir uygulama, hizmet veya bir bölümle ilişkilendirilebilir.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı
Yedekleme ilkesini Yukarıdaki adımda oluşturulan `BackupPolicy1` adıyla ilişkilendirmek için gerekli REST API çağırmak için aşağıdaki PowerShell betiğini yürütün uygulama `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer kullanma

1. Bir uygulama seçin ve eyleme geçin. Uygulama yedeklemesini etkinleştir/Güncelleştir ' e tıklayın.

    ![Uygulama yedeklemesini etkinleştir][3] 

2. Son olarak, istenen ilkeyi seçin ve yedeklemeyi etkinleştir ' e tıklayın.

    ![Ilke seçin][4]

### <a name="verify-that-periodic-backups-are-working"></a>Düzenli yedeklemelerin çalıştığını doğrulama

Uygulama için yedeklemeyi etkinleştirdikten sonra, güvenilir durum bilgisi olan hizmetlere ve uygulama altındaki Reliable Actors ait tüm bölümler, ilişkili yedekleme ilkesine göre düzenli aralıklarla yedeklenmek üzere başlatılır.

![Bölüm BackedUp sistem durumu olayı][0]

### <a name="list-backups"></a>Yedeklemeleri Listele

Güvenilir durum bilgisi olan hizmetlere ve uygulamanın Reliable Actors ait tüm bölümlerle ilişkili yedeklemeler, _Getbackups_ API 'si kullanılarak listelenebilir. Gereksinime bağlı olarak, yedeklemeler uygulama, hizmet veya bir bölüm için Numaralandırılabilir.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

`SampleApp` uygulamasının içindeki tüm bölümler için oluşturulan yedeklemeleri numaralandırmak üzere HTTP API 'sini çağırmak için aşağıdaki PowerShell betiğini yürütün.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Yukarıdaki çalışma için örnek çıktı:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer kullanma

Service Fabric Explorer yedeklemeleri görüntülemek için bir bölüme gidin ve yedeklemeler sekmesini seçin.

![Yedeklemeleri listeleme][5]

## <a name="limitation-caveats"></a>Sınırlama/uyarılar
- Service Fabric PowerShell cmdlet 'leri önizleme modundadır.
- Linux üzerinde Service Fabric kümesi desteği yoktur.

## <a name="next-steps"></a>Sonraki adımlar
- [Düzenli yedekleme yapılandırmasını anlama](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Yedekleme geri yükleme REST API başvurusu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png