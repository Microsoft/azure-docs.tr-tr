---
title: Azure mobil uygulamanız için çevrimdışı eşitlemeyi etkinleştirin (Xamarin. Forms) | Microsoft Docs
description: App Service mobil uygulamayı kullanarak Xamarin. Forms uygulamanızda çevrimdışı verileri önbelleğe alma ve eşitleme hakkında bilgi edinin
documentationcenter: xamarin
author: elamalani
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 65bd619a95b102c4ad4be57f1661a89fbae43e22
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388421"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Xamarin.Forms mobil uygulamanız için çevrimdışı eşitlemeyi etkinleştirme
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu öğreticide, Xamarin. Forms için Azure Mobile Apps çevrimdışı eşitleme özelliği tanıtılmıştır. Çevrimdışı eşitleme son kullanıcıların bir mobil uygulamayla etkileşime geçmesini sağlar--ağ bağlantısı olmasa bile verileri görüntüleme, ekleme veya değiştirme. Değişiklikler yerel bir veritabanında depolanır. Cihaz yeniden çevrimiçi olduktan sonra, bu değişiklikler uzak hizmetle eşitlenir.

Bu öğretici, [Xamarin iOS uygulaması oluşturma] öğreticisini tamamladığınızda oluşturduğunuz Mobile Apps için Xamarin. Forms hızlı başlangıç çözümünü temel alır. Xamarin. Forms için hızlı başlangıç çözümü, yalnızca etkinleştirilmesi gereken çevrimdışı eşitlemeyi desteklemeye yönelik kodu içerir. Bu öğreticide, Azure Mobile Apps çevrimdışı özelliklerini açmak için hızlı başlangıç çözümünü güncelleolursunuz. Ayrıca, uygulamadaki çevrimdışı özel kodu vurgularız. İndirilen hızlı başlangıç çözümünü kullanmazsanız, veri erişim uzantısı paketlerini projenize eklemeniz gerekir. Sunucu Uzantısı paketleri hakkında daha fazla bilgi için bkz. [Azure için .net arka uç sunucu SDK 'sı Mobile Apps çalışma][1].

Çevrimdışı eşitleme özelliği hakkında daha fazla bilgi edinmek için [Azure Mobile Apps 'de çevrimdışı veri eşitleme][2]konusuna bakın.

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Hızlı başlangıç çözümünde çevrimdışı eşitleme işlevini etkinleştirme
Çevrimdışı eşitleme kodu, Önişlemci yönergeleri kullanılarak C# projeye dahil edilir. **Çevrimdışı @ no__t-1SYNC @ no__t-2ENABLED** sembolü tanımlandığında, bu kod yolları yapıya dahil edilir. Windows uygulamaları için, SQLite platformunu da yüklemelisiniz.

1. Visual Studio 'da çözüme sağ tıklayın > çözüm **Için NuGet Paketlerini Yönet...** ' i sağ tıklatın ve Çözümdeki tüm projeler için **Microsoft. Azure. Mobile. Client. SQLiteStore** NuGet paketini arayın ve yüklemeyi yapın.
2. Çözüm Gezgini, taşınabilir sınıf kitaplığı projesi olan ad içinde **Taşınabilir** olan TodoItemManager.cs dosyasını projeden açın ve ardından aşağıdaki Önişlemci yönergesinin açıklamasını kaldırın:

        #define OFFLINE_SYNC_ENABLED
3. Seçim Windows cihazlarını desteklemek için aşağıdaki SQLite çalışma zamanı paketlerinden birini yüklemelisiniz:

   * **Windows 8.1 çalışma zamanı:** [Windows 8.1 Için SQLite][3]'ı yükler.
   * **Windows Phone 8,1:** [Windows Phone 8,1 Için SQLite][4]'ı yükler.
   * **Evrensel Windows platformu** [Evrensel Windows Universal Için SQLite][5]'ı yükler.

     Hızlı başlangıç, bir Evrensel Windows projesi içermediğinden, Xamarin Forms ile Evrensel Windows platformu desteklenir.
4. Seçim Her Windows uygulama projesinde, **başvurular** > **Başvuru Ekle...** öğesine sağ tıklayın, **Windows** klasör > **uzantıları**' nı genişletin.
    Windows SDK **Için Visual C++ 2013 çalışma zamanı** ile birlikte **Windows SDK için uygun SQLite** 'ı etkinleştirin.
    SQLite SDK adları her bir Windows platformunda biraz farklılık gösterir.

## <a name="review-the-client-sync-code"></a>İstemci eşitleme kodunu gözden geçirin
İşte `#if OFFLINE_SYNC_ENABLED` yönergelerinin içindeki öğretici kodunda zaten nelerin dahil olduğuna ilişkin kısa bir genel bakış. Çevrimdışı Eşitleme işlevselliği, taşınabilir sınıf Kitaplığı projesindeki TodoItemManager.cs proje dosyasında bulunur. Özelliğe kavramsal bir genel bakış için bkz. [Azure Mobile Apps 'de çevrimdışı veri eşitleme][2].

* Herhangi bir tablo işlemi gerçekleştirilmeden önce, yerel deponun başlatılmış olması gerekir. Yerel depo veritabanı, aşağıdaki kod kullanılarak **TodoItemManager** sınıf oluşturucusunda başlatılır:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Bu kod, **Mobileservicesqlitestore** sınıfını kullanarak yeni bir yerel SQLite veritabanı oluşturur.

    **Definetable** yöntemi, yerel depoda, belirtilen türdeki alanlarla eşleşen bir tablo oluşturur.  Türün, uzak veritabanındaki tüm sütunları içermesi gerekmez. Sütunların bir alt kümesini depolamak mümkündür.
* **TodoItemManager** içindeki **TodoTable** alanı **ımobileservicetable**yerine bir **ımobileservicesynctable** türüdür. Bu sınıf, tüm oluşturma, okuma, güncelleştirme ve silme (CRUD) tablo işlemleri için yerel veritabanını kullanır. **Imobileservicesynccontext**üzerinde **PushAsync** çağırarak, bu değişikliklerin mobil uygulama arka ucuna ne zaman itileceğine karar verirsiniz. Eşitleme bağlamı, **PushAsync** çağrıldığında bir istemci uygulamasının değiştirdiği tüm tablolardaki değişiklikleri izleyerek ve ileterek Tablo ilişkilerinin korunmasına yardımcı olur.

    Aşağıdaki **Syncasync** yöntemi, mobil uygulama arka ucuna eşitlenmek üzere çağırılır:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Bu örnek, varsayılan eşitleme işleyicisiyle basit hata işlemeyi kullanır. Gerçek bir uygulama, ağ koşulları ve sunucu çakışmaları gibi çeşitli hataları özel bir **ımobileservicesynchandler** uygulaması kullanarak işleyebilir.

## <a name="offline-sync-considerations"></a>Çevrimdışı eşitleme konuları
Örnekte, **Syncasync** yöntemi yalnızca başlangıç sırasında ve bir eşitleme istendiğinde çağırılır.  Android veya iOS uygulamasında bir eşitleme başlatmak için, öğe listesini aşağı çekin; Windows için **Eşitle** düğmesini kullanın. Gerçek dünyada bir uygulamada, ağ durumu değiştiğinde eşitleme tetikleyicisini de yapabilirsiniz.

Bir çekme, bağlam tarafından izlenen bekleyen yerel güncelleştirmeleri olan bir tabloya karşı yürütüldüğünde, bu çekme işlemi önceki bağlam göndereni otomatik olarak tetikler. Bu örnekteki öğeleri yenilerken, eklerken ve tamamlarken açık **PushAsync** çağrısını atlayabilirsiniz.

Belirtilen kodda, uzak TodoItem tablosundaki tüm kayıtlar sorgulanır, ancak bir sorgu kimliği ve sorgu **PushAsync**'e geçirerek kayıtları filtrelemek de mümkündür. Daha fazla bilgi için bkz. [Azure Mobile Apps çevrimdışı veri eşitlemede][2] *artımlı eşitleme* bölümü.

## <a name="run-the-client-app"></a>İstemci uygulamasını çalıştırma
Çevrimdışı eşitleme özelliği etkinken, yerel mağaza veritabanını doldurmak için her platformda istemci uygulamasını en az bir kez çalıştırın. Daha sonra, çevrimdışı bir senaryonun benzetimini yapın ve uygulama çevrimdışıyken yerel depodaki verileri değiştirin.

## <a name="update-the-sync-behavior-of-the-client-app"></a>İstemci uygulamasının eşitleme davranışını güncelleştirme
Bu bölümde, arka ucunuz için geçersiz bir uygulama URL 'SI kullanarak istemci projesini bir çevrimdışı senaryonun benzetimini yapmak üzere değiştirin. Alternatif olarak, cihazınızı "uçak moduna taşıyarak" ağ bağlantılarını devre dışı bırakabilirsiniz.  Veri öğelerini eklediğinizde veya değiştirirken, bu değişiklikler yerel depoda tutulur, ancak bağlantı yeniden oluşturulana kadar arka uç veri deposuyla eşitlenmez.

1. Çözüm Gezgini, **Taşınabilir** projeden Constants.cs proje dosyasını açın ve `ApplicationURL` değerini GEÇERSIZ bir URL 'ye işaret etmek için değiştirin:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. **Taşınabilir** projeden TodoItemManager.cs dosyasını açın ve sonra dene... ' ye temel **özel durum** sınıfı için bir **catch** ekleyin **.**  **Syncasync**içindeki catch bloğu. Bu **catch** bloğu özel durum iletisini konsola aşağıdaki şekilde Yazar:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. İstemci uygulamasını derleyin ve çalıştırın.  Bazı yeni öğeler ekleyin. Her arka uç ile eşitleme denemesi için konsolda bir özel durum günlüğe kaydedildiğini unutmayın. Bu yeni öğeler yalnızca yerel depoda, mobil arka uca itilene kadar bulunur. İstemci uygulaması, tüm oluşturma, okuma, güncelleştirme, silme (CRUD) işlemlerini desteklemek için arka uca bağlı gibi davranır.
4. Uygulamayı kapatın ve oluşturduğunuz yeni öğelerin yerel depoda kalıcı olduğunu doğrulamak için yeniden başlatın.
5. Seçim Arka uç veritabanındaki verilerin değiştirilmediğini görmek üzere Azure SQL veritabanı tablonuzu görüntülemek için Visual Studio 'Yu kullanın.

    Visual Studio 'da **Sunucu Gezgini**açın. **Azure**->**SQL veritabanlarında**veritabanınıza gidin. Veritabanınıza sağ tıklayın ve **SQL Server Nesne Gezgini aç**' ı seçin. Artık SQL veritabanı tablonuza ve içindekilere gidebilirsiniz.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Mobil arka ucunuzu yeniden bağlamak için istemci uygulamasını güncelleştirme
Bu bölümde, uygulamanın çevrimiçi duruma geri geldiğini taklit eden mobil arka uca yeniden bağlayın. Yenileme hareketini gerçekleştirdiğinizde veriler, mobil arka ucunuza eşitlenir.

1. Constants.cs yeniden açın. Doğru URL 'yi göstermek için `applicationURL` ' ı düzeltin.
2. İstemci uygulamasını yeniden derleyin ve çalıştırın. Uygulama, başlatıldıktan sonra mobil uygulama arka ucu ile eşitlemeye çalışır. Hata ayıklama konsolunda hiçbir özel durum günlüğe kaydedilmeyeceğini doğrulayın.
3. Seçim SQL Server Nesne Gezgini veya Fiddler veya [Postman][6]gıbı bir rest aracını kullanarak güncelleştirilmiş verileri görüntüleyin. Arka uç veritabanı ve yerel depo arasında verilerin eşitlendiğini fark edin.

    Verilerin veritabanı ile yerel depo arasında eşitlendiğini ve uygulamanızın bağlantısı kesildiğinde eklediğiniz öğeleri içerdiğini unutmayın.

## <a name="additional-resources"></a>Ek Kaynaklar
* [Azure Mobile Apps’te Çevrimdışı Veri Eşitleme][2]
* [Azure Mobile Apps .NET SDK 'Sı nasıl yapılır][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
