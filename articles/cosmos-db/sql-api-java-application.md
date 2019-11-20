---
title: 'Öğretici: Azure Cosmos DB kullanarak Java uygulama geliştirme öğreticisi'
description: "Öğretici: Bu Java Web uygulaması öğreticisi, Azure Web siteleri 'nde barındırılan bir Java uygulamasında verileri depolamak ve erişmek için Azure Cosmos DB ve SQL API 'sini nasıl kullanacağınızı gösterir."
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: ramkris
ms.openlocfilehash: 4a7c307e8a4d4088fe4d2f7800398fda4704219c
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720841"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Öğretici: Azure Cosmos DB ve SQL API kullanarak Java Web uygulaması oluşturma

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Bu Java web uygulaması öğreticisi, Azure App Service Web Apps’te barındırılan bir Java uygulamasında verileri depolamak ve bunlara erişmek için [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hizmetinin nasıl kullanılacağını size gösterir. Bu makalede aşağıdakileri öğreneceksiniz:

* Eclipse'te temel bir JavaServer Pages (JSP) uygulaması oluşturma.
* [Azure Cosmos DB Java SDK’sı](https://github.com/Azure/azure-documentdb-java) kullanılarak Azure Cosmos DB hizmetiyle çalışma.

Bu Java uygulaması öğreticisi görevleri oluşturmanızı, almanızı ve aşağıdaki görüntüde gösterilen şekilde tamamlandı olarak işaretlemenizi sağlayan bir web tabanlı görev yönetimi uygulamasını nasıl oluşturacağınızı gösterir. Yapılacaklar listesindeki görevlerin her biri, Azure Cosmos DB'de JSON belgeleri olarak depolanır.

![Yapılacaklar Listesi Java uygulamam](./media/sql-api-java-application/image1.png)

> [!TIP]
> Bu uygulama geliştirme öğreticisi, Java kullanımına ilişkin deneyim sahibi olduğunuzu varsayar. Java veya [önkoşul araçlarında](#Prerequisites) yeniyseniz GitHub'dan [yapılacaklar](https://github.com/Azure-Samples/documentdb-java-todo-app) projesinin tamamını indirmenizi ve [bu makalenin sonundaki yönergeleri](#GetProject) kullanarak projeyi oluşturmanızı öneririz. Oluşturduktan sonra, proje bağlamında kodu daha iyi kavramak için makaleyi inceleyebilirsiniz.  
> 
> 

## <a id="Prerequisites"></a>Bu Java web uygulaması öğreticisi için önkoşullar
Bu uygulama geliştirme öğreticisine başlamadan önce aşağıdakilere sahip olmanız gerekir:

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Geliştirme Seti (JDK) 7 +](https://aka.ms/azure-jdks).
* [Java EE Geliştiricileri için Eclipse IDE.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Java çalışma zamanı ortamı (ör. Tomcat veya Jetty) etkin bir Azure Web Sitesi.](../app-service/app-service-web-get-started-java.md)

Bu araçları ilk kez yüklüyorsanız coreservlets.com adresindeki [Öğretici: TomCat7'yi yükleme ve Eclipse ile kullanma](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) makalesinin Hızlı Başlangıç bölümünde yükleme işlem için bir adım adım kılavuz mevcuttur.

## <a id="CreateDB"></a>1. Adım: Azure Cosmos DB hesabı oluşturma
İlk olarak bir Azure Cosmos DB hesabı oluşturalım. Zaten bir hesabınız varsa veya bu öğretici için Azure Cosmos DB Öykünücüsü’nü kullanıyorsanız [2. Adım: Java JSP uygulaması oluşturma](#CreateJSP) adımına atlayabilirsiniz.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a id="CreateJSP"></a>2. Adım: Java JSP uygulaması oluşturma
JSP uygulaması oluşturmak için:

1. İlk olarak, bir Java projesi oluşturarak başlayacağız. Eclipse'i başlatın, ardından **Dosya**'ya tıklayın, **Yeni**'ye tıklayın ve ardından **Dinamik Web Projesi**'ne tıklayın. Kullanılabilir bir proje olarak **Dinamik Web Projesi**'ni listelenmiş şekilde görmüyorsanız şunu yapın: **Dosya**'ya tıklayın, **Yeni**'ye tıklayın, **Proje**… seçeneğine tıklayın, **Web**'i genişletin, **Dinamik Web Projesi**'ne tıklayın ve **İleri**'ye tıklayın.
   
    ![JSP Java Uygulaması Geliştirme](./media/sql-api-java-application/image10.png)
2. **Proje adı** kutusuna bir proje adı girin ve **Hedef Çalışma Zamanı** açılan menüsünde isteğe bağlı olarak bir değer seçin (ör. Apache Tomcat v7.0) ve ardından **Son**'a tıklayın. Bir hedef çalışma zamanının seçilmesi, projenizi Eclipse aracılığıyla yerel olarak çalıştırmanızı sağlar.
3. Eclipse'te Proje Gezgini görünümünde projenizi genişletin. **WebContent**'e sağ tıklayın, **Yeni**'ye tıklayın ve ardından **JSP Dosyası**'na tıklayın.
4. **Yeni JSP Dosyası** iletişim kutusunda dosyaya **index.jsp** adını verin. Üst klasörü aşağıdaki resimde gösterildiği gibi **WebContent** olarak tutun ve ardından **İleri**'ye tıklayın.
   
    ![Yeni bir JSP Dosyası Oluşturma - Java Web Uygulaması Öğreticisi](./media/sql-api-java-application/image11.png)
5. **Select JSP Template (JSP Şablon Seçme)** iletişim kutusunda bu öğreticinin amacı doğrultusunda **New JSP File (html) (Yeni JSP Dosyası (html))** seçeneğini belirleyin ve ardından **Finish (Son)** düğmesine tıklayın.
6. index.jsp dosyası Eclipse'te açıldığında, var olan **öğesinin içinde** `<body>`Hello World! (Merhaba Dünya!) ifadesinin görüntülenmesi için metni ekleyin. Güncelleştirilmiş `<body>` içeriği aşağıdaki kod gibi görünmelidir:
   
        <body>
            <% out.println("Hello World!"); %>
        </body>
7. index.jsp dosyasını kaydedin.
8. 2 adımda bir hedef çalışma zamanı ayarlarsanız **Proje**'ye ve ardından **Çalıştır**'a tıklayıp JSP uygulamanızı yerel olarak çalıştırabilirsiniz:
   
    ![Hello World - Java Uygulaması Öğreticisi](./media/sql-api-java-application/image12.png)

## <a id="InstallSDK"></a>3. Adım: SQL Java SDK’sını yükleme
[Apache Maven](https://maven.apache.org/), SQL Java SDK'sını ve bağımlılıklarını çekmenin en kolay yolunu sağlar.

Bunu yapmak için aşağıdaki adımları tamamlayarak projenizi bir Maven projesine dönüştürmeniz gerekir:

1. Proje Gezgini'nde projenize sağ tıklayın, **Yapılandır**'a tıklayın, **Maven Projesine Dönüştür**'e tıklayın.
2. **Yeni POM oluştur** penceresinde varsayılanları kabul edin ve **Son**'a tıklayın.
3. **Proje Gezgini**'nde pom.xml dosyasını açın.
4. **Bağımlılıklar** sekmesindeki **Bağımlılıklar** bölmesinde **Ekle**'ye tıklayın.
5. **Bağımlılık Seç** penceresinde aşağıdakileri yapın:
   
   * **Grup Kimliği** kutusuna com.microsoft.azure girin.
   * **Yapıt Kimliği** kutusuna azure-documentdb girin.
   * **Sürüm** kutusuna 1.5.1 girin.
     
   ![SQL Java Uygulaması SDK'sını yükleme](./media/sql-api-java-application/image13.png)
     
   * Alternatif olarak, Grup Kimliği ve Yapıt Kimliği için bağımlılık XML'sini bir metin düzenleyicisi aracılığıyla doğrudan pom.xml'ye ekleyin:
        ```xml
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-documentdb</artifactId>
            <version>1.9.1</version>
        </dependency>
        ```
6. **Tamam**'a tıkladığınızda Maven SQL Java SDK'sını yükler.
7. Pom.xml dosyasını kaydedin.

## <a id="UseService"></a>4. Adım: Azure Cosmos DB hizmetini bir Java uygulamasında kullanma
1. İlk olarak, TodoItem.java içinde TodoItem nesnesini tanımlayalım:
   
        @Data
        @Builder
        public class TodoItem {
            private String category;
            private boolean complete;
            private String id;
            private String name;
        }
   
    Bu projede oluşturucuyu, alıcıları, ayarlayıcıları ve bir derleyici oluşturmak için [Project Lombok](https://projectlombok.org/)'u kullanırsınız. Alternatif olarak, bu kodu el ile yazabilir veya IDE'nin oluşturmasını sağlayabilirsiniz.
2. Azure Cosmos DB hizmetini çağırmak için yeni bir **DocumentClient** örneği oluşturmanız gerekir. Genel olarak, sonraki her istek için yeni bir istemci oluşturmak yerine **DocumentClient**'ı tekrar kullanmak en iyisidir. İstemciyi bir **DocumentClientFactory**'ye sarmalayarak tekrar kullanabiliriz. DocumentClientFactory.java içinde, [1. adımda](#CreateDB) panonuza kopyaladığınız URI ve BİRİNCİL ANAHTAR değerini yapıştırmanız gerekir. [YOUR\_ENDPOINT\_HERE] yerine URI'nizi ve [YOUR\_KEY\_HERE] yerine BİRİNCİL ANAHTARINIZI girin.
   
        private static final String HOST = "[YOUR_ENDPOINT_HERE]";
        private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
   
        private static DocumentClient documentClient = new DocumentClient(HOST, MASTER_KEY,
                        ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
   
        public static DocumentClient getDocumentClient() {
            return documentClient;
        }
3. Şimdi Yapılacaklar öğelerimizi Azure Cosmos DB'de kalıcı hale getirmeyi özetlemek için bir Veri Erişim Nesnesi (DAO) oluşturalım.
   
    Yapılacaklar öğelerini bir koleksiyona kaydetmek için, istemcinin hangi veritabanı ve koleksiyona kalıcı hale getireceğini (kendine bağlantılar tarafından başvurulduğu üzere) bilmesi gerekir. Genel olarak, veritabanına ek gidiş gelişleri önlemek için veritabanı ve koleksiyonu mümkün olduğunda ön belleğe almak en iyisidir.
   
    Aşağıdaki kod, veritabanımızın ve koleksiyonumuzun varsa nasıl alınacağını veya yoksa yenisinin nasıl oluşturulacağını gösterir:
   
        public class DocDbDao implements TodoDao {
            // The name of our database.
            private static final String DATABASE_ID = "TodoDB";
   
            // The name of our collection.
            private static final String COLLECTION_ID = "TodoCollection";
   
            // The Azure Cosmos DB Client
            private static DocumentClient documentClient = DocumentClientFactory
                    .getDocumentClient();
   
            // Cache for the database object, so we don't have to query for it to
            // retrieve self links.
            private static Database databaseCache;
   
            // Cache for the collection object, so we don't have to query for it to
            // retrieve self links.
            private static DocumentCollection collectionCache;
   
            private Database getTodoDatabase() {
                if (databaseCache == null) {
                    // Get the database if it exists
                    List<Database> databaseList = documentClient
                            .queryDatabases(
                                    "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (databaseList.size() > 0) {
                        // Cache the database object so we won't have to query for it
                        // later to retrieve the selfLink.
                        databaseCache = databaseList.get(0);
                    } else {
                        // Create the database if it doesn't exist.
                        try {
                            Database databaseDefinition = new Database();
                            databaseDefinition.setId(DATABASE_ID);
   
                            databaseCache = documentClient.createDatabase(
                                    databaseDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return databaseCache;
            }
   
            private DocumentCollection getTodoCollection() {
                if (collectionCache == null) {
                    // Get the collection if it exists.
                    List<DocumentCollection> collectionList = documentClient
                            .queryCollections(
                                    getTodoDatabase().getSelfLink(),
                                    "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (collectionList.size() > 0) {
                        // Cache the collection object so we won't have to query for it
                        // later to retrieve the selfLink.
                        collectionCache = collectionList.get(0);
                    } else {
                        // Create the collection if it doesn't exist.
                        try {
                            DocumentCollection collectionDefinition = new DocumentCollection();
                            collectionDefinition.setId(COLLECTION_ID);
   
                            collectionCache = documentClient.createCollection(
                                    getTodoDatabase().getSelfLink(),
                                    collectionDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return collectionCache;
            }
        }
4. Sonraki adım, TodoItems öğelerini koleksiyonda kalıcı hale getirmek için biraz kod yazmaktır. Bu örnekte TodoItem Eski Basit Java Nesnelerini (POJO'lar) JSON belgelerine seri hale getirmek ve seri durumdan çıkarmak için [Gson](https://code.google.com/p/google-gson/)'u kullanacağız.
   
        // We'll use Gson for POJO <=> JSON serialization for this example.
        private static Gson gson = new Gson();
   
        @Override
        public TodoItem createTodoItem(TodoItem todoItem) {
            // Serialize the TodoItem as a JSON Document.
            Document todoItemDocument = new Document(gson.toJson(todoItem));
   
            // Annotate the document as a TodoItem for retrieval (so that we can
            // store multiple entity types in the collection).
            todoItemDocument.set("entityType", "todoItem");
   
            try {
                // Persist the document using the DocumentClient.
                todoItemDocument = documentClient.createDocument(
                        getTodoCollection().getSelfLink(), todoItemDocument, null,
                        false).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
5. Azure Cosmos veritabanlarına ve koleksiyonlarına benzer şekilde, belgelere de kendine bağlantılar tarafından başvurulur. Aşağıdaki yardımcı işlevi, belgeleri kendine bağlantı yerine başka bir öznitelik (ör. "id") aracılığıyla almamızı sağlar:
   
        private Document getDocumentById(String id) {
            // Retrieve the document using the DocumentClient.
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.id='" + id + "'", null)
                    .getQueryIterable().toList();
   
            if (documentList.size() > 0) {
                return documentList.get(0);
            } else {
                return null;
            }
        }
6. Id ile bir TodoItem JSON belgesini alıp ardından bunu bir POJO'ya seri durumdan çıkarmak için 5. adımdaki yardımcı yöntemi kullanabiliriz:
   
        @Override
        public TodoItem readTodoItem(String id) {
            // Retrieve the document by id using our helper method.
            Document todoItemDocument = getDocumentById(id);
   
            if (todoItemDocument != null) {
                // De-serialize the document in to a TodoItem.
                return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
            } else {
                return null;
            }
        }
7. SQL kullanarak bir koleksiyonu veya TodoItems listesini almak için DocumentClient'ı da kullanabiliriz:
   
        @Override
        public List<TodoItem> readTodoItems() {
            List<TodoItem> todoItems = new ArrayList<TodoItem>();
   
            // Retrieve the TodoItem documents
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
                            null).getQueryIterable().toList();
   
            // De-serialize the documents in to TodoItems.
            for (Document todoItemDocument : documentList) {
                todoItems.add(gson.fromJson(todoItemDocument.toString(),
                        TodoItem.class));
            }
   
            return todoItems;
        }
8. DocumentClient ile bir belgeyi güncelleştirmenin birçok yolu vardır. Yapılacaklar listesi uygulamamızda bir TodoItem'ın tamamlandı ve tamamlanmadı durumları arasında geçiş yapabilmek istiyoruz. Belgenin içinde "tamamlandı" özniteliğini güncelleştirerek bunu yapabiliriz:
   
        @Override
        public TodoItem updateTodoItem(String id, boolean isComplete) {
            // Retrieve the document from the database
            Document todoItemDocument = getDocumentById(id);
   
            // You can update the document as a JSON document directly.
            // For more complex operations - you could de-serialize the document in
            // to a POJO, update the POJO, and then re-serialize the POJO back in to
            // a document.
            todoItemDocument.set("complete", isComplete);
   
            try {
                // Persist/replace the updated document.
                todoItemDocument = documentClient.replaceDocument(todoItemDocument,
                        null).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
9. Son olarak, listemizden bir TodoItem'ı silebilmek istiyoruz. Bunu yapmak için daha önce yazmış olduğumuz yardımcı yöntemi kullanarak kendine bağlantıyı alıp ardından istemciye bunu silmesini söyleyebiliriz:
   
        @Override
        public boolean deleteTodoItem(String id) {
            // Azure Cosmos DB refers to documents by self link rather than id.
   
            // Query for the document to retrieve the self link.
            Document todoItemDocument = getDocumentById(id);
   
            try {
                // Delete the document by self link.
                documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return false;
            }
   
            return true;
        }

## <a id="Wire"></a>5. Adım: Java uygulaması geliştirme projesinin geriye kalan kısmını bağlama
Artık eğlenceli kısımları tamamladığımıza göre, geriye sadece hızlı bir kullanıcı arabirimi oluşturmak ve bunu DAO'muza bağlamak kaldı.

1. İlk olarak, DAO'muzu çağırmak için bir denetleyici oluşturmakla başlayalım:
   
        public class TodoItemController {
            public static TodoItemController getInstance() {
                if (todoItemController == null) {
                    todoItemController = new TodoItemController(TodoDaoFactory.getDao());
                }
                return todoItemController;
            }
   
            private static TodoItemController todoItemController;
   
            private final TodoDao todoDao;
   
            TodoItemController(TodoDao todoDao) {
                this.todoDao = todoDao;
            }
   
            public TodoItem createTodoItem(@NonNull String name,
                    @NonNull String category, boolean isComplete) {
                TodoItem todoItem = TodoItem.builder().name(name).category(category)
                        .complete(isComplete).build();
                return todoDao.createTodoItem(todoItem);
            }
   
            public boolean deleteTodoItem(@NonNull String id) {
                return todoDao.deleteTodoItem(id);
            }
   
            public TodoItem getTodoItemById(@NonNull String id) {
                return todoDao.readTodoItem(id);
            }
   
            public List<TodoItem> getTodoItems() {
                return todoDao.readTodoItems();
            }
   
            public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
                return todoDao.updateTodoItem(id, isComplete);
            }
        }
   
    Karmaşık bir uygulamada, denetleyici DAO'nun üstünde karmaşık bir iş mantığı barındırabilir.
2. Ardından, HTTP isteklerini denetleyiciye yönlendirmek için bir servlet oluşturacağız:
   
        public class TodoServlet extends HttpServlet {
            // API Keys
            public static final String API_METHOD = "method";
   
            // API Methods
            public static final String CREATE_TODO_ITEM = "createTodoItem";
            public static final String GET_TODO_ITEMS = "getTodoItems";
            public static final String UPDATE_TODO_ITEM = "updateTodoItem";
   
            // API Parameters
            public static final String TODO_ITEM_ID = "todoItemId";
            public static final String TODO_ITEM_NAME = "todoItemName";
            public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
            public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
   
            public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
   
            private static final long serialVersionUID = 1L;
            private static final Gson gson = new Gson();
   
            @Override
            protected void doGet(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
   
                String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
   
                TodoItemController todoItemController = TodoItemController
                        .getInstance();
   
                String id = request.getParameter(TODO_ITEM_ID);
                String name = request.getParameter(TODO_ITEM_NAME);
                String category = request.getParameter(TODO_ITEM_CATEGORY);
                boolean isComplete = StringUtils.equalsIgnoreCase("true",
                        request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
   
                switch (request.getParameter(API_METHOD)) {
                case CREATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                            category, isComplete));
                    break;
                case GET_TODO_ITEMS:
                    apiResponse = gson.toJson(todoItemController.getTodoItems());
                    break;
                case UPDATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                            isComplete));
                    break;
                default:
                    break;
                }
   
                response.getWriter().println(apiResponse);
            }
   
            @Override
            protected void doPost(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
                doGet(request, response);
            }
        }
3. Kullanıcıya gösterilecek bir web kullanıcı arabirimine ihtiyacımız olacak. Daha önce oluşturduğumuz index.jsp'yi yeniden yazalım:
    ```html
        <html>
        <head>
          <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
          <title>Azure Cosmos DB Java Sample</title>
   
          <!-- Bootstrap -->
          <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
   
          <style>
            /* Add padding to body for fixed nav bar */
            body {
              padding-top: 50px;
            }
          </style>
        </head>
        <body>
          <!-- Nav Bar -->
          <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
            <div class="container">
              <div class="navbar-header">
                <a class="navbar-brand" href="#">My Tasks</a>
              </div>
            </div>
          </div>
   
          <!-- Body -->
          <div class="container">
            <h1>My ToDo List</h1>
   
            <hr/>
   
            <!-- The ToDo List -->
            <div class = "todoList">
              <table class="table table-bordered table-striped" id="todoItems">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>Complete</th>
                  </tr>
                </thead>
                <tbody>
                </tbody>
              </table>
   
              <!-- Update Button -->
              <div class="todoUpdatePanel">
                <form class="form-horizontal" role="form">
                  <button type="button" class="btn btn-primary">Update Tasks</button>
                </form>
              </div>
   
            </div>
   
            <hr/>
   
            <!-- Item Input Form -->
            <div class="todoForm">
              <form class="form-horizontal" role="form">
                <div class="form-group">
                  <label for="inputItemName" class="col-sm-2">Task Name</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
                  </div>
                </div>
   
                <div class="form-group">
                  <label for="inputItemCategory" class="col-sm-2">Task Category</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
                  </div>
                </div>
   
                <button type="button" class="btn btn-primary">Add Task</button>
              </form>
            </div>
   
          </div>
   
          <!-- Placed at the end of the document so the pages load faster -->
          <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
          <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
          <script src="assets/todo.js"></script>
        </body>
        </html>
    ```
4. Son olarak, web kullanıcı arabirimi ile servlet'i birbirine bağlamak için biraz istemci tarafı JavaScript'i yazın:
   
        var todoApp = {
          /*
           * API methods to call Java backend.
           */
          apiEndpoint: "api",
   
          createTodoItem: function(name, category, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "createTodoItem",
                "todoItemName": name,
                "todoItemCategory": category,
                "todoItemComplete": isComplete
              },
              function(data) {
                var todoItem = data;
                todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
              },
              "json");
          },
   
          getTodoItems: function() {
            $.post(todoApp.apiEndpoint, {
                "method": "getTodoItems"
              },
              function(data) {
                var todoItemArr = data;
                $.each(todoItemArr, function(index, value) {
                  todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
                });
              },
              "json");
          },
   
          updateTodoItem: function(id, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "updateTodoItem",
                "todoItemId": id,
                "todoItemComplete": isComplete
              },
              function(data) {},
              "json");
          },
   
          /*
           * UI Methods
           */
          addTodoItemToTable: function(id, name, category, isComplete) {
            var rowColor = isComplete ? "active" : "warning";
   
            todoApp.ui_table().append($("<tr>")
              .append($("<td>").text(name))
              .append($("<td>").text(category))
              .append($("<td>")
                .append($("<input>")
                  .attr("type", "checkbox")
                  .attr("id", id)
                  .attr("checked", isComplete)
                  .attr("class", "isComplete")
                ))
              .addClass(rowColor)
            );
          },
   
          /*
           * UI Bindings
           */
          bindCreateButton: function() {
            todoApp.ui_createButton().click(function() {
              todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
              todoApp.ui_createNameInput().val("");
              todoApp.ui_createCategoryInput().val("");
            });
          },
   
          bindUpdateButton: function() {
            todoApp.ui_updateButton().click(function() {
              // Disable button temporarily.
              var myButton = $(this);
              var originalText = myButton.text();
              $(this).text("Updating...");
              $(this).prop("disabled", true);
   
              // Call api to update todo items.
              $.each(todoApp.ui_updateId(), function(index, value) {
                todoApp.updateTodoItem(value.name, value.value);
                $(value).remove();
              });
   
              // Re-enable button.
              setTimeout(function() {
                myButton.prop("disabled", false);
                myButton.text(originalText);
              }, 500);
            });
          },
   
          bindUpdateCheckboxes: function() {
            todoApp.ui_table().on("click", ".isComplete", function(event) {
              var checkboxElement = $(event.currentTarget);
              var rowElement = $(event.currentTarget).parents('tr');
              var id = checkboxElement.attr('id');
              var isComplete = checkboxElement.is(':checked');
   
              // Toggle table row color
              if (isComplete) {
                rowElement.addClass("active");
                rowElement.removeClass("warning");
              } else {
                rowElement.removeClass("active");
                rowElement.addClass("warning");
              }
   
              // Update hidden inputs for update panel.
              todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
   
              todoApp.ui_updateForm().append($("<input>")
                .attr("type", "hidden")
                .attr("class", "updateComplete")
                .attr("name", id)
                .attr("value", isComplete));
   
            });
          },
   
          /*
           * UI Elements
           */
          ui_createNameInput: function() {
            return $(".todoForm #inputItemName");
          },
   
          ui_createCategoryInput: function() {
            return $(".todoForm #inputItemCategory");
          },
   
          ui_createButton: function() {
            return $(".todoForm button");
          },
   
          ui_table: function() {
            return $(".todoList table tbody");
          },
   
          ui_updateButton: function() {
            return $(".todoUpdatePanel button");
          },
   
          ui_updateForm: function() {
            return $(".todoUpdatePanel form");
          },
   
          ui_updateId: function() {
            return $(".todoUpdatePanel .updateComplete");
          },
   
          /*
           * Install the TodoApp
           */
          install: function() {
            todoApp.bindCreateButton();
            todoApp.bindUpdateButton();
            todoApp.bindUpdateCheckboxes();
   
            todoApp.getTodoItems();
          }
        };
   
        $(document).ready(function() {
          todoApp.install();
        });
5. Harika! Şimdi geriye yalnızca uygulamayı test etmek kaldı. Uygulamayı yerel olarak çalıştırın, ardından öğe adı ve kategoriyi doldurarak ve **Görev Ekle**'ye tıklayarak birkaç Yapılacaklar öğesi ekleyin.
6. Öğe göründükten sonra, onay kutusundaki işareti değiştirip **Görevleri Güncelleştir**'e tıklayarak öğeyi tamamlandı veya tamamlanmadı olarak güncelleştirebilirsiniz.

## <a id="Deploy"></a>6. Adım: Java uygulamanızı Azure Web Siteleri'ne dağıtma
Azure Web Siteleri Java uygulamalarını dağıtmayı, uygulamanızı bir WAR dosyası olarak dışarı aktarmak ve kaynak denetimi (ör. Git) veya FTP aracılığıyla karşıya yüklemek kadar basit hale getirir.

1. Uygulamanızı bir WAR dosyası olarak dışarı aktarmak için **Proje Gezgini**'nde projenize sağ tıklayın, **Dışarı Aktar**'a tıklayın ve ardından **WAR Dosyası**'na tıklayın.
2. **WAR Dışarı Aktar** penceresinde aşağıdakileri yapın:
   
   * Web projesi kutusuna azure-documentdb-java-sample metnini girin.
   * Hedef kutusunda WAR dosyasını kaydetmek için bir hedef seçin.
   * **Finish (Son)** düğmesine tıklayın.
3. Artık elinizde bir WAR dosyası olduğuna göre, bunu Azure Web Sitenizin **webapps** dizinine yüklemeniz yeterlidir. Dosyayı karşıya yükleme konusunda yönergeler için bkz. [Azure App Service Web Apps’e Java uygulaması ekleme](../app-service/web-sites-java-add-app.md).
   
    WAR dosyası webapps dizinine yüklendikten sonra, çalışma zamanı ortamı bunu eklemiş olduğunuzu algılar ve otomatik olarak yükler.
4. Tamamlanmış ürününüzü görüntülemek için, `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/` adresine gidip görevlerinizi eklemeye başlayın!

## <a id="GetProject"></a>Projeyi GitHub'dan alma
Bu öğreticideki tüm örnekler GitHub'daki [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) projesinde bulunur. todo projesini Eclipse'e aktarmak için [Önkoşullar](#Prerequisites) bölümünde listelenen yazılım ve kaynaklara sahip olduğunuzdan emin olun ve ardından aşağıdakileri yapın:

1. [Proje Lombok](https://projectlombok.org/)'u yükleyin. Lombok projede oluşturucular, alıcılar ve ayarlayıcılar oluşturmak için kullanılır. Lombok.jar dosyasını indirdikten sonra, yüklemek için buna çift tıklayın veya komut satırından yükleyin.
2. Eclipse açıksa kapatın ve Lombok'u yüklemek için yeniden başlatın.
3. Eclipse'te **Dosya** menüsünde **İçeri Aktar**'a tıklayın.
4. **İçeri Aktar** penceresinde **Git**'e tıklayın, **Git Projeleri**'ne tıklayın ve ardından **İleri**'ye tıklayın.
5. **Depo Kaynağı Seçin** ekranında **URI'yi kopyalama**'ya tıklayın.
6. **Kaynak Git Deposu** ekranında, **URI** kutusunda, https://github.com/Azure-Samples/documentdb-java-todo-app.git girin ve ardından **İleri**’ye tıklayın.
7. **Dal Seçimi** ekranında **master**'ın seçili olduğundan emin olun ve ardından **İleri**'ye tıklayın.
8. **Yerel Hedef** ekranında deponun kopyalanabileceği bir klasör seçmek için **Gözat**'a tıklayın ve ardından **İleri**'ye tıklayın.
9. **Projeleri içeri aktarmada kullanmak için sihirbaz seçin** ekranında **Var olan projeleri içeri aktar**'ın seçili olduğundan emin olun ve ardından **İleri**'ye tıklayın.
10. **Projeleri İçeri Aktar** ekranında **DocumentDB** projesinin seçimini kaldırın ve ardından **Son**'a tıklayın. DocumentDB projesi, daha sonra bağımlılık olarak ekleyeceğimiz Azure Cosmos DB Java SDK'sını içerir.
11. **Proje Gezgini**’nde azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java konumuna gidin ve HOST ve MASTER_KEY değerlerini Azure Cosmos DB hesabınızın URI ve PRIMARY KEY değerleriyle değiştirin ve ardından dosyayı kaydedin. Daha fazla bilgi için bkz [. 1. adım. Azure Cosmos veritabanı hesabı oluşturun](#CreateDB).
12. **Proje Gezgini**'nde **azure-documentdb-java-sample**'a sağ tıklayın, **Yapı Yolu**'na tıklayın ve ardından **Oluşturma Yolunu Yapılandır**'a tıklayın.
13. **Java Oluşturma Yolu** ekranında sağ bölmedeki **Kitaplıklar** sekmesini seçin ve ardından **Dış JAR'lar Ekle**'ye tıklayın. Lombok.jar dosyasının konumuna gidin, **Aç**'a tıklayın ve ardından **Tamam**'a tıklayın.
14. **Özellikler** penceresini tekrar açmak için 12. adımı kullanın ve ardından sol bölmedeki **Hedeflenen Çalışma Zamanları**'na tıklayın.
15. **Hedeflenen Çalışma Zamanları** ekranında **Yeni**'ye tıklayın, **Apache Tomcat v7.0**'ı seçin ve ardından **Tamam**'a tıklayın.
16. **Özellikler** penceresini tekrar açmak için 12. adımı kullanın ve ardından sol bölmedeki **Proje Modelleri**'ne tıklayın.
17. **Proje Modelleri** ekranında **Dinamik Web Modülü**'nü ve **Java**'yı seçin ve ardından **Tamam**'a tıklayın.
18. Ekranın en altındaki **Sunucular** sekmesinde **Localhost'ta Tomcat v7.0 Sunucusu**'na sağ tıklayın ve ardından **Ekle ve Kaldır**'a tıklayın.
19. **Ekle ve Kaldır** penceresinde **azure-documentdb-java-sample**'ı **Yapılandırılmış** kutusuna taşıyın ve ardından **Son**'a tıklayın.
20. **Sunucular** sekmesinde **Localhost'ta Tomcat v7.0 Sunucusu**'na sağ tıklayın ve ardından **Yeniden Başlat**'a tıklayın.
21. Bir tarayıcıda, `http://localhost:8080/azure-documentdb-java-sample/` konumuna gidin ve görev listenizi eklemeye başlayın. Varsayılan bağlantı noktası değerlerinizi değiştirdiyseniz 8080'i seçtiğiniz değere değiştirmeyi unutmayın.
22. Projenizi bir Azure Web sitesine dağıtmak için bkz [. 6. adım. Uygulamanızı Azure Web siteleri 'ne dağıtın](#Deploy).

