---
title: Yönetilen özel uç noktalar
description: Azure SYNAPSE Analytics 'te yönetilen özel uç noktaları açıklayan bir makale
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 65794c695fa4b36586b23a308845b1f12a20b7cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98569955"
---
# <a name="synapse-managed-private-endpoints"></a>SYNAPSE yönetilen özel uç noktalar

Bu makalede, Azure SYNAPSE Analytics 'te yönetilen özel uç noktalar açıklanmaktadır.

## <a name="managed-private-endpoints"></a>Yönetilen özel uç noktalar

Yönetilen özel uç noktalar, Azure SYNAPSE çalışma alanınız ile ilişkili bir yönetilen sanal ağda oluşturulan özel uç noktalardır. Yönetilen özel uç noktalar Azure kaynaklarına özel bir bağlantı kurar. Azure SYNAPSE, bu özel uç noktaları sizin adınıza yönetir. Azure hizmetlerine (Azure depolama veya Azure Cosmos DB) ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine erişmek için Azure SYNAPSE çalışma alanınızdan yönetilen özel uç noktalar oluşturabilirsiniz.

Yönetilen özel uç noktaları kullandığınızda, Azure SYNAPSE çalışma alanınız ve diğer Azure kaynakları arasındaki trafik tümüyle Microsoft omurga ağı üzerinden çapraz geçiş yapar. Yönetilen özel uç noktalar veri taşmalarına karşı koruma altına alınmıştır. Yönetilen bir özel uç nokta, Azure hizmeti 'nin sanal ağınızla iletişim kurduğu Azure hizmetini etkin bir şekilde getirmek için yönetilen sanal ağınızdan özel IP adresi kullanır. Yönetilen özel uç noktalar, Azure 'daki belirli bir kaynakla eşlenir ve hizmetin tamamı değildir. Müşteriler, şirket tarafından onaylanan belirli bir kaynakla bağlantıyı sınırlayabilir. 

[Özel bağlantılar ve özel uç noktalar](../../private-link/index.yml)hakkında daha fazla bilgi edinin.

>[!IMPORTANT]
>Yönetilen özel uç noktalar yalnızca yönetilen çalışma alanı sanal ağı olan Azure SYNAPSE çalışma alanlarında desteklenir.

>[!NOTE]
>Bir Azure SYNAPSE çalışma alanı oluştururken, yönetilen bir sanal ağı onunla ilişkilendirmeyi seçebilirsiniz. Çalışma alanınız ile ilişkili bir yönetilen sanal ağ olmasını seçerseniz, çalışma alanınızdan giden trafiği yalnızca onaylanan hedeflere sınırlamayı tercih edebilirsiniz. Bu hedeflere yönetilen özel uç noktalar oluşturmanız gerekir. 


Azure SYNAPSE 'de yönetilen özel uç nokta oluşturduğunuzda bir "bekleyen" durumunda özel bir uç nokta bağlantısı oluşturulur. Bir onay iş akışı başlatılır. Özel bağlantı kaynağı sahibi bağlantıyı onaylaması veya reddetmekten sorumludur. Sahip bağlantıyı onayladığında, özel bağlantı oluşturulur. Ancak, sahip bağlantıyı onaylamazsa özel bağlantı kurulamayacaktır. Her iki durumda da, yönetilen özel uç nokta bağlantının durumuyla güncelleştirilir. Yönetilen özel uç noktaya bağlı özel bağlantı kaynağına trafik göndermek için yalnızca onaylanan durumda olan yönetilen özel bir uç nokta kullanılabilir.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>Adanmış SQL havuzu ve sunucusuz SQL havuzu için yönetilen özel uç noktalar

Adanmış SQL havuzu ve sunucusuz SQL havuzu, Azure SYNAPSE çalışma alanınızdaki analitik yeteneklerdir. Bu yetenekler, [yönetilen çalışma alanı sanal ağına](./synapse-workspace-managed-vnet.md)dağıtılmamış çok kiracılı altyapıyı kullanır.

Bir çalışma alanı oluşturulduğunda, Azure SYNAPSE çalışma alanında, biri adanmış SQL havuzu ve sunucusuz SQL havuzu için olmak üzere iki yönetilen özel uç nokta oluşturur. 

Bu iki yönetilen özel uç nokta SYNAPSE Studio 'da listelenmiştir. Sol gezinti bölmesinde **Yönet** ' i seçin ve ardından bu dosyaları Studio 'da görmek için **yönetilen özel uç noktalar** ' ı seçin.

SQL havuzunu hedefleyen yönetilen özel uç nokta *SYNAPSE-WS- \<workspacename\> SQL* olarak adlandırılır ve sunucusuz SQL havuzunu hedefleyen bir tane *SYNAPSE-WS-sqlondemand-- \<workspacename\>* olarak adlandırılır.

![Adanmış SQL havuzu ve sunucusuz SQL havuzu için yönetilen özel uç noktalar](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Bu iki yönetilen özel uç nokta, Azure SYNAPSE çalışma alanınızı oluştururken sizin için otomatik olarak oluşturulur. Bu iki yönetilen özel uç nokta için ücret ödemeniz gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinmek için [veri kaynaklarınıza yönetilen özel uç noktalar oluşturma](./how-to-create-managed-private-endpoints.md) makalesine ilerleyin.