---
title: Gelen istekler için en düşük gerekli Aktarım Katmanı Güvenliği (TLS) sürümünü zorla
titleSuffix: Azure Storage
description: Azure depolama 'ya karşı istek yapan istemciler için Aktarım Katmanı Güvenliği (TLS) için en düşük sürümü gerektirecek bir depolama hesabı yapılandırın.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 257cd8dce2a080203f116a6f0d5b7c7ebd6d13f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593185"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>Depolama hesabına yönelik istekler için gereken en düşük Aktarım Katmanı Güvenliği (TLS) sürümünü zorla

İstemci uygulaması ve bir Azure depolama hesabı arasındaki iletişim, Aktarım Katmanı Güvenliği (TLS) kullanılarak şifrelenir. TLS, Internet üzerinden istemciler ve hizmetler arasında gizlilik ve veri bütünlüğünü sağlayan standart bir şifreleme protokolüdür. TLS hakkında daha fazla bilgi için bkz. [Aktarım Katmanı Güvenliği](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Azure Storage Şu anda TLS protokolünün üç sürümünü desteklemektedir: 1,0, 1,1 ve 1,2. Azure depolama, genel HTTPS uç noktalarında TLS 1,2 kullanır, ancak geriye dönük uyumluluk için TLS 1,0 ve TLS 1,1 desteklenmeye devam etmektedir.

Varsayılan olarak, Azure depolama hesapları istemcilerin en eski TLS, TLS 1,0 ve üzeri sürümü ile veri göndermesini ve almasına izin verir. Daha sıkı güvenlik önlemleri zorlamak için depolama hesabınızı, istemcilerin daha yeni bir TLS sürümüyle veri göndermesini ve almasını gerektirecek şekilde yapılandırabilirsiniz. Depolama hesabı en az TLS sürümü gerektiriyorsa, eski bir sürümle yapılan tüm istekler başarısız olur.

Bu makalede, depolama hesaplarınız için güvenli TLS 'yi sürekli olarak yönetmek üzere bir sürükleme (algılama-düzeltme-denetim-Idare) çerçevesinin nasıl kullanılacağı açıklanır.

Bir istemci uygulamasından istek gönderirken belirli bir TLS sürümünü belirtme hakkında bilgi için, bkz. [bir istemci uygulaması Için Aktarım Katmanı Güvenliği (TLS) yapılandırma](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>İstemci uygulamaları tarafından kullanılan TLS sürümünü Algıla

Depolama hesabınız için en düşük TLS sürümünü zorlayarak, daha eski bir TLS sürümü ile veri gönderen istemcilerden gelen istekleri reddetmeniz önerilir. En düşük TLS sürümünü yapılandırmanın istemci uygulamalarını nasıl etkileyebileceğini anlamak için, Microsoft, Azure depolama hesabınız için günlük kaydını etkinleştirmenizi ve TLS istemci uygulamalarının hangi sürümlerinin kullandığını tespit etmek üzere bir zaman aralığından sonra günlükleri analiz etmenize olanak önerir.

Azure depolama hesabınıza yönelik istekleri günlüğe kaydetmek ve istemci tarafından kullanılan TLS sürümünü öğrenmek için Azure Izleyici 'de (Önizleme) Azure depolama günlüğü 'nü kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Storage 'ı izleme](../blobs/monitor-blob-storage.md).

Azure Izleyici 'de Azure depolama günlüğü, günlük verilerini çözümlemek için günlük sorgularının kullanılmasını destekler. Günlükleri sorgulamak için bir Azure Log Analytics çalışma alanı kullanabilirsiniz. Günlük sorguları hakkında daha fazla bilgi edinmek için bkz. [öğretici: Log Analytics sorguları kullanmaya başlama](../../azure-monitor/logs/log-analytics-tutorial.md).

Azure depolama verilerini Azure Izleyici ile günlüğe kaydetmek ve Azure Log Analytics ile analiz etmek için, önce hangi istek türlerinin ve hangi depolama hizmetlerini günlüğe kaydetmek istediğinizi belirten bir tanılama ayarı oluşturmanız gerekir. Azure Izleyici 'de Azure depolama günlükleri genel önizleme aşamasındadır ve tüm genel bulut bölgelerinde önizleme testi için kullanılabilir. Bu önizleme, Bloblar (Azure Data Lake Storage 2.), dosyalar, kuyruklar ve tablolar için günlükleri mümkün bir şekilde sunar. Azure portal bir tanılama ayarı oluşturmak için aşağıdaki adımları izleyin:

1. Azure depolama hesabınızı içeren abonelikte yeni bir Log Analytics çalışma alanı oluşturun. Depolama hesabınız için günlüğe kaydetmeyi yapılandırdıktan sonra, Günlükler Log Analytics çalışma alanında kullanılabilir. Daha fazla bilgi için [Azure portal Log Analytics çalışma alanı oluşturma](../../azure-monitor/logs/quick-create-workspace.md)bölümüne bakın.
1. Azure portalda depolama hesabınıza gidin.
1. Izleme bölümünde **Tanılama ayarları (Önizleme)** öğesini seçin.
1. İsteklerini günlüğe kaydetmek istediğiniz Azure Storage hizmetini seçin. Örneğin, istekleri blob depolamaya kaydetmek için **BLOB** ' u seçin.
1. **Tanılama ayarı ekle**’yi seçin.
1. Tanılama ayarı için bir ad girin.
1. **Kategori ayrıntıları**' nın altında, **günlük** bölümünde günlüğe kaydedilecek istek türlerini seçin. Okuma, yazma ve silme isteklerini günlüğe kaydedebilirsiniz. Örneğin, **Storageread** ve **storagewrite** seçildiğinde, okuma ve yazma istekleri seçili hizmete kaydedilir.
1. **Hedef ayrıntıları** altında **Log Analytics gönder**' i seçin. Aşağıdaki görüntüde gösterildiği gibi aboneliğinizi ve daha önce oluşturduğunuz Log Analytics çalışma alanını seçin.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="İstekleri günlüğe kaydetmek için bir tanılama ayarı oluşturmayı gösteren ekran görüntüsü":::

Tanılama ayarını oluşturduktan sonra depolama hesabına yönelik istekler daha sonra bu ayara göre günlüğe kaydedilir. Daha fazla bilgi için bkz. [Azure 'da kaynak günlüklerini ve ölçümleri toplamak için tanılama ayarı oluşturma](../../azure-monitor/essentials/diagnostic-settings.md).

Azure Izleyici 'de Azure depolama günlüklerinde bulunan alanların bir başvurusu için bkz. [kaynak günlükleri (Önizleme)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Günlüğe kaydedilen istekleri TLS sürümüne göre sorgula

Azure Izleyici 'de Azure depolama günlükleri bir depolama hesabına istek göndermek için kullanılan TLS sürümünü içerir. Günlüğe kaydedilen bir isteğin TLS sürümünü denetlemek için **TlsVersion** özelliğini kullanın.

Son yedi gün içinde farklı TLS sürümleriyle blob depolamaya yönelik kaç istek yapıldığını öğrenmek için Log Analytics çalışma alanınızı açın. Sonra, aşağıdaki sorguyu yeni bir günlük sorgusuna yapıştırın ve çalıştırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Sonuçlar, her TLS sürümünde yapılan isteklerin sayısını gösterir:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="TLS sürümünü döndürmek için Log Analytics sorgusunun sonuçlarını gösteren ekran görüntüsü":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Çağıran IP adresine ve Kullanıcı Aracısı üstbilgisine göre günlüğe kaydedilmiş istekleri sorgula

Azure Izleyici 'de Azure depolama günlükleri, depolama hesabına hangi istemci uygulamalarının eriştiğini değerlendirmenize yardımcı olmak üzere arayan IP adresini ve Kullanıcı Aracısı üst bilgisini de içerir. Bu değerleri, istemci uygulamalarının daha yeni bir TLS sürümünü kullanacak şekilde güncelleştirilip güncelleştirilmediğini veya en düşük TLS sürümü ile gönderilmemişse istemcinin isteği başarısız olarak kabul edilip edilmeyeceğini belirlemek için çözümleyebilirsiniz.

Son yedi gün içinde TLS 1,2 'den daha eski bir TLS sürümü olan hangi istemcilerin istek yaptığını öğrenmek için aşağıdaki sorguyu yeni bir günlük sorgusuna yapıştırın ve çalıştırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>En düşük TLS sürümü ile güvenlik risklerini düzeltin

Daha eski TLS sürümlerini kullanan istemcilerden gelen trafiğin en az olduğundan veya daha eski bir TLS sürümüyle yapılan isteklerin başarısız olması için kabul edilebilir olduğundan emin olduğunuzda, depolama hesabınızda en düşük TLS sürümünü zorlamayı kullanmaya başlayabilirsiniz. İstemcilerin bir depolama hesabına karşı isteklerin olması için en düşük TLS sürümünü kullanmasını zorunlu kılmak, verilerinize yönelik güvenlik risklerini en aza indirmek için bir stratejinin bir parçasıdır.

> [!IMPORTANT]
> Azure depolama 'ya bağlanan bir hizmet kullanıyorsanız, bir depolama hesabı için gerekli en düşük sürümü ayarlamadan önce bu hizmetin, istekleri Azure depolama 'ya göndermek için uygun TLS sürümünü kullanmasını sağlayın.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>Depolama hesabı için en düşük TLS sürümünü yapılandırın

Bir depolama hesabı için en düşük TLS sürümünü yapılandırmak üzere hesabın **Minimumtlsversion** sürümünü ayarlayın. Bu özellik, Azure Resource Manager dağıtım modeliyle oluşturulan tüm depolama hesapları için kullanılabilir. Azure Resource Manager dağıtım modeli hakkında daha fazla bilgi için bkz. [depolama hesabına genel bakış](storage-account-overview.md).

**Minimumtlsversion** özelliği varsayılan olarak ayarlanmadı ve açıkça ayarlanana kadar bir değer döndürmez.  Özellik değeri **null** ise, depolama hesabı TLS sürüm 1,0 veya üzeri ile gönderilen isteklere izin verir.

# <a name="portal"></a>[Portal](#tab/portal)

Azure portal ile bir depolama hesabı oluşturduğunuzda, en düşük TLS sürümü varsayılan olarak 1,2 olarak ayarlanır.

Mevcut bir depolama hesabı için en düşük TLS sürümünü Azure portal yapılandırmak için aşağıdaki adımları izleyin:

1. Azure portalda depolama hesabınıza gidin.
1. **Yapılandırma** ayarını seçin.
1. **En düşük TLS sürümü** altında, aşağıdaki görüntüde gösterildiği gibi, bu depolama hesabındaki verilere erişmek için gereken en düşük TLS sürümünü seçmek üzere açılan listesini kullanın.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Azure portal en düşük TLS sürümünün nasıl yapılandırılacağını gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell ile bir depolama hesabı için en düşük TLS sürümünü yapılandırmak üzere [Azure PowerShell Version 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) veya üzeri sürümünü yüklemelisiniz. Ardından, yeni veya mevcut bir depolama hesabı için **Minimumtlsversion** özelliğini yapılandırın. **Minimumtlsversion** için geçerli değerler `TLS1_0` , `TLS1_1` ve ' dir `TLS1_2` .

Aşağıdaki örnek bir depolama hesabı oluşturur ve **Minimumtlsversion** 'ı TLS 1,1 olarak ayarlar, ardından hesabı güncelleştirir ve **minimumtlsversion** 'ı TLS 1,2 olarak ayarlar. Örnek, her durumda özellik değerini de alır. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile bir depolama hesabı için en düşük TLS sürümünü yapılandırmak üzere Azure CLı sürüm 2.9.0 veya üstünü yüklemeyi yapın. Daha fazla bilgi için bkz. [Azure CLI 'Yı yüklerken](/cli/azure/install-azure-cli). Ardından, yeni veya mevcut bir depolama hesabı için **Minimumtlsversion** özelliğini yapılandırın. **Minimumtlsversion** için geçerli değerler `TLS1_0` , `TLS1_1` ve ' dir `TLS1_2` .

Aşağıdaki örnek bir depolama hesabı oluşturur ve **Minimumtlsversion** 'ı TLS 1,1 olarak ayarlar. Daha sonra hesabı güncelleştirir ve **Minimumtlsversion** özelliğini TLS 1,2 olarak ayarlar. Örnek, her durumda özellik değerini de alır. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[Şablon](#tab/template)

Bir depolama hesabı için en düşük TLS sürümünü şablon ile yapılandırmak için, **Minimumtlsversion** özelliği, veya olarak ayarlanmış bir şablon oluşturun `TLS1_0` `TLS1_1` `TLS1_2` . Aşağıdaki adımlar Azure portal bir şablonun nasıl oluşturulacağını açıklamaktadır.

1. Azure portal **kaynak oluştur**' u seçin.
1. **Market 'Te ara**' te, **şablon dağıtımı** yazın ve ardından **ENTER** tuşuna basın.
1. **Şablon dağıtımı seçin (özel şablonlar kullanarak dağıtın) (Önizleme)**, **Oluştur**' u seçin ve ardından **düzenleyicide kendi şablonunuzu oluşturun**' i seçin.
1. Şablon Düzenleyicisi 'nde, yeni bir hesap oluşturmak için aşağıdaki JSON 'a yapıştırın ve en düşük TLS sürümünü TLS 1,2 olarak ayarlayın. Açılı ayraçlar içindeki yer tutucuları kendi değerlerinizle değiştirmeyi unutmayın.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Şablonu kaydedin.
1. Kaynak grubu parametresini belirtin, ardından şablonu dağıtmak ve **Minimumtlsversion** özelliği yapılandırılmış bir depolama hesabı oluşturmak Için **gözden geçir + oluştur** düğmesini seçin.

---

> [!NOTE]
> Depolama hesabı için en düşük TLS sürümünü güncelleştirdikten sonra, değişiklik tamamen yayılmadan önce 30 saniye kadar sürebilir.

En düşük TLS sürümünü yapılandırmak, Azure depolama kaynak sağlayıcısı 'nın 2019-04-01 veya sonraki bir sürümünü gerektirir. Daha fazla bilgi için bkz. [Azure depolama kaynak sağlayıcısı REST API](/rest/api/storagerp/).

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>Birden çok hesap için gereken en düşük TLS sürümünü denetleyin

En iyi performansa sahip bir dizi depolama hesabı genelinde gereken en düşük TLS sürümünü denetlemek için Azure portal Azure Kaynak Grafiği Gezginini kullanabilirsiniz. Kaynak Grafiği Gezginini kullanma hakkında daha fazla bilgi edinmek için bkz. [hızlı başlangıç: Azure Kaynak Grafiği gezginini kullanarak Ilk kaynak grafik sorgunuzu çalıştırma](../../governance/resource-graph/first-query-portal.md).

Kaynak Graph Explorer 'da aşağıdaki sorguyu çalıştırmak, depolama hesaplarının bir listesini döndürür ve her hesap için en düşük TLS sürümünü görüntüler:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>Bir istemciden en düşük TLS sürümünü test etme

Daha eski bir sürümle yapılan bir depolama hesabı yasaklıyor çağrıları için gereken en düşük TLS sürümünü sınamak için, bir istemciyi daha eski bir TLS sürümünü kullanacak şekilde yapılandırabilirsiniz. Bir istemciyi belirli bir TLS sürümünü kullanacak şekilde yapılandırma hakkında daha fazla bilgi için, bkz. [bir istemci uygulaması Için Aktarım Katmanı Güvenliği 'ni (TLS) yapılandırma](transport-layer-security-configure-client-version.md).

Bir istemci, hesap için yapılandırılmış en düşük TLS sürümünü karşılamayan bir TLS sürümünü kullanarak bir depolama hesabına eriştiğinde, Azure Storage hata kodu 400 hatası (Hatalı Istek) ve kullanılan TLS sürümünün bu depolama hesabında istek yapmasına izin verilmediğini belirten bir ileti döndürür.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Uyumluluğu denetlemek için Azure Ilkesini kullanma

Çok sayıda depolama hesabınız varsa, kuruluşunuzun gerektirdiği en düşük TLS sürümü için tüm hesapların yapılandırıldığından emin olmak üzere bir denetim yapmak isteyebilirsiniz. Uyumluluğun bir depolama hesapları kümesini denetlemek için Azure Ilkesi ' ni kullanın. Azure Ilkesi, Azure kaynaklarına kurallar uygulayan ilkeler oluşturmak, atamak ve yönetmek için kullanabileceğiniz bir hizmettir. Azure Ilkesi, bu kaynakları kurumsal standartlarınızla ve hizmet düzeyi Sözleşmelerinizle uyumlu tutmanıza yardımcı olur. Daha fazla bilgi için bkz. [Azure Ilkesine genel bakış](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Denetim efektli bir ilke oluşturma

Azure Ilkesi, bir ilke kuralı bir kaynağa göre değerlendirildiğinde ne olacağını belirlemek için etkileri destekler. Denetim etkisi, bir kaynak uyumlu olmadığında, ancak isteği durdurmadığında bir uyarı oluşturur. Efektler hakkında daha fazla bilgi için bkz. [Azure ilke efektlerini anlama](../../governance/policy/concepts/effects.md).

Azure portal en düşük TLS sürümüne yönelik denetim efektli bir ilke oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal Azure Ilke hizmeti ' ne gidin.
1. **Yazma** bölümünde **tanımlar**' ı seçin.
1. **İlke tanımı Ekle** ' yi seçerek yeni bir ilke tanımı oluşturun.
1. **Tanım konumu** alanı için, denetim ilkesi kaynağının nerede olduğunu belirtmek üzere **daha fazla** düğmesini seçin.
1. İlke için bir ad belirtin. İsteğe bağlı olarak bir açıklama ve kategori belirtebilirsiniz.
1. **İlke kuralı** altında, **policyrule** bölümüne aşağıdaki ilke tanımını ekleyin.

    ```json
    {
      "policyRule": {
        "if": {
          "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Storage/storageAccounts"
            },
            {
              "not": {
                "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
                "equals": "TLS1_2"
              }
            }
          ]
        },
        "then": {
          "effect": "audit"
        }
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

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="Minimum TLS sürümü için denetim ilkesi uyumluluk raporunu gösteren ekran görüntüsü":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>En düşük TLS sürümünü zorlamak için Azure Ilkesini kullanma

Azure Ilkesi, Azure kaynaklarının gereksinimlere ve standartlara bağlı olmasını sağlayarak bulut idare desteği sağlar. Kuruluşunuzdaki depolama hesapları için en düşük TLS sürümü gereksinimini zorlamak için, en düşük TLS gereksinimini ilke tarafından dikte edilen eski bir TLS sürümüne ayarlayan yeni bir depolama hesabı oluşturulmasını önleyen bir ilke oluşturabilirsiniz. Bu ilke, bu hesap için en düşük TLS sürümü ayarı ilkeyle uyumlu değilse, bu ilke, var olan bir hesapta tüm yapılandırma değişikliklerini de engeller.

Zorlama ilkesi, en düşük TLS sürümü kuruluşunuzun standartlarına uygun olmayacak şekilde bir depolama hesabı oluşturup değiştirecek bir isteği engellemek için reddetme efektini kullanır. Efektler hakkında daha fazla bilgi için bkz. [Azure ilke efektlerini anlama](../../governance/policy/concepts/effects.md).

TLS 1,2 ' den daha az bir TLS sürümüne yönelik reddetme etkisi olan bir ilke oluşturmak için, [uyumluluğu denetlemek Için Azure Ilkesi kullanma](#use-azure-policy-to-audit-for-compliance)bölümünde açıklanan adımları izleyin, ancak Ilke tanımının **policyrule** bölümünde aşağıdaki JSON 'yı sağlayın:

```json
{
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "not": {
            "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
            "equals": "TLS1_2"
          }
        }
      ]
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

İlkeyi reddetme etkimiyle oluşturup bir kapsama atadıktan sonra, Kullanıcı 1,2 'den eski olan en düşük TLS sürümüne sahip bir depolama hesabı oluşturamaz. Ya da bir Kullanıcı, mevcut bir depolama hesabında şu anda 1,2 'den eski olan en düşük TLS sürümünü gerektiren yapılandırma değişikliklerini yapabilir. Bunun denenmeye çalışılması bir hata ile sonuçlanır. Hesap oluşturma veya yapılandırmaya devam etmek için depolama hesabı için gereken en düşük TLS sürümü 1,2 olarak ayarlanmalıdır.

Aşağıdaki görüntüde, bir reddetme etkisi olan bir ilke en düşük TLS sürümünün TLS 1,2 olarak ayarlanmasını gerektirdiğinde, en düşük TLS sürümü TLS 1,0 (yeni bir hesap için varsayılan) olarak ayarlanmış bir depolama hesabı oluşturmaya çalıştığınızda oluşan hata gösterilmektedir.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="İlke ihlalinden bir depolama hesabı oluşturulurken oluşan hatayı gösteren ekran görüntüsü":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>En düşük TLS sürümünü gerektirmek için gereken izinler

Depolama hesabı için **Minimumtlsversion** özelliğini ayarlamak için, bir kullanıcının depolama hesapları oluşturma ve yönetme izinleri olması gerekir. Bu izinleri sağlayan Azure rol tabanlı erişim denetimi (Azure RBAC) rolleri, **Microsoft. Storage/storageAccounts/Write** veya **Microsoft. Storage/storageaccounts/ \*** Action ' i içerir. Bu eylemle birlikte yerleşik roller şunlardır:

- Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) rolü
- Azure Resource Manager [katkıda bulunan](../../role-based-access-control/built-in-roles.md#contributor) rolü
- [Depolama hesabı katılımcısı](../../role-based-access-control/built-in-roles.md#storage-account-contributor) rolü

Bu roller, Azure Active Directory (Azure AD) aracılığıyla bir depolama hesabındaki verilere erişim sağlamaz. Ancak, hesap erişim anahtarlarına erişim izni veren **Microsoft. Storage/storageAccounts/ListKeys/Action** öğeleri de bulunur. Bu izinle, bir Kullanıcı, bir depolama hesabındaki tüm verilere erişmek için hesap erişim anahtarlarını kullanabilir.

Rol atamaları, bir kullanıcının depolama hesabı için en düşük TLS sürümünü gerektirmesine izin vermek için depolama hesabının veya daha yüksek düzeyde kapsam içermelidir. Rol kapsamı hakkında daha fazla bilgi için bkz. [Azure RBAC kapsamını anlama](../../role-based-access-control/scope-overview.md).

Bu rollerin atamasını yalnızca bir depolama hesabı oluşturma veya özelliklerini güncelleştirme yeteneğine ihtiyaç duyan kullanıcılarla sınırlandırmamaya dikkat edin. Kullanıcıların görevlerini gerçekleştirmek için ihtiyacı olan en az izinlere sahip olduğundan emin olmak için en az ayrıcalık ilkesini kullanın. Azure RBAC ile erişimi yönetme hakkında daha fazla bilgi için bkz. [Azure RBAC Için en iyi uygulamalar](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Klasik abonelik yöneticisi rolleri hizmet yöneticisi ve Co-Administrator Azure Resource Manager [sahip](../../role-based-access-control/built-in-roles.md#owner) rolünün eşdeğerini içerir. **Sahip** rolü tüm eylemleri içerir, bu nedenle bu yönetici rollerinden birine sahip bir kullanıcı da depolama hesapları oluşturabilir ve yönetebilir. Daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="network-considerations"></a>Ağ konuları

İstemci, depolama hesabına bir istek gönderdiğinde, istemci herhangi bir isteği işlemeden önce depolama hesabının genel uç noktasıyla bir bağlantı kurar. Bağlantı kurulduktan sonra minimum TLS sürümü ayarı denetlenir. İstek, bu ayar tarafından belirtilenden daha eski bir TLS sürümü kullanıyorsa bağlantı başarılı olmaya devam eder, ancak istek sonunda başarısız olur. Azure depolama için genel uç noktalar hakkında daha fazla bilgi için bkz. [Kaynak URI sözdizimi](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax).

## <a name="next-steps"></a>Sonraki adımlar

- [İstemci uygulaması için Aktarım Katmanı Güvenliği 'ni (TLS) yapılandırma](transport-layer-security-configure-client-version.md)
- [BLOB depolama için güvenlik önerileri](../blobs/security-recommendations.md)
