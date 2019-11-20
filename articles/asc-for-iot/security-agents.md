---
title: IoT güvenlik aracıları için Azure Güvenlik Merkezi 'ni kullanmaya başlama | Microsoft Docs
description: IoT güvenlik aracıları için Azure Güvenlik Merkezi 'Ni kullanmaya başlayın.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 6e790c125c59aea85f1ac34240c5a1d1969544ae
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600598"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>IoT cihaz güvenlik aracıları için Azure Güvenlik Merkezi 'Ni kullanmaya başlama

IoT güvenlik aracıları için Azure Güvenlik Merkezi, uzak bağlantıları, etkin uygulamaları, oturum açma olaylarını ve işletim sistemi yapılandırma en iyi yöntemlerini izleme gibi gelişmiş güvenlik özellikleri sunar. Tek bir hizmetle cihaz alanı tehdit koruması ve güvenlik duruşunuzu denetim altına alın. 

Linux ve Windows Güvenlik aracıları için başvuru mimarisi, hem içinde C# hem de C sağlanır.

IoT güvenlik aracıları için Azure Güvenlik Merkezi, cihaz işletim sisteminden Ham olay toplamayı, maliyeti azaltmak için olay toplamayı ve bir cihaz modülü ikizi aracılığıyla yapılandırmayı işler. Güvenlik iletileri, IoT Analytics hizmetleri için Azure Güvenlik Merkezi 'ne IoT Hub aracılığıyla gönderilir.

IoT güvenlik aracıları için Azure Güvenlik Merkezi 'ni dağıtmak ve test etmek için aşağıdaki iş akışını kullanın: 

1. [IoT hizmeti için Azure Güvenlik Merkezi 'ni IoT Hub etkinleştirme](quickstart-onboard-iot-hub.md)
1. IoT Hub kayıtlı bir cihaz yoksa, [Yeni bir cihaz kaydedin](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. Cihazlarınız için [bir azureiotsecurity güvenlik modülü oluşturun](quickstart-create-security-twin.md) .
1. Aracıyı gerçek bir cihaza yüklemek yerine bir Azure sanal cihazına yüklemek için, kullanılabilir bir bölgede [Yeni bir Azure sanal makinesi (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) çalıştırın. 
1. IoT cihazınıza veya yeni VM 'de [IoT güvenlik Aracısı için bir Azure Güvenlik Merkezi dağıtın](how-to-deploy-linux-cs.md) .
1. Bir saldırının zararsız benzetimini çalıştırmak için [trigger_events](https://aka.ms/iot-security-github-trigger-events) yönergelerini izleyin.
1. Önceki adımda oluşan sanal saldırıya yanıt olarak IoT uyarıları için Azure Güvenlik Merkezi 'ni doğrulayın. Betiği çalıştırdıktan sonra beş dakika sonra doğrulamaya başlayın.
1. IoT Hub kullanarak [Log Analytics kullanarak](how-to-security-data-access.md) [uyarıları](concept-security-alerts.md), [önerileri](concept-recommendations.md)ve derin ayrıntılarını inceleyin. 


## <a name="next-steps"></a>Sonraki adımlar

- [Çözümünüzü](quickstart-configure-your-solution.md) yapılandırın
- [Güvenlik modülleri oluştur](quickstart-create-security-twin.md)
- [Özel uyarıları](quickstart-create-custom-alerts.md) yapılandırma
- [Güvenlik aracısı dağıtma](how-to-deploy-agent.md)
