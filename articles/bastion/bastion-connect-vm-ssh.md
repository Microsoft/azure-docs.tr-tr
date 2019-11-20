---
title: Azure 'u kullanarak bir Linux VM 'ye bağlanma | Microsoft Docs
description: Bu makalede, Azure savunma kullanarak Linux sanal makinesine nasıl bağlanacağınızı öğrenin.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: b88327ea0b5d2958cc1c86fa317415f2441af894
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494483"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Azure savunma kullanarak bir Linux sanal makinesine SSH kullanarak bağlanma

Bu makalede, bir Azure sanal ağındaki Linux VM 'lerinize güvenli ve sorunsuz bir şekilde SSH ekleyebilirsiniz. Azure portalından doğrudan bir sanal makineye bağlanabilirsiniz. Azure Bastion’ı kullanırken sanal makineler için bir istemci, aracı veya ek yazılım gerekmez. Azure savunma hakkında daha fazla bilgi için bkz. [genel bakış](bastion-overview.md).

SSH kullanarak bir Linux sanal makinesine bağlanmak için Azure savunma kullanabilirsiniz. Kimlik doğrulaması için hem Kullanıcı adı/parola hem de SSH anahtarlarını kullanabilirsiniz. VM 'nize şu iki kullanarak SSH anahtarlarıyla bağlanabilirsiniz:

* El ile girdiğiniz özel bir anahtar
* Özel anahtar bilgilerini içeren bir dosya

SSH özel anahtarı `"-----BEGIN RSA PRIVATE KEY-----"` ile başlayan ve `"-----END RSA PRIVATE KEY-----"`biten bir biçimde olmalıdır.

## <a name="before-you-begin"></a>Başlamadan önce

VM 'nin bulunduğu sanal ağ için bir Azure savunma ana bilgisayarı ayarladığınızdan emin olun. Daha fazla bilgi için bkz. [Azure savunma Konağı oluşturma](bastion-create-host-portal.md). Savunma hizmeti, sanal ağınıza sağlandıktan ve dağıtıldıktan sonra, bu sanal ağdaki herhangi bir VM 'ye bağlanmak için kullanabilirsiniz. Bağlanmak için savunma kullandığınızda, Windows VM 'ye bağlanmak için RDP kullandığınızı ve Linux sanal makinelerinize bağlanmak için SSH 'yi kullandığınızı varsayar.

Bir bağlantı oluşturmak için aşağıdaki roller gereklidir:

* Sanal makinede okuyucu rolü
* Sanal makinenin özel IP 'si ile NIC 'de okuyucu rolü
* Azure savunma kaynağında okuyucu rolü

## <a name="username"></a>Bağlan: Kullanıcı adı ve parola kullanma

1.   [Azure portalı](https://portal.azure.com) açın. Bağlanmak istediğiniz sanal makineye gidin ve **Bağlan**' a tıklayın. SSH bağlantısı kullanılırken VM 'nin bir Linux sanal makinesi olması gerekir.
1. Bağlan ' a tıkladıktan sonra, üç sekmeye (RDP, SSH ve savunma) sahip bir yan çubuk görüntülenir. Sanal ağ için bir savunma sağlandıysa, savunma sekmesi varsayılan olarak etkindir. Sanal ağ için savunma sağlamadıysanız bkz. [yapılandırmayı yapılandırma](bastion-create-host-portal.md).

   ![VM bağlantısı](./media/bastion-connect-vm-ssh/bastion.png)
1. Sanal makinenize SSH için Kullanıcı adı ve parola girin.
1. Anahtarı girdikten sonra **Bağlan** düğmesine tıklayın.

## <a name="privatekey"></a>Bağlan: El Ile özel bir anahtar girin

1. [Azure portalı](https://portal.azure.com) açın. Bağlanmak istediğiniz sanal makineye gidin ve **Bağlan**' a tıklayın. SSH bağlantısı kullanılırken VM 'nin bir Linux sanal makinesi olması gerekir.
1. Bağlan ' a tıkladıktan sonra, üç sekmeye (RDP, SSH ve savunma) sahip bir yan çubuk görüntülenir. Sanal ağ için bir savunma sağlandıysa, savunma sekmesi varsayılan olarak etkindir. Sanal ağ için savunma sağlamadıysanız bkz. [yapılandırmayı yapılandırma](bastion-create-host-portal.md).

   ![VM bağlantısı](./media/bastion-connect-vm-ssh/bastion.png)
1. Kullanıcı adını girin ve **SSH özel anahtarı**' nı seçin.
1. Özel anahtarınızı **SSH özel anahtarı** metin alanına girin (veya doğrudan yapıştırın).
1. Anahtarı girdikten sonra **Bağlan** düğmesine tıklayın.

## <a name="ssh"></a>Bağlan: özel anahtar dosyası kullanma

1. [Azure portalı](https://portal.azure.com) açın. Bağlanmak istediğiniz sanal makineye gidin ve **Bağlan**' a tıklayın. SSH bağlantısı kullanılırken VM 'nin bir Linux sanal makinesi olması gerekir.

   ![VM bağlantısı](./media/bastion-connect-vm-ssh/connect.png)
1. Bağlan ' a tıkladıktan sonra, üç sekmeye (RDP, SSH ve savunma) sahip bir yan çubuk görüntülenir. Sanal ağ için bir savunma sağlandıysa, savunma sekmesi varsayılan olarak etkindir. Sanal ağ için savunma sağlamadıysanız bkz. [yapılandırmayı yapılandırma](bastion-create-host-portal.md).

   ![VM bağlantısı](./media/bastion-connect-vm-ssh/bastion.png)
1. Kullanıcı adını girin ve **yerel dosyadan SSH özel anahtarı**' nı seçin.
1. (Yerel dosyadaki klasör simgesi **) düğmesine tıklayın** .
1. Dosyaya gözatıp açın, sonra **Aç**' a tıklayın.
1. SANAL makineye bağlanmak için **Bağlan** ' a tıklayın. Bağlan ' a tıkladığınızda bu sanal makineye SSH doğrudan Azure portal açılır. Bu bağlantı, sanal makinenizin özel IP 'si üzerinde savunma hizmetinde 443 numaralı bağlantı noktasını kullanan HTML5 'in üstünden fazla.

## <a name="next-steps"></a>Sonraki adımlar

Savunma [hakkında SSS](bastion-faq.md) makalesini okuyun