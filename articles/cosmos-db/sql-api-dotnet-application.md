---
title: Azure Cosmos DB kullanarak MVC web uygulaması öğreticisini ASP.NET Core
description: Azure Cosmos DB kullanarak bir MVC web uygulaması oluşturmak için MVC öğreticisi ASP.NET Core. Azure App Service-ASP NET Core MVC öğreticisi adım adım ' da barındırılan bir yapılacaklar uygulamasından JSON ve Access verilerini depolayacaksınız.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 528cab915a1ac3918146e428e9ae6b3c401324c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010366"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Öğretici: .NET SDK kullanarak Azure Cosmos DB ile ASP.NET Core MVC web uygulaması geliştirme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Bu öğretici, Azure 'da barındırılan bir ASP.NET MVC uygulamasındaki verileri depolamak ve erişmek için Azure Cosmos DB nasıl kullanacağınızı gösterir. Bu öğreticide .NET SDK V3 ' i kullanırsınız. Aşağıdaki görüntüde, bu makaledeki örneği kullanarak oluşturacağınız Web sayfası gösterilmektedir:

:::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png" alt-text="Bu öğretici tarafından oluşturulan yapılacaklar listesi MVC web uygulaması ekran görüntüsü-ASP NET Core MVC öğretici adım adım":::

Öğreticiyi tamamlamaya yönelik bir zaman yoksa, tüm örnek projeyi [GitHub][GitHub]'dan indirebilirsiniz.

Bu öğreticinin içindekiler:

> [!div class="checklist"]
>
> * Azure Cosmos hesabı oluşturma
> * ASP.NET Core MVC uygulaması oluşturma
> * Uygulamayı Azure Cosmos DB bağlama
> * Veriler üzerinde oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri gerçekleştiriliyor

