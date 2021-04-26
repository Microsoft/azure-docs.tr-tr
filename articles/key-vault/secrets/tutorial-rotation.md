---
title: Azure Key Vault depolanan bir kimlik doğrulama kimlik bilgileri kümesi olan kaynaklar için döndürme öğreticisi
description: Bir kimlik doğrulama kimlik bilgileri kümesi kullanan kaynaklar için gizli dizi döndürmeyi nasıl otomatikleştirebileceğinizi öğrenmek için bu öğreticiyi kullanın.
services: key-vault
author: msmbaldwin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: e66be3b0e3ecae5caa1a76294cc8b8dc11a5f207
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748673"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Bir kimlik doğrulama kimlik bilgileri kümesi kullanan kaynaklar için gizli dizi döndürmeyi otomatikleştirin

Azure hizmetlerinde kimlik doğrulaması yapmanın en iyi yolu [yönetilen bir kimlik](../general/authentication.md)kullanmaktır, ancak bir seçenek olmadığı durumlarda bazı senaryolar vardır. Bu durumlarda, erişim anahtarları veya gizli dizileri kullanılır. Erişim anahtarlarını veya gizli dizileri düzenli olarak döndürmelisiniz.

Bu öğreticide, tek bir kimlik doğrulama kimlik bilgileri kümesi kullanan veritabanları ve hizmetler için düzenli aralıklarla yapılan gizli dizi döndürmenin nasıl otomatikleştirdiği gösterilmektedir. Özellikle, bu öğretici Azure Event Grid bildirimi tarafından tetiklenen bir işlev kullanılarak Azure Key Vault depolanan SQL Server parolalarını döndürür:


:::image type="content" source="../media/rotate-1.png" alt-text="Döndürme çözümünün diyagramı":::

1. Bir parolanın süre sonu tarihinden otuz gün önce, Key Vault Event Grid "sona erme" olayını yayınlar.
1. Event Grid olay aboneliklerini denetler ve olaya abone olan işlev uygulama uç noktasını çağırmak için HTTP POST kullanır.
1. İşlev uygulaması gizli bilgileri alır, yeni bir rastgele parola oluşturur ve Key Vault yeni parolayla gizli dizi için yeni bir sürüm oluşturur.
1. İşlev uygulaması güncelleştirmeleri yeni parolayla SQL Server.

> [!NOTE]
> 3 ve 4. adımlar arasında bir gecikme olabilir. Bu süre boyunca Key Vault gizli dizi SQL Server kimlik doğrulaması yapamaz. Adımların hiçbirinde hata olması durumunda iki saat boyunca yeniden denemeler Event Grid.

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Key Vault
* SQL Server

Mevcut Key Vault ve SQL Server yoksa dağıtım bağlantısı aşağıdaki şekilde kullanılabilir:

[!["Azure 'a dağıt" etiketli bir düğmeyi gösteren resim.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. **Kaynak grubu** altında **Yeni oluştur**' u seçin. Gruba bir ad verin, bu öğreticide **akvrotation** kullanıyoruz.
1. **SQL Yöneticisi oturum açma** bölümünde SQL yönetici oturum açma adı yazın. 
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin

:::image type="content" source="../media/rotate-2.png" alt-text="Kaynak grubu oluşturma":::

Artık Key Vault ve SQL Server bir örnek vardır. Aşağıdaki komutu çalıştırarak bu kurulumu Azure CLı 'da doğrulayabilirsiniz:

```azurecli
az resource list -o table -g akvrotation
```

Sonuç aşağıdaki çıktıyı görür:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation      eastus      Microsoft.Sql/servers/databases
akvrotation-sql2         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql2/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>SQL Server parola döndürme işlevi oluşturma ve dağıtma
> [!IMPORTANT]
> Aşağıdaki şablon Key Vault, SQL Server ve Azure Işlevinin aynı kaynak grubunda olmasını gerektirir

Sonra, sistem tarafından yönetilen kimliğe sahip bir işlev uygulaması oluşturun ve diğer gerekli bileşenlere ek olarak SQL Server parola döndürme işlevlerini dağıtın

İşlev uygulaması bu bileşenleri gerektirir:
- Azure App Service planı
- Olay tetikleyicisi ve http tetikleyicisi ile SQL parola döndürme işlevleri ile İşlev Uygulaması 
- İşlev uygulaması tetikleyici yönetimi için gerekli bir depolama hesabı
- Key Vault gizli dizileri erişmek için İşlev Uygulaması kimlik erişimi ilkesi
- **Secretyaklaştığında süre sonu** olayı Için eventgrid olay aboneliği

1. Azure şablonu dağıtım bağlantısını seçin: 

   [!["Azure 'a dağıt" etiketli bir düğmeyi gösteren resim.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. **Kaynak grubu** listesinde, **akvdönüşü**' ni seçin.
1. **SQL Server adı**' nda, döndürmek için parola Ile SQL Server adını yazın
1. **Key Vault adına**, Anahtar Kasası adını yazın
1. **İşlev uygulaması adına**, işlev uygulama adını yazın
1. Gizli dizi **adında** parolanın depolanacağı gizli adı yazın
1. **Depo URL 'si** içinde, Işlev kodu GitHub Location () yazın **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp.git**
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

:::image type="content" source="../media/rotate-3.png" alt-text="Gözden geçir + oluştur ' u seçin":::
  

Yukarıdaki adımları tamamladıktan sonra bir depolama hesabınız, sunucu grubunuz ve bir işlev uygulamanız olacaktır. Aşağıdaki komutu çalıştırarak bu kurulumu Azure CLı 'da doğrulayabilirsiniz:

```azurecli
az resource list -o table -g akvrotation
```

Sonuç aşağıdaki çıktıya benzer bir şekilde görünecektir:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

Bir işlev uygulaması oluşturma ve Key Vault erişmek için yönetilen kimlik kullanma hakkında bilgi için, bkz. [Azure Portal bir işlev uygulaması oluşturma](../../azure-functions/functions-create-function-app-portal.md), [App Service ve Azure işlevleri için yönetilen kimlik kullanma](../../app-service/overview-managed-identity.md)ve [Azure Portal kullanarak bir Key Vault erişim ilkesi atama](../general/assign-access-policy-portal.md).

### <a name="rotation-function"></a>Döndürme işlevi
Önceki adımda dağıtılan işlevi, Key Vault ve SQL veritabanını güncelleştirerek bir gizli dizi dönüşü tetiklemek için bir olay kullanır. 

#### <a name="function-trigger-event"></a>İşlev tetikleyici olayı

Bu işlev olay verilerini okur ve döndürme mantığını çalıştırır:

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>Gizli anahtar döndürme mantığı
Bu döndürme yöntemi, gizli dizi veritabanı bilgilerini okur, gizliliğin yeni bir sürümünü oluşturur ve veritabanını yeni gizli anahtar ile güncelleştirir:

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
Tüm kodu [GitHub](https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp)' da bulabilirsiniz.

## <a name="add-the-secret-to-key-vault"></a>Gizli dizi Key Vault ekleyin
Kullanıcılara *gizli dizi yönetme* izinleri vermek için erişim ilkenizi ayarlayın:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Gizli dizi için SQL Server kaynak KIMLIĞI, SQL Server oturum açma adı ve geçerlilik süresi içeren etiketlerle yeni bir gizli dizi oluşturun. SQL veritabanından gizli, başlangıç parolasının adını sağlayın (örneğimizde "Simple123") ve yarın için ayarlanmış bir sona erme tarihi ekleyin.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

Kısa süre sonu tarihi ile bir gizli dizi oluşturmak `SecretNearExpiry` , 15 dakika içinde bir olay yayımlar, bu da işlevi parolayı döndürmek için tetikler.

## <a name="test-and-verify"></a>Test ve doğrulama

Gizli dizinin döndürülmeyeceğini doğrulamak için **Key Vault**  >  **gizli** dizi sayfasına gidin:

:::image type="content" source="../media/rotate-8.png" alt-text="Key Vault > gizliliklerin nasıl erişebileceğini gösteren ekran görüntüsü.":::

**SQLPassword** gizli anahtarını açın ve özgün ve döndürülmüş sürümleri görüntüleyin:

:::image type="content" source="../media/rotate-9.png" alt-text="Gizli anahtarlara git":::

### <a name="create-a-web-app"></a>Web uygulaması oluşturma

SQL kimlik bilgilerini doğrulamak için bir Web uygulaması oluşturun. Bu Web uygulaması Key Vault parolayı alacak, SQL veritabanı bilgilerini ve kimlik bilgilerini gizli listeden ayıklayarak SQL Server bağlantısını test edecektir.

Web uygulaması şu bileşenleri gerektirir:
- Sistem tarafından yönetilen kimliğe sahip bir Web uygulaması
- Web uygulaması yönetilen kimliği aracılığıyla Key Vault parolalara erişim için erişim ilkesi

1. Azure şablonu dağıtım bağlantısını seçin: 

   [!["Azure 'a dağıt" etiketli bir düğmeyi gösteren resim.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmain%2FARM-Templates%2FWeb-App%2Fazuredeploy.json)

1. **Akvrotation** kaynak grubunu seçin.
1. **SQL Server adı**' nda, döndürmek için parola Ile SQL Server adını yazın
1. **Key Vault adına**, Anahtar Kasası adını yazın
1. Gizli dizi **adında** parolanın depolandığı gizli adı yazın
1. **Depo URL**'sinde, Web uygulaması kodu GitHub konumu ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** ) yazın
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.


### <a name="open-the-web-app"></a>Web uygulamasını açın

Dağıtılan uygulama URL 'sine gidin:
 
'https://akvrotation-app.azurewebsites.net/'

Uygulama tarayıcıda açıldığında, **oluşturulan gizli değeri** ve bir **veritabanı bağlı** değeri *true* olarak görürsünüz.

## <a name="learn-more"></a>Daha fazla bilgi edinin

- Öğretici: [iki kimlik bilgileri kümesi olan kaynaklar Için döndürme](tutorial-rotation-dual.md)
- Genel Bakış: [Azure Event Grid Key Vault izleme](../general/event-grid-overview.md)
- Nasıl yapılır: [Anahtar Kasası gizli anahtarı değiştiğinde e-posta alma](../general/event-grid-logicapps.md)
- [Azure Key Vault için Azure Event Grid olay şeması](../../event-grid/event-schema-key-vault.md)
