---
title: Azure Service Bus konu filtreleri | Microsoft Docs
description: Bu makalede, abonelerin filtreleri belirterek bir konudan hangi iletileri almak istediğini nasıl tanımlayabileceği açıklanmaktadır.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: f28b26ee112b47b9782823f6c79670dee9a3f082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651672"
---
# <a name="topic-filters-and-actions"></a>Konu başlığı filtreleri ve eylemleri

Aboneler, bir konu başlığından hangi iletileri almak istediklerini tanımlayabilir. Bu iletiler, bir veya daha fazla adlandırılmış abonelik kuralı biçiminde belirtilir. Her kural, belirli iletileri seçen bir **filtre koşulunu** ve **isteğe bağlı olarak** seçilen iletiyi açıklama eklenen bir **eylemi** içerir. 

**Eylemleri olmayan** tüm kurallar, bir koşul kullanılarak birleştirilir `OR` ve birden çok eşleşen kuralsanız bile abonelikte **tek bir iletiyle** sonuçlanır. 

**Bir eyleme sahip** her kural iletinin bir kopyasını oluşturur. Bu ileti, `RuleName` değeri eşleşen kuralın adı olduğu yerde adlı bir özelliğe sahip olacaktır. Bu eylem, abonelik üzerinde bir ileti üretmek için özellikleri ekleyebilir veya güncelleştirebilir ya da özgün iletiden özellikleri silebilir. 

Şu senaryoyu göz önünde bulundurun:

- Abonelikte beş kural vardır.
- İki kural eylemleri içerir.
- Üç kural eylem içermez.

Bu örnekte, beş kuralla eşleşen bir ileti gönderirseniz, abonelikte üç ileti alırsınız. Bu iki kurala sahip iki kural ve eylem olmadan üç kural için bir ileti vardır. 

Yeni oluşturulan her konu aboneliğinin bir ilk varsayılan abonelik kuralı vardır. Kural için açıkça bir filtre koşulu belirtmezseniz, uygulanan filtre, tüm iletilerin abonelikte seçili olmasını sağlayan **doğru** filtredir. Varsayılan kuralda ilişkili ek açıklama eylemi yok.

## <a name="filters"></a>Filtreler
Service Bus üç filtre koşulu destekler:

-   *SQL filtreleri* -bir **sqlfilter** , gelen iletilerin Kullanıcı tanımlı özellikler ve sistem özelliklerine karşı ARACıDA değerlendirilen bir SQL benzeri koşullu ifade barındırır. Koşullu ifadede tüm sistem özelliklerinin ön eki olmalıdır `sys.` . [Filtre koşulları Için SQL-Language alt kümesi](service-bus-messaging-sql-filter.md) , özelliklerin ( `EXISTS` ), null-değerlerin ( `IS NULL` ), mantıksal olmayan/ve/veya ilişkisel işleçlerin, basit sayısal aritmetiğinin ve ile eşleşen basit metin deseninin varlığını sınar `LIKE` .
-   *Boole filtreleri* - **truefilter** ve **yanlışfiltre** tüm gelen iletilerin (**true**) ya da abonelik için seçili olmayan iletilerin (**false**) hiçbirinin seçilmemesine neden olur. Bu iki filtre SQL filtresinden türetilir. 
-   *Bağıntı filtreleri* -bir **correlationfilter** , gelen bir veya daha fazla iletinin Kullanıcı ve sistem özelliklerine göre eşleşen bir koşullar kümesi tutar. Yaygın olarak kullanılan bir kullanım, **CorrelationId** özelliğiyle eşleşmedir, ancak uygulama aşağıdaki özelliklerle eşleşmeyi de seçebilir:

    - **ContentType**
     - **Etiketle**
     - **Ileti**
     - **ReplyTo**
     - **Replytosessionıd**
     - **Kimliği** 
     - **Kime**
     - Kullanıcı tanımlı tüm özellikler. 
     
     Bir özellik için gelen iletinin değeri, bağıntı filtresinde belirtilen değere eşitse bir eşleşme oluşur. Dize ifadeleri için karşılaştırma büyük/küçük harfe duyarlıdır. Birden çok eşleşme özelliği belirtirken, filtre bunları mantıksal ve koşul olarak birleştirir, yani filtrenin eşleşmesi için tüm koşulların eşleşmesi gerekir.

