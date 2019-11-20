---
title: Tek bir veritabanı oluşturma
description: Azure portal, PowerShell ve Azure CLı kullanarak Azure SQL veritabanı 'nda tek bir veritabanı oluşturun ve sorgulayın.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
ms.date: 09/09/2019
ms.openlocfilehash: 482fc3f08fb986908efcab83a82338ced36ee689
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821000"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>Hızlı başlangıç: Azure portal, PowerShell ve Azure CLı kullanarak Azure SQL veritabanı 'nda tek bir veritabanı oluşturma

Tek bir [veritabanı](sql-database-single-database.md) oluşturmak, Azure SQL veritabanı 'nda veritabanı oluşturmak için en hızlı ve en basit dağıtım seçeneğidir. Bu hızlı başlangıçta, Azure portal kullanarak tek bir veritabanını nasıl oluşturacağınız ve sorgulayan gösterilmektedir.

Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. 

Bu hızlı başlangıçtaki tüm adımlar için [Azure Portal](https://portal.azure.com/)oturum açın.

## <a name="create-a-single-database"></a>Tek bir veritabanı oluşturma

Sağlanan veya sunucusuz işlem katmanında tek bir veritabanı oluşturulabilir.

- Sağlanan işlem katmanındaki tek bir veritabanı, iki [satın alma modelinden](sql-database-purchase-models.md)bırını kullanarak CPU ve bellek dahil olmak üzere sabit bir işlem kaynakları miktarı önceden ayrıldı.
- Sunucusuz işlem katmanındaki tek bir veritabanı, otomatik olarak ölçeklenen ve yalnızca [sanal çekirdek tabanlı satın alma modellerinde](sql-database-service-tiers-vcore.md)bulunan CPU ve bellek dahil olmak üzere bir dizi işlem kaynağına sahiptir.

Tek bir veritabanı oluşturduğunuzda, bunu yönetmek için bir [SQL veritabanı sunucusu](sql-database-servers.md) tanımlar ve belirli bir bölgedeki [Azure Kaynak grubuna](../azure-resource-manager/resource-group-overview.md) yerleştirebilirsiniz.

> [!NOTE]
> Bu hızlı başlangıç, [sanal çekirdek tabanlı satın alma modelini](sql-database-service-tiers-vcore.md)kullanır, ancak [DTU tabanlı satın alma modeli](sql-database-service-tiers-DTU.md) de kullanılabilir.

AdventureWorksLT örnek verilerini içeren tek bir veritabanı oluşturmak için:

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Veritabanını sorgulama

Veritabanını oluşturduğdığınıza göre veritabanına bağlanmak ve verileri sorgulamak için Azure portal yerleşik sorgu aracını kullanın.

1. Veritabanınızın **SQL veritabanı** sayfasında, soldaki menüden **sorgu Düzenleyicisi 'ni (Önizleme)** seçin.

   ![Sorgu Düzenleyicisi 'nde oturum açın](./media/sql-database-get-started-portal/query-editor-login.png)

2. Oturum açma bilgilerinizi girip **Tamam**' ı seçin.
3. **Sorgu Düzenleyicisi** bölmesine aşağıdaki sorguyu girin.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. **Çalıştır**' ı seçin ve ardından **sonuçlar** bölmesinde sorgu sonuçlarını gözden geçirin.

   ![Sorgu Düzenleyicisi sonuçları](./media/sql-database-get-started-portal/query-editor-results.png)

5. **Sorgu Düzenleyicisi** sayfasını kapatın ve kaydedilmemiş düzenlemelerinizi atmak isteyip Istemediğiniz sorulduğunda **Tamam** ' ı seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[Sonraki adımlara](#next-steps)gitmek istiyorsanız bu kaynak grubunu, veritabanı sunucusunu ve tek veritabanını saklayın. Sonraki adımlarda, farklı yöntemler kullanarak veritabanınızı nasıl bağlayabilmeniz ve Sorgulayabileceğiniz gösterilmektedir.

Bu kaynakları kullanmayı tamamladığınızda, bunları aşağıdaki gibi silebilirsiniz:

1. Azure portal sol menüden **kaynak grupları**' nı seçin ve ardından **myresourcegroup**' ı seçin.
2. Kaynak grubu sayfanızda **kaynak grubunu sil**' i seçin.
3. Alana *Myresourcegroup* yazın ve ardından **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Şirket içi veya uzak araçlardan tek veritabanına bağlanmak için sunucu düzeyinde bir güvenlik duvarı kuralı oluşturun. Daha fazla bilgi için bkz. [sunucu düzeyinde güvenlik duvarı kuralı oluşturma](sql-database-server-level-firewall-rule.md).
- Sunucu düzeyinde bir güvenlik duvarı kuralı oluşturduktan sonra, birkaç farklı araç ve dil kullanarak veritabanınızı [bağlayın ve sorgulayın](sql-database-connect-query.md) .
  - [SQL Server Management Studio kullanarak bağlanma ve sorgulama](sql-database-connect-query-ssms.md)
  - [Azure Data Studio kullanarak bağlanma ve sorgulama](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Sağlanan işlem katmanında Azure CLı kullanarak tek bir veritabanı oluşturmak için bkz. [Azure CLI örnekleri](sql-database-cli-samples.md).
- Sağlanan işlem katmanında Azure PowerShell kullanarak tek bir veritabanı oluşturmak için bkz. [Azure PowerShell örnekleri](sql-database-powershell-samples.md).
- Azure PowerShell 'i kullanarak sunucusuz işlem katmanında tek bir veritabanı oluşturmak için bkz. [sunucusuz veritabanı oluşturma](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).
