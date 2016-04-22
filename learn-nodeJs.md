# windows 10 上使用node.js遇到的问题记录
## 1. question: npm 安装 express 报如下错：
C:\Users\jq>npm install express

npm ERR! Windows_NT 10.0.10586

npm ERR! argv "D:\\Program Files (x86)\\nodejs\\node.exe" "C:\\Users\\jq\\AppData\\Roaming\\npm\\node_modules\\npm\\bin\\npm-cli.js" "install" "express"

npm ERR! node v4.4.0

npm ERR! npm  v2.10.1


npm ERR! shasum check failed for C:\Users\jq\AppData\Local\Temp\npm-2688-c8f31dad\registry.npmjs.org\express\-\express-4.13.4.tgz

npm ERR! Expected: 3c0b76f3c77590c8345739061ec0bd3ba067ec24

npm ERR! Actual:   5caafeb40d4bb2d5dbcd1f6274bfc78fbeea3291

npm ERR! From:     https://registry.npmjs.org/express/-/express-4.13.4.tgz

npm ERR!

npm ERR! If you need help, you may report this error at:

npm ERR!     <https://github.com/npm/npm/issues>

npm ERR! Please include the following file with any support request:

npm ERR! C:\Users\jq\npm-debug.log

## solution： 使用这个安装 npm install -g express-generator
