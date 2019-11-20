---
title: Cihazları çevrimdışı çalıştırma-Azure IoT Edge | Microsoft Docs
description: IoT Edge cihazların ve modüllerin, uzun süreler boyunca internet bağlantısı olmadan nasıl çalışacağını ve IoT Edge normal IoT cihazlarının da çevrimdışı şekilde çalışmasını nasıl etkinleştirebileceğini anlayın.
author: kgremban
ms.author: kgremban
ms.date: 08/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3fc90e685a3c6a077250028bae5602e95f114c03
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293447"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>IoT Edge cihazları, modülleri ve alt cihazları için genişletilmiş çevrimdışı özellikleri anlayın

Azure IoT Edge, IoT Edge cihazlarınızda genişletilmiş çevrimdışı işlemleri destekler ve IoT Edge olmayan alt cihazlarda çevrimdışı işlemleri de mümkün. Bir IoT Edge cihazda IoT Hub bağlanmak için bir fırsat olduğu sürece, bu ve tüm alt cihazlar aralıklı veya internet bağlantısı olmadan çalışmaya devam edebilir. 


## <a name="how-it-works"></a>Nasıl çalışır

Bir IoT Edge cihaz çevrimdışı moda geçtiğinde, IoT Edge hub 'ı üç rol alır. İlk olarak, yukarı akış yapılacak iletileri depolar ve cihaz yeniden bağlanana kadar bunları kaydeder. İkincisi, işleme devam edebilmek için modüller ve alt cihazların kimliğini doğrulamak üzere IoT Hub adına işlem yapar. Üçüncü olarak, normalde IoT Hub ilerlebilen alt aygıtlar arasında iletişime izin verebilir. 

Aşağıdaki örnekte, bir IoT Edge senaryosunun çevrimdışı modda nasıl çalıştığı gösterilmektedir:

1. **Cihazları Yapılandır**

   IoT Edge cihazların çevrimdışı özellikleri otomatik olarak etkinleştirilmiştir. Bu özelliği diğer IoT cihazlarına genişletmek için IoT Hub cihazlar arasında üst-alt ilişkisi bildirmeniz gerekir. Daha sonra, alt cihazları atanan üst cihazlarına güvenmek ve bir ağ geçidi olarak üst öğe aracılığıyla cihazdan buluta iletişimleri yönlendirmek üzere yapılandırırsınız. 

2. **IoT Hub ile Eşitle**

   IoT Edge çalışma zamanının yüklenmesinden sonra en az bir kez, IoT Edge cihazın IoT Hub ile eşitlenmesi için çevrimiçi olması gerekir. Bu eşitlemede IoT Edge cihaz, kendisine atanmış tüm alt cihazlarla ilgili ayrıntıları alır. IoT Edge cihaz, çevrimdışı işlemleri etkinleştirmek ve telemetri iletilerinin yerel depolama ayarlarını almak için yerel önbelleğini de güvenli şekilde güncelleştirir. 

3. **Çevrimdışı çalış**

   IoT Hub bağlantısı kesilirken, IoT Edge cihaz, dağıtılan modüller ve tüm alt IoT cihazları süresiz olarak çalışabilir. Modüller ve alt cihazlar, çevrimdışıyken IoT Edge hub ile kimlik doğrulaması yaparak başlayabilir ve yeniden başlatılabilir. IoT Hub için telemetri sınırı yukarı akış yerel olarak depolanır. Modüller veya alt IoT cihazları arasında iletişim, doğrudan Yöntemler veya iletiler aracılığıyla sürdürülür. 

4. **IoT Hub yeniden bağlama ve yeniden eşitleme**

   IoT Hub bağlantısı geri yüklendikten sonra, IoT Edge cihaz yeniden eşitlenir. Yerel olarak depolanan iletiler, depolandıkları sırada teslim edilir. Modüller ve cihazların istenen ve bildirilen özellikleri arasındaki farklılıklar uzlaştırılacaktır. IoT Edge cihaz, atanan alt IoT cihazları kümesi üzerinde yapılan değişiklikleri güncelleştirir.

## <a name="restrictions-and-limits"></a>Kısıtlamalar ve sınırlar

