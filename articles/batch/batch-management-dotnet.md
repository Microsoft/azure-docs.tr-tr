---
title: Hesap kaynaklarını yönetmek için Batch yönetimi .NET kitaplığını kullanma
description: Batch yönetimi .NET kitaplığı ile Azure Batch hesabı kaynakları oluşturun, silin ve değiştirin.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896740"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>.NET için Batch yönetimi istemci kitaplığı ile Batch hesaplarını ve kotaları yönetme

Batch hesabı oluşturma, silme, anahtar yönetimi ve kota bulmayı otomatikleştirmek için [Batch yönetimi .net](/dotnet/api/overview/azure/batch) kitaplığını kullanarak, Azure Batch uygulamalarınızda bakım yükünü düşürebilirsiniz.

- Herhangi bir bölge içinde **Batch hesapları oluşturun ve silin** . Örneğin, bağımsız bir yazılım satıcısı (ISV) olarak, her birine Faturalandırma amacıyla ayrı bir Batch hesabı atandığı istemcileriniz için bir hizmet sağlarsanız, müşteri portalınıza hesap oluşturma ve silme yetenekleri ekleyebilirsiniz.
- Batch hesaplarınızdan herhangi birini kullanarak **Hesap anahtarlarını alın ve yeniden oluşturun** . Bu, hesap anahtarlarının düzenli olarak geçişine veya süresinin dolmasını zorlayan güvenlik ilkeleriyle uyum sağlamanıza yardımcı olabilir. Çeşitli Azure bölgelerinde birkaç Batch hesabınız olduğunda, bu geçiş işleminin Otomasyonu çözümünüzün verimliliğini artırır.
- **Hesap kotalarını kontrol edin** ve hangi Batch hesaplarının hangi sınırlara sahip olduğunu belirlemek için deneme yanılma ve hata tahminlerini göz önüne alın. İşleri başlatmadan, havuzlar oluşturmadan veya işlem düğümleri eklemeden önce hesap kotalarınızı denetleyerek, bu işlem kaynaklarının nerede veya ne zaman oluşturulduğunu önceden ayarlayabilirsiniz. Bu hesaplara ek kaynaklar ayırmadan önce hangi hesapların kota artışı gerektirdiğini belirleyebilirsiniz.
- Toplu Işlem yönetimi .NET, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)ve [Azure Resource Manager](../azure-resource-manager/management/overview.md) aynı uygulamada birlikte kullanarak, **diğer Azure hizmetlerinin özelliklerini** tam özellikli bir yönetim deneyimi için birleştirin. Bu özellikleri ve API 'Lerini kullanarak, bir frictiondaha az kimlik doğrulama deneyimi, kaynak grupları oluşturma ve silme özelliği ve bir uçtan uca yönetim çözümü için yukarıda açıklanan özellikleri sağlayabilirsiniz.

> [!NOTE]
> Bu makale, Batch hesaplarınız, anahtarlarınızın ve kotaların programlı yönetimine odaklanırken, [Azure Portal kullanarak](batch-account-create-portal.md)bu etkinliklerin birçoğunu da gerçekleştirebilirsiniz.

## <a name="create-and-delete-batch-accounts"></a>Batch hesapları oluşturma ve silme

Batch yönetim API 'sinin birincil özelliklerinden biri, bir Azure bölgesinde [Batch hesapları](accounts.md) oluşturmak ve silmek. Bunu yapmak için [Batchmanagementclient. account. CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) ve [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync)ya da zaman uyumlu karşılıkları kullanın.

Aşağıdaki kod parçacığı bir hesap oluşturur, yeni oluşturulan hesabı Batch hizmetinden alır ve siler. Bu kod parçacığında ve bu makaledeki diğerleri, `batchManagementClient` [Batchmanagementclient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient)'ın tamamen başlatılmış bir örneğidir.

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Toplu Işlem yönetimi .NET kitaplığı ve BatchManagementClient sınıfı kullanan uygulamalar, hizmet yöneticisi veya yönetim için Batch hesabının sahibi olan abonelik için ortak yönetici erişimi gerektirir. Daha fazla bilgi için Azure Active Directory bölümüne ve [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) Code örneğine bakın.

## <a name="retrieve-and-regenerate-account-keys"></a>Hesap anahtarlarını alın ve yeniden oluşturun

[Getkeysasync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync)kullanarak aboneliğinizdeki herhangi bir Batch hesabından birincil ve ikincil hesap anahtarlarını alın. [Regeneratekeyasync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync)kullanarak bu anahtarları yeniden oluşturabilirsiniz.

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Yönetim uygulamalarınız için kolaylaştırılmış bir bağlantı iş akışı oluşturabilirsiniz. İlk olarak, [Getkeysasync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync)ile yönetmek istediğiniz Batch hesabı için bir hesap anahtarı alın. Ardından, toplu Iş .NET kitaplığının [Batchsharedkeycredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) sınıfını başlatırken, [batchclient](/dotnet/api/microsoft.azure.batch.batchclient)başlatılırken kullanılan bu anahtarı kullanın.

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Azure aboneliğini ve Batch hesabı kotalarını denetleme

Azure abonelikleri ve Batch gibi bireysel Azure Hizmetleri, içerdikleri belirli varlıkların sayısını sınırlayan varsayılan kotalardır. Azure aboneliklerine yönelik varsayılan kotalar için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md). Batch hizmetinin varsayılan kotaları için, [Azure Batch hizmeti Için kotalar ve sınırlar](batch-quota-limit.md)bölümüne bakın. Batch yönetimi .NET kitaplığını kullanarak, uygulamalarınızda bu kotaları kontrol edebilirsiniz. Bu, havuzlar ve işlem düğümleri gibi hesap veya işlem kaynakları eklemeden önce ayırma kararları almanızı sağlar.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Batch hesabı kotaları için bir Azure aboneliğini denetleme

