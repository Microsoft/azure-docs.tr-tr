---
title: Yönetilen örnek saat dilimleri
description: Azure SQL veritabanı yönetilen örneği 'nin saat dilimi özellikleri hakkında bilgi edinin
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: 0dbed3db8e106b9bfe1b48ff2b9bc52840fc4c3a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818861"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Azure SQL veritabanı yönetilen örneği 'nde saat dilimleri

Eşgüdümlü Evrensel Saat (UTC), bulut çözümlerinin veri katmanı için önerilen saat bölgesidir. Azure SQL veritabanı yönetilen örneği Ayrıca, tarih ve saat değerlerini depolayan ve Tarih ve saat işlevlerini belirli bir saat diliminin örtük bağlamıyla çağıran mevcut uygulamaların ihtiyaçlarını karşılamak için bir saat dilimi seçimi sunmaktadır.

[GETDATE ()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) veya clr Code gibi T-SQL işlevleri örnek düzeyinde ayarlanan saat dilimini gözlemler. SQL Server Agent işler, örneğin zaman dilimine göre zamanlamaları da izler.

  >[!NOTE]
  > Yönetilen örnek, Azure SQL veritabanı 'nın saat dilimi ayarını destekleyen tek dağıtım seçeneğidir. Diğer dağıtım seçenekleri her zaman UTC 'yi izler.
Tarih ve saat bilgilerini UTC dışı bir saat diliminde yorumlayabilmeniz gerekiyorsa, tek ve havuza alınmış SQL veritabanlarındaki [saat dilimini](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) kullanın.

## <a name="supported-time-zones"></a>Desteklenen saat dilimleri

Desteklenen bir saat dilimi kümesi, yönetilen örneğin temeldeki işletim sisteminden devralınır. Yeni saat dilimi tanımlarını almak ve var olan değişiklikleri yansıtmak için düzenli olarak güncelleştirilir.

