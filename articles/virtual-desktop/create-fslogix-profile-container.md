---
title: FSLogix profil kapsayıcıları NetApp Windows sanal masaüstü-Azure
description: Windows sanal masaüstündeki Azure NetApp Files kullanarak FSLogix profil kapsayıcısı oluşturma.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: helohr
ms.openlocfilehash: 1f5d1050815961f51c2bb1cfce256b1ea37d3ac1
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605772"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>Azure NetApp Files kullanarak bir konak havuzu için FSLogix profil kapsayıcısı oluşturma

FSLogix profil kapsayıcılarını [Windows sanal masaüstü Önizleme hizmeti](overview.md)için bir kullanıcı profili çözümü olarak kullanmanızı öneririz. FSLogix profil kapsayıcıları, tam bir kullanıcı profilini tek bir kapsayıcıda depolar ve profilleri Windows sanal masaüstü gibi kalıcı olmayan uzak bilgi işlem ortamlarında dolaşımda kullanılmak üzere tasarlanmıştır. Oturum açtığınızda kapsayıcı, yerel olarak desteklenen bir sanal sabit disk (VHD) ve Hyper-V sanal sabit diski (VHDX) kullanarak bilgi işlem ortamına dinamik olarak eklenir. Bu gelişmiş filtre sürücü teknolojileri, Kullanıcı profilinin hemen kullanılabilir olmasını ve sistemde tam olarak yerel bir kullanıcı profili gibi gösterilmesini sağlar. FSLogix profil kapsayıcıları hakkında daha fazla bilgi edinmek için bkz. [Fslogix profil kapsayıcıları ve Azure dosyaları](fslogix-containers-azure-files.md).

Müşterilerin Windows sanal masaüstü ortamları için kurumsal düzeyde SMB birimleri hızla ve güvenilir bir şekilde sağlamasına yardımcı olan, kullanımı kolay bir Azure yerel platform hizmeti olan [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)kullanarak FSLogix profil kapsayıcıları oluşturabilirsiniz. Azure NetApp Files hakkında daha fazla bilgi edinmek için bkz. [Azure NetApp Files nedir?](../azure-netapp-files/azure-netapp-files-introduction.md)

Bu kılavuzda, Windows sanal masaüstü 'nde bir Azure NetApp Files hesabı ayarlama ve FSLogix profil kapsayıcıları oluşturma işlemlerinin nasıl yapılacağı gösterilir.

