---
title: Microsoft OPC yayımcısını dağıtma
description: Bu öğreticide OPC yayımcısını tek başına modunda dağıtmayı öğreneceksiniz.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: c82d15541459b5b482e427fc707b92755aa02c6c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787753"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>Öğretici: OPC yayımcısını dağıtma

OPC Publisher, açık olarak geliştirilen ve endüstriyel varlıklar ve Microsoft Azure bulutu arasındaki boşluğu bağlayan, tam olarak desteklenen bir Microsoft ürünüdür. Bu, OPC UA özellikli varlıklara veya endüstriyel bağlantı yazılımına bağlanarak telemetri verilerini IEC62541 OPC UA PubSub standart biçimi (sürüm 2,6 ' den başlayarak) gibi çeşitli biçimlerde [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) yayımlar.

Modül olarak [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) veya bir kapsayıcı olarak düz Docker üzerinde çalışır. [.Net platformlar arası çalışma zamanının](https://docs.microsoft.com/dotnet/core/introduction)kullandığından, Linux ve Windows 10 ' da yerel olarak da çalışır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * OPC yayımcısını dağıtma
> * OPC yayımcısı 'nın en son yayınlanan sürümünü kapsayıcı olarak çalıştır
> * Azure portal kapsayıcı oluşturma seçeneklerini belirtin

Azure aboneliğiniz yoksa ücretsiz bir deneme hesabı oluşturun

## <a name="prerequisites"></a>Önkoşullar

- Bir IoT Hub oluşturulması gerekir
- IoT Edge bir cihazın oluşturulması gerekir

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>Azure Marketi 'nden OPC yayımcısını dağıtma

1. Kullanılacak Azure aboneliğini seçin. Kullanılabilir bir Azure aboneliği yoksa, bir tane oluşturulması gerekir.
2. OPC yayımcısının veri göndermesi beklenen IoT Hub seçin. IoT Hub yoksa, birinin oluşturulması gerekir.
3. OPC yayımcısının üzerinde çalışması gereken IoT Edge cihazı seçin (veya oluşturulacak yeni bir IoT Edge cihaz için bir ad girin).
4. Oluştur’a tıklayın. Seçili IoT Edge cihazının "cihazdaki modülleri ayarla" sayfası açılır.
5. OPC yayımcısının "güncelleştirme IoT Edge modülünü" sayfasını açmak için "OPCPublisher" düğmesine tıklayın ve ardından "kapsayıcı oluşturma seçenekleri" ni seçin.
6. OPC yayımcısı kullanımınızı temel alan ek kapsayıcı oluşturma seçeneklerini belirtin, aşağıdaki sonraki bölüme bakın.


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>OPC yayımcısı için Microsoft Container Registry Docker kapsayıcılarına el ile erişme

OPC yayımcısı 'nın en son yayınlanan sürümü ile el ile çalıştırılabilir:

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

Burada "ad" kapsayıcının adıdır.

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Azure portal kapsayıcı oluşturma seçeneklerini belirtme
OPC yayımcısı Azure portal aracılığıyla dağıtıldığında, OPC yayımcısının güncelleştirme IoT Edge modülü sayfasında kapsayıcı oluşturma seçenekleri belirtilebilir. Bu oluşturma seçenekleri JSON biçiminde olmalıdır. OPC yayımcısı komut satırı bağımsız değişkenleri cmd anahtarı aracılığıyla belirtilebilir, ör.:
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

OPC yayımcısı için tipik bir IoT Edge modülü kapsayıcısı kümesi oluşturma seçenekleri şunlardır:
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

Bu seçenekler belirtildiğinde OPC yayımcısı yapılandırma dosyasını okur `./pn.json` . OPC yayımcısının çalışma dizini `/appdata` Başlangıç aşamasında olarak ayarlanır ve bu nedenle OPC yayımcısı dosyayı `/appdata/pn.json` Docker kapsayıcısının içinde okur. OPC yayımcısının günlük dosyası öğesine yazılır `/appdata` ve `CertificateStores` Bu dizinde (OPC UA sertifikaları için kullanılan) dizin de oluşturulacaktır. Bu dosyaları IoT Edge ana bilgisayar dosya sisteminde kullanılabilir hale getirmek için, kapsayıcı yapılandırması bir bağlama bağlama birimi gerektirir. `/iiotedge:/appdata`Bağlama, dizini `/appdata` `/iiotedge` (yoksa IoT Edge çalışma zamanı tarafından oluşturulacak) ana bilgisayar diziniyle eşler.
**Bu bağlama bağlama birimi olmadan, kapsayıcı yeniden başlatıldığında tüm OPC yayımcısı yapılandırma dosyaları kaybedilir.**

Ağ üzerinde yapılandırılmış bir DNS sunucusu olmadan ana bilgisayar adını kullanan OPC UA sunucusuna bağlantı, bölüme bir giriş eklenerek elde edilebilir `ExtraHosts` `HostConfig` :

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>Sonraki adımlar 
OPC Publisher Edge modülünü dağıttığınıza göre, bir sonraki adım bunu yapılandırmaktır:

> [!div class="nextstepaction"]
> [OPC yayımcısını yapılandırma](tutorial-publisher-configure-opc-publisher.md)