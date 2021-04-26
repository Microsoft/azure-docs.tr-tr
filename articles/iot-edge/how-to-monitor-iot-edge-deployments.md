---
title: IoT Edge dağıtımlarını izleme-Azure IoT Edge
description: EdgeHub ve edgeAgent dahil olmak üzere yüksek düzey izleme özellikleri ve otomatik dağıtım ölçümleri.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 39e7bb5c151d490e79ef111589f52f260c3e6c7a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483172"
---
# <a name="monitor-iot-edge-deployments"></a>IoT Edge dağıtımlarını izleme

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge, IoT Edge cihazlarınıza dağıtılan modüllerle ilgili gerçek zamanlı bilgileri izlemenize olanak sağlayan raporlama sağlar. IoT Hub hizmeti cihazlardan durumu alır ve işletmen için kullanılabilir hale getirir. İzleme, otomatik dağıtımlar ve katmanlı dağıtımlar içeren [ölçekte yapılan dağıtımlar](module-deployment-monitoring.md) için de önemlidir.

Her iki cihaz ve modülün da bağlantı gibi benzer verileri vardır, bu nedenle değerler cihaz KIMLIĞINE veya modül KIMLIĞINE göre elde edilir.

IoT Hub hizmeti cihaz ve modül TWINS tarafından bildirilen verileri toplar ve cihazların sahip olabileceği çeşitli durumların sayısını sağlar. IoT Hub hizmeti bu verileri dört ölçüm grubuna düzenler:

| Tür | Açıklama |
| --- | ---|
| Hedeflenen | Dağıtım hedefleme durumuyla eşleşen cihazları gösterir IoT Edge. |
| Uygulandı | Daha yüksek önceliğe sahip başka bir dağıtım tarafından hedeflenilmemiş hedeflenen IoT Edge cihazları gösterir. |
| Başarılı raporlama | Modüllerin başarıyla dağıtıldığını bildiren IoT Edge cihazları gösterir. |
| Raporlama hatası | Bir veya daha fazla modülün başarıyla dağıtılmadığını bildiren IoT Edge cihazları gösterir. Hatayı daha fazla araştırmak için, bu cihazlara uzaktan bağlanın ve günlük dosyalarını görüntüleyin. |

IoT Hub hizmeti bu verileri, Azure portal ve Azure CLı 'de izlemeniz için kullanılabilir hale getirir.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Azure portal bir dağıtımı izleme

Bir dağıtımın ayrıntılarını görüntülemek ve çalıştıran cihazları izlemek için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub gidin.
1. Sol bölme menüsünden **IoT Edge** ' yi seçin.
1. **IoT Edge dağıtımları** sekmesini seçin.
1. Dağıtım listesini inceleyin. Her dağıtım için aşağıdaki ayrıntıları görebilirsiniz:

    | Sütun | Açıklama |
    | --- | --- |
    | ID | Dağıtımın adı. |
    | Tür | Dağıtım türü, **dağıtım** ya da **katmanlı dağıtım**. |
    | Hedef koşul | Hedeflenen cihazları tanımlamak için kullanılan etiket. |
    | Öncelik | Dağıtıma atanan öncelik numarası. |
    | Sistem ölçümleri | IoT Hub ' de hedefleme koşuluyla eşleşen cihaz iklistesi sayısı. **Uygulanan** , dağıtım içeriğinin IoT Hub ' de modül TWINS 'lerine uyguladığı cihazların sayısını belirtir. |
    | Cihaz ölçümleri | IoT Edge istemci çalışma zamanındaki başarılı veya hatalı raporlayan IoT Edge cihaz sayısı. |
    | Özel ölçümler | Dağıtım için tanımladığınız tüm ölçümler için verileri bildiren IoT Edge cihazların sayısı. |
    | Oluşturulma Zamanı | Dağıtımın oluşturulduğu zaman damgası. Bu zaman damgası, iki dağıtım aynı önceliğe sahip olduğunda özellikleri bölmek için kullanılır. |

1. İzlemek istediğiniz dağıtımı seçin.  
1. **Dağıtım ayrıntıları** sayfasında, alt bölüme aşağı kaydırın ve **hedef koşul** sekmesini seçin. Hedef koşulla eşleşen cihazları listelemek için **Görünüm** ' ü seçin. Koşulu ve ayrıca **önceliğini** değiştirebilirsiniz. Değişiklik yaptıysanız **Kaydet** ' i seçin.

   ![Dağıtım için hedeflenen cihazları görüntüleme](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. **Ölçümler** sekmesini seçin. **Ölçüm Seç** açılan listesinden bir ölçüm seçerseniz, sonuçları görüntülemeniz Için bir **Görünüm** düğmesi görüntülenir. Tanımladığınız özel ölçümlere ilişkin ölçütleri ayarlamak için **ölçümleri Düzenle** ' yi de seçebilirsiniz. Değişiklik yaptıysanız **Kaydet** ' i seçin.

   ![Dağıtım için ölçümleri görüntüleme](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Dağıtımınızda değişiklik yapmak için bkz. [bir dağıtımı değiştirme](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Azure CLı ile bir dağıtımı izleme

Tek bir dağıtımın ayrıntılarını görüntülemek için [az IoT Edge Deployment Show](/cli/azure/iot/edge/deployment) komutunu kullanın:

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Dağıtım göster komutu aşağıdaki parametreleri alır:

* **--Deployment-ID** -IoT Hub 'ında bulunan dağıtımın adı. Gerekli parametre.
* **--hub-adı** -dağıtımın bulunduğu IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Komutuyla istenen aboneliğe geçiş yapın `az account set -s [subscription name]`

Komut penceresinde dağıtımı inceleyin. **Ölçümler** özelliği her bir hub tarafından değerlendirilen her ölçüm için bir sayı listeler:

* **Targetedcount** -hedefleme koşuluyla eşleşen IoT Hub bir cihaz TWINS sayısını belirten bir sistem ölçümü.
* **appliedCount** -bir sistem ölçümü, dağıtım içeriğinin IoT Hub ' de modül TWINS 'lerine uyguladığı cihazların sayısını belirtir.
* **Reportedbaşarıyla Fulcount** -IoT Edge istemci çalışma zamanının başarılı olarak bildirdiği IoT Edge cihazların sayısını belirten bir cihaz ölçümü.
* **Reportedfailedcount** -IoT Edge istemci çalışma zamanından dağıtım raporlama hatası içindeki IoT Edge cihazlarının sayısını belirten bir cihaz ölçümü.

[Az IoT Edge Deployment Show-Metric](/cli/azure/iot/edge/deployment) komutuyla her ölçüm Için cihaz kimliklerinin veya nesnelerinin bir listesini gösterebilirsiniz:

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Dağıtım Show-Metric komutu aşağıdaki parametreleri alır:

* **--Deployment-ID** -IoT Hub 'ında bulunan dağıtımın adı.
* **--Metric-ID** -cihaz kimliklerinin listesini görmek istediğiniz ölçüm adı (örneğin,) `reportedFailedCount` .
* **--hub-adı** -dağıtımın bulunduğu IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Komutuyla istenen aboneliğe geçiş yapın `az account set -s [subscription name]` .

Dağıtımınızda değişiklik yapmak için bkz. [bir dağıtımı değiştirme](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Sonraki adımlar

IoT Edge dağıtımlarınızın bağlantısı ve sistem durumu için öncelikle IoT Edge Aracısı ve IoT Edge hub çalışma zamanı modülleri olan [Modül TWINS 'i izlemeyi](how-to-monitor-module-twins.md)öğrenin.
