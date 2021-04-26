---
title: Azure SQL yönetilen örnek saat dilimleri
description: Azure SQL yönetilen örneği 'nin saat dilimi özellikleri hakkında bilgi edinin
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 10/12/2020
ms.openlocfilehash: b4ba5f50f5c66b404ec76128a828a1060328f81c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101691000"
---
# <a name="time-zones-in-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği 'nde saat dilimleri
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Eşgüdümlü Evrensel Saat (UTC), bulut çözümlerinin veri katmanı için önerilen saat bölgesidir. Azure SQL yönetilen örneği Ayrıca tarih ve saat değerlerini depolayan ve Tarih ve saat işlevlerini belirli bir saat diliminin örtük bağlamıyla çağıran mevcut uygulamaların ihtiyaçlarını karşılamak için bir saat dilimi seçimi sunmaktadır.

[GETDATE ()](/sql/t-sql/functions/getdate-transact-sql) veya clr Code gibi T-SQL işlevleri örnek düzeyinde ayarlanan saat dilimini gözlemler. SQL Server Agent işler, örneğin zaman dilimine göre zamanlamaları da izler.

  > [!NOTE]
  > Azure SQL veritabanı, saat dilimi ayarlarını desteklemez; her zaman UTC 'yi izler. Tarih ve saat bilgilerini UTC dışı bir saat diliminde yorumlayabilmeniz gerekiyorsa SQL veritabanında [saat dilimini](/sql/t-sql/queries/at-time-zone-transact-sql) kullanın.

## <a name="supported-time-zones"></a>Desteklenen saat dilimleri

Desteklenen bir saat dilimi kümesi, yönetilen örneğin temeldeki işletim sisteminden devralınır. Yeni saat dilimi tanımlarını almak ve var olan değişiklikleri yansıtmak için düzenli olarak güncelleştirilir.

[Günışığından yararlanma zaman/saat dilimi değişiklikleri ilkesi](/troubleshoot/windows-client/system-management-components/daylight-saving-time-help-support) , 2010 ileri 'den geçmiş doğruluğu garanti eder.

Desteklenen saat dilimlerinin adlarına sahip bir liste [sys.time_zone_info](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) sistem görünümü aracılığıyla sunulur.

## <a name="set-a-time-zone"></a>Saat dilimi ayarlama

Yönetilen bir örneğin saat dilimi, yalnızca örnek oluşturma sırasında ayarlanabilir. Varsayılan saat dilimi UTC 'dir.

  >[!NOTE]
  > Mevcut bir yönetilen Örneğin saat dilimi değiştirilemez.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Saat dilimini Azure portal ile ayarlama

Yeni bir örnek için parametreler girdiğinizde, desteklenen saat dilimleri listesinden bir saat dilimi seçin.
  
