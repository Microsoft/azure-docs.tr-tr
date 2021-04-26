---
title: İzleyici modül TWINS-Azure IoT Edge
description: Bağlantı ve sistem durumunu öğrenmek için cihaz ikizlerini ve modül TWINS 'i yorumlama.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a5a31e15c88cef588c93f44c8fe5303d930b5b2c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479381"
---
# <a name="monitor-module-twins"></a>Modül ikizlerini izleme

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure 'da modül TWINS IoT Hub IoT Edge dağıtımlarınızın bağlantısını ve durumunu izlemeyi etkinleştirir. Modül TWINS, IoT Hub 'ınızda çalışan modüllerinizin performansı hakkında yararlı bilgiler depolar. [IoT Edge Aracısı](iot-edge-runtime.md#iot-edge-agent) ve [IoT Edge merkezi](iot-edge-runtime.md#iot-edge-hub) çalışma zamanı modülleri, her biri sırasıyla kendi modül ikliklerini `$edgeAgent` ve `$edgeHub` ' ı korur:

* `$edgeAgent` hem IoT Edge Agent hem de IoT Edge hub Runtime modülleri ve özel modülleriniz hakkındaki sistem durumu ve bağlantı verilerini içerir. IoT Edge Aracısı, modülleri dağıtmaktan, izlemekten ve bağlantı durumunu Azure IoT Hub 'ınıza bildirmekten sorumludur.
* `$edgeHub` bir cihazda ve Azure IoT Hub 'ınız üzerinde çalışan IoT Edge hub 'ı arasındaki iletişimlerle ilgili verileri içerir. Bu, gelen iletileri aşağı akış cihazlarından işlemeyi içerir. IoT Edge hub, Azure IoT Hub ile IoT Edge cihazları ve modülleri arasındaki iletişimin işlenmesinden sorumludur.

Veriler, modül TWINS ' JSON yapılarında istenen ve bildirilen özellik kümeleriyle birlikte meta veriler, Etiketler halinde düzenlenir. deployment.jsdosyasında belirttiğiniz istenen özellikler, TWINS modülüne kopyalanır. IoT Edge Aracısı ve IoT Edge hub 'ı, modülleri için bildirilen özellikleri güncelleştirir.

Benzer şekilde, deployment.jsdosyadaki özel modülleriniz için belirtilen özellikler, ikizi modülüne kopyalanır, ancak çözüm, bildirilen özellik değerlerini sağlamaktan sorumludur.

Bu makalede Azure portal, Azure CLı ve Visual Studio Code modül TWINS 'in nasıl inceleneceğini açıklanmaktadır. Cihazlarınızın dağıtımları nasıl alacağını izleme hakkında bilgi için bkz. [Monitor IoT Edge dağıtımlarını](how-to-monitor-iot-edge-deployments.md)izleme. Modül TWINS kavramıyla ilgili genel bir bakış için bkz. [IoT Hub 'da modül TWINS 'ı anlama ve kullanma](../iot-hub/iot-hub-devguide-module-twins.md).

> [!TIP]
> Bir IoT Edge cihazının IoT Hub 'ından bağlantısı kesilirse, bir çalışma zamanı modülünün bildirilen özellikleri eski olabilir. [](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` Bağlantının kaybolup olmadığını anlamak için modüle ping gönderebilirsiniz.

## <a name="monitor-runtime-module-twins"></a>Çalışma zamanı modülünü izleme TWINS

Dağıtım bağlantı sorunlarını gidermek için IoT Edge Aracısı ve IoT Edge hub çalışma zamanı modülü TWINS ' i gözden geçirin ve ardından diğer modüllerle detaya gidin.

### <a name="monitor-iot-edge-agent-module-twin"></a>IoT Edge aracı modülünü izleme ikizi

Aşağıdaki JSON, `$edgeAgent` Visual Studio Code IÇINDEKI JSON bölümlerinin çoğunu daraltılan ikizi modülünü gösterir.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

JSON, en üstten başlayarak aşağıdaki bölümlerde açıklanabilir:

* Meta veriler-bağlantı verileri Içerir. Intergelme, IoT Edge aracısının bağlantı durumu her zaman bağlantısı kesik durumda: `"connectionState": "Disconnected"` . Bağlantı durumu, cihazdan buluta (D2C) iletilerle ilgilidir ve IoT Edge Aracısı D2C iletilerini göndermez.
* Özellikler-ve alt `desired` `reported` bölümleri içerir.
* Properties. Desired-(daraltılmış olarak gösteriliyor) dosyadaki deployment.jsişleci tarafından ayarlanan beklenen özellik değerleri.
* Properties. raporlanan-IoT Edge Aracısı tarafından raporlanan en son özellik değerleri.

Ve bölümlerinin her ikisi de `properties.desired` `properties.reported` benzer bir yapıya sahiptir ve şema, sürüm ve çalışma zamanı bilgileri için ek meta veriler içerir. Ayrıca `modules` , tüm özel modüller (gibi `SimulatedTemperatureSensor` ) ve `systemModules` `$edgeAgent` `$edgeHub` çalışma zamanı modülleri için bölümü de dahildir.

Bildirilen özellik değerlerini istenen değerlerle karşılaştırarak, tutarsızlıkları tespit edebilir ve sorunları gidermenize yardımcı olabilecek bağlantıları belirleyebilirsiniz. Bu karşılaştırmaları yaparken, `$lastUpdated` `metadata` araştırdığınız özelliğin bölümünde bildirilen değeri denetleyin.

Aşağıdaki özellikler sorun gidermeye yönelik incelemek için önemlidir:

* **ExitCode** -sıfır dışında bir değer modülün hata ile durdurulduğunu belirtir. Ancak, bir modül kasıtlı olarak durdurulmuş bir duruma ayarlandıysa, 137 veya 143 hata kodları kullanılır.

* **Laststarttimeutc** -kapsayıcının en son başlatıldığı **Tarih ve saati** gösterir. Kapsayıcı başlatılmamışsa, bu değer 0001-01-01T00:00:00Z olur.

* **Lastexittimeutc** -kapsayıcının son bittiği **Tarih ve saati** gösterir. Bu değer, kapsayıcı çalışıyorsa ve hiç durdurulmadıysa, 0001-01-01T00:00:00Z 'dir.

* **runtimeStatus** -aşağıdaki değerlerden biri olabilir:

    | Değer | Açıklama |
    | --- | --- |
    | bilinmeyen | Dağıtım oluşturuluncaya kadar varsayılan durum. |
    | geri alma | Modül başlamak üzere zamanlandı ancak şu anda çalışmıyor. Bu değer, yeniden başlatma sırasında durum değişikliği yapılmakta olan bir modül için yararlıdır. Hatalı bir modülün, cool-off döneminde yeniden başlatma beklediği zaman, modül geri alma durumunda olur. |
    | çalıştıran | Modülün Şu anda çalıştığını gösterir. |
    | sağlıksız | Bir sistem durumu araştırma denetimini başarısız veya zaman aşımına uğradığını gösterir. |
    | durdurulacağını | Modülün başarıyla çıkış yaptığını belirtir (sıfır çıkış kodu ile). |
    | başarısız | Modülün bir hata çıkış kodu (sıfır olmayan) ile çıkış yaptığını gösterir. Modül, etkin yeniden başlatma ilkesine bağlı olarak bu durumdan geri dönerek geri geçirebilir. Bu durum, modülün kurtarılamaz bir hatayla karşılaştığını gösterebilir. Microsoft Monitoring Agent (MMA), modülü artık yeniden boyutlandıramaması durumunda yeni bir dağıtım gerektiren hata oluşur. |

Ayrıntılar için bkz. [Edgeagent tarafından bildirilen özellikler](module-edgeagent-edgehub.md#edgeagent-reported-properties) .

### <a name="monitor-iot-edge-hub-module-twin"></a>IoT Edge hub modülünü izleme ikizi

Aşağıdaki JSON, `$edgeHub` Visual Studio Code IÇINDEKI JSON bölümlerinin çoğunu daraltılan ikizi modülünü gösterir.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

JSON, en üstten başlayarak aşağıdaki bölümlerde açıklanabilir:

* Meta veriler-bağlantı verileri Içerir.

* Özellikler-ve alt `desired` `reported` bölümleri içerir.
* Properties. Desired-(daraltılmış olarak gösteriliyor) dosyadaki deployment.jsişleci tarafından ayarlanan beklenen özellik değerleri.
* Properties. raporlanan-IoT Edge hub tarafından raporlanan en son özellik değerleri.

Aşağı akış cihazlarınızla ilgili sorun yaşıyorsanız, bu verilerin incelenbaşlaması iyi bir yerdir.

## <a name="monitor-custom-module-twins"></a>Özel modül TWINS 'i izle

Özel modüllerinizin bağlantısı hakkındaki bilgiler, ikizi aracı modülünde IoT Edge tutulur. Özel modülünüzün ikizi modülü, birincil olarak çözümünüzün verilerini korumak için kullanılır. deployment.jsdosyasında tanımladığınız istenen özellikler ikizi modülüne yansıtılır ve modülünüzün bildirilen özellik değerlerini gerektiği şekilde güncelleştirebilir.

Modülünüzün uygulama koduna bağlı olarak ikizi modülünde bildirilen özellik değerlerini güncelleştirmek için [Azure IoT Hub cihaz SDK](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 'leriyle tercih ettiğiniz programlama dilini kullanabilirsiniz. Aşağıdaki yordam, [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) modülündeki kodu kullanarak bunu yapmak için .NET için Azure SDK 'sını kullanır:

1. [Createfromenvironmentaysnc](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) yöntemiyle [moduleclient](/dotnet/api/microsoft.azure.devices.client.moduleclient) örneği oluşturun.

1. [GetTwinAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync) yöntemiyle Module ikizi 'in özelliklerinin bir koleksiyonunu alın.

1. İstenen özelliklerde yapılan değişiklikleri [Setdesiredpropertyupdatecallbackasync](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync) yöntemiyle yakalamak için bir dinleyici (geri çağırma geçirerek) oluşturun.

1. Geri çağırma yönteminde, ikizi modülündeki bildirilen özellikleri [UpdateReportedPropertiesAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient) yöntemiyle güncelleştirin ve bu özellik değerlerinin bir TwinCollection geçirerek ayarlamak istediğiniz özellik değerlerini bir [](/dotnet/api/microsoft.azure.devices.shared.twincollection) geçirerek güncelleştirin.

## <a name="access-the-module-twins"></a>Modül TWINS 'e erişin

Azure IoT Hub, Visual Studio Code ve Azure CLı ile modül TWINS için JSON 'u inceleyebilirsiniz.

### <a name="monitor-in-azure-iot-hub"></a>Azure IoT Hub izleme

İkizi modülünün JSON 'sini görüntülemek için:

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.
1. Sol bölme menüsünden **IoT Edge** ' yi seçin.
1. **IoT Edge cihazlar** sekmesinde, izlemek istediğiniz modüllerle CIHAZıN **cihaz kimliğini** seçin.
1. **Modüller** sekmesinden modül adı ' nı seçin ve ardından üst menü çubuğundan **modül kimliği ikizi** ' nı seçin.

  ![Azure portal görüntülemek için bir modül ikizi seçin](./media/how-to-monitor-module-twins/select-module-twin.png)

"Bu modül için bir modül kimliği yok" iletisini görürseniz, bu hata arka uç çözümünün artık kimliği ilk oluşturmuş olduğunu gösterir.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Visual Studio Code modül TWINS 'i izleme

Bir modülü ikizi gözden geçirmek ve düzenlemek için:

1. Önceden yüklenmemişse, Visual Studio Code için [Azure IoT araçları uzantısını](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) yükler.
1. **Gezgin**'de **Azure IoT Hub**' i genişletin ve ardından, izlemek istediğiniz modülün bulunduğu cihazı genişletin.
1. Modüle sağ tıklayın ve **modülü Düzenle ikizi**' yi seçin. İkizi modülünün geçici bir dosyası bilgisayarınıza indirilir ve Visual Studio Code görüntülenir.

  ![Visual Studio Code düzenlemek için bir modül ikizi alın](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Değişiklik yaparsanız, IoT Hub 'ınıza değişiklikleri kaydetmek için düzenleyicideki kodun üzerindeki **Ikizi modülünü Güncelleştir** ' i seçin.

  ![Visual Studio Code modül ikizi güncelleştirme](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Azure CLı 'de modül TWINS 'i izleme

IoT Edge çalışıp çalışmadığını görmek için [az IoT Hub Invoke-Module-metodunu](how-to-edgeagent-direct-method.md#ping) kullanarak IoT Edge aracısına ping gönderin.

[Az IoT Hub Module-ikizi](/cli/azure/iot/hub/module-twin) yapısı şu komutları sağlar:

* **az IoT Hub Module-ikizi Show** -Module ikizi tanımını göster.
* **az IoT Hub Module-ikizi Update** -Module ikizi tanımını güncelleştirin.
* **az IoT Hub Module-ikizi Replace** -Module ikizi tanımını hedef JSON ile değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

[Yerleşik doğrudan yöntemler kullanarak EdgeAgent ile iletişim kurmayı](how-to-edgeagent-direct-method.md)öğrenin.
