---
title: IoT önkoşulları için Azure Güvenlik Merkezi | Microsoft Docs
description: IoT hizmeti önkoşulları için Azure Güvenlik Merkezi 'ni kullanmaya başlamak için gereken her şeyin ayrıntıları.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: 4440fec98d1f561da6375bcaadba4282076cc53b
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299474"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>IoT önkoşulları için Azure Güvenlik Merkezi

Bu makalede IoT hizmeti için Azure Güvenlik Merkezi 'nin farklı derleme blokları, başlamak için gerekenler ve hizmeti anlamanıza yardımcı olacak temel kavramlar açıklanmaktadır. 

## <a name="minimum-requirements"></a>Minimum gereksinimler

- Standart katman IoT Hub
    - RBAC rol **sahibi** düzeyi ayrıcalıkları 
- [Log Analytics çalışma alanı](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Güvenlik Merkezi (önerilir)
    - Azure Güvenlik Merkezi 'nin kullanımı, gereksinim değil, öneridir. Azure Güvenlik Merkezi olmadan, diğer Azure kaynaklarınızı IoT Hub içinde görüntüleyemedik. 
 
## <a name="working-with-azure-security-center-for-iot-service"></a>IoT hizmeti için Azure Güvenlik Merkezi ile çalışma

IoT öngörüleri ve raporlama için Azure Güvenlik Merkezi, Azure IoT Hub ve Azure Güvenlik Merkezi kullanılarak kullanılabilir. Azure IoT Hub IoT için Azure Güvenlik Merkezi 'ni etkinleştirmek üzere, **sahip** düzeyi ayrıcalıklarına sahip bir hesap gereklidir. IoT Hub IoT için ASC 'yi etkinleştirdikten sonra IoT öngörüleri için Azure Güvenlik Merkezi, Azure Güvenlik Merkezi 'nde Azure IoT Hub **güvenlik** özelliği olarak ve **IoT** olarak görüntülenir. 

## <a name="supported-service-regions"></a>Desteklenen hizmet bölgeleri 

IoT için Azure Güvenlik Merkezi şu anda aşağıdaki Azure bölgelerindeki IoT Hub 'Ları için desteklenmektedir:
  - Orta ABD  
  - East US 
  - Doğu ABD 2
  - Batı Orta ABD
  - Batı ABD
  - Batı ABD 2
  - Güney Orta ABD
  - Orta Kuzey ABD
  - Orta Kanada
  - Doğu Kanada 
  - Kuzey Avrupa    
  - Güney Brezilya
  - Fransa Orta  
  - Birleşik Krallık Batı 
  - Birleşik Krallık Güney
  - Batı Avrupa 
  - Kuzey Avrupa 
  - Japonya Batı  
  - Japonya Doğu  
  - Avustralya Güneydoğu
  - Avustralya Doğu
  - Doğu Asya   
  - Güneydoğu Asya
  - Kore Orta
  - Kore Güney 
  - Orta Hindistan
  - Güney Hindistan

IoT için Azure Güvenlik Merkezi, tüm Avrupa bölgelerinden gelen tüm trafiği Batı Avrupa bölgesel veri merkezine ve kalan tüm bölgeleri Orta ABD bölgesel veri merkezine yönlendirir.  
  
## <a name="wheres-my-iot-hub"></a>IoT Hub nerede?

Başlamadan önce hizmet kullanılabilirliğini doğrulamak için IoT Hub konumunuzu denetleyin. 

1. IoT Hub açın. 
2. **Genel Bakış**'a tıklayın. 
3. Listelenen konumun [desteklenen hizmet bölgelerinden](#supported-service-regions)biriyle eşleştiğini doğrulayın. 


## <a name="supported-platforms-for-agents"></a>Aracılar için desteklenen platformlar 

IoT aracıları için Azure Güvenlik Merkezi, büyüyen bir cihaz ve platform listesini destekler. Mevcut veya planlı cihaz kitaplığınızı denetlemek için [desteklenen platform listesine](how-to-deploy-agent.md) bakın.  

## <a name="next-steps"></a>Sonraki adımlar
- Azure IoT güvenliğine [genel bakış](overview.md) konusunu okuyun
- [Hizmeti etkinleştirmeyi](quickstart-onboard-iot-hub.md) öğrenin
- [IoT Için Azure Güvenlik MERKEZI SSS](resources-frequently-asked-questions.md) makalesini okuyun
- [IoT uyarıları Için Azure Güvenlik Merkezi 'ni nasıl anlayacağınızı](concept-security-alerts.md) öğrenin
