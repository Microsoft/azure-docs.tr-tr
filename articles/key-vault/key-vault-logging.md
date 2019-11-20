---
title: Günlüğe kaydetme Azure Key Vault | Microsoft Docs
description: Azure Anahtar Kasası günlüğü ile çalışmaya başlamada yardım almak için bu öğreticiyi kullanın.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 997651887c3c378e4791553d5ff05f585ad169ea
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000660"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault günlüğü

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bir veya daha fazla Anahtar Kasası oluşturduktan sonra muhtemelen anahtar kasalarınızın nasıl ve ne zaman erişildiğini ve kim tarafından yapılacağını izlemek isteyeceksiniz. Bu, bilgileri, sağladığınız bir Azure depolama hesabına kaydeden Azure Key Vault için günlüğe kaydetmeyi etkinleştirerek yapabilirsiniz. Belirtilen depolama hesabınız için **Öngörüler-logs-auditevent** adlı yeni bir kapsayıcı otomatik olarak oluşturulur. Birden çok Anahtar Kasası için günlükleri toplamak üzere bu depolama hesabını kullanabilirsiniz.

Kayıt bilgilerinizi 10 dakika (en çok) Anahtar Kasası işleminden sonra erişebilirsiniz. Çoğu durumda, bundan daha hızlı olacaktır.  Depolama hesabınızdaki günlüklerinizi yönetmek size bağlıdır:

* Günlüklerinize erişebilecek kişileri kısıtlayarak güvenliklerini sağlamak için standart Azure erişim denetimi yöntemlerini kullanın.
* Artık depolama hesabınızda tutmak istemediğiniz günlükleri silin.

Azure Anahtar Kasası günlüğü ile çalışmaya başlamada yardım almak için bu öğreticiyi kullanın. Bir depolama hesabı oluşturacak, günlüğe kaydetmeyi etkinleştireceksiniz ve toplanan günlük bilgilerini yorumlayacak.  

> [!NOTE]
> Bu öğretici anahtar kasalarının, anahtarların veya gizli anahtarların nasıl oluşturulacağı hakkında yönergeler içermez. Bu bilgi için bkz. [Azure Key Vault nedir?](key-vault-overview.md). Veya platformlar arası Azure CLı yönergeleri için [Bu eşdeğer öğreticiye](key-vault-manage-with-cli2.md)bakın.
>
> Bu makalede, tanılama günlüğünü güncelleştirmek için Azure PowerShell yönergeler sağlanmaktadır. Tanılama günlük kaydını, Azure portal **tanılama günlükleri** bölümünde Azure izleyici 'yi kullanarak da güncelleştirebilirsiniz. 
>

Key Vault hakkında genel bilgi için bkz. [Azure Key Vault nedir?](key-vault-overview.md). Key Vault nerede kullanılabildiği hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/key-vault/)bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakilere sahip olmanız gerekir:

* Kullanmakta olduğunuz var olan bir anahtar kasası.  
* En düşük 1.0.0 sürümü Azure PowerShell. Azure PowerShell'i yüklemek ve Azure aboneliğinizle ilişkilendirmek için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview). Azure PowerShell zaten yüklediyseniz ve sürümü bilmiyorsanız, Azure PowerShell konsolundan yazın `$PSVersionTable.PSVersion`.  
* Anahtar Kasası günlükleriniz için Azure'da yeterli depolama.

## <a id="connect"></a>Anahtar Kasası aboneliğinize bağlanma

Anahtar günlüğü ayarlamanın ilk adımı, günlüğe kaydetmek istediğiniz anahtar kasasından Azure PowerShell işaret ediyor.

Bir Azure PowerShell oturumu başlatın ve aşağıdaki komutu kullanarak Azure hesabınızda oturum açın:  

```powershell
Connect-AzAccount
```

Açılır tarayıcı penceresinde Azure hesabı kullanıcı adınızı ve parolanızı girin. Azure PowerShell, bu hesapla ilişkili tüm abonelikleri alır. Varsayılan olarak, PowerShell ilk olanı kullanır.

Anahtar kasanızı oluşturmak için kullandığınız aboneliği belirtmeniz gerekebilir. Hesabınıza yönelik abonelikleri görmek için aşağıdaki komutu girin:

```powershell
Get-AzSubscription
```

