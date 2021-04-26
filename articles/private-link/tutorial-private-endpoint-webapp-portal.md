---
title: 'Öğretici: Azure özel uç noktası kullanarak bir Web uygulamasına bağlanma'
titleSuffix: Azure Private Link
description: Özel olarak bir WebApp 'e bağlanmak için Azure özel uç noktasını kullanarak Bu öğreticiye başlayın.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 502c48a92f5b41c4434d03139335a0ce05fa451f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896982"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>Öğretici: Azure Özel Uç Noktasını kullanarak web uygulamasına bağlanma

Azure özel uç noktası, Azure 'da özel bağlantı için temel yapı taşdır. Sanal makineler (VM) gibi Azure kaynaklarının özel olarak özel bağlantı kaynaklarıyla iletişim kurmasını sağlar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir sanal ağ ve savunma ana bilgisayarı oluşturun.
> * Sanal makine oluşturur.
> * WEBAPP oluşturun.
> * Özel bir uç nokta oluşturun.
> * Web uygulaması özel uç noktasına test bağlantısı.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!Note]
> Özel uç nokta, PremiumV2-Tier, PremiumV3 katmanlı Windows Web uygulamaları, Linux Web Apps ve Azure Işlevleri Premium planı (bazen elastik Premium plan olarak adlandırılır) için genel bölgelerde kullanılabilir. 

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure aboneliği

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-virtual-network-and-bastion-host"></a>Sanal ağ ve savunma Konağı oluşturma

Bu bölümde, bir sanal ağ, alt ağ ve savunma ana bilgisayarı oluşturacaksınız. 

Savunma Konağı, Özel uç noktasını test etmek üzere sanal makineye güvenli bir şekilde bağlanmak için kullanılacaktır.

1. Ekranın sol üst kısmındaki **Kaynak oluştur > Ağ > Sanal ağ** seçeneğini belirleyin veya arama kutusuna **Sanal ağ** yazarak arama yapın.

2. **Sanal ağ oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | **Ayar**          | **Değer**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Proje ayrıntıları**  |                                                                 |
    | Abonelik     | Azure aboneliğinizi seçin                                  |
    | Kaynak Grubu   | **Myresourcegroup** seçin |
    | **Örnek ayrıntıları** |                                                                 |
    | Name             | **Myvnet** girin                                    |
    | Region           | **Batı Avrupa** seçin |

3. **IP adresleri** sekmesini seçin veya sayfanın altındaki **Sonraki: IP adresleri** düğmesini seçin.

4. **IP adresleri** sekmesinde şu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | IPv4 adres alanı | **10.1.0.0/16** girin |

5. **Alt ağ adı** altında, **varsayılan** sözcük ' ı seçin.

6. **Alt ağı Düzenle**' de şu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | Alt ağ adı | **Mysubnet** girin |
    | Alt ağ adres aralığı | **10.1.0.0/24** girin |

7. **Kaydet**’i seçin.

8. **Güvenlik** sekmesini seçin.

9. **Bastionhost** altında **Etkinleştir**' i seçin. Bu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | Savunma adı | **Mybastionhost** girin |
    | AzureBastionSubnet adres alanı | **10.1.1.0/24** girin |
    | Genel IP Adresi | **Yeni oluştur**’u seçin. </br> **Ad** Için **Mybastionıp** girin. </br> **Tamam**’ı seçin. |


8. **Gözden geçir + oluştur** sekmesini seçin ya da **gözden geçir + oluştur** düğmesini seçin.

9. **Oluştur**’u seçin.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Bu bölümde, Özel uç noktayı test etmek için kullanılacak bir sanal makine oluşturacaksınız.


1. Portalın sol üst tarafında, **kaynak oluştur**  >  **işlem**  >  **sanal makinesi** ' ni seçin veya arama kutusunda **sanal makine** ara ' yı seçin.
   
2. **Sanal makine oluştur**' da **temel bilgiler** sekmesinde değerleri yazın veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Myresourcegroup** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **Myvm 'yi** girin |
    | Region | **Batı Avrupa** seçin |
    | Kullanılabilirlik seçenekleri | **Altyapı yedekliliği gerekli değil** ' i seçin |
    | Görüntü | **Windows Server 2019 Datacenter-Gen1** seçin |
    | Azure Spot örneği | **Hayır** seçin |
    | Boyut | VM boyutunu seçin veya varsayılan ayarı yapın |
    | **Yönetici hesabı** |  |
    | Kullanıcı adı | Kullanıcı adı girin |
    | Parola | Parola girin |
    | Parolayı onayla | Parolayı yeniden girin |

3. **Ağ** sekmesini seçin veya **Sonraki: diskler**' i ve sonra **İleri: ağ**' ı seçin.
  
4. Ağ sekmesinde, şunu seçin veya girin:

    | Ayar | Değer |
    |-|-|
    | **Ağ arabirimi** |  |
    | Sanal ağ | **myVNet** |
    | Alt ağ | **mySubnet** |
    | Genel IP | **Hiçbiri** seçeneğini belirtin. |
    | NIC ağ güvenlik grubu | **Temel**|
    | Genel gelen bağlantı noktaları | **Hiçbiri** seçeneğini belirtin. |
   
