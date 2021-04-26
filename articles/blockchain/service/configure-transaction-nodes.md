---
title: Azure blok zinciri hizmeti işlem düğümlerini yapılandırma
description: Azure blok zinciri hizmeti işlem düğümlerini yapılandırma
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 0b659fa9f15cd41e9e55de526747d470046cafa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091270"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Azure blok zinciri hizmeti işlem düğümlerini yapılandırma

İşlem düğümleri ortak bir uç nokta aracılığıyla Azure blok zinciri hizmetine blok zinciri işlemleri göndermek için kullanılır. Varsayılan işlem düğümü, blok zincirinde kayıtlı olan Ethereum hesabının özel anahtarını içerir ve bu nedenle silinemez.

Varsayılan işlem düğümü ayrıntılarını görüntülemek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Azure blok zinciri hizmeti üyesine gidin. **İşlem düğümlerini** seçin.

    ![Varsayılan işlem düğümünü seçin](./media/configure-transaction-nodes/nodes.png)

    Genel Bakış ayrıntıları genel uç nokta adreslerini ve ortak anahtarı içerir.

## <a name="create-transaction-node"></a>İşlem düğümü oluştur

Blok zinciri üyesine toplam 10 işlem düğümü için en fazla dokuz ek işlem düğümü ekleyebilirsiniz. İşlem düğümleri ekleyerek ölçeklenebilirliği artırabilir veya yükü dağıtabilirsiniz. Örneğin, farklı istemci uygulamaları için bir işlem düğümü uç noktasına sahip olabilirsiniz.

İşlem düğümü eklemek için:

1. Azure portal Azure blok zinciri hizmeti üyesine gidin ve **ekle > işlem düğümleri**' ni seçin.
1. Yeni işlem düğümü için ayarları doldurun.

    ![İşlem düğümü Ekle](./media/configure-transaction-nodes/add-node.png)

    | Ayar | Açıklama |
    |---------|-------------|
    | Ad | İşlem düğümü adı. Ad, işlem düğümü uç noktasının DNS adresini oluşturmak için kullanılır. Örneğin, `newnode-myblockchainmember.blockchain.azure.com`. Düğüm adı, oluşturulduktan sonra değiştirilemez. |
    | Parola | Güçlü bir parola ayarlayın. Temel kimlik doğrulamasıyla işlem düğümü uç noktasına erişmek için parolayı kullanın.

