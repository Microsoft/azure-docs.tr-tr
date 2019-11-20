---
title: Power BI raporlarını yapılandırma
description: Kurtarma Hizmetleri kasasını kullanarak Azure Backup için Power BI raporlarını yapılandırın.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 6e8482aea4f1d757550d4490e3a9972f664729c9
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173200"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup raporlarını yapılandırma

Bu makalede, bir kurtarma hizmetleri Kasası kullanarak Azure Backup raporlarını yapılandırmak için izlenecek adımlar gösterilmektedir. Ayrıca, Power BI kullanarak raporlara nasıl erişdeğiştirileceğini gösterir. Bu adımları tamamladıktan sonra, raporları görüntülemek, özelleştirmek ve oluşturmak için doğrudan Power BI gidebilirsiniz.

> [!IMPORTANT]
> 1 Kasım 2018 tarihinden itibaren bazı müşteriler Power BI'da Azure Backup uygulaması verilerini yükleme sırasında sorun yaşayabilir ve "JSON girişinin sonunda fazladan karakterler bulduk. Özel durum, IDataReader arabirimi tarafından tetiklendi." iletisiyle karşılaşabilir.
Bunun nedeni, verilerin depolama hesabına yüklenmesi sırasında kullanılan biçimin değiştirilmesidir.
Bu sorundan kaçınmak için lütfen en son uygulamayı (sürüm 1,8) indirin.
>
>

## <a name="supported-scenarios"></a>Desteklenen senaryolar

- Azure Backup raporlar, Azure kurtarma hizmetleri Aracısı kullanılarak Azure sanal makine yedeklemesi ve buluta dosya ve klasör yedeklemesi için desteklenir.
- Azure SQL veritabanı, Azure dosya paylaşımları, Data Protection Manager ve Azure Backup sunucusu raporları Şu anda desteklenmiyor.
- Kasaların her biri için aynı depolama hesabı yapılandırılmışsa raporları kasaların ve aboneliklerde görüntüleyebilirsiniz. Seçilen depolama hesabı, kurtarma hizmetleri kasasıyla aynı bölgede olmalıdır.
- Raporlar için zamanlanmış yenileme sıklığı Power BI 24 saattir. Ayrıca Power BI raporların talep üzerine yenilenmesini gerçekleştirebilirsiniz. Bu durumda, raporları işlemek için müşteri depolama hesabındaki en son veriler kullanılır.

## <a name="prerequisites"></a>Önkoşullar

