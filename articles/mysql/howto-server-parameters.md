---
title: Sunucu parametrelerini Yapılandırma-Azure portal-MySQL için Azure veritabanı
description: Bu makalede, Azure portal kullanarak MySQL için Azure veritabanı 'nda MySQL Server parametrelerinin nasıl yapılandırılacağı açıklanır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 363be8b34f230b812bc24276e1f3925faf0cdc1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540850"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure portal kullanarak MySQL için Azure veritabanı 'nda sunucu parametrelerini yapılandırma

MySQL için Azure veritabanı bazı sunucu parametrelerinin yapılandırılmasını destekler. Bu makalede Azure portal kullanılarak bu parametrelerin nasıl yapılandırılacağı açıklanır. Tüm sunucu parametreleri ayarlanamaz.

>[!Note]
> Sunucu parametreleri sunucu düzeyinde genel olarak güncelleştirilebilir; [Azure CLI](./howto-configure-server-parameters-using-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) veya [Azure Portal](./howto-server-parameters.md) kullanın.

## <a name="configure-server-parameters"></a>Sunucu parametrelerini yapılandırma

1. [Azure Portal](https://portal.azure.com)oturum açın ve ardından MySQL Server Için Azure veritabanınızı bulun.
2. **Ayarlar** bölümünde **sunucu parametreleri** ' ne tıklayarak MySQL için Azure veritabanı sunucusu için sunucu parametreleri sayfasını açın.
:::image type="content" source="./media/howto-server-parameters/auzre-portal-server-parameters.png" alt-text="Azure portal sunucu parametreleri sayfası":::
3. Ayarlamanız gereken ayarları bulun. Amacı ve izin verilen değerleri anlamak için **Açıklama** sütununu gözden geçirin.
:::image type="content" source="./media/howto-server-parameters/3-toggle_parameter.png" alt-text="Açılan listesini numaralandır":::
4. Değişikliklerinizi kaydetmek için  **Kaydet** ' e tıklayın.
:::image type="content" source="./media/howto-server-parameters/4-save_parameters.png" alt-text="Değişiklikleri kaydetme veya atma":::
5. Parametreler için yeni değerler kaydettiyseniz, **Tümünü Sıfırla**' yı seçerek her şeyi varsayılan değerlere geri döndürebilirsiniz.
:::image type="content" source="./media/howto-server-parameters/5-reset_parameters.png" alt-text="Tümünü Varsayılana sıfırla":::

## <a name="setting-parameters-not-listed"></a>Ayar parametreleri listelenmedi

Güncelleştirmek istediğiniz sunucu parametresi Azure portal listelenmemişse, isteğe bağlı olarak, parametresini kullanarak bağlantı düzeyinde ayar yapabilirsiniz `init_connect` . Bu, sunucuya bağlanan her istemci için sunucu parametrelerini ayarlar. 

1. **Ayarlar** bölümünde **sunucu parametreleri** ' ne tıklayarak MySQL için Azure veritabanı sunucusu için sunucu parametreleri sayfasını açın.
2. Ara `init_connect`
3. Sunucu parametrelerini şu biçimde ekleyin: değer `SET parameter_name=YOUR_DESIRED_VALUE` sütununda değer sütunu.

    Örneğin, için ayarını yaparak sunucunuzun karakter kümesini değiştirebilirsiniz. `init_connect``SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. **Kaydet**’a tıklayarak değişikliklerinizi kaydedin.

>[!Note]
> `init_connect`, SÜPER ayrıcalıklar gerektirmeyen parametreleri oturum düzeyinde değiştirmek için kullanılabilir. Parametreyi `init_connect` kullanarak ayarlayıp ayarlayamayacağınızı doğrulamak için `set session parameter_name=YOUR_DESIRED_VALUE;` komutunu yürütün. **Erişim reddedildi; SÜPER ayrıcalıklar gerekiyor** hatası alırsanız ilgili parametreyi "init_connect" kullanarak ayarlayamazsınız.

## <a name="working-with-the-time-zone-parameter"></a>Saat dilimi parametresiyle çalışma

### <a name="populating-the-time-zone-tables"></a>Saat dilimi tablolarını doldurma

Sunucunuzdaki saat dilimi tabloları, `mysql.az_load_timezone` MySQL komut satırı veya MySQL çalışma ekranı gibi bir araçtan saklı yordam çağırarak doldurulabilir.

> [!NOTE]
> `mysql.az_load_timezone`MySQL çalışma modundan komutunu çalıştırıyorsanız, önce kullanarak güvenli güncelleştirme modunu kapatmanız gerekebilir `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Saat dilimi tablolarının doğru doldurulduğundan emin olmak için sunucuyu yeniden başlatmanız gerekir. Sunucuyu yeniden başlatmak için [Azure Portal](howto-restart-server-portal.md) veya [CLI](howto-restart-server-cli.md)kullanın.

Kullanılabilir saat dilimi değerlerini görüntülemek için aşağıdaki komutu çalıştırın:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Genel düzey saat dilimini ayarlama

Genel düzey saat dilimi Azure portal **sunucu parametreleri** sayfasından ayarlanabilir. Aşağıdaki, genel saat dilimini "ABD/Pasifik" değerine ayarlar.

:::image type="content" source="./media/howto-server-parameters/timezone.png" alt-text="Saat dilimi parametresini ayarla":::

### <a name="setting-the-session-level-time-zone"></a>Oturum düzeyi saat dilimini ayarlama

Oturum düzeyi saat dilimi, `SET time_zone` MySQL komut satırı veya MySQL çalışma ekranı gibi bir araçtan komutu çalıştırılarak ayarlanabilir. Aşağıdaki örnek saat dilimini **ABD/Pasifik** saati dilimine göre ayarlar.

```sql
SET time_zone = 'US/Pacific';
```

[Tarih ve saat işlevleri](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)için MySQL belgelerine bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [MySQL Için Azure veritabanı bağlantı kitaplıkları](concepts-connection-libraries.md).
