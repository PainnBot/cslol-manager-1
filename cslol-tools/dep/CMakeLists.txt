cmake_minimum_required(VERSION 3.20)

include(FetchContent)

cmake_policy(SET CMP0169 OLD)

FetchContent_Declare(
    xxhash
    GIT_REPOSITORY https://github.com/Cyan4973/xxHash.git
    GIT_TAG        35b0373c697b5f160d3db26b1cbb45a0d5ba788c # v0.8.1
    CONFIGURE_COMMAND ""
    BUILD_COMMAND ""
)
FetchContent_GetProperties(xxhash)
if(NOT xxhash_POPULATED)
    FetchContent_Populate(xxhash)
    add_library(xxhash STATIC ${xxhash_SOURCE_DIR}/xxhash.c)
    target_include_directories(xxhash PUBLIC ${xxhash_SOURCE_DIR})
    target_compile_definitions(xxhash INTERFACE -DXXH_STATIC_LINKING_ONLY)
    target_compile_definitions(xxhash PRIVATE -DXXH_CPU_LITTLE_ENDIAN)
endif()


FetchContent_Declare(
    zstd
    GIT_REPOSITORY https://github.com/facebook/zstd.git
    GIT_TAG        63779c798237346c2b245c546c40b72a5a5913fe # v1.5.5
    CONFIGURE_COMMAND ""
    BUILD_COMMAND ""
)
FetchContent_GetProperties(zstd)
if(NOT zstd_POPULATED)
    FetchContent_Populate(zstd)
    file(GLOB zstd_SRCS
        ${zstd_SOURCE_DIR}/lib/common/*.c
        ${zstd_SOURCE_DIR}/lib/compress/*.c
        ${zstd_SOURCE_DIR}/lib/decompress/*.c
    )
    add_library(zstd STATIC ${zstd_SRCS})
    target_include_directories(zstd PUBLIC ${zstd_SOURCE_DIR}/lib)
    target_include_directories(zstd PRIVATE ${zstd_SOURCE_DIR}/lib/common)
    target_compile_definitions(zstd INTERFACE -DZSTD_STATIC_LINKING_ONLY)
    target_compile_definitions(zstd PRIVATE -DZSTD_DISABLE_ASM)
    if(MSVC)
        target_compile_options(zstd PRIVATE /wd4267)
    endif()
endif()


FetchContent_Declare(
    libdeflate
    GIT_REPOSITORY https://github.com/ebiggers/libdeflate.git
    GIT_TAG        495fee110ebb48a5eb63b75fd67e42b2955871e2 # v1.18
    CONFIGURE_COMMAND ""
    BUILD_COMMAND ""
)
FetchContent_GetProperties(libdeflate)
if(NOT libdeflate_POPULATED)
    FetchContent_Populate(libdeflate)
    file(GLOB libdeflate_SRCS
        ${libdeflate_SOURCE_DIR}/lib/*.c
        ${libdeflate_SOURCE_DIR}/lib/arm/*.c
        ${libdeflate_SOURCE_DIR}/lib/x86/*.c
    )
    add_library(libdeflate STATIC ${libdeflate_SRCS})
    target_include_directories(libdeflate PUBLIC ${libdeflate_SOURCE_DIR})
    target_include_directories(libdeflate PRIVATE ${libdeflate_SOURCE_DIR}/lib)
endif()


FetchContent_Declare(
    miniz
    GIT_REPOSITORY https://github.com/richgel999/miniz.git
    GIT_TAG        d9d197c92606da7be59cee2be2fbc6830b73c480 # 3.0.2
)
FetchContent_GetProperties(miniz)
if(NOT miniz_POPULATED)
    FetchContent_Populate(miniz)
    # Patch the CMakeLists.txt to use a newer minimum version
    file(READ ${miniz_SOURCE_DIR}/CMakeLists.txt MINIZ_CMAKE)
    string(REPLACE "cmake_minimum_required(VERSION 3.0)" "cmake_minimum_required(VERSION 3.20)" MINIZ_CMAKE ${MINIZ_CMAKE})
    file(WRITE ${miniz_SOURCE_DIR}/CMakeLists.txt ${MINIZ_CMAKE})
    add_subdirectory(${miniz_SOURCE_DIR} ${miniz_BINARY_DIR})
    target_compile_definitions(miniz PRIVATE -DMINIZ_DISABLE_ZIP_READER_CRC32_CHECKS=1)
endif()


FetchContent_Declare(
    fmt
    GIT_REPOSITORY https://github.com/fmtlib/fmt.git
    GIT_TAG        a33701196adfad74917046096bf5a2aa0ab0bb50 # 9.1.0
    CONFIGURE_COMMAND ""
    BUILD_COMMAND ""
)
FetchContent_GetProperties(fmt)
if(NOT fmt_POPULATED)
    FetchContent_Populate(fmt)
    add_library(fmt STATIC ${fmt_SOURCE_DIR}/src/format.cc ${fmt_SOURCE_DIR}/src/os.cc)
    target_include_directories(fmt PUBLIC ${fmt_SOURCE_DIR}/include)
    target_compile_definitions(fmt PRIVATE -DFMT_UNICODE=1)
endif()


FetchContent_Declare(
    fmtlog
    GIT_REPOSITORY https://github.com/MengRao/fmtlog.git
    GIT_TAG        5aea0764c820358ec1a842cd34cb6d494f65cfa1 # 2.2.1
    CONFIGURE_COMMAND ""
    BUILD_COMMAND ""
)
FetchContent_GetProperties(fmtlog)
if(NOT fmtlog_POPULATED)
    FetchContent_Populate(fmtlog)
    add_library(fmtlog INTERFACE)
    target_include_directories(fmtlog INTERFACE ${fmtlog_SOURCE_DIR})
    target_link_libraries(fmtlog INTERFACE fmt)
endif()
