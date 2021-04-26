---
title: Azure Stream Analytics içindeki .NET özel deserileştiricileri kullanarak herhangi bir biçimdeki girişi okuyun
description: Bu makalede serileştirme biçimi ve Azure Stream Analytics bulut ve kenar işleri için özel .NET seri hale Getiricileri tanımlayan arabirimler açıklanmaktadır.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 140a836882ad3abe048047120e4fe1ebc0a3067c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018165"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>.NET özel seri hale getiriciler kullanarak her biçimdeki girişi oku

.NET özel seri hale getiriciler, Azure Stream Analytics işinizin üç [yerleşik veri biçimi](stream-analytics-parsing-json.md)dışındaki biçimlerden verileri okumasına izin verir. Bu makalede, bulut ve kenar işleri Azure Stream Analytics yönelik .NET özel seri hale Getiricileri tanımlayan serileştirme biçimi ve arabirimler açıklanmaktadır. Ayrıca protokol arabelleği ve CSV biçimi için örnek serileştiriciler vardır.

## <a name="net-custom-deserializer"></a>.NET özel seri hale getirici

Aşağıdaki kod örnekleri, özel seri hale getirici 'yi tanımlayan ve uygulayan arabirimlerdir `StreamDeserializer<T>` .

`UserDefinedOperator` Tüm özel akış işleçleri için temel sınıftır. Bu işlem başlatılır `StreamingContext` , bu, seri hale getiriciniz ile ilgili herhangi bir sorun için hata ayıklamanız gereken tanılama yayımlamak için düzenek içeren bağlam sağlar.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Aşağıdaki kod parçacığı, akış verilerinin serisini kaldırma işlemi olur. 

Geçişli hatalar, `IStreamingDiagnostics` geçirilen `UserDefinedOperator` Initialize yöntemi kullanılarak verilmelidir. Tüm özel durumlar hata olarak değerlendirilir ve seri hale getirici yeniden oluşturulur. Belirli sayıda hatalardan sonra iş başarısız durumuna geçer.

`StreamDeserializer<T>` bir akışı, türünde bir nesneye ayırır `T` . Aşağıdaki koşulların karşılanması gerekir:

1. T, bir sınıf veya struct.
1. T 'deki tüm ortak alanlar
    1. [SByte, Byte, Short, ushort, int, uint, Long, DateTime, String, float, Double] ya da Nullable eşdeğerlerine biridir.
    1. Aynı kuralları takip eden başka bir struct veya Class.
    1. `T2`Aynı kuralları izleyen tür dizisi.
    1. `T2`T2 'nin aynı kuralları Izlediği IList.
    1. Özyinelemeli tür yok.

Parametresi `stream` seri hale getirilen nesneyi içeren akışdır. `Deserialize` örnek koleksiyonunu döndürür `T` .

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` Kullanıcı operatörü için tanılamayı yayımlamaya yönelik mekanizmayı içeren bağlamı sağlar.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` seri hale getirici, seri hale getirici ve Kullanıcı tanımlı işlevler dahil Kullanıcı tanımlı operatörler için tanılamadır.

`WriteError` Kaynak günlüklerine bir hata iletisi yazar ve hatayı tanılama 'ya gönderir.

`briefMessage` kısa bir hata iletisidir. Bu ileti, tanılama 'da görünür ve ürün ekibi tarafından hata ayıklama amacıyla kullanılır. Hassas bilgileri eklemeyin ve iletiyi 200 karakterden az tutun

`detailedMessage` , yalnızca depolama ortamınızdaki kaynak günlüklerinizi eklenen ayrıntılı bir hata iletisidir. Bu ileti 2000 karakterden az olmalıdır.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Seri hale getirici örnekleri

Bu bölümde, Protoda ve CSV için özel seri hale getiricilerin nasıl yazılacağı gösterilmektedir. Event hub yakalama için AVRO biçimi gibi ek örnekler için [GitHub 'da Azure Stream Analytics](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)ziyaret edin.

### <a name="protocol-buffer-protobuf-format"></a>Protokol arabelleği (Protoarabelleği) biçimi

Bu, protokol arabelleği biçimini kullanan bir örnektir.

Aşağıdaki protokol arabelleği tanımını varsayın.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

`protoc.exe` **Google. Protoarabellek. Tools** NuGet 'den çalıştırmak, tanımıyla bir. cs dosyası oluşturur. Oluşturulan dosya burada gösterilmez. Stream Analytics projenizde kullandığınız Prototipsiz NuGet sürümünün girişi oluşturmak için kullanılan prototiple sürümle eşleştiğinden emin olmanız gerekir. 

Aşağıdaki kod parçacığı, üretilen dosyanın projeye dahil edildiğini kabul eden seri hale getirici uygulamasıdır. Bu uygulama, oluşturulan dosya üzerinde yalnızca bir ince sarmalayıcı olur.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

Aşağıdaki kod parçacığı, hataları yaymayı da gösteren basit bir CSV seri hale getirici 'dir.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>REST API 'Leri için serileştirme biçimi

Her Stream Analytics girişinin bir **serileştirme biçimi** vardır. Giriş seçenekleri hakkında daha fazla bilgi için bkz. [giriş REST API](/rest/api/streamanalytics/2016-03-01/inputs) belgeleri.

Aşağıdaki JavaScript kodu, REST API kullanılırken .NET seri hale getirici serileştirme biçiminin bir örneğidir:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName` , uygulayan bir sınıf olmalıdır `StreamDeserializer<T>` . Bu, aşağıdaki bölümde açıklanmıştır.

## <a name="region-support"></a>Bölge desteği

Bu özellik aşağıdaki bölgelerde kullanılabilir:

* Orta Batı ABD
* Kuzey Avrupa
* Doğu ABD
* Batı ABD
* Doğu ABD 2
* West Europe

Ek bölgeler için [destek isteyebilirsiniz](https://aka.ms/ccodereqregion) .

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Bu özellik tüm Azure bölgelerinde kullanılabilir olacaktır?

Bu özellik [6 bölgede](#region-support)kullanılabilir. Bu işlevi başka bir bölgede kullanmak istiyorsanız [bir istek gönderebilirsiniz](https://aka.ms/ccodereqregion). Tüm Azure bölgeleri için destek yol haritasında bulunur.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>GetMetadataPropertyValue işlevine benzer girişlerimin MetadataPropertyValue öğesine erişebilir miyim?

Bu işlev desteklenmiyor. Bu özelliğe ihtiyaç duyuyorsanız, bu istek için [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese)üzerinden oy verebilirsiniz.

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Seri hale getirici uygulamamı, diğerlerinin avantajlarından faydalanabilmesi için topluluk ile paylaşabilir miyim?

Seri hale getirinizi uyguladıktan sonra, diğer kişilere topluluk ile paylaşarak yardımcı olabilirsiniz. Kodunuzu [Azure Stream Analytics GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)deposuna gönderebilirsiniz.

### <a name="what-are-the-other-limitations-of-using-custom-deserializers-in-stream-analytics"></a>Stream Analytics özel seri hale getiricilerin kullanılmasıyla ilgili diğer sınırlamalar nelerdir?

Giriş, türü içeren bir şema ile `MapField` prototipsiz biçimindeyse, özel bir seri hale getirici uygulayabilmeniz mümkün olmayacaktır. Ayrıca, özel seri hale getiriciler örnek verileri veya önizleme verilerini desteklemez. 

## <a name="next-steps"></a>Sonraki Adımlar

* [Azure Stream Analytics bulut işleri için .NET özel seri hale getiriciler](custom-deserializer.md)
