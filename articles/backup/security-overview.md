---
title: Güvenlik özelliklerine genel bakış
description: Yedekleme verilerinizi korumanıza ve işletmenizin güvenlik ihtiyaçlarını karşılamanıza yardımcı olan Azure Backup güvenlik özellikleri hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 9aa1909f1590b477d9a7f7a09ad0c2b1936e3e29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96325664"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure Backup güvenlik özelliklerine genel bakış

Verilerinizi korumak için uygulayabileceğiniz en önemli adımlardan biri, güvenilir bir yedekleme altyapısına sahip olur. Ancak verilerinizin güvenli bir şekilde yedeklendiğinden ve yedeklemelerinizin her zaman korunduğundan emin olmak önemlidir. Azure Backup, yedekleme ortamınıza güvenlik sağlar. her ikisi de verileriniz aktarım sırasında ve bekleyen sırada. Bu makalede, yedekleme verilerinizi korumanıza ve işletmenizin güvenlik ihtiyaçlarını karşılamanıza yardımcı olan Azure Backup güvenlik özellikleri listelenmektedir.

## <a name="management-and-control-of-identity-and-user-access"></a>Kimlik ve Kullanıcı erişiminin yönetimi ve denetimi

Kurtarma Hizmetleri kasaları tarafından kullanılan depolama hesapları yalıtılmış ve herhangi bir kötü amaçlı kullanıcı tarafından erişilemez. Erişime yalnızca geri yükleme gibi Azure Backup yönetim işlemleri aracılığıyla izin verilir. Azure Backup, [Azure rol tabanlı erişim denetimi (Azure RBAC)](./backup-rbac-rs-vault.md)kullanarak ayrıntılı erişim aracılığıyla yönetilen işlemleri denetlemenize olanak sağlar. Azure RBAC, takımınızın içindeki görevleri ayırt etmenize ve yalnızca işlerini yapmak için gereken kullanıcılara erişim miktarına izin vermenizi sağlar.

Azure Backup, yedekleme yönetimi işlemlerini denetlemek için üç [yerleşik rol](../role-based-access-control/built-in-roles.md) sağlar:

* Yedek katkıda bulunan-kurtarma hizmetleri kasasını silme ve başkalarına erişim verme dışında yedeklemeler oluşturmak ve yönetmek için
* Yedekleme Işleci-katkıda bulunan her şey, yedeklemenin kaldırılması ve yedekleme ilkelerinin yönetilmesi dışında
* Yedekleme okuyucusu-tüm yedekleme yönetimi işlemlerini görüntüleme izinleri

[Azure Backup yönetmek Için Azure rol tabanlı erişim denetimi](./backup-rbac-rs-vault.md)hakkında daha fazla bilgi edinin.

Azure Backup, güvenlik açıklarını engellemek, algılamak ve yanıtlamak için hizmette yerleşik olarak bulunan çeşitli güvenlik denetimlerine sahiptir. [Azure Backup için güvenlik denetimleri](./security-baseline.md)hakkında daha fazla bilgi edinin.

## <a name="separation-between-guest-and-azure-storage"></a>Konuk ve Azure depolama arasındaki ayrım

Sanal makine yedeklemesini ve SQL ve SAP HANA VM yedeklemesi 'nde içeren Azure Backup, yedekleme verileri Azure depolama alanında depolanır ve konukta yedekleme depolama alanı veya içeriği için doğrudan erişim yoktur.  Sanal makine yedekleme ile, yedekleme anlık görüntüsü oluşturma ve depolama, konuğun uygulamayla tutarlı yedeklemeler için iş yükünü sessiz moda alma dışında bir katılımı olmadığı Azure yapısı tarafından yapılır.  SQL ve SAP HANA ile, yedekleme uzantısı belirli bloblara yazmak için geçici erişimi alır.  Bu şekilde, güvenliği aşılmış bir ortamda bile, var olan yedeklemeler konuk tarafından değiştirilemez veya silinemez.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Azure VM yedeklemesi için Internet bağlantısı gerekli değil

