---
title: Azure Sentinel çalışma alanlarını ölçekli olarak yönetme
description: Temsilcili müşteri kaynakları üzerinde Azure Sentinel 'i etkin bir şekilde yönetmeyi öğrenin.
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 009edaefe021dedb5d9a40a8cc3bac2c2974ae10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702530"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Azure Sentinel çalışma alanlarını ölçekli olarak yönetme

Hizmet sağlayıcı olarak, [Azure açık Thouse](../overview.md)için birden fazla müşteri kiracısından eklendi olabilirsiniz. Azure Mathouse, hizmet sağlayıcılarının birden çok Azure Active Directory (Azure AD) kiracılarının aynı anda, yönetim görevlerini daha verimli hale getiren işlemleri gerçekleştirmesine olanak tanır.

Azure Sentinel, uyarı algılama, tehdit görünürlüğü, proaktif arama ve tehdit yanıtı için tek bir çözüm sunan güvenlik analizlerini ve tehdit bilgilerini sunar. Azure açık bir şekilde, kiracılar genelinde birden çok Azure Sentinel çalışma alanını ölçekli bir şekilde yönetebilirsiniz. Bu, birden çok çalışma alanı genelinde sorgu çalıştırma veya içgörüler elde etmek için bağlı veri kaynaklarınızdaki verileri görselleştirmek ve izlemek üzere çalışma kitapları oluşturmak gibi senaryolara olanak sağlar. Sorgular ve PlayBook 'lar gibi IP, yönetim kiracınızda kalır, ancak müşteri kiracılarında güvenlik yönetimi gerçekleştirmek için kullanılabilir.

Bu konuda, [Azure Sentinel](../../sentinel/overview.md) 'in çapraz kiracı görünürlüğü ve yönetilen güvenlik hizmetleri için ölçeklenebilir bir şekilde nasıl kullanılacağına ilişkin bir genel bakış sunulmaktadır.

> [!TIP]
> Bu konudaki hizmet sağlayıcılarına ve müşterilere başvurduğumuz halde, bu kılavuz [birden çok kiracıyı yönetmek Için Azure açık Thouse kullanan kuruluşlar](../concepts/enterprise.md)için de geçerlidir.

## <a name="architectural-considerations"></a>Mimari konuları

Azure Sentinel kullanarak bir hizmet olarak güvenlik teklifi oluşturmak isteyen bir yönetilen güvenlik hizmeti sağlayıcısı (MSSP) için tek bir güvenlik işlem merkezi (SOC), tek bir müşteri kiracılarında dağıtılan birden çok Azure Sentinel çalışma alanını merkezi olarak izlemek, yönetmek ve yapılandırmak için gerekli olabilir. Benzer şekilde, birden çok Azure AD kiracılarına sahip kuruluşlar, kiracılar genelinde dağıtılan birden çok Azure Sentinel çalışma alanını merkezi olarak yönetmek isteyebilir.

Bu merkezi dağıtım modelinin aşağıdaki avantajları vardır:

- Verilerin sahipliği, her bir yönetilen kiracıyla kalır.
- Verileri coğrafi sınırlar içinde depolamak için gereksinimleri destekler.
- Birden çok müşteriye ait veriler aynı çalışma alanında depolandığından veri yalıtımının yapılmasını sağlar.
- Verilerin uyumluluğunu sağlamaya yardımcı olmak için yönetilen kiracılardan veri alımını önler.
- İlgili maliyetler, Kiracı Yönetimi yerine her bir yönetilen kiracı için ücretlendirilir.
- Azure Sentinel ile tümleştirilmiş tüm veri kaynaklarından ve veri bağlayıcılarından (Azure AD etkinlik günlükleri, Office 365 günlükleri veya Microsoft tehdit koruması uyarıları gibi) veriler her müşteri kiracısında kalır.
- Ağ gecikmesini azaltır.
- Yeni yan kuruluşlar veya müşteriler ekleme veya kaldırma kolaytı.

