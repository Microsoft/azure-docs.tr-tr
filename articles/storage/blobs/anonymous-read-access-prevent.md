---
title: Kapsayıcılara ve bloblara anonim genel okuma erişimini engelleyin
titleSuffix: Azure Storage
description: Bir depolama hesabına karşı Anonim istekleri çözümlemeyi ve tüm depolama hesabı veya tek bir kapsayıcı için anonim erişimin nasıl önleyeceğinizi öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 53f29c2b8f7a17ac2a23cc081660e8dcb4b9f387
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701867"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Kapsayıcılara ve bloblara anonim genel okuma erişimini engelleyin

Azure depolama 'daki kapsayıcılara ve bloblara anonim genel okuma erişimi, verileri paylaşmanın kolay bir yoludur, ancak bir güvenlik riski de sunabilir. Anonim erişim bozacağından ' i yönetmeniz ve verilerinize anonim erişimin nasıl değerlendirileceğini anlamak önemlidir. İşlemsel karmaşıklık, insan hatası veya genel olarak erişilebilen verilere karşı kötü amaçlı saldırı maliyetli veri ihlallerine neden olabilir. Microsoft, yalnızca uygulama senaryonuz için gerektiğinde anonim erişimi etkinleştirmenizi önerir.

Varsayılan olarak, blob verilerinize genel erişim her zaman yasaktır. Ancak, bir depolama hesabının varsayılan yapılandırması, bir kullanıcının bir depolama hesabındaki kapsayıcılara ve bloblara genel erişimi yapılandırmasına izin verir. Gelişmiş güvenlik için, tek bir kapsayıcı için genel erişim ayarından bağımsız olarak, depolama hesabına genel erişime izin verebilirsiniz. Depolama hesabına genel erişime izin vermemek, bir kullanıcının hesaptaki bir kapsayıcı için ortak erişimi etkinleştirmelerini engeller. Microsoft, senaryonuz için gerekli olmadığı takdirde bir depolama hesabına genel erişim izni vermemenizi önerir. Ortak erişime izin vermemek, istenmeyen anonim erişimden kaynaklanan veri ihlallerine engel olmaya yardımcı olur.

Depolama hesabı için genel blob erişimine izin vermemek için Azure depolama, bu hesaba yönelik tüm anonim istekleri reddeder. Bir hesap için genel erişime izin verildikten sonra, söz konusu hesaptaki kapsayıcılar daha sonra genel erişim için yapılandırılamaz. Ortak erişim için önceden yapılandırılmış olan tüm kapsayıcılar, anonim istekleri artık kabul etmez. Daha fazla bilgi için bkz. [kapsayıcılar ve Bloblar için anonim genel okuma erişimini yapılandırma](anonymous-read-access-configure.md).

Bu makalede, depolama hesaplarınız için genel erişimi sürekli olarak yönetmek üzere bir sürükleme (algılama-düzeltme-denetim-Idare) çerçevesinin nasıl kullanılacağı açıklanır.

## <a name="detect-anonymous-requests-from-client-applications"></a>İstemci uygulamalarından gelen Anonim istekleri Algıla

Bir depolama hesabı için genel okuma erişimine izin vermemeniz durumunda, istekleri, genel erişim için yapılandırılmış olan kapsayıcılara ve bloblara reddetmeniz önerilir. Bir depolama hesabı için genel erişime izin vermemek, bu depolama hesabındaki bağımsız kapsayıcılar için genel erişim ayarlarını geçersiz kılar. Depolama hesabı için genel erişime izin verilse, bu hesaba yönelik ileride yapılacak anonim istekler başarısız olur.

Genel erişimin istemci uygulamalarını nasıl etkileyebileceğini anlamak için, Microsoft bu hesap için günlüğe kaydetmeyi ve ölçümleri etkinleştirmenizi ve zaman aralığı boyunca anonim isteklerin desenlerini çözümlemeyi önerir. Depolama hesabına yönelik anonim isteklerin sayısını öğrenmek için ölçümleri kullanın ve hangi kapsayıcıların adsız olarak erişildiğini öğrenmek için günlükleri kullanın.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Ölçüm Gezgini ile anonim istekleri izleme

Bir depolama hesabına Anonim istekleri izlemek için Azure portal Azure Ölçüm Gezgini kullanın. Ölçüm Gezgini hakkında daha fazla bilgi için bkz. [Azure Ölçüm Gezgini](../../azure-monitor/essentials/metrics-getting-started.md)kullanmaya başlama.

