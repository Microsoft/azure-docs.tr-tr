---
title: CIS Microsoft Azure temel geçişleri kıyaslama şeması örnek önerisi eşleme
description: CIS, kıyaslama şeması örneğini Azure Ilkesine göre Microsoft Azure.
ms.date: 10/01/2019
ms.topic: sample
ms.openlocfilehash: 7974a5f9e5ccca41cdaa838cf05e3861dbae1b8d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038437"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>CIS Microsoft Azure temel kıyaslama kıyaslama şeması örneği

Aşağıdaki makalede, Azure şemaları CIS Microsoft Azure, kıyaslama şeması örnek haritaları olan CIS Microsoft Azure, kıyaslama önerilerini temel alarak ve Öneriler hakkında daha fazla bilgi için bkz. [cıs Microsoft Azure kuruluş kıyaslaması](https://www.cisecurity.org/benchmark/azure/).

Aşağıdaki eşlemeler, **Yönetim ve kıyaslama v 1.1.0** önerilerini temel alarak Microsoft Azure. Sağ taraftaki gezinmeyi kullanarak doğrudan belirli bir öneri eşlemesine atlayın.
Eşlenmiş önerilerin birçoğu bir [Azure Policy](../../../policy/overview.md) girişimi ile uygulanır. Tüm girişimi gözden geçirmek için Azure portal **ilkeyi** açın ve **tanımlar** sayfasını seçin. Daha sonra, **\[önizlemeyi\], kıyaslama v 1.1.0 önerilerini temel alarak ve denetim gereksinimleri yerleşik ilke girişimi ' ni desteklemek için belırlı VM uzantılarını dağıtmada Microsoft Azure** .

> [!IMPORTANT]
> Aşağıdaki her denetim bir veya daha fazla [Azure ilke](../../../policy/overview.md) tanımı ile ilişkilidir. Bu ilkeler, denetimiyle [uyumluluğu değerlendirmenize](../../../policy/how-to/get-compliance-data.md) yardımcı olabilir; Ancak, bir denetim ve bir veya daha fazla ilke arasında genellikle bir 1:1 veya bir eşleşme yoktur. Bu nedenle, Azure Ilkesi ile **uyumlu** , yalnızca ilkelerin kendilerine başvurur; Bu, bir denetimin tüm gereksinimleriyle tamamen uyumlu olduğunuzdan emin değildir. Buna ek olarak, uyumluluk standardı şu anda herhangi bir Azure Ilke tanımı tarafından açıklanmayan denetimler içerir. Bu nedenle, Azure Ilkesinde uyumluluk, genel uyumluluk durumunuzu yalnızca kısmi görünümüdür. Bu uyumluluk şeması örneği için denetimler ve Azure Ilke tanımları arasındaki ilişkilendirmeler zaman içinde değişebilir. Değişiklik geçmişini görüntülemek için [GitHub kayıt geçmişine](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md)bakın.

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 tüm ayrıcalıklı kullanıcılar için Multi-Factor Authentication 'ın etkinleştirildiğinden emin olun

Bu şema, çok faktörlü kimlik doğrulaması ayrıcalıklı Azure Active Directory hesaplarında etkin olmadığında izlemenize yardımcı olan [Azure ilke](../../../policy/overview.md) tanımlarını atar.

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- MFA, aboneliğinizde yazma izinleri olan hesaplarda etkinleştirilmelidir

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 çok faktörlü kimlik doğrulamasının tüm ayrıcalıklı olmayan kullanıcılar için etkinleştirildiğinden emin olun

Bu şema, çok faktörlü kimlik doğrulamasının ayrıcalıklı olmayan Azure Active Directory hesaplarında ne zaman etkin olmadığını izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 Konuk Kullanıcı bulunmadığından emin olun

Bu şema, kaldırılması gerekebilecek Konuk hesaplarını izlemenize yardımcı olan [Azure ilke](../../../policy/overview.md) tanımlarını atar.

- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir
- Yazma izinleri olan dış hesapların aboneliğinizden kaldırılması gerekir

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2,1 Standart fiyatlandırma katmanının seçili olduğundan emin olun

Bu şema, Güvenlik Merkezi Standart katmanının etkin olmadığı ağları ve sanal makineleri izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

 - Güvenlik Merkezi Standart fiyatlandırma katmanı seçilmelidir

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2,2 ' izleme aracısının otomatik sağlanması ' öğesinin ' on ' olarak ayarlandığından emin olun

Bu şema, Log Analytics aracısının otomatik olarak sağlanması özelliğinin etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Log Analytics izleme aracısının otomatik olarak sağlanması aboneliğinizde etkinleştirilmelidir

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 "sistem güncelleştirmelerini Izleme" için ASC varsayılan ilke ayarı "devre dışı" olarak belirlendiğinden emin olun

Bu şema, sistem güncelleştirmelerinin sanal makinelere yüklendiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Sistem güncelleştirmelerinin makinelerinizde yüklü olması gerekir

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 "Monitor OS güvenlik açıklarını Izleme", "devre dışı" olarak ASC varsayılan ilke ayarını doğrulayın

Bu şema, düzeltilen sanal makine güvenlik açıklarını izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 "Monitor Endpoint Protection" varsayılan ilke ayarının "devre dışı" olmadığından emin olun

Bu şema, sanal makinelerde Endpoint Protection 'ın etkinleştirilmesini sağlamanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 "disk şifrelemeyi Izle" ayarının "devre dışı" olarak ASC varsayılan ilke ayarını sağlayın

Bu şema, sanal makine disklerinin şifrelenmesini sağlamanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Disk şifrelemesi sanal makinelere uygulanmalıdır

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2,7 ASC varsayılan ilke ayarı "ağ güvenlik gruplarını Izle" ayarının "devre dışı" olmadığından emin olun

Bu şema, Internet 'e yönelik sanal makineleri korumanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Internet 'e yönelik sanal makineler için ağ güvenlik grubu kuralları sağlamlaştırılmış olmalıdır

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 ASC varsayılan ilke ayarı "Web uygulaması güvenlik duvarını Izle" ayarının "devre dışı" olmadığından emin olun

Bu şema, Web uygulamaları çalıştıran sanal makineleri korumanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- IaaS üzerinde Web uygulamalarına yönelik NSG kuralları sağlamlaştırılmış olmalıdır

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2,9 "sonraki nesil güvenlik duvarı 'Nı etkinleştir (NGFW) Izlemenin" "devre dışı" olarak, ASC varsayılan ilke ayarını sağlayın

Bu şema, erişimi kısıtlayarak alt ağları ve sanal makineleri tehditlere karşı korumaya yardımcı olan [Azure ilke](../../../policy/overview.md) tanımları atar. Bu CIS tarafından başvurulan güvenlik merkezi ilkesi Microsoft Azure temelleri kıyaslama önerisi, iki yeni öneriyle değiştirilmiştir. Aşağıda başvurulan ilkeler yeni önerileri ele.

- Alt ağlar bir ağ güvenlik grubuyla ilişkilendirilmelidir
- Sanal makineler bir ağ güvenlik grubuyla ilişkilendirilmelidir

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 "Izleme güvenlik açığı değerlendirmesi" için ASC varsayılan ilke ayarının "devre dışı" olmadığından emin olun

Bu şema, güvenlik açıklarının algılanıp düzeltildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2,11 ASC varsayılan ilke ayarı "Izleme Depolama Blobu şifrelemesi" ayarının "devre dışı" olduğundan emin olun

Azure depolama şifrelemesi, tüm yeni ve mevcut depolama hesapları için etkinleştirilmiştir ve devre dışı bırakılamaz. (Bu, varsayılan bir Azure özelliğidir; ilke ataması yoktur.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 "JıT ağ erişimini Izleme" için ASC varsayılan ilke ayarının "devre dışı" olmadığından emin olun

Bu şema, sanal makinelere erişimi denetlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Tam zamanında ağ erişim denetimi, sanal makinelere uygulanmalıdır

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2,13 ASC varsayılan ilke ayarı "Uyarlamalı uygulama beyaz listesini Izle" ayarının "devre dışı" olduğundan emin olun

Bu şema, sanal makinelerde Uyarlamalı uygulama denetimlerinin etkin olmasını sağlamanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Uyarlamalı uygulama denetimleri sanal makinelerde etkinleştirilmelidir

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2,14 "SQL denetimini Izleme" için ASC varsayılan ilke ayarının "devre dışı" olmadığından emin olun

Bu şema, SQL Server denetiminin etkinleştirildiğinden emin olmaya yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- SQL Server üzerindeki gelişmiş veri güvenliği ayarlarında denetim etkinleştirilmelidir

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 "SQL şifrelemeyi Izleme" için ASC varsayılan ilke ayarının "devre dışı" olmadığından emin olun

Bu şema, SQL veritabanlarında saydam veri şifrelemesi etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2,16 ' güvenlik iletişim e-postalarının ' ayarlandığından emin olun

Bu şema, güvenlik bildirimlerinin düzgün şekilde etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar

- Aboneliğiniz için bir güvenlik iletişim e-posta adresi belirtilmelidir

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2,17 güvenlik kişisinin ' telefon numarası ' olarak ayarlandığından emin olun

Bu şema, güvenlik bildirimlerinin düzgün şekilde etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar

- Aboneliğiniz için bir güvenlik ilgili kişisi telefon numarası belirtilmelidir

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2,18 ' yüksek önem derecesi uyarıları için e-posta bildirimi gönder ' ayarı ' on ' olarak ayarlandığından emin olun

Bu şema, güvenlik bildirimlerinin düzgün şekilde etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar

- Yüksek önem derecesine sahip uyarılar için e-posta bildirimi etkinleştirilmelidir

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2,19 ' e-postayı abonelik sahiplerine Gönder ' in ' on ' olarak ayarlandığından emin olun

Bu şema, güvenlik bildirimlerinin düzgün şekilde etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar

- Yüksek önem derecesi uyarıları için abonelik sahibine e-posta bildirimi etkinleştirilmelidir

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 ' güvenli aktarım gerekli ' ' Enabled ' olarak ayarlandığından emin olun

Bu şema, güvenli olmayan bağlantılara izin veren depolama hesaplarını izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Depolama hesaplarına Güvenli aktarım etkinleştirilmelidir

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 depolama hesapları için varsayılan ağ erişim kuralının Reddet olarak ayarlandığından emin olun

Bu şema, sınırsız erişime izin veren depolama hesaplarını izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3,8 depolama hesabı erişimi için ' güvenilen Microsoft Hizmetleri 'nin etkinleştirildiğinden emin olun

Bu şema, güvenilen Microsoft hizmetlerinden erişim izni olmayan depolama hesaplarını izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Depolama hesapları güvenilen Microsoft hizmetlerinden erişime izin verilmelidir

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 ' denetim ' ' on ' olarak ayarlandığından emin olun

Bu şema, SQL Server denetiminin etkinleştirildiğinden emin olmaya yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar. 

- SQL Server üzerindeki gelişmiş veri güvenliği ayarlarında denetim etkinleştirilmelidir

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 SQL Server için ' denetim ' ilkesindeki ' AuditActionGroups ' işleminin düzgün şekilde ayarlandığından emin olun

Bu şema, SQL Server denetiminin düzgün şekilde yapılandırıldığından emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- SQL denetim ayarları, kritik etkinlikleri yakalamak için yapılandırılmış eylem grupları olmalıdır

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 ' denetim ' saklama 'nin 90 günden daha fazla olduğundan emin olun '

Bu şema, SQL Server günlüklerinin en az 90 gün boyunca bekletildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- SQL Server 'lar 90 günden daha uzun denetim bekletme günlerindeki şekilde yapılandırılmalıdır.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 SQL Server üzerinde ' Gelişmiş veri güvenliği 'nin ' açık ' olarak ayarlandığından emin olun

Bu şema, SQL sunucularında ve SQL yönetilen örneklerinde gelişmiş veri güvenliğinin etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Gelişmiş veri güvenliği, SQL yönetilen örneklerinizin üzerinde etkinleştirilmelidir
- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 ' tehdit algılama türleri ' ' All ' olarak ayarlandığından emin olun

Bu şema, Gelişmiş tehdit korumasının SQL sunucularında ve SQL yönetilen örneklerinde düzgün şekilde yapılandırıldığından emin olmanıza yardımcı olan [Azure ilke](../../../policy/overview.md) tanımlarını atar.

- Gelişmiş tehdit koruması türleri SQL Server Gelişmiş veri güvenliği ayarları 'nda ' tümü ' olarak ayarlanmalıdır
- Gelişmiş tehdit koruması türleri SQL yönetilen örneği gelişmiş veri güvenliği ayarları içinde ' All ' olarak ayarlanmalıdır

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 ' uyarı gönder 'in ayarlanmış olduğundan emin olun

Bu şema, gelişmiş veri güvenliği bildirimlerinin düzgün şekilde etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- SQL yönetilen örneği için gelişmiş veri güvenliği ayarları, güvenlik uyarılarını almak için bir e-posta adresi içermelidir
- SQL Server için gelişmiş veri güvenliği ayarları, güvenlik uyarılarını almak için bir e-posta adresi içermelidir

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 ' e-posta hizmeti ve ortak yöneticiler ' ' in ' etkin ' olduğundan emin olun

Bu şema, gelişmiş veri güvenliği bildirimlerinin düzgün şekilde etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- SQL yönetilen örnek gelişmiş veri güvenliği ayarları 'nda yöneticilere ve abonelik sahiplerine e-posta bildirimleri etkinleştirilmelidir
- Yöneticiler ve abonelik sahipleri için e-posta bildirimleri, SQL sunucusu gelişmiş veri güvenliği ayarında etkin olmalıdır

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 Azure Active Directory yöneticisinin yapılandırıldığından emin olun

Bu şema, SQL Server 'lar için Azure Active Directory yöneticisinin sağlandığından emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 SQL veritabanında ' veri şifreleme ' ' on ' olarak ayarlandığından emin olun

Bu şema, SQL veritabanlarında saydam veri şifrelemesi etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 SQL Server 'ın TDE koruyucunun BYOK ile şifrelendiğinden emin olun (kendi anahtarınızı kullanın)

Bu şema, SQL Server 'lar için saydam veri şifrelenmiş koruyucunun ve SQL yönetilen örneklerin kendi anahtarınızla şifrelendiğinden emin olmanıza yardımcı olan [Azure ilke](../../../policy/overview.md) tanımlarını atar.

- SQL yönetilen örnek TDE koruyucusu kendi anahtarınızla şifrelenmelidir
- SQL Server TDE koruyucusu kendi anahtarınızla şifrelenmelidir

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4,11 MySQL veritabanı sunucusu için ' SSL bağlantısını zorla ' özelliğinin ' ENABLED ' olarak ayarlandığından emin olun

Bu şema, MySQL veritabanı sunucularının SSL bağlantılarını zorunlu tutmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4,13 PostgreSQL veritabanı sunucusu için ' SSL bağlantısını zorla ' özelliğinin ' ENABLED ' olarak ayarlandığından emin olun

Bu şema, PostgreSQL veritabanı sunucularının SSL bağlantılarını zorlayabilmenizi sağlayan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4,17 ' connection_throttling ' sunucu parametresinin PostgreSQL veritabanı sunucusu için ' ON ' olarak ayarlandığından emin olun

Bu şema, PostgreSQL veritabanı sunucularındaki deneme yanılma saldırılarını azaltmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- PostgreSQL veritabanı sunucuları için bağlantı azaltma etkinleştirilmelidir

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4,19 Azure Active Directory yöneticisinin yapılandırıldığından emin olun

Bu şema, SQL Server 'lar için Azure Active Directory yöneticisinin sağlandığından emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar. CIS Microsoft Azure temel bilgiler kıyaslaması bu öneriyi içerir; Ancak, [öneri 4,8](#48-ensure-that-azure-active-directory-admin-is-configured)' nin bir yinelemesi vardır.

- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 bir günlük profilinin var olduğundan emin olun

Bu şema, tüm Azure abonelikleri için bir günlük profilinin mevcut olduğundan emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar. 

- Azure aboneliklerinin etkinlik günlüğü için bir günlük profili olmalıdır

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 etkinlik günlüğü saklama 'nın 365 gün veya daha büyük bir süre sonra ayarlandığından emin olun

Bu şema, etkinlik günlüklerinin en az bir yıl boyunca bekletildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Etkinlik günlüğü en az bir yıl bekletilecektir

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 denetim profilinin tüm etkinlikleri yakalamalarını sağlayın

Bu şema, günlük profilinin düzgün şekilde yapılandırıldığından emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Azure Izleyici günlük profili, ' Write, ' ' DELETE, ' ve ' Action ' kategorilerinin günlüklerini toplamalıdır

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4, günlük profilinin genel dahil tüm bölgeler için etkinlik günlüklerini yakalamalarını sağlayın

Bu şema, günlük profilinin düzgün şekilde yapılandırıldığından emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Azure Izleyici, tüm bölgelerdeki etkinlik günlüklerini toplamalıdır

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Azure Anahtar Kasası günlüğü 'nün ' Enabled ' olduğundan emin olun

Bu şema, Anahtar kasaları için tanılama günlüklerinin etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Key Vault tanılama günlükleri etkinleştirilmelidir

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6,5 Ağ Izleyicisi 'nin ' Enabled ' olduğundan emin olun

Bu şema, kaynakların dağıtıldığı tüm bölgelerde ağ izleyicisinin etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar. Bu ilke, tüm uygulanabilir bölgeleri belirten bir parametre dizisi gerektirir. Bu ilke girişim tanımındaki varsayılan değer ' eastus '.

- Ağ Izleyicisi etkinleştirilmelidir

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 ' işletim sistemi diski 'nin şifrelendiğinden emin olun

Bu şema, sanal makinelerde disk şifrelemenin etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Disk şifrelemesi sanal makinelere uygulanmalıdır

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 ' veri diskleri ' nin şifrelendiğinden emin olun

Bu şema, sanal makinelerde disk şifrelemenin etkinleştirildiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Disk şifrelemesi sanal makinelere uygulanmalıdır

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7,3 ' eklenmemiş disklerin ' de şifrelendiğinden emin olun

Bu şema, eklenmemiş disklerin şifrelenmesini sağlamanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Eklenmemiş diskler şifrelenmelidir

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7,4 yalnızca onaylanan uzantıların yüklü olduğundan emin olun

Bu şema, yalnızca onaylanan sanal makine uzantılarının yüklü olduğundan emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar. Bu ilke, tüm onaylanan sanal makine uzantılarını belirten bir parametre dizisi gerektirir. Bu ilke girişim tanımı, müşterilerin doğrulaması gereken önerilen varsayılanları içerir. 

 - Yalnızca onaylanan VM uzantıları yüklenmelidir

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 tüm sanal makineler için en son işletim sistemi düzeltme eklerinin uygulandığından emin olun

Bu şema, sistem güncelleştirmelerinin sanal makinelere yüklendiğinden emin olmanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Sistem güncelleştirmelerinin makinelerinizde yüklü olması gerekir

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 tüm sanal makineler için Endpoint Protection 'ın yüklü olduğundan emin olun

Bu şema, sanal makinelerde Endpoint Protection 'ın etkinleştirilmesini sağlamanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8,4 anahtar kasasının kurtarılabilir olduğundan emin olun

Bu şema, yanlışlıkla silme durumunda Anahtar Kasası nesnelerinin kurtarılabilir olmasını sağlamanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Key Vault nesneler kurtarılabilir olmalıdır

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 Azure Kubernetes Hizmetleri içinde rol tabanlı erişim denetimi 'ni (RBAC) etkinleştirme

Bu şema, Kubernetes hizmet kümelerinde yönetilen izinler için rol tabanlı erişim denetimi kullanılmasını sağlamanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar

- \[Preview\]: rol tabanlı Access Control (RBAC) Kubernetes hizmetlerinde kullanılmalıdır

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 Web uygulamasının tüm HTTP trafiğini Azure App Service ' de HTTPS 'ye yönlendirdiğinden emin olun

Bu şema, Web uygulamalarına yalnızca güvenli bağlantılar üzerinden erişilebilir olmasını sağlamanıza yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır

## <a name="next-steps"></a>Sonraki adımlar

Artık CIS Microsoft Azure temel alan Kıyaslanmaların denetim eşlemesini gözden geçirdiğinize göre, girişimi atamak için Azure portal Azure Ilkesi şeması hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [CIS Microsoft Azure temel kıyaslanmaları kıyaslama şeması-genel bakış](./index.md)
> [CIS Microsoft Azure temel geçişleri kıyaslama şema-Deploy adımları](./deploy.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.