![Örnek oluşturma sırasında bir saat dilimini ayarlama](./media/timezones-overview/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu

Örnek oluşturma sırasında saat dilimini ayarlamak için [Kaynak Yöneticisi şablonunuzda](./create-template-quickstart.md) TimeZoneId özelliğini belirtin.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

TimeZoneId özelliği için desteklenen değerlerin bir listesi Bu makalenin sonunda bulunur.

Belirtilmemişse, saat dilimi UTC olarak ayarlanır.

## <a name="check-the-time-zone-of-an-instance"></a>Bir örneğin saat dilimini denetleme

[CURRENT_TIMEZONE](/sql/t-sql/functions/current-timezone-transact-sql) işlevi, örneğin saat diliminin görünen adını döndürür.

## <a name="cross-feature-considerations"></a>Özellikler arası konular

### <a name="restore-and-import"></a>Geri yükleme ve içeri aktarma

Bir yedekleme dosyasını geri yükleyebilir veya verileri yönetilen bir örneğe veya farklı saat dilimi ayarlarına sahip bir sunucudan içeri aktarabilirsiniz. Bunu dikkatli bir şekilde yaptığınızdan emin olun. Farklı saat dilimi ayarlarına sahip iki SQL Server örneği arasında veri aktarırken olduğu gibi, uygulama davranışını ve sorgu ve raporların sonuçlarını çözümleyin.

### <a name="point-in-time-restore"></a>Belirli bir noktaya geri yükleme

Bir zaman içinde geri yükleme gerçekleştirdiğinizde geri yükleme süresi UTC saati olarak yorumlanır. Bu şekilde, gün ışığından yararlanma süresi ve olası değişiklikleri nedeniyle herhangi bir belirsizlikleri kaçınılmaz.

### <a name="auto-failover-groups"></a>Otomatik yük devretme grupları

Bir yük devretme grubundaki birincil ve ikincil bir örnek genelinde aynı saat dilimini kullanmak zorunlu değildir, ancak bunu kesinlikle öneririz.

  >[!WARNING]
  > Yük devretme grubundaki birincil ve ikincil örnek için aynı saat dilimini kullanmanızı önemle tavsiye ederiz. Birincil ve ikincil örneklerin tamamında aynı saat dilimini tutmanın nadir bazı kullanım durumları nedeniyle zorlanmaz. El ile veya otomatik yük devretme durumunda, ikincil örnek orijinal saat dilimini koruyacağını anlamak önemlidir.

## <a name="limitations"></a>Sınırlamalar

- Mevcut yönetilen Örneğin saat dilimi değiştirilemez. Geçici bir çözüm olarak, uygun saat dilimine sahip yeni bir yönetilen örnek oluşturun ve ardından el ile yedekleme ve geri yükleme gerçekleştirin ya da önerdiğimiz bir [süre içinde, bir çapraz örnek zaman geri yükleme](./point-in-time-restore.md?tabs=azure-portal#restore-an-existing-database)gerçekleştirin.
- SQL Server Agent işlerden başlatılan dış işlemler, örneğin saat dilimini gözlemlemektir.

## <a name="list-of-supported-time-zones"></a>Desteklenen saat dilimlerinin listesi

| **Saat dilimi KIMLIĞI** | **Saat dilimi görünen adı** |
| --- | --- |
| Tarih çizgisi standart saati | (UTC-12:00) Uluslararası Tarih çizgisi Batı |
| UTC-11 | (UTC-11:00) Eşgüdümlü Evrensel Saat-11 |
| Aleutian standart saati | (UTC-10:00) Aleutian Adaları |
| Hawaii dili standart saati | (UTC-10:00) Hawaii |
| Marquesas standart saati | (UTC-09:30) Marquesas Adaları |
| Standart saat | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Eşgüdümlü Evrensel Saat-09 |
| Pasifik Standart Saati (Meksika) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Eşgüdümlü Evrensel Saat-08 |
| Pasifik standart saati | (UTC-08:00) Pasifik saati (ABD & Kanada) |
| ABD Sıradağlar Standart Saati | (UTC-07:00) Arizona |
| Sıradağlar Standart Saati (Meksika) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Sıradağlar Standart Saati | (UTC-07:00) Sıradağlar Saati (ABD & Kanada) |
| Orta Amerika Standart Saati | (UTC-06:00) Orta Amerika |
| Merkezi Standart Saati | (UTC-06:00) Merkezi saat (ABD & Kanada) |
| Paskalya Adası standart saati | (UTC-06:00) Paskalya Adası |
| Merkezi Standart Saati (Meksika) | (UTC-06:00) Guadalahara, Mexico City, Monterey |
| Kanada Orta standart saati | (UTC-06:00) Saskatchewan |
| GA Pasifik standart saati | (UTC-05:00) Bogota, Lima, Tito, RIO Branco |
| Doğu Standart Saati (Meksika) | (UTC-05:00) Chetumal |
| Doğu Standart Saati | (UTC-05:00) Doğu saati (ABD & Kanada) |
| Haiti standart saati | (UTC-05:00) Haiti |
| Küa standart saati | (UTC-05:00) Havana |
| ABD Doğu Standart Saati | (UTC-05:00) Indiana (Doğu) |
| Turks ve Caicos standart saati | (UTC-05:00) Turks ve Caicos |
| Paraguay standart saati | (UTC-04:00) Asuzu aç |
| Atlantik standart saati | (UTC-04:00) Atlantik Saati (Kanada) |
| Venezuela standart saati | (UTC-04:00) Karakas |
| Orta Brezilya Standart Saati | (UTC-04:00) Cuiaba |
| GA Batı standart saati | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Pasifik GA standart saati | (UTC-04:00) Santiago |
| Newfoundland standart saati | (UTC-03:30) Newfoundland |
| Tocantins standart saati | (UTC-03:00) Araguaina |
| E. Güney Amerika Standart Saati | (UTC-03:00) Brezilya |
| GA Doğu Standart Saati | (UTC-03:00) Cayenne, Fortaleza |
| Arjantin Standart Saati | (UTC-03:00) Buenos Aonin şehri |
| Grönland standart saati | (UTC-03:00) Grönland |
| Montevideo standart saati | (UTC-03:00) Montevideo |
| Magalkulvarlar standart saati | (UTC-03:00) Punta Arenas |
| Saint Pierre standart saati | (UTC-03:00) Saint Pierre ve Miquelon |
| Bahia standart saati | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Eşgüdümlü Evrensel Saat-02 |
| Mid-Atlantic standart saati | (UTC-02:00) Mid-Atlantic-eski |
| Azor Adaları standart saati | (UTC-01:00) Azor Adaları |
| Cabo Verde standart saati | (UTC-01:00) Cabo Verde Adaları |
| UTC | (UTC) Eşgüdümlü Evrensel Saat |
| GMT standart saati | (UTC + 00:00) Dublin, Edinburgh, Lizbon, Londra |
| Greenwich Standart Saati | (UTC + 00:00) Monrovia, Reykjavik |
| Anlatımı. Avrupa standart saati | (UTC + 01:00) Amsterdam, Berlin, Bern, Roma, Stockholm, Viyana |
| Orta Avrupa standart saati | (UTC + 01:00) Belgrad, Bratislava, Budapeşte, Ljubljana, Prag |
| Romanya standart saati | (UTC + 01:00) Brüksel, Kopenhag, Madrid, Paris |
| Fas standart saati | (UTC + 01:00) Kazablanka |
| Sao Tome standart saati | (UTC + 01:00) Sao Tome |
| Orta Avrupa standart saati | (UTC + 01:00) Saraybosna, Üsküp, Varşova, Zagreb |
| Anlatımı. Orta Afrika standart saati | (UTC + 01:00) Batı Orta Afrika |
| Ürdün Standart Saati | (UTC + 02:00) Amman dili |
| GTB standart saati | (UTC + 02:00) Atina, Bukreş |
| Orta Doğu Standart Saati | (UTC + 02:00) Beyrut |
| Mısır standart saati | (UTC + 02:00) Cairo |
| E. Avrupa standart saati | (UTC + 02:00) Chisinau |
| Suriye standart saati | (UTC + 02:00) Şam |
| Batı Bankası standart saati | (UTC + 02:00) Gaza, Hebron |
| Güney Afrika standart saati | (UTC + 02:00) Hara, Pretoria |
| FLE standart saati | (UTC + 02:00) Helsinki, Kiev, Riga, Sofya, Tallinn, Vilnius |
| İsrail standart saati | (UTC + 02:00) Dü |
| Kaliningrad standart saati | (UTC + 02:00) Kaliningrad |
| Sudan standart saati | (UTC + 02:00) Hartum |
| Libya standart saati | (UTC + 02:00) Trablus |
| Namibia Standart Saati | (UTC + 02:00) Wınbir Hoek |
| Arap standart saati | (UTC + 03:00) Bağdat |
| Türkiye standart saati | (UTC + 03:00) Istanbul |
| Arap standart saati | (UTC + 03:00) Kuveyt, Riyad |
| Belarus standart saati | (UTC + 03:00) Minsk |
| Rusça standart saati | (UTC + 03:00) Moskova, St. Petersburg |
| E. Afrika standart saati | (UTC + 03:00) Nairobi |
| Iran Standart Saati | (UTC + 03:30) Tahran |
| Arabistan standart saati | (UTC + 04:00) Abu Dabi, Muscat |
| Astrahan standart saati | (UTC + 04:00) Astrahan, Ulyanovsk |
| Azerbaycan standart saati | (UTC + 04:00) Bakü |
| Rusya zaman Bölge 3 | (UTC + 04:00) İzhevsk, Samara |
| Mauritius Standart Saati | (UTC + 04:00) Bağlantı noktası Loumi |
| Saratov standart saati | (UTC + 04:00) Saratov |
| Gürcüce standart saati | (UTC + 04:00) Tiflis |
| Volgograd standart saati | (UTC + 04:00) VO |
| Kafkasya standart saati | (UTC + 04:00) Erivan |
| Afganistan standart saati | (UTC + 04:30) Kabil |
| Batı Asya Standart Saati | (UTC + 05:00) Ashgabat, Taşkent |
| Ekaterinburg standart saati | (UTC + 05:00) Ekaterinburg |
| Pakistan standart saati | (UTC + 05:00) Islamabad, Karaçi |
| Hindistan standart saati | (UTC + 05:30) Chennai, Kalküta, Mumbai, Yeni Delhi |
| Sri Lanka standart saati | (UTC + 05:30) Sri Jayawardenepura |
| Nepal standart saati | (UTC + 05:45) Katmandu |
| Orta Asya Standart Saati | (UTC + 06:00) Astana |
| Bangladeş standart saati | (UTC + 06:00) Dakka |
| Omsk standart saati | (UTC + 06:00) Omsk |
| Myanmar Standart Saati | (UTC + 06:30) Yangon (Rangoon) |
| Doğu Asya Standart Saati | (UTC + 07:00) Bangkok, Hanoi, Cakarta |
| Alday standart saati | (UTC + 07:00) Barnaul, Gorno-Altaysk |
| Anlatımı. Moğolistan standart saati | (UTC + 07:00) Hovd |
| Kuzey Asya Standart Saati | (UTC + 07:00) Krasnoyarsk |
| No. Orta Asya Standart Saati | (UTC + 07:00) Novosibirsk |
| Tomsk standart saati | (UTC + 07:00) Tomsk |
| Çin standart saati | (UTC + 08:00) Pekin, Chongqing, Hong Kong, Urumçi |
| Kuzey Asya Doğu Standart Saati | (UTC + 08:00) Irkutsk |
| Singapur standart saati | (UTC + 08:00) Kuala Lumpur, Singapur |
| Anlatımı. Avustralya Standart Saati | (UTC + 08:00) Perth |
| Taipei standart saati | (UTC + 08:00) Taipei |
| Ulanbator standart saati | (UTC + 08:00) Ulanbator |
| Orta Batı Batı standart saati | (UTC + 08:45) Eucla |
| Transbaıkal standart saati | (UTC + 09:00) Çıta |
| Tokyo standart saati | (UTC + 09:00) Osaka, Sapporo, Tokyo |
| Kuzey Kore standart saati | (UTC + 09:00) Pyeongyang |
| Kore standart saati | (UTC + 09:00) Seul |
| Yakutsk standart saati | (UTC + 09:00) Yakutsk |
| Orta. Avustralya Standart Saati | (UTC + 09:30) Adelaide |
| Orta Avustralya Standart Saati | (UTC + 09:30) Darwin |
| E. Avustralya Standart Saati | (UTC + 10:00) Brisbane |
| Avustralya Doğu Standart Saati | (UTC + 10:00) Kanbera, Melbourne, Sidney |
| Batı Pasifik standart saati | (UTC + 10:00) Guhar, bağlantı noktası Moresby |
| Tazmanya standart saati | (UTC + 10:00) Hobart |
| Vladivostok Standart Saati | (UTC + 10:00) Vladivostok |
| Lord Howe standart saati | (UTC + 10:30) Lord Howe Adası |
| Bugane ille standart saati | (UTC + 11:00) Bugane ille Adası |
| Rusya saat dilimi 10 | (UTC + 11:00) Chokurdakh |
| Magadan Standart Saati | (UTC + 11:00) Magadan |
| Norfolk standart saati | (UTC + 11:00) Norfolk Adası |
| Sakhalin standart saati | (UTC + 11:00) Sakhalin |
| Orta Pasifik standart saati | (UTC + 11:00) Solomon Adaları, Yeni Kaledonya |
| Rusya saat dilimi 11 | (UTC + 12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Yeni Zelanda standart saati | (UTC + 12:00) Auckland, kaynak |
| UTC + 12 | (UTC + 12:00) Eşgüdümlü Evrensel Saat + 12 |
| Fiji standart saati | (UTC + 12:00) Fiji |
| Kamçatstandart saati | (UTC + 12:00) Petropavlovsk-Kamchatsky-eski |
| Chatham Adaları standart saati | (UTC + 12:45) Chatham Adaları |
| UTC + 13 | (UTC + 13:00) Eşgüdümlü Evrensel Saat + 13 |
| Tonga standart saati | (UTC + 13:00) Nuku 'alofa |
| Samoa standart saati | (UTC + 13:00) Devleti |
| Line Adaları standart saati | (UTC + 14:00) Kiritimati Adası |

## <a name="see-also"></a>Ayrıca bkz. 

- [CURRENT_TIMEZONE (Transact-SQL)](/sql/t-sql/functions/current-timezone-transact-sql)
- [CURRENT_TIMEZONE_ID (Transact-SQL)](/sql/t-sql/functions/current-timezone-id-transact-sql)
- [AT saat dılımı (Transact-SQL)](/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)