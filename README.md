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

#### 2.1.3 Flags
固定ヘッダー内のバイトの残りのビット [3-0] は、以下に示すような、それぞれの MQTT Control Packet タイプに対して固有のフラグを含みます。フラグビットが "Reserved" とマークされた場所は、将来のために予約されており、リストされた値に設定する必要があります (MUST) [MQTT-2.1.3-1]。もし不適切なフラグを受け取った場合、それは不正なパケットとなります。エラーハンドリングについての詳細は [section 4.13](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#S4_13_Errors) を参照してください。

Table 2‑2 Flag Bits
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" width="487" style="width:365.4pt;margin-left:.25in;border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>MQTT Control
  Packet</b></p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Fixed Header
  flags</b></p>
  </td>
  <td width="48" valign="top" style="width:.5in;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Bit 3</b></p>
  </td>
  <td width="48" valign="top" style="width:.5in;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Bit 2</b></p>
  </td>
  <td width="48" valign="top" style="width:.5in;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Bit 1</b></p>
  </td>
  <td width="72" valign="top" style="width:.75in;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Bit 0</b></p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Used in MQTT v5.0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DUP</p>
  </td>
  <td width="96" colspan="2" valign="top" style="width:1.0in;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">QoS</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">RETAIN</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBACK</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBREC</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBREL</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" style="width:.5in;border-top:none;border-left:none;border-bottom:
  solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">1</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBCOMP</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBSCRIBE</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">1</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UNSUBSCRIBE</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">1</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UNSUBACK</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PINGREQ</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PINGRESP</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
 <tr>
  <td width="121" valign="top" style="width:90.9pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">AUTH</p>
  </td>
  <td width="150" valign="top" style="width:112.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Reserved </p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="48" valign="top" style="width:.5in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

DUP = PUBLISH パケットの重複配信   
QoS = PUBLISH のサービスクオリティ   
RETAIN = メッセージフラグを保持した PUBLISH   
PUBLISH パケットにおける DUP、QoS、および RETAIN フラグの説明については、[section 3.3.1](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_CONNECT_Fixed_Header) を参照してください。

#### 2.1.4 Remaining Length
Position: 2 つ目のバイトから開始

Remaining Length は　Variable Byte Integer であり、Variable Header や Payload を含む、Control Packet 内の残りのバイト数を表します。Remaining Length は Remaining Length をエンコードするために使用されるバイトを含みません。パケットサイズは、MQTT Control Packet の合計バイト数であり、すなわち、Fixed Header の長さに Remaining Length を足したものです。

### 2.2 Variable Header
いくつかのタイプの MQTT Control Packet は Varriable Header コンポーネントを含みます。それは Fixed Header と Payload の間にあります。Variable Header の内容はパケットタイプによって異なります。Variable Header の Packet Identifier フィールドはいくつかのパケットタイプに共通です。

#### 2.2.1 Packet Identifier
多くの MQTT Control Packet タイプの Variable Header は Two Byte Integer Packet Identifier フィールドを含みます。これらの MQTT Control Packets は PUBLISH (QoS > 0)、PUBACK、PUBREC、PUBREL、PUBCOMP、SUBSCRIBE、SUBACK、UNSUBSCRIBE、UNSUBACK です。

Packet Identifier が必要な MQTT Control Packet は以下のとおりです。

Table 2‑3 MQTT Control Packets that contain a Packet Identifier
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" style="margin-left:.25in;border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b><span lang="EN-GB">MQTT Control Packet</span></b></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b><span lang="EN-GB">Packet Identifier field</span></b></p>
  </td>
 </tr>
 <tr style="height:17.05pt">
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt;height:17.05pt">
  <p class="MsoNormal"><span lang="EN-GB">CONNECT</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt;height:17.05pt">
  <p class="MsoNormal">NO</p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">CONNACK</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">NO</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PUBLISH</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">YES (If QoS &gt; 0)</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PUBACK</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">YES</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PUBREC</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">YES</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PUBREL</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">YES</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PUBCOMP</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">YES</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">SUBSCRIBE</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">YES</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">SUBACK</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">YES</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">UNSUBSCRIBE</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">YES</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">UNSUBACK</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">YES</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PINGREQ</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">NO</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PINGRESP</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">NO</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">DISCONNECT</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">NO</span></p>
  </td>
 </tr>
 <tr>
  <td width="129" valign="top" style="width:96.95pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">AUTH</span></p>
  </td>
  <td width="171" valign="top" style="width:128.15pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">NO</span></p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

QoS が 0 の場合、PUBLISH パケットは Packet Identifier を含んではなりません (MUST NOT) [MQTT-2.2.1-2]。

Client が新たな SUBSCRIBE、UNSUBSCRIBE、PUBLISH (QoS > 0) MQTT Control Packet を送信する時は、その時点で使用されていない 0 以外の Packet Identifier を設定しなくてはなりません (MUST) [MQTT-2.2.1-3]。

Server が新たな PUBLISH (QoS > 0) MQTT Control Packet を送信する時は、その時点で使用されていない 0 以外の Packet Identifier を設定しなくてはなりません (MUST) [MQTT-2.2.1-4]。

Packet Identifier は送信側が対応する確認応答パケット (以降で定義します) を処理した後に再利用可能となります。QoS 1 の PUBLISH には PUBACK が対応します。QoS 2 の PUBLISH には 128 以上の Reason Code を伴う PUBCOMP か PUBREC が対応します。SUBSCRIBE と UNSUBSCRIBE にはそれぞれ SUBACK と UNSUBACK が対応します。

PUBLISH、SUBSCRIBE、UNSUBSCRIBE パケットに伴って使用される Packet Identifier は、1 つのセッション内のクライアントとサーバーに対して個別に単一の統一された識別子のセットを形成します。1 つの Packet Identifier は 1 度に複数のコマンドで使用することはできません。

PUBACK、PUBREC、PUBREL、PUBCOMP パケットは、送信元である PUBLISH パケットと同じ Packet Identifier を含まなくてはなりません (MUST) [MQTT-2.2.1-5]。SUBACK と UNSUBACK は対応する SUBSCRIBE と UNSUBSCRIBE パケットにて使用された Packet Identifier を含まなくてはなりません (MUST) [MQTT-2.2.1-6]。

Client と Server は それぞれに依存しない Packet Identifiers を設定します。その結果、Client-Server ペアは同じ Packet Identifier を用いて同時にメッセージを交換することができます。

**非規範的なコメント**   
Client は Packet Identifire 0x1234 の PUBLISH パケットを送信し、対応する PUBACK を受け取る前に、Packet Identifire 0x1234 の PUBLISH パケットを Server から受け取ることが可能です。

```
Client                                                 Server

PUBLISH Packet Identifier=0x1234 ‒→

                                                      ←‒ PUBLISH Packet Identifier=0x1234

PUBACK Packet Identifier=0x1234 ‒→

                                                     ←‒ PUBACK Packet Identifier=0x1234
```

#### 2.2.2 Properties
CONNECT、CONNACK、PUBLISH、PUBACK、PUBREC、PUBREL、PUBCOMP、SUBSCRIBE、SUBACK、UNSUBSCRIBE、UNSUBACK、DISCONNECT、および AUTH パケットの Variable Header ーの最後のフィールドは、Properties のセットです。CONNECT パケットには、Payload のある Will Properties フィールドにオプションの Properties セットもあります。

Properties のセットは、Property Length とそれに続く Properties で構成されます。

##### 2.2.2.1 Property Length
Property Length は、Variable Byte Integer としてエンコードされます。Property Length には、それ自体のエンコードに使用されるバイトは含まれませんが、Properties の長さが含まれます。

##### 2.2.2.2 Property
Property は、その使用法とデータ型を定義する Identifier と、それに続く値で構成されます。Identifier は Varialbe Byte Integer としてエンコードされます。パケットタイプにとって適切でない Identifier を含むか、指定されたデータ型ではない値を含む Control Packet は、Malformed Packet となります。もし受け取った場合、[section 4.13](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#S4_13_Errors) でエラーハンドリングについて説明しているような Reason Code 0x81 (Malformed Packet) を含む CONNACK か DISCONNECT パケットを使用してください。Identifiers が異なる Properties の順序には特に意味がありません。

Table 2‑4 - Properties
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" width="685" style="width:513.9pt;border-collapse:collapse;border:none">
 <tbody><tr style="page-break-inside:avoid">
  <td width="95" colspan="2" valign="top" style="width:71.55pt;border:solid windowtext 1.0pt;
  background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>Identifier</b></p>
  </td>
  <td width="188" rowspan="2" valign="top" style="width:141.3pt;border:solid windowtext 1.0pt;
  border-left:none;background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>Name (usage)</b></p>
  </td>
  <td width="150" rowspan="2" valign="top" style="width:112.6pt;border:solid windowtext 1.0pt;
  border-left:none;background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>Type</b></p>
  </td>
  <td width="251" rowspan="2" valign="top" style="width:188.45pt;border:solid windowtext 1.0pt;
  border-left:none;background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>Packet / Will Properties</b></p>
  <p class="MsoNormal"><b>&nbsp;</b></p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>Dec</b></p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>Hex</b></p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">1</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x01</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Payload Format Indicator</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Byte</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH, Will Properties</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">2</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x02</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Message Expiry Interval</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Four Byte Integer</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH, Will Properties</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">3</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x03</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Content Type</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 Encoded String</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH, Will Properties</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">8</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x08</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Response Topic</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 Encoded String</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH, Will Properties</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">9</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x09</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Correlation Data</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Binary Data</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH, Will Properties</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">11</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x0B</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Subscription Identifier</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Variable Byte Integer</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH, SUBSCRIBE</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">17</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x11</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Session Expiry Interval</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Four Byte Integer</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">18</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x12</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">Assigned Client Identifier</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 Encoded String</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">19</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x13</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">Server Keep Alive</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Two Byte Integer</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">21</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x15</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">Authentication Method</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 Encoded String</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK, AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">22</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x16</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">Authentication Data</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Binary Data</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK, AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">23</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x17</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">Request Problem Information</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Byte</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">24</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x18</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">Will Delay Interval</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Four Byte Integer</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Will Properties</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">25</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x19</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Request Response Information</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Byte</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">26</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x1A</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">Response Information</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 Encoded String</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">28</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x1C</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">Server Reference </p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 Encoded String</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">31</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x1F</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">Reason String</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 Encoded String</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, PUBREL, PUBCOMP, SUBACK,
  UNSUBACK, DISCONNECT, AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">33</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x21</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Receive Maximum</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Two Byte Integer</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">34</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x22</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Topic Alias Maximum</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Two Byte Integer</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">35</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x23</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Topic Alias</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Two Byte Integer</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">36</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x24</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Maximum QoS</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Byte</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">37</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x25</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Retain Available</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Byte</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">38</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x26</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">User Property</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 String Pair</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK, PUBLISH, Will Properties, PUBACK,
  PUBREC, PUBREL, PUBCOMP, SUBSCRIBE, SUBACK, UNSUBSCRIBE, UNSUBACK,
  DISCONNECT, AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">39</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x27</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Maximum Packet Size</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Four Byte Integer</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">40</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x28</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Wildcard Subscription Available</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Byte</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">41</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x29</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Subscription Identifier Available</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Byte</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">42</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x2A</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Shared Subscription Available</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Byte</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

**非規範的なコメント**   
Property Identifier は Variable Byte Integer として定義されていますが、仕様の本バージョンにおいてはすべての Property Identifier は 1 バイト長です。

### 2.3 Payload
いくつかの MQTT Control Packets はパケットの最後の部分に Payload を持ちます。PUBLISH パケットでは、これはアプリケーションメッセージです。

Table 2‑5 - MQTT Control Packets that contain a Payload
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" style="margin-left:.25in;border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b><span lang="EN-GB">MQTT Control Packet</span></b></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b><span lang="EN-GB">Payload</span></b></p>
  </td>
 </tr>
 <tr style="height:17.05pt">
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt;height:17.05pt">
  <p class="MsoNormal"><span lang="EN-GB">CONNECT</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt;height:17.05pt">
  <p class="MsoNormal">Required</p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">CONNACK</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">None</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PUBLISH</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">Optional</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PUBACK</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">None</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PUBREC</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">None</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PUBREL</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">None</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PUBCOMP</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">None</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">SUBSCRIBE</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">Required</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">SUBACK</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">Required</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">UNSUBSCRIBE</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">Required</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">UNSUBACK</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">Required</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PINGREQ</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">None</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">PINGRESP</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">None</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">DISCONNECT</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">None</span></p>
  </td>
 </tr>
 <tr>
  <td width="124" valign="top" style="width:93.25pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">AUTH</span></p>
  </td>
  <td width="105" valign="top" style="width:78.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span lang="EN-GB">None</span></p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

### 2.4 Reason Code
Reason Code は 1 byte の符号なし値で、オペレーションの結果を示します。Reason Code 0x80 未満はオペレーションの成功を示します。通常の成功 Reason Code は 0 です。Reason Code 0x80 かそれ以上は失敗を示します。

CONNACK、PUBACK、PUBREC、PUBREL、PUBCOMP、DISCONNECT、および AUTH Control Packets には、Variable Header の一部として 1 つの Reason Code があります。 SUBACK と UNSUBACK パケットには、Payload 内に 1 つ以上の Reason Code のリストが含まれています。

Table 2‑6 - Reason Codes
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" width="673" style="width:504.9pt;border-collapse:collapse;border:none">
 <tbody><tr style="page-break-inside:avoid">
  <td width="113" colspan="2" valign="top" style="width:84.5pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Reason Code</b></p>
  </td>
  <td width="211" rowspan="2" valign="top" style="width:158.5pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Name</b></p>
  </td>
  <td width="349" rowspan="2" valign="top" style="width:261.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Packets</b></p>
  <p class="MsoNormal" align="center" style="text-align:center"><b>&nbsp;</b></p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Decimal</b></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Hex</b></p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x00</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Success</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, PUBREL, PUBCOMP, UNSUBACK, AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x00</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Normal disconnection</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x00</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Granted QoS 0</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">1</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x01</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Granted QoS 1</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">2</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x02</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Granted QoS 2</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">4</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x04</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Disconnect with Will Message</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">16</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x10</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">No matching subscribers</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBACK, PUBREC</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">17</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x11</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">No subscription existed</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UNSUBACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">24</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x18</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Continue authentication</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">25</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x19</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Re-authenticate</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">128</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x80</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Unspecified error</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, SUBACK, UNSUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">129</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x81</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Malformed Packet</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">130</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x82</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Protocol Error</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">131</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x83</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Implementation specific error</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, SUBACK, UNSUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">132</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x84</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Unsupported Protocol Version</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">133</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x85</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Client Identifier not valid</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">134</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x86</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Bad User Name or Password</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">135</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x87</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Not authorized</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, SUBACK, UNSUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">136</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x88</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Server unavailable</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">137</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x89</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Server busy</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">138</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8A</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Banned</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">139</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8B</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Server shutting down</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">140</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8C</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Bad authentication method</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">141</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8D</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Keep Alive timeout</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">142</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8E</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Session taken over</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">143</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8F</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Topic Filter invalid</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK, UNSUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">144</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x90</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Topic Name invalid</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">145</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x91</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Packet Identifier in use</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBACK, PUBREC, SUBACK, UNSUBACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">146</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x92</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Packet Identifier not found</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBREL, PUBCOMP</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">147</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x93</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Receive Maximum exceeded</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">148</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x94</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Topic Alias invalid</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">149</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x95</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Packet too large</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">150</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x96</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Message rate too high</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">151</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x97</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Quota exceeded</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, SUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">152</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x98</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Administrative action</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">153</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x99</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Payload format invalid</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">154</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9A</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Retain not supported</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">155</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9B</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">QoS not supported</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">156</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9C</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Use another server</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">157</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9D</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Server moved</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">158</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9E</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Shared Subscriptions not supported</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">159</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9F</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Connection rate exceeded</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">160</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0xA0</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Maximum connect time</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">161</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0xA1</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Subscription Identifiers not supported</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">162</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0xA2</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Wildcard Subscriptions not supported</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK, DISCONNECT</p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

**非規範的なコメント**   
Reason Code 0x91 (Packet identifier in use) の場合、これに対する応答は、状態を修正しようとするか、1 をセットする Clean Start を使って接続の Session 状態をリセットするか、Client または Server の実装に欠陥があるか判断することです。

## 3 MQTT Control Packets
### 3.1 CONNECT - Connection Request
Client から Server への Network Connection が確立されたあと、Client から Server へ送られる最初のパケットは CONNECT パケットでなくてはなりません (MUST) [MQTT-3.1.0-1]。

Client は 1 つの Network Connection にわたって、CONNECT パケットを一度のみ送信することができます。Server は 2 回目い Client から送信された CONNECT パケットを Protocol Error として扱い、Network Connection を閉じなくてはなりません (MUST) [MQTT-3.1.0-2]。エラーハンドリングについては [section 4.13](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#S4_13_Errors) を参照してください。

Payload は 1 つまたは複数のエンコードされたフィールドを含みます。それらは Client に対してユニークな Client 識別子、Will Topic、Will Payload、User Name そして Password を指定します。Client 識別子を除くすべてを省略でき、それらの存在は Variable Header のフラグに基づいて決定されます。

#### 3.1.1 CONNCET Fixed Header
Figure 3‑1 - CONNECT packet Fixed Header
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="106" valign="top" style="width:79.8pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Bit</b></p>
  </td>
  <td width="53" valign="top" style="width:39.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>7</b></p>
  </td>
  <td width="64" valign="top" style="width:47.7pt;border:solid windowtext 1.0pt;
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
  <td width="70" valign="top" style="width:52.8pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>3</b></p>
  </td>
  <td width="62" valign="top" style="width:46.2pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>2</b></p>
  </td>
  <td width="72" valign="top" style="width:.75in;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>1</b></p>
  </td>
  <td width="79" valign="top" style="width:59.4pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>0</b></p>
  </td>
 </tr>
 <tr>
  <td width="106" valign="top" style="width:79.8pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 1</p>
  </td>
  <td width="249" colspan="4" valign="top" style="width:186.6pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">MQTT Control Packet
  type (1)</p>
  </td>
  <td width="283" colspan="4" valign="top" style="width:2.95in;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Reserved</p>
  </td>
 </tr>
 <tr>
  <td width="106" valign="top" style="width:79.8pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">&nbsp;</p>
  </td>
  <td width="53" valign="top" style="width:39.9pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="64" valign="top" style="width:47.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="60" valign="top" style="width:45.0pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="70" valign="top" style="width:52.8pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="62" valign="top" style="width:46.2pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="72" valign="top" style="width:.75in;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="79" valign="top" style="width:59.4pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
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

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

**Remaining Length フィールド**   
これは Variable Header の長さと Payload の長さを足したものです。Variable Byte Integer でエンコードされます。

#### 3.1.2 CONNECT Variable Header
CONNECT Packet の Variable Header は次のフィールドを順番に持ちます。Protocol Name、Protocol Level、Connect Flags、Keep Alive、Properties です。

##### 3.1.2.1 Protocol Name
Figure 3‑2 - Protocol Name bytes
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" style="margin-left:-.05in;border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="111" valign="top" style="width:83.4pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">&nbsp;</p>
  </td>
  <td width="248" valign="top" style="width:186.2pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Description</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>7</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>6</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>5</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>4</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>3</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>2</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>1</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>0</b></p>
  </td>
 </tr>
 <tr>
  <td width="643" colspan="10" valign="top" style="width:6.7in;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Protocol Name</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.4pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 1</p>
  </td>
  <td width="248" valign="top" style="width:186.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Length MSB (0)</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.4pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 2</p>
  </td>
  <td width="248" valign="top" style="width:186.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Length LSB (4)</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.4pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 3</p>
  </td>
  <td width="248" valign="top" style="width:186.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">‘M’</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.4pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 4</p>
  </td>
  <td width="248" valign="top" style="width:186.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">‘Q’</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.4pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 5</p>
  </td>
  <td width="248" valign="top" style="width:186.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">‘T’</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.4pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 6</p>
  </td>
  <td width="248" valign="top" style="width:186.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">‘T’</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

Protocol Name は UTF-8 Encoded String であり、プロトコル名 "MQTT" を大文字で示します。プロトコル名は UTF-8 文字の "MQTT" でなくてはなりません (MUST)。もし Server が CONNECT を受け入れたくなく、MQTT Server であることを明らかにしたい場合は、Reason Code 0x84 (Unsupported Protocol Version) を持つ CONNACK パケットを送信する場合があり (MAY)、さらに Network Connection をクローズしなくてはなりません (MUST) [MQTT-3.1.2-1]。

**非規範的なコメント**   
ファイアウォールのようなパケットインスペクターは Protocol Name を MQTT トラフィックの識別に利用することができます。

##### 3.1.2.2 Protocol Version
Figure 3‑3 - Protocol Version byte
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" style="margin-left:-.05in;border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="111" valign="top" style="width:83.4pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">&nbsp;</p>
  </td>
  <td width="248" valign="top" style="width:186.2pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Description</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>7</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>6</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>5</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>4</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>3</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>2</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>1</b></p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>0</b></p>
  </td>
 </tr>
 <tr>
  <td width="643" colspan="10" valign="top" style="width:6.7in;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Protocol Level</p>
  </td>
 </tr>
 <tr>
  <td width="111" valign="top" style="width:83.4pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 7</p>
  </td>
  <td width="248" valign="top" style="width:186.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Version(5)</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
  <td width="35" valign="top" style="width:26.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">1</p>
  </td>
 </tr>
</tbody></table>

※ この表は[公式ページ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)のものを引用しています。

Client に使用されるプロトコルのリビジョンレベルを示す 1 byte の符号なし値です。プロトコルのバージョン 5 の Protocol Version フィールドは 5 (0x05) です。

MQTT プロトコルの様々なバージョンをサポートするサーバは、クライアントがどのバージョンの MQTT を使用しているか判断するために、Protocol Version を使用します。もし Protocol Version が 5 でなく、Server が CONNECT パケットを受け入れたくない場合、Server は CONNACK パケットを Reason Code 0x84 (Unsupported Protocol Version) で送信する場合があり (MAY)、Network Connection を閉じなくてはなりません (MUST) [MQTT-3.1.2-2]

3.1.2.3 Connect Flags
Connect Flags バイトは MQTT コネクションの振る舞いを指定するいくつかのパラメータを含みます。また、Payload のフィールドの有無も示します。

Figure 3‑4 - Connect Flag bits
<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border:none">
 <tbody><tr>
  <td width="65" valign="top" style="width:48.55pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>Bit</b></p>
  </td>
  <td width="85" valign="top" style="width:63.65pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>7</b></p>
  </td>
  <td width="84" valign="top" style="width:63.3pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>6</b></p>
  </td>
  <td width="85" valign="top" style="width:63.8pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>5</b></p>
  </td>
  <td width="47" valign="top" style="width:35.45pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>4</b></p>
  </td>
  <td width="47" valign="top" style="width:35.45pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>3</b></p>
  </td>
  <td width="76" valign="top" style="width:56.7pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>2</b></p>
  </td>
  <td width="78" valign="top" style="width:58.3pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>1</b></p>
  </td>
  <td width="71" valign="top" style="width:53.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>0</b></p>
  </td>
 </tr>
 <tr>
  <td width="65" valign="top" style="width:48.55pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">&nbsp;</p>
  </td>
  <td width="85" valign="top" style="width:63.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">User Name Flag</p>
  </td>
  <td width="84" valign="top" style="width:63.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Password Flag</p>
  </td>
  <td width="85" valign="top" style="width:63.8pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Will Retain</p>
  </td>
  <td width="95" colspan="2" valign="top" style="width:70.9pt;border-top:none;
  border-left:none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Will QoS</p>
  </td>
  <td width="76" valign="top" style="width:56.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Will Flag</p>
  </td>
  <td width="78" valign="top" style="width:58.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Clean Start</p>
  </td>
  <td width="71" valign="top" style="width:53.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">Reserved</p>
  </td>
 </tr>
 <tr>
  <td width="65" valign="top" style="width:48.55pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">byte 8</p>
  </td>
  <td width="85" valign="top" style="width:63.65pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">X</p>
  </td>
  <td width="84" valign="top" style="width:63.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">X</p>
  </td>
  <td width="85" valign="top" style="width:63.8pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">X</p>
  </td>
  <td width="47" valign="top" style="width:35.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">X</p>
  </td>
  <td width="47" valign="top" style="width:35.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">X</p>
  </td>
  <td width="76" valign="top" style="width:56.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">X</p>
  </td>
  <td width="78" valign="top" style="width:58.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">X</p>
  </td>
  <td width="71" valign="top" style="width:53.6pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center">0</p>
  </td>
 </tr>
</tbody></table>

Server は CONNECT パケット内のリザーブドフラグが 0 にセットされているか確認しなければなりません (MUST)。もしリザーブドフラグが 0 でない場合は Malformed Packet となります。エラーハンドリングについては [section 4.13](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#S4_13_Errors) を参照してください。 

##### 3.1.2.4 Clean Start
**Position:** Connect Flags バイトのビット 1

このビットは Connection が新たな Session を開始するか既存の Session を継続するかを示します。Session State の定義については [section 4.1](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Session_State) を参照してください。

Clean Start が 1 にセットされた CONNECT パケットを受け取った場合、Cient と Server は既存の Session を破棄して新しい Session を開始しなければなりません (MUST) [MQTT-3.1.2-4]。その結果、Clean Start に 1 がセットされた場合、CONNACK 内の Session Present フラグは常に 0 へセットされます。

Clean Start が 0 にセットされた CONNECT パケットを受け取り、Client Identifier に紐づく Session が存在する場合には、Server は既存の Session の状態に基づいて Client とのコミュニケーションを継続しなくてはなりません (MUST) [MQTT-3.1.2-5]。Clean Start が 0 にセットされた CONNECT パケットを受け取って、Client Identifier に紐づく Session が存在しない場合には、Server は新しい Session を作成しなければなりません (MUST) [MQTT-3.1.2-6]。