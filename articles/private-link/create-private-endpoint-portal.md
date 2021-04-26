---
title: Hızlı başlangıç-Azure portal kullanarak özel uç nokta oluşturma
description: Azure portal kullanarak özel uç nokta oluşturmayı öğrenmek için bu hızlı başlangıcı kullanın.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 395912ae70c5a01bd7de9a80cf8a507dd516028e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018078"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak özel uç nokta oluşturma

Azure Web uygulamasına güvenli bir şekilde bağlanmak için özel bir uç nokta kullanarak Azure özel bağlantısı ile çalışmaya başlayın.

Bu hızlı başlangıçta, bir Azure Web uygulaması için özel bir uç nokta oluşturacak ve özel bağlantıyı test etmek için bir sanal makine dağıtacaksınız.  

Özel uç noktalar Azure SQL ve Azure depolama gibi farklı türlerde Azure hizmetleri için oluşturulabilir.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure aboneliğinizde dağıtılan **PremiumV2 katmanı** veya daha yüksek bir App Service planına sahip bir Azure Web uygulaması.  
    * Daha fazla bilgi ve bir örnek için bkz. [hızlı başlangıç: Azure 'da ASP.NET Core Web uygulaması oluşturma](../app-service/quickstart-dotnetcore.md). 
    * Web uygulaması ve uç nokta oluşturma hakkında ayrıntılı bir öğretici için bkz. [öğretici: Azure özel uç nokta kullanarak bir Web uygulamasına bağlanma](tutorial-private-endpoint-webapp-portal.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-virtual-network-and-bastion-host"></a>Sanal ağ ve savunma Konağı oluşturma

Bu bölümde, bir sanal ağ, alt ağ ve savunma ana bilgisayarı oluşturacaksınız. 

Savunma Konağı, Özel uç noktasını test etmek üzere sanal makineye güvenli bir şekilde bağlanmak için kullanılacaktır.

1. Ekranın sol üst kısmındaki **Kaynak oluştur > Ağ > Sanal ağ** seçeneğini belirleyin veya arama kutusuna **Sanal ağ** yazarak arama yapın.

2. **Sanal ağ oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | **Ayar**          | **Değer**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Proje ayrıntıları**  |                                                                 |
    | Abonelik     | Azure aboneliğinizi seçin                                  |
    | Kaynak Grubu   | **Createprivateendpointqs-RG** seçin |
    | **Örnek ayrıntıları** |                                                                 |
    | Name             | **Myvnet** girin                                    |
    | Region           | **Batı Avrupa**'yı seçin.|

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
    | Kaynak Grubu | **Createprivateendpointqs-RG** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **Myvm 'yi** girin |
    | Region | **Batı Avrupa**'yı seçin. |
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

## <a name="create-a-private-endpoint"></a>Özel Uç Nokta oluşturma

Bu bölümde, Önkoşullar bölümünde oluşturduğunuz Web uygulaması için özel bir uç nokta oluşturacaksınız.

1. Portaldaki ekranın sol üst kısmında, **kaynak oluştur**  >  **ağ**  >  **özel bağlantısı**' nı seçin veya arama kutusuna **özel bağlantı** girin.

2. **Oluştur**’u seçin.

3. **Özel bağlantı merkezi**'nde, sol taraftaki menüden **Özel uç noktalar** ' ı seçin.

4. **Özel uç noktalarında** **+ Ekle**' yi seçin.

5. **Özel uç nokta oluştur**' un **temel bilgiler** sekmesinde, girin veya bu bilgileri seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Createprivateendpointqs-RG** öğesini seçin. Bu kaynak grubunu önceki bölümde oluşturdunuz.|
    | **Örnek ayrıntıları** |  |
    | Name  | **myPrivateEndpoint** değerini girin. |
    | Region | **Batı Avrupa**'yı seçin. |

6. Sayfanın alt kısmındaki **kaynak** sekmesini veya **Sonraki: kaynak** düğmesini seçin.
    
7. **Kaynak** bölümünde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Bağlantı yöntemi | **Dizinimde bir Azure kaynağına bağlan '** ı seçin. |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak türü | **Microsoft. Web/Sites** öğesini seçin. |
    | Kaynak | **\<your-web-app-name>** öğesini seçin. </br> Önkoşullarda oluşturduğunuz Web uygulamasının adını seçin. |
    | Hedef alt kaynak | **Siteleri** seçin. |

8. Ekranın alt kısmındaki **yapılandırma** sekmesini veya **Sonraki: yapılandırma** düğmesini seçin.

9. **Yapılandırma**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Ağ** |  |
    | Sanal ağ | **Myvnet**' i seçin. |
    | Alt ağ | **Mysubnet** öğesini seçin. |
    | **Özel DNS tümleştirme** |  |
    | Özel DNS bölgesi ile tümleştirme | Varsayılan değerini **Evet** olarak bırakın. |
    | Abonelik | Aboneliğinizi seçin. |
    | Özel DNS bölgeleri | Varsayılan **(New) Privatelink.azurewebsites.net** bırakın.
    

13. **Gözden geçir ve oluştur**’u seçin.

14. **Oluştur**’u seçin.

## <a name="test-connectivity-to-private-endpoint"></a>Özel uç nokta ile bağlantıyı sına

Bu bölümde, önceki adımda oluşturduğunuz sanal makineyi özel uç nokta genelinde Web uygulamasına bağlamak için kullanacaksınız.

1. Sol taraftaki Gezinti bölmesinde **kaynak grupları** ' nı seçin.

2. **Createprivateendpointqs-RG** öğesini seçin.

3. **Myvm**' yi seçin.

4. **Myvm** için genel bakış sayfasında **Bağlan** ' ı **seçin.**

5. Mavi **kullanımı** savunma düğmesini seçin.

6. Sanal makine oluşturma sırasında girdiğiniz kullanıcı adını ve parolayı girin.

7. Bağlandıktan sonra sunucuda Windows PowerShell 'i açın.

8. `nslookup <your-webapp-name>.azurewebsites.net` yazın. **\<your-webapp-name>** Önceki adımlarda oluşturduğunuz Web uygulamasının adıyla değiştirin.  Aşağıda görüntülene benzer bir ileti alacaksınız:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Web uygulaması adı için **10.1.0.5** özel IP adresi döndürülür.  Bu adres, daha önce oluşturduğunuz sanal ağın alt ağıdır.

11. **Myvm**'e yönelik savunma bağlantısı ' nda Internet Explorer 'ı açın.

12. Web uygulamanızın URL 'sini girin, **https:// \<your-webapp-name> . azurewebsites.net**.

13. Uygulamanız dağıtılmamışsa varsayılan Web uygulaması sayfasını alacaksınız:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Varsayılan Web uygulaması sayfası." border="true":::

18. **Myvm** bağlantısını kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, sanal ağ, sanal makine ve Web uygulamasını aşağıdaki adımlarla silin:

1. Sol taraftaki menüden **kaynak grupları**' nı seçin.

2. **Createprivateendpointqs-RG** öğesini seçin.

3. **Kaynak grubunu sil**'i seçin.

4. **Kaynak grubu adını türüne** **Createprivateendpointqs-RG** girin.

5. **Sil**’i seçin.


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunu oluşturdunuz:

* Sanal ağ ve savunma Konağı.
* Sanal makine.
* Bir Azure Web uygulaması için özel uç nokta.

Özel uç nokta genelinde Web uygulamasıyla güvenli bir şekilde bağlantıyı test etmek için sanal makineyi kullandınız.



Özel bir uç noktayı destekleyen hizmetler hakkında daha fazla bilgi için bkz.:
> [!div class="nextstepaction"]
> [Özel bağlantı kullanılabilirliği](private-link-overview.md#availability)
