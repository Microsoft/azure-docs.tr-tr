---
title: Öğretici-Windows Server 'ı Azure 'a yedekleme
description: Bu öğretici, şirket içi Windows Server’ların bir Kurtarma Hizmetleri kasasında yedeklenmesi işlemini açıklar.
ms.topic: tutorial
ms.date: 08/22/2018
ms.custom: mvc
ms.openlocfilehash: d29983bec7dbe379bfb2898e0f068a02b01ae726
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171825"
---
# <a name="back-up-windows-server-to-azure"></a>Windows Server’ı Azure’da Yedekleme

Windows Server'ınızı bozulmalara, saldırılara ve olağanüstü durumlara karşı korumak için Azure Backup’ı kullanabilirsiniz. Azure Backup, Microsoft Azure Kurtarma Hizmetleri (MARS) aracısı olarak bilinen basit bir araç sağlar. Windows Server Sistem Durumu aracılığıyla sunucu yapılandırmasını, dosyaları ve klasörleri korumak için Windows Server’a MARS aracısı yüklenir. Bu öğretici, Windows Server’ınızı Azure’da yedeklemek için MARS Aracısını nasıl kullanabileceğinizi açıklar. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * MARS Aracısını indirip ayarlama
> * Sunucunuzun yedeklemeleri için yedekleme zamanlarını ve bekletme zamanlamasını yapılandırma
> * İsteğe bağlı yedekleme gerçekleştirin

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

<https://portal.azure.com.> Azure portal oturum açın

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Windows Server’ı yedekleyebilmeniz için önce yedeklemelerin veya geri yükleme noktalarının depolanacağı bir yer oluşturmanız gerekir. [Kurtarma Hizmetleri kasası](backup-azure-recovery-services-vault-overview.md), Windows Server’ınızdan yedeklemeleri depolayan Azure’daki bir kapsayıcıdır. Azure portalında Kurtarma Hizmetleri kasası oluşturmak için aşağıdaki adımları izleyin.

