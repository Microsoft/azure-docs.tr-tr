---
title: Azure App Service kullanarak PaaS Web ve mobil uygulamaları güvenli hale getirme | Microsoft Docs
description: 'PaaS Web ve mobil uygulamalarınızın güvenliğini sağlamaya yönelik en iyi güvenlik uygulamalarını Azure App Service öğrenin. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: 4967f6f7831f1f09a502b935342fcd752af72fce
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999159"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Azure App Service kullanarak PaaS Web ve mobil uygulamaları güvenli hale getirmek için en iyi yöntemler

Bu makalede, PaaS Web ve mobil uygulamalarınızın güvenliğini sağlamaya yönelik [Azure App Service](/azure/app-service/overview) en iyi güvenlik uygulamaları hakkında bir koleksiyon ele alınmaktadır. Bu en iyi uygulamalar, Azure deneyimimizden ve sizin gibi müşterilerin deneyimlerini elde edilmiştir.

Azure App Service, herhangi bir platform veya cihaz için Web uygulamaları ve mobil uygulamalar oluşturmanıza ve bulutta veya şirket içinde her yerden verilere bağlanmanıza olanak tanıyan bir hizmet olarak platform (PaaS) sunumudur. App Service, daha önce Azure Web siteleri ve Azure Mobile Services olarak ayrı olarak sunulan Web ve mobil özellikleri içerir. Ayrıca iş süreçlerini otomatikleştirmek ve bulut API'leri barındırmak için yeni özellikler içerir. Tek bir tümleşik hizmet olarak, App Service Web, mobil ve tümleştirme senaryolarına zengin bir özellik kümesi sunar.

## <a name="authenticate-through-azure-active-directory-ad"></a>Azure Active Directory ile kimlik doğrulama (AD)
App Service, kimlik sağlayıcınız için bir OAuth 2,0 hizmeti sağlar. OAuth 2,0, Web uygulamaları, masaüstü uygulamaları ve cep telefonları için belirli yetkilendirme akışları sağlarken istemci geliştiricisi basitliği üzerine odaklanır. Azure AD, mobil uygulamalar ve Web uygulamalarına erişim yetkisi vermek için OAuth 2,0 kullanır. Daha fazla bilgi için bkz. [Azure App Service kimlik doğrulaması ve yetkilendirme](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Role göre erişimi kısıtla
Erişim kısıtlama, veri erişimi için güvenlik ilkeleri zorlamak isteyen kuruluşlar için zorunludur. Rol tabanlı erişim denetimi 'ni (RBAC), belirli bir kapsamdaki kullanıcılara, gruplara ve uygulamalara izin atamak için (örneğin, bilmeniz gerekenler ve en az ayrıcalık güvenliği ilkeleri) kullanabilirsiniz. Kullanıcılara uygulamalara erişim verme hakkında daha fazla bilgi edinmek için bkz. [rol tabanlı erişim denetimi nedir?](/azure/role-based-access-control/overview).

## <a name="protect-your-keys"></a>Anahtarlarınızı koruyun
Abonelik anahtarlarınızı kaybetmeniz durumunda güvenliğin ne kadar iyi olduğunu farketmez. Azure Anahtar Kasası, bulut uygulamaları ve hizmetleri tarafından kullanılan şifreleme anahtarlarının ve gizli anahtarların korunmasına yardımcı olur. Key Vault, anahtarları ve gizli dizileri (kimlik doğrulaması anahtarları, depolama hesabı anahtarları, veri şifreleme anahtarları,) şifreleyebilirsiniz. PFX dosyaları ve parolalar), donanım güvenlik modülleri (HSM 'ler) tarafından korunan anahtarları kullanarak. Ek güvenlik için HSM'lerde anahtarları içeri aktarabilir veya oluşturabilirsiniz. Otomatik yenilemeyle TLS sertifikalarınızı yönetmek için Key Vault de kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Key Vault nedir?](../../key-vault/key-vault-overview.md) .

## <a name="restrict-incoming-source-ip-addresses"></a>Gelen kaynak IP adreslerini kısıtla
[App Service ortamlarda](../../app-service/environment/intro.md) , gelen kaynak IP adreslerini ağ güvenlik grupları (NSG 'ler) aracılığıyla kısıtlayabilmenizi sağlayan bir sanal ağ tümleştirme özelliği bulunur. Azure sanal ağları (VNet 'ler) hakkında bilginiz varsa, bu, erişimi denetlediğiniz internet olmayan, yönlendirilebilir bir ağa Azure kaynaklarınızın çoğunu yerleştirmenizi sağlayan bir özelliktir. Daha fazla bilgi edinmek için bkz. [uygulamanızı bir Azure sanal ağı Ile tümleştirme](../../app-service/web-sites-integrate-with-vnet.md).

Windows üzerinde App Service için, Web. config 'i yapılandırarak IP adreslerini dinamik olarak da kısıtlayabilirsiniz. Daha fazla bilgi için bkz. [dınamık IP güvenliği](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Sonraki adımlar
Bu makale, PaaS Web ve mobil uygulamalarınızın güvenliğini sağlamaya yönelik App Service en iyi güvenlik uygulamaları koleksiyonuna tanıtılmıştır. PaaS dağıtımlarınızın güvenliğini sağlama hakkında daha fazla bilgi edinmek için bkz.:

- [PaaS dağıtımlarının güvenliğini sağlama](paas-deployments.md)
- [Azure 'da PaaS veritabanlarının güvenliğini sağlama](paas-applications-using-sql.md)
