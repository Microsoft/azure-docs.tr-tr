---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9b3859d854b3900cc30eda6a95b8425da6763e59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94340950"
---
Bağlama öznitelikleri, dosyasında function.jsdoğrudan tanımlanmıştır. Bağlama türüne bağlı olarak ek özellikler gerekli olabilir. [Kuyruk çıkış yapılandırması](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) , bir Azure depolama kuyruğu bağlaması için gereken alanları açıklar. Uzantı, dosyadaki function.jsbağlama eklemeyi kolaylaştırır. 

Bir bağlama oluşturmak için, HttpTrigger klasörünüzdeki dosyayı sağ tıklatın (macOS üzerinde CTRL + tıklama) `function.json` ve **bağlama Ekle...** seçeneğini belirleyin. Yeni bağlama yönelik aşağıdaki bağlama özelliklerini tanımlamak için istemleri izleyin:

| İstem | Değer | Açıklama |
| -------- | ----- | ----------- |
| **Bağlama yönünü seçin** | `out` | Bağlama bir çıkış bağlamadır. |
| **Yön içeren bağlamayı seçin...** | `Azure Queue Storage` | Bağlama bir Azure depolama kuyruğu bağlamadır. |
| **Kodunuzda bu bağlamayı tanımlamak için kullanılan ad** | `msg` | Kodunuzda başvurulan bağlama parametresini tanımlayan ad. |
| **İletinin gönderileceği kuyruk** | `outqueue` | Bağlamanın yazdığı kuyruğun adı. *SıraAdı* mevcut olmadığında, bağlama ilk kullanımda oluşturulur. |
| **"local.setting.json" ayarını seçin** | `AzureWebJobsStorage` | Depolama hesabı için bağlantı dizesini içeren bir uygulama ayarının adı. `AzureWebJobsStorage`Ayar, işlev uygulamasıyla oluşturduğunuz depolama hesabı için bağlantı dizesini içerir. |

Üzerinde function.jsdizisine eklenen bir bağlama `bindings` , aşağıdaki gibi görünmelidir:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::
