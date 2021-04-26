---
title: Modüller için createOptions yazma-Azure IoT Edge | Microsoft Docs
description: Çalışma zamanında modülleri yapılandırmak için dağıtım bildiriminde createOptions kullanma
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ee5536562eb3f2008908a36ff296ef2cfa337ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200615"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>IoT Edge modülleri için kapsayıcı oluşturma seçeneklerini yapılandırma

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Dağıtım bildirimindeki **createOptions** parametresi, çalışma zamanında modül kapsayıcılarını yapılandırmanızı sağlar. Bu parametre, denetim modüllerini modüller üzerinde genişletir ve modülün konak cihazının kaynaklarına erişimine izin verme veya kısıtlama veya ağı yapılandırma gibi görevler için izin verir.

IoT Edge modüller, IoT Edge cihazınızda Docker uyumlu kapsayıcılar olarak uygulanır. Docker kapsayıcı oluşturmaya yönelik birçok seçenek sunar ve bu seçenekler IoT Edge modüller için de geçerlidir. Daha fazla bilgi için bkz. [Docker kapsayıcı oluşturma seçenekleri](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Biçim oluşturma seçenekleri

IoT Edge dağıtım bildirimi JSON olarak biçimlendirilen oluşturma seçeneklerini kabul eder. Örneğin, her edgeHub modülü için otomatik olarak eklenen oluşturma seçeneklerini alın:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

Bu edgeHub örneği, kapsayıcıda gösterilen bağlantı noktalarını konak cihazdaki bir bağlantı noktasına eşlemek için **Hostconfig. PortBindings** parametresini kullanır.

Visual Studio için Azure IoT Araçları uzantıları 'nı veya Visual Studio Code kullanıyorsanız, oluşturma seçeneklerini **deployment.template.js** dosyadaki JSON biçiminde yazabilirsiniz. Daha sonra, IoT Edge çözümünü derlemek veya dağıtım bildirimini oluşturmak için uzantıyı kullandığınızda, bu, JSON 'u, IoT Edge çalışma zamanının beklediği biçimde, sizin için bir dize olarak oluşturur. Örnek:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Oluşturma seçeneklerini yazmak için bir ipucu, `docker inspect` komutunu kullanmaktır. Geliştirme işleminizin bir parçası olarak, kullanarak modülü yerel olarak çalıştırın `docker run <container name>` . Modülün istediğiniz şekilde çalışmasını istediğinizde, öğesini çalıştırın `docker inspect <container name>` . Bu komut modül ayrıntılarını JSON biçiminde verir. Yapılandırdığınız parametreleri bulun ve JSON ' ı kopyalayın. Örnek:

[![Docker İnceleme edgeHub sonuçları](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Genel senaryolar

Kapsayıcı oluşturma seçenekleri birçok senaryoyu etkinleştirir, ancak IoT Edge çözüm oluştururken en sık kullanılan bazı bazıları aşağıda verilmiştir:

* [Modüller konak depolamaya erişim izni verme](how-to-access-host-storage-from-module.md)
* [Ana bilgisayar bağlantı noktasını modül bağlantı noktasına eşle](#map-host-port-to-module-port)
* [Modül belleğini ve CPU kullanımını kısıtla](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Ana bilgisayar bağlantı noktasını modül bağlantı noktasına eşle

Modülünüzün IoT Edge çözümü dışındaki bir hizmetle iletişim kurması gerekiyorsa ve bunu yapmak için ileti yönlendirmeyi kullanmıyorsanız, bir ana bilgisayar bağlantı noktasını modül bağlantı noktasıyla eşlemeniz gerekir.

>[!TIP]
>Aynı cihazdaki modül-modül iletişimi için bu bağlantı noktası eşlemesi gerekli değildir. A modülünün B modülünde barındırılan bir API sorgulaması gerekiyorsa, bağlantı noktası eşleştirmesi olmadan bunu yapabilir. B modülünün dockerfile içinde bir bağlantı noktasını kullanıma sunması gerekir, örneğin: `EXPOSE 8080` . Ardından A modülü, Modül B 'nin adını kullanarak API 'YI sorgulayabilir, örneğin: `http://ModuleB:8080/api` .

İlk olarak, modül içindeki bir bağlantı noktasının bağlantıları dinlemek için açık olduğundan emin olun. Bunu, dockerfile içindeki bir [sergileme](https://docs.docker.com/engine/reference/builder/#expose) yönergesini kullanarak yapabilirsiniz. Örneğin, `EXPOSE 8080`. Gösterme yönergesi varsayılan olarak TCP protokolüne göre belirlenir veya UDP 'yi belirtebilirsiniz.

Ardından, modüldeki açığa çıkarılan bağlantı noktasını konak cihazdaki bir bağlantı noktasıyla eşlemek için [Docker kapsayıcısının](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) **Hostconfig** grubundaki **portbindings** ayarını kullanın. Örneğin, modül içinde 8080 numaralı bağlantı noktasını açığa çıkardıysanız ve bunu konak cihazının 80 numaralı bağlantı noktasıyla eşlemek istiyorsanız, dosyadaki template.jsoluşturma seçenekleri aşağıdaki örnekteki gibi görünür:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Dağıtım bildirimi için strıningolduktan sonra, aynı yapılandırma aşağıdaki örneğe benzer şekilde görünür:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Modül belleğini ve CPU kullanımını kısıtla

Bir modülün ne kadar ana bilgisayar kaynaklarını kullanabileceğinizi bildirebilirsiniz. Bu denetim, bir modülün çok fazla bellek veya CPU kullanımı tüketmemesini sağlamak ve cihazda diğer işlemlerin çalıştırılmasını engellemek için yararlıdır. Bu ayarları, aşağıdaki gibi, **Hostconfig** grubundaki [Docker kapsayıcı oluşturma seçenekleriyle](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) yönetebilirsiniz:

* **Bellek**: bayt cinsinden bellek sınırı. Örneğin, 268435456 bayt = 256 MB.
* **Memoryswap**: toplam bellek sınırı (bellek + takas). Örneğin, 536870912 bayt = 512 MB
* **Cpuperiod**: CPU süresinin mikrosaniye cinsinden uzunluğu. Varsayılan değer 100000 ' dir; Örneğin, 25000 değeri, bir kapsayıcıyı CPU kaynaklarının %25 ' i ile sınırlandırır.

template.jsbiçiminde, bu değerler aşağıdaki örnekteki gibi görünür:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Son dağıtım bildirimi için, bu değerler aşağıdaki örnekteki gibi görünür:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Sonraki adımlar

Eylemde oluşturma seçenekleri hakkında daha fazla örnek için aşağıdaki IoT Edge örneklere bakın:

* [Raspberry Pi 3 üzerinde Özel Görüntü İşleme ve Azure IoT Edge](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT Edge BLOB depolama örneği](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
