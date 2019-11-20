---
title: Azure Service Fabric düzenli aralıklarla yedekleme yapılandırmasını anlama | Microsoft Docs
description: Uygulama verilerinizin düzenli veri yedeklemesini etkinleştirmek için Service Fabric düzenli yedekleme ve geri yükleme özelliğini kullanın.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAA45B4A-0258-4CB3-A825-7E8F70F28401
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: hrushib
ms.openlocfilehash: 47faeff22db4e4a2b3630104c9b492b43e29fd7b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819275"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Azure Service Fabric düzenli aralıklarla yedekleme yapılandırmasını anlama

Güvenilir durum bilgisi olan hizmetlerin veya Reliable Actors düzenli olarak yedeklenmesini yapılandırma aşağıdaki adımlardan oluşur:

1. **Yedekleme Ilkelerinin oluşturulması**: Bu adımda, gereksinimlere bağlı olarak bir veya daha fazla yedekleme ilkesi oluşturulur.

2. **Yedeklemeyi etkinleştirme**: Bu adımda, **1. adımda** oluşturulan yedekleme Ilkelerini gerekli varlıklar, _uygulama_, _hizmet_veya bir _bölüme_ilişkilendirirsiniz.

## <a name="create-backup-policy"></a>Yedekleme Ilkesi oluştur

Bir yedekleme ilkesi aşağıdaki yapılandırmalardan oluşur:

* **Veri kaybına otomatik geri yükleme**: Bölüm bir veri kaybı olayı yaşıyorsa, geri yüklemenin en son kullanılabilir yedekleme kullanılarak otomatik olarak tetikleneceğini belirtir.

* **En yüksek artımlı yedeklemeler**: iki tam yedekleme arasında alınacak en yüksek artımlı yedekleme sayısını tanımlar. En yüksek artımlı yedeklemeler üst sınırı belirtir. Aşağıdaki koşullardan birinde, belirtilen sayıda artımlı yedekleme işlemi tamamlanmadan tam yedekleme yapılabilir

    1. Çoğaltma, birincil hale geldiği için hiçbir şekilde tam yedekleme gerçekleştirmiştir.

    2. Son yedeklemeden bu yana olan günlük kayıtlarının bazıları kesildi.

    3. Çoğaltma, MaxAccumulatedBackupLogSizeInMB sınırını geçti.

