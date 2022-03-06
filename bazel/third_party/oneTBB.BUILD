# Copyright (c) 2021 Intel Corporation
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# DISCLAIMER: Bazel support is community-based. The maintainers do not
# use Bazel internally. The Bazel build can have security risks or
# optimization gaps.

load("@risc0//bazel/rules/cc:defs.bzl", "cc_library")

package(
    default_visibility = ["//visibility:public"],
)

cc_library(
    name = "tbb",
    srcs = glob([
        "src/tbb/*.cpp",
        "src/tbb/*.h",
    ]) + select({
        "@platforms//cpu:x86_64": glob(["src/tbb/tools_api/**/*.h"]),
        "//conditions:default": [],
    }),
    hdrs = glob([
        "include/tbb/*.h",
        "include/oneapi/*.h",
        "include/oneapi/tbb/*.h",
        "include/oneapi/tbb/detail/*.h",
    ]),
    copts = ["-w"],
    defines = select({
        "@platforms//cpu:x86_64": ["__TBB_NO_IMPLICIT_LINKAGE"],
        "//conditions:default": ["USE_PTHREAD"],
    }),
    includes = [
        "include",
    ],
    linkopts = select({
        "@bazel_tools//platforms:windows": [],
        "@platforms//os:macos": [
            "-ldl",
            "-pthread",
        ],
        "//conditions:default": [
            "-ldl",
            "-pthread",
            "-lrt",
        ],
    }),
    local_defines =
        select({
            "@platforms//cpu:x86_64": ["__TBB_USE_ITT_NOTIFY"],
            "//conditions:default": [],
        }) +
        select({
            "@platforms//os:macos": ["_XOPEN_SOURCE"],
            "//conditions:default": [],
        }) + [
            "__TBB_BUILD",
        ],
    textual_hdrs = select({
        "@platforms//cpu:x86_64": [
            "src/tbb/tools_api/ittnotify_static.c",
        ],
        "//conditions:default": [],
    }),
)

cc_library(
    name = "tbbmalloc",
    srcs =
        glob([
            "src/tbbmalloc/*.h",
            "src/tbb/*.h",
            "src/tbbmalloc_proxy/*.h",
        ]) + [
            "src/tbbmalloc/backend.cpp",
            "src/tbbmalloc/backref.cpp",
            "src/tbbmalloc/frontend.cpp",
            "src/tbbmalloc/large_objects.cpp",
            "src/tbbmalloc/tbbmalloc.cpp",
        ],
    hdrs = glob([
        "include/tbb/*.h",
        "include/oneapi/tbb/detail/*.h",
        "include/oneapi/tbb/*.h",
    ]),
    includes = [
        "include",
    ],
    local_defines = [
        "__TBBMALLOC_BUILD",
    ],
)

cc_library(
    name = "tbbmalloc_proxy",
    srcs = [
        "src/tbbmalloc_proxy/function_replacement.cpp",
        "src/tbbmalloc_proxy/proxy.cpp",
    ],
    deps = [
        ":tbbmalloc",
    ],
)
