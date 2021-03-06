#
# Copyright 2017 The Abseil Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

list(APPEND BASE_PUBLIC_HEADERS
  "attributes.h"
  "call_once.h"
  "casts.h"
  "config.h"
  "dynamic_annotations.h"
  "log_severity.h"
  "macros.h"
  "optimization.h"
  "policy_checks.h"
  "port.h"
  "thread_annotations.h"
)


list(APPEND BASE_INTERNAL_HEADERS
  "internal/atomic_hook.h"
  "internal/cycleclock.h"
  "internal/endian.h"
  "internal/exception_testing.h"
  "internal/exception_safety_testing.h"
  "internal/identity.h"
  "internal/invoke.h"
  "internal/inline_variable.h"
  "internal/low_level_alloc.h"
  "internal/low_level_scheduling.h"
  "internal/malloc_extension.h"
  "internal/malloc_hook_c.h"
  "internal/malloc_hook.h"
  "internal/malloc_hook_invoke.h"
  "internal/per_thread_tls.h"
  "internal/pretty_function.h"
  "internal/raw_logging.h"
  "internal/scheduling_mode.h"
  "internal/spinlock.h"
  "internal/spinlock_wait.h"
  "internal/sysinfo.h"
  "internal/thread_identity.h"
  "internal/throw_delegate.h"
  "internal/tsan_mutex_interface.h"
  "internal/unaligned_access.h"
  "internal/unscaledcycleclock.h"
)


# absl_base main library
list(APPEND BASE_SRC
  "internal/cycleclock.cc"
  "internal/exception_safety_testing.cc"
  "internal/raw_logging.cc"
  "internal/spinlock.cc"
  "internal/sysinfo.cc"
  "internal/thread_identity.cc"
  "internal/unscaledcycleclock.cc"
  "internal/low_level_alloc.cc"
  "internal/malloc_hook.cc"
  ${BASE_PUBLIC_HEADERS}
  ${BASE_INTERNAL_HEADERS}
)

absl_library(
  TARGET
    absl_base
  SOURCES
    ${BASE_SRC}
  PUBLIC_LIBRARIES
    absl_dynamic_annotations
    absl_spinlock_wait
  EXPORT_NAME
    base
)

# malloc extension library
set(MALLOC_EXTENSION_SRC "internal/malloc_extension.cc")
set(MALLOC_EXTENSION_PUBLIC_LIBRARIES absl::base)

absl_library(
  TARGET
    absl_malloc_extension
  SOURCES
    ${MALLOC_EXTENSION_SRC}
  PUBLIC_LIBRARIES
    ${MALLOC_EXTENSION_PUBLIC_LIBRARIES}
  EXPORT_NAME
    malloc_extension
)

# throw delegate library
set(THROW_DELEGATE_SRC "internal/throw_delegate.cc")

absl_library(
  TARGET
    absl_throw_delegate
  SOURCES
    ${THROW_DELEGATE_SRC}
  PUBLIC_LIBRARIES
    ${THROW_DELEGATE_PUBLIC_LIBRARIES}
  PRIVATE_COMPILE_FLAGS
    ${ABSL_EXCEPTIONS_FLAG}
  EXPORT_NAME
    throw_delegate
)


# dynamic_annotations library
set(DYNAMIC_ANNOTATIONS_SRC "dynamic_annotations.cc")

absl_library(
  TARGET
    absl_dynamic_annotations
  SOURCES
    ${DYNAMIC_ANNOTATIONS_SRC}
)


# spinlock_wait library
set(SPINLOCK_WAIT_SRC "internal/spinlock_wait.cc")

absl_library(
  TARGET
    absl_spinlock_wait
  SOURCES
    ${SPINLOCK_WAIT_SRC}
)


# malloc_internal library
list(APPEND MALLOC_INTERNAL_SRC
  "internal/low_level_alloc.cc"
  "internal/malloc_hook.cc"
  "internal/malloc_hook_mmap_linux.inc"
)

