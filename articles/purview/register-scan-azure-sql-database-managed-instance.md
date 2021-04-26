---
title: Azure SQL veritabanı yönetilen örneğini kaydetme ve tarama
description: Bu öğreticide Azure SQL veritabanı yönetilen örneği taraması açıklanmaktadır
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: a30980ba61a1dfec918dce1a55e78f1be2a36dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677846"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Azure SQL veritabanı yönetilen örneğini kaydetme ve tarama

Bu makalede, Azure SQL veritabanı yönetilen örnek veri kaynağını purview 'a kaydetme ve üzerinde tarama ayarlama özetlenmektedir.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Azure SQL veritabanı yönetilen örneği veri kaynağı aşağıdaki işlevleri destekler:

- Azure SQL veritabanı yönetilen örneği 'nde meta verileri ve sınıflandırmayı yakalamak için **tam ve artımlı taramalar** .

- ADF Copy ve veri akışı etkinlikleri için veri varlıkları arasında **kökenini** .

### <a name="known-limitations"></a>Bilinen sınırlamalar

Azure purview, Azure SQL yönetilen örneği 'nde [görünümlerin](/sql/relational-databases/views/views?view=azuresqldb-mi-current&preserve-view=true) taranmasını desteklemez.

## <a name="prerequisites"></a>Önkoşullar

- Henüz bir tane yoksa yeni bir purview hesabı oluşturun.