- Raporları yapılandırmak için bir [Azure depolama hesabı](../storage/common/storage-quickstart-create-account.md) oluşturun. Bu depolama hesabı, raporla ilgili verileri depolamak için kullanılır.
- Power BI portalını kullanarak kendi raporlarınızı görüntülemek, özelleştirmek ve oluşturmak için [bir Power BI hesabı oluşturun](https://powerbi.microsoft.com/landing/signin/) .
- Henüz kayıtlı değilse, **Microsoft. Insights**kaynak sağlayıcısını kaydedin. Raporlama verilerinin depolama hesabına akabilmesi için depolama hesabı ve kurtarma hizmetleri Kasası aboneliklerini kullanın. Bu adımı yapmak için Azure portal gidin, **abonelik** > **kaynak sağlayıcıları**' nı seçin ve bu sağlayıcıyı kaydetmek için denetleyin.

## <a name="configure-storage-account-for-reports"></a>Raporlar için depolama hesabını yapılandırma

Azure portal kullanarak bir kurtarma hizmetleri Kasası için depolama hesabı yapılandırmak için aşağıdaki adımları izleyin. Bu, tek seferlik bir yapılandırmadır. Depolama hesabı yapılandırıldıktan sonra, şablon uygulamasını görüntülemek ve raporları kullanmak için doğrudan Power BI gidebilirsiniz.

1. Zaten açık bir kurtarma hizmetleri Kasası varsa, sonraki adıma gidin. Açık bir kurtarma hizmetleri Kasası yoksa, Azure portal, **tüm hizmetler**' i seçin.

   - Kaynak listesinde **Kurtarma Hizmetleri**' ni girin.
   - Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Kurtarma Hizmetleri kasalarını**gördüğünüzde, bunu seçin.
   - Kurtarma Hizmetleri kasalarının listesi görünür. Kurtarma Hizmetleri kasalarının listesinden bir kasa seçin.

     Seçilen kasa panosu açılır.
2. Kasa altında görünen öğelerin listesinden, **izleme ve raporlar** bölümünde, raporlar için depolama hesabını yapılandırmak üzere **yedekleme raporları** ' nı seçin.

      ![Yedekleme raporları menü öğesini seç adım 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. **Yedekleme raporları** dikey penceresinde **Tanılama ayarları** bağlantısı ' nı seçin. Bu bağlantı, bir müşteri depolama hesabına veri göndermek için kullanılan **Tanılama ayarları** Kullanıcı arabirimini açar.

      ![Tanılamayı etkinleştir adım 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Bir depolama hesabı yapılandırmak için kullanılacak kullanıcı arabirimini açmak üzere **tanılamayı aç '** ı seçin.

      ![Tanılamayı aç 4. adım](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. **Ad** kutusuna bir ayar adı girin. Raporlama verilerinin depolama hesabına akmasını başlatabilmesi **için depolama hesabına Arşivle** onay kutusunu seçin.

      ![Tanılamayı etkinleştir adım 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. **Depolama hesabı**' nı seçin, raporlama verilerini depolamak için listeden ilgili aboneliği ve depolama hesabını seçin ve **Tamam**' ı seçin.

      ![Depolama hesabı seçme adımı 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. **Günlük** bölümünde **AzureBackupReport** onay kutusunu seçin. Bu raporlama verileri için bir bekletme süresi seçmek için kaydırıcıyı taşıyın. Depolama hesabındaki raporlama verileri, Bu kaydırıcı kullanılarak seçilen dönem için tutulur.

      ![Depolama hesabı kaydetme adımı 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Tüm değişiklikleri gözden geçirin ve **Kaydet**' i seçin. Bu eylem, tüm değişikliklerinizin kaydedilmesini ve depolama hesabının artık raporlama verilerini depolamak için yapılandırılmış olmasını sağlar.

9. **Tanılama ayarları** tablosu artık kasa için etkinleştirilen yeni ayarı gösterir. Görünmüyorsa, güncelleştirilmiş ayarı görmek için tabloyu yenileyin.

      ![Tanılama ayarını görüntüle adım 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Depolama hesabını kaydederek raporları yapılandırdıktan sonra, ilk veri gönderimi işleminin bitmesini *24 saat bekleyin* . Azure Backup uygulamayı Power BI yalnızca o zamandan sonra içeri aktarın. Daha fazla bilgi için [SSS bölümüne](backup-azure-monitor-alert-faq.md)bakın.
>
>

## <a name="view-reports-in-power-bi"></a>Power BI raporları görüntüleme

Bir kurtarma hizmetleri Kasası kullanarak raporlar için bir depolama hesabı yapılandırdıktan sonra, raporlama verilerinin ' de akışa başlaması yaklaşık 24 saat sürer. Bir depolama hesabı ayarlamanın 24 saat sonra, Power BI raporları görüntülemek için aşağıdaki adımları izleyin.
Raporu özelleştirmek ve paylaşmak istiyorsanız, bir çalışma alanı oluşturun ve aşağıdaki adımları uygulayın

1. Power BI [oturum açın](https://powerbi.microsoft.com/landing/signin/) .
2. **Microsoft Appsource 'Tan daha fazla uygulama almak > uygulamalar**'a gidin. [Bir hizmete bağlanmak için Power BI belgelerindeki](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/)adımları izleyin.

3. **Arama** çubuğuna **Azure Backup** girin ve **Şimdi al**' ı seçin.

      ![Şablon uygulamasını al](./media/backup-azure-configure-reports/template-app-get.png)
4. Önceki 5. adımda yapılandırılan depolama hesabının adını girin ve **İleri**' yi seçin.

    ![Depolama hesabı adını girin](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)
5. "Key" kimlik doğrulama yöntemini kullanarak bu depolama hesabı için depolama hesabı anahtarını girin. [Depolama erişim anahtarlarını görüntülemek ve kopyalamak](../storage/common/storage-account-manage.md#access-keys)için Azure Portal depolama hesabınıza gidin.

     ![Depolama hesabı girin](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. **Oturum aç '** ı seçin. Oturum açma başarılı olduktan sonra bir veri alma bildirimi görürsünüz.

    ![İçerik paketi içeri aktarılıyor](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    İçeri aktarma işlemi tamamlandıktan sonra **başarı** bildirimi görürsünüz. Depolama hesabındaki veri miktarı büyükse, şablon uygulamasını içeri aktarmak biraz daha uzun sürebilir.

    ![Başarılı içerik paketini içeri aktar](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Veriler başarıyla içeri aktarıldıktan sonra, **Azure Backup** şablonu uygulaması gezinti bölmesindeki **uygulamalarda** görünür. **Panolar**, **raporlar**ve **veri kümeleri**bölümünde listede artık Azure Backup gösterilmektedir.

8. **Panolar**altında, bir sabitlenmiş anahtar raporları kümesini gösteren **Azure Backup**' yi seçin.

      ![Azure Backup panosu](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Tüm rapor kümesini görüntülemek için panodaki herhangi bir raporu seçin.

      ![Azure Backup iş durumu](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Raporlardaki her sekmeyi, bu alandaki raporları görüntülemek için seçin.

      ![Azure Backup raporları sekmeleri](./media/backup-azure-configure-reports/reports-tab-view.png)

## <a name="troubleshooting-errors"></a>Hatalarda sorun giderme

| Hata Ayrıntıları | Çözüm |
| --- | --- |
| Yedekleme raporlarının depolama hesabını ayarladıktan sonra, **depolama hesabı** hala **Yapılandırılmadı**' ı gösterir. | Bir depolama hesabını başarıyla yapılandırdıysanız, raporlama verileriniz Bu soruna karşın içinde akar. Bu sorunu çözmek için Azure portal gidin ve **tüm hizmetler** > **Tanılama ayarları** > **Kurtarma Hizmetleri Kasası** > **düzenleme ayarı**' nı seçin. Daha önce yapılandırılan ayarı silin ve aynı dikey pencerede yeni bir ayar oluşturun. Bu kez, **ad** kutusunda **hizmet**' i seçin. Artık yapılandırılan depolama hesabı görüntülenir. |
|Power BI Azure Backup şablon uygulamasını içeri aktardıktan sonra, "404-Container bulunamadı" hata iletisi görüntülenir. | Daha önce belirtildiği gibi, kurtarma hizmetleri kasasındaki raporları yapılandırdıktan sonra Power BI doğru şekilde görmek için 24 saat beklemeniz gerekir. Raporlara 24 saatten önce erişmeye çalışırsanız, geçerli raporları göstermek için tüm veriler henüz mevcut olmadığından bu hata iletisi görüntülenir. |

## <a name="next-steps"></a>Sonraki adımlar

Depolama hesabını yapılandırıp Azure Backup şablonu uygulamasını içeri aktardıktan sonra, sonraki adımlar raporları özelleştirmek ve rapor oluşturmak için bir raporlama veri modeli kullanmaktır. Daha fazla bilgi için aşağıdaki makalelere bakın.

- [Azure Backup raporlama veri modeli kullanma](backup-azure-reports-data-model.md)
- [Power BI raporları filtrele](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
- [Power BI rapor oluşturma](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
