---
title: .NET için istemci kitaplığı ile hesap kaynaklarını yönetme-Azure Batch | Microsoft Docs
description: Batch yönetimi .NET kitaplığı ile Azure Batch hesabı kaynakları oluşturun, silin ve değiştirin.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: f7554993e2e3d8d2f6bce71db57a746a4392ce1a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095083"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>.NET için Batch yönetimi istemci kitaplığı ile Batch hesaplarını ve kotaları yönetme

> [!div class="op_single_selector"]
> * [Azure portal](batch-account-create-portal.md)
> * [Batch Yönetimi .NET](batch-management-dotnet.md)
> 
> 

Batch hesabı oluşturma, silme, anahtar yönetimi ve kota bulmayı otomatikleştirmek için [Batch yönetimi .net][api_mgmt_net] kitaplığını kullanarak, Azure Batch uygulamalarınızda bakım yükünü düşürebilirsiniz.

* Herhangi bir bölge içinde **Batch hesapları oluşturun ve silin** . Örneğin, bağımsız bir yazılım satıcısı (ISV) olarak, her birine Faturalandırma amacıyla ayrı bir Batch hesabı atandığı istemcileriniz için bir hizmet sağlarsanız, müşteri portalınıza hesap oluşturma ve silme yetenekleri ekleyebilirsiniz.
* Batch hesaplarınızdan herhangi birini kullanarak **Hesap anahtarlarını alın ve yeniden oluşturun** . Bu, hesap anahtarlarının düzenli olarak geçişine veya süresinin dolmasını zorlayan güvenlik ilkeleriyle uyum sağlamanıza yardımcı olabilir. Çeşitli Azure bölgelerinde birkaç Batch hesabınız olduğunda, bu geçiş işleminin Otomasyonu çözümünüzün verimliliğini artırır.
* **Hesap kotalarını kontrol edin** ve hangi Batch hesaplarının hangi sınırlara sahip olduğunu belirlemek için deneme yanılma ve hata tahminlerini göz önüne alın. İşleri başlatmadan, havuzlar oluşturmadan veya işlem düğümleri eklemeden önce hesap kotalarınızı denetleyerek, bu işlem kaynaklarının nerede veya ne zaman oluşturulduğunu önceden ayarlayabilirsiniz. Bu hesaplara ek kaynaklar ayırmadan önce hangi hesapların kota artışı gerektirdiğini belirleyebilirsiniz.
* **Diğer Azure hizmetlerinin özelliklerini** , Batch yönetimi .net, [Azure Active Directory][aad_about]ve [Azure Resource Manager][resman_overview] birlikte aynı uygulamada birlikte kullanarak tam özellikli bir yönetim deneyimi için birleştirin. Bu özellikleri ve API 'Lerini kullanarak, bir frictiondaha az kimlik doğrulama deneyimi, kaynak grupları oluşturma ve silme özelliği ve bir uçtan uca yönetim çözümü için yukarıda açıklanan özellikleri sağlayabilirsiniz.

> [!NOTE]
> Bu makale, Batch hesaplarınızın, anahtarlarınızın ve kotaların programlı yönetimine odaklanırken, [Azure Portal][azure_portal]kullanarak bu etkinliklerin birçoğunu gerçekleştirebilirsiniz. Daha fazla bilgi için, [Azure Batch hizmeti için Azure Portal ve kotaları ve sınırları](batch-quota-limit.md) [kullanarak Azure Batch hesabı oluşturma](batch-account-create-portal.md) konusuna bakın.
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Batch hesapları oluşturma ve silme
Belirtildiği gibi, Batch yönetim API 'sinin birincil özelliklerinden biri de bir Azure bölgesinde Batch hesapları oluşturmak ve silmek. Bunu yapmak için [Batchmanagementclient. account. CreateAsync][net_create] ve [DeleteAsync][net_delete]ya da zaman uyumlu karşılıkları kullanın.

