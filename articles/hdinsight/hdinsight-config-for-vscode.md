---
title: Azure HDInsight yapılandırma ayarları başvurusu
description: Azure HDInsight uzantısının yapılandırmasını tanıtın.
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: a9a444c2557ea17114123cbd5084cbbd9fe6dac6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260019"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Azure HDInsight yapılandırma ayarları başvurusu

Visual Studio Code için Spark & Hive araçları uzantısı, yüksek oranda yapılandırılabilir. Bu sayfada, birlikte çalışabilmeniz için kullanabileceğiniz anahtar ayarları açıklanmaktadır.  

VS Code ayarlarla çalışma hakkında genel bilgi için, [Kullanıcı ve çalışma alanı ayarları](https://code.visualstudio.com/docs/getstarted/settings)' na ve önceden tanımlanmış değişken desteği hakkında bilgi için [değişkenler başvurusuna](https://code.visualstudio.com/docs/editor/variables-reference) bakın.

## <a name="open-the-azure-hdinsight-configuration"></a>Azure HDInsight yapılandırmasını açın

1. Çalışma alanı ayarları oluşturmak için önce bir klasör açın.
2. Tüm komutları göstermek için **CTRL + SHIFT + P** tuşlarına basın veya  ->  **komut paleti göster...** ' a gidin.
3. Arama **kümesi yapılandırması**.
4. Sol dizindeki **Uzantılar** ' ı genişletin ve **HDInsight yapılandırması**' nı seçin.

 ![HDI yapılandırma resmi](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>Genel ayarlar   

|  Özellik   | Varsayılan | Description   |
| ----- | ----- |----- |
| HDInsight: Azure ortamı | Azure | Azure ortamı |
| HDInsight: açık anket bağlantısını devre dışı bırak | İşaretli | HDInsight anketini açmayı etkinleştir/devre dışı bırak |
| HDInsight: etkinleştir Pyspark yükleme | İşaretlemeyin | Pyspark yüklemesinin atlanmasını etkinleştir/devre dışı bırak |
| HDInsight: oturum açma Ipuçları etkin | İşaretlemeyin | Bu seçenek işaretlendiğinde, Azure 'da oturum açarken bir istem olacaktır |
| HDInsight: önceki uzantı sürümü | Geçerli uzantının sürüm numarasını görüntüle | Önceki uzantı sürümünü göster|
| HDInsight: sonuçlar yazı tipi ailesi | -Apple-System, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, DROID sans, sans-serif | Sonuçlar Kılavuzu için yazı tipi ailesini ayarlayın; Düzenleyici yazı tipini kullanmak için boş olarak ayarlayın |
| HDInsight: sonuçlar yazı tipi boyutu | 13 |Sonuçların yazı tipi boyutunu ayarla kız Düzenleyici boyutunu kullanmak için boş olarak ayarlayın |
| HDInsight kümesi: bağlı küme | -- | Bağlı kümeler URL 'leri. Ayrıca, ayarlanacak JSON dosyasını düzenleyebilir |
| HDInsight Hive: yerelleştirmeyi Uygula | İşaretlemeyin | Seçim VSCode 'un yapılandırılmış yerel ayarlarını yerelleştirme için yapılandırma seçenekleri (ayarların etkili olması için VSCode 'u yeniden başlatması gerekir)|
| HDInsight Hive: ekleme üst bilgilerini Kopyala | İşaretlemeyin | Seçim Sonuçlar görünümünden sonuçları kopyalamak için yapılandırma seçeneği |
| HDInsight Hive: kopyalama yeni satırı Kaldır | İşaretli | Seçim Sonuçlar görünümünden çok satırlı sonuçları kopyalamak için yapılandırma seçenekleri |
| HDInsight Hive › biçimi: sütunlardaki sütun tanımlarını Hizala | İşaretlemeyin | Sütun tanımının hizalanması gerekir |
| HDInsight Hive › biçimi: DataType büyük harfleri | yok | Veri türleri büyük harf, küçük harf veya hiçbiri olarak biçimlendirilmelidir (biçimlendirilmedi) |
| HDInsight Hive › biçimi: anahtar sözcük büyük harfleri | yok | Anahtar sözcükler büyük harf, küçük harf veya hiçbiri olarak biçimlendirilmelidir (biçimlendirilmedi) |
| HDInsight Hive › biçimi: sonraki deyimden önce virgül yerleştir | İşaretlemeyin | ' Mycolumn1 ' sonunda değil, bir listedeki her bir deyimin başına virgül yerleştirilmelidir (örneğin, ', mycolumn2 ')|
| HDInsight Hive › biçimi: Select Deyimbaşvurularını yeni satıra yerleştir | İşaretlemeyin | Bir SELECT deyimindeki nesnelere başvurular ayrı satırlara bölünmeli mi? Örneğin, ' SELECT C1, C2 'DAN T1 ' ve C1 ve C2 öğelerinin her ikisi de ayrı satırlarda olacaktır
| HDInsight Hive: günlük hata ayıklama bilgileri | İşaretlemeyin | Seçim Hata ayıklama çıkışını VS Code konsoluna Kaydet (yardım-> değiştirme Geliştirici Araçları) 
| HDInsight Hive: varsayılan Iletiler açık | İşaretli | İletiler bölmesinin varsayılan olarak açık olması için true; kapalı için false|
| HDInsight Hive: sonuçlar yazı tipi ailesi | -Apple-System, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, DROID sans, sans-serif | Sonuçlar Kılavuzu için yazı tipi ailesini ayarlayın; Düzenleyici yazı tipini kullanmak için boş olarak ayarlayın |
| HDInsight Hive: sonuçlar yazı tipi boyutu | 13 | Sonuçlar Kılavuzu için yazı tipi boyutunu ayarlayın; Düzenleyici boyutunu kullanmak için boş olarak ayarlayın |
| HDInsight Hive › CSV olarak Kaydet: üstbilgileri Içer | İşaretli | Seçim Doğru olduğunda, sonuçlar CSV olarak kaydedilirken sütun başlıkları dahil edilir |
| HDInsight Hive: kısayollar | -- | Sonuçlar penceresiyle ilgili kısayollar |
| HDInsight Hive: toplu Iş süresini göster| İşaretlemeyin | Seçim Bireysel toplu işlemler için yürütme süresi gösterilmelidir |
| HDInsight Hive: bölme bölmesi seçimi | ileri | Seçim Yeni sonuç bölmelerinin sütun içinde açılacağı yapılandırma seçenekleri |
| HDInsight Işi gönderme: küme conf | -- | Küme yapılandırması |
| HDInsight Iş gönderimi: Livy conf | -- | Livy yapılandırması. GÖNDERI/toplu işler |
| HDInsight Jupyter: sonuçları sona Ekle| İşaretli | Sonuçların sonuçlar penceresine eklenip eklenmeyeceğini, şifresiz ve görüntüleme. |
| HDInsight Jupyter: Diller | -- | Dil başına varsayılan ayarlar. |
| HDInsight Jupyter › log: verbose | İşaretlemeyin | Ayrıntılı günlük kaydını etkinleştir |
| HDInsight Jupyter › Not Defteri: başlangıç bağımsız değişkenleri | Öğe eklenebilir | ' Jupyter Not defteri ' komut satırı bağımsız değişkenleri. Her bağımsız değişken dizideki ayrı bir öğedir. Tam liste türü için bir Terminal penceresinde ' Jupyter Not defteri--help '. |
| HDInsight Jupyter › Not Defteri: başlangıç klasörü | $ {workspaceRoot} |-- |
| HDInsight Jupyter: Python uzantısı etkin | İşaretli | PySpark etkileşimli işleri gönderirken MS-Python uzantısının Python-Interactive-Window penceresini kullanın. Aksi takdirde, kendi jupyıter penceremizi kullanın |
| HDInsight Spark.NET: 7z | C:\Program Files\7-Zip | 7z.exe> yolu < |
| HDInsight Spark.NET: HADOOP_HOME | D:\winutils | Yalnızca Windows işletim sistemi> bin\winutils.exe <yolu |
| HDInsight Spark.NET: JAVA_HOME | C:\Program Files\Java\ jdk1.8.0_201 \ | Java giriş yolu|
| HDInsight Spark.NET: SCALA_HOME | C:\Program Files (x86) \scala\ | Scala giriş yolu |
| HDInsight Spark.NET: SPARK_HOME | D:\spark-2.3.3-bin-hadoop2.7\ | Spark Home 'ın yolu |
| Hive: kalıcı sorgu sonucu sekmeleri | İşaretlemeyin | Hive Persistqueryresultsekmeleri |
| Hive: bölme bölmesi seçimi | ileri | Seçim Yeni sonuç bölmelerinin sütun içinde açılacağı yapılandırma seçenekleri |
| Hive etkileşimli: yürütülebilir klasörü kopyala | İşaretlemeyin | Hive etkileşimli hizmet çalışma zamanı klasörünü kullanıcının tmp klasörüne kopyalarsanız |
| HQL etkileşimli sunucu: sarmalayıcı bağlantı noktası | 13424 | Hive etkileşimli hizmet bağlantı noktası |
| HQL dil sunucusu: dil sarmalayıcı bağlantı noktası | 12342 | Hive dil hizmeti bağlantı noktası sunucuları dinler. |
| HQL dil sunucusu: en fazla sorun sayısı | 100 | Sunucu tarafından üretilen en fazla sorun sayısını denetler. |
| SYNAPSE Spark COMPUTE: SYNAPSE Spark Işlem Azure ortamı | adet | SYNAPSE Spark Işlem Azure ortamı |
| SYNAPSE Spark Pool Iş gönderimi: Livy conf | -- | Livy yapılandırması. GÖNDERI/toplu işler
| SYNAPSE Spark Pool Iş gönderimi: SYNAPSE Spark havuz kümesi conf | -- | SYNAPSE Spark havuzu yapılandırması |


## <a name="next-steps"></a>Sonraki adımlar

- VSCode için Azure HDInsight hakkında daha fazla bilgi için bkz. [Spark & Hive Visual Studio Code araçları](https://docs.microsoft.com/sql/big-data-cluster/spark-hive-tools-vscode).
- Visual Studio Code için Spark & Hive kullanmayı gösteren bir video için bkz. [spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).