---
title: include dosyası
description: include dosyası
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 09/12/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c20159d0583e18d0f5e71152fdb600d03db43224
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "74005077"
---
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı oluşturma-hiper ölçek (Citus)

PostgreSQL için Azure veritabanı sunucusu oluşturmak üzere şu adımları uygulayın:
1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesine tıklayın.
2. **Yeni** sayfasından **Veritabanları**’nı seçin ve **Veritabanları** sayfasından **PostgreSQL için Azure Veritabanı**’nı seçin.
3. Dağıtım seçeneği için, **Hyperscale (Citus) sunucu grubu** altında **Oluştur** düğmesine tıklayın.
4. Yeni sunucu ayrıntıları formunu aşağıdaki bilgilerle doldurun:
   - Kaynak grubu: Bu alan için metin kutusunun altındaki **Yeni oluştur** bağlantısına tıklayın. **Myresourcegroup**gibi bir ad girin.
   - Sunucu grubu adı: sunucu alt etki alanı için de kullanılacak yeni sunucu grubu için benzersiz bir ad girin.
   - Yönetici Kullanıcı adı: Şu anda **citus**değeri olması gerekiyordu ve değiştirilemez.
   - Parola: en az sekiz karakter uzunluğunda olmalı ve şu kategorilerden üçünden (Ingilizce büyük harfler, Ingilizce küçük harfler, sayılar (0-9) ve alfasayısal olmayan karakterler (!, $, #,%, vb.) karakter içermelidir.
   - Konum: verilere en hızlı erişim sağlamak için kullanıcılarınıza en yakın konumu kullanın.

   > [!IMPORTANT]
   > Burada belirttiğiniz sunucu yöneticisi parolası, sunucuda ve veritabanlarında oturum açmak için gereklidir. Bu bilgileri daha sonra kullanmak üzere aklınızda tutun veya kaydedin.

5. **Sunucu grubunu yapılandır**öğesine tıklayın. Bu bölümdeki ayarları değiştirmeden bırakın ve **Kaydet**' e tıklayın.
6. Ileri ' ye tıklayın, ekranın alt kısmındaki **ağ >** .

7. **Ağ** sekmesinde, **genel uç nokta** radyo düğmesine tıklayın.
   ![ortak uç nokta seçildi](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Bağlantı **+ geçerli ISTEMCI IP adresini ekle**' ye tıklayın.
   ![eklenen istemci IP](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > Azure PostgreSQL sunucusu, 5432 numaralı bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 5432 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda BT departmanınız 5432 numaralı bağlantı noktasını açmadığı sürece Azure SQL Veritabanı sunucunuza bağlanamazsınız.
   >

9. Sunucuyu sağlamak için **gözden geçir + oluştur** ' a ve ardından **Oluştur** ' a tıklayın. Sağlama birkaç dakika sürer.
10. Sayfa dağıtımı izlemeye yeniden yönlendirilir. **Dağıtımınızdan** canlı durum değişikliği **dağıtımınız tamamlandığında**, sayfanın solundaki **çıktılar** menü öğesine tıklayın.
11. Çıktılar sayfası, değeri panoya kopyalamak için düğmenin yanında bir düğme içeren bir düzenleyici ana bilgisayar adı içerecektir. Bu bilgileri daha sonra kullanmak üzere kaydedin.

## <a name="connect-to-the-database-using-psql"></a>Psql kullanarak veritabanına bağlanma

PostgreSQL için Azure veritabanı sunucusunu oluştururken, **citus** adlı varsayılan bir veritabanı oluşturulur. Veritabanı sunucunuza bağlanmak için bir bağlantı dizesi ve yönetici parolası gerekir.

1. Bağlantı dizesini edinin. Sunucu grubu sayfasında **bağlantı dizeleri** menü öğesine tıklayın. ( **Ayarlar**altında.) İşaretlenmiş  **C++ dizeyi (libpq)** bulun. Şu biçimdedir:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Dizeyi kopyalayın. "{\_Password}" öğesini, daha önce seçtiğiniz yönetici parolasıyla değiştirmeniz gerekir. Sistem düz metin parolanızı depolamaz, bağlantı dizesinde sizin için görüntüleyemez.

2. Yerel bilgisayarınızda bir Terminal penceresi açın.

3. İstemde, [psql](https://www.postgresql.org/docs/current/app-psql.html) yardımcı programıyla PostgreSQL Için Azure veritabanı sunucunuza bağlanın. Bağlantı dizenizi tırnak içine geçirin ve parolanızı içerdiğinden emin olun:
   ```bash
   psql "{connection_string}"
   ```

   Örneğin, aşağıdaki komut, **demosunucum**sunucu grubunun düzenleyici düğümüne bağlanır:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
