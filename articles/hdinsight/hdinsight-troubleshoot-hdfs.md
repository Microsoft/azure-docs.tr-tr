---
title: Azure HDInsight 'ta sorun giderme
description: Azure HDInsight ile çalışma hakkında sık sorulan soruların yanıtlarını alın.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: c725e7b4e7af33181cb616d2bce2ae19521a5e7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871886"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Azure HDInsight kullanarak Apache Hadoop HDFS sorunlarını giderme

Hadoop Dağıtılmış Dosya Sistemi (bir) ile çalışırken popüler sorunlar ve çözümleri öğrenin. Komutların tam listesi için, bkz... [komut Kılavuzu](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) ve [dosya sistemi kabuğu Kılavuzu](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Yerel olarak bir küme içinden erişim Nasıl yaparım? mi?

### <a name="issue"></a>Sorun

Azure Blob depolama veya HDInsight kümesinin içinden Azure Data Lake Storage yerine komut satırı ve uygulama kodundan yerel olarak erişin.

### <a name="resolution-steps"></a>Çözüm adımları

1. Komut isteminde, `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` aşağıdaki komutta olduğu gibi, harfine kullanın:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. Kaynak koddan `hdfs://mycluster/` Aşağıdaki örnek uygulamada olduğu gibi, URI 'yi tam olarak kullanın:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Aşağıdaki komutla, HDInsight kümesinde derlenen. jar dosyasını (örneğin, adlı bir dosya `java-unit-tests-1.0.jar` ) çalıştırın:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>Blob üzerinde yazma için depolama özel durumu

### <a name="issue"></a>Sorun

`hadoop` `hdfs dfs` Bir HBase kümesinde ~ 12 GB veya daha büyük olan dosyaları yazmak için veya komutlarını kullanırken, şu hata boyunca karşılaşabilirsiniz:

```error
ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
copyFromLocal: java.io.IOException
        at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
        at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
        at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
        at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
        ... 7 more
```

### <a name="cause"></a>Nedeni

HDInsight kümelerindeki HBase, Azure depolama 'ya yazarken varsayılan olarak 256 KB 'lik bir blok boyutuna sahiptir. HBase API 'Leri veya REST API 'Leri için çalışırken, `hadoop` veya `hdfs dfs` komut satırı yardımcı programları kullanılırken bir hatayla sonuçlanır.

### <a name="resolution"></a>Çözüm

`fs.azure.write.request.size`Daha büyük bir blok boyutunu belirtmek için kullanın. Bu değişikliği, parametresini kullanarak kullanım başına temelinde yapabilirsiniz `-D` . Aşağıdaki komut, komutuyla bu parametreyi kullanan bir örnektir `hadoop` :

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Ayrıca, `fs.azure.write.request.size` Apache ambarı 'nı kullanarak genel değeri de artırabilirsiniz. Aşağıdaki adımlar, ambarı Web Kullanıcı arabirimindeki değeri değiştirmek için kullanılabilir:

1. Tarayıcınızda kümeniz için ambarı Web Kullanıcı arabirimine gidin. URL, `https://CLUSTERNAME.azurehdinsight.net` burada `CLUSTERNAME` kümenizin adıdır. İstendiğinde, kümenin yönetici adını ve parolasını girin.
2. Ekranın sol tarafında, bu ' ı seçin ve ardından **configs** **sekmesini seçin.**
3. **Filter...** alanına, girin `fs.azure.write.request.size` .
4. 262144 (256 KB) değerini yeni değerle değiştirin. Örneğin, 4194304 (4 MB).

    :::image type="content" source="./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png" alt-text="Ambarı Web Kullanıcı arabirimi üzerinden değerin değiştirilmesinin görüntüsü" border="false":::

Ambarı kullanma hakkında daha fazla bilgi için bkz. [Apache ambarı Web Kullanıcı arabirimini kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari.md).

## <a name="du"></a>du

[`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du)Komut, belirli bir dizinde bulunan dosya ve dizinlerin boyutlarını veya yalnızca bir dosya olması durumunda bir dosyanın uzunluğunu görüntüler.

`-s`Seçeneği, görüntülenmekte olan dosya uzunluklarının toplam özetini oluşturur.  
`-h`Seçeneği, dosya boyutlarını biçimlendirir.

Örnek:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>'yi

[-RM](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) komutu bağımsız değişken olarak belirtilen dosyaları siler.

Örnek:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]