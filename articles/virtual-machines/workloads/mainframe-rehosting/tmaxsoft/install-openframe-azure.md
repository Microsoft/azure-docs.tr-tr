---
title: Azure sanal makinelerine TmaxSoft OpenFrame 'i yükle
description: Azure sanal makinelerinde (VM) TmaxSoft OpenFrame ortamını kullanarak IBM z/OS ana bilgisayar iş yüklerinizi yeniden barındırın.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436054"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Azure 'da TmaxSoft OpenFrame 'i yükle

Azure 'da geliştirme, tanıtımlar, testler veya üretim iş yükleri için uygun bir OpenFrame ortamı ayarlamayı öğrenin. Bu öğretici, her adımda size kılavuzluk eder.

OpenFrame, Azure 'da ana bilgisayar öykünme ortamını oluşturan birden çok bileşen içerir. Örneğin, OpenFrame çevrimiçi hizmetler IBM müşteri bilgileri denetim sistemi (CICS) ve OpenFrame Batch gibi ana bilgisayar ara yazılımını değiştirerek, cjes bileşeniyle birlikte IBM ana hattının Iş girişi alt sistemi (JES) yerini almıştır.

OpenFrame, Oracle Database, Microsoft SQL Server, IBM DB2 ve MySQL dahil olmak üzere tüm ilişkisel veritabanları ile birlikte çalışarak. OpenFrame 'in bu yüklemesi, TmaxSoft Tibero ilişkisel veritabanını kullanır. Hem OpenFrame hem de Tibero, Linux işletim sisteminde çalışır. Bu öğreticide, desteklenen diğer Linux dağıtımlarını kullanabilseniz de, CentOS 7,3 yüklenir. OpenFrame uygulama sunucusu ve Tibero veritabanı bir sanal makineye (VM) yüklenir.

Bu öğretici, OpenFrame Suite bileşenleri yüklemesinde size kılavuzluk eden bir adım. Bazıları ayrı yüklenmiş olmalıdır.

Ana OpenFrame bileşenleri:

- Gerekli yükleme paketleri.
- Tibero veritabanı.
- Açık veritabanı bağlantısı (ODBC), OpenFrame 'teki uygulamalar tarafından Tibero veritabanıyla iletişim kurmak için kullanılır.
- Tüm sistemi yöneten ara yazılım olan OpenFrame tabanı.
- Ana bilgisayarın Batch sistemlerini değiştiren çözüm olan OpenFrame Batch.
- SITEF, sistem ve kaynaklara Kullanıcı erişimini denetleyen bir hizmet modülüdür.
- Toplu işlemler için bir sıralama aracı olan Prosıralama.
- Ana bilgisayarın COBOL programlarını yorumlayan bir derleyici.
- Ana bilgisayarın assembler programlarını yorumlayan bir derleyici.
- Ana bilgisayarın ara yazılım ve IBM CICS yerine geçen çözüm olan OpenFrame sunucu türü C (OSC).
- Java Enterprise Edition 6 için sertifikalı bir Web uygulama sunucusu olan Java Enterprise User Solution (JEUS).
- OFGW, bir 3270 dinleyicisi sağlayan OpenFrame Gateway bileşeni.
- Web ortamında OpenFrame 'in işlem ve yönetim işlevlerini sağlayan bir çözüm olan OFManager.

Diğer gerekli OpenFrame bileşenleri:

- OSı, ana bilgisayar ara yazılımı ve ıMS DC 'nin yerini alan bir çözümdür.
- TJES, ana bilgisayarın JES ortamını sağlayan çözümdür.
- Açık sistemde kullanılacak (V) SAM dosyalarını sağlayan çözüm.
- Ana bilgisayarın ıMS DB 'nin yerini alan çözüm.
- OFPLI, ana bilgisayarın PL/ı programlarını yorumlayan bir derleyicidir.
- Ana bilgisayar dili CA-Easytrieve çalıştıran bir çözüm olan PROTRIEVE.
- Ana bilgisayarlar varlıklarını çözümleyen ve sonra bunları Azure 'a geçirirken bir çözüm olan OFMiner.

## <a name="architecture"></a>Mimari

Aşağıdaki şekilde, bu öğreticide yüklü OpenFrame 7,0 mimari bileşenlerine genel bir bakış sunulmaktadır:

![OpenFrame bileşenleri](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure sistem gereksinimleri

Aşağıdaki tabloda, Azure üzerinde Yükleme gereksinimleri listelenmektedir.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Gereksinim</th><th>Açıklama</th></tr>
</thead>
<tbody>
<tr><td>Azure 'da desteklenen Linux dağıtımları
</td>
<td>
Linux x86 2,6 (32 bit, 64-bit)<br/>
Red hat 7. x<br/>
CentOS 7. x<br/>
</td>
</tr>
<tr><td>Donanım
</td>
<td>Çekirdekler: 2 (minimum)<br/>
Bellek: 4 GB (minimum)<br/>
Değiştirme alanı: 1 GB (minimum)<br/>
Sabit disk: 100 GB (minimum)<br/>
</td>
</tr>
<tr><td>Windows kullanıcıları için isteğe bağlı yazılım
</td>
<td>PuTTY: VM özelliklerini yapılandırmak için bu kılavuzda kullanılır<br/>
WinSCP: kullanabileceğiniz popüler bir SFTP istemcisi ve FTP istemcisi<br/>
Windows için tutulma: TmaxSoft tarafından desteklenen bir geliştirme platformu<br/>
(Microsoft Visual Studio şu anda desteklenmiyor)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Önkoşullar

Tüm gerekli yazılımları birleştirmek ve tüm el ile gerçekleştirilen işlemlerin tamamını gerçekleştirmek için birkaç gün harcamayı planlayın.

Başlamadan önce aşağıdakileri yapın:

- TmaxSoft 'den OpenFrame yükleme medyasını alın. Var olan bir TmaxSoft müşterisiyseniz, lisanslı bir kopya için TmaxSoft temsilcinizle iletişime geçin. Aksi takdirde, [Tmaxsoft](https://www.tmaxsoft.com/contact/)'ten bir deneme sürümü isteyin.

- @No__t-0 ' a e-posta göndererek OpenFrame belgelerini isteyin.

- Henüz yoksa bir Azure aboneliği alın. Başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) da oluşturabilirsiniz.

- İsteğe bağlı. Azure VM 'ye erişimi kuruluşunuzdaki izin verilen kullanıcılarla sınırlayan bir sıçrama kutusu veya siteden siteye VPN tüneli ayarlayın. Bu adım gerekli değildir, ancak en iyi uygulamadır.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>OpenFrame ve Tibero için Azure 'da VM ayarlama

OpenFrame ortamını çeşitli dağıtım düzenlerini kullanarak ayarlayabilirsiniz, ancak aşağıdaki yordamda OpenFrame uygulama sunucusunun ve Tibero veritabanının bir VM 'de nasıl dağıtılacağı gösterilmektedir. Daha büyük ortamlarda ve boyutlandırılabilir iş yükleri için, daha iyi performans sağlamak üzere veritabanını kendi sanal makinesine ayrı olarak dağıtmaktır.

**VM oluşturmak için**

1. Azure portal <https://portal.azure.com> ' a gidin ve hesabınızda oturum açın.

2. **Sanal makineler**' e tıklayın.

    ![Azure portal kaynak listesi](media/vm-01.png)

3. **Ekle**'ye tıklayın.

    ![Azure portal seçenek Ekle](media/vm-02.png)

4. **Işletim sistemlerinin**sağında **daha fazla**' ya tıklayın.

     ![Azure portal daha fazla seçenek](media/vm-03.png)

5. Bu Kılavuzu tam olarak izlemek için **CentOS tabanlı 7,3** ' e tıklayın veya desteklenen başka bir Linux dağıtımını seçebilirsiniz.

     ![Azure portal işletim sistemi seçenekleri](media/vm-04.png)