* **Yedekleme zamanlaması**: Düzenli yedeklemelerin alınacağı saat veya sıklık. Yedeklemeleri belirtilen aralıklarla veya günlük/haftalık sabit bir zamanda yinelenecek şekilde zamanlayabilirsiniz.

    1. **Sıklık tabanlı yedekleme zamanlaması**: Bu zamanlama türü, ihtiyalığın sabit aralıklarla veri yedeklemesi yapması durumunda kullanılmalıdır. Art arda iki yedekleme arasındaki istenen zaman aralığı ıSO8601 biçimi kullanılarak tanımlanır. Sıklık tabanlı yedekleme zamanlaması, dakikada zaman aralığını destekler.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Zaman tabanlı yedekleme zamanlaması**: Bu zamanlama türü, ihtiyaç duyuyorsa günün veya haftanın belirli saatlerinde veri yedeklemesi gerçekleştirmek için kullanılmalıdır. Zamanlama sıklığı türü her gün veya haftalık olabilir.
        1. **_Günlük_ saat tabanlı yedekleme zamanlaması**: Bu zamanlama türü, günün belirli saatlerinde veri yedeklemesi yapmak için kimlik gereksinimi olması durumunda kullanılmalıdır. Bunu belirtmek için `ScheduleFrequencyType` _her gün_olarak ayarlayın; `RunTimes`, ıSO8601 biçimindeki gün boyunca istenen saat listesine ayarlı, zaman içinde belirtilen tarih yok sayılır. Örneğin, `0001-01-01T18:00:00` Tarih bölüm _0001-01-01_' i yoksayarak günlük _6:00 PM_ 'yi temsil eder. Aşağıdaki örnekte günlük yedeklemenin _9:00_ saat ve _6:00 PM_ 'de tetiklenmesi için yapılandırma gösterilmektedir.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **_Haftalık_ zaman tabanlı yedekleme zamanlaması**: Bu zamanlama türü, günün belirli saatlerinde veri yedeklemesi yapmak için kimlik gereksinimi olması durumunda kullanılmalıdır. Bunu belirtmek için `ScheduleFrequencyType` _haftalık_olarak ayarlayın; yedeklemenin tetiklenmesi gereken gün listesine `RunDays` ayarlayın ve `RunTimes` biçiminde istenen süre listesine ayarlayın, zaman içinde belirtilen tarih yok sayılır. Düzenli yedeklemenin tetiklenmesi için haftanın gün listesi. Aşağıdaki örnekte, Pazartesi 'Den Cuma 'ya _9:00_ ve _6:00 PM_ itibariyle günlük yedeklemenin tetiklenmesi için yapılandırma gösterilmektedir.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Yedekleme depolaması**: yedeklemelerin yükleneceği konumu belirtir. Depolama alanı, Azure Blob Mağazası veya dosya paylaşımından olabilir.
    1. **Azure Blob Mağazası**: Bu depolama türü, Azure 'da oluşturulan yedeklemeleri depolamak için gerekli olduğunda seçilmelidir. Hem _tek başına_ hem de _Azure tabanlı_ kümeler, bu depolama türünü kullanabilir. Bu depolama türünün açıklaması, bağlantı dizesi ve yedeklemelerin karşıya yüklenmesi gereken kapsayıcının adını gerektirir. Belirtilen ada sahip kapsayıcı yoksa, bir yedeklemenin karşıya yüklenmesi sırasında oluşturulur.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Dosya paylaşma**: Bu depolama türü, şirket içi veri yedeklemesini depolamak gerektiğinde _tek başına_ kümeler için seçilmelidir. Bu depolama türünün açıklaması, yedeklemelerin yüklenmesi gereken dosya paylaşma yolunu gerektirir. Dosya paylaşımının erişimi, aşağıdaki seçeneklerden biri kullanılarak yapılandırılabilir
        1. Dosya paylaşımının erişiminin Service Fabric kümesine ait tüm bilgisayarlara sağlandığı _Tümleşik Windows kimlik doğrulaması_. Bu durumda, _dosya paylaşma_ tabanlı yedekleme depolamasını yapılandırmak için aşağıdaki alanları ayarlayın.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. Dosya paylaşımının erişimi belirli kullanıcılara sağlandığı _Kullanıcı adı ve parola kullanarak dosya paylaşımının korunması_. Dosya paylaşma depolama belirtimi, birincil Kullanıcı adı ve birincil parolayla kimlik doğrulaması başarısız olursa, geri dönüş kimlik bilgileri sağlamak için ikincil Kullanıcı adı ve ikincil parola belirtme yeteneği de sağlar. Bu durumda, _dosya paylaşma_ tabanlı yedekleme depolamasını yapılandırmak için aşağıdaki alanları ayarlayın.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Depolama güvenilirliğinin yedekleme verilerinin güvenilirlik gereksinimlerini karşıladığından veya bu gereksinimleri aştığından emin olun.
>

* **Bekletme ilkesi**: yapılandırılan depolamada yedeklemelerin tutulacağı ilkeyi belirtir. Yalnızca temel bekletme Ilkesi desteklenir.
    1. **Temel bekletme ilkesi**: Bu bekletme ilkesi, daha gerekli olmayan yedekleme dosyalarını kaldırarak en iyi depolama kullanımını sağlamaya olanak tanır. `RetentionDuration`, depolama alanında yedeklemelerin korunması için gereken zaman aralığını ayarlamak için belirtilebilir. `MinimumNumberOfBackups`, belirtilen sayıda yedeklemenin `RetentionDuration`ne olursa olsun her zaman korunduğundan emin olmak için belirtilecek isteğe bağlı bir parametredir. Aşağıdaki örnek, yedeklemeleri _10_ gün boyunca koruyacak yapılandırmayı gösterir ve yedekleme sayısının _20_' nin altına geçmesine izin vermez.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Düzenli yedeklemeyi etkinleştir
Veri yedekleme gereksinimlerini karşılamak için yedekleme ilkesi tanımladıktan sonra, yedekleme ilkesi bir _uygulama_veya _hizmet_ya da bir _bölümle_uygun şekilde ilişkilendirilmelidir.

