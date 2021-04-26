---
title: Aracının ve hub modülünün özellikleri TWINS-Azure IoT Edge
description: EdgeAgent ve edgeHub modülü TWINS için belirli özellikleri ve değerlerini gözden geçirin
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29ec958764f4a464d51f29f4b9c8223d5d7a1760
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576015"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>IoT Edge Aracısı ve IoT Edge hub modülünün özellikleri TWINS

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge Aracısı ve IoT Edge hub 'ı IoT Edge çalışma zamanını oluşturan iki modüllerdir. Her çalışma zamanı modülünün sorumlulukları hakkında daha fazla bilgi için bkz. [Azure IoT Edge çalışma zamanını ve mimarisini anlayın](iot-edge-runtime.md).

Bu makalede, TWINS çalışma zamanı modülünün istenen özellikleri ve raporlanan özellikleri sağlanmaktadır. IoT Edge cihazlarda modül dağıtma hakkında daha fazla bilgi için bkz. [IoT Edge 'da modül dağıtmayı ve yolları oluşturmayı öğrenin](module-composition.md).

Modül ikizi şunları içerir:

* **İstenen özellikler**. Çözüm arka ucu istenen özellikleri ayarlayabilir ve modül bunları okuyabilir. Modül, istenen özelliklerde değişiklik bildirimleri de alabilir. İstenen özellikler, modül yapılandırmalarını veya koşullarını eşitlemeye yönelik bildirilen özelliklerle birlikte kullanılır.

* **Bildirilen özellikler**. Modül bildirilen özellikleri ayarlayabilir ve çözüm arka ucu bunları okuyabilir ve sorgulayabilir. Bildirilen özellikler, modül yapılandırmalarını veya koşullarını eşleştirmek için istenen özelliklerle birlikte kullanılır.

## <a name="edgeagent-desired-properties"></a>EdgeAgent istenen özellikleri

IoT Edge Aracısı için ikizi modülü çağrılır `$edgeAgent` ve bir cihazda çalışan IoT Edge Aracısı ve IoT Hub arasındaki iletişimleri koordine eder. İstenen özellikler, belirli bir cihaza tek bir aygıtın veya ölçekli dağıtımın bir parçası olarak bir dağıtım bildirimi uygulanırken ayarlanır.

| Özellik | Açıklama | Gerekli |
| -------- | ----------- | -------- |
| schemaVersion | "1,0" ya da "1,1". Sürüm 1,1 IoT Edge sürüm 1.0.10 ile kullanıma sunulmuştur ve önerilir. | Yes |
| Runtime. Type | "Docker" olmalıdır | Yes |
| Runtime. Settings. minDockerVersion | Bu dağıtım bildirimi için gereken en düşük Docker sürümüne ayarla | Yes |
| Runtime. Settings. loggingOptions | IoT Edge aracı kapsayıcısı için günlük seçeneklerini içeren bir strıngiingjson. [Docker günlüğü seçenekleri](https://docs.docker.com/engine/admin/logging/overview/) | No |
| Runtime. Settings. registryCredentials<br>. {Registryıd}. Kullanıcı adı | Kapsayıcı kayıt defterinin Kullanıcı adı. Azure Container Registry için, Kullanıcı adı genellikle kayıt adıdır.<br><br> Tüm özel modül görüntüleri için kayıt defteri kimlik bilgileri gereklidir. | No |
| Runtime. Settings. registryCredentials<br>. {Registryıd}. parola | Kapsayıcı kayıt defteri için parola. | No |
| Runtime. Settings. registryCredentials<br>. {Registryıd}. adres | Kapsayıcı kayıt defterinin adresi. Azure Container Registry için, adres genellikle *{Registry Name}. azurecr. IO* olur. | No |  
| systemModules. edgeAgent. Type | "Docker" olmalıdır | Yes |
| systemModules. edgeAgent. Settings. Image | IoT Edge aracısının görüntüsünün URI 'SI. Şu anda IoT Edge Aracısı kendisini güncelleştiremeyebilir. | Yes |
| systemModules. edgeAgent. Settings<br>. createOptions | IoT Edge aracı kapsayıcısının oluşturulmasına yönelik seçenekleri içeren bir strıngiingjson. [Docker oluşturma seçenekleri](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | Bu modülü dağıtan dağıtımın KIMLIĞI. | IoT Hub, bildirim bir dağıtım kullanılarak uygulandığında bu özelliği ayarlar. Dağıtım bildiriminin parçası değil. |
| systemModules. edgeHub. Type | "Docker" olmalıdır | Yes |
| systemModules. edgeHub. durumu | "Çalışıyor" olmalıdır | Yes |
| systemModules. edgeHub. restartPolicy | "Always" olmalıdır | Yes |
| systemModules. edgeHub. startupOrder | Bir modülün başlangıç sırasında sahip olduğu nokta için bir tamsayı değeri. 0 ilk ve en fazla tamsayı (4294967295) en son. Değer sağlanmazsa, varsayılan en fazla tamsayıdır.  | No |
| systemModules. edgeHub. Settings. Image | IoT Edge hub 'ının görüntüsünün URI 'SI. | Yes |
| systemModules. edgeHub. Settings<br>. createOptions | IoT Edge hub kapsayıcısının oluşturulmasına yönelik seçenekleri içeren bir strıngiingjson. [Docker oluşturma seçenekleri](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | Bu modülü dağıtan dağıtımın KIMLIĞI. | IoT Hub, bildirim bir dağıtım kullanılarak uygulandığında bu özelliği ayarlar. Dağıtım bildiriminin parçası değil. |
| Modüler. {ModuleID}. sürüm | Bu modülün sürümünü temsil eden Kullanıcı tanımlı bir dize. | Yes |
| Modüler. {ModuleID}. tür | "Docker" olmalıdır | Yes |
| Modüler. {ModuleID}. durum | {"çalışıyor" \| "durduruldu"} | Yes |
| Modüler. {ModuleID}. restartPolicy | {"hiçbir şekilde" \| "sorunlu" "sağlıksız" " \| \| her zaman"} | Yes |
| Modüler. {ModuleID}. startupOrder | Bir modülün başlangıç sırasında sahip olduğu nokta için bir tamsayı değeri. 0 ilk ve en fazla tamsayı (4294967295) en son. Değer sağlanmazsa, varsayılan en fazla tamsayıdır.  | No |
| Modüler. {ModuleID}. ımagepullpolicy | {"oluşturma sırasında" \| "hiçbir koşulda"} | No |
| Modüler. {ModuleID}. env | Modüle geçirilecek ortam değişkenlerinin listesi. Biçimi alır `"<name>": {"value": "<value>"}` | No |
| Modüler. {ModuleID}. Settings. image | Modül görüntüsünün URI 'SI. | Yes |
| Modüler. {ModuleID}. Settings. createOptions | Modül kapsayıcısının oluşturulmasına yönelik seçenekleri içeren bir strıngiingjson. [Docker oluşturma seçenekleri](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| Modüler. {ModuleID}. Configuration. ID | Bu modülü dağıtan dağıtımın KIMLIĞI. | IoT Hub, bildirim bir dağıtım kullanılarak uygulandığında bu özelliği ayarlar. Dağıtım bildiriminin parçası değil. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent tarafından bildirilen özellikler

IoT Edge Agent tarafından bildirilen Özellikler üç ana bilgi parçasını içerir:

1. Son görülen istenen özelliklerin uygulamanın durumu;
2. IoT Edge Aracısı tarafından bildirilen, cihazda çalışmakta olan modüllerin durumu; '
3. Şu anda cihazda çalışan istenen özelliklerin bir kopyası.

Geçerli istenen özelliklerin kopyası, cihazın en son dağıtımı uygulamışsa veya hala önceki bir dağıtım bildirimini çalıştırmadığını söylemek için yararlıdır.

> [!NOTE]
> IoT Edge aracısının bildirilen özellikleri, uygun ölçekte dağıtımların durumunu araştırmak için [IoT Hub sorgu diliyle](../iot-hub/iot-hub-devguide-query-language.md) sorgulanabilecek şekilde faydalıdır. Durum için IoT Edge Aracısı özelliklerinin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [tek cihazlar için IoT Edge dağıtımlarını anlayın veya ölçeklendirin](module-deployment-monitoring.md).

Aşağıdaki tablo, istenen özelliklerden kopyalanmış olan bilgileri içermez.

| Özellik | Açıklama |
| -------- | ----------- |
| lastDesiredVersion | Bu tamsayı, IoT Edge Aracısı tarafından işlenen istenen özelliklerin son sürümünü ifade eder. |
| lastDesiredStatus. Code | Bu durum kodu, IoT Edge Aracısı tarafından görülen son istenen özellikleri ifade eder. İzin verilen değerler: `200` başarılı, `400` geçersiz yapılandırma, `412` Geçersiz şema sürümü, `417` istenen özellikler boş, `500` başarısız |
| lastDesiredStatus. Description | Durumun metin açıklaması |
| configurationHealth. {DeploymentId}. sistem durumu | `healthy` {DeploymentId} dağıtımı tarafından ayarlanan tüm modüllerin çalışma zamanı durumu ya da ise `running` `stopped` , `unhealthy` Aksi takdirde |
| Runtime. platform. OS | Cihazda çalışan işletim sistemini raporlama |
| Runtime. platform. Architecture | Cihazdaki CPU mimarisini raporlama |
| systemModules. edgeAgent. runtimeStatus | IoT Edge aracısının bildirilen durumu: {"çalışıyor" " \| sağlıksız"} |
| systemModules. edgeAgent. statusDescription | IoT Edge aracısının bildirilen durumunun metin açıklaması. |
| systemModules. edgeHub. runtimeStatus | IoT Edge hub 'ın durumu: {"Running" \| "durduruldu" \| "başarısız" " \| geri alma" \| "sağlıksız"} |
| systemModules. edgeHub. statusDescription | Kötü durumda IoT Edge hub 'ının durumunun metin açıklaması. |
| systemModules. edgeHub. exitCode | Kapsayıcıda çıkılırken IoT Edge hub kapsayıcısı tarafından bildirilen çıkış kodu |
| systemModules. edgeHub. startTimeUtc | IoT Edge hub 'ın son başlatıldığı zaman |
| systemModules. edgeHub. lastExitTimeUtc | IoT Edge hub 'ının son çıkıldığında zaman |
| systemModules. edgeHub. lastRestartTimeUtc | IoT Edge hub 'ının son yeniden başlatılma zamanı |
| systemModules. edgeHub. restartCount | Yeniden başlatma ilkesinin bir parçası olarak bu modülün yeniden başlatılma sayısı. |
| Modüler. {ModuleID}. runtimeStatus | Modülün durumu: {"Running" \| "durduruldu" \| "başarısız" " \| geri alma" \| "sağlıksız"} |
| Modüler. {ModuleID}. statusDescription | Uygun değilse modülün durumunun metin açıklaması. |
| Modüler. {ModuleID}. exitCode | Kapsayıcı çıktığında modül kapsayıcısı tarafından bildirilen çıkış kodu |
| Modüler. {ModuleID}. startTimeUtc | Modülün en son başlatıldığı zaman |
| Modüler. {ModuleID}. lastExitTimeUtc | Modülün son çıkış zamanı |
| Modüler. {ModuleID}. lastRestartTimeUtc | Modülün son yeniden başlatılma zamanı |
| Modüler. {ModuleID}. restartCount | Yeniden başlatma ilkesinin bir parçası olarak bu modülün yeniden başlatılma sayısı. |

## <a name="edgehub-desired-properties"></a>EdgeHub istenen özellikleri

IoT Edge Hub için ikizi modülü çağrılır `$edgeHub` ve bir cihazda çalışan IoT Edge hub 'ı ile IoT Hub arasındaki iletişimleri koordine eder. İstenen özellikler, belirli bir cihaza tek bir aygıtın veya ölçekli dağıtımın bir parçası olarak bir dağıtım bildirimi uygulanırken ayarlanır.

| Özellik | Açıklama | Dağıtım bildiriminde gereklidir |
| -------- | ----------- | -------- |
| schemaVersion | "1,0" ya da "1,1". Sürüm 1,1 IoT Edge sürüm 1.0.10 ile kullanıma sunulmuştur ve önerilir. | Yes |
| yolların. Routetablename | IoT Edge hub yolunu temsil eden bir dize. Daha fazla bilgi için bkz. [yolları bildirme](module-composition.md#declare-routes). | `routes`Öğe mevcut ancak boş olabilir. |
| storeAndForwardConfiguration. timeToLiveSecs | IoT Edge hub 'ın, IoT Hub veya yerel bir modülse, yönlendirme uç noktaları bağlantısı kesildiğinde iletileri tutacağını belirten saniye cinsinden süre. Değer herhangi bir pozitif tamsayı olabilir. | Yes |

## <a name="edgehub-reported-properties"></a>EdgeHub tarafından bildirilen özellikler

| Özellik | Açıklama |
| -------- | ----------- |
| lastDesiredVersion | Bu tamsayı, IoT Edge hub 'ı tarafından işlenen istenen özelliklerin son sürümünü ifade eder. |
| lastDesiredStatus. Code | IoT Edge Merkezi tarafından görülen son istenen özelliklere başvuran durum kodu. İzin verilen değerler: `200` başarılı, `400` geçersiz yapılandırma, `500` başarısız |
| lastDesiredStatus. Description | Durumun metin açıklaması. |
| istemcinin. {Device veya ModuleID}. durum | Bu cihazın veya modülün bağlantı durumu. Olası değerler {"bağlı" \| "bağlantısı kesildi"}. Yalnızca modül kimlikleri bağlantısı kesik durumda olabilir. IoT Edge hub 'ına bağlanan aşağı akış cihazları yalnızca bağlı olduğunda görünür. |
| istemcinin. {Device veya ModuleID}. lastConnectTime | Cihaz veya modülün bağlı olduğu son zaman. |
| istemcinin. {Device veya ModuleID}. lastDisconnectTime | Cihazın veya modülün bağlantısının en son bağlantısı kesildi. |

## <a name="next-steps"></a>Sonraki adımlar

Dağıtım bildirimleri oluşturmak için bu özellikleri nasıl kullanacağınızı öğrenmek için bkz. [IoT Edge modüllerinin nasıl kullanılabileceğini, yapılandırılacağını ve yeniden kullanıldığını anlayın](module-composition.md).
