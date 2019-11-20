---
title: Veri bulma ve sınıflandırma
description: Azure SQL veritabanı ve veri bulma & sınıflandırması
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: b5f839cc6216eb12bfd0a86009ec49e987279d6e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889828"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Azure SQL veritabanı ve SQL veri ambarı veri bulma & sınıflandırması

Veri bulma & sınıflandırması, veritabanlarınızdaki hassas verileri **korumak** **Için Azure**SQL veritabanı 'nda **yerleşik olarak bulunan**gelişmiş özellikleri **sağlar & .**

En hassas verilerinizi (iş, finans, sağlık, kişisel olarak tanımlanabilir veriler (PII) vb.) bulmak ve sınıflandırmak, kurumsal bilgi koruma hazırünüzde bir özetleme rolü oynayabilir. Bu, için altyapı işlevi görebilir:

- Veri gizliliği standartları ve mevzuat uyumluluk gereksinimlerini karşılamanıza yardımcı olma.
- Hassas verilere yönelik anormal erişimlerde izleme (denetim) ve uyarı verme gibi çeşitli güvenlik senaryoları.
- Son derece hassas veriler içeren veritabanlarının güvenliğine erişimi denetleme ve güvenliği artırma.

Veri bulma & sınıflandırması, gelişmiş SQL güvenlik özelliklerine yönelik Birleşik bir paket olan [Gelişmiş veri güvenliği](sql-database-advanced-data-security.md) (ADS) sunumunun bir parçasıdır. Veri bulma & sınıflandırmasına, merkezi SQL ADS portalı aracılığıyla erişilebilir ve yönetilebilir.

