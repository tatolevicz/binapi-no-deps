cmake_minimum_required(VERSION 3.2)
project(binapi)

set(CMAKE_CXX_STANDARD 14)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -fsanitize=address")

add_definitions(
        -UNDEBUG
        -DDTF_HEADER_ONLY
)

set(BINAPI_HEADERS
        include/api.hpp
        include/flatjson.hpp
        include/dtf.hpp
        include/double_type.hpp
        include/enums.hpp
        include/errors.hpp
        include/invoker.hpp
        include/message.hpp
        include/pairslist.hpp
        include/reports.hpp
        include/tools.hpp
        include/types.hpp
        include/websocket.hpp
        )

set(BINAPI_SOURCES
        src/api.cpp
        src/enums.cpp
        src/errors.cpp
        src/pairslist.cpp
        src/reports.cpp
        src/tools.cpp
        src/types.cpp
        src/websocket.cpp
        )


add_library(
        ${PROJECT_NAME}
        ${BINAPI_SOURCES}
)

target_include_directories(${PROJECT_NAME} PUBLIC
        deps/boost/1.76.0/include
        deps/openssl@3/3.0.1/include
        include
        )

#-WARNING: this is extremely necessary to find the static libs .a in the deps folder -----
#-libs : crypto - ssl
target_link_directories(${PROJECT_NAME} PUBLIC deps/openssl@3/3.0.1/lib)
#-----------------------------------------------------------------------------------------

target_link_libraries(
        ${PROJECT_NAME}
        z
        crypto
        ssl
        pthread
)
