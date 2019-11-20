---
title: Mobile Apps ile Evrensel Windows Platformu (UWP) uygulamanız için çevrimdışı eşitlemeyi etkinleştirin | Microsoft Docs
description: Evrensel Windows Platformu (UWP) uygulamanızda çevrimdışı verileri önbelleğe almak ve eşitlemek için bir Azure mobil uygulamasını nasıl kullanacağınızı öğrenin.
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 1f15bd86debd98104a408c00650f5acfab94c526
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388584"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Windows uygulamanız için çevrimdışı eşitlemeyi etkinleştirme
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu öğreticide, Azure Mobil uygulama arka ucu kullanarak bir Evrensel Windows Platformu (UWP) uygulamasına çevrimdışı destek ekleme konusu gösterilmektedir. Çevrimdışı eşitleme, son kullanıcıların bir mobil uygulamayla etkileşime geçmesini sağlar; ağ bağlantısı olmadığında bile veri görüntüleme, ekleme veya değiştirme. Değişiklikler yerel bir veritabanında depolanır. Cihaz yeniden çevrimiçi olduktan sonra, bu değişiklikler uzak arka uca eşitlenir.

Bu öğreticide, UWP uygulama projesini öğreticiden güncelleolursunuz ve Azure Mobile Apps çevrimdışı özelliklerini desteklemek için [Windows uygulaması oluşturma] . İndirilen hızlı başlangıç sunucusu projesini kullanmazsanız, veri erişim uzantısı paketlerini projenize eklemeniz gerekir. Sunucu Uzantısı paketleri hakkında daha fazla bilgi için bkz. [Azure için .net arka uç sunucu SDK 'sı Mobile Apps çalışma](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Çevrimdışı eşitleme özelliği hakkında daha fazla bilgi edinmek için [Azure Mobile Apps’te Çevrimdışı Veri Eşitleme]konusuna bakın.

## <a name="requirements"></a>Gereksinimler  
Bu öğretici aşağıdaki önkoşulları gerektirir:

* Windows 8.1 veya sonraki sürümlerde Visual Studio 2013.
* [Windows uygulaması oluşturma]işleminin tamamlanması[bir Windows uygulaması oluşturun].
* [Azure Mobile Services SQLite Mağazası][sqlite store nuget]
* [Evrensel Windows Platformu geliştirme için SQLite](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Çevrimdışı özellikleri desteklemek için istemci uygulamasını güncelleştirme
Azure mobil uygulaması çevrimdışı özellikleri, çevrimdışı bir senaryonuz olduğunda yerel bir veritabanıyla etkileşime girebilmeniz için izin verir. Uygulamanızda bu özellikleri kullanmak için [syncContext][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable] arabirimini başlatırsınız. SQLite, cihazdaki yerel depo olarak kullanılır.

1. [Evrensel Windows platformu Için SQLite çalışma zamanını](https://sqlite.org/2016/sqlite-uwp-3120200.vsix)yükler.
2. Visual Studio 'da, [Windows uygulaması oluşturma] öğreticisinde tamamladığınız UWP uygulama projesi için NuGet Paket Yöneticisi ' ni açın.
    **Microsoft. Azure. Mobile. Client. SQLiteStore** NuGet paketini arayın ve yükler.
3. Çözüm Gezgini > **Başvuru Ekle...** > **Evrensel Windows** > **uzantıları** **' na** sağ tıklayın ve ardından hem Evrensel Windows platformu hem de  **C++ Visual 2015 için SQLite 'ı etkinleştirin Evrensel Windows Platformu uygulamaları için çalışma zamanı**.

    ![SQLite UWP başvurusu Ekle][1]
4. MainPage.xaml.cs dosyasını açın ve `#define OFFLINE_SYNC_ENABLED` tanımının açıklamasını kaldırın.
5. Visual Studio 'da, istemci uygulamasını yeniden derlemek ve çalıştırmak için **F5** tuşuna basın. Uygulama, çevrimdışı eşitleme 'yi etkinleştirmeden önceki ile aynı şekilde çalışacak. Ancak, yerel veritabanı artık çevrimdışı bir senaryoda kullanılabilecek verilerle doldurulmuştur.

## <a name="update-sync"></a>Arka uca bağlantıyı kesmek için uygulamayı güncelleştirme
Bu bölümde, çevrimdışı bir durumun benzetimini yapmak için mobil uygulama arka ucunuza olan bağlantıyı kesebilirsiniz. Veri öğeleri eklediğinizde, özel durum işleyiciniz uygulamanın çevrimdışı modda olduğunu söyler. Bu durumda, yerel depoya eklenen yeni öğeler, bağlı bir durumda gönderim bir sonraki çalıştırıldığında mobil uygulama arka ucuna eşitlenir.

1. Paylaşılan projedeki App.xaml.cs 'yi düzenleyin. **MobileServiceClient** başlatmasını not edin ve geçersiz bir mobil uygulama URL 'si kullanan aşağıdaki satırı ekleyin:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Ayrıca, cihazdaki WiFi ve hücresel ağları devre dışı bırakarak veya uçak modunu kullanarak çevrimdışı davranışı belirtebilirsiniz.
2. Uygulamayı derlemek ve çalıştırmak için **F5** tuşuna basın. Uygulama başlatıldığında eşitlemenin yenileme işlemi başarısız olduğuna dikkat edin.
3. Yeni öğeleri girin ve **Kaydet**' e her tıkladığınızda gönderme işlemi Için bir [Cancelledbynetworkerror] durumuyla başarısız olduğuna dikkat edin. Ancak, yeni Todo öğeleri, mobil uygulama arka ucuna itilene kadar yerel depoda bulunur.  Bir üretim uygulamasında, bu özel durumları bastırdığınızda, istemci uygulaması hala mobil uygulama arka ucuna bağlı gibi davranır.
4. Uygulamayı kapatın ve oluşturduğunuz yeni öğelerin yerel depoda kalıcı olduğunu doğrulamak için yeniden başlatın.
5. Seçim Visual Studio 'da **Sunucu Gezgini**açın. **Azure**->**SQL veritabanlarında**veritabanınıza gidin. Veritabanınıza sağ tıklayın ve **SQL Server Nesne Gezgini aç**' ı seçin. Artık SQL veritabanı tablonuza ve içindekilere gidebilirsiniz. Arka uç veritabanındaki verilerin değiştirilmediğini doğrulayın.
6. Seçim @No__t-0 biçiminde bir GET sorgusu kullanarak mobil arka ucunuzu sorgulamak için Fiddler veya Postman gibi bir REST aracı kullanın.

## <a name="update-online-app"></a>Mobil uygulama arka ucunuzu yeniden bağlamak için uygulamayı güncelleştirin
Bu bölümde, uygulamayı mobil uygulama arka ucuna yeniden bağlayaöğreneceksiniz. Bu değişiklikler, uygulamada bir ağ yeniden bağlantı benzetimini yapar.

Uygulamayı ilk kez çalıştırdığınızda `OnNavigatedTo` olay işleyicisi `InitLocalStoreAsync` ' i çağırır. İçindeki bu yöntem, yerel deponuzi arka uç veritabanıyla eşitlemek için `SyncAsync` çağırır. Uygulama, başlangıçta eşitlemeye çalışır.

1. App.xaml.cs ' ı paylaşılan projede açın ve önceki `MobileServiceClient` ' ın açıklaması ' nı kullanarak doğru mobil uygulama URL 'sini kullanın.
2. Uygulamayı yeniden derlemek ve çalıştırmak için **F5** tuşuna basın. Uygulama, `OnNavigatedTo` olay işleyicisi yürütüldüğünde gönderme ve çekme işlemlerini kullanarak yerel değişikliklerinizi Azure Mobil uygulama arka ucuna eşitler.
3. Seçim SQL Server Nesne Gezgini veya Fiddler gibi bir REST aracını kullanarak güncelleştirilmiş verileri görüntüleyin. Verilerin Azure Mobil uygulama arka uç veritabanı ve yerel depo arasında eşitlendiğini unutmayın.
4. Uygulamada, bunları yerel depoda tamamlamaya yönelik birkaç öğenin yanındaki onay kutusuna tıklayın.

   `UpdateCheckedTodoItem`, her tamamlanmış öğeyi mobil uygulama arka ucuna eşitlemek için `SyncAsync` ' i çağırır. `SyncAsync` hem itme hem de çekme çağırır. Bununla birlikte, **istemcinin üzerinde değişiklik yaptığı bir tabloya karşı bir çekme işlemi yürüttüğünüzde her zaman otomatik olarak bir gönderim yürütülür**. Bu davranış, yerel depodaki tüm tabloların ilişkilerle birlikte tutarlı kalmasını sağlar. Bu davranış beklenmedik bir gönderim oluşmasına neden olabilir.  Bu davranış hakkında daha fazla bilgi için bkz. [Azure Mobile Apps’te Çevrimdışı Veri Eşitleme].

## <a name="api-summary"></a>API Özeti
Mobil hizmetlerin çevrimdışı özelliklerini desteklemek için [Imobileservicesynctable] arabirimini kullandık ve yerel bir SQLite veritabanı Ile başlatılmış [MobileServiceClient. syncContext][synccontext] ' i kullanıyoruz. Çevrimdışıyken, Mobile Apps normal CRUD işlemleri, işlemler yerel depoya karşı gerçekleşirken, uygulama hala bağlı gibi çalışır. Yerel depoyu sunucusuyla eşleştirmek için aşağıdaki yöntemler kullanılır:

* **[PushAsync]** Bu yöntem [Imobileservicessynccontext]'in bir üyesi olduğundan, tüm tablolardaki değişiklikler arka uca gönderilir. Yalnızca yerel değişiklikleri olan kayıtlar sunucuya gönderilir.
* **[Pullasync]** Bir çekme işlemi, [Imobileservicesynctable]'ten başlatılır. Tabloda izlenen değişiklikler olduğunda, yerel depodaki tüm tabloların ilişkilerle birlikte tutarlı kalmasını sağlamak için örtük bir gönderim çalıştırılır. *PushOtherTables* parametresi, bağlamdaki diğer tabloların örtük bir gönderiminde gönderilip atılmadığını denetler. *Sorgu* parametresi, döndürülen verileri filtrelemek Için bir [ımobileservicetablequery @ No__t-2T >][IMobileServiceTableQuery] veya OData sorgu dizesi alır. *QueryId* parametresi, artımlı eşitleme tanımlamak için kullanılır. Daha fazla bilgi için bkz. [Azure Mobile Apps çevrimdışı veri eşitleme](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** Uygulamanız, eski verileri yerel depodan temizlemek için düzenli aralıklarla bu yöntemi çağırmalıdır. Henüz eşitlenmemiş tüm değişiklikleri temizlemeniz gerektiğinde *zorla* parametresini kullanın.

Bu kavramlar hakkında daha fazla bilgi için bkz. [Azure Mobile Apps 'de çevrimdışı veri eşitleme](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Daha fazla bilgi
Aşağıdaki konular Mobile Apps çevrimdışı eşitleme özelliği hakkında ek arka plan bilgileri sağlar:

* [Azure Mobile Apps’te Çevrimdışı Veri Eşitleme]
* [Azure Mobile Apps .NET SDK 'Sı nasıl yapılır][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Azure Mobile Apps’te Çevrimdışı Veri Eşitleme]: app-service-mobile-offline-data-sync.md
[Windows uygulaması oluşturma]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Imobileservicesynctable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[Imobileservicessynccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
