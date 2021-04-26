---
title: Azure HDInsight 'ta Ranger ve Apache ambarı 'nda LDAP eşitlemesi
description: Ranger ve ambarı 'nda LDAP eşitlemesini çözün ve genel yönergeler sağlayın.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fb9035b4d816c1af84b15e6865335aa1bdf86f5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933415"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight 'ta Ranger ve Apache ambarı 'nda LDAP eşitlemesi

HDInsight Kurumsal Güvenlik Paketi (ESP) kümeleri yetkilendirme için Ranger kullanır. Apache ambarı ve Ranger, kullanıcıları ve grupları bağımsız olarak eşitler ve biraz farklı çalışır. Bu makale, Ranger ve ambarı 'nda LDAP eşitlemesini ele almak için tasarlanmıştır.

## <a name="general-guidelines"></a>Genel yönergeler

* Kümeleri her zaman bir veya daha fazla grup ile dağıtın.
* Kümede daha fazla grup kullanmak istiyorsanız, Azure Active Directory (Azure AD) ' de grup üyeliklerini güncelleştirmek için bir anlamlı olup olmadığını denetleyin.
* Küme gruplarını değiştirmek istiyorsanız, ambarı kullanarak eşitleme filtrelerini değiştirebilirsiniz.
* Azure AD 'deki tüm grup üyeliği değişiklikleri kümeye sonraki eşitlemeler içinde yansıtılır. Değişikliklerin önce Azure AD Domain Services (Azure AD DS) ve sonra kümelere eşitlenmesi gerekir.
* HDInsight kümeleri, küme düğümlerinde grup üyeliklerini proje için Samba/winbind kullanır.
* Grup üyeleri, hem ambarı hem de Ranger için geçişli (tüm alt gruplar ve bunların üyeleri) eşitlenmiş olarak eşitlenir. 

## <a name="users-are-synced-separately"></a>Kullanıcılar ayrı olarak eşitlenir

 * İki farklı amaca hizmet ettiğinden, ambarı ve Ranger Kullanıcı veritabanını paylaşmaz. 
   * Bir kullanıcının, ambarı Kullanıcı ARABIRIMINI kullanması gerekiyorsa, kullanıcının ambarı ile eşitlenmesi gerekir. 
   * Kullanıcı ambarı ile eşitlenmemişse, ambarı kullanıcı ARABIRIMI/API 'SI reddeder, ancak sistemin diğer bölümleri çalışır (Bunlar, Sırasistemi tarafından değil, derecelendirmeden veya Kaynak Yöneticisi tarafından korunur).
   * Ranger ilkelerine Kullanıcı veya grup eklemek için, sorumluların Ranger 'da açıkça eşitlenmesi gerekir.

## <a name="ambari-user-sync-and-configuration"></a>Ambarı Kullanıcı eşitleme ve yapılandırma

Kullanıcı eşitlemesini zamanlamak için bir cron işi olan baş düğümlerden `/opt/startup_scripts/start_ambari_ldap_sync.py` her saat çalıştırılır. Cron işi, eşitlemeyi gerçekleştirmek için, ambarı REST API 'Lerini çağırır. Betik, eşitlenecek Kullanıcı ve grupların bir listesini gönderir (kullanıcılar belirtilen gruplara ait olmayabilir, her ikisi de ayrı olarak belirtilir). Ambarı, sAMAccountName öğesini Kullanıcı adı ve tüm grup üyeleri olarak eşitler, geçişli.

