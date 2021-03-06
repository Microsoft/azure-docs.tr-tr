---
title: Azure Izleyici 'de çözüm Azure Key Vault | Microsoft Docs
description: Azure Key Vault günlüklerini gözden geçirmek için Azure Izleyici 'de Azure Key Vault çözümünü kullanabilirsiniz.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 24869841a106a68234cfec600d9a98e519f8c6cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734205"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Izleyici 'de Azure Key Vault Analytics çözümü

> [!NOTE]
> Bu çözüm kullanım dışıdır. [Artık Key Vault Için Azure izleyici 'yi kullanmanızı öneririz](./key-vault-insights-overview.md).

![Key Vault simgesi](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Key Vault AuditEvent günlüklerini gözden geçirmek için Azure Izleyici 'de Azure Key Vault çözümünü kullanabilirsiniz.

Çözümü kullanmak için Azure Key Vault tanılamayı günlüğe kaydetmeyi etkinleştirmeniz ve tanılamayı bir Log Analytics çalışma alanına yönlendirmelidir. Günlükleri Azure Blob depolamaya yazmak gerekli değildir.

> [!NOTE]
> 2017 Ocak 'ta, Key Vault günlüklerin Log Analytics değiştirme biçimi değiştirildi. Kullanmakta olduğunuz Key Vault çözümü başlığında *(kullanım dışı)* görünüyorsa, izlemeniz gereken adımlar için [eski Key Vault çözümünün geçişine](#migrating-from-the-old-key-vault-solution) bakın.
>
>

## <a name="install-and-configure-the-solution"></a>Çözümü yükleyip yapılandırma
Azure Key Vault çözümünü yüklemek ve yapılandırmak için aşağıdaki yönergeleri kullanın:

1. Azure Key Vault çözümünü Log Analytics çalışma alanınıza eklemek için [Çözüm Galerisi Azure izleyici çözümlerini ekleme](./solutions.md) bölümünde açıklanan işlemi kullanın.
2. [Portal](#enable-key-vault-diagnostics-in-the-portal) veya [PowerShell](#enable-key-vault-diagnostics-using-powershell) kullanarak izlemek üzere Key Vault kaynakları için tanılama günlüğünü etkinleştirme

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Portalda Key Vault tanılamayı etkinleştirme

1. Azure portal izlemek için Key Vault kaynağına gidin
2. *Tanılama ayarlarını* seçerek aşağıdaki sayfayı açın

   ![ContosoKVSCUS Key Vault kaynak için Tanılama Ayarları sayfasının ekran görüntüsü. tanılamayı açma seçeneği vurgulanır.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. *Tanılamayı* aç ' a tıklayarak aşağıdaki sayfayı açın

   ![Tanılama ayarlarını yapılandırma sayfasının ekran görüntüsü. Log Analytics, AuditEvent log ve Allölçümleri ' ne gönder seçenekleri seçilidir.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Tanılama ayarına bir ad verin.
5. *Log Analytics gönder* onay kutusuna tıklayın
6. Mevcut bir Log Analytics çalışma alanını seçin veya bir çalışma alanı oluşturun
7. *Auditevent* günlüklerini etkinleştirmek Için, günlük altındaki onay kutusuna tıklayın.
8. Tanılama 'nın Log Analytics çalışma alanına kaydedilmesini sağlamak için *Kaydet* ' e tıklayın.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>PowerShell kullanarak Key Vault tanılamayı etkinleştirme
Aşağıdaki PowerShell betiği, `Set-AzDiagnosticSetting` Key Vault için kaynak günlüğünü etkinleştirmek üzere nasıl kullanılacağına ilişkin bir örnek sağlar:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Azure Key Vault veri toplama ayrıntılarını gözden geçirin
Azure Key Vault çözüm tanılama günlüklerini doğrudan Key Vault toplar.
Günlükleri Azure Blob depolama alanına yazmak gerekli değildir ve veri toplama için hiçbir aracı gerekmez.

Aşağıdaki tabloda, verilerin Azure Key Vault için nasıl toplandığı hakkında veri toplama yöntemleri ve diğer ayrıntılar gösterilmektedir.

| Platform | Doğrudan aracı | Systems Center Operations Manager Aracısı | Azure | Operations Manager gerekli mi? | Yönetim grubu aracılığıyla gönderilen aracı verileri Operations Manager | Toplama sıklığı |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | varış noktasında |

## <a name="use-azure-key-vault"></a>Azure Key Vault kullanma
[Çözümü yükledikten](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?source=intercept.nl&tab=Overview)sonra, Azure Izleyici **genel bakış** sayfasında **Key Vault Analytics** kutucuğuna tıklayarak Key Vault verileri görüntüleyin. **Öngörüler** bölümünün altında **daha fazla** ' ya tıklayarak bu sayfayı **Azure izleyici** menüsünden açın. 

![Zamana göre Anahtar Kasası işlem hacmi grafiğini gösteren Azure Izleyici Genel Bakış sayfasında Key Vault Analytics kutucuğunun ekran görüntüsü.](media/azure-key-vault/log-analytics-keyvault-tile.png)

**Key Vault Analytics** kutucuğuna tıkladıktan sonra, günlüklerinizin özetlerini görüntüleyebilir ve ardından aşağıdaki kategorilerde Ayrıntılar için ayrıntıya gidebilirsiniz:

* Zaman içinde tüm Anahtar Kasası işlemlerinin hacmi
* Zaman içinde başarısız işlem birimleri
* İşleme göre ortalama işletimsel gecikme süresi
* 1000 'den fazla alan işlem sayısı ve 1000 ms 'den fazla işlem yapan işlemler için hizmet kalitesi

![Tüm Işlemler, başarısız Işlemler ve ortalama Işlem gecikmesi için grafik verileriyle kutucukları gösteren Azure Key Vault panonun ekran görüntüsü.](media/azure-key-vault/log-analytics-keyvault01.png)

![Ortalama Işletimsel gecikme süresi, hizmet kalitesi ve önerilen aramalar için verileri içeren kutucukları gösteren Azure Key Vault panonun ekran görüntüsü.](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Herhangi bir işlemin ayrıntılarını görüntülemek için
1. **Genel bakış** sayfasında **Key Vault Analytics** kutucuğuna tıklayın.
2. **Azure Key Vault** panosunda, dikey pencerelerden birindeki Özet bilgilerini gözden geçirin ve günlük araması sayfasında onunla ilgili ayrıntılı bilgileri görüntülemek için bir tane tıklatın.

    Günlük arama sayfalarında, sonuçları zamana, ayrıntılı sonuçlara ve günlük arama geçmişinize göre görüntüleyebilirsiniz. Ayrıca, sonuçları daraltmak için de modellerle filtre uygulayabilirsiniz.

## <a name="azure-monitor-log-records"></a>Azure Izleyici günlük kayıtları
Azure Key Vault çözümü, Azure Tanılama içindeki [auditevent günlüklerinden](../../key-vault/general/logging.md) toplanan bir tür **Anahtar Kasası** olan kayıtları analiz eder.  Bu kayıtların özellikleri aşağıdaki tabloda verilmiştir:  

| Özellik | Açıklama |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |İsteği yapan istemcinin IP adresi |
| `Category` | *AuditEvent* |
| `CorrelationId` |İstemci tarafı günlüklerini hizmet tarafı (Anahtar Kasası) günlükleriyle ilişkilendirmek için istemcinin geçirebileceği isteğe bağlı bir GUID. |
| `DurationMs` |Milisaniye cinsinden REST API'si isteğini sunmak için geçen süre. Bu süre ağ gecikmesini içermez, bu nedenle istemci tarafında ölçmeniz gereken süre bu süre ile eşleşmeyebilir. |
| `httpStatusCode_d` |İstek tarafından döndürülen HTTP durum kodu (örneğin, *200*) |
| `id_s` |İsteğin benzersiz KIMLIĞI |
| `identity_claim_appid_g` | Uygulama KIMLIĞI için GUID |
| `OperationName` |[Azure Key Vault günlük kaydı](../../key-vault/general/logging.md) bölümünde belgelendiği şekilde işlemin adı |
| `OperationVersion` |İstemci tarafından istenen REST API sürümü (örneğin *2015-06-01*) |
| `requestUri_s` |İsteğin URI 'si |
| `Resource` |Anahtar kasasının adı |
| `ResourceGroup` |Anahtar kasasının kaynak grubu |
| `ResourceId` |Azure Resource Manager Kaynak Kimliği. Key Vault günlükleri için, bu Key Vault kaynak KIMLIĞIDIR. |
| `ResourceProvider` |*MICROSOFT. KEYVAULT* |
| `ResourceType` | *KASALARı* |
| `ResultSignature` |HTTP durumu (örneğin, *Tamam*) |
| `ResultType` |REST API isteğin sonucu (örneğin, *başarılı*) |
| `SubscriptionId` |Key Vault içeren aboneliğin Azure abonelik KIMLIĞI |

## <a name="migrating-from-the-old-key-vault-solution"></a>Eski Key Vault çözümünden geçiş
2017 Ocak 'ta, Key Vault günlüklerin Log Analytics değiştirme biçimi değiştirildi. Bu değişiklikler aşağıdaki avantajları sağlar:
+ Günlükler, bir depolama hesabı kullanılmasına gerek olmadan doğrudan bir Log Analytics çalışma alanına yazılır
+ Günlüklerin oluşturulduğu zamandan daha az gecikme süresi Log Analytics ' de kullanılabilir
+ Daha az yapılandırma adımı
+ Tüm Azure tanılama türleri için ortak bir biçim

Güncelleştirilmiş çözümü kullanmak için:

1. [Tanılamayı doğrudan Key Vault bir Log Analytics çalışma alanına gönderilmek üzere yapılandırın](#enable-key-vault-diagnostics-in-the-portal)  
2. [Çözüm Galerisi Azure izleyici çözümlerini ekleme](./solutions.md) bölümünde açıklanan işlemi kullanarak Azure Key Vault çözümü etkinleştirin
3. Yeni veri türünü kullanmak için kaydedilen sorguları, panoları veya uyarıları güncelleştirme
   + Tür şu şekilde değişir: KeyVaults to AzureDiagnostics. Günlük Key Vault filtrelemek için ResourceType öğesini kullanabilirsiniz.
   + Yerine `KeyVaults` şunu kullanın: `AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Alanlar: (alan adları büyük/küçük harfe duyarlıdır)
   + \_Adında s, d veya g sonekine sahip olan her alan için \_ \_ , ilk karakteri küçük harf olarak değiştirin
   + Adında o soneki olan herhangi bir alan için \_ , veriler iç içe geçmiş alan adlarına göre tek tek alanlara bölünür. Örneğin, çağıranın UPN 'si bir alanda saklanır `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Alan Callerıpaddress, Callerıpaddress olarak değiştirildi
   + RemoteIPCountry alanı artık yok
4. *Key Vault Analytics (kullanım dışı)* çözümünü kaldırın. PowerShell kullanıyorsanız, şunu kullanın `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Değişiklik, yeni çözümde görüntülenmeden önce toplanan veriler görünmez. Eski tür ve alan adlarını kullanarak bu verileri sorgulamaya devam edebilirsiniz.

## <a name="troubleshooting"></a>Sorun giderme
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Sonraki adımlar
* Ayrıntılı Azure Key Vault verilerini görüntülemek için [Azure izleyici 'de günlük sorgularını](../logs/log-query-overview.md) kullanın.

