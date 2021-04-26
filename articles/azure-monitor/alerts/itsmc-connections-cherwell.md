---
title: BT Hizmet Yönetimi Bağlayıcısı ile Cherwell bağlama
description: Bu makalede, ıTSM iş öğelerini merkezi olarak izlemek ve yönetmek için Azure Izleyici 'de BT Hizmet Yönetimi Bağlayıcısı (ITSMC) ile nasıl Iyi bir şekilde kullanılacağı hakkında bilgi sağlanır.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 0f488b047cf1323619d9603204877a55b413c295
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802480"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>BT Hizmet Yönetimi Bağlayıcısı ile Cherwell bağlama

Bu makalede, iş öğelerinizi merkezi olarak yönetmek için Log Analytics Cherwell örneğiniz ile BT Hizmet Yönetimi Bağlayıcısı (ITSMC) arasındaki bağlantının nasıl yapılandırılacağı hakkında bilgi sağlanır.

> [!NOTE]
> Azure uyarısı ile 1 Ekim 2020 Cherwell ıSM tümleştirmesi artık yeni müşteriler için etkinleştirilmeyecektir. Yeni ıTSM bağlantıları desteklenecek.
> Mevcut ıTSM bağlantıları desteklenecek.

Aşağıdaki bölümlerde, Cherwell ürününüzü Azure 'daki ıSMC 'a bağlama hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki önkoşulların karşılandığından emin olun:

- ISMC yüklendi. Daha fazla bilgi: [BT hizmet yönetimi Bağlayıcısı çözümü ekleme](./itsmc-definition.md#add-it-service-management-connector).
- İstemci KIMLIĞI oluşturuldu. Daha fazla bilgi: [Cherwell için ISTEMCI kimliği oluşturma](#generate-client-id-for-cherwell).
- Kullanıcı rolü: yönetici.

## <a name="connection-procedure"></a>Bağlantı yordamı

Bir Cherwell bağlantısı oluşturmak için aşağıdaki yordamı kullanın:

1. Azure portal ' de **tüm kaynaklar** ' a gidin ve **ServiceDesk (yourçalışmaalanıadı)** araması yapın

2. **Çalışma alanı VERI kaynakları** altında **ITSM bağlantıları**' na tıklayın.
    ![Yeni bağlantı](/azure/azure-monitor/alerts/media/itsmc-connections-scsm/add-new-itsm-connection.png)

3. Sağ bölmenin üst kısmındaki **Ekle**' ye tıklayın.

4. Aşağıdaki tabloda açıklandığı gibi bilgileri sağlayın ve bağlantıyı oluşturmak için **Tamam** ' ı tıklatın.

> [!NOTE]
> Tüm bu parametreler zorunludur.

| **Alan** | **Açıklama** |
| --- | --- |
| **Bağlantı adı**   | ISMC 'a bağlamak istediğiniz Cherwell örneği için bir ad yazın.  Bu adı daha sonra bu ıTSM 'de iş öğelerini yapılandırdığınızda ve ayrıntılı Log Analytics 'i görüntülemek için kullanabilirsiniz. |
| **İş ortağı türü**   | **Cherwell** öğesini seçin. |
| **Kullanıcı adı**   | ISMC 'a bağlanabilecek Cherwell Kullanıcı adını yazın. |
| **Parola**   | Bu kullanıcı adıyla ilişkili parolayı yazın. **Note:** Kullanıcı adı ve parola yalnızca kimlik doğrulama belirteçleri oluşturmak için kullanılır ve ıSMC hizmetinin içinde herhangi bir yerde depolanmaz.|
| **Sunucu URL 'SI**   | ISMC 'a bağlamak istediğiniz Cherwell örneğinizin URL 'sini yazın. |
| **İstemci Kimliği**   | Bu bağlantının kimliğini doğrulamak için, Cherwell örneğiniz içinde oluşturduğunuz istemci KIMLIĞINI yazın.   |
| **Veri eşitleme kapsamı**   | ITSMC aracılığıyla eşitlemek istediğiniz Cherwell iş öğelerini seçin.  Bu iş öğeleri Log Analytics 'e aktarılır.   **Seçenekler:**  Olaylar, değişiklik Istekleri. |
| **Verileri eşitleme** | Verilerin içinden istediğiniz geçmiş gün sayısını yazın. **Maksimum sınır**: 120 gün. |
| **ITSM çözümünde yeni yapılandırma öğesi oluştur** | ITSM ürününde yapılandırma öğelerini oluşturmak istiyorsanız bu seçeneği belirleyin. Seçildiğinde, ıSMC, desteklenen ıTSM sisteminde, etkilenen CIS 'yi yapılandırma öğeleri (mevcut olmayan CIS olması durumunda) olarak oluşturur. **Varsayılan**: devre dışı. |

![Cherwell bağlantısı](media/itsmc-connections-cherwell/itsm-connections-cherwell-latest.png)

**Başarıyla bağlandığında ve eşitlendiğinde**:

- Bu Cherwell örneğinden seçilen iş öğeleri Azure Log Analytics içeri aktarılmaktadır **.** BT Hizmet Yönetimi Bağlayıcısı kutucuğunda bu iş öğelerinin özetini görüntüleyebilirsiniz.

- Log Analytics uyarılarından veya günlük kayıtlarından veya bu Cherwell örneğindeki Azure uyarılarından olaylar oluşturabilirsiniz.

Daha fazla bilgi: [Azure uyarılarından ITSM iş öğeleri oluşturun](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Cherwell için istemci KIMLIĞI oluşturma

Cherwell için istemci KIMLIĞINI/anahtarını oluşturmak için aşağıdaki yordamı kullanın:

1. Yönetici olarak Cherwell örneğiniz üzerinde oturum açın.
2.   >  **İstemci ayarları REST API Güvenlik düzenleme**' ye tıklayın.
3. **Yeni istemci parolası oluştur**' u seçin  >  .

    ![Cherwell Kullanıcı kimliği](media/itsmc-connections-cherwell/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>Sonraki adımlar

* [ITSM Bağlayıcısına Genel Bakış](itsmc-overview.md)
* [Azure uyarılarından ıTSM iş öğeleri oluşturma](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [ITSM Bağlayıcısındaki sorunları giderme](./itsmc-resync-servicenow.md)