1. Sol taraftaki menüden **Tüm hizmetler**’i seçin ve hizmet listesinde **Kurtarma Hizmetleri** yazın. **Kurtarma Hizmetleri kasaları**’na tıklayın.

   ![Kurtarma Hizmetleri kasasını açma](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. **Kurtarma Hizmetleri kasaları** menüsünde **Ekle**'ye tıklayın.

   ![kasa için bilgileri sağlama](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. **Kurtarma Hizmetleri kasası** menüsünde,

    * *Ad* alanına **myRecoveryServicesVault** yazın.
    * **Abonelik** bölümünde geçerli abonelik kimliği görüntülenir.
    * **Kaynak grubu** için **Var olanı kullan**’ı seçin ve sonra *myResourceGroup* seçeneğini belirleyin. *myResourceGroup* yoksa **Yeni Oluştur**’u seçin ve *myResourceGroup* yazın.
    * **Konum** açılır menüsünden *Batı Avrupa*’yı seçin.
    * Kurtarma Hizmetleri kasanızı oluşturmak için **Oluştur**’a tıklayın.

Kasanız oluşturulduktan sonra Kurtarma Hizmetleri kasaları listesinde görünür.

## <a name="download-recovery-services-agent"></a>Kurtarma Hizmetleri aracısını indirme

Microsoft Azure Kurtarma Hizmetleri (MARS) aracısı, Windows Server ile Kurtarma Hizmetleri kasanız arasında bir ilişkilendirme oluşturur. Aşağıdaki yordamda, aracının sunucunuza nasıl indirileceği açıklanmaktadır.

1. Kurtarma Hizmetleri kasalarının listesinden **myRecoveryServicesVault** kasasını seçerek panosunu açın.

   ![kasa için bilgileri sağlama](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. Kasa panosu menüsünde **Yedekle**’ye tıklayın.

3. **Yedekleme Hedefi** menüsünde:

   * **iş yükünüz nerede çalışıyor?** için **Şirket içi** ' i seçin
   * **Neleri yedeklemek istiyorsunuz?** için **Dosyalar ve klasörler**'i ve **Sistem Durumu**’nu seçin

   ![kasa için bilgileri sağlama](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. **Altyapıyı hazırlama** menüsünü açmak için **Altyapıyı hazırlama**’ya tıklayın.

5. **Altyapıyı hazırlama** menüsünde **Windows Server veya Windows İstemcisi Aracısı'nı indirin**’e tıklayarak *MARSAgentInstaller.exe* dosyasını indirin.

    ![altyapıyı hazırlama](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    Yükleyici ayrı bir tarayıcı açar ve **MARSAgentInstaller.exe** dosyasını indirir.

6. İndirilen dosyayı çalıştırmadan önce, Altyapıyı hazırlama dikey penceresindeki **İndir** düğmesine tıklayın ve **Kasa Kimlik Bilgileri** dosyasını kaydedin. MARS Aracısı ile Kurtarma Hizmetleri kasası arasında bağlantı kurmak için kasa kimlik bilgileri gereklidir.

    ![altyapıyı hazırlama](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)

## <a name="install-and-register-the-agent"></a>Aracıyı yükleme ve kaydetme

1. İndirilen **MARSagentinstaller.exe** dosyasını bulup üzerine çift tıklayın.
2. **Microsoft Azure Kurtarma Hizmetleri Aracısı Kurulum Sihirbazı** görüntülenir. Sihirbazda ilerledikçe, istendiğinde aşağıdaki bilgileri sağlayın ve **Kaydol**’a tıklayın.
   * Yükleme ve önbellek klasörü için konum.
   * İnternet'e bağlanmak için bir ara sunucu kullanıyorsanız ara sunucu bilgileri.
   * Kimliği doğrulanmış bir ara sunucu kullanıyorsanız kullanıcı adı ve parola bilgileriniz.

     ![altyapıyı hazırlama](./media/tutorial-backup-windows-server-to-azure/mars-installer.png)

3. Sihirbazın sonunda, **Kayıt İşlemine Geç**’e tıklayın ve önceki yordamda indirdiğiniz **Kasa Kimlik Bilgileri** dosyasını sağlayın.

4. İstendiğinde, Windows Server’dan yedeklemeleri şifrelemek için bir şifreleme parolası sağlayın. Kaybolması durumunda Microsoft parolayı kurtaramadığından parolayı güvenli bir konuma kaydedin.

5. **Son**'a tıklayın.

## <a name="configure-backup-and-retention"></a>Yedekleme ve Bekletmeyi Yapılandırma

Windows Server’da, Azure’daki yedeklemelerin ne zaman gerçekleşeceğini zamanlamak için Microsoft Azure Kurtarma Hizmetleri aracısını kullanırsınız. Aracıyı indirdiğiniz sunucuda aşağıdaki adımları yürütün.

1. Microsoft Azure Kurtarma Hizmetleri aracısını açın. Bunu, makinenizde **Microsoft Azure Backup** aramasını yaparak bulabilirsiniz.

2. Kurtarma Hizmetleri aracısı konsolunda, **Eylemler Bölmesi** bölümündeki **Yedeklemeyi Zamanla**’ya tıklayın.

    ![altyapıyı hazırlama](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. **İleri**’ye tıklayarak **Yedeklenecek Öğeleri Seçin** sayfasına gidin.

4. **Öğeleri Ekle**’ye tıklayın ve açılan iletişim kutusundan **Sistem Durumu**’nu ve yedeklemek istediğiniz dosyaları veya klasörleri seçin. Daha sonra, **Tamam**'a tıklayın.

5. **İleri**’ye tıklayın.

6. **Yedekleme Zamanlamasını Belirtin (Sistem Durumu)** sayfasında, Sistem Durumu için yedeklemelerin tetiklenmesinin gerektiği günün saatini veya haftayı belirtin ve **İleri**’ye tıklayın.

7. **Bekletme İlkesi Seçin (Sistem Durumu)** sayfasında, Sistem Durumu için yedekleme kopyasına yönelik Bekletme İlkesini seçin ve **İleri**’ye tıklayın.

8. Benzer şekilde, seçilen dosyalar ve klasörler için yedekleme zamanlamasını ve bekletme ilkesini seçin.

9. **İlk Yedekleme Türünü Seçin** sayfasında **Ağ üzerinden otomatik olarak** seçeneğini belirleyin ve **İleri**'ye tıklayın.

10. **Onay** sayfasında bilgileri gözden geçirin ve **Son**'a tıklayın.

11. Sihirbaz yedekleme zamanlamasını oluşturduktan sonra **Kapat**'a tıklayın.

## <a name="perform-an-on-demand-backup"></a>İsteğe bağlı yedekleme gerçekleştirme

Yedekleme işlerinin çalıştırılması için zamanlamayı belirlediniz. Ancak henüz sunucuyu yedeklemediniz. Sunucunuz için veri dayanıklılığını sağlamak amacıyla isteğe bağlı yedekleme çalıştırmak ideal bir olağanüstü durum kurtarma uygulamasıdır.

1. Microsoft Azure Kurtarma Hizmetleri aracısı konsolunda **Şimdi Yedekle**’ye tıklayın.

    ![altyapıyı hazırlama](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2. **Şimdi Yedekle** sihirbazında, **Dosyalar ve Klasörler** veya **Sistem Durumu** içinden, yedeklemek istediğinizi seçin ve **İleri**’ye tıklayın
3. **Onay** sayfasında, **Şimdi Yedekle** sihirbazının sunucunuzu yedeklemek için kullandığı ayarları gözden geçirin. Ardından **Yedekle**'ye tıklayın.
4. Sihirbazı kapatmak için **Kapat**'a tıklayın. Yedekleme işlemi tamamlanmadan önce sihirbazı kapatırsanız, sihirbaz arka planda çalışmaya devam eder.
5. İlk yedekleme tamamlandıktan sonra, MARS aracısı konsolunun **İşler** bölmesinde **İş tamamlandı** durumu görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure portalı kullanarak şu işlemleri gerçekleştirdiniz:

> [!div class="checklist"]
>
> * Kurtarma Hizmetleri kasası oluşturma
> * Microsoft Azure Kurtarma Hizmetleri aracısını indirme
> * Aracıyı yükleme
> * Windows Server için yedeklemeyi yapılandırma
> * İsteğe bağlı yedekleme gerçekleştirme

Azure’dan Windows Server’a dosyaları kurtarmak için sonraki öğreticiye devam edin

> [!div class="nextstepaction"]
> [Azure’dan Windows Server’a dosyaları kurtarma](./tutorial-backup-restore-files-windows-server.md)
