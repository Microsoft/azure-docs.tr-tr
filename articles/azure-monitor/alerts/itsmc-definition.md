---
title: Log Analytics BT Hizmet Yönetimi Bağlayıcısı
description: Bu makalede, BT Hizmet Yönetimi Bağlayıcısı (ıSMC) hakkında genel bir bakış ve bu iş öğelerini Log Analytics izlemek ve yönetmek ve sorunları hızlı bir şekilde çözmek için kullanma hakkında bilgiler sunulmaktadır.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 98f53ec1b6506a6d47146377e837576254f445e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601075"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-solution"></a>BT hizmet yönetimi çözümünü kullanarak Azure 'ı ıTSM araçlarına bağlama

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Bu makalede, BT hizmet yönetimi (ıTSM) iş öğelerinizi merkezi olarak yönetmek için Log Analytics BT Hizmet Yönetimi Bağlayıcısı (ıSMC) yapılandırma hakkında bilgi sağlanır.

## <a name="add-it-service-management-connector"></a>BT Hizmet Yönetimi Bağlayıcısı Ekle

Bir bağlantı oluşturabilmeniz için önce ıSMC 'yi yüklemeniz gerekir.

1. Azure portal **kaynak oluştur**' u seçin:

   ![Kaynak oluşturmak için menü öğesini gösteren ekran görüntüsü.](media/itsmc-overview/azure-add-new-resource.png)

