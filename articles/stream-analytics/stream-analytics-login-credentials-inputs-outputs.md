---
title: Azure Stream Analytics işlerinde oturum açma kimlik bilgilerini döndürün
description: Bu makalede, Azure Stream Analytics işlerinde giriş ve çıkış havuzları kimlik bilgilerinin nasıl güncelleştirileceğini açıklanmaktadır.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: fd6c072f9783e8ff5d4d5e465b513c2e530bfd63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015241"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Stream Analytics Işinin girdileri ve çıkışları için oturum açma kimlik bilgilerini döndürün

Stream Analytics işinin bir girişi veya çıktısı için kimlik bilgilerini yeniden oluşturduğunuzda, işi yeni kimlik bilgileriyle güncelleştirmeniz gerekir. Kimlik bilgilerini güncelleştirmeden önce işi durdurmanız gerekir, iş çalışırken kimlik bilgilerini değiştiremezsiniz. İşi durdurma ve yeniden başlatma arasındaki gecikme sayısını azaltmak için Stream Analytics, son çıktısından bir işin sürdürülmesini destekler. Bu konuda, oturum açma kimlik bilgilerini döndürme ve yeni kimlik bilgileriyle işi yeniden başlatma işlemleri açıklanmaktadır.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Yeni kimlik bilgilerini yeniden oluşturun ve yeni kimlik bilgileriyle işinizi güncelleştirin 

Bu bölümde, BLOB depolama, Event Hubs, SQL veritabanı ve tablo depolama için kimlik bilgilerini yeniden oluşturma konusunda size yol göstereceğiz. 

### <a name="blob-storagetable-storage"></a>BLOB depolama/tablo depolama
1. Azure portal oturum açın > Stream Analytics işi için giriş/çıkış olarak kullandığınız depolama hesabına gidin.    
2. Ayarlar bölümünde **erişim anahtarları**' nı açın. İki varsayılan anahtar (KEY1, key2) arasında, işiniz tarafından kullanılmayan birini seçin ve yeniden oluşturun:  
   ![Depolama hesabı için anahtarları yeniden oluştur](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Yeni oluşturulan anahtarı kopyalayın.    
4. Azure portal, **Durdur** ' u seçin ve işin durdurulmasını bekleyin > Stream Analytics işinize gidin.    
5. Kimlik bilgilerini güncelleştirmek istediğiniz blob/tablo depolama girişini/çıkışını bulun.    
6. **Depolama hesabı anahtarı** alanını bulun ve yeni oluşturulan anahtarınızı yapıştırın > **Kaydet**' e tıklayın.    
7. Değişikliklerinizi kaydettiğinizde bir bağlantı testi otomatik olarak başlayacaktır, Bildirimler sekmesinden görüntüleyebilirsiniz. İki bildirim vardır: biri, güncelleştirmeyi kaydetmeye karşılık gelir ve bağlantıyı test etmek için diğer karşılık gelir:  
   ![Anahtarı düzenledikten sonra bildirimler](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. [Son durdurulma zamanı bölümünden işinizi başlatmaya](#start-your-job-from-the-last-stopped-time) devam edin.

### <a name="event-hubs"></a>Event Hubs

1. Azure portal oturum açın > Stream Analytics işi için giriş/çıkış olarak kullandığınız Olay Hub 'ına gidin.    
2. Ayarlar bölümünde, **paylaşılan erişim ilkeleri** ' ni açın ve gerekli erişim ilkesini seçin. **Birincil anahtar** ve **İkincil anahtar** arasında, işiniz tarafından kullanılmayan birini seçin ve yeniden oluşturun:  
   ![Event Hubs için anahtarları yeniden oluştur](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Yeni oluşturulan anahtarı kopyalayın.    
4. Azure portal, **Durdur** ' u seçin ve işin durdurulmasını bekleyin > Stream Analytics işinize gidin.    
5. Kimlik bilgilerini güncelleştirmek istediğiniz olay hub 'larının giriş/çıkışını bulun.    
6. **Olay Hub 'ı Ilke anahtarı** alanını bulun ve yeni oluşturulan anahtarınızı yapıştırın > **Kaydet**' e tıklayın.    
7. Değişikliklerinizi kaydettiğinizde bir bağlantı testi otomatik olarak başlayacaktır, başarıyla geçirildiğinden emin olun.    
8. [Son durdurulma zamanı bölümünden işinizi başlatmaya](#start-your-job-from-the-last-stopped-time) devam edin.

### <a name="sql-database"></a>SQL Veritabanı

Mevcut bir kullanıcının oturum açma kimlik bilgilerini güncelleştirmek için SQL veritabanı 'na bağlanmanız gerekir. Azure portal veya SQL Server Management Studio gibi bir istemci tarafı aracı kullanarak kimlik bilgilerini güncelleştirebilirsiniz. Bu bölümde Azure portal kullanarak kimlik bilgilerini güncelleştirme işlemi gösterilmektedir.

1. Azure portal oturum açın > Stream Analytics iş için çıktı olarak kullandığınız SQL veritabanına gidin.    
2. **Veri Gezgini**'nde, veritabanınıza bağlanın/veritabanınıza bağlanın > Yetkilendirme türünü **SQL Server kimlik doğrulaması** olarak seçin > **oturum açma** ve **parola** ayrıntılarına yazın > Tamam ' **ı** seçin.  
   ![SQL veritabanı için kimlik bilgilerini yeniden üret](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. Sorgu sekmesinde, aşağıdaki sorguyu çalıştırarak kullanıcılarınızın parolasını değiştirin (Kullanıcı `<user_name>` adınızla ve yeni parolanızla değiştirdiğinizden emin olun `<new_password>` ):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Yeni parolayı bir yere göz önünde alın.    
5. Azure portal, **Durdur** ' u seçin ve işin durdurulmasını bekleyin > Stream Analytics işinize gidin.    
6. Kimlik bilgilerini döndürmek istediğiniz SQL veritabanı çıktısını bulun. Parolayı güncelleştirin ve değişiklikleri kaydedin.    
7. Değişikliklerinizi kaydettiğinizde bir bağlantı testi otomatik olarak başlayacaktır, başarıyla geçirildiğinden emin olun.    
8. [Son durdurulma zamanı bölümünden işinizi başlatmaya](#start-your-job-from-the-last-stopped-time) devam edin.

### <a name="power-bi"></a>Power BI
1. > Azure portal oturum açarak Stream Analytics işinizi inceleyin > **Durdur** ' u seçin ve işin durdurulmasını bekleyin.    
2. Kimlik bilgilerini yenilemek istediğiniz Power BI çıkışını bulun **>, >** değişiklikleri **kaydedin** (bir başarı iletisi görmeniz gerekir).    
3. Değişikliklerinizi kaydettiğinizde bir bağlantı testi otomatik olarak başlayacaktır, başarıyla geçirildiğinden emin olun.    
4. [Son durdurulma zamanı bölümünden işinizi başlatmaya](#start-your-job-from-the-last-stopped-time) devam edin.

## <a name="start-your-job-from-the-last-stopped-time"></a>İşinizi son durdurulma zamanından başlatın

1. İşi başlatmak için işin **genel bakış** bölmesine gidin > **Başlat** ' ı seçin.    
2. **Son durdurulduğunda** seçin > **Başlat**' a tıklayın. "Son durdurulma" seçeneğinin yalnızca, daha önce işi çalıştırdıysanız ve bazı çıktılar üretileceğini aklınızda göründüğünü unutmayın. İş, son çıkış değerinin saatine göre yeniden başlatılır.
   ![Stream Analytics işini Başlat](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](/rest/api/streamanalytics/)