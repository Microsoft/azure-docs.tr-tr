---
title: Azure HDInsight 'ta Spark yapılandırılmış akışı
description: HDInsight Spark kümelerinde Spark yapılandırılmış akış uygulamalarını kullanma.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: hrasheed
ms.openlocfilehash: 0aaca127fec82d35da0ba943e97221834c2e42ed
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441903"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Apache Spark yapılandırılmış akışa genel bakış

[Apache Spark](https://spark.apache.org/) Yapılandırılmış akış, veri akışlarını işlemek için ölçeklenebilir, yüksek performanslı ve hataya dayanıklı uygulamalar uygulamanıza olanak sağlar. Yapılandırılmış akış Spark SQL altyapısı üzerine kurulmuştur ve derleme sorgularını, toplu sorguları yazacağınız şekilde yazmak için Spark SQL veri çerçevelerinden ve veri kümelerinden yapıları geliştirir.  

Yapılandırılmış akış uygulamaları, HDInsight Spark kümelerinde çalışır ve [Apache Kafka](https://kafka.apache.org/), TCP yuvası (hata ayıklama amacıyla), Azure depolama veya Azure Data Lake Storage akış verilerine bağlanır. Dış depolama hizmetlerine bağlı olan ikinci iki seçenek, depolama alanına eklenen yeni dosyaları izlemenize ve içerikleri akışla işleyecek şekilde işletirecektir. 

Yapılandırılmış akış, giriş verilerine seçim, projeksiyon, toplama, Pencereleme ve başvuru veri çerçevesi ile birleştirme gibi işlemler uyguladığınızda uzun süre çalışan bir sorgu oluşturur. Daha sonra, sonuçları dosya depolama alanına (Azure Storage blob 'Ları veya Data Lake Storage) veya özel kod (örneğin, SQL veritabanı veya Power BI) kullanarak herhangi bir veri deposuna çıktı olarak alırsınız. Yapılandırılmış akış, HDInsight 'ta hata ayıklama için oluşturulan verileri görebileceğiniz şekilde, yerel olarak hata ayıklama için konsola ve bellek içi tabloya yönelik çıktıyı da sağlar. 

![HDInsight ve Spark yapılandırılmış akışı ile akış Işleme](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> Spark yapısal akışı Spark akışını (DStreams) değiştiriyor. İleri, yapılandırılmış akış geliştirmeler ve bakım alacak, ancak DStreams yalnızca bakım modunda olacak. Yapılandırılmış akış şu anda özellik dışında, desteklediği kaynaklar ve havuzlar için DStreams olarak, uygun Spark akış işleme seçeneğini belirlemek için gereksinimlerinizi değerlendirin. 

## <a name="streams-as-tables"></a>Tablolar olarak akışlar

Spark yapılandırılmış akış, bir veri akışını derinlemesine sınırsız bir tablo olarak temsil eder, diğer bir deyişle, yeni veri geldiğinde tablo büyümeye devam eder. Bu *giriş tablosu* , uzun süre çalışan bir sorgu tarafından sürekli olarak işlenir ve bir *Çıkış tablosuna*gönderilir:

![Yapılandırılmış akış kavramı](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

Yapılandırılmış akışta, veriler sisteme ulaşır ve hemen bir giriş tablosuna alınır. Bu giriş tablosuna yönelik işlemler gerçekleştiren sorguları (DataFrame ve DataSet API 'Leri kullanarak) yazarsınız. Sorgu çıktısı başka bir tablo, *Sonuçlar tablosu*oluşturur. Sonuçlar tablosu, bir dış veri deposu için veri çizdiğiniz, örneğin ilişkisel bir veritabanının sonuçlarını içerir. Giriş tablosundan verilerin işlendiği zaman zamanlaması, *tetikleyici aralığı*tarafından denetlenir. Varsayılan olarak, tetikleyici aralığı sıfırdır, bu nedenle yapılandırılmış akış, verileri ulaştığı anda işlemeye çalışır. Uygulamada, bu, yapılandırılmış akış önceki sorgunun çalışmasını işlemeyi tamamladıktan sonra, yeni alınan tüm verilere karşı başka bir işlem çalıştırması başlattığı anlamına gelir. Tetikleyiciyi, zaman tabanlı toplu işlerle işlenmek üzere bir aralıkta çalışacak şekilde yapılandırabilirsiniz. 

Sonuçlar tablolarındaki veriler, yalnızca sorgunun son işlendiği zamandan beri yeni olan verileri (*ekleme modu*) içerebilir veya tablo, akış q 'dan bu yana tüm çıkış verilerini içermesi halinde tablo tamamen yenilenebilir. uıın başladı (*mod*).

### <a name="append-mode"></a>Ekleme modu

Append modunda, yalnızca son sorgu çalıştırmasından sonra sonuçlar tablosuna eklenen satırlar sonuçlar tablosunda bulunur ve dış depolamaya yazılır. Örneğin, en basit sorgu yalnızca girdi tablosundaki tüm verileri, değiştirilmemiş sonuçlar tablosuna kopyalar. Tetikleyici aralığı her geçtiğinde, yeni veriler işlenir ve bu yeni verileri temsil eden satırlar sonuçlar tablosunda görüntülenir. 

Bir termostat gibi Sıcaklık sensörlerinden telemetri işlemekte olduğunuz bir senaryoyu düşünün. İlk tetikleyicinin, cihaz 1 ' de 95 derecenin okuduğu bir olayın 00:01. saatinde tek bir olay işlediğini varsayın. Sorgunun ilk tetikleyicisinde, sonuçlar tablosunda yalnızca 00:01 olan satır görüntülenir. Başka bir olay ulaştığında, yalnızca yeni satır 00:02 saat olur ve sonuç tablosu yalnızca bir satır içerir. 00:02

![Yapılandırılmış akış ekleme modu](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Append modunu kullanırken sorgunuz, tahminleri (ilgili olduğu sütunları seçerek), filtrelemeye (yalnızca belirli koşullara uyan satırları seçerek) veya katılımını (statik bir arama tablosundaki verilerle verileri genişlettirecek) uyguluyor. Ekleme modu, yalnızca ilgili yeni veri noktalarını dış depolamaya itmeyi kolaylaştırır.

### <a name="complete-mode"></a>Mod Tamam

Bu kez, tüm modunu kullanarak aynı senaryoyu göz önünde bulundurun. Tam modda tüm çıkış tablosu her tetikleyicide yenilenir, böylece tablo yalnızca en son tetikleyici çalıştırmasından değil, tüm çalıştırmalardan veri içerir. Verileri değiştirilmemiş şekilde giriş tablosundan sonuçlar tablosuna kopyalamak için, tamamlanmış modunu kullanabilirsiniz. Tetiklenen her çalıştırmada, yeni sonuç satırları önceki tüm satırlarla birlikte görüntülenir. Çıkış sonuçları tablosu, sorgunun başlamasından bu yana toplanan tüm verilerin depolanmasına ve sonunda belleğin tükenmesine neden olur. Tamamlanma modu, gelen verileri bir şekilde özetleyen toplam sorgularla birlikte kullanılmak üzere tasarlanmıştır ve bu nedenle her tetikleyici, sonuçlar tablosu yeni bir Özet ile güncelleştirilir. 

Şimdiye kadar beş saniyelik verilerin zaten işlenmiş olduğunu varsayın ve altıncı saniye için verileri işlemek zaman alabilir. Giriş tablosunda 00:01 saati ve 00:03 zamanı için olaylar bulunur. Bu örnek sorgu hedefi, cihazın ortalama sıcaklığını beş saniyede bir vermektir. Bu sorgunun uygulanması, her 5 saniyelik pencerede kalan tüm değerleri alan bir toplama uygular, sıcaklığın ortalığını ve bu aralığa göre ortalama sıcaklık için bir satır üretir. İlk 5 saniyelik pencerenin sonunda, iki tanımlama grubu vardır: (00:01, 1, 95) ve (00:03, 1, 98). Bu nedenle, Windows 00:00-00:05 için toplama, 96,5 derecenin ortalama sıcaklığını içeren bir tanımlama grubu oluşturur. Sonraki 5 saniyelik pencerede 00:06 sırasında yalnızca bir veri noktası bulunur, bu nedenle elde edilen ortalama sıcaklık 98 derece olur. Bu zaman 00:10, tam modu kullanarak, sorgu yalnızca yenilerini değil, tüm toplanmış satırları çıktıdığı için, sonuçlar tablosu hem Windows 00:00-00:05 hem de 00:05-00:10 için satırlara sahiptir. Bu nedenle, yeni pencereler eklendikçe sonuçlar tablosu büyümeye devam eder.    

![Yapılandırılmış akış tamamlanma modu](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Tüm sorguların tamamı, tablonun sınır olmadan büyümesine neden olur.  Önceki örnekte, zaman içindeki sıcaklığın ortalamasını değil, cihaz KIMLIĞI tarafından bunun ortalaması altında düşünün. Sonuç tablosu, bu cihazdan alınan tüm veri noktalarında cihaz için Ortalama sıcaklığa sahip sabit sayıda satır (cihaz başına bir tane) içerir. Yeni sıcaklıklar alındığından, tablodaki ortalamalar her zaman geçerli olacak şekilde sonuçlar tablosu güncellenir. 

## <a name="components-of-a-spark-structured-streaming-application"></a>Spark yapılandırılmış akış uygulamasının bileşenleri

Basit örnek bir sorgu, saat-uzun pencereler için sıcaklık okumaları özetleyebilir. Bu durumda, veriler Azure Storage 'daki JSON dosyalarında (HDInsight kümesi için varsayılan depolama alanı olarak eklenir) depolanır:

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Bu JSON dosyaları, HDInsight kümesinin kapsayıcısının `temps` altındaki alt klasörde depolanır. 

### <a name="define-the-input-source"></a>Giriş kaynağını tanımlama

İlk olarak, verilerin kaynağını ve bu kaynak için gereken tüm ayarları açıklayan bir veri çerçevesini yapılandırın. Bu örnek, Azure Storage 'daki JSON dosyalarından çizilir ve okuma sırasında bunlara bir şema uygular.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Sorguyu Uygula

Ardından, akış veri çerçevesine yönelik istenen işlemleri içeren bir sorgu uygulayın. Bu durumda, bir toplama tüm satırları 1 saat Windows 'a gruplandırır ve sonra bu 1 saatlik pencerede minimum, ortalama ve maksimum sıcaklıkları hesaplar.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Çıkış havuzunu tanımlama

Sonra, her tetikleyici aralığı içindeki sonuçlar tablosuna eklenen satırlar için hedefi tanımlayın. Bu örnek, tüm satırları daha sonra daha sonra daha sonra daha `temps` sonra mini bellekli bir tabloya çıkarır. Çıkış modunu tamamen doldurun tüm pencereler için tüm satırların her seferinde çıkış olmasını sağlar.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Sorguyu Başlat

Akış sorgusunu başlatın ve sonlandırma sinyali alınana kadar çalıştırın. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Sonuçları görüntüleme

Sorgu çalışırken, aynı mini oturumda sorgu sonuçlarının depolandığı `temps` tabloya karşı bir mini SQL sorgusu çalıştırabilirsiniz. 

    select * from temps

Bu sorgu aşağıdakilere benzer sonuçlar verir:


| penceresine |  Min (geçici) | Ort (geçici) | en fazla (geçici) |
| --- | --- | --- | --- |
|{uıtostart ': u ' 2016-07-26T02:00:00.000 Z ', uı'end '... |    95 |    95,231579 | 99 |
|{uıtostart ': u ' 2016-07-26T03:00:00.000 Z ', uı'end '...  |95 |   96.023048 | 99 |
|{uıtostart ': u ' 2016-07-26T04:00:00.000 Z ', uı'end '...  |95 |   96,797133 | 99 |
|{uıtostart ': u ' 2016-07-26T05:00:00.000 Z ', uı'end '...  |95 |   96,984639 | 99 |
|{uıtostart ': u ' 2016-07-26T06:00:00.000 Z ', uı'end '...  |95 |   97,014749 | 99 |
|{uıtostart ': u ' 2016-07-26T07:00:00.000 Z ', uı'end '...  |95 |   96,980971 | 99 |
|{uıtostart ': u ' 2016-07-26T08:00:00.000 Z ', uı'end '...  |95 |   96,965997 | 99 |  

Spark yapısal akış API 'SI ile birlikte, desteklediği giriş veri kaynakları, işlemler ve çıkış havuzları ile ilgili ayrıntılar için bkz. [Apache Spark yapısal akış Programlama Kılavuzu](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Checkişaret ve sonradan yazma günlükleri

Yapılandırılmış akış esneklik ve hataya dayanıklılık sağlamak için, akış işlemenin düğüm hatalarıyla birlikte kesintisiz bir şekilde devam etmesini sağlamak için *denetim noktası* kullanır. HDInsight 'ta Spark, Azure Storage veya Data Lake Storage dayanıklı depolama için denetim noktaları oluşturur. Bu denetim noktaları, akış sorgusuyla ilgili ilerleme bilgilerini depolar. Ayrıca, yapılandırılmış akış bir *yazma öncesi günlük* (Wal) kullanır. WAL alınan verileri yakalar, ancak henüz bir sorgu tarafından işlenmemiştir. Bir hata oluşursa ve işlem WAL 'den yeniden başlatılırsa, kaynaktan alınan tüm olaylar kaybedilmez.

## <a name="deploying-spark-streaming-applications"></a>Spark akış uygulamalarını dağıtma

Genellikle bir JAR dosyasına yerel olarak bir Spark akış uygulaması derleyin ve ardından JAR dosyasını HDInsight üzerinde Spark 'a dağıtarak, JAR dosyasını HDInsight kümenize bağlı varsayılan depolama alanına kopyalarsınız. Bir POST işlemi kullanarak, uygulamanızı kümenizde bulunan [Apache Livy](https://livy.incubator.apache.org/) REST API 'leriyle başlatabilirsiniz. GÖNDERI gövdesi, JAR 'nizin yolunu sağlayan bir JSON belgesi, ana yöntemi, akış uygulamasını tanımlayan ve çalıştıran sınıfın adı ve isteğe bağlı olarak işin kaynak gereksinimleri (yürütme sayısı, bellek ve çekirdek sayısı gibi) içerir ve uygulama kodunuzun gerektirdiği tüm yapılandırma ayarlarını.

![Spark akış uygulaması dağıtma](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Tüm uygulamaların durumu, bir al uç noktasına karşı bir GET isteğiyle de denetlenebilir. Son olarak, çalışan bir uygulamayı, LIVY uç noktasına karşı SILME isteği vererek sonlandırabilirsiniz. LIVY API 'SI ile ilgili ayrıntılar için bkz. [Apache Livy Ile uzak işler](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta Apache Spark kümesi oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark yapılandırılmış akış Programlama Kılavuzu](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Apache LIVY ile Apache Spark işleri uzaktan başlatın](apache-spark-livy-rest-interface.md)
