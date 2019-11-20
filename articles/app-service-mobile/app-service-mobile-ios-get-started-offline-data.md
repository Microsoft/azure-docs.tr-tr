---
title: İOS mobil uygulamalarıyla çevrimdışı eşitlemeyi etkinleştirme | Microsoft Docs
description: İOS uygulamalarındaki çevrimdışı verileri önbelleğe almak ve eşitlemek için Azure App Service Mobile Apps 'i nasıl kullanacağınızı öğrenin.
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f29a28f9a80b64ef0a6890fa8fc7ecd0ca205e66
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388764"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>İOS mobil uygulamalarıyla çevrimdışı eşitlemeyi etkinleştirme
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu öğretici, iOS için Azure App Service Mobile Apps özelliğiyle çevrimdışı eşitlemeyi ele alır. Çevrimdışı eşitleme ile son kullanıcılar, ağ bağlantısı olmasa bile verileri görüntülemek, eklemek veya değiştirmek için bir mobil uygulamayla etkileşim kurabilir. Değişiklikler yerel bir veritabanında depolanır. Cihaz yeniden çevrimiçi olduktan sonra değişiklikler uzak arka uca eşitlenir.

Mobile Apps ilk deneyiminiz varsa, önce [İOS uygulaması oluşturma]öğreticisini tamamlamalısınız. İndirilen hızlı başlangıç sunucu projesini kullanmazsanız, veri erişimi uzantısı paketlerini projenize eklemeniz gerekir. Sunucu Uzantısı paketleri hakkında daha fazla bilgi için bkz. [Azure için .net arka uç sunucu SDK 'sı Mobile Apps çalışma](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Çevrimdışı eşitleme özelliği hakkında daha fazla bilgi edinmek için [Mobile Apps çevrimdışı veri eşitleme]bölümüne bakın.

## <a name="review-sync"></a>İstemci eşitleme kodunu gözden geçirin
[IOS uygulaması oluşturma] öğreticisi için indirdiğiniz istemci projesi, yerel bir temel veri tabanlı veritabanı kullanarak çevrimdışı eşitlemeyi destekleyen kodu zaten içeriyor. Bu bölümde, öğretici kodunda zaten nelerin dahil olduğu özetlenmektedir. Özelliğe kavramsal bir genel bakış için bkz. [Mobile Apps çevrimdışı veri eşitleme].

Mobile Apps çevrimdışı veri eşitleme özelliğini kullanarak, son kullanıcılar, ağa erişilemediği zaman bile yerel bir veritabanıyla etkileşime geçebilir. Uygulamanızda bu özellikleri kullanmak için, `MSClient` ' ın eşitleme bağlamını başlatın ve bir yerel depoya başvurun. Daha sonra, **Mssynctable** arabirimi aracılığıyla tablonuza başvurabilirsiniz.

**Qstodoservice. m** (hedef-C) veya **ToDoTableViewController. Swift** (Swift) Içinde, üye **Synctable** türünün **mssynctable**olduğunu görürsünüz. Çevrimdışı eşitleme, bu eşitleme tablosu arabirimini **Mstable**yerine kullanır. Eşitleme tablosu kullanıldığında, tüm işlemler yerel depoya gider ve yalnızca açık gönderim ve çekme işlemleriyle birlikte uzak arka uca eşitlenir.

 Bir eşitleme tablosuna başvuru almak için `MSClient` ' de **Synctablewithname** metodunu kullanın. Çevrimdışı eşitleme işlevselliğini kaldırmak için, bunun yerine **Tablewithname** kullanın.

Herhangi bir tablo işlemi gerçekleştirilmeden önce, yerel deponun başlatılmış olması gerekir. İlgili kod aşağıda verilmiştir:

* **Amaç-C**. **Qstodoservice. init** yönteminde:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**. **ToDoTableViewController. Viewdıdload** yönteminde:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Bu yöntem, Mobile Apps SDK 'nın sağladığı `MSCoreDataStore` arabirimini kullanarak bir yerel depo oluşturur. Alternatif olarak, `MSSyncContextDataSource` protokolünü uygulayarak farklı bir yerel depo sağlayabilirsiniz. Ayrıca, bir çakışma işleyicisini belirtmek için **Mssynccontext** öğesinin ilk parametresi kullanılır. @No__t-0 geçirdiğimiz için, herhangi bir çakışmada başarısız olan varsayılan çakışma işleyicisini alırız.

Şimdi de gerçek eşitleme işlemini gerçekleştirelim ve uzak arka uçtaki verileri al:

* **Amaç-C**. `syncData` önce yeni değişiklikleri iter ve sonra uzak arka uca veri almak için **PullData** çağırır. Sırasıyla, **PullData** yöntemi bir sorguyla eşleşen yeni verileri alır:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Swift**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc. via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

Hedef C sürümünde, `syncData` ' da, önce eşitleme bağlamında **Pushwithcompletion** öğesini çağırıyoruz. Bu yöntem, değişiklikleri tüm tablolarda ilettiğinden `MSSyncContext` ' ın (eşitleme tablosunun kendisi değil) bir üyesidir. Yalnızca yerel olarak (CUD işlemleri aracılığıyla) değiştirilen kayıtlar sunucuya gönderilir. Ardından, uzak verileri almak ve yerel veritabanında depolamak için **Mssynctable. pullWithQuery** öğesini çağıran yardımcı **PullData** çağrılır.

Swift sürümünde, gönderim işlemi kesinlikle gerekli olmadığı için **Pushwithcompletion**çağrısı yoktur. Gönderme işlemi yapan tablo için eşitleme bağlamında bekleyen değişiklikler varsa, çekme her zaman önce bir anında iletme yayınlar. Ancak, birden fazla eşitleme tablonuz varsa, her şeyin ilgili tablolarda tutarlı olduğundan emin olmak için doğrudan gönderimi çağırmak en iyisidir.

Hem amaç-C hem de Swift sürümlerinde, almak istediğiniz kayıtları filtrelemek için bir sorgu belirtmek üzere **Pullwithquery** yöntemini kullanabilirsiniz. Bu örnekte sorgu, uzak `TodoItem` tablosundaki tüm kayıtları alır.

**Pullwithquery** 'nin ikinci parametresi, *artımlı eşitleme*IÇIN kullanılan bir sorgu kimliğidir. Artımlı eşitleme, kaydın `UpdatedAt` zaman damgası kullanılarak (yerel depoda `updatedAt` olarak adlandırılır) yalnızca son eşitlemeden bu yana değiştirilen kayıtları alır.) Sorgu KIMLIĞI, uygulamanızdaki her mantıksal sorgu için benzersiz olan açıklayıcı bir dize olmalıdır. Artımlı eşitlemeyi devre dışı bırakmak için `nil` ' ı sorgu KIMLIĞI olarak geçirin. Her çekme işlemindeki tüm kayıtları aldığı için bu yaklaşım potansiyel olarak verimsiz olabilir.

Amaç-C uygulaması, verileri değiştirirken veya eklediğinizde, bir Kullanıcı yenileme hareketini yaptığında ve başlatılırken eşitlenir.

Kullanıcı yenileme hareketini yaptığında ve başlatıldığında Swift uygulaması eşitlenir.

Uygulama, veriler her başlatıldığında (hedef-C) veya uygulama her başladığında (amaç-C ve Swift) eşitlendikten sonra, uygulama kullanıcının çevrimiçi olduğunu varsayar. Daha sonraki bir bölümde, kullanıcıların çevrimdışıyken bile düzenleyebilmeleri için uygulamayı güncelleşceksiniz.

## <a name="review-core-data"></a>Çekirdek veri modelini gözden geçirme
Çekirdek veri çevrimdışı deposunu kullandığınızda veri modelinizdeki belirli tabloları ve alanları tanımlamanız gerekir. Örnek uygulama zaten doğru biçime sahip bir veri modeli içeriyor. Bu bölümde, nasıl kullanıldığını göstermek için bu tabloları yürüyoruz.

**Qsdatamodel. xcdatamodeld**'yi açın. Dört tablo tanımlanmıştır--SDK tarafından kullanılan diğeri ve Yapılacaklar öğeleri için kullanılan bir tane.
  * MS_TableOperations: sunucusuyla eşitlenmesi gereken öğeleri Izler.
  * MS_TableOperationErrors: çevrimdışı eşitleme sırasında meydana gelen tüm hataları Izler.
  * MS_TableConfig: tüm çekme işlemleri için son eşitleme işleminin son güncelleştirilme saatini Izler.
  * TodoItem: yapılacaklar öğelerini depolar. **Createdat**, **updatedat**ve **Version** sistem sütunları isteğe bağlı sistem özellikleridir.

> [!NOTE]
> Mobile Apps SDK, " **``** " ile başlayan sütun adlarını ayırır. Bu öneki sistem sütunları dışında bir şeyle kullanmayın. Aksi takdirde, uzak arka ucu kullandığınızda sütun adlarınız değiştirilir.
>
>

Çevrimdışı eşitleme özelliğini kullandığınızda, üç sistem tablosunu ve veri tablosunu tanımlayın.

### <a name="system-tables"></a>Sistem tabloları

**MS_TableOperations**  

![MS_TableOperations tablo öznitelikleri][defining-core-data-tableoperations-entity]

| Öznitelik | Tür |
| --- | --- |
| id | Tamsayı 64 |
| ID | Dize |
| properties | İkili veriler |
| Tablosundan | Dize |
| Tablotürü | Tamsayı 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors tablo öznitelikleri][defining-core-data-tableoperationerrors-entity]

