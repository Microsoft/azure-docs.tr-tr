---
title: EdgeAgent ve EdgeHub istenen özellikler başvuru - Azure IOT Edge | Microsoft Docs
description: Belirli özellikleri ve değerlerini edgeAgent ve edgeHub modül ikizlerini için gözden geçirin.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: caee247dfb1f7068e935be9c293a28870cfb98ce
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74069322"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>IoT Edge Aracısı ve IoT Edge hub modülünün özellikleri TWINS

IoT Edge Aracısı ve IoT Edge hub 'ı IoT Edge çalışma zamanını oluşturan iki modüllerdir. Her modülün gerçekleştirdiği hangi görevleri hakkında daha fazla bilgi için bkz. [Azure IOT Edge çalışma zamanı ve mimarisini anlama](iot-edge-runtime.md). 

Bu makalede, istenen özellikleri ve çalışma zamanı modül ikizlerini bildirilen özellikleri sağlar. IoT Edge cihazlarda modül dağıtma hakkında daha fazla bilgi için bkz. [IoT Edge 'da modül dağıtmayı ve yolları oluşturmayı öğrenin](module-composition.md).

Modül ikizi şunları içerir: 

* **İstenen özellikler**. Çözüm arka ucu istenen özellikleri ayarlayabilir ve modül bunları okuyabilir. Modül, istenen özelliklerde değişiklik bildirimleri de alabilir. İstenen özellikler, modül yapılandırmalarını veya koşullarını eşitlemeye yönelik bildirilen özelliklerle birlikte kullanılır.

* **Bildirilen özellikler**. Modül bildirilen özellikleri ayarlayabilir ve çözüm arka ucu bunları okuyabilir ve sorgulayabilir. Bildirilen özellikler, modül yapılandırmalarını veya koşullarını eşleştirmek için istenen özelliklerle birlikte kullanılır. 

## <a name="edgeagent-desired-properties"></a>İstenen EdgeAgent özellikleri

IoT Edge aracısına yönelik modül ikizi `$edgeAgent` ve bir cihazda çalışan IoT Edge Aracısı arasındaki iletişimleri koordine eder ve IoT Hub. İstenen özellikleri, bir dağıtım bildirimi tek cihaz veya ölçekli bir dağıtımının parçası olarak belirli bir cihazda uygulama işlemi sırasında ayarlanır. 

| Özellik | Açıklama | Gerekli |
| -------- | ----------- | -------- |
| schemaVersion | "1.0" olması gerekir | Yes |
| Runtime.Type | "Docker" olması gerekir | Yes |
| runtime.settings.minDockerVersion | Bu dağıtım bildirimi tarafından gereken en düşük Docker sürümü için ayarlayın | Yes |
| runtime.settings.loggingOptions | IoT Edge aracı kapsayıcısı için günlük seçeneklerini içeren bir strıngiingjson. [Docker günlük seçenekleri](https://docs.docker.com/engine/admin/logging/overview/) | Hayır |
| runtime.settings.registryCredentials<br>. {registryId} .username | Kapsayıcı kayıt defteri kullanıcı adı. Azure Container Registry için kullanıcı kayıt defteri adı genellikle adıdır.<br><br> Kayıt defteri kimlik bilgilerini ortak olmayan modül görüntüleri için gerekli değildir. | Hayır |
| runtime.settings.registryCredentials<br>. {registryId} .password | Kapsayıcı kayıt defteri parolası. | Hayır |
| runtime.settings.registryCredentials<br>. {registryId} .address | Kapsayıcı kayıt defteri adresi. Azure Container Registry için, adres genellikle *{Registry Name}. azurecr. IO*olur. | Hayır |  
| systemModules.edgeAgent.type | "Docker" olması gerekir | Yes |
| systemModules.edgeAgent.settings.image | IoT Edge aracısının görüntüsünün URI 'SI. Şu anda IoT Edge Aracısı kendisini güncelleştiremez. | Yes |
| systemModules.edgeAgent.settings<br>.createOptions | IoT Edge aracı kapsayıcısının oluşturulmasına yönelik seçenekleri içeren bir strıngiingjson. [Docker oluşturma seçenekleri](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Hayır |
| systemModules.edgeAgent.configuration.id | Bu modül dağıtılan dağıtım kimliği. | IoT Hub, bildirim bir dağıtım kullanılarak uygulandığında bu özelliği ayarlar. Parçası olmayan bir dağıtım bildirimi. |
| systemModules.edgeHub.type | "Docker" olması gerekir | Yes |
| systemModules.edgeHub.status | "Çalışıyor" gerekir | Yes |
| systemModules.edgeHub.restartPolicy | "Her zaman" olması gerekir | Yes |
| systemModules.edgeHub.settings.image | IoT Edge hub 'ının görüntüsünün URI 'SI. | Yes |
| systemModules.edgeHub.settings<br>.createOptions | IoT Edge hub kapsayıcısının oluşturulmasına yönelik seçenekleri içeren bir strıngiingjson. [Docker oluşturma seçenekleri](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Hayır |
| systemModules.edgeHub.configuration.id | Bu modül dağıtılan dağıtım kimliği. | IoT Hub, bildirim bir dağıtım kullanılarak uygulandığında bu özelliği ayarlar. Parçası olmayan bir dağıtım bildirimi. |
| modüller. {Moduleıd} .version | Bu modülün sürümünü temsil eden kullanıcı tanımlı bir dize. | Yes |
| modules.{moduleId}.type | "Docker" olması gerekir | Yes |
| modüller. {Moduleıd} .status | {"çalışıyor" \| "durduruldu"} | Yes |
| modules.{moduleId}.restartPolicy | {"hiçbir zaman" \| "\|" sorunlu "\|" Always "} | Yes |
| Modüler. {ModuleID}. ımagepullpolicy | {"oluşturma sırasında" \| "hiçbir şekilde"} | Hayır |
| modüller. {Moduleıd}.settings.image | Modülü görüntüsü URI. | Yes |
| modules.{moduleId}.settings.createOptions | Modül container oluşturulması için seçenekleri içeren bir dizeleştirilmiş JSON. [Docker oluşturma seçenekleri](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Hayır |
| modüller. {Moduleıd}.configuration.id | Bu modül dağıtılan dağıtım kimliği. | IoT Hub, bildirim bir dağıtım kullanılarak uygulandığında bu özelliği ayarlar. Parçası olmayan bir dağıtım bildirimi. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent bildirilen özellikler

IoT Edge Agent tarafından bildirilen Özellikler üç ana bilgi parçasını içerir:

1. Son görülme istenen özellikleri uygulama durumu;
2. IoT Edge Aracısı tarafından bildirilen, cihazda çalışmakta olan modüllerin durumu; '
3. İstenen özellikleri kullanarak cihaz üzerinde şu anda çalışan bir kopyası.

Geçerli istenen özelliklerin bir kopyası olan bu son bilgi, cihazın istenen en son özellikleri uygulanıp uygulanmadığını veya hala önceki bir dağıtım bildirimini çalıştırmadığını söylemek için yararlıdır.

> [!NOTE]
> IoT Edge aracısının bildirilen özellikleri, uygun ölçekte dağıtımların durumunu araştırmak için [IoT Hub sorgu diliyle](../iot-hub/iot-hub-devguide-query-language.md) sorgulanabilecek şekilde faydalıdır. Durum için IoT Edge Aracısı özelliklerinin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [tek cihazlar için IoT Edge dağıtımlarını anlayın veya ölçeklendirin](module-deployment-monitoring.md).

Aşağıdaki tabloda, istenen özelliklerden kopyalanır bilgileri içermez.

| Özellik | Açıklama |
| -------- | ----------- |
| lastDesiredVersion | Bu tamsayı, IoT Edge Aracısı tarafından işlenen istenen özelliklerin son sürümünü ifade eder. |
| lastDesiredStatus.code | Bu durum kodu, IoT Edge Aracısı tarafından görülen son istenen özellikleri ifade eder. İzin verilen değerler: `200` başarı `400` geçersiz yapılandırma `412` geçersiz şema sürümü `417` istenen özellikleri boş `500` başarısız oldu |
| lastDesiredStatus.description | Durum açıklaması metni |
| deviceHealth | `healthy` tüm modüllerin çalışma zamanı durum ya da ise `running` veya `stopped`, `unhealthy` Aksi takdirde |
| configurationHealth.{deploymentId}.health | `healthy` çalışma zamanı durumu {Deploymentıd} dağıtımı tarafından tüm modüllerin geçerli olduğunda `running` veya `stopped`, `unhealthy` Aksi takdirde |
| runtime.platform.OS | Cihaz üzerinde çalışan işletim Sisteminin raporlama |
| Runtime.Platform.Architecture | Cihazda raporlama CPU mimarisi |
| systemModules.edgeAgent.runtimeStatus | IoT Edge aracısının bildirilen durumu: {"Running" \| "sağlıksız"} |
| systemModules.edgeAgent.statusDescription | IoT Edge aracısının bildirilen durumunun metin açıklaması. |
| systemModules.edgeHub.runtimeStatus | IoT Edge hub 'ın durumu: {"Running" \| "durduruldu" \| "başarısız oldu" \| "backoff" \| "sağlıksız"} |
| systemModules.edgeHub.statusDescription | Kötü durumda IoT Edge hub 'ının durumunun metin açıklaması. |
| systemModules.edgeHub.exitCode | Kapsayıcıda çıkılırken IoT Edge hub kapsayıcısı tarafından bildirilen çıkış kodu |
| systemModules.edgeHub.startTimeUtc | IoT Edge hub 'ın son başlatıldığı zaman |
| systemModules.edgeHub.lastExitTimeUtc | IoT Edge hub 'ının son çıkıldığında zaman |
| systemModules.edgeHub.lastRestartTimeUtc | IoT Edge hub 'ının son yeniden başlatılma zamanı |
| systemModules.edgeHub.restartCount | Kaç kez yeniden başlatma ilkesi bir parçası olarak bu modülü yeniden başlatıldı. |
| modüller. {Moduleıd} .runtimeStatus | Modülün durumu: {"Running" \| "durduruldu" \| "başarısız oldu" \| "backoff" \| "sağlıksız"} |
| modules.{moduleId}.statusDescription | Uygun değilse modülün durumunun metin açıklaması. |
| modules.{moduleId}.exitCode | Kapsayıcı çıktığında modül kapsayıcısı tarafından bildirilen çıkış kodu |
| modüller. {Moduleıd} .startTimeUtc | Modül son başlatıldığı saat |
| modules.{moduleId}.lastExitTimeUtc | Zaman zaman modülü son çıkıldı |
| modules.{moduleId}.lastRestartTimeUtc | Zaman modülün en son ne zaman yeniden başlatıldı |
| modules.{moduleId}.restartCount | Kaç kez yeniden başlatma ilkesi bir parçası olarak bu modülü yeniden başlatıldı. |

## <a name="edgehub-desired-properties"></a>İstenen EdgeHub özellikleri

IoT Edge Hub için modül ikizi `$edgeHub` olarak adlandırılır ve bir cihazda çalışan IoT Edge hub 'ı ve IoT Hub arasındaki iletişimleri koordine eder. İstenen özellikleri, bir dağıtım bildirimi tek cihaz veya ölçekli bir dağıtımının parçası olarak belirli bir cihazda uygulama işlemi sırasında ayarlanır. 

| Özellik | Açıklama | Dağıtım bildiriminde gerekli |
| -------- | ----------- | -------- |
| schemaVersion | "1.0" olması gerekir | Yes |
| yollar. {Routetablename} | IoT Edge hub yolunu temsil eden bir dize. Daha fazla bilgi için bkz. [yolları bildirme](module-composition.md#declare-routes). | `routes` Öğesi var ancak boş olabilir. |
| storeAndForwardConfiguration.timeToLiveSecs | IoT Edge hub 'ın, IoT Hub veya yerel bir modülse, yönlendirme uç noktaları bağlantısı kesildiğinde iletileri tutacağını belirten saniye cinsinden süre. Değer herhangi bir pozitif tamsayı olabilir. | Yes |

## <a name="edgehub-reported-properties"></a>EdgeHub bildirilen özellikler

| Özellik | Açıklama |
| -------- | ----------- |
| lastDesiredVersion | Bu tamsayı, IoT Edge hub 'ı tarafından işlenen istenen özelliklerin son sürümünü ifade eder. |
| lastDesiredStatus.code | IoT Edge Merkezi tarafından görülen son istenen özelliklere başvuran durum kodu. İzin verilen değerler: `200` başarı `400` geçersiz yapılandırma `500` başarısız oldu |
| lastDesiredStatus.description | Durumun metin açıklaması. |
| istemciler. {cihaz veya modül kimliği} .status | Bu cihaz veya modül bağlantı durumu. Olası değerler {"bağlı" \| "bağlantısı"}. Yalnızca modül kimlikleri bağlantısı kesilmiş olabilir. IoT Edge hub 'ına bağlanan aşağı akış cihazları yalnızca bağlı olduğunda görünür. |
| istemciler. {cihaz veya modül kimliği} .lastConnectTime | Cihaz veya modülün bağlı olduğu son zaman. |
| istemciler. {cihaz veya modül kimliği} .lastDisconnectTime | Cihazın veya modülün bağlantısının en son bağlantısı kesildi. |

## <a name="next-steps"></a>Sonraki adımlar

Bu özellikler dağıtım bildirimleri oluşturmak için kullanmayı öğrenin için bkz: [nasıl IOT Edge modülleri, yapılandırılmış, yeniden kaldırılabilir ve anlamak](module-composition.md).
