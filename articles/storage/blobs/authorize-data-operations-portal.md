---
title: Azure portal blob verilerine erişim yetkisi verme seçeneğini belirleyin
titleSuffix: Azure Storage
description: Azure portal kullanarak blob verilerine eriştiğinizde, Portal, kapsamakta olan Azure depolama 'ya istek yapar. Azure depolama 'ya yönelik bu isteklerin kimliği, Azure AD hesabınız veya depolama hesabı erişim anahtarı kullanılarak doğrulanabilir ve yetkilendirilir.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: contperf-fy21q1
ms.openlocfilehash: a12936f8f9f84dacfab4850253df665ae7758be1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102613260"
---
# <a name="choose-how-to-authorize-access-to-blob-data-in-the-azure-portal"></a>Azure portal blob verilerine erişim yetkisi verme seçeneğini belirleyin

[Azure Portal](https://portal.azure.com)kullanarak blob verilerine eriştiğinizde, Portal, kapsamakta olan Azure depolama 'ya istek yapar. Azure depolama 'ya yönelik bir isteğe Azure AD hesabınız veya depolama hesabı erişim anahtarı kullanılarak yetki atanabilir. Portal hangi yöntemi kullandığınızı gösterir ve uygun izinleriniz varsa iki arasında geçiş yapmanızı sağlar.  

Ayrıca, Azure portal tek bir blob karşıya yükleme işleminin nasıl yetkilendirilemez olduğunu belirtebilirsiniz. Varsayılan olarak, Portal bir blob karşıya yükleme işlemini yetkilendirmek için kullanmakta olduğunuz yöntemi kullanır, ancak bir blobu karşıya yüklerken bu ayarı değiştirme seçeneğiniz vardır.

## <a name="permissions-needed-to-access-blob-data"></a>Blob verilerine erişmek için gereken izinler

Azure portal blob verilerine erişimi nasıl yetkilendirmek istediğinize bağlı olarak, belirli izinlere ihtiyacınız vardır. Çoğu durumda, bu izinler Azure rol tabanlı erişim denetimi (Azure RBAC) aracılığıyla sağlanır. Azure RBAC hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Hesap erişim anahtarını kullan

Bir hesap erişim anahtarıyla blob verilerine erişmek için, size atanmış bir Azure rolüne sahip olmanız gerekir. Bu işlem, **Microsoft. Storage/storageAccounts/ListKeys/Action** Azure RBAC eylemini içerir. Bu Azure rolü yerleşik veya özel bir rol olabilir. **Microsoft. Storage/storageAccounts/ListKeys/Action** 'ı destekleyen yerleşik roller şunlardır:

- Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) rolü
- Azure Resource Manager [katkıda bulunan](../../role-based-access-control/built-in-roles.md#contributor) rolü
- [Depolama hesabı katılımcısı](../../role-based-access-control/built-in-roles.md#storage-account-contributor) rolü

Azure portal blob verilerine erişmeye çalıştığınızda, portal önce **Microsoft. Storage/storageAccounts/ListKeys/Action** ile bir rol atanıp atanmadığını denetler. Bu eylemle bir rol atanmışsa, Portal blob verilerine erişmek için hesap anahtarını kullanır. Bu eylemle bir rol atanmamışsa, Portal Azure AD hesabınızı kullanarak verilere erişmeye çalışır.

> [!IMPORTANT]
> Bir depolama hesabı Azure Resource Manager **salt okunur** bir kilit ile kilitlendiğinde, bu depolama hesabı Için [liste anahtarları](/rest/api/storagerp/storageaccounts/listkeys) işlemine izin verilmez. **Liste anahtarları** bir post işlemidir ve hesap Için **salt okunur** BIR kilit yapılandırıldığında tüm post işlemleri engellenir. Bu nedenle, hesap **salt okunur** bir kilit ile kilitlendiğinde, kullanıcıların portalda blob verilerine erişmek IÇIN Azure AD kimlik bilgilerini kullanması gerekir. Azure AD ile portaldaki blob verilerine erişme hakkında daha fazla bilgi için bkz. [Azure AD hesabınızı kullanma](#use-your-azure-ad-account).

> [!NOTE]
> Klasik abonelik yöneticisi rolleri hizmet yöneticisi ve Co-Administrator Azure Resource Manager [sahip](../../role-based-access-control/built-in-roles.md#owner) rolünün eşdeğerini içerir. **Sahip** rolü, **Microsoft. Storage/storageaccounts/ListKeys/ACTION** dahil olmak üzere tüm eylemleri içerir, bu nedenle bu yönetim rollerinden birine sahip bir kullanıcı da, blob verilerine hesap anahtarıyla erişebilirler. Daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Azure AD hesabınızı kullanın

Azure AD hesabınızı kullanarak Azure portal blob verilerine erişmek için aşağıdaki iki deyimin her ikisi de doğru olmalıdır:

- En azından, depolama hesabı düzeyine veya daha yüksek bir kapsamda Azure Resource Manager [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolü atanmıştı. **Okuyucu** rolü, en kısıtlı izinleri verir, ancak depolama hesabı yönetim kaynaklarına erişim izni veren başka bir Azure Resource Manager rolü de kabul edilebilir.
- Blob verilerine erişim sağlayan yerleşik veya özel bir rol atanmıştı.

Kullanıcının Azure portal depolama hesabı yönetim kaynaklarını görüntülemesi ve gezinebilmesi için **okuyucu** rol ataması veya başka bir Azure Resource Manager rol ataması gerekir. Blob verilerine erişim izni veren Azure rolleri, depolama hesabı yönetim kaynaklarına erişim izni vermez. Portalda blob verilerine erişmek için, kullanıcının depolama hesabı kaynaklarında gezinmek için izinleri olması gerekir. Bu gereksinim hakkında daha fazla bilgi için bkz. [Portal erişimi Için okuyucu rolünü atama](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Blob verilerinize erişimi destekleyen yerleşik roller şunlardır:

- [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure Data Lake Storage 2. için POSIX erişim denetimi için.
- [Depolama Blobu veri katılımcısı](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Bloblar için okuma/yazma/silme izinleri.
- [Depolama Blobu veri okuyucusu](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Bloblar için salt okuma izinleri.

Özel roller, yerleşik roller tarafından belirtilen izinlerin farklı birleşimlerini destekleyebilir. Azure özel rolleri oluşturma hakkında daha fazla bilgi için bkz. Azure [özel rolleri](../../role-based-access-control/custom-roles.md) ve [Azure kaynakları Için rol tanımlarını anlama](../../role-based-access-control/role-definitions.md).

> [!IMPORTANT]
> Azure portal Depolama Gezgini önizleme sürümü, blob verilerini görüntülemek ve değiştirmek için Azure AD kimlik bilgilerini kullanmayı desteklemez. Azure portal Depolama Gezgini, her zaman verilere erişmek için hesap anahtarlarını kullanır. Azure portal Depolama Gezgini kullanmak için **Microsoft. Storage/storageAccounts/ListKeys/Action** içeren bir rol atanmalıdır.

## <a name="navigate-to-blobs-in-the-azure-portal"></a>Azure portal bloblara git

Portalda blob verilerini görüntülemek için depolama hesabınıza **Genel Bakış ' a** gidin ve **bloblara** yönelik bağlantılara tıklayın. Alternatif olarak, menüdeki **kapsayıcılar** bölümüne gidebilirsiniz.

:::image type="content" source="media/authorize-data-operations-portal/blob-access-portal.png" alt-text="Azure portal blob verilerine nasıl gidebileceğiniz gösteren ekran görüntüsü":::

## <a name="determine-the-current-authentication-method"></a>Geçerli kimlik doğrulama yöntemini belirleme

Bir kapsayıcıya gittiğinizde, Azure portal kimlik doğrulaması için şu anda hesap erişim anahtarını veya Azure AD hesabınızı kullandığınızı gösterir.

### <a name="authenticate-with-the-account-access-key"></a>Hesap erişim anahtarı ile kimlik doğrulama

Hesap erişim anahtarını kullanarak kimlik doğrulaması yapıyorsanız, portalda kimlik doğrulama yöntemi olarak belirtilen **erişim anahtarı** ' nı görürsünüz:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Hesap anahtarı ile Şu anda kapsayıcılara erişen kullanıcının gösterildiği ekran görüntüsü":::

Azure AD hesabı 'nı kullanmaya geçiş yapmak için görüntüde vurgulanan bağlantıya tıklayın. Size atanan Azure rolleri aracılığıyla uygun izinleriniz varsa, devam edebilirsiniz. Ancak doğru izinleriniz yoksa, aşağıdakine benzer bir hata iletisi görürsünüz:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Azure AD hesabı erişimi desteklemiyorsa gösterilen hata":::

Azure AD hesabınızda görüntüleme izinleri yoksa listede hiçbir blob görünmediğine dikkat edin. Kimlik doğrulaması için erişim anahtarını yeniden kullanmak üzere **anahtara erişmek için** anahtara tıklayın.

### <a name="authenticate-with-your-azure-ad-account"></a>Azure AD hesabınızla kimlik doğrulama

Azure AD hesabınızı kullanarak kimlik doğrulaması yapıyorsanız, portalda kimlik doğrulama yöntemi olarak belirtilen **Azure AD Kullanıcı hesabını** görürsünüz:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Azure AD hesabı ile Şu anda kapsayıcılara erişen kullanıcının gösterildiği ekran görüntüsü":::

Hesap erişim anahtarını kullanmaya geçiş yapmak için görüntüde vurgulanan bağlantıya tıklayın. Hesap anahtarına erişiminiz varsa, devam edebilirsiniz. Ancak, hesap anahtarına erişiminizin olmaması durumunda aşağıdakine benzer bir hata iletisi görürsünüz:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-access-key.png" alt-text="Hesap anahtarına erişiminiz yoksa gösterilen hata":::

Hesap anahtarlarına erişiminiz yoksa listede hiçbir blob görünmediğine dikkat edin. Azure AD hesabınızı kimlik doğrulaması için yeniden kullanmak üzere **Azure AD Kullanıcı hesabı 'Na geçin** bağlantısına tıklayın.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Blob karşıya yükleme işleminin nasıl yetkilendiriyapılacağını belirtin

Azure portal bir blob yüklediğinizde, hesap erişim anahtarı veya Azure AD kimlik bilgilerinizle bu işlemin kimlik doğrulaması ve yetkisini verme işlemlerini belirtebilirsiniz. Varsayılan olarak, portal geçerli kimlik doğrulama yöntemini [geçerli kimlik doğrulama yöntemini belirleme](#determine-the-current-authentication-method)bölümünde gösterildiği gibi kullanır.

Blob yükleme işleminin nasıl yetkilendirileyeceğinizi belirtmek için şu adımları izleyin:

1. Azure portal, blobu yüklemek istediğiniz kapsayıcıya gidin.
1. **Karşıya Yükle** düğmesini seçin.
1. Blob 'un gelişmiş özelliklerini göstermek için **Gelişmiş** bölümünü genişletin.
1. **Kimlik doğrulama türü** alanında, aşağıdaki görüntüde gösterildiği gibi, Azure AD hesabınızı kullanarak veya hesap erişim anahtarıyla karşıya yükleme işlemini yetkilendirmek isteyip istemediğinizi belirtin:

    :::image type="content" source="media/authorize-data-operations-portal/auth-blob-upload.png" alt-text="Blob karşıya yükleme sırasında yetkilendirme yönteminin nasıl değiştirileceğini gösteren ekran görüntüsü":::

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory kullanarak Azure Blob 'larına ve kuyruklara erişim kimlik doğrulaması](../common/storage-auth-aad.md)
- [Azure portalı kullanarak blob ve kuyruk verilerine erişim için Azure rolü atama](../common/storage-auth-aad-rbac-portal.md)
- [Blob ve kuyruk verilerine erişim için Azure rolü atamak üzere Azure CLı 'yi kullanma](../common/storage-auth-aad-rbac-cli.md)
- [Blob ve kuyruk verilerine erişim için bir Azure rolü atamak üzere Azure PowerShell modülünü kullanın](../common/storage-auth-aad-rbac-powershell.md)