Bu makalede açıklanan genişletilmiş çevrimdışı yetenekler, [IoT Edge Version 1.0.7 veya üzeri](https://github.com/Azure/azure-iotedge/releases)sürümlerde sunulmaktadır. Önceki sürümlerde çevrimdışı özelliklerin bir alt kümesi vardır. Genişletilmiş çevrimdışı özellikleri olmayan mevcut IoT Edge cihazları, çalışma zamanı sürümü değiştirilerek yükseltilemez, ancak bu özellikleri kazanmak için yeni bir IoT Edge cihaz kimliğiyle yeniden yapılandırılması gerekir. 

Genişletilmiş çevrimdışı destek, Doğu ABD **hariç** IoT Hub kullanılabildiği tüm bölgelerde kullanılabilir.

Yalnızca IoT Edge olmayan cihazlar alt cihaz olarak eklenebilir. 

IoT Edge cihazlar ve atanan alt cihazları ilk, tek seferlik eşitlemeden sonra süresiz olarak çevrimdışı çalışabilir. Ancak, iletilerin depolanması yaşam süresi (TTL) ayarına ve iletileri depolamaya yönelik kullanılabilir disk alanına bağlıdır. 

## <a name="set-up-parent-and-child-devices"></a>Üst ve alt cihazları ayarlama

IoT Edge bir cihazın genişletilmiş çevrimdışı yeteneklerini alt IoT cihazlarına genişletmesi için iki adımı gerçekleştirmeniz gerekir. İlk olarak Azure portal üst-alt ilişkilerini bildirin. İkinci olarak, üst cihaz ve herhangi bir alt cihaz arasında bir güven ilişkisi oluşturun ve ardından, bir ağ geçidi olarak üst öğe üzerinden gezinmek için cihazdan buluta iletişimleri yapılandırın. 

### <a name="assign-child-devices"></a>Alt cihazları ata

Alt cihazlar, aynı IoT Hub kayıtlı IoT Edge olmayan herhangi bir cihaz olabilir. Üst cihazlarda birden çok alt cihaz olabilir, ancak bir alt cihazın yalnızca bir üst öğesi vardır. Alt cihazları bir sınır cihazına ayarlamak için üç seçenek vardır: Azure portal, Azure CLı veya IoT Hub hizmeti SDK 'sını kullanma. 

Aşağıdaki bölümlerde, var olan IoT cihazları için IoT Hub ' de üst/alt ilişkiyi nasıl bildirebileceği hakkında örnekler sağlanmaktadır. Alt cihazlarınız için yeni cihaz kimlikleri oluşturuyorsanız, daha fazla bilgi için bkz. [Azure 'da bir aşağı akış cihazının kimliğini doğrulama IoT Hub](how-to-authenticate-downstream-device.md) .

#### <a name="option-1-iot-hub-portal"></a>Seçenek 1: IoT Hub Portal

