---
title: "Azure’da Linux VM&quot;ler için SSH anahtar çifti oluşturma | Microsoft Docs"
description: "Azure Linux VM&quot;leri için güvenli bir şekilde SSH ortak ve özel anahtar çifti oluşturun."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: rasquill
experiment_id: rasquill-ssh-20170308
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19acd4efca7ef043f31b436b96f9129caee9591b
ms.lasthandoff: 04/03/2017



---

# <a name="create-an-ssh-public-and-private-key-pair-for-linux-vms"></a>Linux VM'ler için SSH ortak ve özel anahtar çifti oluşturma

Bu makalede Linux VM'lerle kullanmak üzere bir SSH protokolü sürüm 2 RSA ortak ve özel anahtar dosyası çifti oluşturma adımları anlatılmaktadır.  SSH anahtar çiftiyle Azure'da Sanal Makineler oluşturabilirsiniz. Bu sayede kimlik doğrulaması için SSH anahtarlarının kullanımını varsayılan hale getirerek oturum açmak için parolalara duyulan gereksinimi ortadan kaldırırsınız.  Parolalar tahmin edilebilir ve sanal makinelerinizi, parolanızı tahmin etmeye yönelik sayısız girişime maruz bırakır. Azure Şablonları veya `azure-cli` ile oluşturulan sanal makineler, dağıtımın bir parçası olarak SSH ortak anahtarınızı içerebilir; böylece dağıtım sonrası SSH için parolayla girişleri devre dışı bırakma yapılandırma gereksinimini ortadan kaldırır.

## <a name="quick-commands"></a>Hızlı Komutlar

Aşağıdaki komutları bir Bash kabuğundan çalıştırın ve örnekleri kendi seçtiklerinizle değiştirin.

SSH ortak anahtar dosyanız varsayılan olarak `~/.ssh/id_rsa.pub` konumunda oluşturulur. Aşağıdaki komutu kullanmanız istendiğinde, özel anahtarınızı güvenli hale getirmek için bir "parola" oluşturmanız gerekir. (Parola, özel anahtarınızı şifrelemek için kullanılan paroladır.)

```bash
ssh-keygen -t rsa -b 2048 
```

Yeni oluşturulan anahtarı `ssh-agent` içine ekleyin:

```bash
ssh-add ~/.ssh/id_rsa
```

> [!IMPORTANT] 
> Yukarıdaki komutlar neredeyse tüm Linux işletim sistemi dağıtımlarında çalışır ancak ortam önemli ölçüde sınırlanmış olabileceğinden kapsayıcılarda çalışmaz. 

## <a name="detailed-walkthrough"></a>Ayrıntılı Kılavuz

