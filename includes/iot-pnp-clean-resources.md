---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 647226091c3e15a19bf8262c23e62d95018488b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831216"
---
Ek IoT Tak ve Kullan makaleleriyle devam etmeyi planlıyorsanız, bu makalede kullandığınız kaynakları tutabilir ve yeniden kullanabilirsiniz. Aksi takdirde, ek ücretlerden kaçınmak için bu makalede oluşturduğunuz kaynakları silebilirsiniz.

Aşağıdaki Azure CLı komutuyla tüm kaynak grubunu silerek hub ve kayıtlı cihazı aynı anda silebilirsiniz. Bu kaynaklar, tutmak istediğiniz diğer kaynaklarla bir kaynak grubunu paylaştığını bu komutu kullanmayın.

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

Yalnızca IoT Hub 'ını silmek için Azure CLı kullanarak aşağıdaki komutu çalıştırın:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Yalnızca IoT Hub 'ınıza kaydettiğiniz cihaz kimliğini silmek için Azure CLı kullanarak aşağıdaki komutu çalıştırın:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Ayrıca, kopyalanan örnek dosyaları geliştirme makinenizden da kaldırmak isteyebilirsiniz.
