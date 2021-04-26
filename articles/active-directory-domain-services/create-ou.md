---
title: Azure AD Domain Services bir kuruluş birimi (OU) oluşturun | Microsoft Docs '
description: Azure AD Domain Services yönetilen bir etki alanında özel bir kuruluş birimi (OU) oluşturmayı ve yönetmeyi öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fbdfcc23553a27aaa4d7bbd4bff9c4f33cb8fd19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620027"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services yönetilen bir etki alanında kuruluş birimi (OU) oluşturma

Active Directory Domain Services (AD DS) yönetilen bir etki alanındaki kuruluş birimleri (OU), Kullanıcı hesapları, hizmet hesapları veya bilgisayar hesapları gibi nesneleri mantıksal olarak gruplandırmanıza olanak tanır. Daha sonra belirli OU 'Lara Yöneticiler atayabilir ve hedeflenen yapılandırma ayarlarını zorlamak için Grup ilkesi uygulayabilirsiniz.

Azure AD DS yönetilen etki alanları aşağıdaki iki yerleşik OU içerir:

* *Aaddc bilgisayarları* -yönetilen etki alanına katılmış tüm bilgisayarlar için bilgisayar nesnelerini içerir.
* *Aaddc kullanıcıları* -Azure AD kiracısından ' de eşitlenen kullanıcıları ve grupları içerir.

Azure AD DS kullanan iş yükleri oluşturup çalıştırdığınızda, uygulamaların kimliğini doğrulamak için hizmet hesapları oluşturmanız gerekebilir. Bu hizmet hesaplarını düzenlemek için genellikle yönetilen etki alanında özel bir OU oluşturup bu OU içinde hizmet hesapları oluşturursunuz.

Karma bir ortamda, şirket içi AD DS ortamda oluşturulan OU 'Lar yönetilen etki alanı ile eşitlenmez. Yönetilen etki alanları düz bir OU yapısını kullanır. Her Kullanıcı hesabı ve grubu, farklı şirket içi etki alanlarından veya ormanlardan eşitlendiğinde, burada bir hiyerarşik OU yapısını yapılandırmış olsanız bile, *Aaddc kullanıcıları* kapsayıcısında depolanır.

Bu makalede, yönetilen etki alanında bir OU oluşturma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturmak ve yapılandırmak][create-azure-ad-ds-instance]için öğreticiyi doldurun.
* Azure AD DS yönetilen etki alanına katılmış bir Windows Server Yönetim sanal makinesi.
    * Gerekirse, [bir yönetim sanal makinesi oluşturmak][tutorial-create-management-vm]için öğreticiyi izleyin.
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

## <a name="custom-ou-considerations-and-limitations"></a>Özel OU konuları ve sınırlamaları

Yönetilen bir etki alanında özel OU 'Lar oluşturduğunuzda, Kullanıcı yönetimi için ek yönetim esnekliği elde edersiniz ve Grup ilkesi uygulanıyor. Şirket içi AD DS ortamıyla karşılaştırıldığında, yönetilen bir etki alanında özel bir OU yapısı oluştururken ve yönetirken bazı sınırlamalar ve önemli noktalar vardır:

* Özel OU 'Lar oluşturmak için, kullanıcıların *AAD DC Yöneticiler* grubunun bir üyesi olması gerekir.
* Özel bir OU oluşturan bir kullanıcıya, bu OU üzerinden yönetim ayrıcalıkları (tam denetim) verilir ve kaynak sahibidir.
    * Varsayılan olarak, *AAD DC yöneticileri* grubu özel OU 'nun tam denetimine sahiptir.
* Azure AD kiracınızdaki tüm eşitlenmiş Kullanıcı hesaplarını içeren *Aaddc kullanıcıları* için varsayılan bir OU oluşturulur.
    * Kullanıcıları veya grupları *Aaddc kullanıcıları* OU 'dan oluşturduğunuz özel OU 'lara taşıyamazsınız. Yalnızca yönetilen etki alanında oluşturulan kullanıcı hesapları veya kaynaklar özel OU 'Lara taşınabilir.
* Özel OU 'Lar altında oluşturduğunuz Kullanıcı hesapları, gruplar, hizmet hesapları ve bilgisayar nesneleri Azure AD kiracınızda mevcut değildir.
    * Bu nesneler Microsoft Graph API 'sini veya Azure AD Kullanıcı arabirimini kullanarak gösterilmez; Bunlar yalnızca yönetilen etki alanında kullanılabilir.

## <a name="create-a-custom-ou"></a>Özel bir OU oluşturma

Özel bir OU oluşturmak için, etki alanına katılmış bir VM 'den Active Directory yönetim araçlarını kullanırsınız. Active Directory Yönetim Merkezi, OU 'Lar dahil olmak üzere yönetilen bir etki alanında kaynakları görüntülemenize, düzenlemenize ve oluşturmanıza olanak sağlar.

> [!NOTE]
> Yönetilen bir etki alanında özel bir OU oluşturmak için *AAD DC Administrators* grubunun üyesi olan bir kullanıcı hesabında oturum açmış olmanız gerekir.

1. Yönetim sanal makinenizde oturum açın. Azure portal kullanarak bağlanma adımları için bkz. [Windows Server VM 'ye bağlanma][connect-windows-server-vm].
1. Başlangıç ekranından **Yönetim Araçları**' nı seçin. [Yönetim sanal makinesi oluşturmak][tutorial-create-management-vm]için öğreticide yüklü olan kullanılabilir yönetim araçlarının bir listesi gösterilir.
1. OU 'Ları oluşturup yönetmek için, yönetim araçları listesinden **Active Directory Yönetim Merkezi** ' yi seçin.
1. Sol bölmede, *aaddscontoso.com* gibi yönetilen etki alanınızı seçin. Var olan OU 'ların ve kaynakların listesi gösterilir:

    ![Active Directory Yönetim Merkezi yönetilen etki alanınızı seçin](./media/create-ou/create-ou-adac-overview.png)

1. **Görevler** bölmesi Active Directory Yönetim Merkezi sağ tarafında gösterilir. *Aaddscontoso.com* gibi etki alanı altında **Yeni > kuruluş birimi**' ni seçin.

    ![Active Directory Yönetim Merkezi yeni bir OU oluşturma seçeneğini belirleyin](./media/create-ou/create-ou-adac-new-ou.png)

1. **Kuruluş birimi oluştur** iletişim kutusunda, yeni OU Için *Mycustomou* gibi bir **ad** belirtin. OU için, *hizmet hesapları Için özel OU* gibi kısa bir açıklama sağlayın. İsterseniz, OU için **yönetilen** alanı alanını da ayarlayabilirsiniz. Özel OU 'yu oluşturmak için **Tamam**' ı seçin.

    ![Active Directory Yönetim Merkezi özel OU oluşturma](./media/create-ou/create-ou-dialog.png)

1. Active Directory Yönetim Merkezi geri döndüğünüzde, özel OU artık listelenir ve kullanılabilir:

    ![Active Directory Yönetim Merkezi kullanımı için özel OU](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Sonraki adımlar

Yönetim araçlarını kullanma veya hizmet hesaplarını oluşturma ve kullanma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Active Directory Yönetim Merkezi: Başlarken](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560651(v=ws.10))
* [Hizmet Hesapları Adım Adım Kılavuzu](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm