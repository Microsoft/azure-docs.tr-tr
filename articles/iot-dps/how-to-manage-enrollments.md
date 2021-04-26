---
title: Azure IoT Hub cihaz sağlama hizmeti için cihaz kayıtlarını yönetme Azure portal
description: Azure portal cihaz sağlama hizmetiniz (DPS) için cihaz kayıtlarını yönetme
author: wesmc7777
ms.author: wesmc
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: 6ec146a05df1b896f8ca594d29cf13341b70765a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010971"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Azure portal cihaz kayıtlarını yönetme

Bir *cihaz kaydı* , bazı noktaları Azure IoT Hub cihaz sağlama hizmeti 'ne kaydedebileceği tek bir cihazın veya cihaz grubunun bir kaydını oluşturur. Kayıt kaydı, bu kaydın bir parçası olarak cihaz (ler) için ilk yapılandırmayı içerir. Yapılandırmaya dahil edilen IoT Hub 'ı, bir cihazın atanacağı bir dağıtım ilkesine veya hub 'ı bir hub kümesinden yapılandıran bir ayırma ilkesine eklenmiştir. Bu makalede, sağlama hizmetiniz için cihaz kayıtlarını yönetme işlemi gösterilmektedir.


## <a name="create-a-device-enrollment"></a>Cihaz kaydı oluşturma

Cihazları sağlama hizmeti ile kaydedebilmeniz için iki yol vardır:

