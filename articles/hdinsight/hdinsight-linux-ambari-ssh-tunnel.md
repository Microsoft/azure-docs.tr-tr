---
title: Azure HDInsight 'a erişmek için SSH tüneli kullanma
description: Linux tabanlı HDInsight düğümlerinde barındırılan Web kaynaklarına güvenli bir şekilde gözatmaya yönelik bir SSH tüneli kullanmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ef7e0450725b456a7fb2b1ab61c50d7edece52ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867568"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Apache ambarı Web Kullanıcı arabirimi, JobHistory, süs Yot, Apache Oozie ve diğer Uıto 'a erişmek için SSH tüneli kullanın

HDInsight kümeleri, Internet üzerinden Apache ambarı Web Kullanıcı arabirimine erişim sağlar. Bazı özellikler SSH tüneli gerektirir. Örneğin, bir SSH tüneli olmadan Apache Oozie Web ARABIRIMINE internet üzerinden erişilemez.

## <a name="why-use-an-ssh-tunnel"></a>Neden SSH tüneli kullanılmalıdır?

Ambarı içindeki menülerin bazıları yalnızca bir SSH tüneli üzerinden çalışır. Bu menüler, çalışan düğümleri gibi diğer düğüm türlerinde çalışan Web siteleri ve hizmetleri kullanır.

Aşağıdaki Web Uıline bir SSH tüneli gerektirir:

* JobHistory
* NameNode
* İş parçacığı yığınları
* Oozie Web Kullanıcı arabirimi
* HBase Master ve Günlükler Kullanıcı arabirimi

Web hizmetini kullanıma sunan betik eylemleriyle yüklenen hizmetler SSH tüneli gerektirir. Betik eylemiyle yüklenen ton, Web Kullanıcı arabirimine erişmek için bir SSH tüneli gerektirir.

> [!IMPORTANT]  
> Bir sanal ağ üzerinden HDInsight 'a doğrudan erişiminiz varsa, SSH tünellerini kullanmanız gerekmez. Bir sanal ağ üzerinden HDInsight 'a doğrudan erişmenin bir örneği için bkz. [HDInsight 'ı şirket içi ağ belgenize bağlama](connect-on-premises-network.md) .

## <a name="what-is-an-ssh-tunnel"></a>SSH tüneli nedir?

[Secure Shell (SSH) tüneli](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) , yerel makinenizdeki bir bağlantı noktasını HDInsight üzerindeki bir baş düğüme bağlar. Yerel bağlantı noktasına gönderilen trafik, baş düğüme bir SSH bağlantısıyla yönlendirilir. İstek, baş düğüm geldiği gibi çözümlenir. Yanıt daha sonra iş istasyonunuza tünelden geri yönlendirilir.

## <a name="prerequisites"></a>Önkoşullar

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](hdinsight-hadoop-linux-use-ssh-unix.md).

* SOCKS5 proxy kullanacak şekilde yapılandırılabilecek bir Web tarayıcısı.

    > [!WARNING]  
    > Windows Internet ayarları 'nda yerleşik olarak bulunan SOCKS proxy desteği SOCKS5 desteklemez ve bu belgedeki adımlarla çalışmaz. Aşağıdaki tarayıcılar Windows proxy ayarlarını kullanır ve şu anda bu belgedeki adımlarla çalışmaz:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome ayrıca Windows proxy ayarlarını kullanır. Ancak, SOCKS5 destekleyen uzantılar yükleyebilirsiniz. [Foxyıproxy standardı](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp)önerilir.

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>SSH komutunu kullanarak bir tünel oluşturma

Komutunu kullanarak bir SSH tüneli oluşturmak için aşağıdaki komutu kullanın `ssh` . `sshuser`HDInsight kümeniz için BIR SSH kullanıcısı ile değiştirin ve öğesini `CLUSTERNAME` HDInsight kümenizin adıyla değiştirin:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

