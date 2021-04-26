---
title: Azure Depolama Gezgini kullanarak Azure Cosmos DB kaynaklarını yönetme
description: Azure Depolama Gezgini kullanarak Azure Cosmos DB bağlanıp kaynaklarını yönetme hakkında bilgi edinin.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 5b09ce48226b3c31efce4966ec776c10931cc391
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348662"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Azure Depolama Gezgini kullanarak Azure Cosmos DB kaynaklarını yönetme
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB bağlanmak için Azure Depolama Gezgini 'ni kullanabilirsiniz. Windows, macOS veya Linux 'tan Azure 'da ve bağımsız bulutlarda barındırılan Azure Cosmos DB hesaplara bağlanmanızı sağlar.

Farklı Azure varlıklarınızı tek bir yerde yönetmek için aynı aracı kullanın. Depolama Blobları ve kuyrukları gibi diğer Azure varlıklarıyla birlikte Azure Cosmos DB varlıklarını yönetebilir, verileri işleyebilir, saklı yordamları ve Tetikleyicileri güncelleştirebilirsiniz. Azure Depolama Gezgini, SQL, MongoDB, Graph ve tablo API 'Leri için yapılandırılmış Cosmos hesaplarını destekler.

> [!NOTE]
> Depolama Gezgini ile Azure Cosmos DB tümleştirmesi kullanımdan kaldırıldı. Bu sürümden itibaren en az 1 yıl boyunca mevcut işlevler kaldırılmayacaktır. Bunun yerine [Azure portalını](https://portal.azure.com/), [Azure Portal Desktop uygulamasını](https://portal.azure.com/App/Download) veya tek başına [Azure Cosmos DB Gezginini](data-explorer.md) kullanmanız gerekir. Alternatif seçenekler arasında, şu anda Depolama Gezgini’nde desteklenmeyen çok sayıda yeni özellik yer alır.

## <a name="prerequisites"></a>Önkoşullar

MongoDB için bir SQL API 'si veya Azure Cosmos DB API 'SI olan Cosmos hesabı. Hesabınız yoksa Azure portal bir hesap oluşturabilirsiniz. Daha fazla bilgi için bkz. [Azure Cosmos DB: .net Ile SQL API web uygulaması derleme ve Azure Portal](create-sql-api-dotnet.md) .

## <a name="installation"></a>Yükleme

En yeni Azure Depolama Gezgini bitleri yüklemek için bkz. [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/). Windows, Linux ve macOS sürümlerini destekliyoruz.

## <a name="connect-to-an-azure-subscription"></a>Bir Azure aboneliğine Bağlanma

1. **Azure Depolama Gezgini** yükledikten sonra sol bölmedeki **eklenti** simgesini seçin.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Sol bölmedeki eklenti simgesini gösteren ekran görüntüsü.":::

1. **Azure Hesabı Ekle**’yi seçip **Oturum açın**’a tıklayın.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Azure hesabı ekle radyo düğmesinin seçili olduğunu ve Azure ortamı açılır menüsünü gösteren Azure depolama 'ya Bağlan penceresinin ekran görüntüsü.":::

1. **Azure oturum açma** Iletişim kutusunda **oturum aç**' ı seçin ve Azure kimlik bilgilerinizi girin.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Azure aboneliğiniz için kimlik bilgilerinizin nereye girileceğini gösteren oturum açma penceresinin ekran görüntüsü.":::

1. Listeden aboneliğinizi seçip **Uygula**’yı seçin.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Hesap yönetimi bölmesinin, aboneliklerin bir listesini ve Uygula düğmesini gösteren ekran görüntüsü.":::

    Gezgin bölmesi güncelleştirilir ve seçili abonelikteki hesapları gösterir.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Seçilen abonelikteki hesapları gösterecek şekilde güncelleştirilmiş gezgin bölmesinin ekran görüntüsü.":::

    **Cosmos DB hesabınız** Azure aboneliğinize bağlı.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>Azure Cosmos DB bağlanmak için bir bağlantı dizesi kullanın

Bir Azure Cosmos DB bağlanmak için bir bağlantı dizesi kullanabilirsiniz. Bu yöntem yalnızca SQL ve tablo API 'Lerini destekler. Bağlantı dizesiyle bağlanmak için şu adımları izleyin:

1. Sol ağaçta **yerel ve bağlı** ' yı bulun, **Cosmos DB hesaplar**' a sağ tıklayın ve ardından **Cosmos DB Bağlan**' ı seçin.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Sağ tıkladıktan sonra, Azure Cosmos D B 'ye Bağlan vurgulanmış şekilde açılan menüyü gösteren ekran görüntüsü.":::

2. **Cosmos DB Bağlan** penceresinde:
   1. Açılan menüden API 'yi seçin.
   1. Bağlantı dizenizi **bağlantı dizesi** kutusuna yapıştırın. Birincil bağlantı dizesinin nasıl alınacağı hakkında bilgi için bkz. [bağlantı dizesi alma](manage-with-powershell.md#list-keys).
   1. Bir **Hesap etiketi** girin ve ardından Özeti denetlemek için **İleri** ' yi seçin.
   1. Azure Cosmos DB hesabını bağlamak için **Bağlan** ' ı seçin.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="API açılan menüsünü, bağlantı dizesi kutusunu ve hesap etiketi kutusunu gösteren Cosmos D 'ye bağlanma penceresinin ekran görüntüsü.":::

> [!NOTE]
> Azure Depolama Gezgini, Azure Cosmos DB bağlantı dizesinin geçersiz biçimde olduğunu gösteriyorsa, bağlantı dizesinin sonunda noktalı virgül () olduğundan emin olun `;` . Geçerli bir Azure Cosmos DB bağlantı dizesi örneği şöyle olabilir: `AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>Azure Cosmos DB bağlanmak için yerel bir öykünücü kullanın

Öykünücüle bir Azure Cosmos DB bağlanmak için aşağıdaki adımları kullanın. Bu yöntem yalnızca SQL hesaplarını destekler.

1. Cosmos DB öykünücü yükleyip açın. Öykünücüyü yüklemek için, bkz. [Cosmos DB öykünücü](./local-emulator.md).

1. Sol ağaçta **yerel ve bağlı** ' yı bulun, **Cosmos DB hesaplar**' a sağ tıklayın ve ardından **Cosmos DB öykünücüye Bağlan**' ı seçin.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Sağ tıkladıktan sonra, Azure Cosmos D B öykünücüsünde vurgulanan menüyü gösteren ekran görüntüsü.":::

1. **Cosmos DB Bağlan** penceresinde:
   1. Bağlantı dizenizi **bağlantı dizesi** kutusuna yapıştırın. Birincil bağlantı dizesini alma hakkında daha fazla bilgi için bkz. [bağlantı dizesini alma](manage-with-powershell.md#list-keys).
   1. Bir **Hesap etiketi** girin ve ardından Özeti denetlemek için **İleri** ' yi seçin.
   1. Azure Cosmos DB hesabını bağlamak için **Bağlan** ' ı seçin.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Bağlantı dizesi kutusunu ve hesap etiketi kutusunu gösteren Cosmos D B 'ye Bağlan penceresinin ekran görüntüsü.":::

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB kaynak yönetimi

Bir Azure Cosmos DB hesabını yönetmek için aşağıdaki işlemleri kullanın:

* Azure portal hesabı açın.
* Kaynağı hızlı erişim listesine ekleyin.
* Kaynakları arayın ve yenileyin.
* Veritabanları oluşturun ve silin.
* Koleksiyonlar oluşturun ve silin.
* Belge oluşturun, düzenleyin, silin ve filtreleyin.
* Saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri yönetin.

### <a name="quick-access-tasks"></a>Hızlı erişim görevleri

Birçok hızlı eylem görevini gerçekleştirmek için Gezgin bölmesinde bir aboneliğe sağ tıklayabilirsiniz, örneğin:

* Azure Cosmos DB bir hesabı veya veritabanını sağ tıklatın ve ardından **portalda aç** ' ı seçerek Azure Portal tarayıcıdaki kaynağı yönetin.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Sağ tıkladıktan sonra, portalda aç ' ı vurgulanmış şekilde görüntülenen ekran görüntüsü.":::

* Azure Cosmos DB bir hesabı, veritabanını veya toplamayı sağ tıklatın ve hızlı erişim menüsüne eklemek için **hızlı erişim 'e Ekle** ' yi seçin.

* Seçili yol altında anahtar sözcük aramasını etkinleştirmek için **buradan ara** ' yı seçin.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Arama kutusunun vurgulandığını gösteren ekran görüntüsü.":::

### <a name="database-and-collection-management"></a>Veritabanı ve koleksiyon yönetimi

#### <a name="create-a-database"></a>Veritabanı oluşturma

1. Azure Cosmos DB hesabına sağ tıklayın ve ardından **veritabanı oluştur**' u seçin.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Veritabanı oluştur vurgulanarak sağ tıkladıktan sonra görüntülenen menüyü gösteren ekran görüntüsü.":::

1. Veritabanı adını girip, ardından Tamam ' a **basın.**

#### <a name="delete-a-database"></a>Veritabanı silme

1. Veritabanına sağ tıklayın ve ardından **veritabanını sil**' i seçin. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Sağ tıkladıktan sonra, veritabanını sil vurgulanarak görüntülenen ekran görüntüsü.":::

1. Açılır pencerede **Evet** ' i seçin. Veritabanı düğümü silinir ve Azure Cosmos DB hesabı otomatik olarak yenilenir.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Evet düğmesi vurgulanmış şekilde onay penceresinin ekran görüntüsü.":::

#### <a name="create-a-collection"></a>Koleksiyon oluşturma

1. Veritabanınıza sağ tıklayın ve ardından **koleksiyon oluştur**' u seçin.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Koleksiyon oluştur vurgulanarak sağ tıkladıktan sonra görüntülenen menüyü gösteren ekran görüntüsü.":::

1. Koleksiyon Oluştur penceresinde, **koleksıyon kimliği** ve **depolama kapasitesi** gibi istenen bilgileri girin ve bu şekilde devam edin. Son ' u seçerek **Tamam ' ı** seçin.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Koleksiyon oluştur penceresinin, toplama g/s kutusunu ve depolama kapasitesi düğmelerini gösteren ekran görüntüsü.":::

1. Bir bölüm anahtarı belirleyebilmeniz için **sınırsız** ' ı seçin ve ardından son ' u seçerek **Tamam** ' ı seçin.

   > [!NOTE]
   > Bir koleksiyon oluşturduğunuzda bir bölüm anahtarı kullanılırsa, oluşturma işlemi tamamlandıktan sonra, koleksiyonda bölüm anahtarı değerini değiştiremezsiniz.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Koleksiyon oluştur penceresinin, depolama kapasitesi için seçili sınırsız ve bölüm anahtarı kutusunun vurgulandığı ekran görüntüsü.":::

#### <a name="delete-a-collection"></a>Koleksiyonu silme

- Koleksiyona sağ tıklayın, **koleksiyonu Sil**' i seçin ve ardından açılır pencerede **Evet** ' i seçin.

    Koleksiyon düğümü silinir ve veritabanı otomatik olarak yenilenir.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Sağ tıkladıktan sonra, koleksiyonu Sil vurgulanmış şekilde görüntülenen ekran görüntüsü.":::

### <a name="document-management"></a>Belge yönetimi

#### <a name="create-and-modify-documents"></a>Belge oluşturma ve değiştirme

- Sol bölmedeki **Belgeler** ' i açın, **Yeni belge**' yi seçin, sağ bölmedeki Içeriği düzenleyin ve ardından **Kaydet**' i seçin.
- Ayrıca var olan bir belgeyi güncelleştirebilir ve ardından **Kaydet**' i seçebilirsiniz. Değişiklikleri atmak için **at**' ı seçin.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Sol bölmede vurgulanan belgeleri gösteren ekran görüntüsü. Sağ bölmede yeni belge, Kaydet ve at vurgulanacaktır.":::

#### <a name="delete-a-document"></a>Bir belgeyi silme

* Seçili belgeyi silmek için **Sil** düğmesini seçin.

#### <a name="query-for-documents"></a>Belgeler için sorgu

* Belge filtresini düzenlemek için bir [SQL sorgusu](./sql-query-getting-started.md)girin ve ardından **Uygula**' yı seçin.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Sağ bölmenin filtre ve uygulama düğmelerini, KIMLIK numarasını ve vurgulanan sorgu kutusunu gösteren ekran görüntüsü.":::

### <a name="graph-management"></a>Graf yönetimi

#### <a name="create-and-modify-a-vertex"></a>Köşe oluşturma ve değiştirme

* Yeni bir köşe oluşturmak için sol bölmeden **grafik** ' i açın, **Yeni köşe**' i seçin, Içeriği düzenleyin ve ardından **Tamam**' ı seçin.
* Varolan bir köşeyi değiştirmek için sağ bölmedeki kalem simgesini seçin.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Sol bölmede seçilen grafiğin gösterildiği ve sağ bölmede yeni köşe ve kalem simgesinin vurgulandığı ekran görüntüsü.":::

#### <a name="delete-a-graph"></a>Graf silme

* Bir köşeyi silmek için köşe adının yanındaki geri dönüşüm kutusu simgesini seçin.

#### <a name="filter-for-graph"></a>Grafik filtresi

* Grafik filtresini düzenlemek için bir [Gremlin sorgusu](gremlin-support.md)girin ve sonra **Filtre Uygula**' yı seçin.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Sol bölmede seçilen grafiğin gösterildiği ve sağ bölmede vurgulanmış filtre ve sorgu kutusunun gösterildiği ekran görüntüsü.":::

### <a name="table-management"></a>Tablo yönetimi

#### <a name="create-and-modify-a-table"></a>Tablo oluşturma ve değiştirme

* Yeni bir tablo oluşturmak için:
   1. Sol bölmede **varlıklar**' ı açın ve ardından **Ekle**' yi seçin.
   1. **Varlık Ekle** iletişim kutusunda içeriği düzenleyin.
   1. Özellik eklemek için **Özellik Ekle** düğmesini seçin.
   1. **Ekle**'yi seçin.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="Sol bölmede vurgulanan varlıkları gösteren ekran görüntüsü; Ekle, Düzenle, Özellik Ekle ve sağ bölmede vurgulanmış olarak ekle.":::

* Bir tabloyu değiştirmek için **Düzenle**' yi seçin, içeriği değiştirin ve ardından **Güncelleştir**' i seçin.

   

#### <a name="import-and-export-table"></a>Tabloyu içeri ve dışarı aktarma

* İçeri aktarmak için **Içeri aktar** düğmesini seçin ve ardından mevcut bir tabloyu seçin.
* Dışarı aktarmak için **dışarı aktar** düğmesini seçin ve ardından bir hedef seçin.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Sağ bölmede vurgulanan Içeri ve dışarı aktarma düğmelerinin gösterildiği ekran görüntüsü.":::

#### <a name="delete-entities"></a>Varlıkları silme

* Varlıkları seçin ve ardından **Sil** düğmesini seçin.

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Sağ bölmede Sil düğmesinin vurgulandığı ve Evet vurgulu bir onay penceresi gösteren ekran görüntüsü.":::

#### <a name="query-a-table"></a>Tablo sorgulama

- **Sorgu** düğmesini seçin, bir sorgu koşulu girin ve sonra **sorguyu Yürüt** düğmesini seçin. Sorgu bölmesini kapatmak için **sorguyu kapat** düğmesini seçin.

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Sağ bölmenin sorgu Yürüt düğmesini ve sorguyu Kapat düğmesinin vurgulandığı ekran görüntüsü.":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Saklı yordamları, tetikleyicileri ve UDF'leri yönetme

* Saklı yordam oluşturmak için:
  1. Sol ağaçta **saklı yordamlar**' a sağ tıkladıktan sonra **saklı yordam oluştur**' u seçin.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Sol bölmenin, sağ tıkladıktan sonra, saklı yordam oluştur vurgulanmış şekilde görüntülenen ekran görüntüsü.":::
  
  1. Sola bir ad girin, sağ bölmedeki saklı yordam betikleri ' ni girin ve ardından **Oluştur**' u seçin.
  
* Varolan bir saklı yordamı düzenlemek için, yordama çift tıklayın, güncelleştirmeyi yapın ve ardından kaydetmek üzere **Güncelleştir** ' i seçin. Ayrıca, değişikliği iptal etmek için **at** ' ı da seçebilirsiniz.

* **Tetikleyiciler** ve **udf** işlemleri, **saklı yordamlara** benzerdir.

## <a name="troubleshooting"></a>Sorun giderme

Aşağıda, Depolama Gezgini Azure Cosmos DB kullandığınızda ortaya çıkan yaygın sorunlara yönelik çözümler verilmiştir.

### <a name="sign-in-issues"></a>Oturum açma sorunları

İlk olarak, sorunu düzeltiyor olup olmadığını görmek için uygulamanızı yeniden başlatın. Sorun devam ederse sorun gidermeye devam edin.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Sertifika zincirindeki otomatik olarak imzalanan sertifika

Bu hatayı görmenizin birkaç nedeni vardır, en yaygın iki durum şunlardır:

* *Saydam bir proxy*'nin arkasında olursunuz. BT departmanınız gibi birisi, HTTPS trafiğini keser, şifresini çözer ve kendinden imzalı bir sertifika kullanarak şifreler.

* Virüsten koruma yazılımı gibi yazılımlar çalıştırıyorsunuz. Yazılım, otomatik olarak imzalanan bir TLS/SSL sertifikasını aldığınız HTTPS iletilerine çıkartır.

Depolama Gezgini kendinden imzalı bir sertifika bulduğunda, aldığı HTTPS iletisinin üzerinde oynanıp oynanmadığını bilmez. Otomatik olarak imzalanan sertifikanın bir kopyasına sahipseniz, Depolama Gezgini buna güvenmesini söyleyebilirsiniz. Sertifikayı hangi sunucudan eklediyseniz emin değilseniz, şunu bulmayı denemek için aşağıdaki adımları izleyebilirsiniz:

1. OpenSSL 'yi yükler:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): tüm hafif sürümler tamam.
     - macOS ve Linux: işletim sisteminize eklenmelidir.

1. OpenSSL Çalıştır:
    * Windows: Install dizinine gidin **ve sonra da** **openssl.exe** çift tıklayın.
    * Mac ve Linux: bir terminalden **OpenSSL** yürütün.
1. Yürütün `s_client -showcerts -connect microsoft.com:443` .
1. Otomatik olarak imzalanan sertifikaları bulun. Otomatik olarak imzalanan bir emin değilseniz, konunun ("s:") ve verenin ("i:") aynı olduğunu arayın.
1. Otomatik olarak imzalanan bir sertifika bulursanız, **-----Başlangıç sertifikası-----** ve dahil olmak üzere **-----son sertifikaya-----** yeni bir sertifika ekleyerek ve bu her şeyi kopyalayıp yapıştırın. Her biri için CER dosyası.
1. Depolama Gezgini açın ve ardından   >  **SSL sertifikalarını** Düzenle  >  **sertifikaları içeri aktar**' a gidin. Bulmak, seçmek ve sonra açmak için dosya seçiciyi kullanın. Oluşturduğunuz CER dosyaları.

Otomatik olarak imzalanan sertifika bulamazsanız, daha fazla yardım için geri bildirim gönderebilirsiniz.

#### <a name="unable-to-retrieve-subscriptions"></a>Abonelikler alınamıyor

Oturum açtıktan sonra aboneliklerinizi alamadıysanız, bu önerileri deneyin:

* Hesabınızın aboneliklere erişimi olduğunu doğrulayın. Bunu yapmak için [Azure Portal](https://portal.azure.com/)oturum açın.
* Doğru ortamda oturum açtığınızdan emin olun:
  * [Azure](https://portal.azure.com/)
  * [Azure Çin](https://portal.azure.cn/)
  * [Azure Almanya](https://portal.microsoftazure.de/)
  * [Azure ABD Kamu](https://portal.azure.us/)
  * Özel ortam/Azure Stack
* Bir proxy 'nin arkasındaysanız, Depolama Gezgini proxy 'nin düzgün yapılandırıldığından emin olun.
* Hesabı kaldırın ve sonra yeniden ekleyin.
* Aşağıdaki dosyaları giriş dizininizden silin (örneğin: C:\Users\ContosoUser) ve sonra hesabı tekrar ekleyin:
  * .adalcache
  * .devaccounts
  * .extaccounts
* Geliştirici konsolunu açmak için F12 tuşuna basın. Oturum açtığınızda konsolu hata iletileri için izleyin.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="Konsol vurgulanmış olarak gösterilen geliştirici araçları konsolunun ekran görüntüsü.":::

#### <a name="unable-to-see-the-authentication-page"></a>Kimlik doğrulaması sayfası görülemiyor

Kimlik doğrulaması sayfasını göremiyorsanız:

* Bağlantınızın hızına bağlı olarak, oturum açma sayfasının yüklenmesi biraz zaman alabilir. Kimlik doğrulama iletişim kutusunu kapatmadan önce en az bir dakika bekleyin.
* Bir proxy 'nin arkasındaysanız, Depolama Gezgini proxy 'nin düzgün yapılandırıldığından emin olun.
* Geliştirici araçları konsolunda (F12), kimlik doğrulamasının neden çalışmadığını öğrenmek için yanıtları izleyin.

#### <a name="cant-remove-an-account"></a>Hesap kaldırılamıyor

Bir hesabı kaldıramazsanız veya yeniden kimlik doğrulaması bağlantısı hiçbir şey yapmazsa:

* Aşağıdaki dosyaları giriş dizininizden silin ve sonra hesabı tekrar ekleyin:
  * .adalcache
  * .devaccounts
  * .extaccounts

* SAS bağlı Depolama kaynaklarını kaldırmak istiyorsanız şunları silin:
  * Windows için %AppData%/StorageExplorer klasörü
  * /Users/<your_name>/Library/Application Support destek/macOS için StorageExplorer
  * Linux için ~/.config/StorageExplorer
  
  > [!NOTE]
  > Bu dosyaları silerseniz, **tüm kimlik bilgilerinizi yeniden girmeniz gerekir**.

### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS ara sunucu sorunu

ASE 'de bir HTTP/HTTPS proxy yapılandırdığınızda, sol ağaçta Azure Cosmos DB düğümleri listeleyemiyorum. Azure portal Azure Cosmos DB veri Gezgini 'ni bir iş olarak bir arada kullanabilirsiniz.

### <a name="development-node-under-local-and-attached-node-issue"></a>"Yerel ve Bağlı" düğümünün altındaki "Geliştirme" düğümü sorunu

Sol ağaçta **yerel ve ekli** düğüm altında **geliştirme** düğümünü seçtikten sonra yanıt alınmaz. Bu beklenen bir davranıştır.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Seçilen geliştirme düğümünü gösteren ekran görüntüsü.":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>**Yerel ve bağlı** düğümde bir Azure Cosmos DB hesabı iliştirme hatası

**Yerel ve bağlı** düğüme bir Azure Cosmos DB hesabı iliştirdikten sonra aşağıdaki hatayı görürseniz doğru bağlantı dizesini kullandığınızdan emin olun.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="GetAddrInfo ENOTFOUND öğesini belirten alt kaynaklar hata açılır penceresinin ekran görüntüsü.":::

### <a name="expand-azure-cosmos-db-node-error"></a>Azure Cosmos DB düğümünü genişletme hatası

Sol ağaçta düğümleri genişletmeye çalıştığınızda aşağıdaki hatayla karşılaşabilirsiniz.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Bu Cosmos D B hesabına bağlanamadığını belirten alt kaynakları alma hata açılır penceresi ekran görüntüsü.":::

Şu önerileri deneyin:

* Azure Cosmos DB hesabının hazırlama ilerleme durumunda olup olmadığını denetleyin. Hesap başarıyla oluşturulduğunda tekrar deneyin.
* Hesap, **hızlı erişim** veya **yerel ve bağlı** düğümlerin altındaysa, hesabın silinip silinmediğini denetleyin. Bu durumda, düğümü el ile kaldırmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Depolama Gezgini Azure Cosmos DB nasıl kullanacağınızı görmek için bu videoyu izleyin: [Azure Depolama Gezgini 'de Azure Cosmos DB kullanma](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* [Depolama Gezgini ile çalışmaya başlama](../vs-azure-tools-storage-manage-with-storage-explorer.md) konusunda Depolama Gezgini hakkında daha fazla bilgi edinin ve daha fazla hizmet bağlayın.
