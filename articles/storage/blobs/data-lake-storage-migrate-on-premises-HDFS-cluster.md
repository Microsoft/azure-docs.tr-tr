---
title: Şirket içi bir sunucudan Azure Storage 'a veri geçirmek için Azure Data Box kullanın
description: Şirket içi bir, Azure depolama 'ya veri geçirme
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 508c67f73bc0e11330b5772b1c1ba3f9bee5e231
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255678"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Şirket içi bir, Azure depolama 'ya veri geçirmek için Azure Data Box kullanma

Bir Data Box cihaz kullanarak Hadoop kümenizin şirket içi bir sunucudan Azure Storage 'a (BLOB depolama veya Data Lake Storage 2.) veri geçirebilirsiniz. 80-TB Data Box veya 770-TB Data Box Heavy arasından seçim yapabilirsiniz.

Bu makale, bu görevleri tamamlamanıza yardımcı olur:

> [!div class="checklist"]
> * Verilerinizi geçirmeye hazırlanın.
> * Verilerinizi bir Data Box veya Data Box Heavy cihazına kopyalayın.
> * Cihazı Microsoft 'a geri gönderin.
> * Verileri Data Lake Storage 2. üzerine taşıyın.

## <a name="prerequisites"></a>Prerequisites

Geçişi tamamlayabilmeniz için bu şeylere ihtiyacınız vardır.

* İki depolama hesabı; bir hiyerarşik ad alanı üzerinde etkin bir tane ve olmayan bir.

* Kaynak verilerinizi içeren bir şirket içi Hadoop kümesi.

