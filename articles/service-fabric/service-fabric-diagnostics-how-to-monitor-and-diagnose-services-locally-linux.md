---
title: Linux 'ta Azure Service Fabric uygulamalarında hata ayıklama | Microsoft Docs
description: Yerel bir Linux geliştirme makinesinde Service Fabric hizmetlerinizi izlemeyi ve tanılamayı öğrenin.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 017b359f4c6da438f5179813fa3ed1ad2c536834
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168868"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Yerel makine geliştirme kurulumunda hizmetleri izleme ve tanılama


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Hizmetlerin Kullanıcı deneyimine en düşük kesintilerle devam etmesine izin vermek için izleme, algılama, tanılama ve sorun giderme. İzleme ve tanılama, gerçek dağıtılan bir üretim ortamında kritik öneme sahiptir. Hizmet geliştirme sırasında benzer bir modeli benimseme, bir üretim ortamına geçtiğinizde tanılama işlem hattının çalışmasına olanak sağlar. Service Fabric, Service Developers 'ın hem tek makineli yerel geliştirme kurulumları hem de gerçek dünya üretim kümesi kurulumları üzerinde sorunsuz bir şekilde çalışması için tanılamayı uygulamasını kolaylaştırır.


## <a name="debugging-service-fabric-java-applications"></a>Java uygulamalarında hata ayıklama Service Fabric

Java uygulamaları için [birden çok günlük](https://en.wikipedia.org/wiki/Java_logging_framework) çerçevesi mevcuttur. @No__t-0, JRE 'nin varsayılan seçeneği olduğundan, [GitHub 'daki kod örnekleri](https://github.com/Azure-Samples/service-fabric-java-getting-started)için de kullanılır. Aşağıdaki tartışmada `java.util.logging` çerçevesinin nasıl yapılandırılacağı açıklanmaktadır.

Java. util. Logging kullanarak, uygulama günlüklerinizi bellek, çıkış akışları, konsol dosyaları veya yuvalara yeniden yönlendirebilirsiniz. Bu seçeneklerin her biri için çerçevede zaten sağlanmış olan varsayılan işleyiciler vardır. Tüm günlükleri yerel bir dosyaya yeniden yönlendirmek üzere uygulamanız için dosya işleyicisini yapılandırmak üzere bir `app.properties` dosyası oluşturabilirsiniz.

Aşağıdaki kod parçacığı örnek bir yapılandırma içerir:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

@No__t-0 dosyası tarafından işaret edilen klasör var olmalıdır. @No__t-0 dosyası oluşturulduktan sonra, `java.util.logging.config.file` ' i `app.properties` dosyasına ayarlamak için giriş noktası komut dosyanızı `<applicationfolder>/<servicePkg>/Code/` klasöründeki `entrypoint.sh` ' i de değiştirmeniz gerekir. Giriş aşağıdaki kod parçacığı gibi görünmelidir:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Bu yapılandırma, `/tmp/servicefabric/logs/` ' dan dönen bir biçimde günlüklerin toplanmasına neden olur. Bu durumda bulunan günlük dosyası, burada yer aldığı Kapsamım% u.% g. log:
* **% u** eşzamanlı Java işlemlerinde çakışmaları çözümlemek için benzersiz bir sayıdır.
* **% g** , döndürme günlükleri arasında ayrım yapmak için oluşturma numarasıdır.

Varsayılan olarak, hiçbir işleyici açıkça yapılandırılmamışsa konsol işleyicisi kaydedilir. Bir tane,/var/log/Syslog altındaki Syslog 'da günlükleri görüntüleyebilir

Daha fazla bilgi için [GitHub 'daki kod örneklerine](https://github.com/Azure-Samples/service-fabric-java-getting-started)bakın.


## <a name="debugging-service-fabric-c-applications"></a>Service Fabric C# uygulamalarında hata ayıklama


Linux 'ta CoreCLR uygulamalarını izlemek için birden çok çerçeve mevcuttur. Daha fazla bilgi için bkz. [GitHub: Logging](http:/github.com/aspnet/logging).  EventSource C# geliştiricilere tanıdık olduğundan, ' Bu makale Linux 'Ta CoreCLR örnekleri içinde Izlemek için EventSource kullanır.

İlk adım, günlüklerinizi belleğe, Çıkış akışlarına veya konsol dosyalarına yazabilbilmeniz için System. Diagnostics. Tracing ' i dahil etmek için kullanılır.  EventSource kullanarak günlüğe kaydetmek için, Project. json ' a aşağıdaki projeyi ekleyin:

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
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
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


Yukarıdaki kod parçacığı, günlükleri `/tmp/MyServiceLog.txt` ' daki bir dosyaya çıktı. Bu dosya adının uygun şekilde güncelleştirilmesi gerekir. Günlükleri konsola yönlendirmek isterseniz, özelleştirilmiş EventListener Sınıfınıza aşağıdaki kod parçacığını kullanın:

```csharp
public static TextWriter Out = Console.Out;
```

Örneklerdeki örnekler, olayları bir dosyaya günlüğe kaydetmek için EventSource ve özel bir EventListener kullanır. [ C# ](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)



## <a name="next-steps"></a>Sonraki adımlar
Uygulamanıza eklenen izleme kodu aynı zamanda uygulamanızın bir Azure kümesinde tanılamaları ile de birlikte kullanılabilir. Araçların farklı seçeneklerini tartışan ve bunların nasıl ayarlanacağını anlatan bu makalelere göz atın.
* [Azure Tanılama ile günlükleri toplama](service-fabric-diagnostics-how-to-setup-lad.md)
