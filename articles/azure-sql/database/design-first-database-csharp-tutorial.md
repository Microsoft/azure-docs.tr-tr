---
title: 'İlk ilişkisel veritabanınızı tasarlama C #'
description: ADO.NET kullanarak C# ile Azure SQL veritabanı 'nda ilk ilişkisel veritabanınızı tasarlamayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-csharp
ms.topic: tutorial
author: rothja
ms.author: jroth
ms.reviewer: sstein
ms.date: 07/29/2019
ms.openlocfilehash: 67d20a4f0f5d2dae769429c9c476899ea8099c43
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551702"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-cx23-and-adonet"></a>Öğretici: Azure SQL veritabanı C&#x23; ve ADO.NET 'de ilişkisel veritabanı tasarlama
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL veritabanı, Microsoft Bulut (Azure) içindeki ilişkisel bir hizmet olarak veritabanı (DBaaS). Bu öğreticide, Visual Studio ile ADO.NET ve Azure portalını kullanarak şu işlemleri gerçekleştirmeyi öğreneceksiniz:

> [!div class="checklist"]
>
> * Azure portal kullanarak veritabanı oluşturma
> * Azure portal kullanarak sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlama
> * ADO.NET ve Visual Studio ile veritabanına bağlanma
> * ADO.NET ile tablo oluşturma
> * ADO.NET ile veri ekleme, güncelleştirme ve silme
> * ADO.NET ile veri sorgulama

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .

> [!TIP]
> Aşağıdaki Microsoft Learn modülü, basit bir veritabanının oluşturulması dahil olmak üzere [Azure SQL veritabanını sorgulayan bir ASP.NET uygulamasının nasıl geliştirileceği ve yapılandırılacağı](/learn/modules/develop-app-that-queries-azure-sql/)hakkında bilgi edinmenize yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar

[Visual Studio 2019](https://www.visualstudio.com/downloads/) veya sonraki bir sürümünü yükleme.

## <a name="create-a-blank-database-in-azure-sql-database"></a>Azure SQL veritabanı 'nda boş bir veritabanı oluşturma

Azure SQL veritabanı 'ndaki bir veritabanı, tanımlı bir dizi işlem ve depolama kaynağı ile oluşturulur. Veritabanı bir [Azure Kaynak grubu](../../active-directory-b2c/overview.md) içinde oluşturulur ve [mantıksal bir SQL Server](logical-servers.md)kullanılarak yönetilir.

Boş bir veritabanı oluşturmak için bu adımları izleyin.

1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesine tıklayın.
2. **Yeni** sayfasında, Azure Market bölümünde **Veritabanları**’nı seçin ve ardından **Öne Çıkan** bölümünde **SQL Veritabanı**’na tıklayın.

   ![create empty-database](./media/design-first-database-csharp-tutorial/create-empty-database.png)

3. **SQL veritabanı** formunu, önceki görüntüde gösterildiği gibi aşağıdaki bilgilerle doldurun:

    | Ayar       | Önerilen değer | Açıklama |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Veritabanı adı** | *yourDatabase* | Geçerli veritabanı adları için bkz. [veritabanı tanımlayıcıları](/sql/relational-databases/databases/database-identifiers). |
    | **Abonelik** | *Aboneliğiniz*  | Abonelikleriniz hakkında daha ayrıntılı bilgi için bkz. [Abonelikler](https://account.windowsazure.com/Subscriptions). |
    | **Kaynak grubu** | *yourResourceGroup* | Geçerli kaynak grubu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). |
    | **Kaynak seçme** | Boş veritabanı | Boş bir veritabanı oluşturulması gerektiğini belirtir. |

4. Var olan bir sunucuyu kullanmak için **sunucu** ' ya tıklayın veya yeni bir sunucu oluşturun ve yapılandırın. Mevcut bir sunucuyu seçin ya da **Yeni sunucu oluştur ' a** tıklayın ve **Yeni sunucu** formunu aşağıdaki bilgilerle doldurun:

    | Ayar       | Önerilen değer | Açıklama |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Sunucu adı** | Genel olarak benzersiz bir ad | Geçerli sunucu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). |
    | **Sunucu yöneticisi oturum açma bilgileri** | Geçerli bir ad | Geçerli oturum açma adları için bkz. [veritabanı tanımlayıcıları](/sql/relational-databases/databases/database-identifiers). |
    | **Parola** | Geçerli bir parola | Parolanız en az sekiz karakter uzunluğunda olmalı ve şu kategorilerden üçünden karakter kullanmalıdır: büyük harf karakterler, küçük harf karakterler, sayılar ve alfasayısal olmayan karakterler. |
    | **Konum** | Geçerli bir konum | Bölgeler hakkında bilgi için bkz. [Azure Bölgeleri](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/design-first-database-csharp-tutorial/create-database-server.png)