> [!TIP]
> Bu öğreticide, ASP.NET Core MVC ve Azure App Service kullanarak önceki deneyiminiz olduğunu varsaymaktadır. ASP.NET Core veya [Önkoşul araçları](#prerequisites)' nı yeni kullanıyorsanız, [GitHub][GitHub]'dan tüm örnek projeyi indirmeniz, gerekli NuGet paketlerini eklemeniz ve çalıştırmanız önerilir. Projeyi oluşturduktan sonra, proje bağlamındaki kodla ilgili bilgi edinmek için bu makaleyi gözden geçirebilirsiniz.

## <a name="prerequisites"></a><a name="prerequisites"></a>Önkoşullar

Bu makaledeki yönergeleri izleyerek önce aşağıdaki kaynaklara sahip olduğunuzdan emin olun:

* Etkin bir Azure hesabı. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Bu makaledeki tüm ekran görüntüleri Microsoft Visual Studio Community 2019 ' dir. Farklı bir sürüm kullanıyorsanız, ekranlarınız ve seçenekleriniz tamamen eşleşmeyebilir. Önkoşulları karşıladıysanız çözüm çalışmalıdır.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>1. Adım: Azure Cosmos hesabı oluşturma

Bir Azure Cosmos hesabı oluşturarak başlayalım. Zaten bir Azure Cosmos DB SQL API hesabınız varsa veya Azure Cosmos DB öykünücüsü 'nü kullanıyorsanız [2. Adım: yeni bir ASP.NET MVC uygulaması oluşturma](#create-a-new-mvc-application)bölümüne atlayın.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Sonraki bölümde, yeni bir ASP.NET Core MVC uygulaması oluşturacaksınız.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>2. Adım: yeni bir ASP.NET Core MVC uygulaması oluşturma

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur**' da, C# için **ASP.NET Core Web uygulaması** bulun ve seçin. Devam etmek için **İleri** seçeneğini belirleyin.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png" alt-text="Yeni ASP.NET Core Web uygulaması projesi oluştur":::

1. **Yeni projenizi yapılandırın** bölümünde, projeyi *Todo* olarak adlandırın ve **Oluştur**' u seçin.

1. **Yeni bir ASP.NET Core Web uygulaması oluşturma** bölümünde **Web uygulaması (Model-View-Controller)** öğesini seçin. Devam etmek için **Oluştur** ' u seçin.

   Visual Studio boş bir MVC uygulaması oluşturur.

1.   >  ASP.NET uygulamanızı yerel olarak çalıştırmak için hata **ayıklamayı Başlat** veya F5 ' i seçin.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>3. Adım: projeye Azure Cosmos DB NuGet paketi ekleme

Bu çözüm için ihtiyaç duyduğumuz ASP.NET Core MVC Framework kodunun çoğunu kullandığımıza göre, Azure Cosmos DB bağlanmak için gereken NuGet paketlerini ekleyelim.

1. **Çözüm Gezgini**, projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paket Yöneticisi**' nde, **Microsoft. Azure. Cosmos** arayın ve seçin. **Yükle**'yi seçin.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png" alt-text="NuGet paketini yükler":::

   Visual Studio, Azure Cosmos DB paketini ve bağımlılıklarını indirir ve yükler.

   NuGet paketini yüklemek için **Paket Yöneticisi konsolu 'nu** da kullanabilirsiniz. Bunu yapmak için **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu**' nu seçin. Komut isteminde aşağıdaki komutu yazın:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>4. Adım: ASP.NET Core MVC uygulamasını ayarlama

Şimdi modelleri, görünümleri ve denetleyicileri bu MVC uygulamasına ekleyelim.

### <a name="add-a-model"></a><a name="add-a-model"></a> Model ekleme

1. **Çözüm Gezgini**, **modeller** klasörüne sağ tıklayın, sınıf **Ekle**' yi seçin  >  .

1. **Yeni öğe Ekle**' de yeni sınıf *öğesini. cs* olarak adlandırın ve **Ekle**' yi seçin.

1. *Item. cs* sınıfının içeriğini aşağıdaki kodla değiştirin:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB verileri taşımak ve depolamak için JSON kullanır. `JsonProperty`Özniteliğini kullanarak JSON serileştirmelerini ve nesneleri seri hale getirir. `Item`Sınıfı, özniteliğini gösterir `JsonProperty` . Bu kod, JSON 'a giden Özellik adının biçimini denetler. Ayrıca .NET özelliğini yeniden adlandırır `Completed` .

### <a name="add-views"></a><a name="add-views"></a>Görünümler ekleme

Ardından aşağıdaki görünümleri ekleyelim.

* Öğe oluştur görünümü
* Öğe Sil görünümü
* Öğe ayrıntılarını almak için bir görünüm
* Düzenleme öğesi görünümü
* Tüm öğeleri listelemek için bir görünüm

#### <a name="create-item-view"></a><a name="AddNewIndexView"></a>Öğe görünümü oluştur

1. **Çözüm Gezgini**, **Görünümler** klasörüne sağ tıklayın ve   >  **Yeni klasör** Ekle ' yi seçin. Klasör *öğesini* adlandırın.

1. Boş **öğe** klasörünü sağ tıklatın ve ardından Görünüm **Ekle**' yi seçin  >  .

1. **MVC görünümü Ekle**' de, aşağıdaki değişiklikleri yapın:

   * **Görünüm adı**' nda *Oluştur*' u girin.
   * **Şablon**' da **Oluştur**' u seçin.
   * **Model sınıfı**' nda, **öğe (Todo) öğesini seçin. Modeller)**.
   * **Düzen kullan sayfasını** seçin ve *~/views/Shared/_Layout. cshtml* yazın.
   * **Add (Ekle)** seçeneğini belirleyin.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png" alt-text="MVC görünümü Ekle iletişim kutusunu gösteren ekran görüntüsü":::

1. Sonra **Ekle** ' yi seçin ve Visual Studio 'nun yeni bir şablon görünümü oluşturmasına izin verin. Oluşturulan dosyadaki kodu aşağıdaki içeriklerle değiştirin:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Create.cshtml":::

#### <a name="delete-item-view"></a><a name="AddEditIndexView"></a>Öğe görünümünü sil

1. **Çözüm Gezgini**, **öğe** klasörünü yeniden sağ tıklatın, Görünüm Ekle ' yi seçin   >  .

1. **MVC görünümü Ekle**' de, aşağıdaki değişiklikleri yapın:

   * **Görünüm adı** kutusunda *Sil* yazın.
   * **Şablon** kutusunda **Sil**' i seçin.
   * **Model sınıfı** kutusunda **Öğe (todo.Models)** seçeneğini belirleyin.
   * **Düzen kullan sayfasını** seçin ve *~/views/Shared/_Layout. cshtml* yazın.
   * **Add (Ekle)** seçeneğini belirleyin.

1. Sonra **Ekle** ' yi seçin ve Visual Studio 'nun yeni bir şablon görünümü oluşturmasına izin verin. Oluşturulan dosyadaki kodu aşağıdaki içeriklerle değiştirin:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Delete.cshtml":::

#### <a name="add-a-view-to-get-an-item-details"></a><a name="AddItemIndexView"></a>Öğe ayrıntılarını almak için bir görünüm ekleyin

1. **Çözüm Gezgini**, **öğe** klasörünü yeniden sağ tıklatın, Görünüm **Ekle**' yi seçin  >  .

1. **MVC görünümü Ekle**' de, aşağıdaki değerleri sağlayın:

   * **Görünüm adı**' nda, *Ayrıntılar* girin.
   * **Şablon**' da **Ayrıntılar**' ı seçin.
   * **Model sınıfı**' nda, **öğe (Todo) öğesini seçin. Modeller)**.
   * **Düzen kullan sayfasını** seçin ve *~/views/Shared/_Layout. cshtml* yazın.

1. Sonra **Ekle** ' yi seçin ve Visual Studio 'nun yeni bir şablon görünümü oluşturmasına izin verin. Oluşturulan dosyadaki kodu aşağıdaki içeriklerle değiştirin:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Details.cshtml":::

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Düzenleme öğesi görünümü ekleme

1. **Çözüm Gezgini**, **öğe** klasörünü yeniden sağ tıklatın, Görünüm Ekle ' yi seçin   >  .

1. **MVC görünümü Ekle**' de, aşağıdaki değişiklikleri yapın:

   * **Görünüm adı** kutusunda *Edit* yazın.
   * **Şablon** kutusunda **Düzenle**'yi seçin.
   * **Model sınıfı** kutusunda **Öğe (todo.Models)** seçeneğini belirleyin.
   * **Düzen kullan sayfasını** seçin ve *~/views/Shared/_Layout. cshtml* yazın.
   * **Add (Ekle)** seçeneğini belirleyin.

1. Sonra **Ekle** ' yi seçin ve Visual Studio 'nun yeni bir şablon görünümü oluşturmasına izin verin. Oluşturulan dosyadaki kodu aşağıdaki içeriklerle değiştirin:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Edit.cshtml":::

#### <a name="add-a-view-to-list-all-the-items"></a><a name="AddEditIndexView"></a>Tüm öğeleri listelemek için bir görünüm ekleyin

Son olarak, aşağıdaki adımlarla tüm öğeleri almak için bir görünüm ekleyin:

1. **Çözüm Gezgini**, **öğe** klasörünü yeniden sağ tıklatın, Görünüm Ekle ' yi seçin   >  .

1. **MVC görünümü Ekle**' de, aşağıdaki değişiklikleri yapın:

   * **Görünüm adı** kutusunda *Index* yazın.
   * **Şablon** kutusunda **Liste**'yi seçin.
   * **Model sınıfı** kutusunda **Öğe (todo.Models)** seçeneğini belirleyin.
   * **Düzen kullan sayfasını** seçin ve *~/views/Shared/_Layout. cshtml* yazın.
   * **Add (Ekle)** seçeneğini belirleyin.

1. Sonra **Ekle** ' yi seçin ve Visual Studio 'nun yeni bir şablon görünümü oluşturmasına izin verin. Oluşturulan dosyadaki kodu aşağıdaki içeriklerle değiştirin:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Index.cshtml":::

Bu adımları tamamladıktan sonra, Visual Studio 'daki tüm *cshtml* belgelerini kapatın.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Hizmetleri bildirme ve başlatma

İlk olarak, Azure Cosmos DB bağlanma ve kullanma mantığını içeren bir sınıf ekleyeceğiz. Bu öğreticide, bu mantığı adlı bir sınıfa `CosmosDbService` ve adlı bir arabirime kapsülliyoruz `ICosmosDbService` . Bu hizmet CRUD işlemlerini yapar. Ayrıca, tamamlanmamış öğeleri Listeleme, öğeleri oluşturma, düzenlemesi ve silme gibi akış işlemlerini de okur.

1. **Çözüm Gezgini**, projenize sağ tıklayın ve   >  **Yeni klasör** Ekle ' yi seçin. Klasör *hizmetlerini* adlandırın.

1. **Hizmetler** klasörüne sağ tıklayın, sınıf **Ekle**' yi seçin  >  . Yeni sınıfı *Cosmosdbservice* olarak adlandırın ve **Ekle**' yi seçin.

1. *Cosmosdbservice. cs* içeriğini şu kodla değiştirin:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. **Hizmetler** klasörüne sağ tıklayın, sınıf **Ekle**' yi seçin  >  . Yeni sınıfı *ıosmosdbservice* olarak adlandırın ve **Ekle**' yi seçin.

1. Aşağıdaki kodu *ıcosmosdbservice* sınıfına ekleyin:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Çözümünüzde *Startup. cs* dosyasını açın ve aşağıdaki **ınitializecosmosclientınstanceasync** yöntemini ekleyerek yapılandırmayı okur ve istemciyi başlatır.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync" :::

1. Aynı dosyada, `ConfigureServices` yöntemi ile değiştirin:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

   Bu adımdaki kod, [ASP.NET Core ' de bağımlılık ekleme](/aspnet/core/fundamentals/dependency-injection)yoluyla eklenecek tek bir örnek olarak istemciyi başlatır.

   `Item`Aynı dosyanın yöntemindeki yolları düzenleyerek varsayılan MVC denetleyicisini olarak değiştirin `Configure` :

   ```csharp
    app.UseEndpoints(endpoints =>
          {
                endpoints.MapControllerRoute(
                   name: "default",
                   pattern: "{controller=Item}/{action=Index}/{id?}");
          });
   ```


1. Aşağıdaki kod parçacığında gösterildiği gibi, projenin dosyadaki *appsettings.js* yapılandırmayı tanımlayın:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Denetleyici ekleme

1. **Çözüm Gezgini**, **denetleyiciler** klasörüne sağ tıklayın, denetleyici **Ekle**' yi seçin  >  .

1. **Yapı Iskelesi Ekle**' de **MVC denetleyicisi-boş** ' yı seçin ve **Ekle**' yi seçin.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png" alt-text="Yapı Iskelesi Ekle bölümünde MVC denetleyicisini seçin-boş":::

1. Yeni Controller *ıtemcontroller*'ı adlandırın.

1. *Itemcontroller. cs* içeriğini şu kodla değiştirin:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

Bu uygulamayı siteler arası istek sahteciliği saldırılarına karşı korumaya yardımcı olmak için **Validateantiforgerıtoken** özniteliği burada kullanılır. Görünümleriniz, bu karşı koruma belirteci ile birlikte çalışmalıdır. Daha fazla bilgi ve örnek için bkz. [ASP.NET MVC uygulamasındaki siteler arası Istek forgery (CSRF) saldırılarını önleme][Preventing Cross-Site Request Forgery]. [GitHub][GitHub]'da sağlanan kaynak kodu tam uygulamayı içerir.

Ayrıca, aşırı gönderme saldırılarına karşı korumaya yardımcı olmak için yöntem parametresindeki **bind** özniteliğini de kullanırız. Daha fazla bilgi için bkz. [öğretici: ASP.NET MVC 'de Entity Framework CRUD Işlevselliği uygulama][Basic CRUD Operations in ASP.NET MVC].

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>5. Adım: uygulamayı yerel olarak çalıştırma

Uygulamayı yerel bilgisayarınızda test etmek için aşağıdaki adımları kullanın:

1. Uygulamayı hata ayıklama modunda derlemek için Visual Studio 'da F5 tuşuna basın. Bu işlemin uygulamayı oluşturması ve bir tarayıcıyı daha önce gördüğümüz boş kılavuz sayfasıyla başlatması gerekir:

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png" alt-text="Bu öğretici tarafından oluşturulan yapılacaklar listesi Web uygulamasının ekran görüntüsü":::
   
   Uygulama bunun yerine giriş sayfasında açılırsa `/Item` URL 'ye ekleyin.

1. **Yeni oluştur** bağlantısını seçin ve **ad** ve **Açıklama** alanlarına değer ekleyin. **Tamamlandı** onay kutusunu seçilmemiş olarak bırakın. Bu seçeneği belirlerseniz, uygulama yeni öğeyi tamamlanmış durumuna ekler. Öğe artık ilk listede görünmüyor.

1. **Oluştur**’u seçin. Uygulama sizi **Dizin** görünümüne geri gönderir ve öğe listede görüntülenir. **Yapılacaklar** listenize birkaç öğe ekleyebilirsiniz.

    :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png" alt-text="Dizin görünümünün ekran görüntüsü":::
  
1. Listedeki bir **öğenin** yanındaki **Düzenle** ' yi seçin. Uygulama, **Tamamlanan** bayrak dahil olmak üzere, nesnenizin herhangi bir özelliğini güncelleştirebileceğiniz **düzenleme** görünümünü açar. **Tamamlandı** ' ı ve **Kaydet**' i seçerseniz, uygulama **öğeyi** listede tamamlandı olarak görüntüler.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png" alt-text="Tamamlandı kutusu işaretli dizin görünümünün ekran görüntüsü":::

1. [Cosmos Gezginini](https://cosmos.azure.com) veya Azure Cosmos DB öykünücü Veri Gezgini kullanarak Azure Cosmos DB hizmetindeki verilerin durumunu doğrulayın.

1. Uygulamayı sınadıktan sonra, uygulamanın hata ayıklamasını durdurmak için CTRL + F5 ' i seçin. Dağıtıma hazırsınız!

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>6. Adım: uygulamayı dağıtma

Artık uygulamanın tamamı Azure Cosmos DB ile doğru şekilde çalıştığına göre, bu web uygulamasını Azure App Service’e dağıtacağız.  

1. Bu uygulamayı yayımlamak için **Çözüm Gezgini** ' de projeye sağ tıklayın ve **Yayımla**' yı seçin.

1. **Bir yayımlama hedefi** seçin kısmında **App Service**' yi seçin.

1. Mevcut bir App Service profilini kullanmak için **Varolanı Seç**' i seçin ve ardından **Yayımla**' yı seçin.

1. **App Service** bir **abonelik** seçin. Kaynak grubuna veya kaynak türüne göre sıralamak için **Görünüm** filtresini kullanın.

1. Profilinizi bulun ve **Tamam**' ı seçin. Sonra gerekli Azure App Service arayın ve **Tamam**' ı seçin.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png" alt-text="Visual Studio’da App Service iletişim kutusu":::

Başka bir seçenek de yeni bir profil oluşturmaktır:

1. Önceki yordamda olduğu gibi **Çözüm Gezgini** projeye sağ tıklayın ve **Yayımla**' yı seçin.
  
1. **Bir yayımlama hedefi** seçin kısmında **App Service**' yi seçin.

1. **Bir yayımlama hedefi** seçin bölümünde **Yeni oluştur** ' u seçin ve **Yayımla**' yı seçin.

1. **App Service**, Web uygulaması adınızı ve uygun aboneliği, kaynak grubunu ve barındırma planını girip **Oluştur**' u seçin.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png" alt-text="Visual Studio’da App Service’i Oluştur iletişim kutusu":::

Visual Studio, birkaç saniye içinde Web uygulamanızı yayımlar ve projenizi Azure 'da çalıştırdığınız yerde görebileceğiniz bir tarayıcı başlatır!

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, ASP.NET Core MVC web uygulaması oluşturmayı öğrendiniz. Uygulamanız, Azure Cosmos DB depolanan verilere erişebilir. Artık şu kaynaklarla devam edebilirsiniz:

* [Azure Cosmos DB'de bölümleme](./partitioning-overview.md)
* [SQL sorgularını kullanmaya başlama](./sql-query-getting-started.md)
* [Gerçek dünyadan bir örnek kullanarak Azure Cosmos DB'de verileri modelleme ve bölümleme](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: /aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: /aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
