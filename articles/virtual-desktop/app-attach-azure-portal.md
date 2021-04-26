---
title: Windows sanal masaüstü MALTı uygulama iliştirme portalı-Azure
description: Azure portal kullanarak Windows sanal masaüstü için MSIX uygulama iliştirme 'yi ayarlama.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21dbab6c8d4fb12fe79434a6994dd7f5b8a49190
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502717"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Azure portalla MSIX uygulama iliştirmeyi ayarlama

Bu makalede, bir Windows sanal masaüstü ortamında MSIX uygulama iliştirme 'yi ayarlama işleminde size yol gösterilir.

## <a name="requirements"></a>Gereksinimler

İşte MALTı uygulama eklemeyi yapılandırmak için gerekenler şunlardır:

- Çalışan bir Windows sanal masaüstü dağıtımı. Windows sanal masaüstü 'Nü (klasik) dağıtmayı öğrenmek için bkz. [Windows sanal masaüstü 'nde kiracı oluşturma](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Windows sanal masaüstü Azure Resource Manager tümleştirme ile nasıl dağıtılacağını öğrenmek için, bkz. [Azure Portal bir konak havuzu oluşturma](./create-host-pools-azure-marketplace.md).
- En az bir etkin oturum ana bilgisayarı olan bir Windows sanal masaüstü konak Havuzu.
- MSIX paketleme aracı.
- Bir dosya paylaşımında karşıya yüklenen bir MSIX görüntüde genişletilen, MSIX paketlenmiş bir uygulama.
- Windows sanal masaüstü dağıtımınızda MSIX paketinin depolanacağı bir dosya paylaşımıdır.
- MSIX görüntüsünü karşıya yüklediğiniz dosya paylaşımının konak havuzundaki tüm sanal makineler (VM) için de erişilebilir olması gerekir. Kullanıcılar görüntüye erişmek için salt okuma izinlerine sahip olacaktır.
- Sertifikaya genel olarak güvenilmiyorsa, [sertifikaları Install](app-attach.md#install-certificates)' daki yönergeleri izleyin.

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>MSIX uygulama iliştirme uygulamaları için otomatik güncelleştirmeleri kapat

Başlamadan önce, MSIX uygulama iliştirme uygulamaları için otomatik güncelleştirmeleri devre dışı bırakmanız gerekir. Otomatik güncelleştirmeleri devre dışı bırakmak için, yükseltilmiş bir komut isteminde aşağıdaki komutları çalıştırmanız gerekir:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

>[!NOTE]
>Hyper-V ' i etkinleştirdikten sonra sanal makineyi yeniden başlatmanızı öneririz.

## <a name="configure-the-msix-app-attach-management-interface"></a>MSIX uygulama iliştirme yönetim arabirimini yapılandırma

Daha sonra, Azure portal için MSIX uygulama iliştirme yönetim arabirimini indirip yapılandırmanız gerekir.

Yönetim arabirimini ayarlamak için:

1. [Azure Portal açın](https://portal.azure.com).
2. Uzantıyı güvenilir olarak düşünmenizi isteyip istemediğinizi soran bir istem alırsanız, **Izin ver**' i seçin.

      > [!div class="mx-imgBorder"]
      > ![Güvenilmeyen uzantılar penceresinin ekran görüntüsü. "İzin ver" kırmızı renkle vurgulanır.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Konak havuzuna bir MSIX görüntüsü ekleme

Ardından, ana bilgisayar havuzunuza MSIX görüntüsünü eklemeniz gerekir.

MSIX görüntüsünü eklemek için:

1. Azure portalını açın.

2. Arama çubuğuna **Windows sanal masaüstü** ' nü girip hizmet adını seçin.

      > [!div class="mx-imgBorder"]
      > ![Azure portal arama çubuğu açılır menüsünden "Windows sanal masaüstü" seçeneğini belirleyerek bir kullanıcının ekran görüntüsü. "Windows sanal masaüstü" kırmızı renkle vurgulanır.](media/find-and-select.png)

3. MALTıDAN uygulamayı yerleştireceğiniz konak havuzunu seçin.

4. Ana bilgisayar havuzuna eklenmiş olan tüm **Msix paketleriyle** veri kılavuzunu açmak Için **msix paketi** ' ni seçin.

5. **+ Ekle** ' yi seçerek **Msix paketi Ekle** sekmesini açın.

6. **MSIX paketi Ekle** sekmesinde, aşağıdaki değerleri girin:

      - **Msix görüntü yolu** için dosya paylaşımındaki msix görüntüsünü işaret eden GEÇERLI bir UNC yolu girin. (Örneğin, `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` .) İşiniz bittiğinde, yolun geçerli olup olmadığını denetlemek için MSIX kapsayıcısını sorgulanamıyor **Ekle** ' yi seçin.

      - **Msix paketi** için, açılan menüden ilgili msix paket adını seçin. Bu menü yalnızca, **Msix görüntü yolunda** geçerli bir görüntü yolu girdiyseniz doldurulur.

      - **Paket uygulamaları** için, listenin maltıdaki kullanıcılar için kullanılabilir olmasını istediğiniz tüm msix uygulamalarını içerdiğinden emin olun.

      - İsteğe bağlı olarak, paketinizin Kullanıcı dağıtımlarınızda daha kolay bir Kullanıcı olmasını istiyorsanız bir **görünen ad** girin.

      - **Sürümün** doğru sürüm numarasına sahip olduğundan emin olun.

      - Kullanmak istediğiniz **kayıt türünü** seçin. Kullandığınız bir tane, gereksinimlerinize bağlıdır:

    - **Isteğe bağlı kayıt** , Kullanıcı uygulamayı başlatana kadar msix uygulamasının tam kaydını erteler. Bu, kullanmanızı önerdiğimiz kayıt türüdür.

    - Yalnızca Kullanıcı oturum açılırken kayıtları **engellemeye oturum açın** . Kullanıcılar için daha uzun oturum açma süreleriyle sonuçlanabileceğinden, bu türü önermiyoruz.

7.  **Durum** için tercih ettiğiniz durumu seçin.
    -  **Etkin** durum, kullanıcıların paketle etkileşime geçmesini sağlar.
    -  **Etkin olmayan** durum Windows sanal masaüstünün paketi yoksaymasına ve kullanıcılara sunmamasını sağlar.

8. İşiniz bittiğinde **Ekle**' yi seçin.

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX uygulamalarını bir uygulama grubuna yayımlayın

Ardından, uygulamaları pakete yayımlamanız gerekir. Bunu hem masaüstü hem de uzak uygulama uygulama grupları için yapmanız gerekir.

Zaten bir MSIX görüntünüz varsa, [msix uygulamalarını bir uygulama grubuna yayımlamaya](#publish-msix-apps-to-an-app-group)devam edin. Eski uygulamaları test etmek istiyorsanız, eski uygulamayı bir MSIX paketine dönüştürmek için [BIR sanal makinede bulunan bir masaüstü yükleyicisinden msix paketi oluşturma](/windows/msix/packaging-tool/create-app-package-msi-vm/) bölümündeki yönergeleri izleyin.

Uygulamaları yayımlamak için:

1. Windows Sanal Masaüstü kaynak sağlayıcısında **uygulama grupları** sekmesini seçin.

2. Uygulamaları yayımlamak istediğiniz uygulama grubunu seçin.

   >[!NOTE]
   >MSIX uygulaması, her iki uzak uygulama ve Masaüstü uygulama grubuna ek olarak MALTıYA bağlanabilir.

3. Uygulama grubunda olduktan sonra **uygulamalar** sekmesini seçin. **Uygulamalar** Kılavuzu, uygulama grubu içindeki tüm mevcut uygulamaları görüntüler.

4. **+ Ekle** ' yi seçerek **Uygulama Ekle** sekmesini açın.

      > [!div class="mx-imgBorder"]
      > ![Kullanıcının + Ekle seçeneğini belirleyerek uygulama Ekle sekmesini açmak için bir ekran görüntüsü](media/select-add.png)

5. **Uygulama kaynağı** için uygulamanızın kaynağını seçin.
    - Bir masaüstü uygulama grubu kullanıyorsanız, **Msix paketi**' ni seçin.
      
      > [!div class="mx-imgBorder"]
      > ![Uygulama kaynağı açılır menüsünden MSIX paketini seçen bir müşterinin ekran görüntüsü. MSIX paketi kırmızı renkle vurgulanır.](media/select-source.png)
    
    - Uzak bir uygulama grubu kullanıyorsanız, aşağıdaki seçeneklerden birini seçin:
        
        - Başlat menüsü
        - Uygulama yolu
        - MSIX paketi

    - **Uygulama adı** için uygulama için açıklayıcı bir ad girin.

    Aşağıdaki isteğe bağlı özellikleri de yapılandırabilirsiniz:
   
    - **Görünen ad** için, kullanıcılarınızın göreceği paket için yeni bir ad girin.

    - **Açıklama** için uygulama paketinin kısa bir açıklamasını girin.

    - Uzak bir uygulama grubu kullanıyorsanız, bu seçenekleri de yapılandırabilirsiniz:

        - **Simge yolu**
        - **Simge dizini**
        - **Web akışında göster**

6. İşiniz bittiğinde **Kaydet**'i seçin.

>[!NOTE]
>Bir kullanıcı aynı konak havuzundan uzak uygulama grubuna ve Masaüstü uygulama grubuna atandığında, Masaüstü uygulama grubu akışta görüntülenir.

## <a name="assign-a-user-to-an-app-group"></a>Bir uygulama grubuna kullanıcı atama

Bir uygulama grubuna MSIX uygulama atadıktan sonra kullanıcılara onlara erişim vermeniz gerekir. Yayımlanan MSIX uygulamalarıyla bir uygulama grubuna kullanıcılar veya Kullanıcı grupları ekleyerek erişim atayabilirsiniz. Kullanıcılarınızı bir uygulama grubuna atamak için [Azure Portal ile uygulama gruplarını yönetme](manage-app-groups.md) bölümündeki yönergeleri izleyin.

>[!NOTE]
>Genel Önizleme sırasında uzak uygulamaları test ettiğinizde, MSIX uygulama iliştirme uzak uygulamaları akışdan kaybolabilir. Değerlendirme ortamında kullandığınız konak havuzu, üretim ortamındaki bir RD Aracısı tarafından sunulduğundan, uygulamalar görüntülenmez. Üretim ortamındaki RD Aracısı, MALTı uygulama iliştirme uzak uygulamaları mevcut olmadığından, uygulamalar akışta görünmez.

## <a name="change-msix-package-state"></a>MSIX paket durumunu değiştir

Ardından, pakette ne yapmak istediğinize bağlı olarak, MSIX paket durumunu **etkin** veya **etkin değil** olarak değiştirmeniz gerekir. Etkin paketler, kullanıcılarınızın yayımlandıktan sonra etkileşime girebilecekleri paketlerdir. Etkin olmayan paketler Windows sanal masaüstü tarafından yok sayılır, bu sayede kullanıcılarınız içindeki uygulamalarla etkileşime giremeyeceklerdir.

### <a name="change-state-with-the-applications-list"></a>Uygulamalar listesi ile durumu değiştirme

Paket durumunu uygulamalar listesi ile değiştirmek için:

1. Konak havuzunuza gidin ve **Msix paketi**' ni seçin. Konak havuzundaki tüm mevcut MSIX paketlerinin listesini görmeniz gerekir.

2. Durumunu değiştirmeniz gereken MSIX paketlerini seçin, sonra **Durumu Değiştir**' i seçin.

### <a name="change-state-with-update-package"></a>Güncelleştirme paketiyle durumu değiştir

Paket durumunu bir güncelleştirme paketiyle değiştirmek için:

1. Konak havuzunuza gidin ve **Msix paketi**' ni seçin. Konak havuzundaki tüm mevcut MSIX paketlerinin listesini görmeniz gerekir.

2. Durumu, MSIX paket listesinden değiştirmek istediğiniz paketin adını seçin. Bu, **güncelleştirme paketi** sekmesini açar.

3. **Durum** anahtarını **etkin değil** veya **etkin** olarak değiştirin, sonra Kaydet ' i seçin **.**

## <a name="change-msix-package-registration-type"></a>MSIX paket kayıt türünü değiştir

Paketin kayıt türünü değiştirmek için:

1. **Msix paketi** seçin. Konak havuzundaki tüm mevcut MSIX paketlerinin listesini görmeniz gerekir.

2. **Maltıdaki** **paket adı '** nı seçin Bu işlem, paketi güncelleştirmek için dikey pencereyi açar.

3. **Kayıt türünü** **Isteğe bağlı/oturum açma/** kapatma düğmesi ile Istediğiniz şekilde değiştirin ve Kaydet ' i seçin **.**

## <a name="remove-an-msix-package"></a>MSIX paketini kaldırma

Ana bilgisayar havuzunuzdaki bir MSIX paketini kaldırmak için:

1. **Msix paketi** seçin.  Konak havuzundaki tüm mevcut MSIX paketlerinin listesini görmeniz gerekir.

2. Silmek istediğiniz paketin adının sağ tarafındaki üç noktayı seçin ve **Kaldır**' ı seçin.

## <a name="remove-msix-apps"></a>MSIX uygulamalarını kaldır

Tek bir MALTı uygulamayı paketinizden kaldırmak için:

1. Konak havuzuna gidin ve **uygulama grupları**' nı seçin.

2. MALTıDAN uygulamayı kaldırmak istediğiniz uygulama grubunu seçin.

3. **Uygulamalar** sekmesini açın.

4. Kaldırmak istediğiniz uygulamayı seçin ve **Kaldır**' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu özellikle ilgili topluluk sorularımıza [Windows sanal masaüstü TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)' de sorun.

Windows sanal masaüstü [Geri Bildirim Hub 'ında](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)Windows sanal masaüstü geri bildirimini de bırakabilirsiniz.

Yararlı bulabileceğiniz bazı makaleler aşağıda verilmiştir:

- [MSIX uygulama iliştirme sözlüğü](app-attach-glossary.md)
- [MSIX uygulama iliştirme SSS](app-attach-faq.md)
