---
title: Azure Cosmos DB 'de Azure Tablo depolama desteği
description: Azure Cosmos DB Tablo API'si ve Azure depolama tablolarının aynı tablo veri modelini bir işlem paylaşarak nasıl birlikte çalıştığını öğrenin
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 01/08/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: c2b2a9a03a654e8ccba58e62a1d017cd11db5abc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050769"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Tablo API'si ve Azure Tablo depolaması ile geliştirme
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB Tablo API'si ve Azure Tablo depolaması aynı tablo veri modelini paylaşır ve SDK'ları aracılığıyla aynı oluşturma, silme, güncelleştirme ve sorgulama işlemlerini gösterir.

> [!NOTE]
> [Sunucusuz kapasite modu](serverless.md) artık Azure Cosmos db tablo API'si kullanılabilir.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Tablo API'siyle geliştirme

Şu anda [Azure Cosmos DB Tablo API'sinin](table-introduction.md) geliştirme için kullanılabilen dört SDK'sı vardır: 

* [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Bu kitaplık .NET Standard ve genel [Windows Azure depolama SDK 'sı](https://www.nuget.org/packages/WindowsAzure.Storage)ile aynı sınıflara ve Yöntem imzalarına sahiptir, ancak aynı zamanda Tablo API'si kullanarak Azure Cosmos DB hesaplara bağlanma imkanına sahiptir. [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) .NET Framework Library kullanıcıları bakım modunda olduğundan [Microsoft. Azure. Cosmos. Table ' a](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) yükseltmeniz önerilir ve yakında kullanımdan kalkacaktır.

* [Python SDK](table-sdk-python.md): yeni Azure Cosmos DB Python SDK 'Sı, Python 'Da Azure Tablo Depolamayı destekleyen tek SDK 'dir. Bu SDK hem Azure Tablo depolaması hem de Azure Cosmos DB Tablo API'sine bağlanır.

* [Java SDK 'sı](table-sdk-java.md): Bu Azure Storage sdk, Tablo API'si kullanarak Azure Cosmos DB hesaplara bağlanabilir.

* [Node.js SDK](table-sdk-nodejs.md): Bu Azure depolama SDK 'sı, Tablo API'si kullanarak Azure Cosmos DB hesaplara bağlanabilir.


Tablo API'siyle çalışma hakkındaki ek bilgileri [SSS: Tablo API'siyle geliştirme](table-api-faq.md) makalesinde bulabilirsiniz.

## <a name="developing-with-azure-table-storage"></a>Azure Tablo depolamasıyla geliştirme

Azure Tablo depolamasının geliştirme için kullanılabilen SDK'ları vardır:

- [Microsoft. Azure. Storage. blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/), [Microsoft. Azure. Storage. File](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/), [Microsoft. Azure. Storage. Queue](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)ve [Microsoft. Azure. Storage. Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) kitaplıkları Azure Tablo depolama hizmeti ile çalışmanıza olanak sağlar. Azure Cosmos DB Tablo API'si kullanıyorsanız, bunun yerine [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) kitaplığını kullanabilirsiniz.
- [Python SDK 'sı](https://github.com/Azure/azure-cosmos-table-python). Python için Azure Cosmos DB tablo SDK 'Sı tablo depolama hizmetini destekler (Azure Tablo depolama ve Cosmos DB Tablo API'si aynı özellikleri ve işlevleri paylaştığından ve SDK geliştirme çabalarımızı bir çaba ile paylaşmak için bu SDK 'yı kullanmanızı öneririz).
- [Java Için Azure depolama SDK 'sı](https://github.com/azure/azure-storage-java). Bu Azure Depolama SDK'sı, Azure Tablo depolamasını kullanmak için Java'da bir istemci kitaplığı sağlar.
- [ SDKNode.js](https://github.com/Azure/azure-storage-node). Bu SDK, depolama Tablo hizmetini kullanmak için bir Node.js paketi ve tarayıcıyla uyumlu bir JavaScript istemci kitaplığı sağlar.
- [AzureRmStorageTable PowerShell modülü](https://www.powershellgallery.com/packages/AzureRmStorageTable). Bu PowerShell modülünün depolama Tablolarıyla çalışmak için cmdlet'leri vardır.
- [C++ için Azure Depolama İstemcisi Kitaplığı](https://github.com/Azure/azure-storage-cpp/). Bu kitaplık Azure Depolama için uygulamalar oluşturmanıza olanak tanır.
- [Ruby için Azure Depolama Tablosu İstemci Kitaplığı](https://github.com/azure/azure-storage-ruby/tree/master/table). Bu proje, Azure depolaması Tablo hizmetlerine erişmeyi kolaylaştıran bir Ruby paketi sağlar.
- [Azure Depolama Tablosu PHP İstemci Kitaplığı](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Bu proje, Azure depolaması Tablo hizmetlerine erişmeyi kolaylaştıran bir PHP istemci kitaplığı sağlar.


   





