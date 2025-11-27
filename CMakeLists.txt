cmake_minimum_required(VERSION 3.24)
project(LuaButAwesome C)

set(LUA_CORE
    lapi.c lcode.c lctype.c ldebug.c ldo.c ldump.c lfunc.c lgc.c
    llex.c lmem.c lobject.c lopcodes.c lparser.c lstate.c lstring.c
    ltable.c ltm.c lundump.c lvm.c lzio.c
)

set(LUA_LIB
    lauxlib.c lbaselib.c ldblib.c liolib.c lmathlib.c loslib.c
    ltablib.c lstrlib.c lutf8lib.c loadlib.c lcorolib.c linit.c
)

set(LUA_APP
    lua.c
)

add_library(lua_static STATIC ${LUA_CORE} ${LUA_LIB})
target_include_directories(lua_static PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

if(MSVC)
    foreach(flag_var
            CMAKE_C_FLAGS_RELEASE
            CMAKE_C_FLAGS_DEBUG
            CMAKE_C_FLAGS_RELWITHDEBINFO
            CMAKE_C_FLAGS_MINSIZEREL)
        string(REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
    endforeach()
endif()

add_executable(lua ${LUA_APP})
target_link_libraries(lua PRIVATE lua_static)
target_include_directories(lua PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

if(MSVC)
else()
    target_compile_options(lua_static PRIVATE -Wall -O2 -std=c99 -fno-stack-protector -fno-common)
    target_compile_options(lua PRIVATE -Wall -O2 -std=c99 -fno-stack-protector -fno-common)
endif()
