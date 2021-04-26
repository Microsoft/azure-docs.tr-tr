---
title: Azure Cosmos DB içinde benzersiz anahtarlar kullanın
description: Azure Cosmos veritabanı için benzersiz anahtarların nasıl tanımlanacağını ve kullanılacağını öğrenin. Bu makalede ayrıca benzersiz anahtarların bir veri bütünlüğü katmanı nasıl ekleneceği açıklanmaktadır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 9eb2b916bfe6c73a1535afb077b04fbb081dd5f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685729"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Azure Cosmos DB 'de benzersiz anahtar kısıtlamaları
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Benzersiz anahtarlar bir Azure Cosmos kapsayıcısına veri bütünlüğü katmanı ekler. Azure Cosmos kapsayıcısı oluştururken benzersiz bir anahtar ilkesi oluşturursunuz. Benzersiz anahtarlarla, bir mantıksal bölüm içindeki bir veya daha fazla değerin benzersiz olduğundan emin olun. Ayrıca [bölüm anahtarı](partitioning-overview.md)başına benzersizliği garanti edebilirsiniz.

Benzersiz anahtar ilkesiyle bir kapsayıcı oluşturduktan sonra, benzersiz anahtar kısıtlaması tarafından belirtilen şekilde, bir mantıksal bölüm içinde yinelenerek yeni veya mevcut bir öğenin güncelleştirilmesi engellenir. Benzersiz anahtarla birlikte birleştirilmiş bölüm anahtarı, kapsayıcının kapsamındaki bir öğenin benzersizlik düzeyini garanti eder.

Örneğin, `Email address` benzersiz anahtar kısıtlaması ve bölüm anahtarı olarak bir Azure Cosmos kapsayıcısını göz önünde bulundurun `CompanyID` . Kullanıcının e-posta adresini benzersiz bir anahtarla yapılandırdığınızda, her öğe belirli bir içinde benzersiz bir e-posta adresine sahiptir `CompanyID` . Yinelenen e-posta adresleriyle ve aynı bölüm anahtarı değeriyle iki öğe oluşturulamıyor. Azure Cosmos DB SQL (Core) API 'sinde, öğeler JSON değerleri olarak depolanır. Bu JSON değerleri büyük/küçük harfe duyarlıdır. Benzersiz anahtar olarak bir özellik seçtiğinizde, bu özellik için büyük/küçük harfe duyarlı değerler ekleyebilirsiniz. Örneğin, ad özelliğinde tanımlanmış benzersiz bir anahtarınız varsa, "Gaby", "Gaby" öğesinden farklıdır ve her ikisini de kapsayıcıya ekleyebilirsiniz.

Aynı e-posta adresine sahip olan ancak aynı adı, soyadı ve e-posta adresini içermeyen öğeler oluşturmak için benzersiz anahtar ilkesine daha fazla yol ekleyin. Yalnızca e-posta adresini temel alan benzersiz bir anahtar oluşturmak yerine, ad, soyadı ve e-posta adresi birleşimini içeren benzersiz bir anahtar da oluşturabilirsiniz. Bu anahtar, bileşik benzersiz anahtar olarak bilinir. Bu durumda, belirli bir verilen içindeki üç değerin her benzersiz birleşimine `CompanyID` izin verilir. 

Örneğin, kapsayıcı, her öğenin benzersiz anahtar kısıtlamasını aldığı aşağıdaki değerlere sahip öğeler içerebilir.

|CompanyID|Ad|Soyadı|E-posta adresi|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Önceki tabloda listelenen birleşimlerle başka bir öğe eklemeye çalışırsanız bir hata alırsınız. Hata, benzersiz anahtar kısıtlamasının karşılanmadığını gösterir. `Resource with specified ID or name already exists`Dönüş iletisi ya da `Resource with specified ID, name, or unique index already exists` olarak alırsınız. 

## <a name="define-a-unique-key"></a>Benzersiz bir anahtar tanımlayın

Yalnızca bir Azure Cosmos kapsayıcısı oluşturduğunuzda, benzersiz anahtarlar tanımlayabilirsiniz. Benzersiz bir anahtar, mantıksal bir bölümü kapsamlandırılır. Önceki örnekte, kapsayıcıyı ZIP koduna göre bölümleyeceğinize, her mantıksal bölümdeki Yinelenen öğelerle sonlandırın. Benzersiz anahtarlar oluştururken aşağıdaki özellikleri göz önünde bulundurun:

* Var olan bir kapsayıcıyı farklı bir benzersiz anahtar kullanacak şekilde güncelleştiremezsiniz. Diğer bir deyişle, benzersiz bir anahtar ilkesiyle bir kapsayıcı oluşturulduktan sonra ilke değiştirilemez.

* Mevcut bir kapsayıcı için benzersiz bir anahtar ayarlamak için, benzersiz anahtar kısıtlamasına sahip yeni bir kapsayıcı oluşturun. Mevcut kapsayıcıdan yeni kapsayıcıya veri taşımak için uygun veri geçiş aracını kullanın. SQL kapsayıcıları için veri taşıma [aracını](import-data.md) kullanarak verileri taşıyın. MongoDB kapsayıcıları için [mongoimport.exe veya mongorestore.exe](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) kullanarak verileri taşıyın.

* Benzersiz bir anahtar ilkesinde en fazla 16 yol değeri olabilir. Örneğin, değerleri, `/firstName` `/lastName` ve olabilir `/address/zipCode` . Her benzersiz anahtar ilkesi en fazla 10 benzersiz anahtar kısıtlaması veya birleşimine sahip olabilir. Her benzersiz dizin kısıtlamasının birleştirilmiş yolları 60 baytı aşmamalıdır. Önceki örnekte adı, soyadı ve e-posta adresi birlikte tek bir kısıtlamadır. Bu kısıtlama, 16 olası yoldan 3 ' ü kullanır.

* Bir kapsayıcının benzersiz bir anahtar ilkesi olduğunda, bir öğeyi oluşturmak, güncelleştirmek ve silmek için [Istek birimi (ru)](request-units.md) ücretleri biraz daha yüksektir.

* Seyrek benzersiz anahtarlar desteklenmiyor. Bazı benzersiz yol değerleri eksikse, bu değerler benzersizlik kısıtlamasındaki bir parçasını oluşturan null değer olarak değerlendirilir. Bu nedenle, bu kısıtlamayı karşılamak için yalnızca null değeri olan tek bir öğe olabilir.

* Benzersiz anahtar adları büyük/küçük harfe duyarlıdır. Örneğin, benzersiz anahtar kısıtlaması olarak ayarlanmış bir kapsayıcı düşünün `/address/zipcode` . Verilerinizde adlı bir alan varsa `ZipCode` , Azure Cosmos DB aynı olmadığı için benzersiz anahtar olarak "null" ekler `zipcode` `ZipCode` . Bu durumda, aynı "null" değeri benzersiz anahtar kısıtlamasını ihlal ettiğinden, ZipCode içeren diğer tüm kayıtlar eklenemez.

## <a name="next-steps"></a>Sonraki adımlar

* [Mantıksal bölümler](partitioning-overview.md) hakkında daha fazla bilgi edinin
* Kapsayıcı oluştururken [benzersiz anahtarların nasıl tanımlanacağını](how-to-define-unique-keys.md) keşfet
