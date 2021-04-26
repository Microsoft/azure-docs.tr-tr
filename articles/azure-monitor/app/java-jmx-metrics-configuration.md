---
title: JMX ölçümlerini yapılandırma-Java için Azure Izleyici Application Insights
description: Azure Izleyici uygulama öngörüleri Java Aracısı için ek JMX ölçümleri koleksiyonu yapılandırma
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610021"
---
# <a name="configuring-jmx-metrics"></a>JMX ölçümlerini yapılandırma

Application Insights Java 3,0 Aracısı, bazı JMX ölçümlerini varsayılan olarak toplar, ancak çoğu durumda bu yeterli değildir. Bu belgede, Ayrıntılar bölümünde JMX yapılandırma seçeneği açıklanmaktadır.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>Nasıl yaparım? ek JMX ölçümleri toplasın mı?

JMX ölçüm koleksiyonu ```"jmxMetrics"``` , applicationinsights.jsdosyadaki bir bölüm eklenerek yapılandırılabilir. Ölçüm adını, Application Insights kaynağında Azure portal görünmesini istediğiniz şekilde belirtebilirsiniz. Toplanmasını istediğiniz her ölçüm için nesne adı ve özniteliği tanımlamanız gerekir.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>Nasıl yaparım? hangi ölçümlerin yapılandırılacağını öğrensin mi?

Bu şekilde, nesne adlarını ve özniteliklerini bilmeniz gerekir, bu özellikler çeşitli kitaplıklar, çerçeveler ve uygulama sunucuları için farklıdır ve genellikle iyi belgelenmiş değildir. Nesne adlarını ve özniteliklerini almak için, MBean ağacını görüntülemeniz gerekir. MBean, bir cihazı, uygulamayı veya kaynağı temsil eden ve bir öznitelikler kümesine sahip olan yönetilen bir Java nesnesidir. 

Kullanılabilir ölçümleri görüntülemek ve kullanılabilir ölçümleri göz atarak [Java görev denetimini](https://www.oracle.com/java/technologies/jdk-mission-control.html)kullanmanızı öneririz.

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Doğru ölçümlere ulaşmak için Java görev denetimi 'ne nasıl gidebileceğiniz?

Java görev denetimi aracını çalıştırdığınızda, sol tarafta kullanılabilir olan JVM 'lerin bir seçiminiz olacak ve ' JVM Browser ' sekmesi altında ilgili işleme tıklayabilirsiniz. JMC işlem için panoyu yükleyinceye kadar bekleyin, alt kısımdaki ' MBean tarayıcı ' sekmesini seçin (aşağıya bakın). JMC, JVM ile aynı klasörde bulunmalıdır ve işleminiz/uygulamanız çalışır durumda olmalıdır.

![JMC MBean tarayıcısının ekran görüntüsü](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>İstediğim ölçümleri ve gerekli öznitelikleri nasıl edinebilirim?

MBean tarayıcısı, genişletilebilen kategorilerin listesi ile MBean ağacını açar. Sol taraftaki bir kategorinin seçilmesi sağdaki özniteliklerin listesini açar. Aşağıda bir ölçüm, nesne adı ve öznitelikleri örneği verilmiştir. Öznitelikler, aşağıdaki örnekte olduğu gibi iç içe geçmiş olabilir.

![JMC MBean ağacının ekran görüntüsü](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>Yapılandırma örneği

Yukarıdaki görüntüde gösterildiği gibi seçimden birkaç ölçüm yapılandırılacağını sağlar. Birincisi, iç içe geçmiş bir ölçüm örneğidir `LastGcInfo` . birkaç özelliğe sahiptir ve yakalamak istiyoruz `GcThreadCount` .

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>Toplanan ölçüm türleri ve kullanılabilir yapılandırma seçenekleri nelerdir?

Sayısal ve Boolean JMX ölçümlerini destekliyoruz, ancak diğer türler desteklenmez ve yok sayılır. 

Şu anda, her özniteliğin ' nesne adı '/' öznitelik ' çiftinin ayrı olarak yapılandırılması için joker karakterler ve toplanmış öznitelikler desteklenmez. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Application Insights 'ta JMX ölçümlerini nerede bulabilirim?

Uygulamanız çalışırken ve JMX ölçümleri toplandığında, Azure portal adresine giderek ve Application Insights kaynağına giderek bunları görüntüleyebilirsiniz. Ölçümler sekmesinde aşağıda gösterildiği gibi, ölçümleri görüntülemek için aşağı açılan listeyi seçin.

![Portaldaki ölçümlerin ekran görüntüsü](media/java-ipa/jmx/jmx-portal.png)
