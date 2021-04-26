---
title: Azure Percept güvenlik duvarı yapılandırması ve güvenlik önerileri
description: Azure Percept güvenlik duvarı yapılandırması ve güvenlik önerileri hakkında daha fazla bilgi edinin
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: cfc20a30104e24a3950c71bdd8377544803d2f25
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604422"
---
# <a name="azure-percept-firewall-configuration-and-security-recommendations"></a>Azure Percept güvenlik duvarı yapılandırması ve güvenlik önerileri

Azure Percept ile güvenlik duvarlarını ve genel güvenlik en iyi yöntemlerini yapılandırma hakkında bilgi için aşağıdaki yönergeleri gözden geçirin.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Azure Percept DK için güvenlik duvarlarını yapılandırma

Ağ Kurulum, Azure Percept DK cihazlarından yapılan bağlantılara açıkça izin vermenizi gerektiriyorsa, aşağıdaki bileşen listesini gözden geçirin.

Bu denetim listesi, güvenlik duvarı kuralları için bir başlangıç noktasıdır:

|URL (* = joker karakter)|Giden TCP bağlantı noktaları|Kullanım|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|443|Azure DK SOM kimlik doğrulaması ve yetkilendirme|
|*. auth.projectsantacruz.azure.net|443|Azure DK SOM kimlik doğrulaması ve yetkilendirme|

Ayrıca, [Azure IoT Edge tarafından kullanılan bağlantıların](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices)listesini gözden geçirin.

## <a name="additional-recommendations-for-deployment-to-production"></a>Üretime dağıtım için ek öneriler

Azure Percept DK, kutudan çıkan çok çeşitli güvenlik özellikleri sunar. Microsoft, geçerli sürüme dahil edilen güçlü güvenlik özelliklerine ek olarak, üretim dağıtımlarını değerlendirirken aşağıdaki yönergeleri de önerir:

- Cihazın kendisinin güçlü fiziksel koruması
- Rest verisinin etkin olduğundan emin olun
- Cihaz duruşunu sürekli izleyin ve uyarılara hızlı bir şekilde yanıt verin
- Cihaza erişimi olan yönetici sayısını sınırlayın

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Percept güvenliği hakkında daha fazla bilgi edinin](./overview-percept-security.md)