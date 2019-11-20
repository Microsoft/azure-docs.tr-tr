---
title: Azure Depolama Gezgini kullanarak Azure Cosmos DB kaynaklarını yönetme
description: Azure Depolama Gezgini kullanarak Azure Cosmos DB bağlanıp kaynaklarını yönetme hakkında bilgi edinin.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: dech
ms.custom: seodec18
ms.openlocfilehash: fd044d4f32aefc00e1b04f7060e0fc0dc74fd6c7
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882360"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Azure Depolama Gezgini'ni kullanarak verilerle çalışma

Azure Depolama Gezgini’nde Azure Cosmos DB kullanılması, kullanıcıların Azure Cosmos DB varlıklarını yönetmesine, verileri düzenlemesine, saklı yordamların ve tetikleyicilerin yanı sıra Depolama blob’ları ve kuyrukları gibi diğer Azure varlıklarını güncelleştirmesine imkan tanır. Artık farklı Azure varlıklarını aynı aracı kullanarak tek bir yerde yönetebilirsiniz. Şu anda Azure Depolama Gezgini SQL, MongoDB, Graph ve tablo API 'Leri için yapılandırılmış Cosmos hesaplarını destekler.


## <a name="prerequisites"></a>Önkoşullar

MongoDB için SQL API veya Azure Cosmos DB API 'SI ile Cosmos hesabı. Hesabınız yoksa, [Azure Cosmos DB: .NET ve Azure portalı ile bir SQL API'si web uygulaması derleme](create-sql-api-dotnet.md) bölümünde açıklandığı gibi Azure portalından bir hesap oluşturabilirsiniz.

## <a name="installation"></a>Yükleme

Şuradan en yeni Azure Depolama Gezgini bileşenlerini yükleyin: [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) (Windows, Linux ve MAC sürümü artık desteklenmektedir).

## <a name="connect-to-an-azure-subscription"></a>Bir Azure aboneliğine Bağlanma

1. **Azure Depolama Gezgini**’ni yükledikten sonra, aşağıdaki resimde gösterildiği gibi soldaki **eklenti** simgesine tıklayın:
       
   ![Eklenti simgesi](./media/storage-explorer/plug-in-icon.png)
 
2. **Azure Hesabı Ekle**'yi seçip **Oturum açın**'a tıklayın.

   ![Azure aboneliğine bağlanma](./media/storage-explorer/connect-to-azure-subscription.png)

2. **Azure Oturum Açma** iletişim kutusunda **Oturum aç**’ı seçip Azure kimlik bilgilerinizi girin.

    ![Oturum aç](./media/storage-explorer/sign-in.png)

3. Listeden aboneliğinizi seçip **Uygula**’ya tıklayın.

    ![Başvurun](./media/storage-explorer/apply-subscription.png)

    Gezgin bölmesi güncelleştirilir ve seçili abonelikteki hesapları gösterir.

    ![Hesap listesi](./media/storage-explorer/account-list.png)

    **Cosmos DB hesabınızı** Azure aboneliğinize başarıyla bağladınız.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Bağlantı dizesi kullanarak Azure Cosmos DB’ye bağlanma

Azure Cosmos DB’ye bağlanmanın alternatif yollarından biri, bağlantı dizesi kullanmaktır. Bağlantı dizesi kullanarak bağlanmak için aşağıdaki adımları kullanın.

