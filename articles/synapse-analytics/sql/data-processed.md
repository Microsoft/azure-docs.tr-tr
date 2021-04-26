---
title: Sunucusuz SQL havuzu için maliyet yönetimi
description: Bu belgede, Azure depolama 'daki verileri sorgularken sunucusuz SQL havuzunun maliyetinin ve işlenen verilerin nasıl yönetileceği açıklanmaktadır.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8a26f8ced5e91810f8cadff0a27796dc817e6517
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491593"
---
# <a name="cost-management-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu için maliyet yönetimi

Bu makalede, Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu için maliyetleri nasıl tahmin edebileceğiniz ve yönetebileceğiniz açıklanmaktadır:
- Sorgu vermeden önce işlenen veri miktarını tahmin etme
- Bütçeyi ayarlamak için maliyet denetimi özelliğini kullanma

Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu maliyetlerinin yalnızca Azure faturanızda Aylık maliyetlerin yalnızca bir kısmı olduğunu anlayın. Diğer Azure hizmetlerini kullanıyorsanız, üçüncü taraf hizmetler de dahil olmak üzere Azure aboneliğinizde kullanılan tüm Azure hizmetleri ve kaynakları için faturalandırılırsınız. Bu makalede, Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu için maliyetlerin nasıl planlanacağı ve yönetileceği açıklanmaktadır.

## <a name="data-processed"></a>İşlenen veriler

*Işlenen veri* , bir sorgu çalıştırılırken sistemin geçici olarak depoladığı veri miktarıdır. İşlenen veri aşağıdaki miktarlarla oluşur:

- Depolama alanından okunan veri miktarı. Bu miktar şunları içerir:
  - Veriler okunurken okunan veriler.
  - Meta veriler okunurken okunan veriler (Parquet gibi meta veriler içeren dosya biçimleri için).
- Ara sonuçlarda veri miktarı. Bu veriler, sorgu çalışırken düğümler arasında aktarılır. Bu, bitiş noktanıza veri aktarımını sıkıştırılmamış bir biçimde içerir. 
- Depolamaya yazılan veri miktarı. Sonuç kümesini depolama 'ya aktarmak için CETAS kullanırsanız, dışarı yazılan veri miktarı CETAS 'ın SEÇIM bölümü için işlenen veri miktarına eklenir.

Dosyaları depolamadan okumak son derece iyileştirilmiştir. İşlem şunları kullanır:

- Önceden getirme, okunan veri miktarına bazı ek yük ekleyebilecek. Bir sorgu bir dosyanın tamamını okuyorsa, ek yük yoktur. Bir dosya kısmen okunarak, Ilk N sorgu gibi, önceden getirme kullanılarak biraz daha fazla veri okunmalıdır.
- İyileştirilmiş bir virgülle ayrılmış değer (CSV) ayrıştırıcısı. CSV dosyalarını okumak için PARSER_VERSION = ' 2.0 ' kullanırsanız, depolama alanından okunan veri miktarları biraz artar. İyileştirilmiş bir CSV ayrıştırıcısı, dosyaları eşit büyüklükte parçalar halinde paralel olarak okur. Parçalar tüm satırları içermez. Tüm satırların ayrıştırılmasını sağlamak için, İyileştirilmiş CSV ayrıştırıcısı bitişik öbeklerin küçük parçalarını de okur. Bu işlem, küçük miktarda yük getirir.

## <a name="statistics"></a>İstatistikler

Sunucusuz SQL havuzu sorgu iyileştiricisi, en iyi sorgu yürütme planlarını oluşturmak için istatistiklere bağımlıdır. İstatistikleri el ile oluşturabilirsiniz. Aksi halde, sunucusuz SQL havuzu bunları otomatik olarak oluşturur. Her iki durumda da, belirtilen örnek hızda belirli bir sütun döndüren ayrı bir sorgu çalıştırılarak istatistikler oluşturulur. Bu sorguda, işlenen ilişkili bir veri miktarı vardır.

