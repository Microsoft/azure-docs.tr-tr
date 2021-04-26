---
title: 'Azure VPN Gateway: ağ geçidini silme: Portal'
description: Azure portal kullanarak bir sanal ağ geçidini silme
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/10/2021
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 413fd8c7f03ef44abe4bece39ca717c533dea66b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376402"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Portalı kullanarak bir sanal ağ geçidini silme

> [!div class="op_single_selector"]
> * [Azure portalı](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasik)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Bu makale, bir sanal ağ geçidini silmenizi sağlar. Bir VPN ağ geçidi yapılandırması için bir ağ geçidini silmek istediğinizde uygulayabileceğiniz birkaç farklı yaklaşım vardır.

* Her şeyi silmek ve baştan başlamak istiyorsanız, bir test ortamı durumunda olduğu gibi, kaynak grubunu silebilirsiniz. Bir kaynak grubunu sildiğinizde, Grup içindeki tüm kaynakları siler. Bu yöntem yalnızca kaynak grubundaki kaynakları tutmak istemiyorsanız önerilir. Bu yaklaşımı kullanarak yalnızca birkaç kaynağı seçmeli olarak silemezsiniz.

* Kaynak grubunuzdaki bazı kaynakları korumak istiyorsanız, bir sanal ağ geçidini silmek biraz daha karmaşık hale gelir. Sanal ağ geçidini silebilmeniz için önce ağ geçidine bağlı olan tüm kaynakları silmeniz gerekir. İzleyeceğiniz adımlar, oluşturduğunuz bağlantı türüne ve her bağlantı için bağımlı kaynaklara bağlıdır.

> [!IMPORTANT]
> Bu makaledeki adımlar Resource Manager dağıtım modeli için geçerlidir. Klasik dağıtım modeli kullanılarak dağıtılan bir VPN ağ geçidini silmek için, [ağ geçidini silme: klasik](vpn-gateway-delete-vnet-gateway-classic-powershell.md) makalesinde bulunan adımları kullanın.

## <a name="delete-a-vpn-gateway"></a>VPN ağ geçidi silme

Bir sanal ağ geçidini silmek için, önce sanal ağ geçidi ile ilgili olan her kaynağı silmeniz gerekir. Bağımlılıklar nedeniyle kaynakların belirli bir sırada silinmesi gerekir.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Bu noktada, sanal ağ geçidi silinir.

### <a name="to-delete-additional-resources"></a>Ek kaynakları silmek için

Aşağıdaki adımlar artık kullanılmayan kaynakları silmenizi yardımcı olur.

#### <a name="to-delete-the-local-network-gateway"></a>Yerel ağ geçidini silmek için

1. **Tüm kaynaklarda**, her bağlantıyla ilişkili yerel ağ geçitlerini bulun.
1. Yerel ağ geçidinin **genel bakış** dikey penceresinde **Sil**' e tıklayın.

#### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Ağ geçidinin genel IP adresi kaynağını silmek için

1. **Tüm kaynaklar**' da, ağ geçidiyle ILIŞKILI genel IP adresi kaynağını bulun. Sanal ağ geçidi etkin-etkin ise, iki genel IP adresi görürsünüz.
1. Genel IP adresinin **genel bakış** sayfasında **Sil**' e tıklayın ve ardından onaylamak için **Evet** ' i tıklatın.

#### <a name="to-delete-the-gateway-subnet"></a>Ağ geçidi alt ağını silmek için

1. **Tüm kaynaklarda**, sanal ağı bulun. 
1. **Alt ağlar** dikey penceresinde, **Gatewaysubnet** ve ardından **Sil**' e tıklayın. 
1. Ağ geçidi alt ağını silmek istediğinizi onaylamak için **Evet** ' e tıklayın.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Kaynak grubunu silerek VPN ağ geçidini silme

Kaynak grubunda kaynaklarınızdan herhangi birini tutmanın endişeniz yoksa ve yalnızca baştan başlamak istiyorsanız, bir kaynak grubunun tamamını silebilirsiniz. Bu, her şeyi kaldırmanın hızlı bir yoludur. Aşağıdaki adımlar yalnızca Kaynak Yöneticisi dağıtım modeli için geçerlidir.

1. **Tüm kaynaklarda**, kaynak grubunu bulun ve dikey pencereyi açmak için tıklayın.
1. **Sil**'e tıklayın. Sil dikey penceresinde etkilenen kaynakları görüntüleyin. Tüm bu kaynakları silmek istediğinizden emin olun. Aksi takdirde, bu makalenin üst kısmındaki VPN Gateway 'i silme bölümündeki adımları kullanın.
1. Devam etmek için, silmek istediğiniz kaynak grubunun adını yazın ve **Sil**' e tıklayın.
