---
title: Yönetilen örnek özel DNS
description: Bu konu, Azure SQL veritabanı yönetilen örneği ile özel bir DNS için yapılandırma seçeneklerini açıklamaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 3161d931c6e912c05c595db7f3c790da454dd5ed
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823349"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Azure SQL veritabanı yönetilen örneği için özel DNS yapılandırma

Azure SQL veritabanı yönetilen örneği bir Azure [sanal ağı (VNet)](../virtual-network/virtual-networks-overview.md)içinde dağıtılmalıdır. Yönetilen örnekten özel ana bilgisayar adlarının çözümlenemesinin gerekli olduğu birkaç senaryo (örneğin, DB posta, bağlı sunucular, bulut veya karma ortamınızdaki diğer SQL örneklerine) vardır. Bu durumda, Azure içinde özel bir DNS yapılandırmanız gerekir. 

Yönetilen örnek, iç işleyişi için aynı DNS 'yi kullandığından, özel DNS sunucusunu ortak etki alanı adlarını çözümleyebilecek şekilde yapılandırın.

> [!IMPORTANT]
> Her zaman posta sunucusu için tam etki alanı adı (FQDN), SQL Server örneği ve diğer hizmetler için, özel DNS bölgeniz içinde olsalar bile kullanın. Örneğin, `smtp` doğru çözümleyemeyeceği için posta sunucunuz için `smtp.contoso.com` kullanın. Aynı sanal ağ içinde SQL VM 'lerine bağlı sunucu veya çoğaltma oluşturmak için Ayrıca bir FQDN ve varsayılan DNS soneki gerekir. Örneğin, `SQLVM.internal.cloudapp.net`. Daha fazla bilgi için, bkz. [kendı DNS sunucunuzu kullanan ad çözümlemesi](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Sanal ağ DNS sunucularının güncelleştirilmesi, yönetilen örneği hemen etkilemez. Yönetilen örnek DNS yapılandırması, DHCP kiralama süresi dolduktan sonra veya platform upgarade sonrasında, hangisi önce gerçekleşirse güncellenir. **Kullanıcıların, ilk yönetilen örneğini oluşturmadan önce sanal ağ DNS yapılandırmalarını ayarlaması önerilir.**

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [yönetilen örnek nedir?](sql-database-managed-instance.md)
- Yeni bir yönetilen örnek oluşturma hakkında bir öğretici için bkz. [yönetilen örnek oluşturma](sql-database-managed-instance-get-started.md).
- Bir sanal ağı yönetilen bir örnek için yapılandırma hakkında bilgi için bkz. [yönetilen örnekler Için VNET yapılandırması](sql-database-managed-instance-connectivity-architecture.md)
