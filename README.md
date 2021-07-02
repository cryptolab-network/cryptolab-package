# cryptolab-package

This repo contains the docker files of CryptoLab website for and only for W3f Open Grant Program Commitee to inspect and verify the result of the proposal https://github.com/w3f/Open-Grants-Program/blob/master/applications/cryptolab-staking-reward-collector-front-end.md 

## Docker Usage

To start the website,

* docker-compose build
* docker-compose up

Then you can open the url http://127.0.0.1:3030/tools/dotSR to enter the Staking Rewards tool. 

* Copy and paste either a Polkadot or a Kusama address on the *Search for Stash ID* field

![](https://i.imgur.com/TUuTnu6.png)

You can see the rewards of the stash.

![](https://i.imgur.com/AvIrxGi.png)

You can change the search criteria by clicking the filter icon.

![](https://i.imgur.com/winbBE8.png)

You can download the rewards report of either .csv and .json by clicking the download icon

![](https://i.imgur.com/nDG2Wzz.png)


If you entered an incorrect address or a stash with no rewards collected, the page would show a snakebar to tell you that CryptoLab cannot find the result.

![](https://i.imgur.com/Ms0xoWc.png)

## Setup from Source code

To compile and run CryptoLab website, you need to clone the following projects from github.

Polkadot Staking Site (https://github.com/cryptolab-network/polkadot-staking-site)

CryptoLab WebServer (https://github.com/cryptolab-network/cryptolab-web-server)

Chain Data Collector (https://github.com/cryptolab-network/chain-data-collector)

Staking Rewards Collector (https://github.com/w3f/staking-rewards-collector)

You can clone them to a same root folder, for example,

root

 |
 
 -> polkadot-staking-site
 
 -> cryptolab-web-server
 
 -> chain-data-collector
 
 -> staking-rewards-collector

and compile each by

* Polkadot Staking Site

1. ```npm install```
2. ```npm run build```
3. Copy the content in ```./dist``` folder to ```/www/static``` in cryptolab-web-server folder 

```
cd ./dist
cp -r . ../../cryptolab-web-server/www/static
```

* CryptoLab WebServer

1. ```cargo build --release```
2. rename `sample.json` in `./config` to `config.json` or create your own config file.

```json=
{
    "db_address": "127.0.0.1",
    "db_port": 27017,
    "kusama_db_name": "kusama",
    "polkadot_db_name": "polkadot",
    "db_has_credential": false,
    "db_username": "",
    "db_password": "",

    "port": 3030,
    "cors_url": ["http://127.0.0.1:3030", "http://localhost:3030", "http://127.0.0.1:8080"],

    "new_cache_folder": "../chain-data-collector/cache/kusama",
    "new_cache_folder_polkadot": "..//chain-data-collector/cache/polkadot",

    "staking_rewards_collector_dir": "../staking-rewards-collector",
    "serve_www": true
}

```

* Chain Data Collector

1. ```npm install```
2. ```npm run build```
3. rename `sample.json` in `./config` folder to `dev.json` or create your own `dev.json`.

```js=
module.exports = {
  PORT: 3000,
  KUSAMA_WSS: 'wss://kusama.api.onfinality.io/public-ws',
  POLKADOT_WSS: 'wss://polkadot.api.onfinality.io/public-ws',
  API_1KV_KUSAMA: 'https://kusama.w3f.community',
  API_1KV_POLKADOT: 'https://polkadot.w3f.community',
  MONGO_ACCOUNT: '',
  MONGO_PASSWORD: '',
  MONGO_URL: '127.0.0.1',
  MONGO_PORT: 27017,
  MONGO_DBNAME: 'kusama',
  MONGO_DBNAME_POLKADOT: 'polkadot',
  PAGE_SIZE: 1500,
}
```
detailed config information can be found in the README in each repo.

* Staking Rewards Collector

1. ```npm install```

* Mongo DB

You have to setup your own MongoDB in order to run the whole project. We use *MongoDB Community Server v4.4.4*

## Run

Follow these steps to start the CryptoLab website,

1. Start mongoDB. (e.g. mongod --dbpath .\\data\\cryptoLab)
2. Go to ```./chain-data-collector``` and run ```npm run startKusama``` and ```npm run startPolkadot```
3. Go to ```./cryptolab-web-server``` and run ```cargo run --release```
4. Go to http://127.0.0.1:3030/tools/dotSR on your browser and you would see the staking rewards collector webpage

![](https://i.imgur.com/vGCBWVo.png)

## Test

1. rename `sample.json` in `./config` to `config.json` or create your own config file.

2. Go to ```./cryptolab-web-server``` and run ```cargo test```

## System Architecture

The CryptoLab website is composed of several services.

* Chain Data Collector
This repo contains the services to collect chain data from both chains.

* CryptoLab Web Server
This repo serves as the web server of CryptoLab

* Polkadot Staking Site
This repo contains the front end of the CryptoLab

* Staking Rewards Collector
This is an external repo which the CryptoLab used for collecting staking rewards

![](https://i.imgur.com/OnspTWE.png)
