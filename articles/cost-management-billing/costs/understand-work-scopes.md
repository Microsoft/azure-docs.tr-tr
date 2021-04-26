---
title: Azure Maliyet Yönetimi kapsamlarını anlama ve bunlarla çalışma
description: Bu makale, Azure'da sunulan faturalandırma ve kaynak yönetimi kapsamlarını anlamanıza ve bu kapsamları Maliyet Yönetimi ile API'lerde kullanmanıza yardımcı olmayı amaçlamaktadır.
author: bandersmsft
ms.author: banders
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 693045d094ddbf5ad6df81eca8cd16d9cac62fe4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308617"
---
# <a name="understand-and-work-with-scopes"></a>Kapsamları anlama ve bunlarla çalışma

Bu makale, Azure'da sunulan faturalandırma ve kaynak yönetimi kapsamlarını anlamanıza ve bu kapsamları Maliyet Yönetimi ile API'lerde kullanmanıza yardımcı olmayı amaçlamaktadır.

## <a name="scopes"></a>Kapsamlar

_Kapsam_, Azure AD kullanıcılarının hizmetlere eriştiği ve bunları yönettiği Azure kaynak hiyerarşisindeki düğümlerden biridir. Çoğu Azure kaynağı, aboneliklere ait olan kaynak grupları içinde oluşturulur ve dağıtılır. Microsoft ayrıca faturalandırma verilerini yönetmek için Azure aboneliklerinin üzerinde özel rollere sahip olan iki hiyerarşi sunmaktadır:
- Ödemeler ve faturalar gibi faturalandırma verileri
- Maliyet ve ilke idaresi gibi bulut hizmetleri

Kapsamlar; faturalandırma verilerini yönetmenizi, ödemelere özgü rollere sahip olmanızı, faturaları görüntülemenizi ve genel hesap yönetimi işlemlerini yaptığınız yerdir. Faturalama ve hesap rolleri, kaynak yönetimi için kullanılan ve [Azure RBAC](../../role-based-access-control/overview.md) kullanan rollerden ayrı yönetilir. Erişim denetimi farklılıkları dahil olmak üzere ayrı kapsamların amacını net bir şekilde belirlemek için _faturalama kapsamları_ ve _Azure RBAC kapsamları_ şeklinde kullanılır.

