---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 51dc04fbef8d09878f33d7fda6f15039d3afba3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027368"
---
### <a name="configure-a-dns-label-for-the-public-ip-address"></a>Genel IP adresi için DNS etiketi yapılandırma

SQL Server Veritabanı Altyapısına İnternet'ten bağlanmak için önce genel IP adresi için bir DNS etiketi yapılandırmayı düşünün. IP adresine göre de bağlanabilirsiniz, ancak DNS Etiketi tanımlanması daha kolay olan ve temeldeki genel IP adresini soyutlayan bir A Kaydı oluşturur.

> [!NOTE]
> SQL Server örneğine yalnızca aynı Sanal Ağ içinden veya yerel olarak bağlanmayı planlıyorsanız, DNS Etiketleri gerekli değildir.

DNS etiketi oluşturmak için önce portalda **Virtual Machines**’i seçin. Özelliklerini görüntülemek için SQL Server VM’yi seçin.

1. Sanal makineye genel bakış sayfasında **Genel IP adresi**’nizi seçin.

    ![genel ip adresi](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

1. Genel IP adresinizin özelliklerinde **Yapılandırma**’yı genişletin.

1. DNS etiket adı girin. Bu ad, SQL Server VM'nize bağlanmak için IP adresi yerine doğrudan kullanılabilen bir Kayıttır.

1. **Kaydet** düğmesine tıklayın.

    ![dns etiketi](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Başka bir bilgisayardan Veritabanı Altyapısına bağlanma

1. İnternet'e bağlı bir bilgisayarda SQL Server Management Studio’yu (SSMS) açın. SQL Server Management Studio’nuz yoksa [buradan](/sql/ssms/download-sql-server-management-studio-ssms) indirebilirsiniz.

1. **Sunucuya Bağlan** veya **Veritabanı Altyapısına Bağlan** iletişim kutusunda **Sunucu adı** değerini düzenleyin. Sanal makinenin IP adresini veya tam DNS adını girin (önceki görevde saptanmıştır). Ayrıca bir virgül ekleyebilir ya da SQL Server'ın TCP bağlantı noktasını sağlayabilirsiniz. Örneğin, `mysqlvmlabel.eastus.cloudapp.azure.com,1433`.

1. **Kimlik Doğrulaması** kutusunda **SQL Server Kimlik Doğrulaması**’nı seçin.

1. **Oturum Aç** kutusuna geçerli bir SQL oturum açma adı yazın.

1. **Parola** kutusuna oturum açma parolasını yazın.

1. **Bağlan**'a tıklayın.

    ![ssms bağlanma](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)