Azure VM 'lerinin yedeklenmesi, sanal makinenizin diskinden kurtarma hizmetleri kasasına veri taşımayı gerektirir. Ancak, tüm gerekli iletişim ve veri aktarımı yalnızca sanal ağınıza erişmeniz gerekmeden Azure omurga ağında olur. Bu nedenle, güvenli ağların içine yerleştirilmiş olan Azure VM 'lerinin yedeklenmesi, herhangi bir IP veya FQDN 'ye erişim izni vermemesini gerektirmez.

## <a name="private-endpoints-for-azure-backup"></a>Azure Backup için özel uç noktalar

Artık verilerinizi bir sanal ağ içindeki sunuculardan kurtarma hizmetleri kasanıza güvenli bir şekilde yedeklemek için [Özel uç noktaları](../private-link/private-endpoint-overview.md) kullanabilirsiniz. Özel uç nokta, kasalarınızın VNET adres alanından bir IP kullanır, bu nedenle sanal ağlarınızı tüm genel IP 'Lerde kullanıma sunabilmeniz gerekmez. Özel uç noktalar, Azure sanal makinelerinizin içinde çalışan SQL ve SAP HANA veritabanlarınızı yedeklemek ve geri yüklemek için kullanılabilir. Ayrıca, MARS Aracısı kullanılarak şirket içi sunucularınız için de kullanılabilir.

[Azure Backup için](./private-endpoints.md)özel uç noktalar hakkında daha fazla bilgi edinin.

## <a name="encryption-of-data"></a>Verilerin şifrelenmesi

Şifreleme, verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur. Veri şifreleme Azure Backup birçok aşamada oluşur:

* Azure 'da, Azure depolama ile kasa arasındaki yoldaki veriler [https tarafından korunur](backup-support-matrix.md#network-traffic-to-azure). Bu veriler, Azure omurga ağında kalır.

* Yedekleme verileri, [platform tarafından yönetilen anahtarlar](backup-encryption.md)kullanılarak otomatik olarak şifrelenir ve bunu etkinleştirmek için herhangi bir açık işlem yapmanız gerekmez. Ayrıca, Azure Key Vault depolanan [müşteri tarafından yönetilen anahtarları](encryption-at-rest-with-cmk.md) kullanarak yedeklenen verilerinizi şifreleyebilirsiniz. Bu, kurtarma hizmetleri kasanıza yedeklenen tüm iş yükleri için geçerlidir.

