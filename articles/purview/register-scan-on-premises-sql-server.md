---
title: Şirket içi SQL Server 'ı kaydetme ve tarama
description: Bu öğreticide, şirket içinde barındırılan bir IR kullanarak şirket içi SQL Server 'ı nasıl tarayabileceğiniz açıklanmaktadır.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: b5f4218cfcd5f9ccfbe43efac46e2f70fdc30905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99574966"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>Şirket içi SQL Server 'ı kaydetme ve tarama

Bu makalede bir SQL Server veri kaynağını purview 'a kaydetme ve üzerinde tarama ayarlama özetlenmektedir.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

SQL Server şirket içi veri kaynağı aşağıdaki işlevleri destekler:

- Azure VM 'de yüklü bir şirket içi ağda veya bir SQL Server 'da meta verileri ve sınıflandırmayı yakalamak için **tam ve artımlı taramalar** .

- ADF kopyası/veri akışı etkinlikleri için veri varlıkları arasında **kökenini**

SQL Server şirket içi veri kaynağı şunları destekler:

- SQL Server 2019 ' den SQL Server 2000 ' ye geri SQL Server 'ın her sürümü

- Kimlik doğrulama yöntemi: SQL kimlik doğrulaması

### <a name="known-limitations"></a>Bilinen sınırlamalar

Azure purview, SQL Server [görünümlerinin](/sql/relational-databases/views/views) taranmasını desteklemez.

## <a name="prerequisites"></a>Önkoşullar

- Veri kaynaklarını kaydetmeden önce bir Azure purview hesabı oluşturun. Bir purview hesabı oluşturma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure purview hesabı oluşturma](create-catalog-portal.md).

- Veri kaynağını taramak için [Şirket içinde barındırılan bir tümleştirme çalışma zamanı](manage-integration-runtimes.md) ayarlayın.

## <a name="setting-up-authentication-for-a-scan"></a>Tarama için kimlik doğrulamasını ayarlama

Şirket içi SQL Server için kimlik doğrulaması kurmak için yalnızca bir yol vardır:

- SQL Kimlik Doğrulaması

### <a name="sql-authentication"></a>SQL kimlik doğrulaması

SQL hesabının **ana** veritabanına erişimi olmalıdır. Bunun nedeni `sys.databases` ana veritabanında olur. `sys.databases`Sunucu üzerindeki tüm SQL veritabanlarını bulmak Için purview tarayıcısının numaralandırması gerekir.

#### <a name="using-an-existing-server-administrator"></a>Var olan bir sunucu yöneticisini kullanma

Şirket içi SQL Server 'ı taramak için mevcut bir sunucu yöneticisi (SA) kullanıcısını kullanmayı planlıyorsanız, aşağıdakilerden emin olun:

1. `sa` bir Windows kimlik doğrulama hesabı değil.

2. Kullanmayı planladığınız sunucu düzeyinde oturum açmanın, ortak ve sysadmin sunucu rollerine sahip olması gerekir. Bunu sunucusuna bağlanarak, SQL Server Management Studio (SSMS) konumuna giderek, güvenlik ' e giderek, kullanmayı planladığınız oturum açma ' yı seçerek, **Özellikler** ' i sağ tıklayıp **sunucu rolleri**' ni seçerek doğrulayabilirsiniz.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Sunucu düzeyinde oturum açma.":::

#### <a name="creating-a-new-login-and-user"></a>Yeni bir oturum açma ve Kullanıcı oluşturma

SQL Server 'ı tarayabilmesi için yeni bir oturum açma ve Kullanıcı oluşturmak istiyorsanız aşağıdaki adımları izleyin:

> [!Note]
   > Aşağıdaki tüm adımlar [burada](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql) belirtilen kod kullanılarak yürütülebilir

1. SQL Server Management Studio (SSMS) gidin, sunucuya bağlanın, güvenlik ' e gidin, oturum aç ' a sağ tıklayın ve yeni oturum oluştur ' a tıklayın. SQL kimlik doğrulaması ' nı seçtiğinizden emin olun.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Yeni oturum açma ve kullanıcı oluşturun.":::

2. Sol gezinti bölmesinde sunucu rolleri ' ni seçin ve genel rolün atandığından emin olun.

3. Sol gezinti çubuğunda kullanıcı eşlemesi ' ni seçin, haritadaki tüm veritabanları ' nı seçin ve veritabanı rolünü seçin: **db_datareader**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="Kullanıcı eşleme.":::

4. Kaydetmek için Tamam ' ı tıklatın.

5. Oluşturduğunuz kullanıcıya, sağ tıklayıp **Özellikler**' i seçerek tekrar gidin. Yeni bir parola girin ve onaylayın. ' Eski parolayı belirtin ' öğesini seçin ve eski parolayı girin. **Yeni bir oturum açma işlemi oluşturduğunuz anda parolanızı değiştirmeniz gerekir.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="parolayı değiştirin.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>SQL oturum açma parolanızı anahtar kasasında depolama ve purview 'da kimlik bilgisi oluşturma

1. Azure portal1 'de anahtar kasanıza gidin. **Gizli dizileri > ayarları** seçin
1. **+ Oluştur/Içeri aktar** ' ı SEÇIN ve SQL Server oturum açınızdan *parola* olarak **ad** ve **değer** girin
1. Tamamlanacak **Oluştur** ' u seçin
1. Anahtar Kasanızda purview 'a bağlı değilse, [Yeni bir Anahtar Kasası bağlantısı oluşturmanız](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) gerekir
1. Son olarak, taramanızı ayarlamak için **Kullanıcı adını** ve **parolayı** kullanarak [Yeni bir kimlik bilgisi oluşturun](manage-credentials.md#create-a-new-credential)

## <a name="register-a-sql-server-data-source"></a>SQL Server veri kaynağını kaydetme

1. Purview hesabınıza gidin

1. Sol gezinti bölmesinde kaynaklar ve tarama ' nın altında **tümleştirme çalışma zamanları**' nı seçin. Şirket içinde barındırılan bir tümleştirme çalışma zamanının ayarlandığından emin olun. Ayarlanmamışsa, şirket içi ağınıza erişimi olan şirket içi veya Azure VM 'de tarama için şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturmak üzere [burada](manage-integration-runtimes.md) bahsedilen adımları izleyin.

1. Sol gezinti bölmesinde **kaynakları** seçin

1. **Kaydol** ' u seçin

1. **SQL Server** 'ı seçin ve sonra **devam edin**

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="SQL veri kaynağını ayarlayın.":::

5. Kolay bir ad ve sunucu uç noktası sağlayın ve sonra veri kaynağını kaydetmek için **son** ' u seçin. Örneğin, SQL Server FQDN 'SI **foobar.Database.Windows.net** ise, sunucu uç noktası olarak *foobar* girin.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure purview veri kataloğuna gözatın](how-to-browse-catalog.md)
- [Azure purview Veri Kataloğu 'Nda arama](how-to-search-catalog.md)
