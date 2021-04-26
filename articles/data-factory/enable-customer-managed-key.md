---
title: Müşteri tarafından yönetilen anahtarla Azure Data Factory şifreleyin
description: Kendi Anahtarını Getir Data Factory güvenliği geliştirme (BYOK)
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: a18d06e3a0324889a4cb9936fb339fd9d8f9b816
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222716"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlarla Azure Data Factory şifreleyin

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory, verileri bekleyen veri, varlık tanımları ve çalışma sırasında önbelleğe alınan tüm veriler de dahil olmak üzere şifreler. Varsayılan olarak, veriler, veri fabrikanıza benzersiz olarak atanmış rastgele oluşturulmuş bir Microsoft tarafından yönetilen anahtarla şifrelenir. Ek güvenlik garantisi için artık Azure Data Factory içindeki müşteri tarafından yönetilen anahtarlar özelliğiyle Kendi Anahtarını Getir (BYOK) seçeneğini etkinleştirebilirsiniz. Müşteri tarafından yönetilen bir anahtar belirttiğinizde Data Factory, müşteri verilerini şifrelemek için hem fabrika sistem anahtarını hem __de__ CMK 'yi kullanır. Eksik olan iki durum da veri ve fabrika erişimine Izin vermez.

Müşteri tarafından yönetilen anahtarları depolamak için Azure Key Vault gereklidir. Kendi anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasında saklayabilir veya Azure Key Vault API 'Lerini kullanarak anahtarlar oluşturabilirsiniz. Anahtar Kasası ve Data Factory aynı Azure Active Directory (Azure AD) kiracısında ve aynı bölgede olmalıdır, ancak farklı aboneliklerde olabilir. Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault nedir?](../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlar hakkında

Aşağıdaki diyagramda, Data Factory, müşteri tarafından yönetilen anahtarı kullanarak istek yapmak için Azure Active Directory ve Azure Key Vault nasıl kullandığı gösterilmektedir:

  :::image type="content" source="media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png" alt-text="Müşteri tarafından yönetilen anahtarların Azure Data Factory içinde nasıl çalıştığını gösteren diyagram.":::

Aşağıdaki listede, diyagramdaki numaralandırılmış adımlar açıklanmaktadır:

1. Azure Key Vault Yöneticisi, şifreleme anahtarlarına Data Factory ilişkili yönetilen kimliğe izinler verir
1. Data Factory Yöneticisi, fabrikada müşterinin yönettiği anahtar özelliğini sunar
1. Data Factory, Azure Key Vault erişimi Azure Active Directory aracılığıyla doğrulamak için fabrika ile ilişkili yönetilen kimliği kullanır
1. Data Factory, fabrika şifreleme anahtarını Azure Key Vault müşteri anahtarıyla sarar
1. Okuma/yazma işlemleri için Data Factory, şifreleme ve şifre çözme işlemleri gerçekleştirmek üzere hesap şifreleme anahtarını sarmalamak için Azure Key Vault istekleri gönderir

Veri fabrikaları için müşteri tarafından yönetilen anahtar şifrelemesi eklemenin iki yolu vardır. Birisi Azure portal fabrika oluşturma zamanı sırasında ve diğeri fabrika sonrası oluşturma, Data Factory Kullanıcı arabiriminde.

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Önkoşullar-Azure Key Vault yapılandırma ve anahtarları oluşturma

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Azure Key Vault geçici silme özelliğini etkinleştirme ve Temizleme

Data Factory ile müşteri tarafından yönetilen anahtarların kullanılması Key Vault, __geçici silme__ ve __Temizleme__ için iki özellik ayarlanmasını gerektirir. Bu özellikler, yeni veya mevcut bir anahtar kasasında PowerShell veya Azure CLı kullanılarak etkinleştirilebilir. Bu özellikleri var olan bir anahtar kasasında nasıl etkinleştireceğinizi öğrenmek için bkz. [Azure Key Vault kurtarma yönetimi, geçici silme ve Temizleme koruması](../key-vault/general/key-vault-recovery.md)

Azure portal aracılığıyla yeni bir Azure Key Vault oluşturuyorsanız, __geçici silme__ ve __Temizleme işlemi__ şu şekilde etkinleştirilebilir:

  :::image type="content" source="media/enable-customer-managed-key/01-enable-purge-protection.png" alt-text="Key Vault oluşturulduktan sonra geçici silme ve Temizleme korumasının nasıl etkinleştirileceğini gösteren ekran görüntüsü.":::

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Azure Key Vault Data Factory erişim izni verin

Azure Key Vault ve Azure Data Factory aynı Azure Active Directory (Azure AD) kiracısında ve _aynı bölgede_ bulunduğundan emin olun. Azure Key Vault Access Control 'tan, Data Factory 'yi şu izinlerle verin: _Get_, _sarmalama tuşu_ ve _sarmalama tuşu_. Bu izinler, Data Factory ' de müşteri tarafından yönetilen anahtarları etkinleştirmek için gereklidir.