Aşağıdaki kod parçacığı bir hesap oluşturur, yeni oluşturulan hesabı Batch hizmetinden alır ve siler. Bu kod parçacığında ve bu makaledeki diğerleri, `batchManagementClient` [batchmanagementclient][net_mgmt_client]'ın tamamen başlatılmış bir örneğidir.

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
> Toplu Işlem yönetimi .NET kitaplığı ve BatchManagementClient sınıfı kullanan uygulamalar, **Hizmet Yöneticisi** veya yönetim için Batch hesabının sahibi olan abonelik için **ortak yönetici** erişimi gerektirir. Daha fazla bilgi için Azure Active Directory bölümüne ve [AccountManagement][acct_mgmt_sample] Code örneğine bakın.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Hesap anahtarlarını alın ve yeniden oluşturun
[Listkeysasync][net_list_keys]kullanarak aboneliğinizdeki herhangi bir Batch hesabından birincil ve ikincil hesap anahtarlarını alın. [Regeneratekeyasync][net_regenerate_keys]kullanarak bu anahtarları yeniden oluşturabilirsiniz.

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
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
> Yönetim uygulamalarınız için kolaylaştırılmış bir bağlantı iş akışı oluşturabilirsiniz. İlk olarak, [Listkeysasync][net_list_keys]ile yönetmek istediğiniz Batch hesabı için bir hesap anahtarı alın. Ardından, toplu Iş .NET kitaplığının [Batchsharedkeycredentials][net_sharedkeycred] sınıfını başlatırken, [batchclient][net_batch_client]başlatılırken kullanılan bu anahtarı kullanın.
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Azure aboneliğini ve Batch hesabı kotalarını denetleme
Azure abonelikleri ve Batch gibi bireysel Azure Hizmetleri, içerdikleri belirli varlıkların sayısını sınırlayan varsayılan kotalardır. Azure aboneliklerine yönelik varsayılan kotalar için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-subscription-service-limits.md). Batch hizmetinin varsayılan kotaları için, [Azure Batch hizmeti Için kotalar ve sınırlar](batch-quota-limit.md)bölümüne bakın. Batch yönetimi .NET kitaplığını kullanarak, uygulamalarınızda bu kotaları kontrol edebilirsiniz. Bu, havuzlar ve işlem düğümleri gibi hesap veya işlem kaynakları eklemeden önce ayırma kararları almanızı sağlar.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Batch hesabı kotaları için bir Azure aboneliğini denetleme
Bir bölgede Batch hesabı oluşturmadan önce, söz konusu bölgeye bir hesap ekleyip ekleyemeyeceğinizi görmek için Azure aboneliğinizi kontrol edebilirsiniz.

Aşağıdaki kod parçacığında, bir abonelikte yer alan tüm Batch hesaplarının bir koleksiyonunu almak için önce [Batchmanagementclient. account. ListAsync][net_mgmt_listaccounts] kullanacağız. Bu koleksiyonu elde ettiğimiz bir kez, hedef bölgede kaç tane hesap olduğunu belirledik. Ardından, Batch hesabı kotasını almak ve bu bölgede kaç tane hesap (varsa) oluşturulacağına yönelik [Batchmanagementclient. abonelikleri][net_mgmt_subscriptions] kullanırız.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Yukarıdaki kod parçacığında, `creds` bir [TokenCloudCredentials][azure_tokencreds]örneğidir. Bu nesnenin oluşturulmasıyla ilgili bir örnek görmek için GitHub 'daki [AccountManagement][acct_mgmt_sample] kod örneğine bakın.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>İşlem kaynağı kotaları için Batch hesabını denetleme
Batch çözümünüzdeki işlem kaynaklarını arttırmadan önce, ayırmak istediğiniz kaynakların hesabın kotalarını aşmayacağından emin olmak için kontrol edebilirsiniz. Aşağıdaki kod parçacığında, adlı `mybatchaccount`Batch hesabı için kota bilgilerini yazdırdık. Kendi uygulamanızda, bu tür bilgileri kullanarak hesabın oluşturulacak ek kaynakları işleyemeyeceğini belirleyebilirsiniz.

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
> Azure abonelikleri ve hizmetleri için varsayılan kotalar olmakla kalmaz, [Azure Portal][azure_portal]bir istek yayımlayarak bu limitlerin birçoğu ortaya çıkar. Örneğin, Batch hesabı kotalarınızı artırma yönergeleri için [Azure Batch hizmetine yönelik kotalar ve sınırlar](batch-quota-limit.md) bölümüne bakın.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Azure AD 'yi Batch yönetimi .NET ile kullanma

