cmake_minimum_required(VERSION 3.14)
set(CMAKE_CXX_STANDARD 17)
project(wow_simulator)

include(FetchContent)

if (NOT EMSCRIPTEN)
    FetchContent_Declare(
            googletest
            GIT_REPOSITORY https://github.com/google/googletest.git
            GIT_TAG release-1.10.0
    )
    FetchContent_MakeAvailable(googletest)
    if (CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
        foreach (gtest_target gtest gtest_main gmock gmock_main)
            if (TARGET ${gtest_target})
                target_compile_options(${gtest_target} PRIVATE -Wno-error)
            endif ()
        endforeach ()
    endif ()
    enable_testing()
endif ()


if (CMAKE_BUILD_TYPE MATCHES Debug)
    message("Building ${CMAKE_PROJECT_NAME} in Debug mode")
    if (MSVC)
        add_compile_options(/DTEST_VIA_CONFIG /W4 /WX)
    else ()
        add_compile_options(-DTEST_VIA_CONFIG -Wall -Wextra -Werror -Wunused -pedantic)
    endif ()
else ()
    message("Building ${CMAKE_PROJECT_NAME} in Release mode")
    if (MSVC)
        add_compile_options(/O2)
    else ()
        add_compile_options(-O3 -flto)
    endif ()
endif ()

add_subdirectory(simulator)
add_subdirectory(statistics)
add_subdirectory(wow_library)
add_subdirectory(sim_interface)
add_subdirectory(common)
add_subdirectory(item_optimizer)

if (EMSCRIPTEN)
    add_subdirectory(website)
endif ()
