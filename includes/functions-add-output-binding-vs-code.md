---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493963"
---
Visual Studio Code, uygun bir istem kümesini izleyerek dosya üzerinde function.jsbağlama eklemenize olanak tanır. 

Bir bağlama eklemek için, komut paleti ' ni (F1) açın ve **Azure işlevleri: bağlama Ekle...** yazın, yeni bağlama için işlevi seçin ve sonra, işleve eklenmekte olan bağlamanın türüne bağlı olarak değişen istemleri izleyin. 

Aşağıda yeni bir depolama çıkış bağlaması tanımlamak için örnek istemler verilmiştir:

| İstem | Değer | Açıklama |
| -------- | ----- | ----------- |
| **Bağlama yönünü seçin** | `out` | Bağlama bir çıkış bağlamadır. |
| **Yön içeren bağlamayı seçin** | `Azure Queue Storage` | Bağlama bir Azure depolama kuyruğu bağlamadır. |
| **Kodunuzda bu bağlamayı tanımlamak için kullanılan ad** | `msg` | Kodunuzda başvurulan bağlama parametresini tanımlayan ad. |
| **İletinin gönderileceği kuyruk** | `outqueue` | Bağlamanın yazdığı kuyruğun adı. *SıraAdı* mevcut olmadığında, bağlama ilk kullanımda oluşturulur. |
| **"local.settings.json" ayarını seçin** | `MyStorageConnection` | Depolama hesabı için bağlantı dizesini içeren bir uygulama ayarının adı. `AzureWebJobsStorage`Ayar, işlev uygulamasıyla oluşturduğunuz depolama hesabı için bağlantı dizesini içerir. |

Ayrıca, işlev klasörünüzdeki dosyadaki **function.js** doğrudan sağ tıklayıp (MacOS üzerinde CTRL + tıklama), **bağlama Ekle**' yi seçebilir ve aynı istemleri izleyebilirsiniz.

Bu örnekte, aşağıdaki bağlama `bindings` dosyadaki function.jsdizisine eklenir:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```