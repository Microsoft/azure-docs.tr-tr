---
title: Azure 'da verileri bir Windows Server 'a geri yükleme
description: Bu makalede, Azure 'da depolanan verileri Microsoft Azure Kurtarma Hizmetleri (MARS) aracısına sahip bir Windows Server veya Windows bilgisayara geri yüklemeyi öğrenin.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 4d663739f25762723fc3a8f566c4b5ef3f957338
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172780"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Azure Resource Manager dağıtım modelini kullanarak dosyaları Windows 'a geri yükleme

Bu makalede, verileri bir yedekleme kasasından geri yükleme açıklanmaktadır. Verileri geri yüklemek için, Microsoft Azure Kurtarma Hizmetleri (MARS) aracısında verileri kurtar Sihirbazı 'nı kullanın. Şunları yapabilirsiniz:

* Verileri yedeklerin alındığı makineye geri yükleyin.
* Verileri alternatif bir makineye geri yükleme.

Yazılabilir bir kurtarma noktası anlık görüntüsünü kurtarma birimi olarak bağlamak için anlık geri yükleme özelliğini kullanın. Daha sonra kurtarma birimini araştırabilir ve dosyaları yerel bir bilgisayara kopyalayabilir, böylece dosyaları seçmeli olarak geri yükleyebilirsiniz.

