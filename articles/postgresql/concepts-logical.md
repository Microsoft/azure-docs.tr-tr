---
title: Mantıksal kod çözme-PostgreSQL için Azure veritabanı-tek sunucu
description: PostgreSQL için Azure veritabanı 'nda değişiklik verilerini yakalama için mantıksal kod çözme ve wal2json açıklar-tek sunucu
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 0ea58050c5dc952392df56b4fb556a0998eef165
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96938911"
---
# <a name="logical-decoding"></a>Mantıksal kod çözme

[PostgreSQL Içindeki mantıksal kod çözme](https://www.postgresql.org/docs/current/logicaldecoding.html) , dış tüketicilerle veri değişiklikleri akışına olanak sağlar. Mantıksal kod çözme, olay akışı ve değişiklik verilerini yakalama senaryoları için popudöngüsel olarak kullanılır.

Mantıksal kod çözme, Postgres 'nin yazma sonrası günlüğünü (WAL) okunabilir bir biçime dönüştürmek için bir çıktı eklentisi kullanır. PostgreSQL için Azure veritabanı [wal2json](https://github.com/eulerto/wal2json), [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) ve pgoutput çıkış eklentilerini sağlar. pgoutput, PostgreSQL sürüm 10 ve üzerinde PostgreSQL tarafından kullanılabilir hale getirilir.

Postgres mantıksal kod çözmenin nasıl çalıştığına genel bakış için [blogumuzu ziyaret edin](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421). 

> [!NOTE]
> PostgreSQL yayını/aboneliğini kullanan mantıksal çoğaltma, PostgreSQL için Azure veritabanı-tek sunucu ile desteklenmez.


## <a name="set-up-your-server"></a>Sunucunuzu ayarlama 
Mantıksal kod çözme ve [okuma çoğaltmaları](concepts-read-replicas.md) her ikisi de bilgi Için Postgres yazma ilerme günlüğüne (Wal) bağlıdır. Bu iki özellik, Postgres 'den farklı günlük düzeylerine sahip olmalıdır. Mantıksal kod çözme, okuma Çoğaltmalarından daha yüksek bir günlüğe kaydetme düzeyine sahip olmalıdır.

Doğru günlük kaydını yapılandırmak için Azure çoğaltma desteği parametresini kullanın. Azure çoğaltma desteğinin üç ayar seçeneği vardır:

* **Kapalı** -en az bilgiyi Wal 'e yerleştirir. Bu ayar, çoğu PostgreSQL için Azure veritabanı sunucuları üzerinde kullanılamaz.  
* **Çoğaltma** -daha ayrıntılı bir **şekilde.** Bu, [okuma çoğaltmalarının](concepts-read-replicas.md) çalışması için gereken en düşük günlüğe kaydetme düzeyidir. Bu ayar, çoğu sunucuda varsayılandır.
* **Çoğaltmadan** daha ayrıntılı **mantıksal** . Bu, mantıksal kod çözmenin çalışması için en düşük günlük kayıt düzeyidir. Okuma çoğaltmaları bu ayarda de çalışır.


### <a name="using-azure-cli"></a>Azure CLI’yı kullanma

1. Azure.replication_support olarak ayarlayın `logical` .
   ```azurecli-interactive
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. Değişikliği uygulamak için sunucuyu yeniden başlatın.
   ```azurecli-interactive
   az postgres server restart --resource-group mygroup --name myserver
   ```
3. Postgres 9,5 veya 9,6 çalıştırıyorsanız ve ortak ağ erişimi kullanıyorsanız, mantıksal çoğaltmayı çalıştıracağınız istemcinin genel IP adresini dahil etmek için güvenlik duvarı kuralını ekleyin. Güvenlik duvarı kuralı adı **_replrule** içermelidir. Örneğin, *test_replrule*. Sunucuda yeni bir güvenlik duvarı kuralı oluşturmak için [az Postgres Server Firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) komutunu çalıştırın. 

### <a name="using-azure-portal"></a>Azure portalını kullanma

1. Azure çoğaltma desteğini **mantıksal** olarak ayarlayın. **Kaydet**’i seçin.

   :::image type="content" source="./media/concepts-logical/replication-support.png" alt-text="PostgreSQL için Azure veritabanı-çoğaltma-Azure çoğaltma desteği":::

2. **Evet**' i seçerek değişikliği uygulamak için sunucuyu yeniden başlatın.

   :::image type="content" source="./media/concepts-logical/confirm-restart.png" alt-text="PostgreSQL için Azure veritabanı-çoğaltma-yeniden başlatmayı Onayla":::

3. Postgres 9,5 veya 9,6 çalıştırıyorsanız ve ortak ağ erişimi kullanıyorsanız, mantıksal çoğaltmayı çalıştıracağınız istemcinin genel IP adresini dahil etmek için güvenlik duvarı kuralını ekleyin. Güvenlik duvarı kuralı adı **_replrule** içermelidir. Örneğin, *test_replrule*. Daha sonra **Kaydet**'e tıklayın.

   :::image type="content" source="./media/concepts-logical/client-replrule-firewall.png" alt-text="PostgreSQL için Azure veritabanı-çoğaltma-güvenlik duvarı kuralı ekle":::

## <a name="start-logical-decoding"></a>Mantıksal kod çözmeyi Başlat

Mantıksal kod çözme, akış protokolü veya SQL arabirimi aracılığıyla tüketilebilir. Her iki yöntem de [çoğaltma yuvaları](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)kullanır. Yuva, tek bir veritabanındaki değişikliklerin akışını temsil eder.

Çoğaltma yuvası kullanılması için Postgres 'nin çoğaltma ayrıcalıkları gerekir. Şu anda, çoğaltma ayrıcalığı yalnızca sunucunun yönetici kullanıcısı için kullanılabilir. 

### <a name="streaming-protocol"></a>Akış Protokolü
Akış protokolünü kullanan değişiklikleri kullanmak genellikle tercih edilir. Kendi tüketicisini/bağlayıcınızı oluşturabilir veya [Debezium](https://debezium.io/)gibi bir araç kullanabilirsiniz. 

[Pg_recvlogical ile akış protokolünü kullanarak bir örnek](https://github.com/eulerto/wal2json#pg_recvlogical)için wal2json belgelerini ziyaret edin.


### <a name="sql-interface"></a>SQL arabirimi
Aşağıdaki örnekte, SQL arabirimini wal2json eklentisi ile kullanıyoruz.
 
1. Yuva oluşturun.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL komutları verin. Örnek:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Değişiklikleri kullanın.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Çıktı şöyle görünür:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Onu kullanarak işiniz bittiğinde yuvayı atın.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>İzleme Yuvaları

Mantıksal kod çözmeyi izlemeniz gerekir. Kullanılmayan herhangi bir çoğaltma yuvası bırakılmalıdır. Yuvalar, değişiklikler bir tüketici tarafından okunana kadar Postgres WAL günlüklerine ve ilgili sistem kataloglarına sahiptir. Tüketicinizin başarısız olması veya düzgün bir şekilde yapılandırılmamışsa, tüketilmemiş Günlükler depolama alanını doldurup dolduracaktır. Ayrıca, tüketilmemiş Günlükler işlem KIMLIĞI wraparound riskini artırır. Her iki durum da sunucunun kullanılamaz hale gelmesine neden olabilir. Bu nedenle, mantıksal çoğaltma yuvalarının sürekli olarak tüketilmesi kritik öneme sahiptir. Mantıksal çoğaltma yuvası artık kullanılmıyorsa, hemen bırakın.

Pg_replication_slots görünümündeki ' etkin ' sütunu, bir yuvaya bağlı bir tüketici olup olmadığını gösterir.
```SQL
SELECT * FROM pg_replication_slots;
```

Değerler, geçmiş normal eşiklerini arttırabilmeniz için, *kullanılan depolamada* [uyarıları](howto-alert-on-metric.md) ve *çoğaltmalar ölçümleri genelinde en fazla gecikme* değerini ayarlayın. 

> [!IMPORTANT]
> Kullanılmayan çoğaltma yuvalarını bırakmalısınız. Bunun başarısız olması sunucunun kullanılamamasına yol açabilir.

## <a name="how-to-drop-a-slot"></a>Yuva bırakma
Bir çoğaltma yuvasını etkin bir şekilde kullanmıyorsanız bırakmalısınız.

SQL kullanılarak adlandırılan bir çoğaltma yuvasını bırakmak için `test_slot` :
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Mantıksal kod çözme kullanmayı durdurursanız azure.replication_support veya olarak değiştirin `replica` `off` . Tarafından tutulan WAL ayrıntıları `logical` daha ayrıntılıdır ve mantıksal kod çözme kullanımda olmadığında devre dışı bırakılmalıdır. 

 
## <a name="next-steps"></a>Sonraki adımlar

* [Mantıksal kod çözme hakkında daha fazla bilgi edinmek](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)Için Postgres belgelerini ziyaret edin.
* Mantıksal kod çözme hakkında sorularınız varsa [ekibimize](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) ulaşın.
* [Okuma çoğaltmaları](concepts-read-replicas.md)hakkında daha fazla bilgi edinin.