Batch yönetimi .NET kitaplığı bir Azure Kaynak sağlayıcısı istemcsahiptir ve hesap kaynaklarını programlı bir şekilde yönetmek için [Azure Resource Manager][resman_overview] birlikte kullanılır. Batch yönetimi .NET kitaplığı ve [Azure Resource Manager][resman_overview]aracılığıyla herhangi bir Azure Kaynak sağlayıcısı istemcisiyle yapılan isteklerin kimliğini doğrulamak IÇIN Azure AD gerekir. Batch yönetimi .NET kitaplığı ile Azure AD kullanma hakkında daha fazla bilgi için bkz. [Azure Active Directory kullanarak Batch çözümlerinin kimliğini doğrulayın](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>GitHub 'da örnek proje

Batch yönetimi .NET 'i eylemde görüntülemek için GitHub 'da [AccountManagement][acct_mgmt_sample] örnek projesine göz atın. AccountManagement örnek uygulaması aşağıdaki işlemleri gösterir:

1. [Adal][aad_adal]kullanarak Azure AD 'den bir güvenlik belirteci alın. Kullanıcı önceden oturum açmamışsa Azure kimlik bilgileri istenir.
2. Azure AD 'den alınan güvenlik belirteci ile, hesap ile ilişkili aboneliklerin listesi için Azure 'u sorgulamak üzere bir [Subscriptionclient][resman_subclient] oluşturun. Kullanıcı, birden fazla abonelik içeriyorsa listeden bir abonelik seçebilir.
3. Seçili abonelikle ilişkili kimlik bilgilerini al.
4. Kimlik bilgilerini kullanarak bir [ResourceManagementClient][resman_client] nesnesi oluşturun.
5. Bir kaynak grubu oluşturmak için [ResourceManagementClient][resman_client] nesnesini kullanın.
6. Birkaç Batch hesabı işlemini gerçekleştirmek için bir [Batchmanagementclient][net_mgmt_client] nesnesi kullanın:
   * Yeni kaynak grubunda bir Batch hesabı oluşturun.
   * Batch hizmetinden yeni oluşturulan hesabı alın.
   * Yeni hesap için hesap anahtarlarını yazdır.
   * Hesap için yeni bir birincil anahtar oluşturun.
   * Hesap için kota bilgilerini yazdırın.
   * Aboneliğin kota bilgilerini yazdırın.
   * Abonelik içindeki tüm hesapları yazdır.
   * Yeni oluşturulan hesabı silin.
7. Kaynak grubunu silin.

Yeni oluşturulan Batch hesabını ve kaynak grubunu silmeden önce bunları [Azure Portal][azure_portal]görüntüleyebilirsiniz:

Örnek uygulamayı başarılı bir şekilde çalıştırmak için, önce Azure portal Azure AD kiracınızla kaydetmeniz ve Azure Resource Manager API 'sine izinler vermeniz gerekir. [Batch yönetim çözümlerini Active Directory kimlik doğrulaması konusunda](batch-aad-auth-management.md)belirtilen adımları izleyin.


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Azure Active Directory nedir?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Azure AD için kimlik doğrulama senaryoları"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Uygulamaları Azure Active Directory tümleştirme"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
