---
title: Azure uygulama yapılandırma mağazalarından anahtar değerlerini otomatik olarak yedekle
description: Anahtar değerlerinin uygulama yapılandırma depoları arasında otomatik yedeklemesini ayarlamayı öğrenin.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: b3e0bcad7beccc31e1772fbb24ffad7f502b8140
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454252"
---
# <a name="back-up-app-configuration-stores-automatically"></a>Uygulama yapılandırma depolarını otomatik olarak yedekleme

Bu makalede, birincil Azure uygulama yapılandırma deposundan bir ikincil depoya anahtar değerleri otomatik olarak yedeklemeyi nasıl ayarlayacağınızı öğreneceksiniz. Otomatik yedekleme, uygulama yapılandırmasıyla Azure Event Grid tümleştirmesini kullanır. 

Otomatik yedeklemeyi ayarladıktan sonra uygulama yapılandırması, bir yapılandırma deposundaki anahtar değerlerinde yapılan değişiklikler için Azure Event Grid olayları yayımlar. Event Grid, kullanıcıların anahtar değerleri oluşturulduğu, güncelleştirildiği veya silindiği her seferinde yayınlanan olaylara abone olabileceği çeşitli Azure hizmetlerini destekler.

## <a name="overview"></a>Genel Bakış

Bu makalede, Event Grid olayları almak için Azure kuyruk depolama alanını kullanacaksınız ve kuyruktaki olayları toplu olarak işlemek için Azure Işlevlerinin Zamanlayıcı tetikleyicisini kullanacaksınız. 

Olaylara bağlı olarak bir işlev tetiklendiğinde, birincil uygulama yapılandırma deposundan değiştirilen anahtarların en son değerlerini alır ve ikincil depoyu uygun şekilde güncelleştirir. Bu kurulum, tek bir yedekleme işleminde kısa bir dönemde oluşan birden çok değişikliği birleştirmeye yardımcı olur ve bu da uygulama yapılandırma depolarınız için yapılan aşırı istekleri önler.

![Uygulama yapılandırma deposu yedeklemesinin mimarisini gösteren diyagram.](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Kaynak sağlama

Uygulama yapılandırma depolarını yedeklemenin arkasındaki mosyon, uygulamanızın coğrafi dayanıklılığını artırmak için farklı Azure bölgelerinde birden fazla yapılandırma deposu kullanmaktır. Bunu başarmak için birincil ve ikincil Mağazalarınız farklı Azure bölgelerinde olmalıdır. Bu öğreticide oluşturulan diğer tüm kaynaklar, seçtiğiniz herhangi bir bölgede sağlanabilir. Bunun nedeni, birincil bölge kapalıysa, birincil bölgeye yeniden erişilemediği sürece yedeklenecek yeni bir şey olmayacaktır.

Bu öğreticide, bölgede `centralus` ve bölgedeki tüm diğer kaynaklarda ikincil bir depo oluşturacaksınız `westus` .

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)].

## <a name="prerequisites"></a>Önkoşullar 

- Azure geliştirme iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/vs) .

