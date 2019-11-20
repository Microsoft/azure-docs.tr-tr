---
title: Azure Cosmos DB 'de Azure Tablo depolama desteği
description: Azure Cosmos DB Tablo API'si ile Azure Depolama Tabloları'nın birlikte nasıl çalıştığını öğrenin.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 08/05/2019
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 71bc5d73a7b5bc83dc1ac835c80ac1b14d5113e9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814707"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Tablo API'si ve Azure Tablo depolaması ile geliştirme

Azure Cosmos DB Tablo API'si ve Azure Tablo depolaması aynı tablo veri modelini paylaşır ve SDK'ları aracılığıyla aynı oluşturma, silme, güncelleştirme ve sorgulama işlemlerini gösterir. 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Tablo API'siyle geliştirme

Şu anda [Azure Cosmos DB Tablo API'sinin](table-introduction.md) geliştirme için kullanılabilen dört SDK'sı vardır: 

* [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Bu kitaplık .NET Standard ve genel [Windows Azure depolama SDK 'sı](https://www.nuget.org/packages/WindowsAzure.Storage)ile aynı sınıflara ve Yöntem imzalarına sahiptir, ancak aynı zamanda Tablo API'si kullanarak Azure Cosmos DB hesaplara bağlanma imkanına sahiptir. [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) .NET Framework Library kullanıcıları bakım modunda olduğundan [Microsoft. Azure. Cosmos. Table ' a](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) yükseltmeniz önerilir ve yakında kullanımdan kalkacaktır.

* [Python SDK 'sı](table-sdk-python.md): Yeni Azure Cosmos DB Python SDK'sı, Python'da Azure Tablo depolamasını destekleyen tek SDK'sıdır. Bu SDK hem Azure Tablo depolaması hem de Azure Cosmos DB Tablo API'sine bağlanır.

* [Java SDK 'sı](table-sdk-java.md): Bu Azure Depolama SDK'sı, Tablo API'sini kullanarak Azure Cosmos DB hesaplarına bağlanabilir.

* [Node. js SDK 'sı](table-sdk-nodejs.md): Bu Azure Depolama SDK'sı, Tablo API'sini kullanarak Azure Cosmos DB hesaplarına bağlanabilir.


Tablo API'si ile çalışma hakkında ek bilgiler, [SSS bölümünde bulunabilir: Tablo API'si](faq.md#table) makaleyle geliştirin.

## <a name="developing-with-azure-table-storage"></a>Azure Tablo depolamasıyla geliştirme

Azure Tablo depolamasının geliştirme için kullanılabilen SDK'ları vardır:

- [WindowsAzure.Storage .NET SDK'sı](https://www.nuget.org/packages/WindowsAzure.Storage/). Bu kitaplık, depolama Tablo hizmetiyle çalışmanıza olanak tanır.
- [Python SDK’sı](https://github.com/Azure/azure-cosmos-table-python). Python için Azure Cosmos DB tablo SDK 'Sı tablo depolama hizmetini destekler (Azure Tablo depolama ve Cosmos DB Tablo API'si aynı özellikleri ve işlevleri paylaştığından ve SDK geliştirme çabalarımızı bir çaba ile paylaşmak için bu SDK 'yı kullanmanızı öneririz).
- [Java için Azure Depolama SDK'sı](https://github.com/azure/azure-storage-java). Bu Azure Depolama SDK'sı, Azure Tablo depolamasını kullanmak için Java'da bir istemci kitaplığı sağlar.
- [Node.js SDK’sı](https://github.com/Azure/azure-storage-node). Bu SDK, depolama Tablo hizmetini kullanmak için bir Node.js paketi ve tarayıcıyla uyumlu bir JavaScript istemci kitaplığı sağlar.
- [AzureRmStorageTable PowerShell modülü](https://www.powershellgallery.com/packages/AzureRmStorageTable). Bu PowerShell modülünün depolama Tablolarıyla çalışmak için cmdlet'leri vardır.
- [C++ için Azure Depolama İstemcisi Kitaplığı](https://github.com/Azure/azure-storage-cpp/). Bu kitaplık Azure Depolama için uygulamalar oluşturmanıza olanak tanır.
- [Ruby için Azure Depolama Tablosu İstemci Kitaplığı](https://github.com/azure/azure-storage-ruby/tree/master/table). Bu proje, Azure depolaması Tablo hizmetlerine erişmeyi kolaylaştıran bir Ruby paketi sağlar.
- [Azure Depolama Tablosu PHP İstemci Kitaplığı](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Bu proje, Azure depolaması Tablo hizmetlerine erişmeyi kolaylaştıran bir PHP istemci kitaplığı sağlar.


   