6. **Temel bilgiler** için **ad**, **Kullanıcı adı**, **kimlik doğrulama türü**, **abonelik** (Kullandıkça öde ' ın AWS) ve **kaynak grubunun** (var olan bir tane kullanın veya bir tmaxsoft grubu oluşturun) girin.

7. Tamamlandığında ( **kimlik doğrulama türü**için ortak/özel anahtar çifti dahil), **Gönder**' e tıklayın.

> [!NOTE]
> **Kimlik doğrulama türü**için SSH ortak anahtarı kullanılıyorsa, ortak/özel anahtar çiftini oluşturmak için sonraki bölümdeki adımlara bakın ve ardından buradaki adımları sürdürebilirsiniz.

### <a name="generate-a-publicprivate-key-pair"></a>Ortak/özel anahtar çifti oluşturma

Bir Windows işletim sistemi kullanıyorsanız, bir ortak/özel anahtar çifti oluşturmak için PuTTYgen gerekir.

Ortak anahtar serbestçe paylaşılabilir, ancak özel anahtarın tamamen gizli tutulması ve asla başka bir tarafla paylaşılmaması gerekir. Anahtarları oluşturduktan sonra, **SSH ortak anahtarını** , etkin ve Linux sanal makinesine yükleyerek yapılandırmaya yapıştırmanız gerekir. Kullanıcı hesabının giriş dizininin \~/. SSH dizini içinde yetkilendirilmiş @ no__t-0keys içinde depolanır. Linux VM daha sonra, SSH istemcisinde ilişkili **SSH özel anahtarını** (bizim örneğimizde, Putty) sağladığınızda bağlantıyı algılayabilir ve doğrulayabilir.

Yeni kişilere VM 'ye erişim izni verirken: 

- Her yeni bireysel, PuTTYgen kullanarak kendi ortak/özel anahtarlarını oluşturur.
- Bireyler kendi özel anahtarlarını ayrı olarak depolar ve ortak anahtar bilgilerini VM yöneticisine gönderir.
- Yönetici, ortak anahtarın içeriğini \~/. SSH/yetkilendirilmiş @ no__t-1keys dosyasına yapıştırır.
- Yeni bireysel, PuTTY aracılığıyla bağlanır.

**Ortak/özel anahtar çifti oluşturmak için**

1.  PuTTYgen ' i <https://www.putty.org/> ' dan indirin ve tüm varsayılan ayarları kullanarak yükleyin.

2.  PuTTYgen dosyasını açmak için, C: \\Program Files @ no__t-1PuTTY konumunda PuTTY yükleme dizinini bulun.

    ![PuTTY arabirimi](media/puttygen-01.png)

3.  **Oluştur**' a tıklayın.

    ![PuTTY anahtar Oluşturucu iletişim kutusu](media/puttygen-02.png)

4.  Kuşak olduktan sonra, ortak anahtarı ve özel anahtarı kaydedin. Ortak anahtarın içeriğini, **sanal makine oluştur \> temelleri** bölmesinin **SSH ortak anahtar** bölümüne yapıştırın (önceki bölümde 6. ve 7. adımlarda gösterilmektedir).

    ![PuTTY anahtar Oluşturucu iletişim kutusu](media/puttygen-03.png)

### <a name="configure-vm-features"></a>VM özelliklerini yapılandırma

1. Azure portal, **boyut seçin** dikey penceresinde istediğiniz Linux makine donanım ayarlarını seçin. Her iki Tibero ve OpenFrame 'i yüklemeye yönelik *Minimum* gereksinimler, bu örnek yüklemesinde gösterildiği gibi 2 CPU ve 4 GB RAM 'dir:

    ![Sanal makine oluşturma-temel bilgiler](media/create-vm-01.png)

2. **3 ayarlar** ' a tıklayın ve isteğe bağlı özellikleri yapılandırmak için varsayılan ayarları kullanın.
3. Ödeme ayrıntılarınızı gözden geçirin.

    ![Sanal makine oluşturma-satın alma](media/create-vm-02.png)

4. Seçimlerinizi iletin. Azure, VM 'yi dağıtmaya başlar. Bu işlem genellikle birkaç dakika sürer.

5. VM dağıtıldığında, yapılandırma sırasında seçilen tüm ayarları gösteren Pano görüntülenir. **Genel IP adresini**bir yere getirin.

    ![Azure panosunda Tmax](media/create-vm-03.png)

6. PuTTY’yi açın.

7. **Ana bilgisayar adı**için, Kullanıcı adınızı ve KOPYALADıĞıNıZ genel IP adresini yazın. Örneğin, **Kullanıcı adı @ no__t-1publicıp**.

    ![PuTTY yapılandırması iletişim kutusu](media/putty-01.png)

8. **Kategori** kutusunda **bağlantı \> SSH \> kimlik doğrulaması**' na tıklayın. **Özel anahtar** dosyanızın yolunu belirtin.

    ![PuTTY yapılandırması iletişim kutusu](media/putty-02.png)

9. PuTTY penceresini başlatmak için **Aç** ' a tıklayın. Başarılı olursa, Azure 'da çalışan yeni CentOS sanal makinenize bağlanırsınız.

10. Kök kullanıcı olarak oturum açmak için **sudo Bash**yazın.

    ![Komut penceresinde kök kullanıcı oturumu açma](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Ortamı ve paketleri ayarlama

Artık VM oluşturulduğundan ve oturum açtığınız için, birkaç Kurulum adımı gerçekleştirmeniz ve gerekli önyükleme paketlerini kurmanız gerekir.

1. Ana bilgisayar dosyasını (`vi /etc/hosts`) düzenlemek için VI kullanarak **tanıtım** adı 'Nı yerel IP adresine eşleyin. IP 'nin 192.168.96.148 ofdemo olduğunu varsayarsak, bu, değişiklikten önce:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Bu, değişiklikten sonra:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Gruplar ve kullanıcılar oluşturun:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Kullanıcı oframe7 parolasını değiştirme:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. /Etc/sysctl.exe içindeki çekirdek parametrelerini güncelleştirin:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Çekirdek parametrelerini, yeniden başlatma olmadan dinamik olarak yenileyin:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Gerekli paketleri al: sunucunun Internete bağlı olduğundan emin olun, aşağıdaki paketleri indirin ve ardından bunları yükleyin:

     - dos2unix
     - GLIBC
     - göz BC. i686, BC. x86 @ no__t-064
     - libaio
     - ncurler

          > [!NOTE]
          > Ncurses paketini yükledikten sonra aşağıdaki sembolik bağlantıları oluşturun:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - GCC
     - GCC-c + +
     - libaio-delevel. x86 @ no__t-064
     - STrace
     - ltrace
     - GDB

7. Java RPM yüklemesi durumunda şunları yapın:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Tibero veritabanını yükler

Tibero, Azure 'daki OpenFrame ortamında birkaç temel işlev sağlar:

- Tibero, çeşitli sistem işlevleri için OpenFrame iç veri deposu olarak kullanılır.
- KSK 'ler, RRDS ve ESDS dahil VSAM dosyaları, veri depolama için dahili olarak Tibero veritabanını kullanır.
- TACF veri deposu Tibero 'da depolanır.
- OpenFrame Katalog bilgileri Tibero 'da depolanır.
- Tibero veritabanı, uygulama verilerini depolamak için IBM DB2 yerine kullanılabilir.

**Tibero 'ı yüklemek için**

1. Tibero ikili yükleyici dosyasının mevcut olduğunu doğrulayın ve sürüm numarasını gözden geçirin.
2. Tibero yazılımını Tibero Kullanıcı hesabına (oframe) kopyalayın. Örnek:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Vi (`vi .bash_profile`) içinde. bash @ no__t-0profile dosyasını açın ve içine aşağıdaki yapıştırın:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Bash profilini yürütmek için komut isteminde şunu yazın:

    ```
    source .bash_profile
    ```

5. İpucu dosyasını (Tibero için bir yapılandırma dosyası) oluşturun, ardından bunu vi 'de açın. Örnek:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. @No__t-0TB @ no__t-1HOME/Client/config/tbdsn. TBR öğesini değiştirin ve gösterildiği gibi şu oflocalhost yerine 127.0.0.1 koyun:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Veritabanını oluşturun. Aşağıdaki çıktı görüntülenir:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Tibero 'yu geri dönüştürmek için ilk olarak `tbdown` komutunu kullanarak kapatın. Örnek:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Şimdi `tbboot` kullanarak Tibero 'ı başlatın. Örnek:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Tablo alanı oluşturmak için SYS User (sys/Tmax) kullanarak veritabanına erişin, ardından varsayılan birim ve TACF için gerekli tablo alanını oluşturun:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Şimdi aşağıdaki SQL komutlarını yazın:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Tibero 'yu önyükleyin ve Tibero işlemlerinin çalıştığını doğrulayın:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Çıktı:

![Tibero çıkışı](media/tibero-01.png)

## <a name="install-odbc"></a>ODBC 'yi yükler

OpenFrame 'teki uygulamalar, açık kaynak unixODBC projesi tarafından sunulan ODBC API 'sini kullanarak Tibero veritabanıyla iletişim kurar.

ODBC yüklemek için:

1. UnixODBC-2.3.4. tar. gz yükleyici dosyasının mevcut olduğunu doğrulayın veya `wget unixODBC-2.3.4.tar.gz` komutunu kullanın. Örnek:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. İkiliyi sıkıştırmayı açın. Örnek:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. UnixODBC-2.3.4 dizinine gidin ve makine bilgilerini denetleme öğesini kullanarak derleme görevleri dosyasını oluşturun. Örnek:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Varsayılan olarak, unixODBC/usr/local dizinine yüklenir, bu nedenle `--prefix`, konumu değiştirmek için bir değer geçirir. Benzer şekilde, yapılandırma dosyaları varsayılan olarak/vb 'ye yüklenir, bu nedenle `--sysconfdir` istenen konumun değerini geçirir.

4. Derleme görevleri dosyasını yürütme: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Derleme sonrasında yürütülebilir dosyayı program dizinine kopyalayın. Örnek:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Bash profilini düzenlemek için vi kullanın (`vi ~/.bash_profile`) ve aşağıdakileri ekleyin:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. ODBC 'yi uygulayın. Aşağıdaki dosyaları uygun şekilde düzenleyin. Örnek:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Sembolik bir bağlantı oluşturun ve Tibero veritabanı bağlantısını doğrulayın:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Aşağıdaki çıktı görüntülenir:

![SQL 'e bağlı gösteren ODBC çıkışı](media/odbc-01.png)

## <a name="install-openframe-base"></a>OpenFrame temelini yükler

Temel uygulama sunucusu, OpenFrame 'in, işlem işleme sunucusu işlemleri de dahil olmak üzere Azure 'da sistemi yönetmek için kullandığı bireysel hizmetlerden önce yüklenir.

**OpenFrame temelini yüklemek için**

1. Tibero yüklemesinin başarılı olduğundan emin olun, sonra şu OpenFrame @ no__t-0Base7 @ no__t-10 @ no__t-2Linux @ no__t-3x86\_64.bin Installer File ve Base. Properties yapılandırma dosyasının mevcut olduğunu doğrulayın.

2. Bash profilini aşağıdaki Tibero 'a özgü bilgilerle güncelleştirin:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Bash profilini yürütün: `[oframe7@ofdemo ~]$ . .bash_profile`
4. Tibero işlemlerinin çalıştığından emin olun. Örnek:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Taban](media/base-01.png)

     > [!IMPORTANT]
     > Yüklemeden önce Tibero 'ı başlattığınızdan emin olun.

5. [TechNet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) adresinden lisans oluşturun ve OpenFrame Base, Batch, TACF, OSC lisanslarını uygun klasöre yerleştirin:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. OpenFrame temel ikili ve Base. Properties dosyalarını indirin:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Base. Properties dosyasını kullanarak yükleyiciyi yürütün. Örnek:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Tamamlandığında, yükleme tamamlandıktan sonra ileti çalınır.

8. @No__t-0 komutunu kullanarak OpenFrame temel dizin yapısını doğrulayın. Örnek:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. OpenFrame temelini Başlat:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot komut çıkışı](media/base-02.png)

10. İşlem durumunun si içindeki tmadmin komutu kullanılarak hazırlanmaya çalıştığını doğrulayın. RDY, işlemlerin her biri için **durum** sütununda görüntülenir:

     ![tmadmin komut çıktısı](media/base-03.png)

11. OpenFrame temelini kapat:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>OpenFrame toplu Işlemini yükler

OpenFrame toplu işi, anabilgisayar toplu ortamlarının benzetimini yapan ve Azure üzerinde toplu işleri çalıştırmak için kullanılan birkaç bileşenden oluşur.

**Toplu Işi yüklemek için**

1. Temel yüklemenin başarılı olduğundan emin olun, ardından OpenFrame @ no__t-0Batch7 @ no__t-10 @ no__t-2Fix2 @ no__t-3MVS @ no__t-4Linux @ no__t-5x86\_64.bin Installer File ve Batch. Properties yapılandırma dosyasının mevcut olduğunu doğrulayın:

2. Komut isteminde, VI kullanarak Batch. Properties dosyasını düzenlemek için `vi batch.properties` yazın.

3. Parametreleri aşağıdaki gibi değiştirin:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Batch yükleyicisini yürütmek için komut isteminde şunu yazın:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Yükleme tamamlandığında, komut isteminde `tmboot` yazarak yüklü OpenFrame paketlerini başlatın.

    ![tmboot çıkışı](media/tmboot-01.png)

6. OpenFrame işlemini denetlemek için komut isteminde `tmadmin` yazın.

    ![Tmax yönetici ekranı](media/tmadmin-01.png)

7. Aşağıdaki komutları yürütün:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Batch 'i başlatmak ve kapatmak için `tmdown` komutunu kullanın:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>TACF 'yi yükler

TACF Manager, RACF güvenliği aracılığıyla sistemlere ve kaynaklara Kullanıcı erişimini denetleyen bir OpenFrame hizmeti modülüdür.

**TACF 'yi yüklemek için**

1. OpenFrame @ no__t-0Tacf7 @ no__t-10 @ no__t-2Fix2 @ no__t-3Linux @ no__t-4x86\_64.bin Installer File ve TACF. Properties yapılandırma dosyasının mevcut olduğunu doğrulayın.
2. Batch yüklemesinin başarılı olduğundan emin olun, sonra da TACF. Properties dosyasını (`vi tacf.properties`) açmak için VI kullanın.
3. TACF parametrelerini değiştirin:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. TACF yükleyicisini tamamladıktan sonra, TACF ortam değişkenlerini uygulayın. Komut istemine şunları yazın:

     ```
     source \~/.bash\_profile
     ```

5. TACF yükleyicisini yürütün. Komut istemine şunları yazın:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     Çıktı şuna benzer:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. Komut isteminde, OpenFrame 'i yeniden başlatmak için `tmboot` yazın. Çıktı şuna benzer:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. @No__t-1 komutunda `tmadmin` kullanarak işlem durumunun hazırlandiğini doğrulayın. Örnek:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     **Durum** sütununda, RDY görünür:

    ![Durum sütununda RDY](media/tmboot-02.png)

8. Aşağıdaki komutları yürütün:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. @No__t-0 komutunu kullanarak sunucuyu kapatın. Çıktı şuna benzer:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>ProSort 'ı yükler

ProSort, verileri sıralamak için Batch işlemlerinde kullanılan bir yardımcı programdır.

**ProSort 'ı yüklemek için**

1. Toplu yükleme işleminin başarılı olduğundan emin olun ve **prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz** Installer dosyasının mevcut olduğunu doğrulayın.

2. Özellikler dosyasını kullanarak yükleyiciyi yürütün. Komut istemine şunları yazın:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Prosort dizinini ana konuma taşıyın. Komut istemine şunları yazın:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Bir lisans alt dizini oluşturun ve lisans dosyasını buraya kopyalayın. Örnek:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Bash. Profile dosyasını VIT (`vi .bash_profile`) içinde açın ve aşağıdaki gibi güncelleştirin:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Bash profilini yürütmek için komut isteminde şunu yazın: `. .bash_profile`

7. Yapılandırma dosyasını oluşturun. Örnek:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Sembolik bağlantıyı oluşturun. Örnek:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. @No__t-0 komutunu yürüterek ProSort yüklemesini doğrulayın. Örnek:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>OFCOBOL 'yi yükler

OFCOBOL, ana bilgisayarın COBOL programlarını yorumlayan OpenFrame derleyicisidir. 

**OFCOBOL 'yi yüklemek için**

1. Batch/online yüklemesinin başarılı olduğundan emin olun ve OpenFrame @ no__t-0COCıVA3 @ no__t-10 @ no__t-240 @ no__t-3Linux @ no__t-4x86\_64.bin Installer dosyasının mevcut olduğunu doğrulayın.

2. OFCOBOL yükleyicisini yürütmek için komut isteminde şunu yazın:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Lisanslama anlaşmasını okuyun ve devam etmek için ENTER tuşuna basın.

4. Lisans sözleşmesini kabul edin. Yükleme tamamlandığında aşağıdakiler görüntülenir:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Bash profilini VI (`vi .bash_profile`) içinde açın ve OFCOBOL değişkenleriyle güncelleştirildiğinden emin olun.
6. Bash profilini yürütün. Komut istemine şunları yazın:

     ```
      source ~/.bash_profile
     ```

7. OFCOBOL lisansını yüklü klasöre kopyalayın. Örnek:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. OpenFrame tjclrun. conf yapılandırma dosyasına gidin ve vi 'de açın. Örnek:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Değişiklik öncesinde SYSLIB bölümü aşağıda verilmiştir:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Değişiklik sonrasında SYSLIB bölümü aşağıda verilmiştir:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Vi içindeki OpenFrame @ no__t-0COBOL\_InstallLog.log dosyasını gözden geçirin ve hata olmadığından emin olun. Örnek:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. @No__t-0 komutunu kullanın ve yüklemeyi doğrulamak için sürüm numarasını gözden geçirin. Örnek:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. @No__t-0 komutunu kullanarak OpenFrame 'i yeniden başlatın.

## <a name="install-ofasm"></a>OFASM 'yi yükler

OFASM, ana bilgisayarın assembler programlarını yorumlayan OpenFrame derleyicisidir.

**OFASM 'yi yüklemek için**

1. Batch/online yüklemesinin başarılı olduğundan emin olun ve OpenFrame @ no__t-0ASM3 @ no__t-10 @ no__t-2Linux @ no__t-3x86\_64.bin yükleyici dosyasının mevcut olduğunu doğrulayın.

2. Yükleyiciyi yürütün. Örnek:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Lisanslama anlaşmasını okuyun ve devam etmek için ENTER tuşuna basın.
4. Lisans sözleşmesini kabul edin.
5. Bash profilinin OFASM değişkenleriyle güncelleştirildiğinden emin olun. Örnek:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Vi 'de OpenFrame tjclrun. conf yapılandırma dosyasını açın ve aşağıdaki gibi düzenleyin:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Değişiklikten *önceki* [SYSLIB] bölümü aşağıda verilmiştir:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Değişiklik *sonrasında* [SYSLIB] bölümü aşağıda verilmiştir:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. OpenFrame @ no__t-0ASM\_InstallLog.log dosyasını vi 'de açın ve hata olmadığından emin olun. Örnek:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Aşağıdaki komutlardan birini vererek OpenFrame 'i yeniden başlatın:

     ```
     tmdown / tmboot
     ```

     veya

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>OSC 'yi yükler

OSC, yüksek hızlı OLTP işlemlerini ve diğer yönetim işlevlerini destekleyen IBM CICS benzer bir OpenFrame ortamıdır.

**OSC 'yi yüklemek için**

1. Temel yüklemenin başarılı olduğundan emin olun, ardından OpenFrame @ no__t-0S7 @ no__t-10 @ no__t-2Fix2 @ no__t-3Linux @ no__t-4x86\_64.bin Installer File ve OSC. Properties yapılandırma dosyasının mevcut olduğunu doğrulayın.
2. Aşağıdaki parametreleri, OSC. Properties dosyasında düzenleyin:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Aşağıda gösterildiği gibi özellikler dosyasını kullanarak yükleyiciyi yürütün:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     İşiniz bittiğinde, "yükleme tamamlandı" iletisi görüntülenir.

4. Bash profilinin, OSC değişkenleriyle güncelleştirildiğinden emin olun.
5. OpenFrame @ no__t-0S7 @ no__t-10 @ no__t-2Fix2\_InstallLog.log dosyasını gözden geçirin. Şuna benzer şekilde görünecektir:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. ' I kullanarak ofsys. seq yapılandırma dosyasını açın. Örnek:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. @No__t-0BASE ve \#BATCH bölümlerinde, parametreleri gösterildiği gibi düzenleyin.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Lisans dosyasını kopyalayın. Örnek:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. OSC 'yi başlatmak ve kapatmak için, komut isteminde `osctdlinit OSCOIVP1` yazarak CICS Region paylaşılan belleğini başlatın.

10. OSC 'yi önyüklemek için `oscboot` ' i çalıştırın. Çıktı şuna benzer:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. İşlem durumunun kullanılabilir olduğunu doğrulamak için, si içinde `tmadmin` komutunu kullanın. Tüm işlemlerin **durum** sütununda RDY görüntülemesi gerekir.

    ![RDY 'yi görüntüleyen süreçler](media/tmadmin-02.png)

12. @No__t-0 komutunu kullanarak OSC 'yi kapatın.

## <a name="install-jeus"></a>JEABD 'yi yükler

JEUS (Java kurumsal Kullanıcı çözümü), OpenFrame Web uygulaması sunucusunun sunum katmanını sağlar.

JEUS 'yi yüklemeden önce, JEABD 'yi yüklemek için gereken kitaplıkları ve komut satırı araçlarını sağlayan Apache Ant paketini yüklemeniz gerekir.

**Apache Ant yüklemek için**

1. @No__t-0 komutunu kullanarak ant ikilisini indirin. Örnek:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. İkili dosyayı ayıklamak ve uygun bir konuma taşımak için `tar` yardımcı programını kullanın. Örnek:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Verimlilik için bir sembolik bağlantı oluşturun:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Bash profilini VIT (`vi .bash_profile`) içinde açın ve aşağıdaki değişkenlerle güncelleştirin:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Değiştirilen ortam değişkenini uygulayın. Örnek:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**JEABD 'yi yüklemek için**

1. @No__t-0 yardımcı programını kullanarak yükleyiciyi genişletin. Örnek:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Bir **jeus** klasörü oluşturun (`mkdir jeus7`) ve ikiliyi ayıklayın.
3. **Kurulum** dizinine geçin (veya kendi ortamınız için jeus parametresini kullanın). Örnek:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Derlemeyi gerçekleştirmeden önce `ant clean-all` yürütün. Çıktı şuna benzer:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Domain-config-Template. Properties dosyasının yedeklemesini yapın. Örnek:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. VI 'de Domain-config-Template. Properties dosyasını açın:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. @No__t-0 ' i `jeus.password=tmax1234 nodename=ofdemo` olarak değiştir

8. JEUS oluşturmak için `ant install` komutunu yürütün.
9.  . Bash @ no__t-0profile dosyasını aşağıda gösterildiği gibi JEUS değişkenleriyle güncelleştirin:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Bash profilini yürütün. Örnek:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *İsteğe bağlı*. JEUS bileşenlerinin kolay bir şekilde kapatılmasını ve önyüklemesi için bir diğer ad oluşturun:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Yüklemeyi doğrulamak için, etki alanı yöneticisi sunucusunu gösterildiği gibi başlatın:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Sözdizimini kullanarak Web oturumu açmaya göre doğrulayın:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Örneğin, <http://192.168.92.133:9736/webadmin/login.>, oturum açma ekranı görünür:
    
     ![JEUS WebAdmin oturum açma ekranı](media/jeus-01.png)

     > [!NOTE]
     > Bağlantı noktası güvenliği ile ilgili herhangi bir sorunla karşılaşırsanız 9736 numaralı bağlantı noktasını açın veya güvenlik duvarını devre dışı bırakın (`systemctl stop firewall`).

14. Sunucu1 için ana bilgisayar adını değiştirmek için **kilitle & Düzenle**' ye tıklayın ve ardından **Sunucu1**' e tıklayın. Sunucu penceresinde, ana bilgisayar adını aşağıdaki gibi değiştirin:

    1.  **Düğdüğün** **ofdemo**'e geçin.
    2.  Pencerenin sağ tarafındaki **Tamam** ' a tıklayın.
    3.  Pencerenin sol alt tarafında bulunan **Değişiklikleri Uygula** ' ya tıklayın ve açıklama Için *ana bilgisayar adı değişikliğini*girin.

    ![JEUS WebAdmin ekranı](media/jeus-02.png)

15. Onay ekranında yapılandırmanın başarılı olduğunu doğrulayın.

    ![jeus_domain sunucusu ekranı](media/jeus-03.png)

16. Aşağıdaki komutu kullanarak "Sunucu1" yönetilen sunucu işlemini başlatın:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>OFGW 'yi yükler

OFGW, 3270 Terminal öykünücüsü ile OSı temel arasındaki iletişimi destekleyen OpenFrame ağ geçididir ve terminal öykünücüsü ile OSı arasındaki oturumları yönetir.

**OFGW 'yi yüklemek için**

1. JEUS 'nin başarılı bir şekilde yüklendiğinden emin olun, sonra OFGW7 @ no__t-00 @ no__t-11\_Generic.bin yükleyici dosyasının mevcut olduğunu doğrulayın.
2. Yükleyiciyi yürütün. Örnek:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Karşılık gelen istemler için aşağıdaki konumları kullanın:
     -   JEUS giriş dizini
     -   JEUS etki alanı adı
     -   JEUS sunucu adı
     -   Tibero sürücüsü
     -   Tmax düğüm KIMLIĞI tanıtımı

4. Varsayılan değerleri kabul edin ve yükleyiciden çıkmak için ENTER tuşuna basın.

5. OFGW URL 'sinin beklenen şekilde çalıştığını doğrulayın:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Aşağıdaki ekran görünür:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>OFManager 'ı yükler

OFManager, Web ortamında OpenFrame için işlem ve yönetim işlevleri sağlar.

**OFManager 'ı yüklemek için**

1. OFManager7\_Generic.bin yükleyici dosyasının mevcut olduğunu doğrulayın.
2. Yükleyiciyi yürütün. Örnek:

     ```
     OFManager7_Generic.bin
     ```

3.  Devam etmek için ENTER tuşuna basın ve lisans sözleşmesini kabul edin.
4.  Install klasörünü seçin.
5.  Varsayılanları kabul edin.
6.  Veritabanı olarak Tibero 'yu seçin.
7.  Yükleyiciden çıkmak için ENTER tuşuna basın.
8.  OFManager URL 'sinin beklenen şekilde çalıştığını doğrulayın:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Başlangıç ekranı görünür:

![Tmax OpenFrame Manager oturum açma ekranı](media/ofmanager-01.png)

Bu işlem, OpenFrame bileşenlerinin yüklenmesini tamamlar.

## <a name="next-steps"></a>Sonraki adımlar

Ana bilgisayar geçişini düşünüyorsanız, genişleyen iş ortağı ekosistemimiz size yardımcı olacak şekilde sunulmaktadır. Bir iş ortağı çözümü seçme hakkında ayrıntılı kılavuz için, [Platform modernleştirme Alliance](https://datamigration.microsoft.com/)' a bakın.

-   [Azure’ı kullanmaya başlama](https://docs.microsoft.com/azure/)
-   [Host Integration Server () belgeleri](https://docs.microsoft.com/host-integration-server/)
-   [Azure sanal veri merkezi yükseltme-ve-kaydırma Kılavuzu](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
