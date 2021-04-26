---
title: Microsoft OPC yayımcısını yapılandırma
description: Bu öğreticide, OPC yayımcısını tek başına modda yapılandırmayı öğreneceksiniz.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4d4f9c90fd96365216480164f29f08fad92eb9d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787884"
---
# <a name="tutorial-configure-the-opc-publisher"></a>Öğretici: OPC yayımcısını yapılandırma

Bu öğreticide OPC yayımcısının yapılandırması hakkında bilgiler yer alır. Yapılandırmak için birkaç arabirim kullanılabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * OPC yayımcısını yapılandırma dosyası aracılığıyla yapılandırma
> * OPC yayımcısını komut satırı bağımsız değişkenleri aracılığıyla yapılandırma
> * OPC yayımcısını IoT Hub doğrudan yöntemler aracılığıyla yapılandırma
> * Bulut tabanlı, yardımcı REST mikro hizmeti aracılığıyla OPC yayımcısını yapılandırma

## <a name="configuring-security"></a>Güvenliği yapılandırma

IoT Edge, OPC yayımcısı 'nı IoT Hub otomatik olarak erişmek için güvenlik yapılandırması ile birlikte sağlar. OPC yayımcısı, komut satırı parametresi aracılığıyla IoT Hub erişmek için bir cihaz bağlantı dizesi belirterek tek başına Docker kapsayıcısı olarak da çalıştırılabilir `dc` . IoT Hub bir cihaz oluşturulabilir ve bağlantı dizesi Azure portal üzerinden alınabilir.

OPC UA etkin varlıklara erişmek için, X. 509.440 sertifikaları ve bunlarla ilişkili özel anahtarlar OPC UA tarafından kullanılır. Bu, OPC UA uygulama kimlik doğrulaması ve OPC UA Kullanıcı kimlik doğrulamasına ek olarak adlandırılır. OPC yayımcısı tüm uygulama sertifikalarını yönetmek için dosya sistemi tabanlı bir sertifika deposu kullanır. Başlangıç sırasında OPC yayımcısı, bu sertifika depolarında kullanabileceği bir sertifika olup olmadığını denetler ve hiçbiri yoksa, yeni bir otomatik olarak imzalanan sertifika ve yeni ilişkili özel anahtar oluşturur. Otomatik olarak imzalanan sertifikalar, güvenilir bir sertifika yetkilisi tarafından imzalanmadığından zayıf kimlik doğrulaması sağlar, ancak en azından OPC UA etkin varlığına yönelik iletişim bu şekilde şifrelenebilir.

Güvenlik, bayrağı aracılığıyla yapılandırma dosyasında etkinleştirilir `"UseSecurity": true,` . OPC yayımcısının bağlanması gereken OPC UA sunucularında bulunan en güvenli uç nokta otomatik olarak seçilir.
Varsayılan olarak, OPC yayımcısı anonim kullanıcı kimlik doğrulaması kullanır (yukarıda açıklanan uygulama kimlik doğrulamasına ek olarak). Ancak OPC yayımcısı Kullanıcı kimliğini Kullanıcı adı ve parola kullanarak da destekler. Bu, REST API yapılandırma arabirimi (aşağıda açıklanmıştır) veya yapılandırma dosyası aracılığıyla aşağıdaki şekilde belirtilebilir:
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
Ayrıca, OPC yayımcısı sürüm 2,5 ve aşağıdaki yapılandırma dosyasındaki kullanıcı adını ve parolayı şifreler. Sürüm 2,6 ve üzeri yalnızca düz metin olarak Kullanıcı adı ve parolayı destekler. Bu, OPC yayımcısının bir sonraki sürümünde geliştirildi.

OPC yayımcısının güvenlik yapılandırmasını yeniden başlatmalar arasında kalıcı hale getirmek için, sertifika deposu dizininde bulunan sertifika ve özel anahtar IoT Edge ana bilgisayar işletim sistemi FileSystem ile eşlenmelidir. Yukarıdaki "Azure portal kapsayıcı oluşturma seçeneklerini belirleme" bölümüne bakın.

## <a name="configuration-via-configuration-file"></a>Yapılandırma dosyası aracılığıyla yapılandırma

