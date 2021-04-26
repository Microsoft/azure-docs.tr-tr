---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d2e6eb6f38ed85a6b943653ed3e1464ebe629799
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873100"
---
|Name<br /><sub>(Azure portal)</sub> |Description |Efekt (ler) |Sürüm<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MariaDB için Azure veritabanı 'nda coğrafi olarak yedekli yedekleme etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |MariaDB için Azure veritabanı, veritabanı sunucunuz için artıklık seçeneğini seçmenize olanak sağlar. Verilerin yalnızca sunucunuzun barındırıldığı bölgede depolanmadığı, ancak aynı zamanda bir bölge hatası durumunda kurtarma seçeneği sağlamak için eşlenmiş bir bölgeye çoğaltılan coğrafi olarak yedekli bir yedekleme depolamasına ayarlanabilir. Yedekleme için coğrafi olarak yedekli depolamayı yapılandırmaya yalnızca sunucu oluşturma sırasında izin verilir. |Denetim, devre dışı |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB sunucusu bir sanal ağ hizmeti uç noktası kullanmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Sanal ağ tabanlı güvenlik duvarı kuralları, trafiğin Azure sınırları içinde kalmasını sağlarken, belirli bir alt ağdan MariaDB için Azure veritabanı 'na trafik sağlamak üzere kullanılır. Bu ilke, MariaDB için Azure veritabanı 'nın, kullanılmakta olan sanal ağ hizmeti uç noktası olup olmadığını denetlemek için bir yol sağlar. |Auditınotexists, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[MariaDB sunucuları için özel uç nokta etkinleştirilmelidir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Özel uç nokta bağlantıları, MariaDB için Azure veritabanı 'na özel bağlantı etkinleştirerek güvenli iletişim uygular. Yalnızca bilinen ağlardan gelen trafiğe erişimi etkinleştirmek ve Azure içinde olmak üzere diğer tüm IP adreslerinden erişimi engellemek için özel bir uç nokta bağlantısı yapılandırın. |Auditınotexists, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[MariaDB sunucuları için genel ağ erişimi devre dışı bırakılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Güvenliği artırmak için ortak ağ erişim özelliğini devre dışı bırakın ve MariaDB için Azure veritabanınızın yalnızca özel bir uç noktadan erişilebildiğinden emin olun. Bu yapılandırma, Azure IP aralığı dışındaki tüm ortak adres alanından erişimi tamamen devre dışı bırakır ve IP veya sanal ağ tabanlı güvenlik duvarı kurallarıyla eşleşen tüm oturum açmaları reddeder. |Denetim, devre dışı |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
