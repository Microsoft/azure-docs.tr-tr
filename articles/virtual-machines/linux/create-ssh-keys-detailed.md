---
title: Ayrıntılı adımlar-Azure Linux VM 'Leri için SSH anahtar çifti
description: Azure 'da Linux VM 'Ler için SSH ortak ve özel anahtar çifti oluşturma ve yönetme hakkında ayrıntılı adımlar hakkında bilgi edinin.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/17/2018
ms.author: cynthn
ms.openlocfilehash: 61f24776bb9ec9443df421dcbcf35dcc83ec2bc9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036502"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Ayrıntılı adımlar: Azure 'da bir Linux VM 'sine kimlik doğrulaması için SSH anahtarları oluşturma ve yönetme 
Güvenli Kabuk (SSH) anahtar çifti ile Azure 'da, kimlik doğrulaması için SSH anahtarlarını kullanan varsayılan bir Linux sanal makinesi oluşturabilir ve parolaların oturum açması gereksinimini ortadan kaldırabilirsiniz. Azure portal, Azure CLı, Kaynak Yöneticisi şablonları veya diğer araçlarla oluşturulan VM 'Ler, SSH bağlantıları için SSH anahtarı kimlik doğrulamasını ayarlayan dağıtımın bir parçası olarak SSH ortak anahtarınızı içerebilir. 

Bu makalede SSH istemci bağlantıları için SSH RSA genel özel anahtar dosya çifti oluşturma ve yönetme hakkında ayrıntılı arka plan ve adımlar sağlanmaktadır. Hızlı komutları istiyorsanız bkz. [Azure 'Da Linux VM 'ler IÇIN SSH genel-özel anahtar çifti oluşturma](mac-create-ssh-keys.md).

