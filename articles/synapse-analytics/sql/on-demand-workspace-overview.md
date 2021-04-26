---
title: Sunucusuz SQL havuzu
description: Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu hakkında bilgi edinin.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f0abef835e406b442239cecd81fed5751f1c9a92
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378168"
---
# <a name="serverless-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu 

Her Azure SYNAPSE Analytics çalışma alanı, Gölü verileri sorgulamak için kullanabileceğiniz sunucusuz SQL havuzu uç noktaları ile gelir.

Sunucusuz SQL havuzu, Data Lake 'unuzdaki veriler üzerinde bir sorgu hizmetidir. Verilerinize aşağıdaki işlevleri kullanarak erişmenizi sağlar:
 
- Verileri özelleştirilmiş bir depoya kopyalama veya yüklemeye gerek kalmadan verileri sorgulamak için tanıdık bir T-SQL söz dizimi. 
- En popüler sürücüler dahil olmak üzere çok çeşitli iş zekası ve geçici sorgulama araçları sunan T-SQL arabirimi aracılığıyla tümleşik bağlantı. 

Sunucusuz SQL havuzu, büyük ölçekli veriler ve hesaplama işlevleri için oluşturulmuş bir dağıtılmış veri işleme sistemidir. Sunucusuz SQL havuzu, iş yüküne bağlı olarak büyük verilerinizi Saniyeler içinde dakikalar içinde analiz etmenizi sağlar. Yerleşik sorgu yürütme hata toleransı sayesinde sistem, büyük veri kümeleri içeren uzun süreli sorgularda bile yüksek güvenilirlik ve başarı oranları sağlar.

Sunucusuz SQL havuzu sunucusuz olduğundan, kuruluma veya korunacak kümeler için bir altyapı yoktur. Bu hizmet için varsayılan bir uç nokta her Azure SYNAPSE çalışma alanı içinde sağlanır, bu sayede çalışma alanı oluşturulduktan hemen sonra verileri sorgulamaya başlayabilirsiniz. 

Ayrılan kaynaklar için ücret alınmaz, yalnızca çalıştırdığınız sorgular tarafından işlenen veriler için ücretlendirilirsiniz, bu nedenle bu modelin kullanım başına doğru bir ödeme modeli olması gerekir.  

Veri işlem hattınızda Azure SYNAPSE için Apache Spark kullanıyorsanız, veri hazırlama, temizleme veya zenginleştirme için, doğrudan sunucusuz SQL havuzundan, işlemde oluşturduğunuz [Harici Spark tablolarını sorgulayabilirsiniz](develop-storage-files-spark-tables.md) . [Özel bağlantı](../security/how-to-connect-to-workspace-with-private-links.md) kullanarak SUNUCUSUZ SQL havuzu uç noktanızı [yönetilen çalışma alanı sanal](../security/synapse-workspace-managed-vnet.md)ağınıza taşıyın.  

## <a name="serverless-sql-pool-benefits"></a>Sunucusuz SQL havuzu avantajları

Veri Gölü verileri araştırmanıza, bundan Öngörüler elde etmeniz veya mevcut veri dönüştürme işlem hattınızı iyileştirmeniz gerekiyorsa sunucusuz SQL havuzu kullanmaktan faydalanabilirsiniz. Aşağıdaki senaryolar için uygundur:

- Temel bulma ve araştırmayla ilgili veri Gölü biçimlerdeki veriler hakkında hızlı bir neden (Parquet, CSV, JSON), bu sayede öngörüleri nasıl ayıklayabileceğinizi planlayabileceksiniz.
- Mantıksal veri ambarı: verilerin üzerine konumlandırmadan ve dönüştürmeden verileri her zaman güncel görüntülemeye izin vererek ham veya farklı verilerin üzerinde ilişkisel bir soyutlama sağlar.
- Veri dönüştürme-T-SQL ' y i kullanarak Gölü verileri dönüştürmenin basit, ölçeklenebilir ve performanslı yolu, bı ve diğer araçlara geçirilebilir veya bir ilişkisel veri deposuna (SYNAPSE SQL veritabanları, Azure SQL veritabanı vb.) yüklenebilir.

Farklı profesyonel roller sunucusuz SQL havuzundan faydalanabilir:

