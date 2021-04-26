---
title: Azure IoT Central cihaz bağlantılarında sorun giderme | Microsoft Docs
description: IoT Central ' de cihazlarınızdan veri görmediğiniz sorunları giderin
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: 494608f9dd8fbf986dcda6eeb782a64f6a2ca008
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378576"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Cihazlarınızdan gelen verilerin Azure IoT Central'da gösterilmemesi sorununu giderme

Bu belge, cihaz geliştiricilerinin cihazların IoT Central ' a gönderdiği verilerin uygulamada gösterilmediğini bulmasına yardımcı olur.

Araştırmak için iki ana alan vardır:

- Cihaz bağlantısı sorunları
  - Cihaz gibi kimlik doğrulama sorunları geçersiz kimlik bilgilerine sahip
  - Ağ bağlantısı sorunları
  - Cihaz onaylanmadı veya engellendi
- Cihaz yükü şekli sorunları

Bu sorun giderme kılavuzu cihaz bağlantısı sorunları ve cihaz yükü şekli sorunları üzerine odaklanır.

## <a name="device-connectivity-issues"></a>Cihaz bağlantısı sorunları

Bu bölüm, verilerinizin IoT Central ulaşmadığını belirlemenize yardımcı olur.

Daha önce yapmadıysanız, `az cli` Aracı ve `azure-iot` uzantıyı yükleyebilirsiniz.

' Nin nasıl yükleneceğini öğrenmek için `az cli` bkz. [Azure CLI 'yı yüklemek](/cli/azure/install-azure-cli).

Uzantıyı [yüklemek](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) için `azure-iot` Şu komutu çalıştırın:

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> `uamqp`Uzantı komutunu ilk kez çalıştırdığınızda kitaplığı yüklemeniz istenebilir.

`azure-iot`Uzantıyı yükledikten sonra, gönderme iletilerinin IoT Central yolunu yapıp yapyüklenmediğini görmek için cihazınızı başlatın.

IoT Central uygulamanıza sahip olduğunuz abonelikte oturum açmak için aşağıdaki komutları kullanın:

```azurecli
az login
az set account --subscription <your-subscription-id>
```

Cihazınızın gönderdiği Telemetriyi izlemek için aşağıdaki komutu kullanın:

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Cihaz IoT Central başarıyla bağlanırsa aşağıdakine benzer bir çıktı görürsünüz:

