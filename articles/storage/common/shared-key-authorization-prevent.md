---
title: Paylaşılan anahtarla yetkilendirmeyi engelle (Önizleme)
titleSuffix: Azure Storage
description: İstemcilerin istekleri yetkilendirmek için Azure AD 'yi kullanmasını gerektirmek için, paylaşılan anahtarla (Önizleme) yetkilendirilmiş depolama hesabına yönelik isteklere izin verebilirsiniz.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/11/2021
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: b7290abe102d22bb87c87c3c9d13ee99c127b942
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199922"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Azure depolama hesabı (Önizleme) için paylaşılan anahtar yetkilendirmesini engelleyin

Azure depolama hesabına yönelik her güvenli isteği yetkilendirmelidir. Varsayılan olarak, isteklere Azure Active Directory (Azure AD) kimlik bilgileriyle veya paylaşılan anahtar yetkilendirmesi için hesap erişim anahtarı kullanılarak yetki atanabilir. Bu iki tür yetkilendirme, Azure AD, paylaşılan anahtar üzerinde üstün güvenlik ve kullanım kolaylığı sağlar ve Microsoft tarafından önerilir. İstemcilerin istekleri yetkilendirmek için Azure AD 'yi kullanmasını gerektirmek için, paylaşılan anahtarla (Önizleme) yetkilendirilmiş depolama hesabına yönelik isteklere izin verebilirsiniz.

Bir depolama hesabı için paylaşılan anahtar yetkilendirmesinin yetkisini kaldırdığınızda, Azure depolama, hesap erişim anahtarlarıyla yetkilendirilmiş olan bu hesaba yapılan tüm sonraki istekleri reddeder. Yalnızca Azure AD ile yetkilendirilmiş güvenli istekler başarılı olur. Azure AD kullanma hakkında daha fazla bilgi için bkz. [Azure Active Directory kullanarak bloblara ve kuyruklara erişim yetkisi verme](storage-auth-aad.md).

