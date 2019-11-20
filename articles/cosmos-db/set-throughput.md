---
title: Azure Cosmos kapsayıcılarında ve veritabanlarında üretilen iş sağlama
description: Azure Cosmos kapsayıcıları ve veritabanları için sağlanan aktarım hızını ayarlamayı öğrenin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: 9ac22461e04b447fe34d5647eb5ec7847d25a09d
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931280"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Kapsayıcı ve veritabanlarına aktarım hızı sağlama

Azure Cosmos veritabanı, bir kapsayıcı kümesi için bir yönetim birimidir. Bir veritabanı, bir şema belirsiz kapsayıcılar kümesinden oluşur. Azure Cosmos kapsayıcısı, aktarım hızı ve depolama için ölçeklenebilirlik birimidir. Bir kapsayıcı, bir Azure bölgesindeki bir makine kümesi arasında yatay olarak bölümlenmiştir ve Azure Cosmos hesabınızla ilişkili tüm Azure bölgelerinde dağıtılır.

Azure Cosmos DB, aktarım hızını iki ayrı şekilde sağlayabilirsiniz:
 
- Azure Cosmos kapsayıcıları
- Azure Cosmos veritabanları

## <a name="set-throughput-on-a-container"></a>Bir kapsayıcıda üretilen işi ayarlama  

Azure Cosmos kapsayıcısı üzerinde sağlanan aktarım hızı, o kapsayıcı için özel olarak ayrılmıştır. Kapsayıcı, sağlanan aktarım hızını her zaman alır. Bir kapsayıcıda sağlanan aktarım hızı SLA 'Lar tarafından mali olarak desteklenir. Bir kapsayıcıda üretilen işi yapılandırma hakkında bilgi edinmek için bkz. [Azure Cosmos kapsayıcısında Işleme sağlama](how-to-provision-container-throughput.md).

Bir kapsayıcıda sağlanan aktarım hızını ayarlamak en sık kullanılan seçenektir. [Istek birimleri (ru)](request-units.md)kullanarak herhangi bir miktarda aktarım hızı sağlayarak bir kapsayıcı için ölçek işleme esnek sağlayabilirsiniz. 

Bir kapsayıcı için sağlanan aktarım hızı, fiziksel bölümleri arasında eşit olarak dağıtılır ve mantıksal bölümleri fiziksel bölümler arasında eşit bir şekilde dağıtan iyi bir bölüm anahtarı varsayıldığında, üretilen iş her ikisi arasında eşit olarak dağıtılır kapsayıcının mantıksal bölümleri. Mantıksal bölümler için üretilen işi seçmeli olarak belirtemezsiniz. Bir kapsayıcının bir veya daha fazla mantıksal bölümü fiziksel bölüm tarafından barındırıldığından, fiziksel bölümler yalnızca kapsayıcıya aittir ve kapsayıcıda sağlanan aktarım hızını destekler. 

Bir mantıksal bölümde çalışan iş yükü, bu mantıksal bölüme ayrılan aktarım hızına göre daha fazla tüketir, işlemlerinizin hızı sınırlı olacaktır. Oran sınırlaması gerçekleştiğinde, tüm kapsayıcı için sağlanan aktarım hızını artırabilir ya da işlemleri yeniden deneyebilirsiniz. Bölümlendirme hakkında daha fazla bilgi için bkz. [mantıksal bölümler](partition-data.md).

Kapsayıcı için garantili performans istediğinizde, kapsayıcı ayrıntı düzeyinde aktarım hızı yapılandırmanızı öneririz.

Aşağıdaki görüntüde bir fiziksel bölümün bir kapsayıcının bir veya daha fazla mantıksal bölümünü nasıl barındırdığı gösterilmektedir:

![Fiziksel bölüm](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Bir veritabanında üretilen işi ayarlama

Azure Cosmos veritabanında üretilen iş sağladığınızda, aktarım hızı veritabanında tüm kapsayıcılar (paylaşılan veritabanı kapsayıcıları olarak adlandırılır) arasında paylaşılır. Bir özel durum, veritabanındaki belirli kapsayıcılarda sağlanan bir üretilen işi belirtadınız. Veritabanı düzeyinde sağlanan aktarım hızını kapsayıcı genelinde paylaşmak, bir makine kümesinde bir veritabanını barındırmakla benzerdir. Bir veritabanı içindeki tüm kapsayıcılar bir makinede kullanılabilir kaynakları paylaştığından, doğal olarak herhangi bir kapsayıcı üzerinde öngörülebilir bir performans almaz. Bir veritabanında sağlanan üretilen işi yapılandırma hakkında bilgi edinmek için bkz. [Azure Cosmos veritabanında sağlanan aktarım hızını yapılandırma](how-to-provision-database-throughput.md).

Azure Cosmos veritabanında üretilen iş oluşturma, bu veritabanı için her zaman sağlanan aktarım hızını almanızı garanti eder. Veritabanı içindeki tüm kapsayıcılar sağlanan üretilen işi paylaştığından, Azure Cosmos DB söz konusu veritabanındaki belirli bir kapsayıcı için öngörülebilir bir işlem garantisi sağlamaz. Belirli bir kapsayıcının alabileceği aktarım hızı bölümü şu şekilde değişir:

* Kapsayıcı sayısı.
* Çeşitli kapsayıcılar için bölüm anahtarlarının seçimi.
* Kapsayıcının çeşitli mantıksal bölümlerinin tamamında iş yükünün dağılımı. 

Aktarım hızını birden çok kapsayıcı genelinde paylaşmak istediğinizde, ancak belirli bir kapsayıcıya üretilen işi ayırmak istemediğinizde bir veritabanında üretilen işi yapılandırmanızı öneririz. 

Aşağıdaki örneklerde veritabanı düzeyinde üretilen işi sağlamak için nereye tercih edildiği gösterilmektedir:

* Bir kapsayıcı kümesi genelinde veritabanının sağlanan verimini paylaşmak çok kiracılı bir uygulama için yararlıdır. Her kullanıcı ayrı bir Azure Cosmos kapsayıcısı ile temsil edilebilir.

* Bir sanal makine kümesinde veya şirket içi fiziksel sunuculardan Azure Cosmos DB için barındırılan MongoDB veya Cassandra gibi bir NoSQL veritabanını geçirdiğinizde, bir kapsayıcı kümesi genelinde bir veritabanının sağlanan aktarım hızını paylaşmanız yararlı olur. Azure Cosmos veritabanınızda yapılandırılan üretilen iş üretimini, MongoDB veya Cassandra kümenizin işlem kapasitesinden daha fazla maliyetli, daha düşük maliyetli ve elastik bir eşdeğer olarak düşünün.  

Sağlanan aktarım hızı ile bir veritabanı içinde oluşturulan tüm kapsayıcılar bir [bölüm anahtarıyla](partition-data.md)oluşturulmalıdır. Belirli bir zamanda, bir veritabanı içindeki bir kapsayıcıya ayrılan üretilen iş, o kapsayıcının tüm mantıksal bölümlerine dağıtılır. Bir veritabanında yapılandırılmış sağlanan aktarım hızını paylaşan kapsayıcılara sahipseniz, üretilen işi belirli bir kapsayıcıya veya mantıksal bölüme seçmeli olarak uygulayamazsınız. 

Mantıksal bir bölümdeki iş yükü, belirli bir mantıksal bölüme ayrılan aktarım hızına göre daha fazla tüketir, işlemlerinizin hızı sınırlıdır. Oran sınırlaması gerçekleştiğinde, veritabanının tamamı için üretilen işi artırabilir veya işlemleri yeniden deneyebilirsiniz. Bölümlendirme hakkında daha fazla bilgi için bkz. [mantıksal bölümler](partition-data.md).

Bir veritabanında sağlanan aktarım hızı, bu veritabanı içindeki kapsayıcılar tarafından paylaşılabilir. Veritabanı düzeyindeki paylaşılan iş üretiminin her yeni kapsayıcısı, 100 RU/sn gerektirir. Paylaşılan veritabanı teklifiyle kapsayıcılar sağladığınızda:

* Her 25 kapsayıcı bir bölüm kümesi halinde gruplandırılır ve veritabanı üretimi (D), bölüm kümesindeki kapsayıcılar arasında paylaşılır. Veritabanında en fazla 25 kapsayıcı varsa ve zaman içinde herhangi bir noktada, yalnızca bir kapsayıcı kullanıyorsanız, bu kapsayıcı en fazla ' ın ' verimini kullanabilir.

* 25 kapsayıcısından sonra oluşturulan her yeni kapsayıcı için yeni bir bölüm kümesi oluşturulur ve veritabanı üretimi oluşturulan yeni bölüm kümeleri arasında bölünür (2 Bölüm kümesi için D/2, 3 Bölüm kümesi için D/3)...). Herhangi bir zamanda, veritabanından yalnızca bir kapsayıcı kullanıyorsanız, en fazla (D/2, D/3, D/4) kullanılabilir... aktarım hızı). Düşük aktarım hızı verildiğinde, bir veritabanında en fazla 25 kapsayıcı oluşturmanız önerilir.