absl_library(
  TARGET
    absl_malloc_internal
  SOURCES
    ${MALLOC_INTERNAL_SRC}
  PUBLIC_LIBRARIES
    absl_dynamic_annotations
)



#
## TESTS
#

# call once test
set(CALL_ONCE_TEST_SRC "call_once_test.cc")
set(CALL_ONCE_TEST_PUBLIC_LIBRARIES absl::base absl::synchronization)

absl_test(
  TARGET
    call_once_test
  SOURCES
    ${CALL_ONCE_TEST_SRC}
  PUBLIC_LIBRARIES
    ${CALL_ONCE_TEST_PUBLIC_LIBRARIES}
)


# test bit_cast_test
set(BIT_CAST_TEST_SRC "bit_cast_test.cc")

absl_test(
  TARGET
    bit_cast_test
  SOURCES
    ${BIT_CAST_TEST_SRC}
)


# test absl_throw_delegate_test
set(THROW_DELEGATE_TEST_SRC "throw_delegate_test.cc")
set(THROW_DELEGATE_TEST_PUBLIC_LIBRARIES absl::base absl_throw_delegate)

absl_test(
  TARGET
    throw_delegate_test
  SOURCES
    ${THROW_DELEGATE_TEST_SRC}
  PUBLIC_LIBRARIES
    ${THROW_DELEGATE_TEST_PUBLIC_LIBRARIES}
)


# test invoke_test
set(INVOKE_TEST_SRC "invoke_test.cc")
set(INVOKE_TEST_PUBLIC_LIBRARIES absl::strings)

absl_test(
  TARGET
    invoke_test
  SOURCES
    ${INVOKE_TEST_SRC}
  PUBLIC_LIBRARIES
    ${INVOKE_TEST_PUBLIC_LIBRARIES}
)


# test inline_variable_test
list(APPEND INLINE_VARIABLE_TEST_SRC
  "internal/inline_variable_testing.h"
  "inline_variable_test.cc"
  "inline_variable_test_a.cc"
  "inline_variable_test_b.cc"
)

set(INLINE_VARIABLE_TEST_PUBLIC_LIBRARIES absl::base)

absl_test(
  TARGET
    inline_variable_test
  SOURCES
    ${INLINE_VARIABLE_TEST_SRC}
  PUBLIC_LIBRARIES
    ${INLINE_VARIABLE_TEST_PUBLIC_LIBRARIES}
)


# test spinlock_test_common
set(SPINLOCK_TEST_COMMON_SRC "spinlock_test_common.cc")
set(SPINLOCK_TEST_COMMON_PUBLIC_LIBRARIES absl::base absl::synchronization)

absl_test(
  TARGET
    spinlock_test_common
  SOURCES
    ${SPINLOCK_TEST_COMMON_SRC}
  PUBLIC_LIBRARIES
    ${SPINLOCK_TEST_COMMON_PUBLIC_LIBRARIES}
)


# test spinlock_test
set(SPINLOCK_TEST_SRC "spinlock_test_common.cc")
set(SPINLOCK_TEST_PUBLIC_LIBRARIES absl::base absl::synchronization)

absl_test(
  TARGET
    spinlock_test
  SOURCES
    ${SPINLOCK_TEST_SRC}
  PUBLIC_LIBRARIES
    ${SPINLOCK_TEST_PUBLIC_LIBRARIES}
)


# test endian_test
set(ENDIAN_TEST_SRC "internal/endian_test.cc")

absl_test(
  TARGET
    endian_test
  SOURCES
    ${ENDIAN_TEST_SRC}
)


# test config_test
set(CONFIG_TEST_SRC "config_test.cc")
set(CONFIG_TEST_PUBLIC_LIBRARIES absl::base absl::synchronization)
absl_test(
  TARGET
    config_test
  SOURCES
    ${CONFIG_TEST_SRC}
  PUBLIC_LIBRARIES
    ${CONFIG_TEST_PUBLIC_LIBRARIES}
)


