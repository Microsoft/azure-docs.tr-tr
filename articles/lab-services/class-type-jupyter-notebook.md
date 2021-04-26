---
title: Python ve jupi Not defterleri ile veri bilimi öğretmek için laboratuvar kurma | Microsoft Docs
description: Python ve Jupyıter not defterlerini kullanarak veri bilimi öğretmek için laboratuvar ayarlamayı öğrenin.
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: d4034f889334bcf1e4eaa3710a32db60b6a9936b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94648030"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Python ve Jupyıter Not defterleri ile veri bilimi öğretmek için laboratuvar ayarlama
Bu makalede, öğrencilerin [jupi not defterlerini](http://jupyter-notebook.readthedocs.io/)nasıl kullanacağınızı ve öğrencilerin sanal makinelerinde (VM) kendi not defterlerine nasıl bağlanabilmelerini öğretmek için gereken araçlarla laboratuvar hizmetlerinde bir şablon sanal MAKINESINI (VM) nasıl ayarlayabileceğiniz özetlenmektedir.

Jupyter Not defterleri, zengin metin ve yürütülebilir Python kaynak kodunu not defteri adlı tek bir tuval üzerinde kolayca birleştirebilmenizi sağlayan açık kaynaklı bir projem. Bir not defteri çalıştırmak, giriş ve çıkışları doğrusal bir kaydıyla sonuçlanır. Bu çıktılar metin, bilgi tabloları, dağılım çizimleri ve daha fazlasını içerebilir.

## <a name="set-up-the-lab"></a>Laboratuvarı ayarlama

### <a name="lab-configuration"></a>Laboratuvar yapılandırması
Bu Laboratuvarı ayarlamak için bir Azure aboneliğine ve bir laboratuvar hesabına erişmeniz gerekir. Mevcut bir Azure aboneliğine erişip erişetiğinizi öğrenmek için kuruluşunuzun Yöneticisi ile tartışın. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

Azure aboneliğiniz olduğunda öğreticideki yönergeleri izleyerek Azure Lab Services yeni bir laboratuvar hesabı oluşturun: [bir laboratuvar hesabı kurun](tutorial-setup-lab-account.md). Ayrıca var olan bir laboratuvar hesabı da kullanabilirsiniz.

### <a name="lab-account-settings"></a>Laboratuvar hesabı ayarları
Laboratuvar hesabı için aşağıdaki tabloda açıklanan ayarları etkinleştirin. Market görüntülerinin etkinleştirilmesi hakkında daha fazla bilgi için bkz. [Laboratuvar oluşturucuları için kullanılabilen Market görüntülerini belirtme](specify-marketplace-images.md).

| Laboratuvar hesabı ayarı | Yönergeler |
| ------------------- | ------------ |
| Market görüntüsü | Laboratuvar hesabınızda, işletim sistemi ihtiyaçlarınıza göre Azure Marketi görüntülerinden birini etkinleştirin: <br/><ul><li>Veri Bilimi Sanal Makinesi – Windows Server 2019</li><li>Veri Bilimi Sanal Makinesi – Ubuntu 18,04</li></ul> |

> [!NOTE]
> Bu makalede, Jupyter Notebook ile önceden yapılandırılmış olduklarından Azure Marketi 'nde bulunan veri bilimi sanal makine görüntüleri kullanılmaktadır. Bununla birlikte, bu görüntüler veri bilimi için birçok farklı geliştirme ve modelleme aracı da içerir. Bu ek araçlara gerek duymuyorsanız ve yalnızca jupi Not defterleri ile hafif bir kurulum istiyorsanız, özel bir VM görüntüsü oluşturun. Örneğin, [Azure 'Da JupyperHub 'ı yükleme](http://tljh.jupyter.org/en/latest/install/azure.html). Özel görüntü oluşturulduktan sonra, Azure Lab Services içindeki görüntüyü kullanmak için paylaşılan bir görüntü galerisine karşıya yükleyebilirsiniz. [Azure Lab Services ' de paylaşılan görüntü galerisini kullanma](how-to-attach-detach-shared-image-gallery.md)hakkında daha fazla bilgi edinin. 

### <a name="lab-settings"></a>Laboratuvar ayarları
Bir sınıf Laboratuvarı ayarlarken aşağıdaki tabloda gösterildiği gibi **sanal makine boyutunu** ve **sanal makine görüntüsü** ayarlarını yapılandırın. Sınıf Laboratuvarı oluşturma hakkında yönergeler için bkz. [sınıf Laboratuvarı ayarlama](tutorial-setup-classroom-lab.md).

