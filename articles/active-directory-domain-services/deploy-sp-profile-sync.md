---
title: SharePoint kullanıcı profili hizmetini Azure AD DS etkinleştirme | Microsoft Docs
description: Azure Active Directory Domain Services yönetilen bir etki alanını SharePoint Server için profil eşitlemesini destekleyecek şekilde yapılandırma hakkında bilgi edinin
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8fbde10ccf5a7f083f5fbddaadd6668d880a1fac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619836"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>SharePoint Server için Kullanıcı profili eşitlemesini desteklemek üzere Azure Active Directory Domain Services yapılandırma

SharePoint Server, Kullanıcı profillerini eşitlemeye yönelik bir hizmet içerir. Bu özellik, Kullanıcı profillerinin merkezi bir konumda depolanmasına ve birden çok SharePoint sitesi ve çiftlerine erişilebilir olmasını sağlar. SharePoint Server Kullanıcı profili hizmetini yapılandırmak için, Azure Active Directory Domain Services (Azure AD DS) yönetilen bir etki alanında uygun izinlere sahip olmanız gerekir. Daha fazla bilgi için bkz. [SharePoint Server 'da Kullanıcı profili eşitleme](/SharePoint/administration/user-profile-service-administration).

Bu makalede, Azure AD DS 'nin SharePoint Server Kullanıcı profili eşitleme hizmeti 'ne izin vermek üzere nasıl yapılandırılacağı gösterilmektedir.

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
* Kullanıcı profili eşitleme hizmeti için bir SharePoint hizmet hesabı.
    * Gerekirse, bkz. [SharePoint Server 'da yönetim ve hizmet hesaplarını planlayın][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Hizmet hesaplarına genel bakış

Yönetilen bir etki alanında, *Kullanıcılar* kuruluş BIRIMININ (OU) bir parçası olarak *AAD DC hizmet hesapları* adlı bir güvenlik grubu bulunur. Bu güvenlik grubunun üyelerine aşağıdaki ayrıcalıklar atanır:

- Kök do 'da **Dizin değişikliklerini çoğaltma** ayrıcalığı.
- *Yapılandırma* adlandırma bağlamında (kapsayıcı) **Dizin değişikliklerini çoğaltma** ayrıcalığı `cn=configuration` .

*AAD DC hizmet hesapları* güvenlik grubu, yerleşik grup *öncesi Windows 2000 ile uyumlu erişim*'in de bir üyesidir.

Bu güvenlik grubuna eklendiğinde, SharePoint Server Kullanıcı profili eşitleme hizmeti için hizmet hesabına, doğru çalışmak için gerekli ayrıcalıklar verilir.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>SharePoint Server Kullanıcı profili eşitleme desteğini etkinleştir

SharePoint Server hizmet hesabı, değişiklikleri dizine çoğaltmak ve SharePoint Server Kullanıcı profili eşitlemesini doğru şekilde sağlamak için yeterli ayrıcalıklara sahip olmalıdır. Bu ayrıcalıkları sağlamak için SharePoint kullanıcı profili eşitleme için kullanılan hizmet hesabını *AAD DC hizmet hesapları* grubuna ekleyin.

Azure AD DS Yönetim sanal makinenizde aşağıdaki adımları izleyin:

> [!NOTE]
> Yönetilen bir etki alanında Grup üyeliğini düzenlemek için *AAD DC Administrators* grubunun üyesi olan bir kullanıcı hesabında oturum açmış olmanız gerekir.

1. Başlangıç ekranından **Yönetim Araçları**' nı seçin. [Yönetim sanal makinesi oluşturmak][tutorial-create-management-vm]için öğreticide yüklü olan kullanılabilir yönetim araçlarının bir listesi gösterilir.
1. Grup üyeliğini yönetmek için, yönetim araçları listesinden **Active Directory Yönetim Merkezi** ' yi seçin.
1. Sol bölmede, *aaddscontoso.com* gibi yönetilen etki alanınızı seçin. Mevcut OU 'ların ve kaynakların bir listesi gösterilir.
1. **Kullanıcılar** OU 'sunu seçin, ardından *AAD DC hizmet hesapları* güvenlik grubunu seçin.
1. **Üyeler**' i ve ardından **Ekle...** öğesini seçin.
1. SharePoint hizmet hesabının adını girip **Tamam**' ı seçin. Aşağıdaki örnekte, SharePoint hizmet hesabının adı *SPAdmin* şeklindedir:

    ![SharePoint hizmet hesabını AAD DC hizmet hesapları güvenlik grubuna ekleyin](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [SharePoint Server 'da Kullanıcı profili eşitlemesini yönetme](/SharePoint/administration/manage-profile-synchronization).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts