---
title: Verilerinizi ve iş yükünüzü eşit bir şekilde dağıtmak için Azure Cosmos DB bir yapay bölüm anahtarı oluşturun.
description: Azure Cosmos kapsayıcılarınızdaki yapay bölüm anahtarlarını nasıl kullanacağınızı öğrenin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 8b4e2b8abac39f3268e0da7838acd566f40fdccc
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754811"
---
# <a name="create-a-synthetic-partition-key"></a>Yapay bölüm anahtarı oluşturma

Yüzlerce veya binlerce gibi birçok farklı değere sahip bir bölüm anahtarına sahip olmak en iyi uygulamadır. Amaç, bu bölüm anahtarı değerleriyle ilişkili öğeler arasında verilerinizi ve iş yükünüzü eşit olarak dağıtmaktır. Bu tür bir özellik verilerinizde yoksa *yapay bir bölüm anahtarı*oluşturabilirsiniz. Bu belgede, Cosmos Kapsayıcınız için yapay bir bölüm anahtarı oluşturmaya yönelik çeşitli temel teknikler açıklanmaktadır.

## <a name="concatenate-multiple-properties-of-an-item"></a>Bir öğenin birden çok özelliğini birleştirme

Birden çok özellik değerini tek bir yapay `partitionKey` özelliğinde birleştirerek bölüm anahtarı oluşturabilirsiniz. Bu anahtarlara yapay anahtarlar denir. Örneğin, aşağıdaki örnek belgeyi göz önünde bulundurun:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Önceki belge için bir seçenek, bölüm anahtarı olarak/DeviceID veya/Date olarak ayarlanmıştır. Kapsayıcınızı cihaz KIMLIĞI veya tarih temelinde bölümlemek istiyorsanız bu seçeneği kullanın. Diğer bir seçenek de, bu iki değeri bölüm anahtarı olarak kullanılan yapay bir `partitionKey` özelliğine birleştirme.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Gerçek zamanlı senaryolarda, veritabanında binlerce öğe olabilir. Yapay anahtarı el ile eklemek yerine, değerleri birleştirmek ve yapay nesneleri Cosmos kapsayıcılarınızdaki öğelere eklemek için istemci tarafı mantığını tanımlayın.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Rastgele bir sonek ile bölüm anahtarı kullanma

İş yükünü daha eşit bir şekilde dağıtmak için bir başka olası strateji, bölüm anahtarı değerinin sonuna rastgele bir sayı eklemedir. Öğeleri bu şekilde dağıttığınızda, bölümler arasında paralel yazma işlemleri gerçekleştirebilirsiniz.

Bir bölüm anahtarı bir tarihi temsil ediyorsa, örnek bir örnektir. 1 ile 400 arasında rastgele bir sayı seçebilir ve bu dosyayı Tarih soneki olarak birleştirebilirsiniz. Bu yöntem, `2018-08-09.400`aracılığıyla `2018-08-09.1`,`2018-08-09.2`vb. gibi bölüm anahtarı değerlerini de elde eder. Bölüm anahtarını rastgele kullanıma aldığınız için, her gün kapsayıcı üzerindeki yazma işlemleri birden çok bölüme eşit olarak yayılır. Bu yöntem, daha iyi paralellik ve genel daha yüksek verimlilik elde ediyor.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Önceden hesaplanmış soneklerle bölüm anahtarı kullanma 

Rastgele sonek stratejisi, yazma verimini büyük ölçüde iyileştirebilirler, ancak belirli bir öğeyi okumak zordur. Öğeyi yazdığınızda kullanılan sonek değerini bilemezsiniz. Tek tek öğeleri okumayı kolaylaştırmak için önceden hesaplanmış son ekler stratejisini kullanın. Öğeleri bölümler arasında dağıtmak için rastgele bir sayı kullanmak yerine, sorgulamak istediğiniz bir şeye göre hesaplanmış bir sayı kullanın.

Bir kapsayıcının bölüm anahtarı olarak bir tarih kullandığı önceki örneği göz önünde bulundurun. Artık her bir öğenin erişmek istediğimiz bir `Vehicle-Identification-Number` (`VIN`) özniteliği olduğunu varsayalım. Ayrıca, tarihin yanı sıra, `VIN`öğeleri bulmak için genellikle sorguları çalıştırdığınızı varsayalım. Uygulamanız öğeyi kapsayıcıya yazmadan önce, can 'ı temel alan bir karma sonek hesaplayabilir ve bölüm anahtarı tarihine ekleyebilirsiniz. Hesaplama, eşit olarak dağıtılan 1 ile 400 arasında bir sayı oluşturabilir. Bu sonuç, rastgele sonek strateji yöntemi tarafından üretilen sonuçlara benzerdir. Bölüm anahtarı değeri, hesaplanan sonuçla birleştirilmiş bir tarihtir.

Bu stratejiyle, yazma işlemleri bölüm anahtarı değerleri ve bölümler arasında eşit olarak yayılır. Belirli bir `Vehicle-Identification-Number`için bölüm anahtarı değerini hesaplayabilmeniz için belirli bir öğeyi ve tarihi kolayca okuyabilirsiniz. Bu yöntemin avantajı, tek bir sıcak bölüm anahtarı (yani, tüm iş yükünü alan bir bölüm anahtarı) oluşturmaktan kaçınmanızı sağlar. 

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde bölümlendirme kavramı hakkında daha fazla bilgi edinebilirsiniz:

* [Mantıksal bölümler](partition-data.md)hakkında daha fazla bilgi edinin.
* [Azure Cosmos kapsayıcılarında ve veritabanlarında işleme sağlama](set-throughput.md)hakkında daha fazla bilgi edinin.
* [Azure Cosmos kapsayıcısında üretilen iş sağlama](how-to-provision-container-throughput.md)hakkında bilgi edinin.
* [Azure Cosmos veritabanında üretilen iş sağlama](how-to-provision-database-throughput.md)hakkında bilgi edinin.
