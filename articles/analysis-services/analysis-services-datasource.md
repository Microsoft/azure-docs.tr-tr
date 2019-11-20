---
title: Azure Analysis Services desteklenen veri kaynakları | Microsoft Docs
description: Azure Analysis Services tablo 1200 ve daha yüksek veri modelleri için desteklenen veri kaynaklarını ve bağlayıcıları açıklar.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c92baf5c97597a0161f402cc458e90bb3e637d6c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170655"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services desteklenen veri kaynakları

Visual Studio 'da veri alma veya Içeri aktarma Sihirbazı 'nda gösterilen veri kaynakları ve bağlayıcılar hem Azure Analysis Services hem de SQL Server Analysis Services için gösterilmektedir. Ancak, gösterilen tüm veri kaynakları ve bağlayıcılar Azure Analysis Services desteklenmez. Bağlantı kurmak için kullanabileceğiniz veri kaynağı türleri model uyumluluk düzeyi, kullanılabilir veri bağlayıcıları, kimlik doğrulama türü, sağlayıcılar ve şirket içi veri ağ geçidi desteği gibi birçok faktöre bağlıdır. 

## <a name="azure-data-sources"></a>Azure veri kaynakları

|Veri kaynağı  |Bellek içi  |DirectQuery  |
|---------|---------|---------|
|Azure SQL veritabanı<sup>[2](#azsqlmanaged)</sup>     |   Yes      |    Yes      |
|Azure SQL Veri Ambarı     |   Yes      |   Yes       |
|Azure Blob depolama<sup>[1](#tab1400a)</sup>     |   Yes       |    Hayır      |
|Azure Tablo depolama<sup>[1](#tab1400a)</sup>    |   Yes       |    Hayır      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Yes        |  Hayır        |
|Azure Data Lake Store (Gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Yes       |    Hayır      |
|Azure HDInsight,<sup>[1](#tab1400a) .</sup>     |     Yes     |   Hayır       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Yes       |   Hayır       |
||||

yalnızca <a name="tab1400a">1</a> tablolu 1400 ve daha yüksek modeller.   
<a name="azsqlmanaged">2</a> -Azure SQL veritabanı yönetilen örneği desteklenir. Yönetilen örnek, Azure VNet 'te özel bir IP adresi ile çalıştığı için, örnekte ortak uç noktanın etkinleştirilmesi gerekir. Etkinleştirilmemişse, şirket içi veri ağ geçidi gerekir.    
<a name="databricks">3</a> -Spark bağlayıcısını kullanan Azure Databricks Şu anda desteklenmiyor.   
<a name="gen2">4</a> -ADLS 2. Bağlayıcısı Şu anda desteklenmiyor, ancak blob Storage bağlayıcısı bir ADLS 2. veri kaynağıyla birlikte kullanılabilir.


**Sağlayıcı**   
Azure veri kaynaklarına bağlanan bellek içi ve DirectQuery modelleri SQL Server için .NET Framework Veri Sağlayıcısı kullanır.

## <a name="other-data-sources"></a>Diğer veri kaynakları

Bir Azure Analysis Services sunucusundan şirket içi veri kaynaklarına bağlanmak için şirket içi ağ geçidi gerekir. Bir ağ geçidi kullanırken, 64 bit sağlayıcılar gereklidir.

### <a name="in-memory-and-directquery"></a>Bellek içi ve DirectQuery

|Veri kaynağı | Bellek içi sağlayıcı | DirectQuery sağlayıcısı |
|  --- | --- | --- |
| SQL Server |SQL Server MSOLEDBSQL için Microsoft OLE DB sürücüsü (önerilen), SQL Server Native Client 11,0, Veri Sağlayıcısı için .NET Framework SQL Server | SQL Server için .NET Framework Veri Sağlayıcısı |
| SQL Server veri ambarı |SQL Server MSOLEDBSQL için Microsoft OLE DB sürücüsü (önerilen), SQL Server Native Client 11,0, Veri Sağlayıcısı için .NET Framework SQL Server | SQL Server için .NET Framework Veri Sağlayıcısı |
| Oracle | Oracle için OLE DB sağlayıcı, .NET için Oracle Veri Sağlayıcısı |.NET için Oracle Veri Sağlayıcısı |
| Teradata |Teradata için OLE DB sağlayıcısı, .NET için Teradata Veri Sağlayıcısı |.NET için Teradata Veri Sağlayıcısı |
| | | |

### <a name="in-memory-only"></a>Yalnızca bellek içi

|Veri kaynağı  |  
|---------|
|Veritabanına erişin     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Analytics platform sistemi     |  
|CSV dosyası  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Excel çalışma kitabı     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Klasör<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (Beta) |
|JSON belgesi<sup>[1](#tab1400b)</sup>     |  
|İkili 1 ' den satırlar<sup>[](#tab1400b)</sup>     | 
|MySQL Veritabanı     | 
|OData akışı<sup>[1](#tab1400b)</sup>     |  
|ODBC sorgusu     | 
|OLE DB     |   
|PostgreSQL veritabanı<sup>[1](#tab1400b)</sup>    | 
|Salesforce nesneleri<sup>[1](#tab1400b)</sup> |  
|Salesforce raporları<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint listesi<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Sybase veritabanı     |  
|TXT dosyası  |
|XML tablosu<sup>[1](#tab1400b)</sup>    |  
||
 
yalnızca <a name="tab1400b">1</a> tablolu 1400 ve daha yüksek modeller.   
<a name="filesSP">2</a> -şirket içi SharePoint 'teki dosyalar desteklenmez.

## <a name="specifying-a-different-provider"></a>Farklı bir sağlayıcı belirtme

Azure Analysis Services veri modelleri, belirli veri kaynaklarına bağlanırken farklı veri sağlayıcıları gerektirebilir. Bazı durumlarda, SQL Server Native Client (SQLNCLI11) gibi yerel sağlayıcıları kullanarak veri kaynaklarına bağlanan tablo modelleri bir hata döndürebilir. MSOLEDBSQL dışında yerel sağlayıcılar kullanılıyorsa, hata iletisi görebilirsiniz: **' SQLNCLI 11.1 ' sağlayıcısı kayıtlı değil**. Ya da şirket içi veri kaynaklarına bağlanan bir DirectQuery modeliniz varsa ve yerel sağlayıcılar kullanıyorsanız, hata iletisi: **OLE DB satır kümesi oluşturma hatası görebilirsiniz. ' LIMIT ' yakınında yanlış sözdizimi**.

Şirket içi SQL Server Analysis Services tablolu modeli Azure Analysis Services ' e geçirirken sağlayıcının değiştirilmesi gerekebilir.

**Sağlayıcı belirtmek için**

1. Visual Studio > **tablosal model gezgini** > **veri kaynakları**' nda, bir veri kaynağı bağlantısına sağ tıklayın ve ardından **veri kaynağını Düzenle**' ye tıklayın.
2. **Bağlantıyı Düzenle**' de Gelişmiş ' e tıklayarak **İleri** Özellikler penceresini açın.
3. **Gelişmiş özellikleri ayarla** > **sağlayıcılar**' da, uygun sağlayıcıyı seçin.

## <a name="impersonation"></a>Kimliğe bürünme
Bazı durumlarda, farklı bir kimliğe bürünme hesabı belirtmek gerekli olabilir. Kimliğe bürünme hesabı, Visual Studio veya SSMS 'de belirtilebilir.

Şirket içi veri kaynakları için:

* SQL kimlik doğrulaması kullanılıyorsa, kimliğe bürünme hizmet hesabı olmalıdır.
* Windows kimlik doğrulaması kullanıyorsanız, Windows Kullanıcı/parola ayarlayın. SQL Server için, belirli bir kimliğe bürünme hesabıyla Windows kimlik doğrulaması yalnızca bellek içi veri modelleri için desteklenir.

Bulut veri kaynakları için:

* SQL kimlik doğrulaması kullanılıyorsa, kimliğe bürünme hizmet hesabı olmalıdır.

## <a name="oauth-credentials"></a>OAuth kimlik bilgileri

1400 ve üzeri uyumluluk düzeyindeki tablo modelleriyle ilgili olarak, Azure SQL veritabanı, Azure SQL veri ambarı, Dynamics 365 ve SharePoint listesi OAuth kimlik bilgilerini destekler. Azure Analysis Services uzun süre çalışan yenileme işlemleri için zaman aşımlarını önlemek üzere OAuth veri kaynakları için belirteç yenilemeyi yönetir. Geçerli belirteçler oluşturmak için SSMS kullanarak kimlik bilgilerini ayarlayın.

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi ağ geçidi](analysis-services-gateway.md)   
[Sunucunuzu Yönetin](analysis-services-manage.md)   

