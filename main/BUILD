load("@rules_android//android:rules.bzl", "android_library")

genrule(
    name = "empty_java_file",
    outs = ["EmptyFile.java"],
    cmd = "touch $@",
)

android_library(
    name = "basic_lib",
    srcs = [":empty_java_file"],
)