| Laboratuvar ayarları | Değer/yönergeler |
| ------------ | ------------------ | 
| Sanal makine boyutu | <p>Burada seçtiğiniz boyut, çalıştırmak istediğiniz iş yüküne bağlıdır:</p><ul><li>Küçük veya Orta: Jupyıter not defterlerine erişim için temel kurulum için iyi</li><li>Küçük GPU (Işlem) – yapay zeka ve derin öğrenme gibi yoğun işlem yoğunluğu ve ağ kullanımı yoğun uygulamalar için idealdir</li></ul> | 
| Sanal makine görüntüsü | <p>İşletim sistemi ihtiyaçlarınıza göre aşağıdaki görüntülerden birini seçin:</p><ul><li>[Veri Bilimi Sanal Makinesi – Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Veri Bilimi Sanal Makinesi – Ubuntu 18,04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>Şablon sanal makinesi
Laboratuvar oluşturduktan sonra, seçtiğiniz sanal makine boyutu ve görüntü temel alınarak bir şablon VM 'si oluşturulur. Şablon VM 'yi, bu sınıf için öğrencilerinize sağlamak istediğiniz her şey ile yapılandırırsınız. Daha fazla bilgi edinmek için bkz. [şablon sanal makinesini yönetme](how-to-create-manage-template.md). 

Veri Bilimi VM'si görüntüleri varsayılan olarak bu tür bir sınıf için gereken birçok veri bilimi çerçevesi ve aracı ile gelir. Örneğin, görüntüler şunları içerir:

- [Jupyter Not defterleri](http://jupyter-notebook.readthedocs.io/): veri bilimcilerinin ham veri almasına, hesaplamalar çalıştırmasına ve sonuçların tümünü aynı ortamda görüntülemesine olanak tanıyan bir Web uygulaması. Şablon VM 'de yerel olarak çalışır.  
- [Visual Studio Code](https://code.visualstudio.com/): bir not defteri yazarken ve test edilirken zengin etkileşimli bir deneyim sağlayan tümleşik bir geliştirme ORTAMı (IDE). Daha fazla bilgi için bkz. [Visual Studio Code jupi Not defterleri Ile çalışma](https://code.visualstudio.com/docs/python/jupyter-support).

### <a name="provide-notebooks-for-the-class"></a>Sınıf için Not defterleri sağlama
Sonraki görev, öğrencilerin kullanmasını istediğiniz not defterlerini sunmaları sağlamaktır. Kendi not defterlerinizi sağlamak için, not defterlerini şablon VM 'de yerel olarak kaydedebilirsiniz. 

Azure Machine Learning örnek not defterlerini kullanmak istiyorsanız, bkz. [jupi Not defterleri ile ortam yapılandırma](../machine-learning/how-to-configure-environment.md#jupyter). 

### <a name="optional-enable-graphical-desktop-for-linux"></a>İsteğe bağlı: Linux için grafik Masaüstünü Etkinleştirme 
**Veri bilimi sanal makinesi – Ubuntu** görüntüsü, X2GO Server ile zaten sağlanmış ve istemci bağlantılarını kabul etmeye hazır. Şablon sanal makinesi ayarlanırken başka bir adım gerekmez. 

### <a name="publish-the-template-machine"></a>Şablon makinesini yayımlama
Şablonu yayımladığınızda, laboratuvarınızda kayıtlı her öğrenci, bir şablon VM 'nin kopyasını, üzerinde ayarladığınız tüm yerel araçları ve not defterlerini alacak şekilde alır.

## <a name="how-students-connect-to-jupyter-notebooks"></a>Öğrenciler jupi not defterlerine nasıl bağlanır?
Şablonu yayımladıktan sonra, her öğrenci, jupi Not defterleri dahil olmak üzere sınıfı için önceden yapılandırdığınız her şeyi içeren bir VM 'ye erişebilir. Aşağıdaki bölümlerde, öğrencilerin jupi not defterlerine bağlanması için farklı yollar gösterilmektedir. 

### <a name="for-windows-vms"></a>Windows VM'leri için
Windows VM 'Leri ile öğrenciler sağladıysanız, sanal makinelerinize bağlanmaları ve yerel olarak kullanılabilir olan Jupyıter not defterlerini kullanması gerekir. 

Bir Windows VM 'ye bağlanmak için bir öğrenci Uzak Masaüstü bağlantısı (RDP) kullanabilir. Ayrıntılı adımlar için bkz. [sınıf laboratuvarına erişme](how-to-use-classroom-lab.md). 

Mac veya Kmebook kullanan bir öğrenci, veri bilimi Windows VM 'sine bağlanmak için aşağıdaki makalelerdeki yönergeleri izleyebilir. 

- [Mac üzerinde RDP kullanarak VM 'ye bağlanma](connect-virtual-machine-mac-remote-desktop.md)
- [Bir Kmebook 'ta RDP kullanarak VM 'ye bağlanma](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Linux VM'leri için
Linux VM 'Leri olan öğrenciler sağladıysanız, öğrencilerin VM 'lerdeki Jupyter not defterlerine bağlanmak için kullanabileceği birkaç seçenek vardır:

- SANAL makineye bağlandıktan sonra Jupyıter not defterlerine yerel olarak erişin
    - Terminal oturumları için sanal makineye SSH
     - Grafik oturumları için VM 'ye X2Go bağlantısı
- Öğrencinin yerel bilgisayarından VM 'deki jupi sunucusuna doğrudan bağlanmak için SSH tüneli kullanın. 

Aşağıdaki bölümlerde, jupi not defterlerine bağlanmanın bu yolları hakkında ayrıntılı bilgi sağlanmaktadır. 

#### <a name="ssh-to-virtual-machine"></a>SSH 'den sanal makineye
Öğrenciler, bir Terminal oturumundan Linux sanal makinelerine SSH aracılığıyla bağlanabilir. Ayrıntılı adımlar için bkz. [sınıf laboratuvarına erişme](how-to-use-classroom-lab.md). Windows istemci makinesini kullanıyorsa, [Putty](https://www.putty.org/) 'i Indirerek veya [Windows 'da OpenSSH](/windows-server/administration/openssh/openssh_install_firstuse) 'yi komut isteminden SSH 'ye etkinleştirerek bir SSH istemcisini etkinleştirmeleri gerekir. 

1.  VM’yi başlatın.
2.  VM çalışmaya başladıktan sonra **Bağlan**' a tıklayın, bu, aşağıdaki örnekte olduğu gibi SSH komut dizesini sağlayan bir iletişim kutusu açılır:
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  Komut isteminize veya terminale gidin ve bu komutu yapıştırın ve **ENTER** tuşuna basın.
4.  VM 'de oturum açmak için parolayı girin. 

Öğrenciler VM 'lere bağlandıktan sonra, jupi not defterlerini yerel olarak erişebilir ve çalıştırabilir.

#### <a name="x2go-to-virtual-machine"></a>X2Go-sanal makineye
**Veri bilimi sanal makinesi – Ubuntu** görüntüsü, X2GO Server ile zaten sağlanmış ve istemci bağlantılarını kabul etmeye hazır. Linux makinesinin grafik masaüstüne bağlanmak için öğrencilerin, istemci makinelerinde X2Go kurulumu için bu tek seferlik adımları izlemesi gerekir:

1.  İstemci platformunuz için [X2Go istemcisini](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) indirin ve yükleyin.
2.  [Azure Lab Services portalında](https://labs.azure.com), bağlanmak ISTEDIĞINIZ Linux VM 'nin başlatıldığından emin olun.
3.  VM çalışmaya başladıktan sonra **Bağlan**' a tıklayın, bu, aşağıdaki örnekte olduğu gibi SSH komut dizesini sağlayan bir iletişim kutusu açılır:

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. Bu bilgileri aldıktan sonra, X2Go istemci uygulamasını açın ve yeni bir oturum oluşturun. 
5.  **Oturum tercihleri** bölmesinde aşağıdaki değerleri girin:
    - **Oturum adı**: istediğiniz şey olabilir, ancak laboratuvar sanal makinenizin adını kullanmanızı öneririz.
     - **Ana bilgisayar**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **Oturum açma**: öğrenci
     - **SSH bağlantı noktası**: 12345
     - **Oturum türü**: Xfce
6. **Tamam**’ı seçin. 

    > [!NOTE]
     > Yeni bir X2Go oturumu oluştururken, RDP bağlantı noktasını **DEĞIL** SSH bağlantı noktasını kullandığınızdan emin olun.

Şimdi sanal makineye bağlanmak için şu adımları izleyin:    

1.  X2Go istemcisinde, bağlanmak istediğiniz sanal makineye çift tıklayın. 

    ![X2Go istemcisi](./media/class-type-jupyter-notebook/x2go-client.png)
2. SANAL makineye bağlanmak için parolayı girin. (Bağlanmayı tamamlaması için güvenlik duvarınızı atlamak üzere X2Go izni vermeniz gerekebilir.)
3.  Artık Ubuntu Veri Bilimi VM'si grafik arabirimini görmeniz gerekir.


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>VM 'de jupi sunucusuna SSH tüneli
Bazı öğrenciler doğrudan yerel bilgisayarından VM 'lerinin içindeki jupi sunucusuna doğrudan bağlamak isteyebilir. SSH protokolü, yerel bilgisayar ile uzak sunucu arasında bağlantı noktası iletmeyi (bizim örneğimizde, öğrencinin laboratuvar VM 'si), böylece sunucudaki belirli bir bağlantı noktası üzerinde çalışan bir uygulamanın yerel bilgisayardaki eşleme bağlantı noktasına **tünel** oluşturmasını sağlar. Öğrenciler, laboratuvar VM 'lerinde Jupyter sunucusuna SSH tüneli eklemek için aşağıdaki adımları izlemelidir:

1.  [Azure Lab Services portalında](https://labs.azure.com), bağlanmak ISTEDIĞINIZ Linux VM 'nin başlatıldığından emin olun.
2.  VM çalışmaya başladıktan sonra **Bağlan**' a TıKLAYARAK, SSH komut dizesini sağlayan bir iletişim kutusu açılır. Bu, aşağıdaki dize gibi görünür:

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. Yerel bilgisayarınızda bir Terminal veya komut istemi başlatın ve SSH bağlantı dizesini buna kopyalayın. Ardından, `-L 8888:localhost:8888` bağlantı noktaları arasında **tüneli** oluşturan komut dizesine ekleyin. Son dize şöyle görünmelidir:

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. Komutu çalıştırmak için **ENTER** tuşuna basın. 
5.  İstendiğinde, laboratuvar VM 'sine bağlanmak için parolayı girin. 
6.  SANAL makineye bağlandıktan sonra şu komutu kullanarak Jupyter sunucusunu başlatın: 

    ```bash
     jupyter notebook
     ```
7. Komutun çalıştırılması, size Terminal veya komut isteminde bir URL sağlar. URL şöyle görünmelidir:

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. Bu URL 'YI yerel bilgisayarınızdaki bir tarayıcıya yapıştırarak Jupyter Notebook bağlanın ve üzerinde çalışın. 

    > [!NOTE]
    > Visual Studio Code Ayrıca harika bir [Jupyter Notebook düzenlemesi deneyimi](https://code.visualstudio.com/docs/python/jupyter-support)sunar. [Uzak bir jupi sunucusuna bağlanma](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) ve önceki adımdan aynı URL 'yi kullanarak tarayıcıdan değil vs Code bağlantı kurmak için yönergeleri takip edebilirsiniz. 


## <a name="cost-estimate"></a>Maliyet tahmini
Bu sınıf için olası bir maliyet tahminini ele alalım. 25 öğrencilerden oluşan bir sınıf kullanacağız. 20 saatlik zamanlanan sınıf zamanı vardır. Ayrıca, her öğrenci, zamanlanan sınıf zamanı dışında ev ödevleri veya atamalar için 10 saatlik kota alır. Seçtiğimiz VM boyutu, 139 laboratuvar birimi olan küçük GPU (işlem) idi. Küçük (20 laboratuvar birimi) veya orta boyut (42 Lab birimi) kullanmak istiyorsanız, aşağıdaki denklemde bulunan laboratuvar birimi bölümünü doğru sayı ile değiştirebilirsiniz.  

Bu sınıf için olası bir maliyet tahmini örneği aşağıda verilmiştir: 25 öğrenci * (20 zamanlanan saat + 10 kota saati) * 139 Lab birim * 0,01 saat başına USD = 1042,5 ABD

Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure Lab Services fiyatlandırması](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Sonuç
Bu makalede, bir Jupyıter Not defteri sınıfı için Laboratuvar oluşturma adımlarını ele aldık. Diğer makine öğrenimi sınıfları için benzer bir kurulum kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adımlar, herhangi bir laboratuvarı ayarlamak için ortaktır.

- [Şablon oluşturma ve yönetme](how-to-create-manage-template.md)
- [Kullanıcı ekle](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Öğrenciler için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users)