Bir bölgede Batch hesabı oluşturmadan önce, söz konusu bölgeye bir hesap ekleyip ekleyemeyeceğinizi görmek için Azure aboneliğinizi kontrol edebilirsiniz.

Aşağıdaki kod parçacığında, bir abonelikte yer alan tüm Batch hesaplarının bir koleksiyonunu almak için önce **Listasync** kullanırız. Bu koleksiyonu elde ettiğimiz bir kez, hedef bölgede kaç tane hesap olduğunu belirledik. Ardından, Batch hesap kotasını almak ve bu bölgede kaç tane hesap (varsa) oluşturulacağına yönelik olarak **Getquotasasync** kullanırız.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Yukarıdaki kod parçacığında, `creds` bir **TokenCredentials** örneğidir. Bu nesnenin oluşturulmasıyla ilgili bir örnek görmek için GitHub 'daki [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) kod örneğine bakın.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>İşlem kaynağı kotaları için Batch hesabını denetleme

Batch çözümünüzdeki işlem kaynaklarını arttırmadan önce, ayırmak istediğiniz kaynakların hesabın kotalarını aşmayacağından emin olmak için kontrol edebilirsiniz. Aşağıdaki kod parçacığında, adlı Batch hesabı için kota bilgilerini yazdırdık `mybatchaccount` . Kendi uygulamanızda, bu tür bilgileri kullanarak hesabın oluşturulacak ek kaynakları işleyemeyeceğini belirleyebilirsiniz.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Azure abonelikleri ve hizmetleri için varsayılan kotalar olsa da, bu limitlerin birçoğu [Azure Portal bir kota artışı isteyerek](batch-quota-limit.md#increase-a-quota)oluşturulabilir.

## <a name="use-azure-ad-with-batch-management-net"></a>Azure AD 'yi Batch yönetimi .NET ile kullanma

Batch yönetimi .NET kitaplığı bir Azure Kaynak sağlayıcısı istemcsahiptir ve hesap kaynaklarını programlı bir şekilde yönetmek için [Azure Resource Manager](../azure-resource-manager/management/overview.md) birlikte kullanılır. Batch yönetimi .NET kitaplığı ve Azure Resource Manager aracılığıyla herhangi bir Azure Kaynak sağlayıcısı istemcisiyle yapılan isteklerin kimliğini doğrulamak için Azure AD gerekir. Batch yönetimi .NET kitaplığı ile Azure AD kullanma hakkında daha fazla bilgi için bkz. [Azure Active Directory kullanarak Batch çözümlerinin kimliğini doğrulayın](batch-aad-auth.md).

## <a name="sample-project-on-github"></a>GitHub 'da örnek proje

Batch yönetimi .NET 'i eylemde görüntülemek için GitHub 'da [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) örnek projesine göz atın. AccountManagement örnek uygulaması aşağıdaki işlemleri gösterir:

1. [Adal](../active-directory/azuread-dev/active-directory-authentication-libraries.md)kullanarak Azure AD 'den bir güvenlik belirteci alın. Kullanıcı önceden oturum açmamışsa Azure kimlik bilgileri istenir.
2. Azure AD 'den alınan güvenlik belirteci ile, hesap ile ilişkili aboneliklerin listesi için Azure 'u sorgulamak üzere bir [Subscriptionclient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) oluşturun. Kullanıcı, birden fazla abonelik içeriyorsa listeden bir abonelik seçebilir.
3. Seçili abonelikle ilişkili kimlik bilgilerini al.
4. Kimlik bilgilerini kullanarak bir [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) nesnesi oluşturun.
5. Bir kaynak grubu oluşturmak için [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) nesnesini kullanın.
6. Birkaç Batch hesabı işlemini gerçekleştirmek için bir [Batchmanagementclient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) nesnesi kullanın:
   - Yeni kaynak grubunda bir Batch hesabı oluşturun.
   - Batch hizmetinden yeni oluşturulan hesabı alın.
   - Yeni hesap için hesap anahtarlarını yazdır.
   - Hesap için yeni bir birincil anahtar oluşturun.
   - Hesap için kota bilgilerini yazdırın.
   - Aboneliğin kota bilgilerini yazdırın.
   - Abonelik içindeki tüm hesapları yazdır.
   - Yeni oluşturulan hesabı silin.
7. Kaynak grubunu silin.

Örnek uygulamayı başarılı bir şekilde çalıştırmak için, önce Azure portal Azure AD kiracınızla kaydetmeniz ve Azure Resource Manager API 'sine izinler vermeniz gerekir. [Batch yönetim çözümlerini Active Directory kimlik doğrulaması konusunda](batch-aad-auth-management.md)belirtilen adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Batch hizmeti iş akışı ve](batch-service-workflow-features.md) havuzlar, düğümler, işler ve görevler gibi birincil kaynaklar hakkında bilgi edinin.
- [Batch .NET istemci kitaplığı](quick-run-dotnet.md) veya [Python](quick-run-python.md) kullanarak Batch özellikli bir uygulama geliştirmenin temellerini öğrenin. Bu hızlı başlangıç, çoklu işlem düğümlerinde bir iş yükünü yürütmek için Batch hizmetini kullanan bir örnek uygulama boyunca size kılavuzluk eden iş yükü dosyası hazırlama ve alma için Azure depolama 'yı kullanma. git pus
