---
title: Aşağı akış cihazları için ağ geçitleri-Azure IoT Edge | Microsoft Docs
description: Birden çok aşağı akış aygıtından buluta veri gönderen veya yerel olarak işleyen bir saydam, donuk veya ara sunucu ağ geçidi cihazı oluşturmak için Azure IoT Edge kullanın.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/23/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: aa8b7372af91fc7cb194dfc3a6212cb4ce1fa0a2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027355"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>IoT Edge cihazını ağ geçidi olarak kullanma

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge cihazlar ağ geçitleri olarak çalışabilir ve ağdaki diğer aygıtlar arasında bir bağlantı sağlar IoT Hub.

IoT Edge hub modülü IoT Hub gibi davranır, bu nedenle aynı IoT Hub 'ına sahip bir kimliğe sahip diğer cihazlardan gelen bağlantıları işleyebilir. Bu tür ağ geçidi deseninin nedeni, iletiler arasında bir ağ geçidi olmamasına rağmen iletiler aşağı akış aygıtlarından IoT Hub 'e geçebildiğinden *saydam* olarak adlandırılır.

IoT Hub, kendilerine ait olmayan veya bağlanmadıkları cihazlarda, IoT Edge ağ geçitleri bu bağlantıyı verebilir. IoT Edge cihazın, IoT Hub iletilmek üzere gelen aşağı akış cihaz iletilerinde işlem yapması gerektiğinden, bu tür ağ geçidi düzenine *çeviri* denir. Bu senaryolar, işleme adımlarını işlemek için IoT Edge ağ geçidinde ek modüller gerektirir.

Saydam ve çeviri ağ geçidi desenleri birbirini dışlamalı değildir. Tek bir IoT Edge cihaz hem saydam bir ağ geçidi hem de çeviri ağ geçidi olarak çalışabilir.

Tüm ağ geçidi desenleri aşağıdaki avantajları sağlar:

* **Kenarda analiz** : buluta tam uygunlukta telemetri göndermeden aşağı akış cihazlarından gelen verileri işlemek için yerel olarak AI hizmetlerini kullanın. Öngörüleri yerel olarak bulun ve bunlara yanıt verin ve yalnızca IoT Hub bir veri alt kümesini gönderin.
* **Aşağı akış cihaz yalıtımı** – ağ geçidi cihazı, tüm aşağı akış cihazlarının internet üzerinden etkilenmasını sağlayabilir. Bağlantısı olmayan bir işlemsel teknoloji (OT) ağı ile Web 'e erişim sağlayan bir bilgi teknolojisi (BT) ağı arasında yer alabilir. Benzer şekilde, kendi başına IoT Hub bağlanma yeteneğine sahip olmayan cihazlar bunun yerine bir ağ geçidi cihazına bağlanabilir.
* **Bağlantı çoğullama** -IoT Edge bir ağ geçidi üzerinden IoT Hub bağlanan tüm cihazlar aynı temel bağlantıyı kullanabilir. Bu çoğullama özelliği, IoT Edge ağ geçidinin, kendi yukarı akış protokolü olarak AMQP kullanmasını gerektirir.
* **Trafik yumuşatma** -IoT Edge cihaz, trafiği yerel olarak kalıcı hale getiren IoT Hub trafiği kısıtlıyorsa üstel geri alma otomatik olarak uygulanır. Bu avantaj, çözümünüzü trafikte ani artışlar açısından dayanıklı hale getirir.
* **Çevrimdışı destek** -ağ geçidi cihazı, IoT Hub teslim edilmemiş iletileri ve ikizi güncelleştirmelerini depolar.

## <a name="transparent-gateways"></a>Saydam ağ geçitleri

Saydam ağ geçidi modelinde, teorik olarak IoT Hub bağlantı kurmak için bir ağ geçidi cihazına bağlanabilir cihazlar olabilir. Aşağı akış cihazlarının kendi IoT Hub kimlikleri vardır ve MQTT ya da AMQP protokollerini kullanarak bağlanır. Ağ geçidi basitçe cihazlarla IoT Hub arasındaki iletişimi geçirir. Her iki cihaz ve IoT Hub aracılığıyla onlarla etkileşim kuran kullanıcılar, bir ağ geçidinin iletişimlerini ortalamalarını farkında değildir. Bu tanıma olmaması, ağ geçidinin *saydam* olarak kabul edildiği anlamına gelir.

