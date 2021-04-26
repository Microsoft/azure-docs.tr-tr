---
title: Azure Lab Services bir kullanıcıyı laboratuvar Oluşturucu olarak ekleyin
description: Bu makalede, Azure Lab Services ' de laboratuvar hesabı için laboratuvar Oluşturucu rolüne kullanıcı ekleme gösterilmektedir. Laboratuvar oluşturucuları bu laboratuvar hesabı dahilinde Labs oluşturabilir.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a6c89483744f943926f126701d28988358627a27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435969"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Azure Lab Services 'daki laboratuvar hesabına laboratuvar oluşturucuları ekleme
Bu makalede, Azure Lab Services ' deki bir laboratuvar hesabına kullanıcıları laboratuvar oluşturucuları olarak nasıl ekleyeceğiniz gösterilir. Bu kullanımlar daha sonra laboratuvar hesabında Labs oluşturabilir. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Laboratuvar Oluşturucu rolüne Microsoft Kullanıcı hesabı ekleme
Bir laboratuvar hesabında sınıf laboratuvarı ayarlamak için kullanıcının ilgili laboratuvar hesabında **Laboratuvar Oluşturan** rolünün üyesi olması gerekir. Laboratuvar hesabını oluşturmak için kullandığınız hesap otomatik olarak bu role eklenir. Sınıf laboratuvarı oluşturmak için aynı kullanıcıyı kullanmayı planlıyorsanız bu adımı atlayabilirsiniz. Sınıf laboratuvarı oluşturmak için başka bir kullanıcı hesabı kullanmak istiyorsanız aşağıdaki adımları uygulayın: 

Eğitimcilere, sınıfları için laboratuvar oluşturma ve **Laboratuvar Oluşturan** rolüne bunları ekleme izni sağlamak için:

1. **Laboratuvar hesabı** sayfasında, **ERIŞIM denetimi (IAM)** seçeneğini belirleyin ve araç çubuğunda **+ rol ataması Ekle** ' ye tıklayın. 

    ![Access Control > rol ataması Ekle düğmesi](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **Rol ataması Ekle** sayfasında, **rol** için **Laboratuvar Oluşturucu** ' yı seçin, laboratuvar oluşturucuları rolüne eklemek istediğiniz kullanıcıyı seçin ve **Kaydet**' i seçin. 

    ![Laboratuvar Oluşturucu Ekle](./media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Microsoft hesabı olmayan bir kullanıcıyı laboratuvar Oluşturucu olarak ekliyorsanız, [Microsoft hesabı olmayan kullanıcı ekleme laboratuvar Oluşturucu olarak](#add-a-non-microsoft-account-user-as-a-lab-creator) bölümüne bakın. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Laboratuvar Oluşturucusu olarak Microsoft hesabı olmayan bir kullanıcı ekleme
Bir kullanıcıyı laboratuvar Oluşturucu olarak eklemek için, e-posta hesaplarını kullanın. Aşağıdaki e-posta hesabı türleri kullanılabilir:

- University in Azure Active Directory (AAD) tarafından sunulan bir e-posta hesabı.
- ,, Veya gibi bir Microsoft e-posta hesabı `@outlook.com` `@hotmail.com` `@msn.com` `@live.com` .
- Microsoft olmayan bir e-posta hesabı, örneğin, Yahoo veya Google tarafından sağlanmamıştır. Ancak, bu tür hesapların bir Microsoft hesabı bağlantılı olması gerekir.
- GitHub hesabı. Bu hesabın bir Microsoft hesabı ile bağlantılı olması gerekir.

### <a name="using-a-non-microsoft-email-account"></a>Microsoft olmayan bir e-posta hesabı kullanma
Laboratuvar oluşturucuları/Eğitmenler, bir sınıf laboratuvarına kaydolmak ve oturum açmak için Microsoft olmayan e-posta hesaplarını kullanabilir.  Ancak, Laboratuvar Hizmetleri portalında oturum açma, Eğitmenler 'in Microsoft olmayan e-posta adreslerine bağlı bir Microsoft hesabı oluşturmasını gerektirir.

Birçok Eğitmenler, Microsoft olmayan e-posta adreslerine bağlı bir Microsoft hesabı zaten sahip olabilir. Örneğin, Eğitmenler, Microsoft 'un e-posta adreslerini Office, Skype, OneDrive veya Windows gibi diğer ürün veya hizmetleriyle kullandıklarında zaten bir Microsoft hesabı sahiptir.  

Eğitmenler Laboratuvar Hizmetleri portalında oturum açtığında, onlara e-posta adresi ve parolası sorulur. Eğitmen, Microsoft hesabı bağlantılı olmayan bir Microsoft hesabı oturum açmaya çalışırsa, eğitmen aşağıdaki hata iletisini alır: 

![Hata iletisi](./media/how-to-configure-student-usage/cant-find-account.png)

Microsoft hesabı kaydolmak için, Eğitmenler ' e gitmelidir [http://signup.live.com](http://signup.live.com) .  


### <a name="using-a-github-account"></a>GitHub hesabı kullanma
Eğitmenler, bir sınıf laboratuvarına kaydolmak ve oturum açmak için mevcut bir GitHub hesabı da kullanabilir. Eğitmenin zaten GitHub hesabına bağlı bir Microsoft hesabı varsa, bu kullanıcılar oturum açabilir ve önceki bölümde gösterildiği gibi parolasını sağlayabilir. Henüz GitHub hesabını bir Microsoft hesabı bağlamamış olmaları durumunda, **oturum açma seçeneklerini** seçmeleri gerekir:

![Oturum açma seçenekleri bağlantısı](./media/how-to-configure-student-usage/signin-options.png)

**Oturum açma seçenekleri** sayfasında **GitHub ile oturum aç**' ı seçin.

![GitHub bağlantısıyla oturum açın](./media/how-to-configure-student-usage/signin-github.png)

Son olarak, bunlara GitHub hesaplarına bağlı bir Microsoft hesabı oluşturması istenir. Eğitmen **İleri** seçerken otomatik olarak gerçekleşir.  Daha sonra eğitmen hemen oturum açtı ve derslik laboratuvarına bağlanır.


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Laboratuvar sahibi olarak, Labs oluşturma ve yönetme](how-to-manage-classroom-labs.md)
- [Laboratuvar sahibi olarak, şablonları ayarlama ve yayımlama](how-to-create-manage-template.md)
- [Laboratuvar sahibi olarak, bir laboratuvarın kullanımını yapılandırma ve denetleme](how-to-configure-student-usage.md)
- [Laboratuvar kullanıcısı olarak, erişim laboratuvarları](how-to-use-classroom-lab.md)