Oluşturulan istatistiklerden faydalanabilir aynı veya başka bir sorgu çalıştırırsanız, mümkünse istatistikler yeniden kullanılır. İstatistik oluşturmak için işlenen ek veri yok.

Bir Parquet sütunu için istatistikler oluşturulduğunda, yalnızca ilgili sütun dosyalardan okunurdur. Bir CSV sütunu için istatistikler oluşturulduğunda, tüm dosyalar okunup ayrıştırılır.

## <a name="rounding"></a>Yuvarlama

İşlenen veri miktarı, sorgu başına en yakın MB 'ye yuvarlanır. Her sorguda en az 10 MB işlenen veri bulunur.

## <a name="what-data-processed-doesnt-include"></a>İşlenen veriler şunları içermez

- Sunucu düzeyinde meta veriler (oturum açma, roller ve sunucu düzeyi kimlik bilgileri gibi).
- Uç noktanıza oluşturduğunuz veritabanları. Bu veritabanları yalnızca meta verileri (kullanıcılar, roller, şemalar, görünümler, satır içi tablo değerli işlevler [TVFs], saklı yordamlar, veritabanı kapsamlı kimlik bilgileri, dış veri kaynakları, dış dosya biçimleri ve dış tablolar gibi) içerir.
  - Şema çıkarımı kullanırsanız, sütun adlarını ve veri türlerini çıkarması için dosya parçaları okunurdur ve okunan veri miktarı işlenen veri miktarına eklenir.
- Veri tanımlama dili (DDL) deyimleri, CREATE STATıSTıCS deyimi haricinde, verileri depolamadan belirtilen örnek yüzdesine göre işliyor.
- Yalnızca meta veri sorguları.

## <a name="reducing-the-amount-of-data-processed"></a>İşlenen veri miktarını azaltma

Sorgu başına işlenen veri miktarını en iyi hale getirebilir ve verilerinizi bölümleyerek ve Parquet gibi sıkıştırılmış bir sütun tabanlı biçime dönüştürerek performansı artırabilirsiniz.

## <a name="examples"></a>Örnekler

Üç tablo düşünün.

- Population_csv tablosu, 5 TB CSV dosyası tarafından desteklenir. Dosyalar, eşit olarak boyutlandırılmış beş sütunda düzenlenir.
- Population_parquet tablo, population_csv tabloyla aynı verilere sahiptir. 1 TB 'lık Parquet dosyası tarafından desteklenir. Veriler Parquet biçiminde sıkıştırıldığından, bu tablo öncekinden daha küçüktür.
- Very_small_csv tablosu, 100 KB/CSV dosyası tarafından desteklenir.

**Sorgu 1**: population_csv Sum (popülasyon) seçin

Bu sorgu, popülasyon sütunu için değerleri almak üzere tüm dosyaları okur ve ayrıştırır. Düğümler bu tablonun parçalarını işler ve her parçanın popülasyon toplamı düğümler arasında aktarılır. Son Toplam, uç noktanıza aktarılır. 

Bu sorgu 5 TB veri ve parçaların toplamlarını aktarmaya yönelik küçük bir ek yük tutar.

**Sorgu 2**: population_parquet Sum (popülasyon) seçin

Parquet gibi sıkıştırılmış ve sütun tabanlı biçimleri sorguladığınızda, sorgu 1 ' den daha az veri okundu. Sunucusuz SQL havuzu dosyanın tamamı yerine tek bir sıkıştırılmış sütun okuduğu için bu sonucu görürsünüz. Bu durumda, 0,2 TB okunurdur. (Beş eşit boyutlu sütunlar her biri 0,2 TB 'dir.) Düğümler bu tablonun parçalarını işler ve her parçanın popülasyon toplamı düğümler arasında aktarılır. Son Toplam, uç noktanıza aktarılır. 

Bu sorgu 0,2 TB ve parçaların toplamlarını aktarmaya yönelik küçük bir ek yük tutar.

**Sorgu 3**: select * from population_parquet

