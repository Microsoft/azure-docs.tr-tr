---
title: Azure Analysis Services desteklenen veri kaynakları | Microsoft Docs
description: Azure Analysis Services tablo 1200 ve daha yüksek veri modelleri için desteklenen veri kaynaklarını ve bağlayıcıları açıklar.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b51ceb97d607f3082e1e7cc4c94083c9215b2db8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731488"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services’ta desteklenen veri kaynakları

Visual Studio 'da Analysis Services projelerine sahip veri veya tablo Içeri aktarma Sihirbazı ' nda gösterilen veri kaynakları ve bağlayıcılar hem Azure Analysis Services hem de SQL Server Analysis Services için gösterilmektedir. Ancak, gösterilen tüm veri kaynakları ve bağlayıcılar Azure Analysis Services desteklenmez. Bağlantı alabileceğiniz veri kaynağı türleri model uyumluluk düzeyi, kullanılabilir veri bağlayıcıları, kimlik doğrulama türü ve şirket içi veri ağ geçidi desteği gibi birçok faktöre bağlıdır. Aşağıdaki tablolarda Azure Analysis Services için desteklenen veri kaynakları açıklanır.

## <a name="azure-data-sources"></a>Azure veri kaynakları

|Veri kaynağı  |Bellek içi  |DirectQuery  |Notlar |
|---------|---------|---------|---------|
|Azure SQL Veritabanı      |   Evet      |    Yes      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   Yes      |   Yes       |<sup>[iki](#azprovider)</sup>|
|Azure Blob Depolama Alanı      |   Evet       |    Hayır      | <sup>[1](#tab1400a)</sup> |
|Azure Tablo Depolama     |   Evet       |    Hayır      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Evet        |  Hayır        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Storage 1. Nesil      |   Yes       |    Hayır      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Storage 2. Nesil       |   Yes       |    Hayır      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|Azure HDInsight.    |     Yes     |   Hayır       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Evet       |   Hayır       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Notlar:**

yalnızca <a name="tab1400a">1</a> tablolu 1400 ve daha yüksek modeller.  
<a name="azprovider">2</a> -tablo 1200 ve daha yüksek modellerde *sağlayıcı* veri kaynağı olarak belirtildiğinde, hem bellek içi hem de DirectQuery modelleri, SQL Server IÇIN SQL Server msoledbsql (önerilen) veya .NET Framework veri sağlayıcısı için Microsoft OLE DB sürücüsü gerektirir.  
<a name="azsqlmanaged">3</a> -Azure SQL yönetilen örneği desteklenir. SQL yönetilen örneği, Azure sanal ağı 'nda özel bir IP adresiyle çalıştığı için, örnekte ortak uç noktanın etkinleştirilmesi gerekir. Etkinleştirilmemişse, Şirket [içi veri ağ geçidi](analysis-services-gateway.md) gerekir.  
<a name="databricks">4</a> -Spark bağlayıcısını kullanan Azure Databricks Şu anda desteklenmiyor.  
<a name="gen2">5</a> -ADLS 2. Bağlayıcısı Şu anda desteklenmiyor, ancak Azure Blob depolama bağlayıcısı bir ADLS 2. veri kaynağıyla birlikte kullanılabilir.

## <a name="other-data-sources"></a>Diğer veri kaynakları

|Veri kaynağı | Bellek içi | DirectQuery |Notlar   |
|  --- | --- | --- | --- |
|Access Veritabanı     |  Evet | Hayır |  |
|Active Directory     |  Evet | Hayır | <sup>[inç](#tab1400b)</sup>  |
|Analysis Services     |  Yes | Hayır |  |
|Analytics platform sistemi     |  Yes | Hayır |  |
|CSV dosyası  |Yes | Hayır |  |
|Dynamics 365     |  Yes | Hayır | <sup>[inç](#tab1400b)</sup> |
|Excel çalışma kitabı     |  Yes | Hayır |  |
|Exchange      |  Yes | Hayır | <sup>[inç](#tab1400b)</sup> |
|Klasör      |Evet | Hayır | <sup>[inç](#tab1400b)</sup> |
|IBM Informix  |Yes | Hayır |  |
|JSON belgesi      |  Yes | Hayır | <sup>[inç](#tab1400b)</sup> |
|İkili dosyadan satırlar      | Yes | Hayır | <sup>[inç](#tab1400b)</sup> |
|MySQL Veritabanı     | Evet | Hayır |  |
|OData Akışı      |  Evet | Hayır | <sup>[inç](#tab1400b)</sup> |
|ODBC sorgusu     | Yes | Hayır |  |
|OLE DB     |   Evet | Hayır |  |
|Oracle  | Evet  |Yes  | <sup>[tuşlarına](#oracle)</sup> |
|PostgreSQL Veritabanı   | Evet | Hayır | <sup>[inç](#tab1400b)</sup> |
|Salesforce Nesneleri|  Evet | Hayır | <sup>[inç](#tab1400b)</sup> |
|Salesforce Raporları |Evet | Hayır | <sup>[inç](#tab1400b)</sup> |
|SAP HANA     |  Evet | Hayır |  |
|SAP Business Warehouse    |  Yes | Hayır | <sup>[inç](#tab1400b)</sup> |
|SharePoint Listesi      |   Evet | Hayır | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Evet   | Yes  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|SQL Server veri ambarı |Yes   | Yes  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Sybase Veritabanı     |  Evet | Hayır |  |
|Teradata | Evet  | Yes  | <sup>[(](#teradata)</sup> |
|TXT dosyası  |Yes | Hayır |  |
|XML tablosu    |  Yes | Hayır | <sup>[inç](#tab1400b)</sup> |
| | | |

**Notlar:**  
yalnızca <a name="tab1400b">6</a> tablolu 1400 ve daha yüksek modeller.  
<a name="sqlim">7</a> -tabular 1200 ve üzeri modellerde *sağlayıcı* veri kaynağı olarak belirtildiğinde, SQL Server Msoledbsql için Microsoft OLE DB sürücüsü (önerilen), SQL Server Native Client 11,0 veya veri sağlayıcısı için .NET Framework SQL Server belirtin.  
<a name="instgw">8</a> -veri sağlayıcısı olarak MSOLEDBSQL belirtildiğinde, [SQL Server Için Microsoft OLE DB sürücüsünü](/sql/connect/oledb/oledb-driver-for-sql-server) şirket içi veri ağ geçidiyle aynı bilgisayara indirip yüklemek gerekebilir.  
<a name="oracle">9</a> -tabular 1200 modelleri için ya da tablosal 1400 + modelleriyle bir *sağlayıcı* veri kaynağı olarak, .NET için Oracle veri sağlayıcısı belirtin. Yapılandırılmış bir veri kaynağı olarak belirtilmişse, [Oracle yönetilen sağlayıcısını](#enable-oracle-managed-provider)etkinleştirdiğinizden emin olun.   
<a name="teradata">10</a> tablolu 1200 modellerdeki veya tablo 1400 + modelleriyle bir *sağlayıcı* veri kaynağı olarak, .NET için Teradata veri sağlayıcısı belirtin.  
<a name="filesSP">11</a> -şirket içi SharePoint 'teki dosyalar desteklenmez.

Bir Azure Analysis Services sunucusundan şirket içi veri kaynaklarına bağlanmak için şirket [içi ağ geçidi](analysis-services-gateway.md)gerekir. Bir ağ geçidi kullanırken, 64 bit sağlayıcılar gereklidir.

## <a name="understanding-providers"></a>Sağlayıcıları anlama

Visual Studio 'da tablo 1400 ve üzeri model projeleri oluştururken, varsayılan olarak veri **Al** seçeneğini kullanarak bir veri kaynağına bağlanırken bir veri sağlayıcısı belirtmeyin. Tablo 1400 ve üzeri modeller, veri kaynağı ile Analysis Services arasında bağlantıları, veri sorgularını ve karmalar yönetmek için [Power Query](/power-query/power-query-what-is-power-query) bağlayıcıları kullanır. Bunlar bazen bu bağlantı özelliği ayarlarındaki *yapılandırılmış* veri kaynağı bağlantıları sizin için ayarlanmış olarak adlandırılır. Ancak, Visual Studio 'da bir model projesi için eski veri kaynaklarını etkinleştirebilirsiniz. Etkinleştirildiğinde, tablo 1200 ' de desteklenen belirli veri kaynaklarına bağlanmak için **tablo Içeri aktarma Sihirbazı** ' nı ve *eski* veya *sağlayıcı* veri kaynakları olarak daha düşük modelleri kullanabilirsiniz. Sağlayıcı veri kaynağı olarak belirtildiğinde, belirli bir veri sağlayıcısı ve diğer gelişmiş bağlantı özelliklerini belirtebilirsiniz. Örneğin, bir SQL Server veri ambarı örneğine veya hatta bir Azure SQL veritabanına, eski veri kaynağı olarak bağlanabilirsiniz. Daha sonra SQL Server MSOLEDBSQL veri sağlayıcısı OLE DB sürücüsünü seçebilirsiniz. Bu durumda, bir OLE DB veri sağlayıcısı seçildiğinde Power Query Bağlayıcısı üzerinden geliştirilmiş performans sağlayabilirsiniz. 

Visual Studio 'da tablo Içeri aktarma Sihirbazı 'nı kullanırken, herhangi bir veri kaynağına yönelik bağlantılar bir veri sağlayıcısı gerektirir. Sizin için varsayılan bir veri sağlayıcısı seçilidir. Gerekirse veri sağlayıcısını değiştirebilirsiniz. Seçtiğiniz sağlayıcının türü, modelin bellek içi depolama alanı veya DirectQuery kullanıp kullanmadığını ve modelinize dağıttığınız Analysis Services platformunu kullanarak, performansa bağlı olabilir.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Tablo 1400 ve daha yüksek model projelerinde sağlayıcı veri kaynaklarını belirtin

Sağlayıcı veri kaynaklarını etkinleştirmek için, Visual Studio 'da **Araçlar**  >  **Seçenekler**  >  **Analysis Services tablo**  >  **verilerini içeri aktar**' a tıklayın, **eski veri kaynaklarını etkinleştir**' i seçin.

![Eski veri kaynaklarını etkinleştir](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Eski veri kaynakları etkinken **tablosal Model Gezgini**' nde veri kaynağı '   >  **ndan (eski)** veri kaynakları ' na sağ tıklayın.

![Tablosal model Gezgininde eski veri kaynakları](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Tablolu 1200 model projelerinde olduğu gibi, bir veri kaynağına bağlanmak için **tablo Içeri aktarma Sihirbazı 'nı** kullanın. Bağlan sayfasında **Gelişmiş**' e tıklayın. Veri sağlayıcısını ve diğer bağlantı ayarlarını **Gelişmiş Özellikler ayarla**' da belirtin.

![Eski veri kaynakları gelişmiş özellikler](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>Kimliğe bürünme
Bazı durumlarda, farklı bir kimliğe bürünme hesabı belirtmek gerekli olabilir. Kimliğe bürünme hesabı, Visual Studio veya SQL Server Management Studio (SSMS) ' de belirtilebilir.

Şirket içi veri kaynakları için:

* SQL kimlik doğrulaması kullanılıyorsa, kimliğe bürünme hizmet hesabı olmalıdır.
* Windows kimlik doğrulaması kullanıyorsanız, Windows Kullanıcı/parola ayarlayın. SQL Server için, belirli bir kimliğe bürünme hesabıyla Windows kimlik doğrulaması yalnızca bellek içi veri modelleri için desteklenir.

Bulut veri kaynakları için:

* SQL kimlik doğrulaması kullanılıyorsa, kimliğe bürünme hizmet hesabı olmalıdır.


## <a name="oauth-credentials"></a>OAuth kimlik bilgileri

*Bellek içi* modu, Azure SQL veritabanı, Azure SYNAPSE, Dynamics 365 ve SharePoint listesi ile 1400 ve daha yüksek uyumluluk düzeyinde tablolu modeller için OAuth kimlik bilgilerini destekler. Geçerli belirteçler oluşturmak için Power Query kullanarak kimlik bilgilerini ayarlayın. Azure Analysis Services uzun süre çalışan yenileme işlemleri için zaman aşımlarını önlemek üzere OAuth veri kaynakları için belirteç yenilemeyi yönetir. 
> [!NOTE]
> Yönetilen belirteç yenilemesi, bir ağ geçidiyle erişilen veri kaynakları için desteklenmez. Örneğin, bir veya daha fazla karma sorgu veri kaynağına bir ağ geçidiyle erişilir ve/veya [Aspaas\alwaysusegateway](analysis-services-vnet-gateway.md) özelliği **true** olarak ayarlanır. 

OAuth kimlik bilgileriyle doğrudan sorgu modu desteklenmez.

## <a name="enable-oracle-managed-provider"></a>Oracle yönetilen sağlayıcısını etkinleştir

Bazı durumlarda, bir Oracle veri kaynağına yönelik DAX sorguları beklenmedik sonuçlar döndürebilir. Bu, veri kaynağı bağlantısı için kullanılan sağlayıcıdan kaynaklanıyor olabilir.

[Sağlayıcıları anlama](#understanding-providers) bölümünde açıklandığı gibi, tablo modelleri veri kaynaklarına *yapılandırılmış* bir veri kaynağı veya bir *sağlayıcı* veri kaynağı olarak bağlanır. Sağlayıcı veri kaynağı olarak belirtilen Oracle veri kaynağına sahip modeller için, belirtilen sağlayıcının .NET için Oracle Veri Sağlayıcısı (Oracle. DataAccess. Client) olduğundan emin olun. 

Oracle veri kaynağı yapılandırılmış bir veri kaynağı olarak belirtilmişse **Mdataengine\usemanagedoracleprovider** sunucu özelliğini etkinleştirin. Bu özelliğin ayarlanması, modelinizin .NET tarafından yönetilen sağlayıcı için önerilen Oracle Veri Sağlayıcısı kullanarak Oracle veri kaynağına bağlanmasını sağlar.
 
Oracle yönetilen sağlayıcısını etkinleştirmek için:

1. SQL Server Management Studio, sunucunuza bağlanın.
2. Aşağıdaki betiği içeren bir XMLA sorgusu oluşturun. **ServerName** öğesini tam sunucu adıyla değiştirip sorguyu yürütün.

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. Sunucuyu yeniden başlatın.


## <a name="next-steps"></a>Sonraki adımlar

* [Şirket içi ağ geçidi](analysis-services-gateway.md)
* [Sunucunuzu Yönetin](analysis-services-manage.md)