1. Soldaki ağaçtan **Yerel ve Bağlı**’yı bulun ve **Cosmos DB Hesapları**’na sağ tıklayıp **Cosmos DB’ye bağlan...** seçeneğini belirleyin.

    ![Bağlantı dizesiyle Cosmos DB’ye bağlanma](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Yalnızca SQL ve Tablo API’sini destekler. API seçin, **Bağlantı Dizesini** yapıştırın, **Hesap etiketi** girin, **İleri**’ye tıklayın ve özeti denetleyin, ardından **Bağlan**’a tıklayarak Azure Cosmos DB hesabına bağlanın. Birincil bağlantı dizesini alma hakkında daha fazla bilgi için bkz. [bağlantı dizesini alma](manage-with-powershell.md#list-keys).

    ![Connection-string](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Yerel öykünücüyü kullanarak Azure Cosmos DB’ye bağlanma

Öykünücü ile bir Azure Cosmos DB’ye bağlanmak için aşağıdaki adımları uygulayın. Şu anda yalnızca SQL hesabı desteklenmektedir.

1. Öykünücüyü yükleyip başlatın. Öykünücünün nasıl yükleneceği hakkında bilgi için bkz. [Cosmos DB Öykünücüsü](https://docs.microsoft.com/azure/cosmos-db/local-emulator)

2. Soldaki ağaçtan **Yerel ve Bağlı**’yı bulun ve **Cosmos DB Hesapları**’na sağ tıklayıp **Cosmos DB Öykünücüsüne bağlan...** seçeneğini belirleyin.

    ![Öykünücü ile Cosmos DB’ye bağlanma](./media/storage-explorer/emulator-entry.png)

3. Şu anda yalnızca SQL API’si desteklenmektedir. **Bağlantı Dizesini** yapıştırın, **Hesap etiketi** girin, **İleri**’ye tıklayın ve özeti denetleyin, ardından **Bağlan**’a tıklayarak Azure Cosmos DB hesabına bağlanın. Birincil bağlantı dizesini alma hakkında daha fazla bilgi için bkz. [bağlantı dizesini alma](manage-with-powershell.md#list-keys).

    ![Öykünücü ile Cosmos DB’ye bağlan iletişim kutusu](./media/storage-explorer/emulator-dialog.png)


## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB kaynak yönetimi

Bir Azure Cosmos DB hesabını aşağıdaki işlemleri gerçekleştirerek yönetebilirsiniz:
* Azure portalında hesabı açın
* Kaynağı Hhızlı Erişim listesine ekleyin
* Kaynakları arayın ve yenileyin
* Veritabanı oluşturma ve silme
* Koleksiyon oluşturma ve silme
* Belgeleri oluşturun, düzenleyin, silin ve filtreleyin
* Saklı yordamları, tetikleyicileri ve kullanıcı tanımlı işlevleri yönetin

### <a name="quick-access-tasks"></a>Hızlı erişim görevleri

Gezgin bölmesindeki bir aboneliğe sağ tıklayarak birçok hızlı eylem görevi gerçekleştirebilirsiniz:

* Bir Azure Cosmos DB hesabına veya veritabanına sağ tıklayın; **Portalda Aç**’ı seçerek kaynağı Azure portalında, tarayıcıda yönetebilirsiniz.

     ![Portalda açma](./media/storage-explorer/open-in-portal.png)

* Ayrıca, **Hızlı Erişim**’e Azure Cosmos DB hesabı, veritabanı, koleksiyonu ekleyebilirsiniz.
* **Buradan Arayın** özelliği, seçili yol altında anahtar sözcük aramayı etkinleştirir.

    ![buradan arayın](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Veritabanı ve koleksiyon yönetimi
#### <a name="create-a-database"></a>Veritabanı oluşturun 
-   Azure Cosmos DB hesabına sağ tıklayın, **Veritabanı Oluştur**’u seçin, veritabanı adını girin ve işlemi tamamlamak için **Enter** tuşuna basın.
       
    ![Veritabanı oluşturma](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Veritabanı silme
- Veritabanına sağ tıklayın, **Veritabanını Sil**’e tıklayıp açılan pencerede **Evet**’e tıklayın. Veritabanı düğümü silinir ve Azure Cosmos DB hesabı otomatik olarak yenilenir.

    ![Veritabanı silme1](./media/storage-explorer/delete-database1.png)  

    ![Veritabanı silme2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Koleksiyon oluşturun
1. Veritabanınıza sağ tıklayın, **koleksiyon oluştur**' u seçin ve ardından **koleksiyon kimliği**, **depolama kapasitesi**vb. gibi aşağıdaki bilgileri sağlayın. **Tamam** ' a tıklayarak işlemi sona erdirin. 

    ![Koleksiyon oluşturma1](./media/storage-explorer/create-collection.png)

    ![Koleksiyon oluşturma2](./media/storage-explorer/create-collection2.png) 

2. Bölüm anahtarını belirtebilmek için **Sınırsız**’ı seçin ve sonra **Tamam**’a tıklayarak işlemi tamamlayın.

    Bir koleksiyon oluşturulurken bölüm anahtarı kullanılırsa, oluşturma işlemi tamamlandıktan sonra koleksiyondaki bölüm anahtarı değeri değiştirilemez.

    ![Bölüm anahtarı](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Koleksiyon silme
- Koleksiyona sağ tıklayıp **Koleksiyonu Sil**’e tıklayın ve açılan pencerede **Evet**’e tıklayın. 

    Koleksiyon düğümü silinir ve veritabanı otomatik olarak yenilenir.

    ![Koleksiyonu silme](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Belge yönetimi

#### <a name="create-and-modify-documents"></a>Belge oluşturma ve değiştirme
- Yeni bir belge oluşturmak için soldaki pencerede **Belgeler**’i açın, **Yeni Belge**’ye tıklayın, sağ bölmede içeriği düzenleyin ve **Kaydet**’e tıklayın. Ayrıca, mevcut bir belgeyi güncelleştirip **Kaydet**’e tıklayabilirsiniz. Değişiklikler **At** seçeneğine tıklanarak atılabilir.

    ![Belge](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Bir belgeyi silme
- Seçili belgeyi silmek için **Sil** düğmesine tıklayın.

#### <a name="query-for-documents"></a>Belgeler için sorgu
- Bir [SQL sorgusu](how-to-sql-query.md) girip **Uygula**’ya tıklayarak belge filtresini düzenleyin.

    ![Belge Filtresi](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Graf yönetimi

#### <a name="create-and-modify-vertex"></a>Köşe oluşturma ve değiştirme
1. Yeni bir köşe oluşturmak için sol pencereden **Graf**’ı açın, **Yeni Köşe**’ye tıklayın, ardından **Tamam**’a tıklayın.    
2. Mevcut bir köşeyi değiştirmek için sağ bölmede kalem simgesine tıklayın.   

    ![Graph](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Graf silme
- Bir köşeyi silmek için köşe adının yanındaki geri dönüşüm kutusu simgesine tıklayın.

#### <a name="filter-for-graph"></a>Grafik filtresi
- Bir [gremlin sorgusu](gremlin-support.md) girerek grafik filtresini düzenleyin ve sonra **Filtreyi Uygula**’ya tıklayın.

    ![Graf Filtresi](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Tablo yönetimi

#### <a name="create-and-modify-table"></a>Tablo oluşturma ve değiştirme
1. Yeni bir tablo oluşturmak için, sol pencereden **Varlıklar**’ı açın, **Ekle**’ye tıklayın, **Varlık Ekle** iletişim kutusundaki içeriği düzenleyin, **Özellik Ekle** düğmesine tıklayarak özellik ekleyin ve sonra **Ekle**’ye tıklayın.
2. Bir tabloyu değiştirmek için **Düzenle**’ye tıklayın, içeriği değiştirin ve sonra **Güncelleştir**’e tıklayın.

    ![Tablo](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Tabloyu içeri ve dışarı aktarma
1. İçeri aktarmak için, **İçeri Aktar** düğmesine tıklayın ve mevcut bir tabloyu seçin.
2. Dışarı aktarmak için, **Dışarı Aktar** düğmesine tıklayın ve bir hedef seçin.

    ![Tablo İçeri ve Dışarı Aktarma](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Varlıkları silme
- Varlıkları seçin ve **Sil** düğmesine tıklayın.

    ![Tablo silme](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Sorgu tablosu
- **Sorgu** düğmesine tıklayın, sorgu koşulunu girin ve sonra **Sorguyu Yürüt** düğmesine tıklayın. **Sorguyu Kapat** düğmesine tıklayarak Sorgu bölmesini kapatın.

    ![Tablo Sorgusu](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Saklı yordamları, tetikleyicileri ve UDF'leri yönetme
* Saklı yordam oluşturmak için soldaki ağaçta **Saklı Yordam**’a sağ tıklayın, **Saklı Yordam Oluştur**’u seçin, sol tarafa bir ad girin, sağdaki pencerede saklı yordam betiklerini yazın ve sonra **Oluştur**’a tıklayın. 
* Ayrıca, mevcut saklı yordamlara çift tıklayıp güncelleştirmeyi yaptıktan sonra değişikliğinizi **Güncelleştir**’e tıklayarak kaydedebilir veya **At**’a tıklayarak atabilirsiniz.

    ![Saklı yordam](./media/storage-explorer/stored-procedure.png)
* **Tetikleyicilere** ve **UDF**’ye yönelik işlemler, **Saklı Yordamlara** benzer.

## <a name="troubleshooting"></a>Sorun giderme

[Azure Depolama Gezgini’ndeki Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/storage-explorer), Windows, macOS veya Linux’tan Sovereign Clouds ve Azure’da barındırılan Azure Cosmos DB hesaplarına bağlanmanıza olanak sağlayan tek başına bir uygulamadır. Azure Cosmos DB varlıklarını yönetmenize, verileri işlemenize, saklı yordamların ve tetikleyicilerin yanı sıra Depolama blobları ve kuyrukları gibi diğer Azure varlıklarını güncelleştirmesine olanak sağlar.

Bunlar Depolama Gezgini’nde Azure Cosmos DB için görülen yaygın sorunların çözümleridir.

### <a name="sign-in-issues"></a>Oturum açma sorunları

Devam etmeden önce uygulamanızı yeniden başlatmayı deneyin ve sorunların düzeltilip düzeltilmediğine bakın.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Sertifika zincirindeki otomatik olarak imzalanan sertifika

Bu hatayı görmenizin birkaç nedeni vardır. En yaygın iki neden şudur:

+ Bir saydam proxy 'nin arkasında olursunuz, yani birisi (BT departmanınız gibi) HTTPS trafiğini kesintiye *uğratır*, şifrelerini çözerek otomatik olarak imzalanan bir sertifika kullanarak şifreleniyor.

+ Aldığınız HTTPS iletilerine otomatik olarak imzalanan SSL sertifikaları ekleyen antivirüs yazılımı gibi bir yazılım çalıştırıyorsunuzdur.

Depolama Gezgini bu "otomatik olarak imzalanan sertifikalardan" biriyle karşılaştığında artık aldığı HTTPS iletisinin kurcalanıp kurcalanmadığını bilemez. Otomatik olarak imzalanan sertifikanın bir kopyası varsa, Depolama Gezgini’ne buna güvenmesi gerektiğini bildirebilirsiniz. Sertifikayı kimin eklediğinden emin değilseniz, aşağıdaki adımları uygulayarak kendiniz bulmaya çalışabilirsiniz:

1. Açık SSL yükleme
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (basit sürümlerden herhangi biri olabilir)
     - Mac ve Linux: İşletim sisteminize eklenmelidir
2. Açık SSL çalıştırma
    - Windows: Yükleme dizinine gidin, ardından **/bin/** konumuna gidip **openssl.exe** dosyasına çift tıklayın.
    - Mac ve Linux: Bir terminalden **openssl** komutunu yürütün
3. `s_client -showcerts -connect microsoft.com:443` yürütme
4. Otomatik olarak imzalanan sertifikaları bulun. Hangisinin otomatik olarak imzalandığından emin değilseniz, konu ("s:") ve veren ("i:") aynı olan sertifikaları bulun.
5.  Otomatik olarak imzalanan bir sertifika bulduktan sonra, **-----BEGIN CERTIFICATE-----** ile **-----END CERTIFICATE-----** arasındaki (bu kısımlar da dahil) her şeyi kopyalayıp yeni bir .cer dosyasına yapıştırın.
6.  Depolama Gezgini’ni açın ve **Düzenle** > **SSL Sertifikaları** > **Sertifikaları İçeri Aktar** bölümüne gidin. Dosya seçicisini kullanarak, oluşturduğunuz .cer dosyalarını bulun, seçin ve açın.

Yukarıdaki adımları kullanarak otomatik olarak imzalanan bir sertifika bulamazsanız daha fazla yardım için geri bildirim gönderebilirsiniz.

#### <a name="unable-to-retrieve-subscriptions"></a>Abonelikler alınamıyor

Başarıyla oturum açtıktan sonra aboneliklerinizi alamıyorsanız:

- [Azure Portal](https://portal.azure.com/)’da oturum açarak, hesabınızın aboneliklerinize erişiminin olduğunu doğrulayın
- Doğru ortamı kullanarak oturum açtığınızdan emin olun ([Azure](https://portal.azure.com/), [Azure Çin](https://portal.azure.cn/), [Azure Almanya](https://portal.microsoftazure.de/), [Azure ABD Kamu](https://portal.azure.us/) veya Özel Ortam/Azure Stack)
- Bir ara sunucunun ardından değilseniz, Depolama Gezgini ara sunucusunu düzgün şekilde yapılandırdığınızdan emin olun
- Hesabı kaldırıp yeniden eklemeyi deneyin
- Aşağıdaki dosyaları giriş dizininizden (örn: C:\Users\ContosoUser) silmeyi ve sonra hesabı yeniden eklemeyi deneyin:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Oturum açarken geliştirici araçları konsolunda (f12) herhangi bir hata iletisi olup olmadığını gözlemleyin

![console](./media/storage-explorer/console.png)

#### <a name="unable-to-see-the-authentication-page"></a>Kimlik doğrulaması sayfası görülemiyor 

Kimlik doğrulaması sayfasını göremiyorsanız:

- Bağlantınızın hızına bağlı olarak, oturum açma sayfasının yüklenmesi biraz zaman alabilir, kimlik doğrulaması iletişim kutusunu kapatmadan önce en az bir dakika bekleyin
- Bir ara sunucunun ardından değilseniz, Depolama Gezgini ara sunucusunu düzgün şekilde yapılandırdığınızdan emin olun
- F12 tuşuna basarak geliştirici konsolunu getirin. Geliştirici konsolundan gelen yanıtları izleyin ve kimlik doğrulamasının neden çalışmadığına dair bir ipucu bulup bulamayacağınızı görün

#### <a name="cannot-remove-account"></a>Hesap kaldırılamıyor

Bir hesabı kaldıramıyorsanız veya yeniden kimlik doğrulama bağlantısı bir işlem yapmazsa

- Aşağıdaki dosyaları giriş dizininizden silmeyi ve sonra hesabı yeniden eklemeyi deneyin:
  - .adalcache
  - .devaccounts
  - .extaccounts
- SAS bağlı Depolama kaynaklarını kaldırmak istiyorsanız şunları silin:
  - Windows için %AppData%/StorageExplorer klasörü
  - /Users/< your_name >/Library/Application Support desteği/Mac için StorageExplorer
  - Linux için ~/.config/StorageExplorer
  - Bu dosyaları silerseniz **tüm kimlik bilgilerinizi yeniden girmeniz gerekir**


### <a name="httphttps-proxy-issue"></a>Http/Https ara sunucu sorunu

ASE’de http/https ara sunucusunu yapılandırırken sol ağaçta Azure Cosmos DB düğümlerini listeleyemezsiniz. Bu bilinen bir sorundur ve sonraki yayında düzeltilecektir. Şimdilik geçici bir çözüm olarak Azure portalında Azure Cosmos DB veri gezginini kullanabilirsiniz. 

### <a name="development-node-under-local-and-attached-node-issue"></a>"Yerel ve Bağlı" düğümünün altındaki "Geliştirme" düğümü sorunu

Soldaki ağaçta "Yerel ve Bağlı" düğümünün altındaki "Geliştirme" düğümüne tıklandıktan sonra bir yanıt yok.  Bu beklenen bir davranıştır. Azure Cosmos DB yerel öykünücüsü, sonraki yayında desteklenecektir.

![Geliştirme düğümü](./media/storage-explorer/development.png)

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>"Yerel ve Bağlı" düğümünde Azure Cosmos DB hesabını ekleme hatası

"Yerel ve Bağlı" düğümünde Azure Cosmos DB hesabını ekledikten sonra aşağıdaki hatayı görürseniz, doğru bağlantı dizesini kullanıp kullanmadığınızı denetleyin.

![Yerel ve Bağlı düğümünde Azure Cosmos DB ekleme hatası](./media/storage-explorer/attached-error.png)

### <a name="expand-azure-cosmos-db-node-error"></a>Azure Cosmos DB düğümünü genişletme hatası

Soldaki ağaç düğümlerini genişletmeye çalışırken aşağıdaki hatayı görebilirsiniz. 

![Genişletme Hatası](./media/storage-explorer/expand-error.png)

Aşağıdaki önerileri deneyin:

- Azure Cosmos DB hesabının, sağlama durumunda olup olmadığını denetleyin ve hesap başarıyla oluşturulduğunda yeniden deneyin.
- Hesap, "Hızlı Erişim" düğümünün veya "Yerel ve Bağlı" düğümlerinin altındaysa, hesabın silinip silinmediğini denetleyin. Silindiyse, düğümü sizin kaldırmanız gerekir.

## <a name="contact-us"></a>Bize ulaşın

Hiçbir çözüm işinize yaramazsa, sorunu düzeltmek için Azure Cosmos DB Geliştirme Araçları Ekibine ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) sorunun ayrıntılarını içeren bir e-posta gönderin.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Depolama Gezgini’nde Azure Cosmos DB’yi kullanmayı öğrenmek için şu videoyu izleyin: [Azure Depolama Gezgini’nde Azure Cosmos DB kullanma](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* [Depolama Gezgini ile çalışmaya başlama](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) konusunda Depolama Gezgini hakkında daha fazla bilgi edinin ve daha fazla hizmet bağlayın.

