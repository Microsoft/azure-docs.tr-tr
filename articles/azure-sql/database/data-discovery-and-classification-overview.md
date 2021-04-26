---
title: Veri Bulma ve Sınıflandırma
description: Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics için veri bulma & sınıflandırması
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
tags: azure-synapse
ms.openlocfilehash: f47303a953dbb06b4c9efdfbbae41715cad81d2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690813"
---
# <a name="data-discovery--classification"></a>Veri Bulma ve Sınıflandırma
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Veri Bulma ve Sınıflandırma; Azure SQL Veritabanı, Azure SQL Yönetilen Örneği ve Azure Synapse Analytics hizmetlerinde yerleşik olarak bulunur. Veritabanınızdaki hassas verileri bulmak, sınıflandırmak, etiketlemek ve raporlamak için temel yetenekler sağlar.

En hassas verileriniz arasında işletme, finans, sağlık hizmetleri veya kişisel bilgiler bulunabilir. Bu verileri bulmak ve sınıflandırmak, kuruluşunuzun bilgi koruma yaklaşımında bir özetleme rolü oynayabilir. Şunlara altyapı sağlayabilir:

- Veri gizliliği ve yasal uyumluluk gereksinimleri için standartları karşılamanıza yardımcı olma.
- Hassas verilere yönelik izleme (denetim) gibi çeşitli güvenlik senaryoları.
- Son derece hassas veriler içeren veritabanlarının güvenliğine erişimi denetleme ve güvenliği artırma.

