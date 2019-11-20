---
title: Sunucu düzeyinde bir güvenlik duvarı kuralı oluşturma
description: Tek ve havuza alınmış veritabanları için SQL veritabanı sunucu düzeyinde güvenlik duvarı kuralı oluşturma
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
ms.date: 02/11/2019
ms.openlocfilehash: ff2508952b75bad88ff8ff92388c20ba52f50f42
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818251"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak tek ve havuza alınmış veritabanları için sunucu düzeyinde bir güvenlik duvarı kuralı oluşturma

Bu hızlı başlangıçta, veritabanı sunucularına, tek veritabanlarına ve elastik havuzlara ve veritabanlarına bağlanmanızı sağlamak üzere Azure portal kullanarak Azure SQL veritabanı 'nda tek ve havuza alınmış veritabanları için [sunucu düzeyinde güvenlik duvarı kuralı](sql-database-firewall-configure.md) oluşturma işlemi gösterilmektedir. Diğer Azure kaynaklarından ve şirket içi kaynaklardan bağlanmak için bir güvenlik duvarı kuralı gereklidir.

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıçta, başlangıç noktası olarak [Azure Portal kullanarak tek bir veritabanı oluşturma](sql-database-single-database-get-started.md) bölümünde oluşturulan kaynaklar kullanılmaktadır.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-server-level-ip-firewall-rule"></a>Sunucu düzeyinde IP güvenlik duvarı kuralı oluşturma

SQL veritabanı hizmeti, tek ve havuza alınmış veritabanları için veritabanı sunucu düzeyinde bir güvenlik duvarı oluşturur. Bu güvenlik duvarı, güvenlik duvarını açmak için bir IP güvenlik duvarı kuralı oluşturmadığınız müddetçe istemci uygulamalarının sunucuya veya tek veya havuza alınmış veritabanlarından herhangi birine bağlanmasını engeller. Azure dışındaki bir IP adresinden bağlantı için, bağlanabilmek istediğiniz belirli bir IP adresi veya adres aralığı için bir güvenlik duvarı kuralı oluşturun. Sunucu düzeyinde ve veritabanı düzeyinde IP güvenlik duvarı kuralları hakkında daha fazla bilgi için bkz. [SQL veritabanı sunucu düzeyi ve veritabanı DÜZEYINDE IP güvenlik duvarı kuralları](sql-database-firewall-configure.md).

> [!NOTE]
> SQL Veritabanı 1433 numaralı bağlantı noktası üzerinden iletişim kurar. Bir şirket ağından bağlanmaya çalışıyorsanız ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, BT departmanınız 1433 numaralı bağlantı noktasını açmadığı takdirde Azure SQL veritabanı sunucunuza bağlanamazsınız.
> [!IMPORTANT]
> 0\.0.0.0 güvenlik duvarı kuralı, tüm Azure hizmetlerinin sunucu düzeyinde güvenlik duvarı kuralı üzerinden geçmesini ve sunucu üzerinden tek veya havuza alınmış bir veritabanına bağlanmayı denemesini sağlar. 

İstemci IP adresi için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturmak ve yalnızca IP adresiniz için SQL veritabanı güvenlik duvarı üzerinden dış bağlantıyı etkinleştirmek için bu adımları izleyin.

1. [Önkoşul Azure SQL veritabanı](#prerequisites) dağıtımı tamamlandıktan sonra, sol taraftaki menüden **SQL veritabanları** ' nı seçin ve ardından **SQL veritabanları** sayfasında **mysampledatabase** ' i seçin. Veritabanınıza ilişkin genel bakış sayfası açılır ve tam sunucu adı (örneğin, **mynewserver-20170824.database.windows.net**) görüntülenerek daha fazla yapılandırma seçeneği sunulur.

2. Sunucunuza ve veritabanlarına bağlanırken kullanmak için bu tam sunucu adını diğer hızlı başlangıçlara kopyalayın.

   ![sunucu adı](./media/sql-database-get-started-portal/server-name.png)

3. Araç çubuğunda **sunucu güvenlik duvarını ayarla** ' yı seçin. Veritabanı sunucusunun **güvenlik duvarı ayarları** sayfası açılır.

   ![Sunucu düzeyi IP güvenlik duvarı kuralı](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Geçerli IP adresinizi yeni bir sunucu düzeyi IP güvenlik duvarı kuralına eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' yi seçin. Sunucu düzeyinde bir IP güvenlik duvarı kuralı, tek bir IP adresi veya IP adresi aralığı için 1433 bağlantı noktasını açabilir.

   > [!IMPORTANT]
   > Varsayılan olarak, SQL veritabanı güvenlik duvarı üzerinden erişim tüm Azure hizmetleri için devre dışıdır. Tüm Azure hizmetleri için erişimi etkinleştirmek istiyorsanız bu sayfada **Açık '** ı seçin.
   >

5. **Kaydet**’i seçin. SQL veritabanı sunucusunda 1433 numaralı bağlantı noktasını açan geçerli IP adresiniz için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur.

6. **Güvenlik duvarı ayarları** sayfasını kapatın.

SQL Server Management Studio veya seçtiğiniz başka bir aracı kullanarak, daha önce oluşturduğunuz sunucu yönetici hesabını kullanarak bu IP adresinden SQL veritabanı sunucusuna ve veritabanlarına bağlanabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[Sonraki adımlar](#next-steps)’a geçip farklı yöntemler kullanarak veritabanını sorgulama hakkında bilgi edinmek istiyorsanız bu kaynakları kaydedin. Ancak, bu hızlı başlangıçta oluşturduğunuz kaynakları silmek isterseniz, aşağıdaki adımları kullanın.

1. Azure portal sol taraftaki menüden **kaynak grupları** ' nı seçin ve ardından **myresourcegroup**' ı seçin.
2. Kaynak grubu sayfanızda **Sil**' i seçin, metin kutusuna **myresourcegroup** yazın ve ardından **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Artık bir veritabanınız olduğuna göre, şunlar dahil tercih ettiğiniz araçlar ya da diller ile veritabanında [bağlanma ve sorgulama](sql-database-connect-query.md) işlemleri yapabilirsiniz:
  - [SQL Server Management Studio kullanarak bağlanma ve sorgulama](sql-database-connect-query-ssms.md)
  - [Azure Data Studio kullanarak bağlanma ve sorgulama](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- İlk veritabanınızı tasarlamayı, tablolar oluşturmayı ve veri eklemeyi öğrenmek için, şu öğreticilerden birine bakın:
  - [SSMS kullanarak Azure SQL veritabanı 'nda ilk tek veritabanınızı tasarlama](sql-database-design-first-database.md)
  - [Azure SQL veritabanı 'nda tek bir veritabanı tasarlama ve ve ADO.NET C# ile bağlanma](sql-database-design-first-database-csharp.md)
