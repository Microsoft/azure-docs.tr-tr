---
title: Azure endüstriyel IoT 'ye Genel Bakış | Microsoft Docs
description: Bu makalede endüstriyel IoT 'ye genel bakış sunulmaktadır. IIoT 'deki bağlı fabrika, fabrika tabanı bağlantısı ve güvenlik bileşenlerini açıklar.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3df10c9d7630e9db76994e8e508f30adb986e0d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105647051"
---
# <a name="what-is-industrial-iot-iiot"></a>Endüstriyel IoT nedir (IIoT)

> [!IMPORTANT]
> Bu makaleyi güncelleştirdiğimiz sürece, en güncel içerik için bkz. [Azure endüstriyel IoT](https://azure.github.io/Industrial-IoT/) .

IIoT, endüstriyel Nesnelerin İnterneti. IIoT, üretim sektöründe IoT uygulaması aracılığıyla endüstriyel verimlilik düzeyini geliştirir. 

## <a name="improve-industrial-efficiencies"></a>Endüstriyel verimliliği artırmak

Bağlı bir fabrika çözümü hızlandırıcısı ile operasyonel üretkenliğinizi ve karlılığını geliştirin. Fabrika katında zaten çalışan makineleriniz dahil olmak üzere, bulutta endüstriyel ekipman ve cihazlarınızı bağlayın ve izleyin. Tüm fabrika katının performansını artırmanıza yardımcı olan Öngörüler için IoT Verilerinizi çözümleyin.

OPC Ikizi ile fabrika katı makinelere erişmenin zaman alan sürecini azaltın ve IIoT çözümleri oluşturmaya zamandan odaklayın. OPC kasası ile sertifika yönetimi ve endüstriyel varlık tümleştirmesini kolaylaştırın ve varlık bağlantısının güvenli hale getirilmesinden emin olabilirsiniz. Bu mikro hizmetler, [Azure endüstriyel IoT bileşenlerinin](https://github.com/Azure/Industrial-IoT)üzerine REST API 'si sağlar. Hizmet API 'SI, Edge modülü işlevlerini denetlemenize olanak tanır. 

![Endüstriyel IoT’ye genel bakış](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Azure endüstriyel IoT hizmetleri hakkında daha fazla bilgi için bkz. GitHub [deposu](https://github.com/Azure/Industrial-IoT) ve [belgeleri](https://azure.github.io/Industrial-IoT/).
Azure IoT Edge modüllerinin nasıl çalıştığını bilmediğiniz bir sorun yaşıyorsanız, aşağıdaki makalelerle başlayın:
- [Azure IoT Edge hakkında](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge modüller](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Bağlı fabrika

[Bağlı fabrika](../iot-accelerators/iot-accelerators-connected-factory-features.md) , Microsoft 'un belirli iş gereksinimlerini karşılayacak şekilde özelleştirilebilen Azure endüstriyel IoT başvuru mimarisinin bir uygulamasıdır. Tam çözüm kodu açık kaynaktır ve bağlı Factory Çözüm Hızlandırıcısı GitHub deposunda kullanılabilir. Bunu ticari bir ürün için başlangıç noktası olarak kullanabilir ve Azure aboneliğinize dakikalar içinde önceden oluşturulmuş bir çözüm dağıtabilirsiniz. 

## <a name="factory-floor-connectivity"></a>Fabrika tabanı bağlantısı

OPC Ikizi, cihaz bulmayı ve kaydını otomatikleştiren ve REST API 'Ler aracılığıyla endüstriyel cihazların uzaktan denetimini sunan bir IIoT bileşenidir. OPC Ikizi, bulutu ve fabrika ağını bağlamak için Azure IoT Edge ve IoT Hub kullanır. OPC Ikizi, IIoT geliştiricilerinin şirket içi makinelere güvenli bir şekilde erişme konusunda endişelenmeden IIoT uygulamalarına odaklanmasına olanak tanır.

## <a name="security"></a>Güvenlik

OPC Kasası, buluttaki OPC UA sunucusu ve istemci uygulamaları için sertifika yaşam döngüsünü yapılandırabileceğiniz, kaydedebildiğiniz ve yönetebilen OPC UA küresel bulma sunucusu 'nun (GDS) bir uygulamasıdır. OPC Kasası, endüstriyel alanda güvenli varlık bağlantısının uygulanmasını ve bakımını kolaylaştırır. Sertifika yönetimini otomatikleştirerek OPC Kasası, bağlantı ve sertifika yönetimiyle ilişkili el ile ve karmaşık işlemlerden fabrika işleçlerini serbest bırakır.

## <a name="next-steps"></a>Sonraki adımlar

Artık endüstriyel IoT ve bileşenlerine giriş yapmış olduğunuza göre, önerilen sonraki adım aşağıda verilmiştir:

[OPC Twin nedir?](overview-opc-twin.md)
