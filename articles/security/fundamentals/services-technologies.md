---
title: Azure Güvenlik Hizmetleri ve teknolojileri | Microsoft Docs
description: Bu makale, Azure Güvenlik Hizmetleri ve teknolojilerinin seçkin bir listesini sağlar.
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: terrylan
ms.openlocfilehash: 5914d5a54606f49f6fbd809a17e2b47375b58dcd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641970"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Azure 'da kullanılabilen güvenlik hizmetleri ve teknolojileri

Güncel ve gelecekteki Azure müşterilerine sahip tartışmalarda, genellikle Azure 'un sunabileceği güvenlikle ilgili tüm hizmetlerin ve teknolojilerin bir listesini mi yaşıyorsunuz? "

Bulut hizmeti sağlayıcısı seçeneklerini değerlendirirken, bu bilgilerin olması yararlı olacaktır. Bu nedenle, başlamanıza başlamak için bu listeyi sağladık.

Zaman içinde, bu liste Azure 'un yaptığı gibi değişir ve büyür. Güvenlikle ilgili hizmetlerimiz ve teknolojilerimizde güncel kalmak için bu sayfayı düzenli olarak kontrol ettiğinizden emin olun.

## <a name="general-azure-security"></a>Genel Azure güvenliği
|Hizmet|Açıklama|
|--------|--------|
|[Azure &nbsp; Güvenlik &nbsp; Merkezi](../../security-center/security-center-introduction.md)| Karma bulut iş yükleri arasında güvenlik yönetimi ve Gelişmiş tehdit koruması sağlayan bir bulut iş yükü koruma çözümüdür.|
|[Azure Key Vault](../../key-vault/general/overview.md)| Parolalar, bağlantı dizeleri ve uygulamalarınızın çalışır durumda tutulması için gereken diğer bilgiler için güvenli bir gizli anahtar deposu. |
|[Azure İzleyici günlükleri](../../azure-monitor/logs/log-query-overview.md)|Telemetri ve diğer verileri toplayan bir izleme hizmeti ve uygulamalarınız ve kaynaklarınız için operasyonel içgörüler sunmak üzere bir sorgu dili ve analiz altyapısı sağlar. , Tek başına veya güvenlik merkezi gibi diğer hizmetlerle kullanılabilir. |
|[Azure geliştirme ve test laboratuvarları](../../devtest-labs/devtest-lab-overview.md)|Geliştiricilerin ve Test edicilerin Azure 'da hızlı bir şekilde ortam oluşturmalarına yardımcı olan ve maliyeti en aza indirirken bir hizmet.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Depolama alanı güvenliği
|Hizmet|Açıklama|
|------|--------|
| [Azure &nbsp; depolama &nbsp; hizmeti &nbsp; şifrelemesi](../../storage/common/storage-service-encryption.md)|Verilerinizi Azure Storage 'da otomatik olarak şifreleyen bir güvenlik özelliği.   |
|[StorSimple şifrelenmiş karma depolama](../../storsimple/storsimple-ova-overview.md)| Şirket içi cihazlar ile Azure bulut depolama arasındaki depolama görevlerini yöneten tümleşik bir depolama çözümüdür.|
|[Azure Client-Side şifreleme](../../storage/common/storage-client-side-encryption.md)| Azure Storage 'a yüklemeden önce istemci uygulamaları içindeki verileri şifreleyen istemci tarafı şifreleme çözümü; Ayrıca, indirme sırasında verilerin şifresini çözer. |
| [Azure depolama paylaşılan erişim Imzaları](../../storage/common/storage-sas-overview.md)|Paylaşılan erişim imzası, Depolama hesabınızdaki kaynaklara temsilci erişimi sağlar.  |
|[Azure depolama hesabı anahtarları](../../storage/common/storage-account-create.md)| Depolama hesabına erişildiğinde kimlik doğrulama için kullanılan Azure depolama için erişim denetimi yöntemi. |
|[SMB 3,0 şifrelemesi ile Azure dosya paylaşımları](../../storage/files/storage-files-introduction.md)|Sunucu Ileti bloğu (SMB) dosya paylaşımı protokolü için otomatik ağ şifrelemeyi sağlayan bir ağ güvenlik teknolojisi. |
|[Azure Depolama Analizi](/rest/api/storageservices/Storage-Analytics)| Depolama hesabınızdaki veriler için bir günlüğe kaydetme ve ölçüm oluşturma teknolojisi. |

<!------>

