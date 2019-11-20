---
title: Microsoft Azure StorSimple Veri Yöneticisi Kullanıcı arabirimi | Microsoft Docs
description: StorSimple Veri Yöneticisi hizmeti kullanıcı arabirimini kullanmayı açıklar
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 3a87b4c07d2e961d9876fb44c6584f37ad9246df
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796655"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>StorSimple Veri Yöneticisi hizmetini Azure portal yönetme

Bu makalede, StorSimple 8000 serisi cihazlarda bulunan verileri dönüştürmek için StorSimple Veri Yöneticisi Kullanıcı arabirimini nasıl kullanabileceğiniz açıklanmaktadır. Dönüştürülen veriler daha sonra Azure Media Services, Azure HDInsight, Azure Machine Learning ve Azure Bilişsel Arama gibi diğer Azure hizmetleri tarafından tüketilebilir.


## <a name="use-storsimple-data-transformation"></a>StorSimple veri dönüşümünü kullanın

StorSimple Veri Yöneticisi, veri dönüşümünün örneklendiği kaynaktır. Veri dönüştürme hizmeti, StorSimple biçimindeki verileri bloblarda veya Azure dosyalarında yerel biçime dönüştürmenizi sağlar. StorSimple yerel biçim verilerini dönüştürmek için, StorSimple 8000 serisi cihazlarınızın ayrıntılarını ve dönüştürmek istediğiniz ilgilendiğiniz verileri belirtmeniz gerekir.

### <a name="create-a-storsimple-data-manager-service"></a>StorSimple Veri Yöneticisi hizmeti oluşturma

StorSimple Veri Yöneticisi bir hizmet oluşturmak için aşağıdaki adımları gerçekleştirin.

