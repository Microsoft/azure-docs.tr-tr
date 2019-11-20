---
title: 'Öğretici: trafiği ağırlıklı uç noktalara yönlendirme-Azure Traffic Manager'
description: Bu öğretici makalede, Traffic Manager'ı kullanarak trafiği ağırlıklı uç noktalara yönlendirme açıklanmaktadır.
services: traffic-manager
author: asudbring
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: allensu
ms.openlocfilehash: 45ece08599722e04c4e6799fa5c3589cba1fca42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037927"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Öğretici: Traffic Manager'ı kullanarak ağırlıklı uç noktalar ile trafiği denetleme

Bu öğreticide, ağırlıklı yönlendirme yöntemini kullanarak kullanıcı trafiğini uç noktalar arasında yönlendirmeyi denetlemek için Azure Traffic Manager'ı kullanma adımları açıklanmaktadır. Bu yönlendirme yönteminde Traffic Manager profil yapılandırmasında her uç noktasına bir ağırlık değeri atarsınız. Kullanıcı trafiği, her bir uç noktaya atanan ağırlık değerine göre yönlendirilir. Ağırlık 1 ile 1.000 arasında bir tamsayıdır. Bir uç noktaya ne kadar yüksek bir ağırlık değeri atanırsa uç nokta o kadar yüksek önceliğe sahip olur.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * IIS üzerinde basit bir web sitesi çalıştıran iki VM oluşturma.
> * Traffic Manager'ı uygulamalı olarak görmek için iki test amaçlı VM oluşturma.
> * IIS çalıştıran VM'lerin DNS adını yapılandırma.
> * Traffic Manager profili oluşturma.
> * Traffic Manager profiline VM uç noktaları ekleme.
> * Traffic Manager'ın nasıl çalıştığını görün.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Traffic Manager'ın nasıl çalıştığını görmek için bu öğretici kapsamında aşağıdaki kaynakları dağıtın:

- Farklı Azure bölgelerinde (Doğu ABD ve Batı Avrupa) çalışan iki temel web sitesi örneği.
- Traffic Manager'ı test etmek için iki test amaçlı VM: bir VM Doğu ABD bölgesinde, diğeri ise Batı Avrupa bölgesinde olmalıdır. Traffic Manager’ın kullanıcı trafiğini uç noktasına daha yüksek bir ağırlık atanan bir web sitesine nasıl yönlendirdiğini göstermek için test VM’leri kullanılır.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

### <a name="create-websites"></a>Web sitelerini oluşturma

Bu bölümde Traffic Manager profili için iki farklı Azure bölgesinde iki hizmet uç noktası sunan iki web sitesi örneği oluşturacaksınız. İki web sitesini oluşturmak için, aşağıdaki adımları tamamlayın:

1. Biri Doğu ABD diğeri Batı Avrupa bölgesinde olmak üzere basit bir web sitesi çalıştıran iki VM oluşturun.
2. İki VM'ye de IIS sunucusu yükleyin. Varsayılan web sayfasını, web sitesini ziyaret eden kullanıcıların VM adını göreceği şekilde güncelleştirin.

#### <a name="create-vms-for-running-websites"></a>Web sitelerini çalıştırmak için VM oluşturma

Bu bölümde, Doğu ABD ve Azure bölgelerinde Batı Avrupa iki VM (*myIISVMEastUS* ve *myIISVMWestEurope*) oluşturursunuz.

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

#### <a name="install-iis-and-customize-the-default-webpage"></a>IIS yükleme ve varsayılan web sayfasını özelleştirme

Bu bölümde, myIISVMEastUS ve myIISVMWestEurope sanal makinelerinden IIS sunucusunu yüklersiniz ve sonra varsayılan Web sayfasını güncelleştirebilirsiniz. Özelleştirilmiş web sayfası, web sitesini bir web tarayıcısından ziyaret ettiğinizde bağlandığınız VM'nin adını gösterecek.

