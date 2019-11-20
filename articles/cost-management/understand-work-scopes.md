---
title: Azure maliyet yönetimi kapsamlarını anlayın ve bunlarla çalışın
description: Bu makale, Azure 'da kullanılabilen faturalandırma ve kaynak yönetimi kapsamlarını anlamanıza ve maliyet yönetimi ve API 'lerde kapsamları nasıl kullanacağınızı anlamanıza yardımcı olur.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 1f02cf3abaae7d67ba3d204dc9419d9fbfa4a86d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597094"
---
# <a name="understand-and-work-with-scopes"></a>Kapsamları anlama ve bunlarla çalışma

Bu makale, Azure 'da kullanılabilen faturalandırma ve kaynak yönetimi kapsamlarını anlamanıza ve maliyet yönetimi ve API 'lerde kapsamları nasıl kullanacağınızı anlamanıza yardımcı olur.

## <a name="scopes"></a>Kapsamlar

_Kapsam_ , Azure AD kullanıcılarının Hizmetlere erişen ve Hizmetleri yönetmesine sonra Azure kaynak hiyerarşisindeki bir düğümdür. Çoğu Azure kaynağı, aboneliklerinin bir parçası olan kaynak gruplarına oluşturulup dağıtılır. Microsoft, faturalandırma verilerini yönetmek için özel rollere sahip Azure aboneliklerinin üzerinde de iki hiyerarşi sunar:
- Ödemeler ve faturalar gibi faturalandırma verileri
- Maliyet ve ilke yönetimi gibi bulut Hizmetleri

Kapsam, faturalandırma verilerini yönettiğiniz, ödemelere özgü rollere sahip olan roller, faturaları görüntüleme ve genel hesap yönetimi yürütme yerdir. Faturalandırma ve hesap rolleri, [Azure RBAC](../role-based-access-control/overview.md)kullanan kaynak yönetimi için kullanılanlardan ayrı olarak yönetilir. Erişim denetimi farklılıkları da dahil olmak üzere ayrı kapsamların amacını açıkça ayırt etmek için, bunlar sırasıyla _faturalandırma kapsamları_ ve _RBAC kapsamları_olarak adlandırılır.

## <a name="how-cost-management-uses-scopes"></a>Maliyet yönetimi kapsamları nasıl kullanır

Maliyet yönetimi, kuruluşların, tüm faturalama hesabı ya da tek bir kaynak grubu olmak üzere erişim sahibi oldukları düzeyde maliyetleri yönetmesine olanak tanımak için kaynakların üzerindeki tüm kapsamlar üzerinde çalışmaktadır. Faturalama kapsamları Microsoft anlaşmanıza (abonelik türü) göre farklılık gösterir, ancak RBAC kapsamları desteklemez.

## <a name="azure-rbac-scopes"></a>Azure RBAC kapsamları

Azure, kaynak yönetimi için üç kapsamı destekler. Her kapsam, maliyet yönetimi dahil ancak bunlarla sınırlı olmamak üzere erişim ve idare yönetimini destekler.

- Azure aboneliklerini düzenlemek için, [**Yönetim grupları**](../governance/management-groups/overview.md) -en fazla sekiz düzey olan hiyerarşik kapsayıcılar.

    Kaynak türü: [Microsoft. Management/managementGroups](/rest/api/resources/managementgroups)

- **Abonelikler** -Azure kaynakları için birincil kapsayıcılar.

    Kaynak türü: [Microsoft. resources/abonelikler](/rest/api/resources/subscriptions)

