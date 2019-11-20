---
title: 'Hızlı başlangıç: Azure Işlevine özel olaylar gönderme-Event Grid'
description: 'Hızlı başlangıç: bir konu yayımlamak ve bu olaya abone olmak için Azure Event Grid ve Azure CLı veya portal kullanın. Uç nokta için bir Azure Işlevi kullanılır.'
services: event-grid
keywords: ''
author: banisadr
ms.author: babanisa
ms.date: 11/15/2019
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 5c4ba510360475e1365d4901136c94181e8c3da3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174373"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Hızlı başlangıç: Event Grid ile özel olayları Azure Işlevine yönlendirme

Azure Event Grid, bulut için bir olay oluşturma hizmetidir. Azure Işlevleri desteklenen olay işleyicilerinden biridir. Bu makalede, Azure portalını kullanarak özel bir konu oluşturur, bu özel konuya abone olur ve sonucu görüntülemek için olayı tetiklersiniz. Olayları bir Azure Işlevine gönderirsiniz.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Azure Işlevi oluşturma

Özel konuya abone olmadan önce, olayları işlemek için bir işlev oluşturalım. Azure portal ' kaynak oluştur ' öğesine tıklayın ve ' işlev ' yazıp ' İşlev Uygulaması ' öğesini seçin ve Oluştur ' a tıklayın. Kaynak grubu altında ' Yeni Oluştur ' seçeneğini belirleyin ve bir ad verin. Bunu öğreticinin geri kalanı için kullanacaksınız. İşlev Uygulaması bir ad verin, ' Yayımla ' düğmesini ' Code ' üzerinde bırakın, herhangi bir çalışma zamanı ve bölge seçin ve ardından Oluştur ' a basın.

İşlev Uygulaması hazırsanız, bu sayfaya gidin ve ' + yeni Işlev ' seçeneğine tıklayın. Geliştirme ortamı için ' Portal Içi ' seçeneğini belirleyin ve devam ' a basın. İşlev oluştur altında, daha fazla şablon görüntülemek için ' diğer şablonlar ' seçeneğini belirleyin ve ardından ' Azure Event Grid tetikleyicisi ' araması yapın ve bunu seçin. Bu tetikleyiciyi ilk kez kullanıyorsanız, uzantıyı yüklemek için ' Install ' düğmesine tıklamanız gerekebilir.

![İşlev Event Grid tetikleyicisi](./media/custom-event-to-function/grid-trigger.png)

Uzantıyı yükledikten sonra devam ' a tıklayın, işlevinizin adını verin ve ardından Oluştur ' a basın.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Özel konu oluşturma

Event grid konusu, olaylarınızı göndereceğiniz kullanıcı tanımlı bir uç nokta sağlar. 

