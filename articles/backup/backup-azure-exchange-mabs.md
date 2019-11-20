---
title: Exchange Server 'ı Azure Backup Sunucusu yedekleme
description: Azure Backup kullanarak bir Exchange sunucusunu nasıl yedekleyeceğinizi öğrenin Azure Backup Sunucusu
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 73dcc190446a71498dee3f12a5225a56c9f4ddc7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173119"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Azure Backup Sunucusu bir Exchange Server 'ı Azure 'a yedekleme

Bu makalede, Microsoft Exchange Server 'ı Azure 'a yedeklemek için Microsoft Azure Backup sunucusu 'nun (MABS) nasıl yapılandırılacağı açıklanmaktadır.  

## <a name="prerequisites"></a>Önkoşullar

Devam etmeden önce Azure Backup Sunucusu [yüklendiğinden ve hazırlandığından](backup-azure-microsoft-azure-backup.md)emin olun.

## <a name="mabs-protection-agent"></a>MABS koruma Aracısı

MABS koruma aracısını Exchange sunucusuna yüklemek için şu adımları izleyin:

1. Güvenlik duvarlarının doğru yapılandırıldığından emin olun. Bkz. [aracı için güvenlik duvarı özel durumlarını yapılandırma](https://technet.microsoft.com/library/Hh758204.aspx).
2. **Yönetim > aracıları** ' na tıklayarak aracıyı Exchange sunucusuna yükleyip mabs Yönetici Konsolu >. Ayrıntılı adımlar için bkz. [MABS koruma aracısını yüklemeyin](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) .

## <a name="create-a-protection-group-for-the-exchange-server"></a>Exchange Server için bir koruma grubu oluşturun

1. MABS Yönetici Konsolu, **koruma**' ya ve ardından **yeni koruma grubu oluşturma** Sihirbazı ' nı açmak için araç şeridinde **Yeni** ' ye tıklayın.
2. Sihirbazın **hoş geldiniz** ekranında **İleri**' ye tıklayın.
3. **Koruma grubu türünü seçin** ekranında **sunucular** ' ı seçin ve **İleri**' ye tıklayın.
4. Korumak istediğiniz Exchange Server veritabanını seçin ve **İleri**' ye tıklayın.

   > [!NOTE]
   > Exchange 2013 ' i koruyorsanız [exchange 2013 önkoşullarını](https://technet.microsoft.com/library/dn751029.aspx)kontrol edin.
   >
   >

    Aşağıdaki örnekte, Exchange 2010 veritabanı seçilidir.

    ![Grup üyelerini seçin](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Veri koruma yöntemini seçin.

    Koruma grubunu adlandırın ve ardından aşağıdaki seçeneklerden her ikisini birden seçin:

   * Disk kullanarak kısa vadeli koruma istiyorum.
   * Çevrimiçi koruma istiyorum.
6. **İleri**’ye tıklayın.
7. Exchange Server veritabanlarının bütünlüğünü denetlemek istiyorsanız **veri bütünlüğünü denetlemek Için Eseutil 'ı Çalıştır** seçeneğini belirleyin.

    Bu seçeneği belirledikten sonra, Exchange Server üzerinde **eseutil** komutu çalıştırılarak oluşturulan g/ç trafiğinden kaçınmak için yedek tutarlılık denetimi mabs üzerinde çalıştırılır.

   > [!NOTE]
   > Bu seçeneği kullanmak için, ese. dll ve Eseutil. exe dosyalarını MAB sunucusundaki C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin dizinine kopyalamanız gerekir. Aksi takdirde, aşağıdaki hata tetiklenir:  
   > ![Eseutil hatası](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. **İleri**’ye tıklayın.
9. **Kopya yedeklemesi**için veritabanını seçin ve ardından **İleri**' ye tıklayın.

   > [!NOTE]
   > Bir veritabanının en az bir DAG kopyası için "tam yedekleme" seçeneğini seçmezseniz Günlükler kesilmeyecektir.
   >
   >
10. **Kısa vadeli yedekleme**için hedefleri yapılandırın ve ardından **İleri**' ye tıklayın.
11. Kullanılabilir disk alanını gözden geçirin ve ardından **İleri**' ye tıklayın.
12. MAB sunucusunun ilk çoğaltmayı oluşturacak zamanı seçin ve ardından **İleri**' ye tıklayın.
13. Tutarlılık denetimi seçeneklerini belirleyin ve ardından **İleri**' ye tıklayın.
14. Azure 'a yedeklemek istediğiniz veritabanını seçin ve ardından **İleri**' ye tıklayın. Örneğin:

    ![Çevrimiçi koruma verilerini belirtin](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. **Azure Backup**için zamanlamayı tanımlayın ve ardından **İleri**' ye tıklayın. Örneğin:

    ![Çevrimiçi Yedekleme zamanlamasını belirtin](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Çevrimiçi kurtarma noktaları hızlı tam kurtarma noktalarına dayalıdır. Bu nedenle, hızlı tam kurtarma noktası için belirtilen süreden sonra çevrimiçi kurtarma noktasını zamanlamanız gerekir.
    >
    >
16. **Azure Backup**için bekletme ilkesini yapılandırın ve ardından **İleri**' ye tıklayın.
17. Çevrimiçi çoğaltma seçeneğini belirleyin ve **İleri**' ye tıklayın.

    Büyük bir veritabanınız varsa, ağ üzerinden ilk yedeklemenin oluşturulması uzun zaman alabilir. Bu sorundan kaçınmak için çevrimdışı yedekleme oluşturabilirsiniz.  

    ![Çevrimiçi bekletme ilkesini belirtin](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Ayarları onaylayın ve ardından **Grup Oluştur**' a tıklayın.
19. **Kapat**’a tıklayın.

## <a name="recover-the-exchange-database"></a>Exchange veritabanını kurtarma

1. Bir Exchange veritabanını kurtarmak için, MABS Yönetici Konsolu **Kurtarma** ' ya tıklayın.
2. Kurtarmak istediğiniz Exchange veritabanını bulun.
3. *Kurtarma zamanı* açılır listesinden bir çevrimiçi kurtarma noktası seçin.
4. **Kurtarma Sihirbazı 'nı**başlatmak için **kurtar** ' ı tıklatın.

Çevrimiçi kurtarma noktaları için beş kurtarma türü vardır:

* **Özgün Exchange Server konumuna kurtar:** Veriler özgün Exchange sunucusuna kurtarılacak.
* **Exchange Server üzerindeki başka bir veritabanına kurtar:** Veriler başka bir Exchange Server üzerindeki başka bir veritabanına kurtarılacak.
* **Kurtarma veritabanına kurtar:** Veriler bir Exchange kurtarma veritabanına (RDB) kurtarılacak.
* **Ağ klasörüne kopyala:** Veriler bir ağ klasörüne kurtarılacak.
* **Banda Kopyala:** MABS üzerinde bağlı ve yapılandırılmış bir bant kitaplığınız veya tek başına bant sürücünüz varsa, kurtarma noktası boş bir banda kopyalanacaktır.

    ![Çevrimiçi çoğaltma seçin](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Backup SSS](backup-azure-backup-faq.md)
