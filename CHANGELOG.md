# Changelog

All notable changes to this project will be documented in this file. See [standard-version](https://github.com/conventional-changelog/standard-version) for commit guidelines.

> This project is maintained by [MadDogWarner](https://maddogwarner.com) at [github.com/MaddogWarner/homebridge-plugin-withings-sleep-MDW](https://github.com/MaddogWarner/homebridge-plugin-withings-sleep-MDW).
> Forked from [dvcol/homebridge-plugin-withings-sleep](https://github.com/dvcol/homebridge-plugin-withings-sleep) — prior release history below is preserved from the original repository.

## [2.0.0](https://github.com/MaddogWarner/homebridge-plugin-withings-sleep-MDW/releases/tag/v2.0.0) (2026-05-24)


### Breaking Changes

* **runtime:** require Node.js 22.12 or 24.0 and later (was 18.17 or 20.9)
* **schema:** port config type changed from string to integer — update `config.json` to use a bare number (e.g. `3000` not `"3000"`)
* **server:** update Fastify from version 4 to version 5


### Features

* **homebridge:** add Homebridge 2.0 support while retaining Homebridge 1.6 compatibility
* **schema:** define the webhook server port as an integer with a numeric default
* **server:** validate port, SSL certificate, and SSL key configuration before startup


### Bug Fixes

* **deps:** fix the invalid `@types/node` version and update Vitest coverage packages
* **homebridge:** handle asynchronous server startup errors from the accessory constructor
* **metadata:** report Withings manufacturer and Sleep Analyzer model information
* **server:** correct the unknown error fallback message


### Security

* **config:** reject invalid webhook listener ports outside the TCP port range
* **ssl:** reject incomplete or empty SSL certificate and key path configuration
* **logging:** reduce Fastify request logging to warnings and errors by default

### [1.2.1](https://github.com/dvcol/homebridge-plugin-withings-sleep/compare/v1.2.0...v1.2.1) (2024-05-24)


### Bug Fixes

* **schema:** change default alias ([354ec54](https://github.com/dvcol/homebridge-plugin-withings-sleep/commit/354ec54109e5e52be13a4ea37ec543743c6bdfbd))

## [1.2.0](https://github.com/dvcol/homebridge-plugin-withings-sleep/compare/v1.1.0...v1.2.0) (2024-05-24)


### Features

* **server:** adds ssl support ([03111dd](https://github.com/dvcol/homebridge-plugin-withings-sleep/commit/03111ddd31df70eec9da04f9269c3e3bea4f4707))

## 1.1.0 (2024-05-24)


### Features

* **init:** initial commit (repo setup) ([69b86d7](https://github.com/dvcol/homebridge-plugin-withings-sleep/commit/69b86d797f98d5484c03ca6f86bedfd89f3df0b6))
* **plugin:** create switch and occupancy plugins ([65b94bc](https://github.com/dvcol/homebridge-plugin-withings-sleep/commit/65b94bccbe0aa9b66f9aa9c77d6cb60196203db5))


### Bug Fixes

* **lint:** eslint config and dependencies ([7b850ee](https://github.com/dvcol/homebridge-plugin-withings-sleep/commit/7b850ee8b1dea8d8f050a22e3be33a8dd6728220))

## 1.2.0 (2024-05-24)


### Features

* **init:** initial commit (repo setup) ([69b86d7](https://github.com/dvcol/homebridge-plugin-withings-sleep/commit/69b86d797f98d5484c03ca6f86bedfd89f3df0b6))
* **plugin:** create switch and occupancy plugins ([8be87e6](https://github.com/dvcol/homebridge-plugin-withings-sleep/commit/8be87e6bc92ee0c88143c34ac6135734252e6f52))


### Bug Fixes

* **lint:** eslint config and dependencies ([7b850ee](https://github.com/dvcol/homebridge-plugin-withings-sleep/commit/7b850ee8b1dea8d8f050a22e3be33a8dd6728220))

## 1.1.0 (2024-05-24)


### Features

* **init:** initial commit (repo setup) ([69b86d7](https://github.com/dvcol/homebridge-plugin-withings-sleep/commit/69b86d797f98d5484c03ca6f86bedfd89f3df0b6))
* **plugin:** create switch and occupancy plugins ([de6a4e8](https://github.com/dvcol/homebridge-plugin-withings-sleep/commit/de6a4e807dfe63049d3ed9c0ba31f2c8da23a84a))


### Bug Fixes

* **lint:** eslint config and dependencies ([7b850ee](https://github.com/dvcol/homebridge-plugin-withings-sleep/commit/7b850ee8b1dea8d8f050a22e3be33a8dd6728220))
