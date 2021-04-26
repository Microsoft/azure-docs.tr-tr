---
title: MongoDB yerel araçlarını kullanarak MongoDB 'yi MongoDB için Azure Cosmos DB API 'sine çevrimdışına geçirin
description: MongoDB yerel araçlarının, MongoDB örneklerinden Azure Cosmos DB ' a geçiş yapmak için nasıl kullanılabileceğini öğrenin
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 02/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 2e9f3c877a5c4650d3e31fa414cac76837f4c9e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101655760"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>Öğretici: MongoDB yerel araçlarını kullanarak MongoDB 'yi Azure Cosmos DB MongoDB 'ye geçirme

MongoDB yerel araçlarını kullanarak MongoDB 'nin şirket içi veya bulut örneğinden MongoDB 'nin API 'sine Azure Cosmos DB.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Kullanım örneği için uygun MongoDB yerel aracını seçin
> * Geçişi çalıştırma.
> * Geçişi izleme.
> * Geçişin başarılı olduğunu doğrulayın.

Bu öğreticide, MongoDB yerel araçlarını kullanarak MongoDB için bir Azure sanal makinesinde barındırılan MongoDB 'deki bir veri kümesini Azure Cosmos DB geçirebilirsiniz. MongoDB Native araçları, mevcut bir MongoDB örneğinde veri işlemeyi kolaylaştıran ikili bir kümesidir. Azure Cosmos DB Mongo API 'sini kullanıma sunduğundan, MongoDB Native araçları Azure Cosmos DB içine veri ekleyebilir. Bu belge, *mongoexport/mongoımport* veya *mongodump/mongorestore* kullanılarak MongoDB örneğinden verileri geçirmeye yönelik. Yerel araçlar, bağlantı dizelerini kullanarak MongoDB 'ye bağlandığından, araçları dilediğiniz yerde çalıştırabilirsiniz, ancak bu araçların güvenlik duvarı sorunlarından kaçınmak için MongoDB örneğiyle aynı ağ içinde çalıştırılmasını öneririz. 

