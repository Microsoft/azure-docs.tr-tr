---
title: Linux VM üzerinde PostgreSQL ayarlama
description: Azure 'daki bir Linux sanal makinesine PostgreSQL yükleme ve yapılandırma hakkında bilgi edinin
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: f6d521c7003583228990c80a90c1454821f584d3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035259"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Azure’da PostgreSQL yükleme ve yapılandırma
PostgreSQL, Oracle ve DB2 ile benzer gelişmiş bir açık kaynaklı veritabanıdır. Tam ACID uyumluluğu, güvenilir işlem işleme ve çok sürümlü eşzamanlılık denetimi gibi kurumsal özellikli özellikler içerir. Ayrıca, ANSI SQL ve SQL/MED gibi standartları destekler (Oracle, MySQL, MongoDB için yabancı veri sarmalayıcıları dahil) ve diğer birçok kişi). 12 yordamsal diller, GıN ve GiST dizinleri, uzamsal veri desteği ve JSON ya da anahtar-değer tabanlı uygulamalar için birden çok NoSQL benzeri Özellik desteğiyle yüksek düzeyde genişletilebilir.

Bu makalede, Linux çalıştıran bir Azure sanal makinesine PostgreSQL yükleme ve yapılandırma hakkında bilgi edineceksiniz.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>PostgreSQL yükleme
> [!NOTE]
> Bu öğreticiyi tamamlayabilmeniz için zaten Linux çalıştıran bir Azure sanal makineniz olmalıdır. Devam etmeden önce bir Linux VM oluşturmak ve ayarlamak için bkz. [Azure LINUX VM öğreticisi](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Bu durumda, PostgreSQL bağlantı noktası olarak 1999 numaralı bağlantı noktasını kullanın.  

PuTTY aracılığıyla oluşturduğunuz Linux sanal makinesine bağlanın. Azure Linux VM 'yi ilk kez kullanıyorsanız, bir Linux sanal makinesine bağlanmak için PuTTY 'yi nasıl kullanacağınızı öğrenmek için bkz. [Azure 'Da Linux Ile SSH kullanma](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

1. Köke (yönetici) geçiş yapmak için aşağıdaki komutu çalıştırın:
   
        # sudo su -
2. Bazı dağıtımların PostgreSQL yüklemeden önce yüklemeniz gereken bağımlılıkları vardır. Bu listede sahip olup olmadığınızı denetleyin ve uygun komutu çalıştırın:
   
   * Red Hat Base Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Temel Linux 'u kaldırma:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. PostgreSQL ' i kök dizine indirin ve ardından paketi açın:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Yukarıdaki örnek bir örnektir. Daha ayrıntılı indirme adresini [/pub/Source/dizininde](https://ftp.postgresql.org/pub/source/)bulabilirsiniz.
4. Derlemeyi başlatmak için şu komutları çalıştırın:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Belgeler (HTML ve Man sayfaları) ve ek modüller (contrib) dahil olmak üzere derlenebilir her şeyi derlemek istiyorsanız aşağıdaki komutu çalıştırın:
   
        # gmake install-world
   
    Aşağıdaki onay iletisini almalısınız:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>PostgreSQL yapılandırma
1. Seçim PostgreSQL başvurusunu, sürüm numarasını dahil etmek için kısaltmak üzere bir sembolik bağlantı oluşturun:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Veritabanı için bir dizin oluşturun:
   
        # mkdir -p /opt/pgsql_data
3. Kök olmayan bir kullanıcı oluşturun ve bu kullanıcının profilini değiştirin. Ardından, bu yeni kullanıcıya geçin (örneğimizde *Postgres* adı verilir):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > PostgreSQL, güvenlik nedenleriyle veritabanını başlatmak, başlatmak veya kapatmak için kök olmayan bir Kullanıcı kullanır.
   > 
   > 
4. *Bash_profile* dosyasını aşağıdaki komutları girerek düzenleyin. Bu satırlar *bash_profile* dosyasının sonuna eklenecektir:
   
        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF
5. *Bash_profile* dosyasını yürütün:
   
        $ source .bash_profile
6. Aşağıdaki komutu kullanarak yüklemenizi doğrulayın:
   
        $ which psql
   
    Yüklemeniz başarılı olursa aşağıdaki yanıtı görürsünüz:
   
        /opt/pgsql/bin/psql
7. PostgreSQL sürümünü de denetleyebilirsiniz:
   
        $ psql -V

8. Veritabanını Başlat:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Aşağıdaki çıktıyı almalısınız:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>PostgreSQL 'i ayarlama
<!--    [postgres@ test ~]$ exit -->

Aşağıdaki komutları çalıştırın:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

/Etc/ınitve d/PostgreSQL dosyasındaki iki değişkeni değiştirin. Ön ek PostgreSQL yükleme yoluna ayarlanır: **/seçenek/pgsql**. PGDATA, PostgreSQL: **/seçenek/pgsql_data**veri depolama yoluna ayarlanır.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Dosyayı yürütülebilir hale getirmek için değiştirin:

    # chmod +x /etc/init.d/postgresql

PostgreSQL 'i Başlat:

    # /etc/init.d/postgresql start

PostgreSQL uç noktasının açık olup olmadığını denetle:

    # netstat -tunlp|grep 1999

Aşağıdaki çıktıyı görmeniz gerekir:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Postgres veritabanına bağlanma
Postgres kullanıcısına bir kez daha geçin:

    # su - postgres

Bir Postgres veritabanı oluşturun:

    $ createdb events

Yeni oluşturduğunuz olaylar veritabanına bağlanın:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Bir Postgres tablosu oluşturma ve silme
Veritabanına bağlandığınıza göre, burada tablolar oluşturabilirsiniz.

Örneğin, aşağıdaki komutu kullanarak yeni bir örnek Postgres tablosu oluşturun:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Şu sütun adları ve kısıtlamalarına sahip dört sütunlu bir tablo ayarlamış oldunuz:

1. "Ad" sütunu, VARCHAR komutuyla 20 karakter uzunluğunda olacak şekilde sınırlandırılmıştır.
2. "Yiyecek" sütunu, her birinin suntıracağını gösterir. VARCHAR, bu metni 30 karakter altında olacak şekilde sınırlandırır.
3. "Onaylanan" sütunu, kişinin yemeğini getir 'e sahip olup olmadığını kaydeder. Kabul edilebilir değerler şunlardır "Y" ve "N".
4. "Date" sütunu olaya kaydolduklarında görüntülenir. Postgres, tarihlerin yyyy-aa-gg olarak yazılmasını gerektirir.

Tablonuz başarıyla oluşturulduysa şunları görmeniz gerekir:

![image](./media/postgresql-install/no4.png)

Ayrıca, aşağıdaki komutu kullanarak tablo yapısını kontrol edebilirsiniz:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Tabloya veri ekleme
İlk olarak, bir satıra bilgi ekleyin:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Şu çıktıyı görmeniz gerekir:

![image](./media/postgresql-install/no6.png)

Tabloya aynı zamanda daha fazla kişi ekleyebilirsiniz. Bazı seçenekler aşağıda verilmiştir veya kendi kendinize oluşturabilirsiniz:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Tabloları göster
Bir tabloyu göstermek için aşağıdaki komutu kullanın:

    select * from potluck;

Çıktı:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Tablodaki verileri silme
Tablodaki verileri silmek için aşağıdaki komutu kullanın:

    delete from potluck where name=’John’;

Bu, "John" satırındaki tüm bilgileri siler. Çıktı:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Tablodaki verileri güncelleştirme
Bir tablodaki verileri güncelleştirmek için aşağıdaki komutu kullanın. Bu bir tane için, Sandy katıldıklarından emin oldu, bu nedenle RSVP 'yi "N" iken "Y" olarak değiştiririz:

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>PostgreSQL hakkında daha fazla bilgi alın
Azure Linux VM 'de PostgreSQL yüklemesini tamamladığınıza göre, bunu Azure 'da kullanmaktan yararlanabilirsiniz. PostgreSQL hakkında daha fazla bilgi edinmek için [PostgreSQL Web sitesini](https://www.postgresql.org/)ziyaret edin.