- [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu öğretici, Azure CLı 'nin sürüm 2.3.1 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal koleksiyondur.

[az group create](/cli/azure/group) komutunu kullanarak bir kaynak grubu oluşturun.

Aşağıdaki örnek `westus` konumunda `<resource_group_name>` adlı bir kaynak grubu oluşturur.  `<resource_group_name>` değerini kaynak grubunuz için benzersiz bir adla değiştirin.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Uygulama yapılandırma depoları oluşturma

Birincil ve ikincil uygulama yapılandırma depolarınızı farklı bölgelerde oluşturun.
`<primary_appconfig_name>`Ve `<secondary_appconfig_name>` yapılandırma depolarınız için benzersiz adlarla değiştirin. Her mağaza adı bir DNS adı olarak kullanıldığı için benzersiz olmalıdır.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>Bir kuyruk oluşturma

Event Grid tarafından yayınlanan olayları almak için bir depolama hesabı ve kuyruk oluşturun.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Uygulama yapılandırma deposu olaylarınızı abone olma

Birincil uygulama yapılandırma deposundan bu iki olaya abone olursunuz:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

Aşağıdaki komut, kuyruğunuza gönderilen iki olay için bir Event Grid aboneliği oluşturur. Uç nokta türü olarak ayarlanır `storagequeue` ve uç nokta kuyruk kimliği olarak ayarlanır. `<event_subscription_name>`Olay aboneliği için tercih ettiğiniz adla değiştirin.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>Kuyruk depolamadan olayları işlemek için işlevler oluşturma

### <a name="set-up-with-ready-to-use-functions"></a>Kullanıma hazırlama işlevleri ile ayarlama

Bu makalede, aşağıdaki özelliklere sahip C# işlevleri ile çalışacaksınız:
- Çalışma zamanı yığını .NET Core 3,1
- Azure Işlevleri çalışma zamanı sürüm 3. x
- Her 10 dakikada bir Zamanlayıcı tarafından tetiklenen işlev

Verilerinizi yedeklemeye başlamanız daha kolay hale getirmek için, kodda herhangi bir değişiklik yapmadan kullanabileceğiniz [bir işlevi test ettik ve yayımladık](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) . Proje dosyalarını indirin ve [Visual Studio 'dan kendi Azure işlev uygulamanızda yayımlayın](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> İndirdiğiniz koddaki ortam değişkenlerinde herhangi bir değişiklik yapmayın. Sonraki bölümde gerekli uygulama ayarlarını oluşturacaksınız.
>

### <a name="build-your-own-function"></a>Kendi işlevinizi oluşturma

Daha önce belirtilen örnek kod gereksinimlerinizi karşılamıyorsa, kendi işlevinizi de oluşturabilirsiniz. Yedekleme işlemini tamamlamak için işleviniz aşağıdaki görevleri gerçekleştirebilmelidir:
- Event Grid bir bildirim içerip içermediğinden, düzenli aralıklarla kuyrukla içeriğini okuyun. Uygulama ayrıntıları için [depolama kuyruğu SDK 'sına](../storage/queues/storage-quickstart-queues-dotnet.md) bakın.
- Kuyruğunuz [Event Grid olay bildirimleri](./concept-app-configuration-event.md#event-schema)içeriyorsa, tüm benzersiz `<key, label>` bilgileri olay iletilerinden ayıklayın. Anahtar ve etiket birleşimi, birincil depodaki anahtar-değer değişikliklerinin benzersiz tanımlayıcısıdır.
- Birincil depodan tüm ayarları okuyun. Yalnızca ikincil depoda, kuyrukta karşılık gelen bir olaya sahip olan ayarları güncelleştirin. Depoda bulunan ancak birincil depoda olmayan ikincil depodan tüm ayarları silin. Yapılandırma depolarına programlı olarak erişmek için [uygulama yapılandırma SDK 'sını](https://github.com/Azure/AppConfiguration#sdks) kullanabilirsiniz.
- İşlem sırasında özel durum yoksa kuyruktaki iletileri silin.
- Gereksinimlerinize göre hata işleme uygulayın. İşlemek isteyebileceğiniz bazı yaygın özel durumları görmek için yukarıdaki kod örneğine bakın.

Bir işlev oluşturma hakkında daha fazla bilgi edinmek için bkz. [Azure 'da bir Zamanlayıcı tarafından tetiklenen](../azure-functions/functions-create-scheduled-function.md) ve [Visual Studio kullanarak Azure işlevleri geliştiren](../azure-functions/functions-develop-vs.md)bir işlev oluşturma.


> [!IMPORTANT]
> Birincil yapılandırma deponuzda ne sıklıkta değişiklik yapmanız gerektiğini gösteren Zamanlayıcı zamanlamasını seçmek için en iyi yargılarınızı kullanın. İşlevi çok sık çalıştırmak, deponuza yönelik azaltma isteklerini sonlandırabilir.
>


## <a name="create-function-app-settings"></a>İşlev uygulaması ayarları oluşturma

Sağladığımız bir işlev kullanıyorsanız, işlev uygulamanızda aşağıdaki uygulama ayarları gerekir:
- `PrimaryStoreEndpoint`: Birincil uygulama yapılandırma deposu için uç nokta. `https://{primary_appconfig_name}.azconfig.io` bunun bir örneğidir.
- `SecondaryStoreEndpoint`: İkincil uygulama yapılandırma deposu için uç nokta. `https://{secondary_appconfig_name}.azconfig.io` bunun bir örneğidir.
- `StorageQueueUri`: Kuyruk URI 'SI. `https://{unique_storage_name}.queue.core.windows.net/{queue_name}` bunun bir örneğidir.

Aşağıdaki komut, işlev uygulamanızda gerekli uygulama ayarlarını oluşturur. `<function_app_name>`İşlev uygulamanızın adıyla değiştirin.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>İşlev uygulamasının yönetilen kimliğine erişim izni verme

İşlev uygulamanız için sistem tarafından atanan bir yönetilen kimlik eklemek için aşağıdaki komutu veya [Azure Portal](../app-service/overview-managed-identity.md#add-a-system-assigned-identity) kullanın.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Gerekli kaynak oluşturma ve rol yönetimini gerçekleştirmek için hesabınızın `Owner` uygun kapsamda (aboneliğiniz veya kaynak grubunuz) izinleri olması gerekir. Rol atamasıyla ilgili yardıma ihtiyacınız varsa [Azure Portal kullanarak Azure rolleri atamayı](../role-based-access-control/role-assignments-portal.md)öğrenin.

İşlev uygulamanızın yönetilen kimliğine uygulama yapılandırma Depolarınıza erişim izni vermek için aşağıdaki komutları veya [Azure Portal](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration) kullanın. Şu rolleri kullan:
- `App Configuration Data Reader`Rolü birincil uygulama yapılandırma deposuna atayın.
- `App Configuration Data Owner`Rolü Ikincil uygulama yapılandırma deposuna atayın.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

İşlev uygulamanızın yönetilen kimliğine kuyruğunuza erişim izni vermek için aşağıdaki komutu veya [Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal) kullanın. `Storage Queue Data Contributor`Rolü kuyruğa atayın.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Uygulama yapılandırma olayını tetikleme

Her şeyin çalıştığından test etmek için, birincil depodan bir anahtar değeri oluşturabilir, güncelleştirebilir veya silebilirsiniz. Bu değişikliği, zamanlayıcının Azure Işlevleri 'ni tetiklediği birkaç saniye sonra ikincil depoda otomatik olarak görmeniz gerekir.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Olayı tetikledi. Birkaç dakika içinde, Event Grid olay bildirimini kuyruğunuza gönderir. *İşlevinizin bir sonraki zamanlanmış çalıştıktan sonra*, ikincil deponuzdaki yapılandırma ayarlarını görüntüleyerek, birincil depodan güncelleştirilmiş anahtar-değer olup olmadığını görebilirsiniz.

> [!NOTE]
> Test ve sorun giderme sırasında işlevinizi, zamanlanmış Zamanlayıcı tetikleyicisi için beklemeden [el ile tetikleyebilirsiniz](../azure-functions/functions-manually-run-non-http.md) .

Yedekleme işlevinin başarıyla çalıştırıldığından emin olduktan sonra, anahtarın artık ikincil deponuzda bulunduğunu görebilirsiniz.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Sorun giderme

İkincil deponuzda yeni ayarı görmüyorsanız:

- Birincil deponuzda ayarı oluşturduktan *sonra* yedekleme işlevinin tetiklendiğinden emin olun.
- Event Grid olay bildirimini zaman içinde sıraya gönderemiyor olabilir. Kuyruğunuzun birincil deponuzdaki olay bildirimini hala içerip içerbir şekilde kontrol edin. Varsa, yedekleme işlevini yeniden tetikleyin.
- Herhangi bir hata veya uyarı için [Azure işlevleri günlüklerine](../azure-functions/functions-create-scheduled-function.md#test-the-function) bakın.
- Azure işlevi uygulamasının, Azure Işlevlerinin okumaya çalıştığı uygulama ayarları için doğru değerleri içerdiğinden emin olmak için [Azure Portal](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal) kullanın.
- Azure [Application Insights](../azure-functions/functions-monitoring.md?tabs=cmd)kullanarak Azure işlevleri için izleme ve uyarı ayarlama de yapabilirsiniz. 


## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu uygulama yapılandırması ve olay aboneliğiyle çalışmaya devam etmeyi planlıyorsanız, bu makalede oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu makalede oluşturulan kaynakları silmek için aşağıdaki komutu kullanın.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

Anahtar değerlerinizin otomatik yedeklemesini ayarlamayı öğrenmiş olduğunuza göre, uygulamanızın coğrafi dayanıklılığını nasıl artırabileceğiniz hakkında daha fazla bilgi edinin:

- [Dayanıklılık ve olağanüstü durum kurtarma](concept-disaster-recovery.md)