* Azure Backup, işletim sistemi/veri disklerinin [Azure disk şifrelemesi (ade)](backup-azure-vms-encryption.md#encryption-support-using-ade) ve [CMK şifreli disklere sahip VM](backup-azure-vms-encryption.md#encryption-using-customer-managed-keys)'lerle şifrelendiğinden Azure VM 'lerinin yedeklenmesini ve geri yüklenmesini destekler. Daha fazla bilgi için, [şifrelenmiş Azure VM 'leri ve Azure Backup hakkında daha fazla bilgi edinin](./backup-azure-vms-encryption.md).

* MARS aracısına sahip şirket içi sunuculardan veri yedeklendiğinde, veriler Azure Backup yüklenmeden önce bir parola ile şifrelenir ve yalnızca Azure Backup indirildikten sonra şifresi çözülür. [Karma yedeklemeleri korumaya yardımcı olmak için güvenlik özellikleri](#security-features-to-help-protect-hybrid-backups)hakkında daha fazla bilgi edinin.

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>İstemeden silme işleminden yedekleme verilerinin korunması

Azure Backup, silme işleminden sonra bile yedekleme verilerini korumaya yardımcı olmak için güvenlik özellikleri sağlar. Geçici silme sayesinde, Kullanıcı bir VM 'nin yedeklemesini silerse, yedekleme verileri 14 ek gün boyunca tutulur ve bu yedekleme öğesinin veri kaybı olmadan kurtarılmasını sağlar. "Geçici silme" durumundaki yedekleme verilerinin ek 14 gün tutulması size herhangi bir ücret vermez. [Geçici silme hakkında daha fazla bilgi edinin](backup-azure-security-feature-cloud.md).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Şüpheli etkinliğin izlenmesi ve uyarıları

Azure Backup, Azure Backup ilgili olaylara yönelik eylemleri görüntülemek ve yapılandırmak için [yerleşik izleme ve uyarı özellikleri](./backup-azure-monitoring-built-in-monitor.md) sağlar. [Yedekleme raporları](./configure-reports.md) , Kullanım Takibi, yedeklemelerin denetlenmesi ve geri yüklemeleri ve farklı ayrıntı düzeyi düzeylerinde önemli eğilimleri belirlemek için tek bir dura hedefi olarak görev yapar. Azure Backup izleme ve raporlama araçlarının kullanılması, sizi meydana geldiğinde yetkisiz, şüpheli veya kötü amaçlı etkinliklere karşı uyarabilir.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Karma yedeklemeleri korumaya yardımcı olan güvenlik özellikleri

Azure Backup hizmeti, dosyaları, klasörleri ve birim ya da sistem durumunu şirket içi bir bilgisayardan Azure 'a yedeklemek ve geri yüklemek için Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı 'nı kullanır. MARS artık karma yedeklemeleri korumaya yardımcı olacak güvenlik özellikleri sağlıyor. Bu özellikler şunları içerir:

* Bir parolayı değiştirme gibi kritik bir işlem gerçekleştirildiğinde ek bir kimlik doğrulama katmanı eklenir. Bu doğrulama, bu tür işlemlerin yalnızca geçerli Azure kimlik bilgilerine sahip kullanıcılar tarafından gerçekleştirilmesini sağlamaktır. [Saldırıları engelleyen özellikler hakkında daha fazla bilgi edinin](./backup-azure-security-feature.md#prevent-attacks).

* Silinen yedekleme verileri, silme tarihinden itibaren ek 14 gün boyunca tutulur. Bu, belirli bir süre içindeki verilerin kurtarılabilmesini sağlar; bu nedenle bir saldırı gerçekleşse bile veri kaybı olmaz. Ayrıca, bozuk verilere karşı koruma sağlamak için daha fazla sayıda en düşük kurtarma noktası tutulur. [Silinen yedekleme verilerini kurtarma hakkında daha fazla bilgi edinin](./backup-azure-security-feature.md#recover-deleted-backup-data).

* Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı kullanılarak yedeklenen veriler için, verilerin Azure Backup yüklenmeden önce şifrelenmesini ve yalnızca Azure Backup 'den indirildikten sonra şifresinin çözülmesi sağlamak için bir parola kullanılır. Parola ayrıntıları yalnızca parolayı oluşturan kullanıcı ve onunla yapılandırılmış aracı için kullanılabilir. Hiçbir şey iletilmez veya hizmetle paylaşılmaz. Bu, yanlışlıkla ortaya çıkarılan tüm veriler (örneğin, ağ üzerinde bir ortadaki adam saldırısı gibi) parola olmadan kullanılamaz durumda ve parola ağ üzerinden gönderilmediğinde verilerinizin güvenliğinin tam olarak yapılmasını sağlar.

## <a name="compliance-with-standardized-security-requirements"></a>Standartlaştırılmış güvenlik gereksinimleriyle uyumluluk

Kuruluşların, kişilerin verilerinin toplanmasını ve kullanımını yöneten ulusal, bölgesel ve sektöre özgü gereksinimlere uyum sağlamak için Microsoft Azure & Azure Backup kapsamlı bir sertifika ve belirlediğimizi karşıladığımızı kümesi sunar. [Uyumluluk sertifikaları listesine bakın](compliance-offerings.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Backup kullanan bulut iş yüklerini korumanıza yardımcı olacak güvenlik özellikleri](backup-azure-security-feature-cloud.md)
* [Azure Backup kullanan karma yedeklemeleri korumanıza yardımcı olacak güvenlik özellikleri](backup-azure-security-feature.md)