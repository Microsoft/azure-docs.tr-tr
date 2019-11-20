---
title: Azure Güvenlik Merkezi 'nde izlenen Azure ilke tanımları | Microsoft Docs
description: Azure Güvenlik Merkezi 'nde izlenen Azure ilke tanımları.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 8/22/2019
ms.author: memildin
ms.openlocfilehash: 892b33f52788bd37d430f221f14ba4c98f764cfb
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201671"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Güvenlik Merkezi tarafından izlenen Azure Güvenlik ilkeleri
Bu makale, Azure Güvenlik Merkezi 'nde izleyebilmeniz için Azure Ilke tanımlarının bir listesini sağlar. Güvenlik ilkeleri hakkında daha fazla bilgi için bkz. [güvenlik Ilkeleriyle çalışma](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Kullanılabilir güvenlik ilkeleri

Güvenlik Merkezi tarafından izlenen yerleşik ilkeler hakkında bilgi edinmek için aşağıdaki tabloya bakın:

| İlke | İlkenin yaptığı |
| --- | --- |
|Sanal makine ölçek kümelerindeki tanılama günlükleri etkinleştirilmelidir|Bir etkinlik izlemesinin bir olay veya uzlaşmadan sonra araştırılması için günlükleri etkinleştirmenizi öneririz.|
|RootManageSharedAccessKey hariç tüm yetkilendirme kuralları, Olay Hub 'ı ad alanından kaldırılmalıdır|Azure Event Hubs istemcileri, bir ad alanındaki tüm kuyruklara ve konulara erişim sağlayan bir ad alanı düzeyinde erişim ilkesi kullanmamalıdır. En az ayrıcalıklı güvenlik modeliyle uyum sağlamak için, yalnızca belirli varlığa erişim sağlamak üzere kuyruklar ve konular için varlık düzeyinde erişim ilkeleri oluşturmanız gerekir.|
|Olay Hub 'ı varlığındaki yetkilendirme kuralları tanımlanmalıdır|En az ayrıcalık erişimi sağlamak için Event Hubs varlıklarda yetkilendirme kurallarının varlığını denetleyin.|
|Güvenlik Duvarı ve sanal ağ yapılandırmalarına sahip depolama hesaplarına erişim kısıtlı olmalıdır|Depolama hesabı güvenlik duvarı ayarlarınızda Kısıtlanmamış ağ erişimini denetleyin. Yalnızca izin verilen ağların uygulamalarının depolama hesabına erişebilmesi için ağ kurallarını yapılandırın. Belirli bir internet veya şirket içi istemcilerden gelen bağlantılara izin vermek için, belirli Azure sanal ağlarından veya genel İnternet IP adresi aralıklarına giden trafiğe erişim izni verin.|
|Özel RBAC kurallarının kullanımını denetleme|Hataya açık özel rol tabanlı erişim denetimi (RBAC) rolleri yerine "sahip, katkıda bulunan, okuyucu" gibi yerleşik rolleri denetleyin. Özel rollerin kullanımı özel durum olarak değerlendirilir ve kapsamlı inceleme ve tehdit modelleme gerektirir.|
|Azure Stream Analytics tanılama günlükleri etkinleştirilmelidir|Günlüklerin etkinleştirilmesi için denetim yapın ve bunları bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma için etkinlik izleri oluşturur.|
|Depolama hesaplarına Güvenli aktarım etkinleştirilmelidir|Depolama hesabınızda güvenli aktarım için denetim gereksinimleri. Güvenli aktarım, depolama hesabınızı yalnızca güvenli bağlantılardan (HTTPS) istekleri kabul edecek şekilde zorlayan bir seçenektir. HTTPS kullanımı, sunucu ve hizmet arasında kimlik doğrulaması sağlar. Ayrıca, ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı aktarım halindeki verileri de korur.|
|SQL Server için Azure AD yöneticisi sağlanmalı|Azure AD kimlik doğrulamasını etkinleştirmek için SQL Server için bir Azure Active Directory (Azure AD) yöneticisinin sağlanmasına yönelik denetim. Azure AD kimlik doğrulaması, veritabanı kullanıcılarının ve diğer Microsoft hizmetlerinin Basitleştirilmiş izin yönetimini ve merkezi kimlik yönetimini destekler.|
|RootManageSharedAccessKey hariç tüm yetkilendirme kuralları Service Bus ad alanından kaldırılmalıdır|Azure Service Bus istemcileri, bir ad alanındaki tüm kuyruklara ve konulara erişim sağlayan bir ad alanı düzeyinde erişim ilkesi kullanmamalıdır. En az ayrıcalıklı güvenlik modeliyle uyum sağlamak için, yalnızca belirli varlığa erişim sağlamak üzere kuyruklar ve konular için varlık düzeyinde erişim ilkeleri oluşturun.|
|Service Bus tanılama günlükleri etkinleştirilmelidir|Günlüklerin etkinleştirilmesi için denetim yapın ve bunları yıllık olarak tutun. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma için etkinlik izleri oluşturur.|
|ClusterProtectionLevel özelliği EncryptAndSign içinde Service Fabric ayarlanmalıdır|Service Fabric, birincil küme sertifikası kullanan düğümden düğüme iletişim için üç koruma düzeyi sağlar: None, Sign ve EncryptAndSign. Tüm düğümler için iletileri şifrelenir ve dijital olarak imzalanmış emin olmak için koruma düzeyini ayarlayın.|
|İstemci kimlik doğrulaması Azure Active Directory kullanmalıdır|Service Fabric 'de yalnızca Azure AD aracılığıyla istemci kimlik doğrulamasının kullanımını denetleyin.|
|Arama hizmetlerindeki tanılama günlükleri etkinleştirilmelidir|Günlüklerin etkinleştirilmesi için denetim yapın ve bunları bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma için etkinlik izleri oluşturur.|
|Yalnızca Redis Cache güvenli bağlantılar etkinleştirilmelidir|Redin için yalnızca SSL ile SSL üzerinden bağlantı kurulmasını sağlayan denetim. Güvenli bağlantı kullanımı, sunucu ve hizmet arasında kimlik doğrulaması sağlar. Ayrıca, ortadaki adam, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı aktarım halindeki verileri de korur.|
|Logic Apps tanılama günlükleri etkinleştirilmelidir|Günlüklerin etkinleştirilmesi için denetim yapın ve bunları bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma için etkinlik izleri oluşturur.|
|Key Vault tanılama günlükleri etkinleştirilmelidir|Günlüklerin etkinleştirilmesi için denetim yapın ve bunları bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma için etkinlik izleri oluşturur.|
|Olay Hub 'ındaki tanılama günlükleri etkinleştirilmelidir|Günlüklerin etkinleştirilmesi için denetim yapın ve bunları bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma için etkinlik izleri oluşturur.|
|Azure Data Lake Store tanılama günlükleri etkinleştirilmelidir|Günlüklerin etkinleştirilmesini denetleme ve bunları bir yıla kadar tutma. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma için etkinlik izleri oluşturur.|
|Data Lake Analytics tanılama günlükleri etkinleştirilmelidir|Günlüklerin etkinleştirilmesi için denetim yapın ve bunları bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma için etkinlik izleri oluşturur.|
|Depolama hesapları yeni Azurerd kaynaklarına geçirilmelidir|Güvenlik geliştirmeleri sağlamak için depolama hesaplarınız için Azure Resource Manager kullanın. Bunlar: <br>-Daha güçlü erişim denetimi (RBAC)<br>-Daha iyi denetim<br>-Azure Resource Manager tabanlı dağıtım ve idare<br>-Yönetilen kimliklere erişim<br>-Gizlilikler için Azure Key Vault erişim<br>-Azure AD tabanlı kimlik doğrulaması<br>-Daha kolay güvenlik yönetimi için Etiketler ve kaynak grupları desteği|
|Sanal makinelerin yeni Azurerd kaynaklarına geçirilmesi gerekir|Güvenlik geliştirmeleri sağlamak için sanal makineleriniz için Azure Resource Manager kullanın. Bunlar: <br>-Daha güçlü erişim denetimi (RBAC)<br>-Daha iyi denetim<br>-Azure Resource Manager tabanlı dağıtım ve idare<br>-Yönetilen kimliklere erişim<br>-Gizlilikler için Azure Key Vault erişim<br>-Azure AD tabanlı kimlik doğrulaması<br>-Daha kolay güvenlik yönetimi için Etiketler ve kaynak grupları desteği|
|Toplu Iş hesaplarında ölçüm uyarısı kuralları yapılandırılmalıdır|Gerekli ölçümü etkinleştirmek için Azure Batch hesaplarında ölçüm uyarı kurallarının yapılandırmasını denetleyin.|
|Batch hesaplarındaki tanılama günlükleri etkinleştirilmelidir|Günlüklerin etkinleştirilmesi için denetim yapın ve bunları bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma için etkinlik izleri oluşturur.|
|Otomasyon hesabı değişkenlerinde şifreleme etkinleştirilmelidir|Hassas verileri depoladığınızda Azure Otomasyonu hesap değişkeni varlıklarının şifrelenmesini etkinleştirmek önemlidir.|
|Uygulama hizmetlerindeki tanılama günlükleri etkinleştirilmelidir|Uygulamada tanılama günlüklerinin etkinleştirilmesini denetleme. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma için etkinlik izleri oluşturur.|
|SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir|SQL veritabanları için saydam veri şifreleme durumunu denetleyin.|
|SQL Server denetimi etkinleştirilmelidir|Sunucu düzeyinde SQL denetiminin varlığını denetleyin.|
|\[Önizleme]: Azure Güvenlik Merkezi 'nde şifrelenmemiş SQL veritabanını izleme|Azure Güvenlik Merkezi, şifrelenmemiş SQL sunucularını veya veritabanlarını önerilen şekilde izler.|
|\[Önizleme]: Azure Güvenlik Merkezi 'nde denetlenen SQL veritabanını izleme|Azure Güvenlik Merkezi, SQL Server ve SQL denetimi 'nin açık olmadığı veritabanlarını izler.|
|\[Önizleme]: Sistem güncelleştirmelerinin makinelerinizde yüklü olması gerekir|Azure Güvenlik Merkezi, sunucularınızda eksik olan güvenlik sistemi güncelleştirmelerini izler.|
|\[Önizleme]: Depolama hesapları için eksik blob şifrelemesini denetle|Blob şifrelemesi kullanmayan depolama hesaplarını denetleme. Bu, diğer sağlayıcılardan depolama için değil, yalnızca Microsoft depolama kaynak türleri için geçerlidir. Azure Güvenlik Merkezi, olası ağ, tam zamanında erişimi önerilen şekilde izler.|
|\[Önizleme]: Tam zamanında ağ erişim denetimi, sanal makinelere uygulanmalıdır|Azure Güvenlik Merkezi, olası ağ, tam zamanında erişimi önerilen şekilde izler.|
|\[Önizleme]: Uyarlamalı uygulama denetimleri sanal makinelerde etkinleştirilmelidir|Azure Güvenlik Merkezi, olası uygulama beyaz listesi yapılandırmasını izler.|
|\[Önizleme]: Sanal makineler için eksik ağ güvenlik grupları yapılandırılmalıdır|Azure Güvenlik Merkezi, çok fazla izin veren kurallara göre önerilen ağ güvenlik gruplarını izler.|
|\[Önizleme]: Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir|Azure Güvenlik Merkezi, önerilen temeli uygun şekilde karşılamadığı sunucuları izler.| 
|\[Önizleme]: Endpoint Protection 'ın sanal makinelere yüklenmesi gerekir|Azure Güvenlik Merkezi, yüklü bir Microsoft System Center Endpoint Protection aracısına sahip olmayan sunucuları, önerilen şekilde izler.|
|\[Önizleme]: Disk şifrelemesi sanal makinelere uygulanmalıdır|Azure Güvenlik Merkezi, kullanılması gereken disk şifrelemesi etkinleştirilmemiş sanal makineleri izler.|
|\[Önizleme]: Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir|Güvenlik açığı değerlendirme çözümü tarafından algılanan güvenlik açıklarını izleyin ve Azure Güvenlik Merkezi 'nde güvenlik açığı değerlendirmesi çözümü olmayan VM 'Ler önerilir.|
|\[Önizleme]: Azure Güvenlik Merkezi 'nde korumasız Web uygulamasını izleme|Azure Güvenlik Merkezi, önerilen Web uygulaması güvenlik duvarı koruması olmayan Web uygulamalarını izler.|
|\[Önizleme]: Uç nokta koruma çözümünün sanal makinelere yüklenmesi gerekir|Azure Güvenlik Merkezi, önerilen yeni nesil güvenlik duvarı koruması olmayan ağ uç noktalarını izler.|
|\[Önizleme]: SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir|Güvenlik açığı değerlendirmesi tarama sonuçlarını izleyin ve veritabanı güvenlik açıklarını nasıl düzeltebileceğiniz önerilir.|
|\[Önizleme]: Aboneliğiniz için en fazla 3 sahip belirtilmelidir|Güvenliği aşılmış bir sahibe göre ihlal olasılığını azaltmak için en fazla üç abonelik sahibi belirlemeniz önerilir.|
|\[Önizleme]: Aboneliğinize birden fazla sahip atanmalıdır|Yönetici erişiminin artıklığını sağlamak için birden fazla abonelik sahibi belirlemeniz önerilir.|
|\[Önizleme]: MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir |Hesapların veya kaynakların ihlal oluşmasını engellemek için sahip izinlerine sahip tüm abonelik hesaplarında Multi-Factor Authentication (MFA) etkinleştirilmelidir.|
|\[Önizleme]: MFA, abonelik hesaplarınız üzerinde yazma izinlerine sahip olmalıdır|Hesapların veya kaynakların ihlal oluşmasını engellemek için yazma izinlerine sahip tüm abonelik hesaplarında Multi-Factor Authentication etkinleştirilmelidir.|
|\[Önizleme]: MFA, okuma izinlerine sahip abonelik hesaplarınız üzerinde etkinleştirilmelidir|Hesapların veya kaynakların ihlal oluşmasını engellemek için okuma iznine sahip tüm abonelik hesaplarında Multi-Factor Authentication etkinleştirilmelidir.|
|\[Önizleme]: Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır|Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır. Kullanım dışı bırakılan hesapların oturum açması engellendi.|
|\[Önizleme]: Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır|Kullanım dışı bırakılan hesaplar aboneliklerinizden kaldırılmalıdır. Kullanım dışı bırakılan hesapların oturum açması engellendi.|
|\[Önizleme]: Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır|İzin erişimini engellemek için, sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır.|
|\[Önizleme]: Yazma izinleri olan dış hesapların aboneliğinizden kaldırılması gerekir|İzlenmeyen erişimi engellemek için, yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir.|
|\[Önizleme]: Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir|İzlenmeyen erişimi engellemek için okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir.|




## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Güvenlik Merkezi'nde güvenlik ilkelerinin nasıl yapılandırılacağını öğrendiniz. Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın.

* [Azure Güvenlik Merkezi planlama ve işlemler Kılavuzu](security-center-planning-and-operations-guide.md): Azure Güvenlik Merkezi 'nde tasarım konularını nasıl planlayacağınızı ve anlayacağınızı öğrenin.
* [Azure Güvenlik Merkezi 'Nde güvenlik durumu izleme](security-center-monitoring.md): Azure kaynaklarınızı durumunu izleme hakkında bilgi edinin.
* [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md): Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile iş ortağı çözümlerini izleme](security-center-partner-solutions.md): İş ortağı çözümlerinizin sistem durumunu izleme hakkında bilgi edinin.
* [Azure Güvenlik MERKEZI SSS](security-center-faq.md): Hizmet kullanımı ile ilgili sık sorulan soruların yanıtlarını alın.
* [Azure Güvenlik blogu](https://blogs.msdn.com/b/azuresecurity/): Azure güvenliği ve uyumluluğu ile ilgili blog yazılarını bulun.

Azure Ilkesi hakkında daha fazla bilgi edinmek için bkz. [Azure ilkesi nedir?](../governance/policy/overview.md).
