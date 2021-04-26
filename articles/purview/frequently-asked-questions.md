---
title: Sık sorulan sorular (SSS)
description: Bu makalede, Azure purview hakkında sık sorulan sorular yanıtlanmaktadır.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 94b765cbcbdd81505b08052845207ee1d93a28d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667815"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Azure purview hakkında sık sorulan sorular (SSS)

## <a name="overview"></a>Genel Bakış

Birçok kuruluş, verilerinin bütünsel anlayamasından kaynaklanır. Verilerin nerede olduğunu, verilerin nerede bulunduğunu ve ilgili verilerin nasıl bulunacağını ve bu verilere nasıl erişebileceğinizi anlamak zor bir şeydir. Veriler kökenini, sınıflandırma ve kapsamlı meta veriler gibi bağlamla, iş kullanıcılarının doğru verileri aramasını ve bu verileri uygun şekilde kullanmasını zorlaştırır. Sonuç olarak, iş kararlarını bilgilendirmek için toplanan verilerin yalnızca küçük bir bölümü kullanılır. Son olarak, veri güvenliği sorunlarını tanımlama ve hassas verileri koruma tutarsız. Özellikle veri çevikliğini koruyarak devam eden zaman ve çaba gerektirir.

Azure purview, bir veri idare çözümüdür. Müşterilerin kullanımı üzerinde denetim sağlarken müşterilerin tüm verileri hakkında derin bilgi edinmelerini sağlar. Azure purview sayesinde kuruluşlar verileri bulur ve görüntüler. Bunlar, verileri ele almak için Öngörüler elde ederler ve verilere erişimi merkezi olarak yönetir.

## <a name="purpose-of-this-faq"></a>Bu SSS 'nin amacı

Bu SSS, müşterilerin ve alan ekiplerinin genellikle sorduğu yaygın sorulara yanıt verir. Azure takip görünümü ve Azure Veri Kataloğu (ADC) Gen 2 (kullanım dışı) ve Azure Information Protection gibi ilgili çözümlerle ilgili sorularınızı açıklığa kavuşturmaya yöneliktir.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>Meta veri tarama ve sınıflandırma için kullanılabilen kaynak türleri nelerdir?

|Azure|Azure dışı|
|---------|---------|
|Azure Blob depolama|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (2020 sonuna kadar kullanılabilir)|
|Azure SQL Yönetilen Örnek|SAP ECC (2020 sonuna kadar kullanılabilir)|
|Azure Veri Gezgini|SAP S/4 HANA (2020 sonuna kadar kullanılabilir)|
|Azure Data Lake Storage Gen1|Hive meta veri deposu (2020 sonuna kadar kullanılabilir)|
|Azure Data Lake Storage Gen2|Amazon S3|
|Azure Dosyaları|--|
|Azure SQL Veritabanı|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>Hangi veri sistemlerini/işlemcileri bağlayabiliriz ve kökenini alabilirim?

|Veri sistemi/işlemci 
|---------
|Azure Data Factory: kopyalama etkinliği, veri akışı etkinliği 
|Özel kökenini   
|Azure Veri Paylaşımı   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>ADC Gen 2, Azure Information Protection ve Azure takip görünümü nasıl ilişkilidir?

Azure purview, ilk başta ADC Gen 2 olarak başladı, ancak kapsamdaki kapsamda bu yana sahip. Artık, Azure Information Protection veri sınıflandırması, etiketleme ve uyumluluk ilkesi zorlama özellikleri ile birlikte ADC Gen 2 ' nin gelişmiş Katalog yeteneklerini de kapsar. Günümüzde veri yönetimi 'nin daha geniş sektör tanımına daha yakından hizalanır.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>ADC Gen 1 kullanan müşterilere ne olur?

Azure purview, Microsoft 'a yönelik Katalog çözüm alanındaki tüm ürün yeniliklerini odakdır. ADC Gen 1 desteklenmeye devam edecektir.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>Müşteriler aynı abonelikte birden çok Azure purview hesabına sahip olabilir mi?

Evet, abonelik başına ve kiracı başına çok sayıda Azure purview hesabı destekliyoruz.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>ADC Gen 1 ve Azure purview ' ı paralel olarak çalıştırabilir miyim?

Evet. İkisi de bağımsız hizmetlerdir.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>Mevcut ADC Gen 1 veri varlıklarını Azure purview 'a geçirmek Nasıl yaparım?.

