---
title: Kubernetes 'e IoT Edge yüklemesi | Microsoft Docs
description: Yerel bir geliştirme kümesi ortamı kullanarak Kubernetes 'e IoT Edge nasıl yükleneceğinizi öğrenin
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a453779ffe4ae20acf55510d0ac9f9483763af21
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964828"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Kubernetes 'e IoT Edge nasıl yüklenir (Önizleme)

IoT Edge, Kubernetes ile, dayanıklı ve yüksek oranda kullanılabilir bir altyapı katmanı olarak yararlanarak tümleştirilebilir. Kubernetes API sunucusuyla IoT Edge *özel bir kaynak tanımı* (CRD) kaydeder. Ayrıca, bulut tarafından yönetilen istenen durumu yerel küme durumuyla bağdaştıran bir *operatör* (IoT Edge Aracısı) sağlar. 

Modül ömrü, modül kullanılabilirliğini tutan ve yerleştirmesini seçen Kubernetes Zamanlayıcı tarafından yönetilir. IoT Edge, en üstte çalışan Edge uygulaması platformunu yönetir ve IoT Hub belirtilen istenen durumu, uç kümedeki durumuyla sürekli olarak mutabık kılma. Edge uygulama modeli, IoT Edge modülleri ve yolları temel alan tanıdık modeldir. IoT Edge Agent işleci, pods, dağıtımlar, hizmetler vb. Kubernetes 'in küller yapılarına *Otomatik* çeviri gerçekleştirir.

Üst düzey bir mimari diyagramı aşağıda verilmiştir:

![Kubernetes yay](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Edge dağıtımının her bileşeni, cihaza özel bir Kubernetes ad alanı kapsamına alınır ve aynı küme kaynaklarının birden çok uç cihaz ve dağıtımları arasında paylaşılmasını mümkün kılar.

>[!NOTE]
>Kubernetes üzerinde IoT Edge [genel önizlemede](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Hızlı test ortamı için yerel olarak yükler

### <a name="prerequisites"></a>Önkoşullar

* Kubernetes 1,10 veya sonraki bir sürümü. Var olan bir küme kurulumuna sahip değilseniz, [Minikube](https://kubernetes.io/docs/setup/minikube/) kullanarak yerel bir küme ortamı oluşturabilirsiniz. 

* Kubernetes paket yöneticisi olan [helk](https://helm.sh/docs/using_helm/#quickstart-guide).

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) , küme görüntüleme ve bunlarla etkileşim kurma.

### <a name="setup-steps"></a>Kurulum adımları

1. **Minikube** Başlat

    ``` shell
    minikube start
    ```

1. Kümenizdeki **Held** sunucu bileşenini (*Tiller*) başlatma

    ``` shell
    helm init
    ```

1. IoT Edge deposu ekleme ve Held yüklemesini güncelleştirme

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Bir IoT Hub oluşturun](../iot-hub/iot-hub-create-through-portal.md), [bir IoT Edge cihazı kaydedin](how-to-register-device.md)ve bağlantı dizesini aklınızda edin.

1. Kümelendirmek için ıtedşlı ve IoT Edge Aracısı 'nı yükler

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Kubernetes panosunu tarayıcıda açın

    ```shell
    minikube dashboard
    ```

    Küme ad alanları altında, IoT Edge cihaz için bir tane, *msıot-\<ıothub-name >-\<edgedevice-name >* kuralını izleyerek görürsünüz. IoT Edge Aracısı ve ııttedpods 'ler, bu ad alanında çalışır durumda olmalıdır.

1. Hızlı başlangıçta [Modül dağıtma](quickstart-linux.md#deploy-a-module) bölümündeki adımları kullanarak bir sanal sıcaklık algılayıcı modülü ekleyin. IoT Edge modül yönetimi, IoT Hub portalından, tıpkı diğer IoT Edge cihazlarından farklı olarak yapılır. Kubernetes araçları aracılığıyla modül yapılandırmasında yerel değişiklikler yapılması, üzerine yazıldıklarında önerilmez.

1. Birkaç saniye içinde, pano 'daki Edge cihazı ad alanı altındaki **Pod** sayfasının yenilenmesi, verileri IoT Hub IoT Edge merkezi Pod ile birlikte çalışan IoT Edge hub 'ını ve sanal algılayıcı benzetimini listelecektir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Edge dağıtımı tarafından oluşturulan tüm kaynakları kaldırmak için, önceki bölümün 5. adımında kullanılan adla aşağıdaki komutu kullanın.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Sonraki adımlar

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Yüksek oranda kullanılabilir uç ağ geçidi olarak dağıtın 

Bir Kubernetes kümesindeki uç cihaz, aşağı akış cihazları için IoT Ağ geçidi olarak kullanılabilir. Bu, uç dağıtımlar için yüksek kullanılabilirlik sağlayan düğüm hatasına dayanıklı olacak şekilde yapılandırılabilir. Bu senaryoda IoT Edge kullanmak için bu [ayrıntılı izlenecek yolu](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) inceleyin.