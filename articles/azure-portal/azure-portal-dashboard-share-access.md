---
title: Azure rol tabanlı erişim denetimi kullanarak Azure portal panoları paylaşma
description: Bu makalede, Azure rol tabanlı erişim denetimi kullanarak Azure portal bir panonun nasıl paylaşılacağını açıklanmaktadır.
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 336bfe9792c5ba4246458368e008a8c50833ed03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771558"
---
# <a name="share-azure-dashboards-by-using-azure-role-based-access-control"></a>Azure rol tabanlı erişim denetimi kullanarak Azure panoları paylaşma

Bir panoyu yapılandırdıktan sonra yayımlayabilir ve kuruluşunuzdaki diğer kullanıcılarla paylaşabilirsiniz. Diğer kullanıcıların, [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)kullanarak panonuzu görüntülemesine izin verebilirsiniz. Bir role tek bir kullanıcı veya Kullanıcı grubu atayın. Bu rol, bu kullanıcıların yayınlanan panoyu görüntüleyip görüntüleyemeyeceğini veya değiştiremeyeceklerini tanımlar.

Yayımlanan tüm panolar Azure kaynakları olarak uygulanır. Abonelikleriniz içinde yönetilebilir öğeler olarak mevcuttur ve bir kaynak grubunda yer alır. Bir erişim denetimi perspektifinden, panolar, sanal makine veya depolama hesabı gibi diğer kaynaklardan farklı değildir. Panodaki tek Kutucuklar, görüntülenen kaynaklara göre kendi erişim denetimi gereksinimlerini zorunlu tutar. Tek tek bölmelerde verileri korurken bir panoyu büyük ölçüde paylaşabilirsiniz.

## <a name="understanding-access-control-for-dashboards"></a>Panolar için erişim denetimini anlama

Azure rol tabanlı erişim denetimi (Azure RBAC) sayesinde, kullanıcıları rollere üç farklı kapsam düzeyinde atayabilirsiniz:

* aboneliği
* kaynak grubu
* kaynak

Atadığınız izinler abonelikten kaynağa doğru bir şekilde devralınır. Yayınlanan Pano bir kaynaktır. Yayımlanmış Pano için uygulanan aboneliğin rollerine atanmış kullanıcılarınız zaten olabilir.

Azure aboneliğiniz olduğunu ve takımınızın çeşitli üyelerine abonelik için *sahip*, *katkıda bulunan* veya *okuyucu* rollerinin atandığını varsayalım. Sahip veya katkıda bulunanlar olan kullanıcılar, abonelik içinde panoları listeleyebilir, görüntüleyebilir, oluşturabilir, değiştirebilir veya silebilir. Okuyucular olan kullanıcılar panoları listeleyebilir ve görüntüleyebilir, ancak bunları değiştiremez veya silemez. Okuyucu erişimi olan kullanıcılar, yayımlanan bir panoda bir sorunu giderirken, ancak bu değişiklikleri sunucuya geri yayımlayamazlar. Bunlar için panonun özel bir kopyasını oluşturabilir.

Kaynak grubuna çeşitli panolar veya ayrı bir Pano içeren izinler atayabilirsiniz. Örneğin, bir Kullanıcı grubunun abonelik genelinde sınırlı izinleri olması ve belirli bir panoya daha fazla erişimi olması gerektiğine karar verebilirsiniz. Bu kullanıcıları bu pano için bir role atayın.

## <a name="publish-a-dashboard"></a>Pano yayımlama

Aboneliğinizdeki bir kullanıcı grubuyla paylaşmak istediğiniz bir panoyu yapılandıralım. Aşağıdaki adımlarda, bir panonun depolama yöneticileri adlı bir grup ile nasıl paylaşılacağını gösterilmektedir. Grubunuzu dilediğiniz gibi adlandırabilirsiniz. Daha fazla bilgi için bkz. [Azure Active Directory grupları yönetme](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Erişim atamadan önce Panoyu yayımlamanız gerekir.

1. Panoda, **paylaşma**' yı seçin.

    ![Panonuz için paylaşma seçin](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. **Paylaşım ve erişim denetimi**' nde **Yayımla**' yı seçin.

    ![panonuzu yayımlayın](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Varsayılan olarak, paylaşım panonuzu **panolar** adlı bir kaynak grubuna yayınlar. Farklı bir kaynak grubu seçmek için onay kutusunu temizleyin.

Panonuz artık yayımlandı. Abonelikten devralınan izinler uygunsa, daha fazla şey yapmanız gerekmez. Kuruluşunuzdaki diğer kullanıcılar, abonelik düzeyi rolüne göre panoya erişebilir ve panoyu değiştirebilir.

## <a name="assign-access-to-a-dashboard"></a>Panoya erişim atama

Bu Pano için bir kullanıcı grubunu bir role atayabilirsiniz.

1. Panoyu yayımladıktan sonra **paylaşımı Yönet**' i seçin.

1. **Access Control** bu pano için zaten bir rolün atandığı mevcut kullanıcıları görmek için **rol atamaları** ' nı seçin.

1. Yeni bir kullanıcı veya grup eklemek için **Ekle** ve **rol ataması Ekle**' yi seçin.

    ![panoya erişim için Kullanıcı ekleme](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. **Katkıda bulunan** gibi izinleri temsil eden rolü seçin.

1. Role atanacak Kullanıcı veya grubu seçin. Listede Aradığınız kullanıcıyı veya grubu görmüyorsanız arama kutusunu kullanın. Kullanılabilir gruplar listeniz, Active Directory oluşturduğunuz gruplara bağlıdır.

1. Kullanıcıları veya grupları eklemeyi tamamladığınızda **Kaydet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* Rollerin listesi için bkz. [Azure yerleşik rolleri](../role-based-access-control/built-in-roles.md).
* Kaynakları yönetme hakkında bilgi edinmek için, bkz. [Azure Portal kullanarak Azure kaynaklarını yönetme](../azure-resource-manager/management/manage-resources-portal.md).