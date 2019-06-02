npm, node, nodejs

# Nvm

Do *not* use _homebrew_

    # brew install nvm # DONT USE
    
## Without brew

    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
    nvm install node

# Yarn (with nvm)

    brew install yarn --ignore-dependencies

# web/proxy server

Setting up web server that also responds to `OPTIONS`

    const fetch = require('node-fetch')
    const http = require('http')

    const mkShopService = require('../src/api/services/commonjs/shop.js')

    http.createServer((req, res) => {

      if (req.method === 'OPTIONS') {
        res.writeHead(200, {
          'Access-Control-Allow-Origin': '*',
          'Access-Control-Allow-Headers': 'Content-Type',
          'Access-Control-Allow-Method': 'POST',
        })
        res.end()
        return
      }
      // POST

      let body = ''
      req.on('data', chunk => { body += chunk.toString() })
      req.on('end', async () => {
        const shopService = mkShopService(fetch)
        const specsheet = JSON.parse(body)
        res.writeHead(200, {
          'Access-Control-Allow-Origin': '*',
          'Content-Type': 'application/json',
        })
        res.write(JSON.stringify(await shopService.findPrice(specsheet)))
        res.end()
      })
    }).listen(8001)

Setting up a local http proxy server to https:

    const httpProxy = require('http-proxy') // gateway
    const https = require('https')

    // gateway
    httpProxy.createProxyServer(
      {
        // secure: false,
        target: 'https://dulynote.myshopify.com',
        agent  : https.globalAgent,
        headers: {
          host: 'dulynote.myshopify.com',
        },
      }
    ).listen(8000)


# scripts

Making node executable

    #!/usr/bin/env node

Note: the following will *not* work with `nvm`

    #!/usr/local/bin/node

Parsing standard input, executable

    const stdin = process.stdin, stdout = process.stdout, inputChunks = []

    stdin.resume()
    stdin.setEncoding('utf8')

    stdin.on('data', chunk => { inputChunks.push(chunk) })

    stdin.on('end', () => {
      const
        inputJSON = inputChunks.join(),
        parsedData = JSON.parse(inputJSON)
      const token = parsedData.data.attributes.token
      stdout.write(token)
    })