> [!NOTE]
> Şirket içi SQL Server hakkında daha fazla bilgi için bkz. [SQL veri bulma & sınıflandırması](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Veri bulma & sınıflandırması nedir?

Veri bulma & sınıflandırması, Azure 'daki bir dizi temel hizmeti ve yeni özellikleri tanıtır. Yalnızca veritabanını değil, verileri korumayı hedefleyen SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE için yeni bir bilgi koruma paradigması oluşturur. Paradigma şunları içerir:

- **Bulma ve öneriler:** Sınıflandırma Altyapısı, veritabanınızı tarar ve potansiyel olarak hassas veriler içeren sütunları tanımlar. Daha sonra, Azure portal aracılığıyla önerilen sınıflandırmayı gözden geçirmek ve uygulamak için kolay bir yol sağlar.

- **Etiketleme:** SQL Server veritabanı altyapısına eklenmiş olan yeni meta veri özniteliklerini kullanarak, duyarlılık sınıflandırma etiketlerini kalıcı olarak sütunlara uygulayabilirsiniz. Bu meta veriler, daha sonra duyarlılık tabanlı denetim ve koruma senaryoları için kullanılabilir.

- **Sorgu sonucu-ayarlanan duyarlılık:** Sorgu sonuç kümesinin duyarlılığı, denetim amaçlarıyla gerçek zamanlı olarak hesaplanır.

- **Görünürlük:** Veritabanı sınıflandırması durumunu Azure portal ayrıntılı bir panoda görüntüleyebilirsiniz. Ayrıca, uyumluluk ve denetim amaçlarıyla ve diğer gereksinimlerde kullanmak üzere bir raporu Excel biçiminde indirebilirsiniz.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Hassas sütunları bulma, sınıflandırma ve etiketleme

Bu bölümde, aşağıdaki adımlar açıklanmaktadır:

- Veritabanınızdaki hassas verileri içeren sütunları keşfetme, sınıflandırma ve etiketleme.
- Veritabanınızın geçerli sınıflandırma durumunu görüntüleme ve raporları dışarı aktarma.

Sınıflandırma iki meta veri özniteliği içerir:

- **Etiketler**: sütunda depolanan verilerin duyarlılık düzeyini tanımlamak için kullanılan ana sınıflandırma öznitelikleri.  
- **Bilgi türleri**: sütunda depolanan verilerin türü hakkında daha ayrıntılı bilgiler sağlayan öznitelikler.

### <a name="define-and-customize-your-classification-taxonomy"></a>Sınıflandırma taksonominizi tanımlama ve özelleştirme

Veri bulma & sınıflandırması, yerleşik bir duyarlılık etiketleri kümesi ve yerleşik bir bilgi türleri ve bulma mantığı kümesiyle gelir. Artık bu taksonomiyi özelleştirebilir ve özel olarak kendi ortamınız için sınıflandırma yapıları derecelendirme kümesi oluşturabilirsiniz.

Sınıflandırma taksonominizi Azure kuruluşunuzun tamamı için tek bir yerde tanımlayabilir ve özelleştirebilirsiniz. Bu konum, güvenlik ilkenizin bir parçası olarak [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md)'nde bulunur. Yalnızca kuruluşun kök yönetim grubunda yönetici haklarına sahip bir kişi bu görevi gerçekleştirebilir.

Bilgi koruması için ilke yönetiminin bir parçası olarak, Özel Etiketler tanımlayabilir, bunları derecelendirip bunları seçili bir bilgi türleri kümesiyle ilişkilendirebilirsiniz. Ayrıca, kendi özel bilgi türlerinizi ekleyebilir ve bunları dize desenleriyle yapılandırabilirsiniz. Desenler, veritabanlarınızdaki bu tür verileri tanımlamak için bulma mantığına eklenir.

Daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'NDE SQL Information Protection Ilkesini özelleştirme (Önizleme)](../../security-center/security-center-info-protection-policy.md).

Kuruluş genelinde ilke tanımlandıktan sonra, özelleştirilmiş ilkenizi kullanarak ayrı veritabanlarını sınıflandırarak devam edebilirsiniz.

### <a name="classify-your-database"></a>Veritabanınızı sınıflandır

> [!NOTE]
> Aşağıdaki örnek Azure SQL veritabanı 'nı kullanır, ancak veri bulma & sınıflandırmasını yapılandırmak istediğiniz uygun ürünü seçmeniz gerekir.

1. [Azure Portal](https://portal.azure.com) gidin.

1. Azure SQL veritabanı bölmesinizdeki **güvenlik** başlığı altında bulunan **veri bulma & sınıflandırmasına** gidin. Genel Bakış sekmesi, veritabanının geçerli sınıflandırma durumunun bir özetini içerir. Özet, yalnızca belirli şema parçalarını, bilgi türlerini ve etiketleri göstermek üzere filtreleyebileceğiniz tüm sınıflandırılan sütunların ayrıntılı bir listesini içerir. Herhangi bir sütunu henüz sınıflandırılmamış [4. adıma atlayın](#step-4).

    ![Genel Bakış](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. Excel biçiminde bir rapor indirmek için bölmenin üst menüsünde **dışarı aktar** ' ı seçin.

1. <a id="step-4"></a>Verilerinizi **sınıflandırmayla** başlamak Için **veri bulma & sınıflandırma** sayfasında sınıflandırma sekmesini seçin.

    Sınıflandırma Altyapısı, veritabanınızı potansiyel olarak hassas veriler içeren sütunlar için tarar ve önerilen sütun sınıflandırmalarının bir listesini sağlar.

1. Sınıflandırma önerilerini görüntüleyin ve uygulayın:

   - Önerilen sütun sınıflandırmalarının listesini görüntülemek için bölmenin alt kısmındaki öneriler panelini seçin.

   - Belirli bir sütunun önerisini kabul etmek için ilgili satırın sol sütunundaki onay kutusunu seçin. Tüm önerileri kabul edildi olarak işaretlemek için, öneriler tablosu üstbilgisindeki en soldaki onay kutusunu seçin.

   - Seçili önerileri uygulamak için **Seçili önerileri kabul et**' i seçin.

   ![Sınıflandırma için öneriler](./media/data-discovery-and-classification-overview/recommendation.png)

1. Sütunları, alternatif olarak veya öneri tabanlı sınıflandırmanın yanı sıra el ile de sınıflandırabilirsiniz:

   1. Bölmenin üst menüsünde **Sınıflandırma Ekle** ' yi seçin.

   1. Açılan bağlam penceresinde, sınıflandırmak istediğiniz şemayı, tabloyu ve sütunu ve bilgi türünü ve duyarlılık etiketini seçin.

   1. Bağlam penceresinin alt kısmında **Sınıflandırma Ekle** ' yi seçin.

   ![Sınıflandırmayı el ile Ekle](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. Sınıflandırmanızı ve kalıcı olarak etiketlemesini (etiketleyerek) Yeni sınıflandırma meta verileri ile veritabanı sütunlarını, **Sınıflandırma** sayfasında **Kaydet** ' i seçin.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Gizli verilere erişimi denetleme

Bilgi koruma paradigmasının önemli bir yönü, hassas verilere erişimi izleme olanağıdır. [Azure SQL denetimi](../../azure-sql/database/auditing-overview.md) , adlı denetim günlüğüne yeni bir alan içerecek şekilde geliştirilmiştir `data_sensitivity_information` . Bu alan, bir sorgu tarafından döndürülen verilerin duyarlılık sınıflandırmalarını (etiketleri) günlüğe kaydeder. Aşağıda bir örnek verilmiştir:

![Denetim günlüğü](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>İzinler

Bu yerleşik roller bir veritabanının veri sınıflandırmasını okuyabilir:

- Sahip
- Okuyucu
- Katılımcı
- SQL Güvenlik Yöneticisi
- Kullanıcı Erişimi Yöneticisi

Bu yerleşik roller bir veritabanının veri sınıflandırmasını değiştirebilir:

- Sahip
- Katılımcı
- SQL Güvenlik Yöneticisi

[Azure RBAC](../../role-based-access-control/overview.md)'de rol tabanlı izinler hakkında daha fazla bilgi edinin.

## <a name="manage-classifications"></a><a id="manage-classification"></a>Sınıflandırmaları yönetme

Sınıflandırmaları yönetmek için T-SQL, REST API veya PowerShell kullanabilirsiniz.

### <a name="use-t-sql"></a>T-SQL kullanma

T-SQL ' i sütun sınıflandırmaları eklemek veya kaldırmak ve tüm veritabanının sınıflandırmalarını almak için kullanabilirsiniz.

> [!NOTE]
> Etiketleri yönetmek için T-SQL kullandığınızda, bir sütuna eklediğiniz etiketlerin, kuruluşun bilgi koruma ilkesinde (portal önerilerinde görünen etiketler kümesi) var olduğunu belirten bir doğrulama yoktur. Bu nedenle, bunu doğrulamak sizin için en iyisidir.

Sınıflandırmalar için T-SQL kullanma hakkında daha fazla bilgi için aşağıdaki başvurulara bakın:

- Bir veya daha fazla sütunun sınıflandırmasını eklemek veya güncelleştirmek için: [DUYARLıLıK sınıflandırması Ekle](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Sınıflandırmayı bir veya daha fazla sütundan kaldırmak için: [DÜŞÜRÜLME DUYARLıLıK sınıflandırması](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Veritabanındaki tüm sınıflandırmaları görüntülemek için: [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>PowerShell cmdlet'leri kullanma
PowerShell kullanarak Azure SQL veritabanı ve Azure SQL yönetilen örneği için sınıflandırmaları ve önerileri yönetin.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>Azure SQL veritabanı için PowerShell cmdlet 'leri

- [Get-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği için PowerShell cmdlet 'leri

- [Get-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>REST API 'YI kullanma

Sınıflandırmaları ve önerileri programlı bir şekilde yönetmek için REST API kullanabilirsiniz. Yayımlanan REST API aşağıdaki işlemleri destekler:

- [Oluştur veya Güncelleştir](/rest/api/sql/sensitivitylabels/createorupdate): belirtilen sütunun duyarlılık etiketini oluşturur veya güncelleştirir.
- [Sil](/rest/api/sql/sensitivitylabels/delete): belirtilen sütunun duyarlılık etiketini siler.
- [Öneriyi devre dışı bırak](/rest/api/sql/sensitivitylabels/disablerecommendation): belirtilen sütunda duyarlılık önerilerini devre dışı bırakır.
- [Öneriyi etkinleştir](/rest/api/sql/sensitivitylabels/enablerecommendation): belirtilen sütunda duyarlılık önerilerini etkinleştirir. (Öneriler varsayılan olarak tüm sütunlarda etkindir.)
- [Get](/rest/api/sql/sensitivitylabels/get): belirtilen sütunun duyarlılık etiketini alır.
- [Geçerli veritabanına göre Listele](/rest/api/sql/sensitivitylabels/listcurrentbydatabase): belirtilen veritabanının geçerli duyarlılık etiketlerini alır.
- [Veritabanı tarafından önerilen liste](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): belirtilen veritabanının önerilen duyarlılık etiketlerini alır.


## <a name="faq---advanced-classification-capabilities"></a>SSS-gelişmiş sınıflandırma özellikleri

**Soru**: [Azure PURVIEW](../../purview/overview.md) , SQL veri bulma & sınıflandırmasının yerini alacak veya SQL veri bulma & sınıflandırması yakında kullanımdan kalkacaktır mi?
**Cevap**: SQL veri bulma & sınıflandırmasını desteklemeye devam ediyoruz ve gelişmiş sınıflandırma özelliklerini ve veri yönetimini sağlamak için daha zengin özellikleri olan [Azure purview](../../purview/overview.md) 'ı benimsemenizi öneririz. Herhangi bir hizmeti, özelliği, API'yi veya SKU'yu kullanımdan kaldırmaya karar verirsek, bu konuda önceden geçiş yolunu da içeren bir bildirim alırsınız. Burada Microsoft Yaşam Döngüsü ilkeleri hakkında daha fazla bilgi edinebilirsiniz.


## <a name="next-steps"></a><a id="next-steps"></a>Sonraki adımlar

- Sınıflandırılan gizli verilerinize erişimi izlemek ve denetlemek için [Azure SQL denetimini](../../azure-sql/database/auditing-overview.md) yapılandırmayı düşünün.
- Veri bulma & sınıflandırması içeren bir sunum için bkz. [SQL verilerini koruma, sınıflandırma ve etiketleme & | Veriler kullanıma sunuldu](https://www.youtube.com/watch?v=itVi9bkJUNc).
- T-SQL komutlarını kullanarak Azure SQL veritabanlarınızı ve Azure SYNAPSE analizlerinizi Azure purview etiketleriyle sınıflandırmak için bkz. Azure [purview etiketlerini kullanarak Azure SQL verilerinizi sınıflandırın](../../sql-database/scripts/sql-database-import-purview-labels.md).