* [Data Factory Kullanıcı arabiriminde fabrika oluşturulduktan sonra](#post-factory-creation-in-data-factory-ui)müşteri tarafından yönetilen anahtar şifrelemesi eklemek istiyorsanız, Data Factory 'nin yönetilen hizmet KIMLIĞININ (msı) Key Vault için üç izni olduğundan emin olun
* [Azure Portal fabrika oluşturma süresi boyunca](#during-factory-creation-in-azure-portal)müşteri tarafından yönetilen anahtar şifrelemesi eklemek istiyorsanız, Kullanıcı tarafından atanan yönetilen KIMLIğIN (UA-mı) Key Vault için üç izni olduğundan emin olun

  :::image type="content" source="media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png" alt-text="Key Vault Data Factory erişimin nasıl etkinleştirileceğini gösteren ekran görüntüsü.":::

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Azure Key Vault için müşteri tarafından yönetilen anahtar oluştur veya karşıya yükle

Kendi anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasına kaydedebilirsiniz. Veya anahtarlar oluşturmak için Azure Key Vault API 'Lerini kullanabilirsiniz. Data Factory şifreleme ile yalnızca 2048 bitlik RSA anahtarları desteklenir. Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](../key-vault/general/about-keys-secrets-certificates.md).

  :::image type="content" source="media/enable-customer-managed-key/03-create-key.png" alt-text="Customer-Managed anahtarın nasıl oluşturulacağını gösteren ekran görüntüsü.":::

## <a name="enable-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları etkinleştir

### <a name="post-factory-creation-in-data-factory-ui"></a>Data Factory Kullanıcı arabiriminde fabrika sonrası oluşturma

Bu bölümde, fabrika oluşturulduktan _sonra_ Data Factory Kullanıcı arabiriminde müşteri tarafından yönetilen anahtar şifrelemesini ekleme işlemi gösterilmektedir.

> [!NOTE]
> Müşteri tarafından yönetilen anahtar yalnızca boş bir veri fabrikasında yapılandırılabilir. Data Factory, bağlı hizmetler, işlem hatları ve veri akışları gibi kaynaklar içeremez. Üretici oluşturulduktan sonra müşterinin yönettiği anahtarı etkinleştirmeniz önerilir.

> [!IMPORTANT]
> Bu yaklaşım, yönetilen sanal ağ etkin fabrikalar ile çalışmaz. Bu tür fabrikaları şifrelemek istiyorsanız lütfen [alternatif yolu](#during-factory-creation-in-azure-portal)göz önünde bulundurun.

1. Data Factory 'nin Yönetilen Hizmet Kimliği (MSI), Key Vault için _Al_, _sarmalama anahtarı_ ve _sarmalama_ izinleri içerdiğinden emin olun.

1. Data Factory boş olduğundan emin olun. Data Factory, bağlı hizmetler, işlem hatları ve veri akışları gibi kaynaklar içeremez. Şimdilik, müşteri tarafından yönetilen anahtarı boş olmayan bir fabrikada dağıtmak hataya neden olur.

1. Azure portal anahtar URI 'sini bulmak için Azure Key Vault ' a gidin ve anahtarlar ayarını seçin. İstenen anahtarı seçin ve ardından sürümlerini görüntülemek için anahtarı seçin. Ayarları görüntülemek için bir anahtar sürüm seçin

1. :::image type="content" source="media/enable-customer-managed-key/04-get-key-identifier.png" alt-text="Key Vault anahtar URI 'sini alma URI ekran görüntüsünü"::: sağlayan anahtar tanımlayıcı alanının değerini kopyalayın.

1. Azure Data Factory portalını başlatın ve sol taraftaki Gezinti çubuğunu kullanarak Data Factory ' e atlayın Yönetim Portalı

1. __Müşterinin yönettiği anahtar__ simgesine tıklayın :::image type="content" source="media/enable-customer-managed-key/05-customer-managed-key-configuration.png" alt-text="Data Factory Kullanıcı arabiriminde müşteri tarafından yönetilen anahtarı etkinleştirme ekran görüntüsü.":::

1. Daha önce kopyaladığınız müşteri tarafından yönetilen anahtar için URI girin

1. __Kaydet__ ' e tıklayın, Data Factory için müşteri tarafından yönetilen anahtar şifrelemesi etkinleştirilmiştir

### <a name="during-factory-creation-in-azure-portal"></a>Azure portal içinde fabrika oluşturma sırasında

Bu bölümde, fabrika dağıtımı _sırasında_ Azure Portal, müşteri tarafından yönetilen anahtar şifrelemesini ekleme adımları gösterilmektedir.

Fabrikası şifrelemek için, Data Factory öncelikle müşteri tarafından yönetilen anahtarı Key Vault alması gerekir. Fabrika dağıtımı hala devam ettiğinden Yönetilen Hizmet Kimliği (MSI) Key Vault kimlik doğrulaması için henüz kullanılamıyor. Bu yaklaşımı kullanmak için, müşterinin, Data Factory 'ye Kullanıcı tarafından atanan bir yönetilen kimlik (UA-mı) ataması gerekir. , UA-mı 'de tanımlanan rolleri kabul eteceğiz ve Key Vault kimlik doğrulaması yapacağız.

Kullanıcı tarafından atanan yönetilen kimlik hakkında daha fazla bilgi edinmek için bkz. [yönetilen kimlik türleri](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) ve [rol ataması Kullanıcı tarafından atanan yönetilen kimlik](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Kullanıcı tarafından atanan yönetilen kimliğin (UA-mı), Key Vault için anahtar _Al_ ve  _sarmalama_ anahtarı izinlerini içerdiğinden emin olun

1. __Gelişmiş__ sekmesinde, Azure Portal 'de veri fabrikası oluşturma deneyimine yönelik gelişmiş sekmesinin _müşteri tarafından yönetilen anahtar ekran görüntüsünü kullanarak şifrelemeyi etkinleştir_ kutusunu işaretleyin 
   :::image type="content" source="media/enable-customer-managed-key/06-user-assigned-managed-identity.png" alt-text=".":::

1. Key Vault depolanan müşterinin yönettiği anahtarın URL 'sini sağlayın

1. Key Vault kimlik doğrulaması için uygun bir kullanıcı tarafından atanan yönetilen kimlik seçin

1. Fabrika dağıtımına devam et

## <a name="update-key-version"></a>Anahtar sürümünü Güncelleştir

Bir anahtarın yeni bir sürümünü oluşturduğunuzda, Data Factory 'yi yeni sürümü kullanacak şekilde güncelleştirin. Aşağıdaki gibi, bölüm [Data Factory Kullanıcı arabiriminde](#post-factory-creation-in-data-factory-ui)açıklanan benzer adımları izleyin:

1. Yeni anahtar sürümünün URI 'sini Azure Key Vault portalından bulun

1. __Müşteri tarafından yönetilen anahtar__ ayarına git

1. Yeni anahtar için URI 'yi değiştirin ve yapıştırın

1. __Kaydet__ ' e tıklayın ve Data Factory şimdi yeni anahtar sürümüyle şifrelenir

## <a name="use-a-different-key"></a>Farklı bir anahtar kullanın

Data Factory şifreleme için kullanılan anahtarı değiştirmek için, Data Factory ayarları el ile güncelleştirmeniz gerekir. Aşağıdaki gibi, bölüm [Data Factory Kullanıcı arabiriminde](#post-factory-creation-in-data-factory-ui)açıklanan benzer adımları izleyin:

1. Azure Key Vault Portal aracılığıyla yeni anahtar için URI 'yi bulma

1. Müşteri tarafından yönetilen __anahtar__ ayarına git

1. Yeni anahtar için URI 'yi değiştirin ve yapıştırın

1. __Kaydet__ ' e tıklayın ve Data Factory şimdi yeni anahtarla şifreleyeceksiniz

## <a name="disable-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları devre dışı bırak

Tasarıma göre, müşteri tarafından yönetilen anahtar özelliği etkinleştirildikten sonra, ek güvenlik adımını kaldıramazsınız. Fabrika ve verileri şifrelemek için her zaman bir müşteri tarafından sağlanmış anahtar beklenir.

## <a name="customer-managed-key-and-continuous-integration-and-continuous-deployment"></a>Müşteri tarafından yönetilen anahtar ve sürekli tümleştirme ve sürekli dağıtım

Varsayılan olarak, CMK yapılandırması, Factory Azure Resource Manager (ARM) şablonuna dahil değildir. Müşteri tarafından yönetilen anahtar şifreleme ayarlarını, sürekli tümleştirme (CI/CD) için ARM şablonuna dahil etmek için:

1. Fabrikasının git modunda olduğundan emin olun
1. Yönetim Portalı ' na gidin-müşteri tarafından yönetilen anahtar bölümü
1. _ARM şablonuna dahil_ etme seçeneği

  :::image type="content" source="media/enable-customer-managed-key/07-include-in-template.png" alt-text="ARM şablonunda müşteri tarafından yönetilen anahtar ayarını dahil etme ekran görüntüsü.":::

ARM şablonuna aşağıdaki ayarlar eklenecektir. Bu özellikler, [Azure Resource Manager parametreleri yapılandırması](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template) düzenlenerek sürekli tümleştirme ve teslim işlem hatlarında parametrelenebilir.

  :::image type="content" source="media/enable-customer-managed-key/08-template-with-customer-managed-key.png" alt-text="Azure Resource Manager şablonunda müşteri tarafından yönetilen anahtar ayarını dahil etme ekran görüntüsü.":::

> [!NOTE]
> ARM şablonlarına şifreleme ayarını eklemek, diğer ortamlarda git yapılandırması gibi diğer fabrika düzeyi ayarlarını geçersiz kılacak bir fabrika düzeyi ayarı ekler. Bu ayarları, UıAT veya PROD gibi yükseltilmiş bir ortamda etkinleştirdiyseniz, lütfen [CI/CD 'Deki genel parametrelere](author-global-parameters.md#cicd)bakın.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla senaryoda Data Factory’yi kullanma hakkında bilgi almak için [öğreticileri](tutorial-copy-data-dot-net.md) okuyun.
