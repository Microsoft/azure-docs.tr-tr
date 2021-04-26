---
title: PostgreSQL için Azure Veritabanı’nı Yedekleme
description: Uzun süreli saklama (Önizleme) ile PostgreSQL için Azure veritabanı yedeklemesi hakkında bilgi edinin
ms.topic: conceptual
ms.date: 04/12/2021
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 8fd69e016c7f0b175ef49b98add5692743858f62
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480078"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Uzun süreli saklama ile PostgreSQL için Azure veritabanı yedekleme (Önizleme)

Azure Backup ve Azure veritabanı Hizmetleri, yedeklemeleri 10 yıla kadar koruyan PostgreSQL sunucuları için Azure veritabanı için kurumsal sınıf bir yedekleme çözümü oluşturmak üzere bir araya geliyor.

Uzun süreli saklama 'nın yanı sıra, çözüm aşağıda listelenen birçok farklı özelliğe de sahiptir:

- Azure Active Directory ve Yönetilen Hizmet Kimliği (MSI) kimlik doğrulaması kullanılarak veritabanına Azure rol tabanlı erişim denetimi (Azure RBAC).
- Bireysel veritabanı düzeyinde müşteri denetimli zamanlanmış ve isteğe bağlı yedekleme.
- Veritabanı düzeyi herhangi bir Postgres sunucusuna veya doğrudan blob depolamaya geri yükler.
- Uzun vadeli bekletme.
- Tüm işlemler ve işlerin merkezi olarak izlenmesi.
- Yedeklemeler ayrı güvenlik ve hata etki alanlarında depolanır. Bu nedenle, kaynak sunucunun tehlikeye düşmesi veya hatta sonlandırılıp sonlandırılmadığı halde yedeklemeler, [yedekleme kasasında](backup-vault-overview.md)güvende kalır.
- **Pg_dump** kullanımı, geri yükleme sırasında daha fazla esneklik sağlar, böylece veritabanı sürümlerine geri yükleyebilir veya yedeklemenin bir kısmını geri yükleyebilirsiniz.

Bu çözümü, Azure PostgreSQL tarafından sunulan ve 35 güne kadar bekletme sağlayan yerel yedekleme çözümüne ek olarak bağımsız olarak kullanabilirsiniz. Yerel çözüm, en son yedeklerden kurtarmak istediğinizde olduğu gibi, işlemsel kurtarmalar için uygundur. Azure Backup çözümü, uyumluluk gereksinimlerinize ve daha ayrıntılı ve esnek yedekleme ve geri yükleme yapmanıza yardımcı olur.

## <a name="support-matrix"></a>Destek matrisi

