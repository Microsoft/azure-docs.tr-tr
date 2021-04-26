---
title: Azure Event Hubs-farklı protokoller kullanarak Exchange olayları
description: Bu makalede, Azure Event Hubs kullanılırken farklı protokoller (AMQP, Apache Kafka ve HTTPS) kullanan tüketiciler ve üreticileri olayları nasıl değiş tokuş edebilir gösterilmektedir.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: cbc6999e3ede73b948ce034769966922b4b0f282
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010329"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Farklı protokoller kullanan tüketiciler ve üreticileri arasında Exchange olayları: AMQP, Kafka ve HTTPS
Azure Event Hubs, tüketiciler ve üreticileri için üç protokolü destekler: AMQP, Kafka ve HTTPS. Bu protokollerin her birinin bir iletiyi temsil eden kendi yolu vardır. bu nedenle, bir uygulama bir olay hub 'ına bir protokol ile olayları gönderirse ve farklı bir protokolle kullanırsa, olayın çeşitli parçaları ve değerleri tüketiciye ulaştığında nasıl görünür? Bu makalede, bir olay içindeki değerlerin, tüketen uygulama tarafından doğru şekilde yorumlandığından emin olmak için hem üretici hem de tüketici için en iyi yöntemler açıklanmaktadır.

Bu makaledeki öneri, kod parçacıklarını geliştirmekte kullanılan sürümleriyle birlikte bu istemcileri özellikle ele almaktadır:

