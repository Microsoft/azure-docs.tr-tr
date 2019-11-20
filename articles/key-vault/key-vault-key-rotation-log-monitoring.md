---
title: Uçtan uca anahtar döndürme ve denetleme ile Azure Key Vault ayarlama | Microsoft Docs
description: Anahtar dönüşü ayarlamanıza ve Anahtar Kasası günlüklerini izlemenize yardımcı olması için bu nasıl yapılır kılavuzunu kullanın.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 1f60ce3a23882a48e6008b76c0eedcab99e013b2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883456"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Anahtar döndürme ve denetimle Azure Key Vault ayarlama

## <a name="introduction"></a>Giriş

Anahtar kasasından sonra anahtarları ve gizli dizileri depolamak için kullanmaya başlayabilirsiniz. Uygulamalarınızın artık anahtarlarınızı veya gizli dizileri kalıcı hale getirmek zorunda kalmaz, ancak gerektiğinde bunları kasadan talep edebilir. Anahtar Kasası, uygulamanızın davranışını etkilemeden anahtarları ve gizli dizileri güncelleştirmenize olanak tanır. Bu, anahtar ve gizli yönetibilmeniz için bir dizi olasılıktan oluşur.

>[!IMPORTANT]
> Bu makaledeki örnekler yalnızca gösterim amacıyla verilmiştir. Bunlar, üretim kullanımı için tasarlanmamıştır. 

Bu makalede izlenecek yol:

- Gizli dizi depolamak için Azure Key Vault kullanılmasına bir örnek. Bu makalede, depolanan gizli dizi, bir uygulama tarafından erişilen Azure depolama hesabı anahtarıdır. 
- Bu depolama hesabı anahtarının zamanlanan bir döndürmesini uygulama.
- Anahtar Kasası denetim günlüklerini izleme ve beklenmeyen istekler yapıldığında uyarı oluşturma.