### <a name="hierarchical-propagation-of-backup-policy"></a>Yedekleme ilkesinin hiyerarşik yayılımı
Service Fabric, uygulama, hizmet ve bölümler arasındaki ilişki, [uygulama modelinde](./service-fabric-application-model.md)açıklanacak şekilde hiyerarşiktir. Yedekleme ilkesi, hiyerarşideki bir _uygulama_, _hizmet_ya da bir _bölümle_ ilişkilendirilebilir. Yedekleme ilkesi hiyerarşik olarak bir sonraki düzeye yayar. Oluşturulan ve bir _uygulamayla_ilişkili yalnızca bir yedekleme ilkesi olduğu varsayılarak, güvenilir olmayan tüm _hizmetlere_ ve _uygulamanın_ _Reliable Actors_ ait tüm durum bilgisi olmayan bölümlerin, Yedekleme ilkesi. Ya da yedekleme ilkesi _güvenilir bir durum bilgisi olmayan hizmetle_ilişkiliyse, tüm bölümleri yedekleme ilkesi kullanılarak yedeklenir.

### <a name="overriding-backup-policy"></a>Yedekleme ilkesini geçersiz kılma
Aynı yedekleme zamanlaması ile veri yedeklemenin, uygulamanın daha yüksek sıklık zamanlaması kullanarak veya farklı bir depolama hesabına yedekleme yapması ya da farklı bir depolama hesabına yedekleme yapması gereken belirli hizmetler dışında, uygulamanın tüm hizmetleri için gerekli olduğu bir senaryo olabilir veya FileShare. Yedekleme geri yükleme hizmeti bu senaryolara yönelik olarak hizmet ve bölüm kapsamındaki yayılan ilkeyi geçersiz kılmak için tesis sağlar. Yedekleme ilkesi _hizmet_ veya _bölüm_ile ilişkilendirildiğinde, varsa yayılan yedekleme ilkesini geçersiz kılar.

### <a name="example"></a>Örnek

Bu örnek, _MyApp_A_ ve _MyApp_B_iki uygulamayla kurulum kullanır. Uygulama _MyApp_A_ , Iki güvenilir durum bilgisi içeren iki hizmet, _SvcA1_ & _SvcA3_ve bir güvenilir aktör hizmeti olan _ActorA2_içerir. _SvcA1_ üç bölüm Içerir, _ActorA2_ ve _SvcA3_ her biri iki bölüm içerir.  Uygulama _MyApp_B_ , güvenilir durum bilgisi olan üç hizmet, _SvcB1_, _SvcB2_ve _SvcB3_içerir. _SvcB1_ ve _SvcB2_ her biri, _SvcB3_ üç bölüm içerdiğinde iki bölüm içerir.

Bu uygulamaların veri yedekleme gereksinimlerinin şu şekilde olduğunu varsayın

1. MyApp_A
    1. Tüm _güvenilir durum bilgisi olan hizmetler_ ve uygulamaya ait _Reliable Actors_ tüm bölümler için günlük yedekleme verileri oluşturun. Yedekleme verilerini _BackupStore1_konumuna yükleyin.

    2. Hizmetlerden biri olan _SvcA3_, her saat için veri yedeklemesi gerektirir.

    3. Bölüm _SvcA1_P2_ veri boyutu beklenenden fazla ve yedekleme verileri _BackupStore2_farklı depolama konumunda depolanmalıdır.

2. MyApp_B
    1. _SvcB1_ hizmetinin tüm bölümleri Için her pazar günü 8:00:00 ' da bir veri yedeklemesi oluşturun. Yedekleme verilerini _BackupStore1_konumuna yükleyin.

    2. Bölüm _SvcB2_P1_için 8:00 günde her gün bir yedekleme oluşturun. Yedekleme verilerini _BackupStore1_konumuna yükleyin.