5. **Gözden geçir ve oluştur**’u seçin. 
  
6. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

## <a name="create-web-app"></a>Web uygulaması oluşturma

Bu bölümde, bir Web uygulaması oluşturacaksınız.

1. Sol taraftaki menüde, **kaynak**  >  **depolama**  >  **Web uygulaması** oluştur ' u veya arama kutusunda **Web uygulaması** ara ' yı seçin.

2. **Web uygulaması oluştur** ' un **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Myresourcegroup** seçin |
    | **Örnek ayrıntıları** |  |
    | Name | **MyWebApp** girin. Ad kullanılamıyorsa, benzersiz bir ad girin. |
    | Yayımlama | **Kod**’u seçin. |
    | Çalışma zamanı yığını | **.NET Core 3,1 (LTS)** seçeneğini belirleyin. |
    | Operating System | **Windows**' u seçin. |
    | Region | **Batı Avrupa** seçin |
    | **App Service Planı** |  |
    | Windows planı (Batı Avrupa) | **Yeni oluştur**’u seçin. </br> **Ad** alanına **myserviceplan** yazın. |
    | Sku ve boyut | **Boyutu değiştir**'i seçin. </br> **Spec Seçicisi** ekranında **P2V2** öğesini seçin. </br> **Uygula**’yı seçin. |
   
3. **Gözden geçir ve oluştur**’u seçin.

4. **Oluştur**’u seçin.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Azure portal 'de Web uygulaması oluşturma temelleri sekmesi." border="true":::

## <a name="create-private-endpoint"></a>Özel uç nokta oluştur

1. Sol taraftaki menüden **tüm kaynaklar**  >  **MyWebApp** ' i veya oluşturma sırasında seçtiğiniz adı seçin.

2. Web uygulamasına genel bakış ' da **Ayarlar**  >  **ağ**' ı seçin.

3. **Ağ**' da **Özel uç nokta bağlantılarınızı yapılandırın**' ı seçin.

4. **Özel uç nokta bağlantıları** ekranında **+ Ekle** ' yi seçin.

5. **Özel uç nokta Ekle** ekranında aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Mywebappendpoint** girin. |
    | Abonelik | Aboneliğinizi seçin. |
    | Sanal ağ | **Myvnet**' i seçin. |
    | Alt ağ | **Mysubnet** öğesini seçin. |
    | Özel DNS bölgesi ile tümleştirme | **Evet**’i seçin. |

6. **Tamam**’ı seçin.
    

## <a name="test-connectivity-to-private-endpoint"></a>Özel uç nokta ile bağlantıyı sına

Bu bölümde, önceki adımda oluşturduğunuz sanal makineyi özel uç nokta genelinde Web uygulamasına bağlamak için kullanacaksınız.

1. Sol taraftaki Gezinti bölmesinde **kaynak grupları** ' nı seçin.

2. **myResourceGroup** öğesini seçin.

3. **Myvm**' yi seçin.

4. **Myvm** için genel bakış sayfasında **Bağlan** ' ı **seçin.**

5. Mavi **kullanımı** savunma düğmesini seçin.

6. Sanal makine oluşturma sırasında girdiğiniz kullanıcı adını ve parolayı girin.

7. Bağlandıktan sonra sunucuda Windows PowerShell 'i açın.

8. `nslookup <webapp-name>.azurewebsites.net` yazın. **\<webapp-name>** Önceki adımlarda oluşturduğunuz Web uygulamasının adıyla değiştirin.  Aşağıda görüntülene benzer bir ileti alacaksınız:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Web uygulaması adı için **10.1.0.5** özel IP adresi döndürülür.  Bu adres, daha önce oluşturduğunuz sanal ağın alt ağıdır.

9. Yerel bilgisayarınızda bir Web tarayıcısı açın ve **https:// \<webapp-name> . azurewebsites.net** Web uygulamanızın dış URL 'sini girin.

10. Bir **403** sayfası albildiğinizi doğrulayın. Bu sayfa, Web uygulamasının dışarıdan erişilebilir olmadığını gösterir.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="dış Web uygulaması adresi için 403 sayfası." border="true":::

11. **Myvm**'e yönelik savunma bağlantısı ' nda Internet Explorer 'ı açın.

12. Web uygulamanızın URL 'sini girin, **https:// \<webapp-name> . azurewebsites.net**.

13. Varsayılan Web uygulaması sayfasını almanızı doğrulayın.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Varsayılan Web uygulaması sayfası." border="true":::

18. **Myvm** bağlantısını kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, sanal ağ, sanal makine ve Web uygulamasını aşağıdaki adımlarla silin:

1. Sol taraftaki menüden **kaynak grupları**' nı seçin.

2. **myResourceGroup** öğesini seçin.

3. **Kaynak grubunu sil**'i seçin.

4. **Kaynak grubu adını türüne** **myresourcegroup** girin.

5. **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Özel bağlantı hizmeti oluşturmayı öğrenin:
> [!div class="nextstepaction"]
> [Özel Bağlantı hizmeti oluşturma](create-private-link-service-portal.md)
