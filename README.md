This is a reproducer for the bug when AS with Bazel plugin can't load Bazel project that uses 'rules_android' rules.

Steps to reproduce:
Open the project with ASWB and do non-incremental project import.
Expected state: the project is imported
Actual state: the project is not imported, multiple errors are shown

After the import finished with an error, the following log is shown in Bazel Sync Tool Window: https://github.com/KirillTim/rules_android_basicapp/blob/main/not_very_useful_as_log.txt

However, the errors in this log are not the root cause, these are kind of secondary errors. To get the actual error message you need to invoke from the command line the same command Android Studio invokes when doing import. The command is in the first line of `not_very_useful_as_log.txt`:
```
/usr/local/google/home/ktimofeev/go/bin/bazelisk build --tool_tag=ijwb:AndroidStudio --keep_going --noexperimental_run_validations --build_event_binary_file=/tmp/intellij-bep-ac1a2f71-b7bd-492b-b693-6dca902ecb51 --nobuild_event_binary_file_path_conversion --curses=no --color=yes --progress_in_terminal_title=no --aspects=@@intellij_aspect//:intellij_info_bundled.bzl%intellij_info_aspect --override_repository=intellij_aspect=/usr/local/google/home/ktimofeev/.local/share/Google/AndroidStudio2024.2/aswb/aspect --output_groups=intellij-render-resolve-android,intellij-resolve-android-direct-deps,intellij-resolve-java-direct-deps,intellij-info-android-direct-deps,intellij-info-generic,intellij-info-java-direct-deps -- //main:basic_lib //main:basic_app
```
.
After you invoke it, the real error apears in the stdout, see https://github.com/KirillTim/rules_android_basicapp/blob/main/actiual_error_log.txt.

```
ERROR: /usr/local/google/home/ktimofeev/Work/rules_android_with_jvm_external/main/BUILD:10:16: in @@intellij_aspect//:intellij_info_bundled.bzl%intellij_info_aspect aspect on android_library rule //main:basic_lib: 
Traceback (most recent call last):
        File "/usr/local/google/home/ktimofeev/.cache/bazel/_bazel_ktimofeev/603cdd55d1acd3ed60b73c647ce56cc7/external/intellij_aspect/intellij_info_bundled.bzl", line 67, column 37, in _aspect_impl
                return intellij_info_aspect_impl(target, ctx, semantics)
        File "/usr/local/google/home/ktimofeev/.cache/bazel/_bazel_ktimofeev/603cdd55d1acd3ed60b73c647ce56cc7/external/intellij_aspect/intellij_info_impl_bundled.bzl", line 1161, column 35, in intellij_info_aspect_impl
                handled = collect_android_info(target, ctx, semantics, ide_info, ide_info_file, output_groups) or handled
        File "/usr/local/google/home/ktimofeev/.cache/bazel/_bazel_ktimofeev/603cdd55d1acd3ed60b73c647ce56cc7/external/intellij_aspect/intellij_info_impl_bundled.bzl", line 791, column 40, in collect_android_info
                handled = _collect_android_ide_info(target, ctx, semantics, ide_info, ide_info_file, output_groups) or handled
        File "/usr/local/google/home/ktimofeev/.cache/bazel/_bazel_ktimofeev/603cdd55d1acd3ed60b73c647ce56cc7/external/intellij_aspect/intellij_info_impl_bundled.bzl", line 817, column 25, in _collect_android_ide_info
                android = target[android_common.AndroidIdeInfo]
Error: <target //main:basic_lib> (rule 'android_library') doesn't contain declared provider 'AndroidIdeInfo'
```

The problem seems to be that the aspect file that AS uses during the import is outdated and references `AndroidIdeInfo` that is not declared by the `rules_android.`


Bazel version 7.5.0, 
Android Studio Ladybug Feature Drop | 2024.2.2
Build #AI-242.23726.103.2422.12816248, built on December 18, 2024
Runtime version: 21.0.4+-12422083-b607.1 amd64
VM: OpenJDK 64-Bit Server VM by JetBrains s.r.o.
Toolkit: sun.awt.X11.XToolkit
Linux 6.10.11-1rodete2-amd64
GC: G1 Young Generation, G1 Concurrent GC, G1 Old Generation
Memory: 16000M
Cores: 96
Registry:
  ide.experimental.ui=true
  i18n.locale=
Non-Bundled Plugins:
  com.google.idea.bazel.aswb (12816248-api-version-242)
Current Desktop: GNOME
