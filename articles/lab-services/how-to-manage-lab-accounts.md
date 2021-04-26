---
title: Laboratuvar hesaplarını Azure Lab Services yönetme | Microsoft Docs
description: Bir laboratuvar hesabı oluşturmayı, tüm laboratuar hesaplarını görüntülemeyi veya bir Azure aboneliğindeki laboratuvar hesabını silmeyi öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 65ff28401b73698e320735613b32c96bf3de58f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434099"
---
# <a name="create-and-manage-lab-accounts"></a>Laboratuvar hesabı oluşturma ve yönetme
Azure Lab Services, laboratuvar hesabı Labs gibi yönetilen laboratuvar türleri için bir kapsayıcıdır. Yönetici Azure Lab Services bir laboratuvar hesabı ayarlar ve hesapta laboratuvarları oluşturabileceğiniz laboratuvar sahiplerine erişim sağlar. Bu makalede laboratuvar hesabı oluşturma, tüm laboratuar hesaplarını görüntüleme veya laboratuvar hesabını silme işlemlerinin nasıl yapılacağı açıklanır.

## <a name="create-a-lab-account"></a>Laboratuvar hesabı oluşturma
Aşağıdaki adımlar, Azure portalını kullanarak Azure Lab Services ile nasıl bir laboratuvar hesabı oluşturulacağını göstermektedir. 

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol taraftaki menüden **tüm hizmetler** ' i seçin. **DevOps** bölümünde **Laboratuvar hesapları** ' nı seçin. `*` **Laboratuvar hesapları**' nın yanındaki yıldız () seçeneğini belirlerseniz, Sol menüdeki **Sık Kullanılanlar** bölümüne eklenir. Sonraki zamanda, **Sık Kullanılanlar** altında **Laboratuvar hesapları** ' nı seçersiniz.

    ![Tüm hizmetler-> laboratuvar hesapları](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. **Laboratuvar hesapları** sayfasında, sayfada **Ekle** ' yi veya **Laboratuvar hesabı oluştur** ' u seçin. 

    ![Laboratuvar hesapları sayfasında Ekle ' yi seçin](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. **Laboratuvar hesabı oluşturma** sayfasının **temel bilgiler** sekmesinde, aşağıdaki işlemleri yapın: 
    1. **Laboratuvar hesabı adı** için bir ad girin. 
    2. Laboratuvar hesabı oluşturmak istediğiniz **Azure aboneliğini** seçin.
    3. **Kaynak grubu** için, **Yeni oluştur**’u seçip kaynak grubu için bir ad girin.
    4. **Konum** için, laboratuvar hesabının oluşturulmasını istediğiniz bir konumu/bölgeyi seçin.
    5. Laboratuvar **oluşturucunun laboratuvar konumunu seçmesine Izin ver** alanı için, laboratuvar oluşturucularının laboratuvar için bir konum seçebilmesini isteyip istemediğinizi belirtin. Varsayılan olarak, seçeneği devre dışıdır. Laboratuvar oluşturucuları, devre dışı bırakıldığında, oluşturdukları laboratuvar için bir konum belirtemez. Labs, en yakın coğrafi konumda laboratuvar hesabına oluşturulur. Laboratuvar Oluşturucusu etkinleştirildiğinde Laboratuvar oluşturma sırasında bir konum seçebilir. Daha fazla bilgi için bkz. laboratuvar [oluşturucusunun laboratuvar için konum seçmesine Izin verme](allow-lab-creator-pick-lab-location.md). 

        ![Laboratuvar hesabı oluşturma-> temel bilgiler](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Ileri ' yi seçin: **Gelişmiş** sekmesine gitmek için sayfanın alt kısmındaki **Gelişmiş** ' i seçin ve ardından aşağıdaki adımları uygulayın: 
    1. Var olan bir **paylaşılan görüntü Galerisi** seçin veya bir tane oluşturun. Şablon VM 'yi, başkaları tarafından yeniden kullanılabilmesi için paylaşılan görüntü galerisine kaydedebilirsiniz. Paylaşılan görüntü galerileriyle ilgili ayrıntılı bilgi için, bkz. [Azure Lab Services paylaşılan görüntü Galerisi kullanma](how-to-use-shared-image-gallery.md).
    2. Kullanıcılar bağlantılarını kestikten sonra **Windows sanal makinelerini otomatik olarak kapatmak** isteyip istemediğinizi belirtin. Sanal makinelerin otomatik olarak kapanmadan önce kullanıcının yeniden bağlanmasına ne kadar süre beklemesi gerektiğini belirtin. 
    3. **Eş sanal ağ** için, laboratuvar ağı için bir eş sanal ağ (VNet) seçin. Bu hesapta oluşturulan Labs seçili VNet 'e bağlı ve seçilen VNet 'teki kaynaklara erişime sahip. Daha fazla bilgi için bkz. [laboratuvarınızın sanal ağını eş bir sanal ağla bağlama](how-to-connect-peer-virtual-network.md).    
    8. Laboratuvardaki VM 'Ler için bir **adres aralığı** belirtin. Adres aralığı, sınıfsız etki alanları arası yönlendirme (CıDR) gösteriminde olmalıdır (örnek: 10.20.0.0/23). Laboratuvardaki sanal makineler, bu adres aralığında oluşturulacaktır. Daha fazla bilgi için bkz [. laboratuvardaki VM 'ler için bir adres aralığı belirtme](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account)  

        > [!NOTE]
        > **Adres aralığı** özelliği yalnızca laboratuvar için bir **eş sanal ağ** etkinse geçerlidir.

        ![Laboratuvar hesabı oluşturma-> gelişmiş](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. **İleri:** **Etiketler** sekmesine geçmek Için sayfanın alt kısmındaki Etiketler ' i seçin. Laboratuvar hesabıyla ilişkilendirmek istediğiniz etiketleri ekleyin. Etiketler, birden fazla kaynağa ve kaynak grubuna aynı etiketi uygulayarak kaynakları kategorilere ayırmanıza ve birleştirilmiş faturalandırmayı görüntülemenize olanak tanıyan ad/değer çiftleridir. Daha fazla bilgi için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md).

    ![Etiketler sekmesi vurgulanmış olarak "Laboratuvar hesabı oluştur" sayfasını gösteren ekran görüntüsü.](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. **Gözden geçir + oluştur** sekmesine geçmek için bu sayfanın altındaki **gözden geçir + oluştur** ' u seçin. 
4. Bu sayfadaki Özet bilgilerini gözden geçirin ve **Oluştur**' u seçin. 

    ![Laboratuvar hesabı > etiketleri oluşturma](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Dağıtım tamamlanana kadar bekleyin, **sonraki adımlar**' ı genişletin ve aşağıdaki görüntüde gösterildiği gibi **Kaynağa Git** ' i seçin: 

    Araç çubuğundaki **zil simgesini** de seçebilir (**Bildirimler**), dağıtımın başarılı olduğunu doğrulayın ve ardından **Kaynağa Git**' i seçin. 

    Alternatif olarak, **Laboratuvar hesapları** sayfasında **Yenile** ' yi seçin ve oluşturduğunuz laboratuvar hesabını seçin. 

    ![Laboratuvar hesabı oluştur penceresi](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Aşağıdaki **laboratuvar hesabı** sayfasını görürsünüz:

    ![Laboratuvar hesabı sayfası](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Laboratuvar hesaplarını görüntüleme
1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Menüden **tüm kaynaklar** ' ı seçin. 
3. **Tür** Için **Laboratuvar hesapları** ' nı seçin. 
    Abonelik, kaynak grubu, konumlar ve etiketlere göre de filtre uygulayabilirsiniz. 

    ![Tüm kaynaklar-> laboratuvar hesapları](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Laboratuvar hesabını silme
Bir listede laboratuvar hesapları görüntüleyen önceki bölümde yer alan yönergeleri izleyin. Laboratuvar hesabını silmek için aşağıdaki yönergeleri kullanın: 

1. Silmek istediğiniz **Laboratuvar hesabını** seçin. 
2. Araç çubuğundan **Sil** ' i seçin. 

    ![Laboratuvar hesapları-> Sil düğmesi](./media/how-to-manage-lab-accounts/delete-button.png)
1. Onay için **Yes** yazın.
1. **Sil**’i seçin. 

    ![Laboratuvar hesabını silme-onay](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Laboratuvar hesaplarını yönetmek için az. LabServices PowerShell modülünü (Önizleme) da kullanabilirsiniz. Daha fazla bilgi için [GitHub 'Daki az. LabServices giriş sayfasına](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)bakın.

## <a name="next-steps"></a>Sonraki adımlar
İçerik tablosunun (TOC) **nasıl yapılır kılavuzlarındaki**  ->  **Laboratuvar hesapları (Laboratuvar hesabı sahibi) oluşturma ve yapılandırma** bölümündeki diğer makalelere bakın. 