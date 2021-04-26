---
title: Azure 'da uçtan uca güvenlik | Microsoft Docs
description: Makale, bulut kaynaklarınızı güvenli hale getirmenize ve korumanıza ve tehditleri algılamanıza ve araştırmanıza yardımcı olan bir Azure hizmetleri haritası sağlar.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: 3ea3c2bcb878dbd8a712e6076dda09853f55e297
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310351"
---
# <a name="end-to-end-security-in-azure"></a>Azure 'da uçtan uca güvenlik
Uygulamalarınız ve hizmetleriniz için Azure kullanmanın en iyi nedenlerinden biri, kendi sunduğu güvenlik araçları ve özellikleri dizisinden faydalanabilir. Bu araçlar ve yetenekler, güvenli Azure platformunda güvenli çözümler oluşturmayı olanaklı hale getirmeye yardımcı olur. Microsoft Azure, müşteri verilerinin gizliliğini, bütünlüğünü ve kullanılabilirliğini sağlar. Ayrıca, saydam sorumluluklığa de olanak tanır.

Aşağıdaki diyagramda ve belgelerde Azure 'daki güvenlik hizmetleri açıklanır. Bu güvenlik hizmetleri, işletmenizin güvenlik ihtiyaçlarını karşılamanıza ve Kullanıcı, cihaz, kaynak, veri ve uygulamalarınızı bulutta korumanıza yardımcı olur.

## <a name="microsoft-security-services-map"></a>Microsoft Güvenlik Hizmetleri Haritası

Güvenlik Hizmetleri eşlemesi, Hizmetleri korudukları kaynaklarla (sütun) düzenler. Diyagram ayrıca Hizmetleri aşağıdaki kategorilere (satır) gruplandırır:

- Kimlik, konaklar, ağlar ve veriler arasında katmanlı, derinlemesine bir savunma stratejisi uygulamanıza olanak sağlayan güvenli ve koruma hizmetleri. Bu güvenlik hizmetleri ve özellikleri koleksiyonu, Azure ortamınızda güvenlik duruşunuzu anlamak ve geliştirmek için bir yol sağlar.
- Tehditleri algılayın – şüpheli etkinlikleri belirleyen ve tehdidi azaltmaya yardımcı olan hizmetler.
- Araştırın ve yanıtlayın – şüpheli bir etkinliği değerlendirebilmeniz ve yanıt verebilmeniz için günlüğe kaydetme verilerinin çekmesini sağlayan hizmetler.

Diyagram, Azure 'da kullandığınız hizmetlerin güvenliğini sağlamaya yardımcı olmak için kullanabileceğiniz yüksek etkili güvenlik önerileri koleksiyonu olan Azure Güvenlik kıyaslama programı 'nı içerir.

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Azure 'da uçtan uca güvenlik hizmetlerini gösteren diyagram." border="false":::

## <a name="security-controls-and-baselines"></a>Güvenlik denetimleri ve temelleri
[Azure Güvenlik kıyaslama](../benchmarks/introduction.md) programı, Azure 'da kullandığınız hizmetleri güvenli hale getirmeye yardımcı olmak için kullanabileceğiniz, yüksek etkili güvenlik önerileri koleksiyonu içerir:

- Güvenlik denetimleri-bu öneriler, Azure kiracınızda ve Azure hizmetleriniz arasında genel olarak geçerlidir. Her öneri, genellikle kıyaslamanın planlanmasına, onayına veya uygulanmasına dahil olan paydaşların bir listesini tanımlar.
- Hizmet temelleri-bu hizmetin güvenlik yapılandırmasına yönelik öneriler sağlamak için denetimleri tek tek Azure hizmetlerine uygular.

## <a name="secure-and-protect"></a>Güvenli ve koruma

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="Bulut kaynaklarınızı güvenli hale getirmenize ve korumanıza yardımcı olan Azure hizmetlerini gösteren diyagram." border="false":::

