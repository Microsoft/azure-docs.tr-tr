---
title: Kaydet ve Tara Azure Data Lake Storage (ADLS) Gen2
description: Bu öğreticide Azure Data Lake Storage 2. tarama açıklanmaktadır.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 4b7f71b5405708cc1988fafa5ca9c4628fe0d80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98882408"
---
# <a name="register-and-scan-azure-data-lake-storage-gen2"></a>Kaydet ve Tara Azure Data Lake Storage 2.

Bu makalede, Azure Data Lake Storage 2. Azure 'da veri kaynağı olarak nasıl kaydedileceği ve bir tarama ayarlandığı özetlenmektedir.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Azure Data Lake Storage 2. veri kaynağı aşağıdaki işlevleri destekler:

- Azure Data Lake Storage 2. meta verileri ve sınıflandırmayı yakalamak için **tam ve artımlı taramalar**

- ADF kopyası/veri akışı etkinlikleri için veri varlıkları arasında **kökenini**

## <a name="prerequisites"></a>Önkoşullar

Veri kaynaklarını kaydetmeden önce bir Azure purview hesabı oluşturun. Bir purview hesabı oluşturma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure purview hesabı oluşturma](create-catalog-portal.md).

### <a name="setting-up-authentication-for-a-scan"></a>Tarama için kimlik doğrulamasını ayarlama

Aşağıdaki kimlik doğrulama yöntemleri Azure Data Lake Storage 2. için desteklenir:

- Yönetilen Kimlik
- Hizmet sorumlusu
- Hesap anahtarı

#### <a name="managed-identity-recommended"></a>Yönetilen kimlik (önerilen)

Bağlantıyı kurmak için **yönetilen kimlik**' i seçtiğinizde, önce purview hesabınıza veri kaynağını taramak için izin vermeniz gerekir:

1. ADLS 2. depolama hesabınıza gidin.
1. Sol gezinti menüsünden **Access Control (IAM)** seçeneğini belirleyin. 
1. **+ Ekle**'yi seçin.
1. **Rolü** **Depolama Blobu veri okuyucusu** olarak ayarlayın ve giriş kutusunu **seçin** altında Azure purview hesabınızın adını girin. Ardından, bu rol atamasını purview hesabınıza vermek için **Kaydet** ' i seçin.

> [!Note]
> Daha fazla ayrıntı için lütfen [Azure Active Directory kullanarak bloblara ve kuyruklara erişim yetkisi verme](../storage/common/storage-auth-aad.md) adımları bölümüne bakın

#### <a name="account-key"></a>Hesap anahtarı

Kimlik doğrulama yöntemi seçili olduğunda **, anahtar** kasasında erişim anahtarınızı almanız ve depolamanız gerekir:

1. ADLS Gne2 Storage hesabınıza gidin
1. **Erişim anahtarlarına > ayarları** seçin
1. *Anahtarınızı* kopyalayın ve sonraki adımlar için bir yere kaydedin
1. Anahtar kasanıza gidin
1. **Gizli dizileri > ayarları** seçin
1. **+ Oluştur/Içeri aktar** ' ı seçin ve depolama hesabınızdan *anahtar* olarak **ad** ve **değer** girin
1. Tamamlanacak **Oluştur** ' u seçin
1. Anahtar Kasanızda purview 'a bağlı değilse, [Yeni bir Anahtar Kasası bağlantısı oluşturmanız](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) gerekir
1. Son olarak, taramayı kurmak için anahtarı kullanarak [Yeni bir kimlik bilgisi oluşturun](manage-credentials.md#create-a-new-credential)

#### <a name="service-principal"></a>Hizmet sorumlusu

Hizmet sorumlusu kullanmak için mevcut bir tane kullanabilir veya yeni bir tane oluşturabilirsiniz. 

> [!Note]
> Yeni bir hizmet sorumlusu oluşturmanız gerekiyorsa lütfen şu adımları izleyin:
> 1. [Azure Portal](https://portal.azure.com)gidin.
> 1. Sol taraftaki menüden **Azure Active Directory** ' yi seçin.
> 1. **Uygulama kayıtları**’nı seçin.
> 1. **+ Yeni uygulama kaydı**' nı seçin.
> 1. **Uygulama** için bir ad girin (hizmet asıl adı).
> 1. **Yalnızca bu kuruluş dizininde hesaplar '** ı seçin.
> 1. Yeniden yönlendirme URI 'SI için **Web** ' i seçin ve istediğiniz URL 'yi girin; gerçek veya iş olması gerekmez.
> 1. Ardından **Kaydet**’i seçin.

Hizmet sorumlusunun uygulama KIMLIĞI ve gizli anahtarı almak için gereklidir:

1. [Azure Portal](https://portal.azure.com) hizmet sorumlusuna gidin
1. **Uygulama (istemci) kimliğinin** değerlerini, **Sertifikalar & Gizliliklerden** **genel bakış** ve **istemci gizliliğine** kopyalayın.
1. Anahtar kasanıza gidin
1. **Gizli dizileri > ayarları** seçin
1. **+ Oluştur/Içeri aktar** ' ı seçin ve seçtiğiniz **adı** ve **değeri** hizmet sorumlınızdan **istemci gizli anahtarı** olarak girin
1. Tamamlanacak **Oluştur** ' u seçin
1. Anahtar Kasanızda purview 'a bağlı değilse, [Yeni bir Anahtar Kasası bağlantısı oluşturmanız](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) gerekir
1. Son olarak, taramanızı ayarlamak için hizmet sorumlusunu kullanarak [Yeni bir kimlik bilgisi oluşturun](manage-credentials.md#create-a-new-credential)

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>ADLS Gen2 hesabınıza hizmet sorumlusu erişimi verme

1. Depolama hesabınıza gidin.
1. Sol gezinti menüsünden **Access Control (IAM)** seçeneğini belirleyin. 
1. **+ Ekle**'yi seçin.
1. **Rolü** **Depolama Blobu veri okuyucusu** olarak ayarlayın ve giriş kutusunu **seçin** altında hizmet asıl adınızı veya nesne kimliğinizi girin. Ardından, bu rol için hizmet sorumlusu atamasını sağlamak üzere **Kaydet** ' i seçin.
### <a name="firewall-settings"></a>Güvenlik duvarı ayarları

> [!NOTE]
> Depolama hesabı için güvenlik duvarınız etkinse, bir tarama ayarlarken **yönetilen kimlik** kimlik doğrulama yöntemini kullanmanız gerekir.

1. [Azure Portal](https://portal.azure.com) içindeki asls Gen2 Storage hesabınıza gidin
1. **Ayarlar > ağ iletişimi** ' ne gidin ve
1. **Erişime Izin ver** altında **Seçili ağları** seçin
1. **Özel durumlar** bölümünde, **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine izin ver** ' i seçin ve **Kaydet** ' i tıklayın

:::image type="content" source="./media/register-scan-adls-gen2/firewall-setting.png" alt-text="Güvenlik duvarı ayarını gösteren ekran görüntüsü":::

## <a name="register-azure-data-lake-storage-gen2-data-source"></a>Azure Data Lake Storage 2. veri kaynağını Kaydet

Yeni bir ADLS 2. hesabını veri kataloğunuza kaydetmek için aşağıdakileri yapın:

1. Purview hesabınıza gidin
2. Sol gezinti bölmesinde **kaynakları** seçin
3. **Kaydol** ' u seçin
4. **Kayıt kaynakları** üzerinde **Azure Data Lake Storage 2.** seçin
5. **Devam**'ı seçin

**Kaynakları Kaydet (Azure Data Lake Storage 2.)** ekranında şunları yapın:

1. Veri kaynağının katalogda listelenecek bir **ad** girin.
2. Depolama hesaplarını filtrelemek için aboneliğinizi seçin
3. Bir depolama hesabı seçin
4. Bir koleksiyon seçin veya yeni bir tane oluşturun (Isteğe bağlı)
5. Veri kaynağını kaydetmek için **son** ' a gidin.

:::image type="content" source="media/register-scan-adls-gen2/register-sources.png" alt-text="kaynakları kaydetme seçenekleri" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure purview veri kataloğuna gözatın](how-to-browse-catalog.md)
- [Azure purview Veri Kataloğu 'Nda arama](how-to-search-catalog.md)