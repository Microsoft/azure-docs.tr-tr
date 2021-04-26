---
title: Azure HDInsight için bir Java HBase istemcisi oluşturmak için Apache Maven kullanma
description: Apache Maven kullanarak Java tabanlı bir Apache HBase uygulaması oluşturma ve ardından bunu Azure HDInsight 'ta HBase 'e dağıtma hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seodec18, devx-track-java
ms.date: 12/24/2019
ms.openlocfilehash: 13616cf99db39ec4dac1d13e3dcd2cefc5a44614
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942964"
---
# <a name="build-java-applications-for-apache-hbase"></a>Apache HBase için Java uygulamaları oluşturma

Java 'da bir [Apache HBase](https://hbase.apache.org/) uygulaması oluşturmayı öğrenin. Ardından Azure HDInsight 'ta HBase ile uygulamayı kullanın.

Bu belgedeki adımlarda, projeyi oluşturmak ve derlemek için [Apache Maven](https://maven.apache.org/) kullanılır. Maven, Java projelerine yönelik yazılım, belge ve raporlar oluşturmanıza olanak sağlayan bir yazılım proje yönetimi ve kavrama aracıdır.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight 'ta Apache HBase kümesi. Bkz. [Apache HBase ile çalışmaya başlama](./apache-hbase-tutorial-get-started-linux.md).

* [Java geliştirici seti (JDK) sürüm 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Apache [Maven](https://maven.apache.org/download.cgi) , Apache 'e göre düzgün şekilde [yüklendi](https://maven.apache.org/install.html) .  Maven, Java projeleri için bir proje derleme sistemidir.

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

* PowerShell kullanıyorsanız [az Module](/powershell/azure/)gerekecektir.

* Bir metin düzenleyici. Bu makalede Microsoft Notepad kullanılmaktadır.

## <a name="test-environment"></a>Test ortamı

Bu makale için kullanılan ortam, Windows 10 çalıştıran bir bilgisayardır.  Komutlar bir komut isteminde yürütülürler ve çeşitli dosyalar Notepad ile düzenlendi. Ortamınız için uygun şekilde değiştirin.

Bir komut isteminden, çalışan bir ortam oluşturmak için aşağıdaki komutları girin:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven projesi oluşturma

1. **Hbaseapp** adlı bir Maven projesi oluşturmak için aşağıdaki komutu girin:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Bu komut `hbaseapp` , temel bir Maven projesi içeren geçerli konumda adlı bir dizin oluşturur. İkinci komut, çalışma dizinini olarak değiştirir `hbaseapp` . Üçüncü komut daha sonra kullanılacak yeni bir dizin oluşturur `conf` . `hbaseapp`Dizin aşağıdaki öğeleri içerir:

    * `pom.xml`: Proje nesne modeli ([Pod](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)), projeyi oluşturmak için kullanılan bilgileri ve yapılandırma ayrıntılarını içerir.
    * `src\main\java\com\microsoft\examples`: Uygulama kodunuzu içerir.
    * `src\test\java\com\microsoft\examples`: Uygulamanız için testler içerir.

2. Oluşturulan örnek kodu kaldırın. Oluşturulan test ve uygulama dosyalarını silin `AppTest.java` ve `App.java` aşağıdaki komutları girerek:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Proje nesne modelini Güncelleştir

pom.xml dosyasının tam bir başvurusu için bkz https://maven.apache.org/pom.html ..  `pom.xml`Aşağıdaki komutu girerek açın:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Bağımlılık Ekle

İçinde `pom.xml` , bölümüne aşağıdaki metni ekleyin `<dependencies>` :

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Bu bölüm, projenin **HBase-Client** ve **Phoenix-Core** bileşenleri gerektiğini gösterir. Derleme zamanında, bu bağımlılıklar varsayılan Maven deposundan indirilir. Bu bağımlılık hakkında daha fazla bilgi edinmek için [Maven merkezi depo aramasını](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) kullanabilirsiniz.

> [!IMPORTANT]  
> HBase istemcisinin sürüm numarası, HDInsight kümeniz ile birlikte sunulan Apache HBase sürümüyle aynı olmalıdır. Doğru sürüm numarasını bulmak için aşağıdaki tabloyu kullanın.

| HDInsight küme sürümü | Kullanılacak Apache HBase sürümü |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

HDInsight sürümleri ve bileşenleri hakkında daha fazla bilgi için bkz. [HDInsight ile kullanılabilen farklı Apache Hadoop bileşenleri nelerdir](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Yapı yapılandırması

Maven eklentileri projenin derleme aşamalarını özelleştirmenizi sağlar. Bu bölüm eklenti, kaynak ve diğer derleme yapılandırma seçeneklerini eklemek için kullanılır.

Aşağıdaki kodu `pom.xml` dosyasına ekleyin ve dosyayı kaydedin ve kapatın. Bu metin dosyadaki etiketlerin içinde olmalıdır `<project>...</project>` , örneğin, `</dependencies>` ve `</project>` .

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
            <goals>
            <goal>shade</goal>
            </goals>
        </execution>
        </executions>
    </plugin>
    </plugins>
</build>
```

Bu bölüm `conf/hbase-site.xml` , HBase için yapılandırma bilgilerini içeren bir kaynağı () yapılandırır.

> [!NOTE]  
> Ayrıca, yapılandırma değerlerini kod aracılığıyla da ayarlayabilirsiniz. Örnekteki açıklamalara bakın `CreateTable` .

Bu bölüm ayrıca [Apache Maven derleyicisi eklentisini](https://maven.apache.org/plugins/maven-compiler-plugin/) ve [Apache Maven gölge eklentisini](https://maven.apache.org/plugins/maven-shade-plugin/)yapılandırır. Derleyici eklentisi, topolojiyi derlemek için kullanılır. Gölge eklentisi, Maven tarafından oluşturulan JAR paketindeki lisans çoğaltmasını engellemek için kullanılır. Bu eklenti, HDInsight kümesinde çalışma zamanında bir "yinelenen lisans dosyaları" hatası oluşmasını engellemek için kullanılır. Uygulama ile Maven-gölge-eklentisi kullanılması `ApacheLicenseResourceTransformer` hatayı önler.

Maven-gölge-eklentisi, uygulamanın gerektirdiği tüm bağımlılıkları içeren bir Uber jar de oluşturur.

### <a name="download-the-hbase-sitexml"></a>hbase-site.xml indirin

HBase kümesindeki HBase yapılandırmasını dizine kopyalamak için aşağıdaki komutu kullanın `conf` . `CLUSTERNAME`' Yi HDInsight kümenizin adıyla değiştirin ve ardından komutunu girin:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Uygulama oluşturma

### <a name="implement-a-createtable-class"></a>CreateTable sınıfı uygulama

Yeni bir dosya oluşturmak ve açmak için aşağıdaki komutu girin `CreateTable.java` . Yeni bir dosya oluşturmak için istemde **Evet** ' i seçin.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Ardından aşağıdaki Java kodunu kopyalayıp yeni dosyaya yapıştırın. Sonra dosyayı kapatın.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

Bu kod, `CreateTable` adlı bir tablo oluşturan `people` ve önceden tanımlanmış bazı kullanıcılarla doldurmış olan sınıftır.

### <a name="implement-a-searchbyemail-class"></a>SearchByEmail sınıfı uygulama

Yeni bir dosya oluşturmak ve açmak için aşağıdaki komutu girin `SearchByEmail.java` . Yeni bir dosya oluşturmak için istemde **Evet** ' i seçin.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Ardından aşağıdaki Java kodunu kopyalayıp yeni dosyaya yapıştırın. Sonra dosyayı kapatın.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

`SearchByEmail`Sınıfı, satırları e-posta adresine göre sorgulamak için kullanılabilir. Bir normal ifade filtresi kullandığından, sınıfı kullanırken bir dize ya da normal ifade sağlayabilirsiniz.

### <a name="implement-a-deletetable-class"></a>DeleteTable sınıfı uygulama

Yeni bir dosya oluşturmak ve açmak için aşağıdaki komutu girin `DeleteTable.java` . Yeni bir dosya oluşturmak için istemde **Evet** ' i seçin.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Ardından aşağıdaki Java kodunu kopyalayıp yeni dosyaya yapıştırın. Sonra dosyayı kapatın.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

`DeleteTable`Sınıfı, sınıfı tarafından oluşturulan tabloyu devre dışı bırakıp bırakarak bu örnekte oluşturulan HBase tablolarını temizler `CreateTable` .

## <a name="build-and-package-the-application"></a>Uygulamayı derleyin ve paketleyin

1. `hbaseapp`Dizininden, uygulamayı içeren BIR jar dosyası oluşturmak için aşağıdaki komutu kullanın:

    ```cmd
    mvn clean package
    ```

    Bu komut, uygulamayı oluşturur ve bir. jar dosyasında paketler.

2. Komut tamamlandığında, `hbaseapp/target` Dizin adlı bir dosya içerir `hbaseapp-1.0-SNAPSHOT.jar` .

   > [!NOTE]  
   > `hbaseapp-1.0-SNAPSHOT.jar`Dosya bir Uber jar. Uygulamayı çalıştırmak için gereken tüm bağımlılıkları içerir.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>JAR 'yi karşıya yükleme ve işleri çalıştırma (SSH)

Aşağıdaki adımlar, `scp` jar 'Yi HDInsight kümesindeki Apache HBase 'in birincil baş düğümüne kopyalamak için kullanılır. Bu `ssh` komut daha sonra kümeye bağlanmak ve örneği doğrudan baş düğümde çalıştırmak için kullanılır.

1. Jar 'yi kümeye yükleyin. `CLUSTERNAME`Öğesini HDInsight kümenizin adıyla değiştirin ve aşağıdaki komutu girin:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. HBase kümesine bağlanın. `CLUSTERNAME`Öğesini HDInsight kümenizin adıyla değiştirin ve aşağıdaki komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Java uygulamasını kullanarak bir HBase tablosu oluşturmak için, açık SSH bağlantınızda aşağıdaki komutu kullanın:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Bu komut **kişiler** adlı bir HBase tablosu oluşturur ve verileri veriyle doldurur.

4. Tabloda depolanan e-posta adreslerini aramak için aşağıdaki komutu kullanın:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Aşağıdaki sonuçları alırsınız:

    ```console
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

5. Tabloyu silmek için aşağıdaki komutu kullanın:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>JAR 'yi yükleme ve işleri çalıştırma (PowerShell)

Aşağıdaki adımlarda, Apache HBase kümeniz için JAR 'yi varsayılan depolamaya yüklemek için Azure PowerShell [az Module](/powershell/azure/new-azureps-module-az) kullanılır. HDInsight cmdlet 'leri, örnekleri uzaktan çalıştırmak için kullanılır.

1. AZ Module yükledikten ve yapılandırıldıktan sonra adlı bir dosya oluşturun `hbase-runner.psm1` . Bu dosyanın içeriği olarak aşağıdaki metni kullanın:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Bu dosya iki modül içerir:

   * **Add-HDInsightFile** -kümeye dosya yüklemek için kullanılır
   * **Start-HBaseExample** -daha önce oluşturulan sınıfları çalıştırmak için kullanılır

2. `hbase-runner.psm1`Dosyayı `hbaseapp` dizine kaydedin.

3. Modülleri Azure PowerShell kaydedin. Yeni bir Azure PowerShell penceresi açın ve aşağıdaki komutu, kümenizin adıyla değiştirerek aşağıda düzenleyin `CLUSTERNAME` . Ardından aşağıdaki komutları girin:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. ' İ kümenize yüklemek için aşağıdaki komutu kullanın `hbaseapp-1.0-SNAPSHOT.jar` .

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    İstendiğinde, küme oturum açma (yönetici) adını ve parolasını girin. Komutu, `hbaseapp-1.0-SNAPSHOT.jar` `example/jars` kümenizi kümenizin birincil depolamadaki konumuna yükler.

5. Kullanarak bir tablo oluşturmak için `hbaseapp` aşağıdaki komutu kullanın:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    İstendiğinde, küme oturum açma (yönetici) adını ve parolasını girin.

    Bu komut, HDInsight kümenizde HBase 'de **kişiler** adında bir tablo oluşturur. Bu komut konsol penceresinde herhangi bir çıktı göstermez.

6. Tablodaki girdileri aramak için aşağıdaki komutu kullanın:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    İstendiğinde, küme oturum açma (yönetici) adını ve parolasını girin.

    Bu komut, `SearchByEmail` `contactinformation` sütun ailesi ve `email` sütununun dize içerdiği herhangi bir satırı aramak için sınıfını kullanır `contoso.com` . Aşağıdaki sonuçları almalısınız:

    ```output
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

    Değer için **fabrikam.com** kullanılması, `-emailRegex` e-posta alanında **fabrikam.com** olan kullanıcıları döndürür. Normal ifadeleri arama terimi olarak da kullanabilirsiniz. Örneğin, **^ r** , ' r ' harfiyle başlayan e-posta adreslerini döndürür.

7. Tabloyu silmek için aşağıdaki komutu kullanın:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Start-HBaseExample kullanırken sonuç veya beklenmedik sonuç yok

`-showErr`İşi çalıştırırken üretilen standart hatayı (stderr) görüntülemek için parametresini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

[Apache HBase ile SQLLine kullanmayı öğrenin](apache-hbase-query-with-phoenix.md)