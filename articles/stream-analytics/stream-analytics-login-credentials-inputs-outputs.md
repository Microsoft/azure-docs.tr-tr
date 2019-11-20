---
title: Azure Stream Analytics işlerinde oturum açma kimlik bilgilerini döndürme
description: Bu makalede, giriş kimlik bilgilerini güncelleştirmek açıklanır ve çıkış işlerini Azure Stream Analytics'te havuzlarını.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: aad6f237a37ce4782e2c103a7a14e0a3951093de
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612356"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Giriş ve çıkışları bir Stream Analytics işi yönelik oturum açma kimlik bilgilerini döndürme

Bir giriş veya çıkış bir Stream Analytics işi, kimlik bilgilerini yeniden her işi yeni kimlik bilgileriyle güncelleştirmeniz gerekir. İş kimlik bilgilerini güncelleştirmeden önce durdurmalı, iş çalışırken kimlik bilgilerini değiştiremez. Stream Analytics iş durdurup arasında gecikme azaltmak için son çıktısını bir işten sürdürme destekler. Bu konuda, oturum açma kimlik bilgilerini döndürme ve yeni kimlik bilgileriyle iş yeniden başlatma işlemi açıklanmaktadır.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Yeni kimlik bilgilerini yeniden oluşturun ve işiniz yeni kimlik bilgileriyle güncelleştirin 

Bu bölümde, biz size yeniden kimlik bilgileri Blob Depolama, Event Hubs, SQL veritabanı ve tablo depolama için yol gösterir. 

### <a name="blob-storagetable-storage"></a>BLOB Depolama/tablo depolama
1. Azure portalında oturum açın > Stream Analytics işi için giriş/çıkış kullanılan depolama hesabını bulun.    
2. Ayarları bölümünden açın **erişim anahtarları**. İki varsayılan anahtarlar (key1, key2), arasındaki işinizi tarafından kullanılmayan bir çekme ve onu yeniden oluştur:  
   ![Depolama hesabı için anahtarları yeniden oluştur](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Yeni oluşturulan anahtarı kopyalayın.    
4. Stream Analytics işinizi Azure portalından Gözat > seçin **Durdur** ve durdurmak işin tamamlanmasını bekleyin.    
5. Blob/tablo bulun depolama giriş/çıkış kimlik bilgilerini güncelleştirmek istiyorsanız.    
6. Bulma **depolama hesabı anahtarı** alan ve yeni oluşturulan anahtarınızı yapıştırın > tıklatın **Kaydet**.    
7. Bağlantı testi değişikliklerinizi kaydettiğinizde, otomatik olarak başlatılır, bildirimleri sekmesinden görüntüleyebilirsiniz. İki bildirimler-bir güncelleştirme kaydetmek için karşılık gelen vardır ve bağlantıyı test etmek için diğer karşılık gelir:  
   ![Anahtar düzenleme sonra bildirimler](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Devam [son durdurulma süresi, iş başlangıç](#start-your-job-from-the-last-stopped-time) bölümü.

### <a name="event-hubs"></a>Event Hubs

1. Azure portalında oturum açın > Stream Analytics işi için giriş/çıkış kullanılan olay hub'ı bulun.    
2. Ayarları bölümünden açın **paylaşılan erişim ilkeleri** ve gerekli erişim ilkesini seçin. Arasında **birincil anahtar** ve **ikincil anahtar**, işinizi tarafından kullanılmayan bir tane seçin ve bunu yeniden oluştur:  
   ![Event Hubs için anahtarları yeniden oluştur](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Yeni oluşturulan anahtarı kopyalayın.    
4. Stream Analytics işinizi Azure portalından Gözat > seçin **Durdur** ve durdurmak işin tamamlanmasını bekleyin.    
5. Hub'ları giriş/çıkış kimlik bilgilerini güncelleştirmek istediğiniz olayı bulun.    
6. Bulma **olay hub'ı ilke anahtarı** alan ve yeni oluşturulan anahtarınızı yapıştırın > tıklatın **Kaydet**.    
7. Bağlantı testi yaptığınız değişiklikleri kaydettiğinizde, otomatik olarak başlatılacak başarıyla geçti emin olun.    
8. Devam [son durdurulma süresi, iş başlangıç](#start-your-job-from-the-last-stopped-time) bölümü.

### <a name="sql-database"></a>SQL Veritabanı

Mevcut bir kullanıcının oturum açma kimlik bilgilerini güncelleştirmek için SQL veritabanına bağlanmak gerekir. Azure portalı veya SQL Server Management Studio gibi bir istemci araç kullanarak kimlik bilgilerini güncelleştirebilirsiniz. Bu bölümde, Azure portalını kullanarak kimlik bilgilerini güncelleştirme işlemini gösterir.

1. Azure portalında oturum açın > için Stream Analytics işi çıktı olarak kullanılan SQL veritabanı göz atın.    
2. Gelen **Veri Gezgini**, oturum açma/bağlanma veritabanınıza > Yetkilendirme türü olarak seçin **SQL server kimlik doğrulaması** > yazın, **oturum açma** ve  **Parola** ayrıntıları > seçin **Tamam**.  
   ![SQL veritabanı için kimlik bilgilerini yeniden oluşturun](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. Sorgu sekmesinde aşağıdaki sorguyu çalıştırarak, kullanıcının parolasını değiştirmek (değiştirdiğinizden emin olun `<user_name>` adınızı içeren ve `<new_password>` yeni parolanızla):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Yeni parolayı not edin.    
5. Stream Analytics işinizi Azure portalından Gözat > seçin **Durdur** ve durdurmak işin tamamlanmasını bekleyin.    
6. Kimlik bilgilerini döndürme istiyorsanız SQL veritabanı çıkışı bulun. Parolasını güncelleştirin ve değişiklikleri kaydedin.    
7. Bağlantı testi yaptığınız değişiklikleri kaydettiğinizde, otomatik olarak başlatılacak başarıyla geçti emin olun.    
8. Devam [son durdurulma süresi, iş başlangıç](#start-your-job-from-the-last-stopped-time) bölümü.

### <a name="power-bi"></a>Power BI
1. Azure portalında oturum açın > Stream Analytics işinizi bulun > seçin **Durdur** ve durdurmak işin tamamlanmasını bekleyin.    
2. Kimlik bilgilerini yenilemek istediğiniz Power BI çıkışına bulun > tıklatın **yetkilendirmeyi yenilemek** (başarı iletisini görmeniz gerekir) > **Kaydet** değişiklikleri.    
3. Bağlantı testi değişikliklerinizi kaydettiğinizde, otomatik olarak başlatılır, emin olun, başarıyla geçti.    
4. Devam [son durdurulma süresi, iş başlangıç](#start-your-job-from-the-last-stopped-time) bölümü.

## <a name="start-your-job-from-the-last-stopped-time"></a>Son durdurulma süresi, iş başlangıç

1. İşin gidin **genel bakış** bölmesinde > seçin **Başlat** işi başlatmak için.    
2. Seçin **son durdurulduğunda** > tıklatın **Başlat**. Daha önce işlemi çalıştırıldı ve bazı çıkış vardı "son durdurulduğunda" seçeneği yalnızca göründüğüne dikkat edin oluşturulur. İş son çıktı değerinin saat başlatılır.
   ![Stream Analytics işini başlatın](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
