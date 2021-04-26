---
title: Sunucu parametrelerini Yapılandırma-Azure portal-MySQL için Azure veritabanı esnek sunucu
description: Bu makalede, Azure portal kullanarak MySQL için Azure veritabanı esnek sunucusu 'nda MySQL sunucu parametrelerinin nasıl yapılandırılacağı açıklanır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 618862e12bd62fbe37ef5e621c89babd7942c04b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106964"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure portal kullanarak MySQL için Azure veritabanı 'nda sunucu parametrelerini yapılandırma-esnek sunucu

> [!IMPORTANT] 
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Sunucu parametrelerini kullanarak MySQL için Azure veritabanı esnek sunucu yapılandırması ' nı yönetebilirsiniz. Sunucuyu oluştururken sunucu parametreleri varsayılan ve önerilen değer ile yapılandırılır.  

Bu makalede, Azure portal kullanarak sunucu parametrelerinin nasıl görüntüleneceği ve yapılandırılacağı açıklanmaktadır. Azure portal sunucu parametresi dikey penceresi hem değiştirilebilir hem de değiştirilemeyen sunucu parametresini gösterir. Değiştirilemeyen sunucu parametreleri gri değildir.

>[!Note]
> Sunucu parametreleri genel olarak sunucu düzeyinde güncelleştirilemeyebilir, [Azure CLI](./how-to-configure-server-parameters-cli.md) veya [Azure Portal](./how-to-configure-server-parameters-portal.md)kullanabilirsiniz.

## <a name="configure-server-parameters"></a>Sunucu parametrelerini yapılandırma

1. [Azure Portal](https://portal.azure.com)oturum açın ve ardından MySQL Için Azure veritabanı esnek sunucusunu bulun.
2. **Ayarlar** bölümünde **sunucu parametreleri** ' ne tıklayarak MySQL için Azure veritabanı esnek sunucusu için sunucu parametreleri sayfasını açın.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Azure portal sunucu parametreleri sayfası":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Ayarlamanız gereken herhangi bir sunucu parametresini bulun. Amacı ve izin verilen değerleri anlamak için **Açıklama** sütununu gözden geçirin.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Açılan listesini numaralandır":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. Değişikliklerinizi kaydetmek için  **Kaydet** ' e tıklayın.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Değişiklikleri kaydetme veya atma":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. Statik parametreler, sunucu yeniden başlatmanın etkin olmasını gerektiren bir işlem olur. Statik parametreyi değiştiriyorsanız, **Şimdi yeniden başlatmanız** veya **daha sonra yeniden başlatmanız** istenir.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Statik parametre kaydederken yeniden Başlat":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. Parametreler için yeni değerler kaydettiyseniz, **Tümünü Sıfırla**' yı seçerek her şeyi varsayılan değerlere geri döndürebilirsiniz.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Tümünü Varsayılana sıfırla":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>Değiştirilemeyen sunucu parametreleri ayarlanıyor

Güncelleştirmek istediğiniz sunucu parametresi değiştirilebilir değilse, isteğe bağlı olarak parametresini kullanarak bağlantı düzeyinde ayarlayabilirsiniz `init_connect` . Bu, sunucuya bağlanan her istemci için sunucu parametrelerini ayarlar. 

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
> Saat dilimi tablolarının doğru doldurulduğundan emin olmak için sunucuyu yeniden başlatmanız gerekir.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

Kullanılabilir saat dilimi değerlerini görüntülemek için aşağıdaki komutu çalıştırın:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Genel düzey saat dilimini ayarlama

Genel düzey saat dilimi Azure portal **sunucu parametreleri** sayfasından ayarlanabilir. Aşağıdaki, genel saat dilimini "ABD/Pasifik" değerine ayarlar.

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Saat dilimi parametresini ayarla":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>Oturum düzeyi saat dilimini ayarlama

Oturum düzeyi saat dilimi, `SET time_zone` MySQL komut satırı veya MySQL çalışma ekranı gibi bir araçtan komutu çalıştırılarak ayarlanabilir. Aşağıdaki örnek saat dilimini **ABD/Pasifik** saati dilimine göre ayarlar.

```sql
SET time_zone = 'US/Pacific';
```

[Tarih ve saat işlevleri](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)için MySQL belgelerine bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CLI 'da sunucu parametrelerini](./how-to-configure-server-parameters-cli.md) yapılandırma
