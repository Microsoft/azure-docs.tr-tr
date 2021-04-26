---
title: C#, Apache Hive & Apache Pig on Apache Hadoop-Azure HDInsight
description: Azure HDInsight 'ta Apache Hive ve Apache Pig akışı ile C# Kullanıcı tanımlı işlevleri (UDF) kullanmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/06/2019
ms.openlocfilehash: b5682ff58ad827f5a165342f11f03fb49bbe6d2d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867891"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>HDInsight 'ta Apache Hadoop üzerinde Apache Hive ve Apache Pig ile C# Kullanıcı tanımlı işlevleri kullanma

HDInsight üzerinde [Apache Hive](https://hive.apache.org) ve [Apache Pig](https://pig.apache.org) ile C# Kullanıcı tanımlı işlevleri (UDF) kullanmayı öğrenin.

> [!IMPORTANT]
> Bu belgedeki adımlar Linux tabanlı HDInsight kümeleriyle çalışır. Linux, HDInsight sürüm 3.4 ve üzerinde kullanılan tek işletim sistemidir. Daha fazla bilgi için bkz. [HDInsight bileşen sürümü oluşturma](../hdinsight-component-versioning.md).

Hem Hive hem de Pig, işlenmek üzere dış uygulamalara veri geçirebilir. Bu işlem _akış_ olarak bilinir. .NET uygulaması kullanılırken, veriler STDIN üzerindeki uygulamaya geçirilir ve uygulama STDOUT üzerinde sonuçları döndürür. STDıN ve STDOUT 'tan okumak ve yazmak için `Console.ReadLine()` `Console.WriteLine()` bir konsol uygulaması kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* .NET Framework 4,5 ' i hedefleyen C# kodu yazma ve oluşturma hakkında bir benzerlik.

    İstediğiniz IDE 'yi kullanın. [Visual Studio](https://www.visualstudio.com/vs) veya [Visual Studio Code](https://code.visualstudio.com/)önerilir. Bu belgedeki adımlarda Visual Studio 2019 kullanılır.

* . Exe dosyalarını kümeye yüklemek ve Pig ve Hive işlerini çalıştırmak için bir yol. Visual Studio, [Azure PowerShell](/powershell/azure)ve [Azure CLI](/cli/azure/install-azure-cli) [için Data Lake araçlar](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md)önerilir. Bu belgedeki adımlarda, dosyaları karşıya yüklemek ve örnek Hive sorgusunu çalıştırmak için Visual Studio için Data Lake araçları kullanılır.

    Hive sorguları çalıştırmanın diğer yolları hakkında bilgi için bkz. [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](hdinsight-use-hive.md).

* HDInsight kümesi üzerinde Hadoop. Küme oluşturma hakkında daha fazla bilgi için bkz. [HDInsight kümeleri oluşturma](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>HDInsight üzerinde .NET

*Linux tabanlı HDInsight* kümeleri mono kullanır [( https://mono-project.com)](https://mono-project.com) .NET uygulamalarını çalıştırmak için). Tek bir sürüm 4.2.1, HDInsight sürüm 3,6 ' ye dahildir.

.NET Framework sürümleriyle mono uyumluluğu hakkında daha fazla bilgi için bkz. [mono uyumluluk](https://www.mono-project.com/docs/about-mono/compatibility/).

.NET Framework sürümü ve HDInsight sürümlerine dahil olan Mono hakkında daha fazla bilgi için bkz. [HDInsight bileşen sürümleri](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>C projelerini oluşturma \#

Aşağıdaki bölümlerde, Apache Hive UDF ve Apache Pig UDF için Visual Studio 'da C# projesi oluşturma açıklanır.

### <a name="apache-hive-udf"></a>Apache Hive UDF

Apache Hive UDF için C# projesi oluşturmak için:

1. Visual Studio 'Yu başlatın.

2. **Yeni proje oluştur**' u seçin.

3. **Yeni proje oluştur** penceresinde **konsol uygulaması (.NET Framework)** şablonunu (C# sürümü) seçin. Sonra **İleri**’yi seçin.

4. **Yeni projeyi yapılandırın** penceresinde, *hivecsharp* için bir **Proje adı** girin ve yeni projenin kaydedileceği bir konum oluşturun veya **konuma** gidin. Ardından **Oluştur**’u seçin.

5. Visual Studio IDE 'de, *program. cs* ' nin içeriğini aşağıdaki kodla değiştirin:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

6. Menü çubuğundan   >  Projeyi derlemek için derleme **Build Solution** ' ı seçin.

7. Çözümü kapatın.

### <a name="apache-pig-udf"></a>Apache Pig UDF

Apache Hive UDF için C# projesi oluşturmak için:

1. Visual Studio'yu açın.

2. **Başlangıç** penceresinde **Yeni proje oluştur**' u seçin.

3. **Yeni proje oluştur** penceresinde **konsol uygulaması (.NET Framework)** şablonunu (C# sürümü) seçin. Sonra **İleri**’yi seçin.

4. **Yeni projeyi yapılandırın** penceresinde, *pigudf* için bir **Proje adı** girin ve yeni projenin kaydedileceği bir konum oluşturun veya **konuma** gidin. Ardından **Oluştur**’u seçin.

5. Visual Studio IDE 'de, *program. cs* ' nin içeriğini aşağıdaki kodla değiştirin:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Bu kod, Pig adresinden gönderilen satırları ayrıştırır ve ile başlayan satırları yeniden biçimlendirir `java.lang.Exception` .

6. Menü çubuğundan   >  Projeyi derlemek için derleme **Build Solution** ' ı seçin.

7. Çözümü açık bırakın.

## <a name="upload-to-storage"></a>Depolama alanına yükleme

Ardından, Hive ve Pig UDF uygulamalarını bir HDInsight kümesindeki depolamaya yükleyin.

1. Visual Studio 'da Sunucu Gezgini **görüntüle**' ye gidin  >  .

1. **Sunucu Gezgini**' den **Azure**' a sağ tıklayın, **Microsoft Azure aboneliğine Bağlan**' ı seçin ve oturum açma işlemini doldurun.

1. Bu uygulamayı dağıtmak istediğiniz HDInsight kümesini genişletin. Metin **(varsayılan depolama hesabı)** içeren bir giriş listelenir.

    :::image type="content" source="./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png" alt-text="Varsayılan depolama hesabı, HDInsight kümesi, Sunucu Gezgini" border="true":::

    * Bu girdi genişletilirse, küme için varsayılan depolama alanı olarak bir **Azure depolama hesabı** kullanıyorsunuz demektir. Küme için varsayılan depolamada bulunan dosyaları görüntülemek için, girdiyi genişletin ve **(varsayılan kapsayıcı)** öğesine çift tıklayın.

    * Bu girdi genişletilirse, küme için varsayılan depolama alanı olarak **Azure Data Lake Storage** kullanırsınız. Küme için varsayılan depolamada bulunan dosyaları görüntülemek için **(varsayılan depolama hesabı)** girişine çift tıklayın.

1. . Exe dosyalarını karşıya yüklemek için aşağıdaki yöntemlerden birini kullanın:

    * Bir **Azure depolama hesabı** kullanıyorsanız, **karşıya yükleme blobu** simgesini seçin.

        :::image type="content" source="./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png" alt-text="Yeni proje için HDInsight karşıya yükleme simgesi" border="true":::

        **Yeni dosyayı karşıya yükle** Iletişim kutusundaki **dosya adı** altında, **Araştır**' ı seçin. **Blobu karşıya yükle** iletişim kutusunda, *hivecsharp* proje için *bin\Debug* klasörüne gidin ve *HiveCSharp.exe* dosyasını seçin. Son olarak, **Aç** ' ı ve ardından **Tamam** ' ı seçerek karşıya yüklemeyi tamamlayabilirsiniz.

    * **Azure Data Lake Storage** kullanıyorsanız, dosya listesinde boş bir alana sağ tıklayın ve ardından **karşıya yükle**' yi seçin. Son olarak, *HiveCSharp.exe* dosyasını seçin ve **Aç**' ı seçin.

    *HiveCSharp.exe* karşıya yükleme işlemi tamamlandıktan sonra, *PigUDF.exe* dosyası için karşıya yükleme işlemini tekrarlayın.

## <a name="run-an-apache-hive-query"></a>Bir Apache Hive sorgusu çalıştırma

Artık Hive UDF uygulamanızı kullanan bir Hive sorgusu çalıştırabilirsiniz.

1. Visual Studio 'da Sunucu Gezgini **görüntüle**' ye gidin  >  .

2. **Azure** seçeneğini ve sonra **HDInsight** seçeneğini genişletin.

3. *Hivecsharp* uygulamasını dağıttığınız kümeye sağ tıklayın ve ardından **Hive sorgusu yaz**' ı seçin.

4. Hive sorgusu için aşağıdaki metni kullanın:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > `add file`Kümeniz için kullanılan varsayılan depolama türüyle eşleşen deyimin açıklamasını kaldırın.

    Bu sorgu,, `clientid` `devicemake` ve alanlarını seçer `devicemodel` `hivesampletable` ve ardından alanları *HiveCSharp.exe* uygulamasına geçirir. Sorgu, uygulamanın, ve olarak depolanan üç alanı döndürmesini bekler `clientid` `phoneLabel` `phoneHash` . Sorgu ayrıca varsayılan depolama kapsayıcısının kökünde *HiveCSharp.exe* bulmayı bekler.

5. Varsayılan **etkileşimli** öğesini **Batch**'e geçirin ve ardından işi HDInsight kümesine göndermek Için **Gönder** ' i seçin. **Hive Iş Özeti** penceresi açılır.

6. **Iş durumu** **tamamlandı** olarak değişene kadar Özeti yenilemek için **Yenile** ' yi seçin. İş çıktısını görüntülemek için **Iş çıktısı**' nı seçin.

## <a name="run-an-apache-pig-job"></a>Apache Pig işini çalıştırma

Ayrıca, Pig UDF uygulamanızı kullanan bir Pig işi de çalıştırabilirsiniz.

1. HDInsight kümenize bağlanmak için SSH kullanın. (Örneğin, komutunu çalıştırın `ssh sshuser@<clustername>-ssh.azurehdinsight.net` .) Daha fazla bilgi için bkz. [SSH Withhdınsight kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pig komut satırını başlatmak için aşağıdaki komutu kullanın:

    ```shell
    pig
    ```

    Bir `grunt>` istem görüntülenir.

3. .NET Framework uygulamasını kullanan bir Pig işini çalıştırmak için aşağıdakileri girin:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    `DEFINE`İfade,PigUDF.exeuygulaması için bir diğer ad oluşturur `streamer` ve  `CACHE` bunu küme için varsayılan depolamadan yükler. Daha sonra, `streamer` `STREAM` içinde bulunan tek satırları işlemek `LOG` ve verileri bir dizi sütun olarak döndürmek için işleciyle birlikte kullanılır.

    > [!NOTE]
    > Akış için kullanılan uygulama adının, \` diğer ad ve ile birlikte kullanıldığında ' (tek tırnak) karakteriyle çevrelenmelidir `SHIP` .

4. Son satırı girdikten sonra iş başlamalıdır. Aşağıdaki metne benzer bir çıktı döndürür:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. `exit`Pig çıkmak için kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede Hive ve Pig 'dan HDInsight üzerinde bir .NET Framework uygulamasının nasıl kullanılacağını öğrendiniz. Hive ve Pig ile Python kullanmayı öğrenmek isterseniz bkz. [HDInsight 'ta Apache Hive ve Apache Pig Ile Python kullanma](python-udf-hdinsight.md).

Hive kullanmanın diğer yolları için ve MapReduce kullanma hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [HDInsight ile Apache Hive kullanma](hdinsight-use-hive.md)
* [HDInsight ile MapReduce kullanma](hdinsight-use-mapreduce.md)
* [Pig Latin temelleri](https://pig.apache.org/docs/latest/basic.html)
