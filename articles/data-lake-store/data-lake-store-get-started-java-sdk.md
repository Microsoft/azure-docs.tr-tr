---
title: "Azure Data Lake Store’da uygulama geliştirmek için Java SDK&quot;sını kullanma | Microsoft Docs"
description: "Bir Data Lake Store hesabı oluşturmak ve Data Lake Store&quot;da temel işlemleri gerçekleştirmek için Azure Data Lake Store Java SDK’sını kullanma"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 3e4302e7c5b54a46994eccaa8cf9ecbb0a84446d
ms.lasthandoff: 03/03/2017


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>Java'yı kullanarak Azure Data Lake Store ile çalışmaya başlama
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Klasör oluşturma, veri dosyalarını karşıya yükleme ve indirme gibi temel işlemleri gerçekleştirmek için Azure Data Lake Store Java SDK’sını kullanma hakkında bilgi edinin. Data Lake hakkında daha fazla bilgi için bkz. [Azure Data Lake Store](data-lake-store-overview.md).

Azure Data Lake Store için Java SDK API belgelerine, [Azure Data Lake Store Java API belgelerinden](https://azure.github.io/azure-data-lake-store-java/javadoc/) erişebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
* Java Development Kit (Java sürüm 1.7 veya üzerini kullanan JDK 7 ya da üzeri)
* Azure Data Lake Store hesabı. [Azure Portal'ı kullanarak Azure Data Lake Store ile çalışmaya başlama](data-lake-store-get-started-portal.md) bölümündeki yönergeleri uygulayın.
* [Maven](https://maven.apache.org/install.html). Bu eğiticide, yapı ve proje bağımlılıkları için Maven kullanılır. Maven veya Gradle gibi bir yapı sistemi olmadan derleme yapmak mümkün olsa da bu sistemler bağımlılıkların yönetilmesini çok daha kolay hale getirir.
* (İsteğe bağlı) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) vb. bir IDE.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Azure Active Directory'yi kullanarak nasıl kimlik doğrulaması gerçekleştiririm?
Bu eğiticide, Azure Active Directory belirteci (hizmetten hizmete kimlik doğrulama) almak için Azure AD uygulamasının istemci gizli anahtarını kullanırız. Bu belirteci kullanarak işlem dosyasını ve dizin işlemlerini gerçekleştirmek için Data Lake Store istemci nesnesi oluştururuz. İstemci gizli anahtarı kullanarak Azure Data Lake Store’da kimlik doğrulamaya ilişkin yönergeler için aşağıdaki üst düzey adımları gerçekleştiririz:

1. Azure AD web uygulaması oluşturma
2. Azure AD web uygulaması için istemci kimliğini, istemci gizli anahtarını ve belirteç uç noktasını alın.
3. Oluşturduğunuz Java uygulamasından erişmek istediğiniz Data Lake Store dosyasında/klasöründe Azure AD web uygulaması için erişimi yapılandırın.

Bu adımların nasıl gerçekleştirileceğine ilişkin yönergeler için bkz. [Active Directory uygulaması oluşturma](data-lake-store-authenticate-using-active-directory.md).

Azure Active Directory, belirteç almak için başka seçenekler de sunar. Tarayıcınızda çalışan bir uygulama, masaüstü uygulaması olarak dağıtılan bir uygulama ya da şirket içinde veya Azure sanal makinesinde çalışan sunucu uygulaması gibi farklı kimlik doğrulama mekanizmaları arasından senaryonuza en uygun olanını seçebilirsiniz. Parolalar, sertifikalar, 2 öğeli kimlik doğrulaması vb. farklı kimlik bilgileri arasından da seçim yapabilirsiniz. Ayrıca Azure Active Directory, şirket içi Active Directory kullanıcılarınızı bulutla eşitlemenize olanak tanır. Ayrıntılar için bkz. [Azure Active Directory için Kimlik Doğrulama Senaryoları](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Java uygulaması oluşturma
[GitHub’da](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) bulunan kod örneği, depoda dosya oluşturma, dosyaları birleştirme, dosya indirme ve depodaki bazı dosyaları silme işlemlerinde size yol gösterir. Makalenin bu bölümü, kodun ana bölümlerinde sizi yönlendirir.

1. Komut satırından [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) veya bir IDE kullanarak Maven projesi oluşturun. IntelliJ kullanarak Java projesi oluşturma yönergeleri için [buraya](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html) bakın. Eclipse kullanarak proje oluşturma yönergeleri için [buraya](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm) bakın. 
2. Maven **pom.xml** dosyanıza aşağıdaki bağımlılıkları ekleyin. Metnin şu kod parçacığını **\</version>** etiketi ile **\</project>** etiketi arasına ekleyin:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.4</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    İlk bağımlılık, maven deposundan Data Lake Store SDK’sını (`azure-data-lake-store-sdk`) kullanmaktır. İkinci bağımlılık (`slf4j-nop`), bu uygulama için hangi günlük altyapısının kullanılacağını belirtmektir. Data Lake Store SDK’sı; log4j, Java günlük kaydı, logback gibi birçok popüler günlük altyapısından birini seçmenizi veya günlük kaydı seçmemenizi sağlayan [slf4j](http://www.slf4j.org/) günlük cephesini kullanır. Bu örnekte, günlük kaydını devre dışı bırakacak ve dolayısıyla **slf4j-nop** bağlamasını kullanacağız. Uygulamanızda diğer günlük seçeneklerini kullanmak için [buraya](http://www.slf4j.org/manual.html#projectDep) bakın.

### <a name="add-the-application-code"></a>Uygulama kodunu ekleme
Kodun üç ana bölümü vardır.

1. Azure Active Directory belirtecini edinme
2. Data Lake Store istemcisi oluşturmak için belirteci kullanın.
3. İşlemleri gerçekleştirmek için Data Lake Store istemcisini kullanın.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>1. Adım: Azure Active Directory belirteci edinin.
Data Lake Store SDK’sı, Data Lake Store hesabıyla iletişim kurmak için gereken güvenlik belirteçlerini yönetmenizi sağlayacak kullanışlı yöntemler sunar. Bununla birlikte, SDK yalnızca bu yöntemlerin kullanılmasını zorunlu kılmaz. [Azure Active Directory SDK’sını](https://github.com/AzureAD/azure-activedirectory-library-for-java) veya kendi özel kodunuzu kullanma gibi diğer belirteç edinme yöntemlerinden yararlanabilirsiniz.

Daha önce oluşturduğunuz Active Directory Web uygulaması için belirteci edinmek üzere Data Lake Store SDK’sını kullanmak için `AccessTokenProvider` alt sınıflarından birini kullanın (aşağıdaki örnekte `ClientCredsTokenProvider` kullanılmaktadır). Belirteç sağlayıcısı, bellekteki belirteci almak için kullanılan kimlik bilgilerini önbelleğe alır ve süre sonu yaklaştığında belirteci otomatik olarak yeniler. Kendi `AccessTokenProvider` alt sınıflarınızı oluşturabilir, böylece belirteçlerin müşteri kodunuza göre alınmasını sağlayabilirsiniz; ancak şimdilik yalnızca SDK’da verileni kullanalım.

**BURAYI DOLDURUN** alanını, Azure Active Directory Web uygulaması için gerçek değerlerle değiştirin.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>2. Adım: Azure Data Lake Store istemci (ADLStoreClient) nesnesi oluşturma
[ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) nesnesi oluşturmak için Data Lake Store hesap adını ve son adımda oluşturduğunuz belirteç sağlayıcısını belirtmeniz gerekir. Data Lake Store hesap adının tam etki alanı adı olması gerektiğini unutmayın. Örneğin, **BURAYI DOLDURUN** alanını **mydatalakestore.azuredatalakestore.net** gibi bir etki alanı adı değiştirin.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>3. Adım: Dosya ve dizin işlemlerini gerçekleştirmek için ADLStoreClient’ı kullanın
Aşağıdaki kod, bazı yaygın işlemlerin kod parçacıklarını içerir. Diğer işlemleri görmek için **ADLStoreClient** nesnesinin tüm [Data Lake Store Java SDK API belgelerine](https://azure.github.io/azure-data-lake-store-java/javadoc/) bakabilirsiniz.

Dosyalar, standart Java akışları kullanılarak okunur ve yazılır. Bu, standart Java işlevlerinden (ör. biçimlendirilmiş çıkış için Yazdırma akışları ya da en üstteki ek işlevler için herhangi bir sıkıştırma veya şifreleme akışı vb.) yararlanmak için Java akışlarından herhangi birini Data Lake Store akışlarının üzerine katmanlayabilirsiniz.

     // create file and write some content
     String filename = "/a/b/c.txt";
     OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
     PrintStream out = new PrintStream(stream);
     for (int i = 1; i <= 10; i++) {
         out.println("This is line #" + i);
         out.format("This is the same line (%d), but using formatted output. %n", i);
     }
     out.close();
    
    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>4. Adım: Uygulamayı derleme ve çalıştırma
1. Bir IDE içinden çalıştırmak için **Çalıştır** düğmesini bulup basın. Maven’den çalıştırmak [exec: exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html)’i kullanın.
2. Komut satırından çalıştırabileceğiniz tek başına bir jar oluşturmak için jar’ı [Maven derleme eklentisini](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html) kullanarak dahil edilen tüm bağımlılıklarla birlikte derleyin. [Github’daki örnek kaynak kodda](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) bulunan pom.xml, bunun nasıl yapılacağını gösteren bir örnek içerir.

## <a name="next-steps"></a>Sonraki adımlar
* [Java SDK için JavaDoc’u keşfedin](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Data Lake Store'da verilerin güvenliğini sağlama](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics'i Data Lake Store ile kullanma](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight'ı Data Lake Store ile kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)


