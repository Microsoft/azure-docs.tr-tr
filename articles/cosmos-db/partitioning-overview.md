---
title: Azure Cosmos DB'de bölümleme ve yatay ölçeklendirme
description: Azure Cosmos DB bölümlendirme, mantıksal, fiziksel bölümler, bölüm anahtarı seçerken en iyi yöntemler ve mantıksal bölümlerin nasıl yönetileceği hakkında bilgi edinin
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 099c65143f29f4fdf341b52e5d80731f1bdb0808
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031010"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB'de bölümleme ve yatay ölçeklendirme
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB, uygulamanızın performans ihtiyaçlarını karşılamak üzere bir veritabanındaki ayrı kapsayıcıları ölçeklendirmek için bölümleme kullanır. Bölümleme bölümünde, bir kapsayıcıdaki öğeler *mantıksal bölümler* adlı farklı alt kümelere bölünür. Mantıksal bölümler, bir kapsayıcıdaki her öğeyle ilişkili bir *bölüm anahtarının* değerine göre oluşturulur. Bir mantıksal bölümdeki tüm öğeler aynı bölüm anahtarı değerine sahip.

Örneğin, bir kapsayıcı öğeleri barındırır. Her öğenin özellik için benzersiz bir değeri vardır `UserID` . `UserID`Kapsayıcıda öğeler için bölüm anahtarı olarak görev yapar ve 1.000 benzersiz `UserID` değer varsa, kapsayıcı için 1.000 mantıksal bölümler oluşturulur.

