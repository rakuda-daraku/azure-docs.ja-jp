---
title: "Azure Database for MySQL の SSL 接続 | Microsoft Docs"
description: "SSL 接続を適切に使用できるように、Azure Database for MySQL と関連アプリケーションを構成するための情報"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.custom: quick start create
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6dc147c9dd3983c2c599108162fa285d87ffc7a3
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Azure Database for MySQL の SSL 接続
Azure Database for MySQL では、Secure Sockets Layer (SSL) を使用して、データベース サーバーをクライアント アプリケーションに接続できます。 データベース サーバーとクライアント アプリケーションの間に SSL 接続を適用すると、サーバーとアプリケーションの間のデータ ストリームが暗号化され、"man in the middle" 攻撃から保護されます。

## <a name="default-settings"></a>既定の設定
既定では、MySQL サーバーへの接続時に SSL 接続を要求するようにデータベース サービスを構成する必要があります。  可能な場合は、SSL オプションを無効にしないことをお勧めします。 

Azure Portal や CLI を使用して新しい Azure Database for MySQL サーバーをプロビジョニングする場合、SSL 接続の適用が既定で有効になります。 

同様に、Azure Portal のサーバーの下にある "接続文字列" 設定で事前定義された接続文字列には、SSL を使用してデータベース サーバーに接続するための一般的な言語の必須パラメーターが含まれます。 SSL パラメーターはコネクタによって異なります ("ssl=true"、"sslmode=require"、"sslmode=required" など)。

アプリケーションの開発時に SSL 接続を有効または無効にする方法については、[SSL の構成方法](howto-configure-ssl.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
[Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)

