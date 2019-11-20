---
title: Azure Data Factory roller ve izinler
description: Veri fabrikaları oluşturmak ve alt kaynaklarla çalışmak için gereken rolleri ve izinleri açıklar.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: f5b4cbc670a429a84f42be79adb00057656a5483
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681419"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Azure Data Factory roller ve izinler

Bu makalede, Azure Data Factory kaynaklarını oluşturmak ve yönetmek için gereken roller ve bu roller tarafından verilen izinler açıklanmaktadır.

## <a name="roles-and-requirements"></a>Roller ve gereksinimler

Data Factory örnekleri oluşturmak için, Azure’da oturum açarken kullandığınız kullanıcı hesabı, *katkıda bulunan*, *sahip* veya *yönetici* rollerinin üyesi ya da bir Azure aboneliğinin yöneticisi olmalıdır. Abonelikte sahip olduğunuz izinleri görüntülemek için Azure portalına gidin, sağ üst köşeden kullanıcı adınızı ve sonra **İzinler**’i seçin. Birden çok aboneliğe erişiminiz varsa uygun aboneliği seçin. 

Data Factory için veri kümeleri, bağlı hizmetler, işlem hatları, tetikleyiciler ve tümleştirme çalışma zamanları gibi alt kaynaklar oluşturup yönetmek için aşağıdaki gereksinimlerin karşılanması gerekir:
- Azure portalda alt öğe oluşturup yönetebilmek için kaynak grubu düzeyinde **Data Factory Katılımcısı** rolüne veya daha üst düzey bir role sahip olmanız gerekir.
- PowerShell veya SDK ile alt öğe oluşturup yönetebilmek için kaynak düzeyinde **katkıda bulunan** rolü veya daha üst düzey bir rol yeterli olacaktır.

Kullanıcı rolü eklemeye ilişkin örnek yönergeler için [Rol ekleme](../billing/billing-add-change-azure-subscription-administrator.md) makalesine bakın.

## <a name="set-up-permissions"></a>İzinleri ayarla

Data Factory oluşturduktan sonra, diğer kullanıcıların Data Factory ile çalışmasına izin vermek isteyebilirsiniz. Bu erişimi diğer kullanıcılara vermek için, bunları veri fabrikasını içeren kaynak grubundaki yerleşik **Data Factory katkıda** bulunan rolüne eklemeniz gerekir.

### <a name="scope-of-the-data-factory-contributor-role"></a>Data Factory katkıda bulunan rolünün kapsamı

**Data Factory katkıda** bulunan rolündeki üyelik, kullanıcıların aşağıdaki işlemleri yapmanızı sağlar:
- Veri kümeleri, bağlı hizmetler, işlem hatları, Tetikleyiciler ve tümleştirme çalışma zamanları dahil olmak üzere veri fabrikaları ve alt kaynaklar oluşturun, düzenleyin ve silin.
- Kaynak Yöneticisi şablonları dağıtın. Kaynak Yöneticisi dağıtım, Azure portal Data Factory tarafından kullanılan dağıtım yöntemidir.
- Bir veri fabrikası için App Insights uyarılarını yönetin.
- Destek bileti oluşturun.

