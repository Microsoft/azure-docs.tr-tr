---
title: "Oracle 'dan PostgreSQL için Azure veritabanı: geçiş kılavuzu"
titleSuffix: Azure Database for PostgreSQL
description: Bu kılavuzda, Oracle şemanızı PostgreSQL için Azure veritabanı 'na geçirmeniz öğretilir.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 931528ec415cabde8e862db17b9f8f26502f6788
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968943"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Oracle 'ı PostgreSQL için Azure veritabanı 'na geçirme

Bu kılavuzda, Oracle şemanızı PostgreSQL için Azure veritabanı 'na geçirmeniz öğretilir. 

Ayrıntılı ve kapsamlı geçiş kılavuzu için, [Geçiş Kılavuzu kaynaklarına](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf)bakın. 

## <a name="prerequisites"></a>Önkoşullar

Oracle şemanızı PostgreSQL için Azure veritabanı 'na geçirmek için şunları yapmanız gerekir: 

- Kaynak ortamınızın desteklendiğini doğrulayın. 
- En son [ora2pg](https://ora2pg.darold.net/)sürümünü indirin. 
- [DBD modülünün](https://www.cpan.org/modules/by-module/DBD/)en son sürümüne sahip olmalıdır. 


## <a name="overview"></a>Genel Bakış

PostgreSQL, dünyanın en gelişmiş açık kaynaklı veritabanlarının biridir. Bu makalede, bir Oracle veritabanını PostgreSQL 'e geçirmek için ücretsiz ora2pg aracının nasıl kullanılacağı açıklanır. Bir Oracle veritabanını veya MySQL veritabanını PostgreSQL ile uyumlu bir şemaya geçirmek için ora2pg kullanabilirsiniz. 

Ora2pg aracı Oracle veritabanınızı bağlar, otomatik olarak tarar ve yapısını veya verilerini ayıklar. Ardından ora2pg, PostgreSQL veritabanınıza yükleyebilmeniz için SQL komut dosyaları oluşturur. Bir Oracle veritabanını ters mühendislik, büyük bir kurumsal veritabanını geçirme veya yalnızca bazı Oracle verilerini bir PostgreSQL veritabanına çoğaltma gibi görevler için ora2pg kullanabilirsiniz. Aracın kullanımı kolaydır ve Oracle veritabanına bağlanmak için gereken parametreleri sağlama yeteneğinin yanı sıra Oracle veritabanı bilgisi gerektirmez.

> [!NOTE]
> En son ora2pg sürümünü kullanma hakkında daha fazla bilgi için bkz. [ora2pg belgeleri](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Tipik ora2pg geçiş mimarisi

![Ora2pg geçiş mimarisinin ekran görüntüsü.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

VM 'yi ve PostgreSQL için Azure veritabanı 'nı sağlamadıktan sonra aralarında bağlantıyı etkinleştirmek için iki yapılandırmaya ihtiyacınız vardır: **Azure hizmetlerine erişime Izin ver** ve **SSL bağlantısını zorla**: 

- **Bağlantı güvenliği** dikey penceresi > üzerinde **Azure hizmetlerine erişime izin ver**  >  

- **Bağlantı güvenliği** dikey > SSL **ayarlarını**  >  **zorla SSL bağlantısını**  >  **devre dışı** bırakma

### <a name="recommendations"></a>Öneriler

- Oracle sunucusunda değerlendirme veya dışarı aktarma işlemlerinin performansını artırmak için istatistikleri toplayın:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Yerine komutunu kullanarak verileri dışarı aktarın `COPY` `INSERT` .

- Yabancı anahtarları (FKs), kısıtlamaları ve dizinleri olan tabloları dışarı aktarıp çıkarmaktan kaçının. Bu öğeler, PostgreSQL 'e veri aktarma sürecini yavaşlatır.

- *Hiçbir Data yan tümcesini* kullanarak gerçekleştirilmiş görünümler oluşturun. Ardından görünümleri daha sonra yenileyin.

- Mümkünse, gerçekleştirilmiş görünümlerde benzersiz dizinleri kullanın. Söz dizimini kullandığınızda bu dizinler yenilemeyi hızlandırabilirler `REFRESH MATERIALIZED VIEW CONCURRENTLY` .


## <a name="premigration"></a>Ön geçiş 

Kaynak ortamınızın desteklenip desteklenmediğini doğruladıktan ve önkoşulları karşıladığınızı doğruladıktan sonra, ön geçiş aşamasına başlamaya hazırsınız demektir. Başlamak için: 

1. Geçirmeniz gereken veritabanlarının envanterini çıkarın. 
1. Olası geçiş sorunları veya engelleyiciler için bu veritabanlarını değerlendirin.
1. Örtülmüş olan tüm öğeleri çözün. 
 
Oracle için PostgreSQL için Azure veritabanı gibi heterojen geçişleri için, bu aşamada kaynak veritabanı şemalarının hedef ortamla uyumlu hale getirilmesi da gerekir.

### <a name="discover"></a>Bulma

Bulma aşamasının hedefi, var olan veri kaynaklarını ve kullanılmakta olan özelliklerle ilgili ayrıntıları belirlemektir. Bu aşama, geçişin daha iyi anlaşılmasını ve planlanmasına yardımcı olur. İşlem, tüm kuruluşunuzun Oracle örneklerini, kullanımdaki sürüm ve özelliklerle birlikte tanımlamak üzere ağı taramayı içerir.

Oracle için Microsoft preassessment betikleri Oracle veritabanında çalışır. Preassessment betikleri Oracle meta verilerini sorgular. Betikler şunları sağlar:

- Şemaya, türe ve duruma göre nesne sayısı dahil olmak üzere, bir veritabanı envanteri.
- İstatistiklere göre her bir şemadaki ham verilerin kaba bir tahmini.
- Her şemadaki tabloların boyutu.
- Paket başına kod satırı sayısı, işlev, yordam vb.

[Ora2pg Web sitesinden](https://ora2pg.darold.net/)ilgili betikleri indirin.

### <a name="assess"></a>Değerlendirme

Oracle veritabanlarını envanterden sonra, veritabanı boyutu ve olası güçlükler hakkında fikir sahibi olacaksınız. Sonraki adım değerlendirmesi çalıştıralım.

Oracle 'dan PostgreSQL 'e geçiş maliyetinin tahmin edilmesi kolay değildir. Ora2pg, geçiş maliyetini değerlendirmek için, otomatik olarak dönüştüremediğini nesneler ve PL/SQL kodu için tüm veritabanı nesnelerini, işlevleri ve saklı yordamları denetler.

Ora2pg Aracı, bir metin raporu oluşturmak için Oracle veritabanını denetleyen bir içerik çözümleme moduna sahiptir. Rapor, Oracle veritabanının neleri içerdiğini ve ne tür verilebileceğinizi açıklar.

*Analiz ve rapor* modunu etkinleştirmek için, `SHOW_REPORT` aşağıdaki komutta gösterildiği gibi, verilen türü kullanın:

```
ora2pg -t SHOW_REPORT
```

Ora2pg Aracı, Oracle sözdiziminden PostgreSQL 'e SQL ve PL/SQL kodu dönüştürebilir. Veritabanı çözümlendikten sonra, ora2pg kod zorluklarıyla ve tam bir veritabanının geçirilmesi için gereken süreyi tahmin edebilir.

Geçiş maliyetini insan günlerinde tahmin etmek için, ora2pg adlı bir yapılandırma yönergesini kullanmanıza izin verir `ESTIMATE_COST` . Ayrıca, bu yönergeyi bir komut isteminde etkinleştirebilirsiniz:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

Varsayılan geçiş birimi bir PostgreSQL uzmanı için beş dakika içinde temsil eder. Bu geçiş ilk kez ise, yapılandırma yönergesini `COST_UNIT_VALUE` veya komut satırı seçeneğini kullanarak varsayılan geçiş birimini artırabilirsiniz `--cost_unit_value` .

Raporun son satırında, toplam tahmini geçiş kodu insan günlerinde gösterilir. Tahmin, her bir nesne için tahmin edilen geçiş birimi sayısını izler.

Bu geçiş birimi bir PostgreSQL uzmanı için yaklaşık beş dakika temsil eder. Bu geçiş ilk kez ise, yapılandırma yönergesini `COST_UNIT_VALUE` veya komut satırı seçeneğini kullanarak varsayılanı artırabilirsiniz `--cost_unit_value` . 

Aşağıdaki kod örneğinde bazı değerlendirme çeşitlemelerini görürsünüz: 
* Tablo değerlendirmesi
* Sütun değerlendirmesi
* 5 dakikalık varsayılan maliyet birimini kullanan şema değerlendirmesi
* 10 dakikalık bir maliyet birimi kullanan şema değerlendirmesi

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Şema değerlendirmesi geçiş düzeyi B-5 ' in çıktısı aşağıda verilmiştir:

* Geçiş düzeyleri:

  * Bir-geçiş otomatik olarak çalıştırılabilir
    
  * B-kod yeniden yazma ve 5 güne kadar bir insan günü maliyeti ile geçiş
    
  * Kod yeniden yazma ve 5 güne kadar bir insan günü maliyeti ile C geçişi
    
* Teknik düzeyler:

   * 1 = önemsiz: saklı işlev yok ve tetikleyici yok

   * 2 = kolay: saklı işlev yok, ancak Tetikleyiciler; el ile yeniden yazma yok

   * 3 = basit: depolanan işlevler ve/veya Tetikleyiciler; el ile yeniden yazma yok

   * 4 = Manual: saklı işlev yok, ancak kod yeniden yazma ile Tetikleyiciler veya görünümler

   * 5 = zor: kodu yeniden yazma ile saklı işlevler ve/veya Tetikleyiciler

Değerlendirmesi aşağıdakilerden oluşur: 
* Geçişin el ile yeniden yazma gerekip gerekmediğini belirtmek için bir harf (A veya B).

* Teknik zorluk göstermek için 1 ile 5 arasında bir sayı. 

Diğer bir seçenek `-human_days_limit` olan insan günü sınırını belirtir. Burada, geçişin büyük miktarda iş, tam proje yönetimi ve geçiş desteği gerektiğini belirtmek için geçiş düzeyini C olarak ayarlayın. Varsayılan değer 10 insan günleridir. `HUMAN_DAYS_LIMIT`Bu varsayılan değeri kalıcı olarak değiştirmek için yapılandırma yönergesini kullanabilirsiniz.

Bu şema değerlendirmesi, kullanıcıların hangi veritabanı öncelikle geçirilecek ve hangi takımların hangi takımlara harekete geçirileceğine karar vermesine yardımcı olmak için geliştirilmiştir.

### <a name="convert"></a>Dönüştür

Minimum kapalı kalma süresi geçişlerde geçiş kaynağınız değişir. Bir kerelik geçişten sonra veri ve şema açısından hedeften Drifts. *Veri eşitleme* aşaması sırasında kaynaktaki tüm değişikliklerin yakalanıp hedefe neredeyse gerçek zamanlı olarak uygulandığından emin olun. Hedefe yapılan tüm değişikliklerin uygulandığını doğruladıktan sonra kaynaktan hedef ortama kadar *kesilebilir* .

Geçişin bu adımında, Oracle kodu ve DDL betikleri, PostgreSQL 'e dönüştürülür veya çevrilir. Ora2pg Aracı, Oracle nesnelerini bir PostgreSQL biçimindeki otomatik olarak dışa aktarır. Oluşturulan bazı nesneler, el ile değişiklik yapılmadan PostgreSQL veritabanında derlenemiyor.  

El ile müdahale gerektiren öğeleri anlamak için ilk olarak ora2pg tarafından oluşturulan dosyaları PostgreSQL veritabanına göre derleyin. Günlüğü kontrol edin ve şema yapısı PostgreSQL sözdizimiyle uyumlu olana kadar gerekli değişiklikleri yapın.


#### <a name="create-a-migration-template"></a>Geçiş şablonu oluşturma 

Ora2pg 'in sağladığı geçiş şablonunu kullanmanızı öneririz. Ve seçeneklerini kullandığınızda ora2pg, `--project_base` `--init_project` Oracle veritabanından tüm nesneleri dışarı aktarmak için bir iş ağacı, bir yapılandırma dosyası ve bir komut dosyası içeren bir proje şablonu oluşturur. Daha fazla bilgi için [ora2pg belgelerine](https://ora2pg.darold.net/documentation.html)bakın.

Aşağıdaki komutu kullanın: 

```
ora2pg --project_base /app/migration/ --init_project test_project

ora2pg --project_base /app/migration/ --init_project test_project
```

Örnek çıktı aşağıda verilmiştir: 
   
```
Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/

Generating generic configuration file

Creating script export_schema.sh to automate all exports.

Creating script import_all.sh to automate all imports.
```

`sources/`Dizin, Oracle kodunu içerir. `schema/`Dizin, PostgreSQL ile bağlantı verilen kodu içerir. Ve `reports/` Dizin, HTML raporlarını ve geçiş maliyeti değerlendirmesini içerir.


Proje yapısı oluşturulduktan sonra genel bir yapılandırma dosyası oluşturulur. Oracle veritabanı bağlantısını ve yapılandırma dosyasında ilgili yapılandırma parametrelerini tanımlayın. Yapılandırma dosyası hakkında daha fazla bilgi için [ora2pg belgelerine](https://ora2pg.darold.net/documentation.html)bakın.


#### <a name="export-oracle-objects"></a>Oracle nesnelerini dışarı aktarma

Sonra, *export_schema. sh* dosyasını çalıştırarak Oracle nesnelerini PostgreSQL nesneleri olarak dışarı aktarın.

```
cd /app/migration/mig_project
./export_schema.sh
```

Aşağıdaki komutu el ile çalıştırın.

```
SET namespace="/app/migration/mig_project"

ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
%namespace%/config/ora2pg.conf
ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
%namespace%/config/ora2pg.conf
ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
%namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
ora2pg -p -t PACKAGE -o packages.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
ora2pg -p -t PROCEDURE -o procs.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
ora2pg -t SEQUENCE -o sequences.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
%namespace%/config/ora2pg.conf
ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
%namespace%/config/ora2pg.conf
ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
%namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
```

Verileri ayıklamak için aşağıdaki komutu kullanın.

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>Dosyaları derle

Son olarak, tüm dosyaları PostgreSQL için Azure veritabanı sunucusuna göre derleyin. El ile oluşturulan DDL dosyalarını yüklemeyi seçebilir veya bu dosyaları etkileşimli olarak içeri aktarmak için *import_all. sh* ikinci komut dosyasını kullanabilirsiniz.

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-

server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l

%namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
```

Veri içeri aktarma komutu aşağıda verilmiştir:

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

Dosyalar derlenirken, günlükleri kontrol edin ve ora2pg 'in kendi kendine dönüştüremediği tüm söz dizimini düzeltin.

Daha fazla bilgi için bkz: [Oracle 'Dan PostgreSQL Için Azure veritabanı geçiş geçici çözümleri](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf).

## <a name="migrate"></a>Geçiş 

Gerekli önkoşullara sahip olduktan ve geçiş öncesi adımları tamamladıktan sonra şema ve veri geçişini başlatabilirsiniz.

### <a name="migrate-schema-and-data"></a>Şemayı ve verileri geçirme

Gerekli düzeltmeleri yaptığınızda veritabanının kararlı bir derlemesi dağıtıma hazırdır. `psql`Değiştirilen kodu içeren dosyalara işaret eden içeri aktarma komutlarını çalıştırın. Bu görev, veritabanı nesnelerini PostgreSQL veritabanına göre derler ve verileri içeri aktarır.

Bu adımda, verileri içeri aktarmak için bir paralellik düzeyi uygulayabilirsiniz.

### <a name="sync-data-and-cut-over"></a>Verileri eşitleyin ve üzerine kesin

Çevrimiçi (en düşük kapalı kalma süresi) geçişlerde geçiş kaynağı değişmeye devam eder. Bir kerelik geçişten sonra veri ve şema açısından hedeften Drifts. 

*Veri eşitleme* aşaması sırasında kaynaktaki tüm değişikliklerin yakalanıp hedefe neredeyse gerçek zamanlı olarak uygulandığından emin olun. Tüm değişikliklerin uygulandığını doğruladıktan sonra kaynaktan hedef ortama kadar kesilebilir.

Çevrimiçi geçiş yapmak için destek bölümüne başvurun AskAzureDBforPostgreSQL@service.microsoft.com .

Her tablo için ora2pg kullanan bir *Delta/artımlı* geçişte, tarih, saat veya başka bir parametreye göre filtreleyen (*keser*) bir sorgu kullanın. Ardından, kalan verileri geçirirken ikinci bir sorgu kullanarak geçişi tamamlayın.

Kaynak veri tablosunda, önce tüm geçmiş verileri geçirin. Aşağıda bir örnek verilmiştir:

```
select * from table1 where filter_data < 01/01/2019
```

Bunun gibi bir komut çalıştırarak ilk geçişten sonra yapılan değişiklikleri sorgulayabilirsiniz:

```
select * from table1 where filter_data >= 01/01/2019
```

Bu durumda, kaynak ve hedefin her iki tarafında da veri eşliği denetleyerek doğrulamayı geliştirmenize önerilir.

## <a name="postmigration"></a>Geçiş sonrası 

*Geçiş* aşamasından sonra, her şeyin olabildiğince sorunsuz ve etkili bir şekilde çalıştığından emin olmak için postmigration görevlerini doldurun.

### <a name="remediate-applications"></a>Uygulamaları düzelt

Veriler hedef ortama geçirildikten sonra, daha önce kaynağı tüketen tüm uygulamaların hedefi tüketmeye başlaması gerekir. Kurulum bazen uygulamalarda değişiklik yapılmasını gerektirir.

### <a name="test"></a>Test

Veriler hedefe geçirildikten sonra, uygulamaların hedefle düzgün çalıştığını doğrulamak için, veritabanlarına karşı testler çalıştırın. Oracle kaynak ve PostgreSQL hedef veritabanlarında el ile veri doğrulama betikleri çalıştırılarak kaynak ve hedefin doğru şekilde geçirildiğinden emin olun.

İdeal olarak, kaynak ve hedef veritabanlarının bir ağ yolu varsa, ora2pg veri doğrulaması için kullanılmalıdır. `TEST`Bu eylemi, Oracle veritabanındaki tüm nesnelerin PostgreSQL içinde oluşturulduğundan emin olmak için kullanabilirsiniz. 

Şu komutu çalıştırın:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>İyileştirme

Veri doğruluğu sorunlarını mutabık kılma ve tamamlanmanın doğrulanması için postmigration aşaması çok önemlidir. Bu aşamada, iş yüküyle ilgili performans sorunlarını da ele alırsınız.

## <a name="migration-assets"></a>Geçiş varlıkları 

Bu geçiş senaryosu hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın. Gerçek dünyada geçiş projesi etkileşimini destekler.

| Kaynak | Açıklama    |
| -------------- | ------------------ |
| [Oracle 'dan Azure 'a PostgreSQL geçişi kılavuz kitabı](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Bu belge, ora2pg kullanarak mimarlara, danışmanlara, veritabanı yöneticilerine ve ilgili rollere yönelik iş yüklerini Oracle 'dan PostgreSQL için Azure veritabanı 'na hızlıca geçirebilmenizi sağlar. |
| [Oracle 'dan Azure 'a PostgreSQL geçiş geçici çözümleri](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Bu belgede, çalışma yüklerini Oracle 'dan PostgreSQL için Azure veritabanı 'na geçirirken mimarların, danışmanların, veritabanı yöneticilerinin ve ilgili rollerin hızla düzelmekte veya sorunları gidermesine yardımcı olur. |
| [Windows veya Linux 'ta ora2pg yüklemek için adımlar](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Bu belgede, Windows veya Linux 'ta ora2pg kullanarak Oracle 'dan PostgreSQL 'e yönelik şema ve verilerin geçirilmesi için hızlı bir yükleme kılavuzu sağlanmıştır. Daha fazla bilgi için [ora2pg belgelerine](http://ora2pg.darold.net/documentation.html)bakın. |

Veri SQL Mühendisliği ekibi bu kaynakları geliştirdik. Bu takımın temel kurucu, veri platformu geçiş projelerinin Microsoft Azure veri platformuna yönelik karmaşık modernleştirmeyi engellemeyi ve hızlandırmanızı sağlar.

## <a name="more-support"></a>Daha fazla destek

Ora2pg Tooling kapsamının ötesinde geçiş yardımı için [ @Ask PostgreSQL IÇIN Azure DB](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)ile iletişim kurun.

## <a name="next-steps"></a>Sonraki adımlar

Veritabanı ve veri geçişine ve özel görevlere yönelik bir hizmet ve araç matrisi için bkz. [veri geçişi Için hizmetler ve araçlar](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Belgeler: 
- [PostgreSQL için Azure Veritabanı belgeleri](https://docs.microsoft.com/azure/postgresql/)
- [ora2pg belgeleri](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL Web sitesi](https://www.postgresql.org/)
- [PostgreSQL içinde otonom işlem desteği](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 