Öğenin mantıksal bölümünü belirleyen bir bölüm anahtarına ek olarak, bir kapsayıcıdaki her öğe bir *öğe kimliğine* (mantıksal bölüm içinde benzersiz) sahiptir. Bölüm anahtarını ve *öğe kimliğini* birleştirmek, öğenin benzersiz şekilde tanımlandığı öğenin *dizinini* oluşturur. [Bölüm anahtarının seçilmesi](#choose-partitionkey) , uygulamanızın performansını etkileyecek önemli bir karardır.

Bu makalede, mantıksal ve fiziksel bölümler arasındaki ilişki açıklanmaktadır. Ayrıca bölümlendirme için en iyi yöntemleri açıklar ve yatay ölçeklendirmenin Azure Cosmos DB nasıl çalıştığına ilişkin derinlemesine bir görünüm sağlar. Bölüm anahtarınızı seçmek için bu iç ayrıntıları anlamanız gerekli değildir, ancak Azure Cosmos DB nasıl çalıştığına ilişkin netlik sahibi olmak üzere kapsandık.

## <a name="logical-partitions"></a>Mantıksal bölümler

Mantıksal bir bölüm, aynı bölüm anahtarına sahip bir öğe kümesinden oluşur. Örneğin, yiyecek ile ilgili verileri içeren bir kapsayıcıda, tüm öğeler bir `foodGroup` özellik içerir. `foodGroup`Kapsayıcı için bölüm anahtarı olarak ' i kullanabilirsiniz. , Ve gibi belirli değerlere sahip öğe grupları, `foodGroup` ve gibi `Beef Products` `Baked Products` `Sausages and Luncheon Meats` mantıksal bölümler oluşturur.

Mantıksal bir bölüm ayrıca veritabanı işlemlerinin kapsamını tanımlar. Bir mantıksal bölüm içindeki öğeleri, [anlık görüntü yalıtımıyla bir işlem](database-transactions-optimistic-concurrency.md)kullanarak güncelleştirebilirsiniz. Bir kapsayıcıya yeni öğeler eklendiğinde, yeni mantıksal bölümler sistem tarafından saydam olarak oluşturulur. Temel alınan veriler silindiğinde mantıksal bir bölümü silme konusunda endişelenmeniz gerekmez.

Kapsayıcıınızda mantıksal bölüm sayısı için bir sınır yoktur. Her mantıksal bölüm, 20 GB 'a kadar veri saklayabilir. İyi bölüm anahtarı seçimleri çok sayıda olası değer aralığına sahiptir. Örneğin, tüm öğelerin bir özelliği içerdiği bir kapsayıcıda `foodGroup` , `Beef Products` mantıksal bölümün içindeki VERILER 20 GB 'a kadar büyüyebilir. Çok sayıda olası değeri olan [bir bölüm anahtarını seçmek](#choose-partitionkey) kapsayıcının ölçeklenmesini sağlar.

## <a name="physical-partitions"></a>Fiziksel bölümler

Bir kapsayıcı, verileri ve aktarım hızını fiziksel bölümler arasında dağıtarak ölçeklendirilir. Dahili olarak, bir veya daha fazla mantıksal bölüm tek bir fiziksel bölüme eşlenir. Genellikle daha küçük kapsayıcılar birçok mantıksal bölüme sahiptir ancak yalnızca tek bir fiziksel bölüm gerektirir. Mantıksal bölümlerin aksine, fiziksel bölümler sistemin dahili bir uygulamasıdır ve tamamen Azure Cosmos DB tarafından yönetilir.

Kapsayıcılarınızın fiziksel bölümlerinin sayısı aşağıdakilere bağlıdır:

* Sağlanan aktarım hızı sayısı (her bir fiziksel bölüm, saniyede en fazla 10.000 istek birimi sağlar). Fiziksel bölümler için 10.000 RU/sn sınırı, her mantıksal bölüm yalnızca bir fiziksel bölüme eşlendiğinde mantıksal bölümlerin 10.000 RU/s sınırına sahip olduğunu gösterir.

* Toplam veri depolama alanı (her bir fiziksel Bölüm 50 GB 'a kadar veri saklayabilir).

> [!NOTE]
> Fiziksel bölümler sistemin dahili bir uygulamasıdır ve tamamen Azure Cosmos DB tarafından yönetilir. Çözümlerinizi geliştirirken, bunları denetleyemediğiniz için fiziksel bölümlere odaklanmayın. Bunun yerine, Bölüm Anahtarlarınıza odaklanın. Mantıksal bölümlerde üretilen iş tüketimini eşit bir şekilde dağıtan bir bölüm anahtarı seçerseniz, fiziksel bölümlerde üretilen iş tüketiminin dengelenmesi güvence altına alınır.

Kapsayıcıınızda bulunan toplam fiziksel bölüm sayısı için bir sınır yoktur. Sağlanan aktarım hızı veya veri boyutunuz büyüdükçe, Azure Cosmos DB var olanları bölerek otomatik olarak yeni fiziksel bölümler oluşturacak. Fiziksel bölüm bölmelerini uygulamanızın kullanılabilirliğini etkilemez. Fiziksel bölüm ayrıldıktan sonra, tek bir mantıksal bölüm içindeki tüm veriler aynı fiziksel bölümde saklanmaya devam eder. Fiziksel bölüm ayırma, mantıksal bölümlerin fiziksel bölümlere yeni bir eşlemesini oluşturur.

Bir kapsayıcı için sağlanan aktarım hızı fiziksel bölümler arasında eşit olarak bölünür. İstekleri eşit olarak dağıtmayan bir bölüm anahtarı tasarımı, "sık erişimli" olan bölümlerin küçük bir alt kümesine yöneltilen çok fazla istek oluşmasına neden olabilir. Sık kullanılan bölümler, sağlanan aktarım hızının verimsiz bir şekilde kullanılmasına neden olur ve bu da hız sınırlaması ve daha yüksek maliyetlere yol açabilir.

Kapsayıcının fiziksel bölümlerinin Azure portal **ölçüm dikey** penceresinin **depolama** bölümünde görebilirsiniz:

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="Fiziksel bölüm sayısını görüntüleme" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

Yukarıdaki ekran görüntüsünde bir kapsayıcı `/foodGroup` bölüm anahtarı olarak bulunur. Grafikteki üç çubuk bir fiziksel bölümü temsil eder. Görüntüde, **bölüm anahtar aralığı** fiziksel bir bölümle aynıdır. Seçilen fiziksel bölüm en çok önemli 3 boyutlu mantıksal bölümleri içerir: `Beef Products` , `Vegetable and Vegetable Products` ve `Soups, Sauces, and Gravies` .

Saniyede 18.000 istek birimi (RU/sn) üretilen işi sağlarsanız, üç fiziksel bölümün her biri, sağlanan toplam üretilen iş üretiminin 1/3 ' i kullanabilir. Seçilen fiziksel bölümde, mantıksal bölüm anahtarları `Beef Products` `Vegetable and Vegetable Products` ve `Soups, Sauces, and Gravies` toplu olarak, fiziksel bölümün 6.000 tarafından sağlanan ru/s 'yi kullanabilir. Sağlanan aktarım hızı kapsayıcının fiziksel bölümlerine eşit olarak bölündüğü için [doğru mantıksal bölüm anahtarını seçerek](#choose-partitionkey)işleme tüketimini eşit bir şekilde dağıtan bir bölüm anahtarı seçmeniz önemlidir. 

## <a name="managing-logical-partitions"></a>Mantıksal bölümleri yönetme

, Kapsayıcının ölçeklenebilirlik ve performans ihtiyaçlarını etkili bir şekilde karşılamak üzere fiziksel bölümlerin mantıksal bölümlerinin yerleşimini saydam ve otomatik olarak yönetir. Azure Cosmos DB Bir uygulamanın aktarım hızı ve depolama gereksinimleri artdıkça, yükü daha fazla sayıda fiziksel bölümde otomatik olarak yaymak için mantıksal bölümlerin taşınması Azure Cosmos DB. [Fiziksel bölümler](partitioning-overview.md#physical-partitions)hakkında daha fazla bilgi edinebilirsiniz.

Azure Cosmos DB, mantıksal bölümleri fiziksel bölümler arasında yaymak için karma tabanlı bölümleme kullanır. Bir öğenin bölüm anahtarı değerini karma Azure Cosmos DB. Karma hale getirilmiş sonuç fiziksel bölümü belirler. Ardından Azure Cosmos DB, bölüm anahtarı karmalarının anahtar alanını fiziksel bölümler arasında eşit olarak ayırır.

İşlemlere (saklı yordamlarda veya tetikleyicilere) yalnızca tek bir mantıksal bölümdeki öğelere karşı izin verilir.

## <a name="replica-sets"></a>Çoğaltma kümeleri

Her fiziksel bölüm, [*çoğaltma kümesi*](global-dist-under-the-hood.md)olarak da adlandırılan bir çoğaltmalar kümesinden oluşur. Her çoğaltma kümesi, veritabanı altyapısının bir örneğini barındırır. Bir çoğaltma kümesi, fiziksel bölümde depolanan verilerin dayanıklı, yüksek oranda kullanılabilir ve tutarlı olmasını sağlar. Fiziksel bölümü oluşturan her çoğaltma, bölümün depolama kotasını devralır. Fiziksel bölümün tüm çoğaltmaları, fiziksel bölüme ayrılan üretilen işi topluca destekler. Azure Cosmos DB çoğaltma kümelerini otomatik olarak yönetir.

Genellikle, daha küçük kapsayıcılar yalnızca tek bir fiziksel bölüm gerektirir, ancak hala en az 4 yineleme olur.

Aşağıdaki görüntüde, mantıksal bölümlerin küresel olarak dağıtılan fiziksel bölümlerle nasıl eşlendiği gösterilmektedir:

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="Azure Cosmos DB bölümleme gösteren bir görüntü" border="false":::

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Bölüm anahtarını seçme

Bölüm anahtarında iki bileşen vardır: **bölüm anahtar yolu** ve **bölüm anahtarı değeri**. Örneğin, {"UserID" öğesini düşünün: "Andrew", "worksFor": "Microsoft"} bölüm anahtarı olarak "UserID" seçeneğini belirlerseniz, iki bölüm anahtarı bileşeni aşağıda verilmiştir:

* Bölüm anahtarı yolu (örneğin: "/UserID"). Bölüm anahtarı yolu alfasayısal ve alt çizgi (_) karakterlerini kabul eder. Ayrıca, standart yol gösterimini (/) kullanarak iç içe geçmiş nesneleri de kullanabilirsiniz.

* Bölüm anahtarı değeri (örneğin: "Andrew"). Bölüm anahtarı değeri dize veya sayısal türlerde olabilir.

Bölüm anahtarının aktarım hızı, depolama ve uzunluğu hakkında daha fazla bilgi edinmek için [Azure Cosmos DB hizmet kotaları](concepts-limits.md) makalesine bakın.

Bölüm anahtarınızı seçmek Azure Cosmos DB basit ancak önemli bir tasarım seçimdir. Bölüm anahtarınızı seçtikten sonra yerinde değiştirmek mümkün değildir. Bölüm anahtarınızı değiştirmeniz gerekiyorsa, verilerinizi yeni istenen bölüm anahtarınızla yeni bir kapsayıcıya taşımanız gerekir.

**Tüm** kapsayıcılar için, Bölüm anahtarınız şunları yapmalısınız:

* Değişmez değeri olan bir özellik olamaz. Bir özellik bölüm anahtarınıza sahip ise, bu özelliğin değerini güncelleştiremezsiniz.

* Yüksek bir kardinaliteye sahip olma. Diğer bir deyişle, özelliği çok sayıda olası değere sahip olmalıdır.

* İstek birimi (RU) tüketimini ve veri depolamayı tüm mantıksal bölümlerde eşit oranda dağıt. Bu da fiziksel bölümlerinizde RU tüketim ve depolama dağıtımı sağlar.

Azure Cosmos DB [Çoklu öğe ACID işlemlerine](database-transactions-optimistic-concurrency.md#multi-item-transactions) ihtiyacınız varsa, [saklı yordamları veya Tetikleyicileri](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)kullanmanız gerekir. Tüm JavaScript tabanlı saklı yordamlar ve Tetikleyiciler, tek bir mantıksal bölümün kapsamına alınır.

## <a name="partition-keys-for-read-heavy-containers"></a>Okuma ağır kapsayıcılar için bölüm anahtarları

Çoğu kapsayıcı için yukarıdaki ölçüt, bölüm anahtarı seçerken göz önünde bulundurmanız gereken tek şey olur. Öte yandan, büyük okuma ağır kapsayıcılar için, sorgularda filtre olarak sık görüntülenen bir bölüm anahtarı seçmek isteyebilirsiniz. Sorgular, filtre koşuluna bölüm anahtarını ekleyerek [, yalnızca ilgili fiziksel bölümlere etkin](how-to-query-container.md#in-partition-query) bir şekilde yönlendirilebilir.

İş yükünüzün isteklerinin çoğu sorgular ise ve sorgularınızdaki çoğu aynı özellikte bir eşitlik filtresine sahipseniz, bu özellik iyi bir bölüm anahtarı seçeneği olabilir. Örneğin, sık sık filtre uygulayan bir sorgu çalıştırırsanız `UserID` , `UserID` bölüm anahtarı olarak seçilmesi, [bölümler arası sorguların](how-to-query-container.md#avoiding-cross-partition-queries)sayısını azaltır.

Ancak, Kapsayıcınız küçükse, bölümler arası sorguların performans etkisi konusunda endişelenmeniz gereken yeterli fiziksel bölümünüz yok demektir. Azure Cosmos DB çoğu küçük kapsayıcı yalnızca bir veya iki fiziksel bölüm gerektirir.

Kapsayıcınız birkaç fiziksel bölümden daha fazla büyümeye devam ediyorsanız, çapraz bölüm sorgularını en aza indiren bir bölüm anahtarı seçtiğinizden emin olun. Aşağıdakilerden biri geçerliyse, Kapsayıcınız birkaç fiziksel bölümden fazlasını gerektirecektir:

* Kapsayıcıda 30.000 RU üzerinde sağlanan

* Kapsayıcınız 100 GB veri üzerinden depolanacak

## <a name="using-item-id-as-the-partition-key"></a>Bölüm anahtarı olarak öğe KIMLIĞI kullanma

Kapsayıcının çok sayıda olası değeri olan bir özelliği varsa, büyük olasılıkla büyük bir bölüm anahtarı seçimidir. Bu tür bir özelliğin olası bir örneği *öğe kimliğidir*. Küçük okuma ağır kapsayıcılar veya herhangi bir büyüklükte yazma ağır kapsayıcılar için, *öğe kimliği* doğal olarak bölüm anahtarı için harika bir seçimdir.

Kapsayıcıdaki her öğede sistem özelliği *öğe kimliği* var. Öğe için bir mantıksal KIMLIĞI temsil eden diğer özelliklere sahip olabilirsiniz. Çoğu durumda bunlar Ayrıca, *öğe kimliği* ile aynı nedenlerden dolayı büyük bölüm anahtarı seçimleridir.

*Öğe kimliği* , aşağıdaki nedenlerden dolayı harika bir bölüm anahtarı seçimleridir:

* Çok sayıda olası değer aralığı vardır (öğe başına bir benzersiz *öğe kimliği* ).
* Öğe başına benzersiz bir *öğe kimliği* olduğundan, *öğe kimliği* , ru tüketimine ve veri depolama alanı üzerinde eşit dengelemeye sahip harika bir iş yapar.
* *Öğe kimliğini* biliyorsanız, öğenin bölüm anahtarını her zaman öğrentireceğiz, kolayca etkili nokta okuma yapabilirsiniz.

Bölüm anahtarı olarak *öğe kimliği* seçerken göz önünde bulundurmanız gereken bazı noktalar şunlardır:

* *Öğe kimliği* bölüm anahtarınkse, kapsayıcının tamamında benzersiz bir tanımlayıcı olur. Yinelenen *öğe kimliği* olan öğeleriniz olamaz.
* Çok sayıda [fiziksel bölüm](partitioning-overview.md#physical-partitions)içeren bir okuma ağır kapsayıcınız varsa, *öğe kimliği* ile bir eşitlik filtresi varsa sorgular daha verimli olacaktır.
* Saklı yordamları veya Tetikleyicileri birden çok mantıksal bölümde çalıştıramazsınız.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB 'de sağlanan aktarım hızı](request-units.md)hakkında bilgi edinin.
* [Azure Cosmos DB 'de küresel dağıtım](distribute-data-globally.md)hakkında bilgi edinin.
* [Azure Cosmos kapsayıcısında üretilen iş sağlama](how-to-provision-container-throughput.md)hakkında bilgi edinin.
* [Azure Cosmos veritabanında üretilen iş sağlama](how-to-provision-database-throughput.md)hakkında bilgi edinin.
