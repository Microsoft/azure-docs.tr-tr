---
title: 'Öğretici: Azure özel uç noktası kullanarak bir Azure Cosmos hesabına bağlanma'
titleSuffix: Azure Private Link
description: Bir Azure Cosmos hesabına özel olarak bağlanmak için Azure özel uç noktasını kullanarak Bu öğreticiye başlayın.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 3a7e75641f6bb84b490231fcd06e04c3cbad06d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063475"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>Öğretici: Azure özel uç noktası kullanarak bir Azure Cosmos hesabına bağlanma

Azure özel uç noktası, Azure 'da özel bağlantı için temel yapı taşdır. Sanal makineler (VM) gibi Azure kaynaklarının özel olarak özel bağlantı kaynaklarıyla iletişim kurmasını sağlar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir sanal ağ ve savunma ana bilgisayarı oluşturun.
> * Sanal makine oluşturur.
> * Özel bir uç nokta ile Cosmos DB hesabı oluşturun.
> * Cosmos DB hesabı özel uç noktasına yönelik bağlantıyı test edin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

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
    | Region           | **Doğu ABD** seçin |

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
    | Region | **Doğu ABD** seçin |
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

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>Özel uç nokta ile Cosmos DB hesabı oluşturma

Bu bölümde bir Cosmos DB hesabı oluşturacak ve özel uç noktasını yapılandıracaksınız.

1. Sol taraftaki menüde,   >    >  **Hesap Cosmos DB** kaynak veritabanları oluştur ' u seçin veya arama kutusunda **Cosmos DB hesabı** arayın.

2. **Cosmos DB oluşturma hesabı** ' nın **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin. |
    | Kaynak Grubu | **myResourceGroup** öğesini seçin. |
    | **Örnek ayrıntıları** |  |
    | Hesap adı | **Mycosmosdb** girin. Ad kullanılamıyorsa, benzersiz bir ad girin. |
    | API | **Çekirdek (SQL)** öğesini seçin. |
    | Konum | **Doğu ABD**’yi seçin. |
    | Kapasite modu | Varsayılan **sağlanan aktarım hızını** bırakın. |
    | Ücretsiz Katman İndirimi Uygula | **Varsayılan olarak** bırakın. |
    | Coğrafi Yedeklilik | Varsayılan **devre dışı** bırakın. |
    | Birden Çok Bölgeli Yazmalar | Varsayılan **devre dışı** bırakın. |
   
3. **Ağ** sekmesini seçin veya **Sonraki: ağ** düğmesini seçin.

4. **Ağ** sekmesinde, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Ağ bağlantısı** | |
    | Bağlantı yöntemi | **Özel uç nokta**' ı seçin. |
    | **Güvenlik Duvarını yapılandırma** | |
    | Azure portal erişime izin ver | Varsayılan **Izin ver**' i bırakın. |
    | IP 'lerimin erişimine izin ver | Varsayılan **Reddet**' i bırakın. |

5. **Özel uç noktada** **+ Ekle**' yi seçin.

6. **Özel uç nokta oluştur** ' da aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Myresourcegroup** seçin |
    | Konum | **Doğu ABD** seçin |
    | Name | **Myprivateendpoint** girin |
    | Hedef alt kaynak | Varsayılan çekirdeği bırak **(SQL)** |
    | **Ağ** |  |
    | Sanal ağ | **Myvnet** seçin |
    | Alt ağ | **mySubnet** öğesini seçin |
    | **Özel DNS tümleştirme** |
    | Özel DNS bölgesi ile tümleştirme | Varsayılan değeri bırak **Evet** |
    | Özel DNS Bölgesi | Varsayılan (yeni) privatelink.documents.azure.com kalsın |

7. **Tamam**’ı seçin.

8. **Gözden geçir ve oluştur**’u seçin.

9. **Oluştur**’u seçin.

### <a name="add-a-database-and-a-container"></a>Veritabanı ve kapsayıcı ekleme

1. **Kaynak için alındı** ' ı seçin veya Azure Portal sol taraftaki menüden **tüm kaynaklar**  >  **mycosmosdb**' yi seçin.

2. Sol taraftaki menüde **Veri Gezgini**' yi seçin.

