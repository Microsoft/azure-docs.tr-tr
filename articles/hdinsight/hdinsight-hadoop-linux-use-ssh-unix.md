---
title: SSH’yi Hadoop - Azure HDInsight ile Kullanma
description: Secure Shell (SSH) kullanarak HDInsight'a erişebilirsiniz. Bu belge, Windows, Linux, Unix veya macOS istemcilerinden gelen SSH komutlarını kullanarak HDInsight 'a bağlanma hakkında bilgi sağlar.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 02/28/2020
ms.openlocfilehash: b6e8314e87a11deeea1d7c754a366b1bf2214f3c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770300"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>SSH kullanarak HDInsight’a (Apache Hadoop) bağlanma

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) kullanarak Azure hdınsight üzerinde Apache Hadoop güvenli bir şekilde bağlanma hakkında bilgi edinin. Bir sanal ağ üzerinden bağlanma hakkında daha fazla bilgi için bkz. [Azure HDInsight sanal ağ mimarisi](./hdinsight-virtual-network-architecture.md). Ayrıca bkz. [Azure HDInsight kümeleri için bir sanal ağ dağıtımı planlayın](./hdinsight-plan-virtual-network-deployment.md).

Aşağıdaki tablo, bir SSH istemcisi kullanarak HDInsight 'a bağlanırken gereken adres ve bağlantı noktası bilgilerini içerir:

| Adres | Bağlantı noktası | Bağlandığı yer... |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Birincil baş düğüm |
| `<clustername>-ssh.azurehdinsight.net` | 23 | İkincil baş düğüm |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | Edge düğümü (HDInsight üzerinde ML Hizmetleri) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Kenar düğümü (bir kenar düğümü varsa başka bir küme türü) |

`<clustername>` değerini kümenizin adıyla değiştirin. `<edgenodename>` ifadesini kenar düğümünün adıyla değiştirin.

Kümeniz bir kenar düğümü içeriyorsa __kenar düğümüne her zaman SSH’yi kullanarak bağlanmanızı__ öneririz. Baş düğümler, Hadoop’un sistem durumu için kritik öneme sahip olan hizmetleri barındırır. Kenar düğümü yalnızca üzerine yerleştirdiğiniz öğeleri çalıştırır. Kenar düğümlerini kullanma hakkında daha fazla bilgi için bkz. [HDInsight’ta kenar düğümlerini kullanma](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]  
> HDInsight'a ilk kez bağlandığınızda SSH istemciniz konağın orijinalliğinin belirlenemediği yönünde bir uyarı görüntüleyebilir. Konağı SSH istemcinizin güvenilen sunucular listesine eklemek isteyip istemediğiniz sorulduğunda "Evet"i seçin.
>
> Daha önceden aynı ada sahip bir sunucuya bağlandıysanız konak anahtarının sunucu konak anahtarıyla eşleşmediğine dair bir uyarı alabilirsiniz. Var olan sunucu adı girişini kaldırma talimatları için SSH istemcinizin belgelerine bakın.

## <a name="ssh-clients"></a>SSH istemcileri

Linux, Unix ve macOS sistemleri `ssh` ve `scp` komutlarını sağlar. `ssh` istemcisi, yaygın olarak Linux veya Unix tabanlı bir sistemle uzak komut satırı oturumu oluşturmak için kullanılır. `scp` istemcisi, dosyaları istemciniz ve uzak sistem arasında güvenli bir şekilde kopyalamak için kullanılır.

Microsoft Windows, hiçbir SSH istemcisini varsayılan olarak yüklemez. `ssh` ve `scp` istemcileri, aşağıdaki paketler aracılığıyla Windows için kullanılabilir:

* [OpenSSH istemcisi](/windows-server/administration/openssh/openssh_install_firstuse). Bu istemci, Windows 10 Fall Creators Update 'te sunulan isteğe bağlı bir özelliktir.

