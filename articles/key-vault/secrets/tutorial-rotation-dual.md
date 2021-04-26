---
title: İki kimlik bilgileri kümesi olan kaynaklar için döndürme öğreticisi
description: İki kimlik doğrulama kimlik doğrulaması kümesini kullanan kaynaklar için gizli dizi döndürmeyi nasıl otomatikleştirebileceğinizi öğrenmek için bu öğreticiyi kullanın.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 1f656a41b0f447b90f58ec14173e418a2defb72e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484838"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>İki kimlik doğrulama kimlik doğrulaması kümesine sahip kaynaklar için gizli dizi döndürmeyi otomatikleştirin

Azure hizmetlerinde kimlik doğrulaması yapmanın en iyi yolu [yönetilen bir kimlik](../general/authentication.md)kullanmaktır, ancak bir seçenek olmadığı durumlarda bazı senaryolar vardır. Bu durumlarda, erişim tuşları veya parolalar kullanılır. Erişim anahtarlarını ve parolaları sıklıkla döndürmelisiniz.

Bu öğreticide, iki kimlik doğrulama kimlik doğrulaması kümesini kullanan veritabanları ve hizmetler için düzenli aralıklarla yapılan gizli dizi döndürmenin nasıl otomatikleştirdiği gösterilmektedir. Özellikle bu öğreticide, Azure Key Vault 'de depolanan Azure depolama hesabı anahtarlarının gizli dizi olarak nasıl döndürüleceğini gösterilmektedir. Azure Event Grid bildirimi tarafından tetiklenen bir işlev kullanacaksınız. 

> [!NOTE]
> Depolama hesabına atanan erişim için paylaşılan erişim imzası belirteçleri sağlarsanız, depolama hesabı anahtarları Key Vault otomatik olarak yönetilebilir. Erişim anahtarlarına sahip depolama hesabı bağlantı dizeleri gerektiren hizmetler vardır. Bu senaryo için bu çözümü öneririz.

Bu öğreticide açıklanan döndürme çözümü aşağıda verilmiştir: 

![Döndürme çözümünü gösteren diyagram.](../media/secrets/rotation-dual/rotation-diagram.png)

Bu çözümde Azure Key Vault, depolama hesabı ayrı erişim anahtarlarını aynı gizli dizinin sürümleri olarak depolar ve sonraki sürümlerde birincil ve ikincil anahtar arasında geçiş yapın. Bir erişim anahtarı gizli dizinin en son sürümünde depolandığında, alternatif anahtar yeniden oluşturulur ve parolanın yeni en son sürümü olarak Key Vault eklenir. Çözüm, en yeni yeniden üretilen anahtara yenilemek için uygulamanın tüm döndürme döngüsünü sağlar. 

1. Bir parolanın süre sonu tarihinden otuz gün önce, Key Vault Event Grid için yakın süre sonu olayını yayınlar.
1. Event Grid olay aboneliklerini denetler ve olaya abone olan işlev uygulama uç noktasını çağırmak için HTTP POST kullanır.
1. İşlev uygulaması, alternatif anahtarı tanımlar (en son olanı değil) ve depolama hesabını yeniden oluşturmak için çağırır.
1. İşlev uygulaması, yeni bir yeniden oluşturulan anahtarı yeni bir parola sürümü olarak Azure Key Vault ekler.

