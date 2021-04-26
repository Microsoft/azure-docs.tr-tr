---
title: 'Hızlı başlangıç: sunucu oluşturma-Azure portal-PostgreSQL için Azure veritabanı-esnek sunucu'
description: Azure portal Kullanıcı arabirimini kullanarak PostgreSQL için Azure veritabanı oluşturma ve yönetme Kılavuzu-esnek sunucu.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 6d10298b016cbcf362af2d272f9fa822db6e569d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604762"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>Hızlı başlangıç: Azure portal PostgreSQL için Azure veritabanı oluşturma-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

PostgreSQL için Azure Veritabanı, bulutta son derece kullanılabilir olan PostgreSQL veritabanlarını çalıştırmak, yönetmek ve ölçeklendirmek için kullandığınız, yönetilen bir hizmettir. Bu hızlı başlangıçta, Azure portal kullanarak yaklaşık beş dakika içinde PostgreSQL için Azure veritabanı-esnek sunucu oluşturma hakkında bilgi verilmektedir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Web tarayıcınızı açın ve [portala](https://portal.azure.com/) gidin. Portalda oturum açmak için kimlik bilgilerinizi girin. Varsayılan görünüm hizmet panonuzu içerir.

## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL için Azure Veritabanı sunucusu oluşturma

PostgreSQL için Azure Veritabanı sunucusu, yapılandırılmış bir dizi [işlem ve depolama kaynağı](./concepts-compute-storage.md) ile oluşturulur. Sunucu, [Azure kaynak grubu](../../azure-resource-manager/management/overview.md) içinde oluşturulur.

PostgreSQL için Azure Veritabanı sunucusu oluşturmak için şu adımları uygulayın:

1. Portalın sol üst köşesinde bulunan **kaynak oluştur** (+) seçeneğini belirleyin.

2. **Veritabanları**  >  **PostgreSQL için Azure veritabanı**' nı seçin.

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Menüdeki PostgreSQL için Azure veritabanı":::

3. **Esnek sunucu** dağıtım seçeneğini belirleyin.

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="PostgreSQL için Azure veritabanı 'nı seçin-esnek sunucu dağıtım seçeneği":::

4. **Temel** bilgiler formunu aşağıdaki bilgilerle doldurun:

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="Sunucu oluşturma":::

    Ayar|Önerilen Değer|Açıklama
    ---|---|---
    Abonelik|Aboneliğinizin adı|Sunucunuz için kullanmak istediğiniz Azure aboneliği. Birden çok aboneliğiniz varsa, kaynağın faturalandırılması için istediğiniz aboneliği seçin.
    Kaynak grubu|*myresourcegroup*| Yeni bir kaynak grubu adı veya aboneliğinizde var olan bir kaynak grubu.
    Sunucu adı |*mydemoserver*|PostgreSQL için Azure Veritabanı sunucunuzu tanıtan benzersiz bir ad. Girdiğiniz sunucu adına *postgres.database.azure.com* etki alanı adı eklenir. Sunucunuz yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. En az 3, en çok 63 karakterden oluşmalıdır.
    Yönetici kullanıcı adı |*myadmin*| Sunucuya bağlanırken kullanılacak kendi oturum açma hesabınız. Yönetici oturum açma adı **azure_superuser**, **azure_pg_admin**, **yönetici**, **yönetici**, **kök**, **Konuk** veya **ortak** olamaz. **PG_** ile başlayamaz.
    Parola |Parolanız| Sunucu yönetici hesabı için yeni bir parola. 8 ile 128 arasında karakter içermelidir. Parolanız şu kategorilerden üçünde yer alan karakterlerden oluşmalıdır: İngilizce büyük harfler, İngilizce küçük harfler, sayılar (0 - 9) ve alfasayısal olmayan karakterler (!, $, #, %, vb.).
    Konum|Kullanıcılarınıza en yakın bölge| Kullanıcılarınız için en yakın olan konum.
    Sürüm|En son ana sürüm| Belirli gereksinimleriniz olmadığı sürece, en son PostgreSQL ana sürümü.
    İşlem + depolama | **Genel amaçlı**, **4 sanal çekırdek**, **512 GB**, **7 gün** | Yeni sunucunuz için işlem, depolama ve yedekleme yapılandırmaları. **Sunucuyu Yapılandır**' ı seçin. *Genel amaçlı*, *4 sanal çekırdek*, *512 GB* ve *7 gün* , **Işlem katmanı**, **sanal çekirdek**, **depolama** ve **yedekleme saklama süresi** için varsayılan değerlerdir. Bu kaydırıcıları olduğu gibi bırakabilir veya düzenleyebilirsiniz. Bu fiyatlandırma katmanı seçimini kaydetmek için **Tamam**’ı seçin. Sonraki ekran görüntüsü bu seçimleri yakalar.

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="Fiyatlandırma Katmanı bölmesi":::
    
5. Ağ seçeneklerini yapılandırma

    Ağ sekmesinde, sunucunuza nasıl ulaşılabildiğini seçebilirsiniz. PostgreSQL için Azure Veritabanı, sunucu düzeyinde bir güvenlik duvarı oluşturur. Bu güvenlik duvarı, belirli IP adresleri için güvenlik duvarını açmak üzere bir kural oluşturmadığınız sürece, dış uygulama ve araçların sunucuya ve sunucu üzerindeki herhangi bir veritabanına bağlanmasını engeller. Sunucunun genel olarak erişilebilir olmasını öneririz:

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="Ağ bölmesi":::

    Ve sonra kendi istemci IP adresiniz ile kısıtlanıyor:

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Geçerli istemci IP adresi Ekle ' yi seçin":::

6. Seçimlerinizi gözden geçirmek için **gözden geçir + oluştur** ' u seçin. Sunucuyu sağlamak için **Oluştur**’u seçin. Bu işlem birkaç dakika sürebilir.

7. Araç çubuğunda, dağıtım sürecini izlemek için **Bildirimler** simgesini (zil) seçin. Dağıtım tamamlandığında, bu sunucu için Azure portalı panonuzda sunucunun **Genel bakış** sayfasına bir kısayol ekleyen **Panoya sabitle** öğesini seçebilirsiniz. **Kaynağa git**’i seçmek sunucunun **Genel bakış** sayfasını açar.

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="Bildirimler bölmesi":::

   Varsayılan olarak, sunucunuzun altında bir **postgres** veritabanı oluşturulur. [Postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) veritabanı; kullanıcılar, yardımcı programlar ve üçüncü taraf uygulamaları tarafından kullanılmak üzere geliştirilmiş, varsayılan bir veritabanıdır. (Diğer varsayılan veritabanı: **azure_maintenance**. İşlevi, yönetilen hizmet işlemlerini kullanıcı eylemlerinden ayırmaktır. Bu veritabanına erişemezsiniz.)

    > [!NOTE]
    > PostgreSQL sunucusu için Azure Veritabanınıza yönelik bağlantılar 5432 bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 5432 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, BT departmanınız 5432 numaralı bağlantı noktasını açmadığı sürece sunucunuza bağlanamazsınız.
    >

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

PostgreSQL için Azure Veritabanı sunucunuzu oluşturduğunuzda, **postgres** adlı varsayılan veritabanı da oluşturulur. Veritabanı sunucunuza bağlanmak için tam sunucu adını ve yönetici oturum açma kimlik bilgileri gerekir. Bu değerleri Hızlı Başlangıç makalesinde daha önce not almış olabilirsiniz. Almadıysanız, portaldaki sunucuya **Genel Bakış** sayfasında sunucu adını ve oturum açma bilgilerini kolayca bulabilirsiniz.

Sunucunuzun **Genel Bakış** sayfasını açın. **Sunucu adını** ve **Sunucu yöneticisi oturum açma adını** not alın. İmlecinizi her alanın üzerine getirin; metnin sağ tarafında Kopyala simgesi görünür. Değerleri kopyalamak için gerektiği gibi Kopyala simgesini seçin.

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="Sunucuya genel bakış sayfası":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>Psql’yi kullanarak PostgreSQL veritabanına bağlanma

PostgreSQL sunucusu için Azure veritabanınıza çeşitli uygulamalar kullanarak bağlanabilirsiniz. İstemci bilgisayarınızda PostgreSQL yüklüyse, [psql](https://www.postgresql.org/docs/current/static/app-psql.html)’nin yerel bir örneğini kullanarak Azure PostgreSQL sunucusuna bağlanabilirsiniz. Şimdi psql komut satırı yardımcı programını kullanarak Azure PostgreSQL sunucusuna bağlanalım.

1. PostgreSQL için Azure Veritabanı sunucusuna bağlanmak üzere aşağıdaki psql komutunu çalıştırın

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Örneğin aşağıdaki komut, erişim kimlik bilgilerini kullanarak **mydemoserver.postgres.database.azure.com** PostgreSQL sunucunuzda **postgres** adlı varsayılan veritabanına bağlanır. Parola istendiğinde seçtiğiniz `<server_admin_password>` değerini girin.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Bağlantı kurduktan sonra, psql yardımcı programı sql komutlarını girdiğiniz bir postgres istemi görüntüler. Kullandığınız psql ile PostgreSQL için Azure Veritabanı sunucu sürümü farklı olabileceğinden, ilk bağlantı çıkışında bir uyarı gösterilebilir.

   Örnek psql çıktısı:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Güvenlik duvarı istemcinizin IP adreslerine izin verecek biçimde yapılandırılmamışsa aşağıdaki hata oluşur:
   >
   > "psql: ÖNEMLI: Ana bilgisayar `<IP address>` ," myadmin ", veritabanı" Postgres "için bir pg_hba. conf girişi, önemli olan SSL: SSL bağlantısı gerekiyor. SSL seçeneklerini belirtin ve yeniden deneyin.
   >
   > Yukarıdaki güvenlik duvarı kuralları adımındaki istemci IP 'nizin izin verildiğini doğrulayın.

2. Aşağıdaki komutu yazarak istemde "mypgsqldb" adlı boş bir veritabanı oluşturun:

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. İstemde, bağlantıyı yeni oluşturulan **mypgsqldb** veritabanına geçirmek için aşağıdaki komutu yürütün:

    ```bash
    \c mypgsqldb
    ```

4. `\q` yazın ve ardından Enter tuşunu seçerek psql'den çıkın.

Psql aracılığıyla PostgreSQL için Azure Veritabanı sunucusuna bağlandınız ve boş bir kullanıcı veritabanı oluşturdunuz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıç bölümünde oluşturduğunuz kaynakları iki yoldan biriyle temizleyebilirsiniz. Kaynak grubundaki tüm kaynakları içeren Azure kaynak grubunu silebilirsiniz. Diğer kaynakları korumak istiyorsanız yalnızca sunucu kaynağını silin.

> [!TIP]
> Bu koleksiyondaki diğer Hızlı Başlangıçlar, bu Hızlı Başlangıcı temel alır. Hızlı başlangıçlarla çalışmaya devam etmeyi planlıyorsanız bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, portalda bu hızlı başlangıç ile oluşturulmuş olan kaynakları silmek için aşağıdaki adımları izleyin.

Yeni oluşturulan sunucu dahil olmak üzere kaynak grubunun tamamını silmek için:

1. Portalda kaynak grubunuzu bulun. Soldaki menüden **Kaynak grupları**'nı seçin. Ardından, kaynak grubunuzun adını (örnekteki **myresourcegroup** gibi) seçin.

2. Kaynak grubunuzun sayfasında **Sil**’i seçin. Silme işlemini onaylamak için, metin kutusuna kaynak grubunuzun adını (örneğin, **myresourcegroup**) girin. **Sil**’i seçin.

Yalnızca yeni oluşturulan sunucuyu silmek için:

1. Sunucunuz açık değilse portalda sunucuyu bulun. Soldaki menüden **Tüm kaynaklar**'ı seçin. Ardından, oluşturduğunuz sunucuyu arayın.

2. **Genel Bakış** sayfasında **Sil**’i seçin.

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="Sil düğmesi":::

3. Silmek istediğiniz sunucu adını onaylayın ve altındaki etkilenen veritabanlarını görüntüleyin. Metin kutusuna sunucunuzun adını (örneğin, **demosunucum**) girin. **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [App Service ve PostgreSQL ile Docgo uygulaması dağıtma](tutorial-django-app-service-postgres.md)