1. [Azure portalda](https://portal.azure.com/) oturum açmak için Microsoft hesabınızın kimlik bilgilerini kullanın.

2. **+ Kaynak oluştur ' a** tıklayın ve StorSimple veri Yöneticisi arayın.

    ![StorSimple Veri Yöneticisi hizmeti oluşturma 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. StorSimple Veri Yöneticisi ' a ve ardından **Oluştur**' a tıklayın.
    
    ![StorSimple Veri Yöneticisi hizmeti oluşturma 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Yeni hizmet için aşağıdakileri belirtin:

   1. StorSimple Veri Yöneticisi için benzersiz bir **hizmet adı** sağlayın. Hizmetinizi tanımlayabilmek için kullanılan kolay bir addır. Ad harf, rakam ve tirelerden oluşan 3-24 karakter arası uzunlukta olabilir. Ad bir harf veya sayıyla başlamalı ve bitmelidir.

   2. Açılan listeden bir **abonelik** seçin. Abonelik fatura hesabınıza bağlıdır. Yalnızca bir aboneliğiniz varsa, bu alan otomatik olarak doldurulur (ve seçilemez).

   3. Var olan bir kaynak grubunu seçin veya yeni bir grup oluşturun. Daha fazla bilgi edinmek için bkz. [Azure kaynak grupları](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

   4. Hizmetinizin depolama hesaplarınızın ve StorSimple Veri Yöneticisi hizmetinizin barındırıldığı **konumu** belirtin. StorSimple Aygıt Yöneticisi hizmetiniz, Veri Yöneticisi hizmetiniz ve ilişkili depolama hesabı, tümünün desteklenen bölgelerde olması gerekir.
    
   5. Panonuzda bu hizmetin bağlantısını almak için **panoya sabitle**' yi seçin.
    
   6. **Oluştur**'a tıklayın.

      ![StorSimple Veri Yöneticisi hizmeti oluşturma 3](./media/storsimple-data-manager-ui/create-service-4.png)

Hizmetin oluşturulması birkaç dakika sürer. Hizmet başarıyla oluşturulduktan ve yeni hizmet görüntülendikten sonra bir bildirim görürsünüz.

### <a name="create-a-data-transformation-job-definition"></a>Veri dönüştürme işi tanımı oluşturma

Bir StorSimple Veri Yöneticisi hizmeti içinde, bir veri dönüştürme işi tanımı oluşturmanız gerekir. Bir iş tanımı, yerel biçimdeki bir depolama hesabına geçmeyi ilgilendiğiniz StorSimple verilerinin ayrıntılarını belirtir. Bir iş tanımı oluşturduktan sonra, bu işi farklı çalışma zamanı ayarlarıyla yeniden çalıştırabilirsiniz.

Bir iş tanımı oluşturmak için aşağıdaki adımları gerçekleştirin.

1. Oluşturduğunuz hizmete gidin. **Yönetim > iş tanımları**' na gidin.

2. **+ İş tanımı**' na tıklayın.

    ![\+ Iş tanımı ' na tıklayın](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. İş tanımınız için bir ad girin. Ad 3 ila 63 karakter arasında olabilir. Ad, büyük harf, küçük harf, sayı ve kısa çizgi içerebilir.

4. İşinizin çalıştığı bir konum belirtin. Bu konum, Hizmetin dağıtıldığı konumdan farklı olabilir.

5. Kaynak veri deposunu belirtmek için **kaynak** ' a tıklayın.

    ![Kaynak veri deposundan yapılandırma](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Bu yeni bir Veri Yöneticisi hizmeti olduğundan, veri depoları yapılandırılmadı. **Veri kaynağını Yapılandır**bölümünde, storsimple 8000 serisi cihazınızın ayrıntılarını ve ilgilendiğiniz verileri belirtin.

   StorSimple Aygıt Yöneticisi veri deposu olarak eklemek için, veri deposu açılan menüsünde **Yeni Ekle** ' ye tıklayın ve sonra **veri deposu Ekle**' ye tıklayın.

    ![Yeni veri deposu Ekle](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Veri deposu türü olarak **StorSimple 8000 Series Manager** ' ı seçin.
    
   2. Kaynak veri havuzunuz için kolay bir ad girin.
    
   3. Açılan listeden, StorSimple Aygıt Yöneticisi hizmetinize ilişkin bir abonelik seçin.
    
   4. **Kaynak**Için storsimple Aygıt Yöneticisi adını sağlayın.

   5. StorSimple Aygıt Yöneticisi hizmeti için **hizmet veri şifreleme** anahtarını girin. 

      ![Kaynak veri deposu yapılandırma 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Bittiğinde **Tamam** ' a tıklayın. Bu, veri deponuzu kaydeder. Bu StorSimple Aygıt Yöneticisi, bu parametreleri tekrar girmeden diğer iş tanımlarında yeniden kullanın. Yeni oluşturulan kaynak veri deposunun açılan menüde görünmesi için **Tamam** ' a tıkladıktan sonra birkaç saniye sürer.

7. **Veri deposunun**açılan listesinden, oluşturduğunuz veri deposunu seçin. 

   1. İlgilendiğiniz verileri içeren StorSimple 8000 serisi cihazının adını girin.

   2. İlgilendiğiniz verilerinizi içeren StorSimple cihazında bulunan birimin adını belirtin.

   3. **Filtre** alt bölümünde, _\Myrootdirectory\data_ biçiminde ilgilendiğiniz verilerinizi içeren kök dizini girin. _\C: \Data_ gibi sürücü harfleri desteklenmez. Ayrıca, buraya dosya filtresi ekleyebilirsiniz.

   4. Veri dönüştürme hizmeti, anlık görüntüler aracılığıyla Azure 'a gönderilen verilerde çalışır. Bu işi çalıştırdığınızda, bu iş her çalıştırıldığında (en son veriler üzerinde çalışmak üzere) bir yedekleme işlemi gerçekleştirebilir veya buluttaki son mevcut yedeklemeyi (bazı arşivlenmiş veriler üzerinde çalışıyorsanız) kullanabilirsiniz.

   5. **Tamam** düğmesine tıklayın.

      ![Kaynak veri deposu yapılandırma 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Sonra, hedef veri deposunun yapılandırılması gerekir. Dosyaları bu hesaptaki bloblara koymak için depolama hesapları ' nı seçin. Açılan menüde **Yeni Ekle** ' yi ve ardından **Ayarları Yapılandır**' ı seçin.

9. Eklemek istediğiniz hedef depo türünü ve depo ile ilişkili diğer parametreleri seçin.

    Bir depolama hesabı türü hedefi seçerseniz, kolay bir ad, abonelik (hizmet veya diğer ile aynı seçimi seçin) ve bir depolama hesabı belirtebilirsiniz.
        ![hedef veri deposu yapılandırma 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    İş çalıştırıldığında bir depolama kuyruğu oluşturulur. Bu kuyruk, hazır olduğunda dönüştürülen bloblar hakkında iletilerle doldurulur. Bu kuyruğun adı, iş tanımının adıyla aynıdır.
    
10. Veri deposunu ekledikten sonra birkaç dakika bekleyin.
    
    1. Hedef **Hesap adındaki**açılan listeden hedef olarak oluşturduğunuz depoyu seçin.

    2. Depolama türünü blob 'lar veya dosyalar olarak seçin. Dönüştürülen verilerin bulunduğu depolama kapsayıcısının adını belirtin. **Tamam** düğmesine tıklayın.

        ![Hedef veri deposu depolama hesabını yapılandırma](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. İşi çalıştırmadan önce iş süresi tahminini sunma seçeneğini de denetleyebilirsiniz. İş tanımını oluşturmak için **Tamam** ' ı tıklatın. İş tanımınız artık tamamlanmış. Bu iş tanımını farklı çalışma zamanı ayarlarına sahip kullanıcı arabirimi aracılığıyla birden çok kez kullanabilirsiniz.

    ![Tamamlanan iş tanımı](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Yeni oluşturulan iş tanımı, bu hizmet için iş tanımları listesine eklenir.

### <a name="run-the-job-definition"></a>İş tanımını çalıştırma

StorSimple 'tan iş tanımında belirttiğiniz depolama hesabına veri taşımanız gerektiğinde onu çalıştırmanız gerekir. Çalışma zamanında bazı parametreler farklı şekilde belirlenebilir. Adımlar aşağıdaki gibidir:

1. StorSimple Veri Yöneticisi hizmetinizi seçin ve **yönetim > iş tanımları**' na gidin. Çalıştırmak istediğiniz iş tanımını seçin ve tıklayın.
     
     ![İşi Başlat Çalıştır 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. **Şimdi Çalıştır**' a tıklayın.
     
     ![İşi Başlat Çalıştır 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Bu iş çalıştırmasında değiştirmek isteyebileceğiniz ayarları değiştirmek için **Ayarları Çalıştır** ' a tıklayın. **Tamam** ' a ve ardından **Çalıştır** ' a tıklayarak işinizi başlatın.

    ![İş çalıştırmasını Başlat 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Bu işi izlemek için StorSimple Veri Yöneticisi **işlere** gidin. **İşler** dikey penceresinde izlemeye ek olarak, bir dosya StorSimple 'tan depolama hesabına taşındığında bir iletinin eklendiği depolama kuyruğu üzerinde de dinleme yapabilirsiniz.

    ![İşi Başlat Çalıştır 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>Sonraki adımlar

[StorSimple veri Yöneticisi işleri başlatmak için .NET SDK 'Yı kullanın](storsimple-data-manager-dotnet-jobs.md).