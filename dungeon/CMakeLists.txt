cmake_minimum_required(VERSION 3.11)
project(dungeon VERSION 0.1.0)
#include(CTest)
# list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
# enable_testing()
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../cmake/sdl2)
set(FETCHCONTENT_QUIET OFF)
set(CMAKE_CXX_STANDARD 17)
include(FetchContent)

# SPDLOG
FetchContent_Declare(
  spdlog
  GIT_REPOSITORY https://github.com/gabime/spdlog.git
  GIT_TAG        v1.8.5
)

FetchContent_GetProperties(spdlog)

if(NOT spdlog_POPULATED)
  FetchContent_Populate(spdlog)
  add_subdirectory(${spdlog_SOURCE_DIR} ${spdlog_BINARY_DIR})
endif()

# GLM
FetchContent_Declare(
    glm
    GIT_REPOSITORY https://github.com/g-truc/glm.git
    GIT_TAG 0.9.9.8
)

FetchContent_GetProperties(glm)

if(NOT glm_POPULATED)
  FetchContent_Populate(glm)
endif()

file(GLOB SRC src/*.cpp src/**/*.cpp)

if(EMSCRIPTEN)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -sUSE_SDL=2")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -sUSE_SDL_IMAGE=2")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -sUSE_SDL_TTF=2")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} --preload-file assets")

  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -sDISABLE_DEPRECATED_FIND_EVENT_TARGET_BEHAVIOR=0")
  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -sEXTRA_EXPORTED_RUNTIME_METHODS=\"['ccall']\"")
  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -sFORCE_FILESYSTEM=1")
  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -sMODULARIZE=1")
  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -sEXPORT_NAME='pathMap'")

  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -o2")
  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -sUSE_SDL=2 -sUSE_SDL_IMAGE=2 -sUSE_SDL_TTF=2 --preload-file assets")
  set(CMAKE_EXECUTABLE_SUFFIX .html)
endif(EMSCRIPTEN)


add_executable(${PROJECT_NAME} ${SRC})

# set_target_properties(pathMap CXX_STANDARD 17)
file(COPY ${CMAKE_CURRENT_LIST_DIR}/assets DESTINATION ${CMAKE_CURRENT_BINARY_DIR})

if(NOT DEFINED EMSCRIPTEN)
  find_package(SDL2 REQUIRED)
  # target_link_libraries(${PROJECT_NAME} SDL2::Main)

  find_package(SDL2_image REQUIRED)
  # target_link_libraries(${PROJECT_NAME} SDL2::Image)

  find_package(SDL2_ttf REQUIRED)
  # target_link_libraries(${PROJECT_NAME} SDL2::TTF)

  find_package(SDL2_mixer REQUIRED)
  # target_link_libraries(${PROJECT_NAME} SDL2::TTF)

  find_package(SDL2_gfx REQUIRED)
  # target_link_libraries(${PROJECT_NAME} SDL2::TTF)

  find_package(SDL2_net REQUIRED)
  # target_link_libraries(${PROJECT_NAME} SDL2::TTF)
endif(NOT DEFINED EMSCRIPTEN)

#set(CPACK_PROJECT_NAME ${PROJECT_NAME})
#set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
#include(CPack)

if(EMSCRIPTEN)
  set(libs
    spdlog
  )
else()
  set(libs
    SDL2::Main
    SDL2::Image
    SDL2::TTF
    SDL2::Mixer
    SDL2::Net
    SDL2::GFX
    spdlog
  )
endif(EMSCRIPTEN)

# dungeon game
include_directories(${PROJECT_NAME}
  src
  ${spdlog_SOURCE_DIR}
  ${glm_SOURCE_DIR}
)
target_link_libraries(${PROJECT_NAME}
  ${libs}
)
