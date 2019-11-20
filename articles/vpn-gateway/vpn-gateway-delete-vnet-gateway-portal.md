---
title: 'Azure VPN Gateway: ağ geçidini silme: Portal'
description: Kaynak Yöneticisi dağıtım modelindeki Azure portal kullanarak bir sanal ağ geçidini silin.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: e8c7a9c7b6d38c5fee4f57f65dd61a71f9723c07
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146350"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Portalı kullanarak bir sanal ağ geçidini silme

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasik)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Bu makalede, Kaynak Yöneticisi dağıtım modeli kullanılarak dağıtılan bir Azure VPN ağ geçitlerini silme yönergeleri sunulmaktadır. VPN ağ geçidi yapılandırması için bir sanal ağ geçidini silmek istediğinizde uygulayabileceğiniz birkaç farklı yaklaşım vardır.

- Her şeyi silmek ve baştan başlamak istiyorsanız, bir test ortamı durumunda olduğu gibi, kaynak grubunu silebilirsiniz. Bir kaynak grubunu sildiğinizde, Grup içindeki tüm kaynakları siler. Bu yöntem yalnızca kaynak grubundaki kaynakları tutmak istemiyorsanız önerilir. Bu yaklaşımı kullanarak yalnızca birkaç kaynağı seçmeli olarak silemezsiniz.

- Kaynak grubunuzdaki bazı kaynakları korumak istiyorsanız, bir sanal ağ geçidini silmek biraz daha karmaşık hale gelir. Sanal ağ geçidini silebilmeniz için önce ağ geçidine bağlı olan tüm kaynakları silmeniz gerekir. İzleyeceğiniz adımlar, oluşturduğunuz bağlantı türüne ve her bağlantı için bağımlı kaynaklara bağlıdır.

> [!IMPORTANT]
> Aşağıdaki yönergeler Kaynak Yöneticisi dağıtım modeli kullanılarak dağıtılan Azure VPN ağ geçitlerinin nasıl silineceğini açıklamaktadır. Klasik dağıtım modeli kullanılarak dağıtılan bir VPN ağ geçidini silmek için lütfen [burada](vpn-gateway-delete-vnet-gateway-classic-powershell.md)açıklandığı gibi Azure PowerShell kullanın.


## <a name="delete-a-vpn-gateway"></a>VPN ağ geçidi silme

Bir sanal ağ geçidini silmek için, önce sanal ağ geçidi ile ilgili olan her kaynağı silmeniz gerekir. Bağımlılıklar nedeniyle kaynakların belirli bir sırada silinmesi gerekir.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Bu noktada, sanal ağ geçidi silinir. Sonraki adımlar artık kullanılmayan kaynakları silmenizi yardımcı olur.

### <a name="to-delete-the-local-network-gateway"></a>Yerel ağ geçidini silmek için

1. **Tüm kaynaklarda**, her bağlantıyla ilişkili yerel ağ geçitlerini bulun.
2. Yerel ağ geçidinin **genel bakış** dikey penceresinde **Sil**' e tıklayın.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Ağ geçidinin genel IP adresi kaynağını silmek için

1. **Tüm kaynaklar**' da, ağ geçidiyle ILIŞKILI genel IP adresi kaynağını bulun. Sanal ağ geçidi etkin-etkin ise, iki genel IP adresi görürsünüz. 
2. Genel IP adresinin **genel bakış** sayfasında **Sil**' e tıklayın ve ardından onaylamak için **Evet** ' i tıklatın.

### <a name="to-delete-the-gateway-subnet"></a>Ağ geçidi alt ağını silmek için

1. **Tüm kaynaklarda**, sanal ağı bulun. 
2. **Alt ağlar** dikey penceresinde, **Gatewaysubnet**ve ardından **Sil**' e tıklayın. 
3. Ağ geçidi alt ağını silmek istediğinizi onaylamak için **Evet** ' e tıklayın.

## <a name="deleterg"></a>Kaynak grubunu silerek VPN ağ geçidini silme

Kaynak grubunda kaynaklarınızdan herhangi birini tutmanın endişeniz yoksa ve yalnızca baştan başlamak istiyorsanız, bir kaynak grubunun tamamını silebilirsiniz. Bu, her şeyi kaldırmanın hızlı bir yoludur. Aşağıdaki adımlar yalnızca Kaynak Yöneticisi dağıtım modeli için geçerlidir.

1. **Tüm kaynaklarda**, kaynak grubunu bulun ve dikey pencereyi açmak için tıklayın.
2. **Sil**'e tıklayın. Sil dikey penceresinde etkilenen kaynakları görüntüleyin. Tüm bu kaynakları silmek istediğinizden emin olun. Aksi takdirde, bu makalenin üst kısmındaki VPN Gateway 'i silme bölümündeki adımları kullanın.
3. Devam etmek için, silmek istediğiniz kaynak grubunun adını yazın ve **Sil**' e tıklayın.
