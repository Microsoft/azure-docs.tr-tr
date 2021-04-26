---
title: MySQL için dbForge Studio 'Yu kullanarak MySQL için Azure veritabanı 'na bağlanma
description: Makalede, MySQL için dbForge Studio aracılığıyla MySQL için Azure veritabanı sunucusuna nasıl bağlanulacağı gösterilmektedir.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 942651aadf4113c1aca32e4e1d2c558b0d764421
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377234"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>MySQL için dbForge Studio 'Yu kullanarak MySQL için Azure veritabanı 'na bağlanma

[MySQL Için Dbforge Studio 'yu](https://www.devart.com/dbforge/mysql/studio/)kullanarak MySQL Için Azure veritabanı 'na bağlanmak için:

1. Veritabanı menüsünde Yeni bağlantı ' yı seçin.

2. Ana bilgisayar adı ve oturum açma kimlik bilgilerini sağlayın.

3. Yapılandırmayı denetlemek için Bağlantıyı Sına düğmesini seçin.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure Bağlantısı":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Yedekleme ve geri yükleme işlevini kullanarak bir veritabanını geçirme

Studio, veritabanlarının Azure 'a çok çeşitli yollarla geçirilmesini sağlar ve bu seçim yalnızca gereksinimlerinize göre değişir. Veritabanının tamamını taşımanız gerekiyorsa yedekleme ve geri yükleme işlevselliğinin kullanılması en iyisidir. Bu örnekte, MySQL sunucusunda bulunan *Sakila* veritabanını MySQL Için Azure veritabanı 'na geçiririz. MySQL için dbForge Studio 'nun yedekleme ve geri yükleme işlevini kullanarak geçiş işleminin arkasındaki mantık, MySQL veritabanının bir yedeğini oluşturmak ve ardından MySQL için Azure veritabanı 'na geri yüklemektir.

### <a name="back-up-the-database"></a>Veritabanını yedekleme

1. Veritabanı menüsünde, Yedekle ve geri yükle ' nin üzerine gelin ve ardından yedekleme veritabanı ' nı seçin. Veritabanı Yedekleme Sihirbazı görüntülenir.

2. Veritabanı Yedekleme Sihirbazı 'nın yedekleme içeriği sekmesinde, yedeklemek istediğiniz veritabanı nesneleri ' ni seçin.

3. Seçenekler sekmesinde, yedekleme işlemini gereksinimlerinize uyacak şekilde yapılandırın.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Yedekleme Sihirbazı seçenekleri":::

4. Sonra, hata işleme davranışını ve günlüğe kaydetme seçeneklerini belirtin.

5. Yedekle 'yi seçin.

### <a name="restore-the-database"></a>Veritabanını geri yükleme

1. Yukarıda açıklandığı gibi MySQL için Azure veritabanı 'na bağlanın.

2. Veritabanı Gezgini gövdesine sağ tıklayın, Yedekle ve geri yükle ' nin üzerine gelin ve ardından veritabanını geri yükle ' yi seçin.

3. Açılan veritabanı geri yükleme sihirbazında, veritabanı yedeklemesi olan bir dosya seçin.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Geri yükleme adımı":::

4. Geri yükle ' yi seçin.

5. Sonucu denetleyin.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Veritabanlarını kopyalama işlevini kullanarak bir veritabanını geçirme

Veritabanı kopyalama işlevselliği, yedekleme ve geri yükleme ile benzerdir, ancak bunun dışında bir veritabanını geçirmek için iki adım gerekmez. Ve daha fazlası, özelliği tek bir go içinde iki veya daha fazla veritabanının aktarılmasına izin verir. Veritabanı kopyalama işlevselliği yalnızca MySQL için dbForge Studio Enterprise sürümünde kullanılabilir.
Bu örnekte, *world_x* veritabanını MySQL Server 'dan MySQL Için Azure veritabanı 'na geçiririz.
Veritabanlarını kopyalama işlevini kullanarak bir veritabanını geçirmek için:

1. Veritabanı menüsünde veritabanlarını Kopyala ' yı seçin. 

2. Görüntülenen veritabanları Kopyala sekmesinde kaynak ve hedef bağlantıyı belirtip geçirilecek veritabanlarını seçin. Azure MySQL bağlantısı girip *world_x* veritabanını seçersiniz. İşlemi başlatmak için yeşil oku seçin.

3. Sonucu denetleyin.

Veritabanı geçiş çabalarımızın bir sonucu olarak, *world_x* veritabanı Azure MySQL 'de başarıyla görüntülendi.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Veritabanlarını kopyalama sonucu":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Şema ve veri karşılaştırma araçlarını kullanarak bir veritabanını geçirme

MySQL için dbForge Studio, MySQL veritabanları, MySQL şemaları ve \veya verilerini Azure 'a geçirmeye izin veren birkaç araç içerir. İşlevsellik seçimi, gereksinimlerinize ve projenizin gereksinimlerine bağlıdır. Bir veritabanını seçmeli olarak taşımanız gerekiyorsa, diğer bir deyişle, belirli MySQL tablolarını Azure 'a geçirirseniz, şema ve veri karşılaştırma işlevlerini kullanmak en iyisidir.
Bu örnekte, MySQL sunucusunda bulunan *Dünya* veritabanını MySQL Için Azure veritabanı 'na geçiririz. MySQL için dbForge Studio 'nun şema ve veri karşılaştırma işlevini kullanarak geçiş işleminin arkasındaki mantık, MySQL için Azure veritabanı 'nda boş bir veritabanı oluşturmaktır, şema karşılaştırma aracını ve ardından veri karşılaştırma aracı 'nı kullanarak önce gerekli MySQL veritabanıyla eşitler. Bu şekilde, MySQL şemaları ve verileri doğru şekilde Azure 'a taşınır.

### <a name="step-1-connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Adım 1. MySQL için Azure veritabanı 'na bağlanma ve boş bir veritabanı oluşturma

### <a name="step-2-schema-synchronization"></a>Adım 2. Şema eşitleme

1. Karşılaştırma menüsünde yeni şema karşılaştırması ' ni seçin.
Yeni şema karşılaştırma Sihirbazı görüntülenir.

2. Kaynağı ve hedefi seçin, sonra şema karşılaştırma seçeneklerini belirtin. Karşılaştır ' ı seçin.

3. Görüntülenen karşılaştırma sonuçları kılavuzunda, eşitleme için nesneleri seçin. Şema eşitleme Sihirbazı 'nı açmak için yeşil ok düğmesini seçin.

4. Eşitleme Yapılandırma Sihirbazı adımlarını gözden geçir. Değişiklikleri dağıtmak için Synchronize ' ı seçin.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Şema eşitleme Sihirbazı":::

### <a name="step-3-data-comparison"></a>3. Adım Veri karşılaştırması

1. Karşılaştırma menüsünde Yeni veri karşılaştırması ' ni seçin. Yeni veri karşılaştırma Sihirbazı görüntülenir.

2. Kaynağı ve hedefi seçin, ardından veri karşılaştırma seçeneklerini belirtin ve gerekirse eşlemeleri değiştirin. Karşılaştır ' ı seçin.

3. Görüntülenen karşılaştırma sonuçları kılavuzunda, eşitleme için nesneleri seçin. Veri eşitleme Sihirbazı 'nı açmak için yeşil ok düğmesini seçin.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Veri kompozisyonu sonucu":::

4. Eşitleme Yapılandırma Sihirbazı adımlarını gözden geçir. Değişiklikleri dağıtmak için Synchronize ' ı seçin.

5. Sonucu denetleyin.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Veri eşitleme sonucu":::

## <a name="summary"></a>Özet

Günümüzde daha fazla işletme, bu veritabanı hizmeti kolayca ayarlanmayı, yönetmeyi ve ölçeklendirmeyi kolaylaştırmak için veritabanlarını MySQL için Azure veritabanı 'na taşımaktadır. Bu geçişin sorunsuz olması gerekmez. MySQL için dbForge Studio Bode, süreci önemli ölçüde kolaylaştıran geçiş araçları. Studio, veritabanı aktarımının kolayca yapılandırılmasını, kaydedilmesini, düzenlenmesini, otomatik olarak ve zamanlanmasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar
- [MySQL genel bakış](overview.md)