1. **Oluştur**’u seçin.

    Yeni bir işlem düğümü sağlanması yaklaşık 10 dakika sürer. Ek işlem düğümleri ücret doğurur. Maliyetler hakkında daha fazla bilgi için bkz. [Azure fiyatlandırması](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Uç Noktalar

İşlem düğümlerinin benzersiz bir DNS adı ve genel uç noktaları vardır.

İşlem düğümünün uç nokta ayrıntılarını görüntülemek için:

1. Azure portal Azure blok zinciri hizmeti üye işlem düğümlerinizin birine gidin ve **genel bakış**' ı seçin.

    ![Ekran yakalama, bir blok zinciri üyesinin işlem düğümlerine ilişkin genel bakışı gösterir.](./media/configure-transaction-nodes/endpoints.png)

İşlem düğümü uç noktaları güvenlidir ve kimlik doğrulaması gerekir. Azure AD kimlik doğrulaması, HTTPS temel kimlik doğrulaması ve HTTPS üzerinden bir erişim anahtarı veya TLS üzerinden WebSocket kullanarak bir işlem uç noktasına bağlanabilirsiniz.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory Access Control

Azure blok zinciri hizmeti işlem düğümü uç noktaları, Azure Active Directory (Azure AD) kimlik doğrulamasını destekler. Uç noktanız için Azure AD kullanıcısına, gruba ve hizmet sorumlusu erişimine izin verebilirsiniz.

Uç noktanıza Azure AD erişim denetimi sağlamak için:

1. Azure portal, Azure blok zinciri hizmeti üyesine gidin ve **işlem düğümleri > erişim denetimi (IAM) > ekleme > rol ataması Ekle**' yi seçin.
1. Kullanıcı, Grup veya hizmet sorumlusu (uygulama rolleri) için yeni bir rol ataması oluşturun.

    ![IAM rolü Ekle](./media/configure-transaction-nodes/add-role.png)

    | Ayar | Eylem |
    |---------|-------------|
    | Rol | **Sahip**, **katkıda bulunan** veya **Reader**' ı seçin.
    | Şunlara erişim ata: | **Azure AD Kullanıcı, Grup veya hizmet sorumlusu**' nı seçin.
    | Şunu seçin: | Eklemek istediğiniz kullanıcı, Grup veya hizmet sorumlusu için arama yapın.

1. Rol atamasını eklemek için **Kaydet** ' i seçin.

Azure AD erişim denetimi hakkında daha fazla bilgi için bkz [. Azure Portal kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md)

Azure AD kimlik doğrulamasını kullanarak bağlanma hakkında daha fazla bilgi için bkz. [AAD kimlik doğrulaması kullanarak düğümünüz bağlama](configure-aad.md).

### <a name="basic-authentication"></a>Temel kimlik doğrulama

HTTPS temel kimlik doğrulaması için, Kullanıcı adı ve parola kimlik bilgileri isteğin HTTPS üst bilgisinde uç noktaya geçirilir.

İşlem düğümünün temel kimlik doğrulama uç noktası ayrıntılarını Azure portal görüntüleyebilirsiniz. Azure blok zinciri hizmeti üye işlem düğümlerinizin birine gidin ve Ayarlar ' da **temel kimlik doğrulaması** ' nı seçin.

![Temel kimlik doğrulama](./media/configure-transaction-nodes/basic.png)

Kullanıcı adı, düğümünüz adıdır ve değiştirilemez.

URL 'YI kullanmak için, \<password\> düğüm sağlandığı zaman ayarlanan parolayla değiştirin. Parolayı **Sıfırla**' yı seçerek parolayı güncelleştirebilirsiniz.

### <a name="access-keys"></a>Erişim tuşları

Erişim anahtarı kimlik doğrulaması için, erişim anahtarı uç nokta URL 'sine dahildir. İşlem düğümü sağlandığında, iki erişim anahtarı oluşturulur. Kimlik doğrulaması için erişim anahtarı kullanılabilir. İki anahtar, anahtarları değiştirmenize ve döndürmenize olanak tanır.

İşlem düğümünün erişim anahtarı ayrıntılarını görüntüleyebilir ve erişim anahtarlarını içeren uç nokta adreslerini kopyalayabilirsiniz. Azure blok zinciri hizmeti üye işlem düğümlerinizin birine gidin ve Ayarlar ' da **erişim anahtarları** ' nı seçin.

### <a name="firewall-rules"></a>Güvenlik duvarı kuralları

Güvenlik duvarı kuralları, işlem düğümünüz için kimlik doğrulaması gerçekleştirmeye çalışmayan IP adreslerini sınırlamanıza olanak tanır.  İşlem düğümünüz için bir güvenlik duvarı kuralı yapılandırılmamışsa, hiçbir taraf tarafından erişilemez.  

İşlem düğümünün güvenlik duvarı kurallarını görüntülemek için Azure blok zinciri hizmeti üye işlem düğümlerinizin birine gidin ve Ayarlar ' da **güvenlik duvarı kuralları** ' nı seçin.

Güvenlik Duvarı **kuralları** kılavuzunda bir kural adı, başlangıç IP adresi ve bitiş IP adresi girerek güvenlik duvarı kuralları ekleyebilirsiniz.

![Güvenlik duvarı kuralları](./media/configure-transaction-nodes/firewall-rules.png)

Şunları etkinleştirmek için:

* **Tek IP adresi:** Başlangıç ve bitiş IP adresleri için aynı IP adresini yapılandırın.
* **IP adresi aralığı:** Başlangıç ve bitiş IP adresi aralığını yapılandırın. Örneğin, 10.221.34.0 adresinden başlayan ve 10.221.34.255 ile biten bir Aralık tüm 10.221.34.xxx alt ağını etkinleştirir.
* **Tüm IP adreslerine Izin ver:** Başlangıç IP adresini 0.0.0.0 ve bitiş IP adresini 255.255.255.255 olarak yapılandırın.

## <a name="connection-strings"></a>Bağlantı dizeleri

İşlem düğümünüz için bağlantı dizesi sözdizimi, temel kimlik doğrulaması veya erişim anahtarları kullanılarak sağlanır. HTTPS ve WebSockets üzerinden erişim anahtarları dahil bağlantı dizeleri sağlanır.

İşlem düğümünün bağlantı dizelerini görüntüleyebilir ve uç nokta adreslerini kopyalayabilirsiniz. Azure blok zinciri hizmeti üye işlem düğümlerinizin birine gidin ve Ayarlar ' da **bağlantı dizeleri** ' ni seçin.

![Bağlantı dizeleri](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Örnek kod

Örnek kod, Web3, netes, Web3js ve truffle aracılığıyla işlem düğümünüz için hızlı bir şekilde yapılandırılabilir.

Bir işlem düğümünün örnek bağlantı kodunu görüntüleyebilir ve popüler geliştirici araçlarıyla kullanmak üzere kopyalayabilirsiniz. Azure blok zinciri hizmeti üye işlem düğümlerinizin birine gidin ve Ayarlar ' da **örnek kod** ' ı seçin.

Kullanmak istediğiniz kod örneğini görüntülemek için Web3, netes, Truffle veya Web3j sekmesini seçin.

![Örnek kod](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure CLı kullanarak işlem düğümlerini yapılandırma](manage-cli.md)
