# buildifier: disable=same-origin-load
load("//tensorflow:tensorflow.bzl", "filegroup")

# buildifier: disable=same-origin-load
load("//tensorflow:tensorflow.bzl", "get_compatible_with_cloud")
load("//tensorflow/core/platform:rules_cc.bzl", "cc_library")
load("//tensorflow:tensorflow.bzl", "tf_cc_test", "tf_native_cc_binary")
load(
    "//tensorflow/core/platform:build_config.bzl",
    "tf_proto_library",
)
load(
    "//third_party/mlir:tblgen.bzl",
    "gentbl",
)

package(
    default_visibility = [
        ":friends",
    ],
    licenses = ["notice"],  # Apache 2.0
)

package_group(
    name = "friends",
    includes = ["//third_party/mlir:subpackages"],
    packages = [
        "//learning/brain/experimental/mlir/quantization/...",
        "//tensorflow/compiler/mlir/...",
    ],
)

exports_files([
    "quantization_traits.h",
    "quantization_config.h",
    "quantization_utils.h",
])

filegroup(
    name = "quantization_td_files",
    srcs = [
        "quantization.td",
        "@llvm-project//mlir:OpBaseTdFiles",
        "@llvm-project//mlir:QuantizationOpsTdFiles",
    ],
)

gentbl(
    name = "quantization_interfaces_inc_gen",
    compatible_with = get_compatible_with_cloud(),
    tbl_outs = [
        (
            "-gen-op-interface-decls",
            "quantization_interface.h.inc",
        ),
        (
            "-gen-op-interface-defs",
            "quantization_interface.cc.inc",
        ),
    ],
    tblgen = "@llvm-project//mlir:mlir-tblgen",
    td_file = "quantization.td",
    td_srcs = [
        ":quantization_td_files",
    ],
)

tf_proto_library(
    name = "quantization_info_proto",
    srcs = [
        "quantization_info.proto",
    ],
    cc_api_version = 2,
)

cc_library(
    name = "quantization_passes",
    srcs = [
        "import_quant_stats_pass.cc",
    ],
    hdrs = [
        "quantization_passes.h",
    ],
    deps = [
        ":quantization_info_proto_cc",
        "//tensorflow/compiler/mlir/tensorflow:import_utils",
        "//tensorflow/core:lib_proto_parsing",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/strings",
        "@llvm-project//llvm:Support",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:Pass",
        "@llvm-project//mlir:QuantOps",
        "@llvm-project//mlir:StandardOps",
        "@llvm-project//mlir:Support",
    ],
    alwayslink = 1,
)

cc_library(
    name = "quantization_lib",
    srcs = [
        "quantization_driver.cc",
        "quantization_interface.cc.inc",
        "quantization_utils.cc",
    ],
    hdrs = [
        "quantization_interface.h.inc",
        "quantization_traits.h",
        "quantization_utils.h",
    ],
    deps = [
        "//tensorflow/core:lib_proto_parsing",
        "//tensorflow/lite/kernels/internal:tensor_utils",
        "//tensorflow/lite/tools/optimize:quantization_utils",
        "@com_google_absl//absl/memory",
        "@llvm-project//llvm:Support",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:QuantOps",
        "@llvm-project//mlir:StandardOps",
        "@llvm-project//mlir:Support",
    ],
)

cc_library(
    name = "quantization_config",
    srcs = [
        "quantization_config.cc",
    ],
    hdrs = [
        "quantization_config.h",
    ],
    deps = [
        "//tensorflow/core:protos_all_cc",
        "@com_google_absl//absl/strings",
        "@llvm-project//llvm:Support",
    ],
)

tf_native_cc_binary(
    name = "op_quant_spec_getters_gen",
    srcs = [
        "tools/op_quant_spec_getters_gen.cc",
    ],
    deps = [
        "@llvm-project//llvm:Support",
        "@llvm-project//llvm:TableGen",
        "@llvm-project//mlir:TableGen",
    ],
)

cc_library(
    name = "numerical_utils",
    srcs = ["numerical_utils.cc"],
    hdrs = ["numerical_utils.h"],
    deps = [
        "@com_google_absl//absl/types:optional",
    ],
)

cc_library(
    name = "device_target",
    srcs = ["device_target.cc"],
    hdrs = ["device_target.h"],
    deps = [
        ":numerical_utils",
        "@com_google_absl//absl/types:optional",
        "@llvm-project//llvm:Support",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:QuantOps",
        "@llvm-project//mlir:Support",
    ],
)

cc_library(
    name = "quantization_context",
    srcs = ["quantization_context.cc"],
    hdrs = ["quantization_context.h"],
    deps = [
        ":device_target",
        ":quantization_lib",
        "@com_google_absl//absl/memory",
        "@llvm-project//llvm:Support",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:QuantOps",
        "@llvm-project//mlir:StandardOps",
        "@llvm-project//mlir:Support",
    ],
)

tf_cc_test(
    name = "numerical_utils_test",
    srcs = ["numerical_utils_test.cc"],
    deps = [
        ":numerical_utils",
        "@com_google_absl//absl/types:optional",
        "@com_google_googletest//:gtest_main",
    ],
)