1. Soldaki menüden **Tüm kaynaklar**’ı seçin. Kaynak listesinden, **myResourceGroupTM1** kaynak grubundaki **myIISVMEastUS** seçeneğini belirleyin.
2. **Genel Bakış** sayfasında **Bağlan**'ı seçin. **Sanal makineye bağlanma** bölümünde **RDP dosyasını indir**'i seçin.
3. İndirilen .rdp dosyasını açın. İstendiğinde **Bağlan**’ı seçin. VM'yi oluştururken yapılandırdığınız kullanıcı adı ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler** > **Farklı bir hesap kullan** seçeneğini belirlemeniz gerekebilir.
4. **Tamam**’ı seçin.
5. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarı alırsanız, bağlantıya devam etmek için **Evet**’i veya **Bağlan**’ı seçin.
6. Sunucu masaüstünde **Windows Yönetimsel Araçları** > **Sunucu Yöneticisi** bölümüne gidin.
7. VM1'de Windows PowerShell'i açın. IIS sunucusunu yükleyip varsayılan htm dosyasını güncelleştirmek için aşağıdaki komutları kullanın.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![IIS yükleme ve web sayfasını özelleştirme](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. **myIISVMEastUS** ile RDP bağlantısını kapatın.
9. 1-8 arası adımları tekrarlayın. IIS yüklemek ve varsayılan Web sayfasını özelleştirmek için **myResourceGroupTM2** kaynak grubu Içinde VM **MYIISVMWESTEUROPE** ile bir RDP bağlantısı oluşturun.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>IIS çalıştıran VM'lerin DNS adlarını yapılandırma

Traffic Manager, kullanıcı trafiğini hizmet uç noktalarının DNS adına göre yönlendirir. Bu bölümde, myIISVMEastUS ve myIISVMWestEurope IIS sunucuları için DNS adlarını yapılandırırsınız.

1. Soldaki menüden **Tüm kaynaklar**’ı seçin. Kaynak listesinden, **myResourceGroupTM1** kaynak grubundaki **myIISVMEastUS** seçeneğini belirleyin.
2. **Genel bakış** sayfasının **DNS adı** bölümünde **Yapılandır**'ı seçin.
3. **Yapılandır** sayfasının DNS adı bölümünde benzersiz bir ad ekleyin. Daha sonra **Kaydet**’e tıklayın.
4. **MyResourceGroupTM2** kaynak grubundaki **myIISVMWestEurope** adlı VM için 1-3 adımlarını yineleyin.

### <a name="create-a-test-vm"></a>Test VM’si oluşturma

Bu bölümde, her bir Azure bölgesinde (**Doğu ABD** ve **Batı Avrupa**) bir VM (*myVMEastUS* ve *myVMWestEurope*) oluşturursunuz. Bu VM 'Leri, Traffic Manager trafiği daha yüksek ağırlık değerine sahip Web sitesi uç noktasına yönlendirdiğini sınamak için kullanacaksınız.

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

**Ağırlıklı** yönlendirme yöntemini temel alan bir Traffic Manager profili oluşturun.

1. Ekranın sol üst tarafından **Kaynak oluştur** > **Ağ** > **Traffic Manager profili** > **Oluştur**'u seçin.
2. **Traffic Manager profili oluştur** bölümünde aşağıdaki bilgileri girin veya seçin. Diğer ayarlar için varsayılan değerleri kabul edin ve **Oluştur**’u seçin.

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Ad                   | trafficmanager.net bölgesi içinde benzersiz bir ad girin. Traffic Manager profilinize erişmek için kullanılan trafficmanager.net DNS adında bulunur.                                   |
    | Yönlendirme yöntemi          | **Ağırlıklı** yönlendirme yöntemini seçin.                                       |
    | Abonelik            | Aboneliğinizi seçin.                          |
    | Kaynak grubu          | **Var olanı kullan**’ı seçin ve sonra **myResourceGroupTM1** öğesini seçin. |
    |        |   |

    ![Traffic Manager profili oluşturma](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager uç noktalarını ekleme

Kullanıcı trafiğini bunlara yönlendirmek için myIISVMEastUS ve myIISVMWestEurope IIS sunucularını çalıştıran iki VM 'yi ekleyin.

1. Portalın arama çubuğunda önceki bölümde oluşturduğunuz Traffic Manager profili adını arayın. Görüntülenen sonuçlardan profili seçin.
2. **Traffic Manager profilinin** **Ayarlar** bölümünde **Uç noktalar** > **Ekle**'yi seçin.
3. Aşağıdaki bilgileri girin veya seçin. Diğer ayarlar için varsayılan değerleri kabul edin ve **Tamam**’ı seçin.

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Tür                    | Azure uç noktasını girin.                                   |
    | Ad           | **myEastUSEndpoint** yazın.                                        |
    | Hedef kaynak türü           | **Genel IP adresi**'ni seçin.                          |
    | Hedef kaynak          | Genel IP adresine sahip kaynakların aynı abonelik altında listelenmesi için genel IP adresi seçin. **Kaynak** bölümünde **myIISVMEastUS-ip** adlı genel IP adresini seçin. Bu, Doğu ABD bölgesindeki IIS sunucusu VM'sinin IP adresidir.|
    |  Ağırlık      | **100** değerini girin.        |
    |        |           |

4. **MyIISVMWestEurope-IP**genel IP adresi için **myWestEuropeEndpoint** adlı başka bir uç nokta eklemek için adım 2 ve 3 ' ü tekrarlayın. Bu adres, myIISVMWestEurope adlı IIS sunucusu VM 'si ile ilişkilendirilir. **Ağırlık** için **25** değerini girin.
5. Her iki uç noktanın eklenmesi tamamlandığında, **Çevrimiçi** izleme durumuyla birlikte Traffic Manager profili bölümünde gösterilir.

## <a name="test-the-traffic-manager-profile"></a>Traffic Manager profilini test etme

Traffic Manager'ı uygulamalı olarak görmek için şu adımları gerçekleştirin:

1. Traffic Manager profilinizin DNS adını belirleyin.
2. Traffic Manager'ın nasıl çalıştığını görün.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Traffic Manager profilinin DNS adını belirleme

Bu öğreticide kolaylık olması açısından web sitelerini ziyaret etmek için Traffic Manager profilinin DNS adı kullanılmaktadır.

Traffic Manager profilinizin DNS adını belirlemek için şu adımları izleyin:

1. Portalın arama çubuğunda önceki bölümde oluşturduğunuz Traffic Manager profili adını arayın. Görüntülenen sonuçların arasından Traffic Manager profilini seçin.
2. **Genel Bakış**’ı seçin.
3. Traffic Manager profilinin DNS adı görüntülenir. Üretim dağıtımlarında bir gösterim etki alanı adını DNS CNAME kaydı kullanarak Traffic Manager etki alanı adına yönlendirirsiniz.

   ![Traffic Manager DNS adı](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager'ın nasıl çalıştığını görün

Bu bölümde Traffic Manager'ın nasıl çalıştığını görebilirsiniz.

1. Soldaki menüden **Tüm kaynaklar**’ı seçin. Kaynak listesinden, **myResourceGroupTM1** kaynak grubundaki **myVMEastUS** seçeneğini belirleyin.
2. **Genel Bakış** sayfasında **Bağlan**'ı seçin. **Sanal makineye bağlanma** bölümünde **RDP dosyasını indir**'i seçin.
3. İndirilen .rdp dosyasını açın. İstendiğinde **Bağlan**’ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler** > **Farklı bir hesap kullan** seçeneğini belirlemeniz gerekebilir.
4. **Tamam**’ı seçin.
5. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarı alırsanız, bağlantıya devam etmek için **Evet**’i veya **Bağlan**’ı seçin.
6. Web sitesini görüntülemek için myVMEastUS adlı VM'de bir web tarayıcısında Traffic Manager profilinizin DNS adını girin. Ağırlık değeri **100** ile daha yüksek olduğundan myIISVMEastUS üzerindeki IIS sunucusunda barındırılan web sitesine yönlendirilirsiniz. IIS sunucusu myIISVMWestEurope, **25**' in düşük bir uç nokta ağırlığı değerine atanır.

   ![Traffic Manager profilini test etme](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Ağırlıklı Web sitesi yanıtını görmek için VM myVMWestEurope üzerinde 1-6 arası adımları tekrarlayın.

## <a name="delete-the-traffic-manager-profile"></a>Traffic Manager profilini silme

Bu öğreticide oluşturduğunuz kaynak gruplarına ihtiyacınız kalmadığında onları silebilirsiniz. Bunun için kaynak grubunu (**ResourceGroupTM1** veya **ResourceGroupTM2**) ve ardından **Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kullanıcının coğrafi konumuna göre trafiği belirli uç noktalara yönlendirme](traffic-manager-configure-geographic-routing-method.md)
