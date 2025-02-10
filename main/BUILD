load("@rules_android//android:rules.bzl", "android_binary", "android_library")

android_binary(
    name = "basic_app",
    manifest = "java/com/basicapp/AndroidManifest.xml",
    deps = [":basic_lib"],
)

android_library(
    name = "basic_lib",
    srcs = ["java/com/basicapp/BasicActivity.java"],
    javacopts = ["-source 11 -target 11"],
    manifest = "java/com/basicapp/AndroidManifest.xml",
    resource_files = glob(["java/com/basicapp/res/**"]),
)