IoT Edge hub 'ının aşağı akış cihazları ve bulutu arasındaki iletişimi nasıl yönettiği hakkında daha fazla bilgi için, bkz. [Azure IoT Edge çalışma zamanını ve mimarisini anlayın](iot-edge-runtime.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"
![Diyagram-saydam ağ geçidi kalıbı](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

>[!NOTE]
>IoT Edge sürüm 1,1 ve daha eski sürümlerde IoT Edge cihazlar IoT Edge bir ağ geçidinin bir aşağı akış olamaz.
>
>IoT Edge sürüm 1,2 ' den başlayarak, saydam ağ geçitleri, yukarı akış IoT Edge cihazlarındaki bağlantıları işleyebilir. Daha fazla bilgi için bu makalenin [IoT Edge 1,2](?view=iotedge-2020-11&preserve-view=true) sürümüne geçin.

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge sürüm 1,2 ' den başlayarak, saydam ağ geçitleri, yukarı akış IoT Edge cihazlarındaki bağlantıları işleyebilir.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>Üst ve alt ilişkileri

IoT Edge ağ geçidini, kendisine bağlanan bir aşağı akış cihazı *alt* *öğesi* olarak ayarlayarak IoT Hub saydam ağ geçidi ilişkileri bildirirsiniz.

Üst/alt öğe ilişkisi, ağ geçidi yapılandırmasında üç noktada oluşturulmuştur:

#### <a name="cloud-identities"></a>Bulut kimlikleri

Saydam bir ağ geçidi senaryosunda tüm cihazların IoT Hub kimlik doğrulaması yapabilmesi için bulut kimliklerine ihtiyacı vardır. Bir cihaz kimliği oluşturduğunuzda veya güncelleştirdiğinizde, cihazın üst veya alt cihazlarını ayarlayabilirsiniz. Bu yapılandırma üst ağ geçidi cihazını, alt cihazlarının kimlik doğrulamasını işleyecek şekilde yetkilendirir.

>[!NOTE]
>Simetrik anahtar kimlik doğrulaması kullanan aşağı akış cihazları için isteğe bağlı bir adım olarak kullanılan IoT Hub üst cihazı ayarlama. Ancak, 1.1.0 sürümünden itibaren, her bir aşağı akış cihazının bir üst cihaza atanması gerekir.
>
>**AuthenticationMode** ortam değişkenini **cloudandscope** değerine ayarlayarak, IoT Edge hub 'ını önceki davranışa geri dönmek için yapılandırabilirsiniz.

Alt cihazların yalnızca bir üst öğesi olabilir. Her bir üst öğe 100 adede kadar alt öğeye sahip olabilir.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge cihazların her ikisi de saydam ağ geçidi ilişkilerinde hem üst hem de alt olabilir. Birbirini raporlayan birden çok IoT Edge cihazın hiyerarşisi oluşturulabilir. Bir ağ geçidi hiyerarşisinin en üst düğümü beş adede kadar alt öğeye sahip olabilir. Örneğin, bir IoT Edge cihaz, altında alt öğe olarak bağlanmış beş IoT Edge cihaz katmanına sahip olabilir. Ancak beşinci nesde IoT Edge cihazının alt öğesi olamaz, IoT Edge veya başka türlü olabilir.
::: moniker-end

#### <a name="gateway-discovery"></a>Ağ Geçidi bulma

Bir alt cihazın, ana cihazını yerel ağda bulması gerekir. Ağ Geçidi cihazlarını, alt cihazlarının konumunu bulmak için kullanacağı tam etki alanı adı (FQDN) veya IP adresi olan bir **ana bilgisayar** adıyla yapılandırın.

Aşağı akış IoT cihazlarında, bağlantı dizesindeki **Gatewayhostname** parametresini kullanarak üst cihazı işaret edin.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Aşağı akış IoT Edge cihazlarda, üst cihazı işaret etmek için yapılandırma dosyasındaki **parent_hostname** parametresini kullanın.
::: moniker-end

#### <a name="secure-connection"></a>Güvenli bağlantı

Üst ve alt cihazların birbirleriyle bağlantılarının doğrulanması da gerekir. Her cihazın, alt cihazların doğru ağ geçidine bağlandıklarından emin olmak için kullandığı paylaşılan kök CA sertifikasının bir kopyasına ihtiyacı vardır.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Birden çok IoT Edge ağ geçidi, bir ağ geçidi hiyerarşisinde birbirlerine bağlandığında, hiyerarşideki tüm cihazların tek bir sertifika zinciri kullanması gerekir.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>Saydam ağ geçitlerinin arkasındaki cihaz özellikleri

IoT Edge mesajlaşma ardışık düzeninde çalışan tüm IoT Hub temelleri de saydam ağ geçidi senaryolarını destekler. Her IoT Edge ağ geçidinde, üzerinden gelen iletiler için mağaza ve iletme özellikleri vardır.

Ağ geçitlerinin arkasındaki cihazlara kıyasla farklı IoT Hub yeteneklerinin nasıl desteklendiğini görmek için aşağıdaki tabloyu kullanın.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

| Özellik | IoT cihazı | Bir ağ geçidinin arkasındaki IoT |
| ---------- | ---------- | -------------------- |
| [Cihazdan buluta (D2C) iletileri](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Evet-IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-alt IoT D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Buluttan cihaza (C2D) iletiler](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Evet-IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-IoT alt C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [Doğrudan yöntemler](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Yes-IoT Direct yöntemi](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-alt IoT doğrudan yöntemi](./media/iot-edge-as-gateway/check-yes.png) |
| [Cihaz](../iot-hub/iot-hub-devguide-device-twins.md) ikikesi ve [Modül TWINS](../iot-hub/iot-hub-devguide-module-twins.md) | ![Evet-IoT TWINS](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-alt IoT TWINS](./media/iot-edge-as-gateway/check-yes.png) |
| [Karşıya dosya yükleme](../iot-hub/iot-hub-devguide-file-upload.md) | ![Evet-IoT dosya yükleme](./media/iot-edge-as-gateway/check-yes.png) | ![IoT alt dosya yükleme](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| Özellik | IoT cihazı | Bir ağ geçidinin arkasındaki IoT | IoT Edge cihazı | Bir ağ geçidinin arkasında IoT Edge |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [Cihazdan buluta (D2C) iletileri](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Evet-IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-alt IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-alt IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Buluttan cihaza (C2D) iletiler](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Evet-IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-IoT alt C2D](./media/iot-edge-as-gateway/check-yes.png) | ![No-IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![IoT Edge alt C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [Doğrudan yöntemler](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Yes-IoT Direct yöntemi](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-alt IoT doğrudan yöntemi](./media/iot-edge-as-gateway/check-yes.png) | ![Yes-IoT Edge doğrudan yöntemi](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-alt IoT Edge Direct yöntemi](./media/iot-edge-as-gateway/check-yes.png) |
| [Cihaz](../iot-hub/iot-hub-devguide-device-twins.md) ikikesi ve [Modül TWINS](../iot-hub/iot-hub-devguide-module-twins.md) | ![Evet-IoT TWINS](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-alt IoT TWINS](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-IoT Edge TWINS](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-alt IoT Edge TWINS](./media/iot-edge-as-gateway/check-yes.png) |
| [Karşıya dosya yükleme](../iot-hub/iot-hub-devguide-file-upload.md) | ![Evet-IoT dosya yükleme](./media/iot-edge-as-gateway/check-yes.png) | ![IoT alt dosya yükleme](./media/iot-edge-as-gateway/crossout-no.png) | ![IoT Edge dosya yükleme yok](./media/iot-edge-as-gateway/crossout-no.png) | ![IoT Edge alt dosya yükleme yok](./media/iot-edge-as-gateway/crossout-no.png) |
| Kapsayıcı görüntüsü çeker |   |   | ![Evet-IoT Edge kapsayıcı çekme](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-alt IoT Edge kapsayıcı çekme](./media/iot-edge-as-gateway/check-yes.png) |
| Blob karşıya yükleme |   |   | ![Evet-IoT Edge blob karşıya yükleme](./media/iot-edge-as-gateway/check-yes.png) | ![Evet-alt IoT Edge blob karşıya yükleme](./media/iot-edge-as-gateway/check-yes.png) |

**Kapsayıcı görüntüleri** indirilebilir, depolanabilir ve üst cihazlardan alt cihazlara teslim edilebilir.

Destek paketleri ve Günlükler de dahil olmak üzere **Bloblar** alt cihazlardan üst cihazlara yüklenebilir.

::: moniker-end

## <a name="translation-gateways"></a>Çeviri ağ geçitleri

Aşağı akış Cihazları IoT Hub bağlanamıyorsa, IoT Edge ağ geçidinin bir çevirici görevi yapması gerekir. Bu model genellikle MQTT, AMQP veya HTTP desteği olmayan cihazlar için gereklidir. Bu cihazlar IoT Hub bağlanamazlar, Ayrıca, bazı ön işlemler olmadan IoT Edge hub modülüne bağlanamaz.

Genellikle aşağı akış cihazının donanımına veya protokolüne özgü olan özel veya üçüncü taraf modüllerin IoT Edge ağ geçidine dağıtılması gerekir. Bu çeviri modülleri gelen iletileri alır ve IoT Hub tarafından anlaşılabilecek biçime dönüştürür.

Çeviri ağ geçitleri için iki desen vardır: *protokol çevirisi* ve *kimlik çevirisi*.

![Diyagram-çeviri ağ geçidi desenleri](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>Protokol çevirisi

Protokol çevirisi ağ geçidi modelinde, yalnızca IoT Edge ağ geçidinin IoT Hub bir kimliği vardır. Çeviri modülü aşağı akış cihazlarından iletileri alır, bunları desteklenen bir protokole çevirir ve sonra IoT Edge cihaz iletileri aşağı akış cihazları adına gönderir. Tüm bilgiler bir cihazdan, ağ geçidinden geliyor gibi görünüyor. Bulut uygulamaları verileri cihaz başına analiz etmek istiyorsa, aşağı akış cihazları ek tanımlama bilgilerini iletilerine eklemelidir. Ayrıca, TWINS ve doğrudan Yöntemler gibi IoT Hub temel elemanlar yalnızca, akış aşağı cihazlarda değil, ağ geçidi cihazı için desteklenir. Bu düzendeki ağ geçitleri, aşağı akış cihazlarının kimliklerini gizlediklerinden saydam ağ geçitlerinin aksine *donuk* olarak değerlendirilir.

Protokol çevirisi, kaynak kısıtlanmış olan cihazları destekler. Birçok mevcut cihaz, iş içgörülerini destekleyen verileri üretir; Ancak bulut bağlantısı göz önünde bulundurularak tasarlanmamıştır. Donuk ağ geçitleri, bu verilerin bir IoT çözümünde kilidinin açık ve kullanılabilir olmasını sağlar.

### <a name="identity-translation"></a>Kimlik çevirisi

Kimlik çevirisi ağ geçidi, protokol çevirisi üzerinde yapılar, ancak IoT Edge ağ geçidi, aşağı akış cihazları adına bir IoT Hub cihaz kimliği de sağlar. Çeviri modülü, aşağı akış cihazları tarafından kullanılan protokolü anlamak, bunları kimlik sağlamak ve iletilerini IoT Hub temel özelliklerine çevirmekten sorumludur. Aşağı akış cihazları IoT Hub’da ikizler ve yöntemlerle birinci sınıf cihazlar olarak görüntülenir. Kullanıcı IoT Hub’da cihazlarla etkileşim kurabilir ve aradaki ağ geçidi cihazının farkında olmaz.

Kimlik çevirisi, protokol çevirisi avantajlarından yararlanır ve ek olarak, akış yönündeki cihazların buluttan tam yönetilebilirlik sağlar. IoT çözümünüzdeki tüm cihazlar, kullandıkları Protokolden bağımsız olarak IoT Hub gösterir.

### <a name="device-capabilities-behind-translation-gateways"></a>Çeviri ağ geçitlerinin arkasındaki cihaz özellikleri

Aşağıdaki tabloda, IoT Hub özelliklerinin her iki çeviri ağ geçidi desenindeki aşağı akış cihazlarına nasıl genişletileneceği açıklanmaktadır.

| Özellik | Protokol çevirisi | Kimlik çevirisi |
| ---------- | -------------------- | -------------------- |
| IoT Hub Identity kayıt defterinde depolanan kimlikler | Yalnızca ağ geçidi cihazının kimliği | Tüm bağlı cihazların kimlikleri |
| Cihaz çifti | Yalnızca ağ geçidinde bir cihaz ve modül TWINS vardır | Her bağlı cihazın kendi cihazı ikizi vardır |
| Doğrudan Yöntemler ve buluttan cihaza iletiler | Bulut yalnızca ağ geçidi cihazını adresedebilir | Bulut her bağlı cihazı ayrı ayrı ele alabilir |
| [IoT Hub kısıtlar ve kotaları](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Ağ Geçidi cihazına Uygula | Her cihaza Uygula |

Protokol çevirisi modelini kullanırken, bu ağ geçidi üzerinden bağlanan tüm cihazlar, en fazla 50 ileti içerebilen aynı buluttan cihaza kuyruğu paylaşır. Bu stili yalnızca birkaç cihaz her bir alan ağ geçidi üzerinden bağlandığında kullanın ve bunların buluttan cihaza trafiği düşüktür.

IoT Edge çalışma zamanı protokol veya kimlik çevirisi özellikleri içermez. Bu desenler, genellikle kullanılan donanım ve protokole özgü özel veya üçüncü taraf modüller gerektirir. [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) , aralarından seçim yapabileceğiniz çeşitli protokol çeviri modüllerini içerir. Kimlik çevirisi modelini kullanan bir örnek için bkz. [Azure IoT Edge LoRaWAN Starter Kit](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="next-steps"></a>Sonraki adımlar

Saydam bir ağ geçidi ayarlamaya yönelik üç adımı öğrenin:

* [IoT Edge cihazını saydam ağ geçidi olarak davranacak şekilde yapılandırma](how-to-create-transparent-gateway.md)
* [Azure IoT Hub’da bir aşağı akış cihazının kimliğini doğrulama](how-to-authenticate-downstream-device.md)
* [Aşağı akış cihazını Azure IoT Edge ağ geçidine bağlama](how-to-connect-downstream-device.md)
