---
title: IoT Edge IoT güvenlik modülü için Azure Güvenlik Merkezi 'ni anlama | Microsoft Docs
description: IoT Edge için IoT güvenlik modülü için Azure Güvenlik Merkezi 'nin mimarisini ve yeteneklerini anlayın.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 148c68234a937efde554ef00a6014cdc1a350f34
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315900"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge güvenlik modülü

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) kenarda iş akışlarını yönetmek ve gerçekleştirmek için güçlü özellikler sunar.
IoT ortamlarında IoT Edge oynadığı anahtar bölüm, kötü amaçlı aktörler için özellikle çekici hale gelir.

IoT güvenlik modülü için Azure Güvenlik Merkezi, IoT Edge cihazlarınız için kapsamlı bir güvenlik çözümü sağlar.
IoT modülü için Azure Güvenlik Merkezi, Işletim sistemi ve kapsayıcı sisteminizdeki ham güvenlik verilerini, uygulanabilir güvenlik önerileri ve uyarılara toplar, toplar ve analiz eder.

IoT cihazlarına yönelik IoT güvenlik aracıları için Azure Güvenlik Merkezi 'ne benzer şekilde, IoT Edge modülü için Azure Güvenlik Merkezi, ikizi modülü aracılığıyla yüksek düzeyde özelleştirilebilir.
Daha fazla bilgi için bkz. [aracınızı yapılandırma](how-to-agent-configuration.md) .

IoT Edge IoT güvenlik modülü için Azure Güvenlik Merkezi aşağıdaki özellikleri sunmaktadır:

- Temel Işletim sistemi (Linux) ve IoT Edge kapsayıcı sistemlerinden ham güvenlik olayları toplar.
  
  Kullanılabilir güvenlik veri toplayıcıları hakkında daha fazla bilgi edinmek için bkz. [IoT Aracısı yapılandırması Için Azure Güvenlik Merkezi](how-to-agent-configuration.md) .

- IoT Edge dağıtım bildirimlerinin analizi.

- [IoT Edge hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)aracılığıyla gönderilen iletilere ham güvenlik olayları toplar.

- İkizi güvenlik modülünün kullanımıyla yapılandırmayı kaldırın.

  Daha fazla bilgi edinmek için bkz. [IoT Aracısı Için Azure Güvenlik Merkezi 'Ni yapılandırma](how-to-agent-configuration.md) .

IoT Edge için Azure Güvenlik Merkezi, IoT Edge altında ayrıcalıklı modda çalışır.
Modülün Işletim sistemini ve diğer IoT Edge modüllerini izlemesine izin vermek için ayrıcalıklı mod gerekir.

## <a name="module-supported-platforms"></a>Modül desteklenen platformlar

IoT Edge için Azure Güvenlik Merkezi for IoT güvenlik modülü şu anda yalnızca Linux 'ta kullanılabilir. 

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, IoT Edge için IoT güvenlik modülü için Azure Güvenlik Merkezi 'nin mimarisi ve özellikleri hakkında bilgi edindiniz.

IoT dağıtımı için Azure Güvenlik Merkezi 'ni kullanmaya devam etmek için aşağıdaki makaleleri kullanın:

- [IoT Edge için güvenlik modülünü](how-to-deploy-edge.md) dağıtma
- [Güvenlik modülünüzü yapılandırmayı](how-to-agent-configuration.md) öğrenin
- IoT [hizmeti önkoşulları](service-prerequisites.md) Için Azure Güvenlik Merkezi 'ni gözden geçirin
- [IoT Hub IoT hizmeti Için Azure Güvenlik Merkezi 'ni nasıl etkinleştirebileceğinizi](quickstart-onboard-iot-hub.md) öğrenin
- [IoT Için Azure Güvenlik Merkezi](resources-frequently-asked-questions.md) 'nden hizmet hakkında daha fazla bilgi edinin