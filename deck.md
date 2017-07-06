# [fit] trading bot hero

### @danielepolencic

---

![inline 150%](./assets/uasabi-logo.pdf)

---

# once upon a time... :sparkles:

---

![fill](./assets/integer-investments.png)

---

# have you ever noticed?

---

![fill](./assets/transferwise-eur-gbp.jpg)

---

![fill](./assets/transferwise-eur-gbp-zoom.jpg)

---

![fill](./assets/transferwise-eur-gbp-zoom-pointed.jpg)

---

![fill](./assets/transferwise-gbp-eur.jpg)

---

![fill](./assets/transferwise-gbp-eur-zoom.jpg)

---

![fill](./assets/transferwise-gbp-eur-zoom-pointed.jpg)

---

# i want to send £1000
## __to :woman:__

---

# £1 :arrow_right: €1.13640

---

# :information_desk_person: €1136.40

---

# "i changed my mind"

---

# €1 :arrow_right: £0.88

---

# €1136.40 * 0.88

---

# £1000.032

---

# :scream: __£1000__.032 :scream:

---

# __surely__ it's just transferwise

---

![fill](assets/revolut-gbp-eur.jpg)

---

# £1 :arrow_right: €1.13612

---

# __BUY__ €1136.12 
## __price gbp/eur__ 1.13612

---

# €1 :arrow_right: £0.8802

---

# __SELL__ €1136.12
## __price eur/gbp__ 0.8802

---

# £1000.012

---

# :scream: __£1000__.012 :scream:

---

## this doesn't make any sense

---

# :swimmer: liquidity :shower:

^ how do Tw & Cf work?

---

# :money_with_wings: just pennies :money_with_wings:

---

![fit](assets/currencyfair-website.jpg)

---

![fit](assets/currencyfair-spread.jpg)

---

# __BUY__ @1.25
# __SELL__ @0.8032

---

# 1.25 __*__ 0.8032

---

# :scream: __£100__4 :scream:

---

# 2 transactions per day
# 20 days per month

---

# __2 * 20 * £4__ = £160[^*]

[^*]: best case scenario

---

# time to build something

---

# :sparkles: 3 wishes :sparkles:

---

# 1. :ear:

---

## :ear: to price changes

---

## __1.13612 * 0.88 =__ 0.99978 :x:
## __1.13612 * 0.882 =__ 1.00205 :white_check_mark:

---


## __:ear: to price changes__
## :ear: to order changes

---

## created
## modified
## partially matched
## matched in full

---

## __:ear: to price changes__
## __:ear: to order changes__
## :ear: to balance changes

---

## deposit :moneybag:
## withdraw :moneybag:
## orders matched in full 

---

## :clock11::clock9::clock7:
# 2. time travel

---

![fit](assets/timetravel.gif)

---

## :rewind: market conditions
## debugging & :mag:

---

# 3. :passport_control: bulletproof testing

---

![fit](assets/amazon-stock-crash.png)

---

> "As part of its normal process, the UTP distributed test data and certain third parties improperly propagated the data."
- NASDAQ

---

![](assets/tws.png)

---

# oops

---

# mvp :one:

---

## scalable architecture
## :rabbit:MQ
## mysql

^ ts 1.8

---

![fit](assets/mvp1-01.pdf)

---

![fit](assets/mvp1-02.pdf)

---

![fit](assets/mvp1-03.pdf)

---

![fit](assets/mvp1-04.pdf)

---

## 1. no need for multiple workers
## 2. hard to manage deployments
## 3. overengineered

^ syncing state means that I couldn't reboot the server easily
^ hard to test rabbitMQ. proxy-require modules

---

# mvp :two:

---

## redux
## leveldb
## docker

^ ts 1.9

---

![fit](assets/mvp2-01.pdf)

---

![fit](assets/logo-elm.png)
![fit](assets/logo-flux.png)
![fit](assets/logo-redux.png)

---

## 𝒻(state, message) -> state

---

```js
function update(state, message) {
  switch(action.type) {

  case ORDER_OPENED:
    /* ... */

  default:
  return state;
  }
}
```

---

```js
const message = {
  type: ORDER_OPENED,
  price: 1.3508
};

const state = {
  openOrders: []
};
```

---

## 1. syncing state
## 2. redux actions & orchestration

---

![fit](assets/state-01.pdf)

---

