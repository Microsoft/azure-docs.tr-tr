---
title: MongoDB ve Python SDK için Azure Cosmos DB API 'sini kullanarak Flask Web uygulaması oluşturma
description: ", MongoDB için Azure Cosmos DB API 'sini kullanarak bağlanmak ve sorgulamak için kullanabileceğiniz bir Python Flask kodu örneği sunar."
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 2bd8fa81d0825e604c42c54c0f789b7939206804
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756932"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Hızlı başlangıç: Azure Cosmos DB MongoDB için API 'sini kullanarak bir Python uygulaması oluşturma

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Cosmos DB temel dağıtım ve yatay ölçek özelliğinden faydalanabilir ve her şey belge, anahtar/değer ve grafik veritabanlarını hızlıca oluşturup sorgulayabilirsiniz.

Bu hızlı başlangıç kılavuzu aşağıdaki [Flask örneğini](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) kullanır ve [Azure Cosmos DB öykünücüsü](local-emulator.md) ve mongodb için Azure Cosmos DB API 'Si ile basit bir yapılacak-do Flask uygulaması oluşturmayı gösterir.

## <a name="prerequisites"></a>Önkoşullar

- [Azure Cosmos DB Emulator](local-emulator.md)’ı indirin. Öykünücü şu anda yalnızca Windows’da desteklenmektedir. Örnek, herhangi bir platformda yapılabileceği gibi, Azure’dan bir üretim anahtarı ile örneği kullanma işlemini gösterir.

- Visual Studio Code henüz yüklü değilse, platformunuza yönelik [VS Code](https://code.visualstudio.com/Download)’u (Windows, Mac, Linux) hızlıca yükleyebilirsiniz.

- Popüler Python uzantılardan birini yükleyerek Python Dil Desteğini eklediğinizden emin olun.
  1. Bir uzantı seçin.
  2. `Ctrl+Shift+P` Komut Paletine `ext install` yazarak uzantıyı yükleyin.

     Bu belgedeki örnekler Don Jayamanne’ın popüler ve tam özellikli [Python Uzantısını](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) kullanır.

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi GitHub 'dan bir Flask-MongoDB uygulaması kopyalayalım, bağlantı dizesini ayarlayalım ve uygulamayı çalıştıralım. Verilerle programlı bir şekilde çalışmanın ne kadar kolay olduğunu görüyorsunuz.

1. Bir komut istemini açın, git-samples adlı yeni bir klasör oluşturun ve komut istemini kapatın.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere yeni bir klasör olarak değiştirmek için `cd` komutunu kullanın.

    ```bash
    cd "C:\git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Python modüllerini yüklemek için aşağıdaki komutu çalıştırın.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Visual Studio Code’da klasörü açın.

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Veritabanı kaynaklarının kodda nasıl oluşturulduğunu öğrenmekle ilgileniyorsanız, aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Aksi takdirde, [Web uygulamasını çalıştırma](#run-the-web-app) konusuna atlayabilirsiniz. 

Aşağıdaki kod parçacıklarının tümü app.py dosyasından alınmıştır ve yerel Azure Cosmos DB Öykünücüsü’nün bağlantı dizesini kullanır. Başka türlü ayrıştırılamayan ileri eğik çizgiler için aşağıda görüldüğü gibi parolanın bölünmesi gerekir.

* MongoDB istemcisini başlatın, veritabanını alın ve kimlik doğrulaması yapın.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Koleksiyonu alın veya henüz yoksa bir tane oluşturun.

    ```python
    todos = db.todo #Select the collection
    ```

* Uygulama oluşturma

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Web uygulamasını çalıştırma

1. Azure Cosmos DB Emulator’ın çalışır durumda olduğundan emin olun.

2. Bir terminal penceresi açın ve uygulamanın kayıtlı olduğu dizine `cd` uygulayın.

3. Ardından, `set FLASK_APP=app.py`, PowerShell düzenleyicileri için `$env:FLASK_APP = app.py` veya Mac kullanıyorsanız `export FLASK_APP=app.py` için bir Flask uygulaması için ortam değişkenini ayarlayın. 

4. Uygulamayı `flask run` ile çalıştırın ve [http://127.0.0.1:5000/](http://127.0.0.1:5000/) adresine göz atın.

5. Görevleri ekleyip kaldırın ve koleksiyona eklenip kaldırıldığına dikkat edin.

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Bağlantı dizenizi güncelleştirme

Kodu canlı Cosmos hesabına karşı test etmek istiyorsanız, hesap oluşturmak ve bağlantı dizesi bilgilerinizi almak için Azure portal gidin. Ardından uygulamaya kopyalayın.

1. [Azure Portal](https://portal.azure.com/), Cosmos hesabınızda, sol gezinti **bağlantı dizesi**' ne tıklayın ve ardından **okuma-yazma anahtarları**' na tıklayın. Sonraki adımda ekranın sağ tarafındaki kopyalama düğmelerini kullanarak Kullanıcı Adı, Parola ve Ana Bilgisayar değerlerini Dal.cs dosyasına kopyalayacaksınız.

2. Kök dizinde **app.py** dosyasını açın.

3. **username** değerini portaldan kopyalayın (kopyalama düğmesini kullanarak) ve **app.py** dosyasına **name** değeri olarak yapıştırın.

4. Daha sonra portaldan **connection string** değerini kopyalayın **app.py** dosyanızda MongoClient değeri olarak yapıştırın.

5. Son olarak, **password** değerini portaldan kopyalayın ve **app.py** dosyanıza **password** değeri olarak yapıştırın.

Artık uygulamanızı Cosmos DB ile iletişim kurması için gereken tüm bilgileri ile güncelleştirdiniz. Öncekiyle aynı şekilde çalıştırabilirsiniz.

## <a name="deploy-to-azure"></a>Azure'a Dağıt

Bu uygulamayı dağıtmak için Azure 'da yeni bir Web uygulaması oluşturabilir ve bu GitHub deposunun çatalından sürekli dağıtımı etkinleştirebilirsiniz. Azure 'da GitHub ile sürekli dağıtımı ayarlamak için bu [öğreticiyi](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) izleyin.

Azure'a dağıtırken uygulama anahtarlarınızı kaldırmanız ve aşağıdaki bölümün açıklama satırı yapılmadığından emin olmanız gerekir:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Daha sonra MONGOURL, MONGO_PASSWORD ve MONGO_USERNAME değerlerinizi uygulama ayarlarına eklemeniz gerekir. Azure Web Apps’te Uygulama Ayarları hakkında daha fazla bilgi almak için bu [öğreticiyi](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) izleyebilirsiniz.

Bu deponun bir çatalını oluşturmak istemiyorsanız, aşağıdaki Azure’a dağıt düğmesine de tıklayabilirsiniz. Daha sonra Azure’a gidip Cosmos DB hesabı bilgilerinizle uygulama ayarlarını yapmanız gerekir.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Kodunuzun GitHub veya diğer kaynak denetimi seçeneklerinde depolanmasını planlıyorsanız lütfen bağlantı dizelerinizi koddan kaldırmayı unutmayın. Bağlantı dizeleriniz, bunun yerine web uygulamasının uygulama ayarlarıyla ayarlanabilir.

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Cosmos hesabı oluşturmayı ve Flask uygulamasını çalıştırmayı öğrendiniz. Artık Cosmos veritabanınıza ek veri aktarabilirsiniz. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB’ye MongoDB verileri aktarma](mongodb-migrate.md)
