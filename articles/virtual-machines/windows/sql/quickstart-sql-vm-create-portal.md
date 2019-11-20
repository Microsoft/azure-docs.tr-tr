---
title: Portalda SQL Server Windows Sanal Makinesi oluşturma | Microsoft Docs
description: Bu öğreticide Azure portalında Windows SQL Server 2017 sanal makinesi oluşturma adımları gösterilmiştir.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bbf127f105f0ef22a23f00541396c2ac66b0e5f9
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828357"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Hızlı başlangıç: Azure portalında SQL Server 2017 Windows sanal makinesi oluşturma

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Bu hızlı başlangıç, Azure Portal’da SQL Server sanal makinesi oluşturma adımlarında yol gösterir.


  > [!TIP]
  > - Bu hızlı başlangıç, hızlı bir şekilde bir SQL VM sağlama ve VM’ye bağlanma yolu sağlar. Diğer SQL VM sağlama seçimleri hakkında daha fazla bilgi için bkz. [Azure portalında Windows SQL Server VM'leri için sağlama kılavuzu](virtual-machines-windows-portal-sql-server-provision.md).
  > - SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](virtual-machines-windows-sql-server-iaas-faq.md) bölümüne bakın.

## <a id="subscription"></a> Azure aboneliği edinme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a id="select"></a> SQL Server VM görüntüsü seçme

1. Hesabınızı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.

1. Azure portal sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna *Azure SQL* yazın.
1. **+ Ekle** ' yı seçerek **SQL dağıtım seçeneğini seçin** sayfasını açın. **SQL sanal makineler** kutucuğunda **Ayrıntıları göster** ' i seçerek ek bilgileri görüntüleyebilirsiniz.
1. Açılan menüden **ücretsiz SQL Server Lisansı: Windows Server 2016 üzerinde SQL Server 2017 Developer '** ı seçin.

   ![Yeni arama penceresi](./media/quickstart-sql-vm-create-portal/select-sql-2017-vm-image.png)

1. **Oluştur**'u seçin.

   ![Yeni arama penceresi](./media/quickstart-sql-vm-create-portal/create-sql-2017-vm-image.png)

## <a id="configure"></a> Temel ayrıntıları sağlama

**Temel bilgiler** sekmesinde, aşağıdaki bilgileri sağlayın:

1. **Proje ayrıntıları** bölümünde Azure aboneliğinizi seçin ve yeni bir kaynak grubu oluşturmak Için **Yeni oluştur** ' u seçin. Ad için _Sqlvm-RG_ yazın.

   ![Abonelik](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. **Örnek ayrıntıları**:
    1. **Sanal makine adı**Için _sqlvm_ yazın. 
    1. **Bölgeniz**için bir konum seçin. 
    1. Bu hızlı başlangıç amacıyla **kullanılabilirlik seçeneklerini** _hiçbir altyapı yedekliliği olmadan_ayarlayın. Kullanılabilirlik seçenekleri hakkında daha fazla bilgi edinmek için bkz. [kullanılabilirlik](../../windows/availability.md). 
    1. **Görüntü** listesinde _Ücretsiz SQL Server Lisansı: Windows Server 2016 üzerinde SQL Server 2017 Developer_' ı seçin. 
    1. Sanal makine **boyutunun** **boyutunu değiştirmeyi** seçin ve **a2 temel** teklifini seçin. Beklenmedik ücretleri engellemek için, bu kaynaklarla işiniz bittiğinde kaynaklarınızı temizlediğinizden emin olun. 

   ![Örnek ayrıntıları](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. **Yönetici hesabı**altında _azureuser_ ve Password gibi bir Kullanıcı adı sağlayın. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.

   ![Yönetici hesabı](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. **Gelen bağlantı noktası kuralları**altında **Seçili bağlantı noktalarına izin ver** ' i seçin ve ardından açılır listeden **RDP (3389)** öğesini seçin. 

   ![Gelen bağlantı noktası kuralları](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>SQL Server ayarları

**SQL Server ayarları** sekmesinde aşağıdaki seçenekleri yapılandırın:

1. **Güvenlik & ağı**altında, **SQL bağlantısı** için _Genel (Internet_) seçeneğini belirleyin ve genel senaryoda iyi bilinen bir bağlantı noktası numarası kullanmaktan kaçınmak için bağlantı noktasını `1401` olarak değiştirin. 
1. **SQL kimlik doğrulaması**altında **Etkinleştir**' i seçin. SQL Oturum Açma, sanal makine için yapılandırdığınız kullanıcı adı ve parolanın aynısına ayarlanır. [**Azure Key Vault tümleştirme**](virtual-machines-windows-ps-sql-keyvault.md)için varsayılan ayarı kullanın. **Depolama yapılandırması** temel SQL Server VM görüntüsü için kullanılamaz, ancak [depolama yapılandırmasındaki](virtual-machines-windows-sql-server-storage-configuration.md#new-vms)diğer görüntüler için kullanılabilir seçenekler hakkında daha fazla bilgi edinebilirsiniz.  

   ![SQL Server güvenlik ayarları](media/quickstart-sql-vm-create-portal/sql-server-settings.png)


1. Gerekirse diğer ayarları değiştirin ve ardından **gözden geçir + oluştur**' u seçin. 

   ![Gözden geçir + oluştur](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>SQL Server VM’sini oluşturma

**Gözden geçir + oluştur** sekmesinde, Özeti gözden geçirin ve **Oluştur** ' u seçerek bu VM için belirtilen SQL Server, kaynak grubu ve kaynakları oluşturun.

Azure portalından dağıtımı izleyebilirsiniz. Ekranın üst kısmındaki **Bildirimler** düğmesi dağıtımın temel durumunu gösterir. Dağıtım birkaç dakika sürebilir. 

## <a name="connect-to-sql-server"></a>SQL Server'a bağlanma

1. Portalda, sanal makinenizin özelliklerinin **genel bakış** bölümünde SQL Server VM **genel IP adresini** bulun.

1. Internet 'e bağlı farklı bir bilgisayarda [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)öğesini açın.


1. **Sunucuya Bağlan** veya **Veritabanı Altyapısına Bağlan** iletişim kutusunda **Sunucu adı** değerini düzenleyin. Sanal makinenizin genel IP adresini girin. Sonra bir virgül koyun ve yeni sanal makineyi yapılandırırken belirttiğimiz özel bağlantı noktasını (**1401**) ekleyin. Örneğin, `11.22.33.444,1401`.

1. **Kimlik Doğrulaması** kutusunda **SQL Server Kimlik Doğrulaması**’nı seçin.

1. **Oturum Aç** kutusuna geçerli bir SQL oturum açma adı yazın.

1. **Parola** kutusuna oturum açma parolasını yazın.

1. **Bağlan**’ı seçin.

    ![ssms bağlanma](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a id="remotedesktop"></a> VM’de uzaktan oturum açma

Uzak Masaüstü kullanarak SQL Server sanal makinesine bağlanmak için aşağıdaki adımları kullanın:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

SQL Server sanal makineye bağlandıktan sonra, SQL Server Management Studio'yu başlatabilir ve yerel yönetici kimlik bilgilerinizi kullanarak Windows Kimlik Doğrulamasına bağlanabilirsiniz. SQL Server Kimlik Doğrulamasını etkinleştirdiyseniz, sağlama işlemi sırasında yapılandırdığınız SQL oturum açma adı ve parolasını kullanarak da SQL Kimlik Doğrulamasına bağlanabilirsiniz.

Makineye erişim, gereksinimlerinize göre makineyi ve SQL Server ayarlarını doğrudan değiştirmenize olanak tanır. Örneğin, güvenlik duvarı ayarlarını yapılandırabilir veya SQL Server yapılandırma ayarlarını değiştirebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

SQL VM’nizin sürekli çalıştırılması gerekmiyorsa, kullanımda olmadığında durdurarak gereksiz ödeme yapmaktan kaçının. Ayrıca, portalda ilişkili kaynak grubunu silerek, sanal makineyle ilişkilendirilmiş tüm kaynakları kalıcı olarak silebilirsiniz. Bu işlem sanal makineyi de kalıcı olarak sildiğinden, bu komutu dikkatli kullanın. Daha fazla bilgi için bkz. [Azure kaynaklarınızı portal üzerinden yönetme](../../../azure-resource-manager/manage-resource-groups-portal.md).


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure portal SQL Server 2017 sanal makinesini oluşturdunuz. Verilerinizi yeni SQL Server'a geçirme hakkında daha fazla bilgi edinmek için, aşağıdaki makaleye bakın.

> [!div class="nextstepaction"]
> [Veritabanını SQL VM'ye geçirme](virtual-machines-windows-migrate-sql.md)