- [Azure SQL yönetilen örneği 'nde ortak uç noktayı yapılandırma](../azure-sql/managed-instance/public-endpoint-configure.md)
    > [!Note]
    > **Özel uç nokta,** purview tarafından henüz desteklenmediğinden, kuruluşunuzun ortak uç noktaya izin alabilmesi gerekir. Özel uç nokta kullanırsanız, tarama başarılı olmayacaktır.

### <a name="setting-up-authentication-for-a-scan"></a>Tarama için kimlik doğrulamasını ayarlama

Azure SQL veritabanı yönetilen örneğini taramak için kimlik doğrulaması. Yeni kimlik doğrulaması oluşturmanız gerekiyorsa, [SQL veritabanı yönetilen örneği 'ne veritabanı erişimini yetkilendirmeniz](../azure-sql/database/logins-create-manage.md)gerekir. Takiview 'un günümüzde desteklediği üç kimlik doğrulama yöntemi vardır:

- SQL kimlik doğrulaması
- Hizmet Sorumlusu
- Yönetilen Kimlik

#### <a name="sql-authentication"></a>SQL kimlik doğrulaması

> [!Note]
> Yalnızca sunucu düzeyinde asıl oturum açma (sağlama işlemi tarafından oluşturulan) veya `loginmanager` ana veritabanındaki veritabanı rolünün üyeleri yeni oturumlar oluşturabilir. İzin verdikten yaklaşık **15 dakika** sürer, purview hesabının kaynakları tarayabilmesi için uygun izinlere sahip olması gerekir.

Bu kullanılabilir değilse Azure SQL veritabanı yönetilen örneği için oturum açma oluşturmak üzere [oturum oluşturma](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) ' daki yönergeleri izleyebilirsiniz. Sonraki adımlar için **Kullanıcı adı** ve **parolaya** ihtiyacınız olacak.

1. Azure portal, anahtar kasanıza gidin
1. **Gizli dizileri > ayarları** seçin
1. **+ Oluştur/Içeri aktar** ' ı seçin ve Azure SQL veritabanı yönetilen *örneğinden parola* olarak **ad** ve **değer** girin
1. Tamamlanacak **Oluştur** ' u seçin
1. Anahtar Kasanızda purview 'a bağlı değilse, [Yeni bir Anahtar Kasası bağlantısı oluşturmanız](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) gerekir
1. Son olarak, taramanızı ayarlamak için **Kullanıcı adını** ve **parolayı** kullanarak [Yeni bir kimlik bilgisi oluşturun](manage-credentials.md#create-a-new-credential)

#### <a name="service-principal-and-managed-identity"></a>Hizmet sorumlusu ve yönetilen kimlik

Azure SQL veritabanı yönetilen örneğinizi taramak için purview 'ın hizmet sorumlusu kullanmasına izin veren birkaç adım vardır

##### <a name="create-or-use-an-existing-service-principal"></a>Mevcut bir hizmet sorumlusu oluşturun veya kullanın

> [!Note]
> **Yönetilen kimlik** kullanıyorsanız bu adımı atlayın

Hizmet sorumlusu kullanmak için mevcut bir tane kullanabilir veya yeni bir tane oluşturabilirsiniz. 

> [!Note]
> Yeni bir hizmet sorumlusu oluşturmanız gerekiyorsa lütfen şu adımları izleyin:
> 1. [Azure Portal](https://portal.azure.com)gidin.
> 1. Sol taraftaki menüden **Azure Active Directory** ' yi seçin.
> 1. **Uygulama kayıtları**’nı seçin.
> 1. **+ Yeni uygulama kaydı**' nı seçin.
> 1. **Uygulama** için bir ad girin (hizmet asıl adı).
> 1. **Yalnızca bu kuruluş dizininde hesaplar '** ı seçin.
> 1. Yeniden yönlendirme URI 'SI için **Web** ' i seçin ve istediğiniz URL 'yi girin; gerçek veya iş olması gerekmez.
> 1. Ardından **Kaydet**’i seçin.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Veritabanı hesabında Azure AD kimlik doğrulamasını yapılandırma

Hizmet sorumlusu veya yönetilen kimliğin veritabanı, şemalar ve tablolar için meta verileri almak için izni olmalıdır. Ayrıca sınıflandırmanın örneklemesine yönelik tabloları sorgulayabilmelidir.
- [Azure SQL ile Azure AD kimlik doğrulamasını yapılandırma ve yönetme](../azure-sql/database/authentication-aad-configure.md)
- Azure [ad kimlikleriyle eşleştirilmiş kapsanan kullanıcılar oluşturma](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities) hakkında önkoşulları ve öğreticiyi IZLEYEREK Azure SQL veritabanı yönetilen örneği 'Nde BIR Azure AD kullanıcısı oluşturun
- `db_owner`Kimliğe atama (**Önerilen**) izni

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Ana kasaya hizmet sorumlusu ve takip görünümü kimlik bilgileri ekleyin

> [!Note]
> **Yönetilen kimlik** kullanmayı planlıyorsanız, bu adımı atlayabilirsiniz çünkü varsayılan purview kimliği **PURVIEW-MSI** ' de zaten var

Hizmet sorumlusunun uygulama KIMLIĞI ve gizli anahtarı almak için gereklidir:

1. [Azure Portal](https://portal.azure.com) hizmet sorumlusuna gidin
1. **Uygulama (istemci) kimliğinin** değerlerini, **Sertifikalar & Gizliliklerden** **genel bakış** ve **istemci gizliliğine** kopyalayın.
1. Anahtar kasanıza gidin
1. **Gizli dizileri > ayarları** seçin
1. **+ Oluştur/Içeri aktar** ' ı seçin ve seçtiğiniz **adı** ve **değeri** hizmet sorumlınızdan **istemci gizli anahtarı** olarak girin
1. Tamamlanacak **Oluştur** ' u seçin
1. Anahtar Kasanızda purview 'a bağlı değilse, [Yeni bir Anahtar Kasası bağlantısı oluşturmanız](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) gerekir
1. Son olarak, taramanızı ayarlamak için hizmet sorumlusunu kullanarak [Yeni bir kimlik bilgisi oluşturun](manage-credentials.md#create-a-new-credential)

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Azure SQL veritabanı yönetilen örnek veri kaynağını kaydetme

1. Purview hesabınıza gidin

1. Sol gezinti bölmesinde **kaynakları** seçin

1. **Kaydol** ' u seçin

1. **Azure SQL veritabanı yönetilen örneği** ' ni seçin ve sonra **devam et**

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="SQL veri kaynağını ayarlama":::

1. **Azure aboneliği ' nden** seçim yapın, **Azure aboneliği** açılan kutusundan ilgili aboneliği ve **sunucu adı** açılır kutusundan uygun sunucuyu seçin.

1. **Genel uç nokta tam etki alanı adını** ve **bağlantı noktası numarasını** belirtin. Ardından veri kaynağını kaydetmek için **son** ' u seçin.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Azure SQL veritabanı yönetilen örneği ekleme":::

    Örneğin `foobar.public.123.database.windows.net,3342`

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> Taramanızı silmek, varlıklarınızı önceki Azure SQL veritabanı yönetilen örnek taramalarından silmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure purview veri kataloğuna gözatın](how-to-browse-catalog.md)
- [Azure purview Veri Kataloğu 'Nda arama](how-to-search-catalog.md)