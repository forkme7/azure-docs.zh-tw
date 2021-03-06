---
title: "Azure IoT 中樞的 X.509 安全性概念 | Microsoft Docs"
description: "概念 - 了解 X.509 憑證授權單位憑證在 IoT 裝置製造和驗證方面的價值。"
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 34d4be431b76d5ba8258d932cb21ed6f4818863e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>概念性了解 IoT 產業中的 X.509 CA 憑證

本文說明在 IoT 裝置製造和 IoT 中樞驗證中使用 X.509 憑證授權單位 (CA) 憑證的價值。  文中會包含有關供應鏈設定的資訊，並指出各項優點。

本文章說明：

* 何謂 X.509 CA 憑證以及該如何取得
* 如何向 IoT 中樞註冊 X.509 CA 憑證
* 如何設定製造供應鏈以進行 X.509 CA 型驗證
* 使用 X.509 CA 所簽署的裝置如何連線到 IoT 中樞

## <a name="overview"></a>概觀

X.509 憑證授權單位 (CA) 驗證方法可用來對 IoT 中樞驗證裝置，其使用的方法可大幅簡化供應鏈的裝置身分識別建立和生命週期管理工作。

X.509 CA 驗證有一個特殊屬性，那就是 CA 憑證和其下游裝置具有一對多關聯性。  這個關聯性讓您只須註冊一次 X.509 CA 憑證，就能在 IoT 中樞內註冊任意數目的裝置，否則您就必須先為每個裝置預先註冊唯一的憑證，裝置才能夠進行連線。 這個一對多關聯性也可以簡化裝置的憑證生命週期管理作業。

X.509 CA 驗證還有一個重要屬性，那就是能夠簡化供應鏈物流。  想要有安全的裝置驗證，就必須讓每個裝置保有唯一的密碼 (例如金鑰) 以作為信任基礎。 在憑證型驗證中，此密碼就是私密金鑰。 典型的裝置製造流程牽涉到多個步驟和保管者。  要在多位保管者之間安全地管理裝置的私密金鑰並維持信任，既不容易且所費不貲。  您不妨使用憑證授權單位來解決此問題，請將每位保管者簽署至以密碼編譯的信任鏈結，而不是將裝置的私密金鑰交付給他們。  每位保管者再以其個別的製造流程處理步驟簽署裝置。  最終，您會因為使用以密碼編譯的信任鏈結，藉由內建責任制度而擁有最佳的供應鏈。  值得注意的是，當裝置保護其唯一的私密金鑰時，此程序便會產生最大安全性。  為此，我們鼓勵您使用能夠在內部產生永不曝光之私密金鑰的硬體安全模組 (HSM)。

本文會完整呈現 X.509 CA 驗證的使用流程 (從供應鏈設定到裝置連線)，同時透過真實範例來強化您的了解。

## <a name="introduction"></a>簡介

X.509 CA 憑證是數位憑證，其擁有者可以簽署其他憑證。  此數位憑證既是 X.509 (因為它符合 IETF RFC 5280 標準所規定的憑證格式標準)，也是憑證授權單位 (CA) (因為其擁有者可以簽署其他憑證)。

透過具體範例才能真正了解如何使用 X.509 CA。  我們假設有一家公司名叫 Company-X，該公司製作出需要專業安裝的 Smart-X-Widget。 Company-X 將製造和安裝外包。  它與製造商 Factory-Y 簽訂合約，委託他們製造 Smart-X-Widget，並與服務提供者 Technician-Z 簽訂合約，委託他們進行安裝。 Company-X 想要讓 Smart-X-Widget 直接從 Factory-Y 出貨給 Technician-Z 安裝，並想要讓 Smart-X-Widget 在安裝後就直接連線到 Company-X 的 IoT 中樞執行個體，而不需要由 Company-X 介入協助。 為達成此目標，Company-X 必須完成幾個一次性設定作業，讓 Smart-X-Widget 能夠準備好自動連線。  在記住這整個案例後，本文其餘部分將會是以下結構：

