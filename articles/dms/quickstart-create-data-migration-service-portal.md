---
title: 'Hızlı başlangıç: Azure portal kullanarak bir örnek oluşturma'
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturmak için Azure portal kullanın.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/29/2021
ms.openlocfilehash: 8a500104a0273b9e131815c4dc832bd33729cd51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566600"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Hızlı Başlangıç: Azure portalını kullanarak Azure Veritabanı Geçiş Hizmeti örneği oluşturma

Bu hızlı başlangıçta, Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturmak için Azure portal kullanırsınız. Örneği oluşturduktan sonra, verileri birden çok veritabanı kaynağından Azure SQL veritabanı 'na veya SQL Server Azure SQL yönetilen örneğine SQL Server gibi Azure veri platformları arasında geçirmek için kullanabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Web tarayıcınızı açın, [Microsoft Azure portalına gidin](https://portal.azure.com/) ve kimlik bilgilerinizi girerek portalda oturum açın. Varsayılan görünüm hizmet panonuzu içerir.

> [!NOTE]
> Her bölge için bir abonelik başına en fazla 10 DMS örneği oluşturabilirsiniz. Daha fazla sayıda örneğe ihtiyacınız varsa lütfen bir destek bileti oluşturun.

## <a name="register-the-resource-provider"></a>Kaynak sağlayıcısını kaydetme

İlk Veritabanı Geçiş Hizmeti örneğinizi oluşturmadan önce Microsoft.DataMigration kaynak sağlayıcısını kaydedin.

1. Azure portal, **abonelikleri** arayıp seçin.

   ![Portal aboneliklerini gösterme](media/quickstart-create-data-migration-service-portal/portal-select-subscription.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/quickstart-create-data-migration-service-portal/portal-select-resource-provider.png)

3. Geçiş için arama yapın ve ardından **Microsoft. DataMigration** için **Kaydol** ' u seçin.

    ![Kaynak sağlayıcısını kaydetme](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Hizmetin bir örneğini oluşturma

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin. **Azure veritabanı geçiş hizmeti**'ni arayıp seçin.

    ![Azure Market](media/quickstart-create-data-migration-service-portal/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/quickstart-create-data-migration-service-portal/dms-create.png)

3. **Geçiş hizmeti oluşturma** temelleri ekranında:

     - Aboneliği seçin.
     - Yeni bir kaynak grubu oluşturun veya var olan bir grubu seçin.
     - Azure veritabanı geçiş hizmeti örneği için bir ad belirtin.
     - Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz konumu seçin.
     - Hizmet modu olarak **Azure** ' ı seçin.
     - Fiyatlandırma katmanını seçin. Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.
     
    ![Azure veritabanı geçiş hizmeti örneği temelleri ayarlarını yapılandırma](media/quickstart-create-data-migration-service-portal/dms-create-basics.png)

     - Sonraki: Ağ’ı seçin.

4. **Geçiş hizmeti oluştur** ağ ekranında:

    - Var olan bir sanal ağı seçin veya yeni bir ağ oluşturun. Sanal ağ, kaynak veritabanı ve hedef ortama erişimi olan Azure veritabanı geçiş hizmeti sağlar. Azure portal sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

    ![Azure veritabanı geçiş hizmeti örneği ağ ayarlarını yapılandırma](media/quickstart-create-data-migration-service-portal/dms-network-settings.png)

    - Hizmeti oluşturmak için **gözden geçir + oluştur** ' u seçin. 
    
    - Birkaç dakika sonra Azure veritabanı geçiş hizmeti örneğiniz oluşturulup kullanıma hazırlanmaya hazırız:

    ![Geçiş hizmeti oluşturuldu](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[Azure kaynak grubunu](../azure-resource-manager/management/overview.md)silerek bu hızlı başlangıçta oluşturulan kaynakları temizleyebilirsiniz. Kaynak grubunu silmek için, oluşturduğunuz Azure Veritabanı Geçiş Hizmeti örneğine gidin. **Kaynak grubu** adını seçin ve **Kaynak grubunu sil** seçeneğini belirleyin. Bu eylem kaynak grubundaki tüm varlıkları ve kaynak grubunu siler.

## <a name="next-steps"></a>Sonraki adımlar

* [SQL Server’ı Azure SQL Veritabanı’na çevrimdışı geçirme](tutorial-sql-server-to-azure-sql.md)
* [SQL Server’ı Azure SQL Veritabanı’na çevrimiçi geçirme](./tutorial-sql-server-to-azure-sql.md)
* [SQL Server Azure SQL yönetilen örneğine çevrimdışı geçirme](tutorial-sql-server-to-managed-instance.md)
* [SQL Server çevrimiçi olarak Azure SQL yönetilen örneğine geçirme](tutorial-sql-server-managed-instance-online.md)