| Öznitelik | Tür |
| --- | --- |
| id |Dize |
| operationId |Tamsayı 64 |
| properties |İkili veriler |
| Tablotürü |Tamsayı 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Öznitelik | Tür |
| --- | --- |
| id |Dize |
| anahtar |Dize |
| Anahtar |Tamsayı 64 |
| Tablosundan |Dize |
| değer |Dize |

### <a name="data-table"></a>Veri tablosu

**TodoItem**

| Öznitelik | Tür | Not |
| --- | --- | --- |
| id | Dize, gerekli olarak işaretlendi |Uzak depodaki birincil anahtar |
| Tamam | Boole | Yapılacaklar öğesi alanı |
| metin |Dize |Yapılacaklar öğesi alanı |
| CreatedAt | Tarih | seçim **Createdat** sistem özelliğiyle eşlenir |
| updatedAt | Tarih | seçim **Updatedat** sistem özelliğiyle eşlenir |
| version | Dize | seçim Çakışmaları saptamak için kullanılır, sürüme eşlenir |

## <a name="setup-sync"></a>Uygulamanın eşitleme davranışını değiştirme
Bu bölümde, uygulamayı uygulama başlatma veya öğe ekleme ve güncelleştirme sırasında eşitlenmemesi için değiştirirsiniz. Yalnızca yenileme hareketi düğmesi gerçekleştirildiğinde eşitlenir.