- Veri mühendisleri gölü keşfedebilir, bu hizmeti kullanarak verileri dönüştürebilir ve hazırlayabilir ve veri dönüştürme işlem hatlarını basitleştirir. Daha fazla bilgi için bu [öğreticiye](tutorial-data-analyst.md)bakın.
- Veri bilimcileri, OPENROWSET ve otomatik Şema çıkarımı gibi özellikler sayesinde Gölü verilerin içerikleri ve yapısı hakkında hızlı bir neden olabilir.
- Veri analistleri, veri bilimcileri veya veri mühendisleri tarafından oluşturulan [verileri ve Spark dış tablolarını](develop-storage-files-spark-tables.md) , tanıdık T-SQL dilini veya en sevdiğiniz araçları kullanarak, sunucusuz SQL havuzuna bağlanabilir şekilde keşfedebilir.
- Bı uzmanları, Gölü ve Spark tablolarındaki verilerin üzerine hızlı [bir şekilde Power BI raporlar oluşturabilir](tutorial-connect-power-bi-desktop.md) .

## <a name="how-to-start-using-serverless-sql-pool"></a>Sunucusuz SQL havuzunu kullanmaya başlama

Sunucusuz SQL havuzu uç noktası her Azure SYNAPSE çalışma alanı içinde sağlanır. Bir çalışma alanı oluşturabilir ve tanıdığınız araçları kullanarak verileri anında sorgulamaya başlayabilirsiniz.

## <a name="client-tools"></a>İstemci araçları

