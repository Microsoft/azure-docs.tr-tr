---
title: Azure NetApp Files için Azure uygulaması tutarlı anlık görüntü aracı 'nı yükler | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracının yüklenmesi için bir kılavuz sağlar.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 458f4d3f29cb08a94095167ed45133f5cd70f5f4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869200"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulaması tutarlı anlık görüntü aracı 'nı (Önizleme) yükler

Bu makalede, Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracının yüklenmesi için bir kılavuz sunulmaktadır.

## <a name="introduction"></a>Giriş

İndirilebilir Self-yükleyici, anlık görüntü araçlarını, kök olmayan kullanıcı ayrıcalıklarıyla ayarlamayı ve çalıştırmayı kolay hale getirmek için tasarlanmıştır (örneğin, azacsnap). Yükleyici, kullanıcıyı ayarlar ve anlık görüntü araçlarını kullanıcılar `$HOME/bin` alt dizinine (varsayılan = `/home/azacsnap/bin` ) koyar.
Self-yükleyici, yüklemeyi gerçekleştiren kullanıcının yapılandırmasına bağlı olarak tüm dosyalar için doğru ayarları ve yolları belirlemeyi dener (örneğin, kök). Önkoşul adımları (depolama ile iletişimi etkinleştir ve SAP HANA) kök olarak çalıştırılmıştı, yükleme özel anahtarı ve `hdbuserstore` yedekleme kullanıcısının konumuna kopyalar. Ancak, depolama arka ucu ile iletişimi etkinleştiren ve yüklemeden sonra bilgili bir yönetici tarafından el ile yapılacak SAP HANA adımlar için de mümkündür.

## <a name="prerequisites-for-installation"></a>Yükleme önkoşulları

Anlık görüntü ve olağanüstü durum kurtarma komutlarını ayarlamak ve yürütmek için yönergeleri izleyin. Anlık görüntü araçları yüklenmeden ve kullanılmadan önce aşağıdaki adımların kök olarak tamamlanması önerilir.