|Destek  |Ayrıntılar  |
|---------|---------|
|Desteklenen dağıtımlar   |  [PostgreSQL için Azure Veritabanı - Tek Sunucu](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Desteklenen Azure bölgeleri |  Doğu ABD, Doğu ABD 2, Orta ABD, Orta Güney ABD, Batı ABD, Batı ABD 2, Orta Batı ABD, Brezilya Güney, Kanada Orta, Kuzey Avrupa, Batı Avrupa, UK Güney, UK Batı, Almanya Orta Batı, İsviçre Kuzey, İsviçre Batı, Doğu Asya, Güney Doğu Asya, Japonya Doğu, Japonya Batı, Kore orta, Kore Güney, Hindistan Orta, Avustralya Doğu, Avustralya Orta, Avustralya Orta 2, BAE Kuzey  |
|Desteklenen Azure PostgreSQL sürümleri    |   9,5, 9,6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Özellik konuları ve sınırlamaları

- Tüm işlemler yalnızca Azure portal desteklenir.
- En büyük veritabanı boyutu için Önerilen sınır 400 GB 'dir.
- Çapraz bölge yedeklemesi desteklenmez. Bu, bir Azure PostgreSQL sunucusunu başka bir bölgedeki kasaya yedekleyemeyeceğiniz anlamına gelir. Benzer şekilde, bir yedeği yalnızca kasadaki aynı bölgedeki bir sunucuya geri yükleyebilirsiniz.
- Yalnızca veriler geri yükleme sırasında kurtarılır. "Roller" geri yüklenmedi.
- Önizleme aşamasında, çözümü yalnızca test ortamınızda çalıştırmanızı öneririz.

## <a name="backup-process"></a>Yedekleme işlemi

1. Bu çözüm, Azure PostgreSQL veritabanlarınızın yedeklerini almak için **pg_dump** kullanır.

2. Yedeklemek istediğiniz Azure PostgreSQL veritabanlarını belirttiğinizde, hizmet, sunucuda ve veritabanında yedekleme işlemini gerçekleştirmek için doğru izinlere sahip olup olmadığını doğrular.

3. Azure Backup, bir yedekleme uzantısı yüklenmiş bir çalışan rolü yukarı döner. Bu uzantı, Postgres sunucusuyla iletişim kurar.

4. Bu uzantı, bir düzenleyici ve Azure Postgres eklentisi içerir. Düzenleyici, yedekleme, yedekleme ve geri yükleme yapılandırma gibi çeşitli işlemler için iş akışlarını tetiklemeden sorumludur. Bu, gerçek veri akışından eklenti sorumludur.
  
5. Seçili veritabanlarında korumayı yapılandırma tetikledikten sonra, yedekleme hizmeti düzenleyiciyi yedekleme zamanlamaları ve diğer ilke ayrıntıları ile ayarlar.

6. Zamanlanan zamanda, düzenleyici eklenti ile iletişim kurar ve **pg_dump** kullanarak Postgres sunucusundan yedekleme verilerini akışa başlar.

7. Eklenti, verileri doğrudan yedekleme kasasına gönderir ve bir hazırlama konumu gereksinimini ortadan kaldırır. Veriler, Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir ve depolama hesaplarında Azure Backup hizmeti tarafından depolanır.

8. Veri aktarımı tamamlandığında, düzenleyici yedekleme hizmeti ile yürütmeyi onaylar.

    ![Yedekleme işlemi](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Azure PostgreSQL veritabanlarında yedeklemeyi yapılandırma

Birden çok Azure PostgreSQL sunucusu üzerinde birden fazla veritabanında yedekleme yapılandırabilirsiniz. Hizmet tarafından Postgres sunucularını yedeklemek için gereken [Önkoşul izinlerinin](#prerequisite-permissions-for-configure-backup-and-restore) zaten yapılandırılmış olduğundan emin olun.

Aşağıdaki yönergeler, Azure PostgreSQL veritabanlarında Azure Backup kullanarak yedeklemeyi yapılandırmaya yönelik adım adım kılavuzlardır:

1. İşlemi başlatmak için iki yol vardır:

    1. [Yedekleme merkezi](backup-center-overview.md)  ->  **'ne genel bakış**  ->  **yedeklemesi**' ne gidin.

        ![Yedekleme merkezi 'ne git](./media/backup-azure-database-postgresql/backup-center.png)

        **Başlatma: yedeklemeyi yapılandırma** altında, **PostgreSQL için Azure veritabanı** olarak **DataSource türünü** seçin.

        ![Başlat: yedeklemeyi yapılandırma bölümünde DataSource türü ' nü seçin.](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. Alternatif olarak, [yedekleme](backup-vault-overview.md)kasalarını  ->  **yedeklemeye** doğrudan gidebilirsiniz.

        ![Yedekleme kasalarına git](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Yedekleme kasasında yedekleme seçin](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. **Yedeklemeyi Yapılandır** altında, Postgres veritabanlarınızı yedeklemek istediğiniz **yedekleme kasasını** seçin. Zaten kasa bağlamında olduğunuzda bu bilgiler önceden doldurulur.

    ![Yedeklemeyi Yapılandır bölümünde Yedekleme Kasası 'nı seçin](./media/backup-azure-database-postgresql/configure-backup.png)

1. Bir **yedekleme ilkesi** seçin veya oluşturun.

    ![Yedekleme ilkesi seçin](./media/backup-azure-database-postgresql/backup-policy.png)

1. Yedeklenecek kaynakları veya Postgres veritabanlarını seçin.

    ![Yedeklenecek kaynakları seçin](./media/backup-azure-database-postgresql/select-resources.png)

1. Tüm Azure PostgreSQL sunucularının listesinden, kasala aynı bölgedeyse, abonelikler arasında seçim yapabilirsiniz. Bir sunucu içindeki veritabanlarının listesini görmek için oku genişletin.

    ![Sunucuları seçin](./media/backup-azure-database-postgresql/choose-servers.png)

1. Hizmet, kasanın seçili Postgres sunucularını ve veritabanlarını yedekleme izinlerine sahip olup olmadığını doğrulamak için seçili veritabanlarında bu denetimleri çalıştırır.
    1. Devam etmek için tüm veritabanları için **yedekleme hazırlığı** **başarılı** olmalıdır.
    1. Bir hata varsa, hatayı **düzeltin** ve veritabanını **yeniden doğrulayın** ya da seçimleri kaldırın.

    ![Düzeltilme doğrulama hataları](./media/backup-azure-database-postgresql/validation-errors.png)

1. **İnceleme ve yapılandırma** bölümündeki tüm ayrıntıları onaylayın ve işlemi göndermek Için **yedeklemeyi Yapılandır** ' ı seçin.

    ![Inceleme ve yapılandırma ile ayrıntıları onaylayın](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Tetiklendikten sonra, **yedekleme yapılandırma** işlemi bir yedekleme örneği oluşturacaktır. Yedekleme merkezi veya kasa görünümündeki [yedekleme örnekleri](backup-center-monitor-operate.md#backup-instances) bölmesi altında işlemin durumunu izleyebilirsiniz.

    ![Yedekleme örnekleri](./media/backup-azure-database-postgresql/backup-instances.png)

1. Yedeklemeler, ilkede tanımlanan yedekleme zamanlamalarına göre tetiklenir. İşler [yedekleme işleri](backup-center-monitor-operate.md#backup-jobs)altında izlenebilir. Şu anda, son yedi güne ait işleri görüntüleyebilirsiniz.

    ![Yedekleme işleri](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Yedekleme İlkesi Oluştur

1. **Yedekleme merkezi**  ->  **yedekleme ilkeleri**  ->  **Ekle**' ye gidin. Alternatif olarak, **Yedekleme Kasası**  ->  **yedekleme ilkesi**  ->  **Ekle**' ye gidebilirsiniz.

    ![Yedekleme İlkesi Ekle](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Yeni ilke için bir **ad** girin.

    ![İlke adını girin](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Yedekleme zamanlamasını tanımlayın. Şu anda **haftalık** yedeklemeyi destekliyoruz. Yedeklemeleri haftanın bir veya daha fazla gününde zamanlayabilirsiniz.

    ![Yedekleme zamanlamasını tanımlama](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. **Bekletme** ayarlarını tanımlayın. Bir veya daha fazla bekletme kuralı ekleyebilirsiniz. Her bekletme kuralı belirli yedeklemeler için girişleri ve bu yedeklemeler için veri depolama ve bekletme süresini varsayar.

1. Yedeklemelerinizi iki veri deposundan (veya katmanda) birinde depolamayı seçebilirsiniz: **yedekleme veri deposu** (Standart katman) veya **Arşiv veri deposu** (önizlemede).

   Yedekleme veri deposundaki süresi dolduktan sonra yedeklemeyi Arşiv veri deposuna taşımak için **süre sonu** seçeneğini belirleyebilirsiniz.

1. **Varsayılan bekletme kuralı** , başka bir bekletme kuralı yokluğunda uygulanır ve varsayılan değer üç aydan oluşur.

    - Saklama süresi, **yedekleme veri deposundaki** yedi günden 10 yıla kadar değişir.
    - Bekletme süresi, **Arşiv veri deposundaki** altı aydan 10 yıla kadar değişir.

    ![Saklama süresini Düzenle](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>Saklama kuralları önceden belirlenmiş öncelik sırasıyla değerlendirilir. Öncelik **yıllık** bir kural için en yüksektir, ardından **aylık** ve sonra **haftalık** kural. Varsayılan bekletme ayarları, başka hiçbir kural uygun olmadığında uygulanır. Örneğin, aynı kurtarma noktası her hafta alınan ilk başarılı yedeklemenin yanı sıra her ay ilk başarılı yedeklemenin de olabilir. Ancak, aylık kural önceliği haftalık kuraldan daha yüksek olduğundan, her ay uygulanan ilk başarılı yedeklemeye karşılık gelen bekletme geçerlidir.

## <a name="restore"></a>Geri Yükleme

Hizmeti hedef sunucuda uygun izin kümesine sahipse, bir veritabanını aynı abonelik içinde herhangi bir Azure PostgreSQL sunucusuna geri yükleyebilirsiniz. Hizmet tarafından Postgres sunucularının yedeklenme için gereken [Önkoşul izinlerinin](#prerequisite-permissions-for-configure-backup-and-restore) zaten yapılandırılmış olduğundan emin olun.

Geri yükleme tetiklemeye yönelik bu adım adım kılavuzu izleyin:

1. Geri yükleme işlemini başlatmak için iki yol vardır:
    1. [Yedekleme merkezi](backup-center-overview.md)  ->  **'ne genel bakış**  ->  **geri yükleme** bölümüne gidin.

    ![Yedekleme merkezinde geri yükle ' yi seçin](./media/backup-azure-database-postgresql/backup-center-restore.png)

    **Başlat: geri yükle** bölümünde, **PostgreSQL için Azure veritabanı** olarak **DataSource türünü** seçin. **Yedekleme örneğini** seçin.

    ![Başlatma içinde DataSource türünü seçin: geri yükle](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. Alternatif olarak, **Yedekleme Kasası**  ->  **yedekleme örneklerine** doğrudan gidebilirsiniz. Geri yüklemek istediğiniz veritabanına karşılık gelen **yedekleme örneğini** seçin.

    ![Geri yükleme için yedekleme örnekleri](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Yedekleme örneklerinin listesi](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Geri yükle 'Yi seçin](./media/backup-azure-database-postgresql/select-restore.png)

1. Seçili yedekleme örneği için kullanılabilir tüm tam yedeklemeler listesinden **Kurtarma noktası ' nı seçin** . Varsayılan olarak, en son kurtarma noktası seçilidir.

    ![Kurtarma noktası seçin](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Kurtarma noktalarının listesi](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. Giriş **geri yükleme parametreleri**. Bu noktada, iki tür geri yükleme arasından seçim yapabilirsiniz: **veritabanı olarak geri yükle** ve **dosya olarak geri** yükle.

1. **Veritabanı olarak geri yükle**: hedef PostgreSQL sunucusunda yeni bir veritabanı oluşturmak için yedekleme verilerini geri yükleyin.

    - Hedef sunucu, kaynak sunucuyla aynı olabilir. Ancak, özgün veritabanının üzerine yazılması desteklenmez.
    - Tüm abonelikler genelinde sunucudan, ancak kasala aynı bölgede seçim yapabilirsiniz.
    - **Gözden geçir + geri yükle**' yi seçin. Bu işlem, hizmetin hedef sunucuda uygun geri yükleme izinlerine sahip olup olmadığını denetlemek için doğrulamayı tetikler.

    ![Veritabanı olarak geri yükle](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Dosya olarak geri yükle**: yedekleme dosyalarını hedef depolama hesabına (blob 'lar) dökümünü alın.

    - Tüm aboneliklerdeki depolama hesaplarından, ancak kasala aynı bölgede seçim yapabilirsiniz.
    - Seçili depolama hesabı için filtrelenmiş kapsayıcı listesinden hedef kapsayıcıyı seçin.
    - **Gözden geçir + geri yükle**' yi seçin. Bu işlem, hizmetin hedef sunucuda uygun geri yükleme izinlerine sahip olup olmadığını denetlemek için doğrulamayı tetikler.

    ![Dosya olarak geri yükleme](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Kurtarma noktası arşiv katmanındaysa, geri yüklemeden önce kurtarma noktasını yeniden yazmanız gerekir.
   
   ![Yeniden doldurma ayarları](./media/backup-azure-database-postgresql/rehydration-settings.png)
   
   Yeniden doldurma için gereken aşağıdaki ek parametreleri sağlayın:
   - **Yeniden doldurma önceliği:** Varsayılan değer **standarttır**.
   - **Yeniden doldurma süresi:** Maksimum yeniden doldurma süresi 30 gündür ve en az yeniden doldurma süresi 10 gündür. Varsayılan değer **15**' tir.
   
   Kurtarma noktası, belirtilen yeniden doldurma süresi için **yedekleme veri deposunda** depolanır.


1. Bilgileri gözden geçirin ve **geri yükle**' yi seçin. Bu, **yedekleme işleri** altında Izlenebilecek Ilgili geri yükleme işini tetikler.

>[!NOTE]
>PostgreSQL için Azure veritabanı için Arşiv desteği sınırlı genel önizlemede.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Yedekleme ve geri yükleme yapılandırma için önkoşul izinleri

Azure Backup katı güvenlik yönergelerini izler. Yerel bir Azure hizmeti™ olsa da, kaynak üzerindeki izinler kabul edilmez ve Kullanıcı tarafından açıkça verilmesi gerekir.  Benzer şekilde, veritabanına bağlanmak için kimlik bilgileri depolanmaz. Verilerinizi korumak için bu önemlidir. Bunun yerine Azure Active Directory kimlik doğrulaması kullanırız.

Otomatikleştirilmiş bir betik ve ilgili yönergeleri almak için [Bu belgeyi indirin](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) . Yedekleme ve geri yükleme için Azure PostgreSQL sunucusuna uygun bir izin kümesi sağlar.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Yedeklenen Azure PostgreSQL veritabanlarını yönetme

**Yedekleme örneklerinde** gerçekleştirebileceğiniz yönetim işlemleri şunlardır:

### <a name="on-demand-backup"></a>İsteğe bağlı yedekleme

İlkede belirtilen zamanlamada değil bir yedeklemeyi tetiklemek için **yedekleme örnekleri** yedeklemesi ' ne  ->  **Şimdi Yedekle**' ye gidin.
İlişkili yedekleme ilkesinde tanımlanan bekletme kuralları listesinden seçin.

![Yedeklemeyi şimdi Tetikle](./media/backup-azure-database-postgresql/backup-now.png)

![Bekletme kuralları listesinden seçin](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Korumayı durdurma

Bir yedekleme öğesinde korumayı durdurabilirsiniz. Bu, bu yedekleme öğesi için ilişkili kurtarma noktalarını da siler. Kurtarma noktaları en az altı aya ait arşiv katmanında değilse, bu kurtarma noktalarının silinmesi erken silme maliyetine neden olur. Mevcut kurtarma noktalarını korurken korumayı Durdur seçeneğini henüz sağlamayız.

![Korumayı durdurma](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>İlkeyi Değiştir

İlişkili ilkeyi bir yedekleme örneğiyle değiştirebilirsiniz.

1. **Yedekleme örneği**  ->  **değişiklik ilkesini** seçin.

    ![İlkeyi Değiştir](./media/backup-azure-database-postgresql/change-policy.png)

1. Veritabanına uygulamak istediğiniz yeni ilkeyi seçin.

    ![İlkeyi yeniden ata](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Sorun giderme

Bu bölüm, Azure Backup ile Azure PostgreSQL veritabanlarını yedeklemeye yönelik sorun giderme bilgilerini sağlar.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Yedeklemek veya geri yüklemek istediğiniz PG sunucusuna Yedekleme Kasası MSI **okuma** erişimi verin.

PostgreSQL veritabanına güvenli bağlantı kurmak için Azure Backup [yönetilen hizmet kimliği (MSI)](../active-directory/managed-identities-azure-resources/overview.md) kimlik doğrulama modelini kullanır. Bu, yedekleme kasasının yalnızca Kullanıcı tarafından açıkça izin verilen kaynaklara erişim sahibi olacağı anlamına gelir.

Bir sistem MSI, oluşturma sırasında kasaya otomatik olarak atanır. Bu kasaya, veritabanlarını yedeklemek istediğiniz PostgreSQL sunucularına erişim sağlamanız gerekir.

Adımlar:

1. Postgres sunucusunda **Access Control (IAM)** bölmesine gidin.

    ![Access Control bölmesi](./media/backup-azure-database-postgresql/access-control-pane.png)

1. **Rol atamaları Ekle**' yi seçin.

    ![Rol ataması ekle](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. Açılan sağ bağlam bölmesinde, aşağıdakileri girin:<br>

   - **Rol:** Açılan listeden **okuyucu** rolünü seçin.<br>
   - **Erişim ata:** Açılan listede **Kullanıcı, Grup veya hizmet sorumlusu** seçeneğini belirleyin.<br>
   - Şunu **seçin:** Bu sunucuyu ve veritabanlarını yedeklemek istediğiniz yedekleme Kasası adını girin.<br>

    ![Rol seçin](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Azure Active Directory kimlik doğrulayabilecek bir veritabanı yedekleme kullanıcısı oluşturun:

Bu hata, PostgreSQL sunucusu için bir Azure Active Directory yöneticisi veya Azure Active Directory kullanarak kimlik doğrulayabilecek bir yedekleme kullanıcısının yokluğuna gelebilir.

Adımlar:

OSS sunucusuna bir Active Directory Yöneticisi ekleyin:

Bu adım, bir parola yerine Azure Active Directory kimlik doğrulayabilecek bir kullanıcı aracılığıyla veritabanına bağlanmak için gereklidir. PostgreSQL için Azure veritabanı 'nda Azure AD yönetici kullanıcısının rolü **azure_ad_admin** olacaktır. Yalnızca bir **azure_ad_admin** rolü, Azure AD ile kimlik doğrulayabilecek yeni veritabanı kullanıcıları oluşturabilir.

1. Sunucu görünümünün sol gezinti bölmesindeki Active Directory yönetici sekmesine gidin ve Active Directory yöneticisi olarak kendinize (veya başka bir kişi) ekleyin.

    ![Active Directory yöneticisini ayarla](./media/backup-azure-database-postgresql/set-admin.png)

1. AD yöneticisi Kullanıcı ayarını **kaydettiğinizden** emin olun.

    ![Yönetici Kullanıcı ayarını Active Directory Kaydet](./media/backup-azure-database-postgresql/save-admin-setting.png)

İzin verme adımlarını tamamlamak için gerçekleştirmeniz gereken adımların listesi için [Bu belgeye](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) başvurun.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Sunucu görünümünde **Azure hizmetlerine erişime Izin ver** bayrağını etkinleştirerek ağ görüşbir hattını oluşturun. Sunucu görünümünde, **bağlantı güvenliği** bölmesi altında, **Azure hizmetlerine erişime Izin ver** bayrağını **Evet** olarak ayarlayın.

![Azure hizmetlerine erişim izni verme](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>Usererrorcontainernotaccesi

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Dosya olarak geri yüklerken bir depolama hesabı kapsayıcısına geri yükleme izni

1. Yedekleme Kasası MSI ' yı Azure portal kullanarak depolama hesabı kapsayıcılarına erişme iznini verin.
    1.   ->    ->  **Rol ataması eklemek** Access Control depolama hesabına gidin.
    1. **Depolama Blobu veri katılımcısı** rolünü, MSI yedekleme kasasına atayın.

    ![Depolama Blobu veri katılımcısı rolü ata](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Alternatif olarak, geri yüklemekte olduğunuz belirli kapsayıcıya, Azure CLı [az role atama Create](/cli/azure/role/assignment) komutunu kullanarak ayrıntılı izinler verin.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Atane parametresini, kasanın MSI **uygulama kimliğiyle** ve belirli kapsayıcınıza başvuracak kapsam parametresi ile değiştirin.
    1. Kasanın MSI **uygulama kimliğini** almak için **uygulama türü** altındaki **tüm uygulamalar** ' ı seçin:

        ![Tüm uygulamaları seçin](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Kasa adını arayın ve uygulama KIMLIĞINI kopyalayın:

        ![Kasa adı ara](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Yedekleme kasaları genel bakış](backup-vault-overview.md)
