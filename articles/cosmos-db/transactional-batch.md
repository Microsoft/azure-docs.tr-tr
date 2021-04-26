---
title: .NET SDK kullanarak Azure Cosmos DB işlem toplu işlem işlemleri
description: Başarılı veya başarısız olan bir grup nokta işlemini gerçekleştirmek için Azure Cosmos DB .NET SDK 'sında TransactionalBatch 'i nasıl kullanacağınızı öğrenin.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9f6692db2da3722507136a468d1dcbdc2985e73f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97347566"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>.NET SDK kullanarak Azure Cosmos DB işlem toplu işlem işlemleri
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

İşlem toplu işi, bir kapsayıcıda aynı bölüm anahtarıyla başarılı veya başarısız olması gereken bir dizi nokta işlemi tanımlar. .NET SDK 'sında, `TransactionalBatch` sınıfı bu işlem toplu işlemini tanımlamak için kullanılır. Tüm işlemler, işlem toplu işlem içinde açıklandıkları sırada başarılı olursa, işlem kaydedilir. Ancak, herhangi bir işlem başarısız olursa, tüm işlem geri alınır.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Azure Cosmos DB bir işlem nedir?

Tipik bir veritabanındaki bir işlem, tek bir mantıksal iş birimi olarak gerçekleştirilen bir işlem dizisi olarak tanımlanabilir. Her işlem, ACID (Atomicity, tutarlılık, yalıtım, dayanıklılık) özellik garantisi sağlar.

* **Atomicity** , bir işlem içinde gerçekleştirilen tüm işlemlerin tek bir birim olarak değerlendirildiğinden ve tümünün yürütüldüğünden ya da hiçbirinin olmadığından emin garanti eder.
* **Tutarlılık** , verilerin işlemler arasında her zaman geçerli bir durumda olduğundan emin olmanızı sağlar.
* **Yalıtım** , birbirleriyle iki işlemin kesintiye uğratılmasını garanti eder; birçok ticari sistem, uygulama ihtiyaçlarına göre kullanılabilecek birden fazla yalıtım düzeyi sağlar.
* **Dayanıklılık** , bir veritabanında kaydedilen tüm değişikliklerin her zaman mevcut olmasını sağlar.
Azure Cosmos DB, aynı [mantıksal bölüm anahtarı](partitioning-overview.md)içindeki işlemler için [anlık görüntü YALıTıMıYLA tam ACID uyumlu işlemleri](database-transactions-optimistic-concurrency.md) destekler.

## <a name="transactional-batch-operations-vs-stored-procedures"></a>İşlem toplu işlem işlemleri Ile saklı yordamlar

Azure Cosmos DB Şu anda işlemler üzerindeki işlem kapsamını da sağlayan saklı yordamları desteklemektedir. Ancak, işlem toplu işlem işlemleri aşağıdaki avantajları sunar:

* **Dil seçeneği** – işlem toplu işi, zaten kullandığınız SDK ve dilde desteklenir, ancak saklı yordamların JavaScript 'te yazılması gerekir.
* **Kod sürümü oluşturma** – uygulama kodunun sürümü oluşturma ve CI/CD işlem hattınıza ekleme, saklı bir yordamın güncelleştirilmesini düzenleyen ve geçişin doğru zamanda gerçekleşmediğinden emin olmak için çok daha doğal hale gelir. Ayrıca, değişiklikleri geri alma daha kolay hale gelir.
* **Performans** : saklı yordam yürütme ile karşılaştırıldığında, denk işlemlerde %30 ' a varan gecikme süresi azaltılır.
* **İçerik serileştirme** – bir işlem toplu işlemindeki her işlem, yükü için özel serileştirme seçeneklerinden yararlanabilir.

## <a name="how-to-create-a-transactional-batch-operation"></a>İşlem Batch işlemi oluşturma

Bir işlem toplu işlem işlemi oluştururken, bir kapsayıcı örneğinden başlayıp çağrısı yapmanız gerekir `CreateTransactionalBatch` :

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

Ardından, `ExecuteAsync` Batch üzerinde çağırmanız gerekir:

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

Yanıt alındıktan sonra, başarılı olup olmadığını inceleyin ve sonuçları ayıklayın:

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

Bir hata oluşursa, başarısız olan işlem ilgili hatanın durum koduna sahip olur. Diğer tüm işlemlerin 424 durum kodu (başarısız bağımlılığı) olacaktır. Aşağıdaki örnekte, zaten var olan bir öğe (409 HttpStatusCode. Conflict) oluşturmaya çalıştığı için işlem başarısız olur. Durum kodu, bir işlemin hatanın nedenini belirlemesine olanak sağlar.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>İşlem toplu işlem işlemleri nasıl yürütülür

`ExecuteAsync`Yöntemi çağrıldığında, nesnedeki tüm işlemler `TransactionalBatch` gruplandırılır, tek bir yükte serileştirilir ve Azure Cosmos DB hizmetine tek bir istek olarak gönderilir.

Hizmet, isteği alır ve tüm işlemleri bir işlem kapsamı içinde yürütür ve aynı serileştirme protokolünü kullanarak bir yanıt döndürür. Bu yanıt bir başarılı veya bir hata ya da işlem başına bireysel işlem yanıtları sağlar.

SDK, sonucu doğrulamak için size yanıtı gösterir ve isteğe bağlı olarak, iç işlem sonuçlarının her birini ayıklar.

## <a name="limitations"></a>Sınırlamalar

Şu anda iki bilinen sınır vardır:

* Azure Cosmos DB istek boyutu sınırı, `TransactionalBatch` yükün boyutunu 2 MB 'ı aşmayacak şekilde kısıtlar ve en fazla yürütme süresi 5 saniyedir.
* `TransactionalBatch`Performansın beklenen ve SLA 'ların içinde olduğundan emin olmak için 100 işlem başına geçerli bir sınır vardır.

## <a name="next-steps"></a>Sonraki adımlar

* [TransactionalBatch ile neler](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch) yapabileceğiniz hakkında daha fazla bilgi edinin

* Cosmos DB .NET SDK 'umuzu kullanmanın daha fazla yolu için [örneklerimizi](sql-api-dotnet-v3sdk-samples.md) ziyaret edin