OPC yayımcısını yapılandırmanın en basit yolu bir yapılandırma dosyası aracılığıyla yapılır. Örnek bir yapılandırma dosyası ve biçimiyle ilgili belgeler bu depodaki dosya aracılığıyla sağlanır [`publishednodes.json`](https://raw.githubusercontent.com/Azure/iot-edge-opc-publisher/master/opcpublisher/publishednodes.json) .
Yapılandırma dosyası söz dizimi zaman içinde değiştirilmiştir ve OPC yayımcısı hala eski biçimleri okuyabilir, ancak yapılandırmayı kalıcı hale geldiğinde otomatik olarak düzenli bir biçimde yapılır.

Temel bir yapılandırma dosyası şöyle görünür:
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

OPC UA varlıkları, veriler değiştiğinde OPC yayımcısına yalnızca veri değişiklikleri göndererek ağ bant genişliğini iyileştirir. Veri değişikliklerinin daha sık veya düzenli aralıklarla yayımlanması gerekiyorsa OPC yayımcısı, veri öğesinin yapılandırmasında HeartbeatInterval anahtarını belirterek, yapılandırılmış her bir veri öğesi için bir "sinyal" destekler. Aralık saniye cinsinden belirtilir:
```
 "HeartbeatInterval": 3600,
```

OPC UA varlığı, OPC yayımcısı ilk kez bağlandığında, her zaman bir veri öğesinin geçerli değerini gönderir. Bu verilerin IoT Hub yayımlanmasını engellemek için, veri öğesinin yapılandırmasında SkipFirst anahtarı da belirtilebilir:
```
 "SkipFirst": true,
```

Her iki ayar de komut satırı seçenekleri ile genel olarak etkinleştirilebilir.

## <a name="configuration-via-command-line-arguments"></a>Komut satırı bağımsız değişkenleri aracılığıyla yapılandırma

OPC yayımcısı için genel ayarları ayarlamak üzere kullanılabilecek birkaç komut satırı bağımsız değişkeni vardır. [Burada](reference-command-line-arguments.md)açıklanırlar.


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>Yerleşik OPC UA sunucu arabirimi aracılığıyla yapılandırma

>[!NOTE] 
> Bu özellik yalnızca sürüm 2,5 ve OPC yayımcısının altında kullanılabilir. * *

OPC yayımcısının, 62222 numaralı bağlantı noktasında çalışan yerleşik bir OPC UA sunucusu vardır. Üç OPC UA yöntemi uygular:

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

Bu arabirime, bir OPC UA istemci uygulaması (örneğin, [UA uzmanı](https://www.unified-automation.com/products/development-tools/uaexpert.html)) kullanılarak erişilebilir.

## <a name="configuration-via-iot-hub-direct-methods"></a>IoT Hub doğrudan yöntemler aracılığıyla yapılandırma

>[!NOTE] 
> Bu özellik yalnızca sürüm 2,5 ve OPC yayımcısının altında kullanılabilir. * *

OPC yayımcısı, [IoT Hub cihaz SDK 'sını](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)kullanarak bir uygulamadan (dünyanın herhangi bir yerinden) çağrılabilen aşağıdaki [IoT Hub doğrudan yöntemlerini](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods)uygular:

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - Getdiagnosticınfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - Exkurpplication
  - GetInfo

Bu arabirimden yararlanarak OPC yayımcısı açık kaynaklı [Tanılama bilgilerini okumak için bir uygulama](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) ve [örnek yapılandırma uygulaması](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) sağladık.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>Bulut tabanlı, yardımcı REST mikro hizmeti aracılığıyla yapılandırma

>[!NOTE] 
> Bu özellik yalnızca, OPC yayımcısının sürüm 2,6 ve üzeri sürümlerde kullanılabilir.

REST arabirimine sahip bulut tabanlı, yardımcı mikro hizmet [burada](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md)açıklanmış ve kullanılabilir. Bu, bir Openapı ile uyumlu bir arabirim aracılığıyla (örneğin Swagger aracılığıyla) OPC yayımcısı yapılandırmak için kullanılabilir.

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>Ayrı yapılandırma dosyası aracılığıyla basit JSON telemetri biçiminin yapılandırması

>[!NOTE] 
> Bu özellik yalnızca sürüm 2,5 ve OPC yayımcısının altında kullanılabilir.

OPC yayımcısı, standart olmayan ve basit telemetri biçiminin bölümlerinin, TC komut satırı seçeneğiyle belirtilemeyen ayrı bir yapılandırma dosyası aracılığıyla filtrelemesine olanak tanır. Hiçbir yapılandırma dosyası belirtilmemişse, tam JSON telemetri biçimi IoT Hub gönderilir. Ayrı telemetri yapılandırma dosyasının biçimi [burada](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format)açıklanmıştır.

## <a name="next-steps"></a>Sonraki adımlar
OPC yayımcısını yapılandırdığınıza göre, sonraki adım Edge modülünün performansını ve belleğini ayarlamayı öğrenmektedir:

> [!div class="nextstepaction"]
> [Performans ve bellek ayarlama](tutorial-publisher-performance-memory-tuning-opc-publisher.md)