> [!NOTE]
> Bu makalede, anahtar kasanızın ilk kurulumu ayrıntılı olarak açıklanmaz. Bu bilgi için bkz. [Azure Key Vault nedir?](key-vault-overview.md). Platformlar arası komut satırı arabirimi yönergeleri için bkz. [Azure CLI kullanarak Key Vault yönetme](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Anahtar Kasası ayarlama

Bir uygulamanın Key Vault bir gizli dizi almasına izin vermek için, öncelikle gizli anahtarı oluşturmanız ve bunu kasanıza yüklemeniz gerekir.

Bir Azure PowerShell oturumu başlatın ve aşağıdaki komutla Azure hesabınızda oturum açın:

```powershell
Connect-AzAccount
```

Açılır tarayıcı penceresinde, Azure hesabınızın kullanıcı adını ve parolasını girin. PowerShell, bu hesapla ilişkili tüm abonelikleri alır. PowerShell, varsayılan olarak ilk olanı kullanır.

Birden çok aboneliğiniz varsa, anahtar kasanızı oluşturmak için kullanılan bir tane belirtmeniz gerekebilir. Hesabınıza yönelik abonelikleri görmek için aşağıdakileri girin:

```powershell
Get-AzSubscription
```

Günlüğe kaydetmek istediğiniz anahtar kasası ile ilişkili aboneliği belirtmek için şunu girin:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Bu makalede bir depolama hesabı anahtarının gizli olarak depolanması gösterilmektedir. bu depolama hesabı anahtarını almanız gerekir.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Gizli anahtarı aldıktan sonra (Bu durumda, depolama hesabı anahtarınız), bu anahtarı güvenli bir dizeye dönüştürmeniz ve sonra Anahtar Kasanızda bu değere sahip bir gizli dizi oluşturmanız gerekir.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Ardından, oluşturduğunuz gizli dizi için URI 'yi alın. Anahtar kasasını çağırmak ve gizli anahtarı almak için bu URI 'nin sonraki bir adımda olması gerekir. Aşağıdaki PowerShell komutunu çalıştırın ve gizli dizi URI 'SI olan KIMLIK değerini unutmayın:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Uygulamayı ayarlama

Gizli bir gizli dizi olduğuna göre, birkaç adım daha gerçekleştirdikten sonra bu kodu almak ve kullanmak için kodu kullanabilirsiniz.

İlk olarak, Azure Active Directory uygulamanızı kaydetmeniz gerekir. Daha sonra uygulama bilgilerinizi Key Vault söyleyin. böylece uygulamanızdaki isteklere izin verebilir.

> [!NOTE]
> Uygulamanız, anahtar kasanız ile aynı Azure Active Directory kiracısında oluşturulmalıdır.

1. **Azure Active Directory**açın.
2. **Uygulama kayıtları**'nı seçin. 
3. Azure Active Directory bir uygulama eklemek için **Yeni uygulama kaydı** ' nı seçin.

    ![Uygulamaları Azure Active Directory açın](./media/keyvault-keyrotation/azure-ad-application.png)

4. **Oluştur**altında uygulama türünü **Web uygulaması/API** olarak bırakın ve uygulamanıza bir ad verin. Uygulamanıza bir **oturum açma URL 'si**verin. Bu URL, bu tanıtım için istediğiniz herhangi bir şey olabilir.

    ![Uygulama kaydı oluştur](./media/keyvault-keyrotation/create-app.png)

5. Uygulama Azure Active Directory eklendikten sonra uygulama sayfası açılır. **Ayarlar**' ı seçin ve ardından **Özellikler**' i seçin. **Uygulama kimliği** değerini kopyalayın. Daha sonraki adımlarda bu gereklidir.

Daha sonra, Azure Active Directory etkileşime girebilmesi için uygulamanız için bir anahtar oluşturun. Anahtar oluşturmak için **Ayarlar**altında **anahtarlar** ' ı seçin. Azure Active Directory uygulamanız için yeni oluşturulan anahtarı unutmayın. Daha sonraki bir adımda gerekecektir. Bu bölümden ayrıldığınızda anahtar kullanılamaz. 

![Azure Active Directory uygulama anahtarları](./media/keyvault-keyrotation/create-key.png)

Uygulamanızdan anahtar kasasına çağrı yapmadan önce, uygulamanız ve izinleri hakkında anahtar kasasını bildirmeniz gerekir. Aşağıdaki komut, uygulamanın anahtar kasanıza **erişmesini sağlamak için Azure Active Directory uygulamanızdaki kasa** adını ve uygulama kimliğini kullanır.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Artık uygulama çağrılarınızı oluşturmaya başlamaya hazırsınız. Uygulamanızda, Azure Key Vault ve Azure Active Directory etkileşimde bulunmak için gereken NuGet paketlerini yüklemelisiniz. Visual Studio paket yöneticisi konsolundan aşağıdaki komutları girin. Bu makalenin yazıldığı Azure Active Directory paketinin güncel sürümü 3.10.305231913, bu nedenle en son sürümü onaylayın ve gerektiği şekilde güncelleştirin.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Uygulama kodunuzda, Azure Active Directory kimlik doğrulaması için yöntemi tutacak bir sınıf oluşturun. Bu örnekte, bu sınıf **yardımcı programlar**olarak adlandırılır. Aşağıdaki `using` ifadeyi ekleyin:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Sonra, Azure Active Directory JWT belirtecini almak için aşağıdaki yöntemi ekleyin. Bakım için, sabit kodlanmış dize değerlerini Web veya uygulama yapılandırmanıza taşımak isteyebilirsiniz.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Key Vault çağırmak ve gizli değeri almak için gerekli kodu ekleyin. İlk olarak, aşağıdaki `using` ifadeyi eklemeniz gerekir:

```csharp
using Microsoft.Azure.KeyVault;
```

Key Vault çağırmak ve gizli dizinizi almak için yöntem çağrılarını ekleyin. Bu yöntemde, önceki bir adımda kaydettiğiniz gizli URI 'yi sağlarsınız. Daha önce oluşturduğunuz **yardımcı programlar** sınıfından **GetToken** yönteminin kullanımını göz önünde bulabilirsiniz.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Uygulamanızı çalıştırdığınızda, artık Azure Active Directory kimlik doğrulaması yapılmalıdır ve sonra Azure Key Vault gizli değeri alınıyor.

## <a name="key-rotation-using-azure-automation"></a>Azure Otomasyonu 'Nu kullanarak anahtar döndürme

> [!IMPORTANT]
> Azure Otomasyonu runbook 'ları hâlâ `AzureRM` modülün kullanılmasını gerektirir.

Artık Key Vault gizli dizileri olarak depoladığınız değerler için bir döndürme stratejisi ayarlamaya hazırsınız. Gizli dizileri çeşitli yollarla döndürülebilir:

- El ile gerçekleştirilen bir işlemin parçası olarak
- API çağrılarını kullanarak programlı olarak
- Bir Azure Otomasyonu betiği aracılığıyla

Bu makalenin amaçları doğrultusunda, Azure depolama hesabının erişim anahtarını değiştirmek için PowerShell 'i Azure Otomasyonu ile birlikte kullanacaksınız. Daha sonra bu yeni anahtarla bir Anahtar Kasası parolasını güncelleştireceksiniz.

Azure Otomasyonu 'nun Anahtar Kasanızda gizli değerler değiştirmesine izin vermek için **Azurerunasconnection**adlı BAĞLANTıNıN istemci kimliğini almanız gerekir. Bu bağlantı, Azure Otomasyonu örneğinizi oluştururken oluşturulmuştur. Bu KIMLIĞI bulmak için Azure Otomasyonu örneğinden **varlıklar** ' ı seçin. Buradan **Bağlantılar**' ı seçin ve ardından **Azurerunasconnection** hizmet sorumlusu ' nı seçin. **ApplicationId** değerini unutmayın.

![Azure Otomasyonu istemci KIMLIĞI](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

**Varlıklar**' da **modüller**' i seçin. **Galeri**' yi seçin ve ardından aşağıdaki modüllerden her birinin güncelleştirilmiş sürümlerini arayın ve içeri aktarın:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Bu makalenin yazıldığı sırada, aşağıdaki komut dosyası için yalnızca önceden belirtilen modüllerin güncellenmesi gerekir. Otomasyon işiniz başarısız olursa, gerekli tüm modülleri ve bunların bağımlılıklarını içeri aktardığınızı onaylayın.

Azure Otomasyonu bağlantınız için uygulama KIMLIĞINI aldıktan sonra, anahtar kasanıza bu uygulamanın kasadaki gizli dizileri güncelleştirme izni olduğunu söylemeniz gerekir. Aşağıdaki PowerShell komutunu kullanın:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Ardından, Azure Automation örneğinizin altındaki **runbook 'ları** seçin ve ardından **runbook Ekle**' yi seçin. **Hızlı Oluştur**’u seçin. Runbook 'unuzu adlandırın ve Runbook türü olarak **PowerShell** ' i seçin. Bir açıklama ekleyebilirsiniz. Son olarak **Oluştur**' u seçin.

![Runbook oluşturma](./media/keyvault-keyrotation/Create_Runbook.png)

Aşağıdaki PowerShell betiğini yeni runbook 'larınızın düzenleyici bölmesine yapıştırın:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Komut dosyanızı test etmek için Düzenleyici bölmesinde **Test bölmesi** ' ni seçin. Betik hatasız çalıştıktan sonra **Yayımla**' yı seçebilir ve Runbook yapılandırması bölmesinde runbook için bir zamanlama uygulayabilirsiniz.

## <a name="key-vault-auditing-pipeline"></a>Key Vault denetim işlem hattı

Bir Anahtar Kasası ayarladığınızda, anahtar kasasında yapılan erişim isteklerindeki günlükleri toplamak için denetimi açabilirsiniz. Bu Günlükler, belirlenen bir Azure depolama hesabında depolanır ve kullanıma alınabilir, izlenebilir ve analiz edilebilir. Aşağıdaki senaryo, Web uygulamasının uygulama KIMLIĞIYLE eşleşmeyen bir uygulama, kasadan gizli dizileri aldığında bir e-posta gönderen bir işlem hattı oluşturmak için Azure işlevleri, Azure Logic Apps ve Anahtar Kasası denetim günlüklerini kullanır.

İlk olarak, Anahtar Kasanızda günlüğü etkinleştirmeniz gerekir. Aşağıdaki PowerShell komutlarını kullanın. ( [Anahtar Kasası günlüğü hakkında bu makaledeki](key-vault-logging.md)tüm ayrıntıları görebilirsiniz.)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Günlüğe kaydetme etkinleştirildikten sonra, denetim günlükleri belirlenen depolama hesabında depolanmaya başlar. Bu günlüklerde, anahtar kasalarınızın nasıl ve ne zaman erişildiği ve kim tarafından kullanıldığı hakkında olaylar bulunur.

> [!NOTE]
> Kayıt bilgilerinizi, Anahtar Kasası işleminden sonraki 10 dakika sonra erişebilirsiniz. Genellikle daha önce kullanılabilir olacaktır.

Sonraki adım [Azure Service Bus kuyruğu oluşturmaktır](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Bu kuyruk, Anahtar Kasası denetim günlüklerinin gönderildiği yerdir. Denetim günlüğü iletileri kuyrukta olduğunda, mantıksal uygulama bunları kullanır ve üzerinde davranır. Aşağıdaki adımlarla bir Service Bus örneği oluşturun:

1. Service Bus ad alanı oluşturun (kullanmak istediğiniz bir tane varsa, 2. adıma atlayın).
2. Azure portal Service Bus örneğine gidin ve kuyruğu oluşturmak istediğiniz ad alanını seçin.
3.  > **Service Bus** **kurumsal tümleştirme**kaynakoluştur'useçinveardındangerekliayrıntıları > girin.
4. Ad alanını seçip **bağlantı bilgileri**' ni seçerek Service Bus bağlantı bilgilerini bulun. Sonraki bölümde bu bilgilere ihtiyacınız olacaktır.

Daha sonra, depolama hesabındaki Anahtar Kasası günlüklerini yoklamak ve yeni olayları almak için [bir Azure işlevi oluşturun](../azure-functions/functions-create-first-azure-function.md) . Bu işlev bir zamanlamaya göre tetiklenecektir.

Bir Azure işlev uygulaması oluşturmak için **kaynak oluştur**' u seçin, market 'te **işlev uygulaması**arayın ve ardından **Oluştur**' u seçin. Oluşturma sırasında, mevcut bir barındırma planını kullanabilir veya yeni bir tane oluşturabilirsiniz. Dinamik barındırmayı da tercih edebilirsiniz. Azure Işlevleri barındırma seçenekleri hakkında daha fazla bilgi için bkz. [Azure işlevlerini ölçeklendirme](../azure-functions/functions-scale.md).

Azure işlevi uygulaması oluşturulduktan sonra, bu uygulamaya gidin ve dil için **Zamanlayıcı** senaryosunu ve **C\#**  'yi seçin. Sonra **Bu Işlevi oluştur**' u seçin.

![Azure Işlevleri başlangıç dikey penceresi](./media/keyvault-keyrotation/Azure_Functions_Start.png)

**Geliştir** sekmesinde Run. CSX kodunu aşağıdaki kodla değiştirin:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Yukarıdaki koddaki değişkenleri, Anahtar Kasası günlüklerinin yazıldığı depolama hesabınıza, daha önce oluşturduğunuz Service Bus örneğine ve Anahtar Kasası depolama günlüklerinin özel yoluna işaret etmek üzere değiştirin.

İşlevi, Anahtar Kasası günlüklerinin yazıldığı, bu dosyanın en son olaylarını izleyen ve bunları bir Service Bus kuyruğuna gönderen depolama hesabından en son günlük dosyasını seçer. 

Tek bir dosya birden çok olaya sahip olabileceğinden, çalıştırılan son olayın zaman damgasını tespit etmek üzere işlevin de göründüğü bir Sync. txt dosyası oluşturmanız gerekir. Bu dosyanın kullanılması, aynı olayı birden çok kez göndermemenizi sağlar. 

Sync. txt dosyası, son karşılaşılan olayla ilgili bir zaman damgası içerir. Günlükler yüklendiğinde, doğru sıralandıklarından emin olmak için zaman damgalarına göre sıralanmalıdır.

Bu işlev için, Azure Işlevleri 'nde kutudan kullanılamayan birkaç ek kitaplıklara başvuracağız. Bu kitaplıkları dahil etmek için, NuGet kullanarak bunları çekmek üzere Azure Işlevlerine ihtiyacımız var. **Kod** kutusunda, **dosyaları görüntüle**' yi seçin.

!["Dosyaları görüntüle" seçeneği](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Aşağıdaki içeriğe sahip Project. JSON adlı bir dosya ekleyin:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

**Kaydet**' i seçtikten sonra, Azure işlevleri gerekli ikilileri indirir.

**Tümleştirin** sekmesine geçin ve Zamanlayıcı parametresine işlev içinde kullanmak için anlamlı bir ad verin. Yukarıdaki kodda, işlev zamanlayıcının *MyTimer*olarak çağrılmasına bekliyor. Süreölçer için bir [cron ifadesini](../app-service/webjobs-create.md#CreateScheduledCRON) aşağıdaki gibi belirtin: `0 * * * * *`. Bu ifade, işlevin dakikada bir kez çalışmasına neden olur.

Aynı **tümleştirme** sekmesinde **Azure Blob depolama**türünde bir giriş ekleyin. Bu giriş, işlevine baktığı son olayın zaman damgasını içeren Sync. txt dosyasına işaret eder. Bu girişe, işlev içinde parametre adı kullanılarak erişilir. Yukarıdaki kodda, Azure Blob depolama girişi parametre adının *Inputblob*olmasını bekler. Sync. txt dosyasının bulunacağı depolama hesabını seçin (aynı veya farklı bir depolama hesabı olabilir). Yol alanında, dosyanın yolunu biçiminde `{container-name}/path/to/sync.txt`girin.

**Azure Blob depolama**türünde bir çıkış ekleyin. Bu çıktı, girişte tanımladığınız Sync. txt dosyasına işaret eder. Bu çıktı, işlevi tarafından, en son olayın zaman damgasını yazmak için kullanılır. Yukarıdaki kod bu parametrenin *outputblob*olarak adlandırıldığını bekliyor.

İşlev artık hazır. **Geliştirme** sekmesine dönüp kodu kaydettiğinizden emin olun. Herhangi bir derleme hatası için çıkış penceresini kontrol edin ve bunları gerektiği gibi düzeltin. Kod derleniyorsa, kod bundan sonra Anahtar Kasası günlüklerini her dakikada denetlemelidir ve tanımlanan Service Bus kuyruğuna yeni olaylar itilebilmelidir. İşlevin her tetiklenişinde günlüğe kaydetme bilgilerinin günlük penceresine yazılacağını görmeniz gerekir.

### <a name="azure-logic-app"></a>Azure mantıksal uygulaması

Ardından, işlevin Service Bus kuyruğuna itileceği olayları oluşturan, içeriği ayrıştırdığı ve eşleşen koşula göre bir e-posta gönderen bir Azure mantıksal uygulaması oluşturmanız gerekir.

**Kaynak** [](../logic-apps/quickstart-create-first-logic-app-workflow.md)  > tümleştirmemantıksaluygulamasıoluşturseçeneğinibelirleyerekbirmantıksaluygulamaoluşturun. > 

Mantıksal uygulama oluşturulduktan sonra şuraya gidin ve **Düzenle**' yi seçin. Mantıksal uygulama Düzenleyicisi 'nde **Service Bus kuyruğu** ' nu seçin ve kuyruğa bağlamak için Service Bus kimlik bilgilerinizi girin.

![Azure Logic App Service veriyolu](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

**Koşul Ekle**' yi seçin. Koşulda, gelişmiş düzenleyiciye geçin ve aşağıdaki kodu girin. *APP_ID* değerini Web uygulamanızın gerçek uygulama kimliğiyle değiştirin:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Bu ifade, gelen olaydaki *AppID* (Service Bus iletisinin gövdesi) uygulamanın *AppID* değeri değilse aslında **false** döndürür.

Şimdi, **Hayır, HIÇBIR şey yapma**altında bir eylem oluşturun.

![Azure Logic Apps eylemi seçin](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Eylem için **Office 365-e-posta gönder**' i seçin. Tanımlanan koşul **false**değerini döndürdüğünde göndermek üzere bir e-posta oluşturmak için alanları doldurun. Office 365 yoksa, aynı sonuçlara ulaşmak için alternatifler arayın.

Artık bir dakika sonra Yeni Anahtar Kasası denetim günlüklerini gösteren uçtan uca bir işlem hattı vardır. Bulduğu yeni günlüklere Service Bus kuyruğuna iter. Mantıksal uygulama, sıradaki yeni bir ileti olduğunda tetiklenir. Olaydaki *AppID* , çağıran UYGULAMANıN uygulama kimliğiyle eşleşmezse, bir e-posta gönderir.
