---
title: Azure Otomasyonu 'nda rol izinlerini ve güvenliği yönetme
description: Bu makalede, Azure kaynakları için erişim yönetimine olanak sağlayan Azure rol tabanlı erişim denetimi 'nin (Azure RBAC) nasıl kullanılacağı açıklanır.
keywords: otomasyon rbac, rol tabanlı erişim denetimi, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 07/21/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0727d3342c73d9aa4d15e84aacb82bd8fea01d65
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833589"
---
# <a name="manage-role-permissions-and-security"></a>Rol izinlerini ve güvenliği yönetme

Azure rol tabanlı erişim denetimi (Azure RBAC), Azure kaynakları için erişim yönetimine izin vermez. [Azure RBAC](../role-based-access-control/overview.md)'yi kullanarak ekipinizdeki görevleri ayırabilirsiniz ve yalnızca işlerini gerçekleştirmek için ihtiyaç duydukları kullanıcılara, gruplara ve uygulamalara erişim miktarını verebilirsiniz. Azure portal, Azure Command-Line araçları veya Azure Yönetim API 'Lerini kullanarak kullanıcılara rol tabanlı erişim izni verebilirsiniz.

## <a name="roles-in-automation-accounts"></a>Otomasyon hesaplarındaki roller

Azure Otomasyonu 'nda, Otomasyon hesabı kapsamındaki kullanıcılara, gruplara ve uygulamalara uygun Azure rolü atanarak erişim verilir. Aşağıda Automation hesabının desteklediği yerleşik roller bulunmaktadır:

| **Role** | **Açıklama** |
|:--- |:--- |
| Sahip |Sahip rolü, Otomasyon hesabını yönetmek için diğer kullanıcılara, gruplara ve uygulamalara erişim sağlamak dahil olmak üzere Otomasyon hesabı içindeki tüm kaynaklara ve eylemlere erişim sağlar. |
| Katılımcı |Katılımcı rolü, başka kullanıcının Otomasyon hesabına erişim izinlerini değiştirme dışında her şeyi yönetmenizi sağlar. |
| Okuyucu |Okuyucu rolü, Otomasyon hesabında tüm kaynakları görmenizi sağlar; ancak değişiklik yapamazsınız. |
| Otomasyon Operatörü |Otomasyon Işletmeni rolü, runbook adını ve özelliklerini görüntülemenize ve bir Otomasyon hesabındaki tüm runbook 'lar için iş oluşturmanıza ve yönetmenize olanak sağlar. Bu rol, kimlik bilgileri varlıkları ve Runbook 'ları gibi Otomasyon hesabı kaynaklarınızı görüntülenmesini veya değiştirilmesini, ancak kuruluşunuzun üyelerinin bu runbook 'ları yürütmesine izin vermeyi hala sağlamak istiyorsanız yararlıdır. |
|Automation Iş Işleci|Otomasyon Iş Işletmeni rolü, bir Otomasyon hesabındaki tüm runbook 'lar için iş oluşturmanıza ve yönetmenize olanak sağlar.|
|Otomasyon Runbook Işleci|Otomasyon Runbook Işleci rolü, bir runbook 'un adını ve özelliklerini görüntülemenize izin verir.|
| Log Analytics Katkıda Bulunan | Log Analytics katkıda bulunan rolü, tüm izleme verilerini okumanızı ve izleme ayarlarını düzenlemenizi sağlar. İzleme ayarlarını Düzenle, VM uzantılarının sanal makinelere eklenmesini, Azure depolama 'dan günlüklerin toplanmasını yapılandırabilmek için depolama hesabı anahtarlarını okumayı, Otomasyon hesapları oluşturmayı ve yapılandırmayı, Azure Otomasyonu özelliklerini eklemeyi ve tüm Azure kaynaklarında Azure tanılamayı yapılandırmayı içerir.|
| Log Analytics Okuyucusu | Log Analytics okuyucu rolü, tüm izleme verilerini görüntülemenize ve aramanıza ve izleme ayarlarını görüntülemenize olanak sağlar. Bu, tüm Azure kaynaklarında Azure tanılama 'nın yapılandırılmasını görüntülemeyi içerir. |
| Katkıda bulunan izleniyor | Izleme katılımcısı rolü, tüm izleme verilerini okumanızı ve izleme ayarlarını güncelleştirmenizi sağlar.|
| İzleme okuyucusu | Izleme okuyucu rolü, tüm izleme verilerini okumanızı sağlar. |
| Kullanıcı Erişimi Yöneticisi |Kullanıcı Erişimi Yöneticisi rolü, Azure Otomasyonu hesaplarına kullanıcı erişimini yönetmenizi sağlar. |

