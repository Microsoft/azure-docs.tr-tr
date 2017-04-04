---
title: Windows, Linux, Unix ya da OS X&quot;te HDInsight (Hadoop) ile SSH kullanma | Microsoft Belgeleri
description: " Secure Shell (SSH) kullanarak HDInsight&quot;a erişebilirsiniz. Bu belge Windows, Linux, Unix ya da OS X istemcilerinde HDInsight’a bağlanmak için SSH kullanma hakkında bilgi sağlar."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/16/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 89d44476e9de8ac32195efaf66535cdd9fb4260e
ms.lasthandoff: 03/25/2017

---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>SSH kullanarak HDInsight’a (Hadoop) bağlanma

[Güvenli Kabuk (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) kullanarak HDInsight’a güvenli bir şekilde bağlanma hakkında bilgi edinin. HDInsight, küme içindeki düğümler için işletim sistemi olarak Linux (Ubuntu) kullanabilir. SSH, Linux tabanlı bir kümenin baş ve kenar düğümlerine bağlanmak ve doğrudan bu düğümler üzerinde komut çalıştırmak için kullanılabilir.

Aşağıdaki tablo, SSH kullanarak HDInsight’a bağlanırken gereken adres ve bağlantı noktası bilgilerini içerir:

| Adres | Bağlantı noktası | Bağlandığı yer... |
| ----- | ----- | ----- |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Kenar düğümü (varsa) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Birincil baş düğüm |
| `<clustername>-ssh.azurehdinsight.net` | 23 | İkincil baş düğüm |

> [!NOTE]
> `<edgenodename>` ifadesini kenar düğümünün adıyla değiştirin. Kenar düğümlerini kullanma hakkında daha fazla bilgi için bkz. [HDInsight’ta kenar düğümlerini kullanma](hdinsight-apps-use-edge-node.md#access-an-edge-node).
>
> `<clustername>` değerini HDInsight kümenizin adıyla değiştirin.
>
> Varsa __her zaman kenar düğümüne bağlanmanız__ önerilir. Baş düğümler, kümenin durumu için kritik öneme sahip olan hizmetleri barındırır. Kenar düğümü yalnızca üzerine yerleştirdiğiniz öğeleri çalıştırır.

## <a name="ssh-clients"></a>SSH istemcileri

Çoğu işletim sistemi `ssh` istemcisini sağlar. Microsoft Windows, varsayılan olarak bir SSH istemcisi sağlamaz. Windows için SSH istemcisi, aşağıdaki paketlerin her birinde bulunur:

* [Windows 10 için Ubuntu üzerinde Bash](https://msdn.microsoft.com/commandline/wsl/about): Windows üzerinde Bash komut satırı üzerinden `ssh` komutu sağlanır.

* [Git (https://git-scm.com/)](https://git-scm.com/): GitBash komut satırı üzerinden `ssh` komutu sağlanır.

* [GitHub Masaüstü (https://desktop.github.com/)](https://desktop.github.com/) Git Kabuğu komut satırı üzerinden `ssh` komutu sağlanır. GitHub Masaüstü, Git Kabuğunun komut satırı olarak Bash, Windows Komut İstemi veya PowerShell kullanacak şekilde yapılandırılabilir.

* [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH): PowerShell ekibi, OpenSSH’i Windows’a taşımakta ve test yayınları yapmaktadır.

    > [!WARNING]
    > OpenSSH paketi, `sshd` adlı SSH sunucu bileşenini içerir. Bu bileşen, sisteminizde başkalarının bağlanmasına izin verilen bir SSH sunucusu başlatır. Sisteminizde bir SSH sunucusu barındırmak istemiyorsanız, bu bileşeni yapılandırmayın veya 22 numaralı bağlantı noktasını açmayın. HDInsight ile iletişim kurulması gerekmez.

Ayrıca [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) ve [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/) gibi çeşitli grafiksel SSH istemcisi mevcuttur. Bu istemciler HDInsight’a bağlanmak için kullanılabilse de, sunucuya bağlanma işlemi `ssh` yardımcı programını kullanmaktan farklıdır. Daha fazla bilgi için, kullanmakta olduğunuz grafiksel istemcinin belgelerine bakın.

## <a id="sshkey"></a>Kimlik doğrulaması: SSH Anahtarları

SSH anahtarları, kümenin güvenliğini sağlamak için [Ortak anahtar şifrelemesi](https://en.wikipedia.org/wiki/Public-key_cryptography) kullanır. SSH anahtarları parolalara göre daha güvenlidir ve HDInsight kümenizin güvenliğini sağlamak için kolay bir yol sağlar.

SSH hesabınızın güvenliği bir anahtar yardımıyla sağlanıyorsa, bağlantı kurduğunuzda istemci eşleşen özel anahtarı sağlamalıdır:

* Çoğu istemci, bir __varsayılan anahtar__ kullanmak üzere yapılandırılmıştır. Örneğin, `ssh` istemcisi Linux ve Unix ortamlarında `~/.ssh/id_rsa` üzerinde bir özel anahtar arar.

* __Özel anahtarın yolunu__ belirtebilirsiniz. `ssh` istemcisi ile özel anahtarın yolunu belirtmek için `-i` parametresi kullanılır. Örneğin, `ssh -i ~/.ssh/hdinsight sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Farklı sunucularla kullanılacak __birden fazla özel anahtarınız__ varsa, kullanılacak anahtarı otomatik olarak seçmek için [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) gibi yardımcı programlar kullanılabilir.

> [!IMPORTANT]
>
> Özel anahtarınızın güvenliğini şifre ile sağlıyorsanız, anahtarı kullanmak için şifreyi girmeniz gerekir. `ssh-agent` gibi yardımcı programlar, size kolaylık sağlamak için parolayı önbelleğe alabilir.

### <a name="create-an-ssh-key-pair"></a>SSH anahtar çifti oluşturma

Ortak ve özel anahtar dosyaları oluşturmak için `ssh-keygen` komutunu kullanın. Aşağıdaki komut, HDInsight ile kullanılabilecek bir 2048-bit RSA anahtar çifti oluşturur:

    ssh-keygen -t rsa -b 2048

Anahtar oluşturma işlemi sırasında sizden bilgiler istenir. Örneğin, anahtarların nerede depolanacağı veya şifre kullanılıp kullanılmayacağı. İşlem tamamlandıktan sonra biri ortak anahtar, diğeri özel anahtar olmak üzere iki dosya oluşturulur.

* __Ortak anahtar__ bir HDInsight kümesi oluşturmak için kullanılır. Ortak anahtar `.pub` uzantısına sahiptir.

* __Özel anahtar__, HDInsight kümesinde istemcinizin kimliğini doğrulamak için kullanılır.

> [!IMPORTANT]
> Anahtarlarınızın güvenliğini şifre ile sağlayabilirsiniz. Bu parola aslında özel anahtarınız üzerindeki bir şifredir. Özel anahtarınız başkası tarafından ele geçirilirse, anahtarın kullanılması için şifrenin girilmesi gerekir.

### <a name="create-hdinsight-using-the-public-key"></a>Ortak anahtar kullanarak HDInsight oluşturma

| Oluşturma yöntemi | Ortak anahtarı kullanma |
| ------- | ------- |
| **Azure portal** | __Küme oturumu açmak için kullanılan parolayı kullan__ seçeneğinin işaretini kaldırın ve ardından SSH kimlik doğrulama türü olarak __Ortak Anahtar__’ı seçin. Son olarak, ortak anahtar dosyasını seçin veya dosyanın metin içeriğini __SSH ortak anahtarı__ alanına yapıştırın.</br>![HDInsight küme oluşturma işleminde SSH ortak anahtarı iletişim kutusu](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | `New-AzureRmHdinsightCluster` cmdlet'inin `-SshPublicKey` parametresini kullanarak, ortak anahtarın içeriğini dize olarak geçirin.|
| **Azure CLI 1.0** | `azure hdinsight cluster create` komutunun `--sshPublicKey` parametresini kullanarak, ortak anahtarın içeriğini dize olarak geçirin. |
| **Resource Manager Şablonu** | SSH anahtarlarını şablonla kullanma örneği için bkz. [HDInsight’ı SSH anahtarı ile Linux’a dağıtma](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/). [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) dosyasında `publicKeys` öğesi, kümeyi oluştururken Azure’a anahtarları geçirmek için kullanılır. |

## <a id="sshpassword"></a>Kimlik doğrulaması: Parola

SSH hesaplarının güvenliği bir parola kullanılarak sağlanabilir. SSH kullanarak HDInsight’a bağlandığınızda, parola girmeniz istenir.

> [!WARNING]
> SSH için parola kimlik doğrulamasının kullanılması önerilmez. Parolalar tahmin edilebilir ve deneme yanılma saldırılarına karşı savunmasızdır. Bunun yerine, [kimlik doğrulaması için SSH anahtarları](#sshkey) kullanmanız önerilir.

### <a name="create-hdinsight-using-a-password"></a>Parola kullanarak HDInsight oluşturma

| Oluşturma yöntemi | Parola belirtme |
| --------------- | ---------------- |
| **Azure portal** | Varsayılan olarak, SSH kullanıcı hesabı ile küme oturum açma hesabı aynı parolaya sahiptir. Farklı bir parola kullanmak için __Küme oturumu açmak için kullanılan parolayı kullan__ seçeneğinin işaretini kaldırın ve __SSH parolası__ alanına parolayı girin.</br>![HDInsight küme oluşturma işleminde SSH parolası iletişim kutusu](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | `New-AzureRmHdinsightCluster` cmdlet’inin `--SshCredential` parametresini kullanın ve SSH kullanıcı hesabı adı ile parolasını içeren bir `PSCredential` nesnesi geçirin. |
| **Azure CLI 1.0** | `azure hdinsight cluster create` komutunun `--sshPassword` parametresini kullanarak parola değerini belirtin. |
| **Resource Manager Şablonu** | Parolayı şablonla kullanma örneği için bkz. [HDInsight’ı SSH parolası ile Linux’a dağıtma](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) dosyasındaki `linuxOperatingSystemProfile` öğesi, kümeyi oluştururken SSH hesabı adı ile parolasını Azure’a geçirmek için kullanılır.|

### <a name="change-the-ssh-password"></a>SSH parolasını değiştirme

SSH kullanıcı hesabı parolasını değiştirme hakkında bilgi için, [HDInsight’ı Yönetme](hdinsight-administer-use-portal-linux.md#change-passwords) belgesinin __Parolaları değiştirme__ bölümüne bakın.

## <a id="domainjoined"></a>Kimlik doğrulama: Etki alanına katılmış HDInsight

__Etki alanına katılmış HDInsight kümesi__ kullanıyorsanız, SSH ile bağlantı kurduktan sonra `kinit` komutunu kullanmanız gerekir. Bu komut sizden bir etki alanı kullanıcı adı ile parolası ister ve kümenizle ilişkili Azure Active Directory etki alanını kullanarak oturumunuzun kimliğini doğrular.

Daha fazla bilgi için bkz. [Etki alanına katılmış HDInsight yapılandırma](hdinsight-domain-joined-configure.md).

## <a name="connect-to-worker-and-zookeeper-nodes"></a>Çalışan ve Zookeeper düğümlerine bağlanma

Çalışan düğümlerine ve Zookeeper düğümlerine İnternet üzerinden doğrudan erişim sağlamak mümkün değildir, ancak bu düğümlere kümenin baş düğümlerinden veya kenar düğümlerinden erişebilirsiniz. Diğer düğümlere bağlanmak için uygulamanız gereken genel adımlar şunlardır:

1. SSH kullanarak bir baş veya kenar düğümüne bağlanın:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. Baş veya kenar düğümüne yaptığınız SSH bağlantısında `ssh` komutunu kullanarak kümedeki bir çalışan düğümüne bağlanın:

        ssh sshuser@wn0-myhdi

    Kümedeki düğümlerinin etki alanı adlarının listesini almak için [Ambari REST API’sini kullanarak HDInsight’ı yönetme](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) belgesindeki örneklere bakın.

SSH hesabı __parola__ ile korunuyorsa, ilgili parolayı girdikten sonra bağlantı kurulur.

SSH hesabının güvenliği __SSH anahtarları__ ile sağlanıyorsa, yerel ortamınızda SSH aracısı iletme yapılandırmasının gerçekleştirildiğinden emin olun.

> [!NOTE]
> Kümedeki tüm düğümlere doğrudan erişmenin başka bir yolu ise HDInsight’ın bir Azure Sanal Ağına bağlanmasıdır. Bundan sonra, uzak makinenizi aynı sanal ağa bağlayabilir ve kümedeki tüm düğümlere doğrudan erişebilirsiniz.
>
> Daha fazla bilgi için bkz. [HDInsight ile sanal ağ kullanma](hdinsight-extend-hadoop-virtual-network.md).

### <a name="configure-ssh-agent-forwarding"></a>SSH aracı iletmeyi yapılandırma

> [!IMPORTANT]
> Aşağıdaki adımlar, Linux/UNIX tabanlı sistem için geçerlidir ve Bash on Windows 10 ile birlikte çalışır. Bu adımlar sisteminizde çalışmazsa SSH istemcinizin belgelerine bakmanız gerekebilir.

1. Bir metin düzenleyicisiyle `~/.ssh/config` dosyasını açın. Bu dosya yoksa, komut satırında `touch ~/.ssh/config` girerek oluşturabilirsiniz.

2. Aşağıdakileri metni `config` dosyasına ekleyin.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    __Ana bilgisayar__ bilgisini, SSH kullanarak bağlandığınız düğümün adresiyle değiştirin. Önceki örnekte kenar düğümü kullanılmıştır. Bu giriş, belirtilen düğüm için SSH aracı iletmeyi yapılandırır.

3. Terminalde aşağıdaki komutu kullanarak, SSH aracı iletmeyi test edin:

        echo "$SSH_AUTH_SOCK"

    Bu komutun aşağıdaki metne benzer bilgiler döndürmesi gerekir:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Hiçbir şeyin döndürülmemesi, `ssh-agent` özelliğinin çalışmadığını gösterir. Aracı başlatma komut dosyası bilgileri için [ssh ile ssh-agent kullanma (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) sayfasını inceleyin veya SSH istemcinizin `ssh-agent` yükleme ve yapılandırma adımlarına başvurun.

4. **ssh aracı**nın çalıştığını doğruladıktan sonra, SSH özel anahtarınızı aracıya eklemek için aşağıdakini kullanın:

        ssh-add ~/.ssh/id_rsa

    Özel anahtarınızı farklı bir dosyada saklanıyorsa, `~/.ssh/id_rsa` ile dosyanın yolunu değiştirin.

5. SSH kullanarak küme kenar düğümüne veya baş düğümlerine bağlanın. Ardından, SSH komutunu kullanarak bir çalışan veya zookeeper düğümüne bağlanın. İletilen anahtar kullanılarak bağlantı kurulur.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight ile SSH tüneli kullanma](hdinsight-linux-ambari-ssh-tunnel.md)
* [HDInsight ile sanal ağ kullanma](hdinsight-extend-hadoop-virtual-network.md)
* [HDInsight’ta kenar düğümlerini kullanma](hdinsight-apps-use-edge-node.md#access-an-edge-node)