1. [Azure portalda](https://portal.azure.com/) oturum açın.
2. Sol gezinti menüsünde **tüm hizmetler** ' i seçin, **Event Grid**arayın ve **Event Grid konuları**' nı seçin. 

    ![Event Grid konuları seçin](./media/custom-event-to-function/select-event-grid-topics.png)
3. **Event Grid konuları** sayfasında, araç çubuğunda **+ Ekle** ' yi seçin. 

    ![Event Grid Konu Ekle düğmesi](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. **Konu oluştur** sayfasında, aşağıdaki adımları izleyin:

    1. Özel konu için benzersiz bir **ad** sağlayın. Konu adı bir DNS girdisi ile temsil edildiğinden konu adı benzersiz olmalıdır. Görüntüde gösterilen adı kullanmayın. Bunun yerine, kendi adınızı oluşturun - 3-50 karakter arasında olması gerekir ve içeren yalnızca a-z, A-Z, 0-9, değerleri ve "-".
    2. Azure **aboneliğinizi** seçin.
    3. Önceki adımlardan aynı kaynak grubunu seçin.
    4. Olay Kılavuzu konusu için bir **konum** seçin.
    5. **Olay şeması** alanı için varsayılan değer **Event Grid şemayı** tut. 

       ![Konu sayfası oluştur](./media/custom-event-to-function/create-custom-topic.png)
    6. **Oluştur**'u seçin. 

5. Özel konu oluşturulduktan sonra başarılı bildirim görürsünüz. **Kaynak grubuna git**' i seçin. 

   ![Başarılı durum bildirimini görüntüleme](./media/custom-event-to-function/success-notification.png)

6. **Kaynak grubu** sayfasında, olay Kılavuzu konusunu seçin. 

   ![Olay Kılavuzu konu kaynağını seçin](./media/custom-event-to-function/select-event-grid-topic.png)

7. Olay kılavuzunuzun **Event Grid konu** sayfasını görürsünüz. Bu sayfayı açık tutun. Daha sonra hızlı başlangıçta kullanırsınız. 

    ![Event Grid konu giriş sayfası](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Özel konuya abone olma

Event Grid’e hangi olayları izlemek istediğinizi ve olayların nereye gönderileceğini bildirmek için bir Event Grid konusuna abone olursunuz.

1. Şimdi, özel konu başlığı için **Event Grid konu** sayfasında, araç çubuğunda **+ olay aboneliği** ' ni seçin.

   ![Olay aboneliği ekleyin](./media/custom-event-to-function/new-event-subscription.png)

2. **Olay aboneliği oluştur** sayfasında, aşağıdaki adımları izleyin:
    1. Olay aboneliği için bir **ad** girin.
    3. **Uç nokta türü**Için **Azure işlevi** ' ni seçin. 
    4. **Uç nokta seç ' i**seçin. 

       ![Olay abonelik değerlerini sağlama](./media/custom-event-to-function/provide-subscription-values.png)

    5. İşlev uç noktası için İşlev Uygulaması Azure aboneliğini ve kaynak grubunu seçin ve ardından daha önce oluşturduğunuz İşlev Uygulaması ve işlevi seçin. **Seçimi Onayla**'yı seçin.

       ![Uç nokta URL'si sağlama](./media/custom-event-to-function/provide-endpoint.png)

    6. **Olay aboneliği oluştur** sayfasında, **Oluştur**' u seçin.

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
Olayı tetiklediniz ve Event Grid, iletiyi abone olurken yapılandırdığınız uç noktaya gönderdi. Event Grid tetiklenen işleviniz ' ne gidin ve günlükleri açın. Günlüğe olay veri yükünün bir kopyasını görmeniz gerekir. Önce Günlükler penceresini açtığınızdan emin değilseniz veya yeniden bağlan ' ı yeniden deneyin ve ardından bir test olayını tekrar göndermeyi deneyin.

![Işlev tetikleyici günlüğü başarılı](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu olayla çalışmaya devam etmeyi planlıyorsanız bu makalede oluşturulan kaynakları temizlemeyin. Aksi takdirde, bu makalede oluşturduğunuz kaynakları silin.

1. Sol menüdeki **kaynak grupları** ' nı seçin. Sol menüde görmüyorsanız, sol taraftaki menüden **tüm hizmetler** ' i seçin ve **kaynak grupları**' nı seçin. 
2. Kaynak **grubu** sayfasını başlatmak için kaynak grubunu seçin. 
3. Araç çubuğunda **kaynak grubunu sil** ' i seçin. 
4. Kaynak grubunun adını girerek silmeyi onaylayın ve **Sil**' i seçin. 

    ![Kaynak grupları](./media/custom-event-to-function/delete-resource-groups.png)

    Görüntüde gördüğünüz diğer kaynak grubu Cloud Shell pencere tarafından oluşturulmuştur ve kullanılır. Daha sonra Cloud Shell penceresini kullanmayı planlamıyorsanız, silin. 
```

## Next steps

Now that you know how to create topics and event subscriptions, learn more about what Event Grid can help you do:

- [About Event Grid](overview.md)
- [Route Blob storage events to a custom web endpoint](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Stream big data into a data warehouse](event-grid-event-hubs-integration.md)