## <a name="prerequisites"></a>Önkoşullar
* Azure aboneliği. [Ücretsiz bir tane oluşturun.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure [Cloud Shell](https://shell.azure.com/). Bu öğretici, PowerShell env ile portal Cloud Shell kullanıyor
* Azure Key Vault.
* İki Azure depolama hesabı.

Mevcut bir anahtar kasası ve mevcut depolama hesaplarınız yoksa, bu dağıtım bağlantısını kullanabilirsiniz:

[![Azure 'a etiketlenmiş bir dağıtım bağlantısı.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. **Kaynak grubu** altında **Yeni oluştur**' u seçin. Gruba **vatasyon** adını ve ardından **Tamam**' ı seçin.
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

    ![Kaynak grubu oluşturmayı gösteren ekran görüntüsü.](../media/secrets/rotation-dual/dual-rotation-1.png)

Artık bir anahtar kasası ve iki depolama hesabınız olacak. Bu kurulumu Azure CLı 'da veya Azure PowerShell, bu komutu çalıştırarak doğrulayabilirsiniz:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az resource list -o table -g vaultrotation
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzResource -Name 'vaultrotation*' | Format-Table
```
---

Sonuç şu çıkışa benzer şekilde görünecektir:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Anahtar döndürme işlevini oluşturma ve dağıtma

Daha sonra, sistem tarafından yönetilen kimliğe sahip bir işlev uygulamasını, diğer gerekli bileşenlere ek olarak oluşturacaksınız. Ayrıca, depolama hesabı anahtarları için döndürme işlevini de dağıtacaksınız.

İşlev uygulaması döndürme işlevi aşağıdaki bileşenleri ve yapılandırmayı gerektirir:
- Azure App Service planı
- İşlev uygulaması tetikleyicilerini yönetmek için bir depolama hesabı
- Key Vault 'de gizli anahtarlara erişim için bir erişim ilkesi
- Depolama hesabı erişim anahtarlarına erişebilmeleri için işlev uygulamasına atanan depolama hesabı anahtar Işletmeni hizmeti rolü
- Olay tetikleyicisi ve HTTP tetikleyicisi (isteğe bağlı döndürme) ile bir anahtar döndürme işlevi
- **Secretyaklaştığında süre sonu** olayı için Event Grid olay aboneliği

1. Azure şablonu dağıtım bağlantısını seçin: 

   [![Azure şablonu dağıtım bağlantısı.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. **Kaynak grubu** listesinde, **vaultrotation**' ı seçin.
1. **Depolama HESABı RG** kutusunda, depolama hesabınızın bulunduğu kaynak grubunun adını girin. Depolama hesabınız, anahtar döndürme işlevini dağıtacağınız kaynak grubunda zaten bulunuyorsa, **[resourceGroup (). Name]** varsayılan değerini koruyun.
1. **Depolama hesabı adı** kutusuna, döndürülecek erişim anahtarlarını içeren depolama hesabının adını girin. [Ön](#prerequisites)koşullarda oluşturulan depolama hesabı kullanıyorsanız, varsayılan değeri **[Concat (resourceGroup (). Name, ' Storage ')]** tutun.
1. **Key Vault RG** kutusuna anahtar kasanızın bulunduğu kaynak grubunun adını girin. Anahtar kasanızın anahtar döndürme işlevini dağıtacağınız kaynak grubunda zaten mevcut olması durumunda **[resourceGroup (). Name]** varsayılan değerini koruyun.
1. **Key Vault adı** kutusuna anahtar kasasının adını girin. [Ön](#prerequisites)koşullarda oluşturulmuş Anahtar Kasası kullanıyorsanız, varsayılan değeri **[Concat (resourceGroup (). Name, '-kV ')]** tutun.
1. **App Service plan türü** kutusunda barındırma planı ' nı seçin. **Premium planı** yalnızca anahtar kasanızın güvenlik duvarının arkasında olduğu durumlarda gereklidir.
1. **İşlev uygulaması adı** kutusuna, işlev uygulamasının adını girin.
1. Gizli dizi **adı** kutusuna, erişim anahtarlarını depoladığınız parolanın adını girin.
1. **Depo URL 'si** kutusuna, Işlev kodunun GitHub konumunu girin. Bu öğreticide ' i kullanabilirsiniz **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

   ![İşlevin nasıl oluşturulacağını ve dağıtılacağını gösteren ekran görüntüsü.](../media/secrets/rotation-dual/dual-rotation-2.png)

Yukarıdaki adımları tamamladıktan sonra bir depolama hesabınız, bir sunucu grubu, bir işlev uygulaması ve Application Insights olacaktır. Dağıtım tamamlandığında şu sayfayı görürsünüz:

   ![Dağıtımınızın tamamlanmış sayfasını gösteren ekran görüntüsü.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Bir hatayla karşılaşırsanız, bileşenlerin dağıtımını sona bırakmak için yeniden **Dağıt** ' ı seçebilirsiniz.


[Azure örnekleri](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell)'nde döndürme işlevi için dağıtım şablonlarını ve kodu bulabilirsiniz.

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Depolama hesabı erişim anahtarlarını Key Vault ekleyin

İlk olarak, Kullanıcı sorumlunuz için **gizli dizi yönetme** izinleri vermek üzere erişim ilkenizi ayarlayın:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -UserPrincipalName <email-address-of-user> --name vaultrotation-kv -PermissionsToSecrets set,delete,get,list
```
---

Artık değeri olarak depolama hesabı erişim anahtarı ile yeni bir gizli dizi oluşturabilirsiniz. Ayrıca, döndürme işlevinin depolama hesabındaki anahtarı yeniden oluşturabilmesi için, depolama hesabı kaynak KIMLIĞI, gizli geçerlilik süresi ve anahtar KIMLIĞI ' ne ihtiyacınız olacak.

Depolama hesabı kaynak KIMLIĞINI belirleme. Bu değeri `id` özelliğinde bulabilirsiniz.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Anahtar değerlerini alabilmeniz için depolama hesabı erişim anahtarlarını listeleyin:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Süre sonu tarihi yarın, 60 gün ve depolama hesabı kaynak kimliği için geçerlilik süresi olan Anahtar Kasası 'na gizli dizi ekleyin. Ve için elde edilen değerleri kullanarak bu komutu çalıştırın `key1Value` `storageAccountResourceId` :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key1'
    ProviderAddress='<storageAccountResourceId>'
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Yukarıdaki Gizlilik, `SecretNearExpiry` birkaç dakika içinde olayı tetikler. Bu olay, sona erme tarihi 60 gün olarak ayarlanan gizli anahtarı döndürmek için işlevi tetikler. Bu yapılandırmada, ' Secretbir süre sonu ' olayı 30 günde bir tetiklenecek (süre sonu 30 gün) ve döndürme işlevi KEY1 ile key2 arasında bir döndürme işlemi olacaktır.

Depolama hesabı anahtarını ve Key Vault gizliliğini alarak ve bunları karşılaştırarak erişim anahtarlarının yeniden oluşturulduğunu doğrulayabilirsiniz.

Gizli bilgileri almak için bu komutu kullanın:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey -AsPlainText
```
---

`CredentialId`Alternatif olarak güncelleştirilmiş ve yeniden oluşturulan dikkat edin `keyName` `value` :

![İlk depolama hesabı için z keykasası gizli gösterme komutunun çıkışını gösteren ekran görüntüsü.](../media/secrets/rotation-dual/dual-rotation-4.png)

Değerleri karşılaştırmak için erişim tuşlarını alın:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Anahtarın `value` anahtar kasasındaki gizli anahtarla aynı olduğuna dikkat edin:

![İlk depolama hesabı için z depolama hesabı anahtarları listesi komutunun çıkışını gösteren ekran görüntüsü.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Döndürme için depolama hesapları ekleme

Aynı işlev uygulamasını, birden fazla depolama hesabına yönelik anahtarları döndürmek için yeniden kullanabilirsiniz. 

Döndürme için mevcut bir işleve depolama hesabı anahtarları eklemek için şunlar gerekir:
- Depolama hesabı erişim anahtarlarına erişebilmek için işlev uygulamasına atanan depolama hesabı anahtar Işletmeni hizmeti rolü.
- **Secretyaklaştığında süre sonu** olayı için Event Grid bir olay aboneliği.

1. Azure şablonu dağıtım bağlantısını seçin: 

   [![Azure şablonu dağıtım bağlantısı.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. **Kaynak grubu** listesinde, **vaultrotation**' ı seçin.
1. **Depolama HESABı RG** kutusunda, depolama hesabınızın bulunduğu kaynak grubunun adını girin. Depolama hesabınız, anahtar döndürme işlevini dağıtacağınız kaynak grubunda zaten bulunuyorsa, **[resourceGroup (). Name]** varsayılan değerini koruyun.
1. **Depolama hesabı adı** kutusuna, döndürülecek erişim anahtarlarını içeren depolama hesabının adını girin.
1. **Key Vault RG** kutusuna anahtar kasanızın bulunduğu kaynak grubunun adını girin. Anahtar kasanızın anahtar döndürme işlevini dağıtacağınız kaynak grubunda zaten mevcut olması durumunda **[resourceGroup (). Name]** varsayılan değerini koruyun.
1. **Key Vault adı** kutusuna anahtar kasasının adını girin.
1. **İşlev uygulaması adı** kutusuna, işlev uygulamasının adını girin.
1. Gizli dizi **adı** kutusuna, erişim anahtarlarını depoladığınız parolanın adını girin.
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

   ![Ek depolama hesabı oluşturmayı gösteren ekran görüntüsü.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Key Vault başka bir depolama hesabı erişim anahtarı ekleyin

Depolama hesabı kaynak KIMLIĞINI belirleme. Bu değeri `id` özelliğinde bulabilirsiniz.
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Depolama hesabı erişim anahtarlarını, key2 değerini alabilmeniz için listeleyin:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage2 -ResourceGroupName vaultrotation
```
---

Süre sonu tarihi yarın, 60 gün ve depolama hesabı kaynak kimliği için geçerlilik süresi olan Anahtar Kasası 'na gizli dizi ekleyin. Ve için elde edilen değerleri kullanarak bu komutu çalıştırın `key2Value` `storageAccountResourceId` :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key2';
    ProviderAddress='<storageAccountResourceId>';
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey2 -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Gizli bilgileri almak için bu komutu kullanın:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey2 -AsPlainText
```
---

`CredentialId`Alternatif olarak güncelleştirilmiş ve yeniden oluşturulan dikkat edin `keyName` `value` :

![İkinci depolama hesabı için z keykasası gizli gösterme komutunun çıkışını gösteren ekran görüntüsü.](../media/secrets/rotation-dual/dual-rotation-8.png)

Değerleri karşılaştırmak için erişim tuşlarını alın:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Anahtarın `value` anahtar kasasındaki gizli anahtarla aynı olduğuna dikkat edin:

![İkinci depolama hesabı için z depolama hesabı anahtarları listesi komutunun çıkışını gösteren ekran görüntüsü.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>İki kimlik bilgileri kümesi için döndürme işlevlerini Key Vault

İki kimlik bilgileri kümesi ve birkaç kullanım işlevi için döndürme işlevleri şablonu:

- [Proje şablonu](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Redis Cache](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [Depolama hesabı](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> Yukarıdaki döndürme işlevleri, Microsoft tarafından değil, topluluğun bir üyesi tarafından oluşturulur. Topluluk Azure Işlevleri, herhangi bir Microsoft destek programı veya hizmeti kapsamında desteklenmez ve herhangi bir garanti olmaksızın olduğu gıbı kullanılabilir hale getirilir.

## <a name="next-steps"></a>Sonraki adımlar

- Öğretici: [bir kimlik bilgileri kümesi Için gizli dizi döndürme](./tutorial-rotation.md)
- Genel Bakış: [Azure Event Grid Key Vault izleme](../general/event-grid-overview.md)
- Nasıl yapılır: [Azure Portal ilk işlevinizi oluşturma](../../azure-functions/functions-get-started.md)
- Nasıl yapılır: [Key Vault gizli dizi değiştiğinde e-posta alma](../general/event-grid-logicapps.md)
- Başvuru: [Azure Key Vault için Azure Event Grid olay şeması](../../event-grid/event-schema-key-vault.md)
