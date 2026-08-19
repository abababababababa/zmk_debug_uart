# zmk_debug_uart
#
# Read the debug UART GPIO configuration from this project's
# west manifest userdata and expose it to the devicetree
# preprocessor.
message(STATUS "zmk_debug_uart: CMakeLists.txt LOADED")

cmake_minimum_required(VERSION 3.20.0)

find_package(Python3 COMPONENTS Interpreter REQUIRED)

# Get this module's project userdata from the west manifest.
#
# Expected west.yml:
#
# projects:
#   - name: zmk_debug_uart
#     ...
#     userdata:
#       tx_port: 1
#       tx_pin: 12
#
execute_process(
    COMMAND
        ${Python3_EXECUTABLE}
        -c
        "import west.manifest; m=west.manifest.Manifest.from_topdir(); p=m.get_projects(['zmk_debug_uart'])[0]; u=p.userdata or {}; print(f'{u.get(\"tx_port\", \"\")};{u.get(\"tx_pin\", \"\")}')"
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    OUTPUT_VARIABLE DEBUG_UART_GPIO
    OUTPUT_STRIP_TRAILING_WHITESPACE
    RESULT_VARIABLE DEBUG_UART_RESULT
)

if(NOT DEBUG_UART_RESULT EQUAL 0)
    message(
        FATAL_ERROR
        "zmk_debug_uart: failed to read west manifest userdata"
    )
endif()

# Split "port;pin".
list(LENGTH DEBUG_UART_GPIO DEBUG_UART_GPIO_LENGTH)

if(NOT DEBUG_UART_GPIO_LENGTH EQUAL 2)
    message(
        FATAL_ERROR
        "zmk_debug_uart: west.yml userdata must define tx_port and tx_pin"
    )
endif()

list(GET DEBUG_UART_GPIO 0 DEBUG_UART_TX_PORT)
list(GET DEBUG_UART_GPIO 1 DEBUG_UART_TX_PIN)

if(DEBUG_UART_TX_PORT STREQUAL "" OR DEBUG_UART_TX_PIN STREQUAL "")
    message(
        FATAL_ERROR
        "zmk_debug_uart: missing tx_port or tx_pin in west.yml userdata"
    )
endif()

# Pass the values to the devicetree preprocessor.
set(
    DTS_EXTRA_CPPFLAGS
    "${DTS_EXTRA_CPPFLAGS} -DDEBUG_UART_TX_PORT=${DEBUG_UART_TX_PORT} -DDEBUG_UART_TX_PIN=${DEBUG_UART_TX_PIN}"
    CACHE STRING
    "Extra devicetree preprocessor flags"
    FORCE
)

message(
    STATUS
    "zmk_debug_uart: UART TX = P${DEBUG_UART_TX_PORT}.${DEBUG_UART_TX_PIN}"
)

message(
    STATUS
    "zmk_debug_uart: DTS_EXTRA_CPPFLAGS = ${DTS_EXTRA_CPPFLAGS}"
)