**Amaç-C**:

1. **QSTodoListViewController. d**içinde, metodun sonundaki `[self refresh]` ' ye çağrıyı kaldırmak Için **Viewdıdload** yöntemini değiştirin. Artık veriler, uygulama başlatma sırasında sunucusuyla eşitlenmez. Bunun yerine, yerel deponun içeriğiyle eşitlenir.
2. **Qstodoservice. d**' de, öğe eklendikten sonra eşitlenmemesi için `addItem` tanımını değiştirin. @No__t-0 bloğunu kaldırın ve aşağıdaki kodla değiştirin:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. @No__t-0 ' ın tanımını daha önce belirtildiği gibi değiştirin. @No__t-0 bloğunu kaldırın ve aşağıdaki kodla değiştirin:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

@No__t-0 ' da, **ToDoTableViewController. Swift**içinde, uygulama başlatma sırasında eşitlemeyi durdurmak için burada gösterilen iki satırı not edin. Bu yazma sırasında, bir öğe eklendiğinde veya tamamlandığında Swift ToDo uygulaması hizmeti güncelleştirmez. Hizmeti yalnızca uygulama başlatma sırasında güncelleştirir.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Uygulamayı test etme
Bu bölümde, bir çevrimdışı senaryonun benzetimini yapmak için geçersiz bir URL 'ye bağlanırsınız. Veri öğeleri eklediğinizde bunlar yerel çekirdek veri deposunda tutulur, ancak mobil uygulama arka ucu ile eşitlenmez.

1. **Qstodoservice. d** içindeki MOBIL uygulama URL 'sini GEÇERSIZ bir URL 'ye değiştirin ve uygulamayı yeniden çalıştırın:

   **Amaç-C**. QSTodoService. d ' de:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**. ToDoTableViewController. Swift içinde:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Bazı Yapılacaklar öğeleri ekleyin. Simülatör 'dan çıkın (veya uygulamayı zorla kapatın) ve ardından yeniden başlatın. Değişikliklerinizin devam ettiğini doğrulayın.

3. Uzak **TodoItem** tablosunun içeriğini görüntüleyin:
   * Node. js arka ucu için [Azure Portal](https://portal.azure.com/) gidin ve mobil uygulama arka uçta, **kolay tablolar** > **TodoItem**' a tıklayın.  
   * Bir .NET arka ucu için SQL Server Management Studio gibi bir SQL aracını veya Fiddler veya Postman gibi bir REST istemcisini kullanın.  

4. Yeni öğelerin sunucu ile *eşitlenmediğini doğrulayın* .

5. URL 'YI **Qstodoservice. d**içinde doğru bir şekilde değiştirin ve uygulamayı yeniden çalıştırın.

6. Öğe listesini çekerek yenileme hareketini gerçekleştirin.  
İlerleme değiştiricisi görüntülenir.

7. **TodoItem** verilerini yeniden görüntüleyin. Yeni ve değiştirilmiş yapılacak Yapılacaklar öğeleri artık görüntülenmelidir.

## <a name="summary"></a>Özet
Çevrimdışı eşitleme özelliğini desteklemek için `MSSyncTable` arabirimini kullandık ve yerel bir depolarla `MSClient.syncContext` ' i başlatıyoruz. Bu durumda, yerel depo bir temel veri tabanlı veritabanıdır.

Çekirdek veri yerel deposu kullandığınızda, [doğru sistem özelliklerine](#review-core-data)sahip birkaç tablo tanımlamanız gerekir.

Mobil uygulamalar için normal oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri, uygulama hala bağlı gibi çalışır, ancak tüm işlemler yerel depoya karşı gerçekleştirilir.

Yerel depoyu sunucuyla eşitliyoruz, **Mssynctable. pullWithQuery** yöntemini kullandık.

## <a name="additional-resources"></a>Ek kaynaklar
* [Mobile Apps çevrimdışı veri eşitleme]
* [Bulut kapağı: Azure Mobile Services çevrimdışı eşitleme] \(video Mobile Services hakkında, ancak Mobile Apps çevrimdışı eşitleme benzer bir şekilde çalışıyor. \)

<!-- URLs. -->


[İOS uygulaması oluşturma]: app-service-mobile-ios-get-started.md
[Mobile Apps çevrimdışı veri eşitleme]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Bulut kapağı: Azure Mobile Services çevrimdışı eşitleme]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