* 取得 X.509 CA 憑證

* 向 IoT 中樞註冊 X.509 CA 憑證

* 將裝置簽署到憑證信任鏈結

* 裝置連線

## <a name="acquire-the-x509-ca-certificate"></a>取得 X.509 CA 憑證

Company-X 可選擇向公開的根憑證授權單位購買 X.509 CA 憑證，也可以透過自我簽署程序建立一個 X.509 CA 憑證。  根據應用案例，其中一個選項會優於另一個。  但不論是哪個選項，此程序都需要兩個基本步驟，也就是產生公開/私密金鑰組，並將公開金鑰簽章到憑證。

![img-csr-flow](./media/csr-flow.png)

如何完成這些步驟的詳細資訊會隨各服務提供者而不同。

### <a name="purchasing-an-x509-ca-certificate"></a>購買 X.509 CA 憑證

購買 CA 憑證的好處在於，有知名的根 CA 可作為信任的第三方來擔保 IoT 裝置在連線時的合法性。 如果 Company-X 想要讓 Smart-X-Widget 在首次連線到 IoT 中樞之後與第三方產品或服務互動，就會選擇此選項。

若要購買 X.509 CA 憑證，Company-X 則會選擇根憑證服務提供者。 在網際網路上搜尋片語「根 CA」就能找到有用的線索。  根 CA 會指引 Company-X 該如何建立公開/私密金鑰組，以及該如何為其服務產生憑證簽署要求 (CSR)。  CSR 是向憑證授權單位申請憑證的正式程序。  經過這次的購買，便可獲得用來作為授權單位憑證的憑證。  由於 X.509 憑證非常普遍，此憑證可能已正確格式化為 IETF 的 RFC 5280 標準。

### <a name="creating-a-self-signed-x509-ca-certificate"></a>建立自我簽署的 X.509 CA 憑證

建立自我簽署 X.509 CA 憑證的程序與購買程序類似，差別只在於涉及第三方簽署者，例如根憑證授權單位。 在我們的範例中，Company-X 會簽署其授權單位憑證，而非根憑證授權單位。  Company-X 可選擇此選項來進行測試，直到他們準備好購買授權單位憑證。 如果 Company-X 不打算讓 Smart-X-Widget 連線到 IoT 中樞以外的任何第三方服務，則他們也可以在生產環境使用自我簽署的 X.509 CA 憑證。

## <a name="register-the-x509-certificate-to-iot-hub"></a>向 IoT 中樞註冊 X.509 憑證

Company-X 必須向 IoT 中樞註冊 X.509 CA，以在 Smart-X-Widget 連線時由 IoT 中樞進行驗證。  這個一次性的程序可讓您驗證及管理任意數目的 Smart-X-Widget 裝置。  此程序之所以只要進行一次，是因為授權單位憑證與裝置之間具有一對多關聯性，而且此程序也是使用 X.509 CA 驗證方法的其中一個主要優點。  替代方法是為每個 Smart-X-Widget 裝置上傳個別的憑證指紋，但這會因此增加營運成本。

註冊 X.509 CA 憑證的程序包含兩個步驟，亦即上傳憑證和證明憑證擁有權。

