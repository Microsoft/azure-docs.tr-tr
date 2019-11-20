---
title: 'Hızlı başlangıç: go-Azure Event Hubs kullanarak olay gönderme ve alma'
description: 'Hızlı başlangıç: Bu makale, Azure Event Hubs olayları gönderen bir go uygulaması oluşturmaya yönelik bir yol sağlar.'
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: e5f52d0ddbf9a66d974732d6d98ca8a5b09cc2d0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720578"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Hızlı başlangıç: go kullanarak Event Hubs olay gönderme veya olayları alma
Azure Event Hubs saniyede milyonlarca olay alıp işleme kapasitesine sahip olan bir Büyük Veri akış platformu ve olay alma hizmetidir. Event Hubs dağıtılan yazılımlar ve cihazlar tarafından oluşturulan olayları, verileri ve telemetrileri işleyebilir ve depolayabilir. Bir olay hub’ına gönderilen veriler, herhangi bir gerçek zamanlı analiz sağlayıcısı ve işlem grubu oluşturma/depolama bağdaştırıcıları kullanılarak dönüştürülüp depolanabilir. Olay Hub’larının ayrıntılı genel bakışı için bkz. [Olay Hub’larına genel bakış](event-hubs-about.md) ve [Olay Hub’ları özellikleri](event-hubs-features.md).

Bu öğreticide, Olay Hub 'ından olay göndermek veya olayları almak üzere go uygulamalarının nasıl yazılacağı açıklanır. 

> [!NOTE]
> Bu hızlı başlangıcı [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs)’dan örnek olarak indirebilir, `EventHubConnectionString` ve `EventHubName` dizelerini olay hub’ınızdaki değerlerle değiştirebilir ve çalıştırabilirsiniz. Alternatif olarak bu öğreticideki adımları izleyerek kendi çözümünüzü de oluşturabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdaki önkoşulları karşılamanız gerekir:

- Yerel olarak yüklü gidin. Gerekirse [Bu yönergeleri](https://golang.org/doc/install) izleyin.
- Etkin bir Azure hesabı. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap][] oluşturun.
- **Event Hubs bir ad alanı ve bir olay hub 'ı oluşturun**. Event Hubs türünde bir ad alanı oluşturmak ve uygulamanızın Olay Hub 'ı ile iletişim kurması için gereken yönetim kimlik bilgilerini almak için [Azure Portal](https://portal.azure.com) kullanın. Bir ad alanı ve Olay Hub 'ı oluşturmak için [Bu makaledeki](event-hubs-create.md)yordamı izleyin.

## <a name="send-events"></a>Olayları gönderme
Bu bölümde, olayları bir olay hub 'ına göndermek için bir Go uygulamasının nasıl oluşturulacağı gösterilmektedir. 

### <a name="install-go-package"></a>Go paketini yükler

`go get` veya `dep`ile Event Hubs için go paketini alın. Örneğin:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Kod dosyanızdaki paketleri içeri aktarın

Go paketlerini içeri aktarmak için aşağıdaki kod örneğini kullanın:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Hizmet sorumlusu oluşturma

Azure [clı 2,0 Ile Azure hizmet sorumlusu oluşturma](/cli/azure/create-an-azure-service-principal-azure-cli)bölümündeki yönergeleri izleyerek yeni bir hizmet sorumlusu oluşturun. Aşağıdaki adlarla, belirtilen kimlik bilgilerini ortamınıza kaydedin. Hem Go için Azure SDK hem de Event Hubs paketleri bu değişken adlarını aramak üzere önceden yapılandırılmıştır:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Şimdi, Event Hubs istemciniz için bu kimlik bilgilerini kullanan bir yetkilendirme sağlayıcısı oluşturun:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Event Hubs istemcisi oluşturma

Aşağıdaki kod bir Event Hubs istemcisi oluşturur:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>İleti göndermek için kod yazma

Aşağıdaki kod parçacığında, bir terminalden etkileşimli olarak ileti göndermek için (1) veya (2) öğesini kullanarak ileti gönderebilirsiniz:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Özellikler

Olay Hub 'ınızdaki bölümlerin kimliklerini alın:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Olayları Olay Hub 'ına göndermek için uygulamayı çalıştırın. 

Tebrikler! Bir olay hub'ına ileti gönderdiniz.

## <a name="receive-events"></a>Olayları alma

### <a name="create-a-storage-account-and-container"></a>Depolama hesabı ve kapsayıcı oluşturma

Olay akışındaki bölüm ve denetim noktalarında kiralamalar gibi durum, Azure depolama kapsayıcısı kullanılarak alıcılar arasında paylaşılır. Go SDK ile bir depolama hesabı ve kapsayıcı oluşturabilirsiniz, ancak [Azure depolama hesapları hakkındaki](../storage/common/storage-create-storage-account.md)yönergeleri izleyerek de bir tane oluşturabilirsiniz.

Go SDK ile depolama yapıtları oluşturma örnekleri, [Go örnekleri](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) deposunda ve Bu öğreticiye karşılık gelen örnekte bulunabilir.

### <a name="go-packages"></a>Go paketleri

İletileri almak için `go get` veya `dep`Event Hubs için go paketlerini alın:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Kod dosyanızdaki paketleri içeri aktarın

Go paketlerini içeri aktarmak için aşağıdaki kod örneğini kullanın:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Hizmet sorumlusu oluşturma

Azure [clı 2,0 Ile Azure hizmet sorumlusu oluşturma](/cli/azure/create-an-azure-service-principal-azure-cli)bölümündeki yönergeleri izleyerek yeni bir hizmet sorumlusu oluşturun. Aşağıdaki adlarla, belirtilen kimlik bilgilerini ortamınıza kaydedin: hem Go için Azure SDK hem de Event Hubs paketi bu değişken adlarını aramak üzere önceden yapılandırılmıştır.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Daha sonra, Event Hubs istemciniz için bu kimlik bilgilerini kullanan bir yetkilendirme sağlayıcısı oluşturun:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Meta veri yapısını al

Azure go SDK 'sını kullanarak Azure ortamınız hakkında meta veriler içeren bir struct alın. Sonraki işlemler, doğru uç noktaları bulmak için bu yapıyı kullanır.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Kimlik bilgisi Yardımcısı oluştur 

Depolama için bir paylaşılan erişim Imzası (SAS) kimlik bilgisi oluşturmak üzere önceki Azure Active Directory (AAD) kimlik bilgilerini kullanan bir kimlik bilgisi Yardımcısı oluşturun. Son parametre, bu oluşturucuya daha önce kullanılan ortam değişkenlerini kullanmasını söyler:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Bir denetim işaretçisi ve Leaser oluşturma 

Belirli bir alıcıya bölüm kiralamadan sorumlu bir **Leaser**oluşturun ve diğer alıcıların doğru sapmayı okumaya başlayabilmesi için ileti akışı için denetim noktaları yazmaktan sorumlu bir **Denetim işaretçisi**oluşturun.

Şu anda, hem kiralamaları hem de kontrol noktalarını yönetmek için aynı depolama kapsayıcısını kullanan tek bir **Storageleasercheckpointer** vardır. Depolama hesabı ve kapsayıcı adlarına ek olarak, **Storageleasercheckpointer** , önceki adımda oluşturulan kimlik bilgilerini ve kapsayıcıya doğru şekilde erişmek için Azure ortam yapısını gerektirir.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Olay Işlemcisi Konağı oluştur

Artık aşağıdaki gibi bir EventProcessorHost oluşturmak için gereken parçalara sahipsiniz. Aynı **Storageleasercheckpointer** , daha önce açıklandığı gibi hem Leaser hem de Check işaretçisi olarak kullanılır:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>İşleyici oluştur 

Şimdi bir işleyici oluşturun ve olay işleyicisi ana bilgisayarı ile kaydedin. Konak başlatıldığında, bu ve diğer belirtilen işleyicileri gelen iletilere uygular:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>İleti almak için kod yazma

Her şey ayarlandığında, kalıcı olarak çalışmasını önlemek için olay Işlemcisi konağını `Start(context)` başlatabilir veya yalnızca iletiler kullanılabildiği sürece bu `StartNonBlocking(context)` çalıştırabilirsiniz.

Bu öğretici aşağıdaki şekilde başlatılır ve çalışır; `StartNonBlocking`kullanarak bir örnek için GitHub örneğine bakın:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makaleleri okuyun:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs'ın özellikleri ve terminolojisi](event-hubs-features.md)
- [Event Hubs ile ilgili SSS](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[ücretsiz bir hesap]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
