---
title: Azure Event Hubs bir olay hub 'ına dinamik olarak bölüm ekleme
description: Bu makalede, Azure Event Hubs 'te bir olay hub 'ına dinamik olarak bölüm ekleme konusu gösterilmektedir.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: aeeee1bcefe58b006dac0b6913aaa609cbeefb8c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775129"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Azure Event Hubs bir olay hub 'ına (Apache Kafka konuya) dinamik olarak bölüm ekleme
Event Hubs her bir tüketicinin ileti akışında yalnızca belirli bir alt küme ya da bölümü okuduğu bölünmüş bir tüketici modeli aracılığıyla ileti akışı sağlar. Bu model, olay işleme için yatay ölçek sağlar ve kuyruklar ile konularda kullanılamayan diğer akış odaklı özellikleri sunar. Bölüm bir olay hub'ında tutulan olayların sıralı dizisidir. Daha yeni olaylar geldikçe, bu sıranın sonuna eklenir. Genel olarak bölümler hakkında daha fazla bilgi için bkz. [bölümler](event-hubs-scalability.md#partitions)

Bir olay hub 'ı oluşturma sırasında bölüm sayısını belirtebilirsiniz. Bazı senaryolarda, Olay Hub 'ı oluşturulduktan sonra bölümler eklemeniz gerekebilir. Bu makalede, mevcut bir olay hub 'ına dinamik olarak bölümlerin nasıl ekleneceği açıklanmaktadır. 

> [!IMPORTANT]
> Bölümlerin dinamik eklemeleri yalnızca **adanmış** Event Hubs kümelerinde kullanılabilir.

> [!NOTE]
> Apache Kafka istemcileri için, bir **Olay Hub** 'ı bir **Kafka konusuyla** eşlenir. Azure Event Hubs ve Apache Kafka arasında daha fazla eşleme için bkz. [Kafka and Event Hubs kavramsal eşleme](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>Bölüm sayısını güncelleştirme
Bu bölümde, bir olay hub 'ının bölüm sayısını farklı şekillerde güncelleştirme (PowerShell, CLı, vb.) gösterilmektedir.

