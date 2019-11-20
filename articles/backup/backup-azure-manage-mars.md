---
title: MARS Aracısı yedeklemelerini yönetme ve izleme
description: Azure Backup hizmetini kullanarak Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı yedeklemelerini yönetme ve izleme hakkında bilgi edinin.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: edb672f97f467378176bf6cdf04fe8e22cc5e51b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173075"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Azure Backup hizmetini kullanarak Microsoft Azure Kurtarma Hizmetleri (MARS) aracı yedeklemelerini yönetme

Bu makalede, Microsoft Azure Kurtarma Hizmetleri aracısıyla yedeklenen dosya ve klasörlerin nasıl yönetileceği açıklanır.

## <a name="create-a-backup-policy"></a>Yedekleme ilkesi oluşturma

Yedekleme ilkesi, kurtarma noktaları oluşturmak için verilerin ne zaman anlık görüntülerinin alınacağını ve kurtarma noktalarının ne kadar süreyle saklanacağını belirtir. MARS aracısını kullanarak yedekleme ilkesini yapılandırırsınız.

Aşağıdaki şekilde bir ilke oluşturun:

1. MARS aracısını indirip kaydettikten sonra, aracı konsolunu başlatın. Bunu, makinenizde **Microsoft Azure Backup** aramasını yaparak bulabilirsiniz.  
2. **Eylemler**' de, **yedeklemeyi zamanla**' ya tıklayın.

    ![Windows Server yedeklemesini zamanlama](./media/backup-configure-vault/schedule-first-backup.png)