> [!NOTE]
> Farklı [bölgelerde](../../availability-zones/az-overview.md#regions)bulunan temsilcili kaynakları yönetebilirsiniz. Ancak, bir [Ulusal bulut](../../active-directory/develop/authentication-national-cloud.md) ve Azure genel bulutu genelinde veya iki ayrı ulusal bulutta abonelikler temsilciliğini desteklemez.

## <a name="granular-azure-role-based-access-control-azure-rbac"></a>Ayrıntılı Azure rol tabanlı erişim denetimi (Azure RBAC)

Bir MSSP 'nin yöneteceği her bir müşteri aboneliği, [Azure eklendi to Use](onboard-customer.md)' a sahip olmalıdır. Bu, yönetim kiracısındaki belirlenen kullanıcıların, müşteri kiracılarında dağıtılan Azure Sentinel çalışma alanlarında yönetim işlemlerine erişmesini ve bu işlemleri gerçekleştirmesini sağlar.

Yetkilendirmeleri oluştururken, yönetim kiracınızdaki kullanıcılara, gruplara veya hizmet sorumlularına Azure Sentinel yerleşik rollerini atayabilirsiniz:

- [Azure Sentinel okuyucusu](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel Yanıtlayıcısı](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel Katılımcısı](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Ek işlevler gerçekleştirmek için ek yerleşik roller de atamak isteyebilirsiniz. Azure Sentinel ile kullanılabilen belirli roller hakkında daha fazla bilgi için bkz. [Azure Sentinel izinleri](../../sentinel/roles.md).

Müşterilerinize eklendi, atanan kullanıcılar, yönetilen kiracınızda oturum açıp [müşterinin Azure Sentinel çalışma alanına atanmış rollerle doğrudan erişim](../../sentinel/multiple-tenants-service-providers.md) sağlayabilir.

## <a name="view-and-manage-incidents-across-workspaces"></a>Çalışma alanları genelinde olayları görüntüleme ve yönetme

Birden çok müşteri için Azure Sentinel kaynaklarını yönetiyorsanız, aynı anda birden fazla kiracıda birden fazla çalışma alanındaki olayları görüntüleyebilir ve yönetebilirsiniz. Daha fazla bilgi için bkz. [aynı anda birçok çalışma alanında olaylarla çalışma](../../sentinel/multiple-workspace-view.md) ve [Azure Sentinel 'i çalışma alanları ve kiracılar arasında genişletme](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Yönettiğiniz kiracılarınızdaki kullanıcılara, yönetilen tüm çalışma alanları üzerinde okuma ve yazma izinleri atandığından emin olun. Bir kullanıcının bazı çalışma alanlarında okuma izinleri varsa, bu çalışma alanlarında olay seçerken uyarı iletileri görüntülenebilir ve Kullanıcı bu olayları veya seçtiğiniz diğer diğerlerini (diğerleri için izinleriniz olsa bile) değiştiremezler.

## <a name="configure-playbooks-for-mitigation"></a>PlayBook 'ları azaltma için yapılandırma

[Playbooks](../../sentinel/tutorial-respond-threats-playbook.md) , bir uyarı tetiklendiğinde otomatik risk azaltma için kullanılabilir. Bu PlayBook 'lar el ile çalıştırılabilir veya belirli uyarılar tetiklendiğinde otomatik olarak çalıştırılabilirler. PlayBook 'lar yönetim kiracısında veya müşteri kiracısında dağıtılabilir ve bu, kiracının kullanıcılarına bir güvenlik tehdidi karşılığında işlem yapması gereken yanıt yordamlarını sağlar.

## <a name="create-cross-tenant-workbooks"></a>Çapraz kiracı çalışma kitapları oluşturma

Azure ['Da Azure Izleyici çalışma kitapları](../../sentinel/overview.md#workbooks) , Öngörüler kazanmak için bağlı veri kaynaklarınızdan verileri görselleştirmenize ve izlemenize yardımcı olur. Azure Sentinel 'de yerleşik çalışma kitabı şablonlarını kullanabilir veya senaryolarınız için özel çalışma kitapları oluşturabilirsiniz.

Çalışma kitaplarını yönetim kiracınızda dağıtabilir ve müşteri kiracılarında verileri izlemek ve sorgulamak için ölçekli panolar oluşturabilirsiniz. Daha fazla bilgi için bkz. [çapraz çalışma alanı izleme](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks). 

Çalışma kitaplarını, bu müşteriye özgü senaryolar için yönettiğiniz tek bir kiracıya doğrudan da dağıtabilirsiniz.

## <a name="run-log-analytics-and-hunting-queries-across-azure-sentinel-workspaces"></a>Azure Sentinel çalışma alanları genelinde Log Analytics ve sorguları çalıştırma

[Sorguları](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting)yönetme dahil olmak üzere, tehdit algılama için Log Analytics sorguları yönetim kiracısında merkezi olarak oluşturun ve kaydedin. Bu sorgular daha sonra tüm müşterilerinizin Azure Sentinel çalışma alanlarında UNION işleci ve Workspace () ifadesi kullanılarak çalıştırılabilir. Daha fazla bilgi için bkz. [çapraz çalışma alanı sorgulama](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Çoklu çalışma alanları yönetimi için Otomasyonu kullanma

Otomasyonu, birden çok Azure Sentinel çalışma alanını yönetmek ve [sorguları](../../sentinel/hunting.md), PlayBook 'ları ve çalışma kitaplarını yapılandırmak için kullanabilirsiniz. Daha fazla bilgi için bkz. [Otomasyon kullanarak çapraz çalışma alanı yönetimi](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

## <a name="monitor-security-of-office-365-environments"></a>Office 365 ortamlarının güvenliğini izleme

Kiracılar genelinde Office 365 ortamlarının güvenliğini izlemek için Azure ve Azure Sentinel ile birlikte Azure birlikte kullanımı 'nı kullanın. İlk, kullanıma hazır Office 365 veri bağlayıcıları, Exchange ve SharePoint 'teki Kullanıcı ve yönetici Etkinlikleri (OneDrive dahil) hakkındaki bilgilerin yönetilen Kiracıdaki bir Azure Sentinel çalışma alanına dahil edilmesi için [yönetilen kiracı 'da etkinleştirilmelidir](../../sentinel/connect-office-365.md) . Bu, dosya indirmeleri, gönderilen erişim istekleri, Grup olayları ve posta kutusu işlemlerinde değişiklikler ve bu eylemleri gerçekleştiren kullanıcılar hakkında bilgiler içerir. [Office 365 DLP uyarıları](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820) , yerleşik Office 365 bağlayıcısının bir parçası olarak da desteklenir.

[Microsoft Cloud App Security (MCAS) bağlayıcısını](../../sentinel/connect-cloud-app-security.md) , uyarıları ve Cloud Discovery günlüklerini Azure Sentinel 'e akışa almak için etkinleştirebilirsiniz. Bu, bulut uygulamalarına yönelik görünürlük elde etmenizi, siber tehditleri belirleyip işlemenize yönelik gelişmiş analizler almanızı ve verilerin nasıl hareket edebilirliğini denetlemenizi sağlar. MCAS için etkinlik günlükleri [ortak olay biçimi (CEF) kullanılarak tüketilebilir](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849).

Office 365 veri bağlayıcıları ayarladıktan sonra, çalışma kitaplarındaki verileri görüntüleme ve analiz etme, özel uyarılar oluşturmak için sorguları kullanma ve tehditleri yanıtlamak üzere PlayBook 'ları yapılandırma gibi platformlar arası Azure Sentinel özelliklerini kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Sentinel](../../sentinel/overview.md)hakkında bilgi edinin.
- [Azure Sentinel fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/azure-sentinel/)gözden geçirin.
- [Çapraz kiracı yönetim deneyimleri](../concepts/cross-tenant-management-experience.md)hakkında bilgi edinin.

