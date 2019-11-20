---
title: Azure Veri Kataloğu 'nda desteklenen veri kaynakları
description: Bu makalede, Azure Veri Kataloğu için şu anda desteklenen veri kaynaklarının belirtimleri listelenmektedir.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 8f148bfe08d5f7933857eb46cf0d13d4582da108
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950272"
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Azure Veri Kataloğu 'nda desteklenen veri kaynakları

Meta verileri ortak bir API veya bir tıklama-bir kayıt aracı kullanarak ya da doğrudan Azure Veri Kataloğu web portalına el ile girerek yayımlayabilirsiniz. Aşağıdaki tablo, bugün Katalog tarafından desteklenen tüm veri kaynaklarını ve her biri için yayımlama yeteneklerini özetler. Ayrıca, her bir veri kaynağının Portal "açık" deneyimimizden başlatabileceği dış veri araçları da listelenmiştir. İkinci tablo, her veri kaynağı bağlantısı özelliğinin daha teknik bir belirtimini içerir.

## <a name="list-of-supported-data-sources"></a>Desteklenen veri kaynaklarının listesi

<table>
    <tr>
       <td><b>Veri kaynağı nesnesi</b></td>
       <td><b>API</b></td>
       <td><b>El ile giriş</b></td>
       <td><b>Kayıt Aracı</b></td>
       <td><b>Açık Araçlar</b></td>
       <td><b>Notlar</b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store Directory (yalnızca Gen 1)</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store dosyası (yalnızca Gen 1)</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Azure Blob depolama</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Azure depolama dizini</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Azure depolama tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
      </td>
      <td>
      </td>
    </tr>
    <tr>
      <td>HDFS dizini</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>HDFS dosyası</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Hive tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>Hive görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>MySQL tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>MySQL görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Oracle Database tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Oracle Database görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>Diğer (genel varlık)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Azure SQL veri ambarı tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop SQL Server veri araçları</td>
      <td></td>
    </tr>
    <tr>
      <td>Azure SQL veri ambarı görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop SQL Server veri araçları</td>
      <td></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services boyutu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td>SQL Server 2008 ve sonraki sürümler.</td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td>SQL Server 2008 ve sonraki sürümler.</td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services ölçümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td>SQL Server 2008 ve sonraki sürümler.</td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop</td>
      <td>SQL Server 2008 ve sonraki sürümler.</td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services raporu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Browser</td>
      <td>Yalnızca yerel mod sunucuları. SharePoint modu desteklenmez. Yalnızca SQL Server 2008 ve sonraki sürümler</td>
    </tr>
    <tr>
      <td>SQL Server tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop SQL Server veri araçları</td>
      <td>SQL Server 2008 ve sonraki sürümler.</td>
    </tr>
    <tr>
      <td>SQL Server görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel, Power BI Desktop SQL Server veri araçları</td>
      <td>SQL Server 2008 ve sonraki sürümler.</td>
    </tr>
    <tr>
      <td>Teradata tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>Teradata görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Excel</td>
      <td></td>
    </tr>
    <tr>
      <td>SAP Business Warehouse</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td>Yalnızca Ingilizce olarak kullanılabilir. Ölçüler desteklenmez.</td>
    </tr>
    <tr>
      <td>SAP HANA görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>Power BI Desktop</td>
      <td></td>
    </tr>
    <tr>
      <td>DB2 tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>DB2 görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Dosya sistemi dosyası</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>FTP dizini</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>FTP dosyası</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>HTTP raporu</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>HTTP uç noktası</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>HTTP dosyası</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>OData varlık kümesi</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>OData işlevi</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>PostgreSQL tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>PostgreSQL görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>SAP HANA görünümü</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td> Salesforce nesnesi</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>SharePoint listesi </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Azure Cosmos DB koleksiyonu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td>Yalnızca Azure DocumentDB ve Azure Cosmos DB 'deki SQL API koleksiyonlarından eski koleksiyonlar uyumludur. Daha yeni Cosmos DB API 'Leri henüz desteklenmiyor. Veri kaynağı listesinde Azure DocumentDB ' yi seçin.</td>
    </tr>
    <tr>
      <td>Genel ODBC tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Genel ODBC görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Cassandra tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td>Genel bir ODBC varlığı olarak yayımlama</td>
    </tr>
    <tr>
      <td>Cassandra görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td>Genel bir ODBC varlığı olarak yayımlama</td>
    </tr>
    <tr>
      <td>Sybase tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>Sybase görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>MongoDB tablosu</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td>Genel bir ODBC varlığı olarak yayımlama</td>
    </tr>
    <tr>
      <td>MongoDB görünümü</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td>Genel bir ODBC varlığı olarak yayımlama</td>
    </tr>
</table>

