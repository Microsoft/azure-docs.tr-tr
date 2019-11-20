---
title: Veri eğriltme-Visual Studio için Azure Data Lake Araçları çözümleme
description: Visual Studio için Azure Data Lake Araçları kullanarak veri eğriltme sorunları için olası çözümleri giderme.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: 9ff7ba5f04a8c1862f8ef136f8f3f6900f00a431
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802546"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Visual Studio için Azure Data Lake Araçları kullanarak veri eğriltme sorunlarını çözün

## <a name="what-is-data-skew"></a>Veri eğriltme nedir?

Kısaca belirtilen veri eğimi, daha fazla temsil edilen bir değerdir. Vergi dönüşlerine yönelik 50 vergi Examiners, her ABD durumu için bir Examiner atandığını düşünün. Wyote Examiner, popülasyon küçük olduğu için çok daha az. Ancak California 'da, durumun büyük popülasyonu nedeniyle Examiner çok meşgul tutulur.
    ![Data-skew sorun örneği @ no__t-1

Senaryolarımızda, veriler tüm vergi Examiners arasında eşit olarak dağıtılmaktadır, bu da bazı Examiners diğerlerinden daha fazla çalışması gerektiği anlamına gelir. Kendi işiniz, burada Tax-Examiner örneği gibi durumlar hakkında daha fazla durum yaşarsınız. Daha fazla teknik koşullarda, bir köşe eşlerinden çok daha fazla veri alır, köşeyi diğerlerinden daha fazla çalışma ve sonunda işin tamamını yavaşlatan bir durumdur. Ne kadar kötü olursa iş başarısız olabilir, çünkü köşeler, örneğin 5 saatlik çalışma zamanı sınırlaması ve 6 GB bellek sınırlaması olabilir.

## <a name="resolving-data-skew-problems"></a>Veri eğriltme sorunlarını çözme

Visual Studio için Azure Data Lake Araçları, işinizin bir veri eğriltme sorunu olup olmadığını tespit etmenize yardımcı olabilir. Bir sorun varsa, bu bölümdeki çözümleri deneyerek sorunu çözebilirsiniz.

## <a name="solution-1-improve-table-partitioning"></a>Çözüm 1: tablo bölümlemesini geliştirme

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Seçenek 1: eğilmiş anahtar değerini önceden filtreleyin

İş mantığınızı etkilemezse daha yüksek frekanslı değerleri önceden filtreleyebilirsiniz. Örneğin, sütun GUID 'de çok sayıda 000-000-000 varsa, bu değeri toplamak istemeyebilirsiniz. ' İ toplamadan önce, yüksek frekanslı değeri filtrelemek için "WHERE GUID! =" 000-000-000 "" yazabilirsiniz.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>2\. seçenek: farklı bir bölüm veya dağıtım anahtarı seçin

Yukarıdaki örnekte, ülke/bölge üzerinden yalnızca vergi denetimi iş yükünü denetlemek istiyorsanız, anahtar olarak KIMLIK numarasını seçerek veri dağıtımını geliştirebilirsiniz. Farklı bir bölüm veya dağıtım anahtarı, bazı durumlarda verileri daha eşit bir şekilde dağıtabilir, ancak bu seçeneğin iş mantığınızı etkilemediğinden emin olmanız gerekir. Örneğin, her durum için vergi toplamını hesaplamak üzere, _durumu_ bölüm anahtarı olarak belirlemek isteyebilirsiniz. Bu sorunla karşılaşmaya devam ederseniz, 3. seçeneği kullanmayı deneyin.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Seçenek 3: daha fazla bölüm veya dağıtım anahtarı ekleme

Bölüm anahtarı olarak yalnızca _durum_ kullanmak yerine bölümlendirme için birden fazla anahtar kullanabilirsiniz. Örneğin, veri bölümü boyutlarını azaltmak ve verileri daha eşit bir şekilde dağıtmak için, _ZIP kodunu_ ek bir bölüm anahtarı olarak eklemeyi göz önünde bulundurun.

### <a name="option-4-use-round-robin-distribution"></a>4\. seçenek: hepsini bir kez deneme dağıtımı kullanma

Bölüm ve dağıtım için uygun bir anahtar bulamıyorsanız, hepsini bir kez deneme dağıtımı kullanmayı deneyebilirsiniz. Hepsini bir kez deneme dağıtımı, tüm satırları eşit bir şekilde değerlendirir ve rastgele bunları karşılık gelen demetlere koyar. Veriler eşit olarak dağıtılır ancak bazı işlemler için iş performansını azaltan bir dezavantajı olan yerleşim bilgilerini kaybeder. Ayrıca, asimetrik anahtar için de toplama yapıyorsanız, veri eğriltme sorunu devam eder. Hepsini bir kez deneme dağıtımı hakkında daha fazla bilgi edinmek için, [Create Table (u-SQL)](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch)içindeki U-SQL tablo dağıtımları bölümüne bakın: şemayla tablo oluşturma.

## <a name="solution-2-improve-the-query-plan"></a>Çözüm 2: sorgu planını geliştirme

### <a name="option-1-use-the-create-statistics-statement"></a>Seçenek 1: CREATE STATıSTıCS ifadesini kullanın

U-SQL, tablolarda ISTATISTIK oluştur bildirisini sağlar. Bu bildirimde, bir tabloda depolanan değer dağılımı gibi veri özellikleriyle ilgili sorgu iyileştiriciye daha fazla bilgi verilmektedir. Çoğu sorgu için sorgu iyileştiricisi, yüksek kaliteli bir sorgu planı için gerekli istatistikleri zaten oluşturuyor. Bazen, ISTATISTIK oluştur ile veya sorgu tasarımını değiştirerek ek istatistikler oluşturarak sorgu performansını iyileştirebilmeniz gerekebilir. Daha fazla bilgi için bkz. [create STATISTICS (U-SQL)](/u-sql/ddl/statistics/create-statistics) sayfası.

Kod örneği:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>İstatistik bilgileri otomatik olarak güncellenmez. İstatistikleri yeniden oluşturmadan tablodaki verileri güncelleştirirseniz sorgu performansı reddedebilirler.

### <a name="option-2-use-skewfactor"></a>Seçenek 2: SKEWFACTOR kullanma

Her bir durum için vergiyi toplamak istiyorsanız, veri eğriltme sorununa engel olmayan bir yaklaşım olan grup durumunu kullanmanız gerekir. Ancak, iyileştiricinin sizin için bir yürütme planı hazırlayabilmesi için, anahtarlardaki veri eğriliğini belirlemek üzere sorgunuzda bir veri ipucu sağlayabilirsiniz.

Genellikle, parametreyi 0,5 ve 1 olarak ayarlayabilirsiniz. Bu, 0,5 anlamına gelir ve 1 anlamı çok fazla eğriliği değildir. İpucu geçerli deyim ve tüm aşağı akış deyimleri için yürütme planı iyileştirmesini etkilediği için, olası çarpıtılmış anahtar temelinde toplamayı önce ipucunu eklediğinizden emin olun.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Kod örneği:

    //Add a SKEWFACTOR hint.
    @Impressions =
        SELECT * FROM
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions =
        SELECT
            ClientId,
            Query,
            SUM(PageClicks) AS Clicks
        FROM
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    //Query 2 for Key: Query
    @Display =
        SELECT * FROM @Sessions
            INNER JOIN @Campaigns
                ON @Sessions.Query == @Campaigns.Query
        ;   

### <a name="option-3-use-rowcount"></a>Seçenek 3: ROWCOUNT kullanma  
SKEWFACTOR 'e ek olarak, belirli bir asimetrik anahtar katılım durumu için, diğer birleştirilmiş satır kümesinin küçük olduğunu biliyorsanız, katılmadan önce U-SQL ifadesine bir ROWCOUNT ipucu ekleyerek iyileştiriciye söyleyebilirsiniz. Bu şekilde, iyileştirici performansı artırmaya yardımcı olmak için bir yayın katılımı stratejisi seçebilir. ROWCOUNT, veri eğriltme sorununu çözmediğinden emin olun, ancak bazı ek yardım sunabilir.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Kod örneği:

    //Unstructured (24-hour daily log impressions)
    @Huge   = EXTRACT ClientId int, ...
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    //Small subset (that is, ForgetMe opt out)
    @Small  = SELECT * FROM @Huge
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    //Result (not enough information to determine simple broadcast JOIN)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Çözüm 3: Kullanıcı tanımlı Reducer ve birleştirici 'yi geliştirme

Bazen karmaşık işlem mantığı ile başa çıkmak için Kullanıcı tanımlı bir operatör yazabilirsiniz ve iyi yazılmış bir Reducer ve birleştirici, bazı durumlarda bir veri eğriliği sorununu hafifletebilir.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Seçenek 1: mümkünse özyinelemeli bir Reducer kullanın

Varsayılan olarak, Kullanıcı tanımlı bir Reducer, yinelemesiz modda çalışır, bu da bir anahtarın iş azalmasını tek bir köşeye dağıtılmasıdır. Ancak verileriniz eğriltilmiş ise, büyük veri kümeleri tek bir köşede işlenebilir ve uzun bir süre çalışabilir.

Performansı artırmak için, Reducer 'i yinelemeli modda çalışacak şekilde tanımlamak üzere kodunuzda bir öznitelik ekleyebilirsiniz. Daha sonra, büyük veri kümeleri birden fazla köşelere dağıtılabilir ve işinizi hızlandıran paralel olarak çalıştırılabilir.

Özyinelemeli olmayan bir Reducer yinelemeli olarak değiştirmek için, algoritmanız için ilişkilendirilebilir olduğundan emin olmanız gerekir. Örneğin, toplam ilişkilendirilebilir ve ortanca değildir. Ayrıca, Reducer için giriş ve çıkışın aynı şemayı tutabilmeniz gerekir.

Özyinelemeli Reducer özniteliği:

    [SqlUserDefinedReducer(IsRecursive = true)]

Kod örneği:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Seçenek 2: mümkünse satır düzeyi birleştirici modunu kullan

Belirli bir asimetrik anahtar ekleme çalışması için ROWCOUNT ipucuna benzer şekilde, birleştirici modu, çalışmanın eşzamanlı olarak yürütülmesi için çok büyük ölçüde eğilmiş anahtar değer kümelerini birden fazla köşelere dağıtmaya çalışır. Birleştirici modu, veri eğriltme sorunlarını çözemez, ancak çok büyük bir eğilmiş anahtar değer kümesi için bazı ek yardım sunabilir.

Varsayılan olarak, birleştirici modu dolu, yani sol satır kümesi ve sağ satır kümesi ayrılamayan anlamına gelir. Modu sol/sağ/Iç olarak ayarlamak satır düzeyinde birleşime izin vermez. Sistem, karşılık gelen satır kümelerini ayırır ve bunları paralel olarak çalışan birden çok köşelere dağıtır. Ancak, birleştirici modunu yapılandırmadan önce, karşılık gelen satır kümelerinin ayrılabilmesi için dikkatli olun.

Aşağıdaki örnekte, ayrılmış bir sol satır kümesi gösterilmektedir. Her çıkış satırı, soldan tek bir giriş satırına bağlıdır ve bu, büyük olasılıkla aynı anahtar değeri ile sağdaki tüm satırlara bağlıdır. Birleştirici modunu sol olarak ayarlarsanız, sistem büyük ölçüde sola satır kümesini küçük olanlara ayırır ve bunları birden çok köşelere atar.

![Birleştirici modu çizimi](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Yanlış birleştirici modunu ayarlarsanız, bileşim daha az verimlidir ve sonuçlar yanlış olabilir.

Birleştirici modunun öznitelikleri:

- SqlUserDefinedCombiner (Mode = CombinerMode. Full): her çıkış satırı, tüm giriş satırlarına büyük olasılıkla aynı anahtar değeri ile soldan ve sağdan bağlıdır.

- SqlUserDefinedCombiner (Mode = CombinerMode. Left): her çıkış satırı, sol taraftaki tek bir giriş satırına (ve muhtemelen aynı anahtar değeri ile sağdaki tüm satırlara) bağlıdır.

- qlUserDefinedCombiner (Mode = CombinerMode. Right): her çıkış satırı, sağdaki tek bir giriş satırına bağlıdır (ve potansiyel olarak aynı anahtar değeri ile soldan tüm satırlar).

- SqlUserDefinedCombiner (Mode = CombinerMode. Inner): her çıkış satırı, sol taraftaki tek bir giriş satırına ve aynı değer ile sağa bağlıdır.

Kod örneği:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