> [!NOTE]
> Bu belge, Azure SQL veritabanı ve Azure SQL veri ambarı ile ilgilidir. Kolaylık açısından, hem SQL Veritabanı hem de SQL Veri Ambarı için SQL Veritabanı terimi kullanılmaktadır. SQL Server (Şirket içi) için bkz. [SQL veri bulma ve sınıflandırma](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Veri bulma & sınıflandırması nedir?

Veri bulma & sınıflandırması, yalnızca veritabanını değil, verileri korumaya yönelik yeni bir SQL Information Protection paradigmasını oluşturan bir dizi gelişmiş hizmet ve yeni SQL özelliği sunar:

- **Bulma & önerileri**

  Sınıflandırma Altyapısı, veritabanınızı tarar ve potansiyel olarak hassas verileri içeren sütunları tanımlar. Daha sonra, Azure portal aracılığıyla uygun sınıflandırma önerilerini gözden geçirmeniz ve uygulamanız için kolay bir yol sağlar.

- **Kapatma**

  Duyarlılık sınıflandırma etiketleri, SQL altyapısına sunulan yeni sınıflandırma meta veri öznitelikleri kullanılarak sütunlarda kalıcı olarak etiketlenebilir. Bu meta veriler daha sonra Gelişmiş duyarlılık tabanlı denetim ve koruma senaryoları için kullanılabilir.

- **Sorgu sonuç kümesi duyarlılığı**

  Sorgu sonuç kümesinin duyarlılığı, denetim amaçlarıyla gerçek zamanlı olarak hesaplanır.

- **Görünürlük**

  Veritabanı sınıflandırması durumu, portalda ayrıntılı bir panoda görüntülenebilir. Ayrıca, uyumluluk & Denetim amaçlarıyla ve diğer gereksinimlerde kullanılmak üzere bir raporu (Excel biçiminde) indirebilirsiniz.

## <a id="subheading-2"></a>& Etiketle hassas sütunları bulma, sınıflandırma

Aşağıdaki bölümde, veritabanınızdaki hassas verileri içeren sütunları bulma, sınıflandırma ve etiketleme ve veritabanınızın geçerli sınıflandırma durumunu görüntüleme ve raporları dışarı aktarma adımları açıklanmaktadır.

Sınıflandırma iki meta veri özniteliği içerir:

- Etiketler: sütunda depolanan verilerin duyarlılık düzeyini tanımlamak için kullanılan ana sınıflandırma öznitelikleri.  
- Bilgi türleri: sütunda depolanan verilerin türüne ek ayrıntı düzeyi sağlar.

## <a name="define-and-customize-your-classification-taxonomy"></a>Sınıflandırma taksonominizi tanımlama ve özelleştirme

SQL veri bulma & sınıflandırması yerleşik bir duyarlık etiketleri kümesiyle ve yerleşik bir bilgi türleri ve bulma mantığı kümesiyle gelir. Artık bu taksonomiyi özelleştirebilir ve özel olarak ortamınız için bir sınıflandırma yapıları kümesi ve derecelendirmesi tanımlayabilirsiniz.

Sınıflandırma sınıflandırmanızı tanımlama ve özelleştirme, tüm Azure kiracınız için tek bir yerde yapılır. Bu konum, güvenlik Ilkenizin bir parçası olarak [Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/security-center/security-center-intro)'nde bulunur. Yalnızca kiracı kök yönetim grubunda yönetici haklarına sahip bir kişi bu görevi gerçekleştirebilir.

Information Protection İlkesi yönetiminin bir parçası olarak özel Etiketler tanımlayabilir, bunları derecelendirip bunları seçili bir bilgi türleri kümesiyle ilişkilendirebilirsiniz. Ayrıca kendi özel bilgi türlerinizi ekleyebilir ve bunları veritabanlarınızdaki bu tür verileri tanımlamak için bulma mantığına eklenen dize desenleriyle yapılandırabilirsiniz.
[Information Protection ilkesi ile ilgili nasıl yapılır Kılavuzu](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)' nda ilkenizi özelleştirme ve yönetme hakkında daha fazla bilgi edinin.

Kiracı genelinde ilke tanımlandıktan sonra, özelleştirilmiş ilkenizi kullanarak ayrı veritabanlarının sınıflandırmasına devam edebilirsiniz.

## <a name="classify-your-sql-database"></a>SQL veritabanınızı sınıflandırın

1. [Azure Portal](https://portal.azure.com) gidin.

2. Azure SQL veritabanı bölmesinizdeki güvenlik başlığı altında **Gelişmiş veri güvenliği** ' ne gidin. Gelişmiş veri güvenliğini etkinleştirmek için tıklayın ve ardından **veri bulma & sınıflandırma** kartına tıklayın.

   ![Bir veritabanını tarayın](./media/sql-data-discovery-and-classification/data_classification.png)

3. **Genel bakış** sekmesi, yalnızca belirli şema parçalarını, bilgi türlerini ve etiketleri görüntülemek üzere filtreleyebileceğiniz, tüm sınıflandırılmış sütunların ayrıntılı bir listesi dahil olmak üzere veritabanının geçerli sınıflandırma durumunun bir özetini içerir. Henüz herhangi bir sütunu sınıflandırmadıysanız 5. [adıma atlayın](#step-5).

   ![Geçerli sınıflandırma durumunun özeti](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Excel biçiminde bir rapor indirmek için pencerenin üst menüsünde **dışarı aktar** seçeneğine tıklayın.

   ![Excel 'e aktar](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Verilerinizi sınıflandırmayla başlamak için pencerenin üst kısmındaki **Sınıflandırma sekmesine** tıklayın.

    ![Verileri sınıflandır](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. Sınıflandırma Altyapısı, veritabanınızı potansiyel olarak hassas veriler içeren sütunlar için tarar ve **Önerilen sütun sınıflandırmalarının**bir listesini sağlar. Sınıflandırma önerilerini görüntülemek ve uygulamak için:

   - Önerilen sütun sınıflandırmalarının listesini görüntülemek için pencerenin alt kısmındaki öneriler paneline tıklayın:

      ![Verilerinizi sınıflandırın](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Önerilerin listesini gözden geçirin – belirli bir sütuna yönelik öneriyi kabul etmek için ilgili satırın sol sütunundaki onay kutusunu işaretleyin. Öneriler tablosu üstbilgisindeki onay kutusunu işaretleyerek *tüm önerileri* kabul edildi olarak da işaretleyebilirsiniz.

       ![Öneri listesini gözden geçir](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Seçili önerileri uygulamak için mavi **Seçili önerileri kabul et** düğmesine tıklayın.

      ![Önerileri Uygula](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Ayrıca sütunları alternatif olarak veya Ayrıca, öneri tabanlı sınıflandırmayla **el ile sınıflandırabilir** :

   - Pencerenin üst menüsünde **Sınıflandırma Ekle** ' ye tıklayın.

      ![Sınıflandırmayı el ile Ekle](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - Açılan bağlam penceresinde, sınıflandırmak istediğiniz şema > Tablo > sütununu ve bilgi türünü ve duyarlılık etiketini seçin. Ardından bağlam penceresinin altındaki mavi **Sınıflandırma Ekle** düğmesine tıklayın.

      ![Sınıflandırmak için sütun seçin](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Sınıflandırmanızı ve kalıcı olarak etiketlemesini (etiketleyerek) Yeni sınıflandırma meta verileri ile veritabanı sütunlarını kullanarak, pencerenin üst menüsünde **Kaydet** ' e tıklayın.

   ![Kaydet](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Gizli verilere erişimi denetleme

Bilgi koruma paradigmasının önemli bir yönü, hassas verilere erişimi izleme olanağıdır. [Azure SQL veritabanı denetimi](sql-database-auditing.md) , sorgu tarafından döndürülen gerçek verilerin duyarlılık sınıflandırmalarını (etiketleri) günlüğe kaydeden *data_sensitivity_information*adlı denetim günlüğüne yeni bir alan içerecek şekilde geliştirilmiştir.

![Denetim günlüğü](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>T-SQL kullanarak veri sınıflandırmasını yönetme

T-SQL ' i sütun sınıflandırmaları eklemek/kaldırmak ve tüm veritabanının tüm sınıflandırmalarını almak için kullanabilirsiniz.

> [!NOTE]
> Etiketleri yönetmek için T-SQL kullanırken, bir sütuna eklenen etiketlerin kurumsal bilgi koruma ilkesinde (portal önerilerinde görünen etiketler kümesi) mevcut olduğu bir doğrulama yoktur. Bu nedenle, bunu doğrulamak için kullanılır.

- Bir veya daha fazla sütunun sınıflandırmasını ekleyin/güncelleştirin: [DUYARLıLıK sınıflandırması Ekle](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Sınıflandırmayı bir veya daha fazla sütundan kaldırma: [DÜŞÜRÜLME DUYARLıLıĞı sınıflandırması](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Veritabanındaki tüm sınıflandırmaları görüntüle: [sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="manage-classifications-using-rest-apis"></a>REST API 'Leri kullanarak sınıflandırmaları yönetme

Sınıflandırmaları programlı bir şekilde yönetmek için REST API 'Lerini de kullanabilirsiniz. Yayımlanan REST API 'Leri aşağıdaki işlemleri destekler:

- [Oluştur veya Güncelleştir](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) -belirli bir sütunun duyarlılık etiketini oluşturur veya güncelleştirir
- [Sil](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) -belirli bir sütunun duyarlılık etiketini siler
- [Öneriyi devre dışı bırak](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) -belirli bir sütunda duyarlılık önerilerini devre dışı bırakır
- [Öneriyi etkinleştir](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) -belirli bir sütunda duyarlılık önerilerini etkinleştirir (öneriler varsayılan olarak tüm sütunlarda etkindir)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) -belirli bir sütunun duyarlılık etiketini alır
- [Geçerli veritabanına göre Listele](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) -belirli bir veritabanının geçerli duyarlılık etiketlerini alır

- [Veritabanı tarafından önerilen liste](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) -belirli bir veritabanının önerilen duyarlılık etiketlerini alır

## <a name="manage-data-discovery-and-classification-using-azure-powershell"></a>Azure PowerShell kullanarak veri bulmayı ve sınıflandırmayı yönetme

Bir Azure SQL veritabanında ve yönetilen bir örnekte, önerilen tüm sütunları almak için PowerShell 'i kullanabilirsiniz.

### <a name="powershell-cmdlets-for-azure-sql-database"></a>Azure SQL veritabanı için PowerShell cmdlet 'Leri

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>Yönetilen örnek için PowerShell cmdlet 'Leri

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

## <a name="permissions"></a>İzinler

Aşağıdaki yerleşik roller bir Azure SQL veritabanının veri sınıflandırmasını okuyabilir: `Owner`, `Reader`, `Contributor`, `SQL Security Manager` ve `User Access Administrator`.

Aşağıdaki yerleşik roller bir Azure SQL veritabanının veri sınıflandırmasını değiştirebilir: `Owner`, `Contributor``SQL Security Manager`.

[Azure kaynakları Için RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) hakkında daha fazla bilgi edinin

## <a id="subheading-5"></a>Sonraki adımlar

- [Gelişmiş veri güvenliği](sql-database-advanced-data-security.md)hakkında daha fazla bilgi edinin.
- Sınıflandırılan gizli verilerinize erişimi izlemek ve denetlemek için [Azure SQL veritabanı denetimini](sql-database-auditing.md) yapılandırmayı düşünün.

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