> [!NOTE]
> Verileri geri yüklemek için anlık geri yükleme kullanmak istiyorsanız [ocak 2017 Azure Backup güncelleştirme](https://support.microsoft.com/help/3216528?preview) gerekir. Ayrıca, yedekleme verileri destek makalesinde listelenen yerel ayarlarda kasaların korunması gerekir. Anında geri yüklemeyi destekleyen en son yerel ayar listesi için [2017 ocak Azure Backup güncelleştirmesine](https://support.microsoft.com/help/3216528?preview) bakın.
>

Azure portal kurtarma hizmetleri kasalarıyla anında geri yükleme kullanın. Yedekleme kasalarında veri depoladıysanız, bunlar kurtarma hizmetleri kasalarına dönüştürülüyordu. Anında geri yükleme kullanmak istiyorsanız, MARS güncelleştirmesini indirin ve anında geri yükleme ile ilgili yordamları izleyin.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Aynı makineye veri kurtarmak için anlık geri yükleme kullanın

Yanlışlıkla bir dosyayı silmiş ve aynı makineye geri yüklemek istiyorsanız (yedeklemenin alındığı), aşağıdaki adımlar verileri kurtarmanıza yardımcı olur.

1. **Microsoft Azure Backup** ek bileşenini açın. Ek bileşenin nerede yüklü olduğunu bilmiyorsanız, bilgisayarı veya sunucuyu **Microsoft Azure Backup**arayın.

    Masaüstü uygulaması, arama sonuçlarında görünmelidir.

2. Sihirbazı başlatmak için **verileri kurtar** ' ı seçin.

    ![Kurtarma verileri vurgulanmış olarak Azure Backup ekran görüntüsü](./media/backup-azure-restore-windows-server/recover.png)

3. **Başlarken** sayfasında, verileri aynı sunucuya veya bilgisayara geri yüklemek için **bu sunucuyu (`<server name>`)** **İleri** > seçin.

    ![Verileri Kurtarma Sihirbazı Başlarken sayfasının ekran görüntüsü](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. **Kurtarma modunu seç** sayfasında, **Ileri**' > **tek tek dosyalar ve klasörler** ' i seçin.

    ![Verileri kurtar sihirbazının kurtarma modunu seçin sayfasında ekran görüntüsü](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Tek dosya ve klasörleri geri yükleme seçeneği .NET Framework 4.5.2 veya üstünü gerektirir. **Tek tek dosyalar ve klasörler** seçeneğini görmüyorsanız, .NET Framework sürüm 4.5.2 veya sonraki bir sürüme yükseltmeniz ve yeniden denemeniz gerekir.
 
   > [!TIP]
   > **Tek tek dosyalar ve klasörler** seçeneği, kurtarma noktası verilerine hızlı erişim sağlar. Boyut 80 GB 'tan fazla değil, tek tek dosyaları kurtarmak için uygundur ve kurtarma sırasında 5 MBps 'e kadar aktarım hızı veya kopyalama hızları sunar. **Birim** seçeneği, belirtilen birimdeki tüm yedeklenen verileri kurtarır. Bu seçenek, büyük ölçekli verileri veya tüm birimleri kurtarmak için ideal olan daha hızlı aktarım hızları (60 MB/sn 'ye kadar) sağlar.

5. **Birim ve tarih seçin** sayfasında, geri yüklemek istediğiniz dosya ve klasörleri içeren birimi seçin.

    Takvimde, bir kurtarma noktası seçin. **Kalın yazı** tarihi, en az bir kurtarma noktasının kullanılabilirliğini gösterir. Tek bir tarih içinde birden çok kurtarma noktası varsa, **zaman** açılan menüsünden belirli bir kurtarma noktasını seçin.

    ![Veri Kurtarma Sihirbazı 'nın ekran görüntüsü birim ve Tarih sayfası seçme](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Geri yüklenecek kurtarma noktasını seçtikten sonra, **bağla**' yı seçin.

    Azure Backup, yerel kurtarma noktasını takar ve bunu bir kurtarma birimi olarak kullanır.

7. **Dosyaları araştır ve kurtar** sayfasında, Windows Gezgini 'ni açmak için **Araştır** ' ı seçin ve istediğiniz dosya ve klasörleri bulun.

    ![Veri Kurtarma Sihirbazı 'nın dosyaları tarama ve kurtarma sayfasında ekran görüntüsü](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. Windows Gezgini 'nde, geri yüklemek istediğiniz dosya ve klasörleri kopyalayın ve bunları sunucuya veya bilgisayara yerel bir konuma yapıştırın. Dosyaları doğrudan kurtarma biriminden açabilir veya akışa alabilir ve doğru sürümleri kurtardığınızı doğrulayabilirsiniz.

    ![Kopya vurgulanmış şekilde Windows Gezgini 'nin ekran görüntüsü](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. İşiniz bittiğinde, **dosyaları tarayın ve kurtar** sayfasında, çıkar ' **ı seçin.** Ardından, birimi çıkarmak istediğinizi onaylamak için **Evet** ' i seçin.

    ![Veri Kurtarma Sihirbazı 'nın dosyaları tarama ve kurtarma sayfasında ekran görüntüsü](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Çıkar ' ı seçmezseniz kurtarma birimi, bağlanalındığı zamandan itibaren 6 saat boyunca **takılmış olarak kalır**. Ancak, devam eden bir dosya kopyalama durumunda bağlama süresi en fazla 24 saate genişletilir. Birim bağlıyken hiçbir yedekleme işlemi çalıştırılmayacak. Birim bağlandığında, kurtarma birimi kaldırıldıktan sonra çalıştırılacak şekilde zamanlanan tüm yedekleme işlemleri çalıştırılır.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Verileri alternatif bir makineye geri yüklemek için anlık geri yüklemeyi kullanın

Tüm sunucunuzun kaybolması durumunda Azure Backup verileri başka bir makineye de kurtarabilirsiniz. Aşağıdaki adımlarda iş akışı gösterilmektedir.

Bu adımlar aşağıdaki terminolojiyi içerir:

* *Kaynak makine* : yedeklemenin alındığı ve şu anda kullanılamayan özgün makine.
* *Hedef makine* : verilerin kurtarıldığı makine.
* *Örnek kasa* : kaynak makinenin ve hedef makinenin kaydedildiği kurtarma hizmetleri Kasası.

> [!NOTE]
> Yedeklemeler, işletim sisteminin önceki bir sürümünü çalıştıran bir hedef makineye geri yüklenemez. Örneğin, bir Windows 7 bilgisayarından alınan bir yedekleme, Windows 7 (veya üzeri) bir bilgisayara geri yüklenebilir. Windows 8 bilgisayarından alınan bir yedek, Windows 7 bilgisayarına geri yüklenemez.
>
>

1. Hedef makinede **Microsoft Azure Backup** ek bileşenini açın.

2. Hedef makinenin ve kaynak makinenin aynı kurtarma hizmetleri kasasında kayıtlı olduğundan emin olun.

3. Verileri kurtar **sihirbazını**açmak Için **verileri kurtar** ' ı seçin.

    ![Kurtarma verileri vurgulanmış olarak Azure Backup ekran görüntüsü](./media/backup-azure-restore-windows-server/recover.png)

4. **Başlarken** sayfasında **başka bir sunucu**seçin.

    ![Verileri Kurtarma Sihirbazı Başlarken sayfasının ekran görüntüsü](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Örnek kasaya karşılık gelen kasa kimlik bilgileri dosyasını sağlayın ve **İleri**' yi seçin.

    Kasa kimlik bilgileri dosyası geçersiz (veya dolmuşsa), Azure portal örnek kasasından yeni bir kasa kimlik bilgileri dosyası indirin. Geçerli bir kasa kimlik bilgisi belirledikten sonra, karşılık gelen yedekleme kasasının adı görüntülenir.

6. **Yedekleme sunucusunu seçin** sayfasında, görünen makineler listesinden kaynak makineyi seçin ve parolayı girin. Sonra **İleri**’yi seçin.

    ![Veri kurtarma sihirbazının yedek sunucu seçin sayfası ekran görüntüsü](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. **Kurtarma modunu seç** sayfasında, **Ileri**' > **tek tek dosyalar ve klasörler** ' i seçin.

    ![Verileri kurtar sihirbazının kurtarma modunu seçin sayfasında ekran görüntüsü](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. **Birim ve tarih seçin** sayfasında, geri yüklemek istediğiniz dosya ve klasörleri içeren birimi seçin.

    Takvimde, bir kurtarma noktası seçin. **Kalın yazı** tarihi, en az bir kurtarma noktasının kullanılabilirliğini gösterir. Tek bir tarih içinde birden çok kurtarma noktası varsa, **zaman** açılan menüsünden belirli bir kurtarma noktasını seçin.

    ![Veri Kurtarma Sihirbazı 'nın ekran görüntüsü birim ve Tarih sayfası seçme](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Kurtarma noktasını hedef makinenize bir kurtarma birimi olarak bağlamak için **bağla** ' yı seçin.

10. **Dosyaları araştır ve kurtar** sayfasında, Windows Gezgini 'ni açmak için **Araştır** ' ı seçin ve istediğiniz dosya ve klasörleri bulun.

    ![Veri Kurtarma Sihirbazı 'nın dosyaları tarama ve kurtarma sayfasında ekran görüntüsü](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Windows Gezgini 'nde, kurtarma biriminden dosya ve klasörleri kopyalayın ve hedef makine konumunuza yapıştırın. Dosyaları doğrudan kurtarma biriminden açabilir veya akışa alabilir ve doğru sürümlerin kurtarıldığını doğrulayabilirsiniz.

    ![Kopya vurgulanmış şekilde Windows Gezgini 'nin ekran görüntüsü](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. İşiniz bittiğinde, **dosyaları tarayın ve kurtar** sayfasında, çıkar ' **ı seçin.** Ardından, birimi çıkarmak istediğinizi onaylamak için **Evet** ' i seçin.

    ![Veri Kurtarma Sihirbazı 'nın dosyaları tarama ve kurtarma sayfasında ekran görüntüsü](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Çıkar ' ı seçmezseniz kurtarma birimi, bağlanalındığı zamandan itibaren 6 saat boyunca **takılmış olarak kalır**. Ancak, devam eden bir dosya kopyalama durumunda bağlama süresi en fazla 24 saate genişletilir. Birim bağlıyken hiçbir yedekleme işlemi çalıştırılmayacak. Birim bağlandığında, kurtarma birimi kaldırıldıktan sonra çalıştırılacak şekilde zamanlanan tüm yedekleme işlemleri çalıştırılır.
    >

## <a name="next-steps"></a>Sonraki adımlar

Artık dosyalarınızı ve klasörlerinizi kurtardığınıza göre, [yedeklemelerinizi yönetebilirsiniz](backup-azure-manage-windows-server.md).