* [Azure Data Box bir cihaz](https://azure.microsoft.com/services/storage/databox/).

  * [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) veya [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered)sıralayın. Cihazınızı düzenlerken, hiyerarşik ad alanları **etkin olmayan bir** depolama hesabı seçin. Bunun nedeni Data Box cihazların henüz Azure Data Lake Storage 2. doğrudan alımı desteklemezler. Bir depolama hesabına kopyalamanız ve sonra ADLS 2. hesaba ikinci bir kopya yapmanız gerekir. Bu yönergeler aşağıdaki adımlarda verilmiştir.

  * [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) veya [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) şirket içi bir ağa bağlayın.

Hazırsanız başlayalım.

## <a name="copy-your-data-to-a-data-box-device"></a>Verilerinizi bir Data Box cihazına kopyalayın

Verileriniz tek bir Data Box cihazına sığıyorsa, verileri Data Box cihazına kopyalayacaksınız. 

Veri boyutunuz Data Box cihazının kapasitesini aşarsa, [verileri birden çok Data Box cihaz arasında ayırmak için isteğe bağlı prosedürü](#appendix-split-data-across-multiple-data-box-devices) kullanın ve ardından bu adımı gerçekleştirin. 

Şirket içi işlem deponuzdan verileri bir Data Box cihazına kopyalamak için, birkaç şey ayarlayacaksınız ve ardından [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) aracını kullanacaksınız.

Blob/nesne depolamanın REST API 'Leri aracılığıyla Data Box cihazınıza veri kopyalamak için aşağıdaki adımları izleyin. REST API arabirimi, cihazı kümenize bir bir bir bir bir bir bir işlem deposu olarak görünür hale getirir.

1. Verileri REST aracılığıyla kopyalayabilmeniz için, Data Box veya Data Box Heavy REST arabirimine bağlanmak üzere güvenlik ve bağlantı temel öğelerini yapın. Data Box yerel Web Kullanıcı arabiriminde oturum açın ve **Bağlan ve Kopyala** sayfasına gidin. Cihazınız için Azure depolama hesabında, **erişim ayarları**' nın altında **rest**' i bulun ve seçin.

    !["Bağlan ve Kopyala" sayfası](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Erişim depolama hesabı ve verileri karşıya yükle iletişim kutusunda, **BLOB hizmeti uç noktasını** ve **depolama hesabı anahtarını**kopyalayın. Blob hizmeti uç noktasından `https://` ve sondaki eğik çizgiyi atlayın.

    Bu durumda, uç nokta: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Kullanacağınız URI 'nin konak bölümü: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Bir örnek için bkz. [HTTP ÜZERINDEN Rest 'e bağlanma](/azure/databox/data-box-deploy-copy-data-via-rest). 

     !["Depolama hesabına erişin ve verileri karşıya yükleyin" iletişim kutusu](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Her düğümdeki bitiş noktasını ve Data Box veya Data Box Heavy düğüm IP adresini `/etc/hosts` ' a ekleyin.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    DNS için başka bir mekanizma kullanıyorsanız, Data Box uç noktasının çözümlenebildiğinden emin olmalısınız.

4. @No__t-0 kabuk değişkenini `hadoop-azure` ve `azure-storage` jar dosyasının konumuna ayarlayın. Bu dosyaları, Hadoop yükleme dizini altında bulabilirsiniz.

    Bu dosyaların mevcut olup olmadığını öğrenmek için şu komutu kullanın: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. @No__t-0 yer tutucusunu, Hadoop 'yi yüklediğiniz dizinin yoluyla değiştirin. Tam olarak nitelenmiş yollar kullandığınızdan emin olun.

    Örnekler:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar``azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Veri kopyalama için kullanmak istediğiniz depolama kapsayıcısını oluşturun. Ayrıca, bu komutun bir parçası olarak bir hedef dizin belirtmeniz gerekir. Bu noktada bu bir kukla hedef dizin olabilir.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * @No__t-0 yer tutucusunu blob hizmeti uç noktanızın adıyla değiştirin.

    * @No__t-0 yer tutucusunu, hesabınızın erişim anahtarıyla değiştirin.

    * @No__t-0 yer tutucusunu kapsayıcının adıyla değiştirin.

    * @No__t-0 yer tutucusunu, verilerinizi kopyalamak istediğiniz dizinin adıyla değiştirin.

6. Kapsayıcının ve dizininizin oluşturulduğundan emin olmak için bir liste komutu çalıştırın.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * @No__t-0 yer tutucusunu blob hizmeti uç noktanızın adıyla değiştirin.

   * @No__t-0 yer tutucusunu, hesabınızın erişim anahtarıyla değiştirin.

   * @No__t-0 yer tutucusunu kapsayıcının adıyla değiştirin.

7. Hadoop, Data Box BLOB depolama alanından daha önce oluşturduğunuz kapsayıcıya verileri kopyalayın. Kopyaladığınız Dizin bulunamazsa, komut otomatik olarak oluşturur.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * @No__t-0 yer tutucusunu blob hizmeti uç noktanızın adıyla değiştirin.

    * @No__t-0 yer tutucusunu, hesabınızın erişim anahtarıyla değiştirin.

    * @No__t-0 yer tutucusunu kapsayıcının adıyla değiştirin.

    * @No__t-0 yer tutucusunu, dosya dışlamaları listenizi içeren dosyanın adıyla değiştirin.

    * @No__t-0 yer tutucusunu, kopyalamak istediğiniz verileri içeren dizinin adıyla değiştirin.

    * @No__t-0 yer tutucusunu, verilerinizi kopyalamak istediğiniz dizinin adıyla değiştirin.

    @No__t-0 seçeneği, `hadoop-azure*.jar` ve bağımlı `azure-storage*.jar` dosyalarını `distcp` ' e kullanılabilir hale getirmek için kullanılır. Bu, bazı kümeler için zaten oluşabilir.

    Aşağıdaki örnek, verileri kopyalamak için `distcp` komutunun nasıl kullanıldığını gösterir.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Kopyalama hızını artırmak için:

    * Mapto sayısını değiştirmeyi deneyin. (Yukarıdaki örnek `m` = 4 mapkas kullanır.)

    * Birden çok `distcp` ' i paralel olarak çalıştırmayı deneyin.

    * Büyük dosyaların küçük dosyaları daha iyi gerçekleştireceğini unutmayın.

## <a name="ship-the-data-box-to-microsoft"></a>Data Box Microsoft 'a gönderin

Data Box cihazını hazırlamak ve Microsoft 'a göndermek için aşağıdaki adımları izleyin.

1. İlk olarak, [Data Box veya Data Box Heavy göndermeye hazırlama](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Cihaz hazırlama işlemi tamamlandıktan sonra, ürün reçetesi dosyalarını indirin. Bu ürün reçetesini veya bildirim dosyalarını daha sonra Azure 'a yüklenen verileri doğrulamak için kullanacaksınız.

3. Cihazı kapatın ve kabloları kaldırın.

4. KESINTISIZ bir toplama zamanlayın.

    * Data Box cihazlar için bkz. [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)gönderme.

    * Data Box Heavy cihazlar için bkz. [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)gönderme.

5. Microsoft cihazınızı aldıktan sonra veri merkezi ağına bağlanır ve veriler, cihaz sırasını yerleştirdiğinizde belirttiğiniz depolama hesabına (hiyerarşik ad alanları devre dışı bırakılmış olarak) yüklenir. Tüm verilerinizin Azure 'a yüklendiğini, BOM dosyalarına karşı doğrulayın. Artık bu verileri bir Data Lake Storage 2. depolama hesabına taşıyabilirsiniz.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Verileri Azure Data Lake Storage 2. taşıyın

Azure depolama hesabınızda zaten verileriniz var. Artık verileri Azure Data Lake depolama hesabınıza kopyalayacak ve dosyalara ve dizinlere erişim izinleri uygulayacaksınız.

> [!NOTE]
> Veri depolduğunuz Azure Data Lake Storage 2. kullanıyorsanız bu adım gereklidir. Veri depeti için hiyerarşik ad alanı olmadan yalnızca bir BLOB depolama hesabı kullanıyorsanız, bu bölümü atlayabilirsiniz.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Azure Data Lake Storage Gen 2 hesabına veri kopyalama

Azure Data Factory kullanarak veya Azure tabanlı Hadoop kümenizi kullanarak verileri kopyalayabilirsiniz.

* Azure Data Factory kullanmak için bkz. [Azure Data Factory ADLS 2. verileri taşıma](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). **Azure Blob depolamayı** kaynak olarak belirttiğinizden emin olun.

* Azure tabanlı Hadoop kümenizi kullanmak için bu DistCp komutunu çalıştırın:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * @No__t-0 ve `<dest_account>` yer tutucuları kaynak ve hedef depolama hesaplarının adlarıyla değiştirin.

    * @No__t-0 ve `<dest_container>` yer tutucuları kaynak ve hedef kapsayıcıların adlarıyla değiştirin.

    * @No__t-0 ve `<dest_path>` yer tutucuları kaynak ve hedef dizin yollarıyla değiştirin.

    * @No__t-0 yer tutucusunu, verileri içeren depolama hesabının erişim anahtarıyla değiştirin.

    Bu komut, depolama hesabınızdan hem verileri hem de meta verileri Data Lake Storage 2. depolama hesabınıza kopyalar.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage 2. hesabınız için bir hizmet sorumlusu oluşturma

Hizmet sorumlusu oluşturmak için, bkz. [nasıl yapılır: portalı kullanarak kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Makalenin [role uygulamayı atama](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) bölümünde bulunan adımları gerçekleştirirken, **Depolama Blobu veri katılımcısı** rolünü hizmet sorumlusuna atadığınızdan emin olun.

* Makalenin [oturum açmak için değerleri Al](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) bölümünde bulunan adımları gerçekleştirirken, uygulama kimliğini ve istemci gizli değerlerini bir metin dosyasına kaydedin. Bu kadar yakında ihtiyacınız olacak.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Bir kopyalanan dosyalar listesini izinleriyle oluşturma

Şirket içi Hadoop kümesinden şu komutu çalıştırın:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Bu komut, bir kopyalanan dosya listesini izinleriyle birlikte oluşturur.

> [!NOTE]
> Bu komutun çalışması, bu komutun çalıştırılması uzun sürebilir.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Kimliklerin bir listesini oluşturun ve bunları Azure Active Directory (Ekle) kimliklere eşleyin

1. @No__t-0 betiğini indirin. Bu makalenin [yardımcı betiklerine bakın ve kenar düğümünüz tarafından çalıştırılacak şekilde ayarlama](#download-helper-scripts) bölümüne bakın.

2. Benzersiz kimliklerin bir listesini oluşturmak için bu komutu çalıştırın.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Bu betik, ADD-0 @no__t adlı bir dosya oluşturur ve bu kimlik tabanlı kimliklere eşlemeniz gereken kimlikleri içerir.

3. @No__t-0 dosyasını bir metin düzenleyicisinde açın.

4. Dosyada görüntülenen her bir JSON nesnesi için, uygun eşlenmiş kimlikle bir AAD Kullanıcı asıl adı (UPN) veya ObjectID (OID) `target` özniteliğini güncelleştirin. İşiniz bittiğinde dosyayı kaydedin. Sonraki adımda bu dosyaya ihtiyacınız olacaktır.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Kopyalanan dosyalara izinler uygulama ve kimlik eşlemelerini uygulama

Data Lake Storage 2. hesabına kopyaladığınız verilere izinler uygulamak için bu komutu çalıştırın:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* @No__t-0 yer tutucusunu, depolama hesabınızın adıyla değiştirin.

* @No__t-0 yer tutucusunu kapsayıcının adıyla değiştirin.

* @No__t-0 ve `<client-secret>` yer tutucuları, hizmet sorumlusunu oluştururken topladığınız uygulama KIMLIĞI ve istemci gizli anahtarı ile değiştirin.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Ek: verileri birden çok Data Box cihazda bölme

Verilerinizi Data Box bir cihaza taşımadan önce bazı yardımcı betikleri indirmeniz, verilerinizin bir Data Box cihazına sığacak şekilde düzenlendiğinden emin olmanız ve gereksiz dosyaları dışarıda bırakmanız gerekir.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Yardımcı betikleri indirin ve kenar düğümünüz tarafından çalıştırılacak şekilde ayarlayın

1. Şirket içi Hadoop kümenizin kenarından veya baş düğümünden şu komutu çalıştırın:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Bu komut, yardımcı betikleri içeren GitHub deposunu klonlar.

2. Yerel bilgisayarınızda [JQ](https://stedolan.github.io/jq/) paketinin yüklü olduğundan emin olun.

   ```bash
   
   sudo apt-get install jq
   ```

3. [İstekler](http://docs.python-requests.org/en/master/) Python paketini yükler.

   ```bash
   
   pip install requests
   ```

4. Gerekli betiklerdeki yürütme izinlerini ayarlayın.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Verilerinizin Data Box bir cihaza sığacak şekilde düzenlendiğinden emin olun

Verilerinizin boyutu tek bir Data Box cihazının boyutunu aşarsa, dosyaları birden çok Data Box cihazında depolayabilmeniz için gruplara ayırabilirsiniz.

Verileriniz bir tek Data Box cihazının boyutunu aşmazsa sonraki bölüme geçebilirsiniz.

1. Yükseltilmiş izinlerle, önceki bölümde yer alan kılavuzu izleyerek indirdiğiniz `generate-file-list` betiğini çalıştırın.

   Komut parametrelerinin açıklaması aşağıda verilmiştir:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Oluşturulan dosya listelerini, [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) işi tarafından erişilebilmeleri için, bu şekilde bir dosyaya kopyalayın.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Gereksiz dosyaları Dışla

DisCp işinden bazı dizinleri hariç bırakmanız gerekir. Örneğin, kümeyi çalıştıran durum bilgilerini içeren dizinleri hariç tutun.

DistCp işini başlatmayı planladığınız şirket içi Hadoop kümesinde dışlamak istediğiniz dizinlerin listesini belirten bir dosya oluşturun.

Örnek buradadır:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Sonraki adımlar

Data Lake Storage 2. HDInsight kümeleri ile nasıl çalıştığını öğrenin. Bkz. [Azure HDInsight kümeleri ile Azure Data Lake Storage 2. kullanma](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