> [!IMPORTANT]
> Paylaşılan anahtar yetkilendirmesi Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan yasal koşullara yönelik [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Bu makalede, paylaşılan anahtar yetkilendirmesi ile gönderilen isteklerin nasıl algılanacağı ve depolama hesabınız için paylaşılan anahtar yetkilendirmesinin nasıl düzeltileceği açıklanır. Önizlemeye nasıl kaydolacağınızı öğrenmek için [Önizleme hakkında](#about-the-preview)bölümüne bakın.

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>İstemci uygulamaları tarafından kullanılan yetkilendirme türünü Algıla

Bir depolama hesabı için paylaşılan anahtar yetkilendirmesine izin vermemek istediğinizde, paylaşılan anahtar yetkilendirmesi için hesap erişim anahtarlarını kullanan istemcilerden gelen istekler başarısız olur. Bu değişikliği yapmadan önce paylaşılan anahtar yetkilendirmesi 'nin istemci uygulamalarını nasıl etkileyebileceğini anlamak için, depolama hesabı için günlük ve ölçümleri etkinleştirin. Daha sonra isteklerin nasıl yetkilendirildiği hakkında bir süre boyunca hesabınıza yönelik isteklerin desenlerini analiz edebilirsiniz.

Paylaşılan anahtar veya paylaşılan erişim imzası (SAS) ile yetkilendirilmiş depolama hesabının kaç istek aldığını öğrenmek için ölçümleri kullanın. Hangi istemcilerin bu istekleri gönderdiğini öğrenmek için günlükleri kullanın.

Önizleme sırasında paylaşılan erişim imzasıyla yapılan isteklerin yorumlanması hakkında daha fazla bilgi için [Önizleme hakkında](#about-the-preview)bölümüne bakın.

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>Paylaşılan anahtarla kaç istek yetkilendirileceğini izleme

Bir depolama hesabına yönelik isteklerin nasıl yetkilendirildiğini izlemek için Azure portal Azure Ölçüm Gezgini kullanın. Ölçüm Gezgini hakkında daha fazla bilgi için bkz. [Azure Ölçüm Gezgini](../../azure-monitor/essentials/metrics-getting-started.md)kullanmaya başlama.

Paylaşılan anahtar veya SAS ile yapılan istekleri izleyen bir ölçüm oluşturmak için aşağıdaki adımları izleyin:

1. Azure portalda depolama hesabınıza gidin. **İzleme** bölümünde **ölçümler**' i seçin.
1. **Ölçüm ekle** seçeneğini belirleyin. **Ölçüm** iletişim kutusunda aşağıdaki değerleri belirtin:
    1. **Kapsam** alanını depolama hesabının adı olarak ayarlayın.
    1. **Ölçüm ad alanını** *Hesap* olarak ayarlayın. Bu ölçüm, depolama hesabına yönelik tüm istekleri rapor eder.
    1. **Ölçüm** alanını *işlemler* olarak ayarlayın.
    1. **Toplama** alanını *Sum* olarak ayarlayın.

    Yeni ölçüm, belirli bir zaman aralığı boyunca depolama hesabına göre işlem sayısı toplamını görüntüler. Ortaya çıkan ölçüm, aşağıdaki görüntüde gösterildiği gibi görünür:

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="Paylaşılan anahtar veya SAS ile yapılan işlemleri toplamak için metriğin nasıl yapılandırılacağını gösteren ekran görüntüsü":::

1. Ardından, yetkilendirme türüyle ilgili ölçüm üzerinde bir filtre oluşturmak için **Filtre Ekle** düğmesini seçin.
1. **Filtre** iletişim kutusunda aşağıdaki değerleri belirtin:
    1. **Özellik** değerini *kimlik doğrulama* olarak ayarlayın.
    1. **İşleç** alanını eşittir işareti (=) olarak ayarlayın.
    1. **Değerler** alanında *hesap anahtarı* ve *SAS*' ı seçin.
1. Sağ üst köşede, ölçüsünü görüntülemek istediğiniz zaman aralığını seçin. Ayrıca, isteklerin toplanmasının ne kadar ayrıntılı olduğunu, 1 dakikadan 1 aya kadar bir yerde aralıklar belirterek belirtebilirsiniz. Örneğin, son 30 gün içinde güne göre toplanan istekleri görmek için **zaman aralığını** 30 güne ve **zaman parçalı** olarak 1 güne ayarlayın.

Ölçüyü yapılandırdıktan sonra, depolama hesabınıza yönelik istekler grafikte görünmeye başlar. Aşağıdaki görüntüde, paylaşılan anahtarla yetkilendirilmiş veya bir SAS belirteciyle oluşturulan istekler gösterilmektedir. İstekler, son otuz gün içinde gün başına toplanır.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="Paylaşılan anahtarla yetkilendirilmiş toplanmış istekleri gösteren ekran görüntüsü":::

Depolama hesabınızda paylaşılan anahtarla yetkilendirilmiş belirli sayıda istek yapıldığında size bildirimde bulunan bir uyarı kuralı da yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici kullanarak ölçüm uyarıları oluşturma, görüntüleme ve yönetme](../../azure-monitor/alerts/alerts-metric.md).

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>Paylaşılan anahtar veya SAS ile istekleri yetkilendirmek isteyen istemcileri tanımlamak için günlükleri çözümleyin

Azure depolama günlükleri, bir isteğin nasıl yetkilendirildiği de dahil olmak üzere depolama hesabında yapılan isteklerle ilgili ayrıntıları yakalar. Hangi istemcilerin paylaşılan anahtar veya SAS belirteci ile istekleri yetkilendirtiğini belirleyen günlükleri çözümleyebilirsiniz.

Azure depolama hesabınıza istekleri nasıl yetkilendirdiğini değerlendirmek amacıyla günlüğe kaydetmek için Azure Izleyici 'de Azure depolama günlüğü 'nü (Önizleme) kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Storage 'ı izleme](../blobs/monitor-blob-storage.md).

Azure Izleyici 'de Azure depolama günlüğü, günlük verilerini çözümlemek için günlük sorgularının kullanılmasını destekler. Günlükleri sorgulamak için bir Azure Log Analytics çalışma alanı kullanabilirsiniz. Günlük sorguları hakkında daha fazla bilgi edinmek için bkz. [öğretici: Log Analytics sorguları kullanmaya başlama](../../azure-monitor/logs/log-analytics-tutorial.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Azure portal bir tanılama ayarı oluşturun

Azure depolama verilerini Azure Izleyici ile günlüğe kaydetmek ve Azure Log Analytics ile analiz etmek için, önce hangi istek türlerinin ve hangi depolama hizmetlerini günlüğe kaydetmek istediğinizi belirten bir tanılama ayarı oluşturmanız gerekir. Azure portal bir tanılama ayarı oluşturmak için aşağıdaki adımları izleyin:

1. Azure [izleyici önizlemesinde Azure Storage günlük](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)kaydına kaydolun.
1. Azure depolama hesabınızı içeren abonelikte yeni bir Log Analytics çalışma alanı oluşturun veya mevcut bir Log Analytics çalışma alanını kullanın. Depolama hesabınız için günlüğe kaydetmeyi yapılandırdıktan sonra, Günlükler Log Analytics çalışma alanında kullanılabilir. Daha fazla bilgi için [Azure portal Log Analytics çalışma alanı oluşturma](../../azure-monitor/logs/quick-create-workspace.md)bölümüne bakın.
1. Azure portalda depolama hesabınıza gidin.
1. Izleme bölümünde **Tanılama ayarları (Önizleme)** öğesini seçin.
1. İsteklerini günlüğe kaydetmek istediğiniz Azure Storage hizmetini seçin. Örneğin, istekleri blob depolamaya kaydetmek için **BLOB** ' u seçin.
1. **Tanılama ayarı ekle**’yi seçin.
1. Tanılama ayarı için bir ad girin.
1. **Kategori ayrıntıları**' nın altında, **günlük** bölümünde, tüm veri isteklerini seçili hizmete kaydetmek için **storageread**, **storagewrite** ve **storagedelete** ' i seçin.
1. **Hedef ayrıntıları** altında **Log Analytics gönder**' i seçin. Aşağıdaki görüntüde gösterildiği gibi aboneliğinizi ve daha önce oluşturduğunuz Log Analytics çalışma alanını seçin.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="İstekleri günlüğe kaydetmek için bir tanılama ayarı oluşturmayı gösteren ekran görüntüsü":::

Depolama hesabınızda her bir Azure depolama kaynağı türü için bir tanılama ayarı oluşturabilirsiniz.

Tanılama ayarını oluşturduktan sonra depolama hesabına yönelik istekler daha sonra bu ayara göre günlüğe kaydedilir. Daha fazla bilgi için bkz. [Azure 'da kaynak günlüklerini ve ölçümleri toplamak için tanılama ayarı oluşturma](../../azure-monitor/essentials/diagnostic-settings.md).

Azure Izleyici 'de Azure depolama günlüklerinde bulunan alanların bir başvurusu için bkz. [kaynak günlükleri (Önizleme)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>Paylaşılan anahtar veya SAS ile yapılan istekler için sorgu günlükleri

Azure Izleyici 'de Azure depolama günlükleri, bir depolama hesabına istek yapmak için kullanılan yetkilendirme türünü içerir. Paylaşılan anahtar veya SAS ile yetkilendirilmiş son yedi gün içinde yapılan isteklerin günlüklerini almak için Log Analytics çalışma alanınızı açın. Sonra, aşağıdaki sorguyu yeni bir günlük sorgusuna yapıştırın ve çalıştırın. Bu sorgu, paylaşılan anahtar veya SAS ile yetkilendirilmiş en sık gönderilen isteklerin on IP adresini görüntüler:

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

Ayrıca, paylaşılan anahtar veya SAS ile yetkilendirilmiş istekler hakkında bildirim almak için bu sorguya dayalı bir uyarı kuralı yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme](../../azure-monitor/alerts/alerts-log.md).

## <a name="remediate-authorization-via-shared-key"></a>Paylaşılan anahtar aracılığıyla yetkilendirmeyi düzelt

Depolama Hesabınıza yönelik isteklerin nasıl yetkilendirildiği çözümlendikten sonra, paylaşılan anahtar aracılığıyla erişimi engellemek için işlem yapabilirsiniz. Ancak, bunun yerine Azure AD 'yi kullanmak için paylaşılan anahtar yetkilendirmesi kullanan uygulamaları güncelleştirmeniz gerekir. Geçiş izlemek için [istemci uygulamalar tarafından kullanılan yetkilendirme türünü algılama](#detect-the-type-of-authorization-used-by-client-applications) bölümünde açıklandığı gibi günlükleri ve ölçümleri izleyebilirsiniz. Azure AD 'yi blob ve kuyruk verileriyle kullanma hakkında daha fazla bilgi için, bkz. [Azure Active Directory kullanarak bloblara ve kuyruklara erişim yetkisi verme](storage-auth-aad.md).

Paylaşılan anahtarla yetkilendirilmiş istekleri güvenli bir şekilde reddedebileceğiniz konusunda emin olduğunuzda, depolama hesabı için **Allowsharedkeyaccess** özelliğini **false** olarak ayarlayabilirsiniz.

**Allowsharedkeyaccess** özelliği varsayılan olarak ayarlı değildir ve açıkça ayarlanana kadar bir değer döndürmez. Depolama hesabı, özellik değeri **null** olduğunda veya **true** olduğunda paylaşılan anahtarla yetkilendirilmiş isteklere izin verir.

> [!WARNING]
> Herhangi bir istemci şu anda paylaşılan anahtarla Depolama hesabınızdaki verilere erişiyorsa, Microsoft, depolama hesabına paylaşılan anahtar erişimine izin vermeden önce bu istemcileri Azure AD 'ye geçirmenize olanak önerir.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal bir depolama hesabı için paylaşılan anahtar yetkilendirmesi engellemek için şu adımları izleyin:

1. Azure portalda depolama hesabınıza gidin.
1. **Ayarlar**' ın altındaki **yapılandırma** ayarını bulun.
1. **Paylaşılan anahtar erişimine Izin ver** ' i **devre dışı** olarak ayarla.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="Hesap için paylaşılan anahtar erişiminin nasıl engelileceğini gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell ile bir depolama hesabı için paylaşılan anahtar yetkilendirmesine izin vermemek için, [az. Storage PowerShell modülünü](https://www.powershellgallery.com/packages/Az.Storage), sürüm 3.4.0 veya üstünü yüklersiniz. Ardından, **Allowsharedkeyaccess** özelliğini yeni veya mevcut bir depolama hesabı için yapılandırın.

Aşağıdaki örnek, PowerShell ile var olan bir depolama hesabı için paylaşılan anahtarla erişime nasıl izin vermemeyi gösterir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -AllowSharedKeyAccess $false
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile bir depolama hesabı için paylaşılan anahtar yetkilendirmesine izin vermemek için Azure CLı sürüm 2.20.0 veya sonraki bir sürümünü yüklemelisiniz. Daha fazla bilgi için bkz. [Azure CLI 'Yı yüklerken](/cli/azure/install-azure-cli). Ardından, **Allowsharedkeyaccess** özelliğini yeni veya mevcut bir depolama hesabı için yapılandırın.

Aşağıdaki örnek, Azure CLı ile var olan bir depolama hesabı için paylaşılan anahtarla erişime nasıl izin vermemeyi gösterir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-shared-key-access false
```

---

Paylaşılan anahtar yetkilendirmesine izin verdikten sonra, paylaşılan anahtar yetkilendirmesi olan depolama hesabına bir istek yapılması 403 (yasak) hata koduyla başarısız olur. Azure depolama, depolama hesabında anahtar tabanlı yetkilendirmeye izin verilmediğini belirten hata döndürüyor.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>Paylaşılan anahtar erişimine izin verilmediğini doğrulayın

Paylaşılan anahtar yetkilendirmesinin artık izin verilmediğini doğrulamak için, hesap erişim anahtarıyla bir veri işlemini çağırmayı deneyebilirsiniz. Aşağıdaki örnek, erişim anahtarını kullanarak bir kapsayıcı oluşturmaya çalışır. Depolama hesabı için paylaşılan anahtar yetkilendirmesi devre dışı bırakıldığında bu çağrı başarısız olur. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key> \
    --auth-mode key
```

> [!NOTE]
> Anonim istekler yetkilendirilmemiş ve anonim genel okuma erişimi için depolama hesabı ve kapsayıcısını yapılandırdıysanız devam edecektir. Daha fazla bilgi için bkz. [kapsayıcılar ve Bloblar için anonim genel okuma erişimini yapılandırma](../blobs/anonymous-read-access-configure.md).

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>Birden çok hesap için paylaşılan anahtar erişim ayarını denetleyin

En iyi performansa sahip bir dizi depolama hesabı genelinde paylaşılan anahtar erişimi ayarını denetlemek için Azure portal Azure Kaynak Grafiği Gezginini kullanabilirsiniz. Kaynak Grafiği Gezginini kullanma hakkında daha fazla bilgi edinmek için bkz. [hızlı başlangıç: Azure Kaynak Grafiği gezginini kullanarak Ilk kaynak grafik sorgunuzu çalıştırma](../../governance/resource-graph/first-query-portal.md).

Kaynak Graph Explorer 'da aşağıdaki sorguyu çalıştırmak, depolama hesaplarının bir listesini döndürür ve her bir hesap için paylaşılan anahtar erişim ayarını görüntüler:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="permissions-for-allowing-or-disallowing-shared-key-access"></a>Paylaşılan anahtar erişimine izin verme veya erişimi verme izinleri

Depolama hesabı için **Allowsharedkeyaccess** özelliğini ayarlamak için, bir kullanıcının depolama hesapları oluşturma ve yönetme izinleri olması gerekir. Bu izinleri sağlayan Azure rol tabanlı erişim denetimi (Azure RBAC) rolleri, **Microsoft. Storage/storageAccounts/Write** veya **Microsoft. Storage/storageaccounts/ \*** Action ' i içerir. Bu eylemle birlikte yerleşik roller şunlardır:

- Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) rolü
- Azure Resource Manager [katkıda bulunan](../../role-based-access-control/built-in-roles.md#contributor) rolü
- [Depolama hesabı katılımcısı](../../role-based-access-control/built-in-roles.md#storage-account-contributor) rolü

Bu roller, Azure Active Directory (Azure AD) aracılığıyla bir depolama hesabındaki verilere erişim sağlamaz. Ancak, hesap erişim anahtarlarına erişim izni veren **Microsoft. Storage/storageAccounts/ListKeys/Action** öğeleri de bulunur. Bu izinle, bir Kullanıcı, bir depolama hesabındaki tüm verilere erişmek için hesap erişim anahtarlarını kullanabilir.

Rol atamaları, bir kullanıcının depolama hesabı için paylaşılan anahtar erişimine izin verip vermemesine izin vermek için depolama hesabı düzeyi veya daha yüksek olmalıdır. Rol kapsamı hakkında daha fazla bilgi için bkz. [Azure RBAC kapsamını anlama](../../role-based-access-control/scope-overview.md).

Bu rollerin atamasını yalnızca bir depolama hesabı oluşturma veya özelliklerini güncelleştirme yeteneğine ihtiyaç duyan kullanıcılarla sınırlandırmamaya dikkat edin. Kullanıcıların görevlerini gerçekleştirmek için ihtiyacı olan en az izinlere sahip olduğundan emin olmak için en az ayrıcalık ilkesini kullanın. Azure RBAC ile erişimi yönetme hakkında daha fazla bilgi için bkz. [Azure RBAC Için en iyi uygulamalar](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Klasik abonelik yöneticisi rolleri hizmet yöneticisi ve Co-Administrator Azure Resource Manager [sahip](../../role-based-access-control/built-in-roles.md#owner) rolünün eşdeğerini içerir. **Sahip** rolü tüm eylemleri içerir, bu nedenle bu yönetici rollerinden birine sahip bir kullanıcı da depolama hesapları oluşturabilir ve yönetebilir. Daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>Paylaşılan anahtarın SAS belirteçlerini nasıl etkileyeceğini anlayın

Depolama hesabı için paylaşılan anahtar erişimine izin verilirse Azure Storage, SAS türlerini ve istek tarafından hedeflenen hizmeti temel alan SAS belirteçlerini işler. Aşağıdaki tabloda her bir SAS türünün nasıl yetkilendirildiği ve depolama hesabı için **Allowsharedkeyaccess** özelliği **false** olduğunda Azure depolama 'nın bu SAS 'ın nasıl işleneceği gösterilmektedir.

| SAS türü | Yetkilendirme türü | AllowSharedKeyAccess false olduğunda davranış |
|-|-|-|
| Kullanıcı temsili SAS (yalnızca BLOB depolama) | Azure AD | İsteğe izin verilir. Microsoft, üstün güvenlik için mümkün olduğunda Kullanıcı temsili SAS kullanılmasını önerir. |
| Hizmet SAS | Paylaşılan Anahtar | Tüm Azure depolama hizmetleri için istek reddedildi. |
| Hesap SAS | Paylaşılan Anahtar | Tüm Azure depolama hizmetleri için istek reddedildi. |

Paylaşılan erişim imzaları hakkında daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](storage-sas-overview.md).

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Diğer Azure araçları ve hizmetleriyle uyumluluğu göz önünde bulundurun

Birçok Azure hizmeti, Azure depolama ile iletişim kurmak için paylaşılan anahtar yetkilendirmesi kullanır. Bir depolama hesabı için paylaşılan anahtar yetkilendirmesine izin verirseniz, bu hizmetler bu hesaptaki verilere erişemez ve uygulamalarınız olumsuz etkilenebilir.

Bazı Azure Araçları, Azure depolama 'ya erişmek için Azure AD yetkilendirmesi kullanma seçeneği sunar. Aşağıdaki tabloda bazı popüler Azure araçları ve Azure depolama 'ya yönelik istekleri yetkilendirmek için Azure AD kullanıp kullanamayacağını Not eden notlar listelenmektedir.

| Azure aracı | Azure depolama için Azure AD yetkilendirmesi |
|-|-|
| Azure portalı | Destekleniyor. Azure portal Azure AD hesabınızla yetkilendirme hakkında daha fazla bilgi için bkz. [Azure Portal blob verilerine erişim yetkisi verme](../blobs/authorize-data-operations-portal.md). |
| AzCopy | BLOB depolama için desteklenir. AzCopy işlemlerini yetkilendirme hakkında daha fazla bilgi için bkz. AzCopy belgelerinde [Yetkilendirme kimlik bilgilerini nasıl sağlayacaksınız](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) . |
| Azure Depolama Gezgini | Yalnızca BLOB depolama ve Azure Data Lake Storage 2. için desteklenir. Kuyruk depolamaya Azure AD erişimi desteklenmiyor. Doğru Azure AD kiracısını seçtiğinizden emin olun. Daha fazla bilgi için bkz. [Depolama Gezgini kullanmaya başlama](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) |
| Azure PowerShell | Destekleniyor. Azure AD ile blob veya kuyruk işlemleri için PowerShell komutlarının nasıl yetkilendirdiği hakkında daha fazla bilgi için bkz. blob verilerine erişmek için Azure [ad kimlik bilgileriyle PowerShell komutlarını çalıştırma](../blobs/authorize-data-operations-powershell.md) veya [kuyruk verilerine ERIŞIM için Azure AD kimlik bilgileriyle PowerShell komutlarını çalıştırma](../queues/authorize-data-operations-powershell.md). |
| Azure CLI’si | Destekleniyor. Blob ve kuyruk verilerine erişim için Azure AD ile Azure CLı komutlarına yetki verme hakkında bilgi için bkz. [BLOB veya kuyruk verilerine erişmek için Azure AD kimlik bilgileriyle Azure CLI komutlarını çalıştırma](../blobs/authorize-data-operations-cli.md). |
| Azure IoT Hub | Destekleniyor. Daha fazla bilgi için bkz. [sanal ağlar için IoT Hub desteği](../../iot-hub/virtual-network-support.md). |
| Azure Cloud Shell | Azure Cloud Shell, Azure portal tümleşik bir kabuktur. Azure Cloud Shell, depolama hesabındaki bir Azure dosya paylaşımında Kalıcılık için dosya barındırır. Bu depolama hesabı için paylaşılan anahtar yetkilendirmesi devre dışı bırakılırsa, bu dosyalar erişilemez duruma getirilir. Daha fazla bilgi için bkz. [Microsoft Azure dosyaları depolama alanınızı bağlama](../../cloud-shell/overview.md#connect-your-microsoft-azure-files-storage). <br /><br /> Paylaşılan anahtar erişimine izin verilmeyen depolama hesaplarını yönetmek için Azure Cloud Shell komutları çalıştırmak için, önce Azure RBAC aracılığıyla bu hesaplara gerekli izinleri vermiş olduğunuzdan emin olun. Daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?](../../role-based-access-control/overview.md). |

## <a name="transition-azure-files-and-table-storage-workloads"></a>Azure dosyalarını ve tablo depolama iş yüklerini geçirme

Azure depolama, yalnızca blob ve kuyruk depolaması istekleri için Azure AD yetkilendirmesini destekler. Bir depolama hesabı için paylaşılan anahtar ile yetkilendirmeye izin verirseniz, Azure dosyaları veya paylaşılan anahtar yetkilendirmesi kullanan tablo depolaması istekleri başarısız olur. Azure portal dosya ve tablo verilerine erişmek için her zaman paylaşılan anahtar yetkilendirmesi kullandığından, depolama hesabının paylaşılan anahtarıyla yetkilendirmenize izin verirseniz, Azure portal dosya veya tablo verilerine erişemezsiniz.

Microsoft, paylaşılan anahtar aracılığıyla hesaba erişime izin vermeden önce tüm Azure dosyalarını veya tablo depolama verilerini ayrı bir depolama hesabına geçirmenize veya bu ayarı Azure dosyalarını ya da tablo depolama iş yüklerini destekleyen depolama hesaplarına uygulamamanızı önerir.

Bir depolama hesabı için paylaşılan anahtar erişiminin izni vermemek, Azure dosyalarına SMB bağlantılarını etkilemez.

## <a name="about-the-preview"></a>Önizleme hakkında

Paylaşılan anahtar yetkilendirmesinin önlenmesi için Önizleme, Azure genel bulutu 'nda bulunabilir. Yalnızca Azure Resource Manager dağıtım modelini kullanan depolama hesapları için desteklenir. Hangi depolama hesaplarının Azure Resource Manager dağıtım modelini kullanması hakkında daha fazla bilgi için bkz. [depolama hesabı türleri](storage-account-overview.md#types-of-storage-accounts).

Önizleme, aşağıdaki bölümlerde açıklanan sınırlamaları içerir.

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>Ölçümler ve günlüğe kaydetme, nasıl yetkilendirildiğine bakılmaksızın bir SA ile yapılan tüm istekleri raporlar

Azure Izleyici 'de Azure ölçümleri ve günlüğü, Önizlemedeki farklı türdeki paylaşılan erişim imzaları arasında ayrım yapmazlar. Azure Ölçüm Gezgini **SAS** filtresi ve Azure 'Da Azure depolama 'da **SAS** alanı, her IKISI de herhangi bir SAS türüyle yetkilendirilmiş istekleri rapor ediyor. Ancak, farklı türlerdeki paylaşılan erişim imzaları farklı şekilde yetkilendirilir ve paylaşılan anahtar erişimine izin verilmezse farklı şekilde davranır:

- Bir hizmet SAS belirteci veya hesap SAS belirteci paylaşılan anahtarla yetkilendirildi ve **Allowsharedkeyaccess** özelliği **false** olarak ayarlandığında blob depolamaya yönelik bir istekte izin verilmez.
- Kullanıcı temsili SAS, Azure AD ile yetkilendirildi ve **Allowsharedkeyaccess** özelliği **false** olarak ayarlandığında blob depolamaya yönelik bir istekte izin verilecek.

Depolama hesabınıza trafiği değerlendirirken, [istemci uygulamaları tarafından kullanılan yetkilendirme türünü algılama](#detect-the-type-of-authorization-used-by-client-applications) bölümünde açıklandığı gibi ölçüm ve günlüklerin, bir Kullanıcı temsili SAS ile yapılan istekleri dahil olabileceğini göz önünde bulundurun. **Allowsharedkeyaccess** özelliği **false** olarak AYARLANDıĞıNDA Azure depolamanın bir SAS 'a nasıl yanıt vereceğini öğrenmek Için bkz. [paylaşılan anahtarın SAS belirteçlerini nasıl etkilediğini anlayın](#understand-how-disallowing-shared-key-affects-sas-tokens).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure depolama 'daki verilere erişimi yetkilendirme](storage-auth.md)
- [Azure Active Directory kullanarak bloblara ve kuyruklara erişim yetkisi verme](storage-auth-aad.md)
- [Paylaşılan Anahtar ile yetkilendirme](/rest/api/storageservices/authorize-with-shared-key)
