---
title: Azure SQL yönetilen örneği 'ne çevrimiçi geçişlerle ilgili bilinen sorunlar ve sınırlamalar
description: Azure SQL yönetilen örneği 'ne çevrimiçi geçişlerle ilişkili bilinen sorunlar/geçiş sınırlamaları hakkında bilgi edinin.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98695549"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği 'ne çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları

SQL Server ile Azure SQL yönetilen örneği arasındaki çevrimiçi geçişlerle ilişkili bilinen sorunlar ve sınırlamalar aşağıda açıklanmaktadır.

> [!IMPORTANT]
> Azure SQL veritabanı 'na SQL Server çevrimiçi geçişlerde SQL_variant veri türlerinin geçirilmesi desteklenmez.

## <a name="backup-requirements"></a>Yedekleme gereksinimleri

- **Sağlama toplamı olan yedeklemeler**

    Azure veritabanı geçiş hizmeti, şirket içi veritabanlarınızı SQL yönetilen örneğine geçirmek için yedekleme ve geri yükleme yöntemini kullanır. Azure veritabanı geçiş hizmeti yalnızca sağlama toplamı kullanılarak oluşturulan yedeklemeleri destekler.

    Yedekleme [veya geri yükleme sırasında yedekleme sağlama toplamlarını etkinleştirin veya devre dışı bırakın (SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).

    > [!NOTE]
    > Veritabanı yedeklerini sıkıştırma ile alırsanız, açıkça devre dışı bırakılmadığı takdirde sağlama toplamı varsayılan davranıştır.

    Çevrimdışı geçişlerde, **Azure veritabanı geçiş hizmeti 'ne izin vereceğim...**' yi seçerseniz, Azure veritabanı geçiş hizmeti, veritabanı yedeklemesini sağlama toplamı seçeneği etkin olacak şekilde alır.

- **Yedekleme medyası**

    Her yedeklemeyi ayrı bir yedekleme ortamında (yedekleme dosyaları) aldığınızdan emin olun. Azure veritabanı geçiş hizmeti, tek bir yedekleme dosyasına eklenen yedeklemeleri desteklemez. Yedekleme dosyalarını ayırmak için tam yedekleme ve günlük yedeklemeleri yapın.

## <a name="data-and-log-file-layout"></a>Veri ve günlük dosyası düzeni

- **Günlük dosyası sayısı**

    Azure veritabanı geçiş hizmeti birden çok günlük dosyası olan veritabanlarını desteklemez. Birden çok günlük dosyanız varsa, bunları daraltın ve tek bir işlem günlük dosyasında yeniden düzenleyin. Boş olmayan dosyaları günlüğe uzak olmadığınızdan, önce günlük dosyasını yedeklemeniz gerekir.

## <a name="sql-server-features"></a>SQL Server özellikleri

- **FILESTREAM/dosya tabloları**

    SQL yönetilen örneği şu anda FILESTREAM ve FileTables 'ı desteklemiyor. Bu özelliklere bağımlı olan iş yükleri için Azure hedefi olarak Azure VM 'lerinde çalışan SQL Server 'Lar için tercih etmenizi öneririz.

- **Bellek içi tablolar**

    Bellek içi OLTP, SQL yönetilen örneği için Premium ve İş Açısından Kritik katmanlarında kullanılabilir; Genel Amaçlı katmanı bellek Içi OLTP 'Yi desteklemez.

## <a name="migration-resets"></a>Geçiş sıfırlamaları

- **Dağıtımlar**

    SQL yönetilen örneği, otomatik düzeltme eki uygulama ve sürüm güncelleştirmeleri içeren bir PaaS hizmetidir. SQL yönetilen örneğinizin geçirilmesi sırasında kritik olmayan güncelleştirmeler 36 saate kadar tutulur. Daha sonra (ve kritik güncelleştirmeler için), geçiş kesintiye uğratılıyor ise işlem tam geri yükleme durumuna sıfırlanır.

    Tam geçişi geçişi yalnızca tam yedekleme geri yüklendikten sonra çağrılabilir ve tüm günlük yedeklemeleriyle yapılır. Üretim geçişiniz etkileniyorsa [Azure DMS geri bildirim diğer adı](mailto:dmsfeedback@microsoft.com)ile iletişime geçin.

## <a name="smb-file-share-connectivity"></a>SMB dosya paylaşma bağlantısı

SMB dosya paylaşımıyla bağlantı kurma sorunları büyük olasılıkla izin sorunu nedeniyle oluşur. 

SMB dosya paylaşımının bağlantısını test etmek için aşağıdaki adımları izleyin: 

1. SMB dosya paylaşımında bir yedek kaydedin. 
1. Azure veritabanı geçiş hizmeti ve kaynak SQL Server alt ağı arasındaki ağ bağlantısını doğrulayın. Bunu yapmanın en kolay yolu, DMS alt ağına SQL Server sanal makine dağıtmaktır ve kaynak SQL Server SQL Server Management Studio kullanarak bağlanır. 
1. Kaynak SQL Server üst bilgisini FileShare üzerindeki yedekten geri yükleyin: 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

Dosya paylaşımıyla bağlantı kuramıyor, izinleri şu adımlarla yapılandırın: 

1. Dosya Gezgini 'ni kullanarak dosya paylaşımınıza gidin. 
1. Dosya paylaşımıyla sağ tıklayıp Özellikler ' i seçin. 
1. **Paylaşım** sekmesini seçin ve **Gelişmiş paylaşım**' ı seçin. 
1. Geçiş için kullanılan Windows hesabını ekleyin ve tam denetim erişimi atayın. 
1. SQL Server hizmet hesabını ekleyin ve tam denetim erişimi atayın. Hangi hesabın kullanılmakta olduğundan emin değilseniz SQL Server hizmet hesabının **SQL Server Yapılandırma Yöneticisi** denetleyin. 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="Geçiş için kullanılan Windows hesaplarına ve SQL Server hizmeti hesabına yönelik tam denetim erişimi verin. ":::