Desteklenen belirli bir veri kaynağını görmek isterseniz, [Azure geri bildirim forumlarında veri kataloğuna](https://feedback.azure.com/forums/906052-data-catalog)giderek bunu önerin (veya zaten önerildiyse desteğiniz sesinizi).


## <a name="data-source-reference-specification"></a>Veri kaynağı başvuru belirtimi
> [!NOTE]
> Aşağıdaki tabloda yer alan **DSL yapısı** sütunu, yalnızca Azure Veri Kataloğu tarafından kullanılan "adres" özellik paketi için bağlantı özelliklerini listeler. Diğer bir deyişle, "adres" özellik paketi, Azure Veri Kataloğu 'nun devam ettiği, ancak kullanmayan veri kaynağının diğer bağlantı özelliklerini içerebilir.

<table>
    <tr>
       <td><b>Kaynak türü</b></td>
       <td><b>Varlık türü</b></td>
       <td><b>Nesne türleri</b></td>
       <td><b>DSL yapısı<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Kapsayıcı</td>
      <td>Data Lake</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Table</td>
      <td>Directory, file</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Container</td>
      <td>Container</td>
      <td>
        
            Protocol: azure-blobs
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; container
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Table</td>
      <td>Blob, directory</td>
      <td>
        
            Protocol: azure-blobs
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; container
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Container</td>
      <td>Container</td>
      <td>
        
            Protocol: azure-tables
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
        
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        
            Protocol: azure-tables
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name
        
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Container</td>
      <td>Site</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: db2
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: db2
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
        
      </td>
    </tr>
    <tr>
      <td>File system</td>
      <td>Table</td>
      <td>File</td>
      <td>
        
            Protocol: file
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path
        
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Table</td>
      <td>Directory, file</td>
      <td>
        
            Protocol: ftp
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Container</td>
      <td>Cluster</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Table</td>
      <td>Directory, file</td>
      <td>
        
            Protocol: webhdfs
            <br>Authentication: {basic, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: hive
            <br>Authentication: {HDInsight, basic, username, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>connectionProperties:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2}
        
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: hive
            <br>Authentication: {HDInsight, basic, username, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>connectionProperties:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2}
        
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Container</td>
      <td>Site</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Table</td>
      <td>Endpoint, file</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: mysql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: mysql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Container</td>
      <td>Entity container</td>
      <td>
        
            Protocol: odata
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Table</td>
      <td>Entity set, function</td>
      <td>
        
            Protocol: odata
            <br>Authentication: {none, basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; resource
        
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: oracle
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: oracle
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: postgresql
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: postgresql
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>Power BI Desktop</td>
      <td>Container</td>
      <td>Site</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Power BI Desktop</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        
            Protocol: http
            <br>Authentication: {none, basic, windows, oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Table</td>
      <td>Data mashup</td>
      <td>
        
            Protocol: power-query
            <br>Authentication: {oauth}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Table</td>
      <td>Object</td>
      <td>
        
            Protocol: salesforce-com
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; class
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName
        
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Container</td>
      <td>Server</td>
      <td>
        
            Protocol: sap-hana-sql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
        
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Table</td>
      <td>View</td>
      <td>
        
            Protocol: sap-hana-sql
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Table</td>
      <td>List</td>
      <td>
        
            Protocol: sharepoint-list
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Command</td>
      <td>Stored procedure</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Table-valued function</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: tds
          <br>Authentication: {protocol, windows}
          <br>Address:
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Command</td>
      <td>Stored procedure</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Table-valued function</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: tds
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Container</td>
      <td>Model</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Table</td>
      <td>Dimension</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimension}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Container</td>
      <td>Model</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        
            Protocol: analysis-services
            <br>Authentication: {windows, basic, anonymous, none}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Table}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Container</td>
      <td>Server</td>
      <td>
        
            Protocol: reporting-services
            <br>Authentication: {windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010}
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Report</td>
      <td>Report</td>
      <td>
        
            Protocol: reporting-services
            <br>Authentication: {windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010}
        
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: teradata
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Table</td>
      <td>Table, view</td>
      <td>
        
            Protocol: teradata
            <br>Authentication: {protocol, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Container</td>
      <td>Model</td>
      <td>
        <font size="2">
          Protocol: mssql-mds
          <br>Authentication: {windows}
          <br>Address:
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version
        
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Table</td>
      <td>Entity</td>
      <td>
        <font size="2">
          Protocol: mssql-mds
          <br>Authentication: {windows}
          <br>Address:
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version
          <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; entity
        
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: document-db
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Collection</td>
      <td>Collection</td>
      <td>
        
            Protocol: document-db
            <br>Authentication: {azure-access-key}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; collection
        
      </td>
    </tr>
    <tr>
      <td>Generic ODBC</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            Protocol: odbc
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; options
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Generic ODBC</td>
      <td>Table</td>
      <td>Table, View</td>
      <td>
        
            Protocol: odbc
            <br>Authentication: {basic, windows}
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; options
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
        
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Container</td>
      <td>Database</td>
      <td>
        
            protocol: sybase
            <br>authentication: {basic, windows}
            <br>address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
        
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Table</td>
      <td>Table, View</td>
      <td>
        
            protocol: sybase
            <br>authentication: {basic, windows}
            <br>address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object
        
      </td>
    </tr>
    <tr>
      <td>Other (none of the above)</td>
      <td>&#10033;</td>
      <td>&#10033;</td>
      <td>
        
            Protocol: generic-asset
            <br>Address:
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId
        
      </td>
    </tr>
</table>