Bu rol hakkında daha fazla bilgi için bkz. [Data Factory katkıda bulunan rolü](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Kaynak Yöneticisi şablonu dağıtımı

Kaynak grubu düzeyinde veya yukarıdaki **Data Factory katkıda** bulunan rolü, kullanıcıların Kaynak Yöneticisi şablonları dağıtmasını sağlar. Sonuç olarak, rol üyeleri veri kümeleri, bağlı hizmetler, işlem hatları, Tetikleyiciler ve tümleştirme çalışma zamanları dahil olmak üzere hem veri fabrikalarını hem de alt kaynaklarını dağıtmak için Kaynak Yöneticisi şablonları kullanabilir. Bu roldeki üyelik, kullanıcının diğer kaynakları oluşturmalarına izin vermez.

Azure Repos ve GitHub üzerindeki izinler Data Factory izinlerinden bağımsızdır. Sonuç olarak, yalnızca okuyucu rolünün bir üyesi olan depo izinlerine sahip bir Kullanıcı, Data Factory alt kaynakları düzenleyebilir ve değişiklikleri depoya kaydedebilir, ancak bu değişiklikleri yayımlayamaz.

> [!IMPORTANT]
> **Data Factory katkıda** bulunan rolüyle şablon dağıtımı kaynak yöneticisi, izinlerinizi yükselmez. Örneğin, bir Azure sanal makinesi oluşturan bir şablon dağıtırsanız ve sanal makine oluşturma izniniz yoksa, dağıtım bir yetkilendirme hatasıyla başarısız olur.

### <a name="custom-scenarios-and-custom-roles"></a>Özel senaryolar ve özel roller

Bazen farklı veri fabrikası kullanıcıları için farklı erişim düzeyleri vermeniz gerekebilir. Örneğin:
- Kullanıcıların yalnızca belirli bir veri fabrikası üzerinde izinlere sahip olduğu bir gruba ihtiyacınız olabilir.
- Veya kullanıcıların yalnızca bir veri fabrikasını (veya fabrikaları) izleyebileceği ancak değiştiremeyeceğiniz bir gruba ihtiyacınız olabilir.

Özel roller oluşturup bu rollere Kullanıcı atayarak bu özel senaryolar elde edebilirsiniz. Özel roller hakkında daha fazla bilgi için bkz. [Azure 'Da özel roller](..//role-based-access-control/custom-roles.md).

Özel rollerle neler sağlayabileceğinizi gösteren birkaç örnek aşağıda verilmiştir:

- Bir kullanıcının, bir kaynak grubundaki herhangi bir veri fabrikasını Azure portal oluşturmasına, düzenlemesine veya silmesine izin verin.

  Yerleşik **Data Factory katkıda** bulunan rolünü Kullanıcı için kaynak grubu düzeyinde atayın. Bir abonelikteki herhangi bir veri fabrikasına erişime izin vermek istiyorsanız, rolü abonelik düzeyinde atayın.

- Bir kullanıcının bir veri fabrikasını görüntülemesine (okumasına) ve izlemesine izin verin, ancak düzenleyemez veya değiştirmeyin.

  Kullanıcı için veri fabrikası kaynağında yerleşik **okuyucu** rolünü atayın.

- Kullanıcının Azure portal tek bir veri fabrikasını düzenlemesine izin verin.

  Bu senaryo için iki rol ataması gerekir.

  1. Yerleşik **katkıda** bulunan rolünü Veri Fabrikası düzeyinde atayın.
  2. **Microsoft. resources/dağıtımlar/** izniyle özel bir rol oluşturun. Bu özel rolü, kaynak grubu düzeyinde kullanıcıya atayın.

- Kullanıcının yalnızca bağlı bir hizmette bağlantıyı test edebilmesini sağlar

    Şu eylemler için izinlerle özel bir rol rolü oluşturun: **Microsoft. DataFactory/Factory/getFeatureValue/Read** ve **Microsoft. DataFactory/Factory/getDataPlaneAccess/Read**. Bu özel rolü kullanıcı için Data Factory kaynağına atayın.

- Bir kullanıcının PowerShell veya SDK 'dan bir veri fabrikasını güncelleştirmesine izin verin, ancak Azure portal.

  Kullanıcı için veri fabrikası kaynağında yerleşik **katkıda** bulunan rolünü atayın. Bu rol, kullanıcının Azure portal kaynakları görmesini sağlar, ancak kullanıcı **Yayımla** ve **Tümünü Yayımla** düğmelerine erişemez.

## <a name="next-steps"></a>Sonraki adımlar

- Azure 'da roller hakkında daha fazla bilgi edinin- [rol tanımlarını anlayın](../role-based-access-control/role-definitions.md)

- **Data Factory katkıda** bulunan rol [Data Factory katkıda bulunan rolü](../role-based-access-control/built-in-roles.md#data-factory-contributor)hakkında daha fazla bilgi edinin.
