---
title: MARS Aracısı yedeklemelerini yönetme ve izleme
description: Azure Backup hizmetini kullanarak Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı yedeklemelerini yönetme ve izleme hakkında bilgi edinin.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 4306f01d608542f7453b32b32a1a6894c2379159
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515031"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Azure Backup hizmetini kullanarak Microsoft Azure Kurtarma Hizmetleri (MARS) aracı yedeklemelerini yönetme

Bu makalede, Microsoft Azure Kurtarma Hizmetleri aracısıyla yedeklenen dosya ve klasörlerin nasıl yönetileceği açıklanır.

## <a name="modify-a-backup-policy"></a>Yedekleme ilkesini değiştirme

Yedekleme ilkesini değiştirirken, yeni öğeler ekleyebilir, var olan öğeleri yedekten kaldırabilir veya dışlama ayarları kullanılarak yedeklenen dosyaları dışarıda bırakabilirsiniz.

- **Öğe Ekle** bu seçeneği yalnızca yedeklenecek yeni öğeler eklemek için kullanın. Varolan öğeleri kaldırmak için **öğeleri kaldır** veya **dışlama ayarları** seçeneğini kullanın.  
- **Öğeleri kaldır** öğeleri yedeklenmeden kaldırmak için bu seçeneği kullanın.
  - **Öğeleri kaldırmak** yerine bir birim içindeki tüm öğeleri kaldırmak Için **dışlama ayarlarını** kullanın.
  - Bir birimdeki tüm seçimlerin temizlenmesi, öğelerin eski yedeklerinin, son yedekleme sırasında, değişiklik için kapsam olmadan saklama ayarlarına göre korunmasına neden olur.
  - Bu öğeleri yeniden seçtiğinizde, ilk tam yedekleme ve yeni ilke değişiklikleri eski yedeklemelere uygulanmaz.
  - Tüm birim seçimini kaldırmak, bekletme ilkesini değiştirmek için herhangi bir kapsam olmadan son yedeklemeyi korur.
- **Dışlama ayarları** belirli öğelerin yedeklenmesini hariç tutmak için bu seçeneği kullanın.

### <a name="add-new-items-to-existing-policy"></a>Varolan ilkeye yeni öğe ekleme

1. **Eylemler**' de, **yedeklemeyi zamanla**' yı seçin.

    ![Windows Server yedeklemesini zamanlama](./media/backup-configure-vault/schedule-first-backup.png)

2. **İlke öğesi Seç** sekmesinde, **dosyalarınız ve klasörleriniz Için Yedekleme zamanlamasını değiştir** ' i seçin ve ileri ' **yi** seçin.

    ![Ilke öğelerini seçin](./media/backup-azure-manage-mars/select-policy-items.png)