Günlüklerin içinde olması gerekir `/var/log/ambari-server/ambari-server.log` . Daha fazla bilgi için bkz. [ambarı günlüğü düzeyini yapılandırma](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

Data Lake kümelerinde, Kullanıcı sonrası oluşturma kancası, eşitlenen kullanıcılar için giriş klasörleri oluşturmak için kullanılır ve giriş klasörlerinin sahibi olarak ayarlanır. Kullanıcı doğru şekilde ambarla eşitlenmemişse, giriş klasörü doğru şekilde ayarlanmamasından dolayı Kullanıcı çalışan işleri hatalara açabilir.

## <a name="ranger-user-sync-and-configuration"></a>Ranger Kullanıcı eşitleme ve yapılandırma

Ranger, kullanıcıların eşitlenmesi için her saat çalışan yerleşik bir eşitleme motoruna sahiptir. Kullanıcı veritabanını ambarı ile paylaşmaz. HDInsight, yönetici kullanıcıyı, izleme kullanıcısını ve küme oluşturma sırasında belirtilen grubun üyelerini eşitlemek için arama filtresini yapılandırır. Grup üyeleri geçişli olarak eşitlenir:

1. Artımlı eşitlemeyi devre dışı bırakın.
1. Kullanıcı grubu eşitleme haritasını etkinleştirin.
1. Geçişli grup üyelerini dahil etmek için arama filtresi belirtin.
1. Kullanıcılar için sAMAccountName özniteliğini ve gruplar için Name özniteliğini eşitleyin.

### <a name="group-or-incremental-sync"></a>Grup veya artımlı eşitleme

Ranger bir grup eşitleme seçeneğini destekler, ancak grup üyelikleri ve Kullanıcı filtresi arasında birleşim olarak değil, Kullanıcı filtresiyle bir kesişim olarak da kullanılır. Ranger içindeki Grup eşitleme filtresi için tipik kullanım durumu-grup filtresi: (DN = clusteradmingroup), Kullanıcı filtresi: (City = Seattle).

Artımlı eşitleme yalnızca zaten eşitlenmiş olan kullanıcılar (ilk kez) için geçerlidir. Artımlı, ilk eşitlemeden sonra gruplara eklenen yeni kullanıcıları eşitmez.

### <a name="update-ranger-sync-filter"></a>Ranger eşitleme filtresini Güncelleştir

LDAP filtresi, ' ın Ranger Kullanıcı-eşitleme Yapılandırması bölümünün altında bulunan ambarı Kullanıcı arabiriminde bulunabilir. Mevcut filtre formda olacaktır `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Son sırada koşul ekleyip `net ads` arama komutunu veya ldp.exe veya benzer bir şeyi kullanarak filtreyi test edin.

## <a name="ranger-user-sync-logs"></a>Ranger Kullanıcı eşitleme günlükleri

Ranger Kullanıcı eşitlemesi, her iki yayın düğümünden de oluşabilir. Günlükler ' de bulunur `/var/log/ranger/usersync/usersync.log` . Günlüklerin ayrıntı düzeyini artırmak için aşağıdaki adımları uygulayın:

1. Ambarı 'nda oturum açın.
1. Ranger yapılandırma bölümüne gidin.
1. Gelişmiş **usersync-Log4J** bölümüne gidin.
1. Öğesini `log4j.rootLogger` olarak değiştirin `DEBUG` . (Değiştirildikten sonra, şöyle görünmelidir `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. Yapılandırmayı kaydedin ve Ranger 'ı yeniden başlatın.

## <a name="known-issues-with-ranger-user-sync"></a>Ranger Kullanıcı eşitlemeyle ilgili bilinen sorunlar
* Grup adında Unicode karakterler varsa, Ranger eşitlemesi bu nesneyi eşitleyemez. Bir kullanıcı uluslararası karakterler içeren bir gruba aitse, Ranger kısmi grup üyeliğini eşitler
* Kullanıcı adı (sAMAccountName) ve grup adı (ad) 20 karakter uzunluğunda veya daha az olmalıdır. Grup adı daha uzunsa, kullanıcılar, izinleri hesaplarken gruba ait olmadıkları gibi kabul edilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HDInsight 'ta kimlik doğrulama sorunları](./domain-joined-authentication-issues.md)
* [Azure AD kullanıcılarını HDInsight kümesine eşitleme](../hdinsight-sync-aad-users-to-cluster.md)