| Hizmet | Açıklama |
|------|--------|
| [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md)| Veri merkezlerinizin güvenlik duruşunu güçlendirir ve Azure 'da olup olmadıkları ve şirket içi iş yükleriniz genelinde gelişmiş tehdit koruması sağlayan Birleşik bir altyapı güvenliği yönetim sistemi. |
| **Kimlik &nbsp; & &nbsp; erişim &nbsp; yönetimi** | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| Microsoft 'un bulut tabanlı kimlik ve erişim yönetimi hizmeti.  |
|  | [Koşullu erişim](../../active-directory/conditional-access/overview.md) , Azure AD tarafından kimlik sinyallerini bir araya getirmek, kararlar almak ve kuruluş ilkelerini zorlamak için kullanılan bir araçtır. |
|  | [Etki alanı Hizmetleri](../../active-directory-domain-services/overview.md) , etki alanına katılması, Grup ilkesi, Hafif Dizin Erişim Protokolü (LDAP) ve Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı hizmetleri sağlamak IÇIN Azure AD tarafından kullanılan araçtır. |
|  | [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) , kuruluşunuzda önemli kaynaklara erişimi yönetmenizi, denetlemenizi ve izlemenizi sağlayan BIR Azure ad hizmetidir. |
|  | [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) , Azure AD tarafından, ikinci bir kimlik doğrulama biçimi gerektirerek verilere ve uygulamalara erişimi korumaya yardımcı olmak için kullanılan bir araçtır. |
| [Azure AD Kimlik Koruması](../../active-directory/identity-protection/overview-identity-protection.md) | Kuruluşların kimlik tabanlı riskleri algılamayı ve düzeltmesini otomatikleştirmesini, portaldaki verileri kullanarak riskleri araştırmasını ve daha fazla analiz için risk algılama verilerini üçüncü taraf yardımcı programlarına dışa aktarmayı sağlayan bir araç. |
| **Altyapı &nbsp; & &nbsp; ağı** |  |
| [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | Ortak Internet üzerinden bir Azure sanal ağı ile şirket içi bir konum arasında şifrelenmiş trafik göndermek ve Azure sanal ağları ile Microsoft ağı arasında şifrelenmiş trafik göndermek için kullanılan bir sanal ağ geçidi. |
| [Azure DDoS Koruması Standart](../../ddos-protection/ddos-protection-overview.md) | DDoS saldırılarına karşı savunmak için gelişmiş DDoS azaltma özellikleri sağlar. Bir sanal ağdaki belirli Azure kaynaklarınızı korumaya yardımcı olmak üzere otomatik olarak ayarlanır. |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | Hızlı, güvenli ve yaygın olarak ölçeklenebilir Web uygulamaları oluşturmak için Microsoft Global Edge ağını kullanan küresel, ölçeklenebilir bir giriş noktasıdır. |
| [Azure Güvenlik Duvarı](../../firewall/overview.md) | Azure sanal ağ kaynaklarınızı koruyan, yönetilen, bulut tabanlı bir ağ güvenlik hizmeti. Yerleşik yüksek kullanılabilirliğe ve sınırsız bulut ölçeklenebilirliğine sahip, tam durum bilgisi olan bir hizmet olarak güvenlik duvarıdır. |
| [Azure Key Vault](../../key-vault/general/overview.md) | Belirteçler, parolalar, sertifikalar, API anahtarları ve diğer gizli dizileri için güvenli bir gizli anahtar deposu. Key Vault, verilerinizi şifrelemek için kullanılan şifreleme anahtarlarını oluşturmak ve denetlemek için de kullanılabilir. |
| [Key Vault Managed HSM (Önizleme)](../../key-vault/managed-hsm/overview.md) | FIPS 140-2 düzey 3 tarafından doğrulanan HSM 'ler kullanarak bulut uygulamalarınız için şifreleme anahtarlarını korumanıza olanak sağlayan, tam olarak yönetilen, yüksek oranda kullanılabilir, tek kiracılı, standartlara uyumlu bir bulut hizmeti. |
| [Azure Özel Bağlantı](../../private-link/private-link-overview.md) | Azure PaaS hizmetlerine (örneğin, Azure depolama ve SQL veritabanı) ve Azure 'da barındırılan, müşteriye ait/iş ortağı hizmetlerine sanal ağınızdaki özel bir uç nokta üzerinden erişmenizi sağlar. |
| [Azure Application Gateway](../../application-gateway/overview.md) | Web uygulamalarınıza yönelik trafiği yönetmenizi sağlayan gelişmiş bir Web trafiği yük dengeleyici. Application Gateway, bir HTTP isteğinin ek özniteliklerine (örneğin, URI yolu veya ana bilgisayar üstbilgileri) göre yönlendirme kararları verebilir. |
| [Azure Service Bus](../../service-bus-messaging/service-bus-messaging-overview.md) | İleti kuyrukları ve yayımlama-abone olma konuları ile tam olarak yönetilen bir kurumsal ileti Aracısı. Service Bus, uygulamaları ve hizmetleri birbirinden ayırmak için kullanılır. |
| [Web Uygulaması Güvenlik Duvarı](../../web-application-firewall/overview.md) | Yaygın güvenlik açıklarından ve güvenlik açıklarından Web uygulamalarınızın merkezi korumasını sağlar. WAF, Azure Application Gateway ve Azure ön kapılı ile dağıtılabilir. |
| **Veri & uygulaması** |  |
| [Azure Backup](../../backup/backup-overview.md) | Verilerinizi yedeklemek ve Microsoft Azure buluttan kurtarmak için basit, güvenli ve uygun maliyetli çözümler sunar. |
| [Azure Depolama Hizmeti Şifrelemesi](../../storage/common/storage-service-encryption.md) | Verileri depolanmadan önce otomatik olarak şifreler ve verileri aldığınızda otomatik olarak çözer. |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) | Kuruluşların içeriğe Etiketler uygulayarak belge ve e-postaları bulmasına, sınıflandırmasına ve korumasına olanak sağlayan bulut tabanlı bir çözümdür. |
| [API Management](../../api-management/api-management-key-concepts.md) | Mevcut arka uç hizmetleri için tutarlı ve modern API ağ geçitleri oluşturmanın bir yolu. |
| [Azure gizli bilgi işlem](../../confidential-computing/overview.md) | , Bulutta işlendiği sırada hassas verilerinizi yalıtmanızı sağlar. |
| [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops) | Geliştirme projeleriniz, Azure DevOps 'da depolandığında birçok güvenlik ve idare teknolojileri, işletimsel uygulama ve uyumluluk ilkesi katmanından faydalanır. |
| **Müşteri erişimi** |  |
| [Azure AD dış kimlikleri](../../active-directory/external-identities/compare-with-b2c.md) | Azure AD 'de dış kimliklerle, kuruluşunuzun dışındaki kişilerin uygulamalarınıza ve kaynaklarınıza erişmesine izin vererek tercih ettikleri kimliği kullanarak oturum açmasını sağlayabilirsiniz. |
|  | Uygulamalarınızı ve kaynaklarınızı, [Azure AD B2B](../../active-directory/external-identities/what-is-b2b.md) işbirliği aracılığıyla dış kullanıcılarla paylaşabilirsiniz. |
|  | [Azure AD B2C](../../active-directory-b2c/overview.md) , günde milyonlarca kullanıcı ve milyarlarca kimlik doğrulama, hizmet reddi, parola spreyi veya deneme yanılma saldırıları gibi tehditleri izleyerek ve otomatik olarak idare etmenizi sağlar. |

