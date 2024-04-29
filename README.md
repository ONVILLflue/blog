# TelegramBridge/

[![npm version](https://img.shields.io/npm/v/@tgbridge/tg-core.svg?color=blue)](https://www.npmjs.com/package/@tgbridge/tg-core)
[![Downloads](https://img.shields.io/npm/dm/@tgbridge/tg-core.svg)](https://www.npmjs.com/package/@tgbridge/tg-core)
[![Average time to resolve an issue](https://isitmaintained.com/badge/resolution/telegram-bridge/tg-core.svg)](https://isitmaintained.com/project/tgbridge/tg-core 'Average time to resolve an issue')
[![Percentage of issues still open](https://isitmaintained.com/badge/open/telegram-bridge/tg-core.svg)](https://isitmaintained.com/project/tgbridge/tg-core 'Percentage of issues still open')

[![Build Status](https://img.shields.io/github/actions/workflow/status/telegram-bridge/tg-core/compile.yml?branch=master)](https://github.com/telegram-bridge/tg-core/actions/workflows/compile.yml)
[![Build Status](https://img.shields.io/github/actions/workflow/status/telegram-bridge/tg-core/verify.yml?branch=master)](https://github.com/telegram-bridge/tg-core/actions/workflows/verify.yml)
[![Lint Status](https://img.shields.io/github/actions/workflow/status/telegram-bridge/tg-core/style.yml??branch=master&label=lint)](https://github.com/telegram-bridge/tg-core/actions/workflows/style.yml)
[![semantic-release](https://img.shields.io/badge/%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release)

> TelegramBridge/ is a community-driven project focused on extracting core functionality from Telegram Web, enabling development of custom integrations like automated responses, file distribution, natural language processing and countless other applications limited only by creativity...

## Community channels

[![Discord](https://img.shields.io/discord/755892341628747892?color=blueviolet&label=Discord&logo=discord&style=flat)](https://discord.gg/TG8BRIDGE9)
[![Telegram Group](https://img.shields.io/badge/Telegram-Group-32AFED?logo=telegram)](https://t.me/tgbridge_dev)
[![WhatsApp Group](https://img.shields.io/badge/WhatsApp-Group-25D366?logo=whatsapp)](https://chat.whatsapp.com/MTbQz7YxPwmCQOBSKZ900L)
[![YouTube](https://img.shields.io/youtube/channel/subscribers/UCD8K0MH09QrF7KS8Yvx8B?label=YouTube)](https://www.youtube.com/c/telegrambridge)

## How does it work

This project extracts core functionality from Telegram Web sources, utilizing rollup bundler.

Post-compilation, generates `dist/tgbridge-core.js` for injection into Telegram Web interface. Upon injection, exposes global object `TGB`.

Key components of `TGB` object:

- `TGB.bundler` - Utilities for exporting Telegram functions.
- `TGB.telegram` - Exported Telegram core functions.
- `TGB.messaging` - Message handling functions and listeners.
- ...

## Exported Telegram modules

Naming conventions for exported modules:

- `...Schema` - Data structure classes (`UserSchema`, `MessageSchema`)
- `...Set` - Model collection classes (`ChatSet`, `MessageSet`)
- `...Registry` - Global collection instances (`ChatRegistry`, `MessageRegistry`)

## Development

Local setup instructions:

```bash
# install dependencies
yarn install

# fetch telegram web source and format (optional)
yarn fetch:source

# compile production build
yarn compile:prod # or compile:dev for debug builds

# start local browser with auto-injection
yarn dev:local

# or run through VSCode debugger
```

## Usage guide

Fundamentally, inject `tgbridge-core.js` into browser context after Telegram Web initialization.

### TamperMonkey or GreaseMonkey

```javascript
// ==UserScript==
// @name         TG-Core Example
// @namespace    http://tampermonkey.net/
// @version      0.2
// @description  Basic TG-Core implementation
// @author       Developer
// @match        https://web.telegram.org/*
// @icon         https://www.google.com/s2/favicons?domain=telegram.org
// @require      https://github.com/telegram-bridge/tg-core/releases/download/latest/tgbridge-core.js
// @grant        none
// ==/UserScript==

/* globals TGB */

(function () {
  'use strict';

  TGB.bundler.onReady(function () {
    console.log('TelegramBridge TG-Core initialized');
  });

  // Implementation code here...
})();
```

### Puppeteer

```typescript
import * as puppeteer from 'puppeteer-core';

async function initialize() {
  const browser = await puppeteer.launch();
  const context = browser.newPage();

  await context.goto('https://web.telegram.org/');

  await context.addScriptTag({
    path: require.resolve('@tgbridge/tg-core'),
  });

  // Await TG-Core initialization
  await context.waitForFunction(() => window.TGB?.initialized);

  // Execute code: Reference https://pptr.dev/guides/evaluate-javascript
  const authStatus: boolean = await context.evaluate(() =>
    TGB.auth.checkAuthentication()
  );

  // Send message: Reference https://pptr.dev/guides/evaluate-javascript
  const result: object = await context.evaluate(
    (recipient, content) => TGB.messaging.sendText(recipient, content),
    recipient,
    content
  );
}

initialize();
```

## License

Copyright 2022 TelegramBridge Contributors

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