5. **Seç**’e tıklayın.
6. Hizmet katmanını, DTU veya sanal çekirdek sayısını ve depolama alanı miktarını belirtmek için **Fiyatlandırma katmanı**’na tıklayın. Her hizmet katmanı için kullanılabilir DTU 'lar/sanal çekirdek sayısı ve depolama seçeneklerini keşfedebilirsiniz.

    Hizmet katmanını, DTU 'Lar veya sanal çekirdekler sayısını ve depolama alanı miktarını seçtikten sonra **Uygula**' ya tıklayın.

7. Boş veritabanı için bir **harmanlama** girin (Bu öğretici için varsayılan değeri kullanın). Harmanlamalar hakkında daha fazla bilgi için bkz. [Harmanlamalar](/sql/t-sql/statements/collations)

8. **SQL veritabanı** formunu tamamladığınıza göre, veritabanını sağlamak için **Oluştur** ' a tıklayın. Bu adım birkaç dakika sürebilir.

9. Araç çubuğunda **Bildirimler**’e tıklayarak dağıtım işlemini izleyin.

   ![Ekran görüntüsü, Azure portal dağıtım sürmekte olan bildirimleri gösterir.](./media/design-first-database-csharp-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Sunucu düzeyinde IP güvenlik duvarı kuralı oluşturma

SQL veritabanı, sunucu düzeyinde bir IP güvenlik duvarı oluşturur. Bu güvenlik duvarı, bir güvenlik duvarı kuralı tarafından IP 'nin güvenlik duvarından geçmesine izin verilmediği takdirde, dış uygulamaların ve araçların sunucuya ve sunucu üzerindeki veritabanlarına bağlanmasını engeller. Veritabanınıza dış bağlantıyı etkinleştirmek için, önce IP adresiniz (veya IP adres aralığı) için bir IP güvenlik duvarı kuralı eklemeniz gerekir. [Sunucu düzeyinde BIR IP güvenlik duvarı kuralı](firewall-configure.md)oluşturmak için bu adımları izleyin.

> [!IMPORTANT]
> SQL Veritabanı 1433 numaralı bağlantı noktası üzerinden iletişim kurar. Bu hizmete bir kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, yöneticiniz 1433 numaralı bağlantı noktasını açmadığı takdirde veritabanınıza bağlanamazsınız.

1. Dağıtım tamamlandıktan sonra, sol taraftaki menüden **SQL veritabanları** ' na ve ardından **SQL veritabanları** sayfasında *YourDatabase* ' e tıklayın. Veritabanınızın genel bakış sayfası açılır ve tam **sunucu adı** (örneğin, *yourserver.Database.Windows.net*) görüntülenerek daha fazla yapılandırma seçeneği sunulur.

2. Bu tam sunucu adını, SQL Server Management Studio sunucunuza ve veritabanlarına bağlanmak için kullanmak üzere kopyalayın.

   ![sunucu adı](./media/design-first-database-csharp-tutorial/server-name.png)

3. Araç çubuğunda **Sunucu güvenlik duvarını ayarla**’ya tıklayın. Sunucu için **güvenlik duvarı ayarları** sayfası açılır.

   ![Sunucu düzeyi IP güvenlik duvarı kuralı](./media/design-first-database-csharp-tutorial/server-firewall-rule.png)

4. Geçerli IP adresinizi yeni bir IP güvenlik duvarı kuralına eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' ye tıklayın. Bir IP güvenlik duvarı kuralı, tek bir IP adresi veya IP adresi aralığı için 1433 bağlantı noktasını açabilir.

5. **Kaydet**’e tıklayın. Sunucuda 1433 numaralı bağlantı noktasını açan geçerli IP adresiniz için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur.

6. **Tamam**’a tıklayın ve sonra **Güvenlik duvarı ayarları** sayfasını kapatın.

IP adresiniz artık IP güvenlik duvarından geçebilirler. Artık SQL Server Management Studio veya seçtiğiniz başka bir aracı kullanarak veritabanınıza bağlanabilirsiniz. Daha önce oluşturduğunuz sunucu yöneticisi hesabını kullandığınızdan emin olun.

> [!IMPORTANT]
> Varsayılan olarak, SQL veritabanı IP güvenlik duvarı üzerinden erişim tüm Azure hizmetleri için etkinleştirilir. Tüm Azure hizmetlerine erişimi devre dışı bırakmak için bu sayfadaki **kapalı** ' yı tıklatın.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, veritabanı ve tablo oluşturma, veritabanına bağlanma, verileri yükleme ve sorguları çalıştırma gibi temel veritabanı görevlerini öğrendiniz. Şunları öğrendiniz:

> [!div class="checklist"]
>
> * Azure portal kullanarak veritabanı oluşturma
> * Azure portal kullanarak sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlama
> * ADO.NET ve Visual Studio ile veritabanına bağlanma
> * ADO.NET ile tablo oluşturma
> * ADO.NET ile veri ekleme, güncelleştirme ve silme
> * ADO.NET ile veri sorgulama

Veri geçişi hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [DMS kullanarak SQL Server'ı çevrimdışı Azure SQL Veritabanına geçirme](../../dms/tutorial-sql-server-to-azure-sql.md)