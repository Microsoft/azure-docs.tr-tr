---
title: Azure mobil uygulamanız (Cordova) için çevrimdışı eşitlemeyi etkinleştirme | Microsoft Docs
description: Cordova uygulamanızdaki çevrimdışı verileri önbelleğe almak ve eşitlemek için App Service mobil uygulamayı nasıl kullanacağınızı öğrenin
documentationcenter: cordova
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: c5fcc98bf3da79d1978da6e67535a5a93a8fe321
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388843"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Cordova mobil uygulamanız için çevrimdışı eşitlemeyi etkinleştirme
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu öğreticide, Cordova için Azure Mobile Apps çevrimdışı eşitleme özelliği tanıtılmıştır. Çevrimdışı eşitleme son kullanıcıların bir mobil uygulamayla etkileşime geçmesini sağlar @ no__t-0görüntüleme, veri ekleme veya değiştirme, ağ bağlantısı olmadığında bile @ no__t-1. Değişiklikler yerel bir veritabanında depolanır.  Cihaz yeniden çevrimiçi olduktan sonra, bu değişiklikler uzak hizmetle eşitlenir.

Bu öğretici, [Apache Cordova hızlı başlangıç]öğreticiyi tamamladığınızda oluşturduğunuz Mobile Apps için Cordova hızlı başlangıç çözümünü temel alır. Bu öğreticide, Azure Mobile Apps çevrimdışı özellikleri eklemek için hızlı başlangıç çözümünü güncelleşolursunuz.  Ayrıca, uygulamadaki çevrimdışı özel kodu vurgularız.

