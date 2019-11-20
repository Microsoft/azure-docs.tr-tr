---
title: Ethereum-Azure blok zinciri hizmeti için Azure blok zinciri geliştirme seti 'ni kullanma
description: Visual Studio Code 'de Ethereum uzantısı için Azure blok zinciri geliştirme setini kullanarak bir Azure blok zinciri hizmeti Konsorsiyumu ağı 'na bağlanma
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 6364e887c699219d80974d592a8ff7c77cca2621
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329309"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Hızlı başlangıç: Azure blok zinciri hizmeti Consortium ağına bağlanmak için Visual Studio Code kullanma

Bu hızlı başlangıçta, Azure blok zinciri hizmeti 'nde bir konsorsiyunuza eklemek üzere Ethereum Visual Studio Code uzantısı için Azure blok zinciri geliştirme setini yüklersiniz ve kullanıyorsunuz. Azure blok zinciri geliştirme seti, Ethereum defterleri üzerinde akıllı sözleşmeleri oluşturma, bağlama, derleme ve dağıtma işlemlerini basitleştirir. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Hızlı başlangıç: Azure Portal veya hızlı başlangıç kullanarak bir blok zinciri üyesi oluşturma](create-member.md) [: Azure CLI kullanarak bir Azure blok zinciri hizmeti blok zinciri üyesi](create-member-cli.md) oluşturma
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Ethereum uzantısı için Azure blok zinciri geliştirme seti](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Yolunuza Python. exe ' yi ekleyin. Azure blok zinciri geliştirme seti için yolunuzda Python gereklidir.
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLı](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Azure blok zinciri geliştirme seti ortamını doğrulama

Azure blok zinciri geliştirme seti, geliştirme ortamı önkoşullarının karşılandığını doğrular. Geliştirme ortamınızı doğrulamak için:

VS Code komut paletinde **Azure blok Zinciri: karşılama sayfasını göster**' i seçin.

Azure blok zinciri geliştirme seti, tamamlanmasının yaklaşık bir dakika süren bir doğrulama betiği çalıştırır. Bu çıktıyı, **terminal > yeni Terminal**' i seçerek görüntüleyebilirsiniz. Terminal menü çubuğunda, açılan menüde **Çıkış** sekmesini ve **Azure blok zincirini** seçin. Başarılı doğrulama aşağıdaki görüntüde olduğu gibi görünür:

![Geçerli geliştirme ortamı](./media/connect-vscode/valid-environment.png)

 Gerekli bir aracı eksik ise, **Azure blok zinciri geliştirme seti-önizleme** adlı yeni bir sekme, yüklenecek gerekli uygulamaları ve araçları indirmek için bağlantıları listeler.

![Geliştirme Seti gerekli uygulamalar](./media/connect-vscode/required-apps.png)

Hızlı başlangıç ile devam etmeden önce eksik önkoşulları yükler.

## <a name="connect-to-consortium-member"></a>Consortium üyesine Bağlan

Azure blok zinciri geliştirme seti VS Code uzantısını kullanarak, konsorsiyum üyelerine bağlanabilirsiniz. Bir konsorsiyumun bağlantısı kurulduktan sonra, akıllı sözleşmeleri bir Azure blok zinciri hizmeti Consortium üyesine derleyebilir, oluşturabilir ve dağıtabilirsiniz.

Azure blok zinciri hizmeti Consortium üyesine erişiminiz yoksa, önkoşul hızlı başlangıcını doldurun: Azure portal veya hızlı başlangıç [kullanarak bir blok zinciri üyesi oluşturma](create-member.md) [: Azure CLI kullanarak bir Azure blok zinciri hizmeti blok zinciri üyesi oluşturma ](create-member-cli.md).

1. Visual Studio Code (VS Code) gezgin bölmesinde, **Azure blok zinciri** uzantısını genişletin.
1. **Konsorsiya Bağlan**' ı seçin.

   ![Konsorsiyumun bağlantısını yapın](./media/connect-vscode/connect-consortium.png)

    Azure kimlik doğrulaması istenirse, bir tarayıcı kullanarak kimlik doğrulaması yapmak için istemleri izleyin.
1. Komut paleti açılan menüsünde **Azure blok zinciri hizmeti Consortium 'A Bağlan** ' ı seçin.
1. Azure blok zinciri hizmeti Consortium üyele ilişkili aboneliği ve kaynak grubunu seçin.
1. Listeden Consortium ' ı seçin.

Konsorsiyum ve blok zinciri üyeleri, Visual Studio Gezgini yan çubuğunda listelenir.

![Gezgin 'de görünen konsorsiyum](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure blok zinciri hizmeti 'nde bir konsorsiyunuza eklemek üzere Ethereum Visual Studio Code uzantısı için Azure blok zinciri geliştirme setini kullandınız. Bir işlem aracılığıyla akıllı sözleşme işlevi oluşturmak, derlemek, dağıtmak ve yürütmek için Ethereum ve truffle için Azure blok zinciri geliştirme setini kullanmak üzere bir sonraki öğreticiyi deneyin.

> [!div class="nextstepaction"]
> [Akıllı sözleşmeleri oluşturmak, derlemek ve dağıtmak için Visual Studio Code kullanma](send-transaction.md)