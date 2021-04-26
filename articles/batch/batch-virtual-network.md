---
title: Sanal ağda havuz sağlama
description: Bir Azure sanal ağında, işlem düğümlerinin bir dosya sunucusu gibi ağdaki diğer VM 'lerle güvenli bir şekilde iletişim kurabilmesi için bir Batch havuzu oluşturma.
ms.topic: how-to
ms.date: 03/26/2021
ms.custom: seodec18
ms.openlocfilehash: 7213637e89cfccd1352861002c47a696d942d30f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629317"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Sanal ağda Azure Batch havuzu oluşturma

Bir Azure Batch havuzu oluşturduğunuzda, havuzu belirttiğiniz bir [Azure sanal ağının](../virtual-network/virtual-networks-overview.md) (VNet) alt ağında sağlayabilirsiniz. Bu makalede, bir sanal ağda toplu havuzun nasıl ayarlanacağı açıklanır.

## <a name="why-use-a-vnet"></a>Neden VNet kullanmalıyım?

Bir havuzdaki işlem düğümleri, farklı bir VNet gerekmeden, çok örnekli görevleri çalıştırmak gibi birbirleriyle iletişim kurabilir. Ancak, varsayılan olarak, bir havuzdaki düğümler, havuz dışında olan ve lisans sunucuları veya dosya sunucuları gibi sanal makinelerle iletişim kuramaz.

İşlem düğümlerinin diğer sanal makinelerle veya şirket içi bir ağla güvenli bir şekilde iletişim kurmasına izin vermek için, havuzu bir Azure VNet 'in alt ağında sağlayabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- **Kimlik doğrulaması**. Azure sanal ağı kullanmak için Batch istemci API'sinin Azure Active Directory (AD) kimlik doğrulamasını kullanması gerekir. Azure AD için Azure Batch desteği, [Batch hizmeti çözümlerinin kimliğini Active Directory ile doğrulama](batch-aad-auth.md) makalesinde belirtilmiştir.

- **Bir Azure sanal ağı**. VNet gereksinimleri ve yapılandırması için aşağıdaki bölüme bakın. Bir sanal ağı bir veya daha fazla alt ağ ile önceden hazırlamak için Azure portal, Azure PowerShell, Azure Command-Line arabirimi (CLı) veya diğer yöntemleri kullanabilirsiniz.
  - Azure Resource Manager tabanlı VNet oluşturmak için, bkz. [sanal ağ oluşturma](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Yeni dağıtımlar için Kaynak Yöneticisi tabanlı VNet önerilir ve yalnızca sanal makine yapılandırması kullanan havuzlarda desteklenir.
  - Klasik VNet oluşturmak için, bkz. [birden çok alt ağ ile sanal ağ (klasik) oluşturma](/previous-versions/azure/virtual-network/create-virtual-network-classic). Klasik VNet yalnızca Cloud Services yapılandırma kullanan havuzlarda desteklenir.

## <a name="vnet-requirements"></a>Sanal ağ gereksinimleri

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Azure portal VNet ile havuz oluşturma

VNet 'nizi oluşturduktan ve bu ağa bir alt ağ atadıktan sonra, bu VNet ile bir Batch havuzu oluşturabilirsiniz. Azure portal bir havuz oluşturmak için aşağıdaki adımları izleyin: 

1. Azure portalında Batch hesabınıza gidin. Bu hesabın, kullanmayı düşündüğünüz VNet 'i içeren kaynak grubuyla aynı abonelikte ve bölgede olması gerekir.
2. Soldaki **Ayarlar** penceresinde **havuzlar** menü öğesini seçin.
3. **Havuzlar** penceresinde **Ekle**' yi seçin.
4. **Havuz Ekle** penceresinde, **görüntü türü** açılan menüsünden kullanmayı düşündüğünüz seçeneği belirleyin.
5. Özel görüntünüz için doğru **yayımcıyı/teklifi/SKU 'yu** seçin.
6. **Düğüm boyutu**, **hedef adanmış düğümler** ve **düşük öncelikli düğümlerin** yanı sıra istediğiniz isteğe bağlı ayarları da kapsayan, kalan gerekli ayarları belirtin.
7. **Sanal ağ**' da, kullanmak istediğiniz sanal ağı ve alt ağı seçin.

   ![Sanal ağ ile havuz ekleme](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Zorlamalı tünel için Kullanıcı tanımlı yollar

Kuruluşunuzdaki internet 'e bağlı trafiği İnceleme ve günlüğe kaydetme amacıyla Şirket içi konumunuza yeniden yönlendirmek (zorlamak) için gereken gereksinimlere sahip olabilirsiniz. Ayrıca, sanal ağınızdaki alt ağlar için Zorlamalı tünel oluşturmayı etkinleştirmiş olabilirsiniz.

Havuzunuzdaki düğümlerin Zorlamalı tünel etkin olan bir VNet 'te çalıştığından emin olmak için, bu alt ağ için aşağıdaki [Kullanıcı tanımlı yolları](../virtual-network/virtual-networks-udr-overview.md) (UDR) eklemeniz gerekir:

- Batch hizmetinin, görevleri zamanlamak için düğümlerle iletişim kurması gerekir. Bu iletişimi etkinleştirmek için Batch hesabınızın bulunduğu bölgedeki Batch hizmeti tarafından kullanılan her IP adresi için bir UDR ekleyin. Batch hizmetinin IP adreslerinin listesini almak için bkz. [Şirket Içi hizmet etiketleri](../virtual-network/service-tags-overview.md).

- Azure Storage 'a giden trafiğin (özellikle, formun URL 'Leri, `<account>.table.core.windows.net` `<account>.queue.core.windows.net` ve `<account>.blob.core.windows.net` ) Şirket içi ağınız tarafından engellenmediğinden emin olun.

- Sanal dosya takmaları kullanıyorsanız, [ağ gereksinimlerini](virtual-file-mount.md#networking-requirements) gözden geçirin ve gerekli trafiğin engellenmediğinden emin olun.

Bir UDR eklediğinizde, ilgili her Batch IP adresi ön eki için yolu tanımlayın ve **sonraki atlama türünü** **Internet** olarak ayarlayın.

![Kullanıcı tanımlı yol](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> Batch hizmeti IP adresleri zamanla değişebilir. Bir IP adresi değişikliği nedeniyle kesintileri önlemek için, Batch hizmeti IP adreslerini otomatik olarak yenilemek ve yol tablonuzda güncel tutmak için bir işlem oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

- [Batch hizmeti iş akışı ve](batch-service-workflow-features.md) havuzlar, düğümler, işler ve görevler gibi birincil kaynaklar hakkında bilgi edinin.
- [Azure Portal Kullanıcı tanımlı bir yol oluşturmayı](../virtual-network/tutorial-create-route-table-portal.md)öğrenin.
