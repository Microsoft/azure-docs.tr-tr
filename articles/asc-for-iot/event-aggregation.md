---
title: IoT olay toplaması için Azure Güvenlik Merkezi 'ni anlama | Microsoft Docs
description: IoT olay toplama için Azure Güvenlik Merkezi hakkında bilgi edinin.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: ca1d1a5761e62b2838a474dcb83f450987972998
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928967"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>IoT olay toplaması için Azure Güvenlik Merkezi

IoT güvenlik aracıları için Azure Güvenlik Merkezi, yerel cihazınızdan veri ve sistem olayları toplar ve bu verileri işleme ve analiz için Azure bulutuna gönderir. Güvenlik Aracısı, yeni işlem ve yeni bağlantı olayları dahil olmak üzere birçok cihaz olayı türünü toplar. Yeni işlem ve yeni bağlantı olaylarının her ikisi de bir cihazda ikinci olarak sıklıkla meydana gelebilir ve güçlü ve kapsamlı güvenlik için önemli olsa da, güvenlik aracılarının göndermeye zorlanacağı ileti sayısı, IoT Hub hızlı bir şekilde ulaşabilir veya bu işlemden çıkabilir Kota ve maliyet sınırları. Ancak, bu olaylar, cihazınızı korumak için önemli olan önemli ölçüde önemli güvenlik bilgilerini içerir.

Cihazların korunmasını sağlarken ek kotayı ve maliyetleri azaltmak için, IoT aracıları için Azure Güvenlik Merkezi bu tür olayları toplar.

Olay toplama varsayılan olarak **açıktır** , ancak önerilmese de dilediğiniz zaman el **ile kapatılabilir.**

Toplama şu anda aşağıdaki olay türleri için kullanılabilir:
* ProcessCreate
* ConnectionCreate
* ProcessTerminate (yalnızca Windows)

## <a name="how-does-event-aggregation-work"></a>Olay toplama nasıl çalışır?
Olay toplama bırakıldığında, IoT aracıları için Azure Güvenlik **Merkezi, Aralık**dönemi veya zaman penceresi için olayları toplar.
Aralık süresi geçtikten sonra, aracı, toplanan olayları daha fazla analiz için Azure bulutuna gönderir.
Toplanan olaylar, Azure bulutuna gönderilene kadar bellekte depolanır.

Aracının bellekte zaten tutulmakta olan bir olay ile aynı olayı topladığında aracının bellek parmak izini azaltmak için, aracı bu belirli olayın isabet sayısını artırır. Toplama zamanı penceresi geçtiğinde, aracı oluşan her bir olay türünün isabet sayısını gönderir. Olay toplama, yalnızca toplanan her bir olay türünün isabet sayısı toplamını verir.

Olaylar yalnızca aşağıdaki koşullar karşılandığında özdeş olarak değerlendirilir: 

* ProcessCreate olayları- **commandLine**, **çalıştırılabilir**, **Kullanıcı adı**ve Kullanıcı **kimliği** aynı olduğunda
* ConnectionCreate olayları- **commandLine**, **UserID**, **Direction**, **Yerel adres**, **uzak adres**, * * protokolü ve **hedef bağlantı noktası** aynı olduğunda
* ProcessTerminate olayları- **yürütülebilir** ve **Çıkış durumu** aynı olduğunda

### <a name="working-with-aggregated-events"></a>Toplu olaylarla çalışma

Toplama sırasında, toplanmayan olay özellikleri atılır ve Log Analytics 'te 0 değeri ile görüntülenir. 
* ProcessCreate olayları- **İşlemKimliği**ve **ParentProcessId** 0 olarak ayarlanır
* ConnectionCreate olayları- **İşlemKimliği**ve **kaynak bağlantı noktası** 0 olarak ayarlanır

## <a name="event-aggregation-based-alerts"></a>Olay toplama tabanlı uyarılar 
Analiz sonrasında IoT için Azure Güvenlik Merkezi, şüpheli toplanmış olaylar için güvenlik uyarıları oluşturur. Toplanan olaylardan oluşturulan uyarılar, toplanan her olay için yalnızca bir kez görünür.

Her olay için toplama başlangıç zamanı, bitiş zamanı ve isabet sayısı, araştırmalar sırasında kullanılmak üzere Log Analytics içindeki olay **Extradetails** alanında günlüğe kaydedilir. 

Her toplanmış olay, toplanan uyarıların 24 saatlik bir dönemini temsil eder. Her olayın sol üst kısmındaki olay seçenekleri menüsünü kullanarak, toplanan her bir olayı **kapatabilirsiniz** .    

## <a name="event-aggregation-twin-configuration"></a>Event toplamasını ikizi yapılandırması
**Azureiotsecurity** modülünün Module ikizi kimliğinin [Aracı yapılandırma nesnesi](how-to-agent-configuration.md) içindeki IoT olay toplaması için Azure Güvenlik Merkezi yapılandırmasında değişiklikler yapın.

| Yapılandırma adı | Olası değerler | Ayrıntılar | Açıklamalar |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boole | İşlem oluşturma olayları için olay toplamayı etkinleştir/devre dışı bırak |
| Aggregationınterprocesscreate | ISO8601 TimeSpan dizesi | İşlem oluşturma olayları için toplama aralığı |
| aggregationEnabledConnectionCreate | boole| Bağlantı oluşturma olayları için olay toplamayı etkinleştir/devre dışı bırak |
| Aggregationınterconnectioncreate | ISO8601 TimeSpan dizesi | Bağlantı oluşturma olayları için toplama aralığı |
| aggregationEnabledProcessTerminate | boole | İşlem sonlandırma olayları için olay toplamayı etkinleştir/devre dışı bırak | Yalnızca Windows|
| Aggregationınterınsterminate | ISO8601 TimeSpan dizesi | İşlem sonlandırma olayları için toplama aralığı | Yalnızca Windows|
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

Bu makalede IoT güvenlik Aracısı toplaması için Azure Güvenlik Merkezi ve kullanılabilir olay yapılandırma seçenekleri hakkında bilgi edindiniz.

IoT dağıtımı için Azure Güvenlik Merkezi 'ni kullanmaya devam etmek için aşağıdaki makaleleri kullanın:

- [Güvenlik Aracısı kimlik doğrulama yöntemlerini](concept-security-agent-authentication-methods.md) anlama
- [Güvenlik aracısını](how-to-deploy-agent.md) seçme ve dağıtma
- IoT [hizmeti önkoşulları](service-prerequisites.md) Için Azure Güvenlik Merkezi 'ni gözden geçirin
- [IoT Hub IoT hizmeti Için Azure Güvenlik Merkezi 'ni nasıl etkinleştirebileceğinizi](quickstart-onboard-iot-hub.md) öğrenin
- [IoT Için Azure Güvenlik Merkezi](resources-frequently-asked-questions.md) 'nden hizmet hakkında daha fazla bilgi edinin
