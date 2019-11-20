---
title: VM 'Ler için özel IP adresleri yapılandırma (klasik)-Azure portal | Microsoft Docs
description: Azure portal kullanarak sanal makineler (klasik) için özel IP adreslerini yapılandırmayı öğrenin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9de9fd2eb7a46719b841a885ac173f8d9c1271c1
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059102"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Azure portal kullanarak bir sanal makine için özel IP adresleri yapılandırma (klasik)

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Bu makale, klasik dağıtım modelini kapsamaktadır. Ayrıca [Resource Manager dağıtım modelinde statik özel IP adresi yönetme](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Aşağıdaki örnek adımlarda, önceden oluşturulmuş basit bir ortam beklenir. Adımları bu belgede görüntülendikleri gibi çalıştırmak istiyorsanız, önce [VNET oluşturma](virtual-networks-create-vnet-classic-pportal.md)bölümünde açıklanan test ortamını oluşturun.

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Bir VM oluşturulurken özel bir statik IP adresi belirtme
*192.168.1.101*statik özel IP 'Si Ile *Testvnet* adlı VNET 'in *ön uç* alt ağında *DNS01* adlı bir VM oluşturmak için aşağıdaki adımları izleyin:

1. Tarayıcıdan https://portal.azure.com adresine gidin ve gerekiyorsa Azure hesabınızla oturum açın.
2. **Yeni**işlem Windows Server 2012 R2 veri merkezini seçin, bir dağıtım modeli Seç listesinin zaten klasik olduğunu ve sonra Oluştur ' u seçmesini unutmayın. >  > 
   
    ![Azure portal VM oluşturma](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. **VM oluştur**altında, oluşturulacak VM 'nin adını (senaryoda*DNS01* ), yerel yönetici hesabını ve parolayı girin.
   
    ![Azure portal VM oluşturma](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. **İsteğe bağlı yapılandırma** > **ağı** > **sanal ağı**' nı seçin ve ardından **testvnet**' i seçin. **Testvnet** yoksa, *Orta ABD* konumunu kullandığınızdan ve bu makalenin başlangıcında açıklanan test ortamını oluşturmuş olduğunuzdan emin olun.
   
    ![Azure portal VM oluşturma](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. **Ağ**altında, şu anda seçili olan alt ağın *ön uç*olduğundan emin olun **, IP** **adresi ataması** ' nın altında **statik**' ı seçin ve ardından *192.168.1.101* adresini görüldüğü gibi **IP adresi** için girin below.
   
    ![Azure portal VM oluşturma](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. **IP adresleri**altında **Tamam** ' ı seçin, **ağ**altında **Tamam** ' **ı seçin ve** ardından **isteğe bağlı yapılandırma**' nın altında
7. **VM oluştur**altında **Oluştur**' u seçin. Panonuzda aşağıda gösterildiği kutucuğa dikkat edin:
   
    ![Azure portal VM oluşturma](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Bir sanal makine için statik özel IP adresi bilgilerini alma
Yukarıdaki adımlarla oluşturulan VM 'nin statik özel IP adresi bilgilerini görüntülemek için aşağıdaki adımları yürütün.

1. Azure Portal tüm sanal makinelere **göz at** >  **(klasik)**  > **DNS01** > **Tüm ayarlar** > **IP adresleri** ' ni seçin ve IP adresi atamasını ve Aşağıda görüldüğü gibi IP adresi.
   
    ![Azure portal VM oluşturma](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM'den özel statik IP adresi kaldırma

**IP adresleri**altında, **IP adresi atamasının**sağında **dinamik** ' i seçin, **Kaydet**' i seçin ve ardından aşağıdaki resimde gösterildiği gibi **Evet**' i seçin:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Var olan bir VM 'ye statik bir özel IP adresi ekleme

1. Daha önce gösterilen **IP adresleri**altında, **IP adresi atamasının**sağında **statik** ' ı seçin.
2. **IP adresi**için *192.168.1.101* yazın, **Kaydet**' i seçin ve ardından **Evet**' i seçin.

## <a name="set-ip-addresses-within-the-operating-system"></a>İşletim sistemi içinde IP adreslerini ayarlayın

Statik olarak bir sanal makinenin işletim sistemi içinde Azure sanal makinesine atanmış özel IP sürece atamayın, önerilen gerekli. İşletim sistemi içinde özel IP adresini el ile ayarlamanız durumunda Azure VM'sine atanan özel IP adresiyle aynı adresi olduğundan emin olun veya sanal makineye bağlantı kaybedebilir. Hiçbir zaman el ile bir Azure sanal makinesi sanal makinenin işletim sistemi içinde atanan genel IP adresi atamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar
* Hakkında bilgi edinin [ayrılmış genel IP](virtual-networks-reserved-public-ip.md) adresleri.
* Hakkında bilgi edinin [örnek düzeyi genel IP (ILPIP)](virtual-networks-instance-level-public-ip.md) adresleri.
* Başvurun [ayrılmış IP REST API'leri](https://msdn.microsoft.com/library/azure/dn722420.aspx).