1. **Işletim sistemine düzeltme eki** uygulanıyor: [Azure 'Da SAP HANA (büyük örnekler) yükleme ve yapılandırma](../virtual-machines/workloads/sap/hana-installation.md#operating-system)içindeki düzeltme eki uygulama ve SMT kurulumuna bakın.
1. **Zaman eşitleme ayarlanır**. Müşterinin NTP ile uyumlu bir saat sunucusu sağlaması ve işletim sistemini uygun şekilde yapılandırması gerekir.
1. **Hana yüklendi** : [Hana veritabanı 'Nda SAP NetWeaver yüklemesinde](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database)Hana yükleme yönergelerine bakın.
1. **[Depolama ile Iletişimi etkinleştir](#enable-communication-with-storage)** (daha fazla ayrıntı için ayrı bölüme bakın): müşterinin bir özel/ortak anahtar ÇIFTIYLE SSH ayarlaması ve anlık görüntü araçlarının, depolama arka ucunda kurulum Için Microsoft işlemlerine yürütülecek şekilde planlandığı her düğüm için ortak anahtar sağlaması gerekir.
   1. **Azure NetApp Files için (Ayrıntılar için ayrı bölüme bakın)**: müşterinin hizmet sorumlusu kimlik doğrulama dosyasını oluşturması gerekir.
   1. **Azure büyük örnek için (Ayrıntılar için ayrı bir bölüme bakın)**: müşterinin bir özel/ortak anahtar ÇIFTIYLE SSH ayarlaması ve anlık görüntü araçlarının, depolama arka ucunda kurulum Için Microsoft işlemlerine yürütülecek şekilde planlandığı her düğüm için ortak anahtar sağlaması gerekir.

      Düğümlerden birine bağlanmak için SSH kullanarak bunu test edin (örneğin, `ssh -l <Storage UserName> <Storage IP Address>` ).
      `exit`Depolama isteminin oturumunu kapatmak için yazın.

      Microsoft işlemleri, sağlama sırasında depolama kullanıcısını ve depolama IP 'sini sağlayacaktır.
  
1. **[SAP HANA ile Iletişimi etkinleştir](#enable-communication-with-sap-hana)** (daha fazla ayrıntı için ayrı bölüme bakın): müşterinin, anlık görüntüyü gerçekleştirmek için gerekli ayrıcalıklara sahip uygun bir SAP HANA kullanıcısı ayarlaması gerekir.
   1. Bu ayar, komut satırından şu metin kullanılarak test edilebilir `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - Yukarıdaki örneklerde SSL olmayan iletişim SAP HANA.

## <a name="enable-communication-with-storage"></a>Depolama ile iletişimi etkinleştir

Bu bölümde, depolama ile iletişimin nasıl etkinleştirileceği açıklanmaktadır.

### <a name="azure-netapp-files"></a>Azure NetApp Files

RBAC hizmet sorumlusu oluşturma

1. Azure Cloud Shell oturumunda, hizmet sorumlusu ile ilişkilendirmek istediğiniz abonelikte varsayılan olarak oturum açtığınızdan emin olun:

    ```azurecli-interactive
    az account show
    ```

1. Abonelik doğru değilse, şunu kullanın

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. Aşağıdaki örneğe göre Azure CLı kullanarak bir hizmet sorumlusu oluşturun

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. Bu, aşağıdaki örnekte olduğu gibi bir çıktı üretir:

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > Bu komut, abonelik düzeyinde hizmet sorumlusuna RBAC katılımcısı rolünü otomatik olarak atayacaktır, bu kapsamı, testlerin kaynakları oluşturmakta olacağı belirli kaynak grubuna daraltabilirsiniz.

1. Çıktı içeriğini kesip `azureauth.json` komutuyla aynı sistemde depolanan adlı bir dosyaya yapıştırın `azacsnap` ve dosyayı uygun sistem izinleriyle koruyun.

### <a name="azure-large-instance"></a>Azure büyük örnek

Depolama arka ucu ile iletişim, şifrelenmiş bir SSH kanalı üzerinde yürütülür. Aşağıdaki örnek adımlar, bu iletişim için SSH kurulumu hakkında rehberlik sağlamaktır.

1. Dosyayı değiştirme `/etc/ssh/ssh_config`

    Satırın eklendiği aşağıdaki çıktıya başvurun `MACs hmac-sha1` :

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Özel/ortak anahtar çifti oluşturma

    Anahtar çiftini oluşturmak için aşağıdaki örnek komutunu kullanarak anahtar oluştururken bir parola girmeyin.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Ortak anahtarı Microsoft Işlemlerine gönderin

    `cat /root/.ssh/id_rsa.pub`Anlık görüntü araçlarının depolama alt sistemiyle iletişim kurmasını sağlamak için komut çıkışını Microsoft işlemlerine gönderin (aşağıdaki örnekte).

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>SAP HANA ile iletişimi etkinleştir

Anlık görüntü araçları SAP HANA ile iletişim kurar ve veritabanı kaydetme noktasını başlatmak ve serbest bırakmak için uygun izinlere sahip bir kullanıcıya ihtiyacı vardır. Aşağıdaki örnek, SAP HANA v2 kullanıcısının kurulumunu ve `hdbuserstore` SAP HANA veritabanına yönelik iletişimi gösterir.

Aşağıdaki örnek komutlar, SAP HANA 2 üzerinde SYSTEMDB 'de bir Kullanıcı (AZACSNAP) ayarlar.
veritabanı, IP adresini, Kullanıcı adlarını ve parolaları uygun şekilde değiştirin:

1. Kullanıcı oluşturmak için SYSTEMDB 'ye bağlanma

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. Kullanıcı oluşturma

    Bu örnek, SYSTEMDB 'de AZACSNAP kullanıcısını oluşturur.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Kullanıcı izinlerini verme

    Bu örnek, AZACSNAP kullanıcısının iznini, veritabanı ile tutarlı bir depolama anlık görüntüsü gerçekleştirmeye izin verecek şekilde ayarlar.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *Isteğe bağlı* -kullanıcının parolasının süresinin dolmasını engelle

    > [!NOTE]
    > Bu değişikliği yapmadan önce şirket ilkesiyle görüşün.

   Bu örnek, AZACSNAP kullanıcısı için parola süre sonunu devre dışı bırakır, bu değişiklik yapılmadan kullanıcının parolasının süresi, anlık görüntülerin doğru şekilde yapılmasını önler.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. SAP HANA güvenli Kullanıcı deposunu ayarlama (parolayı değiştirme) Bu örnek, `hdbuserstore` SAP HANA güvenli Kullanıcı deposunu ayarlamak Için Linux kabuğundan komutunu kullanır.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Güvenli Kullanıcı deposunun doğru şekilde ayarlandığından emin olmak Için güvenli Kullanıcı mağazasını SAP HANA denetleyin, `hdbuserstore` aşağıdaki örneğe benzer çıktıyı listelemek için komutunu kullanın. İle ilgili daha fazla ayrıntı `hdbuserstore` SAP web sitesinde bulunabilir.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>SAP HANA ile iletişim için SSL kullanma

`azacsnap`Araç, `hdbsql` SAP HANA iletişim kurmak için SAP HANA komutunu kullanır. Bu, SAP HANA ile iletişimi şifrelerken SSL seçeneklerinin kullanımını içerir. `azacsnap``hdbsql`komutun SSL seçeneklerini aşağıdaki gibi kullanır.

Seçeneği kullanılırken aşağıdakiler her zaman kullanılır `azacsnap --ssl` :

- `-e` -TLS encryptionTLS/SSL şifrelemesini etkinleştirilir. Sunucu kullanılabilir en yüksek sunucuyu seçer.
- `-ssltrustcert` -Sunucu sertifikasının doğrulanması gerekip gerekmediğini belirtir.
- `-sslhostnameincert "*"` -Sunucunun kimliğini doğrulamak için kullanılan ana bilgisayar adını belirtir. `"*"`Ana bilgisayar adı olarak belirterek sunucunun ana bilgisayar adı doğrulanmaz.

SSL iletişimi Ayrıca anahtar deposu ve güven deposu dosyaları gerektirir.  Bu dosyaların bir Linux yüklemesinde varsayılan konumlarda depolanması mümkün olsa da, çeşitli SAP HANA sistemleri için doğru anahtar malzemesinin kullanıldığından emin olmak için (diğer bir deyişle, her bir SAP HANA sistemi için farklı anahtar deposu ve güven deposu dosyalarının kullanıldığı durumlarda), `azacsnap` anahtar deposu ve güven deposu dosyalarının `securityPath` yapılandırma dosyasında belirtilen konumda depolanmasını bekler `azacsnap` .

#### <a name="key-store-files"></a>Anahtar deposu dosyaları

- Aynı anahtar malzemesiyle birden fazla SID kullanılıyorsa, yapılandırma dosyasında tanımlanan şekilde securityPath konumuna bağlantılar oluşturmak daha kolaydır `azacsnap` .  SSL kullanan her SID için bu değerlerin mevcut olduğundan emin olun.
  - OpenSSL için:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - Commonşifre için:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- SID başına farklı anahtar malzemesiyle birden çok SID kullanılıyorsa, dosyaları SID yapılandırma dosyasında tanımlanan securityPath konumuna kopyalayın (veya taşıyın ve yeniden adlandırın) `azacsnap` .
  - OpenSSL için:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - Commonşifre için:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

`azacsnap`Çağırdığında `hdbsql` , `-sslkeystore=<securityPath>/<SID>_keystore` komut satırına eklenir.

#### <a name="trust-store-files"></a>Güven deposu dosyaları

- Aynı anahtar malzemesine sahip birden fazla SID kullanılıyorsa, yapılandırma dosyasında tanımlanan securityPath konumuna sabit bağlantılar oluşturun `azacsnap` .  SSL kullanan her SID için bu değerlerin mevcut olduğundan emin olun.
  - OpenSSL için:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - Commonşifre için:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- SID başına farklı anahtar malzemesiyle birden çok SID kullanılıyorsa, dosyaları SID yapılandırma dosyasında tanımlanan securityPath konumuna kopyalayın (veya taşıyın ve yeniden adlandırın) `azacsnap` .
  - OpenSSL için:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - Commonşifre için:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> `<SID>`Dosya adlarının bileşeni, büyük harfle SAP HANA sistem tanımlayıcısı olmalıdır (örneğin,, `H80` `PR1` vb.)

`azacsnap`Çağırdığında `hdbsql` , `-ssltruststore=<securityPath>/<SID>_truststore` komut satırına eklenir.

Bu nedenle, `azacsnap -c test --test hana --ssl openssl` yapılandırma dosyasında olduğu yerde çalıştırıldığında `SID` `H80` `hdbsql` bağlantılar aşağıdaki gibi yürütülür:

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> `\`Karakter, komut satırına geçirilen birden çok parametrenin netliğini artırmak için bir komut satırı çizgisi sarması olur.

## <a name="installing-the-snapshot-tools"></a>Anlık görüntü araçlarını yükleme

İndirilebilir Self-yükleyici, anlık görüntü araçlarını, kök olmayan kullanıcı ayrıcalıklarıyla ayarlamayı ve çalıştırmayı kolay hale getirmek için tasarlanmıştır (örneğin, azacsnap). Yükleyici, kullanıcıyı ayarlar ve anlık görüntü araçlarını kullanıcılar `$HOME/bin` alt dizinine (varsayılan = `/home/azacsnap/bin` ) koyar.

Self-yükleyici, yüklemeyi gerçekleştiren kullanıcının yapılandırmasına bağlı olarak tüm dosyalar için doğru ayarları ve yolları belirlemeyi dener (örneğin, kök). Önceki kurulum adımları (depolama ile iletişimi etkinleştir ve SAP HANA) kök olarak çalıştırılmıştı, yükleme özel anahtarını ve öğesini `hdbuserstore` yedekleme kullanıcısının konumuna kopyalar. Ancak, depolama arka ucu ile iletişimi etkinleştiren ve yüklemeden sonra bilgili yönetici tarafından el ile yapılacak SAP HANA adımlar için de mümkündür.

> [!NOTE]
> Azure büyük örnek yüklemelerinde daha önceki SAP HANA için önceden yüklenmiş anlık görüntü araçlarının dizini idi `/hana/shared/<SID>/exe/linuxx86_64/hdb` .

[Önkoşul adımları](#prerequisites-for-installation) tamamlandığında, anlık görüntü araçlarını şu şekilde Self-Installer kullanarak yükleyebilirsiniz:

1. İndirilen Self-Installer ' i hedef sisteme kopyalayın.
1. Kullanıcı olarak kendi kendine yükleyiciyi yürütün `root` , aşağıdaki örneğe bakın. Gerekirse, komutunu kullanarak dosyayı çalıştırılabilir yapın `chmod +x *.run` .

Self-Installer komutunu herhangi bir bağımsız değişken olmadan çalıştırmak, aşağıdaki gibi anlık görüntü araçlarını yüklemek için yükleyiciyi kullanmayla ilgili yardım görüntüler:

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> Self-Installer, herhangi bir Kullanıcı oluşturma ve kurulum yapmadan, anlık görüntü araçlarını paketten ayıklama (-X) seçeneğine sahiptir. Bu, deneyimli bir yöneticinin kurulum adımlarını el ile tamamlamasını veya mevcut bir yüklemeyi yükseltmek için komutları kopyalamasını sağlar.

### <a name="easy-installation-of-snapshot-tools-default"></a>Anlık görüntü araçlarının kolay yüklemesi (varsayılan)

Yükleyici, Azure 'da SAP HANA için anlık görüntü araçları 'nı hızlı bir şekilde yüklemek üzere tasarlanmıştır. Varsayılan olarak, yükleyici yalnızca-I seçeneğiyle çalışıyorsa, aşağıdaki adımları uygulayın:

1. Anlık görüntü kullanıcısı ' azacsnap ', Ana Dizin oluşturma ve grup üyeliğini ayarlama.
1. Azacsnap Kullanıcı oturumunu yapılandırın `~/.profile` .
1. Azacsnap 'e eklenecek dizinler için dosya sistemi ara `$PATH` , bunlar genellikle ve gibi SAP HANA araçların yollarıdır `hdbsql` `hdbuserstore` .
1. Azacsnap 'e eklenecek dizinler için dosya sisteminde arama yapın `$LD_LIBRARY_PATH` . Birçok komut, doğru yürütülebilmesi için bir kitaplık yolunun ayarlanmış olmasını gerektirir, bu, onu yüklü Kullanıcı için yapılandırır.
1. "Kök" kullanıcıdan (yüklemeyi çalıştıran kullanıcı) azacsnap için, arka uç depolama için SSH anahtarlarını kopyalayın. Bu, "kök" kullanıcının depolama ile olan bağlantısını zaten yapılandırdığı varsayılmaktadır
    - "[depolama ile Iletişimi etkinleştir](#enable-communication-with-storage)" bölümüne bakın.
1. Hedef Kullanıcı için SAP HANA bağlantısı güvenli Kullanıcı deposunu kopyalayın, azacsnap. Bu, "kök" kullanıcının güvenli Kullanıcı deposunu zaten yapılandırmış olduğunu varsayar; "SAP HANA ile iletişimi etkinleştir" bölümüne bakın.
1. Anlık görüntü araçları içine ayıklanır `/home/azacsnap/bin/` .
1. İçindeki komutların `/home/azacsnap/bin/` izinleri ayarlanmış (sahiplik ve yürütülebilir bit, vb.).

Aşağıdaki örnek, varsayılan yükleme seçeneği ile çalıştırıldığında yükleyicinin doğru çıktısını gösterir.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>Anlık görüntü araçlarını kaldırma

Anlık görüntü araçları varsayılan ayarlar kullanılarak yüklenmişse, kaldırma işlemi yalnızca kullanıcının yüklenmesini gerektirir (varsayılan = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>Anlık görüntü araçlarının el ile yüklenmesi

Bazı durumlarda, araçların el ile yüklenmesi gerekir, ancak bu işlemi kolaylaştırmak için yükleyicinin varsayılan seçeneğini kullanmaktır.

Bir karakterle başlayan her satır, `#` kök kullanıcı tarafından çalıştırılan karakteri izleyen örnek komutları gösterir. `\`Bir satırın sonunda, bir kabuk komutu için standart satır devamlılık karakteridir.

Kök süper kullanıcı olarak, el ile yükleme aşağıdaki gibi elde edilebilir:

1. Bu durumda Grup KIMLIĞI = 1010 olan "sapsys" Grup KIMLIĞINI alın

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. ' Azacsnap ' anlık görüntü kullanıcısını oluşturun, Ana Dizin ve 1. adımdaki grup KIMLIĞINI kullanarak Grup üyeliği ayarlayın.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Kullanıcı azacsnap oturumunun mevcut olduğundan emin olun `.profile` .

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Azacsnap 'in $PATH eklenecek dizinler için dosya sistemi ara, bunlar genellikle ve gibi SAP HANA araçların yollarıdır `hdbsql` `hdbuserstore` .

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Güncelleştirilmiş yolu kullanıcının profiline ekle

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Azacsnap 'in $LD _LIBRARY_PATH eklenecek dizinler için dosya sistemi arayın.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Güncelleştirilmiş kitaplık yolunu kullanıcının profiline ekle

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. Azure büyük örneklerinde
    1. "Kök" kullanıcıdan (yüklemeyi çalıştıran kullanıcı) azacsnap için, arka uç depolama için SSH anahtarlarını kopyalayın. Bu, "kök" kullanıcının depolama ile olan bağlantısını zaten yapılandırdığı varsayılmaktadır
       > "[depolama ile Iletişimi etkinleştir](#enable-communication-with-storage)" bölümüne bakın.

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. SSH dosyaları için Kullanıcı izinlerini doğru ayarlama

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. Azure NetApp Files
    1. Kullanıcının `DOTNET_BUNDLE_EXTRACT_BASE_DIR` yolunu .NET Core tek dosya ayıklama kılavuzumu uyarınca yapılandırın.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Hedef Kullanıcı için SAP HANA bağlantısı güvenli Kullanıcı deposunu kopyalayın, azacsnap. Bu, "kök" kullanıcının güvenli Kullanıcı deposunu zaten yapılandırmış olduğunu varsayar.
    > "[SAP HANA ile Iletişimi etkinleştir](#enable-communication-with-sap-hana)" bölümüne bakın.

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. Dosyalar için Kullanıcı izinlerini doğru ayarlama `hdbuserstore`

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. Anlık görüntü araçlarını/Home/azacsnap/bin/. 'ye Ayıkla

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. Komutları çalıştırılabilir yapın

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. Kullanıcının giriş dizininde doğru sahiplik izinlerinin ayarlandığından emin olun

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>Anlık görüntü araçlarının kurulumunu tamamlar

Yükleyici, anlık görüntü araçlarının yüklenmesi yapıldıktan sonra tamamlanması gereken adımları sağlar.
Anlık görüntü araçlarını yapılandırmak ve test etmek için aşağıdaki adımları izleyin.  Başarılı sınamadan sonra, ilk veritabanı tutarlı depolama anlık görüntüsünü gerçekleştirin.

Aşağıdaki çıktıda, yükleyici varsayılan yükleme seçenekleriyle çalıştırıldıktan sonra tamamlanacak adımlar gösterilmektedir:

1. Anlık görüntü Kullanıcı hesabına geçin
    1. `su - azacsnap`
1. HANA güvenli Kullanıcı deposunu ayarlama
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Komutların konumuna Değiştir
   1. `cd /home/azacsnap/bin/`
1. Müşteri ayrıntıları dosyasını yapılandırma
   1. `azacsnap -c configure –-configuration new`
1. Depolama bağlantısını sına.....
   1. `azacsnap -c test –-test storage`
1. HANA bağlantısını test edin.....
    1. SSL olmadan
       1. `azacsnap -c test –-test hana`
    1. SSL ile doğru SSL seçeneğini belirlemeniz gerekir
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. İlk anlık görüntü yedeklemenizi çalıştırın
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

"[SAP HANA ile Iletişimi etkinleştir](#enable-communication-with-sap-hana)" ayarı, yüklemeden önce yapılmadığından, adım 2 gereklidir.

> [!NOTE]
> Test komutları doğru şekilde yürütülmelidir. Aksi takdirde, komutlar başarısız olabilir.

## <a name="configuring-the-database"></a>Veritabanını yapılandırma

Bu bölümde, veri tabanının nasıl yapılandırılacağı açıklanmaktadır.

### <a name="sap-hana-configuration"></a>SAP HANA yapılandırması

Günlük yedeklerinin ve kataloğunun korunmasını sağlamak için SAP HANA uygulanması önerilen bazı değişiklikler vardır. Varsayılan olarak, `basepath_logbackup` ve `basepath_catalogbackup` dosyalarını `$(DIR_INSTANCE)/backup/log` dizine çıktılar ve bu yolun anlık görüntü olarak yapılandırılmış bir birimde olması olası değildir ve `azacsnap` depolama anlık görüntüleri ile korunmaz.

Aşağıdaki `hdbsql` komut örnekleri, tarafından anlık görüntü olabilecek depolama birimlerinde bulunan konumlara günlük ve Katalog yolları ayarlamayı göstermeye yöneliktir `azacsnap` . Komut satırındaki değerleri yerel SAP HANA yapılandırmasıyla eşleşecek şekilde denetlediğinizden emin olun.

### <a name="configure-log-backup-location"></a>Günlük yedekleme konumunu yapılandırma

Bu örnekte, parametre için değişiklik yapılıyor `basepath_logbackup` .

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>Katalog yedekleme konumunu Yapılandır

Bu örnekte, parametre için değişiklik yapılıyor `basepath_catalogbackup` . İlk olarak, `basepath_catalogbackup` Dizin ile aynı sahiplik ile yolu oluşturmadığından, yolun dosya sisteminde bulunduğundan emin olun.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Yolun oluşturulması gerekiyorsa aşağıdaki örnek yolu oluşturur ve doğru sahiplik ve izinleri ayarlar. Bu komutların kök olarak çalıştırılması gerekir.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Aşağıdaki örnek SAP HANA ayarını değiştirecek.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>Günlük ve Katalog yedekleme konumlarını denetleme

Yukarıdaki değişiklikleri yaptıktan sonra, aşağıdaki komutla ayarların doğru olduğundan emin olun.
Bu örnekte, yukarıdaki kılavuzdan sonra ayarlanan ayarlar SISTEM ayarları olarak görüntülenecektir.

> Bu sorgu, karşılaştırma için varsayılan ayarları da döndürür.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>Günlük yedeklemesi zaman aşımını yapılandırma

Günlük yedeklemesi gerçekleştirmek için SAP HANA varsayılan ayarı 900 saniyedir (15 dakika). Bu değerin 300 saniyeye (5 dakika) azaltılması önerilir.  Daha sonra, yapılandırma dosyasının DIĞER birim bölümüne log_backups birimini ekleyerek normal yedeklemeleri (örneğin, her 10 dakikada bir) çalıştırmak mümkündür.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>Günlük yedeklemesi zaman aşımını denetle

Günlük yedekleme zaman aşımında değişiklik yaptıktan sonra, bunun aşağıdaki şekilde ayarlandığından emin olun.
Bu örnekte, ayarlanan ayarlar SISTEM ayarları olarak görüntülenir, ancak bu sorgu karşılaştırma için varsayılan ayarları da döndürür.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure uygulaması tutarlı anlık görüntü aracını yapılandırma](azacsnap-cmd-ref-configure.md)
