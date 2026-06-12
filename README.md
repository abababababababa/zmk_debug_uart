# ZMKで特定のピンからUARTのログを出すためのモジュール

## Usage

### west.yamlに以下記述
remotes:の方
- name: abababababababa
  url-base: https://github.com/abababababababa

### projects:の方
- name: zmk_debug_uart  
  remote: abababababababa  
  revision: main  
  path: modules/zmk_debug_uart  

### build.yamlに以下記述
include:  
  - board: 略  
    shield: 略  
    ↓これ  
    extra-cmake-args: -DEXTRA_CONF_FILE=../modules/zmk_debug_uart/config/debug.conf  

### overlayファイルの先頭に以下記述
#define DEBUG_UART_TX_PORT 1 //環境に応じて変える  
#define DEBUG_UART_TX_PIN  12 //環境に応じて変える  
#include <debug_uart.dtsi>  

