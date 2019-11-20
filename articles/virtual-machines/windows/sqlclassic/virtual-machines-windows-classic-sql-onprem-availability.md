---
title: Şirket içi Always on kullanılabilirlik gruplarını Azure 'a genişletin | Microsoft Docs
description: Bu öğretici, klasik dağıtım modeliyle oluşturulan kaynakları kullanır ve Azure 'da her zaman açık kullanılabilirlik grubu çoğaltması eklemek için SQL Server Management Studio (SSMS) içinde çoğaltma ekleme Sihirbazı 'nın nasıl kullanılacağını açıklar.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 48848fbacdc0e205604bb163aa36bdafcd175b0b
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173535"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Şirket içi Always on kullanılabilirlik gruplarını Azure 'a genişletme
Her zaman açık kullanılabilirlik grupları, ikincil çoğaltmalar ekleyerek veritabanı grupları için yüksek kullanılabilirlik sağlar. Bu çoğaltmalar hata durumunda veritabanlarının yük devretmesini sağlar. Bunlara ek olarak, okuma iş yüklerini veya yedekleme görevlerini boşaltmak için de kullanılabilir.

SQL Server sahip bir veya daha fazla Azure sanal makinesi sağlayarak ve bunları şirket içi kullanılabilirlik gruplarınızı çoğaltmalar olarak ekleyerek, şirket içi kullanılabilirlik gruplarını Microsoft Azure için genişletebilirsiniz.

Bu öğreticide size şunlar varsayılmaktadır:

