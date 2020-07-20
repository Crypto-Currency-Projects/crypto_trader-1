# crypto_trader - Risk-Free Arbitrage trader
![](https://www.bookjournalism.com/contents/download/52739/w/2400/bitcoin_imperialism.gif)

> 21 세기 新 비트제국의 디지털 아오지 탄광의 1 급 모범수들. created : 2019-08-07

## crypto currency auto trader project.
- 크립토 커런시 마켓 매매 자동화 프로젝트다. 
- 여러개의 버전을 개발할 예정이지만 이 repository 는 `무위험 차익거래 봇 (Risk-Free Arbitrage trader bot)` 제작 목적이다. 
- 이 프로젝트는 private 으로 운영된다.

> 적극적 공세, 대담한 찬탈 - Aggressive offensive, Daring usurpation

## Team
- M.S. Kim - 메인 개발
- S.B. Park - advisor / 테스터

## 계획 및 진행상황
-  유동적으로 진행한다.
- [x] public api wrapping - 새로 작업하는 중
- [ ] private api wrapping (각 거래소마다)
- [ ] dashboard design
- [x] trade strategy
- [ ] telegram messenger 기능 추가
- [ ] public api wrapping (websocket api)

## 핵심 설계

### public API (old)
- [cryptowatch](https://cryptowat.ch) 의 API 를 이용해전세계 거래소의 시세 데이터를 받아온다.

### public API (new)
- 각 거래소마다 제공하는 websocket api 로 갈아탐. 
- 실시간 streaming 데이터 획득 가능. 
- rest API 에 비해 유연하고 빠른 속도 보장. 

### public API interface (old)

* 모든 메서드는 [거래소], [종목] 을 인자로 받는다.
* 반환타입은 [거래소], [종목] 으로 2 d List 형식이다.
* 환율정보는 KRW 기준, 인자로는 JPY, EUR, USD 등이 있고 default 는 USD.

`call_api` : API call 하고, 데이터를 저장해놓는 메서드. 

`call_api_rarely` : 1 시간마다 1 번 API call 하는 메서드. 

`get_prices_summary` : 통합 가격상세 정보 반환하는 메서드. 우선 최종가, 거래량만 반환.

`get_orderbooks` : 통합 호가정보 반환 메서드.

`get_trades` : 통합 거래 내역 반환하는 메서드. 우선 가격, 수량만 반환.

`get_price_summary` : 주어진 거래소의 주어진 코인의 price summary 반환하는 메서드. - 내부적으로만 쓰임

`get_orderbook` : 주어진 거래소의 주어진 코인의 10 개의 orderbook 반환하는 메서드. - 내부적으로만 쓰임.

`get_trade` : 주어진 거래소의 주어진 코인의 10 개의 거래 내역 반환하는 메서드. - 내부적으로만 쓰임.

`get_prices` : 통합 시세 반환 메서드 - 오로지 price 만 반환함. `get_price_summary` 와 다르다.

`get_exchange_rates` : 통합 환율 정보 반환 메서드. - 업비트 회사에서 가져옴 - 후에 타 공식 증권사에서 가져오기. default 로 usd


### public API interface (new)

- `WS_public`

* 모든 메서드는 [거래소], [종목] 을 인자로 받는다.

`connect` : 최초로, 주소를 이용해 웹소켓에 접속

`subs_orderbooks` : 실시간 orderbooks 데이터를 받기위해 subscribe

`subs_prices` : 실시간 prices 데이터를 받기위해 subscribe

`unsubs_orderbooks` : unsubscribe

`unsubs_prices` : unsubscribe

`recv_orderbooks` : orderbooks 데이터를 수신하고 반환

`recv_prices` : prices 데이터를 수신하고 반환



### private API
- 각 거래소를 뚫어 API key 를 발급받아 거래 및 출금, 지갑조회가 가능하게 한다.
- 현재 지원하는 거래소로는
1. [Poloniex](https://poloniex.com/) (us)
2. [Kraken](https://www.kraken.com/) (us)
3. [Coinbase](https://www.coinbase.com/) (us)
4. [Bithumb](https://www.bithumb.com/) (kr)
5. [CoinOne](https://coinone.co.kr/) (kr)
6. [Binance](https://www.binance.com/en) (ch) - **인증완료**

- 등이 있고 추후 major 거래소들을 추가할 예정이다.
- 거래소마다 크고 작은 차이점이 많다.
- 차익거래 및 동시거래를 하나의 Application 에서 구동하기위해서, 공통된 통일 interface wrapper 가 필요하다.

### private API interface

* intergrated API
```
├─ Base machine (abstract)
│  ├─ coinone machine
│  ├─ bithumb machine
│  ├─ poloniex machine
│  ├─ kraken machine
│  ├─ coinbase machine
│  ├─ ...
```

* API interface - 각 거래소 machine 은 아래 메서드를 가진다.

`get_sign` : 암호화 sign 을 반환하는 메서드. (주로 US 거래소에서 API 요청시마다 요구)

`get_nonce` : nonce 를 반환하는 메서드. (대부분의 거래소에서 API 요청시 요구. 주로 현재시각에 해당)

`get_user` : 사용자 정보를 반환하는 메서드. 

`authentication` : (필요하면) 인증과정을 수행하는 메서드. 

`set_token` : 접근 token 을 발급받는 메서드. (주로 KR 거래소에서 요구)

`get_token` : 접근 token 을 반환하는 메서드.


`get_balances` : 모든 지갑 잔고를 반환하는 메서드. 

`buy_order` : 매수주문을 하는 메서드. (거래타입 - limit, 지정가, 시장가 등등, 수량, 가격)

`sell_order` : 매도주문을 하는 메서드. (거래타입 - stop, 지정가, 시장가 등등, 수량, 가격)

`cancel_order` : 취소주문을 하는 메서드. (주문번호로 수행)

`move_order` : 수정주문을 하는 메서드. (주문번호로 수행)

`get_order_info` : 주문정보를 반환하는 메서드. (주문번호로 수행)

`get_closed_orders` : 체결완료된 주문 내역들을 반환하는 메서드. (과거 최대 N 개까지 표시)

`get_open_orders` : 미체결 주문건들을 반환하는 메서드. (모든 미체결 주문을 반환)

`get_history_deposit_withdraw` : 입금/출금 내역들을 반환하는 메서드. (특정 asset_pair 의 과거 최대 N 개까지 표시)


### messenger bot
- [Telegram](https://play.google.com/store/apps/details?id=org.telegram.messenger&hl=en), [Slack](https://slack.com/intl/en-kr/), [Discord](https://discordapp.com/), [Kakao](https://www.kakaocorp.com/service/KakaoTalk) 등 major 메신저와 연동하여 모바일로 결과를 받아볼수 있게 한다.
- 현재 [telethon](https://docs.telethon.dev/en/latest/) 모듈을 이용해 Telegram 메신저 연동을 테스트 완료했다.

### Trading Strategy
- 가장 핵심이 되는 거래 전략이다.
- 중요한 점은 손해를 보지않으며 차익을 얻는 거래법이다.
- 거래소간 가격차이를 이용한 거래법.
- 1 회성으로 그치는 것이 아닌 24 시간 동안 간헐적으로 거래가 일어날 수 있는 조건을 계속 찾게 설계가 되어야 함.
- 전략에 대한 상세한 내용은 [전략 문서 링크](https://docs.google.com/document/d/1vKnFJsS_PIH_2j24CitSS6IlUocWW0ajVdg6lqAyphM/edit?usp=sharing) 에 기술되어 있음.