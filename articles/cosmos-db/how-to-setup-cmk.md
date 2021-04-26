---
title: Azure Cosmos DB hesabınız için müşteri tarafından yönetilen anahtarları yapılandırın
description: Azure Cosmos DB hesabınız için müşteri tarafından yönetilen anahtarları Azure Key Vault ile yapılandırma hakkında bilgi edinin
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/01/2021
ms.author: thweiss
ms.openlocfilehash: 1b1fc0b51c1cd2a99ec97bec9f588699a893ceca
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222631"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Azure Key Vault ile Azure Cosmos hesabınız için müşteri tarafından yönetilen anahtarları yapılandırma
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos hesabınızda depolanan veriler, Microsoft tarafından yönetilen anahtarlarla otomatik olarak ve sorunsuz bir şekilde şifrelenir (**hizmet tarafından yönetilen anahtarlar**). İsteğe bağlı olarak, yönettiğiniz anahtarlarla ikinci bir şifreleme katmanı eklemeyi tercih edebilirsiniz (**müşteri tarafından yönetilen anahtarlar**).

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="Müşteri verileri etrafında şifreleme katmanları":::

Müşteri tarafından yönetilen anahtarları [Azure Key Vault](../key-vault/general/overview.md) depolamanız ve müşterinin yönettiği anahtarlarla etkinleştirilen her bir Azure Cosmos hesabı için bir anahtar sağlamanız gerekir. Bu anahtar, bu hesapta depolanan tüm verileri şifrelemek için kullanılır.

> [!NOTE]
> Şu anda, müşteri tarafından yönetilen anahtarlar yalnızca yeni Azure Cosmos hesapları için kullanılabilir. Hesap oluşturma sırasında bunları yapılandırmanız gerekir.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Azure aboneliğiniz için Azure Cosmos DB kaynak sağlayıcısını kaydetme

