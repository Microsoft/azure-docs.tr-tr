---
title: Azure Işlevlerinde Tetikleyiciler ve bağlamalar
description: Azure işlevinizi çevrimiçi olaylara ve bulut tabanlı hizmetlere bağlamak için Tetikleyicileri ve bağlamaları kullanmayı öğrenin.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 914158ba7cfcc7530120d427c62e69036b3bb156
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085080"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Işlevleri Tetikleyicileri ve bağlamaları kavramları

Bu makalede, işlev Tetikleyicileri ve bağlamaları çevreleyen üst düzey kavramlar öğrenirsiniz.

Tetikleyiciler, bir işlevin çalışmasına neden olur. Bir tetikleyici, bir işlevin nasıl çağrılacağını ve bir işlevin tam olarak bir tetikleyicisine sahip olması gerektiğini tanımlar. Tetikleyiciler, genellikle işlevin yükü olarak belirtilen ilişkili verilere sahiptir. 

İşleve bağlama, başka bir kaynağı işleve bildirimli olarak bağlama yöntemidir; bağlamalar, *Giriş bağlamaları*, *Çıkış bağlamaları*veya her ikisi için de bağlanabilir. Bağlamalardan alınan veriler işleve parametre olarak sağlanır.

Farklı bağlamaları, gereksinimlerinize uyacak şekilde karıştırabilir ve eşleştirebilirsiniz. Bağlamalar isteğe bağlıdır ve bir işlevde bir veya daha fazla giriş ve/veya çıkış bağlaması olabilir.

Tetikleyiciler ve bağlamalar, diğer hizmetlere erişimi bir şekilde kodlamadan kaçınmanızı sağlar. İşleviniz, işlev parametrelerinde verileri (örneğin, bir kuyruk iletisinin içeriği) alır. İşlevin dönüş değerini kullanarak verileri (örneğin, kuyruk iletisi oluşturmak için) gönderirsiniz. 

Farklı işlevleri nasıl uygulayabileceğinizi gösteren aşağıdaki örnekleri göz önünde bulundurun.

| Örnek senaryo | Tetikleyici | Giriş bağlama | Çıkış bağlama |
|-------------|---------|---------------|----------------|
| Başka bir kuyruğa yazmak için bir işlev çalıştıran yeni bir kuyruk iletisi ulaştı. | Sıradaki<sup>*</sup> | *Yok.* | Sıradaki<sup>*</sup> |
|Zamanlanan bir iş BLOB depolama içeriğini okur ve yeni bir Cosmos DB belgesi oluşturur. | Zamanlayıcı | Blob Depolama Alanı | Cosmos DB |
|Event Grid, blob depolamadan bir görüntüyü okumak için ve Cosmos DB bir e-posta göndermek için kullanılır. | Event Grid | BLOB depolama ve Cosmos DB | SendGrid |
| Excel sayfasını güncelleştirmek için Microsoft Graph kullanan bir Web kancası. | HTTP | *Yok.* | Microsoft Graph |

<sup>\*</sup>Farklı kuyrukları temsil eder

Bu örneklerin kapsamlı olması amaçlıyordu, ancak Tetikleyicileri ve bağlamaları birlikte nasıl kullanabileceğinizi göstermek için sağlanmıştır.

###  <a name="trigger-and-binding-definitions"></a>Tanımları tetikleme ve bağlama

Tetikleyiciler ve bağlamalar, geliştirme yaklaşımına göre farklı şekilde tanımlanır.

| Platform | Tetikleyiciler ve bağlamalar tarafından yapılandırılır... |
|-------------|--------------------------------------------|
| C#sınıf kitaplığı | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;öznitelikleri ile C# yöntemleri ve parametreleri dekorasyon |
| Diğerlerinin tümü (Azure portal dahil) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[function. JSON](./functions-reference.md) güncelleştiriliyor ([şema](http://json.schemastore.org/function)) |

Portal bu yapılandırma için bir kullanıcı arabirimi sağlar, ancak işlevinizin **tümleştirin** sekmesi aracılığıyla bulunan **Gelişmiş düzenleyiciyi** açarak dosyayı doğrudan düzenleyebilirsiniz.

.NET ' te, parametre türü giriş verileri için veri türünü tanımlar. Örneğin, bir kuyruk `string` tetikleyicisinin metnini bağlamak için kullanın, ikili olarak okunacak bir bayt dizisi ve bir nesneye serbest bırakmak için özel bir tür.

JavaScript gibi dinamik olarak yazılan diller için, *function. JSON* dosyasındaki `dataType` özelliğini kullanın. Örneğin, bir http isteğinin içeriğini ikili biçimde okumak için şu şekilde `dataType` `binary`ayarlayın:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

İçin `dataType` diğer seçenekler ve `stream` ' `string`dir.

## <a name="binding-direction"></a>Bağlama yönü

Tüm tetikleyiciler ve bağlamalar `direction` [function. JSON](./functions-reference.md) dosyasında bir özelliğe sahiptir:

- Tetikleyiciler için, yön her zaman`in`
- Giriş ve çıkış bağlamaları kullanımı `in` ve`out`
- Bazı bağlamalar özel bir yönü `inout`destekler. Kullanıyorsanız, portalda tümleştir sekmesi aracılığıyla yalnızca **Gelişmiş Düzenleyici** kullanılabilir. `inout`

Tetikleyicileri ve bağlamaları yapılandırmak için [bir sınıf kitaplığındaki öznitelikleri](functions-dotnet-class-library.md) kullandığınızda, yön bir öznitelik oluşturucusunda sağlanır veya parametre türünden çıkartılan olur.

## <a name="supported-bindings"></a>Desteklenen bağlamalar

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Önizleme aşamasında olan veya üretim kullanımı için onaylanan bağlamalar hakkında daha fazla bilgi için bkz. [desteklenen diller](supported-languages.md).

## <a name="resources"></a>Kaynaklar
- [Bağlama ifadeleri ve desenleri](./functions-bindings-expressions-patterns.md)
- [Azure Işlevi dönüş değerini kullanma](./functions-bindings-return-value.md)
- [Bağlama ifadesi kaydetme](./functions-bindings-register.md)
- Edici
  - [Azure Işlevlerinde kodunuzu test etme stratejileri](functions-test-a-function.md)
  - [HTTP ile tetiklenen bir işlevi el ile çalıştırma](functions-manually-run-non-http.md)
- [Bağlama hatalarını işleme](./functions-bindings-errors.md)

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Azure Işlevleri bağlama uzantılarını kaydetme](./functions-bindings-register.md)
