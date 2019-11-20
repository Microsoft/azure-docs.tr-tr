---
title: 'Öğretici: Azure Spring Cloud ile dağıtılmış Izleme kullanma | Microsoft Docs'
description: Spring Cloud 'ın dağıtılmış Izlemesini Azure 'da nasıl kullanacağınızı öğrenin Application Insights
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: a9d2100103cdd5858d0d58cf6ef77a6ccac3745f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607560"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>Öğretici: Azure Spring Cloud ile dağıtılmış Izleme kullanma

Yay bulutu 'nın dağıtılmış Izleme Araçları, karmaşık sorunların kolay hata ayıklamasını ve izlenmesini sağlar. Azure yay bulutu, Azure portal tarafından güçlü bir dağıtılmış izleme özelliği sağlamak için Azure 'un [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) [yay bulutunu](https://spring.io/projects/spring-cloud-sleuth) zorbir şekilde tümleştirir.

Bu makalede, şunları nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * Azure portal dağıtılmış Izlemeyi etkinleştir
> * Uygulamanıza yay bulutu Uyuth ekleyin
> * Mikro hizmet uygulamalarınız için bağımlılık haritalarını görüntüleyin
> * Farklı filtrelerle izleme verilerini ara

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için:

* Zaten sağlanmış ve Azure yay bulut hizmeti çalıştırılıyor.  Azure yay bulut hizmeti sağlamak ve başlatmak için [Bu hızlı](spring-cloud-quickstart-launch-app-cli.md) başlangıcı doldurun.
    
## <a name="add-dependencies"></a>Bağımlılık Ekle

Aşağıdaki satırı Application. Properties dosyasına ekleyerek zipkafersender 'ın Web 'e göndermesini etkinleştirin:

```xml
spring.zipkin.sender.type = web
```

[Bir Azure yay bulutu uygulamasını önceden açmaya yönelik kılavuzumuzu](spring-cloud-tutorial-prepare-app-deployment.md)izlediyseniz bir sonraki adımı atlayabilirsiniz. Aksi takdirde, yerel geliştirme ortamınıza gidin ve `pom.xml` dosyanızı düzenleyerek yay bulutu uykuya geçme bağımlılığını ekleyin:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-sleuth</artifactId>
            <version>${spring-cloud-sleuth.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-sleuth</artifactId>
    </dependency>
</dependencies>
```

* Bu değişiklikleri yansıtacak şekilde Azure Spring Cloud Service için derleyin ve dağıtın. 

## <a name="modify-the-sample-rate"></a>Örnek hızını değiştirme
Örnek hızını değiştirerek telemetrinizin toplandığı oranı değiştirebilirsiniz. Örneğin, çoğunlukla yarı bir örnek örneklemek istiyorsanız `application.properties` dosyanıza gidin ve aşağıdaki satırı değiştirin:

```xml
spring.sleuth.sampler.probability=0.5
```

Zaten oluşturulmuş ve dağıtılmış bir uygulamanız varsa, yukarıdaki satırı Azure CLı veya portalına bir ortam değişkeni olarak ekleyerek örnek hızını değiştirebilirsiniz. 

## <a name="enable-application-insights"></a>Application Insights'ı etkinleştirme

1. Azure portal Azure Spring Cloud Service sayfanıza gidin.
1. Izleme bölümünde **Dağıtılmış izleme**' yi seçin.
1. Düzenlemek veya yeni bir ayar eklemek için **ayarı Düzenle** ' yi seçin.
1. Yeni bir Application Insight sorgusu oluşturun veya var olan bir uygulamayı seçebilirsiniz.
1. İzlemek istediğiniz günlüğe kaydetme kategorisini seçin ve bekletme süresini (gün cinsinden) belirtin.
1. Yeni izlemeyi uygulamak için **Uygula** ' yı seçin.

## <a name="view-application-map"></a>Uygulama haritasını görüntüle

Dağıtılmış Izleme sayfasına dönün ve **uygulama haritasını görüntüle**' yi seçin. Uygulamanızın ve izleme ayarlarının görsel temsilini gözden geçirin. Uygulama haritasını kullanmayı öğrenmek için, [Bu makaleye](https://docs.microsoft.com/azure/azure-monitor/app/app-map)bakın.

## <a name="search"></a>Arama

Diğer belirli telemetri öğelerini sorgulamak için Search işlevini kullanın. **Dağıtılmış izleme** sayfasında, **Ara**' yı seçin. Search işlevini kullanma hakkında daha fazla bilgi için [Bu makaleye](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)bakın.

## <a name="application-insights-page"></a>Application Insights sayfası

Application Insights, uygulama haritasına ve aramasına ek olarak izleme özellikleri sağlar. Azure portal uygulamanızın adı için arama yapın ve daha fazla bilgi edinmek için bir Application Insights sayfası başlatın. Bu araçların nasıl kullanılacağına ilişkin daha fazla bilgi için [belgelere göz](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)atın.


## <a name="disable-application-insights"></a>Application Insights devre dışı bırak

1. Azure portal Azure Spring Cloud Service sayfanıza gidin.
1. Izleme bölümünde, **Dağıtılmış izleme**' ye tıklayın.
1. Application Insights devre dışı bırakmak için **devre dışı bırak** 'ı

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Spring Cloud 'da dağıtılmış izlemeyi etkinleştirme ve anlama hakkında bilgi edindiniz. Uygulamanızı bir Azure CosmosDB 'ye bağlamayı öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Uygulamanızı bir Azure CosmosDB 'ye bağlamayı öğrenin](spring-cloud-tutorial-bind-cosmos.md).