3. Yedekleme zamanlaması Sihirbazı > **Başlarken** **İleri**' ye tıklayın.
4. **Yedeklenecek öğeleri seçin**' de, **öğe Ekle**' ye tıklayın.

    ![Yedeklenecek öğeleri seçin](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. **Öğeleri seç**' de, ne yedeklemek istediğinizi seçin ve **Tamam**' a tıklayın.

    ![Yedeklenecek seçili öğeler](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. **Yedeklenecek öğeleri seçin** sayfasında **İleri**' ye tıklayın.
7. **Yedekleme zamanlamasını belirtin** sayfasında, günlük veya haftalık yedeklemeleri ne zaman almak istediğinizi belirtin. Ardından **İleri**'ye tıklayın.

    - Bir yedekleme çekilirken bir kurtarma noktası oluşturulur.
    - Ortamınızda oluşturulan kurtarma noktası sayısı yedekleme zamanlamanıza bağlıdır.

8. Günde en fazla üç kez günlük yedeklemeler zamanlayabilirsiniz. Örneğin ekran görüntüsünde, biri gece yarısı ve diğeri 6:00 PM olmak üzere iki günlük yedek gösterilmektedir.

    ![Günlük zamanlama](./media/backup-configure-vault/day-schedule.png)

9. Haftalık yedeklemeleri de çalıştırabilirsiniz. Örneğin ekran görüntüsünde, 9:30 ve 1:00 ' de Çarşamba & her alternatif Pazar için alınan yedeklemeler gösterilmektedir.

    ![Haftalık zamanlama](./media/backup-configure-vault/week-schedule.png)

10. **Bekletme Ilkesi Seç** sayfasında, verilerinizin geçmiş kopyalarını nasıl depolayakullanacağınızı belirtin. Ardından **İleri**'ye tıklayın.

    - Bekletme ayarları hangi kurtarma noktalarının depolanacağını ve ne kadar süreyle depolanması gerektiğini belirtir.
    - Örneğin, günlük bir bekletme ayarı ayarladığınızda, günlük bekletme için belirtilen zamanda en son kurtarma noktasının belirtilen gün sayısı boyunca tutulacaksınız. Ya da başka bir örnek olarak, her ayın 30. gününde oluşturulan kurtarma noktasının 12 ay boyunca depolanması gerektiğini göstermek için bir aylık bekletme ilkesi belirtebilirsiniz.
    - Günlük ve haftalık kurtarma noktası bekletme genellikle yedekleme zamanlaması ile saatle çakışan. Yedekleme zamanlamaya göre tetiklendiğinde, yedekleme tarafından oluşturulan kurtarma noktası günlük veya haftalık bekletme ilkesinde belirtilen süre için depolanır.
    - Örnek olarak, aşağıdaki ekran görüntüsünde:-gece yarısı ve 6:00 PM 'de günlük yedeklemeler yedi gün boyunca tutulur.
            -Gece yarısı ve 6:00 PM 'de Cumartesi günü gerçekleştirilen yedeklemeler dört hafta boyunca tutulur.
            -Gece yarısı ve 6:00 PM 'deki ayın son haftasında Cumartesi günü alınan yedeklemeler 12 ay boyunca tutulur.
            -Mart 'ın son haftasındaki Cumartesi günü alınan yedeklemeler 10 yıl boyunca tutulur.

    ![Bekletme örneği](./media/backup-configure-vault/retention-example.png)

11. İlk yedekleme **türünü seçin** bölümünde, ağ üzerinden ilk yedeklemeyi almak mi yoksa çevrimdışı yedekleme mi kullanmak istediğinize karar verin (çevrimdışı yedekleme hakkında daha fazla bilgi için bu [makaleye](backup-azure-backup-import-export.md)bakın). İlk yedeklemeyi ağ üzerinden almak için, **ağ üzerinden otomatik olarak** ' ı seçin ve **İleri**' ye tıklayın.

    ![ilk yedekleme türü](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

12. **Onaylama**bölümünde bilgileri gözden geçirin ve ardından **son**' a tıklayın.
    ![](./media/backup-azure-manage-mars/confirm-backup-type.png) yedekleme türünü onaylayın

13. Sihirbaz yedekleme zamanlamasını oluşturduktan sonra **Kapat**'a tıklayın.
  ![yedekleme Işlemini değiştirmeyi Onayla](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Aracının yüklü olduğu her makinede bir ilke oluşturmanız gerekir.

## <a name="modify-a-backup-policy"></a>Yedekleme ilkesini değiştirme

Yedekleme ilkesini değiştirirken, yeni öğeler ekleyebilir, var olan öğeleri yedekten kaldırabilir veya dışlama ayarları kullanılarak yedeklenen dosyaları dışarıda bırakabilirsiniz.

- **Öğe Ekle** bu seçeneği yalnızca yedeklenecek yeni öğeler eklemek için kullanın. Varolan öğeleri kaldırmak için **öğeleri kaldır** veya **dışlama ayarları** seçeneğini kullanın.  
- **Öğeleri kaldır** öğeleri yedeklenmeden kaldırmak için bu seçeneği kullanın.
  - **Öğeleri kaldırmak**yerine bir birim içindeki tüm öğeleri kaldırmak Için **dışlama ayarlarını** kullanın.
  - Bir birimdeki tüm seçimlerin temizlenmesi, öğelerin eski yedeklerinin, son yedekleme sırasında değişiklik için kapsam olmadan saklama ayarlarına göre korunmasına neden olur.
  - Bu öğeleri yeniden seçtiğinizde, ilk tam yedekleme ve yeni ilke değişiklikleri eski yedeklemelere uygulanmaz.
  - Tüm birim seçimini kaldırmak, bekletme ilkesini değiştirmek için herhangi bir kapsam olmadan son yedeklemeyi korur.
- **Dışlama ayarları** belirli öğelerin yedeklenmesini hariç tutmak için bu seçeneği kullanın.
  
### <a name="add-new-items-to-existing-policy"></a>Varolan ilkeye yeni öğe ekleme

1. **Eylemler**' de, **yedeklemeyi zamanla**' ya tıklayın.

    ![Windows Server yedeklemesini zamanlama](./media/backup-configure-vault/schedule-first-backup.png)

2. **İlke öğesi Seç** sekmesinde, **dosyalarınız ve klasörleriniz Için Yedekleme zamanlamasını değiştir** ' i seçin ve **İleri**' ye tıklayın.

    ![Ilke öğelerini seçin](./media/backup-azure-manage-mars/select-policy-items.png)

3. **Zamanlamayı yedeklemeyi Değiştir veya Durdur** sekmesinde **yedekleme öğelerinde veya saatlerde değişiklik yap** ' ı seçin ve **İleri**' ye tıklayın.

    ![Yedeklemeyi değiştirin veya zamanlayın](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Yedeklenecek **öğeleri seçin** sekmesinde, yedeklemek istediğiniz öğeleri eklemek Için **öğe Ekle** ' ye tıklayın.

    ![Yedekleme öğesi ekleme veya zamanlama](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. **Öğeleri seç** penceresinde, eklemek istediğiniz uçveya klasörler ' i seçin ve **Tamam**' a tıklayın.

    ![Öğeleri seçin](./media/backup-azure-manage-mars/select-item.png)

6. Sonraki adımları tamamlayarak işlemi tamamladıktan sonra **son** ' a tıklayın.

### <a name="add-exclusion-rules-to-existing-policy"></a>Mevcut ilkeye dışlama kuralları Ekle

Yedeklenmesini istemediğiniz dosya ve klasörleri atlamak için dışlama kuralları ekleyebilirsiniz. Bunu, yeni bir ilke tanımlarken veya var olan bir ilkeyi değiştirirken yapabilirsiniz.

1. Eylemler bölmesinden, **yedeklemeyi zamanla**' ya tıklayın. **Yedeklenecek öğeleri seçin** ' e gidin ve **dışlama ayarları**' na tıklayın.

    ![Öğeleri seçin](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. **Dışlama ayarları**' nda **dışlama Ekle**' ye tıklayın.

    ![Öğeleri seçin](./media/backup-azure-manage-mars/add-exclusion.png)

3. **Hariç tutulacak öğeleri seçin**listesinden dosya ve klasörlere gözatıp dışlamak istediğiniz öğeleri seçin ve **Tamam**' a tıklayın.

    ![Öğeleri seçin](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Varsayılan olarak, seçilen klasörlerin içindeki tüm **alt klasörler** hariç tutulur. Bunu, **Evet** veya **Hayır**' a tıklayarak değiştirebilirsiniz. Aşağıda gösterildiği gibi, dışarıda tutulacak dosya türlerini düzenleyebilir ve belirli dosyaları seçebilirsiniz:

    ![Öğeleri seçin](./media/backup-azure-manage-mars/subfolders-type.png)

5. Sonraki adımları tamamlayarak işlemi tamamladıktan sonra **son** ' a tıklayın.

### <a name="remove-items-from-existing-policy"></a>Varolan ilkeden öğeleri kaldır

1. Eylemler bölmesinden, **yedeklemeyi zamanla**' ya tıklayın. **Yedeklenecek öğeleri seçin**bölümüne gidin. Listeden, yedekleme zamanlamadan kaldırmak istediğiniz dosya ve klasörleri seçin ve **öğeleri kaldır**' a tıklayın.

    ![Öğeleri seçin](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> Bir birimi ilkeden tamamen kaldırdığınızda dikkatli ilerleyin.  Yeniden eklemeniz gerekiyorsa, yeni bir birim olarak kabul edilir. Sonraki zamanlanmış yedekleme, artımlı yedekleme yerine bir Ilk yedekleme (tam yedekleme) gerçekleştirir. Daha sonra öğeleri daha sonra kaldırmanız ve eklemeniz gerekiyorsa, tam yedekleme yerine artımlı yedekleme sağlamak için **öğeleri kaldırmak** yerine **Dışlamalar ayarlarının** kullanılması önerilir.

2. Sonraki adımları tamamlayarak işlemi tamamladıktan sonra **son** ' a tıklayın.

## <a name="stop-protecting-files-and-folder-backup"></a>Dosya ve klasör yedeklemesini korumayı durdur

Dosya ve klasör yedeklemesini korumayı durdurmanın iki yolu vardır:

- **Korumayı durdurun ve yedekleme verilerini koruyun**.
  - Bu seçenek, gelecekteki tüm yedekleme işlerini korumadan durdurur.
  - Azure Backup hizmet, bekletme ilkesine bağlı olarak yedeklenen kurtarma noktalarını korur.
  - Süre dolmamış kurtarma noktaları için yedeklenmiş verileri geri yükleyebileceksiniz.
  - Korumayı sürdürmeye karar verirseniz, *Yedekleme zamanlamasını yeniden etkinleştir* seçeneğini kullanabilirsiniz. Bundan sonra, veriler yeni bekletme ilkesine göre tutulacaktır.
- **Korumayı durdurun ve yedekleme verilerini silin**.
  - Bu seçenek, gelecekteki tüm yedekleme işlerinin verilerinizi korumasını durdurur ve tüm kurtarma noktalarını siler.
  - *Bu yedekleme öğesi için verileriniz silinmiş bir iletiyle birlikte yedekleme verilerini sil uyarı e-postasını alırsınız. Bu veriler 14 gün boyunca geçici olarak kullanılabilir olacak, sonrasında kalıcı olarak silinecek* ve önerilen eylem, *verilerinizi kurtarmak için 14 gün içinde yedekleme öğesini yeniden koruacaktır.*
  - Korumayı sürdürmesini sağlamak için silme işleminden 14 gün içinde yeniden koruma yapın.

### <a name="stop-protection-and-retain-backup-data"></a>Korumayı durdurun ve yedekleme verilerini koruyun

1. MARS yönetim konsolunu açın, **eylemler bölmesine**gidin ve **yedeklemeyi zamanla**' yı seçin.
    Zamanlanmış bir yedeklemeyi değiştirmek veya durdurmak ![.](./media/backup-azure-manage-mars/mars-actions.png)
1. **Ilke öğesi Seç** sayfasında **dosyalarınız ve klasörleriniz için bir yedekleme zamanlamasını değiştir** ' i seçin, **İleri**' ye tıklayın.
    Zamanlanmış bir yedeklemeyi değiştirmek veya durdurmak ![.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. **Zamanlanmış bir yedeklemeyi Değiştir veya Durdur** sayfasında, **Bu yedekleme zamanlamasını kullanmayı Durdur ' u seçin, ancak bir zamanlama yeniden etkinleştirilinceye kadar depolanan yedeklemeleri saklayın**. Sonra **İleri**’yi seçin.  
    Zamanlanmış bir yedeklemeyi değiştirmek veya durdurmak ![.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. **Zamanlanan yedeklemeyi Duraklat** bölümünde bilgileri gözden geçirin ![zamanlanan bir yedeklemeyi değiştirmek veya durdurmak için **son** ' a tıklayın.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. **yedekleme Işlemini Değiştir** ' de yedekleme duraklatma durumu başarısını denetleyip **son ' a** tıklayın.

### <a name="stop-protection-and-delete-backup-data"></a>Korumayı Durdur ve yedekleme verilerini sil

1. MARS yönetim konsolunu açın, **Eylemler** bölmesine gidin ve **yedeklemeyi zamanla**' yı seçin.
2. **Zamanlanmış bir yedeklemeyi Değiştir veya Durdur** sayfasında, **Bu yedekleme zamanlamasını kullanarak Durdur ' u seçin ve tüm depolanan yedeklemeleri silin**. Sonra **İleri**’yi seçin.

    ![Zamanlanmış bir yedeklemeyi değiştirin veya durdurun.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. **Zamanlanan yedeklemeyi durdur** sayfasında **son**' u seçin.

    ![Zamanlanmış bir yedeklemeyi durdurun.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. El ile oluşturmanız gereken bir güvenlik PIN 'ı (kişisel kimlik numarası) girmeniz istenir. Bunu yapmak için öncelikle Azure portal oturum açın.
5. **Kurtarma Hizmetleri kasası** > **ayarları** > **Özellikler**' e gidin.
6. **GÜVENLIK PIN**'ı altında **Oluştur**' u seçin. Bu PIN 'ı kopyalayın. PIN yalnızca beş dakika için geçerlidir.
7. Yönetim konsolunda PIN 'i yapıştırın ve **Tamam**' ı seçin.

    ![Güvenlik PIN 'ı oluştur.](./media/backup-azure-delete-vault/security-pin.png)

8. **Yedekleme Ilerlemesini Değiştir** sayfasında aşağıdaki ileti görüntülenir: *silinen yedekleme verileri 14 gün boyunca saklanacaktır. Bu süreden sonra, yedekleme verileri kalıcı olarak silinir.*  

    ![Yedekleme altyapısını silin.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Şirket içi yedekleme öğelerini sildikten sonra, portaldan sonraki adımları izleyin.

## <a name="re-enable-protection"></a>Korumayı yeniden etkinleştirin

Verileri korurken korumayı durdurduysanız ve korumayı sürdürmeye karar verdiyseniz Yedekleme zamanlamasını değiştir seçeneğini kullanarak yedeklemeyi yeniden etkinleştirebilirsiniz.

1. **Eylemler** üzerinde **yedeklemeyi zamanla**' yı seçin.
1. **Yedekleme zamanlamasını yeniden etkinleştir ' i seçin. Ayrıca, yedekleme öğelerini veya Tines 'yi değiştirebilir** ve **İleri**' ye tıklayabilirsiniz.
    ![yedekleme altyapısını silin.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. **Yedeklenecek öğeleri seçin**bölümünde **İleri**' ye tıklayın.
    ![yedekleme altyapısını silin.](./media/backup-azure-manage-mars/re-enable-next.png)
1. **Yedekleme zamanlamasını belirtin**bölümünde Yedekleme zamanlamasını belirtip **İleri**' ye tıklayın.
1. **Bekletme Ilkesi Seç**bölümünde, saklama süresini belirtin ve **İleri**' ye tıklayın.
1. Son olarak, **Uyumluluk** ekranında, ilke ayrıntılarını gözden geçirin ve **son**' a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- Desteklenen senaryolar ve sınırlamalar hakkında daha fazla bilgi için, [Mars Için destek matrisine](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)bakın.
- [İsteğe bağlı yedekleme ilkesi saklama davranışı](backup-configure-vault.md#on-demand-backup-policy-retention-behavior)hakkında daha fazla bilgi edinin.