Yeni bir cihaz oluştururken üst-alt ilişkisi bildirebilirsiniz. Ya da var olan cihazlar için, üst IoT Edge cihazının veya alt IoT cihazının cihaz ayrıntıları sayfasından ilişkiyi bildirebilirsiniz. 

   ![IoT Edge cihaz ayrıntıları sayfasından alt cihazları yönetme](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Seçenek 2: `az` komut satırı aracını kullanma

[Azure komut satırı arabirimini](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) [IoT uzantısı](https://github.com/azure/azure-iot-cli-extension) (v 0.7.0 veya üzeri) ile birlikte kullanarak, üst alt ilişkilerini [cihaz kimliği](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) alt komutları ile yönetebilirsiniz. Aşağıdaki örnekte, hub 'daki tüm IoT Edge olmayan cihazları bir IoT Edge cihazının alt cihazları olacak şekilde atamak için bir sorgu kullanılır. 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

Farklı bir cihaz alt kümesini seçmek için [sorguyu](../iot-hub/iot-hub-devguide-query-language.md) değiştirebilirsiniz. Büyük bir cihaz kümesi belirtirseniz komut birkaç saniye sürebilir.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Seçenek 3: IoT Hub hizmeti SDK 'sını kullanma 

Son olarak, Java ya da Node. js IoT Hub Service C#SDK 'sını kullanarak üst alt öğe ilişkilerini programlı bir şekilde yönetebilirsiniz. SDK kullanarak [bir alt cihaz atamaya bir örnek](https://aka.ms/set-child-iot-device-c-sharp) aşağıda verilmiştir. C#

### <a name="set-up-the-parent-device-as-a-gateway"></a>Ana cihazı ağ geçidi olarak ayarlama

Üst/alt öğe ilişkisini saydam bir ağ geçidi olarak düşünebilirsiniz. burada, alt cihazın IoT Hub kendi kimliği vardır, ancak bulut üzerinden kendi üst yoluyla iletişim kurar. Güvenli iletişim için, alt cihazın, ana cihazın güvenilir bir kaynaktan geldiğini doğrulayabilmesi gerekir. Aksi halde, üçüncü taraflar üst düzey cihazları üst öğeleri taklit etmek ve iletişimleri ele almak için ayarlayabilir. 

Bu güven ilişkisini oluşturmanın bir yolu, aşağıdaki makalelerde ayrıntılı olarak açıklanmıştır: 
* [IoT Edge cihazını saydam ağ geçidi olarak davranacak şekilde yapılandırma](how-to-create-transparent-gateway.md)
* [Bir aşağı akış (alt) cihazını Azure IoT Edge bir ağ geçidine bağlama](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS sunucularını belirtme 

Sağlamlığını artırmak için ortamınızda kullanılan DNS sunucusu adreslerini belirtmeniz önemle tavsiye edilir. DNS sunucunuzu IoT Edge için ayarlamak için, bkz. [Edge Aracısı modülü için çözüm ' boş yapılandırma dosyası ' ' nı sürekli raporlar ve sorun giderme makalesinde cihazda hiç modül başlamıyor](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) .

## <a name="optional-offline-settings"></a>İsteğe bağlı çevrimdışı ayarlar

Cihazlarınız çevrimdışı kalırsa, IoT Edge üst cihaz, bağlantı kurulana kadar tüm cihazdan buluta iletileri depolar. IoT Edge hub modülü, çevrimdışı iletilerin depolanmasını ve iletilmesini yönetir. Uzun süreler boyunca çevrimdışı duruma getirebileceği cihazlarda, iki IoT Edge hub ayarını yapılandırarak performansı iyileştirin. 

İlk olarak, IoT Edge hub 'ının iletileri cihazın yeniden bağlanmasına yetecek kadar uzun tutacağından, canlı ayar süresini artırın. Daha sonra, ileti depolaması için ek disk alanı ekleyin. 

### <a name="time-to-live"></a>Yaşam süresi

Yaşam süresi ayarı, bir iletinin süresi dolmadan önce teslim edilebilmesi için bekleyeceği süre (saniye cinsinden). Varsayılan değer 7200 saniyedir (iki saat). Maksimum değer yalnızca 2.000.000.000 etrafında olan bir tamsayı değişkeninin maksimum değeri ile sınırlıdır. 

Bu ayar, ikizi modülünde depolanan IoT Edge hub 'ının istenen bir özelliğidir. Azure portal veya doğrudan dağıtım bildiriminde yapılandırabilirsiniz. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Sistem modülleri için konak depolaması

Mesajlar ve modül durumu bilgileri, varsayılan olarak IoT Edge hub 'ının yerel kapsayıcı dosya sisteminde depolanır. Özellikle çevrimdışı çalışırken, daha iyi güvenilirlik sağlamak için, konak IoT Edge cihazında depolamayı da ayırabilirsiniz. Daha fazla bilgi için bkz. [modüllerin bir cihazın yerel depolama alanına erişmesine Izin verme](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Sonraki adımlar

Üst/alt cihaz bağlantılarınız için saydam bir ağ geçidi ayarlama hakkında daha fazla bilgi edinin: 

* [IoT Edge cihazını saydam ağ geçidi olarak davranacak şekilde yapılandırma](how-to-create-transparent-gateway.md)
* [Azure IoT Hub’da bir aşağı akış cihazının kimliğini doğrulama](how-to-authenticate-downstream-device.md)
* [Aşağı akış cihazını Azure IoT Edge ağ geçidine bağlama](how-to-connect-downstream-device.md)
