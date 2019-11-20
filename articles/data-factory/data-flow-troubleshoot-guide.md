---
title: Azure Data Factory veri akışı sorunlarını giderme
description: Azure Data Factory 'de veri akışı sorunlarını giderme hakkında bilgi edinin.
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 1b2309ec71cb3d43f4e5a39b80db593ab201c614
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721354"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Azure Data Factory veri akışı sorunlarını giderme

Bu makalede Azure Data Factory veri akışları için genel sorun giderme yöntemleri incelenmektedir.

## <a name="common-errors-and-messages"></a>Sık karşılaşılan hatalar ve mesajlar

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Hata iletisi: DF-SYS-01: gölgeli. databricks. org. Apache. Hadoop. FS. Azure. AzureException: com. Microsoft. Azure. Storage. StorageException: belirtilen kapsayıcı yok.

- **Belirtiler**: kapsayıcı mevcut olmadığından veri önizleme, hata ayıklama ve işlem hattı veri akışı yürütme başarısız oluyor

- **Neden**: veri kümesi depolamada bulunmayan bir kapsayıcı içerdiğinde

- **Çözüm**: veri kümenize başvurduğunuz kapsayıcının var olduğundan emin olun

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Hata iletisi: DF-SYS-01: Java. lang. AssertionError: assertion başarısız oldu: çakışan dizin yapıları algılandı. Şüpheli yollar

- **Belirtiler**: Parquet dosyaları ile kaynak dönüşümünde joker karakterler kullanılırken

- **Neden**: yanlış veya geçersiz joker karakter sözdizimi

- **Çözüm**: kaynak dönüştürme seçeneklerinizde kullandığınız joker karakter söz dizimini denetleyin

### <a name="error-message-df-src-002-container-container-name-is-required"></a>Hata iletisi: DF-SRC-002: ' Container ' (kapsayıcı adı) gerekiyor

- **Belirtiler**: kapsayıcı mevcut olmadığından veri önizleme, hata ayıklama ve işlem hattı veri akışı yürütme başarısız oluyor

- **Neden**: veri kümesi depolamada bulunmayan bir kapsayıcı içerdiğinde

- **Çözüm**: veri kümenize başvurduğunuz kapsayıcının var olduğundan emin olun

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Hata iletisi: DF-UNı-001: PrimaryKeyValue uyumsuz türler IntegerType ve StringType

- **Belirtiler**: kapsayıcı mevcut olmadığından veri önizleme, hata ayıklama ve işlem hattı veri akışı yürütme başarısız oluyor

- **Neden**: veritabanı evlerinde yanlış birincil anahtar türü eklemeye çalışırken gerçekleşir

- **Çözüm**: veri akışınızda birincil anahtar için kullandığınız sütunu hedef veritabanınızın veri türüyle eşleşecek şekilde dönüştürmek için türetilmiş bir sütun kullanın

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Hata iletisi: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: Ana bilgisayar xxxxx.database.windows.net bağlantı noktası 1433 için TCP/IP bağlantısı başarısız oldu. Hata: "xxxx.database.windows.net. Bağlantı özelliklerini doğrulayın. Konakta bir SQL Server örneğinin çalıştığından ve bağlantı noktasında TCP/IP bağlantılarını kabul ettiğinizden emin olun. Bağlantı noktasına TCP bağlantılarının bir güvenlik duvarı tarafından engellenmediğinden emin olun. "

- **Belirtiler**: veri önizlemesi yapılamıyor veya veritabanı kaynağı veya havuzu ile işlem hattı yürütülemiyor

- **Neden**: veritabanı güvenlik duvarı tarafından korunuyor

- **Çözüm**: veritabanına yönelik güvenlik duvarı erişimini açın

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Hata iletisi: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: veritabanında ' xxxxxx ' adlı bir nesne zaten var.

- **Belirtiler**: havuz tablo oluşturamıyor

- **Neden**: hedef veritabanında, kaynağınızda veya veri kümesinde tanımlanmış aynı ada sahip bir tablo adı zaten var

- **Çözüm**: oluşturmaya çalıştığınız tablonun adını değiştirin

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-string-or-binary-data-would-be-truncated"></a>Hata iletisi: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: String veya binary verileri kesilecek. 

- **Belirtiler**: bir SQL havuzuna veri yazarken, olası kesme hatası ile veri akışınız işlem hattı yürütmesinde başarısız olur.

- **Neden**: veri akışınızdan SQL veritabanınızdaki bir sütuna haritalardan bir alan, değeri depolamak için yeterince geniş DEĞIL ve SQL sürücüsünün bu hatayı oluşturmasına neden oluyor

- **Çözüm**: türetilmiş bir sütunda ```left()``` kullanarak veya ["hata satırı" modelini](how-to-data-flow-error-rows.md) uyguladığınızda dize sütunlarının verilerinin uzunluğunu azaltabilirsiniz.

### <a name="error-message-since-spark-23-the-queries-from-raw-jsoncsv-files-are-disallowed-when-the-referenced-columns-only-include-the-internal-corrupt-record-column"></a>Hata iletisi: Spark 2,3 ' den Itibaren, başvurulan sütunlar yalnızca iç bozuk kayıt sütununu içeriyorken ham JSON/CSV dosyalarındaki sorgulara izin verilmez. 

- **Belirtiler**: JSON kaynağından okuma başarısız oluyor

- **Neden**: çok sayıda iç içe geçmiş satırda tek bir belge Ile bir JSON kaynağından okurken, Spark aracılığıyla ADF, yeni bir belgenin başladığını ve önceki belgenin bittiğini belirleyemedi.

- **Çözüm**: JSON veri kümesi kullanan kaynak dönüşümünde "JSON ayarları" nı genişletin ve "tek belge" seçeneğini etkinleştirin.


## <a name="general-troubleshooting-guidance"></a>Genel sorun giderme kılavuzu

1. Veri kümesi bağlantılarınızın durumunu denetleyin. Her kaynak ve havuz dönüşümünde, kullandığınız her veri kümesi için bağlı hizmeti ziyaret edin ve bağlantıları test edin.
2. Veri akışı tasarımcısından dosya ve tablo bağlantılarınızın durumunu denetleyin. Hata ayıklama üzerinde geçiş yapın ve verilerinize erişebildiğinizden emin olmak için kaynak dönüşümlerinizin veri önizlemesine tıklayın.
3. Her şey veri önizlemesinden iyi görünüyorsa, işlem hattı tasarımcısına gidin ve veri akışınızı bir işlem hattı etkinliğine yerleştirin. Uçtan uca bir test için işlem hattının hatalarını ayıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla sorun giderme Yardım için şu kaynakları deneyin:

*  [Data Factory blogu](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory Özellik istekleri](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videoları](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forumu](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory için Stack Overflow Forumu](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory hakkında Twitter bilgileri](https://twitter.com/hashtag/DataFactory)
*  [ADF eşleme veri akışları performans Kılavuzu](concepts-data-flow-performance.md)
