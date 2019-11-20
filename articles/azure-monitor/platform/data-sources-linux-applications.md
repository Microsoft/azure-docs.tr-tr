---
title: Azure Izleyici 'de Linux uygulama performansını toplayın | Microsoft Docs
description: Bu makalede, MySQL ve Apache HTTP sunucusu için performans sayaçlarını toplamak üzere Linux için Log Analytics aracısının yapılandırılmasına ilişkin ayrıntılar sağlanmaktadır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 05/04/2017
ms.openlocfilehash: 60f09035f4aabcbd6348fb5608b812ca4b001b45
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932462"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Azure Izleyici 'de Linux uygulamaları için performans sayaçlarını toplama 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Bu makalede, Azure Izleyici 'de belirli uygulamalar için performans sayaçlarını toplamak üzere [Linux için Log Analytics aracısını](https://github.com/Microsoft/OMS-Agent-for-Linux) yapılandırmaya yönelik ayrıntılar sağlanmaktadır.  Bu makaleye dahil edilen uygulamalar şunlardır:  

- [MySQL](#mysql)
- [Apache HTTP sunucusu](#apache-http-server)

## <a name="mysql"></a>MySQL
Log Analytics Aracısı yüklendiğinde bilgisayar üzerinde MySQL Server veya MariaDB sunucusu algılanırsa, MySQL sunucusu için bir performans izleme sağlayıcısı otomatik olarak yüklenir. Bu sağlayıcı, performans istatistiklerini açığa çıkarmak için yerel MySQL/MariaDB sunucusuna bağlanır. Sağlayıcının MySQL sunucusuna erişebilmesi için MySQL Kullanıcı kimlik bilgilerinin yapılandırılması gerekir.

### <a name="configure-mysql-credentials"></a>MySQL kimlik bilgilerini yapılandırma
MySQL OMı sağlayıcısı, MySQL örneğinden performans ve sistem durumu bilgilerini sorgulamak için önceden yapılandırılmış bir MySQL kullanıcısı ve yüklü MySQL istemci kitaplıklarını gerektirir.  Bu kimlik bilgileri, Linux aracısında depolanan bir kimlik doğrulama dosyasında depolanır.  Kimlik doğrulama dosyası, MySQL örneğinin dinlediği bağlama adresini ve bağlantı noktasını ve ölçümleri toplamak için kullanılacak kimlik bilgilerini belirtir.  

Linux için Log Analytics aracısının yüklenmesi sırasında MySQL OMı sağlayıcısı, bağlama adresi ve bağlantı noktası için MySQL My. cnf yapılandırma dosyalarını (varsayılan konumlar) tarar ve MySQL OMı kimlik doğrulama dosyasını kısmen ayarlar.

MySQL kimlik doğrulama dosyası `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`depolanır.


### <a name="authentication-file-format"></a>Kimlik doğrulama dosyası biçimi
Aşağıda MySQL OMı kimlik doğrulama dosyasının biçimi verilmiştir

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Kimlik doğrulama dosyasındaki girişler aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:--|:--|
| Bağlantı noktası | MySQL örneğinin dinlediği geçerli bağlantı noktasını temsil eder. Bağlantı noktası 0, aşağıdaki özelliklerin varsayılan örnek için kullanıldığını belirtir. |
| Bağlama adresi| Geçerli MySQL bağlama adresi. |
| kullanıcı adı| MySQL Server örneğini izlemek için kullanılan MySQL kullanıcısı. |
| Base64 kodlamalı parola| Base64 Ile kodlanmış MySQL izleme kullanıcısının parolası. |
| Otomatik| MySQL OMı sağlayıcısı yükseltildiğinde, My. cnf dosyasındaki değişiklikler için yeniden tarama yapılıp yapılmayacağını ve MySQL OMı kimlik doğrulama dosyasının üzerine yazılmasına izin verilip verilmeyeceğini belirtir. |

### <a name="default-instance"></a>Varsayılan örnek
MySQL OMı kimlik doğrulama dosyası, bir Linux ana bilgisayarında birden çok MySQL örneğinin yönetilmesini kolaylaştırmak için varsayılan bir örnek ve bağlantı noktası numarası tanımlayabilir.  Varsayılan örnek, bağlantı noktası 0 olan bir örnek tarafından gösterilir. Tüm ek örnekler, farklı değerler belirtmedikleri takdirde varsayılan örnekten ayarlanan özellikleri devralacak. Örneğin, ' 3308 ' bağlantı noktasını dinleyen MySQL örneği eklendiyse, 3308 tarihinde dinleme yapan örneği denemek ve izlemek için varsayılan örnek için bağlama adresi, Kullanıcı adı ve Base64 kodlamalı parola kullanılacaktır. 3308 üzerindeki örnek başka bir adrese bağlıysa ve aynı MySQL Kullanıcı adını ve parola çiftini kullanıyorsa yalnızca bağlama adresi gereklidir ve diğer özellikler devralınır.

Aşağıdaki tabloda örnek örnek ayarları vardır 

| Açıklama | Dosya |
|:--|:--|
| 3308 numaralı bağlantı noktasına sahip varsayılan örnek ve örnek. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| 3308 bağlantı noktası ve farklı Kullanıcı adı ve parolasıyla varsayılan örnek ve örnek. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMı kimlik doğrulama dosyası programı
MySQL OMı sağlayıcısı 'nın yüklenmesiyle birlikte, MySQL OMI kimlik doğrulama dosyasını düzenlemek için kullanılabilen bir MySQL OMı kimlik doğrulama dosyası programıdır. Kimlik doğrulama dosyası programı aşağıdaki konumda bulunabilir.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Kimlik bilgileri dosyası omsagent hesabı tarafından okunabilir olmalıdır. Mycimprovauth komutunu omsgent olarak çalıştırmak önerilir.

Aşağıdaki tabloda, mycimprovauth kullanımı için sözdizimi hakkında ayrıntılı bilgi verilmektedir.

| İşlem | Örnek | Açıklama
|:--|:--|:--|
| *yanlış veya doğru* otomatik güncelleştirme | mycimprovauth otomatik güncelleştirme yanlış | Kimlik doğrulama dosyasının yeniden başlatma veya güncelleştirme sırasında otomatik olarak güncelleştirilip güncelleştirimeyeceğini ayarlar. |
| Varsayılan *bağlama adresi Kullanıcı adı parolası* | mycimprovauth varsayılan 127.0.0.1 kök PWD | MySQL OMı kimlik doğrulama dosyasındaki varsayılan örneği ayarlar.<br>Parola alanı düz metin olarak girilmelidir-MySQL OMı kimlik doğrulama dosyasındaki parola temel 64 olarak kodlanır. |
| *varsayılan veya port_num* Sil | mycimprovauth 3308 | Belirtilen örneği varsayılan ya da bağlantı noktası numarasıyla siler. |
| Yardım | mycimprov yardımı | Kullanılacak komutların listesini yazdırır. |
| yazdırdığımda | mycimprov Yazdır | Okunması kolay bir MySQL OMı kimlik doğrulama dosyası yazdırır. |
| port_num *BIND-adres Kullanıcı adı parolasını* Güncelleştir | mycimprov güncelleştirmesi 3307 127.0.0.1 kök PWD | Belirtilen örneği güncelleştirir veya yoksa örneği ekler. |

Aşağıdaki örnek komutlar, localhost üzerinde MySQL sunucusu için varsayılan bir kullanıcı hesabı tanımlar.  Parola alanı düz metin olarak girilmelidir-MySQL OMı kimlik doğrulama dosyasındaki parola temel 64 kodlanacak

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>MySQL performans sayaçları için gereken veritabanı Izinleri
MySQL kullanıcısının MySQL Server performans verilerini toplamak için aşağıdaki sorgulara erişmesi gerekir. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


MySQL kullanıcısı aynı zamanda aşağıdaki varsayılan tablolara erişim ' i de gerektirir.

- information_schema
- MySQL. 

Aşağıdaki verme komutları çalıştırılarak bu ayrıcalıklar verilebilir.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> MySQL izleme kullanıcısına izin vermek için, veren kullanıcının ' ızın verme ' ayrıcalığının yanı sıra verilen ayrıcalığa sahip olması gerekir.

### <a name="define-performance-counters"></a>Performans sayaçlarını tanımlama

Linux için Log Analytics Aracısı 'nı Azure Izleyici 'ye veri gönderecek şekilde yapılandırdıktan sonra, toplanacak performans sayaçlarını yapılandırmanız gerekir.  Aşağıdaki tablodaki sayaçlarla birlikte [Azure izleyici 'de Windows ve Linux performans verileri kaynakları](data-sources-performance-counters.md) yordamını kullanın.

| Nesne adı | Sayaç adı |
|:--|:--|
| MySQL Veritabanı | Bayt cinsinden disk alanı |
| MySQL Veritabanı | Tablolar |
| MySQL sunucusu | Kesilen bağlantı yüzdesi |
| MySQL sunucusu | Bağlantı kullanım yüzdesi |
| MySQL sunucusu | Bayt cinsinden disk alanı kullanımı |
| MySQL sunucusu | Tam tablo taraması yüzdesi |
| MySQL sunucusu | InnoDB arabellek havuzu Isabet yüzdesi |
| MySQL sunucusu | InnoDB arabellek havuzu kullanım yüzdesi |
| MySQL sunucusu | InnoDB arabellek havuzu kullanım yüzdesi |
| MySQL sunucusu | Anahtar Isabetli önbellek okuması yüzdesi |
| MySQL sunucusu | Anahtar önbelleği kullanım yüzdesi |
| MySQL sunucusu | Anahtar önbelleği yazma yüzdesi |
| MySQL sunucusu | Sorgu önbelleği Isabet yüzdesi |
| MySQL sunucusu | Sorgu önbelleği, PCT 'yi ayıklar |
| MySQL sunucusu | Sorgu önbelleği kullanım yüzdesi |
| MySQL sunucusu | Tablo önbelleği Isabet yüzdesi |
| MySQL sunucusu | Tablo önbelleği kullanım yüzdesi |
| MySQL sunucusu | Tablo kilit çakışması yüzdesi |

## <a name="apache-http-server"></a>Apache HTTP sunucusu 
Omsagent paketi yüklendiğinde bilgisayarda Apache HTTP sunucusu algılanırsa, Apache HTTP sunucusu için bir performans izleme sağlayıcısı otomatik olarak yüklenir. Bu sağlayıcı, performans verilerine erişmek için Apache HTTP sunucusuna yüklenmesi gereken bir Apache modülünü kullanır. Modül aşağıdaki komutla yüklenebilir:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Apache izleme modülünü kaldırmak için aşağıdaki komutu çalıştırın:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Performans sayaçlarını tanımlama

Linux için Log Analytics Aracısı 'nı Azure Izleyici 'ye veri gönderecek şekilde yapılandırdıktan sonra, toplanacak performans sayaçlarını yapılandırmanız gerekir.  Aşağıdaki tablodaki sayaçlarla birlikte [Azure izleyici 'de Windows ve Linux performans verileri kaynakları](data-sources-performance-counters.md) yordamını kullanın.

| Nesne adı | Sayaç adı |
|:--|:--|
| Apache HTTP sunucusu | Meşgul çalışanlar |
| Apache HTTP sunucusu | Boştaki çalışanlar |
| Apache HTTP sunucusu | Meşgul çalışanlar |
| Apache HTTP sunucusu | Toplam PCT CPU 'SU |
| Apache sanal ana bilgisayar | Dakika başına hata-Istemci |
| Apache sanal ana bilgisayar | Dakika başına hata-sunucu |
| Apache sanal ana bilgisayar | Istek başına KB |
| Apache sanal ana bilgisayar | Saniye başına istek KB sayısı |
| Apache sanal ana bilgisayar | İstek/saniye |



## <a name="next-steps"></a>Sonraki adımlar
* Linux aracılarından [performans sayaçlarını toplayın](data-sources-performance-counters.md) .
* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../log-query/log-query-overview.md) hakkında bilgi edinin. 