Azure purview API 'Lerini kullanarak ADC Gen 1 ' den ve alma, Azure purview 'a ayıklayın. Sözlük için CSV 'yi temel alan toplu araçları destekliyoruz.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Azure purview kullanarak SQL tabloları için hassas verileri Nasıl yaparım? şifreleyin?

Veri şifreleme, veri kaynağı düzeyinde yapılır. Azure purview yalnızca meta verileri depolar. Verilerin önizlemesini yapmaz.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>Azure purview 'da ADC Gen 2 ' nin tüm özellikleri var mı?

Evet.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>Sözlük ve sınıflandırma arasındaki fark nedir?

Sözlük, veri tüketicileri olarak da bilinen, verilerin teknik olmayan/iş kullanıcıları tarafından izlenen bir adlandırma kuralı kullanır. Bu tür kişiler iş analistleri veya Azure purview kullanan veri bilimcileri, iş kullanımı temelinde belirli veri türlerini aramak için kullanılır. Örneğin, tedarik zinciri analistlerinin hüküm *SKU türlerini* ve *Sevkiyat ayrıntılarını* araması gerekebilir. İlgili verileri bulmak için bu koşullara yönelik sözlükte arama yapılır.
Sınıflandırma, varlık içinde hangi verilerin bulunduğunu belirleyen tablo, sütun veya dosya düzeyindeki bir veri varlığına uygulanan bir etikettir. Sınıflandırma, bulunan veri türüne göre otomatik olarak veya el ile uygulanabilir. Genellikle, bir varlığın hassas verileri içerip içermediğini ve ne tür hassas verilerin olabileceğini belirlemek için sınıflandırma etiketlerini kullanırsınız.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Azure purview, SharePoint ve OneDrive 'daki e-postaları ve PDF 'Leri taramayı ve sınıflandırmalarını ister misiniz?

Şirket içi SharePoint siteleri ve kitaplıklarını taramak Azure Information Protection tarayıcısı aracılığıyla sağlanır. Tarayıcı, aşağıdaki SKU 'Lara sahip bir müşterinin Microsoft 365 aboneliği aracılığıyla kullanılabilir: AıP P1, EMS E3 ve M365 E3. Bu SKU 'Lardan birine sahipseniz, Azure Information Protection tarayıcıyı kullanmaya başlamak için doğru Yetkilendirmelere sahip olmanız gerekir.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>Tarama için kullanılan işlem nedir?
Microsoft tarafından yönetilen bir tarama altyapısı vardır. Desteklediğimiz çoğu Azure/AWS kaynağı için bir tarama altyapısı dağıtmanız gerekmez.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>Azure Resource Manager (ARM) şablonu/CLı/PowerShell aracılığıyla Azure purview sağlamak için bir yol var mı?

Evet, ARM şablonu kullanılabilir

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>Zaten Atlas kullanıyorum, Azure purview 'a kolayca geçiş yapabilir miyim?

Azure purview, Atlas API 'siyle uyumludur. Atlas 'den geçiş yapıyorsanız, önce Azure purview kullanarak veri kaynaklarınızı taramanızı öneririz. Varlıklar hesabınızda kullanılabilir olduktan sonra, varlıkları güncelleştirme veya özel kökenini ekleme gibi tümleştirmede benzer Atlas API 'Leri kullanabilirsiniz. Azure purview, Azure Search kullanmak için arama API 'sini değiştirir, böylece gelişmiş arama 'yı kullanabilmeniz gerekir.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>Kiracımda birden fazla katalog oluşturabilir miyim?

Evet, abonelik başına ve her kiracı için birden çok Azure purview hesabı oluşturabilirsiniz. Sınır sayfasını gözden geçirerek [Azure purview ile kaynaklar için kotaları yönetebilir ve artırabilirsiniz](how-to-manage-quotas.md).

[Azure purview dağıtım en iyi yöntemlerimizde](deployment-best-practices.md), birden çok hesabınız olması gerektiğinde veya bu hesapların ne zaman belgelendiğinden ilgili ek öneri.

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>Birden çok kiracıyı tek bir Azure purview hesabı içinde kaydedebilir miyim?

Hayır, şu anda başka bir kiracının veri kaynağını taramak için, bu kiracıya ayrı bir Azure purview hesabı oluşturmanız gerekir.

### <a name="does-azure-purview-support-column-level-lineage"></a>Azure purview, sütun düzeyi kökenini destekliyor mu?

Evet, Azure purview, kökenini sütun düzeyini destekliyor.