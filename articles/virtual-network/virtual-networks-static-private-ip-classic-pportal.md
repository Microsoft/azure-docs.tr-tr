---
title: VM 'Ler için özel IP adresleri yapılandırma (klasik)-Azure portal | Microsoft Docs
description: Azure portal kullanarak sanal makineler (klasik) için özel IP adreslerini yapılandırmayı öğrenin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6986f6f16cbd32d44223bba4f4be4577fa11258c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222913"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Azure portal kullanarak bir sanal makine için özel IP adresleri yapılandırma (klasik)

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Bu makale, klasik dağıtım modelini kapsamaktadır. Ayrıca [, Kaynak Yöneticisi dağıtım modelinde statik bir özel IP adresi yönetebilirsiniz](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Aşağıdaki örnek adımlarda, önceden oluşturulmuş basit bir ortam beklenir. Adımları bu belgede görüntülendikleri gibi çalıştırmak istiyorsanız, önce [VNET oluşturma](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)bölümünde açıklanan test ortamını oluşturun.

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>VM oluştururken statik özel IP adresi belirtme
*192.168.1.101* statik özel IP 'Si Ile *Testvnet* adlı VNET 'in *ön uç* alt ağında *DNS01* adlı bir VM oluşturmak için aşağıdaki adımları izleyin:

1. Tarayıcıdan https://portal.azure.com adresine gidin ve gerekiyorsa Azure hesabınızla oturum açın.
2. **Yeni**  >  **işlem**  >  **Windows Server 2012 R2 veri merkezini** seçin, **bir dağıtım modeli Seç** listesinin zaten **Klasik** olduğunu ve sonra **Oluştur**' u seçmesini unutmayın.
   
    ![Yeni > Işlem > Windows Server 2012 R2 veri merkezi kutucuğunun vurgulandığı Azure portal gösteren ekran görüntüsü.](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. **VM oluştur** altında, oluşturulacak VM 'nin adını (senaryoda *DNS01* ), yerel yönetici hesabını ve parolayı girin.
   
    ![VM 'nin adı, yerel Yönetici Kullanıcı adı ve parola girerek VM oluşturmayı gösteren ekran görüntüsü.](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. **İsteğe bağlı yapılandırma**  >  **ağı**  >  **sanal ağı**' nı seçin ve ardından **testvnet**' i seçin. **Testvnet** yoksa, *Orta ABD* konumunu kullandığınızdan ve bu makalenin başlangıcında açıklanan test ortamını oluşturmuş olduğunuzdan emin olun.
   
    ![Isteğe bağlı yapılandırma > ağ > sanal ağ > TestVNet seçeneği vurgulanmış şekilde gösteren ekran görüntüsü.](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. **Ağ** altında, şu anda seçili olan alt ağın *ön uç* olduğundan emin olun **, IP** **adresi ataması** ' nın altında **statik**' ı seçin ve ardından aşağıda görüldüğü gibi **IP adresi** için *192.168.1.101* girin.
   
    ![Statik IP adresini yazdığınız IP adresleri alanını vurgulayan ekran görüntüsü.](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. **IP adresleri** altında **Tamam** ' ı seçin, **ağ** altında **Tamam** ' **ı seçin ve** ardından **isteğe bağlı yapılandırma**' nın altında
7. **VM oluştur** altında **Oluştur**' u seçin. Panonuzda aşağıda gösterildiği kutucuğa dikkat edin:
   
    ![Windows Server 2012 R2 Datacenter oluşturma kutucuğunu gösteren ekran görüntüsü.](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Bir VM için statik özel IP adresi bilgilerini alma
Yukarıdaki adımlarla oluşturulan VM 'nin statik özel IP adresi bilgilerini görüntülemek için aşağıdaki adımları yürütün.

1. Azure Portal tüm sanal makinelere **göz at**  >  **(klasik)**  >  **DNS01**  >  **Tüm ayarlar**  >  **IP adresleri** ' ni seçin ve IP adresi atamasını ve IP adresini aşağıda görüldüğü gibi unutmayın.
   
    ![Azure portal VM oluşturma](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Bir VM 'den statik özel IP adresi kaldırma

**IP adresleri** altında, **IP adresi atamasının** sağında **dinamik** ' i seçin, **Kaydet**' i seçin ve ardından aşağıdaki resimde gösterildiği gibi **Evet**' i seçin:
   
![IP adresi atama etiketinin sağında dinamik ' i seçerek statik özel IP adresinin bir VM 'den nasıl kaldırılacağını gösteren ekran görüntüsü.](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Var olan bir VM 'ye statik bir özel IP adresi ekleme

1. Daha önce gösterilen **IP adresleri** altında, **IP adresi atamasının** sağında **statik** ' ı seçin.
2. **IP adresi** için *192.168.1.101* yazın, **Kaydet**' i seçin ve ardından **Evet**' i seçin.

## <a name="set-ip-addresses-within-the-operating-system"></a>İşletim sistemi içinde IP adreslerini ayarla

Gerekli değilse, bir VM 'nin işletim sistemi içinde Azure sanal makinesine atanan özel IP 'yi statik olarak atamanız önerilir. İşletim sistemi içinde özel IP adresini el ile ayarlarsanız, Azure VM 'ye atanan özel IP adresi ile aynı adres olduğundan emin olun veya sanal makine bağlantısını kaybedebilirsiniz. Sanal makinenin işletim sistemi içindeki bir Azure sanal makinesine atanan genel IP adresini asla el ile atamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar
* [Ayrılmış genel IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) adresleri hakkında bilgi edinin.
* [Örnek düzeyi genel IP (ıLPıP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) adresleri hakkında bilgi edinin.
* [Ayrılmış IP REST API 'lerine](/previous-versions/azure/reference/dn722420(v=azure.100))danışın.