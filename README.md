# incomplete ZMKで特定のピンからUARTのログを出すためのモジュール

west.ymlとbuild.yamlに少しコードを書き加えるだけで、UARTを出力できるようになる。
zmk 0.4(main)でのみテスト。

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
```

### build.yamlに以下記述
```yaml
snippet: debug-uart
cmake-args: -DDTS_EXTRA_CPPFLAGS="-DDEBUG_UART_TX_PORT=0;-DDEBUG_UART_TX_PIN=31"
```
#### TX_PORTとTX_PINは適宜環境に合わせて書き換える。

STUDIOと同居させるなら、
```yaml
    cmake-args: -DCONFIG_ZMK_STUDIO=y -DDTS_EXTRA_CPPFLAGS="-DDEBUG_UART_TX_PORT=0;-DDEBUG_UART_TX_PIN=31"
```

### Xiao nRF52840 Plusで使用するときの。

p0.31を使う場合、そのままだとバッテリ監視とかちあうので、
#### overlayファイルに以下を書き加える。
```
/*for 0.31*/
/ {
    chosen {
        /delete-property/ zmk,battery;
    };
};

&vbatt {
    status = "disabled";
};

&gpio0 {
    p14_force_low: p14-force-low {
        gpio-hog;
        gpios = <14 GPIO_ACTIVE_HIGH>;
        output-low;
        line-name = "vbat-divider-gnd-safe-low";
    };
};
```

#### confファイルに以下を書き加える。
```
#for 0.31
CONFIG_GPIO_HOGS=y
```
