---
title: Azure Cosmos DB hesaplarıyla çalışma
description: Bu makalede Azure Cosmos DB hesaplarının nasıl oluşturulduğu ve kullanılacağı açıklanmaktadır
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 57b2cffbf8085e6d393765190a6dfc2dead51625
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757085"
---
# <a name="work-with-azure-cosmos-account"></a>Azure Cosmos hesabıyla çalışma

Azure Cosmos DB, tam olarak yönetilen bir hizmet olarak platform (PaaS). Azure Cosmos DB kullanmaya başlamak için başlangıçta Azure aboneliğinizde bir Azure Cosmos hesabı oluşturmanız gerekir. Azure Cosmos hesabınız benzersiz bir DNS adı içerir ve Azure portal, Azure CLı veya dile özgü farklı SDK 'Ları kullanarak bir hesabı yönetebilirsiniz. Daha fazla bilgi için bkz. [Azure Cosmos hesabınızı yönetme](how-to-manage-database-account.md).

Azure Cosmos hesabı, genel dağıtım ve yüksek kullanılabilirliğin temel birimidir. Verilerinizi ve aktarım hızını birden çok Azure bölgesinde küresel olarak dağıtmak için, Azure bölgelerini istediğiniz zaman Azure Cosmos hesabınıza ekleyebilir ve kaldırabilirsiniz. Azure Cosmos hesabınızı, tek veya birden fazla yazma bölgesi olacak şekilde yapılandırabilirsiniz. Daha fazla bilgi için bkz. Azure [bölgelerini Azure Cosmos hesabınıza ekleme ve kaldırma](how-to-manage-database-account.md). [Varsayılan tutarlılık](consistency-levels.md) düzeyini Azure Cosmos hesabında yapılandırabilirsiniz. Azure Cosmos DB üretilen iş hacmi, 99. yüzdebirlik, tutarlılık ve yüksek kullanılabilirliğe sahip kapsamlı SLA 'Lar sağlar. Daha fazla bilgi için bkz. [SLA 'lar Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos hesabınızda bulunan tüm verilere erişimi güvenli bir şekilde yönetmek için hesabınızla ilişkili [ana anahtarları](secure-access-to-data.md) kullanabilirsiniz. Verilerinize daha güvenli bir şekilde erişmek için Azure Cosmos hesabınızda bir [sanal ağ hizmeti uç noktası](vnet-service-endpoint.md) ve [IP-güvenlik duvarı](firewall-support.md) yapılandırabilirsiniz. 

## <a name="elements-in-an-azure-cosmos-account"></a>Azure Cosmos hesabındaki öğeler

Azure Cosmos kapsayıcısı, temel ölçeklenebilirlik birimidir. Kapsayıcıda sınırsız bir sağlanan üretilen iş (RU/s) ve depolama alanı oluşturabilirsiniz. Azure Cosmos DB, sağlanan aktarım hızını ve depolamayı esnek ölçeklendirmek için belirttiğiniz mantıksal bölüm anahtarını kullanarak kapsayıcınızı saydam olarak bölümlendirir. Daha fazla bilgi için bkz. [Azure Cosmos kapsayıcıları ve öğeleriyle çalışma](databases-containers-items.md).

Şu anda, bir Azure aboneliği altında en fazla 100 Azure Cosmos hesabı oluşturabilirsiniz. Tek bir Azure Cosmos hesabı, sınırsız miktarda veriyi ve sağlanan aktarım hızını neredeyse yönetebilir. Verilerinizi ve sağlanan aktarım hızını yönetmek için, hesabınız altında ve bu veritabanı içinde bir veya daha fazla Azure Cosmos veritabanı oluşturabilir, bir veya daha fazla kapsayıcı oluşturabilirsiniz. Aşağıdaki görüntüde, bir Azure Cosmos hesabındaki öğelerin hiyerarşisi gösterilmektedir:

![Azure Cosmos hesabının hiyerarşisi](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos hesabınızı ve diğer kavramları yönetmeyi öğrenin:

* [Azure Cosmos hesabınızı yönetme](how-to-manage-database-account.md)
* [Genel dağıtım](distribute-data-globally.md)
* [Tutarlılık düzeyleri](consistency-levels.md)
* [Azure Cosmos kapsayıcıları ve öğeleriyle çalışma](databases-containers-items.md)
* [Azure Cosmos hesabınız için VNET hizmeti uç noktası](vnet-service-endpoint.md)
* [Azure Cosmos hesabınız için IP-güvenlik duvarı](firewall-support.md)
* [Azure Cosmos hesabınıza Azure bölgelerini ekleme ve kaldırma](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA 'Lar](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
