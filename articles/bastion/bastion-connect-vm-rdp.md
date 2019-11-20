---
title: Azure savunma kullanarak bir Windows VM 'ye bağlanma | Microsoft Docs
description: Bu makalede, Azure savunma kullanarak Windows çalıştıran bir Azure sanal makinesine nasıl bağlanacağınızı öğrenin.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: dc741007c7de8d8e24f9c0f9e4e0c03306d036a4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498355"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Azure savunma kullanarak bir Windows sanal makinesine bağlanma

Bu makalede, Azure savunma kullanarak bir Azure sanal ağındaki Windows sanal makinelerinize güvenli ve sorunsuz bir şekilde RDP uygulamanız gösterilmektedir. Azure portalından doğrudan bir sanal makineye bağlanabilirsiniz. Azure Bastion’ı kullanırken sanal makineler için bir istemci, aracı veya ek yazılım gerekmez. Azure savunma hakkında daha fazla bilgi için bkz. [genel bakış](bastion-overview.md).

## <a name="before-you-begin"></a>Başlamadan önce

VM 'nin bulunduğu sanal ağ için bir Azure savunma ana bilgisayarı ayarladığınızdan emin olun. Daha fazla bilgi için bkz. [Azure savunma Konağı oluşturma](bastion-create-host-portal.md). Savunma hizmeti, sanal ağınıza sağlandıktan ve dağıtıldıktan sonra, bu sanal ağdaki herhangi bir VM 'ye bağlanmak için kullanabilirsiniz. Savunma, bir Windows sanal makinesine bağlanmak için RDP kullandığınızı ve Linux sanal makinelerinize bağlanmak için SSH kullandığınızı varsayar. Bir Linux VM bağlantısı hakkında daha fazla bilgi için bkz. [VM 'ye bağlanma-Linux](bastion-connect-vm-ssh.md).

Bir bağlantı oluşturmak için aşağıdaki roller gereklidir:

* Sanal makinede okuyucu rolü
* Sanal makinenin özel IP 'si ile NIC 'de okuyucu rolü
* Azure savunma kaynağında okuyucu rolü

## <a name="rdp"></a>RDP kullanarak bağlanma

1. [Azure portalı](https://portal.azure.com) açın. Bağlanmak istediğiniz sanal makineye gidin ve **Bağlan**' a tıklayın. RDP bağlantısı kullanılırken VM 'nin bir Windows sanal makinesi olması gerekir.

   ![VM bağlantısı](./media/bastion-connect-vm-rdp/connect.png)
1. Bağlan ' a tıkladıktan sonra, üç sekmeye (RDP, SSH ve savunma) sahip bir yan çubuk görüntülenir. Sanal ağ için bir savunma sağlandıysa, savunma sekmesi varsayılan olarak etkindir. Sanal ağ için bir savunma sağlamadıysanız, bağlantıyı yapılandırmak için bağlantıya tıklayabilirsiniz. Yapılandırma yönergeleri için bkz. yapılandırmayı [yapılandırma](bastion-create-host-portal.md).

   ![VM bağlantısı](./media/bastion-connect-vm-rdp/bastion.png)
1. Savunma sekmesinde, sanal makinenizin Kullanıcı adı ve parolası ' nı ve ardından **Bağlan**' a tıklayın. Bu sanal makineyle savunma aracılığıyla RDP bağlantısı, bağlantı noktası 443 ve savunma hizmeti kullanılarak doğrudan Azure portal (HTML5 üzerinden) açılır.

   ![VM bağlantısı](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Sonraki adımlar

Savunma [hakkında SSS](bastion-faq.md) makalesini okuyun