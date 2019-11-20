---
title: Azure AD Domain Services parola ilkeleri oluşturma ve kullanma | Microsoft Docs
description: Azure AD DS yönetilen bir etki alanında hesap parolalarını güvenli hale getirmek ve denetlemek için hassas parola ilkelerinin nasıl ve neden kullanılacağını öğrenin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: ffcff84c7778ec3d6395e1c7a706c0deb2a0dc90
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893427"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Yönetilen etki alanlarında parola ve hesap kilitleme ilkeleri

Azure Active Directory Domain Services (Azure AD DS) ' de Kullanıcı güvenliğini yönetmek için hesap kilitleme ayarlarını veya en düşük parola uzunluğunu ve karmaşıklığı denetleyen hassas parola ilkeleri tanımlayabilirsiniz. Azure AD DS yönetilen bir etki alanındaki tüm kullanıcılara varsayılan bir hassas parola ilkesi oluşturulur ve uygulanır. Ayrıntılı denetim sağlamak ve belirli iş veya uyumluluk ihtiyaçlarını karşılamak için, ek ilkeler oluşturulup belirli kullanıcı gruplarına uygulanabilir.

Bu makalede, Azure AD DS Active Directory Yönetim Merkezi kullanarak hassas bir parola ilkesi oluşturma ve yapılandırma açıklanmaktadır.

