---
title: 'Öğretici: karma bağlantıya özel olayları gönderme-Event Grid'
description: 'Öğretici: Azure Event Grid ve Azure CLı kullanarak bir konu yayımlayın ve bu olaya abone olun. Uç nokta için karma bir bağlantı kullanılır.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: tutorial
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 2773d06fdfc606faaab88340fc2ef9685bba21f9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720711"
---
# <a name="tutorial-route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Öğretici: Azure CLı ve Event Grid ile Azure Relay Karma Bağlantılar özel olayları yönlendirme

Azure Event Grid, bulut için bir olay oluşturma hizmetidir. Azure Relay Karma Bağlantılar, desteklenen olay işleyicilerinden biridir. Genel uç noktası olmayan uygulamalardan alınan olayları işlemeniz gerektiğinde olay işleyicisi olarak karma bağlantıları kullanırsınız. Bu uygulamalar kurumsal ağınızın içinde olabilir. Bu makalede Azure CLI ile özel bir konu oluşturacak, bu özel konuya abone olacak ve olayı tetikleyerek sonucu görüntüleyeceksiniz. Olayları karma bağlantıya gönderirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Bu makalede zaten bir karma bağlantınız ve dinleyici uygulamanız olduğu varsayılmıştır. Karma bağlantıları kullanmaya başlamak için bkz. [Relay Karma Bağlantılar’ı kullanmaya başlama - .NET](../service-bus-relay/relay-hybrid-connections-dotnet-get-started.md) veya [Relay Karma Bağlantılar’ı kullanmaya başlama - Düğüm](../service-bus-relay/relay-hybrid-connections-node-get-started.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!NOTE]
> Yerel makinenizde Azure CLı kullanıyorsanız, Azure CLı sürüm 2.0.56 veya üstünü kullanın. Azure CLı 'nın en son sürümünü yükleme yönergeleri için bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Event Grid konuları Azure kaynaklarıdır ve bir Azure kaynak grubuna yerleştirilmelidir. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal koleksiyondur.

[az group create](/cli/azure/group#az-group-create) komutuyla bir kaynak grubu oluşturun. 

Aşağıdaki örnek *westus2* konumunda *gridResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Özel konu oluşturma

Event grid konusu, olaylarınızı göndereceğiniz kullanıcı tanımlı bir uç nokta sağlar. Aşağıdaki örnekte özel konu, kaynak grubunuzda oluşturulur. `<topic_name>` değerini özel konunuz için benzersiz bir adla değiştirin. Bir DNS girişi ile temsil edildiğinden Event Grid konusunun adı benzersiz olmalıdır.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>Özel konuya abone olma

İzlemek istediğiniz olayları Event Grid söylemek için bir Event Grid konusuna abone olursunuz. Aşağıdaki örnek, oluşturduğunuz özel konuya abone olur ve uç nokta için karma bağlantının kaynak KIMLIĞINI geçirir. Karma bağlantı kimliği şu biçimdedir:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

Aşağıdaki betik, geçiş ad alanının kaynak kimliğini alır. Karma bağlantının kimliğini oluşturur ve bir event grid konusuna abone olur. Betik, uç nokta türünü `hybridconnection` olarak ayarlar ve uç noktanın karma bağlantı kimliğini kullanır.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid \
  --expiration-date "<yyyy-mm-dd>"
```

Abonelik için bir [sona erme tarihi](concepts.md#event-subscription-expiration) belirlendiğine dikkat edin.

## <a name="create-application-to-process-events"></a>Olayları işlemek için uygulama oluşturma

Karma bağlantıdan olayları alabilecek bir uygulamaya ihtiyacınız vardır. [Microsoft Azure Event Grid Hybrid Connection Consumer sample for C#](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination), bu işlemi gerçekleştirir. Önkoşul adımlarını tamamladınız.

1. Visual Studio 2019 veya sonraki bir sürüme sahip olduğunuzdan emin olun.

1. Depoyu yerel makinenize kopyalayın.

1. Visual Studio'da HybridConnectionConsumer projesini yükleyin.

1. Program.cs dosyasında `<relayConnectionString>` ve `<hybridConnectionName>` yerine oluşturduğunuz geçiş bağlantısı dizesini ve karma bağlantı adını yazın.

1. Visual Studio'da derleyin ve çalıştırın.

## <a name="send-an-event-to-your-topic"></a>Konunuza olay gönderme

Event Grid’in iletiyi uç noktanıza nasıl dağıttığını görmek için bir olay tetikleyelim. Bu makalede olayı tetiklemek için Azure CLI'yı nasıl kullanacağınız gösterilmektedir. Alternatif olarak [Event Grid yayımcı uygulamasını](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher) da kullanabilirsiniz.

İlk olarak özel konunun URL’sini ve anahtarını alalım. Burada da `<topic_name>` yerine özel konunuzun adını yazın.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Bu makaleyi kolaylaştırmak için özel konuya göndereceğiniz örnek olay verileri sağlanmıştır. Normalde olay verilerini bir uygulama veya Azure hizmeti gönderir. CURL, HTTP istekleri gönderen bir yardımcı programdır. Bu makalede, özel konuya bir olay göndermek için CURL kullanın.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Dinleyici uygulamanız olay iletisini almalıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu olayla çalışmaya devam etmeyi planlıyorsanız bu makalede oluşturulan kaynakları temizlemeyin. Aksi takdirde, bu makalede oluşturduğunuz kaynakları silmek için aşağıdaki komutu kullanın.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Artık konu oluşturma ve olay aboneliklerini öğrendiğinize göre, Event Grid’in size nasıl yardımcı olabileceği konusunda daha fazla bilgi edinebilirsiniz:

- [Event Grid Hakkında](overview.md)
- [Blob depolama olaylarını bir özel web uç noktasına yönlendirme](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid ve Logic Apps ile sanal makine değişikliklerini izleme](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Veri ambarına büyük veri akışı yapma](event-grid-event-hubs-integration.md)
