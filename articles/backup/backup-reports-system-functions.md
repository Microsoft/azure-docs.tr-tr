---
title: Azure Izleyici günlüklerinde sistem işlevleri
description: Sistem işlevlerini kullanarak Azure Izleyici günlüklerine özel sorgular yazma
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: acb45e6ad0250a1f8d10377fdd509e40051f25b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564917"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Azure Izleyici günlüklerinde sistem işlevleri

Azure Backup, Log Analytics (LA) çalışma alanlarınızdaki varsayılan olarak bulunan sistem işlevleri veya çözüm işlevleri olarak adlandırılan bir işlevler kümesi sağlar.
 
Bu işlevler, LA 'daki [ham Azure Backup tablolarındaki](./backup-azure-reports-data-model.md) veriler üzerinde çalışır ve basit sorgular kullanarak yedeklemeyle ilgili tüm varlıklarınızın bilgilerini kolayca almanıza yardımcı olan biçimli verileri döndürür. Kullanıcılar, bu işlevler tarafından döndürülen verileri filtrelemek için parametreleri bu işlevlere geçirebilir. 

Aşağıdaki bölümde açıklandığı gibi çeşitli avantajlar sağladığı için, özel raporlar oluşturmak üzere LA çalışma alanlarında yedekleme verilerinizi sorgulamak için sistem işlevlerinin kullanılması önerilir.

## <a name="benefits-of-using-system-functions"></a>Sistem işlevlerini kullanmanın avantajları

