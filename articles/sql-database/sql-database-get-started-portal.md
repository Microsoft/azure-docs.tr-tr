---
title: "Azure portalı: SQL veritabanı oluşturma | Microsoft Docs"
description: "Azure portalında SQL Veritabanı mantıksal sunucusu, sunucu düzeyi güvenlik duvarı kuralı ve veritabanı oluşturmayı öğrenin. Ayrıca Azure portalını kullanarak bir Azure SQL veritabanında sorgu yürütmeyi öğrenirsiniz."
keywords: "sql veritabanı öğreticisi, sql veritabanı oluşturma"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: be5839e04fae457b889db11dffe56f31afe723a5
ms.lasthandoff: 03/28/2017


---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Azure portalında Azure SQL veritabanı oluşturma

Bu hızlı başlangıç öğreticisinde, Azure’da SQL veritabanı oluşturma adımlar gösterilmektedir.  Azure SQL Veritabanı, bulutta yüksek oranda kullanılabilir SQL Server veritabanlarını çalıştırıp ölçeklendirmenize olanak tanıyan bir “Hizmet Olarak Veritabanı” teklifidir.  Bu hızlı başlangıç, Azure portalını kullanarak yeni bir SQL veritabanı oluşturma işlemini göstermektedir.

## <a name="log-in-to-the-azure-portal"></a>Azure portalında oturum açma