![fit](assets/state-02.pdf)

---

![fit](assets/state-03.pdf)

---

![fit](assets/state-04.pdf)

---

![fit](assets/state-05.pdf)

---

![fit](assets/state-06.pdf)

---

![fit](assets/fuu.jpg)

---

# Reducers are pure

---

# Bad :x:

```js
function(state, action) {
  switch(action.type) {
  case UPDATED_ORDER:
    fetch(`/order${action.payload.orderId}`);
    return state;
  }
}
```

---

#  Good :white_check_mark: 

```js
function doAction(dispatch) {
  dispatch({type: UPDATED_ORDER, id: '1'});

  fetch(`/order${action.payload.orderId}`).then(order => {
    dispatch({type: ORDER_RETRIEVED, order});
  });
}
```

---

# Good :white_check_mark:

```js
function(state, action) {
  switch(action.type) {
  case UPDATED_ORDER:
    /* update state */
    return state;
  }
  case ORDER_RETRIEVED:
    /* update state */
    return state;
  }
}
```

---

## 1. retrieve price
## 2. update order

---

```js
function(dispatch, getState) {
  const order = getState().order;

  dispatch({type: RETRIEVE_PRICE});

  fetch('/price').then(price => {
    if (price > order.price) {

      dispatch({type: UPDATE_ORDER, orderId: order.id, price});

      fetch('/update/order/{order.id}`, {price}).then(order => {
        dispatch({type: ORDER_UPDATED, order});
      });
    };
  });
}
```

---

# meanwhile in the state...

---

```js
function(state, message) {
  switch(action.type) {
  case RETRIEVE_PRICE:
    return {...state, isPending: true};
  case UPDATE_ORDER:
    return {...state, fetchingOrder: true};
  case ORDER_UPDATED:
    return {...state, order: action.order};
  }
}
```

---

# nah, it's not my problem

---

## redux saga
## redux thunk
## mobx + rxjs

---

# mvp :three:

---

## free monad
## redux-like state
## redis

^ ts 2.0, 2.1, 2.2

---

## 𝒻(state, msg) -> [state, cmd]

---

## 1. elm
## 2. redux-loop
## 3. redux-effects

---

```js
function(state, message) {
  switch(message.type) {
    case PRICE_UPDATED:
      if (message.price > state.currentPrice) {
        return [state, fetch(`/update/${state.order.id}`, {price})];
      } else {
        return [state, NONE];
      }
  }
}
```

---

# console.log(fetch(1));

```js
{
  type: 'FETCH',
  payload: {url: 1}
}
```

---

## state + logic :two_women_holding_hands:
## easier testing
## interpreter

---

![fit](assets/mvp3-01.pdf)

---

# getPricesNow()

---

![fit](assets/mvp3-02.pdf)

---

![fit](assets/mvp3-03.pdf)

---

![fit](assets/mvp3-04.pdf)

---

![fit](assets/mvp3-05.pdf)

---

![fit](assets/mvp3-06.pdf)

---

## monads are hard
## still hard to do integration tests
## decoupling effects - business logic

---

# mvp :three:.:one:

---

## redux-like state
## redis
## q architecture

^ ts 2.3, 2.4

---

## fully decoupled effects and business logic
## easier integration tests
## data driven tests
## 100 LoC

---

# Deep dive into architecture redux + effects for decoupling side effects

---

# Deep dive into testing

---

intro
- how I am
  - what I do
    - uasabi ltd
  - cristiano & cf
  - won't share details :D

3 main arguments:

1. basic trading in simple terms
  - what to trade
    - forex, stock, future
  - where to trade
  - basic exchanges, CF, bitstamp, IB
  - strategies
    - buy high, sell low
    - arbitrage
    - market making
    - roll your own (AI)

2. how to build application that trades on the market
  - event based
    - ticker feed
    - elm architecture
      - record/replay
  - why functional
    - no state
    - lambdas
  - redux architecture
    - CQRS
    - redux loop
    - actors & akka

3. how to test like a pro
  - why do you even bother
  - typescript as a testing tool
  - unit testing with tape
    - why not jest, ava, etc.
  - unit testing with data
  - integration
  - snapshot testing
    - record/replay
  - ci/cd

outro
- what does it look like?
  - does it work?
  - next?
  - what I learned
    - testing is key
    - typescript is great
    - finance is simple