Bu komut, trafiği SSH üzerinden kümeye yerel bağlantı noktası 9876 ' e yönlendiren bir bağlantı oluşturur. Seçenekler şunlardır:

|Seçenek |Açıklama |
|---|---|
|D 9876|Trafiği tünelden yönlendiren yerel bağlantı noktası.|
|C|Web trafiği çoğunlukla metin olduğundan tüm verileri sıkıştır.|
|2|SSH 'yi yalnızca protokol sürüm 2 ' i denemeye zorlayın.|
|q|Sessiz mod.|
|T|Yalnızca bir bağlantı noktasını iletmeniz gerektiğinden, sözde TTY ayırmayı devre dışı bırakın.|
|n|Yalnızca bir bağlantı noktasını iletmenizden, STDIN okumayı önleyin.|
|N|Yalnızca bir bağlantı noktasını iletmenizden, uzak bir komut çalıştırmayın.|
|f|Arka planda çalıştırın.|

Komut bittikten sonra, yerel bilgisayarda 9876 numaralı bağlantı noktasına gönderilen trafik küme baş düğümüne yönlendirilir.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>PuTTY kullanarak bir tünel oluşturma

[Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty) , Windows için BIR grafik SSH istemcsahiptir. PuTTY hakkında bilginiz yoksa, [Putty belgelerine](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)bakın. PuTTY kullanarak bir SSH tüneli oluşturmak için aşağıdaki adımları kullanın:

### <a name="create-or-load-a-session"></a>Oturum oluşturma veya yükleme

1. PuTTY ' i açın ve sol menüde **oturum** ' nin seçili olduğundan emin olun. Zaten bir oturum kaydettiyseniz, **kaydedilen oturumlar** listesinden oturum adı ' nı seçin ve **Yükle**' yi seçin.

1. Zaten kaydedilmiş bir oturumunuz yoksa, bağlantı bilgilerinizi girin:

    |Özellik |Değer |
    |---|---|
    |Ana bilgisayar adı (veya IP adresi)|HDInsight kümesi için SSH adresi. Örneğin, **mycluster-ssh.azurehdinsight.net**.|
    |Bağlantı noktası|22|
    |Bağlantı Türü|SSH|

1. **Kaydet**’i seçin

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png" alt-text="HDInsight Putty oturumu oluştur":::

1. İletişim kutusunun solundaki **Kategori** bölümünde **bağlantı**' yı genişletin, **SSH**' yi genişletin ve ardından **tüneller**' ı seçin.

1. **SSH bağlantı noktası iletme formunu denetleyen seçenekler** hakkında aşağıdaki bilgileri sağlayın:

    |Özellik |Değer |
    |---|---|
    |Kaynak bağlantı noktası|İletmek istediğiniz istemcideki bağlantı noktası. Örneğin, **9876**.|
    |Hedef|HDInsight kümesi için SSH adresi. Örneğin, **mycluster-ssh.azurehdinsight.net**.|
    |Dinamik|Dinamik SOCKS proxy yönlendirmesi etkinleştirilir.|

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png" alt-text="PuTTY yapılandırma tünel oluşturma seçenekleri":::

1. Ayarları eklemek için **Ekle** ' yi seçin ve ardından bir SSH bağlantısı açmak için **Aç** ' ı seçin.

1. İstendiğinde, sunucusunda oturum açın.

## <a name="use-the-tunnel-from-your-browser"></a>Tarayıcınızdan Tüneli kullanın

> [!IMPORTANT]  
> Bu bölümdeki adımlarda, tüm platformlarda aynı proxy ayarlarını sağladığından Mozilla FireFox tarayıcısı kullanılır. Google Chrome gibi diğer modern tarayıcıların tünelle çalışması için Foxi proxy gibi bir uzantı gerekebilir.