### <a name="powershell"></a>PowerShell
Bir olay hub 'ındaki bölümleri güncelleştirmek için [set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) PowerShell komutunu kullanın. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
[`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az_eventhubs_eventhub_update)Bir olay hub 'ındaki bölümleri güncelleştirmek için CLI komutunu kullanın. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Resource Manager şablonu
`partitionCount`Kaynak Yöneticisi şablonundaki özelliğin değerini güncelleştirin ve kaynağı güncelleştirmek için şablonu yeniden dağıtın. 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
`AlterTopics`Bölüm sayısını artırmak için API 'yi (örneğin, **Kafka-konular** CLI aracı aracılığıyla) kullanın. Ayrıntılar için bkz. [Kafka konularını değiştirme](http://kafka.apache.org/documentation/#basic_ops_modify_topic). 

## <a name="event-hubs-clients"></a>Event Hubs istemcileri
Bir olay hub 'ında bölüm sayısı güncelleştirilirken Event Hubs istemcilerinin nasıl davranacağını inceleyelim. 

Var olan bir hub 'a bir bölüm eklediğinizde, Olay Hub 'ı istemcisi, `MessagingException` istemcilere varlık meta verilerinin (varlık, Olay Hub 'ınız ve meta verileri bölüm bilgileri olduğunu) bildiren bir hizmet alır. İstemciler AMQP bağlantılarını otomatik olarak yeniden açar ve ardından değiştirilen meta veri bilgilerini seçer. İstemciler normal olarak çalışır.

### <a name="senderproducer-clients"></a>Sender/Producer istemcileri
Event Hubs üç gönderici seçeneği sağlar:

- **Bölüm gönderici** – Bu senaryoda, istemciler olayları doğrudan bir bölüme gönderir. Bölümler tanımlanabilir ve olayları doğrudan bunlara gönderilebilse de, bu kalıbı önermiyoruz. Bölüm ekleme bu senaryoyu etkilemez. Yeni eklenen bölümleri algılayabilmeleri için uygulamaları yeniden başlatmanızı öneririz. 
- **Bölüm anahtarı gönderici** – Bu senaryoda istemciler, bu anahtara ait tüm olayların aynı bölümde sona erdirmek için olayları bir anahtarla gönderir. Bu durumda hizmet, anahtarı ve yönlendirmeleri ilgili bölüme karma hale getirir. Bölüm sayısı güncelleştirmesi, karma değişikliği nedeniyle sıra dışı sorunlara neden olabilir. Bu nedenle, sıralama konusunda dikkat etmeniz durumunda, bölüm sayısını artırmadan önce uygulamanızın mevcut bölümlerdeki tüm olayları tükettiğini doğrulayın.
- Hepsini bir **kez deneme gönderici (varsayılan)** – Bu senaryoda, Event Hubs hizmeti, olayları bölümler arasında ve ayrıca bir yük dengeleme algoritması kullanır. Event Hubs hizmeti bölüm sayısı değişikliklerinin farkındadır ve bölüm sayısını değiştiren Saniyeler içinde yeni bölümlere gönderilir.

### <a name="receiverconsumer-clients"></a>Alıcı/tüketici istemcileri
Event Hubs doğrudan alıcılar ve [olay Işlemcisi Konağı (eskı SDK)](event-hubs-event-processor-host.md)  veya [olay IŞLEMCISI (yeni SDK)](event-processor-balance-partition-load.md)olarak adlandırılan kolay bir tüketici kitaplığı sağlar.

- **Doğrudan alıcılar** – doğrudan alıcılar belirli bölümleri dinler. Çalışma zamanı davranışları, bölümler bir olay hub 'ı için ölçekleniyorsa etkilenmez. Doğrudan alıcılar kullanan uygulamanın yeni bölümleri ve alıcıları buna göre atamasını sağlaması gerekir.
- **Olay işlemcisi Konağı** – bu istemci, varlık meta verilerini otomatik olarak yenilemez. Bu nedenle, bölüm sayısı artışının üzerinde seçim olmayacaktır. Bir olay işlemcisi örneğinin yeniden oluşturulması bir varlık meta verilerinin alınmasına neden olur. Bu, sırayla yeni eklenen bölümler için yeni Bloblar oluşturur. Önceden var olan Bloblar etkilenmez. Tüm örneklerin yeni eklenen bölümlerin farkında olduğundan emin olmak için tüm olay işlemci örneklerinin yeniden başlatılması önerilir ve Yük Dengeleme, tüketiciler arasında doğru şekilde işlenir.

    .NET SDK 'nın eski sürümünü ([windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)) kullanıyorsanız, denetim noktasındaki bölüm sayısı hizmetten getirilen bölüm sayısıyla eşleşmiyorsa, olay işleyicisi ana bilgisayarı yeniden başlatma sırasında var olan bir kontrol noktasını kaldırır. Bu davranışın uygulamanız üzerinde bir etkisi olabilir. 

## <a name="apache-kafka-clients"></a>Apache Kafka istemcileri
Bu bölümde, bir olay hub 'ı için bölüm sayısı güncelleştirildiği zaman Azure Event Hubs 'ın Kafka uç noktasını kullanan Apache Kafka istemcilerinin nasıl davranacağını açıklanmaktadır. 

Apache Kafka protokolle Event Hubs kullanan Kafka istemcileri AMQP protokolünü kullanan Olay Hub istemcilerinden farklı şekilde davranır. Kafka istemcileri, her milisaniyede bir meta verilerini güncelleştirir `metadata.max.age.ms` . Bu değeri istemci yapılandırmalarında belirtirsiniz. `librdkafka`Kitaplıklar aynı yapılandırmayı de kullanır. Meta veri güncelleştirmeleri, bölüm sayısı arttıkça birlikte hizmet değişikliklerinin istemcilerine bildirir. Yapılandırmaların listesi için bkz. [Event Hubs için Apache Kafka yapılandırması](apache-kafka-configurations.md).

### <a name="senderproducer-clients"></a>Sender/Producer istemcileri
Üreticileri her zaman her bir üretilen kayıt kümesi için bölüm hedefini içeren gönderme isteklerini belirler. Bu nedenle, tüm oluşturma bölümlendirme işlemi, üreticinin aracısının meta verilerinin görünümü ile istemci tarafında yapılır. Yeni bölümler üreticinin meta veri görünümüne eklendikten sonra, bunlar üretici istekleri için kullanılabilir olacaktır.

### <a name="consumerreceiver-clients"></a>Tüketici/alıcı istemcileri
Bir tüketici grubu üyesi bir meta veri yenilemesi gerçekleştirdiğinde ve yeni oluşturulan bölümleri başlattığında, bu üye bir grup yeniden dengelemeye başlar. Daha sonra tüketici meta verileri tüm grup üyeleri için yenilenir ve yeni bölümler ayrılan yeniden dengeleme lideri tarafından atanır.

## <a name="recommendations"></a>Öneriler

- Bölüm anahtarını üretici uygulamalarınız ile birlikte kullanıyorsanız ve bir bölümde sıralamayı sağlamak için anahtar karmaya bağlı olarak, bölüm dinamik olarak ekleniyor önerilmez. 

    > [!IMPORTANT]
    > Mevcut veriler sıralamayı korur, ancak bölümlerin eklenmesi nedeniyle bölüm sayısı değiştirildikten sonra karma iletiler için bölüm karma değeri bozulur.
- Mevcut bir konuya veya Olay Hub örneğine bölüm eklemek aşağıdaki durumlarda önerilir:
    - Olayları göndermek için varsayılan yöntemi kullandığınızda
     - Kafka varsayılan bölümleme stratejileri, örnek: yapışkan Atamaveya strateji


## <a name="next-steps"></a>Sonraki adımlar
Bölümler hakkında daha fazla bilgi için bkz. [bölümler](event-hubs-scalability.md#partitions).
