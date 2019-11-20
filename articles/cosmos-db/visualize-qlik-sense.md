---
title: Verilerinizi Azure Cosmos DB ve görselleştirmek için Qlik Sense bağlama
description: Bu makalede, verilerinizi Qlik Sense ve görselleştirmeye Azure Cosmos DB bağlamak için gereken adımlar açıklanmaktadır.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 3a955060eb5f19544860c1c97abe1577084bef24
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985539"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Verilerinizi Azure Cosmos DB ve görselleştirmek için Qlik Sense bağlama

Qlik Sense, farklı kaynaklardaki verileri tek bir görünümde birleştiren bir veri görselleştirme aracıdır. Veriler hakkında anında Öngörüler elde edebilmeniz için verilerinize yönelik her olası ilişkiyi Qlik Sense olarak oluşturur. Qlik Sense kullanarak Azure Cosmos DB verileri görselleştirebilirsiniz. Bu makalede, verilerinizi Qlik Sense ve görselleştirmeye Azure Cosmos DB bağlamak için gereken adımlar açıklanmaktadır. 

> [!NOTE]
> Azure Cosmos DB Qlik Sense, SQL API ve yalnızca MongoDB hesapları için Azure Cosmos DB API 'SI için desteklenir.

İle Azure Cosmos DB Qlik Sense bağlayabilirsiniz:

* ODBC bağlayıcısını kullanarak SQL API Cosmos DB.

* Qlik Sense MongoDB bağlayıcısını (Şu anda önizleme aşamasında) kullanarak MongoDB için API Azure Cosmos DB.

* Qlik Sense 'de REST API bağlayıcısını kullanarak MongoDB ve SQL API için API Azure Cosmos DB.

* Qlik Core için gRPC bağlayıcısını kullanarak Mongo DB API 'SI Cosmos DB.
Bu makalede, ODBC bağlayıcısını kullanarak Cosmos DB SQL API 'sine bağlanma ayrıntıları açıklanmaktadır.

Bu makalede, ODBC bağlayıcısını kullanarak Cosmos DB SQL API 'sine bağlanma ayrıntıları açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki yönergeleri uygulamadan önce, aşağıdaki kaynakların size hazırlantığınızdan emin olun:

* [Qlik Sense masaüstünü](https://www.qlik.com/us/try-or-buy/download-qlik-sense) Indirin veya [Qlik Sense Market öğesini yükleyerek](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense)Azure 'da Qlik Sense ayarlayın.

* [Video oyun verilerini](https://www.kaggle.com/gregorut/videogamesales)indirin, bu örnek veriler CSV biçimindedir. Bu verileri bir Cosmos DB hesabında depolayacaksınız ve Qlik Sense içinde görselleştirebilirsiniz.

* Hızlı başlangıç makalesinin [Hesap oluşturma](create-sql-api-dotnet.md#create-account) bölümünde açıklanan adımları kullanarak Azure Cosmos DB BIR SQL API hesabı oluşturun.

* [Veritabanı ve koleksiyon oluşturma](create-sql-api-java.md#add-a-container) – koleksiyon verimlilik DEĞERINI 1000 ru/s olarak ayarlayabilirsiniz. 

* Örnek video oyunu satış verilerini Cosmos DB hesabınıza yükleyin. Azure Cosmos DB veri geçiş aracını kullanarak verileri içeri aktarabilirsiniz, verileri [sıralı](import-data.md#SQLSeqTarget) veya [toplu olarak içeri aktarabilirsiniz](import-data.md#SQLBulkTarget) . Verilerin Cosmos DB hesabına aktarılması yaklaşık 3-5 dakika sürer.

* ODBC sürücüsünü yükleme, yükleme ve yapılandırma [ile Cosmos DB bağlanma](odbc-driver.md) makalesindeki adımları uygulayın. Video oyunu verileri basit bir veri kümesidir ve şemayı düzenlemeniz gerekmez, varsayılan koleksiyon eşleme şemasını kullanmanız yeterlidir.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Qlik Sense Cosmos DB bağlama

1. Qlik Sense açın ve **Yeni uygulama oluştur**' u seçin. Uygulamanız için bir ad girin ve **Oluştur**' u seçin.

   ![Yeni bir Qlik Sense uygulaması oluşturma](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Yeni uygulama başarıyla oluşturulduktan sonra, **uygulamayı aç** ' ı seçin ve **dosyalardan ve diğer kaynaklardan veri Ekle**' yi seçin. 

3. Veri kaynaklarından **ODBC** ' yi seçerek yeni bağlantı Kurulumu penceresini açın. 

4. **Kullanıcı DSN** ' ye geçin ve daha önce oluşturduğunuz ODBC bağlantısını seçin. Bağlantı için bir ad girin ve **Oluştur**' u seçin. 

   ![Yeni bağlantı oluşturma](./media/visualize-qlik-sense/create-new-connection.png)

5. Bağlantıyı oluşturduktan sonra, video oyun verilerinin bulunduğu veritabanını, koleksiyonu ve ardından Önizleme seçeneğini belirleyebilirsiniz.

   ![Veritabanını ve koleksiyonu seçin](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Sonra verileri Qlik Sense 'e yüklemek için **veri Ekle** ' yi seçin. Verileri Qlik Sense 'e yükledikten sonra öngörüler oluşturabilir ve veriler üzerinde analiz gerçekleştirebilirsiniz. Öngörüleri kullanabilir veya kendi uygulamanızı derleyebilir ve video oyunları satışları araştırın. Aşağıdaki görüntüde gösterilmektedir 

   ![Verileri görselleştirme](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>ODBC ile bağlantı kurulurken sınırlamalar 

Cosmos DB, geliştirici ihtiyaçlarına göre modellenen sürücülerle daha az dağıtılmış bir veritabanıdır. ODBC sürücüsü sütunları, veri türlerini ve diğer özellikleri çıkarması için şema içeren bir veritabanı gerektirir. SQL API 'si ANSI SQL olmadığından, normal SQL sorgusu veya ilişkisel özelliği olan DML sözdizimi Cosmos DB SQL API 'SI için geçerli değildir. Bu nedenle, ODBC sürücüsü aracılığıyla verilen SQL deyimleri, tüm yapılar için eşdeğerleri olmayan Cosmos DB özel SQL sözdizimine çevrilir. Bu çeviri sorunlarını engellemek için, ODBC bağlantısını ayarlarken bir şema uygulamanız gerekir. [ODBC sürücüsüne Bağlan](odbc-driver.md) makalesi, şemayı yapılandırmanıza yardımcı olacak öneriler ve yöntemler sunar. Cosmos DB hesabı içindeki her veritabanı/koleksiyon için bu eşlemeyi oluşturmayı unutmayın.

## <a name="next-steps"></a>Sonraki Adımlar

Power BI gibi farklı bir görselleştirme aracı kullanıyorsanız, aşağıdaki belgedeki yönergeleri kullanarak buna bağlanabilirsiniz:

* [Power BI bağlayıcısını kullanarak Cosmos DB verileri görselleştirme](powerbi-visualize.md)
