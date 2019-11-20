---
title: IoT güvenlik Aracısı mimarisi için Azure Güvenlik Merkezi 'ni anlama | Microsoft Docs
description: IoT hizmeti için Azure Güvenlik Merkezi 'nde kullanılan aracılar için güvenlik Aracısı mimarisini anlayın.
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 998aeab197931a75579fc39b28e3a248b85fc57b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596915"
---
# <a name="security-agent-reference-architecture"></a>Güvenlik Aracısı başvuru mimarisi

IoT için Azure Güvenlik Merkezi, IoT Hub aracılığıyla güvenlik verilerini günlüğe kaydetmek, işlemek, toplamak ve göndermek için kullanılan güvenlik aracıları için başvuru mimarisi sağlar.

Güvenlik aracıları kısıtlanmış bir IoT ortamında çalışacak şekilde tasarlanmıştır ve kullandıkları kaynaklarla karşılaştırıldığında bunların sağladığı değer bakımından yüksek düzeyde özelleştirilebilir.

Güvenlik aracıları aşağıdaki özellikleri destekler:

- Temel Işletim sisteminden (Linux, Windows) ham güvenlik olayları toplayın. Kullanılabilir güvenlik veri toplayıcıları hakkında daha fazla bilgi edinmek için bkz. [IoT Aracısı yapılandırması Için Azure Güvenlik Merkezi](how-to-agent-configuration.md).

- Ham güvenlik olaylarını IoT Hub aracılığıyla gönderilen iletilere toplayın.

- Mevcut cihaz kimliğiyle veya ayrılmış bir modül kimliğiyle kimlik doğrulaması yapın. Daha fazla bilgi için bkz. [Güvenlik Aracısı kimlik doğrulama yöntemleri](concept-security-agent-authentication-methods.md) .

- **Azureiotsecurity** modülünün kullanımı üzerinden uzaktan yapılandırma ikizi. Daha fazla bilgi için bkz. [IoT Aracısı Için Azure Güvenlik Merkezi 'Ni yapılandırma](how-to-agent-configuration.md).

IoT güvenlik aracıları için Azure Güvenlik Merkezi, açık kaynaklı projeler olarak geliştirilmiştir ve GitHub 'dan kullanılabilir: 

- [IoT C tabanlı aracı için Azure Güvenlik Merkezi](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [IoT C#tabanlı aracı Için Azure Güvenlik Merkezi](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Aracılı desteklenen platformlar

IoT için Azure Güvenlik Merkezi, 32 bit ve 64bit Windows için farklı yükleyici aracıları ve 32bit ve 64bit Linux için de aynıdır. Aşağıdaki tabloya göre cihazlarınızın her biri için doğru aracı yükleyicisine sahip olduğunuzdan emin olun:

| Mimari | Linux | Windows |    Ayrıntılar|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| bit  | C  | C#  ||
| 64  | C#veya C           | C#      | Daha kısıtlı veya en az cihaz kaynağı olan cihazlar için C Aracısı kullanmanızı öneririz.|
|

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede IoT güvenlik Aracısı mimarisi ve kullanılabilir yükleyiciler için Azure Güvenlik Merkezi hakkında bilgi edindiniz.

IoT dağıtımı için Azure Güvenlik Merkezi 'ni kullanmaya devam etmek için aşağıdaki makaleleri kullanın:

- [Güvenlik Aracısı kimlik doğrulama yöntemlerini](concept-security-agent-authentication-methods.md) anlama
- [Güvenlik aracısını](how-to-deploy-agent.md) seçme ve dağıtma
- IoT [hizmeti önkoşulları](service-prerequisites.md) Için Azure Güvenlik Merkezi 'ni gözden geçirin
- [IoT Hub IoT hizmeti Için Azure Güvenlik Merkezi](quickstart-onboard-iot-hub.md) 'ni nasıl etkinleştirebileceğinizi öğrenin
- [IoT Için Azure Güvenlik Merkezi](resources-frequently-asked-questions.md) 'nden hizmet hakkında daha fazla bilgi edinin