Bu makalede, [konak havuzlarınızın](create-host-pools-azure-marketplace.md) zaten Windows sanal masaüstü ortamınızda bir veya daha fazla kiracıda gruplandırıldığı varsayılır. Kiracılar ayarlama hakkında bilgi edinmek için bkz. [Windows sanal masaüstü 'nde kiracı oluşturma](tenant-setup-azure-active-directory.md) ve [Tech Community blog gönderimiz](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

Bu kılavuzdaki yönergeler özellikle Windows sanal masaüstü kullanıcıları içindir. Azure NetApp Files ayarlama ve Windows sanal masaüstü dışında FSLogix profil kapsayıcıları oluşturma hakkında daha genel rehberlik arıyorsanız, bkz. Kurulum [Azure NetApp Files ve NFS birimi oluşturma hızlı başlangıç](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>Bu makale Azure NetApp Files paylaşıma erişimin güvenliğini sağlamaya yönelik en iyi yöntemleri kapsamamaktadır.

>[!NOTE]
>Azure 'daki farklı FSLogix profili kapsayıcı depolama seçenekleri hakkında daha fazla bilgi arıyorsanız, bkz. [FSLogix profil kapsayıcıları Için depolama seçenekleri](store-fslogix-profile.md).

## <a name="prerequisites"></a>Ön koşullar

Bir konak havuzu için bir FSLogix profil kapsayıcısı oluşturabilmeniz için öncelikle şunları yapmanız gerekir:

- Windows sanal masaüstü 'Nü ayarlama ve yapılandırma
- Windows sanal masaüstü konak havuzu sağlama
- [Azure NetApp Files aboneliğinizi etkinleştirme](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)

## <a name="set-up-your-azure-netapp-files-account"></a>Azure NetApp Files hesabınızı ayarlama

Başlamak için bir Azure NetApp Files hesabı ayarlamanız gerekir.

1. [Azure portalında](https://portal.azure.com) oturum açın. Hesabınızın katkıda bulunan veya yönetici izinlerine sahip olduğundan emin olun.

2. Azure Cloud Shell açmak için arama çubuğunun sağ tarafındaki **Azure Cloud Shell simgesini** seçin.

3. Azure Cloud Shell açıldıktan sonra **PowerShell**' i seçin.

4. İlk kez Azure Cloud Shell kullanıyorsanız, aynı abonelikte bir depolama hesabı oluşturun Azure NetApp Files ve Windows sanal masaüstünüzü koruyabilirsiniz.

   ![Pencerenin alt kısmındaki depolama Oluştur düğmesine sahip depolama hesabı penceresi kırmızı renkle vurgulanır.](media/create-storage-button.png)

5. Azure Cloud Shell yüklediğinde, aşağıdaki iki cmdlet 'i çalıştırın.

   ```powershell
   az account set --subscription <subscriptionID>
   ```

   ```powershell
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Pencerenin sol tarafında **tüm hizmetler**' i seçin. Menünün üst kısmında görüntülenen arama kutusuna **Azure NetApp Files** girin.

   ![Tüm hizmetler arama kutusuna "Azure NetApp Files" girerek kullanıcının ekran görüntüsü. Arama sonuçları Azure NetApp Files kaynağını gösterir.](media/azure-netapp-files-search-box.png)


7. Arama sonuçlarında **Azure NetApp Files** ' yi seçin ve ardından **Oluştur**' u seçin.

8. **Ekle** düğmesini seçin.
9. **Yeni NetApp hesabı** dikey penceresi açıldığında, aşağıdaki değerleri girin:

    - **Ad**Için NetApp hesap adınızı girin.
    - **Abonelik**için, açılan menüden 4. adımda ayarladığınız depolama hesabı için aboneliği seçin.
    - **Kaynak grubu**için, açılan menüden var olan bir kaynak grubunu seçin ya da **Yeni oluştur**' u seçerek yeni bir tane oluşturun.
    - **Konum**için, açılan menüden NetApp hesabınız için bölge seçin. Bu bölge, oturum ana bilgisayar VM 'leriniz ile aynı bölgede olmalıdır.

   >[!NOTE]
   >Azure NetApp Files Şu anda bölgeler arasında bir birimin bağlanmasını desteklememektedir.

10. İşiniz bittiğinde, Newtapp hesabınızı oluşturmak için **Oluştur** ' u seçin.

## <a name="create-a-capacity-pool"></a>Kapasite havuzu oluşturma

Sonra yeni bir kapasite havuzu oluşturun: 

1. Azure NetApp Files menüsüne gidin ve yeni hesabınızı seçin.
2. Hesap menünüzde depolama hizmeti altında **Kapasite havuzları** ' nı seçin.
3. **Havuz Ekle**' yi seçin.
4. **Yeni kapasite havuzu** dikey penceresi açıldığında, aşağıdaki değerleri girin:

    - **Ad**için yeni kapasite havuzu için bir ad girin.
    - **Hizmet düzeyi**için, açılan menüden istediğiniz değeri seçin. Çoğu ortam için **Premium** önerilir.
       >[!NOTE]
       >Premium ayarı, 256 MBps olan Premium hizmet düzeyi için kullanılabilir en düşük aktarım hızını sağlar. Üretim ortamı için bu aktarım hızını ayarlamanız gerekebilir. Son verimlilik, [üretilen iş sınırları](../azure-netapp-files/azure-netapp-files-service-levels.md)' nda açıklanan ilişkiye dayanır.
    - **Boyut (TiB)** için, gereksinimlerinize en uygun kapasite havuzu boyutunu girin. En küçük boyut 4 TiB 'dir.

5. İşiniz bittiğinde **Tamam**' ı seçin.

## <a name="join-an-active-directory-connection"></a>Active Directory bağlantısına katılarak

Bundan sonra bir Active Directory bağlantısına katılmanız gerekir.

1. Sayfanın sol tarafındaki menüden **Active Directory bağlantıları** ' nı **seçin, sonra birleştir düğmesini seçerek** **Active Directory ekleyin** sayfasını açın.

   ![Active Directory bağlantıları menüsünü Birleştir menüsünün ekran görüntüsü.](media/active-directory-connections-menu.png)

2. Bir bağlantıya katmak için **joın Active Directory** sayfasına aşağıdaki değerleri girin:

    - **BIRINCIL DNS**için, ortamınızda etki alanı adını ÇÖZEBILECEK DNS sunucusunun IP adresini girin.
    - **Etki alanı**için tam etki alanı adını (FQDN) girin.
    - **SMB sunucusu (bilgisayar hesabı) ön eki**için, bilgisayar hesap adına eklemek istediğiniz dizeyi girin.
    - **Kullanıcı adı**için, etki alanına katılmayı gerçekleştirme izinlerine sahip hesabın adını girin.
    - **Parola**için hesabın parolasını girin.

  >[!NOTE]
  >[Active Directory bir bağlantıya katılarak](create-fslogix-profile-container.md#join-an-active-directory-connection) oluşturduğunuz bilgisayar hesabının, **bilgisayarlar** veya kuruluşunuzun **ilgili OU**altında etki alanı denetleyicinizde görüntülendiğini doğrulamak en iyi uygulamadır.

## <a name="create-a-new-volume"></a>Yeni birim oluştur

Ardından, yeni bir birim oluşturmanız gerekir.

1. **Birimler**' i seçin ve ardından **Birim Ekle**' yi seçin.

2. **Birim oluştur** dikey penceresi açıldığında, aşağıdaki değerleri girin:

    - **Birim adı**için yeni birim için bir ad girin.
    - **Kapasite havuzu**için, açılan menüden yeni oluşturduğunuz kapasite havuzunu seçin.
    - **Kota (GiB)** için ortamınıza uygun birim boyutunu girin.
    - **Sanal ağ**için, açılan menüden etki alanı denetleyicisiyle bağlantısı olan var olan bir sanal ağı seçin.
    - **Alt ağ**altında **Yeni oluştur**' u seçin. Bu alt ağın Azure NetApp Files için temsilci olarak olacağını aklınızda bulundurun.

3.  **İleri ' yi seçin: protokol \>\>** protokol sekmesini açın ve birim erişim parametrelerinizi yapılandırın.

## <a name="configure-volume-access-parameters"></a>Birim erişim parametrelerini yapılandırma

Birimi oluşturduktan sonra, birim erişim parametrelerini yapılandırın.

1.  Protokol türü olarak **SMB** ' yi seçin.
2.  **Active Directory** açılır menüsündeki yapılandırma altında, ilk olarak [bir Active Directory bağlantısına katılarak](create-fslogix-profile-container.md#join-an-active-directory-connection)bağladığınız dizini seçin. Her abonelik için bir Active Directory sınırı olduğunu aklınızda bulundurun.
3.  **Ad paylaşma** metin kutusuna, oturum ana bilgisayar havuzu ve kullanıcıları tarafından kullanılan paylaşımın adını girin.

4.  Sayfanın alt kısmındaki **gözden geçir + oluştur** ' u seçin. Bu, doğrulama sayfasını açar. Biriminiz başarıyla doğrulandıktan sonra **Oluştur**' u seçin.

5.  Bu noktada, yeni birim dağıtmaya başlayacaktır. Dağıtım tamamlandıktan sonra Azure NetApp Files paylaşma kullanabilirsiniz.

6.  Bağlama yolunu görmek için **Kaynağa Git** ' i seçin ve Genel Bakış sekmesinde arama yapın.

    ![Bağlama yolunu işaret eden kırmızı bir ok ile genel bakış ekranının ekran görüntüsü.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Oturum ana bilgisayarında FSLogix yapılandırma sanal makineleri (VM 'Ler)

Bu bölüm, bir [dosya paylaşımının kullanıldığı bir konak havuzu için profil kapsayıcısı oluşturma](create-host-pools-user-profile.md)tabanlıdır.

1. Hala oturum ana bilgisayar VM 'sinde uzak durumdayken [FSLogix Agent. zip dosyasını indirin](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) .

2. İndirilen dosyayı sıkıştırmayı açın.

3. Dosyasında **x64** > **sürümleri** ' ne gidin ve **Fslogixappssetup. exe**' yi çalıştırın. Yükleme menüsü açılır.

4.  Ürün anahtarınız varsa, ürün anahtarı metin kutusuna girin.

5. **Lisans hüküm ve koşullarını kabul**ediyorum seçeneğinin yanındaki onay kutusunu işaretleyin.

6. **Yükle**’yi seçin.

7. Aracının yüklü olduğunu onaylamak için, **C:\\Program Files\\FSLogix\\uygulamaları ' na** gidin.

8. Başlat menüsünde, **Regedit** komutunu yönetici olarak çalıştırın.

9. **\\hkey_local_machıne\\software\\FSLogix bilgisayarına**gidin.

10. **Profiller**adlı bir anahtar oluşturun.

11.  **1**veri değerine ayarlanmış bir **REG_DWORD** türü ile **etkin** adlı bir değer oluşturun.

12. **Vhdlocations** adlı bir değeri **çok** dizeli bir türle oluşturun ve veri değerini Azure NetApp Files paylaşımının URI 'si olarak ayarlayın.

## <a name="assign-users-to-session-host"></a>Kullanıcıları oturum konağına atama

1. **PowerShell ISE** 'yi yönetici olarak açın ve Windows sanal masaüstü 'nde oturum açın.

2. Aşağıdaki cmdlet 'leri çalıştırın:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Kimlik bilgileri istendiğinde, Windows sanal masaüstü kiracısında kiracı Oluşturucu veya RDS sahip/RDS katılımcısı rollerine sahip kullanıcı için kimlik bilgilerini girin.

4. Bir kullanıcıyı uzak masaüstü grubuna atamak için aşağıdaki cmdlet 'leri çalıştırın:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Kullanıcıların Azure NetApp dosya paylaşımında erişebildiğinizden emin olun

1. Internet tarayıcınızı açın ve <https://rdweb.wvd.microsoft.com/webclient/index.html>gidin.

2. Uzak Masaüstü grubuna atanan bir kullanıcının kimlik bilgileriyle oturum açın.

3. Kullanıcı oturumunu kurduktan sonra, Azure portal bir yönetici hesabıyla oturum açın.

4. **Azure NetApp Files**açın, Azure NetApp Files hesabınızı seçin ve ardından **birimler**' i seçin. Birimler menüsü açıldıktan sonra karşılık gelen birimi seçin.

   ![Daha önce birimler düğmesi seçili Azure portal daha önce ayarladığınız NetApp hesabının ekran görüntüsü.](media/netapp-account.png)

5. **Genel bakış** sekmesine gidin ve FSLogix profil kapsayıcısının boşluk kullandığını doğrulayın.

6. Uzak Masaüstü 'Nü kullanarak konak havuzunun herhangi bir sanal makine bölümüne doğrudan bağlanın ve **dosya gezginini açın.** Sonra **bağlama yoluna** gidin (aşağıdaki örnekte, bağlama yolu \\\\ANF-SMB-3863.gt1107.onmicrosoft.com\\ANF-Vol).

   Bu klasör içinde, aşağıdaki örnekte olduğu gibi bir profil VHD (veya VHDX) olmalıdır.

   ![Bağlama yolundaki klasörün içindekilerin ekran görüntüsü. İçinde, "Profile_ssbb" adlı tek bir VHD dosyası.](media/mount-path-folder.png)

## <a name="next-steps"></a>Sonraki adımlar

FSLogix profil kapsayıcılarını kullanarak bir kullanıcı profili oluşturabilirsiniz. Yeni kapsayıcılarınız ile Kullanıcı profili paylaşımları oluşturmayı öğrenmek için bkz. bir [dosya paylaşımı kullanarak bir konak havuzu için profil kapsayıcısı oluşturma](create-host-pools-user-profile.md).
