---
title: Azure Key Vault için istemci kitaplıkları | Microsoft Docs
description: Azure Key Vault için istemci kitaplıkları
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: b8690b5ce3e2aee9a554d3e0682161b86264e124
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818542"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault için istemci kitaplıkları

Azure Key Vault için istemci kitaplıkları, .NET, Python, Java ve JavaScript gibi çeşitli dillerden Key Vault işlevlere programlı erişim sağlar.

## <a name="client-libraries-per-language-and-object"></a>Dil ve nesne başına istemci kitaplıkları

Her SDK, aşağıdaki tabloya göre Anahtar Kasası, gizlilikler, anahtarlar ve sertifikalar için ayrı istemci kitaplıklarına sahiptir.

| Dil | Gizli Diziler | Anahtarlar | Sertifikalar | Key Vault (Yönetim düzlemi) |
|--|--|--|--|--|
| .NET | - [API başvurusu](/dotnet/api/azure.security.keyvault.secrets)<br>- [NuGet paketi](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Hızlı başlangıç](../secrets/quick-create-net.md) | - [API başvurusu](/dotnet/api/azure.security.keyvault.keys)<br>- [NuGet paketi](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [Hızlı başlangıç](../keys/quick-create-net.md) | - [API başvurusu](/dotnet/api/azure.security.keyvault.certificates)<br>- [NuGet paketi](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [Hızlı başlangıç](../certificates/quick-create-net.md) | - [API başvurusu](/dotnet/api/microsoft.azure.management.keyvault)<br>- [NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API başvurusu](/python/api/overview/azure/keyvault-secrets-readme)<br>- [Pypı paketi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Hızlı başlangıç](../secrets/quick-create-python.md) |- [API başvurusu](/python/api/overview/azure/keyvault-keys-readme)<br>- [Pypı paketi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Hızlı başlangıç](../keys/quick-create-python.md) | - [API başvurusu](/python/api/overview/azure/keyvault-certificates-readme)<br>- [Pypı paketi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Hızlı başlangıç](../certificates/quick-create-python.md) | - [API başvurusu](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br> - [Pypı paketi](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API başvurusu](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Hızlı başlangıç](../secrets/quick-create-java.md) |- [API başvurusu](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [Hızlı başlangıç](../keys/quick-create-java.md) | - [API başvurusu](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [Hızlı başlangıç](../certificates/quick-create-java.md) |- [API başvurusu](/java/api/com.microsoft.azure.management.keyvault)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [API başvurusu](/javascript/api/@azure/keyvault-secrets/)<br>- [NPM paketi](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Hızlı başlangıç](../secrets/quick-create-node.md) |- [API başvurusu](/javascript/api/@azure/keyvault-keys/)<br>- [NPM paketi](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [Hızlı başlangıç](../keys/quick-create-node.md)| - [API başvurusu](/javascript/api/@azure/keyvault-certificates/)<br>- [NPM paketi](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [Hızlı başlangıç](../certificates/quick-create-node.md) |  - [API başvurusu](/javascript/api/@azure/arm-keyvault/)<br>- [NPM paketi](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Key Vault Geliştirici Kılavuzu](developers-guide.md)
- [Anahtar kasasında kimlik doğrulama](authentication.md) hakkında daha fazla bilgi edinin
- [Key Vault erişimi güvenli hale getirme](security-features.md) hakkında daha fazla bilgi edinin
