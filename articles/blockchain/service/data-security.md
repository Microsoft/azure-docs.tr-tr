---
title: Azure blok zinciri hizmeti güvenliği
description: Azure blok zinciri hizmeti veri erişimi ve güvenlik kavramları
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 63e61844ddb5bd0f0ed52b67e26ea5bf1857fd2b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579914"
---
# <a name="azure-blockchain-service-security"></a>Azure blok zinciri hizmeti güvenliği

Azure Blok Zinciri Hizmeti, verilerinizi güvende tutup bunların kullanılabilir olmasını sağlamak için çeşitli Azure özelliklerini kullanır. Verilerin güvenli hale getirilmesi için yalıtım, şifreleme ve kimlik doğrulaması olanaklarından faydalanılır.

## <a name="isolation"></a>Yalıtım

Azure blok zinciri hizmeti kaynakları özel bir sanal ağda yalıtılmıştır. Her işlem ve doğrulama düğümü bir sanal makinedir (VM). Bir sanal ağdaki VM 'Ler, farklı bir sanal ağdaki VM 'Ler ile doğrudan iletişim kuramaz. Yalıtım, sanal ağ içinde iletişimin özel kalmasını sağlar. Azure sanal ağ yalıtımı hakkında daha fazla bilgi için bkz. [Azure genel bulutundaki yalıtım](../../security/fundamentals/isolation-choices.md#networking-isolation).

![VNET diyagramı](./media/data-security/vnet.png)

## <a name="encryption"></a>Şifreleme

Kullanıcı verileri Azure depolama 'da depolanır. Kullanıcı verileri, güvenlik ve gizlilik için hareket halindeyken ve geri kalanında şifrelenir. Daha fazla bilgi için bkz. [Azure depolama Güvenlik Kılavuzu](../../storage/common/storage-security-guide.md).

## <a name="authentication"></a>Kimlik Doğrulaması

İşlemler, blok zinciri düğümlerine bir RPC uç noktası aracılığıyla gönderilebilir. İstemciler, Kullanıcı kimlik doğrulamasını işleyen ve SSL üzerinden verileri şifreleyen bir ters ara sunucu kullanarak bir işlem düğümüyle iletişim kurar.

![Kimlik doğrulama diyagramı](./media/data-security/authentication.png)

RPC erişimi için üç kimlik doğrulama modu vardır.

### <a name="basic-authentication"></a>Temel kimlik doğrulama

Temel kimlik doğrulaması, Kullanıcı adını ve parolasını içeren bir HTTP kimlik doğrulama üstbilgisi kullanır. Kullanıcı adı, blok zinciri düğümünün adıdır. Parola, bir üye veya düğümün sağlanması sırasında ayarlanır. Parola Azure portal veya CLı kullanılarak değiştirilebilir.

### <a name="access-keys"></a>Erişim tuşları

Erişim anahtarları, uç nokta URL 'sinde bulunan rastgele oluşturulmuş bir dize kullanır. İki erişim anahtarı, anahtar döndürmeyi etkinleştirmeye yardımcı olur. Anahtarlar Azure portal ve CLı 'dan yeniden oluşturulabilir.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD), kullanıcının kimlik doğrulamasının Azure AD Kullanıcı kimlik bilgilerini kullanarak Azure AD tarafından doğrulandığını talep tabanlı bir kimlik doğrulama mekanizması kullanır. Azure AD, bulut tabanlı kimlik yönetimi sağlar ve müşterilerin bulutta tüm kurumsal ve erişim uygulamalarına tek bir kimlik kullanmasına izin verir. Azure blok zinciri hizmeti, Azure AD etkinleştirme KIMLIK Federasyonu, çoklu oturum açma ve Multi-Factor Authentication ile tümleşir. Kullanıcıları, grupları ve uygulama rollerini blok zinciri üyesi ve düğüm erişimi için kuruluşunuza atayabilirsiniz.

Azure AD İstemci proxy 'si [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)' da kullanılabilir. İstemci proxy 'si, kullanıcıyı Azure AD oturum açma sayfasına yönlendirir ve başarılı kimlik doğrulamasından sonra bir taşıyıcı belirteci edinir. Daha sonra Kullanıcı, geth veya Truffle gibi bir Ethereum istemci uygulamasını istemci proxy 'sinin uç noktasına bağlar. Son olarak, bir işlem gönderildiğinde, istemci proxy 'si http üstbilgisindeki taşıyıcı belirtecini çıkartır ve ters proxy, OAuth protokolünü kullanarak belirteci doğrular.

## <a name="keys-and-ethereum-accounts"></a>Anahtarlar ve Ethereum hesapları

Bir Azure blok zinciri hizmeti üyesi sağlanırken, bir Ethereum hesabı ve ortak ve özel anahtar çifti oluşturulur. Özel anahtar, işlemleri blok zincirine göndermek için kullanılır. Ethereum hesabı, ortak anahtarın karmasının son 20 bayttır. Ethereum hesabına cüzdan da denir.

Özel ve ortak anahtar çifti, JSON biçiminde bir anahtar dosyası olarak depolanır. Özel anahtar, blok zinciri defter hizmeti oluşturulduğunda girilen parola kullanılarak şifrelenir.

Özel anahtarlar, işlemleri dijital olarak imzalamak için kullanılır. Özel blok zincirleriyle, özel bir anahtar tarafından imzalanan akıllı sözleşme, İmzalayanın kimliğini temsil eder. İmza geçerliliğini doğrulamak için alıcı, imzalayanın ortak anahtarını imzadan hesaplanan adresle karşılaştırabilirler.

Constellation anahtarları, bir çekirdek düğümünü benzersiz bir şekilde tanımlamak için kullanılır. Constellation anahtarları, düğüm sağlama sırasında oluşturulur ve çekirdekte özel bir işlemin privateFor parametresinde belirtilir.

## <a name="next-steps"></a>Sonraki adımlar

[Azure blok zinciri hizmeti işlem düğümlerini yapılandırma](configure-transaction-nodes.md)