# test raw_logging_test
set(RAW_LOGGING_TEST_SRC "raw_logging_test.cc")
set(RAW_LOGGING_TEST_PUBLIC_LIBRARIES absl::base)

absl_test(
  TARGET
    raw_logging_test
  SOURCES
    ${RAW_LOGGING_TEST_SRC}
  PUBLIC_LIBRARIES
    ${RAW_LOGGING_TEST_PUBLIC_LIBRARIES}
)


# test sysinfo_test
set(SYSINFO_TEST_SRC "internal/sysinfo_test.cc")
set(SYSINFO_TEST_PUBLIC_LIBRARIES absl::base absl::synchronization)

absl_test(
  TARGET
    sysinfo_test
  SOURCES
    ${SYSINFO_TEST_SRC}
  PUBLIC_LIBRARIES
    ${SYSINFO_TEST_PUBLIC_LIBRARIES}
)


# test low_level_alloc_test
set(LOW_LEVEL_ALLOC_TEST_SRC "internal/low_level_alloc_test.cc")
set(LOW_LEVEL_ALLOC_TEST_PUBLIC_LIBRARIES absl::base)

absl_test(
  TARGET
    low_level_alloc_test
  SOURCES
    ${LOW_LEVEL_ALLOC_TEST_SRC}
  PUBLIC_LIBRARIES
    ${LOW_LEVEL_ALLOC_TEST_PUBLIC_LIBRARIES}
)


# test thread_identity_test
set(THREAD_IDENTITY_TEST_SRC "internal/thread_identity_test.cc")
set(THREAD_IDENTITY_TEST_PUBLIC_LIBRARIES absl::base absl::synchronization)

absl_test(
  TARGET
    thread_identity_test
  SOURCES
    ${THREAD_IDENTITY_TEST_SRC}
  PUBLIC_LIBRARIES
    ${THREAD_IDENTITY_TEST_PUBLIC_LIBRARIES}
)

#test exceptions_safety_testing_test
set(EXCEPTION_SAFETY_TESTING_TEST_SRC "exception_safety_testing_test.cc")
set(EXCEPTION_SAFETY_TESTING_TEST_PUBLIC_LIBRARIES absl::base absl::memory absl::meta absl::strings absl::optional)

absl_test(
  TARGET
    absl_exception_safety_testing_test
  SOURCES
    ${EXCEPTION_SAFETY_TESTING_TEST_SRC}
  PUBLIC_LIBRARIES
    ${EXCEPTION_SAFETY_TESTING_TEST_PUBLIC_LIBRARIES}
  PRIVATE_COMPILE_FLAGS
    ${ABSL_EXCEPTIONS_FLAG}
)

# test absl_malloc_extension_system_malloc_test
set(MALLOC_EXTENSION_SYSTEM_MALLOC_TEST_SRC "internal/malloc_extension_test.cc")
set(MALLOC_EXTENSION_SYSTEM_MALLOC_TEST_PUBLIC_LIBRARIES absl::base absl_malloc_extension)
set(MALLOC_EXTENSION_SYSTEM_MALLOC_TEST_PRIVATE_COMPILE_FLAGS "-DABSL_MALLOC_EXTENSION_TEST_ALLOW_MISSING_EXTENSION=1")

absl_test(
  TARGET
    absl_malloc_extension_system_malloc_test
  SOURCES
    ${MALLOC_EXTENSION_SYSTEM_MALLOC_TEST_SRC}
  PUBLIC_LIBRARIES
    ${MALLOC_EXTENSION_SYSTEM_MALLOC_TEST_PUBLIC_LIBRARIES}
  PRIVATE_COMPILE_FLAGS
    ${MALLOC_EXTENSION_SYSTEM_MALLOC_TEST_PRIVATE_COMPILE_FLAGS}
)




