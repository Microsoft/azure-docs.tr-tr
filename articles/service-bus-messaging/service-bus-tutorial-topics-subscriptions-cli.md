---
title: "Öğretici: Azure CLı ile yayımlama/abone olma kanalları ve konu filtrelerini kullanarak perakende envanter sınıflama 'nı güncelleştirme"
description: 'Öğretici: Bu öğreticide, bir konudan ve abonelikten ileti gönderme ve alma ile Azure CLı kullanarak filtre kuralları ekleme ve kullanma hakkında bilgi edineceksiniz.'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 0bd0d8eb8abe6f320f73e35b1e3b08e1d8dc1de3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718917"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Öğretici: CLI'yi ve konuları/abonelikleri kullanarak envanter güncelleştirme

Microsoft Azure Service Bus, uygulamalar ve hizmetler arasında bilgi gönderen çok kiracılı bir bulut mesajlaşma hizmetidir. Zaman uyumsuz işlemler, esnek, aracılı mesajlaşmanın yanı sıra, ilk giren ilk çıkar (FIFO) yöntemiyle yapılandırılmış mesajlaşma ve yayımlama/abonelik olanakları da sunar. Bu öğretici, bir perakende stok senaryosunda, Azure CLI ve Java kullanan yayımlama/abone olma kanallarıyla Service Bus konu başlıklarını ve abonelikleri kullanmayı göstermektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Azure CLI kullanarak bir Service Bus konu başlığı ve bu konu başlığına bir veya daha fazla abonelik oluşturma
> * Azure CLI kullanarak konu başlığı filtreleri ekleme
> * Farklı içerikle iki ileti oluşturma
> * İletileri gönderme ve bunların beklenen aboneliklere vardığını doğrulama
> * Aboneliklerden ileti alma

Bu senaryonun bir örneği, birden çok perakende mağazası için stok sınıflama güncelleştirmesidir. Bu senaryoda, her mağaza veya mağaza grubu, sınıflamalarını güncelleştirmeye yönelik iletiler alır. Bu öğretici, bu senaryonun abonelikler ve filtreler kullanılarak uygulanmasını göstermektedir. İlk olarak önce 3 aboneliği olan bir konu başlığı oluşturun, bazı kurallar ve filtreler ekleyin ve ardından konu başlıkları ve aboneliklerden iletiler gönderip alın.

![konu başlığı](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap][] oluşturabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Java ile bir Service Bus uygulaması geliştirebilmeniz için şunlar yüklü olmalıdır:

- [Java Geliştirme Seti](https://aka.ms/azure-jdks), en son sürümü.
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org), sürüm 3.0 veya üzeri.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.4 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus konu başlıkları ve abonelikler

Her [konu başlığı aboneliği](service-bus-messaging-overview.md#topics) her iletinin bir kopyasını alabilir. Konular, protokol ve anlam açılarından Service Bus kuyrukları ile tam olarak uyumludur. Service Bus konu başlıkları, filtreleme koşullarını ve ileti özelliklerini belirleyen veya değiştiren isteğe bağlı eylemleri olan geniş bir seçim kuralı yelpazesini destekler. Bir kural eşleştiğinde bir ileti oluşturulur. Kurallar, filtreler ve eylemler hakkında daha fazla bilgi edinmek için bu [bağlantıyı](topic-filters.md) izleyin.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

CLI yüklendikten sonra bir komut istemi açın ve Azure'da oturum açmak için aşağıdaki komutları çalıştırın. Cloud Shell kullanıyorsanız, bu adımlar gerekli değildir:

1. Azure CLI'yi yerel olarak kullanıyorsanız Azure'da oturum açma için aşağıdaki komutu çalıştırın. Bu komutları Cloud Shell'de çalıştırıyorsanız bu oturum açma adımı gerekli değildir:

   ```azurecli-interactive
   az login
   ```

2. Geçerli abonelik bağlamını kullanmak istediğiniz Azure aboneliğine ayarlayın:

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>Kaynak sağlamak için CLI kullanma

Service Bus kaynakları sağlamak için aşağıdaki komutu çalıştırın. Tüm yer tutucuları uygun değerlerle değiştirdiğinizden emin olun:

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

Son komut çalıştıktan sonra, bağlantı dizesini ve seçtiğiniz kuyruk adını kopyalayıp Not Defteri gibi geçici bir yere yapıştırın. Bu sonraki adımda gerekecektir.

## <a name="create-filter-rules-on-subscriptions"></a>Aboneliklerde filtre kuralları oluşturma

Ad alanı ve konu başlıkları/abonelikler sağlandıktan ve gerekli kimlik bilgilerini edindikten sonra, aboneliklerde filtre kuralları oluşturmaya ve ileti gönderip almaya hazır olursunuz. [Bu GitHub örnek klasöründeki](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) kodu inceleyebilirsiniz.

## <a name="send-and-receive-messages"></a>İleti alma ve gönderme

1. Cloud Shell'in açık olduğundan Bash isteminin görüntülendiğinden emin olun.

2. [Service Bus GitHub deposunu](https://github.com/Azure/azure-service-bus/) aşağıdaki komutu çalıştırarak kopyalayın:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Örnek klasörü `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java` gidin. Bash kabuğunda komutların büyük/küçük harfe duyarlı olduğunu ve yol ayırıcıların eğik çizgi olması gerektiğini unutmayın.

3. Uygulamayı derlemek için aşağıdaki komutu çalıştırın:
   
   ```shell
   mvn clean package -DskipTests
   ```
4. Programı çalıştırmak için aşağıdaki komutu çalıştırın. Yer tutucuları önceki adımda elde ettiğiniz bağlantı dizesi ve konu başlığı adı ile değiştirdiğinizden emin olun:

   ```shell
   java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   Konu başlığına 10 ileti gönderildiğini ve ardından bunların ayrı aboneliklerden alındığını gözlemleyin:

   ![program çıkışı](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu, ad alanını ve tüm ilgili kaynakları kaldırmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Örnek kodu anlama

Bu bölümde, örnek kodun yaptıkları hakkında daha fazla ayrıntı bulunmaktadır.

### <a name="get-connection-string-and-queue"></a>Bağlantı dizesini ve kuyruğu alma

Kod öncelikle programın geri kalanını yürüten bir dizi değişken bildirmektedir:

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

Bağlantı dizesi ve konu başlığı adı, komut satırı parametreleri aracılığıyla eklenen ve `main()` öğesine geçilen tek değerlerdir. Kodun yürütülmesi `run()` yönteminde tetiklenir ve konu başlığından ileti gönderir ve alır:

```java
public static void main(String[] args) {
    TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
    // Send sample messages.
    this.sendMessagesToTopic();

    // Receive messages from subscriptions.
    this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>İleti göndermek için konu başlığı istemcisi oluşturma

`sendMessagesToTopic()` yöntemi, ileti gönderip almak için, bağlantı dizesini ve konu başlığı adını kullanan bir konu başlığı istemci örneği oluşturur ve ardından iletileri gönderen başka bir yöntemi çağırır:

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
    // Create client for the topic.
    TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

    // Create a message sender from the topic client.

    System.out.printf("\nSending orders to topic.\n");

    // Now we can start sending orders.
    CompletableFuture.allOf(
            SendOrders(topicClient,Store[0]),
            SendOrders(topicClient,Store[1]),
            SendOrders(topicClient,Store[2]),
            SendOrders(topicClient,Store[3]),
            SendOrders(topicClient,Store[4]),
            SendOrders(topicClient,Store[5]),
            SendOrders(topicClient,Store[6]),
            SendOrders(topicClient,Store[7]),
            SendOrders(topicClient,Store[8]),
            SendOrders(topicClient,Store[9])
    ).join();

    System.out.printf("\nAll messages sent.\n");
}

    public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8));
            // We always set the Sent to field
            message.setTo(store);
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both.
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});

            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());
            topicClient.sendAsync(message);
        }

        return new CompletableFuture().completedFuture(null);
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Ayrı aboneliklerden iletiler alma

