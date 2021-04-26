---
title: Azure Backup Sunucusu verileri kurtarma
description: Bu kasaya kayıtlı tüm Azure Backup Sunucusu kurtarma hizmetleri kasasına koruduğunuz verileri kurtarın.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 780c88175397fb06e704e57062ae5c6d3b93d8b8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519597"
---
# <a name="recover-data-from-azure-backup-server"></a>Azure Backup Sunucusu’ndan veri kurtarma

Kurtarma Hizmetleri kasasına yedeklediğiniz verileri kurtarmak için Azure Backup Sunucusu kullanabilirsiniz. Bunu yapma işlemi Azure Backup Sunucusu Yönetim Konsolu ile tümleşiktir ve diğer Azure Backup bileşenleri için kurtarma iş akışına benzer.

> [!NOTE]
> Bu makale, [UR7 sürümleriyle veya üzeri Ile System Center Data Protection Manager 2012 R2](https://support.microsoft.com/kb/3065246)için geçerlidir ve [en son Azure Backup aracısıyla](https://aka.ms/azurebackup_agent)birleştirilir.
>
>

Azure Backup Sunucusu verileri kurtarmak için:

1. Azure Backup Sunucusu Yönetim konsolunun **Kurtarma** sekmesinden **' dış DPM Ekle '** öğesini (ekranın sol üst kısmında) seçin.

    ![Dış DPM Ekle](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Verilerin kurtarıldığı **Azure Backup sunucusu** ilişkili kasadan yeni **kasa kimlik bilgilerini** indirin, kurtarma hizmetleri kasasına kayıtlı Azure Backup sunucuları listesinden Azure Backup sunucusu seçin ve verileri kurtarılan sunucuyla ilişkili **şifreleme parolasını** belirtin.

    ![Dış DPM kimlik bilgileri](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Yalnızca aynı kayıt kasası ile ilişkili Azure Backup sunucuları, birbirlerinin verilerini kurtarabilir.
   >
   >

    Dış Azure Backup Sunucusu başarıyla eklendikten sonra, dış sunucu ve yerel Azure Backup Sunucusu verilerine **Kurtarma** sekmesinden gidebilirsiniz.
3. Dış Azure Backup Sunucusu korunan üretim sunucularının kullanılabilir listesine giderek uygun veri kaynağını seçin.

    ![Dış DPM sunucusuna gözatamıyorum](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. **Kurtarma noktaları** açılan listesinden **ayı ve yılı** seçin, kurtarma noktasının oluşturulduğu zamana ait gerekli **Kurtarma tarihini** seçin ve **Kurtarma saatini** seçin.

    Dosya ve klasörlerin listesi, alt bölmede görünür ve bu, herhangi bir konuma gözatılabilir ve kurtarılabilir.

    ![Dış DPM sunucusu kurtarma noktaları](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Uygun öğeye sağ tıklayın ve **kurtar**' ı seçin.

    ![Dış DPM kurtarma](./media/backup-azure-alternate-dpm-server/recover.png)
6. **Kurtarma seçimini** gözden geçirin. Kurtarılan yedekleme kopyasının verilerinin ve saatinin yanı sıra yedekleme kopyasının oluşturulduğu kaynağı doğrulayın. Seçim yanlışsa kurtarma sekmesine geri dönmek için **iptal** ' i seçerek uygun kurtarma noktasını seçin. Seçim doğruysa, **İleri**' yi seçin.

    ![Dış DPM Kurtarma Özeti](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. **Alternatif bir konuma kurtar**' ı seçin. Kurtarma için doğru konuma **gidin** .

    ![Dış DPM kurtarma alternatif konumu](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Kopyalama, **atlama** veya **üzerine yazma** **oluşturma** ile ilgili seçeneği belirleyin.

   * **Kopya oluştur** -bir ad çakışması varsa dosyanın bir kopyasını oluşturur.
   * **Atla** -bir ad çakışması varsa, özgün dosyayı atan dosyayı kurtarmaz.
   * **Üzerine yaz** -bir ad çakışması varsa, dosyanın varolan kopyasının üzerine yazar.

     **Güvenliği geri yüklemek** için uygun seçeneği belirleyin. Verilerin kurtarıldığı hedef bilgisayarın güvenlik ayarlarını veya kurtarma noktasının oluşturulduğu sırada ürün için geçerli olan güvenlik ayarlarını uygulayabilirsiniz.

     Kurtarma başarıyla tamamlandıktan sonra bir **bildirimin** gönderilip gönderilmeyeceğini belirler.

     ![Dış DPM kurtarma bildirimleri](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. **Özet** ekranında şu ana kadar seçili olan seçenekler listelenir. **Kurtar**' ı seçtiğinizde veriler ilgili şirket içi konuma kurtarılır.

    ![Dış DPM kurtarma seçenekleri Özeti](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Kurtarma işi Azure Backup Sunucusu **izleme** sekmesinde izlenebilir.
   >
   >

    ![Kurtarma izleniyor](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Dış DPM sunucusunun görünümünü kaldırmak için DPM sunucusunun **Kurtarma** SEKMESINDE **dış DPM 'yi temizle** seçeneğini belirleyebilirsiniz.

    ![Dış DPM 'yi Temizleme](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Hata iletilerinde sorun giderme

| Hayır. | Hata İletisi | Sorun giderme adımları |
|:---:|:--- |:--- |
| 1. |Bu sunucu, kasa kimlik bilgisi tarafından belirtilen kasaya kayıtlı değil. |**Neden:** Bu hata, seçilen kasa kimlik bilgileri dosyası kurtarmanın denendiği Azure Backup Sunucusu ilişkili kurtarma hizmetleri kasasına ait olmadığında görüntülenir. <br> **Çözüm:** Azure Backup Sunucusu kaydedildiği kurtarma hizmetleri kasasından kasa kimlik bilgileri dosyasını indirin. |
| 2. |Kurtarılabilir veriler kullanılamıyor ya da seçilen sunucu bir DPM sunucusu değil. |**Neden:** Kurtarma Hizmetleri kasasına kayıtlı başka bir Azure Backup sunucusu yok veya sunucular henüz meta verileri yüklemedi veya seçili sunucu bir Azure Backup Sunucusu (Windows Server veya Windows Istemcisi kullanılarak) değil. <br> **Çözüm:** Kurtarma Hizmetleri kasasına kayıtlı başka Azure Backup sunucuları varsa, en son Azure Backup aracısının yüklü olduğundan emin olun. <br>Kurtarma Hizmetleri kasasına kayıtlı başka Azure Backup sunucular varsa, kurtarma işlemini başlatmak için yüklemeden sonra bir gün bekleyin. Gecelik işi tüm korumalı yedeklemelerin meta verilerini buluta yükleyecek. Veriler kurtarma için kullanılabilir olacaktır. |
| 3. |Bu kasaya kayıtlı başka hiçbir DPM sunucusu yok. |**Neden:** Kurtarmanın denendiği kasaya kayıtlı başka bir Azure Backup sunucusu yok.<br>**Çözüm:** Kurtarma Hizmetleri kasasına kayıtlı başka Azure Backup sunucuları varsa, en son Azure Backup aracısının yüklü olduğundan emin olun.<br>Kurtarma Hizmetleri kasasına kayıtlı başka Azure Backup sunucular varsa, kurtarma işlemini başlatmak için yüklemeden sonra bir gün bekleyin. Gecelik işi tüm korumalı yedeklemelerin meta verilerini buluta yükler. Veriler kurtarma için kullanılabilir olacaktır. |
| 4. |Belirtilen şifreleme parolası, aşağıdaki sunucuyla ilişkili parolayla eşleşmiyor: **\<server name>** |**Neden:** Kurtarılan Azure Backup Sunucusu verileri şifreleme işleminde kullanılan şifreleme parolası, belirtilen şifreleme parolası ile eşleşmiyor. Aracı verilerin şifresini çözemez ve kurtarma başarısız olur.<br>**Çözüm:** Verileri kurtarılan Azure Backup Sunucusu ilişkili şifreleme parolasının aynısını sağlayın. |

## <a name="next-steps"></a>Sonraki adımlar

Diğer SSS belgelerini okuyun:

* Azure VM yedeklemeleri hakkında [sık sorulan sorular](backup-azure-vm-backup-faq.yml)
* Azure Backup aracısıyla ilgili [yaygın sorular](backup-azure-file-folder-backup-faq.yml)
