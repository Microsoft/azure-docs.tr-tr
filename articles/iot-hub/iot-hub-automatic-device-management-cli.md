---
title: Azure IoT Hub ile ölçek sırasında otomatik cihaz yönetimi (CLı) | Microsoft Docs
description: Birden çok IoT cihazını veya modülünü yönetmek için Azure IoT Hub otomatik yapılandırma kullanın
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 0b8b499613f8234f449e6d72f6ed6ec1f2f21287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545421"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Azure CLI’yı kullanarak otomatik IoT cihazı ve modül yönetimi

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT Hub otomatik cihaz yönetimi, büyük cihaz filklerin yönetilmesi için yinelenen ve karmaşık görevlerin birçoğunu otomatikleştirir. Otomatik cihaz yönetimi sayesinde, özelliklerini temel alarak bir cihaz kümesini hedefleyebilir, istenen yapılandırmayı tanımlayabilir ve ardından IoT Hub cihazları kapsama geldiğinde güncelleştirebilir. Bu güncelleştirme, bir _otomatik cihaz yapılandırması_ veya _otomatik modül yapılandırması_ kullanılarak yapılır; bu işlem tamamlama ve uyumluluğu özetler, birleştirme ve çakışmaları idare etmenize ve yapılandırmaları aşamalı bir yaklaşımda kullanıma almanızı sağlar.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Otomatik cihaz yönetimi, istenen özelliklerle bir dizi cihaz WINS veya modül TWINS 'i güncelleştirerek ve ikizi bildirilen özellikleri temel alan bir Özet raporlayarak işe yarar.  Üç bölümden oluşan *yapılandırma* adlı yeni bir sınıf ve JSON belgesi tanıtır:

* **Hedef koşul** , görüntülenecek cihaz TWINS veya modül TWINS kapsamını tanımlar. Hedef koşul, Device ikizi etiketlerinde ve/veya bildirilen özelliklerde bir sorgu olarak belirtilir.

* **Hedef içerik** , hedeflenen cihaz TWINS veya modül TWINS 'de eklenecek veya güncelleştirilebilen istenen özellikleri tanımlar. İçerik, istenen özelliklerin değiştirilmesi için bir yol içerir.

* **Ölçümler** , **başarı**, **devam** ediyor ve **hata** gibi çeşitli yapılandırma durumlarının Özet sayılarını tanımlar. Özel ölçümler, ikizi tarafından bildirilen özelliklerde sorgu olarak belirtilir.  Sistem ölçümleri, hedeflenen TWINS sayısı ve başarıyla güncelleştirilmiş TWINS sayısı gibi ikizi güncelleştirme durumunu ölçen varsayılan ölçülerdir.

Yapılandırma oluşturulduktan ve sonra beş dakikalık aralıklarla otomatik yapılandırmalar ilk kez çalışır. Ölçüm sorguları otomatik yapılandırmanın her çalıştırılışında çalışır.

## <a name="cli-prerequisites"></a>CLı önkoşulları