* Etkin bir Azure aboneliği. [Ücretsiz deneme için kaydolabilirsiniz](https://azure.microsoft.com/pricing/free-trial/).
* Şirket içinde mevcut bir Always on kullanılabilirlik grubu. Kullanılabilirlik grupları hakkında daha fazla bilgi için bkz. [Always on kullanılabilirlik grupları](https://msdn.microsoft.com/library/hh510230.aspx).
* Şirket içi ağ ve Azure sanal ağınız arasındaki bağlantı. Bu sanal ağı oluşturma hakkında daha fazla bilgi için bkz. [Azure Portal (klasik) kullanarak siteden siteye bağlantı oluşturma](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede, klasik dağıtım modelinin kullanımı ele alınmaktadır. Microsoft, en yeni dağıtımların Kaynak Yöneticisi modelini kullanmasını önerir.

## <a name="add-azure-replica-wizard"></a>Azure çoğaltma Sihirbazı ekleme
Bu bölümde, Azure **çoğaltması Ekleme Sihirbazı 'nı** kullanarak her zaman açık kullanılabilirlik grubu çözümünüzü Azure çoğaltmaları içerecek şekilde nasıl genişletebileceğinizi gösterir.

> [!IMPORTANT]
> **Azure çoğaltma ekleme Sihirbazı** yalnızca klasik dağıtım modeliyle oluşturulan sanal makineleri destekler. Yeni VM dağıtımları, daha yeni Kaynak Yöneticisi modelini kullanmalıdır. Kaynak Yöneticisi sahip VM 'Leri kullanıyorsanız, Transact-SQL komutlarını kullanarak ikincil Azure çoğaltmasını el ile eklemeniz gerekir (burada gösterilmez). Bu sihirbaz Kaynak Yöneticisi senaryosunda çalışmayacaktır.

1. SQL Server Management Studio içinde, **her zaman yüksek kullanılabilirlik** > **kullanılabilirlik grupları**' nı genişletin  >  **[kullanılabilirlik grubunuzun adı]** .
2. **Kullanılabilirlik çoğaltmaları**' na sağ tıklayın ve ardından **çoğaltma ekle**' ye tıklayın.
3. Varsayılan olarak, **kullanılabilirlik grubuna çoğaltma Ekle Sihirbazı** görüntülenir. **İleri**'ye tıklayın.  Bu sihirbazın önceki bir kez başlatılması sırasında sayfanın altındaki **Bu sayfayı bir daha gösterme** seçeneğini belirlediyseniz bu ekran görüntülenmez.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Var olan tüm ikincil çoğaltmalara bağlanmanız gerekecektir. Bağlan ' a tıklayabilirsiniz **...** Her bir kopyanın yanında ya da **Tümünü bağla...** seçeneğine tıklayabilirsiniz. ekranın alt kısmında. Kimlik doğrulamasından sonra İleri **' ye tıklayarak sonraki ekrana** ilerleyin.
5. **Çoğaltmaları belirtin** sayfasında, en üstteki: **çoğaltmalar**, **uç noktalar**, **Yedekleme tercihleri**ve **dinleyici**arasında birden çok sekme listelenir. **Çoğaltmalar** sekmesinden **Azure çoğaltma Ekle ' ye tıklayın...** Azure çoğaltma ekleme Sihirbazı 'Nı başlatmak için.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Daha önce yüklediyseniz, yerel Windows sertifika deposundan var olan bir Azure Yönetim sertifikası seçin. Daha önce kullandıysanız bir Azure aboneliğinin kimliğini seçin veya girin. Bir Azure Yönetim sertifikası indirip yüklemek ve Azure hesabı kullanarak Aboneliklerin listesini indirmek için Indir ' e tıklayabilirsiniz.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Sayfadaki her alanı, çoğaltmayı barındıracak Azure sanal makinesini (VM) oluşturmak için kullanılacak değerlerle dolduracaksınız.
   
   | Ayar | Description |
   | --- | --- |
   | **Görüntüyle** |İstenen işletim sistemi ve SQL Server birleşimini seçin |
   | **VM boyutu** |İş gereksinimlerinize en uygun VM 'nin boyutunu seçin |
   | **VM adı** |Yeni VM için benzersiz bir ad belirtin. Ad 3 ila 15 karakter arasında olmalıdır, yalnızca harf, rakam ve kısa çizgi içerebilir ve bir harfle başlamalı ve bir harf ya da sayı ile bitmelidir. |
   | **VM Kullanıcı adı** |VM 'de yönetici hesabı olacak bir Kullanıcı adı belirtin |
   | **VM yönetici parolası** |Yeni hesap için bir parola belirtin |
   | **Parolayı Onayla** |Yeni hesabın parolasını onaylayın |
   | **Sanal ağ** |Yeni VM 'nin kullanması gereken Azure sanal ağını belirtin. Sanal ağlar hakkında daha fazla bilgi için bkz. [sanal ağa genel bakış](../../../virtual-network/virtual-networks-overview.md). |
   | **Sanal ağ alt ağı** |Yeni VM 'nin kullanması gereken sanal ağ alt ağını belirtin |
   | **Etki alanı** |Etki alanı için önceden doldurulan değerin doğru olduğunu onaylayın |
   | **Etki alanı Kullanıcı adı** |Yerel küme düğümlerinde yerel Yöneticiler grubunda bir hesap belirtin |
   | **Parola** |Etki alanı Kullanıcı adı için parola belirtin |
8. Dağıtım ayarlarını doğrulamak için **Tamam** ' ı tıklatın.
9. Geçerli terimler ileri görüntülenir. Bu koşulları kabul ediyorsanız Tamam ' ı okuyun ve **Tamam** ' ı tıklatın.
10. **Çoğaltmaları belirtin** sayfası yeniden görüntülenir. **Çoğaltmalar**, **uç noktalar**ve **Yedekleme tercihleri** sekmelerinde yeni Azure çoğaltmasının ayarlarını doğrulayın. Ayarları, iş gereksinimlerinizi karşılayacak şekilde değiştirin.  Bu sekmelerde bulunan parametreler hakkında daha fazla bilgi için bkz. [çoğaltmaları belirtme sayfası (yeni kullanılabilirlik Grubu Sihirbazı/çoğaltma ekleme Sihirbazı)](https://msdn.microsoft.com/library/hh213088.aspx). Azure çoğaltmaları içeren kullanılabilirlik grupları için dinleyici sekmesi kullanılarak dinleyicileri oluşturutamayacağını unutmayın. Ayrıca, sihirbazı başlatmadan önce bir dinleyici zaten oluşturulduysa, Azure 'da desteklenmediğini belirten bir ileti alırsınız. **Kullanılabilirlik grubu dinleyicisi oluşturma** bölümünde dinleyicileri oluşturma bölümüne bakacağız.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. **İleri**'ye tıklayın.
12. **Ilk veri eşitlemesini Seç** sayfasında kullanmak istediğiniz veri eşitleme yöntemini seçin ve **İleri**' ye tıklayın. Çoğu senaryo için **tam veri eşitleme**' yi seçin. Veri eşitleme yöntemleri hakkında daha fazla bilgi için bkz. [Ilk veri eşitlemesini seçme sayfası (Always on kullanılabilirlik grubu sihirbazları)](https://msdn.microsoft.com/library/hh231021.aspx).
13. **Doğrulama** sayfasında sonuçları gözden geçirin. Bekleyen sorunları düzeltin ve gerekirse doğrulamayı yeniden çalıştırın. **İleri**'ye tıklayın.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. **Özet** sayfasında ayarları gözden geçirin ve ardından **son**' a tıklayın.
15. Sağlama işlemi başlar. Sihirbaz başarıyla tamamlandığında, sihirbazdan çıkmak için **Kapat** ' a tıklayın.

> [!NOTE]
> Azure çoğaltma ekleme Sihirbazı, Users\User \ Appdata\local\sql Server\addreplicawizardyolunda bir günlük dosyası oluşturur. Bu günlük dosyası, başarısız olan Azure çoğaltma dağıtımlarının sorunlarını gidermek için kullanılabilir. Sihirbaz herhangi bir eylemi yürütürken başarısız olursa, sağlanan VM 'yi silme dahil olmak üzere önceki tüm işlemler geri alınır.
> 
> 

## <a name="create-an-availability-group-listener"></a>Kullanılabilirlik grubu dinleyicisi oluşturma
Kullanılabilirlik grubu oluşturulduktan sonra, istemcilerin çoğaltmalara bağlanması için bir dinleyici oluşturmalısınız. Dinleyiciler, birincil veya salt okunurdur ikincil çoğaltmaya doğrudan gelen bağlantılar sağlar. Dinleyiciler hakkında daha fazla bilgi için bkz. [Azure 'Da Always on kullanılabilirlik grupları için BIR ıLB dinleyicisi yapılandırma](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Sonraki adımlar
Her zaman açık kullanılabilirlik grubunuzu Azure 'a genişletmek için **Azure çoğaltma ekleme Sihirbazı 'nı** kullanmanın yanı sıra, bazı SQL Server Iş yüklerini Azure 'a tamamen de taşıyabilirsiniz. Başlamak için bkz. [Azure 'da SQL Server sanal makinesi sağlama](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Azure VM 'lerinde SQL Server çalıştırmaya ilişkin diğer konular için bkz. [Azure sanal makinelerinde SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

