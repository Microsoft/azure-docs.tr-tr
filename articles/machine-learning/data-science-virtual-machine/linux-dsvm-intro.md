---
title: 'Hızlı Başlangıç: CentOS Veri Bilimi Sanal Makinesi oluşturma'
description: Analiz ve makine öğrenimi yapmak için Linux (CentOS) için bir Veri Bilimi Sanal Makinesi yapılandırın ve oluşturun.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 1761673198b3d8ffc3740d64f525c9e70bda9e3e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204128"
---
# <a name="quickstart-set-up-a-centos-linux-data-science-virtual-machine-in-azure"></a>Hızlı Başlangıç: Azure 'da bir CentOS (Linux) Veri Bilimi Sanal Makinesi ayarlama

CentOS tabanlı bir Veri Bilimi Sanal Makinesi çalışmaya başlayın.

## <a name="prerequisites"></a>Önkoşullar

Bir CentOS Veri Bilimi Sanal Makinesi oluşturmak için bir **Azure aboneliğinizin**olması gerekir. [Ücretsiz bir abonelik oluşturun](https://azure.com/free).

## <a name="create-your-centos-data-science-virtual-machine"></a>CentOS Veri Bilimi Sanal Makinesi oluşturun

Aşağıda, CentOS Veri Bilimi Sanal Makinesi örneğini oluşturma adımları verilmiştir:

1. [Azure Portal](https://portal.azure.com) gidin. Henüz oturum açmadıysanız Azure hesabınızda oturum açmanız istenebilir. 
1. Arama çubuğuna "Data Science sanal makinesi" yazın ve CentOS DSVM ' yi seçin.

    ![CentOS arama sonucu](./media/linux-dsvm-intro/search-centos.png)

1. Sonraki pencerede **Oluştur**' u seçin.

    [![](media/linux-dsvm-intro/create-centos.png "CentOS makinesi oluşturma düğmesi")](media/linux-dsvm-intro/create-centos-expanded.png#lightbox)

1. "Sanal makine oluşturma" dikey penceresine yönlendirilmelisiniz.
   
   ![CentOS sanal makinesine karşılık gelen temel bilgiler sekmesi](./media/linux-dsvm-intro/review-create-centos.png)

1. Sihirbazın her adımını yapılandırmak için aşağıdaki bilgileri girin:

    1. **Temel**:
    
       * **Abonelik**: Birden fazla aboneliğiniz varsa makinenin oluşturulacağı ve faturalandırılabileceği bir tane seçin. Bu abonelikte kaynak oluşturma ayrıcalıklarına sahip olmanız gerekir.
       * **Kaynak grubu**: Yeni bir grup oluşturun veya var olan bir grubu kullanın.
       * **Sanal makine adı**: Sanal makinenin adını girin. Azure portal bu şekilde görünür.
       * **Bölge**: En uygun veri merkezini seçin. En hızlı ağ erişimi için bu, verilerinizin çoğunu veya fiziksel konumunuza en yakın olan veri merkezdir. [Azure bölgeleri](https://azure.microsoft.com/global-infrastructure/regions/)hakkında daha fazla bilgi edinin.
       * **Görüntü**: Varsayılan değeri değiştirmeyin.
       * **Boyutu**: Bu, genel iş yükleri için uygun bir boyutla otomatik olarak doldurulur. [Azure 'Da LINUX VM boyutları](../../virtual-machines/linux/sizes.md)hakkında daha fazla bilgi edinin.
       * **Kimlik doğrulama türü**: Daha hızlı kurulum için "parola" yı seçin. 
         
         > [!NOTE]
         > Jupi Terhub 'ı kullanmayı düşünüyorsanız, jupi Pahub 'ı SSH ortak anahtarlarını kullanacak *şekilde yapılandırılmadığından "* parola" seçeneğini belirlediğinizden emin olun.

       * **Kullanıcı adı**: Yönetici Kullanıcı adını girin. Bu, sanal makinenizde oturum açmak için kullanacağınız Kullanıcı adı ve Azure Kullanıcı adınızla aynı olmaması gerekir. Büyük harfli *harfler kullanmayın.*
         
         > [!NOTE]
         > Kullanıcı adınızla büyük harfli harfler kullanırsanız, JupyterHub çalışmaz ve 500 iç sunucu hatasıyla karşılaşacaksınız.

       * **Parola**: Sanal makinenizde oturum açmak için kullanacağınız parolayı girin.    
    
   1. **İncele ve oluştur**’u seçin.
   1. **Gözden geçir + oluştur**
      * Girdiğiniz tüm bilgilerin doğru olduğundan emin olun. 
      * **Oluştur**’u seçin.
    
    Sağlama, yaklaşık 5 dakika sürer. Durum Azure portal görüntülenir.

## <a name="how-to-access-the-centos-data-science-virtual-machine"></a>CentOS 'a erişme Veri Bilimi Sanal Makinesi

CentOS DSVM 'ye üç şekilde erişebilirsiniz:

  * Terminal oturumlar için SSH
  * Grafik oturumlarını X2Go
  * JupyterHub ve Jupyter not defterleri için JupyterLab

Ayrıca, sanal makine üzerinde jupi not defterlerini çalıştırmak ve ücretsiz hizmet katmanının sınırlamalarını atlamak için Azure Notebooks Veri Bilimi Sanal Makinesi ekleyebilirsiniz. Daha fazla bilgi için bkz. [Azure Notebooks projelerini yönetme ve yapılandırma](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

VM oluşturulduktan sonra SSH erişimiyle yapılandırıldıysa, SSH kullanarak oturum açabilirsiniz. Oluşturduğunuz hesabı kimlik bilgilerini kullan **Temelleri** bölümünde metin kabuk arabirimi için adım 3. Windows üzerinde [Putty](https://www.putty.org)gıbı bir SSH istemci aracını indirebilirsiniz. Grafik Masaüstü (X pencere sistemi) tercih ediyorsanız, PuTTY üzerinde X11 iletmeyi kullanabilirsiniz.

> [!NOTE]
> X2Go istemci testinde iletme X11 daha iyi gerçekleştirdi. X2Go istemci masaüstü bir grafik arabirim için kullanmanızı öneririz.

### <a name="x2go"></a>X2Go

Linux VM, X2Go Server ile zaten sağlanmış ve istemci bağlantılarını kabul etmeye hazır. Linux VM grafik masaüstüne bağlanmak için istemcinizi aşağıdaki yordamı tamamlayın:

1. İstemci platformunuza yönelik X2Go istemcisini indirme ve yükleme [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Yeni oluşturduğunuz sanal makineyi açarak Azure portal bulabileceğiniz sanal makinenin genel IP adresini unutmayın.

   ![CentOS makinesi IP adresi](./media/linux-dsvm-intro/centos-ip-address.png)

1. X2Go istemcisini çalıştırın. "Yeni oturum" penceresi otomatik olarak açılamaz, oturum > yeni oturum ' a gidin.

1. Elde edilen yapılandırma penceresinde aşağıdaki yapılandırma parametrelerini girin:
   * **Oturum sekmesini**:
     * **Ana bilgisayar**: Daha önce dekontunu yaptığınız VM 'nizin IP adresini girin.
     * **Oturum aç**: Linux VM 'de Kullanıcı adı girin.
     * **SSH bağlantı noktası**: Varsayılan değeri 22 ' de bırakın.
     * **Oturum türü**: Değeri **Xfce**olarak değiştirin. Şu anda, Linux VM yalnızca XFCE masaüstünü destekler.
   * **Medya sekmesi**: Ses desteğini devre dışı bırakabilirsiniz ve bunları kullanmanız gerekmiyorsa istemci yazdırma özelliğini kapatabilirsiniz.
   * **Paylaşılan klasörler**: Linux VM 'ye bağlı istemci makinelerinizden dizinler istiyorsanız, bu sekmedeki VM ile paylaşmak istediğiniz istemci makine dizinlerini ekleyin.

   ![X2go yapılandırması](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. **Tamam**’ı seçin.
1. VM 'niz için oturum açma ekranını açmak üzere X2Go penceresinin sağ bölmesindeki kutuya tıklayın.
1. VM 'niz için parola girin.
1. **Tamam**’ı seçin.
1. Bağlanmayı tamamlaması için güvenlik duvarınızı atlama X2Go izin vermeniz gerekebilir.
1. Şimdi CentOS DSVM 'nizin grafik arabirimini görmeniz gerekir. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub ve JupyterLab

CentOS DSVM, çok kullanıcılı bir Jupyıter sunucusu olan [Jupyıterhub](https://github.com/jupyterhub/jupyterhub)'ı çalıştırır. Bağlanmak için aşağıdaki adımları uygulayın:

   1. Azure portal VM 'nizi arayıp seçerek sanal makinenizin genel IP adresini unutmayın.

       ![CentOS makinesi IP adresi](./media/linux-dsvm-intro/centos-ip-address.png)

   1. Yerel makinenizden bir Web tarayıcısı açın ve https:\//Your-VM-IP: 8000 ' e giderek daha önce bir yere göz attığınız IP adresini kullanarak "sizin-VM-IP" yi değiştirin.
   1. VM 'yi oluşturmak için kullandığınız kullanıcı adını ve parolayı girin ve oturum açın. 

      ![Jupyıter oturumu gir](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Kullanılabilen birçok örnek Not defterini inceleyin.

JupyterLab, Jupyter Not defterlerinden ve JupyterHub, yeni nesil de kullanılabilir. Bu dosyaya erişmek için jupyterhub 'da oturum açın ve https:\//Your-VM-IP: 8000/User/The-username/Lab URL 'sine gidin ve VM 'yi yapılandırırken seçtiğiniz kullanıcı adıyla "adınızı-username" değerini değiştirin.

Aşağıdaki satırı ekleyerek JupyterLab 'ı `/etc/jupyterhub/jupyterhub_config.py`varsayılan not defteri sunucusu olarak ayarlayabilirsiniz:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Sonraki adımlar

İşte öğrenme ve araştırma nasıl devam edebilirsiniz:

* [Linux için veri bilimi sanal makinesi izlenecek yol bilgisi bilimi](linux-dsvm-walkthrough.md) , burada sağlanan Linux dsvm ile birkaç ortak veri bilimi görevinin nasıl yapılacağını gösterir. 
* Bu makalede açıklanan araçları deneyerek DSVM 'deki çeşitli veri bilimi araçlarını inceleyin. Ayrıca, temel bir `dsvm-more-info` giriş için sanal makinedeki kabukta de çalıştırabilirsiniz ve dsvm 'de yüklü araçlar hakkında daha fazla bilgi için işaretçiler bulabilirsiniz.  
* Sistematik olarak kullanarak uçtan uca analitik çözümler oluşturmayı öğrenin [Team Data Science Process](https://aka.ms/tdsp).
* Ziyaret [Azure AI Gallery](https://gallery.azure.ai/) Azure yapay ZEKA hizmetlerini kullanma makine öğrenimi ve veri analizi örnekleri için.
* Bu sanal makine için uygun [başvuru belgelerine](./reference-centos-vm.md) bakın.