Daha sonra, günlük kaydı yaptığınız Anahtar Kasası ile ilişkili aboneliği belirtmek için şunu girin:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Özellikle hesabınızla ilişkili birden fazla aboneliğiniz varsa, PowerShell 'in doğru aboneliğe işaret edilmesi önemli bir adımdır. Azure Power Shell'i yapılandırma hakkında daha fazla bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview).

## <a id="storage"></a>Günlüklerinizi için bir depolama hesabı oluşturun

Günlüklerinizin mevcut bir depolama hesabını kullanabilseniz de, Key Vault günlüklerine ayrılmayacak bir depolama hesabı oluşturacağız. Daha sonra bunu belirtmemiz gereken durumlarda, ayrıntıları **sa**adlı bir değişkende depolayacağız.

Daha fazla yönetim kolaylığı için, anahtar kasasını içeren kaynakla aynı kaynak grubunu da kullanacağız. [Başlangıç öğreticisinde](key-vault-get-started.md), bu kaynak grubuna **contosoresourcegroup**adı verilir ve Doğu Asya konumunu kullanmaya devam edeceğiz. Bu değerleri, uygun olduğu şekilde kendi değerlerinizle değiştirin:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Mevcut bir depolama hesabını kullanmaya karar verirseniz, anahtar kasanız ile aynı aboneliği kullanması gerekir. Ve klasik dağıtım modeli yerine Azure Resource Manager dağıtım modelini kullanmalıdır.
>
>

## <a id="identify"></a>Günlükleriniz için anahtar kasasını tanımlama

[Başlangıç öğreticisinde](key-vault-get-started.md), Anahtar Kasası adı **contosokeykasaiydi**. Bu adı kullanmaya devam edeceğiz ve ayrıntıları **kV**adlı bir değişkende depolarız:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Günlüğe kaydetmeyi etkinleştirme

Key Vault için günlüğe kaydetmeyi etkinleştirmek üzere, **set-AzDiagnosticSetting** cmdlet 'ini yeni depolama hesabı ve Anahtar Kasası için oluşturduğumuz değişkenlerle birlikte kullanacağız. Ayrıca, **-Enabled** bayrağını **$true** ve kategoriyi **auditevent** (Key Vault günlük için tek kategori) olarak ayarlayacağız:

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Çıktı şöyle görünür:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Bu çıktı, günlük kaydının anahtar kasanız için etkin olduğunu onaylar ve depolama hesabınıza bilgi kaydeder.

İsteğe bağlı olarak, günlüklerinizi için eski günlüklerin otomatik olarak silineceği bir bekletme ilkesi ayarlayabilirsiniz. Örneğin, **-RetentionEnabled** bayrağını **$true**olarak ayarlayarak bekletme ilkesi ayarlayın ve 90 günden eski günlüklerin otomatik olarak silinmesi için **-retentionındays** parametresini **90** olarak ayarlayın.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Günlüğe kaydedilenler:

* Erişim izinlerinin, sistem hatalarının veya hatalı isteklerin sonucu olarak başarısız istekler dahil tüm kimliği doğrulanmış REST API istekleri.
* Oluşturma, silme, Anahtar Kasası erişim ilkelerini ayarlama ve Etiketler gibi Anahtar Kasası özniteliklerini güncelleştirme dahil olmak üzere anahtar kasasındaki işlemler.
* Anahtar kasasındaki anahtarlar ve gizli diziler için aşağıdakiler de dahil olmak üzere işlemler:
  * Bu anahtarları veya parolaları oluşturma, değiştirme veya silme.
  * Anahtarları imzalama, doğrulama, şifreleme, şifre çözme, sarmalama ve kaldırma, gizli dizileri alma ve anahtarları ve gizli dizileri (ve bunların sürümlerini) listeleme.
* Bir 401 yanıtına neden olan kimliği doğrulanmamış istekler. Örnek olarak, hatalı biçimlendirilmiş veya geçerliliği olmayan ya da geçersiz bir belirtece sahip bir taşıyıcı belirteci olmayan isteklerdir.  

## <a id="access"></a>Günlüklerinize erişme

Key Vault Günlükler, verdiğiniz depolama hesabındaki **Öngörüler-logs-auditevent** kapsayıcısında depolanır. Günlükleri görüntülemek için Blobları indirmeniz gerekir.

