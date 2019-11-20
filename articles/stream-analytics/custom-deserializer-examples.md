---
title: Azure Stream Analytics işleri için .NET deserileştiricileri kullanma
description: Bu makalede serileştirme biçimi ve Azure Stream Analytics bulut ve kenar işleri için özel .NET seri hale Getiricileri tanımlayan arabirimler açıklanmaktadır.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: f1452e56054948edffc6e9b3c98fa48d2589cb2a
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024932"
---
# <a name="use-net-deserializers-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics işleri için .NET deserileştiricileri kullanma

Özel .NET seri hale getiriciler, Azure Stream Analytics işinizin üç [yerleşik veri biçimi](stream-analytics-parsing-json.md)dışındaki biçimlerden verileri okumasına izin verir. Bu makalede serileştirme biçimi ve Azure Stream Analytics bulut ve kenar işleri için özel .NET seri hale Getiricileri tanımlayan arabirimler açıklanmaktadır. Ayrıca protokol arabelleği ve CSV biçimi için örnek serileştiriciler vardır.

## <a name="custom-net-deserializer"></a>Özel .NET seri hale getirici

Aşağıdaki kod örnekleri, özel seri hale getirici 'yi tanımlayan ve `StreamDeserializer<T>`uygulayan arabirimlerdir.

Tüm özel akış işleçleri için temel sınıftır `UserDefinedOperator`. Seri hale getiriciniz ile ilgili herhangi bir sorun için hata ayıklamanız gerekecektir. `StreamingContext`

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Aşağıdaki kod parçacığı, akış verilerinin serisini kaldırma işlemi olur. 

`UserDefinedOperator`Initialize yöntemi aracılığıyla geçirilen `IStreamingDiagnostics` kullanılarak, skiprilebilir hatalar oluşturulmalıdır. Tüm özel durumlar hata olarak değerlendirilir ve seri hale getirici yeniden oluşturulur. Belirli sayıda hatalardan sonra iş başarısız durumuna geçer.

`StreamDeserializer<T>` bir akışı `T`türünde bir nesneye ayırır. Aşağıdaki koşulların karşılanması gerekir:

1. T, bir sınıf veya struct.
1. T 'deki tüm ortak alanlar
    1. [SByte, Byte, Short, ushort, int, uint, Long, DateTime, String, float, Double] ya da Nullable eşdeğerlerine biridir.
    1. Aynı kuralları takip eden başka bir struct veya Class.
    1. Aynı kurallara göre `T2` türü dizi.
    1. IList, T2 'nin aynı kurallara göre izlediği`T2`.
    1. Özyinelemeli tür yok.

`stream` parametresi, serileştirilmiş nesneyi içeren akıştır. `Deserialize`, `T` örneklerinin bir koleksiyonunu döndürür.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`, Kullanıcı operatörü için tanılamayı yayımlamaya yönelik mekanizmayı içeren bağlam sağlar.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` seri hale getirici, seri hale getirici ve Kullanıcı tanımlı işlevler dahil Kullanıcı tanımlı işleçler için tanılamadır.

`WriteError` tanılama günlüklerine bir hata mesajı yazar ve hatayı tanılamalara gönderir.

`briefMessage` kısa bir hata iletisidir. Bu ileti, tanılama 'da görünür ve ürün ekibi tarafından hata ayıklama amacıyla kullanılır. Hassas bilgileri eklemeyin ve iletiyi 200 karakterden az tutun

`detailedMessage`, yalnızca depolama ortamınızdaki tanılama günlüklerinizi eklenen ayrıntılı bir hata iletisidir. Bu ileti 2000 karakterden az olmalıdır.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Seri hale getirici örnekleri

Bu bölümde, Protoda ve CSV için özel seri hale getiricilerin nasıl yazılacağı gösterilmektedir. Daha fazla örnek için [GitHub 'da Azure Stream Analytics](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)ziyaret edin.

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

**Google. Protoarabellek. Tools** NuGet içinden `protoc.exe` çalıştırmak, tanımıyla bir. cs dosyası oluşturur. Oluşturulan dosya burada gösterilmez.

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

Her Stream Analytics girişinin bir **serileştirme biçimi**vardır. Giriş seçenekleri hakkında daha fazla bilgi için bkz. [giriş REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) belgeleri.

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

`serializationClassName`, `StreamDeserializer<T>`uygulayan bir sınıf olmalıdır. Bu, aşağıdaki bölümde açıklanmıştır.

## <a name="region-support"></a>Bölge desteği

Bu özellik aşağıdaki bölgelerde kullanılabilir:

* Orta Batı ABD (kullanılabilir)
* Kuzey Avrupa (kullanılabilir)
* Doğu ABD (kullanılabilir)
* Batı ABD (yakında kullanıma alınıyor)
* Doğu ABD 2 (yakında kullanıma alınıyor)
* Batı Avrupa (yakında kullanıma alınıyor)

Ek bölgeler için [destek isteyebilirsiniz](https://aka.ms/ccodereqregion) .

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Bu özellik tüm Azure bölgelerinde kullanılabilir olacaktır?

Bu özellik 6 bölgede (#region destek) kullanılabilir. Bu işlevi başka bir bölgede kullanmak istiyorsanız [bir istek gönderebilirsiniz](https://aka.ms/ccodereqregion). Tüm Azure bölgeleri için destek yol haritasında bulunur.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>GetMetadataPropertyValue işlevine benzer girişlerimin MetadataPropertyValue öğesine erişebilir miyim?

Bu işlev desteklenmiyor. Bu özelliğe ihtiyaç duyuyorsanız, bu istek için [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese)üzerinden oy verebilirsiniz.

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Seri hale getirici uygulamamı, diğerlerinin avantajlarından faydalanabilmesi için topluluk ile paylaşabilir miyim?

Seri hale getirinizi uyguladıktan sonra, diğer kişilere topluluk ile paylaşarak yardımcı olabilirsiniz. Kodunuzu [Azure Stream Analytics GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)deposuna gönderebilirsiniz.

## <a name="next-steps"></a>Sonraki Adımlar

* [Azure Stream Analytics bulut işleri için özel .NET seri hale getiriciler](custom-deserializer.md)
