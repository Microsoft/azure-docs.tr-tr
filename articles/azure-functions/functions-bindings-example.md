---
title: Azure Işlevleri tetikleme ve bağlama örneği
description: Azure Işlev bağlamalarını yapılandırmayı öğrenin
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d1959792823e04cf34d65ab775ae8c51e741e293
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097478"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Azure Işlevleri tetikleme ve bağlama örneği

Bu makalede bir Azure Işlevinde [tetikleyici ve bağlamaların](./functions-triggers-bindings.md) nasıl yapılandırılacağı gösterilir.

Azure kuyruk depolamada yeni bir ileti göründüğünde Azure Tablo depolama alanına yeni bir satır yazmak istediğinizi varsayalım. Bu senaryo, bir Azure kuyruk depolama tetikleyicisi ve bir Azure Tablo depolama çıkış bağlaması kullanılarak uygulanabilir. 

Bu senaryo için bir *function. JSON* dosyası aşağıda verilmiştir. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

`bindings` Dizideki ilk öğe kuyruk depolama tetikleyicisinin bir öğesidir. `type` Ve`direction` özellikleri tetikleyiciyi belirler. `name` Özelliği, kuyruk iletisi içeriğini alan işlev parametresini tanımlar. İzlenecek kuyruğun adı ' nda `queueName`, bağlantı dizesi tarafından `connection`tanımlanan uygulama ayarıdır.

`bindings` Dizideki ikinci öğe, Azure Tablo depolama çıkış bağlamadır. `type` Ve`direction` özellikleri bağlamayı belirler. `name` Özelliği, işlevin yeni tablo satırını nasıl sağladığını belirtir, bu durumda işlev dönüş değeri ' ni kullanarak. Tablonun `tableName`adı ve bağlantı dizesi tarafından `connection`tanımlanan uygulama ayarıdır.

Azure portal *function. JSON* içeriğini görüntülemek ve düzenlemek için Işlevinizin **tümleştirin** sekmesinde **Gelişmiş Düzenleyici** seçeneğine tıklayın.

> [!NOTE]
> Değeri `connection` bağlantı dizesinin kendisini değil bağlantı dizesini içeren bir uygulama ayarının adıdır. Bağlamalar, *function. JSON* ' ın hizmet gizli dizileri içermediğinden en iyi uygulamayı zorlamak için uygulama ayarlarında depolanan bağlantı dizelerini kullanır.

## <a name="c-script-example"></a>C#betik örneği

Bu tetikleyici C# ve bağlama ile birlikte çalışarak betik kodu aşağıda verilmiştir. Kuyruk iletisi içeriğini sağlayan parametre adının olduğunu `order`ve *function. JSON* içindeki `name` Özellik değeri şu olduğundan, bu ad gereklidir.`order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>JavaScript örneği

Aynı *function. JSON* dosyası bir JavaScript işleviyle birlikte kullanılabilir:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Sınıf kitaplığı örneği

Bir sınıf kitaplığında, aynı tetikleyici ve bağlama bilgileri &mdash; kuyruğu ve tablo adları, depolama hesapları, giriş ve çıkış &mdash; için işlev parametreleri, bir Function. JSON dosyası yerine öznitelikler tarafından sağlanır. Bir örneği aşağıda verilmiştir:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Artık bir Azure kuyruğu tarafından tetiklenen ve Azure Tablo depolama alanına veri çıktısı veren bir çalışma işleviniz var.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Işlevleri bağlama ifadesi desenleri](./functions-bindings-expressions-patterns.md)
