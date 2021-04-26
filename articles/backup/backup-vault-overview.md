---
title: Yedekleme kasalarına genel bakış
description: Yedekleme kasalarına genel bakış.
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: e2d720da9474a35870de01559201d22c9e5b567f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739089"
---
# <a name="backup-vaults-overview"></a>Yedekleme kasaları genel bakış

Bu makalede bir yedekleme kasasının özellikleri açıklanmaktadır. Yedekleme Kasası, Azure Backup desteklediği bazı yeni iş yükleri için yedekleme verilerini barındıran Azure 'da bir depolama varlıktır. Yedekleme kasalarını, PostgreSQL için Azure veritabanı sunucuları ve Azure Backup destekleyeceği daha yeni iş yükleri gibi çeşitli Azure hizmetleri için yedekleme verilerini tutmak üzere kullanabilirsiniz. Yedekleme kasaları, yedekleme verilerinizi düzenlemenizi kolaylaştırır, ancak yönetim yükünü en aza indirir. Yedekleme kasaları, şu gibi özellikler sağlayan Azure Azure Resource Manager modelini temel alır:

- **Yedekleme verilerini güvenli hale getirmeye yardımcı olmak Için geliştirilmiş yetenekler**: yedekleme kasaları sayesinde, bulut yedeklemelerini korumak için güvenlik özellikleri sağlar Azure Backup. Güvenlik özellikleri, üretim ve yedekleme sunucuları tehlikeye atılsa bile yedeklemelerinizi güvenli hale getirmek ve verileri güvenli bir şekilde kurtarmanız için de güvence altına alınır. [Daha fazla bilgi edinin](backup-azure-security-feature.md)

- Azure **rol tabanlı erişim denetimi (Azure RBAC)**: Azure RBAC, Azure 'da ayrıntılı erişim yönetimi denetimi sağlar. [Azure çeşitli yerleşik roller sağlar](../role-based-access-control/built-in-roles.md)ve Azure Backup [kurtarma noktalarını yönetmek için üç yerleşik rol](backup-rbac-rs-vault.md)içerir. Yedekleme kasaları, bu kullanıcı rollerine yönelik yedekleme ve geri yükleme erişimini sınırlayan Azure RBAC ile uyumludur. [Daha fazla bilgi edinin](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Yedekleme kasasındaki depolama ayarları

Yedekleme Kasası, zaman içinde oluşturulan yedeklemeleri ve kurtarma noktalarını depolayan bir varlıktır. Yedekleme Kasası, korunan sanal makinelerle ilişkili yedekleme ilkelerini de içerir.

- Azure Backup kasa için depolamayı otomatik olarak işler. Yedekleme kasasını oluştururken iş gereksinimlerinize uygun depolama yedekliliği seçin.

