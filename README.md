# MQTT Version 5.0 の日本語訳
なんとなく MQTT Version 5.0 の日本語訳をやっていくことにした。[公式](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)の Table of Contents をやっていく。訳さないほうが正確に伝わりそうな技術用語や、よくわからない部分は英語のままとしている。

## 1 導入
### 1.0 知的財産権のポリシー
この仕様は、技術委員会が設立されたときに選ばれたモードである [OASIS IPR Policy](https://www.oasis-open.org/policies-guidelines/ipr)の [Non-Assertion](https://www.oasis-open.org/policies-guidelines/ipr#Non-Assertion-Mode)モードの元で提供される。この仕様を実装するのに不可欠な可能性がある特許が開示されているかについては、[TC の Web ページ](https://www.oasis-open.org/committees/mqtt/ipr.php)の知的財産権セクションを参照してください。

### 1.1 MQTT 仕様の組織
仕様は 7 つの Chapter に分かれています。
- Chapter 1 - 導入
- Chapter 2 - MQTT Control Packet format
- Chapter 3 - MQTT Control Packets
- Chapter 4 - Operational behavior
- Chapter 5 - セキュリティ
- Chapter 6 - ネットワークトランスポートとしての Websocket の使用
- Chapter 7 - 適合対象

### 1.2 用語
本仕様における MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" といったキーワードは、非規範的 (non-normative) とマークされている場合を除き、IETF RFC 2119 [[RFC2119]](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#RFC2119)で説明されているように解釈されます。

ネットワークコネクション:   
MQTT で使用されている、基盤となるトランスポートプロトコルによって提供される構造のこと。
- クライアントをサーバに接続する
- 整列され、損失が少ないバイトストリームを、双方向に送信する手段を提供する。
[セクション 4.2](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#:~:text=Refer%20to-,section%204.2,-Network%20Connection%20for) 非規範的なネットワークコネクションを参照してください。

アプリケーションメッセージ:   
アプリケーションのために、MQTT プロトコルによってネットワーク間で転送されるデータのこと。Application Message が MQTT によって転送される際、それはペイロードデータ、Quality of Service (QoS)、プロパティのコレクション、およびトピック名を含みます。

クライアント:
MQTT を使うプログラムまたはデバイスのこと。
- サーバとのコネクションをオープンしてる。
- 他のクライアントが関心を持つアプリケーションメッセージをパブリッシュする。
- 受信したいアプリケーションメッセージを要求するためにサブスクライブする。
- アプリケーションメッセージの要求を削除するためにアンサブスクライブする。
- サーバーとのコネクションをクローズする。