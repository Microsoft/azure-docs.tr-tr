---
title: Azure Lab Services sınıf laboratuvarlarını yönetme | Microsoft Docs
description: Bir derslik Laboratuvarı oluşturma ve yapılandırma, tüm sınıf laboratuvarlarını görüntüleme, kayıt bağlantısını laboratuar kullanıcısı ile paylaşma veya bir laboratuvarı silme hakkında bilgi edinin.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 91469f27e1ca86650cf94fde5cff5d1864300183
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606341"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Azure Lab Services sınıf laboratuvarlarını yönetme 
Bu makalede bir sınıf Laboratuvarı oluşturma ve silme açıklanmaktadır. Ayrıca, tüm sınıf laboratuvarlarını bir laboratuvar hesabında nasıl görüntüleyekullanacağınızı gösterir. 

## <a name="prerequisites"></a>Ön koşullar
Bir laboratuvar hesabında sınıf laboratuvarı ayarlamak için ilgili laboratuvar hesabında **Laboratuvar Oluşturan** rolünün üyesi olmanız gerekir. Laboratuvar hesabını oluşturmak için kullandığınız hesap otomatik olarak bu role eklenir. Laboratuvar sahibi, şu makaledeki adımları kullanarak Laboratuvar Oluşturan rolüne kullanıcı ekleyebilir: [Laboratuvar Oluşturan rolüne kullanıcı ekleme](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Sınıf laboratuvarı oluşturma

1. [Azure Lab Services web sitesine](https://labs.azure.com) gidin. Internet Explorer 11 henüz desteklenmiyor. 
2. **Oturum aç**’ı seçip kimlik bilgilerinizi girin. Laboratuvar hesabındaki **Laboratuvar Oluşturucu** rolünün bir üyesi olan BIR **Kullanıcı kimliği** seçin veya girin ve parola girin. Azure Lab Services, kuruluş hesaplarını ve Microsoft hesaplarını destekler. 
3. **Yeni laboratuvar**' ı seçin. 
    
    ![Sınıf laboratuvarı oluşturma](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. **Yeni Laboratuvar** penceresinde aşağıdaki eylemleri gerçekleştirin: 
    1. Laboratuvarınız için bir **ad** belirtin. 
    2. Sınıf için ihtiyacınız **olan sanal makinelerin boyutunu** seçin. Kullanılabilir boyutların listesi için [VM boyutları](#vm-sizes) bölümüne bakın. 
    3. Derslik Laboratuvarı için kullanmak istediğiniz **sanal makine görüntüsünü** seçin. Bir Linux görüntüsü seçerseniz, bunun için Uzak Masaüstü bağlantısını etkinleştirme seçeneğini görürsünüz. Ayrıntılar için bkz. [Linux için Uzak Masaüstü bağlantısını etkinleştirme](how-to-enable-remote-desktop-linux.md).
    4. Sayfada gösterildiği **saat başına toplam fiyatı** gözden geçirin. 
    6. **Kaydet**’i seçin.

        ![Yeni laboratuvar penceresi](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **Sanal makine kimlik bilgileri** sayfasında, laboratuvardaki tüm VM 'ler için varsayılan kimlik bilgilerini belirtin.
    1. Laboratuvardaki tüm VM'ler için **kullanıcı adını** belirtin.
    2. Kullanıcının **parolasını** belirtin. 

        > [!IMPORTANT]
        > Kullanıcı adını ve parolayı not edin. Bunlar tekrar gösterilmeyecektir.
    3. Öğrencilerin kendi parolalarını ayarlamanızı istiyorsanız **tüm sanal makineler için aynı parolayı kullan** seçeneğini devre dışı bırakın. Bu adım **isteğe bağlıdır**. 

        Öğretmen, laboratuvardaki tüm VM 'Ler için aynı parolayı kullanmayı seçebilir veya öğrencilerin VM 'Leri için parola değiştirmesine izin verebilir. Varsayılan olarak, bu ayar Ubuntu hariç tüm Windows ve Linux görüntüleri için etkinleştirilmiştir. **Ubuntu** VM ' yi seçtiğinizde, bu ayar devre dışıdır, bu nedenle ilk kez oturum açtıklarında öğrencilerin bir parola ayarlaması istenir.  

        ![Yeni laboratuvar penceresi](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
        > [!IMPORTANT]
        > Kullanıcı adını ve parolayı not edin. Bunlar tekrar gösterilmeyecektir.    
    4. Ardından, **sanal makine kimlik bilgileri** sayfasında **İleri** ' yi seçin. 
5. **Laboratuvar ilkeleri** sayfasında, her kullanıcı için ayrılan saat sayısını (**her bir kullanıcı için kota**) laboratuvar için zamanlanan süre dışında girin ve ardından **son**' u seçin. 

    ![Her Kullanıcı için kota](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Şablon VM oluşturma 'nın durumunu gösteren aşağıdaki ekranı görmeniz gerekir. Laboratuvar şablonunun oluşturulması 20 dakika sürebilir. 

    ![Şablon VM oluşturma durumu](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. **Şablon** sayfasında, aşağıdaki adımları uygulayın: Bu adımlar öğretici için **isteğe bağlıdır** .

    2. **Bağlan**'ı seçerek şablon VM'sine bağlanın. Bir Linux şablon sanal makinesi ise, SSH veya RDP kullanarak bağlanmak isteyip istemediğinizi seçin (RDP etkinse).
    1. VM 'nin parolasını sıfırlamak için **Parolayı Sıfırla** ' yı seçin. 
    1. Şablon VM'sinde yazılım yükleme ve yapılandırma işlemlerini gerçekleştirin. 
    1. VM'yi **durdurun**.  
    1. Şablon için bir **açıklama** girin
10. **Şablon** sayfasında, araç çubuğunda **Yayımla** ' yı seçin. 

    ![Şablon Yayımla düğmesi](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Yayımlama işlemini geri alamazsınız. 
8. **Şablonu Yayımla** sayfasında, laboratuvarda oluşturmak istediğiniz sanal makine sayısını girin ve ardından **Yayımla**' yı seçin. 

    ![Şablon yayımlama-sanal makine sayısı](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Şablonu **Yayımlama durumunu** sayfada görürsünüz. Bu işlemin tamamlanması bir saat sürebilir. 

    ![Şablonu yayımlama - ilerleme](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Sol taraftaki menüden sanal makineler ' i seçerek veya sanal makineler Kutucuğu ' nı seçerek **sanal makine havuzu** sayfasına geçin. **Atanmamış** durumda olan sanal makineleri görtığınızdan emin olun. Bu VM’ler henüz bir öğrenciye atanmamıştır. Bu makinelerin durumu **Durduruldu** olmalıdır. Bu sayfadan bir öğrenci VM'sini başlatabilir, VM'ye bağlanabilir, VM'yi durdurabilir ve VM'yi silebilirsiniz. VM'leri bu sayfadan başlatabilir veya öğrencilerinizin başlatmasını sağlayabilirsiniz. 

    ![Durdurulmuş durumdaki sanal makineler](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Bu sayfada aşağıdaki görevleri gerçekleştirebilirsiniz (öğreticide bu adımları uygulayın. Bu adımlar yalnızca bilgileriniz içindir.): 
    
    1. Laboratuvar kapasitesini (laboratuvardaki VM sayısı) değiştirmek için araç çubuğunda **Laboratuvar kapasitesi** ' ni seçin.
    2. Tüm VM 'Leri aynı anda başlatmak için araç çubuğunda **Tümünü Başlat** ' ı seçin. 
    3. Belirli bir sanal makineyi başlatmak için, **durumunda**aşağı oku seçin ve ardından **Başlat**' ı seçin. Ayrıca, ilk sütunda bir VM seçerek ve ardından araç çubuğundan **Başlat** ' ı seçerek bir VM başlatabilirsiniz.                

### <a name="vm-sizes"></a>VM boyutları  

| Boyut | Çekirdek | RAM | Açıklama | 
| ---- | ----- | --- | ----------- | 
| Küçük | 2 | 3,5 GB | Bu boyut, komut satırı, Web tarayıcısı, düşük trafikli web sunucuları, küçük ve orta ölçekli veritabanları için idealdir. |
| Orta | 4 | 7 GB | Bu boyut, ilişkisel veritabanları, bellek içi önbelleğe alma ve analiz için idealdir | 
| Orta (Iç Içe sanallaştırma) | 4 | 16 GB | Bu boyut, ilişkisel veritabanları, bellek içi önbelleğe alma ve analiz için idealdir. Bu boyut, iç içe sanallaştırmayı da destekler. <p>Bu boyut, her öğrencinin birden çok VM gerektiren senaryolarda kullanılabilir. Öğretmenler, sanal makine içinde birkaç küçük boyutlu iç içe sanal makine ayarlamak için iç içe sanallaştırmayı kullanabilir. </p> |
| Büyük | 8 | 32 GB | Bu boyut daha hızlı CPU, daha iyi yerel disk performansı, büyük veritabanları, büyük bellek önbellekler gerektiren uygulamalar için idealdir. Bu boyut, iç içe sanallaştırmayı da destekler |  
| Küçük GPU (görselleştirme) | 6 | 56 GB | Bu boyut, OpenGL ve DirectX gibi çerçeveleri kullanarak uzaktan görselleştirme, akış, oyun ve kodlama için idealdir. | 
| Küçük GPU (Işlem) | 6 | 56 GB | Bu boyut, yapay zeka ve derin öğrenme uygulamaları gibi işlem yoğunluğu ve yoğun ağ kullanımı gerektiren uygulamalar için idealdir. | 
| Orta ölçekli GPU (görselleştirme) | 12 | 112 GB | Bu boyut, OpenGL ve DirectX gibi çerçeveleri kullanarak uzaktan görselleştirme, akış, oyun ve kodlama için idealdir. | 

## <a name="view-all-classroom-labs"></a>Tüm sınıf laboratuvarlarını görüntüleyin
1. [Azure Lab Services Portal](https://labs.azure.com)' a gidin.
2. **Oturum aç '** ı seçin. Laboratuvar hesabındaki **Laboratuvar Oluşturucu** rolünün bir üyesi olan BIR **Kullanıcı kimliği** seçin veya girin ve parola girin. Azure Lab Services, kuruluş hesaplarını ve Microsoft hesaplarını destekler. 
3. Seçilen laboratuvar hesabındaki tüm laboratuvarları görtığınızdan emin olun. Laboratuvarın kutucuğunda, laboratuvardaki sanal makine sayısını ve her kullanıcı için kotayı (zamanlanan sürenin dışında) görürsünüz.

    ![Tüm laboratuvarlar](../media/how-to-manage-classroom-labs/all-labs.png)
3. En üstteki açılan listeyi kullanarak farklı bir laboratuvar hesabı seçin. Laboratuvarları seçilen laboratuvar hesabında görürsünüz. 

## <a name="delete-a-classroom-lab"></a>Sınıf Laboratuvarı silme
1. Laboratuvarın kutucuğunda, köşede üç nokta (...) ve ardından **Sil**' i seçin. 

    ![Sil düğmesi](../media/how-to-manage-classroom-labs/delete-button.png)
3. Silmeye devam etmek için **Laboratuvarı Sil** Iletişim kutusunda **Sil** ' i seçin. 

## <a name="switch-to-another-classroom-lab"></a>Başka bir sınıf laboratuvarına geç
Geçerli olan başka bir sınıf laboratuvarına geçiş yapmak için, en üstteki laboratuvar hesabındaki laboratuvarların açılan listesini seçin.

![Üstteki aşağı açılan listeden laboratuvar listesini seçin](../media/how-to-manage-classroom-labs/switch-lab.png)

Bu açılan listede **Yeni Laboratuvarı** kullanarak yeni bir laboratuvar da oluşturabilirsiniz. 

> [!NOTE]
> Laboratuvarları yönetmek için az. LabServices PowerShell modülünü (Önizleme) da kullanabilirsiniz. Daha fazla bilgi için [GitHub 'Daki az. LabServices giriş sayfasına](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)bakın.

Farklı bir laboratuvar hesabına geçmek için, laboratuvar hesabının yanındaki açılan eklentiyi seçin ve diğer laboratuvar hesabını seçin. 

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Laboratuvar sahibi olarak, şablonları ayarlama ve yayımlama](how-to-create-manage-template.md)
- [Laboratuvar sahibi olarak, bir laboratuvarın kullanımını yapılandırma ve denetleme](how-to-configure-student-usage.md)
- [Laboratuvar kullanıcısı olarak, sınıf laboratuvarlarına erişin](how-to-use-classroom-lab.md)