**Örnek**

* "MyDB" adlı bir veritabanı oluşturmak için 10.000 RU/sn 'nin sağlanan aktarım hızı.

* "MyDB" altında 25 kapsayıcı sağlarsanız, tüm kapsayıcılar bir bölüm kümesi halinde gruplandırılır. Herhangi bir zamanda, veritabanından yalnızca bir kapsayıcı kullanıyorsanız, en fazla 10.000 RU/sn (D) kullanabilir.

* 26. kapsayıcı sağladığınızda yeni bir bölüm kümesi oluşturulur ve üretilen iş, her iki bölüm kümesi arasında eşit olarak bölünür. Bu nedenle, veritabanından yalnızca bir kapsayıcı kullanıyorsanız, en fazla 5K RU/sn (D/2) kullanılabilir. İki bölüm kümesi olduğundan, üretilen iş parçalı çarpanı faktörü D/2 ' ye bölünür.

   Aşağıdaki görüntüde önceki örnek grafik gösterilmektedir:

   ![Veritabanı düzeyinde aktarım hızı içindeki parçalama faktörü](./media/set-throughput/database-level-throughput-shareability-factor.png)


İş yükleriniz bir veritabanındaki tüm koleksiyonları silmeyi ve yeniden oluşturmayı içeriyorsa, boş veritabanını bırakıp koleksiyon oluşturmadan önce yeni bir veritabanı oluşturmanız önerilir. Aşağıdaki görüntüde bir fiziksel bölümün, bir veritabanı içindeki farklı kapsayıcılara ait bir veya daha fazla mantıksal bölümü nasıl barındıragösterdiği gösterilmektedir:

![Fiziksel bölüm](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Bir veritabanında ve kapsayıcıda üretilen işi ayarlama

İki modeli birleştirebilirsiniz. Hem veritabanında hem de kapsayıcıda sağlama aktarımına izin verilir. Aşağıdaki örnek, bir Azure Cosmos veritabanı ve bir kapsayıcı üzerinde üretilen iş sağlama işlemlerinin nasıl yapılacağını göstermektedir:

* *"K"* ru tarafından sağlanan iş hacmi ile *Z* adlı bir Azure Cosmos veritabanı oluşturabilirsiniz. 
* Sonra, veritabanı içinde *A*, *B*, *C*, *D*ve *E* adlı beş kapsayıcı oluşturun. B kapsayıcısını oluştururken, **Bu kapsayıcı seçeneği için adanmış üretilen iş üretimini sağlamayı** etkinleştirdiğinizden emin olun ve bu kapsayıcıda *"P"* ru sağlanan aktarım hızını açık olarak yapılandırın. Yalnızca veritabanı ve kapsayıcı oluştururken paylaşılan ve ayrılmış iş üretimini yapılandırabileceğinizi unutmayın. 

   ![Aktarım hızını kapsayıcı düzeyinde ayarlama](./media/set-throughput/coll-level-throughput.png)

* *"K"* Rus verimlilik, *A*, *C*, *D*ve *E*dört kapsayıcı genelinde paylaşılır. *Bir*, *C*, *D*veya *E* için kullanılabilir aktarım hızı miktarı farklılık gösterir. Her bir kapsayıcının verimlilik için SLA 'lar yoktur.
* *B* adlı kapsayıcı, *"P"* ru aktarım hızını her zaman almak için garanti edilir. SLA 'Lar tarafından desteklenir.

> [!NOTE]
> Sağlanan aktarım hızı olan bir kapsayıcı, paylaşılan veritabanı kapsayıcısına dönüştürülemez. Bunun tersine, paylaşılan bir veritabanı kapsayıcısının ayrılmış bir işleme sahip olacak şekilde dönüştürülemez.

## <a name="update-throughput-on-a-database-or-a-container"></a>Bir veritabanında veya kapsayıcıda üretilen işi güncelleştirme

Bir Azure Cosmos kapsayıcısı veya veritabanı oluşturduktan sonra, sağlanan aktarım hızını güncelleştirebilirsiniz. Veritabanı veya kapsayıcıda yapılandırabileceğiniz en fazla sağlanan aktarım hızı için bir sınır yoktur. Sağlanan en düşük verimlilik aşağıdaki etkenlere bağlıdır: 

* Kapsayıcıda depoladığınız en büyük veri boyutu
* Kapsayıcıda sağladığınız en fazla aktarım hızı
* Paylaşılan aktarım hızı ile bir veritabanında oluşturduğunuz en fazla Azure Cosmos kapsayıcısı sayısı. 

SDK 'Ları kullanarak bir kapsayıcının veya veritabanının en düşük aktarım hızını alabilir veya Azure portal değeri görüntüleyebilirsiniz. .NET SDK kullanırken [Documentclient. Replaceoffsilinebilir Sync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) yöntemi sağlanan aktarım hızı değerini ölçeklendirmenize olanak tanır. Java SDK 'sını kullanırken, [Requestoptions. Setofferüretilen Iş verimlilik](sql-api-java-samples.md#offer-examples) yöntemi sağlanan aktarım hızı değerini ölçeklendirmenize olanak tanır. 

.NET SDK kullanırken [Documentclient. Readoffsilinebilir Sync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) yöntemi, bir kapsayıcının veya bir veritabanının en düşük verimini elde etmenizi sağlar. 

Bir kapsayıcının veya veritabanının sağlanan verimini istediğiniz zaman ölçeklendirebilirsiniz. Aktarım hızını artırmak için bir ölçeklendirme işlemi gerçekleştirildiğinde, sistem görevlerinin gerekli kaynakları sağlaması nedeniyle daha uzun zaman alabilir. Azure portal veya SDK 'Ları kullanarak program aracılığıyla ölçek işleminin durumunu kontrol edebilirsiniz. .NET SDK kullanırken, `DocumentClient.ReadOfferAsync` yöntemini kullanarak ölçeklendirme işleminin durumunu alabilirsiniz.

## <a name="comparison-of-models"></a>Modellerin karşılaştırması

|**Parametresinin**  |**Bir veritabanında sağlanan aktarım hızı**  |**Kapsayıcıda sağlanan aktarım hızı**|
|---------|---------|---------|
|Minimum ru |400 (ilk dört kapsayıcıdan sonra, her ek kapsayıcı için en az 100 ru/saniye gerekir.) |400|
|Kapsayıcı başına en az ru|100|400|
|Maksimum RUs|Sınırsız, veritabanında.|Sınırsız, kapsayıcıda.|
|RUs atanmış veya belirli bir kapsayıcıya kullanılabilir|Garanti yok. Belirli bir kapsayıcıya atanan RUs özelliklere bağlıdır. Özellikler, aktarım hızını, iş yükünün dağıtımını ve kapsayıcı sayısını paylaşan kapsayıcıların bölüm anahtarları seçeneği olabilir. |Kapsayıcıda yapılandırılan tüm RUs 'lar kapsayıcı için özel olarak ayrılmıştır.|
|Bir kapsayıcı için maksimum depolama alanı|Sayısız.|Sayısız.|
|Bir kapsayıcının mantıksal bölümü başına en fazla aktarım hızı|10.000 ru|10.000 ru|
|Kapsayıcının mantıksal bölümü başına en fazla depolama alanı (veri + dizin)|10 GB|10 GB|

## <a name="next-steps"></a>Sonraki adımlar

* [Mantıksal bölümler](partition-data.md)hakkında daha fazla bilgi edinin.
* [Azure Cosmos kapsayıcısında üretilen iş sağlama](how-to-provision-container-throughput.md)hakkında bilgi edinin.
* [Azure Cosmos veritabanında üretilen iş sağlama](how-to-provision-database-throughput.md)hakkında bilgi edinin.

