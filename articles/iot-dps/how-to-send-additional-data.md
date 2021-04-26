---
title: Cihaz ve Azure cihaz sağlama hizmeti arasında yük aktarma
description: Bu belgede cihaz ve cihaz sağlama hizmeti (DPS) arasında bir yükün nasıl aktarılacağı açıklanmaktadır
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: a3ee7f3fca3fff1cd401f26489b01fb9cc4e09c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259528"
---
# <a name="how-to-transfer-payloads-between-devices-and-dps"></a>Cihazlar ve DPS arasında yükleri aktarma
Bazen DPS, cihazların doğru IoT Hub doğru şekilde sağlaması için cihazlardan daha fazla veriye ihtiyaç duyuyor ve bu verilerin cihaz tarafından sağlanması gerekiyor. Bunun tersine, DPS istemci tarafı LOGI 'yi kolaylaştırmak için cihaza veri döndürebilir. 

## <a name="when-to-use-it"></a>Kullanılması gereken durumlar
Bu özellik, [özel ayırma](./how-to-use-custom-allocation-policies.md)geliştirmesi olarak kullanılabilir. Örneğin, cihazları, insan müdahalesi olmadan cihaz modeline göre ayırmak istiyorsunuz. Bu durumda, [özel tahsisi](./how-to-use-custom-allocation-policies.md)kullanacaksınız. Cihazı, [kayıt cihazı çağrısının](/rest/api/iot-dps/runtimeregistration/registerdevice)bir parçası olarak model bilgilerini bildirmek üzere yapılandırabilirsiniz. DPS, cihazın yükünü özel ayırma Web kancasına geçilecektir. Ve işleviniz, cihaz modeli bilgilerini aldığında bu cihazın hangi IoT Hub gideceğine karar verebilir. Benzer şekilde, Web kancası cihaza bazı veriler döndürmesini istiyorsa, verileri Web kancası yanıtında bir dize olarak geri iletir.  

## <a name="device-sends-data-payload-to-dps"></a>Cihaz, veri yükünü DPS 'e gönderir
Cihazınız, DPS 'ye bir [yazmaç cihaz çağrısı](/rest/api/iot-dps/runtimeregistration/registerdevice) gönderirken, kayıt çağrısı, gövdedeki diğer alanları alacak şekilde geliştirilebilir. Gövde aşağıdaki gibi görünür: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS cihaza veri döndürüyor
Özel ayırma ilkesi Web kancası cihaza bazı veriler döndürmesini istiyorsa, verileri Web kancası yanıtında bir dize olarak geri iletir. Değişiklik aşağıdaki yük bölümünde bulunur. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK desteği
Bu özellik C, C#, JAVA ve Node.js [Istemci SDK](./index.yml)'lerinde kullanılabilir.  

## <a name="next-steps"></a>Sonraki adımlar
* Azure IoT Hub ve Azure IoT Hub cihaz sağlama hizmeti için [Azure ıOT SDK 'sını]( https://github.com/Azure/azure-iot-sdks) kullanarak geliştirme