---
title: SAP HANA veritabanlarının yedekleme hatalarını giderme
description: SAP HANA veritabanlarını yedeklemek için Azure Backup kullandığınızda oluşabilecek yaygın hataların nasıl giderileceği açıklanmaktadır.
ms.topic: conceptual
ms.date: 08/03/2019
ms.openlocfilehash: cbffa7415f315fd396e57afa355d2415c4612eb5
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172757"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure 'da SAP HANA veritabanlarının yedeklenmesi sorunlarını giderme

Bu makale, Azure sanal makinelerinde SAP HANA veritabanlarının yedeklenmesi için sorun giderme bilgileri sağlar. Aşağıdaki bölümde SAP HANA Yedeklemedeki yaygın bir hatayı tanılamak için gereken önemli kavramsal veriler ele alınmaktadır.

## <a name="prerequisites"></a>Önkoşullar

[Önkoşulların](backup-azure-sap-hana-database.md#prerequisites)bir parçası olarak preregistration BETIĞININ, Hana 'nın yüklü olduğu sanal makinede çalıştırıldığınızdan emin olun.

### <a name="setting-up-permissions"></a>İzinleri ayarlama

Preregistration betiği şunları yapar:

1. HANA sisteminde AZUREWLBACKUPHANAUSER oluşturur ve gerekli rolleri ve izinleri ekler:
    - VERITABANı YÖNETICISI: geri yükleme sırasında yeni DBs oluşturmak için.
    - Katalog okuma: yedekleme kataloğunu okumak için.
    - SAP_INTERNAL_HANA_SUPPORT: birkaç özel tabloya erişmek için.
2. Tüm işlemleri işlemek için (veritabanı sorguları, geri yükleme işlemleri, yedek yapılandırma ve çalıştırma), HANA eklentisinin Hdbuserstore öğesine bir anahtar ekler.

   Anahtar oluşturmayı onaylamak için, SIDADDM kimlik bilgileriyle HANA makinesinde HDBSQL komutunu çalıştırın:

    ``` hdbsql
    hdbuserstore list
    ```

    Komut çıktısı {SID} {DBNAME} anahtarını, AZUREWLBACKUPHANAUSER olarak gösterilen kullanıcıyla görüntülemelidir.

> [!NOTE]
> **/Usr/SAP/{Sid}/Home/.HDB/** altında benzersiz bir ssfs dosyaları kümesine sahip olduğunuzdan emin olun. Bu yolda yalnızca bir klasör olmalıdır.

### <a name="setting-up-backint-parameters"></a>Backınt parametrelerini ayarlama

Yedekleme için bir veritabanı seçildikten sonra, Azure Backup hizmeti, geri Int parametreleri VERITABANı düzeyinde yapılandırır:

- [catalog_backup_using_backint: true]
- [enable_accumulated_catalog_backup: false]
- [parallel_data_backup_backint_channels: 1]
- [log_backup_timeout_s: 900)]
- [backint_response_timeout: 7200]

> [!NOTE]
> Bu parametrelerin konak düzeyinde mevcut *olmadığından* emin olun. Ana bilgisayar düzeyindeki Parametreler bu parametreleri geçersiz kılacak ve beklenmeyen davranışlara neden olabilir.

## <a name="restore-checks"></a>Geri yükleme denetimleri

### <a name="single-container-database-sdc-restore"></a>Tek kapsayıcı veritabanı (SDC) geri yükleme

HANA için tek bir kapsayıcı veritabanını (SDC) başka bir SDC makinesine geri yüklerken girişlerden yararlanın. Veritabanı adı, köşeli ayraçlar içinde "SDC" eklenmiş şekilde, küçük harfle verilmelidir. HANA örneği büyük harf olarak görüntülenecektir.

"H21" SDC HANA örneğinin yedeklenmekte olduğunu varsayalım. Yedekleme öğeleri sayfasında, yedekleme öğesi adı **"H21 (SDC)"** olarak gösterilir. Bu veritabanını başka bir hedef SDC 'ye geri yüklemeye çalışırsanız, H11 deyin ve aşağıdaki girişlerin sağlanması gerekir.

![SDC geri yükleme girişleri](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Aşağıdaki noktalara dikkat edin:

- Varsayılan olarak, geri yüklenen veritabanı adı, yedek öğe adı (örneğin, H21 (SDC) ile doldurulur
- H11 olarak Target seçildiğinde geri yüklenen veritabanı adı otomatik olarak değişmez. **H11 (SDC) olarak düzenlenmelidir**. SDC söz konusu olduğunda, geri yüklenen veritabanı adı, büyük harfle birlikte hedef örnek KIMLIĞI ve köşeli ayraçlar içinde ' SDC ' olarak eklenir.
- SDC 'nin yalnızca tek bir veritabanı olduğundan, var olan veritabanı verilerinin kurtarma noktası verileriyle geçersiz kılınmasına izin vermek için onay kutusuna tıklamanız gerekir.
- Linux büyük/küçük harfe duyarlıdır ve bu nedenle durumu koruduğunuzdan emin olun.

### <a name="multiple-container-database-mdc-restore"></a>Birden çok kapsayıcı veritabanı (MDC) geri yükleme

HANA için birden çok kapsayıcı veritabanında standart yapılandırma SISTEM DB + 1 veya daha fazla kiracı DBs 'dir. SAP HANA örneğinin tamamının geri yüklenmesi hem SYSTEMDB hem de kiracı veritabanlarını geri yüklemek anlamına gelir. İlk olarak SYSTEMDB 'yi geri yükler ve ardından kiracı DB 'ye devam eder. Sistem DB temelde, seçili hedefteki sistem bilgilerini geçersiz kılmak anlamına gelir. Bu ayrıca hedef örnekteki BackInt ile ilgili bilgileri geçersiz kılar. Bu nedenle, sistem DB bir hedef örneğe geri yüklendikten sonra, birinin ön kayıt betiğini yeniden çalıştırması gerekir. Yalnızca sonraki kiracı DB geri yüklemeleri başarılı olur.

## <a name="common-user-errors"></a>Ortak Kullanıcı hataları

### <a name="usererrorinopeninghanaodbcconnection"></a>Usererrorınopeninghanaodbcconnection

data| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| HANA sistemine bağlanılamadı. Sisteminizin çalışır ve çalışır olduğunu doğrulayın.| HANA veritabanı çalışmadığından Azure Backup hizmeti HANA 'ya bağlanamaz. Ya da HANA çalışıyor ancak Azure Backup hizmetinin bağlanmasına izin vermiyor. | HANA veritabanının veya hizmetinin çalışıp çalışmadığını denetleyin. HANA veritabanı veya hizmeti çalışır duruma ayarlanmışsa, [tüm izinlerin ayarlanmış](#setting-up-permissions)olup olmadığını kontrol edin. Anahtar eksikse, yeni bir anahtar oluşturmak için preregistration betiğini yeniden çalıştırın. |

### <a name="usererrorinvalidbackintconfiguration"></a>Usererrorınvalidbackintconfiguration

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Geçersiz Backınt yapılandırması algılandı. Korumayı durdurun ve veritabanını yeniden yapılandırın.| Backınt parametreleri Azure Backup için yanlış belirtilmiştir. | [Parametrelerin ayarlanmış](#setting-up-backint-parameters)olup olmadığını denetleyin. KONAKTA Backınt tabanlı parametreler varsa, bunları kaldırın. Parametreler ana bilgisayar düzeyinde yoksa ancak bir veritabanı düzeyinde el ile değiştirilmişse, bunları daha önce açıklandığı gibi uygun değerlere dönüştürmeniz gerekir. Ya da, **korumayı durdurun ve Azure Portal yedekleme verilerini koruyun** ve sonra **yedeklemeyi devam ettirir**' ı seçin.|
