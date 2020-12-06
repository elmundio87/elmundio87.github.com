---
layout: post
title: "Validating PAC files with NodeJS/Mocha"
description: ""
category: "nodejs"
tags: ['nodejs','mocha']
---

As a UK resident, I have had to resort to workarounds in order to access certain US-based sites, due to geo-blocks caused by GDPR "compliance".

On my home network I run a Squid proxy that can redirect all traffic through a separate USA-based HTTP proxy.

I'd rather only redirect certain traffic through the proxy (only sites that actually return **HTTP 451 Unavailable For Legal Reasons**). To achieve this I use [PAC (Proxy Auto Configuration)](https://en.wikipedia.org/wiki/Proxy_auto-config) with a curated list of sites that I want to access;

```javascript
// wpad.dat

function FindProxyForURL(url, host) {

  if (isPlainHostName(host) || shExpMatch(host, "*.local"))
    return "DIRECT";

  var usaSites = [
    "nydailynews.com",
    "fayobserver.com",
    "wthr.com",
    "simpsonsworld.com",
    "bowmanextra.com",
    "baltimoresun.com",
    "providencejournal.com",
    "wdrb.com",
    "journaltimes.com"
  ]

  for (i = 0; i < usaSites.length; i++) {
    if (dnsDomainIs(host, usaSites[i])) return "PROXY nas.dipple.local:3128"
  }

  return "DIRECT";

}

```

I host this file in GitHub, and a scheduled task clones the latest version from my private repository and hosts it on my local network as **wpad.dipple.local/wpad.dat**.

I have messed this file up a few times in the past while making changes, as I didn't have a way of easily validating the file for incorrect syntax.

The [pac-resolver](https://preview.npmjs.com/package/pac-resolver) package in NPM is perfect for automating PAC file validation - it will parse a PAC  file, then tell you which value the PAC file will return for a specific host.

Using this module, I built the following mocha test suite;

```javascript
const assert = require('assert');

var fs = require('fs');
var pac = require('pac-resolver');

const testPAC = async (pacFile, endpoint) => {
  const FindProxyForURL = pac(fs.readFileSync(pacFile))
  return await FindProxyForURL(endpoint)
}

describe('Test PAC file wpad.dat', function() {
  it('Default to DIRECT', async function() {
    assert.strictEqual(await testPAC("wpad.dat","http://www.google.com"), "DIRECT");
  });

  it('Switch proxy for certain USA-based sites', async function() {
    assert.strictEqual(await testPAC("wpad.dat","http://nydailynews.com"), "PROXY nas.dipple.local:3128");
  });

});
```

These tests can be executed using the following command;

```bash
./node_modules/mocha/bin/mocha test
```

I then integrated these tests into a Github action. When I open a Pull Request, I can see if the changes will break the file in the main branch.
