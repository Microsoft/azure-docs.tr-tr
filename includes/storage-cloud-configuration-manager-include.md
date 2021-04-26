---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028538"
---
[.NET için Microsoft Azure Yapılandırma Yöneticisi Kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/), yapılandırma dosyasından bağlantı dizesini ayrıştırmak için bir sınıf sağlar. [Cloudconfigurationmanager](/previous-versions/azure/reference/mt634650(v=azure.100)) sınıfı yapılandırma ayarlarını ayrıştırır. Masaüstünde, bir mobil cihazda, bir Azure sanal makinesinde veya Azure bulut hizmetinde çalışan istemci uygulamalarına yönelik ayarları ayrıştırır.

Pakete başvurmak için `CloudConfigurationManager` aşağıdaki `using` yönergeleri ekleyin:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

Burada, yapılandırma dosyasından bir bağlantı dizesinin nasıl alındığını gösteren bir örnek bulunmaktadır:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Azure Yapılandırma Yöneticisi'ni kullanmak isteğe bağlıdır. .NET Framework [ConfigurationManager sınıfı](/dotnet/api/system.configuration.configurationmanager)gıbı bir API de kullanabilirsiniz.