![img-pop-flow](./media/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>X.509 CA 憑證上傳

X.509 CA 憑證上傳程序就只是將 CA 憑證上傳到 IoT 中樞。  IoT 中樞預期憑證是放在檔案中。 Company-X 只需上傳憑證檔案。 不論是哪種情況，憑證檔案都不得包含任何私密金鑰。  控管公開金鑰基礎結構 (PKI) 之標準所提供的最佳做法會要求此案例中 Company-X 的私密金鑰資訊只位於 Company-X 內。

### <a name="proof-of-possession-of-the-certificate"></a>證明憑證擁有權

和所有數位憑證一樣，X.509 CA 憑證也是容易遭到竊聽的公開資訊。  因此，竊聽者可能會攔截憑證，然後嘗試將它當作自己的憑證來上傳。  在我們的範例中，IoT 中樞會想要確定 Company-X 所上傳的 CA 憑證確實屬於 Company-X。 其作法是向 Company-X 查問，讓其透過[擁有權證明 (PoP) 流程](https://tools.ietf.org/html/rfc5280#section-3.1)證明其確實擁有憑證。 擁有權證明流程需要 IoT 中樞產生隨機數字以供 Company-X 使用其私密金鑰來簽署。  如果 Company-X 遵循 PKI 最佳做法並保護其私密金鑰，則只有他們能夠正確的回應擁有權證明查問。  在成功回應擁有權證明查問後，IoT 中樞會繼續註冊 X.509 CA 憑證。

成功回應 IoT 中樞所提出的擁有權證明查問後，就能完成 X.509 CA 註冊。

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>將裝置簽署到憑證信任鏈結

IoT 需要每個裝置都擁有唯一的身分識別。  這些身分識別會採用憑證形式，以供進行憑證型驗證配置。  在我們的範例中，這表示每個 Smart-X-Widget 都必須擁有唯一的裝置憑證。  Company-X 如何在其供應鏈中進行此設定？

其中一種設定方法是為 Smart-X-Widget 預先產生憑證，並將對應的裝置唯一私密金鑰資訊交付給供應鏈夥伴。  對於 Company-X 來說，就是交付給 Factory-Y 和 Technician-Z。  雖然這是有效的方法，但所伴隨而來必須克服的挑戰就是確保信任，詳情如下：

1. 不僅必須與供應鏈夥伴共用裝置的私密金鑰，還會忽視絕不共用私密金鑰的 PKI 最佳做法，使得在供應鏈建立信任的成本變得昂貴。  這表示您需要安裝重要系統 (如用來存放裝置私密金鑰的安全室) 和程序 (如定期的安全性稽核)。  這兩者都會讓供應鏈的成本增加。

2. 對於每個金鑰/裝置組來說，從產生裝置唯一憑證 (因此私密金鑰) 的那一刻起到淘汰裝置為止，嚴密計算供應鏈中的裝置並於稍後在部署時加以管理會變成一對一的工作。 如此一來，您就無法以群組方式管理裝置，除非您在程序裡以某種方式明確內建群組的概念。 因此，嚴密的計算和裝置生命週期管理會成為沈重的作業負擔。  在我們的範例中，Company-X 會扛起此一負擔。

X.509 CA 憑證驗證會透過使用憑證鏈結，來提供可應對前述挑戰的卓越解決方案。  憑證鏈結的形成，是由 CA 簽署中繼 CA，中繼 CA 再簽署其他中繼 CA，如此不斷循環，直到最終的中繼 CA 簽署裝置為止。  在我們的範例中，Company-X 會簽署 Factory-Y，Factory-Y 再簽署 Technician-Z，Technician-Z 最終則簽署 Smart-X-Widget。

![img-cert-chain-hierarchy](./media/cert-chain-hierarchy.png)

上述鏈結中的一連串憑證呈現出了授權單位的交接邏輯。  許多供應鏈都遵循這個交接邏輯，以此讓每個中繼 CA 簽署到鏈結中，並同時接收所有上游的 CA 憑證，而最後一個中繼 CA 則負責最終簽署每個裝置，並將鏈結中的所有授權單位憑證注入到裝置內。 這種情形常見於簽約製造公司底下有工廠階層，並任命其中一家特定工廠來負責製造時。  雖然此階層可能深達數層 (例如，依地理位置/產品類型/生產線分類)，但最終只有該家工廠會與裝置互動，然而鏈結卻是從階層的最上層來維護。

替代鏈結可能會由不同的中繼 CA 來與裝置互動，在此情況下，與裝置互動的 CA 會在當下注入憑證鏈結內容。  您也可以使用混合模型，此時只有一些 CA 會與裝置實際互動。

在我們的範例中，Factory-Y 和 Technician-Z 會與 Smart-X-Widget 互動。  雖然 Company-X 擁有 Smart-X-Widget，但在整個供應鏈中，它實際上不會與裝置有實際的互動。  Smart-X-Widget 的憑證信任鏈結因此包含簽署 Factory-Y 的 Company-X，而 Factory-Y 會再簽署 Technician-Z，Technician-Z 然後會提供最終簽章給 Smart-X-Widget。 Smart-X-Widget 的製造和安裝包含 Factory-Y 和 Technician-Z 使用其個別的中繼 CA 憑證來簽署每個 Smart-X-Widget。 這整個程序的最終結果是 Smart-X-Widget 擁有唯一的裝置憑證，而憑證信任鏈結則會上達 Company-X 的 CA 憑證。

![img-cert-mfr-chain](./media/cert-mfr-chain.png)

現在是回顧 X.509 CA 方法價值的好時機。  Company-X 並未對每個 Smart-X-Widget 預先產生憑證，並將憑證交接給供應鏈，而是改為只交接來簽署 Factory-Y 一次。  雖然不必在整個裝置生命週期追蹤每個裝置，但 Company-X 可能無法透過供應鏈程序所自然浮現的群組 (例如，Technician-Z 在某年 7 月後所安裝的裝置) 來追蹤和管理裝置。

最後同時也是最重要的一點是，CA 驗證方法會在裝置的製造供應鏈中注入嚴密的責任制度。 由於採用憑證鏈結程序，鏈結中每個成員的動作都會以密碼編譯方式記錄下來並可供驗證。

此程序依賴某些假設，必須符合這些假設才會完成。  它需要獨立建立裝置唯一的公開/私密金鑰組，並在裝置內保護私密金鑰。  幸運的是，有能夠在內部產生金鑰並保護私密金鑰的安全矽晶片存在，其形式為硬體安全模組 (HSM)。  Company-X 只需要將一個這樣的晶片加入到 Smart-X-Widget 的元件用料表即可。

## <a name="device-connection"></a>裝置連線

在看過前面幾節後，我們接著要談談裝置連線。  藉由直接將 X.509 CA 憑證註冊到 IoT 中樞一次，數量可能有數百萬台的裝置要如何進行第一次連線並進行驗證？  很簡單，只要透過我們在前面註冊 X.509 CA 憑證時所遇到的相同憑證上傳和擁有權證明流程即可。

專為進行 X.509 CA 驗證所製造的裝置，會配備裝置的唯一憑證以及從其個別的製造供應鏈所得到的憑證鏈結。  裝置連線 (即使是第一次) 進行時會經歷兩個步驟的程序：憑證鏈結上傳和擁有權證明。

在憑證鏈結上傳期間，裝置會將其唯一的裝置憑證連同其內部所安裝的憑證鏈結一起上傳到 IoT 中樞。  使用預先註冊的 X.509 CA 憑證，IoT 中樞就可以透過密碼編譯方式驗證幾件事，上傳的憑證鏈結具有內部一致性，而且鏈結是源自有效的 X.509 CA 憑證擁有者。  就和 X.509 CA 註冊程序一樣，IoT 中樞也會起始擁有權證明查問/回應程序，來確定鏈結以及裝置憑證確實屬於將其上傳的裝置。  其使用的方式是產生隨機查問，讓裝置使用其私密金鑰簽署該查問以供 IoT 中樞驗證。   如果回應成功，IoT 中樞就會接受裝置的確可信，並讓其連線。

在我們的範例中，每個 Smart-X-Widget 都會將其唯一的裝置憑證連同 Factory-Y 和 Technician-Z X.509 CA 憑證上傳，然後再回應 IoT 中樞所提出的擁有權證明查問。

![img-device-pop-flow](./media/device-pop-flow.png)

請注意，信任基礎仰賴於保護私密金鑰，裝置的私密金鑰也包括在內。  因此，我們必須一再強調用來保護裝置私密金鑰之安全矽晶片 (以硬體安全模組 (HSM) 的形式存在)，以及絕不共用任何私密金鑰 (例如，某家工廠將其私密金鑰交付給其他工廠) 之整體最佳做法的重要性。

