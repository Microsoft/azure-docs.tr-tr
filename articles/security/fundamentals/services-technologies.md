---
title: Azure Güvenlik Hizmetleri ve teknolojileri | Microsoft Docs
description: Bu makale, Azure Güvenlik Hizmetleri ve teknolojilerinin seçkin bir listesini sağlar.
services: security
documentationcenter: na
author: barclayn
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
ms.author: barclayn
ms.openlocfilehash: 741e6b9cad20645fdfc085623fd9492a12ac1dfc
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726556"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Azure 'da kullanılabilen güvenlik hizmetleri ve teknolojileri

Güncel ve gelecekteki Azure müşterilerine sahip tartışmalarda, genellikle Azure 'un sunabileceği güvenlikle ilgili tüm hizmetlerin ve teknolojilerin bir listesini mi yaşıyorsunuz? "

Bulut hizmeti sağlayıcısı seçeneklerini değerlendirirken, bu bilgilerin olması yararlı olacaktır. Bu nedenle, başlamanıza başlamak için bu listeyi sağladık.

Zaman içinde, bu liste Azure 'un yaptığı gibi değişir ve büyür. Güvenlikle ilgili hizmetlerimiz ve teknolojilerimizde güncel kalmak için bu sayfayı düzenli olarak kontrol ettiğinizden emin olun.

