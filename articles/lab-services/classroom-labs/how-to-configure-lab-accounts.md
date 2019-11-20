---
title: Laboratuvar hesaplarını Azure Lab Services yapılandırma | Microsoft Docs
description: Bir laboratuvar hesabını oluşturulduktan sonra nasıl yapılandıracağınızı öğrenin.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 9faf7de25b8bf227dd4c7f3588972f98f7493439
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583921"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Azure Lab Services laboratuvar hesaplarını yapılandırma 
Azure Lab Services, laboratuvar hesabı, sınıf laboratuvarları gibi yönetilen laboratuvar türleri için bir kapsayıcıdır. Yönetici Azure Lab Services bir laboratuvar hesabı ayarlar ve hesapta laboratuvarları oluşturabileceğiniz laboratuvar sahiplerine erişim sağlar. Bu makalede laboratuvar hesabı oluşturma, tüm laboratuar hesaplarını görüntüleme veya laboratuvar hesabını silme işlemlerinin nasıl yapılacağı açıklanır.

## <a name="connect-with-a-peer-virtual-network"></a>Eş bir sanal ağla bağlanma
Sanal bir ağı, laboratuvarın sanal ağına eş ağ olarak bağlamak için şu adımları izleyin:

1. **Laboratuvar hesabı** sayfasında, sol taraftaki menüden **Labs yapılandırması** ' nı seçin.

    ![Labs yapılandırma sayfası](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. **Eş sanal ağ**için **etkin** veya **devre dışı**seçeneğini belirleyin. Varsayılan değer **devre dışıdır**. Eş sanal ağını etkinleştirmek için aşağıdaki adımları uygulayın: 
    1. **Etkin**' i seçin.
    2. Açılan listeden **VNET** ' i seçin. 
3. Araç çubuğunda **Kaydet**’i seçin. 

Bu hesapta oluşturulan Labs seçili sanal ağa bağlı. Seçili sanal ağdaki kaynaklara erişebilirler. Daha fazla bilgi için bkz. [Azure Lab Services laboratuvar ağınızı bir eş sanal ağ Ile bağlama](how-to-connect-peer-virtual-network.md).

**Eş sanal ağ** alanı için bir sanal ağ seçtiğinizde, laboratuvar **oluşturucusunun laboratuvar konumunu seçmesine izin ver** seçeneği devre dışı bırakılır. Bunun nedeni, laboratuvar hesabındaki laboratuvarların, eş sanal ağdaki kaynaklarla bağlantı kurmak için laboratuvar hesabıyla aynı bölgede olması gerekir. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Laboratuvar oluşturucusunun laboratuvar için konum seçmesine izin ver
Laboratuvar oluşturucusunun aşağıdaki adımları izleyerek laboratuvar hesabı konumundan farklı bir konumda laboratuvarları oluşturmalarına izin verebilirsiniz: 

1. **Laboratuvar hesabı** sayfasında, sol taraftaki menüden **Labs yapılandırması** ' nı seçin.
2. Laboratuvar oluşturucusunun laboratuvar **konumunu seçmesine Izin ver**için, laboratuvar Oluşturucu 'nun laboratuvar için bir konum seçebilmesini istiyorsanız **etkin** ' i seçin. Devre dışıysa, laboratuvarlar laboratuvar hesabının mevcut olduğu yerde otomatik olarak oluşturulur. 
    
    **Eş sanal ağ** alanı için bir sanal ağ seçtiğinizde bu alan devre dışıdır. Bunun nedeni, laboratuvar hesabındaki laboratuvarların, eş sanal ağdaki kaynaklara erişmesi için laboratuvar hesabıyla aynı bölgede olması gerekir. 
1. Araç çubuğunda **Kaydet**’i seçin. 

    ![Laboratuvar konumu ayarını Yapılandır](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Laboratuvardaki VM 'Ler için bir adres aralığı belirtin
Aşağıdaki yordamda, laboratuvardaki VM 'Ler için bir adres aralığı belirtme adımları vardır. Daha önce belirttiğiniz aralığı güncelleştirirseniz, değiştirilen adres aralığı yalnızca değişiklik yapıldıktan sonra oluşturulan VM 'Ler için geçerlidir. 

Göz önünde bulundurmanız gereken adres aralığını belirtirken bazı kısıtlamalar aşağıda verilmiştir. 

- Ön ek, 23 ' ten küçük veya buna eşit olmalıdır. 
- Bir sanal ağ, laboratuvar hesabına eşlenirse, belirtilen adres aralığı eşlenen sanal ağın adres aralığıyla çakışamaz.

1. **Laboratuvar hesabı** sayfasında, sol taraftaki menüden **Labs yapılandırması** ' nı seçin.
2. **Adres aralığı** alanı için, laboratuvarda oluşturulacak VM 'ler için adres aralığını belirtin. Adres aralığı, sınıfsız etki alanları arası yönlendirme (CıDR) gösteriminde olmalıdır (örnek: 10.20.0.0/23). Laboratuvardaki sanal makineler, bu adres aralığında oluşturulacaktır.
3. Araç çubuğunda **Kaydet**’i seçin. 

    ![Adres aralığını yapılandır](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Laboratuvar Oluşturan rolüne kullanıcı ekleme
Bir laboratuvar hesabında sınıf laboratuvarı ayarlamak için kullanıcının ilgili laboratuvar hesabında **Laboratuvar Oluşturan** rolünün üyesi olması gerekir. Laboratuvar hesabını oluşturmak için kullandığınız hesap otomatik olarak bu role eklenir. Sınıf laboratuvarı oluşturmak için aynı kullanıcıyı kullanmayı planlıyorsanız bu adımı atlayabilirsiniz. Sınıf laboratuvarı oluşturmak için başka bir kullanıcı hesabı kullanmak istiyorsanız aşağıdaki adımları uygulayın: 

Eğitimcilere, sınıfları için laboratuvar oluşturma ve **Laboratuvar Oluşturan** rolüne bunları ekleme izni sağlamak için:

1. **Laboratuvar hesabı** sayfasında, **ERIŞIM denetimi (IAM)** seçeneğini belirleyin ve araç çubuğunda **+ rol ataması Ekle** ' ye tıklayın. 

    ![Access Control > rol ataması Ekle düğmesi](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **Rol ataması Ekle** sayfasında, **rol**için **Laboratuvar Oluşturucu** ' yı seçin, laboratuvar oluşturucuları rolüne eklemek istediğiniz kullanıcıyı seçin ve **Kaydet**' i seçin. 

    ![Laboratuvar Oluşturucu Ekle](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Microsoft hesabı olmayan bir kullanıcıyı laboratuvar Oluşturucu olarak ekliyorsanız, [Microsoft hesabı olmayan kullanıcı ekleme laboratuvar Oluşturucu olarak](#add-a-non-microsoft-account-user-as-a-lab-creator) bölümüne bakın. 

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Laboratuvar oluşturucuları için kullanılabilen Market görüntülerini belirtme
Laboratuvar sahibi olarak laboratuvar oluşturucuların laboratuvar hesabında laboratuvar oluşturmak için kullanacağı Market görüntülerini belirtebilirsiniz. 

1. Sol taraftaki menüden **Market görüntüleri**'ni seçin. Listede varsayılan olarak tüm görüntüler (hem etkin hem devre dışı) yer alır. Yalnızca etkin/devre dışı görüntüleri görmek için en üstteki açılan listeden **Yalnızca etkin**/**Yalnızca devre dışı** seçeneğini belirleyerek listeyi filtreleyebilirsiniz. 
    
    ![Market görüntüleri sayfası](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Listede görüntülenen Market görüntüleri yalnızca aşağıdaki koşulları karşılayan görüntülerdir:
        
    - Tek bir VM oluşturur.
    - VM'leri sağlamak için Azure Resource Manager'ı kullanır
    - Ek lisans planı satın alınmasını gerektirmez
2. Etkin durumdaki bir Market görüntüsünü **devre dışı bırakmak** için aşağıdaki eylemlerden birini gerçekleştirin: 
    1. Son sütundaki **... (üç nokta)** simgesini ve ardından **Görüntüyü devre dışı bırak**'ı seçin. 

        ![Tek bir görüntüyü devre dışı bırakma](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Listede görüntü adlarının yanında yer alan onay kutularını seçerek bir daha fazla görüntüyü seçin ve **Seçilen görüntüleri devre dışı bırak**'a tıklayın. 

        ![Birden fazla görüntüyü devre dışı bırakma](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Benzer şekilde bir Market görüntüsünü **etkinleştirmek** için aşağıdaki eylemlerden birini gerçekleştirebilirsiniz: 
    1. Son sütundaki **... (üç nokta)** simgesini ve ardından **Görüntüyü etkinleştir**'i seçin. 
    2. Listede görüntü adlarının yanında yer alan onay kutularını seçerek bir daha fazla görüntüyü seçin ve **Seçilen görüntüleri etkinleştir**'e tıklayın. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Laboratuvar Oluşturucusu olarak Microsoft hesabı olmayan bir kullanıcı ekleme
Bir kullanıcıyı laboratuvar Oluşturucu olarak eklemek için, e-posta hesaplarını kullanın. Aşağıdaki e-posta hesabı türleri kullanılabilir:

- University in Office 365 Azure Active Directory (AAD) tarafından sunulan bir e-posta hesabı. 
- `@outlook.com`, `@hotmail.com`, `@msn.com`veya `@live.com`gibi bir Microsoft e-posta hesabı.
- Microsoft olmayan bir e-posta hesabı, örneğin, Yahoo veya Google tarafından sağlanmamıştır. Ancak, bu tür hesapların bir Microsoft hesabı bağlantılı olması gerekir.
- GitHub hesabı. Bu hesabın bir Microsoft hesabı ile bağlantılı olması gerekir.

### <a name="using-a-non-microsoft-email-account"></a>Microsoft olmayan bir e-posta hesabı kullanma
Laboratuvar oluşturucuları/Eğitmenler, bir sınıf laboratuvarına kaydolmak ve oturum açmak için Microsoft olmayan e-posta hesaplarını kullanabilir.  Ancak, Laboratuvar Hizmetleri portalında oturum açma, Eğitmenler 'in Microsoft olmayan e-posta adreslerine bağlı bir Microsoft hesabı oluşturmasını gerektirir.

Birçok Eğitmenler, Microsoft olmayan e-posta adreslerine bağlı bir Microsoft hesabı zaten sahip olabilir. Örneğin, Eğitmenler, Microsoft 'un e-posta adreslerini Office, Skype, OneDrive veya Windows gibi diğer ürün veya hizmetleriyle kullandıklarında zaten bir Microsoft hesabı sahiptir.  

Eğitmenler Laboratuvar Hizmetleri portalında oturum açtığında, onlara e-posta adresi ve parolası sorulur. Eğitmen, Microsoft hesabı bağlantılı olmayan bir Microsoft hesabı oturum açmaya çalışırsa, eğitmen aşağıdaki hata iletisini alır: 

![Hata iletisi](../media/how-to-configure-student-usage/cant-find-account.png)

Microsoft hesabı kaydolmak için, Eğitmenler [http://signup.live.com](http://signup.live.com)' e gitmelidir.  


### <a name="using-a-github-account"></a>GitHub hesabı kullanma
Eğitmenler, bir sınıf laboratuvarına kaydolmak ve oturum açmak için mevcut bir GitHub hesabı da kullanabilir. Eğitmenin zaten GitHub hesabına bağlı bir Microsoft hesabı varsa, bu kullanıcılar oturum açabilir ve önceki bölümde gösterildiği gibi parolasını sağlayabilir. Henüz GitHub hesabını bir Microsoft hesabı bağlamamış olmaları durumunda, **oturum açma seçeneklerini**seçmeleri gerekir:

![Oturum açma seçenekleri bağlantısı](../media/how-to-configure-student-usage/signin-options.png)

**Oturum açma seçenekleri** sayfasında **GitHub ile oturum aç**' ı seçin.

![GitHub bağlantısıyla oturum açın](../media/how-to-configure-student-usage/signin-github.png)

Son olarak, bunlara GitHub hesaplarına bağlı bir Microsoft hesabı oluşturması istenir. Eğitmen **İleri**seçerken otomatik olarak gerçekleşir.  Daha sonra eğitmen hemen oturum açtı ve derslik laboratuvarına bağlanır.


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Laboratuvar sahibi olarak, Labs oluşturma ve yönetme](how-to-manage-classroom-labs.md)
- [Laboratuvar sahibi olarak, şablonları ayarlama ve yayımlama](how-to-create-manage-template.md)
- [Laboratuvar sahibi olarak, bir laboratuvarın kullanımını yapılandırma ve denetleme](how-to-configure-student-usage.md)
- [Laboratuvar kullanıcısı olarak, sınıf laboratuvarlarına erişin](how-to-use-classroom-lab.md)
