---
title: Azure Lab Services Labs 'i yönetme | Microsoft Docs
description: Bir sınıf Laboratuvarı oluşturma ve yapılandırma, tüm laboratuvarları görüntüleme, kayıt bağlantısını laboratuar kullanıcısı ile paylaşma veya bir laboratuvarı silme hakkında bilgi edinin.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c6acb9609abac15b9ff92250e3d5d44c585881cc
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481812"
---
# <a name="manage-labs-in-azure-lab-services"></a>Azure Lab Services laboratuvarları yönetme 
Bu makalede bir sınıf Laboratuvarı oluşturma ve silme açıklanmaktadır. Ayrıca laboratuvar hesabındaki tüm laboratuvarları nasıl görüntüleyekullanabileceğinizi gösterir. 

## <a name="prerequisites"></a>Önkoşullar
Bir laboratuvar hesabında sınıf laboratuvarı ayarlamak için ilgili laboratuvar hesabında **Laboratuvar Oluşturan** rolünün üyesi olmanız gerekir. Laboratuvar hesabını oluşturmak için kullandığınız hesap otomatik olarak bu role eklenir. Laboratuvar sahibi, şu makaledeki adımları kullanarak Laboratuvar Oluşturan rolüne kullanıcı ekleyebilir: [Laboratuvar Oluşturan rolüne kullanıcı ekleme](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Sınıf laboratuvarı oluşturma

1. [Azure Lab Services web sitesine](https://labs.azure.com) gidin.
1. **Oturum aç**’ı seçip kimlik bilgilerinizi girin. Laboratuvar hesabındaki **Laboratuvar Oluşturucu** rolünün bir üyesi olan BIR **Kullanıcı kimliği** seçin veya girin ve parola girin. Azure Lab Services, kuruluş hesaplarını ve Microsoft hesaplarını destekler. 
1. **Yeni laboratuvar**' ı seçin. 
    
    ![Sınıf laboratuvarı oluşturma](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. **Yeni Laboratuvar** penceresinde aşağıdaki eylemleri gerçekleştirin: 
    1. Laboratuvarınız için bir **ad** belirtin. 
    1. Sınıf için ihtiyacınız **olan sanal makinelerin boyutunu** seçin. Kullanılabilir boyutların listesi için [VM boyutları](#vm-sizes) bölümüne bakın. 
    1. Derslik Laboratuvarı için kullanmak istediğiniz **sanal makine görüntüsünü** seçin. Bir Linux görüntüsü seçerseniz, **Uzak Masaüstü bağlantısını etkinleştirme** seçeneğini görürsünüz. Ayrıntılar için bkz. [Linux için Uzak Masaüstü bağlantısını etkinleştirme](how-to-enable-remote-desktop-linux.md).

        Laboratuvar hesabı sahibi kimlik bilgilerini kullanarak oturum açtıysanız, laboratuvar için daha fazla görüntü etkinleştirme seçeneği görüntülenir. Daha fazla bilgi için bkz. [Laboratuvar oluşturma sırasında görüntüleri etkinleştirme](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    1. Sayfada gösterildiği **saat başına toplam fiyatı** gözden geçirin. 
    1. **Kaydet**’i seçin.

        !["Yeni laboratuvar" penceresini gösteren ekran görüntüsü.](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Laboratuvar hesabı laboratuvar [Oluşturucu 'nın laboratuvar konumu](allow-lab-creator-pick-lab-location.md) seçeneğini seçmesine izin verecek şekilde yapılandırıldıysa laboratuvarınız için bir konum seçme seçeneği görürsünüz. 
4. **Sanal makine kimlik bilgileri** sayfasında, laboratuvardaki tüm VM 'ler için varsayılan kimlik bilgilerini belirtin.
    1. Laboratuvardaki tüm VM'ler için **kullanıcı adını** belirtin.
    2. Kullanıcının **parolasını** belirtin. 

        > [!IMPORTANT]
        > Kullanıcı adını ve parolayı not edin. Bunlar tekrar gösterilmeyecektir.
    3. Öğrencilerin kendi parolalarını ayarlamanızı istiyorsanız **tüm sanal makineler için aynı parolayı kullan** seçeneğini devre dışı bırakın. Bu adım **isteğe bağlıdır**. 

        Bir eğitimci, laboratuvardaki tüm VM 'Ler için aynı parolayı kullanmayı seçebilir veya öğrencilerin VM 'Leri için parola değiştirmesine izin verebilir. Varsayılan olarak, bu ayar Ubuntu hariç tüm Windows ve Linux görüntüleri için etkinleştirilmiştir. **Ubuntu** VM ' yi seçtiğinizde, bu ayar devre dışıdır, bu nedenle ilk kez oturum açtıklarında öğrencilerin bir parola ayarlaması istenir.  

        ![Yeni laboratuvar penceresi](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Ardından, **sanal makine kimlik bilgileri** sayfasında **İleri** ' yi seçin. 
5. **Laboratuvar ilkeleri** sayfasında, aşağıdaki adımları uygulayın:
    1. Laboratuvar için zamanlanan sürenin dışında her bir kullanıcı için ayrılan saat sayısını (**her bir kullanıcı için kota**) girin. 
    2. **Sanal makinelerin otomatik olarak kapatılmasını** seçeneği için, kullanıcının BAĞLANTıSı kesildiğinde VM 'nin otomatik olarak kapatılmasını isteyip istemediğinizi belirtin. Ayrıca, sanal makinenin otomatik olarak kapanmadan önce kullanıcının yeniden bağlanmasına ne kadar süre beklemesi gerektiğini de belirtebilirsiniz. Daha fazla bilgi için bkz. [bağlantı kesildiğinde VM 'lerin otomatik kapatılmasını etkinleştirme](how-to-enable-shutdown-disconnect.md).
    3. Ardından **Son**'u seçin. 

        ![Her Kullanıcı için kota](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Şablon VM oluşturma 'nın durumunu gösteren aşağıdaki ekranı görmeniz gerekir. Laboratuvar şablonunun oluşturulması 20 dakika sürebilir. 

    ![Şablon VM oluşturma durumu](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. **Şablon** sayfasında, aşağıdaki adımları uygulayın: Bu adımlar öğretici için **isteğe bağlıdır** .

    1. **Bağlan**'ı seçerek şablon VM'sine bağlanın. Bir Linux şablon sanal makinesi ise, SSH veya GUI Uzak Masaüstü kullanarak bağlanmak isteyip istemediğinizi seçersiniz.  GUI uzak masaüstü kullanmak için ek kurulum gereklidir. Daha fazla bilgi için bkz. [Linux sanal makineleri için grafik uzak masaüstünü etkinleştirme](how-to-use-remote-desktop-linux-student.md) .
    1. VM 'nin parolasını sıfırlamak için **Parolayı Sıfırla** ' yı seçin. 
    1. Şablon VM'sinde yazılım yükleme ve yapılandırma işlemlerini gerçekleştirin. 
    1. VM'yi **durdurun**.  
    1. Şablon için bir **açıklama** girin
9.  **Şablon** sayfasında, araç çubuğunda **Yayımla** ' yı seçin. 

    ![Şablon Yayımla düğmesi](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Yayımlama işlemini geri alamazsınız. 
10. **Şablonu Yayımla** sayfasında, laboratuvarda oluşturmak istediğiniz sanal makine sayısını girin ve ardından **Yayımla**' yı seçin. 

    ![Şablon yayımlama-sanal makine sayısı](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Şablonu **Yayımlama durumunu** sayfada görürsünüz. Bu işlemin tamamlanması bir saat sürebilir. 

    ![Şablonu yayımlama - ilerleme](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Sol taraftaki menüden sanal makineler ' i seçerek veya sanal makineler Kutucuğu ' nı seçerek **sanal makine havuzu** sayfasına geçin. **Atanmamış** durumda olan sanal makineleri görtığınızdan emin olun. Bu VM’ler henüz bir öğrenciye atanmamıştır. Bu makinelerin durumu **Durduruldu** olmalıdır. Bu sayfadan bir öğrenci VM'sini başlatabilir, VM'ye bağlanabilir, VM'yi durdurabilir ve VM'yi silebilirsiniz. VM'leri bu sayfadan başlatabilir veya öğrencilerinizin başlatmasını sağlayabilirsiniz. 

    ![Durdurulmuş durumdaki sanal makineler](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Bu sayfada aşağıdaki görevleri gerçekleştirebilirsiniz (öğreticide bu adımları uygulayın. Bu adımlar yalnızca bilgileriniz içindir.): 
    
    1. Laboratuvar kapasitesini (laboratuvardaki VM sayısı) değiştirmek için araç çubuğunda **Laboratuvar kapasitesi** ' ni seçin.
    2. Tüm VM 'Leri aynı anda başlatmak için araç çubuğunda **Tümünü Başlat** ' ı seçin. 
    3. Belirli bir sanal makineyi başlatmak için, **durumunda** aşağı oku seçin ve ardından **Başlat**' ı seçin. Ayrıca, ilk sütunda bir VM seçerek ve ardından araç çubuğundan **Başlat** ' ı seçerek bir VM başlatabilirsiniz.                

### <a name="vm-sizes"></a>VM boyutları  

| Boyut | Çekirdekler | RAM | Açıklama | 
| ---- | ----- | --- | ----------- | 
| Küçük | 2 | 3,5 GB | Bu boyut, komut satırı, Web tarayıcısı, düşük trafikli web sunucuları, küçük ve orta ölçekli veritabanları için idealdir. |
| Orta | 4 | 7 GB | Bu boyut, ilişkisel veritabanları, bellek içi önbelleğe alma ve analiz için idealdir | 
| Orta (Iç Içe sanallaştırma) | 4 | 16 GB | Bu boyut, ilişkisel veritabanları, bellek içi önbelleğe alma ve analiz için idealdir. Bu boyut, iç içe sanallaştırmayı da destekler. <p>Bu boyut, her öğrencinin birden çok VM gerektiren senaryolarda kullanılabilir. Eğitimciler, sanal makine içinde birkaç küçük boyutlu iç içe sanal makine ayarlamak için iç içe sanallaştırmayı kullanabilir. </p> |
| Küçük GPU (Işlem) | 6 | 56 GB | <p>Bu boyut, yapay zeka ve derin öğrenme uygulamaları gibi işlem yoğunluğu ve yoğun ağ kullanımı gerektiren uygulamalar için idealdir.</p><p>Azure Lab Services, GPU görüntüleriyle bir laboratuvar oluşturduğunuzda sizin için gerekli GPU sürücülerini otomatik olarak yükleyip yapılandırır. </p> | 
| Küçük GPU (görselleştirme) | 6 | 56 GB | Bu boyut, OpenGL ve DirectX gibi çerçeveleri kullanarak uzaktan görselleştirme, akış, oyun ve kodlama için idealdir. | 
| Büyük | 8 | 16 GB | Bu boyut daha hızlı CPU, daha iyi yerel disk performansı, büyük veritabanları, büyük bellek önbellekler gerektiren uygulamalar için idealdir. |
| Büyük (Iç Içe sanallaştırma) | 8 | 32 GB | Bu boyut daha hızlı CPU, daha iyi yerel disk performansı, büyük veritabanları, büyük bellek önbellekler gerektiren uygulamalar için idealdir. Bu boyut, iç içe sanallaştırmayı da destekler. |  
| Orta ölçekli GPU (görselleştirme) | 12 | 112 GB | Bu boyut, OpenGL ve DirectX gibi çerçeveleri kullanarak uzaktan görselleştirme, akış, oyun ve kodlama için idealdir. | 

> [!NOTE]
> Bir sınıf Laboratuvarı oluştururken listede bu sanal makine boyutlarından bazılarını görmeyebilirsiniz. Liste, laboratuvarın konumunun geçerli kapasitesine göre doldurulur. Laboratuvar hesabı Oluşturucusu laboratuvar [oluşturucularının laboratuvar için bir konum seçmesine izin veriyorsa](allow-lab-creator-pick-lab-location.md), laboratuvar için farklı bir konum seçmeyi DENEYEBILIR ve VM boyutunun kullanılabilir olup olmadığını görebilirsiniz. 

## <a name="view-all-labs"></a>Tüm laboratuvarları görüntüleme

1. [Azure Lab Services Portal](https://labs.azure.com)' a gidin.
1. **Oturum aç**'ı seçin. Laboratuvar hesabındaki **Laboratuvar Oluşturucu** rolünün bir üyesi olan BIR **Kullanıcı kimliği** seçin veya girin ve parola girin. Azure Lab Services, kuruluş hesaplarını ve Microsoft hesaplarını destekler. 

    [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]
1. Seçilen laboratuvar hesabındaki tüm laboratuvarları görtığınızdan emin olun. Laboratuvarın kutucuğunda, laboratuvardaki sanal makine sayısını ve her kullanıcı için kotayı (zamanlanan sürenin dışında) görürsünüz.

    ![Tüm laboratuvarlar](./media/how-to-manage-classroom-labs/all-labs.png)
1. En üstteki açılan listeyi kullanarak farklı bir laboratuvar hesabı seçin. Laboratuvarları seçilen laboratuvar hesabında görürsünüz. 

## <a name="delete-a-classroom-lab"></a>Sınıf Laboratuvarı silme

1. Laboratuvarın kutucuğunda, köşede üç nokta (...) ve ardından **Sil**' i seçin. 

    ![Sil düğmesi](./media/how-to-manage-classroom-labs/delete-button.png)
1. Silmeye devam etmek için **Laboratuvarı Sil** Iletişim kutusunda **Sil** ' i seçin. 

## <a name="switch-to-another-classroom-lab"></a>Başka bir sınıf laboratuvarına geç

Geçerli olan başka bir sınıf laboratuvarına geçiş yapmak için, en üstteki laboratuvar hesabındaki laboratuvarların açılan listesini seçin.

![Üstteki aşağı açılan listeden laboratuvar listesini seçin](./media/how-to-manage-classroom-labs/switch-lab.png)

Bu açılan listede **Yeni Laboratuvarı** kullanarak yeni bir laboratuvar da oluşturabilirsiniz. 

> [!NOTE]
> Laboratuvarları yönetmek için az. LabServices PowerShell modülünü (Önizleme) da kullanabilirsiniz. Daha fazla bilgi için [GitHub 'Daki az. LabServices giriş sayfasına](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)bakın.

Farklı bir laboratuvar hesabına geçmek için, laboratuvar hesabının yanındaki açılan eklentiyi seçin ve diğer laboratuvar hesabını seçin. 

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Laboratuvar sahibi olarak, şablonları ayarlama ve yayımlama](how-to-create-manage-template.md)
- [Laboratuvar sahibi olarak, bir laboratuvarın kullanımını yapılandırma ve denetleme](how-to-configure-student-usage.md)
- [Laboratuvar kullanıcısı olarak, erişim laboratuvarları](how-to-use-classroom-lab.md)

