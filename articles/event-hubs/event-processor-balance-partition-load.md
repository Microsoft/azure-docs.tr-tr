---
title: Birden çok örnek arasında bölüm yükünü dengeleme-Azure Event Hubs | Microsoft Docs
description: Olay işlemcisi ve Azure Event Hubs SDK kullanarak, uygulamanızın birden çok örneğinde bölüm yükünün nasıl dengeleneceğini açıklar.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: af307058d0eda6b96c0811bccc245c09e2bdd27d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025053"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Uygulamanızın birden çok örneği arasında bölüm yükünü dengeleme
Olay işleme uygulamanızı ölçeklendirmek için, uygulamanın birden çok örneğini çalıştırabilir ve yükün kendileri arasında dengelenmesi sağlayabilirsiniz. Eski sürümlerde, [Eventprocessorhost](event-hubs-event-processor-host.md) , alma sırasında programınızın birden çok örneği ve kontrol noktası olayları arasındaki yükü dengelemenize izin verildi. Yeni sürümlerde (5,0 sonraki sürümler), **Eventprocessorclient** (.net ve Java) veya **Eventhubconsumerclient** (Python ve JavaScript) aynı şekilde yapmanıza olanak sağlar. Geliştirme modeli olayları kullanarak daha basit hale getirilir. Bir olay işleyicisini kaydederek ilgilendiğiniz olaylara abone olursunuz. İstemci kitaplığının eski sürümünü kullanıyorsanız, aşağıdaki geçiş kılavuzlarını inceleyin: [.net](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/migration-guide.md), [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/servicebus/azure-servicebus/migration_guide.md)ve [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/migrationguide.md).

Bu makalede, bir olay hub 'ından olayları okumak için birden çok örnek kullanmanın örnek bir senaryosu açıklanmakta ve daha sonra aynı Olay Hub 'ını ve tüketici grubunu kullanan diğer tüketicilerle yük dengelemenize olanak tanıyan olay işlemcisi istemcisinin özellikleri hakkında ayrıntılı bilgi verir.

> [!NOTE]
> Event Hubs Ölçeklendirilecek anahtar bölümlenmiş tüketicilerinin fikrine yöneliktir. [Rekabet tüketicilerinin](/previous-versions/msp-n-p/dn568101(v=pandp.10)) deseninin aksine, bölümlenmiş tüketici deseninin çekişme sorunlarını ortadan kaldırarak ve uçtan uca paralellik için kolaylaştırarak yüksek ölçeklenmesini sağlar.

## <a name="example-scenario"></a>Örnek senaryo

Örnek senaryo olarak 100.000 evlerini izleyen bir ev güvenlik şirketi düşünün. Her dakika, bir hareket algılayıcısı, kapı/pencere açık algılayıcı, cam kesmesi algılayıcısı vb. gibi çeşitli sensörlerden, her girişe yüklenmiş olan verileri alır. Şirket, bir evin neredeyse gerçek zamanlı olarak kendi giriş etkinliklerini izlemesine yönelik bir Web sitesi sağlar.

Her algılayıcı, verileri bir olay hub 'ına iter. Olay Hub 'ı 16 bölüm ile yapılandırılır. Tüketim uçta, bu olayları okuyabilen, bunları birleştirebilen (filtre, toplama ve benzeri) bir mekanizmaya ihtiyacınız vardır ve toplamanın, daha sonra Kullanıcı dostu bir Web sayfasına yansıtılmasına neden olan bir Depolama Blobu ile dökümünü alabilirsiniz.

## <a name="write-the-consumer-application"></a>Tüketici uygulamasını yazma

Tüketici dağıtılmış bir ortamda tasarlarken, senaryonun aşağıdaki gereksinimleri işlemesi gerekir:

1. **Ölçek:** Her tüketiciye birkaç Event Hubs bölümden okuma sahipliği alan birden çok tüketici oluşturun.
2. **Yük Dengeleme:** Tüketicileri dinamik olarak artırın veya azaltın. Örneğin, her girişe yeni bir algılayıcı türü (örneğin, bir karbon MONOXIDE algılayıcısı) eklendiğinde, olayların sayısı artar. Bu durumda, işleç (insan), tüketici örneklerinin sayısını artırır. Daha sonra, tüketici havuzu, yükü yeni eklenen tüketicilerle paylaşmak için sahip oldukları bölüm sayısını yeniden dengeedebilir.
3. **Hatalarda sorunsuz bir şekilde sürdürülür:** Bir tüketici (**tüketici a**) başarısız olursa (örneğin, tüketiciyi barındıran sanal makine aniden kilitlenirse), diğer tüketiciler **tüketici a** 'nın sahip olduğu bölümleri alabilir ve devam edebilir. Ayrıca, *denetim noktası* ya da *konum* olarak adlandırılan devamlılık noktası, **tüketicisinin** başarısız olduğu veya bundan biraz önce olması gereken kesin bir noktada olmalıdır.
4. **Olayları tüketme:** Önceki üç işaret, tüketicinin yönetimiyle ilgilenirken, olayları tüketmek ve bu konuyla ilgili bir şey yapmak için kod olmalıdır. Örneğin, bunu toplayın ve BLOB depolamaya yükleyin.

## <a name="event-processor-or-consumer-client"></a>Olay işlemcisi veya tüketici istemcisi

Bu gereksinimleri karşılamak için kendi çözümünüzü derlemeniz gerekmez. Azure Event Hubs SDK 'Ları bu işlevselliği sağlar. .NET veya Java SDK 'larında, bir olay işlemcisi istemcisi (EventProcessorClient) kullanır ve Python ve JavaScript SDK 'lerinde EventHubConsumerClient kullanırsınız. SDK 'nın eski sürümünde bu özellikleri destekleyen olay işleyicisi ana bilgisayarı (EventProcessorHost).

Üretim senaryolarının çoğunluğunda, olayları okumak ve işlemek için olay işlemcisi istemcisini kullanmanızı öneririz. İşlemci istemcisinin, ilerleme durumunu kontrol etmek için bir yol sağlarken bir olay hub 'ının tüm bölümlerindeki olayları performans ve hataya dayanıklı bir şekilde işlemeye yönelik sağlam bir deneyim sağlamaya yöneliktir. Olay işlemcisi istemcileri, belirli bir olay hub 'ı için bir tüketici grubu bağlamında birlikte çalışmaya çalışabilir. İstemciler, grup için kullanılabilir veya kullanılamaz hale geldiğinde iş dağıtımını ve iş dengelemeyi otomatik olarak yönetir.

## <a name="partition-ownership-tracking"></a>Bölüm sahipliği izleme

Bir olay işlemcisi örneği, genellikle bir veya daha fazla bölümden olayları sahipleyip işler. Bölümlerin sahipliği, bir olay hub 'ı ve Tüketici grubu birleşimiyle ilişkili tüm etkin olay işlemcisi örnekleri arasında eşit olarak dağıtılır. 

Her olay işlemcisine, bir denetim noktası deposundaki bir girişi ekleyerek veya güncelleştirerek bölümlerin, benzersiz bir tanımlayıcı ve talep sahipliği olarak verilmiş olması sağlanır. Tüm olay işlemcisi örnekleri, kendi işleme durumunu güncelleştirmek ve diğer etkin örnekler hakkında bilgi edinmek için bu mağazanızla düzenli olarak iletişim kurar. Bu veriler daha sonra etkin işlemciler arasında yükü dengelemek için kullanılır. Yeni örnekler, ölçeği genişletmek için işleme havuzuna katılabilir. Örnekler, hatalardan veya ölçeği azaltıp azaltılarsa, Bölüm sahipliği diğer etkin işlemcilere düzgün şekilde aktarılır.

Denetim noktası deposundaki bölüm sahipliği kayıtları, Event Hubs ad alanı, Olay Hub 'ı adı, Tüketici grubu, olay işlemci tanımlayıcısı (sahip olarak da bilinir), bölüm KIMLIĞI ve son değiştirilme zamanı durumunu izler.



| Event Hubs ad alanı               | Olay Hub'ı adı | **Tüketici grubu** | Sahip                                | Bölüm Kimliği | Son değiştirme zamanı  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Her olay işlemcisi örneği bir bölümün sahipliğini alır ve bölümü bilinen son [denetim noktasından](# Checkpointing)işlemeye başlar. Bir işlemci başarısız olursa (VM kapanır), diğer örnekler bunu son değiştirme zamanına bakarak algılar. Diğer örnekler, önceden etkin olmayan örneğe ait olan bölümlerin sahipliğini almaya çalışır ve denetim noktası deposu, bir bölümün sahipliğinin yalnızca birinin başarılı olmasını garanti eder. Bu nedenle, belirli bir zamanda, bir bölümden olayları alan en fazla bir işlemci vardır.

## <a name="receive-messages"></a>İleti alma

