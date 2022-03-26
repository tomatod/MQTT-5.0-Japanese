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
MQTT で使用されている、基盤となるトランスポートプロトコルによって提供される構造のこと。
- Client を Server に接続する
- 整列され、損失が少ないバイトストリームを、双方向に送信する手段を提供する。
[セクション 4.2](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#:~:text=Refer%20to-,section%204.2,-Network%20Connection%20for) 非規範的なNetwork Connectionを参照してください。

#### Application Message:   
アプリケーションのために、MQTT プロトコルによってネットワーク間で転送されるデータのこと。Application Message が MQTT によって転送される際、それはペイロードデータ、Quality of Service (QoS)、プロパティのコレクション、およびトピック名を含みます。

#### Client:   
** MQTT を使うプログラムまたはデバイスのこと。 **
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
[RFC2119]

Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119, DOI 10.17487/RFC2119, March 1997,

http://www.rfc-editor.org/info/rfc2119

 

[RFC3629]

Yergeau, F., "UTF-8, a transformation format of ISO 10646", STD 63, RFC 3629, DOI 10.17487/RFC3629, November 2003,

http://www.rfc-editor.org/info/rfc3629

 

[RFC6455]

Fette, I. and A. Melnikov, "The WebSocket Protocol", RFC 6455, DOI 10.17487/RFC6455, December 2011,

http://www.rfc-editor.org/info/rfc6455

 

[Unicode]

The Unicode Consortium. The Unicode Standard,

http://www.unicode.org/versions/latest/

### 1.4 非引用規格
[RFC0793]

Postel, J., "Transmission Control Protocol", STD 7, RFC 793, DOI 10.17487/RFC0793, September 1981, http://www.rfc-editor.org/info/rfc793

 

[RFC5246]

Dierks, T. and E. Rescorla, "The Transport Layer Security (TLS) Protocol Version 1.2", RFC 5246, DOI 10.17487/RFC5246, August 2008,

http://www.rfc-editor.org/info/rfc5246

 

[AES]

Advanced Encryption Standard (AES) (FIPS PUB 197).

https://csrc.nist.gov/csrc/media/publications/fips/197/final/documents/fips-197.pdf

 

[CHACHA20]

ChaCha20 and Poly1305 for IETF Protocols

https://tools.ietf.org/html/rfc7539

 

[FIPS1402]

Security Requirements for Cryptographic Modules (FIPS PUB 140-2)

https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf

 

[IEEE 802.1AR]

IEEE Standard for Local and metropolitan area networks - Secure Device Identity

http://standards.ieee.org/findstds/standard/802.1AR-2009.html

 

[ISO29192]

ISO/IEC 29192-1:2012 Information technology -- Security techniques -- Lightweight cryptography -- Part 1: General

https://www.iso.org/standard/56425.html

 

[MQTT NIST]

MQTT supplemental publication, MQTT and the NIST Framework for Improving Critical Infrastructure Cybersecurity

http://docs.oasis-open.org/mqtt/mqtt-nist-cybersecurity/v1.0/mqtt-nist-cybersecurity-v1.0.html

 

[MQTTV311]

MQTT V3.1.1 Protocol Specification

http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html

 

[ISO20922]

MQTT V3.1.1 ISO Standard (ISO/IEC 20922:2016)

https://www.iso.org/standard/69466.html

 

[NISTCSF]

Improving Critical Infrastructure Cybersecurity Executive Order 13636

https://www.nist.gov/sites/default/files/documents/itl/preliminary-cybersecurity-framework.pdf

 

[NIST7628]

NISTIR 7628 Guidelines for Smart Grid Cyber Security Catalogue

https://www.nist.gov/sites/default/files/documents/smartgrid/nistir-7628_total.pdf

 

[NSAB]

NSA Suite B Cryptography

http://www.nsa.gov/ia/programs/suiteb_cryptography/

 

[PCIDSS]

PCI-DSS Payment Card Industry Data Security Standard

https://www.pcisecuritystandards.org/pci_security/

 

[RFC1928]

Leech, M., Ganis, M., Lee, Y., Kuris, R., Koblas, D., and L. Jones, "SOCKS Protocol Version 5", RFC 1928, DOI 10.17487/RFC1928, March 1996,

http://www.rfc-editor.org/info/rfc1928

 

[RFC4511]

Sermersheim, J., Ed., "Lightweight Directory Access Protocol (LDAP): The Protocol", RFC 4511, DOI 10.17487/RFC4511, June 2006,

http://www.rfc-editor.org/info/rfc4511

 

[RFC5280]

Cooper, D., Santesson, S., Farrell, S., Boeyen, S., Housley, R., and W. Polk, "Internet X.509 Public Key Infrastructure Certificate and Certificate Revocation List (CRL) Profile", RFC 5280, DOI 10.17487/RFC5280, May 2008,

http://www.rfc-editor.org/info/rfc5280

 

[RFC6066]

Eastlake 3rd, D., "Transport Layer Security (TLS) Extensions: Extension Definitions", RFC 6066, DOI 10.17487/RFC6066, January 2011,

http://www.rfc-editor.org/info/rfc6066

 

[RFC6749]

Hardt, D., Ed., "The OAuth 2.0 Authorization Framework", RFC 6749, DOI 10.17487/RFC6749, October 2012,

http://www.rfc-editor.org/info/rfc6749

 

[RFC6960]

Santesson, S., Myers, M., Ankney, R., Malpani, A., Galperin, S., and C. Adams, "X.509 Internet Public Key Infrastructure Online Certificate Status Protocol - OCSP", RFC 6960, DOI 10.17487/RFC6960, June 2013,

http://www.rfc-editor.org/info/rfc6960

 

[SARBANES]

Sarbanes-Oxley Act of 2002.

http://www.gpo.gov/fdsys/pkg/PLAW-107publ204/html/PLAW-107publ204.htm

 

[USEUPRIVSH]

U.S.-EU Privacy Shield Framework

https://www.privacyshield.gov

 

[RFC3986]

Berners-Lee, T., Fielding, R., and L. Masinter, "Uniform Resource Identifier (URI): Generic Syntax", STD 66, RFC 3986, DOI 10.17487/RFC3986, January 2005,

http://www.rfc-editor.org/info/rfc3986

 

[RFC1035]

Mockapetris, P., "Domain names - implementation and specification", STD 13, RFC 1035, DOI 10.17487/RFC1035, November 1987,

http://www.rfc-editor.org/info/rfc1035

 

[RFC2782]

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