---
title: 'Hızlı başlangıç: Web uç noktasına özel olayları gönderme-Event Grid Azure portal'
description: 'Hızlı başlangıç: özel bir konu yayımlamak ve bu konu için olaylara abone olmak için Azure Event Grid ve Azure portal kullanın. Olaylar bir Web uygulaması tarafından işlenir.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 398e63ec9a8b9e1b16d8ffcee538351fc6572de9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720780"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Hızlı başlangıç: Azure portal ve Event Grid özel olayları Web uç noktasına yönlendirme

Azure Event Grid, bulut için bir olay oluşturma hizmetidir. Bu makalede, Azure portalını kullanarak özel bir konu oluşturur, bu özel konuya abone olur ve sonucu görüntülemek için olayı tetiklersiniz. Normalde olayları, olay verilerini işleyen ve eylemler gerçekleştiren bir uç noktaya gönderirsiniz. Bununla birlikte, bu makaleyi basitleştirmek için olayları iletilerin toplandığı ve görüntülendiği bir web uygulamasına gönderirsiniz.

İşiniz bittiğinde, olay verilerinin web uygulamasına gönderildiğini görürsünüz.

![Sonuçları görüntüleme](./media/custom-event-quickstart-portal/view-result.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Özel konu oluşturma

Event grid konusu, olaylarınızı göndereceğiniz kullanıcı tanımlı bir uç nokta sağlar. 

1. [Azure portalda](https://portal.azure.com/) oturum açın.
2. Sol gezinti menüsünde **tüm hizmetler** ' i seçin, **Event Grid**arayın ve **Event Grid konuları**' nı seçin. 

    ![Event Grid konuları seçin](./media/custom-event-quickstart-portal/select-event-grid-topics.png)
3. **Event Grid konuları** sayfasında, araç çubuğunda **+ Ekle** ' yi seçin. 

    ![Event Grid Konu Ekle düğmesi](./media/custom-event-quickstart-portal/add-event-grid-topic-button.png)
4. **Konu oluştur** sayfasında, aşağıdaki adımları izleyin:
    1. Özel konu için benzersiz bir **ad** sağlayın. Konu adı bir DNS girdisi ile temsil edildiğinden konu adı benzersiz olmalıdır. Görüntüde gösterilen adı kullanmayın. Bunun yerine, kendi adınızı oluşturun; 3-50 karakter arasında olmalıdır ve yalnızca a-z, A-Z, 0-9 ve "-" değerlerini içermelidir.
    2. Azure **aboneliğinizi** seçin.
    3. Var olan bir kaynak grubunu seçin veya **Yeni oluştur**' u seçin ve **kaynak grubu**için bir **ad** girin.
    4. Olay Kılavuzu konusu için bir **konum** seçin.
    5. **Olay şeması** alanı için varsayılan değer **Event Grid şemayı** tut. 

       ![Konu sayfası oluştur](./media/custom-event-quickstart-portal/create-custom-topic.png)
    6. **Oluştur**'u seçin. 
5. Özel konu oluşturulduktan sonra başarılı bildirim görürsünüz. **Kaynak grubuna git**' i seçin. 

   ![Başarılı durum bildirimini görüntüleme](./media/custom-event-quickstart-portal/success-notification.png)
6. **Kaynak grubu** sayfasında, olay Kılavuzu konusunu seçin. 

   ![Olay Kılavuzu konu kaynağını seçin](./media/custom-event-quickstart-portal/select-event-grid-topic.png)
7. Olay kılavuzunuzun **Event Grid konu** sayfasını görürsünüz. Bu sayfayı açık tutun. Daha sonra hızlı başlangıçta kullanırsınız. 

    ![Event Grid konu giriş sayfası](./media/custom-event-quickstart-portal/event-grid-topic-home-page.png)

## <a name="create-a-message-endpoint"></a>İleti uç noktası oluşturma
Özel konu için bir abonelik oluşturmadan önce, olay iletisi için bir uç nokta oluşturun. Normalde, olay verileri temelinde uç nokta eylemleri gerçekleştirir. Bu hızlı başlangıcı basitleştirmek için, olay iletilerini görüntüleyin bir [önceden oluşturulmuş web uygulaması](https://github.com/Azure-Samples/azure-event-grid-viewer) dağıtırsınız. Dağıtılan çözüm bir App Service planı, App Service web uygulaması ve GitHub'dan kaynak kod içerir.

1. Çözümü aboneliğinize dağıtmak için makale sayfasında **Azure 'A dağıt** ' ı seçin. Azure portalında parametre değerlerini girin.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
1. Dağıtımın tamamlanması birkaç dakika sürebilir. Dağıtım başarıyla gerçekleştirildikten sonra, web uygulamanızı görüntüleyip çalıştığından emin olun. Web tarayıcısında şu adrese gidin: `https://<your-site-name>.azurewebsites.net`
1. Siteyi görürsünüz ancak henüz yayımlanmış olay yoktur.

   ![Yeni siteyi görüntüleme](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>Özel konuya abone olma

Event Grid’e hangi olayları izlemek istediğinizi ve olayların nereye gönderileceğini bildirmek için bir Event Grid konusuna abone olursunuz.

1. Şimdi, özel konu başlığı için **Event Grid konu** sayfasında, araç çubuğunda **+ olay aboneliği** ' ni seçin.

   ![Olay aboneliği ekleyin](./media/custom-event-quickstart-portal/new-event-subscription.png)
2. **Olay aboneliği oluştur** sayfasında, aşağıdaki adımları izleyin:
    1. Olay aboneliği için bir **ad** girin.
    3. **Uç nokta türü**Için **Web kancası** seçin. 
    4. **Uç nokta seç ' i**seçin. 

       ![Olay abonelik değerlerini sağlama](./media/custom-event-quickstart-portal/provide-subscription-values.png)
    5. Web kancası uç noktası için web uygulamanızın URL'sini girin ve ana sayfa URL'sine `api/updates` ekleyin. **Seçimi Onayla**'yı seçin.

       ![Uç nokta URL'si sağlama](./media/custom-event-quickstart-portal/provide-endpoint.png)
    6. **Olay aboneliği oluştur** sayfasında, **Oluştur**' u seçin.

3. Web uygulamanızı yeniden görüntüleyin ve buna bir abonelik doğrulama olayının gönderildiğine dikkat edin. Göz simgesini seçerek olay verilerini genişletin. Uç noktanın olay verilerini almak istediğini doğrulayabilmesi için Event Grid doğrulama olayını gönderir. Web uygulaması aboneliği doğrulamak için kod içerir.

    ![Abonelik olayını görüntüleme](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>Konunuza olay gönderme

Şimdi, Event Grid’in iletiyi uç noktanıza nasıl dağıttığını görmek için bir olay tetikleyelim. Özel konunuza bir test olayı göndermek için Azure CLI veya PowerShell kullanın. Normalde olay verilerini bir uygulama veya Azure hizmeti gönderir.

Birinci örnekte, Azure CLI kullanılmaktadır. Özel konunun URL’si ve anahtarı ile örnek olay verilerini alır. `<topic name>` yerine özel konunuzun adını yazın. Örnek olay verileri oluşturulur. JSON’un `data` öğesi, olayınızın yüküdür. Bu alana doğru oluşturulmuş herhangi bir JSON gelebilir. Ayrıca, gelişmiş yönlendirme ve filtreleme için konu alanını da kullanabilirsiniz. CURL, HTTP istekleri gönderen bir yardımcı programdır.


### <a name="azure-cli"></a>Azure CLI
1. Azure portal **Cloud Shell**' ni seçin. Cloud Shell penceresinin sol üst köşesindeki **Bash** ' i seçin. 

    ![Cloud Shell-Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Konunun **uç noktasını** almak için aşağıdaki komutu çalıştırın: komutunu kopyalayıp yapıştırdıktan sonra, komutu çalıştırmadan önce **konu adını** ve **kaynak grubu adını** güncelleştirin. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Özel konunun **anahtarını** almak için aşağıdaki komutu çalıştırın: komutunu kopyalayıp yapıştırdıktan sonra, komutu çalıştırmadan önce **konu adını** ve **kaynak grubu** adını güncelleştirin. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Aşağıdaki ifadeyi olay tanımıyla kopyalayın ve **ENTER**tuşuna basın. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Olayı göndermek için aşağıdaki **kıvrımlı** komutunu çalıştırın:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
İkinci örnek, benzer adımları gerçekleştirmek için PowerShell’i kullanır.

1. Azure portal **Cloud Shell** ' i seçin (alternatif olarak https://shell.azure.com/)gidin. Cloud Shell penceresinin sol üst köşesindeki **PowerShell** ' i seçin. Azure CLı bölümünde örnek **Cloud Shell** pencere görüntüsüne bakın.
2. Aşağıdaki değişkenleri ayarlayın. Her komutu kopyalayıp yapıştırdıktan sonra, komutu çalıştırmadan önce **konu adını** ve **kaynak grubu adını** güncelleştirin:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. Aşağıdaki komutları çalıştırarak, konusunun **uç noktasını** ve **anahtarlarını** alın:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Olayı hazırlayın. Cloud Shell penceresinde deyimleri kopyalayın ve çalıştırın. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. Olayı göndermek için **Invoke-WebRequest** cmdlet 'ini kullanın. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Event Grid görüntüleyicisinde doğrulama
Olayı tetiklediniz ve Event Grid, iletiyi abone olurken yapılandırdığınız uç noktaya gönderdi. Az önce gönderdiğiniz olayı görmek için web uygulamanızı görüntüleyin.

```json
{
  "id": "974",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2019-03-28T01:11:59+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/myegridrg/providers/Microsoft.EventGrid/topics/myegridtopic"
}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu olayla çalışmaya devam etmeyi planlıyorsanız bu makalede oluşturulan kaynakları temizlemeyin. Aksi takdirde, bu makalede oluşturduğunuz kaynakları silin.

1. Sol menüdeki **kaynak grupları** ' nı seçin. Sol menüde görmüyorsanız, sol taraftaki menüden **tüm hizmetler** ' i seçin ve **kaynak grupları**' nı seçin. 
2. Kaynak **grubu** sayfasını başlatmak için kaynak grubunu seçin. 
3. Araç çubuğunda **kaynak grubunu sil** ' i seçin. 
4. Kaynak grubunun adını girerek silmeyi onaylayın ve **Sil**' i seçin. 

    ![Kaynak grupları](./media/custom-event-quickstart-portal/delete-resource-groups.png)

    Görüntüde gördüğünüz diğer kaynak grubu Cloud Shell pencere tarafından oluşturulmuştur ve kullanılır. Daha sonra Cloud Shell penceresini kullanmayı planlamıyorsanız, silin. 

## <a name="next-steps"></a>Sonraki adımlar

Özel konu ve olay abonelikleri oluşturma işlemini öğrendiğinize göre artık Event Grid’in size nasıl yardımcı olabileceği konusunda daha fazla bilgi edinebilirsiniz:

- [Event Grid Hakkında](overview.md)
- [Blob depolama olaylarını bir özel web uç noktasına yönlendirme](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid ve Logic Apps ile sanal makine değişikliklerini izleme](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Veri ambarına büyük veri akışı yapma](event-grid-event-hubs-integration.md)
