---
title: Sistem durumunu bir Windows sunucusuna geri yükleme
description: Windows Server sistem durumunu Azure 'daki bir yedekten geri yüklemek için adım adım açıklama.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 08/18/2017
ms.openlocfilehash: 8e256fbac0651b4c237c540151b3377927989d36
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172825"
---
# <a name="restore-system-state-to-windows-server"></a>Sistem durumunu Windows Server 'a geri yükleme

Bu makalede, Windows Server sistem durumu yedeklemelerinin bir Azure kurtarma hizmetleri kasasından nasıl geri yükleneceği açıklanmaktadır. Sistem durumunu geri yüklemek için, sistem durumu yedeğine sahip olmanız gerekir ( [sistem](backup-azure-system-state.md#back-up-windows-server-system-state)durumu yedekleme bölümündeki yönergeleri kullanarak oluşturulur ve [Microsoft Azure kurtarma hizmetleri (mars) aracısının en son sürümünü](https://aka.ms/azurebackup_agent)yüklediğinizden emin olun. Windows Server sistem durumu verilerini bir Azure kurtarma hizmetleri kasasından kurtarmak iki adımlı bir işlemdir:

1. Sistem durumunu Azure Backup dosya olarak geri yükleyin. Sistem durumunu Azure Backup dosya olarak geri yüklerken şunlardan birini yapabilirsiniz:
   * Sistem durumunu yedeklerin alındığı sunucuya geri yükleyin veya
   * Sistem durumu dosyasını alternatif bir sunucuya geri yükleyin.

2. Geri yüklenen sistem durumu dosyalarını bir Windows Server 'a uygulayın.

## <a name="recover-system-state-files-to-the-same-server"></a>Sistem durumu dosyalarını aynı sunucuya kurtar

Aşağıdaki adımlarda, Windows Server yapılandırmanızın önceki bir duruma nasıl geri alınacağı açıklanmaktadır. Sunucu yapılandırmanızın bilinen, kararlı bir duruma geri alınması son derece değerlidir. Aşağıdaki adımlar, bir kurtarma hizmetleri kasasından sunucunun sistem durumunu geri yükler.

1. **Microsoft Azure Backup** ek bileşenini açın. Ek bileşenin nerede yüklü olduğunu bilmiyorsanız, bilgisayarı veya sunucuyu **Microsoft Azure Backup**arayın.

    Masaüstü uygulaması, arama sonuçlarında görünmelidir.

2. Sihirbazı başlatmak için **verileri kurtar** ' ı tıklatın.

    ![Verileri kurtar](./media/backup-azure-restore-windows-server/recover.png)

3. **Başlarken** bölmesinde, verileri aynı sunucuya veya bilgisayara geri yüklemek Için, **bu sunucuyu (`<server name>`)** seçin ve **İleri**' ye tıklayın.

    ![Verileri aynı makineye geri yüklemek için bu sunucu seçeneğini belirleyin](./media/backup-azure-restore-system-state/samemachine.png)

4. **Kurtarma modunu seçin** bölmesinde **sistem durumu** ' nu seçin ve ardından **İleri**' ye tıklayın.

    ![Dosyalara gözatamıyorum](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. **Birim ve tarih seçin** bölmesinde takvimde, bir kurtarma noktası seçin.

    Herhangi bir kurtarma noktasından dilediğiniz zaman geri yükleyebilirsiniz. **Kalın yazı** tarihi, en az bir kurtarma noktasının kullanılabilirliğini gösterir. Bir tarih seçtiğinizde, birden fazla kurtarma noktası varsa, **zaman** açılan menüsünde belirli bir kurtarma noktasını seçin.

    ![Birim ve Tarih](./media/backup-azure-restore-system-state/select-date.png)

6. Geri yüklenecek kurtarma noktasını seçtikten sonra **İleri**' ye tıklayın.

    Azure Backup, yerel kurtarma noktasını takar ve bunu bir kurtarma birimi olarak kullanır.

7. Sonraki bölmede, kurtarılan sistem durumu dosyaları için hedefi belirtin ve Windows Gezgini 'ni açmak ve istediğiniz dosya ve klasörleri bulmak için **Araştır** ' a tıklayın. **Her iki sürüme de sahip olmak için kopya oluştur**seçeneği, tüm sistem durumu arşivi kopyasını oluşturmak yerine, mevcut bir sistem durumu dosya arşivinde tek tek dosyaların kopyalarını oluşturur.

    ![Kurtarma seçenekleri](./media/backup-azure-restore-system-state/recover-as-files.png)

8. **Onay** bölmesinde Kurtarma ayrıntılarını doğrulayın ve **kurtar**' a tıklayın.

   ![kurtarma eylemini onaylamak için kurtar 'a tıklayın](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kurtarma hedefindeki *WindowsImageBackup* dizinini sunucunun kritik olmayan bir birimine kopyalayın. Genellikle, Windows işletim sistemi birimi kritik birimdir.

10. Kurtarma başarılı olduktan sonra, sistem durumu kurtarma işlemini gerçekleştirmek için [geri yüklenen sistem durumu dosyalarını Windows Server 'A uygulayın](backup-azure-restore-system-state.md)bölümündeki adımları izleyin.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Sistem durumu dosyalarını alternatif bir sunucuya kurtarma

Windows Server bozuksa veya erişilemezse ve Windows Server sistem durumunu kurtararak onu kararlı bir duruma geri yüklemek istiyorsanız, bozuk sunucunun sistem durumunu başka bir sunucudan geri yükleyebilirsiniz. Sistem durumunu ayrı bir sunucuda geri yüklemek için aşağıdaki adımları kullanın.  

Bu adımlarda kullanılan terminoloji şunları içerir:

* *Kaynak makine* : yedeklemenin alındığı ve şu anda kullanılamayan özgün makine.
* *Hedef makine* : verilerin kurtarıldığı makine.
* *Örnek kasa* : *kaynak makinenin* ve *hedef makinenin* kaydedildiği kurtarma hizmetleri Kasası. <br/>

> [!NOTE]
> Bir makineden alınan yedeklemeler, işletim sisteminin önceki bir sürümünü çalıştıran bir makineye geri yüklenemez. Örneğin, bir Windows Server 2016 makinesinden alınan yedeklemeler Windows Server 2012 R2 'ye geri yüklenemez. Ancak ters bir değer mümkündür. Windows Server 2016 ' ü geri yüklemek için Windows Server 2012 R2 'deki yedeklemeleri kullanabilirsiniz.
>

1. *Hedef makinede* **Microsoft Azure Backup** ek bileşenini açın.
2. *Hedef makinenin* ve *kaynak makinenin* aynı kurtarma hizmetleri kasasında kayıtlı olduğundan emin olun.
3. İş akışını başlatmak için **verileri kurtar** ' ı tıklatın.
4. **Başka bir sunucu** seçin

    ![Başka bir sunucu](./media/backup-azure-restore-system-state/anotherserver.png)

5. *Örnek kasaya*karşılık gelen kasa kimlik bilgileri dosyasını sağlayın. Kasa kimlik bilgileri dosyası geçersiz (veya dolmuşsa), Azure portal *örnek kasasından* yeni bir kasa kimlik bilgileri dosyası indirin. Kasa kimlik bilgileri dosyası sağlandığında, kasa kimlik bilgileri dosyasıyla ilişkili kurtarma hizmetleri Kasası görüntülenir.

6. Yedekleme sunucusunu seçin bölmesinde, görünen makineler listesinden *kaynak makineyi* seçin.
7. Kurtarma modunu seçin bölmesinde **sistem durumu** ' nu seçin ve **İleri**' ye tıklayın.

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. **Birim ve tarih seçin** bölmesinde takvimde, bir kurtarma noktası seçin. Herhangi bir kurtarma noktasından dilediğiniz zaman geri yükleyebilirsiniz. **Kalın yazı** tarihi, en az bir kurtarma noktasının kullanılabilirliğini gösterir. Bir tarih seçtiğinizde, birden fazla kurtarma noktası varsa, **zaman** açılan menüsünde belirli bir kurtarma noktasını seçin.

    ![Öğe ara](./media/backup-azure-restore-system-state/select-date.png)

9. Geri yüklenecek kurtarma noktasını seçtikten sonra **İleri**' ye tıklayın.

10. **Sistem durumu kurtarma modunu seçin** bölmesinde sistem durumu dosyalarının kurtarılmasını istediğiniz hedefi belirtin ve ardından **İleri**' ye tıklayın.

    ![Şifreleme](./media/backup-azure-restore-system-state/recover-as-files.png)

    **Her iki sürüme de sahip olmak için kopya oluştur**seçeneği, tüm sistem durumu arşivi kopyasını oluşturmak yerine, mevcut bir sistem durumu dosya arşivinde tek tek dosyaların kopyalarını oluşturur.

11. Onay bölmesinde Kurtarma ayrıntılarını doğrulayın ve **kurtar**' ı tıklatın.

    ![kurtarma işlemini onaylamak için kurtar düğmesine tıklayın](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. *WindowsImageBackup* dizinini sunucunun kritik olmayan bir birimine kopyalayın (örneğin, D:\). Genellikle, Windows işletim sistemi birimi kritik birimdir.

13. Kurtarma işlemini gerçekleştirmek için, [geri yüklenen sistem durumu dosyalarını bir Windows Server 'a uygulamak](#apply-restored-system-state-on-a-windows-server)için aşağıdaki bölümü kullanın.

## <a name="apply-restored-system-state-on-a-windows-server"></a>Windows Server 'da geri yüklenen sistem durumunu Uygula

Azure kurtarma hizmetleri Aracısı 'nı kullanarak sistem durumunu dosyalar olarak kurtardıktan sonra, kurtarılan sistem durumunu Windows Server 'a uygulamak için Windows Server Yedekleme yardımcı programını kullanın. Windows Server Yedekleme yardımcı programı sunucuda zaten var. Aşağıdaki adımlarda kurtarılan sistem durumunun nasıl uygulanacağı açıklanmaktadır.

1. Sunucunuzu *Dizin Hizmetleri onarım modunda*yeniden başlatmak için aşağıdaki komutları kullanın. Yükseltilmiş bir komut isteminde:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

2. Yeniden başlatmadan sonra Windows Server Yedekleme ek bileşenini açın. Ek bileşenin nerede yüklü olduğunu bilmiyorsanız, bilgisayarı veya sunucuyu **Windows Server yedekleme**arayın.

    Masaüstü uygulaması arama sonuçlarında görünür.

3. Ek bileşende **yerel yedekleme**' yi seçin.

    ![geri yüklemek için yerel yedekleme 'yi seçin](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. Yerel yedekleme konsolunda, **Eylemler bölmesinde**, Kurtarma Sihirbazı 'nı açmak için **kurtar** ' ı tıklatın.

5. **Başka bir konumda depolanan bir yedek**seçeneğini belirleyin ve **İleri**' ye tıklayın.

   ![farklı bir sunucuya kurtarmayı seçin](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Konum türünü belirtirken, sistem durumu yedeğiniz başka bir sunucuya kurtarılıyorsa, **uzak paylaşılan klasör** ' i seçin. Sistem durumlarınızın yerel olarak kurtarılması durumunda **yerel sürücüler**' i seçin.

    ![Yerel sunucu veya başka bir sunucudan kurtarma yapılıp yapılmayacağını seçin](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. *WindowsImageBackup* dizininin yolunu girin veya bu dizini içeren yerel sürücüyü (örneğin, D:\windowsımagebackup) seçin, Azure kurtarma hizmetleri Aracısı 'Nı kullanarak sistem durumu dosyalarının kurtarmasının bir parçası olarak kurtarıldı ve **İleri**' ye tıklayın.

    ![paylaşılan dosyanın yolu](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Geri yüklemek istediğiniz sistem durumu sürümünü seçin ve **İleri**' ye tıklayın.

9. Kurtarma türünü seçin bölmesinde **sistem durumu** ' nu seçin ve **İleri**' ye tıklayın.

10. Sistem durumu kurtarma konumu için **özgün konum**' u seçin ve **İleri**' ye tıklayın.

11. Onay ayrıntılarını gözden geçirin, yeniden başlatma ayarlarını doğrulayın ve geri yüklenen sistem durumu dosyalarını uygulamak için **kurtar** ' ı tıklatın.

    ![geri yükleme sistem durumu dosyalarını Başlat](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Active Directory sunucuda sistem durumu kurtarmaya yönelik özel konular

Sistem durumu yedeklemesi Active Directory verileri içerir. Active Directory Etki Alanı hizmetini (AD DS) geçerli durumundan önceki bir duruma geri yüklemek için aşağıdaki adımları kullanın.

1. Dizin Hizmetleri geri yükleme modu 'nda (DSRM) etki alanı denetleyicisini yeniden başlatın.
2. AD DS kurtarmak için Windows Server Yedekleme cmdlet 'lerini kullanmak için [buradaki](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) adımları izleyin.

## <a name="troubleshoot-failed-system-state-restore"></a>Hatalı sistem durumu geri yükleme sorunlarını giderme

Sistem durumunu uygulamaya yönelik önceki işlem başarıyla tamamlanmazsa, Windows Server 'nizi kurtarmak için Windows kurtarma ortamı 'nı (Win RE) kullanın. Aşağıdaki adımlarda Win RE kullanılarak nasıl kurtarılacağı açıklanmaktadır. Bu seçeneği yalnızca Windows Server, sistem durumu geri yüklemesinden sonra normal olarak önyüklenemediğinde kullanın. Aşağıdaki işlem sistem dışı verileri siler, dikkatli olun.

1. Windows Server 'ı Windows kurtarma ortamı 'nda (Win RE) önyükleyin.

2. Kullanılabilir üç seçenekten sorun giderme ' yi seçin.

    ![menüyü açma](./media/backup-azure-restore-system-state/winre-1.png)

3. **Gelişmiş Seçenekler** ekranından **komut istemi** ' ni seçin ve Sunucu Yöneticisi Kullanıcı adını ve parolasını belirtin.

   ![menüyü açma](./media/backup-azure-restore-system-state/winre-2.png)

4. Sunucu Yöneticisi Kullanıcı adını ve parolasını belirtin.

    ![menüyü açma](./media/backup-azure-restore-system-state/winre-3.png)

5. Komut istemi 'ni yönetici modunda açtığınızda, sistem durumu yedekleme sürümlerini almak için aşağıdaki komutu çalıştırın.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![Sistem durumu yedekleme sürümlerini al](./media/backup-azure-restore-system-state/winre-4.png)

6. Yedeklemede kullanılabilir tüm birimleri almak için aşağıdaki komutu çalıştırın.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Sistem durumu yedekleme sürümlerini al](./media/backup-azure-restore-system-state/winre-5.png)

7. Aşağıdaki komut, sistem durumu yedeklemesinin parçası olan tüm birimleri kurtarır. Bu adımın yalnızca sistem durumunun parçası olan kritik birimleri kurtardığını unutmayın. Tüm sistem dışı veriler silinir.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![Sistem durumu yedekleme sürümlerini al](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Sonraki adımlar

* Artık dosyalarınızı ve klasörlerinizi kurtardığınıza göre, [yedeklemelerinizi yönetebilirsiniz](backup-azure-manage-windows-server.md).