Anonim istekleri izleyen bir ölçüm oluşturmak için aşağıdaki adımları izleyin:

1. Azure portalda depolama hesabınıza gidin. **İzleme** bölümünde **ölçümler**' i seçin.
1. **Ölçüm ekle** seçeneğini belirleyin. **Ölçüm** iletişim kutusunda aşağıdaki değerleri belirtin:
    1. Kapsam alanını depolama hesabının adı olarak ayarlayın.
    1. **Ölçüm ad alanını** *BLOB* olarak ayarlayın. Bu ölçüm, istekleri yalnızca blob depolamaya göre rapor eder.
    1. **Ölçüm** alanını *işlemler* olarak ayarlayın.
    1. **Toplama** alanını *Sum* olarak ayarlayın.

    Yeni ölçüm, belirli bir zaman aralığı boyunca blob depolamaya göre işlem sayısı toplamını görüntüler. Ortaya çıkan ölçüm, aşağıdaki görüntüde gösterildiği gibi görünür:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Blob işlemlerini toplamak için ölçümün nasıl yapılandırılacağını gösteren ekran görüntüsü":::

1. Ardından, anonim istekler için ölçüm üzerinde bir filtre oluşturmak üzere **Filtre Ekle** düğmesini seçin.
1. **Filtre** iletişim kutusunda aşağıdaki değerleri belirtin:
    1. **Özellik** değerini *kimlik doğrulama* olarak ayarlayın.
    1. **İşleç** alanını eşittir işareti (=) olarak ayarlayın.
    1. **Values** alanını *anonim* olarak ayarlayın.
1. Sağ üst köşede, ölçümü görüntülemek istediğiniz zaman aralığını seçin. Ayrıca, isteklerin toplanmasının ne kadar ayrıntılı olduğunu, 1 dakikadan 1 aya kadar bir yerde aralıklar belirterek belirtebilirsiniz.

Ölçümü yapılandırdıktan sonra, anonim istekler grafikte görünmeye başlar. Aşağıdaki görüntüde, son otuz dakika içinde toplanan anonim istekler gösterilmektedir.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Blob depolamaya yönelik toplanmış Anonim istekleri gösteren ekran görüntüsü":::

