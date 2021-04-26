---
title: Öğretici-Azure AD Domain Services bir çoğaltma kümesi oluşturma | Microsoft Docs
description: Hizmet dayanıklılığı için Azure portal çoğaltma kümelerini oluşturma ve kullanma hakkında bilgi edinin Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: justinha
ms.openlocfilehash: a016287fedbd303a5571100130769ce4299828bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798600"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services"></a>Öğretici: Azure Active Directory Domain Services dayanıklılık veya coğrafi konum için çoğaltma kümeleri oluşturma ve kullanma

Azure Active Directory Domain Services (Azure AD DS) yönetilen bir etki alanının esnekliğini artırmak veya uygulamalarınıza yakın ek coğrafi konumlara dağıtım yapmak için *çoğaltma kümelerini* kullanabilirsiniz. *Aaddscontoso.com* gibi her Azure AD DS yönetilen etki alanı ad alanı bir ilk çoğaltma kümesi içerir. Diğer Azure bölgelerinde ek çoğaltma kümeleri oluşturma özelliği, yönetilen bir etki alanı için coğrafi dayanıklılık sağlar.

Azure AD DS 'yi destekleyen herhangi bir Azure bölgesindeki eşlenen herhangi bir sanal ağa bir çoğaltma kümesi ekleyebilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal ağ gereksinimlerini anlayın
> * Çoğaltma kümesi oluşturma
> * Bir çoğaltma kümesini silme

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure Resource Manager dağıtım modeli kullanılarak oluşturulan ve Azure AD kiracınızda yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturun ve yapılandırın][tutorial-create-instance].

    > [!IMPORTANT]
    > Klasik dağıtım modeli kullanılarak oluşturulan yönetilen etki alanları çoğaltma kümelerini kullanamaz. Ayrıca, yönetilen etki alanınız için en az *Kurumsal* SKU kullanmanız gerekir. Gerekirse, [yönetilen bir etki alanı için SKU 'yu değiştirin][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bu öğreticide, Azure portal kullanarak çoğaltma kümeleri oluşturup yönetirsiniz. Başlamak için öncelikle [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="networking-considerations"></a>Ağ konusunda dikkat edilmesi gerekenler

Çoğaltma kümelerini barındıran sanal ağların birbirleriyle iletişim kurabilmesi gerekir. Azure AD DS bağımlı olan uygulama ve hizmetlerin ayrıca çoğaltma kümelerini barındıran sanal ağlara ağ bağlantısı olması gerekir. Azure sanal ağ eşlemesi, tümüyle bir ağ oluşturma ağı oluşturmak için tüm sanal ağlar arasında yapılandırılmalıdır. Bu eşlemeler, çoğaltma kümeleri arasında etkin site içi çoğaltmayı etkinleştirir.

Azure AD DS 'de çoğaltma kümelerini kullanabilmeniz için aşağıdaki Azure sanal ağ gereksinimlerini gözden geçirin:

* Sanal ağ eşleme ve yönlendirmeye izin vermek için çakışan IP adresi alanları kullanmaktan kaçının.
* Senaryonuzu desteklemek için yeterli IP adresi olan alt ağlar oluşturun.
* Azure AD DS 'nin kendi alt ağına sahip olduğundan emin olun. Bu sanal ağ alt ağını uygulama VM 'Leri ve hizmetleriyle paylaşmayın.
* Eşlenen sanal ağlar geçişli DEĞILDIR.

> [!TIP]
> Azure portal bir çoğaltma kümesi oluşturduğunuzda, sanal ağlar arasındaki ağ eşlemeleri sizin için oluşturulur.
>
> Gerekirse, Azure portal bir çoğaltma kümesi eklediğinizde bir sanal ağ ve alt ağ oluşturabilirsiniz. Ya da, bir çoğaltma kümesi için hedef bölgede var olan sanal ağ kaynaklarını seçebilirsiniz ve henüz yoksa, eşlere otomatik olarak oluşturulmasını sağlayabilirsiniz.

## <a name="create-a-replica-set"></a>Çoğaltma kümesi oluşturma

*Aaddscontoso.com* gibi yönetilen bir etki alanı oluşturduğunuzda, bir ilk çoğaltma kümesi oluşturulur. Ek çoğaltma kümeleri aynı ad alanını ve yapılandırmayı paylaşır. Yapılandırma, Kullanıcı kimliği ve kimlik bilgileri, gruplar, Grup İlkesi nesneleri, bilgisayar nesneleri ve diğer değişiklikler dahil olmak üzere Azure AD DS değişiklikleri, yönetilen etki alanındaki tüm çoğaltma kümelerine AD DS çoğaltma kullanılarak uygulanır.

Bu öğreticide, ilk Azure AD DS çoğaltma kümesinden farklı bir Azure bölgesinde ek bir çoğaltma kümesi oluşturacaksınız.

Ek bir çoğaltma kümesi oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal, araması yapın ve **Azure AD Domain Services** seçin.
1. *Aaddscontoso.com* gibi yönetilen etki alanınızı seçin.
1. Sol taraftaki **çoğaltma kümeleri**' ni seçin. Her bir yönetilen etki alanı, aşağıdaki örnekte gösterildiği gibi seçili bölgede bir ilk çoğaltma kümesi içerir:

    ![Azure portal bir çoğaltma kümesini görüntüleme ve ekleme için örnek ekran görüntüsü](./media/tutorial-create-replica-set/replica-set-list.png)

    Ek bir çoğaltma kümesi oluşturmak için **+ Ekle**' yi seçin.

1. *Çoğaltma kümesi Ekle* penceresinde, *Doğu ABD* gibi hedef bölgeyi seçin.

    Hedef bölgede, *VNET-eastus* gibi bir sanal ağ seçin ve ardından *aeklemeleri-subnet* gibi bir alt ağ seçin. Gerekirse, hedef bölgeye bir sanal ağ eklemek için **Yeni oluştur** ' u seçin ve ardından Azure AD DS için bir alt ağ oluşturmak için **yönetin** .

    Mevcut değilse, Azure sanal ağ eşlemeleri, mevcut yönetilen etki alanının sanal ağı ile hedef sanal ağ arasında otomatik olarak oluşturulur.

    Aşağıdaki örnek ekran görüntüsünde, *Doğu ABD* yeni bir çoğaltma kümesi oluşturma işlemi gösterilmektedir:

    ![Azure portal bir çoğaltma kümesi oluşturmak için örnek ekran görüntüsü](./media/tutorial-create-replica-set/create-replica-set.png)

1. Hazırlanıyor, **Kaydet**' i seçin.

Çoğaltma kümesini oluşturma işlemi, kaynaklar hedef bölgede oluşturulduğu zaman zaman alır. Yönetilen etki alanının kendisi daha sonra AD DS çoğaltma kullanılarak çoğaltılır.

Aşağıdaki örnek ekran görüntüsünde gösterildiği gibi, dağıtım devam ettiği için çoğaltma kümesi, *sağlama* olarak rapor eder. Tamamlandığında, çoğaltma kümesi *çalışıyor* olarak gösterilir.

![Azure portal çoğaltma kümesi dağıtım durumunun örnek ekran görüntüsü](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>Bir çoğaltma kümesini silme

Yönetilen bir etki alanı şu anda dört çoğaltmayla sınırlıdır-ilk çoğaltma kümesi ve üç ek çoğaltma kümesi. Artık bir çoğaltma kümesine gerek duymuyorsanız veya başka bir bölgede bir çoğaltma kümesi oluşturmak istiyorsanız gereksiz çoğaltma kümelerini silebilirsiniz.

> [!IMPORTANT]
> Yönetilen bir etki alanındaki son çoğaltma kümesini silemezsiniz.

Bir çoğaltma kümesini silmek için aşağıdaki adımları izleyin:

1. Azure portal, araması yapın ve **Azure AD Domain Services** seçin.
1. *Aaddscontoso.com* gibi yönetilen etki alanınızı seçin.
1. Sol taraftaki **çoğaltma kümeleri**' ni seçin. Çoğaltma kümeleri listesinden, silmek istediğiniz çoğaltma kümesinin yanındaki **...** bağlam menüsünü seçin.
1. Bağlam menüsünden **Sil** ' i seçin ve ardından çoğaltma kümesini silmek istediğinizi onaylayın.

> [!NOTE]
> Çoğaltma kümesi silme işlemi zaman alan bir işlem olabilir.

Artık çoğaltma kümesi tarafından kullanılan sanal ağa veya eşlemeye ihtiyacınız yoksa, bu kaynakları da silebilirsiniz. Diğer bölgedeki başka hiçbir uygulama kaynağına, silmeden önce ağ bağlantılarına gerek olmadığından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Sanal Ağ eşlemesini yapılandırma
> * Farklı bir coğrafi bölgede çoğaltma kümesi oluşturma
> * Bir çoğaltma kümesini silme

Daha fazla kavramsal bilgi için Azure AD DS 'de çoğaltma kümelerinin nasıl çalıştığını öğrenin.

> [!div class="nextstepaction"]
> [Çoğaltma kümesi kavramları ve özellikleri][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