> [!NOTE]
> Parola ilkeleri yalnızca Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulan Azure AD DS yönetilen etki alanları için kullanılabilir. Klasik kullanılarak oluşturulan eski yönetilen etki alanları için, [Klasik sanal ağ modelinden Kaynak Yöneticisi 'e geçiş][migrate-from-classic]yapın.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
  * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
  * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
  * Gerekirse, [Azure Active Directory Domain Services bir örnek oluşturmak ve yapılandırmak][create-azure-ad-ds-instance]için öğreticiyi doldurun.
  * Azure AD DS örneği, Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulmuş olmalıdır. Gerekirse, [Klasik sanal ağ modelinden Kaynak Yöneticisi ' ye geçirin][migrate-from-classic].
* Azure AD DS yönetilen etki alanına katılmış bir Windows Server Yönetim sanal makinesi.
  * Gerekirse, [bir yönetim sanal makinesi oluşturmak][tutorial-create-management-vm]için öğreticiyi izleyin.
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

## <a name="default-password-policy-settings"></a>Varsayılan parola ilkesi ayarları

Hassas parola ilkeleri (FGPPs), bir etki alanındaki farklı kullanıcılara parola ve hesap kilitleme ilkeleri için özel kısıtlamalar uygulamanıza imkan tanır. Örneğin, ayrıcalıklı hesapların güvenliğini sağlamak için, normal ayrıcalıklı olmayan hesaplardan daha sıkı hesap kilitleme ayarları uygulayabilirsiniz. Azure AD DS yönetilen bir etki alanı içinde birden fazla FGPPs oluşturabilir ve bunları kullanıcılara uygulamak için öncelik sırasını belirtebilirsiniz.

İlkeler, Azure AD DS yönetilen bir etki alanında Grup ilişkilendirmesi aracılığıyla dağıtılır ve yaptığınız tüm değişiklikler sonraki Kullanıcı oturumu sırasında uygulanır. İlkenin değiştirilmesi, zaten kilitlenen bir kullanıcı hesabının kilidini açmıyor.

Parola ilkeleri, uygulanan kullanıcı hesabının oluşturulma şekline bağlı olarak biraz daha farklı davranır. Azure AD DS bir kullanıcı hesabının oluşturulabilmesi için iki yol vardır:

* Kullanıcı hesabı Azure AD 'den eşitlenebilir. Bu, doğrudan Azure 'da oluşturulan bulut Kullanıcı hesaplarını ve Azure AD Connect kullanılarak şirket içi AD DS ortamından eşitlenmiş karma Kullanıcı hesaplarını içerir.
    * Azure AD DS 'daki Kullanıcı hesaplarının çoğu Azure AD 'den eşitleme işlemi aracılığıyla oluşturulur.
* Kullanıcı hesabı Azure AD DS yönetilen bir etki alanında el ile oluşturulabilir ve Azure AD 'de mevcut değildir.

Tüm kullanıcılar, oluşturulma şeklinden bağımsız olarak, Azure AD DS varsayılan parola ilkesi tarafından uygulanan aşağıdaki hesap kilitleme ilkelerine sahiptir:

* **Hesap kilitleme süresi:** 30
* **İzin verilen başarısız oturum açma denemesi sayısı:** 5
* **Başarısız oturum açma denemesi sayısı sıfırlama süresi:** 30 dakika
* **En fazla parola yaşı (ömür):** 90 gün

Bu varsayılan ayarlarla, 2 dakika içinde beş geçersiz parola kullanılırsa, Kullanıcı hesapları 30 dakika boyunca kilitlenir. Hesaplar 30 dakika sonra otomatik olarak açılır.

Hesap kilitleme işlemleri yalnızca yönetilen etki alanı içinde oluşur. Kullanıcı hesapları yalnızca Azure AD DS ve yönetilen etki alanına karşı başarısız oturum açma girişimleri nedeniyle kilitlidir. Azure AD 'den veya şirket içinde eşitlenmiş Kullanıcı hesapları, kaynak dizinlerinde yalnızca Azure AD DS kilitli değildir.

90 günden büyük bir maksimum parola yaşı belirten bir Azure AD parola ilkeniz varsa, bu parola yaşı Azure AD DS varsayılan ilkesine uygulanır. Azure AD DS 'de farklı bir maksimum parola yaşı tanımlamak için özel bir parola ilkesi yapılandırabilirsiniz. Azure AD 'de bir Azure AD DS parola ilkesinde yapılandırılmış en kısa bir parola yaşı varsa, Azure AD 'den veya şirket içi bir AD DS ortamından bir dikkatli olmanız gerekir. Bu senaryoda, şirket içi AD DS ortamında Azure AD 'de değiştirilmesi istenmeden önce, bir kullanıcının parolasının kullanım süreleri Azure AD DS olabilir.

Azure AD DS yönetilen bir etki alanında el ile oluşturulan kullanıcı hesapları için, varsayılan ilkeden aşağıdaki ek parola ayarları da uygulanır. Bu ayarlar, Azure AD 'den eşitlenen Kullanıcı hesaplarına uygulanmaz, çünkü bir Kullanıcı, parolasını doğrudan Azure AD DS 'de güncelleştiremez.

* **En az parola uzunluğu (karakter):** 7
* **Parolaların karmaşıklık gereksinimlerini karşılaması gerekir**

Varsayılan parola ilkesindeki hesap kilitleme veya parola ayarlarını değiştiremezsiniz. Bunun yerine, *AAD DC yöneticileri* grubunun üyeleri özel parola ilkeleri oluşturabilir ve sonraki bölümde gösterildiği gibi varsayılan yerleşik ilkeyi geçersiz kılmak (öncelikli hale getirmeleri) için yapılandırabilir.

## <a name="create-a-custom-password-policy"></a>Özel parola ilkesi oluşturma

Azure 'da uygulama oluşturup çalıştırdığınızda, özel bir parola ilkesi yapılandırmak isteyebilirsiniz. Örneğin, farklı hesap kilitleme ilkesi ayarlarını ayarlamak için bir ilke oluşturabilirsiniz.

Özel parola ilkeleri, Azure AD DS yönetilen bir etki alanındaki gruplara uygulanır. Bu yapılandırma varsayılan ilkeyi etkin bir şekilde geçersiz kılar.

Özel bir parola ilkesi oluşturmak için, etki alanına katılmış bir VM 'den Active Directory yönetim araçlarını kullanırsınız. Active Directory Yönetim Merkezi, OU 'Lar dahil olmak üzere Azure AD DS yönetilen bir etki alanında kaynakları görüntülemenize, düzenlemenize ve oluşturmanıza olanak sağlar.

> [!NOTE]
> Azure AD DS yönetilen bir etki alanında özel bir parola ilkesi oluşturmak için, *AAD DC Administrators* grubunun üyesi olan bir kullanıcı hesabında oturum açmış olmanız gerekir.

1. Başlangıç ekranından **Yönetim Araçları**' nı seçin. [Yönetim sanal makinesi oluşturmak][tutorial-create-management-vm]için öğreticide yüklü olan kullanılabilir yönetim araçlarının bir listesi gösterilir.
1. OU 'Ları oluşturup yönetmek için, yönetim araçları listesinden **Active Directory Yönetim Merkezi** ' yi seçin.
1. Sol bölmede, *contoso.com*gibi Azure AD DS yönetilen etki alanınızı seçin.
1. **Sistem** kapsayıcısını ve sonra **parola ayarları kapsayıcısı**açın.

    Azure AD DS yönetilen etki alanı için yerleşik bir parola ilkesi gösterilir. Bu yerleşik ilkeyi değiştiremezsiniz. Bunun yerine, varsayılan ilkeyi geçersiz kılmak için özel bir parola ilkesi oluşturun.

    ![Active Directory Yönetim Merkezi parola ilkesi oluşturma](./media/password-policy/create-password-policy-adac.png)

1. Sağdaki **Görevler** panelinde **Yeni > parola ayarları**' nı seçin.
1. **Parola ayarlarını oluştur** iletişim kutusunda, Ilke Için *Mycustomfgpp*gibi bir ad girin.
1. Birden çok parola ilkesi varsa, en yüksek önceliğe veya önceliğe sahip ilke bir kullanıcıya uygulanır. Sayı ne kadar düşükse öncelik o kadar yüksektir. Varsayılan parola ilkesinin önceliği *200*' dir.

    Varsayılan değer olan *1*' i geçersiz kılmak için özel parola ilkenizin önceliğini ayarlayın.

1. Diğer parola ilkesi ayarlarını istediğiniz gibi düzenleyin. Aşağıdaki anahtar noktalarını unutmayın:

    * Parola karmaşıklığı, yaşı veya sona erme zamanı gibi ayarlar yalnızca Azure AD DS yönetilen bir etki alanında el ile oluşturulan kullanıcılara.
    * Hesap kilitleme ayarları tüm kullanıcılar için geçerlidir, ancak yalnızca yönetilen etki alanı içinde etkili olur.

    ![Özel bir hassas parola ilkesi oluşturma](./media/how-to/custom-fgpp.png)

1. **Yanlışlıkla silinmeye karşı koru**seçeneğinin işaretini kaldırın. Bu seçenek işaretliyse, FGPP 'yi kaydedemezsiniz.
1. **Doğrudan Için geçerli** bölümünde, **Ekle** düğmesini seçin. **Kullanıcıları veya grupları seç** Iletişim kutusunda **konumlar** düğmesine tıklayın.

    ![Parola ilkesinin uygulanacağı kullanıcıları ve grupları seçin](./media/how-to/fgpp-applies-to.png)

1. Parola ilkeleri yalnızca gruplara uygulanabilir. **Konumlar** iletişim kutusunda, *contoso.com*gibi etki alanı adını genişletin ve ardından **aaddc kullanıcıları**gibi bir OU seçin. Uygulamak istediğiniz kullanıcı grubunu içeren özel bir OU varsa, bu OU 'yu seçin.

    ![Grubun ait olduğu OU 'yu seçin](./media/how-to/fgpp-container.png)

1. İlkeyi uygulamak istediğiniz grubun adını yazın ve ardından grubun var olduğunu doğrulamak için **adları denetle** ' yi seçin.

    ![FGPP uygulanacak grubu arayıp seçin](./media/how-to/fgpp-apply-group.png)

1. Şimdi, bölümünde seçtiğiniz grubun adı bölüm **Için geçerli** olarak görüntüleniyorsa, özel parola ilkenizi kaydetmek için **Tamam** ' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

Parola ilkeleri hakkında daha fazla bilgi ve Active Directory Yönetim Merkezi 'ni kullanma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Hassas parola ilkeleri hakkında bilgi edinin](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [AD Yönetim merkezini kullanarak hassas parola ilkelerini yapılandırma](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
