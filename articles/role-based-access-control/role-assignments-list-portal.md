---
title: Azure portal-Azure RBAC kullanarak Azure rol atamalarını listeleme
description: Azure portal ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak kullanıcıların, grupların, hizmet sorumlularının veya yönetilen kimliklerin hangi kaynakların erişimi olduğunu belirlemeyi öğrenin.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.openlocfilehash: 7d53df4159c071c17657e8a8367da605b6a5dc9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556170"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Azure portal kullanarak Azure rol atamalarını listeleyin

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Bu makalede, Azure portal kullanarak rol atamalarının nasıl listeleneceğini açıklanmaktadır.

> [!NOTE]
> Kuruluşunuzun, [Azure tarafından yetkilendirilen kaynak yönetimi](../lighthouse/concepts/azure-delegated-resource-management.md)kullanan bir hizmet sağlayıcısına dış kaynaklı yönetim işlevleri varsa, bu hizmet sağlayıcısı tarafından yetkilendirilen rol atamaları burada gösterilmez.

## <a name="list-role-assignments-for-a-user-or-group"></a>Bir kullanıcı veya grup için rol atamalarını listeleme

Bir abonelikte bir kullanıcıya veya gruba atanan rolleri görmenin hızlı bir yolu, **Azure rol atamaları** bölmesini kullanmaktır.

1. Azure portal, Azure portal menüsünden **tüm hizmetler** ' i seçin.

1. **Azure Active Directory** ' yi seçin ve ardından **Kullanıcılar** veya **gruplar**' ı seçin.

1. İçin rol atamalarını listelemek istediğiniz kullanıcıya veya gruba tıklayın.