## <a name="database-security"></a>Veritabanı güvenliği
|Hizmet|Açıklama|
|------|--------|
| [Azure &nbsp; SQL &nbsp; güvenlik duvarı](../../azure-sql/database/firewall-configure.md)|Veritabanına yönelik ağ tabanlı saldırılara karşı koruma sağlayan bir ağ erişim denetimi özelliği. |
|[Azure &nbsp; SQL &nbsp; hücre &nbsp; düzeyi şifreleme](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database)| Parçalı düzeyde şifreleme sağlayan bir veritabanı güvenlik teknolojisi.  |
| [Azure &nbsp; SQL &nbsp; bağlantı şifrelemesi](../../azure-sql/database/logins-create-manage.md)|SQL Veritabanı güvenliği sağlamak için erişimi IP adresine göre bağlantıyı sınırlayan güvenlik duvarı kuralları, kullanıcıların kimliğini kanıtlamasını gerektiren kimlik doğrulama sistemleri ve kullanıcıları belirli eylemler ve verilerle sınırlayan yetkilendirme sistemleriyle denetler. |
| [Azure SQL her zaman şifreleme](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|Azure SQL veritabanı veya SQL Server veritabanlarında depolanan kredi kartı numaraları veya ulusal tanımlama numaraları (örneğin, ABD sosyal güvenlik numaraları) gibi hassas verileri korur.  |
| [Azure &nbsp; SQL &nbsp; Saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)| Tüm veritabanının depolanmasını şifreleyen bir veritabanı güvenlik özelliği. |
| [Azure SQL veritabanı denetimi](../../azure-sql/database/auditing-overview.md)|Veritabanı olaylarını izleyen ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne yazan bir veritabanı denetleme özelliği.  |


## <a name="identity-and-access-management"></a>Kimlik ve erişim yönetimi
|Hizmet|Açıklama|
|------|--------|
| [Azure &nbsp; rol tabanlı &nbsp; erişim denetimi](../../role-based-access-control/role-assignments-portal.md)|Kullanıcıların yalnızca kuruluştaki rollerine göre erişmesi gereken kaynaklara erişmesine izin vermek için tasarlanan bir erişim denetimi özelliği.  |
| [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)|Azure 'da çok kiracılı, bulut tabanlı bir dizini ve birden çok kimlik yönetimi hizmetini destekleyen bulut tabanlı bir kimlik doğrulama deposu.  |
| [Azure Active Directory B2C](../../active-directory-b2c/overview.md)|Azure tabanlı uygulamalar kullanılırken müşterilerin kaydolmasına, oturum açma ve profillerini yönetme konusunda denetim sağlayan bir kimlik yönetimi hizmeti.   |
| [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)| Active Directory Domain Services bulut tabanlı ve yönetilen bir sürümü. |
| [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)| Güvenli bilgilere erişim izni vermeden önce birkaç farklı kimlik doğrulama ve doğrulama biçimi kullanan bir güvenlik sağlaması. |

## <a name="backup-and-disaster-recovery"></a>Yedekleme ve olağanüstü durum kurtarma
|Hizmet|Açıklama|
|------|--------|
| [Azure &nbsp; yedekleme](../../backup/backup-overview.md)| Azure bulutunda verileri yedeklemek ve geri yüklemek için kullanılan Azure tabanlı bir hizmet. |
| [Azure &nbsp; site &nbsp; Recovery](../../site-recovery/site-recovery-overview.md)|Fiziksel ve sanal makinelerde (VM) çalışan iş yüklerini birincil bir siteden ikincil bir konuma çoğaltan bir hatadan sonra hizmetlerin kurtarılmasını sağlamak için bir çevrimiçi hizmet. |

## <a name="networking"></a>Ağ
|Hizmet|Açıklama|
|------|--------|
| [Ağ &nbsp; Güvenlik &nbsp; grupları](../../virtual-network/virtual-network-vnet-plan-design-arm.md)| İzin verme veya reddetme kararları vermek için 5 tanımlama grubu kullanan ağ tabanlı erişim denetimi özelliği.  |
| [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)| Azure sanal ağlarına şirket içi erişime izin vermek için VPN uç noktası olarak kullanılan bir ağ aygıtı.  |
| [Azure Application Gateway](../../application-gateway/overview.md)|URL 'ye göre yol gösteren ve SSL yük boşaltma yapan gelişmiş bir Web uygulaması yük dengeleyici. |
|[Web uygulaması güvenlik duvarı](../../web-application-firewall/afds/afds-overview.md) (WAF)|Yaygın güvenlik açıklarından ve güvenlik açıklarından Web uygulamalarınızın merkezi korumasını sağlayan bir Application Gateway özelliği|
| [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)|TCP/UDP uygulama ağı yük dengeleyici. |
| [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)| Şirket içi ağlar ve Azure sanal ağları arasında adanmış bir WAN bağlantısı. |
| [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)| Küresel bir DNS yük dengeleyici.|
| [Azure uygulama proxy 'Si](../../active-directory/manage-apps/application-proxy.md)| Şirket içinde barındırılan Web uygulamalarına yönelik uzaktan erişimin güvenliğini sağlamak için kullanılan kimlik doğrulaması ön ucu. |
|[Azure Güvenlik Duvarı](../../firewall/overview.md)|Azure sanal ağ kaynaklarınızı koruyan, yönetilen, bulut tabanlı bir ağ güvenlik hizmeti.|
|[Azure DDoS koruması](../../ddos-protection/ddos-protection-overview.md)|Uygulama tasarımı en iyi uygulamaları ile birlikte, DDoS saldırılarına karşı savunma sağlar.|
|[Sanal ağ hizmet uç noktaları](../../virtual-network/virtual-network-service-endpoints-overview.md)|Sanal ağ özel adres alanınızı ve VNet 'iniz kimliğini doğrudan bir bağlantı üzerinden Azure hizmetlerine genişletir.|
