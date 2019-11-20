---
title: Azure IoT Central bağlayıcısını Microsoft Flow ' de kullanma | Microsoft Docs
description: İş akışlarını tetiklemek, cihazları oluşturmak, almak, güncelleştirmek, silmek ve iş akışlarında komutları çalıştırmak için Microsoft Flow IoT Central bağlayıcısını kullanın.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: f1912b90df75f3d85f6ba5d730c723cf42803237
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930368"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Microsoft Flow içinde IoT Central Bağlayıcısı ile iş akışları oluşturun

*Bu konu, oluşturucular ve yöneticiler için geçerlidir.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

İş kullanıcılarının kullandığı birçok uygulama ve hizmet içindeki iş akışlarını otomatikleştirmek için Microsoft Flow kullanın. Microsoft Flow IoT Central bağlayıcısını kullanarak IoT Central bir kural tetiklendiğinde iş akışlarını tetikleyebilirsiniz. IoT Central veya başka bir uygulama tarafından tetiklenen bir iş akışında, aşağıdakileri yapmak için IoT Central bağlayıcısında bulunan eylemleri kullanabilirsiniz:
- Cihaz oluşturma
- Cihaz bilgilerini al
- Cihazın özelliklerini ve ayarlarını güncelleştirme
- Cihazda bir komut çalıştırma
- Bir cihazı silme