* [Windows 10 ' da Ubuntu üzerinde Bash](/windows/wsl/about).

* [Azure Cloud Shell](../cloud-shell/quickstart.md). Cloud Shell, tarayıcınızda bir bash ortamı sağlar.

* [Git](https://git-scm.com/).

Ayrıca, [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/) ve [MobaXterm](https://mobaxterm.mobatek.net/)gibi birçok grafik SSH istemcisi de vardır. Bu istemciler HDInsight’a bağlanmak için kullanılabilse de bağlanma işlemi `ssh` yardımcı programını kullanmaktan farklıdır. Daha fazla bilgi için, kullanmakta olduğunuz grafik istemcisinin belgelerine bakın.

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>Kimlik doğrulaması: SSH Anahtarları

SSH anahtarları SSH oturumlarının kimliğini doğrulamak için [ortak anahtar şifrelemesi](https://en.wikipedia.org/wiki/Public-key_cryptography) kullanır. SSH anahtarları parolalara göre daha güvenlidir ve Hadoop kümenize erişimin güvenliğini sağlamak için kolay bir yol sağlar.

SSH hesabınızın güvenliği bir anahtar yardımıyla sağlanıyorsa, bağlantı kurduğunuzda istemci eşleşen özel anahtarı sağlamalıdır:

* Çoğu istemci, bir __varsayılan anahtar__ kullanmak üzere yapılandırılmıştır. Örneğin, `ssh` istemcisi Linux ve Unix ortamlarında `~/.ssh/id_rsa` üzerinde bir özel anahtar arar.

* __Özel anahtarın yolunu__ belirtebilirsiniz. `ssh` istemcisi ile özel anahtarın yolunu belirtmek için `-i` parametresi kullanılır. Örneğin, `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Farklı sunucularla kullanılacak __birden fazla özel anahtarınız__ varsa [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) gibi bir yardımcı program kullanmayı deneyin. `ssh-agent` yardımcı programı, SSH oturumu oluşturulurken kullanılacak anahtarı otomatik olarak seçmek için kullanılabilir.

> [!IMPORTANT]  
> Özel anahtarınızın güvenliğini şifre ile sağlıyorsanız, anahtarı kullanmak için şifreyi girmeniz gerekir. `ssh-agent` gibi yardımcı programlar, size kolaylık sağlamak için parolayı önbelleğe alabilir.

### <a name="create-an-ssh-key-pair"></a>SSH anahtar çifti oluşturma

Ortak ve özel anahtar dosyaları oluşturmak için `ssh-keygen` komutunu kullanın. Aşağıdaki komut, HDInsight ile kullanılabilecek bir 2048-bit RSA anahtar çifti oluşturur:

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

Anahtar oluşturma işlemi sırasında bilgi girmeniz istenir. Örneğin, anahtarların nerede depolanacağı veya şifre kullanılıp kullanılmayacağı. İşlem tamamlandıktan sonra biri ortak anahtar, diğeri özel anahtar olmak üzere iki dosya oluşturulur.

* __Ortak anahtar__ bir HDInsight kümesi oluşturmak için kullanılır. Ortak anahtar `.pub` uzantısına sahiptir.

* __Özel anahtar__, HDInsight kümesinde istemcinizin kimliğini doğrulamak için kullanılır.

> [!IMPORTANT]  
> Anahtarlarınızın güvenliğini şifre ile sağlayabilirsiniz. Parola, aslında özel anahtarınız üzerindeki bir şifredir. Özel anahtarınız başkası tarafından ele geçirilirse, anahtarın kullanılması için şifrenin girilmesi gerekir.

### <a name="create-hdinsight-using-the-public-key"></a>Ortak anahtar kullanarak HDInsight oluşturma

| Oluşturma yöntemi | Ortak anahtarı kullanma |
| ------- | ------- |
| Azure portalı | __SSH için küme oturum açma parolası kullan__ seçeneğinin işaretini kaldırın ve ardından SSH kimlik doğrulaması türü olarak __ortak anahtar__ ' ı seçin. Son olarak, ortak anahtar dosyasını seçin veya dosyanın metin içeriğini __SSH ortak anahtarı__ alanına yapıştırın.</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png" alt-text="HDInsight küme oluşturma işleminde SSH ortak anahtarı iletişim kutusu"::: |
| Azure PowerShell | `-SshPublicKey` [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet 'inin parametresini kullanın ve ortak anahtarın içeriğini dize olarak geçirin.|
| Azure CLI | `--sshPublicKey`Komutun parametresini kullanın [`az hdinsight create`](/cli/azure/hdinsight#az_hdinsight_create) ve ortak anahtarın içeriğini dize olarak geçirin. |
| Resource Manager Şablonu | SSH anahtarlarını şablonla kullanma örneği için bkz. [HDInsight’ı SSH anahtarı ile Linux’a dağıtma](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/). [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) dosyasında `publicKeys` öğesi, kümeyi oluştururken Azure’a anahtarları geçirmek için kullanılır. |

## <a name="authentication-password"></a>Kimlik doğrulaması: Parola

SSH hesaplarının güvenliği bir parola kullanılarak sağlanabilir. SSH kullanarak HDInsight 'a bağlandığınızda, parolayı girmeniz istenir.

> [!WARNING]  
> Microsoft, SSH için parola kimlik doğrulamasının kullanılmasını önermez. Parolalar tahmin edilebilir ve deneme yanılma saldırılarına karşı savunmasızdır. Bunun yerine, [kimlik doğrulaması için SSH anahtarları](#sshkey) kullanmanız önerilir.

> [!IMPORTANT]  
> SSH hesabı parolasının, HDInsight kümesi oluşturulduktan 70 gün sonra süresi dolar. Parolanızın süresi dolarsa, [HDInsight’ı yönetme](hdinsight-administer-use-portal-linux.md#change-passwords) belgesindeki bilgileri kullanarak değiştirebilirsiniz.

### <a name="create-hdinsight-using-a-password"></a>Parola kullanarak HDInsight oluşturma

| Oluşturma yöntemi | Parola belirtme |
| --------------- | ---------------- |
| Azure portalı | Varsayılan olarak, SSH kullanıcı hesabı ile küme oturum açma hesabı aynı parolaya sahiptir. Farklı bir parola kullanmak için __SSH için küme oturum açma parolası kullan__' ın işaretini kaldırın ve ardından __SSH parolası__ alanına parolayı girin.</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png" alt-text="HDInsight küme oluşturma işleminde SSH parolası iletişim kutusu":::|
| Azure PowerShell | `--SshCredential` [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet 'inin PARAMETRESINI kullanın ve `PSCredential` SSH kullanıcı hesabı adını ve parolasını içeren bir nesne geçirin. |
| Azure CLI | `--ssh-password`Komutun parametresini kullanın [`az hdinsight create`](/cli/azure/hdinsight#az_hdinsight_create) ve parola değerini sağlayın. |
| Resource Manager Şablonu | Parolayı şablonla kullanma örneği için bkz. [HDInsight’ı SSH parolası ile Linux’a dağıtma](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) dosyasındaki `linuxOperatingSystemProfile` öğesi, kümeyi oluştururken SSH hesabı adı ile parolasını Azure’a geçirmek için kullanılır.|

### <a name="change-the-ssh-password"></a>SSH parolasını değiştirme

SSH kullanıcı hesabı parolasını değiştirme hakkında bilgi için, [HDInsight’ı Yönetme](hdinsight-administer-use-portal-linux.md#change-passwords) belgesinin __Parolaları değiştirme__ bölümüne bakın.

## <a name="authentication-domain-joined-hdinsight"></a>Kimlik doğrulaması etki alanına katılmış HDInsight

__Etki alanına katılmış bir HDInsight kümesi__ KULLANıYORSANıZ, `kinit` SSH yerel kullanıcısı ile bağlandıktan sonra komutunu kullanmanız gerekir. Bu komut sizden bir etki alanı kullanıcı adı ile parolası ister ve kümenizle ilişkili Azure Active Directory etki alanını kullanarak oturumunuzun kimliğini doğrular.

Etki alanı hesabı kullanarak, etki alanına katılmış her düğümde (örneğin, baş düğüm, kenar düğümü), SSH 'ye Kerberos kimlik doğrulamasını da etkinleştirebilirsiniz. Bunu yapmak için sshd config dosyasını düzenleyin:

```bash
sudo vi /etc/ssh/sshd_config
```

açıklamayı kaldırın ve `KerberosAuthentication` değerini `yes` olarak değiştirin

```bash
sudo service sshd restart
```

`klist`Kerberos kimlik doğrulamasının başarılı olup olmadığını doğrulamak için komutunu kullanın.

Daha fazla bilgi için bkz. [Etki alanına katılmış HDInsight yapılandırma](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-nodes"></a>Düğümlere bağlanma

Baş düğümlere ve kenar düğümüne (varsa) 22 ve 23 numaralı bağlantı noktalarında internet üzerinden erişilebilir.

* __Baş düğümlere__ bağlanırken, birincil baş düğüme bağlanmak için __22__, ikincil baş düğüme bağlanmak için __23__ numaralı bağlantı noktasını kullanın. Kullanılacak tam etki alanı adı `clustername-ssh.azurehdinsight.net`‘tir, burada `clustername` kümenizin adıdır.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```

* __Kenar düğümüne__ bağlanırken 22 numaralı bağlantı noktasını kullanın. Kullanılacak tam etki alanı adı `edgenodename.clustername-ssh.azurehdinsight.net`‘tir, burada `edgenodename` kenar düğümü oluştururken girdiğiniz addır. `clustername`, kümenin adıdır.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]  
> Önceki örneklerde, parola ile kimlik doğrulaması kullandığınız veya sertifika kimlik doğrulamasının otomatik olarak yapıldığı varsayılır. Kimlik doğrulaması için bir SSH anahtar çifti kullanıyorsanız ve sertifika otomatik olarak kullanılmıyorsa, özel anahtarı belirtmek için `-i` parametresini kullanın. Örneğin, `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

Bağlandıktan sonra istem, SSH kullanıcı adını ve bağlı olduğunuz düğümü belirtecek şekilde değişir. Örneğin, `sshuser` olarak birincil baş düğüme bağlıyken komut istemi `sshuser@<active-headnode-name>:~$` değerini gösterir.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Çalışan ve Apache Zookeeper düğümlerine bağlanma

Çalışan düğümlerine ve Zookeeper düğümlerine doğrudan internet 'ten erişilemez. Bunlara, küme baş düğümleri veya kenar düğümlerinden erişilebilir. Diğer düğümlere bağlanmak için uygulamanız gereken genel adımlar şunlardır:

1. SSH kullanarak bir baş veya kenar düğümüne bağlanın:

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. Baş veya kenar düğümüne yaptığınız SSH bağlantısında `ssh` komutunu kullanarak kümedeki bir çalışan düğümüne bağlanın:

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    Düğüm adlarının bir listesini almak için [Apache ambarı REST API belgelerini kullanarak HDInsight 'ı yönetme](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) bölümüne bakın.

SSH hesabının güvenliği bir __parola__ kullanılarak sağlanıyorsa bağlanırken parolayı girin.

SSH hesabının güvenliği __SSH anahtarları__ kullanılarak sağlanıyorsa istemciden SSH iletmenin etkinleştirildiğinden emin olun.

> [!NOTE]  
> Kümedeki tüm düğümlere doğrudan erişmenin başka bir yolu ise HDInsight’ın bir Azure Sanal Ağına bağlanmasıdır. Bundan sonra, uzak makinenizi aynı sanal ağa bağlayabilir ve kümedeki tüm düğümlere doğrudan erişebilirsiniz.
>
> Daha fazla bilgi için bkz. [HDInsight için bir sanal ağ planlayın](hdinsight-plan-virtual-network-deployment.md).

### <a name="configure-ssh-agent-forwarding"></a>SSH aracı iletmeyi yapılandırma

> [!IMPORTANT]  
> Aşağıdaki adımlar, Linux veya UNIX tabanlı bir sistem için geçerlidir ve Windows 10 üzerinde Bash ile birlikte çalışır. Bu adımlar sisteminizde çalışmazsa SSH istemcinizin belgelerine bakmanız gerekebilir.

1. Bir metin düzenleyicisiyle `~/.ssh/config` dosyasını açın. Bu dosya yoksa, komut satırında `touch ~/.ssh/config` girerek oluşturabilirsiniz.

2. Aşağıdakileri metni `config` dosyasına ekleyin.

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    __Ana bilgisayar__ bilgisini, SSH kullanarak bağlandığınız düğümün adresiyle değiştirin. Önceki örnekte kenar düğümü kullanılmıştır. Bu giriş, belirtilen düğüm için SSH aracı iletmeyi yapılandırır.

3. Terminalde aşağıdaki komutu kullanarak, SSH aracı iletmeyi test edin:

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    Bu komutun aşağıdaki metne benzer bilgiler döndürmesi gerekir:

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    Hiçbir şey döndürülmezse, `ssh-agent` çalışmıyor demektir. Daha fazla bilgi için [ssh ile ssh-agent kullanma (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) sayfasındaki aracı başlatma betikleri bilgilerine bakın veya SSH istemcinizin belgelerine başvurun.

4. **SSH aracısının** çalıştığını doğruladıktan sonra SSH özel anahtarınızı aracıya eklemek için aşağıdakileri kullanın:

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    Özel anahtarınızı farklı bir dosyada saklanıyorsa, `~/.ssh/id_rsa` ile dosyanın yolunu değiştirin.

5. SSH kullanarak küme kenar düğümüne veya baş düğümlerine bağlanın. Ardından, SSH komutunu kullanarak bir çalışan veya zookeeper düğümüne bağlanın. İletilen anahtar kullanılarak bağlantı kurulur.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight ile SSH tüneli kullanma](hdinsight-linux-ambari-ssh-tunnel.md)
* [HDInsight’ta kenar düğümlerini kullanma](hdinsight-apps-use-edge-node.md#access-an-edge-node)
* [HDInsight ile SCP kullanma](./use-scp.md)