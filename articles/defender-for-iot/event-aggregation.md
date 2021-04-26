---
title: Defender-IoT-Micro-Agent klasik olay toplama
description: IoT olay toplama için Defender hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 3/23/2021
ms.openlocfilehash: e6c0f0e6e1f4027716ff8e3ca99f1c8803f33260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779146"
---
# <a name="defender-iot-micro-agent-classic-event-aggregation"></a>Defender-IoT-Micro-Agent klasik olay toplama

IoT güvenlik aracıları için Defender, yerel cihazınızdan veri ve sistem olayları toplar ve bu verileri işleme ve analiz için Azure bulutuna gönderir. Güvenlik Aracısı, yeni işlem ve yeni bağlantı olayları dahil olmak üzere birçok cihaz olayı türünü toplar. Yeni işlem ve yeni bağlantı olaylarının her ikisi de bir cihazda ikinci olarak sıklıkla meydana gelebilir ve güçlü ve kapsamlı güvenlik için önemli olsa da, güvenlik aracılarının göndermeye zorlanacağı ileti sayısı, IoT Hub kotasına ve maliyet sınırlarına hızlı bir şekilde ulaşabilir veya bu sınıra ulaşabilirler. Ancak, bu olaylar, cihazınızı korumak için önemli olan önemli ölçüde önemli güvenlik bilgilerini içerir.

Daha fazla kotayı ve cihazlarınızı koruma altına alırken, IoT aracıları için Defender bu olay türlerini toplar.

Olay toplama varsayılan olarak **açıktır** , ancak önerilmese de dilediğiniz zaman el **ile kapatılabilir.**

Toplama şu anda aşağıdaki olay türleri için kullanılabilir:

* ProcessCreate
* ConnectionCreate
* ProcessTerminate (yalnızca Windows)

## <a name="how-does-event-aggregation-work"></a>Olay toplama nasıl çalışır?

Olay toplama bırakıldığında, IoT aracıları için **Defender, Aralık** dönemi veya zaman penceresi için olayları toplar.
Aralık süresi geçtikten sonra, aracı, toplanan olayları daha fazla analiz için Azure bulutuna gönderir.
Toplanan olaylar, Azure bulutuna gönderilene kadar bellekte depolanır.

Aracının bellekte zaten tutulmakta olan bir olay ile aynı olayı topladığında aracının bellek parmak izini azaltmak için, aracı bu belirli olayın isabet sayısını artırır. Toplama zamanı penceresi geçtiğinde, aracı oluşan her bir olay türünün isabet sayısını gönderir. Olay toplama, yalnızca toplanan her bir olay türünün isabet sayısı toplamını verir.

Olaylar yalnızca aşağıdaki koşullar karşılandığında özdeş olarak değerlendirilir:

* ProcessCreate olayları- **commandLine**, **çalıştırılabilir**, **Kullanıcı adı** ve Kullanıcı **kimliği** aynı olduğunda
* ConnectionCreate olayları- **commandLine**, **UserID**, **Direction**, **Yerel adres**, **uzak adres**, **protokol** ve **hedef bağlantı noktası** aynı olduğunda.
* ProcessTerminate olayları- **yürütülebilir** ve **Çıkış durumu** aynı olduğunda

### <a name="working-with-aggregated-events"></a>Toplu olaylarla çalışma

Toplama sırasında, toplanmayan olay özellikleri atılır ve Log Analytics 'te 0 değeri ile görüntülenir.

* ProcessCreate olayları- **İşlemKimliği** ve **ParentProcessId** 0 olarak ayarlanır
* ConnectionCreate olayları- **İşlemKimliği** ve **kaynak bağlantı noktası** 0 olarak ayarlanır

## <a name="event-aggregation-based-alerts"></a>Olay toplama tabanlı uyarılar

Analiz sonrasında, IoT için Defender şüpheli toplanmış olaylar için güvenlik uyarıları oluşturur. Toplanan olaylardan oluşturulan uyarılar, toplanan her olay için yalnızca bir kez görünür.

Her olay için toplama başlangıç zamanı, bitiş zamanı ve isabet sayısı, araştırmalar sırasında kullanılmak üzere Log Analytics içindeki olay **Extradetails** alanında günlüğe kaydedilir.

Her toplanmış olay, toplanan uyarıların 24 saatlik bir dönemini temsil eder. Her olayın sol üst kısmındaki olay seçenekleri menüsünü kullanarak, toplanan her bir olayı **kapatabilirsiniz** .

## <a name="event-aggregation-twin-configuration"></a>Event toplamasını ikizi yapılandırması

**Azureiotsecurity** modülünün Module ikizi kimliğinin [Aracı yapılandırma nesnesi](how-to-agent-configuration.md) içindeki IoT olay toplama için Defender yapılandırmasında değişiklikler yapın.

| Yapılandırma adı | Olası değerler | Ayrıntılar | Açıklamalar |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | İşlem oluşturma olayları için olay toplamayı etkinleştir/devre dışı bırak |
| Aggregationınterprocesscreate | ISO8601 TimeSpan dizesi | İşlem için toplama aralığı olaylar oluşturuyor |
| aggregationEnabledConnectionCreate | boolean| Bağlantı oluşturma olayları için olay toplamayı etkinleştir/devre dışı bırak |
| Aggregationınterconnectioncreate | ISO8601 TimeSpan dizesi | Bağlantı için toplama aralığı olaylar oluşturuyor |
| aggregationEnabledProcessTerminate | boolean | İşlem sonlandırma olayları için olay toplamayı etkinleştir/devre dışı bırak | Yalnızca Windows|
| Aggregationınterınsterminate | ISO8601 TimeSpan dizesi | İşlem sonlandırıldığında olayları toplama aralığı | Yalnızca Windows|
|

## <a name="default-configurations-settings"></a>Varsayılan yapılandırma ayarları

| Yapılandırma adı | Varsayılan değerler |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| Aggregationınterprocesscreate | PT1H|
| aggregationEnabledConnectionCreate | true |
| Aggregationınterconnectioncreate | PT1H|
| aggregationEnabledProcessTerminate | true |
| Aggregationınterınsterminate | PT1H|
|

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, IoT güvenlik Aracısı toplaması için Defender ve kullanılabilir olay yapılandırma seçenekleri hakkında bilgi edindiniz.

IoT dağıtımı için Defender 'ı kullanmaya devam etmek için aşağıdaki makaleleri kullanın:

- [Güvenlik Aracısı kimlik doğrulama yöntemlerini](concept-security-agent-authentication-methods.md) anlama
- [Güvenlik aracısını](how-to-deploy-agent.md) seçme ve dağıtma
- IoT [Sistem önkoşulları](quickstart-system-prerequisites.md) Için Defender 'ı gözden geçirin
- [IoT Hub Için Defender 'ı nasıl etkinleştirebileceğinizi](quickstart-onboard-iot-hub.md) öğrenin
- [IoT Için Defender](resources-frequently-asked-questions.md) aracılığıyla hizmet hakkında daha fazla bilgi edinin