* **Daha basit sorgular**: Using işlevleri, sorgularda gerekli olan birleştirme sayısını azaltmanıza yardımcı olur. Varsayılan olarak, işlevler sorgulanmakta olan varlıkla ilgili tüm bilgileri (yedekleme örneği, iş, kasa vb.) birleştiren ' düzleştirilmiş ' şemaları döndürür. Örneğin, yedekleme öğesi adına ve ilişkili kapsayıcısına göre başarılı yedekleme işlerinin bir listesini almanız gerekiyorsa, **_AzureBackup_getJobs ()** işlevine yönelik basit bir çağrı, her iş için bu bilgileri size verecektir. Öte yandan, ham tabloları doğrudan sorgulamak, [AddonAzureBackupJobs](./backup-azure-reports-data-model.md#addonazurebackupjobs) ve [Coreazurebackup](./backup-azure-reports-data-model.md#coreazurebackup) tabloları arasında birden fazla birleştirme gerçekleştirmenizi gerektirir.

* **Eski tanılama olayından daha yumuşak geçiş**: Sistem işlevlerini kullanmak [eski tanılama olayından](./backup-azure-diagnostic-events.md#legacy-event) (AzureDiagnostics modundaki AzureBackupReport) [kaynağa özgü olaylara](./backup-azure-diagnostic-events.md#diagnostics-events-available-for-azure-backup-users)sorunsuz bir şekilde geçiş yapmanıza yardımcı olur. Azure Backup tarafından sunulan tüm sistem işlevleri, işlevin verileri yalnızca kaynağa özgü tablolardan mi sorgulayıp, hem de eski tablodan kaynağa özgü tablolardan (kayıtları yinelenenleri kaldırma ile) sorgulayamayacağını seçmenizi sağlayan bir parametre belirtmenize olanak tanır.
    * Kaynağa özgü tablolara başarıyla geçiş yaptıysanız, eski tablonun işlev tarafından sorgulanmasını dışlamayı seçebilirsiniz.
    * Şu anda geçiş işlemi yapıyorsanız ve eski tablolarda analiz için ihtiyaç duyduğunuz bazı veriler varsa, eski tabloyu eklemeyi seçebilirsiniz. Geçiş tamamlandığında ve artık eski tablodan veriye ihtiyacınız kalmadığında, eski tabloyu hariç tutmak için Sorgularınızdaki işleve geçirilen parametrenin değerini güncelleştirmeniz yeterlidir.
    * Hala yalnızca eski tabloyu kullanıyorsanız, eski tabloyu aynı parametre aracılığıyla eklemeyi tercih ederseniz işlevler çalışmaya devam edecektir. Ancak, en erken [kaynağa özgü tablolara geçmeniz](./backup-azure-diagnostic-events.md#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace) önerilir.

* **Özel sorguların önemli olma olasılığını azaltır**: Azure Backup gelecekteki raporlama senaryolarına uyum sağlamak için temel alınan La tablolarının şemasına yönelik iyileştirmeler tanıyorsa, işlevlerin tanımı da şema değişikliklerini dikkate alacak şekilde güncelleştirilir. Bu nedenle, özel sorgular oluşturmak için sistem işlevleri kullanırsanız, tabloların temeldeki şemasında değişiklikler olsa bile, sorgularınız kesintiye uğramaz.

> [!NOTE]
> Sistem işlevleri Microsoft tarafından korunur ve tanımları kullanıcılar tarafından düzenlenemez. Düzenlenebilir işlevlere ihtiyacınız varsa, LA 'da [kaydedilmiş işlevler](../azure-monitor/logs/functions.md) oluşturabilirsiniz.

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Azure Backup tarafından sunulan sistem işlevleri türleri

* **Çekirdek işlevler**: Bunlar, yedekleme örnekleri, kasalar, Ilkeler, Işler ve faturalandırma varlıkları gibi anahtar Azure Backup varlıkların herhangi birini sorgulamanızı sağlayan işlevlerdir. Örneğin, **_AzureBackup_getBackupInstances** işlevi ortamınızda en son tamamlanan güne (UTC) göre mevcut olan tüm yedekleme örneklerinin bir listesini döndürür. Bu makalede, bu temel işlevlerin her biri için parametreler ve döndürülen şema aşağıda özetlenmiştir.

* **Eğilim işlevleri**: Bunlar, yedeklemelerle ilgili varlıklarınız için geçmiş kayıtlar döndüren işlevlerdir (örneğin, yedekleme örnekleri, faturalandırma grupları) ve bu varlıklarla ilgili önemli ölçümler (örneğin, sayı, depolama alanı) hakkındaki günlük, haftalık ve aylık eğilim bilgilerini almanızı sağlar. Bu bir eğilim işlevinin parametreleri ve döndürülen şeması, bu makalede aşağıda özetlenmiştir.

> [!NOTE]
> Şu anda sistem işlevleri, son tamamlanan güne (UTC) kadar veri döndürür. Geçerli kısmi güne ait veriler döndürülmedi. Bu nedenle, geçerli güne ait kayıtları almak istiyorsanız ham LA tabloları kullanmanız gerekir.


## <a name="list-of-system-functions"></a>Sistem işlevlerinin listesi

### <a name="core-functions"></a>Çekirdek Işlevler

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults ()

Bu işlev, Azure ortamınızda LA çalışma alanıyla ilişkili tüm kurtarma hizmetleri kasalarının listesini döndürür.

**Parametreler**

| **Parametre adı** | **Açıklama** | **Gerekli mi?** | **Örnek değer** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Bu parametreyi yalnızca, RangeStart 'dan RangeEnd 'e kadar olan kasada ilgili tüm kayıtları almanız gerekiyorsa RangeEnd parametresiyle birlikte kullanın. Varsayılan olarak, RangeStart ve RangeEnd değeri null olur ve bu, işlevin her kasa için yalnızca en son kaydı almasına sahip olur. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Bu parametreyi RangeStart parametresiyle birlikte kullanın ve bu parametreyi, RangeStart ile RangeEnd arasındaki zaman aralığında tüm kasaya ilişkin kayıtları almanız gerekiyorsa kullanın. Varsayılan olarak, RangeStart ve RangeEnd değeri null olur ve bu, işlevin her kasa için yalnızca en son kaydı almasına sahip olur. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir abonelik kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlev için parametre olarak bir dizi virgülle ayrılmış abonelik kimliklerinin belirtilmesi, yalnızca belirtilen aboneliklerde bulunan bu kasaları almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm abonelikler genelinde kayıtlar için arama yapar. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir bölge kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işleve parametre olarak virgülle ayrılmış bölgelerin bir listesini belirtmek, yalnızca belirtilen bölgelerde olan bu kasaları almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm bölgelerde kayıtlar için arama yapar. | N | "eastus, westus"|
| VaultList    |Bu parametreyi, belirli bir kasa kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlev için bir parametre olarak virgülle ayrılmış bir kasa adları listesi belirtilmesi, yalnızca belirtilen kasaların ilgili kayıtlarını almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlevi tüm kasaların tamamında kayıtlar için arama yapar. | N |"vault1, vault2, vault3"|
| VaultTypeList     | İşlevin çıkışını belirli bir kasa türüyle ilgili kayıtlarla filtrelemek için bu parametreyi kullanın. Şu anda desteklenen tek kasa türü, bu parametrenin varsayılan değeri olan "Microsoft. RecoveryServices/Vaults" dir | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Verilerin eski AzureDiagnostics tablosunda sorgulanıp sorgulanmayacağını belirlemek için bu parametreyi kullanın. Bu parametrenin değeri false ise, işlevi hem AzureDiagnostics tablosundan hem de kaynağa özgü tablolardan verileri sorgular. Bu parametrenin değeri true ise, işlevi yalnızca kaynağa özgü tablolardan verileri sorgular. True varsayılan değerdir. | N | true |

**Döndürülen alanlar**

| **Alan adı** | **Açıklama** |
| -------------- | --------------- |
| UniqueId | Kasanın benzersiz KIMLIĞINI belirten birincil anahtar |
| Id | Kasanın Azure Resource Manager (ARM) KIMLIĞI |
| Name | Kasanın adı |
| SubscriptionId | Kasanın bulunduğu aboneliğin KIMLIĞI |
| Konum | Kasanın bulunduğu konum |
| VaultStore_StorageReplicationType | Kasayla ilişkili depolama çoğaltma türü |
| Etiketler | Kasanın etiketleri |
| TimeGenerated | Kaydın zaman damgası |
| Tür |  Kasanın türü "Microsoft. RecoveryServices/kaults"|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies ()

Bu işlev, Azure ortamınızda kullanılmakta olan yedekleme ilkelerinin listesini, veri kaynağı türü, depolama çoğaltma türü gibi her ilke hakkında ayrıntılı bilgilerle birlikte döndürür.

**Parametreler**

| **Parametre adı** | **Açıklama** | **Gerekli mi?** | **Örnek değer** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Bu parametreyi RangeStart parametresiyle birlikte kullanın, yalnızca, ilişkili tüm kayıtları RangeStart 'dan RangeEnd 'e kadar sürede almanız gerekiyorsa. Varsayılan olarak, RangeStart ve RangeEnd değeri null olur ve bu, işlevin her ilke için yalnızca en son kaydı almasına izin getirir. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Bu parametreyi RangeStart parametresi ile birlikte kullanın ve bu parametreyi, RangeStart ile RangeEnd arasındaki zaman aralığında tüm ilkeyle ilgili kayıtları almanız gerekir. Varsayılan olarak, RangeStart ve RangeEnd değeri null olur ve bu, işlevin her ilke için yalnızca en son kaydı almasına izin getirir. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir abonelik kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlev için parametre olarak bir dizi virgülle ayrılmış abonelik kimliklerinin belirtilmesi, yalnızca belirtilen aboneliklerde bulunan ilkeleri almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm abonelikler genelinde kayıtlar için arama yapar. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir bölge kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işleve parametre olarak virgülle ayrılmış bölgelerin bir listesini belirtmek, yalnızca belirtilen bölgelerde olan ilkeleri almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm bölgelerde kayıtlar için arama yapar. | N | "eastus, westus"|
| VaultList    |Bu parametreyi, belirli bir kasa kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlev için bir parametre olarak virgülle ayrılmış bir kasa adları listesi belirtilmesi, yalnızca belirtilen kasaların ilgili ilkelerinin kayıtlarını almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlevi tüm kasaların içindeki ilkelerin kayıtlarını arayacak şekilde yapar. | N |"vault1, vault2, vault3"|
| VaultTypeList     | İşlevin çıkışını belirli bir kasa türüyle ilgili kayıtlarla filtrelemek için bu parametreyi kullanın. Şu anda desteklenen tek kasa türü, bu parametrenin varsayılan değeri olan "Microsoft. RecoveryServices/Vaults" dir. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Verilerin eski AzureDiagnostics tablosunda sorgulanıp sorgulanmayacağını belirlemek için bu parametreyi kullanın. Bu parametrenin değeri false ise, işlevi hem AzureDiagnostics tablosundan hem de kaynağa özgü tablolardan verileri sorgular. Bu parametrenin değeri true ise, işlevi yalnızca kaynağa özgü tablolardan verileri sorgular. True varsayılan değerdir. | N | true |
| BackupSolutionList | Bu parametreyi, Azure ortamınızda kullanılan belirli bir yedekleme çözümleri kümesi için işlevin çıkışını filtrelemek üzere kullanın. Örneğin, bu parametrenin değeri olarak "Azure sanal makine yedekleme, Azure VM yedeklemesi 'nde SQL", DPM "belirtirseniz, işlev yalnızca Azure sanal makine yedeklemesi, Azure VM yedeklemesi veya DPM 'den Azure 'a yedekleme kullanılarak yedeklenen öğelerle ilişkili kayıtları döndürür. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlevin yedekleme raporları tarafından desteklenen tüm yedekleme çözümleriyle ilgili kayıtları döndürmesini sağlar (desteklenen değerler "Azure sanal makine yedeklemesi", "Azure VM yedekleme 'de SQL", "Azure VM yedeklemesi 'nde SAP HANA", "Azure depolama (Azure dosyaları) yedeklemesi", "Azure Backup Aracısı", "DPM", "Azure Backup Sunucusu" veya bu değerlerden herhangi birinin virgülle ayrılmış birleşimi). | N | "Azure sanal makine yedeklemesi, Azure 'da SQL VM yedeklemesi, DPM, Azure Backup Aracısı" |

**Döndürülen alanlar**

| **Alan adı** | **Açıklama** |
| -------------- | --------------- |
| UniqueId | İlkenin benzersiz KIMLIĞINI belirten birincil anahtar |
| Id | İlkenin Azure Resource Manager (ARM) KIMLIĞI |
| Name | İlke adı |
| Yedekleme çözümü | İlkenin ilişkilendirildiği yedekleme çözümü. Örneğin, Azure VM yedeklemesi, Azure VM 'de SQL yedekleme ve benzeri. |
| TimeGenerated | Kaydın zaman damgası |
| Vaultuniqueıd | İlkeyle ilişkili kasayı ifade eden yabancı anahtar |
| Vaultresourceıd | İlkeyle ilişkili kasanın KIMLIĞI Azure Resource Manager (ARM) |
| VaultName | İlkeyle ilişkili kasasının adı |
| VaultTags | İlkeyle ilişkili kasasının etiketleri |
| VaultLocation | İlkeyle ilişkili kasasının konumu |
| Vaultsubscriptionıd | İlkeyle ilişkili kasanın abonelik KIMLIĞI |
| VaultStore_StorageReplicationType | İlkeyle ilişkili kasanın depolama çoğaltma türü |
| VaultType | Kasanın türü "Microsoft. RecoveryServices/kaults" |
| ExtendedProperties | İlkenin ek özellikleri |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs ()

Bu işlev, belirli bir zaman aralığında tetiklenen tüm yedekleme ve geri yükleme işlerinin listesini, her işle ilgili olarak iş durumu, iş süresi, aktarılan veri vb. gibi ayrıntılı bilgileri döndürür.

**Parametreler**

| **Parametre adı** | **Açıklama** | **Gerekli mi?** | **Örnek değer** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart 'dan RangeEnd 'e kadar geçen süre içinde başlatılan tüm işlerin listesini almak için bu parametreyi RangeEnd parametresiyle birlikte kullanın. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart 'dan RangeEnd 'e kadar geçen süre içinde başlatılan tüm işlerin listesini almak için RangeStart parametresiyle birlikte bu parametreyi kullanın. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir abonelik kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlev için parametre olarak bir dizi virgülle ayrılmış abonelik kimliklerinin belirtilmesi, yalnızca belirtilen aboneliklerde kasalarla ilişkili işleri almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm abonelikler genelinde kayıtlar için arama yapar. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir bölge kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işleve parametre olarak virgülle ayrılmış bölgelerin bir listesini belirtmek, yalnızca belirtilen bölgelerdeki kasalarla ilişkili işleri almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm bölgelerde kayıtlar için arama yapar. | N | "eastus, westus"|
| VaultList    | Bu parametreyi, belirli bir kasa kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlevin parametresi olarak virgülle ayrılmış bir kasa adları listesi belirtilmesi, yalnızca belirtilen kasaların bulunduğu işleri almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlevi tüm kasaların üzerinde işler için arama yapar. | N |"vault1, vault2, vault3"|
| VaultTypeList     | İşlevin çıkışını belirli bir kasa türüyle ilgili kayıtlarla filtrelemek için bu parametreyi kullanın. Şu anda desteklenen tek kasa türü, bu parametrenin varsayılan değeri olan "Microsoft. RecoveryServices/Vaults" dir. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Verilerin eski AzureDiagnostics tablosunda sorgulanıp sorgulanmayacağını belirlemek için bu parametreyi kullanın. Bu parametrenin değeri false ise, işlevi hem AzureDiagnostics tablosundan hem de kaynağa özgü tablolardan verileri sorgular. Bu parametrenin değeri true ise, işlevi yalnızca kaynağa özgü tablolardan verileri sorgular. True varsayılan değerdir. | N | true |
| BackupSolutionList | Bu parametreyi, Azure ortamınızda kullanılan belirli bir yedekleme çözümleri kümesi için işlevin çıkışını filtrelemek üzere kullanın. Örneğin, bu parametrenin değeri olarak "Azure sanal makine yedekleme, Azure VM yedeklemesi 'nde SQL", DPM "belirtirseniz, işlev yalnızca Azure sanal makine yedeklemesi, Azure VM yedeklemesi veya DPM 'den Azure 'a yedekleme kullanılarak yedeklenen öğelerle ilişkili kayıtları döndürür. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlevin yedekleme raporları tarafından desteklenen tüm yedekleme çözümleriyle ilgili kayıtları döndürmesini sağlar (desteklenen değerler "Azure sanal makine yedeklemesi", "Azure VM yedekleme 'de SQL", "Azure VM yedeklemesi 'nde SAP HANA", "Azure depolama (Azure dosyaları) yedeklemesi", "Azure Backup Aracısı", "DPM", "Azure Backup Sunucusu" veya bu değerlerden herhangi birinin virgülle ayrılmış birleşimi). | N | "Azure sanal makine yedeklemesi, Azure 'da SQL VM yedeklemesi, DPM, Azure Backup Aracısı" |
| JobOperationList | Belirli bir iş türü için işlevin çıkışını filtrelemek için bu parametreyi kullanın. Örneğin, yedekleme veya geri yükleme. Varsayılan olarak, bu parametrenin değeri "*" olur, bu da işlev yedekleme ve geri yükleme işleri için arama yapar. | N | Yedeklenmiş | 
| JobStatusList | İşlevin çıkışını belirli bir iş durumu için filtrelemek üzere bu parametreyi kullanın. Örneğin, tamamlandı, başarısız, vb. Varsayılan olarak, bu parametrenin değeri "*" olur ve bu, işlevin durum ne olursa olsun tüm işler için arama yapar. | N | "Başarısız, Completedwithuyarılar" |
| Jobfailuılist | Belirli bir hata kodu için işlevin çıkışını filtrelemek için bu parametreyi kullanın. Varsayılan olarak, bu parametrenin değeri "*" olur ve bu, hata kodu ne olursa olsun işlevin tüm işler için arama yapar. | N | Başarılı
| DatasourceSetName | İşlevin çıkışını belirli bir üst kaynağa filtrelemek için bu parametreyi kullanın. Örneğin, "Testvm" sanal makinesine ait olan Azure VM yedekleme örneklerine SQL döndürmek için bu parametrenin değeri olarak _Testvm_ ' yi belirtin. Varsayılan olarak, değer "*" olur ve bu, işlev tüm yedekleme örneklerinde kayıtlar için arama yapar. | N | Testvm |
| Backupınstancename | Belirli bir yedekleme örneğindeki işleri ada göre aramak için bu parametreyi kullanın. Varsayılan olarak, değer "*" olur ve bu, işlev tüm yedekleme örneklerinde kayıtlar için arama yapar. | N | Testvm |
| ExcludeLog | Bu parametreyi, günlük işlerinin işlev tarafından döndürülmesinden dışlamak için kullanın (Sorgu performansına yardımcı olur). Varsayılan olarak, bu parametrenin değeri true 'dur, bu da işlev günlük işlerini hariç tutar. | N | true


**Döndürülen alanlar**

| **Alan adı** | **Açıklama** |
| -------------- | --------------- |
| UniqueId | İşin benzersiz KIMLIĞINI belirten birincil anahtar |
| OperationCategory | Gerçekleştirilen işlemin kategorisi. Örneğin, yedekleme, geri yükleme |
| İşlem | Gerçekleştirilen işlemin ayrıntıları. Örneğin, günlük (günlük yedeklemesi için)|
| Durum | İşin durumu. Örneğin, tamamlanan, başarısız, Completedwithuyarılar |
| ErrorTitle | İşin hata kodu |
| StartTime | İşin başladığı tarih ve saat |
| DurationInSecs | Saniye cinsinden iş süresi |
| Datatransferredinmb | İş tarafından MB olarak aktarılan veriler |
| RestoreJobRPDateTime | Kurtarılan kurtarma noktasının oluşturulduğu tarih ve saat |
| RestoreJobRPLocation | Kurtarılan kurtarma noktasının depolandığı konum |
| Backupınstanceuniqueıd | İşle ilişkili yedekleme örneğine başvuran yabancı anahtar |
| Backupınstanceıd | İşle ilişkili yedekleme örneğinin KIMLIĞI Azure Resource Manager (ARM) |
| Backupınstancefriendlyname | İşle ilişkili yedekleme örneğinin adı |
| Datasourceresourceıd | İşle ilişkili temel alınan veri kaynağının KIMLIĞI Azure Resource Manager (ARM). Örneğin, VM 'nin Azure Resource Manager (ARM) KIMLIĞI |
| DatasourceFriendlyName | İşle ilişkili temel alınan veri kaynağının kolay adı |
| DatasourceType | İşle ilişkili veri kaynağının türü. Örneğin, "Microsoft. COMPUTE/virtualMachines" |
| BackupSolution | İşin ilişkilendirildiği yedekleme çözümü. Örneğin, Azure VM yedeklemesi, Azure VM 'de SQL yedekleme ve benzeri. |
| Datasourcesetresourceıd | Veri kaynağının üst kaynağının KIMLIĞI (varsa) Azure Resource Manager (ARM). Örneğin, Azure VM veri kaynağındaki bir SQL için, bu alan SQL veritabanının bulunduğu sanal makinenin Azure Resource Manager (ARM) KIMLIĞINI içerir |
| DatasourceSetType | Veri kaynağının üst kaynak kaynağının türü (uygun olduğunda). Örneğin, Azure VM veri kaynağı 'nda bir SAP HANA için bu alan, üst kaynak bir Azure VM olduğundan Microsoft. COMPUTE/virtualMachines olacaktır |
| Vaultresourceıd | İşle ilişkili kasanın KIMLIĞI Azure Resource Manager (ARM) |
| Vaultuniqueıd | İşle ilişkili kasayı ifade eden yabancı anahtar |
| VaultName | İşle ilişkili kasasının adı |
| VaultTags | İşle ilişkili kasasının etiketleri |
| Vaultsubscriptionıd | İşle ilişkili kasanın abonelik KIMLIĞI |
| VaultLocation | İşle ilişkili kasasının konumu |
| VaultStore_StorageReplicationType | İşle ilişkili kasanın depolama çoğaltma türü |
| VaultType | Kasanın türü "Microsoft. RecoveryServices/kaults" |
| TimeGenerated | Kaydın zaman damgası |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances ()

Bu işlev, kurtarma hizmetleri kasalarınızla ilişkili yedekleme örneklerinin listesini, bulut depolama alanı tüketimi, ilişkili ilke vb. gibi her bir yedekleme örneğiyle ilgili ayrıntılı bilgilerle birlikte döndürür.

**Parametreler**

| **Parametre adı** | **Açıklama** | **Gerekli mi?** | **Örnek değer** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Bu parametreyi yalnızca, RangeStart 'dan RangeEnd 'e kadar geçen süre içindeki tüm yedekleme örneğiyle ilgili kayıtları almanız gerekiyorsa, RangeEnd parametresiyle birlikte kullanın. Varsayılan olarak, RangeStart ve RangeEnd değeri null olur ve bu, işlevi her bir yedekleme örneği için yalnızca en son kaydı alacak şekilde yapar. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Bu parametreyi RangeStart parametresiyle birlikte kullanın, yalnızca RangeStart 'dan RangeEnd 'e kadar olan tüm yedekleme örneğiyle ilgili kayıtları almanız gerekiyorsa. Varsayılan olarak, RangeStart ve RangeEnd değeri null olur ve bu, işlevi her bir yedekleme örneği için yalnızca en son kaydı alacak şekilde yapar. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir abonelik kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlev için parametre olarak bir dizi virgülle ayrılmış abonelik kimliklerinin belirtilmesi, yalnızca belirtilen aboneliklerde bulunan yedekleme örneklerini almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm abonelikler genelinde kayıtlar için arama yapar. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir bölge kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işleve parametre olarak virgülle ayrılmış bölgelerin bir listesini belirtmek, yalnızca belirtilen bölgelerde olan yedekleme örneklerini almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm bölgelerde kayıtlar için arama yapar. | N | "eastus, westus"|
| VaultList    |Bu parametreyi, belirli bir kasa kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlevin parametresi olarak virgülle ayrılmış bir kasa adları listesi belirtilmesi, yalnızca belirtilen kasaların bulunduğu yedekleme örneklerinin kayıtlarını almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev, tüm kasaların içindeki yedekleme örneklerinin kayıtları için arama yapar. | N |"vault1, vault2, vault3"|
| VaultTypeList     | İşlevin çıkışını belirli bir kasa türüyle ilgili kayıtlarla filtrelemek için bu parametreyi kullanın. Şu anda desteklenen tek kasa türü, bu parametrenin varsayılan değeri olan "Microsoft. RecoveryServices/Vaults" dir. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Verilerin eski AzureDiagnostics tablosunda sorgulanıp sorgulanmayacağını belirlemek için bu parametreyi kullanın. Bu parametrenin değeri false ise, işlevi hem AzureDiagnostics tablosundan hem de kaynağa özgü tablolardan verileri sorgular. Bu parametrenin değeri true ise, işlevi yalnızca kaynağa özgü tablolardan verileri sorgular. True varsayılan değerdir. | N | true |
| BackupSolutionList | Bu parametreyi, Azure ortamınızda kullanılan belirli bir yedekleme çözümleri kümesi için işlevin çıkışını filtrelemek üzere kullanın. Örneğin, bu parametrenin değeri olarak "Azure sanal makine yedekleme, Azure VM yedeklemesi 'nde SQL", DPM "belirtirseniz, işlev yalnızca Azure sanal makine yedeklemesi, Azure VM yedeklemesi veya DPM 'den Azure 'a yedekleme kullanılarak yedeklenen öğelerle ilişkili kayıtları döndürür. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlevin yedekleme raporları tarafından desteklenen tüm yedekleme çözümleriyle ilgili kayıtları döndürmesini sağlar (desteklenen değerler "Azure sanal makine yedeklemesi", "Azure VM yedekleme 'de SQL", "Azure VM yedeklemesi 'nde SAP HANA", "Azure depolama (Azure dosyaları) yedeklemesi", "Azure Backup Aracısı", "DPM", "Azure Backup Sunucusu" veya bu değerlerden herhangi birinin virgülle ayrılmış birleşimi). | N | "Azure sanal makine yedeklemesi, Azure 'da SQL VM yedeklemesi, DPM, Azure Backup Aracısı" |
| Protectionınfolist | Yalnızca etkin bir şekilde korunan yedekleme örneklerinin dahil edilip edilmeyeceğini veya korumanın durdurulduğu örnekleri ve ilk yedeklemenin beklediği örnekleri dahil etmek için bu parametreyi kullanın. Desteklenen değerler "Protected", "Protectiondurdurulan", "ınitialbackuppending" veya bu değerlerden herhangi birinin virgülle ayrılmış bir birleşimidir. Varsayılan olarak, bu değer "*" değeridir ve bu durum, koruma ayrıntılarından bağımsız olarak tüm yedekleme örneklerinin işlev aramasını yapar. | N | Korunamadı |
| DatasourceSetName | İşlevin çıkışını belirli bir üst kaynağa filtrelemek için bu parametreyi kullanın. Örneğin, "Testvm" sanal makinesine ait olan Azure VM yedekleme örneklerine SQL döndürmek için bu parametrenin değeri olarak _Testvm_ ' yi belirtin. Varsayılan olarak, değer "*" olur ve bu, işlev tüm yedekleme örneklerinde kayıtlar için arama yapar. | N | Testvm |
| Backupınstancename | Belirli bir yedekleme örneğini ada göre aramak için bu parametreyi kullanın. Varsayılan olarak, değer "*" olur ve bu, işlev aramasını tüm yedekleme örnekleri için yapar. | N | Testvm |
| DisplayAllFields | İşlev tarafından döndürülen alanların yalnızca bir alt kümesini almak isteyip istemediğinizi seçmek için bu parametreyi kullanın. Bu parametrenin değeri false ise işlev, işlevin çıktısından depolama ve bekletme noktasıyla ilgili bilgileri ortadan kaldırır. Bu işlevi daha büyük bir sorguda ara adım olarak kullanıyorsanız ve analiz için gerekli olmayan sütunları ortadan kaldırarak sorgunun performansını iyileştirmeniz gerekiyorsa yararlıdır. Varsayılan olarak, bu parametrenin değeri true 'dur, bu da işlevin yedekleme örneğiyle ilgili tüm alanları döndürmesini sağlar. | N | true |

**Döndürülen alanlar**

| **Alan adı** | **Açıklama** |
| -------------- | --------------- |
| UniqueId | Yedekleme örneğinin benzersiz KIMLIĞINI belirten birincil anahtar |
| Id | Yedekleme örneğinin KIMLIĞI Azure Resource Manager (ARM) |
| FriendlyName | Yedekleme örneğinin kolay adı |
| Protectionınfo | Yedekleme örneğinin koruma ayarları hakkında bilgi. Örneğin, koruma yapılandırıldı, koruma durduruldu, ilk yedekleme bekliyor |
| LatestRecoveryPoint | Yedekleme örneğiyle ilişkili en son kurtarma noktasının tarihi ve saati |
| OldestRecoveryPoint | Yedekleme örneğiyle ilişkili en eski kurtarma noktasının tarihi ve saati |
| Sourcesizeınmbs | Yedekleme örneğinin MB cinsinden ön uç boyutu |
| VaultStore_StorageConsumptionInMBs | Kasa standart katmanındaki yedekleme örneği tarafından tüketilen toplam bulut depolaması |
| DataSourceFriendlyName | Yedekleme örneğine karşılık gelen veri kaynağının kolay adı |
| BackupSolution | Yedekleme örneğinin ilişkilendirildiği yedekleme çözümü. Örneğin, Azure VM yedeklemesi, Azure VM 'de SQL yedekleme ve benzeri. |
| DatasourceType | Yedekleme örneğine karşılık gelen veri kaynağının türü. Örneğin, "Microsoft. COMPUTE/virtualMachines" |
| Datasourceresourceıd | Yedekleme örneğine karşılık gelen temel alınan veri kaynağının KIMLIĞI Azure Resource Manager (ARM). Örneğin, VM 'nin Azure Resource Manager (ARM) KIMLIĞI |
| DatasourceSetFriendlyName | Veri kaynağının üst kaynağının kolay adı (uygun olduğunda). Örneğin, Azure VM veri kaynağındaki bir SQL için, bu alan SQL veritabanının mevcut olduğu VM 'nin adını içerir |
| Datasourcesetresourceıd | Veri kaynağının üst kaynağının KIMLIĞI (varsa) Azure Resource Manager (ARM). Örneğin, Azure VM veri kaynağındaki bir SQL için, bu alan SQL veritabanının bulunduğu sanal makinenin Azure Resource Manager (ARM) KIMLIĞINI içerir |
| DatasourceSetType | Veri kaynağının üst kaynak kaynağının türü (uygun olduğunda). Örneğin, Azure VM veri kaynağı 'nda bir SAP HANA için bu alan, üst kaynak bir Azure VM olduğundan Microsoft. COMPUTE/virtualMachines olacaktır |
| PolicyName | Yedekleme örneğiyle ilişkili ilkenin adı |
| Policyuniqueıd | Yedekleme örneğiyle ilişkili ilkeye başvuran yabancı anahtar  |
| PolicyId | Yedekleme örneğiyle ilişkili ilkenin KIMLIĞI Azure Resource Manager (ARM) |
| Vaultresourceıd | Yedekleme örneğiyle ilişkili kasanın KIMLIĞI Azure Resource Manager (ARM) |
| Vaultuniqueıd | Yedekleme örneğiyle ilişkili kasayı ifade eden yabancı anahtar |
| VaultName | Yedekleme örneğiyle ilişkili kasasının adı |
| VaultTags | Yedekleme örneğiyle ilişkili kasasının etiketleri |
| Vaultsubscriptionıd | Yedekleme örneğiyle ilişkili kasanın abonelik KIMLIĞI |
| VaultLocation | Yedekleme örneğiyle ilişkili kasasının konumu |
| VaultStore_StorageReplicationType | Yedekleme örneğiyle ilişkili kasanın depolama çoğaltma türü |
| VaultType | Kasanın türü "Microsoft. RecoveryServices/kaults" |
| TimeGenerated | Kaydın zaman damgası |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups ()

Bu işlev, ön uç boyutu ve toplam bulut depolaması gibi önemli faturalandırma bileşenleriyle birlikte, yedeklemeyle ilgili tüm faturalandırma varlıklarının (Faturalandırma grupları) listesini döndürür.

**Parametreler**

| **Parametre adı** | **Açıklama** | **Gerekli mi?** | **Örnek değer** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Bu parametreyi yalnızca, RangeStart 'dan RangeEnd 'e kadar olan tüm faturalandırma grubu ile ilgili kayıtları almanız gerekiyorsa RangeEnd parametresiyle birlikte kullanın. Varsayılan olarak, RangeStart ve RangeEnd değeri null olur ve bu, işlevi her faturalandırma grubu için yalnızca en son kaydı alacak şekilde yapar. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Bu parametreyi RangeStart parametresiyle birlikte kullanın, yalnızca RangeStart 'dan RangeEnd 'e kadar olan tüm faturalandırma grubu ile ilgili kayıtları bir süre içinde getirin. Varsayılan olarak, RangeStart ve RangeEnd değeri null olur ve bu, işlevi her faturalandırma grubu için yalnızca en son kaydı alacak şekilde yapar. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir abonelik kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlev için parametre olarak bir dizi virgülle ayrılmış abonelik kimliklerinin belirtilmesi, yalnızca belirtilen aboneliklerde bulunan Faturalandırma gruplarını almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm abonelikler genelinde kayıtlar için arama yapar. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir bölge kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işleve parametre olarak virgülle ayrılmış bölgelerin bir listesini belirtmek, yalnızca belirtilen bölgelerde olan faturalandırma gruplarını almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm bölgelerde kayıtlar için arama yapar. | N | "eastus, westus"|
| VaultList    |Bu parametreyi, belirli bir kasa kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlevin parametresi olarak virgülle ayrılmış bir kasa adları listesi belirtilmesi, yalnızca belirtilen kasaların bulunduğu yedekleme örneklerinin kayıtlarını almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlevi tüm kasaların içindeki fatura gruplarının kayıtlarını arayacak şekilde yapar. | N |"vault1, vault2, vault3"|
| VaultTypeList     | İşlevin çıkışını belirli bir kasa türüyle ilgili kayıtlarla filtrelemek için bu parametreyi kullanın. Şu anda desteklenen tek kasa türü, bu parametrenin varsayılan değeri olan "Microsoft. RecoveryServices/Vaults" dir. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Verilerin eski AzureDiagnostics tablosunda sorgulanıp sorgulanmayacağını belirlemek için bu parametreyi kullanın. Bu parametrenin değeri false ise, işlevi hem AzureDiagnostics tablosundan hem de kaynağa özgü tablolardan verileri sorgular. Bu parametrenin değeri true ise, işlevi yalnızca kaynağa özgü tablolardan verileri sorgular. True varsayılan değerdir. | N | true |
| BackupSolutionList | Bu parametreyi, Azure ortamınızda kullanılan belirli bir yedekleme çözümleri kümesi için işlevin çıkışını filtrelemek üzere kullanın. Örneğin, bu parametrenin değeri olarak "Azure sanal makine yedekleme, Azure VM yedeklemesi 'nde SQL", DPM "belirtirseniz, işlev yalnızca Azure sanal makine yedeklemesi, Azure VM yedeklemesi veya DPM 'den Azure 'a yedekleme kullanılarak yedeklenen öğelerle ilişkili kayıtları döndürür. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlevin yedekleme raporları tarafından desteklenen tüm yedekleme çözümleriyle ilgili kayıtları döndürmesini sağlar (desteklenen değerler "Azure sanal makine yedeklemesi", "Azure VM yedekleme 'de SQL", "Azure VM yedeklemesi 'nde SAP HANA", "Azure depolama (Azure dosyaları) yedeklemesi", "Azure Backup Aracısı", "DPM", "Azure Backup Sunucusu" veya bu değerlerden herhangi birinin virgülle ayrılmış birleşimi). | N | "Azure sanal makine yedeklemesi, Azure 'da SQL VM yedeklemesi, DPM, Azure Backup Aracısı" |
| BillingGroupName | Belirli bir faturalandırma grubunu ada göre aramak için bu parametreyi kullanın. Varsayılan olarak, değer "*" olur ve bu, işlev tüm faturalandırma grupları için arama yapar. | N | Testvm |

**Döndürülen alanlar**

| **Alan adı** | **Açıklama** |
| -------------- | --------------- |
| UniqueId | Fatura grubunun benzersiz KIMLIĞINI belirten birincil anahtar |
| FriendlyName | Faturalandırma grubunun kolay adı |
| Name | Faturalandırma grubunun adı |
| Tür | Faturalandırma grubu türü. Örneğin, ProtectedContainer veya Backupıtem |
| Sourcesizeınmbs | Fatura grubunun MB cinsinden ön uç boyutu |
| VaultStore_StorageConsumptionInMBs | Kasa standart katmanındaki faturalandırma grubu tarafından tüketilen toplam bulut depolaması |
| BackupSolution | Faturalama grubunun ilişkilendirildiği yedekleme çözümü. Örneğin, Azure VM yedeklemesi, Azure VM 'de SQL yedekleme ve benzeri. |
| Vaultresourceıd | Faturalandırma grubuyla ilişkili kasanın KIMLIĞI Azure Resource Manager (ARM) |
| Vaultuniqueıd | Faturalandırma grubuyla ilişkili kasayı ifade eden yabancı anahtar |
| VaultName | Faturalandırma grubuyla ilişkili kasasının adı |
| VaultTags | Fatura grubuyla ilişkili kasasının etiketleri |
| Vaultsubscriptionıd | Faturalandırma grubuyla ilişkili kasanın abonelik KIMLIĞI |
| VaultLocation | Faturalandırma grubuyla ilişkili kasasının konumu |
| VaultStore_StorageReplicationType | Faturalandırma grubuyla ilişkili kasanın depolama çoğaltma türü |
| VaultType | Kasanın türü "Microsoft. RecoveryServices/kaults" |
| TimeGenerated | Kaydın zaman damgası |
| ExtendedProperties | Faturalandırma grubunun ek özellikleri |

### <a name="trend-functions"></a>Eğilim Işlevleri

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends ()

Bu işlev, her bir yedekleme örneği için geçmiş kayıtlar döndürür ve bu, yedek örnek sayısı ve depolama tüketimiyle ilgili günlük, haftalık ve aylık eğilimleri birden çok ayrıntı düzeyi düzeyinde görüntülemenize olanak sağlar.

**Parametreler**

| **Parametre adı** | **Açıklama** | **Gerekli mi?** | **Örnek değer** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart 'dan RangeEnd 'e kadar olan tüm yedekleme örneği ilişkili kayıtlarını almak için bu parametreyi RangeEnd parametresiyle birlikte kullanın. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart 'dan RangeEnd 'e kadar olan tüm yedekleme örneği ile ilgili kayıtları almak için RangeStart parametresiyle birlikte bu parametreyi kullanın. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir abonelik kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlev için parametre olarak bir dizi virgülle ayrılmış abonelik kimliklerinin belirtilmesi, yalnızca belirtilen aboneliklerde bulunan yedekleme örneklerini almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm abonelikler genelinde kayıtlar için arama yapar. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir bölge kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işleve parametre olarak virgülle ayrılmış bölgelerin bir listesini belirtmek, yalnızca belirtilen bölgelerde olan yedekleme örneklerini almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm bölgelerde kayıtlar için arama yapar. | N | "eastus, westus"|
| VaultList    |Bu parametreyi, belirli bir kasa kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlevin parametresi olarak virgülle ayrılmış bir kasa adları listesi belirtilmesi, yalnızca belirtilen kasaların bulunduğu yedekleme örneklerinin kayıtlarını almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev, tüm kasaların içindeki yedekleme örneklerinin kayıtları için arama yapar. | N |"vault1, vault2, vault3"|
| VaultTypeList     | İşlevin çıkışını belirli bir kasa türüyle ilgili kayıtlarla filtrelemek için bu parametreyi kullanın. Şu anda desteklenen tek kasa türü, bu parametrenin varsayılan değeri olan "Microsoft. RecoveryServices/Vaults" dir. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Verilerin eski AzureDiagnostics tablosunda sorgulanıp sorgulanmayacağını belirlemek için bu parametreyi kullanın. Bu parametrenin değeri false ise, işlevi hem AzureDiagnostics tablosundan hem de kaynağa özgü tablolardan verileri sorgular. Bu parametrenin değeri true ise, işlevi yalnızca kaynağa özgü tablolardan verileri sorgular. True varsayılan değerdir. | N | true |
| BackupSolutionList | Bu parametreyi, Azure ortamınızda kullanılan belirli bir yedekleme çözümleri kümesi için işlevin çıkışını filtrelemek üzere kullanın. Örneğin, bu parametrenin değeri olarak "Azure sanal makine yedekleme, Azure VM yedeklemesi 'nde SQL", DPM "belirtirseniz, işlev yalnızca Azure sanal makine yedeklemesi, Azure VM yedeklemesi veya DPM 'den Azure 'a yedekleme kullanılarak yedeklenen öğelerle ilişkili kayıtları döndürür. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlevin yedekleme raporları tarafından desteklenen tüm yedekleme çözümleriyle ilgili kayıtları döndürmesini sağlar (desteklenen değerler "Azure sanal makine yedeklemesi", "Azure VM yedekleme 'de SQL", "Azure VM yedeklemesi 'nde SAP HANA", "Azure depolama (Azure dosyaları) yedeklemesi", "Azure Backup Aracısı", "DPM", "Azure Backup Sunucusu" veya bu değerlerden herhangi birinin virgülle ayrılmış birleşimi). | N | "Azure sanal makine yedeklemesi, Azure 'da SQL VM yedeklemesi, DPM, Azure Backup Aracısı" |
| Protectionınfolist | Yalnızca etkin bir şekilde korunan yedekleme örneklerinin dahil edilip edilmeyeceğini veya korumanın durdurulduğu örnekleri ve ilk yedeklemenin beklediği örnekleri dahil etmek için bu parametreyi kullanın. Desteklenen değerler "Protected", "Protectiondurdurulan", "ınitialbackuppending" veya bu değerlerden herhangi birinin virgülle ayrılmış bir birleşimidir. Varsayılan olarak, bu değer "*" değeridir ve bu durum, koruma ayrıntılarından bağımsız olarak tüm yedekleme örneklerinin işlev aramasını yapar. | N | Korunamadı |
| DatasourceSetName | İşlevin çıkışını belirli bir üst kaynağa filtrelemek için bu parametreyi kullanın. Örneğin, "Testvm" sanal makinesine ait olan Azure VM yedekleme örneklerine SQL döndürmek için bu parametrenin değeri olarak _Testvm_ ' yi belirtin. Varsayılan olarak, değer "*" olur ve bu, işlev tüm yedekleme örneklerinde kayıtlar için arama yapar. | N | Testvm |
| Backupınstancename | Belirli bir yedekleme örneğini ada göre aramak için bu parametreyi kullanın. Varsayılan olarak, değer "*" olur ve bu, işlev aramasını tüm yedekleme örnekleri için yapar. | N | Testvm |
| DisplayAllFields | İşlev tarafından döndürülen alanların yalnızca bir alt kümesini almak isteyip istemediğinizi seçmek için bu parametreyi kullanın. Bu parametrenin değeri false ise işlev, işlevin çıktısından depolama ve bekletme noktasıyla ilgili bilgileri ortadan kaldırır. Bu işlevi daha büyük bir sorguda ara adım olarak kullanıyorsanız ve analiz için gerekli olmayan sütunları ortadan kaldırarak sorgunun performansını iyileştirmeniz gerekiyorsa yararlıdır. Varsayılan olarak, bu parametrenin değeri true 'dur, bu da işlevin yedekleme örneğiyle ilgili tüm alanları döndürmesini sağlar. | N | true |
| Toplamatürü | Verilerin alınması gereken zaman parçalı yapısını belirtmek için bu parametreyi kullanın. Bu parametrenin değeri "Daily" ise, işlev her gün yedekleme örneği başına bir kayıt döndürür ve depolama alanı tüketiminin günlük eğilimlerini ve yedekleme örneği sayısını analiz etmenize olanak tanır. Bu parametrenin değeri "Weekly" ise, işlev her bir yedekleme örneği başına haftada bir kayıt döndürür ve haftalık eğilimleri analiz etmenize olanak tanır. Benzer şekilde, aylık eğilimleri çözümlemek için "Monthly" belirtebilirsiniz. Varsayılan değer "günlük" değeridir. Verileri daha büyük zaman aralıklarında görüntülüyorsanız, daha iyi sorgu performansı ve eğilim analizi kolaylığı için "haftalık" veya "aylık" kullanılması önerilir. | N | Mi |

**Döndürülen alanlar**

| **Alan adı** | **Açıklama** |
| -------------- | --------------- |
| UniqueId | Yedekleme örneğinin benzersiz KIMLIĞINI belirten birincil anahtar |
| Id | Yedekleme örneğinin KIMLIĞI Azure Resource Manager (ARM) |
| FriendlyName | Yedekleme örneğinin kolay adı |
| Protectionınfo | Yedekleme örneğinin koruma ayarları hakkında bilgi. Örneğin, koruma yapılandırıldı, koruma durduruldu, ilk yedekleme bekliyor |
| LatestRecoveryPoint | Yedekleme örneğiyle ilişkili en son kurtarma noktasının tarihi ve saati |
| OldestRecoveryPoint | Yedekleme örneğiyle ilişkili en eski kurtarma noktasının tarihi ve saati |
| Sourcesizeınmbs | Yedekleme örneğinin MB cinsinden ön uç boyutu |
| VaultStore_StorageConsumptionInMBs | Kasa standart katmanındaki yedekleme örneği tarafından tüketilen toplam bulut depolaması |
| DataSourceFriendlyName | Yedekleme örneğine karşılık gelen veri kaynağının kolay adı |
| BackupSolution | Yedekleme örneğinin ilişkilendirildiği yedekleme çözümü. Örneğin, Azure VM yedeklemesi, Azure VM 'de SQL yedekleme ve benzeri. |
| DatasourceType | Yedekleme örneğine karşılık gelen veri kaynağının türü. Örneğin, "Microsoft. COMPUTE/virtualMachines" |
| Datasourceresourceıd | Yedekleme örneğine karşılık gelen temel alınan veri kaynağının KIMLIĞI Azure Resource Manager (ARM). Örneğin, VM 'nin Azure Resource Manager (ARM) KIMLIĞI |
| DatasourceSetFriendlyName | Veri kaynağının üst kaynağının kolay adı (uygun olduğunda). Örneğin, Azure VM veri kaynağındaki bir SQL için, bu alan SQL veritabanının mevcut olduğu VM 'nin adını içerir |
| Datasourcesetresourceıd | Veri kaynağının üst kaynağının KIMLIĞI (varsa) Azure Resource Manager (ARM). Örneğin, Azure VM veri kaynağındaki bir SQL için, bu alan SQL veritabanının bulunduğu sanal makinenin Azure Resource Manager (ARM) KIMLIĞINI içerir |
| DatasourceSetType | Veri kaynağının üst kaynak kaynağının türü (uygun olduğunda). Örneğin, Azure VM veri kaynağı 'nda bir SAP HANA için bu alan, üst kaynak bir Azure VM olduğundan Microsoft. COMPUTE/virtualMachines olacaktır |
| PolicyName | Yedekleme örneğiyle ilişkili ilkenin adı |
| Policyuniqueıd | Yedekleme örneğiyle ilişkili ilkeye başvuran yabancı anahtar  |
| PolicyId | Yedekleme örneğiyle ilişkili ilkenin KIMLIĞI Azure Resource Manager (ARM) |
| Vaultresourceıd | Yedekleme örneğiyle ilişkili kasanın KIMLIĞI Azure Resource Manager (ARM) |
| Vaultuniqueıd | Yedekleme örneğiyle ilişkili kasayı ifade eden yabancı anahtar |
| VaultName | Yedekleme örneğiyle ilişkili kasasının adı |
| VaultTags | Yedekleme örneğiyle ilişkili kasasının etiketleri |
| Vaultsubscriptionıd | Yedekleme örneğiyle ilişkili kasanın abonelik KIMLIĞI |
| VaultLocation | Yedekleme örneğiyle ilişkili kasasının konumu |
| VaultStore_StorageReplicationType | Yedekleme örneğiyle ilişkili kasanın depolama çoğaltma türü |
| VaultType | Kasanın türü "Microsoft. RecoveryServices/kaults" |
| TimeGenerated | Kaydın zaman damgası |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends ()

Bu işlev, her faturalandırma varlığının geçmiş kayıtlarını döndürür ve bu, ön uç boyutu ve depolama tüketimiyle ilgili günlük, haftalık ve aylık eğilimleri birden çok ayrıntı düzeyi düzeyinde görüntülemenize olanak sağlar.

**Parametreler**

| **Parametre adı** | **Açıklama** | **Gerekli mi?** | **Örnek değer** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart 'dan RangeEnd 'e kadar olan tüm faturalandırma grubu ile ilgili kayıtları almak için RangeEnd parametresiyle birlikte bu parametreyi kullanın. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart 'dan RangeEnd 'e kadar olan tüm faturalandırma grubu ile ilgili kayıtları almak için RangeStart parametresiyle birlikte bu parametreyi kullanın. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList  | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir abonelik kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlev için parametre olarak bir dizi virgülle ayrılmış abonelik kimliklerinin belirtilmesi, yalnızca belirtilen aboneliklerde bulunan Faturalandırma gruplarını almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm abonelikler genelinde kayıtlar için arama yapar. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Bu parametreyi, yedekleme verilerinin bulunduğu belirli bir bölge kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işleve parametre olarak virgülle ayrılmış bölgelerin bir listesini belirtmek, yalnızca belirtilen bölgelerde olan faturalandırma gruplarını almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlev tüm bölgelerde kayıtlar için arama yapar. | N | "eastus, westus"|
| VaultList    |Bu parametreyi, belirli bir kasa kümesi için işlevin çıkışını filtrelemek üzere kullanın. Bu işlevin parametresi olarak virgülle ayrılmış bir kasa adları listesi belirtilmesi, yalnızca belirtilen kasaların bulunduğu yedekleme örneklerinin kayıtlarını almanıza yardımcı olur. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlevi tüm kasaların içindeki fatura gruplarının kayıtlarını arayacak şekilde yapar. | N |"vault1, vault2, vault3"|
| VaultTypeList     | İşlevin çıkışını belirli bir kasa türüyle ilgili kayıtlarla filtrelemek için bu parametreyi kullanın. Şu anda desteklenen tek kasa türü, bu parametrenin varsayılan değeri olan "Microsoft. RecoveryServices/Vaults" dir. | N | "Microsoft. RecoveryServices/Vaults"|
| ExcludeLegacyEvent  | Verilerin eski AzureDiagnostics tablosunda sorgulanıp sorgulanmayacağını belirlemek için bu parametreyi kullanın. Bu parametrenin değeri false ise, işlevi hem AzureDiagnostics tablosundan hem de kaynağa özgü tablolardan verileri sorgular. Bu parametrenin değeri true ise, işlevi yalnızca kaynağa özgü tablolardan verileri sorgular. True varsayılan değerdir. | N | true |
| BackupSolutionList | Bu parametreyi, Azure ortamınızda kullanılan belirli bir yedekleme çözümleri kümesi için işlevin çıkışını filtrelemek üzere kullanın. Örneğin, bu parametrenin değeri olarak "Azure sanal makine yedekleme, Azure VM yedeklemesi 'nde SQL", DPM "belirtirseniz, işlev yalnızca Azure sanal makine yedeklemesi, Azure VM yedeklemesi veya DPM 'den Azure 'a yedekleme kullanılarak yedeklenen öğelerle ilişkili kayıtları döndürür. Varsayılan olarak, bu parametrenin değeri ' * ' olur ve bu, işlevin yedekleme raporları tarafından desteklenen tüm yedekleme çözümleriyle ilgili kayıtları döndürmesini sağlar (desteklenen değerler "Azure sanal makine yedeklemesi", "Azure VM yedekleme 'de SQL", "Azure VM yedeklemesi 'nde SAP HANA", "Azure depolama (Azure dosyaları) yedeklemesi", "Azure Backup Aracısı", "DPM", "Azure Backup Sunucusu" veya bu değerlerden herhangi birinin virgülle ayrılmış birleşimi). | N | "Azure sanal makine yedeklemesi, Azure 'da SQL VM yedeklemesi, DPM, Azure Backup Aracısı" |
| BillingGroupName | Belirli bir faturalandırma grubunu ada göre aramak için bu parametreyi kullanın. Varsayılan olarak, değer "*" olur ve bu, işlev tüm faturalandırma grupları için arama yapar. | N | Testvm |
| Toplamatürü | Verilerin alınması gereken zaman parçalı yapısını belirtmek için bu parametreyi kullanın. Bu parametrenin değeri "Daily" ise, işlev günlük faturalandırma grubu başına bir kayıt döndürür ve depolama alanı tüketiminin ve ön uç boyutunun günlük eğilimlerini analiz etmenize olanak tanır. Bu parametrenin değeri "Weekly" ise, işlev her bir yedekleme örneği başına haftada bir kayıt döndürür ve haftalık eğilimleri analiz etmenize olanak tanır. Benzer şekilde, aylık eğilimleri çözümlemek için "Monthly" belirtebilirsiniz. Varsayılan değer "günlük" değeridir. Verileri daha büyük zaman aralıklarında görüntülüyorsanız, daha iyi sorgu performansı ve eğilim analizi kolaylığı için "haftalık" veya "aylık" kullanılması önerilir. | N | Mi |

**Döndürülen alanlar**

| **Alan adı** | **Açıklama** |
| -------------- | --------------- |
| UniqueId | Fatura grubunun benzersiz KIMLIĞINI belirten birincil anahtar |
| FriendlyName | Faturalandırma grubunun kolay adı |
| Name | Faturalandırma grubunun adı |
| Tür | Faturalandırma grubu türü. Örneğin, ProtectedContainer veya Backupıtem |
| Sourcesizeınmbs | Fatura grubunun MB cinsinden ön uç boyutu |
| VaultStore_StorageConsumptionInMBs | Kasa standart katmanındaki faturalandırma grubu tarafından tüketilen toplam bulut depolaması |
| BackupSolution | Faturalama grubunun ilişkilendirildiği yedekleme çözümü. Örneğin, Azure VM yedeklemesi, Azure VM 'de SQL yedekleme ve benzeri. |
| Vaultresourceıd | Faturalandırma grubuyla ilişkili kasanın KIMLIĞI Azure Resource Manager (ARM) |
| Vaultuniqueıd | Faturalandırma grubuyla ilişkili kasayı ifade eden yabancı anahtar |
| VaultName | Faturalandırma grubuyla ilişkili kasasının adı |
| VaultTags | Fatura grubuyla ilişkili kasasının etiketleri |
| Vaultsubscriptionıd | Faturalandırma grubuyla ilişkili kasanın abonelik KIMLIĞI |
| VaultLocation | Faturalandırma grubuyla ilişkili kasasının konumu |
| VaultStore_StorageReplicationType | Faturalandırma grubuyla ilişkili kasanın depolama çoğaltma türü |
| VaultType | Kasanın türü "Microsoft. RecoveryServices/kaults" |
| TimeGenerated | Kaydın zaman damgası |
| ExtendedProperties | Faturalandırma grubunun ek özellikleri |

## <a name="sample-queries"></a>Örnek sorgular

Aşağıda, sistem işlevlerini kullanmaya başlamanıza yardımcı olacak bazı örnek sorgular verilmiştir.

- Belirli bir zaman aralığındaki tüm başarısız Azure VM yedekleme işleri

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Belirli bir zaman aralığındaki tüm SQL günlüğü yedekleme işleri

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- "Testvm" sanal makinesi için kullanılan yedekleme depolaması haftalık eğilimi

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>Sonraki adımlar
[Yedekleme raporları hakkında daha fazla bilgi edinin](./configure-reports.md)