# Exchange Rates API

This is an effort to make a scalable alternative to the fixer.io API that will be discontinued on June 1st, 2018.

Exchange rates API is a free service for current and historical foreign exchange rates [published by the European Central Bank](https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/index.en.html).

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/madisvain/exchangeratesapi)

## Usage

Get the latest foreign exchange rates.

```http
GET /latest
```

Get historical rates for any day since 1999.

```http
GET /2018-03-26
```

Rates are quoted against the Euro by default. Quote against a different currency by setting the base parameter in your request.

```http
GET /latest?base=USD
```

Request specific exchange rates by setting the symbols parameter.

```http
GET /latest?symbols=USD,GBP
```

The primary use case is client side. For instance, with [money.js](https://openexchangerates.github.io/money.js/) in the browser

```js
let demo = () => {
  let rate = fx(1).from("GBP").to("USD")
  alert("£1 = $" + rate.toFixed(4))
}

fetch('https://exchangeratesapi.io/api/latest')
  .then((resp) => resp.json())
  .then((data) => fx.rates = data.rates)
  .then(demo)
```

## Stack

Exchange rates API is built upon Sanic to achieve high throughput. The current setup can asyncronously handle thousands of requests per second.

#### Libraries used
* [Sanic](https://github.com/channelcat/sanic)
* [GINO](https://github.com/fantix/gino)
* [asyncpg](https://github.com/MagicStack/asyncpg)
* [requests](https://github.com/requests/requests)
* [APScheduler](https://github.com/agronholm/apscheduler)
* [uvloop](https://github.com/MagicStack/uvloop)
* [ultraJSON](https://github.com/esnme/ultrajson)

## Deployment
#### Virtualenv
```shell
pipenv shell
```

#### Install packages
```shell
pipenv install
```

#### Load in initial data & Scheduler
The scheduler will keep your database up to date hourly with information from European Central bank. It will download the last 90 days worth of data every hour.

_The reference rates are usually updated around 16:00 CET on every working day, except on TARGET closing days. They are based on a regular daily concertation procedure between central banks across Europe, which normally takes place at 14:15 CET._

On initialization it will check the database. If it's empty all the historic rates will be downloaded and records created in the database.

## Development
```shell
gunicorn exchangerates.api:app --reload
```

## Contributing
Thanks for your interest in the project! All pull requests are welcome from developers of all skill levels. To get started, simply fork the master branch on GitHub to your personal account and then clone the fork into your development environment.

Madis Väin (madisvain on Github, Twitter) is the original creator of the Exchange Rates API framework.

## License
MIT