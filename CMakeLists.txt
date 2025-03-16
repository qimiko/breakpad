cmake_minimum_required(VERSION 3.20)
project(Breakpad LANGUAGES CXX ASM)

set(Breakpad_SOURCES
	${CMAKE_CURRENT_SOURCE_DIR}/src/common/convert_UTF.cc
	${CMAKE_CURRENT_SOURCE_DIR}/src/common/md5.cc
	${CMAKE_CURRENT_SOURCE_DIR}/src/common/string_conversion.cc
)

set(Breakpad_HEADERS
	${CMAKE_CURRENT_SOURCE_DIR}/src/
)

if (CMAKE_SYSTEM_NAME STREQUAL "Android")
	include(FetchContent)

	FetchContent_Declare(lss
		GIT_REPOSITORY https://chromium.googlesource.com/linux-syscall-support.git
		GIT_TAG v2024.02.01
		SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/src/third_party/lss
	)

	FetchContent_MakeAvailable(lss)

	list(APPEND Breakpad_HEADERS
		${CMAKE_CURRENT_BINARY_DIR}/src/
	)

	list(APPEND Breakpad_SOURCES
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/linux/crash_generation/crash_generation_client.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/linux/dump_writer_common/thread_info.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/linux/dump_writer_common/ucontext_reader.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/linux/handler/exception_handler.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/linux/handler/minidump_descriptor.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/linux/log/log.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/linux/microdump_writer/microdump_writer.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/linux/minidump_writer/linux_dumper.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/linux/minidump_writer/linux_ptrace_dumper.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/linux/minidump_writer/minidump_writer.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/linux/minidump_writer/pe_file.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/client/minidump_file_writer.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/common/linux/breakpad_getcontext.S
		${CMAKE_CURRENT_SOURCE_DIR}/src/common/linux/elfutils.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/common/linux/file_id.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/common/linux/guid_creator.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/common/linux/linux_libc_support.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/common/linux/memory_mapped_file.cc
		${CMAKE_CURRENT_SOURCE_DIR}/src/common/linux/safe_readlink.cc
	)

	list(APPEND Breakpad_HEADERS
		${CMAKE_CURRENT_SOURCE_DIR}/src/common/android/include/
		${CMAKE_CURRENT_SOURCE_DIR}/src/common/android/include/
	)
endif()

add_library(Breakpad STATIC ${Breakpad_SOURCES})

target_compile_features(Breakpad PUBLIC cxx_std_17)
set_target_properties(Breakpad PROPERTIES CXX_EXTENSIONS OFF)

target_include_directories(Breakpad PUBLIC ${Breakpad_HEADERS})