2. Azure Marketi 'nde **BT hizmet yönetimi Bağlayıcısı** arayın. Sonra **Oluştur**' u seçin.

   ![Azure Marketi 'ndeki Oluştur düğmesini gösteren ekran görüntüsü.](media/itsmc-overview/add-itsmc-solution.png)

3. **La çalışma alanı** bölümünde, itsmc ' ı yüklemek istediğiniz Log Analytics çalışma alanını seçin.
   > [!NOTE]
   > Log Analytics çalışma alanlarını yalnızca şu bölgelerde yükleyebilirsiniz: Doğu ABD, Batı ABD 2, Orta Güney ABD, Orta Batı ABD, US Gov Arizona, US Gov Virginia, Kanada Orta, Batı Avrupa, Güney UK, Güneydoğu Asya, Japonya Doğu, Orta Hindistan ve Avustralya Güneydoğu.

4. **Log Analytics çalışma alanı** bölümünde, itsmc kaynağını oluşturmak istediğiniz kaynak grubunu seçin:

   ![Log Analytics çalışma alanı bölümünü gösteren ekran görüntüsü.](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > Microsoft Operations Management Suite (OMS) ile Azure Izleyici arasında devam eden geçişin bir parçası olarak, OMS çalışma alanları artık *Log Analytics çalışma alanları* olarak adlandırılmaktadır.

5. **Tamam**’ı seçin.

ISMC kaynağı dağıtıldığında, pencerenin sağ üst köşesinde bir bildirim görüntülenir.

## <a name="create-an-itsm-connection"></a>ITSM bağlantısı oluşturma

ISMC yükledikten sonra, ITSM aracınızdan bağlantı sağlamak için ITSM aracınızı hazırmalısınız. Bağlanmakta olduğunuz ıTSM ürününe bağlı olarak, yönergeler için aşağıdaki bağlantılardan birini seçin:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

ITSM aracınızı önceden tamamladıktan sonra bağlantı oluşturmak için aşağıdaki adımları izleyin:

1. **Tüm kaynaklarda**, **ServiceDesk 'yi arayın (*çalışma alanınızın adı*)**:

   ![Azure portal son kaynakları gösteren ekran görüntüsü.](media/itsmc-definition/create-new-connection-from-resource.png)

1. Sol bölmedeki **çalışma alanı veri kaynakları** altında **ITSM bağlantıları**' nı seçin:

   ![ITSM bağlantıları menü öğesini gösteren ekran görüntüsü.](media/itsmc-overview/add-new-itsm-connection.png)

1. **Bağlantı ekle**' yi seçin.

1. Kullanmakta olduğunuz ıTSM ürününe göre bağlantı ayarlarını belirtin:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > Varsayılan olarak, ıSMC her 24 saatte bir bağlantının yapılandırma verilerini yeniler. Yaptığınız düzenlemeleri veya şablon güncelleştirmelerini yansıtacak şekilde bağlantınızın verilerini hemen yenilemek için bağlantı bölmesinde **Eşitle** düğmesini seçin:
   >
   > ![Bağlantı bölmesindeki Eşitle düğmesini gösteren ekran görüntüsü.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Azure uyarılarından ıTSM iş öğeleri oluşturma

ITSM bağlantınızı oluşturduktan sonra, Azure uyarılarını temel alan ıTSM aracınız üzerinde iş öğeleri oluşturmak için IMC ' yi kullanabilirsiniz. İş öğelerini oluşturmak için, eylem gruplarında ıTSM eylemini kullanacaksınız.

Eylem grupları, Azure uyarılarınız için eylemleri tetiklemenin modüler ve yeniden kullanılabilir bir yolunu sağlar. İşlem gruplarını, ölçüm uyarıları, etkinlik günlüğü uyarıları ve Azure portal uyarılarını Log Analytics kullanabilirsiniz.

> [!NOTE]
> ITSM bağlantısını oluşturduktan sonra eşitleme işleminin tamamlanabilmesi için 30 dakika beklemeniz gerekir.

## <a name="define-a-template"></a>Şablon tanımlama

Belirli iş öğesi türleri ıTSM aracında tanımladığınız şablonları kullanabilir. Şablonları kullanarak, bir eylem grubu için sabit değerlere göre otomatik olarak doldurulacak alanları tanımlayabilirsiniz. Bir eylem grubu tanımının bir parçası olarak kullanmak istediğiniz şablonu tanımlayabilirsiniz. ServiceNow belgeleri oluşturma hakkında bilgi edinmek için (burada) [ https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/form-administration/task/t_CreateATemplateUsingTheTmplForm.html ].

Bir eylem grubu oluşturmak için:

1. Azure portal,  **Uyarılar**' ı seçin.
2. Ekranın üst kısmındaki menüde **Yönet eylemler**' i seçin:

    ![Eylemleri Yönet menü öğesini gösteren ekran görüntüsü.](media/itsmc-overview/action-groups-selection-big.png)

   **Eylem grubu oluştur** penceresi görüntülenir.

3. Eylem grubunuzu oluşturmak istediğiniz **aboneliği** ve **kaynak grubunu** seçin. Eylem grubu **adı** ve eylem grubunuz için **görünen ad** değerlerini girin. Ardından **İleri: bildirimler**' i seçin.

    ![Eylem grubu Oluştur penceresini gösteren ekran görüntüsü.](media/itsmc-overview/action-groups-details.png)

4. **Bildirimler** sekmesinde **İleri: eylemler**' i seçin.
5. **Eylemler** sekmesinde, **eylem türü** listesinde **ISM** ' yi seçin. **Ad** için, eylem için bir ad sağlayın. Ardından **düzenleme ayrıntılarını** temsil eden kalem düğmesini seçin.

    ![Bir eylem grubu oluşturmak için seçimleri gösteren ekran görüntüsü.](media/itsmc-definition/action-group-pen.png)

6. **Abonelik** listesinde, Log Analytics çalışma alanınızı içeren aboneliği seçin. **Bağlantı** listesinde ITSM bağlayıcınızın adını seçin. Bunun ardından çalışma alanınızın adı gelir. Bir örnek *Myitsmconnector (MyWorkspace)*.

7. Bir **Iş öğesi** türü seçin.

8. Sabit değerlerle kullanıma hazır alanları doldurmanız istiyorsanız **özel şablon kullan**' ı seçin. Aksi takdirde, **şablon** listesinde var olan bir [şablonu](#define-a-template) seçin ve şablon alanlarına sabit değerleri girin.

9. ITSM eylem grubu oluşturmak için arabirimin son bölümünde her uyarı için kaç iş öğesi oluşturulacağını tanımlayabilirsiniz.

   > [!NOTE]
   > Bu bölüm yalnızca günlük arama uyarıları için geçerlidir. Tüm diğer uyarı türleri için, uyarı başına bir iş öğesi oluşturacaksınız.

   * **Iş öğesi** açılan listesinde **olay** veya **Uyarı** ' yı seçtiyseniz, her yapılandırma öğesi için bireysel iş öğeleri oluşturma seçeneğiniz vardır.
    
     ![Bir iş öğesi olarak olay seçiliyken I T S M bileti alanını gösteren ekran görüntüsü.](media/itsmc-overview/itsm-action-configuration.png)
    
     * **Her yapılandırma öğesi için bireysel çalışma öğeleri oluştur** onay kutusunu seçerseniz, her uyarıdaki her yapılandırma öğesi yeni bir iş öğesi oluşturur. Aynı etkilenen yapılandırma öğeleri için birkaç uyarı gerçekleşeceğinden, her yapılandırma öğesi için birden fazla iş öğesi olacaktır.

       Örneğin, üç yapılandırma öğesine sahip bir uyarı üç iş öğesi oluşturacaktır. Bir yapılandırma öğesi olan bir uyarı, bir iş öğesi oluşturur.
        
     * **Her yapılandırma öğesi için bireysel çalışma öğeleri oluştur** onay kutusunu temizlerseniz, ısmc her bir uyarı kuralı için tek bir iş öğesi oluşturur ve bu tüm etkilenen yapılandırma öğelerine eklenir. Önceki biri kapatılmışsa yeni bir iş öğesi oluşturulur.

       >[!NOTE]
       > Bu durumda, tetiklenen uyarılardan bazıları ıTSM aracında yeni iş öğeleri oluşturmaz.

       Örneğin, üç yapılandırma öğesine sahip bir uyarı, bir iş öğesi oluşturur. Önceki örnekteki aynı uyarı kuralına yönelik bir uyarı için bir yapılandırma öğesi varsa, bu yapılandırma öğesi oluşturulan iş öğesinde etkilenen yapılandırma öğeleri listesine eklenecektir. Bir yapılandırma öğesi olan farklı bir uyarı kuralı için bir uyarı, bir iş öğesi oluşturur.

   * **Iş öğesi** açılan listesinde **olay** ' ı seçtiyseniz, her bir günlük girdisi için veya her bir yapılandırma öğesi için bireysel iş öğeleri oluşturmayı seçebilirsiniz.
    
     ![Çalışma öğesi olarak seçilen olay ile ı T S M bileti alanını gösteren ekran görüntüsü.](media/itsmc-overview/itsm-action-configuration-event.png)

     * **Her günlük girdisi için bireysel iş öğeleri Oluştur ' u seçerseniz (yapılandırma öğesi alanı doldurulmamış olur. , Çok sayıda iş öğesine neden olabilir.)**, günlük araması uyarı sorgusunun arama sonuçlarındaki her bir satır için bir iş öğesi oluşturulacaktır. İş öğesinin yükünde Açıklama özelliği, arama sonuçlarından alınan satırı içerecektir.
      
     * **Her yapılandırma öğesi için bireysel iş öğeleri oluştur**' u seçerseniz, her uyarıdaki her yapılandırma öğesi yeni bir iş öğesi oluşturur. Her yapılandırma öğesi ıTSM sisteminde birden fazla iş öğesine sahip olabilir. Bu seçenek, iş öğesi türü olarak **olay** ' i seçtikten sonra görünen onay kutusunu seçme ile aynıdır.

10. **Tamam**’ı seçin.

Bir Azure uyarı kuralı oluşturduğunuzda veya düzenlediğinizde, bir ıTSM eylemi olan bir eylem grubunu kullanın. Uyarı tetiklendiğinde, çalışma öğesi ıTSM aracında oluşturulur veya güncelleştirilir.

> [!NOTE]
> ITSM eyleminin fiyatlandırması hakkında daha fazla bilgi için, eylem grupları için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/monitor/) bakın.
>
> Uyarı kuralı tanımındaki kısa açıklama alanı, ıTSSM eylemini kullanarak gönderdiğinizde 40 karakterle sınırlıdır.

## <a name="next-steps"></a>Sonraki adımlar

* [ISMC sorunlarını giderme](./itsmc-resync-servicenow.md)
