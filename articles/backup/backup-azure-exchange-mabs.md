---
title: Exchange Server 'ı Azure Backup Sunucusu yedekleme
description: Azure Backup kullanarak bir Exchange sunucusunu nasıl yedekleyeceğinizi öğrenin Azure Backup Sunucusu
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: f3a7fae5a1f5ec933c015546ddf2bdb2898e3904
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515507"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Azure Backup Sunucusu bir Exchange Server 'ı Azure 'a yedekleme

Bu makalede, Microsoft Exchange Server 'ı Azure 'a yedeklemek için Microsoft Azure Backup sunucusu 'nun (MABS) nasıl yapılandırılacağı açıklanmaktadır.  

## <a name="prerequisites"></a>Önkoşullar

Devam etmeden önce Azure Backup Sunucusu [yüklendiğinden ve hazırlandığından](backup-azure-microsoft-azure-backup.md)emin olun.

## <a name="mabs-protection-agent"></a>MABS koruma Aracısı

MABS koruma aracısını Exchange sunucusuna yüklemek için şu adımları izleyin:

1. Güvenlik duvarlarının doğru yapılandırıldığından emin olun. Bkz. [aracı için güvenlik duvarı özel durumlarını yapılandırma](/system-center/dpm/configure-firewall-settings-for-dpm).
2. **Yönetim > aracıları ' nı** , mabs Yönetici Konsolu > ' i seçerek aracıyı Exchange sunucusuna yükler. Ayrıntılı adımlar için bkz. [MABS koruma aracısını yüklemeyin](/system-center/dpm/deploy-dpm-protection-agent) .

## <a name="create-a-protection-group-for-the-exchange-server"></a>Exchange Server için bir koruma grubu oluşturun

1. MABS Yönetici Konsolu, **koruma**' yı seçin ve ardından **yeni koruma grubu oluşturma** Sihirbazı 'nı açmak için araç şeridinde **Yeni** ' yi seçin.
2. Sihirbazın **hoş geldiniz** ekranında **İleri**' yi seçin.
3. **Koruma grubu türünü seçin** ekranında **sunucular** ' ı seçin ve ileri ' **yi** seçin.
4. Korumak istediğiniz Exchange Server veritabanını seçin ve **İleri ' yi** seçin.

   > [!NOTE]
   > Exchange 2013 ' i koruyorsanız [exchange 2013 önkoşullarını](/system-center/dpm/back-up-exchange)kontrol edin.
   >
   >

    Aşağıdaki örnekte, Exchange 2010 veritabanı seçilidir.

    ![Grup üyelerini seçin](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Veri koruma yöntemini seçin.

    Koruma grubunu adlandırın ve ardından aşağıdaki seçeneklerden her ikisini birden seçin:

   * Disk kullanarak kısa vadeli koruma istiyorum.
   * Çevrimiçi koruma istiyorum.
6. **İleri**’yi seçin.
7. Exchange Server veritabanlarının bütünlüğünü denetlemek istiyorsanız **veri bütünlüğünü denetlemek Için Eseutil 'ı Çalıştır** seçeneğini belirleyin.

    Bu seçeneği belirledikten sonra, Exchange Server üzerinde **eseutil** komutu çalıştırılarak oluşturulan g/ç trafiğinden kaçınmak için yedek tutarlılık denetimi mabs üzerinde çalıştırılır.

   > [!NOTE]
   > Bu seçeneği kullanmak için, Ese.dll ve Eseutil.exe dosyalarını MABS sunucusundaki C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin dizinine kopyalamanız gerekir. Aksi takdirde, aşağıdaki hata tetiklenir:  
   > ![Eseutil hatası](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. **İleri**’yi seçin.
9. **Kopya yedeklemesi** için veritabanını seçin ve ardından **İleri**' yi seçin.

   > [!NOTE]
   > Veritabanının en az bir DAG kopyası için "tam yedekleme" seçeneğini seçmezseniz Günlükler kesilmez.
   >
   >
10. **Kısa vadeli yedekleme** için hedefleri yapılandırın ve ardından **İleri**' yi seçin.
11. Kullanılabilir disk alanını gözden geçirin ve ardından **İleri**' yi seçin.
12. MABS sunucusunun ilk çoğaltmayı oluşturmasının zamanı seçin ve ardından **İleri**' yi seçin.
13. Tutarlılık denetimi seçeneklerini belirleyin ve ardından **İleri**' yi seçin.
14. Azure 'a yedeklemek istediğiniz veritabanını seçin ve ardından **İleri**' yi seçin. Örnek:

    ![Çevrimiçi koruma verilerini belirtin](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. **Azure Backup** için zamanlamayı tanımlayın ve ardından **İleri**' yi seçin. Örnek:

    ![Çevrimiçi Yedekleme zamanlamasını belirtin](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Çevrimiçi kurtarma noktaları hızlı tam kurtarma noktalarına dayalıdır. Bu nedenle, hızlı tam kurtarma noktası için belirtilen süreden sonra çevrimiçi kurtarma noktasını zamanlamanız gerekir.
    >
    >
16. **Azure Backup** için bekletme ilkesini yapılandırın ve ardından **İleri**' yi seçin.
17. Çevrimiçi çoğaltma seçeneğini belirleyin ve Ileri ' **yi** seçin.

    Büyük bir veritabanınız varsa, ağ üzerinden ilk yedeklemenin oluşturulması uzun zaman alabilir. Bu sorundan kaçınmak için çevrimdışı yedekleme oluşturabilirsiniz.  

    ![Çevrimiçi bekletme ilkesini belirtin](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Ayarları onaylayın ve ardından **Grup Oluştur**' u seçin.
19. **Kapat**’ı seçin.

## <a name="recover-the-exchange-database"></a>Exchange veritabanını kurtarma

1. Bir Exchange veritabanını kurtarmak için, MABS Yönetici Konsolu **Kurtarma** ' yı seçin.
2. Kurtarmak istediğiniz Exchange veritabanını bulun.
3. *Kurtarma zamanı* açılır listesinden bir çevrimiçi kurtarma noktası seçin.
4. **Kurtarma Sihirbazı 'nı** başlatmak için **kurtar** ' ı seçin.

Çevrimiçi kurtarma noktaları için beş kurtarma türü vardır:

* **Özgün Exchange Server konumuna kurtar:** Veriler özgün Exchange sunucusuna kurtarılacak.
* **Exchange Server üzerindeki başka bir veritabanına kurtar:** Veriler başka bir Exchange Server üzerindeki başka bir veritabanına kurtarılacak.
* **Kurtarma veritabanına kurtar:** Veriler bir Exchange kurtarma veritabanına (RDB) kurtarılacak.
* **Ağ klasörüne kopyala:** Veriler bir ağ klasörüne kurtarılacak.
* **Banda Kopyala:** MABS üzerinde bağlı ve yapılandırılmış bir bant kitaplığınız veya tek başına bant sürücünüz varsa, kurtarma noktası boş bir banda kopyalanacaktır.

    ![Çevrimiçi çoğaltma seçin](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Backup SSS](backup-azure-backup-faq.yml)
