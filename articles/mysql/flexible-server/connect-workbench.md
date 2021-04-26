---
title: 'Hızlı başlangıç: Connect-MySQL çalışma ekranı-MySQL için Azure veritabanı-esnek sunucu'
description: Bu hızlı başlangıçta MySQL çalışma sunucusunu kullanarak MySQL için Azure veritabanı 'na bağlanma ve veri sorgulama işlemleri sunulmaktadır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 95ecb64165075a7effe0c6eaf568ee172ad67b9b
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226303"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server-preview"></a>Hızlı başlangıç: MySQL çalışma sunucusunu kullanarak MySQL için Azure veritabanı 'na bağlanma ve veri sorgulama-esnek sunucu (Önizleme)


> [!IMPORTANT] 
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu hızlı başlangıçta MySQL çalışma ekranı uygulamasını kullanarak MySQL için Azure veritabanı esnek sunucusuna nasıl bağlanacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıçta, başlangıç noktası olarak şu kılavuzlardan birinde oluşturulan kaynaklar kullanılmaktadır:

- [Azure portal kullanarak MySQL için Azure veritabanı esnek sunucusu oluşturma](./quickstart-create-server-portal.md)
- [Azure CLı kullanarak MySQL için Azure veritabanı esnek sunucusu oluşturma](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>İstemci iş istasyonunuzu hazırlama
- Esnek sunucunuzu *özel erişim (VNET tümleştirmesi)* ile oluşturduysanız, sunucunuza aynı VNET içindeki bir kaynaktan sunucunuza bağlanmanız gerekir. Bir sanal makine oluşturabilir ve bunu esnek sunucunuz ile oluşturulan VNet 'e ekleyebilirsiniz. [Azure CLI kullanarak MySQL Için Azure veritabanı esnek sunucu sanal ağı oluşturma ve yönetme](./how-to-manage-virtual-network-cli.md)bölümüne bakın.
- Esnek sunucunuzu *ortak erişim (izin VERILEN IP adresleri)* ile oluşturduysanız, sunucunuzdaki güvenlik duvarı kuralları lıstesıne yerel IP adresinizi ekleyebilirsiniz. [Azure CLI kullanarak MySQL Için Azure veritabanı esnek sunucu güvenlik duvarı kuralları oluşturma ve yönetme](./how-to-manage-firewall-cli.md)bölümüne bakın.

- [MySQL web sitesinden](https://dev.mysql.com/downloads/workbench/) MySQL Workbench’i indirip bilgisayarınıza yükleyin.

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma

Esnek sunucuya bağlanmak için gereken bağlantı bilgilerini alın. Tam sunucu adı ve oturum açma kimlik bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure Portal sol taraftaki menüden **tüm kaynaklar**' ı seçin ve oluşturduğunuz sunucuyu (örneğin, **demosunucum**) arayın.
3. Sunucu adını seçin.
4. Sunucunun **Genel Bakış** panelinden **Sunucu adı** ile **Sunucu yöneticisi oturum açma adı**’nı not alın. Parolanızı unutursanız, bu panelden parolayı da sıfırlayabilirsiniz.
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>MySQL çalışma ekranı kullanarak sunucuya bağlanma

MySQL çalışma ekranı kullanarak MySQL için Azure veritabanı esnek sunucusuna bağlanmak için:

1. Bilgisayarınızda MySQL Workbench uygulamasını başlatın.

2. **Yeni Bağlantı Oluştur** iletişim kutusundaki **Parametreler** sekmesine aşağıdaki bilgileri girin:

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="yeni bağlantı oluştur":::

    | **Parametreler** | **Önerilen değer** | **Alan açıklaması** |
    |---|---|---|
    |    Bağlantı Adı | Tanıtım Bağlantısı | Bu bağlantı için bir etiket belirtin. |
    | Bağlantı Yöntemi | Standart (TCP/IP) | Standart (TCP/IP) yeterlidir. |
    | Konak adı | *sunucu adı* | MySQL için Azure Veritabanını oluştururken kullandığınız sunucu adı değerini belirtin. Gösterilen örnek sunucumuz: mydemoserver.mysql.database.azure.com. Örnekte gösterildiği gibi tam etki alanı adını (\*.mysql.database.azure.com) kullanın. Sunucu adınızı anımsamıyorsanız bağlantı bilgilerini almak için bir önceki bölümdeki adımları izleyin.  |
    | Bağlantı noktası | 3306 | MySQL Azure veritabanına bağlanırken her zaman bağlantı noktası olarak 3306 kullanın. |
    | Kullanıcı adı |  *Sunucu Yöneticisi oturum açma adı* | MySQL için Azure Veritabanını oluştururken girdiğiniz sunucu yöneticisi oturum açma kullanıcı adını yazın. Örnek Kullanıcı adı myadmin ' dir. Kullanıcı adını anımsamıyorsanız bağlantı bilgilerini almak için bir önceki bölümdeki adımları izleyin.
    | Parola | parolanız | Parolayı kaydetmek için **Kasada Depola...** düğmesine tıklayın. |

3. Tüm parametrelerin doğru yapılandırılıp yapılandırılmadığını test etmek için **Bağlantıyı Sına**’ya tıklayın.

4. Bağlantıyı kaydetmek için **Tamam**’a tıklayın.

5. **MySQL Bağlantıları** listesinde sunucunuza karşılık gelen kutucuğa tıklayıp bağlantının kurulmasını bekleyin.

    Sorgularınızı yazabileceğiniz boş bir düzenleyici içeren yeni bir SQL sekmesi açılır.

> [!NOTE]
> TLS 1,2 kullanılarak şifrelenen bağlantı, MySQL için Azure veritabanı esnek sunucusu 'nda gereklidir ve zorlanır. MySQL 'in sunucunuza bağlanması için TLS/SSL sertifikalarına genellikle ek bir yapılandırma gerekli olmasa da, TLS/SSL CA sertifikasını MySQL çalışma ekranı ile bağlamayı öneririz. Daha fazla bilgi için bkz. [TLS/SSL kullanarak bağlanma](./how-to-connect-tls-ssl.md)

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Tablo oluşturma, veri ekleme, verileri okuma, verileri güncelleştirme, verileri silme

1. Bazı örnek verileri görmek için örnek SQL kodunu kopyalayıp boş bir SQL sekmesine tıklayın.

    Bu kod quickstartdb adlı boş bir veritabanı oluşturur ve sonra stok adlı bir örnek tablo oluşturur. Birkaç adım ekler, sonra da satırları okur. Update deyimiyle verileri değiştirir ve satırları yeniden okur. Son olarak bir satırı siler ve satırları yeniden okur.

    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;

    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);

    -- Read
    SELECT * FROM inventory;

    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;

    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    Ekran görüntüsünde, SQL Workbench’te örnek bir SQL kodu ve kod çalıştırıldıktan sonra oluşan çıktı gösterilmektedir.

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="Örnek SQL kodunu çalıştırmak için MySQL Workbench SQL Sekmesi":::

2. Örnek SQL Kodunu çalıştırmak için **SQL Dosyası** sekmesindeki araç çubuğunda bulunan şimşek simgesine tıklayın.
3. Sayfanın ortasındaki **Sonuç Izgarası** bölümünde üç sekme halinde sonuçların yer aldığına dikkat edin.
4. Sayfanın en altındaki **Çıktı** listesine dikkat edin. Her komutun durumu gösterilir.

Şimdi MySQL çalışma sunucusunu kullanarak MySQL için Azure veritabanı esnek sunucusuna bağlandınız ve SQL dilini kullanarak verileri sorguladınız.

## <a name="next-steps"></a>Sonraki adımlar
- [MySQL Için Azure veritabanı-esnek sunucu 'Da Aktarım Katmanı Güvenliği (TLS 1,2) kullanılarak şifrelenmiş bağlantı](./how-to-connect-tls-ssl.md).
- [MySQL Için Azure veritabanı esnek sunucusu 'Nda ağ](./concepts-networking.md)hakkında daha fazla bilgi edinin.
- [Azure Portal kullanarak MySQL Için Azure veritabanı esnek sunucu güvenlik duvarı kuralları oluşturun ve yönetin](./how-to-manage-firewall-portal.md).
- [Azure Portal kullanarak MySQL Için Azure veritabanı esnek sunucu sanal ağını oluşturun ve yönetin](./how-to-manage-virtual-network-portal.md).