* Azure aboneliğinizdeki bir [IoT Hub 'ı](../iot-hub/iot-hub-create-using-cli.md) . 

* Ortamınızdaki [Azure CLI](/cli/azure/install-azure-cli) . Azure CLı sürümünüz en azından 2.0.70 veya üzeri olmalıdır. Doğrulamak için `az –-version` kullanın. Bu sürüm, az uzantı komutlarını destekler ve Knack komut çerçevesini kullanıma sunar. 

* [Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>TWINS 'i Uygula

Otomatik cihaz yapılandırmalarının, bulut ve cihazlar arasında durum eşitlemesini sağlamak için cihaz ikikesi kullanılması gerekir.  Daha fazla bilgi için bkz. [IoT Hub cihaz TWINS 'ı anlama ve kullanma](iot-hub-devguide-device-twins.md).

Otomatik modül yapılandırmalarının, bulut ve modüller arasında durumu eşitlemesini sağlamak için modül TWINS 'in kullanılmasını gerektirir. Daha fazla bilgi için bkz. [IoT Hub modül TWINS 'ı anlama ve kullanma](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>TWINS 'i hedeflemek için etiketleri kullanma

Bir yapılandırma oluşturmadan önce, hangi cihazları veya modülleri etkilenmesini istediğinizi belirtmeniz gerekir. Azure IoT Hub cihazları tanımlar ve cihaz ikizi Etiketler kullanarak modülleri tanımlar ve ikizi Module içindeki etiketleri belirler. Her bir cihaz veya modülde birden çok etiket olabilir ve bunları çözümünüz için anlamlı hale getiren herhangi bir şekilde tanımlayabilirsiniz. Örneğin, farklı konumlardaki cihazları yönetiyorsanız, bir cihaz ikizi aşağıdaki etiketleri ekleyin:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Hedef içerik ve ölçümleri tanımlama

Hedef içerik ve ölçüm sorguları, cihaz ikizi veya modülünü tanımlayan JSON belgeleri olarak belirtilir ve bu özellikleri ölçmek için ayarlayın ve rapor edin.  Azure CLı kullanarak otomatik bir yapılandırma oluşturmak için hedef içerik ve ölçümleri yerel olarak. txt dosyası olarak kaydedin. Yapılandırmayı cihazınıza uygulamak için komutunu çalıştırdığınızda sonraki bir bölümde dosya yollarını kullanırsınız.

Otomatik cihaz yapılandırmasına yönelik temel bir hedef içerik örneği aşağıda verilmiştir:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Otomatik modül yapılandırması çok benzer şekilde davranır, ancak `moduleContent` yerine hedefleyebilirsiniz `deviceContent` .

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Ölçüm sorgularının örnekleri aşağıda verilmiştir:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

Modüller için ölçüm sorguları Ayrıca cihaz sorgularına benzerdir, ancak ' den ' ı seçersiniz `moduleId` `devices.modules` . Örnek: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Yapılandırma oluşturma

Hedef içeriği ve ölçümleri içeren bir yapılandırma oluşturarak hedef cihazları yapılandırırsınız. 

Bir yapılandırma oluşturmak için aşağıdaki komutu kullanın:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**yapılandırma-kimliği** -IoT Hub 'ında oluşturulacak yapılandırmanın adı. Yapılandırmanıza en fazla 128 harf olan benzersiz bir ad verin. Boşluklardan ve aşağıdaki geçersiz karakterlerden kaçının: `& ^ [ ] { } \ | " < > /` .

* --**Etiketler** -yapılandırmanızı izlemeye yardımcı olmak için Etiketler ekleyin. Etiketler, dağıtımınızı tanımlayan ad ve değer çiftleridir. Örneğin `HostPlatform, Linux` veya `Version, 3.0.1` olabilir.

* --ikizi istenen özellikler olarak ayarlanacak hedef içeriğe yönelik **içerik** satır içi JSON veya dosya yolu. 

* --**hub-adı** -yapılandırmanın oluşturulacağı IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Komutuyla istenen aboneliğe geçiş yapın `az account set -s [subscription name]`

* --**hedef koşul** -Bu yapılandırmayla hangi cihazların veya modüllerin hedefleneceğini belirleyen bir hedef koşul girin. Otomatik cihaz yapılandırması için, bu durum cihaz ikizi etiketlerine veya cihaz ikizi istenen özelliklere dayalıdır ve ifade biçimiyle eşleşmelidir. Örneğin `tags.environment='test'` veya `properties.desired.devicemodel='4000x'` olabilir. Otomatik modül yapılandırması için, koşul modül ikizi Tags veya Module ikizi istenen özellikleri temel alır. Örneğin `from devices.modules where tags.environment='test'` veya `from devices.modules where properties.reported.chillerProperties.model='4000x'` olabilir.

* --**Öncelik** -pozitif bir tamsayı. İki veya daha fazla yapılandırmanın aynı cihaza veya modüle hedeflenmiş olması durumunda, öncelik için en yüksek sayısal değere sahip yapılandırma uygulanır.

* --**ölçümler** -ölçüm sorgularına FilePath. Ölçümler, bir cihaz ya da modülün yapılandırma içeriği uygulandıktan sonra yeniden rapor edebileceği çeşitli durumların Özet sayısını sağlar. Örneğin, bekleyen ayarlar değişiklikleri, hatalara yönelik bir ölçüm ve başarılı ayarlar değişiklikleri için bir ölçüm oluşturabilirsiniz. 

## <a name="monitor-a-configuration"></a>Bir yapılandırmayı izleme

Bir yapılandırmanın içeriğini göstermek için aşağıdaki komutu kullanın:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**yapılandırma-kimliği** -IoT Hub 'ında bulunan yapılandırmanın adı.

* --**hub-adı** -yapılandırmanın bulunduğu IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Komutuyla istenen aboneliğe geçiş yapın `az account set -s [subscription name]`

Komut penceresinde yapılandırmayı inceleyin. **Ölçümler** özelliği her bir hub tarafından değerlendirilen her ölçüm için bir sayı listeler:

* **Targetedcount** -hedefleme koşuluyla eşleşen IoT Hub cihaz TWINS veya modül TWINS sayısını belirten bir sistem ölçümü.

* **appliedCount** -bir sistem ölçümü, hedef içeriğe uygulanmış olan cihazların veya modüllerin sayısını belirtir.

* **Özel ölçümünüzün** -tanımladığınız tüm ölçümler Kullanıcı ölçümleridir.

Aşağıdaki komutu kullanarak her ölçüm için cihaz kimliklerinin, modül kimliklerinin veya nesnelerin bir listesini gösterebilirsiniz:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**yapılandırma-kimliği** -IoT Hub 'ında bulunan dağıtımın adı.

* --**ölçüm-kimliği** -cihaz kimliklerinin veya modül kimliklerinin listesini görmek istediğiniz ölçümün adı (örneğin,) `appliedCount` .

* --**hub-adı** -dağıtımın bulunduğu IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Komutuyla istenen aboneliğe geçiş yapın `az account set -s [subscription name]` .

* --**Ölçüm-türü** -ölçüm türü veya olabilir `system` `user` .  Sistem ölçümleri `targetedCount` ve ' dir `appliedCount` . Diğer tüm ölçümler Kullanıcı ölçümleridir.

## <a name="modify-a-configuration"></a>Yapılandırma değiştirme

Bir yapılandırmayı değiştirdiğinizde, değişiklikler hedeflenen tüm cihazlara hemen çoğaltılır. 

Hedef koşulu güncelleştirirseniz, aşağıdaki güncelleştirmeler gerçekleşir:

* Bir ikizi eski hedef koşulunu karşılamıyorsa, ancak yeni hedef koşulunu karşılıyorsa ve bu yapılandırma bu ikizi için en yüksek önceliktir, bu yapılandırma uygulanır. 

* Şu anda bu yapılandırmayı çalıştıran bir ikizi artık hedef koşulu karşılamıyorsa, yapılandırmanın ayarları kaldırılır ve ikizi bir sonraki en yüksek öncelik yapılandırması tarafından değiştirilir. 

* Şu anda bu yapılandırmayı çalıştıran bir ikizi, hedef koşulunu artık karşılamıyor ve diğer yapılandırmaların hedef koşulunu karşılamıyorsa, yapılandırmadan gelen ayarlar kaldırılır ve ikizi üzerinde başka bir değişiklik yapılmaz. 

Bir yapılandırmayı güncelleştirmek için aşağıdaki komutu kullanın:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**yapılandırma-kimliği** -IoT Hub 'ında bulunan yapılandırmanın adı.

* --**hub-adı** -yapılandırmanın bulunduğu IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Komutuyla istenen aboneliğe geçiş yapın `az account set -s [subscription name]` .

* --yapılandırmada bir özelliği **ayarlayın** . Aşağıdaki özellikleri güncelleştirebilirsiniz:

    * targetCondition-Örneğin `targetCondition=tags.location.state='Oregon'`

    * etikete 

    * Priority

## <a name="delete-a-configuration"></a>Yapılandırma silme

Bir yapılandırmayı sildiğinizde, herhangi bir cihaz WINS veya modül TWINS, bir sonraki en yüksek öncelikli yapılandırmasını alır. TWINS başka bir yapılandırmanın hedef koşulunu karşılamıyorsa, başka hiçbir ayar uygulanmaz. 

Bir yapılandırmayı silmek için aşağıdaki komutu kullanın:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**yapılandırma-kimliği** -IoT Hub 'ında bulunan yapılandırmanın adı.

* --**hub-adı** -yapılandırmanın bulunduğu IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Komutuyla istenen aboneliğe geçiş yapın `az account set -s [subscription name]` .

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede IoT cihazlarını ölçeklendirerek nasıl yapılandıracağınızı ve izleyeceğinizi öğrendiniz. Azure IoT Hub 'yi yönetme hakkında daha fazla bilgi edinmek için bu bağlantıları izleyin:

* [IoT Hub cihaz kimliklerinizi toplu olarak yönetme](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub 'ınızı izleme](monitor-iot-hub.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)
* [Azure IOT Edge ile sınır cihazlarına Al dağıtma](../iot-edge/quickstart-linux.md)

Tam zamanında sağlama işlemini etkinleştirmek üzere IoT Hub cihaz sağlama hizmetini kullanarak araştırmak için, bkz.: 

* [Azure IoT Hub Cihazı Sağlama Hizmeti](../iot-dps/index.yml)