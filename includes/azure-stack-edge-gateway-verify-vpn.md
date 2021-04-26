---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467977"
---
VPN 'i doğrulamak için, yalnızca oluşturduğunuz sanal ağ tarafından erişilebilen bir depolama hesabı oluşturabilirsiniz. Bu depolama hesabını oluşturmak ve oluşturduğunuz sanal ağla ilişkilendirmek için aşağıdaki adımları izleyin:

1. Depolama hesabı oluşturma. Azure Stack Edge cihazınızdan kullanmayı planladığınız depolama hesabını kullanabilirsiniz. Depolama hesabına bir dış ağdan erişmeyi deneyin (seçili ağ dışında). Depolama hesabının erişilebilir olması gerekir.
2. Azure portal depolama hesabına gidin. 
3. **Güvenlik duvarları ve sanal ağlar**'a gidin. **Erişime Izin ver**' ın sağ bölmesinde **Seçili ağlar**' ı seçin. **Depolama hesabımızın sanal ağlarla güvende olmasını sağlamak** için **+ var olan sanal ağı ekle** ' yi seçin.

    ![VPN 1 doğrula](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. **Ağları Ekle** dikey penceresinde:

    - Aboneliği seçin. Bu, Azure Stack Edge/Data Box Gateway kaynağınız ile ilişkili abonelikle aynıdır. 
    - Açılan listeden, bu depolama hesabıyla ilişkilendirilecek sanal ağı seçin. Önceki adımda oluşturduğunuz sanal ağı seçin.
    - **Alt ağlarda****_varsayılan_* _ ve _GatewaySubnet * seçeneklerini belirleyin. Bu sanal ağ/alt ağ birleşimleri için hizmet uç noktaları etkinleştirilecek. Erişimin etkinleştirilmesi 15 dakika kadar sürer.
    - **Etkinleştir**’i seçin.

    ![VPN 2 ' i doğrula](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. **Ayarları** kaydedin.

    ![VPN 3 ' ü doğrula](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. Ayarlar kaydedildikten sonra, sanal ağın etkinleştirildiği alt ağları görebilirsiniz.

    ![VPN 4 ' ü doğrula](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. Verilerin şimdi yalnızca VPN üzerinden gidecebildiğini doğrulamak için şu adımları izleyin: 
    - Depolama hesabına bir dış ağdan erişmeyi deneyin (seçili ağ dışında). Depolama hesabına erişilemiyor. 
    - Seçili ağlarda etkinleştirdiğiniz sanal ağ/alt ağlardan depolama hesabına erişmeyi deneyin. Depolama hesabının erişilebilir olması gerekir. 
 
Bu depolama hesabına yalnızca VPN etkinse erişebilirsiniz. VPN 'yi devre dışı bırakırsanız, depolama hesabının ağ ayarlarını da ayarlamanız gerekir. 

Daha fazla bilgi için [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](../articles/storage/common/storage-network-security.md)bölümüne gidin. 