3. **Zamanlamayı yedeklemeyi Değiştir veya Durdur** sekmesinde **yedekleme öğelerinde veya saatlerde değişiklik yap** ' ı seçin ve ileri ' **yi** seçin.

    ![Yedeklemeyi değiştirin veya zamanlayın](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Yedeklenecek **öğeleri seçin** sekmesinde, yedeklemek istediğiniz öğeleri eklemek Için **öğe Ekle** ' yi seçin.

    ![Yedekleme öğesi ekleme veya zamanlama](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. **Öğeleri seç** penceresinde, eklemek istediğiniz dosya veya klasörleri seçin ve **Tamam**' ı seçin.

    ![Öğeleri seçin](./media/backup-azure-manage-mars/select-item.png)

6. Sonraki adımları tamamlayarak işlemi tamamladıktan sonra **son** ' u seçin.

### <a name="add-exclusion-rules-to-existing-policy"></a>Mevcut ilkeye dışlama kuralları Ekle

Yedeklenmesini istemediğiniz dosya ve klasörleri atlamak için dışlama kuralları ekleyebilirsiniz. Bunu, yeni bir ilke tanımlarken veya var olan bir ilkeyi değiştirirken yapabilirsiniz.

1. Eylemler bölmesinden, **yedeklemeyi zamanla**' yı seçin. **Yedeklenecek öğeleri seçin** ' e gidin ve **dışlama ayarları**' nı seçin.

    ![Dışlama ayarları](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. **Dışlama ayarları**' nda **dışlama Ekle**' yi seçin.

    ![Dışlama Ekle](./media/backup-azure-manage-mars/add-exclusion.png)

3. **Hariç tutulacak öğeleri seçin** listesinden dosya ve klasörlere gözatıp dışlamak istediğiniz öğeleri seçin ve **Tamam**' ı seçin.

    ![Dışlanacak öğeleri seçin](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Varsayılan olarak, seçilen klasörlerin içindeki tüm **alt klasörler** hariç tutulur. Bunu, **Evet** veya **Hayır**' a tıklayarak değiştirebilirsiniz. Aşağıda gösterildiği gibi, dışlanacak dosya türlerini düzenleyebilir ve belirtebilirsiniz:

    ![Alt klasör türlerini seçin](./media/backup-azure-manage-mars/subfolders-type.png)

5. Sonraki adımları tamamlayarak işlemi tamamladıktan sonra **son** ' u seçin.

### <a name="remove-items-from-existing-policy"></a>Varolan ilkeden öğeleri kaldır

1. Eylemler bölmesinden, **yedeklemeyi zamanla**' yı seçin. **Yedeklenecek öğeleri seçin** bölümüne gidin. Listeden, yedekleme zamanlamadan kaldırmak istediğiniz dosya ve klasörleri seçin ve **öğeleri kaldır**' ı seçin.

    ![Kaldırılacak öğeleri seçin](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Bir birimi ilkeden tamamen kaldırdığınızda dikkatli ilerleyin.  Yeniden eklemeniz gerekiyorsa, yeni bir birim olarak kabul edilir. Sonraki zamanlanmış yedekleme, artımlı yedekleme yerine bir Ilk yedekleme (tam yedekleme) gerçekleştirir. Daha sonra öğeleri daha sonra kaldırmanız ve eklemeniz gerekiyorsa, tam yedekleme yerine artımlı yedekleme sağlamak için **öğeleri kaldırmak** yerine **Dışlamalar ayarlarının** kullanılması önerilir.

2. Sonraki adımları tamamlayarak işlemi tamamladıktan sonra **son** ' u seçin.

## <a name="stop-protecting-files-and-folder-backup"></a>Dosya ve klasör yedeklemesini korumayı durdur

Dosya ve klasör yedeklemesini korumayı durdurmanın iki yolu vardır:

- **Korumayı durdurun ve yedekleme verilerini koruyun**.
  - Bu seçenek, gelecekteki tüm yedekleme işlerini korumadan durdurur.
  - Azure Backup hizmet, var olan tüm kurtarma noktalarını tutmaya devam edecektir.  
  - Süre dolmamış kurtarma noktaları için yedeklenmiş verileri geri yükleyebileceksiniz.
  - Korumayı sürdürmeye karar verirseniz, *Yedekleme zamanlamasını yeniden etkinleştir* seçeneğini kullanabilirsiniz. Bundan sonra, veriler yeni bekletme ilkesine göre bekletilecektir.
- **Korumayı durdurun ve yedekleme verilerini silin**.
  - Bu seçenek, gelecekteki tüm yedekleme işlerinin verilerinizi korumasını durdurur ve tüm kurtarma noktalarını siler.
  - *Bu yedekleme öğesi için verileriniz silinmiş bir iletiyle birlikte yedekleme verilerini silme uyarısı e-postası alacaksınız. Bu veriler 14 gün boyunca geçici olarak kullanılabilir olacak, sonrasında kalıcı olarak silinecek* ve önerilen eylem, *verilerinizi kurtarmak için 14 gün içinde yedekleme öğesini yeniden koruacaktır.*
  - Korumayı sürdürmesini sağlamak için silme işleminden 14 gün içinde yeniden koruma yapın.

### <a name="stop-protection-and-retain-backup-data"></a>Korumayı durdurun ve yedekleme verilerini koruyun

1. MARS yönetim konsolunu açın, **eylemler bölmesine** gidin ve **yedeklemeyi zamanla**' yı seçin.

    ![Yedeklemeyi Zamanla ' yı seçin](./media/backup-azure-manage-mars/mars-actions.png)
1. **Ilke öğesi Seç** sayfasında **dosyalarınız ve klasörleriniz için bir yedekleme zamanlaması Değiştir** ' i seçin ve **İleri ' yi** seçin.

    ![İlke öğesi seçin](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. **Zamanlanmış bir yedeklemeyi Değiştir veya Durdur** sayfasında, **Bu yedekleme zamanlamasını kullanmayı Durdur ' u seçin, ancak bir zamanlama yeniden etkinleştirilinceye kadar depolanan yedeklemeleri saklayın**. Ardından **İleri**' yi seçin.

    ![Zamanlanmış bir yedeklemeyi durdurun.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. **Zamanlanan yedeklemeyi Duraklat** bölümünde, bilgileri gözden geçirin ve **son**' u seçin.

    ![Zamanlanmış bir yedeklemeyi duraklatın.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. **Yedekleme Işlemini değiştirme** sırasında, zaman çizelgenize yedekleme duraklamasını denetleyin ve **çıkmak için Kapat** ' ı seçin.

### <a name="stop-protection-and-delete-backup-data"></a>Korumayı Durdur ve yedekleme verilerini sil

1. MARS yönetim konsolunu açın, **Eylemler** bölmesine gidin ve **yedeklemeyi zamanla**' yı seçin.
2. **Zamanlanmış bir yedeklemeyi Değiştir veya Durdur** sayfasında, **Bu yedekleme zamanlamasını kullanarak Durdur ' u seçin ve tüm depolanan yedeklemeleri silin**. Ardından **İleri**' yi seçin.

    ![Zamanlanmış bir yedeklemeyi değiştirin veya durdurun.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. **Zamanlanan yedeklemeyi durdur** sayfasında **son**' u seçin.

    ![Zamanlanmış bir yedeklemeyi durdurup son ' u seçin](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. El ile oluşturmanız gereken bir güvenlik PIN 'ı (kişisel kimlik numarası) girmeniz istenir. Bunu yapmak için öncelikle Azure portal oturum açın.
5. **Kurtarma Hizmetleri Kasası**  >  **ayarları**  >  **özellikleri**' ne gidin.
6. **GÜVENLIK PIN**'ı altında **Oluştur**' u seçin. Bu PIN 'ı kopyalayın. PIN yalnızca beş dakika için geçerlidir.
7. Yönetim konsolunda PIN 'i yapıştırın ve **Tamam**' ı seçin.

    ![Güvenlik PIN 'ı oluştur.](./media/backup-azure-delete-vault/security-pin.png)

8. **Yedekleme Ilerlemesini Değiştir** sayfasında aşağıdaki ileti görüntülenir: *silinen yedekleme verileri 14 gün boyunca saklanacaktır. Bu süreden sonra, yedekleme verileri kalıcı olarak silinir.*  

    ![Yedekleme ilerlemesini değiştirme](./media/backup-azure-delete-vault/deleted-backup-data.png)

Şirket içi yedekleme öğelerini sildikten sonra, portaldan sonraki adımları izleyin.

## <a name="re-enable-protection"></a>Korumayı yeniden etkinleştirin

Verileri korurken korumayı durdurduysanız ve korumayı sürdürmeye karar verdiyseniz Yedekleme zamanlamasını değiştir seçeneğini kullanarak yedeklemeyi yeniden etkinleştirebilirsiniz.

1. **Eylemler** üzerinde **yedeklemeyi zamanla**' yı seçin.
1. **Yedekleme zamanlamasını yeniden etkinleştir ' i seçin. Ayrıca, yedekleme öğelerini veya zamanları değiştirebilir** ve ileri ' **yi** seçebilirsiniz.<br>

    ![Yedekleme zamanlamasını yeniden etkinleştirin](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. **Yedeklenecek öğeleri seçin**' de, **İleri**' yi seçin.

    ![Yedeklenecek öğeleri seçin](./media/backup-azure-manage-mars/re-enable-next.png)
1. **Yedekleme zamanlamasını belirtin** bölümünde Yedekleme zamanlamasını belirtin ve **İleri**' yi seçin.
1. **Bekletme Ilkesi Seç** bölümünde, saklama süresini belirtin ve **İleri**' yi seçin.
1. Son olarak, **onay** ekranında, ilke ayrıntılarını gözden geçirin ve **son**' u seçin.

## <a name="re-generate-passphrase"></a>Parolayı yeniden oluştur

Bir parola, şirket içi veya yerel makinenizi, MARS aracısını veya Azure 'dan veya Azure 'dan yedekleme sırasında verileri şifrelemek ve şifrelerini çözmek için kullanılır. Parolayı kaybettiyseniz veya unuttuysanız, bu adımları izleyerek, parolayı yeniden oluşturabilirsiniz (makineniz hala kurtarma hizmetleri kasası ile kaydedilir ve yedekleme yapılandırılmışsa):

1. Mars Aracısı konsolundan **Eylemler bölmesi**  >  >**Özellikleri Değiştir** ' e gidin. Ardından **şifreleme sekmesine** gidin.<br>
1. **Parolayı Değiştir** onay kutusunu seçin.<br>
1. Yeni bir parola girin veya **parola oluştur**' u seçin.
1. Yeni parolayı kaydetmek için **Araştır** ' ı seçin.

    ![Parola oluştur.](./media/backup-azure-manage-mars/passphrase.png)

1. Değişiklikleri uygulamak için **Tamam ' ı** seçin.  Kurtarma Hizmetleri Kasası için Azure portal [güvenlik özelliği](./backup-azure-security-feature.md#enable-security-features) etkinleştirilmişse, güvenlik PIN 'ini girmeniz istenir. PIN 'i almak için bu [makalede](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)listelenen adımları izleyin.<br>
1. Portaldan güvenlik PIN 'ini yapıştırın ve değişiklikleri uygulamak için **Tamam** ' ı seçin.<br>

    ![Güvenlik PIN 'ini Yapıştır](./media/backup-azure-manage-mars/passphrase2.png)
1. Parolanın, Azure Key Vault tercihen farklı bir konuma (kaynak makineden başka) güvenli bir şekilde kaydedildiğinden emin olun. MARS aracılarıyla yedeklenmekte olan birden fazla makineniz varsa tüm parolaları izleyin.

## <a name="managing-backup-data-for-unavailable-machines"></a>Kullanılamayan makineler için yedekleme verilerini yönetme

Bu bölümde, kaynak makinenizin MARS ile korunan, bozuk, kötü amaçlı yazılım/fidye veya kullanımdan kaldırılan bir senaryo açıklanmaktadır.

Bu makineler için Azure Backup hizmeti, en son kurtarma noktasının, yedekleme ilkesinde belirtilen bekletme kurallarına göre sona ermemesini sağlar (yani ayıklanmaz). Bu nedenle, makineyi güvenli bir şekilde geri yükleyebilirsiniz.  Yedeklenen verilerde gerçekleştirebileceğiniz aşağıdaki senaryoları göz önünde bulundurun:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Senaryo 1: kaynak makine kullanılamıyor ve artık yedekleme verilerini tutmanız gerekmiyor

- Yedeklenen verileri, [Bu makalede](backup-azure-delete-vault.md#delete-protected-items-on-premises)listelenen adımları kullanarak Azure Portal silebilirsiniz.

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Senaryo 2: kaynak makine kullanılamıyor ve yedekleme verilerini tutmanız gerekiyor

MARS için yedekleme ilkesini yönetmek, Portal üzerinden değil MARS konsolu aracılığıyla yapılır. Mevcut kurtarma noktalarının bekletme ayarlarını, süresi dolmadan önce genişletmeniz gerekiyorsa, makineyi geri yüklemeniz, MARS konsolunu yüklemeniz ve ilkeyi genişletmeniz gerekir.

- Makineyi geri yüklemek için aşağıdaki adımları uygulayın:
  1. [VM 'yi alternatif bir hedef makineye geri yükleme](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Hedef Makineyi, kaynak makineyle aynı ana bilgisayar adına yeniden oluştur
  1. Aracıyı yükler ve aynı kasaya ve aynı parolayla yeniden kaydolun
  1. Saklama süresini gereksinimlerinize göre genişletmek için MARS istemcisini başlatın
- MARS ile korunan yeni geri yüklenen makineniz yedeklemeler almaya devam edecektir.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>MARS Aracısı için virüsten koruma yapılandırma

MARS aracısının işlemiyle ilgili çakışmaları önlemek için virüsten koruma yazılımınız için aşağıdaki yapılandırmayı öneririz.

1. **Yol dışlamaları ekleme**: performans ve olası çakışmaların azalmasına engel olmak için, aşağıdaki yolları virüsten koruma yazılımının gerçek zamanlı izlemelerinden hariç tutun:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` ve alt klasörler
    1. **Karalama klasörü**: karalama klasörü standart konumda değilse, bunları dışlamalarla birlikte ekleyin.  Karalama klasörü konumunu belirleme [adımları için buraya bakın](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-) .
1. **Ikili Dışlamalar ekleme**: yedekleme ve konsol etkinliklerinin azalmasına engel olmak için, aşağıdaki ikili dosyalar için işlemleri virüsten koruma yazılımının gerçek zamanlı izlemeden hariç tutun:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Bu yolların dışlanması birçok virüsten koruma yazılımı için yeterli olacaktır, ancak bazı işlemler MARS Aracısı işlemlerini kesintiye uğratabilecek olmaya devam edebilir. Beklenmeyen hatalardan karşılaşırsanız, geçici olarak virüsten koruma yazılımını kaldırın ve sorun olup olmadığını görmek için izleyin. Bu sorunu giderirse, ürünlerinin doğru yapılandırmasıyla ilgili yardım almak için virüsten koruma yazılımı satıcınıza başvurun.

## <a name="next-steps"></a>Sonraki adımlar

- Desteklenen senaryolar ve sınırlamalar hakkında daha fazla bilgi için, [Mars aracısının destek matrisine](./backup-support-matrix-mars-agent.md)bakın.
- [İsteğe bağlı yedekleme ilkesi saklama davranışı](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)hakkında daha fazla bilgi edinin.
- Daha sık sorulan sorular için bkz. [Mars ARACıSı SSS](backup-azure-file-folder-backup-faq.yml).
