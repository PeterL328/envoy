load(
    "//bazel:envoy_build_system.bzl",
    "envoy_cc_extension",
    "envoy_cc_library",
    "envoy_extension_package",
)

licenses(["notice"])  # Apache 2

envoy_extension_package()

envoy_cc_library(
    name = "ext_proc",
    srcs = [
        "ext_proc.cc",
        "processor_state.cc",
    ],
    hdrs = [
        "ext_proc.h",
        "processor_state.h",
    ],
    tags = ["skip_on_windows"],
    deps = [
        ":client_lib",
        ":matching_utils_lib",
        ":mutation_utils_lib",
        ":on_processing_response_interface",
        "//envoy/event:timer_interface",
        "//envoy/http:filter_interface",
        "//envoy/http:header_map_interface",
        "//envoy/stats:stats_macros",
        "//source/common/buffer:buffer_lib",
        "//source/common/protobuf",
        "//source/common/protobuf:utility_lib",
        "//source/common/runtime:runtime_features_lib",
        "//source/extensions/filters/common/mutation_rules:mutation_rules_lib",
        "//source/extensions/filters/http/common:pass_through_filter_lib",
        "//source/extensions/filters/http/ext_proc/http_client:http_client_lib",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/strings:str_format",
        "@com_google_absl//absl/strings:string_view",
        "@envoy_api//envoy/config/common/mutation_rules/v3:pkg_cc_proto",
        "@envoy_api//envoy/config/core/v3:pkg_cc_proto",
        "@envoy_api//envoy/extensions/filters/http/ext_proc/v3:pkg_cc_proto",
        "@envoy_api//envoy/service/ext_proc/v3:pkg_cc_proto",
    ],
)

envoy_cc_library(
    name = "client_lib",
    srcs = ["client_impl.cc"],
    hdrs = ["client_impl.h"],
    deps = [
        "//envoy/grpc:async_client_manager_interface",
        "//source/common/http:sidestream_watermark_lib",
        "//source/extensions/filters/common/ext_proc:grpc_client_lib",
        "@envoy_api//envoy/config/core/v3:pkg_cc_proto",
        "@envoy_api//envoy/service/ext_proc/v3:pkg_cc_proto",
    ],
)

envoy_cc_extension(
    name = "config",
    srcs = ["config.cc"],
    hdrs = ["config.h"],
    tags = ["skip_on_windows"],
    deps = [
        ":client_lib",
        ":ext_proc",
        "//source/extensions/filters/http/common:factory_base_lib",
        "//source/extensions/filters/http/ext_proc/http_client:http_client_lib",
        "@envoy_api//envoy/extensions/filters/http/ext_proc/v3:pkg_cc_proto",
    ],
)

envoy_cc_library(
    name = "mutation_utils_lib",
    srcs = ["mutation_utils.cc"],
    hdrs = ["mutation_utils.h"],
    deps = [
        "//envoy/buffer:buffer_interface",
        "//envoy/http:header_map_interface",
        "//envoy/stats:stats_interface",
        "//source/common/http:header_utility_lib",
        "//source/common/protobuf:utility_lib",
        "//source/extensions/filters/common/mutation_rules:mutation_rules_lib",
        "@com_google_absl//absl/status",
        "@envoy_api//envoy/service/ext_proc/v3:pkg_cc_proto",
    ],
)

envoy_cc_library(
    name = "matching_utils_lib",
    srcs = ["matching_utils.cc"],
    hdrs = ["matching_utils.h"],
    copts = select({
        "//bazel:windows_x86_64": [],
        "//conditions:default": [
            "-DUSE_CEL_PARSER",
        ],
    }),
    tags = ["skip_on_windows"],
    deps = [
        "//envoy/http:header_map_interface",
        "//source/common/protobuf",
        "//source/extensions/filters/common/expr:evaluator_lib",
        "@com_google_cel_cpp//eval/public:cel_expr_builder_factory",
    ] + select(
        {
            "//bazel:windows_x86_64": [],
            "//conditions:default": [
                "@com_google_cel_cpp//parser",
            ],
        },
    ),
)

envoy_cc_library(
    name = "on_processing_response_interface",
    hdrs = ["on_processing_response.h"],
    tags = ["skip_on_windows"],
    deps = [
        "//envoy/config:typed_config_interface",
        "//envoy/server:factory_context_interface",
        "//source/common/stats:symbol_table_lib",
        "@envoy_api//envoy/service/ext_proc/v3:pkg_cc_proto",
    ],
)