1. Tarayıcınızı, bir **SOCKS v5** ara sunucusu olarak tünel oluştururken kullandığınız **localhost** 'u ve bağlantı noktasını kullanacak şekilde yapılandırın. Firefox ayarları şöyle görünür. 9876 'den farklı bir bağlantı noktası kullandıysanız, bağlantı noktasını kullandığınız şekilde değiştirin:

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png" alt-text="Firefox tarayıcı ara sunucu ayarları":::

   > [!NOTE]  
   > **Uzak DNS** seçildiğinde, HDInsight kümesini kullanarak etki alanı adı SISTEMI (DNS) istekleri çözümlenir. Bu ayar, kümenin baş düğümünü kullanarak DNS 'i çözer.

2. Tünelin, gibi bir siteyi ziyaret ederek çalıştığını doğrulayın [https://www.whatismyip.com/](https://www.whatismyip.com/) . Döndürülen IP Microsoft Azure veri merkezi tarafından kullanılan bir tane olmalıdır.

## <a name="verify-with-ambari-web-ui"></a>Ambarı Web Kullanıcı arabirimi ile doğrulama

Küme kurulduktan sonra, aşağıdaki adımları kullanarak hizmet Web Hizmetleri 'ni ambarı Web sitesinden erişebildiğinizi doğrulayın:

1. Tarayıcınızda `http://headnodehost:8080` adresine gidin. `headnodehost`Adres, kümeye tünel üzerinden gönderilir ve ambarı 'nın üzerinde çalıştığı baş düğüme çözümlenir. İstendiğinde, kümeniz için Yönetici Kullanıcı adı 'nı (yönetici) ve parolayı girin. Ambarı Web Kullanıcı arabirimi ile ikinci bir kez sorulabilir. Öyleyse, bilgileri yeniden girin.

   > [!NOTE]  
   > `http://headnodehost:8080`Kümeye bağlanmak için adresi kullanırken tünelden bağlanıyorsunuz. İletişim, HTTPS yerine SSH tüneli kullanılarak güvenli hale getirilir. HTTPS kullanarak internet üzerinden bağlanmak için, `https://clustername.azurehdinsight.net` ' ı kullanın, burada `clustername` kümenin adıdır.

2. Ambarı web kullanıcı arabiriminden, sayfanın solundaki listeden, "öğesini seçin.

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png" alt-text="Apache ambarı hizmeti seçildi":::

3. Bir hizmet bilgisi görüntülendiğinde **hızlı bağlantılar**' ı seçin. Küme kafası düğümlerinin bir listesi görüntülenir. Baş düğümlerden birini seçin ve ardından **süs Code Kullanıcı arabirimini** seçin.

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png" alt-text="QuickLinks menüsü genişletilmiş resim":::

    > [!NOTE]  
    > __Hızlı bağlantılar__' ı seçtiğinizde bir bekleme göstergesi alabilirsiniz. Bu durum, yavaş bir internet bağlantınız varsa oluşabilir. Sunucudan veri alınması için bir dakika veya iki dakika bekleyip listeyi yeniden deneyin.
    >
    > **Hızlı bağlantılar** menüsündeki bazı girişler, ekranın sağ tarafında kesilebilir. Bu durumda, farenizi kullanarak menüyü genişletin ve menünün geri kalanını görmek için ekranı sağa kaydırmak üzere sağ ok tuşunu kullanın.

4. Aşağıdaki görüntüye benzer bir sayfa görüntülenir:

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png" alt-text="Hadoop süs Code Kullanıcı arabiriminin görüntüsü":::

    > [!NOTE]  
    > Bu sayfanın URL 'sine dikkat edin; Şuna benzer olmalıdır `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster` . Bu URI, düğümün dahili tam etki alanı adını (FQDN) kullanıyor ve yalnızca bir SSH tüneli kullanılırken erişilebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bir SSH tüneli oluşturma ve kullanma hakkında bilgi edindiğinize göre, ambarı kullanmanın diğer yolları için aşağıdaki belgeye bakın:

* [Apache ambarı kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari.md)
