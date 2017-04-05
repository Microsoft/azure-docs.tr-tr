---
title: "İlk Bakış: Azure sanal makinelerini kurtarma hizmetleri kasasıyla koruma | Microsoft Docs"
description: "Sanal makineleri bir kurtarma hizmetleri kasasıyla koruyun. Verilerinizi korumak için, Resource Manager tarafından dağıtılan, Klasik modunda dağıtılan VM&quot;lerin, Premium Depolama VM&quot;lerinin, Şifrelenmiş VM’lerin ve Yönetilen Diskler üzerindeki VM’lerin yedeklemelerini kullanın. Bir kurtarma hizmetleri kasası oluşturun ve kaydedin. Azure&quot;da VM&quot;leri kaydedin, ilke oluşturun ve VM&quot;leri koruyun."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keyword: backups; vm backup
ms.assetid: 45e773d6-c91f-4501-8876-ae57db517cd1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/15/2017
ms.author: markgal;jimpark
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d39678bb7e7d2263b6b2f8d36da1bd86dcfff271
ms.lasthandoff: 04/03/2017


---
# <a name="back-up-azure-virtual-machines-to-recovery-services-vaults"></a>Azure sanal makinelerini Kurtarma Hizmetleri kasalarına yedekleme
> [!div class="op_single_selector"]
> * [VM'leri bir kurtarma hizmetleri kasasıyla koruma](backup-azure-vms-first-look-arm.md)
> * [VM'leri yedekleme kasasıyla koruma](backup-azure-vms-first-look.md)
>
>

Bu öğretici, bir kurtarma hizmetleri kasası oluşturmaya ve bir Azure sanal makinesini (VM) yedeklemeye yönelik adımlar boyunca size yol gösterir. Kurtarma hizmetleri kasaları şunları korur:

* Azure Resource Manager tarafından dağıtılan VM'ler
* Klasik VM'ler
* Standart depolama VM'leri
* Premium depolama VM'leri
* Yönetilen Diskler üzerinde çalışan sanal makineler
* Azure Disk Şifrelemesi kullanılarak BEK ve KEK ile şifrelenmiş VM’ler
* Windows sanal makinelerinin VSS, Linux sanal makinelerinin özel anlık görüntü öncesi ve anlık görüntü sonrası betikler kullanılarak uygulamada tutarlı yedeklenmesi

Premium depolama VM'lerini koruma hakkında daha fazla bilgi için [Premium Storage VM'lerini Yedekleme ve Geri Yükleme](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup) makalesine bakın. Yönetilen disk sanal makinelerine yönelik destek hakkında daha fazla bilgi için bkz. [Yönetilen diskler üzerindeki sanal makineleri yedekleme ve geri yükleme](backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). Linux sanal makine yedeklemesi için betik öncesi ve betik sonrası çerçeve hakkında daha fazla bilgi almak isterseniz bkz. [Betik öncesi ve betik sonrası ile uygulamada tutarlı Linux sanal makine yedeklemesi] (https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)

> [!NOTE]
> Bu öğretici, Azure aboneliğinizde zaten bir VM'niz olduğunu ve yedekleme hizmetinin VM'ye erişmesine izin vermek üzere gerekli işlemleri yaptığınızı varsayar.
>
>

[!INCLUDE [learn-about-Azure-Backup-deployment-models](../../includes/backup-deployment-models.md)]

Korumak istediğiniz sanal makine sayısına bağlı olarak farklı başlangıç noktalarından başlayabilirsiniz. Tek bir işlemde birden çok sanal makine yedeklemek istiyorsanız Kurtarma Hizmetleri Kasası'na gidin ve [yedekleme işini kasa panosundan başlatın](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-recovery-services-vault). Tek bir sanal makineyi yedeklemek istiyorsanız VM yönetim dikey penceresinden yedekleme işini başlatabilirsiniz.

## <a name="configure-the-backup-job-from-the-vm-management-blade"></a>Yedekleme işini VM yönetim dikey penceresinden yapılandırın

Aşağıdaki adımları, Azure portalında sanal makine yönetimi dikey penceresinden yedekleme işini yapılandırmak için kullanın. Bu adımlar klasik portaldaki sanal makineler için geçerli değildir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Hub menüsünde **Diğer Hizmetler**’e tıklayıp Filtre listesine **Sanal makineler** yazın. Siz yazarken kaynakların listesini filtrelenir. Sanal makineler seçeneğini gördüğünüzde seçin.

  ![Hub menüsünde metin iletişim kutusunu açmak için Diğer Hizmetler'e tıklayın ve Sanal makineler yazın](./media/backup-azure-vms-first-look-arm/open-vm-from-hub.png)

  Abonelikteki sanal makineler (VM) listesi görüntülenir.

  ![Abonelikteki VM'lerin listesi görüntülenir.](./media/backup-azure-vms-first-look-arm/list-of-vms.png)

3. Listeden yedekleyeceğiniz VM'yi seçin.

  ![Abonelikteki VM'lerin listesi görüntülenir.](./media/backup-azure-vms-first-look-arm/list-of-vms-selected.png)

  VM’yi seçtiğinizde, sanal makineler sola kaydırılır ve sanal makine yönetim dikey penceresi ve sanal makine panosu açılır. </br>
 ![VM Yönetimi dikey penceresi](./media/backup-azure-vms-first-look-arm/vm-management-blade.png)

4. VM yönetim dikey penceresinde **Ayarlar** bölümünde **Yedekle**’ye tıklayın. </br>

  ![VM yönetim dikey penceresindeki yedekle seçeneği](./media/backup-azure-vms-first-look-arm/backup-option-vm-management-blade.png)

  Yedeklemeyi etkinleştir dikey penceresi açılır.

  ![VM yönetim dikey penceresindeki yedekle seçeneği](./media/backup-azure-vms-first-look-arm/vm-blade-enable-backup.png)

5. Kurtarma Hizmetleri kasası için **Var olanı seç**’e tıklayın ve açılan listeden kasayı seçin.

  ![Yedekleme Sihirbazını Etkinleştirme](./media/backup-azure-vms-first-look-arm/vm-blade-enable-backup.png)

  Kurtarma Hizmetleri kasası yoksa veya yeni bir kasa kullanmak istiyorsanız, **Yeni oluştur**’a tıklayın ve yeni kasanın adını girin. Sanal makine ile aynı Kaynak Grubunda ve aynı konumda yeni bir kasa oluşturulur. Farklı değerlere sahip bir Kurtarma Hizmetleri kasası oluşturmak istiyorsanız [kurtarma hizmetleri kasası oluşturma](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm) konusuna bakın.

6. Yedekleme ilkesinin ayrıntılarını görüntülemek için **Yedekleme ilkesi**’ne tıklayın.

  **Yedekleme İlkesi** dikey penceresi açılır ve seçili ilke ayrıntıları gösterilir. Başka ilke varsa, farklı bir yedekleme ilkesi seçmek için açılan menüyü kullanın. Yeni bir ilke oluşturmak istiyorsanız açılan menüden **Yeni Oluştur**'u seçin. Bir yedekleme ilkesi tanımlamaya yönelik yönergeler için bkz. [Yedekleme ilkesi tanımlama](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Yedekleme ilkesindeki değişiklikleri kaydetmek ve Yedekleme dikey penceresini etkinleştirme iletişim kutusuna dönmek için **Tamam**’a tıklayın.

  ![Yedekleme ilkesini seçme](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new-2.png)

7. Yedekleme etkinleştir dikey penceresinde, ilkeyi dağıtmak için **Yedeklemeyi Etkinleştir** öğesine tıklayın. İlke dağıtmak, onu kasayla ve sanal makinelerle ilişkilendirir.

  ![Yedeklemeyi Etkinleştir düğmesi](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-button.png)

8. Yapılandırmanın ilerlemesini portalda görünen bildirimler aracılığıyla takip edebilirsiniz. Aşağıdaki örnek Dağıtımın başlatıldığını gösterir.

  ![Yedeklemeyi Etkinleştirme bildirimi](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. Yapılandırma tamamlandığında Öğeyi Yedekle dikey penceresini açmak ve ayrıntıları görüntülemek için VM yönetim dikey penceresinde **Yedekle**’ye tıklayın.

  ![VM Yedekleme Öğesi Görünümü](./media/backup-azure-vms-first-look-arm/backup-item-view.png)

  İlk yedekleme gerçekleştirilene kadar **yedekleme durumu**, **Uyarı (İlk yedekleme beklemede)** olarak gösterilir. Bir sonraki zamanlanmış yedekleme işinin ne zaman olduğu görmek için **Yedekleme ilkesi** altında ilke adına tıklayın. Yedekleme İlkesi dikey penceresi açılır ve zamanlanmış yedekleme gösterilir.

10. Bir yedekleme işini çalıştırmak ve ilk kurtarma noktasını oluşturmak için Yedekleme kasası dikey penceresinde **Şimdi yedekle**’ye tıklayın.

  ![İlk yedeklemeyi çalıştırmak Şimdi yedekle’ye tıklayın](./media/backup-azure-vms-first-look-arm/backup-now.png)

  Şimdi Yedekle dikey penceresi açılır.

  ![Şimdi Yedekle dikey penceresi](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

11. Şimdi Yedekle dikey penceresinde, takvim simgesine tıklayın, bu kurtarma noktasının korunduğu son günü seçmek için Takvim denetimlerini kullanın ve **Yedekle**’ye tıklayın.

  ![Şimdi Yedekle kurtarma noktasının korunduğu son günü ayarlayın](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Dağıtım bildirimleri, yedekleme işinin tetiklendiğini ve Yedekleme işleri sayfasında işin ilerleme durumunu izleyebileceğinizi bilmenizi sağlar.

## <a name="configure-the-backup-job-from-the-recovery-services-vault"></a>Yedekleme işini Kurtarma Hizmetleri kasasından yapılandırın
Yedekleme işini yapılandırmak için, aşağıdaki adımları tamamlayın.  

1. Bir sanal makine için Kurtarma Hizmetleri kasası oluşturun.
2. Bir senaryo seçmek, bir Yedekleme ilkesi ayarlamak ve korunacak öğeleri tanımlamak için Azure portalını kullanın.
3. İlk yedeklemeyi çalıştırın.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Bir VM için kurtarma hizmetleri kasası oluşturma
Kurtarma Hizmetleri kasası, zaman içinde oluşturulan tüm yedeklemeleri ve kurtarma noktalarını depolayan bir varlıktır. Kurtarma Hizmetleri kasası ayrıca, korumalı VM'lere uygulanan yedekleme ilkesini de içerir.

> [!NOTE]
> VM'leri yedekleme işlemi, yerel bir işlemdir. Bir konumdaki VM'leri, başka bir konumda bulunan bir Kurtarma Hizmetleri kasasına yedekleyemezsiniz. Bu nedenle, yedeklenecek VM'ler içeren her Azure konumu için en az bir kurtarma Hizmetleri kasası ilgili konumda mevcut olmalıdır.
>
>

Kurtarma Hizmetleri kasası oluşturmak için:

1. Önceden yapmadıysanız Azure aboneliğinizi kullanarak [Azure portalında](https://portal.azure.com/) oturum açın.
2. Hub menüsünde **Diğer hizmetler**’e tıklayıp Filtre iletişim kutusunda **Kurtarma Hizmetleri** yazın. Siz yazarken kaynakların listesini filtrelenir. Listede Kurtarma Hizmetleri kasaları seçeneğini gördüğünüzde buna tıklayın.

    ![Kurtarma Hizmetleri Kasası oluşturma 1. adım](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Abonelikte Kurtarma Hizmetleri kasaları varsa, kasalar listelenir.

    ![Kurtarma Hizmetleri Kasası oluşturma 2. adım](./media/backup-azure-vms-first-look-arm/list-of-rs-vault.png)
3. **Kurtarma Hizmetleri kasaları** menüsünde **Ekle**'ye tıklayın.

    ![Kurtarma Hizmetleri Kasası oluşturma 2. adım](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Kurtarma Hizmetleri kasası dikey penceresi açılır ve sizden bir **Ad**, **Abonelik**, **Kaynak Grubu** ve **Konum** sağlamanızı ister.

    ![Kurtarma Hizmetleri Kasası Oluşturma 3. adım](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. **Ad** alanına, kasayı tanımlayacak kolay bir ad girin. Adın Azure aboneliği için benzersiz olması gerekir. 2 ila 50 karakterden oluşan bir ad yazın. Ad bir harf ile başlamalıdır ve yalnızca harf, rakam ve kısa çizgi içerebilir.

5. **Abonelik** bölümündeki açılır menüyü kullanarak Azure aboneliğini seçin. Yalnızca bir abonelik kullanıyorsanız bu abonelik görüntülenir ve sonraki adıma atlayabilirsiniz. Hangi aboneliğin kullanılacağından emin değilseniz varsayılan (veya önerilen) aboneliği kullanın. Yalnızca kuruluş hesabınızın birden çok Azure aboneliği ile ilişkili olması durumunda birden çok seçenek olur.

6. **Kaynak grubu** bölümünde:

    * bir Kaynak grubu oluşturmak istiyorsanız, **Yeni oluştur**’u seçin.
    Veya
    * **Var olanı kullan**’ı seçin ve açılır menüyü kullanarak mevcut Kaynak gruplarının listesine bakın.

  Kaynak grupları hakkında eksiksiz bilgiler için bkz. [Azure Resource Manager’a genel bakış](../azure-resource-manager/resource-group-overview.md).

7. Kasa için coğrafi bölgeyi seçmek üzere **Konum**'a tıklayın. Bu seçim, yedekleme verilerinizin gönderildiği coğrafi bölgeyi belirler.

  > [!IMPORTANT]
  > VM'nizin hangi konumda bulunduğundan emin değilseniz kasa oluşturma iletişim kutusunu kapatın ve portaldaki Virtual Machines listesine gidin. Birden çok bölgede sanal makineniz varsa her bölgede bir Kurtarma Hizmetleri kasası oluşturun. Sonraki konuma geçmeden önce, kasayı ilk konumda oluşturun. Yedekleme verilerini depolamak için depolama hesaplarının belirtilmesi gerekmez; Kurtarma Hizmetleri kasası ve Azure Backup hizmeti depolamayı otomatik olarak gerçekleştirir.
  >

8. Kurtarma Hizmetleri kasası dikey penceresinin alt kısmındaki **Oluştur**’a tıklayın.

    Kurtarma Hizmetleri kasasının oluşturulması birkaç dakika sürebilir. Portalın sağ üst kısmından durum bildirimlerini izleyin. Kasanız oluşturulduktan sonra Kurtarma Hizmetleri kasaları listesinde görünür. Birkaç dakika sonra kasayı görmezseniz **Yenile**’ye tıklayın.

    ![Yenile düğmesine tıklayın](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Kasanızı Kurtarma Hizmetleri kasaları listesinde gördükten sonra, depolama yedekliliğini ayarlamaya hazır olursunuz.

Kasanızı oluşturduğunuza göre, artık depolama çoğaltmayı nasıl ayarlayacağınızı öğrenebilirsiniz.

### <a name="set-storage-replication"></a>Depolama Çoğaltmayı Ayarlama
Depolama çoğaltma seçeneği, coğrafi olarak yedekli depolama ve yerel olarak yedekli depolama arasında seçim yapmanıza olanak sağlar. Varsayılan olarak, kasanız coğrafi olarak yedekli depolamaya sahiptir. Kurtarma Hizmetleri kasası, birincil yedeklemeniz ise, depolama çoğaltma seçeneğini coğrafi olarak yedekli depolama şeklinde bırakın. Daha düşük dayanıklılık düzeyinde olan daha uygun maliyetli bir seçenek istiyorsanız yerel olarak yedekli depolamayı seçin. [Coğrafi olarak yedekli](../storage/storage-redundancy.md#geo-redundant-storage) ve [yerel olarak yedekli](../storage/storage-redundancy.md#locally-redundant-storage) depolama seçenekleri hakkında daha fazla bilgiyi [Azure Storage çoğaltmaya genel bakış](../storage/storage-redundancy.md) bölümünde edinebilirsiniz.

Depolama çoğaltma ayarını düzenlemek için:

1. **Kurtarma Hizmetleri kasaları** dikey penceresinden, yeni kasayı seçin.

  ![Kurtarma Hizmetleri kasası listesinden yeni kasayı seçin](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

  Kasayı seçtiğinizde, Ayarlar dikey penceresi (*üst kısmında kasanın adı yer alır*) ve kasa ayrıntıları dikey penceresi açılır.

  ![Yeni kasa için depolama yapılandırmasını görüntüleme](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)

2. Yeni kasanın Ayarlar dikey penceresinde dikey kaydırma çubuğunu kullanarak Yönet bölümüne inin ve **Yedekleme Altyapısı**’na tıklayın.
    Yedekleme Altyapısı dikey penceresi açılır.
3. Yedekleme Altyapısı dikey penceresinde, **Yedekleme Yapılandırması**’na tıklayarak **Yedekleme Yapılandırması** dikey penceresini açın.

    ![Yeni kasa için depolama yapılandırması ayarlama](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Kasanız için uygun depolama çoğaltma seçeneğini belirleyin.

    ![depolama yapılandırması seçenekleri](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Varsayılan olarak, kasanız coğrafi olarak yedekli depolamaya sahiptir. Azure'ı birincil yedek depolama uç noktası olarak kullanıyorsanız, **Coğrafi olarak yedekli** seçeneğini kullanmaya devam edin. Azure’u birincil yedek depolama uç noktası olarak kullanmıyorsanız, Azure depolama maliyetlerini azaltan **Yerel olarak yedekli** seçeneğini belirleyin. [Coğrafi olarak yedekli](../storage/storage-redundancy.md#geo-redundant-storage) ve [yerel olarak yedekli](../storage/storage-redundancy.md#locally-redundant-storage) depolama seçenekleri hakkında daha fazla bilgiyi [Depolama yedekliliğine genel bakış](../storage/storage-redundancy.md) bölümünden edinebilirsiniz.


## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Yedekleme hedefi seçme, ilke ayarlama ve korunacak öğeleri tanımlama
VM'yi bir kasaya kaydetmeden önce, aboneliğe eklenmiş olan yeni sanal makinelerin tanımlandığından emin olmak için bulma işlemini çalıştırın. Bu işlem, bulut hizmeti adı ve bölge gibi ek bilgilerle birlikte abonelikteki sanal makinelerin listesi için Azure'ı sorgular. Azure portalda, senaryo, kurtarma hizmetleri kasasına yerleştireceğiniz içeriğe başvuruda bulunur. İlke, kurtarma noktalarının ne sıklıkta ve ne zaman alınacağına yönelik zamanlamadır. İlke aynı zamanda kurtarma noktaları için bekletme aralığını içerir.

1. Açık bir kurtarma hizmetleri kasanız zaten varsa 2. adıma geçin. Bu seçeneği görmezseniz, Hub menüsünde **Diğer hizmetler**'e tıklayın ve kaynak listesinde **Kurtarma Hizmetleri** yazıp **Kurtarma Hizmetleri kasaları** seçeneğine tıklayın.

    ![Kurtarma Hizmetleri Kasası oluşturma 1. adım](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Kurtarma hizmetleri kasalarının listesi görünür.

    ![Kurtarma Hizmetleri kasaları listesinin görünümü](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    Kurtarma hizmetleri kasalarının listesinden bir kasayı seçerek kasanın panosunu açın.

     ![Kasa dikey penceresini açma](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Kasa panosu menüsünden Yedekleme dikey penceresini açmak için **Yedekle** seçeneğine tıklayın.

    ![Yedekleme dikey penceresini açma](./media/backup-azure-arm-vms-prepare/backup-button.png)

    Yedekleme ve Yedekleme Hedefi dikey pencereleri açılır.

    ![Senaryo dikey penceresini açma](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)
3. Yedekleme Hedefi dikey penceresindeki **İş yükünüzün çalıştırıldığı yer** açılan menüsünde Azure'u seçin. **Neleri yedeklemek istiyorsunuz** açılan penceresinde, Sanal makine’yi seçin ve ardından **Tamam**’a tıklayın.

    Bu eylemler, VM uzantısını kasa ile kaydeder. Yedekleme Hedefi dikey penceresi kapanır ve **Yedekleme ilkesi** dikey penceresi açılır.

    ![Senaryo dikey penceresini açma](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. Yedekleme ilkesi dikey penceresinde, kasaya uygulamak istediğiniz yedekleme ilkesini seçin.

    ![Yedekleme ilkesini seçme](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Varsayılan ilkenin ayrıntıları, açılan menü altında listelenir. Yeni bir ilke oluşturmak istiyorsanız açılan menüden **Yeni Oluştur**'u seçin. Bir yedekleme ilkesi tanımlamaya yönelik yönergeler için bkz. [Yedekleme ilkesi tanımlama](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Yedekleme ilkesini kasa ile ilişkilendirmek için **Tamam**’a tıklayın.

    Yedekleme ilkesi dikey penceresi kapanır ve **Sanal makineleri seçin** dikey penceresi açılır.
5. **Sanal makineleri seçin** dikey penceresinde belirtilen ilkeyle ilişkilendirilecek sanal makineleri seçin ve **Tamam**'a tıklayın.

    ![İş yükünü seçme](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    Seçilen sanal makine doğrulanır. Görmeyi beklediğiniz sanal makineleri görmüyorsanız Kurtarma Hizmetleri kasasıyla aynı Azure konumda olduklarından emin olun. Kurtarma Hizmetleri kasasının konumu kasa panosunda gösterilir.

6. Kasa için tüm ayarları tanımladığınıza göre, ilkeyi kasaya ve VM’lere dağıtmak için Yedekleme dikey penceresinde **Yedeklemeyi Etkinleştir** öğesine tıklayın. Yedekleme ilkesinin dağıtılmasıyla, sanal makine için ilk kurtarma noktasını oluşturulmaz.

    ![Yedeklemeyi Etkinleştirme](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Yedekleme başarıyla etkinleştirildikten sonra yedekleme ilkeniz ilgili zamanda çalıştırılır. Ancak, ilk yedekleme işini başlatmak için devam edin.

## <a name="initial-backup"></a>İlk yedekleme
Sanal makine üzerinde bir yedekleme ilkesinin dağıtılmış olması, verilerin yedeklendiği anlamına gelmez. Varsayılan olarak, zamanlanmış birinci yedekleme (yedekleme ilkesinde tanımlandığı şekilde) ilk yedeklemedir. İlk yedekleme gerçekleştirilene kadar, **Yedekleme İşleri** dikey penceresindeki Son Yedekleme Durumu **Uyarı (ilk yedekleme bekleme)** olarak gösterilir.

![Yedekleme beklemede](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

İlk yedeklemeniz kısa süre içinde başlamazsa **Şimdi Yedekle** seçeneğini çalıştırmanız önerilir.

İlk yedekleme işini çalıştırmak için:

1. Kasa panosunda **Yedekleme Öğeleri** altındaki sayıya veya **Yedekleme Öğeleri** kutucuğuna tıklayın. <br/>
  ![Ayarlar simgesi](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  **Yedekleme Öğeleri** dikey penceresi açılır.

  ![Öğeleri yedekleme](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. **Yedekleme Öğeleri** dikey penceresinde öğeyi seçin.

  ![Ayarlar simgesi](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  **Yedekleme Öğeleri** listesi açılır. <br/>

  ![Tetiklenmiş yedekleme işi](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. **Yedekleme Öğeleri** listesinde üç noktaya **...** tıklayarak Bağlam menüsünü açın.

  ![Bağlam menüsü](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Bağlam menüsü görüntülenir.

  ![Bağlam menüsü](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. Bağlam menüsünde **Şimdi yedekle**’ye tıklayın.

  ![Bağlam menüsü](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Şimdi Yedekle dikey penceresi açılır.

  ![Şimdi Yedekle dikey penceresi](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. Şimdi Yedekle dikey penceresinde, takvim simgesine tıklayın, bu kurtarma noktasının korunduğu son günü seçmek için Takvim denetimlerini kullanın ve **Yedekle**’ye tıklayın.

  ![Şimdi Yedekle kurtarma noktasının korunduğu son günü ayarlayın](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Dağıtım bildirimleri, yedekleme işinin tetiklendiğini ve Yedekleme işleri sayfasında işin ilerleme durumunu izleyebileceğinizi bilmenizi sağlar. VM’nizin boyutuna bağlı olarak, ilk yedeklemenin oluşturulması biraz zaman alabilir.

6. İlk yedekleme durumunu izlemek için kasa panosunda **Yedekleme İşleri** kutucuğunda **Devam eden**’e tıklayın.

  ![Yedekleme İşleri kutucuğu](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Yedekleme İşleri dikey penceresi açılır.

  ![Yedekleme İşleri kutucuğu](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  **Yedekleme işleri** dikey penceresinde tüm işlerin durumunu görebilirsiniz. VM’niz için yedekleme işinin devam edip etmediğini veya bitip bitmediğini kontrol edin. Yedekleme işi tamamlandığında, durum *Tamamlandı* olur.

  > [!NOTE]
  > Azure Backup hizmeti, yedekleme işleminin parçası olarak, her VM'deki yedekleme uzantısına tüm yazma işlemlerini boşaltmaya ve tutarlı bir anlık görüntü almaya yönelik bir komut verir.
  >
  >


[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>VM Aracısı'nı sanal makineye yükleme
Bu bilgiler, gerekmesi halinde kullanılabilmesi için sağlanmıştır. Backup uzantısının çalışması için Azure VM Aracısı'nın Azure sanal makinesine yüklenmesi gerekir. Ancak VM'niz Azure galerisinden oluşturulmuşsa VM Aracısı, sanal makine üzerinde zaten mevcuttur. Şirket içi veri merkezlerinden geçişi sağlanan VM'lerde VM Aracısı yüklü olmaz. Böyle bir durumda, VM Aracısı'nın yüklenmesi gerekir. Azure VM’yi yedekleme konusunda sorun yaşarsanız Azure VM Aracısı'nın sanal makineye doğru şekilde yüklenip yüklenmediğini kontrol edin (aşağıdaki tabloya bakın). Özel bir VM oluşturuyorsanız sanal makine sağlanmadan önce [**VM Aracısı'nı yükle** onay kutusunun seçili olduğundan emin olun](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[VM Aracısı](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) ve [nasıl yükleneceği](../virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) konusunda bilgi edinin.

Aşağıdaki tabloda, Windows ve Linux VM'ler için VM Aracısı ile ilgili ek bilgiler sağlanmıştır.

| **İşlem** | **Windows** | **Linux** |
| --- | --- | --- |
| VM Aracısı'nı yükleme |<li>[Aracı MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) dosyasını indirip yükleyin. Yüklemeyi tamamlamak için Yönetici ayrıcalıklarına sahip olmanız gerekir. <li>Aracının yüklü olduğunu belirtmek üzere [VM özelliğini güncelleştirin](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx). |<li> En son [Linux aracısını](https://github.com/Azure/WALinuxAgent) GitHub'dan yükleyin. Yüklemeyi tamamlamak için Yönetici ayrıcalıklarına sahip olmanız gerekir. <li> Aracının yüklü olduğunu belirtmek üzere [VM özelliğini güncelleştirin](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx). |
| VM Aracısı'nı güncelleştirme |VM Aracısı'nı güncelleştirmek için [VM Aracısı ikili dosyalarının](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) yeniden yüklenmesi yeterlidir. <br>VM aracısı güncelleştirilirken herhangi bir yedekleme işleminin çalıştırılmadığından emin olun. |[Linux VM Aracısı'nı güncelleştirme](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ile ilgili yönergeleri uygulayın. <br>VM Aracısı güncelleştirilirken herhangi bir yedekleme işleminin çalıştırılmadığından emin olun. |
| VM Aracısı yüklemesini doğrulama |<li>Azure VM'de *C:\WindowsAzure\Packages* klasörüne gidin. <li>Mevcut WaAppAgent.exe dosyasını bulmanız gerekir.<li> Dosyaya sağ tıklayın, **Özellikler**'e gidin ve ardından **Ayrıntılar** sekmesini seçin. Ürün Sürümü alanı 2.6.1198.718 veya üzeri olmalıdır. |Yok |

### <a name="backup-extension"></a>Backup uzantısı
VM Aracısı sanal makineye yüklendikten sonra, Azure Backup hizmeti yedekleme uzantısını VM Aracısı'na yükler. Azure Backup hizmeti, ek kullanıcı müdahalesi olmadan sorunsuz bir şekilde yedekleme uzantısını yükseltir ve uzantıya düzeltme eki uygular.

VM çalışmıyor olsa dahi, Backup hizmeti yedekleme uzantısını yükler. Çalışan bir VM, uygulamayla tutarlı bir kurtarma noktası alınma olasılığını en yükseğe çıkarır. Ancak Azure Backup hizmeti, uzantının yüklenememiş olması ve VM'nin kapalı olması durumunda dahi VM'yi yedeklemeye devam eder. Bu yedekleme türüne Çevrimdışı VM adı verilir ve kurtarma noktası *çökme tutarlıdır*.

## <a name="troubleshooting-information"></a>Sorun giderme bilgileri
Bu makaledeki bazı görevleri yerine getirirken sorun yaşıyorsanız lütfen [Sorun giderme rehberine](backup-azure-vms-troubleshoot.md) başvurun.

## <a name="pricing"></a>Fiyatlandırma
Azure VM'lerin yedeklenmesi maliyeti, korumalı örneklerinin sayısını temel alır. Korumalı bir örneğin tanımı için, bkz: [Korumalı örnek nedir](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). Bir sanal makineyi yedeklemenin maliyetini hesaplamaya ilişkin bir örnek için, bkz: [Korumalı örnekler nasıl hesaplanır](backup-azure-vms-introduction.md#calculating-the-cost-of-protected-instances). [Yedekleme Fiyatlandırması](https://azure.microsoft.com/pricing/details/backup/) hakkında bilgi için Azure Backup Fiyatlandırma sayfasına bakın.

## <a name="questions"></a>Sorularınız mı var?
Sorularınız varsa veya dahil edilmesini istediğiniz herhangi bir özellik varsa [bize geri bildirim gönderin](http://aka.ms/azurebackup_feedback).