MongoDB yerel araçları, verileri yalnızca konak donanımı izin verdiği kadar hızlı taşıyabilir; Yerel araçlar, toplam geçiş zamanının bir sorun olmadığı küçük veri kümeleri için en basit çözümdür. [MongoDB Spark Bağlayıcısı](https://docs.mongodb.com/spark-connector/current/), [Azure veri GEÇIŞ hizmeti (DMS)](../dms/tutorial-mongodb-cosmos-db.md)veya [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db-mongodb-api.md) , ölçeklenebilir bir geçiş işlem hattına ihtiyacınız varsa daha iyi alternatifler olabilir.

Zaten bir MongoDB kaynağınız yoksa, [Azure 'Da WINDOWS VM 'de MongoDB 'Yi kurma ve yapılandırma](/previous-versions/azure/virtual-machines/windows/install-mongodb)makalesine bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* Aktarım hızını tahmin etme, bölüm anahtarı seçme ve dizin oluşturma ilkesi gibi [geçiş öncesi adımları doldurun](../cosmos-db/mongodb-pre-migration.md) .
* [MongoDB hesabı için bir Azure Cosmos DB API 'Si oluşturun](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* MongoDB Örneğinizde oturum açın
    * [Bu bağlantıdan MongoDB yerel araçlarını indirip yükleyin](https://www.mongodb.com/try/download/database-tools).
        * **MongoDB yerel araçlar sürümünüzün mevcut MongoDB örneğiniz ile eşleştiğinden emin olun.**
        * MongoDB örneğinizin Azure Cosmos DB Mongo API 'den farklı bir sürümü varsa, **hem MongoDB yerel araç sürümlerini hem de MongoDB için uygun araç sürümünü ve Azure Cosmos DB Mongo API 'sini kullanın.**
    * `readWrite`Zaten bir tane yoksa, izinleri olan bir kullanıcı ekleyin. Bu öğreticide daha sonra *mongoexport* ve *mongodump* araçları için bu kullanıcı adını/parolayı belirtin.

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Sunucu tarafı yeniden denemelerini Azure Cosmos DB yapılandırma

MongoDB 'den geçiş yapan müşteriler kaynak idare özelliğinden Azure Cosmos DB avantajdan faydalanabilir. Azure Cosmos DB, bu istek sağlanan RU/s kapsayıcısını aşarsa geçiş sırasında verilen bir isteği kısıtlayabilir daha sonra bu isteğin yeniden denenilmesi gerekir. Geçiş Aracı ve Azure Cosmos DB arasındaki ağ atlamada yer alan gidiş dönüş süresi, bu isteğin genel yanıt süresini etkiler; Ayrıca MongoDB yerel araçlarının yeniden denemeleri işleyemeyebilir. *Sunucu tarafı yeniden deneme* özelliği Azure Cosmos DB, hizmetin kısıtlama hata kodlarını kesmesini ve daha sonra istek yanıt sürelerini önemli ölçüde iyileştirerek çok daha düşük bir gidiş dönüş süresi ile yeniden denemesini sağlar. MongoDB Native araçların perspektifinden, yeniden denemeleri işleme gereksinimi, geçiş sırasında deneyiminizi olumsuz etkileyecek şekilde en aza indirilir.

Sunucu tarafı yeniden deneme özelliğini Azure Cosmos DB portalının *Özellikler* dikey penceresinde bulabilirsiniz.

![MongoDB SSR özelliğinin ekran görüntüsü.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

*Devre dışıysa*, aşağıda gösterildiği gibi etkinleştirmenizi öneririz

![MongoDB SSR etkinleştirme ekran görüntüsü.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>Uygun MongoDB Native aracını seçin

![En iyi MongoDB yerel aracını seçme diyagramı.](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* *mongoexport/mongoımport* , MongoDB veritabanınızın bir alt kümesini geçirmek için en iyi geçiş araçları çiftidir.
    * *mongoexport* , mevcut verilerinizi insan tarafından OKUNABILEN bir JSON veya CSV dosyasına dışarı aktarır. *mongoexport* , dışarı aktarılacak mevcut verilerinizin alt kümesini belirten bir bağımsız değişken alır. 
    * *mongoımport* bir JSON veya CSV dosyası açar ve içeriği hedef veritabanı örneğine ekler (bu durumda Azure Cosmos DB.). 
    * JSON ve CSV 'nin bir Compact biçimleri olduğunu unutmayın; *mongoımport* , Azure Cosmos DB veri gönderdiğinden fazla ağ ücretlerine tabi olabilirsiniz.
* *mongodump/mongorestore* , tüm MongoDB veritabanınızı geçirmeye yönelik en iyi geçiş araçları çiftidir. Compact BSON biçimi, veriler Azure Cosmos DB eklendiği için ağ kaynaklarının daha verimli bir şekilde kullanılmasını sağlar.
    * *mongodump* , mevcut verilerinizi bir bSon dosyası olarak dışarı aktarır.
    * *mongorestore* bSon dosya dökümlerinizi Azure Cosmos DB içine aktarır.
* Bu şekilde, Azure Cosmos DB Mongo API 'sine aktarmak istediğiniz küçük bir JSON dosyanız varsa *mongoımport* Aracı, verileri almak için hızlı bir çözümdür.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>Azure Cosmos DB Mongo API kimlik bilgilerini toplayın

Azure Cosmos DB Mongo API 'SI, MongoDB yerel araçlarının yararlanmasına yönelik uyumlu erişim kimlik bilgileri sağlar. Azure Cosmos DB Mongo API 'sine veri geçirebilmek için bu erişim kimlik bilgilerine sahip olmanız gerekir. Bu kimlik bilgilerini bulmak için:

1. Azure portalını açın
1. Azure Cosmos DB Mongo API hesabınıza gidin
1. Sol gezinti çubuğunda *bağlantı dizesi* dikey penceresini seçin ve aşağıdakine benzer bir ekran görmeniz gerekir:

    ![Azure Cosmos DB kimlik bilgilerinin ekran görüntüsü.](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *Ana bilgisayar* -Azure Cosmos DB uç noktası, MongoDB ana bilgisayar adı olarak çalışır
    * *Bağlantı noktası* -MongoDB yerel araçları Azure Cosmos DB bağlandığında, bu bağlantı noktasını açık bir şekilde belirtmeniz gerekir
    * *Kullanıcı adı* -Azure Cosmos DB uç noktası etki alanı adının ön eki MongoDB Kullanıcı adı olarak çalışır
    * *Parola* -Azure Cosmos DB ana anahtar, MongoDB parolası olarak çalışır
    * Ayrıca,  `true` The The The The MongoDB native Tool, Azure Cosmos DB veri yazarken SSL 'yi **etkinleştirmelidir** .

## <a name="perform-the-migration"></a>Geçiş gerçekleştirme

1. Hangi veritabanı (ler) ve koleksiyonları geçirmek istediğinizi seçin. Bu örnekte, *EDX* veritabanındaki bir *sorgu* koleksiyonunu mongodb 'den Azure Cosmos DB 'a geçiriyoruz.

Bu bölümün geri kalanında, önceki bölümde seçtiğiniz araçların çiftini kullanarak size kılavuzluk edilir.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoımport*

1. Kaynak MongoDB örneğinden verileri dışarı aktarmak için MongoDB örnek makinesinde bir Terminal açın. Bir Linux makinegerekliyse, şunu yazın

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    Windows 'ta yürütülebilir dosya olur `mongoexport.exe` . *Ana bilgisayar*, *bağlantı noktası*, *Kullanıcı adı* ve *parola* , mevcut MongoDB veritabanı örneğinizin özelliklerine bağlı olarak doldurulmalıdır. 
    
    Ayrıca, MongoDB veri kümesinin yalnızca bir alt kümesini dışarı aktarmayı da tercih edebilirsiniz. Bunu yapmanın bir yolu, ek bir filtre bağımsız değişkeni eklemektir:
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    Yalnızca filtreyle eşleşen belgeler `{"field1":"value1"}` verilecek.

    Çağrıyı yürütmeden bir dosyanın oluşturulduğunu görmeniz gerekir `edx.json` :

    ![Mongoexport çağrısının ekran görüntüsü.](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. Azure Cosmos DB aktarmak için aynı terminalden yararlanabilirsiniz `edx.json` . `mongoimport`Bir Linux makinesinde çalıştırıyorsanız, şunu yazın

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    Windows 'ta yürütülebilir dosya olur `mongoimport.exe` . *Konak*, *bağlantı noktası*, *Kullanıcı adı* ve *parola* , daha önce topladığınız Azure Cosmos DB kimlik bilgilerine göre doldurulmalıdır. 
1. *Mongoımport*'tan Terminal çıkışını **izleyin** . Geçiş durumunda güncelleştirmeleri içeren terminalde metin satırlarını yazdırıldığını görmeniz gerekir:

    ![Mongoımport çağrısının ekran görüntüsü.](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. Son olarak, geçişin başarılı olduğunu **doğrulamak** için Azure Cosmos DB inceleyin. Azure Cosmos DB portalını açın ve Veri Gezgini gidin. *Importedquery* koleksiyonu içeren bir *EDX* veritabanının oluşturulduğunu (1) görmeniz gerekir ve (2) yalnızca bir veri alt kümesini verirseniz *ımportedquery* *yalnızca* istenen veri alt kümesiyle eşleşen belgeleri içermelidir. Aşağıdaki örnekte, filtreyle eşleşen yalnızca bir belge vardır `{"field1":"value1"}` :

    ![Cosmos DB veri doğrulamasının ekran görüntüsü.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. MongoDB örneğinizi bir BSON veri dökümü oluşturmak için MongoDB örnek makinesinde bir Terminal açın. Bir Linux makinegerekliyse, şunu yazın

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    *Ana bilgisayar*, *bağlantı noktası*, *Kullanıcı adı* ve *parola* , mevcut MongoDB veritabanı örneğinizin özelliklerine bağlı olarak doldurulmalıdır. Bir `edx-dump` dizinin üretilmekte olduğunu ve `edx-dump` kaynak MongoDB örneğinizin kaynak hiyerarşisini (veritabanı ve koleksiyon yapısı) yeniden ürettiğini görmeniz gerekir. Her koleksiyon bir BSON dosyası tarafından temsil edilir:

    ![Mongodump çağrısının ekran görüntüsü.](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. İçeriğini Azure Cosmos DB ' ye geri yüklemek için aynı terminalden yararlanabilirsiniz `edx-dump` . `mongorestore`Bir Linux makinesinde çalıştırıyorsanız, şunu yazın

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    Windows 'ta yürütülebilir dosya olur `mongorestore.exe` . *Konak*, *bağlantı noktası*, *Kullanıcı adı* ve *parola* , daha önce topladığınız Azure Cosmos DB kimlik bilgilerine göre doldurulmalıdır. 
1. *Mongorestore*'tan Terminal çıkışını **izleyin** . Geçiş durumunda, Terminal güncelleştirmesine satır yazdırıldığını görmeniz gerekir:

    ![Mongorestore çağrısının ekran görüntüsü.](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. Son olarak, geçişin başarılı olduğunu **doğrulamak** için Azure Cosmos DB inceleyin. Azure Cosmos DB portalını açın ve Veri Gezgini gidin. *Importedquery* koleksiyonu içeren bir *EDX* veritabanının oluşturulduğunu (1) ve (2) *ımportedquery* 'nin kaynak koleksiyondaki *Tüm* veri kümesini içermesi gerektiğini görmeniz gerekir:

    ![Cosmos DB mongorestore verilerini doğrulama ekran görüntüsü.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>Geçiş sonrası iyileştirmesi

MongoDB veritabanında depolanan verileri MongoDB için Azure Cosmos DB API 'sine geçirdikten sonra, Azure Cosmos DB bağlanabilir ve verileri yönetebilirsiniz. Ayrıca, dizin oluşturma ilkesini iyileştirmek, varsayılan tutarlılık düzeyini güncelleştirmek veya Azure Cosmos DB hesabınız için genel dağıtımı yapılandırmak gibi geçiş sonrası en iyi duruma getirme adımlarını da gerçekleştirebilirsiniz. Daha fazla bilgi için, [geçiş sonrası iyileştirme](../cosmos-db/mongodb-post-migration.md) makalesine bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Cosmos DB hizmeti bilgileri](https://azure.microsoft.com/services/cosmos-db/)
* [MongoDB veritabanı araçları belgeleri](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>Sonraki adımlar

* Microsoft [veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/)'ndaki ek senaryolar için geçiş kılavuzunu gözden geçirin.