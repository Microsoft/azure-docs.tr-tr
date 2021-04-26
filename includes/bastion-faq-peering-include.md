---
title: include dosyası
description: include dosyası
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94356661"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>Eşlenen sanal ağlarda hala birden fazla savunma ana bilgisayarı dağıtabilir miyim?

Evet. Varsayılan olarak, bir Kullanıcı VM 'nin bulunduğu aynı sanal ağa dağıtılan savunma konağını görür. Bununla birlikte, **Bağlan** menüsünde, bir Kullanıcı, eşlenmiş ağlarda birden fazla savunma konağını tespit edebilir. Sanal ağda dağıtılan VM 'ye bağlanmak için kullanmayı tercih ettikleri savunma konağını seçebilir.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Eşlenen sanal ağlarım farklı aboneliklerde dağıtılırsa, savunma ile bağlantı gönderilir mi?

Evet, savunma ile bağlantı, tek bir kiracının farklı aboneliklerine eşlenmiş sanal ağlarda çalışmaya devam edecektir. İki farklı kiracıda abonelikler desteklenmez. **Bağlan** açılan menüsünde, kullanıcının **abonelik > genel abonelikte** erişimi olan alt öğeleri seçmesini sağlamalıdır.

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="Genel abonelikler filtresi" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Eşlenen VNet 'e erişimim var, ancak sanal makineyi orada dağıtamıyoruz.

Kullanıcının hem sanal makine hem de eşlenmiş VNet için **okuma** erişimi olduğundan emin olun. Ek olarak, kullanıcının aşağıdaki kaynaklara **okuma** ERIŞIMININ olduğunu IAM bölümünde da denetleyin:

* Sanal makinede okuyucu rolü.
* Sanal makinenin özel IP 'si ile NIC 'de okuyucu rolü.
* Azure savunma kaynağında okuyucu rolü.
* Sanal ağ üzerinde okuyucu rolü (eşlenen sanal ağ yoksa gerekli değildir).

|İzinler|Description|İzin türü|
|---|---| ---|
|Microsoft. Network/bastionHosts/okuma |Bir savunma ana bilgisayarı alır|Eylem|
|Microsoft. Network/virtualNetworks/Bastionkonakları/eylem |Sanal bir ağdaki savunma ana bilgisayar başvurularını alır.|Eylem|
|Microsoft. Network/virtualNetworks/Bastionkonakları/varsayılan/eylem|Sanal bir ağdaki savunma ana bilgisayar başvurularını alır.|Eylem|
|Microsoft. Network/NetworkInterfaces/Read|Bir ağ arabirimi tanımını alır.|Eylem|
|Microsoft. Network/NetworkInterfaces/ipconfigurations/Read|Bir ağ arabirimi IP yapılandırma tanımını alır.|Eylem|
|Microsoft. Network/virtualNetworks/Read|Sanal ağ tanımını al|Eylem|
|Microsoft. Network/virtualNetworks/alt ağlar/virtualMachines/Read|Bir sanal ağ alt ağındaki tüm sanal makinelere başvuruları alır|Eylem|
|Microsoft. Network/virtualNetworks/virtualMachines/Read|Bir sanal ağdaki tüm sanal makinelere başvuruları alır|Eylem|