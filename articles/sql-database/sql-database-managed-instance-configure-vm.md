---
title: İstemci VM-yönetilen örneği bağlama
description: Azure sanal makinesinden SQL Server Management Studio kullanarak Azure SQL veritabanı yönetilen örneğine bağlanın.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: 7273c7b1dbf5eb6c855b95a8661f38bd4bd14af7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839039"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Hızlı başlangıç: Azure SQL veritabanı yönetilen örneğine bağlanmak için Azure VM 'yi yapılandırma

Bu hızlı başlangıçta, Azure sanal makinesini SQL Server Management Studio (SSMS) kullanarak Azure SQL veritabanı yönetilen örneği 'ne bağlanacak şekilde nasıl yapılandırabileceğiniz gösterilmektedir. Noktadan siteye bağlantı kullanarak şirket içi istemci bilgisayarından bağlanmayı gösteren hızlı başlangıç için bkz. [Noktadan siteye bağlantı yapılandırma](sql-database-managed-instance-configure-p2s.md)

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıçta, [yönetilen bir örnek oluşturmak](sql-database-managed-instance-get-started.md) için başlangıç noktası olarak oluşturulan kaynaklar kullanılmaktadır.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Yönetilen örnek VNet 'te yeni bir alt ağ oluşturma

Aşağıdaki adımlar yönetilen örnek VNet 'inde bir Azure sanal makinesinin yönetilen örneğe bağlanabilmesi için yeni bir alt ağ oluşturur. Yönetilen örnek alt ağı, yönetilen örneklere ayrılmıştır. Bu alt ağda Azure sanal makineleri gibi başka herhangi bir kaynak oluşturamazsınız.

1. [Yönetilen örnek oluşturma](sql-database-managed-instance-get-started.md) hızlı başlangıç bölümünde oluşturduğunuz yönetilen örnek için kaynak grubunu açın. Yönetilen örneğiniz için sanal ağı seçin.

   ![Yönetilen Örnek kaynakları](./media/sql-database-managed-instance-configure-vm/resources.png)

