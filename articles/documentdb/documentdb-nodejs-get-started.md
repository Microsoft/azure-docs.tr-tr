---
title: "DocumentDB için NoSQL Node.js öğreticisi | Microsoft Belgeleri"
description: "DocumentDB Node.js SDK&quot;sını kullanarak NoSQL veritabanı ve konsol uygulaması oluşturan bir NoSQL Node.js öğreticisi. DocumentDB, JSON için bir NoSQL veritabanıdır."
keywords: "node.js öğreticisi, düğüm veritabanı"
services: documentdb
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 2b8ac838e9387b04467f03d0608da05b3edfdd26
ms.lasthandoff: 03/28/2017


---
# <a name="nosql-nodejs-tutorial-documentdb-nodejs-console-application"></a>NoSQL Node.js öğreticisi: DocumentDB Node.js konsol uygulaması
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [MongoDB için Node.js](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Azure DocumentDB Node.js SDK'sı için Node.js öğreticisine hoş geldiniz! Bu öğreticiyi uyguladıktan sonra, DocumentDB kaynaklarını oluşturan ve sorgulayan bir konsol uygulamasına sahip olacaksınız.

Şu konulara değineceğiz:

* DocumentDB hesabı oluşturma ve DocumentDB hesabına bağlanma
* Uygulamanızı kurma
* Düğüm veritabanı oluşturma
* Koleksiyon oluşturma
* JSON belgeleri oluşturma
* Koleksiyonu sorgulama
* Bir belgeyi değiştirme
* Bir belgeyi silme
* Düğüm veritabanını silme

Zamanınız yok mu? Endişelenmeyin! Eksiksiz çözümü [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started)'da bulabilirsiniz. Hızlı yönergeler için bkz. [Eksiksiz çözüm edinme](#GetSolution).

Node.js öğreticisini tamamladıktan sonra, bize geri bildirim sağlamak için lütfen bu sayfanın üst ve alt kısmındaki oylama düğmelerini kullanın. Doğrudan sizinle iletişim kurmamızı isterseniz yorumlarınıza e-posta adresinizi ekleyin.

Şimdi başlayalım!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Node.js öğreticisi için önkoşullar
Lütfen aşağıdakilere sahip olduğunuzdan emin olun:

* Etkin bir Azure hesabı. Bir aboneliğiniz yoksa [Ücretsiz Azure Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
    * Alternatif olarak bu öğretici için [Azure DocumentDB Öykünücüsü](documentdb-nosql-local-emulator.md)’nü kullanabilirsiniz.
* [Node.js](https://nodejs.org/) v0.10.29 sürümü veya sonraki bir sürüm.

## <a name="step-1-create-a-documentdb-account"></a>1. Adım: DocumentDB hesabı oluşturma
Bir DocumentDB hesabı oluşturalım. Kullanmak istediğiniz bir hesap zaten varsa [Node.js uygulamanızı kurma](#SetupNode)'ya atlayabilirsiniz. DocumentDB Öykünücüsü’nü kullanıyorsanız öykünücünün kurulumunu gerçekleştirmek için lütfen [Azure DocumentDB Öykünücüsü](documentdb-nosql-local-emulator.md) konusundaki adımları izleyin ve [Node.js uygulamanızı kurma](#SetupNode) adımına atlayın.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupNode"></a>2. Adım: Node.js uygulamanızı kurma
1. Sık kullandığınız terminali açın.
2. Node.js uygulamanızı kaydetmek istediğiniz klasör veya dizini bulun.
3. Aşağıdaki komutlarla iki adet boş JavaScript dosyası oluşturun:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Npm aracılığıyla documentdb modülünü yükleyin. Aşağıdaki komutu kullanın:
   * ```npm install documentdb --save```

Harika! Kurulumu tamamladığınıza göre, biraz kod yazmaya başlayalım.

## <a id="Config"></a>3. Adım: Uygulamanızın yapılandırmalarını ayarlama
Sık kullandığınız metin düzenleyicisinde ```config.js``` öğesini açın.

Ardından, aşağıdaki kod parçacığını kopyalayıp yapıştırın ve DocumentDB uç noktası uri ve birincil anahtarınıza ```config.endpoint``` ve ```config.primaryKey``` özelliklerini ayarlayın. Bu yapılandırmaların her ikisini de [Azure Portalı](https://portal.azure.com)'nda bulabilirsiniz.

![Node.js öğreticisi - ETKİN hub'ı vurgulanmış, DocumentDB hesabı dikey penceresinde ANAHTARLAR düğmesi vurgulanmış ve Anahtarlar dikey penceresinde URI, BİRİNCİL ANAHTAR ve İKİNCİL ANAHTAR değerleri vurgulanmış bir DocumentDB hesabını gösteren Azure Portalı'nın ekran görüntüsü - Node veritabanı][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Aşağıdaki ```config.endpoint``` ve ```config.authKey``` özelliklerinizi ayarladığınız ```config``` nesnenize ```database id```, ```collection id``` ve ```JSON documents``` öğelerini kopyalayıp yapıştırın. Veritabanınızda depolamak istediğiniz veriler zaten varsa belge tanımları eklemek yerine DocumentDB'nin [Veri Geçiş Aracı](documentdb-import-data.md)'nı kullanabilirsiniz.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


Veritabanı, koleksiyon ve belge tanımları DocumentDB ```database id```, ```collection id``` ve belgelerin verileri görevini görür.

Son olarak, ```app.js``` dosyasının içinde başvurabilmek için ```config``` nesnenizi dışarı aktarın.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>4. Adım: DocumentDB hesabına bağlanma
Bir metin düzenleyicisinde boş ```app.js``` dosyanızı açın. ```documentdb``` modülünü ve yeni oluşturduğunuz ```config``` modülünü içeri aktarmak için aşağıdaki kodu kopyalayıp yapıştırın.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Yeni bir DocumentClient oluşturmak için, önceden kaydedilen ```config.endpoint``` ve ```config.primaryKey``` öğelerini kullanmak amacıyla kodu kopyalayıp yapıştırın.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Artık documentdb istemcisini başlatmaya yarayacak koda sahip olduğunuza göre, DocumentDB kaynaklarıyla çalışmaya bakalım.

## <a name="step-5-create-a-node-database"></a>5. Adım: Düğüm veritabanı oluşturma
Not Found, database url ve collection url için HTTP durumunu ayarlamak üzere aşağıdaki kodu kopyalayıp yapıştırın. Bu URL'ler, DocumentDB istemcisinin doğru veritabanı ve koleksiyonu bulma şeklidir.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

Bir [veritabanı](documentdb-resources.md#databases), **DocumentClient** sınıfının [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) işlevi kullanılarak oluşturulabilir. Veritabanı, koleksiyonlar genelinde bölümlenmiş belge depolama alanının mantıksal bir kapsayıcısıdır.

```config``` nesnesinde ```id``` belirtilmiş şekilde app.js dosyasında yeni veritabanınızı oluşturmak için **getDatabase** işlevini kopyalayın ve yapıştırın. İşlev, aynı ```FamilyRegistry``` kimliğine sahip bir veritabanının zaten var olup olmadığını denetler. Varsa yeni bir veritabanı oluşturmak yerine var olan veritabanını getireceğiz.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Aşağıdaki kodu kopyalayın ve çıkış iletisini ve **getDatabase** işlevine çağrıyı yazdıracak **exit** yardımcı işlevini eklemek için **getDatabase** işlevini ayarladığınız yere yapıştırın.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Terminalinizde ```app.js``` dosyanızı bulun ve şu komutu çalıştırın: ```node app.js```

Tebrikler! Başarılı bir şekilde bir DocumentDB veritabanı oluşturdunuz.

## <a id="CreateColl"></a>6. Adım: Koleksiyon oluşturma
> [!WARNING]
> **CreateDocumentCollectionAsync**, fiyatlandırmaya yönelik etkilere sahip yeni bir koleksiyon oluşturur. Daha ayrıntılı bilgi için lütfen [fiyatlandırma sayfamızı](https://azure.microsoft.com/pricing/details/documentdb/) ziyaret edin.
> 
> 

Bir [koleksiyon](documentdb-resources.md#collections), **DocumentClient** sınıfının [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) işlevi kullanılarak oluşturulabilir. Koleksiyon, JSON belgelerinin ve ilişkili JavaScript uygulama mantığının bir kapsayıcısıdır.

```config``` nesnesinde belirtilmiş ```id``` ile yeni koleksiyonunuzu oluşturmak üzere **getCollection** işlevini kopyalayıp app.js dosyasındaki **getDatabase** işlevinin altına yapıştırın. Yine aynı ```FamilyCollection``` kimliğine sahip bir koleksiyonun zaten var olup olmadığını denetleyeceğiz. Varsa yeni bir koleksiyon oluşturmak yerine var olan koleksiyonu getireceğiz.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

**getCollection** işlevini yürütmek için **getDatabase**'e yönelik çağrının altında bulunan kodu kopyalayın ve yapıştırın.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Terminalinizde ```app.js``` dosyanızı bulun ve şu komutu çalıştırın: ```node app.js```

Tebrikler! Başarılı bir şekilde bir DocumentDB koleksiyonu oluşturdunuz.

## <a id="CreateDoc"></a>7. Adım: Belge oluşturma
Bir [belge](documentdb-resources.md#documents), **DocumentClient** sınıfının [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) işlevi kullanılarak oluşturulabilir. Belgeler, kullanıcı tanımlı (rastgele) JSON içeriğidir. Artık DocumentDB'ye bir belge yerleştirebilirsiniz.

```config``` nesnesinde kaydedilen JSON verilerini içeren belgeleri oluşturmak için **getFamilyDocument** işlevini **getCollection** işlevinin altına kopyalayıp yapıştırın. Yine aynı kimliğe sahip bir belgenin zaten var olup olmadığını denetleyeceğiz.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

**getFamilyDocument** işlevini yürütmek için **getCollection** işlevine yönelik çağrının altına kodu kopyalayıp yapıştırın.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Terminalinizde ```app.js``` dosyanızı bulun ve şu komutu çalıştırın: ```node app.js```

Tebrikler! Başarılı bir şekilde bir DocumentDB belgesi oluşturdunuz.

![Node.js öğreticisi - Hesap, veritabanı, koleksiyon ve belgeler arasındaki hiyerarşik ilişkiyi gösteren diyagram - Node veritabanı](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

## <a id="Query"></a>8. Adım: DocumentDB kaynaklarını sorgulama
DocumentDB, her bir koleksiyonda depolanan JSON belgeleri için [zengin sorguların](documentdb-sql-query.md) gerçekleştirilmesini destekler. Aşağıdaki örnek kod, koleksiyonunuzdaki belgeler için çalıştırabileceğiniz bir sorguyu gösterir.

**queryCollection** işlevini kopyalayıp app.js dosyasındaki **getFamilyDocument** işlevinin altına yapıştırın. DocumentDB, aşağıda gösterildiği gibi SQL benzeri sorguları destekler. Karmaşık sorgular derleme hakkında daha fazla bilgi için [Query Playground](https://www.documentdb.com/sql/demo) sayfasını ve [sorgu belgelerini](documentdb-sql-query.md) inceleyin.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


Aşağıdaki diyagramda, DocumentDB SQL sorgusu söz diziminin oluşturduğunuz koleksiyonda nasıl çağrıldığı gösterilmektedir.

![Node.js öğreticisi - Sorgunun kapsamını ve sorgunun anlamını gösteren diyagram - Node veritabanı](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

DocumentDB sorguları zaten tek bir koleksiyon kapsamında olduğundan, sorgudaki [FROM](documentdb-sql-query.md#FromClause) anahtar sözcüğü isteğe bağlıdır. Bu nedenle, "FROM Families f", "FROM root r" veya seçtiğiniz herhangi bir başka değişken adıyla değiştirilebilir. DocumentDB; Families, root veya seçtiğiniz değişken adının varsayılan olarak geçerli koleksiyona başvurduğu sonucuna varır.

**queryCollection** işlevini yürütmek için kodu kopyalayıp **getFamilyDocument**'a yönelik çağrının altına yapıştırın.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Terminalinizde ```app.js``` dosyanızı bulun ve şu komutu çalıştırın: ```node app.js```

Tebrikler! DocumentDB belgelerini başarılı bir şekilde sorguladınız.

## <a id="ReplaceDocument"></a>9. Adım: Bir belgeyi değiştirme
DocumentDB, JSON belgelerini değiştirmeyi destekler.

**replaceFamilyDocument** işlevini kopyalayıp app.js dosyasındaki **queryCollection** işlevinin altına yapıştırın.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

**replaceDocument** işlevini yürütmek için **queryCollection**'a çağrının altına kodu kopyalayıp yapıştırın. Ayrıca, belgenin başarılı bir şekilde değiştiğini doğrulamak üzere **queryCollection**'ı tekrar çağırmak için kodu ekleyin.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Terminalinizde ```app.js``` dosyanızı bulun ve şu komutu çalıştırın: ```node app.js```

Tebrikler! Bir DocumentDB belgesini başarıyla değiştirdiniz.

## <a id="DeleteDocument"></a>10. Adım: Bir belgeyi silme
DocumentDB, JSON belgelerini silmeyi destekler.

**deleteFamilyDocument** işlevini **replaceFamilyDocument** işlevinin altına kopyalayıp yapıştırın.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

**deleteDocument** işlevini yürütmek için ikinci **queryCollection**'a çağrının altına kodu kopyalayın ve yapıştırın.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Terminalinizde ```app.js``` dosyanızı bulun ve şu komutu çalıştırın: ```node app.js```

Tebrikler! Bir DocumentDB belgesini başarıyla sildiniz.

## <a id="DeleteDatabase"></a>11. Adım: Node veritabanını silme
Oluşturulan veritabanı silindiğinde, veritabanı ve tüm alt kaynaklar (koleksiyonlar, belgeler vb.) kaldırılır.

Veritabanını ve tüm alt kaynaklarını kaldırmak için **cleanup** işlevini kopyalayıp **deleteFamilyDocument** işlevinin altına yapıştırın.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

**cleanup** işlevini yürütmek için **deleteFamilyDocument**'a çağrının altına kodu kopyalayıp yapıştırın.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>12. Adım: Node.js uygulamanızı hep birlikte çalıştırın!
İşlevlerinizi çağırma dizisinin bütünüyle şu şekilde görünmesi gerekir:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Terminalinizde ```app.js``` dosyanızı bulun ve şu komutu çalıştırın: ```node app.js```

Başlarken uygulamanızın çıktısını görmeniz gerekir. Çıktı aşağıdaki örnek metinle eşleşmelidir.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Tebrikler! Node.js öğreticisini tamamladınız ve ilk DocumentDB konsol uygulamanızı oluşturdunuz.

## <a id="GetSolution"></a>Eksiksiz Node.js öğreticisi çözümünü edinme
Bu öğreticideki adımları tamamlama fırsatınız olmadıysa veya yalnızca kodu indirmek isterseniz [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started)'dan ulaşabilirsiniz.

Bu makaledeki tüm örnekleri içeren GetStarted çözümünü çalıştırmak için aşağıdakilere ihtiyacınız vardır:

* [DocumentDB hesabı][documentdb-create-account].
* GitHub'da bulunan [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) çözümü.

Npm aracılığıyla **documentdb** modülünü yükleyin. Aşağıdaki komutu kullanın:

* ```npm install documentdb --save```

Ardından, ```config.js``` dosyasında config.endpoint ve config.authKey değerlerini [3. Adım: Uygulamanızın yapılandırmalarını ayarlama](#Config) bölümünde açıklandığı gibi güncelleştirin. 

Ardından terminalinizde ```app.js``` dosyanızı bulun ve şu komutu çalıştırın: ```node app.js```.

Hepsi bu kadar, derleyin ve devam edin! 

## <a name="next-steps"></a>Sonraki adımlar
* Daha karmaşık bir Node.js örneği ister misiniz? Bkz. [DocumentDB kullanarak bir Node.js web uygulaması derleme](documentdb-nodejs-application.md).
* [Bir DocumentDB hesabını izleme](documentdb-monitor-accounts.md) hakkında bilgi edinin.
* [Query Playground](https://www.documentdb.com/sql/demo)'daki örnek veri kümelerimizde sorgular çalıştırın.
* [DocumentDB belge sayfasının](https://azure.microsoft.com/documentation/services/documentdb/) Geliştirme bölümünde programlama modeli hakkında daha fazla bilgi edinin.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

