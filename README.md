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

Network Connection:   
MQTT で使用されている、基盤となるトランスポートプロトコルによって提供される構造のこと。
- Client を Server に接続する
- 整列され、損失が少ないバイトストリームを、双方向に送信する手段を提供する。
[セクション 4.2](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#:~:text=Refer%20to-,section%204.2,-Network%20Connection%20for) 非規範的なNetwork Connectionを参照してください。

Application Message:   
アプリケーションのために、MQTT プロトコルによってネットワーク間で転送されるデータのこと。Application Message が MQTT によって転送される際、それはペイロードデータ、Quality of Service (QoS)、プロパティのコレクション、およびトピック名を含みます。

Client:   
MQTT を使うプログラムまたはデバイスのこと。
- Server との Network Connection をオープンする。
- 他の Client が関心を持つ Application Message をパブリッシュする。
- 受信したい Application Message を要求するためにサブスクライブする。
- Application Message の要求を削除するためにアンサブスクライブする。
- Server との Network Connection をクローズする。

Server:   
Application Message をパブリッシュする Client と Subscription を作成する Client の仲介者として振る舞うプログラムまたはデバイスのこと。
- Client からのNetwork Connectionを受け入れる。
- Client によってパブリッシュされた Application Message を受け入れる
- Client からの Subscribe や Unsubscribe を処理する。
- Client Subscription にマッチする Application Message を転送する。
- Client からの Network Connection をクローズする。

Session:   
1 つの Client と 1 つの Server 間のステートフルな対話のこと。一部の Session は接続の間のみ持続する他、Client と Server 間の複数の連続した Network Connection にまたがることもできます。

Subscription:   
1 つの Subscription は 1 つの Topic Filter と最大 QoS で構成されます。1 つの Subscription は 1 つの Session とひも付きます。1 つの Session は 1 つ以上の Subscription を含みます。1 つの Session の中のそれぞれの Subscription は異なる Topic Filter を持ちます。

Shared Subscription:   
1 つの Shared Subscription は 1 つの Topic Filter と最大 QoS で構成されます。1 つの Shared Subscription は、広範囲のメッセージ交換パターンを許可するために、1 つ以上の Session にひも付きます。Shared Subscription にマッチする単一の Application Message はこれらの Session の 1 つと紐づく Client に対してのみ送信されます。1 つの Session は 1 つ以上の Shared Subscription をサブスクライブでき、Shared Subscription と Shared ではない Subscription の療法を含むことができます。