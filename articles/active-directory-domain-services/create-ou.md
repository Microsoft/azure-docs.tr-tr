---
title: Azure AD Domain Services bir kuruluş birimi (OU) oluşturun | Microsoft Docs '
description: Azure AD Domain Services yönetilen bir etki alanında özel bir kuruluş birimi (OU) oluşturmayı ve yönetmeyi öğrenin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 7d651849f5c8d930d99e87931eed5b823e90113c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474757"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services yönetilen bir etki alanında kuruluş birimi (OU) oluşturma

Active Directory Domain Services (AD DS) içindeki kuruluş birimleri (OU), Kullanıcı hesapları, hizmet hesapları veya bilgisayar hesapları gibi nesneleri mantıksal olarak gruplandırmanıza olanak tanır. Daha sonra belirli OU 'Lara Yöneticiler atayabilir ve hedeflenen yapılandırma ayarlarını zorlamak için Grup ilkesi uygulayabilirsiniz.

Azure AD DS yönetilen etki alanları, iki adet yerleşik ou- *Aaddc bilgisayarı* ve *Aaddc kullanıcısı*içerir. *Aaddc bilgisayarları* OU, yönetilen etki alanına katılmış tüm bilgisayarlar için bilgisayar nesneleri içerir. *Aaddc kullanıcıları* OU, Azure AD kiracısından ' de eşitlenen kullanıcıları ve grupları içerir. Azure AD DS kullanan iş yükleri oluşturup çalıştırdığınızda, uygulamaların kimliğini doğrulamak için hizmet hesapları oluşturmanız gerekebilir. Bu hizmet hesaplarını düzenlemek için genellikle Azure AD DS yönetilen etki alanında özel bir OU oluşturup bu OU içinde hizmet hesapları oluşturursunuz.

Karma bir ortamda, şirket içi AD DS ortamında oluşturulan OU 'Lar Azure AD DS ile eşitlenmez. Azure AD DS yönetilen etki alanları düz bir OU yapısını kullanır. Her Kullanıcı hesabı ve grubu, farklı şirket içi etki alanlarından veya ormanlardan eşitlendiğinde, burada bir hiyerarşik OU yapısını yapılandırmış olsanız bile, *Aaddc kullanıcıları* kapsayıcısında depolanır.

Bu makalede, Azure AD DS yönetilen etki alanında bir OU oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services bir örnek oluşturmak ve yapılandırmak][create-azure-ad-ds-instance]için öğreticiyi doldurun.
* Azure AD DS yönetilen etki alanına katılmış bir Windows Server Yönetim sanal makinesi.
    * Gerekirse, [bir yönetim sanal makinesi oluşturmak][tutorial-create-management-vm]için öğreticiyi izleyin.
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

## <a name="custom-ou-considerations-and-limitations"></a>Özel OU konuları ve sınırlamaları

Azure AD DS yönetilen bir etki alanında özel OU 'Lar oluşturduğunuzda, Kullanıcı yönetimi ve Grup İlkesi uygulama konusunda ek yönetim esnekliği elde edersiniz. Şirket içi AD DS ortamıyla karşılaştırıldığında, Azure AD DS özel bir OU yapısını oluştururken ve yönetirken bazı sınırlamalar ve önemli noktalar vardır:

* Özel OU 'Lar oluşturmak için, kullanıcıların *AAD DC Yöneticiler* grubunun bir üyesi olması gerekir.
* Özel bir OU oluşturan bir kullanıcıya, bu OU üzerinden yönetim ayrıcalıkları (tam denetim) verilir ve kaynak sahibidir.
    * Varsayılan olarak, *AAD DC yöneticileri* grubu özel OU 'nun tam denetimine sahiptir.
* Azure AD kiracınızdaki tüm eşitlenmiş Kullanıcı hesaplarını içeren *Aaddc kullanıcıları* için varsayılan bir OU oluşturulur.
    * Kullanıcıları veya grupları *Aaddc kullanıcıları* OU 'dan oluşturduğunuz özel OU 'lara taşıyamazsınız. Yalnızca Azure AD DS tarafından yönetilen etki alanında oluşturulan kullanıcı hesapları veya kaynaklar, özel OU 'Lara taşınabilir.
* Özel OU 'Lar altında oluşturduğunuz Kullanıcı hesapları, gruplar, hizmet hesapları ve bilgisayar nesneleri Azure AD kiracınızda mevcut değildir.
    * Bu nesneler, Azure AD Graph API veya Azure AD Kullanıcı arabirimindeki ile gösterilmez; Bunlar yalnızca Azure AD DS yönetilen etki alanında kullanılabilir.

## <a name="create-a-custom-ou"></a>Özel bir OU oluşturma

Özel bir OU oluşturmak için, etki alanına katılmış bir VM 'den Active Directory yönetim araçlarını kullanırsınız. Active Directory Yönetim Merkezi, OU 'Lar dahil olmak üzere Azure AD DS yönetilen bir etki alanında kaynakları görüntülemenize, düzenlemenize ve oluşturmanıza olanak sağlar.

> [!NOTE]
> Azure AD DS yönetilen bir etki alanında özel bir OU oluşturmak için *AAD DC Administrators* grubunun üyesi olan bir kullanıcı hesabında oturum açmış olmanız gerekir.

1. Yönetim sanal makinenizde oturum açın. Azure portal kullanarak bağlanma adımları için bkz. [Windows Server VM 'ye bağlanma][connect-windows-server-vm].
1. Başlangıç ekranından **Yönetim Araçları**' nı seçin. [Yönetim sanal makinesi oluşturmak][tutorial-create-management-vm]için öğreticide yüklü olan kullanılabilir yönetim araçlarının bir listesi gösterilir.
1. OU 'Ları oluşturup yönetmek için, yönetim araçları listesinden **Active Directory Yönetim Merkezi** ' yi seçin.
1. Sol bölmede, *contoso.com*gibi Azure AD DS yönetilen etki alanınızı seçin. Var olan OU 'ların ve kaynakların listesi gösterilir:

    ![Active Directory Yönetim Merkezi Azure AD DS yönetilen etki alanınızı seçin](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

1. **Görevler** bölmesi Active Directory Yönetim Merkezi sağ tarafında gösterilir. *Contoso.com*gibi etki alanı altında **Yeni > kuruluş birimi**' ni seçin.

    ![Active Directory Yönetim Merkezi yeni bir OU oluşturma seçeneğini belirleyin](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

1. **Kuruluş birimi oluştur** iletişim kutusunda, yeni OU Için *Mycustomou*gibi bir **ad** belirtin. OU için, *hizmet hesapları Için özel OU*gibi kısa bir açıklama sağlayın. İsterseniz, OU için **yönetilen** alanı alanını da ayarlayabilirsiniz. Özel OU 'yu oluşturmak için **Tamam**' ı seçin.

    ![Active Directory Yönetim Merkezi özel OU oluşturma](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

1. Active Directory Yönetim Merkezi geri döndüğünüzde, özel OU artık listelenir ve kullanılabilir:

    ![Active Directory Yönetim Merkezi kullanımı için özel OU](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="next-steps"></a>Sonraki adımlar

Yönetim araçlarını kullanma veya hizmet hesaplarını oluşturma ve kullanma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Active Directory Yönetim Merkezi: Başlarken](https://technet.microsoft.com/library/dd560651.aspx)
* [Hizmet hesapları adım adım Kılavuzu](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