1. [Azure Portal](https://portal.azure.com/)oturum açın, Azure aboneliğinize gidin ve **Ayarlar** sekmesinde **kaynak sağlayıcıları** ' nı seçin:

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="Sol menüden kaynak sağlayıcıları girişi":::

1. **Microsoft.DocumentDB** kaynak sağlayıcısını arayın. Kaynak sağlayıcının zaten kayıtlı olarak işaretlendiğinden emin olun. Aksi takdirde, kaynak sağlayıcısını seçip **Kaydet**' i seçin:

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="Microsoft.DocumentDB kaynak sağlayıcısını kaydetme":::

## <a name="configure-your-azure-key-vault-instance"></a>Azure Key Vault örneğinizi yapılandırma

Azure Cosmos DB ile müşteri tarafından yönetilen anahtarların kullanılması, şifreleme anahtarlarınızı barındırmak için kullanmayı planladığınız Azure Key Vault örneğinde iki özellik ayarlamanızı gerektirir: **geçici silme** ve **Temizleme koruması**.

Yeni bir Azure Key Vault örneği oluşturursanız, oluşturma sırasında bu özellikleri etkinleştirin:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="Yeni bir Azure Key Vault örneği için geçici silme ve Temizleme korumasını etkinleştirme":::

Mevcut bir Azure Key Vault örneğini kullanıyorsanız, Azure portal **Özellikler** bölümüne bakarak bu özelliklerin etkinleştirildiğini doğrulayabilirsiniz. Bu özelliklerden herhangi biri etkinleştirilmemişse, aşağıdaki makalelerden birinde "geçici silme etkinleştiriliyor" ve "Temizleme korumasını etkinleştirme" bölümlerine bakın:

- [PowerShell ile geçici silmeyi kullanma](../key-vault/general/key-vault-recovery.md)
- [Azure CLı ile geçici silme kullanma](../key-vault/general/key-vault-recovery.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a><a id="add-access-policy"></a> Azure Key Vault örneğine bir erişim ilkesi ekleme

1. Azure portal, şifreleme anahtarlarınızı barındırmak için kullanmayı planladığınız Azure Key Vault örneğine gidin. Sol menüden **erişim ilkeleri** ' ni seçin:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="Sol menüden erişim ilkeleri":::

1. **+ Erişim Ilkesi Ekle**' yi seçin.

1. **Anahtar izinleri** aşağı açılan menüsünde **Al**, **sarmalama tuşu** ve **sarmalama anahtarı** izinlerini seçin:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="Doğru izinleri seçme":::

1. **Asıl seçin** altında **hiçbiri seçili**' i seçin.

1. **Azure Cosmos DB** sorumlusu arayıp seçin (bulmayı kolaylaştırmak için asıl kimliğe göre de arama yapın, `a232010e-820c-4083-83bb-3ace5fc29d0b` birincil kimliğin bulunduğu Azure Kamu bölgeleri dışında herhangi bir Azure bölgesi için `57506a73-e302-42a9-b869-6f12d9ec29e9` ). **Azure Cosmos DB** sorumlusu listede yoksa, bu makalenin [kaynak sağlayıcısını kaydetme](#register-resource-provider) bölümünde açıklandığı gibi **Microsoft.Documentdb** kaynak sağlayıcısını yeniden kaydetmeniz gerekebilir.

   > [!NOTE]
   > Bu, Azure Cosmos DB ilk taraf kimliği Azure Key Vault erişim ilkenize kaydeder. Bu birinci taraf kimliğini Azure Cosmos DB hesabı yönetilen Kimliğiniz ile değiştirmek için, bkz. [Azure Key Vault erişim ilkesinde yönetilen kimlik kullanma](#using-managed-identity).

1. En altta **Seç ' i** seçin. 

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="Azure Cosmos DB sorumlusu seçin":::

1. Yeni erişim ilkesini eklemek için **Ekle** ' yi seçin.

1. Tüm değişiklikleri kaydetmek için Key Vault örneğinde **Kaydet** ' i seçin.

## <a name="generate-a-key-in-azure-key-vault"></a>Azure Key Vault bir anahtar oluşturun

1. Azure portal, şifreleme anahtarlarınızı barındırmak için kullanmayı planladığınız Azure Key Vault örneğine gidin. Ardından, sol menüden **anahtarlar** ' ı seçin:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="Sol menüden anahtarlar girdisi":::

1. **Oluştur/al**' ı seçin, yeni anahtar için bir ad girin ve bir RSA anahtar boyutu seçin. En iyi güvenlik için en az 3072 önerilir. Sonra **Oluştur**' u seçin.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="Yeni anahtar oluştur":::

1. Anahtar oluşturulduktan sonra, yeni oluşturulan anahtarı ve ardından geçerli sürümünü seçin.

1. Son eğik çizgiden sonraki bölüm dışında anahtarın **anahtar tanımlayıcısını** kopyalayın:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="Anahtarın anahtar tanımlayıcısı kopyalanıyor":::

## <a name="create-a-new-azure-cosmos-account"></a>Yeni bir Azure Cosmos hesabı oluşturun

### <a name="using-the-azure-portal"></a>Azure portalını kullanma

Azure portal yeni bir Azure Cosmos DB hesabı oluşturduğunuzda, **şifreleme** adımında **müşteri tarafından yönetilen anahtar** ' ı seçin. **Anahtar URI 'si** alanında, önceki adımdan kopyaladığınız Azure Key Vault anahtarının URI/anahtar tanımlayıcısını yapıştırın:

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="Azure portal CMK parametrelerini ayarlama":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Azure PowerShell kullanma

PowerShell ile yeni bir Azure Cosmos DB hesabı oluşturduğunuzda:

- Daha önce, **Propertyobject** Içindeki **keyvaultkeyuri** özelliğinin ALTıNA kopyalanmış Azure Key Vault anahtarının URI 'sini geçirin.

- API sürümü olarak **2019-12-12** veya üstünü kullanın.

> [!IMPORTANT]
> `locations`Hesabı, müşteri tarafından yönetilen anahtarlarla başarıyla oluşturulacak şekilde, özelliği açıkça ayarlamanız gerekir.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

Hesap oluşturulduktan sonra, Azure Key Vault anahtarının URI 'sini getirerek müşteri tarafından yönetilen anahtarların etkinleştirildiğini doğrulayabilirsiniz:

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma

Azure Resource Manager şablonuyla yeni bir Azure Cosmos hesabı oluşturduğunuzda:

- Daha önce, **Properties** nesnesine **keyvaultkeyuri** özelliği altında kopyaladığınız Azure Key Vault anahtarının URI 'sini geçirin.

- API sürümü olarak **2019-12-12** veya üstünü kullanın.

> [!IMPORTANT]
> `locations`Hesabı, müşteri tarafından yönetilen anahtarlarla başarıyla oluşturulacak şekilde, özelliği açıkça ayarlamanız gerekir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

Şablonu aşağıdaki PowerShell betiği ile dağıtın:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Azure CLı 'yi kullanma

Azure CLı aracılığıyla yeni bir Azure Cosmos hesabı oluşturduğunuzda, daha önce parametresinin altında kopyaladığınız Azure Key Vault anahtarın URI 'sini geçirin `--key-uri` .

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

Hesap oluşturulduktan sonra, Azure Key Vault anahtarının URI 'sini getirerek müşteri tarafından yönetilen anahtarların etkinleştirildiğini doğrulayabilirsiniz:

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="using-a-managed-identity-in-the-azure-key-vault-access-policy"></a><a id="using-managed-identity"></a> Azure Key Vault erişim ilkesinde yönetilen bir kimlik kullanma

Bu erişim ilkesi, şifreleme anahtarlarınızın Azure Cosmos DB hesabınız tarafından erişilebilir olmasını sağlar. Bu, belirli bir Azure Active Directory (AD) kimliğine erişim verilerek yapılır. İki tür kimlik desteklenir:

- Azure Cosmos DB, Azure Cosmos DB hizmetine erişim vermek için ilk taraf kimliği kullanılabilir.
- Azure Cosmos DB hesabınızın [yönetilen kimliği](how-to-setup-managed-identity.md) , hesabınıza özel olarak erişim sağlamak için kullanılabilir.

Sistem tarafından atanan bir yönetilen kimlik yalnızca Hesabınız oluşturulduktan sonra alınabileceğinden, [yukarıda](#add-access-policy)açıklandığı gibi öncelikle ilk taraf kimliği kullanarak hesabınızı oluşturmanız gerekir. Ardından:

1. Bu işlem, hesap oluşturma sırasında yapılmadıysa, hesabınızda [sistem tarafından atanan bir yönetilen kimliği etkinleştirin](how-to-setup-managed-identity.md) ve atanan ' yi kopyalayın `principalId` .

1. [Yukarıda](#add-access-policy)açıklandığı gibi Azure Key Vault hesabınıza yeni bir erişim ilkesi ekleyin, ancak `principalId` Azure Cosmos DB birinci taraf kimliği yerine önceki adımda kopyaladığınız şekilde kullanın.

1. Azure Key Vault içindeki şifreleme Anahtarlarınıza erişirken sistem tarafından atanan yönetilen kimliği kullanmak istediğinizi belirtmek için Azure Cosmos DB Hesabınızı güncelleştirin. Bunu, hesabınızın Azure Resource Manager şablonunda bu özelliği belirterek yapabilirsiniz:

   ```json
   {
       "type": " Microsoft.DocumentDB/databaseAccounts",
       "properties": {
           "defaultIdentity": "SystemAssignedIdentity",
           // ...
       },
       // ...
   }
   ```

1. İsteğe bağlı olarak, Azure Key Vault erişim ilkenizde Azure Cosmos DB birinci taraf kimliğini kaldırabilirsiniz.

## <a name="key-rotation"></a>Anahtar döndürme

Azure Cosmos hesabınız tarafından kullanılan müşteri tarafından yönetilen anahtarı döndürmek, iki şekilde yapılabilir.

- Azure Key Vault Şu anda kullanılan anahtarın yeni bir sürümünü oluşturun:

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="Yeni bir anahtar sürümü oluştur":::

- Hesabınızdaki anahtar URI 'sini güncelleştirerek Şu anda tamamen farklı bir anahtarla kullanılan anahtarı değiştirin. Azure portal Azure Cosmos hesabınıza gidin ve sol menüden **veri şifrelemeyi** seçin:

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Veri şifreleme menü girdisi":::

    Ardından, **anahtar URI** 'sini kullanmak istediğiniz yeni anahtarla değiştirin ve **Kaydet**' i seçin:

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="Anahtar URI 'sini Güncelleştir":::

    PowerShell 'de aynı sonucu elde etmek için şu adımları uygulayın:

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

Önceki anahtar veya anahtar sürümü 24 saat sonra devre dışı bırakılabilir veya [Azure Key Vault denetim günlükleri](../key-vault/general/logging.md) artık bu anahtar veya anahtar sürümünde Azure Cosmos DB etkinliği göstermez.
    
## <a name="error-handling"></a>Hata işleme

Azure Cosmos DB Customer-Managed anahtarları (CMK) kullanırken, herhangi bir hata oluşursa Azure Cosmos DB, yanıtta bir HTTP alt durum kodu ile birlikte hata ayrıntılarını döndürür. Sorunun kök nedenini ayıklamak için bu alt durum kodunu kullanabilirsiniz. Desteklenen HTTP alt durum kodlarının listesini almak için [Azure Cosmos DB Için http durum kodları](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) makalesine bakın.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları etkinleştirmek için ek bir ücret var mı?

Hayır, bu özelliği etkinleştirme ücreti yok.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>Müşteri tarafından yönetilen anahtarlar Kapasite planlamasını nasıl etkiler?

Müşteri tarafından yönetilen anahtarlar kullanılırken, veritabanı işlemleriniz tarafından tüketilen [Istek birimleri](./request-units.md) , verilerinizin şifrelenmesini ve şifresinin çözülmesi için gereken ek işlemeyi yansıtan bir artış sağlar. Bu, sağlanan kapasitenizin biraz daha yüksek bir kullanımına neden olabilir. Rehberlik için aşağıdaki tabloyu kullanın:

| İşlem türü | İstek birimi artışı |
|---|---|
| Nokta-okuma (öğeleri kendi KIMLIĞINE göre getirme) | işlem başına %5% |
| Herhangi bir yazma işlemi | işlem başına %6%<br/>dizinli özellik başına yaklaşık ve + 0,06 RU |
| Sorgular, okuma ve değişiklik akışı veya çakışma akışı | işlem başına %15% |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlarla hangi veriler şifrelenir?

Azure Cosmos hesabınızda depolanan tüm veriler, aşağıdaki meta veriler dışında müşteri tarafından yönetilen anahtarlarla şifrelenir:

- Azure Cosmos DB [hesaplarınızın, veritabanlarının ve kapsayıcılarınızın](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account) adları

- [Saklı yordamlarınızın](./stored-procedures-triggers-udfs.md) adları

- [Dizin oluşturma ilkeleriniz](./index-policy.md) içinde belirtilen özellik yolları

- Kapsayıcılarınızın [bölüm anahtarlarının](./partitioning-overview.md) değerleri

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Mevcut Azure Cosmos hesapları için müşteri tarafından yönetilen anahtarlar destekleniyor mu?

Bu özellik şu anda yalnızca yeni hesaplar için kullanılabilir.

### <a name="is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB [analitik deposuyla](analytical-store-introduction.md)birlikte müşteri tarafından yönetilen anahtarların kullanılması mümkün mü?

Evet, ancak analitik depoyu etkinleştirmeden önce Azure Key Vault erişim ilkenizde [Azure Cosmos DB hesabının yönetilen kimliğini kullanmanız](#using-managed-identity) gerekir.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Hesap düzeyi anahtarlardan daha ince ayrıntı düzeyi desteklemeye yönelik bir plan var mı?

Şu anda değil, ancak kapsayıcı düzeyi anahtarlar kabul ediliyor.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Azure Cosmos hesabmda müşteri tarafından yönetilen anahtarların etkinleştirilip etkinleştirilmediğini nasıl anlayabilirim?

Azure portal Azure Cosmos hesabınıza gidin ve Sol menüdeki **veri şifreleme** girişini izleyin; Bu giriş varsa, hesabınızda müşteri tarafından yönetilen anahtarlar etkinleştirilir:

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Veri şifreleme menü girdisi":::

Ayrıca, Azure Cosmos hesabınızın ayrıntılarını programlı bir şekilde getirip özelliğin varlığını arayabilirsiniz `keyVaultKeyUri` . [PowerShell](#using-powershell) ve [Azure CLI 'yi kullanma](#using-azure-cli)yolları için yukarıdaki bölümüne bakın.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Müşteri tarafından yönetilen anahtarlar yedeklemeyi nasıl etkiler?

Azure Cosmos DB, hesabınızda depolanan verilerin [düzenli ve otomatik yedeklemelerini](./online-backup-and-restore.md) alır. Bu işlem şifrelenmiş verileri yedekler. Geri yüklenen yedeklemeyi kullanmak için, yedekleme sırasında kullandığınız şifreleme anahtarı gereklidir. Bu, hiçbir iptali yapılmadığı ve yedeklemenin yapıldığı sırada kullanılan anahtarın sürümünün etkinleştirilmeyeceği anlamına gelir.

### <a name="how-do-i-revoke-an-encryption-key"></a>Nasıl yaparım? bir şifreleme anahtarı iptal edilsin mi?

Anahtarın en son sürümü devre dışı bırakılarak anahtar iptali yapılır:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="Anahtarın sürümünü devre dışı bırakma":::

Alternatif olarak, bir Azure Key Vault örneğinden tüm anahtarları iptal etmek için, Azure Cosmos DB sorumlusuna verilen erişim ilkesini silebilirsiniz:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="Azure Cosmos DB sorumlusu için erişim ilkesi siliniyor":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Müşteri tarafından yönetilen anahtar iptal edildikten sonra hangi işlemler kullanılabilir?

Şifreleme anahtarı iptal edildiğinde mümkün olan tek işlem, hesap silme işlemidir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB 'de veri şifreleme](./database-encryption-at-rest.md)hakkında daha fazla bilgi edinin.
- [Cosmos DB verilerine güvenli erişim](secure-access-to-data.md)hakkında genel bakış alın.
