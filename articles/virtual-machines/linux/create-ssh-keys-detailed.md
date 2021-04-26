---
title: SSH anahtar çifti oluşturmak için ayrıntılı adımlar
description: Azure 'da Linux VM 'Ler için SSH ortak ve özel anahtar çifti oluşturma ve yönetme hakkında ayrıntılı adımlar hakkında bilgi edinin.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 9d888de78432558cda8e08c3de91be9673231b47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554726"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Ayrıntılı adımlar: Azure 'da bir Linux VM 'sine kimlik doğrulaması için SSH anahtarları oluşturma ve yönetme

Secure Shell (SSH) anahtar çifti ile, kimlik doğrulaması için SSH anahtarları kullanan bir Linux sanal makinesi oluşturabilirsiniz. Bu makalede SSH istemci bağlantıları için SSH RSA genel özel anahtar dosya çiftinin nasıl oluşturulduğu ve kullanılacağı gösterilir.

Hızlı komutları istiyorsanız bkz. [Azure 'Da Linux VM 'ler IÇIN SSH genel-özel anahtar çifti oluşturma](mac-create-ssh-keys.md).

SSH anahtarları oluşturmak ve bunları bir Windows bilgisayarından bir **Windows** bilgisayardan bağlanmak üzere kullanmak için bkz. [Azure 'DA Windows ile SSH anahtarlarını kullanma](ssh-from-windows.md). Portalda VM oluşturmak için SSH anahtarlarını oluşturmak ve yönetmek üzere [Azure Portal](../ssh-keys-portal.md) de kullanabilirsiniz.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>SSH anahtarlarının kullanımı ve avantajları

Ortak anahtarı belirterek bir Azure VM oluşturduğunuzda, Azure ortak anahtarı ( `.pub` biçimde) `~/.ssh/authorized_keys` VM 'deki klasöre kopyalar. İçindeki SSH anahtarları, `~/.ssh/authorized_keys` istemcinin BIR SSH bağlantısında karşılık gelen özel anahtarla eşleşmesini sağlamak için kullanılır. Kimlik doğrulaması için SSH anahtarları kullanan bir Azure Linux sanal makinesinde Azure, SSHD sunucusunu parola oturum açma ve yalnızca SSH anahtarlarına izin verecek şekilde yapılandırır. SSH anahtarlarıyla bir Azure Linux VM 'si oluşturarak, VM dağıtımının güvenliğini sağlamaya ve dosyadaki parolaları devre dışı bırakmanın tipik dağıtım sonrası yapılandırma adımını kaydetmenizi sağlayabilirsiniz `sshd_config` .

SSH anahtarlarını kullanmak istemiyorsanız, Linux sanal makinenizin parolasını parola kimlik doğrulaması kullanacak şekilde ayarlayabilirsiniz. SANAL makinenizin Internet 'e açık olmaması durumunda parolaların kullanılması yeterli olabilir. Ancak, her bir Linux sanal makinesi için parolalarınızı yönetmeniz ve minimum parola uzunluğu ve normal güncelleştirmeler gibi sağlıklı parola ilkelerini ve uygulamaları korumanız gerekir. 

## <a name="generate-keys-with-ssh-keygen"></a>SSH-keygen ile anahtar oluşturma

Anahtarları oluşturmak için, `ssh-keygen` Azure Cloud Shell, macOS veya Linux ana bilgisayarı ve Windows 10 ' da OpenSSH yardımcı programları ile kullanılabilen tercih edilen bir komut vardır. `ssh-keygen` bir dizi soru sorar ve sonra özel bir anahtar ve eşleşen bir ortak anahtar yazar. 

SSH anahtarları, varsayılan olarak `~/.ssh` dizininde tutulur.  `~/.ssh` dizininiz yoksa `ssh-keygen` komutu, doğru izinler ile sizin için oluşturur.

### <a name="basic-example"></a>Temel örnek

Aşağıdaki `ssh-keygen` komut, dizinde varsayılan olarak 4096 BITLIK ssh rsa ortak ve özel anahtar dosyaları oluşturur `~/.ssh` . Geçerli konumda bir SSH anahtar çifti varsa, bu dosyaların üzerine yazılır.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Ayrıntılı örnek
Aşağıdaki örnek, bir SSH RSA anahtar çifti oluşturmak için ek komut seçeneklerini gösterir. Geçerli konumda bir SSH anahtar çifti varsa, bu dosyaların üzerine yazılır. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Komut açıklaması**

`ssh-keygen` = anahtarları oluşturmak için kullanılan program

`-m PEM` = anahtarı ped olarak Biçimlendir

`-t rsa` = Bu örnekte, bu durumda, RSA biçiminde oluşturulacak anahtar türü

`-b 4096` = Bu örnekte, anahtardaki bit sayısı 4096

`-C "azureuser@myserver"` = kolayca tanımlamak için ortak anahtar dosyasının sonuna eklenen bir açıklama. Normalde açıklama olarak bir e-posta adresi kullanılır, ancak altyapınız için en iyi şeyi kullanın.

`-f ~/.ssh/mykeys/myprivatekey` = varsayılan adı kullanmayı tercih ederseniz, özel anahtar dosyasının dosya adı. Birlikte eklenmiş karşılık gelen ortak anahtar dosyası `.pub` aynı dizinde oluşturulur. Dizinin var olması gerekir.

`-N mypassphrase` = özel anahtar dosyasına erişmek için kullanılan ek bir parola. 

### <a name="example-of-ssh-keygen"></a>ssh-keygen örneği

```bash
ssh-keygen -t rsa -m PEM -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>Kayıtlı anahtar dosyaları

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Bu makale için anahtar çifti adı. Adında bir anahtar çiftinin olması `id_rsa` varsayılandır; bazı araçlar `id_rsa` özel anahtar dosya adını bekleyebilir, bu nedenle bir tane olması iyi bir fikirdir. `~/.ssh/` dizini, SSH anahtar çiftleri ve SSH yapılandırma dosyası için varsayılan konumdur. Tam yol belirtilmezse `ssh-keygen` tarafından oluşturulan anahtarlar varsayılan `~/.ssh` dizininde değil geçerli çalışma dizininde olur.

#### <a name="list-of-the-ssh-directory"></a>`~/.ssh`Dizinin listesi

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Anahtar parolası

`Enter passphrase (empty for no passphrase):`

Özel anahtarınıza bir parola eklemeniz *önemle* önerilir. Anahtar dosyasını korumak için bir parola olmadan, dosyayı içeren herkes ilgili ortak anahtara sahip herhangi bir sunucuda oturum açmak için kullanabilir. Bir parola eklemek, birinin özel anahtar dosyanıza erişim sağlayabilmesi durumunda anahtarları değiştirmeniz için size zaman kazandırmakta daha fazla koruma sağlar.

## <a name="generate-keys-automatically-during-deployment"></a>Dağıtım sırasında anahtarları otomatik olarak oluştur

VM 'nizi oluşturmak için [Azure CLI](/cli/azure) kullanıyorsanız, isteğe bağlı olarak [az VM Create](/cli/azure/vm) komutunu seçeneğiyle çalıştırarak SSH genel ve özel anahtar dosyaları oluşturabilirsiniz `--generate-ssh-keys` . Anahtarlar ~/PST SSH dizininde depolanır. Bu komut seçeneğinin, bu konumda zaten varsa anahtarların üzerine yazılmadığını unutmayın.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>VM dağıtımında SSH ortak anahtarı sağla

Kimlik doğrulaması için SSH anahtarları kullanan bir Linux sanal makinesi oluşturmak için Azure portal, CLı, Kaynak Yöneticisi şablonları veya diğer yöntemleri kullanarak VM oluştururken SSH ortak anahtarınızı sağlayın. Portalı kullanırken, ortak anahtarın kendisini girersiniz. VM 'nizi mevcut bir ortak anahtarla oluşturmak için [Azure CLI](/cli/azure) 'yı kullanırsanız, [az VM Create](/cli/azure/vm) komutunu seçeneğiyle çalıştırarak bu ortak anahtarın değerini veya konumunu belirtin `--ssh-key-value` . 

SSH ortak anahtarının biçimiyle ilgili bilgi sahibi değilseniz, ortak anahtarınızı `cat` aşağıdaki şekilde çalıştırarak, `~/.ssh/id_rsa.pub` kendi ortak anahtar dosyası konumuyla değiştirin:

```bash
cat ~/.ssh/id_rsa.pub
```

Çıktı aşağıdakine benzer (burada Redaksiyonu yapılmış):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Ortak anahtar dosyasının içeriğini kopyalayıp Azure portal veya bir Kaynak Yöneticisi şablonuna yapıştırırsanız, ek boşluk kopyalamayın veya ek satır sonları eklediğinizden emin olun. Örneğin, macOS kullanıyorsanız, içeriği kopyalamak için ortak anahtar dosyasını (varsayılan olarak, `~/.ssh/id_rsa.pub` ) **pbcopy** 'e kanal oluşturarak (örneğin, aynı şeyi yapan diğer Linux programları vardır `xclip` ).

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

Her SSH oturum açma ile özel anahtar dosya parolanızı yazmadan kaçınmak için, `ssh-agent` özel anahtar dosya parolanızı önbelleğe almak için kullanabilirsiniz. Mac kullanıyorsanız macOS anahtarlığı, çağırdığınızda özel anahtar parolasını güvenli bir şekilde depolar `ssh-agent` .

`ssh-agent` `ssh-add` Parolayı etkileşimli olarak kullanmanıza gerek kalmayacak şekilde anahtar dosyaları hakkında SSH sistemini bilgilendirmek için ve kullanın.

```bash
eval "$(ssh-agent -s)"
```

Şimdi `ssh-add` komutunu kullanarak özel anahtarı `ssh-agent` öğesine ekleyin.

```bash
ssh-add ~/.ssh/id_rsa
```

Özel anahtar parolası artık ' de depolanır `ssh-agent` .

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Anahtarı mevcut bir sanal makineye kopyalamak için SSH-Copy-ID kullanın

Zaten bir VM oluşturduysanız, kullanarak Linux VM 'nize yeni bir SSH ortak anahtarı ekleyebilirsiniz `ssh-copy-id` .

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH yapılandırma dosyası oluşturma ve yapılandırma

, `~/.ssh/config` Oturum açma işlemlerini hızlandırmak ve SSH istemci davranışınızı iyileştirmek için BIR SSH yapılandırma dosyası () oluşturup yapılandırabilirsiniz. 

Aşağıdaki örnek, varsayılan SSH özel anahtarını kullanarak belirli bir VM 'ye Kullanıcı olarak hızlı bir şekilde oturum açmak için kullanabileceğiniz basit bir yapılandırmayı gösterir. 

Dosyayı oluşturun.

```bash
touch ~/.ssh/config
```

Yeni SSH yapılandırmasını eklemek için dosyayı düzenleyin

```bash
vim ~/.ssh/config
```

Ana bilgisayar VM 'niz için uygun yapılandırma ayarlarını ekleyin. Bu örnekte, VM adı *myvm* , hesap adı ise *azureuser*.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Her birinin kendi adanmış anahtar çiftini kullanmasını sağlamak için ek konaklar için yapılandırma ekleyebilirsiniz. Daha gelişmiş yapılandırma seçenekleri için bkz. [SSH yapılandırma dosyası](https://www.ssh.com/ssh/config/) .

Artık bir SSH anahtar çiftiniz ve yapılandırılmış bir SSH yapılandırma dosyasına sahip olduğunuza göre, Linux VM 'niz üzerinde hızlı ve güvenli bir şekilde oturum açabiliyor olabilirsiniz. Aşağıdaki komutu çalıştırdığınızda SSH, `Host myvm` SSH yapılandırma dosyasındaki bloğundan ayarları bulur ve yükler.

```bash
ssh myvm
```

Bir SSH anahtarı kullanarak bir sunucuda ilk kez oturum açtığınızda, komut sizden bu anahtar dosyası için parola ister.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adım, yeni SSH ortak anahtarını kullanarak Azure Linux VM’ler oluşturmaktır. Oturum açma olarak SSH ortak anahtarıyla oluşturulan Azure VM 'Ler, varsayılan oturum açma yöntemiyle, Parolalarla oluşturulan VM 'lerden daha güvenlidir.

* [Azure portalı ile Linux sanal makinesi oluşturma](quick-create-portal.md)
* [Azure CLI ile Linux sanal makinesi oluşturma](quick-create-cli.md)
* [Bir Azure şablonu kullanarak bir Linux VM oluşturma](create-ssh-secured-vm-from-template.md)
