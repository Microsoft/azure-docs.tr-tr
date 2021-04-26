---
title: "Öğretici: Azure özel uç noktası kullanarak Azure SQL Server 'a bağlanma-Portal"
description: Azure portal kullanarak özel bir uç nokta ile Azure SQL Server oluşturmayı öğrenmek için bu öğreticiyi kullanın.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 8bb227bdc4bbcdf986c2ed64e1f89e1dfa289b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554981"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>Öğretici: Azure özel uç nokta kullanarak Azure SQL Server 'a bağlanma-Azure portal

Azure özel uç noktası, Azure 'da özel bağlantı için temel yapı taşdır. Sanal makineler (VM) gibi Azure kaynaklarının özel olarak özel bağlantı kaynaklarıyla iletişim kurmasını sağlar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir sanal ağ ve savunma ana bilgisayarı oluşturun.
> * Sanal makine oluşturur.
> * Azure SQL Server ve özel uç nokta oluşturun.
> * SQL Server özel uç noktası ile bağlantıyı test edin.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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
    | Kaynak Grubu   | **CreateSQLEndpointTutorial-RG** seçin |
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
    | Kaynak Grubu | **CreateSQLEndpointTutorial** seçin |
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

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Azure SQL Server ve özel uç nokta oluşturma

Bu bölümde, Azure 'da bir SQL Server oluşturacaksınız. 

1. Ekranın sol üst tarafında Azure Portal **kaynak**  >  **veritabanları** oluştur  >  **SQL veritabanı**' nı seçin.

1. **SQL veritabanı oluştur**' un **temel bilgiler** sekmesinde, girin veya bu bilgileri seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **CreateSQLEndpointTutorial** öğesini seçin. Bu kaynak grubunu önceki bölümde oluşturdunuz.|
    | **Veritabanı ayrıntıları** |  |
    | Veritabanı adı  | **Mysqldatabase** girin. Bu ad alındıysanız, benzersiz bir ad oluşturun. |
    | Sunucu | **Yeni oluştur**’u seçin. |

6. **Yeni sunucu**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sunucu adı  | **Te yapılandırılmış MySQLServer** girin. Bu ad alındıysanız, benzersiz bir ad oluşturun.|
    | Sunucu yöneticisi oturumu açma | Tercih etmek için bir yönetici adı girin. |
    | Parola | Seçtiğiniz bir parolayı girin. Parola en az 8 karakter uzunluğunda olmalı ve tanımlanan gereksinimleri karşılamalıdır. |
    | Konum | **Doğu ABD** seçin |
    
7. **Tamam**’ı seçin.

8. **Ağ** sekmesini seçin veya **Sonraki: ağ** düğmesini seçin.

9. **Ağ** sekmesinde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Ağ bağlantısı** | |
    | Bağlantı yöntemi | **Özel uç nokta**' ı seçin. |
   
10. **Özel uç noktalarda** **+ Özel uç nokta Ekle** ' yi seçin.

11. **Özel uç nokta oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **CreateSQLEndpointTutorial** öğesini seçin. |
    | Konum | **Doğu ABD**’yi seçin. |
    | Name | **MyPrivateSQLendpoint** girin. |
    | Hedef alt kaynak | **SqlServer**' ı seçin. |
    | **Ağ** |  |
    | Sanal ağ | **Myvnet**' i seçin. |
    | Alt ağ | **Mysubnet** öğesini seçin. |
    | **Özel DNS tümleştirme** | |
    | Özel DNS bölgesi ile tümleştirme | Varsayılan **Evet**' i bırakın. |
    | Özel DNS Bölgesi | Varsayılan **(yeni) Privatelink.Database.Windows.net** bırakın. |

12. **Tamam**’ı seçin. 

13. **Gözden geçir ve oluştur**’u seçin.

14. **Oluştur**’u seçin.

## <a name="test-connectivity-to-private-endpoint"></a>Özel uç nokta ile bağlantıyı sına

Bu bölümde, önceki adımda oluşturduğunuz sanal makineyi kullanarak özel uç nokta genelinde SQL Server 'a bağlanırsınız.

1. Sol taraftaki Gezinti bölmesinde **kaynak grupları** ' nı seçin.

2. **CreateSQLEndpointTutorial** öğesini seçin.

3. **Myvm**' yi seçin.

4. **Myvm** için genel bakış sayfasında **Bağlan** ' ı **seçin.**

5. Mavi **kullanımı** savunma düğmesini seçin.

6. Sanal makine oluşturma sırasında girdiğiniz kullanıcı adını ve parolayı girin.

7. Bağlandıktan sonra sunucuda Windows PowerShell 'i açın.

8. `nslookup <sqlserver-name>.database.windows.net` yazın. **\<sqlserver-name>** Önceki adımlarda oluşturduğunuz SQL Server 'ın adıyla değiştirin.  Aşağıda görüntülene benzer bir ileti alacaksınız:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    SQL Server adı için **10.1.0.5** özel IP adresi döndürülür.  Bu adres, daha önce oluşturduğunuz sanal ağın alt ağıdır.


9. **Myvm**'de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) 'yi yükler.

10. **SQL Server Management Studio** açın.

4. **Sunucuya Bağlan**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sunucu türü | **Veritabanı Altyapısı**’nı seçin.|
    | Sunucu adı | **\<sqlserver-name> . Database.Windows.net** girin |
    | Kimlik doğrulaması | **SQL Server Kimlik Doğrulaması**'nı seçin. |
    | Kullanıcı adı | Sunucu oluşturma sırasında girdiğiniz kullanıcı adını girin |
    | Parola | Sunucu oluşturma sırasında girdiğiniz parolayı girin |
    | Parolayı unutmayın | **Evet**’i seçin. |

1. **Bağlan**’ı seçin.
2. Sol menüden veritabanlarına gözatamazsınız.
3. I **Mysqldatabase**'dan bilgi oluşturun veya sorgulayın.
4. **Myvm** ile uzak masaüstü bağlantısını kapatın. 

## <a name="clean-up-resources"></a>Kaynakları temizleme 
Özel uç nokta, SQL Server ve VM 'yi kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları silin: 
1. Portalın üst kısmındaki **arama** kutusuna **CreateSQLEndpointTutorial** girin ve arama sonuçlarından **CreateSQLEndpointTutorial** öğesini seçin. 
2. **Kaynak grubunu sil**'i seçin. 
3. **Kaynak grubu adını yazmak** için CreateSQLEndpointTutorial girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunu oluşturdunuz:

* Sanal ağ ve savunma Konağı.
* Sanal makine.
* Özel uç nokta ile Azure SQL Server.

Sanal makineyi, Özel uç nokta genelinde SQL Server ile güvenli bir şekilde test etmek için kullandınız.

Bir sonraki adım olarak, Web uygulamasını, sanal ağın dışında bir Web uygulamasını bir veritabanının özel uç noktasına bağlayan **Azure SQL veritabanı mimarisi senaryosuna özel bağlantı ile** de ilgileniyor olabilirsiniz.
> [!div class="nextstepaction"]
> [Azure SQL veritabanı 'na özel bağlantı içeren Web uygulaması](/azure/architecture/example-scenario/private-web-app/private-web-app)