Sunucusuz SQL havuzu, mevcut SQL geçici sorgulama ve iş zekası araçlarının Veri Gölü içine dokunmasına olanak sağlar. Tanıdık T-SQL söz dizimini sağladığından, SQL teklifleriyle TDS bağlantısı kurabilen herhangi bir araç, [SYNAPSE SQL 'e bağlanabilir ve sorgu](connect-overview.md)oluşturabilir. Azure Data Studio ile bağlanabilir, geçici sorgular çalıştırabilir veya birkaç dakika içinde Öngörüler elde etmek için Power BI ile bağlanabilirsiniz.

## <a name="t-sql-support"></a>T-SQL desteği

Sunucusuz SQL havuzu, yarı yapılandırılmış ve yapılandırılmamış verileri sorgulama konusunda deneyimlere uyum sağlamak için bazı açılardan biraz gelişmiş/genişletilmiş olan T-SQL sorgulama yüzey alanı sunar. Ayrıca, daha az SQL havuzunun tasarımı nedeniyle T-SQL dilinin bazı yönleri desteklenmez, örneğin DML işlevselliği Şu anda desteklenmemektedir.

- İş yükü tanıdık kavramlar kullanılarak düzenlenebilir:
- Veritabanları-sunucusuz SQL havuzu uç noktası birden çok veritabanına sahip olabilir.
- Şemalar-bir veritabanı Içinde, şemalar adlı bir veya daha fazla nesne sahiplik grubu olabilir.
- Görünümler
- Dış kaynaklar – veri kaynakları, dosya biçimleri ve tablolar

Şu kullanılarak güvenlik zorlanabilir:

- Oturum açma bilgileri ve kullanıcılar
- Depolama hesaplarına erişimi denetlemek için kimlik bilgileri
- Nesne düzeyinde verme, reddetme ve iptal etme izinleri
- Azure Active Directory tümleştirmesi

Desteklenen T-SQL:

- Tam [seçme](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) yüzeyı alanı SQL işlevlerinin çoğunluğu dahil desteklenir
- CETAS-DıŞ TABLOYU SEÇ OLARAK OLUŞTUR
- Yalnızca görünümler ve güvenlikle ilgili DDL deyimleri

Sunucusuz SQL havuzunda yerel depolama alanı yoktur, yalnızca meta veri nesneleri veritabanlarında depolanır. Bu nedenle, aşağıdaki kavramlarla ilgili T-SQL desteklenmez:

- Tables
- Tetikleyiciler
- Gerçekleştirilmiş görünümler
- Görünümler ve güvenlikle ilgili olanlar dışındaki DDL deyimleri
- DML deyimleri

### <a name="extensions"></a>Uzantıları

Data Lake 'teki dosyalarda bulunan verilerin yerinde sorgulanmasında sorunsuz bir deneyim sağlamak için, sunucusuz SQL havuzu aşağıdaki özellikleri ekleyerek var olan [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) işlevini genişletir:

[Birden çok dosyayı veya klasörü sorgulama](query-data-storage.md#query-multiple-files-or-folders)

[PARQUET dosya biçimi](query-data-storage.md#query-parquet-files)

[Sınırlandırılmış metinle çalışmak için ek seçenekler (alan Sonlandırıcı, satır Sonlandırıcı, kaçış karakteri)](query-data-storage.md#query-csv-files)

[Seçili sütunların bir alt kümesini okuyun](query-data-storage.md#read-a-chosen-subset-of-columns)

[Şema çıkarımı](query-data-storage.md#schema-inference)

[filename işlevi](query-data-storage.md#filename-function)

[FilePath işlevi](query-data-storage.md#filepath-function)

[Karmaşık türlerle ve iç içe veya yinelenen veri yapıları ile çalışma](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Güvenlik

Sunucusuz SQL havuzu verilerinize güvenli bir şekilde erişmek için mekanizmalar sunar.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory tümleştirmesi ve çok faktörlü kimlik doğrulaması

Sunucusuz SQL havuzu, veritabanı kullanıcısı ve diğer Microsoft hizmetlerinin kimliklerini [Azure Active Directory tümleştirmeyle](../../azure-sql/database/authentication-aad-configure.md)merkezi olarak yönetmenize olanak sağlar. Bu özellik, izin yönetimini kolaylaştırırken güvenliği artırır. Azure Active Directory (Azure AD), çoklu bir oturum açma işlemini desteklerken veri ve uygulama güvenliğini artırmak için [çok faktörlü kimlik doğrulamasını](../../azure-sql/database/authentication-mfa-ssms-configure.md) (MFA) destekler.

#### <a name="authentication"></a>Kimlik Doğrulaması

Sunucusuz SQL havuzu kimlik doğrulaması, kullanıcıların uç noktaya bağlanırken kimliklerini nasıl kanıtlayacağına başvurur. İki tür kimlik doğrulaması desteklenir:

- **SQL kimlik doğrulaması**

  Bu kimlik doğrulama yöntemi bir Kullanıcı adı ve parola kullanır.

- **Azure Active Directory kimlik doğrulaması**:

  Bu kimlik doğrulama yöntemi, Azure Active Directory tarafından yönetilen kimlikleri kullanır. Azure AD kullanıcıları için Multi-Factor Authentication etkinleştirilebilir. [Mümkün olan her durumda](/sql/relational-databases/security/choose-an-authentication-mode?view=azure-sqldw-latest&preserve-view=true)Active Directory kimlik doğrulaması (tümleşik güvenlik) kullanın.

#### <a name="authorization"></a>Yetkilendirme

Yetkilendirme, bir kullanıcının sunucusuz bir SQL havuzu veritabanında neler yapabileceğini belirtir ve Kullanıcı hesabınızın veritabanı rolü üyelikleri ve nesne düzeyi izinleri tarafından kontrol edilir.

SQL kimlik doğrulaması kullanılırsa, SQL kullanıcısı yalnızca sunucusuz SQL havuzunda bulunur ve izinler sunucusuz SQL havuzundaki nesneler kapsamlandırılır. Diğer hizmetlerde (Azure depolama gibi) güvenli kılınabilir nesnelere erişim, yalnızca sunucusuz SQL Havuzu kapsamında bulunduğundan SQL kullanıcısına doğrudan verilemez. SQL kullanıcısının dosyalara erişmek için [Desteklenen yetkilendirme türlerinden](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) birini kullanması gerekir.

Azure AD kimlik doğrulaması kullanılırsa, Kullanıcı sunucusuz SQL havuzunda ve Azure depolama gibi diğer hizmetlerde oturum açabilir ve Azure AD kullanıcısına izin verebilir.

### <a name="access-to-storage-accounts"></a>Depolama hesaplarına erişim

Sunucusuz SQL havuzu hizmetinde oturum açan bir Kullanıcı, Azure depolama 'daki dosyalara erişme ve bunları sorgulama yetkisine sahip olmalıdır. Sunucusuz SQL havuzu aşağıdaki yetkilendirme türlerini destekler:

- **Paylaşılan erişim imzası (SAS)** , depolama hesabındaki kaynaklara temsilci erişimi sağlar. SAS ile, istemcilere, hesap anahtarlarını paylaşmadan depolama hesabındaki kaynaklara erişim izni verebilirsiniz. SAS: geçerlilik aralığı, izin verilen izinler, kabul edilebilir IP adresi aralığı, kabul edilebilir protokol (https/http) olan istemcilere verdiğiniz erişim türü üzerinde ayrıntılı denetim elde etmenizi sağlar.

- **Kullanıcı kimliği** ("doğrudan geçiş" olarak da bilinir), SUNUCUSUZ SQL havuzunda oturum açan Azure AD kullanıcısının kimliğinin verilere erişim yetkisi vermek için kullanıldığı bir yetkilendirme türüdür. Verilere erişmeden önce Azure depolama yöneticisinin verilere erişmek için Azure AD kullanıcısına izin vermesi gerekir. Bu yetkilendirme türü, sunucusuz SQL havuzunda oturum açan Azure AD kullanıcısını kullanır, bu nedenle SQL kullanıcı türleri için desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar
Uç nokta bağlantısı ve dosya sorgulama hakkında ek bilgiler aşağıdaki makalelerde bulunabilir: 
- [Uç noktanıza bağlanma](connect-overview.md)
- [Dosyalarınızı sorgulama](develop-storage-files-overview.md)
