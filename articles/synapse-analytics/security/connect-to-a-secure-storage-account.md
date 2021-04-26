---
title: Azure SYNAPSE çalışma alanınızdan güvenli bir depolama hesabına bağlanma
description: Bu makalede, Azure SYNAPSE çalışma alanınızdan güvenli bir depolama hesabına nasıl bağlanabileceğiniz açıklanır
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5d43d6f56b48a34fa34baf727508ad8f1c151aa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674326"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>SYNAPSE çalışma alanınızdan güvenli bir Azure depolama hesabına bağlanma

Bu makale, Azure SYNAPSE çalışma alanınızdan güvenli bir Azure depolama hesabına nasıl bağlanacaksınız. Bir Azure Depolama hesabını, çalışma alanınızı oluştururken SYNAPSE çalışma alanınıza bağlayabilirsiniz. Çalışma alanınızı oluşturduktan sonra, daha fazla depolama hesabı bağlayabilirsiniz.


## <a name="secured-azure-storage-accounts"></a>Güvenli Azure depolama hesapları
Azure depolama, depolama hesaplarınıza erişimi sağlamanıza ve denetlemenize olanak tanıyan katmanlı bir güvenlik modeli sağlar. IP güvenlik duvarı kurallarını, seçili genel IP adresi aralıklarından depolama hesabınıza erişimi sağlamak için yapılandırabilirsiniz. Ayrıca, seçili sanal ağlardan gelen trafiğin depolama hesabınıza erişimine izin vermek için ağ kurallarını yapılandırabilirsiniz. Seçilen IP adresi aralıklarından ve aynı depolama hesabındaki seçili sanal ağlardan erişim sağlayan ağ kurallarından erişime izin veren IP güvenlik duvarı kurallarını birleştirebilirsiniz. Bu kurallar, bir depolama hesabının genel uç noktası için geçerlidir. Çalışma alanınızda oluşturulan yönetilen özel uç noktalardan bir depolama hesabına giden trafiğe izin vermek için herhangi bir erişim kuralına gerek yoktur. Depolama güvenlik duvarı kuralları, mevcut depolama hesaplarına veya bunları oluştururken yeni depolama hesaplarına uygulanabilir. Depolama [hesabınızın güvenliğini](../../storage/common/storage-network-security.md)sağlama hakkında daha fazla bilgi edinebilirsiniz.

## <a name="synapse-workspaces-and-virtual-networks"></a>SYNAPSE çalışma alanları ve sanal ağlar
Bir Synapse çalışma alanı oluşturduğunuzda, yönetilen bir sanal ağın kendisiyle ilişkilendirilmesi için etkinleştirmeyi seçebilirsiniz. Oluşturduğunuz çalışma alanınız için yönetilen sanal ağı etkinleştirmezseniz, çalışma alanınız paylaşılan bir sanal ağ ve onunla ilişkili bir yönetilen sanal ağı olmayan diğer SYNAPSE çalışma alanlarıyla birlikte bulunur. Çalışma alanını oluştururken yönetilen sanal ağı etkinleştirdiyseniz, çalışma alanınız Azure SYNAPSE tarafından yönetilen ayrılmış bir sanal ağla ilişkilendirilir. Bu sanal ağlar, müşteri aboneliğinizde oluşturulmaz. Bu nedenle, yukarıda açıklanan ağ kurallarını kullanarak bu sanal ağlardan gelen trafiğin güvenli depolama hesabınıza erişimine izin vermeniz mümkün olmayacaktır.  

## <a name="access-a-secured-storage-account"></a>Güvenli depolama hesabına erişme
SYNAPSE, ağ kurallarınıza dahil edilemez ağlardan çalışır. Çalışma alanınızdan güvenli depolama hesabınıza erişimi etkinleştirmek için aşağıdakilerin yapılması gerekir.

* Yönetilen bir sanal ağla ilişkili bir Azure SYNAPSE çalışma alanı oluşturun ve bu bilgisayardan güvenli depolama hesabına yönetilen özel uç noktalar oluşturun
* Azure SYNAPSE çalışma alanınızın güvenli depolama hesabınıza güvenilir bir Azure hizmeti olarak erişmesine izin verin. Güvenilen bir hizmet olarak, Azure SYNAPSE, depolama hesabınıza güvenli bir şekilde bağlanmak için güçlü kimlik doğrulama kullanacaktır.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Yönetilen bir sanal ağ ile bir Synapse çalışma alanı oluşturun ve depolama hesabınızda yönetilen özel uç noktalar oluşturun
Onunla ilişkili yönetilen bir sanal ağa sahip bir Synapse çalışma alanı oluşturmak için [Bu adımları](./synapse-workspace-managed-vnet.md) izleyebilirsiniz. İlişkili bir yönetilen sanal ağa sahip çalışma alanı oluşturulduktan sonra, [burada](./how-to-create-managed-private-endpoints.md)listelenen adımları izleyerek güvenli depolama hesabınıza yönetilen bir özel uç nokta oluşturabilirsiniz. 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Azure SYNAPSE çalışma alanınızın güvenli depolama hesabınıza güvenilir bir Azure hizmeti olarak erişmesine izin verin
Adanmış SQL havuzu ve sunucusuz SQL havuzu gibi analitik yetenekler, yönetilen sanal ağa dağıtılmamış çok kiracılı altyapıyı kullanır. Bu yetenekten gelen trafiğin güvenli depolama hesabına erişmesi için aşağıdaki adımları izleyerek çalışma alanının sistem tarafından atanan yönetilen kimliğine göre depolama hesabınıza erişimi yapılandırmanız gerekir.

Azure portal ' de, güvenli depolama hesabınıza gidin. Sol gezinti bölmesinden **ağ** ' ı seçin. **Kaynak örnekleri** bölümünde, **kaynak türü** olarak *Microsoft. SYNAPSE/Workspaces* ' ı seçin ve **örnek adı** için çalışma alanı adınızı girin. **Kaydet**’i seçin.

![Depolama hesabı ağ yapılandırması.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

Artık, güvenli depolama hesabınıza çalışma alanından erişebilmelisiniz.


## <a name="next-steps"></a>Sonraki adımlar

[Yönetilen çalışma alanı sanal ağı](./synapse-workspace-managed-vnet.md)hakkında daha fazla bilgi edinin.

[Yönetilen özel uç noktalar](./synapse-workspace-managed-private-endpoints.md)hakkında daha fazla bilgi edinin.