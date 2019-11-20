---
title: Cihaz ve Azure cihaz sağlama hizmeti arasında ek veri aktarma
description: Bu belgede, cihaz ve DPS arasında ek verilerin nasıl aktarılacağı açıklanmaktadır
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 90402dd6fbe19811b5bb6d5ac0fbdd984b71fd33
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123174"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>Cihaz ve DPS arasında ek veri aktarma
Bazen DPS’nin cihazı doğru IoT Hub’ına düzgün bir şekilde sağlamak için cihazlardan daha fazla veri alması gerekebilir. Bu verilerin cihaz tarafından sağlanması gerekir. Bunun tersine, DPS istemci tarafı LOGI 'yi kolaylaştırmak için cihaza veri döndürebilir. 

## <a name="when-to-use-it"></a>Ne zaman kullanılır?
Bu özellik, [özel ayırma](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)geliştirmesi olarak kullanılabilir. Örneğin, cihazları, insan müdahalesi olmadan cihaz modeline göre ayırmak istiyorsunuz. Bu durumda, [özel tahsisi](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)kullanacaksınız. Cihazı, [kayıt cihazı çağrısının](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)bir parçası olarak model bilgilerini bildirmek üzere yapılandırabilirsiniz. DPS, cihazın bilgilerini özel ayırma Web kancasına geçilecektir. Ve işleviniz, cihaz modeli bilgilerini aldığında bu cihazın hangi IoT Hub gideceğine karar verebilir. Benzer şekilde, web kancası cihaza veri döndürmek istiyorsa, verileri web kancası yanıtında bir dize olarak geçirir.  

## <a name="device-sends-data-to-dps"></a>Cihaz, verileri DPS 'e gönderir
Cihazınız, DPS 'ye bir [yazmaç cihaz çağrısı](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) gönderirken, kayıt çağrısı, gövdedeki diğer alanları alacak şekilde geliştirilebilir. Gövde aşağıdaki gibi görünür: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS cihaza veri döndürüyor
Özel ayırma ilkesi Web kancası cihaza bazı veriler döndürmesini istiyorsa, verileri Web kancası yanıtında bir dize olarak geri iletir. Değişiklik, aşağıdaki returnData bölümünde bulunur. 
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
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK desteği
Bu özellik C, C#, Java ve Node. js [istemci SDK](https://docs.microsoft.com/azure/iot-dps/)'lerinde kullanılabilir.  

## <a name="next-steps"></a>Sonraki adımlar
* Azure IoT Hub ve Azure IoT Hub cihaz sağlama hizmeti için [Azure ıOT SDK 'sını]( https://github.com/Azure/azure-iot-sdks) kullanarak geliştirme
