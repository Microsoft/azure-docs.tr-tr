---
title: 'Öğretici: Web sitesi yanıtını geliştirme-Azure Traffic Manager'
description: Bu öğretici makalesinde, yüksek oranda yanıt veren bir Web sitesi oluşturmak için bir Traffic Manager profili oluşturma açıklanır.
services: traffic-manager
author: asudbring
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: allensu
ms.openlocfilehash: a988b16bb07b1486c4434114eb8653c959aefc6a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023281"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Öğretici: Traffic Manager kullanarak Web sitesi yanıtını geliştirme

Bu öğreticide, Kullanıcı trafiğini en düşük gecikmeyle Web sitesine yönlendirerek yüksek oranda yanıt veren bir Web sitesi oluşturmak için Traffic Manager nasıl kullanılacağı açıklanmaktadır. Genellikle, en düşük gecikme süresine sahip veri merkezi, coğrafi mesafede en yakın bir değer olacaktır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * IIS üzerinde basit bir web sitesi çalıştıran iki VM oluşturma
> * Traffic Manager'ı uygulamalı olarak görmek için iki test amaçlı VM oluşturma
> * IIS çalıştıran VM'lerin DNS adını yapılandırma
> * İyileştirilmiş Web sitesi performansı için Traffic Manager profili oluşturma
> * Traffic Manager profiline VM uç noktaları ekleme
> * Traffic Manager'ın nasıl çalıştığını görün

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide Traffic Manager'ın çalışmasını uygulamalı olarak görmek için şu sistemleri dağıtmanız gerekir:

- Farklı Azure bölgelerinde çalışan iki temel Web sitesi örneği- **Doğu ABD** ve **Batı Avrupa**.
- Traffic Manager bir VM 'yi test etmek için iki test VM 'si **Doğu ABD** ve **Batı Avrupa**ikinci VM. Test VM 'Leri, Traffic Manager en düşük gecikme süresini sağladığından Kullanıcı trafiğinin aynı bölgede çalışan Web sitesine nasıl yönlendirdiğini göstermek için kullanılır.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

### <a name="create-websites"></a>Web sitelerini oluşturma

Bu bölümde Traffic Manager profili için iki farklı Azure bölgesinde iki hizmet uç noktası sunan iki web sitesi örneği oluşturacaksınız. İki web sitesi oluşturmak için aşağıdaki adımları izleyin:

1. Biri **Doğu ABD** diğeri **Batı Avrupa** bölgesinde olmak üzere basit bir web sitesi çalıştıran iki VM oluşturun.
2. İki VM'de de IIS sunucusu yükleyin ve varsayılan web sitesi sayfasını web sitesini ziyaret eden kullanıcıların VM adını göreceği şekilde güncelleştirin.

#### <a name="create-vms-for-running-websites"></a>Web sitelerini çalıştırmak için VM oluşturma

Bu bölümde, **Doğu ABD** ve Azure bölgelerine **Batı Avrupa** Iki VM *myIISVMEastUS* ve *myIISVMWestEurope* oluşturulur.

1. Azure portal sol üst köşesinde, **Windows Server 2019 Datacenter** > **Işlem** > **kaynak oluştur** ' u seçin.
2. İçinde **sanal makine oluşturma**yazın veya aşağıdaki değerleri seçin **Temelleri** sekmesinde:

   - **Abonelik** > **kaynak grubu**: **Yeni oluştur** ' u seçin ve **myResourceGroupTM1**yazın.
   - **Örnek ayrıntıları** > **sanal makine adı**: tür *myIISVMEastUS*.
   - **Örnek ayrıntıları** > **bölgesi**: **Doğu ABD**seçin.
   - **Yönetici hesabı** > **Kullanıcı**adı: tercih etmek için bir Kullanıcı adı girin.
   - **Yönetici hesabı** > **parola**: seçtiğiniz parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.
   - **Gelen bağlantı noktası kuralları** > **ortak gelen bağlantı noktaları**: **Seçili bağlantı noktalarına izin ver**öğesini seçin
   - **Gelen bağlantı noktası kuralları** > **gelen bağlantı noktalarını seçin**: aşağı açılan kutuda **RDP** ve **http** ' yi seçin.