* **Kayıt grubu** , ortak bir kanıtlama mekanizmasını paylaşan bir cihaz grubu için giriştir. Bir başlangıç yapılandırmasını paylaşan çok sayıda cihaz için veya hepsi aynı kiracıya giden cihazlar için bir kayıt grubu kullanmanızı öneririz. [Simetrik anahtar](concepts-symmetric-key-attestation.md) veya [X. 509.440 sertifikaları](concepts-x509-attestation.md) kullanan cihazlar desteklenir. 

    Simetrik Anahtarlarla kayıt grupları oluşturma ve kullanma hakkında adım adım yönergeler için bkz. [simetrik anahtarlarla cihazları sağlama](how-to-legacy-device-symm-key.md) öğreticisi.

    Aşağıdaki adımları kullanarak bir cihaz grubu için portalda bir kayıt grubu oluşturursunuz:

    1. Azure portal oturum açın ve sol taraftaki menüden **tüm kaynaklar** ' a tıklayın.  
    1. Cihazınızı kaynak listesinden kaydetmek istediğiniz cihaz sağlama hizmeti ' ne tıklayın.  
    1. Sağlama hizmetinizde kayıtları **Yönet**' e tıklayın ve ardından en üstteki **kayıt grubu Ekle** düğmesine tıklayın.  
     
        ![Portalda kayıt grubu](./media/how-to-manage-enrollments/add-group-enrollment.png)
        
    1. "Kayıt grubu Ekle" paneli göründüğünde, kaydınız için bilgileri girin ve **Kaydet**' e tıklayın.  
     
        [![Portala bir kayıt grubu ekleme](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
        
        | Alan | Açıklama |
        | :--- | :--- |
        | **Grup adı** | Cihaz grubunuz için gereken ad. |
        | **Kanıtlama türü** | Cihazlarınızın kullanacağı kanıtlama yöntemine bağlı olarak kanıtlama türü için **sertifika** veya **simetrik anahtar** ' a tıklayın. |
        | **Sertifika türü** | Sertifika kanıtlama kullanıyorsanız kullanılabilir. cihaz sertifikalarınızın hangi sertifikaya imzalandığını bağlı olarak **CA sertifikası** veya **Ara** ' yı seçin. |
        | **Birincil sertifika** | Cihaz sertifikalarınızı bir kök CA sertifikası ile imzalandıysanız, bu kök CA sertifikasında birlikte bulunan [elinde](how-to-verify-certificates.md) bulunan sahip olması gerekir. Daha sonra bunu cihaz grubu için **birincil sertifika** olarak seçebilirsiniz.<br><br>Cihaz sertifikalarınızı bir ara sertifikayla imzalandıysanız ara sertifikanızı karşıya yüklemenizi sağlamak için karşıya yükleme düğmesi kullanılabilir. Ara ' yı imzalayan sertifikanın Ayrıca, BT için de sahip olma [kanıtı](how-to-verify-certificates.md) olması gerekir. |

        
    

* **Tek bir kayıt** , bir IoT Hub 'ına atanabilecek tek bir cihaz için bir giriştir. [Simetrik anahtar](concepts-symmetric-key-attestation.md), [X. 509.440 sertifikaları](concepts-x509-attestation.md)ve [TPM kanıtlama](concepts-tpm-attestation.md) kullanan cihazlar desteklenir. 

    Aşağıdaki adımları kullanarak portalda tek bir kayıt oluşturabilirsiniz:

    1. Azure portal oturum açın ve sol taraftaki menüden **tüm kaynaklar** ' a tıklayın.
    1. Cihazınızı kaynak listesinden kaydetmek istediğiniz cihaz sağlama hizmeti ' ne tıklayın.
    1. Sağlama hizmetinizde kayıtları **Yönet**' e tıklayın, ardından üst kısımdaki **tek kayıt Ekle** düğmesine tıklayın.   

       [![Portalda bireysel kayıt ekleme](./media/how-to-manage-enrollments/add-individual-enrollment.png)](./media/how-to-manage-enrollments/add-individual-enrollment.png#lightbox)

    1. "Kayıt Ekle" paneli göründüğünde, tek tek cihaz kaydı için bilgileri girin ve **Kaydet**' e tıklayın. 
     
        [![Portalda bireysel kayıt](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)
    
        | Alan | Açıklama |
        | :--- | :--- |
        | **Mechanism** | , Cihazlarınızın kullanacağı kanıtlama yöntemine bağlı olarak kullanılacak kanıtlama mekanizması için **X. 509.952**, **TPM** veya **simetrik anahtar** seçin. |
        | Kanıtlama ayarları | Simetrik Anahtarlarla bireysel kayıtlar oluşturma ve kullanma hakkında adım adım yönergeler için, [simetrik bir cihaz sağlama](quick-create-simulated-device-symmetric-key-java.md#create-a-device-enrollment) veya [x. 509.440 sertifika cihazı sağlama](quick-create-simulated-device-x509-java.md#create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry) hızlı başlangıçlarından birine bakın.<br><br>TPM kanıtlama kullanarak bireysel kayıtları oluşturmaya ve kullanmaya yönelik adım adım yönergeler için, [sanal BIR TPM cihazı sağlama](quick-create-simulated-device-tpm-java.md#create-a-device-enrollment-entry) örneklerinden birine bakın.|
        | **IoT Hub cihaz KIMLIĞI** |  Bu KIMLIK cihazınızı temsil eder. Cihaz KIMLIĞI için kurallara uymalıdır. Daha fazla bilgi için bkz. [cihaz kimliği özellikleri](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).<br><br>X. 509.440 sertifikalarını kullanırken, bu metin kayıt için karşıya yüklediğiniz cihaz sertifikasındaki konu adı olmalıdır. Bu konu adının bir cihaz KIMLIĞI için kurallara uyması gerekir.|
            


## <a name="update-an-enrollment-entry"></a>Kayıt girişini güncelleştirme
Portalda mevcut bir kayıt girişini aşağıdaki adımları kullanarak güncelleştirebilirsiniz:

1. Cihaz sağlama hizmetinizi Azure portal açın ve kayıtları **Yönet**' e tıklayın. 
1. Değiştirmek istediğiniz kayıt girdisine gidin. Cihaz kaydınız hakkında özet bilgiler açan girişe tıklayın. 
1. Bu sayfada, cihazın bağlanması gereken IoT Hub 'ı ve cihaz KIMLIĞI gibi güvenlik türü ve kimlik bilgileri dışındaki öğeleri değiştirebilirsiniz. Başlangıçtaki cihaz ikizi durumunu da değiştirebilirsiniz. 
1. Tamamlandıktan sonra, cihaz kaydınız güncelleştirmek için **Kaydet** ' e tıklayın. 

    ![Portalda kaydı güncelleştir](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Cihaz kaydını kaldırma
Cihazınızın herhangi bir IoT Hub 'ına sağlanması gerekmeyen durumlarda, aşağıdaki adımları kullanarak portalda ilgili kayıt girişini kaldırabilirsiniz:

1. Cihaz sağlama hizmetinizi Azure portal açın ve kayıtları **Yönet**' e tıklayın. 
1. ' A gidin ve kaldırmak istediğiniz kayıt girişini seçin. 
1. Üstteki **Sil** düğmesine tıklayın ve ardından onaylamanız istendiğinde **Evet** ' i seçin. 
1. Eylem tamamlandığında, girdinizi cihaz kayıtları listesinden kaldırıldığını görürsünüz. 
 
    ![Portalda kaydı kaldırma](./media/how-to-manage-enrollments/remove-enrollment.png)