Kapsamlar hakkında daha fazla bilgi edinmek için [Maliyet Yönetimi hiyerarşileri ayarlama](https://www.youtube.com/watch?v=n3TLRaYJ1NY) videosunu izleyin. Diğer videoları izlemek için [Maliyet Yönetimi YouTube kanalını](https://www.youtube.com/c/AzureCostManagement) ziyaret edin.

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

## <a name="how-cost-management-uses-scopes"></a>Maliyet Yönetimi kapsamları nasıl kullanır?

Maliyet Yönetimi, ödeme hesabının tamamı veya tek bir kaynak grubu fark etmeksizin kuruluşların maliyetleri erişim sağladıkları düzeyde yönetmelerini sağlamak için kaynakların üzerindeki tüm kapsamlarda çalışır. Faturalama kapsamları Microsoft sözleşmenize (abonelik türüne) bağlı olsa da Azure RBAC kapsamları her zaman aynıdır.

## <a name="azure-rbac-scopes"></a>Azure RBAC kapsamları

Azure, kaynak yönetimi için üç kapsamı destekler. Her bir kapsam, maliyet yönetimi dahil olmak ancak bununla sınırlı olmamak üzere erişim ve idare yönetimini destekler.

- [**Yönetim grupları**](../../governance/management-groups/overview.md): Azure aboneliklerini düzenlemek için kullanılan ve en fazla sekiz düzeye kadar çıkan hiyerarşik kapsayıcılardır.

    Kaynak türü: [Microsoft.Management/managementGroups](/rest/api/managementgroups/)

- **Abonelikler**: Azure kaynaklarına yönelik birincil kapsayıcılardır.

    Kaynak türü: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Kaynak grupları**](../../azure-resource-manager/management/overview.md#resource-groups): Bir Azure çözümünün aynı yaşam döngüsünü paylaşan kaynaklarından oluşan mantıksal gruplardır. Bu kaynaklar birlikte dağıtılıp birlikte silinebilir.

    Kaynak türü: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Yönetim grupları, abonelikleri bir hiyerarşi altında düzenlemenizi sağlar. Örneğin yönetim gruplarını kullanarak mantıksal bir kuruluş hiyerarşisi oluşturabilirsiniz. Ardından ekiplerinize üretim ile geliştirme ve test iş yükleri için abonelik verebilirsiniz. Daha sonra aboneliklerde her bir alt sistemi veya bileşeni yönetmek için kaynak grupları oluşturabilirsiniz.

Kuruluş hiyerarşisi oluşturmak, maliyet ve ilke uyumluluğunun kuruluş genelinde yerleşmesini sağlar. Bu sayede her lider kendi maliyetlerini görüntüleyebilir ve analiz edebilir. Bu liderler daha sonra Danışman önerileriyle en alt düzeyde verimsiz harcama düzenlerini düzeltmek ve maliyetleri iyileştirmek için bütçe oluşturabilirler.

Maliyetleri görüntüleme ve isteğe bağlı olarak bütçeler ve dışarı aktarma işlemleri gibi maliyet yapılandırmasını yönetme erişimi verme işlemleri, Azure RBAC ile idare kapsamlarında gerçekleştirilir. Azure RBAC'yi kullanarak Azure AD kullanıcılarına ve gruplarına belirli bir kapsamda ve altında belirli bir rol için tanımlanmış olan önceden belirlenmiş eylemleri gerçekleştirme erişimi verebilirsiniz. Örneğin yönetim grubu kapsamında atanan bir rol, aynı izinleri iç içe yerleştirilmiş aboneliklere ve kaynak gruplarına da verir.

Maliyet Yönetimi, aşağıdaki kapsamlar için aşağıda belirtilen yerleşik rolleri destekler:

- [**Sahip**](../../role-based-access-control/built-in-roles.md#owner): Maliyetleri görüntüleyebilir ve maliyet yapılandırması da dahil olmak üzere her şeyi yönetebilir.
- [**Katkıda bulunan**](../../role-based-access-control/built-in-roles.md#contributor): Maliyetleri görüntüleyebilir ve maliyet yapılandırması dahil, ancak erişim denetimi hariç olmak üzere her şeyi yönetebilir.
- [**Okuyucu**](../../role-based-access-control/built-in-roles.md#reader): Maliyet verileri ve yapılandırma da dahil olmak üzere her şeyi görüntüleyebilir, ancak herhangi bir değişiklik yapamaz.
- [**Maliyet Yönetimi Katkıda Bulunanı**](../../role-based-access-control/built-in-roles.md#cost-management-contributor): Maliyetleri görüntüleyebilir, maliyet yapılandırmasını yönetebilir ve önerileri görüntüleyebilir.
- [**Maliyet Yönetimi Okuyucusu**](../../role-based-access-control/built-in-roles.md#cost-management-reader): Maliyet verilerini, maliyet yapılandırmasını ve önerileri görüntüleyebilir.

Maliyet Yönetimi Katkıda Bulunanı, önerilen en düşük ayrıcalıklı roldür. Rol, insanlara maliyetleri daha etkili şekilde izlemek ve raporlamak için bütçe ve dışarı aktarım oluşturma ve yönetme olanağı sağlar. Maliyet Yönetimi Katkıda Bulunanları, karmaşık maliyet yönetimi senaryolarını destekleyecek ek rollere de ihtiyaç duyabilir. Aşağıdaki senaryoları göz önünde bulundurun:

- **Kaynak kullanımını bildirme**: Azure Maliyet Yönetimi, maliyeti Azure portalda gösterir. Tam kullanım desenlerindeki maliyetle ilişkili olan kullanımı içerir. Bu rapor API ve indirme ücretlerini de gösterebilir, ancak bunları daha iyi kavramak için Azure İzleyici’deki ayrıntılı kullanım ölçümlerini incelemeniz yararlı olabilir. Ayrıntılı kullanım ölçümleriyle ilgili raporlamaya da ihtiyaç duyduğunuz tüm kapsamlarda [İzleyici Okuyucusu](../../role-based-access-control/built-in-roles.md#monitoring-reader)’nu etkinleştirebilirsiniz.
- **Bütçeler aşıldığında harekete geçme**: Maliyet Yönetimi Katkıda Bulunanlarının fazla kullanım durumlarına otomatik olarak müdahale edebilmek için eylem grubu oluşturma ve yönetme izinlerine de sahip olması gerekir. Bütçe eşikleri aşıldığında kullanılacak eylem grubunu içeren kaynak grubuna [İzleme Katkıda Bulunanı](../../role-based-access-control/built-in-roles.md#monitoring-contributor) iznini verebilirsiniz. Belirli eylemlerin otomatikleştirilmesi için Otomasyon ve Azure İşlevleri gibi kullanılan hizmetlere özgü ek rollere ihtiyaç duyulur.
- **Maliyet verilerini dışarı aktarmayı zamanlama**: Maliyet Yönetimi Katkıda Bulunanlarının, verileri depolama hesabına kopyalamak üzere bir dışarı aktarma işlemi zamanlamak için depolama hesaplarını yönetme izinlerine de sahip olması gerekir. Maliyet verilerinin dışarı aktarıldığı depolama hesabını içeren kaynak grubuna [Depolama Hesabı Katkıda Bulunanı](../../role-based-access-control/built-in-roles.md#storage-account-contributor) iznini verebilirsiniz.
- **Maliyet tasarrufu önerilerini görüntüleme**: Maliyet Yönetimi Okuyucuları ve Maliyet Yönetimi Katkıda Bulunanları, varsayılan olarak maliyet önerilerini *görüntüleme* erişimine sahiptir. Ancak maliyet önerilerine göre işlem yapmak için ilgili kaynaklar üzerinde erişim sahibi olmaları gerekir. Maliyet tabanlı önerilere göre hareket etmek istiyorsanız [hizmete özgü bir rol](../../role-based-access-control/built-in-roles.md#all) atamayı düşünebilirsiniz.

Yönetim grupları yalnızca Kurumsal Anlaşma (EA), Kullandıkça Öde (PAYG) veya Microsoft iş abonelikleri içeriyorsa desteklenir. Microsoft Müşteri Sözleşmesi veya Azure Active Directory abonelikleri gibi diğer abonelik türlerine sahip yönetim gruplarında maliyetler görüntülenemez. Abonelik türleriniz karışıksa desteklenen abonelikler için Maliyet Yönetimi’ni etkinleştirmek amacıyla, desteklenmeyen abonelikleri ayrı bir yönetim grubu hiyerarşisi koluna taşıyın. Örneğin, kök yönetim grubu kapsamında iki yönetim grubu oluşturun: **Azure AD** ve **Kuruluşum**. Azure AD aboneliğinizi **Azure AD** yönetim grubuna taşıyın ve sonra **Kuruluşum** yönetim grubunu kullanarak maliyetlerin görüntüleyip yönetin.

## <a name="enterprise-agreement-scopes"></a>Kurumsal Anlaşma kapsamları

Kayıt olarak da adlandırılan Kurumsal Anlaşma (EA) ödeme hesapları iki kapsama sahiptir:

- [**Ödeme hesabı**](../manage/view-all-accounts.md): Bir EA kaydını temsil eder. Faturalar bu kapsamda oluşturulur. Market ve rezervasyonlar gibi kullanım tabanlı olmayan satın alma işlemleri yalnızca bu kapsamda kullanılabilir. Bunlar departmanlarda veya kayıt hesaplarında temsil edilmez. Rezervasyon kullanımı, diğer tüm kullanımlarla birlikte tek tek kaynaklara uygulanır. Kullanım, faturalama hesabındaki aboneliklerde toplanır. Her kaynağa ayrılan rezervasyon maliyetlerini görmek için, maliyet analizinde **İtfa edilen maliyet** görünümüne geçin.

    Kaynak türü: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Departman**: İsteğe bağlı kayıt hesapları gruplaması.

    Kaynak türü: `Billing/billingAccounts/departments`

- **Kayıt hesabı**: Tek bir hesap sahibini temsil eder. Birden çok kişiye erişim vermeyi desteklemez.

    Kaynak türü: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

İdare kapsamları tek bir dizine bağlıdır ancak EA faturalama kapsamları öyle değildir. Bir EA ödeme hesabı, farklı Azure AD dizinlerinde birden fazla aboneliğe sahip olabilir.

EA faturalama kapsamları şu rolleri destekler:

- **Kuruluş yöneticisi**: Ödeme hesabı ayarlarını ve erişimini yönetebilir, tüm maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilir. Bütçeler ve dışarı aktarma işlemleri örnek olarak verilebilir. Pratikte EA faturalama kapsamı, [Maliyet Yönetimi Katkıda Bulunanı Azure rolü](../../role-based-access-control/built-in-roles.md#cost-management-contributor) ile aynıdır.
- **Kurumsal salt okunur kullanıcı**: Ödeme hesabı ayarlarını, maliyet verilerini ve maliyet yapılandırmasını görüntüleyebilir. Bütçeler ve dışarı aktarma işlemleri örnek olarak verilebilir. Pratikte EA faturalama kapsamı, [Maliyet Yönetimi Okuyucusu Azure rolü](../../role-based-access-control/built-in-roles.md#cost-management-reader) ile aynıdır.
- **Departman yöneticisi**: Maliyet merkezi gibi departman ayarlarını yönetebilir, tüm maliyetlere erişebilir, onları görüntüleyebilir ve maliyet yapılandırmasını yönetebilir. Bütçeler ve dışarı aktarma işlemleri örnek olarak verilebilir.  Departman yöneticilerinin ve salt okunur kullanıcıların maliyetleri görebilmesi için **DA ücretleri görüntüleme** fatura hesabı ayarının etkinleştirilmiş olması gerekir. **DA ücretleri görüntüleme** seçeneğinin devre dışı bırakılması halinde hesap veya abonelik sahibi olsalar dahi kullanıcılar herhangi bir düzeydeki maliyetleri göremezler.
- **Departman salt okunur kullanıcı**: Departman ayarlarını, maliyet verilerini ve maliyet yapılandırmasını görüntüleyebilir. Bütçeler ve dışarı aktarma işlemleri örnek olarak verilebilir. **DA ücretleri görüntüleme** seçeneğinin devre dışı bırakılması halinde hesap veya abonelik sahibi olsalar dahi kullanıcılar herhangi bir düzeydeki maliyetleri göremezler.
- **Hesap sahibi**: Kayıt hesabı ayarlarını (maliyet merkezi gibi) yönetebilir, tüm maliyetleri görüntüleyebilir ve kayıt hesabının maliyet yapılandırmasını (bütçeler ve dışarı aktarma işlemleri gibi) yönetebilir. Hesap sahiplerinin ve Azure RBAC kullanıcılarının maliyetleri görebilmesi için **AO ücretleri görüntüleme** faturalama hesabı ayarının etkinleştirilmiş olması gerekir.

EA ödeme hesabı kullanıcıları, faturalara doğrudan erişim sahibi değildir. Faturalar, dış toplu lisanslama sistemi aracılığıyla kullanılabilir.

Azure abonelikleri kayıt hesapları altında iç içe yerleştirilmiş şekilde bulunur. Faturalandırma kullanıcıları, kendi kapsamları altında yer alan aboneliklere ve kaynak gruplarına ait maliyet verilerine erişebilir. Bu kaynakları Azure portalında göremez ve yönetemezler. Kullanıcılar, Azure portaldaki hizmet listesinde **Maliyet Yönetimi + Faturalandırma**’ya giderek maliyetleri görüntüleyebilir. Bu kullanıcılar maliyetleri belirli aboneliklere ve kaynak gruplarına göre filtreleyerek ihtiyaç duydukları raporlara ulaşabilirler.

Faturalandırma kullanıcıları, açıkça belirli bir ödeme hesabının altına girmeyen yönetim gruplarına erişemez. Yönetim gruplarına açıkça erişim verilmelidir. Yönetim grupları, iç içe yerleştirilmiş aboneliklere ait toplam maliyetleri kapsar. Ancak yalnızca kullanım tabanlı satın alma işlemleri dahil edilir. Rezervasyonlar ve üçüncü taraf Market teklifleri gibi satın alma işlemleri dahil edilmez. Bu maliyetleri görüntülemek için EA ödeme hesabını kullanın.

## <a name="individual-agreement-scopes"></a>Bağımsız sözleşme kapsamları

Kullandıkça öde ve Ücretsiz Deneme ile geliştirme ve test teklifleri gibi bağımsız tekliflerden oluşturulan Azure abonelikleri özel ödeme hesabı kapsamına sahip değildir. Bunun yerine her abonelikte EA hesabı sahibi gibi bir hesap sahibi veya hesap yöneticisi vardır.

- [**Ödeme hesabı**](../manage/view-all-accounts.md): Bir veya daha fazla Azure aboneliği için tek bir hesap sahibini temsil eder. Şu anda birden fazla kişiye veya toplu maliyet görünümlerine erişim izni vermeyi desteklemez.

    Kaynak türü: Uygulanamaz

Bireysel Azure aboneliği hesap yöneticileri, [Azure Portal](https://portal.azure.com)aboneliklerinden fatura ve ödemeler gibi faturalandırma verilerini görüntüleyebilir ve yönetebilir  >   > bir abonelik seçin.

EA'dan farklı olarak bağımsız Azure abonelik hesabı yöneticileri, Azure portalında faturalarını görebilir. Maliyet Yönetimi Okuyucusu ile Maliyet Yönetimi Katkıda Bulunanı rollerinin fatura erişimi sağlamadığını unutmayın. Daha fazla bilgi için bkz. [Faturalara erişim izni verme](../manage/manage-billing-access.md#give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Microsoft Müşteri Sözleşmesi kapsamları

Microsoft Müşteri Sözleşmesi ödeme hesapları şu kapsamlara sahiptir:

- **Ödeme hesabı**: Birden çok Microsoft ürünü ve hizmeti için bir müşteri sözleşmesini temsil eder. Müşteri Sözleşmesi ödeme hesapları, işlev açısından EA kayıtları ile aynı değildir. EA kayıtları, faturalama profillerine daha yakından uyumludur.

    Kaynak türü: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Faturalama profili**: Faturaya dahil edilen abonelikleri tanımlar. Faturalama profilleri, faturaların oluşturulduğu kapsamda olduğundan EA kaydıyla aynı işlevlere sahiptir. Benzer şekilde kullanım tabanlı olmayan satın alma işlemleri (Market ve rezervasyonlar gibi) yalnızca bu kapsamda kullanılabilir. Bunlar fatura bölümlerine dahil edilmez.

    Kaynak türü: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Fatura bölümü** - Bir faturadaki veya faturalama profilindeki abonelik grubunu temsil eder. Fatura bölümleri departmanlar gibidir, bir fatura bölümünde birden fazla kişi erişim sağlayabilir.

    Kaynak türü: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Müşteri**: Bir iş ortağı tarafından Microsoft Müşteri Sözleşmesi'ne eklenmiş olan belirli bir müşteriyle ilişkilendirilmiş abonelik grubunu temsil eder. Bu kapsam, Bulut Çözümü Sağlayıcılarına (CSP) özeldir.

Müşteri Sözleşmesi ödeme hesapları, EA faturalama kapsamlarından farklı olarak tek bir dizine _bağlıdır_ ve birden fazla Azure AD dizininde farklı hesaplara sahip olamaz.

Müşteri Sözleşmesi faturalama kapsamları iş ortakları için geçerli değildir. İş ortağı rolleri ve izinleri, [Kullanıcı rollerini ve izinlerini atama](/partner-center/permissions-overview) bölümünde listelenmiştir.

Müşteri Sözleşmesi faturalama kapsamları şu rolleri destekler:

- **Sahip**: Faturalandırma ayarlarını yönetebilir, tüm maliyetlere erişebilir, bunları görüntüleyebilir ve maliyet yapılandırmasını yönetebilir. Bütçeler ve dışarı aktarma işlemleri örnek olarak verilebilir. Pratikte bu Müşteri Sözleşmesi faturalama kapsamı, [Maliyet Yönetimi Katkıda Bulunanı Azure rolü](../../role-based-access-control/built-in-roles.md#cost-management-contributor) ile aynıdır.
- **Katkıda bulunan**: Erişim haricindeki faturalandırma ayarlarını yönetebilir, tüm maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilir. Bütçeler ve dışarı aktarma işlemleri örnek olarak verilebilir. Pratikte bu Müşteri Sözleşmesi faturalama kapsamı, [Maliyet Yönetimi Katkıda Bulunanı Azure rolü](../../role-based-access-control/built-in-roles.md#cost-management-contributor) ile aynıdır.
- **Okuyucu**: Faturalandırma ayarlarını, maliyet verilerini ve maliyet yapılandırmasını görüntüleyebilir. Bütçeler ve dışarı aktarma işlemleri örnek olarak verilebilir. Pratikte bu Müşteri Sözleşmesi faturalama kapsamı, [Maliyet Yönetimi Okuyucusu Azure rolü](../../role-based-access-control/built-in-roles.md#cost-management-reader) ile aynıdır.
- **Fatura yöneticisi**: Faturaları görüntüleyip ödeyebilir, maliyet verilerini ve yapılandırmasını görüntüleyebilir. Bütçeler ve dışarı aktarma işlemleri örnek olarak verilebilir. Pratikte bu Müşteri Sözleşmesi faturalama kapsamı, [Maliyet Yönetimi Okuyucusu Azure rolü](../../role-based-access-control/built-in-roles.md#cost-management-reader) ile aynıdır.
- **Azure aboneliği oluşturucusu**: Azure aboneliği oluşturabilir, maliyetleri görüntüleyebilir ve maliyet yapılandırmasını yönetebilir. Bütçeler ve dışarı aktarma işlemleri örnek olarak verilebilir. Pratikte bu Müşteri Sözleşmesi faturalama kapsamı, EA kayıt hesabı sahibi rolüyle aynıdır.

Azure abonelikleri, EA kayıt hesaplarında olduğu gibi fatura bölümleri altında iç içe yerleştirilir. Faturalandırma kullanıcıları, kendi kapsamları altında yer alan aboneliklere ve kaynak gruplarına ait maliyet verilerine erişebilir. Ancak bu kaynakları Azure portalında göremez ve yönetemezler. Faturalandırma kullanıcıları, Azure portalındaki hizmet listesinde **Maliyet Yönetimi + Faturalandırma**'ya giderek maliyetleri görüntüleyebilir. Bu kullanıcılar maliyetleri belirli aboneliklere ve kaynak gruplarına göre filtreleyerek ihtiyaç duydukları raporlara ulaşabilirler.

Faturalandırma kullanıcıları, açıkça ödeme hesabının altına girmeyen yönetim gruplarına erişemez. Ancak yönetim grupları kuruluş için etkinleştirildiğinde tüm abonelik maliyetleri tek bir dizinle sınırlı olan ödeme hesabında ve kök yönetim grubunda toplanır. Yönetim grupları yalnızca kullanım tabanlı ücretleri içerir. Rezervasyonlar ve üçüncü taraf Market teklifleri gibi satın alma işlemleri yönetim gruplarına dahil edilmez. Bu nedenle ödeme hesabı ve kök yönetim grubu, farklı toplamlar verebilir. Bu maliyetleri görüntülemek için ödeme hesabını veya ilgili faturalama profilini kullanın.

## <a name="aws-scopes"></a>AWS kapsamları

AWS tümleştirmesi tamamlandıktan sonra bkz. [AWS tümleştirmesini ayarlama ve yapılandırma](aws-integration-set-up-configure.md). Aşağıdaki kapsamlar kullanılabilir:

- **Dış faturalama hesabı**: Üçüncü taraf satıcı ile yapılan müşteri sözleşmesini temsil eder. Bu, EA ödeme hesabı ile benzerdir.

    Kaynak türü: `Microsoft.CostManagement/externalBillingAccounts`

- **Dış abonelik**: Üçüncü taraf satıcı ile oluşturulan müşteri işlem hesabını temsil eder. Bu, Azure aboneliği ile benzerdir.

    Kaynak türü: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Bulut Çözümü Sağlayıcısı (CSP) kapsamları

Aşağıdaki kapsamlar, Microsoft Müşteri Sözleşmesi kapsamındaki müşteriler için CSP'lerde desteklenir:

- **Ödeme hesabı**: Birden çok Microsoft ürünü ve hizmeti için bir müşteri sözleşmesini temsil eder. Müşteri Sözleşmesi ödeme hesapları, işlev açısından EA kayıtları ile aynı değildir. EA kayıtları, faturalama profillerine daha yakından uyumludur.

    Kaynak türü: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Faturalama profili**: Faturaya dahil edilen abonelikleri tanımlar. Faturalama profilleri, faturaların oluşturulduğu kapsamda olduğundan EA kaydıyla aynı işlevlere sahiptir. Benzer şekilde kullanım tabanlı olmayan satın alma işlemleri (Market ve rezervasyonlar gibi) yalnızca bu kapsamda kullanılabilir.

    Kaynak türü: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Müşteri**: Bir iş ortağı tarafından Microsoft Müşteri Sözleşmesi'ne eklenmiş olan belirli bir müşteriyle ilişkilendirilmiş abonelik grubunu temsil eder.

Doğrudan iş ortağının Azure kiracısında bulunan ödeme hesapları, faturalama profilleri ve müşteriler ile ilgili maliyetleri yönetme ve görüntüleme işlemleri yalnızca *Genel yönetici* ve *Yönetici aracısı* rolleri tarafından gerçekleştirilebilir. İş ortağı merkezi rolleri hakkında daha fazla bilgi için bkz. [Kullanıcı rollerini ve izinlerini atama](/partner-center/permissions-overview).

Azure Maliyet Yönetimi, CSP iş ortağı müşterilerini yalnızca Microsoft Müşteri Sözleşmesi'ne sahip olmaları durumunda destekler. Henüz bir Microsoft Müşteri Sözleşmesi’ne sahip olmayan desteklenen CSP müşterileri için bkz. [İş Ortağı Merkezi](/azure/cloud-solution-provider/overview/partner-center-overview).

CSP kapsamlarındaki yönetim grupları Maliyet Yönetimi tarafından desteklenmez. CSP aboneliğiniz varsa ve maliyet analizinde bir yönetim grubuna kapsam belirliyorsanız şuna benzer bir hata görürsünüz:

`Management group <ManagementGroupName> does not have any valid subscriptions`

## <a name="switch-between-scopes-in-cost-management"></a>Maliyet Yönetimi'ndeki kapsamlar arasında geçiş yapma

Azure portalındaki tüm Maliyet Yönetimi görünümlerinin sol üst köşesinde **Kapsam** seçimi düğmesi bulunur. Bu düğmeyi kullanarak kapsamı hızlıca değiştirebilirsiniz. **Kapsam** düğmesini seçerek kapsam seçiciyi açın. Burada ödeme hesapları, kök yönetim grubu ve kök yönetim grubu altında bulunmayan abonelikler yer alır. Kapsam seçmek için arka planını seçerek vurgulayın ve en altta **Seç**’e tıklayın. Bir abonelik içindeki kaynak grupları gibi iç içe yerleştirilmiş olan kapsamların detayına gitmek için kapsam adı bağlantısını seçin. İç içe yerleştirilmiş düzeylerde üst kapsamı seçmek için kapsam seçicisinin en üstündeki **Bu &lt;kapsamı&gt; seç** bağlantısını seçin.

## <a name="identify-the-resource-id-for-a-scope"></a>Kapsamın kaynak kimliğini belirleme

Maliyet Yönetimi API'leriyle çalışırken kapsamı bilmek çok önemlidir. Maliyet Yönetimi API'leri için doğru kapsam URI'sini oluşturmak üzere aşağıdaki bilgileri kullanın.

### <a name="billing-accounts"></a>Ödeme hesapları

1. Azure portalını açın ve hizmetler listesinde **Maliyet Yönetimi + Faturalama**’ya gidin.
2. Ödeme hesabı menüsünde **Özellikler**'i seçin.
3. Ödeme hesabı kimliğini kopyalayın.
4. Kapsamınız: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Faturalama profilleri

1. Azure portalını açın ve hizmetler listesinde **Maliyet Yönetimi + Faturalama**’ya gidin.
2. Ödeme hesabı menüsünde **Faturalama profilleri**'ni seçin.
3. Faturalama profilinin adını seçin.
4. Faturalama profili menüsünde **Özellikler**'i seçin.
5. Ödeme hesabı ve faturalama profili kimliklerini kopyalayın.
6. Kapsamınız: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Fatura bölümleri

1. Azure portalını açın ve hizmetler listesinde **Maliyet Yönetimi + Faturalama**’ya gidin.
2. Ödeme hesabı menüsünde **Fatura bölümleri**'ni seçin.
3. Fatura bölümünün adını seçin.
4. Fatura bölümü menüsünde **Özellikler**'i seçin.
5. Ödeme hesabı ve fatura bölümü kimliklerini kopyalayın.
6. Kapsamınız: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA departmanları

1. Azure portalını açın ve hizmetler listesinde **Maliyet Yönetimi + Faturalama**’ya gidin.
2. Ödeme hesabı menüsünde **Departmanlar**'ı seçin.
3. Departmanın adını seçin.
4. Departman menüsünde **Özellikler**'i seçin.
5. Ödeme hesabı ve departman kimliklerini kopyalayın.
6. Kapsamınız: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA kaydı hesabı

1. Azure portalını açın ve hizmetler listesinde **Maliyet Yönetimi + Faturalama**’ya gidin.
2. Ödeme hesabı menüsünde **Kayıt hesapları**'nı seçin.
3. Kayıt hesabının adını seçin.
4. Kayıt hesabı menüsünde **Özellikler**'i seçin.
5. Faturalama hesabı ve kayıt hesabı kimliklerini kopyalayın.
6. Kapsamınız: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Yönetim grubu

1. Azure portalını açın ve hizmetler listesinde **Yönetim grupları**’na gidin.
2. Yönetim grubuna gidin.
3. Tablodan yönetim grubu kimliğini kopyalayın.
4. Kapsamınız: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Abonelik

1. Azure portalını açın ve hizmetler listesinde **Abonelikler**’e gidin.
2. Tablodan abonelik kimliğini kopyalayın.
3. Kapsamınız: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Kaynak grupları

1. Azure portalını açın ve hizmetler listesinde **Kaynak grupları**’na gidin.
2. Kaynak grubunun adını seçin.
3. Kaynak grubu menüsünde **Özellikler**'i seçin.
4. Kaynak kimliği alanındaki değeri kopyalayın.
5. Kapsamınız: `"/subscriptions/{id}/resourceGroups/{name}"`

Maliyet Yönetimi şu anda [Azure Global](https://management.azure.com) ve [Azure Kamu](https://management.usgovcloudapi.net) ortamlarında desteklenmektedir. Azure Kamu hakkında daha fazla bilgi için bkz. [Azure Global ve Kamu API uç noktaları](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping).

## <a name="next-steps"></a>Sonraki adımlar

- Maliyet Yönetimi için ilk hızlı başlangıcı önceden tamamlamadıysanız, [Maliyetleri analiz etmeye başlama](quick-acm-cost-analysis.md) bölümünden bilgi edinin.
