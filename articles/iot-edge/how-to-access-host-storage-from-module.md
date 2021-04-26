---
title: IoT Edge cihaz yerel depolama alanını bir modülden kullanın-Azure IoT Edge | Microsoft Docs
description: IoT Edge cihaz yerel depolama alanına modül erişimi sağlamak için ortam değişkenlerini ve oluşturma seçeneklerini kullanın.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 78752d4da42fe07461ae0e82b10343dc7219ad91
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482067"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Modüllerin bir cihazın yerel depolama alanına erişmesine izin ver

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure depolama hizmetleri 'ni veya cihazınızın kapsayıcı depolama alanını kullanarak veri depolamanın yanı sıra, özellikle çevrimdışı çalışırken daha iyi bir güvenilirlik sağlamak için konak IoT Edge cihazının kendisini de ayırabilirsiniz.

## <a name="link-module-storage-to-device-storage"></a>Modül depolama alanını cihaz depolamaya bağlama

Modül depolamadaki bir bağlantıyı konak sistemindeki depolamaya etkinleştirmek için modülünüzün kapsayıcıda bir depolama klasörünü işaret eden bir ortam değişkeni oluşturun. Ardından, bu depolama klasörünü konak makinesindeki bir klasöre bağlamak için oluşturma seçeneklerini kullanın.

Örneğin, IoT Edge hub 'ını cihazınızın yerel depolamadaki iletileri depolamak ve daha sonra almak için etkinleştirmek istiyorsanız, ortam değişkenlerini ve oluşturma seçeneklerini **çalışma zamanı ayarları** bölümünde Azure portal yapılandırabilirsiniz.

1. IoT Edge hub ve IoT Edge Aracısı için, modüldeki bir dizini işaret eden **StorageFolder** adlı bir ortam değişkeni ekleyin.
1. IoT Edge hub ve IoT Edge Aracısı için, konak makinedeki yerel bir dizini modüldeki bir dizine bağlamak üzere bağlamalar ekleyin. Örnek:

   ![Yerel depolama için oluşturma seçenekleri ve ortam değişkenleri ekleme](./media/how-to-access-host-storage-from-module/offline-storage.png)

Ya da, yerel depolamayı doğrudan dağıtım bildiriminde yapılandırabilirsiniz. Örnek:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

`<HostStoragePath>`Ve ' i `<ModuleStoragePath>` konak ve modüllü depolama yolunuza göre değiştirin; her iki değer de mutlak bir yol olmalıdır.

Örneğin, bir Linux sisteminde, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` ana bilgisayar sisteminizdeki **/etc/ıotedge/Storage** dizini, kapsayıcıda **/iotedge/Storage/** dizinine eşlenmişse anlamına gelir. Bir Windows sisteminde, başka bir örnek olarak, `"Binds":["C:\\temp:C:\\contemp"]` ana bilgisayar sisteminizdeki **c: \\ Temp** dizininin, kapsayıcıda **c: \\ Contemp** dizinine eşlendiği anlamına gelir.

Ayrıca, Linux cihazlarda modülünüzün Kullanıcı profilinin konak sistem dizini için gereken okuma, yazma ve yürütme izinlerine sahip olduğundan emin olun. Cihazınızın yerel depolamasına iletileri depolamak üzere IoT Edge hub 'ı etkinleştirmeye yönelik daha önceki örneğe dönerek, Kullanıcı profili, UID 1000 için izin vermeniz gerekir. (IoT Edge Aracısı kök olarak çalışır, bu nedenle ek izin gerektirmez.) Linux sistemlerinde dizin izinlerini yönetmenin birkaç yolu vardır `chown` . Bu, Dizin sahibini değiştirmek ve sonra `chmod` izinleri değiştirmek için:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

[Docker belgelerinden](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)oluşturma seçenekleri hakkında daha fazla ayrıntı bulabilirsiniz.

## <a name="encrypted-data-in-module-storage"></a>Modül depolamadaki şifrelenmiş veriler

Modüller, verileri şifrelemek için IoT Edge Daemon iş yükü API 'sini çağırdıkça, şifreleme anahtarı modül KIMLIĞI ve modülün oluşturma KIMLIĞI kullanılarak türetilir. Oluşturma KIMLIĞI, bir modülün dağıtımdan kaldırılması ve ardından aynı modül KIMLIĞINE sahip başka bir modülün aynı cihaza dağıtılması halinde gizli dizileri korumak için kullanılır. Bir modülün oluşturma kimliğini, [az IoT Hub Module-Identity Show](/cli/azure/iot/hub/module-identity)Azure CLI komutunu kullanarak görüntüleyebilirsiniz.

Dosyalar arasında modüller arasında dosya paylaşmak istiyorsanız, bunların gizli dizileri içermemesi gerekir, aksi takdirde şifre çözülemez.

## <a name="next-steps"></a>Sonraki adımlar

Bir modülden konak depolamaya erişme hakkında ek bir örnek için, bkz. [IoT Edge Azure Blob Storage ile verileri kenarda depolama](how-to-store-data-blob.md).
