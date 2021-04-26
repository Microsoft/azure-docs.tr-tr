---
title: Azure Service Bus AMQP 1,0 'ye Genel Bakış
description: Azure Service Bus bir açık standart protokol olan Gelişmiş İleti Sıraya Alma Protokolü (AMQP) nasıl destekleyeceğinizi öğrenin.
ms.topic: article
ms.date: 04/08/2021
ms.openlocfilehash: 006511789bfa93f8e7d578ed21a73a2563fb4c6b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304431"
---
# <a name="advanced-message-queueing-protocol-amqp-10-support-in-service-bus"></a>Service Bus 'de gelişmiş Ileti sıraya alma Protokolü (AMQP) 1,0 desteği
Azure Service Bus bulut hizmeti, [AMQP 1,0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html) ' i birincil iletişim yöntemi olarak kullanır. Microsoft, sektördeki iş ortaklarıyla, hem müşteriler hem de rekabet eden mesajlaşma aracılarında, [Oasin AMQP Technical komite](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp)' de geliştirilen yeni uzantılarla, AMQP 'yi geçmiş yılda geliştirme ve geliştirme olanağı elde etti. AMQP 1,0, bir ISO ve ıEC Standard ([ıso 19464:20149](https://www.iso.org/standard/64955.html)). 

AMQP, satıcı tarafsız ve uygulamayla bağımsız bir açık standart protokol kullanarak platformlar arası, karma uygulamalar oluşturmanıza olanak sağlar. Farklı diller ve çerçeveler kullanılarak oluşturulan ve farklı işletim sistemlerinde çalışan bileşenleri kullanarak uygulamalar oluşturabilirsiniz. Tüm bu bileşenler Service Bus bağlanabilir ve yapılandırılmış iş iletilerini verimli bir şekilde ve tam Aslına göre sorunsuz şekilde değiştirebilir.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Giriş: AMQP 1,0 nedir ve neden önemlidir?
Geleneksel olarak, ileti yönelimli ara yazılım ürünleri, istemci uygulamaları ve aracılar arasındaki iletişim için özel protokoller kullandı. Bu, belirli bir satıcının mesajlaşma Aracısı 'nı seçtiğinizde, istemci uygulamalarınızı bu aracıya bağlamak için söz konusu satıcının kitaplıklarını kullanmanız gerektiğini gösterir. Bir uygulamanın farklı bir ürüne taşıma ile bağlantılı tüm uygulamalarda kod değişikliği yapılması gerektiğinden, bu satıcının bir ölçüde bağımlılığı vardır. Java Community 'de, Java Ileti hizmeti (JMS) gibi dile özgü API standartları ve yay çerçevesinin soyutlamaları, alleviated bir sorun olduğunu, ancak diğer dilleri kullanarak geliştiricilerin hariç tutulmasını sağlayan bir özelliktir.

