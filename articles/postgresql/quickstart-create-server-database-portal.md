---
title: 'Hızlı başlangıç: sunucu oluşturma-Azure portal-PostgreSQL için Azure veritabanı-tek sunucu'
description: Bu hızlı başlangıç kılavuzunda, Azure portal kullanarak bir PostgreSQL için Azure veritabanı sunucusu oluşturacaksınız ve yönetebilirsiniz.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 6b5b145e84a3844b646ec9adfb46f8a221c6d0a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606513"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak PostgreSQL için Azure veritabanı sunucusu oluşturma

PostgreSQL için Azure Veritabanı, bulutta son derece kullanılabilir olan PostgreSQL veritabanlarını çalıştırmak, yönetmek ve ölçeklendirmek için kullandığınız, yönetilen bir hizmettir. Bu hızlı başlangıçta, tek bir PostgreSQL sunucusu için Azure veritabanı oluşturma ve buna bağlanma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar
Bir Azure aboneliği gereklidir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL için Azure Veritabanı sunucusu oluşturma
PostgreSQL için Azure veritabanı tek sunuculu veritabanı oluşturmak için [Azure Portal](https://portal.azure.com/) gidin. *PostgreSQL sunucuları Için Azure veritabanı*'nı arayın ve seçin.

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="PostgreSQL için Azure veritabanı 'nı bulun.":::

1. **Add (Ekle)** seçeneğini belirleyin.

2. PostgreSQL için Azure veritabanı oluşturma sayfasında,  **tek sunucu**' yı seçin.

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="Tek sunucu seçin":::

3. Şimdi aşağıdaki bilgilerle **temel bilgiler** formunu girin.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Tek bir sunucu oluşturmaya yönelik temel bilgiler sekmesini gösteren ekran görüntüsü.":::

   |Ayar|Önerilen değer|Açıklama|
   |:---|:---|:---|
   |Abonelik|abonelik adınız|istediğiniz Azure aboneliğini seçin.|
   |Kaynak grubu|*myresourcegroup*| Aboneliğinizden yeni veya var olan bir kaynak grubu.|
   |Sunucu adı |*mydemoserver*|PostgreSQL için Azure Veritabanı sunucunuzu tanıtan benzersiz bir ad. *Postgres.Database.Azure.com* etki alanı adı, sağladığınız sunucu adına eklenir. Sunucunuz yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. 3 ile 63 arasında karakter içermelidir.|
   |Veri kaynağı | Yok | Sıfırdan yeni bir sunucu oluşturmak için **Yok**'u seçin. Yalnızca var olan bir sunucunun coğrafi yedeklemesinden geri yükleme yaptıysanız **yedekleme** ' yi seçin.|
   |Yönetici kullanıcı adı |*myadmin*| Sunucu Yöneticisi Kullanıcı adınızı girin. **PG_** ile başlayamaz ve bu değerlere izin verilmez: **azure_superuser**, **azure_pg_admin**, **yönetici**, **yönetici**, **kök**, **Konuk** veya **genel**.|
   |Parola |parolanız| Sunucu Yöneticisi kullanıcısı için yeni bir parola. Aşağıdaki kategorilerden üçünden 8 ila 128 karakter içermesi gerekir: Ingilizce büyük harfler, Ingilizce küçük harfler, sayılar (0-9) ve alfasayısal olmayan karakterler (örneğin,!, $, #,%).|
   |Konum|istediğiniz konum| Açılan listeden bir konum seçin.|
   |Sürüm|En son ana sürüm| Belirli gereksinimleriniz olmadığı sürece, en son PostgreSQL ana sürümü.|
   |İşlem + depolama | *Varsayılanları Kullan*| Varsayılan fiyatlandırma katmanı, **4 sanal çekirdek** ve **100 GB** depolama alanı ile **genel amaçlı** . Yedekleme saklama, **coğrafi olarak yedekli** yedekleme seçeneğiyle **7 güne** ayarlanır.<br/>[Fiyatlandırma](https://azure.microsoft.com/pricing/details/postgresql/server/) hakkında bilgi edinin ve gerekirse Varsayılanları güncelleştirin.|


   > [!NOTE]
   > Hafif işlem ve g/ç iş yükünüz için yeterli ise temel fiyatlandırma katmanını kullanmayı düşünün. Temel fiyatlandırma katmanında oluşturulan sunucuların daha sonra Genel Amaçlı veya bellek için Iyileştirilmiş olarak ölçeklenemeyeceğini unutmayın.

5. Seçimlerinizi gözden geçirmek için **gözden geçir + oluştur** ' u seçin. Sunucuyu sağlamak için **Oluştur**’u seçin. Bu işlem birkaç dakika sürebilir.
    > [!NOTE]
    > Boş bir veritabanı, **Postgres** oluşturulur. Ayrıca, yönetilen hizmet işlemlerini Kullanıcı eylemlerinden ayırmak için kullanılan bir **azure_maintenance** veritabanı bulacaksınız. **Azure_maintenance** veritabanına erişemezsiniz.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="başarılı dağıtım.":::

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>Güvenlik duvarı kuralını yapılandırma
Varsayılan olarak, oluşturduğunuz sunucuya herkese açık bir şekilde erişilebilir. IP adresiniz için izinler vermeniz gerekir. Azure portal sunucu kaynağınız ' ne gidin ve sunucu kaynağınız için sol taraftaki menüden **bağlantı güvenliği** ' ni seçin. Kaynağınızın nasıl bulunacağını bilmiyorsanız bkz. [kaynakları açma](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Bağlantı güvenliği için güvenlik duvarı kurallarını gösteren ekran görüntüsü.":::

**Geçerli ISTEMCI IP adresi ekle**' yi seçin ve ardından **Kaydet**' i seçin. Daha fazla IP adresi ekleyebilir veya bu IP adreslerinden sunucunuza bağlanmak için bir IP aralığı sağlayabilirsiniz. Daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı 'Nda güvenlik duvarı kuralları](./concepts-firewall-rules.md).

> [!NOTE]
> Bağlantı sorunlarından kaçınmak için ağınızın 5432 numaralı bağlantı noktası üzerinden giden trafiğe izin verdiğinden emin olun. PostgreSQL için Azure veritabanı bu bağlantı noktasını kullanır.

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>Psql ile sunucuya bağlanma

Popüler PostgreSQL istemcileri olan [psql](http://postgresguide.com/utilities/psql.html) veya [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html)' i kullanabilirsiniz. Bu hızlı başlangıçta, Azure portal içindeki [Azure Cloud Shell](../cloud-shell/overview.md) psql kullanarak bağlanacağız.

1. Sunucunuzun **genel bakış** bölümünden yeni oluşturduğunuz sunucu için sunucu adı, Sunucu Yöneticisi oturum açma adı, parola ve abonelik kimliğini bir yere göz atın.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="bağlantı bilgilerini alın.":::


2. Sol üst taraftaki simgeyi seçerek portalda Azure Cloud Shell açın.

   > [!NOTE]
   > Cloud Shell ilk kez açarsanız, bir kaynak grubu ve depolama hesabı oluşturmak için bir istem görürsünüz. Bu bir kerelik bir adımdır ve tüm oturumlar için otomatik olarak eklenir.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Azure Cloud Shell açma için sunucu bilgilerini ve simgesini gösteren ekran görüntüsü.":::

3. Azure Cloud Shell terminalinde aşağıdaki komutu çalıştırın. Değerleri gerçek sunucu adı ve Yönetici Kullanıcı oturum açma adınızla değiştirin. Yönetici kullanıcıyla boş veritabanı **Postgres** 'yi şu biçimde kullanın: `<admin-username>@<servername>` .

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   Bu deneyim Cloud Shell terminalinde nasıl görünür:

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. Aynı Azure Cloud Shell terminalinde, **Konuk** adlı bir veritabanı oluşturun.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Bağlantıları yeni oluşturulan **Konuk** veritabanına geçirin.

   ```bash
   \c guest
   ```
6. Yazın `\q` ve ardından Enter tuşunu seçerek psql ' i kapatın.

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bir kaynak grubundaki PostgreSQL için Azure veritabanı sunucusunu başarıyla oluşturdunuz. Gelecekte bu kaynaklara ihtiyaç duymazsanız, kaynak grubunu veya PostgreSQL sunucusunu silerek bunları silebilirsiniz.

Kaynak grubunu silmek için:

1. Azure portal, **kaynak gruplarını** arayıp seçin.
2. Kaynak grubu listesinde, kaynak grubunuzun adını seçin.
3. Kaynak grubunuzun **genel bakış** sayfasında **kaynak grubunu sil**' i seçin.
4. Onay iletişim kutusunda, kaynak grubunuzun adını girip **Sil**' i seçin.

Sunucuyu silmek için, sunucunuzun **genel bakış** sayfasında **Sil** düğmesini seçin:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Sunucu silme düğmesini gösteren ekran görüntüsü.":::

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Dışarı aktarma ve içeri aktarma kullanarak veritabanınızı geçirme](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [Veritabanı tasarlama](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[Aradığınızı bulamıyor musunuz? Bize bilgi verin.](https://aka.ms/postgres-doc-feedback)