Ayrıca, depolama hesabınızda belirli sayıda anonim istek yapıldığında size bildirimde bulunan bir uyarı kuralı yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici kullanarak ölçüm uyarıları oluşturma, görüntüleme ve yönetme](../../azure-monitor/alerts/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Anonim istek alma kapsayıcılarını tanımlamak için günlükleri analiz etme

Azure depolama günlükleri, bir isteğin nasıl yetkilendirildiği de dahil olmak üzere depolama hesabında yapılan isteklerle ilgili ayrıntıları yakalar. Hangi kapsayıcıların adsız istek aldığını öğrenmek için günlükleri çözümleyebilirsiniz.

Anonim istekleri değerlendirmek amacıyla Azure depolama hesabınıza yönelik istekleri günlüğe kaydetmek için Azure Izleyici 'de (Önizleme) Azure depolama günlüğü 'nü kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Storage 'ı izleme](./monitor-blob-storage.md).

Azure Izleyici 'de Azure depolama günlüğü, günlük verilerini çözümlemek için günlük sorgularının kullanılmasını destekler. Günlükleri sorgulamak için bir Azure Log Analytics çalışma alanı kullanabilirsiniz. Günlük sorguları hakkında daha fazla bilgi edinmek için bkz. [öğretici: Log Analytics sorguları kullanmaya başlama](../../azure-monitor/logs/log-analytics-tutorial.md).

> [!NOTE]
> Azure Izleyici 'de Azure depolama günlüğü önizlemesi yalnızca Azure genel bulutunda desteklenir. Kamu bulutları Azure Izleyici ile Azure depolama için günlüğe kaydetmeyi desteklemez.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Azure portal bir tanılama ayarı oluşturun

Azure depolama verilerini Azure Izleyici ile günlüğe kaydetmek ve Azure Log Analytics ile analiz etmek için, önce hangi istek türlerinin ve hangi depolama hizmetlerini günlüğe kaydetmek istediğinizi belirten bir tanılama ayarı oluşturmanız gerekir. Azure portal bir tanılama ayarı oluşturmak için aşağıdaki adımları izleyin:

1. Azure depolama hesabınızı içeren abonelikte yeni bir Log Analytics çalışma alanı oluşturun. Depolama hesabınız için günlüğe kaydetmeyi yapılandırdıktan sonra, Günlükler Log Analytics çalışma alanında kullanılabilir. Daha fazla bilgi için [Azure portal Log Analytics çalışma alanı oluşturma](../../azure-monitor/logs/quick-create-workspace.md)bölümüne bakın.
1. Azure portalda depolama hesabınıza gidin.
1. Izleme bölümünde **Tanılama ayarları (Önizleme)** öğesini seçin.
1. Blob depolamada yapılan istekleri günlüğe kaydetmek için **BLOB** ' u seçin.
1. **Tanılama ayarı ekle**’yi seçin.
1. Tanılama ayarı için bir ad girin.
1. **Kategori ayrıntıları**' nın altında, **günlük** bölümünde günlüğe kaydedilecek istek türlerini seçin. Tüm anonim istekler okuma istekleri olacak, bu nedenle Anonim istekleri yakalamak için **Storageread** öğesini seçin.
1. **Hedef ayrıntıları** altında **Log Analytics gönder**' i seçin. Aşağıdaki görüntüde gösterildiği gibi aboneliğinizi ve daha önce oluşturduğunuz Log Analytics çalışma alanını seçin.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="İstekleri günlüğe kaydetmek için bir tanılama ayarı oluşturmayı gösteren ekran görüntüsü":::

Tanılama ayarını oluşturduktan sonra depolama hesabına yönelik istekler daha sonra bu ayara göre günlüğe kaydedilir. Daha fazla bilgi için bkz. [Azure 'da kaynak günlüklerini ve ölçümleri toplamak için tanılama ayarı oluşturma](../../azure-monitor/essentials/diagnostic-settings.md).

Azure Izleyici 'de Azure depolama günlüklerinde bulunan alanların bir başvurusu için bkz. [kaynak günlükleri (Önizleme)](./monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Anonim istekler için sorgu günlükleri

Azure Izleyici 'de Azure depolama günlükleri, bir depolama hesabına istek yapmak için kullanılan yetkilendirme türünü içerir. Günlük sorgunuzda, anonim istekleri görüntülemek için **AuthenticationType** özelliğini filtreleyin.

Blob depolamaya yönelik anonim istekler için son 7 güne ait günlükleri almak için Log Analytics çalışma alanınızı açın. Sonra, aşağıdaki sorguyu yeni bir günlük sorgusuna yapıştırın ve çalıştırın:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

Ayrıca, bu sorguya bağlı olarak, anonim istekler hakkında bildirim almak için bir uyarı kuralı yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme](../../azure-monitor/alerts/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Anonim genel erişimi düzelt

Depolama hesabınızdaki kapsayıcılara ve bloblara Anonim istekleri değerlendirdikten sonra, genel erişimi sınırlamak veya engellemek için işlem yapabilirsiniz. Depolama hesabınızdaki bazı kapsayıcıların genel erişim için kullanılabilir olması gerekiyorsa, Depolama hesabınızdaki her bir kapsayıcı için genel erişim ayarını yapılandırabilirsiniz. Bu seçenek, genel erişim üzerinde en ayrıntılı denetimi sağlar. Daha fazla bilgi için bkz. [bir kapsayıcı için genel erişim düzeyini ayarlama](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

Gelişmiş güvenlik için, tüm depolama hesabı için genel erişime izin vermemeyi sağlayabilirsiniz. Bir depolama hesabı için genel erişim ayarı, bu hesaptaki kapsayıcıların bağımsız ayarlarını geçersiz kılar. Bir depolama hesabı için genel erişime izin vermemek istediğinizde, herkese açık erişime izin verecek şekilde yapılandırılmış tüm kapsayıcılara artık anonim olarak erişilemez. Daha fazla bilgi için bkz. [depolama hesabı için genel okuma erişimine Izin verme veya bu erişimi engelleme](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

Senaryonuz belirli kapsayıcıların genel erişim için kullanılabilir olmasını gerektiriyorsa, bu kapsayıcıları ve bloblarını genel erişim için ayrılan depolama hesaplarına taşımak önerilir. Bundan sonra, diğer depolama hesapları için genel erişime izin vermeyebilirsiniz.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Bir bloba genel erişime izin verilmediğini doğrulama

Belirli bir bloba yönelik ortak erişime izin verilmedikçe emin olmak için blobu URL 'SI aracılığıyla indirmeyi deneyebilirsiniz. İndirme başarılı olursa blobu hala herkese açık olarak kullanılabilir olur. Depolama hesabı için genel erişime izin verilmediğinden blob herkese açık bir şekilde erişilemezse, bu depolama hesabında genel erişime izin verilmediğini belirten bir hata iletisi görürsünüz.

Aşağıdaki örnek, bir blob 'u URL aracılığıyla indirmeyi denemek için PowerShell 'in nasıl kullanılacağını gösterir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Kapsayıcının ortak erişim ayarını değiştirmeye izin verilmediğini doğrula

Depolama hesabı için ortak erişime izin verdikten sonra kapsayıcının genel erişim ayarının değiştirilemeyeceğini doğrulamak için, ayarı değiştirmeyi deneyebilirsiniz. Depolama hesabı için genel erişime izin verilmedikçe kapsayıcının ortak erişim ayarını değiştirmek başarısız olur.

Aşağıdaki örnek, bir kapsayıcının ortak erişim ayarını değiştirmeye çalışmak için PowerShell 'in nasıl kullanılacağını gösterir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Ortak erişime sahip bir kapsayıcı oluşturmaya izin verilmediğini doğrulayın

Depolama hesabı için genel erişime izin verilmediğinde, ortak erişim etkin olan yeni bir kapsayıcı oluşturabileceksiniz. Doğrulamak için, ortak erişim etkinleştirilmiş bir kapsayıcı oluşturmayı deneyebilirsiniz.

Aşağıdaki örnek, ortak erişim etkinleştirilmiş bir kapsayıcı oluşturmayı denemek için PowerShell 'in nasıl kullanılacağını gösterir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>Birden çok hesap için genel erişim ayarını denetleyin

En iyi performansa sahip bir dizi depolama hesabı genelinde genel erişim ayarını denetlemek için Azure portal Azure Kaynak Grafiği Gezginini kullanabilirsiniz. Kaynak Grafiği Gezginini kullanma hakkında daha fazla bilgi edinmek için bkz. [hızlı başlangıç: Azure Kaynak Grafiği gezginini kullanarak Ilk kaynak grafik sorgunuzu çalıştırma](../../governance/resource-graph/first-query-portal.md).

**Allowblobpublicaccess** özelliği bir depolama hesabı için varsayılan olarak ayarlı değildir ve açıkça ayarlanana kadar bir değer döndürmez. Özellik değeri **null** ya da **doğru** olduğunda depolama hesabı ortak erişime izin verir.

Kaynak Graph Explorer 'da aşağıdaki sorguyu çalıştırmak, depolama hesaplarının bir listesini döndürür ve her bir hesap için ortak erişim ayarını görüntüler:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

Aşağıdaki görüntüde bir abonelik genelindeki bir sorgunun sonuçları gösterilmektedir. **Allowblobpublicaccess** özelliğinin açıkça ayarlandığı depolama hesapları için, sonuçlarda **doğru** veya **yanlış** olarak göründüğünü unutmayın. **Allowblobpublicaccess** özelliği bir depolama hesabı için ayarlanmamışsa, sorgu sonuçlarında boş (veya null) olarak görünür.

:::image type="content" source="media/anonymous-read-access-prevent/check-public-access-setting-accounts.png" alt-text="Depolama hesapları genelinde genel erişim ayarı için sorgu sonuçlarını gösteren ekran görüntüsü":::

## <a name="use-azure-policy-to-audit-for-compliance"></a>Uyumluluğu denetlemek için Azure Ilkesini kullanma

Çok sayıda depolama hesabınız varsa, bu hesapların genel erişimi engelleyecek şekilde yapılandırıldığından emin olmak için bir denetim yapmak isteyebilirsiniz. Uyumluluğun bir depolama hesapları kümesini denetlemek için Azure Ilkesi ' ni kullanın. Azure Ilkesi, Azure kaynaklarına kurallar uygulayan ilkeler oluşturmak, atamak ve yönetmek için kullanabileceğiniz bir hizmettir. Azure Ilkesi, bu kaynakları kurumsal standartlarınızla ve hizmet düzeyi Sözleşmelerinizle uyumlu tutmanıza yardımcı olur. Daha fazla bilgi için bkz. [Azure Ilkesine genel bakış](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Denetim efektli bir ilke oluşturma

Azure Ilkesi, bir ilke kuralı bir kaynağa göre değerlendirildiğinde ne olacağını belirlemek için etkileri destekler. Denetim etkisi, bir kaynak uyumlu olmadığında, ancak isteği durdurmadığında bir uyarı oluşturur. Efektler hakkında daha fazla bilgi için bkz. [Azure ilke efektlerini anlama](../../governance/policy/concepts/effects.md).

Azure portal bir depolama hesabı için genel erişim ayarı için denetim etkisi olan bir ilke oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal Azure Ilke hizmeti ' ne gidin.
1. **Yazma** bölümünde **tanımlar**' ı seçin.
1. **İlke tanımı Ekle** ' yi seçerek yeni bir ilke tanımı oluşturun.
1. **Tanım konumu** alanı için, denetim ilkesi kaynağının nerede olduğunu belirtmek üzere **daha fazla** düğmesini seçin.
1. İlke için bir ad belirtin. İsteğe bağlı olarak bir açıklama ve kategori belirtebilirsiniz.
1. **İlke kuralı** altında, **policyrule** bölümüne aşağıdaki ilke tanımını ekleyin.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. İlkeyi kaydedin.

### <a name="assign-the-policy"></a>İlke atama

Sonra, ilkeyi bir kaynağa atayın. İlke kapsamı bu kaynağa ve altındaki kaynaklara karşılık gelir. İlke atama hakkında daha fazla bilgi için bkz. [Azure ilke atama yapısı](../../governance/policy/concepts/assignment-structure.md).

İlkeyi Azure portal atamak için aşağıdaki adımları izleyin:

1. Azure portal Azure Ilke hizmeti ' ne gidin.
1. **Yazma** bölümünde **atamalar**' ı seçin.
1. Yeni ilke ataması oluşturmak için **Ilke ata** ' yı seçin.
1. **Kapsam** alanı için, ilke atamasının kapsamını seçin.
1. **İlke tanımı** alanı Için, **daha fazla** düğmesini seçin ve ardından listeden önceki bölümde tanımladığınız ilkeyi seçin.
1. İlke ataması için bir ad girin. Açıklama isteğe bağlıdır.
1. **İlke zorlamasının** *etkin* olarak ayarlanmış kalsın. Bu ayarın denetim ilkesi üzerinde hiçbir etkisi yoktur.
1. Atamayı oluşturmak için **gözden geçir + oluştur** ' u seçin.

### <a name="view-compliance-report"></a>Uyumluluk raporunu görüntüle

İlkeyi atadıktan sonra, uyumluluk raporunu görüntüleyebilirsiniz. Bir denetim ilkesi için uyumluluk raporu, ilke ile uyumlu olmayan depolama hesaplarının hakkında bilgi sağlar. Daha fazla bilgi için bkz. [ilke uyumluluk verilerini edinme](../../governance/policy/how-to/get-compliance-data.md).

Uyumluluk raporunun, ilke ataması oluşturulduktan sonra kullanılabilir olması birkaç dakika sürebilir.

Uyumluluk raporunu Azure portal görüntülemek için aşağıdaki adımları izleyin:

1. Azure portal Azure Ilke hizmeti ' ne gidin.
1. **Uyumluluk**' i seçin.
1. Önceki adımda oluşturduğunuz ilke atamasının adı için sonuçları filtreleyin. Rapor, ilkeyle ilgili olarak kaç kaynağın uyumsuz olduğunu gösterir.
1. Uyumluluğa sahip olmayan depolama hesaplarının bir listesi de dahil olmak üzere ek ayrıntılar için raporda ayrıntıya gidebilirsiniz.

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="Blob genel erişimi için denetim ilkesi uyumluluk raporunu gösteren ekran görüntüsü":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>Yetkili erişimi zorlamak için Azure Ilkesini kullanma

Azure Ilkesi, Azure kaynaklarının gereksinimlere ve standartlara bağlı olmasını sağlayarak bulut idare desteği sağlar. Kuruluşunuzdaki depolama hesaplarının yalnızca yetkili isteklere izin verdiğinden emin olmak için, anonim isteklere izin veren bir genel erişim ayarı ile yeni bir depolama hesabı oluşturulmasını önleyen bir ilke oluşturabilirsiniz. Bu ilke, bu hesap için genel erişim ayarı ilkeyle uyumlu değilse, mevcut bir hesapta tüm yapılandırma değişikliklerini de engeller.

Zorlama ilkesi, genel erişime izin vermek üzere bir depolama hesabı oluşturacak veya değiştirecek bir isteği engellemek için reddetme efektini kullanır. Efektler hakkında daha fazla bilgi için bkz. [Azure ilke efektlerini anlama](../../governance/policy/concepts/effects.md).

Anonim isteklere izin veren bir genel erişim ayarı için reddetme etkisi olan bir ilke oluşturmak için, [uyumluluğu denetlemek Için Azure Ilkesi kullanma](#use-azure-policy-to-audit-for-compliance)bölümünde açıklanan adımları izleyin, ancak Ilke tanımının **POLICYRULE** bölümünde aşağıdaki JSON 'ı sağlayın:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

İlkeyi reddetme etkimiyle oluşturup bir kapsama atadıktan sonra, Kullanıcı, genel erişime izin veren bir depolama hesabı oluşturamaz. Ya da bir Kullanıcı, mevcut bir depolama hesabında halen genel erişime izin veren bir yapılandırma değişikliği yapabilir. Bunun denenmeye çalışılması bir hata ile sonuçlanır. Hesap oluşturma veya yapılandırmaya devam etmek için depolama hesabının genel erişim ayarı **yanlış** olarak ayarlanmalıdır.

Aşağıdaki görüntüde, reddetme etkisi olan bir ilke ortak erişime izin verilmediğinde genel erişime izin veren (yeni bir hesap için varsayılan) bir depolama hesabı oluşturmaya çalıştığınızda oluşan hata gösterilmektedir.

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="İlke ihlalinden bir depolama hesabı oluşturulurken oluşan hatayı gösteren ekran görüntüsü":::

## <a name="permissions-for-allowing-or-disallowing-public-access"></a>Genel erişime izin verme veya erişimi vermeme izinleri

Depolama hesabı için **Allowblobpublicaccess** özelliğini ayarlamak için, bir kullanıcının depolama hesapları oluşturma ve yönetme izinleri olması gerekir. Bu izinleri sağlayan Azure rol tabanlı erişim denetimi (Azure RBAC) rolleri, **Microsoft. Storage/storageAccounts/Write** veya **Microsoft. Storage/storageaccounts/ \*** Action ' i içerir. Bu eylemle birlikte yerleşik roller şunlardır:

- Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) rolü
- Azure Resource Manager [katkıda bulunan](../../role-based-access-control/built-in-roles.md#contributor) rolü
- [Depolama hesabı katılımcısı](../../role-based-access-control/built-in-roles.md#storage-account-contributor) rolü

Bu roller, Azure Active Directory (Azure AD) aracılığıyla bir depolama hesabındaki verilere erişim sağlamaz. Ancak, hesap erişim anahtarlarına erişim izni veren **Microsoft. Storage/storageAccounts/ListKeys/Action** öğeleri de bulunur. Bu izinle, bir Kullanıcı, bir depolama hesabındaki tüm verilere erişmek için hesap erişim anahtarlarını kullanabilir.

Rol atamaları, bir kullanıcının depolama hesabı için genel erişime izin verip vermemesine izin vermek için depolama hesabı düzeyi veya daha yüksek olmalıdır. Rol kapsamı hakkında daha fazla bilgi için bkz. [Azure RBAC kapsamını anlama](../../role-based-access-control/scope-overview.md).

Bu rollerin atamasını yalnızca bir depolama hesabı oluşturma veya özelliklerini güncelleştirme yeteneğine ihtiyaç duyan kullanıcılarla sınırlandırmamaya dikkat edin. Kullanıcıların görevlerini gerçekleştirmek için ihtiyacı olan en az izinlere sahip olduğundan emin olmak için en az ayrıcalık ilkesini kullanın. Azure RBAC ile erişimi yönetme hakkında daha fazla bilgi için bkz. [Azure RBAC Için en iyi uygulamalar](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Klasik abonelik yöneticisi rolleri hizmet yöneticisi ve Co-Administrator Azure Resource Manager [sahip](../../role-based-access-control/built-in-roles.md#owner) rolünün eşdeğerini içerir. **Sahip** rolü tüm eylemleri içerir, bu nedenle bu yönetici rollerinden birine sahip bir kullanıcı da depolama hesapları oluşturabilir ve yönetebilir. Daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="next-steps"></a>Sonraki adımlar

- [Kapsayıcılar ve BLOB 'lar için anonim genel okuma erişimini yapılandırma](anonymous-read-access-configure.md)
- [.NET ile ortak kapsayıcılara ve bloblara anonim erişim](anonymous-read-access-client.md)