Ayrıca, farklı satıcılardan gelen mesajlaşma aracılarını bağlamak daha da karmaşık. Genellikle, bir sistemden diğerine ileti taşımak ve özel ileti biçimleri arasında çeviri yapmak için uygulama düzeyinde köprü oluşturmayı gerektirir. Bu, yaygın bir gereksinimdir; Örneğin, daha eski farklı sistemlere yeni bir Birleşik arabirim sağlamanız veya bir birleşmeyi izleyen BT sistemlerini tümleştirmeniz gerekir. AMQP, aracıların doğrudan bağlanmasına olanak sağlar. Örneğin, [Apache Qpid gönderme yönlendiricisi](https://qpid.apache.org/components/dispatch-router/index.html) gibi yönlendiricileri veya [Kbbitmq](service-bus-integrate-with-rabbitmq.md)gibi bir aracı yerel "shovels" gibi yönlendiricileri kullanarak.

Yazılım endüstrisi, hızlı hareketli bir iş; yeni programlama dilleri ve uygulama çerçeveleri bazen bewildering hızda tanıtılmıştır. Benzer şekilde, BT sistemlerinin gereksinimleri zaman içinde geliştikçe, geliştiriciler en son Platform özelliklerinden faydalanmak ister. Ancak, bazen seçili mesajlaşma satıcısı bu platformları desteklemez. Mesajlaşma protokolleri özel ise başkalarının bu yeni platformlar için kitaplık sağlaması mümkün değildir. Bu nedenle, mesajlaşma ürününü kullanmaya devam edebilmeniz için ağ geçitleri veya köprüler oluşturma gibi yaklaşımları kullanmanız gerekir.

Gelişmiş İleti Sıraya Alma Protokolü (AMQP) 1,0 geliştirmesi bu sorunlar tarafından ele alınmıştır. Çoğu finansal hizmetler firmadan kaynaklanan JP Morgan Chase adresinden kaynaklandığından, ileti odaklı ara yazılım kullanıcıları ağır kullanıcılardır. Amaç basittir: farklı diller, çerçeveler ve işletim sistemleri kullanılarak oluşturulan bileşenleri kullanarak ileti tabanlı uygulamalar oluşturmaya olanak tanıyan bir açık standart mesajlaşma Protokolü oluşturmak için, hepsi bir dizi sağlayıcıdan alınan en iyi bileşen bileşenlerini kullanarak.

## <a name="amqp-10-technical-features"></a>AMQP 1,0 Teknik özellikleri
AMQP 1,0, güçlü, platformlar arası mesajlaşma uygulamaları oluşturmak için kullanabileceğiniz verimli, güvenilir, hat düzeyinde bir mesajlaşma protokolüdür. Protokol basit bir amaca sahiptir: iki taraf arasında güvenli, güvenilir ve verimli ileti aktarımının sayısını tanımlamak için. İletiler, heterojen gönderenlerin ve alıcıların yapılandırılmış iş iletilerini tam Aslına göre değiş tokuş etmesini sağlayan taşınabilir bir veri temsili kullanılarak kodlanır. En önemli özelliklerin özeti aşağıda verilmiştir:

* **Verimli**: amqp 1,0, protokol yönergeleri ve üzerinden aktarılan iş iletileri için ikili bir kodlama kullanan bağlantı yönelimli bir protokoldür. Ağ ve bağlı bileşenlerin kullanımını en üst düzeye çıkarmak için gelişmiş akış denetimi şemaları içerir. Bu, protokol verimlilik, esneklik ve birlikte çalışabilirlik arasında bir denge sunacak şekilde tasarlanmıştır.
* **Güvenilir**: amqp 1,0 protokolü, bir dizi güvenilirlik garantisi ile, yangın ve unutmaları, kabul edilen bir teslimin tamamen bir kez teslim edilmesini sağlar.
* **Esnek**: amqp 1,0, farklı topolojileri desteklemek için kullanılabilen esnek bir protokoldür. Aynı protokol istemciden istemciye, istemciden aracıya ve aracıdan aracıya iletişimler için kullanılabilir.
* **Broker-model bağımsız**: amqp 1,0 belirtimi, bir aracı tarafından kullanılan mesajlaşma modelinde herhangi bir gereksinim yapmaz. Bu, AMQP 1,0 desteğini mevcut mesajlaşma aracılarına kolayca eklemek mümkün olduğu anlamına gelir.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1,0 standart (büyük harf ' i içeren)
AMQP 1,0, ISO ve IEC tarafından ISO/ıEC 19464:2014 olarak onaylanan uluslararası bir standarttır.

AMQP 1,0, her iki teknoloji tedarikçisinden ve Son Kullanıcı firmasından 20 ' den fazla şirketten oluşan bir çekirdek grup tarafından 2008 ' den beri geliştirilmektedir. Bu süre boyunca, Kullanıcı firmaları gerçek iş gereksinimlerini karşılamaktadır ve teknoloji satıcıları bu gereksinimleri karşılamak için protokolü geliştirmiştir. İşlem boyunca, satıcılar, uygulamaları arasında birlikte çalışabilirliği doğrulamak üzere işbirliği yaptıkları atölyeler 'a katılmış.

2011 Ekim 'de, geliştirme işi, yapılandırılmış bilgi standartlarının (OASı 'ler) Ilerleme ve OASSıS AMQP 1,0 standardı, Ekim 2012 ' de piyasaya sürüldü. Aşağıdaki firmalar standart geliştirme sırasında teknik komite 'ye katılmış:

* **Teknoloji satıcıları**: Axway yazılımı, Huawei Technologies, IIT Software, ınetco sistemleri, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Kullanıcı firmaları**: Amerika Bankası, kredi Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

[Oasin AMQP Technical komite](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) 'in geçerli sandalyesi Red Hat ve Microsoft ' ü temsil etmektedir.

Açık standartların yaygın olarak alıntı yapılan avantajlarından bazıları şunlardır:

* Satıcı kilitlemeye daha az şans
* Birlikte çalışabilirlik
* Kitaplıkların ve araç yelpazelerinin geniş kullanılabilirliği
* Kullanımdan korunmaya karşı koruma
* Bilgili personelin kullanılabilirliği
* Daha düşük ve yönetilebilir risk

## <a name="amqp-10-and-service-bus"></a>AMQP 1,0 ve Service Bus
Azure Service Bus ' de AMQP 1,0 desteği, Service Bus Kuyruklama ve verimli bir ikili protokol kullanarak bir dizi platformda Aracılı mesajlaşma özelliklerini yayımlayabileceğiniz ve abone olabileceğiniz anlamına gelir. Ayrıca, bir dil, çerçeve ve işletim sistemi karışımı kullanılarak oluşturulan bileşenlerden oluşan uygulamalar oluşturabilirsiniz.

Aşağıdaki şekilde, Linux üzerinde çalışan Java istemcilerinin, Windows üzerinde çalışan standart Java Ileti hizmeti (JMS) API 'SI ve .NET istemcileri kullanılarak yazılmış, AMQP 1,0 kullanılarak Service Bus aracılığıyla Exchange iletileri kullanılarak yazıldığı örnek bir dağıtım gösterilmektedir.

![İki Linux ortamı ve iki Windows ortamı içeren bir Service Bus ileti alışverişi gösteren diyagram.][0]

**Şekil 1: Service Bus ve AMQP 1,0 kullanarak platformlar arası mesajlaşma gösteren örnek dağıtım senaryosu**

Azure SDK aracılığıyla kullanılabilen tüm desteklenen Service Bus istemci kitaplıkları AMQP 1,0 kullanır.

- [.NET için Azure Service Bus](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Java için Azure Service Bus kitaplıkları](/java/api/overview/azure/servicebus?preserve-view=true)
- [Java JMS 2,0 için Azure Service Bus sağlayıcısı](how-to-use-java-message-service-20.md)
- [JavaScript ve TypeScript için Azure Service Bus modülleri](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Python için Azure Service Bus kitaplıkları](/python/api/overview/azure/servicebus?preserve-view=true)

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Ayrıca, herhangi bir AMQP 1,0 uyumlu protokol yığınından Service Bus kullanabilirsiniz:

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>Özet
* AMQP 1,0, platformlar arası karma uygulamalar oluşturmak için kullanabileceğiniz açık ve güvenilir bir mesajlaşma protokolüdür. AMQP 1,0, bir OASSıS standardıdır.

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi edinmeye hazır mısınız? Aşağıdaki bağlantıları ziyaret edin:

* [AMQP ile .NET 'ten Service Bus kullanma]
* [AMQP ile Java 'dan Service Bus kullanma]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[AMQP ile .NET 'ten Service Bus kullanma]: service-bus-amqp-dotnet.md
[AMQP ile Java 'dan Service Bus kullanma]: ./service-bus-java-how-to-use-jms-api-amqp.md

