---
title: "Hızlı başlangıç: IoT Hub 'da IoT hizmeti için Azure Güvenlik Merkezi 'Ni etkinleştirme"
description: Bu hızlı başlangıçta, IoT Hub IoT hizmeti için Azure Güvenlik Merkezi 'ni nasıl etkinleştirebileceğinizi öğrenin.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 641ba4c8d3b0e54132c19a493e1e4bf17bb28e13
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904105"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Hızlı başlangıç: IoT Hub 'da IoT hizmeti için Azure Güvenlik Merkezi 'Ni ekleme

Bu makalede, mevcut IoT Hub IoT hizmeti için Azure Güvenlik Merkezi 'nin nasıl etkinleştirileceği hakkında bir açıklama sunulmaktadır. Şu anda bir IoT Hub yoksa, kullanmaya başlamak için [Azure Portal kullanarak IoT Hub oluşturma](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) makalesine bakın. 

> [!NOTE]
> IoT için Azure Güvenlik Merkezi şu anda yalnızca Standart katman IoT Hub 'Larını desteklemektedir.


## <a name="prerequisites-for-enabling-the-service"></a>Hizmeti etkinleştirme önkoşulları

- Log Analytics çalışma alanı
  - İki tür bilgi, IoT için Azure Güvenlik Merkezi tarafından Log Analytics çalışma alanınızda varsayılan olarak saklanır; **güvenlik uyarıları** ve **önerileri**. 
  - Ek bilgi türü, **Ham olaylar**için depolama alanını eklemeyi seçebilirsiniz. **Ham olayların** Log Analytics daha fazla depolama maliyeti taşıdığına göz önünde unutmayın. 
- IoT Hub (Standart katman)
- Tüm [hizmet önkoşullarını](service-prerequisites.md) karşılayın 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>IoT Hub IoT için Azure Güvenlik Merkezi 'ni etkinleştirin 

IoT Hub güvenliği sağlamak için: 

1. **IoT Hub** Azure Portal açın. 
1. **Güvenlik** menüsünde, **IoT çözümünüzün güvenliğini sağla**' yı tıklatın.    


Tebrikler! IoT Hub IoT için Azure Güvenlik Merkezi 'Ni etkinleştirmeyi tamamladınız. 

### <a name="geolocation-and-ip-address-handling"></a>Coğrafi konum ve IP adresi işleme

IoT çözümünüzü güvenli hale getirmek için IoT cihazlarınıza gelen ve giden bağlantıların IP adresleri, IoT Edge ve IoT Hub, varsayılan olarak toplanır ve depolanır. Bu bilgiler şüpheli IP kaynaklarından anormal bağlantıları algılamak için gereklidir. Örneğin, bilinen bir botnet 'in IP kaynağından veya coğrafi konum dışında bir IP kaynağından bağlantı kurmak için denemeler yapılır. IoT hizmeti için Azure Güvenlik Merkezi, IP adresi verilerinin toplanmasını istediğiniz zaman etkinleştirme ve devre dışı bırakma esnekliği sunar. 

IP adresi verilerini toplamayı etkinleştirmek veya devre dışı bırakmak için: 

1. IoT Hub açın ve **güvenlik** menüsünden **genel bakış** ' ı seçin. 
2. **Ayarlar** ekranını seçin ve coğrafi konum ve/veya IP işleme ayarlarını istediğiniz gibi değiştirin.

### <a name="log-analytics-creation"></a>Log Analytics oluşturma

IoT için Azure Güvenlik Merkezi açıldığında, IoT cihazlarınıza, IoT Edge ve IoT Hub yönelik ham güvenlik olaylarını, uyarıları ve önerilerini depolamak için varsayılan bir Azure Log Analytics çalışma alanı oluşturulur. Her ay, Azure Log Analytics hizmetine müşteri başına alınan ilk beş (5) veri giriş ücretsizdir. Azure Log Analytics çalışma alanınızda alınan her GB veri, ilk 31 gün boyunca ek ücret olmaksızın tutulur. [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) fiyatlandırması hakkında daha fazla bilgi edinin.

Log Analytics çalışma alanı yapılandırmasını değiştirmek için:

1. IoT Hub açın ve **güvenlik** menüsünden **genel bakış** ' ı seçin. 
2. **Ayarlar** ekranını seçin ve Log Analytics ayarların çalışma alanı yapılandırmasını istediğiniz gibi değiştirin.

### <a name="customize-your-iot-security-solution"></a>IoT Güvenlik çözümünüzü özelleştirme
Varsayılan olarak, IoT çözümü için Azure Güvenlik Merkezi 'ni açmak, Azure aboneliğinizdeki tüm IoT Hub 'Larını otomatik olarak korur. 

Belirli bir IoT Hub IoT hizmeti için Azure Güvenlik Merkezi 'ni etkinleştirmek veya devre dışı bırakmak için: 

1. IoT Hub açın ve **güvenlik** menüsünden **genel bakış** ' ı seçin. 
2. **Ayarlar** ekranını seçin ve Azure aboneliğinizdeki herhangi bir IoT Hub 'ının güvenlik ayarlarını istediğiniz gibi değiştirin.


## <a name="next-steps"></a>Sonraki adımlar

Çözümünüzü yapılandırmak için sonraki makaleye ilerleyin...

> [!div class="nextstepaction"]
> [Çözümünüzü yapılandırın](quickstart-configure-your-solution.md)