IoT Central mobil bildirimler ve Microsoft ekipleri gibi diğer hizmetlere bağlanan [bu Microsoft Flow şablonlarına](https://aka.ms/iotcentralflowtemplates) göz atın.

## <a name="prerequisites"></a>Önkoşullar

- Kullandıkça Öde uygulaması
- Microsoft Flow kullanmak için bir Microsoft kişisel veya iş veya okul hesabı ([Microsoft Flow planları hakkında daha fazla bilgi edinin](https://aka.ms/microsoftflowplans))
- Azure IoT Central bağlayıcısını kullanmak için bir iş veya okul hesabı

## <a name="trigger-a-workflow"></a>İş akışını tetikleme

Bu bölümde, bir kural IoT Central tetiklendiğinde Flow mobil uygulamasında bir mobil bildirimin nasıl tetiklenmesi gösterilmektedir. Katıştırılmış Microsoft Flow tasarımcısını kullanarak bu iş akışının tamamını IoT Central uygulamanız içinde oluşturabilirsiniz.

1. [IoT Central bir kural oluşturarak](howto-create-telemetry-rules.md)başlayın. Kural koşullarını kaydettikten sonra, **Microsoft Flow eylemini** yeni bir eylem olarak seçin. İş akışınızı yapılandırmak için bir iletişim kutusu penceresi açılır. İmzaladığınız IoT Central kullanıcı hesabı Microsoft Flow oturum açmak için kullanılır.

    ![Yeni bir Microsoft Flow eylemi oluştur](media/howto-add-microsoft-flow/createflowaction.png)

1. Erişiminiz olan ve bu IoT Central kuralına bağlı olan iş akışlarının bir listesini görürsünüz. **Şablonları keşfet** veya **Yeni > Şablondan Oluştur** ' a tıklayın ve kullanılabilir şablonlardan herhangi birini seçebilirsiniz. 

    ![Kullanılabilir Microsoft Flow şablonları](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Seçtiğiniz şablondaki bağlayıcılarda oturum açmanız istenir. 

    > [!NOTE]
    > Azure IoT Central bağlayıcısını kullanmak için bir Azure Active Directory hesabı (iş veya okul hesabı) kullanarak oturum açmanız gerekir. abc@outlook.com veya abc@live.com gibi bir kişisel hesap Azure IoT Central Bağlayıcısı tarafından desteklenmez.

    Bağlayıcılarda oturum açtıktan sonra, iş akışınızı derlemek için tasarımcı 'ya de sahip olursunuz. İş akışında, uygulamanız ve kuralınız zaten doldurulmuş bir IoT Central tetikleyicisi vardır.

1. Eyleme geçirilen bilgileri özelleştirerek ve yeni eylemler ekleyerek iş akışını özelleştirebilirsiniz. Bu örnekte, eylem **Bildirimler-bir mobil bildirim gönder**. IoT Central kuralınızdan *dinamik içerik* ekleyebilirsiniz ve bu, bildirime cihaz adı ve zaman damgası gibi önemli bilgileri sağlar.

    > [!NOTE]
    > Kuralı tetikleyen ölçüm ve özellik değerlerini almak için dinamik içerik penceresinde **daha fazla metni göster** ' i seçin.

    ![Dinamik bölme açıkken akış düzenlemesi eylemi](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Eyleminizi düzenlemenizi bitirdiğinizde **Kaydet**' i seçin. İş akışınızın genel bakış sayfasına yönlendirilirsiniz. Burada çalıştırma geçmişini görebilir ve diğer iş arkadaşlarınızla paylaşabilirsiniz.

    > [!NOTE]
    > IoT Central uygulamanızdaki diğer kullanıcıların bu kuralı düzenlemesini istiyorsanız, Microsoft Flow birlikte onlarla paylaşmanız gerekir. Microsoft Flow hesaplarını iş akışınıza sahipler olarak ekleyin.

1. IoT Central uygulamanıza geri giderseniz, bu kuralın Eylemler alanında bir Microsoft Flow eylemi olduğunu görürsünüz.

Ayrıca, doğrudan Microsoft Flow IoT Central bağlayıcısını kullanarak iş akışları da oluşturabilirsiniz. Ardından, hangi IoT Central uygulamasının bağlanılacağını seçebilirsiniz.

## <a name="create-a-device-in-a-workflow"></a>İş akışında cihaz oluşturma

Bu bölümde, mobil cihazda Microsoft Flow mobil uygulamayı kullanarak bir düğmenin IoT Central yeni bir cihazın nasıl oluşturulacağı gösterilmektedir. Bir cihaz başka bir yere eklendiğinde yeni bir cihaz oluşturarak IoT Central ile Dynamics gibi ERP sistemlerini bütünleştirmek için akışta bu eylemi kullanabilirsiniz.

1. Microsoft Flow ' de boş bir iş akışı oluşturarak başlayın.

1. Bir tetikleyici olarak **Mobil Için akış düğmesini** arayın.

1. Bu tetikleyiciye **Cihaz adı**adlı bir metin girişi ekleyin. Açıklama metnini, **yeni cihazınızın cihaz adını girmek**üzere değiştirin.

1. Yeni bir eylem ekleyin. **Azure IoT Central arama-cihaz oluşturma** eylemi.

1. Uygulamanızı seçin ve açılan kutudan bir cihaz oluşturmak için bir cihaz şablonu seçin. Cihazın tüm özelliklerini ve ayarlarını göstermek için eylemi genişlettireceksiniz.

1. Cihaz adı alanını seçin. Dinamik içerik bölmesinden **Cihaz adı**' nı seçin. Bu değer, kullanıcının mobil uygulama aracılığıyla girdiği girişten geçirilir ve yeni cihazınızın IoT Central adıdır. Bu örnekte, tek gerekli alan, kırmızı yıldız işaretiyle belirtilen cihaz adıdır. Başka bir cihaz şablonunda yeni bir cihaz oluşturmak için doldurulması gereken birden çok gerekli alan olabilir.

    ![Flow cihaz eylemi oluşturma dinamik bölmesi](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. Seçim Yeni cihaz oluşturma için uygun gördüğünüz gibi diğer alanları da doldurabilirsiniz.

1. Son olarak, iş akışınızı kaydedin.

1. Microsoft Flow mobil uygulamasında iş akışınızı deneyin. Uygulamadaki **düğmeler** sekmesine gidin. Yeni cihaz iş akışı oluşturmak > Düğinizi görmeniz gerekir. Yeni cihazınızın adını girin ve IoT Central ' de göster ' i izleyin!

    ![Flow cihaz mobil ekran görüntüsü](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>İş akışındaki bir cihazı güncelleştirme

Bu bölümde, mobil cihazda Microsoft Flow mobil uygulamayı kullanarak bir düğmenin IoT Central içindeki cihaz ayarlarının ve özelliklerinin nasıl güncelleşmesi gösterilmektedir.

1. Microsoft Flow ' de boş bir iş akışı oluşturarak başlayın.

1. Bir tetikleyici olarak **Mobil Için akış düğmesini** arayın.

1. Bu tetikleyicide, değiştirmek istediğiniz bir ayara veya özelliğe karşılık gelen bir **konum** metin girişi gibi bir giriş ekleyin. Açıklama metnini değiştirin.

1. Yeni bir eylem ekleyin. **Azure IoT Central arama-bir cihaz eylemini güncelleştirme** .

1. Açılan listeden uygulamanızı seçin. Şimdi, güncelleştirmek istediğiniz mevcut bir cihazın KIMLIĞI gerekir. 

    > [!NOTE] 
    > Güncelleştirmek istediğiniz cihazın cihaz ayrıntıları sayfasında **URL 'de bulunan kimliği kullanmanız gerekir** . Cihaz Gezgini 'nin cihaz listesinde bulunan cihaz KIMLIĞI, Microsoft Flow ' de kullanılacak doğru değil.

    ![URL 'den IoT Central KIMLIĞI](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Cihaz adını güncelleştirebilirsiniz. Cihazların özelliklerini ve ayarlarını güncelleştirmek için **cihaz şablonu** açılan menüsünde güncelleştirmek istediğiniz cihazın cihaz şablonunu seçmeniz gerekir. Eylem kutucuğu, güncelleştirebilmeniz için tüm özellikleri ve ayarları gösterecek şekilde genişler.

    ![Akış güncelleştirme cihaz iş akışı](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Güncelleştirmek istediğiniz özelliklerin ve ayarların her birini seçin. Dinamik içerik bölmesinden, tetikleyiciden ilgili girişi seçin. Bu örnekte, cihazın Location özelliğini güncelleştirmek için konum değeri aşağı dağıtılır.

1. Son olarak, iş akışınızı kaydedin.

1. Microsoft Flow mobil uygulamasında iş akışınızı deneyin. Uygulamadaki **düğmeler** sekmesine gidin. Bir cihaz iş akışını güncelleştirmeniz > görmeniz gerekir. Girişleri girin ve IoT Central cihazda güncelleştirilmiş ' ı görün!

## <a name="get-device-information-in-a-workflow"></a>İş akışında cihaz bilgilerini al

**Azure IoT Central-cihaz al** eylemini kullanarak CIHAZ bilgilerini kimliğine göre alabilirsiniz. 
> [!NOTE] 
> Güncelleştirmek istediğiniz cihazın cihaz ayrıntıları sayfasında **URL 'de bulunan kimliği kullanmanız gerekir** . Cihaz Gezgini 'nin cihaz listesinde bulunan cihaz KIMLIĞI, Microsoft Flow ' de kullanılacak doğru değil.

İş akışınızda sonraki eylemlere geçirilecek cihaz adı, cihaz şablonu adı, özellik değerleri ve ayar değerleri gibi bilgileri alabilirsiniz. Bir cihazdan Microsoft ekiplerine müşteri adı özellik değeri geçen örnek bir iş akışı aşağıda verilmiştir.

   ![Flow cihaz iş akışını al](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Bir iş akışındaki cihazda bir komut çalıştırma
**Azure IoT Central** 'YI kullanarak kimliği tarafından belirtilen bir cihazda komutu çalıştırabilirsiniz. komut eylemi çalıştırın. 

> [!NOTE] 
> Güncelleştirmek istediğiniz cihazın cihaz ayrıntıları sayfasında **URL 'de bulunan kimliği kullanmanız gerekir** . Cihaz Gezgini 'nin cihaz listesinde bulunan cihaz KIMLIĞI, Microsoft Flow ' de kullanılacak doğru değil.
    
Bu eylem aracılığıyla komutun parametrelerini çalıştırmak ve geçirmek için komutu seçebilirsiniz. İşte Microsoft Flow Mobile App 'teki bir düğmeden cihaz yeniden başlatma komutunu çalıştıran örnek bir iş akışı.

   ![Flow cihaz iş akışını al](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>İş akışındaki bir cihazı silme

**Azure IoT Central-cihaz silme** eylemini kullanarak BIR cihazı kimliğine göre silebilirsiniz. 
> [!NOTE] 
> Güncelleştirmek istediğiniz cihazın cihaz ayrıntıları sayfasında **URL 'de bulunan kimliği kullanmanız gerekir** . Cihaz Gezgini 'nin cihaz listesinde bulunan cihaz KIMLIĞI, Microsoft Flow ' de kullanılacak doğru değil.

İşte Microsoft Flow Mobile App 'teki bir düğmenin göndermesinden bir cihazı silen örnek bir iş akışı.

   ![Akış silme cihaz iş akışı](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Sorun giderme

Azure IoT Central Bağlayıcısı bağlantısı oluştururken sorun yaşıyorsanız, size yardımcı olacak bazı ipuçları aşağıda verilmiştir.

1. Microsoft kişisel hesapları (@hotmail.com, @live.com, @outlook.com etki alanları) Şu anda desteklenmiyor. Azure Active Directory (AD) iş veya okul hesabı kullanmanız gerekir.

2. Microsoft Flow IoT Central bağlayıcısını kullanmak için, IoT Central uygulamasında en az bir kez oturum açmış olmanız gerekir. Aksi takdirde uygulama, uygulama açılan penceresinde görünmez.

3. Bir Azure AD hesabı kullanırken bir hata alıyorsanız, Windows PowerShell 'i açmayı deneyin ve yönetici olarak aşağıdaki komutu çalıştırın.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Sonraki adımlar

İş akışları oluşturmak için Microsoft Flow kullanmayı öğrendiğinize göre, önerilen sonraki adım [cihazları yönetmektedir](howto-manage-devices.md).