Çevrimdışı eşitleme özelliği hakkında daha fazla bilgi edinmek için [Azure Mobile Apps’te Çevrimdışı Veri Eşitleme]konusuna bakın. API kullanımının ayrıntıları için [API belgelerine](https://azure.github.io/azure-mobile-apps-js-client)bakın.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Hızlı başlangıç çözümüne çevrimdışı eşitleme ekleme
Çevrimdışı eşitleme kodu uygulamaya eklenmelidir. Çevrimdışı eşitleme, projede Azure Mobile Apps eklentisi dahil edildiğinde otomatik olarak uygulamanıza eklenen Cordova-SQLite-Storage eklentisini gerektirir. Hızlı başlangıç projesi bu eklentilerin her ikisini de içerir.

1. Visual Studio 'nun Çözüm Gezgini, index. js ' yi açın ve aşağıdaki kodu değiştirin

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    Şu kodla:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Sonra, aşağıdaki kodu değiştirin:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    Şu kodla:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Yukarıdaki kod eklemeleri yerel depoyu başlatır ve Azure arka uçta kullanılan sütun değerleriyle eşleşen bir yerel tablo tanımlar. (Bu kodda tüm sütun değerlerini eklemeniz gerekmez.)  @No__t-0 alanı mobil arka uç tarafından korunur ve çakışma çözümü için kullanılır.

    **Getsynccontext**'i çağırarak eşitleme bağlamına bir başvuru alırsınız. Eşitleme bağlamı, `.push()` çağrıldığında bir istemci uygulamasının değiştirdiği tüm tablolardaki değişiklikleri izleyerek ve ileterek Tablo ilişkilerinin korunmasına yardımcı olur.

3. Uygulama URL 'sini mobil uygulama uygulamanızın URL 'niz olarak güncelleştirin.

4. Sonra şu kodu değiştirin:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    Şu kodla:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Yukarıdaki kod, eşitleme bağlamını başlatır ve ardından yerel tabloya bir başvuru almak için getSyncTable (getTable yerine) öğesini çağırır.

    Bu kod, tüm oluşturma, okuma, güncelleştirme ve silme (CRUD) tablo işlemleri için yerel veritabanını kullanır.

    Bu örnek, eşitleme çakışmaları üzerinde basit hata işleme gerçekleştirir. Gerçek bir uygulama, ağ koşulları, sunucu çakışmaları ve diğerleri gibi çeşitli hataları işleyebilir. Kod örnekleri için bkz. [Çevrimdışı eşitleme örneğinde].

5. Sonra, gerçek eşitlemeyi gerçekleştirmek için bu işlevi ekleyin.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    **SyncContext. push ()** öğesini çağırarak, mobil uygulama arka ucuna ne zaman değişiklik gönderileceğine karar verirsiniz. Örneğin, eşitleme düğmesine bağlı bir düğme olay işleyicisinde **Syncarka uç** çağırabilirsiniz.

## <a name="offline-sync-considerations"></a>Çevrimdışı eşitleme konuları

Örnekte, **syncContext** **Push** yöntemi yalnızca oturum açma için geri çağırma işlevindeki uygulama başlangıcında çağırılır.  Gerçek dünyada bir uygulamada, bu eşitleme işlevselliğinin el ile tetiklenmesi veya ağ durumunun ne zaman değiştiği de yapabilirsiniz.

Bir çekme, bağlam tarafından izlenen bekleyen yerel güncelleştirmeleri olan bir tabloya karşı yürütüldüğünde, bu çekme işlemi otomatik olarak bir gönderim tetikler. Bu örnekteki öğeleri yenilerken, eklerken ve tamamlarken açık bir **gönderme** çağrısını atlayabilirsiniz, çünkü gereksiz olabilir.

Belirtilen kodda, uzak TodoItem tablosundaki tüm kayıtlar sorgulanır, ancak bir sorgu kimliği ve sorguyu **Push**'e geçirerek kayıtları filtrelemek de mümkündür. Daha fazla bilgi için bkz. [Azure Mobile Apps’te Çevrimdışı Veri Eşitleme] *artımlı eşitleme* bölümü.

## <a name="optional-disable-authentication"></a>Seçim Kimlik doğrulamasını devre dışı bırak

Çevrimdışı eşitlemeyi test etmeden önce kimlik doğrulaması yapmak istemiyorsanız, oturum açma için geri çağırma işlevini açıklama olarak ayarlayın, ancak kodu geri çağırma işlevinin içinde yorumişaretini kaldırın.  Oturum açma satırları yorum yapıldıktan sonra kod aşağıdaki gibidir:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Artık uygulamayı çalıştırdığınızda uygulama Azure arka ucu ile eşitlenir.

## <a name="run-the-client-app"></a>İstemci uygulamasını çalıştırma
Çevrimdışı eşitleme özelliği etkinken, yerel mağaza veritabanını doldurmak için her platformda istemci uygulamasını en az bir kez çalıştırabilirsiniz. Daha sonra, çevrimdışı bir senaryonun benzetimini yapın ve uygulama çevrimdışıyken yerel depodaki verileri değiştirin.

## <a name="optional-test-the-sync-behavior"></a>Seçim Eşitleme davranışını test etme
Bu bölümde, arka ucunuz için geçersiz bir uygulama URL 'SI kullanarak bir çevrimdışı senaryonun benzetimini yapmak üzere istemci projesini değiştirirsiniz. Veri öğelerini eklediğinizde veya değiştirirken, bu değişiklikler yerel depoda tutulur, ancak bağlantı yeniden oluşturulana kadar arka uç veri deposuyla eşitlenmez.

1. Çözüm Gezgini, index. js proje dosyasını açın ve uygulama URL 'sini aşağıdaki kod gibi geçersiz bir URL 'ye işaret etmek üzere değiştirin:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. İndex. html ' de, CSP `<meta>` öğesini aynı geçersiz URL ile güncelleştirin.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. İstemci uygulamasını derleyin ve çalıştırın ve uygulama oturum açtıktan sonra arka uca eşitlemeye çalıştığında konsolda bir özel durum günlüğe kaydedilir. Eklediğiniz her yeni öğe, mobil arka uca gönderilene kadar yalnızca yerel depoda bulunur. İstemci uygulaması arka uca bağlı gibi davranır.

4. Uygulamayı kapatın ve oluşturduğunuz yeni öğelerin yerel depoda kalıcı olduğunu doğrulamak için yeniden başlatın.

5. Seçim Arka uç veritabanındaki verilerin değiştirilmediğini görmek üzere Azure SQL veritabanı tablonuzu görüntülemek için Visual Studio 'Yu kullanın.

    Visual Studio 'da **Sunucu Gezgini**açın. **Azure**->**SQL veritabanlarında**veritabanınıza gidin. Veritabanınıza sağ tıklayın ve **SQL Server Nesne Gezgini aç**' ı seçin. Artık SQL veritabanı tablonuza ve içindekilere gidebilirsiniz.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>Seçim Mobil arka ucunuza yeniden bağlanmayı test etme

Bu bölümde, uygulamanın çevrimiçi duruma geri geldiğini taklit eden mobil arka uca yeniden bağlayaöğreneceksiniz. Oturum açtığınızda, veriler mobil arka ucunuza eşitlenir.

1. İndex. js ' ye yeniden açın ve uygulama URL 'sini geri yükleyin.
2. Index. html dosyasını yeniden açın ve CSP `<meta>` öğesindeki uygulama URL 'sini düzeltin.
3. İstemci uygulamasını yeniden derleyin ve çalıştırın. Uygulama, oturum açtıktan sonra mobil uygulama arka ucuna eşitlemeye çalışır. Hata ayıklama konsolunda hiçbir özel durum günlüğe kaydedilmeyeceğini doğrulayın.
4. Seçim SQL Server Nesne Gezgini veya Fiddler gibi bir REST aracını kullanarak güncelleştirilmiş verileri görüntüleyin. Arka uç veritabanı ve yerel depo arasında verilerin eşitlendiğini fark edin.

    Verilerin veritabanı ile yerel depo arasında eşitlendiğini ve uygulamanızın bağlantısı kesildiğinde eklediğiniz öğeleri içerdiğini unutmayın.

## <a name="additional-resources"></a>Ek kaynaklar
* [Azure Mobile Apps’te Çevrimdışı Veri Eşitleme]
* [Apache Cordova için Visual Studio Araçları]

## <a name="next-steps"></a>Sonraki adımlar
* [Çevrimdışı eşitleme örneğinde] çakışma çözümü gibi gelişmiş çevrimdışı eşitleme özelliklerini gözden geçirin
* [API belgelerindeki](https://azure.github.io/azure-mobile-apps-js-client)ÇEVRIMDıŞı eşitleme API başvurusunu gözden geçirin.

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova hızlı başlangıç]: app-service-mobile-cordova-get-started.md
[Çevrimdışı eşitleme örneğinde]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Azure Mobile Apps’te Çevrimdışı Veri Eşitleme]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Apache Cordova için Visual Studio Araçları]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
