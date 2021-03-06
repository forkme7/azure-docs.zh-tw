---
title: 針對已加入混合式 Azure Active Directory 的下層裝置進行疑難排解 | Microsoft Docs
description: 針對已加入混合式 Azure Active Directory 的下層裝置進行疑難排解。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 0d21a8848222c4b09723e22d2d51ec43b2154553
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>針對已加入混合式 Azure Active Directory 的下層裝置進行疑難排解 

本主題僅適用於下列裝置： 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

對於 Windows 10 或 Windows Server 2016，請參閱[針對已加入混合式 Azure Active Directory 的 Windows 10 和 Windows Server 2016 裝置進行疑難排解](device-management-troubleshoot-hybrid-join-windows-current.md)。

本主題假設您[設定已加入混合式 Azure Active Directory 的裝置](device-management-hybrid-azuread-joined-devices-setup.md)來支援下列案例：

- 裝置型條件式存取

- [設定的企業漫遊](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello 企業版](active-directory-azureadjoin-passport-deployment.md) 





本主題提供有關如何解決潛在問題的疑難排解指引。  

**您應該知道的事情：** 

- 每位使用者的裝置數目上限是以裝置為主。 舉例來說：如果 *jdoe* 和 *jharnett* 登入裝置，在 [使用者] 資訊索引標籤中就會為每位使用者建立個別的註冊 (DeviceID)。  

- 您可以將裝置的初始註冊/加入設定為在登入或鎖定/解除鎖定時嘗試執行。 工作排程器工作可能會觸發 5 分鐘的延遲。 

- 重新安裝作業系統或手動取消註冊再重新註冊時，可能會在 Azure AD 上建立新的註冊，而導致在 Azure 入口網站中 [使用者] 資訊索引標籤底下有多個項目。 

## <a name="step-1-retrieve-the-registration-status"></a>步驟 1：擷取註冊狀態 

**確認註冊狀態：**  

1. 以系統管理員身分開啟命令提示字元 

2. 輸入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

此命令會顯示一個對話方塊，可提供您有關加入狀態的更多詳細資料。

![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>步驟 2：評估混合式 Azure AD 加入狀態 

如果未成功加入混合式 Azure AD，對話方塊會提供有關所發生問題的詳細資料。

**最常見的問題包括：**

- AD FS 或 Azure AD 設定不正確

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- 您不是以網域使用者身分登入

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    這種情況可能是由幾種不同的原因所致︰
    
    1. 如果登入的使用者不是網域使用者 (例如本機使用者)。 對下層裝置的混合式 Azure AD 聯結僅支援網域使用者。
    
    2. 如果 Autoworkplace.exe 因故無法以無訊息方式使用 Azure AD 或 AD FS 進行驗證。 幾個可能的原因包括對 Azure AD URL 的輸出網路連線有問題 (請檢查先決條件)，或是已為使用者啟用/設定 MFA，但在同盟伺服器上未設定 WIAORMUTLIAUTHN (請檢查設定步驟)。 另一個可能的原因是，主領域探索 (HRD) 頁面正在等候使用者互動，導致 Autoworkplace.exe 無法以無訊息模式取得權杖。
    
    3. 如果組織使用 Azure AD 無縫單一登入，則下列 URL 不會存在於裝置的 IE 內部網路設定中：
    
       - https://autologon.microsoftazuread-sso.com

    
       而且必須為內部網路區域啟用 [允許透過指令碼更新狀態列]。

- 已達到配額限制

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- 服務沒有回應 

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

您也可以在事件記錄檔的 [應用程式及服務記錄檔] > [Microsoft-Workplace Join] 底下找到狀態資訊。
  
**混合式 Azure AD 加入失敗的最常見原因包括：** 

- 您的電腦不在組織的內部網路上，或不在可連線到內部部署 AD 網域控制站的 VPN 上。

- 您是使用本機電腦帳戶來登入您的電腦。 

- 服務組態問題： 

  - 同盟伺服器已設定為支援 **WIAORMULTIAUTHN**。 

  - 沒有「服務連接點」物件指向電腦所屬 AD 樹系之 Azure AD 中已驗證的網域名稱。

  - 使用者已達到裝置限制。 

## <a name="next-steps"></a>後續步驟

如有問題，請參閱[裝置管理常見問題集](device-management-faq.md)  