3. **Veri Gezgini** penceresinde **yeni kapsayıcı**' yı seçin.

4. **Kapsayıcı Ekle**' de aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Veritabanı Kimliği | **Yeni oluştur**' un varsayılan öğesini bırakın. </br> Metin kutusuna **mydatabaseıd** değerini girin. |
    | Aktarım hızı (400-100.000 RU/sn) | Varsayılan **olarak varsayılan**' i bırakın. </br> Metin kutusuna **400** girin. |
    | Kapsayıcı Kimliği | **Mycontainerıd** girin |
    | Bölüm anahtarı | **/MyKey** girin |

5. **Tamam**’ı seçin.

6. CosmosDB hesabının **Ayarlar** bölümünde **anahtarlar**' ı seçin.

7. **BIRINCIL bağlantı dizesinde** Kopyala ' yı seçin.

## <a name="test-connectivity-to-private-endpoint"></a>Özel uç nokta ile bağlantıyı sına

Bu bölümde, önceki adımda oluşturduğunuz sanal makineyi kullanarak özel uç nokta genelinde Cosmos DB hesabına bağlanırsınız.

1. Sol taraftaki Gezinti bölmesinde **kaynak grupları** ' nı seçin.

1. **myResourceGroup** öğesini seçin.

1. **Myvm**' yi seçin.

1. **Myvm** için genel bakış sayfasında **Bağlan** ' ı **seçin.**

1. Mavi **kullanımı** savunma düğmesini seçin.

1. Sanal makine oluşturma sırasında girdiğiniz kullanıcı adını ve parolayı girin.

1. Bağlandıktan sonra sunucuda Windows PowerShell 'i açın.

1. `nslookup <cosmosdb-account-name>.documents.azure.com`Ad çözümlemesini girin ve doğrulayın. **\<cosmosdb-account-name>** Önceki adımlarda oluşturduğunuz Cosmos DB hesabının adıyla değiştirin. 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```
    Cosmos DB hesap adı için **10.1.0.5** özel IP adresi döndürülür.  Bu adres, daha önce oluşturduğunuz sanal ağın alt ağıdır.
    
1. Portaldan Azure Cosmos DB birincil Bağlantı dizenizi alın. Geçerli bir bağlantı dizesi şu biçimdedir:
   
   SQL API hesapları için: `https://<accountName>.documents.azure.com:443/;AccountKey=<accountKey>;` MongoDB için Azure Cosmos DB API 'si için: `mongodb://<accountName>:<accountKey>@cdbmongo36.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false`

1. [Microsoft Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) sanal makineye yükler.

1. **Microsoft Azure Depolama Gezgini** yüklendikten sonra **son** ' u seçin.  Uygulamayı açmak için kutuyu işaretli bırakın.

1. **Azure depolama 'Ya Bağlan** ekranında **iptal**' i seçin.

1. Depolama Gezgini **Cosmos DB hesaplar** ' da sağ fare düğmesini seçin ve **Cosmos DB Bağlan**' ı seçin.

1. Varsayılan **SQL** ' **i Select API** altında bırakın.

1. **Bağlantı dizesi** altındaki kutuya, bağlantı dizesini önceki adımlarda kopyaladığınız Cosmos DB hesabından yapıştırın.

1. **İleri**’yi seçin.

1. **Bağlantı özetinde** ayarların doğru olduğundan emin olun.  

1. **Bağlan**’ı seçin.

1. **Myvm** bağlantısını kapatın.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, sanal ağ, sanal makine ve Cosmos DB hesabını aşağıdaki adımlarla silin:

1. Sol taraftaki menüden **kaynak grupları**' nı seçin.

2. **myResourceGroup** öğesini seçin.

3. **Kaynak grubunu sil**'i seçin.

4. **Kaynak grubu adını türüne** **myresourcegroup** girin.

5. **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunu oluşturdunuz:

* Sanal ağ ve savunma Konağı.
* Sanal makine.
* Cosmos DB hesabı.

Özel bağlantı hizmeti oluşturmayı öğrenin:
> [!div class="nextstepaction"]
> [Özel Bağlantı hizmeti oluşturma](create-private-link-service-portal.md)