SSH ortak ve özel anahtarlarını kullanmak, Linux sunucularınızda oturum açmanın en kolay yoludur. [Ortak anahtar şifrelemesi](https://en.wikipedia.org/wiki/Public-key_cryptography), Azure'daki Linux veya BSD VM'nizde oturum açmak için parolalara kıyasla çok daha güvenli bir yol sunar. Parolalar, saldırılara çok daha kolay şekilde maruz kalır.

> [!IMPORTANT]
> Ortak anahtarınız herkesle paylaşılabilir; ancak, özel anahtarınıza yalnızca siz (veya yerel güvenlik altyapınız) sahip olursunuz.  SSH özel anahtarının korunması için [çok güvenli parola](https://www.xkcd.com/936/) (kaynak:[xkcd.com](https://xkcd.com)) gereklidir.  Bu parola yalnızca özel SSH anahtarına erişim için kullanılır ve kullanıcı hesabı parolası **değildir**.  SSH anahtarınıza parola eklediğinizde bu parola özel anahtarı 128 bit AES kullanarak şifreler; böylece özel anahtar, şifresini çözen parola olmadan kullanılamaz.  Bir saldırganın özel anahtarınızı çalması ve bu anahtarın bir parolasının olmaması halinde saldırgan, bu özel anahtarı ilgili ortak anahtara sahip sunucularınızda oturum açmak için kullanabilir.  Özel anahtar parola korumalı ise, Azure’daki altyapınız için ek bir güvenlik katmanı sağlayarak, bu saldırgan tarafından kullanılamaz.

Bu makalede, Resource Manager üzerindeki dağıtımlar için önerilen SSH protokolü sürüm 2 RSA ortak ve özel anahtar dosyaları oluşturulmaktadır.  Hem klasik hem de Resource Manager dağıtımları için [portalda](https://portal.azure.com) *ssh-rsa* anahtarları gereklidir.

## <a name="disable-ssh-passwords-by-using-ssh-keys"></a>SSH anahtarlarını kullanarak SSH parolalarını devre dışı bırakma

Azure en az 2048 bit, ssh-rsa biçimli ortak ve özel anahtarlar gerektirir. Anahtarları oluşturmak için `ssh-keygen` kullanın. Bu, bir dizi soru sorduktan sonra özel bir anahtar ve eşleşen bir ortak anahtar yazar. Azure VM oluşturulduğunda ortak anahtar şuraya kopyalanır: `~/.ssh/authorized_keys`.  `~/.ssh/authorized_keys` içindeki SSH anahtarları, bir istemcinin SSH oturum açma bağlantısındaki ilgili özel anahtarla eşleşip eşleşmediğini sınar.  Kimlik doğrulama için SSH anahtarları kullanılarak bir Azure Linux VM oluşturulduğunda Azure, SSHD sunucusunu parolayla girişleri devre dışı bırakarak yalnızca SSH anahtarlarına izin verecek şekilde yapılandırır.  Dolayısıyla, SSH anahtarlarıyla Azure Linux VM'leri oluşturarak VM dağıtımının güvenliğini sağlamaya yardımcı olabilirsiniz ve dağıtım sonrasında sshd_config yapılandırma dosyasında parolaları devre dışı bırakma yapılandırma adımının uygulanmasına gerek kalmaz.

## <a name="using-ssh-keygen"></a>SSH-keygen’i kullanma

Bu komut, 2048 bit RSA kullanarak parola korumalı (şifrelenmiş) bir SSH anahtar çifti oluşturur; kolayca anlaşılabilmesi için komuta açıklama eklenir.  

SSH anahtarları, varsayılan olarak `~/.ssh` dizininde tutulur.  `~/.ssh` dizininiz yoksa `ssh-keygen` komutu, doğru izinler ile sizin için oluşturur.

```bash
ssh-keygen \
-t rsa \
-b 2048 
```

*Komut açıklaması*

`ssh-keygen` = anahtarları oluşturmak için kullanılan program

`-t rsa` = Oluşturulacak olan anahtar türü; burada RSA biçimindedir [vikipedi](https://tr.wikipedia.org/wiki/RSA)

`-b 2048` = anahtar bitleri


## <a name="classic-portal-and-x509-certs"></a>Klasik portal ve X.509 sertifikaları

Azure [klasik portalını](https://manage.windowsazure.com/) kullanıyorsanız SSH anahtarları için X.509 kullanmanız gerekir.  Diğer SSH ortak anahtar türlerine izin verilmez, tümünün X.509 sertifikası *olması gerekir*.

Var olan SSH-RSA özel anahtarınızdan X.509 sertifikası oluşturmak için:

```bash
openssl req -x509 \
-key ~/.ssh/id_rsa \
-nodes \
-days 365 \
-newkey rsa:2048 \
-out ~/.ssh/id_rsa.pem
```

## <a name="classic-deploy-using-asm"></a>`asm` kullanarak klasik dağıtım

Klasik dağıtım modelini kullanıyorsanız (Azure hizmet yönetimi CLI `asm`) bir **.pem** kapsayıcısındaki SSH-RSA ortak anahtarını veya RFC4716 biçimindeki anahtarı kullanabilirsiniz.  SSH-RSA ortak anahtarı, bu makalenin önceki bölümlerinde `ssh-keygen` kullanılarak oluşturulan anahtardır.

Var olan bir SSH ortak anahtarından RFC4716 biçimli bir anahtar oluşturmak için:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>ssh-keygen örneği

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ahmet/.ssh/id_rsa.
Your public key has been saved in /home/ahmet/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Kaydedilen anahtar dosyaları:

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): ~/.ssh/id_rsa`

Bu makale için anahtar çifti adı.  Anahtar çiftine **id_rsa** adı verilmesi varsayılandır ve bazı araçlar **id_rsa** özel anahtar adı bekleyebilir, bu nedenle bir tane olması iyi bir fikirdir. `~/.ssh/` dizini, SSH anahtar çiftleri ve SSH yapılandırma dosyası için varsayılan konumdur.  Tam yol belirtilmezse `ssh-keygen` tarafından oluşturulan anahtarlar varsayılan `~/.ssh` dizininde değil geçerli çalışma dizininde olur.

`~/.ssh` dizininin listesi.

```bash
ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 rsa.pub
```

Anahtar Parolası:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`, özel anahtarı "parola" olarak şifrelemek için kullanılan bir parolayı ifade eder.  Anahtar çiftlerinize bir parola eklemeniz *önemle* önerilir. Özel anahtarı koruyan bir parola olmadığında, anahtar dosyasına sahip herkes bunu ilgili ortak anahtara sahip herhangi bir sunucuda oturum açmak için kullanabilir. Bir parola eklemek, birinin özel anahtar dosyanıza erişim sağlama ihtimaline karşı, kimliğinizi doğrulamak için kullanılan anahtarları değiştirmeniz için size zaman tanıyarak daha fazla koruma sağlar.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Özel anahtar parolanızı depolamak için ssh-agent kullanma

Her SSH oturum açma işleminde özel anahtar dosya parolanızı yazmamak için `ssh-agent` kullanarak özel anahtar dosya parolanızı önbelleğe alabilirsiniz. Mac kullanıyorsanız `ssh-agent` öğesini çağırdığınızda OSX Anahtar Zinciri, özel anahtar parolalarını güvenli bir şekilde depolar.

SSH sistemini anahtar dosyaları hakkında bilgilendirerek parola kullanma gereksinimini ortadan kaldırmak için `ssh-agent` ve `ssh-add` bilgilerini doğrulayıp kullanın.

```bash
eval "$(ssh-agent -s)"
```

Şimdi `ssh-add` komutunu kullanarak özel anahtarı `ssh-agent` öğesine ekleyin.

```bash
ssh-add ~/.ssh/id_rsa
```

Özel anahtar parolası artık `ssh-agent` içinde depolanmış olur.

## <a name="using-ssh-copy-id-to-install-the-new-key"></a>Yeni anahtarı yüklemek için `ssh-copy-id` kullanma
Zaten bir VM oluşturduysanız, VM kullanıcı adını ve sunucu adresini kendi değerlerinizle değiştirerek aşağıdaki komutla yeni SSH ortak anahtarını Linux sanal makinenize yükleyebilirsiniz:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH yapılandırma dosyası oluşturma ve yapılandırma

Oturum açma işlemlerini hızlandırmak ve SSH istemci davranışınızı iyileştirmek için en iyi uygulama olarak bir `~/.ssh/config` dosyası oluşturmanız ve yapılandırmanız önerilir.

Aşağıdaki örnek, standart bir yapılandırmayı gösterir.

### <a name="create-the-file"></a>Önbelleği oluşturma

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Yeni SSH yapılandırması eklemek için dosyayı düzenleme:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Örnek `~/.ssh/config` dosyası:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Bu SSH yapılandırması, size her sunucu için bölümler sunarak her birinin kendi özel anahtar çiftinin olmasını sağlar. Varsayılan ayarlar (`Host *`), üst düzey yapılandırma dosyasındaki belirli ana bilgisayarların hiçbiriyle eşleşmeyen tüm ana bilgisayarlar içindir.

### <a name="config-file-explained"></a>Yapılandırma dosyası açıklaması

`Host` = terminal üzerinde çağrılan ana bilgisayar adı.  `ssh fedora22`, `SSH`'ye `Host fedora22` etiketli ayarlar bloğundaki değerlerin kullanılması gerektiğini bildirir. NOT: Konak, kullanımınız için mantıklı olan ve bir sunucunun gerçek konak adını temsil etmeyen herhangi bir etiket olabilir.

`Hostname 102.160.203.241` = erişim sağlanan sunucuya ilişkin IP adresi veya DNS adı.

`User ahmet` = sunucuda oturum açarken kullanılacak uzak kullanıcı hesabı.

`PubKeyAuthentication yes`= SSH’ye, oturum açmak için bir SSH anahtarı kullanmak istediğinizi iletir.

`IdentityFile /home/ahmet/.ssh/id_id_rsa`, = kimlik doğrulaması için kullanılacak SSH özel anahtarı ve ilgili ortak anahtar.

## <a name="ssh-into-linux-without-a-password"></a>Parolasız Linux içine SSH

Artık bir SSH anahtar çiftiniz ve yapılandırılmış bir SSH yapılandırma dosyasına sahip olduğunuza göre Linux VM'nizde hızlı ve güvenli şekilde oturum açabilirsiniz. SSH anahtarı kullanarak bir sunucuda ilk oturum açışınızda, komut sizden bu anahtar dosyasına ilişkin parolayı ister.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Komut açıklaması

`ssh fedora22` yürütüldüğünde, SSH önce `Host fedora22` bloğundaki tüm ayarları bulur ve yükler ve sonra son blok olan `Host *` içindeki kalan tüm ayarları yükler.

## <a name="next-steps"></a>Sonraki Adımlar

Sonraki adım, yeni SSH ortak anahtarını kullanarak Azure Linux VM’ler oluşturmaktır.  Oturum açma işlemi için SSH ortak anahtarıyla oluşturulan Azure VM'ler, varsayılan oturum açma yöntemiyle (parolalar) oluşturulan VM'lere göre daha güvenlidir.  SSH anahtarları kullanılarak oluşturulan Azure VM'ler, varsayılan olarak parola kullanımı devre dışı bırakılmış şekilde yapılandırılır; böylece parola tahminiyle gerçekleştirilen saldırı girişimleri önlenir. SSH anahtar çiftinizi oluşturma konusunda daha fazla yardıma ihtiyacınız varsa veya klasik portal ile kullanılanlar gibi ek sertifikalara gereksinim duyuyorsanız bkz. [SSH anahtar çiftleri ve sertifikalar oluşturmak için ayrıntılı adımlar](create-ssh-keys-detailed.md).

* [Azure şablonu kullanarak güvenli bir Linux VM oluşturma](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure portalını kullanarak güvenli bir Linux VM oluşturma](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI kullanarak güvenli bir Linux VM oluşturma](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