## <a name="role-permissions"></a>Rol izinleri

Aşağıdaki tablolar her role verilen belirli izinleri anlatmaktadır. Bu, izinleri veren eylemleri ve bunları kısıtlayan NotActions öğesini içerebilir.

### <a name="owner"></a>Sahip

Bir sahip, erişim dahil her şeyi yönetebilir. Aşağıdaki tabloda rol için verilen izinler gösterilmektedir:

|Eylemler|Açıklama|
|---|---|
|Microsoft. Automation/automationAccounts/|Tüm türlerin kaynaklarını oluşturun ve yönetin.|

### <a name="contributor"></a>Katılımcı

Katkıda bulunan, erişim dışında her şeyi yönetebilir. Aşağıdaki tabloda rol için verilen ve Reddedilen izinler gösterilmektedir:

|**Eylemler**  |**Açıklama**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/|Tüm türlerin kaynaklarını oluşturma ve yönetme|
|**Eylem değil**||
|Microsoft. Authorization/*/Delete| Rolleri ve rol atamalarını silin.       |
|Microsoft. Authorization/*/Write     |  Roller ve rol atamaları oluşturun.       |
|Microsoft. Authorization/yükseltir Teaccess/Action    | Kullanıcı Erişim Yöneticisi oluşturma özelliğini reddeder.       |

### <a name="reader"></a>Okuyucu

Okuyucu bir Otomasyon hesabındaki tüm kaynakları görüntüleyebilir, ancak herhangi bir değişiklik yapamaz.

|**Eylemler**  |**Açıklama**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/Read|Otomasyon hesabındaki tüm kaynakları görüntüleyin. |

### <a name="automation-operator"></a>Otomasyon Operatörü

Otomasyon operatörü işleri oluşturabilir ve yönetebilir ve bir Otomasyon hesabındaki tüm runbook 'lar için Runbook adlarını ve özellikleri okuyabilir.

>[!NOTE]
>Tek tek runbook 'lara İşletmen erişimini denetlemek istiyorsanız bu rolü ayarlayın. Bunun yerine, **Otomasyon Işi operatörü** ve **Otomasyon Runbook işleci** rollerini birlikte kullanın.

Aşağıdaki tabloda rol için verilen izinler gösterilmektedir:

|**Eylemler**  |**Açıklama**  |
|---------|---------|
|Microsoft. Authorization/*/Read|Yetkilendirmeyi okuyun.|
|Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read|Karma runbook çalışanı kaynaklarını okuyun.|
|Microsoft. Automation/automationAccounts/Jobs/okuma|Runbook 'un işlerini listeleyin.|
|Microsoft. Automation/automationAccounts/Jobs/sürdürülecek/Action|Duraklatılmış bir işi sürdürür.|
|Microsoft. Automation/automationAccounts/Jobs/durdur/eylem|Sürmekte olan bir işi iptal edin.|
|Microsoft. Automation/automationAccounts/Jobs/akışlar/okuma|Iş akışlarını ve çıktıyı okuyun.|
|Microsoft. Automation/automationAccounts/Jobs/output/Read|Bir işin çıkışını alın.|
|Microsoft. Automation/automationAccounts/Jobs/beklet/eylem|Sürmekte olan bir işi duraklatın.|
|Microsoft. Automation/automationAccounts/Jobs/Write|İş oluşturun.|
|Microsoft. Automation/automationAccounts/Jobzamanlamalar/okuma|Azure Otomasyonu iş zamanlaması alın.|
|Microsoft. Automation/automationAccounts/Jobzamanlamalar/yazma|Azure Otomasyonu iş zamanlaması oluşturma.|
|Microsoft. Automation/automationAccounts/linkedWorkspace/Read|Otomasyon hesabına bağlı çalışma alanını alın.|
|Microsoft. Automation/automationAccounts/Read|Azure Otomasyonu hesabı alın.|
|Microsoft. Automation/automationAccounts/runbook 'lar/okuma|Azure Otomasyonu runbook 'u alın.|
|Microsoft. Automation/automationAccounts/zamanlamalar/okuma|Azure Otomasyonu zamanlama varlığı alın.|
|Microsoft. Automation/automationAccounts/zamanlamalar/yazma|Azure Otomasyonu zamanlama varlığı oluşturun veya güncelleştirin.|
|Microsoft. resources/abonelikler/resourceGroups/Read      |Rolleri ve rol atamalarını okuyun.         |
|Microsoft. resources/dağıtımlar/*      |Kaynak grubu dağıtımlarını oluşturun ve yönetin.         |
|Microsoft. Insights/alertRules/*      | Uyarı kuralları oluşturun ve yönetin.        |
|Microsoft. support/* |Destek biletleri oluşturun ve yönetin.|

### <a name="automation-job-operator"></a>Automation Iş Işleci

Otomasyon hesabı kapsamında bir Otomasyon Işi operatörü rolü verilir.Bu, İşletmen izinlerinin hesaptaki tüm runbook 'lar için iş oluşturmasına ve yönetmesine izin verir. Iş Işleci rolüne Otomasyon hesabını içeren kaynak grubunda okuma izinleri verildiyse, rolün üyeleri runbook 'ları başlatma yeteneğine sahiptir. Ancak, bunları oluşturma, düzenleme veya silme yeteneğine sahip değildir.

Aşağıdaki tabloda rol için verilen izinler gösterilmektedir:

|**Eylemler**  |**Açıklama**  |
|---------|---------|
|Microsoft. Authorization/*/Read|Yetkilendirmeyi okuyun.|
|Microsoft. Automation/automationAccounts/Jobs/okuma|Runbook 'un işlerini listeleyin.|
|Microsoft. Automation/automationAccounts/Jobs/sürdürülecek/Action|Duraklatılmış bir işi sürdürür.|
|Microsoft. Automation/automationAccounts/Jobs/durdur/eylem|Sürmekte olan bir işi iptal edin.|
|Microsoft. Automation/automationAccounts/Jobs/akışlar/okuma|Iş akışlarını ve çıktıyı okuyun.|
|Microsoft. Automation/automationAccounts/Jobs/beklet/eylem|Sürmekte olan bir işi duraklatın.|
|Microsoft. Automation/automationAccounts/Jobs/Write|İş oluşturun.|
|Microsoft. resources/abonelikler/resourceGroups/Read      |  Rolleri ve rol atamalarını okuyun.       |
|Microsoft. resources/dağıtımlar/*      |Kaynak grubu dağıtımlarını oluşturun ve yönetin.         |
|Microsoft. Insights/alertRules/*      | Uyarı kuralları oluşturun ve yönetin.        |
|Microsoft. support/* |Destek biletleri oluşturun ve yönetin.|

### <a name="automation-runbook-operator"></a>Otomasyon Runbook Işleci

Runbook kapsamında bir Otomasyon Runbook Işleç rolü verilir. Bir Automation runbook Işleci, runbook 'un adını ve özelliklerini görüntüleyebilir.**Otomasyon Işi operatörü** rolüyle birlikte bu rol, işlecin runbook için iş oluşturmasını ve yönetmesini sağlar. Aşağıdaki tabloda rol için verilen izinler gösterilmektedir:

|**Eylemler**  |**Açıklama**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/runbook 'lar/okuma     | Runbook 'ları listeleyin.        |
|Microsoft. Authorization/*/Read      | Yetkilendirmeyi okuyun.        |
|Microsoft. resources/abonelikler/resourceGroups/Read      |Rolleri ve rol atamalarını okuyun.         |
|Microsoft. resources/dağıtımlar/*      | Kaynak grubu dağıtımlarını oluşturun ve yönetin.         |
|Microsoft. Insights/alertRules/*      | Uyarı kuralları oluşturun ve yönetin.        |
|Microsoft. support/*      | Destek biletleri oluşturun ve yönetin.        |

### <a name="log-analytics-contributor"></a>Log Analytics Katkıda Bulunan

Log Analytics katkıda bulunan, tüm izleme verilerini okuyabilir ve izleme ayarlarını düzenleyebilir. İzleme ayarlarını düzenlediğinizde VM 'lere VM uzantısının eklenmesi dahildir; Azure depolama 'dan günlüklerin toplanmasını yapılandırabilmek için depolama hesabı anahtarlarını okuma; Otomasyon hesapları oluşturma ve yapılandırma; özellikler ekleme; ve Azure tanılama 'yı tüm Azure kaynaklarında yapılandırma. Aşağıdaki tabloda rol için verilen izinler gösterilmektedir:

|**Eylemler**  |**Açıklama**  |
|---------|---------|
|*/Read|Gizli dizileri hariç tüm türlerin kaynaklarını okuyun.|
|Microsoft. Automation/automationAccounts/*|Otomasyon hesaplarını yönetin.|
|Microsoft. ClassicCompute/virtualMachines/Extensions/*|Sanal makine uzantıları oluşturun ve yönetin.|
|Microsoft. ClassicStorage/storageAccounts/listKeys/Action|Klasik depolama hesabı anahtarlarını listeleyin.|
|Microsoft. COMPUTE/virtualMachines/Extensions/*|Klasik sanal makine uzantıları oluşturun ve yönetin.|
|Microsoft. Insights/alertRules/*|Uyarı kurallarını okuma/yazma/silme.|
|Microsoft. Insights/diagnosticSettings/*|Tanılama ayarlarını okuma/yazma/silme.|
|Microsoft. Operationalınsights/*|Azure Izleyici günlüklerini yönetin.|
|Microsoft. OperationsManagement/*|Çalışma alanlarında Azure Otomasyonu özelliklerini yönetin.|
|Microsoft. resources/dağıtımlar/*|Kaynak grubu dağıtımlarını oluşturun ve yönetin.|
|Microsoft. resources/abonelikler/ResourceGroups/dağıtımlar/*|Kaynak grubu dağıtımlarını oluşturun ve yönetin.|
|Microsoft. Storage/storageAccounts/listKeys/Action|Depolama hesabı anahtarlarını listeleyin.|
|Microsoft. support/*|Destek biletleri oluşturun ve yönetin.|

### <a name="log-analytics-reader"></a>Log Analytics Okuyucusu

Log Analytics okuyucu tüm izleme verilerini görüntüleyip arayabilir ve tüm Azure kaynaklarında Azure tanılama 'nın yapılandırılmasını görüntüleme dahil olmak üzere izleme ayarlarını görüntüleyebilir. Aşağıdaki tabloda rol için verilen veya reddedilen izinler gösterilmektedir:

|**Eylemler**  |**Açıklama**  |
|---------|---------|
|*/Read|Gizli dizileri hariç tüm türlerin kaynaklarını okuyun.|
|Microsoft. Operationalınsights/çalışma alanları/analiz/sorgu/eylem|Azure Izleyici günlüklerinde sorguları yönetin.|
|Microsoft. Operationalınsights/çalışma alanları/arama/eylem|Azure Izleyici günlük verilerini arayın.|
|Microsoft. support/*|Destek biletleri oluşturun ve yönetin.|
|**Eylem değil**| |
|Microsoft. Operationalınsights/çalışma alanları/sharedKeys/Read|Paylaşılan erişim anahtarları okunamıyor.|

### <a name="monitoring-contributor"></a>Katkıda bulunan izleniyor

Bir Izleme katılımcısı, tüm izleme verilerini okuyabilir ve izleme ayarlarını güncelleştirebilir. Aşağıdaki tabloda rol için verilen izinler gösterilmektedir:

|**Eylemler**  |**Açıklama**  |
|---------|---------|
|*/Read|Gizli dizileri hariç tüm türlerin kaynaklarını okuyun.|
|Microsoft. AlertsManagement/uyarılar/*|Uyarıları yönetin.|
|Microsoft. AlertsManagement/alertsSummary/*|Uyarı panosunu yönetin.|
|Microsoft. Insights/AlertRules/*|Uyarı kurallarını yönetin.|
|Microsoft. Insights/bileşenler/*|Application Insights bileşenlerini yönetin.|
|Microsoft.Insights/DiagnosticSettings/*|Tanılama ayarlarını yönetin.|
|Microsoft. Insights/eventTypes/*|Bir abonelikteki etkinlik günlüğü olaylarını (yönetim olayları) listeleyin. Bu izin, etkinlik günlüğüne hem programlı hem de portala erişim için geçerlidir.|
|Microsoft. Insights/LogDefinitions/*|Bu izin, Portal aracılığıyla etkinlik günlüklerine erişmesi gereken kullanıcılar için gereklidir. Etkinlik günlüğündeki günlük kategorilerini listeleyin.|
|Microsoft. Insights/MetricDefinitions/*|Ölçüm tanımlarını oku (bir kaynak için kullanılabilen ölçüm türlerinin listesi).|
|Microsoft. Insights/ölçümler/*|Bir kaynak için ölçümleri okuyun.|
|Microsoft. Insights/Register/ACTION|Microsoft. Insights sağlayıcısını kaydedin.|
|Microsoft. Insights/webtests/*|Application Insights Web testlerini yönetin.|
|Microsoft. Operationalınsights/Workspaces/ıntelligencepacks/*|Azure Izleyici günlükleri çözüm paketlerini yönetin.|
|Microsoft. Operationalınsights/çalışma alanları/Savedaramalar/*|Azure Izleyici günlükleri kaydedilmiş aramalarını yönetme.|
|Microsoft. Operationalınsights/çalışma alanları/arama/eylem|Log Analytics çalışma alanlarını ara.|
|Microsoft. Operationalınsights/Workspaces/sharedKeys/Action|Log Analytics çalışma alanı için anahtarları listeleyin.|
|Microsoft. Operationalınsights/Workspaces/storageınsiizconfigs/*|Azure Izleyici günlük depolama öngörülerini yönetin.|
|Microsoft. support/*|Destek biletleri oluşturun ve yönetin.|
|Microsoft. WorkloadMonitor/iş yükleri/*|Iş yüklerini yönetin.|

### <a name="monitoring-reader"></a>İzleme okuyucusu

Izleme okuyucusu tüm izleme verilerini okuyabilir. Aşağıdaki tabloda rol için verilen izinler gösterilmektedir:

|**Eylemler**  |**Açıklama**  |
|---------|---------|
|*/Read|Gizli dizileri hariç tüm türlerin kaynaklarını okuyun.|
|Microsoft. Operationalınsights/çalışma alanları/arama/eylem|Log Analytics çalışma alanlarını ara.|
|Microsoft. support/*|Destek biletleri oluşturma ve yönetme|

### <a name="user-access-administrator"></a>Kullanıcı Erişimi Yöneticisi

Kullanıcı erişimi Yöneticisi, Azure kaynaklarına Kullanıcı erişimini yönetebilir. Aşağıdaki tabloda rol için verilen izinler gösterilmektedir:

|**Eylemler**  |**Açıklama**  |
|---------|---------|
|*/Read|Tüm kaynakları okuyun|
|Microsoft. Authorization/*|Yetkilendirmeyi Yönetme|
|Microsoft. support/*|Destek biletleri oluşturma ve yönetme|

## <a name="feature-setup-permissions"></a>Özellik kurulumu izinleri

Aşağıdaki bölümlerde, Güncelleştirme Yönetimi ve Değişiklik İzleme ve envanter özelliklerinin etkinleştirilmesi için gereken en düşük izinler açıklanır.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>Bir VM 'den Güncelleştirme Yönetimi ve Değişiklik İzleme ve envanteri etkinleştirme izinleri

|**Eylem**  |**İzin**  |**En düşük kapsam**  |
|---------|---------|---------|
|Yeni dağıtım yaz      | Microsoft. resources/dağıtımlar/*          |Abonelik          |
|Yeni kaynak grubu yaz      | Microsoft. resources/abonelikler/resourceGroups/Write        | Abonelik          |
|Yeni varsayılan çalışma alanı oluştur      | Microsoft. Operationalınsights/çalışma alanları/yazma         | Kaynak grubu         |
|Yeni hesap oluştur      |  Microsoft. Automation/automationAccounts/Write        |Kaynak grubu         |
|Çalışma alanını ve hesabı bağla      |Microsoft. Operationalınsights/çalışma alanları/yazma</br>Microsoft. Automation/automationAccounts/Read|Çalışma alanı</br>Otomasyon hesabı
|MMA uzantısı oluştur      | Microsoft. COMPUTE/virtualMachines/Write         | Sanal Makine         |
|Kayıtlı arama oluştur      | Microsoft. Operationalınsights/çalışma alanları/yazma          | Çalışma alanı         |
|Kapsam yapılandırması oluştur      | Microsoft. Operationalınsights/çalışma alanları/yazma          | Çalışma alanı         |
|Ekleme durumu denetimi-okuma çalışma alanı      | Microsoft. Operationalınsights/çalışma alanları/okuma         | Çalışma alanı         |
|Ekleme durumu denetimi-hesabın bağlantılı çalışma alanı özelliğini oku     | Microsoft. Automation/automationAccounts/Read      | Otomasyon hesabı        |
|Ekleme durumu denetimi-çözümü oku      | Microsoft. Operationalınsights/Workspaces/ıntelligencepacks/okuma          | Çözüm         |
|Ekleme durumu denetimi-VM okuma      | Microsoft. COMPUTE/virtualMachines/okuma         | Sanal Makine         |
|Ekleme durumu denetimi-oku hesabı      | Microsoft. Automation/automationAccounts/Read  |  Otomasyon hesabı   |
| VM<sup>1</sup> için çalışma alanı denetimini ekleme       | Microsoft. Operationalınsights/çalışma alanları/okuma         | Abonelik         |
| Log Analytics sağlayıcıyı kaydetme |Microsoft. Insights/Register/ACTION | Abonelik|

<sup>1</sup> bu ızın, VM portalı deneyimi aracılığıyla özellikleri etkinleştirmek için gereklidir.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Otomasyon hesabından Güncelleştirme Yönetimi ve Değişiklik İzleme ve envanteri etkinleştirme izinleri

|**Eylem**  |**İzin** |**En düşük kapsam**  |
|---------|---------|---------|
|Yeni dağıtım oluştur     | Microsoft. resources/dağıtımlar/*        | Abonelik         |
|Yeni kaynak grubu oluştur     | Microsoft. resources/abonelikler/resourceGroups/Write         | Abonelik        |
|Automationekleme dikey penceresi-yeni çalışma alanı oluştur     |Microsoft. Operationalınsights/çalışma alanları/yazma           | Kaynak grubu        |
|Automationekleme dikey penceresi-bağlantılı çalışma alanını oku     | Microsoft. Automation/automationAccounts/Read        | Otomasyon hesabı       |
|Automationekleme dikey penceresi-çözümü oku     | Microsoft. Operationalınsights/Workspaces/ıntelligencepacks/okuma         | Çözüm        |
|Automationekleme dikey penceresi-çalışma alanını oku     | Microsoft. Operationalınsights/Workspaces/ıntelligencepacks/okuma        | Çalışma alanı        |
|Çalışma alanı ve hesap için bağlantı oluştur     | Microsoft. Operationalınsights/çalışma alanları/yazma        | Çalışma alanı        |
|ShoeBox için yazma hesabı      | Microsoft. Automation/automationAccounts/Write        | Hesap        |
|Kayıtlı aramayı Oluştur/Düzenle     | Microsoft. Operationalınsights/çalışma alanları/yazma        | Çalışma alanı        |
|Kapsam yapılandırması oluştur/Düzenle     | Microsoft. Operationalınsights/çalışma alanları/yazma        | Çalışma alanı        |
| Log Analytics sağlayıcıyı kaydetme |Microsoft. Insights/Register/ACTION | Abonelik|
|**2. adım-birden çok VM 'yi etkinleştirme**     |         |         |
|Vmontaslak dikey penceresi-MMA uzantısı oluşturma     | Microsoft. COMPUTE/virtualMachines/Write           | Sanal Makine        |
|Kayıtlı aramayı Oluştur/Düzenle     | Microsoft. Operationalınsights/çalışma alanları/yazma           | Çalışma alanı        |
|Kapsam yapılandırması oluştur/Düzenle  | Microsoft. Operationalınsights/çalışma alanları/yazma   | Çalışma alanı|

## <a name="update-management-permissions"></a>Güncelleştirme yönetimi izinleri

Güncelleştirme yönetimi, hizmeti sağlamak için birden çok hizmete ulaşır. Aşağıdaki tabloda, güncelleştirme yönetimi dağıtımlarını yönetmek için gereken izinler gösterilmektedir:

|**Kaynak**  |**Role**  |**Kapsam**  |
|---------|---------|---------|
|Otomasyon hesabı     | Log Analytics Katkıda Bulunan       | Otomasyon hesabı        |
|Otomasyon hesabı    | Sanal Makine Katılımcısı        | Hesabın kaynak grubu        |
|Log Analytics çalışma alanı     | Log Analytics Katkıda Bulunan| Log Analytics çalışma alanı        |
|Log Analytics çalışma alanı |Log Analytics Okuyucusu| Abonelik|
|Çözüm     |Log Analytics Katkıda Bulunan         | Çözüm|
|Sanal Makine     | Sanal Makine Katılımcısı        | Sanal Makine        |

## <a name="configure-azure-rbac-for-your-automation-account"></a>Otomasyon hesabınız için Azure RBAC 'yi yapılandırma

Aşağıdaki bölümde, Otomasyon hesabınızda [Azure Portal](#configure-azure-rbac-using-the-azure-portal) ve [POWERSHELL](#configure-azure-rbac-using-powershell)aracılığıyla Azure RBAC 'nin nasıl yapılandırılacağı gösterilmektedir.

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Azure portal kullanarak Azure RBAC 'yi yapılandırma

1. [Azure portalında](https://portal.azure.com/) oturum açın Otomasyon Hesapları sayfasından Otomasyon hesabınızı açın.
2. Erişim denetimi (IAM) sayfasını açmak için **Access Control (IAM)** seçeneğine tıklayın. Otomasyon hesabınızı yönetmek ve Otomasyon hesabı için yapılandırılabilen mevcut rolleri görüntülemek için yeni kullanıcılar, gruplar ve uygulamalar eklemek üzere bu sayfayı kullanabilirsiniz.
3. **Rol atamaları** sekmesine tıklayın.

   ![Erişim düğmesi](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Yeni kullanıcı ekleme ve rol atama

1. Erişim denetimi (ıAM) sayfasında **+ rol ataması Ekle**' ye tıklayın. Bu eylem, bir Kullanıcı, Grup veya uygulama ekleyebileceğiniz ve ilgili bir rol atayabileceğiniz rol ataması Ekle sayfasını açar.

2. Kullanılabilir roller listesinden bir rol seçin. Otomasyon hesabının desteklediği kullanılabilir yerleşik rolleri veya tanımladığınız herhangi bir özel rolü seçebilirsiniz.

3. **Seçim** alanında izinleri vermek istediğiniz kullanıcının adını yazın. Listeden kullanıcıyı seçin ve **Kaydet**' e tıklayın.

   ![Kullanıcı ekle](media/automation-role-based-access-control/automation-04-add-users.png)

   Şimdi, seçilen rolün atandığı kullanıcılar sayfasına eklenen kullanıcıyı görmeniz gerekir.

   ![Kullanıcıları listele](media/automation-role-based-access-control/automation-05-list-users.png)

   Kullanıcıya Roller sayfasından rol atayabilirsiniz.

4. Roller sayfasını açmak için erişim denetimi (ıAM) sayfasındaki **Roller** ' e tıklayın. Rolün adını ve bu role atanan kullanıcı ve grup sayısını görüntüleyebilirsiniz.

    ![Kullanıcılar sayfasından rol atama](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Yalnızca Otomasyon hesabı kapsamında rol tabanlı erişim denetimi ayarlayabilirsiniz; Otomasyon hesabının altındaki herhangi bir kaynakta değil.

#### <a name="remove-a-user"></a>Kullanıcıyı kaldırma

Otomasyon hesabını yönetmesiz veya kuruluş için artık çalışan bir kullanıcının erişim iznini kaldırabilirsiniz. Aşağıdaki adımları kullanarak kullanıcı kaldırabilirsiniz:

1. Erişim denetimi (ıAM) sayfasında, kaldırılacak kullanıcıyı seçip **Kaldır**' a tıklayın.
2. Atama ayrıntıları sayfasında **Kaldır** düğmesine tıklayın.
3. Kaldırmayı onaylamak için **Evet**'e tıklayın.

   ![Kullanıcıları kaldır](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>PowerShell kullanarak Azure RBAC yapılandırma

Ayrıca, aşağıdaki [Azure PowerShell cmdlet 'lerini](../role-based-access-control/role-assignments-powershell.md)kullanarak bir Otomasyon hesabına rol tabanlı erişimi de yapılandırabilirsiniz:

[Get-AzRoleDefinition](/powershell/module/Az.Resources/Get-AzRoleDefinition) , Azure Active Directory bulunan tüm Azure rollerini listeler. `Name`Belirli bir rolün gerçekleştirebileceği tüm eylemleri listelemek için bu cmdlet 'i parametresiyle birlikte kullanabilirsiniz.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

Örnek çıktı aşağıda verilmiştir:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment) , Azure rol atamalarını belirtilen kapsamda listeler. Hiçbir parametre olmadan bu cmdlet, abonelik altında yapılan tüm rol atamalarını döndürür. `ExpandPrincipalGroups`Belirtilen kullanıcı için erişim atamalarını ve kullanıcının ait olduğu grupları listelemek için parametresini kullanın.

**Örnek:** Bir Otomasyon hesabı içindeki tüm kullanıcıları ve rollerinin listesini listelemek için aşağıdaki cmdlet 'i kullanın.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Örnek çıktı aşağıda verilmiştir:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

Kullanıcılara, gruplara ve uygulamalara belirli bir kapsama erişim atamak için [New-Azroleatama](/powershell/module/Az.Resources/New-AzRoleAssignment) kullanın.

**Örnek:** Otomasyon hesabı kapsamındaki bir kullanıcı için "Otomasyon Işleci" rolünü atamak için aşağıdaki komutu kullanın.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Örnek çıktı aşağıda verilmiştir:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Belirli bir kapsamdaki belirtilen kullanıcı, Grup veya uygulamanın erişimini kaldırmak için [Remove-Azroleatama](/powershell/module/Az.Resources/Remove-AzRoleAssignment) komutunu kullanın.

**Örnek:** Kullanıcıyı Otomasyon hesabı kapsamındaki otomasyon operatörü rolünden kaldırmak için aşağıdaki komutu kullanın.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Yukarıdaki örnekte,,, `sign-in ID of a user you wish to remove` ve ' `SubscriptionID` yi `Resource Group Name` `Automation account name` hesap ayrıntılarınız ile değiştirin. Kullanıcı rolü atamalarını kaldırmaya devam etmeden önce onaylamanız istendiğinde **Evet** ' i seçin.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Otomasyon Işletmeni rolü için Kullanıcı deneyimi-Otomasyon hesabı

Otomasyon hesabı kapsamındaki otomasyon operatörü rolüne atanan bir Kullanıcı, kendisi/kişinin atandığı Otomasyon hesabını görüntülediğinde, Kullanıcı yalnızca runbook 'ların, runbook işlerinin ve otomasyon hesabında oluşturulan zamanlamaların listesini görüntüleyebilir. Bu Kullanıcı bu öğelerin tanımlarını görüntüleyemez. Kullanıcı runbook işini başlatabilir, durdurabilir, askıya alabilir, sürdürebilir veya zamanlayabilir. Ancak, kullanıcının yapılandırma, karma çalışan grupları veya DSC düğümleri gibi diğer otomasyon kaynaklarına erişimi yoktur.

![Kaynaklara erişim yok](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Runbook 'lar için Azure RBAC 'yi yapılandırma

Azure Otomasyonu, belirli runbook 'lara Azure rolleri atamanıza olanak tanır. Bunu yapmak için, belirli bir runbook 'a bir kullanıcı eklemek üzere aşağıdaki betiği çalıştırın. Otomasyon hesabı Yöneticisi veya Kiracı Yöneticisi bu betiği çalıştırabilir.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Betik çalıştırıldıktan sonra, kullanıcının Azure portal oturum açmasını ve **tüm kaynakları** seçmesini sağlayabilirsiniz. Listede, Kullanıcı kendisi için bir Otomasyon Runbook Işleci olarak eklenmiş runbook 'u görebilir.

![Portalda runbook Azure RBAC](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Otomasyon işletmeni rolü için Kullanıcı deneyimi-runbook

Runbook kapsamındaki otomasyon operatörü rolüne atanan bir kullanıcı atanmış bir runbook 'u görüntülediğinde, Kullanıcı yalnızca runbook 'u başlatabilir ve Runbook işlerini görüntüleyebilir.

![Yalnızca başlangıç erişimi vardır](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Sonraki adımlar

* PowerShell kullanarak Azure RBAC hakkında daha fazla bilgi edinmek için bkz. [Azure PowerShell kullanarak Azure rol atamaları ekleme veya kaldırma](../role-based-access-control/role-assignments-powershell.md).
* Runbook 'ların türleri hakkında daha fazla bilgi için bkz. [Azure Automation runbook türleri](automation-runbook-types.md).
* Bir runbook 'u başlatmak için bkz. [Azure Otomasyonu 'nda runbook başlatma](start-runbooks.md).