İlk olarak, kapsayıcı adı için bir değişken oluşturun. Bu değişkeni, izlenecek yolun geri kalanı boyunca kullanacaksınız.

```powershell
$container = 'insights-logs-auditevent'
```

Bu kapsayıcıdaki tüm Blobları listelemek için şunu girin:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Çıkış şuna benzer:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Bu Çıkışta gördüğünüz gibi bloblar bir adlandırma kuralını izler: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Tarih ve saat değerleri UTC'yi kullanır.

Birden çok kaynağa yönelik günlükleri toplamak için aynı depolama hesabını kullanabilmeniz için, blob adı içindeki tam kaynak KIMLIĞI, yalnızca ihtiyaç duyduğunuz bloblara erişmek veya onları indirmek için yararlıdır. Ancak bunu yapmadan önce tüm blobların nasıl indirileceğini ele alacağız.

Blobları indirmek için bir klasör oluşturun. Örneğin:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Ardından tüm blobların listesini alın:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Blob 'ları hedef klasöre indirmek için **Get-AzStorageBlobContent** aracılığıyla bu listeyi kanal halinde kullanın:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Bu ikinci komutu çalıştırdığınızda **/** blob adlarındaki sınırlayıcısı hedef klasörün altında tam klasör yapısı oluşturur. Bu yapıyı blob 'ları dosya olarak indirmek ve depolamak için kullanacaksınız.

Blobları seçmeli olarak indirmek için jokerleri kullanın. Örneğin:

* Birden çok anahtar kasanız varsa ve yalnızca CONTOSOKEYVAULT3 adlı bir anahtar kasası için günlük indirmek isterseniz:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Birden çok kaynak grubunuz varsa ve yalnızca bir kaynak grubu için günlük indirmek isterseniz `-Blob '*/RESOURCEGROUPS/<resource group name>/*'` kullanın:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 2019 Ocak ayının tüm günlüklerini indirmek istiyorsanız şunu kullanın `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Artık günlüklerin içinde neler olduğuna bakmaya başlamak için hazırsınız. Ancak, bu konuda geçiş yapmadan önce, daha fazla iki komut bilmeniz gerekir:

* Anahtar kasası kaynağınızın tanılama ayarlarının durumunu sorgulamak için: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Anahtar kasası kaynağınızın günlüğe kaydetmesini devre dışı bırakmak için: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Anahtar Kasası günlüklerinizi yorumlama

Tek tek bloblar JSON blobu olarak biçimlendirilip metin olarak depolanır. Bir örnek günlük girişine bakalım. Şu komutu çalıştırın:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Şuna benzer bir günlük girdisi döndürür:

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

Aşağıdaki tabloda alan adları ve açıklamaları listelenmektedir:

| Alan adı | Açıklama |
| --- | --- |
| **saat** |UTC olarak tarih ve saat. |
| **resourceId** |Azure Resource Manager kaynak kimliği. Key Vault günlükleri için, her zaman Key Vault kaynak KIMLIĞI olur. |
| **OperationName** |Sonraki tabloda belirtildiği gibi işlemin adı. |
| **operationVersion** |İstemci tarafından istenen sürümü REST API. |
| **Kategori** |Sonuç türü. Key Vault günlükleri için, **auditevent** tek ve kullanılabilir bir değerdir. |
| **'ı** |REST API isteğinin sonucu. |
| **resultSignature** |HTTP durumu. |
| **resultDescription** |Kullanılabilir olduğunda sonuç hakkında ek açıklama. |
| **Ort** |Milisaniye cinsinden REST API'si isteğini sunmak için geçen süre. Buna ağ gecikme süresi dahil değildir; bu nedenle istemci tarafında ölçtüğünüz süre bu süreyle eşleşmeyebilir. |
| **Callerıpaddress** |İsteği yapan istemcinin IP adresi. |
| **ID** |İstemci tarafı günlüklerini hizmet tarafı (Anahtar Kasası) günlükleriyle ilişkilendirmek için istemcinin geçirebileceği isteğe bağlı bir GUID. |
| **IDENTITY** |REST API isteğinde sunulan belirteçten kimlik. Bu, genellikle bir "Kullanıcı," bir "hizmet sorumlusu" veya "Kullanıcı + AppID" birleşimidir ve bir Azure PowerShell cmdlet 'inin sonucu olan bir istekte bulunur. |
| **Özellikleri** |İşleme göre farklılık gösteren bilgiler (**OperationName**). Çoğu durumda bu alan istemci bilgilerini (istemci tarafından geçirilen kullanıcı aracısı dizesi), tam REST API istek URI 'sini ve HTTP durum kodunu içerir. Ayrıca, bir nesne bir isteğin sonucu olarak döndürüldüğünde (örneğin, **Keycreate** veya **vaultget**), anahtar URI 'sini ("kimlik" olarak), kasa URI 'sini veya gizli URI 'yi de içerir. |

**OperationName** alan değerleri *objectverb* biçimindedir. Örneğin:

* Tüm Anahtar Kasası işlemleri `Vault<action>` , `VaultGet` ve `VaultCreate`gibi biçimdedir.
* Tüm anahtar işlemleri `Key<action>` , `KeySign` ve `KeyList`gibi biçimdedir.
* Tüm gizli işlemler `Secret<action>` , `SecretGet` ve `SecretListVersions`gibi biçimdedir.

Aşağıdaki tabloda, **OperationName** değerleri ve karşılık gelen REST API komutları listelenmektedir:

| operationName | REST API komutu |
| --- | --- |
| **Kimlik Doğrulaması** |Azure Active Directory uç noktası aracılığıyla kimlik doğrulaması |
| **VaultGet** |[Bir anahtar kasası hakkında bilgi edinme](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Anahtar kasası oluşturma veya güncelleştirme](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Anahtar kasası silme](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Bir anahtar kasasını güncelleştirme](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Bir kaynak grubundaki tüm anahtar kasalarını listeleme](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Anahtar oluşturma](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Bir anahtar hakkında bilgi edinme](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **Keyımport** |[Bir kasaya anahtar aktarma](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **Anahtar yedekleme** |[Anahtar yedekleme](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Anahtar silme](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Bir anahtarı geri yükleme](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Bir anahtar ile oturum açma](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Bir anahtar ile doğrulama](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Bir anahtarı sarmalama](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **Tuş kaydırmayı geri al** |[Bir anahtarı kaydırma](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Bir anahtar ile şifreleme](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **Keyşifre çözme** |[Bir anahtar ile şifre çözme](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Bir anahtarı güncelleştirme](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Bir kasadaki anahtarları listeleme](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Bir anahtarın sürümlerini listeleme](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Gizli anahtar oluşturma](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Gizli dizi alın](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Gizli anahtarı güncelleştirme](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Gizli anahtarı silme](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **Secrelıst** |[Bir kasadaki gizli anahtarları listeleme](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Bir gizli anahtarın sürümlerini listeleme](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Azure Izleyici günlüklerini kullanma

Key Vault **auditevent** günlüklerini gözden geçirmek Için Azure izleyici günlüklerinde Key Vault çözümünü kullanabilirsiniz. Azure Izleyici günlüklerinde, verileri analiz etmek ve ihtiyacınız olan bilgileri almak için günlük sorgularını kullanırsınız. 

Bunu ayarlama dahil daha fazla bilgi için bkz. [Azure izleyici günlüklerinde Azure Key Vault çözümü](../azure-monitor/insights/azure-key-vault.md). Bu makalede, Azure Izleyici günlükleri önizlemesi sırasında sunulan eski Key Vault çözümünden geçiş yapmanız gerekiyorsa, günlüklerinizi bir Azure depolama hesabına ilk kez yönlendirmekte ve Azure Izleyici günlüklerini buradan okumak üzere yapılandırmış olursunuz.

## <a id="next"></a>Sonraki adımlar

.NET Web uygulamasında Azure Key Vault kullanan bir öğretici için bkz. [bir Web uygulamasından Azure Key Vault kullanma](tutorial-net-create-vault-azure-web-app.md).

Programlama başvuruları için bkz. [Azure Anahtar Kasası geliştirici kılavuzu](key-vault-developers-guide.md).

Azure Key Vault için Azure PowerShell 1,0 cmdlet 'lerinin bir listesi için bkz. [Azure Key Vault cmdlet 'leri](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Azure Key Vault ile anahtar döndürme ve günlük denetimi hakkında bir öğretici için bkz. [uçtan uca anahtar döndürme ve denetleme ile Key Vault ayarlama](key-vault-key-rotation-log-monitoring.md).