[Azure Portal](https://portal.azure.com/)’da oturum açın.

## <a name="create-a-sql-database"></a>SQL veritabanı oluşturma

Azure SQL veritabanı bir dizi [işlem ve depolama kaynağı](sql-database-service-tiers.md) ile oluşturulur. Veritabanı bir [Azure kaynak grubu](../azure-resource-manager/resource-group-overview.md) ve bir [Azure SQL Veritabanı mantıksal sunucusu](sql-database-features.md) içinde oluşturulur. 

Adventure Works LT örnek verilerini içeren bir SQL veritabanı oluşturmak için bu adımları izleyin. 

1. Azure portalının sol üst köşesinde bulunan **Yeni** düğmesine tıklayın.

2. **Yeni** penceresinden **Veritabanları**’nı seçin ve **Veritabanları** penceresinden **SQL Veritabanı**’nı seçin.

    ![create database-1](./media/sql-database-get-started/create-database-1.png)

3. SQL Veritabanı formunu, önceki görüntüde gösterildiği gibi aşağıdaki bilgilerle doldurun: 
   - Veritabanı adı: **mySampleDatabase** seçeneğini kullanın
   - Kaynak grubu: **myResourceGroup** seçeneğini kullanın
   - Kaynak: **Örnek (AdventureWorksLT)** seçeneğini belirleyin

4. Yeni veritabanınız için yeni bir sunucu oluşturup yapılandırmak üzere **Sunucu**’ya tıklayın. Genel olarak benzersiz bir sunucu adı belirterek **Yeni sunucu formu**’nu doldurun, Sunucu yönetici oturumu için bir ad sağlayın ve ardından seçtiğiniz parolayı belirtin. 

    ![create database-server](./media/sql-database-get-started/create-database-server.png)
5. **Seç**'e tıklayın.

6. Yeni veritabanınıza ait hizmet katmanını ve performans düzeyini belirtmek için **Fiyatlandırma katmanı**’na tıklayın. Bu hızlı başlangıç için **20 DTU** ve **250** GB depolama alanı seçin

    ![create database-s1](./media/sql-database-get-started/create-database-s1.png)

7. **Uygula**'ya tıklayın.  

8. Veritabanını sağlamak için **Oluştur**’a tıklayın. Sağlama birkaç dakika sürer. 

9. Araç çubuğunda **Bildirimler**’e tıklayarak dağıtım işlemini izleyin.

    ![bildirim](./media/sql-database-get-started/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>Sunucu düzeyinde bir güvenlik duvarı kuralı oluşturma

SQL Veritabanı hizmeti, güvenlik duvarını belirli IP adreslerine açmaya yönelik bir güvenlik duvarı kuralı oluşturulmadıkça, dış uygulama ve araçların sunucuya ya da sunucu üzerindeki herhangi bir veritabanına bağlanmasını engelleyen sunucu düzeyinde bir güvenlik duvarı kuralı oluşturur. SQL Veritabanı güvenlik duvarı üzerinden yalnızca IP adresinize yönelik dış bağlantıları etkinleştirmek üzere istemcinizin IP adresi için bir [SQL Veritabanı sunucu düzeyi güvenlik duvarı kuralı](sql-database-firewall-configure.md) oluşturmak için bu adımları izleyin. 

1. Dağıtım tamamlandıktan sonra, **SQL veritabanları** sayfasında soldaki menüden **SQL veritabanları**’na ve yeni veritabanınız **mySampleDatabase**’e tıklayın. Veritabanınız için bir genel bakış sayfası açılır ve tam sunucu adı (örneğin **mynewserver20170327.database.windows.net**) görüntülenerek daha fazla yapılandırma seçeneği sunulur.

      ![sunucu güvenlik duvarı kuralı](./media/sql-database-get-started/server-firewall-rule.png) 

2. Önceki görüntüde gösterildiği gibi araç çubuğundaki **sunucu güvenlik duvarı ayarla** öğesine tıklayın. SQL Veritabanı sunucusu için **Güvenlik duvarı ayarları** sayfası açılır. 

3. Araç çubuğundaki **İstemci IP’si ekle** öğesine tıklayın ve ardından **Kaydet**’e tıklayın. Geçerli IP adresiniz için bir sunucu düzeyi güvenlik duvarı kuralı oluşturulur.

      ![sunucu güvenlik duvarı kuralı ayarla](./media/sql-database-get-started/server-firewall-rule-set.png) 

4. **Tamam**’a tıklayın ve ardından **Güvenlik duvarı ayarları** sayfasını kapatmak için **X** öğesine tıklayın.

Artık SQL Server Management Studio veya seçtiğiniz başka bir aracı kullanarak veritabanına ve sunucusuna bağlanabilirsiniz.

## <a name="query-the-sql-database"></a>SQL veritabanını sorgulama

SQL veritabanımız oluşturulurken **AdventureWorksLT** örnek veritabanı ile doldurulmuştur (bu hızlı başlangıcın başındaki Kullanıcı Arabirimi Oluşturma bölümünde belirlediğimiz seçeneklerden biriydi). Şimdi de verileri sorgulamak için Azure portalındaki yerleşik sorgu aracını kullanalım. 

1. Veritabanınız için SQL Veritabanı sayfasında, araç çubuğundaki **Araçlar**’a tıklayın. **Araçlar** sayfası açılır.

     ![araçlar menüsü](./media/sql-database-get-started/tools-menu.png) 

2. **Sorgu düzenleyicisi (önizleme)**’ye tıklayın, **Önizleme koşulları** onay kutusuna tıklayın ve ardından **Tamam**’a tıklayın. Sorgu düzenleyicisi sayfası açılır.

3. **Oturum aç**’a tıklayın ve istendiğinde **SQL Server kimlik doğrulamasını** seçin ve daha önce oluşturduğunuz sunucu yöneticisi oturum açma bilgilerini ve parolayı girin.

    ![oturum açma](./media/sql-database-get-started/login.png) 

4. Oturum açmak için **Tamam**’a tıklayın.

5. Kimlik doğrulaması yaptıktan sonra sorgu düzenleyici bölmesine aşağıdaki sorguyu yazın.

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. **Çalıştır**’a tıklayın ve **Sonuçlar** bölmesindeki sorgu sonuçlarını gözden geçirin.

    ![sorgu düzenleyicisi sonuçları](./media/sql-database-get-started/query-editor-results.png)

7. **Sorgu düzenleyici** sayfasını kapatmak için **X** simgesine, **Araçlar** sayfasını kapatmak için yeniden **X** simgesine tıklayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu koleksiyondaki diğer hızlı başlangıçlar, bu hızlı başlangıca göre belirlenir. Sonraki hızlı başlangıçlar veya öğreticilerle devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, Azure portalda bu hızlı başlangıç ile oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Azure portalında sol taraftaki menüden, **Kaynak grupları**’na tıklayın ve ardından **myResourceGroup**’a tıklayın. 
2. Kaynak grubu sayfanızda, **Sil**’e tıklayın, metin kutusuna **myResourceGroup** yazın ve ardından **Sil**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- SQL Server Management Studio kullanarak bağlanmak ve sorgu yürütmek için, bkz. [SSMS ile bağlanma ve sorgu yürütme](sql-database-connect-query-ssms.md)
- Visual Studio kullanarak bağlanmak için bkz. [Visual Studio ile bağlanma ve sorgu yürütme](sql-database-connect-query.md).
- SQL Veritabanı’na teknik bir genel bakış için, bkz. [SQL Veritabanı hizmeti hakkında](sql-database-technical-overview.md).

