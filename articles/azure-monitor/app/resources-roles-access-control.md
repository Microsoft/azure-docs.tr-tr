---
title: Azure Application Insights kaynaklar, roller ve erişim denetimi | Microsoft Docs
description: Kuruluşunuzun öngörülerinin sahipleri, katkıda bulunanlar ve okuyucular.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4254170ad100b634439cc846dc381e305ae71d1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589605"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Application Insights içindeki kaynaklar, roller ve erişim denetimi

Azure [rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)kullanarak Azure [Application Insights][start]verilerinize okuma ve güncelleştirme erişimi olduğunu kontrol edebilirsiniz.

> [!IMPORTANT]
> **Kaynak grubunda veya** uygulama kaynağınızın ait olduğu abonelikteki kullanıcılara erişim atayın. **Application Insights bileşeni katkıda bulunan** rolünü atayın. Bu, uygulama kaynağınızın yanı sıra Web testlerine ve uyarılara erişimin tek düzen denetimini sağlar. [Daha fazla bilgi edinin](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Kaynaklar, gruplar ve abonelikler

İlki, bazı tanımlar:

* **Kaynak** -Microsoft Azure bir hizmetin örneği. Application Insights kaynağınız, uygulamanızdan gönderilen telemetri verilerini toplar, analiz eder ve görüntüler.  Diğer Azure kaynakları türleri arasında Web uygulamaları, veritabanları ve sanal makineler bulunur.
  
    Kaynaklarınızı görmek için [Azure Portal][portal]açın, oturum açın ve tüm kaynaklar ' a tıklayın. Bir kaynağı bulmak için, filtre alanına adının bir kısmını yazın.
  
    ![Azure kaynakları listesi](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Kaynak grubu**][group] -her kaynak bir gruba aittir. Grup, özellikle Access Control için ilgili kaynakları yönetmenin kolay bir yoludur. Örneğin, tek bir kaynak grubunda, bir Web uygulaması, uygulamayı izlemek için bir Application Insights kaynağı ve dışarıya aktarılmış verileri tutmak için bir depolama kaynağı koyabilirsiniz.

* [**Abonelik**](https://portal.azure.com) -Application Insights veya diğer Azure kaynaklarını kullanmak Için bir Azure aboneliğinde oturum açın. Her kaynak grubu, Fiyat paketinizi seçtiğiniz bir Azure aboneliğine aittir ve bir kuruluş aboneliği ise, üyeleri ve bunların erişim izinlerini seçin.
* [**Microsoft hesabı**][account] -Microsoft Azure aboneliklerde, Xbox Live, Outlook.com ve diğer Microsoft hizmetlerinde oturum açmak için kullandığınız Kullanıcı adı ve parola.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a> Kaynak grubunda erişimi denetleme

Uygulamanız için oluşturduğunuz kaynağa ek olarak, uyarılar ve Web testleri için ayrı gizli kaynaklar da olduğunu anlamak önemlidir. Bunlar, Application Insights kaynağınız ile aynı [kaynak grubuna](#resource-group) iliştirilir. Ayrıca, Web siteleri veya depolama gibi diğer Azure hizmetlerini de yerleştirebilirsiniz.

## <a name="to-provide-access-to-another-user"></a>Başka bir kullanıcıya erişim sağlamak için

Abonelik veya kaynak grubu için sahip haklarınızın olması gerekir.

Kullanıcının bir [Microsoft hesabına][account]sahip olması veya [Kurumsal Microsoft hesabına](../../active-directory/fundamentals/sign-up-organization.md)erişimi olması gerekir. Kişilere ve ayrıca Azure Active Directory tanımlı kullanıcı gruplarına erişim sağlayabilirsiniz.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Kaynak grubuna veya doğrudan kaynağın kendine gitme

Sol taraftaki menüden **erişim denetimi (IAM)** seçeneğini belirleyin.

![Azure portal erişim denetimi düğmesinin ekran görüntüsü](./media/resources-roles-access-control/0001-access-control.png)

**Rol ataması Ekle** ' yi seçin

![Ekle düğmesi vurgulanmış şekilde, erişim denetimi menüsünün kırmızı renkte ekran görüntüsü](./media/resources-roles-access-control/0002-add.png)

Aşağıdaki **Izin ekleme** görünümü öncelikle Application Insights kaynaklara özeldir, erişim denetimi izinlerini kaynak grupları gibi daha yüksek bir düzeyden görüntülüyorsanız, Application Insights daha fazla merkezli rol görürsünüz.

Tüm Azure rol tabanlı erişim denetimi ile ilgili bilgileri görüntülemek için yerleşik roller [resmi başvuru içeriğini](../../role-based-access-control/built-in-roles.md)kullanır.

![Erişim denetimi kullanıcı rolü listesinin ekran görüntüsü](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Rol seçin

Uygun olduğunda, ilişkili resmi başvuru belgelerine bağlantı veriyoruz.

| Rol | Kaynak grubunda |
| --- | --- |
| [Sahibi](../../role-based-access-control/built-in-roles.md#owner) |Kullanıcı erişimi de dahil olmak üzere herhangi bir şeyi değiştirebilir. |
| [Katkıda Bulunan](../../role-based-access-control/built-in-roles.md#contributor) |Tüm kaynaklar dahil olmak üzere herhangi bir şeyi düzenleyebilir. |
| [Application Insights bileşeni Katılımcısı](../../role-based-access-control/built-in-roles.md#application-insights-component-contributor) |Application Insights kaynaklarını düzenleyebilir. |
| [Okuyucu](../../role-based-access-control/built-in-roles.md#reader) |, Herhangi bir şeyi görüntüleyebilir ancak değiştiremez. |
| [Application Insights Snapshot Debugger](../../role-based-access-control/built-in-roles.md#application-insights-snapshot-debugger) | Kullanıcıya Application Insights Snapshot Debugger özelliklerini kullanma izni verir. Bu rolün sahip veya katkıda bulunan rollerinin ne olduğunu unutmayın. |
| Azure hizmet dağıtımı Release Management Katılımcısı | Azure hizmet dağıtımı aracılığıyla hizmet dağıtımı için katkıda bulunan rolü. |
| [Veri Takiger](../../role-based-access-control/built-in-roles.md#data-purger) | Kişisel verileri temizlemek için özel rol. Daha fazla bilgi için [kişisel verilere yönelik kılavuzumuza](../logs/personal-data-mgmt.md) bakın.   |
| ExpressRoute Yöneticisi | Hızlı yollar silme ve yönetme oluşturabilir.|
| [Log Analytics Katkıda Bulunan](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) | Log Analytics katkı, tüm izleme verilerini okuyabilir ve izleme ayarlarını düzenleyebilir. İzleme ayarlarını düzenlediğinizde VM 'lere VM uzantısının eklenmesi dahildir; Azure depolama 'dan günlüklerin toplanmasını yapılandırabilmek için depolama hesabı anahtarlarını okuma; Otomasyon hesapları oluşturma ve yapılandırma; çözümler ekleme; ve Azure tanılama 'yı tüm Azure kaynaklarında yapılandırma.  |
| [Log Analytics Okuyucusu](../../role-based-access-control/built-in-roles.md#log-analytics-reader) | Log Analytics okuyucu tüm izleme verilerini görüntüleyip arayabilir ve tüm Azure kaynaklarında Azure tanılama 'nın yapılandırılmasını görüntüleme dahil olmak üzere izleme ayarlarını görüntüleyebilir. |
| masterreader | Bir kullanıcının her şeyi görüntülemesine izin verir, ancak değişiklik yapamaz. |
| [Katkıda bulunan izleniyor](../../role-based-access-control/built-in-roles.md#monitoring-contributor) | Tüm izleme verilerini okuyabilir ve izleme ayarlarını güncelleştirebilir.|
| [Ölçüm yayımcısını izleme](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher) | Azure kaynaklarında ölçüm yayımlamaya izin vermez. |
| [İzleme okuyucusu](../../role-based-access-control/built-in-roles.md#monitoring-reader) | Tüm izleme verilerini okuyabilir. |
| Kaynak Ilkesi katılımcısı (Önizleme) | Kaynak ilkesi oluşturma/değiştirme, destek bileti oluşturma ve kaynak/hiyerarşiyi okuma haklarıyla, EA 'dan Kullanıcı geri alma.  |
| [Kullanıcı Erişimi Yöneticisi](../../role-based-access-control/built-in-roles.md#user-access-administrator) | Bir kullanıcının, diğer kullanıcılar için Azure kaynaklarına erişimi yönetmesine izin verir.|
| [Web sitesi Katılımcısı](../../role-based-access-control/built-in-roles.md#website-contributor) | Web sitelerini yönetmenizi sağlar, ancak bunlara erişemez.|

' Editing ' oluşturma, silme ve güncelleştirme içerir:

* Kaynaklar
* Web testleri
* Uyarılar
* Sürekli dışarı aktarma

#### <a name="select-the-user"></a>Kullanıcıyı seçin

İstediğiniz kullanıcı dizinde değilse, Microsoft hesabı herkes davet edebilirsiniz.
(Outlook.com, OneDrive, Windows Phone veya XBox Live gibi hizmetler kullanıyorsa, Microsoft hesabı vardır.)

## <a name="related-content"></a>İlgili içerik

* [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Rol üyeliğini belirleyecek PowerShell sorgusu

Belirli roller bildirimlere ve e-posta uyarılarına bağlanabileceğinden, belirli bir role ait olan kullanıcıların listesini oluşturmak yararlı olabilir. Bu liste türlerini oluşturmaya yardımcı olmak için, özel gereksinimlerinize uyacak şekilde ayarlanabilecek aşağıdaki örnek sorguları sunuyoruz:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Yönetici rolleri + katkıda bulunan rolleri için tüm aboneliği sorgula

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Sahipler ve katkıda bulunanlar için belirli bir Application Insights kaynağı bağlamı içinde sorgulama

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Sahipler ve katkıda bulunanlar için belirli bir kaynak grubunun bağlamı içinde sorgulama

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md