2. **Alt ağları** seçin ve ardından **+ alt ağ** ' ı seçerek yeni bir alt ağ oluşturun.

   ![Yönetilen örnek alt ağları](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Bu tablodaki bilgileri kullanarak formu doldurun:

   | Ayar| Önerilen değer | Açıklama |
   | ---------------- | ----------------- | ----------- |
   | **Ad** | Geçerli bir ad|Geçerli adlar için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming).|
   | **Adres aralığı (CIDR bloğu)** | Geçerli bir Aralık | Bu hızlı başlangıç için varsayılan değer iyidir.|
   | **Ağ güvenlik grubu** | None | Bu hızlı başlangıç için varsayılan değer iyidir.|
   | **Yol tablosu** | None | Bu hızlı başlangıç için varsayılan değer iyidir.|
   | **Hizmet uç noktaları** | 0 seçili | Bu hızlı başlangıç için varsayılan değer iyidir.|
   | **Alt ağ temsili** | None | Bu hızlı başlangıç için varsayılan değer iyidir.|

   ![İstemci VM için yeni yönetilen örnek alt ağı](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Bu ek alt ağı yönetilen örnek VNet 'te oluşturmak için **Tamam ' ı** seçin.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Sanal ağdaki yeni alt ağ içinde sanal makine oluşturma

Aşağıdaki adımlarda, yönetilen örneğe bağlanmak için yeni alt ağda bir sanal makine oluşturma adımları gösterilmektedir.

## <a name="prepare-the-azure-virtual-machine"></a>Azure sanal makinesini hazırlama

SQL yönetilen örneği özel sanal ağınıza yerleştirildiğinden, SQL Server Management Studio veya Azure Data Studio gibi yüklü bir SQL istemci aracı ile Azure VM oluşturmanız gerekir. Bu araç, yönetilen örneğe bağlanmanıza ve sorguları yürütmenize imkan tanır. Bu hızlı başlangıç SQL Server Management Studio kullanır.

Tüm gerekli araçlarla istemci sanal makinesi oluşturmanın en kolay yolu Azure Resource Manager şablonlarını kullanmaktır.

1. Azure portal başka bir tarayıcı sekmesinde oturum açtığınızdan emin olun. Ardından, bir istemci sanal makinesi oluşturmak ve SQL Server Management Studio yüklemek için aşağıdaki düğmeyi seçin:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Aşağıdaki tabloda bulunan bilgileri kullanarak formu doldurun:

   | Ayar| Önerilen değer | Açıklama |
   | ---------------- | ----------------- | ----------- |
   | **Abonelik** | Geçerli bir abonelik | Yeni kaynaklar oluşturmak için izninizin olduğu bir abonelik olmalıdır. |
   | **Kaynak Grubu** |[Yönetilen örnek oluştur](sql-database-managed-instance-get-started.md) hızlı başlangıçta belirttiğiniz kaynak grubu.|Bu kaynak grubu, VNet 'in bulunduğu bir kaynak olmalıdır.|
   | **Konum** | Kaynak grubunun konumu | Bu değer, seçili kaynak grubuna göre doldurulur. |
   | **Sanal makine adı**  | Geçerli bir ad | Geçerli adlar için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming).|
   |**Yönetici Kullanıcı adı**|Geçerli bir Kullanıcı adı|Geçerli adlar için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). Ayrılmış sunucu düzeyi rol olan gibi "serveradmin" kullanmayın.<br>Bu Kullanıcı adını [, sanal makineye bağlandığınız](#connect-to-virtual-machine)her seferinde kullanırsınız.|
   |**Parola**|Geçerli bir parola|Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.<br>Bu parolayı [, sanal makineye bağlandığınız](#connect-to-virtual-machine)her seferinde kullanırsınız.|
   | **Sanal makine boyutu** | Geçerli boyut | Bu **Standard_B2s** şablonundaki varsayılan değer bu hızlı başlangıç için yeterlidir. |
   | **Konum**|[resourceGroup (). Location].| Bu değeri değiştirmeyin. |
   | **Sanal ağ adı**|Yönetilen örneği oluşturduğunuz sanal ağ.|
   | **Alt ağ adı**|Önceki yordamda oluşturduğunuz alt ağın adı| Yönetilen örneği oluşturduğunuz alt ağı seçmeyin.|
   | **yapıt konumu** | [Deployment (). Properties. templateLink. Uri] | Bu değeri değiştirmeyin. |
   | **yapıt konumu SAS belirteci** | boş bırakın | Bu değeri değiştirmeyin. |

   ![İstemci sanal makinesi oluşturma](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   [Yönetilen örneğinizi oluştururken](sql-database-managed-instance-get-started.md)önerilen VNET adını ve varsayılan alt ağı kullandıysanız, son iki parametreyi değiştirmeniz gerekmez. Aksi takdirde, bu değerleri ağ ortamını ayarlarken girdiğiniz değerlerle değiştirmeniz gerekir.

3. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum** onay kutusunu seçin.
4. Azure VM 'yi ağınızda dağıtmak için **satın al** ' ı seçin.
5. Dağıtım durumunu görüntülemek için **Bildirimler** simgesini seçin.

> [!IMPORTANT]
> Oluşturma sonrası betiklerin SQL Server Management Studio yüklemesi için zaman kazandırmak üzere sanal makine oluşturulduktan yaklaşık 15 dakika sonrasına devam etmez.

## <a name="connect-to-virtual-machine"></a>Sanal makineye bağlanma

Aşağıdaki adımlarda, uzak masaüstü bağlantısı kullanarak yeni oluşturduğunuz sanal makineye bağlanma işlemi gösterilmektedir.

1. Dağıtım tamamlandıktan sonra sanal makine kaynağına gidin.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. **Bağlan**’ı seçin.

   Bir Uzak Masaüstü Protokolü dosya (. rdp dosyası) formu, sanal makinenin genel IP adresi ve bağlantı noktası numarasıyla birlikte görüntülenir.

   ![RDP formu](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. **RDP dosyasını indir**' i seçin.

   > [!NOTE]
   > Sanal makinenize bağlanmak için SSH de kullanabilirsiniz.

4. **Sanal makine bağlantısı** formunu kapatın.
5. VM'nize bağlanmak için indirilen RDP dosyasını açın.
6. İstendiğinde, **Bağlan**' ı seçin. Mac bilgisayarlarda, Mac App Store’dan bu [Uzak Masaüstü İstemcisi](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) gibi bir RDP istemcisi indirmeniz gerekir.

7. Sanal makineyi oluştururken belirttiğiniz kullanıcı adını ve parolayı girip **Tamam**' ı seçin.

8. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bağlantıya devam etmek için **Evet** ' i veya **devam et** ' i seçin.

Sunucu Yöneticisi panosunda sanal makinenize bağlanırsınız.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Yönetilen örneğe bağlanmak için SSMS kullanma

1. Sanal makinede SQL Server Management Studio (SSMS) öğesini açın.

   Bu, her SSMS ilk kez başlatıldığından bu yana yapılandırmayı tamamlaması gerektiğinden, açılması birkaç dakika sürer.
2. **Sunucuya Bağlan** iletişim kutusunda, **sunucu adı** kutusuna yönetilen örneğiniz için tam **ana bilgisayar adını** girin. **SQL Server kimlik doğrulaması**' nı seçin, Kullanıcı adınızı ve parolanızı girin ve ardından **Bağlan**' ı seçin.

    ![ssms bağlanma](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Bağlandıktan sonra Veritabanları düğümündeki sistem ve kullanıcı veritabanlarınızı ve Güvenlik, Sunucu Nesneleri, Çoğaltma, Yönetim, SQL Server Agent ile XEvent Profil Oluşturucu düğümlerindeki çeşitli nesneleri görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Noktadan siteye bağlantı kullanarak şirket içi istemci bilgisayarından bağlanmayı gösteren hızlı başlangıç için bkz. [Noktadan siteye bağlantı yapılandırma](sql-database-managed-instance-configure-p2s.md).
- Uygulamaların bağlantı seçeneklerine genel bir bakış için bkz: [Uygulamalarınızı Yönetilen Örneğe bağlama](sql-database-managed-instance-connect-app.md).
- Mevcut bir SQL Server veritabanını Şirket içinden yönetilen bir örneğe geri yüklemek için, [geçiş Için Azure veritabanı geçiş hizmeti 'ni (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) veya bir veritabanı yedekleme dosyasından geri yüklemek için [T-SQL restore komutunu](sql-database-managed-instance-get-started-restore.md) kullanabilirsiniz.