Bu sorgu tüm sütunları okur ve tüm verileri sıkıştırılmamış bir biçimde aktarır. Sıkıştırma biçimi 5:1 ise sorgu 6 TB 'yi işleyerek 1 TB 'yi okuyup sıkıştırılmamış verileri 5 TB 'a aktarır.

**Sorgu 4**: very_small_csv Count (*) seçin

Bu sorgu tüm dosyaları okur. Bu tablo için depolama alanındaki dosyaların toplam boyutu 100 KB 'dir. Düğümler bu tablonun parçalarını işler ve her parçanın toplamı düğümler arasında aktarılır. Son Toplam, uç noktanıza aktarılır. 

Bu sorgu 100 KB 'tan fazla veriyi işler. Bu sorgu için işlenen veri miktarı, bu makalenin [yuvarlama](#rounding) bölümünde belirtildiği gıbı 10 MB 'a yuvarlanır.

## <a name="cost-control"></a>Maliyet denetimi

Sunucusuz SQL havuzundaki maliyet denetimi özelliği, işlenen veri miktarı için bütçeyi ayarlamanıza olanak sağlar. Bütçeyi bir gün, hafta ve ay boyunca işlenen TB veri olarak ayarlayabilirsiniz. Aynı zamanda bir veya daha fazla bütçe ayarlamış olabilirsiniz. Sunucusuz SQL havuzunun maliyet denetimini yapılandırmak için SYNAPSE Studio veya T-SQL kullanabilirsiniz.

## <a name="configure-cost-control-for-serverless-sql-pool-in-synapse-studio"></a>SYNAPSE Studio 'da sunucusuz SQL havuzu için maliyet denetimini yapılandırma
 
SYNAPSE Studio 'da sunucusuz SQL havuzu için maliyet denetimini yapılandırmak için, analiz havuzları altındaki SQL havuzu öğesini seçme bölümünden sol taraftaki menüden öğeyi Yönet ' e gidin. Sunucusuz SQL havuzunun üzerine geldiğinizde, bu simgeye bir maliyet denetimi simgesi görürsünüz.

![Maliyet denetimi gezintisi](./media/data-processed/cost-control-menu.png)

Maliyet denetimi simgesine tıkladığınızda bir yan çubuk görüntülenir:

![Maliyet denetimi yapılandırması](./media/data-processed/cost-control-sidebar.png)

Bir veya daha fazla bütçe ayarlamak için, önce ayarlamak istediğiniz bir bütçe için radyo düğmesini etkinleştir ' e tıklayın, metin kutusuna tamsayı değerini girin. Değer için birim TBs 'dir. İstediğiniz bütçeleri yapılandırdıktan sonra yan çubuğun alt kısmındaki Uygula düğmesine tıklayın. Bu, bütçeniz artık ayarlanmış.

## <a name="configure-cost-control-for-serverless-sql-pool-in-t-sql"></a>T-SQL içindeki sunucusuz SQL havuzu için maliyet denetimini yapılandırma

T-SQL ' te sunucusuz SQL havuzu için maliyet denetimini yapılandırmak üzere aşağıdaki saklı yordamlardan birini veya birkaçını yürütmeniz gerekir.

```sql
sp_set_data_processed_limit
    @type = N'daily',
    @limit_tb = 1

sp_set_data_processed_limit
    @type= N'weekly',
    @limit_tb = 2

sp_set_data_processed_limit
    @type= N'monthly',
    @limit_tb = 3334
```

Geçerli yapılandırmayı görmek için aşağıdaki T-SQL ifadesini yürütün:

```sql
SELECT * FROM sys.configurations
WHERE name like 'Data processed %';
```

Geçerli gün, hafta veya ay boyunca işlenen veri miktarını görmek için aşağıdaki T-SQL ifadesini yürütün:

```sql
SELECT * FROM sys.dm_external_data_processed
```

## <a name="next-steps"></a>Sonraki adımlar

Sorguların performans için nasıl iyileştirileceği hakkında bilgi edinmek için bkz. [sunucusuz SQL havuzu Için en iyi uygulamalar](best-practices-sql-on-demand.md).
