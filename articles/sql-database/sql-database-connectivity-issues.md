---
title: Geçici hatalarla çalışma
description: Azure SQL veritabanı 'nda bir SQL bağlantı hatasını veya geçici bir hatayı nasıl giderebileceğinizi, tanıleyeceğinizi ve önleyeceğinizi öğrenin.
keywords: SQL bağlantısı, bağlantı dizesi, bağlantı sorunları, geçici hata, bağlantı hatası
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 11/14/2019
ms.openlocfilehash: c25fa3f378c1e5a0f8bc26e4fb8c6f4ec752b43c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082489"
---
# <a name="working-with-sql-database-connection-issues-and-transient-errors"></a>SQL veritabanı bağlantı sorunlarıyla ve geçici hatalarla çalışma

Bu makalede, bağlantı hatalarının ve istemci uygulamanızın Azure SQL veritabanı ile etkileşime geçtiğinde karşılaştığı geçici hataların nasıl engelleneceği, giderileceği, tanılanacağı ve azaltılacağını açıklanmaktadır. Yeniden deneme mantığını yapılandırmayı, bağlantı dizesini derlemeyi ve diğer bağlantı ayarlarını ayarlamayı öğrenin.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Geçici hatalar (geçici hatalar)

Geçici hata olarak da bilinen geçici bir hata, kendisini en kısa sürede çözümleyen bir hataya sahiptir. Geçici hataların geçici bir nedeni, Azure sisteminin çeşitli iş yüklerinin daha iyi yük dengelenmesi için donanım kaynaklarını hızlı bir şekilde kaydırır. Bu yeniden yapılandırma olaylarının çoğu 60 saniyeden az bir süre içinde tamamlanır. Bu yeniden yapılandırma sırasında zaman dilimi sırasında SQL veritabanı 'na bağlantı sorunlarıyla karşılaşabilirsiniz. SQL veritabanına bağlanan uygulamalar bu geçici hataların beklenildiği için oluşturulmalıdır. Bunları işlemek için, kullanıcıların uygulama hataları olarak kullanıcılara sunulmasını sağlamak yerine, yeniden deneme mantığını uygulayın.

İstemci programınız ADO.NET kullanıyorsa, programınız **SqlException**throw tarafından geçici hata ile bildirilir. 

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Bağlantı ve komut karşılaştırması

SQL bağlantısını yeniden deneyin veya aşağıdakilere bağlı olarak yeniden oluşturun:

- **Bağlantı deneme sırasında geçici bir hata oluşur**

Birkaç saniyelik bir gecikmeden sonra bağlantıyı yeniden deneyin.

- **SQL sorgu komutu sırasında geçici bir hata oluşuyor**

Komutu hemen yeniden denemeyin. Bunun yerine, bir gecikmeden sonra bağlantıyı tekrar oluşturun. Sonra komutu yeniden deneyin.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Geçici hatalar için yeniden deneme mantığı

Bazen geçici bir hatayla karşılaşan istemci programları, yeniden deneme mantığı içerdiğinde daha sağlamdır. Programınız üçüncü taraf ara yazılım aracılığıyla SQL veritabanıyla iletişim kurduğunda, satıcıdan geçici hatalar için yeniden deneme mantığı içerip içermediğini öğrenin.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Yeniden deneme ilkeleri

- Hata geçicidir, bir bağlantıyı açmayı yeniden deneyin.
- Geçici bir hatayla başarısız olan bir SQL `SELECT` ifadesini doğrudan yeniden denemeyin. Bunun yerine, yeni bir bağlantı oluşturun ve `SELECT`yeniden deneyin.
- Bir SQL `UPDATE` deyimleri geçici bir hata ile başarısız olduğunda, GÜNCELLEŞTIRMEYI yeniden denemeden önce yeni bir bağlantı kurun. Yeniden deneme mantığı, tüm veritabanı işleminin bittiğini veya işlemin tamamının geri alındığından emin olmalıdır.

