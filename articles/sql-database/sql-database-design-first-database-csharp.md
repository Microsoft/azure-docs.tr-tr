---
title: İlk ilişkisel veritabanınızı tasarlamaC#
description: ADO.NET C# kullanarak Azure SQL veritabanı 'nda tek bir veritabanında ilk ilişkisel veritabanınızı tasarlamayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
ms.date: 07/29/2019
ms.openlocfilehash: 6c4a99367c7fc6a864f6a16bc614e4c10663d0ff
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837169"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Öğretici: Azure SQL veritabanı C&#x23; ve ADO.NET içinde tek bir veritabanında Ilişkisel veritabanı tasarlama

Azure SQL veritabanı, Microsoft Bulut (Azure) içindeki ilişkisel bir hizmet olarak veritabanı (DBaaS). Bu öğreticide, Visual Studio ile ADO.NET ve Azure portalını kullanarak şu işlemleri gerçekleştirmeyi öğreneceksiniz:

> [!div class="checklist"]
> * Azure portal * kullanarak tek bir veritabanı oluşturun
> * Azure portal kullanarak sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlama
> * ADO.NET ve Visual Studio ile veritabanına bağlanma
> * ADO.NET ile tablo oluşturma
> * ADO.NET ile veri ekleme, güncelleştirme ve silme
> * ADO.NET ile veri sorgulama

\* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .

> [!TIP]
> Aşağıdaki Microsoft Learn modülü, basit bir veritabanının oluşturulması dahil olmak üzere [Azure SQL veritabanını sorgulayan bir ASP.NET uygulamasının nasıl geliştirileceği ve yapılandırılacağı](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)hakkında bilgi edinmenize yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar

[Visual Studio 2019](https://www.visualstudio.com/downloads/) veya sonraki bir sürümünü yükleme.

## <a name="create-a-blank-single-database"></a>Boş bir tek veritabanı oluşturma

Azure SQL veritabanı 'nda tek bir veritabanı, tanımlı bir dizi işlem ve depolama kaynağı ile oluşturulur. Veritabanı bir [Azure Kaynak grubu](../azure-resource-manager/resource-group-overview.md) içinde oluşturulur ve [veritabanı sunucusu](sql-database-servers.md)kullanılarak yönetilir.

Boş tek veritabanı oluşturmak için bu adımları izleyin.

1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesine tıklayın.
2. **Yeni** sayfasında, Azure Market bölümünde **Veritabanları**’nı seçin ve ardından **Öne Çıkan** bölümünde **SQL Veritabanı**’na tıklayın.

   ![create empty-database](./media/sql-database-design-first-database/create-empty-database.png)

3. **SQL veritabanı** formunu, önceki görüntüde gösterildiği gibi aşağıdaki bilgilerle doldurun:

    | Ayar       | Önerilen değer | Açıklama |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Veritabanı adı** | *yourDatabase* | Geçerli veritabanı adları için bkz. [veritabanı tanımlayıcıları](/sql/relational-databases/databases/database-identifiers). |
    | **Abonelik** | *Aboneliğiniz*  | Abonelikleriniz hakkında daha ayrıntılı bilgi için bkz. [Abonelikler](https://account.windowsazure.com/Subscriptions). |
    | **Kaynak grubu** | *yourResourceGroup* | Geçerli kaynak grubu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). |
    | **Kaynak seçme** | Boş veritabanı | Boş bir veritabanı oluşturulması gerektiğini belirtir. |

4. Mevcut bir veritabanı sunucusunu kullanmak için **sunucu** ' ya tıklayın veya yeni bir veritabanı sunucusu oluşturun ve yapılandırın. Mevcut bir sunucuyu seçin ya da **Yeni sunucu oluştur ' a** tıklayın ve **Yeni sunucu** formunu aşağıdaki bilgilerle doldurun:

    | Ayar       | Önerilen değer | Açıklama |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Sunucu adı** | Genel olarak benzersiz bir ad | Geçerli sunucu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). |
    | **Sunucu yöneticisi oturum açma bilgileri** | Geçerli bir ad | Geçerli oturum açma adları için bkz. [veritabanı tanımlayıcıları](/sql/relational-databases/databases/database-identifiers). |
    | **Parola** | Geçerli bir parola | Parolanız en az sekiz karakter uzunluğunda olmalı ve şu kategorilerden üçünden karakter kullanmalıdır: büyük harf karakterler, küçük harf karakterler, sayılar ve alfasayısal olmayan karakterler. |
    | **Konum** | Geçerli bir konum | Bölgeler hakkında bilgi için bkz. [Azure Bölgeleri](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. **Seç**'e tıklayın.
6. Hizmet katmanını, DTU veya sanal çekirdek sayısını ve depolama alanı miktarını belirtmek için **Fiyatlandırma katmanı**’na tıklayın. Her hizmet katmanı için kullanılabilir DTU 'lar/sanal çekirdek sayısı ve depolama seçeneklerini keşfedebilirsiniz.

    Hizmet katmanını, DTU 'Lar veya sanal çekirdekler sayısını ve depolama alanı miktarını seçtikten sonra **Uygula**' ya tıklayın.

7. Boş veritabanı için bir **harmanlama** girin (Bu öğretici için varsayılan değeri kullanın). Harmanlamalar hakkında daha fazla bilgi için bkz. [Harmanlamalar](/sql/t-sql/statements/collations)

8. **SQL veritabanı** formunu tamamladığınıza göre, tek veritabanını sağlamak için **Oluştur** ' a tıklayın. Bu adım birkaç dakika sürebilir.

9. Araç çubuğunda **Bildirimler**’e tıklayarak dağıtım işlemini izleyin.

   ![bildirim](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Sunucu düzeyinde IP güvenlik duvarı kuralı oluşturma

SQL veritabanı hizmeti, sunucu düzeyinde bir IP güvenlik duvarı oluşturur. Bu güvenlik duvarı, bir güvenlik duvarı kuralı tarafından IP 'nin güvenlik duvarından geçmesine izin verilmediği takdirde, dış uygulamaların ve araçların sunucuya ve sunucu üzerindeki veritabanlarına bağlanmasını engeller. Tek veritabanınızın dış bağlantısını etkinleştirmek için, önce IP adresiniz (veya IP adres aralığı) için bir IP güvenlik duvarı kuralı eklemeniz gerekir. [SQL veritabanı sunucu düzeyinde BIR IP güvenlik duvarı kuralı](sql-database-firewall-configure.md)oluşturmak için bu adımları izleyin.

> [!IMPORTANT]
> SQL veritabanı hizmeti 1433 bağlantı noktası üzerinden iletişim kurar. Bu hizmete bir kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, yöneticiniz 1433 numaralı bağlantı noktasını açmadığı takdirde tek veritabanınıza bağlanamazsınız.

1. Dağıtım tamamlandıktan sonra, sol taraftaki menüden **SQL veritabanları** ' na ve ardından **SQL veritabanları** sayfasında *YourDatabase* ' e tıklayın. Veritabanınızın genel bakış sayfası açılır ve tam **sunucu adı** (örneğin, *yourserver.Database.Windows.net*) görüntülenerek daha fazla yapılandırma seçeneği sunulur.

2. Bu tam sunucu adını, SQL Server Management Studio sunucunuza ve veritabanlarına bağlanmak için kullanmak üzere kopyalayın.

   ![sunucu adı](./media/sql-database-design-first-database/server-name.png)

3. Araç çubuğunda **Sunucu güvenlik duvarını ayarla**’ya tıklayın. SQL Veritabanı sunucusu için **Güvenlik duvarı ayarları** sayfası açılır.

   ![Sunucu düzeyi IP güvenlik duvarı kuralı](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Geçerli IP adresinizi yeni bir IP güvenlik duvarı kuralına eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' ye tıklayın. Bir IP güvenlik duvarı kuralı, tek bir IP adresi veya IP adresi aralığı için 1433 bağlantı noktasını açabilir.

5. **Kaydet** düğmesine tıklayın. SQL veritabanı sunucusunda 1433 numaralı bağlantı noktasını açan geçerli IP adresiniz için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur.

6. **Tamam**’a tıklayın ve sonra **Güvenlik duvarı ayarları** sayfasını kapatın.

IP adresiniz artık IP güvenlik duvarından geçebilirler. Artık SQL Server Management Studio veya seçtiğiniz başka bir aracı kullanarak tek veritabanınıza bağlanabilirsiniz. Daha önce oluşturduğunuz sunucu yöneticisi hesabını kullandığınızdan emin olun.

> [!IMPORTANT]
> Varsayılan olarak, SQL veritabanı IP güvenlik duvarı üzerinden erişim tüm Azure hizmetleri için etkinleştirilir. Tüm Azure hizmetleri için devre dışı bırakmak isterseniz bu sayfadaki **KAPALI** öğesine tıklayın.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, veritabanı ve tablo oluşturma, veritabanına bağlanma, verileri yükleme ve sorguları çalıştırma gibi temel veritabanı görevlerini öğrendiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Veritabanı oluşturma
> * Güvenlik duvarı kuralı ayarlama
> * [Visual Studio ve C#](sql-database-connect-query-dotnet-visual-studio.md) ile veritabanına bağlanma
> * Tablo oluşturma
> * Veri ekleme, güncelleştirme, silme ve sorgulama

Veri geçişi hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [DMS kullanarak SQL Server'ı çevrimdışı Azure SQL Veritabanına geçirme](../dms/tutorial-sql-server-to-azure-sql.md)
