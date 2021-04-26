---
title: Azure SQL veritabanını kaydetme ve tarama
description: Bu öğreticide Azure SQL veritabanı 'nın nasıl taranması açıklanmaktadır
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 9873ec2113c0c5704ccca4fa10daf6ac8d572989
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107440"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Azure SQL veritabanını kaydetme ve tarama

Bu makalede bir Azure SQL veritabanı veri kaynağını purview 'a kaydetme ve üzerinde tarama ayarlama özetlenmektedir.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Azure SQL veritabanı veri kaynağı aşağıdaki işlevleri destekler:

- Azure SQL veritabanı 'nda meta verileri ve sınıflandırmayı yakalamak için **tam ve artımlı taramalar** .

- ADF Copy ve veri akışı etkinlikleri için veri varlıkları arasında **kökenini** .

### <a name="known-limitations"></a>Bilinen sınırlamalar

> * Azure purview, Azure SQL veritabanı 'nda [görünümlerin](/sql/relational-databases/views/views?view=azuresqldb-current&preserve-view=true) taranmasını desteklemez.
> * Azure purview, şema sekmesinde 300 sütundan fazlasını desteklemez ve "ek sütunlar-kesildi" olarak gösterilir. 

## <a name="prerequisites"></a>Önkoşullar

1. Henüz bir tane yoksa yeni bir purview hesabı oluşturun.

1. Purview hesabı ve Azure SQL veritabanı arasında ağ erişimi.


### <a name="set-up-authentication-for-a-scan"></a>Tarama için kimlik doğrulamasını ayarlama

Azure SQL veritabanı 'nı taramak için kimlik doğrulaması. Yeni kimlik doğrulaması oluşturmanız gerekiyorsa, [SQL veritabanı 'na veritabanı erişimini yetkilendirmeniz](../azure-sql/database/logins-create-manage.md)gerekir. Takiview 'un günümüzde desteklediği üç kimlik doğrulama yöntemi vardır:

- SQL kimlik doğrulaması
- Hizmet Sorumlusu
- Yönetilen Kimlik

#### <a name="sql-authentication"></a>SQL kimlik doğrulaması

> [!Note]
> Yalnızca sunucu düzeyinde asıl oturum açma (sağlama işlemi tarafından oluşturulan) veya `loginmanager` ana veritabanındaki veritabanı rolünün üyeleri yeni oturumlar oluşturabilir. İzin verdikten yaklaşık **15 dakika** sürer, purview hesabının kaynakları tarayabilmesi için uygun izinlere sahip olması gerekir.

Bu kullanılabilir değilse Azure SQL veritabanı için oturum açma oluşturmak üzere [oturum oluşturma](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) ' daki yönergeleri izleyebilirsiniz. Sonraki adımlar için **Kullanıcı adı** ve **parolaya** ihtiyacınız olacak.