```output
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Cihazınızın IoT Central ile birlikte değişiminde olan özellik güncelleştirmelerini izlemek için aşağıdaki Önizleme komutunu kullanın:

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Cihaz, özellik güncelleştirmelerini başarıyla gönderirse aşağıdakine benzer bir çıktı görürsünüz:

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Terminalinizde veriler görünürse, veriler IoT Central uygulamanız olarak bu duruma gelir.

Birkaç dakika sonra herhangi bir veri görünmüyorsa, `Enter` `return` çıktının takılı olması durumunda klavyenizdeki veya tuşuna basmayı deneyin.

Terminalinizde herhangi bir veri görünmüyorsa, büyük olasılıkla cihazınızda ağ bağlantısı sorunları yaşıyor veya IoT Central verileri doğru bir şekilde göndermiyor olabilir.

### <a name="check-the-provisioning-status-of-your-device"></a>Cihazınızın sağlama durumunu denetleyin

Verileriniz izleyicisinde görünmüyorsa, aşağıdaki komutu çalıştırarak cihazınızın sağlama durumunu kontrol edin:

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

Aşağıdaki çıktıda, bağlantısı engellenen bir cihaz örneği gösterilmektedir:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Cihaz sağlama durumu | Açıklama | Olası risk azaltma |
| - | - | - |
| Sağlanan | Anında tanınabilir sorun yoktur. | Yok |
| Kaydedildi | Cihaz henüz IoT Central bağlanmadı. | Bağlantı sorunları için cihaz günlüklerinizi denetleyin. |
| Engellendi | Cihazın IoT Central 'e bağlanması engellendi. | Cihazın IoT Central uygulamasına bağlanması engellendi. IoT Central cihaz engelini kaldırın ve yeniden deneyin. Daha fazla bilgi için bkz. [Cihazları engelleme](concepts-get-connected.md#device-status-values). |
| Durumdaki | Cihaz onaylanmadı. | Cihaz IoT Central uygulamasına bağlanacak şekilde onaylanmadı. IoT Central cihazı onaylayın ve yeniden deneyin. Daha fazla bilgi için bkz. [cihazları onaylama](concepts-get-connected.md#device-registration) |
| İlişkilendirilmemiş | Cihaz bir cihaz şablonuyla ilişkilendirilmemiş. | IoT Central verileri ayrıştırmayı bilmesi için cihazı bir cihaz şablonuyla ilişkilendirin. |

[Cihaz durum kodları](concepts-get-connected.md#device-status-values)hakkında daha fazla bilgi edinin.

### <a name="error-codes"></a>Hata kodları

Hala verilerinizin neden gösterilmediğini tanılamadıysanız `monitor-events` , bir sonraki adım cihazınız tarafından bildirilen hata kodlarını aramaya yöneliktir.

Cihazınızda bir hata ayıklama oturumu başlatın veya cihazınızdan günlükleri toplayın. Cihazın rapor aldığı herhangi bir hata kodunu denetleyin.

Aşağıdaki tablolarda, genel hata kodları ve hafifletmek için olası eylemler gösterilmektedir.

Kimlik doğrulama akışla ilgili sorunlar görüyorsanız:

| Hata kodu | Açıklama | Olası risk azaltma |
| - | - | - |
| 400 | İsteğin gövdesi geçerli değil. Örneğin, ayrıştırılamıyor veya nesne doğrulanamıyor. | Kanıtlama akışının bir parçası olarak doğru istek gövdesini gönderdiğinizden emin olun veya bir cihaz SDK 'Sı kullanın. |
| 401 | Yetkilendirme belirteci doğrulanamıyor. Örneğin, süresi sona ermiştir veya isteğin URI 'sine uygulanmaz. Bu hata kodu, TPM kanıtlama akışının bir parçası olarak cihazlara de döndürülür. | Cihazınızın doğru kimlik bilgilerine sahip olduğundan emin olun. |
| 404 | Cihaz sağlama hizmeti örneği veya kayıt gibi bir kaynak yok. | [Müşteri desteği ile Bilet dosyası oluşturma](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 412 | `ETag`İstekteki, `ETag` RFC7232 başına mevcut kaynak ile eşleşmiyor. | [Müşteri desteği ile Bilet dosyası oluşturma](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 429 | İşlemler, hizmet tarafından kısıtlanıyor. Belirli hizmet limitleri için bkz. [cihaz sağlama hizmeti sınırlarını IoT Hub](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | İleti sıklığını azaltın, sorumlulukları daha fazla cihaz arasında ayırın. |
| 500 | Bir iç hata oluştu. | Size daha fazla yardımcı olup olmadığını görmek için [müşteri desteğiyle bir bilet dosyası](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) yapın. |

### <a name="file-upload-error-codes"></a>Karşıya dosya yükleme hata kodları

Bir cihaz buluta dosya yüklemeye çalıştığında görebileceğiniz yaygın hata kodlarının listesi aşağıda verilmiştir. Cihazınızın bir dosyayı karşıya yükleyebilmesi için uygulamanızdaki [cihaz dosya yüklemelerini](howto-configure-file-uploads.md) yapılandırmanız gerektiğini unutmayın.

| Hata kodu | Açıklama | Olası risk azaltma |
| - | - | - |
| 403006  | Eşzamanlı karşıya dosya yükleme işlemlerinin sayısını aştınız. Her cihaz istemcisi, 10 eşzamanlı dosya yükleme ile sınırlıdır. | Cihazın, karşıya dosya yükleme işleminin tamamlandığını IoT Central hemen bildirdiğinden emin olun. Bu işe yaramazsa, istek zaman aşımını azaltmayı deneyin. |

## <a name="payload-shape-issues"></a>Yük şekli sorunları

Cihazınızın IoT Central veri gönderdiğini belirlediğinizde, sonraki adım cihazınızın verileri geçerli bir biçimde göndermesini sağlamaktır.

Cihaz verilerinin IoT Central görünmemesine neden olan genel sorunların iki ana kategorisi vardır:

- Cihaz şablonundan cihaza veri uyumsuzluğu:
  - Yazım hataları veya büyük/küçük harf eşleştirme sorunları gibi adlandırırken uyumsuzluk.
  - Şemanın cihaz şablonunda tanımlanmadığı Modellenmemiş Özellikler.
  - Şablonda tanımlanmış tür gibi şema uyumsuzluğu `boolean` , ancak veri bir dizedir.
  - Aynı telemetri adı birden çok arabirimde tanımlanmıştır, ancak cihaz IoT Tak ve Kullan uyumlu değildir.
- Veri şekli geçersiz JSON. Daha fazla bilgi için bkz. [telemetri, özellik ve komut yükleri](concepts-telemetry-properties-commands.md).

Sorununuzla ilgili hangi kategorilerin olduğunu saptamak için senaryonuz için en uygun komutu çalıştırın:

- Telemetriyi doğrulamak için Önizleme komutunu kullanın:

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Özellik güncelleştirmelerini doğrulamak için Önizleme komutunu kullanın

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

`uamqp`Bir komutu ilk kez çalıştırdığınızda kitaplığı yüklemeniz istenebilir `validate` .

Aşağıdaki çıktı, Validate komutundan örnek hata ve uyarı iletilerini gösterir:

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Bir GUI kullanmayı tercih ediyorsanız, bir şeyin modellendirilmediğinden bakmak için IoT Central **ham veri** görünümünü kullanın. **Ham veri** görünümü, cihazın hatalı biçimlendirilmiş JSON gönderip göndermediğini algılamaz.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Ham veri görünümünün ekran görüntüsü":::

Sorunu tespit ettiğinizde cihaz bellenimini güncelleştirmeniz veya daha önce Modellenmemiş verileri modelleyen yeni bir cihaz şablonu oluşturmanız gerekebilir.

Verileri doğru şekilde modelleyen yeni bir şablon oluşturmayı seçerseniz, cihazları eski şablonlarınızdan yeni şablona geçirin. Daha fazla bilgi edinmek için bkz. [Azure IoT Central uygulamanızda cihazları yönetme](howto-manage-devices.md).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/community/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir [Azure destek bileti](https://portal.azure.com/#create/Microsoft.Support)dosyası da oluşturabilirsiniz.

Daha fazla bilgi için bkz. [Azure IoT desteği ve yardım seçenekleri](../../iot-fundamentals/iot-support-help.md).