- [**Kaynak grupları**](../azure-resource-manager/resource-group-overview.md#resource-groups) -aynı yaşam döngüsünü paylaşan bir Azure çözümü için ilgili kaynakların mantıksal gruplandırmaları. Örneğin, birlikte dağıtılan ve silinen kaynaklar.

    Kaynak türü: [Microsoft. resources/abonelikler/resourceGroups](/rest/api/resources/resourcegroups)

Yönetim grupları, abonelikleri bir hiyerarşiye düzenlemenizi sağlar. Örneğin, Yönetim gruplarını kullanarak bir mantıksal kuruluş hiyerarşisi oluşturabilirsiniz. Ardından, üretim ve geliştirme/test iş yükleri için takımlar abonelikleri verin. Ve sonra her bir alt sistemi veya bileşeni yönetmek için aboneliklerde kaynak grupları oluşturun.

Kuruluş hiyerarşisi oluşturmak, maliyet ve ilke uyumluluğu toplaması kuruluş sağlar. Ardından, her öncü geçerli maliyetlerini görüntüleyebilir ve analiz edebilir. Daha sonra, ücretsiz harcama desenleri oluşturmak ve en düşük düzeyde danışman önerileriyle maliyetleri iyileştirmek için bütçeleri oluşturabilir.

Maliyetleri görüntüleme ve isteğe bağlı olarak bütçe ve dışarı aktarma gibi maliyet konfigürasyonunu yönetme erişimi verme, Azure RBAC kullanılarak idare kapsamları üzerinde gerçekleştirilir. Belirli bir kapsamdaki ve aşağıdaki bir rolde tanımlanmış bir dizi önceden tanımlanmış eylem kümesi gerçekleştirmek için Azure RBAC 'yi Azure AD kullanıcılarına ve gruplarına erişim sağlamak üzere kullanırsınız. Örneğin, bir yönetim grubu kapsamına atanan bir rol, iç içe abonelikler ve kaynak grupları için de aynı izinleri verir.

Maliyet yönetimi aşağıdaki kapsamların her biri için aşağıdaki yerleşik rolleri destekler:

- [**Sahip**](../role-based-access-control/built-in-roles.md#owner) – maliyet yapılandırması dahil olmak üzere maliyetleri görüntüleyebilir ve her şeyi yönetebilir.
- [**Katkıda bulunan**](../role-based-access-control/built-in-roles.md#contributor) – maliyet yapılandırması dahil, ancak erişim denetimi hariç olmak üzere maliyetleri görüntüleyebilir ve her şeyi yönetebilir.
- [**Okuyucu**](../role-based-access-control/built-in-roles.md#reader) – maliyet verileri ve yapılandırma dahil olmak üzere her şeyi görüntüleyebilir, ancak herhangi bir değişiklik yapamaz.
- [**Maliyet yönetimi katılımcısı**](../role-based-access-control/built-in-roles.md#cost-management-contributor) – maliyetleri görüntüleyebilir, maliyet yapılandırmasını yönetebilir ve önerileri görüntüleyebilirsiniz.
- [**Maliyet yönetimi okuyucusu**](../role-based-access-control/built-in-roles.md#cost-management-reader) – maliyet verilerini, maliyet yapılandırmasını görüntüleyebilir ve önerileri görüntüleyebilirsiniz.

Maliyet yönetimi katılımcısı, önerilen en düşük ayrıcalıklı roldür. Kullanıcıların bütçeleri oluşturup yönetmesine ve maliyetleri daha etkin bir şekilde izlemelerine ve rapor etmesine olanak tanır. Maliyet yönetimi katılımcıları, uçtan uca maliyet yönetimi senaryolarını desteklemek için ek roller de gerektirebilir. Aşağıdaki senaryoları göz önünde bulundurun:

- **Bütçeler aşıldığında davran** – maliyet yönetimi katkıda bulunanlar Ayrıca fazla kullanım için otomatik olarak tepki vermek üzere eylem grupları oluşturma ve/veya yönetme erişimi de gerektirir. Bütçe eşikleri aşıldığında kullanılacak eylem grubunu içeren bir kaynak grubuna [Izleme katılımcısı](../role-based-access-control/built-in-roles.md#monitoring-contributor) vermeyi göz önünde bulundurun. Belirli eylemlerin otomatikleştirilmesi, otomasyon ve Azure Işlevleri gibi, kullanılan belirli hizmetler için ek roller gerektirir.
- **Maliyet verilerinin dışarı aktarılmasını zamanla** – maliyet yönetimi katılımcıları, verileri bir depolama hesabına kopyalamak üzere bir dışarı aktarma zamanlamak için depolama hesaplarını yönetme erişimi de gerektirir. Maliyet verilerinin aktarılacağı depolama hesabını içeren bir kaynak grubuna [depolama hesabı katılımcısı](../role-based-access-control/built-in-roles.md#storage-account-contributor) vermeyi göz önünde bulundurun.
- Maliyet **tasarrufu önerilerini görüntüleme** – maliyet yönetimi okuyucuları ve maliyet yönetimi katılımcıları, maliyet önerilerini varsayılan olarak *görüntüleme* erişimine sahiptir. Ancak, maliyet önerilerinde işlem için erişim, tek tek kaynaklara erişim gerektirir. Maliyet tabanlı bir öneri üzerinde işlem yapmak istiyorsanız [hizmete özgü bir rol](../role-based-access-control/built-in-roles.md#built-in-role-descriptions) vermeyi göz önünde bulundurun.

## <a name="enterprise-agreement-scopes"></a>Kurumsal Anlaşma kapsamları

Kayıt olarak da bilinen Kurumsal Anlaşma (EA) Faturalandırma hesapları aşağıdaki kapsamlara sahiptir:

- [**Faturalandırma hesabı**](../billing/billing-view-all-accounts.md) -bir EA kaydını temsil eder. Faturalar bu kapsamda oluşturulur. Market ve rezervasyonlar gibi kullanım tabanlı olmayan satın alma işlemleri yalnızca bu kapsamda kullanılabilir. Departmanlar veya kayıt hesaplarında temsil edilmez.

    Kaynak türü: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Departman** -isteğe bağlı kayıt hesapları gruplandırması.

    Kaynak türü: `Billing/billingAccounts/departments`

- **Kayıt hesabı** -tek bir hesap sahibini temsil eder. Birden çok kişiye erişim vermeyi desteklemez.

    Kaynak türü: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

İdare kapsamları tek bir dizine bağlansa da, EA faturalandırma kapsamları değildir. Bir EA faturalandırma hesabının herhangi bir sayıda Azure AD dizininde aboneliği olabilir.

EA faturalandırma kapsamları aşağıdaki rolleri destekler:

- **Kurumsal Yönetici** – faturalandırma hesabı ayarlarını ve erişimini yönetebilir, tüm maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilir. Örneğin, bütçeler ve dışarı aktarmalar. İşlevinde, EA faturalandırma kapsamı, [maliyet yönetimi katılımcısı Azure RBAC rolüyle](../role-based-access-control/built-in-roles.md#cost-management-contributor)aynıdır.
- **Kurumsal salt okunurdur Kullanıcı** – faturalandırma hesabı ayarlarını, maliyet verilerini ve maliyet yapılandırmasını görüntüleyebilir. Örneğin, bütçeler ve dışarı aktarmalar. İşlevinde, EA faturalandırma kapsamı, [maliyet yönetimi okuyucusu Azure RBAC rolüyle](../role-based-access-control/built-in-roles.md#cost-management-reader)aynıdır.
- **Departman Yöneticisi** – maliyet merkezi gibi departman ayarlarını yönetebilir ve erişim sağlayabilir, tüm maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilir. Örneğin, bütçeler ve dışarı aktarmalar.  Ayrıca, maliyetleri görmek için departman yöneticileri ve salt okuma kullanıcıları için de **görüntüleme ücretleri** faturalandırma hesabı ayarı etkinleştirilmelidir. **Da görünüm ücretleri** devre dışıysa, departman kullanıcıları hesap veya abonelik sahibi olsalar bile herhangi bir düzeyde maliyeti göremez.
- **Bölüm salt okuma kullanıcısı** – departman ayarlarını, maliyet verilerini ve maliyet yapılandırmasını görüntüleyebilir. Örneğin, bütçeler ve dışarı aktarmalar. **Da görünüm ücretleri** devre dışıysa, departman kullanıcıları hesap veya abonelik sahibi olsalar bile herhangi bir düzeyde maliyeti göremez.
- **Hesap sahibi** – kayıt hesabı için kayıt hesabı ayarlarını (maliyet merkezi gibi) yönetebilir, tüm maliyetleri görüntüleyebilir ve maliyet yapılandırmasını (örneğin, bütçeler ve dışarı aktarmalar) yönetebilirsiniz. Hesap sahipleri için **Ao görünüm ücretleri** faturalandırma hesabı ayarı etkinleştirilmelidir ve kullanıcıların maliyetleri görmesini sağlar.

EA faturalandırma hesabı kullanıcıları faturalara doğrudan erişemez. Faturalar, bir dış toplu lisanslama sisteminde kullanılabilir.

Azure abonelikleri kayıt hesapları altında iç içe geçmiş. Faturalandırma kullanıcıları, kendi kapsamları altında olan abonelikler ve kaynak grupları için maliyet verilerine erişebilir. Azure portal kaynakları görmek veya yönetmek için erişimleri yoktur. Faturalama kullanıcıları **maliyet yönetimi + faturalandırma** ' e giderek maliyetleri görüntüleyebilir Azure Portal hizmet listesi. Ardından, maliyetleri raporlamak için gereken belirli abonelikler ve kaynak grupları için filtre uygulayabilir.

Faturalandırma kullanıcıları, belirli bir faturalandırma hesabı altında açıkça düşmedikleri için yönetim gruplarına erişemez. Erişim, yönetim gruplarına açıkça verilmelidir. Yönetim grupları tüm iç içe aboneliklerden alınan maliyetleri geri alma. Ancak, yalnızca kullanım tabanlı satın alma işlemleri dahil değildir. Rezervasyonlar ve üçüncü taraf Market teklifleri gibi satın almalara dahil değildir. Bu maliyetleri görüntülemek için EA faturalandırma hesabını kullanın.

## <a name="individual-agreement-scopes"></a>Bireysel anlaşma kapsamları

Kullandıkça öde ve ücretsiz deneme ve geliştirme/test teklifleri gibi bireysel tekliflerden oluşturulan Azure abonelikleri, açık bir faturalandırma hesabı kapsamına sahip değildir. Bunun yerine, her aboneliğin EA hesabı sahibi gibi bir hesap sahibi veya hesap yöneticisi vardır.

- [**Faturalandırma hesabı**](../billing/billing-view-all-accounts.md) -bir veya daha fazla Azure aboneliği için tek bir hesap sahibini temsil eder. Bu, şu anda birden çok kişiye erişim vermeyi veya toplu maliyet görünümlerine erişimi desteklemez.

    Kaynak türü: geçerli değil

Bireysel Azure aboneliği hesap yöneticileri, [Azure Hesap Merkezi](https://account.azure.com/subscriptions)fatura ve ödemeler gibi faturalandırma verilerini görüntüleyebilir ve yönetebilir. Ancak, maliyet verilerini görüntüleyemez veya Azure portal kaynakları yönetebilir. Hesap yöneticisine erişim vermek için, daha önce bahsedilen maliyet yönetimi rollerini kullanın.

EA 'dan farklı olarak, bireysel Azure aboneliği hesap yöneticileri Azure portal faturalarını görüntüleyebilir. Maliyet yönetimi okuyucusu ve maliyet yönetimi katkıda bulunan rollerinin faturalara erişim sağlamadığını aklınızda bulundurun. Daha fazla bilgi için bkz. [faturalara erişim verme](../billing/billing-manage-access.md##give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Microsoft müşteri anlaşması kapsamları

Microsoft Müşteri Sözleşmesi faturalandırma hesaplarında aşağıdaki kapsamlar vardır:

- **Faturalandırma hesabı** -birden çok Microsoft ürünü ve hizmeti için bir müşteri sözleşmesi temsil eder. Müşteri Sözleşmesi faturalandırma hesapları, EA kayıtları ile aynı şekilde işlevsel değildir. EA kayıtları, faturalandırma profillerine daha yakından hizalanır.

    Kaynak türü: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Faturalandırma profili** -bir faturaya dahil edilen abonelikleri tanımlar. Fatura profilleri, faturaların oluşturulduğu kapsam olduğundan, bir EA kaydının işlevsel eşdeğeridir. Benzer şekilde, kullanım tabanlı olmayan (Market ve rezervasyonlar gibi) satın alma işlemleri yalnızca bu kapsamda kullanılabilir. Bunlar fatura bölümlerine dahil edilmez.

    Kaynak türü: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Fatura bölümü** -bir fatura veya faturalandırma profilindeki bir abonelik grubunu temsil eder. Fatura bölümleri departmanlar gibi, birden çok kişi de bir fatura bölümüne erişebilir.

    Kaynak türü: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Müşteri** -iş ortağı tarafından Microsoft Müşteri anlaşmasıyla eklendi belirli bir müşteriyle ilişkili bir abonelik grubunu temsil eder. Bu kapsam CSP 'ye özeldir.

EA faturalandırma kapsamlarından farklı olarak, Müşteri Sözleşmesi _faturalandırma hesapları tek_ bir dizine bağlanır ve birden çok Azure AD dizininde abonelikler olamaz.

Müşteri Sözleşmesi faturalandırma kapsamları iş ortakları için uygulanmaz. İş ortağı rolleri ve izinleri, [Kullanıcı rolleri ve Izinleri atama](/partner-center/permissions-overview)konusunda belgelenmiştir.

Müşteri Sözleşmesi faturalandırma kapsamları aşağıdaki rolleri destekler:

- **Sahip** – fatura ayarlarını yönetebilir ve erişimi, tüm maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilir. Örneğin, bütçeler ve dışarı aktarmalar. İşlevinde, bu müşteri sözleşmesi faturalandırma kapsamı, [maliyet yönetimi katılımcısı Azure RBAC rolüyle](../role-based-access-control/built-in-roles.md#cost-management-contributor)aynıdır.
- **Katkıda bulunan** – erişim hariç faturalandırma ayarlarını yönetebilir, tüm maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilirsiniz. Örneğin, bütçeler ve dışarı aktarmalar. İşlevinde, bu müşteri sözleşmesi faturalandırma kapsamı, [maliyet yönetimi katılımcısı Azure RBAC rolüyle](../role-based-access-control/built-in-roles.md#cost-management-contributor)aynıdır.
- **Okuyucu** – faturalandırma ayarlarını, maliyet verilerini ve maliyet yapılandırmasını görüntüleyebilir. Örneğin, bütçeler ve dışarı aktarmalar. İşlevinde, bu müşteri sözleşmesi faturalandırma kapsamı, [maliyet yönetimi okuyucusu Azure RBAC rolüyle](../role-based-access-control/built-in-roles.md#cost-management-reader)aynıdır.
- **Fatura Yöneticisi** – faturaları görüntüleyebilir ve ödeyebilir ve maliyet verilerini ve yapılandırmayı görüntüleyebilir. Örneğin, bütçeler ve dışarı aktarmalar. İşlevinde, bu müşteri sözleşmesi faturalandırma kapsamı, [maliyet yönetimi okuyucusu Azure RBAC rolüyle](../role-based-access-control/built-in-roles.md#cost-management-reader)aynıdır.
- **Azure abonelik Oluşturucu** – Azure abonelikleri oluşturabilir, maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilir. Örneğin, bütçeler ve dışarı aktarmalar. İşlevinde, bu müşteri sözleşmesi faturalandırma kapsamı, EA kayıt hesabı sahibi rolüyle aynıdır.

Azure abonelikleri, EA kayıt hesapları altında oldukları gibi, fatura bölümlerinin altına yuvalanmıştır. Faturalandırma kullanıcıları, kendi kapsamları altındaki abonelikler ve kaynak grupları için maliyet verilerine erişebilir. Ancak, Azure portal kaynakları görmek veya yönetmek için erişimleri yoktur. Faturalama kullanıcıları **maliyet yönetimi + faturalandırma** ' e giderek maliyetleri görüntüleyebilir Azure Portal hizmet listesi. Ardından, maliyetleri raporlamak için gereken belirli abonelikler ve kaynak grupları için filtreleyin.

Faturalama hesabı, faturalandırma hesabının altına açık olmadıkları için yönetim gruplarına erişemez. Ancak, kuruluş için yönetim grupları etkinleştirildiğinde, her ikisi de tek bir dizinle sınırlı olduklarından, tüm abonelik ücretleri faturalandırma hesabına ve kök yönetim grubuna aktarılır. Yönetim grupları yalnızca kullanım tabanlı satın alımları içerir. Rezervasyonlar ve üçüncü taraf Market teklifleri gibi satın alma işlemleri, yönetim gruplarına dahil değildir. Bu nedenle, faturalandırma hesabı ve kök yönetim grubu farklı toplamlar rapor edebilir. Bu maliyetleri görüntülemek için faturalandırma hesabını veya ilgili faturalandırma profilini kullanın.

## <a name="aws-scopes"></a>AWS kapsamları

AWS tümleştirmesi tamamlandıktan sonra, bkz. [AWS tümleştirmesini ayarlama ve yapılandırma](aws-integration-set-up-configure.md). Aşağıdaki kapsamlar kullanılabilir:

- **Dış faturalandırma hesabı** -üçüncü taraf satıcı ile bir müşteri anlaşmasını temsil eder. Bu, EA faturalandırma hesabına benzer.

    Kaynak türü: `Microsoft.CostManagement/externalBillingAccounts`

- **Dış abonelik** -üçüncü taraf satıcı ile bir müşteri operasyonel hesabını temsil eder. Bu, bir Azure aboneliğine benzerdir.

    Kaynak türü: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Bulut çözümü sağlayıcısı (CSP) kapsamları

Aşağıdaki kapsamlar, Microsoft Müşteri anlaşmasındaki müşterilerin bulunduğu CSP 'Ler için desteklenir:

- **Faturalandırma hesabı** -birden çok Microsoft ürünü ve hizmeti için bir müşteri sözleşmesi temsil eder. Müşteri Sözleşmesi faturalandırma hesapları, EA kayıtları ile aynı şekilde işlevsel değildir. EA kayıtları, faturalandırma profillerine daha yakından hizalanır.

    Kaynak türü: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Faturalandırma profili** -bir faturaya dahil edilen abonelikleri tanımlar. Fatura profilleri, faturaların oluşturulduğu kapsam olduğundan, bir EA kaydının işlevsel eşdeğeridir. Benzer şekilde, kullanım tabanlı olmayan (Market ve rezervasyonlar gibi) satın alma işlemleri yalnızca bu kapsamda kullanılabilir.

    Kaynak türü: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Müşteri** -bir iş ortağı tarafından Microsoft Müşteri anlaşmasıyla eklendi belirli bir müşteriyle ilişkili bir abonelik grubunu temsil eder.

Yalnızca *genel yönetici* ve yönetici Aracısı rollerine sahip olan kullanıcılar, doğrudan ortağın Azure *kiracısında* faturalandırma hesapları, faturalandırma profilleri ve müşteriler için maliyetleri yönetebilir ve görüntüleyebilir. İş Ortağı Merkezi rolleri hakkında daha fazla bilgi için bkz. [Kullanıcı rolleri ve Izinleri atama](/partner-center/permissions-overview).

Azure maliyet yönetimi yalnızca müşterilerin bir Microsoft Müşteri Anlaşması varsa CSP iş ortağı müşterilerini destekler. Henüz bir Microsoft Müşteri sözleşmenizde olmayan CSP desteği olan müşteriler için bkz. [Iş Ortağı Merkezi](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Maliyet yönetimi 'nde kapsamlar arasında geçiş yapma

Azure Portal tüm maliyet yönetimi görünümleri, görünümün sol üst kısmındaki **kapsam** seçimi hap ' i içerir. Kapsamı hızlı bir şekilde değiştirmek için kullanın. Kapsam seçiciyi açmak için **kapsama** tıklayın. Faturalandırma hesaplarını, kök yönetim grubunu ve kök yönetim grubu altında iç içe olmayan abonelikleri gösterir. Bir kapsam seçmek için, üst plana tıklayarak vurgulayın ve ardından altta **Seç** ' e tıklayın. Bir abonelikteki kaynak grupları gibi iç içe kapsamların ayrıntısına gitmek için kapsam adı bağlantısına tıklayın. Herhangi bir iç içe düzeydeki üst kapsamı seçmek için, kapsam seçicinin üst kısmında **bu &lt;scope seçin &gt;** ' e tıklayın.

## <a name="identify-the-resource-id-for-a-scope"></a>Bir kapsamın kaynak KIMLIĞINI tanımla

Maliyet yönetimi API 'Leri ile çalışırken, kapsamın kritik olduğunu bilmektir. Maliyet yönetimi API 'Leri için doğru kapsam URI 'sini derlemek üzere aşağıdaki bilgileri kullanın.

### <a name="billing-accounts"></a>Faturalandırma hesapları

1. Azure portal açın ve ardından hizmetler listesinden **maliyet yönetimi + faturalandırma** ' a gidin.
2. Faturalandırma hesabı menüsünde **Özellikler** ' i seçin.
3. Faturalandırma hesabı KIMLIĞINI kopyalayın.
4. Kapsamınız: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Faturalama profilleri

1. Azure portal açın ve ardından hizmetler listesinden **maliyet yönetimi + faturalandırma** ' a gidin.
2. Faturalandırma hesabı menüsünde **faturalandırma profilleri** ' ni seçin.
3. İstenen fatura profilinin adına tıklayın.
4. Faturalandırma profili menüsünde **Özellikler** ' i seçin.
5. Faturalandırma hesabı ve faturalandırma profili kimliklerini kopyalayın.
6. Kapsamınız: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Fatura bölümleri

1. Azure portal açın ve ardından hizmetler listesinden **maliyet yönetimi + faturalandırma** ' a gidin.
2. Faturalandırma hesabı menüsünde **Fatura bölümleri** ' ni seçin.
3. İstenen fatura bölümünün adına tıklayın.
4. Fatura bölüm menüsünde **Özellikler** ' i seçin.
5. Faturalandırma hesabı ve fatura bölümü kimliklerini kopyalayın.
6. Kapsamınız: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA departmanları

1. Azure portal açın ve ardından hizmetler listesinden **maliyet yönetimi + faturalandırma** ' a gidin.
2. Faturalandırma hesabı menüsünde **Departmanlar** ' ı seçin.
3. İstenen departmanın adına tıklayın.
4. Departman menüsünde **Özellikler** ' i seçin.
5. Faturalandırma hesabı ve departman kimliklerini kopyalayın.
6. Kapsamınız: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA kayıt hesabı

1. Azure portal açın ve Hizmetler listesinde **maliyet yönetimi + faturalandırma** ' a gidin.
2. Faturalandırma hesabı menüsünde **kayıt hesapları** ' nı seçin.
3. İstenen kayıt hesabının adına tıklayın.
4. Kayıt hesabı menüsünde **Özellikler** ' i seçin.
5. Faturalandırma hesabı ve kayıt hesabı kimliklerini kopyalayın.
6. Kapsamınız: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Yönetim grubu

1. Azure portal açın ve Hizmetler listesinde **Yönetim grupları** ' na gidin.
2. İstenen yönetim grubuna gidin.
3. Yönetim grubu KIMLIĞINI tablodan kopyalayın.
4. Kapsamınız: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Abonelik

1. Azure portal açın ve hizmetler listesinden **abonelikler** ' e gidin.
2. Tablodaki abonelik KIMLIĞINI kopyalayın.
3. Kapsamınız: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Kaynak grupları

1. Azure portal açın ve hizmetler listesinden **kaynak grupları** ' na gidin.
2. İstenen kaynak grubunun adına tıklayın.
3. Kaynak grubu menüsünde **Özellikler** ' i seçin.
4. Kaynak KIMLIĞI alan değerini kopyalayın.
5. Kapsamınız: `"/subscriptions/{id}/resourceGroups/{name}"`

Maliyet yönetimi şu anda [Azure genel](https://management.azure.com) ve [Azure Kamu](https://management.usgovcloudapi.net)'da desteklenmektedir. Azure Kamu hakkında daha fazla bilgi için bkz. [Azure genel ve kamu API uç noktaları](../azure-government/documentation-government-developer-guide.md#endpoint-mapping) _._

## <a name="next-steps"></a>Sonraki adımlar

- Maliyet yönetimi için ilk hızlı başlangıcı Henüz tamamlamadıysanız, [maliyetleri çözümlemeye başlamak](quick-acm-cost-analysis.md)için bunu okuyun.
