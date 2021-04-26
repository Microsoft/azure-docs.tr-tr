---
title: Öğretici-Azure IoT Central 'da bir video analizi IoT Edge örneği oluşturma (Linux VM)
description: Bu öğreticide, bir Linux sanal makinesinde video analizi-nesne ve hareket algılama uygulama şablonuyla kullanılacak bir video analizi IoT Edge örneği oluşturma işleminin nasıl yapılacağı gösterilmektedir.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 10ddbf3dde62380eb79af685ad41b22e4552cea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832632"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>Öğretici: video analizi için IoT Edge örneği oluşturma (Linux VM)

Azure IoT Edge, bulut zekasını dağıtarak ve çalıştırarak yerel olarak sunan tam olarak yönetilen bir hizmettir:

* Özel mantık
* Azure hizmetleri
* Yapay zeka

IoT Edge, bu hizmetler doğrudan platformlar arası IoT cihazlarında çalışır. böylece IoT çözümünüzü bulutta veya çevrimdışı ortamda güvenli bir şekilde çalıştırmanızı sağlar.

Bu öğreticide bir Azure VM 'de IoT Edge cihazının nasıl hazırlanacağı gösterilmektedir. IoT Edge örnek, Azure IoT Central video analizi-nesne ve hareket algılama uygulama şablonunun kullandığı canlı video analizi modüllerini çalıştırır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Azure IoT Edge çalışma zamanı yüklü bir Azure VM oluşturma
> * Canlı video analizi modülünü barındırmak ve IoT Central bağlanmak için IoT Edge yüklemesini hazırlayın

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, Azure IoT Central bir önceki [video analizi uygulaması oluşturma](./tutorial-video-analytics-create-app-yolo-v3.md) veya [Azure IoT Central (openvino &trade; ) öğreticisinde bir video analizi oluşturma](tutorial-video-analytics-create-app-openvino.md) işlemini tamamlamalısınız.

Ayrıca bir Azure aboneliğine de ihtiyacınız vardır. Azure aboneliğiniz yoksa, [Azure kaydolma sayfasında](https://aka.ms/createazuresubscription)ücretsiz olarak bir tane oluşturabilirsiniz.

## <a name="deploy-azure-iot-edge"></a>Azure IoT Edge dağıt

En son IoT Edge çalışma zamanı ve canlı video analizi modülleri yüklü bir Azure VM oluşturmak için aşağıdaki düğmeyi seçin:

[![iotedge-vm-deploy için Azure Düğmesini dağıtma](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

**Özel dağıtım** formunu gerçekleştirmek için aşağıdaki tablodaki bilgileri kullanın:

| Alan | Değer |
| ----- | ----- |
| Abonelik | Azure aboneliğinizi seçin. |
| Kaynak grubu | *LVA-RG* -önceki öğreticide oluşturduğunuz kaynak grubu. |
| Region       | *Doğu ABD* |
| DNS etiketi öneki | VM için benzersiz bir DNS ön eki seçin. Tüm alfabetik karakterler, basamaklar veya özel karakterler olmalıdır. |
| Yönetici Kullanıcı adı | *AzureUser* |
| Yönetici Parolası | Bir parola girin. *scratchpad.txt* dosyadaki parolayı bir yere göz önünde, daha sonra kullanacaksınız. |
| Kapsam Kimliği | **Kapsam kimliği** , ağ geçidi cihazını eklediğinizde önceki öğreticideki *scratchpad.txt* dosyasında bir Note yaptık. |
| Cihaz Kimliği | *ağ geçidi-001* -önceki öğreticide oluşturduğunuz ağ geçidi cihazı. |
| Cihaz anahtarı | **Cihaz birincil anahtarı** , ağ geçidi cihazını eklediğinizde önceki öğreticideki *scratchpad.txt* dosyasında bir Note. |
| IoT Merkezi uygulama konağı | Önceki öğreticideki *scratchpad.txt* dosyasında bir Note oluşturduğunuz **Uygulama URL 'si** . Örneğin, *Traders.azureiotcentral.com*. |
| IoT Merkezi uygulaması API belirteci | **IŞLEÇ API belirteci** , önceki öğreticideki *scratchpad.txt* dosyasında bir Note yaptınız. |
| IoT merkezi cihaz sağlama anahtarı | **SAS-IoT-Devices grubu birincil anahtarı** , önceki öğreticideki *scratchpad.txt* dosyasında bir Note yaptık. |
| VM Boyutu | *Standard_DS1_v2* |
| Ubuntu işletim sistemi sürümü | *18,04-LTS* |
| Konum | *[resourceGroup (). Location]* |

**Gözden geçir ve oluştur**’u seçin. Doğrulama tamamlandığında **Oluştur**' u seçin. Dağıtımın tamamlanabilmesi için genellikle üç dakika sürer. Dağıtım tamamlandığında, Azure portal **LVA-RG** kaynak grubuna gidin.

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>IoT Edge çalışma zamanının modülleri yüklediğini emin olun

Azure portal, **LVA-RG** kaynak grubuna gidin ve sanal makineyi seçin. Ardından, **destek + sorun giderme** bölümünde **seri konsol**' yi seçin.

Bir istem almak için **ENTER** tuşuna basın `login:` . Sanal makineyi oluştururken seçtiğiniz Kullanıcı adı ve parola olarak *AzureUser* kullanın.

IoT Edge çalışma zamanının sürümünü denetlemek için aşağıdaki komutu çalıştırın. Yazma sırasında sürüm 1.0.9 ' dir:

```bash
sudo iotedge --version
```

Şu komutu kullanarak IoT Edge modüllerinizi listeleyin:

```bash
sudo iotedge list
```

Dağıtım aşağıdaki beş IoT Edge modülünü çalışacak şekilde yapılandırdı:

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

Dağıtım, canlı video analizi için gerekli modüllerle özel bir IoT Edge ortamı oluşturdu. Dağıtım, IoT Edge çalışma zamanının IoT Central bağlanmak için IoT cihaz sağlama hizmeti ' ni kullandığından emin olmak için varsayılan **config. YAML** 'yi güncelleştirmiş. Dağıtım Ayrıca, modüllerde ek yapılandırma verileri sağlamak için **/Data/Storage** klasöründe **state.js** adlı bir dosya oluşturmuştur. Daha fazla bilgi için bkz. [video analizi için IoT Edge örneği oluşturma (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md) öğreticisi.

IoT Edge cihazda sorun gidermek için, bkz. [IoT Edge cihazınızda sorun giderme](../../iot-edge/troubleshoot.md)

## <a name="use-the-rtsp-simulator"></a>RTSP simülatörü kullanma

IoT Edge cihazınıza bağlanacak gerçek kamera cihazları yoksa, video analizi uygulama şablonundaki iki sanal kamera cihazını kullanabilirsiniz. Bu bölümde, IoT Edge cihazınızda sanal bir video akışının nasıl kullanılacağı gösterilmektedir.

Bu yönergeler, [Live555 medya sunucusunu](http://www.live555.com/mediaServer/) bir Docker KAPSAYıCıSıNDA bir RTSP simülatör olarak kullanır.

> [!NOTE]
> Bu depodaki üçüncü taraf yazılımlara yapılan başvurular yalnızca bilgilendirme ve kolaylık amaçlıdır. Microsoft, üçüncü taraf yazılım için haklar sağlamaz ve bu haklara sahip değildir. Daha fazla bilgi için bkz. [Live555 Media Server](http://www.live555.com/mediaServer/).

IoT Edge sanal makinenizin bir Docker kapsayıcısında **rtspvideo** yardımcı programını çalıştırmak için aşağıdaki komutu kullanın. Docker kapsayıcısı, bir arka plan RTSP akışı oluşturur:

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

Docker kapsayıcılarını listelemek için aşağıdaki komutu kullanın:

```bash
sudo docker ps
```

Liste, **live555** adlı bir kapsayıcı içerir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulamayı tamamladıysanız, oluşturduğunuz tüm kaynakları aşağıdaki şekilde kaldırabilirsiniz:

1. IoT Central uygulamasında, **Yönetim** bölümünde **uygulamanızın** sayfasına gidin. Ardından **Sil**’i seçin.
1. Azure portal, **LVA-RG** kaynak grubunu silin.
1. Yerel makinenizde **amp-Viewer** Docker kapsayıcısını durdurun.

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure 'da çalışan bir Linux sanal makinesinde IoT Edge çalışma zamanı, LVA modülleri ve Live555 simülasyon akışını dağıttınız.

Kameraları yönetmek için sonraki öğreticiye uyun

> [!div class="nextstepaction"]
> [Video analizlerini izleme ve yönetme-nesne ve hareket algılama uygulaması](./tutorial-video-analytics-manage.md)