Tüm Filtreler ileti özelliklerini değerlendirir. Filtreler ileti gövdesini değerlendiremez.

Karmaşık filtre kuralları işlem kapasitesi gerektirir. Özellikle, SQL filtre kurallarının kullanımı, abonelik, konu ve ad alanı düzeyinde genel ileti aktarım hızını daha düşük bir şekilde oluşmasına neden olur. Mümkün olduğunda uygulamalar, işleme göre çok daha verimli olduklarından ve üretilen iş üzerinde daha az etkilendiklerinden SQL benzeri filtreler üzerinden bağıntı filtreleri seçmelidir.

## <a name="actions"></a>Eylemler

SQL filtre koşulları ile, özellikleri ve değerlerini ekleyerek, kaldırarak veya değiştirerek iletiye açıklama eklenebilir bir eylem tanımlayabilirsiniz. Bu eylem, SQL UPDATE deyimi söz dizimini kullanan [SQL benzeri bir ifade kullanır](service-bus-messaging-sql-filter.md) . İşlem eşleştirdikten sonra ve ileti abonelikte seçildikten sonra ileti üzerinde yapılır. İleti özelliklerindeki değişiklikler, aboneliğe kopyalanmış ileti için özeldir.

## <a name="usage-patterns"></a>Kullanım desenleri

Bir konu için en basit kullanım senaryosu, her aboneliğin bir konuya gönderilen her iletinin bir kopyasını aldığından ve bu da bir yayın deseninin yapılmasını sağlar.

Filtreler ve eylemler iki ek desen grubunu etkinleştirir: bölümlendirme ve yönlendirme.

Bölümlendirme, çeşitli mevcut konu aboneliklerine iletileri öngörülebilir ve birbirini dışlayan şekilde dağıtmak için filtreler kullanır. Bölümlendirme deseninin bir sistem, her biri, genel verilerin bir alt kümesini tutan bölmeleri özdeş olan çok sayıda farklı bağlamı işlemek üzere ölçeklenirken kullanılır. Örneğin, müşteri profili bilgileri. Bölümlendirme ile, Yayımcı, bölümleme modeli bilgisine gerek kalmadan iletiyi bir konuya gönderir. Daha sonra ileti, bölümün ileti işleyicisi tarafından alınabilecek doğru aboneliğe taşınır.

Yönlendirme, ileti aboneliklerine öngörülebilir bir şekilde ileti dağıtmak için filtreleri kullanır, ancak özel olması gerekmez. [Otomatik iletme](service-bus-auto-forwarding.md) özelliği ile birlikte, konu filtreleri bir Azure bölgesindeki ileti dağıtımı için bir Service Bus ad alanı içinde karmaşık yönlendirme grafikleri oluşturmak için kullanılabilir. Azure Service Bus ad alanları arasında bir köprü görevi gören Azure Işlevleri veya Azure Logic Apps, iş kolu uygulamalarına doğrudan tümleştirmeyle karmaşık küresel topolojiler oluşturabilirsiniz.

## <a name="examples"></a>Örnekler
Örnekler için bkz. [Service Bus filtresi örnekleri](service-bus-filter-examples.md).



> [!NOTE]
> Azure portal artık Service Bus Explorer işlevselliğini desteklediğinden, abonelik filtreleri portalda oluşturulabilir veya düzenlenebilir. 

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki örneklere bakın: 

- [Filtrelerle .NET-temel gönderme ve alma öğreticisi](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET-konu filtreleri](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager şablonu](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)