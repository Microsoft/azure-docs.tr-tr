---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: e6ca830a7224326c17003924b76192b95ba6bcb2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152024"
---
## <a name="clean-up-resources"></a>Kaynakları temizleme

Ek IoT Tak ve Kullan makaleleriyle devam etmeyi planlıyorsanız, bu hızlı başlangıçta kullandığınız kaynakları tutabilir ve yeniden kullanabilirsiniz. Aksi takdirde, ek ücretlerden kaçınmak için bu hızlı başlangıçta oluşturduğunuz kaynakları silebilirsiniz.

Tüm kaynak grubunu Azure CLı için aşağıdaki komutla silerek hem hub hem de kayıtlı cihazı bir kerede silebilirsiniz. (Ancak, bu kaynaklar farklı amaçlar için sahip olduğunuz diğer kaynaklarla bir kaynak grubu paylaştığını bu şekilde kullanmayın.)

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

Ayrıca, klonlanan SDK dosyalarını geliştirme makinenizden da kaldırmak isteyebilirsiniz.