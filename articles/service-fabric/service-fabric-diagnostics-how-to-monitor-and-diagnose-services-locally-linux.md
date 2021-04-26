---
title: Linux 'ta Azure Service Fabric uygulamalarında hata ayıklama
description: Yerel bir Linux geliştirme makinesinde Service Fabric hizmetlerinizi izlemeyi ve tanılamayı öğrenin.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 523cb0d1a8e8f322c1936f1fe52a954399b2acc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88999776"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Yerel bir Linux makine geliştirme kurulumunda hizmetleri izleme ve tanılama


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Hizmetlerin Kullanıcı deneyimine en düşük kesintilerle devam etmesine izin vermek için izleme, algılama, tanılama ve sorun giderme. İzleme ve tanılama, gerçek dağıtılan bir üretim ortamında kritik öneme sahiptir. Hizmet geliştirme sırasında benzer bir modeli benimseme, bir üretim ortamına geçtiğinizde tanılama işlem hattının çalışmasına olanak sağlar. Service Fabric, Service Developers 'ın hem tek makineli yerel geliştirme kurulumları hem de gerçek dünya üretim kümesi kurulumları üzerinde sorunsuz bir şekilde çalışması için tanılamayı uygulamasını kolaylaştırır.


## <a name="debugging-service-fabric-java-applications"></a>Java uygulamalarında hata ayıklama Service Fabric

Java uygulamaları için [birden çok günlük](https://en.wikipedia.org/wiki/Java_logging_framework) çerçevesi mevcuttur. , `java.util.logging` JRE 'nin varsayılan seçeneği olduğundan, [GitHub 'daki kod örnekleri](https://github.com/Azure-Samples/service-fabric-java-getting-started)için de kullanılır. Aşağıdaki tartışmada Framework 'ün nasıl yapılandırılacağı açıklanmaktadır `java.util.logging` .

Java. util. Logging kullanarak, uygulama günlüklerinizi bellek, çıkış akışları, konsol dosyaları veya yuvalara yeniden yönlendirebilirsiniz. Bu seçeneklerin her biri için çerçevede zaten sağlanmış olan varsayılan işleyiciler vardır. `app.properties`Tüm günlükleri yerel bir dosyaya yeniden yönlendirmek üzere uygulamanız için dosya işleyicisini yapılandırmak üzere bir dosya oluşturabilirsiniz.

Aşağıdaki kod parçacığı örnek bir yapılandırma içerir:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Dosya tarafından işaret edilen klasör `app.properties` var olmalıdır. `app.properties`Dosya oluşturulduktan sonra, `entrypoint.sh` `<applicationfolder>/<servicePkg>/Code/` özelliği dosya olarak ayarlamak için, giriş noktası komut dosyanızı klasöre de değiştirmeniz gerekir `java.util.logging.config.file` `app.properties` . Giriş aşağıdaki kod parçacığı gibi görünmelidir:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Bu yapılandırma, üzerinde bir döndürme sırasında günlüklerin toplanmasına neden olur `/tmp/servicefabric/logs/` . Bu durumda bulunan günlük dosyası, burada yer aldığı Kapsamım% u .% g. log:
* **% u** eşzamanlı Java işlemlerinde çakışmaları çözümlemek için benzersiz bir sayıdır.
* **% g** , döndürme günlükleri arasında ayrım yapmak için oluşturma numarasıdır.

Varsayılan olarak, hiçbir işleyici açıkça yapılandırılmamışsa konsol işleyicisi kaydedilir. Bir tane,/var/log/Syslog altındaki Syslog 'da günlükleri görüntüleyebilir

Daha fazla bilgi için [GitHub 'daki kod örneklerine](https://github.com/Azure-Samples/service-fabric-java-getting-started)bakın.


## <a name="debugging-service-fabric-c-applications"></a>C# uygulamalarında hata ayıklama Service Fabric


Linux 'ta CoreCLR uygulamalarını izlemek için birden çok çerçeve mevcuttur. Daha fazla bilgi için bkz. [günlük için .net uzantıları](https://github.com/dotnet/extensions/tree/master/src/Logging).  EventSource, C# geliştiricilerine tanıdık olduğundan, ' Bu makale Linux 'ta CoreCLR örnekleri içinde izlemek için EventSource kullanır.

İlk adım, günlüklerinizi belleğe, Çıkış akışlarına veya konsol dosyalarına yazabilbilmeniz için System. Diagnostics. Tracing ' i dahil etmek için kullanılır.  EventSource kullanarak günlüğe kaydetmek için aşağıdaki projeyi project.jsekleyin:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Hizmet olayını dinlemek için özel bir EventListener kullanabilir ve sonra bunları izleme dosyalarına uygun şekilde yeniden yönlendirebilirsiniz. Aşağıdaki kod parçacığı, EventSource ve özel bir EventListener kullanarak günlüğe kaydetmenin örnek bir uygulamasını gösterir:


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


Yukarıdaki kod parçacığı, içindeki bir dosyaya Günlükler verir `/tmp/MyServiceLog.txt` . Bu dosya adının uygun şekilde güncelleştirilmesi gerekir. Günlükleri konsola yönlendirmek isterseniz, özelleştirilmiş EventListener Sınıfınıza aşağıdaki kod parçacığını kullanın:

```csharp
public static TextWriter Out = Console.Out;
```

[C# örneklerindeki](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) örnekler, olayları bir dosyaya günlüğe kaydetmek için EventSource ve özel bir EventListener kullanır.



## <a name="next-steps"></a>Sonraki adımlar
Uygulamanıza eklenen izleme kodu aynı zamanda uygulamanızın bir Azure kümesinde tanılamaları ile de birlikte kullanılabilir. Araçların farklı seçeneklerini tartışan ve bunların nasıl ayarlanacağını anlatan bu makalelere göz atın.
* [Azure Tanılama ile günlükleri toplama](./service-fabric-diagnostics-event-aggregation-lad.md)