[Günışığından yararlanma zaman/saat dilimi değişiklikleri ilkesi](https://aka.ms/time) , 2010 ileri 'den geçmiş doğruluğu garanti eder.

Desteklenen saat dilimlerinin adlarına sahip bir liste, [sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) sistem görünümü aracılığıyla sunulur.

## <a name="set-a-time-zone"></a>Saat dilimi ayarlama

Yönetilen bir örneğin saat dilimi, yalnızca örnek oluşturma sırasında ayarlanabilir. Varsayılan saat dilimi UTC 'dir.

  >[!NOTE]
  > Mevcut bir yönetilen Örneğin saat dilimi değiştirilemez.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Saat dilimini Azure portal ile ayarlama

Yeni bir örnek için parametreler girdiğinizde, desteklenen saat dilimleri listesinden bir saat dilimi seçin.
  
![Örnek oluşturma sırasında bir saat dilimini ayarlama](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu

Örnek oluşturma sırasında saat dilimini ayarlamak için [Kaynak Yöneticisi şablonunuzda](https://aka.ms/sql-mi-create-arm-posh) TimeZoneId özelliğini belirtin.

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

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) işlevi, örneğin saat diliminin görünen adını döndürür.

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

- Mevcut yönetilen Örneğin saat dilimi değiştirilemez.
- SQL Server Agent işlerden başlatılan dış işlemler, örneğin saat dilimini gözlemlemektir.

## <a name="list-of-supported-time-zones"></a>Desteklenen saat dilimlerinin listesi

| **Saat dilimi KIMLIĞI** | **Saat dilimi görünen adı** |
| --- | --- |
| Tarih çizgisi standart saati | (UTC-12:00) Uluslararası Tarih Çizgisi Batı |
| UTC-11 | (UTC-11:00) Eşgüdümlü Evrensel Saat-11 |
| Aleutian standart saati | (UTC-10:00) Aleutian Adaları |
| Hawaii dili standart saati | (UTC-10:00) Hawaii |
| Marquesas standart saati | (UTC-09:30) Marquesas Adaları |
| Standart saat | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Eşgüdümlü Evrensel Saat-09 |
| Pasifik Standart Saati (Meksika) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Eşgüdümlü Evrensel Saat-08 |
| Pasifik standart saati | (UTC-08:00) Pasifik Saati (ABD ve Kanada) |
| ABD Sıradağlar Standart Saati | (UTC-07:00) Arizona |
| Sıradağlar Standart Saati (Meksika) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Sıradağlar Standart Saati | (UTC-07:00) Sıradağlar Saati (ABD ve Kanada) |
| Orta Amerika Standart Saati | (UTC-06:00) Orta Amerika |
| Merkezi Standart Saati | (UTC-06:00) Merkezi Saat (ABD ve Kanada) |
| Paskalya Adası standart saati | (UTC-06:00) Paskalya Adası |
| Merkezi Standart Saati (Meksika) | (UTC-06:00) Guadalahara, Mexico City, Monterey |
| Kanada Orta standart saati | (UTC-06:00) Saskatchewan |
| GA Pasifik standart saati | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Doğu Standart Saati (Meksika) | (UTC-05:00) Chetumal |
| Doğu Standart Saati | (UTC-05:00) Doğu Saati (ABD ve Kanada) |
| Haiti standart saati | (UTC-05:00) Haiti |
| Küa standart saati | (UTC-05:00) Havana |
| ABD Doğu Standart Saati | (UTC-05:00) Indiana (Doğu) |
| Turks ve Caicos standart saati | (UTC-05:00) Turks ve Caicos |
| Paraguay standart saati | (UTC-04:00) Asuncion |
| Atlantik standart saati | (UTC-04:00) Atlantik Saati (Kanada) |
| Venezuela standart saati | (UTC-04:00) Karakas |
| Orta Brezilya Standart Saati | (UTC-04:00) Cuiaba |
| GA Batı standart saati | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Pasifik GA standart saati | (UTC-04:00) Santiago |
| Newfoundland standart saati | (UTC-03:30) Newfoundland |
| Tocantins standart saati | (UTC-03:00) Araguaina |
| E. Güney Amerika Standart Saati | (UTC-03:00) Brezilya |
| GA Doğu Standart Saati | (UTC-03:00) Cayenne, Fortaleza |
| Arjantin Standart Saati | (UTC-03:00) Buenos Aires |
| Grönland standart saati | (UTC-03:00) Grönland |
| Montevideo standart saati | (UTC-03:00) Montevideo |
| Magalkulvarlar standart saati | (UTC-03:00) Punta Arenas |
| Saint Pierre standart saati | (UTC-03:00) Saint Pierre ve Miquelon |
| Bahia standart saati | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Eşgüdümlü Evrensel Saat-02 |
| Orta Atlantik standart saati | (UTC-02:00) Orta Atlantik - Eski |
| Azor Adaları standart saati | (UTC-01:00) Azor Adaları |
| Cabo Verde standart saati | (UTC-01:00) Cape Verde Adaları |
| UTC | (UTC) Eşgüdümlü Evrensel Saat |
| GMT standart saati | (UTC+00:00) Dublin, Edinburgh, Lizbon, Londra |
| Greenwich Standart Saati | (UTC+00:00) Monrovya, Reykjavik |
| Anlatımı. Avrupa standart saati | (UTC+01:00) Amsterdam, Berlin, Bern, Roma, Stockholm, Viyana |
| Orta Avrupa standart saati | (UTC+01:00) Belgrat, Bratislava, Budapeşte Ljubljana, Prag |
| Romanya standart saati | (UTC+01:00) Brüksel, Kopenhag, Madrid, Paris |
| Fas standart saati | (UTC + 01:00) Kazablanka |
| Sao Tome standart saati | (UTC + 01:00) Sao Tome |
| Orta Avrupa standart saati | (UTC+01:00) Saraybosna, Üsküp, Varşova, Zagreb |
| Anlatımı. Orta Afrika standart saati | (UTC+01:00) Orta Batı Afrika |
| Ürdün Standart Saati | (UTC+02:00) Amman |
| GTB standart saati | (UTC+02:00) Atina, Reykjavik |
| Orta Doğu Standart Saati | (UTC+02:00) Beyrut |
| Mısır standart saati | (UTC+02:00) Kahire |
| E. Avrupa standart saati | (UTC+02:00) Chisinau |
| Suriye standart saati | (UTC+02:00) Şam |
| Batı Bankası standart saati | (UTC+02:00) Gazze, Hebron |
| Güney Afrika standart saati | (UTC+02:00) Harare, Pretoria |
| FLE standart saati | (UTC+02:00) Helsinki, Kiev, Riga, Sofya, Tallinn, Vilnius |
| İsrail standart saati | (UTC+02:00) Kudüs |
| Kaliningrad standart saati | (UTC+02:00) Kaliningrad |
| Sudan standart saati | (UTC + 02:00) Hartum |
| Libya standart saati | (UTC+02:00) Tripoli |
| Namibia Standart Saati | (UTC + 02:00) Wınbir Hoek |
| Arap standart saati | (UTC+03:00) Bağdat |
| Türkiye standart saati | (UTC + 03:00) Istanbul |
| Arap standart saati | (UTC+03:00) Kuveyt, Riyad |
| Belarus standart saati | (UTC+03:00) Minsk |
| Rusça standart saati | (UTC + 03:00) Moskova, St. Petersburg |
| E. Afrika standart saati | (UTC+03:00) Nairobi |
| Iran Standart Saati | (UTC+03:30) Tahran |
| Arabistan standart saati | (UTC+04:00) Abu Dabi, Muscat |
| Astrahan standart saati | (UTC+04:00) Astrahan, Ulyanovsk |
| Azerbaycan standart saati | (UTC+04:00) Bakü |
| Rusya zaman Bölge 3 | (UTC+04:00) İjevsk, Samara |
| Mauritius Standart Saati | (UTC+04:00) Port Louis |
| Saratov standart saati | (UTC + 04:00) Saratov |
| Gürcüce standart saati | (UTC+04:00) Tiflis |
| Volgograd standart saati | (UTC + 04:00) VO |
| Kafkasya standart saati | (UTC+04:00) Erivan |
| Afganistan standart saati | (UTC+04:30) Kabil |
| Batı Asya Standart Saati | (UTC+05:00) Aşkabad, Taşkent |
| Ekaterinburg standart saati | (UTC+05:00) Ekaterinburg |
| Pakistan standart saati | (UTC+05:00) İslamabat, Karaçi |
| Hindistan standart saati | (UTC+05:30) Chennai, Kalküta, Mumbai, Yeni Delhi |
| Sri Lanka standart saati | (UTC+05:30) Sri Jayawardenepura |
| Nepal standart saati | (UTC+05:45) Kathmandu |
| Orta Asya Standart Saati | (UTC+06:00) Astana |
| Bangladeş standart saati | (UTC+06:00) Dakka |
| Omsk standart saati | (UTC + 06:00) Omsk |
| Myanmar Standart Saati | (UTC+06:30) Yangon (Rangoon) |
| Doğu Asya Standart Saati | (UTC+07:00) Bangkok, Hanoi, Cakarta |
| Alday standart saati | (UTC+07:00) Barnaul, Gorno-Altaysk |
| Anlatımı. Moğolistan standart saati | (UTC+07:00) Hovd |
| Kuzey Asya Standart Saati | (UTC+07:00) Krasnoyarsk |
| No. Orta Asya Standart Saati | (UTC + 07:00) Novosibirsk |
| Tomsk standart saati | (UTC+07:00) Tomsk |
| Çin standart saati | (UTC+08:00) Pekin, Chongqing, Hong Kong, Urumçi |
| Kuzey Asya Doğu Standart Saati | (UTC+08:00) Irkutsk |
| Singapur standart saati | (UTC+08:00) Kuala Lumpur, Singapur |
| Anlatımı. Avustralya Standart Saati | (UTC+08:00) Pert |
| Taipei standart saati | (UTC+08:00) Taipei |
| Ulanbator standart saati | (UTC+08:00) Ulanbator |
| Orta Batı Batı standart saati | (UTC+08:45) Eucla |
| Transbaıkal standart saati | (UTC+09:00) Chita |
| Tokyo standart saati | (UTC+09:00) Osaka, Sapporo, Tokyo |
| Kuzey Kore standart saati | (UTC + 09:00) Pyeongyang |
| Kore standart saati | (UTC+09:00) Seul |
| Yakutsk standart saati | (UTC+09:00) Yakutsk |
| Orta. Avustralya Standart Saati | (UTC+09:30) Adelaide |
| Orta Avustralya Standart Saati | (UTC+09:30) Darwin |
| E. Avustralya Standart Saati | (UTC+10:00) Brisbane |
| Avustralya Doğu Standart Saati | (UTC+10:00) Kanbera, Melbourne, Sidney |
| Batı Pasifik standart saati | (UTC+10:00) Guam, Port Moresby |
| Tazmanya standart saati | (UTC+10:00) Hobart |
| Vladivostok Standart Saati | (UTC+10:00) Vladivostok |
| Lord Howe standart saati | (UTC+10:30) Lord Howe Adası |
| Bugane ille standart saati | (UTC+11:00) Bougainville Adası |
| Rusya saat dilimi 10 | (UTC+11:00) Chokurdakh |
| Magadan Standart Saati | (UTC+11:00) Magadan |
| Norfolk standart saati | (UTC+11:00) Norfolk Adası |
| Sakhalin standart saati | (UTC+11:00) Sakhalin |
| Orta Pasifik standart saati | (UTC+11:00) Solomon Adaları, Yeni Kaledonya |
| Rusya saat dilimi 11 | (UTC+12:00) Anadır, Petropavlovsk-Kamçatski |
| Yeni Zelanda standart saati | (UTC+12:00) Auckland, Wellington |
| UTC + 12 | (UTC+12:00) Eşgüdümlü Evrensel Saat+12 |
| Fiji standart saati | (UTC+12:00) Fiji |
| Kamçatstandart saati | (UTC+12:00) Petropavlovsk-Kamchatsky - Eski |
| Chatham Adaları standart saati | (UTC+12:45) Chatham Adaları |
| UTC + 13 | (UTC + 13:00) Eşgüdümlü Evrensel Saat + 13 |
| Tonga standart saati | (UTC+13:00) Nuku'alofa |
| Samoa standart saati | (UTC+13:00) Samoa |
| Line Adaları standart saati | (UTC+14:00) Kiritimati Adası |

## <a name="see-also"></a>Ayrıca bkz. 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT saat dılımı (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys. time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