1. Azure portal, anahtar kasanıza gidin
1. **Gizli dizileri > ayarları** seçin
1. **+ Oluştur/Içeri aktar** ' ı seçin ve Azure SQL veritabanından *parola* olarak **adı** ve **değeri** girin
1. Tamamlanacak **Oluştur** ' u seçin
1. Anahtar Kasanızda purview 'a bağlı değilse, [Yeni bir Anahtar Kasası bağlantısı oluşturmanız](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) gerekir
1. Son olarak, taramanızı ayarlamak için **Kullanıcı adını** ve **parolayı** kullanarak [Yeni bir kimlik bilgisi oluşturun](manage-credentials.md#create-a-new-credential)

#### <a name="service-principal-and-managed-identity"></a>Hizmet sorumlusu ve yönetilen kimlik

Azure SQL veritabanınızı taramak için purview 'ın hizmet sorumlusu 'nı veya purview 'ın **yönetilen kimliğini** kullanmasına izin veren birkaç adım vardır

   > [!Note]
   > Purview 'in taranması için **uygulama (istemci) kimliği** ve **istemci parolası** gerekir.

##### <a name="create-or-use-an-existing-service-principal"></a>Mevcut bir hizmet sorumlusu oluşturun veya kullanın

> [!Note]
> Purview 'un **yönetilen kimliğini** kullanıyorsanız bu adımı atlayın

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
- Yönetilen kimlik kullanıyorsanız, purview hesabınızın kendi yönetilen kimliği vardır ve bu, temel olarak sizin oluşturduğunuz zaman sizin oluşturduğunuz bir addır. Azure SQL veritabanı ['nda hizmet sorumlusu kullanıcısı oluşturma](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database)öğreticisini IZLEYEREK Azure SQL veritabanı 'Nda BIR Azure AD kullanıcısını tam olarak takip ettiğiniz yönetilen kimlik veya kendi hizmet sorumlusu ile oluşturmanız gerekir. Kimliğe doğru izinleri (örn. `db_owner` veya) atamanız gerekir `db_datareader` . Kullanıcı oluşturmak ve izin vermek için örnek SQL söz dizimi:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > `Username`Kendi hizmet sorumlusu veya takip görünümü tarafından yönetilen kimlik. [Sabit veritabanı rolleri ve özellikleri](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles)hakkında daha fazla bilgi edinebilirsiniz.
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Ana kasaya hizmet sorumlusu ve takip görünümü kimlik bilgileri ekleyin

> [!Note]
> Purview 'un **yönetilen kimliğini** kullanmayı planlıyorsanız, varsayılan purview 'ın yönetilen kimliği **PURVIEW-MSI** kimlik bilgilerinde zaten olduğundan bu adımı atlayabilirsiniz.

Hizmet sorumlusunun uygulama KIMLIĞI ve gizli anahtarı almak için gereklidir:

1. [Azure Portal](https://portal.azure.com) hizmet sorumlusuna gidin
1. **Uygulama (istemci) kimliğinin** değerlerini, **Sertifikalar & Gizliliklerden** **genel bakış** ve **istemci gizliliğine** kopyalayın.
1. Anahtar kasanıza gidin
1. **Gizli dizileri > ayarları** seçin
1. **+ Oluştur/Içeri aktar** ' ı seçin ve seçtiğiniz **adı** ve **değeri** hizmet sorumlınızdan **istemci gizli anahtarı** olarak girin
1. Tamamlanacak **Oluştur** ' u seçin
1. Anahtar Kasanızda purview 'a bağlı değilse, [Yeni bir Anahtar Kasası bağlantısı oluşturmanız](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) gerekir
1. Son olarak, taramanızı ayarlamak için hizmet sorumlusunu kullanarak [Yeni bir kimlik bilgisi oluşturun](manage-credentials.md#create-a-new-credential)

### <a name="firewall-settings"></a>Güvenlik duvarı ayarları

Veritabanı sunucunuz Azure bağlantılarının etkinleştirilmesini sağlamalıdır. Bu, Azure purview 'ın sunucuya ulaşmasını ve sunucuya bağlanmasını sağlar. [Azure 'ın Içinden bağlantılar](../azure-sql/database/firewall-configure.md#connections-from-inside-azure)Için nasıl yapılır kılavuzunu izleyebilirsiniz.

1. Veritabanı hesabınıza gidin
1. **Genel bakış** sayfasında sunucu adını seçin
1. **Güvenlik > güvenlik duvarları ve sanal ağlar 'ı** seçin
1. **Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin ver** için **Evet** ' i seçin

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine izin verin." border="true":::
    
> [!Note]
> Şu anda Azure purview, VNET yapılandırmasını desteklemez. Bu nedenle, IP tabanlı güvenlik duvarı ayarları yapılamaz.

## <a name="register-an-azure-sql-database-data-source"></a>Azure SQL veritabanı veri kaynağını kaydetme

Veri kataloğunuza yeni bir Azure SQL veritabanı kaydetmek için aşağıdakileri yapın:

1. Purview hesabınıza gidin

1. Sol gezinti bölmesinde **kaynakları** seçin

1. **Kaydol** ' u seçin

1. **Kayıt kaynakları** ÜZERINDE **Azure SQL veritabanı**' nı seçin. **Devam**’ı seçin.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="Yeni veri kaynağını Kaydet" border="true":::

**Kaynakları Kaydet (Azure SQL veritabanı)** ekranında, şunları yapın:

1. Veri kaynağının katalogda listelenecek bir **ad** girin.
1. **Azure aboneliği ' nden** seçim yapın, **Azure aboneliği** açılan kutusundan ilgili aboneliği ve **sunucu adı** açılır kutusundan uygun sunucuyu seçin.
1. Veri kaynağını kaydetmek için **son** ' a gidin.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="kaynakları kaydetme seçenekleri" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> * Taramanızı silmek, varlıklarınızı önceki Azure SQL veritabanı taramalarından silmez.
> * Kaynak tablonuz değiştirilirse varlık artık şema değişiklikleriyle güncelleştirilmeyecek ve takip görünümü şema sekmesinden açıklama düzenledikten sonra kaynak tabloyu yeniden taramayacaktır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure purview veri kataloğuna gözatın](how-to-browse-catalog.md)
- [Azure purview Veri Kataloğu 'Nda arama](how-to-search-catalog.md)
