---
title: Sunucu düzeyinde bir güvenlik duvarı kuralı oluşturma
description: Sunucu düzeyinde güvenlik duvarı kuralı oluşturma
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, sstein
ms.date: 02/11/2019
ms.openlocfilehash: bef1e9bdafd91a51290fe84af074c8cece7cc371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102486735"
---
# <a name="quickstart-create-a-server-level-firewall-rule-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak sunucu düzeyinde bir güvenlik duvarı kuralı oluşturma
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu hızlı başlangıçta, [MANTıKSAL SQL Server 'lar](logical-servers.md), tek veritabanları ve elastik havuzlara ve veritabanlarına bağlanmanızı sağlamak üzere Azure Portal kullanarak Azure SQL veritabanı 'nda [sunucu düzeyinde güvenlik duvarı kuralı](firewall-configure.md) oluşturma işlemi gösterilmektedir. Diğer Azure kaynaklarından ve şirket içi kaynaklardan bağlanmak için bir güvenlik duvarı kuralı gereklidir. Sunucu düzeyinde güvenlik duvarı kuralları, Azure SQL yönetilen örneği için geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıçta, başlangıç noktası olarak [Azure Portal kullanarak tek bir veritabanı oluşturma](single-database-create-quickstart.md) bölümünde oluşturulan kaynaklar kullanılmaktadır.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-server-level-ip-firewall-rule"></a>Sunucu düzeyinde IP güvenlik duvarı kuralı oluşturma

 SQL veritabanı, tek ve havuza alınmış veritabanları için sunucu düzeyinde bir güvenlik duvarı oluşturur. Bu güvenlik duvarı, güvenlik duvarını açmak için bir IP güvenlik duvarı kuralı oluşturmadığınız müddetçe istemci uygulamalarının sunucuya veya veritabanlarının veritabanlarına bağlanmasını engeller. Azure dışındaki bir IP adresinden bağlantı için, belirli bir IP adresi için bir güvenlik duvarı kuralı veya bağlanabilebilmek istediğiniz adres aralığı için bir güvenlik duvarı kuralı oluşturun. Sunucu düzeyinde ve veritabanı düzeyinde IP güvenlik duvarı kuralları hakkında daha fazla bilgi için bkz. [sunucu düzeyi ve veritabanı DÜZEYINDE IP güvenlik duvarı kuralları](firewall-configure.md).

> [!NOTE]
> Azure SQL Veritabanı, 1433 numaralı bağlantı noktası üzerinden iletişim kurar. Bir şirket ağından bağlanmaya çalışıyorsanız ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, BT departmanınız 1433 numaralı bağlantı noktasını açmadığı takdirde sunucunuza bağlanamazsınız.
> [!IMPORTANT]
> 0.0.0.0 güvenlik duvarı kuralı, tüm Azure hizmetlerinin sunucu düzeyinde güvenlik duvarı kuralı üzerinden geçmesini ve sunucu üzerinden bir veritabanına bağlanmayı denemesini sağlar.

İstemci IP adresi için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturmak ve yalnızca IP adresiniz için Azure SQL veritabanı güvenlik duvarı üzerinden dış bağlantıyı etkinleştirmek için bu adımları izleyin.

1. [Veritabanı](#prerequisites) dağıtımı tamamlandıktan sonra, sol taraftaki menüden **SQL veritabanları** ' nı seçin ve ardından **SQL veritabanları** sayfasında **mysampledatabase** ' i seçin. Veritabanınızın genel bakış sayfası açılır ve tam sunucu adı (örneğin, **MyNewServer-20170824.Database.Windows.net**) görüntülenerek daha fazla yapılandırma seçeneği sunulur.

2. Sunucunuza ve veritabanlarına bağlanırken kullanmak için bu tam sunucu adını diğer hızlı başlangıçlara kopyalayın.

   ![sunucu adı](./media/firewall-create-server-level-portal-quickstart/server-name.png)

3. Araç çubuğunda **sunucu güvenlik duvarını ayarla** ' yı seçin. Sunucu için **güvenlik duvarı ayarları** sayfası açılır.

   ![Sunucu düzeyi IP güvenlik duvarı kuralı](./media/firewall-create-server-level-portal-quickstart/server-firewall-rule.png)

4. Geçerli IP adresinizi yeni bir sunucu düzeyi IP güvenlik duvarı kuralına eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' yi seçin. Sunucu düzeyinde bir IP güvenlik duvarı kuralı, tek bir IP adresi veya IP adresi aralığı için 1433 bağlantı noktasını açabilir.

   > [!IMPORTANT]
   > Varsayılan olarak, Azure SQL veritabanı güvenlik duvarı üzerinden erişim tüm Azure hizmetleri için devre dışıdır. Tüm Azure hizmetleri için erişimi etkinleştirmek istiyorsanız bu sayfada **Açık '** ı seçin.
   >

5. **Kaydet**’i seçin. Sunucuda 1433 numaralı bağlantı noktasını açan geçerli IP adresiniz için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur.

6. **Güvenlik duvarı ayarları** sayfasını kapatın.

SQL Server Management Studio veya seçtiğiniz başka bir aracı kullanarak, daha önce oluşturduğunuz sunucu yönetici hesabını kullanarak bu IP adresinden sunucuya ve veritabanlarına bağlanabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[Sonraki adımlar](#next-steps)’a geçip farklı yöntemler kullanarak veritabanını sorgulama hakkında bilgi edinmek istiyorsanız bu kaynakları kaydedin. Ancak, bu hızlı başlangıçta oluşturduğunuz kaynakları silmek isterseniz, aşağıdaki adımları kullanın.

1. Azure portal sol taraftaki menüden **kaynak grupları** ' nı seçin ve ardından **myresourcegroup**' ı seçin.
2. Kaynak grubu sayfanızda **Sil**' i seçin, metin kutusuna **myresourcegroup** yazın ve ardından **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Artık bir veritabanınız olduğuna göre, şunlar dahil tercih ettiğiniz araçlar ya da diller ile veritabanında [bağlanma ve sorgulama](connect-query-content-reference-guide.md) işlemleri yapabilirsiniz:
  - [SQL Server Management Studio kullanarak bağlanma ve sorgulama](connect-query-ssms.md)
  - [Azure Data Studio kullanarak bağlanma ve sorgulama](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- İlk veritabanınızı tasarlamayı, tablolar oluşturmayı ve veri eklemeyi öğrenmek için, şu öğreticilerden birine bakın:
  - [SSMS kullanarak Azure SQL veritabanı 'nda ilk tek veritabanınızı tasarlama](design-first-database-tutorial.md)
  - [Azure SQL veritabanı 'nda tek bir veritabanı tasarlama ve C# ve ADO.NET ile bağlanma](design-first-database-csharp-tutorial.md)
