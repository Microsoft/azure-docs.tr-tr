---
title: Azure Service Fabric’te düzenli yedekleme ve geri yükleme
description: Uygulama verilerinizin düzenli veri yedeklemesini etkinleştirmek için Service Fabric düzenli yedekleme ve geri yükleme özelliğini kullanın.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: 42097b50277e78b3f0e8f5e61a2bf70cc08dbc02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103198722"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Azure Service Fabric kümesinde düzenli olarak yedekleme ve geri yükleme
> [!div class="op_single_selector"]
> * [Azure’da kümeler](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Tek başına kümeler](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric, güvenilir, dağıtılmış ve mikro hizmet tabanlı bulut uygulamaları geliştirmeyi ve yönetmeyi kolaylaştıran bir dağıtılmış sistemler platformudur. Hem durum bilgisi olmayan hem de durum bilgisi olan mikro hizmetlerin çalıştırılmasına izin verir. Durum bilgisi olan hizmetler, istek ve yanıtın ötesinde kesilebilir, yetkili durumunu veya tamamlanmış bir işlemi koruyabilir. Durum bilgisi olan bir hizmet uzun bir süre aşağı gittiğinde veya bir olağanüstü durum nedeniyle bilgileri kaybederse, bir kez daha başlatıldıktan sonra hizmet sağlamaya devam edebilmek için, durumunun son yedeklenmesinden geri yüklenmesi gerekebilir.

Service Fabric, hizmetin yüksek oranda kullanılabilir olduğundan emin olmak için durumu birden çok düğüm arasında çoğaltır. Kümedeki bir düğüm başarısız olsa bile, hizmet kullanılabilir olmaya devam eder. Ancak, bazı durumlarda hizmet verilerinin daha geniş hatalara karşı güvenilir olması tercih edilir.
 
Örneğin, hizmet aşağıdaki senaryolardan korunmak için verilerini yedeklemek isteyebilir:
- Service Fabric kümesinin tamamının kalıcı kaybolması durumunda.
- Bir hizmet bölümünün çoğaltmalarının çoğunluğunun kalıcı kaybolması
- Durumun yanlışlıkla silindiği veya bozulduğu yönetim hataları. Örneğin, yeterli ayrıcalığa sahip bir yönetici yanlışlıkla hizmeti siler.
- Hizmette veri bozulmasına neden olan hatalar. Örneğin, bu durum bir hizmet kodu yükseltmesinin hatalı verileri güvenilir bir koleksiyona yazmaya başladığında meydana gelebilir. Böyle bir durumda, hem kod hem de verilerin önceki bir duruma döndürülmesi gerekebilir.
- Çevrimdışı veri işleme. Veri üreten hizmetten ayrı olarak gerçekleşen iş zekası için verilerin çevrimdışı işlenmesini sağlamak uygun olabilir.

Service Fabric, zaman [yedekleme ve geri yükleme](service-fabric-reliable-services-backup-restore.md)için oluşturulmuş bir API sağlar. Uygulama geliştiricileri bu API 'Leri, hizmetin durumunu düzenli aralıklarla yedeklemek için kullanabilir. Ayrıca, hizmet yöneticileri, uygulamayı yükseltmeden önce olduğu gibi, bir yedekleme özelliğini hizmetin dışından belirli bir zamanda tetiklemeyi tercih ediyorsanız, geliştiricilerin, hizmeti bir API olarak yedeklemeyi (ve geri yüklemeyi) kullanıma sunması gerekir. Yedeklemelerin saklanması bunun üzerinde ek bir maliyettir. Örneğin, her yarı saatte beş artımlı yedekleme yapmak ve ardından tam yedekleme yapmak isteyebilirsiniz. Tam yedeklemeden sonra, önceki artımlı yedeklemeleri silebilirsiniz. Bu yaklaşım, uygulama geliştirme sırasında ek kod lideri olmasını gerektirir.

Service Fabric 'de yedekleme ve geri yükleme hizmeti, durum bilgisi olan hizmetlerde depolanan bilgilerin kolay ve otomatik yedeklemesini mümkün bir şekilde sunar. Uygulama verilerini düzenli aralıklarla yedeklemek, veri kaybına ve hizmetin kullanılamamasına karşı koruma için temel bir uygulamadır. Service Fabric, ek kod yazmak zorunda kalmadan durum bilgisi olan Reliable Services (aktör hizmetleri dahil) düzenli olarak yedeklenmesini yapılandırmanıza olanak tanıyan, isteğe bağlı bir yedekleme ve geri yükleme hizmeti sağlar. Ayrıca, daha önce alınan yedeklemelerin geri yüklenmesini de kolaylaştırır. 


Service Fabric, düzenli yedekleme ve geri yükleme özelliğiyle ilgili aşağıdaki işlevlere ulaşmak için bir API kümesi sağlar:

- Güvenilir durum bilgisi olan ve Reliable Actors yedekleme 'yi (harici) depolama konumlarına yükleme desteğiyle düzenli olarak durum bilgisi olan ve düzenli olarak yedekleyin. Desteklenen depolama konumları
    - Azure Depolama
    - Dosya paylaşma (Şirket içi)
- Yedeklemeleri listeleme
- Bir bölümün geçici yedeklemesini tetikleyin
- Önceki yedeklemeyi kullanarak bir bölümü geri yükleme
- Yedeklemeleri geçici olarak askıya al
- Yedeklemelerin bekletme yönetimi (yakında)

## <a name="prerequisites"></a>Önkoşullar
* Yapı sürümü 6,4 veya üzeri bir küme Service Fabric. Azure kaynak şablonu kullanarak Service Fabric kümesi oluşturma adımları için bu [makaleye](service-fabric-cluster-creation-via-arm.md) başvurun.
* Yedeklemeleri depolamak üzere depolamaya bağlanmak için gereken gizli dizileri şifrelemek için X. 509.440 sertifikası. X. 509.952 sertifikası alma veya oluşturma hakkında bilgi edinmek için [makaleye](service-fabric-cluster-creation-via-arm.md) bakın.
* Service Fabric SDK 3,0 veya üzeri sürümleri kullanılarak oluşturulmuş güvenilir durum bilgisi olan uygulamayı Service Fabric. .NET Core 2,0 'yi hedefleyen uygulamalar için, uygulama Service Fabric SDK sürümü 3,1 veya üzeri kullanılarak oluşturulmalıdır.
* Uygulama yedeklemelerini depolamak için Azure depolama hesabı oluşturun.
* Yapılandırma çağrıları yapmak için Microsoft. ServiceFabric. PowerShell. http modülünü (Önizleme) yükler.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> PowerShellGet sürümünüz 1.6.0 'den küçükse, *-allowbir ön* sürüm bayrağı için destek eklemek üzere ' yi güncelleştirmeniz gerekir:
>
> `Install-Module -Name PowerShellGet -Force`

* `Connect-SFCluster`Microsoft. ServiceFabric. PowerShell. http modülünü kullanarak herhangi bir yapılandırma isteği yapmadan önce, kümenin komutunu kullanarak bağlı olduğundan emin olun.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Yedekleme ve geri yükleme hizmeti etkinleştiriliyor

### <a name="using-azure-portal"></a>Azure portalını kullanma

`Include backup restore service`Sekmesinin altındaki onay kutusunu `+ Show optional settings` etkinleştirin `Cluster Configuration` .

![Portal Ile yedekleme geri yükleme hizmetini etkinleştir][1]


### <a name="using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma
İlk olarak, kümenizde _yedekleme ve geri yükleme hizmetini_ etkinleştirmeniz gerekir. Dağıtmak istediğiniz kümenin şablonunu alın. [Örnek şablonları](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) kullanabilir ya da bir kaynak yöneticisi şablonu oluşturabilirsiniz. _Yedekleme ve geri yükleme hizmetini_ aşağıdaki adımlarla etkinleştirin:

1. `apiversion`Kaynak için olarak ayarlanmış olup olmadığını denetleyin **`2018-02-01`** ve yoksa `Microsoft.ServiceFabric/clusters` , aşağıdaki kod parçacığında gösterildiği gibi güncelleştirin:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Şimdi  `addonFeatures` `properties` Aşağıdaki kod parçacığında gösterildiği gibi bölümüne aşağıdaki bölümü ekleyerek yedekleme ve geri yükleme hizmetini etkinleştirin: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Kimlik bilgilerinin şifrelenmesi için X. 509.440 sertifikasını yapılandırın. Bu, depolamaya bağlanmak için girilen kimlik bilgilerinin kalıcı olmadan önce şifrelenmesini sağlamak açısından önemlidir. Aşağıdaki `BackupRestoreService` `fabricSettings` kod parçacığında gösterildiği gibi bölümüne aşağıdaki bölümü ekleyerek şifreleme sertifikasını yapılandırın: 

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

4. Küme şablonunuzu önceki değişikliklerle güncelleştirdikten sonra, dağıtım/yükseltme tamamlanana izin verin. Tamamlandıktan sonra _yedekleme ve geri yükleme hizmeti_ kümenizde çalışmaya başlar. Bu hizmetin URI 'Si `fabric:/System/BackupRestoreService` ve hizmet, Service Fabric Explorer 'ın sistem hizmeti bölümünde bulunabilir. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Güvenilir durum bilgisi olan hizmet ve Reliable Actors için düzenli yedeklemeyi etkinleştirme
Güvenilir durum bilgisi olan hizmet ve Reliable Actors için düzenli yedeklemeyi etkinleştirme adımlarını inceleyelim. Bu adımlarda varsayılmaktadır
- Küme, _yedekleme ve geri yükleme hizmeti_ ile X. 509.440 Security kullanılarak ayarlanır.
- Küme üzerinde güvenilir bir durum bilgisi olan hizmet dağıtılır. Bu hızlı başlangıç kılavuzunun amacı için uygulama URI 'si `fabric:/SampleApp` ve bu uygulamaya ait güvenilir durum bilgisi olan hizmet URI 'si `fabric:/SampleApp/MyStatefulService` . Bu hizmet tek bölüm ile dağıtılır ve bölüm KIMLIĞI olur `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .
- Yönetici rolüne sahip istemci sertifikası, aşağıdaki betiklerin çağrıldığı makinede bulunan _CurrentUser_ sertifika depolama konumunun (_Kişisel_) depolama _adı ' na_ yüklenir. Bu örnek `1b7ebe2174649c45474a4819dafae956712c31d3` , bu sertifikanın parmak izi olarak kullanır. İstemci sertifikaları hakkında daha fazla bilgi için bkz. [Service Fabric istemcileri Için rol tabanlı erişim denetimi](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Yedekleme İlkesi Oluştur

İlk adım Yedekleme zamanlamasını açıklayan yedekleme ilkesi, yedekleme verileri için hedef depolama, ilke adı, yedekleme depolaması için tam yedekleme ve bekletme ilkesi tetiklemeden önce izin verilen maksimum artımlı yedeklemeler oluşturmaktır. 

Yedekleme depolaması için yukarıda oluşturulan Azure Storage hesabını kullanın. Kapsayıcı `backup-container` yedeklemeleri depolamak için yapılandırılmış. Bu ada sahip bir kapsayıcı, zaten mevcut değilse, yedekleme karşıya yüklemesi sırasında oluşturulur. `ConnectionString`Azure depolama hesabı için geçerli bir bağlantı dizesiyle doldurun, `account-name` depolama hesabınızın adıyla ve `account-key` depolama hesabı anahtarınızla değiştirin.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

Yeni yedekleme ilkesi oluşturmak için aşağıdaki PowerShell cmdlet 'lerini yürütün. `account-name`Depolama hesabı adınızla ve `account-key` depolama hesabı anahtarınızla değiştirin.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

Yeni ilke oluşturmak için gerekli REST API çağırmak üzere aşağıdaki PowerShell betiğini yürütün. `account-name`Depolama hesabı adınızla ve `account-key` depolama hesabı anahtarınızla değiştirin.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
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
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer kullanma

1. Service Fabric Explorer, yedeklemeler sekmesine gidin ve yedekleme Ilkesi oluştur > Eylemler ' i seçin.

    ![Yedekleme Ilkesi oluştur][6]

2. Bilgileri doldurun. Azure kümeleri için AzureBlobStore seçilmelidir.

    ![Yedekleme Ilkesi oluşturma Azure Blob depolama][7]

### <a name="enable-periodic-backup"></a>Düzenli yedeklemeyi etkinleştir
Uygulamanın veri koruma gereksinimlerini karşılamak için yedekleme ilkesi tanımladıktan sonra, yedekleme ilkesinin uygulamayla ilişkilendirilmesi gerekir. Gereksinime bağlı olarak, yedekleme ilkesi bir uygulama, hizmet veya bir bölümle ilişkilendirilebilir.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

Yedekleme ilkesini `BackupPolicy1` uygulama ile yukarıdaki adımda oluşturulan adla ilişkilendirmek için gerekli REST API çağırmak üzere aşağıdaki PowerShell betiğini yürütün `SampleApp` .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer kullanma
Service Fabric Explorer için [Gelişmiş modun](service-fabric-visualizing-your-cluster.md#backup-and-restore) etkinleştirildiğinden emin olun 

1. Bir uygulama seçin ve eyleme geçin. Uygulama yedeklemesini etkinleştir/Güncelleştir ' e tıklayın.

    ![Uygulama yedeklemesini etkinleştir][3]

2. Son olarak, istenen ilkeyi seçin ve yedeklemeyi etkinleştir ' e tıklayın.

    ![Ilke seçin][4]


### <a name="verify-that-periodic-backups-are-working"></a>Düzenli yedeklemelerin çalıştığını doğrulama

Uygulama düzeyinde yedeklemeyi etkinleştirdikten sonra, güvenilir durum bilgisi olan hizmetlere ve uygulama altındaki Reliable Actors sahip olan tüm bölümler, ilişkili yedekleme ilkesine göre düzenli aralıklarla yedeklenmek üzere başlatılır. 

![Bölüm BackedUp sistem durumu olayı][0]

### <a name="list-backups"></a>Yedeklemeleri Listele

Güvenilir durum bilgisi olan hizmetlere ve uygulamanın Reliable Actors ait tüm bölümlerle ilişkili yedeklemeler, _Getbackups_ API 'si kullanılarak listelenebilir. Yedeklemeler, bir uygulama, hizmet veya bir bölüm için Numaralandırılabilir.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft. ServiceFabric. PowerShell. http modülünü kullanan PowerShell

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>PowerShell kullanarak Rest çağrısı

Uygulamanın içindeki tüm bölümler için oluşturulan yedeklemeleri numaralandırmak üzere HTTP API 'sini çağırmak için aşağıdaki PowerShell betiğini yürütün `SampleApp` .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer kullanma

Service Fabric Explorer yedeklemeleri görüntülemek için bir bölüme gidin ve yedeklemeler sekmesini seçin.

![Yedeklemeleri listeleme][5]

## <a name="limitation-caveats"></a>Sınırlama/uyarılar
- Service Fabric PowerShell cmdlet 'leri önizleme modundadır.
- Linux üzerinde Service Fabric kümesi desteği yoktur.

## <a name="next-steps"></a>Sonraki adımlar
- [Düzenli aralıklarla yedekleme yapılandırmasını anlama](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Yedekleme geri yükleme REST API başvurusu](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png
