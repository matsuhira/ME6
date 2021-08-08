# ME6について


## ME6とは

MACアドレスを、IPv6アドレスにマッピングしたME6アドレス(ME6A)を用いて、Ethernet over IPv6カプセル化を行う技術です。<br>

MACアドレスの重複やテナントに対応するために、ME6Aは、plane-IDを含みます。 <br>
現実的は、MACアドレスは48bitとすれば、
IPv6 Prefixを64bitとすると、Plane-IDには16bit割り当て可能ですので、2^16 = 約6万のテナントネットワークの収容を可能にします。
更に多くのテナントを収容したければ、IPv6 prefixを48bitとすれば、Plane-IDに32bit割り当てできますので、2^32=約42億のテナントネットワークの収容が可能になります。<br>
以下が、ME6Aのアドレスフォーマットです。仕様的には、EUI-48とEUI-64の双方を対象にしています。<br>

````
|    128 - m -n bits    |        m bits        |       n bits     |
+-----------------------+----------------------+------------------+
|      ME6A prefix      |  Ethernet plane ID   | Ethernet address |
+-----------------------+----------------------+------------------+
````

なお、ME6は、Multiple Ethernet - IPv6 address mappingの略、ME6Aは、Multiple Ethernet - IPv6 mapped IPv6 addressの略です。<br>

## ME6Eとは

ME6Aを用いて、Ethernet over IPv6カプセル化に適用した技術です。EthernetフレームにIPv6ヘッダを付与するカプセル化を行いますが、その際、IPv6アドレスとしてME6Aを用います。
EthernetによるL2の通信を、IPv6にマッピングしたIPv6アドレスを用いることにより、IPv6の空間での通信を可能にします。 <br>

Ethernetの通信は、フラッティングが基本で、学習による枝刈りをして、その結果、ユニキャストの通信を実現します。
ME6Eは、このEthernetの通信を、IPルーティングによるユニキャストの通信で実現します。<br>

ME6Eは、ME6Eの経路を広告するか否かで、ME6Aの生成方法が異なります。経路広告する場合はME6E-FP、経路広告しない場合はME6E-PRと呼びます。<br>

なお、ME6Eは、Multiple Ethernet - IPv6 address mapping encapsulationの略です。<br>

#### ME6E-FP

ME6E-FPは、ME6AのIPv6 prefixに、ME6A専用のprefixを割り当てるものです。<br>

ME6Aのprefixを固定にすることにより、テナントネットワーク/L2VPNが属するplane-IDと、MACアドレスが決まれば、ME6Aアドレスが決まります。 
すなわち、ME6E-FPで必要となる設定は、ME6A prefixと自身のplane-IDとMACアドレスの情報のみです。 <br>
通信相手のME6Aは宛先MACアドレスから自動的に生成できます。そして、通信相手への経路情報は、ルーティングプロトコルで伝えます。 
ルーティングプロトコルは任意で、OSPFv3でも、IS-ISでも、BGPでも利用可能です。現実的かはともかく、スタティックルーティングでも動作する筈です。<br>

設定は少なく済むメリットがありますが、MACアドレスへの経路をIPv6アドレスにマッピングして経路広告する必要がありますので、MACアドレス数分の経路が追加になります。<br>

なお、ME6E-FPは、Multiple Ethernet - IPv6 address mapping encapsulation - fixed prefixの略です。<br>

#### ME6E-PR
ME6E-PRは、IPv6の経路広告をしない前提であり、MACアドレスが接続されるIPv6ネットワークのIPv6 prefixをテーブルに保持しておくことにより、 
宛先MACアドレスが接続されるIPv6 prefixを解決してME6Aを求めるものです。IPv6が利用可能であることが前提で、IPv6通信を実現する経路広告に、 
Ethernetの通信を乗せてやるというイメージです。<br>

ME6Aの経路広告を行いませんので、エンドユーザ主導で実現できますが、MACアドレスと、そのサブネットが接続されるIPv6アドレスのprefixの対応関係を 
テーブルで管理する必要があります。ただ、この対応関係は、そのplaneの利用者で同一の情報になりますので、設定簡易化の作りこみの余地があります。<br>

ME6E-PRは、Neighbour DiscoveryのNeighbour Advertisementの解決により、 IPv6パケットを受信します。

ME6E-PRによるEthernetフレームの通信は、MACアドレスに対応するME6Aのアドレス解決が図られた際、ME6E-PRは代理応答を行い、該当するME6A宛てのIPv6パケットを 
受信します。受信後は、IPv6ヘッダを外し、デカプセル化し、Ethernetフレームを取り出し、ノードに送信します。

なお、ME6E-PRは、Multiple Ethernet - IPv6 address mapping encapsulation - prefix resolutionの略です。