3. **Yönetim** sekmesini seçin veya **Sonraki: diskler**' i ve sonra ileri: **ağ**' ı ve ardından **İleri: yönetim**' i seçin. Altında **izleme**ayarlayın **önyükleme tanılaması** için **kapalı**.
4. **İncele ve oluştur**’u seçin.
5. Ayarları gözden geçirin ve ardından **Oluştur**' a tıklayın.  
6. **Kaynak grubu** adı *myResourceGroupTM2*, *Batı Avrupa* **konumu** ve diğer tüm ayarların *myIISVMEastUS*ile aynı olan *myIISVMWestEurope*adlı ikinci bir sanal makine oluşturmak için adımları izleyin.
7. Sanal makinelerin oluşturulması birkaç dakika sürebilir. Her iki sanal makine de oluşturulmadan kalan adımlara devam etmeyin.

   ![VM oluşturma](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS yükleme ve varsayılan web sayfasını özelleştirme

Bu bölümde, *myIISVMEastUS* ve *myIISVMWestEurope*sanal makinelerinden IIS sunucusunu yüklersiniz ve ardından varsayılan Web sitesi sayfasını güncelleştirebilirsiniz. Özelleştirilmiş web sitesi sayfası, web sitesini bir web tarayıcısından ziyaret ettiğinizde bağlandığınız VM'nin adını gösterecek.

1. Sol menüden **Tüm kaynaklar**’ı seçin ve kaynak listesinden, *myResourceGroupTM1* kaynak grubunda bulunan *myIISVMEastUS* öğesine tıklayın.
2. **Genel Bakış** sayfasında **Bağlan**'a tıklayın ve **Sanal makineye bağlanma** bölümünde **RDP dosyasını indir**'i seçin.
3. İndirilen rdp dosyasını açın. İstendiğinde **Bağlan**’ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan** seçeneğini belirlemeniz gerekebilir.
4. **Tamam**’ı seçin.
5. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarı alırsanız, bağlantıya devam etmek için **Evet**’i veya **Bağlan**’ı seçin.
6. Sunucu masaüstünde **Windows Yönetimsel Araçları**>**Sunucu Yöneticisi** bölümüne gidin.
7. VM1’de Windows PowerShell’i başlatın ve IIS sunucusunu yükleyip varsayılan htm dosyasını güncelleştirmek için aşağıdaki komutları kullanın.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![IIS yükleme ve web sayfasını özelleştirme](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. *myIISVMEastUS* ile RDP bağlantısını kapatın.
9. IIS yüklemek ve varsayılan Web sayfasını özelleştirmek için *myResourceGroupTM2* kaynak grubu Içinde VM *MYIISVMWESTEUROPE* ile bir RDP bağlantısı oluşturarak 1-8 arasındaki adımları yineleyin.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>IIS çalıştıran VM'lerin DNS adlarını yapılandırma

Traffic Manager, kullanıcı trafiğini hizmet uç noktalarının DNS adına göre yönlendirir. Bu bölümde, IIS sunucuları- *myIISVMEastUS* ve *myIISVMWestEurope*için DNS adlarını yapılandırırsınız.

1. Sol menüden **Tüm kaynaklar**’a tıklayın ve kaynak listesinden, *myResourceGroupTM1* kaynak grubunda bulunan *myIISVMEastUS* öğesini seçin.
2. **Genel bakış** sayfasının **DNS adı** bölümünde **Yapılandır**'ı seçin.
3. **Yapılandır** sayfasının DNS adı bölümünde benzersiz bir ad ekleyip **Kaydet**'i seçin.
4. *MyResourceGroupTM2* kaynak grubunda bulunan *myIISVMWestEurope* adlı VM için 1-3 adımlarını yineleyin.

### <a name="create-test-vms"></a>Test amaçlı VM'leri oluşturma

Bu bölümde, her bir Azure bölgesinde (**Doğu ABD** ve **Batı Avrupa**) bir VM (*myVMEastUS* ve *myVMWestEurope*) oluşturursunuz. Bu VM'leri web sitesine göz attığınızda Traffic Manager'ın trafiği en yakın IIS sunucusuna nasıl yönlendirdiğini test etmek için kullanacaksınız.

1. Azure portal sol üst köşesinde, **Windows Server 2019 Datacenter** > **Işlem** > **kaynak oluştur** ' u seçin.
2. İçinde **sanal makine oluşturma**yazın veya aşağıdaki değerleri seçin **Temelleri** sekmesinde:

   - **Abonelik** > **kaynak grubu**: **myResourceGroupTM1**öğesini seçin.
   - **Örnek ayrıntıları** > **sanal makine adı**: tür *myVMEastUS*.
   - **Örnek ayrıntıları** > **bölgesi**: **Doğu ABD**seçin.
   - **Yönetici hesabı** > **Kullanıcı**adı: tercih etmek için bir Kullanıcı adı girin.
   - **Yönetici hesabı** > **parola**: seçtiğiniz parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.
   - **Gelen bağlantı noktası kuralları** > **ortak gelen bağlantı noktaları**: **Seçili bağlantı noktalarına izin ver**öğesini seçin
   - **Gelen bağlantı noktası kuralları** > **gelen bağlantı noktalarını seçin**: açılan kutuda **RDP** 'yi seçin.

3. **Yönetim** sekmesini seçin veya **Sonraki: diskler**' i ve sonra ileri: **ağ**' ı ve ardından **İleri: yönetim**' i seçin. Altında **izleme**ayarlayın **önyükleme tanılaması** için **kapalı**.
4. **İncele ve oluştur**’u seçin.
5. Ayarları gözden geçirin ve ardından **Oluştur**' a tıklayın.  
6. **Kaynak grubu** adı *myResourceGroupTM2*, *Batı Avrupa* **konumu** ve diğer tüm ayarların *myVMEastUS*ile aynı olan *myVMWestEurope*adlı ikinci bir sanal makine oluşturmak için adımları izleyin.
7. Sanal makinelerin oluşturulması birkaç dakika sürebilir. Her iki sanal makine de oluşturulmadan kalan adımlara devam etmeyin.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma

En düşük gecikme süresine sahip bir uç noktaya göndererek Kullanıcı trafiğini yönlendiren bir Traffic Manager profili oluşturun.

1. Ekranın sol üst tarafından **Kaynak oluştur** > **Ağ** > **Traffic Manager profili** > **Oluştur**'u seçin.
2. **Traffic Manager profili oluştur** ekranında aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Oluştur**'u seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Ad                   | Bu adın trafficmanager.net bölgesinde benzersiz olması ve Traffic Manager profilinize erişmek için kullanılan trafficmanager.net DNS adı ile sonuçlanması gerekir.                                   |
    | Yönlendirme yöntemi          | **Performans** yönlendirme yöntemini seçin.                                       |
    | Abonelik            | Aboneliğinizi seçin.                          |
    | Kaynak grubu          | *MyResourceGroupTM1*kaynak grubunu seçin. |
    | Konum                | **Doğu ABD**’yi seçin. Bu ayar, kaynak grubunun konumunu ifade eder ve genel olarak dağıtılacak Traffic Manager profilini etkilemez.                              |
    |

    ![Traffic Manager profili oluşturma](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager uç noktalarını ekleme

Kullanıcı trafiğini kullanıcıya en yakın uç noktaya yönlendirmek için IIS sunucularını çalıştıran iki VM 'yi ekleyin- *myIISVMEastUS* & *myIISVMWestEurope* .

1. Portalın arama çubuğunda önceki bölümde oluşturduğunuz Traffic Manager profili adını arayın ve görüntülenen sonuçların arasından bu profili seçin.
2. **Traffic Manager profili** sayfasının **Ayarlar** bölümünde **Uç noktalar**'a ve ardından **Ekle**'ye tıklayın.
3. Aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Tamam**’ı seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Tür                    | Azure uç noktası                                   |
    | Ad           | myEastUSEndpoint                                        |
    | Hedef kaynak türü           | Genel IP Adresi                          |
    | Hedef kaynak          | Genel IP adresine sahip kaynakların aynı abonelik altında listelenmesi için **Genel IP adresi seçin**. **Kaynak** bölümünde *myIISVMEastUS-ip* adlı genel IP adresini seçin. Bu, Doğu ABD bölgesindeki IIS sunucusu VM'sinin IP adresidir.|
    |        |           |

4. *MyIISVMWestEurope*adlı IIS sunucusu VM ile Ilişkili olan *myIISVMWestEurope-IP* genel IP adresi için *myWestEuropeEndpoint* adlı başka bir uç nokta eklemek üzere 2 ve 3. adımları yineleyin.
5. Her iki uç noktanın eklenmesi tamamlandığında, **Çevrimiçi** izleme durumuyla birlikte **Traffic Manager profili** bölümünde gösterilir.

    ![Traffic Manager uç noktası ekleme](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager profilini test etme

Bu bölümde, Traffic Manager en düşük gecikme süresini sağlamak için Kullanıcı trafiğinin Web sitesini çalıştıran en yakın VM 'lere nasıl yönlendirdiğini test edersiniz. Traffic Manager'ı uygulamalı olarak görmek için şu adımları gerçekleştirin:

1. Traffic Manager profilinizin DNS adını belirleyin.
2. Traffic Manager'ın nasıl çalıştığını görmek için şu adımları izleyin:
    - **Doğu ABD** bölgesinde bulunan test VM 'Sinden (*myVMEastUS*), bir Web TARAYıCıSıNDA, Traffic Manager profilinizin DNS adına gidin.
    - **Batı Avrupa** bölgesinde bulunan test VM 'Sinden (*myVMWestEurope*), bir Web TARAYıCıSıNDA, Traffic Manager profilinizin DNS adına gidin.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Traffic Manager profilinin DNS adını belirleme

Bu öğreticide kolaylık olması açısından web sitelerini ziyaret etmek için Traffic Manager profilinin DNS adı kullanılmaktadır.

Traffic Manager profilinizin DNS adını belirlemek için şu adımları izleyin:

1. Portalın arama çubuğunda önceki bölümde oluşturduğunuz **Traffic Manager profili** adını arayın. Görüntülenen sonuçların arasından Traffic Manager profilini seçin.
2. **Genel Bakış**'a tıklayın.
3. **Traffic Manager profili** penceresinde yeni oluşturduğunuz Traffic Manager profilinin DNS adı görüntülenir. Üretim dağıtımlarında bir gösterim etki alanı adını DNS CNAME kaydı kullanarak Traffic Manager etki alanı adına yönlendirirsiniz.

   ![Traffic Manager DNS adı](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager'ın nasıl çalıştığını görün

Bu bölümde Traffic Manager'ın nasıl çalıştığını görebilirsiniz.

1. Sol menüden **Tüm kaynaklar**’ı seçin ve kaynak listesinden, *myResourceGroupTM1* kaynak grubunda bulunan *myVMEastUS* öğesine tıklayın.
2. **Genel Bakış** sayfasında **Bağlan**'a tıklayın ve **Sanal makineye bağlanma** bölümünde **RDP dosyasını indir**'i seçin.
3. İndirilen rdp dosyasını açın. İstendiğinde **Bağlan**’ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan** seçeneğini belirlemeniz gerekebilir.
4. **Tamam**’ı seçin.
5. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarı alırsanız, bağlantıya devam etmek için **Evet**’i veya **Bağlan**’ı seçin.
1. Web sitesini görüntülemek için *myVMEastUS* adlı VM'de bir web tarayıcısında Traffic Manager profilinizin DNS adını yazın. **Doğu ABD**konumunda bulunan VM bu yana, **Doğu ABD**bulunan en yakın IIS sunucu *myIISVMEastUS* üzerinde barındırılan en yakın Web sitesine yönlendirilir.

   ![Traffic Manager profilini test etme](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Ardından, 1-5 adımlarını kullanarak **Batı Avrupa** bulunan VM *myVMWestEurope* 'e bağlanın ve bu VM 'nin Traffic Manager profili etki alanı adına gidin. **Batı Avrupa**konumunda bulunan VM bu yana, artık **Batı Avrupa**bulunan IIS sunucusu *myIISVMWestEurope* en yakın barındırılan Web sitesine yönlendirilmiştiniz.

   ![Traffic Manager profilini test etme](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Traffic Manager profilini silme

İhtiyacınız kalmadığında kaynak gruplarını (**ResourceGroupTM1** ve **ResourceGroupTM2**) silebilirsiniz. Bunun için kaynak grubunu (**ResourceGroupTM1** veya **ResourceGroupTM2**) ve ardından **Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Trafiği bir uç nokta kümesine dağıtma](traffic-manager-configure-weighted-routing-method.md)
