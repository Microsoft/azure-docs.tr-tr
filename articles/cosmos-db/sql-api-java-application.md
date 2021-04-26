---
title: 'Öğretici: Azure Cosmos DB ve SQL API kullanarak Java Web uygulaması oluşturma'
description: "Öğretici: Bu Java Web uygulaması öğreticisi, Azure Web siteleri 'nde barındırılan bir Java uygulamasında verileri depolamak ve erişmek için Azure Cosmos DB ve SQL API 'sini nasıl kullanacağınızı gösterir."
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 489f4aad78437edad2184e773d97d35af24f3249
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286003"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Öğretici: Azure Cosmos DB ve SQL API kullanarak Java Web uygulaması oluşturma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Bu Java web uygulaması öğreticisi, Azure App Service Web Apps’te barındırılan bir Java uygulamasında verileri depolamak ve bunlara erişmek için [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hizmetinin nasıl kullanılacağını size gösterir. Bu makalede aşağıdakileri öğreneceksiniz:

* Eclipse'te temel bir JavaServer Pages (JSP) uygulaması oluşturma.
* [Azure Cosmos DB Java SDK’sı](https://github.com/Azure/azure-documentdb-java) kullanılarak Azure Cosmos DB hizmetiyle çalışma.

Bu Java uygulaması öğreticisi görevleri oluşturmanızı, almanızı ve aşağıdaki görüntüde gösterilen şekilde tamamlandı olarak işaretlemenizi sağlayan bir web tabanlı görev yönetimi uygulamasını nasıl oluşturacağınızı gösterir. Yapılacaklar listesindeki görevlerin her biri, Azure Cosmos DB'de JSON belgeleri olarak depolanır.

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="Yapılacaklar Listesi Java uygulamam":::

> [!TIP]
> Bu uygulama geliştirme öğreticisi, Java kullanımına ilişkin deneyim sahibi olduğunuzu varsayar. Java veya [önkoşul araçlarında](#Prerequisites) yeniyseniz GitHub'dan [yapılacaklar](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) projesinin tamamını indirmenizi ve [bu makalenin sonundaki yönergeleri](#GetProject) kullanarak projeyi oluşturmanızı öneririz. Oluşturduktan sonra, proje bağlamında kodu daha iyi kavramak için makaleyi inceleyebilirsiniz.  

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>Bu Java web uygulaması öğreticisi için önkoşullar

Bu uygulama geliştirme öğreticisine başlamadan önce aşağıdakilere sahip olmanız gerekir:

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Geliştirme Seti (JDK) 7 +](/java/azure/jdk/).
* [Java EE Geliştiricileri için Eclipse IDE.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Java çalışma zamanı ortamı (ör. Tomcat veya Jetty) etkin bir Azure Web Sitesi.](../app-service/quickstart-java.md)

Bu araçları ilk kez yüklüyorsanız coreservlets.com, öğreticinin hızlı başlangıç bölümünde yükleme işlemini adım adım sağlar [: TomCat7 yükleme ve Çakışan Küreler Ile kullanma](https://www.youtube.com/watch?v=jOdCfW7-ybI&t=2s) .

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Azure Cosmos DB hesabı oluşturma

İlk olarak bir Azure Cosmos DB hesabı oluşturalım. Zaten bir hesabınız varsa veya bu öğretici için Azure Cosmos DB Öykünücüsü’nü kullanıyorsanız [2. Adım: Java JSP uygulaması oluşturma](#CreateJSP) adımına atlayabilirsiniz.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>Java JSP uygulaması oluşturma

JSP uygulaması oluşturmak için:

1. İlk olarak, bir Java projesi oluşturarak başlayacağız. Eclipse'i başlatın, ardından **Dosya**'ya tıklayın, **Yeni**'ye tıklayın ve ardından **Dinamik Web Projesi**'ne tıklayın. Kullanılabilir bir proje olarak listelenen **dinamik Web projesini** görmüyorsanız şunları yapın: **Dosya**, **Yeni**, **Proje**..., **Web**' i genişletin, **dinamik Web projesi**' ne tıklayın ve **İleri**' ye tıklayın.
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="JSP Java Uygulaması Geliştirme":::

1. **Proje adı** kutusuna bir proje adı girin ve **Hedef Çalışma Zamanı** açılan menüsünde isteğe bağlı olarak bir değer seçin (ör. Apache Tomcat v7.0) ve ardından **Son**'a tıklayın. Bir hedef çalışma zamanının seçilmesi, projenizi Eclipse aracılığıyla yerel olarak çalıştırmanızı sağlar.

1. Eclipse'te Proje Gezgini görünümünde projenizi genişletin. **WebContent**'e sağ tıklayın, **Yeni**'ye tıklayın ve ardından **JSP Dosyası**'na tıklayın.

1. **Yeni JSP Dosyası** iletişim kutusunda dosyaya **index.jsp** adını verin. Üst klasörü aşağıdaki resimde gösterildiği gibi **WebContent** olarak tutun ve ardından **İleri**'ye tıklayın.
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="Yeni bir JSP Dosyası Oluşturma - Java Web Uygulaması Öğreticisi":::

1. **Select JSP Template (JSP Şablon Seçme)** iletişim kutusunda bu öğreticinin amacı doğrultusunda **New JSP File (html) (Yeni JSP Dosyası (html))** seçeneğini belirleyin ve ardından **Finish (Son)** düğmesine tıklayın.

1. *index.jsp* dosyası tutulma 'de açıldığında, Merhaba Dünya görüntülenecek metni ekleyin **!** `<body>`Hello World! (Merhaba Dünya!) ifadesinin görüntülenmesi için metni ekleyin. Güncelleştirilmiş `<body>` içeriği aşağıdaki kod gibi görünmelidir:

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. *index.jsp* dosyasını kaydedin.

1. 2 adımda bir hedef çalışma zamanı ayarlarsanız **Proje**'ye ve ardından **Çalıştır**'a tıklayıp JSP uygulamanızı yerel olarak çalıştırabilirsiniz:

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Hello World - Java Uygulaması Öğreticisi":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>SQL Java SDK 'sını yükler

[Apache Maven](https://maven.apache.org/), SQL Java SDK'sını ve bağımlılıklarını çekmenin en kolay yolunu sağlar. Bunu yapmak için, aşağıdaki adımları kullanarak projenizi bir Maven projesine dönüştürmeniz gerekir:

1. Proje Gezgini'nde projenize sağ tıklayın, **Yapılandır**'a tıklayın, **Maven Projesine Dönüştür**'e tıklayın.

1. **Yeni POM oluştur** penceresinde varsayılanları kabul edin ve **Son**'a tıklayın.

1. **Proje Gezgini**'nde pom.xml dosyasını açın.

1. **Bağımlılıklar** sekmesindeki **Bağımlılıklar** bölmesinde **Ekle**'ye tıklayın.

1. **Bağımlılık Seç** penceresinde aşağıdakileri yapın:
   
   * **Grup Kimliği** kutusunda, girin `com.azure` .
   * **Yapıt kimliği** kutusuna, girin `azure-cosmos` .
   * **Sürüm** kutusuna yazın `4.11.0` .
  
   Ya da, Grup KIMLIĞI ve yapıt KIMLIĞI için bağımlılık XML 'sini doğrudan *pom.xml* dosyasına ekleyebilirsiniz:

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-cosmos</artifactId>
     <version>4.11.0</version>
   </dependency>
   ```

1. **Tamam** ' a tıklayın ve Maven SQL Java SDK 'sını yükler veya pom.xml dosyasını kaydeder.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>Java uygulamanızda Azure Cosmos DB hizmetini kullanma

Şimdi de modelleri, görünümleri ve denetleyicileri Web uygulamanıza ekleyelim.

### <a name="add-a-model"></a>Model ekleme

İlk olarak, yeni bir *TodoItem. Java* dosyası içinde bir model tanımlayalim. `TodoItem`Sınıfı, alıcı ve ayarlayıcı yöntemleriyle birlikte bir öğenin şemasını tanımlar:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>Veri erişim nesnesi (DAO) sınıfları ekleme

Azure Cosmos DB yapılacaklar öğelerini soyutlamak için bir veri erişim nesnesi (DAO) oluşturun. Yapılacaklar öğelerini bir koleksiyona kaydetmek için, istemcinin hangi veritabanı ve koleksiyona kalıcı hale getireceğini (kendine bağlantılar tarafından başvurulduğu üzere) bilmesi gerekir. Genel olarak, veritabanına ek gidiş gelişleri önlemek için veritabanı ve koleksiyonu mümkün olduğunda ön belleğe almak en iyisidir.

1. Azure Cosmos DB hizmetini çağırmak için yeni bir nesne örneği oluşturmalısınız `cosmosClient` . Genel olarak, `cosmosClient` sonraki her istek için yeni bir istemci oluşturmak yerine nesneyi yeniden kullanmak en iyisidir. İstemcisini sınıfı içinde tanımlayarak yeniden kullanabilirsiniz `cosmosClientFactory` . [1. adımda](#CreateDB)kaydettiğiniz konak ve MASTER_KEY değerlerini güncelleştirin. Ana bilgisayar değişkenini URI 'ınızla değiştirin ve MASTER_KEY BIRINCIL ANAHTARıNıZLA değiştirin. `CosmosClientFactory` *Cosmosclientfactory. Java* dosyası içinde sınıfını oluşturmak için aşağıdaki kodu kullanın:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/CosmosClientFactory.java":::

1. Yeni bir *TodoDao. Java* dosyası oluşturun ve `TodoDao` Todo öğelerini oluşturmak, güncelleştirmek, okumak ve silmek için sınıfı ekleyin:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDao.java":::

1. Yeni bir *Mockdao. Java* dosyası oluşturun ve sınıfı ekleyin `MockDao` , bu sınıf `TodoDao` öğeler üzerinde CRUD işlemleri gerçekleştirmek için sınıfını uygular:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/MockDao.java":::

1. Yeni bir *Docdbdao. Java* dosyası oluşturun ve sınıfı ekleyin `DocDbDao` . Bu sınıf, Todoıtems kapsayıcıya kalıcı hale getirmek için kodu tanımlar, varsa veritabanınızı ve koleksiyonunuzu alır veya yoksa yeni bir tane oluşturur. Bu örnek, TodoItem düz eski Java nesnelerini (POJOs) JSON belgelerine seri hale getirmek ve seri hale getirmek için [Gson](https://code.google.com/p/google-gson/) kullanır. Yapılacaklar öğelerini bir koleksiyona kaydetmek için, istemcinin hangi veritabanı ve koleksiyona kalıcı hale getireceğini (kendine bağlantılar tarafından başvurulduğu üzere) bilmesi gerekir. Bu sınıf, belgeleri kendi kendine bağlamak yerine başka bir özniteliğe (ör. "ID") almak için de yardımcı işlevi tanımlar. Bir TodoItem JSON belgesini KIMLIĞE göre almak ve sonra bir POJO 'ya seri hale getirmek için yardımcı yöntemi kullanabilirsiniz.

   Ayrıca, `cosmosClient` BIR SQL sorgusu kullanarak bir todoıtems koleksiyonu veya listesini almak için istemci nesnesini de kullanabilirsiniz. Son olarak, listenizden bir TodoItem silmek için Delete yöntemini tanımlarsınız. Aşağıdaki kod, sınıfının içeriğini gösterir `DocDbDao` :

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/DocDbDao.java":::

1. Sonra, yeni bir *TodoDaoFactory. Java* dosyası oluşturun ve `TodoDaoFactory` Yeni bir docdbdao nesnesi oluşturan sınıfı ekleyin:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>Denetleyici ekleme

Uygulamanıza *TodoItemController* denetleyicisi ekleyin. Bu projede oluşturucuyu, alıcıları, ayarlayıcıları ve bir derleyici oluşturmak için [Project Lombok](https://projectlombok.org/)'u kullanırsınız. Alternatif olarak, bu kodu el ile yazabilir veya IDE 'nin oluşturmasını sağlayabilirsiniz.:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>Servlet oluşturma

Daha sonra, HTTP isteklerini denetleyiciye yönlendirmeye yönelik bir servlet oluşturun. *Apıvlet. Java* dosyasını oluşturun ve bunun altında aşağıdaki kodu tanımlayın:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Java uygulamasının geri kalanını birlikte bağlayın

Eğlenceli bitleri tamamladığımıza göre, her şey hızlı bir kullanıcı arabirimi oluşturmak ve bunu DAO 'ıza bağlamak.

1. Kullanıcıya görüntülenecek bir Web Kullanıcı arabirimine ihtiyacınız vardır. Daha önce oluşturduğumuz *index.jsp* 'yi aşağıdaki kodla yeniden yazalım:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. Son olarak, Web Kullanıcı arabirimini bağlamak için biraz istemci tarafı JavaScript yazın ve servlet birlikte:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. Şimdi geriye yalnızca uygulamayı test etmek kaldı. Uygulamayı yerel olarak çalıştırın, ardından öğe adı ve kategoriyi doldurarak ve **Görev Ekle**'ye tıklayarak birkaç Yapılacaklar öğesi ekleyin. Öğe görüntülendikten sonra, onay kutusunu değiştirerek ve **görevleri güncelleştir**' i tıklatarak tamamlanıp tamamlanmayacağını güncelleştirebilirsiniz.

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Java uygulamanızı Azure Web siteleri 'ne dağıtma

Azure Web Siteleri Java uygulamalarını dağıtmayı, uygulamanızı bir WAR dosyası olarak dışarı aktarmak ve kaynak denetimi (ör. Git) veya FTP aracılığıyla karşıya yüklemek kadar basit hale getirir.

1. Uygulamanızı bir WAR dosyası olarak dışarı aktarmak için **Proje Gezgini**'nde projenize sağ tıklayın, **Dışarı Aktar**'a tıklayın ve ardından **WAR Dosyası**'na tıklayın.

1. **WAR Dışarı Aktar** penceresinde aşağıdakileri yapın:
   
   * Web projesi kutusuna Azure-Cosmos-Java-Sample yazın.
   * Hedef kutusunda WAR dosyasını kaydetmek için bir hedef seçin.
   * **Finish (Son)** düğmesine tıklayın.

1. Artık elinizde bir WAR dosyası olduğuna göre, bunu Azure Web Sitenizin **webapps** dizinine yüklemeniz yeterlidir. Dosyayı karşıya yükleme konusunda yönergeler için bkz. [Azure App Service Web Apps’e Java uygulaması ekleme](../app-service/quickstart-java.md). WAR dosyası webapps dizinine yüklendikten sonra, çalışma zamanı ortamı eklemiş olduğunu algılar ve otomatik olarak yükler.

1. Tamamlanmış ürününüzü görüntülemek için, `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-cosmos-java-sample/` adresine gidip görevlerinizi eklemeye başlayın!

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>Projeyi GitHub'dan alma

Bu öğreticideki tüm örnekler GitHub'daki [todo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) projesinde bulunur. todo projesini Eclipse'e aktarmak için [Önkoşullar](#Prerequisites) bölümünde listelenen yazılım ve kaynaklara sahip olduğunuzdan emin olun ve ardından aşağıdakileri yapın:

1. [Proje Lombok](https://projectlombok.org/)'u yükleyin. Lombok projede oluşturucular, alıcılar ve ayarlayıcılar oluşturmak için kullanılır. Lombok.jar dosyasını indirdikten sonra, yüklemek için buna çift tıklayın veya komut satırından yükleyin.

1. Eclipse açıksa kapatın ve Lombok'u yüklemek için yeniden başlatın.

1. Eclipse'te **Dosya** menüsünde **İçeri Aktar**'a tıklayın.

1. **İçeri Aktar** penceresinde **Git**'e tıklayın, **Git Projeleri**'ne tıklayın ve ardından **İleri**'ye tıklayın.

1. **Depo Kaynağı Seçin** ekranında **URI'yi kopyalama**'ya tıklayın.

1. **Kaynak Git Deposu** ekranında, **URI** kutusunda, https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app girin ve ardından **İleri**’ye tıklayın.

1. **Dal seçimi** ekranında, **ana** ' ın seçili olduğundan emin olun ve ardından **İleri**' ye tıklayın.

1. **Yerel Hedef** ekranında deponun kopyalanabileceği bir klasör seçmek için **Gözat**'a tıklayın ve ardından **İleri**'ye tıklayın.

1. **Projeleri içeri aktarmada kullanmak için sihirbaz seçin** ekranında **Var olan projeleri içeri aktar**'ın seçili olduğundan emin olun ve ardından **İleri**'ye tıklayın.

1. **Projeleri İçeri Aktar** ekranında **DocumentDB** projesinin seçimini kaldırın ve ardından **Son**'a tıklayın. DocumentDB projesi, daha sonra bağımlılık olarak ekleyeceğimiz Azure Cosmos DB Java SDK'sını içerir.

1. **Proje Gezgini**'nde Azure-Cosmos-Java-sample\src\com.Microsoft.Azure.Cosmos.Sample.dao\DocumentClientFactory.Java adresine gıdın ve konağı ve MASTER_KEY değerlerini Azure Cosmos DB HESABıNıZ için URI ve birincil anahtarla değiştirin ve dosyayı kaydedin. Daha fazla bilgi için bkz [. 1. adım. Azure Cosmos veritabanı hesabı oluşturun](#CreateDB).

1. **Proje Gezgini**'nde, **Azure-Cosmos-Java-Sample**' a sağ tıklayın, **Yapı yolu**' na tıklayın ve ardından **Yapı yolunu Yapılandır**' a tıklayın.

1. **Java Oluşturma Yolu** ekranında sağ bölmedeki **Kitaplıklar** sekmesini seçin ve ardından **Dış JAR'lar Ekle**'ye tıklayın. Lombok.jar dosyasının konumuna gidin, **Aç**'a tıklayın ve ardından **Tamam**'a tıklayın.

1. **Özellikler** penceresini tekrar açmak için 12. adımı kullanın ve ardından sol bölmedeki **Hedeflenen Çalışma Zamanları**'na tıklayın.

1. **Hedeflenen Çalışma Zamanları** ekranında **Yeni**'ye tıklayın, **Apache Tomcat v7.0**'ı seçin ve ardından **Tamam**'a tıklayın.

1. **Özellikler** penceresini tekrar açmak için 12. adımı kullanın ve ardından sol bölmedeki **Proje Modelleri**'ne tıklayın.

1. **Proje Modelleri** ekranında **Dinamik Web Modülü**'nü ve **Java**'yı seçin ve ardından **Tamam**'a tıklayın.

1. Ekranın en altındaki **Sunucular** sekmesinde **Localhost'ta Tomcat v7.0 Sunucusu**'na sağ tıklayın ve ardından **Ekle ve Kaldır**'a tıklayın.

1. **Ekle ve Kaldır** penceresinde **Azure-Cosmos-Java-Sample** ' ı **yapılandırılmış** kutusuna taşıyın ve ardından **son**' a tıklayın.

1. **Sunucular** sekmesinde **Localhost'ta Tomcat v7.0 Sunucusu**'na sağ tıklayın ve ardından **Yeniden Başlat**'a tıklayın.

1. Bir tarayıcıda, `http://localhost:8080/azure-cosmos-java-sample/` konumuna gidin ve görev listenizi eklemeye başlayın. Varsayılan bağlantı noktası değerlerinizi değiştirdiyseniz 8080'i seçtiğiniz değere değiştirmeyi unutmayın.

1. Projenizi bir Azure Web sitesine dağıtmak için bkz [. 6. adım. Uygulamanızı Azure Web siteleri 'ne dağıtın](#Deploy).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Cosmos DB node.js uygulama oluşturma](sql-api-nodejs-application.md)
