# incomplete ZMKで特定のピンからUARTのログを出すためのモジュール

## Usage

### west.ymlに以下記述
remotes:の方
```yaml
- name: abababababababa
  url-base: https://github.com/abababababababa
```

projects:の方
```yaml
- name: zmk_debug_uart
  remote: abababababababa
  revision: main
  userdata:
    tx_port: 1
    tx_pin: 12
```

P0.06なら
```yaml
userdata:
  tx_port: 0
  tx_pin: 6
```
P1.12なら
```yaml
userdata:
  tx_port: 1
  tx_pin: 2
```

### build.yamlに以下記述
```yaml
snippet: debug-uart
```