1. **Azure rol atamaları**' na tıklayın.

    Yönetim grubu, abonelik, kaynak grubu veya kaynak gibi çeşitli kapsamlardaki seçili kullanıcı veya gruba atanan rollerin listesini görürsünüz. Bu liste, okuma izninizin olduğu tüm rol atamalarını içerir.

    ![Bir kullanıcıya rol atama](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. Aboneliği değiştirmek için **abonelikler** listesine tıklayın.

## <a name="list-owners-of-a-subscription"></a>Abonelik sahiplerini listeleme

Bir abonelik için [sahip](built-in-roles.md#owner) rolüne atanan kullanıcılar, abonelikteki her şeyi yönetebilir. Bir aboneliğin sahiplerini listelemek için bu adımları izleyin.

1. Azure portal, **tüm hizmetler** ve ardından **abonelikler**' e tıklayın.

1. Sahiplerini listelemek istediğiniz aboneliğe tıklayın.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. Bu aboneliğin tüm rol atamalarını görüntülemek için **Rol atamaları** sekmesine tıklayın.

1. Bu abonelik için sahip rolüne atanmış tüm kullanıcıları görmek için **sahipler** bölümüne ilerleyin.

   ![Abonelik erişim denetimi-rol atamaları sekmesi](./media/role-assignments-list-portal/sub-access-control-role-assignments-owners.png)

## <a name="list-role-assignments-at-a-scope"></a>Rol atamalarını bir kapsamda listeleme

1. Azure portal, **tüm hizmetler** ' e tıklayın ve kapsamı seçin. Örneğin, **Yönetim grupları**, **abonelikler**, **kaynak grupları** veya bir kaynak seçebilirsiniz.

1. Belirli kaynağa tıklayın.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. Bu kapsamdaki tüm rol atamalarını görüntülemek için **rol atamaları** sekmesine tıklayın.

   ![Erişim denetimi-rol atamaları sekmesi](./media/role-assignments-list-portal/rg-access-control-role-assignments.png)

   Rol atamaları sekmesinde kimlerin bu kapsamda erişimi olduğunu görebilirsiniz. Bazı rollerin kapsamı **Bu kaynak** olarak belirlenmişken diğerlerinin başka bir kapsamdan **(Devralınmış)** olduğuna dikkat edin. Erişim özellikle bu kaynağa atanır veya bir atamadan üst kapsama devralınır.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Kapsamdaki Kullanıcı için rol atamalarını listeleme

Bir kullanıcının, grubun, hizmet sorumlusunun veya yönetilen kimliğin erişimini listelemek için, rol atamalarını listeleyin. Belirli bir kapsamdaki tek bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik için rol atamalarını listelemek üzere bu adımları izleyin.

1. Azure portal, **tüm hizmetler** ' e tıklayın ve kapsamı seçin. Örneğin, **Yönetim grupları**, **abonelikler**, **kaynak grupları** veya bir kaynak seçebilirsiniz.

1. Belirli kaynağa tıklayın.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. **Erişimi denetle** sekmesine tıklayın.

    ![Kaynak grubu erişim denetimi-erişim sekmesine bakın](./media/role-assignments-list-portal/rg-access-control-check-access.png)

1. **Bul** listesinde, erişimi denetlemek istediğiniz kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik ' i seçin.

1. Arama kutusuna, görünen adlar, e-posta adresleri veya nesne tanımlayıcıları için dizinde arama yapmak üzere bir dize girin.

    ![Erişim denetimi seçim listesi](./media/shared/rg-check-access-select.png)

1. **Atamalar** bölmesini açmak için güvenlik sorumlusuna tıklayın.

    Bu bölmede, bu kapsamda seçili güvenlik sorumlusu için erişimi görebilir ve bu kapsama devralınmış olursunuz. Alt kapsamların atamaları listelenmez. Aşağıdaki atamaları görürsünüz:

    - Azure RBAC ile rol atamaları eklendi.
    - Azure şemaları veya Azure yönetilen uygulamaları kullanılarak eklenen atamaları engelleyin.
    - Klasik dağıtımlar için klasik hizmet yöneticisi veya Co-Administrator atamaları. 

    ![Atamalar bölmesi](./media/shared/rg-check-access-assignments-user.png)

## <a name="list-role-assignments-for-a-managed-identity"></a>Yönetilen bir kimlik için rol atamalarını listeleyin

Belirli bir kapsamdaki sistem tarafından atanan ve Kullanıcı tarafından atanan Yönetilen kimlikler için rol atamalarını, daha önce açıklandığı gibi **erişim denetimi (IAM)** dikey penceresini kullanarak listeleyebilirsiniz. Bu bölümde, yalnızca yönetilen kimliğin rol atamalarının nasıl listeleneceğini açıklanmaktadır.

### <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

1. Azure portal, sistem tarafından atanan bir yönetilen kimlik açın.

1. Sol taraftaki menüden **kimlik**' e tıklayın.

    ![Sistem tarafından atanan yönetilen kimlik](./media/shared/identity-system-assigned.png)

1. **İzinler** altında **Azure rol atamaları**' na tıklayın.

    Yönetim grubu, abonelik, kaynak grubu veya kaynak gibi çeşitli kapsamlardaki seçili sistem tarafından atanan yönetilen kimliğe atanan rollerin listesini görürsünüz. Bu liste, okuma izninizin olduğu tüm rol atamalarını içerir.

    ![Sistem tarafından atanan yönetilen kimlik için rol atamaları](./media/shared/role-assignments-system-assigned.png)

1. Aboneliği değiştirmek için **abonelik** listesine tıklayın.

### <a name="user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik

1. Azure portal, Kullanıcı tarafından atanan bir yönetilen kimlik açın.

1. **Azure rol atamaları**' na tıklayın.

    Yönetim grubu, abonelik, kaynak grubu veya kaynak gibi çeşitli kapsamlardaki seçili kullanıcı tarafından atanan yönetilen kimliğe atanan rollerin listesini görürsünüz. Bu liste, okuma izninizin olduğu tüm rol atamalarını içerir.

    ![Kullanıcı tarafından atanan yönetilen kimlik için rol atamalarını gösteren ekran görüntüsü.](./media/shared/role-assignments-user-assigned.png)

1. Aboneliği değiştirmek için **abonelik** listesine tıklayın.

## <a name="list-number-of-role-assignments"></a>Rol atamalarının sayısını listeleyin

Her abonelikte en fazla **2000** rol ataması yapabilirsiniz. Bu sınır, abonelik, kaynak grubu ve kaynak kapsamlarındaki rol atamalarını içerir. Bu sınırı izlemenize yardımcı olması için, **rol atamaları** sekmesi geçerli aboneliğin rol atamalarının sayısını listeleyen bir grafik içerir.

![Erişim denetimi-rol atamaları grafiğinin sayısı](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

En büyük sayıya yakınlarınız varsa ve daha fazla rol ataması eklemeye çalışırsanız, **rol ataması Ekle** bölmesinde bir uyarı görürsünüz. Rol atamalarının sayısını azaltabileceğiniz yollar için bkz. [Azure RBAC sorunlarını giderme](troubleshooting.md#azure-role-assignments-limit).

![Erişim denetimi-rol atama uyarısı ekleme](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments"></a>Rol atamalarını indir

Rol atamalarını CSV veya JSON biçimlerindeki bir kapsamda indirebilirsiniz. Bu, bir elektronik tablodaki listeyi incelemeniz veya bir aboneliği geçirirken bir sayım almanız gerektiğinde yararlı olabilir.

Rol atamalarını indirdiğinizde aşağıdaki ölçütleri göz önünde bulundurmanız gerekir:

- Dizin okuyucuları rolü gibi dizin okuma izinleriniz yoksa DisplayName, Signınname ve ObjectType sütunları boş olur.
- Güvenlik sorumlusu silinmiş olan rol atamaları dahil değildir.
- Klasik yöneticilere verilen erişim dahil değildir.

Rol atamalarını bir kapsamda indirmek için bu adımları izleyin.

1. Azure portal, **tüm hizmetler** ' e tıklayın ve ardından rol atamalarını indirmek istediğiniz kapsamı seçin. Örneğin, **Yönetim grupları**, **abonelikler**, **kaynak grupları** veya bir kaynak seçebilirsiniz.

1. Belirli kaynağa tıklayın.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. Rol atamalarını indir bölmesini açmak için rol **atamalarını** İndir ' e tıklayın.

    ![Erişim denetimi-rol atamalarını Indirin](./media/role-assignments-list-portal/download-role-assignments.png)

1. İndirilen dosyaya dahil etmek istediğiniz rol atamalarını seçmek için onay kutularını kullanın.

    - **Devralınan** -geçerli kapsam için devralınan rol atamalarını dahil edin.
    - **Geçerli kapsamda** -geçerli kapsam için rol atamaları ekleyin.
    - **Alt öğeler** -geçerli kapsamın altındaki düzeylerde rol atamaları ekleyin. Bu onay kutusu yönetim grubu kapsamı için devre dışıdır.

1. Virgülle ayrılmış değerler (CSV) veya JavaScript Nesne Gösterimi (JSON) olabilen dosya biçimini seçin.

1. Dosya adını belirtin.

1. İndirmeyi başlatmak için **Başlat** ' a tıklayın.

    Aşağıda her dosya biçimi için çıktının örnekleri gösterilmektedir.

    ![Rol atamalarını CSV olarak indir](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![JSON biçiminde olarak indirilen rol atamalarının ekran görüntüsü.](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal kullanarak Azure rolleri atama](role-assignments-portal.md)
- [Azure RBAC sorunlarını giderme](troubleshooting.md)
