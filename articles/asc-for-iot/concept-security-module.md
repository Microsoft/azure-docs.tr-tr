---
title: IoT güvenlik modülü TWINS için Azure Güvenlik Merkezi 'Ni anlama | Microsoft Docs
description: Güvenlik modülü TWINS kavramı ve IoT için Azure Güvenlik Merkezi 'nde nasıl kullanıldıkları hakkında bilgi edinin.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: ab3b6e740e644a1ed1495eb776045888be448047
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596506"
---
# <a name="security-module"></a>Güvenlik modülü


Bu makalede IoT için Azure Güvenlik Merkezi 'nin cihaz ikleri ve modülleri nasıl kullandığı açıklanır. 

## <a name="device-twins"></a>Cihaz ikikesi

Azure 'da oluşturulan IoT çözümleri için, cihaz ikis, hem cihaz yönetimi hem de süreç otomasyonu 'nda anahtar rol oynar.  

IoT için Azure Güvenlik Merkezi, mevcut IoT cihaz yönetimi Platformunuzla tam tümleştirme sunarak, cihaz güvenlik durumunuzu yönetmenize ve mevcut cihaz denetimi yeteneklerini kullanmanıza olanak sağlar. Tümleştirme, IoT Hub ikizi mekanizması kullanılarak elde edilir.  

Azure IoT Hub 'de [cihaz](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) ikseponları kavramı hakkında daha fazla bilgi edinin. 

## <a name="security-module-twins"></a>Güvenlik modülü TWINS

IoT için Azure Güvenlik Merkezi, hizmette bulunan her cihaz için bir güvenlik modülü ikizi tutar.
İkizi güvenlik modülü, çözümünüzde her belirli cihaz için cihaz güvenliğiyle ilgili tüm bilgileri tutar.
Cihaz güvenlik özellikleri, daha güvenli iletişim için ikizi ve daha az kaynak gerektiren güncelleştirmeleri ve bakımı etkinleştirmek üzere özel bir güvenlik modülünde saklanır.  

İkizi oluşturmayı, özelleştirmeyi ve yapılandırmayı öğrenmek için bkz. [Güvenlik modülü oluşturma ikizi](quickstart-create-security-twin.md) ve [güvenlik aracılarını yapılandırma](how-to-agent-configuration.md) . IoT Hub modül TWINS kavramı hakkında daha fazla bilgi edinmek için bkz. [Modül TWINS 'ı anlama](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) . 
 

## <a name="see-also"></a>Ayrıca bkz.
- [IoT için Azure Güvenlik Merkezi 'ne genel bakış](overview.md)
- [Güvenlik aracılarını dağıtma](how-to-deploy-agent.md)
- [Güvenlik Aracısı kimlik doğrulama yöntemleri](concept-security-agent-authentication-methods.md)