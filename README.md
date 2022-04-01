# MQTT Version 5.0 の日本語訳
なんとなく MQTT Version 5.0 の日本語訳をやっていくことにした。[公式](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)の Table of Contents をやっていく。訳さないほうが正確に伝わりそうな技術用語や、よくわからない部分は英語のままとしている。

## 1 導入
### 1.0 知的財産権のポリシー
この仕様は、技術委員会が設立されたときに選ばれたモードである [OASIS IPR Policy](https://www.oasis-open.org/policies-guidelines/ipr)の [Non-Assertion](https://www.oasis-open.org/policies-guidelines/ipr#Non-Assertion-Mode)モードの元で提供される。この仕様を実装するのに不可欠な可能性がある特許が開示されているかについては、[TC の Web ページ](https://www.oasis-open.org/committees/mqtt/ipr.php)の知的財産権セクションを参照してください。

### 1.1 MQTT 仕様の組織
仕様は 7 つのチャプターに分かれています。
- チャプター 1 - 導入
- チャプター 2 - MQTT Control Packet format
- チャプター 3 - MQTT Control Packets
- チャプター 4 - Operational behavior
- チャプター 5 - セキュリティ
- チャプター 6 - ネットワークトランスポートとしての Websocket の使用
- チャプター 7 - 適合対象

### 1.2 用語
本仕様における MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" といったキーワードは、非規範的 (non-normative) とマークされている場合を除き、IETF RFC 2119 [[RFC2119]](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#RFC2119)で説明されているように解釈されます。

#### Network Connection:
**MQTT で使用されている、基盤となるトランスポートプロトコルによって提供される構造のこと。**
- Client を Server に接続する
- 整列され、損失が少ないバイトストリームを、双方向に送信する手段を提供する。
[セクション 4.2](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#:~:text=Refer%20to-,section%204.2,-Network%20Connection%20for) 非規範的なNetwork Connectionを参照してください。

#### Application Message:
アプリケーションのために、MQTT プロトコルによってネットワーク間で転送されるデータのこと。Application Message が MQTT によって転送される際、それはペイロードデータ、Quality of Service (QoS)、プロパティのコレクション、およびトピック名を含みます。

#### Client:
MQTT を使うプログラムまたはデバイスのこと。
- Server との Network Connection をオープンする。
- 他の Client が関心を持つ Application Message をパブリッシュする。
- 受信したい Application Message を要求するためにサブスクライブする。
- Application Message の要求を削除するためにアンサブスクライブする。
- Server との Network Connection をクローズする。

#### Server:
Application Message をパブリッシュする Client と Subscription を作成する Client の仲介者として振る舞うプログラムまたはデバイスのこと。
- Client からのNetwork Connectionを受け入れる。
- Client によってパブリッシュされた Application Message を受け入れる
- Client からの Subscribe や Unsubscribe を処理する。
- Client Subscription にマッチする Application Message を転送する。
- Client からの Network Connection をクローズする。

#### Session:
1 つの Client と 1 つの Server 間のステートフルな対話のこと。一部の Session は接続の間のみ持続する他、Client と Server 間の複数の連続した Network Connection にまたがることもできます。

#### Subscription:
1 つの Subscription は 1 つの Topic Filter と最大 QoS で構成されます。1 つの Subscription は 1 つの Session とひも付きます。1 つの Session は 1 つ以上の Subscription を含みます。1 つの Session の中のそれぞれの Subscription は異なる Topic Filter を持ちます。

#### Shared Subscription:
1 つの Shared Subscription は 1 つの Topic Filter と最大 QoS で構成されます。1 つの Shared Subscription は、広範囲のメッセージ交換パターンを許可するために、1 つ以上の Session にひも付きます。Shared Subscription にマッチする単一の Application Message はこれらの Session の 1 つと紐づく Client に対してのみ送信されます。1 つの Session は 1 つ以上の Shared Subscription をサブスクライブでき、Shared Subscription と Shared ではない Subscription の両方を含むことができます。

#### Wildcard Subscription:
1 つの Wildcard Subscription は 1 つまたは複数のワイルドカード文字を含んだ 1 つの Topic Filter を持つ 1 つの Subscription です。これは、そのサブスクリプションが 1 つ以上の Topic Name にマッチすることを許します。

#### Topic Name:
Server で認識されているサブスクリプションにマッチする Application Message にアタッチされたラベルのこと。

#### Topic Filter:
1 つまたは複数のトピックに対する関心を示すために、Subscription 内に含まれる表現のこと。

#### MQTT Control Packet:
Network Connection を通じて送信される情報のパケット。MQTT は異なる 15 種類の MQTT Control Packet を定義し、例えば PUBLISH パケットは Application Message を伝えるのに使用されます。

#### Malformed Packet:
この仕様にしたがって解析することができないコントロールパケットのこと。エラーハンドリングの情報についてはセクション 4.13 を参照してください。

#### Protocol Error:
パケットが解析された後に検出され、プロトコルで許可されていないデータを含んでいるか、Client または Server の状態と矛盾しているエラーのこと。エラーハンドリングについてはセクション 4.13 を参照してください。

#### Will Message:
Network Connection が正常にクローズされなかった場合に Network Connection がクローズされた後、Server によってパブリッシュされた Application Message のこと。Will Message についてはセクション 3.1.2.5 を参照してください。

#### Disallowed Unicode code point:
UTF-8 Encoded String において含まれるべきでない、Unicode Control Codes と Unicode Noncharacter のセットのこと。Disallowed Unicode code point についての詳細な情報はセクション 1.5.4 を参照してください。

### 1.3 引用規格
**[RFC2119]**

Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119, DOI 10.17487/RFC2119, March 1997,

http://www.rfc-editor.org/info/rfc2119

 

**[RFC3629]**

Yergeau, F., "UTF-8, a transformation format of ISO 10646", STD 63, RFC 3629, DOI 10.17487/RFC3629, November 2003,

http://www.rfc-editor.org/info/rfc3629

 

**[RFC6455]**

Fette, I. and A. Melnikov, "The WebSocket Protocol", RFC 6455, DOI 10.17487/RFC6455, December 2011,

http://www.rfc-editor.org/info/rfc6455

 

**[Unicode]**

The Unicode Consortium. The Unicode Standard,

http://www.unicode.org/versions/latest/

### 1.4 非引用規格
**[RFC0793]**

Postel, J., "Transmission Control Protocol", STD 7, RFC 793, DOI 10.17487/RFC0793, September 1981, http://www.rfc-editor.org/info/rfc793

 

**[RFC5246]**

Dierks, T. and E. Rescorla, "The Transport Layer Security (TLS) Protocol Version 1.2", RFC 5246, DOI 10.17487/RFC5246, August 2008,

http://www.rfc-editor.org/info/rfc5246

 

**[AES]**

Advanced Encryption Standard (AES) (FIPS PUB 197).

https://csrc.nist.gov/csrc/media/publications/fips/197/final/documents/fips-197.pdf

 

**[CHACHA20]**

ChaCha20 and Poly1305 for IETF Protocols

https://tools.ietf.org/html/rfc7539

 

**[FIPS1402]**

Security Requirements for Cryptographic Modules (FIPS PUB 140-2)

https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf

 

**[IEEE 802.1AR]**

IEEE Standard for Local and metropolitan area networks - Secure Device Identity

http://standards.ieee.org/findstds/standard/802.1AR-2009.html

 

**[ISO29192]**

ISO/IEC 29192-1:2012 Information technology -- Security techniques -- Lightweight cryptography -- Part 1: General

https://www.iso.org/standard/56425.html

 

**[MQTT NIST]**

MQTT supplemental publication, MQTT and the NIST Framework for Improving Critical Infrastructure Cybersecurity

http://docs.oasis-open.org/mqtt/mqtt-nist-cybersecurity/v1.0/mqtt-nist-cybersecurity-v1.0.html

 

**[MQTTV311]**

MQTT V3.1.1 Protocol Specification

http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html

 

**[ISO20922]**

MQTT V3.1.1 ISO Standard (ISO/IEC 20922:2016)

https://www.iso.org/standard/69466.html

 

**[NISTCSF]**

Improving Critical Infrastructure Cybersecurity Executive Order 13636

https://www.nist.gov/sites/default/files/documents/itl/preliminary-cybersecurity-framework.pdf

 

**[NIST7628]**

NISTIR 7628 Guidelines for Smart Grid Cyber Security Catalogue

https://www.nist.gov/sites/default/files/documents/smartgrid/nistir-7628_total.pdf

 

**[NSAB]**

NSA Suite B Cryptography

http://www.nsa.gov/ia/programs/suiteb_cryptography/

 

**[PCIDSS]**

PCI-DSS Payment Card Industry Data Security Standard

https://www.pcisecuritystandards.org/pci_security/

 

**[RFC1928]**

Leech, M., Ganis, M., Lee, Y., Kuris, R., Koblas, D., and L. Jones, "SOCKS Protocol Version 5", RFC 1928, DOI 10.17487/RFC1928, March 1996,

http://www.rfc-editor.org/info/rfc1928

 

**[RFC4511]**

Sermersheim, J., Ed., "Lightweight Directory Access Protocol (LDAP): The Protocol", RFC 4511, DOI 10.17487/RFC4511, June 2006,

http://www.rfc-editor.org/info/rfc4511

 

**[RFC5280]**

Cooper, D., Santesson, S., Farrell, S., Boeyen, S., Housley, R., and W. Polk, "Internet X.509 Public Key Infrastructure Certificate and Certificate Revocation List (CRL) Profile", RFC 5280, DOI 10.17487/RFC5280, May 2008,

http://www.rfc-editor.org/info/rfc5280

 

**[RFC6066]**

Eastlake 3rd, D., "Transport Layer Security (TLS) Extensions: Extension Definitions", RFC 6066, DOI 10.17487/RFC6066, January 2011,

http://www.rfc-editor.org/info/rfc6066

 

**[RFC6749]**

Hardt, D., Ed., "The OAuth 2.0 Authorization Framework", RFC 6749, DOI 10.17487/RFC6749, October 2012,

http://www.rfc-editor.org/info/rfc6749

 

**[RFC6960]**

Santesson, S., Myers, M., Ankney, R., Malpani, A., Galperin, S., and C. Adams, "X.509 Internet Public Key Infrastructure Online Certificate Status Protocol - OCSP", RFC 6960, DOI 10.17487/RFC6960, June 2013,

http://www.rfc-editor.org/info/rfc6960

 

**[SARBANES]**

Sarbanes-Oxley Act of 2002.

http://www.gpo.gov/fdsys/pkg/PLAW-107publ204/html/PLAW-107publ204.htm

 

**[USEUPRIVSH]**

U.S.-EU Privacy Shield Framework

https://www.privacyshield.gov

 

**[RFC3986]**

Berners-Lee, T., Fielding, R., and L. Masinter, "Uniform Resource Identifier (URI): Generic Syntax", STD 66, RFC 3986, DOI 10.17487/RFC3986, January 2005,

http://www.rfc-editor.org/info/rfc3986

 

**[RFC1035]**

Mockapetris, P., "Domain names - implementation and specification", STD 13, RFC 1035, DOI 10.17487/RFC1035, November 1987,

http://www.rfc-editor.org/info/rfc1035

 

**[RFC2782]**

Gulbrandsen, A., Vixie, P., and L. Esibov, "A DNS RR for specifying the location of services (DNS SRV)", RFC 2782, DOI 10.17487/RFC2782, February 2000,

http://www.rfc-editor.org/info/rfc2782

### 1.5 データ表現
#### 1.5.1 ビット
1 つのバイト内のビットには、7 から 0 のラベルが付いています。ビット番号 7 は最上位ビットであり、最下位ビットにはビット番号 0 が割り当てられます。

#### 1.5.2 Two Byte Integer
Two Byte Integer データ値はビッグエンディアン順の 16 bit の符号なし整数です。つまり、上位バイトが下位バイトに先行します。これは 16 ビット文字がネットワーク上において最上位バイト (MSB) が提示されたあとに、最下位バイト (LSB) が続くということを意味します。

#### 1.5.3 Four Byte Integer
Four Byte Integer データ値はビッグエンディアン順の 32 bit の符号なし整数です。つまり、上位バイトが連続する下位バイトに先行します。これは 32 ビット文字がネットワーク上において最上位バイト (MSB) が提示されたあとに、その次の最上位バイト (MSB) が続き、さらにその次の最上位バイト (MSB) が続き、最後に最下位バイト (LSB) が続くということを意味します。

#### 1.5.4 UTF-8 Encoded String
後に示される MQTT Control Packet 内のフィールドは　UTF-8 文字列としてエンコードされます。UTF-8 [RFC3629](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#RFC3629) は優れた Unicode [Unicode](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#Unicode) 文字のエンコーディングであり、テキストベースのコミュニケーションをサポートするために ASCII 文字のエンコーディングを最適化します。

これらの各文字列には、UTF-8 エンコード文字自身のバイト数を与える Two Byte Integer 長フィールドがプレフィクスとして付与されています。以下の Figure 1.1 Structure of UTF-8 Encoded String のとおりです。その結果、UTF-8 エンコード文字の最大サイズは 65535 byte です。※

※ うまく訳せなかったので原文:   
Each of these strings is prefixed with a Two Byte Integer length field that gives the number of bytes in a UTF-8 encoded string itself, as illustrated in Figure 1.1 Structure of UTF-8 Encoded Strings below. Consequently, the maximum size of a UTF-8 Encoded String is 65,535 bytes.

特に明記されていない限り、すべての UTF-8 エンコード文字列は、0 〜 65535 byte の範囲の任意の長さにすることができます。

Figure 1‑1 Structure of UTF-8 Encoded Strings
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="213" valign="top" style="width:159.6pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Bit</b></p>
  </td>
  <td width="53" valign="top" style="width:39.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>7</b></p>
  </td>
  <td width="53" valign="top" style="width:39.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>6</b></p>
  </td>
  <td width="53" valign="top" style="width:39.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>5</b></p>
  </td>
  <td width="53" valign="top" style="width:39.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>4</b></p>
  </td>
  <td width="53" valign="top" style="width:39.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>3</b></p>
  </td>
  <td width="53" valign="top" style="width:39.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>2</b></p>
  </td>
  <td width="53" valign="top" style="width:39.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>1</b></p>
  </td>
  <td width="53" valign="top" style="width:39.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>0</b></p>
  </td>
 </tr>
 <tr>
  <td width="213" valign="top" style="width:159.6pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 1</p>
  </td>
  <td width="426" colspan="8" valign="top" style="width:319.2pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">String length MSB</p>
  </td>
 </tr>
 <tr>
  <td width="213" valign="top" style="width:159.6pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 2</p>
  </td>
  <td width="426" colspan="8" valign="top" style="width:319.2pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">String length LSB</p>
  </td>
 </tr>
 <tr>
  <td width="213" valign="top" style="width:159.6pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 3 ….</p>
  </td>
  <td width="426" colspan="8" valign="top" style="width:319.2pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">UTF-8 encoded
  character data, if length &gt; 0.</p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

1 つの UTF-8 エンコード文字内の文字データは Unicode 仕様 [Unicode](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#Unicode) で定義され、RFC 3629 [RFC3629](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#RFC3629) で再記述された、整形式の UTF-8 でなければなりません (MUST)。特に、文字データは、U+D800 と U+DFFF [MQTT-1.5.4-1] 間のコードポイントのエンコーディングを含んではなりません (MUST NOT)。もし Client か Server が奇形の UTF-8 を含む MQTT Control Packet を受け取った場合、それは Malformed Packet です。エラーハンドリングについてはセクション 4.13 を参照してください。

UTF-8 エンコード文字は null 文字 U+0000 のエンコーディングを含んではなりません (MUST NOT)。[MQTT-1.5.4-2]。もし、レシバー (Client か Server) が U+0000 を含む MQTT Control Packet を受け取った場合には、それは Malformed Packet です。エラーハンドリングについてはセクション 4.13 を参照してください。

データは以下にリストされた Unicode [Unicode] コードポイントのエンコーディングを含むべきではありません (SHOULD NOT)。もしレシーバー (Server または Client) がそれらを含む MQTT Control Packet を受け取った場合、それは Malformed Packet として扱われる可能性がある (MAY)。これらは Disallowed Unicoded コードポイントである。Disallowed Unicode コードポイントのハンドリングについてはセクション 5.4.9 を参照してください。

 - U+0001..U+001F コントロール文字
 - U+007F..U+009F コントロール文字
 - コードポイントは Unicode 仕様 [[Unicode]](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#Unicode) 非文字列として定義されている (例えば U+0FFFF)。

UTF-8 エンコードされたシーケンス 0xEF 0xBB 0xBF は、文字列のなかで現れても、常に U+FEFF ("ZERO WIDTH NO-BREAK SPACE") と解釈され、パケットレシバーによってスキップまたは削除されてはなりません (MQTT-1.5.4-3)。

**非規範的な例**   
例えば、LATIN CAPITAL Letter A の後にコードポイント U+2A6D4 (CJK IDEOGRAPH EXTENSION B 文字を表す) が続く A𪛔 は以下のようにエンコードされます。

Figure 1‑2 UTF-8 Encoded String non-normative example
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Bit</b></p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>7</b></p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>6</b></p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>5</b></p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>4</b></p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>3</b></p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>2</b></p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>1</b></p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>0</b></p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 1</p>
  </td>
  <td width="511" colspan="8" valign="top" style="width:383.1pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">String Length MSB
  (0x00)</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">&nbsp;</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 2</p>
  </td>
  <td width="511" colspan="8" valign="top" style="width:383.1pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">String Length LSB
  (0x05)</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">&nbsp;</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 3</p>
  </td>
  <td width="511" colspan="8" valign="top" style="width:383.1pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">‘A’ (0x41)</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">&nbsp;</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 4</p>
  </td>
  <td width="511" colspan="8" valign="top" style="width:383.1pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">(0xF0)</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">&nbsp;</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 5</p>
  </td>
  <td width="511" colspan="8" valign="top" style="width:383.1pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">(0xAA)</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">&nbsp;</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
 </tr>
 <tr style="height:24.9pt">
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt;height:24.9pt">
  <p class="MsoNormal">byte 6</p>
  </td>
  <td width="511" colspan="8" valign="top" style="width:383.1pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt;height:24.9pt">
  <p class="MsoNormal" align="center" style="text-align:center">(0x9B)</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">&nbsp;</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 7</p>
  </td>
  <td width="511" colspan="8" valign="top" style="width:383.1pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">(0x94)</p>
  </td>
 </tr>
 <tr>
  <td width="128" valign="top" style="width:95.7pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">&nbsp;</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center;page-break-after:
  avoid">0</p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

#### 1.5.5 Variable Byte Integer
Variable Byte Integer は 127 までの値にシングルバイトを使うエンコードスキーマを用いてエンコードされます。より大きい値は以下のように扱われる。それぞれのバイトの最下位の 7 bit はデータをエンコードし、最上位ビットは後に続くバイトが存在するかを示すために使用されます。したがって、それぞれのバイトは 128 の値と "continuation bit" をエンコードします。Variable Byte Integer フィールドにおける最大のバイト数は 4 です。エンコードされた値は、その値を表すのに最小のバイト数を使用しなくてはなりません (MUST) [MQTT-1.5.5-1]。これについては、Table 1-1 に示されます。

Table 1-1 Size of Variable Byte Integer
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="61" valign="top" style="width:45.9pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Digits</b></p>
  </td>
  <td width="240" valign="top" style="width:2.5in;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>From</b></p>
  </td>
  <td width="252" valign="top" style="width:189.0pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>To</b></p>
  </td>
 </tr>
 <tr>
  <td width="61" valign="top" style="width:45.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="240" valign="top" style="width:2.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">0 (0x00)</p>
  </td>
  <td width="252" valign="top" style="width:189.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">127 (0x7F)</p>
  </td>
 </tr>
 <tr>
  <td width="61" valign="top" style="width:45.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">2</p>
  </td>
  <td width="240" valign="top" style="width:2.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">128 (0x80, 0x01)</p>
  </td>
  <td width="252" valign="top" style="width:189.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">16,383 (0xFF, 0x7F)</p>
  </td>
 </tr>
 <tr>
  <td width="61" valign="top" style="width:45.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">3</p>
  </td>
  <td width="240" valign="top" style="width:2.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">16,384 (0x80, 0x80, 0x01)</p>
  </td>
  <td width="252" valign="top" style="width:189.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">2,097,151 (0xFF, 0xFF, 0x7F)</p>
  </td>
 </tr>
 <tr>
  <td width="61" valign="top" style="width:45.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">4</p>
  </td>
  <td width="240" valign="top" style="width:2.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">2,097,152 (0x80, 0x80, 0x80,
  0x01)</p>
  </td>
  <td width="252" valign="top" style="width:189.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">268,435,455 (0xFF, 0xFF, 0xFF,
  0x7F)</p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

**非規範的コメント**   
非負の整数を Variable Byte Integer (X) エンコーディングスキーマへエンコードするためのアルゴリズムは以下のようになります。

```
do
   encodedByte = X MOD 128
   X = X DIV 128
   // if there are more data to encode, set the top bit of this byte
   if (X > 0)
      encodedByte = encodedByte OR 128
   endif
   'output' encodedByte
while (X > 0)
```

ここで、MOD はモジュロ演算子 (C では %)、DIV は整数除算 (C では /)、OR はビット計算の or (C では |) です。

**非規範的コメント**   
Variable Byte Integer をデコードするアルゴリズムは以下のようになる。

```
multiplier = 1
value = 0
do
   encodedByte = 'next byte from stream'
   value += (encodedByte AND 127) * multiplier
   if (multiplier > 128*128*128)
      throw Error(Malformed Variable Byte Integer)
   multiplier *= 128
while ((encodedByte AND 128) != 0)
```

このアルゴリズムが終了した時、```value``` は Variable Byte Inter の値を含む。

#### 1.5.6 Binary Data
Binary Data はデータバイト数を示す Two Byte Integer 長によって表現され、後にバイト数が続きます。したがって、Binary Data は 0 から 65535 byte の範囲に制限されます。

#### 1.5.7 UTF-8 String Pair
1 つの UTF-8 文字ペアは 2 つの UTF-8 エンコード文字で構成される。このデータタイプは name-value のペアを保持するために使用されます。最初の文字列は name として機能し、2番目の文字列には value が含まれます。

両方の文字は、UTF-8 エンコード文字列の要件に準拠する必要があります (MUST) [MQTT-1.5.7-1]。もしレシーバー (Client または Server) がこれらの要件を満たさない文字ペアを受け取った場合、それは不正な形式のパケットです。エラーハンドリングについては、セクション 4.13 を参照してください。

### 1.6 セキュリティ
MQTT クライアントとサーバの実装は、チャプター 5 で議論するような認証と認可およびセキュアな対話オプションを提供すべきです (SHOULD)。クリティカルなインフラ、個人を特定できる情報、あるいはその他の個人またはセンシティブな情報に関係するアプリケーションは、これらのセキュリティ機能を使用することが推奨されます。

### 1.7 編集規則
この仕様内で黄色にハイライトされたテキストは適合性宣言を示しています。各適合性宣言には [MQTT-x.x.x-y] というフォーマットの参照が割り当てられており、x.x.x はセクション番号であり、y セクション内のステートメント番号です。

### 1.8 Change history
#### 1.8.1 MQTT v3.1.1
MQTT v3.1.1 は最初の OASIS スタンダードバージョンの MQTT です [MQTTV311]。   
MQTT v3.1.1 は ISO/IEC 20922:2016 [[ISO20922]](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#ISO20922) のスタンダードでもあります。

#### 1.8.2 MQTT v5.0
MQTT v5.0 はコア機能を保ちながら、多数の新機能を MQTT に追加しています。主要な機能は、
- スケーラビリティと大規模システムのための機能強化
- エラーレポートの向上
- 探索機能と要求応答を含む一般的なパターンの形式化
- ユーザプロパティを含む拡張機能
- パフォーマンス向上とスモールクライアントのサポート

MQTT v5.0 における変更のまとめについては [Appendix C](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#AppendixC) を参照してください。


## 2 MQTT Control Packet format
### 2.1 MQTT Control Packet の構造
MQTT プロトコルは一連の MQTT Control Packets を定義された方法で交換し合うことによって動作します。このセクションはこれらのパケットのフォーマットを説明します。

1 つの MQTT Control Packet は 3 つ以上のパートで構成され、常に以下に示すような順番の並びとなります。

Figure 2‑1 Structure of an MQTT Control Packet
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" width="643" style="width:6.7in;border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="643" valign="top" style="width:6.7in;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">固定ヘッダ、すべての MQTT Control Packets に存在する</p>
  </td>
 </tr>
 <tr>
  <td width="643" valign="top" style="width:6.7in;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">可変ヘッダー、いくつかの MQTT Control Packets に存在する</p>
  </td>
 </tr>
 <tr>
  <td width="643" valign="top" style="width:6.7in;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">ペイロード、いくつかの MQTT Control Packets に存在する</p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

#### 2.1.1 Fixed Header
それぞれの MQTT Control Packet は以下に示すような 1 つの固定ヘッダーを含みます。

Figure 2‑2 Fixed Header format
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="106" valign="top" style="width:79.8pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Bit</b></p>
  </td>
  <td width="69" valign="top" style="width:51.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>7</b></p>
  </td>
  <td width="60" valign="top" style="width:45.0pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>6</b></p>
  </td>
  <td width="60" valign="top" style="width:45.0pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>5</b></p>
  </td>
  <td width="72" valign="top" style="width:.75in;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>4</b></p>
  </td>
  <td width="72" valign="top" style="width:.75in;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>3</b></p>
  </td>
  <td width="72" valign="top" style="width:.75in;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>2</b></p>
  </td>
  <td width="60" valign="top" style="width:45.0pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>1</b></p>
  </td>
  <td width="67" valign="top" style="width:.7in;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>0</b></p>
  </td>
 </tr>
 <tr>
  <td width="106" valign="top" style="width:79.8pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 1</p>
  </td>
  <td width="261" colspan="4" valign="top" style="width:195.6pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">MQTT Control Packet
  type</p>
  </td>
  <td width="271" colspan="4" valign="top" style="width:203.4pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Flags specific to
  each MQTT Control Packet type</p>
  </td>
 </tr>
 <tr>
  <td width="106" valign="top" style="width:79.8pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 2…</p>
  </td>
  <td width="532" colspan="8" valign="top" style="width:399.0pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Remaining Length</p>
  </td>
 </tr>
</tbody></table>

#### 2.1.2 MQTT Control Packet type
**Position:** byte 1, bit 7-4   
4 bit の符号なし値として表され、以下のようになります。

Table 2‑1 MQTT Control Packet types
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" width="667" style="width:6.95in;border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Name</b></p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Value</b></p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Direction of
  flow</b></p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Description</b></p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Forbidden</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Client to Server</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Connection request</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">2</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Server to Client</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Connect acknowledgment</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">3</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Client to Server or</p>
  <p class="MsoNormal" align="center" style="text-align:center">Server to Client</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Publish message</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBACK</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">4</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Client to Server or</p>
  <p class="MsoNormal" align="center" style="text-align:center">Server to Client</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Publish acknowledgment (QoS 1)</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBREC</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">5</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Client to Server or</p>
  <p class="MsoNormal" align="center" style="text-align:center">Server to Client</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Publish received (QoS 2 delivery part 1)</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBREL</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">6</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Client to Server or</p>
  <p class="MsoNormal" align="center" style="text-align:center">Server to Client</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Publish release (QoS 2 delivery part 2)</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBCOMP</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">7</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Client to Server or</p>
  <p class="MsoNormal" align="center" style="text-align:center">Server to Client</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Publish complete (QoS 2 delivery part 3)</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBSCRIBE</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">8</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Client to Server</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Subscribe request</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">9</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Server to Client</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Subscribe acknowledgment</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UNSUBSCRIBE</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">10</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Client to Server</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Unsubscribe request</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UNSUBACK</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">11</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Server to Client</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Unsubscribe acknowledgment</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PINGREQ</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">12</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Client to Server</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PING request</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PINGRESP</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">13</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Server to Client</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PING response</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">14</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Client to Server or</p>
  <p class="MsoNormal" align="center" style="text-align:center">Server to Client</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Disconnect notification</p>
  </td>
 </tr>
 <tr style="height:3.0pt">
  <td width="111" valign="top" style="width:83.05pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt;height:3.0pt">
  <p class="MsoNormal">AUTH</p>
  </td>
  <td width="70" valign="top" style="width:52.85pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt;height:3.0pt">
  <p class="MsoNormal" align="center" style="text-align:center">15</p>
  </td>
  <td width="132" valign="top" style="width:99.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt;height:3.0pt">
  <p class="MsoNormal" align="center" style="text-align:center">Client to Server or
  Server to Client</p>
  </td>
  <td width="354" valign="top" style="width:265.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt;height:3.0pt">
  <p class="MsoNormal">Authentication exchange</p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

2.1.3 Flags