### <a name="other-considerations-for-retry"></a>Yeniden denemeye yönelik diğer noktalar

- Çalışma saatlerinden sonra otomatik olarak başlayan ve sabah, yeniden deneme girişimleri arasında uzun zaman aralıklarıyla çok hasta olacak şekilde sona ereceği bir Batch programı.
- Bir kullanıcı arabirimi programı, insanın çok uzun süre sonra izin vermesini sağlayacak şekilde hesaba katmalıdır. Bu ilke sistemi isteklerle birlikte taşabileceğinden çözüm birkaç saniyede bir yeniden denenmemelidir.

### <a name="interval-increase-between-retries"></a>Yeniden denemeler arasındaki Aralık artışı

İlk yeniden denemeden önce 5 saniye beklemeniz önerilir. 5 saniyelik riskden daha kısa bir gecikme sonrasında bulut hizmeti 'nin yeniden denenmesi. Sonraki her yeniden deneme için, gecikme en fazla 60 saniyeye kadar üstel olarak artar.

ADO.NET kullanan istemciler için engelleme süresi hakkında bir tartışma için bkz. [SQL Server bağlantı havuzu (ADO.net)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

Ayrıca, programın kendiliğinden sonlandırmadan önce en fazla yeniden deneme sayısını ayarlamak isteyebilirsiniz.

### <a name="code-samples-with-retry-logic"></a>Yeniden deneme mantığı ile kod örnekleri

Yeniden deneme mantığına sahip kod örnekleri şurada bulunabilir:

- [Dayanıklı bağlantısı 'i ADO.NET ile SQL 'e bağlama][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [PHP ile dayanıklı bağlantısı 'i SQL 'e bağlama][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Yeniden deneme mantığınızı test edin

Yeniden deneme mantığınızı test etmek için, programınız çalışmaya devam ederken düzeltilabilmeyen bir hata benzetimi yapmanız veya hataya neden olmanız gerekir.

#### <a name="test-by-disconnecting-from-the-network"></a>Ağ bağlantısını keserek test edin

Yeniden deneme mantığınızı test etmenin bir yolu, program çalışırken istemci bilgisayarınızın ağ bağlantısını kesmenin bir yoludur. Hata:

- **SqlException. Number** = 11001
- İleti: "böyle bir konak bilinmiyor"

İlk yeniden deneme denemesinin bir parçası olarak, istemci bilgisayarınızı ağa yeniden bağlayıp bağlanmayı deneyebilirsiniz.

Bu testi pratik hale getirmek için, programınızı çalıştırmadan önce bilgisayarınızı ağdan çıkarın. Sonra programınız, programın şunları yapmasına neden olan bir çalışma zamanı parametresini tanır:

- Geçici olarak göz önünde bulundurulması gereken hata listesine 11001 ekleyin.
- İlk bağlantısını her zamanki gibi deneyin.
- Hata yakalandıktan sonra listeden 11001 'yi kaldırın.
- Kullanıcıya bilgisayarı ağa takmasını bildiren bir ileti görüntüler.
- **Console. ReadLine** yöntemini ya da ok düğmesi olan bir iletişim kutusunu kullanarak daha fazla yürütmeyi duraklatın. Bilgisayar ağa takıldıktan sonra Kullanıcı ENTER tuşuna basar.
- Bağlanmayı yeniden deneyin, başarı bekleniyor.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Bağlanırken, veritabanı adını yanlış yazarak test edin

Programınız, ilk bağlantı denemesinden önce Kullanıcı adını kasıtlı olarak yanlış şekilde hatalı yazalabilir. Hata:

- **SqlException. Number** = 18456
- İleti: "' WRONG_MyUserName ' kullanıcısı için oturum açma başarısız."

İlk yeniden deneme denemesinin bir parçası olarak, programınız yanlış yazımı düzeltip bağlanmayı deneyebilir.

Bu testi pratik hale getirmek için programınız programın şunları yapmasına neden olan bir çalışma zamanı parametresini tanır:

- Geçici olarak göz önünde bulundurulması gereken hata listesine 18456 ekleyin.
- Kullanıcı adına ' WRONG_ ' öğesini seyrek olarak ekleyin.
- Hata yakalandıktan sonra listeden 18456 'yi kaldırın.
- Kullanıcı adından ' WRONG_ ' öğesini kaldırın.
- Bağlanmayı yeniden deneyin, başarı bekleniyor.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Bağlantı yeniden deneme için .NET SqlConnection parametreleri

İstemci programınız **System. Data. SqlClient. SqlConnection**.NET Framework SıNıFıNı kullanarak SQL veritabanı 'na bağlanırsa, bağlantı yeniden deneme özelliğini kullanabilmeniz için .NET 4.6.1 veya üstünü (veya .NET Core) kullanın. Özelliği hakkında daha fazla bilgi için [Bu Web sayfasına](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection)bakın.

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

**SqlConnection** nesneniz için [bağlantı dizesi](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) oluşturduğunuzda, değerleri aşağıdaki parametreler arasında koordine edin:

- **ConnectRetryCount**:&nbsp;&nbsp;varsayılan değer 1 ' dir. Aralık 0 ile 255 arasındadır.
- **ConnectRetryInterval**:&nbsp;&nbsp;varsayılan değer 10 saniyedir. Aralık 1 ile 60 arasındadır.
- **Bağlantı zaman aşımı**:&nbsp;&nbsp;varsayılan değer 15 saniyedir. Aralık 0 ile 2147483647 arasındadır.

Özellikle, seçtiğiniz değerler aşağıdaki eşitliği doğru hale getirir: bağlantı zaman aşımı = ConnectRetryCount * Connectionretryınterval

Örneğin, sayı 3 ' e eşitse ve Aralık 10 saniyeye eşitse, yalnızca 29 saniyelik bir zaman aşımı sisteme, üçüncü ve son yeniden denemeye bağlanmayı yeterli zaman vermez: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Bağlantı ve komut karşılaştırması

**ConnectRetryCount** ve **ConnectRetryInterval** parametreleri, **SqlConnection** nesnenizin, programınız için denetim döndüren veya bothering olmadan Connect işlemini yeniden denemesini sağlar. Yeniden denemeler aşağıdaki durumlarda oluşabilir:

- mySqlConnection. Open Yöntem çağrısı
- mySqlConnection. Execute yöntemi çağrısı

Bir alt tcellik vardır. *Sorgunuz* yürütülürken geçici bir hata oluşursa, **SqlConnection** nesneniz bağlantı işlemini yeniden denemez. Sorgunuzu kesinlikle yeniden denemez. Ancak, **SqlConnection** yürütme için sorgunuzu göndermeden önce bağlantıyı çok hızlı denetler. Hızlı denetim bir bağlantı sorunu algılarsa, **SqlConnection** bağlantı işlemini yeniden dener. Yeniden deneme başarılı olursa, sorgunuz yürütme için gönderilir.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount, uygulama yeniden deneme mantığı ile birleştirilmelidir

Uygulamanızın sağlam özel yeniden deneme mantığı olduğunu varsayalım. Bağlantı işlemini dört kez yeniden deneyebilir. Bağlantı dizenizi **ConnectRetryInterval** ve **ConnectRetryCount** = 3 ' ü eklerseniz, yeniden deneme sayısını 4 * 3 = 12 yeniden deneme olacak şekilde artırabilirsiniz. Çok sayıda yeniden deneme gibi bir işlem yapabilirsiniz.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>SQL veritabanı bağlantıları

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Bağlantı: bağlantı dizesi

SQL veritabanına bağlanmak için gerekli olan bağlantı dizesi, SQL Server bağlanmak için kullanılan dizeden biraz farklıdır. Veritabanınızın bağlantı dizesini [Azure Portal](https://portal.azure.com/)kopyalayabilirsiniz.

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Bağlantı: IP adresi

SQL veritabanı sunucusunu, istemci programınızı barındıran bilgisayarın IP adresinden gelen iletişimi kabul edecek şekilde yapılandırmanız gerekir. Bu yapılandırmayı ayarlamak için [Azure Portal](https://portal.azure.com/)aracılığıyla güvenlik duvarı ayarlarını düzenleyin.

IP adresini yapılandırmayı unutursanız, programınız gerekli IP adresini belirten kullanışlı bir hata iletisiyle başarısız olur.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Daha fazla bilgi için bkz. [SQL veritabanında güvenlik duvarı ayarlarını yapılandırma](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Bağlantı: bağlantı noktaları

Genellikle, istemci programınızı barındıran bilgisayardaki giden iletişim için yalnızca bağlantı noktası 1433 ' ün açık olduğundan emin olmanız gerekir.

Örneğin, istemci programınız bir Windows bilgisayarında barındırılıyorsa, 1433 numaralı bağlantı noktasını açmak için konakta Windows Güvenlik Duvarı 'nı kullanabilirsiniz.

1. Denetim Masası 'Nı açın.
2. **Tüm Denetim Masası öğeleri** > **Windows Güvenlik Duvarı** > **gelişmiş ayarlar** > **giden kuralları** > **Eylemler** > **Yeni kural**' ı seçin.

İstemci programınız bir Azure sanal makinesinde (VM) barındırılıyorsa, [ADO.NET 4,5 ve SQL veritabanı için 1433 ' den sonraki bağlantı noktalarını](sql-database-develop-direct-route-ports-adonet-v12.md)okuyun.

Bağlantı noktalarının ve IP adreslerinin yapılandırması hakkında arka plan bilgileri için bkz. [Azure SQL veritabanı güvenlik duvarı](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Bağlantı: ADO.NET 4.6.2 veya üzeri

Programınız, SQL veritabanına bağlanmak için **System. Data. SqlClient. SqlConnection** gibi ADO.NET sınıfları kullanıyorsa, .NET Framework sürüm 4.6.2 veya sonraki bir sürümünü kullanmanızı öneririz.

#### <a name="starting-with-adonet-462"></a>ADO.NET 4.6.2 'den başlayarak

- Azure SQL veritabanları için hemen yeniden denenmeye yönelik bağlantı açma denemesi, böylece bulut özellikli uygulamaların performansını geliştirir.

#### <a name="starting-with-adonet-461"></a>ADO.NET 4.6.1 'den başlayarak

- SQL veritabanı için, **SqlConnection. Open** metodunu kullanarak bir bağlantı açtığınızda güvenilirlik artırıldı. **Open** yöntemi artık, bağlantı zaman aşımı süresi içinde belirli hatalar için geçici hatalara yanıt olarak en iyi çaba yeniden deneme mekanizmalarını içerir.
- Bağlantı havuzu oluşturma desteklenir ve bu, programınıza verdiği bağlantı nesnesinin çalıştığını etkili bir doğrulama içerir.

Bağlantı havuzundan bir bağlantı nesnesi kullandığınızda, programınızın kullanımda olmadığı durumlarda bağlantıyı geçici olarak kapatması önerilir. Bir bağlantıyı yeniden açmak pahalı değildir, ancak yeni bir bağlantı oluşturalım.

ADO.NET 4,0 veya önceki bir sürümünü kullanıyorsanız en son ADO.NET sürümüne yükseltmenizi öneririz. Ağustos 2018 itibariyle [ADO.NET 4.6.2 indirebilirsiniz](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Tanılama

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Tanılama: yardımcı programların bağlanıp bağlanamayacağını test edin

Programınız SQL veritabanı 'na bağlanamıyorsa, tek bir tanılama seçeneği, bir yardımcı programı ile bağlanmayı denemenize yardımcı olur. İdeal olarak, yardımcı program, programınızın kullandığı kitaplığı kullanarak bağlanır.

Herhangi bir Windows bilgisayarında, bu yardımcı programları deneyebilirsiniz:

- ADO.NET kullanarak bağlanan SQL Server Management Studio (SSMS. exe)
- [ODBC](https://msdn.microsoft.com/library/jj730308.aspx) kullanarak bağlanan `sqlcmd.exe`

Programınız bağlandıktan sonra, kısa bir SQL SELECT sorgusunun çalışıp çalışmadığını test edin.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Tanılama: açık bağlantı noktalarını denetleyin

Bağlantı noktası sorunları nedeniyle bağlantı girişimlerinin başarısız olduğunu kuşkulanıyorsanız, bilgisayarınızda bağlantı noktası yapılandırmalarına rapor veren bir yardımcı program çalıştırabilirsiniz.

Linux 'ta aşağıdaki yardımcı programlar yararlı olabilir:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: örnek değeri IP adresiniz olacak şekilde değiştirin.

Windows 'da, [PortQry. exe](https://www.microsoft.com/download/details.aspx?id=17148) yardımcı programı yararlı olabilir. Aşağıda, bir SQL veritabanı sunucusunda bağlantı noktası durumu sorgulanan ve bir dizüstü bilgisayarda çalıştırılan örnek bir yürütme verilmiştir:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Tanılama: hatalarınızı günlüğe kaydedin

Aralıklı bir sorun bazen gün veya hafta içinde genel bir düzenin algılanması ile en iyi şekilde tanılanıyor.

İstemciniz, karşılaştığı tüm hataları günlüğe kaydederek bir tanılamada yardımcı olabilir. Günlük girişlerini SQL veritabanı 'nın kendisini kendi kendine kaydettiği hata verileriyle ilişkilendirebileceksiniz.

Enterprise Library 6 (EntLib60), günlüğe kaydetmeye yardımcı olacak .NET yönetilen sınıfları sağlar. Daha fazla bilgi için bkz. [5-bir günlüğün altına düşme kadar kolay: günlük uygulama bloğunu kullanın](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Tanılama: hatalar için sistem günlüklerini Inceleyin

Hata günlüklerini ve diğer bilgileri sorgulayan bazı Transact-SQL SELECT deyimleri aşağıda verilmiştir.

| Günlük sorgusu | Açıklama |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[Sys. event_log](https://msdn.microsoft.com/library/dn270018.aspx) görünümü, geçici hatalara veya bağlantı hatalarına neden olabilecek bazı olaylar hakkında bilgi sunar.<br/><br/>İdeal olarak, **start_time** veya **end_time** değerlerini, istemci programınızın sorun yaşadığında ilgili bilgilerle ilişkilendirebiliriz.<br/><br/>Bu sorguyu çalıştırmak için *ana* veritabanına bağlanmanız gerekir. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[Sys. database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) görünümü, ek Tanılamalar için toplanan olay türleri sayısını sunar.<br/><br/>Bu sorguyu çalıştırmak için *ana* veritabanına bağlanmanız gerekir. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Tanılama: SQL veritabanı günlüğünde sorun olaylarını arama

SQL veritabanı günlüğündeki sorun olaylarıyla ilgili girdiler için arama yapabilirsiniz. *Ana* veritabanında aşağıdaki Transact-SQL SELECT ifadesini deneyin:

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Sys. fn_xe_telemetry_blob_target_read_file 'den döndürülen birkaç satır

Aşağıdaki örnekte, döndürülen bir satırın nasıl görünebileceğini gösterilmektedir. Gösterilen null değerler genellikle diğer satırlarda null değildir.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Kuruluş kitaplığı 6

Enterprise Library 6 (EntLib60), biri SQL veritabanı hizmeti olan bulut hizmetleri için güçlü istemcileri uygulamanıza yardımcı olan bir .NET sınıfları çerçevesidir. EntLib60 'in yardımcı olduğu her bir alana adanmış konuları bulmak için bkz. [Enterprise Library 6-nisan 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Geçici hataları işlemek için yeniden deneme mantığı, EntLib60 'in yardımcı olabilecek tek bir alandır. Daha fazla bilgi için bkz. [4-Perseverance, tüm üç aylık gizlilik: geçici hata Işleme uygulama bloğunu kullanma](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> EntLib60 için kaynak kodu, [Indirme merkezinden](https://go.microsoft.com/fwlink/p/?LinkID=290898)genel karşıdan yükleme için kullanılabilir. Microsoft 'un EntLib 'e daha fazla özellik güncelleştirmesi veya bakım güncelleştirmesi yapma planı yoktur.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Geçici hatalar için EntLib60 sınıfları ve yeniden dene

Aşağıdaki EntLib60 sınıfları, yeniden deneme mantığı için özellikle kullanışlıdır. Bu sınıfların tümü, **Microsoft. Yöntemler. EnterpriseLibrary. TransientFaultHandling**ad alanı altında veya altında bulunur.

**Microsoft. Yöntemler. EnterpriseLibrary. TransientFaultHandling**ad alanında:

- **Retrypolicy** sınıfı
  - **ExecuteAction** yöntemi
- **Üs Albackoff** sınıfı
- **SqlDatabaseTransientErrorDetectionStrategy** sınıfı
- **Reliablesqlconnection** sınıfı
  - **ExecuteCommand** yöntemi

**Microsoft. Yöntemler. EnterpriseLibrary. TransientFaultHandling. TestSupport**ad alanında:

- **AlwaysTransientErrorDetectionStrategy** sınıfı
- **NeverTransientErrorDetectionStrategy** sınıfı

EntLib60 hakkındaki bilgilerin bazı bağlantıları aşağıda verilmiştir:

- Ücretsiz kitap indirme: [Geliştirici Kılavuzu Microsoft Kurumsal kitaplığı, 2. sürümde](https://www.microsoft.com/download/details.aspx?id=41145).
- En iyi uygulamalar: [yeniden deneme genel Kılavuzu](../best-practices-retry-general.md) , yeniden deneme mantığı hakkında harika bir tartışmaya sahiptir.
- NuGet indirme: [Kurumsal kitaplık-geçici hata Işleme uygulama bloğu 6,0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: günlük bloğu

- Günlüğe kaydetme bloğu, aşağıdakileri yapmak için kullanabileceğiniz, son derece esnek ve yapılandırılabilir bir çözümdür:
  - Günlük iletilerini çok çeşitli konumlarda oluşturun ve saklayın.
  - İletileri kategorilere ayırın ve filtreleyin.
  - Hata ayıklama ve izleme için yararlı olan bağlamsal bilgileri, ayrıca denetim ve genel günlük gereksinimlerini toplayın.
- Günlüğe kaydetme bloğu, hedef günlük deposunun konumu ve türü ne olursa olsun, uygulama kodunun tutarlı olması için günlük hedefinden günlük oluşturma işlevini soyutlar.

Daha fazla bilgi için bkz. [5-bir günlüğün altına düşme kadar kolay: günlük uygulama bloğunu kullanın](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 Isgeçici yöntem kaynak kodu

Next, **SqlDatabaseTransientErrorDetectionStrategy** sınıfından C# **ısgeçici** yönteminin kaynak kodudur. Kaynak kodu, 2013 Nisan itibariyle ne kadar geçici ve yeniden denemeye göz önünde bulundurduğuna ilişkin hataları açıklığa kavuşturulur.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Sonraki adımlar

- Diğer yaygın SQL veritabanı bağlantı sorunlarını giderme hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı bağlantı sorunlarını giderme](sql-database-troubleshoot-common-connection-issues.md).
- [SQL veritabanı ve SQL Server için bağlantı kitaplıkları](sql-database-libraries.md)
- [SQL Server bağlantı havuzu (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- Yeniden deneme, yalnızca bir şey için yeniden deneme davranışı ekleme görevini basitleştirmek için [ Python 'Da yazılmış bir Apache 2,0 lisanslı genel amaçlı yeniden deneme kitaplığıdır](https://pypi.python.org/pypi/retrying) .

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