`receiveAllMessages()` yöntemi `receiveAllMessageFromSubscription()` yöntemini çağırır, bu yöntem de çağrı başına bir abonelik istemcisi oluşturur ve ayrı aboneliklerden iletiler alır:

```java
public void receiveAllMessages() throws Exception {
    System.out.printf("\nStart Receiving Messages.\n");

    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2])
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {

    int receivedMessages = 0;

    // Create subscription client.
    IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

    // Create a receiver from the subscription client and receive all messages.
    System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

    while (true)
    {
        // This will make the connection wait for N seconds if new messages are available.
        // If no additional messages come we close the connection. This can also be used to realize long polling.
        // In case of long polling you would obviously set it more to e.g. 60 seconds.
        IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
        if (receivedMessage != null)
        {
            if ( receivedMessage.getProperties() != null ) {
                System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));
                
                // Show the label modified by the rule action
                if(receivedMessage.getLabel() != null)
                    System.out.printf("Label=%s\n", receivedMessage.getLabel());
            }
            
            byte[] body = receivedMessage.getBody();
            Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
            System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());
            
            subscriptionClient.complete(receivedMessage.getLockToken());
            receivedMessages++;
        }
        else
        {
            // No more messages to receive.
            subscriptionClient.close();
            break;
        }
    }
    System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
    
    return new CompletableFuture().completedFuture(null);
}
```

> [!NOTE]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus gezgin, kullanıcıların bir Service Bus ad alanına bağlanmasına ve mesajlaşma varlıklarını kolay bir şekilde yönetmesine olanak tanır. Araç içeri/dışarı aktarma işlevselliği gibi gelişmiş özellikler ya da konu, kuyruk, abonelik, geçiş Hizmetleri, Bildirim Hub 'ları ve Olay Hub 'larını test etme yeteneği sağlar. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide Azure CLI'yi kullanarak kaynaklar sağladınız, sonra bir Service Bus bir konu başlığından ve bunun aboneliklerinden iletiler gönderdi ve aldı. Şunları öğrendiniz:

> [!div class="checklist"]
> * Azure portalı kullanarak bir Service Bus konu başlığı ve bunun için bir veya daha fazla abonelik oluşturma
> * .NET kodu kullanarak konu filtreleri ekleme
> * Farklı içerikle iki ileti oluşturma
> * İletileri gönderme ve bunların beklenen aboneliklere vardığını doğrulama
> * Aboneliklerden ileti alma

Daha fazla ileti gönderme ve alma örneği için [GitHub’daki Service Bus örnekleri](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted) ile çalışmaya başlayın.

Service Bus’ın yayımlama/abone olma özelliklerini kullanma hakkında daha fazla bilgi edinmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [PowerShell ve konular/abonelikler kullanarak stok güncelleştirme](service-bus-tutorial-topics-subscriptions-portal.md)

[ücretsiz bir hesap]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