* Kafka Java istemcisi (sürüm 1.1.1 from https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Java için Event Hubs Client Microsoft Azure (sürüm 1.1.0 https://github.com/Azure/azure-event-hubs-java)
* .NET için Event Hubs Client Microsoft Azure (sürüm 2.1.0 https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (sürüm 5.0.0 https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (yalnızca üreticileri destekler)

Diğer AMQP istemcileri biraz farklı davranabilir. AMQP 'nin iyi tanımlanmış bir tür sistemi vardır, ancak bu tür sisteminden ve bu türden dile özgü türlerin serileştirilmesi, istemcinin bir AMQP iletisinin bölümlerine erişim sağladığı şekilde istemciye bağlıdır.

## <a name="event-body"></a>Olay gövdesi
Tüm Microsoft AMQP istemcileri, olay gövdesini yorumlanan bayt olmayan bir paket olarak temsil eder. Üreten bir uygulama istemciye bir bayt dizisi geçirir ve tüketen bir uygulama istemciden aynı sırayı alır. Bayt sırasının yorumu uygulama kodu içinde gerçekleşir.

HTTPS üzerinden bir olay gönderilirken, olay gövdesi, aynı zamanda yorumlanamayan baytlar olarak kabul edilen içeriktir. Aşağıdaki kodda gösterildiği gibi, belirtilen ByteArraySerializer ve Bytearrayserializer kullanarak bir Kafka üreticisi veya TÜKETİCİSİNDE aynı duruma ulaşmak kolaydır:

### <a name="kafka-byte-producer"></a>Kafka Byte [] üreticisi

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka Byte [] tüketicisi
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Bu kod, uygulamanın iki yarısı arasında bir saydam bayt işlem hattı oluşturur ve uygulama geliştiricisinin, çalışma zamanında seri durumdan çıkarma, olay üzerinde kullanıcı kümesi özelliklerindeki tür veya gönderici bilgilerine dayalı olarak el ile serileştirmesi ve serileştirmesi dahil olmak üzere her türlü şekilde el ile serileştirmesi ve serileştirmesi sağlar.

Tek, sabit bir olay gövdesi türüne sahip uygulamalar, verileri saydam bir şekilde dönüştürmek için diğer Kafka serileştiricileri ve deserileştiriciler kullanabilir. Örneğin, JSON kullanan bir uygulamayı düşünün. JSON dizesinin oluşturulması ve yorumu uygulama düzeyinde gerçekleşir. Event Hubs düzeyinde, olay gövdesi her zaman bir dizedir, UTF-8 kodlamasında karakterleri temsil eden bir bayt dizisidir. Bu durumda, Kafka üreticisi veya tüketicisi, aşağıdaki kodda gösterildiği gibi, belirtilen StringSerializer veya StringDeserializer 'dan faydalanabilir:

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8 dize üreticisi
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 dize tüketicisi
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

AMQP tarafında hem Java hem de .NET, UTF-8 bayt dizilerinden ve dışı dizeleri dönüştürmek için yerleşik yollar sağlar. Microsoft AMQP istemcileri olayları EventData adlı bir sınıf olarak temsil eder. Aşağıdaki örneklerde bir AMQP üreticisi içindeki bir EventData olay gövdesine bir UTF-8 dizesinin serileştirilmesinin yanı sıra EventData olay gövdesinin bir AMQP TÜKETİCİSİNDE bir UTF-8 dizesine nasıl seri hale kullanılacağı gösterilmektedir.

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 dize üreticisi
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 dize tüketicisi
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8 dize üreticisi
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 dize tüketicisi
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Kafka açık kaynak olduğundan, uygulama geliştiricisi herhangi bir serileştirici veya seri hale getirici uygulamasını inceleyebilir ve kodu uygulayabilir; bu da AMQP tarafında uyumlu bir bayt dizisi üretir veya tüketir.

## <a name="event-user-properties"></a>Olay Kullanıcı Özellikleri

Kullanıcı kümesi özellikleri hem AMQP istemcilerinden ayarlanabilir hem de alınabilir (Microsoft AMQP istemcilerinde özellikler olarak adlandırılır) ve Kafka (üstbilgiler olarak adlandırılır). HTTPS göndericiler, bir olayda Kullanıcı özelliklerini, POST işleminde HTTP üstbilgileri olarak sağlayarak ayarlayabilir. Ancak Kafka, hem olay gövdelerini hem de olay üstbilgisi değerlerini bayt dizileri olarak değerlendirir. AMQP istemcilerinde, özellik değerleri, AMQP tür sistemine göre özellik değerlerini kodlayarak iletilen türlerdir.

HTTPS özel bir durumdur. Gönderme noktasındaki tüm özellik değerleri UTF-8 metinlerdir. Event Hubs hizmeti, uygun özellik değerlerini AMQP kodlu 32 bit ve 64 bit işaretli tamsayılara, 64 bit kayan nokta numaralarına ve Boole değerlerine dönüştürmek için sınırlı bir yorum miktarı sağlar. Bu türlerden birine uymayan herhangi bir özellik değeri dize olarak kabul edilir.

Özellik yazmak için bu yaklaşımların karıştırılması, bir Kafka tüketicinin AMQP türü bilgileri dahil olmak üzere ham AMQP kodlu bayt sırasını gördüğü anlamına gelir. Bir AMQP tüketicisi, uygulamanın yorumlanması gereken Kafka üreticisi tarafından gönderilen türsüz bir bayt dizisini görür.

AMQP veya HTTPS üreticileri 'tan Özellikler alan Kafka tüketicileri için, Kafka ekosistemindeki diğer serileştiricilerden sonra Modellenen AmqpDeserializer sınıfını kullanın. Veri baytlarının bir Java türünde serisini kaldırmak için AMQP kodlu bayt dizilerinden tür bilgilerini yorumlar.

En iyi uygulama olarak, AMQP veya HTTPS aracılığıyla gönderilen iletilere bir özellik eklemeniz önerilir. Kafka tüketicisi, başlık değerlerinin AMQP serisini kaldırma ihtiyacı olup olmadığını anlamak için bunu kullanabilir. Özelliğin değeri önemli değildir. Yalnızca Kafka tüketicinin başlık listesinde bulabileceği ve davranışını uygun şekilde ayarlayabileceği iyi bilinen bir ada ihtiyacı vardır.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP to Kafka Part 1: özelliklerle C# (.net) içinde bir olay oluşturun ve gönderin
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP to Kafka Part 2: bir Kafka tüketicisindeki bu özellikleri seri durumdan çıkarmak için AmqpDeserializer kullanın
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Uygulama bir özellik için beklenen türü biliyorsa, daha sonra atama gerektirmeyen seri kaldırma yöntemleri vardır, ancak özellik beklenen türden değilse bir hata oluşturur.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP to Kafka Part 3: bir Kafka TÜKETİCİSİNDE AmqpDeserializer kullanmanın farklı bir yolu
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Diğer yönden daha dahil olmak üzere, bir Kafka üreticisi tarafından ayarlanan üstbilgiler her zaman bir AMQP tüketicisi tarafından ham bayt olarak görülecağından (Java için Microsoft Azure Event Hubs Istemcisi veya `System.Byte[]` Microsoft 'un .net AMQP istemcileri için org. Apache. qpid. protoda En kolay yol, Kafka Producer tarafında üst bilgi değerleri için bayt oluşturmak üzere Kafka tarafından sağlanan serileştiriciden birini kullanmak ve ardından AMQP tüketicisi tarafında uyumlu bir seri hale getiriciler kodu yazmaktır.

AMQP ile Kafka gibi, önerdiğimiz en iyi yöntem, Kafka aracılığıyla gönderilen iletilere bir özellik dahil etmektir. AMQP tüketicisi, başlık değerlerinin serisini kaldırma ihtiyacı olup olmadığını anlamak için özelliğini kullanabilir. Özelliğin değeri önemli değildir. Yalnızca AMQP tüketicisinin üst bilgi listesinde bulabileceği ve davranışını uygun şekilde ayarlayabileceği iyi bilinen bir ada ihtiyacı vardır. Kafka üreticisi değiştirilemediğinde, tüketen uygulamanın özellik değerinin bir ikili veya bayt türünde olup olmadığını denetlemesi ve türe göre serisini kaldırma girişimi de mümkündür.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka to AMQP Bölüm 1: özellikler ile Kafka 'dan bir olay oluşturun ve gönderin
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka to AMQP Bölüm 2: C# dilinde bu özellikleri manuel olarak serisini kaldırma (.net)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka to AMQP Bölüm 3: Java 'da bu özellikleri manuel olarak serisini kaldırma
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, protokol istemcilerinizi değiştirmeden veya kendi kümelerinizi çalıştırmadan Event Hubs nasıl akışa alınacağını öğrendiniz. Kafka için Event Hubs ve Event Hubs hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:  

* [Event Hubs hakkında bilgi edinin](./event-hubs-about.md)
* [Kafka için Event Hubs hakkında bilgi edinin](event-hubs-for-kafka-ecosystem-overview.md)
* [Kafka için Event Hubs GitHub'ındaki diğer örnekleri keşfedin](https://github.com/Azure/azure-event-hubs-for-kafka)
* [Bulutta Event Hubs Için Kafka şirket içi olayları akışa](event-hubs-kafka-mirror-maker-tutorial.md) almak üzere [mirrormaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 'ı kullanın.
* [Yerel Kafka uygulamaları](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache flink](event-hubs-kafka-flink-tutorial.md)veya [Akka akışlarını](event-hubs-kafka-akka-streams-tutorial.md) kullanarak Event Hubs nasıl akışı yapabileceğinizi öğrenin
