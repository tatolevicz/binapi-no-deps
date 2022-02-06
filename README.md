# binapi-no-deps
A easy setup for the binapi  - https://github.com/niXman/binapi

## 💻 Easy Setup to test

1. create an empty C++ project on Clion - VSCode or whatever.

2. clone this repository on it:
```
git clone git@github.com:tatolevicz/binapi-no-deps.git external/binapi
``` 
3. create a CMakeLists.txt on the root of your project:
```
cmake_minimum_required(VERSION 3.17)
project(TestBINAPI)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -fsanitize=address")

add_executable(TestBINAPI main.cpp)

add_subdirectory(external/binapi)

target_include_directories(TestBINAPI PUBLIC
        external/binapi/include
        )

target_link_libraries(TestBINAPI
        binapi
        )

```

## 🚀 Using the library - Simple socket example:
```
#include <binapi/api.hpp>
#include <binapi/websocket.hpp>

#include <boost/asio/io_context.hpp>
#include <boost/asio/steady_timer.hpp>

#include <iostream>

int main() {
    boost::asio::io_context ioctx;

    binapi::ws::websockets ws{
            ioctx
            ,"stream.binance.com"
            ,"9443"
    };

    auto trade_handler = ws.trade("BTCUSDT",
             [](const char *fl, int ec, std::string emsg, auto trades) {
                 if ( ec ) {
                     std::cerr << "subscribe trades error: fl=" << fl << ", ec=" << ec << ", emsg=" << emsg << std::endl;

                     return false;
                 }

                 std::cout << "trades: " << trades << std::endl;

                 return true;
             }
    );

    boost::asio::steady_timer timer0{ioctx, std::chrono::steady_clock::now() + std::chrono::seconds(10)};
    timer0.async_wait([&ws, trade_handler](const auto &/*ec*/){
        std::cout << "unsubscribing trade_handler: " << trade_handler << std::endl;
        ws.unsubscribe(trade_handler);
    });

    ioctx.run();

    return EXIT_SUCCESS;
}

```
Running this you will be able to see 10 seconds of real-time BTCUSDT trades from binance socket connection.


Enjoy it.