- Depolama artıklığı hakkında daha fazla bilgi edinmek için [coğrafi](../storage/common/storage-redundancy.md#geo-redundant-storage) ve [Yerel](../storage/common/storage-redundancy.md#locally-redundant-storage) artıklık üzerinde bu makalelere bakın.

## <a name="encryption-settings-in-the-backup-vault"></a>Yedekleme kasasındaki şifreleme ayarları

Bu bölümde, yedekleme kasasında depolanan yedekleme verilerinizi şifrelemek için kullanılabilen seçenekler açıklanmaktadır. Azure Backup hizmeti, yedekleme kasasının yönetilen kimliğine erişmek için **yedekleme yönetim hizmeti** uygulamasını kullanır, ancak Azure Key Vault.


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Platform tarafından yönetilen anahtarlar kullanılarak yedekleme verilerinin şifrelenmesi

Varsayılan olarak, tüm verileriniz platform tarafından yönetilen anahtarlar kullanılarak şifrelenir. Bu şifrelemeyi etkinleştirmek için uçtan herhangi bir açık işlem yapmanız gerekmez. Yedekleme kasanıza yedeklenen tüm iş yükleri için geçerlidir.

## <a name="create-a-backup-vault"></a>Yedekleme Kasası oluşturma

Yedekleme Kasası, zaman içinde oluşturulan kurtarma noktalarını depolayan ve yedeklemeyle ilgili işlemleri gerçekleştirmek için bir arabirim sağlayan bir yönetim varlıktır. Bu işlemler arasında isteğe bağlı yedekleme, geri yükleme gerçekleştirme ve yedekleme ilkesi oluşturma sayılabilir.

Bir yedekleme Kasası oluşturmak için aşağıdaki adımları izleyin.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

<https://portal.azure.com> adresinden Azure portalında oturum açın.

### <a name="create-backup-vault"></a>Yedekleme Kasası oluşturma

1. Arama kutusuna **yedekleme kasaları** yazın.
1. **Hizmetler**' in altında, **yedekleme kasaları**' nı seçin.
1. **Yedekleme kasaları** sayfasında **Ekle**' yi seçin.
1. **Temel bilgiler sekmesinde**, **proje ayrıntıları** altında, doğru aboneliğin seçildiğinden emin olun ve ardından yeni kaynak grubu **Oluştur** ' u seçin. Ad için *Myresourcegroup* yazın.

  ![Yeni kaynak grubu oluştur](./media/backup-vault-overview/new-resource-group.png)

1. **Örnek ayrıntıları**' nın altında, **Yedekleme Kasası adı** için *mykasa* yazın ve **bölgeniz** için *Doğu ABD* Bu örnekte istediğiniz bölgeyi seçin.
1. Şimdi **depolama yedeklerinizi** seçin. Depolama yedekliliği, öğeler kasada korunduktan sonra değiştirilemez.
1. Azure 'u birincil yedek depolama uç noktası olarak kullanıyorsanız, varsayılan **coğrafi olarak yedekli** ayarını kullanmaya devam edebilirsiniz.
1. Azure’u birincil yedek depolama uç noktası olarak kullanmıyorsanız, Azure depolama maliyetlerini azaltan **Yerel olarak yedekli** seçeneğini belirleyin.
1. [Coğrafi](../storage/common/storage-redundancy.md#geo-redundant-storage) ve [Yerel](../storage/common/storage-redundancy.md#locally-redundant-storage) artıklık hakkında daha fazla bilgi edinin.

  ![Depolama artıklığı seçin](./media/backup-vault-overview/storage-redundancy.png)

1. Sayfanın alt kısmındaki gözden geçir + Oluştur düğmesini seçin.

    ![Gözden geçir + oluştur ' u seçin](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Yedekleme kasasını silme

Bu bölümde, bir yedekleme kasasının nasıl silineceği açıklanmaktadır. Bağımlılıkları kaldırmak ve ardından kasayı silmek için yönergeler içerir.

### <a name="before-you-start"></a>Başlamadan önce

Bir yedekleme kasasını aşağıdaki bağımlılıklardan biriyle silemezsiniz:

- Korumalı veri kaynakları (örneğin, PostgreSQL için Azure veritabanı sunucuları) içeren bir kasayı silemezsiniz.
- Yedekleme verileri içeren bir kasayı silemezsiniz.

Bağımlılıkları kaldırmadan kasayı silmeye çalışırsanız aşağıdaki hata iletileriyle karşılaşırsınız:

>Kasada mevcut yedekleme örnekleri veya yedekleme ilkeleri olduğundan, Yedekleme Kasası silinemiyor. Kasadaki mevcut tüm yedekleme örneklerini ve yedekleme ilkelerini silip kasayı silmeyi deneyin.

### <a name="proper-way-to-delete-a-vault"></a>Kasayı silmenin doğru yolu

>[!WARNING]
Aşağıdaki işlem bozucu olduğundan geri alınamaz. Korunan sunucuyla ilişkili tüm yedekleme verileri ve yedekleme öğeleri kalıcı olarak silinecek. Dikkatli olun.

Bir kasayı doğru bir şekilde silmek için şu sırada bulunan adımları izlemeniz gerekir:

- Korunan herhangi bir öğe olup olmadığını doğrulayın:
  - Sol gezinti çubuğundaki **yedekleme örnekleri** ' ne gidin. Burada listelenen tüm öğelerin önce silinmesi gerekir.

Bu adımları tamamladıktan sonra kasayı silmeye devam edebilirsiniz.

### <a name="delete-the-backup-vault"></a>Yedekleme kasasını silme

Kasada daha fazla öğe yoksa, kasa panosunda **Sil** ' i seçin. Kasayı silmek isteyip istemediğinizi soran bir onay metni görürsünüz.

![Kasayı silme](./media/backup-vault-overview/delete-vault.png)

1. Kasayı silmek istediğinizi doğrulamak için **Evet** ' i seçin. Kasa silinir. Portal **Yeni** hizmet menüsüne geri döner.

## <a name="monitor-and-manage-the-backup-vault"></a>Yedekleme kasasını izleme ve yönetme

Bu bölümde, yedekleme kasalarınızı izlemek ve yönetmek için Yedekleme Kasası **genel bakış** panosunun nasıl kullanılacağı açıklanmaktadır. Genel bakış bölmesi iki kutucuk içerir: **işler** ve **örnekler**.

![Genel Bakış Panosu](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Yedekleme örneklerini yönet

**İşler** kutucuğunda yedekleme kasasındaki tüm yedekleme ve ilgili işleri geri yükleme ' nin özetlenen görünümünü alırsınız. Bu kutucuktaki sayıların seçilmesi, belirli bir veri kaynağı türü, işlem türü ve durum için iş hakkında daha fazla bilgi görüntülemenizi sağlar.

![Yedekleme örnekleri](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Yedekleme işlerini yönetme

**Yedekleme örnekleri** kutucuğunda, yedekleme kasasındaki tüm yedekleme örneklerinin özetlenmiş bir görünümünü alırsınız. Bu kutucuktaki sayıların seçilmesi, belirli bir veri kaynağı türü ve koruma durumu için yedekleme örnekleri hakkında daha fazla bilgi görüntülemenizi sağlar.

![Yedekleme işleri](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure PostgreSQL veritabanlarında yedeklemeyi yapılandırma](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
