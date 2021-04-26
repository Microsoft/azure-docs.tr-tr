---
title: Bir sanal makine için yönlendirme tercihi seçeneklerini Yapılandırma-Azure portal
description: Hem yönlendirme tercih seçeneklerinin her ikisini de etkinleştirmeyi öğrenin
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101680152"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>Bir sanal makine için her iki yönlendirme tercihi seçeneğini yapılandırın

Bu makalede, bir sanal makine (VM) için hem [yönlendirme tercihi](./routing-preference-overview.md) seçeneklerinin (Internet ve Microsoft Global Ağ) nasıl yapılandırılacağı gösterilmektedir. Bu, iki sanal ağ arabirimi, Microsoft Global ağı aracılığıyla yönlendirilen genel IP ile bir ağ arabirimi ve bir ISS ağı aracılığıyla bir genel IP 'ye yönlendirilen diğeri ile gerçekleştirilir.

## <a name="prerequisites"></a>Önkoşullar

Azure portal kullanarak [Azure Portal kullanarak statik genel IP adresine sahip bir sanal makine oluşturma](./virtual-network-deploy-static-pip-arm-portal.md) bölümünde açıklanan yönergeleri IZLEYEREK genel IP adresine sahip bir sanal makine oluşturun. Genel IP için varsayılan yönlendirme tercihi seçeneği **Microsoft Global Network** üzerinden yapılır. Ortak IP 'si oluşturulmuş bir sanal makineniz olduktan sonra, trafiği transit ISS ağı üzerinden **Internet** olarak yapılandırılmış yönlendirme tercihiyle yönlendiren ikinci BIR genel IP 'yi ekleyin.

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>Yönlendirme tercihi seçimi Internet ile genel IP adresi oluşturma
1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Kaynak oluştur**’u seçin. 
3. Arama kutusuna *genel IP adresi* yazın.
4. Arama sonuçlarında **genel IP adresi**' ni seçin. Sonra, **genel IP adresi** sayfasında **Oluştur**' u seçin.
5. **Yönlendirme tercihi** seçeneklerinde **Internet**' i seçin.

      ![Genel IP adresi oluşturma](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > Genel IP adresleri IPv4 veya IPv6 adresiyle oluşturulur. Ancak, yönlendirme tercihi şu anda yalnızca ıPV4 'Ü destekler.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>Bir ağ arabirimi oluşturun ve genel IP 'yi ilişkilendirin

1. Varsayılan ayarlarla Azure portal kullanarak bir [ağ arabirimi](./routing-preference-overview.md) oluşturun. 
1. [Genel IP 'yi, önceki bölümde oluşturulan ağ arabirimiyle ilişkilendirin](./associate-public-ip-address-vm.md) . Bir IP adresinin görünmesi birkaç saniye sürebilir. IP yapılandırmasına atanan genel IP adresini aşağıda gösterildiği gibi görüntüleyin:

    ![Genel IP 'yi ilişkilendir](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>VM 'ye ağ arabirimi iliştirme

1. [Önceki bölümde oluşturulan ağ arabirimini sanal makineye ekleyin](./virtual-network-network-interface-vm.md).
2. Artık, bir sanal makineyle ilişkili iki sanal ağ arabirimini, biri Microsoft küresel ağı aracılığıyla yönlendirilen bir genel IP ile, diğeri ise bir ISS ağıyla, diğer bir deyişle,  ![ BIR VM 'ye ağ arabirimi iliştirme gibi görüntüleyebilirsiniz.](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>Sonraki adımlar
- [Yönlendirme tercihi ile genel IP](routing-preference-overview.md)hakkında daha fazla bilgi edinin.
- [BIR VM için yönlendirme tercihini yapılandırın](tutorial-routing-preference-virtual-machine-portal.md).
- [PowerShell kullanarak genel IP adresi için yönlendirme tercihini yapılandırın](routing-preference-powershell.md).
- Azure 'da [genel IP adresleri](./public-ip-addresses.md#public-ip-addresses) hakkında daha fazla bilgi edinin.
- Tüm [genel IP adresi ayarları](virtual-network-public-ip-address.md#create-a-public-ip-address)hakkında daha fazla bilgi edinin.