---
title: Sanal ağda havuz Sağlama-Azure Batch | Microsoft Docs
description: Bir Azure sanal ağında, işlem düğümlerinin bir dosya sunucusu gibi ağdaki diğer VM 'lerle güvenli bir şekilde iletişim kurabilmesi için bir Batch havuzu oluşturma.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 78f29bacaadac5f01e4a8dd26bf03b2bda84f2bf
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577573"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Sanal ağda Azure Batch havuzu oluşturma

Bir Azure Batch havuzu oluşturduğunuzda, havuzu belirttiğiniz bir [Azure sanal ağının](../virtual-network/virtual-networks-overview.md) (VNet) alt ağında sağlayabilirsiniz. Bu makalede, bir sanal ağda toplu havuzun nasıl ayarlanacağı açıklanır. 

## <a name="why-use-a-vnet"></a>Neden VNet kullanmalıyım?

Azure Batch havuzun, işlem düğümlerinin birbirleriyle iletişim kurmasına izin vermek için ayarları vardır. Örneğin, çok örnekli görevleri çalıştırmak için. Bu ayarlar ayrı bir sanal ağ gerektirmez. Ancak, varsayılan olarak, düğümler, bir lisans sunucusu ya da bir dosya sunucusu gibi Batch havuzunun parçası olmayan sanal makinelerle iletişim kuramaz. Havuz işlem düğümlerinin diğer sanal makinelerle veya şirket içi bir ağla güvenli bir şekilde iletişim kurmasına izin vermek için, havuzu bir Azure VNet 'in alt ağında sağlayabilirsiniz. 

## <a name="prerequisites"></a>Ön koşullar

* **Kimlik Doğrulaması**. Azure sanal ağı kullanmak için Batch istemci API'sinin Azure Active Directory (AD) kimlik doğrulamasını kullanması gerekir. Azure AD için Azure Batch desteği, [Batch hizmeti çözümlerinin kimliğini Active Directory ile doğrulama](batch-aad-auth.md) makalesinde belirtilmiştir. 

* **Bir Azure sanal ağı**. VNet gereksinimleri ve yapılandırması için aşağıdaki bölüme bakın. Bir sanal ağı bir veya daha fazla alt ağ ile önceden hazırlamak için Azure portal, Azure PowerShell, Azure komut satırı arabirimi (CLı) veya diğer yöntemleri kullanabilirsiniz.  
  * Azure Resource Manager tabanlı VNet oluşturmak için, bkz. [sanal ağ oluşturma](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Yeni dağıtımlar için Kaynak Yöneticisi tabanlı VNet önerilir ve yalnızca sanal makine yapılandırmasındaki havuzlar üzerinde desteklenir.
  * Klasik VNet oluşturmak için, bkz. [birden çok alt ağ ile sanal ağ (klasik) oluşturma](../virtual-network/create-virtual-network-classic.md). Klasik VNet yalnızca Cloud Services yapılandırmasındaki havuzlarda desteklenir.

## <a name="vnet-requirements"></a>Sanal ağ gereksinimleri

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Portalda VNet ile havuz oluşturma

VNet 'nizi oluşturduktan ve bu ağa bir alt ağ atadıktan sonra, bu VNet ile bir Batch havuzu oluşturabilirsiniz. Azure portal bir havuz oluşturmak için aşağıdaki adımları izleyin: 

1. Azure portalında Batch hesabınıza gidin. Bu hesabın, kullanmayı düşündüğünüz VNet 'i içeren kaynak grubuyla aynı abonelikte ve bölgede olması gerekir. 
2. Soldaki **Ayarlar** penceresinde **havuzlar** menü öğesini seçin.
3. **Havuzlar** penceresinde **Ekle** komutunu seçin.
4. **Havuz Ekle** penceresinde, **görüntü türü** açılan menüsünden kullanmayı düşündüğünüz seçeneği belirleyin. 
5. Özel görüntünüz için doğru **yayımcıyı/teklifi/SKU 'yu** seçin.
6. **Düğüm boyutu**, **hedef adanmış düğümler**ve **düşük öncelikli düğümlerin**yanı sıra istediğiniz isteğe bağlı ayarları da kapsayan, kalan gerekli ayarları belirtin.
7. **Sanal ağ**' da, kullanmak istediğiniz sanal ağı ve alt ağı seçin.
  
   ![Sanal ağ ile havuz ekleme](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Zorlamalı tünel için Kullanıcı tanımlı yollar

Kuruluşunuzdaki Internet 'e bağlı trafiği İnceleme ve günlüğe kaydetme amacıyla Şirket içi konumunuza yeniden yönlendirmek (zorlamak) için gereken gereksinimlere sahip olabilirsiniz. VNet 'iniz içindeki alt ağlar için Zorlamalı tünel oluşturmayı etkinleştirmiş olabilirsiniz. 

Azure Batch havuzu işlem düğümlerinizin Zorlamalı tünel etkin olan bir VNet 'te çalıştığından emin olmak için, bu alt ağ için aşağıdaki [Kullanıcı tanımlı yolları](../virtual-network/virtual-networks-udr-overview.md) eklemeniz gerekir:

* Batch hizmetinin, görevleri zamanlamak için havuz işlem düğümleriyle iletişim kurması gerekir. Bu iletişimi etkinleştirmek için Batch hesabınızın bulunduğu bölgedeki Batch hizmeti tarafından kullanılan her IP adresi için Kullanıcı tanımlı bir yol ekleyin. Batch hizmetinin IP adreslerinin listesini edinme hakkında bilgi edinmek için bkz. [Şirket Içi hizmet etiketleri](../virtual-network/service-tags-overview.md)

* Azure Storage 'a giden trafiğin (özellikle, `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`ve `<account>.blob.core.windows.net`) Şirket içi ağ gereciniz aracılığıyla engellenmediğinden emin olun.

Kullanıcı tanımlı bir yol eklediğinizde, ilgili her Batch IP adresi ön eki için yolu tanımlayın ve **sonraki atlama türünü** **Internet**olarak ayarlayın. Aşağıdaki örneğe bakın:

![Kullanıcı tanımlı yol](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Sonraki adımlar

- Toplu Işe yönelik ayrıntılı genel bakış için bkz. [Batch ile büyük ölçekli paralel işlem çözümleri geliştirme](batch-api-basics.md).
- Kullanıcı tanımlı yol oluşturma hakkında daha fazla bilgi için bkz. [Kullanıcı tanımlı yol oluşturma-Azure Portal](../virtual-network/tutorial-create-route-table-portal.md).
