---
title: Uzaktan Izleme çözüm hızlandırıcısına genel bakış-Azure | Microsoft Docs
description: Bu makalede, uzaktan Izleme çözümünün nasıl çalıştığını anlamanıza olanak tanımak için bazı önemli öğelere genel bakış sunulmaktadır.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 33005bc286f7dc2c0ebed74bd9df0309e71346f5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824828"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Uzaktan İzleme çözümü hızlandırıcısına genel bakış

Uzaktan Izleme [çözümü Hızlandırıcısı](../iot-accelerators/about-iot-accelerators.md) , uzak konumlardaki birden fazla makine için uçtan uca izleme çözümü uygular. Bu çözüm, iş senaryosunun genel uygulamasını sağlamak üzere temel Azure hizmetlerini bir araya getirir. Çözümü kendi uygulamanız için başlangıç noktası olarak kullanabilir ve kendi özel iş gereksinimlerinizi karşılayacak şekilde [özelleştirebilirsiniz](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) .

Bu makale, nasıl çalıştığını anlamanıza olanak tanımak için uzaktan Izleme çözümünün bazı önemli öğelerine kılavuzluk eder. Bu bilgiler şunları yapmanıza yardımcı olur:

* Çözümdeki sorunları giderme.
* Çözümü kendinize özel gereksinimleri karşılayacak şekilde nasıl özelleştireceğinizi planlama.
* Azure hizmetlerini kullanan kendi IoT çözümünüzü tasarlama.

Uzaktan Izleme çözümü Hızlandırıcısı kodu GitHub 'da kullanılabilir:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Mantıksal mimari

Aşağıdaki diyagramda, [IoT mimarisinde](../iot-fundamentals/iot-introduction.md)bulunan uzaktan izleme çözümü hızlandırıcısının mantıksal bileşenleri özetlenmektedir:

![Mantıksal mimari](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Mikro hizmetlerdeki neden?

Bulut mimarisi, Microsoft 'un ilk çözüm hızlandırıcılarını yayımladığından geliştirilmiştir. [Mikro hizmetler](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) , geliştirme hızından ödün vermeden ölçek ve esneklik elde etmek için kendini kanıtlamış bir uygulama olarak ortaya çıktı. Birçok Microsoft hizmeti bu mimari kalıbı harika güvenilirlik ve ölçeklenebilirlik sonuçlarıyla birlikte kullanır. Güncelleştirilmiş çözüm hızlandırıcılarını bu dersleri uygulamaya koyarak, bunlardan de yararlanabilirsiniz.

> [!TIP]
> Mikro hizmet mimarisi hakkında daha fazla bilgi için [.NET Uygulama Mimarisi](https://www.microsoft.com/net/learn/architecture) ve [Mikro hizmetler: Bulut tarafından desteklenen bir uygulama devrimi](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) konusunu inceleyin.

## <a name="device-connectivity"></a>Cihaz bağlantısı

Çözüm, mantıksal mimarinin cihaz bağlantısı bölümünde aşağıdaki bileşenleri içerir:

### <a name="real-devices"></a>Gerçek cihazlar

Gerçek cihazları çözüme bağlayabilirsiniz. Azure IoT cihaz SDK 'larını kullanarak sanal cihazlarınızın davranışını uygulayabilirsiniz.

Çözüm portalındaki panodan gerçek aygıtları sağlayabilirsiniz.

### <a name="device-simulation-microservice"></a>Cihaz benzetimi mikro hizmet

Çözüm, çözümdeki uçtan uca akışı test etmek üzere çözüm portalından bir sanal cihaz havuzunu yönetmenizi sağlayan [cihaz benzetimi mikro hizmetini](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) içerir. Sanal cihazlar:

* Cihazdan buluta telemetri oluşturma.
* IoT Hub 'ten gelen buluttan cihaza yöntem çağrılarına yanıt verin.

Mikro hizmet, simülasyonları oluşturmanız, başlatmanız ve durdurmanız için sizin için yeniden bir uç nokta sağlar. Her simülasyon, farklı türlerdeki bir sanal cihaz kümesinden oluşur. Bu, telemetri gönderir ve yöntem çağrılarına yanıt verir.

Çözüm portalındaki panodan sanal cihazlar sağlayabilirsiniz.

### <a name="iot-hub"></a>IoT Hub’ı

Hem gerçek hem de sanal cihazlardan buluta gönderilen [IoT Hub 'ı](../iot-hub/index.yml) ıngests telemetrisi. IoT Hub 'ı, Telemetriyi işlemek için IoT çözüm arka ucunda bulunan hizmetler için kullanılabilir hale getirir.

IoT hub çözümde aynı zamanda şunları yapar:

* Portala bağlanmasına izin verilen tüm cihazların kimliklerini ve kimlik doğrulama anahtarlarını depolayan bir kimlik kayıt defteri tutar.
* Çözüm Hızlandırıcısı adına cihazlarınızda yöntemleri çağırır.
* Tüm kayıtlı cihazlar için cihaz ikizlerini tutar. Cihaz ikizi bir cihaz tarafından bildirilen özellik değerlerini depolar. Cihaz ikizi ayrıca cihaz bir kez daha bağlandığında alabilmesi için çözüm portalında ayarlanmış istenen özellikleri depolar.
* Birden fazla cihaza ait özellikleri ayarlamak veya birden fazla cihaz üzerinde yöntem çağırmak için işleri zamanlar.

## <a name="data-processing-and-analytics"></a>Veri işleme ve analizi

Çözüm, mantıksal mimarinin veri işleme ve analiz bölümünde aşağıdaki bileşenleri içerir:

### <a name="iot-hub-manager-microservice"></a>IoT Hub Manager Mikro hizmeti

Çözüm, IoT Hub 'ınız ile etkileşimleri işlemek için [IoT Hub Manager Mikro hizmetini](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) içerir; örneğin:

* IoT cihazları oluşturma ve yönetme.
* Cihaz ikizlerini yönetme.
* Cihazlarda Yöntemler çağrılıyor.
* IoT kimlik bilgilerini yönetme.

Bu hizmet ayrıca Kullanıcı tanımlı gruplara ait olan cihazları almak için IoT Hub sorguları çalıştırır.

Mikro hizmet, cihazları ve cihaz ikizlerini yönetmek, yöntemleri çağırmak ve IoT Hub sorguları çalıştırmak için yeniden bir uç nokta sağlar.

### <a name="device-telemetry-microservice"></a>Cihaz telemetrisi mikro hizmeti

[Cihaz telemetrisi mikro hizmeti](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) , Time Series Insights ' de depolanan cihaz telemetrisine okuma erişimi Için bir yeniden deneme uç noktası sağlar. Ayrıca, yeniden deneme uç noktası kurallar üzerinde CRUD işlemlerine ve depolama tanımları için okuma/yazma erişimine de izin verebilir.

### <a name="storage-adapter-microservice"></a>Depolama bağdaştırıcısı mikro hizmeti

[Depolama bağdaştırıcısı mikro hizmeti](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) , anahtar-değer çiftlerini yönetir, depolama hizmeti semantiğini soyutlayan ve Azure Cosmos DB kullanarak her biçimdeki verileri depolamak için basit bir arabirim sunuyor.

Değerler koleksiyonlar halinde düzenlenir. Tek tek değerler üzerinde çalışabilir veya tüm koleksiyonları getirebilirsiniz. Karmaşık veri yapıları istemciler tarafından serileştirilir ve basit metin yükü olarak yönetilir.

Hizmet, anahtar-değer çiftlerinde CRUD işlemleri için yeniden bir uç nokta sağlar. Deðerler

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Çözüm Hızlandırıcı dağıtımları, kuralları, uyarıları, yapılandırma ayarlarını ve diğer tüm soğuk depolamayı depolamak için [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) kullanır.

### <a name="azure-stream-analytics-manager-microservice"></a>Azure Stream Analytics Manager Mikro hizmeti

[Azure Stream Analytics Manager Mikro hizmeti](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) , yapılandırma ayarları, bunları başlatma ve durdurma ve durumlarını izleme dahil olmak üzere Azure Stream ANALYTICS (ASA) işlerini yönetir.

ASA işi iki başvuru veri kümesi tarafından desteklenir. Bir veri kümesi kuralları tanımlar ve bir cihaz grubunu tanımlar. Kurallar başvuru verileri, cihaz telemetrisi mikro hizmeti tarafından yönetilen bilgilerden oluşturulur. Azure Stream Analytics Manager Mikro hizmeti, telemetri kurallarını akış işleme mantığına dönüştürür.

Cihaz grupları başvuru verileri, gelen telemetri iletisine hangi kural grubunun uygulanacağını belirlemek için kullanılır. Cihaz grupları yapılandırma mikro hizmeti tarafından yönetilir ve Azure IoT Hub Device ikizi sorgularını kullanır.

ASA işleri, depolama ve analiz için Time Series Insights bağlı cihazlardan Telemetriyi teslim edebilir.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) , cihazlardan yüksek hacimli veri akışını incelemenize olanak tanıyan bir olay işleme motorudur.

### <a name="azure-time-series-insights"></a>Azure Zaman Serisi Görüşleri

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) , telemetrisi çözüm hızlandırıcısına bağlı cihazlardan depolar. Ayrıca çözüm Web Kullanıcı arabiriminde cihaz telemetrisini görselleştirmeyi ve sorgulamayı de mümkün kılar.

> [!NOTE]
> Time Series Insights Azure Çin bulutu 'nda Şu anda kullanılamıyor. Azure Çin bulutu 'ndaki yeni uzaktan Izleme çözümü Hızlandırıcısı dağıtımları tüm depolama için Cosmos DB kullanır.

### <a name="configuration-microservice"></a>Yapılandırma mikro hizmeti

[Yapılandırma mikro hizmeti](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) , cihaz grupları, çözüm ayarları ve çözüm hızlandırıcısında Kullanıcı ayarları için CRUD Işlemleri için yeniden bir uç nokta sağlar. Yapılandırma verilerini kalıcı hale getirmek için depolama bağdaştırıcısı mikro hizmeti ile birlikte kullanılır.

### <a name="authentication-and-authorization-microservice"></a>Kimlik doğrulama ve yetkilendirme mikro hizmeti

[Kimlik doğrulama ve yetkilendirme mikro hizmeti](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) , çözüm hızlandırıcısına erişim yetkisi olan kullanıcıları yönetir. Kullanıcı yönetimi, [OpenID Connect](https://openid.net/connect/)'i destekleyen herhangi bir kimlik hizmeti sağlayıcısı kullanılarak yapılabilir.

### <a name="azure-active-directory"></a>Azure Active Directory

Çözüm Hızlandırıcı dağıtımları bir OpenID Connect sağlayıcısı olarak [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) kullanır. Azure Active Directory, Kullanıcı bilgilerini depolar ve JWT belirteci imzalarını doğrulamak için sertifika sağlar.

## <a name="presentation"></a>Sununuzda

Çözüm, mantıksal mimarinin sunum bölümünde aşağıdaki bileşenleri içerir:

[Web Kullanıcı arabirimi, bir tepki verme JavaScript uygulamasıdır](https://github.com/Azure/pcs-remote-monitoring-webui). Uygulama:

* Yalnızca JavaScript 'e tepki kullanır ve tamamen tarayıcıda çalışır.
* CSS ile stillendirilmiş.
* AJAX çağrıları aracılığıyla herkese açık mikro hizmetlerle etkileşime girer.

Kullanıcı arabirimi tüm çözüm Hızlandırıcısı işlevlerini gösterir ve gibi diğer mikro hizmetlerle etkileşime girer:

* Kullanıcı verilerini korumak için kimlik doğrulama ve yetkilendirme mikro hizmeti.
* IoT cihazlarını listelemek ve yönetmek için IoT Hub Manager Mikro hizmeti.

Kullanıcı arabirimi, cihaz telemetrinin sorgulanmasını ve analizini etkinleştirmek için Azure Time Series Insights Gezginini tümleştirir.

Yapılandırma mikro hizmeti, Kullanıcı arabiriminin yapılandırma ayarlarını depolamasını ve almasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Kaynak kodu ve geliştirici belgelerini araştırmak isterseniz, iki GitHub depolarından biriyle başlayın:

* [Azure IoT (.net) Ile uzaktan izleme Için Çözüm Hızlandırıcısı](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Azure IoT (Java) Ile uzaktan izleme Için Çözüm Hızlandırıcısı](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Ayrıntılı çözüm mimarisi diyagramları:
* [Uzaktan izleme mimarisi Için Çözüm Hızlandırıcısı](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Uzaktan Izleme Çözüm Hızlandırıcısı hakkında daha fazla kavramsal bilgi için bkz. [çözüm hızlandırıcıyı özelleştirme](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
