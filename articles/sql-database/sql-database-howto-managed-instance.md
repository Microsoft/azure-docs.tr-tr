---
title: Yönetilen örnek yapılandırma
description: Azure SQL veritabanı yönetilen örneğini yapılandırma ve yönetme hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: 906ae2a970ce1d5b82302d0277ca45bd93c23011
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811191"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Azure SQL veritabanı 'nda yönetilen örnek kullanma

Bu makalede, yönetilen örneğinizi yönetmenize ve yapılandırmanıza yardımcı olabilecek çeşitli kılavuzlar, betikler ve açıklamalar bulabilirsiniz.

## <a name="migration"></a>Geçiş

- [Yönetilen bir örneğe](sql-database-managed-instance-migrate.md) geçiş: yönetilen bir örneğe geçiş için önerilen geçiş işlemi ve araçları hakkında bilgi edinin.

- [TDE sertifikayı yönetilen örneğe geçirme](sql-database-managed-instance-migrate-tde-certificate.md) – SQL Server veritabanınız saydam veri şifrelemesi (tde) ile korunuyorsa, yönetilen bir örneğin Azure 'da geri yüklemek istediğiniz yedeğin şifresini çözmek için kullanabileceği sertifikayı geçirmeniz gerekir.

## <a name="network-configuration"></a>Ağ yapılandırması

- [Yönetilen örnek alt ağının boyutunu belirleme](sql-database-managed-instance-determine-size-vnet-subnet.md) – yönetilen örnek, içindeki kaynakları ekledikten sonra yeniden boyutlandırılanamaz alt ağa göre yerleştirilir. Bu nedenle, alt ağda dağıtmak istediğiniz örnek sayısına ve türlerine bağlı olarak alt ağ için hangi IP adresi aralığının gerekli olacağını hesaplamanız gerekir.
- [Yönetilen bir örnek için yeni VNET ve alt ağ oluşturma](sql-database-managed-instance-create-vnet-subnet.md) – yönetilen örneklerinizi dağıtmak Istediğiniz Azure sanal ağı ve alt ağı [burada açıklanan ağ gereksinimlerine](sql-database-managed-instance-connectivity-architecture.md#network-requirements)göre yapılandırılmalıdır. Bu kılavuzda, yeni VNet ve alt ağını, yönetilen örnekler için doğru şekilde oluşturmanın en kolay yolunu bulabilirsiniz.
- [Mevcut VNET ve alt ağı yönetilen bir örnek Için yapılandırma](sql-database-managed-instance-configure-vnet-subnet.md) – mevcut VNET ve alt ağınızı içinde yönetilen örnekleri dağıtmak üzere yapılandırmak istiyorsanız, [ağ gereksinimlerini](sql-database-managed-instance-connectivity-architecture.md#network-requirements) denetleyen betiği bulabilir ve şunları yapılandırabilirsiniz: gereksinimlere göre alt ağ.
- [Özel DNS yapılandırma](sql-database-managed-instance-custom-dns.md) – özel etki alanlarındaki dış kaynaklara, DB posta profillerinin bağlı sunucusu aracılığıyla yönetilen örneğinizden erişmek ISTIYORSANıZ özel DNS 'yi yapılandırmanız gerekir.
- [Eşitleme ağ yapılandırması](sql-database-managed-instance-sync-network-configuration.md) - [uygulamanızı bir Azure sanal ağı ile tümleştirseniz](../app-service/web-sites-integrate-with-vnet.md)de,&#39;yönetilen bir örneğe bağlantı kurabilirsiniz. Deneyebileceğiniz bir şey, hizmet planınız için ağ yapılandırmasını yenilememiz olabilir.
- [Yönetim uç noktası IP adresini bul](sql-database-managed-instance-find-management-endpoint-ip-address.md) – yönetilen örnek, yönetim amaçlarıyla genel uç noktasını kullanır. Yönetim uç noktasının IP adresini burada açıklanan betiği kullanarak belirleyebilirsiniz.
- [Yerleşik güvenlik duvarı korumasını doğrulama](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – yönetilen örnek, yalnızca gerekli bağlantı noktalarında trafiğe izin veren yerleşik güvenlik duvarıyla korunmaktadır. Bu kılavuzda açıklanan betiği kullanarak yerleşik güvenlik duvarı kurallarını denetleyebilir ve doğrulayabilirsiniz.
- [Uygulamaları bağlama](sql-database-managed-instance-connect-app.md) – yönetilen örnek, özel IP adresi ile kendi özel Azure sanal ağınıza yerleştirilir. Yönetilen örneğiniz için uygulamaları bağlamak üzere farklı desenler hakkında bilgi edinin.

## <a name="feature-configuration"></a>Özellik yapılandırması

- [İşlemsel çoğaltma](replication-with-sql-database-managed-instance.md) , verilerinizi yönetilen örnekler arasında veya şirket içi SQL Server yönetilen bir örneğe çoğaltmanıza olanak sağlar ve tam tersi de geçerlidir. Bu kılavuzda işlem çoğaltmasını kullanma ve yapılandırma hakkında daha fazla bilgi edinin.
- [Tehdit algılamayı yapılandırma](sql-database-managed-instance-threat-detection.md) – [TEHDIT algılama](sql-database-threat-detection-overview.md) , SQL ekleme veya şüpheli konumlardan erişim gibi çeşitli olası saldırıları ALGıLAYAN yerleşik bir Azure SQL veritabanı özelliğidir. Bu kılavuzda, yönetilen bir örnek için [tehdit algılamayı](sql-database-threat-detection-overview.md) etkinleştirme ve yapılandırma hakkında bilgi edinebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Tek veritabanları Için nasıl yapılır kılavuzlarından](sql-database-howto-single-database.md) daha fazla bilgi edinin