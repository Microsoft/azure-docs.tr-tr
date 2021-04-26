---
title: Bir birimdeki tüm dosyaları MARS ile geri yükleme
description: MARS Aracısı 'nı kullanarak bir birimdeki tüm dosyaları geri yüklemeyi öğrenin.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 1d04e9f77b9f92594def9381f973c999e96b2cb2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516510"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>MARS Aracısı 'nı kullanarak bir birimdeki tüm dosyaları geri yükleme

Bu makalede, Microsoft Azure Kurtarma Hizmetleri (MARS) aracısında verileri kurtar Sihirbazı 'nı kullanarak tüm bir birimdeki tüm yedeklenen dosyaların nasıl geri yükleneceği açıklanmaktadır. Seçenekleriniz şunlardır:

- Bir birimdeki yedeklenen tüm dosyaları yedeklerin alındığı makineye geri yükleyin.
- Bir birimdeki yedeklenen tüm dosyaları alternatif bir makineye geri yükleyin.

>[!TIP]
>**Birim** seçeneği, belirtilen birimdeki tüm yedeklenen verileri kurtarır. Bu seçenek daha hızlı aktarım hızları (40 MB/sn 'ye kadar) sağlar ve büyük ölçekli verileri veya tüm birimleri kurtarmak için önerilir.
>
>**Tek tek dosyalar ve klasörler seçeneği** , kurtarma noktası verilerine hızlı erişim sağlar. Tek tek dosyaları kurtarmak için uygundur ve 80 GB 'tan az bir toplam boyut için önerilir. Kurtarma sırasında 6 MBps 'e kadar aktarım hızı veya kopyalama hızları sunar.

## <a name="volume-level-restore-to-the-same-machine"></a>Birim düzeyi aynı makineye geri yükleme

Aşağıdaki adımlar bir birimdeki yedeklenen tüm dosyaları kurtarmanıza yardımcı olur:

1. **Microsoft Azure Backup** ek bileşenini açın. Ek bileşenin nerede yüklü olduğunu bilmiyorsanız, bilgisayarı veya sunucuyu **Microsoft Azure Backup** arayın. Masaüstü uygulaması, arama sonuçlarında görünmelidir.

1. Sihirbazı başlatmak için **verileri kurtar** ' ı seçin.

    ![Verileri Kurtar menüsü](./media/restore-all-files-volume-mars/recover.png)

1. **Başlarken** sayfasında, verileri aynı sunucuya veya bilgisayara geri yüklemek için, ileri ' yi **(sunucu adı)** seçin  >  .

    ![Başlarken sayfası](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. **Kurtarma modunu seçin** sayfasında, sonraki **birim**' i seçin  >  .

    ![Kurtarma modunu seçin](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. **Birim ve tarih seçin** sayfasında, geri yüklemek istediğiniz birimi seçin.

    Takvimde, bir kurtarma noktası seçin. **Kalın yazı** tarihi, en az bir kurtarma noktasının kullanılabilirliğini gösterir. Tek bir tarih içinde birden çok kurtarma noktası varsa, **zaman** açılan menüsünden belirli bir kurtarma noktasını seçin.

     ![Birim ve tarih seçin](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. **Kurtarma seçeneklerini belirtin** sayfasında geri yükleme davranışını yapılandırın.
    1. Kurtarma hedefini seçin:
        - **Özgün konum**: verileri özgün yola geri yükleyin.
        - **Başka bir konum**: verilerin geri yükleneceği alternatif bir konum belirtin.
    1. **Yedekteki öğelerin kurtarma hedefinde zaten olduğu zaman** için davranışı seçin:
        - **Her iki sürüme de sahip olmak için kopya oluşturun**: aynı ada sahip bir dosya zaten mevcutsa, kurtarma noktasındaki veriler kopya olarak geri yüklenir. Kopyanın, aşağıdaki biçimlerden birinde yerel geri yükleme işi saati kullanılarak yerelleştirilmiş bir dosya adı öneki olacaktır:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Kurtarılan sürümlerle var olan sürümlerin üzerine yaz**: aynı ada sahip bir dosya zaten varsa, içerik kurtarma noktasındaki verilerle değiştirilmiştir.
        - **Kurtarma hedefinde zaten mevcut olan öğeleri KURTARMAYIN**: aynı ada sahip bir dosya zaten varsa, atlanır.
    1. Dosyanın kurtarma noktasındaki özgün izinlerle geri yüklenmesi gerekiyorsa **kurtarılan dosya veya klasör için erişim denetimi listesi (ACL) Izinlerini geri yükle** ' yi etkinleştirin.
        ![Kurtarma seçeneklerini belirtin](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. **Onay** bölmesinde Kurtarma ayrıntılarını doğrulayın ve **kurtar**' ı seçin.

    ![Onay ayrıntıları](./media/restore-all-files-volume-mars/confirmation-details.png)

1. **Kurtarma ilerlemesi** sayfasında, kurtarma işinin ilerlemesini izleyin. Sihirbaz ayrıca güvenle kapatılabilir ve kurtarma işlemi arka planda devam eder. Panodaki kurtarma işine çift tıklayarak ilerleme durumunu yeniden görüntüleyebilirsiniz.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Birim düzeyi alternatif bir makineye geri yükleme

Aşağıdaki adımlar, bir birimdeki tüm yedeklenen dosyaları alternatif bir makineye kurtarmanıza yardımcı olur. Tüm sunucunuzun kaybolması durumunda Azure Backup verileri kurtarmak için bu adımları kullanabilirsiniz.

Bu adımlar aşağıdaki terminolojiyi içerir:

- *Kaynak makine* : yedeklemenin alındığı ve şu anda kullanılamayan özgün makine.
- *Hedef makine* : verilerin kurtarıldığı makine.
- *Örnek kasa* : kaynak makinenin ve hedef makinenin kaydedildiği kurtarma hizmetleri Kasası.

> [!NOTE]
> Yedeklemeler, işletim sisteminin önceki bir sürümünü çalıştıran bir hedef makineye geri yüklenemez. Örneğin, bir Windows 7 bilgisayarından alınan bir yedekleme, Windows 7 (veya üzeri) bir bilgisayara geri yüklenebilir. Windows 10 bilgisayarından alınan bir yedek bir Windows 7 bilgisayarına geri yüklenemez.

1. Hedef makinede **Microsoft Azure Backup** ek bileşenini açın.

1. Hedef makinenin ve kaynak makinenin aynı kurtarma hizmetleri kasasında kayıtlı olduğundan emin olun.

1. Verileri kurtar **sihirbazını** açmak Için **verileri kurtar** ' ı seçin.

    ![Azure Backup ekran görüntüsü, verileri kurtar vurgulanmış (alternatif makineye geri yükle)](./media/backup-azure-restore-windows-server/recover.png)

1. **Başlarken** sayfasında **başka bir sunucu** seçin.

    ![Veri Kurtarma Sihirbazı Başlarken sayfasının ekran görüntüsü (alternatif makineye geri yükle)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Örnek kasaya karşılık gelen kasa kimlik bilgileri dosyasını sağlayın ve **İleri**' yi seçin.

    Kasa kimlik bilgileri dosyası geçersiz (veya dolmuşsa), Azure portal [örnek kasasından yeni bir kasa kimlik bilgileri dosyası indirin](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) . Geçerli bir kasa kimlik bilgisi belirledikten sonra, karşılık gelen yedekleme kasasının adı görüntülenir.

1. **Yedekleme sunucusunu seçin** sayfasında, görünen makineler listesinden kaynak makineyi seçin ve parolayı girin. Sonra **İleri**’yi seçin.

    ![Veri kurtarma sihirbazının ekran görüntüsü yedekleme sunucusunu seçin sayfası (alternatif makineye geri yükle)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. **Kurtarma modunu seçin** sayfasında, sonraki **birim**' i seçin  >  .

    ![Kurtarma modunu seçin](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. **Birim ve tarih seçin** sayfasında, geri yüklemek istediğiniz birimi seçin.

    Takvimde, bir kurtarma noktası seçin. **Kalın yazı** tarihi, en az bir kurtarma noktasının kullanılabilirliğini gösterir. Tek bir tarih içinde birden çok kurtarma noktası varsa, **zaman** açılan menüsünden belirli bir kurtarma noktasını seçin.

     ![Birim ve tarih seçin](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. **Kurtarma seçeneklerini belirtin** sayfasında geri yükleme davranışını yapılandırın.
    1. Kurtarma hedefini seçin:
        - **Özgün konum**: verileri özgün yola geri yükleyin.
        - **Başka bir konum**: verilerin geri yükleneceği alternatif bir konum belirtin.
    1. **Yedekteki öğelerin kurtarma hedefinde zaten olduğu zaman** için davranışı seçin:
        - **Her iki sürüme de sahip olmak için kopya oluşturun**: aynı ada sahip bir dosya zaten mevcutsa, kurtarma noktasındaki veriler kopya olarak geri yüklenir. Kopyanın, aşağıdaki biçimlerden birinde yerel geri yükleme işi saati kullanılarak yerelleştirilmiş bir dosya adı öneki olacaktır:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Kurtarılan sürümlerle var olan sürümlerin üzerine yaz**: aynı ada sahip bir dosya zaten varsa, içerik kurtarma noktasındaki verilerle değiştirilmiştir.
        - **Kurtarma hedefinde zaten mevcut olan öğeleri KURTARMAYIN**: aynı ada sahip bir dosya zaten varsa, atlanır.
    1. Dosyanın kurtarma noktasındaki özgün izinlerle geri yüklenmesi gerekiyorsa **kurtarılan dosya veya klasör için erişim denetimi listesi (ACL) Izinlerini geri yükle** ' yi etkinleştirin.
        ![Kurtarma seçeneklerini belirtin](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. **Onay** bölmesinde Kurtarma ayrıntılarını doğrulayın ve **kurtar**' ı seçin.

    ![Onay ayrıntıları](./media/restore-all-files-volume-mars/confirmation-details.png)

1. **Kurtarma ilerlemesi** sayfasında, kurtarma işinin ilerlemesini izleyin. Sihirbaz ayrıca güvenle kapatılabilir ve kurtarma işlemi arka planda devam eder. Panodaki kurtarma işine çift tıklayarak ilerleme durumunu yeniden görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Artık dosyalarınızı ve klasörlerinizi kurtardığınıza göre, [yedeklemelerinizi yönetebilirsiniz](backup-azure-manage-windows-server.md).
- [Dosya ve klasörleri yedekleme hakkında sık sorulan soruları](backup-azure-file-folder-backup-faq.yml)bulun.
