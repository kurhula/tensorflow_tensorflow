# Copyright 2024 Google LLC.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

load("//tensorflow/lite/experimental/litert/build_common:litert_build_defs.bzl", "copy_file", "litert_dynamic_lib")

package(
    # copybara:uncomment default_applicable_licenses = ["//tensorflow:license"],
    default_visibility = ["//tensorflow/lite/experimental/litert:__subpackages__"],
)

litert_dynamic_lib(
    name = "dispatch_api",
    srcs = [
        "dispatch_api.cc",
        "litert_dispatch_device_context.cc",
        "litert_dispatch_invocation_context.cc",
    ],
    hdrs = [
        "litert_dispatch_device_context.h",
        "litert_dispatch_invocation_context.h",
    ],
    copts = [
        "-Os",
        "-fno-exceptions",
        "-fno-unwind-tables",
        "-fno-asynchronous-unwind-tables",
        "-ffunction-sections",
        "-fdata-sections",
    ],
    export_litert_only = True,
    linkopts = select({
        "//tensorflow:android": ["-landroid"],
        "//conditions:default": [],
    }) + [
        "-Wl,-soname=libLiteRtDispatch_GoogleTensor.so",
        "-Wl,-lc++abi",
    ],
    shared_lib_name = "dispatch_api_so",
    so_name = "libLiteRtDispatch_Mediatek.so",
    tags = [
        # Remove when sdk is available to bazel.
        "nobuilder",
        "notap",
    ],
    visibility = ["//tensorflow/lite/experimental/litert:__subpackages__"],
    deps = [
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/container:flat_hash_set",
        "@com_google_absl//absl/container:node_hash_set",
        "@com_google_absl//absl/strings:string_view",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/types:span",
        # copybara:uncomment "//third_party/neuro_pilot:latest_host_headers",
        "//tensorflow/lite/experimental/litert/c:litert_runtime_c_api_shared_lib",
        "//tensorflow/lite/experimental/litert/cc:litert_expected",
        "//tensorflow/lite/experimental/litert/cc:litert_macros",
        "//tensorflow/lite/experimental/litert/core:dynamic_loading",
        "//tensorflow/lite/experimental/litert/core/util:tensor_type_util",
        "//tensorflow/lite/experimental/litert/vendors/c:litert_dispatch_c_api",
        "//tensorflow/lite/experimental/litert/vendors/mediatek:neuron_adapter_api",
        "//tensorflow/lite/experimental/litert/vendors/mediatek/schema:mediatek_litert_schema",
    ],
)

# This is cc_library target for `libLiteRtDispatch_Mediatek.so`.
cc_library(
    name = "dispatch_api_shared_lib",
    srcs = [":dispatch_api_so"],
)

# Copies the shared library so that it is available for use in test data as libLiteRtDispatch_Mediatek.so.
copy_file(
    name = "copy_dispatch_api_so",
    src = "//tensorflow/lite/experimental/litert/vendors/mediatek/dispatch:dispatch_api_so",
    target = "libLiteRtDispatch_Mediatek.so",
)

cc_test(
    name = "dispatch_api_mediatek_test",
    srcs = [
        "dispatch_api_mediatek_test.cc",
    ],
    data = [
        ":dispatch_api_so",
    ],
    linkopts = select({
        "//tensorflow:android": ["-landroid"],
        "//conditions:default": [],
    }),
    tags = [
        "no-remote-exec",
        "no_oss",
        "nobuilder",
        "nosan",
        "notap",
    ],
    deps = [
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/log:absl_log",
        "@com_google_absl//absl/types:span",
        # copybara:uncomment "//third_party/neuro_pilot:latest_host_headers",
        "//tensorflow/lite/experimental/litert/c:litert_common",
        "//tensorflow/lite/experimental/litert/c:litert_tensor_buffer",
        "//tensorflow/lite/experimental/litert/cc:litert_any",
        "//tensorflow/lite/experimental/litert/core:filesystem",
        "//tensorflow/lite/experimental/litert/test:common",
        "//tensorflow/lite/experimental/litert/test:simple_model_npu",
        "//tensorflow/lite/experimental/litert/vendors/c:litert_dispatch_c_api",
        "@com_google_googletest//:gtest_main",
    ],
)
