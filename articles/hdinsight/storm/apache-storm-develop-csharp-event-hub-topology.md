---
title: Azure HDInsight ile Event Hubs olayları işleme
description: Visual Studio için HDInsight araçları 'nı kullanarak, Azure Event Hubs C# Visual Studio 'da oluşturulmuş bir fırtınası topolojisine sahip verileri nasıl işleyebileceğinizi öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 62d65a4f004494ac4ce4ecd3df0f091460028d8f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800062"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>HDInsight 'ta Apache Storm ile Azure Event Hubs olayları işleme (C#)

HDInsight üzerinde [Apache Storm](https://storm.apache.org/) Azure Event Hubs ile nasıl çalışacağınızı öğrenin. Bu belge Event Hubs verileri C# okumak ve yazmak için bir fırtınası topolojisi kullanır

> [!NOTE]  
> Bu projenin Java sürümü için bkz. [HDInsight 'ta Apache Storm Ile Azure Event Hubs işleme olayları (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

## <a name="scpnet"></a>SCP.NET

Bu belgedeki adımlarda, HDInsight üzerinde fırtınası ile kullanım için topolojiler ve bileşenler oluşturmayı C# kolaylaştıran bir NuGet paketi olan SCP.net kullanılır.

> [!IMPORTANT]  
> Bu belgedeki adımlar Visual Studio ile bir Windows geliştirme ortamı kullandığından, derlenen proje Linux kullanan HDInsight kümesinde bir fırtınası 'ya gönderilebilir. Yalnızca 28 Ekim 2016 ' den sonra oluşturulan Linux tabanlı kümeler, SCP.NET topolojilerini destekler.

HDInsight 3,4 ve üzeri topoloji çalıştırmak C# için mono kullanın. Bu belgede kullanılan örnek HDInsight 3,6 ile birlikte çalışmaktadır. HDInsight için kendi .NET çözümlerinizi oluşturmayı planlıyorsanız olası uyumsuzluklar için [mono uyumluluk](https://www.mono-project.com/docs/about-mono/compatibility/) belgesini kontrol edin.

### <a name="cluster-versioning"></a>Küme sürümü oluşturma

Projeniz için kullandığınız Microsoft. SCP. net. SDK NuGet paketi, HDInsight üzerinde yüklü olan birincil fırtınası sürümüyle aynı olmalıdır. HDInsight sürümleri 3,5 ve 3,6, fırtınası 1. x kullanır, bu nedenle SCP.NET Version 1.0. x. x 'i bu kümeleriyle kullanmanız gerekir.

> [!IMPORTANT]  
> Bu belgedeki örnek bir HDInsight 3,5 veya 3,6 kümesi bekliyor.
>
> Linux, HDInsight sürüm 3.4 ve üzerinde kullanılan tek işletim sistemidir. 

C#topolojiler ayrıca .NET 4,5 ' i de hedeflemelidir.

## <a name="how-to-work-with-event-hubs"></a>Event Hubs ile çalışma

Microsoft, bir fırtınası topolojisinden Event Hubs iletişim kurmak için kullanılabilecek bir Java bileşenleri kümesi sağlar. Bu bileşenlerin HDInsight 3,6 ile uyumlu bir sürümünü içeren Java Arşivi (JAR) dosyasını [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)adresinde bulabilirsiniz.

> [!IMPORTANT]  
> Bileşenler Java 'da yazılırken, bunları bir C# topolojiden kolayca kullanabilirsiniz.

Aşağıdaki bileşenler bu örnekte kullanılır:

* __Eventhubspout__: Event Hubs verilerini okur.
* __Eventhubcıvata__: verileri Event Hubs yazar.
* __Eventhubspoutconfig__: EventHubSpout 'yi yapılandırmak için kullanılır.
* __Eventhubcıvatconfig__: eventhubcıvaı yapılandırmak için kullanılır.

### <a name="example-spout-usage"></a>Örnek Spout kullanımı

SCP.NET topolojinize EventHubSpout eklemek için yöntemler sağlar. Bu yöntemler, bir Java bileşeni eklemek için genel yöntemleri kullanmaktan daha kolay bir Spout eklemeyi kolaylaştırır. Aşağıdaki örnek, SCP.NET tarafından sunulan __Seteventhubspout__ ve **Eventhubspoutconfig** yöntemlerini kullanarak Spout oluşturmayı göstermektedir:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

Önceki örnekte __Eventhubspout__adlı yeni bir Spout bileşeni oluşturulur ve bunu bir olay hub 'ı ile iletişim kuracak şekilde yapılandırır. Bileşen için paralellik ipucu, Olay Hub 'ındaki bölüm sayısına ayarlanır. Bu ayar, fırtınası 'nın her bölüm için bileşenin bir örneğini oluşturmasına izin verir.

### <a name="example-bolt-usage"></a>Örnek sürgüsü kullanımı

Bir cıvacının örneğini oluşturmak için **Javabileşen Mentconstructor** metodunu kullanın. Aşağıdaki örnek, **Eventhubcıvaun**yeni bir örneğini oluşturmayı ve yapılandırmayı gösterir:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> Bu örnek, Spout örneği gibi bir **Eventhubcıvatconfig**oluşturmak Için **javacomponentconstructor** yerine bir dize olarak geçirilen bir Clojure ifadesi kullanır. Her iki yöntem de işe yarar. Size en uygun yöntemi kullanın.

## <a name="download-the-completed-project"></a>Tamamlanmış projeyi indirin

Bu makalede oluşturulan projenin tüm sürümünü [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)'dan indirebilirsiniz. Ancak, bu makaledeki adımları izleyerek yapılandırma ayarlarını yine de sağlamanız gerekir.

### <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Storm kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) ve **küme türü**için **fırtınası** seçme.

    > [!WARNING]  
    > Bu belgede kullanılan örnek, HDInsight sürüm 3,5 veya 3,6 ' de fırtınası gerektirir. Bu, son sınıf adı değişiklikleri nedeniyle HDInsight 'ın eski sürümleriyle çalışmaz. Bu örneğin eski kümeler ile birlikte çalışarak bir sürümü için bkz. [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* [Azure Olay Hub 'ı](../../event-hubs/event-hubs-create.md).

* [Azure .NET SDK 'sı](https://azure.microsoft.com/downloads/).

* [Visual Studio Için HDInsight araçları](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Geliştirme ortamınızda Java JDK 1,8 veya sonraki bir sürümü. JDK İndirmeleri [Oracle](https://aka.ms/azure-jdks)'dan edinilebilir.

  * **JAVA_HOME** ortam değişkeni, Java içeren dizine işaret etmelidir.
  * **% JAVA_HOME%/bin** dizini yolda olmalıdır.

## <a name="download-the-event-hubs-components"></a>Event Hubs bileşenlerini indirin

[https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)Event Hubs Spout ve cıvam bileşenini indirin.

`eventhubspout`adlı bir dizin oluşturun ve dosyayı dizine kaydedin.

## <a name="configure-event-hubs"></a>Event Hubs Yapılandır

Event Hubs Bu örnek için veri kaynağıdır. [Event Hubs kullanmaya başlama](../../event-hubs/event-hubs-create.md)konusunun "bir olay hub 'ı oluşturma" bölümündeki bilgileri kullanın.

1. Olay Hub 'ı oluşturulduktan sonra, Azure portal **EventHub** ayarlarını görüntüleyin ve **paylaşılan erişim ilkeleri**' ni seçin. Aşağıdaki ilkeleri eklemek için **+ Ekle** ' yi seçin:

   | Adı | İzinler |
   | --- | --- |
   | yazarı |Gönder |
   | Okuyucu |Dinle |

    ![Erişim ilkelerini paylaşma penceresinin ekran görüntüsü](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. **Okuyucu** ve **Yazıcı** ilkelerini seçin. Bu değerler daha sonra kullanıldığından, her iki ilke için de birincil anahtar değerini kopyalayın ve kaydedin.

## <a name="configure-the-eventhubwriter"></a>EventHubWriter 'ı yapılandırma

1. Visual Studio için HDInsight araçları 'nın en son sürümünü henüz yüklemediyseniz bkz. [Visual Studio Için HDInsight araçlarını kullanmaya başlama](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Çözümü [eventhub-fırtınası-karma](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)konumundan indirin.

3. **Eventhubwriter** projesinde **app. config** dosyasını açın. Daha önce aşağıdaki anahtarlar için değeri dolduracak olan olay hub 'ında bulunan bilgileri kullanın:

   | Anahtar | Değer |
   | --- | --- |
   | EventHubPolicyName |Yazıcı (ilke için *gönderme* izni olan farklı bir ad kullandıysanız bunun yerine kullanın.) |
   | EventHubPolicyKey |Yazıcı ilkesi için anahtar. |
   | EventHubNamespace |Olay Hub 'ınızı içeren ad alanı. |
   | EventHubName |Olay Hub 'ınız adı. |
   | EventHubPartitionCount |Olay Hub 'ınızdaki bölüm sayısı. |

4. **App. config** dosyasını kaydedin ve kapatın.

## <a name="configure-the-eventhubreader"></a>EventHubReader 'ı yapılandırma

1. **Eventhubreader** projesini açın.

2. **Eventhubreader**için **app. config** dosyasını açın. Daha önce aşağıdaki anahtarlar için değeri dolduracak olan olay hub 'ında bulunan bilgileri kullanın:

   | Anahtar | Değer |
   | --- | --- |
   | EventHubPolicyName |okuyucu ( *dinleme* izniyle ilke için farklı bir ad kullandıysanız, bunun yerine kullanın.) |
   | EventHubPolicyKey |Okuyucu ilkesi için anahtar. |
   | EventHubNamespace |Olay Hub 'ınızı içeren ad alanı. |
   | EventHubName |Olay Hub 'ınız adı. |
   | EventHubPartitionCount |Olay Hub 'ınızdaki bölüm sayısı. |

3. **App. config** dosyasını kaydedin ve kapatın.

## <a name="deploy-the-topologies"></a>Topolojileri dağıtma

1. **Çözüm Gezgini**, **Eventhubreader** projesine sağ tıklayın ve **HDInsight 'ta fırtınası 'ya gönder**' i seçin.

    ![HDInsight 'ta fırtınası üzerine gönder vurgulanmış Çözüm Gezgini ekran görüntüsü](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. **Topolojiyi gönder** iletişim kutusunda, **fırtınası kümenizi**seçin. **Ek konfigürasyonlar**' ı genişletin, **Java dosya yolları**' nı seçin, **...** ' ı seçin ve daha önce indirdiğiniz jar dosyasını içeren dizini seçin. Son olarak **Gönder**' e tıklayın.

    ![Gönderme topolojisi iletişim kutusunun ekran görüntüsü](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. Topoloji gönderildiğinde, **fırtınası topolojileri Görüntüleyicisi** görüntülenir. Topoloji hakkındaki bilgileri görüntülemek için sol bölmedeki **Eventhubreader** topolojisini seçin.

    ![Fırtınası topolojileri görüntüleyicisinin ekran görüntüsü](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. **Çözüm Gezgini**, **Eventhubwriter** projesine sağ tıklayın ve **HDInsight 'ta fırtınası 'ya gönder**' i seçin.

5. **Topolojiyi gönder** iletişim kutusunda, **fırtınası kümenizi**seçin. **Ek konfigürasyonlar**' ı genişletin, **Java dosya yolları**' nı seçin, **...** ' ı seçin ve daha önce indirdiğiniz jar dosyasını içeren dizini seçin. Son olarak **Gönder**' e tıklayın.

6. Topoloji gönderildiğinde, her iki topolojinin kümede çalıştığından emin olmak için, **fırtınası topolojileri görüntüleyicisinde** topoloji listesini yenileyin.

7. **Fırtınası topolojileri görüntüleyicisinde** **Eventhubreader** topolojisini seçin.

8. Cıvatanın bileşen özetini açmak için diyagramdaki **Logcıvata** bileşeni çift tıklayın.

9. **Yürüticileri** bölümünde **bağlantı noktası** sütunundaki bağlantılardan birini seçin. Bu, bileşen tarafından günlüğe kaydedilen bilgileri görüntüler. Günlüğe kaydedilen bilgiler aşağıdaki metne benzer:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Topolojileri durdurma

Topolojileri durdurmak için, **fırtınası topolojisi görüntüleyicisinde**her bir topoloji seçin ve ardından **Sonlandır**' ı tıklatın.

![Sonlandırma düğmesi vurgulanmış olan fırtınası topolojisi görüntüleyicisinin ekran görüntüsü](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Kümenizi silme

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure Event Hubs 'de verilerle çalışmak için bir C# topolojiden Java Event Hubs Spout ve cıvatın nasıl kullanılacağını öğrendiniz. Topolojiler oluşturma C# hakkında daha fazla bilgi için aşağıdakilere bakın:

* [Visual C# Studio kullanarak hdınsight 'ta Apache Storm için topolojiler geliştirin](apache-storm-develop-csharp-visual-studio-topology.md)
* [SCP Programlama Kılavuzu](apache-storm-scp-programming-guide.md)
* [HDInsight üzerinde Apache Storm için örnek topolojiler](apache-storm-example-topology.md)
