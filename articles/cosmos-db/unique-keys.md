---
title: Azure Cosmos DB içinde benzersiz anahtarlar kullanın
description: Azure Cosmos veritabanınızda benzersiz anahtarların nasıl kullanılacağını öğrenin
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 4a929566d464f8548c4bffeb9f89099e77722e67
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756784"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Azure Cosmos DB 'de benzersiz anahtar kısıtlamaları

Benzersiz anahtarlar bir Azure Cosmos kapsayıcısına veri bütünlüğü katmanı ekler. Azure Cosmos kapsayıcısı oluştururken benzersiz bir anahtar ilkesi oluşturursunuz. Benzersiz anahtarlarla, bir mantıksal bölüm içindeki bir veya daha fazla değerin benzersiz olduğundan emin olun. Ayrıca [bölüm anahtarı](partition-data.md)başına benzersizliği garanti edebilirsiniz. 

Benzersiz anahtar ilkesiyle bir kapsayıcı oluşturduktan sonra, benzersiz anahtar kısıtlaması tarafından belirtilen şekilde, bir mantıksal bölüm içinde yinelenerek yeni veya mevcut bir öğenin güncelleştirilmesi engellenir. Benzersiz anahtarla birlikte birleştirilmiş bölüm anahtarı, kapsayıcının kapsamındaki bir öğenin benzersizlik düzeyini garanti eder.

Örneğin, benzersiz anahtar kısıtlaması olarak e-posta adresi ile bir Azure Cosmos kapsayıcısını ve bölüm anahtarı olarak `CompanyID` göz önünde bulundurun. Kullanıcının e-posta adresini benzersiz bir anahtarla yapılandırdığınızda, her öğenin belirli bir `CompanyID`için benzersiz bir e-posta adresi vardır. Yinelenen e-posta adresleriyle ve aynı bölüm anahtarı değeriyle iki öğe oluşturulamıyor. 

Aynı e-posta adresine sahip olan ancak aynı adı, soyadı ve e-posta adresini içermeyen öğeler oluşturmak için benzersiz anahtar ilkesine daha fazla yol ekleyin. Yalnızca e-posta adresini temel alan benzersiz bir anahtar oluşturmak yerine, ad, soyadı ve e-posta adresi birleşimini içeren benzersiz bir anahtar da oluşturabilirsiniz. Bu anahtar, bileşik benzersiz anahtar olarak bilinir. Bu durumda, belirli bir `CompanyID` içindeki üç değerin her benzersiz birleşimine izin verilir. 

Örneğin, kapsayıcı, her öğenin benzersiz anahtar kısıtlamasını aldığı aşağıdaki değerlere sahip öğeler içerebilir.

|CompanyID|Ad|Soyadı|E-posta adresi|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Önceki tabloda listelenen birleşimlerle başka bir öğe eklemeye çalışırsanız bir hata alırsınız. Hata, benzersiz anahtar kısıtlamasının karşılanmadığını gösterir. Dönüş iletisi olarak `Resource with specified ID or name already exists` ya da `Resource with specified ID, name, or unique index already exists` alırsınız. 

## <a name="define-a-unique-key"></a>Benzersiz bir anahtar tanımlayın

Yalnızca bir Azure Cosmos kapsayıcısı oluşturduğunuzda, benzersiz anahtarlar tanımlayabilirsiniz. Benzersiz bir anahtar, mantıksal bir bölümü kapsamlandırılır. Önceki örnekte, kapsayıcıyı ZIP koduna göre bölümleyeceğinize, her mantıksal bölümdeki Yinelenen öğelerle sonlandırın. Benzersiz anahtarlar oluştururken aşağıdaki özellikleri göz önünde bulundurun:

* Var olan bir kapsayıcıyı farklı bir benzersiz anahtar kullanacak şekilde güncelleştiremezsiniz. Diğer bir deyişle, benzersiz bir anahtar ilkesiyle bir kapsayıcı oluşturulduktan sonra ilke değiştirilemez.

* Mevcut bir kapsayıcı için benzersiz bir anahtar ayarlamak için, benzersiz anahtar kısıtlamasına sahip yeni bir kapsayıcı oluşturun. Mevcut kapsayıcıdan yeni kapsayıcıya veri taşımak için uygun veri geçiş aracını kullanın. SQL kapsayıcıları için veri taşıma [aracını](import-data.md) kullanarak verileri taşıyın. MongoDB kapsayıcıları için [mongoımport. exe veya mongorestore. exe](mongodb-migrate.md) ' yi kullanarak verileri taşıyın.

* Benzersiz bir anahtar ilkesinde en fazla 16 yol değeri olabilir. Örneğin, değerler `/firstName`, `/lastName`ve `/address/zipCode`olabilir. Her benzersiz anahtar ilkesi en fazla 10 benzersiz anahtar kısıtlaması veya birleşimine sahip olabilir. Her benzersiz dizin kısıtlamasının birleştirilmiş yolları 60 baytı aşmamalıdır. Önceki örnekte adı, soyadı ve e-posta adresi birlikte tek bir kısıtlamadır. Bu kısıtlama, 16 olası yoldan 3 ' ü kullanır.

* Bir kapsayıcının benzersiz bir anahtar ilkesi olduğunda, bir öğeyi oluşturmak, güncelleştirmek ve silmek için [Istek birimi (ru)](request-units.md) ücretleri biraz daha yüksektir.

* Seyrek benzersiz anahtarlar desteklenmiyor. Bazı benzersiz yol değerleri eksikse, bu değerler benzersizlik kısıtlamasındaki bir parçasını oluşturan null değer olarak değerlendirilir. Bu nedenle, bu kısıtlamayı karşılamak için yalnızca null değeri olan tek bir öğe olabilir.

* Benzersiz anahtar adları büyük/küçük harfe duyarlıdır. Örneğin, benzersiz anahtar kısıtlaması `/address/zipcode`olarak ayarlanmış bir kapsayıcı düşünün. Verilerinizde `ZipCode`adlı bir alan varsa, `zipcode` `ZipCode`ile aynı olmadığından, Azure Cosmos DB benzersiz anahtar olarak "null" ekler. Bu durumda, aynı "null" değeri benzersiz anahtar kısıtlamasını ihlal ettiğinden, ZipCode içeren diğer tüm kayıtlar eklenemez.

## <a name="next-steps"></a>Sonraki adımlar

* [Mantıksal bölümler](partition-data.md) hakkında daha fazla bilgi edinin
* Kapsayıcı oluştururken [benzersiz anahtarların nasıl tanımlanacağını](how-to-define-unique-keys.md) keşfet
