---
title: MongoDB için Azure Cosmos DB API 'de çok belgeli işlemler kullanma
description: MongoDB 4,0 için Azure Cosmos DB API 'sindeki bir sabit koleksiyonda çok belgeli bir işlem (hepsi veya-Nothing anlam) yürütemeyen örnek bir Mongo kabuğu uygulaması oluşturmayı öğrenin.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: f319db76c8aee5a2a35ff8ca9670c42089350ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692479"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'de çok belgeli işlemler kullanma
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Bu makalede, sunucu sürümü 4,0 ile MongoDB için Azure Cosmos DB API 'sindeki bir sabit koleksiyonda çok belgeli bir işlem yürüten Mongo kabuğu uygulaması oluşturacaksınız.

## <a name="what-are-multi-document-transactions"></a>Çok belgeli işlemler nelerdir?

MongoDB için Azure Cosmos DB API 'sinde, tek bir belgedeki işlemler atomik bir belgedir. Çoklu belge işlemleri, uygulamaların birden çok belge arasında Atomik işlemler yürütmesine olanak sağlar. İşlemlere "hepsi veya-Nothing" semantiğini sunar. İşlemede, işlemler içinde yapılan değişiklikler kalıcıdır ve işlem başarısız olursa, işlem içindeki tüm değişiklikler atılır.

Çok belgeli işlemler, **ACID** semantiğini izler:

* Atomicity: tüm işlemler bir tane olarak değerlendirildi
* Tutarlılık: kaydedilen veri geçerli
* Yalıtım: diğer işlemlerden yalıtılmış
* Dayanıklılık: istemci istediğinde Işlem verileri kalıcıdır

## <a name="requirements"></a>Gereksinimler

Çok belgeli işlemler, API sürüm 4,0 ' deki bir parçalı bir koleksiyon içinde desteklenir. Çoklu belge işlemleri koleksiyonlar genelinde veya 4,0 sürümündeki parçalı koleksiyonlar arasında desteklenmez. İşlemler için zaman aşımı sabit 5 saniyedir.

Tel Protokolü sürüm 4,0 veya üstünü destekleyen tüm sürücüler, MongoDB çok belgeli işlemler için Azure Cosmos DB API 'YI destekleyecektir.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>MongoDB kabuğu 'nda çok belgeli işlemler çalıştırma
> [!Note]
> Bu örnek, MongoDB pusula içinde Embedded MongoSH Beta (Shell) içinde çalışmaz.

1. Bir komut istemi açın, Mongo kabuğu sürüm 4,0 ve üzeri sürümünün yüklü olduğu dizine gidin:

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Mongo kabuğu betik *connect_friends.js* oluşturun ve aşağıdaki içeriği ekleyin

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. Çoklu belge işlemini yürütmek için aşağıdaki komutu çalıştırın. Ana bilgisayar, bağlantı noktası, Kullanıcı ve anahtar Azure portal bulunabilir.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>Sonraki adımlar

[MongoDB 4,0 için Azure Cosmos DB API](mongodb-feature-support-40.md) 'deki yenilikleri keşfet
