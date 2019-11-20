---
title: System Center DPM aracılığıyla bir Exchange Server yedekleme
description: System Center 2012 R2 DPM kullanarak bir Exchange Server 'ı Azure Backup nasıl yedekleyeceğinizi öğrenin
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: b2231fcadc3e0cb591302ba47572627ae3a17e58
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173273"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>System Center 2012 R2 DPM ile Azure Backup’a Exchange sunucusu yedekleme

Bu makalede, bir Microsoft Exchange sunucusunu Azure Backup üzere yedeklemek için System Center 2012 R2 Data Protection Manager (DPM) sunucusunun nasıl yapılandırılacağı açıklanır.  

## <a name="updates"></a>Güncelleştirmeler

DPM sunucusunu Azure Backup başarıyla kaydetmek için, System Center 2012 R2 DPM için en son güncelleştirme paketini ve Azure Backup aracısının en son sürümünü yüklemelisiniz. [Microsoft kataloğundan](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)en son güncelleştirme paketini alın.

> [!NOTE]
> Bu makaledeki örneklerde, Azure Backup aracısının sürüm 2.0.8719.0 yüklüdür ve güncelleştirme paketi 6, System Center 2012 R2 DPM 'ye yüklenir.
>
>

## <a name="prerequisites"></a>Önkoşullar

Devam etmeden önce, iş yüklerini korumak için Microsoft Azure Backup kullanmaya yönelik tüm [önkoşulların](backup-azure-dpm-introduction.md#prerequisites-and-limitations) karşılandığından emin olun. Bu Önkoşullar şunları içerir:

* Azure sitesinde bir yedekleme Kasası oluşturuldu.
* Aracı ve kasa kimlik bilgileri DPM sunucusuna indirildi.
* Aracı DPM sunucusuna yüklenir.
* Kasa kimlik bilgileri, DPM sunucusunu kaydetmek için kullanıldı.
* Exchange 2016 ' i koruyorsanız lütfen DPM 2012 R2 UR9 veya üzeri sürümlere yükseltin

## <a name="dpm-protection-agent"></a>DPM koruma Aracısı

DPM koruma aracısını Exchange sunucusuna yüklemek için şu adımları izleyin:

1. Güvenlik duvarlarının doğru yapılandırıldığından emin olun. Bkz. [aracı için güvenlik duvarı özel durumlarını yapılandırma](https://technet.microsoft.com/library/Hh758204.aspx).
2. **Yönetim > aracıları** ' na tıklayarak aracıyı Exchange sunucusuna (DPM Yönetici Konsolu ' de Install > ' a tıklayın. Ayrıntılı adımlar için bkz. [DPM koruma aracısını yükler](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) .

## <a name="create-a-protection-group-for-the-exchange-server"></a>Exchange Server için bir koruma grubu oluşturun

1. DPM Yönetici Konsolu, **koruma**' yı ve ardından **yeni koruma grubu oluşturma** Sihirbazı 'nı açmak için araç şeridinde **Yeni** ' ye tıklayın.
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

    Bu seçeneği belirledikten sonra, Exchange Server üzerinde **eseutil** komutu çalıştırılarak oluşturulan g/ç trafiğinden kaçınmak için, yedekleme TUTARLıLıK denetimi DPM sunucusunda çalıştırılır.

   > [!NOTE]
   > Bu seçeneği kullanmak için, ese. dll ve Eseutil. exe dosyalarını DPM sunucusundaki C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin dizinine kopyalamanız gerekir. Aksi takdirde, aşağıdaki hata tetiklenir:  
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
12. DPM sunucusunun ilk çoğaltmayı oluşturma zamanını seçin ve ardından **İleri**' ye tıklayın.
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

1. Bir Exchange veritabanını kurtarmak için DPM Yönetici Konsolu **Kurtarma** ' ya tıklayın.
2. Kurtarmak istediğiniz Exchange veritabanını bulun.
3. *Kurtarma zamanı* açılır listesinden bir çevrimiçi kurtarma noktası seçin.
4. **Kurtarma Sihirbazı 'nı**başlatmak için **kurtar** ' ı tıklatın.

Çevrimiçi kurtarma noktaları için beş kurtarma türü vardır:

* **Özgün Exchange Server konumuna kurtar:** Veriler özgün Exchange sunucusuna kurtarılacak.
* **Exchange Server üzerindeki başka bir veritabanına kurtar:** Veriler başka bir Exchange Server üzerindeki başka bir veritabanına kurtarılacak.
* **Kurtarma veritabanına kurtar:** Veriler bir Exchange kurtarma veritabanına (RDB) kurtarılacak.
* **Ağ klasörüne kopyala:** Veriler bir ağ klasörüne kurtarılacak.
* **Banda Kopyala:** DPM sunucusunda bağlı ve yapılandırılmış bir bant kitaplığınız veya tek başına bant sürücünüz varsa, kurtarma noktası boş bir banda kopyalanacaktır.

    ![Çevrimiçi çoğaltma seçin](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Backup SSS](backup-azure-backup-faq.md)