Bir olay işlemcisi oluşturduğunuzda, olayları ve hataları işleyecek işlevleri belirtirsiniz. Olayları işleyen işleve yapılan her çağrı, belirli bir bölümden tek bir olay sağlar. Bu olayı işlemek sizin sorumluluğunuzdadır. Tüketicinin her iletiyi en az bir kez işlediğinden emin olmak istiyorsanız, yeniden deneme mantığı ile kendi kodunuzu yazmanız gerekir. Ancak kired iletiler hakkında dikkatli olun.

Şeyleri nispeten hızlı yapmanızı öneririz. Diğer bir deyişle, mümkün olduğunca az işlem yapın. Depolama alanına yazmanız ve bazı yönlendirme yapmanız gerekiyorsa, iki Tüketici grubu kullanmak ve iki olay işlemcisi olması daha iyidir.

## <a name="checkpointing"></a>Denetim noktası oluşturma

*Checkişaret* , bir olay işlemcisinin bir bölüm içinde son başarılı bir şekilde işlenen etkinliğin konumunu işaretleyen veya işleme yaptığı bir işlemdir. Bir kontrol noktasının işaretlenmesi genellikle olayları işleyen ve bir tüketici grubu içindeki bölüm başına temelinde gerçekleşen işlev içinde yapılır. 

Bir olay işlemcisinin bir bölümden bağlantısı kesilirse, başka bir örnek, bu bölümün daha önce ilgili tüketici grubundaki son işlemci tarafından kaydedilen denetim noktasında bölümü işlemeye da yardımcı olabilir. İşlemci bağlandığı zaman, okumayı başlatacak konumu belirtmek için Olay Hub 'ına geçişi geçirir. Bu şekilde, hem aşağı akış uygulamaları için olayları "tamamlanmış" olarak işaretlemek ve bir olay işlemcisi geçtiğinde dayanıklılık sağlamak için Checkmark 'ı kullanabilirsiniz. Bu denetim noktası sürecinden daha düşük bir konum belirterek eski verilere dönmek mümkündür. 

Denetim noktası bir olayı işlenen olarak işaretlemek için gerçekleştirildiğinde, denetim noktası deposundaki bir giriş, olayın kayması ve sıra numarası ile eklenir veya güncelleştirilir. Kullanıcılar, denetim noktasını güncelleştirme sıklığından önce karar almalıdır. Başarılı bir şekilde işlenen her olayın ardından güncelleştirme, temel alınan denetim noktası deposuna bir yazma işlemi tetiklediği için performans ve maliyet etkilerine sahip olabilir. Ayrıca, her bir olayın denetim noktası, bir Service Bus sırasının bir olay hub 'ından daha iyi bir seçenek olabileceği sıraya alınmış mesajlaşma deseninin bir göstergesi olduğunu gösteriyor. Event Hubs arkasındaki düşünce, harika ölçekte "en az bir kez" teslim almanızı öneririz. Aşağı akış sistemlerinizi ıdempotent yaparak, aynı olayların birden çok kez alınmasına neden olan hatalardan veya yeniden başlatmaları kurtarmak kolaydır.

> [!NOTE]
> Azure Blob Storage 'ı, Azure 'da bulunan farklı bir Storage blob SDK sürümünü destekleyen bir ortamda denetim noktası deposu olarak kullanıyorsanız, depolama hizmeti API sürümünü bu ortam tarafından desteklenen belirli bir sürümle değiştirmek için kodu kullanmanız gerekir. Örneğin, [Event Hubs bir Azure Stack hub sürümü 2002](/azure-stack/user/event-hubs-overview)çalıştırıyorsanız, depolama hizmeti için en yüksek sürüm 2017-11-09 ' dir. Bu durumda, Storage Service API sürümünü 2017-11-09 'e hedeflemek için kodu kullanmanız gerekir. Belirli bir depolama API sürümünün nasıl hedeflenecek hakkında bir örnek için GitHub 'da şu örneklere bakın: 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) veya  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>İş parçacığı güvenliği ve işlemci örnekleri

Varsayılan olarak, olayları işleyen işlev, belirli bir bölüm için sıralı olarak çağrılır. Olay göndericisi, diğer iş parçacıklarında arka planda çalışmaya devam ettiğinden, sonraki olaylar ve bu işleve yapılan aynı bölüm sırasından bu işleve yapılan çağrılar. Farklı bölümlerdeki olaylar eşzamanlı olarak işlenebilir ve bölümler arasında erişilen tüm paylaşılan durumları eşitlenmelidir.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki hızlı çalışmaya bakın:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