## <a name="detect-threats"></a>Tehditleri algılama

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="Tehditleri algılayan Azure hizmetlerini gösteren diyagram." border="false":::

| Hizmet | Açıklama |
|------|--------|
| [Azure Defender](../../security-center/azure-defender.md) | Azure ve hibrit kaynaklarınızın ve iş yüklerinizin gelişmiş, akıllı ve korumasını getirir. Güvenlik Merkezi 'ndeki Azure Defender panosu, ortamınız için bulut iş yükü koruma özelliklerinin görünürlüğünü ve denetimini sağlar. |
| [Azure Sentinel](../../sentinel/overview.md) | Ölçeklenebilir, bulutta yerel, güvenlik bilgileri olay yönetimi (SıEM) ve güvenlik Orchestration otomatik yanıtı (SOAR) çözümü. Sentinel, kuruluş genelinde akıllı güvenlik Analizi ve tehdit bilgileri sunarak uyarı algılama, tehdit görünürlüğü, proaktif arama ve tehdit yanıtı için tek bir çözüm sunar. |
| **Kimlik &nbsp; & &nbsp; erişim &nbsp; yönetimi** |  |
| [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/defender/microsoft-365-defender) | Karmaşık saldırılara karşı tümleşik koruma sağlamak amacıyla uç noktalar, kimlikler, araştırma ve yanıt noktaları, kimlik, e-posta ve uygulamalar genelinde algılamayı, engellemesini, araştırmayı ve yanıtı yerel olarak koordine eden birleştirilmiş bir ön ve sonrası kurumsal savunma paketi |
|  | [Uç nokta Için Microsoft Defender](https://docs.microsoft.com/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint.md) , kurumsal ağların gelişmiş tehditleri engellemesine, algılamasına, araştırmasına ve yanıt vermesine yardımcı olmak üzere tasarlanan bir kurumsal uç nokta güvenlik platformudur. |
|  | [Kimlik Için Microsoft Defender](https://docs.microsoft.com/defender-for-identity/what-is) , kuruluşunuzda yönlendirilen gelişmiş tehditleri, riskli kimlikleri ve kötü amaçlı Insider eylemlerini tanımlamak, algılamak ve araştırmak için şirket içi Active Directory sinyallerinizi kullanan bulut tabanlı bir güvenlik çözümüdür. |
| [Azure AD Kimlik Koruması](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | Kullanıcı riskini ve risk algılamalarını yönetmenize yardımcı olmak üzere iki tür otomatik bildirim e-postası gönderir: risk altındaki kullanıcılar e-posta ve haftalık özet e-postası algıladı |
| **Altyapı & ağı** |  |
| [IoT için Azure Defender](../../defender-for-iot/overview.md) | IoT/OT cihazlarını, güvenlik açıklarını ve tehditleri tanımlamaya yönelik Birleşik bir güvenlik çözümüdür. Mevcut IoT/OT cihazlarını korumanızı veya yeni IoT yeniliklerine güvenlik derlemenize gerek duyduğunuzda IoT/OT ortamınızın tamamını güvenli hale getirmenizi sağlar. |
| [Azure Ağ İzleyicisi](../../network-watcher/network-watcher-monitoring-overview.md) | Bir Azure sanal ağındaki kaynaklar için izleme, tanılama, ölçümleri görüntüleme ve günlükleri etkinleştirme ya da devre dışı bırakma araçları sağlar. Ağ Izleyicisi, sanal makineler, sanal ağlar, uygulama ağ geçitleri ve yük dengeleyiciler içeren IaaS ürünlerinin ağ durumunu izlemek ve onarmak üzere tasarlanmıştır. |
| [Azure Ilke denetim günlüğü](../../governance/policy/overview.md) | , Kurumsal standartları zorlamaya ve üstün ölçekli uyumluluğu değerlendirmenize yardımcı olur. Azure Ilkesi olay kaynağını, tarihi, kullanıcıyı, zaman damgasını, kaynak adreslerini, hedef adreslerini ve diğer yararlı öğeleri içerecek şekilde otomatik olarak etkinleştirilen etkinlik günlüklerini kullanır. |
| **Veri & uygulaması** |  |
| [Kapsayıcı kayıt defterleri için Azure Defender](../../security-center/defender-for-container-registries-introduction.md) | Azure Resource Manager tabanlı Azure Container Registry kayıt defterlerinden görüntüleri taramak ve görüntülerinize daha derin görünürlük sağlamak için bir güvenlik açığı tarayıcısı içerir. |
| [Kubernetes için Azure Defender](../../security-center/defender-for-kubernetes-introduction.md) | , AKS tarafından yönetilen hizmetlerinizi Azure Kubernetes hizmeti (AKS) tarafından alınan Günlükler aracılığıyla izleyerek küme düzeyinde tehdit koruması sağlar. |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | Birden çok bulutta çalışan bir bulut erişim güvenlik Aracısı (CASB). Tüm bulut hizmetlerinizde siber saldırıları belirlemek ve bunlarla savaşmak için zengin görünürlük, verilerin yolculuğu üzerinde denetim ve gelişmiş analizler sağlar. |

## <a name="investigate-and-respond"></a>Araştırma ve müdahale etme

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="Tehditleri araştırmanıza ve bunlara yanıt vermenize yardımcı olan Azure hizmetlerini gösteren diyagram." border="false":::

| Hizmet | Açıklama |
|------|--------|
| [Azure Sentinel](../../sentinel/hunting.md) | Kuruluşunuzun veri kaynakları genelinde güvenlik tehditleri arayalım için güçlü arama ve sorgu araçları. |
| [Azure &nbsp; izleyici &nbsp; günlükleri &nbsp; ve &nbsp; ölçümleri](../../azure-monitor/overview.md) | Bulut ve şirket içi ortamlarınızdaki telemetri toplama, çözümleme ve üzerinde işlem yapmaya yönelik kapsamlı bir çözüm sunar. Azure Izleyici, çeşitli kaynaklardan [veri toplar ve](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor) bunları analiz, görselleştirme ve uyarı için kullanılabilecek ortak bir veri platformuna toplar. |
| **Kimlik &nbsp; & &nbsp; erişim &nbsp; yönetimi** |  |
| [Azure &nbsp; ad &nbsp; raporları &nbsp; ve &nbsp; izleme](https://docs.microsoft.com/azure/active-directory/reports-monitoring/) | [Azure AD raporları](../../active-directory/reports-monitoring/overview-reports.md) , ortamınızdaki etkinliğin kapsamlı bir görünümünü sağlar. |
|  | [Azure AD izleme](../../active-directory/reports-monitoring/overview-monitoring.md) , Azure ad etkinlik günlüklerinizi farklı uç noktalara yönlendirmenizi sağlar.|
| [Azure AD PıM denetim geçmişi](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | Tüm ayrıcalıklı roller için son 30 gün içindeki tüm rol atamalarını ve etkinleştirmeleri gösterir. |
| **Veri & uygulaması** |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate) | Bulut ortamınızda neler olduğunu daha ayrıntılı bir şekilde anlayabilmek için araçlar sağlar. |

## <a name="next-steps"></a>Sonraki adımlar

- [Bulutta paylaşılan sorumluluğu](shared-responsibility.md)anlayın.

- [Azure bulutundaki yalıtım seçimlerini](isolation-choices.md) hem kötü amaçlı hem de kötü amaçlı olmayan kullanıcılara karşı anlayın.