Bu veri yedekleme gereksinimlerini karşılamak için, BP_5 BP_1 yedekleme ilkeleri oluşturulur ve yedekleme aşağıdaki gibi etkinleştirilir.
1. MyApp_A
    1. Sıklık tabanlı yedekleme zamanlaması olan _BP_1_yedekleme ilkesi oluşturun, sıklık 24 saat olarak ayarlanır. ve _BackupStore1_depolama konumunu kullanacak şekilde yapılandırılmış yedekleme depolama alanı. [Uygulama yedekleme](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) API 'sini etkinleştir ' i kullanarak uygulama _MyApp_A_ için bu ilkeyi etkinleştirin. Bu eylem, _güvenilir durum bilgisi olan hizmetlerin_ tüm bölümleri ve uygulama _MyApp_A_ait _Reliable Actors_ için yedekleme ilkesi _BP_1_ kullanarak veri yedeklemesini sağlar.

    2. Sıklık 1 saat olarak ayarlandığı sıklık tabanlı yedekleme zamanlamasıyla _BP_2_yedekleme ilkesi oluşturun. ve _BackupStore1_depolama konumunu kullanacak şekilde yapılandırılmış yedekleme depolama alanı. [Hizmet yedekleme API 'Sini etkinleştir özelliğini](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) kullanarak Service _SvcA3_ için bu ilkeyi etkinleştirin. Bu eylem, otomatik olarak etkinleştirilen ilke _BP_1_ , bu bölümler için _BP_2_ yedekleme Ilkesi 'ni kullanarak Service _SvcA3_ 'in tüm bölümleri tarafından veri yedeklemeye _BP_2_ .

    3. Sıklık tabanlı yedekleme zamanlaması olan _BP_3_yedekleme ilkesi oluşturun, sıklık 24 saat olarak ayarlanır. ve _BackupStore2_depolama konumunu kullanacak şekilde yapılandırılmış yedekleme depolama alanı. Bölüm [yedeklemesini etkinleştir](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API 'yi kullanarak bölüm _SvcA1_P2_ için bu ilkeyi etkinleştirin. Bu eylem, bölüm _SvcA1_P2_için _BP_3_ açık olarak etkinleştirilen _BP_1_ yayılan ilkeyi geçersiz kılar.

2. MyApp_B
    1. Zamanlama sıklığı türünün haftalık olarak ayarlandığı, çalıştırma günleri pazar olarak ayarlandığı ve çalışma zamanlarının 8:00 olarak ayarlandığı zamana dayalı yedekleme zamanlamasıyla yedekleme ilkesi oluşturun _BP_4_. Depolama konumu _BackupStore1_kullanacak şekilde yapılandırılmış yedekleme depolama alanı. [Hizmet yedekleme API 'Sini etkinleştir özelliğini](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) kullanarak Service _SvcB1_ için bu ilkeyi etkinleştirin. Bu eylem, Service _SvcB1_'in tüm bölümleri için yedekleme ilkesi _BP_4_ kullanarak veri yedeklemeyi mümkün bir şekilde sunar.

    2. Zamanlama sıklığı türünün günlük olarak ayarlandığı ve çalışma zamanlarının 8:00 olarak ayarlandığı zamana dayalı yedekleme zamanlamasıyla _BP_5_yedekleme ilkesi oluşturun. Depolama konumu _BackupStore1_kullanacak şekilde yapılandırılmış yedekleme depolama alanı. Bölüm [yedeklemesini etkinleştir](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API 'yi kullanarak bölüm _SvcB2_P1_ için bu ilkeyi etkinleştirin. Bu eylem, bölüm _SvcB2_P1_için yedekleme ilkesi _BP_5_ kullanarak veri yedeklemeye izin vermez.

Aşağıdaki diyagramda açık olarak etkinleştirilen yedekleme ilkeleri ve yayılmış yedekleme ilkeleri gösterilmektedir.

![Service Fabric uygulama hiyerarşisi][0]

## <a name="disable-backup"></a>Yedeklemeyi devre dışı bırak
Yedekleme ilkeleri, verileri yedeklemeye gerek kalmadığında devre dışı bırakılabilir. Bir _uygulamada_ etkinleştirilen yedekleme Ilkesi yalnızca [uygulama yedekleme API 'Sini devre dışı bırak](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) kullanılarak aynı _uygulamada_ devre dışı bırakılabilir, bir _hizmette_ etkinleştirilen yedekleme ilkesi devre dışı kullanılarak aynı _hizmette_ devre dışı bırakılabilir [ Hizmet yedekleme](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup) API 'si ve bir _bölümde_ etkinleştirilen yedekleme Ilkesi, [bölüm yedeklemesi API 'sini devre dışı bırakma](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup) kullanılarak aynı _bölümde_ devre dışı bırakılabilir.

* Bir _uygulama_ için yedekleme ilkesini devre dışı bırakmak, yedekleme Ilkesinin güvenilir durum bilgisi olan hizmet bölümlerine veya güvenilir aktör bölümlerine yayılmasının sonucu olarak oluşan tüm düzenli veri yedeklemelerini durduruyor.

* Bir _hizmet_ için yedekleme ilkesini devre dışı bırakmak, bu yedekleme ilkesinin _hizmet_bölümlerine yayılmasının sonucu olarak oluşan tüm düzenli veri yedeklemelerini sonlandırır.

* _Bölüm_ için yedekleme ilkesini devre dışı bırakmak, bölümdeki yedekleme ilkesi nedeniyle tüm düzenli veri yedeklemesini sonlandırır.

* Bir varlık (uygulama/hizmet/bölüm) için yedeklemeyi devre dışı bırakırken, yapılandırılan depolama alanındaki tüm yedekleri silmek için `CleanBackup` _true_ olarak ayarlanabilir.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Askıya al & yedeklemeyi sürdürür
Belirli durumlar, verilerin düzenli olarak yedeklenme için geçici olarak askıya alma talebinde bulunabilir. Bu tür durumlarda, gereksinime bağlı olarak, yedekleme API 'sini bir _uygulama_, _hizmet_veya _bölümde_kullanabilirsiniz. Düzenli yedekleme askıya alma, uygulama hiyerarşisinin uygulandığı noktadan sonra alt ağacı üzerinde geçişlidir. 

* [Askıya alma, uygulama yedeklemesi](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) API 'sini kullanarak bir _uygulamaya_ uygulandığında, verilerin düzenli olarak yedeklenmesi için bu uygulamadaki tüm hizmetler ve bölümler askıya alınır.

* [Askıya alma hizmeti yedekleme](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) API 'sini kullanan bir _hizmette_ , askıya alma tamamlandığında, verilerin düzenli olarak yedeklenmesi için bu hizmetin altındaki tüm bölümler askıya alınır.

* [Askıya alma, Bölüm yedekleme](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) API 'sini kullanarak bir _bölüme_ uygulandığında, verilerin düzenli olarak yedeklenmesi için bu hizmetin altındaki bölümleri askıya alır.

Askıya alma gereksinimi bittikten sonra, ilgili yedekleme API 'SI kullanılarak düzenli veri yedeklemesi geri yüklenebilir. Düzenli yedekleme, askıya alındığı yerde aynı _uygulama_, _hizmet_veya _bölümde_ sürdürülmelidir.

* Bir _uygulamaya_askıya alma uygulanmışsa, [uygulama yedekleme](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) API 'sini sürdürür kullanılarak devam edilmelidir. 

* Askıya alma _hizmeti bir hizmette_uygulanmışsa, [hizmet yedekleme](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup) API 'si kullanılarak devam edilmelidir.

* Bir _bölüme_askıya alma uygulanmışsa, [bölüm yedekleme](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup) API 'si kullanılarak devam edilmelidir.

### <a name="difference-between-suspend-and-disable-backups"></a>Yedeklemeleri askıya al ve devre dışı bırak arasındaki fark
Belirli bir uygulama, hizmet veya bölüm için yedeklemeler artık gerekli olmadığında yedeklemeyi devre dışı bırak kullanılmalıdır. Bunlardan biri, temiz yedeklemeler parametresi ile birlikte devre dışı bırakma ve tüm mevcut yedeklemelerin de silindiği anlamına gelir. Ancak, yerel disk tam veya karşıya yükleme, bilinen ağ sorunu nedeniyle başarısız olduğunda yedeklemeleri geçici olarak kapatmak istediği senaryolarda askıya alma kullanılır. 

Disable Özelliği, yalnızca yedekleme için daha önce etkinleştirilen bir düzeyde çağrılabilir ancak askıya alma, doğrudan veya devralma/hiyerarşi aracılığıyla yedekleme için etkinleştirilmiş olan herhangi bir düzeyde uygulanabilir. Örneğin, yedekleme bir uygulama düzeyinde etkinleştirilmişse, birisi yalnızca uygulama düzeyinde devre dışı bırakmayı çağırabilir ancak uygulama, uygulamanın altındaki herhangi bir hizmette veya bölümde çağrılabilir. 

## <a name="auto-restore-on-data-loss"></a>Veri kaybına otomatik geri yükleme
Hizmet bölümü, beklenmeyen hatalardan dolayı verileri kaybedebilir. Örneğin, bir bölüm (birincil çoğaltma dahil) için üç çoğaltma için olan disk bozulur veya silinir.

Service Fabric, bölümün veri kaybına neden olduğunu algıladığında, bölüm üzerinde `OnDataLossAsync` arabirim yöntemini çağırır ve veri kaybını sağlamak için bölümün gerekli eylemi yerine gelmesini bekler. Bu durumda, bölümdeki etkin yedekleme ilkesinde `AutoRestoreOnDataLoss` bayrağı `true` olarak ayarlandıysa, geri yükleme Bu bölüm için kullanılabilir en son yedekleme kullanılarak otomatik olarak tetiklenir.

## <a name="get-backup-configuration"></a>Yedekleme yapılandırmasını al
Ayrı API 'Ler, bir _uygulama_, _hizmet_ve _bölüm_ kapsamında yedekleme yapılandırma bilgilerini almak için kullanılabilir hale getirilir. [Uygulama yedekleme yapılandırma bilgilerini alın](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [hizmet yedekleme yapılandırma bilgilerini alın](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)ve [bölüm yedekleme yapılandırma bilgilerini al](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) , bu API 'ler sırasıyla bu API 'lerdir. Genellikle, bu API 'Ler, yedekleme ilkesinin uygulandığı kapsam ve yedekleme askıya alma ayrıntıları için geçerli yedekleme ilkesini döndürür. Aşağıda, bu API 'lerin döndürülen sonuçları hakkında kısa bir açıklama verilmiştir.

- Uygulama yedekleme yapılandırma bilgisi: uygulamada uygulanan yedekleme ilkesinin ayrıntılarını ve uygulamaya ait hizmetlere ve bölümlere yönelik tüm ridden ilkelerini sağlar. Ayrıca, uygulama ve BT hizmetleri ve bölümleri için askıya alınma bilgilerini içerir.

- Hizmet yedekleme yapılandırma bilgisi: geçerli yedekleme ilkesinin, hizmette ve bu ilkenin uygulandığı kapsamın ve tüm ridden ilkelerinin bulunduğu kapsamdaki ayrıntıları sağlar. Ayrıca hizmet ve bölümleri için askıya alınma bilgilerini de içerir.

- Bölüm yedekleme yapılandırma bilgisi: Bölüm ve bu ilkenin uygulandığı kapsamdaki geçerli yedekleme ilkesinin ayrıntılarını sağlar. Ayrıca, bölümlerin askıya alınma bilgilerini de içerir.

## <a name="list-available-backups"></a>Kullanılabilir yedeklemeleri listeleme

Kullanılabilir yedeklemeler, yedekleme listesi API 'SI kullanılarak listelenebilir. API çağrısının sonucu, yedek depolamada bulunan ve geçerli yedekleme ilkesinde yapılandırılan tüm yedeklemelerle ilgili yedekleme bilgileri öğelerini içerir. Bu API 'nin farklı türevleri, bir uygulamaya, hizmete veya bölüme ait kullanılabilir yedeklemeleri listelemek için verilmiştir. Bu API 'Ler, tüm uygulanabilir bölümlerin _en son_ kullanılabilir yedeklemesini veya _Başlangıç tarihi_ ile _bitiş tarihine_göre yedeklemelerin filtrelenmesini destekler.

Bu API 'Ler Ayrıca sonuçların sayfalandırmayı destekler, _MaxResults_ parametresi sıfır olmayan pozitif tamsayı olarak AYARLANDıĞıNDA, API en yüksek _MaxResults_ yedekleme bilgileri öğelerini döndürür. Büyük bir durumda, _MaxResults_ değerinden daha fazla yedek bilgi öğesi bulunur, sonra bir devamlılık belirteci döndürülür. Geçerli devamlılık belirteci parametresi, sonraki sonuç kümesini almak için kullanılabilir. Geçerli devamlılık belirteci değeri, API 'nin bir sonraki çağrısına geçirildiğinde, API sonraki sonuç kümesini döndürür. Tüm kullanılabilir sonuçlar döndürüldüğünde yanıta hiçbir devamlılık belirteci eklenmez.

Desteklenen çeşitler hakkında kısa bilgiler aşağıda verilmiştir.

- [Uygulama yedekleme listesini al](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): belirtilen Service Fabric uygulamasına ait her bölüm için kullanılabilen yedeklemelerin listesini döndürür.

- [Hizmet yedekleme listesini al](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist): verilen Service Fabric hizmetine ait her bölüm için kullanılabilen yedeklemelerin listesini döndürür.
 
- [Bölüm yedekleme listesini al](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): belirtilen bölüm için kullanılabilen yedeklemelerin listesini döndürür.

## <a name="next-steps"></a>Sonraki adımlar
- [Yedekleme geri yükleme REST API başvurusu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