## <a name="general-azure-security"></a>Genel Azure güvenliği
|Hizmet|Açıklama|
|--------|--------|
|[Azure&nbsp;güvenlik&nbsp;Merkezi](/azure/security-center/security-center-intro)| Karma bulut iş yükleri arasında güvenlik yönetimi ve Gelişmiş tehdit koruması sağlayan bir bulut iş yükü koruma çözümüdür.|
|[Azure Anahtar Kasası.](/azure/key-vault/key-vault-overview)| Parolalar, bağlantı dizeleri ve uygulamalarınızın çalışır durumda tutulması için gereken diğer bilgiler için güvenli bir gizli anahtar deposu. |
|[Azure İzleyici günlükleri](/azure/log-analytics/log-analytics-overview)|Telemetri ve diğer verileri toplayan bir izleme hizmeti ve uygulamalarınız ve kaynaklarınız için operasyonel içgörüler sunmak üzere bir sorgu dili ve analiz altyapısı sağlar. , Tek başına veya güvenlik merkezi gibi diğer hizmetlerle kullanılabilir. |
|[Azure geliştirme ve test laboratuvarları](/azure/lab-services/devtest-lab-overview)|Geliştiricilerin ve Test edicilerin Azure 'da hızlı bir şekilde ortam oluşturmalarına yardımcı olan ve maliyeti en aza indirirken bir hizmet.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Depolama alanı güvenliği
|Hizmet|Açıklama|
|------|--------|
| [Azure&nbsp;depolama&nbsp;hizmetişifrelemesi&nbsp;](/azure/storage/common/storage-service-encryption)|Verilerinizi Azure Storage 'da otomatik olarak şifreleyen bir güvenlik özelliği.   |
|[StorSimple şifrelenmiş karma depolama](/azure/storsimple/storsimple-ova-overview)| Şirket içi cihazlar ile Azure bulut depolama arasındaki depolama görevlerini yöneten tümleşik bir depolama çözümüdür.|
|[Azure Istemci tarafı şifreleme](/azure/storage/common/storage-client-side-encryption)| Azure Storage 'a yüklemeden önce istemci uygulamaları içindeki verileri şifreleyen istemci tarafı şifreleme çözümü; Ayrıca, indirme sırasında verilerin şifresini çözer. |
| [Azure depolama paylaşılan erişim Imzaları](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|Paylaşılan erişim imzası, depolama hesabınızdaki kaynaklara temsilci erişimi sağlar.  |
|[Azure depolama hesabı anahtarları](/azure/storage/common/storage-create-storage-account)| Depolama hesabına erişildiğinde kimlik doğrulama için kullanılan Azure depolama için erişim denetimi yöntemi. |
|[SMB 3,0 şifrelemesi ile Azure dosya paylaşımları](/azure/storage/files/storage-files-introduction)|Sunucu Ileti bloğu (SMB) dosya paylaşımı protokolü için otomatik ağ şifrelemeyi sağlayan bir ağ güvenlik teknolojisi. |
|[Azure Depolama Analizi](/rest/api/storageservices/Storage-Analytics)| Depolama hesabınızdaki veriler için bir günlüğe kaydetme ve ölçüm oluşturma teknolojisi. |

<!------>

## <a name="database-security"></a>Veritabanı güvenliği
|Hizmet|Açıklama|
|------|--------|
| [Azure&nbsp;SQL&nbsp;güvenlik duvarı](/azure/sql-database/sql-database-firewall-configure)|Veritabanına yönelik ağ tabanlı saldırılara karşı koruma sağlayan bir ağ erişim denetimi özelliği. |
|[Azure&nbsp;SQL&nbsp;hücre&nbsp;düzeyi şifreleme](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Parçalı düzeyde şifreleme sağlayan bir veritabanı güvenlik teknolojisi.  |
| [Azure&nbsp;SQL&nbsp;bağlantı şifrelemesi](/azure/sql-database/sql-database-control-access)|SQL Veritabanı güvenliği sağlamak için erişimi IP adresine göre bağlantıyı sınırlayan güvenlik duvarı kuralları, kullanıcıların kimliğini kanıtlamasını gerektiren kimlik doğrulama sistemleri ve kullanıcıları belirli eylemler ve verilerle sınırlayan yetkilendirme sistemleriyle denetler. |
| [Azure SQL her zaman şifreleme](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Azure SQL veritabanı veya SQL Server veritabanlarında depolanan kredi kartı numaraları veya ulusal tanımlama numaraları (örneğin, ABD sosyal güvenlik numaraları) gibi hassas verileri korur.  |
| [Azure&nbsp;SQL&nbsp;saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Tüm veritabanının depolanmasını şifreleyen bir veritabanı güvenlik özelliği. |
| [Azure SQL veritabanı denetimi](/azure/sql-database/sql-database-auditing)|Veritabanı olaylarını izleyen ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne yazan bir veritabanı denetleme özelliği.  |


## <a name="identity-and-access-management"></a>Kimlik ve erişim yönetimi
|Hizmet|Açıklama|
|------|--------|
| [Azure&nbsp;rol&nbsp;tabanlıAccessControl&nbsp;](/azure/active-directory/role-based-access-control-configure)|Kullanıcıların yalnızca kuruluştaki rollerine göre erişmesi gereken kaynaklara erişmesine izin vermek için tasarlanan bir erişim denetimi özelliği.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Azure 'da çok kiracılı, bulut tabanlı bir dizini ve birden çok kimlik yönetimi hizmetini destekleyen bulut tabanlı bir kimlik doğrulama deposu.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Azure tabanlı uygulamalar kullanılırken müşterilerin kaydolmasına, oturum açma ve profillerini yönetme konusunda denetim sağlayan bir kimlik yönetimi hizmeti.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| Active Directory Domain Services bulut tabanlı ve yönetilen bir sürümü. |
| [Azure çok faktörlü kimlik doğrulaması](/azure/active-directory/authentication/multi-factor-authentication)| Güvenli bilgilere erişim izni vermeden önce birkaç farklı kimlik doğrulama ve doğrulama biçimi kullanan bir güvenlik sağlaması. |

## <a name="backup-and-disaster-recovery"></a>Yedekleme ve olağanüstü durum kurtarma
|Hizmet|Açıklama|
|------|--------|
| [Azure&nbsp;yedekleme](/azure/backup/backup-introduction-to-azure-backup)| Azure bulutunda verileri yedeklemek ve geri yüklemek için kullanılan Azure tabanlı bir hizmet. |
| [Azure&nbsp;site&nbsp;Recovery](/azure/site-recovery/site-recovery-overview)|Fiziksel ve sanal makinelerde (VM) çalışan iş yüklerini birincil bir siteden ikincil bir konuma çoğaltan bir hatadan sonra hizmetlerin kurtarılmasını sağlamak için bir çevrimiçi hizmet. |

## <a name="networking"></a>Ağ
|Hizmet|Açıklama|
|------|--------|
| [Ağ&nbsp;güvenlik&nbsp;grupları](/azure/virtual-network/virtual-networks-nsg)| İzin verme veya reddetme kararları vermek için 5 tanımlama grubu kullanan ağ tabanlı erişim denetimi özelliği.  |
| [Azure VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| Azure sanal ağlarına şirket içi erişime izin vermek için VPN uç noktası olarak kullanılan bir ağ aygıtı.  |
| [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction)|URL 'ye göre yol gösteren ve SSL yük boşaltma yapan gelişmiş bir Web uygulaması yük dengeleyici. |
|[Web uygulaması güvenlik duvarı](/azure/frontdoor/waf-overview) WAF|Yaygın güvenlik açıklarından ve güvenlik açıklarından Web uygulamalarınızın merkezi korumasını sağlayan bir Application Gateway özelliği|
| [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)|TCP/UDP uygulama ağı yük dengeleyici. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| Şirket içi ağlar ve Azure sanal ağları arasında adanmış bir WAN bağlantısı. |
| [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)| Küresel bir DNS yük dengeleyici.|
| [Azure uygulama proxy 'Si](/azure/active-directory/active-directory-application-proxy-get-started)| Şirket içinde barındırılan Web uygulamalarına yönelik uzaktan erişimin güvenliğini sağlamak için kullanılan kimlik doğrulaması ön ucu. |
|[Azure Güvenlik Duvarı](/azure/firewall/overview)|Azure sanal ağ kaynaklarınızı koruyan, yönetilen, bulut tabanlı bir ağ güvenlik hizmeti.|
|[Azure DDoS koruması](/azure/virtual-network/ddos-protection-overview)|Uygulama tasarımı en iyi uygulamaları ile birlikte, DDoS saldırılarına karşı savunma sağlar.|
|[Sanal ağ hizmet uç noktaları](/azure/virtual-network/virtual-network-service-endpoints-overview)|Sanal ağ özel adres alanınızı ve VNet 'iniz kimliğini doğrudan bir bağlantı üzerinden Azure hizmetlerine genişletir.|