Windows bilgisayarda SSH anahtarları oluşturma ve kullanma hakkında ek yollar için bkz. [Azure 'Da Windows Ile SSH anahtarlarını kullanma](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Özel anahtar parolası
SSH özel anahtarının korunması için çok güvenli bir parola olmalıdır. Bu parola yalnızca özel SSH anahtar dosyasına erişim için kullanılır ve Kullanıcı hesabı parolası *değildir* . SSH anahtarınıza bir parola eklediğinizde, özel anahtarın şifresini çözmek için parola olmadan kullanılamaz olması için, özel anahtarı 128 bit AES kullanarak şifreler. Bir saldırgan özel anahtarınızı ve bu anahtarın bir parolası yoksa, bu özel anahtarı ilgili ortak anahtara sahip herhangi bir sunucuda oturum açmak için kullanabilir. Özel anahtar bir parola ile korunuyorsa, Azure 'daki altyapınız için ek bir güvenlik katmanı sağlayan bu saldırgan tarafından kullanılamaz.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>SSH anahtarlarının kullanımı ve avantajları

Ortak anahtarı belirterek bir Azure VM oluşturduğunuzda, Azure ortak anahtarı (`.pub` biçiminde) VM 'deki `~/.ssh/authorized_keys` klasörüne kopyalar. `~/.ssh/authorized_keys` SSH anahtarları, istemcinin bir SSH bağlantısında karşılık gelen özel anahtarla eşleşmesini sağlamak için kullanılır. Kimlik doğrulaması için SSH anahtarları kullanan bir Azure Linux sanal makinesinde Azure, SSHD sunucusunu parola oturum açma ve yalnızca SSH anahtarlarına izin verecek şekilde yapılandırır. Bu nedenle, SSH anahtarlarıyla bir Azure Linux VM 'si oluşturarak, VM dağıtımının güvenliğini sağlamaya ve `sshd_config` dosyasında parolaları devre dışı bırakmanın tipik dağıtım sonrası yapılandırma adımını kaydetmenizi sağlayabilirsiniz.

SSH anahtarlarını kullanmak istemiyorsanız, Linux sanal makinenizin parolasını parola kimlik doğrulaması kullanacak şekilde ayarlayabilirsiniz. SANAL makinenizin Internet 'e açık olmaması durumunda parolaların kullanılması yeterli olabilir. Ancak, her bir Linux sanal makinesi için parolalarınızı yönetmeniz ve minimum parola uzunluğu ve normal güncelleştirmeler gibi sağlıklı parola ilkelerini ve uygulamaları korumanız gerekir. SSH anahtarlarının kullanılması, birden çok VM genelinde bireysel kimlik bilgilerini yönetmenin karmaşıklığını azaltır.

## <a name="generate-keys-with-ssh-keygen"></a>SSH-keygen ile anahtar oluşturma

Anahtarları oluşturmak için, tercih edilen bir komut, Azure Cloud Shell, macOS veya Linux ana bilgisayarı, [Linux Için Windows alt sistemi](https://docs.microsoft.com/windows/wsl/about)ve diğer araçlar Ile birlikte OpenSSH yardımcı programları ile kullanılabilir `ssh-keygen`. `ssh-keygen` bir dizi soru sorar ve sonra özel bir anahtar ve eşleşen bir ortak anahtar yazar. 

SSH anahtarları, varsayılan olarak `~/.ssh` dizininde tutulur.  `~/.ssh` dizininiz yoksa `ssh-keygen` komutu, doğru izinler ile sizin için oluşturur.

### <a name="basic-example"></a>Temel örnek

Aşağıdaki `ssh-keygen` komutu, `~/.ssh` dizininde varsayılan olarak 2048 bit SSH RSA ortak ve özel anahtar dosyaları üretir. Geçerli konumda bir SSH anahtar çifti varsa, bu dosyaların üzerine yazılır.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Ayrıntılı örnek
Aşağıdaki örnek, bir SSH RSA anahtar çifti oluşturmak için ek komut seçeneklerini gösterir. Geçerli konumda bir SSH anahtar çifti varsa, bu dosyaların üzerine yazılır. 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Komut açıklaması**

`ssh-keygen` = anahtarları oluşturmak için kullanılan program

`-t rsa` = bu durumda, RSA biçiminde oluşturulacak anahtarın türü

`-b 4096` = anahtardaki bit sayısı, bu durumda 4096

`-C "azureuser@myserver"` = kolayca tanımlamak için ortak anahtar dosyasının sonuna eklenen bir açıklama. Normalde açıklama olarak bir e-posta adresi kullanılır, ancak altyapınız için en iyi şeyi kullanın.

`-f ~/.ssh/mykeys/myprivatekey` = varsayılan adı kullanmayı tercih ederseniz, özel anahtar dosyasının dosya adı. `.pub` eklenmiş karşılık gelen ortak anahtar dosyası aynı dizinde oluşturulur. Dizin var olmalıdır.

`-N mypassphrase` = özel anahtar dosyasına erişmek için kullanılan ek bir parola. 

### <a name="example-of-ssh-keygen"></a>ssh-keygen örneği

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
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

#### <a name="saved-key-files"></a>Kayıtlı anahtar dosyaları

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Bu makale için anahtar çifti adı. `id_rsa` adlı bir anahtar çiftinin olması varsayılandır; Bazı araçlar `id_rsa` özel anahtar dosya adını bekleyebilir, bu nedenle bir tane olması iyi bir fikirdir. `~/.ssh/` dizini, SSH anahtar çiftleri ve SSH yapılandırma dosyası için varsayılan konumdur. Tam yol belirtilmezse `ssh-keygen` tarafından oluşturulan anahtarlar varsayılan `~/.ssh` dizininde değil geçerli çalışma dizininde olur.

#### <a name="list-of-the-ssh-directory"></a>`~/.ssh` dizininin listesi

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Anahtar parolası

`Enter passphrase (empty for no passphrase):`

Özel anahtarınıza bir parola eklemeniz *önemle* önerilir. Anahtar dosyasını korumak için bir parola olmadan, dosyayı içeren herkes ilgili ortak anahtara sahip herhangi bir sunucuda oturum açmak için kullanabilir. Bir parola eklemek, birinin özel anahtar dosyanıza erişim sağlayabilmesi durumunda anahtarları değiştirmeniz için size zaman kazandırmakta daha fazla koruma sağlar.

## <a name="generate-keys-automatically-during-deployment"></a>Dağıtım sırasında anahtarları otomatik olarak oluştur

VM 'nizi oluşturmak için [Azure CLI](/cli/azure) kullanıyorsanız, isteğe bağlı olarak, `--generate-ssh-keys` seçeneği ile [az VM Create](/cli/azure/vm) komutunu çalıştırarak SSH ortak ve özel anahtar dosyaları oluşturabilirsiniz. Anahtarlar ~/PST SSH dizininde depolanır. Bu komut seçeneğinin, bu konumda zaten varsa anahtarların üzerine yazılmadığını unutmayın.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>VM dağıtımında SSH ortak anahtarı sağla

Kimlik doğrulaması için SSH anahtarları kullanan bir Linux sanal makinesi oluşturmak için Azure portal, CLı, Kaynak Yöneticisi şablonları veya diğer yöntemleri kullanarak VM oluştururken SSH ortak anahtarınızı sağlayın. Portalı kullanırken, ortak anahtarın kendisini girersiniz. VM 'nizi mevcut bir ortak anahtarla oluşturmak için [Azure CLI](/cli/azure) kullanıyorsanız, `--ssh-key-value` seçeneği ile [az VM Create](/cli/azure/vm) komutunu çalıştırarak bu ortak anahtarın değerini veya konumunu belirtin. 

SSH ortak anahtarının biçimiyle ilgili bilgi sahibi değilseniz, `cat` aşağıdaki gibi çalıştırarak ortak anahtarınızı görebilir ve `~/.ssh/id_rsa.pub` kendi ortak anahtar dosya konumınızla değiştirin:

```bash
cat ~/.ssh/id_rsa.pub
```

Çıktı aşağıdakine benzer (burada Redaksiyonu yapılmış):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Ortak anahtar dosyasının içeriğini kopyalayıp Azure portal veya bir Kaynak Yöneticisi şablonuna yapıştırırsanız, ek boşluk kopyalamayın veya ek satır sonları eklediğinizden emin olun. Örneğin, macOS kullanıyorsanız, içeriği kopyalamak için ortak anahtar dosyasını (varsayılan olarak, `~/.ssh/id_rsa.pub`) **pbcopy** öğesine (`xclip`gibi aynı şeyi yapan diğer Linux programları vardır) kanal oluşturarak aktarabilirsiniz.

Çok satırlı biçimdeki bir ortak anahtar kullanmayı tercih ederseniz, daha önce oluşturduğunuz ortak anahtardan bir ped kapsayıcısında RFC4716 biçimli bir anahtar oluşturabilirsiniz.

Var olan bir SSH ortak anahtarından RFC4716 biçimli bir anahtar oluşturmak için:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH istemcisiyle sanal makinenize SSH
Azure sanal makinenize dağıtılan ortak anahtar ve yerel sisteminizdeki özel anahtar ile sanal makinenizin IP adresini veya DNS adını kullanarak sanal makinenize SSH 'yi kullanın. Aşağıdaki komutta *azureuser* ve *myvm.westus.cloudapp.Azure.com* değerini Yönetici Kullanıcı adı ve tam etki alanı adı (veya IP adresi) ile değiştirin:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Anahtar çiftinizi oluştururken bir parola sağladıysanız, oturum açma işlemi sırasında istendiğinde parolayı girin. (Sunucu `~/.ssh/known_hosts` klasörünüze eklenir ve Azure VM’nizdeki ortak anahtar değiştirilene veya sunucu adı `~/.ssh/known_hosts` konumundan kaldırılana kadar yeniden bağlanmanız istenmez.)

VM tam zamanında erişim ilkesi kullanıyorsa, VM 'ye bağlanabilmeniz için önce erişim istemeniz gerekir. Tam zamanında ilkesi hakkında daha fazla bilgi için, bkz. [tam zamanında ilkesini kullanarak sanal makine erişimini yönetme](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Özel anahtar parolanızı depolamak için SSH-Agent kullanın

Her SSH oturum açma ile özel anahtar dosya parolanızı yazmadan kaçınmak için, `ssh-agent` kullanarak özel anahtar dosya parolanızı önbelleğe alabilirsiniz. Mac kullanıyorsanız, `ssh-agent`çağırdığınızda macOS anahtarlığı özel anahtar parolasını güvenli bir şekilde depolar.

Parolayı etkileşimli olarak kullanmanıza gerek kalmaması için, SSH sistemine anahtar dosyaları hakkında bilgi vermek üzere `ssh-agent` ve `ssh-add` kullanın.

```bash
eval "$(ssh-agent -s)"
```

Şimdi `ssh-agent` komutunu kullanarak özel anahtarı `ssh-add` öğesine ekleyin.

```bash
ssh-add ~/.ssh/id_rsa
```

Özel anahtar parolası artık `ssh-agent`depolanır.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Anahtarı mevcut bir sanal makineye kopyalamak için SSH-Copy-ID kullanın
Zaten bir VM oluşturduysanız, aşağıdaki gibi bir komutla Linux sanal makinenize yeni SSH ortak anahtarını yükleyebilirsiniz:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH yapılandırma dosyası oluşturma ve yapılandırma

, Oturum açma işlemlerini hızlandırmak ve SSH istemci davranışınızı iyileştirmek için bir SSH yapılandırma dosyası (`~/.ssh/config`) oluşturup yapılandırabilirsiniz. 

Aşağıdaki örnek, varsayılan SSH özel anahtarını kullanarak belirli bir VM 'ye Kullanıcı olarak hızlı bir şekilde oturum açmak için kullanabileceğiniz basit bir yapılandırmayı gösterir. 

### <a name="create-the-file"></a>Önbelleği oluşturma

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Yeni SSH yapılandırmasını eklemek için dosyayı düzenleyin

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Örnek yapılandırma

Ana bilgisayar VM 'niz için uygun yapılandırma ayarlarını ekleyin.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Her birinin kendi adanmış anahtar çiftini kullanmasını sağlamak için ek konaklar için yapılandırma ekleyebilirsiniz. Daha gelişmiş yapılandırma seçenekleri için bkz. [SSH yapılandırma dosyası](https://www.ssh.com/ssh/config/) .

Artık bir SSH anahtar çiftiniz ve yapılandırılmış bir SSH yapılandırma dosyasına sahip olduğunuza göre, Linux VM 'niz üzerinde hızlı ve güvenli bir şekilde oturum açabiliyor olabilirsiniz. Aşağıdaki komutu çalıştırdığınızda SSH, SSH yapılandırma dosyasında `Host myvm` bloğundan tüm ayarları bulur ve yükler.

```bash
ssh myvm
```

Bir SSH anahtarı kullanarak bir sunucuda ilk kez oturum açtığınızda, komut sizden bu anahtar dosyası için parola ister.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adım, yeni SSH ortak anahtarını kullanarak Azure Linux VM’ler oluşturmaktır. Oturum açma olarak SSH ortak anahtarıyla oluşturulan Azure VM 'Ler, varsayılan oturum açma yöntemiyle, Parolalarla oluşturulan VM 'lerden daha güvenlidir.

* [Azure portal bir Linux sanal makinesi oluşturun](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLı ile Linux sanal makinesi oluşturma](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure şablonu kullanarak bir Linux VM oluşturma](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
